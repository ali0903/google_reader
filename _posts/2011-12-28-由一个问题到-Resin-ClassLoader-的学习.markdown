---
layout: post
title:  "由一个问题到 Resin ClassLoader 的学习"
date:   2011-12-28 12:22:55
author: liuxiaori
categories: program
---

## 由一个问题到 Resin ClassLoader 的学习
### by liuxiaori
### at 2011-12-28 12:22:55
### original <http://coolshell.cn/articles/6112.html>

<div style="clear:both;margin-top:5px;margin-bottom:5px"></div><p><strong>（<font color="#CC0000">感谢网友 liuxiaori 分享其经历</font>）, 从这个分享我们可以看到什么叫真正的学习。牛！</strong></p>
<h4>背景</h4>
<p>某日临近下班，一个同事欲取任何类中获取项目绝对路径，不通过Request方式获取，可是始终获取不到预想的路径。于是晚上回家google了一下，误以为是System.getProperty(“java.class.path”)-未实际进行测试，早上来和同事沟通，提出了使用这个内置方法，结果人家早已验证过，该方法是打印出CLASSPATH环境变量的值。</p>
<p>于是乎，继续google，找到了Class的getResource与getResourceAsStream两个方法。这两个方法会委托给ClassLoader对应的同名方法。以为这样就可以搞定(实际上确实可以搞定)，但验证过程中却发生了奇怪的事情。</p>
<p>软件环境：Windows XP、Resin 3、Tomcat6.0、Myeclipse、JDK1.5</p>
<h4>发展</h4>
<p>我的验证思路是这样的：</p>
<ol>
<li>定义一个Servlet，然后在该Servlet中调用Path类的getPath方法，getPath方法返回工程classpath的绝对路径，显示在jsp中。</li>
<li>另外在Path类中，通过Class的getResourceAsStream读取当前工程classpath路径中的a.txt文件，写入到getResource路径下的b.txt。</li>
</ol>
<p>由于时间匆忙，代码没有好好去组织。大致能看出上述两个功能，很简单不做解释。</p>
<p><span></span></p>
<pre>
public class Path {
    public String getPath() throws IOException
    {
        InputStream is = this.getClass().getResourceAsStream(&quot;/a.txt&quot;);
        File file = new File(Path.class.getResource(&quot;/&quot;).getPath()+&quot;/b.txt&quot;);
        OutputStream os = new FileOutputStream(file);
        int bytesRead = 0;
        byte[] buffer = new byte[8192];
        while ((bytesRead = is.read(buffer, 0, 8192)) != -1) {
            os.write(buffer, 0, bytesRead);
        }
        os.close();
        is.close();
        return this.getClass().getResource(&quot;/&quot;).getPath();
    }
}
</pre>
<pre>
public class PathServlet extends HttpServlet {
    private static final long serialVersionUID = 4443655831011903288L;
    public void doGet(HttpServletRequest request, HttpServletResponse response)
        throws ServletException, IOException
    {
        Path path = new Path();
        request.setAttribute(&quot;path&quot;, path.getPath());
        PrintWriter out = response.getWriter();
        out.println(&quot;Class.getResource(&#39;/&#39;).getPath():&quot; + path.getPath());
    }

    public void doPost(HttpServletRequest request, HttpServletResponse response)
        throws ServletException, IOException
    {
        doGet(request, response);
    }
}</pre>
<p>在此之前使用main函数测试Path.class.getResource(“/”).getPath()打印出预想的路径为：/D:/work/project/EhCacheTestAnnotation/WebRoot/WEB-INF/classes/</p>
<p>于是将WEB应用部署到Resin下，运行定义好的Servlet，出乎意料的结果是：/D:/work/resin-3.0.23/webapps/WEB-INF/classes/ 。特别奇怪，怎么会丢掉项目名称：EhCacheTestAnnotation呢？</p>
<p>还有一点值得注意，getPath方法中使用getResourceAsStream(“/a.txt”)却正常的读到了位于下图的a.txt。<br>
<img title="resin01" src="http://coolshell.cn/wp-content/uploads/2011/12/resin01.png" alt="" width="547" height="154"></p>
<p style="text-align:center">然后写到了如下图的b.txt中。代码中是这样实现的：File file = new File(Path.class.getResource(“/”).getPath()+”/b.txt”);本意是想在a.txt文件目录下入b.txt。结果却和料想的不一样。<br>
<img src="http://coolshell.cn/wp-content/uploads/2011/12/resin02.png" alt="" width="430" height="119"></p>
<p>请注意，区别还是丢掉了项目名称。</p>
<p>写的比较乱，稍微总结下：</p>
<p>程序中使用ClassLoader的两个方法：getResourceAsStream和getResource。但是事实证明在WEB应用的场景下却得到了不同的结果。大家别误会啊，看名字他们两个方法肯定不一样，这个我知道，但是getResourceAsStream总会获取指定路径下的文件吧，示例中的参数为”/a.txt”，正确读取“/D:/work/resin-3.0.23/webapps/EhCacheTestAnnotation/WEB-INF/classes/ ”下的a.txt，可是将文件写到getResource方法的getPath返回路径的b.txt文件。两个位置的差别在项目名称(EhCacheTestAnnotation)。</p>
<p>这样我暂且得出一个结论：通过getResourceAsStream和getResource两个方法获取的路径是不同的。但是为什么呢？</p>
<p>于是查看了ClassLoader的源码，贴出getResource和getResourceAsStream的源码。</p>
<pre>
public URL getResource(String name) {
    URL url;
    if (parent != null) {
        url = parent.getResource(name);
    } else {
        url = getBootstrapResource(name);
    }

    if (url == null) {
        url = findResource(name);
    }
    return url;
}

public InputStream getResourceAsStream(String name) {
    URL url = getResource(name);
    try {
        return url != null ? url.openStream() : null;
    } catch (IOException e) {
        return null;
    }
}</pre>
<p>从代码中看，getResourceAsStream将获取URL委托给了getResource方法。天啊，这是怎么回事儿？由此我彻底迷茫了，百思不得其解。</p>
<p>但是没有因此就放弃，继续回想了一遍整个过程：</p>
<ol>
<li>在main函数中，测试getResource与getResourceAsStream是完全相同的，正确的。</li>
<li>将其部署到Resin下，导致了getResource与getResourceAsStream获取的路径不一致。</li>
</ol>
<p>一个闪光点，是不是与web容器有关啊，于是换成Tomcat6.0。OMG，“奇迹”出现了，真的是这样子啊，换成Tomcat就一样了啊！和预想的一致。</p>
<p style="text-align:center">在Tomcat下运行结果如下图：<br>
<img src="http://coolshell.cn/wp-content/uploads/2011/12/resin03.png" alt="" width="554" height="107"></p>
<p>对，这就是我想要的。</p>
<p>因此我对Resin产生了厌恶感，之前也因为在Resin下程序报错，在Tomcat下正常运行而纠结了好久。记得看《松本行弘的程序世界》中对C++中的多继承是这样评价的(大概意思)：多重继承带来的负面影响多数是由于使用不当造成的。是不是因为对Resin使用不得当才使得和Tomcat下得到不同的结果。</p>
<p>最终，在查阅Resin配置文件resin.conf时候在&lt;host-default&gt;标签下发现了这样一段：</p>
<pre>
&lt;class-loader&gt;
        &lt;compiling-loader path=&quot;webapps/WEB-INF/classes&quot;/&gt;
        &lt;library-loader path=&quot;webapps/WEB-INF/lib&quot;/&gt; 
&lt;/class-loader&gt;</pre>
<p>其中的compiling-loader很可能与之有关，遂将其注释掉，一切正常。担心是错觉，于是将compiling-loader的path属性改成：webapps/WEB-INF/classes1，然后运行pathServlet，b.txt位置如下图：</p>
<p style="text-align:center"><img src="http://coolshell.cn/wp-content/uploads/2011/12/resin04.png" alt="" width="483" height="93"></p>
<p>确实与compiling-loader有关。</p>
<h4>结论</h4>
<p>终于通过将&lt;class-loader&gt;标签注释掉，同样可以在Resin中获取“预想”的路径。验证了的确是使用Resin的人出了问题。</p>
<h4>疑问</h4>
<div>
<p>但是没有这样就结束，我继续对getResource的源码进行了跟进，由于能力有限，没有弄清楚getResource的原理。</p>
<p>最终留下了两个疑问：</p>
<p>1、如果追踪到getResource方法的最底层(也许是JVM层面)，它实现的原理是什么？</p>
<p>2、为何Resin中&lt;class-loader&gt;的配置会对getResource产生影响，但是对getResourceAsStream毫无影响(getResourceAsStream可是将获取路径委托给getResource的啊)。还是这里我理解或者使用错误了？</p>
<p>本来文章到这里就结束了，本来是想问问牛人的，但是这个问题引起了很多的好奇心，于是我又花了一两周做了下面的调查。</p>
<h4>Resin中类加载器</h4>
<p>在我了解的<span style="font-family:&#39;Times New Roman&#39;">ClassLoader</span><span style="font-family:宋体">是在</span>com.caucho.loader包下，结构请看下图：<br>
<img src="http://coolshell.cn/wp-content/uploads/2011/12/resin05.png" alt="图1" width="390" height="810"><br>
图1<br>
<a href="http://coolshell.cn/wp-content/uploads/2011/12/resin06.png"><img src="http://coolshell.cn/wp-content/uploads/2011/12/resin06.png" alt="图2 （点击看大图）" width="677" height="354"></a><br>
图2</p>
<p>从上面两幅图中可以看出，图1是与Jdk有关联的，继承自java.net.URLClassLoader。DynamicClassLoader的注释是这样的：</p>
<pre>
/**
* Class	loader which checks for changes in class files and automatically
* picks up new jars.
*
* DynamicClassLoaders can be chained creating one virtual class loader.
* From the perspective of the JDK, it's all one classloader.  Internally,
* the class loader chain searches like a classpath.
*/
</pre>
<p>EnvironmentClassLoader又继承了DynamicClassLoader<span style="font-family:宋体">。</span></p>
<p>图<span style="font-family:&#39;Times New Roman&#39;">2</span><span style="font-family:宋体">应该是</span><span style="font-family:&#39;Times New Roman&#39;">Resin</span><span style="font-family:宋体">本身的</span><span style="font-family:&#39;Times New Roman&#39;">ClassLoader</span><span style="font-family:宋体">，其中</span><span style="font-family:&#39;Times New Roman&#39;">Loader</span><span style="font-family:宋体">是一个抽象类，包含了各种子类类加载器。</span></p>
<p>从两幅图中是看不出<span style="font-family:&#39;Times New Roman&#39;">Resin</span><span style="font-family:宋体">自身的</span><span style="font-family:&#39;Times New Roman&#39;">Loader</span><span style="font-family:宋体">体系与继承自</span><span style="font-family:&#39;Times New Roman&#39;">JVM</span><span style="font-family:宋体">的类加载器存在关系，那是不是他们就不存在某种关联呢？其实不是这样子的。请看下面</span><span style="font-family:&#39;Times New Roman&#39;">DynamicClassLoader</span><span style="font-family:宋体">源码的片段：</span></p>
<pre>
// List of resource loaders
private ArrayList _loaders = new ArrayList();
private JarLoader _jarLoader;
private PathLoader _pathLoader;
</pre>
<p>清楚了吧，这两个Loader<span style="font-family:宋体">分支通过组合的方式协作。</span></p>
<h4>类加载器顺序</h4>
<p>既然<span style="font-family:&#39;Times New Roman&#39;">Resin</span><span style="font-family:宋体">标准的</span><span style="font-family:&#39;Times New Roman&#39;">Loader</span><span style="font-family:宋体">及其子类以组合的方式嵌入到</span><span style="font-family:&#39;Times New Roman&#39;">DynamicClassLoader</span><span style="font-family:宋体">中，那么在加载一个“资源”时，</span><span style="font-family:&#39;Times New Roman&#39;">Loader</span><span style="font-family:宋体">分支和</span><span style="font-family:&#39;Times New Roman&#39;">java.net.URLClassLoader</span><span style="font-family:宋体">分支的先后顺序是什么样子的呢？</span></p>
<p>首先使用下面这段代码，将类加载器名称打印到控制台：</p>
<pre>
ClassLoader loader = PathServlet.class.getClassLoader();
while (loader != null) {
    System.out.println(loader.toString());
    loader = loader.getParent();
}
</pre>
<p>输出的结果为：</p>
<blockquote><p><em>EnvironmentClassLoader[web-app:http://localhost:8080/Test]</em></p>
<p><em><em>EnvironmentClassLoader[web-app:http://localhost:8080]</em></em></p>
<p><em>EnvironmentClassLoader[cluster ]</em></p>
<p><em><em>EnvironmentClassLoader[]</em></em></p>
<p><em>sun.misc.Launcher$AppClassLoader@cac268</em></p>
<p><em>sun.misc.Launcher$ExtClassLoader@1a16869</em></p></blockquote>
<p>额，没有任何一个Resin<span style="font-family:宋体">的</span><span style="font-family:&#39;Courier New&#39;">Loader</span><span style="font-family:宋体">被打印出来啊，对头，有就错了。下面就让我们看看</span><span style="font-family:&#39;Courier New&#39;">DynamicClassLoader</span><span style="font-family:宋体">中</span><span style="font-family:&#39;Courier New&#39;">getResource</span><span style="font-family:宋体">的源码来解答。</span></p>
<pre>
/**
* Gets the named resource
*
* @param name name of the resource
*/

public URL getResource(String name)
{
    if (_resourceCache == null) {
        long expireInterval = getDependencyCheckInterval();
        _resourceCache = new TimedCache(256, expireInterval);
    }

    URL url = _resourceCache.get(name);
    if (url == NULL_URL)
        return null;
    else if (url != null)
        return url;

    boolean isNormalJdkOrder = isNormalJdkOrder(name);

    if (isNormalJdkOrder) {
    url = getParentResource(name);
    if (url != null)
        return url;
    }

    ArrayList loaders = _loaders;
    for (int i = 0; loaders != null &amp;&amp; i &lt; loaders.size(); i++) {
        Loader loader = loaders.get(i);
        url = loader.getResource(name);

        if (url != null) {
            _resourceCache.put(name, url);
            return url;
        }

    }

    if (! isNormalJdkOrder) {
        url = getParentResource(name);
        if (url != null)
            return url;
    }

    _resourceCache.put(name, NULL_URL);
    return null;
}
</pre>
<p>代码不难懂，我画了一张流程图，不规范，凑合看下。<br>
<img title="resin07" src="http://coolshell.cn/wp-content/uploads/2011/12/resin07.png" alt="" width="326" height="601"></p>
<h4>总结</h4>
<pre>
boolean isNormalJdkOrder = isNormalJdkOrder(name);
</pre>
<p>这行代码控制着Resin<span style="font-family:宋体">类加载的顺序，如果是常规的类加载顺序</span><span style="font-family:&#39;Courier New&#39;">(</span><span style="font-family:宋体">向上代理，原文：</span>Returns true if the class loader should use the normal order, i.e. looking at the parents first.)<span style="font-family:宋体">，则先</span>url = getParentResource(name)，后遍历_loaders。否则是按照先遍历_loaders<span style="font-family:宋体">再</span>url = getParentResource(name)向上代理。</p>
<p>在我的调试经历中，一直都是先向上代理，后遍历_loaders<span style="font-family:宋体">的顺序，未遇到第二种方式。</span></p>
<p>文字对先向上代理，后遍历的顺序做点儿说明：</p>
<ol>
<li>首先使用“最上层”的<em>sun.misc.Launcher$ExtClassLoader@1a16869</em>加载name<span style="font-family:宋体">资源，如果找到就返回</span><span style="font-family:&#39;Courier New&#39;">URL</span><span style="font-family:宋体">否则返回</span><span style="font-family:&#39;Courier New&#39;">null</span></li>
<li>程序返回到<em>sun.misc.Launcher$AppClassLoader@cac268</em>，首先判断父类加载器返回的<span style="font-family:&#39;Courier New&#39;">url</span><span style="font-family:宋体">是否为</span><span style="font-family:&#39;Courier New&#39;">null</span><span style="font-family:宋体">，如果不为</span><span style="font-family:&#39;Courier New&#39;">null</span><span style="font-family:宋体">则返回</span><span style="font-family:&#39;Courier New&#39;">url</span><span style="font-family:宋体">，返回</span><span style="font-family:&#39;Courier New&#39;">null</span><span style="font-family:宋体">。</span></li>
<li><span style="font-family:宋体"><em><em><em>EnvironmentClassLoader[]</em></em></em></span></li>
<li>程序返回到<em>EnvironmentClassLoader[cluster ]</em>的getParentResource<span style="font-family:宋体">，再返回到</span><span style="font-family:&#39;Courier New&#39;">getResource</span><span style="font-family:宋体">，如果</span><span style="font-family:&#39;Courier New&#39;">url</span><span style="font-family:宋体">不为</span><span style="font-family:&#39;Courier New&#39;">null</span><span style="font-family:宋体">，则直接返回，否则遍历</span>ArrayList&lt;Loader&gt; loaders = _loaders;从各个loader<span style="font-family:宋体">中加载</span><span style="font-family:&#39;Courier New&#39;">name</span><span style="font-family:宋体">，如果加载成功，即不为</span><span style="font-family:&#39;Courier New&#39;">null</span><span style="font-family:宋体">，则返回，否则继续遍历，直至遍历完成。</span></li>
<li><em><em>EnvironmentClassLoader[web-app:http://localhost:8080]</em></em>同4</li>
<li><em>EnvironmentClassLoader[web-app:http://localhost:8080/Test]</em>同4</li>
</ol>
<p>OK<span style="font-family:宋体">，完事儿，后续还有，准备好好写几篇。</span></p>
<p>本文同时发布于：</p>
<ul>
<li><a href="http://www.oschina.net/question/129471_34225">http://www.oschina.net/question/129471_34225</a>。</li>
<li><a href="http://www.oschina.net/question/129471_35231#AnchorAnswer143898">http://www.oschina.net/question/129471_35231#AnchorAnswer143898</a></li>
</ul>
</div>
<p style="text-align:left">（全文完）</p>
<div style="float:left">
<div>
<a></a>
<a></a>
<a></a>
<a></a>
<a></a>
<a></a>
<a></a>
<a></a>
<a></a>
<a></a>
<a></a>
<a></a>
<a></a>
<a></a>
<a></a>
<a href="http://www.jiathis.com/share?uid=1541368"></a>
<a></a>
</div>



|2|left</div><div style="clear:both;margin-top:5px;margin-bottom:5px"></div><table cellspacing="0" cellpadding="3" border="0" style="clear:both">
    
    <tr>
        <td colspan="5"><b><font size="-1" style="display:block!important;padding:20px 0 5px!important">您可能也喜欢：</font></b></td>
    </tr>
    
        <tr>
                <td width="102" valign="top" style="padding:5px!important;margin:0!important">
                    <a title="Resin服务器getResource揭秘" style="text-decoration:none!important" href="http://app.wumii.com/ext/redirect?url=http%3A%2F%2Fcoolshell.cn%2Farticles%2F6335.html&amp;from=http%3A%2F%2Fcoolshell.cn%2Farticles%2F6112.html">
                        <img style="margin:0!important;padding:2px!important;border:1px solid #dddddd!important;width:96px!important;height:96px!important" src="http://static.wumii.com/site_images/2012/01/05/13442866.png" width="96px" height="96px"><br>
                        <font size="-1" color="#333333" style="display:block!important;line-height:15px!important;width:102px!important;font:12px/15px arial!important;height:60px!important;margin:3px 0 0 0!important;padding:0!important;overflow:hidden!important">Resin服务器getResource揭秘</font>
                    </a>
                </td>
                <td width="102" valign="top" style="padding:5px!important;margin:0!important;border-left:1px solid #dddddd!important">
                    <a title="酷壳 – CoolShell.cn" style="text-decoration:none!important" href="http://app.wumii.com/ext/redirect?url=http%3A%2F%2Fcoolshell.cn%2F&amp;from=http%3A%2F%2Fcoolshell.cn%2Farticles%2F6112.html">
                        <img style="margin:0!important;padding:2px!important;border:1px solid #dddddd!important;width:96px!important;height:96px!important" src="http://static.wumii.com/site_images/2012/02/03/14687960.jpg" width="96px" height="96px"><br>
                        <font size="-1" color="#333333" style="display:block!important;line-height:15px!important;width:102px!important;font:12px/15px arial!important;height:60px!important;margin:3px 0 0 0!important;padding:0!important;overflow:hidden!important">酷壳 – CoolShell.cn</font>
                    </a>
                </td>
                <td width="102" valign="top" style="padding:5px!important;margin:0!important;border-left:1px solid #dddddd!important">
                    <a title="JAVA的字符串拼接与性能" style="text-decoration:none!important" href="http://app.wumii.com/ext/redirect?url=http%3A%2F%2Fcoolshell.cn%2Farticles%2F2235.html&amp;from=http%3A%2F%2Fcoolshell.cn%2Farticles%2F6112.html">
                        <img style="margin:0!important;padding:2px!important;border:1px solid #dddddd!important;width:96px!important;height:96px!important" src="http://static.wumii.com/site_images/2011/06/21/13691753.png" width="96px" height="96px"><br>
                        <font size="-1" color="#333333" style="display:block!important;line-height:15px!important;width:102px!important;font:12px/15px arial!important;height:60px!important;margin:3px 0 0 0!important;padding:0!important;overflow:hidden!important">JAVA的字符串拼接与性能</font>
                    </a>
                </td>
                <td width="102" valign="top" style="padding:5px!important;margin:0!important;border-left:1px solid #dddddd!important">
                    <a title="Java构造时成员初始化的陷阱" style="text-decoration:none!important" href="http://app.wumii.com/ext/redirect?url=http%3A%2F%2Fcoolshell.cn%2Farticles%2F1106.html&amp;from=http%3A%2F%2Fcoolshell.cn%2Farticles%2F6112.html">
                        <img style="margin:0!important;padding:2px!important;border:1px solid #dddddd!important;width:96px!important;height:96px!important" src="http://static.wumii.com/images/blogWidget/wordpress_default.gif" width="96px" height="96px"><br>
                        <font size="-1" color="#333333" style="display:block!important;line-height:15px!important;width:102px!important;font:12px/15px arial!important;height:60px!important;margin:3px 0 0 0!important;padding:0!important;overflow:hidden!important">Java构造时成员初始化的陷阱</font>
                    </a>
                </td>
                <td width="102" valign="top" style="padding:5px!important;margin:0!important;border-left:1px solid #dddddd!important">
                    <a title="Java如何取源文件中文件名和行号" style="text-decoration:none!important" href="http://app.wumii.com/ext/redirect?url=http%3A%2F%2Fcoolshell.cn%2Farticles%2F611.html&amp;from=http%3A%2F%2Fcoolshell.cn%2Farticles%2F6112.html">
                        <img style="margin:0!important;padding:2px!important;border:1px solid #dddddd!important;width:96px!important;height:96px!important" src="http://static.wumii.com/images/blogWidget/wordpress_default.gif" width="96px" height="96px"><br>
                        <font size="-1" color="#333333" style="display:block!important;line-height:15px!important;width:102px!important;font:12px/15px arial!important;height:60px!important;margin:3px 0 0 0!important;padding:0!important;overflow:hidden!important">Java如何取源文件中文件名和行号</font>
                    </a>
                </td>
        </tr>
    
    <tr>
        <td colspan="5" align="right">
            <a style="text-decoration:none!important" href="http://www.wumii.com/widget/relatedItems" title="无觅相关文章插件">
                <font size="-1" color="#bbbbbb" style="display:block!important;font-family:arial!important;padding:5px 0!important;font-size:12px!important;color:#bbb!important">无觅</font>
            </a>
        </td>
    </tr>
</table><h3>相关文章</h3><ul><li>2012年01月05日 -- <a href="http://coolshell.cn/articles/6335.html" title="Resin服务器getResource揭秘">Resin服务器getResource揭秘</a></li><li>2012年01月06日 -- <a href="http://coolshell.cn/articles/6424.html" title="Hash Collision DoS 问题">Hash Collision DoS 问题</a></li><li>2011年10月31日 -- <a href="http://coolshell.cn/articles/5709.html" title="API设计：用流畅接口构造内部DSL">API设计：用流畅接口构造内部DSL</a></li><li>2011年10月14日 -- <a href="http://coolshell.cn/articles/5576.html" title="那些曾伴我走过编程之路的软件">那些曾伴我走过编程之路的软件</a></li><li>2011年08月01日 -- <a href="http://coolshell.cn/articles/5075.html" title="你确信你了解时间吗？">你确信你了解时间吗？</a></li><li>2011年07月18日 -- <a href="http://coolshell.cn/articles/4990.html" title="程序员技术练级攻略">程序员技术练级攻略</a></li><li>2011年06月29日 -- <a href="http://coolshell.cn/articles/4905.html" title="语言的数据亲和力">语言的数据亲和力</a></li></ul>