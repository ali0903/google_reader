---
layout: post
title:  "IronRuby － 快速在半小时学习Ruby基础知识"
date:   2010-06-24 11:23:00
author: 周 金根
categories: program
---

## IronRuby － 快速在半小时学习Ruby基础知识
### by 周 金根
### at 2010-06-24 11:23:00
### original <http://www.cnblogs.com/zhoujg/archive/2010/06/24/1762981.html>

<p><a href="http://www.cnblogs.com/zhoujg/"><img src="http://pic.cnblogs.com/face/u14032.jpg" alt="" border="0"></a><br>作者: <a href="http://www.cnblogs.com/zhoujg/">周 金根</a> 发表于 2010-06-24 11:23 <a href="http://www.cnblogs.com/zhoujg/archive/2010/06/24/1762981.html">原文链接</a> 阅读: 847 评论: 6</p><p>　　在前面几篇blog中我介绍了一些IronRuby相关的内容，由于IronRuby是Ruby在.Net的一种实现而已，所以其基本语法和使用都类似Ruby，那么要想学会使用IronRuby，我们首先就要先快速了解如何使用Ruby，本篇主要介绍一下Ruby的一些基础知识，这也是我这几天主要用到的东西，希望对初学者有所帮助。</p>
<h3>Variables</h3>
<p>定义变量很简单，只要写上一个小写的编码名，后面跟上等号和值就行了，如</p>
<div style="padding:0px;margin:0px;display:inline;float:none">
<pre>str = "你好"<br>num = 1 <br>arr = [1, 2, 3] </pre>
</div>
<h3>Conditions</h3>
<div style="padding:0px;margin:0px;display:inline;float:none">
<ul>
<li>if XXX then  XXX  elseif XXX then  XXX  end</li>
<li>title = "登录" if (title == "")</li>
<li>unless (password == &quot;&quot;) the  XXX  end</li>
</ul>
</div>
<h3>Loops</h3>
<ul>
<li>while XXX do XXX  end</li>
<li>until XXX do  XXX  end</li>
<li>for item in XXX do  XXX  end</li>
<li>10.times do |i|  print i end</li>
<li>a.each do |item|  puts item  end</li>
</ul>
<h3>Error Handling</h3>
<div style="padding:0px;margin:0px;display:inline;float:none">
<pre>begin<br>  # something risky<br>rescue SomeException <br>  XXX<br>rescue Exception<br>  XXX<br>rescue<br>  XXX <br>end</pre>
</div>
<h3>Methods</h3>
<div style="padding:0px;margin:0px;display:inline;float:none">
<pre><pre>def add(a, b)<br>  a + b<br>end</pre>
<br><br></pre>
</div>
<ul>
<li>方法返回值可以通过return XXX，或者直接在方法最后写上需要返回的值</li>
<li>result1 = add(1, 2)   或者不加括号   result2 = add 1, 2</li>
</ul>
<h3>Classes</h3>
<div style="padding:0px;margin:0px;display:inline;float:none">
<pre>class TestApplication<br>  def initialize  #构造函数<br>    self.runing = false  #给属性赋值<br>  end<br><p>  attr_accessor :runing  #属性  attr_reader  attr_writer<br><br>  @@instance = nil  #类变量<br>  <i>@</i>instance_variable = 0  #实例变量<br><br>  def self.instance<br>    @@instance = self.new if @@instance == nil<br>    @@instance  #函数返回单例<br>  end</p><br> #实例方法<br>  def run<br><p>     return if self.runing<br>     XXX<br>     self.runing = true<br>  end</p><pre>#类方法<br>def self.say_hello<br>   puts "Hello"<br>end</pre>
<br><br><br>
<p>end</p>
<br></pre>
</div>
<ul>
<li>生成类 TestApplication.new</li>
<li>使用<b>super</b>调用父类方法，如果参数一样，则可以不传人参数</li>
</ul>
<h3>Using Code from Other Files</h3>
<div style="padding:0px;margin:0px;display:inline;float:none">
<ul>
<li>
<pre>$LOAD_PATH &lt;&lt; &#39;D:/GZJ/OpenExpressApp/Tool/OpenTest/dll&#39;<br>require "Microsoft.VisualStudio.TestTools.UITesting.dll"</pre>
</li>
<li>  require &quot;Utils/find_control_helper.rb&quot;</li>
</ul>
</div>
<p> </p>
<p><b>欢迎转载，转载请注明：转载自</b><b><a href="http://zhoujg.cnblogs.com/">周金根</a> [ <a href="http://zhoujg.cnblogs.com/">http://zhoujg.cnblogs.com/</a> ]</b></p><img src="http://www.cnblogs.com/zhoujg/aggbug/1762981.html?type=1" width="1" height="1" alt=""><p>评论: 6　<a href="http://www.cnblogs.com/zhoujg/archive/2010/06/24/1762981.html#pagedcomment">查看评论</a>　<a href="http://www.cnblogs.com/zhoujg/archive/2010/06/24/1762981.html#commentform">发表评论</a></p><p><a href="http://a4.yeshj.com/rd/35451/">软件研发团队管理年会(上海，7.10-7.11)</a></p><hr><p>最新新闻：<br>· <a href="http://news.cnblogs.com/n/66924/">18 个在线地图服务商已经拿到良民证，还不包括百度和谷歌</a><span style="color:gray">(2010-06-24 20:22)</span><br>· <a href="http://news.cnblogs.com/n/66923/">Opera成立15周年</a><span style="color:gray">(2010-06-24 20:21)</span><br>· <a href="http://news.cnblogs.com/n/66922/">Ustream CEO：不惧YouTube推出视频直播服务</a><span style="color:gray">(2010-06-24 20:05)</span><br>· <a href="http://news.cnblogs.com/n/66921/">微软看好未来几年在线广告市场</a><span style="color:gray">(2010-06-24 20:03)</span><br>· <a href="http://news.cnblogs.com/n/66920/">第一批用户反馈 iPhone 4 天线设计可能有缺陷</a><span style="color:gray">(2010-06-24 19:54)</span><br></p><p>编辑推荐：<a href="http://www.cnblogs.com/firelong/archive/2010/06/24/1764597.html">C#会重蹈覆辙吗？反射及元数据的性能问题</a><br></p><p>网站导航：<a href="http://www.cnblogs.com">博客园首页</a>  <a href="http://home.cnblogs.com/">个人主页</a>  <a href="http://news.cnblogs.com">新闻</a>  <a href="http://home.cnblogs.com/ing/">闪存</a>  <a href="http://home.cnblogs.com/group/">小组</a>  <a href="http://space.cnblogs.com/q/">博问</a>  <a href="http://space.cnblogs.com">社区</a>  <a href="http://kb.cnblogs.com">知识库</a></p>