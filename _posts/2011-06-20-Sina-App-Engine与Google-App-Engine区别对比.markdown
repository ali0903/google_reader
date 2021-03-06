---
layout: post
title:  "Sina App Engine与Google App Engine区别对比"
date:   2011-06-20 07:05:57
author: 
categories: program
---

## Sina App Engine与Google App Engine区别对比
### by 
### at 2011-06-20 07:05:57
### original <http://www.cnbeta.com/articles/146261.htm>

<div><a rel="nofollow" href="http://www.cnbeta.com/topics/138.htm"><img src="http://img.cnbeta.com/topics/communication.png" alt="通信技术" name="sign" align="right"></a>
        <p><b>感谢<a rel="nofollow" href="http://www.lovelucy.info">LoveLucy</a>的投递</b><br>
新浪推出了自己的云计算服务平台，和Google一样，都叫App Engine。根据NIST对于云计算的定义，Google App Engine属于PaaS平台型服务，那么<b>它具体提供什么样的服务，新浪推出的Sina App Engine和Google的又有哪些区别呢？</b></p>
		<p><span style="font-weight:bold">背景</span><p>云计算服务在Google、Amazon等公司成功产品的引领下，正在商业化的道路上大步向前。在NIST的定义中，有三种云计算服务模型。</p>
