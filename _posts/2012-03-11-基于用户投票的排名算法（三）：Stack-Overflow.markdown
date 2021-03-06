---
layout: post
title:  "基于用户投票的排名算法（三）：Stack Overflow"
date:   2012-03-11 14:07:05
author: 
categories: program
---

## 基于用户投票的排名算法（三）：Stack Overflow
### by 
### at 2012-03-11 14:07:05
### original <http://www.ruanyifeng.com/blog/2012/03/ranking_algorithm_stack_overflow.html>

<p>上一篇文章，我介绍了<a href="http://www.ruanyifeng.com/blog/2012/03/ranking_algorithm_reddit.html">Reddit</a>的排名算法。</p><p>它的特点是，用户可以投赞成票，也可以投反对票。也就是说，除了时间因素以外，只要考虑两个变量就够了。</p>

<p>但是，还有一些特定用途的网站，必须考虑更多的因素。世界排名第一的程序员问答社区<a href="http://stackoverflow.com/?tab=hot">Stack Overflow</a>，就是这样一个网站。</p>

<p><a href="http://stackoverflow.com/?tab=hot"><img src="http://image.beekka.com/blog/201203/bg2012031101.jpg"></a></p>

<p>你在上面提出各种关于编程的问题，等待别人回答。访问者可以对你的问题进行投票（赞成票或反对票），表示这个问题是不是有价值。</p>

<p><img src="http://image.beekka.com/blog/201203/bg2012031102.png"></p>

<p>一旦有人回答了你的问题，其他人也可以对这个回答投票（赞成票或反对票）。</p>

<p><img src="http://image.beekka.com/blog/201203/bg2012031103.png"></p>

<p>排名算法的作用是，找出某段时间内的热点问题，即哪些问题最被关注、得到了最多的讨论。</p>

<p>在Stack Overflow的页面上，每个问题前面有三个数字，分别表示问题的得分、回答的数目和该问题的浏览次数。以这些变量为基础，就可以设计算法了。</p>

<p><img src="http://image.beekka.com/blog/201203/bg2012031104.png"></p>

<p>创始人之一的Jeff Atwood，曾经在几年前，<a href="http://meta.stackoverflow.com/questions/11602/what-formula-should-be-used-to-determine-hot-questions">公布</a>过排名得分的计算公式。</p>

<p><img src="http://chart.googleapis.com/chart?cht=tx&amp;chl=%5Cfrac%7B(log_%7B10%7DQviews)%5Ctimes%204%2B%5Cfrac%7BQanswers%5Ctimes%20Qscore%7D%7B5%7D%2Bsum(Ascores)%7D%7B((Qage%2B1)-(%5Cfrac%7BQage-Qupdated%7D%7B2%7D))%5E%7B1.5%7D%7D&amp;chs=120" style="border:none"></p>

<p>写成<a href="http://pastebin.com/FWJRbf1N">php代码</a>，就是下面这样：</p>

<p><a href="http://pastebin.com/FWJRbf1N"><img src="http://image.beekka.com/blog/201203/bg2012031105.png"></a></p>

<p>各个算法变量的含义如下：</p>

<p><strong>（1）Qviews（问题的浏览次数）</strong></p>

<p>　　<img src="http://chart.googleapis.com/chart?cht=tx&amp;chl=(log_%7B10%7DQviews)%5Ctimes%204&amp;chs=40" style="border:none"></p>

<p>某个问题的浏览次数越多，就代表越受关注，得分也就越高。这里使用了以10为底的对数，用意是当访问量越来越大，它对得分的影响将不断变小。</p>

<p><strong>（2）Qscore（问题得分）和Qanswers（回答的数量）</strong></p>

<p>　　<img src="http://chart.googleapis.com/chart?cht=tx&amp;chl=Qscore%20%5Ctimes%20%5Cfrac%7BQanswers%7D%7B5%7D&amp;chs=60" style="border:none"></p>

<p>首先，Qscore（问题得分）= 赞成票-反对票。如果某个问题越受到好评，排名自然应该越靠前。</p>

