---
layout: post
title:  "NHibernate 3.0.0.GA正式发布了"
date:   2010-12-05 13:27:03
author: 
categories: program
---

## NHibernate 3.0.0.GA正式发布了
### by 
### at 2010-12-05 13:27:03
### original <http://news.cnblogs.com/n/83509/>

<h1>　　发布</h1>
<p>　　2010年12月4号Fabio Maulo发布了NHibernate 3.0.0.GA正式版本。</p>
<h1>　　下载地址</h1>
<p>　　你可以到<a href="http://sourceforge.net/projects/nhibernate/files/">这里</a>下载NHibernate 3.0.0.GA，基于.Net3.5平台，具体文件如下。 </p>
<ul>
<li>NHibernate二进制文件：NHibernate-3.0.0.GA-bin.zip</li>
<li>NHibernate参考文档：NHibernate-3.0.0.GA-reference.zip</li>
<li>NHibernate源码：NHibernate-3.0.0.GA-src.zip</li>
</ul>
<h1>　　特性介绍</h1>
<p>　　NHibernate 3.0.0版本历经Alpha1、Alpha2、Alpha3、Beta1、Beta2、RC版本，修复了200多个BUG，增加了很多新特性。下面我自己归类总结一下这些新特性吧。</p>
<h2>　　程序集部分</h2>
<p>　　重写了日志模块，NHibernate 3.0.0.GA无需依赖log4net.dll程序集了。</p>
<p>　　Castle的bytecode-providers：使用2010年11月15号发布的2.5.2正式版本，即Castle.Core.dll程序集。</p>
<h2>　　Configuration部分</h2>
<p>　　新增两种方式<strong>流配置(fluent-configuration)和lambda表达式配置(lambda-configuration)</strong>配置SessionFactory。</p>
<p>　　新增对WCF的支持，提供<strong>WcfOperationSessionContext</strong>，我们可以直接配置"current_session_context_class"为"wcf_operation"，直接在WCF中应用NHibernate。</p>
<p>　　在Configuration类中新增了<strong>AddDeserializedMapping</strong>(HbmMapping mappingDocument, string documentFileName)方法，可以直接解析HbmMapping，我们可以选择不使用xml文件Mapping。</p>
<h2>　　Mapping部分</h2>
<p>　　Fabio Maulo完全重构了NHibernate.Cfg.MappingSchema部分，提高了性能，另外Fabio Maulo设计了一个配置ORM工具——<a href="http://code.google.com/p/codeconform/">ConfOrm</a>，解析Domain结构，按照模式适配器转换成HbmMapping类。当然了，<a href="http://code.google.com/p/codeconform/">ConfOrm</a>不是NHibernate的一部分，我们需要另外单独下载。我之前写一些文章介绍了这个工具，参考下面分享。</p>
<h2>　　Query部分</h2>
<p>　　在NHibernate 3.0.0版本中，新增了<strong>Linq Provider和strongly typed query API (QueryOver)</strong>两种查询方式。</p>
<p><strong>　　QueryOver</strong>是Lambda表达式方式的条件(Criteria)查询，可读性强且支持property-renaming特性。</p>
<p><strong>　　Linq</strong>是由Steve Strong贡献者开发的，完全基于AST的Linq Provider实现。</p>
<h2>　　其他</h2>
<p>　　除了上面可以归类的新特性，还提供了很多新特性，我先来列举一下：</p>
<p><strong>　　属性延迟加载和No Proxy关联</strong>特性：它仅支持Castle的bytecode-providers，不支持Spring.Net和LinFu。</p>
<p>　　内置hbm2ddl工具。</p>
<p>　　还有其他特性不一一列举。以后博文介绍。</p>
<h1>　　信息分享</h1>
<h2>　　博文和站点</h2>
<p>　　<a href="http://nhforge.org/">NHibernate官方站点</a></p>
<p>　　<a href="http://nhforge.org/doc/nh/en/">NHibernate在线参考文档</a>已经更新至NHibernate 3.0.0版本，具体修改了一些错误和增加了QueryOver章节。</p>
<p>　　<a href="http://kb.cnblogs.com/zt/nhibernate/">博客园NHibernate专题</a></p>
<p>　　<a href="http://www.cnblogs.com/lyj/tag/NHibernate/">NHibernate3剖析系列文章</a></p>
<p>　　<a href="http://www.cnblogs.com/lyj/tag/ConfORM/">ConfORM实战系列文章</a></p>
<p>　　<a href="http://www.cnblogs.com/lyj/archive/2010/10/11/nhibernate-3-0-cookbook.html">NHibernate新书：NHibernate 3.0 CookBook</a></p>
<h2>　　工具</h2>
<p>　　<a href="http://hqladdin.codeplex.com/">HQL Language Service for Visual Studio</a>：为我们编写HQL查询提供了智能提示和语法验证检查功能。</p>
<p>　　<a href="http://nhprof.com/">NHibernate Profile</a>：可视化NHibernate性能检测优化工具。</p>
<p>　　<a href="http://www.slyce.com/">Visual NHibernate</a>：可视化设计和映射工具。</p>
<p>　　<a href="http://www.mindscape.co.nz/products/nhdesigner">Mindscape NHibernate Designer</a>：VS设计工具，类似于EF设计器。</p>
<h2>　　未来</h2>
<p>　　据我了解的情况来看，将来的版本发布预计一两个月后会发布3.0.1版本，然后就升级至.Net4平台开发NHibernate 
4版本，主要就是逐步改写内部代码去掉Iesi.Collections.dll，使用.Net4提供的ISet集合和添加一些新功能，比如<a href="http://fabiomaulo.blogspot.com/2010/06/duck-typing-with-nhibernate-reloaded.html">Duck 
Typing</a>等。</p>
<p>　　还有NHibernate源码将迁移到CodePlex网站，使用Hg版本控制。</p><p>　　本文链接：<a href="http://news.cnblogs.com/n/83509/">http://news.cnblogs.com/n/83509/</a></p><p>　　<a href="http://job.cnblogs.com">程序员找工作，就在博客园</a></p><p>　　<a href="http://a4.yeshj.com/rd/34138/">每天10分钟，轻松学外语</a></p><img src="http://news.cnblogs.com/news/rssclick.aspx?id=83509" width="1" height="1" alt="">