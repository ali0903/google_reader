---
layout: post
title:  "JavaScript解析：让搜索引擎看到更真实的网页"
date:   2012-07-03 10:39:54
author: editor
categories: program
---

## JavaScript解析：让搜索引擎看到更真实的网页
### by editor
### at 2012-07-03 10:39:54
### original <http://www.udpwork.com/item/7626.html>

<p>长期以来，站长们选择使用JavaScript来实现网页的动态行为，这样做的原因是多种多样的，如加快页面的响应速度、降低网站流量、隐藏链接或者嵌入广告等。由于早期的搜索引擎没有相应的处理能力，导致在索引这类网页上往往出现问题，可能无法收录有价值的资源，也可能出现作弊。</p>
<p>引入JavaScript解析的目的，正是为了解决上述两方面的问题，其结果也就是使搜索引擎可以更为清晰的了解用户实际打开该网页时看到的效果。比如有些网站会将用户评论、评分等信息从网页HTML中抽离，利用JavaScript甚至AJAX等方法在页面被打开的时候动态显示出来，早期的搜索引擎此时能处理到的页面内容就是缺失的，由此会进一步影响对该页面索引价值的判断。</p>
<p>要引入JavaScript解析，需要考虑本身的设计与实现、解析速度和对系统其它方面影响等因素，本文通过一些典型的案例来分析如何设计并实现一套网页JavaScript解析系统，并简要介绍这样的系统对于搜索引擎其它部分的作用和影响。</p>
<p></p>
<h2>一、发现页面链接</h2>
<p>一般来说，页面链接都是以HTML中的A标签形式存在，链接URL标记在href属性中，但实际存在着一些网站会选择更为“动态”的方式，较为常见的方式有两种：一种是动态写入或调整A标签，另一种是在用户点击的时候触发事件改变默认的链接打开方式。</p>
<h3>1. 动态写入或调整链接标签</h3>
<p>抽象的说，网页要实现这样的效果，乃至后文描述的其它效果，与把大象放入冰箱极为类似，分为三步：找到要写入/修改的目标（找到大象），准备好要写入/修改的内容（打开冰箱门），执行写入/修改（把它放进去）。</p>
<p>这三步操作映射到JavaScript上，就是先后调用三组标准的浏览器功能函数：页面元素定位、数据准备以及页面修改。那么，JavaScript解析的工作就是同样提供出这样的函数，随着被站长的JavaScript代码调用自然的发现相应的内容和行为。</p>
<p>分析至此，所需实现的函数也就基本确定了，其中较为简单的包括：</p>
<ul><li><ul><li>document.getElementById // 定位</li>
<li>document.getElementsByTagName // 定位</li>
<li>document.getElementsByClassName // 定位</li>
<li>node.[firstChild/nextSibling/previousSibling/parentNode] // 定位</li>
<li>document.[createElement/createTextNode] // 创建链接</li>
<li>node.[appendChild/insertBefore/innerHTML=?] // 写入内容</li>
<li>element.getAttribute, element.setAttribute // 设置属性</li>
<li>element.href = ? // 设置属性</li>
</ul>
</li>
</ul>
<p>至于要写入的内容，可能是以数组等形式保存在JavaScript种，也可能是使用AJAX动态加载。前者属于JavaScript语言的内置功能，此处不再复述；后者是一个单独的话题，会在后文专门讨论。</p>
<h3>2. 点击时触发事件改变默认的链接打开方式</h3>
<p>页面这样做的原因不一，有的是为了隐藏链接，有的是为了实现弹出窗口，有的则是为了程序拼接URL，还有的是做检查看是否应该打开链接等等。但所有这些原因都对应着同样的实现方法：添加click事件。</p>
<p>添加click事件的方法有三种：</p>
<ul><li><ul><li>将A标签的href属性设置为“javascript:func(…)”的形式</li>
<li>设置A标签的onclick属性，设置为onclick=”js_code” 的形式</li>
<li>调用事件绑定函数，如my_link_node.addEventListener(‘click’, func, false)</li>
</ul>
</li>
</ul>
<p>支持这三种方法本身是较为简单的，需要注意的地方在于如何触发这样的click事件，以及如何在触发之后截获目的URL。</p>
<p>对于触发事件而言，首先需要收集到所有可能的click事件，而后再依次触发。但对于每一个要触发的click而言，实际触发之前必须先检查其是否还存在，这是由于在其之前的click事件很可能已经把当前这个click删除掉了。</p>
<p>要做到截获URL，首先要实现相关的页面跳转函数，既location.href = ?，window.open等。而后通过设置一系列标志，将本次点击和页面跳转关连起来，如此也就得到了目标URL。</p>
<h2>二、动态页面内容</h2>
<p>页面动态内容是一种提升页面加载速度、增强网站技术灵活性的手段，可以将那些会改变的内容（如评论、评分等）抽离，使页面分为静态和动态两部分：静态内容可以使用缓存等方法加快页面显示速度、降低网站流量；动态内容则有格式简单好生成的优势，同时也能节省流量。</p>
<p>另一方面，动态内容也是加载广告和内容作弊的重要方法，最常见的就是写入iframe，这对于早期搜索引擎而言有极大的隐蔽性。</p>
<p>在技术层次上，动态页面内容所需要的工作与上一节“动态写入或调整A标签”在很大程度上是相同的，这里需要增加的是经典的“document.write”方法。</p>
<p>该方法是最早的JavaScript功能之一，用于向页面直接写入一段HTML代码，至今仍在广泛使用。对于该方法，早期的搜索引擎都有所支持，但方法基本限于字符匹配，仅能支持最直接的写入一个JavaScript字符串的方式，对于稍微复杂的文本拼接显得无能为力。但对于JavaScript解析而言，这段代码终究是要符合语言规范的，因此就能做到完整支持，处理文本拼接、条件判断和混淆代码等各种情况。</p>
<p>这里还需要讨论的一点是嵌套的document.write，也就是通过document.write写入一个SCRIPT标签，该标签内部是另一段document.write。这类问题在跳转作弊页面中屡见不鲜，对其支持就不只需要JavaScript解析，还需要HTML解析器能够支持处理嵌套的HTML写入功能，这里就不加以分析了。</p>
<p>通过上述方法，无论是网页的主体信息，还是广告或其它辅助信息，都会被暴露出来，从而更好的理解站长意图。</p>
<h2>三、网页跳转</h2>
<p>网页跳转在有些情况下是达到页面效果的必要选择，但同样会用于作弊。在技术上，多以下面两种方式出现：</p>
<ul><li><ul><li>直接调用页面跳转函数</li>
<li>针对搜索引擎的UA、referer等调用页面跳转函数</li>
</ul>
</li>
</ul>
<p>这里要实现识别，最核心的就是实现页面跳转函数：location对象。由于这是技术上唯一的JavaScript跳转函数，所以无论页面的JavaScript如何撰写如何混淆，最终都会调用该函数。因此，尽管不同页面的跳转代码看上去五花八门，但识别出来却是简单的。</p>
<h2>四、关于AJAX</h2>
<p>AJAX是极为常见的网页技术，根本上说就是在网页显示期间，动态的从互联网上获取一段数据（可能是HTML也可能是其它），经过处理后加以显示。</p>
<p>对于该技术，根本的工作并不在于XMLHttpRequest对象的实现，而是在于对搜索引擎爬虫架构的影响。众所周知，爬虫抓取页面，遍历其链接，再依次抓取的形式设计的，其工作主要集中在调度和控制抓取压力上，抓取器本身较为简单，通常不具备抓取后即时执行JavaScript并抓取AJAX数据的能力，因此需要技术升级方可支持AJAX。</p>
<p>对抓取器的分析超出了本文的范围，有兴趣的读者可以查看其它相关文献。</p>
<h2>总结</h2>
<p>通过前面的案例分析，我们总结出了实现JavaScript解析所需要的基本工作，此外再增加一定的基础性建设就能构成一套较为完整的系统了。这里我们再次整理一下，将其分为三个部分：</p>
<p>1.  在HTML解析器中嵌入JavaScript语言引擎，语言引擎可以选择V8、SpiderMonkey等成熟的开源方案。</p>
<p>2.  实现所需的功能函数，具体可参考W3C的相关HTML和DOM规范。</p>
<p>3.  作为一个直接推论，需要收录所谓的.js文件，这是JavaScript解析所需要“解析”的源代码。</p>
<p>本文中介绍的功能仅是一部分较为常见的JavaScript功能，要让搜素引擎真正看到实际的页面还需要进一步实现其它需要的功能，此外还需要配合对HTML、CSS、图片等资源的支持。</p>
<p>最后，对于希望使用JavaScript的站长来说，本文给出如下建议：</p>
<p>1.  不要使用过于复杂的JavaScript技术，这不利于搜索引擎的收录</p>
<p>2.  不要阻止对.js文件的收录，否则会限制JavaScript解析的能力</p>
<p>3.  合理的划分站点的静态部分和动态部分</p>
<p></p>
<p>by Lileding</p>

			<div style="margin-top:8px;padding:6px 0;border-top:1px solid #3cf">
				<div style="text-align:center;margin:16px 0;padding:6px;border:0px dashed #999;font-family:arial;font-size:26px;font-weight:bold">
	<a href="http://www.udpwork.com/item/7626.html#review_form" title="不喜欢" style="text-decoration:none">
		<img src="http://www.udpwork.com//images/thumb_down24.gif" alt="">
		<span style="color:#f33">0</span>
	</a>
	   
	<a href="http://www.udpwork.com/item/7626.html#review_form" title="喜欢" style="text-decoration:none">
		<img src="http://www.udpwork.com//images/thumb_up24.gif" alt="">
		<span style="color:#3c3">0</span>
	</a>
</div>				<p>
					由 <a href="http://www.udpwork.com/">IT 牛人博客聚合网站(udpwork.com)</a> 聚合
					|
					<a href="http://www.udpwork.com/item/7626.html#reviews">评论: 0</a>
					|
					<a href="http://book.benegg.com/tag/%E7%BC%96%E7%A8%8B?from=udpwork-feed">10000+ 本编程/Linux PDF/CHM 电子书下载</a>
				</p>
			</div>