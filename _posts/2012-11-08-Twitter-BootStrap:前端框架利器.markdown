---
layout: post
title:  "Twitter BootStrap:前端框架利器"
date:   2012-11-08 10:35:46
author: Airen
categories: program
---

## Twitter BootStrap:前端框架利器
### by Airen
### at 2012-11-08 10:35:46
### original <http://www.w3cplus.com/css/twitter-bootstrap.html>

<div><div><div><p>Web前端开发者每天都与HTML、CSS、JavaScript打交道，然而不少人都是周而复始地写模板、样式和交互效果，并没有想过如何将这些重复的工作整合在一起。Twitter推出的Bootstrap能够帮助Web前端开发者摆脱这种重复劳动。</p>
<h3 style="font-size:24px;color:green">
	Bootstrap的历史</h3>
<p>为了应对复杂的需求，早期的Twitter前端工程师在开发网站时几乎采用了所有自己熟悉的前端库。造成了网站维护困难、扩展性不强、开发成本高等问题。此时BootStrap被提上了日程。Twitter要求前端工程师完全依靠这一单一框架进行前端开发。</p>
<p>Twitter 在2011年8月将其开源，并在2012年2月3日发布了2.0版。在GitHub上，这个项目已有拥超过2万位关注者和4000个分支。 Bootstrap的设计者、著名前端工程师Mark Otto这样写道：“Bootstrap是我和Jacob Thornton编写的一个前端工具箱，目的是为了帮助设计师和Web前端开发人员快速有效地创建一个结构简单、性能优良、页面精致的Web应用。它使用 了最新的浏览器技术，可以提供精致的网页排版方式以及表单、按钮、表格、网格栅格化、导航等诸多元素。”Bootstrap的内置样式继承了Mark Otto简洁亮丽的设计风格，任何开发团队都能使用它提供的HTML模板、CSS样式和jQuery组件来布署或者重建一个外观漂亮的页面应用。</p>
<h3 style="font-size:24px;color:green">
	BootStrap 2的新特性</h3>
<p>BootStrap 2在原有特性的基础上着重改进了用户的体验和交互性，比如新增加的媒体展示功能，适用于智能手机上多钟屏幕规格的响应式布局，另外新增了12款jQuery插件，可以满足Web页面常用的用户体验和交互功能。</p>
<h3 style="font-size:24px;color:green">
	BootStrap 2的运用</h3>
<h4>
	Bootstrap的文件结构</h4>
<p>读 者可以直接从GitHub下载到Bootstrap源码，本地解压后可以看到这样的目录结构：docs、img、jquery-ui- bootstrap、js和less。其中最为重要的是“docs”下的CSS样式文件，“less”中的编译文件和“js”下的jQuery插件。</p>
<h4>
	Bootstrap的安装</h4>
<p>关于如何应用提取出来的文件大致有两步：第一是如何安装Bootstrap的基本样式，第二是如何调用Bootstrap的jQuery插件，我们首先来看样式的安装。</p>
<p>样式的安装有多种方法，图1展示的是一种常用的调用样式方法“link”。</p>
<p><img alt="" height="92" src="http://www.w3cplus.com/sites/default/files/styles/print_image/public/blogs/bootstrap/bootstrap-1.jpg" style="border:1px solid #ccc;padding:2px" title="图1  Bootstrap样式调用方式" width="502"></p>
<p>这 里有两个关键点，其中“bootstrap.css”是Bootstrap中的基本样式文件，只要使用Bootstrap就必须调用这个文件。而 “bootstrap-responsive.css”则可以根据你的爱好来选择，如果想让项目具有响应式布局的效果，就必须要调用这个样式文件，而且调 用必须遵循先后顺序，“bootstrap-responsive.css”必须放置在“bootstrap.css”之后，否则便不具有响应式布局功 能。而最后的“style.css”是项目中的自定义样式，用来覆盖Bootstrap中的一些默认设置，便于开发者定制。</p>
<p>“CSS”样式安装完后，就可以进入“js”的调用，方法很简单，只需把想要的jQuery插件按照与上一步相似的方式加入到代码中。</p>
<h4>
	Bootstrap 2的模块</h4>
