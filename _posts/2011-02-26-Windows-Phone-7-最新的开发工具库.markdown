---
layout: post
title:  "Windows Phone 7 最新的开发工具库"
date:   2011-02-26 11:42:00
author: Alexis
categories: program
---

## Windows Phone 7 最新的开发工具库
### by Alexis
### at 2011-02-26 11:42:00
### original <http://www.cnblogs.com/alexis/archive/2011/02/26/1965649.html>

<p><p>Shawn Wildermuth 在CodePlex上发布了一个新的WP7工具库的项目，包含了WP7控件、转换类、帮助类等等。</p> <p>项目的地址为：<a title="http://phoney.codeplex.com/" href="http://phoney.codeplex.com/">http://phoney.codeplex.com/</a></p> <p>这是项目解决方案的截图，包含了示例文件</p> <p><img style="margin:0px 0px 20px" src="http://images.cnblogs.com/cnblogs_com/alexis/20110225WP01.jpg"></p> <p><font color="#ff0000">如果你没有安装silverlight toolkit的话直接运行程序会报如下的错误</font>，因为默认在装Dev10的时候不会安装Silverlight的Testing库的。</p> <p><img style="margin:0px 0px 20px" src="http://images.cnblogs.com/cnblogs_com/alexis/20110225WP02.jpg"><img style="margin:0px 0px 20px" src="http://images.cnblogs.com/cnblogs_com/alexis/20110225WP03.jpg"></p> <p><strong><font size="6">项目描述</font></strong></p> <p>该项目宝航了一些供Windows Phone 7应用程序的类和控件，如下：</p> <p>标准的转换器、一些控件、Phone日志、淡入淡出消息类、BitlyHelper 类、Twitter帮助类等</p> <p>  <p><strong><font size="6">实例</font></strong></p> <p><strong>BitlyHelper 类</strong></p> <p>一个通过使用设置自己的关键字/用户名的键值对来缩短URI的类</p> <p>使用方法：</p><pre><span style="color:#2b91af">BitlyHelper</span>.SetCredentials(<span style="color:#a31515">"MYBITLYAPIKEY"</span>, <span style="color:#a31515">"MYBITLYUSERNAME"</span>);
<span style="color:#2b91af">BitlyHelper</span>.Shorten(<span style="color:#a31515">"http://phoney.codeplex.com"</span>, (result, error) =&gt;
  {
    <span style="color:blue">if </span>(error != <span style="color:blue">null</span>)
    {
      <span style="color:#2b91af">MessageBox</span>.Show(<span style="color:blue">string</span>.Concat(<span style="color:#a31515">"Error Shortening Url: "</span>, error.Message));
    }
    <span style="color:blue">else
    </span>{ 
      MessageBox.Show(result, 3000);
    }
  });
</pre>
<p> </p>
<p><strong>FadingMessage 类</strong></p>
<p>创建一个渐入渐出的提示消息，使用方法如下：</p><pre><span style="color:#2b91af">FadingMessage</span>.ShowTextMessage(<span style="color:#a31515">"Saved..."</span>);
</pre>
<p><img style="margin:0px 0px 20px" src="http://images.cnblogs.com/cnblogs_com/alexis/20110225WP04.jpg"></p>
<p>当然你也可以自己定义消息的样式：</p><pre><span style="color:#2b91af">FadingMessage </span>msg = <span style="color:blue">new </span><span style="color:#2b91af">FadingMessage</span>()
{
  MessageToShow = theMessage,
  VerticalAlignment = <span style="color:#2b91af">VerticalAlignment</span>.Top,
  HorizontalAlignment = <span style="color:#2b91af">HorizontalAlignment</span>.Right
};
msg.Show(3000); <span style="color:green">// 3 seconds
</span></pre>
<p> </p>
<p><br><strong>Phone日志类</strong></p>
<p>这个类是用来帮助你记录Phone中的简单日志的，使用方法为：</p><pre><span style="color:#2b91af">PhoneLogger</span>.LogError(<span style="color:#a31515">"Test Logging Message"</span>);

var log = <span style="color:#2b91af">PhoneLogger</span>.LogContents;

</pre>
<p> </p>
<p>该项目中还包含一些颜色、笔刷、字体等资源。</p>
<p>项目源文件下载地址：<a title="http://phoney.codeplex.com/" href="http://phoney.codeplex.com/">http://phoney.codeplex.com/</a></p>
<p> </p>
<p>这里提供能直接运行的源文件：<a href="http://files.cnblogs.com/alexis/PhoneyTools.rar"><img style="margin:0px 0px 20px" src="http://images.cnblogs.com/cnblogs_com/alexis/download.gif"></a></p><img src="http://www.cnblogs.com/alexis/aggbug/1965649.html?type=1" width="1" height="1" alt=""><p>作者: <a href="http://www.cnblogs.com/alexis/">Alexis</a> 发表于 2011-02-26 11:42 <a href="http://www.cnblogs.com/alexis/archive/2011/02/26/1965649.html">原文链接</a></p><p>评论: 5　<a href="http://www.cnblogs.com/alexis/archive/2011/02/26/1965649.html#pagedcomment">查看评论</a>　<a href="http://www.cnblogs.com/alexis/archive/2011/02/26/1965649.html#commentform">发表评论</a></p><hr><p>最新新闻：<br>· <a href="http://news.cnblogs.com/n/92269/">UE3开发者工具包免特许权使用费金额提高至5万美元</a><span style="color:gray">(2011-02-26 23:28)</span><br>· <a href="http://news.cnblogs.com/n/92268/">雅虎计划重构 Hadoop-MapReduce，解决性能瓶颈</a><span style="color:gray">(2011-02-26 23:15)</span><br>· <a href="http://news.cnblogs.com/n/92267/">Facebook新隐私保护政策删除晦涩法律术语</a><span style="color:gray">(2011-02-26 22:07)</span><br>· <a href="http://news.cnblogs.com/n/92266/">SNS游戏Ravenwood Fair运营有道：玩家超千万</a><span style="color:gray">(2011-02-26 22:05)</span><br>· <a href="http://news.cnblogs.com/n/92265/">10件事让你成为一个更好的网页设计师</a><span style="color:gray">(2011-02-26 21:57)</span><br></p><p>编辑推荐：<a href="http://kb.cnblogs.com/page/92260/">热点回顾：不要困在自己建造的盒子里--写给.NET程序员</a><br></p><p>网站导航：<a href="http://www.cnblogs.com">博客园首页</a>  <a href="http://home.cnblogs.com/">我的园子</a>  <a href="http://news.cnblogs.com">新闻</a>  <a href="http://home.cnblogs.com/ing/">闪存</a>  <a href="http://home.cnblogs.com/group/">小组</a>  <a href="http://space.cnblogs.com/q/">博问</a>  <a href="http://kb.cnblogs.com">知识库</a></p></p></p>