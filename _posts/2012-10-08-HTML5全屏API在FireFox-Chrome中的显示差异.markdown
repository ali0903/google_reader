---
layout: post
title:  "HTML5全屏API在FireFox/Chrome中的显示差异"
date:   2012-10-08 21:30:25
author: 张 鑫旭
categories: program
---

## HTML5全屏API在FireFox/Chrome中的显示差异
### by 张 鑫旭
### at 2012-10-08 21:30:25
### original <http://www.zhangxinxu.com/wordpress/2012/10/html5-full-screen-api-firefox-chrome-difference/>

<p>by <a href="http://www.zhangxinxu.com/">zhangxinxu</a> from <a href="http://www.zhangxinxu.com/">http://www.zhangxinxu.com</a><br>
本文地址：<a href="http://www.zhangxinxu.com/wordpress/?p=2679">http://www.zhangxinxu.com/wordpress/?p=2679</a></p>
<h3>一、人人网上……</h3>
<p>今天，在人人网上看同学晒得照片的时候，发现了这个按钮：<br>
<img alt="人人网上新出现的图片处理图标 张鑫旭-鑫空间-鑫生活" src="http://image.zhangxinxu.com/image/blog/201210/2012-10-08_194122.png" title="人人网上新出现的图片处理图标" width="326" height="156"></p>
<p>这个按钮是？我移了好几遍，没有<code>title</code>提示。图形也不具有代表性，一个圈圈意思是？——圈人？？是不是啊？？不敢点，点了不知道会发生什么，浏览器会不会关掉！！我思想斗争做了很久，终于弱弱地点了一下~~</p>
<p><img src="http://image.zhangxinxu.com/image/emtion/sweat.png" width="150" height="150"></p>
<p>草骆驼，居然是——全屏显示的提示：<br>
<img alt="全屏显示的提示 张鑫旭-鑫空间-鑫生活" src="http://image.zhangxinxu.com/image/blog/201210/2012-10-08_195501.png" title="全屏显示的提示" width="575" height="413"></p>
<p>人人兄，使用新技术固然值得提倡，这个圈圈也不加个提示——让人知道点了之后是全屏啊！！否则，俺们脆弱的小心脏根本承受不起啊——屏幕立马黑了个通透——还以为盖茨发现我的系统是盗版的黑了我的屏呢！！！</p>
<p><img src="http://image.zhangxinxu.com/image/emtion/surprise.png" width="150" height="150"></p>
<p>好吧，玩完两把三国杀，情绪稳定了，回到正题。人人网这个全屏效果就是使用的HTML5的全屏API，使用其实很easy的！对于全屏API，年初的时候就有耳闻，但未做测试与了解，现在，人人君已经用在实际项目中了，显然，有必要了解一番，可以在自己的项目中耍耍酷，过过HTML5的瘾。</p>
<h3>二、相关文章以及一些技术点</h3>
<p>搜了下，介绍的文章还不少，您可以参考：</p>
<ul>
<li>武方博原创的<a href="http://www.wufangbo.com/html5-quanping/">html5实现全屏的api方法</a>一文。文章前面属于备忘性质的内容，没看头；不过，最后的心得很不错，实际应用经验，值得一看。</li>
<li>heero翻译的“<a href="http://heeroluo.net/Article/Detail/97">[译]原生全屏Javascript API</a>”。去年时候内容，稍微old的点，多理论，有值得注意的知识点，能了解API的大概。</li>
<li>sitePoint上的”<a href="http://www.sitepoint.com/html5-full-screen-api/">How to Use the HTML5 Full-Screen API</a>“, 较新的文章吗，跟实际应用走的也很近，有demo，更值得参考。</li>
</ul>
<p>如果您正好处于节后综合症发病期，懒得点击上面链接，也可以看下面我精简出来的一些实用的东西：</p>
<ol>
<li>全屏效果JavaScript实现</li>
<li>全屏效果<code>click</code>, <code>mousedwon</code>, <code>mouseup</code>事件才能触发</li>
<li>全屏方法为：<code>全屏元素.requestFullScreen()</code>; 目前不同的浏览器需要不同的前缀，如<code>mozRequestFullScreen</code>或<code>webkitRequestFullScreen</code></li>
<li>取消全屏为：<code>全屏元素.cancelFullScreen()</code>; 同样，不同浏览器需要添加私有前缀，如<code>mozCancelFullScreen</code>或<code>webkitCancelFullScreen</code>.</li>
<li>可以通过<code>document.fullScreen</code>判断浏览器是否处于全屏状态。注意：FireFox和Chrome写法上有不同，Webkit内核浏览器需要增加个<code>is</code>, 如<code>document.webkitIsFullScreen</code>; 而Firefox则是<code>document.mozFullScreen</code>.</li>
<li><code>:full-screen{}</code>用在CSS代码中，可以控制全屏元素（及其子元素）全屏状态时的样式。同样，不同浏览器不同前缀。如<code>:-moz-full-screen{}</code>或<code>:- webkit-full-screen{}</code>.</li>
<li>一个页面如果有多个全屏元素，在CSS控制的时候，可以使用类似<code>#element:full-screen{}</code>的选择器分别控制。</li>
</ol>
<p>如果以上密密麻麻的文字看得你头大眼花，换个轻松的浏览，您可以狠狠地点击这里：<a href="http://www.zhangxinxu.com/study/201210/html5-full-screen-api.html">HTML5 full-screen全屏API测试demo</a></p>
<p>点击demo页面中，万年不变的张含韵照片，即可触发全屏浏览提示，类似下面：<br>
<img alt="全屏浏览器张含韵的照片截图 张鑫旭-鑫空间-鑫生活" src="http://image.zhangxinxu.com/image/blog/201210/2012-10-08_203401.png" title="全屏浏览器张含韵的照片截图" width="464" height="349"></p>
<p>该demo几乎均遵循了上面概要的一些技术tips, 完整代码demo页面上均有，不难懂的，大家有兴趣可以看看。</p>
<p>如果您想在实际项目中使用HTML5全屏API，demo页面中出现的这个方法必不可少（可以兼容以后N多年的HTML5发展）：</p>
<div>
<pre>var runPrefixMethod = function(element, method) {
    var usablePrefixMethod;
    ["webkit", "moz", "ms", "o", ""].forEach(function(prefix) {
        if (usablePrefixMethod) return;
        if (prefix === "") {
            <span style="color:green">// 无前缀，方法首字母小写</span>
            method = method.slice(0,1).toLowerCase() + method.slice(1);

        }

        var typePrefixMethod = typeof element[prefix + method];

        if (typePrefixMethod + "" !== "undefined") {
            if (typePrefixMethod === "function") {
                usablePrefixMethod = element[prefix + method]();
            } else {
                usablePrefixMethod = element[prefix + method];
            }
        }
    });

    return usablePrefixMethod;
};
</pre>
</div>
<p>既是方法执行（如果是方法），又是属性判断（是否支持属性），很实用，可以直接拷贝粘贴使用。</p>
<p>OK，这里所说的东西，其实上面几篇参考文章都有说过，没意思！现在目光转移到下面，呼吸一些新鲜的空气~~<br>
<img src="http://image.zhangxinxu.com/image/emtion/smile.png" width="150" height="150"></p>
<h3>三、FireFox/Chrome中的显示差异</h3>
<p><strong>表象的差异：</strong><br>
表象的差异就是是否支持全屏提示的差异了，FireFox浏览器是个大框框（参见上面截图），而Chrome浏览器下是个实色背景（参见下截图）：<br>
<img alt="Chrome浏览器下是否允许全屏的提示 张鑫旭-鑫空间-鑫生活" src="http://image.zhangxinxu.com/image/blog/201210/2012-10-08_205130.png" title="Chrome浏览器下是否允许全屏的提示" width="463" height="350"></p>
<p>浏览器生产商的喜好，没什么好说的。</p>
<p><strong>更细致的差异：</strong><br>
如果您观察够仔细，您可能会发现，在全屏状态下，FireFox浏览器下点击界面的任意位置，都会回到正常状态；但是Chrome浏览器下，只有点击图片才能回到正常状态。下面两张图是同一位置，鼠标手形的差异对比：<br>
<img alt="FireFox浏览器下图片外部的鼠标手形 张鑫旭-鑫空间-鑫生活" src="http://image.zhangxinxu.com/image/blog/201210/2012-10-08_205529.png" title="FireFox浏览器下图片外部的鼠标手形" width="242" height="163"> <img alt="Chrome浏览器图片外部的鼠标手形截图 张鑫旭-鑫空间-鑫生活" src="http://image.zhangxinxu.com/image/blog/201210/2012-10-08_205636.png" title="Chrome浏览器图片外部的鼠标手形截图" width="232" height="132"></p>
<p><strong>深层次的原因：</strong><br>
虽然，demo页面中，背景都是全屏的黑色，图片效果一样，高度60%放大显示，垂直且水平居中，但是，其背后实现的机制却是很不一样的。</p>
<p>FireFox浏览器对全屏元素进行的一些CSS属性的强设置，打开FireBug，查看系统默认样式，您会看到：<br>
<img alt="FireFox浏览器中默认对全屏元素的CSS设置 张鑫旭-鑫空间-鑫生活" src="http://image.zhangxinxu.com/image/blog/201210/2012-10-08_210718.png" title="FireFox浏览器中默认对全屏元素的CSS设置" width="554" height="267"></p>
<p>也就是，元素宽高100%显示，黑色背景，固定定位——这也是为什么FireFox浏览器下点击屏幕任意位置会退出全屏——因为绑定了事件的全屏元素满屏显示了。</p>
<p>背景色非强设置，通过如下CSS，我们就可以进行修改：</p>
<div>
<pre>:-moz-full-screen {
    background-color: #fff;
}</pre>
</div>
<p>结果，就是下图这个样子：<br>
<img alt="FireFox浏览器下全屏背景白色的显示 张鑫旭-鑫空间-鑫生活" src="http://image.zhangxinxu.com/image/blog/201210/2012-10-08_211215.png" title="FireFox浏览器下全屏背景白色的显示" width="464" height="336"></p>
<p>而Chrome浏览器下，虽然默认对全屏元素也有CSS设置，但是寥寥：<br>
<img alt="Chrome浏览器下全屏元素的默认CSS设置 张鑫旭-鑫空间-鑫生活" src="http://image.zhangxinxu.com/image/blog/201210/2012-10-08_211425.png" title="Chrome浏览器下全屏元素的默认CSS设置" width="320" height="89"></p>
<p>好吧，基本上就是两个酱油CSS设置，背景色还是白色的。</p>
<p>现在问题来了？为何两个浏览器默认的全屏CSS样式不一样，但是，都是黑色背景，图片垂直水平居中呢！！！</p>
<p><strong>解释：</strong>FireFox浏览器下的黑色背景就是全屏元素，其中图片的居中对齐是通过CSS控制的（:after伪类生成元素+vertical-align:middle实现），例如去掉<code>text-align:center</code>图片就不水平居中了；而Chrome的黑色背景属于系统的东西，其全屏元素似乎被浏览器劫持，默认状况下永远屏幕居中显示（去掉<code>text-align:center</code>图片依然水平居中）——脱离了常规CSS理解——如默认的<code>display:block</code>状态似乎变成了<code>display:inline-block</code>等。</p>
<p>然而，我们是有办法让Chrome表现得跟FireFox浏览器一样地，就是增加一样FireFox浏览器默认有的样式，如：</p>
<div>
<pre>:-webkit-full-screen {
    background-color: black;
    position: fixed;
    top: 0;
    right: 0;
    left: 0;
    bottom: 0;
}</pre>
</div>
<p>于是，这样子，即使在Chrome浏览器下，点击屏幕的任意位置，全屏状态都会退出！</p>
<p><img src="http://image.zhangxinxu.com/image/emtion/cute.png" width="150" height="150"></p>
<p>原创文章，转载请注明来自<a href="http://www.zhangxinxu.com/">张鑫旭-鑫空间-鑫生活</a>[<a href="http://www.zhangxinxu.com/">http://www.zhangxinxu.com</a>]<br>
本文地址：<a href="http://www.zhangxinxu.com/wordpress/?p=2679">http://www.zhangxinxu.com/wordpress/?p=2679</a></p>
<p>（本篇完）</p>
<div>有话要说，点击<a href="http://www.zhangxinxu.com/wordpress/2012/10/html5-full-screen-api-firefox-chrome-difference/#response">这里</a>发表评论。</div>