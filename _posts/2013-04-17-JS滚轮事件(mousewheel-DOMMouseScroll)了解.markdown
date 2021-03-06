---
layout: post
title:  "JS滚轮事件(mousewheel/DOMMouseScroll)了解"
date:   2013-04-17 01:29:27
author: 张 鑫旭
categories: program
---

## JS滚轮事件(mousewheel/DOMMouseScroll)了解
### by 张 鑫旭
### at 2013-04-17 01:29:27
### original <http://www.zhangxinxu.com/wordpress/2013/04/js-mousewheel-dommousescroll-event/>

<p>by <a href="http://www.zhangxinxu.com/">zhangxinxu</a> from <a href="http://www.zhangxinxu.com/">http://www.zhangxinxu.com</a><br>
本文地址：<a href="http://www.zhangxinxu.com/wordpress/?p=3175">http://www.zhangxinxu.com/wordpress/?p=3175</a></p>
<h3>一、学无止境、温故知新</h3>
<p><span>//zxx: 本段与技术无关，一些很个人的吐槽，可以跳过</span><br>
<img src="http://image.zhangxinxu.com/image/emtion/normal.png" width="150" height="150" alt="正常状态">已经没有了小学生时代过目不忘的记忆力了，很多自己折腾的东西、接触的东西，短短1年之后就全然不记得了。比方说，完全记不得获取元素与页面距离的方法(<a href="http://www.zhangxinxu.com/wordpress/?p=1907">getBoundingClientRect</a>)，或者是不记得现代浏览器下触发DOM自定义事件的方法(<a href="http://www.zhangxinxu.com/wordpress/?p=2330">dispatchEvent</a>). 显然，适当的温习，翻阅以前的东西，或者自己空余时间处理相关的东西还是有必要的。其实，细想，东西记不住是自己自身原因，在折腾的时候就没有想方设法牢记（而不是通过反复使用记住）。比方说<code>getBoundingClientRect</code>就是“得到客户端矩形边界”的意思，或者使用邪恶记法记住“割(<code>g</code>)逼(<code>b</code>)艹(<code>c</code>)软(<code>r</code>)”。<code>dispatchEvent</code>方法使用“<strong>3</strong>步走”，“创建(<code>createEvent</code>)-初始(<code>init*Event</code>)-分派(<code>dispatchEvent</code>)”。</p>
<p>学习的脚步不能停止。一站到底的那些“变态”们也有不知道的东西，显然，我们这些草辈，尤其年轻的自己，不知道的更多。谁年轻的时候没有过或多或少的迷茫，问自己“路在何方”，问自己“该做哪个方向”，无论你选择的是什么，学习的脚步是不能停止的。坚持着坚持着，路自然就会清晰，你就会知道接下来该怎么走了。只怕畏首畏尾，得过且过，年轻就是资本，义无反顾前行吧。</p>
<p>我凭着兴趣走上现在的道路，完全是兴趣学习（我喜欢这些，我要学），不是职业学习（做前端需要什么，我就去学什么）。工作的这些年，技术、产品的自我沉浸不知不觉限制了自己的眼界，好在意识到问题的存在其实已经解决了问题的一半。这里之所以会说这些是想提醒自己，万万不可矫枉过正，技术、产品的学习还是主要的，只是要多多抬头看看办公室之外的世界（不是刷微博获得的浅认识）。</p>
<p>昨天机缘巧合遇到“滚轮事件”，以前折腾“<a href="http://www.zhangxinxu.com/wordpress/?p=1597">自定义滚动条</a>”时候使用过鼠标滚轮事件，不过这是基于MooTools已经兼容好的<code>mousewheel</code>事件实现的，如果要说出其中的实现机制，浏览器兼容差异等，就傻眼了。学无止境，因此，查阅之，实践之，整理之。</p>
<h3>二、兼容差异大全</h3>
<p>滚轮事件的兼容性差异有些不拘一格，不是以往的IE8-派和其他派，而是FireFox派和其他派。</p>
<p>包括IE6在内的浏览器是使用<code>onmousewheel</code>，而FireFox浏览器一个人使用<code>DOMMouseScroll</code>. 经自己测试，即使现在FireFox 19下，也是不识<code>onmousewheel</code>。</p>
<p>一个最简单的使用差异（<code>body</code>滚动条由内部一定高<code>div</code>撑开）：</p>
<div>
<pre>document.body.<span style="color:#cd0000">onmousewheel</span> = function(event) {
    event = event || window.event;
    console.dir(event);	
};</pre>
</div>
<div>
<pre>document.body.addEventListener(<span style="color:#cd0000">"DOMMouseScroll"</span>, function(event) {
    console.dir(event);	
});</pre>
</div>
<p>以上输出差异见下面（IE7, IE10, Chrome, 以及FireFox，鼠标向下滚动， win7）（可<a href="http://www.zhangxinxu.com/study/201304/mousewheel-dommousescroll-same-different.html">点击此页面</a>单独查看表格内容）：</p>
<table cellspacing="1" cellpadding="0" border="0" width="100%">
<tbody>
<tr>
<th>属性名\浏览器</th>
<th>FireFox</th>
<th>Chrome</th>
<th>IE10</th>
<th>IE7</th>
</tr>
<tr>
<th>recordset</th>
<td><span style="color:#999">×没有该属性</span></td>
<td><span style="color:#999">×没有该属性</span></td>
<td><span style="color:#999">×没有该属性</span></td>
<td>null</td>
</tr>
<tr>
<th>type</th>
<td>DOMMouseScroll</td>
<td>mousewheel</td>
<td>mousewheel</td>
<td>mousewheel</td>
</tr>
<tr>
<th>fromElement</th>
<td><span style="color:#999">×没有该属性</span></td>
<td>null</td>
<td>null</td>
<td>null</td>
</tr>
<tr>
<th>toElement</th>
<td><span style="color:#999">×没有该属性</span></td>
<td>[object HTMLDivElement]</td>
<td>null</td>
<td>null</td>
</tr>
<tr>
<th>altLeft</th>
<td><span style="color:#999">×没有该属性</span></td>
<td><span style="color:#999">×没有该属性</span></td>
<td><span style="color:#999">×没有该属性</span></td>
<td>false</td>
</tr>
<tr>
<th>keyCode</th>
<td><span style="color:#999">×没有该属性</span></td>
<td>0</td>
<td><span style="color:#999">×没有该属性</span></td>
<td>0</td>
</tr>
<tr>
<th>repeat</th>
<td><span style="color:#999">×没有该属性</span></td>
<td><span style="color:#999">×没有该属性</span></td>
<td><span style="color:#999">×没有该属性</span></td>
<td>false</td>
</tr>
<tr>
<th>reason</th>
<td><span style="color:#999">×没有该属性</span></td>
<td><span style="color:#999">×没有该属性</span></td>
<td><span style="color:#999">×没有该属性</span></td>
<td>0</td>
</tr>
<tr>
<th>data</th>
<td><span style="color:#999">×没有该属性</span></td>
<td><span style="color:#999">×没有该属性</span></td>
<td><span style="color:#999">×没有该属性</span></td>
<td>空字符串</td>
</tr>
<tr>
<th>behaviorCookie</th>
<td><span style="color:#999">×没有该属性</span></td>
<td><span style="color:#999">×没有该属性</span></td>
<td><span style="color:#999">×没有该属性</span></td>
<td>0</td>
</tr>
<tr>
<th>source</th>
<td><span style="color:#999">×没有该属性</span></td>
<td><span style="color:#999">×没有该属性</span></td>
<td><span style="color:#999">×没有该属性</span></td>
<td>null</td>
</tr>
<tr>
<th>contentOverflow</th>
<td><span style="color:#999">×没有该属性</span></td>
<td><span style="color:#999">×没有该属性</span></td>
<td><span style="color:#999">×没有该属性</span></td>
<td>false</td>
</tr>
<tr>
<th>behaviorPart</th>
<td><span style="color:#999">×没有该属性</span></td>
<td><span style="color:#999">×没有该属性</span></td>
<td><span style="color:#999">×没有该属性</span></td>
<td>0</td>
</tr>
<tr>
<th>url</th>
<td><span style="color:#999">×没有该属性</span></td>
<td><span style="color:#999">×没有该属性</span></td>
<td><span style="color:#999">×没有该属性</span></td>
<td>空字符串</td>
</tr>
<tr>
<th>dataTransfer</th>
<td><span style="color:#999">×没有该属性</span></td>
<td>null</td>
<td><span style="color:#999">×没有该属性</span></td>
<td>null</td>
</tr>
<tr>
<th>ctrlKey</th>
<td>false</td>
<td>false</td>
<td>false</td>
<td>false</td>
</tr>
<tr>
<th>shiftLeft</th>
<td><span style="color:#999">×没有该属性</span></td>
<td><span style="color:#999">×没有该属性</span></td>
<td><span style="color:#999">×没有该属性</span></td>
<td>false</td>
</tr>
<tr>
<th>dataFld</th>
<td><span style="color:#999">×没有该属性</span></td>
<td><span style="color:#999">×没有该属性</span></td>
<td><span style="color:#999">×没有该属性</span></td>
<td>空字符串</td>
</tr>
<tr>
<th>returnValue</th>
<td><span style="color:#999">×没有该属性</span></td>
<td>true</td>
<td><span style="color:#999">×没有该属性</span></td>
<td>undefined</td>
</tr>
<tr>
<th>qualifier</th>
<td><span style="color:#999">×没有该属性</span></td>
<td><span style="color:#999">×没有该属性</span></td>
<td><span style="color:#999">×没有该属性</span></td>
<td>空字符串</td>
</tr>
<tr>
<th>wheelDelta</th>
<td><span style="color:#999">×没有该属性</span></td>
<td>-120</td>
<td>-120</td>
<td>-120</td>
</tr>
<tr>
<th>bookmarks</th>
<td><span style="color:#999">×没有该属性</span></td>
<td><span style="color:#999">×没有该属性</span></td>
<td><span style="color:#999">×没有该属性</span></td>
<td>null</td>
</tr>
<tr>
<th>actionURL</th>
<td><span style="color:#999">×没有该属性</span></td>
<td><span style="color:#999">×没有该属性</span></td>
<td><span style="color:#999">×没有该属性</span></td>
<td>空字符串</td>
</tr>
<tr>
<th>button</th>
<td>0</td>
<td>0</td>
<td>0</td>
<td>0</td>
</tr>
<tr>
<th>srcFilter</th>
<td><span style="color:#999">×没有该属性</span></td>
<td><span style="color:#999">×没有该属性</span></td>
<td><span style="color:#999">×没有该属性</span></td>
<td>null</td>
</tr>
<tr>
<th>nextPage</th>
<td><span style="color:#999">×没有该属性</span></td>
<td><span style="color:#999">×没有该属性</span></td>
<td><span style="color:#999">×没有该属性</span></td>
<td>空字符串</td>
</tr>
<tr>
<th>cancelBubble</th>
<td>false</td>
<td>false</td>
<td>false</td>
<td>false</td>
</tr>
<tr>
<th>x</th>
<td><span style="color:#999">×没有该属性</span></td>
<td>799</td>
<td>876</td>
<td>839</td>
</tr>
<tr>
<th>y</th>
<td><span style="color:#999">×没有该属性</span></td>
<td>283</td>
<td>322</td>
<td>325</td>
</tr>
<tr>
<th>buttonID</th>
<td><span style="color:#999">×没有该属性</span></td>
<td><span style="color:#999">×没有该属性</span></td>
<td><span style="color:#999">×没有该属性</span></td>
<td>0</td>
</tr>
<tr>
<th>srcElement</th>
<td><span style="color:#999">×没有该属性</span></td>
<td>[object HTMLDivElement]</td>
<td>[object HTMLDivElement]</td>
<td>[object]</td>
</tr>
<tr>
<th>screenX</th>
<td>934</td>
<td>799</td>
<td>876</td>
<td>841</td>
</tr>
<tr>
<th>screenY</th>
<td>453</td>
<td>344</td>
<td>377</td>
<td>382</td>
</tr>
<tr>
<th>srcUrn</th>
<td><span style="color:#999">×没有该属性</span></td>
<td><span style="color:#999">×没有该属性</span></td>
<td><span style="color:#999">×没有该属性</span></td>
<td>空字符串</td>
</tr>
<tr>
<th>origin</th>
<td><span style="color:#999">×没有该属性</span></td>
<td><span style="color:#999">×没有该属性</span></td>
<td><span style="color:#999">×没有该属性</span></td>
<td>空字符串</td>
</tr>
<tr>
<th>boundElements</th>
<td><span style="color:#999">×没有该属性</span></td>
<td><span style="color:#999">×没有该属性</span></td>
<td><span style="color:#999">×没有该属性</span></td>
<td>[object]</td>
</tr>
<tr>
<th>clientX</th>
<td>1168</td>
<td>799</td>
<td>876</td>
<td>841</td>
</tr>
<tr>
<th>clientY</th>
<td>456</td>
<td>283</td>
<td>322</td>
<td>327</td>
</tr>
<tr>
<th>propertyName</th>
<td><span style="color:#999">×没有该属性</span></td>
<td><span style="color:#999">×没有该属性</span></td>
<td><span style="color:#999">×没有该属性</span></td>
<td>空字符串</td>
</tr>
<tr>
<th>shiftKey</th>
<td>false</td>
<td>false</td>
<td>false</td>
<td>false</td>
</tr>
<tr>
<th>ctrlLeft</th>
<td><span style="color:#999">×没有该属性</span></td>
<td><span style="color:#999">×没有该属性</span></td>
<td><span style="color:#999">×没有该属性</span></td>
<td>false</td>
</tr>
<tr>
<th>offsetX</th>
<td><span style="color:#999">×没有该属性</span></td>
<td>791</td>
<td>868</td>
<td>829</td>
</tr>
<tr>
<th>offsetY</th>
<td><span style="color:#999">×没有该属性</span></td>
<td>275</td>
<td>314</td>
<td>310</td>
</tr>
<tr>
<th>altKey</th>
<td>false</td>
<td>false</td>
<td>false</td>
<td>false</td>
</tr>
<tr>
<th>initMouseWheelEvent</th>
<td><span style="color:#999">×没有该属性</span></td>
<td><span style="color:#999">×没有该属性</span></td>
<td> function initMouseWheelEvent() { [native code] } </td>
<td><span style="color:#999">×没有该属性</span></td>
</tr>
<tr>
<th>layerX</th>
<td>1168</td>
<td>799</td>
<td>876</td>
<td><span style="color:#999">×没有该属性</span></td>
</tr>
<tr>
<th>layerY</th>
<td>456</td>
<td>283</td>
<td>322</td>
<td><span style="color:#999">×没有该属性</span></td>
</tr>
<tr>
<th>which</th>
<td>1</td>
<td>1</td>
<td>1</td>
<td><span style="color:#999">×没有该属性</span></td>
</tr>
<tr>
<th>buttons</th>
<td>0</td>
<td><span style="color:#999">×没有该属性</span></td>
<td>0</td>
<td><span style="color:#999">×没有该属性</span></td>
</tr>
<tr>
<th>metaKey</th>
<td>false</td>
<td>false</td>
<td>false</td>
<td><span style="color:#999">×没有该属性</span></td>
</tr>
<tr>
<th>pageX</th>
<td>1168</td>
<td>799</td>
<td>876</td>
<td><span style="color:#999">×没有该属性</span></td>
</tr>
<tr>
<th>pageY</th>
<td>456</td>
<td>283</td>
<td>322</td>
<td><span style="color:#999">×没有该属性</span></td>
</tr>
<tr>
<th>relatedTarget</th>
<td>null</td>
<td>null</td>
<td>null</td>
<td><span style="color:#999">×没有该属性</span></td>
</tr>
<tr>
<th>getModifierState</th>
<td>function getModifierState() { [native code] }</td>
<td><span style="color:#999">×没有该属性</span></td>
<td> function getModifierState() { [native code] } </td>
<td><span style="color:#999">×没有该属性</span></td>
</tr>
<tr>
<th>initMouseEvent</th>
<td>function initMouseEvent() { [native code] }</td>
<td>function initMouseEvent() { [native code] }</td>
<td> function initMouseEvent() { [native code] } </td>
<td><span style="color:#999">×没有该属性</span></td>
</tr>
<tr>
<th>detail</th>
<td>3</td>
<td>0</td>
<td>0</td>
<td><span style="color:#999">×没有该属性</span></td>
</tr>
<tr>
<th>view</th>
<td>[object Window]</td>
<td>[object Window]</td>
<td>[object Window]</td>
<td><span style="color:#999">×没有该属性</span></td>
</tr>
<tr>
<th>initUIEvent</th>
<td>function initUIEvent() { [native code] }</td>
<td>function initUIEvent() { [native code] }</td>
<td> function initUIEvent() { [native code] } </td>
<td><span style="color:#999">×没有该属性</span></td>
</tr>
<tr>
<th>bubbles</th>
<td>true</td>
<td>true</td>
<td>true</td>
<td><span style="color:#999">×没有该属性</span></td>
</tr>
<tr>
<th>cancelable</th>
<td>true</td>
<td>true</td>
<td>true</td>
<td><span style="color:#999">×没有该属性</span></td>
</tr>
<tr>
<th>currentTarget</th>
<td>[object HTMLBodyElement]</td>
<td>[object HTMLBodyElement]</td>
<td>[object HTMLBodyElement]</td>
<td><span style="color:#999">×没有该属性</span></td>
</tr>
<tr>
<th>defaultPrevented</th>
<td>false</td>
<td>false</td>
<td>false</td>
<td><span style="color:#999">×没有该属性</span></td>
</tr>
<tr>
<th>eventPhase</th>
<td>3</td>
<td>3</td>
<td>3</td>
<td><span style="color:#999">×没有该属性</span></td>
</tr>
<tr>
<th>isTrusted</th>
<td>true</td>
<td><span style="color:#999">×没有该属性</span></td>
<td>true</td>
<td><span style="color:#999">×没有该属性</span></td>
</tr>
<tr>
<th>target</th>
<td>[object HTMLDivElement]</td>
<td>[object HTMLDivElement]</td>
<td>[object HTMLDivElement]</td>
<td><span style="color:#999">×没有该属性</span></td>
</tr>
<tr>
<th>timeStamp</th>
<td>14296937</td>
<td>1366106275177</td>
<td>1366106216522</td>
<td><span style="color:#999">×没有该属性</span></td>
</tr>
<tr>
<th>initEvent</th>
<td>function initEvent() { [native code] }</td>
<td>function initEvent() { [native code] }</td>
<td> function initEvent() { [native code] } </td>
<td><span style="color:#999">×没有该属性</span></td>
</tr>
<tr>
<th>preventDefault</th>
<td>function preventDefault() { [native code] }</td>
<td>function preventDefault() { [native code] }</td>
<td> function preventDefault() { [native code] } </td>
<td><span style="color:#999">×没有该属性</span></td>
</tr>
<tr>
<th>stopImmediate<br>Propagation</th>
<td>function stopImmediate<br>Propagation() { [native code] }</td>
<td>function stopImmediate<br>Propagation() { [native code] }</td>
<td> function stopImmediate<br>Propagation() { [native code] } </td>
<td><span style="color:#999">×没有该属性</span></td>
</tr>
<tr>
<th>stopPropagation</th>
<td>function stopPropagation() { [native code] }</td>
<td>function stopPropagation() { [native code] }</td>
<td> function stopPropagation() { [native code] } </td>
<td><span style="color:#999">×没有该属性</span></td>
</tr>
<tr>
<th>AT_TARGET</th>
<td>2</td>
<td>2</td>
<td>2</td>
<td><span style="color:#999">×没有该属性</span></td>
</tr>
<tr>
<th>BUBBLING_PHASE</th>
<td>3</td>
<td>3</td>
<td>3</td>
<td><span style="color:#999">×没有该属性</span></td>
</tr>
<tr>
<th>CAPTURING_PHASE</th>
<td>1</td>
<td>1</td>
<td>1</td>
<td><span style="color:#999">×没有该属性</span></td>
</tr>
<tr>
<th>webkitDirection<br>InvertedFromDevice</th>
<td><span style="color:#999">×没有该属性</span></td>
<td>false</td>
<td><span style="color:#999">×没有该属性</span></td>
<td><span style="color:#999">×没有该属性</span></td>
</tr>
<tr>
<th>wheelDeltaY</th>
<td><span style="color:#999">×没有该属性</span></td>
<td>-120</td>
<td><span style="color:#999">×没有该属性</span></td>
<td><span style="color:#999">×没有该属性</span></td>
</tr>
<tr>
<th>wheelDeltaX</th>
<td><span style="color:#999">×没有该属性</span></td>
<td>0</td>
<td><span style="color:#999">×没有该属性</span></td>
<td><span style="color:#999">×没有该属性</span></td>
</tr>
<tr>
<th>webkitMovementY</th>
<td><span style="color:#999">×没有该属性</span></td>
<td>0</td>
<td><span style="color:#999">×没有该属性</span></td>
<td><span style="color:#999">×没有该属性</span></td>
</tr>
<tr>
<th>webkitMovementX</th>
<td><span style="color:#999">×没有该属性</span></td>
<td>0</td>
<td><span style="color:#999">×没有该属性</span></td>
<td><span style="color:#999">×没有该属性</span></td>
</tr>
<tr>
<th>charCode</th>
<td><span style="color:#999">×没有该属性</span></td>
<td>0</td>
<td><span style="color:#999">×没有该属性</span></td>
<td><span style="color:#999">×没有该属性</span></td>
</tr>
<tr>
<th>clipboardData</th>
<td><span style="color:#999">×没有该属性</span></td>
<td>undefined</td>
<td><span style="color:#999">×没有该属性</span></td>
<td><span style="color:#999">×没有该属性</span></td>
</tr>
<tr>
<th>initWebKitWheelEvent</th>
<td><span style="color:#999">×没有该属性</span></td>
<td>function initWebKitWheelEvent() { [native code] }</td>
<td><span style="color:#999">×没有该属性</span></td>
<td><span style="color:#999">×没有该属性</span></td>
</tr>
<tr>
<th>NONE</th>
<td>0</td>
<td>0</td>
<td><span style="color:#999">×没有该属性</span></td>
<td><span style="color:#999">×没有该属性</span></td>
</tr>
<tr>
<th>MOUSEDOWN</th>
<td>1</td>
<td>1</td>
<td><span style="color:#999">×没有该属性</span></td>
<td><span style="color:#999">×没有该属性</span></td>
</tr>
<tr>
<th>MOUSEUP</th>
<td>2</td>
<td>2</td>
<td><span style="color:#999">×没有该属性</span></td>
<td><span style="color:#999">×没有该属性</span></td>
</tr>
<tr>
<th>MOUSEOVER</th>
<td>4</td>
<td>4</td>
<td><span style="color:#999">×没有该属性</span></td>
<td><span style="color:#999">×没有该属性</span></td>
</tr>
<tr>
<th>MOUSEOUT</th>
<td>8</td>
<td>8</td>
<td><span style="color:#999">×没有该属性</span></td>
<td><span style="color:#999">×没有该属性</span></td>
</tr>
<tr>
<th>MOUSEMOVE</th>
<td>16</td>
<td>16</td>
<td><span style="color:#999">×没有该属性</span></td>
<td><span style="color:#999">×没有该属性</span></td>
</tr>
<tr>
<th>MOUSEDRAG</th>
<td>32</td>
<td>32</td>
<td><span style="color:#999">×没有该属性</span></td>
<td><span style="color:#999">×没有该属性</span></td>
</tr>
<tr>
<th>CLICK</th>
<td>64</td>
<td>64</td>
<td><span style="color:#999">×没有该属性</span></td>
<td><span style="color:#999">×没有该属性</span></td>
</tr>
<tr>
<th>DBLCLICK</th>
<td>128</td>
<td>128</td>
<td><span style="color:#999">×没有该属性</span></td>
<td><span style="color:#999">×没有该属性</span></td>
</tr>
<tr>
<th>KEYDOWN</th>
<td>256</td>
<td>256</td>
<td><span style="color:#999">×没有该属性</span></td>
<td><span style="color:#999">×没有该属性</span></td>
</tr>
<tr>
<th>KEYUP</th>
<td>512</td>
<td>512</td>
<td><span style="color:#999">×没有该属性</span></td>
<td><span style="color:#999">×没有该属性</span></td>
</tr>
<tr>
<th>KEYPRESS</th>
<td>1024</td>
<td>1024</td>
<td><span style="color:#999">×没有该属性</span></td>
<td><span style="color:#999">×没有该属性</span></td>
</tr>
<tr>
<th>DRAGDROP</th>
<td>2048</td>
<td>2048</td>
<td><span style="color:#999">×没有该属性</span></td>
<td><span style="color:#999">×没有该属性</span></td>
</tr>
<tr>
<th>FOCUS</th>
<td>4096</td>
<td>4096</td>
<td><span style="color:#999">×没有该属性</span></td>
<td><span style="color:#999">×没有该属性</span></td>
</tr>
<tr>
<th>BLUR</th>
<td>8192</td>
<td>8192</td>
<td><span style="color:#999">×没有该属性</span></td>
<td><span style="color:#999">×没有该属性</span></td>
</tr>
<tr>
<th>SELECT</th>
<td>16384</td>
<td>16384</td>
<td><span style="color:#999">×没有该属性</span></td>
<td><span style="color:#999">×没有该属性</span></td>
</tr>
<tr>
<th>CHANGE</th>
<td>32768</td>
<td>32768</td>
<td><span style="color:#999">×没有该属性</span></td>
<td><span style="color:#999">×没有该属性</span></td>
</tr>
<tr>
<th>rangeParent</th>
<td>[object HTMLDivElement]</td>
<td><span style="color:#999">×没有该属性</span></td>
<td><span style="color:#999">×没有该属性</span></td>
<td><span style="color:#999">×没有该属性</span></td>
</tr>
<tr>
<th>rangeOffset</th>
<td>0</td>
<td><span style="color:#999">×没有该属性</span></td>
<td><span style="color:#999">×没有该属性</span></td>
<td><span style="color:#999">×没有该属性</span></td>
</tr>
<tr>
<th>isChar</th>
<td>false</td>
<td><span style="color:#999">×没有该属性</span></td>
<td><span style="color:#999">×没有该属性</span></td>
<td><span style="color:#999">×没有该属性</span></td>
</tr>
<tr>
<th>mozMovementX</th>
<td>1168</td>
<td><span style="color:#999">×没有该属性</span></td>
<td><span style="color:#999">×没有该属性</span></td>
<td><span style="color:#999">×没有该属性</span></td>
</tr>
<tr>
<th>mozMovementY</th>
<td>576</td>
<td><span style="color:#999">×没有该属性</span></td>
<td><span style="color:#999">×没有该属性</span></td>
<td><span style="color:#999">×没有该属性</span></td>
</tr>
<tr>
<th>mozPressure</th>
<td>0</td>
<td><span style="color:#999">×没有该属性</span></td>
<td><span style="color:#999">×没有该属性</span></td>
<td><span style="color:#999">×没有该属性</span></td>
</tr>
<tr>
<th>mozInputSource</th>
<td>1</td>
<td><span style="color:#999">×没有该属性</span></td>
<td><span style="color:#999">×没有该属性</span></td>
<td><span style="color:#999">×没有该属性</span></td>
</tr>
<tr>
<th>initNSMouseEvent</th>
<td>function initNSMouseEvent() { [native code] }</td>
<td><span style="color:#999">×没有该属性</span></td>
<td><span style="color:#999">×没有该属性</span></td>
<td><span style="color:#999">×没有该属性</span></td>
</tr>
<tr>
<th>axis</th>
<td>2</td>
<td><span style="color:#999">×没有该属性</span></td>
<td><span style="color:#999">×没有该属性</span></td>
<td><span style="color:#999">×没有该属性</span></td>
</tr>
<tr>
<th>initMouseScrollEvent</th>
<td>function initMouseScrollEvent() { [native code] }</td>
<td><span style="color:#999">×没有该属性</span></td>
<td><span style="color:#999">×没有该属性</span></td>
<td><span style="color:#999">×没有该属性</span></td>
</tr>
<tr>
<th>originalTarget</th>
<td>[object HTMLDivElement]</td>
<td><span style="color:#999">×没有该属性</span></td>
<td><span style="color:#999">×没有该属性</span></td>
<td><span style="color:#999">×没有该属性</span></td>
</tr>
<tr>
<th>explicitOriginalTarget</th>
<td>[object HTMLDivElement]</td>
<td><span style="color:#999">×没有该属性</span></td>
<td><span style="color:#999">×没有该属性</span></td>
<td><span style="color:#999">×没有该属性</span></td>
</tr>
<tr>
<th>preventBubble</th>
<td>function preventBubble() { [native code] }</td>
<td><span style="color:#999">×没有该属性</span></td>
<td><span style="color:#999">×没有该属性</span></td>
<td><span style="color:#999">×没有该属性</span></td>
</tr>
<tr>
<th>preventCapture</th>
<td>function preventCapture() { [native code] }</td>
<td><span style="color:#999">×没有该属性</span></td>
<td><span style="color:#999">×没有该属性</span></td>
<td><span style="color:#999">×没有该属性</span></td>
</tr>
<tr>
<th>getPreventDefault</th>
<td>function getPreventDefault() { [native code] }</td>
<td><span style="color:#999">×没有该属性</span></td>
<td><span style="color:#999">×没有该属性</span></td>
<td><span style="color:#999">×没有该属性</span></td>
</tr>
<tr>
<th>RESET</th>
<td>65536</td>
<td><span style="color:#999">×没有该属性</span></td>
<td><span style="color:#999">×没有该属性</span></td>
<td><span style="color:#999">×没有该属性</span></td>
</tr>
<tr>
<th>SUBMIT</th>
<td>131072</td>
<td><span style="color:#999">×没有该属性</span></td>
<td><span style="color:#999">×没有该属性</span></td>
<td><span style="color:#999">×没有该属性</span></td>
</tr>
<tr>
<th>SCROLL</th>
<td>262144</td>
<td><span style="color:#999">×没有该属性</span></td>
<td><span style="color:#999">×没有该属性</span></td>
<td><span style="color:#999">×没有该属性</span></td>
</tr>
<tr>
<th>LOAD</th>
<td>524288</td>
<td><span style="color:#999">×没有该属性</span></td>
<td><span style="color:#999">×没有该属性</span></td>
<td><span style="color:#999">×没有该属性</span></td>
</tr>
<tr>
<th>UNLOAD</th>
<td>1048576</td>
<td><span style="color:#999">×没有该属性</span></td>
<td><span style="color:#999">×没有该属性</span></td>
<td><span style="color:#999">×没有该属性</span></td>
</tr>
<tr>
<th>XFER_DONE</th>
<td>2097152</td>
<td><span style="color:#999">×没有该属性</span></td>
<td><span style="color:#999">×没有该属性</span></td>
<td><span style="color:#999">×没有该属性</span></td>
</tr>
<tr>
<th>ABORT</th>
<td>4194304</td>
<td><span style="color:#999">×没有该属性</span></td>
<td><span style="color:#999">×没有该属性</span></td>
<td><span style="color:#999">×没有该属性</span></td>
</tr>
<tr>
<th>ERROR</th>
<td>8388608</td>
<td><span style="color:#999">×没有该属性</span></td>
<td><span style="color:#999">×没有该属性</span></td>
<td><span style="color:#999">×没有该属性</span></td>
</tr>
<tr>
<th>LOCATE</th>
<td>16777216</td>
<td><span style="color:#999">×没有该属性</span></td>
<td><span style="color:#999">×没有该属性</span></td>
<td><span style="color:#999">×没有该属性</span></td>
</tr>
<tr>
<th>MOVE</th>
<td>33554432</td>
<td><span style="color:#999">×没有该属性</span></td>
<td><span style="color:#999">×没有该属性</span></td>
<td><span style="color:#999">×没有该属性</span></td>
</tr>
<tr>
<th>RESIZE</th>
<td>67108864</td>
<td><span style="color:#999">×没有该属性</span></td>
<td><span style="color:#999">×没有该属性</span></td>
<td><span style="color:#999">×没有该属性</span></td>
</tr>
<tr>
<th>FORWARD</th>
<td>134217728</td>
<td><span style="color:#999">×没有该属性</span></td>
<td><span style="color:#999">×没有该属性</span></td>
<td><span style="color:#999">×没有该属性</span></td>
</tr>
<tr>
<th>HELP</th>
<td>268435456</td>
<td><span style="color:#999">×没有该属性</span></td>
<td><span style="color:#999">×没有该属性</span></td>
<td><span style="color:#999">×没有该属性</span></td>
</tr>
<tr>
<th>BACK</th>
<td>536870912</td>
<td><span style="color:#999">×没有该属性</span></td>
<td><span style="color:#999">×没有该属性</span></td>
<td><span style="color:#999">×没有该属性</span></td>
</tr>
<tr>
<th>TEXT</th>
<td>1073741824</td>
<td><span style="color:#999">×没有该属性</span></td>
<td><span style="color:#999">×没有该属性</span></td>
<td><span style="color:#999">×没有该属性</span></td>
</tr>
<tr>
<th>ALT_MASK</th>
<td>1</td>
<td><span style="color:#999">×没有该属性</span></td>
<td><span style="color:#999">×没有该属性</span></td>
<td><span style="color:#999">×没有该属性</span></td>
</tr>
<tr>
<th>CONTROL_MASK</th>
<td>2</td>
<td><span style="color:#999">×没有该属性</span></td>
<td><span style="color:#999">×没有该属性</span></td>
<td><span style="color:#999">×没有该属性</span></td>
</tr>
<tr>
<th>SHIFT_MASK</th>
<td>4</td>
<td><span style="color:#999">×没有该属性</span></td>
<td><span style="color:#999">×没有该属性</span></td>
<td><span style="color:#999">×没有该属性</span></td>
</tr>
<tr>
<th>META_MASK</th>
<td>8</td>
<td><span style="color:#999">×没有该属性</span></td>
<td><span style="color:#999">×没有该属性</span></td>
<td><span style="color:#999">×没有该属性</span></td>
</tr>
<tr>
<th>SCROLL_PAGE_UP</th>
<td>-32768</td>
<td><span style="color:#999">×没有该属性</span></td>
<td><span style="color:#999">×没有该属性</span></td>
<td><span style="color:#999">×没有该属性</span></td>
</tr>
<tr>
<th>SCROLL_PAGE_DOWN</th>
<td>32768</td>
<td><span style="color:#999">×没有该属性</span></td>
<td><span style="color:#999">×没有该属性</span></td>
<td><span style="color:#999">×没有该属性</span></td>
</tr>
<tr>
<th style="font-size:12px">MOZ_SOURCE_UNKNOWN</th>
<td>0</td>
<td><span style="color:#999">×没有该属性</span></td>
<td><span style="color:#999">×没有该属性</span></td>
<td><span style="color:#999">×没有该属性</span></td>
</tr>
<tr>
<th style="font-size:12px">MOZ_SOURCE_MOUSE</th>
<td>1</td>
<td><span style="color:#999">×没有该属性</span></td>
<td><span style="color:#999">×没有该属性</span></td>
<td><span style="color:#999">×没有该属性</span></td>
</tr>
<tr>
<th style="font-size:12px">MOZ_SOURCE_PEN</th>
<td>2</td>
<td><span style="color:#999">×没有该属性</span></td>
<td><span style="color:#999">×没有该属性</span></td>
<td><span style="color:#999">×没有该属性</span></td>
</tr>
<tr>
<th style="font-size:12px">MOZ_SOURCE_ERASER</th>
<td>3</td>
<td><span style="color:#999">×没有该属性</span></td>
<td><span style="color:#999">×没有该属性</span></td>
<td><span style="color:#999">×没有该属性</span></td>
</tr>
<tr>
<th style="font-size:12px">MOZ_SOURCE_CURSOR</th>
<td>4</td>
<td><span style="color:#999">×没有该属性</span></td>
<td><span style="color:#999">×没有该属性</span></td>
<td><span style="color:#999">×没有该属性</span></td>
</tr>
<tr>
<th style="font-size:12px">MOZ_SOURCE_TOUCH</th>
<td>5</td>
<td><span style="color:#999">×没有该属性</span></td>
<td><span style="color:#999">×没有该属性</span></td>
<td><span style="color:#999">×没有该属性</span></td>
</tr>
<tr>
<th style="font-size:12px">MOZ_SOURCE_KEYBOARD</th>
<td>6</td>
<td><span style="color:#999">×没有该属性</span></td>
<td><span style="color:#999">×没有该属性</span></td>
<td><span style="color:#999">×没有该属性</span></td>
</tr>
<tr>
<th style="font-size:12px">HORIZONTAL_AXIS</th>
<td>1</td>
<td><span style="color:#999">×没有该属性</span></td>
<td><span style="color:#999">×没有该属性</span></td>
<td><span style="color:#999">×没有该属性</span></td>
</tr>
<tr>
<th style="font-size:12px">VERTICAL_AXIS</th>
<td>2</td>
<td><span style="color:#999">×没有该属性</span></td>
<td><span style="color:#999">×没有该属性</span></td>
<td><span style="color:#999">×没有该属性</span></td>
</tr>
</tbody>
</table>
<p>对照表格内容，可以看到，鼠标滚动事件与点击事件有很多类似的地方。比方说兼容部分：<code>event.type</code>, <code>event.screenX/event.screenY</code>, <code>event.clientX/event.clientY</code>, <code>event.altKey</code>, <code>event.shiftKey</code>, <code>event.cancelBubble</code>都是一样的，不兼容的部分，IE6-8的<code>event.srcElement</code>与其他浏览器的<code>event.target</code>. </p>
<p>进口的苹果分外甜，滚轮事件显然也是有额外的差异的，想想也知道，是与滚轮相关的，也是我们实际应用最常用的。</p>
<p>在除了FireFox之外的浏览器下，滚动的上下滚动与否是下面这个-<code>event.wheelDelta</code>（<span style="color:#999">//zxx: 本文发布后补充：Delta读音对应希腊字母△，形状就像三角裤，因此，wheelDelta可以记做“滚轮的三角裤”</span>）：<br>
<img src="http://image.zhangxinxu.com/image/blog/201304/2013-04-16_191959.png" width="601" height="65" alt="event.wheelDelta与滚动示意"></p>
<p>根据<a href="http://www.zhangxinxu.com/study/201304/js-mousewheel-or-dommousescroll.html">自己的测试</a>，在我的win7系统下，无论IE7, IE10, Opera12,或者是safari5.1，每次往下滚动<code>event.wheelDelta</code>值都是<code>-120</code>. <span>//zxx:网上有说法说Safari值为<code>-360</code>, 我对此表示怀疑，下图为我的测试截图。</span><br>
<img src="http://image.zhangxinxu.com/image/blog/201304/2013-04-16_192722.png" width="191" height="125" alt="Safari浏览器下wheelDelta截图"></p>
<p>对于FireFox浏览器（Opera浏览器也有），判断鼠标滚动方向的属性为<code>event.detail</code>, 向下滚动值为<code>3</code>.<br>
<img src="http://image.zhangxinxu.com/image/blog/201304/2013-04-16_193934.png" width="362" height="58" alt="FireFox浏览器下event.detail, 值为3"></p>
<p>需要注意的是，FireFox浏览器的方向判断的数值的正负与其他浏览器是相反的。FireFox浏览器向下滚动是正值，而其他浏览器是负值。</p>
<h3>三、兼容的滚轮事件方法</h3>
<p>知己知彼百战百胜，知道了差异就知道如何处理这些差异。毕竟不是写JS库，我们这里只处理滚动方向这块的差异。</p>
<p>整合我们通常事件添加方法，于是有（下代码代号为<code>addEvent.js</code>）：</p>
<div>
<pre><span style="color:green">/**
 * 简易的事件添加方法
 */</span>
 
