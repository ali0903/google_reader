---
layout: post
title:  "经过一年时间的沉淀 再次回首 TCP Socket服务器编程"
date:   2010-06-27 02:54:00
author: 辰
categories: program
---

## 经过一年时间的沉淀 再次回首 TCP Socket服务器编程
### by 辰
### at 2010-06-27 02:54:00
### original <http://www.cnblogs.com/zc22/archive/2010/06/27/1766007.html>

<p><a href="http://www.cnblogs.com/zc22/"><img src="http://pic.cnblogs.com/face/u19065.jpg" alt="" border="0"></a><br>作者: <a href="http://www.cnblogs.com/zc22/">辰</a> 发表于 2010-06-27 02:54 <a href="http://www.cnblogs.com/zc22/archive/2010/06/27/1766007.html">原文链接</a> 阅读: 1485 评论: 40</p><p>------------------</p>
<p><strong>前言</strong></p>
<p>------------------</p>
<p>开发了这么多年，发现最困难的程序开发就是通讯系统。</p>
<p> </p>
<p>其他大部分系统，例如CRM/CMS/权限框架/MIS之类的，无论怎么复杂，基本上都能够本地代码本地调试，性能也不太重要。（也许这个就是.net的企业级开发的战略吧）</p>
<p> </p>
<p>可是来到通讯系统，一切变得困难复杂。原因实在太多了，如：</p>
<ul><li><strong>性能永远是第一位</strong>：有时候一个if判断都要考虑性能，毕竟要损耗一个CPU指令，而在通讯系统服务器，每秒钟都产生上百万级别的通讯量，这样一个if就浪费了1个毫秒了。</li><li><strong>系统环境极其恶劣</strong>：所有我们可以想象的恶意攻击、异常输入等都要考虑；</li><li><strong>网络说断就断</strong>：在socket环境下，客户端可以以各种理由断开链接，而且服务器根本不会知道，连一个流水作业的业务逻辑都无法保证正常执行，因此需要设计各种辅助的协议、架构去监督。</li><li><strong>各种网络链接问题</strong>：例如代理、防火墙等等。。。</li></ul>
<p>经过了1年的跌跌撞撞，我总算收获了点有用的经验，本文先从设计角度介绍一些我在Socket编程中的经验，下一篇在放出源代码。</p>
<p> </p>
<p>------------------</p>
<p><strong>现有的Socket编程资源</strong></p>
<p>------------------</p>
<p>1. 首选推荐开源的XMPP框架，也就是Google的Gtalk的开源版本。里面的架构写的非常漂亮。特点就是：简洁、清晰。</p>
<p> </p>
<p>2. 其次推荐LumaQQ.net，这套框架本身写的一般般，但是腾讯的服务器非常的猛，这样必然导致客户端也要比较猛。通过学习这套框架，能够了解腾讯的IM传输协议设计，而且他们的协议是TCP/UDP结合，一举两得。</p>
<p> </p>
<p>3. 最后就是DotMsn。这个写的实在很一般般，而且也主要针对了MSN的协议特点。是能够学习到一点点的框架知识的，不过要有所鉴别。</p>
<p> </p>
<p>------------------</p>
<p><strong>Socket的选择</strong></p>
<p>------------------</p>
<p>在Java，到了Java5终于出现了异步编程，NIO，于是各种所谓的框架冒了出来，例如MINA, xsocket等等；而在.NET，微软一早就为我们准备好了完善的Socket模型。主要包括：同步Socket、异步Socket；我还听说了.net 3.x之后，异步的Socket内置了完成端口。综合各种模型的性能，我总结如下：</p>
<p> </p>
<p>1. 如果是短链接，使用同步socket。例如http服务器、转接服务器等等。</p>
<p> </p>
<p>2. 如果是长链接，使用异步socket。例如通讯系统（QQ / Fetion）、webgame等。</p>
<p> </p>
<p>3. .net的异步socket的连接数性能在 7500/s（每秒并发7500个socket链接）。而听说完成端口在1.5w所有。但是我到目前还没有正式见过所谓的完成端口，不知道到底有多牛逼。</p>
<p> </p>
<p>4. 我听说了java的NIO性能在5000/s所有，我们项目内部也进行了链接测试，在4000~5000比较稳定，当然如果代码调优之后，能提高一点点。</p>
<p> </p>
<p>------------------</p>
<p><strong>TCP </strong><strong>Socket协议定义</strong></p>
<p>------------------</p>
<p>本文从这里开始，主要介绍TCP的socket编程。</p>
<p>新手们（例如当初的我），第一次写socket，总是以为在发送方压入一个&quot;Helloworld&quot;,接收方收到了这个字符串，就“精通”了Socket编程了。而实际上，这种编程根本不可能用在现实项目，因为：</p>
<p> </p>
<p>1. socket在传输过程中，helloworld有可能被拆分了，分段到达客户端），例如 hello   +   world，一个分段就是一个包（Package），这个就是<strong><font color="#ff0000">分包问题</font></strong>。</p>
<p> </p>
<p>2. socket在传输过成功，不同时间发送的数据包有可能被合并，同时到达了客户端，这个就是<strong style="color:red">黏包</strong><strong style="color:red">问题</strong>。例如发送方发送了hello+world,而接收方可能一次就接受了helloworld.</p>
<p> </p>
<p>3. socket会自动在每个包后面补n个 0x0 byte，分割包。具体怎么去补，这个我就没有深入了解。</p>
<p> </p>
<p>4. 不同的数据类型转化为byte的长度是不同的，例如int转为byte是4位（int32），这样我们在制作socket协议的时候要特别小心了。具体可以使用以下代码去测试：</p>
<div><img alt="" src="http://images.cnblogs.com/OutliningIndicators/ContractedBlock.gif"><img src="http://images.cnblogs.com/OutliningIndicators/ExpandedBlockStart.gif"><span>代码</span> 
<div>
<div><span style="color:#000000">        </span><span style="color:#0000ff">public</span><span style="color:#000000"> </span><span style="color:#0000ff">void</span><span style="color:#000000"> test()<br>        {<br>            </span><span style="color:#0000ff">int</span><span style="color:#000000"> myInt </span><span style="color:#000000">=</span><span style="color:#000000"> </span><span style="color:#800080">1</span><span style="color:#000000">;<br>            </span><span style="color:#0000ff">byte</span><span style="color:#000000">[] bytes </span><span style="color:#000000">=</span><span style="color:#000000"> </span><span style="color:#0000ff">new</span><span style="color:#000000"> </span><span style="color:#0000ff">byte</span><span style="color:#000000">[</span><span style="color:#800080">1024</span><span style="color:#000000">];<br>            BinaryWriter writer </span><span style="color:#000000">=</span><span style="color:#000000"> </span><span style="color:#0000ff">new</span><span style="color:#000000"> BinaryWriter(</span><span style="color:#0000ff">new</span><span style="color:#000000"> MemoryStream(bytes));<br>            writer.Write(myInt);<br>            writer.Write(</span><span style="color:#800000">"</span><span style="color:#800000">j</span><span style="color:#800000">"</span><span style="color:#000000">);<br>            writer.Close();<br>        }</span></div></div></div>
<p> </p>
<p> </p>
<p>尽管socket环境如此恶劣，但是TCP的链接也至少保证了：</p>
<ul><li>包发送顺序在传输过程中是不会改变的，例如发送方发送 H E L L，那么接收方一定也是<strong>顺序收到</strong>H E L L，这个是TCP协议承诺的，因此这点成为我们解决分包、黏包问题的关键。</li><li>如果发送方发送的是helloworld, 传输过程中分割成为hello+world，那么TCP<strong>保证了</strong>在hello与world之间没有其他的byte。但是<strong>不能保证</strong>helloworld和下一个命令之间没有其他的byte。</li></ul>
<p> </p>
<p>因此，如果我们要使用socket编程，就一定要编写自己的协议。目前业界主要采取的协议定义方式是：<strong>包头+包体长度+包体</strong>。具体如下：</p>
<p> </p>
<p>1. 一般包头使用一个int定义，例如int = 173173173；作用是区分每一个有效的数据包，因此我们的服务器可以通过这个int去切割、合并包，组装出完整的传输协议。有人使用回车字符去分割包体，例如常见的SMTP/POP协议，这种做法在特定的协议是没有问题的，可是如果我们传输的信息内容自带了回车字符串，那么就糟糕了。所以在设计协议的时候要特别小心。</p>
<p> </p>
<p>2. 包体长度使用一个int定义，这个长度表示包体所占的比特流长度，用于服务器正确读取并分割出包。</p>
<p> </p>
<p>3. 包体就是自定义的一些协议内容，例如是对像序列化的内容（现有的系统已经很常见了，使用对象序列化、反序列化能够极大简化开发流程，等版本稳定后再转入手工压入byte操作）。</p>
<p> </p>
<p>一个实际编写的例子：比如我要传输2个整型 int = 1, int = 2，那么实际传输的数据包如下：</p>
<p>   173173173               8                  1         2</p>
<p>|------包头------|----包体长度----|--------包体--------|</p>
<p>这个数据包就是4个整型，总长度 = 4*4  = 16。 </p>
<p> </p>
<p><strong>说说我走的弯路：</strong></p>
<p>我曾经偷懒，使用特殊结束符去分割包体，这样传输的数据包就不需要指名长度了。可是后来高人告诉我，如果使用特殊结束符去判断包，性能会损失很大，因为我们每次读取一个byte，都要做一次if判断，这个性能损失是非常严重的。所以最终还是走主流，使用以上的结构体。</p>
<p> </p>
<p> </p>
<p>------------------</p>
<p><strong>Socket接收的逻辑概述</strong></p>
<p>------------------</p>
<p>针对了我们的数据包设计+socket的传输特点，我们的接收逻辑主要是：</p>
<p>1. 寻找包头。这个包头就是一个int整型。但是写代码的时候要非常注意，一个int实际上占据了4个byte，而可悲的是这4个byte在传输过程中也可能被socket 分割了，因此读取判断的逻辑是：</p>
<ul><li>判断剩余长度是否大于4</li><li>读取一个int，判断是否包头，如果是就跳出循环。</li><li>如果不是包头，则倒退3个byte，回到第一点。</li><li>如果读取完毕也没有找到，则有可能包头被分割了，因此当前已读信息压入接收缓存，等待下一个包到达后合并判断。</li></ul>
<p>2. 读取包体长度。由于长度也是一个int，因此判断的时候也要小心，同上。</p>
<p>3. 读取包体，由于已知包体长度，因此读取包体就变得非常简单了，只要一直读取到长度未知，剩余的又回到第一条寻找包头。</p>
<p> </p>
<p>这个逻辑不要小看，就这点东西忙了我1天时间。而非常奇怪的是，我发现c#写的socket，似乎没有我说的这么复杂逻辑。大家可以看看LumaQQ.net / DotMsn等，他们的socket接收代码都非常简单。我猜想：要么是.net的socket进行了优化，不会对int之类的进行分割传输；要么就是作者偷懒，随便写点代码开源糊弄一下。<br></p>
<p> </p>
<p>------------------</p>
<p><strong>Socket服务器参数概述</strong></p>
<p>------------------</p>
<p>我在开篇也说了，Socket服务器的环境是非常糟糕了，最糟糕的就是客户端断线之后服务器没有收到通知。 因为socket断线这个也是个信息，也要从客户端传递到我们socket服务器。有可能网络阻塞了，导致服务器连断开的通知都没有收到。</p>
<p>因此，我们写socket服务器，就要面对2个环境：</p>
<p>1. 服务器在处理业务逻辑中的任何时候都会收到Exception, 任何时候都会因为链接中断而断开。</p>
<p>2. 服务器接收到的客户端请求可以是任意字符串，因此在处理业务逻辑的时候，必须对各种可能的输入都判断，防止恶意攻击。</p>
<p> </p>
<p>针对以上几点，我们的服务器设计必须包含以下参数：</p>
<p>1. 客户端链接时间记录：主要判断客户端空连接情况，防止连接数被恶意占用。</p>
<p>2. 客户端请求频率记录：要防止客户端频繁发送请求导致服务器负荷过重。</p>
<p>3. 客户端错误记录：一次错误可能导致服务器产生一次exception，而这个性能损耗是非常严重的，因此要严格监控客户端的发送协议错误情况。</p>
<p>4. 客户端发送信息长度记录：有可能客户端恶意发送非常长的信息，导致服务器处理内存爆满，直接导致宕机。</p>
<p> </p>
<p>5. 客户端短时间暴涨：有可能在短时间内，客户端突然发送海量数据，直接导致服务器宕机。因此我们必须有对服务器负荷进行监控，一旦发现负荷过重，直接对请求的socket返回处理失败，例如我们常见的“404”。</p>
<p> </p>
<p>6. 服务器短时间发送信息激增：有可能在服务器内部处理逻辑中，突然产生了海量的数据需要发送，例如游戏中的“群发”；因此必须对发送进行队列缓存，然后进行合并发送，减轻socket的负荷。</p>
<p> </p>
<p> </p>
<p>------------------</p>
<p><strong>后记</strong></p>
<p>------------------</p>
<p>本文从架构设计分析了一个socket服务器的设计要点。如果您有其他见解，欢迎留言与讨论。</p><img src="http://www.cnblogs.com/zc22/aggbug/1766007.html?type=1" width="1" height="1" alt=""><p>评论: 40　<a href="http://www.cnblogs.com/zc22/archive/2010/06/27/1766007.html#pagedcomment">查看评论</a>　<a href="http://www.cnblogs.com/zc22/archive/2010/06/27/1766007.html#commentform">发表评论</a></p><p><a href="http://a4.yeshj.com/rd/35451/">软件研发团队管理年会(上海，7.10-7.11)</a></p><hr><p>最新新闻：<br>· <a href="http://news.cnblogs.com/n/67047/">Google发布Chrome补丁 修补多项安全</a><span style="color:gray">(2010-06-27 18:45)</span><br>· <a href="http://news.cnblogs.com/n/67046/">程序员必看电影：Java 4-ever</a><span style="color:gray">(2010-06-27 16:08)</span><br>· <a href="http://news.cnblogs.com/n/67045/">微软高管用数据来说话：即时通讯全球第一</a><span style="color:gray">(2010-06-27 16:02)</span><br>· <a href="http://news.cnblogs.com/n/67044/">图说iPhone历史</a><span style="color:gray">(2010-06-27 15:56)</span><br>· <a href="http://news.cnblogs.com/n/67043/">iPhone4 3G网络上传速度大幅提升</a><span style="color:gray">(2010-06-27 15:51)</span><br></p><p>编辑推荐：<a href="http://news.cnblogs.com/n/67046/">程序员必看电影：Java 4-ever</a><br></p><p>网站导航：<a href="http://www.cnblogs.com">博客园首页</a>  <a href="http://home.cnblogs.com/">个人主页</a>  <a href="http://news.cnblogs.com">新闻</a>  <a href="http://home.cnblogs.com/ing/">闪存</a>  <a href="http://home.cnblogs.com/group/">小组</a>  <a href="http://space.cnblogs.com/q/">博问</a>  <a href="http://space.cnblogs.com">社区</a>  <a href="http://kb.cnblogs.com">知识库</a></p>