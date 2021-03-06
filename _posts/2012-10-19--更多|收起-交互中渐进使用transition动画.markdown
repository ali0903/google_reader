---
layout: post
title:  "“更多|收起”交互中渐进使用transition动画"
date:   2012-10-19 15:54:10
author: 张 鑫旭
categories: program
---

## “更多|收起”交互中渐进使用transition动画
### by 张 鑫旭
### at 2012-10-19 15:54:10
### original <http://www.zhangxinxu.com/wordpress/2012/10/more-display-show-hide-tranisition/>

<p>by <a href="http://www.zhangxinxu.com/">zhangxinxu</a> from <a href="http://www.zhangxinxu.com/">http://www.zhangxinxu.com</a><br>
本文地址：<a href="http://www.zhangxinxu.com/wordpress/?p=2717">http://www.zhangxinxu.com/wordpress/?p=2717</a></p>
<h3>一、这是个平淡的开头</h3>
<p>在web页面上，类似于“展开更多”、“显示全部”这类显隐效果，就跟大上海的私家车一样，随处可见。随便Open两个页面，就可见到影分身：<br>
<img alt="豆瓣网上的" src="http://image.zhangxinxu.com/image/blog/201210/2012-10-18_150234.png" title="豆瓣网上的" width="332" height="146"> <img alt="大众点评的" src="http://image.zhangxinxu.com/image/blog/201210/2012-10-18_150326.png" title="大众点评的" width="321" height="90"></p>
<p>一般而言，且平心而论，这类交互效果应当就是这样子的——“唐突的显隐交互”，即内容“啪”一下子呈现或隐藏。</p>
<p>简洁明快，快速消费，且性能不错，实现以及后期维护成本也很低；对于功能性，多交互的商业站点而言，诸多权衡，确实是不错的选择。不过，类似广告宣传的展示性网站，更注重炫酷效果，唐突以及生硬的效果显然是接受不了的。</p>
<p>还有可能接受不了的是年轻气盛的交互设计师——其可能无法忍受一段内容唐突地出现在用户面前——而不是slideDown这类柔和的动画形式——因为前者可能会让用户觉得不舒服，体验不够好。</p>
<p>我突然想到了网上常常会出现的一句话：强奸着强奸着，也就习惯了~~</p>
<p>想到这句话，说明自己也矛盾，或者说自己还没有想明白：整站流畅交互固然不错，就像Apple的产品一样，会有非常不错的体验。但是，实际上，自己内心真正想法是：如果网站交互都带有动画性质，反而很不好；即使将实现以及性能等因素撇开不谈，也是不合适的；因为，网站可能需要自己的气质，过多的效果可能会显得浮躁~~</p>
<p>这种感觉就像是：一个中国妹子，全身上下都是扎眼的高档装饰品，不见得好看（Apple这样的名媛或贵妇另当别论）；反而只有头上插了朵小花，更好看。所谓画龙点睛，1~2处惊艳即可，如果龙身上画满了眼睛——我勒个去，密集恐惧~~</p>
<p>听说有个“二八法则”的，如果应用在显隐toggle交互上，8分“啪”显隐，2分“呼”显隐？？——我不确定，你怎么看呢？</p>
<h3>二、“更多|收起”喜欢的动画</h3>
<p>虽然，web世界中，交互动画效果N多多，但是，很多都是约定俗成的，或者称之为“有固定套路的”。</p>
<p>根据David Kaneda创建的Transitions动画CSS代码，我们可以将效果归结为这几大类：slide(滑来滑去), fade(淡入淡出), flip(飞来飞去), pop(大大小小).</p>
<p>如居中弹框呈现与隐藏，适合pop效果；绝对定位浮动层（如智能提示下拉框，自定义时间选择控件）等的呈现与隐藏使用fade效果；幻灯片播放的广告位效果一般为slide效果；点击某商品飞入页面右下角或左上角的购物车就是flip效果（类似最新FireFox浏览器关闭标签页效果）。</p>
<p>而对于页面上，“展开更多|收起更多”这类交互，由于元素默认隐藏，且显示内容就在点击区域附近，因此，想要避免唐突，适合的效果只能是从无到有慢慢“滑出来”-也就是slide效果。</p>
<p><strong>正统的slide效果与伪slide效果</strong><br>
熟知jQuery的人应该都知道，其效果API中有<code>slideDown()</code>, <code>slideUp()</code>, <code>slideToggle()</code>的动画效果API. 那这几个方法是正统的slide效果呢还是伪slide效果？</p>
<p>答案是：伪slide效果！</p>
<p>正统的slide效果，应该是元素整体位置的移动，如这个demo所示的效果，您可以狠狠地点击这里：<a href="http://www.zhangxinxu.com/study/201210/true-slide-demo.html">正统slide效果演示demo</a><br>
<img alt="正统slide元素下面先显示 张鑫旭-鑫空间-鑫生活" src="http://image.zhangxinxu.com/image/blog/201210/2012-10-18_204626.png" title="正统slide元素下面先显示" width="403" height="244"></p>
<p>而jQuery <code>slideDown()</code>/<code>slideUp()</code>效果中，元素本身并没有移动，类似这个demo所示效果：<a href="http://www.zhangxinxu.com/study/201210/pseudo-slide-demo.html">伪slide效果演示demo</a><br>
<img alt="伪slide效果元素的上部分先显示 张鑫旭-鑫空间-鑫生活" src="http://image.zhangxinxu.com/image/blog/201210/2012-10-18_205515.png" title="伪slide效果元素的上部分先显示" width="408" height="277"></p>
<div style="width:550px;border-bottom:2px dotted #999;margin-top:5px;margin-bottom:5px"></div>
<p>在我们实际制作页面的时候，采用的都是“伪slide效果”，为何？</p>
<p>因为更低的成本！归根结底是因为：一般的元素（都是非定位元素）是从上往下一次排下来的。因此，通过控制<code>height</code>来实现slide效果的时候，都是元素的上半部分先显示。正统的slide效果的实现需要将动画呈现元素改成定位元素（应用position:absolute/fixed/…），这显然只适用于某些特殊的情况……</p>
<p>因此，作为API, 插件或公共方法，所实现的slide效果都是“伪slide效果”。</p>
<h3>三、jQuery中滑动API</h3>
<p><strong>实现的原理</strong><br>
一图胜前言，下图为slideDown效果进行中的时候，对动画元素HTML代码的截图：<br>
<img alt="jQuery slide效果进行中的截图 张鑫旭-鑫空间-鑫生活" src="http://image.zhangxinxu.com/image/blog/201210/2012-10-18_211312.png" title="jQuery slide效果进行中的截图" width="534" height="110"></p>
<p>因此，可以知道，jQuery的slide效果是同时变更元素的<code>height</code>/<code>margin-top</code>/<code>margin-bottom</code>/<code>padding-top</code>/<code>padding-bottom</code>/ + 设置<code>overflow:hidden</code>值实现的。</p>
<p>注意：没有同时变更<code>border-top</code>/<code>border-bottom</code>的宽度值，因此，如果元素的边框较大，slide效果在结束的瞬间会有顿一下的感觉。在jQuery1.6版本中，宽边框元素无论是<code>slideDown</code>效果还是<code>slideUp</code>效果，在动画要结束的时候，都会有明显的顿感；在jQuery1.8版本中，<code>slideDown</code>效果从头到尾都算流程；但是<code>slideUp</code>效果结束时候有顿感。</p>
<p>您可以狠狠地点击这里：<a href="http://www.zhangxinxu.com/study/201210/jquery-slide.html">jQuery1.6以及jQuery1.8版本slide效果对比demo</a></p>
<p><img alt="IE7浏览器下效果的顿感以及bug截图 张鑫旭-鑫空间-鑫生活" src="http://image.zhangxinxu.com/image/blog/201210/2012-10-18_212730.png" title="IE7浏览器下效果的顿感以及bug截图" width="533" height="247"></p>
<p><strong>应用局限</strong><br>
个人观点，jQuery中的slide滑动效果，看上去还那么回事，实际上，是个鸡肋API.</p>
<p>1. 依赖jQuery库。jQuery库本身越来越庞大，比方说jQuery1.8 gzip后居然还有30多K，个人觉得相当大，也相当笨重了。实际上，对于实际项目，选择器没有必要那么强大，有一半的API可以阉割掉，gzip后8~9K足矣，下图我最近某项目使用jQuery文件：<br>
<img alt="jQuery核心代码压缩后gzip后大小" src="http://image.zhangxinxu.com/image/blog/201210/2012-10-18_215123.png" title="jQuery核心代码压缩后gzip后大小" width="530" height="62"></p>
<p>2. 效果本身不是很完美。如上面演示的，边框元素顿感，以及IE6/IE7浏览器下直接显示bug. </p>
<p>3. 性能问题。如果页面复杂，本身交互比较多。对于IE6这类浏览器，如此动画效果，必定会有卡、顿的感觉；甚至会浏览器扛不住直接崩掉。因此，在实际项目的时候，slide动画使用并不多。尤其对于“更多|收起”交互，因为切换元素都不是绝对定位元素，因此，强烈的重绘会让CPU激动不已！</p>
<p>4. 过分兼容。我觉得，就算应用slide效果，最好IE6~7下直接“唐突显示”，其他现代浏览器浏览器则动画什么的。什么样车有什么样的的马力，什么样的马力跑什么样的速度！……</p>
<p>因此，如果我们非得应用一些slide交互效果，我们可以需求其他更简单，更方便，更灵活的方法。例如，借助CSS3中的transition, 渐进实现一些动画效果。</p>
<h3>五、transition实现 – 真的很简单</h3>
<p>拿上面出现的“正统slide效果页面”同样效果举例，您可以狠狠地点击这里：<a href="http://www.zhangxinxu.com/study/201210/true-slide-transition.html">transition实现正统slide效果demo</a></p>
<p>上demo IE10+, 最近版本的FireFox, Chrome, Opera浏览器都可以看到非常流畅的slide动画效果。</p>
<p>相比之前模拟动画demo，这里多了这么行CSS代码：</p>
<div>
<pre>.container {
    transition: height 0.6s;
}</pre>
</div>
<p>然后，JS代码就基本上全部消灭了，只留下改变高度的几行代码：</p>
<div>
<pre>var display = false;

