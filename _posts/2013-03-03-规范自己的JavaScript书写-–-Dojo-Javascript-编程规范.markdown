---
layout: post
title:  "规范自己的JavaScript书写 – Dojo Javascript 编程规范"
date:   2013-03-03 22:21:45
author: 
categories: program
---

## 规范自己的JavaScript书写 – Dojo Javascript 编程规范
### by 
### at 2013-03-03 22:21:45
### original <http://item.feedsky.com/~feedsky/bingo929/~7170939/664471539/5279850/1/item.html>

<p><a href="http://blog.bingo929.com/dojo-javascript-style-guide.html"><img src="http://blog.bingo929.com/wp-content/uploads/2010/10/title3.jpg" alt="Dojo Javascript 编程规范" title="规范自己的JavaScript书写" width="500" height="150"></a></p>
<h3>前言</h3>
<p>良好的JavaScript书写习惯的优点不言而喻，今天彬Go向大家推荐Dojo <a href="http://blog.bingo929.com/category/technology/javascript">Javascript</a> 编程规范，相当不错的 Javascript 编程风格规范，建议大家可以借鉴一下此规范编写 Javascript。感谢i.feelinglucky的翻译。<br>
<span></span></p>
<h3>序</h3>
<p>Any violation to this guide is allowed if it enhances readability.</p>
<p>所有的代码都要变成可供他人容易阅读的。</p>
<h3>快读参考</h3>
<p>核心 API 请使用下面的风格：</p>
<table style="border:1px solid #ccc">
<tbody>
<tr>
<td><strong>结构</strong></td>
<td><strong>规则</strong></td>
<td><strong>注释</strong></td>
</tr>
<tr>
<td>模块</td>
<td>小写</td>
<td>不要使用多重语义（Never multiple words）</td>
</tr>
<tr>
<td>类</td>
<td>骆驼</td>
<td></td>
</tr>
<tr>
<td>公有方法</td>
<td>混合</td>
<td>其他的外部调用也可以使用 lower_case()，这样的风格</td>
</tr>
<tr>
<td>公有变量</td>
<td>混合</td>
<td></td>
</tr>
<tr>
<td>常量</td>
<td>骆驼 或 大写</td>
<td></td>
</tr>
</tbody>
</table>
<p>下面的虽然不是必要的，但建议使用：</p>
<table style="border:1px solid #ccc">
<tbody>
<tr>
<td><strong>结构</strong></td>
<td><strong>规则</strong></td>
</tr>
<tr>
<td>私有方法</td>
<td>混合，例子：<tt>_mixedCase</tt></td>
</tr>
<tr>
<td>私有变量</td>
<td>混合，例子：<tt>_mixedCase</tt></td>
</tr>
<tr>
<td>方法（method）参数</td>
<td>混合，例子：<tt>_mixedCase, mixedCase</tt></td>
</tr>
<tr>
<td>本地（local）变量</td>
<td>混合，例子：<tt>_mixedCase, mixedCase</tt>
<p><tt> </tt></p></td>
</tr>
</tbody>
</table>
<h3>命名规范</h3>
<p>1.变量名称 必须为 小写字母。<br>
2.类的命名使用骆驼命名规则，例如：</p>
<div style="overflow:auto;white-space:nowrap;border:1px solid #9f9f9f;width:435px"><div style="padding:5px;font:normal 12px/1.4em Monaco,Lucida Console,monospace;white-space:nowrap">   Account<span style="color:#339933">,</span> EventHandler</div></div>
<p>3.常量 必须 在对象（类）或者枚举变量的前部声明。枚举变量的命名必须要有实际的意义，并且其成员 必须 使用骆驼命名规则或使用大写：</p>
<div style="overflow:auto;white-space:nowrap;border:1px solid #9f9f9f;width:435px"><div style="padding:5px;font:normal 12px/1.4em Monaco,Lucida Console,monospace;white-space:nowrap"><span style="color:#003366;font-weight:bold">var</span> NodeTypes <span style="color:#339933">=</span> <span style="color:#009900">{</span><br>
    Element <span style="color:#339933">:</span> <span style="color:#cc0000">1</span><span style="color:#339933">,</span><br>
    DOCUMENT<span style="color:#339933">:</span> <span style="color:#cc0000">2</span><br>
<span style="color:#009900">}</span></div></div>
<p>4.简写单词 不能使用 大写名称作为变量名：</p>
<div style="overflow:auto;white-space:nowrap;border:1px solid #9f9f9f;width:435px"><div style="padding:5px;font:normal 12px/1.4em Monaco,Lucida Console,monospace;white-space:nowrap">getInnerHtml<span style="color:#009900">(</span><span style="color:#009900">)</span><span style="color:#339933">,</span> getXml<span style="color:#009900">(</span><span style="color:#009900">)</span><span style="color:#339933">,</span> XmlDocument</div></div>
<p>5.方法的命令 必须 为动词或者是动词短语：</p>
<div style="overflow:auto;white-space:nowrap;border:1px solid #9f9f9f;width:435px"><div style="padding:5px;font:normal 12px/1.4em Monaco,Lucida Console,monospace;white-space:nowrap">obj.<span style="color:#660066">getSomeValue</span><span style="color:#009900">(</span><span style="color:#009900">)</span></div></div>
<p>6.公有类的命名 必须 使用混合名称（mixedCase）命名。<br>
7.CSS 变量的命名 必须 使用其对应的相同的公共类变量。<br>
8.私有类的变量属性成员 必须 使用混合名称（mixedCase）命名，并前面下下划线（_）。例如：</p>
<div style="overflow:auto;white-space:nowrap;border:1px solid #9f9f9f;width:435px"><div style="padding:5px;font:normal 12px/1.4em Monaco,Lucida Console,monospace;white-space:nowrap"><span style="color:#003366;font-weight:bold">var</span> MyClass <span style="color:#339933">=</span> <span style="color:#003366;font-weight:bold">function</span><span style="color:#009900">(</span><span style="color:#009900">)</span><span style="color:#009900">{</span><br>
   <span style="color:#003366;font-weight:bold">var</span> _buffer<span style="color:#339933">;</span><br>
   <span style="color:#000066;font-weight:bold">this</span>.<span style="color:#660066">doSomething</span> <span style="color:#339933">=</span> <span style="color:#003366;font-weight:bold">function</span><span style="color:#009900">(</span><span style="color:#009900">)</span><span style="color:#009900">{</span><br>
   <span style="color:#009900">}</span><span style="color:#339933">;</span><br>
<span style="color:#009900">}</span></div></div>
<p>9.变量如果设置为私有，则前面 必须 添加下划线。</p>
<div style="overflow:auto;white-space:nowrap;border:1px solid #9f9f9f;width:435px"><div style="padding:5px;font:normal 12px/1.4em Monaco,Lucida Console,monospace;white-space:nowrap"><span style="color:#000066;font-weight:bold">this</span>._somePrivateVariable <span style="color:#339933">=</span> statement<span style="color:#339933">;</span></div></div>
<p>10.通用的变量 必须 使用与其名字一致的类型名称：</p>
<div style="overflow:auto;white-space:nowrap;border:1px solid #9f9f9f;width:435px"><div style="padding:5px;font:normal 12px/1.4em Monaco,Lucida Console,monospace;white-space:nowrap">setTopic<span style="color:#009900">(</span>topic<span style="color:#009900">)</span> <span style="color:#006600;font-style:italic">// 变量 topic 为 Topic 类型的变量</span></div></div>
<p>11.所有的变量名 必须 使用英文名称。<br>
12.变量如有较广的作用域（large scope），必须使用全局变量；此时可以设计成一个类的成员。相对的如作用域较小或为私有变量则使用简洁的单词命名。<br>
13.如果变量有其隐含的返回值，则避免使用其相似的方法：</p>
<div style="overflow:auto;white-space:nowrap;border:1px solid #9f9f9f;width:435px"><div style="padding:5px;font:normal 12px/1.4em Monaco,Lucida Console,monospace;white-space:nowrap">getHandler<span style="color:#009900">(</span><span style="color:#009900">)</span><span style="color:#339933">;</span> <span style="color:#006600;font-style:italic">// 避免使用 getEventHandler()</span></div></div>
<p>14.公有变量必须清楚的表达其自身的属性，避免字义含糊不清，例如：</p>
<div style="overflow:auto;white-space:nowrap;border:1px solid #9f9f9f;width:435px"><div style="padding:5px;font:normal 12px/1.4em Monaco,Lucida Console,monospace;white-space:nowrap">MouseEventHandler</div></div>
<p>，而非 MseEvtHdlr。<br>
　　请再次注意这条规定，这样做得的好处是非常明显的。它能明确的表达表达式所定义的含义。例如：<br>
　　　</p>
<div style="overflow:auto;white-space:nowrap;border:1px solid #9f9f9f;width:435px"><div style="padding:5px;font:normal 12px/1.4em Monaco,Lucida Console,monospace;white-space:nowrap">dojo.<span style="color:#660066">events</span>.<span style="color:#660066">mouse</span>.<span style="color:#660066">Handler</span> <span style="color:#006600;font-style:italic">// 而非 dojo.events.mouse.MouseEventHandler</span></div></div>
<p>15.类/构造函数 可以使用 扩展其基类的名称命名，这样可以正确、迅速的找到其基类的名称：<br>
　EventHandler<br>
　UIEventHandler<br>
　MouseEventHandler<br>
　　基类可以在明确描述其属性的前提下，缩减其命名：<br>
　　　MouseEventHandler as opposed to MouseUIEventHandler.</p>
<h3>特殊命名规范</h3>
<ol>
<li>术语 “get/set” 不要和一个字段相连，除非它被定义为私有变量。</li>
<li>前面加 “is” 的变量名 应该 为布尔值，同理可以为 “has”, “can” 或者 “should”。</li>
<li>术语 “compute” 作为变量名应为已经计算完成的变量。</li>
<li>术语 “find” 作为变量名应为已经查找完成的变量。</li>
<li>术语 “initialize” 或者 “init” 作为变量名应为已经实例化（初始化）完成的类或者其他类型的变量。</li>
<li>UI （用户界面）控制变量应在名称后加控制类型，例如： leftComboBox, TopScrollPane。</li>
<li>复数必须有其公共的名称约定（原文：Plural form MUST be used to name collections）。</li>
<li>带有 “num” 或者 “count” 开头的变量名约定为数字（对象）。</li>
<li>重复变量建议使用 “i”, “j”, “k” （依次类推）等名称的变量。</li>
<li>补充用语必须使用补充词，例如： get/set, add/remove, create/destroy, start/stop, insert/delete, begin/end, etc.</li>
<li>能缩写的名称尽量使用缩写。</li>
<li>避免产生歧义的布尔变量名称，例如：<br>
　　isNotError, isNotFound 为非法</li>
<li>错误类建议在变量名称后加上 “Exception” 或者 “Error”。</li>
<li>方法如果返回一个类，则应该在名称上说明返回什么；如果是一个过程，则应该说明做了什么。</li>
</ol>
<h3>文件</h3>
<p>缩进请使用 4 个空白符的制表位。<br>
如果您的编辑器支持 文件标签_（file tags），请加添如下的一行使我们的代码更容易阅读：</p>
<div style="overflow:auto;white-space:nowrap;border:1px solid #9f9f9f;width:435px"><div style="padding:5px;font:normal 12px/1.4em Monaco,Lucida Console,monospace;white-space:nowrap"><span style="color:#006600;font-style:italic">// vim:ts=4:noet:tw=0:</span></div></div>
<p>译注：老外用 VIM 编辑器比较多，此条可以选择遵循。</p>
<p>代码折叠必须看起来是完成并且是合乎逻辑的：</p>
<div style="overflow:auto;white-space:nowrap;border:1px solid #9f9f9f;width:435px"><div style="padding:5px;font:normal 12px/1.4em Monaco,Lucida Console,monospace;white-space:nowrap"><span style="color:#003366;font-weight:bold">var</span> someExpression <span style="color:#339933">=</span> Expression1<br>
    <span style="color:#339933">+</span> Expression2<br>
    <span style="color:#339933">+</span> Expression3<span style="color:#339933">;</span><br>
