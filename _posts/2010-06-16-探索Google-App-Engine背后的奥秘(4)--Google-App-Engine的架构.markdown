---
layout: post
title:  "探索Google App Engine背后的奥秘(4)- Google App Engine的架构"
date:   2010-06-16 02:16:24
author: dbanotes@gmail.com(Fenng)
categories: program
---

## 探索Google App Engine背后的奥秘(4)- Google App Engine的架构
### by dbanotes@gmail.com(Fenng)
### at 2010-06-16 02:16:24
### original <http://www.dbanotes.net/arch/google_app_engine-arch_intro.html>

<p> By：<a href="http://peopleyun.com/">ikewu</a> posted @ <a href="http://www.dbanotes.net/">dbanotes.net</a>. <a href="http://www.dbanotes.net/index.xml">RSS</a> | Ad.<a href="http://www.infoq.com/cn/vendorcontent/show.action?vcr=1047">Adobe Flash Builder 4 简体中文正式版下载</a>

<blockquote>按：此为客座博文系列。投稿人<a href="http://peopleyun.com/">吴朱华</a>曾在IBM中国研究院从事与云计算相关的研究，现在正致力于研究云计算技术。</blockquote>

<p>本篇将首先介绍App Engine的一些设计理念，接着将对App Engine的组成部分等进行介绍。</p>

<h1><strong>设计理念</strong></h1>

<p>App Engine在设计理念方面，主要可以总结为下面这五条：</p>

<ul>
<li>重用现有的Google技术：大家都知道，重用是软件工程的核心理念之一，因为通过重用不仅能减低开发成本，而且能简化架构。在App Engine开发的过程中，重用的思想也得到了非常好的体现，比如Datastore是基于Google的bigtable技术，Images服务是基于Picasa的，用户认证服务是利用Google Account的，Email服务是基于Gmail的等。 </li>
<li>无状态：为了让更好地支持扩展，Google没有在应用服务器层存储任何重要的状态，而主要在datastore这层对数据进行持久化，这样当应用流量突然爆发时，可以通过为应用添加新的服务器来实现扩展。 </li>
<li>硬限制：App Engine对运行在其之上的应用代码设置了很多硬性限制，比如无法创建Socket和Thread等有限的系统资源，这样能保证不让一些恶性的应用影响到与其临近应用的正常运行，同时也能保证在应用之间能做到一定的隔离。 </li>
<li>利用Protocol Buffers技术来解决服务方面的异构性：应用服务器和很多服务相连，有可能会出现异构性的问题，比如应用服务器是用Java写的，而部分服务是用C++写的等。Google在这方面的解决方法是基于语言中立，平台中立和可扩展的Protocol Buffer，并且在App Engine平台上所有API的调用都需要在进行RPC（Remote Procedure Call，远程方面调用）之前被编译成Protocol Buffer的二进制格式。 </li>
<li>分布式数据库：因为App Engine将支撑海量的网络应用，所以独立数据库的设计肯定是不可取的，而且很有可能将面对起伏不定的流量，所以需要一个分布式的数据库来支撑海量的数据和海量的查询。 </li>
</ul>

<h1><strong>组成部分</strong></h1>

<p><a href="http://www.dbanotes.net/Images/GAE%20ARCH.jpg"><img alt="GAE ARCH.jpg" src="http://www.dbanotes.net/assets_c/2010/06/GAE%20ARCH-thumb-580x434-59.jpg" width="580" height="434"></a> </p>
<p align="center">图1. GAE的架构图（图源自参[6]）</p>

<p>简单而言，其架构可以分为三个部分：前端，Datastore和服务群：</p>

<p><strong>前端</strong></p>

<p>共包括四个模块：</p>

<ul>
<li>Front End：既可以认为它是Load Balancer，也可以认为它是Proxy，它主要负责负载均衡和将请求转发给App Server（应用服务器）或者Static Files等工作。 </li>
<li>Static Files：在概念上，比较类似于CDN（Content Delivery Network，内容分发网络），用于存储和传送那些应用附带的静态文件，比如图片，CSS和JS脚本等。 </li>
<li>App Server：用于处理用户发来的请求，并根据请求的内容来调用后面的Datastore和服务群。 </li>
<li>App Master：是在应用服务器间调度应用，并将调度之后的情况通知Front End。 </li>
</ul>

<p><strong>Datastore</strong></p>

<p>它是基于BigTable技术的分布式数据库，虽然其也可以被理解成为一个服务，但是由于其是整个App Engine唯一存储持久化数据的地方，所以其是App Engine中一个非常核心的模块。其具体细节将在下篇和大家讨论。</p>

<p><strong>服务群</strong></p>

<p>整个服务群包括很多服务供App Server调用，比如Memcache，图形，用户，URL抓取和任务队列等。</p>

<h1>Python版和Java版App Engine在实现方面的区别</h1>

