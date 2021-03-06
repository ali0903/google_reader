---
layout: post
title:  "Why Erlang"
date:   2011-03-16 20:15:26
author: yedingding
categories: program
---

## Why Erlang
### by yedingding
### at 2011-03-16 20:15:26
### original <http://heikezhi.com/2011/03/16/why-erlang/>

<p><a href="http://inagist.com/">In-A-Gist</a>是一个从实时数据流里汇总信息, 并实时地显示信息流行趋势的服务. 目前的主要信息来源是Twitter. 如果你有chrome/safari, 可以访问<a href="http://justinbieber.inagist.com">justinbieber.inagist.com</a>, <a href="http://libya.inagist.com">libya.inagist.com</a>了解一下这个服务. In-A-Gist在他们的官方博客里面介绍了这个应用背后的一些技术, 是用erlang和websocket构建的, 解释了为什么他们选择erlang这个偏冷门的语言. 原文在<a href="http://blog.inagist.com/why-erlang?c=1">http://blog.inagist.com/why-erlang?c=1</a>.</p>
<p><strong>轻量级进程</strong></p>
<p>这个服务和其他服务最主要的区别就是如何实时的概括Tweet流. In-A-Gist并不是简单的把所有的tweet都存储下来做分析, 而是每个用户有自己的一个缓存区, 新的tweets会先进入这个区里. 当某个tweet或者其中的关键字被重复了多次以后, 这个tweet就会被提取出来. 这个过程就是Erlang的优势所在. 每个流消费者对应一个Erlang进程, 轻量级并且独立. 这个进程本质上就是一个用户代理, 获取tweets, 存入缓冲区, 最后向服务器提供数据. 从技术上而言, 这个进程是一个gen_server + supervisor的实现. 所以即使某个进程死掉的啊supervisor也会重启启动这个进程, 对其他的进程不会有任何的影响.</p>
<p><strong>消息</strong></p>
<p>那么这个进程和数据流之间是如何结合起来的呢. 这中间就多了一层: tweet处理进程组. 每个进程组维护tweet消费进程, 当从twitter streaming api里得到一个新的tweet后, 就会创建一个新的进程, 解码json流, 然后向下发消息给了之前提到的缓存区维护进程. 由于这种分散的架构, 系统很容易的分布开.</p>
<p><strong>分布式的Erlang</strong></p>
<p>很容易想到如果tweet疯涨的话, 那么跟着每一层的消费进程也需要疯涨, 系统的压力就会增加. 这里Erlang又派上用场了. Erlang是为了分布而生的, 原生就是分布式的. 所以进程组并不需要在同一台机器上, 而是可以跨机器存在. 也就是说每个tweet消费进程是分布在不同机器上的, 很容易地就实现了扩展性. 而且, 即使其中的机器当机了也不会影响集群里的其他机器继续提供服务.</p>
<p><strong>实时传送</strong></p>
<p>为了让消息能实时的显示在客户端, 这里使用了websockets. 当缓存区里tweets被分析出来属于热门消息时, 就会从区里提取出来, 转化为JSON对象, 然后经由WebSocket传送到客户端. 作者发布了一个<a href="https://chrome.google.com/extensions/detail/oangdphebgapkakpmiiceehanhopodgo">chrome浏览器插件</a>, 有兴趣的可以看一下.</p>
<p><strong>流搜索</strong></p>
<p>作者在这里还介绍了一下他们是如何用Riak来做存储和搜索系统的. Riak是Erlang写的一个NoSQL系统. 作者在Riak的基础上开发了流搜索功能. 有兴趣的可以看原文.</p>
<p>这片文章主要介绍了Erlang的优势和适合做怎样的系统. 大家有问题可以去博客上留言或者mention @jebui.</p>
<table cellspacing="0" cellpadding="3" border="0" style="clear:both">
    
    <tr>
        <td colspan="4"><b><font size="-1" style="display:block!important;padding:20px 0 5px!important">无觅猜您也喜欢：</font></b></td>
    </tr>
    
        <tr>
                <td width="102" valign="top" style="padding:5px!important;margin:0!important">
                    <a title="Landing Page进化史" style="text-decoration:none!important" href="http://app.wumii.com/ext/redirect.htm?url=http%3A%2F%2Fheikezhi.com%2F2011%2F03%2F18%2Flanding-page-evolution%2F&amp;from=http%3A%2F%2Fheikezhi.com%2F2011%2F03%2F16%2Fwhy-erlang%2F">
                        <img style="margin:0!important;padding:2px!important;border:1px solid #dddddd!important;width:96px!important;height:96px!important" src="http://static.wumii.com/site_images/2011/03/19/3819722.jpg" width="96px" height="96px"><br>
                        <font size="-1" color="#333333" style="display:block!important;line-height:15px!important;width:102px!important;font:12px/15px arial!important;height:60px!important;margin:3px 0 0 0!important;padding:0!important;overflow:hidden!important">Landing Page进化史</font>
                    </a>
                </td>
                <td width="102" valign="top" style="padding:5px!important;margin:0!important;border-left:1px solid #dddddd!important">
                    <a title="Hello world!" style="text-decoration:none!important" href="http://app.wumii.com/ext/redirect.htm?url=http%3A%2F%2Fheikezhi.com%2F2011%2F03%2F11%2Fhello-world%2F&amp;from=http%3A%2F%2Fheikezhi.com%2F2011%2F03%2F16%2Fwhy-erlang%2F">
                        <img style="margin:0!important;padding:2px!important;border:1px solid #dddddd!important;width:96px!important;height:96px!important" src="http://static.wumii.com/images/blogWidget/wordpress_default.gif" width="96px" height="96px"><br>
                        <font size="-1" color="#333333" style="display:block!important;line-height:15px!important;width:102px!important;font:12px/15px arial!important;height:60px!important;margin:3px 0 0 0!important;padding:0!important;overflow:hidden!important">Hello world!</font>
                    </a>
                </td>
                <td width="102" valign="top" style="padding:5px!important;margin:0!important;border-left:1px solid #dddddd!important">
                    <a title="将任何网页变成Katamari Damacy游戏" style="text-decoration:none!important" href="http://app.wumii.com/ext/redirect.htm?url=http%3A%2F%2Fheikezhi.com%2F2011%2F03%2F13%2Fkatamari-hack%2F&amp;from=http%3A%2F%2Fheikezhi.com%2F2011%2F03%2F16%2Fwhy-erlang%2F">
                        <img style="margin:0!important;padding:2px!important;border:1px solid #dddddd!important;width:96px!important;height:96px!important" src="http://static.wumii.com/site_images/2011/03/14/3502487.png" width="96px" height="96px"><br>
                        <font size="-1" color="#333333" style="display:block!important;line-height:15px!important;width:102px!important;font:12px/15px arial!important;height:60px!important;margin:3px 0 0 0!important;padding:0!important;overflow:hidden!important">将任何网页变成Katamari Damacy游戏</font>
                    </a>
                </td>
                <td width="102" valign="top" style="padding:5px!important;margin:0!important;border-left:1px solid #dddddd!important">
                    <a title="世界，你准备好了吗？" style="text-decoration:none!important" href="http://app.wumii.com/ext/redirect.htm?url=http%3A%2F%2Fheikezhi.com%2F2011%2F03%2F11%2Flets-hack-now%2F&amp;from=http%3A%2F%2Fheikezhi.com%2F2011%2F03%2F16%2Fwhy-erlang%2F">
                        <img style="margin:0!important;padding:2px!important;border:1px solid #dddddd!important;width:96px!important;height:96px!important" src="http://static.wumii.com/site_images/2011/03/13/3474551.jpg" width="96px" height="96px"><br>
                        <font size="-1" color="#333333" style="display:block!important;line-height:15px!important;width:102px!important;font:12px/15px arial!important;height:60px!important;margin:3px 0 0 0!important;padding:0!important;overflow:hidden!important">世界，你准备好了吗？</font>
                    </a>
                </td>
        </tr>
    
    <tr>
        <td colspan="4" align="right">
            <a style="text-decoration:none!important" href="http://www.wumii.com/widget/relatedItems.htm" title="无觅相关文章插件">
                <font size="-1" color="#bbbbbb" style="display:block!important;font-family:arial!important;padding:5px 0!important;font-size:12px!important;color:#bbb!important">无觅</font>
            </a>
        </td>
    </tr>
</table><img src="http://www1.feedsky.com/t1/486527138/heikezhi/feedsky/s.gif?r=http://heikezhi.com/2011/03/16/why-erlang/" border="0" height="0" width="0"><p><a href="http://www1.feedsky.com/r/l/feedsky/heikezhi/486527138/art01.html"><img border="0" ismap src="http://www1.feedsky.com/r/i/feedsky/heikezhi/486527138/art01.gif"></a></p>