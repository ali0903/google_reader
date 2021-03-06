---
layout: post
title:  "PRISM：一个WPF的合成UI框架"
date:   2010-11-16 16:07:53
author: 
categories: program
---

## PRISM：一个WPF的合成UI框架
### by 
### at 2010-11-16 16:07:53
### original <http://news.cnblogs.com/n/81278/>

<p>　　复杂的客户端应用程序包含了很多需要从各种组件构建而来的图形界面，这些界面也许会被独立的团队进行开发，并像一个庞大的谜题一样组合在一起。这样的应用程序具有所谓的“Composite UI”。这样的应用程序由构建块或模块组成，这些模块被独立开发，并且它们能协同工作并能尽可能不能影响的组合成一个应用程序。用于那样目标的一个解决方案或框架，对于构架师和开发人员是很有帮助的。</p>
<p>　　微软在过去一段时间里，创建了几个这样的解决方案，如<a href="http://msdn2.microsoft.com/en-us/library/aa480450.aspx"> Composite UI Application Block</a> （CAB）：</p>
<blockquote>
<p>　　它提供了一些被证明是成功的实践，来创建基于例如Composite模式这样的知名设计模式的复杂智能客户端用户界面，在其中，简单的用户界面部件能被组合一起创建一个复杂的解决方案，但同时也允许这些部件能被独立地开发、测试和部署。</p>
</blockquote>
<p>　　另外一个微软的解决方案是<a href="http://msdn2.microsoft.com/en-us/library/aa480482.aspx"> Smart Client Software Factory</a>：</p>
<blockquote>
<p>Smart Client Software Factory提供了一个一系列完整的向导，来辅助构架师和开发人员创建复合智能客户端应用程序。</p>
</blockquote>
<p>另外的解决方案有针对Windows Mobile平台的<a href="http://msdn2.microsoft.com/en-us/library/aa480471.aspx"> Mobile Client Software Factory</a>，针对Web的<a href="http://msdn2.microsoft.com/en-us/library/bb264518.aspx"> Web Client Software Factory</a>。</p>
<p>　　PRISM代表了PResentation Integration SysteM，它也是一个由微软支持的针对WPF的项目，其托管在<a href="http://www.codeplex.com/prism">CodePlex</a>上。据一个微软技术产品计划师，Glenn Block所说，PRISM将提供如下优点：</p>
<ul>
<li> 
<ul>
<li>为WPF提供完整的支持 </li>
<li>动态组合用户界面组件 </li>
<li>应用程序模块能被独立的团队开发、测试和部署 </li>
<li>允许逐步采用其中的一些功能 </li>
<li>提供完整的用户体验 </li>
</ul>
</li>
</ul>
<p>　　目前，PRISM包含了一个参考实现，一些可重用的函数库和模式向导”，但是这个计划是开发一个能支撑复合应用程序开发的框架。最新的版本已于2008年4月15日发布了，它还是一个开发中的项目。当前的源代码依赖于<a href="http://www.castleproject.org/castle/download.html">Castle Windsor</a>，但最终版本可能不能再依赖它。</p>
<p><strong>　　查看英文原文：</strong><a href="http://www.infoq.com/news/2008/05/PRISM">PRISM: A WPF Composite UI Framework</a> </p><p><br>　　相关新闻：<br>　　· <a href="http://news.cnblogs.com/n/81255/">SuperSocket即将支持自定义协议</a><span style="color:gray">(2010-11-16)</span><br>　　· <a href="http://news.cnblogs.com/n/78931/">基于.NET/C#开发跨平台WP7、iPhone及Android应用程序</a><span style="color:gray">(2010-10-28)</span><br>　　· <a href="http://news.cnblogs.com/n/78214/">从高司令到Ray Ozzie,技术大牛们的冬天来了？</a><span style="color:gray">(2010-10-22)</span><br>　　· <a href="http://news.cnblogs.com/n/73428/">百万开发者拥戴！七大.NET著名开源项目</a><span style="color:gray">(2010-09-07)</span><br>　　· <a href="http://news.cnblogs.com/n/76477/">远离.NET</a><span style="color:gray">(2010-10-07)</span><br></p><p>　　本文链接：<a href="http://news.cnblogs.com/n/81278/">http://news.cnblogs.com/n/81278/</a></p><p>　　<a href="http://job.cnblogs.com">程序员找工作，就在博客园</a></p><p>　　<a href="http://a4.yeshj.com/rd/34138/">每天10分钟，轻松学外语</a></p><img src="http://news.cnblogs.com/news/rssclick.aspx?id=81278" width="1" height="1" alt="">