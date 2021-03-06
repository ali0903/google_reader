---
layout: post
title:  "Javascript 严格模式详解"
date:   2013-01-14 13:42:05
author: 
categories: program
---

## Javascript 严格模式详解
### by 
### at 2013-01-14 13:42:05
### original <http://www.ruanyifeng.com/blog/2013/01/javascript_strict_mode.html>

<p><strong>一、概述</strong></p>

<p>除了正常运行模式，ECMAscript 5添加了第二种运行模式：<a href="https://developer.mozilla.org/en-US/docs/JavaScript/Reference/Functions_and_function_scope/Strict_mode">"严格模式"</a>（strict mode）。顾名思义，这种模式使得Javascript在更严格的条件下运行。</p><p><img src="http://image.beekka.com/blog/201301/bg2013011401.jpg"></p>

<p>设立"严格模式"的目的，主要有以下几个：</p>

<blockquote>

<p>　　- 消除Javascript语法的一些不合理、不严谨之处，减少一些怪异行为;</p>

<p>　　- 消除代码运行的一些不安全之处，保证代码运行的安全；</p>

<p>　　- 提高编译器效率，增加运行速度；</p>

<p>　　- 为未来新版本的Javascript做好铺垫。</p>

</blockquote>

<p>"严格模式"体现了Javascript更合理、更安全、更严谨的发展方向，包括IE 10在内的主流浏览器，都已经<a href="http://kangax.github.com/es5-compat-table">支持</a>它，许多大项目已经开始全面拥抱它。</p>

<p>另一方面，同样的代码，在"严格模式"中，可能会有不一样的运行结果；一些在"正常模式"下可以运行的语句，在"严格模式"下将不能运行。掌握这些内容，有助于更细致深入地理解Javascript，让你变成一个更好的程序员。</p>

<p>本文将对"严格模式"做详细介绍。</p>

<p><strong>二、进入标志</strong></p>

<p>进入"严格模式"的标志，是下面这行语句：</p>

<blockquote>

<p>　　"use strict";</p>

</blockquote>

<p>老版本的浏览器会把它当作一行普通字符串，加以忽略。</p>

<p><strong>三、如何调用</strong></p>

<p>"严格模式"有两种调用方法，适用于不同的场合。</p>

<p><strong>3.1 针对整个脚本文件</strong></p>

<p>将"use strict"放在脚本文件的第一行，则整个脚本都将以"严格模式"运行。如果这行语句不在第一行，则无效，整个脚本以"正常模式"运行。如果不同模式的代码文件合并成一个文件，这一点需要特别注意。</p>

<p>(严格地说，只要前面不是产生实际运行结果的语句，"use strict"可以不在第一行，比如直接跟在一个空的分号后面。)</p>

<blockquote>

<p>　　&lt;script&gt;<br>
　　　　"use strict";<br>
　　　　console.log("这是严格模式。");<br>
　　&lt;/script&gt;</p>

<p>　　&lt;script&gt;<br>
　　　　console.log("这是正常模式。");<br>
　　&lt;/script&gt;</p>

</blockquote>

<p>上面的代码表示，一个网页中依次有两段Javascript代码。前一个script标签是严格模式，后一个不是。</p>

<p><strong>3.2 针对单个函数</strong></p>

<p>将"use strict"放在函数体的第一行，则整个函数以"严格模式"运行。</p>

<blockquote>

<p>　　function strict(){<br>
　　　　"use strict";<br>
　　　　return "这是严格模式。";<br>
　　}</p>

<p>　　function notStrict() {<br>
　　　　return "这是正常模式。";<br>
　　}</p>

</blockquote>

<p><strong>3.3 脚本文件的变通写法</strong></p>

<p>因为第一种调用方法不利于文件合并，所以更好的做法是，借用第二种方法，将整个脚本文件放在一个立即执行的匿名函数之中。</p>

<blockquote>

<p>　　(function (){<br>
 <br>
　　　　"use strict";</p>

<p>　　　　// some code here<br>
 <br>
　　 })();</p>

