---
layout: post
title:  "使用 Vundle 管理 Vim 插件"
date:   2012-04-14 00:04:55
author: alswl
categories: program
---

## 使用 Vundle 管理 Vim 插件
### by alswl
### at 2012-04-14 00:04:55
### original <http://www.udpwork.com/item/7118.html>

<div></div>
<p>早在这个月初，我就在 Vim 的邮件列表看到了一封关于 Vim 插件管理的<a href="https://groups.google.com/d/topic/vim-cn/mPES0sNT87Q/discussion">邮件</a>。 才惊呼原来有这么强大的插件可以用来管理我那一坨插件， 今天细细阅读并查了一些资料，便开始配置自己的 Vim。</p>
<h2>几种管理插件</h2>
<p>Vim 的插件管理工具有蛮多，比如：</p>
<ul><li><a href="https://github.com/gmarik/vundle">Vundle</a></li>
<li><a href="http://www.vim.org/scripts/script.php?script_id=2905">vim-addon-manager</a></li>
<li><a href="http://www.vim.org/scripts/script.php?script_id=2332">vpathogen.vim</a></li>
<li><a href="http://www.vim.org/scripts/script.php?script_id=3458">vvundle</a></li>
<li><a href="https://github.com/c9s/Vimana">vvimana</a></li>
<li><a href="http://www.douban.com/note/173144456/">一位同学自己写的</a></li>
</ul>
<p>Vim-addon-manager 和 vimana 的对比，参见<a href="http://yixf.name/2011/10/26/vim%E7%9A%84%E6%8F%92%E4%BB%B6%E7%AE%A1%E7%90%86%E5%B7%A5%E5%85%B7/">Vim的插件管理工具</a></p>
<p>我最会选择了 Vundle，通过子目录管理插件，支持 Git 更新。 我其实不太喜欢子目录，觉得目录太多了看着烦，但是考虑到在没有良好的 PKG 包描述文件的前提下，分子目录是一种简单有效（粗暴）的方法。</p>
<p>Vundle 通过 git 来对插件进行更新，有三种源可以添加：</p>
<ul><li>github 中 vim-scripts 的项目（这个账号是为 Pathogen 建的，用来建立对 Vim.org 上脚本的镜像）</li>
<li>github 某个 Vim 插件项目</li>
<li>某个 git 源</li>
</ul>
<p>以上也是我推荐的选择插件顺序，我认为没有必要去取最新的开发版插件。</p>
<h2>Vundle 安装</h2>
<p>无二话，官方文档的<a href="https://github.com/gmarik/vundle">Quick Start</a>写的很详细 ，一句话：</p>
<pre>$ git clone https://github.com/gmarik/vundle.git ~/.vim/bundle/vundle
</pre><p>然后开始配置 .vimrc，我的<a href="https://github.com/alswl/vimrc/blob/master/.vimrc">.vimrc</a></p>
<p>重点是配置各种Bundle我的配置如下</p>
<pre>&quot; My Bundles here:

&quot; vim-scripts repos

&quot; Syntax
Bundle &#39;asciidoc.vim&#39;
Bundle &#39;confluencewiki.vim&#39;
Bundle &#39;html5.vim&#39;
Bundle &#39;JavaScript-syntax&#39;
&quot;Bundle &#39;mako.vim&#39;
Bundle &#39;moin.vim&#39;
Bundle &#39;python.vim--Vasiliev&#39;
Bundle &#39;xml.vim&#39;

&quot; Color

Bundle &#39;desert256.vim&#39;
Bundle &#39;Impact&#39;
Bundle &#39;matrix.vim&#39;
Bundle &#39;vibrantink&#39;
Bundle &#39;vividchalk.vim&#39;

&quot; Ftplugin
Bundle &#39;python_fold&#39;

&quot; Indent
&quot;Bundle &#39;indent/html.vim&#39;
Bundle &#39;IndentAnything&#39;
Bundle &#39;Javascript-Indentation&#39;
Bundle &#39;mako.vim--Torborg&#39;
Bundle &#39;gg/python.vim&#39;

