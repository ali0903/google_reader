---
layout: post
title:  "【外刊IT评论网】JavaScript6看上去很美"
date:   2011-12-07 00:51:26
author: Aqee
categories: program
---

## 【外刊IT评论网】JavaScript6看上去很美
### by Aqee
### at 2011-12-07 00:51:26
### original <http://www.aqee.net/ecmascript-6-looks-promising/>

本文是从 <a href="http://kishorelive.com/2011/11/22/ecmascript-6-looks-promising/">ECMAScript 6 looks promising</a> 这篇文章翻译而来。 <hr><p>JavaScript6让我很兴奋，这是我看了David Herman在YUIConf 2011大会上的<a href="http://www.yuiblog.com/blog/2011/11/21/video-yuiconf2011-dherma/">演讲</a>后的反应。我非常希望这些语言特征能尽快的在V8引擎上实现，这样我就可以在node.js里使用它们了。这些新增加的语言特征对于一个使用JavaScript的程序员来说，解决了很多以前使用起来很不方便的东西。尽管这个标准中2013年才能制定完成(大会上David这样说的)，谷歌浏览器和火狐浏览器很有可能会提前实现这些特征。<span></span></p>
<p>从我个人来说，下面的这些特征我很感兴趣：</p>
<p>
<h3><code>let</code> 关键字</h3>
</p>
<p><code>let</code> 关键字能产生一个代码块内的变量范围。因为 <code>var</code> 有一个函数式的范围概念，这有时候容易产生出不注意的错误。以后，我们完全使用 <code>let</code>来代替<code>var</code>，从而避免这样的错误。</p>
<p>
<h3>缺省参数</h3>
</p>
<div>
<div>
<pre style="font-family:monospace"><span style="color:rgb(0,51,102);font-weight:bold">function</span> foo<span style="color:rgb(0,153,0)">(</span>bar<span style="color:rgb(51,153,51)">=</span><span style="color:rgb(51,102,204)">"baz"</span><span style="color:rgb(0,153,0)">)</span> <span style="color:rgb(0,153,0)">{</span>
   console.<span style="color:rgb(102,0,102)">log</span><span style="color:rgb(0,153,0)">(</span>bar<span style="color:rgb(0,153,0)">)</span><span style="color:rgb(51,153,51)">;</span>

<span style="color:rgb(0,153,0)">}</span></pre>
</div>
</div>
<p>有了缺省参数，省去了很多折腾的代码。</p>
<p>
<h3>非严格的解构方式</h3>
</p>
<p>很像Python语言，解构时不需要严格匹配。</p>
<div>
<div>
<pre style="font-family:monospace">let <span style="color:rgb(0,153,0)">[</span>x<span style="color:rgb(51,153,51)">,</span>y<span style="color:rgb(0,153,0)">]</span> <span style="color:rgb(51,153,51)">=</span> <span style="color:rgb(0,153,0)">[</span><span style="color:rgb(204,0,0)">3</span><span style="color:rgb(51,153,51)">,</span><span style="color:rgb(204,0,0)">4</span><span style="color:rgb(51,153,51)">,</span><span style="color:rgb(204,0,0)">5</span><span style="color:rgb(0,153,0)">]</span><span style="color:rgb(51,153,51)">;</span>   <span style="color:rgb(0,102,0);font-style:italic">// x=3, y = 4</span></pre>
</div>
</div>
<p>
<h3>多行模式的字符串表达</h3>
<p>
  你可以简单的使用 <code>`</code> (反点号)操作符来声明一个多行字符串。</p>
<div>
<div>
<pre style="font-family:monospace"><span style="color:rgb(0,51,102);font-weight:bold">var</span> htmlString <span style="color:rgb(51,153,51)">=</span> `Say hello to
multi<span style="color:rgb(51,153,51)">-</span>line
strings<span style="color:rgb(51,153,51)">!</span>`</pre>
</div></div></p>

<p>
<h3>模板化</h3>
<p>
  你可以通过这种方式把JavaScript变量嵌入的字符串中：</p>
<div>
<div>
<pre style="font-family:monospace"><span style="color:rgb(0,51,102);font-weight:bold">var</span> firstName <span style="color:rgb(51,153,51)">=</span> <span style="color:rgb(51,102,204)">"Jack"</span><span style="color:rgb(51,153,51)">;</span>
<span style="color:rgb(0,51,102);font-weight:bold">var</span> message <span style="color:rgb(51,153,51)">=</span> `Hello $<span style="color:rgb(0,153,0)">{</span>firstName<span style="color:rgb(0,153,0)">}</span><span style="color:rgb(51,153,51)">!</span>`<span style="color:rgb(51,153,51)">;</span> <span style="color:rgb(0,102,0);font-style:italic">// "Hello Jack!"</span></pre>
</div></div></p>

<p>
<h3>List 复合操作</h3>
</p>
<p>同样，这也是一个非常Python的结构：</p>
<div>
<div>
<pre style="font-family:monospace">let even <span style="color:rgb(51,153,51)">=</span> <span style="color:rgb(0,153,0)">[</span> x <span style="color:rgb(0,0,102);font-weight:bold">for</span> <span style="color:rgb(0,153,0)">(</span>x <span style="color:rgb(0,0,102);font-weight:bold">in</span> values<span style="color:rgb(0,153,0)">(</span><span style="color:rgb(0,153,0)">[</span><span style="color:rgb(204,0,0)">1</span><span style="color:rgb(51,153,51)">,</span><span style="color:rgb(204,0,0)">2</span><span style="color:rgb(51,153,51)">,</span><span style="color:rgb(204,0,0)">3</span><span style="color:rgb(51,153,51)">,</span><span style="color:rgb(204,0,0)">4</span><span style="color:rgb(51,153,51)">,</span><span style="color:rgb(204,0,0)">5</span><span style="color:rgb(51,153,51)">,</span><span style="color:rgb(204,0,0)">6</span><span style="color:rgb(0,153,0)">]</span><span style="color:rgb(0,153,0)">)</span><span style="color:rgb(0,153,0)">)</span> <span style="color:rgb(0,0,102);font-weight:bold">if</span> <span style="color:rgb(0,153,0)">(</span>x <span style="color:rgb(51,153,51)">%</span>2 <span style="color:rgb(51,153,51)">===</span> <span style="color:rgb(204,0,0)">0</span><span style="color:rgb(0,153,0)">)</span> <span style="color:rgb(0,153,0)">]</span><span style="color:rgb(51,153,51)">;</span></pre>
</div>
</div>
<p>使用 <code>values()</code> 能够让 <code>x</code> 来表示List里的成员的值，而不是成员变量。你也可以使用新的 <code>for of</code> 语法来实现这样的功能：</p>
<div>
<div>
<pre style="font-family:monospace">let even <span style="color:rgb(51,153,51)">=</span> <span style="color:rgb(0,153,0)">[</span> x <span style="color:rgb(0,0,102);font-weight:bold">for</span><span style="color:rgb(0,153,0)">(</span>x of <span style="color:rgb(0,153,0)">[</span><span style="color:rgb(204,0,0)">1</span><span style="color:rgb(51,153,51)">,</span><span style="color:rgb(204,0,0)">2</span><span style="color:rgb(51,153,51)">,</span><span style="color:rgb(204,0,0)">3</span><span style="color:rgb(51,153,51)">,</span><span style="color:rgb(204,0,0)">4</span><span style="color:rgb(51,153,51)">,</span><span style="color:rgb(204,0,0)">5</span><span style="color:rgb(51,153,51)">,</span><span style="color:rgb(204,0,0)">6</span><span style="color:rgb(0,153,0)">]</span><span style="color:rgb(0,153,0)">)</span> <span style="color:rgb(0,0,102);font-weight:bold">if</span> <span style="color:rgb(0,153,0)">(</span>x<span style="color:rgb(51,153,51)">%</span>2 <span style="color:rgb(51,153,51)">===</span> <span style="color:rgb(204,0,0)">0</span><span style="color:rgb(0,153,0)">)</span> <span style="color:rgb(0,153,0)">]</span><span style="color:rgb(51,153,51)">;</span></pre>
</div>
</div>
<p>除此之外， <code>map</code>, <code>filter</code>, <code>reduce</code> 等等概念也将会成为新标准的<a href="https://developer.mozilla.org/Special:Tags?tag=ECMAScript5">内容之一</a> (有一些功能已经在谷歌浏览器和火狐浏览器里被实现，<a href="http://msdn.microsoft.com/en-us/library/ff679973%28v=vs.94%29.aspx">IE9</a>也有不少这方面的改进)。</p>
<p><strong>更新：</strong></p>
<p>发现这个帖子被顶到了HN的首页，我必须要加上遗漏的新标准中的模块系统操作功能。演讲中的例子：</p>
<div>
<div>
<pre style="font-family:monospace"><span style="color:rgb(0,51,102);font-weight:bold">import</span> <span style="color:rgb(0,153,0)">{</span> $ <span style="color:rgb(0,153,0)">}</span> from <span style="color:rgb(51,102,204)">"jquery.js"</span>
<span style="color:rgb(0,51,102);font-weight:bold">import</span> <span style="color:rgb(0,153,0)">{</span> map<span style="color:rgb(51,153,51)">,</span> each <span style="color:rgb(0,153,0)">}</span> from <span style="color:rgb(51,102,204)">"underscore.js"</span></pre>
</div>
</div>
<p>还是一样，全是Python的套路！被引用的JavaScript文件会代码执行前加载的你的浏览器中。</p>
<hr>本文来自<a href="http://www.aqee.net">外刊IT评论网</a>(<a href="http://www.aqee.net">www.aqee.net</a>)，原始地址：<a href="http://www.aqee.net/ecmascript-6-looks-promising/" rel="bookmark">JavaScript6看上去很美</a><br><img src="http://www1.feedsky.com/t1/584781130/aqee-net/feedsky/s.gif?r=http://www.aqee.net/ecmascript-6-looks-promising/" border="0" height="0" width="0">