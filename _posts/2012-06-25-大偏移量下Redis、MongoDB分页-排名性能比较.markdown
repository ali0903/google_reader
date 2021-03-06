---
layout: post
title:  "大偏移量下Redis、MongoDB分页/排名性能比较"
date:   2012-06-25 23:19:22
author: nosqlfan
categories: program
---

## 大偏移量下Redis、MongoDB分页/排名性能比较
### by nosqlfan
### at 2012-06-25 23:19:22
### original <http://item.feedsky.com/~feedsky/nosqlfan/~8149226/676627738/6253001/1/item.html>

<p>题目其实并不太准确，因为数据库并不会提供<span><a href="http://blog.nosqlfan.com/tags/%e5%88%86%e9%a1%b5" title="查看 分页 的全部文章">分页</a></span>、排名等功能，提供的只是数据的存取，分页排名这些都是我们基于数据库的实用案例而已。然而无论是<span><a href="http://blog.nosqlfan.com/tags/redis" title="查看 Redis 的全部文章">Redis</a></span>还是<span><a href="http://blog.nosqlfan.com/tags/mongodb" title="查看 MongoDB 的全部文章">MongoDB</a></span>，通常都有一些常规的做分页和排名的方法。本文就通过一些测试数据来向大家介绍Redis和MongoDB（以及传统关系型数据库）在这方面的性能差别。</p>
<h3>分页</h3>
<p>首先我们来做一个分页，在MongoDB中示例数据如下所未：</p>
<pre>db.scores.find();
{lid: ObjectId("4fe506dabb2bfa742d000001"), score: 1, name: 'user_1'}
{lid: ObjectId("4fe506dabb2bfa742d000001"), score: 2, name: 'user_2'}
{lid: ObjectId("4fe506dabb2bfa742d000001"), score: 3, name: 'user_3'}
{lid: ObjectId("4fe506dabb2bfa742d000001"), score: 4, name: 'user_4'}</pre>
<p>其中lid字段用于区分不同的纬度，主要用在筛选上，在测试collection中，一共有五个不同的lid值，每一个对应1,200,000条数据，一共6,000,000条数据。索引在lid 和 score上。（下面的查询能使用到索引）</p>
<p>然后我们进行下面的性能测试：</p>
<pre>collection = Mongo::Connection.new.db(&#39;test&#39;).collection(&#39;scores&#39;)
Benchmark.bmbm do |x|
  x.report(&quot;mongo small&quot;) do
    100.times do |i|
      collection.find({:lid =&gt; lids.sample}, {:fields =&gt; {:_id =&gt; false, :score =&gt; true, :user =&gt; true}}).sort({:score =&gt; -1}).limit(20).skip(i * 20).to_a
    end
  end
  x.report(&quot;mongo medium&quot;) do
    100.times do |i|
      collection.find({:lid =&gt; lids.sample}, {:fields =&gt; {:_id =&gt; false, :score =&gt; true, :user =&gt; true}}).sort({:score =&gt; -1}).limit(20).skip(i * 1000).to_a
    end
  end
  x.report(&quot;mongo large&quot;) do
    100.times do |i|
      collection.find({:lid =&gt; lids.sample}, {:fields =&gt; {:_id =&gt; false, :score =&gt; true, :user =&gt; true}}).sort({:score =&gt; -1}).limit(20).skip(i * 10000).to_a
    end
  end
end</pre>
<p>上面分别对skip条数比较小，中等大小和非常大三种情况进行了测试。而limit指定获取的数据都一样是20条。这三种情况下的测试结果分别是：0.6 秒， 17 秒，173 秒。</p>
<p>我们可以看到，对MongoDB来说，skip的大小严重影响性能，应该严格避免特别大的skip操作。</p>
<p>下面我们将类似的数据用Redis的Sorted Sets进行存储。并进行相应的性能测试</p>
<pre>redis = Redis.new(:driver =&gt; :hiredis)
Benchmark.bmbm do |x|
  x.report(&quot;redis small&quot;) do
    100.times do |i|
      start = i * 20
      redis.zrevrange(lids.sample, start, start + 20, :with_scores =&gt; true)
    end
  end
  x.report(&quot;redis medium&quot;) do
    100.times do |i|
      start = i * 1000
      redis.zrevrange(lids.sample, start, start + 20, :with_scores =&gt; true)
    end
  end
  x.report(&quot;redis large&quot;) do
    100.times do |i|
      start = i * 10000
      redis.zrevrange(lids.sample, start, start + 20, :with_scores =&gt; true)
    end
  end</pre>
<p>这里skip的值和上面MongoDB中是一样的，那么Redis的表现如何呢。这三种情况下的测试结果分别是：0.028 秒， 0.025 秒， 0.028 秒。</p>
<p>采用类似于MongoDB的数据结构存储在PostgreSQL中并进行相同的测试，其结果比MongoDB还要差一点。具体结果如下：</p>
<blockquote><p>mongo small   <span style="color:#339966"><strong>0.6</strong></span><br>
mongo medium   <span style="color:#339966"><strong>17</strong></span><br>
mongo large   <span style="color:#339966"><strong>173</strong></span><br>
redis small   <span style="color:#339966"><strong>0.028</strong></span><br>
redis medium   <span style="color:#339966"><strong>0.025</strong></span><br>
redis large   <span style="color:#339966"><strong>0.028</strong></span><br>
pg small   <strong><span style="color:#339966">1</span></strong><br>
pg medium   <span style="color:#339966"><strong>122</strong></span><br>
pg large   <span style="color:#339966"><strong>650 </strong></span></p></blockquote>
<h3>排名</h3>
<p>排名功能与分页功能类似，不同的是排名是通过计算大于某个值的条数来做的。</p>
<p>比如：</p>
<pre>//sql
select count(*) from scores where lid = $1 and score &gt; $2

//mongo
db.scores.find({lid: lid, score: {$gt: score}}).count()</pre>
<p>由于排名和分页实现原理上类似，所以结果实际上差不多。测试结果如下：</p>
<blockquote><p>mongo top rank   <span style="color:#339966"><strong>1.155847</strong></span><br>
mongo average    <span style="color:#339966"><strong>22.291007</strong></span></p>
<p>redis top rank   <span style="color:#339966"><strong>0.169442</strong></span><br>
redis average    <span style="color:#339966"><strong>0.162205</strong></span></p>
<p>pg top rank      <span style="color:#339966"><strong>0.714144</strong></span><br>
pg average       <span style="color:#339966"><strong>21.771570</strong></span></p></blockquote>
<h3>结论</h3>
<p>上面做了对比，那么本文要说一个什么问题呢？</p>
<p>首先，在MongoDB中，尽量避免进行比较大的skip操作，比如在分页中，如果你能知道需要获取数据的上一条score是多少，那么可能能够用下面的方法来获取你要的数据，而不是通过一次很大的skip操作。</p>
<pre>  db.scores.find({lid: lid, score: {$lt: last_score}}).sort({score: -1}).limit(20)</pre>
<p>另外，如果你需要进行比较大的skip操作或者count比较大的数量，那么可以考虑采用Redis的Sorted Sets来做。</p>
<h3>后记</h3>
<p>本文在微博上引起了一些技术朋友的讨论，对于对比的问题这里做一个说明。</p>
<p>我们知道，Redis是内存数据库，而MongoDB不是，所以有朋友质疑这里的对比是否只是内存与磁盘的对比。实际上这一说法不无道理，上面的测试数据出自原作者文章，其文章也并未提及MongoDB是否都在内存中。根据我个人的实验结果，当数据全部能够在内存中时，确实不会出现如本文中所说的MongoDB性能严重差异。但是，随着skip的变大，操作时间还是在显著变长，而Redis的Sorted Sets则相对稳定。</p>
<p>同时也欢迎更多实验对比数据和原理分析的讨论。感谢大家。</p>
<p>来源：<a href="http://openmymind.net/Paging-And-Ranking-With-Large-Offsets-MongoDB-vs-Redis-vs-Postgresql/">openmymind.net</a>
<div style="margin-top:20px;margin-left:70px;line-height:24px;border:1px solid #ccc;text-align:center;width:545px;background:#fff">
<div style="font-size:16px;font-family:Verdana;background:#d20;color:#fff;float:left;border-radius:10px 0 10px 0;padding:3px 12px 4px;line-height:32px;margin-top:14px">42区 VPS</div>
<div>
42qu.com 云主机 , 卖给创业的你 。 <a href="http://vps.42qu.com/by/iammutex/rss" style="text-decoration:none;background:none;color:#02d">点击这里 , 查看详情</a>
</div>
</div>
<p style="margin:0;padding:0;height:1px;overflow:hidden">
    <a href="http://www.wumii.com/widget/relatedItems.htm" style="border:0"><img src="http://static.wumii.com/images/pixel.png" alt="无觅相关文章插件，快速提升流量" style="border:0;padding:0;margin:0"></a></p>
<table cellspacing="0" cellpadding="2" border="0" width="100%" style="clear:both">
    
    <tr>
        <td><b><font size="-1" style="display:block!important;padding:20px 0 5px!important">相关文章：</font></b></td>
    </tr>
    
            <tr>
                <td style="margin:0!important;padding:0!important;line-height:20px!important">
                    <img border="0" src="http://static.wumii.cn/images/widget/widget_solidPoint.gif">
                    <a style="text-decoration:none!important" href="http://app.wumii.com/ext/redirect?url=http%3A%2F%2Fblog.nosqlfan.com%2Fhtml%2F1466.html&amp;from=http%3A%2F%2Fblog.nosqlfan.com%2Fhtml%2F4056.html">
                        <font size="-1" color="#333333" style="line-height:1.65em;font-size:14px!important">foursquare 的数据分析系统（Hadoop+Hive+Redis+MongoDB）</font>
                    </a>
                </td>
            </tr>
            <tr>
                <td style="margin:0!important;padding:0!important;line-height:20px!important">
                    <img border="0" src="http://static.wumii.cn/images/widget/widget_solidPoint.gif">
                    <a style="text-decoration:none!important" href="http://app.wumii.com/ext/redirect?url=http%3A%2F%2Fblog.nosqlfan.com%2Fhtml%2F2460.html&amp;from=http%3A%2F%2Fblog.nosqlfan.com%2Fhtml%2F4056.html">
                        <font size="-1" color="#333333" style="line-height:1.65em;font-size:14px!important">Bump的Redis应用经验</font>
                    </a>
                </td>
            </tr>
            <tr>
                <td style="margin:0!important;padding:0!important;line-height:20px!important">
                    <img border="0" src="http://static.wumii.cn/images/widget/widget_solidPoint.gif">
                    <a style="text-decoration:none!important" href="http://app.wumii.com/ext/redirect?url=http%3A%2F%2Fblog.nosqlfan.com%2Fhtml%2F538.html&amp;from=http%3A%2F%2Fblog.nosqlfan.com%2Fhtml%2F4056.html">
                        <font size="-1" color="#333333" style="line-height:1.65em;font-size:14px!important">Redis并发性能测试benchmark</font>
                    </a>
                </td>
            </tr>
            <tr>
                <td style="margin:0!important;padding:0!important;line-height:20px!important">
                    <img border="0" src="http://static.wumii.cn/images/widget/widget_solidPoint.gif">
                    <a style="text-decoration:none!important" href="http://app.wumii.com/ext/redirect?url=http%3A%2F%2Fblog.nosqlfan.com%2Fhtml%2F1845.html&amp;from=http%3A%2F%2Fblog.nosqlfan.com%2Fhtml%2F4056.html">
                        <font size="-1" color="#333333" style="line-height:1.65em;font-size:14px!important">Cassandra、MongoDB、CouchDB、Redis、Riak、HBase比较</font>
                    </a>
                </td>
            </tr>
            <tr>
                <td style="margin:0!important;padding:0!important;line-height:20px!important">
                    <img border="0" src="http://static.wumii.cn/images/widget/widget_solidPoint.gif">
                    <a style="text-decoration:none!important" href="http://app.wumii.com/ext/redirect?url=http%3A%2F%2Fblog.nosqlfan.com%2Fhtml%2F674.html&amp;from=http%3A%2F%2Fblog.nosqlfan.com%2Fhtml%2F4056.html">
                        <font size="-1" color="#333333" style="line-height:1.65em;font-size:14px!important">Cinderella 提供对MongoDB、Redis的支持</font>
                    </a>
                </td>
            </tr>
    
    <tr>
        <td align="right">
            <a style="text-decoration:none!important" href="http://www.wumii.com/widget/relatedItems" title="无觅相关文章插件">
                <font size="-1" color="#bbbbbb" style="display:block!important;font-family:arial!important;padding:5px 0!important;font-size:12px!important;color:#bbb!important">无觅</font>
            </a>
        </td>
    </tr>
</table><img src="http://www1.feedsky.com/t1/676627738/nosqlfan/feedsky/s.gif?r=http://item.feedsky.com/~feedsky/nosqlfan/~8149226/676627738/6253001/1/item.html" border="0" height="0" width="0"></p>