<p>Qanswers表示回答的数量，代表有多少人参与这个问题。这个值越大，得分将成倍放大。这里需要注意的是，如果无人回答，Qanswers就等于0，这时Qscore再高也没用，意味着再好的问题，也必须有人回答，否则进不了热点问题排行榜。</p>

<p><strong>（3）Ascores（回答得分）</strong></p>

<p>　　<img src="http://chart.googleapis.com/chart?cht=tx&amp;chl=sum(Ascores)&amp;chs=40" style="border:none"></p>

<p>一般来说，"回答"比"问题"更有意义。这一项的得分越高，就代表回答的质量越高。</p>

<p>但是我感觉，简单加总的设计还不够全面。这里有两个问题。首先，一个正确的回答胜过一百个无用的回答，但是，简单加总会导致，1个得分为100的回答与100个得分为1的回答，总得分相同。其次，由于得分会出现负值，因此那些特别差的回答，会拉低正确回答的得分。</p>

<p><strong>（4）Qage（距离问题发表的时间）和Qupdated（距离最后一个回答的时间）</strong></p>

<p>　　<img src="http://chart.googleapis.com/chart?cht=tx&amp;chl=((Qage%2B1)-(%5Cfrac%7BQage-Qupdated%7D%7B2%7D))%5E%7B1.5%7D&amp;chs=60" style="border:none"></p>

<p>改写一下，可以看得更清楚：</p>

<p>　　<img src="http://chart.googleapis.com/chart?cht=tx&amp;chl=(%5Cfrac%7BQage%7D%7B2%7D%2B%5Cfrac%7BQupdated%7D%7B2%7D%2B1)%5E%7B1.5%7D&amp;chs=60" style="border:none"></p>

<p>Qage和Qupdated的单位都是秒。如果一个问题的存在时间越久，或者距离上一次回答的时间越久，Qage和Qupdated的值就相应增大。</p>

<p>也就是说，随着时间流逝，这两个值都会越变越大，导致分母增大，因此总得分会越来越小。</p>

<p><strong>（５）总结</strong></p>

<p>Stack Overflow热点问题的排名，与<strong>参与度</strong>（Qviews和Qanswers）和<strong>质量</strong>（Qscore和Ascores）成正比，与<strong>时间</strong>（Qage和Qupdated）成反比。</p>

<p>（完）</p><div style="color:#556677;line-height:160%;padding:0.3em 0.5em;border:1px solid #d3d3d3;margin:1em;background-color:#aad2f0;border-radius:10px"><h3>文档信息</h3>
<ul>
<li>版权声明：自由转载-非商用-非衍生-保持署名 | <a href="http://creativecommons.org/licenses/by-nc-nd/3.0/deed.zh">Creative Commons BY-NC-ND 3.0</a></li>
<li>原文网址：<a href="http://www.ruanyifeng.com/blog/2012/03/ranking_algorithm_stack_overflow.html">http://www.ruanyifeng.com/blog/2012/03/ranking_algorithm_stack_overflow.html</a></li>
<li>最后修改时间：2012年4月17日 11:37</li>
<li>付费支持：<a href="https://mai.alipay.com/p.htm?id=2012033102826278"><img src="http://www.ruanyifeng.com/blog/images/rmb_32.png" alt="支付宝担保交易" style="border:none;vertical-align:middle"></a> | <a href="https://www.paypal.com/cgi-bin/webscr?cmd=_xclick&amp;business=yifeng.ruan@gmail.com&amp;currency_code=USD&amp;amount=2.99&amp;return=http://www.ruanyifeng.com/thank.html&amp;item_name=Ruan%20YiFeng&#39;s%20Blog&amp;undefined_quantity=1&amp;no_note=0"><img src="http://www.ruanyifeng.com/blog/images/dollar_32.png" alt="paypal" style="border:none;vertical-align:middle"></a> </li>
</ul></div><div style="color:#556677;line-height:160%;padding:0.3em 0.5em;margin:1em;border-radius:10px"><p><a href="http://yipinzao.com/" style="border:none"><img src="http://www.ruanyifeng.com/blog/images/ad_yipinzao.jpg" style="border:none"></a>
</p></div>