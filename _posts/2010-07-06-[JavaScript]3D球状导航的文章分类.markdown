---
layout: post
title:  "[JavaScript]3D球状导航的文章分类"
date:   2010-07-06 00:02:00
author: Justin
categories: program
---

## [JavaScript]3D球状导航的文章分类
### by Justin
### at 2010-07-06 00:02:00
### original <http://www.cnblogs.com/justinw/archive/2010/07/06/1771759.html>

<p><a href="http://www.cnblogs.com/justinw/"><img src="http://pic.cnblogs.com/face/u7781.jpg" alt="" border="0"></a><br>作者: <a href="http://www.cnblogs.com/justinw/">Justin</a> 发表于 2010-07-06 00:02 <a href="http://www.cnblogs.com/justinw/archive/2010/07/06/1771759.html">原文链接</a> 阅读: 1017 评论: 10</p><p>前几天写了左边这个3D球状导航的文章分类，这里对实现细节做个简单记录。</p>
<p><span style="font-size:14pt"><strong>效果：</strong></span></p>
<p>分类标题呈现3D球状效果，点击分类标题的时候，会弹出这个分类对应的推荐文章列表。</p>
<p><strong><span style="font-size:14pt">效果截图：</span></strong></p>
<p><img src="http://pic002.cnblogs.com/img/justinw/201007/2010070523342688.png"><img src="http://pic002.cnblogs.com/img/justinw/201007/2010070523584819.png"></p>
<p><span style="font-size:14pt"><strong>HTML：</strong></span></p>
<pre>    &lt;div id=&quot;mainList&quot;&gt;
        &lt;div id=&quot;list&quot;&gt;
            &lt;ul&gt;
                &lt;li&gt;&lt;a class=&quot;HTML&quot; onclick=&quot;ClickLink(this)&quot; href=&quot;#&quot;&gt;HTML&lt;/a&gt;&lt;/li&gt;
                &lt;li&gt;&lt;a class=&quot;CSS&quot; onclick=&quot;ClickLink(this)&quot; href=&quot;#&quot;&gt;CSS&lt;/a&gt;&lt;/li&gt;
                &lt;li&gt;&lt;a class=&quot;JS&quot; onclick=&quot;ClickLink(this)&quot; href=&quot;#&quot;&gt;JavaScript&lt;/a&gt;&lt;/li&gt;
                &lt;li&gt;&lt;a class=&quot;AJAX&quot; onclick=&quot;ClickLink(this)&quot; href=&quot;#&quot;&gt;Ajax&lt;/a&gt;&lt;/li&gt;
                &lt;li&gt;&lt;a class=&quot;ASPNET&quot; onclick=&quot;ClickLink(this)&quot; href=&quot;#&quot;&gt;Asp.net&lt;/a&gt;&lt;/li&gt;
                &lt;li&gt;&lt;a class=&quot;CSHARP&quot; onclick=&quot;ClickLink(this)&quot; href=&quot;#&quot;&gt;C#&lt;/a&gt;&lt;/li&gt;
                &lt;li&gt;&lt;a class=&quot;DEBUG&quot; onclick=&quot;ClickLink(this)&quot; href=&quot;#&quot;&gt;Debugging&lt;/a&gt;&lt;/li&gt;
                &lt;li&gt;&lt;a class=&quot;PERF&quot; onclick=&quot;ClickLink(this)&quot; href=&quot;#&quot;&gt;Performance&lt;/a&gt;&lt;/li&gt;
                &lt;li&gt;&lt;a class=&quot;ARCHITERT&quot; onclick=&quot;ClickLink(this)&quot; href=&quot;#&quot;&gt;Architect&lt;/a&gt;&lt;/li&gt;
            &lt;/ul&gt;
        &lt;/div&gt;  
