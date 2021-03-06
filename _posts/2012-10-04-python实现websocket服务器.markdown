---
layout: post
title:  "python实现websocket服务器"
date:   2012-10-04 00:11:37
author: isnowfy
categories: program
---

## python实现websocket服务器
### by isnowfy
### at 2012-10-04 00:11:37
### original <http://www.udpwork.com/item/8185.html>

<p><a href="http://www.isnowfy.com/wp-content/uploads/2012/10/websocket-lifecycle.png"><img src="http://www.isnowfy.com/wp-content/uploads/2012/10/websocket-lifecycle-300x245.png"></a></p>
<p>websocket是什么呢，websocket是一个浏览器和服务器通信的新的协议，一般而言，浏览器和服务器通信最常用的是http协议，但是http协议是无状态的，每次浏览器请求信息，服务器返回信息后这个浏览器和服务器通信的信道就被关闭了，这样使得服务器如果想主动给浏览器发送信息变得不可能了，服务器推技术在http时代的解决方案一个是客户端去轮询，或是使用<a href="http://www.isnowfy.com/comet-web-chat/">comet技术</a>，而websocket则和一般的socket一样，使得浏览器和服务器建立了一个双工的通道。
<br>
</p>
<p>具体的websocket协议在<a href="http://tools.ietf.org/html/rfc6455">rfc6455</a>里面有，这里简要说明一下。websocket通信需要先有个握手的过程，使得协议由http转变为webscoket协议，然后浏览器和服务器就可以利用这个socket来通信了。</p>
<p>首先浏览器发送握手信息，要求协议转变为websocket</p>
<pre>
GET / HTTP/1.1
Host: example.com
Upgrade: websocket
Connection: Upgrade
Sec-WebSocket-Key: dGhlIHNhbXBsZSBub25jZQ==
Origin: http://example.com
</pre><p>服务器接收到信息后，取得其中的Sec-WebSocket-Key，将他和一个固定的字符串258EAFA5-E914-47DA-95CA-C5AB0DC85B11做拼接，得到的字符串先用sha1做一下转换，再用base64转换一下，就得到了回应的字符串，这样服务器端发送回的消息是这样的</p>
<pre>
HTTP/1.1 101 Switching Protocols
Upgrade: websocket
Connection: Upgrade
Sec-WebSocket-Accept: s3pPLMBiTxaQ9kYGzzhZRbK+xOo=
</pre><p>这样握手就完成了，用python来实现这段握手过程的话就是下面这样。</p>
<div><ol><li><div> defhandshake(conn):</div>
</li>
<li><div>    key =None</div>
</li>
<li><div>    data = conn.recv(8192)</div>
</li>
<li><div>   ifnotlen(data):</div>
</li>
<li><div>       returnFalse</div>
</li>
<li><div>   forlineindata.split('\r\n\r\n')[0].split('\r\n')[1:]:</div>
</li>
<li><div>        k, v = line.split(': ')</div>
</li>
<li><div>       ifk =='Sec-WebSocket-Key':</div>
</li>
<li><div>            key =base64.b64encode(hashlib.sha1(v +'258EAFA5-E914-47DA-95CA-C5AB0DC85B11').digest())</div>
</li>
<li><div>   ifnotkey:</div>
</li>
<li><div>        conn.close()</div>
</li>
<li><div>       returnFalse</div>
</li>
<li><div>    response ='HTTP/1.1 101 Switching Protocols\r\n'\</div>
</li>
<li><div>               'Upgrade: websocket\r\n'\</div>
</li>
<li><div>               'Connection: Upgrade\r\n'\</div>
</li>
<li><div>               'Sec-WebSocket-Accept:'+ key +'\r\n\r\n'</div>
</li>
<li><div>    conn.send(response)</div>
</li>
<li><div>   returnTrue</div>
</li>
</ol>
</div>
<p>握手过程完成之后就是信息传输了，websocket的数据信息格式是这样的。</p>
<pre>
+-+-+-+-+-------+-+-------------+-------------------------------+
 0                   1                   2                   3
 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1
