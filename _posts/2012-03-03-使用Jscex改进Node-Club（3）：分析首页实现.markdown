---
layout: post
title:  "使用Jscex改进Node Club（3）：分析首页实现"
date:   2012-03-03 16:57:04
author: jeffz@live.com (老赵)
categories: program
---

## 使用Jscex改进Node Club（3）：分析首页实现
### by jeffz@live.com (老赵)
### at 2012-03-03 16:57:04
### original <http://blog.zhaojie.me/2012/02/jscexify-nodeclub-3-home-page-implementation.html>

<p>上次我们已经将<a href="https://github.com/JeffreyZhao/jscex">Jscex</a>成功地引入项目，现在便可以正式开始关注Node Club的实现了。Node Club中存在大量基于回调的JavaScript代码，颇有无从下手的感觉。既然如此，我们便随便挑一个，从首页入手吧！</p>

<h1>首页逻辑</h1>

<p>我们先从首页的JavaScript代码开始。首页的目标其实很简单，加载几部分数据组成一个对象，再交给模板引擎生成HTML代码并输出。就目前来说，显示首页需要加载以下数据：</p>

<ul>
  <li>标签 </li>

  <li>最新话题 </li>

  <li>热门话题 </li>

  <li>明星用户 </li>

  <li>得分最高的用户 </li>

  <li>无回复的话题 </li>

  <li>话题总数 </li>
</ul>

<p>对于传统Web开发技术来说，要做到这点着实容易，伪代码如下：</p>

<pre><span style="color:blue">function </span>(request, response) {
    <span style="color:blue">var </span>tags = tag_ctrl.get_all_tags(); <span style="color:#006400">// 标签
    </span><span style="color:blue">var </span>topics = topic_ctrl.get_topics_by_query(...); <span style="color:#006400">// 最新话题
    </span><span style="color:blue">var </span>hot_topics = topic_ctrl.get_topics_by_query(...); <span style="color:#006400">// 热门话题
    </span><span style="color:blue">var </span>stars = user_ctrl.get_topics_by_query(...); <span style="color:#006400">// 明星用户
    </span><span style="color:blue">var </span>tops = user_ctrl.get_users_by_query(...); <span style="color:#006400">// 得分最高用户
    </span><span style="color:blue">var </span>no_reply_topics = topic_ctrl.get_topics_by_query(...); <span style="color:#006400">// 无回复话题
    </span><span style="color:blue">var </span>topic_count = topic_ctrl.get_count_by_query(...); <span style="color:#006400">// 话题总数

    </span>response.render(<span style="color:maroon">&quot;index&quot;</span>, { ... }); <span style="color:#006400">// 输出HTML
</span>}</pre>

<p>但是在Node.js这个大环境中，这些方法都是用回调函数来返回结果的，因此代码往往会写成：</p>

<pre><span style="color:blue">function </span>(request, response, next) {
    <span style="color:#006400">// 标签
    </span>tag_ctrl.get_all_tags(<span style="color:blue">function </span>(err, tags) {
        <span style="color:blue">if </span>(err) <span style="color:blue">return </span>next(err);
        
        <span style="color:#006400">// 最新话题
        </span>topic_ctrl.get_topics_by_query(..., <span style="color:blue">function </span>(err, topics) {
            <span style="color:blue">if </span>(err) <span style="color:blue">return </span>next(err);
            
            <span style="color:#006400">// 热门话题
            </span>topic_ctrl.get_topics_by_query(..., <span style="color:blue">function </span>(err, hot_topics) {
                <span style="color:blue">if </span>(err) <span style="color:blue">return </span>next(err);
                
                <span style="color:#006400">// 明星用户
                </span>topic_ctrl.get_topics_by_query(..., <span style="color:blue">function </span>(err, stars) {
                    <span style="color:blue">if </span>(err) <span style="color:blue">return </span>next(err);
                    
                    <span style="color:#006400">// 得分最高用户
                    </span>user_ctrl.get_users_by_query(..., <span style="color:blue">function </span>(err, tops) {
                        <span style="color:blue">if </span>(err) <span style="color:blue">return </span>next(err);
                        
                        <span style="color:#006400">// 无回复话题
                        </span>topic_ctrl.get_topics_by_query(..., <span style="color:blue">function </span>(err, no_reply_topics) {
                            <span style="color:blue">if </span>(err) <span style="color:blue">return </span>next(err);
                            
                            topic_ctrl.get_count_by_query(..., <span style="color:blue">function </span>(err, topic_count) {
                                <span style="color:blue">if </span>(err) <span style="color:blue">return </span>next(err);
                                
                                <span style="color:#006400">// 输出HTML
                                </span>response.render(<span style="color:maroon">&quot;index&quot;</span>, { ... });
                            });
                        });
                    }); 
                });
            });
        });
    });
}</pre>