<p>BootStrap 2的模块从大的方面可以分为布局框架、页面排版、基本组件、jQuery插件以及变量编译的Less几个部分。与第1版相比，Bootstrap 2增加了多个新模块，比如布局框架中的“响应式布局”，页面排版中的“ICON”，基本组件中的“进度条”，而jQuery插件从以前的5个效果增加到 12个，完全可以满足项目常用的交互效果。下面来简单了解一下Bootstrap 2中各模块的功能。</p>
<p><strong>页面布局</strong></p>
<p>布局在每个项 目中都必不可少，Bootstrap在960gs的基础上扩展了一套优秀的Grids布局，而在“响应式布局”中有更强大的功能，能让网格布局适应各种设 备。使用也相当简单，只需要按照HTML模板应用，就能轻松地构建你所需的布局效果。此外，改变模板中的类名，就能实现不同的布局风格。比如常见的“固定 布局”，只需要在HTML中添加“container”类名；而要实现“流体布局”，只需要在HTML中添加“container-fluid”类名。 Bootstrap还为开发者设计了“Responsive”， 令布局框架更为出色。开发者可以在此基础上进行任何样式的覆盖，从而实现理想中的响应式设计。</p>
<p><strong>页面排版</strong></p>
<p>页面排版的好坏直接影 响产品风格，说直白点就是好不好看。在Bootstrap中，页面的排版都是从全局的概念上出发，定制了主体文本、段落文本、强调文本、标题、Code风 格、按钮、表单、表格等格式。而Bootstrap 2中又添加了几个新亮点。其一是“Code”使用了“Google Prettify”插件，增强了代码的阅读体验；其二在“按钮”中增加了组合、下拉、图标等效果，如图2所示。</p>
<p><img alt="图2  Bootsrap 2中的按钮样式" src="http://www.w3cplus.com/sites/default/files/styles/print_image/public/blogs/bootstrap/bootstrap-2.jpg" style="border:1px solid #ccc;padding:2px" title="图2  Bootsrap 2中的按钮样式"></p>
<p>第三就是“Icon”的使用，Bootstrap在“Icon”部分采用了“Sprites”技术，为大家准备了上百种常用的“Icon”图标应用。</p>
<p>说 起Icon，这里有必要在向读者推荐一个从BootStrap扩展出来的Font Awesome项目。它是Dave Gandy在Bootstrap的基础上扩展出来的一个Icon主题，最大的特点在于，整套图标中没有运用任何图片。大家可能会觉得奇怪，没有图片如何制 作Icon？难道是通过纯CSS编写的吗？是的，Font Awesome项目中主要运用了CSS3的“@font-face”和“伪元素”一起实现。</p>
<p>要使用Font Awesome，首先需要在服务器上安装“fontawesome-webfont”字体，接着在样式中通过“@font-face”来启用这些字体：</p>
<p><img alt="" height="188" src="http://www.w3cplus.com/sites/default/files/styles/print_image/public/blogs/bootstrap/bootstrap-3.jpg" style="border:1px solid #ccc;padding:2px" width="502"></p>
<p>具备了上面的条件后，就只要样式中调用“FontAwesome”字体，并在“伪类元素”中使用对应的“字符编码”：</p>
<p><img alt="" height="147" src="http://www.w3cplus.com/sites/default/files/styles/print_image/public/blogs/bootstrap/bootstrap-4.jpg" style="border:1px solid #ccc;padding:2px" width="502"></p>
<p>这样一来，就不采用任何图片实现Icon效果，一起来看一个效果图吧。</p>
<p><img alt="" height="136" src="http://www.w3cplus.com/sites/default/files/styles/print_image/public/blogs/bootstrap/bootstrap-5.jpg" style="border:1px solid #ccc;padding:2px" width="314"></p>
<h4>
	基本组件</h4>
<p>基 本组件是Bootstrap 2的精华之一，里面都是开发者平时需要的交互组件。比如“网站导航”、“Tabs”、“工具条”、“面包屑”、“分页栏”、“提示标签”、“产品展示”、 “提示信息块”和“进度条”等。这些组件都配有“jQuery”插件，运用它们可以大幅度提高用户的交互体验，使产品不再那么呆板无吸引力。说了这么多种 组件，具体该如何使用？下面我以常见的“Tabs”组件为例，向大家展示使用方法。</p>
<p>要想使用BootStrap基本组件，必须满足三点：第一，最基本的HTML结构要对号；第二，需要Bootstrap中的“jQuery”插件提供相应功能；第三，在项目中对应的“Tabs”元素上启用“Tabs”功能。</p>
<p>1. HTML Markup</p>
<p><img alt="" src="http://www.w3cplus.com/sites/default/files/styles/print_image/public/blogs/bootstrap/bootstrap-6.jpg" style="border:1px solid #ccc;padding:2px" title=""></p>
<p>2. 调用jQuery插件</p>
<p>3. 开启“Tab”功能</p>
<p><img alt="" src="http://www.w3cplus.com/sites/default/files/styles/print_image/public/blogs/bootstrap/bootstrap-7.jpg" style="border:1px solid #ccc;padding:2px" title=""></p>
<p>对于其他组件，使用方法相近，在此不做赘述。</p>
<h4>
	jQuery插件</h4>
