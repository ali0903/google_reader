---
layout: post
title:  "JavaScript写的网页迷宫游戏（三）"
date:   2012-11-26 23:16:55
author: oldj
categories: program
---

## JavaScript写的网页迷宫游戏（三）
### by oldj
### at 2012-11-26 23:16:55
### original <http://oldj.net/article/javascript-maze-3/?utm_source=rss&utm_medium=rss&utm_campaign=javascript-maze-3>

<p>最近学习了一下<a href="http://www.phonegap.com/">phoneGap</a>，把之前做的<a href="http://oldj.net/article/javascript-maze/">JavaScript写的网页迷宫游戏</a>改造了一下，做成了一个手机游戏。</p>
<p>游戏在我的第一代小米手机上的截图如下：</p>
<p><img style="display:block;margin-left:auto;margin-right:auto" src="http://oldj.net/wp-content/uploads/2012/11/jm-maze-1.png" alt="jm-maze" title="jm-maze-1.png" border="0" width="480" height="854"></p>
<p>你用浏览器访问<a href="http://oldj.net/static/jm-maze/">http://oldj.net/static/jm-maze/</a>，在线体验一下。或者也可以在Google Play上搜索“oldj.net”，安装这个<a href="https://play.google.com/store/apps/details?id=net.oldj&amp;feature=search_result#?t=W251bGwsMSwxLDEsIm5ldC5vbGRqIl0.">迷宫游戏的Android版本</a>。</p>
<p>开发过程中用到的主要技术有<a href="http://www.phonegap.com/">phoneGap</a>、<a href="http://coffeescript.org/">CoffeeScript</a>，另外，作为《<a href="http://www.ituring.com.cn/book/1002">深入浅出jQuery Mobile</a>》的译者之一，我也把<a href="http://jquerymobile.com/">jQuery Mobile</a>用上了，以便在一些效果的实现上偷懒。:)</p>
<p>我用的IDE是Intellij IDEA，有一个老外的博客详细介绍了<a href="http://therockncoder.blogspot.com/2012/07/android-intellij-idea-and-phonegap.html">在IDEA上配置phoneGap环境的步骤</a>，如果像我一样一开始不知道怎么配置，可以参见前面的链接。</p>
<p>最后，这个游戏还比较粗糙，我还没有花时间来做优化，在我的小米手机上感觉没有在电脑上那么流畅，如果将来有时间，我会继续改进。</p>
<p style="font-style:italic">前传：<a href="http://oldj.net/article/javascript-maze/">一</a>、<a href="http://oldj.net/article/javascript-maze-2/">二</a></p>
<table cellspacing="0" cellpadding="3" border="0" style="clear:both">
    
    <tr>
        <td colspan="5"><b><font size="-1" style="display:block!important;padding:20px 0 5px!important">您可能也喜欢</font></b></td>
    </tr>
    
        <tr>
                <td width="102" valign="top" style="padding:5px!important;margin:0!important">
                    <a title="JavaScript 写的网页迷宫游戏（二）" style="text-decoration:none!important" href="http://app.wumii.com/ext/redirect?url=http%3A%2F%2Foldj.net%2Farticle%2Fjavascript-maze-2%2F&amp;from=http%3A%2F%2Foldj.net%2Farticle%2Fjavascript-maze-3%2F">
                        <img style="margin:0!important;padding:2px!important;border:1px solid #dddddd!important;width:96px!important;height:96px!important" src="http://static.wumii.cn/site_images/ti/hSf3UnKu.png?i=fkiUyxvI" width="96px" height="96px"><br>
                        <font size="-1" color="#0063BE" style="display:block!important;line-height:15px!important;width:102px!important;font:12px/15px arial!important;height:60px!important;margin:3px 0 0 0!important;padding:0!important;overflow:hidden!important">JavaScript 写的网页迷宫游戏（二）</font>
                    </a>
                </td>
                <td width="102" valign="top" style="padding:5px!important;margin:0!important;border-left:1px solid #dddddd!important">
                    <a title="JavaScript写的网页迷宫游戏" style="text-decoration:none!important" href="http://app.wumii.com/ext/redirect?url=http%3A%2F%2Foldj.net%2Farticle%2Fjavascript-maze%2F&amp;from=http%3A%2F%2Foldj.net%2Farticle%2Fjavascript-maze-3%2F">
                        <img style="margin:0!important;padding:2px!important;border:1px solid #dddddd!important;width:96px!important;height:96px!important" src="http://static.wumii.cn/site_images/ti/ENPm6CbT.png?i=vxEOxRDB" width="96px" height="96px"><br>
                        <font size="-1" color="#0063BE" style="display:block!important;line-height:15px!important;width:102px!important;font:12px/15px arial!important;height:60px!important;margin:3px 0 0 0!important;padding:0!important;overflow:hidden!important">JavaScript写的网页迷宫游戏</font>
                    </a>
                </td>
                <td width="102" valign="top" style="padding:5px!important;margin:0!important;border-left:1px solid #dddddd!important">
                    <a title="HTML5 版塔防游戏" style="text-decoration:none!important" href="http://app.wumii.com/ext/redirect?url=http%3A%2F%2Foldj.net%2Farticle%2Fhtml5-tower-defense%2F&amp;from=http%3A%2F%2Foldj.net%2Farticle%2Fjavascript-maze-3%2F">
                        <img style="margin:0!important;padding:2px!important;border:1px solid #dddddd!important;width:96px!important;height:96px!important" src="http://static.wumii.cn/site_images/ti/hP3FdrY6.png?i=LuW05TNM" width="96px" height="96px"><br>
                        <font size="-1" color="#0063BE" style="display:block!important;line-height:15px!important;width:102px!important;font:12px/15px arial!important;height:60px!important;margin:3px 0 0 0!important;padding:0!important;overflow:hidden!important">HTML5 版塔防游戏</font>
                    </a>
                </td>
                <td width="102" valign="top" style="padding:5px!important;margin:0!important;border-left:1px solid #dddddd!important">
                    <a title="在HTML5中翻转图片" style="text-decoration:none!important" href="http://app.wumii.com/ext/redirect?url=http%3A%2F%2Foldj.net%2Farticle%2Fflip-images-in-html5%2F&amp;from=http%3A%2F%2Foldj.net%2Farticle%2Fjavascript-maze-3%2F">
                        <img style="margin:0!important;padding:2px!important;border:1px solid #dddddd!important;width:96px!important;height:96px!important" src="http://static.wumii.cn/site_images/ti/cF2ENFXa.png?i=YzF28TSD" width="96px" height="96px"><br>
                        <font size="-1" color="#0063BE" style="display:block!important;line-height:15px!important;width:102px!important;font:12px/15px arial!important;height:60px!important;margin:3px 0 0 0!important;padding:0!important;overflow:hidden!important">在HTML5中翻转图片</font>
                    </a>
                </td>
                <td width="102" valign="top" style="padding:5px!important;margin:0!important;border-left:1px solid #dddddd!important">
                    <a title="不用HTML5/CSS3如何在网页上画机器猫" style="text-decoration:none!important" href="http://app.wumii.com/ext/redirect?url=http%3A%2F%2Foldj.net%2Farticle%2Fweb-doraemon-without-html5-or-css3%2F&amp;from=http%3A%2F%2Foldj.net%2Farticle%2Fjavascript-maze-3%2F">
                        <img style="margin:0!important;padding:2px!important;border:1px solid #dddddd!important;width:96px!important;height:96px!important" src="http://static.wumii.cn/site_images/ti/k7oZXTn.jpg?i=JirO170d" width="96px" height="96px"><br>
                        <font size="-1" color="#0063BE" style="display:block!important;line-height:15px!important;width:102px!important;font:12px/15px arial!important;height:60px!important;margin:3px 0 0 0!important;padding:0!important;overflow:hidden!important">不用HTML5/CSS3如何在网页上画机器猫</font>
                    </a>
                </td>
        </tr>
    
    <tr>
        <td colspan="5" align="right">
            <a style="text-decoration:none!important" href="http://www.wumii.com/widget/relatedItems" title="无觅相关文章插件">
                <font size="-1" color="#bbbbbb" style="display:block!important;font-family:arial!important;padding:5px 0!important;font-size:12px!important;color:#bbb!important">无觅</font>
            </a>
        </td>
    </tr>
</table><img src="http://www1.feedsky.com/t1/724263225/oldj/feedsky/s.gif?r=http://oldj.net/article/javascript-maze-3/?utm_source=rss&amp;utm_medium=rss&amp;utm_campaign=javascript-maze-3" border="0" height="0" width="0">