---
layout: post
title:  "也谈Go语言编程 – Hello，Go!"
date:   2012-08-17 13:24:19
author: bigwhite
categories: program
---

## 也谈Go语言编程 – Hello，Go!
### by bigwhite
### at 2012-08-17 13:24:19
### original <http://tonybai.com/2012/08/17/hello-go/?utm_source=rss&utm_medium=rss&utm_campaign=hello-go>

<p><i><b>Go is expressive, concise, clean, and efficient. Its concurrency mechanisms make it easy to write programs that get the most out of multicore and networked machines, while its novel type system enables flexible and modular program construction. Go compiles quickly to machine code yet has the convenience of garbage collection and the power of run-time reflection. It&#39;s a fast, statically typed, compiled language that feels like a dynamically typed, interpreted language.</b> </i><br>
	                                                                                          <b>– 摘自<a href="http://golang.org">Go语言</a>官方站点</b></p>
<p>对于一门编程语言最深刻的喜欢莫过于对这门编程语言的设计理念的认同了，<a href="http://en.wikipedia.org/wiki/Go_(programming_language)">Go语言</a>是继<a href="http://tonybai.com/tag/c">C语言</a>之后又一门让我有如此感觉的编程语言。</p>
<p>初听到这门语言的存在时，我皱了皱眉：怎么起了这么一个名字！绝大多数编程语言都以名词或人名命名(如C、Java、<a href="http://python.org">Python</a>、<a href="http://ruby-lang.org">Ruby</a>、 <a href="http://haskell.org">Haskell</a>、Ada等)，而这门语言却用了一个日常生活中使用极为频繁的动词Go作为名字，这似乎有些太大众化了。不知为何，这个名字总是让 我联想到以前中国农村给小孩子常起的几个名字：二狗、牛娃等^_^。况且之前已经有很多IT产品也以Go作为名字(例 如，Thoughtworks公司出品的敏捷管理工具也叫<a href="http://www.thoughtworks-studios.com/go-agile-release-management">Go</a>)。</p>
<p>不过随着对这门语言的了解的深入，名字已不再是问题了。Go语言对我这个C程序员产生了强大的吸引力，原因如下：</p>
<p>* Go保持了与C语言一脉相承的理念：短小精悍、致力于成为系统编程语言、简洁而熟悉的C语言家族语法、静态编译型语言、保留了指针、运行高效；<br>
	* Go填平了C语言与生俱来的为数不少的&quot;坑&quot;；<br>
	* Go提升了编译速度，统一了源码组织、构建规范以及编码规范，让程序员更集中精力于问题域；<br>
	* Go改进了并发模型，在语言级别原生支持多核平台；<br>
	* Go语言起点高，以创新的设计以及甚小的代价兼容了现有主流编程范型(例如OO等)。</p>
<p>因此有人称Go为21世纪的C语言，我觉得不为过。从这篇文章开始，我将和大家一起走入Go语言的世界。</p>
<p><b>一、安装Go</b></p>
<p>Go语言官方站(从国内访问十分不稳定，时能时不能，原因你懂的)对Go安装有着较为详尽的说明。如果你使用的是Linux、Mac OS或Windows，那你应该可以很顺利地完成Go的安装。Go于今年上旬发布了第一个稳定版本Go 1，目前最新版本是1.0.2，可以从Google Code上的<a href="http://code.google.com/p/go">Go项目</a>中下载。我的环境为Ubuntu 10.04 32-bit，下载<a href="http://go.googlecode.com/files/go1.0.2.linux-386.tar.gz">go1.0.2.linux-386.tar.gz</a>后，解压到/usr/local/go下面：</p>
<p><span style="font-family:courier new,courier,monospace">$ ls /usr/local/go<br>
	api/     bin/           doc/        include/  LICENSE  PATENTS    README        src/   VERSION<br>
	AUTHORS  CONTRIBUTORS  favicon.ico  lib/      misc/    pkg/    robots.txt  test/</span></p>
<p>然后将/usr/local/go/bin添加到你的PATH环境变量中，你就可以在任意目录下执行go程序了：</p>
<p><span style="font-family:courier new,courier,monospace">$ go version<br>
	go version go1.0.2</span></p>
