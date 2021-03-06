---
layout: post
title:  "sassCore——设计更好的sass库"
date:   2013-05-25 21:35:14
author: marvin
categories: program
---

## sassCore——设计更好的sass库
### by marvin
### at 2013-05-25 21:35:14
### original <http://www.w3cplus.com/blog/768.html>

<div><div><div><p>目前sass库中应用最多的应该就是<a href="http://compass-style.org/">compass</a>和<a href="http://bourbon.io/">bourbon</a>，但是使用之后会发现compass设计太复杂了，而bourbon有点太简单了。于是只好琢磨着去搞一个使用起来更方便合理的sass库，经过翻阅众多资料、实践及思考，终于有了现在的<a href="https://github.com/marvin1023/sassCore">sassCore</a>，当然到目前为止sassCore还不是非常完善，但是对付日常的工作已经很够了。</p>
<h2>
	之所以说是设计更好，决不会空穴来风，下面我们从几个方面简单说下sassCore的优势。</h2>
<p>1、sassCore涵盖范围广，目前涉及了setting，reset，mixin，css3，typography，media-queries，grids，helps八个部分。</p>
<p>2、sassCore采用开关机制，对是否支持ie6/7等众多条件可以通过设置为true或false来搞定。</p>
<p>3、sassCore引入最新的sass变量机制，默认变量!default，在应用的时候只需在你引入sassCore文件之前，重新申明变量就ok，而不需要去修改sassCore文件。</p>
<p>4、sassCore引入最新的sass placeholder选择器，如果需要extend样式的时候，避免了先前的class申明，placeholder的优点在于如果你不调用，根本不会解析出样式。</p>
<p>5、sassCore使用新的思想，科学的方法，跟随流行，每一个文件都是经过深思熟虑，几经更改的结晶。</p>
<h2>
	当然优势不是说出来的，而是在代码中体验的。下面我们从各个文件来简单分析下sassCore。</h2>
<p><strong>setting</strong>是负责变量的文件，拿<span>$</span><span>lte7</span><span>:</span> <span>true</span> <span>!</span><span>default</span><span>;来说，表示默认支持ie6/7，这根据你的实际项目来决定，如果你不需要兼容ie6/7的话，在引入setting文件之前先设置</span><span>$</span><span>lte7</span><span>:</span> false;那么所有sassCore里面的文件解析出来就不会有为ie6/7而设置的一些样式，这里面就涉及了最新的变量机制及开关机制。</p>
<p><strong>reset</strong>其实包括normalize和reset，normalize相信不用我再介绍了，当然这个normalize是用scss写的，来自于shkm的<a href="https://github.com/shkm/normalize.sass">normalize.sass</a>，做了些修改，并把标题样式去掉了。而reset是根据目前我们大家的使用习惯进行了一些归零行动，及设置文字字体及颜色，打印样式等。</p>
<p><strong>mixin</strong>是负责功能方面的文件，这里我们大概分成三个部分，一个是混合部分即mixin，一个是placeholder选择器部分即%，最后就是我们的function函数部分。所以我们常用的include及extend当然就是来自于这里了。mixin一般适用于传递参数，然后它通过include调用，样式是以拷贝形式存在的；而%适用于定义那些没有参数的一些小的代码片段，然后它通过extend调用，样式是以联合申明而存在的。从这两个比较来说：联合申明当然要优于拷贝，但是参数的作用那也是相当的优越啊，所以这两个东西可以并列存在。当然为了方便一些已经有旧习惯使用include的人，这里在定义%的同时，我也同时定义了mixin。至于function，那就涉及到一些计算什么的了，最典型的就是px转em或rem了，其实在grids里面也用到了function来计算宽度。</p>
<p><strong>grids</strong>是负责网格系统，话说这个scss文件是我修改最频繁花费时间最多也是最纠结的，这所有的“最”最终导向一个我想要的结果。目前来说我们常见的网格系统都是用class来布局的，这对我们的html结构就需要对应的class，所以使用起来html结构就略显不雅。这里我参考了<a href="http://www.blankwork.net/">blankwork</a>，<a href="http://semantic.gs/">semantic</a>等优秀的网格设计思路，前者主要是固定宽度的一种更好的计算宽度方式，而后者主要是对于流体布局的网格计算。前者所有的思想都集合在一个get-width的function上，而这个function比起其他的来说更优秀的体现在加入了一个$_subtract参数，这样就可以不必要是刚好的网格宽度，而可以灵活减少。至于流体布局的sass中，semantic就不得不提了。最后把优秀的吸收，然后再加工创造，添加新的思想及功能，引入开关机制，更好的切换固定宽度与流体。默认为流体，如要固定宽度，在引入文件之前设置<span>$</span><span>_percentLayout为false即ok。</span></p>
<p><span><strong>css3</strong>顾名思义是负责css3的前缀的，拷贝了</span><a href="http://bourbon.io/">bourbon</a>的，关于这个bourbon和compass的设计思路都是一样的，通过定义一个function，然后只需传递你的属性及需要的前缀就可以了，我们可以看到有很多人其实是一条一条写出来的，这是多么的不科学啊，因为css3的属性也比较多，所以没有一一测试，如果你使用后有什么bug欢迎及时反馈，谢谢！</p>
<p><span><strong>media-queries</strong>就是响应式布局的一些宽度判断，没什么好说的，直接把<span><a href="https://github.com/paranoida" rel="author">paranoida</a></span>的</span><span><a href="https://github.com/paranoida/sass-mediaqueries">sass-mediaqueries</a></span>拷贝过来了哈哈，原谅我的偷懒吧，因为他已经有写得很好了。</p>
<p><strong>typography</strong>就是负责文字排版的，这里主要考虑到文章详细页和其他页面的一些不同情况而给详细页加入了article这个class，里面的一些元素如ul，li，p给予一定的样式，而不是清零的。</p>
<p>至于最后的<strong>helpers</strong>，就是一些常用的class了，这个看个人喜好吧。没什么说的，我也没写几个，一看就明白了。</p>
<p>下一篇我们将说下在实际项目中使用sassCore</p>
</div></div></div><div><ul><li><a href="http://www.w3cplus.com/blog/tags/8.html">html&amp;css</a></li></ul></div><div><ul><li><a href="http://www.w3cplus.com/blog/tags/302.html">sass</a></li><li><a href="http://www.w3cplus.com/blog/tags/309.html">preprocessor</a></li></ul></div><div><div><div><div>
      <div>21</div>
                  <a href="http://www.w3cplus.com/vote/node/768/1/vote/alternate/ZejPgXzXfVHKiE31oB614GNETcubXzk2CBjDZezUbF4/nojs" rel="nofollow">
                <div title="Vote up!"></div>
          <div>Vote up!</div>
              </a>
                </div>
</div></div></div><img src="http://www1.feedsky.com/t1/727625099/W3CPlus/feedsky/s.gif?r=http://www.w3cplus.com/blog/768.html" border="0" height="0" width="0">