define(function(require, exports, module) {
    exports.addEvent = (function(window, undefined) {        
        var _eventCompat = function(event) {
            var type = event.type;
            <span style="color:#cd0000">if (type == 'DOMMouseScroll' || type == 'mousewheel') {
                event.delta = (event.wheelDelta) ? event.wheelDelta / 120 : -(event.detail || 0) / 3;
            }</span>
            //alert(event.delta);
            if (event.srcElement &amp;&amp; !event.target) {
                event.target = event.srcElement;    
            }
            if (!event.preventDefault &amp;&amp; event.returnValue !== undefined) {
                event.preventDefault = function() {
                    event.returnValue = false;
                };
            }
            <span style="color:green">/* 
               ......其他一些兼容性处理 */</span>
            return event;
        };
        if (window.addEventListener) {
            return function(el, type, fn, capture) {
                <span style="color:#cd0000">if (type === &quot;mousewheel&quot; &amp;&amp; document.mozHidden !== undefined) {
                    type = &quot;DOMMouseScroll&quot;;
                }</span>
                el.addEventListener(type, function(event) {
                    fn.call(this, _eventCompat(event));
                }, capture || false);
            }
        } else if (window.attachEvent) {
            return function(el, type, fn, capture) {
                el.attachEvent("on" + type, function(event) {
                    event = event || window.event;
                    fn.call(el, _eventCompat(event));    
                });
            }
        }
        return function() {};    
    })(window);        
});</pre>
</div>
<p>于是，我们就可以很从容使用<code>mousewheel</code>事件了。例如：</p>
<div>
<pre>addEvent(dom, &quot;mousewheel&quot;, function(event) {
    if (event.delta &lt; 0) { alert(&quot;鼠标向上滚了！&quot;); }
});</pre>
</div>
<h3>四、简单的实例、上面方法验证</h3>
<p>本想做个完备的幻灯平滑移动效果（左右有点击按钮之类），结果一不小心，都凌晨了，于是，改变主意了，就只做了个鼠标滚动，图片列表左右移动的效果。您可以狠狠地点击这里：<a href="http://www.zhangxinxu.com/study/201304/mousewheel-event-image-slide.html">滚轮事件下图片列表左右滑动demo</a></p>
<p>鼠标放在图片列表区域上，鼠标滚轮下滚滚，上滚滚，就可以看到图片列表们左右平滑移动的效果了。<br>
<img src="http://image.zhangxinxu.com/image/blog/201304/2013-04-17_010228.png" width="416" height="202" alt="鼠标滚轮事件下的平滑切换效果"></p>
<p>其中的滚轮相关交互就是使用的上面<code>exports</code>暴露的<code>addEvent</code>方法。</p>
<p>相关代码实现如下，下面这个展示的就是平滑移动的核心代码们（代号为<code>slide.js</code>）：</p>
<div>
<pre><span style="color:green">/**
 * 简易的列表左右滑动切换效果
 * 鼠标事件是关键，因此，一些数值写死在方法中，纯测试用
 */</span>
 
