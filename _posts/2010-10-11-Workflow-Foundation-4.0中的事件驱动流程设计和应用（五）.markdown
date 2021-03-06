---
layout: post
title:  "Workflow Foundation 4.0中的事件驱动流程设计和应用（五）"
date:   2010-10-11 19:46:00
author: 陈希章
categories: program
---

## Workflow Foundation 4.0中的事件驱动流程设计和应用（五）
### by 陈希章
### at 2010-10-11 19:46:00
### original <http://www.cnblogs.com/chenxizhang/archive/2010/10/11/1848167.html>

<p><p>之前，我通过4篇文章介绍了在WF4中开发基于事件的工作流的范例。请参考下面的链接。 <p><a href="http://www.cnblogs.com/chenxizhang/archive/2010/10/07/1845104.html">Workflow Foundation 4.0中的事件驱动流程设计和应用（一）</a> <p><a href="http://www.cnblogs.com/chenxizhang/archive/2010/10/07/1845136.html">Workflow Foundation 4.0中的事件驱动流程设计和应用（二）</a> <p><a href="http://www.cnblogs.com/chenxizhang/archive/2010/10/07/1845164.html">Workflow Foundation 4.0中的事件驱动流程设计和应用（三）</a> <p><a title="Workflow Foundation 4.0中的事件驱动流程设计和应用（四）" href="http://www.cnblogs.com/chenxizhang/archive/2010/10/10/1847465.html">Workflow Foundation 4.0中的事件驱动流程设计和应用（四）</a> <p> </p> <p>这一篇是这个系列的最后一篇，介绍如何通过配置文件，而不是代码的方式启动宿主。这在现实工作中是相当有用的，请大家参考下面的实例。</p> <p>【注意】有朋友也问到单独用数据库存储业务方面的数据，那是没有错的。一般可以通过自定义的Activity去完成这些操作，都是标准的ADO.NET的数据访问操作。这里就不做展开了。</p> <p> </p> <p>这个案例的最终代码范例，请通过 <a href="http://files.cnblogs.com/chenxizhang/WF4EventDrivenSolution-final.rar">这里</a> 下载</p> <h1>1.修改之前的Host代码</h1><pre><span>using</span> System;
<span>using</span> System.Collections.Generic;
<span>using</span> System.Linq;
<span>using</span> System.Text;
<span>using</span> System.ServiceModel;
<span>using</span> System.Activities;
<span>using</span> System.ServiceModel.Activities;
<span>using</span> System.ServiceModel.Description;

<span>using</span> System.Activities.DurableInstancing;
<span>using</span> System.Runtime.DurableInstancing;
<span>using</span> System.Activities.Persistence;
<span>using</span> System.ServiceModel.Activities.Description;
<span>using</span> System.Xml.Linq;

<span>namespace</span> Host
{
    <span>class</span> Program
    {
        <span>static</span> <span>void</span> Main(<span>string</span>[] args)
        {
            var host = <span>new</span> WorkflowServiceHost(
                <span>new</span> DocumentReviewLib.DocumentReviewWorkflow(),
                <span>new</span> Uri(<span>"http://localhost:8080/DRS"</span>));

            host.AddDefaultEndpoints();<span>//这个方法是添加了一些标准的端点</span>

            host.Description.Behaviors.Add(
                <span>new</span> ServiceMetadataBehavior() { HttpGetEnabled = <span>true</span> });

            var store = <span>new</span> SqlWorkflowInstanceStore(<span>"server=(local)\\sqlexpress;database=WF4;integrated security=true"</span>);

            host.UnknownMessageReceived += (o, e) =&gt;
            {
                Console.WriteLine(<span>"\n"</span> + e.Message + <span>"\n"</span>);
            };


            host.Description.Behaviors.Add(
                <span>new</span> WorkflowIdleBehavior()
                {
                    TimeToPersist = TimeSpan.FromSeconds(0)
                });

            XNamespace xNS = XNamespace.Get(<span>"http://xizhang.com/DocumentReview"</span>);
            store.Promote(<span>"DocumentReview"</span>,
                <span>new</span> List&lt;XName&gt;() { xNS.GetName(<span>"TicketId"</span>) },
                <span>null</span>);


            host.WorkflowExtensions.Add(<span>new</span> Extensions.MyInstanceStoreParticpant());


            host.DurableInstancingOptions.InstanceStore = store;
            host.Open();

            var common = <span>new</span> ServiceHost(
                <span>typeof</span>(CommonService),
                <span>new</span> Uri(<span>"http://localhost:8080/Common"</span>));

            common.AddServiceEndpoint(
                <span>typeof</span>(ICommonService).FullName,
                <span>new</span> BasicHttpBinding(),
                <span>""</span>);

            common.Open();


            Console.WriteLine(<span>"Server is ready."</span>);
            Console.Read();

        }


    }


    [ServiceContract]
    <span>public</span> <span>interface</span> ICommonService
    {
        [OperationContract]
        <span>int</span>[] GetTicketIds();
    }


    <span>public</span> <span>class</span> CommonService : ICommonService
    {

        <span>public</span> <span>int</span>[] GetTicketIds()
        {
            var ctx = <span>new</span> InstanceStoreDataContext();
            <span>return</span> ctx.DocumentReviewTasks.Select(r =&gt; (<span>int</span>)r.TicketId).ToArray();
        }
    }

}
</pre>
<p>

