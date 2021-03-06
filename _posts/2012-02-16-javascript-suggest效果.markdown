---
layout: post
title:  "javascript suggest效果"
date:   2012-02-16 10:34:00
author: 司徒正美
categories: program
---

## javascript suggest效果
### by 司徒正美
### at 2012-02-16 10:34:00
### original <http://www.cnblogs.com/rubylouvre/archive/2012/02/16/2353615.html>

<p>像百度与google，当我们往搜索框输入东西时就会出现一排列表提示用户有什么热门或适合的候选词，这种效果就叫suggest。本文将一步步教你如何设计它。</p>
<p>首先，用到的框架当然是我的框架mass Framework，当然你用其他框架也可以，如jQuery，没有什么复杂的东西。只要弄懂原理，一下子就能搞出来。想必，以后你们工作也遇到做搜索框的活儿。</p>
<p>由于本人没有后端，因此取用一个对象作为本地数据库。而我现在要做的，其实远远比suggest高级，类似IDE的语法提示的东西。当前成品已放到<a href="https://github.com/RubyLouvre/mass-Framework/blob/master/doc/index.html">github</a>上。</p>
<p><img src="http://images.cnblogs.com/cnblogs_com/rubylouvre/199042/o_suggest.jpg"></p>
<p>好了，我们动手吧。首先是结构层，装了FF的同学可以在百度首页查看源码，当输入几个字母时，会动态生成了那些HTML。不过怎么也好，其成就是一个DIV放到搜索栏的下方，里面放了一个table，table动态存放候选词。并且候选词如果不是用户输入的部分，也就是说，JS自动补充的部分它会把它们放到一个b标签加粗显示出来。不过， 我觉得用table太重量化，改用了ul列表，为了让IE6也支持掠过变色效果，我还在里面套了一个a标签。为了放便取词，我还为它(a标签)，添加了一个属性，专门用于存放补充元整后的词汇。大抵是这个样子：</p>
<pre>&lt;div id=&quot;search_wrapper&quot;&gt;
        &lt;div&gt;
            &lt;input id=&quot;search&quot; autocomplete=&quot;off&quot;&gt;
        &lt;/div&gt;
        &lt;div id=&quot;suggest_wrapper&quot;&gt;
            &lt;ul id=&quot;suggest_list&quot;&gt;
                &lt;li&gt;
                    &lt;a data-value=&quot;完整的词汇&quot; href=&quot;javascript:void(0)&quot;&gt;
                        用户输入部分
                        &lt;b&gt;自动提示部分&lt;/b&gt;
                    &lt;/a&gt;
                &lt;/li&gt;
                &lt;li&gt;
                    &lt;a data-value=&quot;完整的词汇&quot; href=&quot;javascript:void(0)&quot;&gt;
                        用户输入部分
                        &lt;b&gt;自动提示部分&lt;/b&gt;
                    &lt;/a&gt;
                &lt;/li&gt;
                &lt;!-- 更多li 最多10个 --&gt;
            &lt;/ul&gt;
        &lt;/div&gt;
    &lt;/div&gt;
</pre>
<p>看一看结构，其实就是两部分，div#search_wrapper为可见，div#suggest_wrapper为“不可见”（只要里面没有li元素，它就不占空间，显示不出来了）。input搜索框有个属性autocomplete，用于关掉浏览器自带的提示功能。关于data-value，这种命名方法是HTML5推荐的方式，用于定义要缓存的数据，data-*在新锐浏览器中会放到一个叫dataset的对象中。比如：</p>
<pre>&lt;div id=&quot;司徒正美&quot;
  data-drink=&quot;coffee&quot;
  data-meal-time=&quot;12:00&quot;&gt;12:00&lt;/div&gt;
</pre>
<p>我们可以通过如下方式访问到它：</p>
<pre>var el= document.getElementById('司徒正美');
 alert( el.dataset.drink );
 alert( el.dataset.mealTime );
