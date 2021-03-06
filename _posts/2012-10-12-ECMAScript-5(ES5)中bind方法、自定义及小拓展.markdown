---
layout: post
title:  "ECMAScript 5(ES5)中bind方法、自定义及小拓展"
date:   2012-10-12 17:27:37
author: 张 鑫旭
categories: program
---

## ECMAScript 5(ES5)中bind方法、自定义及小拓展
### by 张 鑫旭
### at 2012-10-12 17:27:37
### original <http://www.zhangxinxu.com/wordpress/2012/10/ecmascript-es5-bind-array-slice-call-apply/>

<p>by <a href="http://www.zhangxinxu.com/">zhangxinxu</a> from <a href="http://www.zhangxinxu.com/">http://www.zhangxinxu.com</a><br>
本文地址：<a href="http://www.zhangxinxu.com/wordpress/?p=2689">http://www.zhangxinxu.com/wordpress/?p=2689</a></p>
<p>ECMAScript 5.1 (或仅 ES5) 是ECMAScript(基于JavaScript的规范)标准最新修正。其中，新增了一个名叫<code>bind</code>函数扩展方法()，以前有提过，<a href="http://www.zhangxinxu.com/wordpress/2012/01/introducing-ecmascript-5-1/#function-bind">点击这里查看详细</a>。</p>
<p>不过，之前的介绍纯粹的翻译，含糊其辞，水土不服，这里，换身接地气的装束，让大家好好了解了解。</p>

