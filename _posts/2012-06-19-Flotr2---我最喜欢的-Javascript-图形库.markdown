---
layout: post
title:  "Flotr2 - 我最喜欢的 Javascript 图形库"
date:   2012-06-19 07:03:00
author: log4d
categories: program
---

## Flotr2 - 我最喜欢的 Javascript 图形库
### by log4d
### at 2012-06-19 07:03:00
### original <http://www.udpwork.com/item/7504.html>

<p>原文地址：<a href="http://swizec.com/blog/flotr2-my-favorite-javascript-graph-library">Flotr2 - my favorite javascript graph library</a></p>
<p>译者：<a href="http://log4d.com/2012/06/flotr2/">alswl</a></p>
<p>我研究了很久，得到的终极数据可视化方案是 javascript + html ，这是唯一像样又好用的。</p>
<p><a href="http://www.gnuplot.info/">Gnuplot</a>虽然看上去不错，但是不太漂亮。
一些基于 Python 的图形化工具也有同样的问题。<a href="http://www.wolfram.com/products/mathematica/">Mathematica</a>在我看来则太正式了，所以我不怎么用它。</p>
<p>基于 javascript 的图形化工具其中一个巨大优势是易于共享。
通过设置 Github Page、分享链接等方法，我们都可以很方便的获取最新图表数据，
甚至可以进行交互。</p>
<h2>那些基于 javascript 的图形化工具</h2>
<p>有很多 javascript 图形化库，从极简到复杂绚丽的风格，都极为丰富。</p>
<p>最流行的可能就是<a href="http://d3js.org/">d3.js</a>和<a href="http://raphaeljs.com/">Raphael</a>了。
他们用起来相当麻烦，但却能实现绚丽到超越你想象的可视化效果。</p>
<p>Raphael 还一个 web 矢量库，外观同样很漂亮。</p>
<p><a href="http://upload.log4d.com/2012/06/Screen-Shot-2012-06-01-at-2.41.59-PM.png?1d5d3d"><img src="http://upload.log4d.com/2012/06/Screen-Shot-2012-06-01-at-2.41.59-PM.png?1d5d3d" alt="An analytics graph in Raphael"></a></p>
<p>Raphael 中的分析图表</p>
<p><a href="http://upload.log4d.com/2012/06/Screen-Shot-2012-06-01-at-2.42.12-PM.png?1d5d3d"><img src="http://upload.log4d.com/2012/06/Screen-Shot-2012-06-01-at-2.42.12-PM.png?1d5d3d" alt="Bubble graph in Raphael"></a></p>
<p>Raphael 中的气泡图</p>
<p><a href="http://upload.log4d.com/2012/06/Screen-Shot-2012-06-01-at-2.42.31-PM.png?1d5d3d"><img src="http://upload.log4d.com/2012/06/Screen-Shot-2012-06-01-at-2.42.31-PM.png?1d5d3d" alt="A clock made with Raphael"></a></p>
<p>Raphael 中的钟表时间图</p>
<p>D3.js 的愿景是帮助人们轻松的完成数据可视化，可惜事与愿违，
它弄得太复杂了。</p>
<p><a href="http://upload.log4d.com/2012/06/Screen-Shot-2012-06-01-at-3.03.35-PM.png?1d5d3d"><img src="http://upload.log4d.com/2012/06/Screen-Shot-2012-06-01-at-3.03.35-PM.png?1d5d3d" alt="HN top titles visualisation in d3.js"></a></p>
<p>Hacker News 文章标题的可视化数据</p>
<p><a href="http://upload.log4d.com/2012/06/Screen-Shot-2012-06-01-at-2.46.31-PM.png?1d5d3d"><img src="http://upload.log4d.com/2012/06/Screen-Shot-2012-06-01-at-2.46.31-PM.png?1d5d3d" alt="House hunting in d3.js"></a></p>
<p>用 d3.js 绘制购房信息</p>
<p><a href="http://upload.log4d.com/2012/06/Screen-Shot-2012-06-01-at-2.46.53-PM.png?1d5d3d"><img src="http://upload.log4d.com/2012/06/Screen-Shot-2012-06-01-at-2.46.53-PM.png?1d5d3d" alt="Scatterplots made with d3.js"></a></p>
<p>用 d3.js 制作的散布图</p>
<h2>Flotr2</h2>
<p>这些可视化工具，虽然看上去很华丽，但是难以使用。
他们会很浪费时间，让人捣鼓到最后却发现什么都没弄出来。
大部分 Raphael 和 d3 的功能矫正过枉了，有时我们只想要一个简单的绘图工具而已。</p>
<p>为了将<a href="http://swizec.com/blog/a-year-of-750words-com-with-shiny-graphs/swizec/4378">我去年的 750 个关键字</a>数据可视化，我使用了<a href="http://humblesoftware.com/flotr2/">Flotr2</a>。
只花费我数分钟，就能通过操作 javascript 将数据转化成 Flotr2 能理解的函数，
然后 Flotr2 就将数据转化成漂亮的图表。</p>
<p>Flotr2 最大的优点是他没有过度设计，当你只想要一个简单图表时候，
它不会让你去操作过多东西。</p>
<p>往往只需要你设定必要的数据和一两个标签，比如：</p>
<div><table><tr><td><pre>1
2
3
4
5
6
7
8
9
10
11
12
13
14
15
16
17
18
19
20
21
22
23
24
25
26
27
28
29
30
31
32
33
34
35
36
37
38
39
40
41
42
43
44
45
46
47
48
49
</pre></td>
<td><pre>function draw_time(container, data, title) {
  var
  start = new Date(DATA[0].date).getTime(),
  options,
  graph,
  i, x, o;

  options = {
      xaxis : {
          mode : 'time',
          labelsAngle : 45
      },
      selection : {
          mode : 'x'
      },
      legend: {
          position: 'se'
      },
      HtmlText : false,
      title : title
  };

  // Draw graph with default options, overwriting with passed options
  function drawGraph (opts) {

      // Clone the options, so the 'options' variable always keeps intact.
      o = Flotr._.extend(Flotr._.clone(options), opts || {});

      // Return a new graph.
      return Flotr.draw(
          container,
          data,
          options
      );
  }

  graph = drawGraph();

  Flotr.EventAdapter.observe(container, 'flotr:select', function(area){
      // Draw selected area
      graph = drawGraph({
          xaxis : { min : area.x1, max : area.x2, mode : 'time', labelsAngle : 45 },
          yaxis : { min : area.y1, max : area.y2 }
      });
  });

  // When graph is clicked, draw the graph with default area.
  Flotr.EventAdapter.observe(container, 'flotr:click', function () { graph = drawGraph();});
}
</pre></td>
</tr>
</table>
</div>
<p>然后你就能获得下面这张图表：</p>
<p><a href="http://upload.log4d.com/2012/06/Screen-Shot-2012-06-01-at-2.58.02-PM.png?1d5d3d"><img src="http://upload.log4d.com/2012/06/Screen-Shot-2012-06-01-at-2.58.02-PM.png?1d5d3d" alt="A graph in Flotr2"></a></p>
<p>好用吧。</p>
<p>看上去蛮漂亮，符合它简洁易用的特点。</p>
<div><p><strong>版权所有 © 2010 转载本站文章请注明：</strong>
转载自<a href="http://log4d.com/">Log4D</a>
<br>
<strong>原文链接:</strong>
<a href="http://log4d.com/2012/06/flotr2/">http://log4d.com/2012/06/flotr2/</a>
<br>
您可以随意地转载本站的文章，但是必须在醒目位置注明来源及本站链接，不可以将本站文章商业化使用，或者修改、转换或者以本作品为基础进行创作。
<br>
3a1ff193cee606bd1e2ea554a16353ee</p>
</div>
<img src="http://feeds.feedburner.com/~r/dddspace/~4/E5B-Mz8WXGU">
			<div style="margin-top:8px;padding:6px 0;border-top:1px solid #3cf">
				<div style="text-align:center;margin:16px 0;padding:6px;border:0px dashed #999;font-family:arial;font-size:26px;font-weight:bold">
	<a href="http://www.udpwork.com/item/7504.html#review_form" title="不喜欢" style="text-decoration:none">
		<img src="http://www.udpwork.com//images/thumb_down24.gif" alt="">
		<span style="color:#f33">0</span>
	</a>
	   
	<a href="http://www.udpwork.com/item/7504.html#review_form" title="喜欢" style="text-decoration:none">
		<img src="http://www.udpwork.com//images/thumb_up24.gif" alt="">
		<span style="color:#3c3">0</span>
	</a>
</div>				<p>
					由 <a href="http://www.udpwork.com/">IT 牛人博客聚合网站(udpwork.com)</a> 聚合
					|
					<a href="http://www.udpwork.com/item/7504.html#reviews">评论: 0</a>
					|
					<a href="http://book.benegg.com/tag/%E7%BC%96%E7%A8%8B?from=udpwork-feed">10000+ 本编程/Linux PDF/CHM 电子书下载</a>
				</p>
			</div>