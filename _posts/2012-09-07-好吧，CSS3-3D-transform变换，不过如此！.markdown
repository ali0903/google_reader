---
layout: post
title:  "好吧，CSS3 3D transform变换，不过如此！"
date:   2012-09-07 01:05:18
author: 张 鑫旭
categories: program
---

## 好吧，CSS3 3D transform变换，不过如此！
### by 张 鑫旭
### at 2012-09-07 01:05:18
### original <http://www.zhangxinxu.com/wordpress/2012/09/css3-3d-transform-perspective-animate-transition/>

<p>by <a href="http://www.zhangxinxu.com/">zhangxinxu</a> from <a href="http://www.zhangxinxu.com/">http://www.zhangxinxu.com</a><br>
本文地址：<a href="http://www.zhangxinxu.com/wordpress/?p=2592">http://www.zhangxinxu.com/wordpress/?p=2592</a></p>
<h3>一、写在前面的秋裤</h3>
<p>早在去年的去年，我就大肆介绍了<a href="http://www.zhangxinxu.com/wordpress/?p=1268">2D transform相关内容</a>。看过海贼王的都知道，带D的家伙都不是好惹的，2D我辈尚可以应付，3D的话，呵呵，估计我等早就在千里之外被其霸气震晕了~~</p>
<p>看看下图女帝的动作以及神情，就可以知道名字带D的家伙的厉害！<br>
<img alt="路飞女帝D族的厉害 张鑫旭-鑫空间-鑫生活" src="http://image.zhangxinxu.com/image/blog/201209/nvdi-lufei-yamiedie.jpg" title="路飞女帝D族的厉害" width="500" height="229"></p>
<p>最近折腾iPad的一些东西，有一些3D效果的交互。有些事情，总以为是遥远的未来，谁知真正发生的时候说来就来，比如说一颗想结婚的心，又比方说在实际项目中折腾3D transform效果。</p>
<p><img alt="哭泣鑫表情 张鑫旭-鑫空间-鑫生活" src="http://image.zhangxinxu.com/image/emtion/cry.png" title="哭泣鑫表情" style="float:left" width="150" height="150"></p>
<p>然而，虽然以前折腾过<a href="http://www.zhangxinxu.com/study/201001/css3-animate-3d-effect-demo.html">3D变换效果(webkit)</a>，但都是依葫芦画瓢，囫囵吞枣，真正要轻松实现想要的3D效果，是需要深入理解的，于是，此时的自己苦逼了，泪奔ing……</p>
<p>木有办法，找资料，自己思考学习呗，当我看到下面这张基本图的时候，我的右侧的浓眉毛不由自主抖动了两下，呵，呵呵~~<br>
<img alt="3D变换坐标图解 张鑫旭-鑫空间-鑫生活" src="http://image.zhangxinxu.com/image/blog/201209/3d_axes.png" title="3D变换坐标图解" width="368" height="402"></p>
<p>这个长得像原子核一样的是什么东东？那像章鱼哥一样四处横生的箭头好吓人哦！后面怎么还有一个苍蝇拍？？ <img src="http://mat1.gtimg.com/www/mb/images/face/26.gif"> CSS好可怕，我要回去找妈妈……</p>
<p>想必大部分的同行应该跟我一样，没有爱因斯坦爷爷的智商，没有上镜需要把表摘掉的爸爸。因此，那些术语连篇的CSS3 3D transform介绍的资料过于耀眼，无法直视。怎么办？</p>
<p>好吧，佛家有云，我不入地狱谁入地狱。这里，我就从凡人们的视角说说CSS3 3D transform的一些东西，希望说的东西比较亲民，不要吓着大家。</p>
<h3>二、首先，情感化认识</h3>
<p>我觉得吧，要想理解一个东西，最好先有一些感性的认识。</p>
<p>CSS3中的3D变换效果，本质上就是我们OOXX时候各种姿势的变换，又称各种体位的变换。</p>
<p>虽然都是成年人，但考虑到仍有不少窝中待守的雏鸟，如果上面的解释想不过来，就想想以下这些：<br>
<strong>1. </strong>下图的这些人在干嘛？<br>
<img src="http://image.zhangxinxu.com/image/blog/201209/tiaoshui.jpg" height="300"></p>
<p>跳水？NO, No, No!! 记住，他们不是在跳水，是在做3D变换！！！</p>
<p><strong>2.</strong> 下图可爱baby在干嘛<img src="http://mat1.gtimg.com/www/mb/images/face/101.gif">？<br>
<img src="http://image.zhangxinxu.com/image/blog/201209/baby-move.jpg" height="300"><br>
广播体操？NO, No, No!! 记住，他不是在做操，是在做3D变换！！！</p>
<p><strong>3.</strong> 来到2次元，下图这个妹子在这幅姿态称为：<br>
<img src="http://image.zhangxinxu.com/image/blog/201209/maimeng.jpg" height="300" style="vertical-align:middle"> <img alt="鑫表情 性感 色" src="http://image.zhangxinxu.com/image/emtion/sex.png" title="鑫表情 性感 色" width="150" height="150" style="margin-left:100px;vertical-align:middle"><br>
卖萌？NO, No, No!! 记住，他不是在卖萌，是在做3D变换！！！</p>
<p>哈哈哈哈，是否意识到：在显示世界中，一切的动作（包括上面巨乳萌妹所引发的精虫上脑），都是属于3D transform变换。 因此，要学习与理解3D transform变换很简单，一句话，到现实世界找个东西映射一下即可。</p>
<h3>三、认识的突破口：rotateX, rotateY, rotateZ</h3>
<p>3D transform中有下面这三个方法：</p>
<ul>
<li><code>rotateX( angle )</code></li>
<li><code>rotateY( angle )</code></li>
<li><code>rotateZ( angle )</code></li>
</ul>
<p>理解了这三个方法，后面更难懂的<code>perspective</code>就好下手了，可以说是突破口！</p>
<p><code>rotate</code>旋转的意思，<code>rotateX</code>旋转X轴，<code>rotateY</code>旋转Y轴，<code>rotateZ</code>旋转Z轴……<img src="http://mat1.gtimg.com/www/mb/images/face/34.gif"></p>
<p>什么X轴/Y轴/Z轴，这几个词从我嘴里一出来，别说你们，我自己都晕了~~</p>
<p>赶快，从现实世界找对应东西理解（参照下面人的旋转）：<br>
邹凯的体操单杠运动是<code>rotateX</code>；<br>
<img alt="单杠" src="http://image.zhangxinxu.com/image/blog/201209/dangang.jpg" title="单杠" width="300" height="226"></p>
<p>蔡依林姐姐的钢管舞是<code>rotateY</code>；<br>
<img alt="蔡依林-钢管舞 张鑫旭-鑫空间-鑫生活" src="http://image.zhangxinxu.com/image/blog/201209/gangguan-jolin.jpg" title="蔡依林-钢管舞" width="237" height="300"></p>
<p>旋转飞刀的特技表演是<code>rotateZ</code>。<br>
<img alt="飞刀魔术" src="http://image.zhangxinxu.com/image/blog/201209/feidao.jpg" title="飞刀魔术" width="330" height="227"></p>
<p>还是理解不过来？好吧，假设你是男的，以你的女朋友举例，假如原本你和她面对面站着，然后你——<br>
从正面将其推到就是<code>rotateX</code>；<br>
<img alt="妹子推到与transform rotateX 张鑫旭-鑫空间-鑫生活" src="http://image.zhangxinxu.com/image/blog/201209/tuidao-x.jpg" title="妹子推到与transform rotateX" width="225" height="300"></p>
<p>让其原地转个90度欣赏其侧面的丰满曲线就是<code>rotateY</code>；<br>
<img alt="妹子推到与transform rotateY 张鑫旭-鑫空间-鑫生活" src="http://image.zhangxinxu.com/image/blog/201209/tuidao-y.jpg" title="妹子推到与transform rotateY" width="217" height="300"></p>
<p>把妹子抱到床上侧面躺着就是<code>rotateZ</code>。<br>
<img alt="妹子推到与transform rotateZ 张鑫旭-鑫空间-鑫生活" src="http://image.zhangxinxu.com/image/blog/201209/tuidao-z.jpg" title="妹子推到与transform rotateZ" width="300" height="188"></p>
<p>于是，下面CSS世界中的简单3D效果是不是更容易理解了呢？！<br>
<img alt="transform rotateX(45deg)的效果图 张鑫旭-鑫空间-鑫生活" src="http://image.zhangxinxu.com/image/blog/201209/2012-09-05_175406.png" title="transform rotateX(45deg)的效果图" width="318" height="230" style="vertical-align:top"> <img alt="transform rotateY(45deg)的效果图 张鑫旭-鑫空间-鑫生活" src="http://image.zhangxinxu.com/image/blog/201209/2012-09-05_175458.png" title="transform rotateY(45deg)的效果图" width="276" height="252" style="vertical-align:top"> <img alt="transform rotateZ(45deg)的效果图 张鑫旭-鑫空间-鑫生活" src="http://image.zhangxinxu.com/image/blog/201209/2012-09-05_175551.png" title="transform rotateZ(45deg)的效果图" width="311" height="298" style="vertical-align:top"></p>
<div><em>如果您看到下面的文字，可能是由于在其他网站或是RSS中阅读本文，本文原地址：<a href="http://www.zhangxinxu.com/wordpress/?p=2592">http://www.zhangxinxu.com/wordpress/?p=2592</a>，本文作者：<a href="http://www.zhangxinxu.com/">张鑫旭</a>，来自张鑫旭-鑫空间-鑫生活，访问原出处阅读体验更佳。</em></div>
<p style="color:#888">//zxx: 下面为广告~~注意不要勿点~~嘻嘻~~</p>
<div style="width:468px;padding:5px;background-color:#cad5eb">

