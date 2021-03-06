---
layout: post
title:  "SPDY Protocol"
date:   2013-05-13 15:49:23
author: sunchangming
categories: program
---

## SPDY Protocol
### by sunchangming
### at 2013-05-13 15:49:23
### original <http://www.udpwork.com/item/9832.html>

<p><a href="http://dev.chromium.org/spdy/spdy-protocol/">SPDY</a>可视为在原来的https协议的SSL和<a href="http://tools.ietf.org/html/rfc2616">HTTP</a>之间新加了一层：</p>
<p><img src="http://blog.sunchangming.com//soarjOjSeS5hoFYvjtAnxCg.png"></p>
<p><a href="http://dev.chromium.org/spdy/spdy-protocol/">SPDY</a>对<a href="http://tools.ietf.org/html/rfc2616">HTTP</a>主要的增强：</p>
<ol><li>请求多路复用。在一个tcp连接上并行发行多个请求。而传统来说，为了并行发起多个HTTP请求就得有多条TCP连接。</li>
<li>带优先级的请求。</li>
<li>头部压缩</li>
<li>Server Push 。 这个和websocket中的push不是一个东西。</li>
<li>Server Hint 服务器主动建议客户端下载某些资源。</li>
</ol>
<p>总的来说，SPDY分为两层，framing层（TCP层上的帧）和HTTP层。</p>
<p>Once the connection is established, clients and servers exchange framed messages。</p>
<p>frame分为两种：</p>
<ol><li>control frames</li>
<li>data frames</li>
</ol>
<p>无论是哪种类型的frames，都有一个8字节的头部。头部的第一位如果是1，代表是control frame，如果是0，代表是data frame。</p>
<p>control frame的格式:</p>
<ol><li>control bit: 长度1位，值永远是1。</li>
<li>Version: 15位。标识spdy的版本号。2或者3。</li>
<li>Type: 16 位。 是哪种control frame.</li>
<li>Flags: 8位。 一些标志。具体含义要根据这个control frame的type来决定。</li>
<li>Length: 24位无符号整数，标识后面data字段的长度。</li>
<li>Data: 有效负荷。</li>
</ol>
<p>截至SPDY/3，control frame一共有以下10种：</p>
<pre>
			// Types of Spdy Control Frames.
			enum SpdyControlType {
			  SYN_STREAM = 1,
			  SYN_REPLY,
			  RST_STREAM,
			  SETTINGS,
			  NOOP,  // Because it is valid in SPDY/2, kept for identifiability/enum order.
			  PING,
			  GOAWAY,
			  HEADERS,
			  WINDOW_UPDATE,
			  CREDENTIAL,
			  NUM_CONTROL_FRAME_TYPES
			};

			</pre><p>其中NOOP是在SPDY/2中定义的。在SPDY/3中已经去掉。</p>
<p>Data frames的格式:</p>
<ol><li>control bit: 长度1位，值永远是0。</li>
<li>stream-id: 31位的整数，标识stream的id。</li>
<li>flags: 8位。目前只用到最后一位。 (flags &amp; 0x01) == 1 表示这个frame是当前流的最后一帧。</li>
<li>Length: 24位无符号整数，标识后面data字段的长度。</li>
<li>Data: 有效负荷。</li>
</ol>
无论Data frames还是Control frames，Data之前字段加起来都是固定的64 bits。所以可以用这样的C struct表示一个frame的头部（不含Data部分)<pre>
			// A SPDY stream id is a 31 bit entity.
			typedef uint32 SpdyStreamId;

			// protocol over-the-wire sizes.
			#pragma pack(push)
			#pragma pack(1)

			// A special structure for the 8 bit flags and 24 bit length fields.
			union FlagsAndLength {
			  uint8 flags_[4];  // 8 bits
			  uint32 length_;   // 24 bits
			};

			// The basic SPDY Frame structure.
			struct SpdyFrameBlock {
			  union {
			    struct {
			      uint16 version_;
			      uint16 type_;
			    } control_;
			    struct {
			      SpdyStreamId stream_id_;
			    } data_;
			  };
			  FlagsAndLength flags_length_;
			};
			#pragma pack(pop)
			</pre><p>sizeof(SpdyFrameBlock)应该等于8。</p>
<p>创建Session:</p>
<p>int spdylay_session_client_new(spdylay_session **session_ptr, uint16_t version, const spdylay_session_callbacks *callbacks, void *user_data);
<br>
int spdylay_session_server_new(spdylay_session **session_ptr, uint16_t version, const spdylay_session_callbacks *callbacks, void *user_data);</p>
<p>流：
			  流是双向的，由很多帧组成。</p>
<ol><li>无论是server还是client，都可以创建流</li>
<li>流可以携带一系列的name/value对，作为头部。（这点很像http消息头部）</li>
<li>同一个connection上可以并发的传递多个流。</li>
<li>流可以在传输完毕前被提前cancel。</li>
</ol>
<p>流的创建和关闭：</p>
<ol><li>SYN_STREAM 创建一个新的流</li>
<li>SYN_REPLY SYN_STREAM的回答。</li>
<li>RST_STREAM 关闭一个流</li>
</ol>
<p>无论是server还是client，如果要正常关闭一个TCP连接，那么在关闭前应该发送GOAWAY frame，</p>

			<div style="margin-top:8px;padding:6px 0;border-top:1px solid #3cf">
				<div style="text-align:center;margin:16px 0;padding:6px;border:0px dashed #999;font-family:arial;font-size:26px;font-weight:bold">
	<a href="http://www.udpwork.com/item/9832.html#review_form" title="不喜欢" style="text-decoration:none">
		<img src="http://www.udpwork.com//images/thumb_down24.gif" alt="">
		<span style="color:#f33">0</span>
	</a>
	   
	<a href="http://www.udpwork.com/item/9832.html#review_form" title="喜欢" style="text-decoration:none">
		<img src="http://www.udpwork.com//images/thumb_up24.gif" alt="">
		<span style="color:#3c3">0</span>
	</a>
</div>				<p>
					由 <a href="http://www.udpwork.com/">udpwork.com</a> 聚合
					|
					<a href="http://www.udpwork.com/item/9832.html#reviews">评论: 0</a>
					|
					<a href="http://www.jikenow.com/">要! 要! 即刻! Now!</a>
				</p>
			</div>