button.onclick = function() {
    display = !display;
    <span style="color:#cd0000">container.style.height = display? "192px": "0px"</span>
    return false;
};
})();
</pre>
</div>
<p>简单，真好！我突然诗兴大发：“脱裤子般简单，拉大便般舒畅；啊！真好，真好！”<br>
众人：“好诗！好诗！！”</p>
<h3>六、transition实现的公共方法</h3>
<p>在展示transition slide公共方法前，有必要讲下CSS3 <code>transition</code>动画效果出现的条件：</p>
<ol>
<li>动画属性前后不同值必须含数值，例如<code>height:0</code> → <code>height: auto</code>是没有动画效果的</li>
<li>不能属性值的应用前后必须有时间差，例如：
<div>
<pre>element.style.height = "0px";
element.style.height = "100px";</pre>
</div>
<p> 虽然这里元素高度设置了<code>0</code>, 也设置了<code>100</code>像素。但是，由于浏览器的性能机制，只会直接渲染后面100像素高度，因此，是不会出现动画效果的！</p></li>
</ol>
<p>因此，考虑到在实际的交互中，显示元素的高度是不可能都已知的，我们要实现transition的公共方法的最难点，就是得到展开元素的精确高度值(height:auto是不会有动画效果的)。</p>
<p>本着简单，人人可以上手的指导思想，我们需要一些约定，关于CSS以及HTML结构的。</p>
<p>HTML结构：</p>
<div>
<pre>container
    box
        list
        list</pre>
