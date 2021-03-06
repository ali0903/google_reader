---
layout: post
title:  "Linux下Rails3 + Lighttpd + fcgi部署研究"
date:   2011-06-21 13:15:48
author: MengLee
categories: program
---

## Linux下Rails3 + Lighttpd + fcgi部署研究
### by MengLee
### at 2011-06-21 13:15:48
### original <http://menglee.iteye.com/blog/1099600>

<p><span>
</span>
</p>
<div>转帖请保留链接：<a href="http://blog.sina.com.cn/s/blog_40c3a6d70100snsu.html">http://blog.sina.com.cn/s/blog_40c3a6d70100snsu.html</a>
</div>
<p> </p>
<p><strong>前言：为什么要研究Ruby on Rails的部署</strong>
</p>
<p>    学习Ruby on Rails已经一段时间了，一直使用自带的WEBrick服务器进行开发。</p>
<p>    WEBrick是一款纯Ruby编写的服务器，使用方便，很适合开发环境下进行系统调试。但是它不支持多线程访问，换句话说，所有的Ruby请求都是按照到达的时间先后，顺序处理的，因此效率不高，无法应用在实际的生产环境中。所以今天研究了一下如何将Rails3应用部署到真实的线上环境中。</p>
<p> </p>
<p>    搜集了一下当前比较流行的Rails部署方案，这些资料里面介绍了许多可选的方案：</p>
<p>    1、Agile Web Development with Rails (4th edition)，Chapter 16；</p>
<p>    2、范凯的博客：在Linux平台上安装和配置Ruby on Rails详解；</p>
<p>    3、一个日本开发者的博客：Rails 3.0.0rc + lighttpd + FastCGI で無理矢理動かしてみた。</p>
<p>    其中，范凯在RoR部署方案深度剖析一文中详细列举和分析了各种方案的优缺点以及性能上的差异，十分有借鉴意义。他认为各个方案在性能方面的排序为：</p>
<p>   <em> Lighttpd+FastCGI  &gt;  Lighttpd+Mongrel  &gt;  Nginx+Mongrel  &gt;  Apache+Mongrel  &gt;  Ngignx+FastCGI  &gt;  Apache+FastCGI</em>
</p>
<p>    因此，本文也采取了和他相同的部署方案。但是，由于他的博客较老，Ruby on Rails两年来从2.x升级到了3.x，以前的部署步骤也发生了变化，按照他的方法已经无法成功部署Rails3应用了。因此，本文根据相关资料，进行了些尝试。</p>
<p> </p>
<p>    我使用的环境如下：</p>
<p>    1、Linux ubuntu 2.6.32-32-generic #62-Ubuntu SMP Wed Apr 20 21:54:21 UTC 2011 i686 GNU/Linux</p>
<p>    2、gcc version 4.3.4 (Ubuntu 4.3.4-10ubuntu1)</p>
<p>    在开始部署之前，请确保你有一个Unix系统和较高版本的gcc编译器。</p>
<p> </p>
<p><strong>一、安装Ruby解释器</strong>
</p>
<p>    现在Ruby提供了针对Ubuntu平台的deb安装包，并且你也可以使用apt-get安装Ruby解释器，但是我仍然建议自行下载Ruby源代码编译安装。因为可以自己定制Ruby安装的路径，并且可以在编译过程中自行添加更多的特性，还可以自己控制安装的版本。</p>
<p>    Ruby的源代码可以从Ruby官方网站下载：Ruby解释器下载</p>
<p>    下载源代码包到本地Linux主机，然后解压缩，进入该目录，进行配置，编译和安装：</p>
<p>    tar xvfz ruby-1.9.2-p180.tar.gz</p>
<p>    cd ruby-1.9.2-p180</p>
<p>    ./configure -prefix=/opt/ruby-1.9.2-p180</p>
<p>    make &amp;&amp; make install</p>
<p> </p>
<p>    如果想浏览所有的configure参数，可以：</p>
<p>    ./configure -h</p>
<p>    如果不定制安装的目录，默认将安装到/usr/local目录下面。然而我建议自行定制一个ruby的安装目录，例如/opt/ruby-1.9.2-p180，这样便于以后的升级，不会和操作系统其他软件混在一起。而且今后可能会安装其他版本的Ruby解释器，通过分开目录安装还可以在不同的Ruby解释器之间切换。</p>
<p> </p>
<p>    安装好以后，我比较喜欢在/usr/local/bin下面建立软连接的方式来把Ruby加入系统搜索路径：</p>
<p>    ln -s /opt/ruby-1.9.2-p180/bin/ruby /usr/local/bin/ruby</p>
<p>    ln -s /opt/ruby-1.9.2-p180/bin/gem /usr/local/bin/gem</p>
<p> </p>
<p><strong>二、安装Ruby on Rails</strong>
</p>
<p>    确认服务器已经连接互联网的情况下执行：</p>
<p>    gem install rails</p>
<p>    即通过gem从rubyforge网站下载rails所有依赖包安装。</p>
<p> </p>
<p>    安装好rails以后，可以执行：</p>
<p>    ln -s /opt/ruby-1.9.2-p180/bin/rails /usr/local/bin/rails</p>
<p>    rails –v</p>
<p>    确认一下rails的版本。</p>
<p> </p>
<p><strong>三、安装Ruby的数据库适配器 </strong>
</p>
<p>    Rails发行包中已经自带纯Ruby的MySQL数据库适配器，然而对于生产环境来说，我们仍然应该下载安装C版本的数据库适配器，以达到更好的性能。Rails升级到3.0之后，推荐使用mysql2作为MySQL驱动，从这里可以下载    mysql2-0.2.11.gem<br>
    gem install mysql2-0.2.11.gem -l -- --with-mysql-dir=/opt/mysql5<br>
    注意--with-mysql-dir应该指向MySQL数据库的安装路径，如果数据库服务器和Web服务器不在同一台机器上，那么Web服务器上也必须安装MySQL软件，因为ruby的C版本MySQL适配器需要在编译的时候联接MySQL的系统库。</p>
