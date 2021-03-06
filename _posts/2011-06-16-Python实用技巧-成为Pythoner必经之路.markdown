---
layout: post
title:  "Python实用技巧-成为Pythoner必经之路"
date:   2011-06-16 01:30:48
author: 
categories: program
---

## Python实用技巧-成为Pythoner必经之路
### by 
### at 2011-06-16 01:30:48
### original <http://www.iteye.com/topic/1092772>

<span style="font-size:large">前言</span>
<br>    本文主要记录 Python 中一些常用技巧，本文重点描述的是告诉你怎么写才是更好？  如果你并不熟悉Python语法，希望你能在下面代码片段中看到Python的简单、优雅; 如果你象我这样，对 Python 有兴趣或并正在学习，我相信下面的技巧并不会让你失望; 如果你已经是一名 Pythoner ，那么很乐于你分享你的经验和技巧。
<br>
<br><span style="font-size:large">目录</span>
<br><ul>
<li>Python 禅道
</li><li>代码风格: 提高可读性
</li><li>PEP 8: Python 代码风格指南
</li><li>空格(行)使用 (1)
</li><li>空格(行)使用 (2)
</li><li>命名
</li><li>较长代码行
</li><li>较长字符串
</li><li>复合语句
</li><li>字符串文档 &amp; 注释
</li><li>交换变量
</li><li>更多关于 Tuples
</li><li>关于 "_"
</li><li>创建String: 从列表中创建
</li><li>尽可能的使用
</li><li>字典中的 get 函数
</li><li>字典中的 setdefault 函数 (1)
</li><li>字典中的 setdefault 函数 (2)
</li><li>defaultdict
</li><li>创建 &amp; 分割字典
</li><li>判断 True 值
</li><li>True 值
</li><li>索引 &amp; 项 (1)
</li><li>索引 &amp; 项 (2): enumerate
</li><li>默认参数值
</li><li>列表理解
</li><li>生成器表达式 (1)
</li><li>生成器表达式 (2)
</li><li>排序
</li><li>使用 DSU *排序
</li><li>使用 Key 排序
</li><li>生成器
</li><li>生成器示例
</li><li>从文件中读取数据行
</li><li>try/except 示例
</li><li>导入(Importing)
</li><li>模块 &amp; 脚本
</li><li>模块结构
</li><li>命令行处理
</li><li>简单比复杂好
</li><li>不要重新发明轮子
</li></ul>
<br>
<br><span style="font-size:large">章节</span>
<br>
<br><span style="font-size:large"><strong>Python 禅道</strong></span>
<br>这是Python的指导原则，但有不同诠释。
<br>
<br>如果您使用的一种编程语言是以小品喜剧艺术团命名的，你最好有幽默感。<img src="http://www.iteye.com/images/smiles/icon_lol.gif"> 
<br>
<br>美丽优于丑陋。
<br>明确优于含蓄。
<br>简单比复杂好。
<br>平倘优于嵌套。
<br>稀疏比密集更好。
<br>特殊情况不能特殊到打破规则。
<br>错误不应该默默传递。
<br>......
<br>
<br>
<br><span style="font-size:large"><strong>代码风格: 提高可读性</strong></span>
<br><strong>Programs must be written for people to read, and only incidentally for machines to execute.
<br></strong>     —Abelson &amp; Sussman, Structure and Interpretation of Computer Programs
<br>
<br>
<br><span style="font-size:large"><strong> PEP 8: Python 代码风格指南</strong></span>
<br>值得阅读:
<br><a href="http://www.python.org/dev/peps/pep-0008/">http://www.python.org/dev/peps/pep-0008/</a>
<br>
<br>
<br><span style="font-size:large"><strong> 空格(行)使用 (1)</strong></span>
<br><ul>
<li>使用 4 个空格缩进。
</li><li>不要使用制表符。
</li><li>不要将制表符和空格混合使用。
</li><li>IDEL和Emacs的Python的都支持 spaces模式。
</li><li>每个函数之间应该有一个空行。
</li><li>每一个 Class 之间应该有两个空行。
</li></ul>
<br>
<br><span style="font-size:large"><strong> 空格(行)使用 (2)</strong></span>
<br><ul>
<li>在使用 字典(dict), 列表(list), 元组(tuple), 参数(argument)列表时， 应在 "," 前添加一个空格, 并且使用字典(dict)时，在 ":" 号后添加空格，而不是在前面添加。
</li><li>在括号之前或参数之前不添加空格。
</li><li>在文档注释中前后应该没有空格。
</li></ul>
<br><pre name="code">def make_squares(key, value=0):
    """Return a dictionary and a list..."""
    d = {key: value}
    l = [key, value]
    return d, l</pre>
