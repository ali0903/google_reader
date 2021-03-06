---
layout: post
title:  "Reds：一个Redis加Node.js的全文搜索引擎"
date:   2011-08-02 13:46:40
author: nosqlfan
categories: program
---

## Reds：一个Redis加Node.js的全文搜索引擎
### by nosqlfan
### at 2011-08-02 13:46:40
### original <http://item.feedsky.com/~feedsky/nosqlfan/~8149226/543387930/6253001/1/item.html>

<p><span><a href="http://blog.nosqlfan.com/tags/reds" title="查看 Reds 的全部文章">Reds</a></span>是由<a href="https://www.learnboost.com/">LearnBoost</a>公司的TJ Holowaychuk开发的一个基于<span><a href="http://blog.nosqlfan.com/tags/redis" title="查看 Redis 的全部文章">Redis</a></span>的Node.js全文搜索引擎，其代码加上注释也只有300行。不得不说又是一个Redis的最佳实践，它的主要原理是通过Redis的sets数据结构将分词后的词语碎片进行存储。这里的分词仅仅是对英文按空格进行切分（中文分词就不要想了～）。</p>
<p><a href="http://blog.nosqlfan.com/wp-content/uploads/2011/08/redisnodejs.jpg"><img title="redisnodejs" src="http://blog.nosqlfan.com/wp-content/uploads/2011/08/redisnodejs.jpg" alt="" width="621" height="89"></a></p>
<p>例子：<br>
先添加几个句子到搜索引擎中建立索引</p>
<pre>var strs = [];
strs.push('Tobi wants four dollars');
strs.push('Tobi only wants $4');
strs.push('Loki is really fat');
strs.push('Loki, Jane, and Tobi are ferrets');
strs.push('Manny is a cat');
strs.push('Luna is a cat');
strs.push('Mustachio is a cat');

strs.forEach(function(str, i){ search.index(str, i); });</pre>
<p>然后在Tobi dollars这个组合进行搜索</p>
<pre>search.query(query = 'Tobi dollars', function(err, ids){
  if (err) throw err;
  console.log('Search results for "%s":', query);
  ids.forEach(function(id){
    console.log('  - %s', strs[id]);
  });
  process.exit();
});</pre>
<p>下面是其搜索结果</p>
<pre>Search results for "Tobi dollars":
  - Tobi wants four dollars</pre>