</p>
<h1>2. 修改之后的Host代码（请大家比较一下有何区别）</h1><pre><span>using</span> System;
<span>using</span> System.Collections.Generic;
<span>using</span> System.Linq;
<span>using</span> System.Text;
<span>using</span> System.ServiceModel;
<span>using</span> System.Activities;
<span>using</span> System.ServiceModel.Activities;
<span>using</span> System.ServiceModel.Description;

<span>using</span> System.Activities.DurableInstancing;
<span>using</span> System.Runtime.DurableInstancing;
<span>using</span> System.Activities.Persistence;
<span>using</span> System.ServiceModel.Activities.Description;
<span>using</span> System.Xml.Linq;

<span>namespace</span> Host
{
    <span>class</span> Program
    {
        <span>static</span> <span>void</span> Main(<span>string</span>[] args)
        {
            var host = <span>new</span> WorkflowServiceHost(
                <span>new</span> WorkflowService() { 
                    ConfigurationName = <span>"DocumentReviewLib.DocumentReviewWorkflow"</span>,
                    Body = <span>new</span> DocumentReviewLib.DocumentReviewWorkflow()
                });


            <span>//这里可以通过进一步的Behavior定制来简化。此处略            </span>
            XNamespace xNS = XNamespace.Get(<span>"http://xizhang.com/DocumentReview"</span>);
            var store = (SqlWorkflowInstanceStoreBehavior)host.Description.Behaviors.FirstOrDefault(b =&gt; b.GetType() == <span>typeof</span>(SqlWorkflowInstanceStoreBehavior));

            store.Promote(<span>"DocumentReview"</span>,
                <span>new</span> List&lt;XName&gt;() { xNS.GetName(<span>"TicketId"</span>) },
                <span>null</span>);



            <span>//这里可以通过进一步的Behavior定制来简化。此处略</span>
            host.WorkflowExtensions.Add(<span>new</span> Extensions.MyInstanceStoreParticpant());

            host.Open();

            var common = <span>new</span> ServiceHost(<span>typeof</span>(CommonService));

            common.Open();


            Console.WriteLine(<span>"Server is ready."</span>);
            Console.Read();

        }


    }


    [ServiceContract]
    <span>public</span> <span>interface</span> ICommonService
    {
        [OperationContract]
        <span>int</span>[] GetTicketIds();
    }


    <span>public</span> <span>class</span> CommonService : ICommonService
    {

        <span>public</span> <span>int</span>[] GetTicketIds()
        {
            var ctx = <span>new</span> InstanceStoreDataContext();
            <span>return</span> ctx.DocumentReviewTasks.Select(r =&gt; (<span>int</span>)r.TicketId).ToArray();
        }
    }

}
</pre>