<br>
<br><span style="font-size:large"><strong> 命名</strong></span>
<br><ul>
<li><span style="color:gray">joined_lower</span> 可以是 函数名, 方法名, 属性名
</li><li><span style="color:gray">joined_lower</span> or <span style="color:gray">ALL_CAPS</span> 是常量
</li><li><span style="color:gray">StudlyCaps</span> 类名
</li><li><span style="color:gray">camelCase</span> 只有在预先制定好的命名规范使用
</li><li>属性: <span style="color:gray">interface</span>, <span style="color:gray">_internal</span>, <span style="color:gray">__private</span>
</li><li> 但尽量避免__private形式。下面两个链接解释了 为什么python中没有 private声明？
</li></ul>      <a href="http://stackoverflow.com/questions/70528/why-are-pythons-private-methods-not-actually-private">http://stackoverflow.com/questions/70528/why-are-pythons-private-methods-not-actually-private</a>
<br>      <a href="http://stackoverflow.com/questions/1641219/does-python-have-private-variables-in-classes">http://stackoverflow.com/questions/1641219/does-python-have-private-variables-in-classes</a>
<br>
<br>
<br><span style="font-size:large"><strong> 较长代码行</strong></span>
<br>保持一行代码在 80 个字符长度。
<br>在括号内使用隐含的行延续：
<br><pre name="code"> def __init__(self, first, second, third,
             fourth, fifth, sixth):
    output = (first + second + third
              + fourth + fifth + sixth)
</pre>
<br>或者在需要换行的位置使用 \ 来延续行：
<br><pre name="code">VeryLong.left_hand_side \
    = even_longer.right_hand_side()</pre>另外，使用反斜杠是有风险的，如果你添加一个空格在反斜杠后面，它就出错了。此外，它使代码难看。
<br>
<br>
<br><span style="font-size:large"><strong> 较长字符串</strong></span>
<br>将相邻的字符串进行连接的做法：
<br><pre name="code">&gt;&gt;&gt; print &#39;o&#39; &#39;n&#39; &quot;e&quot;
one</pre>
<br>虽然字符之间的空格不是必需的，但是这样有助于可读性。
<br><pre name="code">&gt;&gt;&gt; print &#39;t&#39; r&#39;\/\/&#39; &quot;&quot;&quot;o&quot;&quot;&quot;
t\/\/o</pre>
<br>用一个 “r“ 开头的字符串是一个“raw“的字符串(类似java中的转义符)。上面的反斜杠就会当成普通字符串处理。他们对正则表达式和Windows文件系统路径非常有用。
<br>注意：使用字符串变量名无法通过以上方式进行连接。
<br><pre name="code">&gt;&gt;&gt; a = &#39;three&#39;
&gt;&gt;&gt; b = &#39;four&#39;
&gt;&gt;&gt; a b
  File &quot;&lt;stdin&gt;&quot;, line 1
    a b
      ^
SyntaxError: invalid syntax</pre>
<br>这是因为自动连接是由Python解析器/编译器来处理的，因为其无法在编译时对变量值进行"翻译"，所以就这种必须在运行时使用“+“运算符来连接变量。
<br>
<br>
<br><span style="font-size:large"><strong> 复合语句</strong></span>
<br>Good：
<br><pre name="code">if foo == 'blah':
    do_something()
