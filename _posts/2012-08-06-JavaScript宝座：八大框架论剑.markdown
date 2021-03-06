---
layout: post
title:  "JavaScript宝座：八大框架论剑"
date:   2012-08-06 07:59:47
author: 齐哲
categories: program
---

## JavaScript宝座：八大框架论剑
### by 齐哲
### at 2012-08-06 07:59:47
### original <http://blog.jobbole.com/24878/?utm_source=rss&utm_medium=rss&utm_campaign=javascript%25e5%25ae%259d%25e5%25ba%25a7%25ef%25bc%259a%25e4%25b8%2583%25e5%25a4%25a7%25e6%25a1%2586%25e6%259e%25b6%25e8%25ae%25ba%25e5%2589%2591>

<p>一周前，<a href="http://throneofjs.com/">Throne of JS大会</a>在多伦多召开，这应该是我参加过的最有料也最不一样的一次大会。大会官网如是说：</p>
<p><span style="color:#888888">加载整个页面，然后再“渐进增强”以添加动态行为，这种构建Web应用的方式已经不够好了。要想让应用加载快，反应灵敏，而且又引领潮流，必须彻底检讨你的开发手段。</span></p>
<p>这次大会邀请了八大JavaScript框架/库的创建人，他们济济一堂，面对面交流各自的技术理念。所谓八大框架/库分别是：AngularJS、Backbone、Batman、CanJS、Ember、Meteor、Knockout、Spine。<sup><a href="http://blog.stevensanderson.com/2012/08/01/rich-javascript-applications-the-seven-frameworks-throne-of-js-2012/">1</a></sup></p>
<p><strong>声明：</strong>我在会上讲Knockout，因此我的观点显然不是中立的。在这篇文章中，我重点讨论这些创建人的思路和技术理念，尽量不提我赞成或反对什么。</p>
<p><sup><a href="http://blog.stevensanderson.com/2012/08/01/rich-javascript-applications-the-seven-frameworks-throne-of-js-2012/">1</a></sup> <em>没错，是8个框架，不是7个。但到底怎么回事儿，会议主办方也没有明确给我们解释过……</em></p>
<p style="text-align:center"><a href="http://blog.jobbole.com/wp-content/uploads/2012/08/Rich-JavaScript-Applications-%E2%80%93-the-Seven-Frameworks-3.jpg" rel="lightbox[24878]" title="JavaScript宝座：七大框架论剑"><img title="JavaScript宝座：七大框架论剑" src="http://blog.jobbole.com/wp-content/uploads/2012/08/Rich-JavaScript-Applications-%E2%80%93-the-Seven-Frameworks-3.jpg" alt="JavaScript宝座：七大框架论剑" width="560" height="305"></a></p>
<p><strong>文章可长啦，先概述一下：</strong></p>
<p>●对许多Web开发人员来说，要构建富Web应用，使用客户端框架是理所当然的。如果你什么框架也没用，那要么你不是在做应用，要么就会错过很多好东西。</p>
<p>●在使用方法上，这些框架很多地方都是一致的（模型-视图-*架构、声明绑定，等等——详见下文） ，因此从某种意义讲，无论你选择哪一个，都能得到同样的好处。</p>
<p>●理念上还是有不少差异，特别是在对框架和库的看法上，分歧格外大。你的选择会深刻影响你的架构。</p>
<p>●会议本身活泼，新颖，技术小组之间有很多交流和对话。我希望能有更多类似的会议。</p>
<p><strong>技术：共识与分歧</strong></p>
<p>随着每个SPA（Single Page Application，单页应用）技术的逐一展示，一些相当明显的相似性和差异性浮出了水面。</p>
<p><strong>共识：渐进增强不能建立真正的应用</strong></p>
<p>各技术门派一致认为，真正的JavaScript应用必须有适当的数据模型，并具备客户端渲染能力，而绝不仅仅是服务器处理数据再加上一些Ajax和jQuery代码那么简单。</p>
<p>用Backbone创建人Jeremy Ashkenas的话说：“现如今，你说‘单页应用’，都跟说‘不用马拉的车’差不多了”（意思是，早已经没那么新鲜了）。</p>
<p><em>“不用马拉的车”（horseless carriage）是汽车刚刚发明的时候，人们对它的称呼。——译者注</em></p>
<p><strong>共识：模型-视图-某某</strong></p>
<p>所有技术门派都坚持模型－视图分离。有的强调MVC（Model View Control），有的提到MVVM（Model View ViewModel），甚至有人拒绝明确说出第三个词儿（只提模型、视图，然后加上让它们协调运作的东西）。对各门派而言，最终结果其实是相似的。</p>
<p><strong>共识：推崇数据绑定</strong></p>
<p>除了Backbone和Spine之外，其他框架都在自己的视图里内置了声明数据绑定的机制（Backbone的设计理念强调让用户“自选视图技术”）。</p>
<p><strong>共识：IE6已死</strong></p>
<p>在小组讨论中，大多数框架的创建者说，他们对IE<span><a href="http://blog.jobbole.com/12749/" title="浏览器">浏览器</a></span>的支持只限于7+（事实上，Ember和AngularJS的起点是IE8，Batman需要ES5“垫片”才能在IE9之前的IE版本中使用）。这也是大势所趋：<a href="http://blog.jquery.com/2012/06/28/jquery-core-version-1-9-and-beyond/">jQuery 2已经不打算支持IE9以下的旧版本IE了</a>。</p>
<p>只有Backbone和Knockout还坚定支持IE6+（我不清楚Backbone的内部实现，但Knockout会把IE6/7那些令人抓狂的渲染及事件方面的怪异行为屏蔽掉）。</p>
<p><strong>共识：许可和源代码控制</strong></p>
<p>大家都使用MIT许可，并且托管在<span><a href="http://blog.jobbole.com/6492/" title="GitHub如何运作：时间并不决定一切">GitHub</a></span>上。</p>
<p><strong>分歧：库与框架</strong></p>
<p>这是目前最大的分歧。下表对JavaScript库和框架进行了归类：</p>
<p style="text-align:center"><a href="http://blog.jobbole.com/wp-content/uploads/2012/08/Rich-JavaScript-Applications-%E2%80%93-the-Seven-Frameworks-1.png" rel="lightbox[24878]" title="JavaScript宝座：七大框架论剑"><img title="JavaScript宝座：七大框架论剑" src="http://blog.jobbole.com/wp-content/uploads/2012/08/Rich-JavaScript-Applications-%E2%80%93-the-Seven-Frameworks-1.png" alt="JavaScript宝座：七大框架论剑" width="572" height="145"></a></p>
<p><em>*括号中的数字是最近某个时间点GitHub上的关注者数量，粗略地代表各自的<span><a href="http://www.amazon.cn/gp/product/B0044KME2E/ref=as_li_qf_sp_asin_il_tl?ie=UTF8&amp;tag=vastwork-23&amp;linkCode=as2&amp;camp=536&amp;creative=3200&amp;creativeASIN=B0044KME2E" title="影响力" rel="nofollow">影响力</a></span>。</em></p>
<p><strong>什么意思呢？</strong></p>
<p><strong></strong><strong>●JavaScript库</strong>，插到既有架构中，补充特定功能。</p>
<p><strong>●JavaScript框架</strong>，提供一个架构（文件结构啊，等等），你必须遵守它，只要你遵守，那剩下的就全都是处理通用需求了。</p>
<p>目前来看，鼓吹框架模型最卖力气的是Ember，其创建人Yehuda Katz之前是（理念相似的）Rails和SproutCore项目的开发者。他的观点是，缺少任何组件都不够给力，都不能说是真正在推动技术进步。相反的观点说，库的目的更明确，因而更容易掌握、采用、定制，也有助于把项目风险降到最低，毕竟你的架构不会严重依赖任何一个外部项目。根据我参加对话的情况看，现场观众也分成了两派，有支持框架的，也有支持库的。</p>
<p>请注意，AngularJS可以说是介于库和框架之间一种形态：它不要求开发时遵守特定的文件组织方式（与库类似），但在运行时，它提供一个“应用生命周期”，可以对号入座地把代码安排进去（与框架类似）。之所以把它归入框架之列，是因为AngularJS团队乐于接受这个说法。</p>
<p><strong>分歧：灵活，还是整合</strong></p>
<p>每个技术门派都有不同程度的强制性规定：</p>
<p style="text-align:center"><a href="http://blog.jobbole.com/wp-content/uploads/2012/08/Rich-JavaScript-Applications-%E2%80%93-the-Seven-Frameworks-2.png" rel="lightbox[24878]" title="JavaScript宝座：七大框架论剑"><img title="JavaScript宝座：七大框架论剑" src="http://blog.jobbole.com/wp-content/uploads/2012/08/Rich-JavaScript-Applications-%E2%80%93-the-Seven-Frameworks-2.png" alt="JavaScript宝座：七大框架论剑" width="601" height="312"></a></p>
<p> </p>
<p>不难想见，只要某个库在某方面是开放的，他们的人就会强调只有这样才能从总体上确保跟第三方库兼容。同样，显而易见的反对意见则是，只有内置才能保证无缝整合。再次，根据我参加的对话，现场观众也各持己见，说什么的都有，基本上可以看出每个人对其他技术组合的了解程度。</p>
<p>Ember的Tom Dale说：“我们加入了很多魔法，但都是有用的魔法，换句话说，它们可以分解为常规的操作原语。”</p>
<p><strong>分歧：基于字符串的模板与基于DOM的模板</strong></p>
<p>（请参考上面的表格。）对基于字符串的模板，大家几乎都选择Handlebars.js作为模板引擎，它俨然成了这个领域的霸主，当然CanJS用的是EJS。对基于字符串的模板，支持的人认为“它更快”（不一定），而且“理论上，服务器也可以处理它”（也不一定，因为前提必须是在服务器上运行所有模型代码，而实践中根本没人那么做）。</p>
<p>而基于DOM的模板呢，意味着纯粹通过在实际标记中绑定来控制流程（each、if，等等），且不依赖任何外部模板库。支持的声音有“它更快”（不一定），另外“代码易读、易写，且标记与模板之间没有隔阂，CSS如何与之交互也一目了然。”</p>
<p>在我看来，最有吸引力的说法来自AngularJS那帮家伙，他们认为在不久的将来，基于DOM的模板会得到浏览器原生支持。所以我们最好现在就用，从而可以轻松应对未来。AngularJS来自Google，所以他们在开发Chromium时会考虑这一点，而且也会说服标准主体接纳这个建议。</p>
<p><strong>分歧：服务器中立到什么程度</strong></p>
<p>Batman和Meteor明显依赖服务器：Batman是为Rails设计的，而Meteor本身就是服务器。其他大多数都追求服务器中立。但实际上，Ember的架构、强制性规定，以及某些工具都倾向于Rails开发者。当然，Ember绝对也能跟其他服务器技术搭配，只不过眼下还需要较多手工配置。</p>
<p><strong>技术门派概览</strong></p>
<p>以下是所有JavaScript库/框架的基本技术细节。</p>
<p><strong>Backbone</strong></p>
<p><strong>●Who: </strong>Jeremy Ashkenas和DocumentCloud。</p>
<p><strong>●What: </strong></p>
<p>+ 用JavaScript实现模型-视图，MIT许可。</p>
<p>+ 只有一个文件，1000行代码，在所有库中最小！</p>
<p>+ 功能极其专一，只提供REST可持久模型及简单路由和回调（以便你知道何时渲染视图，但视图渲染机制由你自己选择）。</p>
<p>+ 名气最大，很多大牌站点都在用（也许是因为它最小，容易部署）。</p>
<p><strong>●Why:</strong></p>
<p>+ 非常小，使用它之前，你完全可以通读并理解它的源代码。</p>
<p>+ 不会影响你的服务器架构或文件组织方式。可以在页面的某一部分内运行——不需要控制整个页面。</p>
<p>+ Jeremy好像进入了一种禅宗所谓的入定的状态，对一切都能坦然接受。他就像一个大人，看着一群孩子在那里辩论。</p>
<p><strong>●Where:</strong> <a href="https://github.com/documentcloud/backbone/">GitHub</a> 及 <a href="http://backbonejs.org/">自有站点</a>。</p>
<p><strong>●When:</strong> 至今已诞生近两年了。</p>
<p><strong>Meteor</strong></p>
<p><strong></strong><strong>●Who:</strong> <a href="http://www.meteor.com/about/people">Meteor 开发团队</a>（他们<a href="http://www.prnewswire.com/news-releases/andreessen-horowitz-matrix-partners-invest-112-million-in-meteor-163687206.html">刚募集到1120万美元投资</a>，因此可以全职开发）。</p>
<p><strong>●What:</strong></p>
<p><strong></strong>+ 前瞻性极强的一个框架，想不出有谁那么激进过（也许<a href="http://derbyjs.com/">Derby</a>算一个）。</p>
<p>+ 将一个服务器端运行时环境（用Node+Mongo搭建）和一个客户端运行时环境衔接起来，让你的代码在两端都能运行，还包含数据库。利用WebSockets实现所有客户端和服务器之间的同步。</p>
<p>+ 在修改代码时就“实时部署”——客户端运行时可以即时更新而不丢失状态。</p>
<p>+ 可以<a href="http://www.meteor.com/screencast">看看这个视频</a>，对它的认识就会更全面。</p>
<p>+ 跟会上与我有过交流的所有人一样，我也衷心希望这个框架获得成功——Web开发就需要这种激进的改革才能真正进步。</p>
<p><strong>●Why:</strong> 你实在觉得做常规Web开发太无聊了，想找点刺激。</p>
<p><strong>●Where:</strong> <a href="https://github.com/meteor/meteor">GitHub</a> 和 <a href="http://www.meteor.com/">自有站点</a>。</p>
<p><strong>●When:</strong> 诞生时间不长；除了其核心团队在用，不知道还有没有其他站点实际在用Meteor。不过，这个团队真是在严肃地做着一件前无古人的事。</p>
<h3>Ember</h3>
<p><strong>●Who:</strong> Yehuda Katz （之前开发过jQuery和Rails）、Ember团队和Yehuda的公司<a href="http://www.facebook.com/tildeinc">Tilde</a>。</p>
<p><strong>●What:</strong></p>
<p>+ 构建“超级Web应用”所需的一切，MIT许可。</p>
<p>+ 功能最多，体积最大。</p>
<p>+ 融入了很多设计理念，涉及如何分解并对页面进行层次控制，以及如何利用一个状态机驱动的系统联结各个层次。</p>
<p>+ 正在开发一个功能非常完善的数据访问库（Ember.Data）。</p>
<p>+ 要在运行时控制整个页面，因此不适合开发大页面上的“富应用区”。</p>
<p>+ 对文件、URL等都有相当严格的一套约束，不过要是不喜欢，你可以重写，只要你知道怎么做就OK。</p>
<p>+ 设计灵感来自Rails和Cocoa。</p>
<p>+ 工具：为Rails提供项目模板（但如果你手工编写代码，也可以使用其他服务器端平台）。</p>
<p><strong>●Why:</strong> 常见的问题应该有通用的解决方案——Ember提供了所有通用解决方案。</p>
<p><strong>●Where:</strong> <a href="https://github.com/emberjs/ember.js/">GitHub</a> 和 <a href="http://emberjs.com/">自有站点</a>。</p>
<p><strong>●When:</strong> 尚未发布1.0版，但也快了。然后，API基本就能稳定下来。</p>
<p><strong>●AngularJS</strong></p>
<p><strong>●Who:</strong> Google（他们内部在使用）。<br>
<strong></strong></p>
<p><strong>●What:</strong></p>
<p>+ 用JavaScript实现模型-视图-其他，MIT许可。</p>
<p>+ 基于DOM的模板，具备可观察能力、声明绑定机制，还有准MVVM式的代码风格（他们自己说是Model-View-Whatever）</p>
<p>+ 内置基本URL路由和数据持久化能力</p>
<p>+ 工具：附带一个Chrome调试器插件，让你在调试的时候能够查看模型；还附带一个Jasmine测试框架。<br>
<strong></strong></p>
<p><strong>●Why:</strong></p>
<p>+ 从概念上讲，他们说这个框架相当于一个“填料层”，盖在当前浏览器上，以实现未来的浏览器将可能原生具备的能力（即声明绑定和可观察能力）。因此，我们现在就应该着手这么来写代码了。</p>
<p>+ 对服务器架构或文件组织方式没有影响。可以用在页面的某一小部分中——不需要控制整个页面。<br>
<strong></strong></p>
<p><strong>●Where:</strong> <a href="https://github.com/angular/">GitHub</a> 和 <a href="http://angularjs.org/">自有站点</a>。<br>
<strong></strong></p>
<p><strong>●When:</strong> 成品级框架，Google已经搞出来有一段时间了。</p>
<p><strong>Knockout</strong></p>
<p><strong>●Who:</strong> Knockout 团队和社区（核心团队目前有三个人，包括我）。<br>
<strong></strong></p>
<p><strong>●What:</strong></p>
<p>+ 用JavaScript实现模型-视图-视图模型（MVVM，Model-View-ViewModel），MIT许可。</p>
<p>+ 功能集中在富用户界面元素：基于DOM的声明绑定模板，可观察的模型加自动依赖检测。</p>
<p>+ 没有限定URL路由或数据访问——可组合任意第三方库（例如，用Sammy.js做路由，用纯Ajax实现存储）。</p>
<p>+ 在降低使用门槛方面下了很大工夫，提供详尽的文档和<a href="http://learn.knockoutjs.com/">交互式示例</a>。<br>
<strong></strong></p>
<p><strong>●Why:</strong></p>
<p>+ 只做好一件事（UI），向后兼容到IE6。</p>
<p>+ 对服务器架构或文件组织方式没有影响。可以用在页面的某一小部分中——不需要控制整个页面。<br>
<strong></strong></p>
<p><strong>●Where:</strong> <a href="https://github.com/SteveSanderson/knockout">GitHub</a> 和 <a href="http://knockoutjs.com/">自有站点</a>。<br>
<strong></strong></p>
<p><strong>●When:</strong> 到现在已经正式发布近两年了。</p>
<p><strong>Spine</strong></p>
<p><strong>●Who:</strong> Alex MacCaw。<br>
<strong></strong></p>
<p><strong>●What:</strong></p>
<p>+ 用JavaScript实现MVC，MIT许可证。</p>
<p>+ 由最早为O’Reilly一本书写的示例代码发展而来，已成为一个OSS（Open Source Software，开源软件）项目。</p>
<p>+ 是Backbone的一个衍生版（看名字就知道<sup><a href="http://weibo.com/u/1906984307">3</a></sup>）。<br>
<strong></strong></p>
<p><strong>●Why:</strong> 你喜欢Backbone，但又想要点<a href="http://stackoverflow.com/questions/6530444/backbone-js-vs-spine-js">不一样的东西</a>。<br>
<strong></strong></p>
<p><strong>●Where:</strong> <a href="https://github.com/maccman/spine">GitHub</a> 和 <a href="http://spinejs.com/">自有站点</a><br>
<strong></strong></p>
<p><strong>●When:</strong> v1.0.0已经发布。</p>
<p><sup>3</sup> <em>Backbone和Spine都是“脊椎”的意思。——译者注</em></p>
<p><strong>Batman</strong></p>
<p><strong>●Who:</strong> <a href="http://www.shopify.com/">Shopify</a> （一家电子商务平台公司）的团队。<br>
<strong></strong></p>
<p><strong>●What:</strong></p>
<p>+ 在JavaScript中实现MVC，几乎是专门为Rails+CoffeeScript开发者定制的，MIT许可。<br>
+ 是所有框架中强制性规定最多的。你必须遵守其约定（例如，怎么组织文件和URL）。否则，就像他们幻灯片中说的，“你还是用其他框架吧”。<br>
+ 非常完善的框架，具有相当丰富的模型、视图和控制器，还有路由。当然，还有可观察机制。<br>
+ 基于DOM的模板。<br>
<strong></strong></p>
<p><strong>●Why:</strong> 如果你使用Rails和CoffeeScript，你找到亲人了。<br>
<strong></strong></p>
<p><strong>●Where:</strong> <a href="https://github.com/Shopify/batman">GitHub</a> 和 <a href="http://batmanjs.org/">自有站点</a>。<br>
<strong></strong></p>
<p><strong>●When:</strong> 当前版本 0.9，几个月内将发布1.0版。</p>
<p><strong>CanJS</strong></p>
<p><strong>●Who:</strong> <a href="http://bitovi.com/">Bitovi</a>（一家JavaScript咨询/培训公司）的团队。<br>
<strong></strong></p>
<p><strong>●What:</strong></p>
<p>+ 用JavaScript实现MVC，MIT许可。</p>
<p>+ REST可持久模型、基本的路由、基于字符串的模板。</p>
<p>+ 知名度不高（我也是上周才听说它的），但它的前身却是原来的<a href="http://javascriptmvc.com/">JavaScriptMVC项目</a>。<br>
<strong></strong></p>
<p><strong>●Why</strong>: 旨在集上述各技术门派之所长，提供与它们类似的功能，同时又保持体积小巧。<br>
<strong></strong></p>
<p><strong>●Where:</strong> <a href="https://github.com/jupiterjs/canjs">GitHub</a> 和 <a href="http://canjs.us/">自有站点</a>。<br>
<strong></strong></p>
<p><strong>●When:</strong> 1.0 版已经发布了。</p>
<p><strong>总结</strong></p>
<p>如果你正在考虑选型的问题，想知道上面这些框架/库中的哪一个最适合你的新项目，那我建议你重点关注以下两点。</p>
<p><strong>功能范围</strong>。你想让这个框架或库为你做多少事儿？你的项目是从头做起，因而需要一个能贯穿始终的完整的各项功能齐备的架构吗？或者，你其实更喜欢自己来挑选模式和库？对不同的项目，不同的团队，任何选择都有价值，都是正确的。<br>
<strong></strong></p>
<p><strong>设计美学</strong>。你看过它们的代码吗，用没用过自己选择的框架构建出了一些小巧的应用？你喜欢这样做吗？不要只看它们的说明或者功能列表就作出选择：那些信息有价值，但不全面。打个比方，如果你置自己主观的编码经验于不顾，那就像在选择小说时只看它有几章几节，或者在找对象时只看其简历或个人描述。</p>
<p>尽管存在分歧，但我认为所有技术门派有一个重大的共性：它们都践行了模型与视图分离的思想。而这个思想早在Web诞生之前就已存在，到现在差不多有20年历史了。这么说吧，就算你只做一个基本的Web应用的UI，在客户端应用这一思想也永远是正确的</p>
<p> </p>
<p>原文链接：<a href="http://blog.stevensanderson.com/2012/08/01/rich-javascript-applications-the-seven-frameworks-throne-of-js-2012/">Rich JavaScript Applications – the Seven Frameworks</a>　作者：Steven Sanderson</p>
<p>翻译：<a href="http://weibo.com/lisf">@李松峰</a></p>
<p> </p>
<h2>相关文章</h2><ul><li><a href="http://blog.jobbole.com/12001/" title="推荐10个出色的JavaScript动画框架">推荐10个出色的JavaScript动画框架</a></li><li><a href="http://blog.jobbole.com/1483/" title="20个值得一试的JavaScript框架">20个值得一试的JavaScript框架</a></li><li><a href="http://blog.jobbole.com/1398/" title="8个针对开发者的MooTools实用插件">8个针对开发者的MooTools实用插件</a></li><li><a href="http://blog.jobbole.com/1380/" title="JavaScript最佳开发工具集合">JavaScript最佳开发工具集合</a></li><li><a href="http://blog.jobbole.com/1288/" title="10个Web移动开发JavaScript框架">10个Web移动开发JavaScript框架</a></li><li><a href="http://blog.jobbole.com/947/" title="5个实用的Javascript框架">5个实用的Javascript框架</a></li><li><a href="http://blog.jobbole.com/24602/" title="向非程序员解释 JavaScript">向非程序员解释 JavaScript</a></li><li><a href="http://blog.jobbole.com/24480/" title="前端框架你究竟选什么">前端框架你究竟选什么</a></li><li><a href="http://blog.jobbole.com/24329/" title="了解 JavaScript 中的内置对象">了解 JavaScript 中的内置对象</a></li><li><a href="http://blog.jobbole.com/23563/" title="Javascript定义类（class）的三种方法">Javascript定义类（class）的三种方法</a></li></ul>