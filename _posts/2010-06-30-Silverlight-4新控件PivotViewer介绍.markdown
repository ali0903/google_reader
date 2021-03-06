---
layout: post
title:  "Silverlight 4新控件PivotViewer介绍"
date:   2010-06-30 07:02:00
author: jv9
categories: program
---

## Silverlight 4新控件PivotViewer介绍
### by jv9
### at 2010-06-30 07:02:00
### original <http://www.cnblogs.com/jv9/archive/2010/06/30/1767989.html>

<p>作者: <a href="http://www.cnblogs.com/jv9/">jv9</a> 发表于 2010-06-30 07:02 <a href="http://www.cnblogs.com/jv9/archive/2010/06/30/1767989.html">原文链接</a> 阅读: 1414 评论: 5</p><p>早在Silverlight 4正式版发布之前，微软发布PivotViewer控件的演示版本，其处理以及展示大批量数据的功能吸引不少开发人员和终端客户，今天微软发布了PivotViewer控件正式版本，本文将介绍一下PivotViewer控件。</p>
<p> </p>
<p><strong>首先了解一下PivotViewer控件的功能是什么？</strong></p>
<p>简单的理解，PivotViewer控件是一款处理并展示大批量数据的Silverlight控件。在官方的解释PivotViewer控件可在较短时间内载入近千条数据，并将其进行可视化处理，在使用过程中，结合Deep Zoom技术，可以轻松阅览高分辨率的数据内容。不仅如此，PivotViewer控件结合其完美的动画效果提高用户交互体验。</p>
<p><img style="width:646px;height:329px" border="0" alt="" src="http://images.cnblogs.com/cnblogs_com/jv9/Image00451.jpg" width="646" height="329"></p>
<p> </p>
<p><strong>PivotViewer控件的应用范围？</strong></p>
<p>PivotViewer控件的优势是处理大量数据，将其数据进行可视化处理，并展示给终端客户。其功能决定该控件可被应用于商业智能分析，零售业展示，图片图像展示，学术研究展示等方向。</p>
<p><img style="width:646px;height:376px" border="0" alt="" src="http://images.cnblogs.com/cnblogs_com/jv9/Image00450.jpg" width="646" height="376"></p>
<p> </p>
<p><strong>PivotViewer控件的数据请求？</strong></p>
<p>从下图可以看到PivotViewer控件获取数据和传统Web数据请求相似，只是传统型Web请求，返回的是图片或者是HTML文本，而PivotViewer控件的Web请求，返回的是一个Collection数据集合文件，其文件后缀名也和传统的不同，分别是CXML,DZC,DZI. 在文件获取成功后，PivotViewer控件将对数据进行显示。</p>
<p>CXML格式是XML格式的数据集合文件；</p>
<p>DZI格式是Deep Zoom图片文件；</p>
<p>DZC格式是DZI的数据集合文件。</p>
<p>如果需要了解以上格式，可以访问<a href="http://msdn.microsoft.com/en-us/library/cc645077(VS.95).aspx">这里</a>。</p>
<p><img border="0" alt="" src="http://images.cnblogs.com/cnblogs_com/jv9/untitled0629.jpg" width="578" height="210"></p>
<p> </p>
<p><strong>如何在开发中使用PivotViewer控件？</strong></p>
<p>在使用PivotViewer控件前，首先需要满足其环境需求，</p>
<p>1. <a href="http://go.microsoft.com/?linkid=9725128">Visual Studio 2010；</a></p>
<p>2. <a href="http://go.microsoft.com/fwlink/?LinkID=177428">Silverlight Tools for Visual Studio 2010</a></p>
<p>3. <a href="http://silverlight.codeplex.com/">Silverlight 4 Toolkit（April 2010）</a></p>
<p>4. <a href="http://www.microsoft.com/downloads/details.aspx?displaylang=en&amp;FamilyID=9a1bb862-d80c-4145-9320-b279a63bff91">PrivotViewer控件类库</a></p>
<p>5. 添加MIME类型到Web服务器，分别是:</p>
<p>    CXML - text/xml</p>
<p>    DZC  - text/xml</p>
<p>    DZI  - text/xml</p>
<p>6. 对于跨域读取访问，需要添加clientaccesspolicy.xml策略文件到Web服务器根目录。</p>
<p> </p>
<p>在满足以上条件后，可创建新VS2010项目，在页面中添加PivotViewer控件，即可查看使用效果。</p>
<p> </p>
<div>
<div><span style="color:#008080">1</span> <span style="color:#0000ff">&lt;</span><span style="color:#800000">local:CustomPivotViewer </span><span style="color:#ff0000">x:Name</span><span style="color:#0000ff">="PivotViewer"</span><span style="color:#ff0000"><br></span><span style="color:#008080">2</span> <span style="color:#ff0000">    Grid.Row</span><span style="color:#0000ff">="0"</span><span style="color:#ff0000"> Grid.Column</span><span style="color:#0000ff">="0"</span><span style="color:#ff0000"><br></span><span style="color:#008080">3</span> <span style="color:#ff0000">    LinkClicked</span><span style="color:#0000ff">="PivotViewer_LinkClicked"</span><span style="color:#ff0000"><br></span><span style="color:#008080">4</span> <span style="color:#ff0000">    ItemDoubleClicked</span><span style="color:#0000ff">="PivotViewer_ItemDoubleClicked"</span><span style="color:#0000ff">/&gt;</span></div></div>
<p> </p>
<p>在<a href="http://www.microsoft.com/downloads/details.aspx?displaylang=en&amp;FamilyID=9a1bb862-d80c-4145-9320-b279a63bff91">PrivotViewer控件类库</a>安装完成后，会在Microsoft SDKs\Silverlight\v4.0目录创建PivotViewer目录，其中包含PivotViewer的DLL类库，以及演示代码。<a href="http://files.cnblogs.com/jv9/PivotViewerSampleXapCode.zip">下载演示代码</a></p>
<p> </p>
<p><strong>PivotViewer控件的学习资源和在线演示</strong></p>
<p>更多的PivotViewer控件学习资源，请大家访问微软Silverlight官网：<a href="http://www.silverlight.net/learn/pivotviewer/">http://www.silverlight.net/learn/pivotviewer/</a></p>
<p>PivotViewer控件的在线演示：</p>
<p><a href="http://www.hitched.co.uk/wedding-venues/visual-search.htm">http://www.hitched.co.uk/wedding-venues/visual-search.htm</a></p>
<p><a href="http://netflixpivot.cloudapp.net/">http://netflixpivot.cloudapp.net/</a></p>
<p> </p>
<p>PivotViewer控件就介绍到这里，Enjoy。</p>
<p> </p>
<p>欢迎大家加入"专注Silverlight" 技术讨论群：</p>
<div>32679955(六群) </div>
<div>23413513(五群) </div>
<div>32679922(四群) </div>
<div>100844510(三群) </div>
<p>37891947(二群) </p>
<p>22308706(一群) </p>
<p> </p>
<p> </p>
<p> </p>
<p> </p>
<p> </p><img src="http://www.cnblogs.com/jv9/aggbug/1767989.html?type=1" width="1" height="1" alt=""><p>评论: 5　<a href="http://www.cnblogs.com/jv9/archive/2010/06/30/1767989.html#pagedcomment">查看评论</a>　<a href="http://www.cnblogs.com/jv9/archive/2010/06/30/1767989.html#commentform">发表评论</a></p><p><a href="http://mpd.cnblogs.com/">软件研发团队管理年会(上海，7.10-7.11)</a></p><hr><p>最新新闻：<br>· <a href="http://news.cnblogs.com/n/67308/">商业周刊：谷歌 Android拓展中印市场</a><span style="color:gray">(2010-06-30 18:12)</span><br>· <a href="http://news.cnblogs.com/n/67307/">福布斯：数解微软</a><span style="color:gray">(2010-06-30 17:34)</span><br>· <a href="http://news.cnblogs.com/n/67306/">消息称淘宝将开展与网游联合运营合作</a><span style="color:gray">(2010-06-30 17:33)</span><br>· <a href="http://news.cnblogs.com/n/67303/">IPv4地址面临枯竭　IPv6大规模部署须提速</a><span style="color:gray">(2010-06-30 17:14)</span><br>· <a href="http://news.cnblogs.com/n/67302/">汉王：非革命者、非榜样</a><span style="color:gray">(2010-06-30 16:50)</span><br></p><p>编辑推荐：<a href="http://www.cnblogs.com/fzwudc/archive/2010/06/29/1767618.html">程序员的语言“艳遇史”（六）——网络工程师perl</a><br></p><p>网站导航：<a href="http://www.cnblogs.com">博客园首页</a>  <a href="http://home.cnblogs.com/">个人主页</a>  <a href="http://news.cnblogs.com">新闻</a>  <a href="http://home.cnblogs.com/ing/">闪存</a>  <a href="http://home.cnblogs.com/group/">小组</a>  <a href="http://space.cnblogs.com/q/">博问</a>  <a href="http://space.cnblogs.com">社区</a>  <a href="http://kb.cnblogs.com">知识库</a></p>