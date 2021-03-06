---
layout: post
title:  "使用JavaScript和Canvas开发游戏（一）"
date:   2011-08-10 20:26:29
author: 为之漫笔
categories: program
---

## 使用JavaScript和Canvas开发游戏（一）
### by 为之漫笔
### at 2011-08-10 20:26:29
### original <http://www.cn-cuckoo.com/2011/08/10/game-development-with-javascript-and-the-canvas-element-2554.html>

<p>原文作者：Matthew Casperson • 编辑：Michele McDonough<br>
原文链接: <a href="http://www.brighthub.com/hubfolio/matthew-casperson/blog/archive/2009/06/29/game-development-with-javascript-and-the-canvas-element.aspx">Game Development with JavaScript and the Canvas element</a></p>
<p>1、认识一下Canvas<br>
2、在Canvas上绘图<br>
3、通过Canvas元素实现高级图像操作<br>
4、通过Canvas实现视差滚动<br>
5、写一个游戏框架（一）<br>
6、写一个游戏框架（二）<br>
7、动画<br>
8、JavaScript键盘输入<br>
9、综合运用<br>
10、定义级别<br>
11、跳跃与坠落<br>
12、添加道具<br>
13、加载资源<br>
14、添加主菜单</p>
<h1>1、认识一下Canvas</h1>
<p><a href="http://www.brighthub.com/internet/web-development/articles/38364.aspx">http://www.brighthub.com/internet/web-development/articles/38364.aspx</a></p>
<p>Canvas元素以及JavaScript引擎中新增的一些特性，让Web开发人员不必借助第三方插件，即可设计开发出精细且具有交互性的2D网页。这篇文章就向大家介绍一下Canvas元素，以及它的一些可能的用途。</p>
<h2>JavaScript与Canvas元素</h2>
<p>HTML是为创建静态页面而生的。HTML所能实现的动态效果，也仅限于显示GIF动画和闪烁的文本。JavaScript改变了这一切，通过它能够动态修改网页。今天，很多Web服务都利用AJAX来创建网页，为用户提供更加流畅的体验，也超越了标准HTML页面中常见的“点击－重新加载－点击”式的交互模式。</p>
<p>然而，JavaScript的某些功能会受到其宿主浏览器的制约。尽管可以在网页中创建和修改任何元素，但JavaScript不能（轻易地）让浏览器显示一种新对象。通过JavaScript修改文本、插入图像或者缩放表格都很容易，因为这些对象本来就是HTML所支持的。如果你想再玩得刺激一点，比如写一个网页游戏，怎么办？那恐怕就得苦心积虑地改变标准HTML元素的用途，克服种种不测才能达到目的。要么，你就得求助于Flash或Silverlight这样的插件。</p>
<p>Canvas元素登场了。这个新HTML元素为JavaScript开发者提供了一种无需插件即可在网页中直接绘图的机制。Canvas元素最早是由苹果公司在其WebKit框架中引入的，Safari浏览器和Dashboard微件都在使用。Canvas元素现在也被建议加入了HTML5规范，得到了最新的Chrome、Firefox、Opera以及Konqueror等浏览器的支持。Internet Explorer（至少在IE8之前）还不支持Canvas，但ExplorerCanvas项目倒是为IE提供了与Canvas元素类似的功能。</p>
<p>Canvas元素对做过2D图形编程的人是小菜一碟。可以在这个元素上画线、画各种形状、画渐变，甚至可以利用与其他2D API中类似的函数来修改其中的每一个像素。得益于Chrome的V8、Firefox的SpiderMonkey以及Safari的Nitro等最新JavaScript引擎的性能，创建精细且具有交互性的Web应用已经完全没有问题。</p>
<p>我们这一系列文章将教会大家使用JavaScript和Canvas元素创建一个简单的平台游戏。将要涉及的内容包括动画、加载资源、分层渲染、滚动和交互。通过一步一步地展示示例代码和实际效果，你可以很快学会如何驾驭强大的Canvas元素。</p>
<h1>2、在Canvas上绘图</h1>
<p><a href="http://www.brighthub.com/internet/web-development/articles/38744.aspx">http://www.brighthub.com/internet/web-development/articles/38744.aspx</a></p>
<p>下面，我们就通过一个循序渐进的示例及实时演示，来介绍如何使用JavaScript在Canvas元素上绘图及实现动画。</p>
<h2>准备工作</h2>
<p>知道了什么是Canvas元素之后，该学习在屏幕上绘图了。首先，需要一个HTML页面来放置和显示Canvas元素。<br>
<span></span></p>
<pre>

  &lt;!DOCTYPE HTML PUBLIC &quot;-//W3C//DTD HTML 4.01//EN&quot;&gt;
  &lt;html lang=&quot;en&quot;&gt;
     &lt;head&gt;
        &lt;title&gt;JavaScript Platformer 1&lt;/title&gt;
        &lt;script type=&quot;text/javascript&quot; src=&quot;jsplatformer1.js&quot;&gt;&lt;/script&gt;
        &lt;style type=&quot;text/css&quot;&gt;
           body { font-family: Arial,Helvetica,sans-serif;}
        &lt;/style&gt;
     &lt;/head&gt;
    &lt;body&gt;
       &lt;p&gt;
          &lt;a href=&quot;http://www.brighthub.com/internet/web-development/articles/38364.aspx&quot;&gt;
             Game Development with Javascript and the canvas element
          &lt;/a&gt;
       &lt;/p&gt;
       &lt;canvas id=&quot;canvas&quot; width=&quot;600&quot; height=&quot;400&quot;&gt;
          &lt;p&gt;Your browser does not support the canvas element.&lt;/p&gt;
       &lt;/canvas&gt;
    &lt;/body&gt;
 &lt;/html&gt;
