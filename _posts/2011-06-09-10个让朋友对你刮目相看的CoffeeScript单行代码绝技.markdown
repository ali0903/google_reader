---
layout: post
title:  "10个让朋友对你刮目相看的CoffeeScript单行代码绝技"
date:   2011-06-09 00:48:05
author: yuanyi
categories: program
---

## 10个让朋友对你刮目相看的CoffeeScript单行代码绝技
### by yuanyi
### at 2011-06-09 00:48:05
### original <http://heikezhi.com/2011/06/08/10-coffeescript-one-liners-to-impress-your-friends/>

<p><a href="http://heikezhi.com/wp-content/uploads/2011/06/4335060317_8b8eb74b19_m.jpg"><img src="http://heikezhi.com/wp-content/uploads/2011/06/4335060317_8b8eb74b19_m.jpg" alt="" title="4335060317_8b8eb74b19_m" width="193" height="240"></a>或许你已经看过了<a href="http://solog.co/">Marcus Kazmierczak</a>的这篇在HN上颇受欢迎的“<a href="http://solog.co/47/10-scala-one-liners-to-impress-your-friends/">10个让朋友对你刮目相看的Scala单行代码绝技</a>”了，尽管我对Scala并不了解（Java也是），但是这看起来还真不错，于是我也有点手痒，想让我的朋友们也对我刮目相看一小下——不过不是从Java到Scala，我是从Javascript到CoffeeScript，下面的例子都是基于<a href="http://nodejs.org/">node.js</a>环境的。</p>
<p>1. 列表中的每项乘2</p>
<p>Marcus的第一个例子演示了map函数，我们可以使用range语法以及一个匿名函数来完成同样的事情：</p>
<pre name="code">
[1..10].map (i) -&gt; i*2
</pre>
<p>我们还有下面这个更易读的版本：</p>
<pre name="code">
i * 2 for i in [1..10]
</pre>
<p>2. 数列求和</p>
<p>Javascript（以及CoffeeScript扩展）同样有原生的<a href="http://en.wikipedia.org/wiki/Map_%28higher-order_function%29">map</a>以及<a href="http://en.wikipedia.org/wiki/Fold_%28higher-order_function%29">reduce</a>函数：</p>
<pre name="code">
[1..1000].reduce (t, s) -&gt; t + s

(reduce == reduceLeft, 还有reduceRight)
</pre>
<p>3. 检查字符串是否包含某个单词</p>
<p>这实在是再简单不过了，因为我们有some方法，只要数组中的任何元素满足条件它就会返回true：</p>
<pre name="code">
wordList = [&quot;coffeescript&quot;, &quot;eko&quot;, &quot;play framework&quot;, &quot;and stuff&quot;, &quot;falsy&quot;]
tweet = &quot;This is an example tweet talking about javascript and stuff.&quot;

