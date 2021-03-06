---
layout: post
title:  "开发Once Touch的一些感想和经验小结"
date:   2012-11-24 16:00:00
author: 
categories: program
---

## 开发Once Touch的一些感想和经验小结
### by 
### at 2012-11-24 16:00:00
### original <http://blog.leezhong.com/tech/2012/11/24/once-touch-conclude.html>

<p>Once Touch是我开发的第一款iOS应用，这也延续了我每换一家公司就换一门语言的「优良」传统：ActionScript(时光网)-&gt;PHP(凤凰网)-&gt;Python(知乎)-&gt;Objective-C(Legend33)。希望这次是最后的转型。</p>

<p>简单说说为什么选择iOS平台。主要有以下几个原因：</p>

<h3>用户基数</h3>

<p>iPhone和iPad的总销量都是上亿的规模，所以不用担心会是个小众平台，但你会说Android的占有量比iOS要高得多，这就引出下一个原因：消费体验和消费习惯</p>

<h3>消费体验</h3>

<p>关联信用卡，点击两下按钮，输入密码就能完成了一次购买，简单几步，钱就流到了你的卡里，只要你的app足够优秀，足够吸引人。当然了，还要交给老大30%的地摊费。</p>

<h3>消费习惯</h3>

<p>虽然在国内越狱的iPhone还是呈现一统江湖状，但还是有那么一部分人愿意为优秀的app买单，而且随着支持国内的银行卡、系统升级、Gift Card等原因，会有越来越多的人加入支付队伍。如果把眼光放在全球，就更不用纠结国内的悲惨现状。</p>

<h3>产品品质</h3>

<p>尽管随着乔老爷的离去，苹果的产品开始被更多的人所诟病（当然我也在其中），但不可否认，它依旧是最好的产品，至少在我看来如此。给她一点时间去适应，去试错、去磨合，去找到自己的节奏，相信在Android的鞭策下，她还会带给我们更多的惊喜。</p>

<h3>开发体验</h3>

<p>第一次打开Xcode时，就喜欢上了这个界面，心想，以后如果能用它开发app会是件多么愉快的事情啊。Objective-C虽然需要花点时间去适应它那有点别扭的语法，但过了适应期后，这种障碍就不存在了。Xcode虽然偶尔会崩溃、出现莫名的错误、查文档很慢，基本上还是很称职的，在2010 Air上能有这样的表现我已经很满足了。</p>

<h3>进入门槛</h3>

<p>相比web开发，iOS的进入门槛明显要高不少：中文资料偏少、需要一套苹果的开发设备、有一定的编程经验。所以搞iOS开发的不少都是从其他语言转过来的。如果一项技能不好学，但学会了又很有用，你愿不愿意花时间去攻克它？</p>

<hr>

<p>我的iOS学习之路，主要是这几个工具：<a href="http://www.amazon.com/Programming-Objective-C-Edition-Developers-Library/dp/0321811909">Programming in Objective-C</a>、<a href="https://itunes.apple.com/itunes-u/ipad-iphone-application-development/id473757255?mt=10">Stanford iPad and iPhone Application Development</a>、<a href="http://www.stackoverflow.com">StackOverflow</a>。接下来就是不断地练习了。Once Touch的开发用到了Cocos2d框架，所以还看了<a href="http://www.amazon.com/Learn-cocos2d-Game-Development-Apress/dp/1430238135/">learn cocos2d game development with ios 5</a>这本书，了解基本的概念和使用。</p>

<p>开发Once Touch第一版的时候，架构想得不多，主要是以实现为主。但是跟所有项目一样，唯一不变的就是变化。在那个体系下要增加点变化会比较麻烦，常常是牵一发而动全身。即使这样，只要时间足够，这些变化还是可以被搞定的，所以国内的互联网公司常常需要很多人，因为低质量的代码需要更多的人去维护，而且周期也会变长，自然也就不敏捷了。</p>

<p>后来要开发universal版了，iPad、iPhone5自然也都要支持，如果再在现有的代码上进行调整，我觉得对自己太残忍，以后也不好意思开源，索性就重新再写一遍吧。</p>

<p>这次重写的指导方针就是模块化编程，也就是多用组合，少用继承。比如Once Touch里的小球，可以移动、缩放，就可以把移动和缩放单独提出来作为两个模块，小球再通过addChild的方式把这两个模块添加进来，这样小球就可以移动和缩放了。</p>

<h3>为什么要多用组合，少用继承</h3>

<p>先来看两张图片，第一张是国际空间站</p>

<p><img src="http://blog.leezhong.com/image/space-station.jpg" alt="image"></p>

<p>再来看看拆解图</p>

<p><img src="http://blog.leezhong.com/image/space-station-parts.jpg" alt="image"></p>

