---
layout: post
title:  "MapReduce的模式、算法和用例"
date:   2013-02-05 09:48:12
author: nosqlfan
categories: program
---

## MapReduce的模式、算法和用例
### by nosqlfan
### at 2013-02-05 09:48:12
### original <http://blog.nosqlfan.com/html/4179.html>

<p>本文英文原文发表于知名技术博客《<a href="http://highlyscalable.wordpress.com/2012/09/18/distributed-algorithms-in-nosql-databases/">Highly Scalable Blog</a>》，由@<a href="http://weibo.com/juliashine">juliashine</a> 进行翻译投稿。感谢译者的共享精神！</p>
<p>译者介绍：Juliashine是多年抓娃工程师，现工作方向是海量数据处理与分析，关注Hadoop与NoSQL生态体系。</p>
<p>英文原文：《<a href="http://highlyscalable.wordpress.com/2012/02/01/mapreduce-patterns/">MapReduce Patterns, Algorithms, and Use Cases</a>》</p>
<p>译文地址：《<a href="http://yangguan.org/mapreduce-patterns-algorithms-and-use-cases/">MapReduce的模式、算法和用例</a>》</p>
<p>在这篇文章里总结了几种网上或者论文中常见的MapReduce模式和算法，并系统化的解释了这些技术的不同之处。所有描述性的文字和代码都使用了标准hadoop的MapReduce模型，包括Mappers, Reduces, Combiners, Partitioners,和 sorting。如下图所示：</p>
<div><img title="map-reduce-pattern-1" src="http://pic.yupoo.com/iammutex/CCpfPnwE/b9KDn.png" alt="" width="602" height="618"></div>
<h3>基本MapReduce模式</h3>
<h4>计数与求和</h4>
<p><strong>问题陈述:</strong> 有许多文档，每个文档都有一些字段组成。需要计算出每个字段在所有文档中的出现次数或者这些字段的其他什么统计值。例如，给定一个log文件，其中的每条记录都包含一个响应时间，需要计算出平均响应时间。</p>
<p><strong>解决方案:</strong></p>
<p>让我们先从简单的例子入手。在下面的代码片段里，Mapper每遇到指定词就把频次记1，Reducer一个个遍历这些词的集合然后把他们的频次加和。</p>
<div>
<div>
<div>
<pre>class Mapper
     method Map(docid id, doc d)
         for all term t in doc d do
              Emit(term t, count 1)

class Reducer
     method Reduce(term t, counts [c1, c2,...])
          sum = 0
	  for all count c in [c1, c2,...] do
               sum = sum + c
               Emit(term t, count sum)</pre>
<p>这种方法的缺点显而易见，Mapper提交了太多无意义的计数。它完全可以通过先对每个文档中的词进行计数从而减少传递给Reducer的数据量:</p>
</div>
</div>
</div>
<pre>class Mapper
	   method Map(docid id, doc d)
	      H = new AssociativeArray
	      for all term t in doc d do
	          H{t} = H{t} + 1
	      for all term t in H do
	         Emit(term t, count H{t})</pre>
<p>如果要累计计数的的不只是单个文档中的内容，还包括了一个Mapper节点处理的所有文档，那就要用到Combiner了:</p>
<pre>	class Mapper
	   method Map(docid id, doc d)
	      for all term t in doc d do
	         Emit(term t, count 1)

	class Combiner
	   method Combine(term t, [c1, c2,...])
	      sum = 0
	      for all count c in [c1, c2,...] do
	          sum = sum + c
	      Emit(term t, count sum)

	class Reducer
	   method Reduce(term t, counts [c1, c2,...])
	      sum = 0
	      for all count c in [c1, c2,...] do
	          sum = sum + c
	      Emit(term t, count sum)</pre>
