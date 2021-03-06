---
layout: post
title:  "配置Nginx＋uwsgi更方便地部署python应用"
date:   2010-10-11 07:33:43
author: observer专栏杂记
categories: program
---

## 配置Nginx＋uwsgi更方便地部署python应用
### by observer专栏杂记
### at 2010-10-11 07:33:43
### original <http://obmem.info/?p=703>

<a href="http://sr.ju690.cn/meme/item/85813">玩聚SR还知道：</a>
<blockquote>
<div style="background-color:#e1ecfe!important;width:auto">
	<div>还有<a href="http://sr.ju690.cn/people/Dotwoo@GoogleReader?i=15098" title="Dotwoo通过GoogleReader分享的其他文章"><img src="http://sr.ju690.cn/static/clogo/4.png?v=690" border="0/"> Dotwoo</a>, <a href="http://sr.ju690.cn/people/est@GoogleReader?i=110" title="est通过GoogleReader分享的其他文章"><img src="http://sr.ju690.cn/static/clogo/4.png?v=690" border="0/"> est</a>, <a href="http://sr.ju690.cn/people/wei@GoogleReader?i=14620" title="wei通过GoogleReader分享的其他文章"><img src="http://sr.ju690.cn/static/clogo/4.png?v=690" border="0/"> wei</a>, 推荐，<a href="http://sr.ju690.cn/meme/item/85813"><b>查看全部 <big style="color:#ff7b7a;font-family:&#39;Georgia&#39;;font-size:16px">13</big> 个推荐 </b></a>
	</div>
</div></blockquote>

<div style="margin:0;padding:10px 0"><a href="http://sr.ju690.cn/author/observer%E4%B8%93%E6%A0%8F%E6%9D%82%E8%AE%B0"><b>observer专栏杂记</b></a>发表于2010-10-11 07:33:43</div>
<div style="margin:0;padding:5px 0"><p>个人觉得php最方便的就是deployment了，只要把php文件丢到支持php的路径里面，然后访问那个路径就能使用了；无论给主机添加多少php应用，只要把目录改好就没你的事了，完全不用关心php-cgi运行得如何，deployment极为方便。</p>
<p>反观python，部属起来真是头痛，常见的部署方法有：</p>
<ol>
<li><strong>fcgi</strong>：用spawn-fcgi或者框架自带的工具对各个project分别生成监听进程，然后和http服务互动</li>
<li><strong>wsgi</strong>：利用http服务的mod_wsgi模块来跑各个project</li>
</ol>
<p>无论哪种都很麻烦，apache的mod_wsgi配置起来很麻烦，内存占用还大，如果要加上nginx作为静态页面的服务器那就更麻烦了；反正我的应用基本上到后来都是是各个project各自为战，且不说管理上的混乱，这样对负载也是不利的，空闲的project和繁忙的project同样需要占用内存，很容易出现站着茅坑不拉屎的现象。</p>
<p>如果有个啥东东能像php-cgi一样监听同一端口，进行统一管理和负载平衡，那真是能省下大量的部署功夫。偶然看到了<a href="http://projects.unbit.it/uwsgi/">uWSGI</a>，才发现居然一直不知道有那么方便地统一部署工具。<br>
<span></span><br>
uWSGI，既不用wsgi协议也不用fcgi协议，而是自创了一个uwsgi的协议，据说<a href="http://www.peterbe.com/plog/fcgi-vs-gunicorn-vs-uwsgi">该协议大约是fcgi协议的10倍那么快</a>，有个比较见下图</p>
<p><img title="fcgi-vs-gunicorn-vs-uwsgi" src="http://www.peterbe.com/plog/fcgi-vs-gunicorn-vs-uwsgi/shootout.png" alt="" width="655" height="582"></p>
<p>uWSGI的主要特点如下，其中一些功能让我感动得泪流满面</p>
<ol>
<li>超快的性能</li>
<li>低内存占用（实测为apache2的mod_wsgi的一半左右）</li>
<li>多app管理（终于不用冥思苦想下个app用哪个端口比较好了-.-）</li>
<li>详尽的日志功能（可以用来分析app性能和瓶颈）</li>
<li>高度可定制（内存大小限制，服务一定次数后重启等）</li>
</ol>
<p>总而言之uwgi是个部署用的好东东，正如uWSGI作者所吹嘘的：</p>
<p><span><em><code>If you are searching for a simple wsgi-only server, uWSGI is not for you, but if you are building a real (production-ready) app that need to be rock-solid, fast and easy to distribute/optimize for various load-average, you will pathetically and morbidly fall in love (we hope) with uWSGI. </code></em></span></p>
<h1><span>正式开工</span></h1>
<p>uwsgi的文档虽然蛮多也很详细，但是他们网站的排版真是让人无语，粗粗看上去根本不知道文档在哪里。其实是在这里：<a href="http://projects.unbit.it/uwsgi/wiki/Doc">http://projects.unbit.it/uwsgi/wiki/Doc</a></p>
<h2>0.安装uwsgi</h2>
<p>ubuntu有uwsgi的ppa</p>