<p> </p>
<p><strong>四、安装fcgi支持库</strong>
</p>
<p>    由于Ruby的fcgi支持库需要在编译的时候联接fcgi的系统库，因此我们需要先安装fcgi库，下载fcgi源代码发行包fcgi-2.4.0.tar.gz</p>
<p>    tar xzvf fcgi-2.4.0.tar.gz</p>
<p>    cd fcgi-2.4.0</p>
<p>    ./configure --prefix=/opt/fcgi-2.4.0</p>
<p>    make &amp;&amp; make install</p>
<p>    同样，将fcgi安装在自己指定的目录下，而不是默认的/usr/local，避免多个软件混在一起。</p>
<p> </p>
<p><strong>五、安装Ruby的FCGI支持</strong>
</p>
<p>    接下来就可以安装ruby的fcgi支持库了，下载fcgi-0.8.8.tgz</p>
<p>    tar xzvf fcgi-0.8.8.tgz</p>
<p>    cd fcgi-0.8.8</p>
<p>    ruby install.rb config -- --with-fcgi-include=/opt/fcgi-2.4.0/include --with-fcgi-lib=/opt/fcgi-2.4.0/lib</p>
<p>    ruby install.rb setup</p>
<p>    ruby install.rb install</p>
<p> </p>
<p>    注意：安装完毕之后，请确保/opt/ruby-1.9.2/lib/ruby/site_ruby/1.9.1/i686-linux/目录下有这三个共享库文件：fcgi.so  mysql.so  openssl.so，否则运行中会出现问题。如果缺少fcgi.so请重复步骤五，如果缺少mysql.so请重复步骤三，如果缺少openssl.so，请安装openssl库之后重复步骤一。</p>
<p> </p>
<p><strong>六、安装Lighttpd Web Server</strong>
</p>
<p>    在安装lighttpd之前，应该确认操作系统已经安装pcre，即Perl兼容的规则表达式库。</p>
<p>    如果没有，请执行：</p>
<p>    sudo apt-get install libpcre3 libpcre3-dev</p>
<p> </p>
<p>    然后下载Lighttpd：</p>
<p>    http://www.lighttpd.net/download/</p>
<p>    tar xzvf lighttpd-1.4.28.tar.gz</p>
<p>    cd lighttpd-1.4.28</p>
<p>    ./configure --prefix=/opt/lighttpd-1.4.28</p>
<p> </p>
<p>    configure完毕以后，会给出一个激活的模块和没有激活模块的清单，可以检查一下，是否自己需要的模块都已经激活，在enable的模块中一定要有“mod_rewrite”这一项，否则重新检查pcre是否安装。然后编译安装：</p>
<p>    make &amp;&amp; make install</p>
<p> </p>
<p><strong>七、配置Lighttpd</strong>
</p>
<p>    将Lighttpd的示例配置文件拷贝到配置文件目录中放置：</p>
<p>    mkdir /etc/lighttpd</p>
<p>    cp ./lighttpd-1.4.28/doc/config/lighttpd.conf /etc/lighttpd/</p>
<p>    cp ./lighttpd-1.4.28/doc/config/modules.conf /etc/lighttpd/</p>
<p> </p>
<p>    同时可以根据你的实际情况进行修改，主要修改如下：</p>
<p>    1）对/etc/lighttpd/modules.conf进行如下修改：</p>
<p> </p>
<pre name="code">  server.modules = (
        "mod_access",
        "mod_alias",
        "mod_auth",
        "mod_evasive",
        "mod_redirect",
        "mod_rewrite",
        "mod_setenv",
        "mod_usertrack",
        "mod_fastcgi",
        "mod_simple_vhost",
        "mod_compress",
        "mod_accesslog",
  )</pre>
