---
layout: post
title:  "Windows Phone7中的IronRuby"
date:   2010-11-19 23:08:00
author: 马宁
categories: program
---

## Windows Phone7中的IronRuby
### by 马宁
### at 2010-11-19 23:08:00
### original <http://www.cnblogs.com/aawolf/archive/2010/11/19/1882247.html>

<p><p>作者：马宁</p>  <p>示例代码下载地址：</p>  <p><a title="WP7_aawolf_IronRubyWP7.rar" href="http://files.cnblogs.com/aawolf/WP7_aawolf_IronRubyWP7.rar">WP7_aawolf_IronRubyWP7.rar</a></p>  <p>写这篇BLOG完全是因为看了MSDN上的这篇文章：</p>  <p><a href="http://msdn.microsoft.com/en-us/magazine/ff960707.aspx">http://msdn.microsoft.com/en-us/magazine/ff960707.aspx</a></p>  <p>Windows Phone 7的开发工具不支持动态语言，所以IronRuby支持Windows Phone 7就显得格外重要了。刚看这篇文章的时候，还闹了个笑话，看了一遍代码，一句都不认识，心想难道语言换到.NET上，变化怎么这么大？仔细一看，原来是Ruby，而不是Python ^_^，小蟒蛇这次落后了。以前用Python写过自动化测试脚本，没接触过Ruby，所以，把Ruby看成Python了。</p>  <p>不支持动态语言，一直是Windows Mobile编程的痛，这次终于有搞头了。终于可以动态改变程序的逻辑了，光这一点就给我们提供了无限的想象空间。Windows Phone上的F#也快了吧？^_^</p>  <p>言归正传，这次我完全是照葫芦画瓢，只是将自己尝试中的一些关键点写出来，让大家少走弯路。更多信息大家可以参考Shay Friedman的BLOG：<a href="http://ironshay.com/">http://ironshay.com/</a></p>  <p>首先，我们要下载IronRuby for Windows Phone版本（.NET 3.5）：</p>  <p><a href="http://ironruby.codeplex.com/releases/view/43540#DownloadId=133276">http://ironruby.codeplex.com/releases/view/43540#DownloadId=133276</a></p>  <p>然后，在Visual Studio 2010中创建一个Silverlight for Windows Phone 7的工程，工程名叫做“IronRubyWP7”，然后选择“Project”菜单的“Add Reference”选项，在弹出的对话框中，选择“Browse”标签，我们可以找到解压后的IronRuby目录，将\silverlight\bin中的DLL文件加入到工程中来：</p>  <p><a href="http://images.cnblogs.com/cnblogs_com/aawolf/WindowsLiveWriter/WindowsPhone7IronRuby_14560/IronRuby1_2.jpg"><img style="border-bottom:0px;border-left:0px;display:inline;border-top:0px;border-right:0px" title="IronRuby1" border="0" alt="IronRuby1" src="http://images.cnblogs.com/cnblogs_com/aawolf/WindowsLiveWriter/WindowsPhone7IronRuby_14560/IronRuby1_thumb.jpg" width="526" height="417"></a>     <br>在忽略一些警告提示之后，程序集将被加入到工程中。我们可以在Solution Explorer中看到刚被加入的程序集：</p>  <p><a href="http://images.cnblogs.com/cnblogs_com/aawolf/WindowsLiveWriter/WindowsPhone7IronRuby_14560/IronRuby2_2.jpg"><img style="border-bottom:0px;border-left:0px;display:inline;border-top:0px;border-right:0px" title="IronRuby2" border="0" alt="IronRuby2" src="http://images.cnblogs.com/cnblogs_com/aawolf/WindowsLiveWriter/WindowsPhone7IronRuby_14560/IronRuby2_thumb.jpg" width="323" height="460"></a>     <br>接下来，我们在工程中添加一个文本文件，在Solution Explorer中选中IronRubyWP7，右键，“Add”-“New Item”，在对话框中选择“Text File”，将文件名改为“MainPage.rb”。</p>  <p>选中MainPage.rb文件，在属性中将“Build Action”设置为“Embedded Resource”。</p>  <p><a href="http://images.cnblogs.com/cnblogs_com/aawolf/WindowsLiveWriter/WindowsPhone7IronRuby_14560/IronRuby3_2.jpg"><img style="border-bottom:0px;border-left:0px;display:inline;border-top:0px;border-right:0px" title="IronRuby3" border="0" alt="IronRuby3" src="http://images.cnblogs.com/cnblogs_com/aawolf/WindowsLiveWriter/WindowsPhone7IronRuby_14560/IronRuby3_thumb.jpg" width="492" height="366"></a> </p>  <p>我们打开MainPage.rb文件，输入下面的Ruby代码：</p>  <p> </p> <font color="#0080ff"></font>  <pre><span># Include namespaces for ease of use</span>

include System::Windows::Media

include System::Windows::Controls

<span># Set the titles</span>

Phone.find_name(<span>&quot;ApplicationTitle&quot;</span>).text = <span>&quot;aawolf.cnblogs.com&quot;</span>

Phone.find_name(<span>&quot;PageTitle&quot;</span>).text = <span>&quot;IronRuby&amp; WP7&quot;</span>

<span># Create a new text block</span>

textBlock = TextBlock.new

textBlock.text = <span>&quot;IronRuby is running on Windows Phone 7!&quot;</span>

textBlock.foreground = SolidColorBrush.new(Colors.Green)

textBlock.font_size = 48

textBlock.text_wrapping = System::Windows::TextWrapping.Wrap

<span># Add the text block to the page</span>

Phone.find_name(<span>&quot;ContentPanel&quot;</span>).children.add(textBlock)</pre>


