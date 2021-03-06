---
layout: post
title:  "LuaJIT FFI 调用 Curl 示例"
date:   2012-10-11 15:00:00
author: chenxiaoyu
categories: program
---

## LuaJIT FFI 调用 Curl 示例
### by chenxiaoyu
### at 2012-10-11 15:00:00
### original <http://www.udpwork.com/item/8321.html>

<p><a href="http://luajit.org">LuaJIT</a>是一个好东西，比官方<a href="http://lua.org">Lua</a>解释器性能上提升很多。ngx_lua/<a href="http://openresty.org">ngx_openresty</a>都推荐用 LuaJIT 来加速 Lua 代码。</p>
<p>除去性能和速度上的优势，LuaJIT 还提供了 C Binding 模块：FFI，可以理解为类似 Python 中的 ctypes 模块，不过更加小巧和直观。意味着可以很方便的调用动态库啦。</p>
<p>下面示例如何在 FFI 调用 Curl 中的相关函数，环境如下：</p>
<div><pre>    操作系统：   Mac OS X 10.8.2
    LuaJIT版本：LuaJIT-2.0.0-beta10
    Curl版本：  7.24.0
</pre></div>
<p>测试代码如下：</p>
<div><pre>    local ffi = require 'ffi'

    ffi.cdef[[
        void *curl_easy_init();
        int curl_easy_setopt(void *curl, int option, ...);
        int curl_easy_perform(void *curl);
        void curl_easy_cleanup(void *curl);
        char *curl_easy_strerror(int code);
    ]]

    local libcurl = ffi.load('libcurl')

    local curl = libcurl.curl_easy_init()
    local CURLOPT_URL = 10002 -- 参考 curl/curl.h 中定义

    if curl then
        libcurl.curl_easy_setopt(curl, CURLOPT_URL, 'http://example.com')
        res = libcurl.curl_easy_perform(curl)
        if res ~= 0 then
            print(ffi.string(libcurl.curl_easy_strerror(res)))
        end
        libcurl.curl_easy_cleanup(curl)
    end
</pre></div>
<p>参考：<a href="http://develcuy.com/en/playing-luajit-ffi">http://develcuy.com/en/playing-luajit-ffi</a>（被此文坑了一下，示例中 curl_easy_setopt 函数 option 类型为：char，正确的应该是：int）</p>
<p>报错信息如下（可以辅助设置 VERBOSE（值为41） 选项调试）：</p>
<div><pre>    URL using bad/illegal format or missing URL
</pre></div>
<p><strong>END</strong>
</p>

			<div style="margin-top:8px;padding:6px 0;border-top:1px solid #3cf">
				<div style="text-align:center;margin:16px 0;padding:6px;border:0px dashed #999;font-family:arial;font-size:26px;font-weight:bold">
	<a href="http://www.udpwork.com/item/8321.html#review_form" title="不喜欢" style="text-decoration:none">
		<img src="http://www.udpwork.com//images/thumb_down24.gif" alt="">
		<span style="color:#f33">0</span>
	</a>
	   
	<a href="http://www.udpwork.com/item/8321.html#review_form" title="喜欢" style="text-decoration:none">
		<img src="http://www.udpwork.com//images/thumb_up24.gif" alt="">
		<span style="color:#3c3">0</span>
	</a>
</div>				<p>
					由 <a href="http://www.udpwork.com/">IT 牛人博客聚合网站(udpwork.com)</a> 聚合
					|
					<a href="http://www.udpwork.com/item/8321.html#reviews">评论: 0</a>
					|
					<a href="http://book.benegg.com/tag/%E7%BC%96%E7%A8%8B?from=udpwork-feed">10000+ 本编程/Linux PDF/CHM 电子书下载</a>
				</p>
			</div>