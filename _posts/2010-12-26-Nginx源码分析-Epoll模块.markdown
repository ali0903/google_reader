---
layout: post
title:  "Nginx源码分析-Epoll模块"
date:   2010-12-26 18:46:36
author: yixiao
categories: program
---

## Nginx源码分析-Epoll模块
### by yixiao
### at 2010-12-26 18:46:36
### original <http://www.tbdata.org/archives/1296>

<p>Linux平台上，Nginx使用epoll完成事件驱动，实现高并发；本文将不对epoll本身进行介绍（网上一堆一堆的文章介绍epoll的原理及使用方法，甚至源码分析等），仅看一下Nginx是如何使用epoll的。</p>
<p>Nginx在epoll模块中定义了好几个函数，这些函数基本都是作为回调注册到事件抽象层的对应接口上，从而实现了事件驱动的具体化，我们看如下的一段代码：</p>
<pre>
ngx_event_module_t  ngx_epoll_module_ctx = {
    &amp;epoll_name,
    ngx_epoll_create_conf,               /* create configuration */
    ngx_epoll_init_conf,                 /* init configuration */
    {
        ngx_epoll_add_event,             /* add an event */
        ngx_epoll_del_event,             /* delete an event */
        ngx_epoll_add_event,             /* enable an event */
        ngx_epoll_del_event,             /* disable an event */
        ngx_epoll_add_connection,        /* add an connection */
        ngx_epoll_del_connection,        /* delete an connection */
        NULL,                            /* process the changes */
        ngx_epoll_process_events,        /* process the events */
        ngx_epoll_init,                  /* init the events */
        ngx_epoll_done,                  /* done the events */
    }
};
</pre>
<p><span></span><br>
这段代码就是epoll的相关函数注册到事件抽象层，这里所谓的事件抽象层在前面的博文中有提过，就是Nginx为了方便支持和开发具体的I/O模型，从而实现的一层抽象。代码后面的注释将功能说明得很详细了，本文就只重点关注ngx_epoll_init和ngx_epoll_process_events两个函数，其他几个函数就暂且忽略了。</p>
<p>ngx_epoll_init主要是完成epoll的相关初始化工作，代码分析如下：</p>
<pre>
static ngx_int_t
ngx_epoll_init(ngx_cycle_t *cycle, ngx_msec_t timer)
{
    ngx_epoll_conf_t  *epcf;
	/*取得epoll模块的配置结构*/
    epcf = ngx_event_get_conf(cycle-&gt;conf_ctx, ngx_epoll_module);
	/*ep是epoll模块定义的一个全局变量，初始化为-1*/
    if (ep == -1) {
    	/*创一个epoll对象，容量为总连接数的一半*/
        ep = epoll_create(cycle-&gt;connection_n / 2);
        if (ep == -1) {
            ngx_log_error(NGX_LOG_EMERG, cycle-&gt;log, ngx_errno,
                          &quot;epoll_create() failed&quot;);
            return NGX_ERROR;
        }
    }
	/*nevents也是epoll模块定义的一个全局变量，初始化为0*/
    if (nevents events) {
        if (event_list) {
            ngx_free(event_list);
        }

		/*event_list存储产生事件的数组*/
        event_list = ngx_alloc(sizeof(struct epoll_event) * epcf-&gt;events,
                               cycle-&gt;log);
        if (event_list == NULL) {
            return NGX_ERROR;
        }
    }
    nevents = epcf-&gt;events;
	/*初始化全局变量ngx_io, ngx_os_is定义为：
		ngx_os_io_t ngx_os_io = {
    		ngx_unix_recv,
    		ngx_readv_chain,
    		ngx_udp_unix_recv,
    		ngx_unix_send,
    		ngx_writev_chain,
    		0
		};（位于src/os/unix/ngx_posix_init.c）
	*/
    ngx_io = ngx_os_io;
	/*这里就是将epoll的具体接口函数注册到事件抽象层接口ngx_event_actions上。
	具体是上文提到的ngx_epoll_module_ctx中封装的如下几个函数
        ngx_epoll_add_event,
        ngx_epoll_del_event,
        ngx_epoll_add_event,
        ngx_epoll_del_event,
        ngx_epoll_add_connection,
        ngx_epoll_del_connection,
        ngx_epoll_process_events,
        ngx_epoll_init,
        ngx_epoll_done,
	*/
    ngx_event_actions = ngx_epoll_module_ctx.actions;
#if (NGX_HAVE_CLEAR_EVENT)
	/*epoll将添加这个标志,主要为了实现边缘触发*/
    ngx_event_flags = NGX_USE_CLEAR_EVENT
#else
	/*水平触发*/
    ngx_event_flags = NGX_USE_LEVEL_EVENT
#endif
                      |NGX_USE_GREEDY_EVENT /*io的时候，直到EAGAIN为止*/
                      |NGX_USE_EPOLL_EVENT; /*epoll标志*/
    return NGX_OK;
}
</pre>
<p>epoll初始化工作没有想象中的复杂，和我们平时使用epoll都一样，下面看ngx_epoll_process_events，这个函数主要用来完成事件的等待并处理。</p>
<pre>
static ngx_int_t
ngx_epoll_process_events(ngx_cycle_t *cycle, ngx_msec_t timer, ngx_uint_t flags)
{
    int                events;
    uint32_t           revents;
    ngx_int_t          instance, i;
    ngx_uint_t         level;
    ngx_err_t          err;
    ngx_log_t         *log;
    ngx_event_t       *rev, *wev, **queue;
    ngx_connection_t  *c;
	/*一开始就是等待事件，最长等待时间为timer；nginx为事件
	专门用红黑树维护了一个计时器。后续对这个timer单独分析。
	*/
    events = epoll_wait(ep, event_list, (int) nevents, timer);
    if (events == -1) {
        err = ngx_errno;
    } else {
        err = 0;
    }
    if (flags &amp; NGX_UPDATE_TIME || ngx_event_timer_alarm) {
        /*执行一次时间更新, nginx将时间缓存到了一组全局变量中，方便程序高效的获取事件。*/
        ngx_time_update();
    }
	/*处理wait错误*/
    if (err) {
        if (err == NGX_EINTR) {
            if (ngx_event_timer_alarm) {
                ngx_event_timer_alarm = 0;
                return NGX_OK;
            }
            level = NGX_LOG_INFO;
        } else {
            level = NGX_LOG_ALERT;
        }
        ngx_log_error(level, cycle-&gt;log, err, &quot;epoll_wait() failed&quot;);
        return NGX_ERROR;
    }
	/*wait返回事件数0，可能是timeout返回，也可能是非timeout返回；非timeout返回则是error*/
    if (events == 0) {
        if (timer != NGX_TIMER_INFINITE) {
            return NGX_OK;
        }
        ngx_log_error(NGX_LOG_ALERT, cycle-&gt;log, 0,
                      &quot;epoll_wait() returned no events without timeout&quot;);
        return NGX_ERROR;
    }
    log = cycle-&gt;log;
	/*for循环开始处理收到的所有事件*/
    for (i = 0; i read;
		。。。。。。。。。。。。。

		/*取得发生一个事件*/
        revents = event_list[i].events;

		/*记录wait的错误返回状态*/
        if (revents &amp; (EPOLLERR|EPOLLHUP)) {
            ngx_log_debug2(NGX_LOG_DEBUG_EVENT, log, 0,
                           &quot;epoll_wait() error on fd:%d ev:%04XD&quot;,
                           c-&gt;fd, revents);
        }
        if ((revents &amp; (EPOLLERR|EPOLLHUP))
             &amp;&amp; (revents &amp; (EPOLLIN|EPOLLOUT)) == 0)
        {
            /*
             * if the error events were returned without EPOLLIN or EPOLLOUT,
             * then add these flags to handle the events at least in one
             * active handler
             */
            revents |= EPOLLIN|EPOLLOUT;
        }
		/*该事件是一个读事件，并该连接上注册的读事件是active的*/
        if ((revents &amp; EPOLLIN) &amp;&amp; rev-&gt;active) {
            if ((flags &amp; NGX_POST_THREAD_EVENTS) &amp;&amp; !rev-&gt;accept) {
                rev-&gt;posted_ready = 1;
            } else {
                rev-&gt;ready = 1;
            }

			/*事件放入相应的队列中；关于此处的先入队再处理，在前面的文章中已经介绍过了。*/
            if (flags &amp; NGX_POST_EVENTS) {
                queue = (ngx_event_t **) (rev-&gt;accept ?
                               &amp;ngx_posted_accept_events : &amp;ngx_posted_events);
                ngx_locked_post_event(rev, queue); /*入队*/
            } else {
                rev-&gt;handler(rev);
            }
        }
        wev = c-&gt;write;
		/*发生的是一个写事件，和读事件完全一样的逻辑过程*/
        if ((revents &amp; EPOLLOUT) &amp;&amp; wev-&gt;active) {
            if (flags &amp; NGX_POST_THREAD_EVENTS) {
                wev-&gt;posted_ready = 1;
            } else {
                wev-&gt;ready = 1;
            }
			/*先入队再处理*/
            if (flags &amp; NGX_POST_EVENTS) {
                ngx_locked_post_event(wev, &amp;ngx_posted_events);
            } else {
                wev-&gt;handler(wev);
            }
        }
    }
    return NGX_OK;
}
</pre>
<p>本文将关注的两个epoll函数也就这么一点代码了，但整个epoll还有添加事件和删除事件等的相关函数，代码都很简单，本文就不做具体的分析了。</p>