---
layout: post
title:  "探索Google App Engine背后的奥秘(1)--Google的核心技术"
date:   2010-06-04 23:26:37
author: dbanotes@gmail.com(Fenng)
categories: program
---

## 探索Google App Engine背后的奥秘(1)--Google的核心技术
### by dbanotes@gmail.com(Fenng)
### at 2010-06-04 23:26:37
### original <http://www.dbanotes.net/arch/google_app_engine_arch.html>

<p> 作者：<a href="http://peopleyun.com/">ikewu</a> 发布在 <a href="http://www.dbanotes.net/">dbanotes.net</a>. <a href="http://www.dbanotes.net/index.xml">RSS</a> | Ad.<a href="http://www.infoq.com/cn/vendorcontent/show.action?vcr=1047">Adobe Flash Builder 4 简体中文正式版下载</a>

<blockquote>按：此为客座博文系列。投稿人<a href="http://peopleyun.com/">吴朱华</a>曾在IBM中国研究院从事与云计算相关的研究，现在正致力于研究云计算技术。</blockquote>

<p>本系列文章基于公开资料对Google App Engine的实现机制这个话题进行深度探讨。在切入Google App Engine之前，首先会对Google的核心技术和其整体架构进行分析，以帮助大家之后更好地理解Google App Engine的实现。</p>

<p>本篇将主要介绍Google的十个核心技术，而且可以分为四大类：</p>

<ul>
<li>分布式基础设施：GFS、Chubby 和 Protocol Buffer。 </li>
<li>分布式大规模数据处理：MapReduce 和 Sawzall。 </li>
<li>分布式数据库技术：BigTable 和数据库 Sharding。 </li>
<li>数据中心优化技术：数据中心高温化、12V电池和服务器整合。 </li>
</ul>
<br>
<h1>分布式基础设施</h1>

<p><strong>GFS</strong></p>

<p>由于搜索引擎需要处理海量的数据，所以Google的两位创始人Larry Page和Sergey Brin在创业初期设计一套名为"BigFiles"的文件系统，而GFS（全称为"Google File System"）这套分布式文件系统则是"BigFiles"的延续。</p>
<p>首先，介绍它的架构，GFS主要分为两类节点：</p>
<ul>
<li>Master节点：主要存储与数据文件相关的元数据，而不是Chunk（数据块）。元数据包括一个能将64位标签映射到数据块的位置及其组成文件的表格，数据块副本位置和哪个进程正在读写特定的数据块等。还有Master节点会周期性地接收从每个Chunk节点来的更新（"Heart-beat"）来让元数据保持最新状态。 </li>
<li>Chunk节点：顾名思义，肯定用来存储Chunk，数据文件通过被分割为每个默认大小为64MB的Chunk的方式存储，而且每个Chunk有唯一一个64位标签，并且每个Chunk都会在整个分布式系统被复制多次，默认为3次。 </li>
</ul>

<p>下图就是GFS的架构图：</p>
<p><a href="http://www.dbanotes.net/Images/Google-file-system.png"><img alt="Google-file-system.png" src="http://www.dbanotes.net/assets_c/2010/06/Google-file-system-thumb-600x269-42.png" width="580" height="260" style="text-align:center;display:block;margin:0 auto 20px"></a></p>
<p align="center">图1. GFS的架构图（参片[15]）</p>

<p>接着，在设计上，GFS主要有八个特点：</p>

<ul>
<li>大文件和大数据块：数据文件的大小普遍在GB级别，而且其每个数据块默认大小为64MB，这样做的好处是减少了元数据的大小，能使Master节点能够非常方便地将元数据放置在内存中以提升访问效率。 </li>
<li>操作以添加为主：因为文件很少被删减或者覆盖，通常只是进行添加或者读取操作，这样能充分考虑到硬盘线性吞吐量大和随机读写慢的特点。 </li>
<li>支持容错：首先，虽然当时为了设计方便，采用了单Master的方案，但是整个系统会保证每个Master都会有其相对应的复制品，以便于在Master节点出现问题时进行切换。其次，在Chunk层，GFS已经在设计上将节点失败视为常态，所以能非常好地处理Chunk节点失效的问题。 </li>
<li>高吞吐量：虽然其单个节点的性能无论是从吞吐量还是延迟都很普通，但因为其支持上千的节点，所以总的数据吞吐量是非常惊人的。 </li>
<li>保护数据：首先，文件被分割成固定尺寸的数据块以便于保存，而且每个数据块都会被系统复制三份。 </li>
<li>扩展能力强：因为元数据偏小，使得一个Master节点能控制上千个存数据的Chunk节点。 </li>
<li>支持压缩：对于那些稍旧的文件，可以通过对它进行压缩，来节省硬盘空间，并且压缩率非常惊人，有时甚至接近90%。 </li>
<li>用户空间：虽然在用户空间运行在运行效率方面稍差，但是更便于开发和测试，还有能更好利用Linux的自带的一些POSIX API。 </li>
</ul>