<p>看出模块的好处了吧，只要接口是对的，且功能也正常，这个模块是非洲产的，还是美国造的，都不care。也就是说空间站完全不关心每个模块的内部实现。每个模块只要完成自己的功能就行了，出了问题，也只需要调整某一个模块，而不用担心会引发其他模块功能的异常。这其实就是OO设计里的SOLID原则。</p>

<h3>Xcode的文件管理</h3>

<p>之前的习惯是把文件都交给Xcode来管理，在Xcode里新建Group，因为Xcode并不会在新建Group时在文件系统也新建文件夹，所以在Project在Xcode看起来挺整洁，到了文件系统就又是另一回事了。那能不能两全其美呢，当然是可以的，稍微改变下流程就行。不要在Xcode里新建Group，先在文件系统中建立好文件夹，然后把文件夹拖到Xcode的目录中就可以了，以后在该Group下新建的文件都会出现在文件系统中对应的文件夹内。</p>

<p><img src="http://blog.leezhong.com/image/xcode-finder.png" alt="image"></p>

<h3>目录结构</h3>

<p>从上面的图中可以看到我的目录结果，简单说明一下：</p>

<h4>Utils</h4>

<p>这里放一些与应用无关的帮助类文件，如Global.h / Helper.h / Macro.h等等。</p>

<h4>Components</h4>

<p>这是重头戏，所有的模块都会被放到这里。</p>

<h4>Vendors</h4>

<p>这里放一些第三方的类库，如MagicalRecord / CCUIViewWrapper等等。</p>

<h4>Models</h4>

<p>与数据打交道的Model都会放到这里</p>

<h4>Config</h4>

<p>应用的默认设置，主要是一个plist文件</p>

<h4>Textures</h4>

<p>这是一个比较特殊的文件夹，不是Group，而是Folder，也就是走的文件系统。这么做的原因是，把iPad和iPhone需要用到的资源分开存储（分别放到iPad和iPhone文件夹里），按需载入。</p>

<h3>模块的粒度</h3>

<p>以这个画面为例</p>

<p><img src="http://blog.leezhong.com/image/once-touch-popup.jpg" alt="image"></p>

<p>应该把星星作为一个模块，还是把3个星星作为一个模块？分数有没有必要作为一个模块？左边黄色的那个圈呢？</p>

<p>如果模块的粒度划分过细，那么组装的过程就会很痛苦。想像一下某个MM拎着一堆电脑零件来找你组装台电脑，你信心满满地告诉她：几分钟搞定。等你打开箱子后发现，风扇、硬盘神马的都是以最小单位存在，还得先把这些附件先组装起来，看你崩不崩溃。</p>

<p>如果粒度划分过粗，就失去了模块的意义，基本上也无法复用了。一辆雪佛莱，如果说还能作为某个更大实体的零部件的话，估计也就只有擎天柱了。</p>

<p>所以恰当的粒度划分才会带来生产力的提升。还是以上面这张图为例，星星、钻石、分数和左边的圆，如果其他地方很少会单独使用到这些实体，就可以整体作为一个模块。当然如果将来其他模块会用到其中的某一部分，就需要重新拆分了。</p>

<h3>模块与母体之间的通信</h3>

<p>首先，模块之间是互盲的，A模块不需要知道B模块的存在，更不能依赖B模块，这样才能解耦(话虽如此，实际上经常会自然不自然地与某个模块发生耦合)。模块只需要提供接口供母体使用就行了。当然在特定事件发生时，还需要通知母体，这就需要用到delegate。有delegate，自然也少不了protocol。以小球为例，小球本身是一个模块，该模块又有移动和缩放两个子模块。</p>

<h4>MovementComponent</h4>

<div><pre><code><span>@interface</span> <span>MovementComponent</span> : <span>BaseComponent</span> <span>{</span>
    <span>float</span> <span>_radian</span><span>;</span> <span>// 小球的半径</span>
    <span>float</span> <span>_delay</span><span>;</span> <span>// 小球是否需要延迟运动</span>
    <span>BOOL</span> <span>_isOutSide</span><span>;</span> <span>// 小球是否在外面，Challenge/Endless模式会用到</span>
<span>}</span>

<span>@property</span> <span>(</span><span>nonatomic</span><span>)</span> <span>float</span> <span>speed</span><span>;</span> <span>// 小球的移动速度</span>
<span>@property</span> <span>float</span> <span>delay</span><span>;</span> <span>// 延迟时间</span>
<span>@property</span> <span>(</span><span>nonatomic</span><span>)</span> <span>BOOL</span> <span>isOutSide</span><span>;</span> <span>// 是否在外面</span>