</pre>
<p>这些HTML代码很直观。其中有两个重要的元素。</p>
<pre>

&lt;script type=&quot;text/javascript&quot; src=&quot;jsplatformer1.js&quot;&gt;&lt;/script&gt;
</pre>
<p>这里包含的是将会修改Canvas元素的JavaScript代码，对应的Canvas元素的标记如下：</p>
<pre>

&lt;canvas id=&quot;canvas&quot; width=&quot;600&quot; height=&quot;400&quot;&gt;
	&lt;p&gt;Your browser does not support the canvas element.&lt;/p&gt;
&lt;/canvas&gt;
</pre>
<p>以上代码创建了一个Canvas元素。不支持Canvas的浏览器，比如Internet Explorer（IE8之前的版本），会忽略这个元素，而只显示其子元素。在这个简单的例子中，这个子元素就是一个段落，其中的文本告诉用户他们的浏览器不支持Canvas元素。而对于那些支持Canvas元素的浏览器，如Chrome、Opera和Firefox，则会忽略Canvas元素的子元素。</p>
<p>这个Canvas元素的ID属性很重要，因为后面的JavaScript将通过它来取得对该元素的引用。而width和height属性指定了画布的宽度和高度，这两个属性跟table或img等其他HTML元素中的同名属性作用一样。</p>
<p>以下是 jsplatformer1.js的代码：</p>
<pre>

//每秒钟target帧
const FPS = 30;
var x = 0;
var y = 0;
var xDirection = 1;
var yDirection = 1;
var image = new Image();
//建议读者将图片下载到本地加载（经测试，此图片响应头部的Content-Type为application/empty，浏览器无法识别）
image.src = &quot;http://javascript-tutorials.googlecode.com/files/jsplatformer1-smiley.jpg&quot;;
var canvas = null;
var context2D = null;