<p>因为大多数服务都可以被这两个版本共享，所以两者之间的区别主要集中在App Server端，Python版App Server应该是经过Google修改的Python Runtime，版本号应该是2.5.2，而Java版App Server是基于Jetty 6的，因为它的体积和最常用的Tomcat相比更娇小，这样能使得一台服务器支持更多的应用，而且其应该经过Google的一定的修改。</p>

<h1>流程</h1>

<p>在这里举一个普通的HTTP请求的处理流程为例：</p>

<ul>
<li>用户发送一个HTTP请求。 </li>
<li>Front End接受这个请求，并将这个请求转发给一个空闲的App Server。 </li>
<li>App Server会处理这个请求。
<li>检查用于处理这个请求的Handler是不是已经被初始化了，如果没有的话，需要对这个Handler进行初始化。 </li>
<li>调用服务群的用户认证服务来对用户进行认证，如果失败的话，需要终止整个请求的处理工作，并返回用户无法被认证的信息。 </li>
<li>查看这个请求所需的数据是否已经缓存在Memcahe中，如果没有的话，将对Datastore发出查询请求来得到数据。 </li>
<li>通过整合上步得到数据来生成相关的HTML，并返回给用户。 </li>
</li>
<li>由于HTML里面会包含对一些静态文件的引用，比如图片和CSS等，所以当用户收到HTML之后，还会通过Front End对Static Files里面存储的静态文件进行读取。 </li>
</ul>

<p>本篇结束，下篇将关注App Engine最核心的Datastore的设计。</p>

<p>--EOF--</p></p>
<hr>
<p><strong>最近文章|Recent Articles</strong></p>
   <ul>
    
      <li><a href="http://www.dbanotes.net/review/3par_acquired_by_HP.html">3PAR 争夺战</a></li>
    
      <li><a href="http://www.dbanotes.net/mylife/inception.html">盗梦空间（Inception）</a></li>
    
      <li><a href="http://www.dbanotes.net/jobs/howto_choose_company_style.html">去大公司还是去小公司工作？</a></li>
    
      <li><a href="http://www.dbanotes.net/review/web_product.html">Web 产品的改进</a></li>
    
   </ul>
<p>本站赞助商：<a href="http://www.douban.com/">豆瓣网</a></p>
<p><strong> 评论数(0)|<a href="http://www.dbanotes.net/arch/google_app_engine-arch_intro.html#comments" title="Comment on: 探索Google App Engine背后的奥秘(4)- Google App Engine的架构">添加评论</a></strong> | 最近作者还说了什么? Follow <a href="http://www.twitter.com/fenng">Fenng@Twitter</a><br>本文网址：<a href="http://www.dbanotes.net/arch/google_app_engine-arch_intro.html">http://www.dbanotes.net/arch/google_app_engine-arch_intro.html</a></p>
<p>DBA Notes 理念: 用简约的技术取得最大的收益...</p>



<div name="ClickComments"></div>

<div>
<a href="http://feeds.feedburner.com/~ff/webarch?a=O4hj4fO5QNM:v8Ld0YzSG4A:yIl2AUoC8zA"><img src="http://feeds.feedburner.com/~ff/webarch?d=yIl2AUoC8zA" border="0"></a> <a href="http://feeds.feedburner.com/~ff/webarch?a=O4hj4fO5QNM:v8Ld0YzSG4A:qj6IDK7rITs"><img src="http://feeds.feedburner.com/~ff/webarch?d=qj6IDK7rITs" border="0"></a> <a href="http://feeds.feedburner.com/~ff/webarch?a=O4hj4fO5QNM:v8Ld0YzSG4A:F7zBnMyn0Lo"><img src="http://feeds.feedburner.com/~ff/webarch?i=O4hj4fO5QNM:v8Ld0YzSG4A:F7zBnMyn0Lo" border="0"></a> <a href="http://feeds.feedburner.com/~ff/webarch?a=O4hj4fO5QNM:v8Ld0YzSG4A:V_sGLiPBpWU"><img src="http://feeds.feedburner.com/~ff/webarch?i=O4hj4fO5QNM:v8Ld0YzSG4A:V_sGLiPBpWU" border="0"></a> <a href="http://feeds.feedburner.com/~ff/webarch?a=O4hj4fO5QNM:v8Ld0YzSG4A:mqyYa2mfVbY"><img src="http://feeds.feedburner.com/~ff/webarch?d=mqyYa2mfVbY" border="0"></a> <a href="http://feeds.feedburner.com/~ff/webarch?a=O4hj4fO5QNM:v8Ld0YzSG4A:I9og5sOYxJI"><img src="http://feeds.feedburner.com/~ff/webarch?d=I9og5sOYxJI" border="0"></a> <a href="http://feeds.feedburner.com/~ff/webarch?a=O4hj4fO5QNM:v8Ld0YzSG4A:bcOpcFrp8Mo"><img src="http://feeds.feedburner.com/~ff/webarch?d=bcOpcFrp8Mo" border="0"></a>
</div>