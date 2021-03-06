---
layout: post
title:  "Bump的Redis应用经验"
date:   2011-07-18 15:49:28
author: 
categories: program
---

## Bump的Redis应用经验
### by 
### at 2011-07-18 15:49:28
### original <http://blog.nosqlfan.com/html/2460.html>

<p>来自: <a href="http://www.feedzshare.com/b/4219241/2">NoSQLfan - FeedzShare</a>  
<br>发布时间:2011年07月17日,  已有 3 人推荐 </p>
<hr><div><p>这里说的<a href="http://bu.mp/">Bump</a>就是那个手机碰一碰就能交换信息的app，本文来自<span><a href="http://blog.nosqlfan.com/tags/bump" title="查看 Bump 的全部文章">Bump</a></span>的开发者博客，他们描述了自己使用<span><a href="http://blog.nosqlfan.com/tags/redis" title="查看 Redis 的全部文章">Redis</a></span>的几种方法，以及在使用过程中的一些经验，包括与<span><a href="http://blog.nosqlfan.com/tags/mongodb" title="查看 MongoDB 的全部文章">MongoDB</a></span>同机部署的一些问题，NoSQLFan转译如下，详细描述可移步<a href="http://devblog.bu.mp/how-we-use-redis-at-bump">原文</a></p>
<p><a href="http://blog.nosqlfan.com/wp-content/uploads/2011/07/bump.jpg"><img title="bump" src="http://blog.nosqlfan.com/wp-content/uploads/2011/07/bump.jpg" alt="" width="329" height="364"></a></p>
<h3>1.将Redis用作消息队列</h3>
<p>采用的当然是Redis的List数据结构，而实际上NoSQLFan之前还讨论过采用sorted sets结构来做带权重的消息队列的方法。</p>
<h3>2.将Redis用作日志收集器</h3>
<p>实际上还是一个队列，多个端点将日志信息写入Redis，然后一个worker统一将所有日志写到磁盘。</p>
<h3>3.存储社交关系</h3>
<p>比如将每个人的好友存在一个集合（set）中，这样求两个人的共同好友的操作，可能就只需要用求交集命令即可。</p>
<h3>4.用作缓存层</h3>
<p>用来做持久化存储的缓存，和Memcached一样，可以在持久化存储获取成功后将数据在Redis中进行缓存，我们知道，Redis的性能优于Memcached。</p>
<h3>5.关于持久化</h3>
<p>用作持久存储的时候，可以选择定时flush成rdb或开启aof日志的做法，具体选择哪一种，可以根据业务上可接受的数据可靠程度而定。</p>
<h3>6.与MongoDB一起使用的问题</h3>
<p>一个提醒，在Bump早期，曾将Redis和MongoDB放在同一台机器，而由于Redis的单线程，常由于MongoDB对于CPU或磁盘IO的占用而导致其某个过程（比如malloc或者写aof）执行时间过长，从而进一步导致其所有操作被阻塞并延迟，所以尽量不要将其用于可能被大量占用CPU或磁盘IO，导致Redis工作线程被阻塞的场合。</p>
<p>来源：<a href="http://devblog.bu.mp/how-we-use-redis-at-bump">devblog.bu.mp</a>
<p><span>技术传播，需要你我共同努力！</span><a href="http://twitter.com/share?url=http%3A%2F%2Fblog.nosqlfan.com%2Fhtml%2F2460.html&amp;text=Bump%E7%9A%84Redis%E5%BA%94%E7%94%A8%E7%BB%8F%E9%AA%8C%20@nosqlfan" title="Twitter"><img src="http://pic.yupoo.com/iammutex/B8hVKEJk/custom.png"></a><a href="http://v.t.sina.com.cn/share/share.php?title=Bump%E7%9A%84Redis%E5%BA%94%E7%94%A8%E7%BB%8F%E9%AA%8C%20@nosqlfan%20&amp;url=http%3A%2F%2Fblog.nosqlfan.com%2Fhtml%2F2460.html" title="新浪微博"><img src="http://pic.yupoo.com/iammutex/B8hVKrzm/custom.png"></a><a href="http://v.t.qq.com/share/share.php?title=Bump%E7%9A%84Redis%E5%BA%94%E7%94%A8%E7%BB%8F%E9%AA%8C%20@nosqlfan%20&amp;url=http%3A%2F%2Fblog.nosqlfan.com%2Fhtml%2F2460.html" title="腾讯微博"><img src="http://pic.yupoo.com/iammutex/B8hVJX6o/custom.png"></a><a href="http://sns.qzone.qq.com/cgi-bin/qzshare/cgi_qzshare_onekey?title=Bump%E7%9A%84Redis%E5%BA%94%E7%94%A8%E7%BB%8F%E9%AA%8C%20@nosqlfan%20&amp;url=http%3A%2F%2Fblog.nosqlfan.com%2Fhtml%2F2460.html" title="QQ空间"><img src="http://pic.yupoo.com/iammutex/B8hVJSKI/custom.png"></a><a href="http://www.douban.com/recommend/?url=http%3A%2F%2Fblog.nosqlfan.com%2Fhtml%2F2460.html&amp;title=Bump%E7%9A%84Redis%E5%BA%94%E7%94%A8%E7%BB%8F%E9%AA%8C%20@nosqlfan" title="豆瓣9点"><img src="http://pic.yupoo.com/iammutex/B8hVJrri/custom.png"></a><a href="http://xianguo.com/service/submitdigg?link=http%3A%2F%2Fblog.nosqlfan.com%2Fhtml%2F2460.html&amp;title=Bump%E7%9A%84Redis%E5%BA%94%E7%94%A8%E7%BB%8F%E9%AA%8C%20@nosqlfan%20&amp;content=utf-8" title="鲜果"><img src="http://pic.yupoo.com/iammutex/B8hVJ4v4/custom.png"></a><a href="http://share.renren.com/share/buttonshare.do?link=http%3A%2F%2Fblog.nosqlfan.com%2Fhtml%2F2460.html" title="人人网"><img src="http://pic.yupoo.com/iammutex/B8hVI86k/custom.png"></a><a href="http://www.facebook.com/sharer.php?u=http%3A%2F%2Fblog.nosqlfan.com%2Fhtml%2F2460.html&amp;title=Bump%E7%9A%84Redis%E5%BA%94%E7%94%A8%E7%BB%8F%E9%AA%8C%20@nosqlfan" title="FaceBook"><img src="http://pic.yupoo.com/iammutex/B8hVHr67/custom.png"></a></p>
<table cellspacing="0" cellpadding="3" border="0">
    
    <tr>
        <td colspan="5"><b><font size="-1">您可能还喜欢：</font></b></td>
    </tr>
    
        <tr>
                <td width="106" valign="top">
                    <a title="Redis命令参考中文版翻译" href="http://app.wumii.com/ext/redirect.htm?url=http%3A%2F%2Fblog.nosqlfan.com%2Fhtml%2F2404.html&amp;from=http%3A%2F%2Fblog.nosqlfan.com%2Fhtml%2F2460.html">
                        <img src="http://pic.yupoo.com/iammutex/B8sPBDdj/square.jpg" width="100px" height="100px"><br>
                        <font size="-1" color="#333333">Redis命令参考中文版翻译</font>
                    </a>
                </td>
                <td width="106" valign="top">
                    <a title="foursquare 的数据分析系统（Hadoop+Hive+Redis+MongoDB）" href="http://app.wumii.com/ext/redirect.htm?url=http%3A%2F%2Fblog.nosqlfan.com%2Fhtml%2F1466.html&amp;from=http%3A%2F%2Fblog.nosqlfan.com%2Fhtml%2F2460.html">
                        <img src="http://static.wumii.com/site_images/2011/06/09/11639351.png" width="100px" height="100px"><br>
                        <font size="-1" color="#333333">foursquare 的数据分析系统（Hadoop+Hive+Redis+MongoDB）</font>
                    </a>
                </td>
                <td width="106" valign="top">
                    <a title="Redis 测试引擎将升级提速" href="http://app.wumii.com/ext/redirect.htm?url=http%3A%2F%2Fblog.nosqlfan.com%2Fhtml%2F2383.html&amp;from=http%3A%2F%2Fblog.nosqlfan.com%2Fhtml%2F2460.html">
                        <img src="http://static.wumii.com/site_images/2011/07/12/17143319.jpg" width="100px" height="100px"><br>
                        <font size="-1" color="#333333">Redis 测试引擎将升级提速</font>
                    </a>
                </td>
                <td width="106" valign="top">
                    <a title="非同一般的Redis介绍" href="http://app.wumii.com/ext/redirect.htm?url=http%3A%2F%2Fblog.nosqlfan.com%2Fhtml%2F2364.html&amp;from=http%3A%2F%2Fblog.nosqlfan.com%2Fhtml%2F2460.html">
                        <img src="http://pic.yupoo.com/iammutex/B8sPBDdj/square.jpg" width="100px" height="100px"><br>
                        <font size="-1" color="#333333">非同一般的Redis介绍</font>
                    </a>
                </td>
                <td width="106" valign="top">
                    <a title="REDIS TO GO：一个Redis存储服务" href="http://app.wumii.com/ext/redirect.htm?url=http%3A%2F%2Fblog.nosqlfan.com%2Fhtml%2F1940.html&amp;from=http%3A%2F%2Fblog.nosqlfan.com%2Fhtml%2F2460.html">
                        <img src="http://static.wumii.com/site_images/2011/06/14/12130942.png" width="100px" height="100px"><br>
                        <font size="-1" color="#333333">REDIS TO GO：一个Redis存储服务</font>
                    </a>
                </td>
        </tr>
    
    <tr>
        <td colspan="5" align="right">
            <a href="http://www.wumii.com/widget/relatedItems.htm" title="无觅相关文章插件">
                <font size="-1" color="#bbbbbb">无觅</font>
            </a>
        </td>
    </tr>
</table></p></div><img src="http://img.tongji.linezing.com/1017243/tongji.gif"><img src="http://img.tongji.linezing.com/855372/tongji.gif">