<div><div><pre>add-apt-repository ppa:stevecrozz<span>/</span>ppa
<span>apt-get</span> update
<span>apt-get</span> <span>install</span> uwsgi</pre></div></div>

<h2>1. 用uwsgi代替mod_wsgi</h2>
<p>nginx的整体配置说来话长，我也不再罗嗦了，假设已经明白nginx的基本配置，那么uwsgi就类似这么配置：</p>
<pre lang="c++">location / {
  include uwsgi_params
  uwsgi_pass 127.0.0.1:9090
}</pre>
<p>这就是把所有url传给9090端口的uwsgi协议程序来互动。</p>
<p>再到project目录建立myapp.py，使得application调用框架的wsgi接口，比如web.py就是</p>

<div><div><pre>......
<span>app</span> = web.<span>application</span><span>(</span>urls, <span>globals</span><span>(</span><span>)</span><span>)</span>
application = app.<span>wsgifunc</span><span>(</span><span>)</span></pre></div></div>

<p>再比如django就是</p>

<div><div><pre>.......
<span>from</span> django.<span>core</span>.<span>handlers</span>.<span>wsgi</span> <span>import</span> WSGIHandler
application = WSGIHandler<span>(</span><span>)</span></pre></div></div>

<p>然后运行uwsgi监听9090，其中-w后跟模块名，也就是刚才配置的myapp</p>

<div><div><pre>uwsgi <span>-s</span> :<span>9090</span> <span>-w</span> myapp</pre></div></div>

<p>运行网站发现已经部署完成了。</p>
<h2>2. uwsgi的参数</h2>
<p>以上是单个project的最简单化部署，uwsgi还是有很多令人称赞的功能的，例如</p>
<p>并发4个线程</p>

<div><div><pre>uwsgi <span>-s</span> :<span>9090</span> <span>-w</span> myapp <span>-p</span> <span>4</span></pre></div></div>

<p>主控制线程+4个线程</p>

<div><div><pre>uwsgi <span>-s</span> :<span>9090</span> <span>-w</span> myapp <span>-M</span> <span>-p</span> <span>4</span></pre></div></div>

<p>执行超过30秒的client直接放弃</p>

<div><div><pre>uwsgi <span>-s</span> :<span>9090</span> <span>-w</span> myapp <span>-M</span> <span>-p</span> <span>4</span> <span>-t</span> <span>30</span></pre></div></div>

<p>限制内存空间128M</p>

<div><div><pre>uwsgi <span>-s</span> :<span>9090</span> <span>-w</span> myapp <span>-M</span> <span>-p</span> <span>4</span> <span>-t</span> <span>30</span> <span>--limit-as</span> <span>128</span></pre></div></div>

<p>服务超过10000个req自动respawn</p>