<p>
       这个文件设置了lighttpd需要开启的模块。
</p>
<p>    2）对/etc/lighttpd/modules.conf进行如下修改：</p>
<p>       server.document-root, server.error-log，accesslog.filename需要指定相应的目录，如果没有创建对应的目录，lighttpd启动时会报错。</p>
<p>    3）用什么权限来运行Lighttpd</p>
<p>       server.username = "USER_NAME"</p>
<p>       server.groupname = "USER_GROUP"</p>
<p>       从安全角度来说，不建议用root权限运行Web Server，可以自行指定普通用户权限。</p>
<p>    4)  <span style="color:#ff0000">配置Lighttpd和Ruby on Rails之间的通信</span>
</p>
<p>       这一步配置十分重要，最简单的配置如下：</p>
<p>       <span style="white-space:pre">$HTTP["host"] =~ "(^|\.)testmengli\.com" {</span>
</p>
<p><span style="white-space:pre">           server.document-root = "/home/jowett/mini/public"</span>
</p>
<p><span style="white-space:pre">           server.error-handler-404 = "/dispatch.fcgi"</span>
</p>
<p><span style="white-space:pre">           fastcgi.server = (&quot;.fcgi&quot; =&gt;</span>
</p>
<p><span style="white-space:pre">               (&quot;localhost&quot; =&gt;</span>
</p>
<p><span style="white-space:pre">                   (</span>
</p>
<p><span style="white-space:pre">                     &quot;min-procs&quot; =&gt; 1,</span>
</p>
<p><span style="white-space:pre">                     &quot;max-procs&quot; =&gt; 2,</span>
</p>
<p><span style="white-space:pre">                     &quot;socket&quot; =&gt; &quot;/home/jowett/mini/tmp/socket/rails.socket&quot;,</span>
</p>
<p><span style="white-space:pre">                     &quot;bin-path&quot; =&gt; &quot;/home/jowett/mini/public/dispatch.fcgi&quot;,</span>
</p>
<p><span style="white-space:pre">                     &quot;bin-environment&quot; =&gt; (&quot;RAILS_ENV&quot; =&gt; &quot;development&quot;)</span>
</p>
<p><span style="white-space:pre">                   )</span>
</p>
<p><span style="white-space:pre">               )</span>
</p>
<p><span style="white-space:pre">           )</span>
</p>
<p><span style="white-space:pre">       }</span>
</p>
<p>       即由lighttpd启动2个FCGI进程，lighttpd和fcgi之间使用本机Unix Socket通信。</p>
<p>       socket：指定了通信的socket文件的存放位置；</p>
<p>       bin-path：指定了request转发的程序位置；</p>
<p>       server.error-handler-404：指定了请求的资源找不到时，进行处理的程序。相当于把动态请求过滤给Ruby on Rails服务器进行处理。</p>
<p>       5) 配置mime</p>
<p>          必须设置mime配置文件，不然lighttpd无法识别css、image等资源类型。</p>
<p> </p>
<p>          在/etc/lighttpd/lighttpd.conf中添加一行：</p>
<p>          include "mime.conf"</p>
<p>          然后创建mime配置文件：</p>
<p>          touch /etc/lighttpd/mime.conf</p>
<p> </p>
<p>          并且加入如下内容：</p>
<p> </p>
<pre name="code">     ##  MimeType handling
     ## -------------------
     ##
     ## Use the &quot;Content-Type&quot; extended attribute to obtain mime type if
     ## possible
     ##
     mimetype.use-xattr        = &quot;disable&quot; 

     ##
     ## mimetype mapping
     ##
     mimetype.assign             = (
            &quot;.pdf&quot;          =&gt;      &quot;application/pdf&quot;,
            &quot;.sig&quot;          =&gt;      &quot;application/pgp-signature&quot;,
            &quot;.spl&quot;          =&gt;      &quot;application/futuresplash&quot;,
            &quot;.class&quot;        =&gt;      &quot;application/octet-stream&quot;,
            &quot;.ps&quot;           =&gt;      &quot;application/postscript&quot;,
            &quot;.torrent&quot;      =&gt;      &quot;application/x-bittorrent&quot;,
            &quot;.dvi&quot;          =&gt;      &quot;application/x-dvi&quot;,
            &quot;.gz&quot;           =&gt;      &quot;application/x-gzip&quot;,
            &quot;.pac&quot;          =&gt;      &quot;application/x-ns-proxy-autoconfig&quot;,
            &quot;.swf&quot;          =&gt;      &quot;application/x-shockwave-flash&quot;,
            &quot;.tar.gz&quot;       =&gt;      &quot;application/x-tgz&quot;,
            &quot;.tgz&quot;          =&gt;      &quot;application/x-tgz&quot;,
            &quot;.tar&quot;          =&gt;      &quot;application/x-tar&quot;,
            &quot;.zip&quot;          =&gt;      &quot;application/zip&quot;,
            &quot;.mp3&quot;          =&gt;      &quot;audio/mpeg&quot;,
            &quot;.m3u&quot;          =&gt;      &quot;audio/x-mpegurl&quot;,
            &quot;.wma&quot;          =&gt;      &quot;audio/x-ms-wma&quot;,
            &quot;.wax&quot;          =&gt;      &quot;audio/x-ms-wax&quot;,
            &quot;.ogg&quot;          =&gt;      &quot;application/ogg&quot;,
            &quot;.wav&quot;          =&gt;      &quot;audio/x-wav&quot;,
            &quot;.gif&quot;          =&gt;      &quot;image/gif&quot;,
            &quot;.jpg&quot;          =&gt;      &quot;image/jpeg&quot;,
            &quot;.jpeg&quot;         =&gt;      &quot;image/jpeg&quot;,
            &quot;.png&quot;          =&gt;      &quot;image/png&quot;,
            &quot;.xbm&quot;          =&gt;      &quot;image/x-xbitmap&quot;,
            &quot;.xpm&quot;          =&gt;      &quot;image/x-xpixmap&quot;,
            &quot;.xwd&quot;          =&gt;      &quot;image/x-xwindowdump&quot;,
            &quot;.css&quot;          =&gt;      &quot;text/css&quot;,
            &quot;.html&quot;         =&gt;      &quot;text/html&quot;,
            &quot;.htm&quot;          =&gt;      &quot;text/html&quot;,
            &quot;.js&quot;           =&gt;      &quot;text/javascript&quot;,
            &quot;.asc&quot;          =&gt;      &quot;text/plain&quot;,
            &quot;.c&quot;            =&gt;      &quot;text/plain&quot;,
            &quot;.cpp&quot;          =&gt;      &quot;text/plain&quot;,
            &quot;.log&quot;          =&gt;      &quot;text/plain&quot;,
            &quot;.conf&quot;         =&gt;      &quot;text/plain&quot;,
            &quot;.text&quot;         =&gt;      &quot;text/plain&quot;,
            &quot;.txt&quot;          =&gt;      &quot;text/plain&quot;,
            &quot;.spec&quot;         =&gt;      &quot;text/plain&quot;,
            &quot;.dtd&quot;          =&gt;      &quot;text/xml&quot;,
            &quot;.xml&quot;          =&gt;      &quot;text/xml&quot;,
            &quot;.mpeg&quot;         =&gt;      &quot;video/mpeg&quot;,
            &quot;.mpg&quot;          =&gt;      &quot;video/mpeg&quot;,
            &quot;.mov&quot;          =&gt;      &quot;video/quicktime&quot;,
            &quot;.qt&quot;           =&gt;      &quot;video/quicktime&quot;,
            &quot;.avi&quot;          =&gt;      &quot;video/x-msvideo&quot;,
            &quot;.asf&quot;          =&gt;      &quot;video/x-ms-asf&quot;,
            &quot;.asx&quot;          =&gt;      &quot;video/x-ms-asf&quot;,
            &quot;.wmv&quot;          =&gt;      &quot;video/x-ms-wmv&quot;,
            &quot;.bz2&quot;          =&gt;      &quot;application/x-bzip&quot;,
            &quot;.tbz&quot;          =&gt;      &quot;application/x-bzip-compressed-tar&quot;,
            &quot;.tar.bz2&quot;      =&gt;      &quot;application/x-bzip-compressed-tar&quot;,
            &quot;.odt&quot;          =&gt;      &quot;application/vnd.oasis.opendocument.text&quot;, 
            &quot;.ods&quot;          =&gt;      &quot;application/vnd.oasis.opendocument.spreadsheet&quot;, 
            &quot;.odp&quot;          =&gt;      &quot;application/vnd.oasis.opendocument.presentation&quot;, 
            &quot;.odg&quot;          =&gt;      &quot;application/vnd.oasis.opendocument.graphics&quot;, 
            &quot;.odc&quot;          =&gt;      &quot;application/vnd.oasis.opendocument.chart&quot;, 
            &quot;.odf&quot;          =&gt;      &quot;application/vnd.oasis.opendocument.formula&quot;, 
            &quot;.odi&quot;          =&gt;      &quot;application/vnd.oasis.opendocument.image&quot;, 
            &quot;.odm&quot;          =&gt;      &quot;application/vnd.oasis.opendocument.text-master&quot;, 
            &quot;.ott&quot;          =&gt;      &quot;application/vnd.oasis.opendocument.text-template&quot;,
            &quot;.ots&quot;          =&gt;      &quot;application/vnd.oasis.opendocument.spreadsheet-template&quot;,
            &quot;.otp&quot;          =&gt;      &quot;application/vnd.oasis.opendocument.presentation-template&quot;,
            &quot;.otg&quot;          =&gt;      &quot;application/vnd.oasis.opendocument.graphics-template&quot;,
            &quot;.otc&quot;          =&gt;      &quot;application/vnd.oasis.opendocument.chart-template&quot;,
            &quot;.otf&quot;          =&gt;      &quot;application/vnd.oasis.opendocument.formula-template&quot;,
            &quot;.oti&quot;          =&gt;      &quot;application/vnd.oasis.opendocument.image-template&quot;,
            &quot;.oth&quot;          =&gt;      &quot;application/vnd.oasis.opendocument.text-web&quot;,

            # make the default mime type application/octet-stream.
            &quot;&quot;              =&gt;      &quot;application/octet-stream&quot;,
     )</pre>
 
<p> </p>
<p><strong>八、启动Lighttpd</strong>
</p>
<p>    在/etc/hosts文件中加入下面一行：</p>
<p>    127.0.0.1       testmengli.com</p>
<p> </p>
<p>    Lighttpd的启动命令是：/usr/local/lighttpd/sbin/lighttpd -f /etc/lighttpd/lighttpd.conf</p>
<p> </p>
<p>    通过以上步骤，就基本实现了Rails应用在生产环境下的部署，用到的技术包括Lighttpd+fcgi+mysql，希望对大家有帮助！</p>
<p> </p>
          
          <br><br>
          <span style="color:red">
            <a href="http://menglee.iteye.com/blog/1099600#comments" style="color:red">已有 <strong>0</strong> 人发表留言，猛击-&gt;&gt;<strong>这里</strong>&lt;&lt;-参与讨论</a>
          </span>
          <br><br><br>
<span style="color:#e28822">ITeye推荐</span>
<br>
<ul><li><a href="http://www.iteye.com/clicks/433"><span style="color:red;font-weight:bold">—软件人才免语言低担保 赴美带薪读研！— </span></a></li></ul>
<br><br><br>