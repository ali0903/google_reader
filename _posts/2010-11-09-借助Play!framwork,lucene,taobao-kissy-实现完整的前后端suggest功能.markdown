---
layout: post
title:  "借助Play!framwork,lucene,taobao kissy 实现完整的前后端suggest功能"
date:   2010-11-09 14:22:09
author: 
categories: program
---

## 借助Play!framwork,lucene,taobao kissy 实现完整的前后端suggest功能
### by 
### at 2010-11-09 14:22:09
### original <http://www.javaeye.com/topic/806590>

先上效果图:
<br>
<br><img src="http://dl.javaeye.com/upload/attachment/345317/bac00c4b-9c45-3aca-82fa-ba9dcffb2833.jpg">
<br>   我是基于Play!framework开发的网站 想了解童鞋可以看看<a href="http://www.javaeye.com/topic/806974">http://www.javaeye.com/topic/806974</a>这篇帖子,目前最新版本是1.1，还有童鞋没听说过play!framework吗？简单的科普一下(以下内容来自于互联网)： 
<br>	<div>引用</div><div>Play Framework是一个功能完整的Java Web开发框架。采用RESTful架构设计，简便灵活。Play Framework使用MVC模式作为Web层，集成JPA构建持久层，Play Framework还使用一个基于Groovy的模板引擎。
<br>其特征如下：
<br>   1.  Play Framework让开发者无须重新编译打包发布应用，即可看到修改后的效果，方便开发人员调试应用。
<br>   2. Play!Framework采用了无状态模型，是一个真正意义上的“无共享”系统，能够在多个服务器上部署多个Play!Framework的实例，所有实例都不会互相干扰。
<br>   3. Play!Framework采用了Groovy作为模板引擎，让表示层真正做到了开发高效简洁
<br>   4. Play!Framework拥有精确的错误定位机制，当错误发生的时候，可以精确的定位到错误代码的位置。
<br>   5. Play!Framework的速度很快，启动快，运行的速度也十分快。</div>
<br>   官方网站是: [url]http://www.playframework.org/  [/url]  再广一个告:中文社区<a href="http://www.daocaozhu.com/">http://www.daocaozhu.com/</a> (刚开始弄,还希望喜欢play的童鞋一块在这里交流play!framework的使用心得,为play!framework在中国的发展贡献你的力量)
<br>回到正题上来：要实现这样的效果，我们都知道需要两个必不可少的工具:
<br>   <strong>1、前端suggest组件;2、中文分词以及全文检索</strong>
<br>   Suggest组件我选择了淘宝UED团队开发的开源js库:<strong>Kissy</strong> 官方网站是：[url]https://www.github.com/kissyteam/kissy   [/url]为什么选择kissy呐，我主要是被它的suggest组件还有图片轮换的组件所吸引,而且是淘宝UED团队开发的，质量上应该有保证,当然用起来确实也不错,虽然在选型上有些激进^_^。
<br>  全文检索毫无疑问是lucene,中文分词我用的是庖丁解牛(感谢作者的贡献)。
<br>Play提供了很多module，而集成lucene我们就需要用到search-module:<a href="http://www.playframework.org/modules/search">http://www.playframework.org/modules/search</a>   使用方法很简单,下载search-module到你Play目录下的modules文件夹里(不过好像play的安装包里已经带了,忘记了^_^)，然后在你项目的配置文件application.conf中  
<br><img src="http://dl.javaeye.com/upload/attachment/345323/d93a0964-45f2-3983-a2f3-4d19d06e17d3.jpg">
<br>  去掉标注为红色的那一行前面的#号，如果你将你的项目部署到服务器上，而你的服务器上没有play安装包，那你可以把search包直接放到你的项目WEB-INF(把项目打成war后)路径下，这儿改为:module.search=../search，这样就不会有找不到search module的错误了。
<br>  Search module已经配置好了，那我们就完整的实现这个小东西：前端的东西基本上引入kissy的主文件和kissy suggest 就行（当然还少不了必要的css^_^）。我们这儿为：
<br>  
<br><pre name="code">
&lt;script type=&quot;text/javascript&quot; src=&quot;@{&#39;public/javascripts/kissy/kissy.js&#39;}&quot;&gt;&lt;/script&gt;     
&lt;script type=&quot;text/javascript&quot; src=&quot;@{&#39;/public/javascripts/kissy/suggest.js&#39;}&quot;&gt;&lt;/script&gt;  
</pre>
<br>然后查询控件为:
<br>
<br><pre name="code">
 &lt;form id=&quot;J_TSearchForm&quot; action=&quot;@{Shops.search()}&quot; name=&quot;search&quot; target=&quot;_top&quot;&gt; 
          	&lt;div class=&quot;tsearch-panel-fields &quot;&gt;
			&lt;label for=&quot;q&quot; class=&quot;&quot;&gt;输入您想要的商品名称&lt;/label&gt;
			&lt;input name=&quot;q&quot; id=&quot;q&quot; autocomplete=&quot;off&quot; accesskey=&quot;s&quot;&gt;
			 &lt;s class=&quot;rc-tp-l&quot;&gt;&lt;/s&gt;
			 &lt;s class=&quot;rc-bt-l&quot;&gt;&lt;/s&gt;
		&lt;/div&gt;
		&lt;button type=&quot;submit&quot;&gt;搜索&lt;/button&gt; 
 &lt;/form&gt;
</pre>
<br>代码很眼熟？好吧，我承认，我山寨的taobao的…..
<br>加入Js 代码:
<br>
<br><pre name="code">
	   KISSY.ready(function(S) {
	      var sug = new S.Suggest('#q', "@{Shops.searchSuggest()}", {
	            autoFocus: true,
	            resultFormat: '约%result%个宝贝'
	        });
	   });
</pre>
<br>   开始说说后端实现的思路:  首先我们需要建立一个搜索词的词库。这个词库包含的内容有 1、检索词  2、检索次数（用于实现哪个词热度排序）3、检索词的拼音缩写版  用于输入个拼音也能给出提示。
<br>
<br>首先说下模型类
<br>
<br>这儿我们主要用到了两个实体<strong>:Product(商品对象)  SearchIndex(搜索建议词对象)</strong>
<br>
<br><pre name="code">
@Entity
@Indexed
public class SearchIndex extends Model{

	@Field
	public String name;//关键词
	
	@Field
	public long searchTimes;// 搜索次数
	
	@Field
	public String pinyin;//拼音版
	
	public SearchIndex(String name){
		this.name = name;		
		this.pinyin = Tool.cn2Spell(name);
		save();
	}
</pre>
<br>  上面是SearchIndex的大部分代码。大体说下：@Index表示这个类可以需要lucene建立索引,继承的Model类是play对CRUD的大部分封装，是以JPA为基础作的富血的Domain Model的基类。然后@Field注解表示需要索引的字段。  在构造方法中，有Tool.cn2Spell这一句，使用了Pinyin4j做的从汉字到拼音简写的转换。这段代码网上找的，我就不贴出来了。
<br>
<br>   然后说下搜索词的词库的建立：这儿我们主要是把商品名给分词后保存到词库中建立的搜索提示词的词库。在Product的构造方法里我们加了这么一句：
<br>   <pre name="code">this.addToIndexStore(name);</pre>
<br>这个方法的实现为：
<br><pre name="code">

	public static void addToIndexStore(String productName){
		try {
			List&lt;String&gt; list =Tool.paodingAnalyzerWord(productName);
			for(String str:list){
				if(!SearchIndex.isExsist(str)){
					new SearchIndex(str);
					Logger.info(&quot;SearchIndex  添加索引:&quot;, str);
				}
			}
		} catch (IOException e) {
			e.printStackTrace();
		}
	}
</pre>
<br>  这儿我们用到了<strong>庖丁解牛的中文分词</strong>。Search module默认使用的分词是lucene自带的那个StandardAnalyzer,这个在处理中文上还无法满足我们的需要，所以我选用了社区中比较知名的庖丁解牛，事实上开始我选的是IK Analyzer的，但一直没有找到合适匹配Play自带的lucene的版本 ^_^ 。下载庖丁http://code.google.com/p/paoding/  因为我使用的play版本是1.0.3 其中自带的lucene版本是2.3.1  所以我们下载庖丁用那个paoding-analysis-2.0.4-beta.zip 即可  
<br>还需要更改一下search module默认的分词：找到application.conf: 加入下面两句
<br><pre name="code">
  play.search.reindex=enabled
  play.search.analyser=net.paoding.analysis.analyzer.PaodingAnalyzer
</pre>
<br>第一句的作用官方文档是这样解释的：
<br>
<br><img src="http://dl.javaeye.com/upload/attachment/345337/16bd0287-c4d3-3f77-9833-2226baa406e2.jpg">
<br>
<br>  然后我们再来看看上面包含代码的那个图中的代码：分词的那一句是：
<br>   Tool.paodingAnalyzerWord:看代码：
<br><pre name="code">
	public static List&lt;String&gt; paodingAnalyzerWord(String word) throws IOException{
	    PaodingAnalyzer analyzer = new PaodingAnalyzer();
	    StringBuilder sb = new StringBuilder();
	    TokenStream ts = analyzer.tokenStream(&quot;&quot;, new StringReader(word));
	    Token token;
             sb.setLength(0);
             ArrayList&lt;String&gt; results = new ArrayList&lt;String&gt;();
             while ((token = ts.next()) != null) {
                sb.append(new String(token.termBuffer()).trim()).append(&#39;/&#39;);
             results.add(new String(token.termBuffer()).trim());
        }
        if (sb.length() &gt; 0) {
           sb.setLength(sb.length() - 1);
        }
        
        return results;
}
</pre>
<br>
<br>这段代码实现了中文分词，基本上没有啥需要解释的。
<br>然后我们遍历分出来的词，查询下这个词是不是已经存在，存在的话就不用理它，不存在的话就加入到检索词词库中。这儿我都是和数据库直接交互，可能性能上会有问题，所以我们可以把搜索词全部加载到缓存中再做处理。
<br>
<br>这样搜索建议词的词库就建立好了。
<br>
<br>然后我们看看Shops.searchSuggest（上图中搜索form提交的地方）方法的实现:
<br><pre name="code">

    /**
     * 商品搜索提示
     */
	public static void searchSuggest(String q) throws IOException{
		
	Search.Query query = Search.search(&quot;name:&quot;+q.trim()+&quot;* OR pinyin:&quot;+q.trim()+&quot;*&quot;, SearchIndex.class);
		
	List&lt;SearchIndex&gt; list =query.orderBy(&quot;searchTimes&quot;).reverse().page(0, 8).fetch();
		
	List&lt;String[]&gt; results = new ArrayList&lt;String[]&gt;();
		
		
	for(SearchIndex si:list){			
	     Search.Query q2=Search.search(&quot;name:&quot;+si.name+&quot;*&quot;,Product.class);
	     String[] str ={si.name,String.valueOf(q2.count())};			
	     results.add(str);
	}
		
	String result =new Gson().toJson(results);		
	renderJSON(&quot;KISSY.Suggest.callback({&#39;result&#39;:&quot;+result.replace(&quot;\&quot;&quot;, &quot;\&#39;&quot;)+&quot;})&quot;);
	}
</pre>
<br>  解释下上面的代码:
<br>
<br>  第一句中的Search.Query是search module中的类，主要是对lucene操作的一些封装。Search.search()中的第一个参数是lucene查询表达式。这儿表示以name或者pinyin这两个字段进行匹配查询。这个查询用于查找用户输入字的相关搜索词（suggest）。
<br>
<br>   下面的for循环用于查询匹配这个搜索词的商品的数量。
<br>最后返回json：注意，这儿返回的JSON串都必须是单引号包括。比如{‘name’:’zhangsan’}这种。
<br>
<br>   这样基本上就完成了一个简单的search suggest  功能。写的比较繁杂，主要是为了力求把事情说清楚，同时掺杂了一些play的使用说明。本文主要目的是向大家展示Play!framework的灰常好用,<strong>由于个人水平有限，极有可能存在不合适的地方，所以本文参考为主，不对的还请大家多多指教</strong>。
<br>
          
          <br><br>
          作者: <a href="http://hsfgo.javaeye.com">lookdd1</a> 
          <br>
          声明: 本文系JavaEye网站发布的原创文章，未经作者书面许可，严禁任何网站转载本文，否则必将追究法律责任！
          <br><br>
          <span style="color:red">
            <a href="http://www.javaeye.com/topic/806590" style="color:red">已有 <strong>16</strong> 人发表回复，猛击-&gt;&gt;<strong>这里</strong>&lt;&lt;-参与讨论</a>
          </span>
          <br><br><br>
<span style="color:#e28822">JavaEye推荐</span>
<br>
<ul><li><a href="http://www.iteye.com/clicks/433"><span style="color:red;font-weight:bold">—软件人才免语言低担保 赴美带薪读研！— </span></a></li></ul>
<br><br><br>