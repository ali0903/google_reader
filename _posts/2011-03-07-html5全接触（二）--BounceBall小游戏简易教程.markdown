---
layout: post
title:  "html5全接触（二）--BounceBall小游戏简易教程"
date:   2011-03-07 18:49:00
author: 岑安
categories: program
---

## html5全接触（二）--BounceBall小游戏简易教程
### by 岑安
### at 2011-03-07 18:49:00
### original <http://www.cnblogs.com/hongru/archive/2011/03/07/1973792.html>

<p><p><span style="white-space:pre">	</span>最近一段时间都比较忙，好久没更新博客了，遵循着“时间就像那啥，挤挤总会有的”的原则，承接着上一篇html5先关的博文，继续我们的趣味html5之旅。</p>
<p><span style="white-space:pre">	</span>前一段时间很流行用html5写小游戏，当了解了一些常用的api之后，你会发现，写一些简单的小游戏自娱自乐也不会那么困难，当然，做逻辑和界面复杂的游戏除外。以下会提供一个弹球小游戏的简单教程，希望感兴趣的朋友能在编码中找到一点乐趣。</p>
<p>&lt;!-- <span style="color:red">注：以下demo木有神马高深的东东，大牛们觉得无味请略过。同时，由于砖块厚度与弹球的纵向变换单元的比例不协调，故没做砖块的侧向碰撞监测..</span> --&gt; </p>
&lt;!DOCTYPE html&gt;
&lt;html&gt;
&lt;head&gt;
&lt;style&gt;
body {margin:0; position:absolute; width:100%; height:100%}
canvas {display: block; margin: 20px auto; border: 2px solid #333}
.info {width: 600px; margin: 0 auto; color: #666; text-align:center}
&lt;/style&gt;

&lt;/head&gt;
&lt;body&gt;
&lt;canvas id=&quot;canvas&quot; width=&quot;600&quot; height=&quot;400&quot;&gt;Your Broswer don&#39;t support html5 canvas&lt;/canvas&gt;
&lt;p class=&quot;info&quot;&gt;空格-开始/暂停 | 方向键控制挡板左右&lt;/p&gt;

&lt;/body&gt;
&lt;/html&gt;

<p> </p>
<p>既然是教程，咱们还是一步一步来：（代码可以直接在textarea里看到，源码就不贴了）<br>
【step 1】画个小球<br>
&lt;!DOCTYPE html&gt;
&lt;html&gt;
&lt;head&gt;
&lt;style&gt;
body {margin:0; position:absolute; width:100%; height:100%}
canvas {display: block; margin: 20px auto; border: 2px solid #333}
&lt;/style&gt;
  &lt;/head&gt;
&lt;body&gt;
&lt;canvas id=&quot;canvas&quot; width=&quot;600&quot; height=&quot;400&quot;&gt;Your Broswer don&#39;t support html5 canvas&lt;canvas&gt;
&lt;/body&gt;
&lt;/html&gt;

<br>

</p>
<p>【step 2】为了方便扩展以及养成良好的编码习惯，我们稍微做点封装和结构化；同时增加画矩形的方法rect和清除画布的方法clear<br>
&lt;!DOCTYPE html&gt;
&lt;html&gt;
&lt;head&gt;
&lt;style&gt;
body {margin:0; position:absolute; width:100%; height:100%}
canvas {display: block; margin: 20px auto; border: 2px solid #333}
&lt;/style&gt;
  &lt;/head&gt;
&lt;body&gt;
&lt;canvas id=&quot;canvas&quot; width=&quot;600&quot; height=&quot;400&quot;&gt;Your Broswer don&#39;t support html5 canvas&lt;canvas&gt;
&lt;/body&gt;
&lt;/html&gt;

<br>

</p>
<p>【step 3】让小球动起来，因为canvas是画布，想让小球动起来，最直接的想法莫过于通过定时器setInterval之类，每次先清除画布，然后重绘一个小球，定位到路径的下一点即可，视觉上连贯起来就动起来了。咱们还是上代码（我这个是低配版，所以每次的位置变化量我都写死的）<br>
&lt;!DOCTYPE html&gt;
&lt;html&gt;
&lt;head&gt;
&lt;style&gt;
body {margin:0; position:absolute; width:100%; height:100%}
canvas {display: block; margin: 20px auto; border: 2px solid #333}
&lt;/style&gt;
  &lt;/head&gt;
&lt;body&gt;
&lt;canvas id=&quot;canvas&quot; width=&quot;600&quot; height=&quot;400&quot;&gt;Your Broswer don&#39;t support html5 canvas&lt;canvas&gt;
&lt;/body&gt;
&lt;/html&gt;

<br>

</p>
<p>【step 4】做边界碰撞的反弹效果，也很简单，判断下小球的坐标x y,监测坐标与canvas高宽，在临界点让对应的变化量做反转即可，比如碰撞上下边界，就让dy = -dy即可。思路化为代码：</p>
&lt;!DOCTYPE html&gt;
&lt;html&gt;
&lt;head&gt;
&lt;style&gt;
body {margin:0; position:absolute; width:100%; height:100%}
canvas {display: block; margin: 20px auto; border: 2px solid #333}
&lt;/style&gt;
  &lt;/head&gt;
&lt;body&gt;
&lt;canvas id=&quot;canvas&quot; width=&quot;600&quot; height=&quot;400&quot;&gt;Your Broswer don&#39;t support html5 canvas&lt;canvas&gt;
&lt;/body&gt;
&lt;/html&gt;
<br>

<p>【step 5】绘制弹板，并且绑定键盘事件，让挡板可以左右移动。绘制的方法很简单，直接用canvas的rect方法，前面已经写好了，绑定键盘事件，咱们直接绑在左右按钮上，监测keydown，keyup事件，判断keyCode，然后每次重绘的时候让挡板移位即可。</p>
&lt;!DOCTYPE html&gt;
&lt;html&gt;
&lt;head&gt;
&lt;style&gt;
body {margin:0; position:absolute; width:100%; height:100%}
canvas {display: block; margin: 20px auto; border: 2px solid #333}
&lt;/style&gt;
  &lt;/head&gt;
&lt;body&gt;
&lt;canvas id=&quot;canvas&quot; width=&quot;600&quot; height=&quot;400&quot;&gt;Your Broswer don&#39;t support html5 canvas&lt;canvas&gt;
&lt;/body&gt;
&lt;/html&gt;
<br>

<p>【step 6】修改碰撞监测，小球落在挡板内可以继续，落在挡板外游戏结束。只需要修改撞击下边界的条件。即当球的y坐标大于画布总高度减去挡板厚度时，判断此时球的x坐标是否在挡板范围内。如果是那么小球弹回，游戏继续，否则游戏结束。游戏结束也很简单，直接清掉计数器。</p>
&lt;!DOCTYPE html&gt;
&lt;html&gt;
&lt;head&gt;
&lt;style&gt;
body {margin:0; position:absolute; width:100%; height:100%}
canvas {display: block; margin: 20px auto; border: 2px solid #333}
&lt;/style&gt;
  &lt;/head&gt;
&lt;body&gt;
&lt;canvas id=&quot;canvas&quot; width=&quot;600&quot; height=&quot;400&quot;&gt;Your Broswer don&#39;t support html5 canvas&lt;canvas&gt;
&lt;/body&gt;
&lt;/html&gt;
<br>

<p>【step 7】经过以上阶段，下面可以开始画砖块了，也很简单，定义一个二维的矩阵，根据行列数，算好每个砖块的宽度，通过之前写好的rect把这个二维矩阵转换成小方块就行了。<br>
</p>
<div>
<div><span style="color:#000000">        initBricks : </span><span style="color:#0000ff">function</span><span style="color:#000000"> (row, col) {<br>
            B.row </span><span style="color:#000000">=</span><span style="color:#000000"> row;<br>
            B.col </span><span style="color:#000000">=</span><span style="color:#000000"> col;<br>
            B.w </span><span style="color:#000000">=</span><span style="color:#000000"> W</span><span style="color:#000000">/</span><span style="color:#000000">col - 1;</span><span style="color:#000000"><br>
</span><span style="color:#000000">            B.h </span><span style="color:#000000">=</span><span style="color:#000000"> </span><span style="color:#000000">15</span><span style="color:#000000">;<br>
            B.pad </span><span style="color:#000000">=</span><span style="color:#000000"> </span><span style="color:#000000">1</span><span style="color:#000000">;<br>
            B.bricks </span><span style="color:#000000">=</span><span style="color:#000000"> </span><span style="color:#0000ff">new</span><span style="color:#000000"> Array(row);<br>
            <br>
            </span><span style="color:#0000ff">for</span><span style="color:#000000"> (</span><span style="color:#0000ff">var</span><span style="color:#000000"> i</span><span style="color:#000000">=</span><span style="color:#000000">0</span><span style="color:#000000">; i</span><span style="color:#000000">&lt;</span><span style="color:#000000">row; i</span><span style="color:#000000">++</span><span style="color:#000000">) {<br>
                B.bricks[i] </span><span style="color:#000000">=</span><span style="color:#000000"> </span><span style="color:#0000ff">new</span><span style="color:#000000"> Array(col);<br>
                </span><span style="color:#0000ff">for</span><span style="color:#000000"> (</span><span style="color:#0000ff">var</span><span style="color:#000000"> j</span><span style="color:#000000">=</span><span style="color:#000000">0</span><span style="color:#000000">; j</span><span style="color:#000000">&lt;</span><span style="color:#000000">col; j</span><span style="color:#000000">++</span><span style="color:#000000">) {<br>
                    B.bricks[i][j] </span><span style="color:#000000">=</span><span style="color:#000000"> </span><span style="color:#000000">1</span><span style="color:#000000">;<br>
                }<br>
            }<br>
        },<br>
        drawBricks : </span><span style="color:#0000ff">function</span><span style="color:#000000"> () {<br>
            </span><span style="color:#0000ff">for</span><span style="color:#000000"> (</span><span style="color:#0000ff">var</span><span style="color:#000000"> i</span><span style="color:#000000">=</span><span style="color:#000000">0</span><span style="color:#000000">; i</span><span style="color:#000000">&lt;</span><span style="color:#000000">B.row; i</span><span style="color:#000000">++</span><span style="color:#000000">) {</span><span style="color:#000000"><br>
                </span><span style="color:#0000ff">for</span><span style="color:#000000"> (</span><span style="color:#0000ff">var</span><span style="color:#000000"> j</span><span style="color:#000000">=</span><span style="color:#000000">0</span><span style="color:#000000">; j</span><span style="color:#000000">&lt;</span><span style="color:#000000">B.col; j</span><span style="color:#000000">++</span><span style="color:#000000">) {<br>
                    B.bricks[i][j] </span><span style="color:#000000">===</span><span style="color:#000000"> </span><span style="color:#000000">1</span><span style="color:#000000"> </span><span style="color:#000000">&amp;&amp;</span><span style="color:#000000"> </span><span style="color:#0000ff">this</span><span style="color:#000000">.rect(j</span><span style="color:#000000">*</span><span style="color:#000000">(B.w</span><span style="color:#000000">+</span><span style="color:#000000">B.pad) </span><span style="color:#000000">+</span><span style="color:#000000"> B.pad, i</span><span style="color:#000000">*</span><span style="color:#000000">(B.h</span><span style="color:#000000">+</span><span style="color:#000000">B.pad)</span><span style="color:#000000">+</span><span style="color:#000000">B.pad, B.w, B.h);<br>
                }<br>
            }<br>
        },</span></div>
</div>
<p> </p>
&lt;!DOCTYPE html&gt;
&lt;html&gt;
&lt;head&gt;
&lt;style&gt;
body {margin:0; position:absolute; width:100%; height:100%}
canvas {display: block; margin: 20px auto; border: 2px solid #333}
&lt;/style&gt;
  &lt;/head&gt;
&lt;body&gt;
&lt;canvas id=&quot;canvas&quot; width=&quot;600&quot; height=&quot;400&quot;&gt;Your Broswer don&#39;t support html5 canvas&lt;canvas&gt;
&lt;/body&gt;
&lt;/html&gt;

<br>

<p>【step 8】砖块碰撞。这里只做粗糙的碰撞监测，而且由于砖块厚度太低，dy都比砖块厚度更大，所以侧向的碰撞这里没做考虑。考虑纵向的碰撞的话，简单点，可以实时监控小球的y坐标，当满足小球坐标在砖块区并且砖块存在，那么就表示小球有和砖块碰撞，那么清除当前碰撞砖块，小球反向即可。<br>
</p>
<div>
<div><span style="color:#000000">        hitBrick : </span><span style="color:#0000ff">function</span><span style="color:#000000"> (x, y) {<br>
            </span><span style="color:#0000ff">var</span><span style="color:#000000"> rh </span><span style="color:#000000">=</span><span style="color:#000000"> B.h </span><span style="color:#000000">+</span><span style="color:#000000"> B.pad,<br>
                cw </span><span style="color:#000000">=</span><span style="color:#000000"> B.w </span><span style="color:#000000">+</span><span style="color:#000000"> B.pad,<br>
                row </span><span style="color:#000000">=</span><span style="color:#000000"> Math.floor(y</span><span style="color:#000000">/</span><span style="color:#000000">rh),</span><span style="color:#000000"><br>
</span><span style="color:#000000">                col </span><span style="color:#000000">=</span><span style="color:#000000"> Math.floor(x</span><span style="color:#000000">/</span><span style="color:#000000">cw);</span><span style="color:#000000"><br>
</span><span style="color:#000000">            </span><span style="color:#0000ff">if</span><span style="color:#000000"> (y </span><span style="color:#000000">&lt;</span><span style="color:#000000"> B.row</span><span style="color:#000000">*</span><span style="color:#000000">rh </span><span style="color:#000000">&amp;&amp;</span><span style="color:#000000"> row </span><span style="color:#000000">&gt;=</span><span style="color:#000000"> </span><span style="color:#000000">0</span><span style="color:#000000"> </span><span style="color:#000000">&amp;&amp;</span><span style="color:#000000"> col </span><span style="color:#000000">&gt;=</span><span style="color:#000000"> </span><span style="color:#000000">0</span><span style="color:#000000"> </span><span style="color:#000000">&amp;&amp;</span><span style="color:#000000"> B.bricks[row][col] </span><span style="color:#000000">===</span><span style="color:#000000"> </span><span style="color:#000000">1</span><span style="color:#000000">) {<br>
                dy </span><span style="color:#000000">=</span><span style="color:#000000"> </span><span style="color:#000000">-</span><span style="color:#000000">dy;<br>
                B.bricks[row][col] </span><span style="color:#000000">=</span><span style="color:#000000"> </span><span style="color:#000000">0</span><span style="color:#000000">;<br>
            }<br>
        },</span></div>
</div>
<p>
&lt;!DOCTYPE html&gt;
&lt;html&gt;
&lt;head&gt;
&lt;style&gt;
body {margin:0; position:absolute; width:100%; height:100%}
canvas {display: block; margin: 20px auto; border: 2px solid #333}
&lt;/style&gt;
  &lt;/head&gt;
&lt;body&gt;
&lt;canvas id=&quot;canvas&quot; width=&quot;600&quot; height=&quot;400&quot;&gt;Your Broswer don&#39;t support html5 canvas&lt;canvas&gt;
&lt;/body&gt;
&lt;/html&gt;
<br>

</p>
<p>好了，其实到这里差不多可以结束了，算是一个最最基础的打砖块小游戏，当然，感兴趣的同学可以自己再加其他的元素，增加其可玩性，比如我最开始那个加了暂停功能，加了颜色，加了结束画面。甚至你可以自己去加砖块道具，设置不同关卡，增加不同砖块属性，增加各种玩法来增强其可玩性。<br>
<span style="white-space:pre">	</span>仅仅作为教程，我觉得基本目的达到了。由于篇幅原因，有些具体的细节没法一一讲清楚。请包涵。如果对canvas的api不是很熟悉的同学可以先去查阅下资料。</p><img src="http://www.cnblogs.com/hongru/aggbug/1973792.html?type=1" width="1" height="1" alt=""><p>作者: <a href="http://www.cnblogs.com/hongru/">岑安</a> 发表于 2011-03-07 18:49 <a href="http://www.cnblogs.com/hongru/archive/2011/03/07/1973792.html">原文链接</a></p><p>评论: 6　<a href="http://www.cnblogs.com/hongru/archive/2011/03/07/1973792.html#pagedcomment">查看评论</a>　<a href="http://www.cnblogs.com/hongru/archive/2011/03/07/1973792.html#commentform">发表评论</a></p><hr><p>最新新闻：<br>· <a href="http://news.cnblogs.com/n/93229/">MG Siegler：你能跨过移动互联网时代的阻碍---“应用墙”吗？</a><span style="color:gray">(2011-03-07 23:05)</span><br>· <a href="http://news.cnblogs.com/n/93228/">长了恶意软件吗？Google 帮你杀干净！</a><span style="color:gray">(2011-03-07 22:32)</span><br>· <a href="http://news.cnblogs.com/n/93227/">doodle：三八妇女节</a><span style="color:gray">(2011-03-07 22:27)</span><br>· <a href="http://news.cnblogs.com/n/93226/">PPTV核聚变战略迈开首步 人员扩张将超500人</a><span style="color:gray">(2011-03-07 22:18)</span><br>· <a href="http://news.cnblogs.com/n/93225/">美团王兴：千万资金烧钱砸广告 不如直接返还用户</a><span style="color:gray">(2011-03-07 22:16)</span><br></p><p>编辑推荐：<a href="http://www.cnblogs.com/cmt/archive/2011/03/07/1973890.html">博客园电子期刊2011年2月刊发布</a><br></p><p>网站导航：<a href="http://www.cnblogs.com">博客园首页</a>  <a href="http://home.cnblogs.com/">我的园子</a>  <a href="http://news.cnblogs.com">新闻</a>  <a href="http://home.cnblogs.com/ing/">闪存</a>  <a href="http://home.cnblogs.com/group/">小组</a>  <a href="http://space.cnblogs.com/q/">博问</a>  <a href="http://kb.cnblogs.com">知识库</a></p></p>