<p>现在Google内部至少运行着200多个GFS集群，最大的集群有几千台服务器，并且服务于多个Google服务，比如Google搜索。但由于GFS主要为搜索而设计，所以不是很适合新的一些Google产品，比YouTube、Gmail和更强调大规模索引和实时性的Caffeine搜索引擎等，所以Google已经在开发下一代GFS，代号为"Colossus"，并且在设计方面有许多不同，比如：支持分布式Master节点来提升高可用性并能支撑更多文件，Chunk节点能支持1MB大小的chunk以支撑低延迟应用的需要。</p>

<p><strong>Chubby</strong></p>

<p>简单的来说，Chubby 属于分布式锁服务，通过 Chubby，一个分布式系统中的上千个client都能够对于某项资源进行"加锁"或者"解锁"，常用于BigTable的协作工作，在实现方面是通过对文件的创建操作来实现"加锁"，并基于著名科学家Leslie Lamport的Paxos算法。</p>

<p><strong>Protocol Buffer</strong></p>

<p>Protocol Buffer，是Google内部使用一种语言中立、平台中立和可扩展的序列化结构化数据的方式，并提供 Java、C++ 和 Python 这三种语言的实现，每一种实现都包含了相应语言的编译器以及库文件，而且它是一种二进制的格式，所以其速度是使用 XML 进行数据交换的10倍左右。它主要用于两个方面：其一是RPC通信，它可用于分布式应用之间或者异构环境下的通信。其二是数据存储方面，因为它自描述，而且压缩很方便，所以可用于对数据进行持久化，比如存储日志信息，并可被Map Reduce程序处理。与Protocol Buffer比较类似的产品还有Facebook的 <a href="http://incubator.apache.org/thrift/">Thrift </a>，而且 Facebook 号称Thrift在速度上还有一定的优势。</p>

<h1>分布式大规模数据处理</h1>

<p><strong>MapReduce</strong></p>

<p>首先，在Google数据中心会有大规模数据需要处理，比如被网络爬虫（Web Crawler）抓取的大量网页等。由于这些数据很多都是PB级别，导致处理工作不得不尽可能的并行化，而Google为了解决这个问题，引入了MapReduce这个编程模型，MapReduce是源自函数式语言，主要通过"Map（映射）"和"Reduce（化简）"这两个步骤来并行处理大规模的数据集。Map会先对由很多独立元素组成的逻辑列表中的每一个元素进行指定的操作，且原始列表不会被更改，会创建多个新的列表来保存Map的处理结果。也就意味着，Map操作是高度并行的。当Map工作完成之后，系统会先对新生成的多个列表进行清理（Shuffle）和排序，之后会这些新创建的列表进行Reduce操作，也就是对一个列表中的元素根据Key值进行适当的合并。</p>

<p>下图为MapReduce的运行机制：</p>

<p><a href="http://www.dbanotes.net/Images/Map%20Reduce.PNG"><img alt="Map Reduce.PNG" src="http://www.dbanotes.net/assets_c/2010/06/Map%20Reduce-thumb-580x415-44.png" width="580" height="415"></a> </p>
<p align="center">图2. MapReduce的运行机制（参[19]） </p>

<p>接下来，将根据上图来举一个MapReduce的例子：比如，通过搜索Spider将海量的Web页面抓取到本地的GFS集群中，然后Index系统将会对这个GFS集群中多个数据Chunk进行平行的Map处理，生成多个Key为URL，value为html页面的键值对（Key-Value Map），接着系统会对这些刚生成的键值对进行Shuffle（清理），之后系统会通过Reduce操作来根据相同的key值（也就是URL）合并这些键值对。</p>