<p><strong>应用:</strong><br>
Log 分析, 数据查询</p>
<h4>整理归类</h4>
<p><strong><strong>问题陈述</strong>:</strong></p>
<p>有一系列条目，每个条目都有几个属性，要把具有同一属性值的条目都保存在一个文件里，或者把条目按照属性值分组。 最典型的应用是倒排索引。</p>
<p><strong>解决方案：</strong></p>
<p>解决方案很简单。 在 Mapper 中以每个条目的所需属性值作为 key，其本身作为值传递给 Reducer。 Reducer 取得按照属性值分组的条目，然后可以处理或者保存。如果是在构建倒排索引，那么 每个条目相当于一个词而属性值就是词所在的文档ID。<br>
<strong>应用:</strong><br>
倒排索引， ETL</p>
<h4>过滤 (文本查找)，解析和校验</h4>
<p><strong>问题陈述:</strong></p>
<p>假设有很多条记录，需要从其中找出满足某个条件的所有记录，或者将每条记录传换成另外一种形式（转换操作相对于各条记录独立，即对一条记录的操作与其他记录无关）。像文本解析、特定值抽取、格式转换等都属于后一种用例。</p>
<p><strong>解决方案:</strong></p>
<p>非常简单，在Mapper 里逐条进行操作，输出需要的值或转换后的形式。<br>
<strong>应用:</strong><br>
日志分析，数据查询，ETL，数据校验</p>
<h4>分布式任务执行</h4>
<p><strong>问题陈述:</strong></p>
<p>大型计算可以分解为多个部分分别进行然后合并各个计算的结果以获得最终结果。</p>
<p><strong>解决方案:</strong> 将数据切分成多份作为每个 Mapper 的输入，每个Mapper处理一份数据，执行同样的运算，产生结果，Reducer把多个Mapper的结果组合成一个。<br>
<strong>案例研究： 数字通信系统模拟</strong><br>
像 WiMAX 这样的数字通信模拟软件通过系统模型来传输大量的随机数据，然后计算传输中的错误几率。 每个 Mapper 处理样本 1/N  的数据，计算出这部分数据的错误率，然后在 Reducer 里计算平均错误率。<br>
<strong>应用:</strong><br>
工程模拟，数字分析，性能测试</p>
<h4>排序</h4>
<p><strong>问题陈述:</strong></p>
<p>有许多条记录，需要按照某种规则将所有记录排序或是按照顺序来处理记录。</p>
<p><strong>解决方案:</strong> 简单排序很好办 – Mappers 将待排序的属性值为键，整条记录为值输出。 不过实际应用中的排序要更加巧妙一点， 这就是它之所以被称为MapReduce 核心的原因（“核心”是说排序？因为证明Hadoop计算能力的实验是大数据排序？还是说Hadoop的处理过程中对key排序的环节？）。在实践中，常用组合键来实现二次排序和分组。</p>
<p>MapReduce 最初只能够对键排序， 但是也有技术利用可以利用Hadoop 的特性来实现按值排序。想了解的话可以看 <a href="http://www.riccomini.name/Topics/DistributedComputing/Hadoop/SortByValue/">这篇博客</a>。</p>
<p>按照BigTable的概念，使用 MapReduce来对最初数据而非中间数据排序，也即保持数据的有序状态更有好处，必须注意这一点。换句话说，在数据插入时排序一次要比在每次查询数据的时候排序更高效。<br>
<strong>应用:</strong><br>
ETL，数据分析</p>
<h3>非基本 MapReduce 模式</h3>
<h4>迭代消息传递 (图处理)</h4>
<p><strong>问题陈述：</strong></p>
<p><strong> </strong>假设一个实体网络，实体之间存在着关系。 需要按照与它比邻的其他实体的属性计算出一个状态。这个状态可以表现为它和其它节点之间的距离， 存在特定属性的邻接点的迹象， 邻域密度特征等等。</p>
<p><strong>解决方案：</strong></p>
<p><strong> </strong> 网络存储为系列节点的结合，每个节点包含有其所有邻接点ID的列表。按照这个概念，MapReduce 迭代进行，每次迭代中每个节点都发消息给它的邻接点。邻接点根据接收到的信息更新自己的状态。当满足了某些条件的时候迭代停止，如达到了最大迭代次数（网络半径）或两次连续的迭代几乎没有状态改变。从技术上来看，Mapper 以每个邻接点的ID为键发出信息，所有的信息都会按照接受节点分组，reducer 就能够重算各节点的状态然后更新那些状态改变了的节点。下面展示了这个算法：</p>
<pre>class Mapper
   method Map(id n, object N)
      Emit(id n, object N)
      for all id m in N.OutgoingRelations do
         Emit(id m, message getMessage(N))

