---
layout: post
title:  "Khronos 号召开发者参与 WebGL 1.0.1 一致性测试"
date:   2012-04-14 11:57:12
author: admin
categories: program
---

## Khronos 号召开发者参与 WebGL 1.0.1 一致性测试
### by admin
### at 2012-04-14 11:57:12
### original <http://www.hiwebgl.com/?p=1022>

<div><p>今天 Khronos 在 WebGL Public Wiki 上专门开辟页面，<a href="http://www.khronos.org/webgl/wiki/CrowdsourcingDriverTesting">号召开发者为 WebGL 1.0.1 一致性测试贡献数据</a>。页面中写道，WebGL 将 3D 图形带入到了 Web，然而为了实现这一目标，我们需要用户安装更好的图形驱动程序。WebGL 的一致性测试套件相当优秀，但是图形驱动程序中的一些 bug 却会导致测试失败。我们需要知道哪些驱动能够最接近于通过所有 WebGL 一致性测试。<strong>我们需要你的帮助！</strong></p>
<p>一致性测试是协议测试的一个重要方面，是性能测试、互操作性测试和健壮性测试的基础，是协议开发人员首要关心的问题，它测试协议的实现是否符合协议规范。正是因为一致性测试，我们现在才可以在 Chrome、Firefox、Safari 中能够无缝的运行和显示目前几乎所有的 WebGL 程序。</p>
<p>WebGL 工作组成员、Mozilla软件工程师同时也是著名的 WebGL 开发者 Benoit Jacob 在 WebGL 公共邮件列表中写到：“这一测试对于 WebGL 是否能够从 ‘experimental-webgl’ 顺利转换为 ‘webgl’ 是至关重要的一步。”</p>
<p>要参加一致性测试，请使用以下任意的桌面操作系统：Windows、Mac OS X、Linux，并遵循以下步骤：</p>
<ol>
<li>安装如下列出的最新的预览版浏览器。请不要使用旧版本浏览器进行测试，否则会干扰测试结果。</li>
<ul>
<li>Chrome <a href="http://www.chromium.org/getting-involved/dev-channel">Dev Channel </a>（当前版本号为 Chrome 20），或 <a href="http://tools.google.com/dlpage/chromesxs">Chrome Canary</a>。</li>
<li>Firefox 14，目前处于 <a href="http://nightly.mozilla.org/">Nightly Channel</a>。</li>
<li>Opera 12，目前处于 <a href="http://www.opera.com/browser/next/">Opera Next</a> 版本。</li>
<li>Safari，请使用当前的 <a href="http://nightly.webkit.org/">WebKit Nightly</a> 版本，然后<a href="http://www.hiwebgl.com/?p=628#Safari">按照指定步骤开启 WebGL 支持</a>。</li>
</ul>
<li>安装最新的图形驱动程序。</li>
<ul>
<li>非常欢迎大家安装 Beta 版的图形驱动，这会对测试帮助更大。</li>
</ul>
<li><a href="https://cvs.khronos.org/svn/repos/registry/trunk/public/webgl/conformance-suites/1.0.1/webgl-conformance-tests.html">点击这里进入 WebGL 1.0.1 一致性测试页面</a>，在页面中点击“run tests”按钮开始测试。当测试结束时，会出现一个写有“display text summary”按钮。点击这个按钮后会显示此次测试的文字摘要。</li>
<li>进入你的 E-mail 邮箱，新建一封邮件，将测试的文字摘要复制粘贴到邮件正文中，并附上你的显卡系统信息。要获得你的显卡系统信息，请：</li>
<ul>
<li>在 Chrome 或 Opera 中，在浏览器地址栏输入“about:gpu”并回车，进入并复制该页面中的信息。</li>
<li>在 Firefox 中，在浏览器地址栏中输入“about:support”并回车，进入该页面后找到图形区域，复制该区域的信息。</li>
</ul>
<li>发送邮件到 <a href="mailto:webgl-driver-testing@googlegroups.com">webgl-driver-testing@googlegroups.com</a> 。</li>
<ul>
<li>请使用一个摘要式的邮件主题，给出你的浏览器和显卡系统信息。例如：“Firefox 14, Windows 7 64bit, NVIDIA 295.67, Quadro FX 880M”</li>
</ul>
</ol>
<p> </p>
</div>