---
layout: post
title:  "给wordpress添加Ctrl+Enter快捷发表评论功能"
date:   2010-08-27 14:54:28
author: 疯狂小强
categories: program
---

## 给wordpress添加Ctrl+Enter快捷发表评论功能
### by 疯狂小强
### at 2010-08-27 14:54:28
### original <http://www.woiweb.net/wordpress-ctrl-enter-comment-function.html>

<p>经常逛论坛的人一般都习惯写完评论后，直接按ctrl+enter提交评论，使用QQ聊天时也是这样，打完字随手按下快捷键搞定，其实通过加入js代码，在<span><a href="http://www.woiweb.net/tag/wordpress">wordpress</a></span>评论时也可以实现这个功能。（注意代码中的全角都要改为半角），今天在网上发现了<a href="http://mybloging.cn/">“小石头”</a>的这段代码，与大家一起分享。<span></span></p>
<p>方法一、在<span><a href="http://www.woiweb.net/tag/wordpress">wordpress</a></span>的comments.php里&lt;/form&gt;&lt;/div&gt;&lt;?php endif; ?&gt;后插入如下js代码也可以实现这个功能：</p>
<pre>&lt;script type=&quot;text/<span><a href="http://www.woiweb.net/category/javascript" rel="nofollow">javascript</a></span>&quot;&gt;
document.getElementById(&quot;comment&quot;).onkeydown = function (moz_ev)
{
var ev = null;
if (window.event){
ev = window.event;
}else{
ev = moz_ev;
}
if (ev != null &amp;&amp; ev.ctrlKey &amp;&amp; ev.keyCode == 13)
{
document.getElementById(&quot;submit&quot;).click();
}
}
&lt;/script&gt;</pre>
<p>然后继续在comments.php文件中找到下面这句：</p>
<pre>&lt;p&gt;&lt;input name=&quot;submit&quot; type=&quot;submit&quot; id=&quot;submit&quot; tabindex=&quot;5&quot; value=&quot;Leave comment&quot; /&gt;
</pre>
<p>将value=”Leave comment”（不同的主题可能会有些差别，其实就是提交的那个按扭）的value值里加上一个(Ctrl+Enter)，改成value=”Leave comment (Ctrl+Enter)”</p>
<p>方法二、此法更为简洁，强烈推荐使用</p>
<p>在 comments.php 中找到以下代码：<br>
&lt;textarea name=”comment” id=”comment” cols=”105″ rows=”10″tabindex=”4″&gt;&lt;/textarea&gt;修改为：</p>
<pre>
&lt;textarea name=&quot;comment&quot; id=&quot;comment&quot; cols=&quot;105&quot; rows=&quot;10&quot;tabindex=&quot;4&quot;
onkeydown=&quot;if(event.ctrlKey&amp;&amp;event.keyCode==13)
{document.getElementById(&#39;submit&#39;).click();return false};&quot;&gt;
&lt;/textarea&gt;</pre>