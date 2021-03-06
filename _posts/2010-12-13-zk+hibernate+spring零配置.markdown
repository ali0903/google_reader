---
layout: post
title:  "zk+hibernate+spring零配置"
date:   2010-12-13 15:47:55
author: 
categories: program
---

## zk+hibernate+spring零配置
### by 
### at 2010-12-13 15:47:55
### original <http://www.javaeye.com/topic/840871>

<p> </p>
<p>zk+hibernate+spring零配置</p>
<p> </p>
<p>zk5.0.5</p>
<p> </p>
<p>hibernate3.6</p>
<p> </p>
<p>spring3.05</p>
<p> </p>
<p>也许你在使用apply或者use一个类名的或者<span style="font-family:Courier New">&lt;?variable-resolver class=&quot;org.zkoss.zkplus.spring.DelegatingVariableResolver&quot;?&gt;</span> </p>
<p> </p>
<p>那么下面的方法对你也许是一种更优雅的方式</p>
<p> </p>
<p> </p>
<p> </p>
<p>一，spring自动扫描注解配置，如果熟悉ss2h，看起来没什么不同</p>
<p> </p>
<pre name="code">&lt;?xml version=&quot;1.0&quot; encoding=&quot;UTF-8&quot;?&gt;
&lt;beans xmlns=&quot;http://www.springframework.org/schema/beans&quot;
	xmlns:xsi=&quot;http://www.w3.org/2001/XMLSchema-instance&quot; xmlns:aop=&quot;http://www.springframework.org/schema/aop&quot;
	xmlns:context=&quot;http://www.springframework.org/schema/context&quot;
	xmlns:jdbc=&quot;http://www.springframework.org/schema/jdbc&quot; xmlns:jee=&quot;http://www.springframework.org/schema/jee&quot;
	xmlns:lang=&quot;http://www.springframework.org/schema/lang&quot; xmlns:oxm=&quot;http://www.springframework.org/schema/oxm&quot;
	xmlns:task=&quot;http://www.springframework.org/schema/task&quot; xmlns:tx=&quot;http://www.springframework.org/schema/tx&quot;
	xmlns:util=&quot;http://www.springframework.org/schema/util&quot;
	xsi:schemaLocation=&quot;http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans-3.0.xsd
		http://www.springframework.org/schema/aop http://www.springframework.org/schema/aop/spring-aop-3.0.xsd
		http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context-3.0.xsd
		http://www.springframework.org/schema/jdbc http://www.springframework.org/schema/jdbc/spring-jdbc-3.0.xsd
		http://www.springframework.org/schema/jee http://www.springframework.org/schema/jee/spring-jee-3.0.xsd
		http://www.springframework.org/schema/lang http://www.springframework.org/schema/lang/spring-lang-3.0.xsd
		http://www.springframework.org/schema/oxm http://www.springframework.org/schema/oxm/spring-oxm-3.0.xsd
		http://www.springframework.org/schema/task http://www.springframework.org/schema/task/spring-task-3.0.xsd
		http://www.springframework.org/schema/tx http://www.springframework.org/schema/tx/spring-tx-3.0.xsd
		http://www.springframework.org/schema/util http://www.springframework.org/schema/util/spring-util-3.0.xsd&quot;&gt;

	&lt;description&gt;系统配置信息&lt;/description&gt;
	
	
	&lt;!-- 定义受环境影响易变的变量 --&gt;

	&lt;context:property-placeholder location=&quot;/WEB-INF/config/application.properties&quot; /&gt;
	
	&lt;!--  --&gt;
	&lt;!-- 数据源配置 --&gt;
	&lt;!--  --&gt;
	&lt;bean id=&quot;dataSource&quot; class=&quot;com.mchange.v2.c3p0.ComboPooledDataSource&quot;&gt;
		&lt;property name=&quot;driverClass&quot; value=&quot;${jdbc.driverClass}&quot; /&gt;
		&lt;property name=&quot;jdbcUrl&quot; value=&quot;${jdbc.url}&quot; /&gt;
		&lt;property name=&quot;user&quot; value=&quot;${jdbc.username}&quot; /&gt;
		&lt;property name=&quot;password&quot; value=&quot;${jdbc.password}&quot; /&gt;
		&lt;property name=&quot;maxPoolSize&quot; value=&quot;${jdbc.maxPoolSize}&quot;&gt;&lt;/property&gt;
		&lt;property name=&quot;minPoolSize&quot; value=&quot;${jdbc.minPoolSize}&quot;&gt;
		&lt;/property&gt;
		&lt;property name=&quot;initialPoolSize&quot; value=&quot;${jdbc.initialPoolSize}&quot;&gt;
		&lt;/property&gt;
		&lt;property name=&quot;maxIdleTime&quot; value=&quot;${jdbc.maxIdleTime}&quot;&gt;
		&lt;/property&gt;
		&lt;property name=&quot;acquireIncrement&quot; value=&quot;${jdbc.acquireIncrement}&quot;&gt;
		&lt;/property&gt;
		&lt;property name=&quot;acquireRetryAttempts&quot; value=&quot;${jdbc.acquireRetryAttempts}&quot;&gt;
		&lt;/property&gt;
		&lt;property name=&quot;acquireRetryDelay&quot; value=&quot;${jdbc.acquireRetryDelay}&quot;&gt;
		&lt;/property&gt;
		&lt;property name=&quot;idleConnectionTestPeriod&quot; value=&quot;${jdbc.idleConnectionTestPeriod}&quot;&gt;
		&lt;/property&gt;
		&lt;property name=&quot;preferredTestQuery&quot; value=&quot;${jdbc.preferredTestQuery}&quot;&gt;
		&lt;/property&gt;
		&lt;property name=&quot;numHelperThreads&quot; value=&quot;${jdbc.numHelperThreads}&quot;&gt;
		&lt;/property&gt;
	&lt;/bean&gt;

	&lt;bean id=&quot;sessionFactory&quot;
		class=&quot;org.springframework.orm.hibernate3.annotation.AnnotationSessionFactoryBean&quot;&gt;
		&lt;property name=&quot;dataSource&quot; ref=&quot;dataSource&quot; /&gt;
		&lt;property name=&quot;hibernateProperties&quot;&gt;
			&lt;props&gt;
				&lt;prop key=&quot;hibernate.dialect&quot;&gt;${hibernate.dialect}&lt;/prop&gt;
				&lt;prop key=&quot;hibernate.show_sql&quot;&gt;${hibernate.show_sql}&lt;/prop&gt;
				&lt;prop key=&quot;hibernate.format_sql&quot;&gt;${hibernate.format_sql}&lt;/prop&gt;
				&lt;prop key=&quot;hibernate.generate_statistics&quot;&gt;${hibernate.generate_statistics}&lt;/prop&gt;
				&lt;prop key=&quot;hibernate.max_fetch_depth&quot;&gt;${hibernate.max_fetch_depth}&lt;/prop&gt;
				&lt;prop key=&quot;hibernate.jdbc.fetch_size&quot;&gt;${hibernate.jdbc.fetch_size}&lt;/prop&gt;
				&lt;prop key=&quot;hibernate.jdbc.batch_size&quot;&gt;${hibernate.jdbc.batch_size}&lt;/prop&gt;
				&lt;prop key=&quot;hibernate.connection.autocommit&quot;&gt;${hibernate.connection.autocommit}&lt;/prop&gt;
				&lt;prop key=&quot;hibernate.cache.use_query_cache&quot;&gt;${hibernate.cache.use_query_cache}&lt;/prop&gt;
				&lt;prop key=&quot;hibernate.cache.provider_class&quot;&gt;${hibernate.cache.provider_class}&lt;/prop&gt;
				&lt;prop key=&quot;hibernate.cache.use_second_level_cache&quot;&gt;${hibernate.cache.use_second_level_cache}&lt;/prop&gt;
				&lt;prop key=&quot;hibernate.cglib.use_reflection_optimizer&quot;&gt;${hibernate.cglib.use_reflection_optimizer}&lt;/prop&gt;
			&lt;/props&gt;
		&lt;/property&gt;
		&lt;property name=&quot;packagesToScan&quot; value=&quot;com.linktel.linkRec.entity&quot; /&gt;
	&lt;/bean&gt;

	&lt;!-- 事务管理器配置,单数据源事务 --&gt;
	&lt;bean id=&quot;transactionManager&quot;
		class=&quot;org.springframework.orm.hibernate3.HibernateTransactionManager&quot;&gt;
		&lt;property name=&quot;sessionFactory&quot; ref=&quot;sessionFactory&quot; /&gt;
	&lt;/bean&gt;

	&lt;!-- 使用annotation 自动注册bean,并检查@Required,@Autowired的属性已被注入 --&gt;
	&lt;context:component-scan base-package=&quot;com.linktel.linkRec&quot; /&gt;

	&lt;!-- 使用annotation定义事务 --&gt;
	&lt;tx:annotation-driven transaction-manager=&quot;transactionManager&quot;
		proxy-target-class=&quot;true&quot; /&gt;