</div>
<h3>四、必不可少的perspective属性</h3>
<p><code>perspective</code>的中文意思是：透视，视角！</p>
<p><code>perspective</code>属性的存在与否决定了你所看到的是2次元的还是3次元的，也就是是2D transform还是3D transform. 这不难理解，没有透视，不成3D.</p>
<p>我们初中学美术，或者学建筑的同学肯定接触过透视的一些东西：<br>
<img src="http://image.zhangxinxu.com/image/blog/201209/toushi.png" height="183" alt="3D透视 张鑫旭-鑫空间-鑫生活"> <img src="http://image.zhangxinxu.com/image/blog/201209/toushi2.png" height="183" alt="3D透视 张鑫旭-鑫空间-鑫生活"></p>
<p>不过，CSS3 3D transform中的透视的透视点与上面两张示例图是不同的：CSS3 3D transform的<strong>透视点是在浏览器的前方</strong>！</p>
<p>或者这么理解吧：显示器中3D效果元素的透视点在显示器的上方（不是后面），近似就是我们眼睛所在方位！</p>
<p>比方说，一个1680像素宽的显示器中有张美女图片，应用了3D transform，同时，该元素或该元素父辈元素设置的<code>perspective</code>大小为2000像素。则这张美女多呈现的3D效果就跟你本人在1.2个显示器宽度的地方(1680*1.2≈2000)看到的真实效果一致！！<br>
<img alt="1680宽度像素显示器与3D transform视角大小示意 张鑫旭-鑫空间-鑫生活" src="http://image.zhangxinxu.com/image/blog/201209/3d-distance.jpg" title="1680宽度像素显示器与3D transform视角大小示意" width="434" height="291"></p>
<h3>五、translateZ帮你寻找透视位置</h3>
<p>如果说<code>rotateX</code>/<code>rotateY</code>/<code>rotateZ</code>可以帮助理解三维坐标，则<code>translateZ</code>则可以帮你理解透视位置。</p>
<p>我们都知道近大远小的道理，对于没有<code>rotateX</code>以及<code>rotateY</code>的元素，<code>translateZ</code>的功能就是让元素在自己的眼前或近或远。比方说，我们设置元素<code>perspective</code>为201像素，如下：</p>
<div>
<pre>perspective: 201px;</pre>
</div>
<p>则其子元素，设置的<code>translateZ</code>值越小，则子元素大小越小（因为元素远去，我们眼睛看到的就会变小）；<code>translateZ</code>值越大，该元素也会越来越大，当<code>translateZ</code>值非常接近201像素，但是不超过201像素的时候（如200像素），该元素的大小就会撑满整个屏幕（如果父辈元素没有类似overflow:hidden的限制的话）。因为这个时候，子元素正好移到了你的眼睛前面，所谓“一叶蔽目，不见泰山”，就是这么回事。当<code>translateZ</code>值再变大，超过201像素的时候，该元素看不见了——这很好理解：我们是看不见眼睛后面的东西的！</p>
<p>再生动的文字描述也不如一个实例来得直观，您可以狠狠地点击这里：<a href="http://www.zhangxinxu.com/study/201209/transform-perspective-translateZ.html">translateZ方法辅助理解perspective视角demo</a></p>
<p>建议Chrome浏览器下访问，可以使用<code>range</code>控件，演示效果更赞，如下截图：-100时候最小，200时候超级满屏（垂直方向因特殊布局限制没有显示），250的时候因为元素已经在视点之外，因此是一片空白（看不见）。<br>
<img alt="translateZ -100像素最远距离最小显示 张鑫旭-鑫空间-鑫生活" src="http://image.zhangxinxu.com/image/blog/201209/2012-09-05_220540.png" title="-100像素最远距离最小显示 张鑫旭-鑫空间-鑫生活" width="366" height="292"> <img alt="translateZ 200像素时候超级大的显示" src="http://image.zhangxinxu.com/image/blog/201209/2012-09-05_220622.png" title="translateZ 200像素时候超级大的显示" width="378" height="328"> <img alt="translateZ为250像素时候元素在视区之外，因此看不见是空白 张鑫旭-鑫空间-鑫生活" src="http://image.zhangxinxu.com/image/blog/201209/2012-09-05_220711.png" title="translateZ为250像素时候元素在视区之外，因此看不见是空白" width="376" height="326"></p>
<h3>六、perspective属性的两种书写</h3>
<p><code>perspective</code>属性有两种书写形式，一种用在舞台元素上（动画元素们的共同父辈元素）；第二种就是用在当前动画元素上，与transform的其他属性写在一起。如下代码示例：</p>
<div>
<pre>.stage {
    perspective: 600px;
}</pre>
</div>
<p>以及：</p>
<div>
<pre>#stage .box {
    transform: perspective(600px) rotateY(45deg);
}</pre>
</div>
<p>您可以狠狠地点击这里：<a href="http://www.zhangxinxu.com/study/201209/transform-perspective-two-write-style.html">perspective属性的两种书写demo</a></p>
<p>结果如下缩略图：<br>
<img alt="CSS3 transform perspective两种书写形式demo效果截图 " src="http://image.zhangxinxu.com/image/blog/201209/2012-09-05_222328.png" title="CSS3 transform perspective两种书写形式demo效果截图" width="264" height="267"></p>
<p>从上图我们貌似可以看到，虽然书写的形式，属性名称不一致，但是，效果貌似是一样的~~果真是这样吗？？？</p>
<p>实际上不然，上面的demo上下两个效果之所以会一样，是因为舞台上只有一个元素，因此，发生了巧合，其正好表现一样了。如果，如果舞台上有很多个元素，则两种书写形式的表现差异就会立马显示出来了！</p>
<p>您可以狠狠地点击这里：<a href="http://www.zhangxinxu.com/study/201209/transform-perspective-same-rotate.html">舞台多元素下的perspective两种书写对比demo</a></p>
<p>demo页面效果缩略图如下（因背景色随机，可能与下图有差异）：<br>
<img alt="不同transform perspective书写下的表现差异" src="http://image.zhangxinxu.com/image/blog/201209/2012-09-05_222911.png" title="不同transform perspective书写下的表现差异" width="414" height="332"></p>
<p>好吧，图中的效果其实不难理解。上面舞台整个作为透视元素，因此，显然，我们看到的每个子元素的形体都是不一样的；而下面，每个元素都有一个自己的视点，因此，显然，因为rotateY的角度是一样的，因此，看上去的效果也就一模一样了！</p>
<p><strong>关于Chrome浏览器以及透视盲区</strong><br>
在Chrome浏览器下，要想看到完整的3D效果，还需要3D变换元素正好在窗体的垂直居中位置，因此，在Chrome浏览器下，生成了两个位置居中的按钮，帮助您看到想要的效果：<br>
<img alt="Chrome浏览器下位置居中按钮 张鑫旭-鑫空间-鑫生活" src="http://image.zhangxinxu.com/image/blog/201209/2012-09-05_224317.png" title="Chrome浏览器下位置居中按钮" width="277" height="146"><br>
<img alt="Chrome浏览器下舞台垂直居中3D效果显示" src="http://image.zhangxinxu.com/image/blog/201209/2012-09-05_224348.png" title="Chrome浏览器下舞台垂直居中3D效果显示" width="365" height="253"></p>
<p>当我们改变第一个<code>range</code>控件值为200的时候，您会发现右侧第三个元素看不见了：<br>
<img alt="200值的时候有元素看不见" src="http://image.zhangxinxu.com/image/blog/201209/2012-09-05_224822.png" title="200值的时候有元素看不见" width="514" height="250"></p>
<p>这不难理解，前面一排门，每个门都是1米，你距离门2米，显示，当所有门都开了45°角的时候，此时，距离中间门右侧的第二个门正好与你的视线平行，这个门的门面显然就什么也看不到。这就是为什么上面右侧第三个门一片空白的元素——特定的视角以及距离形成的视觉盲区。</p>
<div><em>如果您看到下面的文字，可能是由于在其他网站或是RSS中阅读本文，本文原地址：<a href="http://www.zhangxinxu.com/wordpress/?p=2592">http://www.zhangxinxu.com/wordpress/?p=2592</a>，本文作者：<a href="http://www.zhangxinxu.com/">张鑫旭</a>，来自张鑫旭-鑫空间-鑫生活，访问原出处阅读体验更佳。</em></div>
<h3>七、理解perspective-origin</h3>
<p><code>perspective-origin</code>这个属性超级好理解，表示你那双色迷迷的眼睛看的位置。默认就是所看舞台或元素的中心。有时候，我们对中心的位置是不感兴趣的，希望视线放在其他一些地方。比方说<img src="http://mat1.gtimg.com/www/mb/images/face/2.gif">：<br>
<img alt="不同视线落地位置对应不同的perspective-origin值" src="http://image.zhangxinxu.com/image/blog/201209/what-are-u-looking.png" title="不同视线落地位置对应不同的perspective-origin值" width="425" height="300"></p>
<p>一图胜千言，屌丝男们这个应该都懂的。</p>
<p>下面为立方体的实际应用透视效果图：</p>
<div>
<pre>perspective-origin: 25% 75%;</pre>
</div>
<p><img alt="立方体不同透视角度的效果 张鑫旭-鑫空间-鑫生活" src="http://image.zhangxinxu.com/image/blog/201209/perspective02.png" title="立方体不同透视角度的效果" width="258" height="255"></p>
<h3>八、transform-style: preserve-3d</h3>
<p><code>transform-style</code>属性也是3D效果中经常使用的，其两个参数，<code>flat|preserve-3d</code>. 前者<code>flat</code>为默认值，表示平面的；后者<code>preserve-3d</code>表示3D透视。</p>
<p><code>preserve-3d</code>符合我们真实世界的思维认识。比方说，你让妹子右转了45度，此时妹子脑袋左转45度想你吐舌卖萌，妹子的脸蛋应该和你是面对面平行的。<br>
<img alt="妹子推到与transform rotateY 张鑫旭-鑫空间-鑫生活" src="http://image.zhangxinxu.com/image/blog/201209/tuidao-y.jpg" title="身体旋转脸也旋转" width="108" height="150"><br>
应用<code>transform-style: preserve-3d</code>声明的元素确实是这样表现的，但是，如果使用默认的<code>flat</code>值，其效果表现——恕我想象力有限——想不通：妹子的脸还是左转45度的，同时脑袋似乎移到了身体以外的地方<img src="http://mat1.gtimg.com/www/mb/images/face/26.gif">！</p>
<p>因此，基本上，我们想要根据现实经验实现一些3D效果的时候，<code>transform-style: preserve-3d</code>是少不了的。一般而言，该声明应用在3D变换的兄弟元素们的父元素上，也就是舞台元素。</p>
<h3>九、backface-visibility</h3>
<p>在显示世界中，我们无法穿过软妹A看到其身后的软妹B或C或D；但是，在CSS3的3D世界中，默认情况下，我们是可以看到背后的元素（也不知可不可以透视妹子的衣服~<img src="http://mat1.gtimg.com/www/mb/images/face/2.gif">）！<br>
<img alt="看不到后面的软妹，哦呵呵,  backface-visibility, 张鑫旭-鑫空间-鑫生活" src="http://image.zhangxinxu.com/image/blog/201209/ruanmei.png" title="看不到后面的软妹，哦呵呵" width="300" height="182"></p>
<p>因此，为了切合实际，我们常常会这样设置，使后面元素不可见：</p>
<div>
<pre>backface-visibility:hidden;</pre>
</div>
<h3 style="color:both">十、实际应用-图片的旋转木马效果</h3>
<p>您可以狠狠地点击这里：<a href="http://www.zhangxinxu.com/study/201209/pictures-3d-slide-view.html">图片的旋转木马效果demo</a></p>
<p>建议在足够新版本的FireFox浏览器或Safari浏览器下观看，Chrome可能需要居中定位查看，下图为效果缩略图：<br>
<img alt="图片3D旋转木马效果截图 张鑫旭-鑫空间-鑫生活" src="http://image.zhangxinxu.com/image/blog/201209/2012-09-06_235939.png" title="图片3D旋转木马效果截图" width="314" height="195"></p>
<p><strong>原理：</strong><br>
那些看上去很酷酷的CSS3 3D效果其实就颠来倒去那几个属性（本文提到的这几个），折腾来折腾去，这里这个效果显然也是如此。</p>
<p>首先HTML结构，如下：</p>
<div>
<pre>舞台
    容器
        图片
        图片
        图片
        ...</pre>