<p>如果你得到上面的输出结果，可以断定你的Go安装成功了！</p>
<p><b>二、第一个Go程序 – Hello, Go!</b></p>
<p>我们建立一个用于编写Go程序的工作目录go-examples，其绝对路径为/home/tonybai/go-examples。好了，开始 编写我们的第一个Go程序。</p>
<p>我们在go-examples下创建一个文件hellogo.go，其内容如下：</p>
<p><span style="font-family:courier new,courier,monospace">package main</span></p>
<p><span style="font-family:courier new,courier,monospace">import (<br>
	    &quot;fmt&quot;<br>
	)</span></p>
<p><span style="font-family:courier new,courier,monospace">func main() {<br>
	    fmt.Printf(&quot;Hello, Go!\n&quot;)<br>
	}</span></p>
<p>下面我们来编译该源文件并执行生成的可执行文件：</p>
<p><span style="font-family:courier new,courier,monospace">$ go build hellogo.go<br>
	$ ls<br>
	hellogo*  hellogo.go<br>
	$ hellogo<br>
	Hello, Go!</span></p>
<p>通过go build加上要编译的Go源文件名，我们即可得到一个可执行文件，默认情况下这个文件的名字为源文件名字去掉.go后缀。当然我们也 可以通过-o选项来指定其他名字：</p>
<p><span style="font-family:courier new,courier,monospace">$ go build -o myfirstgo hellogo.go<br>
	$ ls<br>
	myfirstgo*  hellogo.go</span></p>
<p>如果我们在go-examples目录下直接执行go build命令，后面不带文件名，我们将得到一个与目录名同名的可执行文件：</p>
<p><span style="font-family:courier new,courier,monospace">$ go build<br>
	$ ls<br>
	go-examples*  hellogo.go</span></p>
<p><b>三、程序入口点(entry point)和包(package)</b></p>
<p>Go保持了与C家族语言一致的风格：即目标为可执行程序的Go源码中务必要有一个名为main的函数，该函数即为可执行程序的入口点。除此之外 Go还增加了一个约束：作为入口点的main函数必须在名为main的package中。正如上面hellogo.go源文件中的那样，在源码第 一行就声明了该文件所归属的package为main。</p>
<p>Go去除了头文件的概念，而借鉴了很多主流语言都采用的package的源码组织方式。package是个逻辑概念，与文件没有一一对应的关系。 如果多个源文件都在开头声明自己属于某个名为foo的包，那这些源文件中的代码在逻辑上都归属于包foo(这些文件最好在同一个目录下，至少目前 的Go版本还无法支持不同目录下的源文件归属于同一个包)。</p>
<p>我们看到hellogo.go中import一个名为fmt的包，并利用该包内的Printf函数输出&quot;Hello, Go!&quot;。直觉告诉我们fmt包似乎是一个标准库中的包。没错，fmt包提供了格式化文本输出以及读取格式化输入的相关函数，与C中的printf或 scanf等类似。我们通过import语句将fmt包导入我们的源文件后就可以使用该fmt包导出(export)的功能函数了(比如 Printf)。</p>
<p>在C中，我们通过static来标识局部函数还是全局函数。而在Go中，包中的函数是否可以被外部调用，要看该函数名的首母是否为大写。这是一种 Go语言固化的约定：首母大写的函数被认为是导出的函数，可以被包之外的代码调用；而小写字母开头的函数则仅能在包内使用。在例子中你也看到了 fmt包的Printf函数其首母就是大写的。</p>
<p><b>四、GOPATH</b></p>
<p>我们把上面的hellogo.go稍作改造，拆分成两个文件：main.go和hello.go。</p>
<p><span style="font-family:courier new,courier,monospace">/* hello.go */<br>
	package hello</span></p>
<p><span style="font-family:courier new,courier,monospace">import &quot;fmt&quot;</span></p>
<p><span style="font-family:courier new,courier,monospace">func Hello(who string) {<br>
	    fmt.Printf(&quot;Hello, %s!\n&quot;, who)<br>
	}</span></p>
<p><span style="font-family:courier new,courier,monospace">/* main.go */<br>
	package main</span></p>
<p><span style="font-family:courier new,courier,monospace">import (<br>
	    &quot;hello&quot;<br>
	)</span></p>
<p><span style="font-family:courier new,courier,monospace">func main() {<br>
	    hello.Hello(&quot;Go!&quot;)<br>
	}</span></p>
