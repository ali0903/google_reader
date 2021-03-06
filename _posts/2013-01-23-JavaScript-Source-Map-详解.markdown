---
layout: post
title:  "JavaScript Source Map 详解"
date:   2013-01-23 14:30:38
author: 阮一峰
categories: program
---

## JavaScript Source Map 详解
### by 阮一峰
### at 2013-01-23 14:30:38
### original <http://www.udpwork.com/item/9133.html>

<p>上周，<a href="http://blog.jquery.com/2013/01/15/jquery-1-9-final-jquery-2-0-beta-migrate-final-released/">jQuery 1.9</a>发布。</p>
<p><img src="http://image.beekka.com/blog/201301/bg2013012203.png"></p>
<p>这是2.0版之前的最后一个新版本，有很多新功能，其中一个就是支持Source Map。</p>
<p>访问<a href="http://ajax.googleapis.com/ajax/libs/jquery/1.9.0/jquery.min.js">http://ajax.googleapis.com/ajax/libs/jquery/1.9.0/jquery.min.js</a>，打开压缩后的版本，滚动到底部，你可以看到最后一行是这样的：</p>
<p>　　//@ sourceMappingURL=jquery.min.map</p>
<p>这就是Source Map。它是一个独立的map文件，与源码在同一个目录下，你可以<a href="http://ajax.googleapis.com/ajax/libs/jquery/1.9.0/jquery.min.map">点击进去</a>，看看它的样子。</p>
<p>这是一个很有用的功能，本文将详细讲解这个功能。</p>
<p><strong>一、从源码转换讲起</strong>
</p>
<p>JavaScript脚本正变得越来越复杂。大部分源码（尤其是各种函数库和框架）都要经过转换，才能投入生产环境。</p>
<p>常见的源码转换，主要是以下三种情况：</p>
<p>　　（1）压缩，减小体积。比如jQuery 1.9的源码，压缩前是252KB，压缩后是32KB。</p>
<p>　　（2）多个文件合并，减少HTTP请求数。</p>
<p>　　（3）其他语言编译成JavaScript。最常见的例子就是CoffeeScript。</p>
<p>这三种情况，都使得实际运行的代码不同于开发代码，除错（debug）变得困难重重。</p>
<p>通常，JavaScript的解释器会告诉你，第几行第几列代码出错。但是，这对于转换后的代码毫无用处。举例来说，jQuery 1.9压缩后只有3行，每行3万个字符，所有内部变量都改了名字。你看着报错信息，感到毫无头绪，根本不知道它所对应的原始位置。</p>
<p>这就是Source map想要解决的问题。</p>
<p><strong>二、什么是Source map</strong>
</p>
<p>简单说，Source map就是一个信息文件，里面储存着位置信息。也就是说，转换后的代码的每一个位置，所对应的转换前的位置。</p>
<p>有了它，出错的时候，除错工具将直接显示原始代码，而不是转换后的代码。这无疑给开发者带来了很大方便。</p>
<p><img src="http://image.beekka.com/blog/201301/bg2013012204.png"></p>
<p>目前，暂时只有Chrome浏览器支持这个功能。在Developer Tools的Setting设置中，确认选中&quot;Enable source maps&quot;。</p>
<p><img src="http://image.beekka.com/blog/201301/bg2013012201.png"></p>
<p><strong>三、如何启用Source map</strong>
</p>
<p>正如前文所提到的，只要在转换后的代码尾部，加上一行就可以了。</p>
<p>　　//@ sourceMappingURL=/path/to/file.js.map</p>
<p>map文件可以放在网络上，也可以放在本地文件系统。</p>
<p><strong>四、如何生成Source map</strong>
</p>
<p>最常用的方法是使用Google的<a href="https://developers.google.com/closure/compiler/">Closure编译器</a>。</p>
<p>生成命令的格式如下：</p>
<p>　　java -jar compiler.jar \
<br>
　　　　--js script.js \
<br>
　　　　--create_source_map ./script-min.js.map \
<br>
　　　　--source_map_format=V3 \
<br>
　　　　--js_output_file script-min.js</p>
<p>各个参数的意义如下：</p>
<p>　　- js： 转换前的代码文件
<br>
　　- create_source_map： 生成的source map文件
<br>
　　- source_map_format：source map的版本，目前一律采用V3。
<br>
　　- js_output_file： 转换后的代码文件。</p>
<p>其他的生成方法可以参考<a href="http://net.tutsplus.com/tutorials/tools-and-tips/source-maps-101/">这篇文章</a>。</p>
<p><strong>五、Source map的格式</strong>
</p>
<p>打开Source map文件，它大概是这个样子：</p>
<p>　　{
<br>
　　　　version : 3,
<br>
　　　　file: &quot;out.js&quot;,
<br>
　　　　sourceRoot : &quot;&quot;,
<br>
　　　　sources: [&quot;foo.js&quot;, &quot;bar.js&quot;],
<br>
　　　　names: [&quot;src&quot;, &quot;maps&quot;, &quot;are&quot;, &quot;fun&quot;],
<br>
　　　　mappings: &quot;AAgBC,SAAQ,CAAEA&quot;
<br>
　　}</p>
<p>整个文件就是一个JavaScript对象，可以被解释器读取。它主要有以下几个属性：</p>
<p>　　- version：Source map的版本，目前为3。</p>
<p>　　- file：转换后的文件名。</p>
<p>　　- sourceRoot：转换前的文件所在的目录。如果与转换前的文件在同一目录，该项为空。</p>
<p>　　- sources：转换前的文件。该项是一个数组，表示可能存在多个文件合并。</p>
<p>　　- names：转换前的所有变量名和属性名。</p>
<p>　　- mappings：记录位置信息的字符串，下文详细介绍。</p>
<p><strong>六、mappings属性</strong>
</p>
<p>下面才是真正有趣的部分：两个文件的各个位置是如何一一对应的。</p>
<p>关键就是map文件的mappings属性。这是一个很长的字符串，它分成三层。</p>
<p>　　第一层是<strong>行对应</strong>
，以分号（;）表示，每个分号对应转换后源码的一行。所以，第一个分号前的内容，就对应源码的第一行，以此类推。</p>
<p>　　第二层是<strong>位置对应</strong>
，以逗号（,）表示，每个逗号对应转换后源码的一个位置。所以，第一个逗号前的内容，就对应该行源码的第一个位置，以此类推。</p>
<p>　　第三层是<strong>位置转换</strong>
，以<a href="http://en.wikipedia.org/wiki/Variable-length_quantity">VLQ编码</a>表示，代表该位置对应的转换前的源码位置。</p>
<p>举例来说，假定mappings属性的内容如下：</p>
<p>　　mappings:&quot;AAAAA,BBBBB;CCCCC&quot;</p>
<p>就表示，转换后的源码分成两行，第一行有两个位置，第二行有一个位置。</p>
<p><strong>七、位置对应的原理</strong>
</p>
<p>每个位置使用五位，表示五个字段。</p>
<p>从左边算起，</p>
<p>　　- 第一位，表示这个位置在（转换后的代码的）的第几列。</p>
<p>　　- 第二位，表示这个位置属于sources属性中的哪一个文件。</p>
<p>　　- 第三位，表示这个位置属于转换前代码的第几行。</p>
<p>　　- 第四位，表示这个位置属于转换前代码的第几列。</p>
<p>　　- 第五位，表示这个位置属于names属性中的哪一个变量。</p>
<p>有几点需要说明。首先，所有的值都是以0作为基数的。其次，第五位不是必需的，如果该位置没有对应names属性中的变量，可以省略第五位。再次，每一位都采用VLQ编码表示；由于VLQ编码是变长的，所以每一位可以由多个字符构成。</p>
<p>如果某个位置是AAAAA，由于A在VLQ编码中表示0，因此这个位置的五个位实际上都是0。它的意思是，该位置在转换后代码的第0列，对应sources属性中第0个文件，属于转换前代码的第0行第0列，对应names属性中的第0个变量。</p>
<p><strong>八、VLQ编码</strong>
</p>
<p>最后，谈谈如何用<a href="http://en.wikipedia.org/wiki/Variable-length_quantity">VLQ编码</a>表示数值。</p>
<p>这种编码最早用于MIDI文件，后来被多种格式采用。它的特点就是可以非常精简地表示很大的数值。</p>
<p>VLQ编码是变长的。如果（整）数值在-15到+15之间（含两个端点），用一个字符表示；超出这个范围，就需要用多个字符表示。它规定，每个字符使用6个两进制位，正好可以借用<a href="http://en.wikipedia.org/wiki/Base_64">Base 64</a>编码的字符表。</p>
<p><img src="http://image.beekka.com/blog/201301/bg2013012202.png"></p>
<p>在这6个位中，左边的第一位（最高位）表示是否&quot;连续&quot;（continuation）。如果是1，代表这６个位后面的6个位也属于同一个数；如果是0，表示该数值到这6个位结束。</p>
<p>　　Continuation
<br>
　　|　　　　　Sign
<br>
　　|　　　　　|
<br>
　　V　　　　　V
<br>
　　１０１０１１</p>
<p>这6个位中的右边最后一位（最低位）的含义，取决于这6个位是否是某个数值的VLQ编码的第一个字符。如果是的，这个位代表&quot;符号&quot;（sign），0为正，1为负（Source map的符号固定为0）；如果不是，这个位没有特殊含义，被算作数值的一部分。</p>
<p><strong>九、VLQ编码：实例</strong>
</p>
<p>下面看一个例子，如何对数值16进行VLQ编码。</p>
<p>　　第一步，将16改写成二进制形式10000。</p>
<p>　　第二步，在最右边补充符号位。因为16大于0，所以符号位为0，整个数变成100000。</p>
<p>　　第三步，从右边的最低位开始，将整个数每隔5位，进行分段，即变成1和00000两段。如果最高位所在的段不足5位，则前面补0，因此两段变成00001和00000。</p>
<p>　　第四步，将两段的顺序倒过来，即00000和00001。</p>
<p>　　第五步，在每一段的最前面添加一个&quot;连续位&quot;，除了最后一段为0，其他都为1，即变成100000和000001。</p>
<p>　　第六步，将每一段转成Base 64编码。</p>
<p>查表可知，100000为g，000001为B。因此，数值16的VLQ编码为gB。上面的过程，看上去好像很复杂，做起来其实很简单，具体的实现请看官方的<a href="https://github.com/mozilla/source-map/blob/master/lib/source-map/base64-vlq.js">base64-vlq.js</a>文件，里面有详细的注释。</p>
<p><strong>十、参考链接</strong>
</p>
<p>　　-<a href="http://www.html5rocks.com/en/tutorials/developertools/sourcemaps/">Introduction To JavaScript Source Maps</a>
<br>
　　-<a href="https://docs.google.com/document/d/1U1RGAehQwRypUTovF1KRlpiOFze0b-_2gc6fAH0KY0k/edit">Source Map Revision 3 Proposal</a></p>
<p>（完）</p>
<div><h3>文档信息</h3>
<ul><li>版权声明：自由转载-非商用-非衍生-保持署名 |<a href="http://creativecommons.org/licenses/by-nc-nd/3.0/deed.zh">Creative Commons BY-NC-ND 3.0</a></li>
<li>原文网址：<a href="http://www.ruanyifeng.com/blog/2013/01/javascript_source_map.html">http://www.ruanyifeng.com/blog/2013/01/javascript_source_map.html</a></li>
<li>最后修改时间：2013年1月27日 01:14</li>
<li>付费支持：<a href="https://me.alipay.com/ruanyf"><img src="http://www.ruanyifeng.com/blog/images/rmb_32.png" alt="人民币 - 支付宝"></a>|<a href="https://www.paypal.com/cgi-bin/webscr?cmd=_xclick&amp;business=yifeng.ruan@gmail.com&amp;currency_code=USD&amp;amount=0.99&amp;return=http://www.ruanyifeng.com/thank.html&amp;item_name=Ruan%20YiFeng&#39;s%20Blog&amp;undefined_quantity=1&amp;no_note=0"><img src="http://www.ruanyifeng.com/blog/images/dollar_32.png" alt="美元 - paypal"></a></li>
</ul>
</div>
<div></div>

			<div style="margin-top:8px;padding:6px 0;border-top:1px solid #3cf">
				<div style="text-align:center;margin:16px 0;padding:6px;border:0px dashed #999;font-family:arial;font-size:26px;font-weight:bold">
	<a href="http://www.udpwork.com/item/9133.html#review_form" title="不喜欢" style="text-decoration:none">
		<img src="http://www.udpwork.com//images/thumb_down24.gif" alt="">
		<span style="color:#f33">0</span>
	</a>
	   
	<a href="http://www.udpwork.com/item/9133.html#review_form" title="喜欢" style="text-decoration:none">
		<img src="http://www.udpwork.com//images/thumb_up24.gif" alt="">
		<span style="color:#3c3">0</span>
	</a>
</div>				<p>
					由 <a href="http://www.udpwork.com/">udpwork.com</a> 聚合
					|
					<a href="http://www.udpwork.com/item/9133.html#reviews">评论: 0</a>
					|
					<a href="http://www.jikenow.com/">要! 要! 即刻! Now!</a>
				</p>
			</div>