<h3>一、对函数扩展的bind方法</h3>
<p>狄仁杰：元芳，你对<code>bind</code>怎么看？<br>
<img alt="狄仁杰" src="http://image.zhangxinxu.com/image/blog/201210/direnjie.jpg" title="狄仁杰" width="180" height="180"></p>
<p>李元芳：大人，这一定是个很有用的方法！<br>
<img alt="李元芳" src="http://image.zhangxinxu.com/image/blog/201210/liyuanfang.jpg" title="李元芳" width="180" height="180"></p>
<p>狄仁杰：我可以断定，<code>bind</code>是一个方法！<br>
<img alt="狄仁杰" src="http://image.zhangxinxu.com/image/blog/201210/direnjie.jpg" title="狄仁杰" width="180" height="180"></p>
<p>李元芳：大人未经实践就知道<code>bind</code>是一个方法，真乃神人也！<br>
<img alt="李元芳" src="http://image.zhangxinxu.com/image/blog/201210/liyuanfang.jpg" title="李元芳" width="180" height="180"></p>
<p>狄仁杰：而且是个扩展方法！<br>
<img alt="狄仁杰" src="http://image.zhangxinxu.com/image/blog/201210/direnjie.jpg" title="狄仁杰" width="180" height="180"></p>
<p>李元芳：什么？扩展方法？大人，属下愚钝，听不明白！<br>
<img alt="李元芳" src="http://image.zhangxinxu.com/image/blog/201210/liyuanfang.jpg" title="李元芳" width="180" height="180"></p>
<p>狄仁杰：元芳，你可认识jQuery?<br>
<img alt="狄仁杰" src="http://image.zhangxinxu.com/image/blog/201210/direnjie.jpg" title="狄仁杰" width="180" height="180"></p>
<p>李元芳：jQuery? 大人，莫非就是最近江湖广为流传，从米国传过来的开发利器？<br>
<img alt="李元芳" src="http://image.zhangxinxu.com/image/blog/201210/liyuanfang.jpg" title="李元芳" width="180" height="180"></p>
<p>狄仁杰：正是！你可知其中也有<code>bind</code>方法？<br>
<img alt="狄仁杰" src="http://image.zhangxinxu.com/image/blog/201210/direnjie.jpg" title="狄仁杰" width="180" height="180"></p>
<p>李元芳：什么？这西洋利器也有<code>bind</code>方法。属下孤陋寡闻，望大人指点明津！<br>
<img alt="李元芳" src="http://image.zhangxinxu.com/image/blog/201210/liyuanfang.jpg" title="李元芳" width="180" height="180"></p>
<p>狄仁杰：jQuery的包装器扩展了一个<code>bind</code>方法，可以用来绑定事件。<br>
<img alt="狄仁杰" src="http://image.zhangxinxu.com/image/blog/201210/direnjie.jpg" title="狄仁杰" width="180" height="180"></p>
<p>李元芳：大人果然学贯中外，居然可以知道jQuery的<code>bind</code>是用来绑定事件的，属下实在佩服！！<br>
<img alt="李元芳" src="http://image.zhangxinxu.com/image/blog/201210/liyuanfang.jpg" title="李元芳" width="180" height="180"></p>
<p>狄仁杰：元芳，你速去豆瓣弄件jQuery的浅色内衣。<br>
<img alt="狄仁杰" src="http://image.zhangxinxu.com/image/blog/201210/direnjie.jpg" title="狄仁杰" width="180" height="180"></p>
<p>李元芳：大人，内衣弄来了，长这样：</p>
<div>
<pre>$("#test").bind("click", function() {
   console.log("内衣你妹！");
});</pre>
</div>
<p><img alt="李元芳" src="http://image.zhangxinxu.com/image/blog/201210/liyuanfang.jpg" title="李元芳" width="180" height="180"></p>
<p>狄仁杰：呵呵，这就证实了jQuery的<code>bind</code>是对包装器的扩展！<br>
<img alt="狄仁杰" src="http://image.zhangxinxu.com/image/blog/201210/direnjie.jpg" title="狄仁杰" width="180" height="180"></p>
<p>李元芳：大人，果然事件结果都被你一一说中！<br>
<img alt="李元芳" src="http://image.zhangxinxu.com/image/blog/201210/liyuanfang-cute.jpg" title="李元芳" width="180" height="180"></p>
<p>狄仁杰：不过jQuery的<code>bind</code>并不是幕后主谋！<br>
<img alt="狄仁杰" src="http://image.zhangxinxu.com/image/blog/201210/direnjie.jpg" title="狄仁杰" width="180" height="180"></p>
<p>李元芳：纳尼！发酵了这么久，原来不是茅台是酱油！大人，那幕后主谋究竟是？<br>
<img alt="李元芳" src="http://image.zhangxinxu.com/image/blog/201210/liyuanfang.jpg" title="李元芳" width="180" height="180"></p>
<p>狄仁杰：元芳，你可知<code>prototype.js</code>以及<code>MooTools.js</code>中的<code>bind</code>方法？<br>
<img alt="狄仁杰" src="http://image.zhangxinxu.com/image/blog/201210/direnjie.jpg" title="狄仁杰" width="180" height="180"></p>
<p>李元芳：大人，什么<code>prototype.js</code>还有<code>MooTools.js</code>？小的听糊涂了。<br>
<img alt="李元芳" src="http://image.zhangxinxu.com/image/blog/201210/liyuanfang.jpg" title="李元芳" width="180" height="180"></p>
<p>狄仁杰：这可不怪你，你前些日子忙着和如燕造人，江湖消息没能及时获取。<br>
<img alt="狄仁杰" src="http://image.zhangxinxu.com/image/blog/201210/direnjie.jpg" title="狄仁杰" width="180" height="180"></p>
<p>李元芳：大人，造人的事情你都知道啊！<br>
<img alt="李元芳" src="http://image.zhangxinxu.com/image/blog/201210/liyuanfang-han.jpg" title="李元芳" width="180" height="180"></p>
<p>狄仁杰：大人我知道的事情可多着呢！<code>prototype.js</code>和<code>MooTools.js</code>都是米国过来的开发利器，不过江湖名声威望不及jQuery；但仍有不少帮派以及小村寨使用之。<br>
<img alt="狄仁杰" src="http://image.zhangxinxu.com/image/blog/201210/direnjie.jpg" title="狄仁杰" width="180" height="180"></p>
<p>李元芳：原来是这样，怪不得大人让我去取豆瓣的内衣而不是点评的！大人果然深谋远虑，那这两个利器的<code>bind</code>方法是？<br>
<img alt="李元芳" src="http://image.zhangxinxu.com/image/blog/201210/liyuanfang.jpg" title="李元芳" width="180" height="180"></p>
<p>狄仁杰：元芳，你这个问题问得好！你可以看这一集的标题，就会知道答案了！<br>
<img alt="狄仁杰" src="http://image.zhangxinxu.com/image/blog/201210/direnjie.jpg" title="狄仁杰" width="180" height="180"></p>
<p>李元芳：大人，导演插了太多图片，距离太远看不到了！<br>
<img alt="李元芳" src="http://image.zhangxinxu.com/image/blog/201210/liyuanfang.jpg" title="李元芳" width="180" height="180"></p>
<p>狄仁杰：恩？还有这种事！来人，给元芳拿个望远镜过来！<br>
<img alt="狄仁杰" src="http://image.zhangxinxu.com/image/blog/201210/direnjie.jpg" title="狄仁杰" width="180" height="180"></p>
<p>路人甲递过一个御用望远镜……<br>
<img alt="望远镜" src="http://image.zhangxinxu.com/image/blog/201210/wyj-hand.jpg" title="望远镜" width="180" height="180"></p>
<p>李元芳：原来如此，属下一下子明白了——<code>MooTools.js</code>的<code>bind</code>方法是对函数的扩展！大人这么远都能明察秋毫，真乃神人也！！<br>
<img alt="李元芳" src="http://image.zhangxinxu.com/image/blog/201210/liyuanfang-wyj.jpg" title="李元芳" width="180" height="180"> <img alt="标题文字" src="http://image.zhangxinxu.com/image/blog/201210/wyj-text.png" title="标题文字" width="180" height="180"></p>
<p>狄仁杰：元芳，你可知这里的<code>bind</code>如何使用？又有何作用呢？<br>
<img alt="狄仁杰" src="http://image.zhangxinxu.com/image/blog/201210/direnjie.jpg" title="狄仁杰" width="180" height="180"></p>
<p>李元芳：这……恕属下再次愚钝，还请大人指明！<br>
<img alt="李元芳" src="http://image.zhangxinxu.com/image/blog/201210/liyuanfang.jpg" title="李元芳" width="180" height="180"></p>
<p>狄仁杰：元芳，下面这段代码你怎么看？</p>
<div>
<pre>var button = document.getElementById("button");
button.onclick = function() {
    alert(this.id); <span style="color:green">// 弹出button</span>
};</pre>
</div>
<p><img alt="狄仁杰" src="http://image.zhangxinxu.com/image/blog/201210/direnjie.jpg" title="狄仁杰" width="180" height="180"></p>
<p>李元芳：大人，虽然我不懂洋文，但我可以确定这段代码一定藏有天大的玄机。<br>
<img alt="李元芳" src="http://image.zhangxinxu.com/image/blog/201210/liyuanfang.jpg" title="李元芳" width="180" height="180"></p>
<p>狄仁杰：从这段代码可以推断，点击某个机关后会弹出一个暗器！<br>
<img alt="狄仁杰" src="http://image.zhangxinxu.com/image/blog/201210/direnjie.jpg" title="狄仁杰" width="180" height="180"></p>
<p>李元芳：大人，暗藏机关您都可以一眼看出来啊！！属下终于明白周星驰常说的”我对你的敬仰犹如滔滔江水连绵不绝，又如黄河泛滥一发不可收拾…”的含义了……<br>
<img alt="李元芳" src="http://image.zhangxinxu.com/image/blog/201210/liyuanfang-cute.jpg" title="李元芳" width="180" height="180"></p>
<p>狄仁杰：周星驰是谁？<br>
<img alt="狄仁杰" src="http://image.zhangxinxu.com/image/blog/201210/direnjie-ask.jpg" title="狄仁杰" width="180" height="180"></p>
<p>李元芳：大人，我也不认识。剧本台词是这样，我就照念了~~<br>
<img alt="李元芳" src="http://image.zhangxinxu.com/image/blog/201210/liyuanfang.jpg" title="李元芳" width="180" height="180"></p>
<p>狄仁杰：元芳，下面这段代码你又怎么看？</p>
<div>
<pre>var button = document.getElementById("button"),
    text = document.getElementById("text");
