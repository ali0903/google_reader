---
layout: post
title:  "NGINX upstream statistics"
date:   2012-01-03 21:59:10
author: iceskysl
categories: program
---

## NGINX upstream statistics
### by iceskysl
### at 2012-01-03 21:59:10
### original <http://www.iceskysl.com/?p=869>

<p>使用过nginx的应该都都晓得upstream，前面一篇文章说到《nginx upstream的5种配置方式》，例如如下是很常见的一段配置</p>
<p>#create by ice 2011.12.22<br>
upstream unicon_v2_www {<br>
# This is the socket we configured in unicorn.rb<br>
server unix:/var/www/v2/tmp/sockets/unicorn.sock backup;<br>
server 192.168.0.1:5000 weight=9 max_fails=2 fail_timeout=30s;<br>
server 192.168.0.2:5000 weight=3 max_fails=2 fail_timeout=30s;<br>
}<br>
server {<br>
listen 80;<br>
server_name www.iceskysl.com;<br>
error_log /opt/nginx/logs/error/error_v2_www.log;<br>
access_log /opt/nginx/logs/access/access_v2_www.log main;</p>
<p>root /var/www/v2/public/;<br>
index index.html index.htm;</p>
<p>location / {<br>
proxy_redirect off;<br>
proxy_set_header X-Real-IP $remote_addr;<br>
proxy_set_header Host $host;<br>
proxy_set_header Content-Length $content_length;<br>
proxy_set_header X-Forwarded-For $remote_addr;<br>
client_max_body_size 10m;<br>
client_body_buffer_size 128k;<br>
proxy_connect_timeout 90;<br>
proxy_send_timeout 90;<br>
proxy_read_timeout 90;<br>
proxy_buffer_size 4k;<br>
proxy_buffers 4 32k;<br>
proxy_busy_buffers_size 64k;<br>
proxy_temp_file_write_size 64k;<br>
chunked_transfer_encoding off;<br>
proxy_pass http://unicon_v2_www/;<br>
}<br>
}</p>
<p>其中upstream的这段配置：<br>
upstream unicon_v2_www {<br>
# This is the socket we configured in unicorn.rb<br>
server unix:/var/www/v2/tmp/sockets/unicorn.sock backup;<br>
server 192.168.0.1:5000 weight=9 max_fails=2 fail_timeout=30s;<br>
server 192.168.0.2:5000 weight=3 max_fails=2 fail_timeout=30s;<br>
}</p>
<p>我们了解到socket模式的这个是backup，其他两个机器分别weight=3和weight=9,于是我们想监控下upstream每个后端具体处理了多少请求，有没有问题等，这个好像就不好监控了，nginx自带的status模块只能监控总的请求数，无法区分每个upstream的具体情况。<br>
找到一个ustats（http://code.google.com/p/ustats/），其介绍是：NGINX basic upstream statistics module，具体的如下：<span></span></p>
<blockquote><p>USTATS module provides basic statistics for each backend in nginx upstreams:</p>
<p>Number of requests<br>
Http 499/500/503 errors count<br>
Tcp errors<br>
Http read/write timeouts<br>
Fail timeout<br>
Max fails count<br>
Last failed access time<br>
Total fails count<br>
Blacklisted backend highlighting<br>
Down backends highlighting<br>
The module allows you to sort values in some columns for each separate upstream. The data can be retrieved as JSON by appending “?json” to the end of location on which the module was set to work on (see configuration instructions below).</p></blockquote>
<p>看上去正是我需要的，有时间试试看～（为啥nginx不学haproxy官方自带一个详细statistics的页面呢～haproxy那个statistics很详细很好用～）<br>
详细使用指南：<a href="http://code.google.com/p/ustats/">http://code.google.com/p/ustats/</a></p>
<p><img src="http://ustats.googlecode.com/svn/wiki/shot1.png" alt=""></p>