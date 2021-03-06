---
layout: post
title:  "WPF 扩展玻璃效果（Aero Glass）"
date:   2010-10-04 21:13:00
author: Gnie
categories: program
---

## WPF 扩展玻璃效果（Aero Glass）
### by Gnie
### at 2010-10-04 21:13:00
### original <http://www.cnblogs.com/gnielee/archive/2010/10/04/wpf-extend-aero-glass.html>

<p><a href="http://www.cnblogs.com/gnielee/"><img src="http://pic.cnblogs.com/face/u40474.jpg" alt="" border="0"></a><br>作者: <a href="http://www.cnblogs.com/gnielee/">Gnie</a> 发表于 2010-10-04 21:13 <a href="http://www.cnblogs.com/gnielee/archive/2010/10/04/wpf-extend-aero-glass.html">原文链接</a> 阅读: 1505 评论: 18</p><p>     Windows 7 操作系统默认具有一款玻璃效果主题（Aero Glass）。如果选择了该款主题，所有的应用程序标题栏都会处于玻璃透明效果（如下图）。这个功能是由Desktop Window Manager（DWM）服务支持的。</p>  <p><img style="background-image:none;border-right-width:0px;padding-left:0px;padding-right:0px;display:inline;border-top-width:0px;border-bottom-width:0px;border-left-width:0px;padding-top:0px" title="GlassExample" border="0" alt="GlassExample" src="http://images.cnblogs.com/cnblogs_com/gnielee/Windows-Live-Writer/WPF_11044/GlassExample.png" width="501" height="134"></p>  <p>     默认情况下，我们编写的应用程序在Windows 7 中也只有标题栏和窗口框架会具备玻璃效果，其他区域仍是不透明状态（如下图）。如果想将程序整体都改为上图IE 窗口的效果，可以使用DWM API 将玻璃区域进行扩展。</p>  <p><img style="background-image:none;border-right-width:0px;padding-left:0px;padding-right:0px;display:inline;border-top-width:0px;border-bottom-width:0px;border-left-width:0px;padding-top:0px" title="Notepad" border="0" alt="Notepad" src="http://images.cnblogs.com/cnblogs_com/gnielee/Windows-Live-Writer/WPF_11044/Notepad_1.png" width="254" height="126"></p>  <p>首先，从dwmapi.dll 中调取DwmExtendFrameIntoClientArea 方法。</p>  <pre>[<span style="color:#2b91af">StructLayout</span>(<span style="color:#2b91af">LayoutKind</span>.Sequential)]
<span style="color:blue">public struct </span><span style="color:#2b91af">MARGINS
</span>{
    <span style="color:blue">public int </span>cxLeftWidth;      
    <span style="color:blue">public int </span>cxRightWidth;     
    <span style="color:blue">public int </span>cyTopHeight;      
    <span style="color:blue">public int </span>cyBottomHeight;   
};

[<span style="color:#2b91af">DllImport</span>(<span style="color:#a31515">&quot;DwmApi.dll&quot;</span>)]
<span style="color:blue">public static extern int </span>DwmExtendFrameIntoClientArea(
    <span style="color:#2b91af">IntPtr </span>hwnd,
    <span style="color:blue">ref </span><span style="color:#2b91af">MARGINS </span>pMarInset);</pre>

<p>创建方法ExtendAeroGlass 方法，可将WPF Window窗口的Aero Glass 区域扩展。</p>