<p>用go build编译main.go，结果如下：</p>
<p><span style="font-family:courier new,courier,monospace">$ go build main.go<br>
	main.go:4:2: import &quot;hello&quot;: cannot find package</span></p>
<p>编译器居然提示无法找到hello这个package，而hello.go中明明定义了package hello了。这是怎么回事呢？原来go compiler搜索package的方式与我们常规理解的有不同，Go在这方面也有一套约定，这里面涉及到一个重要的环境变量：GOPATH。我们可以使用go help gopath来查看一下有关gopath的manual。</p>
<p>Go compiler的package搜索顺序是这样的，以搜索hello这个package为例：</p>
<p>* 首先，Go compiler会在GO安装目录(GOROOT，这里是/usr/local/go)下查找是否有src/pkg/hello相关包源码；如果没有则继续；<br>
	* 如果export GOPATH=PATH1:PAHT2，则Go compiler会依次查找是否存在PATH1/src/hello、PATH2/src/hello；配置在GOPATH中的PATH1和PATH2被称作workplace；<br>
	* 如果在上述几个位置均无法找到hello这个package，则提示出错。</p>
<p>在本例子中，我们尚未设置过GOPATH环境变量，也没有建立类似PATH1/src/hello这样的路径，因此Go compiler显然无法找到hello这个package了。我们来设置一下GOPATH变量并建立相关目录：</p>
<p><span style="font-family:courier new,courier,monospace">$ export GOPATH=/home/tonybai/go-examples<br>
	$ mkdir src/hello<br>
	$ mv hello.go src/hello<br>
	$ go build main.go<br>
	$ ls<br>
	main*  main.go    src/<br>
	$ main<br>
	Hello, Go!</span></p>
<p><b>五、Go install</b></p>
<p>我们将main.go移到src/main中，这样这个demo project显得更加合理，所有源码均在src下：</p>
<p><span style="font-family:courier new,courier,monospace">$cd src<br>
	$ ls<br>
	hello/    main/</span></p>
<p>Go提供了install命令，与build命令相比，install命令在编译源码后还会将可执行文件或库文件安装到约定的目录下。我们以main目录为例：</p>
<p><span style="font-family:courier new,courier,monospace">$ cd main<br>
	$ go install</span></p>
<p>install命令执行后，我们发现main目录下没有任何变化，原先build时产生的main可执行文件也不见了踪影。别急，前面说过Go install也有一套自己的约定：<br>
	* go install(在src/DIR下)编译出的可执行文件以其所在目录名(DIR)命名<br>
	* go install将可执行文件安装到与src同级别的bin目录下，bin目录由go install自动创建<br>
	* go install将可执行文件依赖的各种package编译后，放在与src同级别的pkg目录下</p>
<p>现在我们来看看bin目录：<br>
	<span style="font-family:courier new,courier,monospace">$ ls /home/tonybai/go-examples<br>
	bin/  src/ pkg/<br>
	$ ls bin<br>
	main*</span></p>
<p>的确出现一个bin目录，并且刚刚编译的程序main在bin下面。</p>
<p>hello.go编译后并非可执行程序，在编译main的同时，由于main依赖hello package，因此hello也被关联编译了。这与单独在hello目录下执行install的结果是一样的，我们试试：</p>
<p><span style="font-family:courier new,courier,monospace">$ cd hello<br>
	$ go install<br>
	$ ls /home/tonybai/go-examples<br>
	bin/  pkg/  src/</span></p>
<p>在我们的workspace(go-examples目录)下出现了一个pkg目录，pkg目录下是一个名为linux_386的子目录，其下面有一个文 件：hello.a。这就是我们install的结果。hello.go被编译为hello.a并安装到pkg/linux_386目录下了。</p>
<p>.a这个后缀名让我们想起了静态共享库，但这里的.a却是Go独有的文件格式，与传统的<a href="http://tonybai.com/2012/04/11/multiple-definitions-of-the-compiling-phase/">静态共享库</a>并不兼容。但Go语言的设计者使用这个后缀名似乎是希望 这个.a文件也承担起Go语言中&quot;静态共享库&quot;的角色。我们不妨来试试，看看这个hello.a是否可以被Go compiler当作&quot;静态共享库&quot;来对待。我们移除src中的hello目录，然后在main目录下执行go build：</p>
<p><span style="font-family:courier new,courier,monospace">$ go build<br>
	main.go:4:2: import &quot;hello&quot;: cannot find package</span></p>
