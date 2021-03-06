---
layout: post
title:  "淘宝详情页的 BigRender 优化与存放大块 HTML 内容的最佳方式"
date:   2011-09-23 23:56:16
author: lifesinger
categories: program
---

## 淘宝详情页的 BigRender 优化与存放大块 HTML 内容的最佳方式
### by lifesinger
### at 2011-09-23 23:56:16
### original <http://lifesinger.wordpress.com/2011/09/23/bigrender-for-taobao-item/>

<p>对于复杂页面，为了将用户关注的内容尽可能快渲染出来，至少有两种方式：</p>
<p><strong>一、Facebook 的 BigPipe 方式。</strong>先输出页面整体布局，然后逐步输出脚本块，一边输出一边执行，将内容渲染回页面布局中。这样可以让服务端的运算、网络传输和浏览器端的渲染变成并行。BigPipe 最主要解决的问题是服务端的运算时间，当服务端的运算时间大于 300 ~ 500ms 时才能体现出优势。当服务端响应非常快（小于 100ms），BigPipe 退化为下面要讲的 BigRender.</p>
<p><strong>二、淘宝商品详情页的 BigRender 方式。</strong>淘宝的商品详情页，服务端平均响应时间为 52ms, 采用 BigPipe chunked 输出意义不大。这次优化主要在浏览器端。页面下载完毕后，要经过 Tokenization — Tree Construction — Rendering. 要让首屏尽快出来，得给浏览器减轻渲染首屏的工作量。可以从两方面入手：</p>
<ol>
<li><strong>减少 DOM 节点数。</strong>节点数越少，意味着 Tokenization, Rendering 等操作耗费的时间越少。（对于典型的淘宝商品详情页，经测试发现，每增加一个 DOM 节点，会导致首屏渲染时间延迟约 0.5ms.）</li>
<li><strong>减少脚本执行时间。</strong>脚本执行和 UI Update 共享一个 thread, 脚本耗的时间越少，UI Update 就能越发提前。</li>
</ol>
<h2>减少首屏 DOM 节点数</h2>
<p>对于 BigPipe 来说，初始输出的只有页面布局，DOM 节点数不多。首屏的 DOM 节点数主要取决于首屏脚本块中，字符串化的 html 代码：</p>
<pre>
big_pipe.onPageletArrive({ &quot;content&quot;: { /* data */ } })
</pre>
<p>这种方式下，页面中的 DOM 节点是逐步增加的。尚未渲染的 DOM 节点，不会影响 TTI 区域。</p>
<p>对于 BigRender 来说，减少 DOM 节点数的方式有：</p>
<ol>
<li>和 Facebook 的 BigPipe 一样，调整页面代码为 页面布局 + 脚本块。BigPipe 是服务器 chunked 输出 html 内容，BigRender 是服务器一次性输出，其他都是一样的。</li>
<li>尽量少调整页面代码，但通过某种方式，将首屏不需要的 html 代码先存放起来。渲染好首屏后，再将存储好的 html 代码逐步渲染出来。</li>
</ol>
<h2>用 js 字符串来存放 html 代码</h2>
<p>最容易想到的一种方式是学习 Facebook 好榜样，用 js 字符串来存放：</p>
<pre>
&lt;script&gt;
var data = &quot;&lt;p&gt;some data&lt;/p&gt;...&quot;;
&lt;/script&gt;
</pre>
<p><span></span><br>
这种方式对于 BigRender 来说，并不是很好：</p>
<ol>
<li>由于存放在 js 字符串变量中，需要对双引号或单引号转义。</li>
<li>由于 script 是内嵌的，需要对 script <a href="http://mathiasbynens.be/notes/etago">ETAGO</a> 转义。</li>
<li>服务器端需要将 html 代码转化为一行。（也可以不转成一行，用续行符来做。）</li>
<li>当 html 代码中含有 script 时，需要先去除 script 中的单行注释，否则转化成一行时，会出问题。这一步，看似简单，实际上很不容易，特别是对于淘宝旺铺这种有第三方代码的情况。（移除注释的方法可以参考：<a href="http://lifesinger.github.com/lab/2011/remove-comments-safely/">Simple but Safe Comment Removal</a>, 使用正则的方式很难做到 0 bug, 不用正则的话，需要引入 html parser 和 javascript parser, 效率更低。）</li>
</ol>
<p>把代码规范做好，把校验工作做好，再加上预处理和缓存，js 字符串的方式也是非常不错的。但对于淘宝详情页来说，目前用 js 字符串的方式需要做的改动比较多，增加的服务器消耗不少，不是很合适。</p>
<p>我们这次优化的目标是：</p>
<ol>
<li>大幅度减少首屏渲染时间。</li>
<li>尽量不改变原有开发习惯。</li>
<li>用尽量少的代码做尽量多的优化。</li>
</ol>
<h2>用注释来存放 html 代码</h2>
<p>为了便于获取注释内容，添加一层包裹：</p>
<pre>
&lt;div id=&quot;comment-data&quot;&gt;&lt;!--
html code
--&gt;&lt;/div&gt;
</pre>
<p>这样，获取代码很简单：</p>
<pre>
var htmlCode = document.getElementById('comment-data').childNodes[0].nodeValue;
</pre>
<p>缺点是：</p>
<ol>
<li>服务端，html 中的 <code>--&gt;</code> 要替换为某种特殊标记。（不能简单转义为 <code>--&amp;gt;</code>）</li>
<li>服务端，html 中的 <code>--</code> 也要替换为某种特殊标记。否则在 Firefox 低版本中存在 <a href="http://marcorogers.com/blog/08-02-2009/strange-firefox-rendering-bug">bug</a>.</li>
<li>浏览器端，得到 htmlCode 后，要将上面的特殊标记替换回原值。</li>
</ol>
<p>当 html code 很大时，替换的效率不高。依赖特殊标记的替换理论上也不完美。</p>
<p>还有什么存放方式呢？</p>
<h2>HTML 的元素类型</h2>
<p>HTML 元素分为五大类：</p>
<ol>
<li>Void elements. 像 hr, br, base 这种。</li>
<li>Raw text elements. 有两个：script 和 style.</li>
<li>RCDATA elements. 也有两个：textarea 和 title.</li>
<li>Foreign elements. 来自 MATHML 和 SVG 的元素。</li>
<li>Normal elements. 除了以上四种类型之外的所有元素，比如 p, div, iframe 等。</li>
</ol>
<p>显然，Void elements 和 Foreign elements 不适合用来存放 html 代码。</p>
<p>对于 Normal elements, 里面的 &lt; 字符会被当做 tag open 来解析，有一个方式是通过 display:none 来避免渲染：</p>
<pre>
&lt;div style=&quot;display:none&quot;&gt;
html code
&lt;/div&gt;
</pre>
<p>这样做，减少的只是可见的 DOM 节点数，DOM 总数依旧不变。Tokenization — Tree Construction 等操作的耗时并没减少。</p>
<p>我们将重点放到 Raw text elements 和 RCDATA elements 上来。</p>
<h2>CDATA, PCDATA 和 RCDATA</h2>
<p>先了解下 CDATA(Character Data) 的相关知识点。</p>
<p>在 XML 中，不包含子元素的元素的内容默认必须是 PCDATA(Parsed Character Data):</p>
<pre>
&lt;data&gt;&amp;lt;p&amp;gt;some text&amp;lt;/p&amp;gt;&lt;/data&gt;
</pre>
<p>“Parsed” 是指 &lt; 和 &amp; 字符要转换成 &amp;lt; 和 &amp;amp; 实体字符形式。如果不想写一大堆 &amp;xx;, 可以直接标记为 CDATA:</p>
<pre>
&lt;data&gt;&lt;![CDATA[&lt;p&gt;some text&lt;/p&gt;]]&gt;&lt;/data&gt;
</pre>
<p>这是 XML 的习惯，很严格，但对用户并不友好。在 HTML 中，如果要兼容 XML, 得像如下一样：</p>
<pre>
&lt;script&gt;
//&lt;![CDATA[
var t = &quot;&lt;p&gt;&quot;;
//]]&gt;
&lt;/script&gt;
</pre>
<p>增加的 &lt;![CDATA[ 很无聊。script 中本就是 CDATA.</p>
<p>为了让用户更舒心，让代码更自然，HTML 将 script 和 style 定义为 Raw text elements. 也就是说，这两个元素里面的内容是 raw text, 里面出现的 &gt; 就表示 &gt; 字符本身，不会被当作 tag open 来解析；&amp;gt; 也不会根据实体字符来转义，就表示 &amp;gt; 字串自身。这就是 CDATA.</p>
<p>Raw text elements 有一个限制：里面的内容不能有自身的 <a href="http://mathiasbynens.be/notes/etago">ETAGO</a> 标记，也就是说，script 里的内容不能含有 &lt;/script(\s|\\|&gt;), 否则就会导致 script 提前结束：</p>
<pre>
&lt;script&gt;
document.write(&#39;&lt;script&gt;alert(&quot;O HAI&quot;)&lt;/script&gt;&#39;);
&lt;/script&gt;
</pre>
<p>上面的代码会出错，必须打破 &amp;lt/script 组合：</p>
<pre>
&lt;script&gt;
  // Using string concatenation:
  document.write(&#39;&lt;script&gt;alert(&quot;heh&quot;)&lt;&#39; + &#39;/script&gt;&#39;); // Lame.
  // Using a string literal escape:
  document.write(&#39;&lt;script&gt;alert(&quot;huh&quot;)&lt;\x3Cscript&gt;&#39;); // Lame.
  // Simply escaping the solidus character with a reverse solidus (\):
  document.write(&#39;&lt;script&gt;alert(&quot;O HAI&quot;)&lt;\/script&gt;&#39;); // Awesome!
&lt;/script&gt;
</pre>
<p>style 也类似，不多说。</p>
<p>除了 Raw text elements, 还有 RCDATA elements. 我们来看看。</p>
<p>RCDATA(Replaceable Character Data) 表示里面可以有 &amp;xx; 等实体字符，也可以包含 &lt; 字符而不会被当作 tag open 来解析。比如：</p>
<pre>
&lt;textarea&gt;&lt;p&gt;&amp;lt;&lt;/p&gt;&lt;/textarea&gt;
</pre>
<p>在 RCDATA 里，&amp;lt; 可替换为 &lt; （Replaceable 的含义），拿到值（比如 textarea.value）后，是无从得知源码里是否有 &amp;lt; 等实体字符的。</p>
<h2>用 script 来存放 html 代码</h2>
<p>回到正题。在 Raw text elements 里，可以用 script 来存放数据：</p>
<pre>
&lt;script type=&quot;text/html&quot; id=&quot;script-data&quot;&gt;
&lt;p&gt;some text&lt;/p&gt;
&lt;/script&gt;
</pre>
<p>获取也很简单：</p>
<pre>
var htmlCode = document.getElementById('script-data').innerHTML;
</pre>
<p>这个方案比用注释来存放的方案更好，但依旧存在以下缺点：</p>
<ol>
<li>服务端，要将 script 里 html 中的 &lt;/script 替换为某种特殊标记。</li>
<li>浏览器端，得到 htmlCode 后，要将上面的特殊标记替换回原值。</li>
</ol>
<p>注意：特殊标记不能是 &lt;\/script, 因为有可能存在以下代码：</p>
<pre>
&lt;script type=&quot;text/html&quot; id=&quot;script-data&quot;&gt;
&lt;script&gt;
var str = &#39;&lt;\/script&gt;&#39;;
&lt;\/script&gt;
&lt;/script&gt;
</pre>
<p>这样替换回原值时，会误伤 str 字符串。</p>
<h2>用 textarea 来存放 html 代码</h2>
<p>textarea 中的内容会按照 RCDATA 规则来解析：</p>
<ol>
<li>遇到 &amp; 时，会尽可能得到实体字符。</li>
<li>遇到 &lt;/textarea(\s|\\|&gt;) 时，会结束解析。</li>
<li>其他都直接作为 textarea 的内容。</li>
</ol>
<pre>
&lt;textarea id=&quot;area-data&quot;&gt;
&lt;p&gt;some text&lt;/p&gt;
&lt;/textarea&gt;
</pre>
<p>获取非常简单：</p>
<pre>
var htmlCode = document.getElementById('area-data').value;
</pre>
<p>缺点：</p>
<ol>
<li>服务端，要将 html 中的 &amp; 转义成 &amp;amp;</li>
<li>服务端，要打破 ETAGO, 将 &lt;/textarea 转义成 &amp;lt;/textarea</li>
</ol>
<p>优点很明显，在浏览器端，只需通过 textarea.value 取值即可，无需进行任何转义替换操作。<br>
并且理论上不会出现任何 bug.</p>
<h2>存放大块 HTML 代码的最佳方式</h2>
<p>经过上面的分析，结果已经很明显，用 RCDATA elements 来存放数据是最妥当的。title 元素明显不合适，因此最后的选择就剩下一个了：textarea. 并且从语义上讲，用 text area 来存放 html text 也说得过去^o^</p>
<h2>回到首屏渲染优化</h2>
<p>可以根据实际情况，将页面划分成几大区域。非首屏区域，简单转义后，直接用 textarea 包裹起来。这样，DOM 数立刻就减少了。浏览器在拿到 html 代码时，首次 Tokenization — Tree Construction 的速度就会大大加快。</p>
<p>完整的优化，还需要：</p>
<ol>
<li>给浏览器合理的喘息（UI Update）时间，等首屏真正在显示器上绘制出来后，再进行下一步操作。</li>
<li>得到 textarea.value, 填充回 DOM 树时，得妥善处理内嵌的 script 代码。</li>
<li>对内嵌 script 代码中的 document.write 要妥善处理。</li>
<li>通过 textarea 回填，里面的非 defer 和 async 脚本会从同步变成异步。要妥善处理依赖关系，不破坏原有脚本逻辑。</li>
<li>对于优化项目来说，完备的测试和监控非常重要。</li>
<li>这次还做了 AssetsTransfer. 用户第一次访问时，会将首屏相关的脚本和样式内嵌，并做预加载。用户再次访问时，则改成外链方式，这样能充分利用浏览器缓存，并减少 html 传输量。</li>
</ol>
<p>最后，给一张优化成果图：<br>
<img src="http://lifesinger.files.wordpress.com/2011/09/detail-speed.png?w=476&amp;h=291" alt="" title="detail-speed" width="476" height="291"></p>
<p>这是一个典型的淘宝详情页的首屏时间趋势图。可看出，首屏时间从优化前的 3s 降低到了优化后的 1.5s 左右，快了一倍！</p>
<p>更深度的优化需要对页面内容（包括脚本）做进一步的细粒度模块化，区分出优先级，然后根据需求，灵活自由地控制各个模块的下载和执行等等⋯⋯</p>
<p>这篇博客写得比较杂，关于 BigRender 优化的更多细节，以后有机会再细说。欢迎反馈、拍砖。欢迎业界各位朋友尝试 BigRender 优化，希望国内的站点速度都越来越快！</p>
<h2>参考资料</h2>
<ol>
<li><a href="http://en.wikipedia.org/wiki/CDATA">CDATA</a></li>
<li><a href="http://www.flightlab.com/~joe/sgml/cdata.html">CDATA Confusion</a></li>
<li><a href="http://www.w3.org/TR/html5/tokenization.html">HTML5 Tokenization</a></li>
<li><a href="http://mathiasbynens.be/notes/etago">The end-tag open (ETAGO) delimiter</a></li>
<li><a href="http://yiminghe.iteye.com/blog/1160895">tokenization of html</a></li>
<li><a href="http://yiminghe.iteye.com/blog/788929">html 实体字符值</a></li>
</ol>
<h2>补充说明</h2>
<p><ins>2011-09-23:</ins> 在业界，script 经常用来存放 template 数据：</p>
<pre>
&lt;script type=&quot;text/template&quot;&gt;
&lt;h1&gt;{{title}}&lt;/h1&gt;
&lt;p&gt;I am {{name}}...&lt;/p&gt;
&lt;/script&gt;
</pre>
<p>绝大部分情况下，template 里不会出现 &lt;/script . 这样，服务端和浏览器端都无需做任何 replace, 是目前用来存放 template 的最佳实践。</p>
<p><ins>2011-09-24:</ins> 举例说明下 textarea 中为何要转义 &amp;. 假设原代码为：</p>
<pre>
&lt;p&gt;&amp;amp;lt; represents &amp;lt;&lt;/p&gt;
&lt;p&gt;&amp;lt;/p&amp;gt;&lt;/p&gt;
</pre>
<p>如果直接放到 textarea 中，</p>
<pre>
&lt;textarea&gt;
&lt;p&gt;&amp;amp;lt; represents &amp;lt;&lt;/p&gt;
&lt;p&gt;&amp;lt;/p&amp;gt;&lt;/p&gt;
&lt;/textarea&gt;
</pre>
<p>由于 textarea 是 RCDATA 元素，上面的代码等价于：</p>
<pre>
&lt;textarea&gt;
&lt;p&gt;&amp;lt; represents &lt;&lt;/p&gt;
&lt;p&gt;&lt;/p&gt;&lt;/p&gt;
&lt;/textarea&gt;
</pre>
<p>获取 textarea.value, 回填到 DOM 树的代码为：</p>
<pre>
out.innerHTML = textarea.value;
</pre>
<p>这时页面中的显示效果明显和原来不一样了。<br>
如果将所有 &amp; 都转化成 &amp;amp; 则可以保持原样。</p>
<p>注意：理论上，并不需要将所有 &amp; 转化成 &amp;amp;, 只需要将与 HTML 语法冲突的字符串 &amp;lt; &amp;gt; &amp;amp; &amp;nbsp; 中的 &amp; 转化成 &amp;amp; 即可。但这样做，还得处理 &amp;#xx 等数值表示，比如 &amp; 还有 &amp;#38; &amp;#0038; 两种表现形式，这样替换起来更麻烦，不如将所有 &amp; 替换成 &amp;amp; 来得快捷高效。</p>
<br>  <a rel="nofollow" href="http://feeds.wordpress.com/1.0/gocomments/lifesinger.wordpress.com/456/"><img alt="" border="0" src="http://feeds.wordpress.com/1.0/comments/lifesinger.wordpress.com/456/"></a> <a rel="nofollow" href="http://feeds.wordpress.com/1.0/godelicious/lifesinger.wordpress.com/456/"><img alt="" border="0" src="http://feeds.wordpress.com/1.0/delicious/lifesinger.wordpress.com/456/"></a> <a rel="nofollow" href="http://feeds.wordpress.com/1.0/gofacebook/lifesinger.wordpress.com/456/"><img alt="" border="0" src="http://feeds.wordpress.com/1.0/facebook/lifesinger.wordpress.com/456/"></a> <a rel="nofollow" href="http://feeds.wordpress.com/1.0/gotwitter/lifesinger.wordpress.com/456/"><img alt="" border="0" src="http://feeds.wordpress.com/1.0/twitter/lifesinger.wordpress.com/456/"></a> <a rel="nofollow" href="http://feeds.wordpress.com/1.0/gostumble/lifesinger.wordpress.com/456/"><img alt="" border="0" src="http://feeds.wordpress.com/1.0/stumble/lifesinger.wordpress.com/456/"></a> <a rel="nofollow" href="http://feeds.wordpress.com/1.0/godigg/lifesinger.wordpress.com/456/"><img alt="" border="0" src="http://feeds.wordpress.com/1.0/digg/lifesinger.wordpress.com/456/"></a> <a rel="nofollow" href="http://feeds.wordpress.com/1.0/goreddit/lifesinger.wordpress.com/456/"><img alt="" border="0" src="http://feeds.wordpress.com/1.0/reddit/lifesinger.wordpress.com/456/"></a> <img alt="" border="0" src="http://stats.wordpress.com/b.gif?host=lifesinger.wordpress.com&amp;blog=38365&amp;post=456&amp;subd=lifesinger&amp;ref=&amp;feed=1" width="1" height="1">