</div>
<p>对于舞台，很简单，加个视距，比方说800像素：</p>
<div>
<pre> perspective: 800px;</pre>
</div>
<p>对于容器，很简单，加个3D视图声明，如下：</p>
<div>
<pre>transform-style: preserve-3d;</pre>
</div>
<p>然后就是图片们了。为了不至于产生类似DNA的螺旋状效果，我们让所有图片<code>position:absolute</code>，公用同一个中心点。</p>
<p>显然，图片旋转木马是类似钢管舞旋转的运动，因此，我们关心的是<code>rotateY</code>的大小。</p>
<p>因为要正好绕成一个圈，因此，图片<code>rotateY</code>值正好0~360等分，于是，如果有9张图片，则每个图片的旋转角度累加40(360 / 9 = 40)度即可。因此有：</p>
<div>
<pre>img:nth-child(1) { transform: rotateY(   0deg ); }
img:nth-child(2) { transform: rotateY(  40deg ); }
img:nth-child(3) { transform: rotateY(  80deg ); }
img:nth-child(4) { transform: rotateY( 120deg ); }
img:nth-child(5) { transform: rotateY( 160deg ); }
img:nth-child(6) { transform: rotateY( 200deg ); }
img:nth-child(7) { transform: rotateY( 240deg ); }
img:nth-child(8) { transform: rotateY( 280deg ); }
img:nth-child(9) { transform: rotateY( 320deg ); }</pre>
</div>
<p>这样就好了吗？</p>
<p>No, No, No!!!</p>
<p>想想看那，虽然9个绝色美女每个人的方位不一样，但都站在同一个点上，早就挤作一团，A罩都挤成C了，显然是不行的（见下图只设置rotateY）！我们需要拉开空间~~</p>
<p><img alt="只设置rotateY时候，众多美女图片挤作一团" src="http://image.zhangxinxu.com/image/blog/201209/2012-09-07_002527.png" title="只设置rotateY时候，众多美女图片挤作一团" width="300" height="212"></p>
<p>如何拉开空间，很简单。</p>
<p>想想看那：9个美女，分别面朝东南西北共9个不同方位，她们只要每个人向前走个4~5步，美女们之间的空间不久拉开了，呈现圆形了！想象一下夜空中，礼花绽开的场景~~</p>
<p>这里的向前走4~5步，聪明的人应该已经知道了，就是本文提到的<code>translateZ</code>, 当<code>translateZ</code>为正值的时候，元素会向其面对的方向走去；如果元素无旋转，就会朝显示器走来！！</p>
<p>现在只剩下一个问题了，美女们要向前走多远呢？？</p>
<p>这个距离是有计算公式滴！</p>
<p>拿本demo距离，每张美女图片的宽度是128像素，因此，有如下理想方位效果图：<br>
<img alt="旋转木马效果理想方位图 张鑫旭-鑫空间-鑫生活" src="http://image.zhangxinxu.com/image/blog/201209/diagram.png" title="旋转木马效果理想方位图" width="540" height="402"></p>
<p>上图中红色标注的<code>r</code>就是的demo页面中图片要<code>translateZ</code>的理想值（该值可以让所有图片无缝围成一个圆）！</p>
<p><code>r</code>的计算很简单，有初中数学水平的人应该都会：</p>
<div>
<pre>r = 64 / Math.tan(20 / 180 * Math.PI) ≈ 175.8</pre>
</div>
<p>demo页面为了好看，图片之间留了点间距，使用的<code>translateZ</code>的值为<code>175.8 + 20 = 195.8</code>.<br>
<img alt="旋转木马demo页面translateZ值大小" src="http://image.zhangxinxu.com/image/blog/201209/2012-09-07_003832.png" title="旋转木马demo页面translateZ值大小" width="407" height="173"></p>
<p>最后的最后，要让木马旋转起来，只要让容器每次旋转40度就可以了。</p>
<p>节省篇幅，具体的JavaScript操作代码就不展示了，您有兴趣可以查看demo页面源代码。</p>
<p>理解了旋转木马3D效果实现原理，基本上，其他些3D效果可以轻松驾驭了，因此，本效果还是值得你花功夫看看滴~~</p>
<h3>十一、好吧，结语</h3>
<p>理论上，现实世界，及3次元世界中的各种有规律的运动效果都可以使用CSS3 transform 3D方法实现。文章最后的旋转木马效果可以说是各类千奇百怪效果中的沧海一粟~~其他各类有的没有的效果就靠你的大脑就构想了。至于实现嘛，理解了，也就都是小菜。但是，要是不理解，纯粹从网上copy些效果代码，那永远就是copy的命咯！</p>
<p>文章篇幅已经很长了，我的指头也敲出老茧来了，就不再啰嗦什么了。希望本文的嗑叨、卖弄、折腾能够让您学习CSS3 3D transform变换的相关东西更加轻松点！</p>
<p>行文仓促，文中有错误在所难免，欢迎诸位指正。最后，感谢阅读，共同进步！</p>
<div>
<p>觉得这里的文章不错，希望他一直走下去？您可以：<a href="https://me.alipay.com/zhangxinxu" title="小小赞助大大帮助"><img src="http://www.zhangxinxu.com/wordpress/wp-content/themes/default/images/pay_encourage.png" width="159" height="37" alt="支付鼓励"></a></p>
</div>
<p>原创文章，转载请注明来自<a href="http://www.zhangxinxu.com/">张鑫旭-鑫空间-鑫生活</a>[<a href="http://www.zhangxinxu.com/">http://www.zhangxinxu.com</a>]<br>
本文地址：<a href="http://www.zhangxinxu.com/wordpress/?p=2592">http://www.zhangxinxu.com/wordpress/?p=2592</a></p>
<p>（本篇完）</p>
<div>有话要说，点击<a href="http://www.zhangxinxu.com/wordpress/2012/09/css3-3d-transform-perspective-animate-transition/#response">这里</a>发表评论。</div>