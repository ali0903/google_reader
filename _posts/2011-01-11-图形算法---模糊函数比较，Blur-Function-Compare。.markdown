---
layout: post
title:  "图形算法 - 模糊函数比较，Blur Function Compare。"
date:   2011-01-11 01:33:00
author: foxriver
categories: program
---

## 图形算法 - 模糊函数比较，Blur Function Compare。
### by foxriver
### at 2011-01-11 01:33:00
### original <http://www.cppblog.com/foxriver/archive/2011/01/11/138316.html>

<p>加入比较的4种方法有：</p>
<p>1. 快速高斯模糊。<br>2. 二次Summed Area Table Blur(适合gpu, 常用于DOF，<a href="http://www.gamasutra.com/view/feature/3102/four_tricks_for_fast_blurring_in_.php">http://www.gamasutra.com/view/feature/3102/four_tricks_for_fast_blurring_in_.php</a>)<br>3. Alpha Blur(motionblur变种，优化过后的算法，严重依赖相邻像素之间累积关系，不适合gpu, <a href="http://freespace.virgin.net/hugo.elias/graphics/x_motion.htm">http://freespace.virgin.net/hugo.elias/graphics/x_motion.htm</a>)<br>4. SuperFastBoxBlur(<a href="http://incubator.quasimondo.com/processing/superfast_blur.php">http://incubator.quasimondo.com/processing/superfast_blur.php</a><a href="http://incubator.quasimondo.com/processing/fast_blur_deluxe.php"></a>)</p>
<p>其中，这四种方法内，除了高斯模糊是O(n)之外，其他都是O(1)效率。也就是说，运算速度和模糊半径没有关系，只和图片大小有关。</p>
<p><br><img border="0" alt="" src="http://www.cppblog.com/images/cppblog_com/foxriver/blurcompare.png" width="768" height="768"><br><br>图上时间仅供参考，除了AlphaBlur代码优化过，其它方法仅仅是基本实现原理。SuperFastBoxBlur和SummedAreaTable实际使用中，要比图上更快些。<br><br>以下方法没有列入比较范围</p>
<p>1. 直接基于O(n*n)的高斯模糊核，速度太慢。图上方法采用纵向和横向高斯核分解。<br>2. Stack Blur, 比高斯快的方法，并且效果和效率兼备。(<a href="http://www.codeproject.com/KB/graphics/blurringwithcuda.aspx">http://www.codeproject.com/KB/graphics/blurringwithcuda.aspx</a><a href="http://incubator.quasimondo.com/processing/fast_blur_deluxe.php"></a>)<br>3. FFT gaussian blur<br>4. High-Dimensional Gaussian Filtering (bilateral)<br>5. constant time filters - heat diffusion.(Kass, 2006)<br>6. constant time filters - SVD, singular value decomposition (Gotsman 1994)<br>7. SAT衍生出来的一些方法，Fast Filter Spreading，Linear Filters and their Transposes。<br><br>---------------------------------------------------<br>关于2次Summed Area Table实现。<br><br>一次SAT采样4个点，二次SAT则需要9个点，三次需要16个点，以此类推。并且乘上对应的Weight Function，除以(面积^n, n = 几次). 详细公式见：Filtering By Repeated Integration<br><br></p>
<img src="http://www.cppblog.com/foxriver/aggbug/138316.html" width="1" height="1"><br><br><div align="right"><a style="text-decoration:none" href="http://www.cppblog.com/foxriver/">foxriver</a> 2011-01-11 01:33 <a href="http://www.cppblog.com/foxriver/archive/2011/01/11/138316.html#Feedback" style="text-decoration:none">发表评论</a></div>