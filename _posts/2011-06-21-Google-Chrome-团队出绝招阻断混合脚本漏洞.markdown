---
layout: post
title:  "Google Chrome 团队出绝招阻断混合脚本漏洞"
date:   2011-06-21 10:54:47
author: 
categories: program
---

## Google Chrome 团队出绝招阻断混合脚本漏洞
### by 
### at 2011-06-21 10:54:47
### original <http://www.cnbeta.com/articles/146416.htm>

<div><a rel="nofollow" href="http://www.cnbeta.com/topics/443.htm"><img src="http://img.cnbeta.com/topics/chrome1.gif" alt="Google Chrome 谷歌浏览器" name="sign" align="right"></a>
        <p>“混合脚本执行”漏洞通常在通过 HTTPS 传送的页面加载通过 HTTP 传送的脚本、CSS 
或插件资源时出现。中间攻击者（如处于同一无线网络的用户）通常能截取 HTTP 资源加载，并对加载资源的网站获取完全权限。情况糟糕的话跟没用 HTTPS 
一个样。稍轻但类似的问题――暂且称之为“混合显示”漏洞――通常在通过 HTTPS 传送的页面加载通过 HTTP 传送的图像、iFrame 
框架或字体时出现，但只能影响页面外观。</p>
		<p>浏览器长期以来使用不同的指示符、情态动词对话框、拦截选项甚至是点击确认等方法将这些情况告知用户。如果网站页面存在混合脚本执行问题，Chromium 
当前会在 Omnibox 中这样指示：<p><a rel="nofollow" href="http://img.cnbeta.com/newsimg/110621/1054470233236417.png"><img alt="" src="http://img.cnbeta.com/newsimg/110621/1054470233236417.png" height="82" width="243"></a></p>
<p>混合显示内容会这样指示：</p>
<p><a rel="nofollow" href="http://img.cnbeta.com/newsimg/110621/10544811595798890.png"><img alt="" src="http://img.cnbeta.com/newsimg/110621/10544811595798890.png" height="82" width="243"></a></p>
<p>如果网站有 HTTPS 页面显示带红叉的 https，就非常值得主动调查了：</p>
<ul><li>网站在其他主流浏览器（如 IE9 或 FF4）中会因为点击确认对话框与丑陋的情态动词对话框而难以正常显示。 </li>
<li>可能存在危害整个 HTTPS 连接的安全漏洞。 </li>
</ul>
<p>自 Chromium 14 的首个版本 (14.0.785.0 canary) 起，Google 
将开始测试默认拦截混合脚本。当拦截到混合脚本时会出现这样的信息栏：</p>
<p><a rel="nofollow" href="http://www.guao.hk/wp-content/uploads/2011/06/blocked.png"><img alt="" src="http://img.cnbeta.com/newsimg/110621/1054482573162.png" height="71" width="550"></a></p>
<p>作为用户，可以选择不应用拦截而重新加载。理想情况下，将来的信息栏将不再保留允许用户绕过拦截的选项。Google 
安全团队的经验表明，有些用户即使在最可怕的警告信息面前也会点击“允许执行”――完全不顾可能的后果。</p>
<p><strong>帮助站长分析的工具</strong><br>
如果 Chromium 的 UI 显示网站上存在混合内容问题，可以尝试 Google 
的开发工具定位问题。有用的信息通常记录在 JavaScript 控制台 (菜单 -&gt; 工具 -&gt; JavaScript 控制台)：</p>
<p><a rel="nofollow" href="http://www.guao.hk/wp-content/uploads/2011/06/mixedscriptconsole.png"><img alt="" src="http://img.cnbeta.com/newsimg/110621/1054483219017177.png" height="290" width="550"></a></p>
<p>站长还可以打开“网络”选项卡重新加载页面，并查找通过 http:// 
协议传送的内容。值得注意的是，混合脚本得以执行时，整个原始页面都会受到影响，因此需要查看所有引用了发现的来源的标签页的控制台。要清除错误，所有引用了有害来源的标签页都要关闭。特别棘手的情况是不清楚来源如何产生危害，这时还可以<a rel="nofollow" href="http://www.chromium.org/for-testers/enable-logging">启用命令行控制台调试功能</a>查看相关的警告信息。</p>
<p>Chromium 13 
提供了命令行标记 <strong><code>--no-running-insecure-content</code></strong>。Google 
建议站长与高级用户附带该标记运行 
Chrome，以便清理有问题的网站。（还针对较轻级别的混合内容问题提供了 <strong><code>--no-displaying-insecure-content</code></strong>；Chromium 
14 尚无计划默认拦截此内容。）</p>
<p>而 Chromium 14 将提供相反的标记: <code>--allow-running-insecure-content</code>，方便使用未能及时修正这些错误的内部应用程序的用户与管理员。</p>
<p>via <a rel="nofollow" title="Trying to end mixed scripting vulnerabilities" href="http://googleonlinesecurity.blogspot.com/2011/06/trying-to-end-mixed-scripting.html">Google Online Security Blog</a></p>
<p><strong>编注：</strong>Chrome 14 已于上周部署到了 dev 
分支，拦截后的提示文字说明更加全面，并且去掉了“确定”按钮（与“关闭”图标功能重复），还提供了<a rel="nofollow" href="https://www.google.com/support/chrome/bin/answer.py?answer=1342714">“了解更多”链接</a>――不过帮助中心还没有准备好相应内容。</p>
<div><a rel="nofollow" href="http://www.guao.hk/wp-content/uploads/2011/06/script-blocked-infotip.png"><img alt="" src="http://img.cnbeta.com/newsimg/110621/1054484434218433.png" height="78" width="550"></a> <p>提示：“该站点的部分脚本内容不安全，为了您的安全已进行拦截。”按钮：“仍然加载（不推荐）”<br>
<br>
文/<a rel="nofollow" href="http://google.org.cn/">谷奥</a></p>
</div></p></div>