---
layout: post
title:  "疑问：为什么要使用href=”javascript:void(0);”？"
date:   2013-01-28 17:27:46
author: 张 鑫旭
categories: program
---

## 疑问：为什么要使用href=”javascript:void(0);”？
### by 张 鑫旭
### at 2013-01-28 17:27:46
### original <http://www.zhangxinxu.com/wordpress/2013/01/why-use-href-javascript-void0/>

<p>by <a href="http://www.zhangxinxu.com/">zhangxinxu</a> from <a href="http://www.zhangxinxu.com/">http://www.zhangxinxu.com</a><br>
本文地址：<a href="http://www.zhangxinxu.com/wordpress/?p=2963">http://www.zhangxinxu.com/wordpress/?p=2963</a></p>
<h3>一、我是一只小白</h3>
<p><img alt="小白" src="http://image.zhangxinxu.com/image/blog/201301/xiaobai.png" title="小白" width="300" height="300"></p>
<blockquote><p>我是一只小白，<br>
心中有所怀揣，<br>
虽然现在步履蹒跚，<br>
但是丝毫不影响我对JS的满心期待。<br>
我是一只小白，<br>
复杂难题自然无奈，<br>
简单疑问也会塞满我的脑袋，<br>
但我依旧努力追寻答案。<br>
我是一只小白，<br>
心知止步就会淘汰，<br>
故或无敌勤奋或无敌慵懒，<br>
我会迎来我的精彩！
</p></blockquote>
<p>哈哈，突然诗兴大发，这打油诗的文笔跟这画风一样犀利<img src="http://mat1.gtimg.com/www/mb/images/face/13.gif">！</p>
<p><span>—————————–我是低调的分隔线—————————–</span></p>
<p>下半年浮躁多了，得好好静心学习，告别小白。果断买了几本书：<br>
<img alt="买的书" src="http://ww2.sinaimg.cn/bmiddle/4b4d632fgw1e1152q1fbnj.jpg" title="买的书" width="440" height="124"></p>
<p>其中，最先看的是《javascript模式》——不少人认为很shi的一本书。<span style="color:#999">……评价观后感等500字省略……</span>当我看到第二章要结束的时候，又一次看到了“<strong>JS中避免使用void</strong>”的观点，于是，勾起了身为小白的我困扰已久的一个问题：“既然JS中<code>void</code>不推荐，那为何新浪微博，淘宝之流的首页JS操作的<code>href</code>都是<code>javascript:void(0);</code>呢？”</p>
<p>截图为证！<br>
<img alt="新浪微博javascript:void(0) 张鑫旭-鑫空间-鑫生活" src="http://image.zhangxinxu.com/image/blog/201301/2013-01-28_162421.png" title="新浪微博javascript:void(0)" width="277" height="232"> <img alt="淘宝网首页javascript:void(0) 张鑫旭-鑫空间-鑫生活" src="http://image.zhangxinxu.com/image/blog/201301/2013-01-28_162659.png" title="淘宝网首页javascript:void(0)" width="313" height="113"></p>
<p>为什么？为什么？都系睇？都系睇？<br>
<img alt="鑫表情 疑问 " src="http://image.zhangxinxu.com/image/emtion/ask.png" title="鑫表情 疑问" width="150" height="150"></p>
<h3>二、小白的疑惑</h3>
<p>百撕不得骑姐啊百撕不得骑姐！</p>
<p>每当看到<code>void</code>，我都想起了N多年前，刚接触程序C语言那会的梦魇，大学唯一挂过科的就是C语言，每次都让我想起那个干巴巴的女讲师。像我这种纯良的好学生，大学之前网吧都没有去过的好同学，也不先具述程序何物有何用，开头就噼里啪啦变量啊，语句啊，XXX的，每天对着黑色的背景的奇怪东西敲a, b, c，那种感觉就像是小时候被大孩子摁在河底呛水喝，云里雾里，水里土里。我想起了小时候看过的一个故事，一个在农村上学的小伙子有幸作为交换生去英国交流学习，住在普通人家里（自然很热情），可是没几天，这位小伙子就被赶走了——原因是：丫的尿尿都不掀马桶盖，家里可是有lady的，太不尊重了！而实际上，这位小伙子第一次见到马桶就是在这户人家，他根本不知道马桶盖是干嘛用的，尿尿的时候要掀起来！</p>
<p>是习惯还是规范，我疑惑了！所谓<code>javascript:void(0)</code>之流的风气我估计就是丫的些C, C++所谓程序背景人带来的，可以追溯到10年之前……</p>
<p>我正好打开着QQ邮箱，于是看了下其<code>href</code>值，为<code>javascript:;</code>：<br>
<img alt="QQ邮箱中href值截图 张鑫旭-鑫空间-鑫生活" src="http://image.zhangxinxu.com/image/blog/201301/2013-01-28_165126.png" title="QQ邮箱中href值截图" width="291" height="102"></p>
<p>由此可以证明，<code>void(0)</code>确实是多余的，没用的，那为何这么多网站都使用这个呢？每次都要多敲7个多余的字符，这是要闹哪样，莫非绩效是根据HTML的代码量来算的！<br>
<img alt="鑫表情 鄙视" src="http://image.zhangxinxu.com/image/emtion/hum.png" title="鑫表情 鄙视" width="150" height="150"></p>
<p>百撕不得骑姐啊百撕不得骑姐！</p>
<p>然后，我还有更进一步的疑问：JavaScript中语句最后的分号是可以缺省的，那为何要使用<code>javascript:;</code>而不是<code>javascript:</code>呢？</p>
<p>是习惯还是规范，我疑惑了！</p>
<p>具有代码洁癖的coder们，没事多写一个分号，圣洁的精神世界杯玷污了，怎么能忍受的了呢？这又不是多多语句，不写分号可能会出现意想不到的情况！</p>
<p>像我这种懒到手抽筋，多写一个字符都会难受到满地打滚的人，实在是想不过来啊想不过来！</p>
<p>或许只因我是个小白，巴神的世界我不懂。</p>
<h3>三、我是一只小白</h3>
<p><img alt="小白" src="http://image.zhangxinxu.com/image/blog/201301/xiaobai2.png" title="小白" width="300" height="300"></p>
<blockquote><p>我是一只小白，<br>
很多问题偶都不明白，<br>
谁来告诉我答案，<br>
是我太菜，<br>
还是JS的高峰实在难攀，<br>
谁来给我指南，<br>
明明href可以很简单，<br>
为何啰哩吧嗦像个老太太，<br>
我是一个小白，<br>
想不明白想不明白！
</p></blockquote>
<p>哈哈，突然诗兴大发，这打油诗的文笔跟这画风一样犀利<img src="http://mat1.gtimg.com/www/mb/images/face/13.gif">！</p>
<p><span>—————————–我是低调的分隔线—————————–</span></p>
<p>我使用<code>href="javascript:"</code>很多年了，一直都是惴惴不安地使用，因为别人都是<code>href="javascript:void(0);"</code>，是不是因为<code>href="javascript:"</code>有什么潜在的问题？比方说影响性能？或者让人不能识别马桶盖？</p>
<p>求指点，求吐槽！</p>
<p>原创文章，转载请注明来自<a href="http://www.zhangxinxu.com/">张鑫旭-鑫空间-鑫生活</a>[<a href="http://www.zhangxinxu.com/">http://www.zhangxinxu.com</a>]<br>
本文地址：<a href="http://www.zhangxinxu.com/wordpress/?p=2963">http://www.zhangxinxu.com/wordpress/?p=2963</a></p>
<p>（本篇完）</p>
<div>有话要说？点击<a href="http://www.zhangxinxu.com/wordpress/2013/01/why-use-href-javascript-void0/#respond">这里</a>发表评论。</div>