<p>最后，通过MapReduce这么简单的编程模型，不仅能用于处理大规模数据，而且能将很多繁琐的细节隐藏起来，比如自动并行化，负载均衡和机器宕机处理等，这样将极大地简化程序员的开发工作。MapReduce可用于包括"分布grep，分布排序，web访问日志分析，反向索引构建，文档聚类，机器学习，基于统计的机器翻译，生成Google的整个搜索的索引"等大规模数据处理工作。Yahoo也推出MapReduce的开源版本Hadoop，而且Hadoop在业界也已经被大规模使用。</p>

<p><strong>Sawzall</strong></p>

<p>Sawzall可以被认为是构建在MapReduce之上的采用类似Java语法的DSL（Domain-Specific Language），也可以认为它是分布式的AWK。它主要用于对大规模分布式数据进行筛选和聚合等高级数据处理操作，在实现方面，是通过解释器将其转化为相对应的MapReduce任务。除了Google的Sawzall之外，yahoo推出了相似的Pig语言，但其语法类似于SQL。</p>

<h1>分布式数据库技术</h1>

<p><strong>BigTable</strong></p>

<p>由于在Google的数据中心存储PB级以上的非关系型数据时候，比如网页和地理数据等，为了更好地存储和利用这些数据，Google开发了一套数据库系统，名为"BigTable"。BigTable不是一个关系型的数据库，它也不支持关联（Join）等高级SQL操作，取而代之的是多级映射的数据结构，并是一种面向大规模处理、容错性强的自我管理系统，拥有TB级的内存和PB级的存储能力，使用结构化的文件来存储数据，并每秒可以处理数百万的读写操作。</p>

<p>什么是多级映射的数据结构呢？就是一个稀疏的，多维的，排序的Map，每个Cell由行关键字，列关键字和时间戳三维定位．Cell的内容是一个不解释的字符串，比如下表存储每个网站的内容与被其他网站的反向连接的文本。 反向的URL com.cnn.www是这行的关键字；contents列存储网页内容，每个内容有一个时间戳，因为有两个反向连接，所以archor的Column Family有两列：anchor: cnnsi.com和anchhor:my.look.ca。Column Family这个概念，使得表可以轻松地横向扩展。下面是它具体的数据模型图：</p>

<p><a href="http://www.dbanotes.net/Images/Big%20Table%20Model.PNG"><img alt="Big Table Model.PNG" src="http://www.dbanotes.net/assets_c/2010/06/Big%20Table%20Model-thumb-580x132-46.png" width="580" height="132"></a></p>
<p align="center">图3. BigTable数据模型图（参[4]） </p>

<p>在结构上，首先，BigTable基于GFS分布式文件系统和Chubby分布式锁服务。其次BigTable也分为两部分：其一是Master节点，用来处理元数据相关的操作并支持负载均衡。其二是tablet节点，主要用于存储数据库的分片tablet，并提供相应的数据访问，同时Tablet是基于名为SSTable的格式，对压缩有很好的支持。</p>

<p><a href="http://www.dbanotes.net/Images/BigTable.PNG"><img alt="BigTable.PNG" src="http://www.dbanotes.net/assets_c/2010/06/BigTable-thumb-580x335-48.png" width="580" height="335" style="text-align:center;display:block;margin:0 auto 20px"></a></p>
<p align="center">图4. BigTable架构图（参[15]） </p>

<p>BigTable正在为Google六十多种产品和项目提供存储和获取结构化数据的支撑平台，其中包括有Google Print、 Orkut、Google Maps、Google Earth和Blogger等，而且Google至少运行着500个BigTable集群。    </p>

<p>随着Google内部服务对需求的不断提高和技术的不断地发展，导致原先的BigTable已经无法满足用户的需求，而Google也正在开发下一代BigTable，名为"Spanner（扳手）"，它主要有下面这些BigTable所无法支持的特性：</p>

