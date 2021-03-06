---
layout: post
title:  "使用CocoaPods来做iOS程序的包依赖管理"
date:   2012-12-02 14:09:00
author: 
categories: program
---

## 使用CocoaPods来做iOS程序的包依赖管理
### by 
### at 2012-12-02 14:09:00
### original <http://blog.devtang.com/blog/2012/12/02/use-cocoapod-to-manage-ios-lib-dependency/>

<p><img src="http://blog.devtang.com/images/cocoapods-logo.png"></p>

<h2>前言</h2>

<p>每种语言发展到一个阶段，就会出现相应的依赖管理工具, 或者是中央代码仓库。比如</p>

<ul>
<li>Java: maven，Ivy</li>
<li>Ruby: gems</li>
<li>Python: pip, easy_install</li>
<li>Nodejs: npm</li>
</ul>


<p>随着iOS开发者的增多，业界也出现了为iOS程序提供依赖管理的工具，这个工具叫：<a href="http://cocoapods.org/">CocoaPods</a>。</p>




<h2>CocoaPods简介</h2>

<p>CocoaPods是一个负责管理iOS项目中第三方开源代码的工具。CocoaPods<a href="https://github.com/CocoaPods/CocoaPods">项目的源码</a>在Github上管理。该项目开始于2011年8月12日，经过一年多的发展，现在已经超过1000次提交，并且持续保持活跃更新。开发iOS项目不可避免地要使用第三方开源库，CocoaPods的出现使得我们可以节省设置和更新第三方开源库的时间。</p>

<p>拿我之前开发的粉笔网iPhone客户端为例，其使用了14个第三方开源库。在没有使用CocoaPods以前，我需要：</p>

<ol>
<li>把这些第三方开源库的相关文件复制到项目中，或者设置成git的submodule，然后这些开源库通常需要依赖系统的一些framework，我需要手工地将这些framework一一增加到项目依赖中，比如ASI网络库就需要增加以下framework: CFNetwork, SystemConfiguration, MobileCoreServices, CoreGraphics and zlib。</li>
<li>对于RegexKitLite这个正则表达式库，我还需要设置-licucore的编译参数</li>
<li>手工管理这些依赖包的更新。</li>
</ol>


<p>这些体力活虽然简单，但毫无技术含量并且浪费时间。在使用CocoaPods之后，我只需要将用到的第三方开源库放到一个名为Podfile的文件中，然后执行pod install。CocoaPods就会自动将这些第三方开源库的源码下载下来，并且为我的工程设置好相应的系统依赖和编译参数。</p>

<h2>CocoaPods的安装和使用介绍</h2>

<h3>安装</h3>

<p>安装方式异常简单, Mac下都自带ruby，使用ruby的gem命令即可下载安装：</p>

<span></span><div><table><tr><td><pre><span>1</span>
<span>2</span>
</pre></td><td><pre><code><span><span>$ </span>gem install cocoapods
</span><span><span>$ </span>pod setup
</span></code></pre></td></tr></table></div>


<h3>使用</h3>

<p>使用时需要新建一个名为Podfile的文件，以如下格式，将依赖的库名字依次列在文件中即可</p>

<span></span><div><table><tr><td><pre><span>1</span>
<span>2</span>
<span>3</span>
<span>4</span>
<span>5</span>
</pre></td><td><pre><code><span>platform :ios
</span><span>pod <span>&#39;JSONKit&#39;</span>,       <span>&#39;~&gt; 1.4&#39;</span>
</span><span>pod <span>&#39;Reachability&#39;</span>,  <span>&#39;~&gt; 3.0.0&#39;</span>
</span><span>pod <span>&#39;ASIHTTPRequest&#39;</span>
</span><span>pod <span>&#39;RegexKitLite&#39;</span>
</span></code></pre></td></tr></table></div>


<p>然后你将编辑好的Podfile文件放到你的项目根目录中，执行如下命令即可：</p>

<span></span><div><table><tr><td><pre><span>1</span>
<span>2</span>
</pre></td><td><pre><code><span><span>cd</span> <span>&quot;your project home&quot;</span>
</span><span>pod install
</span></code></pre></td></tr></table></div>


<p>现在，你的所有第三方库都已经下载完成并且设置好了编译参数和依赖，你只需要记住如下2点即可：</p>

<ol>
<li>使用CocoaPods生成的 <em>.xcworkspace 文件来打开工程，而不是以前的 </em>.xcodeproj 文件。</li>
<li>每次更改了Podfile文件，你需要重新执行一次pod install命令。</li>
</ol>


<h3>查找第三方库</h3>

<p>你如果不知道cocoaPods管理的库中，是否有你想要的库，那么你可以通过pod search命令进行查找，以下是我用pod search json查找到的所有可用的库：</p>

