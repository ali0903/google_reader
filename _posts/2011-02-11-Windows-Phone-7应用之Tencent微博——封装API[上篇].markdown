---
layout: post
title:  "Windows Phone 7应用之Tencent微博——封装API[上篇]"
date:   2011-02-11 17:28:00
author: chenkai
categories: program
---

## Windows Phone 7应用之Tencent微博——封装API[上篇]
### by chenkai
### at 2011-02-11 17:28:00
### original <http://www.cnblogs.com/chenkai/archive/2011/02/11/1951527.html>

<p><p><font face="Cambria">马上就要春节放假.年底活动安排的比较多.庸庸碌碌了一阵,忙中偷闲 这篇博文已经在LiveWriter躺了三天.下午回来一打开就看见标题.继续来完善Windows phone 7上Tencent微博客户端.上一篇中</font><a href="http://www.cnblogs.com/chenkai/archive/2011/01/13/1934222.html"><font face="Cambria">Windows Phone 7应用之Tencent微博——OAuth认证</font></a><font face="Cambria"> 提到以OAuth协议验证的原理流程,本篇来关注一下Tencent微博OAuth协议API封装. 目前Tencent 微博采用Google OAuth开放协议. 这是Google OAuth认证服务中提供的一个开源项目.其中提供多种语言Java/C#/Ruby等实现OAuth协议底层代码.版本是1.0的. 目前OAuth 2.0协议可能在年后[2011]推出. 不过在Google一个小项目中Play Ground With OAuth中已经推出OAuth2.0认证服务. 据我所知Oauth1.0 在访问安全上存在漏洞.才衍生出2.0 版本.但这个说法没有得到权威机构的证实. 如果对OAuth协议底层实现感兴趣的同学可以研究一下.</font></p>  <p><font face="Cambria">在进入封装Tencent OAuth协议API前,需要一些准备工作. 注册一个微博开放平台开发者账号.也就是我上节提到Appkey. 在这里想发一下牢骚.注册Sina微博需要身份证号绑定. 原因不解释.各位体会. 表示反感. 另外就是进入Sina 微博开放平台.你会发现 东西太多了. 作为一个第三方应用开发者你该干什么?从那下手呢? 你需要持续看完几大篇介绍平台文章才看到自己关心重点.   这也是我说Sina API是一个&quot;巨无霸&quot;绝非是空穴来风. 另外一个就是针对申请开发者账号, 采用一个独立微博 类似发一条消息 等候验证. 这种方式不仅在某种程度上暴露了开发者个人信息同时也是自己应用信息在未审核通过之前被他人浏览了, Sina在主观上暴露了开发者意图隐私上没有做保护. 太没安全感.开发人员的IDea很容易在未审核前提就被他人给剽窃了. 审核时间令人不敢恭维. 而Tencent在我发出第一次请求后就受理 通过,. 短短两天时间.并且是在审核之后才对外公开相应应用信息.</font></p>  <p><font face="Cambria">首先是Sina绑定身份证方式就让我觉得没有安全感.网络上没有自由性. 这仿佛也注定自己对Sina Mini Blog 始终没有信心. 当然常玩推特同学就不用担心这个问题了. 墙内和墙外是另个世界. 如上纯属于个人看法.Sina没有赢得我的好感,.转入正题 来说申请Appkey开发者账号.</font></p>  <h3 style="padding-bottom:3px;background-color:#80ac92;font-family:verdana,geneva,arial,helvetica,sans-serif;height:44px;color:white;font-size:15pt;font-weight:bolder;padding-top:10px"><strong><font face="Cambria">&lt;1&gt;建立Tencent 微博应用</font></strong></h3>  <p><font face="Cambria">Tencent Mini Blog PlatFrom[开发平台]:</font><a href="http://open.t.qq.com/"><font face="Cambria">http://open.t.qq.com/</font></a><font face="Cambria"> 点击大概看一下开发微博引用流程 你是否觉得这种方式比Sina那大篇幅文字要直观呢?:</font></p>  <p><font face="Cambria"><img style="background-image:none;border-right-width:0px;padding-left:0px;padding-right:0px;display:inline;float:left;border-top-width:0px;border-bottom-width:0px;border-left-width:0px;padding-top:0px" title="2011-01-20_182834" border="0" alt="2011-01-20_182834" align="left" src="http://images.cnblogs.com/cnblogs_com/chenkai/201102/201102111728153088.png" width="646" height="254"></font></p>  <p> </p>  <p> </p>  <p> </p>  <p> </p>  <p> </p>  <p> </p>  <p> </p>  <p><font face="Cambria">审核通过后就看到从属于开发者和对应应用唯一标识AppKey:</font></p>  <p><font face="Cambria"><img style="background-image:none;border-right-width:0px;padding-left:0px;padding-right:0px;display:inline;float:left;border-top-width:0px;border-bottom-width:0px;border-left-width:0px;padding-top:0px" title="2011-01-12_160623" border="0" alt="2011-01-12_160623" align="left" src="http://images.cnblogs.com/cnblogs_com/chenkai/201102/201102111728168038.png" width="684" height="211"></font></p>  <p> </p>  <p> </p>  <p> </p>  <p> </p>  <p> </p>  <p> </p>  <p><font face="Cambria">有了开发应用AppKey剩下问题就来关注一下不同等级Appkey访问Tencent 微博的权限问题:</font></p>  <ul>   <li><font face="Cambria"><strong>初级授权</strong>：150,000次请求[可支撑至少1,000活跃用户同时在线]</font> </li>    <li><font face="Cambria">读请求：每API单用户每小时150次[包括获取公共时间轴，首页时间轴等]        <br>写请求：不可用</font> </li>    <li><font face="Cambria"><strong>中级授权</strong>：750,000次请求[可支撑至少5,000活跃用户同时在线]</font> </li>    <li><font face="Cambria">读请求：每API单用户每小时150次        <br>写请求：每用户每小时最大100次[包括发微博、转播、对话、私信、收听等]</font> </li> </ul>  <p><font face="Cambria">在第三方应用中关键是具有写请求权限. 这样我们才可以通过第三方向Tencent 微博平台写入数据. 这就意味至少要从Tencent那里拿到中级授权. 对于开发而言单个AppKey 具有75W次访问频率 已经足够使用. 其他相关素材以说明请参考<font face="Cambria">Tencent Mini Blog PlatFrom[开发平台]:</font><a href="http://open.t.qq.com/"><font face="Cambria">http://open.t.qq.com/</font></a><font face="Cambria">  如下重点来说一下Tencent 微博对第三方访问时API封装.</font></font><font face="Cambria"> </font></p>  <p><font face="Cambria"></font></p>  <h3 style="padding-bottom:3px;background-color:#80ac92;font-family:verdana,geneva,arial,helvetica,sans-serif;height:44px;color:white;font-size:15pt;font-weight:bolder;padding-top:10px"><strong><font face="Cambria">&lt;2&gt;Tencent微博API封装</font></strong></h3>  <p><font face="Cambria">一般在微博的应用中.涉及到开放平台都具有两个核心特点. 第一个就是对外公开具备安全机制的数据访问协议<a href="http://www.cnblogs.com/chenkai/archive/2011/01/13/1934222.html">[上篇讲到的Google OAuth]</a> 另外一个因素就是开放平台数据流的流入和流出. 即开放平台本身的意义 开放数据读取. 和写入. 那磨对外公开的统一的API无非制定一个统一方式. 另外一个目前Sina和Tencent 都采用的是Google OAuth协议方式. 现在我们都能看到各自推出官方的支持不同语言的API.当然也包括一些第三方定义API. 如果你对Google OAuth协议有兴趣.也可以完全根据开放Sina或是Tencent等开放平台协议.可以写出一套自己的访问API. 这里来详细解析一下Tencent微博开放平台Google OAuth协议时如何工作的; 这也是封装API的前提.</font></p>  <p><font face="Cambria">ok.在开放平台中提供<a href="http://open.t.qq.com/resource.php?i=3,1">不同语言的SDK下载</a> 这里拿C#作为标准语言. 官方也提供C#的比较简单的SDK.如果你觉得没有必要重复制造轮子.就拿官方提供版本来具体看一下Google OAuth如何工作的: <a href="http://open.t.qq.com/download/C%23-SDK.7z">C#版本的SDK下载地址</a> 打开解决方案:</font></p>  <p><img style="background-image:none;border-right-width:0px;padding-left:0px;padding-right:0px;display:inline;float:left;border-top-width:0px;border-bottom-width:0px;border-left-width:0px;padding-top:0px" title="2011-02-11_122835" border="0" alt="2011-02-11_122835" align="left" src="http://images.cnblogs.com/cnblogs_com/chenkai/201102/201102111728163545.png" width="345" height="301"></p>  <p><font face="Cambria"></font></p>  <p><font face="Cambria"> </font></p>  <p> </p>  <p> </p>  <p> </p>  <p> </p>  <p> </p>  <p> </p>  <p> </p>  <p> </p>  <p><font face="Cambria">在Google OAuth协议中我们首先需要获得Request Token的授权 在通过Request Token授权信息获取Access_Token即最终开放平台身份认证才可实现第三方的数据读写.如上SDK中Oauth.cs即定义Google Oauth的源码 你可以可以在 <a href="http://code.google.com/apis/accounts/docs/OAuth.html">Google OAuth开源项目</a>中看到. 实现请求的方式有两种同步SyncHttp和异步AsyncHttp. QWeiboRequest则封装这两种请求的方式.来看看如何首先拿到RequstToken的授权: 在<a href="http://open.t.qq.com/resource.php?i=1,1">APi中可以看到Tencent给出请求说明:</a> 需要参数:</font></p>  <p><img style="background-image:none;border-right-width:0px;padding-left:0px;padding-right:0px;display:inline;float:left;border-top-width:0px;border-bottom-width:0px;border-left-width:0px;padding-top:0px" title="2011-02-11_134922" border="0" alt="2011-02-11_134922" align="left" src="http://images.cnblogs.com/cnblogs_com/chenkai/201102/201102111728167972.png" width="685" height="247"></p>  <p><font face="Cambria"></font></p>  <p> </p>  <p> </p>  <p> </p>  <p> </p>  <p> </p>  <p> </p>  <p> </p>  <p><font face="Cambria">request_token-URL地址:</font><a href="https://open.t.qq.com/cgi-bin/request_token"><font face="Cambria">https://open.t.qq.com/cgi-bin/request_token</font></a>. <font face="Cambria">发起一个Requst_token:</font></p>  <div>   <pre><span>   1:  </span> <span>private</span> <span>bool</span> GetRequestToken(<span>string</span> customKey, <span>string</span> customSecret)</pre>

  <pre><span>   2:  </span>        {</pre>

  <pre><span>   3:  </span>            <span>string</span> url = <span>"https://open.t.qq.com/cgi-bin/request_token"</span>;</pre>

  <pre><span>   4:  </span>            List&lt;Parameter&gt; parameters = <span>new</span> List&lt;Parameter&gt;();</pre>

  <pre><span>   5:  </span>            OauthKey oauthKey = <span>new</span> OauthKey();</pre>

  <pre><span>   6:  </span>            oauthKey.customKey = customKey;</pre>

  <pre><span>   7:  </span>            oauthKey.customSecrect = customSecret;</pre>

  <pre><span>   8:  </span>            oauthKey.callbackUrl = <span>"http://www.qq.com"</span>;</pre>

  <pre><span>   9:  </span> </pre>

  <pre><span>  10:  </span>            QWeiboRequest request = <span>new</span> QWeiboRequest();</pre>

  <pre><span>  11:  </span>            <span>return</span> ParseToken(request.SyncRequest(url, <span>"GET"</span>, oauthKey, parameters, <span>null</span>));</pre>

  <pre><span>  12:  </span>        }</pre>
