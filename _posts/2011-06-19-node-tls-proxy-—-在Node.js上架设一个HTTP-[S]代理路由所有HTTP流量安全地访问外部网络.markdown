---
layout: post
title:  "node-tls-proxy — 在Node.js上架设一个HTTP [S]代理路由所有HTTP流量安全地访问外部网络"
date:   2011-06-19 11:14:01
author: iGFW
categories: program
---

## node-tls-proxy — 在Node.js上架设一个HTTP [S]代理路由所有HTTP流量安全地访问外部网络
### by iGFW
### at 2011-06-19 11:14:01
### original <http://igfw.tk/archives/3428>

<p>A HTTPS proxy to route all HTTP traffic securely to the external network (based on <a rel="nofollow" href="http://nodejs.org/">node.js</a>)</p>
<p>The general idea is mentioned here: <a rel="nofollow" href="http://dhruvbird.blogspot.com/2011/03/https-for-all-browsing.html">Using HTTPS for all browsing</a></p>
<p>To get started:</p>
<ol>
<li>Copy remote-proxy.js to a remote machine that you trust to not be packet sniffed by anyone.</li>
<li>Start it by typing “node remote-proxy.js”</li>
<li>Run local-proxy.js on your machine by typing: “node local-proxy.js –remote=REMOTE_PROXY_HOST_NAME_OR_IP”</li>
<li>Go to Firefox/Chrome (or whatever your browser is) and set ONLY the HTTP proxy to localhost port 8080.</li>
<li>DO NOT set any other proxy (even HTTPS) to localhost port 8080.</li>
<li>DO NOT forget to replace the SSL keys with your own (self generated keys that is)</li>
<li>That’s it. All traffic from your machine is now secure from local packet sniffers.</li>
</ol>
<p><a rel="nofollow" href="http://www.reddit.com/r/programming/comments/fzu0c/ive_created_an_https_based_proxy_for_relatively/"><span></span>A thread on reddit praising/bashing the idea/app</a>. Use this to comment.</p>
<p>项目地址：<a href="http://code.google.com/p/node-tls-proxy/">http://code.google.com/p/node-tls-proxy/</a></p>
<p>下载地址：<a href="http://code.google.com/p/node-tls-proxy/source/browse/#svn%2Ftrunk">http://code.google.com/p/node-tls-proxy/source/browse/#svn%2Ftrunk</a></p>
<p><a href="https://no.de/">https://no.de/</a>提供免费测试的nodejs主机，我申请过一个，申请比较麻烦，要在Linux系统下用命令申请邀请码（我等了一个星期才收到），然后申请主机，还要生成私钥来认证ssh登陆，总之申请到后发现可以ssh隧道转发代理翻墙，不过过了一段时间由于没有部署应用被删除了，那个应用部署我按照说明都不成功，太笨了。</p>
<p><strong>本文原始地址：</strong><a href="http://igfw.tk/archives/3428">http://igfw.tk/archives/3428</a></p>