<pre><span style="color:blue">private void </span>ExtendAeroGlass(<span style="color:#2b91af">Window </span>window)
{
    <span style="color:blue">try
    </span>{
        <span style="color:green">// 为WPF程序获取窗口句柄
         </span><span style="color:#2b91af">IntPtr </span>mainWindowPtr = <span style="color:blue">new </span><span style="color:#2b91af">WindowInteropHelper</span>(window).Handle;
        <span style="color:#2b91af">HwndSource </span>mainWindowSrc = <span style="color:#2b91af">HwndSource</span>.FromHwnd(mainWindowPtr);
        mainWindowSrc.CompositionTarget.BackgroundColor = <span style="color:#2b91af">Colors</span>.Transparent;

        <span style="color:green">// 设置Margins
        </span><span style="color:#2b91af">MARGINS </span>margins = <span style="color:blue">new </span><span style="color:#2b91af">MARGINS</span>();

        <span style="color:green">// 扩展Aero Glass
        </span>margins.cxLeftWidth = -1;
        margins.cxRightWidth = -1;
        margins.cyTopHeight = -1;
        margins.cyBottomHeight = -1;

        <span style="color:blue">int </span>hr = DwmExtendFrameIntoClientArea(mainWindowSrc.Handle, <span style="color:blue">ref </span>margins);
        <span style="color:blue">if </span>(hr &lt; 0)
        {
            <span style="color:#2b91af">MessageBox</span>.Show(<span style="color:#a31515">&quot;DwmExtendFrameIntoClientArea Failed&quot;</span>);
        }
    }
    <span style="color:blue">catch </span>(<span style="color:#2b91af">DllNotFoundException</span>)
    {
        <span style="color:#2b91af">Application</span>.Current.MainWindow.Background = <span style="color:#2b91af">Brushes</span>.White;
    }
}</pre>

<p>简单制作一个WPF 界面。</p>

<pre><span style="color:blue">&lt;</span><span style="color:#a31515">Window </span><span style="color:red">x</span><span style="color:blue">:</span><span style="color:red">Class</span><span style="color:blue">=&quot;WpfAeroGlass.MainWindow&quot;
        </span><span style="color:red">xmlns</span><span style="color:blue">=&quot;http://schemas.microsoft.com/winfx/2006/xaml/presentation&quot;
        </span><span style="color:red">xmlns</span><span style="color:blue">:</span><span style="color:red">x</span><span style="color:blue">=&quot;http://schemas.microsoft.com/winfx/2006/xaml&quot;
        </span><span style="color:red">Title</span><span style="color:blue">=&quot;MainWindow&quot; </span><span style="color:red">Height</span><span style="color:blue">=&quot;350&quot; </span><span style="color:red">Width</span><span style="color:blue">=&quot;525&quot;&gt;
    &lt;</span><span style="color:#a31515">Grid </span><span style="color:red">x</span><span style="color:blue">:</span><span style="color:red">Name</span><span style="color:blue">=&quot;layout&quot;&gt;
        &lt;</span><span style="color:#a31515">Button </span><span style="color:red">x</span><span style="color:blue">:</span><span style="color:red">Name</span><span style="color:blue">=&quot;btn&quot; </span><span style="color:red">Content</span><span style="color:blue">=&quot;Button&quot; </span><span style="color:red">Margin</span><span style="color:blue">=&quot;191,66,202,211&quot; /&gt;
        &lt;</span><span style="color:#a31515">CheckBox </span><span style="color:red">x</span><span style="color:blue">:</span><span style="color:red">Name</span><span style="color:blue">=&quot;checkBox&quot; </span><span style="color:red">Content</span><span style="color:blue">=&quot;Extend AeroGlass&quot;
                  </span><span style="color:red">Click</span><span style="color:blue">=&quot;CheckBox_Checked&quot; </span><span style="color:red">Height</span><span style="color:blue">=&quot;24&quot; </span><span style="color:red">Width</span><span style="color:blue">=&quot;121&quot; /&gt;
    &lt;/</span><span style="color:#a31515">Grid</span><span style="color:blue">&gt;
&lt;/</span><span style="color:#a31515">Window</span><span style="color:blue">&gt;
</span></pre>

<p>补充CheckBox 点击事件，在其中启用ExtendAeroGlass 方法。</p>

<pre><span style="color:blue">private void </span>CheckBox_Checked(<span style="color:blue">object </span>sender, <span style="color:#2b91af">RoutedEventArgs </span>e)
{
    <span style="color:blue">if <font color="#000000">(</font></span>checkBox.IsChecked.Value)
    {
        <span style="color:blue">this</span>.Background = <span style="color:#2b91af">Brushes</span>.Transparent;
        ExtendAeroGlass(<span style="color:blue">this</span>);
    }
    <span style="color:blue">else
    </span>{
        <span style="color:blue">this</span>.Background = <span style="color:#2b91af">Brushes</span>.White;
    }
}</pre>

