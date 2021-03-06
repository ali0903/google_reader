---
layout: post
title:  "Chromium 用 Blink引擎 代替 Webkit"
date:   2013-04-04 10:34:32
author: 
categories: program
---

## Chromium 用 Blink引擎 代替 Webkit
### by 
### at 2013-04-04 10:34:32
### original <http://lyric.im/yabou-of-google-replace-webkit-with-blink/>

<p>被评论说文章写得太技术看不懂，今天先说非技术部分。</p>
<p>首先，对用户的影响：</p>
<ul>
<li>这一改进主要在技术层次上，会使得Chrome和Chromium更高更快更强。</li>
<li>Google以后会拿出一整套和微软直接抗衡的解决方案。</li>
</ul>
<p>看，非技术的部分两句话说完了，也就是整个分析的结论。作为一个风水师，我发现通过技术变迁来给产品的战略看风水也是蛮有趣的。</p>
<p>然后是技术部分，换引擎的理由倒是很显然：</p>
<ul>
<li>因为Webkit性能不够</li>
<li>因为不适合Chromium/Chrome的多进程架构</li>
<li>清晰的codebase有利于以后Chromium的发展</li>
</ul>
<p>对生态圈的影响：</p>
<ul>
<li>Google的目标就是让Web更快。<ol>
<li>最初是觉得浏览器都太烂于是坑了Mozilla，出了Chrome</li>
<li>其次是觉得Javascript的VM太慢，所以出了V8引擎</li>
<li>然后觉得V8还是不给力啊，本质上还是Javascript太渣了，搞个Dart语言看看；</li>
<li>之后Google发现好像是ISP跟不上我们的脚步了，于是<a href="https://fiber.google.com/about/">开始给用户铺光纤</a></li>
<li>觉得HTTP太低效了于是推出SPDY…</li>
<li>实验性的Chrome OS和Chromebook</li>
<li>攻击性很强的<a href="http://lyric.im/more-about-new-chrome-packaged-apps/">新Chrome App API</a>发布</li>
<li>再到今天的Blink代替Webkit</li>
</ol>
</li>
<li>总之就是要让网速不是障碍，让网络服务速度不是障碍，最后让Web App速度赶上本地App，然后就可以正面开战了。</li>
<li>所有这一切都被称为「Google的野望」，大概描述了一个屌丝青年Google：先小敲小打地重新做轮子，大家都觉得伊是个大好人；然后放一块儿就构建自己的生态系统；最后在眼皮底下干翻以微软为首的老一辈革命家的全景。</li>
<li>嗯，在攻击性很强的new Chrome App API的加持下，Web App能做的事情已经非常多，类似一个本地程序了，大家快来一改现在Chrome App渣一样的局面。</li>
</ul>
<p>对开发者的影响：</p>
<ul>
<li>对Angular.js这样的JS框架是利好消息，对所有的Web App来说也是利好消息，因为目前对新引擎的野望中，有3项的改进会让DOM操作变得更快，并且明确说明会重写整个webkit的DOM实现。考虑到现在DOM操作速度如此之慢，已经是Web App发展的瓶颈了，Google拿它开刀是理所当然。<ol>
<li>提升DOM 3 Event和UI Event的性能</li>
<li>解决目前Webkit对DOM的向后兼容性所导致的性能问题</li>
<li>DOM移到Javascript heap</li>
</ol>
</li>
<li>对前端工程师来说不需要太担心，<ol>
<li>Blink fork 自 Webkit，以后也会兼容已经成为标准的-webkit-*私有特性。</li>
<li>Chromium 团队渐进性更新很靠谱。</li>
<li>考虑到Google的操性，如果要往Chromium/Chrome加私货，一定会加到Chrome App和Chrome extension里；如果实在要往通用web里加，一定会先折腾成标准或者至少是标准草案，然后自己先拿出实现来；因此不会形成IE里的ActiveX那种东西。</li>
</ol>
</li>
</ul>