<br>
<span style="color:#003366;font-weight:bold">var</span> o <span style="color:#339933">=</span> someObject.<span style="color:#660066">get</span><span style="color:#009900">(</span><br>
    Expression1<span style="color:#339933">,</span><br>
    Expression2<span style="color:#339933">,</span><br>
    Expression3<br>
<span style="color:#009900">)</span><span style="color:#339933">;</span></div></div>
<p>注：表达式的缩进与变量声明应为一致的。<br>
注：函数的参数应采用明确的缩进，缩进规则与其他块保持一致。</p>
<h3>变量</h3>
<ol>
<li>变量必须在声明初始化以后才能使用，即便是 NULL 类型。</li>
<li>变量不能产生歧义。</li>
<li>相关的变量集应该放在同一代码块中，非相关的变量集不应该放在同一代码块中。</li>
<li>变量应该尽量保持最小的生存周期。</li>
<li>循环/重复变量的规范：</li>
<ol>
<li>只有循环控制块的话，则必须使用 FOR 循环。</li>
<li>循环变量应该在循环开始前就被初始化；如使用 FOR 循环，则使用 FOR 语句初始化循环变量。</li>
<li>“do … while” 语句是被允许的。</li>
<li>“break” 和 “continue” 语句仍然允许使用（但请注意）。</li>
</ol>
<li>条件表达式</li>
<ol>
<li>应该尽量避免复杂的条件表达式，如有必要可以使用临时布尔变量。</li>
<li>The nominal case SHOULD be put in the “if” part and the exception in the “else” part of an “if” statement.</li>
<li>应避免在条件表达式中加入块。</li>
</ol>
<li>杂项</li>
<ol>
<li>尽量避免幻数（Magic numbers），他们应该使用常量来代替。</li>
<li>浮点变量必须指明小数点后一位（即使是 0）。</li>
<li>浮点变量必须指明实部，即使它们为零（使用 0. 开头）。</li>
</ol>
</ol>
<h3>布局</h3>
<h4>块</h4>
<ol>
<li>
普通代码段 应该 看起来如下：
<div style="overflow:auto;white-space:nowrap;border:1px solid #9f9f9f;width:435px"><div style="padding:5px;font:normal 12px/1.4em Monaco,Lucida Console,monospace;white-space:nowrap"><span style="color:#000066;font-weight:bold">while</span> <span style="color:#009900">(</span><span style="color:#339933">!</span>isDone<span style="color:#009900">)</span><span style="color:#009900">{</span><br>
        doSomething<span style="color:#009900">(</span><span style="color:#009900">)</span><span style="color:#339933">;</span><br>
    isDone <span style="color:#339933">=</span> moreToDo<span style="color:#009900">(</span><span style="color:#009900">)</span><span style="color:#339933">;</span><br>
<span style="color:#009900">}</span></div></div>
</li>
<li>
IF 语句 应该 看起来像这样：
<div style="overflow:auto;white-space:nowrap;border:1px solid #9f9f9f;width:435px"><div style="padding:5px;font:normal 12px/1.4em Monaco,Lucida Console,monospace;white-space:nowrap"><span style="color:#000066;font-weight:bold">if</span> <span style="color:#009900">(</span>someCondition<span style="color:#009900">)</span><span style="color:#009900">{</span><br>
        statements<span style="color:#339933">;</span><br>
<span style="color:#009900">}</span> <span style="color:#000066;font-weight:bold">else</span> <span style="color:#000066;font-weight:bold">if</span> <span style="color:#009900">(</span>someOtherCondition<span style="color:#009900">)</span><span style="color:#009900">{</span><br>
    statements<span style="color:#339933">;</span><br>
<span style="color:#009900">}</span> <span style="color:#000066;font-weight:bold">else</span> <span style="color:#009900">{</span><br>
    statements<span style="color:#339933">;</span><br>
<span style="color:#009900">}</span></div></div>
</li>
<li>
FOR 语句 应该 看起来像这样：
<div style="overflow:auto;white-space:nowrap;border:1px solid #9f9f9f;width:435px"><div style="padding:5px;font:normal 12px/1.4em Monaco,Lucida Console,monospace;white-space:nowrap"><span style="color:#000066;font-weight:bold">for</span> <span style="color:#009900">(</span>initialization<span style="color:#339933">;</span> condition<span style="color:#339933">;</span> update<span style="color:#009900">)</span><span style="color:#009900">{</span><br>
        statements<span style="color:#339933">;</span><br>
<span style="color:#009900">}</span></div></div>
</li>
<li>
WHILE 语句 应该 看起来像这样：
<div style="overflow:auto;white-space:nowrap;border:1px solid #9f9f9f;width:435px"><div style="padding:5px;font:normal 12px/1.4em Monaco,Lucida Console,monospace;white-space:nowrap"><span style="color:#000066;font-weight:bold">while</span> <span style="color:#009900">(</span><span style="color:#339933">!</span>isDone<span style="color:#009900">)</span> <span style="color:#009900">{</span><br>
        doSomething<span style="color:#009900">(</span><span style="color:#009900">)</span><span style="color:#339933">;</span><br>
    isDone <span style="color:#339933">=</span> moreToDo<span style="color:#009900">(</span><span style="color:#009900">)</span><span style="color:#339933">;</span><br>
<span style="color:#009900">}</span></div></div>
</li>
<li>
DO … WHILE 语句 应该 看起来像这样：
<div style="overflow:auto;white-space:nowrap;border:1px solid #9f9f9f;width:435px"><div style="padding:5px;font:normal 12px/1.4em Monaco,Lucida Console,monospace;white-space:nowrap"><span style="color:#000066;font-weight:bold">do</span> <span style="color:#009900">{</span><br>
        statements<span style="color:#339933">;</span><br>
<span style="color:#009900">}</span> <span style="color:#000066;font-weight:bold">while</span> <span style="color:#009900">(</span>condition<span style="color:#009900">)</span><span style="color:#339933">;</span></div></div>
</li>
<li>
SWITCH 语句 应该 看起来像这样：
<div style="overflow:auto;white-space:nowrap;border:1px solid #9f9f9f;width:435px"><div style="padding:5px;font:normal 12px/1.4em Monaco,Lucida Console,monospace;white-space:nowrap"><span style="color:#000066;font-weight:bold">switch</span> <span style="color:#009900">(</span>condition<span style="color:#009900">)</span> <span style="color:#009900">{</span><br>
<span style="color:#000066;font-weight:bold">case</span> ABC<span style="color:#339933">:</span><br>
    statements<span style="color:#339933">;</span><br>
    <span style="color:#006600;font-style:italic">//  fallthrough</span><br>
<span style="color:#000066;font-weight:bold">case</span> DEF<span style="color:#339933">:</span><br>
    statements<span style="color:#339933">;</span><br>
    <span style="color:#000066;font-weight:bold">break</span><span style="color:#339933">;</span><br>
<span style="color:#003366;font-weight:bold">default</span><span style="color:#339933">:</span><br>
        statements<span style="color:#339933">;</span><br>
    <span style="color:#000066;font-weight:bold">break</span><span style="color:#339933">;</span><br>
<span style="color:#009900">}</span></div></div>
</li>
<li>
TRY … CATCH 语句 应该 看起来像这样：
<div style="overflow:auto;white-space:nowrap;border:1px solid #9f9f9f;width:435px"><div style="padding:5px;font:normal 12px/1.4em Monaco,Lucida Console,monospace;white-space:nowrap"><span style="color:#000066;font-weight:bold">try</span> <span style="color:#009900">{</span><br>
    statements<span style="color:#339933">;</span><br>
<span style="color:#009900">}</span> <span style="color:#000066;font-weight:bold">catch</span><span style="color:#009900">(</span>ex<span style="color:#009900">)</span> <span style="color:#009900">{</span><br>
    statements<span style="color:#339933">;</span><br>
<span style="color:#009900">}</span> <span style="color:#000066;font-weight:bold">finally</span> <span style="color:#009900">{</span><br>
    statements<span style="color:#339933">;</span><br>
<span style="color:#009900">}</span></div></div>
</li>
<li>
单行的 IF – ELSE，WHILE 或者 FOR 语句也 必须 加入括号，不过他们可以这样写：
<div style="overflow:auto;white-space:nowrap;border:1px solid #9f9f9f;width:435px"><div style="padding:5px;font:normal 12px/1.4em Monaco,Lucida Console,monospace;white-space:nowrap"><span style="color:#000066;font-weight:bold">if</span> <span style="color:#009900">(</span>condition<span style="color:#009900">)</span><span style="color:#009900">{</span> statement<span style="color:#339933">;</span> <span style="color:#009900">}</span><br>
<span style="color:#000066;font-weight:bold">while</span> <span style="color:#009900">(</span>condition<span style="color:#009900">)</span><span style="color:#009900">{</span> statement<span style="color:#339933">;</span> <span style="color:#009900">}</span><br>
<span style="color:#000066;font-weight:bold">for</span> <span style="color:#009900">(</span>intialization<span style="color:#339933">;</span> condition<span style="color:#339933">;</span> update<span style="color:#009900">)</span><span style="color:#009900">{</span> statement<span style="color:#339933">;</span> <span style="color:#009900">}</span></div></div>
</li>
</ol>
<h3>空白</h3>
<ol>
<li>操作符 建议 使用空格隔开（包括三元操作符）。</li>
<li>下面的关键字 避免使用 空白隔开：
<ul>
<li>break</li>
<li>catch</li>
<li>continue</li>
<li>do</li>
<li>else</li>
<li>finally</li>
<li>for</li>
<li>function （如果为匿名函数，例如：var foo = function(){}; ）</li>
<li>if</li>
<li>return</li>
<li>switch</li>
<li>this</li>
<li>try</li>
<li>void</li>
<li>while</li>
<li>with</li>
</ul>
</li>
<li>下面的关键字必须使用空白隔开：
<ul>
<li>case</li>
<li>default</li>
<li>delete</li>
<li>function （如果为申明，例如：function foo(){}; ）</li>
<li>in</li>
<li>instanceof</li>
<li>new</li>
<li>throw</li>
<li>typeof</li>
<li>var</li>
</ul>
</li>
<li>逗号（,） 建议 使用空白隔开。</li>
<li>冒号（:） 建议 使用空白隔开。</li>
<li>点（.） 在后部 建议 使用空白隔开。</li>
<li>点（.） 避免 在前部使用空白。</li>
<li>函数调用和方法 避免 使用空白，例如： doSomething(someParameter); // 而非 doSomething (someParameter)</li>
<li>逻辑块 之间使用空行。</li>
<li>声明 建议 对齐使其更容易阅读。</li>
</ol>
<h3>注释</h3>
<ol>
<li>生涩的代码就 <i>没有必要</i> 添加注释了，首先您需要 <strong>重写</strong> 它们。 </li>
<li>所有的注释请使用英文。 </li>
<li>从已解决的方案到未开发的功能，注释 <i>必须</i> 与代码相关。 </li>
<li>大量的变量申明后 <i>必须</i> 跟随一段注释。 </li>
<li>注释需要说明的是代码段的用处，尤其是接下来的代码段。 </li>
<li>注释 <i>没有必要</i> 每行都添加。 </li>
</ol>
<h3>文档</h3>
<p>下面提供了一些基本的函数或者对象的描述方法：</p>
<ul>
<li><strong>总结（summary）</strong>: 简短的表述此函数或者对象实现的目的 </li>
<li><strong>描述（description）</strong>: 对于此函数或者类的简短的描述 </li>
<li><strong>返回（return）</strong>: 描述此函数返回什么（并不包括返回类型） </li>
</ul>
<h3>基本函数信息</h3>
<div style="overflow:auto;white-space:nowrap;border:1px solid #9f9f9f;width:435px"><div style="padding:5px;font:normal 12px/1.4em Monaco,Lucida Console,monospace;white-space:nowrap"><span style="color:#003366;font-weight:bold">function</span><span style="color:#009900">(</span><span style="color:#009900">)</span><span style="color:#009900">{</span><br>
    <span style="color:#006600;font-style:italic">// summary: Soon we will have enough treasure to rule all of New Jersey.</span><br>
    <span style="color:#006600;font-style:italic">// description: Or we could just get a new roomate.</span><br>
    <span style="color:#006600;font-style:italic">//          Look, you go find him.  He don&#39;t yell at you.</span><br>
    <span style="color:#006600;font-style:italic">//          All I ever try to do is make him smile and sing around</span><br>
    <span style="color:#006600;font-style:italic">//          him and dance around him and he just lays into me.</span><br>
    <span style="color:#006600;font-style:italic">//          He told me to get in the freezer &#39;cause there was a carnival in there.</span><br>
    <span style="color:#006600;font-style:italic">// returns:  Look, a Bananarama tape!</span><br>
<span style="color:#009900">}</span></div></div>
<h3>对象函数信息</h3>
<p>没有返回值描述</p>
<div style="overflow:auto;white-space:nowrap;border:1px solid #9f9f9f;width:435px"><div style="padding:5px;font:normal 12px/1.4em Monaco,Lucida Console,monospace;white-space:nowrap"><span style="color:#009900">{</span><br>
    <span style="color:#006600;font-style:italic">// summary: Dingle, engage the rainbow machine!</span><br>
    <span style="color:#006600;font-style:italic">// description: </span><br>
    <span style="color:#006600;font-style:italic">//          Tell you what, I wish I was--oh my g--that beam,</span><br>
    <span style="color:#006600;font-style:italic">//          coming up like that, the speed, you might wanna adjust that.</span><br>
    <span style="color:#006600;font-style:italic">//          It really did a number on my back, there. I mean, and I don&#39;t</span><br>
    <span style="color:#006600;font-style:italic">//          wanna say whiplash, just yet, cause that&#39;s a little too far,</span><br>
    <span style="color:#006600;font-style:italic">//          but, you&#39;re insured, right?</span><br>
<span style="color:#009900">}</span></div></div>
<h3>函数的声明</h3>
<p>在有的情况下，对于函数的调用和声明是隐义（invisible）的。在这种情况下，我们没有办法在函数中加入说明等（供程序调用）。如果您遭遇了这种情况，您可以使用一个类来封装函数。</p>
<p>注：此此方法只能在函数没有初始化的参数情况下。如过不是，则它们会被忽略。</p>
<div style="overflow:auto;white-space:nowrap;border:1px solid #9f9f9f;width:435px"><div style="padding:5px;font:normal 12px/1.4em Monaco,Lucida Console,monospace;white-space:nowrap">dojo.<span style="color:#660066">declare</span><span style="color:#009900">(</span><br>
    <span style="color:#3366cc">&quot;foo&quot;</span><span style="color:#339933">,</span><br>
    <span style="color:#003366;font-weight:bold">null</span><span style="color:#339933">,</span><br>
    <span style="color:#009900">{</span><br>
        <span style="color:#006600;font-style:italic">// summary: Phew, this sure is relaxing, Frylock.</span><br>
        <span style="color:#006600;font-style:italic">// description: </span><br>
        <span style="color:#006600;font-style:italic">//              Thousands of years ago, before the dawn of</span><br>
        <span style="color:#006600;font-style:italic">//              man as we knew him, there was Sir Santa of Claus: an</span><br>
        <span style="color:#006600;font-style:italic">//              ape-like creature making crude and pointless toys out</span><br>
        <span style="color:#006600;font-style:italic">//              of dino-bones, hurling them at chimp-like creatures with</span><br>
        <span style="color:#006600;font-style:italic">//              crinkled hands regardless of how they behaved the</span><br>
        <span style="color:#006600;font-style:italic">//              previous year.</span><br>
        <span style="color:#006600;font-style:italic">// returns: Unless Carl pays tribute to the Elfin Elders in space.</span><br>
        <span style="color:#009900">}</span><br>
<span style="color:#009900">)</span><span style="color:#339933">;</span><br>
<br>
<br>
<span style="color:#339933">&lt;</span>h3<span style="color:#339933">&gt;</span>参数<span style="color:#339933">&lt;/</span>h3<span style="color:#339933">&gt;</span><br>
<span style="color:#339933">&lt;</span>ol<span style="color:#339933">&gt;</span><br>
    <span style="color:#339933">&lt;</span>li<span style="color:#339933">&gt;</span>简单类型<br>
