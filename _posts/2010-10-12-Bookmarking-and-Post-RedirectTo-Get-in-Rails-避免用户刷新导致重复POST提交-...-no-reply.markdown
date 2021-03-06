---
layout: post
title:  "Bookmarking and Post-RedirectTo-Get in Rails 避免用户刷新导致重复POST提交 ... no reply"
date:   2010-10-12 08:34:18
author: Latest from ChinaonRails
categories: program
---

## Bookmarking and Post-RedirectTo-Get in Rails 避免用户刷新导致重复POST提交 ... no reply
### by Latest from ChinaonRails
### at 2010-10-12 08:34:18
### original <http://feedproxy.google.com/~r/LatestFromChinaonrails/~3/7kpsBdV47Sc/4530.html>

<img src="http://upload.wikimedia.org/wikipedia/commons/thumb/f/f3/PostRedirectGet_DoubleSubmitProblem.png/350px-PostRedirectGet_DoubleSubmitProblem.png" border="0"><br>
<br>
在Web应用中，采用POST提交信息是非常常见的，然而如果目标页面打开得太慢，用户就可能会刷新页面，这时候之前已经提交过的信息就会被重复提交。即 使用户成功打开了POST提交目标页，之后他通过链接导航到别处了，再退回到POST提交目标页时仍可能会重复提交页面（例如因为浏览器缓存已失效）。而 且，只要碰到重复POST提交的场景，浏览器就会问用户是否确认重做此操作，用户并不一定能正确理解重做意味着重做什么，浏览器又不允许网站向用户解释清 楚，所以这属于非常不友善的设计。那么应该如何避免用户刷新带来的重复提交呢？<br>
<img src="http://upload.wikimedia.org/wikipedia/commons/thumb/3/3c/PostRedirectGet_DoubleSubmitSolution.png/350px-PostRedirectGet_DoubleSubmitSolution.png" border="0"><br>
Post/Redirect/Get (PRG) 是一种常用web开发的技术，主要是用来避免用户重复提交（post），使得用户可以更加直观的使用刷新。<br>
<br>
当用户提交一个操作的时候，不需要返回操作完以后的页面，而是重定向到该页面，让浏览器去重新get该页面。即post提交，redirect重定向，get获取该页面。<br>
<br>
<br>
这种模式叫Post，Redirect，Get(PRG)模式<br>
<a href="http://en.wikipedia.org/wiki/Post/Redirect/Get" rel="nofollow external">http://en.wikipedia.org/wiki/Post/Redirect/Get</a><br>
它可以"避免二次提交和允许web应用程序使用浏览器书签和reload按钮来更直观的表现"<br>
<a href="http://www.akshay.cc/2010/10/bookmarking-and-post-redirectto-get-in.html" rel="nofollow external">http://www.akshay.cc/2010/10/bookmarking-and-post-redirectto-get-in.html</a>
<p><a href="http://feedads.g.doubleclick.net/~a/MxeFjN9sxIH50dm0T0ZdOo5dC04/0/da"><img src="http://feedads.g.doubleclick.net/~a/MxeFjN9sxIH50dm0T0ZdOo5dC04/0/di" border="0" ismap></a><br>
<a href="http://feedads.g.doubleclick.net/~a/MxeFjN9sxIH50dm0T0ZdOo5dC04/1/da"><img src="http://feedads.g.doubleclick.net/~a/MxeFjN9sxIH50dm0T0ZdOo5dC04/1/di" border="0" ismap></a></p><img src="http://feeds.feedburner.com/~r/LatestFromChinaonrails/~4/7kpsBdV47Sc" height="1" width="1"><img src="http://www1.feedsky.com/t1/422690900/RubyonRails_q5tb/feedsky/s.gif?r=http://feedproxy.google.com/~r/LatestFromChinaonrails/~3/7kpsBdV47Sc/4530.html" border="0" height="0" width="0"><p><a href="http://www1.feedsky.com/r/l/feedsky/RubyonRails_q5tb/422690900/art01.html"><img border="0" ismap src="http://www1.feedsky.com/r/i/feedsky/RubyonRails_q5tb/422690900/art01.gif"></a></p>