&quot; Plugin
Bundle &#39;The-NERD-tree&#39;
Bundle &#39;AutoClose--Alves&#39;
Bundle &#39;auto_mkdir&#39;
Bundle &#39;cecutil&#39;
Bundle &#39;fcitx.vim&#39;
Bundle &#39;FencView.vim&#39;
&quot;Bundle &#39;FuzzyFinder&#39;
Bundle &#39;jsbeautify&#39;
Bundle &#39;L9&#39;
Bundle &#39;Mark&#39;
Bundle &#39;matrix.vim&#39;
Bundle &#39;mru.vim&#39;
Bundle &#39;The-NERD-Commenter&#39;
&quot;Bundle &#39;project.vim&#39;
Bundle &#39;restart.vim&#39;
Bundle &#39;taglist.vim&#39;
&quot;Bundle &#39;templates.vim&#39;
&quot;Bundle &#39;vimim.vim&#39;
Bundle &#39;ZenCoding.vim&#39;
Bundle &#39;css_color.vim&#39;
Bundle &#39;hallettj/jslint.vim&#39;
</pre><p>需要注意的是，Vim 插件名称可能存在重名的情况，这时候就需要在插件后面加上作者的姓氏， 比如Bundle 'Javascript-Indentation'，而遇到插件名有空格和斜杠的话， 需要将空格和斜杠替换为-。</p>
<p>执行BundleInstall即可完成安装，如果出现错误提示，多半是名称有问题， 去 github 和 vim.org 搜索一下吧。</p>
<p>更多参考：</p>
<ul><li><a href="http://blog.houqp.me/use-vundle-to-manage-your-plugins/">Vim插件管理利器——Vundle</a></li>
<li><a href="http://www.cnblogs.com/qiangji/archive/2011/07/31/Vundle.html">使用Vundle 来管理 Vim 插件</a></li>
</ul>
<p>ps：这篇文章第一次尝试使用 Markdown 写博，以前全部是在用 Asciidoc 写（我的 Github 里的README，都喜欢用.asciidoc）。貌似是由于 github 带动， Mardown 自 2010 年之后使用人数猛涨，证据在此，上图是 Markdown，下图是 Asciidoc</p>
<p><img src="http://log4d.com//wp-content/uploads/2012/04/markdown-asciidoc.png" alt="Markdown vs Asciidoc"></p>
<div><p><strong>版权所有 © 2010 转载本站文章请注明：</strong>
转载自<a href="http://log4d.com/">Log4D</a>
<br>
<strong>原文链接:</strong>
<a href="http://log4d.com/2012/04/vundle">http://log4d.com/2012/04/vundle</a>
<br>
您可以随意地转载本站的文章，但是必须在醒目位置注明来源及本站链接，不可以将本站文章商业化使用，或者修改、转换或者以本作品为基础进行创作。
<br>
3a1ff193cee606bd1e2ea554a16353ee</p>
</div>
<div><div><a></a><a></a><a></a><a></a><a></a><a></a><a></a><a href="http://www.jiathis.com/share/"></a><a></a></div>
|2|left|yes</div>
<div></div>
<div>相关文章</div>
<ul><li><a href="http://log4d.com/2011/07/vimperator-pass-through" title="Vimperator的Pass through">Vimperator的Pass through</a></li>
<li><a href="http://log4d.com/2011/06/mako-vim" title="一个mako.vim">一个mako.vim</a></li>
<li><a href="http://log4d.com/2011/01/regex-group-catch" title="正则表达式抓捕替换">正则表达式抓捕替换</a></li>
<li><a href="http://log4d.com/2010/09/vs-vim" title="将VisualStudio打造成Vim">将VisualStudio打造成Vim</a></li>
<li><a href="http://log4d.com/2010/03/vim-indentation-and-space-config" title="Vim中缩进和空格的使用">Vim中缩进和空格的使用</a></li>
</ul>
<img src="http://feeds.feedburner.com/~r/dddspace/~4/ifIoHp-TBXo">
			<div style="margin-top:8px;padding:6px 0;border-top:1px solid #3cf">
				<div style="text-align:center;margin:16px 0;padding:6px;border:0px dashed #999;font-family:arial;font-size:26px;font-weight:bold">
	<a href="http://www.udpwork.com/item/7118.html#review_form" title="不喜欢" style="text-decoration:none">
		<img src="http://www.udpwork.com//images/thumb_down24.gif" alt="">
		<span style="color:#f33">0</span>
	</a>
	   
	<a href="http://www.udpwork.com/item/7118.html#review_form" title="喜欢" style="text-decoration:none">
		<img src="http://www.udpwork.com//images/thumb_up24.gif" alt="">
		<span style="color:#3c3">0</span>
	</a>
</div>				<p>
					由 <a href="http://www.udpwork.com/">IT 牛人博客聚合网站(udpwork.com)</a> 聚合
					|
					<a href="http://www.udpwork.com/item/7118.html#reviews">评论: 0</a>
					|
					<a href="http://book.benegg.com/tag/%E7%BC%96%E7%A8%8B?from=udpwork-feed">10000+ 本编程/Linux PDF/CHM 电子书下载</a>
				</p>
			</div>