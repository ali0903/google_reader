---
layout: post
title:  "修改hosts看youtube（使用谷歌北京IP非IPv6）"
date:   2011-07-18 14:56:27
author: iGFW
categories: program
---

## 修改hosts看youtube（使用谷歌北京IP非IPv6）
### by iGFW
### at 2011-07-18 14:56:27
### original <http://igfw.tk/archives/3910>

<p>众所周知 youtube 被和谐不是一天两天的事情了，翻墙的方式也众多，在这里我总结下我的方法。</p>
<p><strong>一、修改hosts文件</strong><br>
经过晚间的抓包分析 共得到800余行域名<br>
hosts文件在C:\WINDOWS\system32\drivers\etc目录下 hosts文件</p>
<h2>由于字符太多 请<a href="http://blog.xiaohai.co/wp-content/uploads/2011/07/youtube-hosts.zip">点击查看</a></h2>
<p><span></span></p>
<p>如有遗漏 还请见谅 请在下方留言 一同维护。</p>
<p><strong>二、通过路由器劫持DNS</strong><br>
路由器使用的是tomato，ddwrt openwrt等固件都可以。<br>
再菜单栏选Advanced Settings(高级设置) –&gt; DHCP / DNS –&gt;勾选 Intercept DNS Port(UDP 53)<br>
输入如下字符并保存。<br>
<code>address=/.youtube.com/203.208.46.29<br>
address=/.ytimg.com/203.208.46.29<br>
#Client<br>
address=/.googlevideo.com/203.208.46.29<br>
address=/.2mdn.net/203.208.46.29</code><br>
此方法简单易改动 无需为域名/IP 变动做出大改动，缺点：一般路由器不支持</p>
<p>附：iPhone iPad iPod hosts列表<br>
修改hosts需要越狱，hosts文件再/etc 目录下 hosts文件 使用ifile 或其它工具修改 <a href="http://blog.xiaohai.co/wp-content/uploads/2011/07/ios-youtube.txt">点击查看hosts文件</a> *便携设备专用<br>
客户端可播放大部分视频 网页端间歇性故障<br>
无Android设备暂未测试</p>
<p><strong>原文</strong>：<a href="http://blog.xiaohai.co/archives/youtube-gfw-hosts/">http://blog.xiaohai.co/archives/youtube-gfw-hosts/</a></p>