<p>请注意，我修改了最后一行的容器控件名称，原示例中的名称是“ContentGrid”，但是Silverlight for Windows Phone向导默认创建的XAML文件中容器类名称是“ContentPanel”。这里会引起一个运行期错误，因为IronRuby不能Debug，所以第一次调试起来比较痛苦。</p>

<p>接下来，我们要打开MainPage.xaml.cs文件，将IronRuby初始化代码，加入到MainPage类的构造函数中：</p>

<pre>    <span>public</span> <span>partial</span> <span>class</span> MainPage : PhoneApplicationPage
    {
        <span>// Constructor</span>
        <span>public</span> MainPage()
        {
            InitializeComponent();

            <span>// Allow both portrait and landscape orientations</span>
            SupportedOrientations = SupportedPageOrientation.PortraitOrLandscape;

            <span>// Create an IronRuby engine and prevent compilation</span>
            ScriptEngine engine = Ruby.CreateEngine();

            <span>// Load the System.Windows.Media assembly to the IronRuby context</span>
            engine.Runtime.LoadAssembly(<span>typeof</span>(Color).Assembly);

            <span>// Add a global constant named Phone, which will allow access to this class</span>
            engine.Runtime.Globals.SetVariable(<span>&quot;Phone&quot;</span>, <span>this</span>);

            <span>// Read the IronRuby code</span>
            Assembly execAssembly = Assembly.GetExecutingAssembly();
            Stream codeFile =
              execAssembly.GetManifestResourceStream(<span>&quot;IronRubyWP7.MainPage.rb&quot;</span>);
            <span>string</span> code = <span>new</span> StreamReader(codeFile).ReadToEnd();

            <span>// Execute the IronRuby code</span>
            engine.Execute(code);
        }
    }</pre>


<p>请大家注意InitializeComponent方法的位置，在初始化IronRuby运行时之前，一定要先完成控件初始化的工作，这是我血和泪的教训。另外一个需要注意的地方，就是读取Ruby文件的路径。我们似乎也可以通过HttpRequest获取一个Stream是吧？笑而不语 ^_^</p>

<p>最后运行的效果是这样子的，整个开发过程历时两小时：</p>

<p><a href="http://images.cnblogs.com/cnblogs_com/aawolf/WindowsLiveWriter/WindowsPhone7IronRuby_14560/clip_image008_2.jpg"><img style="border-bottom:0px;border-left:0px;display:inline;border-top:0px;border-right:0px" title="clip_image008" border="0" alt="clip_image008" src="http://images.cnblogs.com/cnblogs_com/aawolf/WindowsLiveWriter/WindowsPhone7IronRuby_14560/clip_image008_thumb.jpg" width="568" height="307"></a></p>

<h4>相关资源</h4>

<p>马宁的Windows Phone 7开发教程（1）——<a href="http://www.cnblogs.com/aawolf/archive/2010/08/28/1811438.html">Windows Phone开发工具初体验</a></p>

<p>马宁的Windows Phone 7开发教程（2）——<a href="http://www.cnblogs.com/aawolf/archive/2010/08/29/1811686.html">Windows Phone XNA 4.0 3D游戏开发</a></p>

<p>马宁的Windows Phone 7开发教程（3）——<a href="http://www.cnblogs.com/aawolf/archive/2010/09/20/1831848.html">XNA下使用MessageBox和软键盘</a></p>

<p>马宁的Windows Phone 7开发教程（4）——<a href="http://www.cnblogs.com/aawolf/archive/2010/09/22/1833167.html#19458048">XNA显示中文字体</a></p> <img src="http://www.cnblogs.com/aawolf/aggbug/1882247.html?type=1" width="1" height="1" alt=""><p>作者: <a href="http://www.cnblogs.com/aawolf/">马宁</a> 发表于 2010-11-19 23:08 <a href="http://www.cnblogs.com/aawolf/archive/2010/11/19/1882247.html">原文链接</a></p><p>评论: 4　<a href="http://www.cnblogs.com/aawolf/archive/2010/11/19/1882247.html#pagedcomment">查看评论</a>　<a href="http://www.cnblogs.com/aawolf/archive/2010/11/19/1882247.html#commentform">发表评论</a></p><hr><p>最新新闻：<br>· <a href="http://news.cnblogs.com/n/81770/">京东曝光发票证据回应艳照门 称进货渠道正规</a><span style="color:gray">(2010-11-20 20:35)</span><br>· <a href="http://news.cnblogs.com/n/81769/">Google Protocol Buffer原理</a><span style="color:gray">(2010-11-20 20:27)</span><br>· <a href="http://news.cnblogs.com/n/81768/">张朝阳：微博大战已全面爆发</a><span style="color:gray">(2010-11-20 17:59)</span><br>· <a href="http://news.cnblogs.com/n/81767/">SAP与RIM联合开发移动企业管理软件</a><span style="color:gray">(2010-11-20 17:56)</span><br>· <a href="http://news.cnblogs.com/n/81764/">Top500超级计算机排名遭嘲讽</a><span style="color:gray">(2010-11-20 17:12)</span><br></p><p>编辑推荐：<a href="http://www.cnblogs.com/guaiguai/archive/2010/11/20/1882345.html">Android整体印象</a><br></p><p>网站导航：<a href="http://www.cnblogs.com">博客园首页</a>  <a href="http://home.cnblogs.com/">我的园子</a>  <a href="http://news.cnblogs.com">新闻</a>  <a href="http://home.cnblogs.com/ing/">闪存</a>  <a href="http://home.cnblogs.com/group/">小组</a>  <a href="http://space.cnblogs.com/q/">博问</a>  <a href="http://kb.cnblogs.com">知识库</a></p></p>