define(function(require, exports, module) {
    <span style="color:#cd0000">var Event = require("/study/201304/addEvent.js");</span>
    var _move = function(ele, to, from) {
        <span style="color:green">// 动画实现
        // ...</span>
    };
    return {
        index: 0,
        visible: 4,
        init: function(box) {
            <span style="color:green">// box指滚动的列表容器</span>
            var self = this
              , length = box.getElementsByTagName("li").length;
            <span style="color:#cd0000">Event.addEvent</span>(box.parentNode, "<span style="color:#cd0000">mousewheel</span>", function(event) {
                 if (<span style="color:#cd0000">event.delta &gt; 0</span> &amp;&amp; self.index &gt; 0) {
                    <span style="color:green">// 往上滚</span>
                    self.index--;
                 } else if (<span style="color:#cd0000">event.delta &lt; 0</span> &amp;&amp; self.index &lt; length - self.visible) {
                     <span style="color:green">// 往下</span>
                     self.index++;                     
                 } else {
                    return; 
                 }
                 _move(box, -1 * self.index * 140);    
             
                 event.preventDefault();
            });
        }
    };
});
 </pre>
</div>
<p>原理很简单，滚轮改变，索引改变，也就是列表的最终位置改变，动画到目标位置即可。</p>
<p>然后，demo页面<a href="http://www.zhangxinxu.com/wordpress/?p=2476">使用<code>seajs</code></a>简单调用就可以了！</p>
<div>
<pre>var $ = function(id) {
    return document.getElementById(id);
};
seajs.use("/study/201304/slide.js", function(slide) {
    slide.init($("slideBox"));
});</pre>
</div>
<p>就结束了，一些具体细节，例如关于HTML部分，或者动画的实现等，可以去demo等查看代码展示。</p>
<p>不过从效果来看，IE6以及IE7浏览器下的滚动并没有hold页面的滚动条，多番其他尝试也是如此，希望可以有相关经验的同行指点下，优化IE7/IE7浏览器下的体验效果。</p>
<p>原本还想再添加一个自定义滚动条的demo的，一看时间，我勒个去，已经1:11:11了，好不吉利的数字啊，看了下程序员运势万年历，今天不适宜写demo。于是，结语睡觉。</p>
<h3>五、首尾呼应的结语</h3>
<p><img src="http://image.zhangxinxu.com/image/emtion/normal.png" width="150" height="150" alt="正常状态">鼠标滚轮相关东西，我现在各个细节历历在目，要是现在发我张卷子，考鼠标滚轮事件知识，没有个90分我自己都不信。然而，目前为止，自己并未刻意去记忆，因此，如果接下来的1年时间自己很少或不接触相关内容。估计到时别人一问，小心脏一慌，说不定就一下子想不起"DOMMouseScroll"这厮了。因此，在结尾处，我要给自己来个特殊记忆。</p>
<p>怎么记呢？恩……啊，抓狂了，想不出来<img src="http://mat1.gtimg.com/www/mb/images/face/18.gif">。<code>event.wheelDelta</code>/<code>event.detail</code></p>
<p><code>wheelDelta</code>→滚轮的三角裤，火狐是骚狐狸，没有这个三角裤？欲知详情，请看火狐？<img src="http://mat1.gtimg.com/www/mb/images/face/27.gif"></p>
<p>……得，睡了，梦里再想吧~~</p>
<p>原创文章，转载请注明来自<a href="http://www.zhangxinxu.com/">张鑫旭-鑫空间-鑫生活</a>[<a href="http://www.zhangxinxu.com/">http://www.zhangxinxu.com</a>]<br>
本文地址：<a href="http://www.zhangxinxu.com/wordpress/?p=3175">http://www.zhangxinxu.com/wordpress/?p=3175</a></p>
<p>（本篇完）</p>
<div>有话要说？点击<a href="http://www.zhangxinxu.com/wordpress/2013/04/js-mousewheel-dommousescroll-event/#respond">这里</a>发表评论。</div>