+-+-+-+-+-------+-+-------------+-------------------------------+
|F|R|R|R| opcode|M| Payload len |    Extended payload length    |
|I|S|S|S|  (4)  |A|     (7)     |             (16/64)           |
|N|V|V|V|       |S|             |   (if payload len==126/127)   |
| |1|2|3|       |K|             |                               |
+-+-+-+-+-------+-+-------------+ - - - - - - - - - - - - - - - +
|     Extended payload length continued, if payload len == 127  |
+ - - - - - - - - - - - - - - - +-------------------------------+
|                               | Masking-key, if MASK set to 1 |
+-------------------------------+-------------------------------+
| Masking-key (continued)       |          Payload Data         |
+-------------------------------- - - - - - - - - - - - - - - - +
:                     Payload Data continued ...                :
+ - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - +
|                     Payload Data continued ...                |
+---------------------------------------------------------------+
</pre><p>值得注意的是payload len这项，表示数据的长度有多少，如果小于126，那么payload len就是数据的长度，如果是126那么接下来2个字节是数据长度，如果是127表示接下来8个字节是数据长度，然后后面会有四个字节的mask，真实数据要由payload data和mask做异或才能得到，这样就可以得到数据了。发送数据的格式和接受的数据类似，具体细节可以去参考<a href="http://tools.ietf.org/html/rfc6455">rfc6455</a>，这里就不过多赘述了。</p>
<p>然后在我的github上有我实现的websocket server的<a href="https://github.com/isnowfy/python-websocket">代码</a>，简单使用的话是足够了，但是如果是作为应用还是需要把所有的协议的具体细节都去实现才可以。
<br>
<strong>我猜您可能还会喜欢:</strong>
</p>
<ul><li><a href="http://www.isnowfy.com/machine-learning-subtitle-script/" title="2011 年 10 月 11 日">machine learning公开课的字幕获取脚本</a></li>
<li><a href="http://www.isnowfy.com/python-chinese-segmentation/" title="2012 年 03 月 17 日">python中文分词</a></li>
<li><a href="http://www.isnowfy.com/java-hashmap/" title="2011 年 12 月 07 日">浅析java的hashmap</a></li>
<li><a href="http://www.isnowfy.com/introduction-to-gevent/" title="2011 年 12 月 21 日">gevent初步</a></li>
<li><a href="http://www.isnowfy.com/web-paint/" title="2011 年 10 月 03 日">网络涂鸦板</a></li>
<li><a href="http://www.isnowfy.com/libcurl-fetch-webpage/" title="2011 年 08 月 27 日">c中使用libcurl抓取网页</a></li>
<li><a href="http://www.isnowfy.com/introduction-to-cuda/" title="2012 年 10 月 07 日">CUDA编程入门</a></li>
</ul>
<p></p>

			<div style="margin-top:8px;padding:6px 0;border-top:1px solid #3cf">
				<div style="text-align:center;margin:16px 0;padding:6px;border:0px dashed #999;font-family:arial;font-size:26px;font-weight:bold">
	<a href="http://www.udpwork.com/item/8185.html#review_form" title="不喜欢" style="text-decoration:none">
		<img src="http://www.udpwork.com//images/thumb_down24.gif" alt="">
		<span style="color:#f33">0</span>
	</a>
	   
	<a href="http://www.udpwork.com/item/8185.html#review_form" title="喜欢" style="text-decoration:none">
		<img src="http://www.udpwork.com//images/thumb_up24.gif" alt="">
		<span style="color:#3c3">1</span>
	</a>
</div>				<p>
					由 <a href="http://www.udpwork.com/">IT 牛人博客聚合网站(udpwork.com)</a> 聚合
					|
					<a href="http://www.udpwork.com/item/8185.html#reviews">评论: 1</a>
					|
					<a href="http://book.benegg.com/tag/%E7%BC%96%E7%A8%8B?from=udpwork-feed">10000+ 本编程/Linux PDF/CHM 电子书下载</a>
				</p>
			</div>