</div>


<p><font face="Cambria">在对应参数中需要指定Appkey的键值对. 和回调路径[也可以不指定 这里为了演示]. 就可以发起指定Get请求.来看一下带有完整参数获取Request_Token URL： </font></p>

<div>
  <pre><span>   1:  </span><span>//Request _Token 完整的URL地址 可以详细看到每个参数配置</span></pre>

  <pre><span>   2:  </span>https:<span>//open.t.qq.com/cgi-bin/request_token</span></pre>

  <pre><span>   3:  </span>?oauth_callback=http%3A%2F%2Fqunmgr.qq.com%2Foauthapi%2Fcallback.php</pre>

  <pre><span>   4:  </span>&amp;oauth_consumer_key=0aae7ba5c10d4b939694b0b3b9ffe8ac</pre>

  <pre><span>   5:  </span>&amp;oauth_nonce=b788149731e0ed75daa03a6e1e30427c</pre>

  <pre><span>   6:  </span>&amp;oauth_signature=zO5%2FgT%2FP6tbhBBZVuoyfjPwjdxY%3D</pre>

  <pre><span>   7:  </span>&amp;oauth_signature_method=HMAC-SHA1</pre>

  <pre><span>   8:  </span>&amp;oauth_timestamp=1288932298</pre>

  <pre><span>   9:  </span>&amp;oauth_version=1.0</pre>
