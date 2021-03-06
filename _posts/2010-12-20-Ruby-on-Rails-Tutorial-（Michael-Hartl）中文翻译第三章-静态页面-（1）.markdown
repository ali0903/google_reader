---
layout: post
title:  "Ruby on Rails Tutorial （Michael Hartl）中文翻译第三章 静态页面 （1）"
date:   2010-12-20 21:58:11
author: 
categories: program
---

## Ruby on Rails Tutorial （Michael Hartl）中文翻译第三章 静态页面 （1）
### by 
### at 2010-12-20 21:58:11
### original <http://www.javaeye.com/topic/847581>

（周末偷懒了。。。这周开始第三章。。静态页面，加油～）
<br><span style="font-size:large">第三章 Mostly static pages</span>
<br>在这一章中我们将开始开发一个例子程序，这个程序就是本书中接下来的所使用的例子。
<br>尽管这个程序最终将会实现用户，微博信息，登录登出验证框架等，但是我们就从看起来蛮简单的开始——创建静态页面。尽管它很简单，但是创建静态页面是一项非常有意义和启发性的练习，对于我们新程序来说是一个完美的开端。
<br>Rails虽然是用来设计数据库支持的动态网站，但是它也擅长用原生的HTML文件来产生静态网页。实际上用Rails产生静态网页会有明显的优势：我们可以非常容易的添加一小部分的动态内容。在这一章中我们也将学习如何添加这些内容。继续下去我们就将会第一次亲密接触自动测试，它会让我们对我们写过的代码更有自信。此外，有一个好的测试工具会是我们重构代码的时候更有信心——只改结构和形式没有改变它应有的功能。
<br>
<br>如第二章所说，我们从新建一个Rails的应用程序开始，这次叫它：sample_app：
<br><pre name="code">
$ cd ~/rails_projects
$ rails new sample_app -T
$ cd sample_app
</pre>
<br>在这里的 -T 参数是告诉Rails不要生成使用Test::Unit工具测试test文件夹。这部是我门不要测试，正好相反，在第3.2节中我们将使用Rspec测试框架来代替Test::Unit。
<br>回到我们的项目，我们接下来要做的是使用文本编辑器去修改Gemfile文件。我们需要添加一些我们程序需要的gem包，和前一章中所声明的，sqlite3-ruby的gem的版本是1.2.5，这和以前一样，是我们在本地开发所需要的gem（重申：我的系统需要1.2.5版本，如果你无法使用它，你可以使用1.3.1版本），另一方面，对于这个示例次序来说，我们还需要2个以前没用的gem：Rspec和 Rails的rspec库，
<br>示例代码如代码3.1（提示：如果你想要提前把我们这个程序的所有gem都安装，我觉得你应该参考代码10.42）：
<br>代码3.1：示例程序的Gemfile
<br><pre name="code">
source &#39;http://rubygems.org&#39;

gem &#39;rails&#39;, &#39;3.0.3&#39;
gem &#39;sqlite3-ruby&#39;, &#39;1.3.2&#39;, :require =&gt; &#39;sqlite3&#39;

group :development do
  gem &#39;rspec-rails&#39;, &#39;2.3.0&#39;
end

group :test do
  gem &#39;rspec&#39;, &#39;2.3.0&#39;
  gem &#39;webrat&#39;, &#39;0.7.1&#39;
end
</pre>
<br>上面代码的开发模式里包含了rspec-rails，所以我们可以使用Rspec-specific生成器，在测试模式下包含了rspec，所以我们可以运行那些测试。（我们也包含Webrat测试工具，以前的版本它会被当成一个依赖包自动的添加，但是现在我们需要手动的添加它，它是目前用来生成页面测试的工具，所以我们需要它）。然后我们用Bundle install来安装这些gem。
<br><pre name="code">
$ bundle install
</pre>
<br>为了让Rails知道我们使用Rspec代替Test::Unit，我们需要安装一些Rspec需要的文件。我们可以通过 rails generate：
<br><pre name="code">
$ rails generate rspec:install
</pre>
<br>到这里，我要做的只剩下初始化Git库了：
<br>
<br><pre name="code">
$ git init
$ git add .
$ git commit -m "Initial commit"
</pre>
<br>
<br>和第一程序一样，我还是建议更新“README”文件（位于程序的根目录。）。代码3.2的描述或许对你有些帮助。
<br>
<br>代码 3.2 示例程序的READER文件.  
<br><pre name="code">
# Ruby on Rails Tutorial: sample application

This is the sample application for
[*Ruby on Rails Tutorial: Learn Rails by Example*](http://railstutorial.org/)
by [Michael Hartl](http://michaelhartl.com/).
</pre>
<br>接下来，我们给他添上markdown的后缀名，然后提交它：
<br><pre name="code">
$ git mv README README.markdown
$ git commit -a -m "Improved the README"
</pre>
<br>
<br>
<br><img src="http://dl.javaeye.com/upload/attachment/371103/e190c673-810d-3c45-90c6-5214093390eb.png">
<br>
<br>图3.1： 创建示例程序的GitHub库
<br>
<br>为了将我们书中的程序放在github进行控制，增加一个新库是一个不错的主意。
<br><pre name="code">
$ git remote add origin git@github.com:&lt;username&gt;/sample_app.git
$ git push origin master
</pre>
<br>(提示：到这一步 http://github.com/railstutorial/sample_app 库中的代码是示例程序所有的代码，欢迎咨询参考，但是有2点要注意：1，自己输入代码比使用现成的代码会学到更多的东西；2，书中提到Github库的代码有的会有差别，这主要是由于一方面这个库合并了本书中的练习和视频教程中的练习。)
<br>当然，我也可以在现在就部署到Heroku，
<br><pre name="code">
$ heroku create
$ git push heroku master
</pre>
<br>
<br>(如果错误可以参考代码1.8试着修复。) 在接下来的教程中，我建议你可以定期的push和发布程序；
<br><pre name="code">
$ git push
$ git push heroku
</pre>
<br>
<br>这样我们就可以开发示例程序了~
<br>
<br><span style="font-size:large">3.1 静态页面</span>
<br>
<br>Rails有2个主要的方法产生静态网页。第一，Rails可以处理由原生的HTML文件组成的真正静态页面（我也不明白什么意思，看下去吧）。第二，Rails允许我们定义rails可以渲染的含有原生HTML的视图，这样服务器可以将它发送到浏览器。
<br>回忆一下第1.2.3（图1.2）节中所讲Rails的结构对我们把握中心是很有帮助的。这一节我们主要的工作目录是app/controllers 和 app/views，在3.2节中我们会接触到新的目录；
<br>
<br>（==========================分割线，慢慢翻译，也是我学习的过程==明天继续===========================）
<br>
<br>
          
          <br><br>
          作者: <a href="http://iseesea.javaeye.com">iseesea</a> 
          <br>
          声明: 本文系JavaEye网站发布的原创文章，未经作者书面许可，严禁任何网站转载本文，否则必将追究法律责任！
          <br><br>
          <span style="color:red">
            <a href="http://www.javaeye.com/topic/847581" style="color:red">已有 <strong>0</strong> 人发表回复，猛击-&gt;&gt;<strong>这里</strong>&lt;&lt;-参与讨论</a>
          </span>
          <br><br><br>
<span style="color:#e28822">JavaEye推荐</span>
<br>
<ul><li><a href="http://www.iteye.com/clicks/433"><span style="color:red;font-weight:bold">—软件人才免语言低担保 赴美带薪读研！— </span></a></li></ul>
<br><br><br>