---
layout: post
title:  "修改swfupload V2.5beta3 笔记.part2"
date:   2012-09-12 11:11:54
author: rank
categories: program
---

## 修改swfupload V2.5beta3 笔记.part2
### by rank
### at 2012-09-12 11:11:54
### original <http://www.udpwork.com/item/8101.html>

<p>在github上我放了一个修改版，有兴趣的同学可以测试下性能
<br>
<a href="https://github.com/ranklau/repos/tree/master/labs/swfupload2.5-fixed" title="https://github.com/ranklau/repos/tree/master/labs/swfupload2.5-fixed">https://github.com/ranklau/repos/tree/master/labs/swfupload2.5-fixed</a>
<br>
注意，有php文件，要在支持php的webserver下运行。
<br>

<br>
<b>再议问题</b>
<br>
经过上一轮的swfupload修改个人以为差不多是结论了。但好景不长，最近有人反馈</p>
<ul><li>上传速度非常慢，有的时候甚至8分钟都没上传完图片；</li>
<li>其次是上传图片数据到阈值就无法上传的问题；</li>
</ul>
那天debug了一个下午，发现：<ul><li>问题1上传速度慢的问题，只存在IE里。（奇怪吧，为什么flash的性能还和浏览器有差别）</li>
<li>问题2不是flash的问题，与脚本使用有关（经查问题发现flash隐藏之后在IE下压缩和上传都不工作）</li>
</ul>
<b>改造以及测试</b>
<br>

<br>
随之，经过一系列的调试和全系列机器环境测试；
<br>
测试环境在flash player版本都是11+（小版本没计）在mac下的最新版safari, chrome, firefox以及windows xp以及win 7下的ie6,ie7,ie8,ie7；
<br>
测试图片由5616 × 3744 5M及3888 × 2592px的图组成；
<br>
由原图压缩成1000*1000的图；质量90。
<br>
以下所说时间均为压缩时间（不包括上传时间）。
<br>

<br>
我用了多种异步JPEGencode的库，发现还是alchemy的效率比较好，所以只列它的数据。
<br>
异步library在非IE下表现良好，但是在IE下性能极差。<ul><li>IE时间值大致分布在4s~30s之间（均值8s～10s左右，时间上随机性很大）</li>
<li>非IE下值相对很稳定，时间都在3~5s这个区间。</li>
</ul>
IE与其他浏览器相比，性能有的时候甚至有差10倍！这不能忍啊。
<br>
我猜测是flash player在IE下，不能获得较长的CPU时间片。（注：这是有办法测试的，我只是目测了CPU监控得出的结论。）
<br>

<br>
因此我试着改用同步压缩，而不用异步。发现效果比预期要好得多（如果用swc会更佳）。
<br>
用同步的压缩有可以去<a href="http://www.bytearray.org%E7%94%A8%E6%94%B9%E8%89%AF%E7%89%88%E7%9A%84JPEGEncoder.as%E3%80%82" title="http://www.bytearray.org用改良版的JPEGEncoder.as。">http://www.bytearray.org用改良版的JPEGEncoder.as。</a>
<br>
我用的是liuyang同学推荐的<a href="http://www.blooddy.by/en/crypto/" title="http://www.blooddy.by/en/crypto/">blooddy_crypto.swc</a>
<br>

<br>
测试出来的结果相当理想：<ul><li>IE时间值大致分布在1~8s之间（均值4s左右，时间上随机性很大，一次上传的图片越多，越趋向于均值）</li>
<li>非IE下值相对很稳定，时间都是1s左右，绝在多数用时不超过1s。</li>
</ul>
没有出现假死现象。用i4 cpu测试，cpu使用最高才13%。
<br>

<br>
这给我的教训是为什么不及早使用同步压缩？
<br>
是一直以来的观念束缚了我，认为同步一定会假死。
<br>
从结果来看则不然。1是压缩时间相对较少，2flash本身对此也有控制。
<br>
从cpu监控可以看到控制都较为良好（都没到50%）；
<br>
从测试结果来看，ie下的flash player的性能短期使用很随机，长期看性能平均下来比非ie要慢不少。
<br>
曲线是由快而慢，然后慢慢是趋向均值（非常怪异，也合理的曲线），虽然这让人很懊恼。
			<div style="margin-top:8px;padding:6px 0;border-top:1px solid #3cf">
				<div style="text-align:center;margin:16px 0;padding:6px;border:0px dashed #999;font-family:arial;font-size:26px;font-weight:bold">
	<a href="http://www.udpwork.com/item/8101.html#review_form" title="不喜欢" style="text-decoration:none">
		<img src="http://www.udpwork.com//images/thumb_down24.gif" alt="">
		<span style="color:#f33">0</span>
	</a>
	   
	<a href="http://www.udpwork.com/item/8101.html#review_form" title="喜欢" style="text-decoration:none">
		<img src="http://www.udpwork.com//images/thumb_up24.gif" alt="">
		<span style="color:#3c3">0</span>
	</a>
</div>				<p>
					由 <a href="http://www.udpwork.com/">IT 牛人博客聚合网站(udpwork.com)</a> 聚合
					|
					<a href="http://www.udpwork.com/item/8101.html#reviews">评论: 0</a>
					|
					<a href="http://book.benegg.com/tag/%E7%BC%96%E7%A8%8B?from=udpwork-feed">10000+ 本编程/Linux PDF/CHM 电子书下载</a>
				</p>
			</div>