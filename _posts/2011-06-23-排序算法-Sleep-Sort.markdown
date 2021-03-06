---
layout: post
title:  "排序算法 Sleep Sort"
date:   2011-06-23 08:43:18
author: 陈皓
categories: program
---

## 排序算法 Sleep Sort
### by 陈皓
### at 2011-06-23 08:43:18
### original <http://coolshell.cn/articles/4883.html>

<p>排序算法好像是程序员学习编程最多的算法，也可能是算法研究者们最喜欢研究的算法了。排序有很多很多的算法，比如，冒泡，插入，选择，堆，快速，归并等等（你可以看看本站以前的那些文章：<a title="可视化的排序过程" href="http://coolshell.cn/articles/3933.html">可视化的排序</a>，<a title="一个排序算法比较的网站" href="http://coolshell.cn/articles/399.html">排序算法比较</a>，<a title="一个显示排序过程的Python脚本" href="http://coolshell.cn/articles/536.html">显示排序过程的python</a>）这里向大家介绍一个“巨NB”的排序算法——Sleep Sort。</p>
<p>闲言少说，请看下面的代码（用Shell脚本写的）</p>
<pre>#!/bin/bash
function f() {
    sleep &quot;$1&quot;
    echo &quot;$1&quot;
}
while [ -n &quot;$1&quot; ]
do
    f &quot;$1&quot; &amp;
    shift
done
wait</pre>
<p>用法如下：</p>
<p style="padding-left:30px">./sleepsort.bash 5 3 6 3 6 3 1 4 7</p>
<p>相信你可以会去试一下这个脚本，也相你你试完后你一定会说——“<strong>我擦，真TMD排序了！</strong>”，我还是不要解释这段代码了，过多的解释会不如代码那么直接，而且解释会影响你对这个排序算法的NB性。只想说——<strong>这是正二八经的多线程、多进程排序啊</strong>。我们的<a title="可视化的排序过程" href="http://coolshell.cn/articles/3933.html">Bogo排序</a>也黯然失色啊。</p>
<p>下面我们需要对这个算法做一些分析——</p>
<p><span></span>1）让我们来分析一个这这个程序的算法复杂度，太简单了，不就是O(最大数的秒数)，呵呵。所以，如果出现这样的数列将是恶梦的——2 1 4 3 2 1 99999999</p>
<p>2）这个排序好是好，但对于负数或浮点数就有bug了。负数的解决方案是，我们可以这样来：x/2+MaxInt/2（时间可能相当长，不过依然工作）。对于浮点数，看看下面的代码.</p>
<pre>#!/bin/bash
function f() {
  sleep $(echo &quot;($2 - 1) + $1 / 10 ^ $2&quot; | bc -l)
  echo &quot;$1&quot;
}
while [ -n &quot;$1&quot; ]
do
  f &quot;$1&quot; $(echo -n &quot;$1&quot; | wc -c) &amp;
  shift
done
wait</pre>
<p>3）我们来看看各种语言版本的实现吧。<br>
<strong>Java</strong></p>
<pre>public class SleepSort {
    public static void main(String[] args) {
        int[] ints = {1,4,7,3,8,9,2,6,5};
        SortThread[] sortThreads = new SortThread[ints.length];
        for (int i = 0; i &lt; sortThreads.length; i++) {
            sortThreads[i] = new SortThread(ints[i]);
        }
        for (int i = 0; i &lt; sortThreads.length; i++) {
            sortThreads[i].start();
        }
    }
}
class SortThread extends Thread{
    int ms = 0;
    public SortThread(int ms){
        this.ms = ms;
    }
    public void run(){
        try {
            sleep(ms*10+10);
        } catch (InterruptedException e) {
            // TODO Auto-generated catch block
            e.printStackTrace();
        }
        System.out.println(ms);
    }
}</pre>
<p><strong>Javascript</strong></p>
<pre>function sleepsort() {
    for (var i = 0, il = arguments.length; i &lt; il; i++) {
        (function(args, index) {
            setTimeout(function() {
                document.body.innerHTML += args[index] + &#39;, &#39;;
            }, args[index]);
        }(arguments, i));
    }
};
</pre>
<p><strong>Brainfuck </strong>(关于这门语言，请<a title="BT雷人的程序语言（大全）" href="http://coolshell.cn/articles/4458.html">参看这篇文章</a>)</p>
<p><code>,&gt;,&gt;++++++++[&lt;------&lt;------&gt;&gt;-]<br>
&lt;&lt;[&gt;[&gt;+&gt;+&lt;&lt;-]&gt;&gt;[&lt;&lt;+,&gt;,&gt;++++++++[&lt;------&lt;------&gt;&gt;-]<br>
&lt;&lt;[ ----------[++++++++++&gt;----------]++++++++++<br>
&gt;[&gt;+&gt;+&lt;&lt;-]&gt;&gt;[&lt;&lt;+&gt;&gt;-]&lt;&lt;&lt;-]  &gt;&gt;&gt;++++++[&lt;++++++++&gt;-]&lt;.&gt;.&gt;&gt;-]&lt;&lt;&lt;-]<br>
,----------[----------------------.,----------]<br>
,---&lt;&lt;&lt;+&gt;&gt;&gt;-------[----------------------.,----------]<br>
&gt;&gt; ----------[++++++++++&gt;----------]++++++++++<br>
&gt;++++++[&lt;++++++++&gt;-]&lt; ----------[++++++++++&gt;----------]++++++++++<br>
.&gt;. ----------[++++++++++&gt;----------]++++++++++<br>
&gt;++&gt;+&lt;&lt;-]&gt;&gt;[&lt;&lt;+&gt;&gt;-]&lt;&lt;&lt;-]  &gt;&gt;[&gt;[&gt;+&gt;+&lt;&lt;-]&gt;&gt;[&lt;&lt;----------[++++++++++&gt;----------]++++++++++<br>
&gt;++,&gt;,&gt;++++++++[&lt;------&lt;------&gt;&gt;-]<br>
&lt;&lt;</code></p>
<p>（全文完）</p>
<h3>相关文章</h3><ul><li>2011年05月04日 -- <a href="http://coolshell.cn/articles/4671.html" title="可视化的数据结构和算法">可视化的数据结构和算法</a></li><li>2011年04月06日 -- <a href="http://coolshell.cn/articles/4220.html" title="一些有意思的文章和资源">一些有意思的文章和资源</a></li><li>2011年03月18日 -- <a href="http://coolshell.cn/articles/3933.html" title="可视化的排序过程">可视化的排序过程</a></li><li>2011年03月04日 -- <a href="http://coolshell.cn/articles/3903.html" title="一些有意思的贴子和工具">一些有意思的贴子和工具</a></li><li>2011年02月28日 -- <a href="http://coolshell.cn/articles/3738.html" title="打印质数的各种算法">打印质数的各种算法</a></li><li>2010年07月12日 -- <a href="http://coolshell.cn/articles/2583.html" title="一些重要的算法">一些重要的算法</a></li><li>2009年04月15日 -- <a href="http://coolshell.cn/articles/536.html" title="一个显示排序过程的Python脚本">一个显示排序过程的Python脚本</a></li></ul>