button.onclick = function() {
    alert(this.id); <span style="color:green">// 弹出text</span>
}.bind(text);</pre>
</div>
<p><img alt="狄仁杰" src="http://image.zhangxinxu.com/image/blog/201210/direnjie.jpg" title="狄仁杰" width="180" height="180"></p>
<p>李元芳：根据大人之前的推断，点击某个机关后会弹出一个暗器！<br>
<img alt="李元芳" src="http://image.zhangxinxu.com/image/blog/201210/liyuanfang.jpg" title="李元芳" width="180" height="180"></p>
<p>狄仁杰：……<br>
<img alt="狄仁杰" src="http://image.zhangxinxu.com/image/blog/201210/direnjie.jpg" title="狄仁杰" width="180" height="180"></p>
<p>李元芳：而且这个名字的暗器叫做”text”.<br>
<img alt="李元芳" src="http://image.zhangxinxu.com/image/blog/201210/liyuanfang.jpg" title="李元芳" width="180" height="180"></p>
<p>狄仁杰：……<br>
<img alt="狄仁杰" src="http://image.zhangxinxu.com/image/blog/201210/direnjie.jpg" title="狄仁杰" width="180" height="180"></p>
<p>李元芳：但是，奇怪啊，为什么弹出暗器是”text”呢？<br>
<img alt="李元芳" src="http://image.zhangxinxu.com/image/blog/201210/liyuanfang.jpg" title="李元芳" width="180" height="180"></p>
<p>狄仁杰：……<br>
<img alt="狄仁杰" src="http://image.zhangxinxu.com/image/blog/201210/direnjie.jpg" title="狄仁杰" width="180" height="180"></p>
<p>李元芳：莫非这就是bind的作用——改变了函数内部的上下文<code>this</code>？！<br>
<img alt="李元芳" src="http://image.zhangxinxu.com/image/blog/201210/liyuanfang.jpg" title="李元芳" width="180" height="180"></p>
<p>狄仁杰：……元芳！！如燕喊你回家吃饭！！<br>
<img alt="狄仁杰" src="http://image.zhangxinxu.com/image/blog/201210/direnjie-fire.jpg" title="狄仁杰" width="180" height="180"></p>
<p>李元芳：大人，您这是……怎~么~了~？？<br>
<img alt="李元芳" src="http://image.zhangxinxu.com/image/blog/201210/liyuanfang.jpg" title="李元芳" width="180" height="180"></p>
<p>旁白：<span style="color:#666">元芳此时定睛看了看短词少句的剧本，脸色立马变大便，眼睛直接变成了火影中的白眼……</span></p>
<p>李元芳：——擦，吃翔了我！！sorry啊，大人，我台词看错行了！！求鞭笞！<br>
<img alt="李元芳" src="http://image.zhangxinxu.com/image/blog/201210/liyuanfang-jing.jpg" title="李元芳" width="180" height="180"></p>
<p>狄仁杰：（稳定了情绪，长舒了一口气~~）我们这是荒诞搞笑剧，不是SM剧，鞭笞什么的就算了！不过下面我要多讲几句，寻求心理平衡！<br>
<img alt="狄仁杰" src="http://image.zhangxinxu.com/image/blog/201210/direnjie.jpg" title="狄仁杰" width="180" height="180"></p>
<p>李元芳：大人应该的！<img src="http://mat1.gtimg.com/www/mb/images/face/76.gif"><br>
<img alt="李元芳" src="http://image.zhangxinxu.com/image/blog/201210/liyuanfang.jpg" title="李元芳" width="180" height="180"></p>
<p>狄仁杰：函数扩展<code>bind</code>使用如下：
<div>
<pre>function() {}.bind(thisArg [, arg1 [, arg2, …]]);</pre>
</div>
<p>翻译成唐文就是：
<div>
<pre>函数.bind(this上下文参数, 普通参数1, 普通参数2, ...);</pre>
</div>
<p>根据我的实践与调查，<code>bind</code>方法尤其在对象字面量编程风格下尤为受用。</p>
<div>
<pre>var OOO = {
    color: "#cd0000",
    element: $("#text"),
    events: function() {
        $("input[type='button']").addEventListener("click", function(e) {
            this.element.style.color = this.color;
        }.bind(this));
        return this;
    },
    init: function() {
        this.events();
    }
};</pre>
</div>
<p>元芳，你可知道《葵花宝典》？<br>
<img alt="狄仁杰" src="http://image.zhangxinxu.com/image/blog/201210/direnjie.jpg" title="狄仁杰" width="180" height="180"></p>
<p>李元芳：《葵花宝典》！是不是那个几百年之后，宫中那个名叫”无二弟郎”创建的一门神功？<br>
<img alt="李元芳" src="http://image.zhangxinxu.com/image/blog/201210/liyuanfang.jpg" title="李元芳" width="180" height="180"></p>
<p>狄仁杰：正是！你可知道，这门武功不是什么人都可以练的？<br>
<img alt="狄仁杰" src="http://image.zhangxinxu.com/image/blog/201210/direnjie.jpg" title="狄仁杰" width="180" height="180"></p>
<p>李元芳：什么？武功也会搞歧视……大人，那这门武功看哪些人不顺眼呢？<br>
<img alt="李元芳" src="http://image.zhangxinxu.com/image/blog/201210/liyuanfang.jpg" title="李元芳" width="180" height="180"></p>
<p>狄仁杰：那些可以造人的男人！<br>
<img alt="狄仁杰" src="http://image.zhangxinxu.com/image/blog/201210/direnjie.jpg" title="狄仁杰" width="180" height="180"></p>
<p>李元芳：……<br>
<img alt="李元芳" src="http://image.zhangxinxu.com/image/blog/201210/liyuanfang-han.jpg" title="李元芳" width="180" height="180"></p>
<p>狄仁杰：<code>bind</code>方法跟《葵花宝典》一样，不是谁都可以用的？<br>
<img alt="狄仁杰" src="http://image.zhangxinxu.com/image/blog/201210/direnjie.jpg" title="狄仁杰" width="180" height="180"></p>
<p>李元芳：哦？居然还有这等玄机！莫非<code>bind</code>方法我又不能使用？<br>
<img alt="李元芳" src="http://image.zhangxinxu.com/image/blog/201210/liyuanfang-han.jpg" title="李元芳" width="180" height="180"></p>
<p>狄仁杰：那倒不是。N多年前，有一族姓氏为ie的外人迁至我大唐，其目前留在世上的第<code>6~8</code>代后人是不能使用bind方法的，逐渐长大的第9代后人，以及还在襁褓中的第10代后人倒是可以使用！<br>
<img alt="狄仁杰" src="http://image.zhangxinxu.com/image/blog/201210/direnjie.jpg" title="狄仁杰" width="180" height="180"></p>
<p>李元芳：大人如此见多识广，真可谓当代奇人啊！那这IE6~8终日不得惶恐，岂不是很悲剧？<br>
<img alt="李元芳" src="http://image.zhangxinxu.com/image/blog/201210/liyuanfang.jpg" title="李元芳" width="180" height="180"></p>
<p>狄仁杰：就跟《葵花宝典》清了裤裆依然可以练一样，IE6~8也是有办法不被歧视，避免悲剧的！<br>
<img alt="狄仁杰" src="http://image.zhangxinxu.com/image/blog/201210/direnjie.jpg" title="狄仁杰" width="180" height="180"></p>
<p>李元芳：大人，那这个方法是？<br>
<img alt="李元芳" src="http://image.zhangxinxu.com/image/blog/201210/liyuanfang.jpg" title="李元芳" width="180" height="180"></p>
<p>狄仁杰：这个方法就是……<br>
<img alt="狄仁杰" src="http://image.zhangxinxu.com/image/blog/201210/direnjie.jpg" title="狄仁杰" width="180" height="180"></p>
<p>导演：咔！！本集拍完了，广告时间，大伙休息下，马上进入下集拍摄！<br>
<img alt="导演" src="http://image.zhangxinxu.com/image/blog/201210/zhouxingchi.jpg" title="导演" width="180" height="180"></p>
<p style="color:#888">//zxx: 下面为广告~~注意不要勿点~~嘻嘻~~</p>
<div style="width:468px;padding:5px;background-color:#cad5eb">

