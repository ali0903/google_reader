---
layout: post
title:  "Google SPDY初探：HTTP 1.1之外的世界"
date:   2011-05-11 21:48:12
author: yuanyi
categories: program
---

## Google SPDY初探：HTTP 1.1之外的世界
### by yuanyi
### at 2011-05-11 21:48:12
### original <http://heikezhi.com/2011/05/11/life-beyond-http-11-googles-spdy/>

<p><img src="http://heikezhi.com/wp-content/uploads/2011/05/http-protocol.png" alt="" title="http-protocol" width="151" height="84"><a href="http://www.w3.org/People/Berners-Lee/">蒂姆.伯纳斯李</a>爵士以他早期在URI，HTTP以及HTML方面的卓越贡献而收获盛誉，正是他的这些努力促成了1996年HTTP 1.0规范(<a href="http://tools.ietf.org/html/rfc1945">RFC 1945</a>)的发布，1997年的HTTP 1.1协议草案(RFC 2068)以及1999年HTTP 1.1规范(<a href="http://tools.ietf.org/html/rfc2616">RFC 2616</a>)的发布，如果没有这些协议，我们所熟知的Web根本不会存在。</p>
<p>尽管如此，但是这些协议发布至今已经过去了十几个年头，全世界的网民数量已经达到10亿级别，我们的Web应用的体积和复杂度也在直线上升，当初HTTP规范已经已经不能适应这个新时代，Google的Chrome浏览器早在一年前就已经从地址栏移除了http://前缀，并且不仅如此，如果你使用Chrome浏览器，同时使用Google的网络服务，那么有可能，你已经不再是通过HTTP访问这些服务了，没错，很有可能你的浏览器使用的是SPDY——下面就让我们来一探究竟。</p>
<p><strong>HTTP的延迟问题</strong></p>
<p>当我们将每天工作的大部分时间都投入到我们的Web应用上时，延迟问题就变的举足轻重，不幸的是，这正是HTTP规范的痛处，最初的HTTP 1.0规范规定了在每一个单独的请求/相应之后都必须关闭与服务器间的连接：也就是说连接无法重用，必须为每次请求重新创建一条新的TCP连接——这是方式代价非常高。</p>
<p><img src="http://heikezhi.com/wp-content/uploads/2011/05/keepalive-pipelining.png" alt="" title="keepalive-pipelining" width="158" height="149">HTTP 1.1的最大改变就是针对这个问题的：RFC 2616 将连接默认为”<a href="http://en.wikipedia.org/wiki/HTTP_persistent_connection">keep-alive</a>“，并且介绍了一个”pipelining“的特性，keep-alive让我们可以在多个请求之前重用tcp连接，而<a href="http://en.wikipedia.org/wiki/HTTP_pipelining">pipelining</a>则让我们可以一次发起多个请求，两个都是很不错的改变，但不幸的是，keep-alive如今已经被大部分服务器和客户端所接受，但是pipelining却在今日的<a href="http://en.wikipedia.org/wiki/HTTP_pipelining">大多数浏览器中被默认禁用（除了opera）</a>。</p>
<p>尽管如此，即使我们<a href="http://tools.ietf.org/html/draft-nottingham-http-pipeline-01">部署了pipelining支持</a>，仍然还有一个无法解决的问题：HTTP严格限制请求必须以FIFO的方式被处理，因此如果在你的请求队列中有一个非常慢的动态请求，那么与它共用同一TCP通道的其它请求都必须等待这个请求先处理完成。</p>
<p><strong>SPDY：HTTP 1.1之外的世界</strong></p>
<p>SPDY（SPeeDY）是Google的一个研究项目，它旨在提供一个应用层的协议用于传输Web内容，SPDY最主要的目标就是降低网络延迟，这是他们在项目发布时的承诺：而根据与HTTP的对比测试，事实显示SPDY平均可以<a href="http://www.chromium.org/spdy/spdy-whitepaper">降低大约64%的页面读取时间</a>！同样的页面，同样的TCP管道，只是协议不同。</p>
<p><img src="http://heikezhi.com/wp-content/uploads/2011/05/spdy.png" alt="" title="spdy" width="129" height="216">关于SPDY的变化和特性简单说来就是：真正的<a href="http://www.chromium.org/spdy/spdy-protocol/spdy-protocol-draft2#TOC-Streams">请求pipelining</a>，没有FIFO限制，同时<a href="http://www.chromium.org/spdy/spdy-protocol/spdy-protocol-draft2#TOC-Framing-Layer">消息帧</a>机制可以有效简化客户端和服务端的开发，强制压缩（包括头部），调度优先级，甚至<a href="http://www.chromium.org/spdy/spdy-protocol/spdy-protocol-draft2#TOC-Server-Push-Transactions">双向通讯</a>（服务器push），让我们继续深入看看。</p>
<p>不同于HTTP，SPDY中的每个请求都有一个stream ID，这让我们可以使用单个TCP通道来在服务器和客户端之间并行传输数据：我们可以在同一时间通过同一个通道响应多个资源请求，只需要识别出数据对应的stream就可以了，为了实现这一点，SPDY协议定义了两种非常简单的二进制帧类型：<a href="http://www.chromium.org/spdy/spdy-protocol/spdy-protocol-draft2#TOC-Control-frames">控制帧</a>和<a href="http://www.chromium.org/spdy/spdy-protocol/spdy-protocol-draft2#TOC-Data-frames">数据帧</a>。</p>
<p>另外，在HTTP载荷被压缩时，HTTP头依然在以明文形式传输，而SPDY会使用一个预定义的字典来压缩所有的头部元数据，同样，因为SPDY支持真正的pipelining，我们就可以为每个资源请求分配一个优先级（比如HTML优先，JS第二），还有，为什么不让浏览器一次性发起所有请求呢？简单点说，如果服务器知道你要抓取那个页面，那么它就会在客户端解析完HTML之前”提示“客户端还需要加载哪些图片-服务器推送！</p>
<p><strong>SPDY应用</strong></p>
<p>事实上，Chomium项目已经完成了对SPDY的初步集成和测试，Google的chrome浏览器也已经内置了SPDY支持，并且如果没有意外，Google的服务器也已经支持SPDY了，换句话说，如果你使用chrome，那么当你访问Google的服务时，你已经在<a href="http://groups.google.com/group/spdy-dev/msg/dcc9c3a3e50c694f">使用SPDY加载页面</a>而不是http了。</p>
<p>想要自己体验SPDY？可以看看这个Apache插件（<a href="http://code.google.com/p/mod-spdy/">mod-spdy</a>），以及<a href="https://github.com/mnot/nbhttp/tree/spdy">Python实现</a>，或者<a href="https://github.com/igrigorik/spdy">Ruby的spdy gem</a>，目前这个Ruby的spdy解析器尚未完工，但是它已经可以解析收到的数据，并为SPDY客户端生成响应，可以试试这个hello world例子：</p>
<pre name="code">
$: &lt;&lt; &#39;lib&#39; &lt;&lt; &#39;../lib&#39;

