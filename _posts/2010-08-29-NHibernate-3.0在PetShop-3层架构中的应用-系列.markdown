---
layout: post
title:  "NHibernate 3.0在PetShop 3层架构中的应用 系列"
date:   2010-08-29 23:51:00
author: liuxiaojun
categories: program
---

## NHibernate 3.0在PetShop 3层架构中的应用 系列
### by liuxiaojun
### at 2010-08-29 23:51:00
### original <http://www.cnblogs.com/liuxiaojun/archive/2010/08/29/example_nhibernate_in_petshop_summary.html>

<p>作者: <a href="http://www.cnblogs.com/liuxiaojun/">liuxiaojun</a> 发表于 2010-08-29 23:51 <a href="http://www.cnblogs.com/liuxiaojun/archive/2010/08/29/example_nhibernate_in_petshop_summary.html">原文链接</a> 阅读: 1280 评论: 8</p><p>PetShop 及 NHibernate 对于.Net平台下的开发者一定很熟悉，前者是一个经典的软件开发架构，后者是是一个强大的ORM框架。</p>
<p>近期有一个项目，部分功能需要由原来的SQL Server数据改为Oracle数据，其中原来的DAL层用的是LINQ To SQL方式实现的，而微软LINQ并没有提供到Oracle的映射功能（在这里鄙视一下MS），如果要改成直接用ADO.NET来实现DAL层，无疑工作量是巨大的。通过综合考量决定使用其他ORM框架对项目进行改造，比较了一下常用的开源ORM框架，因为NHibernate最近推出了其3.0版本，里面集成了LINQ功能， 所以最后决定采用NHibernate对项目进行改造。</p>
<p> </p>
<p> 本文依据此次项目经历，整理出一个Demo，来体验一下NHibernate在基于PetShop多层架构开发中的应用。</p>
<p> </p>
<ul><li><a href="http://www.cnblogs.com/liuxiaojun/articles/example_nhibernate_in_petshop.html">Demo详解</a></li><li><a href="http://files.cnblogs.com/liuxiaojun/LXJ.NHibernate.Demo.rar">Demo源代码下载</a></li></ul>  <img src="http://www.cnblogs.com/liuxiaojun/aggbug/1812147.html?type=1" width="1" height="1" alt=""><p>评论: 8　<a href="http://www.cnblogs.com/liuxiaojun/archive/2010/08/29/example_nhibernate_in_petshop_summary.html#pagedcomment">查看评论</a>　<a href="http://www.cnblogs.com/liuxiaojun/archive/2010/08/29/example_nhibernate_in_petshop_summary.html#commentform">发表评论</a></p><p><a href="http://job.cnblogs.com/">程序员找工作，就在博客园</a></p><hr><p>最新新闻：<br>· <a href="http://news.cnblogs.com/n/72436/">让人震惊的10个非技术人员无法理解的软件概念</a><span style="color:gray">(2010-08-30 15:47)</span><br>· <a href="http://news.cnblogs.com/n/72435/">微软要求美国最高法院重审Word侵权案</a><span style="color:gray">(2010-08-30 15:44)</span><br>· <a href="http://news.cnblogs.com/n/72434/">戴志康诉说两小时收购 畅谈康盛创想的明天</a><span style="color:gray">(2010-08-30 15:41)</span><br>· <a href="http://news.cnblogs.com/n/72432/">iPhone 4也来双SIM卡</a><span style="color:gray">(2010-08-30 15:11)</span><br>· <a href="http://news.cnblogs.com/n/72431/">微软网络业务亏损60亿美元 霸主地位遭挑战</a><span style="color:gray">(2010-08-30 15:08)</span><br></p><p>编辑推荐：<a href="http://www.cnblogs.com/cmt/archive/2010/08/30/1812291.html">上周热点回顾（8.23-8.29） </a><br></p><p>网站导航：<a href="http://www.cnblogs.com">博客园首页</a>  <a href="http://home.cnblogs.com/">个人主页</a>  <a href="http://news.cnblogs.com">新闻</a>  <a href="http://home.cnblogs.com/ing/">闪存</a>  <a href="http://home.cnblogs.com/group/">小组</a>  <a href="http://space.cnblogs.com/q/">博问</a>  <a href="http://space.cnblogs.com">社区</a>  <a href="http://kb.cnblogs.com">知识库</a></p>