<div><div><pre>uwsgi <span>-s</span> :<span>9090</span> <span>-w</span> myapp <span>-M</span> <span>-p</span> <span>4</span> <span>-t</span> <span>30</span> <span>--limit-as</span> <span>128</span> <span>-R</span> <span>10000</span></pre></div></div>

<p>后台运行等</p>

<div><div><pre>uwsgi <span>-s</span> :<span>9090</span> <span>-w</span> myapp <span>-M</span> <span>-p</span> <span>4</span> <span>-t</span> <span>30</span> <span>--limit-as</span> <span>128</span> <span>-R</span> <span>10000</span> <span>-d</span> uwsgi.log</pre></div></div>

<p>更多用法见文档：<a href="http://projects.unbit.it/uwsgi/wiki/Doc">http://projects.unbit.it/uwsgi/wiki/Doc</a></p>
<h2>3.为uwsgi配置多个站点</h2>
<p>为了让多个站点共享一个uwsgi服务，必须把uwsgi运行成虚拟站点：去掉“-w myapp”加上”–vhost”</p>

<div><div><pre>uwsgi <span>-s</span> :<span>9090</span> <span>-M</span> <span>-p</span> <span>4</span> <span>-t</span> <span>30</span> <span>--limit-as</span> <span>128</span> <span>-R</span> <span>10000</span> <span>-d</span> uwsgi.log <span>--vhost</span></pre></div></div>

<p>然后必须配置virtualenv，virtualenv是python的一个很有用的虚拟环境工具，这样安装</p>

<div><div><pre><span>apt-get</span> <span>install</span> python-setuptools
easy_install virtualenv</pre></div></div>

<p>然后设置一个/多个app基准环境</p>

<div><div><pre>virtualenv <span>/</span>var<span>/</span>www<span>/</span>myenv</pre></div></div>

<p>应用环境，在此环境下安装的软件仅在此环境下有效</p>

<div><div><pre><span>source</span> <span>/</span>var<span>/</span>www<span>/</span>myenv<span>/</span>bin<span>/</span>activate
pip <span>install</span> django
pip <span>install</span> mako
...</pre></div></div>

<p>最后配置nginx，注意每个站点必须单独占用一个server，同一server不同location定向到不同的应用不知为何总是失败，我猜也算是一个bug。</p>

<div><div><pre>   server <span>{</span>
        listen       <span>80</span><span>;</span>
        server_name  app1.<span>mydomain</span>.<span>com</span><span>;</span>
        location <span>/</span> <span>{</span>
                include uwsgi_params<span>;</span>
                uwsgi_pass 127.0.0.1<span>:</span><span>9090</span><span>;</span>
                uwsgi_param UWSGI_PYHOME <span>/</span>var<span>/</span>www<span>/</span>myenv<span>;</span>
                uwsgi_param UWSGI_SCRIPT myapp1<span>;</span>
                uwsgi_param UWSGI_CHDIR <span>/</span>var<span>/</span>www<span>/</span>myappdir1<span>;</span>
        <span>}</span>
    <span>}</span>
    server <span>{</span>
        listen       <span>80</span><span>;</span>
        server_name  app2.<span>mydomain</span>.<span>com</span><span>;</span>
        location <span>/</span> <span>{</span>
                include uwsgi_params<span>;</span>
                uwsgi_pass 127.0.0.1<span>:</span><span>9090</span><span>;</span>
                uwsgi_param UWSGI_PYHOME <span>/</span>var<span>/</span>www<span>/</span>myenv<span>;</span>
                uwsgi_param UWSGI_SCRIPT myapp2<span>;</span>
                uwsgi_param UWSGI_CHDIR <span>/</span>var<span>/</span>www<span>/</span>myappdir2<span>;</span>
        <span>}</span>
    <span>}</span></pre></div></div>

<p>如此这般，重启nginx服务，两个站点就可以共用一个uwsgi服务了。</p>
<h2>4.实战应用</h2>
<p>最初的设置完毕以后，再添加的应用，只需要在nginx里面进行少量修改，无需重启uwsgi，就能立刻部署完毕。uwsgi自带了基于django的监控uwsgi运行状态的工具，就拿它来部署好了：</p>