require &#39;eventmachine&#39;
require &#39;spdy&#39;

class SPDYHandler &lt; EM::Connection
  def post_init
    @parser = SPDY::Parser.new
    @parser.on_headers_complete do |stream_id, associated_stream, priority, headers|
      p [:SPDY_HEADERS, headers]

      sr = SPDY::Protocol::Control::SynReply.new
      h = {&#39;Content-Type&#39; =&gt; &#39;text/plain&#39;, &#39;status&#39; =&gt; &#39;200 OK&#39;, &#39;version&#39; =&gt; &#39;HTTP/1.1&#39;}
      sr.create(:stream_id =&gt; 1, :headers =&gt; h)
      send_data sr.to_binary_s

      p [:SPDY, :sent, :SYN_REPLY]

      d = SPDY::Protocol::Data::Frame.new
      d.create(:stream_id =&gt; 1, :data =&gt; &quot;This is SPDY.&quot;)
      send_data d.to_binary_s

      p [:SPDY, :sent, :DATA]

      d = SPDY::Protocol::Data::Frame.new
      d.create(:stream_id =&gt; 1, :flags =&gt; 1)
      send_data d.to_binary_s

      p [:SPDY, :sent, :DATA_FIN]
    end

    start_tls
  end

  def receive_data(data)
    @parser &lt;&lt; data
  end

  def unbind
    p [:SPDY, :connection_closed]
  end
end

EM.run do
  EM.start_server &#39;0.0.0.0&#39;, 10000, SPDYHandler
end

# (1) start the SPDY eventmachine server
# &gt; ruby spdy_server.rb
#
# (2) start Chrome and force it to use SPDY over SSL.. on OSX:
# &gt; /Applications/Google\ Chrome.app/Contents/MacOS/Google\ Chrome --use-spdy=ssl
#
# (3) visit https://localhost:10000/
</pre>
<p><strong>通向HTTP 2.0，还是其它？</strong></p>
<p>SPDY提供了如此多的好处，但奇怪的却没有引起多大的反响，就连Google自己也在发布声明后就没了动静——低延迟的Web服务还是非常吸引人的，或许他们的低调是出于某种考虑吧，不管怎么说，这是一个开发的协议，并且值得关注。</p>
<p>本文翻译自： “<a href="http://www.igvita.com/2011/04/07/life-beyond-http-11-googles-spdy">Life beyond HTTP 1.1: Google’s SPDY</a>“, 作者：<a href="http://www.igvita.com/about/">Ilya Grigorik</a></p>
<p>想和我们一道传播黑客精神？<a href="http://heikezhi.com/join">快来加入吧！</a></p>
<table cellspacing="0" cellpadding="3" border="0" style="clear:both">
    
    <tr>
        <td colspan="4"><b><font size="-1" style="display:block!important;padding:20px 0 5px!important">无觅猜您也喜欢：</font></b></td>
    </tr>
    
        <tr>
                <td width="102" valign="top" style="padding:5px!important;margin:0!important">
                    <a title="世界上最有趣的开发者" style="text-decoration:none!important" href="http://app.wumii.com/ext/redirect.htm?url=http%3A%2F%2Fheikezhi.com%2F2011%2F05%2F03%2Fthe-most-interesting-developer-in-the-world%2F&amp;from=http%3A%2F%2Fheikezhi.com%2F2011%2F05%2F11%2Flife-beyond-http-11-googles-spdy%2F">
                        <img style="margin:0!important;padding:2px!important;border:1px solid #dddddd!important;width:96px!important;height:96px!important" src="http://static.wumii.com/site_images/2011/05/04/7537330.png" width="96px" height="96px"><br>
                        <font size="-1" color="#333333" style="display:block!important;line-height:15px!important;width:102px!important;font:12px/15px arial!important;height:60px!important;margin:3px 0 0 0!important;padding:0!important;overflow:hidden!important">世界上最有趣的开发者</font>
                    </a>
                </td>
                <td width="102" valign="top" style="padding:5px!important;margin:0!important;border-left:1px solid #dddddd!important">
                    <a title="世界，你准备好了吗？" style="text-decoration:none!important" href="http://app.wumii.com/ext/redirect.htm?url=http%3A%2F%2Fheikezhi.com%2F2011%2F03%2F11%2Flets-hack-now%2F&amp;from=http%3A%2F%2Fheikezhi.com%2F2011%2F05%2F11%2Flife-beyond-http-11-googles-spdy%2F">
                        <img style="margin:0!important;padding:2px!important;border:1px solid #dddddd!important;width:96px!important;height:96px!important" src="http://static.wumii.com/site_images/2011/03/13/3474551.jpg" width="96px" height="96px"><br>
                        <font size="-1" color="#333333" style="display:block!important;line-height:15px!important;width:102px!important;font:12px/15px arial!important;height:60px!important;margin:3px 0 0 0!important;padding:0!important;overflow:hidden!important">世界，你准备好了吗？</font>
                    </a>
                </td>
                <td width="102" valign="top" style="padding:5px!important;margin:0!important;border-left:1px solid #dddddd!important">
                    <a title="我从失败中学到的10件事" style="text-decoration:none!important" href="http://app.wumii.com/ext/redirect.htm?url=http%3A%2F%2Fheikezhi.com%2F2011%2F05%2F22%2F10-things-i-learned-from-failure%2F&amp;from=http%3A%2F%2Fheikezhi.com%2F2011%2F05%2F11%2Flife-beyond-http-11-googles-spdy%2F">
                        <img style="margin:0!important;padding:2px!important;border:1px solid #dddddd!important;width:96px!important;height:96px!important" src="http://static.wumii.com/images/blogWidget/wordpress_default.gif" width="96px" height="96px"><br>
                        <font size="-1" color="#333333" style="display:block!important;line-height:15px!important;width:102px!important;font:12px/15px arial!important;height:60px!important;margin:3px 0 0 0!important;padding:0!important;overflow:hidden!important">我从失败中学到的10件事</font>
                    </a>
                </td>
                <td width="102" valign="top" style="padding:5px!important;margin:0!important;border-left:1px solid #dddddd!important">
                    <a title="Chrome增加远程调试支持" style="text-decoration:none!important" href="http://app.wumii.com/ext/redirect.htm?url=http%3A%2F%2Fheikezhi.com%2F2011%2F05%2F15%2Fchrome-support-remote-debugging%2F&amp;from=http%3A%2F%2Fheikezhi.com%2F2011%2F05%2F11%2Flife-beyond-http-11-googles-spdy%2F">
                        <img style="margin:0!important;padding:2px!important;border:1px solid #dddddd!important;width:96px!important;height:96px!important" src="http://static.wumii.com/site_images/2011/05/16/8730204.png" width="96px" height="96px"><br>
                        <font size="-1" color="#333333" style="display:block!important;line-height:15px!important;width:102px!important;font:12px/15px arial!important;height:60px!important;margin:3px 0 0 0!important;padding:0!important;overflow:hidden!important">Chrome增加远程调试支持</font>
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
</table><img src="http://www1.feedsky.com/t1/512596486/heikezhi/feedsky/s.gif?r=http://heikezhi.com/2011/05/11/life-beyond-http-11-googles-spdy/" border="0" height="0" width="0">