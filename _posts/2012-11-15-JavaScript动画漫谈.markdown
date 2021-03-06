---
layout: post
title:  "JavaScript动画漫谈"
date:   2012-11-15 18:11:05
author: JerryQu
categories: program
---

## JavaScript动画漫谈
### by JerryQu
### at 2012-11-15 18:11:05
### original <http://www.udpwork.com/item/8669.html>

<p>动画现在基本是web站点必备项，各大JS框架都封装了相应的代码。QWrap也内置了一个实现，支持常见的动画效果和算子及队列播放，<a href="http://dev.qwrap.com/resource/js/components/anim/_tests/retouched_test2.html">这里有示例</a>。JS动画的实现有多种方式，各自有哪些区别，QWrap使用的哪一种？本文闲聊这个话题。</p>
<p>一、使用浏览器的定时器（setInterval/setTimeout）。这种动画实现方式很早就被大家广泛使用，原理简单，兼容性好。最核心的原理就是利用定时器，在一定时间内（duration），以一定的间隔（frameTime）执行动画函数（callback）。在动画函数里可以改变元素大小、位置（css属性值）；可以改变元素滚动条位置（scrollTop/scrollLeft）；甚至用来改变文本内容（倒计时）。</p>
<p>动画间隔决定了动画的每秒帧数（FPS），一般的，FPS越高，动画就表现得越流畅，FPS偏低，动画就会不流畅、卡顿。JQuery中，动画间隔默认为13ms，也就是说理想状态下，动画的每秒帧数是70多。实际上，由于JS定时器精度问题，间隔不可能太小；在计算机资源占用比较大时，这个间隔也没办法得到保证；更为严重的是，新一点的浏览器在页面不可见时（例如切换到其他tab，浏览器被最小化），会自动提高定时器执行间隔，firefox5开始，setInterval的间隔在浏览器最小化之后至少被提高到1000ms。</p>
<p>动画时长 = 播放总帧数 * 帧间隔平均值。由于帧间隔不可控，可能被提高到1000ms甚至更高，那么实现动画时面临两个选择：要保证播放总帧数，动画时长就会增加；要保证动画时长，就必须牺牲掉总帧数。实际上我们一般采用第二种方式，也就是丢帧保时的策略来实现动画，来看一个简单的例子：</p>
<pre>&lt;script&gt;
var timerId, startTime, frameTime = 13, dur = 3 * 1000;

function animFun(time) {
    var per = Math.min(1.0, (new Date - startTime) / dur);
    if(per &gt;= 1) {
        clearTimeout(timerId);
    } else {
        document.getElementById(&quot;animated&quot;).style.left = Math.round(500 * per) + &quot;px&quot;;
    }
}

function start() {
    startTime = new Date;
    timerId = setInterval(animFun, frameTime);
}
&lt;/script&gt;
&lt;div id=&quot;animated&quot; onclick=&quot;start()&quot; style=&quot;position: absolute; left: 0px; padding: 50px;background: crimson; color: white&quot;&gt;Click Me&lt;/div&gt;
</pre><p>在动画开始时记录一个初始时间，动画函数里用当前时间减去初始时间，得到的时间差除以总时长，可以得到动画执行进度（per）。再根据per去改变元素的css属性值，就实现了一个最简单的动画。</p>
<p>上面例子中，方块运动是匀速的，平淡无奇。想要运动轨迹更有趣，就需要引入动画算子。动画算子是一个函数，把动画进度per转换为另外一个值，在上面例子基础上改进下：</p>
<pre>&lt;script&gt;
var timerId, startTime, frameTime = 13, dur = 3 * 1000;

function bounceOut(p) {
    if (p &lt; (1 / 2.75)) {
        return (7.5625 * p * p);
    } else if (p &lt; (2 / 2.75)) {
        return (7.5625 * (p -= (1.5 / 2.75)) * p + 0.75);
    } else if (p &lt; (2.5 / 2.75)) {
        return (7.5625 * (p -= (2.25 / 2.75)) * p + 0.9375);
    }
    return (7.5625 * (p -= (2.625 / 2.75)) * p + 0.984375);
}

function animFun(time) {
    var per = Math.min(1.0, (new Date - startTime) / dur);
    if(per &gt;= 1) {
        clearTimeout(timerId);
    } else {
        document.getElementById(&quot;animated&quot;).style.left = Math.round(500 * bounceOut(per)) + &quot;px&quot;;
    }
}

