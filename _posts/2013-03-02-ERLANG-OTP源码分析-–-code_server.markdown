---
layout: post
title:  "ERLANG OTP源码分析 – code_server"
date:   2013-03-02 11:47:21
author: 
categories: program
---

## ERLANG OTP源码分析 – code_server
### by 
### at 2013-03-02 11:47:21
### original <http://item.feedsky.com/~feedsky/hoterran/~8589439/686055968/6694695/1/item.html>

<p>继续上一次的 sys 模块升级的话题，我们再讨论一下升级基本原理，了解一下 code 和 code_server 模块。</p>
<p><span></span></p>
<h2>简单升级</h2>
<p>上文也说到过，如果升级不改动 State 里的内容，你可以在不 suspend gen 进程的情况下快速升级。</p>
<p>在集群的每个节点内（当然也可以通过 rpc）</p>
<ol>
<li>拷贝 beam 到线上的 ebin 目录。</li>
<li>code:purge(Module).</li>
<li>code:load_file(Module).</li>
<li>让代码触发 Fully qualified function calls，走到新的 beam 代码。</li>
</ol>
<p>如果你想省略后 2，3 两步，可以利用 mochiweb 的 reloader 模块进行自动加载，其原理也是发现 beam 文件时间戳的变化调用 2，3 两步。</p>
<h2>升级的原理</h2>
<p>结合 code_server 代码来研究一下升级的原理。</p>
<p>erlang 可以同时允许两套代码同时加载运行在虚拟机内部运行，一套被成为老版本代码，一套被成为当前版本代码，即为最新代码。</p>
<p>升级调度的命令一般在 code 模块，它其实是 code_server 模块的接口，上面的 purge、load_file 实际上都是向 code_server 进程发送相应的话 call 消息，erlang 一般都这么玩。</p>
<p>code_server 是一个 gen_server like 的进程，但却没有使用behaviour，因为是 kernel 里的模块吧，保持简单不依赖。</p>
<h3>code:purge</h3>
<p>code:purge 底层调度的是 code_server 里的 do_purge 函数，它的作用就是杀掉在调用老代码的进程，并清空老代码，之后就只有当前代码了。</p>
<pre>do_purge([P|Ps], Mod, Purged) -&gt;
    case erlang:check_process_code(P, Mod) of
    true -&gt;
        Ref = erlang:monitor(process, P),
        exit(P, kill),
        receive
        {&#39;DOWN&#39;,Ref,process,_Pid,_} -&gt; ok
        end,
        do_purge(Ps, Mod, true);
    false -&gt;
        do_purge(Ps, Mod, Purged)</pre>
<p>erlang:check_process 就是用来判断进程是否有在调用老代码的函数，有则杀之，没有则继续下一个进程，这里要初一看会杀进程，这不科学阿，岂不是会造成程序突然 down 掉。<br>
何为老代码，假设当前代码为 2 版本，现在要升级为 3 版本，这次的 check_process 是杀掉还在执行 1 版本代码的进程，而不是 2，所以正常运行的进程是不会被杀掉的，这个后面也会详细讲解。</p>
<h3>code:load_file</h3>
<p>code:load_file 最后调度的是 erlang:load_module, 该函数做了两步事情：</p>
<ol>
<li>把当前代码覆盖老代码。</li>
<li>载入新的 beam 文件，把其代码作为当前代码。</li>
</ol>
<p>结合例子更好说明问题：</p>
<ol>
<li>应用开始 vsn=1 这就是当前代码，老代码不存在。</li>
<li>升级后 vsn=2 这就是当前代码，老代码为 vsn=1。</li>
<li>再升级，分三步：
<ul>
<li>purege 先把还在调用 vsn=1 代码的进程杀掉。</li>
<li>load module 把当前代码 vsn=2 视为成老代码。</li>
<li>把新的 beam vsn=3 当做当前代码。</li>
</ul>
</li>
</ol>
<p>我们用例子来证明一下。是否会杀掉调用老代码的进程。</p>
<pre>
-module(e3).
-compile(export_all).

start() -&gt;
	register(?MODULE, spawn(?MODULE, loop, [])).
switch() -&gt;
	?MODULE ! code_switch.
compile() -&gt;
	compile:file(?MODULE),
	code:purge(?MODULE),
	code:load_file(?MODULE).
msg() -&gt;
	?MODULE ! hello.
loop() -&gt;
    receive
        code_switch -&gt;
            ?MODULE:loop();
        _ -&gt;
			io:format(&quot;~p~n&quot;, [&quot;bb&quot;]),
            loop()
    end.
</pre>
<p>我们开始实验，启动进程后，两次加载 e3 这个模块。</p>
<pre>
    1&gt; c(e3).
    {ok,e3}
    2&gt; e3:start().
    true
    3&gt; whereis(e3).
    &lt;0.41.0&gt;
    4&gt; c(e3).
    {ok,e3}
    5&gt; whereis(e3).
    &lt;0.41.0&gt;
    6&gt; c(e3).
    {ok,e3}
    7&gt; whereis(e3).
    undefined
</pre>
<p>可以看到第二次的加载 c(e3) 杀掉了 e3 这个进程（C 内部调度了 purge），从代码可以知道 e3 一直在 receive 等待外界的消息，一直调用者老代码，所以被杀。</p>
<h3>Fully qualified function calls</h3>
<p>热升级的目的就是让老代码的程序能运行新的代码，要触发这个切换，最关键一步必须使用完全限定的函数调用，即上面的 ?MODULE:loop 这样会触发虚拟机寻找当前代码继续运行。</p>
<p>上面的场景，代码就是停留在 receive 这块，连续发生了两次升级，老的进程被杀死， 如何避免这种情况呢，个人认为代码的等待，尽量不要在用户模块里，让程序一旦发生挂起时都停留在系统模块（例如 gen）里。</p>
<p>例如上个例子如果使用 gen_server 则 receive 在 gen_server 这个模块，程序占用的老代码模块就不是 e3 了，所以不会被杀死，而 gen 进程调用 callback 函数都使用 Mod:handle_call 这类 Fully qualified function calls 的调用方法，所以加载过新代码之后，gen 进程一接收到消息，自然的切换到当前代码上了。</p>
<p>还有网络类的应用，尽量不要使用 {active, false}，这样 Socket 会阻塞在用户模块的的 gen_tcp:recv 里，如果这个 Socket 在升级之后没有任何数据交互（未离开过用户模块），那么下次升级这个连接将被杀掉，这个也是我们要避免的。</p>
<p>升级之前，我们可以运行如下的程序可以提前发现即将被杀掉的进程。</p>
<pre>
<code>[Pid|| Pid &lt;- processes(), true =:= erlang:check_process_code(Pid, Module)] </code>
</pre>
<p>code_server其它函数都比较简单，以后再讲。</p>
<p>未完待续</p>
<h1>参考</h1>
<ul>
<li>http://www.cnblogs.com/me-sa/archive/2011/10/29/erlang0010.html</li>
<li>http://erldocs.com/R15B/kernel/code.html</li>
</ul><img src="http://www1.feedsky.com/t1/686055968/hoterran/feedsky/s.gif?r=http://item.feedsky.com/~feedsky/hoterran/~8589439/686055968/6694695/1/item.html" border="0" height="0" width="0">