<p>更多相关信息及源码请上其在GitHub上的项目地址：<a href="https://github.com/visionmedia/reds">https://github.com/visionmedia/reds</a></p>
<p>同时感谢@<a href="http://twitter.com/#!/xu_lele">xu_lele</a>的分享，分享NoSQL相关技术与新闻，只需要在twitter，新浪微博，腾讯微博上@nosqlfan 即可。
<p style="font-weight:bold"><span style="padding-top:5px;float:left">技术传播，需要你我共同努力！</span><a href="http://twitter.com/share?url=http%3A%2F%2Fblog.nosqlfan.com%2Fhtml%2F2676.html&amp;text=Reds%EF%BC%9A%E4%B8%80%E4%B8%AARedis%E5%8A%A0Node.js%E7%9A%84%E5%85%A8%E6%96%87%E6%90%9C%E7%B4%A2%E5%BC%95%E6%93%8E%20@nosqlfan" title="Twitter" style="text-decoration:none;margin:2px;border:none"><img style="border:none;padding:0px" src="http://pic.yupoo.com/iammutex/B8hVKEJk/custom.png"></a><a href="http://v.t.sina.com.cn/share/share.php?title=Reds%EF%BC%9A%E4%B8%80%E4%B8%AARedis%E5%8A%A0Node.js%E7%9A%84%E5%85%A8%E6%96%87%E6%90%9C%E7%B4%A2%E5%BC%95%E6%93%8E%20@nosqlfan%20&amp;url=http%3A%2F%2Fblog.nosqlfan.com%2Fhtml%2F2676.html" title="新浪微博" style="text-decoration:none;margin:2px;border:none"><img style="border:none;padding:0px" src="http://pic.yupoo.com/iammutex/B8hVKrzm/custom.png"></a><a href="http://v.t.qq.com/share/share.php?title=Reds%EF%BC%9A%E4%B8%80%E4%B8%AARedis%E5%8A%A0Node.js%E7%9A%84%E5%85%A8%E6%96%87%E6%90%9C%E7%B4%A2%E5%BC%95%E6%93%8E%20@nosqlfan%20&amp;url=http%3A%2F%2Fblog.nosqlfan.com%2Fhtml%2F2676.html" title="腾讯微博" style="text-decoration:none;margin:2px;border:none"><img style="border:none;padding:0px" src="http://pic.yupoo.com/iammutex/B8hVJX6o/custom.png"></a><a href="http://sns.qzone.qq.com/cgi-bin/qzshare/cgi_qzshare_onekey?title=Reds%EF%BC%9A%E4%B8%80%E4%B8%AARedis%E5%8A%A0Node.js%E7%9A%84%E5%85%A8%E6%96%87%E6%90%9C%E7%B4%A2%E5%BC%95%E6%93%8E%20@nosqlfan%20&amp;url=http%3A%2F%2Fblog.nosqlfan.com%2Fhtml%2F2676.html" title="QQ空间" style="text-decoration:none;margin:2px;border:none"><img style="border:none;padding:0px" src="http://pic.yupoo.com/iammutex/B8hVJSKI/custom.png"></a><a href="http://www.douban.com/recommend/?url=http%3A%2F%2Fblog.nosqlfan.com%2Fhtml%2F2676.html&amp;title=Reds%EF%BC%9A%E4%B8%80%E4%B8%AARedis%E5%8A%A0Node.js%E7%9A%84%E5%85%A8%E6%96%87%E6%90%9C%E7%B4%A2%E5%BC%95%E6%93%8E%20@nosqlfan" title="豆瓣9点" style="text-decoration:none;margin:2px;border:none"><img style="border:none;padding:0px" src="http://pic.yupoo.com/iammutex/B8hVJrri/custom.png"></a><a href="http://xianguo.com/service/submitdigg?link=http%3A%2F%2Fblog.nosqlfan.com%2Fhtml%2F2676.html&amp;title=Reds%EF%BC%9A%E4%B8%80%E4%B8%AARedis%E5%8A%A0Node.js%E7%9A%84%E5%85%A8%E6%96%87%E6%90%9C%E7%B4%A2%E5%BC%95%E6%93%8E%20@nosqlfan%20&amp;content=utf-8" title="鲜果" style="text-decoration:none;margin:2px;border:none"><img style="border:none;padding:0px" src="http://pic.yupoo.com/iammutex/B8hVJ4v4/custom.png"></a><a href="http://share.renren.com/share/buttonshare.do?link=http%3A%2F%2Fblog.nosqlfan.com%2Fhtml%2F2676.html" title="人人网" style="text-decoration:none;margin:2px;border:none"><img style="border:none;padding:0px" src="http://pic.yupoo.com/iammutex/B8hVI86k/custom.png"></a><a href="http://www.facebook.com/sharer.php?u=http%3A%2F%2Fblog.nosqlfan.com%2Fhtml%2F2676.html&amp;title=Reds%EF%BC%9A%E4%B8%80%E4%B8%AARedis%E5%8A%A0Node.js%E7%9A%84%E5%85%A8%E6%96%87%E6%90%9C%E7%B4%A2%E5%BC%95%E6%93%8E%20@nosqlfan" title="FaceBook" style="text-decoration:none;margin:2px;border:none"><img style="border:none;padding:0px" src="http://pic.yupoo.com/iammutex/B8hVHr67/custom.png"></a>    </p>
<table cellspacing="0" cellpadding="3" border="0" style="clear:both">
    
    <tr>
        <td colspan="5"><b><font size="-1" style="display:block!important;padding:20px 0 5px!important">相关文章：</font></b></td>
    </tr>
    
        <tr>
                <td width="106" valign="top" style="padding:5px!important;margin:0!important">
                    <a title="Redis运维之道" style="text-decoration:none!important" href="http://app.wumii.com/ext/redirect.htm?url=http%3A%2F%2Fblog.nosqlfan.com%2Fhtml%2F2692.html&amp;from=http%3A%2F%2Fblog.nosqlfan.com%2Fhtml%2F2676.html">
                        <img style="margin:0!important;padding:2px!important;border:1px solid #dddddd!important;width:100px!important;height:100px!important" src="http://static.wumii.com/site_images/2011/08/03/20737794.jpg" width="100px" height="100px"><br>
                        <font size="-1" color="#333333" style="display:block!important;line-height:15px!important;width:106px!important;font:12px/15px arial!important;height:60px!important;margin:3px 0 0 0!important;padding:0!important;overflow:hidden!important">Redis运维之道</font>
                    </a>
                </td>
                <td width="106" valign="top" style="padding:5px!important;margin:0!important;border-left:1px solid #dddddd!important">
                    <a title="Instagram的实时图片Demo：Node.js, Redis 加 Web Sockets" style="text-decoration:none!important" href="http://app.wumii.com/ext/redirect.htm?url=http%3A%2F%2Fblog.nosqlfan.com%2Fhtml%2F2007.html&amp;from=http%3A%2F%2Fblog.nosqlfan.com%2Fhtml%2F2676.html">
                        <img style="margin:0!important;padding:2px!important;border:1px solid #dddddd!important;width:100px!important;height:100px!important" src="http://static.wumii.com/site_images/2011/06/16/12611411.jpg" width="100px" height="100px"><br>
                        <font size="-1" color="#333333" style="display:block!important;line-height:15px!important;width:106px!important;font:12px/15px arial!important;height:60px!important;margin:3px 0 0 0!important;padding:0!important;overflow:hidden!important">Instagram的实时图片Demo：Node.js, Redis 加 Web Sockets</font>
                    </a>
                </td>
                <td width="106" valign="top" style="padding:5px!important;margin:0!important;border-left:1px solid #dddddd!important">
                    <a title="lodis：一个JavaScript实现的本地Redis存储" style="text-decoration:none!important" href="http://app.wumii.com/ext/redirect.htm?url=http%3A%2F%2Fblog.nosqlfan.com%2Fhtml%2F2696.html&amp;from=http%3A%2F%2Fblog.nosqlfan.com%2Fhtml%2F2676.html">
                        <img style="margin:0!important;padding:2px!important;border:1px solid #dddddd!important;width:100px!important;height:100px!important" src="http://pic.yupoo.com/iammutex/B8sPBDdj/square.jpg" width="100px" height="100px"><br>
                        <font size="-1" color="#333333" style="display:block!important;line-height:15px!important;width:106px!important;font:12px/15px arial!important;height:60px!important;margin:3px 0 0 0!important;padding:0!important;overflow:hidden!important">lodis：一个JavaScript实现的本地Redis存储</font>
                    </a>
                </td>
                <td width="106" valign="top" style="padding:5px!important;margin:0!important;border-left:1px solid #dddddd!important">
                    <a title="Redis作者详谈2.4版本改进" style="text-decoration:none!important" href="http://app.wumii.com/ext/redirect.htm?url=http%3A%2F%2Fblog.nosqlfan.com%2Fhtml%2F2620.html&amp;from=http%3A%2F%2Fblog.nosqlfan.com%2Fhtml%2F2676.html">
                        <img style="margin:0!important;padding:2px!important;border:1px solid #dddddd!important;width:100px!important;height:100px!important" src="http://static.wumii.com/site_images/2011/07/30/20125005.png" width="100px" height="100px"><br>
                        <font size="-1" color="#333333" style="display:block!important;line-height:15px!important;width:106px!important;font:12px/15px arial!important;height:60px!important;margin:3px 0 0 0!important;padding:0!important;overflow:hidden!important">Redis作者详谈2.4版本改进</font>
                    </a>
                </td>
                <td width="106" valign="top" style="padding:5px!important;margin:0!important;border-left:1px solid #dddddd!important">
                    <a title="*nix、node.js、MongoDB 下一代的LAMP" style="text-decoration:none!important" href="http://app.wumii.com/ext/redirect.htm?url=http%3A%2F%2Fblog.nosqlfan.com%2Fhtml%2F639.html&amp;from=http%3A%2F%2Fblog.nosqlfan.com%2Fhtml%2F2676.html">
                        <img style="margin:0!important;padding:2px!important;border:1px solid #dddddd!important;width:100px!important;height:100px!important" src="http://pic.yupoo.com/iammutex/B8sPBDdj/square.jpg" width="100px" height="100px"><br>
                        <font size="-1" color="#333333" style="display:block!important;line-height:15px!important;width:106px!important;font:12px/15px arial!important;height:60px!important;margin:3px 0 0 0!important;padding:0!important;overflow:hidden!important">*nix、node.js、MongoDB 下一代的LAMP</font>
                    </a>
                </td>
        </tr>
    
    <tr>
        <td colspan="5" align="right">
            <a style="text-decoration:none!important" href="http://www.wumii.com/widget/relatedItems.htm" title="无觅相关文章插件">
                <font size="-1" color="#bbbbbb" style="display:block!important;font-family:arial!important;padding:5px 0!important;font-size:12px!important;color:#bbb!important">无觅</font>
            </a>
        </td>
    </tr>
</table><img src="http://www1.feedsky.com/t1/543387930/nosqlfan/feedsky/s.gif?r=http://item.feedsky.com/~feedsky/nosqlfan/~8149226/543387930/6253001/1/item.html" border="0" height="0" width="0"></p>