　　　简单的类型的参数可以直接在函数参数定义中注释说明。<br>
<span style="color:#009900">[</span>cc lang<span style="color:#339933">=</span><span style="color:#3366cc">&quot;javascript&quot;</span><span style="color:#009900">]</span><span style="color:#003366;font-weight:bold">function</span><span style="color:#009900">(</span><span style="color:#006600;font-style:italic">/*String*/</span> foo<span style="color:#339933">,</span> <span style="color:#006600;font-style:italic">/*int*/</span> bar<span style="color:#009900">)</span>...</div></div>

<li>可变类型参数<br>
　　下面是几个修饰符供参考：
<ul>
<li>? 可选参数</li>
<li>… 说面参数范围不确定</li>
<li> 数组</li>
</ul>
<div style="overflow:auto;white-space:nowrap;border:1px solid #9f9f9f;width:435px"><div style="padding:5px;font:normal 12px/1.4em Monaco,Lucida Console,monospace;white-space:nowrap"><span style="color:#003366;font-weight:bold">function</span><span style="color:#009900">(</span><span style="color:#006600;font-style:italic">/*String?*/</span> foo<span style="color:#339933">,</span> <span style="color:#006600;font-style:italic">/*int...*/</span> bar<span style="color:#339933">,</span> <span style="color:#006600;font-style:italic">/*String[]*/</span> baz<span style="color:#009900">)</span>...</div></div>
<li>全局参数描述<br>
　如果你想增加一个描述，你可以将它们移至初始化块。