<div><div><pre>server <span>{</span>
    listen <span>80</span><span>;</span>
    root   <span>/</span>var<span>/</span>www<span>/</span>django1.23<span>;</span>
    index  index.<span>html</span> index.<span>htm</span><span>;</span>
    server_name uwsgiadmin.<span>django</span>.<span>obmem</span>.<span>info</span><span>;</span>
    access_log  <span>/</span>var<span>/</span>log<span>/</span>nginx<span>/</span>django.<span>access</span>.<span>log</span><span>;</span>
    location <span>/</span>media<span>/</span> <span>{</span>
        root <span>/</span>var<span>/</span>www<span>/</span>django1.23<span>/</span>adminmedia<span>;</span>
        rewrite <span>^/</span>media<span>/</span><span>(</span>.<span>*</span><span>)</span>$ <span>/</span>$<span>1</span> <span>break</span><span>;</span>
    <span>}</span>
    location <span>/</span> <span>{</span>
        include uwsgi_params<span>;</span>
        uwsgi_pass 127.0.0.1<span>:</span><span>9090</span><span>;</span>
        uwsgi_param UWSGI_PYHOME <span>/</span>var<span>/</span>www<span>/</span>django1.23<span>/</span>vtenv<span>;</span>
        uwsgi_param UWSGI_CHDIR <span>/</span>var<span>/</span>www<span>/</span>django1.23<span>/</span>uwsgiadmin<span>;</span>
        uwsgi_param UWSGI_SCRIPT uwsgiadmin_wsgi<span>;</span>
    <span>}</span>
<span>}</span></pre></div></div>

<p>于是uwsgi的监控信息可以在<a href="http://uwsgiadmin.django.obmem.info">http://uwsgiadmin.django.obmem.info</a> 看到用户名密码都是admin。</p>
<p>再比如<a href="http://code.google.com/p/lbforum/">LBForum</a>论坛程序的部署：根据<a href="http://github.com/vicalloy/LBForum">安装说明</a>安装完毕，再按<a href="http://vik.haoluobo.com/lbforum/topic/60/">部署说明</a>修改完配置文件，然后只需修改nginx配置文件：</p>

<div><div><pre>server <span>{</span>
    listen <span>80</span><span>;</span>
    root   <span>/</span>var<span>/</span>www<span>/</span>django1.23<span>;</span>
    index  index.<span>html</span> index.<span>htm</span><span>;</span>
    server_name lbforum.<span>django</span>.<span>obmem</span>.<span>info</span><span>;</span>
    access_log  <span>/</span>var<span>/</span>log<span>/</span>nginx<span>/</span>django.<span>access</span>.<span>log</span><span>;</span>
    location <span>/</span> <span>{</span>
        include uwsgi_params<span>;</span>
        uwsgi_pass 127.0.0.1<span>:</span><span>9090</span><span>;</span>
        uwsgi_param UWSGI_PYHOME <span>/</span>var<span>/</span>www<span>/</span>django1.23<span>/</span>vtenv<span>;</span>
        uwsgi_param UWSGI_CHDIR <span>/</span>var<span>/</span>www<span>/</span>django1.23<span>/</span>LBForum<span>/</span>sites<span>/</span><span>default</span><span>;</span>
        uwsgi_param UWSGI_SCRIPT lbforum_wsgi<span>;</span>
    <span>}</span>
<span>}</span></pre></div></div>

<p>于是 <a href="http://lbforum.django.obmem.info">http://lbforum.django.obmem.info</a> 就是论坛程序了。</p>
<h1><span>后记</span></h1>
<p>虽然写出来寥寥几行，配置的时候我可吃尽了uwsgi的苦头，有些想当然的用法完全不能成立，–no-site参数一加上去其他都好使LBForum怎么都部署不了，一开始多站点公用uwsgi怎么都成功不了等等。</p>
<p>python世界很有趣，一直会发现好玩的东东，但是python世界也很折腾人，大部分东东都是dev版本，文档缺失，各种兼容问题……大约是因为在python中，有个idea到实现出来实在是太过高效的关系吧，唉，被折腾死了。</p></div>