<span></span><div><table><tr><td><pre><span>1</span>
<span>2</span>
<span>3</span>
<span>4</span>
<span>5</span>
<span>6</span>
<span>7</span>
<span>8</span>
<span>9</span>
<span>10</span>
<span>11</span>
<span>12</span>
<span>13</span>
<span>14</span>
<span>15</span>
<span>16</span>
<span>17</span>
<span>18</span>
<span>19</span>
<span>20</span>
<span>21</span>
<span>22</span>
<span>23</span>
<span>24</span>
<span>25</span>
<span>26</span>
<span>27</span>
<span>28</span>
<span>29</span>
<span>30</span>
<span>31</span>
<span>32</span>
<span>33</span>
<span>34</span>
<span>35</span>
<span>36</span>
<span>37</span>
<span>38</span>
<span>39</span>
<span>40</span>
<span>41</span>
<span>42</span>
<span>43</span>
<span>44</span>
</pre></td><td><pre><code><span><span>$ </span>pod search json
</span><span>
</span><span>
</span><span>-&gt; AnyJSON <span>(</span>0.0.1<span>)</span>
</span><span>   Encode / Decode JSON by any means possible.
</span><span>   - Homepage: https://github.com/mattt/AnyJSON
</span><span>   - Source:   https://github.com/mattt/AnyJSON.git
</span><span>   - Versions: 0.0.1 <span>[</span>master repo<span>]</span>
</span><span>
</span><span>
</span><span>-&gt; JSONKit <span>(</span>1.5pre<span>)</span>
</span><span>   A Very High Performance Objective-C JSON Library.
</span><span>   - Homepage: https://github.com/johnezang/JSONKit
</span><span>   - Source:   git://github.com/johnezang/JSONKit.git
</span><span>   - Versions: 1.5pre, 1.4 <span>[</span>master repo<span>]</span>
</span><span>
</span><span>
</span><span>-&gt; MTJSONDictionary <span>(</span>0.0.4<span>)</span>
</span><span>   An NSDictionary category <span>for </span>when you<span>&#39;</span>re working with it converting to/from JSON. DEPRECATED, use MTJSONUtils
</span><span>   instead.
</span><span>   - Homepage: https://github.com/mysterioustrousers/MTJSONDictionary.git
</span><span>   - Source:   https://github.com/mysterioustrousers/MTJSONDictionary.git
</span><span>   - Versions: 0.0.4, 0.0.3, 0.0.2 <span>[</span>master repo<span>]</span>
</span><span>
</span><span>
</span><span>-&gt; MTJSONUtils <span>(</span>0.1.0<span>)</span>
</span><span>   An NSObject category <span>for </span>working with JSON.
</span><span>   - Homepage: https://github.com/mysterioustrousers/MTJSONUtils.git
</span><span>   - Source:   https://github.com/mysterioustrousers/MTJSONUtils.git
</span><span>   - Versions: 0.1.0, 0.0.1 <span>[</span>master repo<span>]</span>
</span><span>
</span><span>
</span><span>-&gt; SBJson <span>(</span>3.1.1<span>)</span>
</span><span>   This library implements strict JSON parsing and generation in Objective-C.
</span><span>   - Homepage: http://stig.github.com/json-framework/
</span><span>   - Source:   https://github.com/stig/json-framework.git
</span><span>   - Versions: 3.1.1, 3.1, 3.0.4, 2.2.3 <span>[</span>master repo<span>]</span>
</span><span>
</span><span>
</span><span>-&gt; TouchJSON <span>(</span>1.0<span>)</span>
</span><span>   TouchJSON is an Objective-C based parser and generator <span>for </span>JSON encoded data.
</span><span>   - Homepage: https://github.com/TouchCode/TouchJSON
</span><span>   - Source:   https://github.com/TouchCode/TouchJSON.git
</span><span>   - Versions: 1.0 <span>[</span>master repo<span>]</span>
</span></code></pre></td></tr></table></div>


<h3>生成第三方库的帮助文档</h3>

<p>如果你想让CococaPods帮你生成第三方库的帮助文档，并集成到XCode中，那么用brew安装appledoc即可：</p>

<span></span><div><table><tr><td><pre><span>1</span>
</pre></td><td><pre><code><span>brew install appledoc
</span></code></pre></td></tr></table></div>


<p>关于appledoc，我在今年初的另一篇博客<a href="http://blog.devtang.com/blog/2012/02/01/use-appledoc-to-generate-xcode-doc/">《使用Objective-C的文档生成工具:appledoc》</a>中有专门介绍。它最大的优点是可以将帮助文档集成到XCode中，这样你在敲代码的时候，按住opt键单击类名或方法名，就可以显示出相应的帮助文档。</p>

<h2>原理</h2>

<p>大概研究了一下CocoaPods的原理，它是将所有的依赖库都放到另一个名为Pods项目中，然后让主项目依赖Pods项目，这样，源码管理工作都从主项目移到了Pods项目中。发现的一些技术细节有：</p>

<ol>
<li>Pods项目最终会编译成一个名为libPods.a的文件，主项目只需要依赖这个.a文件即可。</li>
<li>对于资源文件，CocoaPods提供了一个名为Pods-resources.sh的bash脚本，该脚本在每次项目编译的时候都会执行，将第三方库的各种资源文件复制到目标目录中。</li>
<li>CocoaPods通过一个名为Pods.xcconfig的文件来在编译时设置所有的依赖和参数。</li>
</ol>


<p>Have fun!</p>