window.onload = init;
function init(){
	canvas = document.getElementById(&#39;canvas&#39;);
	context2D = canvas.getContext(&#39;2d&#39;);
	setInterval(draw, 1000/FPS);
}
function draw(){
	context2D.clearRect(0, 0, canvas.width, canvas.height);
	context2D.drawImage(image, x, y);
	x += 1* xDirection;
	y += 1* yDirection;

	if (x &gt;= 450) {
		x = 450;
		xDirection = -1;
	}else if(x &lt;= 0){
		x = 0;
		xDirection = 1;
	}
	if (y &gt;= 250) {
		y = 250;
		yDirection = -1;
	}else if(y &lt;= 0){
		y = 0;
		yDirection = 1;
	}
}
</pre>
<p>如果只是一个Canvas元素，也没有什么用。JavaScript必须要在这块画布上面画点什么，相应的代码保存在 jsplatformer1.js中。简单来说，JavaScript在这里先加载了一幅图像，然后将其画在画布上面，最后让它在画布上移动。</p>
<p>首先，定义一些全局变量。</p>
<pre>

const FPS = 30;
</pre>
<p>FPS定义的是画布重绘的频率。</p>
<pre>

var x = 0;
var y = 0;
var xDirection = 1;
var yDirection = 1;
</pre>
<p>变量x、y、xDirection和yDirection用于定义图像（相对于画布左上角）的位置，以及它在任意一时刻移动的方向。</p>
<pre>

var image = new Image();
image.src = &quot;http://javascript-tutorials.googlecode.com/files/jsplatformer1-smiley.jpg&quot;;
</pre>
<p>要把图像画到画布上，必须先加载一幅图像。为此，我们创建一个Image对象，将其src属性设置为一幅图像文件的URL（建议把图片下载到本地。——译者注）。</p>
<pre>

var canvas = null;
var context2D = null;
</pre>
<p>我们还需要取得对Canvas元素以及绘图上下文（稍后再详细介绍绘图上下文）的引用。稍后我们会把正确的值赋给这两个变量，现在先把它们设置为null。</p>
<pre>

window.onload = init;
</pre>
<p>最后，当页面加载完成后，我们必须知道立即运行绘制画布的代码；因此，在window对象的onload事件发生时，立即调用init函数。</p>
<h2>init函数</h2>
<pre>

function init(){
	canvas = document.getElementById(&#39;canvas&#39;);
	context2D = canvas.getContext(&#39;2d&#39;);
	setInterval(draw, 1000/FPS);
}
</pre>
<p>页面加载完毕后就会调用上面这个init函数。在这个函数中，我们先通过在HTML文件中指定的ID属性取得画布元素（毫无疑问，除了把它叫做画布，还能叫个啥？），然后再取得这个画布的2D绘图上下文对象。</p>
<p>上下文对象用于定义如何在画布上绘图。顾名思义，2D上下文嘛，支持在画布上绘制2D图形、图像和文本。支持画布元素的浏览器都支持2D上下文，除了2D上下文，还有其他试验性的上下文对象。Opera有一个专门为游戏设计的2D上下文，而Mozilla则有一个能够显示3D场景的上下文。可惜呀，目前这些上下文对象只有特定的浏览器才支持。如果你想用画布来创建Web应用，最好还是只使用常见的2D上下文。</p>
<p>因为我们在这里是想绘制一幅能移动的图像，所以必须建立渲染循环（render loop）。所谓渲染循环，实际上就是一个被重复调用的函数，渲染循环的每一次迭代，（在这个例子中）都可以让图像在屏幕上产生一点位移，如此循环往复就能给人图像在移动的感觉。为此，我们调用了setInterval函数，它的第一个参数是应该被重复调用的函数，这里的函数名是draw。setInterval函数的第二个参数指定调用函数的频率。这个参数值的单位是毫秒，而用1000除以早先定义的FPS得到的就是每次调用之间相隔的毫秒数。</p>
<p>这里需要注意一下，虽然我们指定每秒钟调用30次draw函数，但实际上不会调用30次。多长时间调用一次draw函数，取决于底层JavaScript引擎的速度和要执行的draw函数代码的复杂程度。如果系统很慢的话，很可能每秒钟只能调用一次draw函数。所以说，这里指定给setInterval的频率只是一种最理想的情况。</p>
<h2>draw函数</h2>
<p>在画布上绘图的操作实际上都是由draw函数来完成的。下面我们就一步一步地说明其中的绘图操作。</p>
<pre>

context2D.clearRect(0, 0, canvas.width, canvas.height);
</pre>
<p>所有绘图操作都是在上下文对象上发生的，并不是在画布元素上发生的。这里首先清空上下文，以便为绘制每一帧画面准备一个干净的版面。</p>
<pre>

context2D.drawImage(image, x, y);
</pre>
<p>紧接着，就把图像绘制到上下文对象中，参数x和y指定了绘制图像的左上角坐标。</p>
<pre>

x += 1 * xDirection;
y += 1 * yDirection;
</pre>
<p>为了让图像在画布上移动，需要根据xDirection和yDirection是等于1（向右或向下）还是等于-1（向左或向上），来递增或递减x与y的值。</p>
<pre>

if (x &gt;= 450){
	x = 450;
	xDirection = -1;
} else if (x &lt;= 0) {
	x = 0;
	xDirection = 1;
}
if (y &gt;= 250) {
	y = 250;
	yDirection = -1;
} else if (y &lt;= 0) {
	y = 0;
	yDirection = 1;
}
</pre>
<p>如果图像移动到了画布外面，则反转图像的移动方向。我们知道图像的大小是150×150像素，而画布的大小的是600×400像素，因而就有了450（600 – 150）和250（400 – 150）这两个值。</p>
<p>最后的效果就是笑脸图像会在画布的范围内反弹往复。此时此刻，有读者可能会想：同样的效果如果通过修改DIV元素的位置来实现可能更容易一些。这一点我不否认。但这个例子只演示了画布元素所能实现的简单效果。下一篇文章我们就会介绍使用画布元素能够实现的高级效果，同样的效果若采用其他方式，恐怕就要困难多了。</p>
<p>为之漫笔 最后编辑于：<br>
2011/08/12 @ 06:39</p>