<div style="padding-top:20px;border-top:1px solid #cbd9d9;padding-bottom:10px;FONT-SIZE:13px">
<strong>推荐相关的热文: </strong><br> 
	<div><img src="https://www.google.com/s2/favicons?domain=it.solidot.org" border="0/"> <a title="I2P 0.7.8发布 | 16个推荐" href="http://sr.ju690.cn/meme/item/53742">I2P 0.7.8发布 | 16荐</a>
	</div> 
	<div><img src="https://www.google.com/s2/favicons?domain=xbeta.info" border="0/"> <a title="第三篇 打造JAVA开发环境——EditPlus篇 | 12个推荐" href="http://sr.ju690.cn/meme/item/83594">第三篇 打造JAVA开发环境——EditPlus篇 | 12荐</a>
	</div> 
	<div><img src="https://www.google.com/s2/favicons?domain=ubuntu.tmublr.com" border="0/"> <a title="Ubuntu 上安装及配置 Cherokee , 号称最快的 Web 服务器 | 4个推荐" href="http://sr.ju690.cn/meme/item/73300">Ubuntu 上安装及配置 Cherokee , 号称最快的 Web 服务器 | 4荐</a>
	</div> 
	<div><img src="https://www.google.com/s2/favicons?domain=www.gaoang.com" border="0/"> <a title="Hadoop Studio开发部署MapReduce应用 | 4个推荐" href="http://sr.ju690.cn/meme/item/40832">Hadoop Studio开发部署MapReduce应用 | 4荐</a>
	</div> 
	<div><img src="https://www.google.com/s2/favicons?domain=code.google.com" border="0/"> <a title="simplecd -    Project Hosting on Google Code | 19个推荐" href="http://sr.ju690.cn/meme/item/55063">simplecd -    Project Hosting on Google .. | 19荐</a>
	</div> 
	<div><img src="https://www.google.com/s2/favicons?domain=eishn.blog.163.com" border="0/"> <a title="关于 WSGI | 10个推荐" href="http://sr.ju690.cn/meme/item/62044">关于 WSGI | 10荐</a>
	</div> 
	<div><img src="https://www.google.com/s2/favicons?domain=www.gtdstudy.com" border="0/"> <a title="Emacs 普通人的编辑利器 | 20个推荐" href="http://sr.ju690.cn/meme/item/84940">Emacs 普通人的编辑利器 | 20荐</a>
	</div> 
	<div><img src="https://www.google.com/s2/favicons?domain=apple4.us" border="0/"> <a title="工具癖系列之 Jjgod | 7个推荐" href="http://sr.ju690.cn/meme/item/34602">工具癖系列之 Jjgod | 7荐</a>
	</div> 
	<div><img src="https://www.google.com/s2/favicons?domain=internet.solidot.org" border="0/"> <a title="帮助更多人连上 Tor | 33个推荐" href="http://sr.ju690.cn/meme/item/43525">帮助更多人连上 Tor | 33荐</a>
	</div> 
	<div><img src="https://www.google.com/s2/favicons?domain=emulefans.com" border="0/"> <a title="在eMule中调用外部程序进行预览 | 7个推荐" href="http://sr.ju690.cn/meme/item/84192">在eMule中调用外部程序进行预览 | 7荐</a>
	</div>
</div>
<div style="float:left;margin:2px 10px 2px 2px">
<a href="http://sr.ju690.cn" title="玩聚SR"><img src="http://sr.ju690.cn/static/images/fish.jpg" border="0" width="80px"></a>
</div>
<div><a href="http://sr.ju690.cn/">The readings, filtered by social network!</a><br>阅读<a href="http://sr.ju690.cn/tag/tech">科技</a>频道热文。手机请登录<a href="http://sr.ju690.cn/m">移动版</a>。</div>