<span>-</span> <span>(</span><span>void</span><span>)</span><span>move</span><span>;</span> <span>// 开始运动</span>
<span>-</span> <span>(</span><span>void</span><span>)</span><span>stopMove</span><span>;</span> <span>// 停止运动</span>
</code></pre></div>


<h4>ZoomComponent</h4>

<div><pre><code><span>@class</span> <span>ZoomComponent</span>;

<span>@protocol</span> <span>ZoomComponentProtocol</span> <span>&lt;</span><span>NSObject</span><span>&gt;</span>

<span>// 当小球是最后一个球时，也就是得到perfect后，会有一个覆盖全屏的缩放</span>
<span>// 等到缩放完成后需要通知delegate</span>
<span>-</span> <span>(</span><span>void</span><span>)</span><span>zoomComponentDidGiantZoomOut</span><span>:</span><span>(</span><span>ZoomComponent</span> <span>*</span><span>)</span><span>zoomComponent</span><span>;</span>

<span>@optional</span>
<span>// 小球从最大缩放到最小后，需要通知delegate</span>
<span>-</span> <span>(</span><span>void</span><span>)</span><span>zoomComponentDidZoomOut</span><span>:</span><span>(</span><span>ZoomComponent</span> <span>*</span><span>)</span><span>zoomComponent</span><span>;</span>

<span>@end</span>

<span>@interface</span> <span>ZoomComponent</span> : <span>BaseComponent</span> <span>{</span>
    <span>BOOL</span> <span>_pauseZoomOut</span><span>;</span> <span>// 在Endless模式，最后一个球是不消失的，所以需要停止缩小</span>
<span>}</span>

<span>-</span> <span>(</span><span>void</span><span>)</span><span>reInit</span><span>;</span> <span>// 重新初始化，对已有小球的重复利用，用于Challenge/Endless模式</span>
<span>-</span> <span>(</span><span>void</span><span>)</span><span>zoomIn</span><span>;</span> <span>// 放大</span>
<span>-</span> <span>(</span><span>void</span><span>)</span><span>pauseZoomOut</span><span>;</span> <span>// 停止缩小</span>
<span>-</span> <span>(</span><span>void</span><span>)</span><span>resumeZoomOut</span><span>;</span> <span>// 可以缩小</span>
<span>-</span> <span>(</span><span>void</span><span>)</span><span>giantZoomIn</span><span>;</span> <span>// 全屏放大</span>

<span>@property</span> <span>(</span><span>nonatomic</span><span>)</span> <span>BOOL</span> <span>isSmall</span><span>;</span> <span>// 当前是否为小球状态</span>
<span>@property</span> <span>(</span><span>nonatomic</span><span>)</span> <span>BOOL</span> <span>isBig</span><span>;</span> <span>// 当前是否为大球状态</span>
<span>@property</span> <span>(</span><span>nonatomic</span><span>)</span> <span>BOOL</span> <span>isZoomIn</span><span>;</span> <span>// 是否开始放大</span>
<span>@property</span> <span>(</span><span>nonatomic</span><span>)</span> <span>BOOL</span> <span>isZoomOut</span><span>;</span> <span>// 是否开始缩小</span>
<span>@property</span> <span>(</span><span>nonatomic</span><span>)</span> <span>BOOL</span> <span>finishedZoom</span><span>;</span> <span>// 是否已经完成了缩放整个流程</span>
<span>@property</span> <span>(</span><span>nonatomic</span><span>)</span> <span>float</span> <span>zoomDuration</span><span>;</span> <span>// 放大的时间</span>
<span>@property</span> <span>(</span><span>nonatomic</span><span>)</span> <span>float</span> <span>bigDuration</span><span>;</span> <span>// 大球状态停留的时间</span>
<span>@property</span> <span>(</span><span>nonatomic</span><span>)</span> <span>float</span> <span>bigScale</span><span>;</span> <span>// 放大到多少倍</span>
<span>@property</span> <span>(</span><span>nonatomic</span><span>,</span> <span>weak</span><span>)</span> <span>id</span><span>&lt;</span><span>ZoomComponentProtocol</span><span>&gt;</span> <span>delegate</span><span>;</span> <span>// 母体</span>
<span>@end</span>
</code></pre></div>


<p>ZoomComponent里有一个protocol和一个delegate，如果某个object想要添加ZoomComponent，则必须实现ZoomComponentProtocol协议，并且把自己设置为ZoomComponent的delegate，这样在相应的事件发生时，会自动触发协议里的几个方法。因为是小球添加了MovementComponent和ZoomComponent，所以BallComponent需要实现ZoomComponentProtocol</p>

<h4>BallComponent</h4>

<div><pre><code><span>#import &quot;ZoomComponent.h&quot;</span>