</blockquote>

<p><strong>四、语法和行为改变</strong></p>

<p>严格模式对Javascript的语法和行为，都做了一些改变。</p>

<p><strong>4.1 全局变量显式声明</strong></p>

<p>在正常模式中，如果一个变量没有声明就赋值，默认是全局变量。严格模式禁止这种用法，全局变量必须显式声明。</p>

<blockquote>

<p>"use strict";</p>

<p>　　v = 1; // 报错，v未声明</p>

<p>　　for(i = 0; i &lt; 2; i++) { // 报错，i未声明<br>
　　}</p>

</blockquote>

<p>因此，严格模式下，变量都必须先用var命令声明，然后再使用。</p>

<p><strong>4.2 静态绑定</strong></p>

<p>Javascript语言的一个特点，就是允许"动态绑定"，即某些属性和方法到底属于哪一个对象，不是在编译时确定的，而是在运行时（runtime）确定的。</p>

<p>严格模式对动态绑定做了一些限制。某些情况下，只允许静态绑定。也就是说，属性和方法到底归属哪个对象，在编译阶段就确定。这样做有利于编译效率的提高，也使得代码更容易阅读，更少出现意外。</p>

<p>具体来说，涉及以下几个方面。</p>

<p><strong>（1）禁止使用with语句</strong></p>

<p>因为with语句无法在编译时就确定，属性到底归属哪个对象。</p>

<blockquote>

<p>　　"use strict";</p>

<p>　　var v  = 1;</p>

<p>　　with (o){ // 语法错误 <br>
　　　　v = 2;<br>
　　}</p>

</blockquote>

<p><strong>（2）创设eval作用域</strong></p>

<p>正常模式下，Javascript语言有两种变量作用域（scope）：全局作用域和函数作用域。严格模式创设了第三种作用域：eval作用域。</p>

<p>正常模式下，eval语句的作用域，取决于它处于全局作用域，还是处于函数作用域。严格模式下，eval语句本身就是一个作用域，不再能够生成全局变量了，它所生成的变量只能用于eval内部。</p>

<blockquote>

<p>　　"use strict";</p>

<p>　　var x = 2;</p>

<p>　　console.info(eval("var x = 5; x")); // 5</p>

<p>　　console.info(x); // 2</p>

</blockquote>

<p><strong>4.3 增强的安全措施</strong></p>

<p><strong>（1）禁止this关键字指向全局对象</strong></p>

<blockquote>

<p>　　function f(){<br>
　　　　return !this;<br>
　　} <br>
　　// 返回false，因为"this"指向全局对象，"!this"就是false</p>

<p>　　function f(){   <br>
　　　　"use strict";<br>
　　　　return !this;<br>
　　} <br>
　　// 返回true，因为严格模式下，this的值为undefined，所以"!this"为true。</p>

</blockquote>

<p>因此，使用构造函数时，如果忘了加new，this不再指向全局对象，而是报错。</p>

<blockquote>

<p>　　function f(){   </p>

<p>　　　　"use strict";</p>

<p>　　　　this.a = 1;</p>

<p>　　};</p>

<p>　　f();// 报错，this未定义</p>

</blockquote>

<p><strong>（2）禁止在函数内部遍历调用栈</strong></p>

<blockquote>

<p>　　function f1(){</p>

<p>　　　　"use strict";</p>

<p>　　　　f1.caller;    // 报错</p>

<p>　　　　f1.arguments; // 报错</p>

<p>　　}</p>

<p>　　f1();</p>

</blockquote>

<p><strong>4.4 禁止删除变量</strong></p>

<p>严格模式下无法删除变量。只有configurable设置为true的对象属性，才能被删除。</p>

<blockquote>

<p>　　"use strict";</p>

<p>　　var x;</p>

<p>　　delete x; // 语法错误</p>

<p>　　var o = Object.create(null, 'x', {<br>
　　　　　　value: 1,<br>
　　　　　　configurable: true<br>
 　　});</p>