class Reducer
   method Reduce(id m, [s1, s2,...])
      M = null
      messages = []
      for all s in [s1, s2,...] do
          if IsObject(s) then
             M = s
          else               // s is a message
             messages.add(s)
      M.State = calculateState(messages)
      Emit(id m, item M)</pre>
<p>一个节点的状态可以迅速的沿着网络传全网，那些被感染了的节点又去感染它们的邻居，整个过程就像下面的图示一样：</p>
<p><img title="map-reduce-pattern-2" src="http://pic.yupoo.com/iammutex/CCpfPOAk/eUVpA.png" alt="" width="550" height="467"><br>
<strong>案例研究： 沿分类树的有效性传递</strong><br>
<strong>问题陈述：</strong></p>
<p>这个问题来自于真实的电子商务应用。将各种货物分类，这些类别可以组成一个树形结构，比较大的分类（像男人、女人、儿童）可以再分出小分类（像男裤或女装），直到不能再分为止（像男式蓝色牛仔裤）。这些不能再分的基层类别可以是有效（这个类别包含有货品）或者已无效的（没有属于这个分类的货品）。如果一个分类至少含有一个有效的子分类那么认为这个分类也是有效的。我们需要在已知一些基层分类有效的情况下找出分类树上所有有效的分类。</p>
<p><strong>解决方案：</strong></p>
<p><strong> </strong>这个问题可以用上一节提到的框架来解决。我们咋下面定义了名为 getMessage和 calculateState 的方法：</p>
<pre>    class N
	   State in {True = 2, False = 1, null = 0},
    initialized 1 or 2 for end-of-line categories, 0 otherwise
	method getMessage(object N)
	   return N.State
	method calculateState(state s, data [d1, d2,...])
	   return max( [d1, d2,...] )</pre>
<p><strong>案例研究：广度优先搜索</strong><br>
<strong><strong>问题陈述</strong>：</strong>需要计算出一个图结构中某一个节点到其它所有节点的距离。</p>
<p><strong>解决方案：</strong> Source源节点给所有邻接点发出值为0的信号，邻接点把收到的信号再转发给自己的邻接点，每转发一次就对信号值加1：</p>
<pre>    class N
	   State is distance,
    initialized 0 for source node, INFINITY for all other nodes
	method getMessage(N)
	   return N.State + 1
	method calculateState(state s, data [d1, d2,...])
	   min( [d1, d2,...] )</pre>
<p><strong>案例研究：网页排名和 Mapper 端数据聚合</strong><br>
这个算法由Google提出，使用权威的PageRank算法，通过连接到一个网页的其他网页来计算网页的相关性。真实算法是相当复杂的，但是核心思想是权重可以传播，也即通过一个节点的各联接节点的权重的均值来计算节点自身的权重。</p>
<pre>    class N
	    State is PageRank
	method getMessage(object N)
	    return N.State / N.OutgoingRelations.size()
	method calculateState(state s, data [d1, d2,...])
	    return ( sum([d1, d2,...]) )</pre>
<p>要指出的是上面用一个数值来作为评分实际上是一种简化，在实际情况下，我们需要在Mapper端来进行聚合计算得出这个值。下面的代码片段展示了这个改变后的逻辑 （针对于 PageRank 算法）：</p>
<pre>    class Mapper
	   method Initialize
	      H = new AssociativeArray
	   method Map(id n, object N)
	      p = N.PageRank  / N.OutgoingRelations.size()
	      Emit(id n, object N)
	      for all id m in N.OutgoingRelations do
	         H{m} = H{m} + p
	   method Close
	      for all id n in H do
	         Emit(id n, value H{n})

	class Reducer
	   method Reduce(id m, [s1, s2,...])
	      M = null
	      p = 0
	      for all s in [s1, s2,...] do
	          if IsObject(s) then
	             M = s
	          else
	             p = p + s
	      M.PageRank = p
	      Emit(id m, item M)</pre>
<p><strong>应用：</strong><br>
图分析，网页索引</p>
<h4>值去重 （对唯一项计数）</h4>
<p><strong>问题陈述:</strong> 记录包含值域F和值域 G，要分别统计相同G值的记录中不同的F值的数目 (相当于按照 G分组).</p>
<p>这个问题可以推而广之应用于分面搜索（某些电子商务网站称之为Narrow Search）</p>
<pre>  Record 1: F=1, G={a, b}
  Record 2: F=2, G={a, d, e}
  Record 3: F=1, G={b}
  Record 4: F=3, G={a, b}

  Result:
  a -&amp;gt; 3 // F=1, F=2, F=3
  b -&amp;gt; 2 // F=1, F=3
  d -&amp;gt; 1 // F=2
  e -&amp;gt; 1 // F=2</pre>