<span>@interface</span> <span>BallComponent</span> : <span>BaseComponent</span><span>&lt;</span><span>ZoomComponentProtocal</span><span>&gt;</span><span>{</span>
    <span>int</span> <span>_index</span><span>;</span>
    <span>BOOL</span> <span>_isMasterBall</span><span>;</span>
    <span>ZoomComponent</span> <span>*</span><span>_zoomComponent</span><span>;</span>
    <span>MovementComponent</span> <span>*</span><span>_movementComponent</span><span>;</span>
    <span>CCSprite</span> <span>*</span><span>_ballSprite</span><span>;</span>
<span>}</span>
</code></pre></div>


<p>这里又有问题了，Layer添加了BallComponent后，需要调用Ball的一些方法和属性，如isBig / move 等等，对于Ball来说，其实就是调用Zoom和Movement对应的方法，如ballComponent.isBig其实就是zoomComponent.isBig。那能不能对外暴露zoomComponent和movementComponent呢，如ball.zoomComponent.isBig 或 [ball.movementComponent move]。这样不好，前面说过了，调用方不应该知道模块的内部实现，因为模块的内部是有可能变动的。所以，就只能进行二次封装，类似下面：</p>

<div><pre><code><span>-</span> <span>(</span><span>void</span><span>)</span><span>setBigDuration:</span><span>(</span><span>float</span><span>)</span><span>bigDuration</span>
<span>{</span>
    <span>_zoomComponent</span><span>.</span><span>bigDuration</span> <span>=</span> <span>bigDuration</span><span>;</span>
<span>}</span>

<span>-</span> <span>(</span><span>void</span><span>)</span><span>setBigScale:</span><span>(</span><span>float</span><span>)</span><span>bigScale</span>
<span>{</span>
    <span>_zoomComponent</span><span>.</span><span>bigScale</span> <span>=</span> <span>bigScale</span><span>;</span>
<span>}</span>

<span>-</span> <span>(</span><span>void</span><span>)</span><span>setSpeed:</span><span>(</span><span>float</span><span>)</span><span>speed</span>
<span>{</span>
    <span>_movementComponent</span><span>.</span><span>speed</span> <span>=</span> <span>speed</span><span>;</span>
<span>}</span>

<span>-</span> <span>(</span><span>void</span><span>)</span><span>setDelay:</span><span>(</span><span>float</span><span>)</span><span>delay</span>
<span>{</span>
    <span>_movementComponent</span><span>.</span><span>delay</span> <span>=</span> <span>delay</span><span>;</span>
<span>}</span>
</code></pre></div>


<p>初看起来会觉得挺累赘，但是从长远考虑这么做是值得的。显性好于隐性，这样能一下知道ball模块对外暴露了哪些方法。</p>

<p>添加模块和设置delegate的过程大体如下：</p>

<div><pre><code><span>_zoomComponent</span> <span>=</span> <span>[</span><span>ZoomComponent</span> <span>node</span><span>];</span>
<span>_zoomComponent</span><span>.</span><span>bigDuration</span> <span>=</span> <span>bigDuration</span><span>;</span>
<span>_zoomComponent</span><span>.</span><span>bigScale</span> <span>=</span> <span>bigScale</span><span>;</span>
<span>_zoomComponent</span><span>.</span><span>delegate</span> <span>=</span> <span>self</span><span>;</span>
<span>[</span><span>self</span> <span>addChild</span><span>:</span><span>_zoomComponent</span><span>];</span>
</code></pre></div>


<h3>小结</h3>

<p>使用继承会使架构变得复杂，为了保证每个类都只有自己需要的方法，需要很小心地定义public / protected / private方法，尤其是对父类地改动更需小心，这种架构很难应对多变的环境。</p>

<p>使用组合可以让每一个模块专注于自己地功能，按需使用，模块的调整只要不影响接口，爱怎么折腾就怎么折腾，提升了自由度。</p>

<p>不过组合也是有副作用的，尤其是涉及到子模块，比如上面的BallComponent，需要重新定义一遍接口，而继承的话就没有这个问题。有一些公共的属性和方法也可以通过继承来实现，比如所有的Component都继承自BaseComponent。</p>

<p>所以尽量多用组合，少用继承。</p>

<p>一些我觉得还不错的资源：</p>

<ul>
<li><a href="http://www.slideshare.net/nzakas/scalable-javascript-application-architecture">Scalable JavaScript Application Architecture</a></li>
<li><a href="http://stackoverflow.com/questions/49002/prefer-composition-over-inheritance">Prefer composition over inheritance?</a></li>
<li><a href="http://programmers.stackexchange.com/q/65179">Where does this concept of “favor composition over inheritance” come from?</a></li>
</ul>