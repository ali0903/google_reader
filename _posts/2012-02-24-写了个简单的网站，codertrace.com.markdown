---
layout: post
title:  "写了个简单的网站，codertrace.com"
date:   2012-02-24 09:22:00
author: Kevin Lynx
categories: program
---

## 写了个简单的网站，codertrace.com
### by Kevin Lynx
### at 2012-02-24 09:22:00
### original <http://www.udpwork.com/item/6895.html>

<h3>简介</h3>
<p>因为写<a href="https://github.com/kevinlynx/ext-blog">ext-blog</a>的原因，慢慢喜欢上github_ 。然后突然有一天产生了一个想法：如果可以把自己的博客_ 和<a href="https://github.com/kevinlynx">github主页</a>集中到一块展示给别人，会不会是一种很方便的自我简介方式？然后我就动手写了<a href="http://codertrace.com">codertrace.com</a>。</p>
<p>所以，<a href="http://codertrace.com">codertrace.com</a>这个网站的作用就是用来集中让程序员炫耀的。它通过RSS抓取，将你的博客，github主页，或其他有RSS输出的信息集中到一块展示给别人。这些信息通常就可以代表一个程序员。</p>
<p>如果你是程序员，也不妨试试。</p>
<img src="http://www.cppblog.com/images/cppblog_com/kevinlynx/codertrace.png"><h3>技术信息</h3>
<p>不知道哪个王八蛋说的，程序员每一年得学一门新语言。我2010年末接触了Lisp，然后莫名其妙地写了<a href="https://github.com/kevinlynx/ext-blog">ext-blog</a>，又莫名其妙地在2011年末接触了Ruby。因为大学期间太痴迷C++，我勤奋努力，几乎通晓这门语言的各种细节；后来又稍微实践了下编译原理。在这若干年间，断断续续也接触过其他脚本类语言，我甚至在android上用java写过几个<a href="http://kevinlynx.iteye.com">小应用</a>。基于这些积累，我发现我可以很快上手Ruby，然后再上手Rails，然后就有了<a href="http://codertrace.com">codertrace.com</a>（当然还做过一些小的<a href="http://klquiz.heroku.com">APP</a>)</p>
<p>所以，<a href="http://codertrace.com">codertrace.com</a>就是一个Ruby on Rails的应用。当我用这货来做WEB的时候，我才发现曾经用Lisp写博客是多么geek。这种感觉就像你在用汇编写一个GUI程序一样。我的意思是，ruby/rails的世界里有太多现成的东西，但lisp的世界里没有。</p>
<p>而且，ruby是一个很爽的语言。我太喜欢它的closure语法，简洁，不需要加其他关键字就可以构造（例如其他语言map(function (item) xxxx end)，或者map(lambda (item) xxx )）。但我不喜欢在使用的地方通过yield去调用---这就像一个hack。我更不喜欢ruby用proc去封装closure。好吧，这其实是我自我分裂，为什么我要把ruby看成一个函数式语言？</p>
<p>脚本语言真是太酷了。</p>
<h3>服务器信息</h3>
<p>我很穷。不管你信不信，我真的舍不得花1000RMB买个VPS来架设<a href="http://codertrace.com">codertrace.com</a>。目前，<a href="http://codertrace.com">codertrace.com</a>架设在<a href="http://heroku.com">heroku.com</a>，而且还使用的是免费服务。免费服务竟然只有5M数据库。<a href="http://codertrace.com">codertrace.com</a>后台为了异步抓取用户提供的RSS，还使用了一个单独的进程(delayed_job ruby gem)。这也不是免费的。</p>
<p>但ruby的世界里有太多现成的东西了，甚至有很多现成的库解决这里的两个问题：heroku_external_db，这个gem可以让codertrace使用heroku以外的数据库，然后我就在我的VPS上搭了个mysql，这下流量和网站响应速度悲剧了啊，你想你请求个页面，这个页面直接涉及到若干条数据库查询。而这些查询的请求和回应竟然是通过internet网络传输的。</p>
<p>workless，这个gem可以在有异步任务时，例如codertrace上读取RSS，就会自动开启这个worker进程，然后heroku开始计费，当没有任务时，它又自动关闭这个进程。虽然省了美元，但再一次让网站的响应速度打了折扣。</p>
<p>为了实现自定义域名，我需要将<a href="http://codertrace.com">codertrace.com</a>指向<a href="http://heroku.com">heroku.com</a>提供的IP。但也许你会同我一样愤怒，因为它提供的几个IP都被GFW墙了！所以，目前的实现方案是，我将<a href="http://codertrace.com">codertrace.com</a>指向了我博客对应的VPS，然后在VPS上使用nginx反向代理到<a href="http://heroku.com">heroku.com</a>提供的IP。即使如此，我最近甚至发现<a href="http://codertrace.com">codertrace.com</a>竟然神奇般地会域名解析错误，难道godaddy的name server也要被GFW和谐？？</p>
<h3>故事</h3>
<p>作为一个宅男，在工作的若干年中，若干个假期我都用来打游戏，或者写程序。</p>
<p>所以，当这个成为习惯的时候，<a href="http://codertrace.com">codertrace.com</a>，就顺理成章地消费了我今年的春节假期。我发现一个人窝在租的小房子里写代码是件很爽的事情。在当前这个社会环境下，你可以专注地去干件喜欢的事情，还不用处理各种生活琐事，真是太爽了。</p>
<p>但为什么我平时得不到这种感觉？因为，我，是一个没钱的程序员。我和我老婆租在一个标间里。在这样狭小的空间里，多个人就是多几倍干扰。这太残酷了。</p>
<h3>末了</h3>
<p>曾经我以为我很牛逼，曾经我以为程序员很牛逼。后来我慢慢发现自己很垃圾。我没有写出来过牛逼的程序，大概也没能力写。还记得那个程序员的故事吗？就是有个傻逼也以为程序员很牛逼，但不幸在一家非IT公司的IT部门工作，他的程序员同事的工作就是每周填个excel表格。他后来很绝望，因为他没有为世界贡献过任何代码。后来，这货丢下一切，坐上去某地的飞机走了。</p>
<img src="http://www.cppblog.com/kevinlynx/aggbug/166394.html">
<br>

<br>
<div><a href="http://www.cppblog.com/kevinlynx/">Kevin Lynx</a>2012-02-24 09:22<a href="http://www.cppblog.com/kevinlynx/archive/2012/02/24/166394.html#Feedback">发表评论</a></div>

			<div style="margin-top:8px;padding:6px 0;border-top:1px solid #3cf">
				<div style="text-align:center;margin:16px 0;padding:6px;border:0px dashed #999;font-family:arial;font-size:26px;font-weight:bold">
	<a href="http://www.udpwork.com/item/6895.html#review_form" title="不喜欢" style="text-decoration:none">
		<img src="http://www.udpwork.com//images/thumb_down24.gif" alt="">
		<span style="color:#f33">0</span>
	</a>
	   
	<a href="http://www.udpwork.com/item/6895.html#review_form" title="喜欢" style="text-decoration:none">
		<img src="http://www.udpwork.com//images/thumb_up24.gif" alt="">
		<span style="color:#3c3">0</span>
	</a>
</div>				<p>
					由 <a href="http://www.udpwork.com/">IT 牛人博客聚合网站(udpwork.com)</a> 聚合
					|
					<a href="http://www.udpwork.com/item/6895.html#reviews">评论: 0</a>
				</p>
			</div>