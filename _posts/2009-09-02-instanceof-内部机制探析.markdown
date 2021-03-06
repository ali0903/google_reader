---
layout: post
title:  "instanceof 内部机制探析"
date:   2009-09-02 22:17:25
author: lifesinger
categories: program
---

## instanceof 内部机制探析
### by lifesinger
### at 2009-09-02 22:17:25
### original <http://lifesinger.org/blog/2009/09/instanceof-mechanism/>

<p>在 JavaScript 中，可以用 instanceof 来判断一个对象是不是某个类或其子类的实例。比如：</p>
<pre>
// 代码 1
function Pig() {}
var pig = new Pig();
alert(pig instanceof Pig); // =&gt; true

function FlyPig() {}
FlyPig.prototype = new Pig();
var flyPig = new FlyPig();
alert(flyPig instanceof Pig); // =&gt; true
</pre>
<p>来看另一段代码：</p>
<pre>
// 代码 2
function Pig() { Pig.prototype = {/* some code */} }
var pig = new Pig();
alert(pig instanceof Pig); // =&gt; false
</pre>
<p>为何上面的猪 pig 不再是猪 Pig 了呢？<span></span></p>
<p>当一个对象是某个类的实例时，意味着这个对象具有该类的方法和属性。在 JavaScript 中，一个猪类的特性体现在原型中：</p>
<pre>
// 代码 3
function Pig() {}
Pig.prototype = {
    &quot;吃猪食&quot;: function() {},
    &quot;睡觉&quot;: function() {},
    &quot;长膘&quot;: function() {}
};
var pig = new Pig();
alert(pig instanceof Pig); //=&gt; true
</pre>
<p>如果动态改变了猪的特性，让猪变成了牛：</p>
<pre>
// 代码 4
Pig.prototype = {
    &quot;吃草&quot;: function() {},
    &quot;犁田&quot;: function() {}
};
var niu= new Pig();
alert(pig instanceof Pig); //=&gt; false
alert(niu instanceof Pig); //=&gt; true
</pre>
<p>当未改变 Pig 的 prototype 时，猪还是猪，因此代码 3 中 pig 是 Pig 的实例。当改变 prototype 后，猪已经不是猪，而是披着猪皮的牛了。因此代码 4 中 pig 不再是 Pig 的实例，niu 反而是 Pig 的实例。</p>
<p>进一步分析前，先回顾一下 <a href="http://lifesinger.org/blog/2009/08/new-funtion-secret/">new 的内部机制</a>。代码 2 中的 new Pig() 实际上等价为：</p>
<pre>
// var pig = new Pig() 的等价伪代码：
var pig = (function() {
    var o = {};
    o.__proto__ = Pig.prototype; // line 2
    Pig.call(o);
    Pig.prototype = {/* some code */}; // line 4
    return o; // line 5
})();
</pre>
<p>可以看出，在 line 2 时，o.__proto__ 指向了 Pig.prototype 指向的值。但在 line 4 时，Pig.prototype 指向了新值。也就是说，在 line 5 返回时，pig.__proto__ !== Pig.prototype. 正是这个变化，导致了代码 2 中的 pig 不是 Pig.</p>
<p>已经可以大胆推论出：instanceof 判断 pig 是不是 Pig 的依据是：看隐藏的 pig.__proto__ 属性是否等于 Pig.prototype !</p>
<p>为了进一步确认，我们可以在 Firefox 下模拟 instanceof 的内部实现代码：</p>
<pre>
/**
 * Gecko 引擎下，模拟 instanceof
 */
function _instanceof(obj, cls) {
    // instanceof 的左操作数必须是非null对象或函数对象
    if((typeof obj !== &quot;object&quot; || obj === null)
            &amp;&amp; typeof obj !== &quot;function&quot;) {
        return false;
    }

    // instanceof 的右操作数必须是函数对象
    if(typeof cls !== &quot;function&quot;) {
        throw new Error(&quot;invalid instanceof operand (&quot; + cls + &quot;)&quot;);
    }

    // 向上回溯判断
    var p = obj.__proto__, cp = cls.prototype;
    while(p) {
        if(p === cp) return true;
        p = p.__proto__;
    }
    return false;
}
</pre>
<p>测试页面：<a href="http://lifesinger.googlecode.com/svn/trunk/lab/2009/simulate-instanceof.html">simulate-intanceof.html</a></p>
<p>最后考考大家：</p>
<pre>
function Bird() {}
var bird = new Bird();
var o = {};
bird.__proto__ = o;
Bird.prototype = o;
alert(bird instanceof Bird); // true or false?
</pre>