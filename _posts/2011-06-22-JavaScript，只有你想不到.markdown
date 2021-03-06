---
layout: post
title:  "JavaScript，只有你想不到"
date:   2011-06-22 21:59:27
author: 为之漫笔
categories: program
---

## JavaScript，只有你想不到
### by 为之漫笔
### at 2011-06-22 21:59:27
### original <http://www.cn-cuckoo.com/2011/06/22/time-to-learn-javascript-2463.html>

<p style="text-align:right">原文地址：<a href="http://radar.oreilly.com/2011/06/time-to-learn-javascript.html">http://radar.oreilly.com/2011/06/time-to-learn-javascript.html</a><br>作者简介：<a href="http://radar.oreilly.com.cn/user/michael_loukides">Mike Loukides </a></p>
<p><a href="http://radar.oreilly.com.cn/user/michael_loukides"><img src="http://www.cn-cuckoo.com/images/loukides.jpg" style="float:left;margin:0 1em 1em 0"></a>很长时间以来，JavaScript在我眼里都是编程语言中的二等公民。早先，它经常是很多安全问题的发源地，就像是胶水一样，它能把HTML应用与样式粘到一块，可没有人拿它来正正规规地写程序；这样的情形太普遍了。而Java、Ruby、Python，这些才是真正能用来写程序的语言。</p>
<p>过去几年间，我对JavaScript的态度有了彻底的改变。JavaScript已经“长大成人”了。我敢保证很多JavaScript开发人员都不会认同我前面的说法，他们会说JavaScript一直都是一个十分强大、成熟、深得人心的语言。或许他们说得没错，事实上只要是一门完整的编程语言，就能拿来写程序，也包括BASIC这种滥东西。而一门语言真正有用，必须一方面自身具备很强的表达能力，另一方面还要有众多的库和开发工具。显然，JavaScript的表达能力早就没有问题了，即便是创建对象的方式有点不好让人接受，其实问题也不大。直到最近，一些极其重要的扭转局面的技术出现了：jQuery、JSON、Node.js和HTML5。或许JavaScript以前就是一门完善的语言了，但却是这些重要的相关技术（以及其他一些没有在这里提及的），让JavaScript成为了每一个开发人员都知道的语言。如果明年你要学一门新语言的话，那一定就是JavaScript。</p>
<h2>潜力无限的Node.js</h2>
<p>说<a href="http://nodejs.org/">Node.js</a>潜力无限的意思，就是它有可能引发Web开发的革命。Node.js是一个框架，用于构建高性能Web应用——即使是巨量的请求也能应对如流。虽然Node本身作为一个底层框架，能够用于构建任何应用，但它还是最适合构建Web服务器。它的异步事件驱动模式与传统的请求-响应模式相比，无疑更适合Web应用。<span></span></p>
<p>有两方面因素更让人看好Node。首先，Google在提升JavaScript性能方面掀起了一场革命。这句话的意思并不是说你随时随地都可以用上最好的JavaScript引擎（尽管这也是我们一个美好的期望）。但可以肯定的是，Google在其他竞争对手还没有上心的情况下，真的把JavaScript性能当成了一回事儿。如此一来，就把Mozilla、Apple、Microsoft、Opera，还有其他浏览器开发商逼到了性能竞赛的跑道上。结果导致我们现在使用的JavaScript引擎较之几年前快了不知道有多少倍，完全有能力运行复杂的大型Web应用。</p>
<p>其次，Node有着庞大的开发人员基础。不管大家在服务器端使用的是什么语言，但在客户端却鲜有不使用JavaScript的。有的人可能是“剪刀加浆糊”式的东拼西凑，有的人则可能用JavaScript做出了高超的Ajax应用，而有的人甚至实现了全功能的应用程序，像Twitter或Gmail。可不管怎么说，JavaScript开发人员的数量无疑是非常庞大的。而<a href="http://www.crockford.com/">Doug Crockford</a>等作者更是极力宣传所有人都应该把JavaScript当成一门严肃正经的编程语言来看待——尽管它还有不少缺点。</p>
<p>当时当下，编写Node应用相对还是个“粗”活儿，毕竟它只是一个底层库。想象一下单纯使用JavaScript写代码，对，就是这种感觉，Node当前还是一个beta版的格局，与Rails或Django这样成熟的Web开发框架还没法比。这种状况无疑会改变。一些轻量级的框架，比如<a href="http://expressjs.com/">Express</a>，已经出现了；我坚信更多基于Node的全功能框架将继续不断涌现。</p>
<p>前面提到过一些几乎完全在浏览器中运行的高级Web应用。那些都已经不算什么新鲜事儿了，<a href="http://en.wikipedia.org/wiki/History_of_Gmail">Gmail多大了</a>？<a href="http://en.wikipedia.org/wiki/Google_Maps#History">Google Maps贵庚了</a>？不过，用JavaScript编写在浏览器中运行的应用的客户端无疑是越来越有吸引力了。HTML5则继续推高了人们对这一趋势的期许。</p>
<h2>HTML5就是JavaScript</h2>
<p>我不知道已经说过多少次了，HTML5实际上并没有多少与HTML有关，它其实就是JavaScript。HTML本身有什么变化？不过一些新标签而已，况且哪个新标签都不难理解。HTML5的威力在于让你能用JavaScript来创建这些标签。假如没有后台代码通过Canvas来创建动画、游戏，或者通过它来实现一些数据的可视化，这个标签也没有大用处。从浏览器开始支持Canvas开始，我已经看到了<a href="http://www.kevs3d.co.uk/dev/asteroids/">Asteroids</a>（行星游戏）的上百个实现，那都是开发人员为熟悉这个新特性所做的练习。有的比较粗糙一些，而有的则极其精美。这些完全都要归功于JavaScript。</p>
<p>由此可见，HTML5并不是以尖括号为特征的标签语言的一次大的改进，其实质是赋予了JavaScript更强大的能力。<a href="http://en.wikipedia.org/wiki/WebGL">WebGL</a>库（当前还羽翼未丰）支持在HTML5的画布中绘制实时的3D图形。HTML5的地理位置支持在浏览器中实现LBS（Location Based Service）应用——这都是手机的基本配置。而持久存储以及离线功能则为开发能与桌面应用媲美，但却在浏览器中运行的全功能应用奠定了基础。目前，就连<a href="http://ajaxian.com/archives/iphone-web-multitouch-javascript-virtual-light-table">增加多点触摸事件的实验性的库</a>也已经出现了。凡此种种，无一不是实实在在的JavaScript特性。HTML5只是为这些高级功能的发挥提供了舞台。</p>
<p>退一步讲，不依赖于HTML5的浏览器端开发库也取得了长足的进步。长久以来，JavaScript一直都是在HTML中实现动态效果的不二之选。可两个问题迟迟得不到解决：一是浏览器兼容性问题，二是直接操作DOM太麻烦。<a href="http://jquery.com/">jQuery</a>让这两个问题霎那间消失得无影无踪，这个库已经成为现代基于浏览器的客户端开发的基本配置。不过，并非只有jQuery。<a href="http://vis.stanford.edu/protovis/">Protovis</a>、<a href="https://github.com/mbostock/d3">还有D3</a>，都可以让你直接在浏览器中创建复杂的交互性数据可视化效果，有史以来第一次让浏览器成为了展示数据的一个重要媒介。</p>
<h2>JavaScript与数据库，编译器与语言</h2>
<p>就连数据库里都开始广泛使用JavaScript了！当前如火如荼的NoSQL运动的三只领头羊：<a href="http://couchdb.apache.org/">CouchDB</a>、<a href="http://www.mongodb.org/">MongoDB</a>和<a href="http://wiki.basho.com/">Riak</a>，都是“文档数据库”。它们保存的不是表，而是文档。这几个数据库所谓的“文档”，其实就是<a href="http://www.json.org/">JSON</a>文档，而不是Word或Excel。（Riak除了JSON文档，还支持XML和纯文本。）JSON已经成为一种被广泛采用的数据交换格式（所有现代的编程语言几乎全都有解析JSON的库），不过请注意，JSON实际上不就是一种序列化JavaScript对象的格式嘛！因此，虽然你可以在任何语言中使用JSON，但在JavaScript开发中使用它则是再自然不过的事了。况且，JSON 这个格式成为一种跨语言的标准，而不是Python、Ruby或Java等语言的序列化格式，这个事实本身足以说明JavaScript将在更加广阔的舞台上大显身手。还不仅仅如此，上述三个数据库都内置了支持JavaScript查询的能力。未来几年，更多的人都将会惊讶地发现，JavaScript和JSON还会内置到其他应用程序中！</p>
<p>JavaScript时代的大幕才刚刚拉开。在今年的<a href="http://2011.jsconf.us/">JSConf</a>上，一个核心主题就是“JavaScript到JavaScript的编译器”，也被人们看成是未来的一个主要趋势。Google在“编译生成JavaScript代码”方面是首开先河者。据我所知，<a href="http://code.google.com/webtoolkit/">GWT（Google Web Toolkit）</a>应该是通过编译（从Java代码）生成JavaScript代码的第一个框架。以前我对GWT并没有太重视，只是觉得它是一个致力于拯救那些Java程序员的框架，好让他们不必因为（学习）编写JavaScript而浪费时间。可是，GWT在编译过程中对JavaScript做了那么多的优化，简直是太神了。<a href="http://code.google.com/closure/compiler/">Closure</a>就是一个“JavaScript到JavaScript的编译器”，能够实现同样级别的优化。<a href="http://code.google.com/p/traceur-compiler/">Traceur</a>，这是几个星期前才冒出来的一个框架，通过它能够试验JavaScript的新特性，换句话说，它可以把带有实验性语言特性的JavaScript代码编译成可以在所有现代平台中运行的JavaScript代码。</p>
<p>最后，我们也开始看到了当初Java大旗下JVM语言的蓬勃景象：<a href="https://github.com/jashkenas/coffee-script/wiki/List-of-languages-that-compile-to-JS">很多语言都在致力于编译成JavaScript</a>！其中有一些语言比较有意思，像<a href="http://jashkenas.github.com/coffee-script/">Coffeescript</a>和<a href="http://weepy.github.com/kaffeine/">Kaffeine</a>，它们在风格上酷似JavaScript，但更关注弥补JavaScript的一些不够完善的地方。是不是觉得JavaScript的对象模型特有意思，可怎么看怎么有点笨笨滴，有木有？是不是一想到基于原型创建一个实际的对象都需要反反复复地定义这定义那，就望而却步了，有木有？Coffeescript对此作了明显的改进。除了完善对象模型，Coffeescript 还添加了类似列表解析（list comprehensions）的新特性，去掉了大部分花括号。就像在Python中一样，要使用缩进来区分代码块。</p>
<p>未来的Web服务器、取之不尽的客户端库、HTML5、数据库，乃至基于JavaScript的语言——我现在一睁眼看到的就是JavaScript！假如你曾经对JavaScript敬而远之，今年可是该学习它了。没有任何理由，真的，再不学，恐怕你就没机会跟上时代了！</p>