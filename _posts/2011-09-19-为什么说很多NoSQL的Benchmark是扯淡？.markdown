---
layout: post
title:  "为什么说很多NoSQL的Benchmark是扯淡？"
date:   2011-09-19 21:57:16
author: nosqlfan
categories: program
---

## 为什么说很多NoSQL的Benchmark是扯淡？
### by nosqlfan
### at 2011-09-19 21:57:16
### original <http://item.feedsky.com/~feedsky/nosqlfan/~8149226/558493316/6253001/1/item.html>

<p>正如原作者所言，本文有标题党之嫌，但确实道出了一个众所周知的问题。就是很多NoSQL产品的官方 <span><a href="http://blog.nosqlfan.com/tags/benchmark" title="查看 benchmark 的全部文章">benchmark</a></span> 过高。虽然本人并不完全同意作者的观点，但是其不盲从轻信较劲的态度还是值得学习。</p>
<blockquote><p>抱歉我用了这么一个标题党的题目做为标题。</p>
<p>写这篇文章只是想引起大家的注意：在选择NoSQL产品时，达到标称<span><a href="http://blog.nosqlfan.com/tags/%e6%80%a7%e8%83%bd" title="查看 性能 的全部文章">性能</a></span>，需要诸多限制条件，例如本文主要讨论的磁盘I/O。</p>
<p>现在NoSQL的产品已经很多了，很多都宣称“我们的QPS可以达到十万，甚至百万”，但是当我们在生产环境中使用的时候，却明显的感觉到，随着数据文件不断增大，NoSQL的性能却指数下降，问题处在哪里了？</p>
<p>这些NOSQL的Benchmark的量都有一个前提<span style="color:red">“你得内存足够放下你的全部数据文件”</span></p>
<p>Case1：有人说，我内存16GB，那只能说明你得数据规模还不够大……我已经经历被无数上百GB的数据库折磨过了。此外，你可能需要在1GB内存的虚拟机上支撑数10GB的数据，比如我现在的情况。</p>
<p>继续讨论，一旦内存放不下全部的数据，会怎么办呢？<br>
有很多策略，但无非都是访问磁盘，将数据Cache到内存中。</p>
<p>我们先讨论最坏的情况，假定每条记录的偏移是放在内存中，但所有数据都放在磁盘，我们使用fseek等操作来查询磁盘。</p>
<p>来看下面的测试代码。</p>
<pre>#相关代码, [四号程序员] http://www.coder4.com
void test_fseek_set()
{
    long offset;
    FILE*fp = NULL;
    long i;

    fp = fopen(FILE_NAME, &quot;r&quot;);
    if(!fp)
    {
        printf(&quot;Open file fail.\n&quot;);
        printf(&quot;%s\n&quot;,strerror(errno));
        exit(-1);
    }   

    for(i=0; i&lt;TIMES; i++)
    {
        //Because random max is 1&lt;&lt;30 - 1
        offset = random() * 10 % MAX_FILE;
        if(fseek(fp, offset, SEEK_SET))
        {
            printf(&quot;fseek error.\n&quot;);
            printf(&quot;%ld&quot;,offset);
            printf(&quot;%s\n&quot;,strerror(errno));
        }
    }   

    fclose(fp);
}</pre>
<p>好了，你猜猜上述随机fseek的程序在一个7200转的硬盘上，针对一个4GB的文件随机访问，能跑多块？</p>
<p>答案是QPS&lt;=80。</p>
<p>有人说你骗人，我跑的能到1XXX，那么请你执行下述命令清空你内存中的磁盘缓存。</p>
<pre>#相关代码, [四号程序员] http://www.coder4.com
sync; echo 3 &gt; /proc/sys/vm/drop_caches</pre>
<p>很多时候，之所以我们能在小数据时达到NoSQL官方标称的QPS，而大数据量却指数下降，都是这些缓存在作怪。说白了，<span style="color:red">我们很Happy的Benchmark半天，实际是在玩系统的缓存</span>，当然快了。</p>
<p>一旦你的数据文件大于内存磁盘缓存，那么速度会马上像我列举的这样，不会多余80QPS，在一个4GB的文件上。</p>
<p>有人说mmap，我曾经也是这样YY的，但根据我的测试，事情不是这样。<br>
我有一个120GB的Tokyo Cabinet数据文件，把内存开满，它默认会用mmap，然后你会发现top中“VIRT”一列，会显示为120GB+（换算后），而我得机器内存却只有32GB。这时，当你访问恰好不在内存中的那部分数据时，操作系统会进行非常耗时的换入换出操作(首先就需要fseek等)。在这台24核、32GB的机器上，QPS勉强能达到3000（这已经远远低于标称的QPS）,而一旦清空缓存，QPS会迅速跌落到70左右……</p>
<p>可能还会有人说：我没事闲的为啥要自己清空缓存？</p>
<p>机器不是给你一个NoSQL进程服务的，很多系统其他服务都需要访问磁盘，读取文件，渐渐的就会把你Cache起来的内存全部换掉，根据实际测试的情况，一台完全闲置的机器，开TT能达到3000, 闲置放置48小时（不开其他服务）， 性能就会骤降到1000左右，再放置72小时左右，就回归到70的qps了，此时Cache已经基本完全换出。</p>
<p>综上，mmap不是神，因为你的内存不够，而其他进程也会争夺内存来做自己的Cache。</p>
<p>如果你想充分发挥NoSQL的性能，建议用支持集群的NoSQL产品，尽量将全部数据放入内存中。</p>
<p>或者你没钱购置很多Moster内存的服务器，像我一样，就不要期望NoSQL能有很惊人的性能了。此时，NoSQL所能带来的提升，只是关系数据库所剪掉的那部分开销，如果你基本没有什么join，那么可能还会不如关系数据库。</p>
<p>分析性能，我们不能仅仅看官方的数据比较，要考虑机器的实际情况和自己的数据规模，最终才能分析出瓶颈出在哪里。</p></blockquote>
<p>对于原作者的观点，本人提出两点看法：</p>
<ul>
<li>1.要充分发挥NoSQL性能，并不是一定要尽量把所有数据放到内存，实际上只要保证了热数据都能装在内存中就够了。（这里的热数据，包含了索引数据及系统开销）</li>
<li>2.作者举例中的程序，主要用了磁盘seek，磁盘的seek速度慢，原本就是磁盘物理结构的硬伤，所以许多NoSQL存储采用了变随机写为顺序写的方式，减少磁盘seek操作，也是提升IO性能的良方。</li>
</ul>
<p>来源：<a href="http://www.coder4.com/archives/2171">www.coder4.com</a></p>
<table cellspacing="0" cellpadding="2" border="0" width="100%" style="clear:both">
    
    <tr>
        <td><b><font size="-1" style="display:block!important;padding:20px 0 5px!important">相关文章：</font></b></td>
    </tr>
    
            <tr>
                <td style="margin:0!important;padding:0!important;line-height:20px!important">
                    <img border="0" src="http://static.wumii.com/images/widget/widget_solidPoint.gif">
                    <a style="text-decoration:none!important" href="http://app.wumii.com/ext/redirect.htm?url=http%3A%2F%2Fblog.nosqlfan.com%2Fhtml%2F538.html&amp;from=http%3A%2F%2Fblog.nosqlfan.com%2Fhtml%2F3086.html">
                        <font size="-1" color="#333333" style="line-height:1.65em;font-size:14px!important">Redis并发性能测试benchmark</font>
                    </a>
                </td>
            </tr>
            <tr>
                <td style="margin:0!important;padding:0!important;line-height:20px!important">
                    <img border="0" src="http://static.wumii.com/images/widget/widget_solidPoint.gif">
                    <a style="text-decoration:none!important" href="http://app.wumii.com/ext/redirect.htm?url=http%3A%2F%2Fblog.nosqlfan.com%2Fhtml%2F2819.html&amp;from=http%3A%2F%2Fblog.nosqlfan.com%2Fhtml%2F3086.html">
                        <font size="-1" color="#333333" style="line-height:1.65em;font-size:14px!important">LevelDB、TreeDB、SQLite3性能对比测试</font>
                    </a>
                </td>
            </tr>
            <tr>
                <td style="margin:0!important;padding:0!important;line-height:20px!important">
                    <img border="0" src="http://static.wumii.com/images/widget/widget_solidPoint.gif">
                    <a style="text-decoration:none!important" href="http://app.wumii.com/ext/redirect.htm?url=http%3A%2F%2Fblog.nosqlfan.com%2Fhtml%2F1329.html&amp;from=http%3A%2F%2Fblog.nosqlfan.com%2Fhtml%2F3086.html">
                        <font size="-1" color="#333333" style="line-height:1.65em;font-size:14px!important">MongoDB、HandlerSocket和MySQL性能测试及其结果分析</font>
                    </a>
                </td>
            </tr>
            <tr>
                <td style="margin:0!important;padding:0!important;line-height:20px!important">
                    <img border="0" src="http://static.wumii.com/images/widget/widget_solidPoint.gif">
                    <a style="text-decoration:none!important" href="http://app.wumii.com/ext/redirect.htm?url=http%3A%2F%2Fblog.nosqlfan.com%2Fhtml%2F2305.html&amp;from=http%3A%2F%2Fblog.nosqlfan.com%2Fhtml%2F3086.html">
                        <font size="-1" color="#333333" style="line-height:1.65em;font-size:14px!important">来自Riak的LevelDB与InnoDB的性能测试</font>
                    </a>
                </td>
            </tr>
            <tr>
                <td style="margin:0!important;padding:0!important;line-height:20px!important">
                    <img border="0" src="http://static.wumii.com/images/widget/widget_solidPoint.gif">
                    <a style="text-decoration:none!important" href="http://app.wumii.com/ext/redirect.htm?url=http%3A%2F%2Fblog.nosqlfan.com%2Fhtml%2F737.html&amp;from=http%3A%2F%2Fblog.nosqlfan.com%2Fhtml%2F3086.html">
                        <font size="-1" color="#333333" style="line-height:1.65em;font-size:14px!important">关于NoSQL的思考-为什么我们要优化存储的写性能？</font>
                    </a>
                </td>
            </tr>
    
    <tr>
        <td align="right">
            <a style="text-decoration:none!important" href="http://www.wumii.com/widget/relatedItems.htm" title="无觅相关文章插件">
                <font size="-1" color="#bbbbbb" style="display:block!important;font-family:arial!important;padding:5px 0!important;font-size:12px!important;color:#bbb!important">无觅</font>
            </a>
        </td>
    </tr>
</table><img src="http://www1.feedsky.com/t1/558493316/nosqlfan/feedsky/s.gif?r=http://item.feedsky.com/~feedsky/nosqlfan/~8149226/558493316/6253001/1/item.html" border="0" height="0" width="0">