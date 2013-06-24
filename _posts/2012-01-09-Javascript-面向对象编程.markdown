---
layout: post
title:  "Javascript 面向对象编程"
date:   2012-01-09 08:16:27
author: 陈皓
categories: program
---

## Javascript 面向对象编程
### by 陈皓
### at 2012-01-09 08:16:27
### original <http://coolshell.cn/articles/6441.html>

<div style="clear:both;margin-top:5px;margin-bottom:5px"></div><p>Javascript是一个类C的语言，他的面向对象的东西相对于C++/Java比较奇怪，但是其的确相当的强大，在 <a href="http://www.cnblogs.com/weidagang2046/">Todd 同学</a>的“<a title="对象的消息模型" href="http://coolshell.cn/articles/5202.html" rel="bookmark">对象的消息模型</a>”一文中我们已经可以看到一些端倪了。这两天有个前同事总在问我Javascript面向对象的东西，所以，索性写篇文章让他看去吧，这里这篇文章主要想从一个整体的角度来说明一下Javascript的面向对象的编程。（<strong>成文比较仓促，应该有不准确或是有误的地方，请大家批评指正</strong>）</p>
<p>另，这篇文章主要基于 <a href="http://www.ecma-international.org/publications/standards/Ecma-262.htm">ECMAScript 5</a>， 旨在介绍新技术。关于兼容性的东西，请看最后一节。</p>
<h4>初探</h4>
<p>我们知道Javascript中的变量定义基本如下：</p>
<pre>var name = 'Chen Hao';;
var email = 'haoel(@)hotmail.com';
var website = 'http://coolshell.cn';</pre>
<p>如果要用对象来写的话，就是下面这个样子：</p>
<pre>var chenhao = {
    name :'Chen Hao',
    email : 'haoel(@)hotmail.com',
    website : 'http://coolshell.cn'
};</pre>
<p>于是，我就可以这样访问：</p>
<pre>
//以成员的方式
chenhao.name;
chenhao.email;
chenhao.website;

//以hash map的方式
chenhao[&quot;name&quot;];
chenhao[&quot;email&quot;];
chenhao[&quot;website&quot;];
</pre>
<p>关于函数，我们知道Javascript的函数是这样的：</p>
<p><span></span></p>
<pre>var doSomething = function(){
   alert('Hello World.');
};</pre>
<p>于是，我们可以这么干：</p>
<pre>
var sayHello = function(){
   var hello = &quot;Hello, I&#39;m &quot;+ this.name
                + &quot;, my email is: &quot; + this.email
                + &quot;, my website is: &quot; + this.website;
   alert(hello);
};

//直接赋值，这里很像C/C++的函数指针
chenhao.Hello = sayHello;

chenhao.Hello();
</pre>
<p>相信这些东西都比较简单，大家都明白了。 可以看到javascript对象函数是直接声明，直接赋值，直接就用了。runtime的动态语言。</p>
<p>还有一种比较规范的写法是：</p>
<pre>
//我们可以看到， 其用function来做class。
var Person = function(name, email, website){
    this.name = name;
    this.email = email;
    this.website = website;

    this.sayHello = function(){
        var hello = &quot;Hello, I&#39;m &quot;+ this.name  + &quot;, \n&quot; +
                    &quot;my email is: &quot; + this.email + &quot;, \n&quot; +
                    &quot;my website is: &quot; + this.website;
        alert(hello);
    };
};