</div>
<h3>二、为IE6~8自定义bind方法</h3>
<p>李元芳：大人，您先~~<br>
<img alt="李元芳" src="http://image.zhangxinxu.com/image/blog/201210/liyuanfang.jpg" title="李元芳" width="180" height="180"></p>
<p>狄仁杰：通过对<code>Function</code>的<code>prototype</code>原型进行扩展，可以为IE6~8自定义<code>bind</code>方法。元芳，你可知如何自定义？<br>
<img alt="狄仁杰" src="http://image.zhangxinxu.com/image/blog/201210/direnjie.jpg" title="狄仁杰" width="180" height="180"></p>
<p>李元芳：大人，您是知道的，我不能抢你的风头。因此，属下不知，请大人道明真相！<br>
<img alt="李元芳" src="http://image.zhangxinxu.com/image/blog/201210/liyuanfang.jpg" title="李元芳" width="180" height="180"></p>
<p>狄仁杰：曾泰，去我书房把案头上的密函拿来。<br>
<img alt="狄仁杰" src="http://image.zhangxinxu.com/image/blog/201210/direnjie.jpg" title="狄仁杰" width="180" height="180"></p>
<p>曾泰：师傅，密函在此……<br>
<img alt="曾泰" src="http://image.zhangxinxu.com/image/blog/201210/zengtai.jpg" title="曾泰" width="180" height="180"> <img alt="密函" src="http://image.zhangxinxu.com/image/blog/201210/mihan.jpg" title="密函" width="392" height="288"></p>
<p>狄仁杰：曾泰，你给大伙儿念念。<br>
<img alt="狄仁杰" src="http://image.zhangxinxu.com/image/blog/201210/direnjie.jpg" title="狄仁杰" width="180" height="180"></p>
<p>曾泰：咳咳……恩恩……这个……那个……恩……啊……<br>
<img alt="曾泰" src="http://image.zhangxinxu.com/image/blog/201210/zengtai.jpg" title="曾泰" width="180" height="180"> </p>
<p>狄仁杰：曾泰，你这是舌头抽筋了？？<br>
<img alt="狄仁杰" src="http://image.zhangxinxu.com/image/blog/201210/direnjie.jpg" title="狄仁杰" width="180" height="180"></p>
<p>曾泰：恩师，下面的密函文字都是English, 我不认识字啊！</p>
<div>
<pre>if (!function() {}.bind) {
    Function.prototype.bind = function(context) {
        var self = this
            , args = Array.prototype.slice.call(arguments);

        return function() {
            return self.apply(context, args.slice(1));
        }
    };
}</pre>
</div>
<p><img alt="曾泰" src="http://image.zhangxinxu.com/image/blog/201210/zengtai.jpg" title="曾泰" width="180" height="180"></p>
<p>狄仁杰：曾泰，你的脑袋还是一如既往的秀逗，既然是密文，你就用密语念就行了！<br>
<img alt="狄仁杰" src="http://image.zhangxinxu.com/image/blog/201210/direnjie.jpg" title="狄仁杰" width="180" height="180"></p>
<p>曾泰：恩师当真高明！&amp;……#&amp;……￥&amp;*@￥%#…%#&amp;#%￥&amp;*……<br>
<img alt="曾泰" src="http://image.zhangxinxu.com/image/blog/201210/zengtai-liang.jpg" title="曾泰" width="180" height="180"></p>
<p>狄仁杰：元芳，这段密文你怎么看？<br>
<img alt="狄仁杰" src="http://image.zhangxinxu.com/image/blog/201210/direnjie.jpg" title="狄仁杰" width="180" height="180"></p>
<p>李元芳：我觉得这段密文后面一定有一个惊天大秘密！<br>
<img alt="李元芳" src="http://image.zhangxinxu.com/image/blog/201210/liyuanfang.jpg" title="李元芳" width="180" height="180"></p>
<p>狄仁杰：元芳，曾泰，你们都过来，顺便把你们的鼠标都移过来，狠狠地点击这里：<a href="http://www.zhangxinxu.com/study/201210/custom-bind-method-for-function.html">为函数自定义bind方法demo</a></p>
<p>然后点击那个名叫”点击我”的机关，如何？<br>
<img alt="狄仁杰" src="http://image.zhangxinxu.com/image/blog/201210/direnjie.jpg" title="狄仁杰" width="180" height="180"></p>
<p>李元芳：大人，我怎么变红了！！！<br>
<img alt="李元芳" src="http://image.zhangxinxu.com/image/blog/201210/liyuanfang-red.jpg" title="李元芳" width="180" height="180"></p>
<p>狄仁杰：呵呵，说明你可以使用bind方法！曾泰，我没记错的话，你就是ie家族第八代后人，后因坑爹致死跟妈姓才改姓曾，你也来试试机关！<br>
<img alt="狄仁杰" src="http://image.zhangxinxu.com/image/blog/201210/direnjie.jpg" title="狄仁杰" width="180" height="180"></p>
<p>曾泰：恩师，我也变红了！<br>
<span style="width:284px;margin:2px;background-color:#fff;font-size:12px">我勒个去，我还有如此悲惨的剧情设定啊！好吧，人在江湖漂，哪有不挨刀！被ie，被克父，被红了都认了！</span><img alt="曾泰" src="http://image.zhangxinxu.com/image/blog/201210/zengtai-red.jpg" title="曾泰" width="180" height="180"></p>
<p>狄仁杰：红了好啊，红了好啊！说明密函中的自定义方法是有效的！<br>
<img alt="IE8浏览器下点击按钮文字变红 张鑫旭-鑫空间-鑫生活" src="http://image.zhangxinxu.com/image/blog/201210/2012-10-12_155407.png" title="IE8浏览器下点击按钮文字变红" width="349" height="144"></p>
<p>机关对应的密文更能说明这一切：</p>
<div>
<pre>var eleBtn = document.getElementById("button")
    , eleText = document.getElementById("text");

