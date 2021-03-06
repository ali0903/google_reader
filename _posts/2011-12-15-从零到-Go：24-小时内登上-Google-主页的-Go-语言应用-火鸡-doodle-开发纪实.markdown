---
layout: post
title:  "从零到 Go：24 小时内登上 Google 主页的 Go 语言应用“火鸡”doodle 开发纪实"
date:   2011-12-15 16:41:30
author: xslidian
categories: program
---

## 从零到 Go：24 小时内登上 Google 主页的 Go 语言应用“火鸡”doodle 开发纪实
### by xslidian
### at 2011-12-15 16:41:30
### original <http://www.guao.hk/posts/from-zero-to-go-launching-on-the-google-homepage-in-24-hours.html>

<p><em>本文是 Google 搜索团队软件工程师 Reinaldo Aguiar 发表在 <a href="http://www.guao.hk/tag/go">Go 语言</a>博客的客座文章，他分享了<strong>在一天之内</strong>完成首款 Go 程序的开发并发布给数百万受众的经历。</em></p>
<p>我最近有幸参与了一项虽小却曝光率极高的“20% 项目”——<a href="http://www.google.com/logos/2011/thanksgiving.html">2011 年感恩节的 Google Doodle</a>。<a title="Google+ 互动 doodle：感恩节 2011" href="http://www.guao.hk/posts/google-plus-doodle-thanksgiving-day-2011.html">这幅 doodle</a> 中的火鸡由不同样式的头、翅膀、羽毛与爪子随机组合而成。用户可以通过点击火鸡的不同部位自定义组合。这种互动通过 JavaScript、CSS 实现，由浏览器实时渲染出各种火鸡。</p>
<p><a href="http://www.guao.hk/wp-content/uploads/2011/12/image001.png"><img src="http://www.guao.hk/wp-content/uploads/2011/12/image001-300x182.png" alt="" width="300" height="182"></a></p>
<p>用户制作出的个性化火鸡可以分享到 Google+ 上。点击“分享”按钮（图中未给出）即可在用户的 Google+ 流中生成一篇含有火鸡图片的帖子。要满足这种需求，图片必须是单独一张，且与用户所制作的火鸡完全相同。</p>
<p>由于火鸡的八个部位（头、双爪、几片羽毛等）各有 13 种样式，用户可能设计出八亿多种火鸡。预先制作好八亿多张图片显然行不通。因此，必须在服务端实时生成图片。出于即时扩展性与高度可用性的共同需求，合适的平台非常明显：Google App Engine！</p>
<p>接下来要决定的就是选用哪款 App Engine runtime 了。图像处理任务极度依赖 CPU，所以这种情况下性能是决定性因素。</p>
<p>为确保可靠，我们首先进行了测试。我们为新版 <a href="http://code.google.com/appengine/docs/python/python27/newin27.html">Python 2.7 runtime</a>（该版本提供基于 C 的图像处理库 <a href="http://www.pythonware.com/products/pil/">PIL</a>）与 Go runtime 准备了一些等效的演示应用。各应用 分别合成几张小图片生成图像文件，编码为 JPEG，并将 JPEG 数据作为 HTTP 响应发回客户端。Python 2.7 应用处理请求的中位响应时间为 65 毫秒，而 Go 应用的中位延时仅为 32 毫秒。</p>
<p>因此这成为了试用 Go runtime 的大好机会。</p>
<p>此前我对 Go 语言毫无经验，而时间又很紧：两天内达到生产需求。虽然紧张，我还是将它视作从另一常被忽略的方面——开发速度——测试 Go 的机会。完全没有 Go 语言开发经验的人能在多快的时间内掌握并开发出高性能高扩展性的应用？</p>
<h3>设计</h3>
<p>基本步骤是在 URL 中编码火鸡各态、实时绘制并编码图像。</p>
<p>各 doodle 的基础是背景图画：</p>
<p><a href="http://www.guao.hk/wp-content/uploads/2011/12/image01.jpg"><img src="http://www.guao.hk/wp-content/uploads/2011/12/image01-300x165.jpg" alt="" width="300" height="165"></a></p>
<p>有效的请求 URL 形如：<a href="http://google-turkey.appspot.com/thumb/20332620"><code>http://google-turkey.appspot.com/thumb/20332620</code></a></p>
<p><code>/thumb/</code> 后面跟着的数字字串（十六进制）代表各外观元素要绘制的形状，如下图所示：</p>
<p><a href="http://www.guao.hk/wp-content/uploads/2011/12/image03.png"><img src="http://www.guao.hk/wp-content/uploads/2011/12/image03.png" alt="" width="514" height="322"></a></p>
<p>程序的请求接管器解析 URL 决定各组件所选定的元素，在背景上绘制对应图像，并返回 JPEG 成品。</p>
<p>如果出错则返回默认图像。不必返回错误页面，因为用户不可能看到——浏览器肯定是在加载 <code>image</code> 标记中的 URL。</p>
<h3>实现</h3>
<p>在软件包层面，我们声明了一些数据结构，描述火鸡的各个元素、对应图像所在文件夹，以及各图像应绘制在背景图上的位置。</p>
<pre>var (
    // 各外观元素存储位置的文件夹映射。
    dirs = map[string]string{
        "h": "img/heads",
        "b": "img/eyes_beak",
        "i": "img/index_feathers",
        "m": "img/middle_feathers",
        "r": "img/ring_feathers",
        "p": "img/pinky_feathers",
        "f": "img/feet",
        "w": "img/wing",
    }

    // urlMap 映射各 URL 字符与所对应的外观元素。
    urlMap = [...]string{"b", "h", "i", "m", "r", "p", "f", "w"}

    // layoutMap 映射各外观元素与在背景图像上的位置。
    layoutMap = map[string]image.Rectangle{
        "h": {image.Pt(109, 50), image.Pt(166, 152)},
        "i": {image.Pt(136, 21), image.Pt(180, 131)},
        "m": {image.Pt(159, 7), image.Pt(201, 126)},
        "r": {image.Pt(188, 20), image.Pt(230, 125)},
        "p": {image.Pt(216, 48), image.Pt(258, 134)},
        "f": {image.Pt(155, 176), image.Pt(243, 213)},
        "w": {image.Pt(169, 118), image.Pt(250, 197)},
        "b": {image.Pt(105, 104), image.Pt(145, 148)},
    }
)</pre>
<p>上述各点的几何位置是通过图像中各元素的实际位置而得到的。</p>
<p>每次请求都从磁盘加载图像是很浪费的重复行为，因此我们在收到首个请求时就将全部 106 幅图像（13×8 个元素 + 1 幅背景 + 1 幅默认图）加载到全局变量中。</p>
<pre>var (
    // elements 映射各外观元素及其图像。
    elements = make(map[string][]*image.RGBA)

    // backgroundImage 含背景图像数据。
    backgroundImage *image.RGBA

    // defaultImage 是出错时返回的图像。
    defaultImage *image.RGBA

    // loadOnce 用于仅在首次请求时调用 load 函数。
    loadOnce <a href="http://golang.org/pkg/sync/#Once">sync.Once</a>
)