wordList.some (word) -&gt; ~tweet.indexOf word
</pre>
<p>下面的语句会返回匹配到的单词：</p>
<pre name="code">
wordList.filter (word) -&gt; ~tweet.indexOf word
</pre>
<p>～在CoffeeScript中并不是什么特别的操作符，这里我们使用了一个小技巧，它实际上就是<a href="https://developer.mozilla.org/en/JavaScript/Reference/Operators/Bitwise_Operators">按位取反</a>操作符，会对数值按位进行取反操作，在上面的例子里它相当于-x-1，这里我们用它来检查数组的下标是否大于-1，因为-(-1)-1 == 0，会返回false。</p>
<p>4. 读取文件</p>
<p>使用客户端Javascript框架的用户会对下面的代码感到很亲切：</p>
<pre name="code">
fs.readFile &#39;data.txt&#39;, (err, data) -&gt; fileText = data
</pre>
<p>你还可以使用同步版本：</p>
<pre name="code">
fileText = fs.readFileSync('data.txt').toString()
</pre>
<p>不过在node.js中，你只有在程序启动时才可以使用同步版本，其它时间你都应该使用异步版本。</p>
<p>5. 生日快乐</p>
<p>首先，先来一个Scala版本的映射版，不过我对字符串做了一点篡改：</p>
<pre name="code">
[1..4].map (i) -&gt; console.log &quot;Happy Birthday &quot; + (if i is 3 then &quot;dear Robert&quot; else &quot;to You&quot;)
</pre>
<p>下面再来一个优化版，这个读起来更像伪代码了：</p>
<pre name="code">
console.log "Happy Birthday #{if i is 3 then "dear Robert" else "to You"}" for i in [1..4]
</pre>
<p>6. 过滤数列</p>
<p>将一个数字序列过滤为两种类型，这已经很接近了：</p>
<pre name="code">
passed = []
failed = []
(if score &gt; 60 then passed else failed).push score for score in [49, 58, 76, 82, 88, 90]
</pre>
<p>(也可以使用filter，但是那样就不是一行了。。。）</p>
<p>7. 读取并解析一个XML Web service</p>
<p>XML是个神马东东？从没听过，让我们把它换成json，你可以使用<a href="http://github.com/mikeal/request">request</a>库：</p>
<pre name="code">
request.get { uri:&#39;path/to/api.json&#39;, json: true }, (err, r, body) -&gt; results = body
</pre>
<p>8. 找到一个数列的最小（最大）值</p>
<p>我们有非常棒的apply函数，它可以让你通过一个数组来调用拥有可变参数的函数：Math.max以及Math.min，这两个函数都接受可变长度参数，比如Math.max 30, 10, 20返回30，让我们试试下面的代码：</p>
<pre name="code">
Math.max.apply @, [14, 35, -7, 46, 98] # 98
Math.min.apply @, [14, 35, -7, 46, 98] # -7
</pre>
<p>9. 并行处理</p>
<p>这个还不行，你可以创建<a href="http://nodejs.org/docs/v0.4.8/api/child_processes.html">子进程</a>并自己和它们进行通讯，或者使用<a href="https://github.com/pgriess/node-webworker">WebWorkers API实现</a>，让我们跳过这个。</p>
<p>10. 埃拉托斯特尼筛法</p>
<p>（译者注：埃拉托斯特尼筛法是古希腊数学家埃拉托斯特尼所提出的一种简单的判定素数的算法，详细介绍请参见<a href="http://zh.wikipedia.org/wiki/%E5%9F%83%E6%8B%89%E6%89%98%E6%96%AF%E7%89%B9%E5%B0%BC%E7%AD%9B%E6%B3%95">维基百科</a>）</p>
<p>一行可以搞定？</p>
<pre name="code">
sieve = (num) -&gt;
    numbers = [2..num]
    while ((pos = numbers[0]) * pos) &lt;= num
        delete numbers[i] for n, i in numbers by pos
        numbers.shift()
    numbers.indexOf(num) &gt; -1
</pre>
<p>更新 (06/05): <a href="http://twitter.com/dionyziz">@dionyziz</a> 发给了我这个更简洁的版本:</p>
<pre name="code">
primes = []
primes.push i for i in [2..100] when not (j for j in primes when i % j == 0).length
</pre>
<p>现在我们可以像原始版一样用一行来完成判定了：</p>
<pre name="code">
(n) -&gt; (p.push i for i in [2..n] when not (j for j in (p or p=[]) when i%j == 0)[0]) and n in p
</pre>
<p>或者是下面这样：</p>
<pre name="code">
(n) -&gt; (p.push i for i in [2..n] when !(p or p=[]).some((j) -&gt; i%j is 0)) and n in p
</pre>
<p>11. 奖励</p>
<p>最后，再奖励你们一个你肯定没见过的最易读的<a href="http://en.wikipedia.org/wiki/Bizz_buzz">fizzbuzz</a>（数3，数5游戏）版本：</p>
<pre name="code">
"#{if i%3 is 0 then 'fizz' else ''}#{if i%5 is 0 then 'buzz' else ''}" or i for i in [1..100]
</pre>
<p>更新：基于satyr的提示，这里是一个更简单，也更取巧的版本：</p>
<pre name="code">
['fizz' unless i%3] + ['buzz' unless i%5] or i for i in [1..100]
</pre>
<p>如果你对数组使用+操作符，它会变成一个字符串，当数组中包含undefined或是null时，[].toString()和[].join(‘,’)是一个效果，这在Javascript中也同样有效（[undefined] + “b” === “b”）</p>
<p>结论</p>
<p>我很吃惊这些例子中的一些语法居然和Scala如此接近，而我原以为它应该是属于另外一个星球的编程语言。</p>
<p>你可以在这里了解更多<a href="http://jashkenas.github.com/coffee-script/">关于CoffeeScript的知识</a>，以及<a href="http://rosettacode.org/wiki/Category:CoffeeScript">代码片段</a>，也欢迎在Twitter上Follow <a href="http://twitter.com/ricardobeat">@ricardobeat</a>.</p>
<p>———-<br>
本文翻译自：”<a href="http://ricardo.cc/2011/06/02/10-CoffeeScript-One-Liners-to-Impress-Your-Friends.html">10 CoffeeScript One Liners to Impress Your Friends</a>“， 作者：<a href="http://ricardo.cc/">Ricardo Tomasi</a>，照片：<a href="http://www.flickr.com/photos/62337512@N00/">apdk</a></p>
<p>想和我们一道传播黑客精神？<a href="http://heikezhi.com/join">快来加入吧！</a></p>
<table cellspacing="0" cellpadding="3" border="0" style="clear:both">
    
    <tr>
        <td colspan="4"><b><font size="-1" style="display:block!important;padding:20px 0 5px!important">无觅猜您也喜欢：</font></b></td>
    </tr>
    
        <tr>
                <td width="102" valign="top" style="padding:5px!important;margin:0!important">
                    <a title="Rails 3.1 CoffeeScript &amp; SASS初体验" style="text-decoration:none!important" href="http://app.wumii.com/ext/redirect.htm?url=http%3A%2F%2Fheikezhi.com%2F2011%2F04%2F19%2Fhow-to-rails-3-1-coffeescript-howto%2F&amp;from=http%3A%2F%2Fheikezhi.com%2F2011%2F06%2F08%2F10-coffeescript-one-liners-to-impress-your-friends%2F">
                        <img style="margin:0!important;padding:2px!important;border:1px solid #dddddd!important;width:96px!important;height:96px!important" src="http://static.wumii.com/images/blogWidget/wordpress_default.gif" width="96px" height="96px"><br>
                        <font size="-1" color="#333333" style="display:block!important;line-height:15px!important;width:102px!important;font:12px/15px arial!important;height:60px!important;margin:3px 0 0 0!important;padding:0!important;overflow:hidden!important">Rails 3.1 CoffeeScript &amp; SASS初体验</font>
                    </a>
                </td>
                <td width="102" valign="top" style="padding:5px!important;margin:0!important;border-left:1px solid #dddddd!important">
                    <a title="代码中最重要的并不是代码" style="text-decoration:none!important" href="http://app.wumii.com/ext/redirect.htm?url=http%3A%2F%2Fheikezhi.com%2F2011%2F06%2F09%2Fthe-most-important-code-isnt-code%2F&amp;from=http%3A%2F%2Fheikezhi.com%2F2011%2F06%2F08%2F10-coffeescript-one-liners-to-impress-your-friends%2F">
                        <img style="margin:0!important;padding:2px!important;border:1px solid #dddddd!important;width:96px!important;height:96px!important" src="http://static.wumii.com/site_images/2011/06/10/11657980.png" width="96px" height="96px"><br>
                        <font size="-1" color="#333333" style="display:block!important;line-height:15px!important;width:102px!important;font:12px/15px arial!important;height:60px!important;margin:3px 0 0 0!important;padding:0!important;overflow:hidden!important">代码中最重要的并不是代码</font>
                    </a>
                </td>
                <td width="102" valign="top" style="padding:5px!important;margin:0!important;border-left:1px solid #dddddd!important">
                    <a title="GNU/Linux中有多少GNU的代码？" style="text-decoration:none!important" href="http://app.wumii.com/ext/redirect.htm?url=http%3A%2F%2Fheikezhi.com%2F2011%2F06%2F02%2Fgnulinux%25E4%25B8%25AD%25E6%259C%2589%25E5%25A4%259A%25E5%25B0%2591gnu%25E7%259A%2584%25E4%25BB%25A3%25E7%25A0%2581%25EF%25BC%259F%2F&amp;from=http%3A%2F%2Fheikezhi.com%2F2011%2F06%2F08%2F10-coffeescript-one-liners-to-impress-your-friends%2F">
                        <img style="margin:0!important;padding:2px!important;border:1px solid #dddddd!important;width:96px!important;height:96px!important" src="http://static.wumii.com/site_images/2011/06/03/10756597.png" width="96px" height="96px"><br>
                        <font size="-1" color="#333333" style="display:block!important;line-height:15px!important;width:102px!important;font:12px/15px arial!important;height:60px!important;margin:3px 0 0 0!important;padding:0!important;overflow:hidden!important">GNU/Linux中有多少GNU的代码？</font>
                    </a>
                </td>
                <td width="102" valign="top" style="padding:5px!important;margin:0!important;border-left:1px solid #dddddd!important">
                    <a title="你不是软件工程师" style="text-decoration:none!important" href="http://app.wumii.com/ext/redirect.htm?url=http%3A%2F%2Fheikezhi.com%2F2011%2F05%2F10%2Fyou-are-not-a-software-engineer%2F&amp;from=http%3A%2F%2Fheikezhi.com%2F2011%2F06%2F08%2F10-coffeescript-one-liners-to-impress-your-friends%2F">
                        <img style="margin:0!important;padding:2px!important;border:1px solid #dddddd!important;width:96px!important;height:96px!important" src="http://static.wumii.com/site_images/2011/05/11/8235757.jpg" width="96px" height="96px"><br>
                        <font size="-1" color="#333333" style="display:block!important;line-height:15px!important;width:102px!important;font:12px/15px arial!important;height:60px!important;margin:3px 0 0 0!important;padding:0!important;overflow:hidden!important">你不是软件工程师</font>
                    </a>
                </td>
        </tr>
    
    <tr>
        <td colspan="4" align="right">
            <a style="text-decoration:none!important" href="http://www.wumii.com/widget/relatedItems.htm" title="无觅相关文章插件">
                <font size="-1" color="#bbbbbb" style="display:block!important;font-family:arial!important;padding:5px 0!important;font-size:12px!important;color:#bbb!important">无觅</font>
            </a>
        </td>
    </tr>
</table><img src="http://www1.feedsky.com/t1/526842565/heikezhi/feedsky/s.gif?r=http://heikezhi.com/2011/06/08/10-coffeescript-one-liners-to-impress-your-friends/" border="0" height="0" width="0">