</div>


<p><font face="Cambria">ok.用Fiddler监视一下请求的参数和数据:</font></p>

<p><img style="background-image:none;border-right-width:0px;padding-left:0px;padding-right:0px;display:inline;float:left;border-top-width:0px;border-bottom-width:0px;border-left-width:0px;padding-top:0px" title="2011-02-11_142427" border="0" alt="2011-02-11_142427" align="left" src="http://images.cnblogs.com/cnblogs_com/chenkai/201102/201102111728216082.png" width="644" height="366"></p>

<p> </p>

<p> </p>

<p> </p>

<p> </p>

<p> </p>

<p> </p>

<p> </p>

<p> </p>

<p> </p>

<p> </p>

<p><font face="Cambria">看一下返回值和对应参数:-返回值:</font></p>

<div>
  <pre><span>   1:  </span><span>//从tencent 获取到请求返回值</span></pre>

  <pre><span>   2:  </span>oauth_token=a63f4a0e1242462fb8c11e53159ba294</pre>

  <pre><span>   3:  </span>&amp;oauth_token_secret=40ece707e064128e4fec692e3c09c692</pre>

  <pre><span>   4:  </span>&amp;oauth_callback_confirmed=true</pre>
</div>


<p><font face="Cambria">参数说明:</font></p>