<h1>演示效果</h1>

<p>运行程序后，默认界面状态。</p>

<p><img style="background-image:none;border-right-width:0px;padding-left:0px;padding-right:0px;display:inline;border-top-width:0px;border-bottom-width:0px;border-left-width:0px;padding-top:0px" title="Default" border="0" alt="Default" src="http://images.cnblogs.com/cnblogs_com/gnielee/Windows-Live-Writer/WPF_11044/Default.png" width="529" height="354"></p>

<p>点击&quot;Extend AeroGlass&quot; 选框，界面中&lt;Grid&gt; 也将呈现玻璃效果。</p>

<p><img style="background-image:none;border-right-width:0px;padding-left:0px;padding-right:0px;display:inline;border-top-width:0px;border-bottom-width:0px;border-left-width:0px;padding-top:0px" title="AllGlass" border="0" alt="AllGlass" src="http://images.cnblogs.com/cnblogs_com/gnielee/Windows-Live-Writer/WPF_11044/AllGlass_1.png" width="529" height="354"></p>

<h1>Windows API</h1>

<p>     通过<a href="http://code.msdn.microsoft.com/WindowsAPICodePack">Windows API Code Pack</a> 可以对Aero Glass 效果进行开启或关闭。在程序中加入Microsoft.WindowsAPICodePack.Shell 命名空间，调整AeroGlassCompositioinEnabled 完成开/关Aero Glass的效果。</p>

<pre><span style="color:#2b91af">GlassWindow</span>.AeroGlassCompositionEnabled = checkBox.IsChecked.Value;</pre>

<h1>源代码</h1>

<p><a href="http://cid-c75f4e27adfe5bbc.office.live.com/self.aspx/GnieTech/WpfAeroGlass.zip">WpfAeroGlass.zip</a></p><img src="http://www.cnblogs.com/gnielee/aggbug/1842120.html?type=1" width="1" height="1" alt=""><p>评论: 18　<a href="http://www.cnblogs.com/gnielee/archive/2010/10/04/wpf-extend-aero-glass.html#pagedcomment">查看评论</a>　<a href="http://www.cnblogs.com/gnielee/archive/2010/10/04/wpf-extend-aero-glass.html#commentform">发表评论</a></p><p><a href="http://job.cnblogs.com/">程序员找工作，就在博客园</a></p><hr><p>最新新闻：<br>· <a href="http://news.cnblogs.com/n/76434/">ZFS文件系统提供数据加密功能</a><span style="color:gray">(2010-10-06 13:26)</span><br>· <a href="http://news.cnblogs.com/n/76433/">传必应将取代谷歌成为火狐浏览器默认搜索</a><span style="color:gray">(2010-10-06 13:24)</span><br>· <a href="http://news.cnblogs.com/n/76432/">Android过去6个月成美国智能手机买主首选平台</a><span style="color:gray">(2010-10-06 13:22)</span><br>· <a href="http://news.cnblogs.com/n/76431/">Foursquare解释昨天11小时宕机事件：数据库Shards过载</a><span style="color:gray">(2010-10-06 13:16)</span><br>· <a href="http://news.cnblogs.com/n/76430/">AOL也山寨团购网站</a><span style="color:gray">(2010-10-06 13:11)</span><br></p><p>编辑推荐：<a href="http://news.cnblogs.com/n/76302/">2010年10月编程语言排行榜：Java的混乱之治</a><br></p><p>网站导航：<a href="http://www.cnblogs.com">博客园首页</a>  <a href="http://home.cnblogs.com/">个人主页</a>  <a href="http://news.cnblogs.com">新闻</a>  <a href="http://home.cnblogs.com/ing/">闪存</a>  <a href="http://home.cnblogs.com/group/">小组</a>  <a href="http://space.cnblogs.com/q/">博问</a>  <a href="http://space.cnblogs.com">社区</a>  <a href="http://kb.cnblogs.com">知识库</a></p>