<p>很多人不喜欢这类层层嵌套的代码，但老实说，因为这里没有涉及逻辑判断，循环等令人头大的问题，所以在我看来其实这种串行的逻辑其实十分清晰（尽管不太漂亮），一眼就能看清楚在做什么。其实让我不喜欢的倒是这里不断出现的错误处理代码：</p>

<pre><span style="color:blue">if </span>(err) <span style="color:blue">return </span>next(err);</pre>

<p>我一直把反复出现的错误处理代码作为传统异步编程中最麻烦（又不可遗漏）的方面之一。可惜在大量的示例代码中，这反而会被人忽略掉，造成其实“不怎么麻烦”的假象。我认为，作为一个优秀的异步类库，都应该在错误处理上花点功夫，避免开发人员在各个地方不断浪费青春。例如，在这方面各类Promise模型作的都不错。</p>

<h1>首页实现</h1>

<p>Node Club使用<a href="https://github.com/JacksonTian/eventproxy">EventProxy</a>类库来尝试解决大量异步函数的嵌套问题。在首页上主要使用了以下这种模式：</p>

<pre><span style="color:blue">function </span>(request, response, next) {

    <span style="color:#006400">// 定义最终的回调函数</span>
    <span style="color:blue">var </span>render = <span style="color:blue">function </span>(tags, topics, hot_topics, stars, tops, no_reply_topics, pages){
        response.render(<span style="color:maroon">'index'</span>, {...});
    };

    <span style="color:#006400">// 注册最终的回调函数</span>
    <span style="color:blue">var </span>proxy = <span style="color:blue">new </span>EventProxy();
    proxy.assign(<span style="color:maroon">'tags'</span>, <span style="color:maroon">'topics'</span>, <span style="color:maroon">'hot_topics'</span>, <span style="color:maroon">'stars'</span>, <span style="color:maroon">'tops'</span>, <span style="color:maroon">'no_reply_topics'</span>, <span style="color:maroon">'pages'</span>, render);
    
    tag_ctrl.get_all_tags(<span style="color:blue">function </span>(err, tags){
        <span style="color:blue">if </span>(err) <span style="color:blue">return </span>next(err);
        proxy.trigger(<span style="color:maroon">'tags'</span>, tags);
    });

    topic_ctrl.get_topics_by_query(..., <span style="color:blue">function </span>(err, topics) {
        <span style="color:blue">if </span>(err) <span style="color:blue">return </span>next(err);
        proxy.trigger(<span style="color:maroon">'topics'</span>, topics);
    });
    
    topic_ctrl.get_topics_by_query(..., <span style="color:blue">function </span>(err, hot_topics) {
        <span style="color:blue">if </span>(err) <span style="color:blue">return </span>next(err);
        proxy.trigger(<span style="color:maroon">'hot_topics'</span>, hot_topics);
    });
    
    user_ctrl.get_users_by_query(..., <span style="color:blue">function </span>(err, users) {
        <span style="color:blue">if </span>(err) <span style="color:blue">return </span>next(err);
        proxy.trigger(<span style="color:maroon">'stars'</span>, users);
    });
    
    user_ctrl.get_users_by_query(..., <span style="color:blue">function </span>(err, tops) {
        <span style="color:blue">if </span>(err) <span style="color:blue">return </span>next(err);
        proxy.trigger(<span style="color:maroon">'tops'</span>, tops);
    });
    
    topic_ctrl.get_topics_by_query(..., <span style="color:blue">function </span>(err, no_reply_topics) {
        <span style="color:blue">if </span>(err) <span style="color:blue">return </span>next(err);
        proxy.trigger(<span style="color:maroon">'no_reply_topics'</span>, no_reply_topics);
    });
    
    topic_ctrl.get_count_by_query(..., <span style="color:blue">function </span>(err, all_topics_count) {
        <span style="color:blue">if </span>(err) <span style="color:blue">return </span>next(err);
        <span style="color:blue">var </span>pages = Math.ceil(all_topics_count / limit);
        proxy.trigger(<span style="color:maroon">'pages'</span>, pages);
    });
};</pre>