<p><img style="background-image:none;border-right-width:0px;padding-left:0px;padding-right:0px;display:inline;float:left;border-top-width:0px;border-bottom-width:0px;border-left-width:0px;padding-top:0px" title="2011-02-11_142819" border="0" alt="2011-02-11_142819" align="left" src="http://images.cnblogs.com/cnblogs_com/chenkai/201102/20110211172821476.png" width="687" height="136"></p>

<p> </p>

<p> </p>

<p> </p>

<p> </p>

<p><font face="Cambria">You See!这就是一个Request _Token从请求参数发出到返回值的整个过程, 获取Request_Token最为关键参数就是Oauth_token我们可以通过它获得AccessToken. 依次类推可以根据Tencent给出官方说明 获得AccessToken 具体实现:</font></p>

<div>
  <pre><span>   1:  </span><span>//获取一个人已经认证的Access Token </span></pre>

  <pre><span>   2:  </span><span>private</span> <span>bool</span> GetAccessToken(<span>string</span> customKey, <span>string</span> customSecret, <span>string</span> requestToken,</pre>

  <pre><span>   3:  </span> <span>string</span> requestTokenSecrect, <span>string</span> verify)</pre>

  <pre><span>   4:  </span>        {</pre>

  <pre><span>   5:  </span>            <span>string</span> url = <span>"https://open.t.qq.com/cgi-bin/access_token"</span>;</pre>

  <pre><span>   6:  </span>            List&lt;Parameter&gt; parameters = <span>new</span> List&lt;Parameter&gt;();</pre>

  <pre><span>   7:  </span>            OauthKey oauthKey = <span>new</span> OauthKey();</pre>

  <pre><span>   8:  </span>            oauthKey.customKey = customKey;</pre>

  <pre><span>   9:  </span>            oauthKey.customSecrect = customSecret;</pre>

  <pre><span>  10:  </span>            oauthKey.tokenKey = requestToken;</pre>

  <pre><span>  11:  </span>            oauthKey.tokenSecrect = requestTokenSecrect;</pre>

  <pre><span>  12:  </span>            oauthKey.verify = verify;</pre>

  <pre><span>  13:  </span> </pre>

  <pre><span>  14:  </span>            QWeiboRequest request = <span>new</span> QWeiboRequest();</pre>

  <pre><span>  15:  </span>            <span>return</span> ParseToken(request.SyncRequest(url, <span>"GET"</span>, oauthKey, parameters, <span>null</span>));</pre>

  <pre><span>  16:  </span>        }</pre>