</pre>
<p><strong><span style="font-size:14pt">核心JS:动态生成3D球状云+弹出模式窗体-articlemap.js</span></strong></p>
<p>
<pre>$(function() {

    var element = $(&#39;#list a&#39;); ;
    var offset = 0;
    var stepping = 0.02;
    var list = $(&#39;#list&#39;);
    var $list = $(list)

    $list.mousemove(function(e) {
        var topOfList = $list.eq(0).offset().top
        var listHeight = $list.height()
        stepping = (e.clientY - topOfList) / listHeight * 0.2 - 0.1;

    });

    for (var i = element.length - 1; i &gt;= 0; i--) {
        element[i].elemAngle = i * Math.PI * 2 / element.length;
    }

    setInterval(render, 50);

    function render() {
        for (var i = element.length - 1; i &gt;= 0; i--) {

            var angle = element[i].elemAngle + offset;

            x = 120 + Math.sin(angle) * 30;
            y = 45 + Math.cos(angle) * 40;
            size = Math.round(15 - Math.sin(angle) * 15);

            var elementCenter = $(element[i]).width() / 2;

            var leftValue = (($list.width() / 2) * x / 100 - elementCenter) + &quot;px&quot;

            $(element[i]).css(&quot;fontSize&quot;, size + &quot;pt&quot;);
            $(element[i]).css(&quot;opacity&quot;, size / 100);
            $(element[i]).css(&quot;zIndex&quot;, size);
            $(element[i]).css(&quot;left&quot;, leftValue);
            $(element[i]).css(&quot;top&quot;, y + &quot;%&quot;);
        }

        offset += stepping;
    }

});

function ClickLink(item) {
    var typName = $(item).text();

    var links = null;

    switch (typName) {
        case &quot;HTML&quot;:
            links = TrainLinks(Links.HTML);
            break;
        case &quot;CSS&quot;:
            links = TrainLinks(Links.CSS);
            break;
        case &quot;JavaScript&quot;:
            links = TrainLinks(Links.JavaScript);
            break;
        case &quot;Ajax&quot;:
            links = TrainLinks(Links.Ajax);
            break;
        case &quot;Asp.net&quot;:
            links = TrainLinks(Links.Aspnet);
            break;
        case &quot;C#&quot;:
            links = TrainLinks(Links.CSharp);
            break;
        case &quot;Debugging&quot;:
            links = TrainLinks(Links.Debugging);
            break;
        case &quot;Performance&quot;:
            links = TrainLinks(Links.Performance);
            break;
        case &quot;Architect&quot;:
            links = TrainLinks(Links.Architect);
            break;
    }

    CommonHelper.showNoBtnAlert(typName, links);
}

function TrainLinks(arr) {
    var links = &quot;这个可以有，暂时还没有！&quot;;
    if (arr.length &gt; 0) {
        links = &#39;&lt;div&gt;&lt;ul&gt;&#39;;
    }
    for (i = 0, j = arr.length; i &lt; j; i++) {
        links += &#39;&lt;li&gt;&lt;a target=&quot;_blank&quot; href=&quot;&#39; + arr[i][0] + &#39;&quot;&gt;&#39; + arr[i][1] + &#39;&lt;/a&gt;&lt;/li&gt;&#39;;
    }
    if (arr.length &gt; 0) {
        links += &#39;&lt;/ul&gt;&lt;/div&gt;&#39;;
    }

    return links;
}
</pre>
</p>
<p><strong><span style="font-size:14pt">辅助存储推荐文章列表的JS（<span style="color:#993300">未来通过修改这个文件来维护推荐文章</span>）:</span></strong></p>
<pre>var Links = {
    HTML: [],
    CSS: [["http://www.cnblogs.com/justinw/archive/2010/06/16/1758922.html", "制作50个超棒动画效果教程"]],
    JavaScript: [
    ["http://www.cnblogs.com/justinw/archive/2009/11/27/1611728.html", "[原创]AjaxControlToolkitTests自动测试框架完全解析之一：架构篇 "],
    ["http://www.cnblogs.com/justinw/archive/2009/11/30/1613391.html", "[原创]AjaxControlToolkitTests自动测试框架完全解析之二：实现篇(1) "],
    ["http://www.cnblogs.com/justinw/archive/2009/12/02/1615382.html", "[JavaScript] 使用document.createDocumentFragment优化性能 "],
    ],
    Ajax: [],
    Aspnet: [],
    CSharp: [],
    ......
    Performance: [
    ["http://www.cnblogs.com/justinw/archive/2009/11/26/1611397.html", "[推荐]前端性能分析工具：dynaTrace Ajax Edition"]
    ]
}
</pre>
<p><span style="font-size:14pt"><strong>其他：</strong></span></p>
<p><span style="font-size:14pt"></span>这里还引用了jQuery UI的Dialog和一些自定义的CSS设置，具体参考实例即可。</p>
<p><b><span style="font-size:14pt">下载源代码</span></b>：<a href="http://files.cnblogs.com/justinw/articlemap.rar"><img height="62" width="221" src="http://images.cnblogs.com/cnblogs_com/justinw/25027/o_srcdown.jpg" alt="下载源代码"></a></p><img src="http://www.cnblogs.com/justinw/aggbug/1771759.html?type=1" width="1" height="1" alt=""><p>评论: 10　<a href="http://www.cnblogs.com/justinw/archive/2010/07/06/1771759.html#pagedcomment">查看评论</a>　<a href="http://www.cnblogs.com/justinw/archive/2010/07/06/1771759.html#commentform">发表评论</a></p><p><a href="http://job.cnblogs.com/enterprise/2453/">百度期待您的加盟</a></p><hr><p>最新新闻：<br>· <a href="http://news.cnblogs.com/n/67687/">如果Google.cn回来，你会接受吗？</a><span style="color:gray">(2010-07-06 17:39)</span><br>· <a href="http://news.cnblogs.com/n/67686/">高通为Android开发开源处理器驱动</a><span style="color:gray">(2010-07-06 17:38)</span><br>· <a href="http://news.cnblogs.com/n/67685/">360免费杀毒猜想：下一个QQ</a><span style="color:gray">(2010-07-06 17:33)</span><br>· <a href="http://news.cnblogs.com/n/67684/">柳传志：联想仍在创业</a><span style="color:gray">(2010-07-06 17:27)</span><br>· <a href="http://news.cnblogs.com/n/67683/">新浪12日上线团购平台 将通过微博推广</a><span style="color:gray">(2010-07-06 17:21)</span><br></p><p>编辑推荐：<a href="http://www.cnblogs.com/topic/53/">C#大论战：在讨论中学习</a><br></p><p>网站导航：<a href="http://www.cnblogs.com">博客园首页</a>  <a href="http://home.cnblogs.com/">个人主页</a>  <a href="http://news.cnblogs.com">新闻</a>  <a href="http://home.cnblogs.com/ing/">闪存</a>  <a href="http://home.cnblogs.com/group/">小组</a>  <a href="http://space.cnblogs.com/q/">博问</a>  <a href="http://space.cnblogs.com">社区</a>  <a href="http://kb.cnblogs.com">知识库</a></p>