<p><strong>解决方案 I:</strong></p>
<p>第一种方法是分两个阶段来解决这个问题。第一阶段在Mapper中使用F和G组成一个复合值对，然后在Reducer中输出每个值对，目的是为了保证F值的唯一性。在第二阶段，再将值对按照G值来分组计算每组中的条目数。</p>
<p>第一阶段：</p>
<pre>  class Mapper
    method Map(null, record [value f, categories [g1, g2,...]])
      for all category g in [g1, g2,...]
        Emit(record [g, f], count 1)

  class Reducer
    method Reduce(record [g, f], counts [n1, n2, ...])
      Emit(record [g, f], null )</pre>
<p>第二阶段：</p>
<pre>  class Mapper
    method Map(record [f, g], null)
      Emit(value g, count 1)

  class Reducer
    method Reduce(value g, counts [n1, n2,...])
      Emit(value g, sum( [n1, n2,...] ) )</pre>
<p><strong>解决方案 II:</strong></p>
<p>第二种方法只需要一次MapReduce 即可实现，但扩展性不强。算法很简单-Mapper 输出值和分类，在Reducer里为每个值对应的分类去重然后给每个所属的分类计数加1，最后再在Reducer结束后将所有计数加和。这种方法适用于只有有限个分类，而且拥有相同F值的记录不是很多的情况。例如网络日志处理和用户分类，用户的总数很多，但是每个用户的事件是有限的，以此分类得到的类别也是有限的。值得一提的是在这种模式下可以在数据传输到Reducer之前使用Combiner来去除分类的重复值。</p>
<pre>class Mapper
method Map(null, record [value f, categories [g1, g2,...] )
for all category g in [g1, g2,...]
Emit(value f, category g)

class Reducer
method Initialize
H = new AssociativeArray : category -&amp;gt; count
method Reduce(value f, categories [g1, g2,...])
[g1&#39;, g2&#39;,..] = ExcludeDuplicates( [g1, g2,..] )
for all category g in [g1&#39;, g2&#39;,...]
H{g} = H{g} + 1
method Close
for all category g in H do
Emit(category g, count H{g})</pre>
<p><strong>应用：</strong><br>
日志分析，用户计数</p>
<h4>互相关</h4>
<p><strong>问题陈述：</strong>有多个各由若干项构成的组，计算项两两共同出现于一个组中的次数。假如项数是N，那么应该计算N*N。</p>
<p>这种情况常见于文本分析（条目是单词而元组是句子），市场分析（购买了此物的客户还可能购买什么）。如果N*N小到可以容纳于一台机器的内存，实现起来就比较简单了。</p>
<p><strong>配对法</strong></p>
<p>第一种方法是在Mapper中给所有条目配对，然后在Reducer中将同一条目对的计数加和。但这种做法也有缺点：</p>
<ul>
<li>使用 combiners 带来的的好处有限，因为很可能所有项对都是唯一的</li>
<li>不能有效利用内存</li>
</ul>
<pre>class Mapper
method Map(null, items [i1, i2,...] )
for all item i in [i1, i2,...]
for all item j in [i1, i2,...]
Emit(pair [i j], count 1)

class Reducer
method Reduce(pair [i j], counts [c1, c2,...])
s = sum([c1, c2,...])
Emit(pair[i j], count s)</pre>
<p><strong>Stripes Approach（条方法？不知道这个名字怎么理解）</strong></p>
<p>第二种方法是将数据按照pair中的第一项来分组，并维护一个关联数组，数组中存储的是所有关联项的计数。The second approach is to group data by the first item in pair and maintain an associative array (“stripe”) where counters for all adjacent items are accumulated. Reducer receives all stripes for leading item i, merges them, and emits the same result as in the Pairs approach.</p>
<ul>
<li>中间结果的键数量相对较少，因此减少了排序消耗。</li>
<li>可以有效利用 combiners。</li>
<li>可在内存中执行，不过如果没有正确执行的话也会带来问题。</li>
<li>实现起来比较复杂。</li>
<li>一般来说， “stripes” 比 “pairs” 更快</li>
</ul>
<pre>class Mapper
method Map(null, items [i1, i2,...] )
for all item i in [i1, i2,...]
H = new AssociativeArray : item -&amp;gt; counter
for all item j in [i1, i2,...]
H{j} = H{j} + 1
Emit(item i, stripe H)

class Reducer
method Reduce(item i, stripes [H1, H2,...])
H = new AssociativeArray : item -&amp;gt; counter
H = merge-sum( [H1, H2,...] )
for all item j in H.keys()
Emit(pair [i j], H{j})</pre>
<p><strong>应用：</strong><br>
文本分析，市场分析<br>
<strong>References:</strong></p>
<ol>
<li>Lin J. Dyer C. Hirst G. <a href="http://www.amazon.com/Data-Intensive-Processing-MapReduce-Synthesis-Technologies/dp/1608453421/">Data Intensive Processing MapReduce</a></li>
</ol>
<h3>用MapReduce 表达关系模式</h3>
<p>在这部分我们会讨论一下怎么使用MapReduce来进行主要的关系操作。</p>
<h4>筛选（Selection）</h4>
<pre>class Mapper
method Map(rowkey key, tuple t)
if t satisfies the predicate
Emit(tuple t, null)</pre>
<h4>投影（Projection）</h4>
<p>投影只比筛选稍微复杂一点，在这种情况下我们可以用Reducer来消除可能的重复值。</p>
<pre>class Mapper
method Map(rowkey key, tuple t)
tuple g = project(t) // extract required fields to tuple g
Emit(tuple g, null)

class Reducer
method Reduce(tuple t, array n) // n is an array of nulls
Emit(tuple t, null)</pre>
<h4>合并（Union）</h4>
<p>两个数据集中的所有记录都送入Mapper，在Reducer里消重。</p>
<pre>class Mapper
method Map(rowkey key, tuple t)
Emit(tuple t, null)

class Reducer
method Reduce(tuple t, array n) // n is an array of one or two nulls
Emit(tuple t, null)</pre>
<h4>交集（Intersection）</h4>
<p>将两个数据集中需要做交叉的记录输入Mapper，Reducer 输出出现了两次的记录。因为每条记录都有一个主键，在每个数据集中只会出现一次，所以这样做是可行的。</p>
<pre>class Mapper
method Map(rowkey key, tuple t)
Emit(tuple t, null)

class Reducer
method Reduce(tuple t, array n) // n is an array of one or two nulls
if n.size() = 2
Emit(tuple t, null)</pre>
<h4>差异（Difference）</h4>
<p>假设有两个数据集R和S，我们要找出R与S的差异。Mapper将所有的元组做上标记，表明他们来自于R还是S，Reducer只输出那些存在于R中而不在S中的记录。</p>
<pre>class Mapper
method Map(rowkey key, tuple t)
Emit(tuple t, string t.SetName) // t.SetName is either 'R' or 'S'

class Reducer
method Reduce(tuple t, array n) // array n can be ['R'], ['S'], ['R' 'S'], or ['S', 'R']
if n.size() = 1 and n[1] = 'R'
Emit(tuple t, null)</pre>
<h4>分组聚合（GroupBy and Aggregation）</h4>
<p>分组聚合可以在如下的一个MapReduce中完成。Mapper抽取数据并将之分组聚合，Reducer 中对收到的数据再次聚合。典型的聚合应用比如求和与最值可以以流的方式进行计算，因而不需要同时保有所有的值。但是另外一些情景就必须要两阶段MapReduce，前面提到过的惟一值模式就是一个这种类型的例子。</p>
<pre>class Mapper
method Map(null, tuple [value GroupBy, value AggregateBy, value ...])
Emit(value GroupBy, value AggregateBy)
class Reducer
method Reduce(value GroupBy, [v1, v2,...])
Emit(value GroupBy, aggregate( [v1, v2,...] ) ) // aggregate() : sum(), max(),...</pre>
<h4>连接（Joining）</h4>
<p>MapperReduce框架可以很好地处理连接，不过在面对不同的数据量和处理效率要求的时候还是有一些技巧。在这部分我们会介绍一些基本方法，在后面的参考文档中还列出了一些关于这方面的专题文章。<br>
<strong>分配后连接 （Reduce端连接,排序-合并连接）</strong><br>
这个算法按照键K来连接数据集R和L。Mapper 遍历R和L中的所有元组，以K为键输出每一个标记了来自于R还是L的元组，Reducer把同一个K的数据分装入两个容器（R和L），然后嵌套循环遍历两个容器中的数据以得到交集，最后输出的每一条结果都包含了R中的数据、L中的数据和K。这种方法有以下缺点：</p>
<ul>
<li>Mapper要输出所有的数据，即使一些key只会在一个集合中出现。</li>
<li>Reducer 要在内存中保有一个key的所有数据，如果数据量打过了内存，那么就要缓存到硬盘上，这就增加了硬盘IO的消耗。</li>
</ul>
<p>尽管如此，再分配连接方式仍然是最通用的方法，特别是其他优化技术都不适用的时候。</p>
<pre>class Mapper
method Map(null, tuple [join_key k, value v1, value v2,...])
Emit(join_key k, tagged_tuple [set_name tag, values [v1, v2, ...] ] )

class Reducer
method Reduce(join_key k, tagged_tuples [t1, t2,...])
H = new AssociativeArray : set_name -&amp;gt; values
for all tagged_tuple t in [t1, t2,...] // separate values into 2 arrays
H{t.tag}.add(t.values)
for all values r in H{&#39;R&#39;} // produce a cross-join of the two arrays
for all values l in H{&#39;L&#39;}
Emit(null, [k r l] )</pre>
<p><strong>复制链接Replicated Join （Mapper端连接, Hash 连接）</strong><br>
在实际应用中，将一个小数据集和一个大数据集连接是很常见的（如用户与日志记录）。假定要连接两个集合R和L，其中R相对较小，这样，可以把R分发给所有的Mapper，每个Mapper都可以载入它并以连接键来索引其中的数据，最常用和有效的索引技术就是哈希表。之后，Mapper遍历L，并将其与存储在哈希表中的R中的相应记录连接，。这种方法非常高效，因为不需要对L中的数据排序，也不需要通过网络传送L中的数据，但是R必须足够小到能够分发给所有的Mapper。</p>
<pre>class Mapper
method Initialize
H = new AssociativeArray : join_key -&amp;gt; tuple from R
R = loadR()
for all [ join_key k, tuple [r1, r2,...] ] in R
H{k} = H{k}.append( [r1, r2,...] )

method Map(join_key k, tuple l)
for all tuple r in H{k}
Emit(null, tuple [k r l] )</pre>
<p><strong>参考：</strong></p>
<ol>
<li><a href="http://www.inf.ed.ac.uk/publications/thesis/online/IM100859.pdf">Join Algorithms using Map/Reduce</a></li>
<li><a href="http://infolab.stanford.edu/~ullman/pub/join-mr.pdf">Optimizing Joins in a MapReduce Environment</a></li>
</ol>
<h3>应用于机器学习和数学方面的 MapReduce 算法</h3>
<ul>
<li>C. T. Chu <em>et al</em> provides an excellent description of  machine learning algorithms for MapReduce in the article <a title="Map-Reduce for Machine Learning on Multicore" href="http://www.cs.stanford.edu/people/ang//papers/nips06-mapreducemulticore.pdf">Map-Reduce for Machine Learning on Multicore</a>.</li>
<li>FFT using MapReduce:  <a href="http://www.slideshare.net/hortonworks/large-scale-math-with-hadoop-mapreduce">http://www.slideshare.net/hortonworks/large-scale-math-with-hadoop-mapreduce</a></li>
<li>MapReduce for integer factorization: <a href="http://www.javiertordable.com/files/MapreduceForIntegerFactorization.pdf">http://www.javiertordable.com/files/MapreduceForIntegerFactorization.pdf</a></li>
<li>Matrix multiplication with MapReduce: <a href="http://csl.skku.edu/papers/CS-TR-2010-330.pdf">http://csl.skku.edu/papers/CS-TR-2010-330.pdf</a> and <a href="http://www.norstad.org/matrix-multiply/index.html">http://www.norstad.org/matrix-multiply/index.html</a></li>
</ul>
<div style="margin-top:20px;margin-left:70px;line-height:24px;border:1px solid #ccc;text-align:center;width:545px;background:#fff">
<div style="font-size:16px;font-family:Verdana;background:#d20;color:#fff;float:left;border-radius:10px 0 10px 0;padding:3px 12px 4px;line-height:32px;margin-top:14px">42区 VPS</div>
<div>
42qu.com 云主机 , 卖给创业的你 。 <a href="http://vps.42qu.com/by/iammutex/rss" style="text-decoration:none;background:none;color:#02d">点击这里 , 查看详情</a>
</div>
</div>
<table cellspacing="0" cellpadding="2" border="0" width="100%" style="clear:both">
    
    <tr>
        <td><b><font size="-1" style="display:block!important;padding:20px 0 5px!important">相关文章：</font></b></td>
    </tr>
    
            <tr>
                <td style="margin:0!important;padding:0!important;line-height:20px!important">
                    <img border="0" src="http://static.wumii.cn/images/widget/widget_solidPoint.gif">
                    <a style="text-decoration:none!important" href="http://app.wumii.com/ext/redirect?url=http%3A%2F%2Fblog.nosqlfan.com%2Fhtml%2F94.html&amp;from=http%3A%2F%2Fblog.nosqlfan.com%2Fhtml%2F4179.html">
                        <font size="-1" color="#333333" style="line-height:1.65em;font-size:14px!important">一个有意思的MapReduce的slide</font>
                    </a>
                </td>
            </tr>
            <tr>
                <td style="margin:0!important;padding:0!important;line-height:20px!important">
                    <img border="0" src="http://static.wumii.cn/images/widget/widget_solidPoint.gif">
                    <a style="text-decoration:none!important" href="http://app.wumii.com/ext/redirect?url=http%3A%2F%2Fblog.nosqlfan.com%2Fhtml%2F383.html&amp;from=http%3A%2F%2Fblog.nosqlfan.com%2Fhtml%2F4179.html">
                        <font size="-1" color="#333333" style="line-height:1.65em;font-size:14px!important">Mongodb Mapreduce 初窥</font>
                    </a>
                </td>
            </tr>
            <tr>
                <td style="margin:0!important;padding:0!important;line-height:20px!important">
                    <img border="0" src="http://static.wumii.cn/images/widget/widget_solidPoint.gif">
                    <a style="text-decoration:none!important" href="http://app.wumii.com/ext/redirect?url=http%3A%2F%2Fblog.nosqlfan.com%2Fhtml%2F3756.html&amp;from=http%3A%2F%2Fblog.nosqlfan.com%2Fhtml%2F4179.html">
                        <font size="-1" color="#333333" style="line-height:1.65em;font-size:14px!important">mrcc：基于MapReduce的分布式C语言编译器</font>
                    </a>
                </td>
            </tr>
            <tr>
                <td style="margin:0!important;padding:0!important;line-height:20px!important">
                    <img border="0" src="http://static.wumii.cn/images/widget/widget_solidPoint.gif">
                    <a style="text-decoration:none!important" href="http://app.wumii.com/ext/redirect?url=http%3A%2F%2Fblog.nosqlfan.com%2Fhtml%2F665.html&amp;from=http%3A%2F%2Fblog.nosqlfan.com%2Fhtml%2F4179.html">
                        <font size="-1" color="#333333" style="line-height:1.65em;font-size:14px!important">mincemeat.py-最精简的MapReduce引擎实现</font>
                    </a>
                </td>
            </tr>
            <tr>
                <td style="margin:0!important;padding:0!important;line-height:20px!important">
                    <img border="0" src="http://static.wumii.cn/images/widget/widget_solidPoint.gif">
                    <a style="text-decoration:none!important" href="http://app.wumii.com/ext/redirect?url=http%3A%2F%2Fblog.nosqlfan.com%2Fhtml%2F2272.html&amp;from=http%3A%2F%2Fblog.nosqlfan.com%2Fhtml%2F4179.html">
                        <font size="-1" color="#333333" style="line-height:1.65em;font-size:14px!important">下一代Hadoop MapReduce</font>
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
</table><img src="http://www1.feedsky.com/t1/723917537/nosqlfan/feedsky/s.gif?r=http://blog.nosqlfan.com/html/4179.html" border="0" height="0" width="0">