<ul>
<li>支持多种数据结构，比如table，familie，group和coprocessor等。 </li>
<li>基于分层目录和行的细粒度的复制和权限管理。 </li>
<li>支持跨数据中心的强一致性和弱一致性控制。 </li>
<li>基于Paxos算法的强一致性副本同步，并支持分布式事务。 </li>
<li>提供许多自动化操作。 </li>
<li>强大的扩展能力，能支持百万台服务器级别的集群。 </li>
<li>用户可以自定义诸如延迟和复制次数等重要参数以适应不同的需求。 </li>
</ul>

<p><strong>数据库Sharding</strong></p>

<p>Sharding就是分片的意思，虽然非关系型数据库比如BigTable在Google的世界中占有非常重要的地位，但是面对传统OLTP应用，比如广告系统，Google还是采用传统的关系型数据库技术，也就是MySQL，同时由于Google所需要面对流量非常巨大，所以Google在数据库层采用了分片（Sharding）的水平扩展（Scale Out）解决方案，分片是在传统垂直扩展（Scale Up）的分区模式上的一种提升，主要通过时间，范围和面向服务等方式来将一个大型的数据库分成多片，并且这些数据片可以跨越多个数据库和服务器来实现水平扩展。</p>
<p>Google整套数据库分片技术主要有下面这些优点：</p>

<ul>
<li>扩展性强：在Google生产环境中，已经有支持上千台服务器的MySQL分片集群。 </li>
<li>吞吐量惊人：通过巨大的MySQL分片集群能满足巨量的查询请求。 </li>
<li>全球备份：不仅在一个数据中心还是在全球的范围，Google都会对MySQL的分片数据进行备份，这样不仅能保护数据，而且方便扩展。 </li>
</ul>

<p>在实现方面，主要可分为两块：其一是在MySQL InnoDB基础上添加了数据库分片的技术。其二是在ORM层的Hibernate的基础上也添加了相关的分片技术，并支持虚拟分片（Virtual Shard）来便于开发和管理。同时Google也已经将这两方面的代码提交给相关组织。</p>

<h1>数据中心优化技术</h1>

<p><strong>数据中心高温化</strong></p>

<p>大中型数据中心的PUE（Power Usage Effectiveness）普遍在2左右，也就是在服务器等计算设备上耗1度电，在空调等辅助设备上也要消耗一度电。对一些非常出色的数据中心，最多也就能达到1.7，但是Google通过一些有效的设计使部分数据中心到达了业界领先的1.2，在这些设计当中，其中最有特色的莫过于数据中心高温化，也就是让数据中心内的计算设备运行在偏高的温度下，Google的能源方面的总监Erik Teetzel在谈到这点的时候说："普通的数据中心在70华氏度（21摄氏度）下面工作，而我们则推荐80华氏度（27摄氏度）"。但是在提高数据中心的温度方面会有两个常见的限制条件：其一是服务器设备的崩溃点，其二是精确的温度控制。如果做好这两点，数据中心就能够在高温下工作，因为假设数据中心的管理员能对数据中心的温度进行正负1/2度的调节，这将使服务器设备能在崩溃点5度之内工作，而不是常见的20度之内，这样既经济，又安全。还有，业界传言Intel为Google提供抗高温设计的定制芯片，但云计算界的顶级专家James Hamilton认为不太可能，因为虽然处理器也非常惧怕热量，但是与内存和硬盘相比还是强很多，所以处理器在抗高温设计中并不是一个核心因素。同时他也非常支持使数据中心高温化这个想法，而且期望将来数据中心甚至能运行在40摄氏度下，这样不仅能节省空调方面的成本，而且对环境也很有利。</p>

<p><strong>12V电池</strong></p>

<p>由于传统的UPS在资源方面比较浪费，所以Google在这方面另辟蹊径，采用了给每台服务器配一个专用的12V电池的做法来替换了常用的UPS，如果主电源系统出现故障，将由该电池负责对服务器供电。虽然大型UPS可以达到92%到95%的效率，但是比起内置电池的99.99%而言是非常捉襟见肘的，而且由于能量守恒的原因，导致那么未被UPS充分利用的电力会被转化成热能，这将导致用于空调的能耗相应地攀升，从而走入一个恶性循环。同时在电源方面也有类似的"神来之笔"，普通的服务器电源会同时提供5V和12V的直流电。但是Google设计的服务器电源只输出12V直流电，必要的转换在主板上进行，虽然这种设计会使主板的成本增加1美元到2美元，但是它不仅能使电源能在接近其峰值容量的情况下运行，而且在铜线上传输电流时效率更高。</p>