<p>　　delete o.x; // 删除成功</p>

</blockquote>

<p><strong>4.5 显式报错</strong></p>

<p>正常模式下，对一个对象的只读属性进行赋值，不会报错，只会默默地失败。严格模式下，将报错。</p>

<blockquote>

<p>　　"use strict";<br>
 <br>
　　var o = {};</p>

<p>　　Object.defineProperty(o, "v", { value: 1, writable: false });</p>

<p>　　o.v = 2; // 报错</p>

</blockquote>

<p>严格模式下，对一个使用getter方法读取的属性进行赋值，会报错。</p>

<blockquote>

<p>　　"use strict";<br>
 <br>
　　var o = {<br>
    <br>
　　　　get v() { return 1; } </p>

<p>　　};</p>

<p>　　o.v = 2; // 报错</p>

</blockquote>

<p>严格模式下，对禁止扩展的对象添加新属性，会报错。</p>

<blockquote>

<p>　　"use strict";</p>

<p>　　var o = {};</p>

<p>　　Object.preventExtensions(o);</p>

<p>　　o.v = 1; // 报错</p>

</blockquote>

<p>严格模式下，删除一个不可删除的属性，会报错。</p>

<blockquote>

<p>　　"use strict";</p>

<p>　　delete Object.prototype; // 报错</p>

</blockquote>

<p><strong>4.6 重名错误</strong></p>

<p>严格模式新增了一些语法错误。</p>

<p><strong>（1）对象不能有重名的属性</strong></p>

<p>正常模式下，如果对象有多个重名属性，最后赋值的那个属性会覆盖前面的值。严格模式下，这属于语法错误。</p>

<blockquote>

<p>　　"use strict";</p>

<p>　　var o = {<br>
　　　　p: 1,<br>
　　　　p: 2<br>
　　}; // 语法错误</p></blockquote>

<p><strong>（2）函数不能有重名的参数</strong></p>

<p>正常模式下，如果函数有多个重名的参数，可以用arguments[i]读取。严格模式下，这属于语法错误。</p>

<blockquote>

<p>　　"use strict";</p>

<p>　　function f(a, a, b) { // 语法错误  </p>

<p>　　　　return ; </p>

<p>　　}</p>

</blockquote>

<p><strong>4.7 禁止八进制表示法</strong></p>

<p>正常模式下，整数的第一位如果是0，表示这是八进制数，比如0100等于十进制的64。严格模式禁止这种表示法，整数第一位为0，将报错。</p>

<blockquote>

<p>　　"use strict";</p>

<p>　　var n = 0100; // 语法错误</p>

</blockquote>

<p><strong>4.8 arguments对象的限制</strong></p>

<p>arguments是函数的参数对象，严格模式对它的使用做了限制。</p>

<p><strong>（1）不允许对arguments赋值</strong></p>

<blockquote>

<p>　　"use strict";</p>

<p>　　arguments++; // 语法错误</p>

<p>　　var obj = { set p(arguments) { } };  // 语法错误</p>

<p>　　try { } catch (arguments) { }  // 语法错误</p>

<p>　　function arguments() { }  // 语法错误</p>

<p>　　var f = new Function("arguments", "'use strict'; return 17;");  // 语法错误</p>

</blockquote>

<p><strong>（2）arguments不再追踪参数的变化</strong></p>

<blockquote>

<p>　　function f(a) {</p>

<p>　　　　a = 2;</p>

<p>　　　　return [a, arguments[0]];</p>

<p>　　}</p>

<p>　　f(1); // 正常模式为[2,2]</p>

<p>　　function f(a) {</p>

<p>　　　　"use strict";    </p>

<p>　　　　a = 2;</p>

<p>　　　　return [a, arguments[0]];</p>

<p>　　}</p>

<p>　　f(1); // 严格模式为[2,1]</p>

</blockquote>

<p><strong>（3）禁止使用arguments.callee</strong></p>

<p>这意味着，你无法在匿名函数内部调用自身了。</p>

<blockquote>

<p>　　"use strict";</p>