var chenhao = new Person(&quot;Chen Hao&quot;, &quot;haoel@hotmail.com&quot;,
                                     &quot;http://coolshell.cn&quot;);
chenhao.sayHello(); </pre>
<p>顺便说一下，要删除对象的属性，很简单：</p>
<pre>delete chenhao['email']</pre>
<p>上面的这些例子，我们可以看到这样几点：</p>
<ol>
<li>Javascript的数据和成员封装很简单。没有类完全是对象操作。纯动态！</li>
<li>Javascript function中的this指针很关键，如果没有的话，那就是局部变量或局部函数。</li>
<li>Javascript对象成员函数可以在使用时临时声明，并把一个全局函数直接赋过去就好了。</li>
<li>Javascript的成员函数可以在实例上进行修改，也就是说不同实例相同函数名的行为不一定一样。</li>
</ol>
<h4>属性配置 – Object.defineProperty</h4>
<p>先看下面的代码：</p>
<pre>
//创建对象
var chenhao = Object.create(null);

//设置一个属性
 Object.defineProperty( chenhao,
                'name', { value:  'Chen Hao',
                          writable:     true,
                          configurable: true,
                          enumerable:   true });

//设置多个属性
Object.defineProperties( chenhao,
    {
        'email'  : { value:  'haoel@hotmail.com',
                     writable:     true,
                     configurable: true,
                     enumerable:   true },
        'website': { value: 'http://coolshell.cn',
                     writable:     true,
                     configurable: true,
                     enumerable:   true }
    }
);
</pre>
<p>下面就说说这些属性配置是什么意思。</p>
<ul>
<li>writable：这个属性的值是否可以改。</li>
<li>configurable：这个属性的配置是否可以改。</li>
<li>enumerable：这个属性是否能在for…in循环中遍历出来或在Object.keys中列举出来。</li>
<li>value：属性值。</li>
<li>get()/set(_value)：get和set访问器。</li>
</ul>
<h4>Get/Set 访问器</h4>
<p>关于get/set访问器，它的意思就是用get/set来取代value（其不能和value一起使用），示例如下：</p>
<pre>var  age = 0;
Object.defineProperty( chenhao,
            'age', {
                      get: function() {return age+1;},
                      set: function(value) {age = value;}
                      enumerable : true,
                      configurable : true
                    }
);
chenhao.age = 100; //调用set
alert(chenhao.age); //调用get 输出101（get中+1了）;
</pre>
<p>我们再看一个更为实用的例子——利用已有的属性(age)通过get和set构造新的属性(birth_year)：</p>
<pre>
Object.defineProperty( chenhao,
            'birth_year',
            {
                get: function() {
                    var d = new Date();
                    var y = d.getFullYear();
                    return ( y - this.age );
                },
                set: function(year) {
                    var d = new Date();
                    var y = d.getFullYear();
                    this.age = y - year;
                }
            }
);

alert(chenhao.birth_year);
chenhao.birth_year = 2000;
alert(chenhao.age);
</pre>
<p>这样做好像有点麻烦，你说，我为什么不写成下面这个样子：</p>
<pre>
var chenhao = {
    name: &quot;Chen Hao&quot;,
    email: &quot;haoel@hotmail.com&quot;,
    website: &quot;http://coolshell.cn&quot;,
    age: 100,
    get birth_year() {
        var d = new Date();
        var y = d.getFullYear();
        return ( y - this.age );
    },
    set birth_year(year) {
        var d = new Date();
        var y = d.getFullYear();
        this.age = y - year;
    }

};
alert(chenhao.birth_year);
chenhao.birth_year = 2000;
alert(chenhao.age);
</pre>
<p>是的，你的确可以这样的，不过通过defineProperty()你可以干这些事：<br>
1）设置如 writable，configurable，enumerable 等这类的属性配置。<br>
2）动态地为一个对象加属性。比如：一些HTML的DOM对像。</p>
<h4>查看对象属性配置</h4>
<p>如果查看并管理对象的这些配置，下面有个程序可以输出对象的属性和配置等东西：</p>
<pre>//列出对象的属性.
function listProperties(obj)
{
    var newLine = &quot;&lt;br /&gt;&quot;;
    var names = Object.getOwnPropertyNames(obj);
    for (var i = 0; i &lt; names.length; i++) {
        var prop = names[i];
        document.write(prop + newLine);

        // 列出对象的属性配置（descriptor）动用getOwnPropertyDescriptor函数。
        var descriptor = Object.getOwnPropertyDescriptor(obj, prop);
        for (var attr in descriptor) {
            document.write(&quot;...&quot; + attr + &#39;: &#39; + descriptor[attr]);
            document.write(newLine);
        }
        document.write(newLine);
    }
}

listProperties(chenhao);</pre>
<h4>call，apply， bind 和 this</h4>
<p>关于Javascript的this指针，和C++/Java很类似。 我们来看个示例：（这个示例很简单了，我就不多说了）</p>
<pre>function print(text){
    document.write(this.value + &#39; - &#39; + text+ &#39;&lt;br&gt;&#39;);
}

var a = {value: 10, print : print};
var b = {value: 20, print : print};

print(&#39;hello&#39;);// this =&gt; global, output &quot;undefined - hello&quot;

a.print(&#39;a&#39;);// this =&gt; a, output &quot;10 - a&quot;
b.print(&#39;b&#39;); // this =&gt; b, output &quot;20 - b&quot;

a[&#39;print&#39;](&#39;a&#39;); // this =&gt; a, output &quot;10 - a&quot;
</pre>
<p>我们再来看看call 和 apply，这两个函数的差别就是参数的样子不一样，另一个就是性能不一样，apply的性能要差很多。（关于性能，可到 <a href="http://jsperf.com/">JSPerf</a> 上去跑跑看看）</p>
<pre>print.call(a, &#39;a&#39;); // this =&gt; a, output &quot;10 - a&quot;
print.call(b, &#39;b&#39;); // this =&gt; b, output &quot;20 - b&quot;

print.apply(a, [&#39;a&#39;]); // this =&gt; a, output &quot;10 - a&quot;
print.apply(b, [&#39;b&#39;]); // this =&gt; b, output &quot;20 - b&quot;</pre>
<p>但是在bind后，this指针，可能会有不一样，但是因为Javascript是动态的。如下面的示例</p>
<pre>var p = print.bind(a);
p(&#39;a&#39;);             // this =&gt; a, output &quot;10 - a&quot;
p.call(b, &#39;b&#39;);     // this =&gt; a, output &quot;10 - b&quot;
p.apply(b, [&#39;b&#39;]);  // this =&gt; a, output &quot;10 - b&quot;</pre>
<h4>继承 和 重载</h4>
<p>通过上面的那些示例，我们可以通过Object.create()来实际继承，请看下面的代码，Student继承于Object。</p>
<pre>
var Person = Object.create(null);

Object.defineProperties
(
    Person,
    {
        &#39;name&#39;  : {  value: &#39;Chen Hao&#39;},
        &#39;email&#39;  : { value : &#39;haoel@hotmail.com&#39;},
        &#39;website&#39;: { value: &#39;http://coolshell.cn&#39;}
    }
);

Person.sayHello = function () {
    var hello = &quot;&lt;p&gt;Hello, I am &quot;+ this.name  + &quot;, &lt;br&gt;&quot; +
                &quot;my email is: &quot; + this.email + &quot;, &lt;br&gt;&quot; +
                &quot;my website is: &quot; + this.website;
    document.write(hello + &quot;&lt;br&gt;&quot;);
}

var Student = Object.create(Person);
Student.no = &quot;1234567&quot;; //学号
Student.dept = &quot;Computer Science&quot;; //系

//使用Person的属性
document.write(Student.name + &#39; &#39; + Student.email + &#39; &#39; + Student.website +&#39;&lt;br&gt;&#39;);

//使用Person的方法
Student.sayHello();

//重载SayHello方法
Student.sayHello = function (person) {
    var hello = &quot;&lt;p&gt;Hello, I am &quot;+ this.name  + &quot;, &lt;br&gt;&quot; +
                &quot;my email is: &quot; + this.email + &quot;, &lt;br&gt;&quot; +
                &quot;my website is: &quot; + this.website + &quot;, &lt;br&gt;&quot; +
                &quot;my student no is: &quot; + this. no + &quot;, &lt;br&gt;&quot; +
                &quot;my departent is: &quot; + this. dept;
    document.write(hello + &#39;&lt;br&gt;&#39;);
}
//再次调用
Student.sayHello();

//查看Student的属性（只有 no 、 dept 和 重载了的sayHello）
document.write(&#39;&lt;p&gt;&#39; + Object.keys(Student) + &#39;&lt;br&gt;&#39;);
</pre>
<p>通用上面这个示例，我们可以看到，Person里的属性并没有被真正复制到了Student中来，但是我们可以去存取。这是因为Javascript用委托实现了这一机制。其实，这就是Prototype，Person是Student的Prototype。</p>
<p>当我们的代码需要一个属性的时候，Javascript的引擎会先看当前的这个对象中是否有这个属性，如果没有的话，就会查找他的Prototype对象是否有这个属性，一直继续下去，直到找到或是直到没有Prototype对象。</p>
<p>为了证明这个事，我们可以使用Object.getPrototypeOf()来检验一下：</p>
<pre>Student.name = &#39;aaa&#39;;

//输出 aaa
document.write(&#39;&lt;p&gt;&#39; + Student.name + &#39;&lt;/p&gt;&#39;);

//输出 Chen Hao
document.write(&#39;&lt;p&gt;&#39; +Object.getPrototypeOf(Student).name + &#39;&lt;/p&gt;&#39;);</pre>
<p>于是，你还可以在子对象的函数里调用父对象的函数，就好像C++里的 Base::func() 一样。于是，我们重载hello的方法就可以使用父类的代码了，如下所示：</p>
<pre>//新版的重载SayHello方法
Student.sayHello = function (person) {
    Object.getPrototypeOf(this).sayHello.call(this);
    var hello = &quot;my student no is: &quot; + this. no + &quot;, &lt;br&gt;&quot; +
                &quot;my departent is: &quot; + this. dept;
    document.write(hello + &#39;&lt;br&gt;&#39;);
}</pre>
<p>这个很强大吧。</p>
<h4>组合</h4>
<p>上面的那个东西还不能满足我们的要求，我们可能希望这些对象能真正的组合起来。为什么要组合？因为我们都知道是这是OO设计的最重要的东西。不过，这对于Javascript来并没有支持得特别好，不好我们依然可以搞定个事。</p>
<p>首先，我们需要定义一个Composition的函数：（target是作用于是对象，source是源对象），下面这个代码还是很简单的，就是把source里的属性一个一个拿出来然后定义到target中。</p>
<pre>
function Composition(target, source)
{
    var desc  = Object.getOwnPropertyDescriptor;
    var prop  = Object.getOwnPropertyNames;
    var def_prop = Object.defineProperty;

    prop(source).forEach(
        function(key) {
            def_prop(target, key, desc(source, key))
        }
    )
    return target;
}
</pre>
<p>有了这个函数以后，我们就可以这来玩了：</p>
<pre>
//艺术家
var Artist = Object.create(null);
Artist.sing = function() {
    return this.name + &#39; starts singing...&#39;;
}
Artist.paint = function() {
    return this.name + &#39; starts painting...&#39;;
}

//运动员
var Sporter = Object.create(null);
Sporter.run = function() {
    return this.name + &#39; starts running...&#39;;
}
Sporter.swim = function() {
    return this.name + &#39; starts swimming...&#39;;
}

Composition(Person, Artist);
document.write(Person.sing() + &#39;&lt;br&gt;&#39;);
document.write(Person.paint() + &#39;&lt;br&gt;&#39;);

Composition(Person, Sporter);
document.write(Person.run() + &#39;&lt;br&gt;&#39;);
document.write(Person.swim() + &#39;&lt;br&gt;&#39;);

//看看 Person中有什么？（输出：sayHello,sing,paint,swim,run）
document.write(&#39;&lt;p&gt;&#39; + Object.keys(Person) + &#39;&lt;br&gt;&#39;);
</pre>
<h4>Prototype 和 继承</h4>
<p>我们先来说说Prototype。我们先看下面的例程，这个例程不需要解释吧，很像C语言里的函数指针，在C语言里这样的东西见得多了。</p>
<pre>var plus = function(x,y){
    document.write( x + &#39; + &#39; + y + &#39; = &#39; + (x+y) + &#39;&lt;br&gt;&#39;);
    return x + y;
};

var minus = function(x,y){
    document.write(x + &#39; - &#39; + y + &#39; = &#39; + (x-y) + &#39;&lt;br&gt;&#39;);
    return x - y;
};

var operations = {
    &#39;+&#39;: plus,
    &#39;-&#39;: minus
};

var calculate = function(x, y, operation){
    return operations[operation](x, y);
};

calculate(12, 4, &#39;+&#39;);
calculate(24, 3, &#39;-&#39;);
</pre>
<p>那么，我们能不能把这些东西封装起来呢，我们需要使用prototype。看下面的示例：</p>
<pre>var Cal = function(x, y){
    this.x = x;
    this.y = y;
}

Cal.prototype.operations = {
    '+': function(x, y) { return x+y;},
    '-': function(x, y) { return x-y;}
};

Cal.prototype.calculate = function(operation){
    return this.operations[operation](this.x, this.y);
};

var c = new Cal(4, 5);

c.calculate('+');
c.calculate('-');</pre>
<p>这就是prototype的用法，prototype 是javascript这个语言中最重要的内容。网上有太多的文章介始这个东西了。说白了，prototype就是对一对象进行扩展，其特点在于通过“复制”一个已经存在的实例来返回新的实例,而不是新建实例。被复制的实例就是我们所称的“原型”，这个原型是可定制的（当然，这里没有真正的复制，实际只是委托）。上面的这个例子中，我们扩展了实例Cal，让其有了一个operations的属性和一个calculate的方法。</p>
<p>这样，我们可以通过这一特性来实现继承。还记得我们最最前面的那个Person吧， 下面的示例是创建一个Student来继承Person。</p>
<pre>
function Person(name, email, website){
    this.name = name;
    this.email = email;
    this.website = website;
};

Person.prototype.sayHello = function(){
    var hello = &quot;Hello, I am &quot;+ this.name  + &quot;, &lt;br&gt;&quot; +
                &quot;my email is: &quot; + this.email + &quot;, &lt;br&gt;&quot; +
                &quot;my website is: &quot; + this.website;
    return hello;
};

function Student(name, email, website, no, dept){
    var proto = Object.getPrototypeOf;
    proto(Student.prototype).constructor.call(this, name, email, website);
    this.no = no;
    this.dept = dept;
}

// 继承prototype
Student.prototype = Object.create(Person.prototype);

//重置构造函数
Student.prototype.constructor = Student;

//重载sayHello()
Student.prototype.sayHello = function(){
    var proto = Object.getPrototypeOf;
    var hello = proto(Student.prototype).sayHello.call(this) + &#39;&lt;br&gt;&#39;;
    hello += &quot;my student no is: &quot; + this. no + &quot;, &lt;br&gt;&quot; +
             &quot;my departent is: &quot; + this. dept;
    return hello;
};

var me = new Student(
    &quot;Chen Hao&quot;,
    &quot;haoel@hotmail.com&quot;,
    &quot;http://coolshell.cn&quot;,
    &quot;12345678&quot;,
    &quot;Computer Science&quot;
);
document.write(me.sayHello());</pre>
<h4>兼容性</h4>
<p>上面的这些代码并不一定能在所有的浏览器下都能运行，因为上面这些代码遵循 ECMAScript 5 的规范，关于ECMAScript 5 的浏览器兼容列表，你可以看这里“<a href="http://kangax.github.com/es5-compat-table/">ES5浏览器兼容表</a>”。</p>
<p>本文中的所有代码都在Chrome最新版中测试过了。</p>
<p>下面是一些函数，可以用在不兼容ES5的浏览器中：</p>
<h5>Object.create()函数</h5>
<pre>function clone(proto) {
    function Dummy() { }

    Dummy.prototype             = proto;
    Dummy.prototype.constructor = Dummy;

    return new Dummy(); //等价于Object.create(Person);
}

var me = clone(Person);
</pre>
<h5>defineProperty()函数</h5>
<pre>function defineProperty(target, key, descriptor) {
    if (descriptor.value){
        target[key] = descriptor.value;
    }else {
        descriptor.get &amp;&amp; target.__defineGetter__(key, descriptor.get);
        descriptor.set &amp;&amp; target.__defineSetter__(key, descriptor.set);
    }

    return target
}</pre>
<h5>keys()函数</h5>
<pre>function keys(object) { var result, key
    result = [];
    for (key in object){
        if (object.hasOwnProperty(key))  result.push(key)
    }

    return result;
}</pre>
<h5>Object.getPrototypeOf() 函数</h5>
<pre>function proto(object) {
    return !object?                null
         : '__proto__' in object?  object.__proto__
         : /* not exposed? */      object.constructor.prototype
}</pre>
<h5>bind 函数</h5>
<pre>var slice = [].slice

function bind(fn, bound_this) { var bound_args
    bound_args = slice.call(arguments, 2)
    return function() { var args
        args = bound_args.concat(slice.call(arguments))
        return fn.apply(bound_this, args) }
}
</pre>
<h4>参考</h4>
<ul>
<li>W3CSchool</li>
<li>MDN (Mozilla Developer Network)</li>
<li>MSDN (Microsoft Software Development Network)</li>
<li><a href="http://killdream.github.com/blog/2011/10/understanding-javascript-oop/">Understanding Javascript OOP</a>.</li>
</ul>
<p><span style="color:#cc0000"><strong>（转载时请注明作者和出处，请勿用于任何商业用途）</strong></span></p>
<div style="float:left">
<div>
<a></a>
<a></a>
<a></a>
<a></a>
<a></a>
<a></a>
<a></a>
<a></a>
<a></a>
<a></a>
<a></a>
<a></a>
<a></a>
<a></a>
<a></a>
<a href="http://www.jiathis.com/share?uid=1541368"></a>
<a></a>
</div>



</div><div style="clear:both;margin-top:5px;margin-bottom:5px"></div><table cellspacing="0" cellpadding="3" border="0" style="clear:both">
    
    <tr>
        <td colspan="5"><b><font size="-1" style="display:block!important;padding:20px 0 5px!important">您可能也喜欢：</font></b></td>
    </tr>
    
        <tr>
                <td width="102" valign="top" style="padding:5px!important;margin:0!important">
                    <a title="再谈javascript面向对象编程 " style="text-decoration:none!important" href="http://app.wumii.com/ext/redirect?url=http%3A%2F%2Fcoolshell.cn%2Farticles%2F6668.html&amp;from=http%3A%2F%2Fcoolshell.cn%2Farticles%2F6441.html">
                        <img style="margin:0!important;padding:2px!important;border:1px solid #dddddd!important;width:96px!important;height:96px!important" src="http://static.wumii.com/site_images/2012/02/27/16161591.png" width="96px" height="96px"><br>
                        <font size="-1" color="#333333" style="display:block!important;line-height:15px!important;width:102px!important;font:12px/15px arial!important;height:60px!important;margin:3px 0 0 0!important;padding:0!important;overflow:hidden!important">再谈javascript面向对象编程 </font>
                    </a>
                </td>
                <td width="102" valign="top" style="padding:5px!important;margin:0!important;border-left:1px solid #dddddd!important">
                    <a title="理解Javascript的闭包" style="text-decoration:none!important" href="http://app.wumii.com/ext/redirect?url=http%3A%2F%2Fcoolshell.cn%2Farticles%2F6731.html&amp;from=http%3A%2F%2Fcoolshell.cn%2Farticles%2F6441.html">
                        <img style="margin:0!important;padding:2px!important;border:1px solid #dddddd!important;width:96px!important;height:96px!important" src="http://static.wumii.com/site_images/2012/03/07/16657660.png" width="96px" height="96px"><br>
                        <font size="-1" color="#333333" style="display:block!important;line-height:15px!important;width:102px!important;font:12px/15px arial!important;height:60px!important;margin:3px 0 0 0!important;padding:0!important;overflow:hidden!important">理解Javascript的闭包</font>
                    </a>
                </td>
                <td width="102" valign="top" style="padding:5px!important;margin:0!important;border-left:1px solid #dddddd!important">
                    <a title="Javascript的两本书" style="text-decoration:none!important" href="http://app.wumii.com/ext/redirect?url=http%3A%2F%2Fcoolshell.cn%2Farticles%2F1608.html&amp;from=http%3A%2F%2Fcoolshell.cn%2Farticles%2F6441.html">
                        <img style="margin:0!important;padding:2px!important;border:1px solid #dddddd!important;width:96px!important;height:96px!important" src="http://static.wumii.com/site_images/2011/11/08/10437626.jpg" width="96px" height="96px"><br>
                        <font size="-1" color="#333333" style="display:block!important;line-height:15px!important;width:102px!important;font:12px/15px arial!important;height:60px!important;margin:3px 0 0 0!important;padding:0!important;overflow:hidden!important">Javascript的两本书</font>
                    </a>
                </td>
                <td width="102" valign="top" style="padding:5px!important;margin:0!important;border-left:1px solid #dddddd!important">
                    <a title="一段Javascript的代码" style="text-decoration:none!important" href="http://app.wumii.com/ext/redirect?url=http%3A%2F%2Fcoolshell.cn%2Farticles%2F3540.html&amp;from=http%3A%2F%2Fcoolshell.cn%2Farticles%2F6441.html">
                        <img style="margin:0!important;padding:2px!important;border:1px solid #dddddd!important;width:96px!important;height:96px!important" src="http://static.wumii.com/images/blogWidget/wordpress_default.gif" width="96px" height="96px"><br>
                        <font size="-1" color="#333333" style="display:block!important;line-height:15px!important;width:102px!important;font:12px/15px arial!important;height:60px!important;margin:3px 0 0 0!important;padding:0!important;overflow:hidden!important">一段Javascript的代码</font>
                    </a>
                </td>
                <td width="102" valign="top" style="padding:5px!important;margin:0!important;border-left:1px solid #dddddd!important">
                    <a title="Javascript程序员嘴最脏??" style="text-decoration:none!important" href="http://app.wumii.com/ext/redirect?url=http%3A%2F%2Fcoolshell.cn%2Farticles%2F1850.html&amp;from=http%3A%2F%2Fcoolshell.cn%2Farticles%2F6441.html">
                        <img style="margin:0!important;padding:2px!important;border:1px solid #dddddd!important;width:96px!important;height:96px!important" src="http://static.wumii.com/site_images/2011/11/08/10437573.jpg" width="96px" height="96px"><br>
                        <font size="-1" color="#333333" style="display:block!important;line-height:15px!important;width:102px!important;font:12px/15px arial!important;height:60px!important;margin:3px 0 0 0!important;padding:0!important;overflow:hidden!important">Javascript程序员嘴最脏??</font>
                    </a>
                </td>
        </tr>
    
    <tr>
        <td colspan="5" align="right">
            <a style="text-decoration:none!important" href="http://www.wumii.com/widget/relatedItems" title="无觅相关文章插件">
                <font size="-1" color="#bbbbbb" style="display:block!important;font-family:arial!important;padding:5px 0!important;font-size:12px!important;color:#bbb!important">无觅</font>
            </a>
        </td>
    </tr>
</table><h3>相关文章</h3><ul><li>2012年03月07日 -- <a href="http://coolshell.cn/articles/6731.html" title="理解Javascript的闭包">理解Javascript的闭包</a></li><li>2012年02月27日 -- <a href="http://coolshell.cn/articles/6668.html" title="再谈javascript面向对象编程 ">再谈javascript面向对象编程 </a></li><li>2011年08月15日 -- <a href="http://coolshell.cn/articles/5202.html" title="对象的消息模型">对象的消息模型</a></li><li>2011年12月07日 -- <a href="http://coolshell.cn/articles/6043.html" title="Web开发中需要了解的东西">Web开发中需要了解的东西</a></li><li>2011年11月24日 -- <a href="http://coolshell.cn/articles/5537.html" title="一些文章资源和趣闻">一些文章资源和趣闻</a></li><li>2011年10月31日 -- <a href="http://coolshell.cn/articles/5709.html" title="API设计：用流畅接口构造内部DSL">API设计：用流畅接口构造内部DSL</a></li><li>2011年07月21日 -- <a href="http://coolshell.cn/articles/5035.html" title="面向对象的Shell脚本">面向对象的Shell脚本</a></li></ul>