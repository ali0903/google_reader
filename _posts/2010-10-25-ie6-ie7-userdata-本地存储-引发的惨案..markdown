---
layout: post
title:  "ie6 ie7 userdata 本地存储 引发的惨案."
date:   2010-10-25 18:38:00
author: Franky
categories: program
---

## ie6 ie7 userdata 本地存储 引发的惨案.
### by Franky
### at 2010-10-25 18:38:00
### original <http://www.cnblogs.com/_franky/archive/2010/10/25/1860649.html>

<p><p>我使用 documentElement 作为userdata 作为本地存储的载体. </p>
<p><em>document.documentElement.addBehavior("#default#userdata");</em></p>
<p>悲剧就产生了.</p>
<p> </p>
<p>当使用mootools 的 选择器方法 时.  如果很悲剧的 使用了不恰当的选择符.比如</p>
<p>$$(&#39;[attr=123]&#39;); </p>
<p>就报错了. 为什么呢？</p>
<p>查看 mootools 源码 发现 他的选择器在遍历所有节点时 简单的使用了下面的逻辑 :</p>
<p> </p>
<p> </p>
<div>
<pre>getProperty: function(attribute){
		var key = attributes[attribute];
		var value = (key) ? this[key] : this.getAttribute(attribute, 2);
		return (bools[attribute]) ? !!value : (key) ? value : value || null;
}
</pre>
</div>
<p> </p>
<p> </p>
<p>问题就出在 this.getAttribute(attribute,<span style="color:#ff0000">2</span>)  这个参数2上. </p>
<p> </p>
<p> </p>
<p> 因为我使用documentElement作为 userdata载体节点. 则在</p>
<p><em>document.documentElement.addBehavior("#default#userdata");</em></p>
<p>语句执行后,ie会重写 该节点的getAttribute方法 . 而这个新重写的方法.只支持一个参数.而这个方法大概是c++ 语言实现的.不支持动态参数. 那么你传递了第二个参数 就会导致异常.</p>
<p> </p>
<p>当this 指的是document.documentElement 即 HTML节点时.  则 明显 mootools 上面的代码就会执行到这个被重写过的方法.并传入第二参数.</p>
<p> </p>
<p>好吧. 到了这里 你可能会问我. 为什么你非要用DocumentElement作为载体呢？ 选个其他节点不就ok了？  遗憾的是 .上面的选择器方法调用时.要遍历 所有节点... 那么 我用哪个节点 可以逃脱这个魔咒呢？. (如果这个节点不在dom树上.那么addBehavior将会报无权限.).</p>
<p> </p>
<p>那么 出现这种悲剧 到底是谁的过错呢？ 我想 即不是 那个写下 $$(&#39;[attr=123]&#39;);的人 ,也不是 使用了userdata的我 . 而是万恶的 微软. 你既然给你节点的 getattribute原始方法 设计了 第二参数..那么你,那么你重写他的时候.为什么又去掉了第二参数呢？ 更可悲的是,你还是不给他容错.</p>
<p>好吧.现在可好了..要么我让人家别用mootools 要么我不用userdata。 要么我用一个复杂的解决办法 修改userdata的操作方式.要么人家要修改 mootools 源码，或选择器器的选择符.  </p>
<p> </p>
<p>说些题外话. jquery 就考虑到了这个问题. </p>
<p>人家就做了判断 动态 切换  选择使用参数2 或不适用.</p>
<p> </p>
<p>最后补充下getAttribute方法,第二参数 值为2 的作用.</p>
<p> </p>
<p>对于一个 a 标签.  当我们使用getAttribute方法 获取他的 href属性时. 第二参数就起到关键作用了:</p>
<p>如下表:</p>
<table border="0">
<tbody>
<tr>
<th colspan="4">获得href和src属性的浏览器差异（相对地址与自动编码情况)<br></th>
</tr>
<tr>
<td width="20%"> </td>
<td width="25%"><strong>.href</strong></td>
<td width="25%"><strong> getAttribute(“href”)</strong></td>
<td width="30%"><strong>getAttribute(“href”, 2)</strong></td>
</tr>
<tr>
<td><strong>IE6</strong></td>
<td>相对转绝对，不编码</td>
<td> 相对转绝对，不编码</td>
<td> 原始值</td>
</tr>
<tr>
<td><strong>IE7</strong></td>
<td>相对转绝对，汉字不编码，特殊符号编码</td>
<td> 相对转绝对，汉字不编码，特殊符号编码</td>
<td> 原始值</td>
</tr>
<tr>
<td><strong>IE8 as IE7</strong></td>
<td>相对转绝对，汉字不编码，特殊符号编码</td>
<td> 相对转绝对，汉字不编码，特殊符号编码</td>
<td> 原始值</td>
</tr>
<tr>
<td><strong>IE8 standard</strong></td>
<td>相对转绝对，汉字不编码，特殊符号编码</td>
<td> 原始值</td>
<td> 原始值</td>
</tr>
<tr>
<td><strong>Firefox 3.0+</strong></td>
<td>相对转绝对，全部编码</td>
<td> 原始值</td>
<td> 原始值</td>
</tr>
<tr>
<td><strong>Chrome 2.0+</strong></td>
<td>相对转绝对，全部编码</td>
<td> 原始值</td>
<td> 原始值</td>
</tr>
<tr>
<td><strong>Safari 4.0+</strong></td>
<td>相对转绝对，汉字编码，特殊符号不编码</td>
<td> 原始值</td>
<td> 原始值</td>
</tr>
<tr>
<td><strong>Opera 9.6+</strong></td>
<td>相对转绝对，汉字编码，特殊符号不编码</td>
<td> 原始值</td>
<td> 原始值</td>
</tr>
</tbody>
</table><img src="http://www.cnblogs.com/_franky/aggbug/1860649.html?type=1" width="1" height="1" alt=""><p>作者: <a href="http://www.cnblogs.com/_franky/">Franky</a> 发表于 2010-10-25 18:38 <a href="http://www.cnblogs.com/_franky/archive/2010/10/25/1860649.html">原文链接</a></p><p>评论: 4　<a href="http://www.cnblogs.com/_franky/archive/2010/10/25/1860649.html#pagedcomment">查看评论</a>　<a href="http://www.cnblogs.com/_franky/archive/2010/10/25/1860649.html#commentform">发表评论</a></p><hr><p>最新新闻：<br>· <a href="http://news.cnblogs.com/n/78672/">你和你的工作</a><span style="color:gray">(2010-10-26 16:25)</span><br>· <a href="http://news.cnblogs.com/n/78671/">京东商城开放平台年底前正式上线</a><span style="color:gray">(2010-10-26 16:01)</span><br>· <a href="http://news.cnblogs.com/n/78670/">郭台铭：供应链拯救世界</a><span style="color:gray">(2010-10-26 15:36)</span><br>· <a href="http://news.cnblogs.com/n/78667/">一个人到底需要多大的存储量</a><span style="color:gray">(2010-10-26 15:19)</span><br>· <a href="http://news.cnblogs.com/n/78664/">周鸿祎：互联网公司衰落的六大共性</a><span style="color:gray">(2010-10-26 15:04)</span><br></p><p>编辑推荐：<a href="http://news.cnblogs.com/n/78582/">开发人员需要知道如何做，做什么，和为什么做</a><br></p><p>网站导航：<a href="http://www.cnblogs.com">博客园首页</a>  <a href="http://home.cnblogs.com/">我的园子</a>  <a href="http://news.cnblogs.com">新闻</a>  <a href="http://home.cnblogs.com/ing/">闪存</a>  <a href="http://home.cnblogs.com/group/">小组</a>  <a href="http://space.cnblogs.com/q/">博问</a>  <a href="http://kb.cnblogs.com">知识库</a></p></p>