<p>Bootstrap中的jQuery插件主要用来帮助开发者实现与用户交互的功能，在第1版中，Bootstrap就为大家提供了6种常见的插件。</p>
<ol><li>
		弹出框（Modals）：在JavaScript模板基础上自定义的一款流线型、灵活性极强的弹出蒙板效果的插件；</li>
<li>
		下拉框（Dropdowns）：Bootstrap中一款轻巧实用的插件，可能帮助你制作具有下拉功能，比如下拉菜单、下拉按钮、下拉工具条等效果；</li>
<li>
		滚动条（Scrollspy）：实现滚动条位置的效果，比如在导航中有多个标签，用户点击其中一个标签，滚动条会自己动定位到导航中标签对应的文本位置；</li>
<li>
		Tabs：这个插件能够快速实现本地内容的转换，动态切换标签对应的本地内容；</li>
<li>
		提示工具（Tooltips）：是一款优秀的jQuery插件，无需加载任何图片，采用CSS3新技术，动态显示“data-attributes”存储的标题信息；</li>
<li>
		提示面板（Popover）：在Tooltips的插件上扩展，用来显示一些叠加内容的提示效果，此插件需要配合Tooltips一起使用。</li>
</ol><p>Bootstrap 2在前面6种插件的基础上又新增加了6种jQuery插件：</p>
<ol><li>
		警报信息（Alert）：用来关闭警报信息块；</li>
<li>
		按钮（Button）：用来控制按钮的状态或更多组件功能，比如“复选框”、“单选按钮”以及“载入状态条”等；</li>
<li>
		手风琴（Collapse）：一款轻巧实用的手风琴插件，可以用来制作折叠面板或菜单等效果；</li>
<li>
		幻灯片（Carouse）：实现图片播放功能的插件；</li>
<li>
		补全文本（Typeahead）：可以记住文本框输入的文本，下次输入时可以自动补全；</li>
<li>
		动画效果（Transitions）：BootStrap使用这个插件，为一些动效果增加了过渡性，使动画效果更细腻、生动。</li>
</ol><p>上面简单介绍了Bootstrap 2中的jQuery插件，至于如何使用，还需要根据Bootstrap所提供的文档，以及各插件的参数，具体问题具体分析。因为只有充分了解，才能灵活运用。</p>
<h4>
	变量编译Less</h4>
<p>Less 是一个动态CSS语言框架，基于自己的JS引擎或者服务器端对传统的CSS进行了动态的扩展，使得Less具有更强大的功能和灵活性。基于Less，我们 编辑CSS就可以像使用编程语言一样，定义变量、嵌入声明、混合模式、运算等。Bootstrap中有一套编辑好的Less框架，开发者可以将其应用到自 己的项目，也可以通过less.js、Less.app或Node.js等方法来转译Less文件。Less文件一旦编译，Bootstrap框架就仅包 含CSS样式，这意味着没有多余的图片、Flash之类元素。Less好处是利于编写，但缺点是只适合用于开发一些简洁的CSS样式。举个简单的例子，如 果你想为多个样式设置同一个颜色，可以这样写。</p>
<p><img alt="" height="142" src="http://www.w3cplus.com/sites/default/files/styles/print_image/public/blogs/bootstrap/bootstrap-8.jpg" style="border:1px solid #ccc;padding:2px" title="代码1" width="177"></p>
<p>编译后的CSS如下所示：</p>
<p><img alt="" height="142" src="http://www.w3cplus.com/sites/default/files/styles/print_image/public/blogs/bootstrap/bootstrap-9.jpg" style="border:1px solid #ccc;padding:2px" title="代码2" width="177"></p>
<p>以上代码只是最简的Less应用示范。除了变量，还可以在CSS中进行数学运算、传参、文件相互引用、规则嵌套等。可以想象，这种编写CSS的方式会为前端开发带来多大的改变。无论是从灵活性、扩展性还是可维护性上，Less都让CSS开发效率有了大幅提升。</p>
<h4>
	Bootstrap的jQuery UI</h4>