function start() {
    startTime = new Date;
    timerId = setInterval(animFun, frameTime);
}
&lt;/script&gt;
&lt;div id=&quot;animated&quot; onclick=&quot;start()&quot; style=&quot;position: absolute; left: 0px; padding: 50px;background: crimson; color: white&quot;&gt;Click Me&lt;/div&gt;
</pre><p>这下方块运动就有趣多了，上面例子中的bounceOut就是一个算子。有些动画组件算子可能会接受更多参数（运动距离、时间等），但是QWrap中，算子都只需要传per。这里有<a href="http://qgy18.imququ.com/lab/animation/easing.html">QWrap内置算子的演示</a>。</p>
<p>二、W3C有一份WindowAnimationTiming interface规范，也可以用来实现动画。它的核心方法是requestAnimactionFrame和cancelRequestAnimationFrame。各大浏览器新版都有实现，这部分内容我<a href="http://www.imququ.com/post/75.html">之前介绍过</a>，不了解的同学可以点过去看，看完记得再回来。</p>
<p>可以看到，W3C这份规范提供的动画没有帧间隔时间这个概念，也就是何时触发下一帧完全由浏览器控制。其它方面跟setInterval动画几乎一样，上面的算子也可以直接拿来用。下面说下我遇到的几个坑：</p>
<p>首先是firefox：在11之前的某个版本开始，firefox实现了mozRequestAnimationFrame，却没有提供对应的mozCancelRequestAnimationFrame，那时网上有文章会提到通过“注册、移除moz私有的beforepaint事件”来模拟这个事件。坑爹的是，firefox11开始有了mozCancelRequestAnimationFrame，但老方案直接抛异常。</p>
<p>webkit下也有坑：某个诡异的webKit版本下，webkitRequestAnimationFrame没有给回调函数传time参数，更神奇的是一些webkit居然传递错误格式的time。为此，在webkit下我们通常不用参数里的time，改为自己new Date。</p>
<p>综上，由于各浏览器对标准实现的不一致和bug，最终我们在QWrap中并没有使用基于原生动画函数的版本。不过也可以像下面这样变通使用，来绕过那些坑：</p>
<pre>&lt;script&gt;
var timerId, startTime, dur = 3 * 1000,
    requestAnimationFrame = window.webkitRequestAnimationFrame ||
            window.mozRequestAnimationFrame ||
            window.oRequestAnimationFrame;

