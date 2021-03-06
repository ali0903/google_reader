---
layout: post
title:  "JavaScript需要类吗?"
date:   2012-10-19 14:52:06
author: 杨玉廷
categories: program
---

## JavaScript需要类吗?
### by 杨玉廷
### at 2012-10-19 14:52:06
### original <http://hp.dewen.org/?p=1865>

<div style="text-align:center;margin-bottom:5px;margin-top:5px"><img src="http://hp.dewen.org/wp-content/uploads/2012/10/best-javascript.jpg" alt="JavaScript需要类吗?" title="JavaScript需要类吗?" border="0">
<p><span style="font-weight:bold">本文作者：<a href="http://www.cnblogs.com/ziyunfei/">紫云飞</a> | 原文地址：<a href="http://www.cnblogs.com/ziyunfei/archive/2012/10/17/2727121.html">JavaScript需要类吗?</a></span></p></div>
<p>译者注:在我长达一年的工作生涯中,我遇到过有人把构造函数称做类,还有人把对象字面量称做类.这比把火狐扩展叫成插件都令我*疼.下面是Brendan Eich给今年的jsconf.eu录制的视频,其中提到了类.<span></span></p>
<p></p>
<p>无论你喜欢还是不喜欢,ECMAScript 6中将会包含类(class)这个新东西了<sup>[1]</sup>.在JavaScript中,对类的需求一直都有两极分化的趋势.有些人特别喜欢<a href="http://www.dewen.org/topic/11">JavaScript</a>中没有类,因为这和其他语言不同.另一方面,还有一些人厌恶JavaScript没有类,因为这和其他语言不同.那些从C++或Java转到JavaScript的人们最需要克服的一个心理障碍就是”JavaScript中没有类”,有些人和我说过,这就是他们为什么不喜欢Javascript或者不继续深入学习Javascript的原因之一.</p>
<p>JavaScript从发明的那天起就没有真正的类,这使得从一开始就造成了混乱.有不少JavaScript书籍或文章中都讲到了类,就好像JavaScript中真的存在类一样.但其实,他们所说的类只是一些自定义的构造函数,这些函数可以用来构造一些自定义的引用类型.在JavaScript中,引用类型已经是最接近于类的东西了.下面的代码你应该已经很熟悉了:</p>
<blockquote><p>function MyCustomType(value) {<br>
    this.property = value;<br>
}</p>
<p>MyCustomType.prototype.method = function() {<br>
    return this.property;<br>
};</p></blockquote>
<p>很多时候,这种代码被解释为是声明了一个<code>MyCustomType</code>类.但实际上,该代码做的所有事情仅仅是声明了一个<code>MyCustomType</code>函数<code>,配合new运算符可以用它创建一个引用类型</code><code>MyCustomType的实例</code>.该函数和其他的函数并没有什么本质的不同.因为其他自定义的函数也可以作为构造函数来使用.</p>
<p>这样的代码从表面上看起来根本不像是在定义一个类,被定义的构造函数和其原型对象上的方法似乎没有什么联系.如果是<a href="http://hp.dewen.org/?p=1865">JavaScript</a>新手,很可能会认为这是两段完全无关的代码.但实际上,这两段代码有非常紧密的联系,只是和其他语言中的类的写法相差甚远罢了.</p>
<p>更令人困惑的是,一旦谈到继承,大部分人想到的术语是子类和超类等等,这样的概念只有在存在真正的类的前提下才有意义.在JavaScript中,实现继承的代码同样是冗长的:</p>
<blockquote><p>function Animal(name) {<br>
    this.name = name;<br>
}</p>
<p>Animal.prototype.sayName = function() {<br>
    console.log(this.name);<br>
};</p>
<p>function Dog(name) {<br>
    Animal.call(this, name);<br>
}</p>
<p>Dog.prototype = new Animal(null);<br>
Dog.prototype.bark = function() {<br>
    console.log(“Woof!”);<br>
};
</p></blockquote>
<p>实现继承需要两个步骤,创建构造函数和重写原型对象,这样的代码非常混乱.</p>
<p>在第一版的《JavaScript高级程序设计》中,我使用过术语”类”.但从我收到的反馈中看,这样写是很令人困惑的.所以在第二版中,我把所有的”类(class)”都替换成了”类型(type)”.事实表明,使用”类型”这个术语可以减少很多的混乱.</p>
<p>可是,还有一个比较突出的问题:定义一个自定义类型的语法是冗长的,实现两个类型之间的继承会更加复杂.没有什么简单的方法可以调用一个属于超类型的方法.在目前看来,创建并管理一个自定义类型是件很痛苦的事,如果你不信,可以看看有下面有多少JavaScript框架使用了自己的定义自定义类型和继承的方法:</p>
<ul>
<li><strong>YUI</strong> – 用<code>Y.extend()来实现继承</code>.使用该方法会在子类型上添加一个”<code>superclass"属性.</code><sup>[2]</sup></li>
<li><strong>Prototype</strong> – 用<code>Class.create()和</code><code>Object.extend()来处理对象和"类"</code>.<sup>[3]</sup></li>
<li><strong>Dojo</strong> – 使用<code>dojo.declare()和</code><code>dojo.extend()</code>.<sup>[4]</sup></li>
<li><strong>MooTools</strong> – 有一个自定义类型<code>Class,可以用来定义和扩展"类"</code>.<sup>[5]</sup></li>
</ul>
<p>这么多的JavaScript框架都有自己的解决方案,这明显是非常混乱的.JavaScript开发者们需要一种更好的实现此功能的语法.</p>
<p>ECMAScript 6中的类其实并没有什么新东西,只是在你已经熟悉的模式上增加了一层语法糖.看看下面这个例子:</p>
<blockquote><p>class MyCustomType {<br>
    constructor(value) {<br>
        this.property = value;<br>
    }</p>
<p>    method() {<br>
        return this.property;<br>
    }<br>
}</p></blockquote>
<p>这个ECMAScript 6中的类定义其实就是本文上面那个MyCustomType例子的另一种写法.使用这种类创建的对象实例完全和使用构造函数创建的对象实例一样.唯一的区别就是前者拥有更紧凑的语法.下面看看继承的写法:</p>
<blockquote><p>class Animal {<br>
    constructor(name) {<br>
        this.name = name;<br>
    }</p>
<p>    sayName() {<br>
        console.log(this.name);<br>
    }<br>
}</p>
<p>class Dog extends Animal {<br>
    constructor(name) {<br>
        super(name);<br>
    }</p>
<p>    bark() {<br>
        console.log(“Woof!”);<br>
    }<br>
}</p></blockquote>
<p>在实际效果上这个例子也同样等同于前面那种继承的写法.只是复杂的实现步骤被一个简单的<code>extends关键字代替了</code>.在类定义中你还可以直接使用<code>super(),无需明确指出超类型的构造函数</code>.</p>
<p>ECMAScript 6中的类是基于你已经熟知的JavaScript模式.实现继承的原理还和以前一样(基于原型链,调用超类型的构造函数),方法添加在原型上,属性在构造函数中声明.真正的区别只有一个:你可以打更少的字.</p>
<p>所以,如果你现在仍然不赞同ECMAScript 6引入类这么个东西,你可以这么想,要引入的这个类不是什么新东西,也并没有从根本上改变JavaScript的工作机制.不过我个人更推荐使用关键字<code>type而不是</code><code>class</code>.</p>
<p>那么JavaScript真的需要类吗?答案是不需要,但JavaScript的确需要一个更简洁的方法来创建自定义类型.这恰巧就是ECMAScript 6中的”类”正要做的.如果这个”类”能帮助来自其他语言的开发者们更容易的转向JavaScript,那么它就是好东西.</p>
<h2>参考</h2>
<ol>
<li><a href="http://wiki.ecmascript.org/doku.php?id=strawman:maximally_minimal_classes">Maximally minimal classes</a> (ECMA)</li>
<li><a href="http://yuilibrary.com/yui/docs/api/classes/YUI.html#method_extend">YUI extend()</a> (YUILibrary)</li>
<li><a href="http://prototypejs.org/learn/class-inheritance">Prototype Classes and Inheritance</a> (Prototype)</li>
<li><a href="http://www.sitepen.com/blog/2010/07/01/creating-and-enhancing-dojo-classes/">Creating and Enhancing Dojo Classes</a> (SitePen)</li>
<li><a href="http://mootools.net/docs/core/Class/Class">MooTools Class</a> (MooTools)</li>
</ol>
<h2 style="font-size:16px;text-transform:none">延伸阅读</h2>
<p><a href="http://www.dewen.org/">德问编程社交问答</a><br>
<a href="http://www.dewen.org/questions/hot">最近热门问题</a></p>