<p>Bootstrap的jQuery UI其实是从框架中衍生出来的一个jQuery UI主题，受到Twitter项目的启发，Addy Osmani也在Bootstrap的基础上整理出一个jQuery UI Bootstrap主题。</p>
<p>jQuery UI Bootstrap除了包含Bootstrap各个方面功能之外，还在其基础上补充了以下特性：动态添加Tabs、日期范围选择组件、自定义文件载入框、滑动块、日期控件。</p>
<h4>
	BootStrap 2案例</h4>
<p>通过前面的介绍，你可能已经体会到Bootstrap的强大，但能否运用到生产项目中，可能还存有疑问。</p>
<p>目 前使用Bootstrap的著名案例有NASA和MSNBC的Breaking News。此外很多CMS也在运用Bootstrap框架，比如大家熟悉的WordPress、Drupal等。如果你还想了解更多Bootstrap案 例，可以参考Wrapbootstrap.com。</p>
<h3 style="font-size:24px;color:green">
	BootStrap 2的优势和缺点</h3>
<p>如今的Bootstrap已包括了几十个组件，每个组件都自然地结合了设计与开发，具有完整的实例文档，定义了真正的组件和模板。无论处在何种技术水平的开发者，也无论处在哪个工作流程中，都可以使用Bootstrap快速、方便地构建开发者喜欢的应用。</p>
<p>难能可贵的是，Bootstrap依旧本着“并行开发”、“作为产品的风格指南”和“迎合所有的技能水平”的原则帮助开发者解决实际问题，不断完善自己，吸引更多人选择Bootstrap应用于自己的项目中。</p>
<p>然 而古人云“万物相生相克”，有好就有坏，Bootstrap也是一样。对于在国内的开发者来说，最可怕的就是IE兼容问题。目前Bootstrap对 IE6到IE8的支持都不友好。另一个缺点是，采用Bootstrap的模板，网站结构时常会显得臃肿。此外，覆盖一些样式时会造成代码冗余。但与其他前 端框架相比，我个人觉得Bootstrap的缺点仅此而以，至于其他方面希望有机会与大家一起探讨和学习。</p>
<h3 style="font-size:24px;color:green">
	总结</h3>
<p>Bootstrap是一套前端开发利器。它可以帮助我们加速项目开发，让我们身处在一个完备的系统中，拥有一致的设计和实现方法。不需要在外观上花费过多时间，使开发者能将精力集中于更重要的功能。</p>
<p>Bootstrap将改变我们的合作方式与开发进程，任何人都可以基于Bootstrap建立可扩展的前端工具包，或者在它的基础上启动属于自己的框架。</p>
<p>最 后在次感谢《程序员》责任编辑——<span style="font-family:arial,sans-serif;font-size:14px"><a href="http://lispython.com">卢鸫翔</a>老师，如果大家对这个感谢兴趣可以到<a href="https://speakerdeck.com/w3cplus/twitter-bootstrap-qian-duan-kuang-jia-li-qi">这里</a>下载PDF文档。</span></p>
<p><font face="arial, sans-serif"><span style="font-size:14px">如需转载，烦请注明出处：<a href="http://www.w3cplus.com/css/twitter-bootstrap.html">http://www.w3cplus.com/</a></span></font><a href="http://www.w3cplus.com/css/twitter-bootstrap.html">css/twitter-bootstrap.html</a></p>
</div></div></div><div><ul><li><a href="http://www.w3cplus.com/blog/tags/8.html">css</a></li></ul></div><div><ul><li><a href="http://www.w3cplus.com/blog/tags/126.html">bootstrap</a></li></ul></div><div><div><div><div>
      <div>8</div>
                  <a href="http://www.w3cplus.com/vote/node/515/1/vote/alternate/dOuxj4npv7M7oi5wafUVnmBiAa-2PD88AJx-Mqq72z8/nojs" rel="nofollow">
                <div title="Vote up!"></div>
          <div>Vote up!</div>
              </a>
                </div>
</div></div></div><img src="http://www1.feedsky.com/t1/691023147/W3CPlus/feedsky/s.gif?r=http://www.w3cplus.com/css/twitter-bootstrap.html" border="0" height="0" width="0">