---
layout: post
title:  "[转载]最小高度的实现min-height（兼容IE6、IE7、IE8、IE9、F"
date:   2013-05-02 14:25:36
author: 晴方好玲
categories: program
---

## [转载]最小高度的实现min-height（兼容IE6、IE7、IE8、IE9、F
### by 晴方好玲
### at 2013-05-02 14:25:36
### original <http://blog.sina.com.cn/s/blog_897db7b50101b4dc.html>

<div style="padding-top:15px;margin:20px 0;border:none;border-top:1px dotted #ccc"><div style="margin-bottom:12px"><span style="margin-right:25px"><strong>原文地址：</strong><a href="http://blog.sina.com.cn/s/blog_9050e71e0100yqhd.html" title="最小高度的实现min-height（兼容IE6、IE7、IE8、IE9、FF、360等）">最小高度的实现min-height（兼容IE6、IE7、IE8、IE9、FF、360等）</a></span><span><strong>作者：</strong><a href="http://blog.sina.com.cn/u/2421221150" title="用户体验设计中心">用户体验设计中心</a></span></div><div><p>
   <strong> 如果觉得有用请关注一下以下网址哦，谢谢</strong>！<a href="http://meihaoduo.taobao.com/">http://meihaoduo.taobao.com/</a></p>
<p><br>
   
我们做WEB开发的时候，为了美观往往会固定一个容器的高度，当内容没有超过高度的时候，整体是</p>
<p>美观的，但当内容超过高度的时候，在IE6下，容器能够自动的伸展以适应内容的变化。保持了美观</p>
<p>性，但IE其他版本和其他浏览器就没有这么幸运了，其他浏览器下，当内容超过了容器高度的时候</p>
<p>，容器就不能自动的伸展以适应内容的变化了，内容直接撑破了容器，超出的内容直接跑到容器外</p>
<p>面了，这样打破了整体的美观性。</p>
<p>
    
为了解决这个问题，我们引入了最小高度 min-height，我们先设置一个适合整体美观的高度为最小</p>
<p>高度，当内容超过了最小高度的时候，容器的高度会随着内容的增多而增多，一直保持整体的美观</p>
<p>性。</p>
<p> </p>
<p>   
下面是代码，注意代码的顺序不能变，必须这样的顺序，因为 min-height在IE6是不起作用的。</p>
<p> </p>
<p>
   <strong><em> 如果觉得有用请关注一下以下网址哦，谢谢！</em></strong><a href="http://meihaoduo.taobao.com/">http://meihaoduo.taobao.com/</a></p>
<p><br>
.test{</p>
<p>   
min-height:100px;<br>
    height:auto
!important;<br>
   
height:100px;</p>
<p>    }</p></div></div><br><img src="http://simg.sinajs.cn/blog7style/images/special/1265.gif"> <a href="http://sina.allyes.com/main/adfclick?db=sina&amp;bid=204720,469641,474922&amp;cid=0,0,0&amp;sid=473458&amp;advid=358&amp;camid=37389&amp;show=ignore&amp;url=http://qing.blog.sina.com.cn/tag/%E5%86%99%E7%9C%9F">青春就应该这样绽放</a>  <a href="http://sina.allyes.com/main/adfclick?db=sina&amp;bid=204720,469645,474926&amp;cid=0,0,0&amp;sid=473464&amp;advid=358&amp;camid=37389&amp;show=ignore&amp;url=http%3A%2F%2Funion.9173.com%2Fpub%3Fp%3D1%26u%3D1008">游戏测试：三国时期谁是你最好的兄弟！！</a>  <a href="http://sina.allyes.com/main/adfclick?db=sina&amp;bid=204720,469646,474927&amp;cid=0,0,0&amp;sid=473465&amp;advid=358&amp;camid=37389&amp;show=ignore&amp;url=http://qing.blog.sina.com.cn/tag/%E6%98%9F%E5%BA%A7">你不得不信的星座秘密</a><img src="http://sina.allyes.com/main/adfclick?db=sina&amp;bid=204720,470173,475454&amp;cid=0,0,0&amp;sid=474001&amp;advid=358&amp;camid=37389&amp;show=ignore&amp;url=http://simg.sinajs.cn/blog7style/images/common/sg_trans.gif?t=8">