<p>这种模式可以简单概括为：</p>

<ul>
  <li>准备一个最终的回调方法，在获取所有结果后执行，并使用assign方法注册给EventProxy对象。 </li>

  <li>发起各异步操作，并将结果使用trigger方法提交至EventProxy。 </li>
</ul>

<p>当得到所有结果后，EventProxy自然会执行最终的回调方法，即完成最终的任务。</p>

<h1>实现分析</h1>

<p>从表面上看来，似乎EventProxy避免了回调函数的层层嵌套，但它的做法只是将每个异步调用分离出来（当然，的确会清晰一些）。如果您把现在的代码与之前“传统”代码相比，会发现两者的差距似乎只是——Tab的数量，或者说是缩进数量。真实的工作，例如每步操作的错误处理代码，还必须完全保留。简单地说，使用EventProxy其实并没有节省什么工作。</p>

<p>而且，我们完全无需使用EventProxy，也可以轻松写出类似的代码：</p>

<pre><span style="color:blue">function </span>(request, response, next) {

    <span style="color:blue">var </span>data = { };
    <span style="color:blue">var </span>steps = [<span style="color:maroon">&quot;tags&quot;</span>, <span style="color:maroon">&quot;topics&quot;</span>, ...];

    <span style="color:blue">var </span>done = <span style="color:blue">function </span>(name, value) {
        data[name] = value;
        <span style="color:blue">if </span>(steps.remove(name).length &gt; 0) <span style="color:blue">return</span>;

        response.render(<span style="color:maroon">&quot;index&quot;</span>, {...});
    }

    tag_ctrl.get_all_tags(<span style="color:blue">function </span>(err, tags) {
        <span style="color:blue">if </span>(err) <span style="color:blue">return </span>next(err);
        done(<span style="color:maroon">&quot;tags&quot;</span>, tags);
    });

    topic_ctrl.get_topics_by_query(..., <span style="color:blue">function </span>(err, topics) {
        <span style="color:blue">if </span>(err) <span style="color:blue">return </span>next(err);
        done(<span style="color:maroon">&quot;topics&quot;</span>, topics);
    });

    ...
};</pre>

<p>我们只要使用一个steps数组来准备所有的“数据”，每次完成后剔除一个，直到全部剔除为止即可。这么做还有个好处便是无需关注顺序，在使用EventProxy的时候，我们必须将注册时的使用的名称，和回调函数的参数保持顺序一致。试想，如果要增加一个步骤或是改变一些顺序，我们则必须加倍小心了。所以在我看来，在这里使用EventProxy并没有带来太多的益处，从简化编程的角度来说，效果十分有限。</p>

<p>要简化编程体验，还是得看Jscex的，下次我们便来改造Node Club的首页。</p>

<h1>相关文章</h1>

<ul>
  <li><a href="http://blog.zhaojie.me/2012/02/jscexify-nodeclub-1-prepare-nodeclub-website.html">使用Jscex改进Node Club（1）：运行Node Club网站</a> </li>

  <li><a href="http://blog.zhaojie.me/2012/02/jscexify-nodeclub-2-import-jscex.html">使用Jscex改进Node Club（2）：引入Jscex类库</a> </li>

  <li>使用Jscex改进Node Club（3）：分析首页实现</li>
</ul>