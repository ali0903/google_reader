---
layout: post
title:  "如何让 Instagram 在一百多万用户爆炸后稳定运行"
date:   2012-11-19 00:06:00
author: log4d
categories: program
---

## 如何让 Instagram 在一百多万用户爆炸后稳定运行
### by log4d
### at 2012-11-19 00:06:00
### original <http://www.udpwork.com/item/8698.html>

<p>原文链接：<a href="http://instagram-engineering.tumblr.com/post/20541814340/keeping-instagram-up-with-over-a-million-new-users-in">Keeping Instagram up with over a million new users in twelve hours</a></p>
<p>这周二我们发布了<a href="https://play.google.com/store/apps/details?id=com.instagram.android">Instagram for Android</a>，反响空前强烈。
最近这几周我们一直（在基础设施方面）做负载计划，为所有可能发生的事故做准备。
尽管如此，在发布那天，最大的挑战仍然是如何快速定位问题，找到瓶颈并尽快修复他们。
在这里我们将分享一些帮助我们解决问题的工具和技术。</p>
<h2>statsd</h2>
<p><a href="http://github.com/etsy/statsd/">statsd</a>在 Instagram 很受欢迎。这是<a href="http://github.com/etsy/">Etsy</a>写的一个网络监控器，
能够统计并图形化数据。它有两种核心的统计方式：计数器和计时器。
我们用计数器来跟踪许多信息，从每秒钟注册数到喜欢数量统计。
同时我们使用计时器来跟踪 feeds 变化，用户停留在 feeds 多久，和其他的主要操作。</p>
<p>我们喜欢 statsd 的最大原因是它能迅速将状态显示并图形化出来。
Stats 几乎是实时的（在我们系统中有 10 秒钟的延时）
帮助我们评估系统状况和代码变化情况。
Stats 可以随意启用，当我们需要跟踪时，能够快速启用它。
你可以指定一个取样密度，来均匀的查看 web 应用概况，
而不会影响它运行效率。</p>
<p>总结：实时统计可以动态添加，帮助你线上诊断而无须等待问题重现。</p>
<h2>Dogslow</h2>
<p><a href="http://blog.bitbucket.org/2011/05/17/tracking-slow-requests-with-dogslow/">Bitbucket</a>出品，Dogslow 是一个 Django middleware，它能监视运行的进程，
当发现有进程运行时间超过N秒时，会保存当前进程信息的快照到磁盘。
一直开着它很耗性能，但当去定位突发问题的瓶颈时，就会非常有用（我们在 web
服务器上加了一个开关控制）。</p>
<p>在发布那天，我们发现很多运行时间超过 1.5s 的进程都卡在 memcached 的 set()
和 get_many() 方法上。我们使用 Munin 跟踪我们的机器状态，发现 memcached
每秒有 5 万次请求，因此，尽管还没到达 CPU 上限，却被 memcached
拖慢了应用服务器速度。</p>
<p>总结：瓶颈往往是后台的一个短板造成的，定位到底哪里卡下来能帮助找出问题根源。</p>
<h2>Replication &amp; Read-slaves</h2>
<p>我们主要的两个数据源<a href="http://redis.io">Redis</a>和<a href="http://postgresql.org/">PostgreSQL</a>都可以方便地主从分离。
当我们的 Redis 数据库到达每秒 4 万请求时候，开始成为瓶颈。我们在另外一台服务器
打开<a href="http://redis.io/topics/replication">SYNCing to the master</a>，然后将读请求发送到这台服务器，
这些操作仅耗时 20 分钟。根据几个案例，有些机器的负载会偏离了我们预期，
所以我们会提前准备好从服务器，从而快速地分离出读请求。</p>
<p>比方说 Postgres，我们混合使用<a href="http://wiki.postgresql.org/wiki/Streaming_Replication">Streaming Replication</a>和 Amazon EBS
快照来快速启动从服务器。所有从主 DB 到从服务器的数据不定时会有 EBS 快照。
根据这些快照，我们可以在 20 分钟内启动新的从服务器并通知主服务器。
在 AWS 这样的环境中，我们使用脚本和命令来很快的预判并部署新的从服务器。</p>
<p>总结：如果读能力会变成瓶颈，那就预先准备好从服务器，并让他们轮询服务。
一旦出现了新的读问题，要做的仅仅是加更多的读服务器到轮询中去。</p>
<h2>PGFouine</h2>
<p><a href="http://pgfouine.projects.postgresql.org/">PGFouine</a>是一个分析 PostgreSQL 查询日志并生成查询结果的工具。
将最重、最常用、最慢的查询拆分出来。我们写了一个 Fabric 脚本来方便的使用它，
连接到一个数据库，然后等待 30 秒并记录下其中每条查询，把结果保存到本地再用
pgfouine 来分析，这里可以看到<a href="https://gist.github.com/2307647">as a gist</a>。PGFouine
是我们用来分析数据库性能的核心工具。帮我们找出哪些查询应该被 memcached 缓存，
哪些查询了过度的数据等等。发布那天数据库出现了一点压力，我们就跑 PGFouine，
然后部署一些提升性能的代码来缓解压力，然后再跑一次 PGFouine 来确认效果。</p>
<p>知道平日里数据库表现性能很重要，因此我们也定期在无压力的服务器上统计信息。</p>
<p>总结：数据库日志分析（尤其注意迭代循环查询和缓存经常使用的数据）</p>
<h2>One more thing</h2>
<p>另一个帮助我们顺利通过发布第一天的工具是我们自己写的一个 node.js 服务，node2dm，
用来推送通知消息到 Android C2DM 服务。它至今已经发送了 500 万条推送。</p>
<p>我们调查了一些支持 C2DM 的可用服务，但要么是开源包不那么被积极维护，
要么是不支持 Google 所有的功能。我们现在用<a href="http://github.com/Instagram/node2dm">open sourcing node2dm</a>，
你可以随意 forlk / pull-request 来帮助我们改进。</p>
<h2>Interested?</h2>
<p>如果你对这些很感兴趣，你可以跟我们联系来获得工作机会，<a href="http://instagram.jobscore.com/jobs/instagram/engineer/bXctey0Oir4kCZeJe4bk1X">drop us a note</a>，
我们需要知道你的想法。</p>
<p>你可以在 Hacker News 来<a href="http://news.ycombinator.com/item?id=3804351">discuss this post</a>。</p>
<p>—</p>
<p>Mike Krieger, co-founder</p>
<div><p><strong>版权所有 © 2010 转载本站文章请注明：</strong>
转载自<a href="http://log4d.com/">Log4D</a>
<br>
<strong>原文链接:</strong>
<a href="http://log4d.com/2012/11/keeping-instagram-up-with-over-a-million-new-users-in-twelve-hours/">http://log4d.com/2012/11/keeping-instagram-up-with-over-a-million-new-users-in-twelve-hours/</a>
<br>
您可以随意地转载本站的文章，但是必须在醒目位置注明来源及本站链接，不可以将本站文章商业化使用，或者修改、转换或者以本作品为基础进行创作。
<br>
3a1ff193cee606bd1e2ea554a16353ee</p>
</div>
<img src="http://feeds.feedburner.com/~r/dddspace/~4/KxuHacWa2Gg">
			<div style="margin-top:8px;padding:6px 0;border-top:1px solid #3cf">
				<div style="text-align:center;margin:16px 0;padding:6px;border:0px dashed #999;font-family:arial;font-size:26px;font-weight:bold">
	<a href="http://www.udpwork.com/item/8698.html#review_form" title="不喜欢" style="text-decoration:none">
		<img src="http://www.udpwork.com//images/thumb_down24.gif" alt="">
		<span style="color:#f33">0</span>
	</a>
	   
	<a href="http://www.udpwork.com/item/8698.html#review_form" title="喜欢" style="text-decoration:none">
		<img src="http://www.udpwork.com//images/thumb_up24.gif" alt="">
		<span style="color:#3c3">0</span>
	</a>
</div>				<p>
					由 <a href="http://www.udpwork.com/">IT 牛人博客聚合网站(udpwork.com)</a> 聚合
					|
					<a href="http://www.udpwork.com/item/8698.html#reviews">评论: 0</a>
					|
					<a href="http://book.benegg.com/tag/%E7%BC%96%E7%A8%8B?from=udpwork-feed">10000+ 本编程/Linux PDF/CHM 电子书下载</a>
				</p>
			</div>