&lt;/beans&gt;
</pre>
<p> </p>
<p> 二，zk controller 注解配置，注意scope必须是prototype，因为composer是一个状态对象</p>
<p> </p>
<p> </p>
<pre name="code">@Controller
@Scope("prototype")
public class LoginController extends GenericAutowireComposer {
</pre>
<p> </p>
<p> </p>
<p> </p>
<p>三，页面中使用ctrl</p>
<p> </p>
<p>    首先要引入我自定义的el函数库,zktools工具将在下面讲到</p>
<p> </p>
<pre name="code">&lt;?taglib uri=&quot;http://www.zkoss.org/zktools/zktools&quot; prefix=&quot;z&quot; ?&gt;


</pre>
<p> </p>
<p> 然后在window apply中使用，其中z是prefix="z" 中的z,ctrl是zktools.jar中定义的一个el函数，这个和c标签很类  似， 'loginController'是【二】中LoginController类spring 受管理bean名字，通常我们喜欢使用</p>
<p>长长的类路径apply="com.linktel.linkRec.web.controller.LoginController"， </p>
<p>但这样创建的对象不是spring管理，你无法向LoginController类里注入service，当然你可以使用SpringUtil.getBean</p>
<p>获取bean事例，但就丧失使用annotation的好处了</p>
<p> </p>
<p> </p>
<pre name="code">&lt;window apply=&quot;${z:ctrl(&#39;loginController&#39;)}&quot; width=&quot;300px&quot;
</pre>
<p> </p>
<p> </p>
<p> </p>
<p> 完整使用</p>
<p> </p>
<p> </p>
<p> </p>
<pre name="code">&lt;?xml version=&quot;1.0&quot; encoding=&quot;utf-8&quot;?&gt;
&lt;?page  cacheable=&quot;false&quot; language=&quot;xul/html&quot; zscriptLanguage=&quot;Java&quot; contentType=&quot;text/html;charset=UTF-8&quot;?&gt;
&lt;?taglib uri=&quot;http://www.zkoss.org/dsp/web/core&quot; prefix=&quot;c&quot; ?&gt;
&lt;?taglib uri=&quot;http://www.zkoss.org/zktools/zktools&quot; prefix=&quot;z&quot; ?&gt;
&lt;div
	style=&quot;background: url(&#39;images/headerbg.png&#39;) repeat-x;  background-position:0 -53px;height:100%;&quot;
	xmlns:w=&quot;client&quot; xmlns:n=&quot;native&quot; xmlns=&quot;http://www.zkoss.org/2005/zul&quot;
	xmlns:xsi=&quot;http://www.w3.org/2001/XMLSchema-instance&quot;
	xsi:schemaLocation=&quot;http://www.zkoss.org/2005/zul http://www.zkoss.org/2005/zul/zul.xsd&quot;&gt;
	&lt;window apply=&quot;${z:ctrl(&#39;loginController&#39;)}&quot; width=&quot;300px&quot;
		height=&quot;140px&quot; onOK=&quot;Events.postEvent(&amp;quot;onClick&amp;quot;,btnLogin,null);&quot;
		mode=&quot;overlapped&quot; position=&quot;center,top&quot; style=&quot;margin-top:100px;&quot;&gt;</pre>
<p> </p>
<p> </p>
<p> </p>
<p> </p>
<p> </p>
<p> </p>
<p>四，获取spring bean的el函数</p>
<p>在【三】中的页面zul中，我们仅仅简单的使用了一个表达式${z:ctrl('loginController')}， 这个表达式由</p>
<p>zk框架解释，还记的我们在页面首部添加的一个指令吗？</p>
<p>&lt;?taglib uri=&quot;<a href="http://www.zkoss.org/zktools/zktools">http://www.zkoss.org/zktools/zktools</a>&quot; prefix=&quot;z&quot; ?&gt;</p>
<p>和jsp c标签类似， zk中的标签也是在tld文件中定义的，大部分的web容器都可以自动加载tld文件，</p>
<p>zk也是一样，但zk不会自动加载tld，它自动加载类路径下的一个metainfo/tld/config.xml文件，这是zk的约定</p>
<p> </p>
<p> <img src="http://dl.javaeye.com/upload/attachment/366444/74b62434-ed0e-3781-b3ed-b5357c5d1998.png" alt="" width="245" height="247"></p>
<p> </p>
<p> </p>
<p>在config.xml文件中我定义了如下内容，当然这些也是zk的约定，taglib-location告诉zk框架tld文件位置，<br>那么zk自动去找这个文件并解析</p>
<p> </p>
<pre name="code">&lt;?xml version=&quot;1.0&quot; encoding=&quot;UTF-8&quot;?&gt;
&lt;!-- 

 by sunflower
 
 --&gt;
&lt;config&gt;
	&lt;version&gt;
		&lt;version-class&gt;org.zkway.zktools.Version&lt;/version-class&gt;
		&lt;version-uid&gt;1.0&lt;/version-uid&gt;
	&lt;/version&gt;
	&lt;taglib&gt;
		&lt;taglib-uri&gt;http://www.zkoss.org/zktools/zktools&lt;/taglib-uri&gt;
		&lt;taglib-location&gt;/web/WEB-INF/tld/zktools/zktools.tld&lt;/taglib-location&gt;
	&lt;/taglib&gt;
&lt;/config&gt;
</pre>
<p> </p>
<p>那么我们看看zktools.tld里如何定义的</p>
<p> </p>
<pre name="code"> &lt;!--  --&gt;
	&lt;!-- 获取spring 管理bean --&gt;
	&lt;!--  --&gt;
	&lt;function&gt;
		&lt;name&gt;ctrl&lt;/name&gt;
		&lt;function-class&gt;org.zkway.zktools.ZkCommonTools&lt;/function-class&gt;
		&lt;function-signature&gt;java.lang.Object getController(java.lang.String)&lt;/function-signature&gt;
		&lt;description&gt;获取spring 管理controller&lt;/description&gt;
	&lt;/function&gt;
	
	&lt;function&gt;
		&lt;name&gt;comp&lt;/name&gt;
		&lt;function-class&gt;org.zkway.zktools.ZkCommonTools&lt;/function-class&gt;
		&lt;function-signature&gt;java.lang.Object getComponent(java.lang.String)&lt;/function-signature&gt;
		&lt;description&gt;获取spring 管理组件对象&lt;/description&gt;
	&lt;/function&gt;
		
	&lt;function&gt;
		&lt;name&gt;bean&lt;/name&gt;
		&lt;function-class&gt;org.zkway.zktools.ZkCommonTools&lt;/function-class&gt;
		&lt;function-signature&gt;java.lang.Object getBean(java.lang.String)&lt;/function-signature&gt;
		&lt;description&gt;获取spring 管理bean对象&lt;/description&gt;
	&lt;/function&gt;
	
	&lt;!--  --&gt;
	&lt;!-- 工具函数 --&gt;
	&lt;!--  --&gt;
	&lt;function&gt;
		&lt;name&gt;size&lt;/name&gt;
		&lt;function-class&gt;org.zkway.zktools.ZkCommonTools&lt;/function-class&gt;
		&lt;function-signature&gt;int size(java.lang.Object)&lt;/function-signature&gt;
		&lt;description&gt;获取一个对象的大小&lt;/description&gt;
	&lt;/function&gt;
	&lt;function&gt;
		&lt;name&gt;length&lt;/name&gt;
		&lt;function-class&gt;org.zkway.zktools.ZkCommonTools&lt;/function-class&gt;
		&lt;function-signature&gt;int size(java.lang.Object)&lt;/function-signature&gt;
		&lt;description&gt;获取一个对象的大小&lt;/description&gt;
	&lt;/function&gt;
	&lt;function&gt;
		&lt;name&gt;empty&lt;/name&gt;
		&lt;function-class&gt;org.zkway.zktools.ZkCommonTools&lt;/function-class&gt;
		&lt;function-signature&gt;boolean isEmpty(java.lang.Object)&lt;/function-signature&gt;
		&lt;description&gt;判断一个对象是否是空对象&lt;/description&gt;
	&lt;/function&gt;
	
	&lt;!--  --&gt;
	&lt;!-- 日期工具函数 --&gt;
	&lt;!--  --&gt;
    &lt;function&gt;
		&lt;name&gt;now&lt;/name&gt;
		&lt;function-class&gt;org.zkway.zktools.ZkCommonTools&lt;/function-class&gt;
		&lt;function-signature&gt;java.util.Date currentTime()&lt;/function-signature&gt;
		&lt;description&gt;获取当前时间&lt;/description&gt;
	&lt;/function&gt;
	&lt;function&gt;
		&lt;name&gt;today&lt;/name&gt;
		&lt;function-class&gt;org.zkway.zktools.ZkCommonTools&lt;/function-class&gt;
		&lt;function-signature&gt;java.util.Date today(int, int, int)&lt;/function-signature&gt;
		&lt;description&gt;获取今天的日期时间，可以指定今天时分秒,-1表示不设置&lt;/description&gt;
	&lt;/function&gt;
	&lt;function&gt;
		&lt;name&gt;firstDayOfMonth&lt;/name&gt;
		&lt;function-class&gt;org.zkway.zktools.ZkCommonTools&lt;/function-class&gt;
		&lt;function-signature&gt;java.util.Date firstDayOfMonth()&lt;/function-signature&gt;
		&lt;description&gt;获取当前月的第一天&lt;/description&gt;
	&lt;/function&gt;
	&lt;function&gt;
		&lt;name&gt;lastDayOfMonth&lt;/name&gt;
		&lt;function-class&gt;org.zkway.zktools.ZkCommonTools&lt;/function-class&gt;
		&lt;function-signature&gt;java.util.Date lastDayOfMonth()&lt;/function-signature&gt;
		&lt;description&gt;获取当前月的最后一天&lt;/description&gt;
	&lt;/function&gt;
	
	&lt;function&gt;
		&lt;name&gt;fmtDate&lt;/name&gt;
		&lt;function-class&gt;org.zkway.zktools.ZkCommonTools&lt;/function-class&gt;
		&lt;function-signature&gt;java.lang.String formatDate(java.lang.String)&lt;/function-signature&gt;
		&lt;description&gt;根据参数指定的模式，格式化当前日期&lt;/description&gt;
	&lt;/function&gt;

 
</pre>
<p> </p>
<p> </p>
<p>1)&lt;name&gt;定义了el函数的名字，例如${z:ctrl(&#39;loginController&#39;)}中的ctrl对应以下中的ctrl</p>
<p> </p>
<pre name="code">&lt;function&gt;
   &lt;name&gt;ctrl&lt;/name&gt;
   &lt;function-class&gt;org.zkway.zktools.ZkCommonTools&lt;/function-class&gt;
   &lt;function-signature&gt;java.lang.Object getController(java.lang.String)&lt;/function-signature&gt;
   &lt;description&gt;获取spring 管理controller&lt;/description&gt;
&lt;/function&gt;

</pre>
<p> <br>2)function-signature函数指定了函数签名，具体哪个函数</p>
<p>3)function-class为function-signature函数所属的类</p>
<p>4）description为函数描述</p>
<p> </p>
<p>那么这个函数必须符合什么规范呢：public,static 可以不是final</p>
<p>  </p>
<pre name="code">public class ZkCommonTools {

	/**
	 * 获取spring管理的控制器对象，
	 * &lt;p&gt;
	 * 
	 * 支持的类型为:{@link Composer},{@link Component},{@link FullComposer}
	 * 
	 * @param beanName
	 * @return
	 */
	public static final Object getController(String beanName) {
		Object controller = SpringUtil.getBean(beanName);
		if (controller instanceof Composer)
			return controller;
		else if (controller instanceof FullComposer)
			return controller;
		else if (controller instanceof Component)
			return controller;
		return null;
	}
	</pre>
<p> </p>
<p> </p>
<p> </p>
<p> </p>
<p> </p>
<p> </p>
<p> </p>
<p>五，总结</p>
<p> </p>
<p>首先我们在spring的配置中开启自动扫描</p>
<p> </p>
<pre name="code">&lt;!-- 使用annotation 自动注册bean,并检查@Required,@Autowired的属性已被注入 --&gt;
	&lt;context:component-scan base-package=&quot;com.linktel.linkRec&quot; /&gt;
</pre>
<p> </p>
<p>将controller注册为spring bean</p>
<p> </p>
<pre name="code">@Controller
@Scope("prototype")
public class LoginController extends GenericAutowireComposer {
</pre>
<p> </p>
<p>然后我们需要解决的问题是如何获取bean，如果直接apply或use一个类名，</p>
<p>那么类实例由zk创建，我们无法享受spring管理bean给我们带来的任何好处。</p>
<p> </p>
<p>也许会这么做，就是在forward到一个zul页面前，先将spring bean放在可用作用域（execution,request,session，ThreadLocal），</p>
<p>然后目标页面直接使用el表达式获取${reqeustScope['loginController']}，但实际项目中，下一站到底是什么,大多数情况我们是无法预知的，</p>
<p>只有到达目标你才能知道结果，所以预先读取bean很多情况下有他的局限性。</p>
<p> </p>
<p>在本文中我定义了一个el函数ctrl，这个函数的参数为spring bean名字，el函数的返回值是spring管理的bean事例</p>
<p> </p>
<p> </p>
<p>大致流程如下</p>
<p> </p>
<p>spring scan bean ---&gt; register controller bean ---&gt; el function 获取 spring bean --&gt;zk interpret -</p>
<p> </p>
<p> </p>
<p> </p>
<p> </p>
<p> </p>
<p> </p>
<p> </p>
          
  <br><br>
  <ul>
    本文附件下载:
    
      <li><a href="http://dl.javaeye.com/topics/download/88bbc8ba-b8dc-3616-af7c-acad669f9a59">zk_spring_hibernate零配置.part3.rar</a> (5.2 MB)</li>
    
      <li><a href="http://dl.javaeye.com/topics/download/039df5fa-993a-3152-bddf-4216d2455b23">zk_spring_hibernate零配置.part1.rar</a> (7 MB)</li>
    
      <li><a href="http://dl.javaeye.com/topics/download/158fa228-091e-3671-b775-52077cab26fa">zk_spring_hibernate零配置.part2.rar</a> (7 MB)</li>
    
      <li><a href="http://dl.javaeye.com/topics/download/c1a0cb02-1725-3aad-8d58-fe0673f64124">zktools函数库.rar</a> (4.4 KB)</li>
    
      <li><a href="http://dl.javaeye.com/topics/download/625386a0-f432-35f7-9ac1-6bd3ce8d84e0">zktools源码.rar</a> (2.4 MB)</li>
    
  </ul>

          <br><br>
          作者: <a href="http://sun4love.javaeye.com">sun4love</a> 
          <br>
          声明: 本文系JavaEye网站发布的原创文章，未经作者书面许可，严禁任何网站转载本文，否则必将追究法律责任！
          <br><br>
          <span style="color:red">
            <a href="http://www.javaeye.com/topic/840871" style="color:red">已有 <strong>0</strong> 人发表回复，猛击-&gt;&gt;<strong>这里</strong>&lt;&lt;-参与讨论</a>
          </span>
          <br><br><br>
<span style="color:#e28822">JavaEye推荐</span>
<br>
<ul><li><a href="http://www.iteye.com/clicks/433"><span style="color:red;font-weight:bold">—软件人才免语言低担保 赴美带薪读研！— </span></a></li></ul>
<br><br><br>