<p>基本信息格式为： *关键字* 描述字段 （ *key* Descriptive sentence）<br>
参数和变量的格式为： *关键字* ~*类型*~ 描述字段 （ *key* ~*type*~ Descriptive sentence）<br>
注： *关键字* 和 ~*类型*~ 可以使用任何字母和数字表述。</p>
<div style="overflow:auto;white-space:nowrap;border:1px solid #9f9f9f;width:435px"><div style="padding:5px;font:normal 12px/1.4em Monaco,Lucida Console,monospace;white-space:nowrap"><span style="color:#003366;font-weight:bold">function</span> <span style="color:#009900">(</span>foo<span style="color:#339933">,</span> bar<span style="color:#009900">)</span> <span style="color:#009900">{</span><br>
    <span style="color:#006600;font-style:italic">// foo: String</span><br>
    <span style="color:#006600;font-style:italic">//          used for being the first parameter</span><br>
    <span style="color:#006600;font-style:italic">// bar: int</span><br>
    <span style="color:#006600;font-style:italic">//          used for being the second parameter</span><br>
<span style="color:#009900">}</span></div></div>
<h3>变量</h3>
<p>由于实例变量、原型变量和外部变量的声明是一致的，所以有很多的方法声明、修改变量。具体的如何定义和定位应在变量最先出现的位置指明变量的名称、类型、作用域等信息。</p>
<div style="overflow:auto;white-space:nowrap;border:1px solid #9f9f9f;width:435px"><div style="padding:5px;font:normal 12px/1.4em Monaco,Lucida Console,monospace;white-space:nowrap"><span style="color:#003366;font-weight:bold">function</span> foo<span style="color:#009900">(</span><span style="color:#009900">)</span> <span style="color:#009900">{</span><br>
    <span style="color:#006600;font-style:italic">// myString: String</span><br>
    <span style="color:#006600;font-style:italic">// times: int</span><br>
    <span style="color:#006600;font-style:italic">//          How many times to print myString</span><br>
    <span style="color:#006600;font-style:italic">// separator: String</span><br>
    <span style="color:#006600;font-style:italic">//          What to print out in between myString*</span><br>
    <span style="color:#000066;font-weight:bold">this</span>.<span style="color:#660066">myString</span> <span style="color:#339933">=</span> <span style="color:#3366cc">&quot;placeholder text&quot;</span><span style="color:#339933">;</span><br>
    <span style="color:#000066;font-weight:bold">this</span>.<span style="color:#660066">times</span> <span style="color:#339933">=</span> <span style="color:#cc0000">5</span><span style="color:#339933">;</span><br>
<span style="color:#009900">}</span></div></div>
<div style="overflow:auto;white-space:nowrap;border:1px solid #9f9f9f;width:435px"><div style="padding:5px;font:normal 12px/1.4em Monaco,Lucida Console,monospace;white-space:nowrap">foo.<span style="color:#660066">prototype</span>.<span style="color:#660066">setString</span> <span style="color:#339933">=</span> <span style="color:#003366;font-weight:bold">function</span> <span style="color:#009900">(</span>myString<span style="color:#009900">)</span> <span style="color:#009900">{</span><br>
    <span style="color:#000066;font-weight:bold">this</span>.<span style="color:#660066">myString</span> <span style="color:#339933">=</span> myString<span style="color:#339933">;</span><br>
<span style="color:#009900">}</span></div></div>
<div style="overflow:auto;white-space:nowrap;border:1px solid #9f9f9f;width:435px"><div style="padding:5px;font:normal 12px/1.4em Monaco,Lucida Console,monospace;white-space:nowrap">foo.<span style="color:#660066">prototype</span>.<span style="color:#660066">toString</span> <span style="color:#339933">=</span> <span style="color:#003366;font-weight:bold">function</span><span style="color:#009900">(</span><span style="color:#009900">)</span> <span style="color:#009900">{</span><br>
    <span style="color:#000066;font-weight:bold">for</span><span style="color:#009900">(</span>int i <span style="color:#339933">=</span> <span style="color:#cc0000">0</span><span style="color:#339933">;</span> i</div></div></li></li>