<p><strong>服务器整合</strong></p>

<p>谈到虚拟化的杀手锏时，第一个让人想到肯定是服务器整合，而且普遍能实现1:8的整合率来降低各方面的成本。有趣的是，Google在硬件方面也引入类似服务器整合的想法，它的做法是在一个机箱大小的空间内放置两台服务器，这些做的好处有很多，首先，减小了占地面积。其次，通过让两台服务器共享诸如电源等设备，来降低设备和能源等方面的投入。</p>

<p>本篇结束，<a href="http://www.dbanotes.net/arch/google_app_engine_arch_guess.html">下篇</a>将猜想一下Google整体架构。</p>

<p>--EOF--</p></p>
<hr>
<p><strong>最近文章|Recent Articles</strong></p>
   <ul>
    
      <li><a href="http://www.dbanotes.net/review/web_product.html">Web 产品的改进</a></li>
    
      <li><a href="http://www.dbanotes.net/jobs/Jobs_info.html">与工作有关的几则信息</a></li>
    
      <li><a href="http://www.dbanotes.net/review/sedo_sucks.html">在 Sedo 交易域名应谨慎</a></li>
    
      <li><a href="http://www.dbanotes.net/review/97_Things_Every_Software_Architect_Should_Know.html">架构师应该知道的那些事儿</a></li>
    
   </ul>
<p>本站赞助商：<a href="http://www.douban.com/">豆瓣网</a></p>
<p><strong> 评论数(10)|<a href="http://www.dbanotes.net/arch/google_app_engine_arch.html#comments" title="Comment on: 探索Google App Engine背后的奥秘(1)--Google的核心技术">添加评论</a></strong> | 最近作者还说了什么? Follow <a href="http://www.twitter.com/fenng">Fenng@Twitter</a><br>本文网址：<a href="http://www.dbanotes.net/arch/google_app_engine_arch.html">http://www.dbanotes.net/arch/google_app_engine_arch.html</a></p>
<p>DBA Notes 理念: 用简约的技术取得最大的收益...</p>



<div name="ClickComments"></div>

<div>
<a href="http://feeds.feedburner.com/~ff/webarch?a=mgunoeY6VZc:npuuqADVI-U:yIl2AUoC8zA"><img src="http://feeds.feedburner.com/~ff/webarch?d=yIl2AUoC8zA" border="0"></a> <a href="http://feeds.feedburner.com/~ff/webarch?a=mgunoeY6VZc:npuuqADVI-U:qj6IDK7rITs"><img src="http://feeds.feedburner.com/~ff/webarch?d=qj6IDK7rITs" border="0"></a> <a href="http://feeds.feedburner.com/~ff/webarch?a=mgunoeY6VZc:npuuqADVI-U:F7zBnMyn0Lo"><img src="http://feeds.feedburner.com/~ff/webarch?i=mgunoeY6VZc:npuuqADVI-U:F7zBnMyn0Lo" border="0"></a> <a href="http://feeds.feedburner.com/~ff/webarch?a=mgunoeY6VZc:npuuqADVI-U:V_sGLiPBpWU"><img src="http://feeds.feedburner.com/~ff/webarch?i=mgunoeY6VZc:npuuqADVI-U:V_sGLiPBpWU" border="0"></a> <a href="http://feeds.feedburner.com/~ff/webarch?a=mgunoeY6VZc:npuuqADVI-U:mqyYa2mfVbY"><img src="http://feeds.feedburner.com/~ff/webarch?d=mqyYa2mfVbY" border="0"></a> <a href="http://feeds.feedburner.com/~ff/webarch?a=mgunoeY6VZc:npuuqADVI-U:I9og5sOYxJI"><img src="http://feeds.feedburner.com/~ff/webarch?d=I9og5sOYxJI" border="0"></a> <a href="http://feeds.feedburner.com/~ff/webarch?a=mgunoeY6VZc:npuuqADVI-U:bcOpcFrp8Mo"><img src="http://feeds.feedburner.com/~ff/webarch?d=bcOpcFrp8Mo" border="0"></a>
</div>