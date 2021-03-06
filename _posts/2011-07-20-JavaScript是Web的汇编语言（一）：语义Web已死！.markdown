---
layout: post
title:  "JavaScript是Web的汇编语言（一）：语义Web已死！"
date:   2011-07-20 20:59:04
author: 为之漫笔
categories: program
---

## JavaScript是Web的汇编语言（一）：语义Web已死！
### by 为之漫笔
### at 2011-07-20 20:59:04
### original <http://www.cn-cuckoo.com/2011/07/20/javascript-is-assembly-language-for-the-web-i-2511.html>

<p style="text-align:right">原文地址：<a href="http://www.hanselman.com/blog/JavaScriptIsAssemblyLanguageForTheWebSematicMarkupIsDeadCleanVsMachinecodedHTML.aspx">JavaScript is Assembly Language for the Web: Sematic Markup is Dead! Clean vs. Machine-coded HTML</a></p>
<p><strong><span style="color:red">（更新）</span>有些人认为“JavaScript是Web的汇编语言”完全是精神病说的话。为此，我询问了几位JavaScript权威，比如Brendan Eich（JavaScript之父）、Douglas Crockford（JSON之父），还有Mike Shaver（Mozilla技术副总裁）。<a href="http://http://www.cn-cuckoo.com/2011/07/21/javascript-is-assembly-language-for-the-web-ii-2531.html">他们的评论发表在下一篇文章里</a>。</strong></p>
<p>昨天我跟Erik Meijer聊天，他说：</p>
<div style="border-left:2px solid #ddd;margin-left:1em;padding-left:1em;font-weight:bold">JavaScript就是一个汇编语言。JavaScript加上生成的HTML就像是.NET汇编一样。浏览器可以执行这些代码，但没人真的关心里面到底写的是什么。<br>
——Erik Meijer</div>
<p>怎么会说起这件事儿呢？当时我正在<strong>试用Google+</strong>，就跟上大多数让我印象深刻的网站一样，我立即就查看它的源代码。不看不要紧，一看吓一跳：<br>
<img src="http://www.cn-cuckoo.com/cache/img/2511_image_1.png" alt=""></p>
<p>咱就将就说吧，我看到了1300行代码，密密麻麻的，大约90KB。上面图片显示的只是最前面的一小部分，基本上都是“瘦身后”的JavaScript代码。再往下看，页面中间呢，全都是像下面这样的span、div以及生成的类和id：<span></span></p>
<p><img src="http://www.cn-cuckoo.com/cache/img/2511_image_2.png" alt=""></p>
<p><strong>我勒个去，满篇都是GUID（Globally Unique Identifier，全局唯一标识符）。</strong></p>
<p>话又说回来了，<strong><a title="http://msn.com" href="http://msn.com">http://msn.com</a>、<a title="http://www.bing.com" href="http://www.bing.com">http://www.bing.com</a>、<a title="http://.www.facebook.com" href="http://.www.facebook.com">http://.www.facebook.com</a></strong>，全都这样啊。就连<strong><a title="http://www.twitter.com" href="http://www.twitter.com">http://www.twitter.com</a></strong>也都开始有点“瘦身”的迹象了。所有大点的网站好像都丝毫不在乎什么标记之美。这是为什么呢？</p>
<p>这样有效率啊，性能高啊。Google很多最出色的网站背后都依赖GWT呢。<strong>在这种情况下，要是这一类网站的里头和外头全都一样漂亮，你反而会觉得不可思议了。</strong></p>
<p>不能不说这可真有点讽刺的意味。曾几何时，ASP.NET的开发人员对ViewState可是怨声载道啊。“简直太笨了”的意思就是“我看不懂它都干什么了。”ViewState曾经（现在也）是一项让Web开发效率提高很多倍的技术。它跟Google Web Toolkit（GWT）不一样，但GWT与WebForms的出发点也并非完全没有相似之处。看看GWT网站自己怎么说：</p>
<div style="border-left:2px solid #ddd;margin-left:1em;padding-left:1em">Google Web Toolkit（GWT）是一个开发工具包，用于构建和优化基于浏览器的复杂应用。GWT的目标是提高高性能Web应用开发的效率，而且无需开发人员熟悉浏览器的各种怪癖，以及XMLHttpRequest，还有JavaScript。</div>
<p>这个出发点可真是值得赞美，不对？难道不可以这样说（抱歉，开个玩笑而已）：</p>
<div style="border-left:2px solid #ddd;margin-left:1em;padding-left:1em">“<strong>ASP.NET WebForms</strong>”是一个开发工具包，用于构建和优化基于浏览器的复杂应用。它的目标是提高高性能Web应用开发的效率，而且无需开发人员熟悉浏览器的各种怪癖，以及XMLHttpRequest，还有JavaScript。</div>
<p>本文的目的不是想夸奖WebForms，也不是给WebForms正名。WebForms对于某些应用是不二之选，正如GWT对其他一些应用那样。我真正想说的是，使用服务器端的工具包，没有办法像使用jQuery写出清晰的JavaScript，或者使用Razor或HAML写出清晰、清楚的标记一样，给Web开发带来真正的快乐。归根结底，其实就是你选择的抽象级别的问题。</p>
<p>所谓的语义标记在这种情况下仍然是被隐藏的，而诸如<strong><a title="http://schema.org" href="http://schema.org">http://schema.org</a></strong>之类的站点也仍然非常重要，只不过可别指望你能在心仪的站点里看到缩进得像俳句一样整齐的源代码。</p>
<p>大家知道，精简和压缩属于正交优化。而我要说的是，一点也不在乎标记和脚本发送到客户端之后是否美观，确实太草率了。<strong>假如谁都不在乎发送到浏览器的标记，只在乎结果，那谁的标记和JS就那么不值钱啊，谁还愿意主动公开自己的源代码呢？</strong>反正，网站不是运行得挺好嘛，谁还在乎其他的？</p>
<p>现在我要给亲爱的读者提个问题，你觉得自己为什么那么在乎点击“查看网页源代码”之后的结果呢？难道HTML5和JavaScript是Web的新汇编语言不成？</p>
<p><strong>（更新）声明一下：</strong><br>
当然，这个比喻不一定准确。JavaScript代码无论从外观到行为，肯定不像ASM。但作为一个比喻，至少可以说明：</p>
<ul>
<li>JavaScript无所不在；</li>
<li>它速度快而且越来越快；</li>
<li>JavaScript酷似低级的Web编程语言；</li>
<li>它可以通过手工编写，也可以从另一种语言编译而来。</li>
</ul>
<p>作为开发人员或者设计人员，如果有工具提供了你需要的控制和你需要的结果，你最关心哪一个？我认为Rails、ASP.NET，甚至GWT，都没有100%做到这一点。它们都有自己的问题，但<strong>我认为将来的Web不会再专注于清晰的标记，而是夺目的用户体验和语言、工具的天下，开发人员会很享受，效率也会更高。</strong></p>
<p>亲爱的读者，你愿意HTML和JavaScript再多抽象一点吗？还是希望它少抽象一点？</p>
<p><strong>（再更新）</strong>为了让大家明白，我得再说一遍。本文讨论了两个独立的问题。一个当然就是源代码经过了精简和通常的混淆。但这只是第一个问题。真正的问题在于，JavaScript已经成了其他多种语言的目标语言。GWT是一个用JAVA来写Web应用的框架，它产生的字节码是“JavaScript”。GWT为原来天然的语言（HTML+JS）选择了一个设计好的高级语言，并将整个浏览器当成了一个VM。好，问题来了：我们是在写汇编呢，还是在写某种更高级点的代码？而且，我刚知道Google+是用Closure来写的，但这不影响前面的问题。</p>