<h1>3.添加的app.config文件内容</h1><pre>&lt;?xml version=<span>"1.0"</span> encoding=<span>"utf-8"</span> ?&gt;
&lt;configuration&gt;
    &lt;configSections&gt;
    &lt;/configSections&gt;
    &lt;connectionStrings&gt;
        &lt;add name=<span>"Host.Properties.Settings.WF4ConnectionString"</span> connectionString=<span>"Data Source=.\sqlexpress;Initial Catalog=WF4;Integrated Security=True"</span>
            providerName=<span>"System.Data.SqlClient"</span> /&gt;
    &lt;/connectionStrings&gt;
  &lt;system.serviceModel&gt;

    
    
    &lt;behaviors&gt;
      &lt;serviceBehaviors&gt;
        &lt;behavior name=<span>"WorkflowService"</span>&gt;
          &lt;sqlWorkflowInstanceStore connectionStringName=<span>"Host.Properties.Settings.WF4ConnectionString"</span>/&gt;
          &lt;workflowIdle timeToPersist=<span>"0"</span> timeToUnload=<span>"0"</span>/&gt;
          &lt;serviceMetadata httpGetEnabled=<span>"true"</span>/&gt;
        &lt;/behavior&gt;
      &lt;/serviceBehaviors&gt;
    &lt;/behaviors&gt;
    
    &lt;services&gt;
      &lt;service name=<span>"DocumentReviewLib.DocumentReviewWorkflow"</span> behaviorConfiguration=<span>"WorkflowService"</span>&gt;
        &lt;host&gt;
          &lt;baseAddresses&gt;
            &lt;add baseAddress=<span>"http://localhost:8080/DRS"</span>/&gt;
          &lt;/baseAddresses&gt;
        &lt;/host&gt;
        &lt;endpoint contract=<span>"IDocumentReview"</span> address=<span>""</span> binding=<span>"basicHttpBinding"</span>&gt;&lt;/endpoint&gt;
      &lt;/service&gt;

      &lt;service name=<span>"Host.CommonService"</span>&gt;
        &lt;host&gt;
          &lt;baseAddresses&gt;
            &lt;add baseAddress=<span>"http://localhost:8080/Common"</span>/&gt;
          &lt;/baseAddresses&gt;
        &lt;/host&gt;

        &lt;endpoint contract=<span>"Host.ICommonService"</span> binding=<span>"basicHttpBinding"</span> address=<span>""</span>&gt;&lt;/endpoint&gt;
      &lt;/service&gt;
    &lt;/services&gt;
    
    
    
  &lt;/system.serviceModel&gt;
  
&lt;/configuration&gt;</pre><pre> </pre><pre>这个案例的最终代码范例，请通过 <a href="http://files.cnblogs.com/chenxizhang/WF4EventDrivenSolution-final.rar">这里</a> 下载</pre><pre> </pre><pre><strong><font face="Verdana"></font></strong> </pre><img src="http://www.cnblogs.com/chenxizhang/aggbug/1848167.html?type=0" width="1" height="1" alt=""><p>作者: <a href="http://www.cnblogs.com/chenxizhang/">陈希章</a> 发表于 2010-10-11 19:46 <a href="http://www.cnblogs.com/chenxizhang/archive/2010/10/11/1848167.html">原文链接</a></p><p>评论: 3　<a href="http://www.cnblogs.com/chenxizhang/archive/2010/10/11/1848167.html#pagedcomment">查看评论</a>　<a href="http://www.cnblogs.com/chenxizhang/archive/2010/10/11/1848167.html#commentform">发表评论</a></p><hr><p>最新新闻：<br>· <a href="http://news.cnblogs.com/n/77345/">Valve宣布《Dota 2》</a><span style="color:gray">(2010-10-14 23:08)</span><br>· <a href="http://news.cnblogs.com/n/77343/">苹果向获救智利矿工捐赠iPod</a><span style="color:gray">(2010-10-14 22:54)</span><br>· <a href="http://news.cnblogs.com/n/77342/">全能的操作系统——Intel的Meego</a><span style="color:gray">(2010-10-14 22:34)</span><br>· <a href="http://news.cnblogs.com/n/77339/">谷歌与出版商举行谈判 商讨内容收费事宜</a><span style="color:gray">(2010-10-14 21:57)</span><br>· <a href="http://news.cnblogs.com/n/77338/">拯救自己！最新Windows Phone 7广告欣赏</a><span style="color:gray">(2010-10-14 21:48)</span><br></p><p>编辑推荐：<a href="http://news.cnblogs.com/n/77257/">CodeRun Studio：基于Web的IDE开发工具</a><br></p><p>网站导航：<a href="http://www.cnblogs.com">博客园首页</a>  <a href="http://home.cnblogs.com/">我的园子</a>  <a href="http://news.cnblogs.com">新闻</a>  <a href="http://home.cnblogs.com/ing/">闪存</a>  <a href="http://home.cnblogs.com/group/">小组</a>  <a href="http://space.cnblogs.com/q/">博问</a>  <a href="http://kb.cnblogs.com">知识库</a></p></p></p></p></p></p></p>