<p>Go编译器提示无法找到hello这个包，可见目前版本的Go编译器似乎不理pkg下的.a文件。<a href="http://code.google.com/p/go/issues/detail?id=2775">http://code.google.com/p/go/issues/detail?id=2775</a> 这个issue也印证了这一点，不过后续Go版本很可能会支持链接.a文件。毕竟我们在使用第三方package的时候，很可能无法得到其源码，并且在每个项目中都保存一份第三方包的源码也十分不利于项目源码的后期维护。</p>
<p><b>六、像脚本一样运行Go源码</b></p>
<p>Go具有很高的编译效率，这得益于其设计者对该目标的重视以及设计过程中细节方面的把控，当然这不是本文要关注的话题。正是由于go具有极速的编译，我们才可以像使用运行脚本语言那样使用它。</p>
<p>目前Go提供了run命令来直接运行源文件。比如：</p>
<p><span style="font-family:courier new,courier,monospace">$ go run main.go<br>
	Hello, Go!</span></p>
<p>go run实际上是一个将编译源码和运行编译后的二进制程序结合在一起的命令。但目前go源文件尚不支持作成Shebang Script，因为Go compiler尚不识别#!符号，下面的源码文件运行起来会出错：</p>
<p><span style="font-family:courier new,courier,monospace">#! /usr/local/go/bin/go run</span></p>
<p><span style="font-family:courier new,courier,monospace">package main</span></p>
<p><span style="font-family:courier new,courier,monospace">import (<br>
	    &quot;hello&quot;<br>
	)</span></p>
<p><span style="font-family:courier new,courier,monospace">func main() {<br>
	    hello.Hello(&quot;Go!&quot;)<br>
	}</span></p>
<p><span style="font-family:courier new,courier,monospace">$ go run main.go<br>
	package :<br>
	main.go:1:1: illegal character U+0023 &#39;#&#39;</span></p>
<p>不过我们可以可借助一些第三方工具来运行Shebang Go scripts，比如<a href="https://wiki.ubuntu.com/gorun">gorun</a>。</p>
<p><b>七、测试Go程序</b></p>
<p>前面说过Go起点较高，因此其自身就提供了一个轻量级<a href="http://tonybai.com/2010/09/30/opensource-a-lightweight-c-unit-test-framework/">单元测试</a>框架包以及运行测试集的命令。</p>
<p>我们用一个例子来说明如何编写包的测试代码以及如何运行这个测试。我们在go-examples/src下建立另外一个目录mymath，mymath目录下mymath包的代码如下：</p>
<p><span style="font-family:courier new,courier,monospace">/* mymath.go */<br>
	package mymath</span></p>
<p><span style="font-family:courier new,courier,monospace">func MyAdd(i int, j int) int {<br>
	    return i + j<br>
	}</span></p>
<p>要对mymath包进行测试，我们需在同一目录下创建mymath_test.go文件，其中对MyAdd函数的测试代码如下：</p>
<p><span style="font-family:courier new,courier,monospace">/* mymath_test.go */<br>
	package mymath</span></p>
<p><span style="font-family:courier new,courier,monospace">import &quot;testing&quot;</span></p>
<p><span style="font-family:courier new,courier,monospace">func TestMyAdd(t *testing.T) {<br>
	    a, b := 4, 2<br>
	    if x := MyAdd(a, b); x != 6 {<br>
	        t.Errorf(&quot;MyAdd(%d, %d) = %d, want %d&quot;, a, b, x, 6)<br>
	    }<br>
	}</span></p>
<p>在这个文件中我们import了Go提供的标准单元测试包-testing，并且每个测试方法都已Test作为前缀开头。现在我们来运行一下这个测试，在mymath目录下运行go test命令：</p>
<p><span style="font-family:courier new,courier,monospace">$ go test<br>
	PASS<br>
	ok      mymath    0.007s</span></p>
<p>如果用例出错，我们就可看到下面提示：</p>
<p><span style="font-family:courier new,courier,monospace">$go test<br>
	— FAIL: TestMyAdd (0.00 seconds)<br>
	    mymath_test.go:8: MyAdd(4, 2) = 6, want 6<br>
	FAIL<br>
	exit status 1<br>
	FAIL    mymath    0.007s</span></p>