<p>　　var f = function() { return arguments.callee; };</p>

<p>　　f(); // 报错</p>

</blockquote>

<p><strong>4.9 函数必须声明在顶层</strong></p>

<p>将来Javascript的新版本会引入"块级作用域"。为了与新版本接轨，严格模式只允许在全局作用域或函数作用域的顶层声明函数。也就是说，不允许在非函数的代码块内声明函数。</p>

<blockquote>

<p>　　"use strict";</p>

<p>　　if (true) {</p>

<p>　　　　function f() { } // 语法错误</p>

<p>　　}</p>

<p>　　for (var i = 0; i &lt; 5; i++) {</p>

<p>　　　　function f2() { } // 语法错误</p>

<p>　　}</p>

</blockquote>

<p><strong>4.10 保留字</strong></p>

<p>为了向将来Javascript的新版本过渡，严格模式新增了一些保留字：implements, interface, let, package, private, protected, public, static, yield。</p>

<p>使用这些词作为变量名将会报错。</p>

<blockquote>

<p>　　function package(protected) { // 语法错误</p>

<p>　　　　"use strict";</p>

<p>　　　　var implements; // 语法错误</p>

<p>　　}</p>

</blockquote>

<p>此外，ECMAscript第五版本身还规定了另一些保留字（class, enum, export, extends, import, super），以及各大浏览器自行增加的const保留字，也是不能作为变量名的。</p>

<p><strong>五、参考链接</strong></p>

<p>  - MDN, <a href="https://developer.mozilla.org/en-US/docs/JavaScript/Reference/Functions_and_function_scope/Strict_mode">Strict mode</a><br>
  - Dr. Axel Rauschmayer，<a href="http://www.2ality.com/2011/01/javascripts-strict-mode-summary.html">JavaScript's strict mode: a summary</a><br>
  - Douglas Crockford, <a href="http://java-script.limewebs.com/strictMode/test_hosted.html">Strict Mode Is Coming To Town</a></p>

<p>（完）</p><div style="color:#556677;line-height:160%;padding:0.3em 0.5em;border:1px solid #d3d3d3;margin:1em;background-color:#aad2f0;border-radius:10px"><h3>文档信息</h3>
<ul>
<li>版权声明：自由转载-非商用-非衍生-保持署名 | <a href="http://creativecommons.org/licenses/by-nc-nd/3.0/deed.zh">Creative Commons BY-NC-ND 3.0</a></li>
<li>原文网址：<a href="http://www.ruanyifeng.com/blog/2013/01/javascript_strict_mode.html">http://www.ruanyifeng.com/blog/2013/01/javascript_strict_mode.html</a></li>
<li>最后修改时间：2013年2月 4日 14:29</li>
<li>付费支持：<a href="https://me.alipay.com/ruanyf"><img src="http://www.ruanyifeng.com/blog/images/rmb_32.png" title="人民币" alt="人民币 - 支付宝" style="border:none;vertical-align:middle"></a> | <a href="https://www.paypal.com/cgi-bin/webscr?cmd=_xclick&amp;business=yifeng.ruan@gmail.com&amp;currency_code=USD&amp;amount=0.99&amp;return=http://www.ruanyifeng.com/thank.html&amp;item_name=Ruan%20YiFeng&#39;s%20Blog&amp;undefined_quantity=1&amp;no_note=0"><img src="http://www.ruanyifeng.com/blog/images/dollar_32.png" alt="美元 - paypal" title="美元" style="border:none;vertical-align:middle"></a> </li>
</ul></div><div style="color:#556677;line-height:160%;padding:0.3em 0.5em;margin:1em;border-radius:10px"><p><strong>[广告]</strong>　<a href="http://ushan.cn?utm_source=ruanyifeng.com" style="border:none">优衫（Ushan）是国内顶尖的定制西服店，常年为众多政商名流、影视明星、跨国高管定制衬衫与西服。以工艺精良、用料考究、版型出色、性价比高等特点广受各界好评。</a>
</p>
</div>