do_one()
do_two()
do_three()</pre>
<br>Bad：
<br><pre name="code">if foo == 'blah': do_something()
do_one(); do_two(); do_three()</pre>
<br>
<br><span style="font-size:large"><strong> 文档注释(Docstrings) &amp; 注释</strong></span>
<br>文档注释 = 用于解释如何使用代码
<br>      文档注释公约：<a href="http://www.python.org/dev/peps/pep-0257/">http://www.python.org/dev/peps/pep-0257/</a>
<br>注释 = 为什么 (理由) &amp; 代码如何工作的如：
<br><pre name="code"># !!! BUG: ...
# !!! FIX: This is a hack
# ??? Why is this here?</pre><em><span style="color:gray">注释对于任何语言开发者来说已经最基本的东西了，这里就不详细说了.
<br></span></em>
<br>
<br><span style="font-size:large"><strong> 交换变量</strong></span>
<br>在其它语言的交换变量的做法一般是:
<br><pre name="code">temp = a
a = b
b = temp</pre>
<br>Python的做法：
<br><pre name="code">b, a = a, b</pre>
<br>也许你见到过这样的情况，但是你知道它是如何工作的吗？
<br><ul>
<li>首先，逗号是元组构造语法。
</li><li>等号的右边是定义一个元组 (tuple packing).
</li><li>其左边为一个目标元组 (tuple unpacking)).
</li></ul>右边的元组根据名称被 unpacked 到左边的无组。
<br>更多关于 unpacked例子：
<br><pre name="code">&gt;&gt;&gt; info =[&#39;David&#39;, &#39;Pythonista&#39;, &#39;+1250&#39;]
&gt;&gt;&gt; name, title, phone = info
&gt;&gt;&gt; name
&#39;Davids&#39;
&gt;&gt;&gt; title
&#39;Pythonista&#39;
&gt;&gt;&gt; phone
&#39;+1250&#39;</pre>
<br>在结构化的数据上使用循环：
<br>info 是在上面定义的一个 list . 所以下面的 people 有两个项,  两个项都是分别都拥有三个项的 list.
<br><pre name="code">&gt;&gt;&gt; people = [info, [&#39;Guido&#39;, &#39;BDFL&#39;, &#39;unlisted&#39;]]
&gt;&gt;&gt; for (name, title, phone) in people:
...     print name, phone
...
David +1250
Guido unlisted</pre>
<br>以上循环中，people中的两个项(list item)，都已经被 unpacked 到 (name, title, phone) 无组中。
<br>可以任意嵌套(只要左右两边的结构一定要能够匹配得上):
<br><pre name="code">&gt;&gt;&gt; david, (gname, gtitle, gphone) = people
&gt;&gt;&gt; gname
&#39;Guido&#39;
&gt;&gt;&gt; gtitle
&#39;BDFL&#39;
&gt;&gt;&gt; gphone
&#39;unlisted&#39;
&gt;&gt;&gt; david
[&#39;David&#39;, &#39;Pythonista&#39;, &#39;+1250&#39;]</pre>
<br>
<br><span style="font-size:large"><strong> 更多关于 Tuples</strong></span>
<br>我们看到的是元组通过逗号构造，而不是括号。例如：
<br><pre name="code">&gt;&gt;&gt; 1,
(1,)</pre>
<br>Python的解释器会为你显示括号，所以建议你使用括号：
<br><pre name="code">&gt;&gt;&gt; (1,)
(1,)</pre>
<br>千万不要忘记逗号!
<br><pre name="code">&gt;&gt;&gt; (1)
1</pre>
<br>在只有一个元素的元组，尾随逗号是必须的，在2 + 元素的元组，尾随逗号是可选的。 如果创建一个 0或空元组，一对括号是快捷的语法：
<br><pre name="code">&gt;&gt;&gt; ()
()
&gt;&gt;&gt; tuple()
()</pre>
<br>一个常见的​​错误当你并不想要一个无组，却无意的添加了一个逗号，很容易造成你在代码中的错误，如：
<br><pre name="code">&gt;&gt;&gt; value = 1,
&gt;&gt;&gt; value # is a tuple, not a int
(1,)</pre>
<br>所以，当你发现一个元组时，赶紧去找一下那个,号吧。
<br>
<br>
<br><span style="font-size:large"><strong> 关于 "_"</strong></span>
<br>是一个非常有用的功能，但是却很少有人知道。
<br>当你在交互式模式下(如 IDEL)计算一个表达式或调用一个函数后，其结果必然是一个临时名称，_（下划线）：
<br><pre name="code">&gt;&gt;&gt; 1 + 1
2
&gt;&gt;&gt; _
2</pre>
<br>在 _ 中存储最后输出的值。
<br>当输出的结果是 None 或没有任何输出时，而 _ 的值并不会改变，仍然保存上一次的值。这就是方便所在。
<br>当然，这只能交互式的模式中使用，在模块中不能支持。
<br>这在交互式模式中是非常有用的，当你在过程中没有保存计算结果 或 你想看最后一步的执行的输出结果：
<br><pre name="code">&gt;&gt;&gt; import math
&gt;&gt;&gt; math.pi / 3
1.0471975511965976
&gt;&gt;&gt; angle = _
&gt;&gt;&gt; math.cos(angle)
0.50000000000000011
&gt;&gt;&gt; _
0.50000000000000011</pre>
<br>
<br><span style="font-size:large"><strong> 创建String: 从列表中创建</strong></span>
<br>开始定义一个 string  列表：<pre name="code">colors = ['red', 'blue', 'green', 'yellow']</pre>当我们需要将上面的列表连接成一个字符串。尤其当 list 是一个很大的列表时....
<br>不要这样做：
<br><pre name="code">result = ''
for s in colors:
    result += s</pre>这种方式效率非常低下的，它有可怕的内存使用问题，至于为什么，如果你是 javaer 的话，其中的 string 连接，我想你并不陌生。
<br>相反，你应该这样做：
<br><pre name="code"> result = ''.join(colors)</pre>
<br>当你只有几十或几百个string项连接时，它们效率上并不会太大的差别。但你要在养成写高效代码的习惯，因为当字符串数千时，join 比起 for 连接性能会能有所提升。
<br>
<br>如果你需要使用一个函数来生成一个字符串列表，同样可以使用：<pre name="code">result = ''.join(fn(i) for i in items)</pre>
<br>
<br>
<br><span style="font-size:large"><strong> 尽可能的使用</strong></span>
<br>Good:<pre name="code">for key in d:
    print key</pre>
<br><ul>
<li>使用 in 一般情况下是非常快的。
</li><li>这种方式也适用于其它的容器对象（如 list，tuple 和 set）。
</li><li>in 是操作符(正如上面所看到的)。
</li></ul>
<br>Bad:
<br><pre name="code">for key in d.keys():
    print key
</pre>
<br>保持与上面的一致性，使用 use key in dict 方式，而不是 dict.has_key():
<br><pre name="code"> # do this:
if key in d:
    ...do something with d[key]

# not this:
if d.has_key(key):
    ...do something with d[key]
</pre>
<br>
<br>
<br><span style="font-size:large"><strong>字典中的 get 函数</strong></span>
<br>我们经常需要在字典中初始化数据：
<br>以下是不好的实现方法：
<br><pre name="code">navs = {}
for (portfolio, equity, position) in data:
    if portfolio not in navs:
        navs[portfolio] = 0
    navs[portfolio] += position * prices[equity]</pre>
<br>使用dict.get(key, default) 删除 if 判断代码：
<br><pre name="code">navs = {}
for (portfolio, equity, position) in data:
    navs[portfolio] = (navs.get(portfolio, 0)
                       + position * prices[equity])</pre>这种方式更为直接。
<br>
<br>
<br><span style="font-size:large"><strong>字典中的 setdefault 函数 (1)</strong></span>
<br>当我们要初始化一个可变字典的值。每个字典的值将是一个列表。下面是不好的做法：
<br>初始化可变字典的值：
<br><pre name="code">equities = {}
for (portfolio, equity) in data:
    if portfolio in equities:
        equities[portfolio].append(equity)
    else:
        equities[portfolio] = [equity]</pre>
<br>通过 dict.setdefault(key, default) 使这段代码工作的更好:
<br><pre name="code">equities = {}
for (portfolio, equity) in data:
    equities.setdefault(portfolio, []).append(
                                         equity)</pre>
<br>dict.setdefault（）等同于“ get, or set &amp; get“ 或&quot;如果没有，就设置&quot;;  如果你的字典Key是复杂的计算或long类型，使用 setdefault 是特别有效的。
<br>
<br>
<br><span style="font-size:large"><strong>字典中的 setdefault 函数 (2) </strong></span>
<br>在我们看到的setdefault字典方法也可以作为一个独立的语句使用：
<br><pre name="code">avs = {}
for (portfolio, equity, position) in data:
    navs.setdefault(portfolio, 0)
    navs[portfolio] += position * prices[equity]</pre>
<br>我们在这里忽略了字典的setdefault方法返回的默认值。我们正利用的setdefault中的作用，仅仅只是在dict中没有 key 的值的时候才会设置。
<br>
<br>
<br><span style="font-size:large"><strong>创建 &amp; 分割字典</strong></span>
<br>如果你有两份 list 对象，希望通过这两个对象构建一个 dict 对象。
<br><pre name="code">given = [&#39;John&#39;, &#39;Eric&#39;, &#39;Terry&#39;, &#39;Michael&#39;]
family = [&#39;Cleese&#39;, &#39;Idle&#39;, &#39;Gilliam&#39;, &#39;Palin&#39;]
pythons = dict(zip(given, family))
&gt;&gt;&gt; pprint.pprint(pythons)
{&#39;John&#39;: &#39;Cleese&#39;,
 &#39;Michael&#39;: &#39;Palin&#39;,
 &#39;Eric&#39;: &#39;Idle&#39;,
 &#39;Terry&#39;: &#39;Gilliam&#39;}</pre>
<br>同样，如果希望获取两份列表，也是非常简单：
<br><pre name="code">&gt;&gt;&gt; pythons.keys()
[&#39;John&#39;, &#39;Michael&#39;, &#39;Eric&#39;, &#39;Terry&#39;]
&gt;&gt;&gt; pythons.values()
[&#39;Cleese&#39;, &#39;Palin&#39;, &#39;Idle&#39;, &#39;Gilliam&#39;]</pre>
<br>需要注意的是，上面 list 虽然是有序的，但是 dict 中的  keys 和 values 是无序的，这正是因为 dict 本质就是无序存储的。
<br>
<br>
<br><span style="font-size:large"><strong>索引 &amp; 项 (1)</strong></span>
<br>如果你需要一个列表，这里有一个可爱的方式来节省你的输入：
<br><pre name="code">&gt;&gt;&gt; items = &#39;zero one two three&#39;.split()
&gt;&gt;&gt; print items
[&#39;zero&#39;, &#39;one&#39;, &#39;two&#39;, &#39;three&#39;]</pre>
<br>如果我们需要遍历这个 list ，而且需要 index 和 item：
<br><pre name="code">                  - or -
i = 0
for item in items:      for i in range(len(items)):
    print i, item               print i, items[i]
    i += 1
</pre>
<br>
<br><span style="font-size:large"><strong>索引 &amp; 项 (2): enumerate</strong></span>
<br>通过 enumerate 可以返回 list 中的 (index, item)元组：
<br><pre name="code">&gt;&gt;&gt; print list(enumerate(items))
[(0, &#39;zero&#39;), (1, &#39;one&#39;), (2, &#39;two&#39;), (3, &#39;three&#39;)]</pre>
<br>于是，遍历list获取index 及 item 就更加简单了：
<br><pre name="code">for (index, item) in enumerate(items):
    print index, item</pre>
<br><pre name="code"># compare:              # compare:
index = 0               for i in range(len(items)):
for item in items:              print i, items[i]
    print index, item
    index += 1
</pre>
<br>不难看出，使用 enumerate 比起下面两种方式，更加简单，更加容易阅读，这正是我们想要的。
<br>下面是例子是如何通过 enumerate 返回迭代器：
<br><pre name="code">&gt;&gt;&gt; enumerate(items)
&lt;enumerate object at 0x011EA1C0&gt;
&gt;&gt;&gt; e = enumerate(items)
&gt;&gt;&gt; e.next()
(0, &#39;zero&#39;)
&gt;&gt;&gt; e.next()
(1, &#39;one&#39;)
&gt;&gt;&gt; e.next()
(2, &#39;two&#39;)
&gt;&gt;&gt; e.next()
(3, &#39;three&#39;)
&gt;&gt;&gt; e.next()
Traceback (most recent call last):
  File &quot;&lt;stdin&gt;&quot;, line 1, in ?
StopIteration</pre>
<br>
<br>
<br><span style="font-size:large"><strong>默认参数值</strong></span>
<br>这是对于一个初学者常犯的错误，甚至于一些高级开发人员也会遇到，因为他们并不了解 Python 中的 names.
<br><pre name="code">def bad_append(new_item, a_list=[]):
    a_list.append(new_item)
    return a_list
</pre>
<br>这里的问题是，a_list是一个空列表，默认值是在函数定义时进行初始化。因此，每次调用该函数，你会得到不相同的默认值。尝试了好几次：
<br><pre name="code">&gt;&gt;&gt; print bad_append(&#39;one&#39;)
[&#39;one&#39;]
&gt;&gt;&gt; print bad_append(&#39;two&#39;)
[&#39;one&#39;, &#39;two&#39;]</pre>
<br>列表是可变对象，你可以改变它们的内容。正确的方式是先获得一个默认的列表（或dict，或sets）并在运行时创建它。
<br><pre name="code">def good_append(new_item, a_list=None):
    if a_list is None:
        a_list = []
    a_list.append(new_item)
    return a_list</pre>
<br>
<br>
<br>
<br><span style="font-size:large"><strong>判断 True 值</strong></span>
<br><pre name="code"># do this:        # not this:
if x:             if x == True:
    pass              pass</pre>
<br>它的优势在于效率和优雅。
<br>判断一个list：
<br><pre name="code"># do this:        # not this:
if items:         if len(items) != 0:
    pass              pass

                  # and definitely not this:
                  if items != []:
                      pass</pre>
<br>
<br>
<br><span style="font-size:large"><strong>True 值</strong></span>
<br>True和False是内置的bool类型的布尔值的实例。谁都只有其中的一个实例。
<br><table><tr><td>False</td><td>True</td></tr><tr><td>False (== 0)</td><td>True (== 1)</td></tr><tr><td>&quot;&quot; (empty string)       </td><td>any string but "" (" ", "anything")</td></tr><tr><td>0, 0.0      </td><td>any number but 0 (1, 0.1, -1, 3.14)</td></tr><tr><td>[], (), {}, set()      </td><td>any non-empty container ([0], (None,), [''])</td></tr><tr><td>None      </td><td>almost any object that's not explicitly False</td></tr></table>
<br>
<br><span style="font-size:large"><strong>简单比复杂好</strong></span>
<br><strong>Debugging is twice as hard as writing the code in the first place. Therefore, if you write the code as cleverly as possible, you are, by definition, not smart enough to debug it.
<br></strong>     —Brian W. Kernighan
<br>
<br>
<br>
<br><span style="font-size:large"><strong>不要重新发明轮子</strong></span>
<br>在写任何代码之前,
<br> ➔ ➔ ➔ ➔
<br><ul>
<li><a href="http://docs.python.org/library/index.html">检查python 标准库</a>.
</li><li>检查Python的包索引 (the "Cheese Shop"):
</li>           <a href="http://cheeseshop.python.org/pypi">http://cheeseshop.python.org/pypi</a>
<li>Search the web. Google is your friend.
</li></ul>
<br>原文：[urlhttp://python.net/~goodger/projects/pycon/2007/idiomatic/handout.html][url]
<br>
          
          <br><br>
          作者: <a href="http://denger.iteye.com">denger</a> 
          <br>
          声明: 本文系ITeye网站发布的原创文章，未经作者书面许可，严禁任何网站转载本文，否则必将追究法律责任！
          <br><br>
          <span style="color:red">
            <a href="http://www.iteye.com/topic/1092772" style="color:red">已有 <strong>0</strong> 人发表回复，猛击-&gt;&gt;<strong>这里</strong>&lt;&lt;-参与讨论</a>
          </span>
          <br><br><br>
<span style="color:#e28822">ITeye推荐</span>
<br>
<ul><li><a href="http://www.iteye.com/clicks/433"><span style="color:red;font-weight:bold">—软件人才免语言低担保 赴美带薪读研！— </span></a></li></ul>
<br><br><br>