</div>
<p>其中<strong>container</strong>以下CSS是必不可少的（如果内部子元素没有绝对定位元素，<code>position:relative</code>可以省略），就是下面这样（私有前缀这里省掉了）：</p>
<div>
<pre>.container {
    height: 0; position: relative;  overflow: hidden; transition: height 0.6s;
}</pre>
</div>
<p>其中，动画时间你自己控制，你还可以增加缓动类型，例如<code>ease</code>:</p>
<div>
<pre>.container {
    transition: height 0.3s ease;
}</pre>
</div>
<p>对于HTML结构，只有唯一一个要求，就是<strong>container</strong>的子元素只有一个（方便高度的获取，多子元素，高度计算麻烦多了）。</p>
<p>例如：</p>
<div>
<pre>&lt;div class=&quot;container&quot;&gt;
    &lt;p&gt;我是孤独的根号3...&lt;/p&gt;
&lt;/div&gt;</pre>
</div>
<p>或者：</p>
<div>
<pre>&lt;div class=&quot;container&quot;&gt;
    &lt;ul&gt;
        &lt;li&gt;列表1&lt;/li&gt;
        &lt;li&gt;列表2&lt;/li&gt;
    &lt;/ul&gt;
&lt;/div&gt;</pre>
</div>
<p>准备工作完毕，下面就是方法了（IE6~8浏览器直接设置<code>height:auto</code>即可）：</p>
<div>
<pre>var slideToggleTrans = function(element, display) { //  display表示默认更多展开元素是显示状态还是隐藏
    if (typeof window.screenX === "number") {
        <span style="color:green">// 现代浏览器</span>
        element.addEventListener(&quot;click&quot;, function() {
            display = !display;
            var rel = this.getAttribute(&quot;data-rel&quot;), eleMore = document.querySelector(&quot;#&quot; + rel);    

            eleMore &amp;&amp; (eleMore.style.height = display? (function() {
                var height = 0;
                Array.prototype.slice.call(eleMore.childNodes).forEach(function(child) {
                    if (child.nodeType === 1) {
                        var oStyle = window.getComputedStyle(child);
                        height = child.clientHeight + (parseInt(oStyle.borderTopWidth) || 0) + (parseInt(oStyle.borderBottomWidth) || 0);
                    }
                });
                return height;
            })() + &quot;px&quot;: &quot;0px&quot;);
        });
    } else {
        <span style="color:green">// IE6-IE8浏览器</span>
        element.attachEvent(&quot;onclick&quot;, function() {
            display = !display;
            var rel = element.getAttribute(&quot;data-rel&quot;), eleMore = document.getElementById(rel);
            eleMore &amp;&amp; (eleMore.style.height = display? &quot;auto&quot;: &quot;0px&quot;);
            return false;
        });
    }
};</pre>
</div>
<p>触发按钮，以及对应的显示隐藏元素，通过按钮元素上面自定义的”<code>data-rel</code>“属性相关联。<br>
<img alt="data-rel与显隐元素的id相关联 张鑫旭-鑫空间-鑫生活" src="http://image.zhangxinxu.com/image/blog/201210/2012-10-19_143055.png" title="data-rel与显隐元素的id相关联" width="578" height="179"></p>
<p>您可以狠狠地点击这里：<a href="http://www.zhangxinxu.com/study/201210/slide-transition-common-method.html">渐进使用transition的显隐公共方法demo</a></p>
<p>不支持transition的直接“啪啪”显示，支持transition的“呼呼”显示。渐进增强，简单又高性能。</p>
<p><strong>需要额外说明的</strong></p>
<ol>
<li>以上公共的<code>slideToggleTrans</code>方法如果直接用在实际项目中，是有所欠缺的。缺在哪里呢？就是没有添加回调。因此，您可能需要添加一个额外的参数以及两行代码：
<div>
<pre>var slideToggleTrans = function(element, display, callback) {
    <span style="color:green">// click事件中: ...; callback.call(element, eleMore, display);</span>
}</pre>
</div>
</li>
<li>我们平时显隐更多地是通过<code>display</code>属性控制。然而，<code>display</code>无法触发transition动画。如果我们先设置<code>display:block</code>，再去改变高度，行也行。但是，这种感觉就像是：平时用地下水冲马桶的，后来自来水也能冲干净，但非要再用地下水冲一次。而且，在脚本的编写上，更折腾（<code>display:none</code>的元素无法获取子元素的真实高度）。<br>
还是那句话，为了让一切都显得那么简单，我们要稍微改变下习惯，学习使用<code>height</code>控制元素的显隐。</li>
<li>以上公共方法不依赖任何JavaScript库。我这里想说的不是称赞其灵活性，而是想说，我们平时开发，都应该有自己的JS框架的。因此，上述代码有必要在自己框架基础上进行一番整改，可以大大简单代码；同时提高参数的可定制性(data-rel, 回调等)；以及其他相关扩展——如ajax更多加载的动画显示等！<br>
这里的，仅仅是最简单原始的，抛砖引玉而已。</li>
<li>定高其实是件伴随风险的事情，因为会有这样的情况：1. 页面内容宽度自适应，窄屏下元素应该更高；2. 更多元素内部还有类似展开收起的交互，高度可变。<br>
因此，我们还需要额外处理。情况1，需要在动画结束的时候，设置height: auto; 情况2则是，内部再出现2次交互的时候，再次改动元素的高度值（自动触发动画）。</li>
</ol>
<h3>七、这是一个平淡的结语</h3>
<p>我脑中突然冒出两个名字：“社会框架”和“团队框架”。“社会框架”就像我们使用的完整版的jQuery，因为其要兼容千万开发者受众各种环境，各种使用情况，因此，变得庞大是在所难免的。但是，如果放在单一的团队的，很多很多的东西都是多余的。因为，团队中可以有一个名叫“规范”的东西进行约束。——比方说，这种UI交互，我们的HTML结构上需要注意什么，需要什么样的CSS配合就可以使用极简的JS代码实现我们想要的效果等等。</p>
<p>说点更实际的，本文的内容，如果要让我写一个面向万千使用者的transition动画插件，我需要考虑的就非常多：<br>
1. 有的用户喜欢默认设置<code>display:none</code>, 以及其他CSS属性（例如其对容器设置了会冲突的overflow:auto）;<br>
2. 显隐元素里面就是文本，或者是列表子节点们。我需要获取各种情况下子元素们的高度。这很头疼；<br>
3. 交互场景千千万。显隐元素内部还有其他域高度打交道的交互，我该如何处理；<br>
4. 等等其他N多……</p>
<p>要全部兼顾上面的问题，可以想象，我的JS代码不是几十行就可以搞定了。肯定会有很多与核心逻辑不相干的代码，去处理这些个特殊情况。于是，洋洋洒洒N多代码，这是我很不喜欢的，大家也一定不会喜欢的。</p>
<p>但是，如果code只是面向一个规范的团队，或是自己内部小组，OK，我们就可以通过一些约定俗成，大大简化我们的代码，我们的开发，最后是大家都开心！</p>
<p>我越来越不喜欢过于肥胖的jQuery了！</p>
<p>原创文章，转载请注明来自<a href="http://www.zhangxinxu.com/">张鑫旭-鑫空间-鑫生活</a>[<a href="http://www.zhangxinxu.com/">http://www.zhangxinxu.com</a>]<br>
本文地址：<a href="http://www.zhangxinxu.com/wordpress/?p=2717">http://www.zhangxinxu.com/wordpress/?p=2717</a></p>
<p>（本篇完）</p>
<div>有话要说，点击<a href="http://www.zhangxinxu.com/wordpress/2012/10/more-display-show-hide-tranisition/#response">这里</a>发表评论。</div>