if(!requestAnimationFrame) { alert(&#39;你的浏览器不支持requestAnimationFrame！&#39;); }

function animFun(time) {
    if(!timerId) return false;
    var per = Math.min(1.0, (new Date - startTime) / dur);
    if(per &gt;= 1) {
        timerId = null;
    } else {
        document.getElementById(&quot;animated&quot;).style.left = Math.round(500 * per) + &quot;px&quot;;
        requestAnimationFrame(animFun);
    }
}

function start() {
    startTime = new Date;
    timerId = 1;
    requestAnimationFrame(animFun);
}
&lt;/script&gt;
&lt;div id=&quot;animated&quot; onclick=&quot;start()&quot; style=&quot;position: absolute; left: 0px; padding: 50px;background: crimson; color: white&quot;&gt;Click Me&lt;/div&gt;
</pre><p>三、CSS3动画。css3中的Transition可以用来平滑改变css属性值。简而言之，给元素设置下面这样的css样式后，再改变transition-property指定属性的值，浏览器会自动处理剩下的事情：</p>
transition-property :* //需要动画的css属性，如height，all表示全部属性
<br>
transition-duration:*//持续时间，duration，单位s
<br>
transition-delay:* //延迟时间，单位s
<br>
transition-timing-function:*//动画算子，有ease | linear | ease-in | ease-out | ease-in-out | cubic-bezier<p>通常，我们在css里写好transition规则，再改变元素class就可以拥有css3动画了。但是为了使用更方便，我们也可以把这个过程封装成为js组件。监听动画执行结束，也需要依赖js。</p>
<p>具体的代码就不贴了，后面例子里都有，这里说一些需要关注的地方。本来，检测浏览器是否支持css3动画可以通过创建临时元素，判断'transitionProperty' in el.style是否为true就可以了；但是，万恶的浏览器厂商前缀（就是那些-webkit-、 -moz-、-o-、-ms-），使这个过程变得复杂多了。最坑爹的是IE，css里可以用“-ms-transition-property”，js中对应的style，却是“el.style.msTransitionProperty”。QWrap在设置css的函数中，会先调用StringH.camelize函数处理属性名，会把“-ms-xxx-ooo”变成“MsXxxOoo”，IE不认！其他浏览器则无论首字母是否大小都支持。</p>
<p>要监听动画结束，webkit需要给动画元素绑定webkitTransitionEnd事件，firefox是transitionend，opera是oTransitionEnd，IE是MSTransitionEnd。firefox支持标准事件名，其他浏览器都是“小写前缀+TransitionEnd”，唯独IE的ms全都要大写！</p>
<p>好在，随着浏览器的发展，大家逐渐开始支持标准写法，慢慢不再需要写前缀了。我这边测试，最新的IE10 for Win7 Pre Release版也支持了标准写法，所以js需要优先尝试无前缀的用法。详细的情况可以去<a href="http://caniuse.com/#search=transition">CanIUse</a>看。</p>
<p>--- 分割线 ---</p>
<p>几种动画实现都大概介绍了下，都可以封装成JS组件，有一致的接口方便调用。那么实际项目中该如何选择呢？</p>
<p>从浏览器支持度来看，除了第一种方案，其余方案都是没办法在低版本IE使用的。如果需要支持更多浏览器，可以采用方案一（QWrap）；或者优先使用方案二，方案一作为降级（JQuery）。如果是面向移动平台，可以使用方案三（Zepto）。</p>
<p>移动平台上能用css3动画就不要用requestAnimationFrame，因为iOS6才开始支持它。使用css3的transition+transform，渲染效率比动画callback里改变css属性值高很多。另外，iOS上移动或缩放页面时，操作的是touch开始时的截屏，页面是静止不动的。那么松开手指，只有css3动画能连续播放，其他两种方案因为都是根据时间差计算进度，会明显的跳跃到某帧。</p>
<p>css3动画有一些功能上的缺失，它不支持在播放每一帧时触发callback，也就是没办法监控播放进度，也没办法暂停和恢复动画。但是css3动画组件可以把Transform封装进去，提供一些很赞的新功能：<a href="http://qgy18.imququ.com/lab/animation/transform.html">DEMO</a>。更复杂的动画，或者有暂停播放的需求，通过CSS3 Animation可以实现，不过我们暂时很少用到，所以没做封装。</p>
<p>而setInterval和requestAnimationFrame实现的动画，在改变css属性时需要额外做更多事情，例如backgroundColor需要转化为R、G、B三个数值来分别变换；其它有单位的属性值，如height:100px，也需要转化为{value:100, unit:'px'}这样的形式，再对value进行变换。但是，由于每个属性都可以应用不同的算子，可以组合出独特的运动轨迹，<a href="http://qgy18.imququ.com/lab/animation/ball.html">DEMO</a>。</p>
<p>前面说到在页面不可见时，浏览器会自动提高setInterval的间隔。那对于requestAnimationFrame和css3动画，浏览器会怎么处理呢？</p>
<p><a href="http://st.imququ.com/uploads/2012/11/chrome.png" title="点击查看大图"><img src="http://st.imququ.com/uploads/2012/11/m_chrome.png"></a>
<br>
（chrome23）</p>
<p><a href="http://st.imququ.com/uploads/2012/11/firefox.png" title="点击查看大图"><img src="http://st.imququ.com/uploads/2012/11/m_firefox.png"></a>
<br>
（firefox16）</p>
<p>这个测试页面引入了三个iframe，对应本文三种动画实现，duration都是2s。16s的时间内，动画应该执行8个周期。页面处于不可见时：chrome下，用requestAnimationFrame实现的动画完全被停止播放；其他动画执行周期都有一定程度下降。<a href="http://qgy18.imququ.com/lab/animation/index1.html">测试地址在这里</a>，大家可以自己试试~</p>
<p>本文链接：<a href="http://www.imququ.com/post/js_animation.html" title="Permalink to JavaScript动画漫谈">http://www.imququ.com/post/js_animation.html</a></p>
<p>--EOF--</p>

			<div style="margin-top:8px;padding:6px 0;border-top:1px solid #3cf">
				<div style="text-align:center;margin:16px 0;padding:6px;border:0px dashed #999;font-family:arial;font-size:26px;font-weight:bold">
	<a href="http://www.udpwork.com/item/8669.html#review_form" title="不喜欢" style="text-decoration:none">
		<img src="http://www.udpwork.com//images/thumb_down24.gif" alt="">
		<span style="color:#f33">0</span>
	</a>
	   
	<a href="http://www.udpwork.com/item/8669.html#review_form" title="喜欢" style="text-decoration:none">
		<img src="http://www.udpwork.com//images/thumb_up24.gif" alt="">
		<span style="color:#3c3">0</span>
	</a>
</div>				<p>
					由 <a href="http://www.udpwork.com/">IT 牛人博客聚合网站(udpwork.com)</a> 聚合
					|
					<a href="http://www.udpwork.com/item/8669.html#reviews">评论: 0</a>
					|
					<a href="http://book.benegg.com/tag/%E7%BC%96%E7%A8%8B?from=udpwork-feed">10000+ 本编程/Linux PDF/CHM 电子书下载</a>
				</p>
			</div>