</div>


<p><font face="Cambria">通过Requst_token 获得认证后就成功获取Access_Token前提条件Oauth_token参数. 通过OAuth_Token从Tencent服务器端交换过来一个Access_token. 从Tencent请求需要参数:</font></p>

<p><img style="background-image:none;border-right-width:0px;padding-left:0px;padding-right:0px;display:inline;float:left;border-top-width:0px;border-bottom-width:0px;border-left-width:0px;padding-top:0px" title="2011-02-11_153930" border="0" alt="2011-02-11_153930" align="left" src="http://images.cnblogs.com/cnblogs_com/chenkai/201102/201102111728223998.png" width="689" height="281"></p>

<p><font face="Cambria"></font></p>

<p> </p>

<p> </p>

<p> </p>

<p> </p>

<p> </p>

<p> </p>

<p> </p>

<p><font face="Cambria">ok.在来通过IEWatch或是Fiddle看一下请求的整个URL参数配置:</font></p>

<div>
  <pre><span>   1:  </span><span>//对应的Access_Token请求的完整URL 注意Oauth_token对应从REquest_token获取返回值Oauthtoken</span></pre>

  <pre><span>   2:  </span>https:<span>//open.t.qq.com/cgi-bin/access_token</span></pre>

  <pre><span>   3:  </span>?oauth_consumer_key=0aae7ba5c10d4b939694b0b3b9ffe8ac</pre>

  <pre><span>   4:  </span>&amp;oauth_nonce=35885b07fe7dc53942d405e552d086fe</pre>

  <pre><span>   5:  </span>&amp;oauth_signature=TXBihj%2FH4avLJaWmjYARDVVCu8M%3D</pre>

  <pre><span>   6:  </span>&amp;oauth_signature_method=HMAC-SHA1</pre>

  <pre><span>   7:  </span>&amp;oauth_timestamp=1288933101</pre>

  <pre><span>   8:  </span>&amp;oauth_token=66ae88faecd14a1f8e826d35d5e857a7</pre>

  <pre><span>   9:  </span>&amp;oauth_verifier=1154e3657d5542338eece2767172da02</pre>

  <pre><span>  10:  </span>&amp;oauth_version=1.0</pre>
</div>


<p>在来查看对应的返回值:</p>

<div>
  <pre><span>   1:  </span><span>//Access_token对应的返回值</span></pre>

  <pre><span>   2:  </span>oauth_token=ffc37464473e48a2b22226350dc98210</pre>

  <pre><span>   3:  </span>&amp;oauth_token_secret=3fe92ec42e936672ceca549d432c237d</pre>

  <pre><span>   4:  </span>&amp;name=chenkai</pre>
</div>


<p>返回值参数说明:</p>

<p><img style="background-image:none;border-right-width:0px;padding-left:0px;padding-right:0px;display:inline;float:left;border-top-width:0px;border-bottom-width:0px;border-left-width:0px;padding-top:0px" title="2011-02-11_154444" border="0" alt="2011-02-11_154444" align="left" src="http://images.cnblogs.com/cnblogs_com/chenkai/201102/201102111728238948.png" width="684" height="134"></p>

<p> </p>

<p> </p>

<p> </p>

<p> </p>

<p><font face="Cambria">注意在请求的参数中. 上一次通过Request_token返回一个OauthToken返回值用来作为AccessToken请求的Oauth_Token参数的值从服务器端交换出一个AccessToken.从返回值参数name我们就可以看到自己微薄的账户名以及即将获取的Oauth_token和Secret键值对.</font></p>

<p><font face="Cambria">如上从上篇的Google Oauth协议概念转换成与Tencent平台交互. 转换成整个流程API的封装. 由此我们可以通过这个Google OAuth在Tencent平台上运用我们可以轻松知道整个获取数据整个流程.</font></p>

<p><font face="Cambria">整理好Oauth认证协议.注意官方给出C#SDK针对普通类库类型. 相对在Windows phone 7开发必须继承Windows phone 7 Class laibry类库.APi我们可以重新设计:</font></p>

<p><img style="background-image:none;border-right-width:0px;padding-left:0px;padding-right:0px;display:inline;float:left;border-top-width:0px;border-bottom-width:0px;border-left-width:0px;padding-top:0px" title="2011-02-11_170704" border="0" alt="2011-02-11_170704" align="left" src="http://images.cnblogs.com/cnblogs_com/chenkai/201102/201102111728237835.png" width="323" height="301"></p>