</pre>
<p>当然，你也可以不用设置属性，直接取a标签的innerText或textContext。</p>
<p>注意：完整的词汇 = 用户输入部分 + 自动提示部分。因此你不要在a标签里面加这么多东西，防止出现空格什么的，导致检索失败！</p>
<p>接着是样式部分，不过不详述了。很简单：</p>
<pre>#search_wrapper {
                height:50px;
            }
            #search{
                width:300px;
            }
            #suggest_wrapper{
                position:relative;
            }
            #suggest_list{
                position:absolute;
                z-index:100;
                list-style: none;
                margin:0;
                padding:0;
                background:#fffafa;
                border:1px solid #ccc;
                border-bottom:0 none;
            }
            #suggest_list li a{
                display: block;
                height:20px;
                width:304px;
                color: #000;
                border-bottom:1px solid #ccc;
                line-height:20px;
                text-decoration: none;
            }
            #suggest_list li a:hover, .glow_suggest {
                 background:#ffff80;
            }
</pre>
<p>好了，到重点了。由于我没有后台，要使用一个本地对象作为本地数据库。这对象当然是个JS对象了。我们遍历对象一般都是obj.aaa.bbb.ccc，这样一直点下去，其实每到一个点号时，就是用for in 循环进行遍历。因此我们监听文本内容的输入的情况，一但发生变化就取得输入框的内容，然后在for in 循环中比较。如果是与这个输入值开头的属性就取出来，放到一个数组中，一直取够十个，然后把这些数组的内容拼接成上述描绘的li元素格式，一并贴到ul元素之内。当中，我们还要注意点中,如果一开始就输入点号,我们就取window对象的十个属性吧,以后遇到点号就切换这个对象。</p>
<p>好了,开始写码,由于用到我的框架,大家可以到<a href="https://github.com/RubyLouvre/mass-Framework">这里</a>去下。在项目首页有README，教你是怎么安装微型.Net服务器与查看文档的。一开始，你就姑且把它当成是添加了模块加载功能的jQuery，API 90%神似。我们要用到它的事件模块与属性模块，它会把相关依赖加载好的，再添加ready参数，它就会在domReady后执行。我们选择输入框后为它绑定一个input事件，这是一个标准浏览器都支持的事件，IE下我的框架已经兼容好了，用jQuery与原生的同学请用propertychange事件模拟。</p>
<pre>//by 司徒正美
$.require(&quot;ready,event,attr&quot;,function(){
    var search = $(&quot;#search&quot;), hash = window, prefix = &quot;&quot;, fixIE = NaN;
    search.addClass(&quot;search_target&quot;);
    search.input(function(){//监听输入
        var
        input = this.value,//原始值
        val = input.slice( prefix.length),//比较值
        output = []; //用来放置输出内容
        if( fixIE === input){
            return //IE下肃使是通过程序改变输入框里面的值也会触发propertychange事件，导致我们无法进行上下翻操作
        }
        for(var prop in hash){
            if( prop.indexOf( val ) === 0  ){//取得以输入值开头的API
                if( output.push( &#39;&lt;li&gt;&lt;a href=&quot;javascript:void(0)&quot; data-value=&quot;&#39;+prefix +
                    prop+&#39;&quot;&gt;&#39;+ input + &quot;&lt;b&gt;&quot; + (prefix  + prop ).slice( input.length ) +&quot;&lt;/b&gt;&lt;/a&gt;&lt;/li&gt;&quot; ) == 10){
                    break;
                }
            }
        }
        //如果向前遇到点号,或向后取消点号
        if( val.charAt(val.length - 1) === &quot;.&quot; || (input &amp;&amp; !val) ){
            var arr = input.split(&quot;.&quot;); hash = window;
            for(var j = 0; j &lt; arr.length; j++){
                var el = arr[j];
                if(el &amp;&amp; hash[ el ]){
                    hash = hash[ el ];//重新设置要遍历API的对象
                }
            }
            prefix = input == &quot;.&quot; ? &quot;&quot; : input;
            for( prop in hash){
                if( output.push( &#39;&lt;li&gt;&lt;a href=&quot;javascript:void(0)&quot;  data-value=&quot;&#39;+prefix +
                    prop+&#39;&quot;&gt;&#39;+ input + &quot;&lt;b&gt;&quot; + (prefix + prop ).slice( prefix.length ) +&quot;&lt;/b&gt;&lt;/a&gt;&lt;/li&gt;&quot; ) == 10){
                    break;
                }
            }
        }
        $(&quot;#suggest_list&quot;).html( output.join(&quot;&quot;) );
        if(!input){//重置所有
            hash = window;
            fixIE = prefix = output = [];
        }
    });

});
</pre>
<p>当提示列表出来后，我们就监听上下翻效果。也就是点击键盘的方位键时，会上下高亮提示的条目，并且它填进搜索框中。这时需要绑定keyup事件，检查其keyCode，标准浏览器管它为which，可以看我的这篇博文<a href="http://www.cnblogs.com/rubylouvre/archive/2009/08/20/1550526.html">《javascript 键盘事件总结》</a>。实现原理很简单，定义一个外围的变量，用于存放高亮的位置（索引值），然后用上翻时就减一，用下翻时就加一，然后取得提示列表中的所有a标签，用索引值定位到某一个a标签中，高亮它，然后去掉原先高亮的a标签。</p>
<pre>//by 司徒正美
$.require("ready,event,attr",function(){
    var search = $("#search"), hash = window, prefix = "";
    search.input(function(){//监听输入
//.....
    });
    var glowIndex = -1;
    $(document).keyup(function(e){//监听上下翻
        if(/search_target/i.test( e.target.className)){//只代理特定元素,提高性能
            var upOrdown = 0
            if(e.which === 38 || e.which === 104){ //up 8
                upOrdown --;
            }else if(e.which === 40 || e.which === 98){//down 2
                upOrdown ++;
            }
            if(upOrdown){
                var list =  $("#suggest_list a");
                //转移高亮的栏目
                list.eq(glowIndex).removeClass("glow_suggest");
                glowIndex += upOrdown;
                var el = list.eq( glowIndex ).addClass("glow_suggest");
                fixIE = el.attr("data-value")
                search.val( fixIE )
                if(glowIndex === list.length - 1){
                    glowIndex = -1;
                }
            }
        }
    });
});
</pre>
<p>最后是回车提交。我又写到一个keyup事件中去。当然你们可以设法把两个keyup合成一个（监听window），我这样写纯粹是为了教学的需要。</p>
<pre>//by 司徒正美
$.require(&quot;ready,event,attr&quot;,function(){
    var search = $(&quot;#search&quot;), hash = window, prefix = &quot;&quot;;
    search.input(function(){//监听输入
//.....
    });
    var glowIndex = -1;
    $(window).keyup(function(e){//监听上下翻
//.....
    });
    search.keyup(function(e){//监听提交
        var input = this.value;
        if(input &amp;&amp; (e.which == 13 || e.which == 108)){ //如果按下ENTER键
           alert(input)//实际项目中，应该是进行页面跳转，跑到搜索结果页中去的！
        }
    });
});
</pre>
<p>到此，suggest效果就完成了。如果下了我的框架的同学，开启服务器，打开文档首页就能看到这个效果。而在实际项目，suggest其实更简单些，就是当输入框文本变化时，AJAX请求后台一个数组，然后再把它拼接成li元素的格式就行了。</p><img src="http://www.cnblogs.com/rubylouvre/aggbug/2353615.html?type=1" width="1" height="1" alt=""><p><a href="http://www.cnblogs.com/rubylouvre/archive/2012/02/16/2353615.html">本文链接</a></p>