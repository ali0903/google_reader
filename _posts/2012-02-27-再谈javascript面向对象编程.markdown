---
layout: post
title:  "再谈javascript面向对象编程"
date:   2012-02-27 08:25:13
author: Neo
categories: program
---

## 再谈javascript面向对象编程
### by Neo
### at 2012-02-27 08:25:13
### original <http://coolshell.cn/articles/6668.html>

<p><strong>前言:</strong>虽有陈皓<a href="http://coolshell.cn/articles/6441.html">《Javascript 面向对象编程》</a>珠玉在前，但是我还是忍不住再画蛇添足的补上一篇文章，主要是因为javascript这门语言魅力。另外这篇文章是一篇入门文章，我也是才开始学习Javascript，有一点心得，才想写一篇这样文章，文章中难免有错误的地方，还请各位不吝吐槽指正</p>
<h4><strong><span style="color:#008000">吐槽Javascript</span></strong></h4>
<p>初次接触Javascript，这门语言的确会让很多正规军感到诸多的不适，这种不适来自于Javascript的语法的简练和不严谨，这种不适也来自Javascript这个悲催的名称，我在想网景公司的Javascript设计者在给他起名称那天一定是脑壳进水了,让Javascript这么多年来受了这么多不白之冤，人们都认为他是Java的附属物，一个WEB玩具语言。因此才会有些人会对Javascript不屑，认为Javascript不是一门真正的语言，但是这此他们真的错了。Javascript不仅是一门语言，是一门真真正正的语言，而且他还是一门里程碑式的语言，他独创多种新的编程模式原型继承，闭包（<strong>作者注：闭包不是JS首创，应该Scheme首创，prototypal inheritance 和 dynamic objects 是self语言首创，Javascript的首创并不精彩,谢谢网友的指正。</strong>），对后来的动态语言产生了巨大的影响。做为当今最流行的语言（没有之一），看看git上提交的最多的语言类型就能明白。随着HTML5的登场，浏览器将在个人电脑上将大显身手，完全有替换OS的趋势的时候，Javascript做为浏览器上的一门唯一真真的语言，如同C之于 unix/linux，java之于JVM，Cobol之于MainFrame，我们也需要来重新的认真地认识和审视这门语言。另外Javascript的正式名称是：ECMAScript，这个名字明显比Javascript帅太多了！<br>
<span></span><br>
言归正传，我们切入主题——Javascript的面向对象编程。要谈Javascript的面向对象编程，我们第一步要做的事情就是忘记我们所学的面向对象编程。传统C++或Java的面向对象思维来学习Javascript的面向对象会给你带来不少困惑，让我们先忘记我们所学的，从新开始学习这门特殊的面向对象编程。既然是OO编程，要如何来理解OO编程呢，记得以前学C++，学了很久都不入门，后来有幸读了《Inside The C++ Object Model》这本大作，顿时豁然开朗，因此本文也将以对象模型的方式来探讨的Javascript的OO编程。因为Javascript 对象模型的特殊性，所以使得Javascript的继承和传统的继承非常不一样，同时也因为Javascript里面没有类，这意味着Javascript里面没有extends,implements。那么Javascript到底是如何来实现OO编程的呢？好吧，让我们开始吧，一起在Javascript的OO世界里来一次漫游</p>
<p>首先，我们需要先看看Javascript如何定义一个对象。下面是我们的一个对象定义：</p>
<pre>
var o = {};
</pre>
<p>还可以这样定义一个对象</p>
<pre>
function f() {
}
</pre>
<p>对，你们没有看错，在Javascript里面，函数也是对象。<br>
当然还可以</p>
<pre>
var array1= [ 1,2,3];
</pre>
<p>数组也是一个对象。<br>
其他关于对象的基本的概念的描述，还是请各位亲们参见陈皓<a href="http://coolshell.cn/articles/6441.html">《Javascript 面向对象编程》</a>文章。<br>
对象都有了，唯一没有的就是class，因为在Javascript里面是没有class关键字的，算好还有function，function的存在让我们可以变通的定义类，在扩展这个主题前，我们还需要了解一个Javascript对象最重要的属性，<strong>__proto__</strong>成员。</p>
<h4><strong><span style="color:#008000">__proto__成员</span></strong></h4>
<p>严格的说这个成员不应该叫这个名字，__proto__是Firefox中的称呼，__proto__只有在Firefox浏览器中才能被访问到。<strong>做为一个对象，当你访问其中的一个成员或方法的时候，如果这个对象中没有这个方法或成员，那么Javascript引擎将会访问这个对象的__proto__成员所指向的另外的一个对象，并在那个对象中查找指定的方法或成员，如果不能找到，那就会继续通过那个对象的__proto__成员指向的对象进行递归查找，直到这个链表结束</strong>。<br>
好了，让我们举一个例子。<br>
比如上上面定义的数组对象array1。当我们创建出array1这个对象的时候，array1实际在Javascript引擎中的对象模型如下：<br>
<img src="http://coolshell.cn/wp-content/uploads/2012/02/joo_1.png" alt="" width="416" height="208"><br>
array1对象具有一个length属性值为3，但是我们可以通过如下的方法来为array1增加元素：</p>
<pre>
array1.push(4);
</pre>
<p>push这个方法来自于array1的__proto__成员指向对象的一个方法(Array.prototye.push())。正是因为所有的数组对象（通过[]来创建的）都包含有一个指向同一个具有push,reverse等方法对象(Array.prototype)的__proto__成员，才使得这些数组对象可以使用push,reverse等方法。</p>
<p>那么这个__proto__这个属性就相当于面向对象中的”has a”关系，这样的的话，只要我们有一个模板对象比如Array.prototype这个对象，然后把其他的对象__proto__属性指向这个对象的话就完成了一种继承的模式。不错！我们完全可以这么干。但是别高兴的太早，这个属性只在FireFox中有效，其他的浏览器虽然也有属性，但是不能通过__proto__来访问，只能通过getPrototypeOf方法进行访问，而且这个属性是只读的。看来我们要在Javascript实现继承并不是很容易的事情啊。</p>
<h4><strong><span style="color:#008000">函数对象prototype成员</span></strong></h4>
<p>首先我们先来看一段函数prototype成员的定义，</p>
<blockquote><p><strong>When a function object is created, it is given a prototype member which is an object containing a constructor member which is a reference to the function object</strong><br>
当一个函数对象被创建时，这个函数对象就具有一个prototype成员，这个成员是一个对象，这个对象包含了一个构造子成员，这个构造子成员会指向这个函数对象。</p></blockquote>
<p>例如：</p>
<pre>
function Base() {
    this.id = &quot;base&quot;
}
</pre>
<p>Base这个函数对象就具有一个prototype成员，关于构造子其实Base函数对象自身，为什么我们将这类函数称为构造子呢？原因是因为这类函数设计来和new 操作符一起使用的。为了和一般的函数对象有所区别，这类函数的首字母一般都大写。构造子的主要作用就是来创建一类相似的对象。</p>
<p>上面这段代码在Javascript引擎的对象模型是这样的<br>
<img src="http://coolshell.cn/wp-content/uploads/2012/02/joo_2.png" alt="" width="382" height="190"></p>
<h4><strong><span style="color:#008000">new 操作符</span></strong></h4>
<p>在有上面的基础概念的介绍之后，在加上new操作符，我们就能完成传统面向对象的class + new的方式创建对象，在Javascript中，我们将这类方式成为Pseudoclassical。<br>
基于上面的例子，我们执行如下代码</p>
<pre>
var obj = new Base();
</pre>
<p>这样代码的结果是什么，我们在Javascript引擎中看到的对象模型是：<br>
<img src="http://coolshell.cn/wp-content/uploads/2012/02/joo_3.png" alt="" width="403" height="207"></p>
<p>new操作符具体干了什么呢?其实很简单，就干了三件事情。</p>
<pre>
var obj  = {};
obj.__proto__ = Base.prototype;
Base.call(obj);
</pre>
<p>第一行，我们创建了一个空对象obj<br>
第二行，我们将这个空对象的__proto__成员指向了Base函数对象prototype成员对象<br>
第三行，我们将Base函数对象的this指针替换成obj，然后再调用Base函数，于是我们就给obj对象赋值了一个id成员变量，这个成员变量的值是”base”，关于call函数的用法，请参看陈皓<a href="http://coolshell.cn/articles/6441.html">《Javascript 面向对象编程》</a>文章<br>
如果我们给Base.prototype的对象添加一些函数会有什么效果呢？<br>
例如代码如下：</p>
<pre>
Base.prototype.toString = function() {
    return this.id;
}
</pre>
<p>那么当我们使用new创建一个新对象的时候，根据__proto__的特性，toString这个方法也可以做新对象的方法被访问到。于是我们看到了：<br>
<strong>构造子中，我们来设置‘类’的成员变量（例如：例子中的id），构造子对象prototype中我们来设置‘类’的公共方法。于是通过函数对象和Javascript特有的__proto__与prototype成员及new操作符，模拟出类和类实例化的效果。</strong></p>
<h4><strong><span style="color:#008000">Pseudoclassical 继承</span></strong></h4>
<p>我们模拟类，那么继承又该怎么做呢？其实很简单，我们只要将构造子的prototype指向父类即可。例如我们设计一个Derive 类。如下</p>
<pre>
function Derive(id) {
    this.id = id;
}
Derive.prototype = new Base();
Derive.prototype.test = function(id){
    return this.id === id;
}
var newObj = new Derive(&quot;derive&quot;);
</pre>
<p>这段代码执行后的对象模型又是怎么样的呢？根据之前的推导，应该是如下的对象模型<br>
<img src="http://coolshell.cn/wp-content/uploads/2012/02/joo_4.png" alt="" width="645" height="216"><br>
这样我们的newObj也继承了基类Base的toString方法，并且具有自身的成员id。关于这个对象模型是如何被推导出来的就留给各位同学了，参照前面的描述，推导这个对象模型应该不难。<br>
Pseudoclassical继承会让学过C++/Java的同学略微的感受到一点舒服，特别是new关键字，看到都特亲切，不过两者虽然相似，但是机理完全不同。当然不关什么样继承都是不能离不开__proto__成员的。</p>
<h4><strong><span style="color:#008000">Prototypal继承</span></strong></h4>
<p>这是Javascript的另外一种继承方式，这个继承也就是之前陈皓文章《Javascript 面向对象编程》中create函数，非常可惜的是这个是ECMAScript V5的标准，支持V5的浏览器目前看来也就是IE9，Chrome最新版本和Firefox。虽然看着多，但是做为IE6的重灾区的中国，我建议各位还是避免使用create函数。好在没有create函数之前，Javascript的使用者已经设计出了等同于这个函数的。例如：我们看看Douglas Crockford的object函数。</p>
<pre>
function object(old) {
   function F() {};
   F.prototype = old;
   return new F();
}
var newObj = object(oldObject);
</pre>
<p>例如如下代码段</p>
<pre>
var base ={
  id:&quot;base&quot;,
  toString:function(){
          return this.id;
  }
};
var derive = object(base);
</pre>
<p>上面函数的执行后的对象模型是：<br>
<img src="http://coolshell.cn/wp-content/uploads/2012/02/joo_5.png" alt="" width="451" height="230"><br>
如何形成这样的对象模型，原理也很简单，只要把object这个函数扩展一下，就能画出这个模型，怎么画留给读者自己去画吧。<br>
这样的继承方式被称为原型继承。相对来说要比Pseudoclassical继承来的简单方便。ECMAScript V5正是因为这原因也才增加create函数，让开发者可以快速的实现原型继承。<br>
上述两种继承方式是Javascript中最常用的继承方式。通过本文的讲解，你应该对Javascript的OO编程有了一些‘原理’级的了解了吧</p>
<h4><strong><span style="color:#008000">参考:</span></strong></h4>
<p><a href="http://msdn.microsoft.com/en-us/scriptjunkie/ff852808">《Prototypes and Inheritance in JavaScript Prototypes and Inheritance in JavaScript》</a><br>
<a href="http://yuiblog.com/blog/2006/11/27/video-crockford-advjs/">Advance Javascript</a> （Douglas Crockford 大神的视频，一定要看啊）</p>
<h4><strong><span style="color:#008000">题外话：</span></strong></h4>
<p>web2.0后，web应用可谓飞速发展，如今在HTML5发布之际，浏览器的功能被大大强化，我感觉Browser远远在不是一个Browser那么简单了。记得C++之父曾经这样说过JAVA，JAVA不是跨平台，JAVA本身就是一个平台。如今的Browser也本身就是一个平台了，好在这个平台是基于标准的。如果Browser是平台，由于Browser安全沙箱的限制，个人电脑的资源被使用的很少，感觉Browser就是一个NC（Network Computer）？我们居然又回到了Sun最初提出的构想，Sun是不是太强大了些？</p>
<table cellspacing="0" cellpadding="3" border="0" style="clear:both">
    
    <tr>
        <td colspan="5"><b><font size="-1" style="display:block!important;padding:20px 0 5px!important">您可能也喜欢：</font></b></td>
    </tr>
    
        <tr>
                <td width="102" valign="top" style="padding:5px!important;margin:0!important">
                    <a title="Javascript 面向对象编程" style="text-decoration:none!important" href="http://app.wumii.com/ext/redirect?url=http%3A%2F%2Fcoolshell.cn%2Farticles%2F6441.html&amp;from=http%3A%2F%2Fcoolshell.cn%2Farticles%2F6668.html">
                        <img style="margin:0!important;padding:2px!important;border:1px solid #dddddd!important;width:96px!important;height:96px!important" src="http://static.wumii.com/images/blogWidget/wordpress_default.gif" width="96px" height="96px"><br>
                        <font size="-1" color="#333333" style="display:block!important;line-height:15px!important;width:102px!important;font:12px/15px arial!important;height:60px!important;margin:3px 0 0 0!important;padding:0!important;overflow:hidden!important">Javascript 面向对象编程</font>
                    </a>
                </td>
                <td width="102" valign="top" style="padding:5px!important;margin:0!important;border-left:1px solid #dddddd!important">
                    <a title="理解Javascript的闭包" style="text-decoration:none!important" href="http://app.wumii.com/ext/redirect?url=http%3A%2F%2Fcoolshell.cn%2Farticles%2F6731.html&amp;from=http%3A%2F%2Fcoolshell.cn%2Farticles%2F6668.html">
                        <img style="margin:0!important;padding:2px!important;border:1px solid #dddddd!important;width:96px!important;height:96px!important" src="http://static.wumii.com/site_images/2012/03/07/16657660.png" width="96px" height="96px"><br>
                        <font size="-1" color="#333333" style="display:block!important;line-height:15px!important;width:102px!important;font:12px/15px arial!important;height:60px!important;margin:3px 0 0 0!important;padding:0!important;overflow:hidden!important">理解Javascript的闭包</font>
                    </a>
                </td>
                <td width="102" valign="top" style="padding:5px!important;margin:0!important;border-left:1px solid #dddddd!important">
                    <a title="又一个Javascript试验田" style="text-decoration:none!important" href="http://app.wumii.com/ext/redirect?url=http%3A%2F%2Fcoolshell.cn%2Farticles%2F2276.html&amp;from=http%3A%2F%2Fcoolshell.cn%2Farticles%2F6668.html">
                        <img style="margin:0!important;padding:2px!important;border:1px solid #dddddd!important;width:96px!important;height:96px!important" src="http://static.wumii.com/images/blogWidget/wordpress_default.gif" width="96px" height="96px"><br>
                        <font size="-1" color="#333333" style="display:block!important;line-height:15px!important;width:102px!important;font:12px/15px arial!important;height:60px!important;margin:3px 0 0 0!important;padding:0!important;overflow:hidden!important">又一个Javascript试验田</font>
                    </a>
                </td>
                <td width="102" valign="top" style="padding:5px!important;margin:0!important;border-left:1px solid #dddddd!important">
                    <a title="Javascript的两本书" style="text-decoration:none!important" href="http://app.wumii.com/ext/redirect?url=http%3A%2F%2Fcoolshell.cn%2Farticles%2F1608.html&amp;from=http%3A%2F%2Fcoolshell.cn%2Farticles%2F6668.html">
                        <img style="margin:0!important;padding:2px!important;border:1px solid #dddddd!important;width:96px!important;height:96px!important" src="http://static.wumii.com/site_images/2011/11/08/10437626.jpg" width="96px" height="96px"><br>
                        <font size="-1" color="#333333" style="display:block!important;line-height:15px!important;width:102px!important;font:12px/15px arial!important;height:60px!important;margin:3px 0 0 0!important;padding:0!important;overflow:hidden!important">Javascript的两本书</font>
                    </a>
                </td>
                <td width="102" valign="top" style="padding:5px!important;margin:0!important;border-left:1px solid #dddddd!important">
                    <a title="20个优秀的Javascript导航技术" style="text-decoration:none!important" href="http://app.wumii.com/ext/redirect?url=http%3A%2F%2Fcoolshell.cn%2Farticles%2F918.html&amp;from=http%3A%2F%2Fcoolshell.cn%2Farticles%2F6668.html">
                        <img style="margin:0!important;padding:2px!important;border:1px solid #dddddd!important;width:96px!important;height:96px!important" src="http://static.wumii.com/images/blogWidget/wordpress_default.gif" width="96px" height="96px"><br>
                        <font size="-1" color="#333333" style="display:block!important;line-height:15px!important;width:102px!important;font:12px/15px arial!important;height:60px!important;margin:3px 0 0 0!important;padding:0!important;overflow:hidden!important">20个优秀的Javascript导航技术</font>
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
</table><h3>相关文章</h3><ul><li>2012年03月07日 -- <a href="http://coolshell.cn/articles/6731.html" title="理解Javascript的闭包">理解Javascript的闭包</a></li><li>2012年01月09日 -- <a href="http://coolshell.cn/articles/6441.html" title="Javascript 面向对象编程">Javascript 面向对象编程</a></li><li>2011年08月15日 -- <a href="http://coolshell.cn/articles/5202.html" title="对象的消息模型">对象的消息模型</a></li><li>2011年12月07日 -- <a href="http://coolshell.cn/articles/6043.html" title="Web开发中需要了解的东西">Web开发中需要了解的东西</a></li><li>2011年11月24日 -- <a href="http://coolshell.cn/articles/5537.html" title="一些文章资源和趣闻">一些文章资源和趣闻</a></li><li>2011年10月31日 -- <a href="http://coolshell.cn/articles/5709.html" title="API设计：用流畅接口构造内部DSL">API设计：用流畅接口构造内部DSL</a></li><li>2011年07月21日 -- <a href="http://coolshell.cn/articles/5035.html" title="面向对象的Shell脚本">面向对象的Shell脚本</a></li></ul>