<p><font face="Cambria"></font></p>

<p> </p>

<p> </p>

<p> </p>

<p> </p>

<p> </p>

<p> </p>

<p> </p>

<p> </p>

<p><font face="Cambria">OAuth协议目录下.OAuthBase作为Google OAuth协议基类. 在此基础上TencentOAuthBase类继承与它,并实现对Tencent基础请求进行处理. 当然如此定义好数据输入和输出通道 剩下就是大量关于Windows phone 7Tencent微博应用定义和实现了. 这就是一个量的问题.API初步定义结构分为三层:</font></p>

<p><font face="Cambria"><img style="background-image:none;border-right-width:0px;padding-left:0px;padding-right:0px;display:inline;float:left;border-top-width:0px;border-bottom-width:0px;border-left-width:0px;padding-top:0px" title="2011-02-11_172156" border="0" alt="2011-02-11_172156" align="left" src="http://images.cnblogs.com/cnblogs_com/chenkai/201102/20110211172823310.png" width="326" height="442"></font></p>

<p> </p>

<p> </p>

<p> </p>

<p> </p>

<p> </p>

<p> </p>

<p> </p>

<p> </p>

<p> </p>

<p> </p>

<p> </p>

<p> </p>

<p><font face="Cambria">如上也是我对Windows phone 7 API封装结构整个设想,.现在已经走到第二步. 关于红色框系.即在完整移植Tencent微博到Windows phone 7客户端后.加上不同于Web终端 移动终端特有的运用. 正在考虑把微博人物之间关系建立人际圈. 加入Bing Map Control相关小细节.</font></p>

<p><font face="Cambria">本篇初步拟定API封装整个层次思路. 实现对Google Oauth协议通信的实现. 有了这些 就不愁数据的交互操作了. 下一篇中我会采用编译好的API实现在Windows phone 7上推送出第一条微博. 如有任何疑问请在留言中提出.</font></p><img src="http://www.cnblogs.com/chenkai/aggbug/1951527.html?type=1" width="1" height="1" alt=""><p>作者: <a href="http://www.cnblogs.com/chenkai/">chenkai</a> 发表于 2011-02-11 17:28 <a href="http://www.cnblogs.com/chenkai/archive/2011/02/11/1951527.html">原文链接</a></p><p>评论: 9　<a href="http://www.cnblogs.com/chenkai/archive/2011/02/11/1951527.html#pagedcomment">查看评论</a>　<a href="http://www.cnblogs.com/chenkai/archive/2011/02/11/1951527.html#commentform">发表评论</a></p><hr><p>最新新闻：<br>· <a href="http://news.cnblogs.com/n/90768/">多玩网官方称再融一亿美元 大量招聘YY工程师</a><span style="color:gray">(2011-02-12 15:19)</span><br>· <a href="http://news.cnblogs.com/n/90767/">脑后装相机教授已忍无可忍</a><span style="color:gray">(2011-02-12 15:15)</span><br>· <a href="http://news.cnblogs.com/n/90766/">朱骏谈Red 5游戏研发：烧了6千万美元还得烧</a><span style="color:gray">(2011-02-12 15:13)</span><br>· <a href="http://news.cnblogs.com/n/90765/">分析师普遍看涨百度：目标价最高160美元</a><span style="color:gray">(2011-02-12 14:44)</span><br>· <a href="http://news.cnblogs.com/n/90764/">谷歌地球10大非常发现：飞机坟场和血湖入列</a><span style="color:gray">(2011-02-12 14:08)</span><br></p><p>编辑推荐：<a href="http://news.cnblogs.com/n/90711/">评论：诺基亚的混乱</a><br></p><p>网站导航：<a href="http://www.cnblogs.com">博客园首页</a>  <a href="http://home.cnblogs.com/">我的园子</a>  <a href="http://news.cnblogs.com">新闻</a>  <a href="http://home.cnblogs.com/ing/">闪存</a>  <a href="http://home.cnblogs.com/group/">小组</a>  <a href="http://space.cnblogs.com/q/">博问</a>  <a href="http://kb.cnblogs.com">知识库</a></p></p>