---
layout: post
title:  "Linux kernel测试初探"
date:   2012-12-25 20:57:58
author: 
categories: program
---

## Linux kernel测试初探
### by 
### at 2012-12-25 20:57:58
### original <http://blog.jobbole.com/31690/?utm_source=rss&utm_medium=rss&utm_campaign=linux-kernel%25e6%25b5%258b%25e8%25af%2595%25e5%2588%259d%25e6%258e%25a2>

<p>来源：<a href="http://qa.baidu.com/blog/?p=1026" rel="nofollow">百度质量部</a></p>
<p><strong>前言</strong></p>
<p>Linux基金会在4月3日公布了Linux开发年报，向我们展示了linux kernel作为世上最大开源合作项目之一的魅力。自2005年以来，共有800家公司7800名开发人员参与Linux kernel开发，最近一年也有200家公司共1000名开发人员参与。目前在Linus Torvalds的监督之下，Linux核心约2到3个月发布一个新的稳定版本，每次更新大约包含8000到12000项修改，平均每小时有提交6.88次修改。内核所有文件代码已超过1500万行，大约以每年10%的代码量在增长。</p>
<p>规模组织如此巨大，开发又活跃，且对质量有非常高要求的项目，不禁好奇它是如何做好质量保证的，于是查阅了网上较多资料，探索Linux kernel的质量保证之道。</p>
<p><strong>名词解释</strong></p>
<p>Linux Kernel和GNU/Linux。Linux kernel作为系统的最底层，是负责管理硬件、执行任务调度、维护整体安全和完整性的系统软件。一个内核不是一套完整的操作系统，人们将 Linux 内核和 GNU 系统组成完整的自由系统：基于 Linux 的 GNU 系统（或简称为 GNU/Linux 系统）。咱们平时所说的Linux系统多是指GNU/Linux。</p>
<p>Patch。升级内核时，不必要每次都下载或提交整个的源码文件，而是使用patch，或者叫做补丁或升级包。很多不同版本的内核源代码是以一组补丁(补丁集)甚至是若干独立补丁的形式存在的。这些补丁应该应用在特定版本的内核树上。</p>
<p>内核树。树的意思是“包含内核源代码的目录的内容”。另外一层意思是“内核源码的开发分支”。不同的分支命名也不一样，比较流行的分支（树）有-next树（前身是Andrew Morton维护的-mm树），-rt 树（实时树，包含把linux转换为实时系统的补丁）。</p>
<p>-next 树，由Stephen Rothwell维护，是一些其他树以及各种独立的实验性补丁，它集合了主线和多种内核子系统(子系统树)维护人所使用的树，子系统树中含有被认为是实验性的和还没准备好合并到主线中的各种补丁。换句话说，-next树的每个版本都是带有额外修改和一些bug的主线的未来快照。Bug尽可能在这些补丁合并到主线之前得到修正。</p>
<p><strong>开发流程</strong></p>
<p>要了解测试，先得看看整个项目的开发过程。</p>
<p>Linux kernel开发流程是基于一个松散的、定时发布的滚动模型，每2-3个月发布一个新稳定版本。2-3个月被认为是一个合适的时间周期，能加快开发速度的同时，也减少发行商需要处理的外部修改，更短则导致测试时间太少，新内核测试不充分；更长则导致发布版本之间会堆积太多工作。此模型从2005年正式确认，尽早将新功能融入内核主线，将发布给用户的延期降至最低。</p>
<p>在准备提交Linux kernel代码前，开发者将升级拆分为一个个小的互相独立的单元，叫做补丁（patch）。每个补丁通常只做一件事情，并且应当保证系统在打上此补丁之后是依然能正常编译和工作的。这导致每个补丁都可以做代码质量和正确性评审，同时也导致每个版本提交的补丁数较大。</p>
<p>提交的补丁并不是直接进入到内核主线，参考图一的代码流动过程，只有一个人能将补丁放入到内核主线：Linus Torvalds。但是随着越来越多的补丁加入到内核，大约只有2%的补丁是Linux直接选入的。内核代码从逻辑上是拆分成一系列的子系统集合。每个子系统负责内核的一部分（比如网络、SCSI驱动、特定架构支持、内存管理、视频设备等），并且拥有指定的维护者，维护者对该子系统的代码总体负责。当前有过超过100个子系统。补丁先是进入到一个子系统树，子系统维护者接受修改时，补丁就会附加“由谁签收通过” 的一行签字。该信息表明此补丁可以合入到内核。</p>
<p><a href="http://blog.jobbole.com/wp-content/uploads/2012/12/kernel-testing-01.jpg" rel="lightbox[31690]" title="Linux kernel测试初探"><img title="Linux kernel测试初探" src="http://blog.jobbole.com/wp-content/uploads/2012/12/kernel-testing-01.jpg" alt="Linux kernel测试初探" width="558" height="409"></a></p>
<p style="text-align:center">图一 代码流动过程</p>
<p>Linux-next树本意是在Linus合并代码前将各子系统的代码整合到一起，并进行测试，挑选出可能存在的冲突和跨子系统问题。<br>
<strong></strong></p>
<p><strong>实际例子</strong></p>
<p>一个新版本2.6.20的发布过程如下图二。在每一个版本开始时，Linus会打开合并窗口（merge window），从那时起，被认为是可靠稳定的代码会被合并到内核主线，新的功能或驱动等得以加入进来，合并窗口会开启两周，然后Linus会宣布窗口关闭，并发布第一个rc版本，如下图2.6.20-rc1。此-rc1版本的发布意味着新功能的加入已经完成，以后再加入的补丁只能是修复问题，以确保此版本的稳定，但也有即少数包含新功能的补丁会被认为是不友好的。问题修复的代码不断进入主线，Linus接下来大约每周发布一个新的rc版本，通常会在6-9个rc版本后认为此版本已经稳定并且最终发布2.6.20版本，再进入下一个三位版本的迭代。发布之后，此2.6.x版本就移交给稳定组（stable team），稳定组时而会再发布2.6.x.y版本，每个四位版本的发布会有两个条件：（1）修复重大的bug；（2）该补丁也已被融入到下个三位版本开发的主线中了。稳定组通常维护一个稳定版本6个月左右，之后就由使用该版内核的发行版发行商负责。</p>
<p style="text-align:center"><a href="http://blog.jobbole.com/wp-content/uploads/2012/12/kernel-testing-02.jpg" rel="lightbox[31690]" title="Linux kernel测试初探"><img title="Linux kernel测试初探" src="http://blog.jobbole.com/wp-content/uploads/2012/12/kernel-testing-02.jpg" alt="Linux kernel测试初探" width="558" height="419"></a><br>
图二 一个版本的发布过程</p>
<p style="text-align:left"><strong>测试</strong></p>
<p style="text-align:left">对Linux kernel测试面临的挑战巨大：如前面的数据所显示，（1）其快速活跃的开发；（2）经常性的版本发布，每次发布都包含大量修改，以及内核本身支持配置的灵活性、可扩展性，待测点太多（3）需要覆盖各种平台，Linux已支持二十多种平台体系，是支持平台最广的系统；（4）过时的case需要维护以及大量遗留代码。</p>
<p style="text-align:center">与松散的开发流程相对应，没有看到正式的测试流程，开源软件很大一部分的测试执行是依靠用户实际运行使用。如下图三所示。<br>
<a href="http://blog.jobbole.com/wp-content/uploads/2012/12/kernel-testing-01.jpg" rel="lightbox[31690]" title="Linux kernel测试初探"><img title="Linux kernel测试初探" src="http://blog.jobbole.com/wp-content/uploads/2012/12/kernel-testing-01.jpg" alt="Linux kernel测试初探" width="558" height="409"></a><br>
图三 开源测试的大体流程</p>
<p><strong>测试项目简介</strong></p>
<p>Linux kernel与测试相关较出名的开源项目有：</p>
<p>LTP：Linux Test Project http://ltp.sourceforge.net/。</p>
<p>Autotest：http://autotest.<span><a href="http://blog.jobbole.com/6492/" title="GitHub如何运作：时间并不决定一切">GitHub</a></span>.com/</p>
<p>CrackerJack Project：http://ossipedia.ipa.go.jp/crackerjack/index.html。</p>
<p>LTP是一个联合项目主要验证Linux系统的可靠性、健壮性和稳定性，最先由SGI™启动，并由IBM®负责维护。2012年4月发布的最新稳定版本已拥有3000+的case（case增长并不多，06年时就有2900+的case），用于测试Linux kernel以及相关功能。使用的<span><a href="http://blog.jobbole.com/tag/%E7%BC%96%E7%A8%8B%E8%AF%AD%E8%A8%80/" title="如何选择语言和编程语言排名相关文章">编程语言</a></span>主要是ANSI-C（占94%），以及Bash脚本（占5%），还有Perl（占0.62%）。它也有一套使用ANSI-C和Bash写case的模板。</p>
<p>Autotest是后启之秀，最早被用于Linux kernel自动化测试的框架，使用python语言，现在也被多个其它项目所应用。新case能较容易添加，ANSI-C或者Bash写的case也能较方便地融入此框架。针对Linux kernel的测试项目地址是http://test.kernel.org，用于交流、共享和分析测试数据。Autotest框架分服务端和客户端，在监听到有新版本发布（三位版本、四位版本、-rc版本、-git版本）时，会自动触发执行自动化，执行过程见下图四。</p>
<p style="text-align:center"><a href="http://blog.jobbole.com/wp-content/uploads/2012/12/kernel-testing-04.jpg" rel="lightbox[31690]" title="Linux kernel测试初探"><img title="Linux kernel测试初探" src="http://blog.jobbole.com/wp-content/uploads/2012/12/kernel-testing-04.jpg" alt="Linux kernel测试初探" width="558" height="334"></a><br>
图四 Autotest自动化执行流程</p>
<p style="text-align:center">CrackerJack 是一个主要负责linux kernel兼容性测试的项目，找出不同内核版本系统调用的diff情况，已支持317个系统调用。它也是使用Autotest框架执行，diff结果是个矩阵可见下图五或者网址http://ossipedia.ipa.go.jp/crackerjack/compare_results.html。Diff比较是智能的，并非单独地比较输出结果，比如time时间调用每次系统返回都是不一样的，比较时就是检查两次调用之间的时间差。<br>
<a href="http://blog.jobbole.com/wp-content/uploads/2012/12/kernel-testing-05.jpg" rel="lightbox[31690]" title="Linux kernel测试初探"><img title="Linux kernel测试初探" src="http://blog.jobbole.com/wp-content/uploads/2012/12/kernel-testing-05.jpg" alt="Linux kernel测试初探" width="558" height="342"></a><br>
图五 CrackerJack diff结果</p>
<p><strong>测试方式</strong></p>
<p>测试手段是多样的：单测、集成测试、功能测试、性能测试、压力测试、回归测试等，但没有一种手段在任意时候都合适。质量保证是多方面的，除了要求开发者写出高质量代码外，静态代码检查、还要有频繁且严格的code review，下面列举一些针对Linux kernel特点的测试。</p>
<p>1. 开发者测试。鼓励开发者写单元测试，但很多时候有太多的依赖假设，单测等是很难的，比如要测试U盘在读写过程中被拔掉，再插上的情况，只有实际运行执行才能验证程序是否正确。无论开发人员使用何种方式，都需要保证负责的代码是经过测试了的。不需要在所有版本上运行，但需要确保代码质量，不能假定的字节序、字节长度，都应该使用标准接口。有些时候是修复别人报的bug，而开发人员又没有复现该bug的环境，此时修改也需要由bug提交者确认在其环境测试通过或者在其它能复现该bug的环境验证通过。对修改代码的质量保证方法还包括交由其它人评审通过或测试通过。编译器的报警也是需要修复的。</p>
<p>2. 社区测试。社区开发模式，也强调社区测试，鼓励大家在做好数据备份前提下使用最新发布的版本，类似软件正式发布前的试用版本，以确保在不同的机器不同的平台上能实际正常运行。对此类版本的使用是有风险的，有可能导致系统崩溃。通常在系统安装后，启动时也会比较小心，逐项加载启动，以检查每步都是正常的。还会做一些非常规的操作，也就是异常测试。</p>
<p>3. 配置测试。Linux kernel的配置也较复杂，以支持较好的灵活性和可扩展性，测试需要尽量地覆盖不同的配置组合条件。有的做法是随机配置，再编译运行启动，7*24小时重复不间断地做，以找出可能存在的问题。做的过程中也有优化，比如关闭一些不必要的选项，减少编译的时间。</p>
<p>有些配置项是有助于测试执行时监控问题和分析问题的，比如打印出debug日志，或者在出错时打印尽可能多的信息，kernel在某些设置下自身也会做一些运行过程中的检查，如CONFIG_DETECT_SOFTLOCKUP能检查出内核部分是否在内核模式中循环超过60秒的bug，这类配置通常会在测试时打开。</p>
<p>4. 硬件测试。验证kernel对不同平台的支持时，会使用些不常用的硬件，以及不常用的硬件组合，不同的体系架构。</p>
<p>5. 对待变化。由于Linux kernel经常变化，不可能每次变动都同等对待。针对-rc候选发布版本，需要严格地测试，因为它们一旦被认为是稳定的之后就会正式发布，并很可能由发行商选中到发行版中。而针对-next树或者以前的-mm树，因为它们太容易变化了，没有时间和资源运行全量测试，通常只执行最基本的测试。</p>
<p>6. 工具。Linux测试工具众多，可见http://ltp.sourceforge.net/tooltable.php，覆盖率、安全性、调试、网络、性能等方方面面工具都应有尽有。</p>
<p style="text-align:center">7. 性能测试。Linux kernel对性能要求也比较高，性能测试通常要注意的几点：（1）最好是利用benchmark，以取得可信数据；（2）避免I/O缓存因素，缓存对性能影响较大；（3）确保测量环境的稳定性，特别是比较前后版本时。Autotest中也包含性能测试自动化，一次升级导致的性能问题和修复如下图六所示，2.6.14-rc2-mm1版本与2.6.14-rc1-mm1版本相比执行时间由101增加到了111，性能降低了10%，直到2.6.16-rc1-mm4版本才修复。<br>
<a href="http://blog.jobbole.com/wp-content/uploads/2012/12/kernel-testing-06.jpg" rel="lightbox[31690]" title="Linux kernel测试初探"><img title="Linux kernel测试初探" src="http://blog.jobbole.com/wp-content/uploads/2012/12/kernel-testing-06.jpg" alt="Linux kernel测试初探" width="558" height="355"></a><br>
图六 Autotest性能测试执行结果图</p>
<p>8. 可测性。Linux kernel在可测性方面也有较好支持。</p>
<p>（1）内核模块化。启动内核模块支持后，内核的某些部分就可以在需要的时候才装载到内存中，使内核变得更小，运行更快。模块化的内核部分可以在系统运行期间装载或者卸载。</p>
<p>（2）内核hacking 选项，编译一个测试用的内核，有助有内核特定功能的调试，也需要注意有部分选项会导致性能降低。</p>
<p>（3）Magic SysRq键，神奇的键盘快捷键，可以利用快捷键直接向内核传递特定的指定。</p>
<p>（4）多种信息收集方式，内核的bug不一定是导致系统崩溃，一些严重的错误通过日志等表现出来，信息收集就变得犹为重要了。可以通过Syslog, console, dmesg等方式dump和显示日志，除此之外，还可以通过串口和网络控制台远程收集信息。</p>
<p>（5）kexec快速重启系统，使用 kexec可以直接重新启动到另一个内核，不再必须通过固件和引导装载程序阶段，能为测试节省大量时间。</p>
<p>（6）使用biselect追查问题，当发现一个bug却不知道是由哪个补丁引入时，使用二分查找，代码管理系统git以及补丁管理工具quilt都支持二分查找。</p>
<p>9. 覆盖率。关于Linux kernel的测试覆盖率，没有看到最新的统计，不过以前数据多是在20%左右的分支覆盖率。</p>
<p>虽然说自动化测试越来越重要，但是将内核部分测试自动化却是非常困难的，自动化只涵盖了一少部分功能，性能的自动化看起来做得更多些。质量更多还是由开发人员以及社区测试来保证。另外一方面看，Linux kernel本身的架构应该也有较高的容错性和可扩展性，以支持如此大量又频繁的修改。</p>
<p><strong>说明</strong></p>
<p>写本文时，我还并没有实际参与过Linux kernel的开发或测试，只是对其有兴趣，于是在网上查阅了大量文档，有些信息可能已经过时或者与实际情况不符，仅供参考。</p>
<p>引用说明</p>
<p><strong>网上链接</strong></p>
<p>Crackerjack Project http://ossipedia.ipa.go.jp/crackerjack/index.html</p>
<p>Linux命名争议 http://en.wikipedia.org/wiki/GNU/Linux_naming_controversy</p>
<p>Linux Test Project，测试Linux kernel以及相关功能 http://ltp.sourceforge.net/</p>
<p>Linux测试工具 http://ltp.sourceforge.net/tooltable.php</p>
<p>Linux 维基百科 http://en.wikipedia.org/wiki/Linux</p>
<p>Linux Kernel维基百科 http://en.wikipedia.org/wiki/Linux_kernel</p>
<p>开发流程 http://linuxwireless.org/en/developers/process</p>
<p>Autotest项目http://autotest.github.com/</p>
<p>Greg Kroah Hartman on the Linux Kernel视频http://www.youtube.com/watch?v=L2SED6sewRw</p>
<p>http://stackoverflow.com/questions/3177338/how-is-linux-kernel-tested</p>
<p>kernel官网 http://www.kernel.org</p>
<p>Linux kernel邮件列表FQA http://ftp.osuosl.org/pub/linux/docs/lkml/</p>
<p><strong>Pdf电子书</strong></p>
<p>《Best Practices in Linux Kernel Testing》</p>
<p>《Linux Kernel Tester’s Guide》version 0.3，中文版《Linux 内核测试指南》</p>
<p>《How to Participate in the Linux Community》2008.8</p>
<p>《Linux Kernel Development：How Fast it is Going, Who is Doing It, What They are Doing, and Who is Sponsoring It》2012.3</p>

<h2>相关文章</h2><ul><li><a href="http://blog.jobbole.com/30789/">移动应用实用测试指南</a></li><li><a href="http://blog.jobbole.com/30738/">寻找更好的Javascript单元测试工具</a></li><li><a href="http://blog.jobbole.com/30134/">Testacular：Google开源的JavaScript测试执行过程管理工具</a></li><li><a href="http://blog.jobbole.com/20728/">Bill Liu：探索性测试揭秘</a></li><li><a href="http://blog.jobbole.com/8139/">10本Linux免费电子书</a></li><li><a href="http://blog.jobbole.com/24419/">Linux 内核 3.3 和 3.4 简介</a></li><li><a href="http://blog.jobbole.com/1077/">敏捷软件测试的七个关键成功要素​</a></li><li><a href="http://blog.jobbole.com/17395/">陈皓：我们需要专职的QA吗？</a></li><li><a href="http://blog.jobbole.com/15277/">Google是如何做测试的？（一、二）</a></li><li><a href="http://blog.jobbole.com/22176/">陈皓：性能调优攻略</a></li></ul>