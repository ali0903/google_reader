---
layout: post
title:  "我有一个Hello World的C++程序编译不过"
date:   2011-04-02 16:20:34
author: 
categories: program
---

## 我有一个Hello World的C++程序编译不过
### by 
### at 2011-04-02 16:20:34
### original <http://news.cnblogs.com/n/96233/>

<p>     在StackOverflow上有这样<a href="http://stackoverflow.com/questions/5508110/why-is-this-program-erroneously-rejected-by-three-c-compilers">一个贴子</a>，楼主说，我有下面这样的一个C++程序，为什么编译不通过啊。其让我想起了以前的这两个帖子《<a title="编程真难啊" href="http://coolshell.cn/articles/1391.html">编程真难啊</a>》和《<a title="给我一个序列号" href="http://coolshell.cn/articles/1693.html">给我一个序列号</a>》。<strong>仅以此篇文章祝大家假期快乐吧</strong>。</p>
<p style="text-align:center"><img src="http://pic003.cnblogs.com/2011/1/201104/2011040216520732.png" alt=""></p>
<p style="text-align:center">hello world 程序</p>
<p>　　楼主还给出了相关的编译出错的信息（相信你一看就明白问题在哪里了，你应该还会发出一声“靠”！！！）</p>
<p>　　先是用Visual C++ 2010编译</p>
<p>　　<code>c:\dev&gt;cl </code><code>/nologo</code> <code>helloworld.png</code></p>
<p>　　<code>cl : Command line warning D9024 : unrecognized </code><code>source</code> <code>file</code> <code>type</code> <code>'helloworld.png'</code><code>, object </code><code>file</code> <code>assumed</code></p>
<p>　　<code>helloworld.png : fatal error LNK1107: invalid or corrupt </code><code>file</code><code>: cannot </code><code>read</code> <code>at 0x5172</code></p>
<p>　　再用G++ 4.5.2编译</p>
<p>　　<code>c:\dev&gt;g++ helloworld.png</code></p>
<p>　　<code>helloworld.png: </code><code>file</code> <code>not recognized: File </code><code>format</code> <code>not recognized</code></p>
<p>　　<code>collect2: ld returned 1 </code><code>exit</code> <code>status</code></p>
<p>　　再用clang编译</p>
<p>　　<code>c:\dev&gt;clang++ helloworld.png</code></p>
<p>　　<code>helloworld.png: </code><code>file</code> <code>not recognized: File </code><code>format</code> <code>not recognized</code></p>
<p>　　<code>collect2: ld returned 1 </code><code>exit</code> <code>status</code></p>
<p>　　<code>clang++: error: linker (via gcc) </code><code>command</code> <code>failed with </code><code>exit</code> <code>code 1 (use -</code><code>v</code> <code>to see invocation)</code></p>
<p>　　不过，最强大的，有人居然给出了一个fix，靠！</p>
<p style="text-align:center"><strong><a href="http://i.imgur.com/QlGpd.gif">点击链接</a></strong></p>
<p style="text-align:center">hello world 的解决方案（图片有点大4M，请耐心等待下载）</p>
<p>　　真是BT啊，呵呵。<strong>仅以此篇文章祝大家假期快乐吧</strong>。</p><p><br>　　本文链接：<a href="http://news.cnblogs.com/n/96233/">http://news.cnblogs.com/n/96233/</a></p><p>　　相关新闻：<br>　　· <a href="http://news.cnblogs.com/n/92536/">开发人员、程序员与计算机科学家三者之间的区别</a><span style="color:gray">(2011-03-01)</span><br>　　· <a href="http://news.cnblogs.com/n/94804/">经验丰富的程序员和代码行数</a><span style="color:gray">(2011-03-21)</span><br>　　· <a href="http://news.cnblogs.com/n/93239/">做一个女软件工程师的好处</a><span style="color:gray">(2011-03-08)</span><br>　　· <a href="http://news.cnblogs.com/n/92343/">（麻省理工免费课程）计算机科学和编程</a><span style="color:gray">(2011-02-28)</span><br>　　· <a href="http://news.cnblogs.com/n/92099/">5点钟起床，疯了吗？</a><span style="color:gray">(2011-02-25)</span><br></p><img src="http://news.cnblogs.com/news/rssclick.aspx?id=96233" width="1" height="1" alt="">