eleBtn.onclick = function(color) {
    color = color || "#003399";
    this.style.color = color;
}.bind(eleText, "#cd0000");</pre>
</div>
<p><img alt="狄仁杰" src="http://image.zhangxinxu.com/image/blog/201210/direnjie.jpg" title="狄仁杰" width="180" height="180"></p>
<p>李元芳：不过大人，我还有不解，密文中<code>Array.prototype.slice.call(arguments)</code>是什么意思？<br>
<img alt="李元芳" src="http://image.zhangxinxu.com/image/blog/201210/liyuanfang-red.jpg" title="李元芳" width="180" height="180"></p>
<p>狄仁杰：这个嘛……<br>
<img alt="狄仁杰" src="http://image.zhangxinxu.com/image/blog/201210/direnjie.jpg" title="狄仁杰" width="180" height="180"></p>
<p>导演：咔！！本集over. 那个，我老婆催我回去早点休息了。因此，下一集我一个人来演就可以了。大家都散了吧，各回各家，各找各妈！<br>
<img alt="导演" src="http://image.zhangxinxu.com/image/blog/201210/zhouxingchi.jpg" title="导演" width="180" height="180"></p>
<p>李元芳/曾泰（异口同声）：导演，我们现在红得像个卫生巾一样，回不了家啊，先帮我们还原吧！<br>
<img alt="李元芳" src="http://image.zhangxinxu.com/image/blog/201210/liyuanfang-red.jpg" title="李元芳" width="180" height="180"> <img alt="曾泰" src="http://image.zhangxinxu.com/image/blog/201210/zengtai-red.jpg" title="曾泰" width="180" height="180"></p>
<p>导演：回不了家，那正好！路口红灯正好坏了，你们俩过去，不要浪费了资源！<br>
<img alt="导演" src="http://image.zhangxinxu.com/image/blog/201210/zhouxingchi.jpg" title="导演" width="180" height="180"></p>
<p>李元芳/曾泰：……<br>
<img alt="李元芳" src="http://image.zhangxinxu.com/image/blog/201210/liyuanfang-red-han.jpg" title="李元芳" width="180" height="180"> <img alt="曾泰" src="http://image.zhangxinxu.com/image/blog/201210/zengtai-red-han.jpg" title="曾泰" width="180" height="180"></p>
<h3>三、小拓展 – Array.prototype.slice.call</h3>
<p>查看jQuery的源代码，会看到类似：</p>
<div>
<pre>slice = Array.prototype.slice,</pre>
</div>
<p>或</p>
<div>
<pre>array = Array.prototype.slice.call( array, 0 );</pre>
</div>
<p>的代码。</p>
<p>干嘛用的呢？</p>
<p>作用就是：<strong>将类似数组的对象转换为真实的数组。</strong></p>
<p>真实数组具有slice方法，可以对数组进行浅复制（不影响原数组），返回的依然是数组。</p>
<p>类似数组虽然有length属性，可以使用<code>for</code>循环遍历，却不能直接使用slice方法，会报错！但是通过<code>Array.prototype.slice.call</code>则不会报错，本身（类似数组）被从头到尾slice复制了一遍——变成了真实数组！</p>
<p>上一集demo中函数内部的<code>arguments</code>是应该都熟知的类似数组，于是：</p>
<div>
<pre>Array.prototype.slice.call(arguments)</pre>
</div>
<p>所返回的就是完整参数的真实数组了！下面的code也就容易理解了。</p>
<p>至于为什么”类似数组不能直接slice, 而借助<code>Array.prototype</code>可以slice”我也不清楚其中深层次的原因，还希望有过相关研究的大人指点迷津！</p>
<p>原创文章，转载请注明来自<a href="http://www.zhangxinxu.com/">张鑫旭-鑫空间-鑫生活</a>[<a href="http://www.zhangxinxu.com/">http://www.zhangxinxu.com</a>]<br>
本文地址：<a href="http://www.zhangxinxu.com/wordpress/?p=2689">http://www.zhangxinxu.com/wordpress/?p=2689</a></p>
<p>（本篇完）</p>
<div>有话要说，点击<a href="http://www.zhangxinxu.com/wordpress/2012/10/ecmascript-es5-bind-array-slice-call-apply/#response">这里</a>发表评论。</div></p></p>