<ul><li>Software as a Service (<strong>SaaS</strong>)：软件即服务模型，用户直接使用构建在云端的软件。例如，Google Docs, Salesforce.com</li>
<li>Platform as a Service (<strong>PaaS</strong>)：平台即服务模型，用户使用云平台所支持的语言和工具，开发应用并部署在云平台。例如，Google App Engine, Force.com</li>
<li>Infrastructure as a Service (<strong>IaaS</strong>)：基础架构即服务模型，提供处理器、存储、网络等（虚拟）硬件资源给用户，用户可任意安装软件和开发环境，包括安装操作系统。例如，Amazon EC2, Joyent</li>
</ul>
<p>可见，三种模型从应用到平台再到架构，越来越底层，用户得到的可操作性和灵活性也越来越大。</p>
<h4>Google App Engine是什么？</h4>
<p>Google App Engine（GAE） 
提供一整套开发组件来让用户轻松地在本地构建和调试网络应用，之后能让用户在Google强大的基础设施上部署和运行网络应用程序。配置可随应用的访问量
和数据存储需要的增长轻松扩展，使用 GAE，将不再需要维护服务器：只需上传你的应用程序，它便可立即为你的客户提供服务。</p>
<p>简单来说，这和虚拟主机服务类似，只是运行环境不同。虚拟主机支持的是ASP, JSP, PHP等网页应用，而GAE现支持Java、Python和Google自家开发的Go这三种语言开发的应用程序，并为这三种语言提供基本相同的功能和API。</p>
<p>GAE提供大量的免费使用额度和灵活的资费标准。多达 500 MB 的存储空间，以及可支持每月约 500 万页面浏览量的足够的 CPU 和带宽，完全免费。选择付费服务则可按需提高相应配置。</p>
<p><strong>功能支持</strong></p>
<ul><li>网址抓取（<strong>URL Fetch</strong>）：访问互联网上的资源，抓取检索数据。</li>
<li>邮件（<strong>Mail</strong>）： GAE可以利用基于Gmail的基础设施来发送电子邮件。</li>
<li><strong>Memcache</strong>缓存：高性能的内存缓存保障，对于那些不需要持久性存储和事务功能的数据（例如临时数据或从数据存储区复制到缓存以进行高速访问的数据）很有用。</li>
<li>图像操作（<strong>Image Manipulation</strong>）：使用该 API，您可以对 JPEG 和 PNG 格式的图像进行缩放、裁剪、旋转和翻转，还能使用预先定义的算法提升图片的质量。</li>
<li>计划任务和任务队列（<strong>Scheduled Tasks &amp; Task Queues</strong>）：允许将任务计划为按指定间隔运行，这些任务通常称为Cron job。另外可以通过在一个队列插入任务（以Web Hook的形式）来实现后台处理，GAE会根据调度方面的设置来安排这个队列里面的任务执行。</li>
</ul>
<p><strong>管理界面</strong><br>
GAE与Google帐户集成，进行登录管理和用户认证。登录控制台http://appengine.google.com/</p>
<div style="width:581px"><a rel="nofollow" href="http://img.cnbeta.com/newsimg/110620/07055701657900667.png"><img title="GAE管理平台" src="http://img.cnbeta.com/newsimg/110620/07055701657900667.png" alt="GAE管理平台" height="564" width="571"></a><p>GAE管理平台</p>
</div>
<h4>Sina App Engine是什么？</h4>
<p>Sina App Engine（SAE），是由新浪公司开发和运营的开放云计算平台。SAE为App开发者提供稳定、快捷、透明、可控的服务化的平台，并且减少开发者的开发和维护成本。和GAE一样，他们都属于PaaS平台型云计算服务。</p>
<p>SAE通过Web Service Pool，提供以PHP+HTTP为基础的计算中心，这对于一个普通网站来说完全足够了。这样一来，大量草根站长就可以使用SAE来当做虚拟主机免费空间，希望开放注册后新浪能够有足够技术和经济实力支撑住整个平台。</p>
<p>与传统主机托管服务相比而言，传统托管面向的是硬件软件设备，使用者得到的也是设备的使用权；而SAE面向的服务，使用者得到的是服务的使用权。开发者可以在其上通过在线调试、日志分析、协作共享等功能进行web开发。</p>
<p>最重要的是SAE提供的完整的分布式web服务的解决方案，具有高可靠性，开发者只需专注于应用的功能开发，而不必担心故障宕机、服务扩容，甚至不
用关心主辅同步和读写分离，因为所有这些SAE都已经为用户完整提供。与虚拟主机采用固定计费的方式不同，SAE采用预充值方式，“所付即所用，所付仅所
用”，按需付费更加灵活和节省成本，web服务的一切损耗均提供报表查询和账单汇总，用户一目了然。SAE可以为运营节约成本，包括运维成本。</p>
<p>使用SAE云计算，省钱+高效+可靠。由此可以预见，国内的虚拟主机商可能将因此面临不小的冲击。</p>
<p><strong>功能支持</strong></p>
<ul><li>PHP运行环境</li>
<li>MySQL：分布式数据库服务，通过RDC提供</li>
<li>Memcache：分布式缓存服务</li>
<li>Storage：文件存储服务</li>
<li>Cron：定时服务</li>
<li>Image：图像处理服务</li>
<li>FetchURL：网页抓取服务</li>
<li>Mail：邮件服务</li>
<li>TaskQueue：异步轻量级任务队列</li>
<li>DeferredJob：异步大任务延迟队列</li>
<li>Counter：计数器服务</li>
</ul>
<p>从上面可以看到，Sina App Engine提供了大部分Google App 
Engine支持的功能，另外SAE选择PHP作为首选支持语言，并支持MySQL数据库。这主要是因为PHP语言在目前国内Web开发的比重最大。未来
SAE会考虑支持更多的Web开发语言，如Python、Java等。</p>
<p>在SAE上的所有服务都具有大型分布式系统的高可靠性、高扩展性、易维护性等优点。目前SAE的内核（即Runtime）和大部分服务之间的通讯是
采用HTTP REST方式，SAE平台理论上可以对接各种服务，对接的第三方服务和内部服务一样可以通过在线管理平台进行管理、查看统计配额、计费等。</p>
<p><strong>管理界面</strong><br>
登录http://sae.sina.com.cn，可以通过Web在线管理平台对应用进行管理。目前可以使用新浪微博帐号登录，并支持OAuth。</p>
<div style="width:498px"><a rel="nofollow" href="http://img.cnbeta.com/newsimg/110620/0706001465652481.png"><img title="SAE在线管理平台" src="http://img.cnbeta.com/newsimg/110620/0706001465652481.png" alt="SAE在线管理平台" height="459" width="488"></a><p>SAE在线管理平台</p>
</div>
<h4>SAE和GAE的比较</h4>
<table border="1" cellpadding="0" cellspacing="0">
<tbody>
<tr>
<td valign="top" width="126"><br>
</td>
<td valign="top" width="150">Sina App Engine</td>
<td valign="top" width="150">Google App Engine</td>
</tr>
<tr>
<td valign="top" width="126">云计算模型</td>
<td valign="top" width="150">PaaS</td>
<td valign="top" width="150">PaaS</td>
</tr>
<tr>
<td valign="top" width="126">支持语言</td>
<td valign="top" width="150">PHP</td>
<td valign="top" width="150">Java、Python、Go</td>
</tr>
<tr>
<td valign="top" width="126">数据库支持</td>
<td valign="top" width="150">MySQL 最大5GB</td>
<td valign="top" width="150">暂不支持</td>
</tr>
<tr>
<td valign="top" width="126">每个帐号可拥有app数量</td>
<td valign="top" width="150">10个</td>
<td valign="top" width="150">10个</td>
</tr>
<tr>
<td valign="top" width="126">单app存储限额</td>
<td valign="top" width="150">最多10GB，单文件不大于4M</td>
<td valign="top" width="150">1GB免费，无最大上限</td>
</tr>
<tr>
<td valign="top" width="126">代码大小</td>
<td valign="top" width="150">每帐户不超过100M，单app总代码不超过50M</td>
<td valign="top" width="150">单app不超过150MB</td>
</tr>
<tr>
<td valign="top" width="126">绑定域名</td>
<td valign="top" width="150">需另行申请，备案</td>
<td valign="top" width="150">支持</td>
</tr>
<tr>
<td valign="top" width="126">免费额度</td>
<td valign="top" width="150">各项服务通过扣除虚拟货币“云豆”实现<a rel="nofollow" title="云豆限额标准" href="http://sae.sina.com.cn/?m=faqs&amp;catId=7#show_38">限额</a>。<br>
每日免费发放200云豆（实名认证后1000云豆）</td>
<td valign="top" width="150">每日6.5 CPU-hours，流入流出带宽各1GB，存储1GB。<br>
<a rel="nofollow" title="GAE免费使用额度" href="http://code.google.com/appengine/docs/quotas.html">具体额度</a></td>
</tr>
<tr>
<td valign="top" width="126">超过免费限额的收费标准</td>
<td valign="top" width="150">1元=100云豆，暂未支持支付</td>
<td valign="top" width="150">流入带宽：0.1美元/GB<br>
流出带宽：0.12美元/GB<br>
CPU 时间：0.1美元/CPU小时<br>
存储：0.15美元/GB 每月</td>
</tr>
</tbody>
</table>
<p>现在国内的企业都在嚷嚷着云计算，不过一直都是雷声
大，雨点小，多数仅仅是属于概念炒作而已。而Sina App 
Engine的出现却让人眼前一亮，这个平台如果能够完善下去，的确可以成为中国云计算服务的典范。</p>
<p>注册Sina App Engine：<a rel="nofollow" href="http://sae.sina.com.cn/">http://sae.sina.com.cn/<br>
</a>注册Google App Engine：<a rel="nofollow" href="https://appengine.google.com/">https://appengine.google.com/</a></p>
<br>
供稿/ <a rel="nofollow" href="http://www.lovelucy.info">LoveLucy</a><br></p></div>