<p>由上可以看出，Go test也有自己的一些约定：测试源文件的名字必须以_test.go作为结尾；测试代码与被测代码在同一个包中；测试代码要导入testing包；测试 函数要以Test作为前缀，并且测试函数的函数签名必须是这样的：func TestXXX(t *testing.T)。</p>
<p>语言自带对测试的支持的好处是一致性，避免了大家使用不同的测试框架而给阅读、交流和维护带来的不便。</p>
<p><b>八、项目源码组织</b></p>
<p>有了源码、有了对编译原理的理解、有了测试框架的支持，我们就可以策划项目源码组织形式了。不过Go的诸多约定基本上已经将我们限制在如下结构上：</p>
<p><span style="font-family:courier new,courier,monospace">proj1/<br>
	    bin/<br>
	        myapp1*<br>
	    pkg/<br>
	        linux_386/<br>
	            lib1.a<br>
	            lib2.a<br>
	    src/<br>
	        lib1/<br>
	            lib1.go     <br>
	            lib1_test.go<br>
	        lib2/<br>
	            lib2.go     <br>
	            lib2_test.go<br>
	        … …<br>
	        myapp1/<br>
	            main.go       # main package source<br>
	            main_test.go  # test source</span></p>
<p><span style="font-family:courier new,courier,monospace">proj2/<br>
	    bin/<br>
	        myapp2*<br>
	    pkg/<br>
	        linux_386/<br>
	            lib3.a<br>
	            lib4.a<br>
	    src/<br>
	        lib3/<br>
	            lib3.go     <br>
	            lib3_test.go<br>
	        lib4/<br>
	            lib4.go     <br>
	            lib4_test.go<br>
	        … …<br>
	        myapp2/<br>
	            main.go       # main package source<br>
	            main_test.go  # test source</span></p>
<p>基于上述结构，我们可将GOPATH设置为<span style="font-family:courier new,courier,monospace">proj1_path:proj2_path</span>。</p>
<p><b>九、代码风格(coding style)</b></p>
<p>Go程序员可以不再纠结于到底使用哪种代码风格，因为Go已经将代码风格做了严格的约定，一旦违反，Compiler直接给出Error。go还提供了fmt命令来协助Go程序员按标准格式化源文件。</p>
<p>从上面例子中我们可以看到Go的几大风格特点是：</p>
<p>* 左大括号&#39;{&#39;一定在函数名或if等语句在同一行<br>
	<span style="font-family:courier new,courier,monospace">   func foo {</span></p>
<p><span style="font-family:courier new,courier,monospace">   }</span></p>
<p>* 无需显式用分号;将语句分隔(除非是在一行写上多条语句)，因为compiler会替大家在适当位置加入分号的。<br>
	<span style="font-family:courier new,courier,monospace">   i, j := 2, 3<br>
	   MyAdd(i, j)</span></p>
<p><span style="font-family:courier new,courier,monospace">   if x := MyAdd(a, b); x != 6 {<br>
	            … …<br>
	   }</span></p>
<p>* if、for等后面的表达式无需用小括号括上<br>
	  <br>
	 <span style="font-family:courier new,courier,monospace">  if x != 5 {<br>
	            … …<br>
	   }</span></p>
<p><b>十、查看文档</b></p>
<p>Go的全量文档几乎与Go安装包一起发布。安装Go后，执行<span style="font-family:courier new,courier,monospace">godoc –http=:端口号</span>即可启动doc server。打开浏览器，输入http://localhost:端口号即可以看到几乎与Go官方站完全相同的文档页面。</p>
<p><b>十一、参考书籍</b></p>
<p>Go毕竟是新生代语言，其自身尚不成熟和完善，资料也较少。这里推荐两本市面上比较好的且内容已更新到Go 1的书籍：</p>
<p>* Mark Summerfield的《<a href="http://book.douban.com/subject/7070565">Programming in Go: creating applications for the 21st century</a>》<br>
	* Ivo Balbaert的《<a href="http://book.douban.com/subject/10558892/">The Way to Go – A Thorough Introduction to the Go Programming Language</a>》</p>
<p style="text-align:left">© 2012, <a href="http://tonybai.com">bigwhite</a>. 版权所有. </p><img src="http://www1.feedsky.com/t1/685950361/bigwhite/feedsky/s.gif?r=http://tonybai.com/2012/08/17/hello-go/?utm_source=rss&amp;utm_medium=rss&amp;utm_campaign=hello-go" border="0" height="0" width="0">