// load 函数从磁盘读取各 PNG 图像，并存储到对应的全局变量中。
func load() {
    defaultImage = loadPNG(defaultImageFile)
    backgroundImage = loadPNG(backgroundImageFile)
    for dirKey, dir := range dirs {
        paths, err := filepath.Glob(dir + "/*.png")
        if err != nil {
            panic(err)
        }
        for _, p := range paths {
            elements[dirKey] = append(elements[dirKey], loadPNG(p))
        }
    }
}</pre>
<p>请求按下述顺序处理：</p>
<ol>
<li>解析请求 URL，按顺序解码出各字符的十进制值。</li>
<li>为背景图像创建副本，作为最终图像的基础。</li>
<li>在背景图像上绘制各图像元素（使用 <code>layoutMap</code> 判断应绘制的位置。）</li>
<li>将图像编码为 JPEG</li>
<li>将 JPEG 直接写入 HTTP 响应写入器中，将图像返回给用户。</li>
</ol>
<p>如果出错，则将 <code>defaultImage</code> 返回给用户，并在 App Engine 控制台记下日志，供日后分析之用。</p>
<p>下面是含说明注释的请求接管器代码：</p>
<pre>func handler(w http.ResponseWriter, r *http.Request) {
    // <a href="http://blog.golang.org/2010/08/defer-panic-and-recover.html">Defer</a> 函数可以从错乱中恢复。
    // 恢复时将错误情况记录到 App Engine 控制台并给用户发送默认图像。
    defer func() {
        if err := recover(); err != nil {
            c := appengine.NewContext(r)
            c.Errorf(&quot;%s&quot;, err)
            c.Errorf(&quot;%s&quot;, &quot;Traceback: %s&quot;, r.RawURL)
            if defaultImage != nil {
                w.Header().Set(&quot;Content-type&quot;, &quot;image/jpeg&quot;)
                jpeg.Encode(w, defaultImage, &amp;imageQuality)
            }
        }
    }()

    // 在首次请求时从磁盘加载图像。
    loadOnce.Do(load)

    // 创建背景副本，作为绘制基础。
    bgRect := backgroundImage.Bounds()
    m := image.NewRGBA(bgRect.Dx(), bgRect.Dy())
    draw.Draw(m, m.Bounds(), backgroundImage, image.ZP, draw.Over)

    // 处理请求字串中的各个字符。
    code := strings.ToLower(r.URL.Path[len(prefix):])
    for i, p := range code {
        // 解码遇到的十六进制字符 p。
        if p &lt; &#39;a&#39; {
            // 是数字
            p = p - &#39;0&#39;
        } else {
            // 是字母
            p = p - &#39;a&#39; + 10
        }

        t := urlMap[i]    // 按索引查找元素类型
        em := elements[t] // 按类型查找元素图像
        if p &gt;= len(em) {
            panic(fmt.Sprintf(&quot;元素索引越界 %s: &quot;+
                &quot;%d &gt;= %d&quot;, t, p, len(em)))
        }

        // 将元素绘制到 m 上
        // 使用 layoutMap 指定其位置。
        draw.Draw(m, layoutMap[t], em[p], image.ZP, draw.Over)
    }

    // 编码为 JPEG 图像并写为响应。
    w.Header().Set(&quot;Content-type&quot;, &quot;image/jpeg&quot;)
    w.Header().Set(&quot;Cache-control&quot;, &quot;public, max-age=259200&quot;)
    jpeg.Encode(w, m, &amp;imageQuality)
}</pre>
<p>为简洁起见，这些代码段中我省略了一些辅助函数。完整代码请参阅<a href="http://code.google.com/p/go-thanksgiving/source/browse/">源码</a>。</p>
<h3>性能</h3>
<p><a href="http://www.guao.hk/wp-content/uploads/2011/12/image02.png"><img src="http://www.guao.hk/wp-content/uploads/2011/12/image02-550x135.png" alt="" width="550" height="135"></a></p>
<p>该图表从 App Engine 控制台截取，展示了发布后的平均请求时间。显然，即使在高负载情况下也没有超过 60 ms，中位延迟时间为 32 ms。考虑请求接管器在处理图像并实时编码，这已经相当快了。</p>
<h3>结论</h3>
<p>我觉得 Go 语言的语法直观、简单且洁净。我过去常与解析型语言打交道，尽管 Go 是静态类型系统的编译型语言，编写这款应用的感觉却更像是在用动态解析型语言。</p>
<p>开发服务器提供了可以在程序有变动后迅速重新编译的 <a href="http://code.google.com/appengine/downloads.html#Google_App_Engine_SDK_for_Go">SDK</a>，所以开发部署与解析型语言一样快。而且非常简单——我只花了不到一分钟就配置好了开发环境。</p>
<p>Go 语言优秀的文档也帮助了我迅速完成开发。文档是从源代码生成的，各函数的文档与相关联的源码直接链接。这不仅可以让开发者迅速理解特定函数的作用，还鼓励开发者深入挖掘软件包的实现，简化了对良好编程风格与规则的掌握。</p>
<p>编写这款应用的过程中，我只参考了三份资源：App Engine 的 <a href="http://code.google.com/appengine/docs/go/gettingstarted/helloworld.html">Hello World Go 示例</a>、<a href="http://golang.org/pkg/">Go 软件包文档</a>以及<a href="http://blog.golang.org/2011/09/go-imagedraw-package.html">一篇演示 Draw 软件包的博文</a>。感谢开发服务器的迅速部署，以及该语言自身的优异特性，我得以在 24 小时内掌握该语言，并开发出超快、满足生产需求的 doodle 生成器。</p>
<p>应用的完整源码（包括图像文件）可以在 <a href="http://code.google.com/p/go-thanksgiving/source/browse/">Google Code 项目</a>中下载到。</p>
<p>向设计该 doodle 的 Guillermo Real 与 Ryan Germick 致以特别的谢意。</p>
<p><em>原文：<a href="http://blog.golang.org/2011/12/from-zero-to-go-launching-on-google.html">From zero to Go: launching on the Google homepage in 24 hours</a></em></p>
<hr>
<p><small>© xslidian 发表于 <a href="http://www.guao.hk">谷奥——探寻谷歌的奥秘 ( http://www.guao.hk )</a>, 2011.  |
<a href="http://www.guao.hk/posts/from-zero-to-go-launching-on-the-google-homepage-in-24-hours.html#comments">2 条评论</a> |
<a href="http://www.guao.hk/posts/from-zero-to-go-launching-on-the-google-homepage-in-24-hours.html">永久链接</a> |
<a href="http://google.org.cn/about/">关于谷奥</a> |
<a href="http://google.org.cn/submit/">投稿/爆料</a><br>
Post tags: <a href="http://www.guao.hk/tag/doodle" rel="tag">doodle</a>, <a href="http://www.guao.hk/tag/doodle-for-google" rel="tag">doodle for Google</a>, <a href="http://www.guao.hk/tag/go" rel="tag">Go</a>
</small></p>
<img src="http://img.tongji.linezing.com/1105192/tongji.php" border="0" width="0" height="0">