---
layout: post
title:  "如何设计一门语言（六）——exception和error code"
date:   2013-06-10 15:01:00
author: 陈梓瀚(vczh)
categories: program
---

## 如何设计一门语言（六）——exception和error code
### by 陈梓瀚(vczh)
### at 2013-06-10 15:01:00
### original <http://www.cppblog.com/vczh/archive/2013/06/10/200920.html>

<p><font size="2" face="微软雅黑">我一直以来对于exception的态度都是很明确的。首先exception是好的，否则就不会有绝大多数的语言都支持他了。其次，error code也没什么问题，只是需要一个前提——你的语言得跟Haskell一样有monad和comonad。你看Haskell就没有exception，大家也写的很开心。为什么呢？因为只要把返回带error code结果的函数给做成一个monad/comonad，那么就可以用CPS变换把它变成exception了。所以说CPS作为跟goto同样基本的控制流语句真是当之无愧呀，只是CPS是type rich的，goto是type poor的。</font></p> <p><font size="2" face="微软雅黑">其实很多人对于exception的恐惧心理在于你不知道一个函数会抛什么exception出来，然后程序一crash你就傻逼了。对于server来讲情况还好，出了问题只要杀掉快速重启就行了，如今没个replication和fault tolerance还有脸说你在写后端（所以不知道那些做web的人究竟在反对什么）？这主要的问题还是在于client。只要client上面的东西还没保存，那你一crash数据就完蛋了是不是——当然这只是你的想象啦，其实根本不是这样子的。</font></p> <p><font size="2" face="微软雅黑">我们的程序抛了一个access violation出来，和抛了其它exception出来，究竟有什么区别呢？access violation是一个很奇妙的东西，一旦抛了出来就告诉你你的程序没救了，继续执行下去说不定还会有破坏作用。特别是对于C/C++/Delphi这类语言来说，你不小心把错误的东西写进了什么乱七八糟的指针里面去，那会儿什么事情都没发生，结果程序跑着跑着就错了。因为你那个算错了得到的野指针，说不定是隔壁的不知道什么object的成员变量，说不定是heap里面的数据结构，或者说别的什么东西，就这么给你写了。如果你写了别的object的成员变量那封装肯定就不管用了，这个类的不变量就给你破坏了。既然你的成员函数都是基于不变量来写的，那这个时候出错时必须的。如果你写到了heap的数据结构那就更加呵呵呵了，说不定下次一new就崩了，而且你还不知道为什么。</font></p> <p><font size="2" face="微软雅黑">出了access violation以外的exception基本是没什么危害的，最严重的大概也就是网线被拔了，另一块不是装OS的硬盘突然坏了什么的这种反正你也没办法但是好歹还可以处理的事情。如果这些exception是你自己抛出来的那就更可靠了——那都是计划内的。只要程序<strong>未来不会进入access violation的状态</strong>，那证明你<strong>现在所能拿到的所有变量</strong>，还有指针指向的memory，基本上都还是靠谱的。出了你救不了的错误，至少你还可以吧数据安全的保存下来，然后让自己重启——就跟word一样。但是你有可能会说，拿出了access violation怎么就不能保存数据了呢？因为这个时候内存都毁了，指不定你保存数据的代码new点东西然后挂了，这基本上是没准的。</font></p> <p><font size="2" face="微软雅黑">所以无论你喜欢exception还是喜欢error code，你所希望达到的效果本质上就是<strong>避免程序未来会进入access violation的状态</strong>。想做到这一点，方法也是很简单粗暴的——只要你在函数里面把运行前该对函数做的检查都查一遍就好了。这个无论你用exception还是用error code，写起来都是一样的。区别在于调用你的函数的那个人会怎么样。那么我来举个例子，譬如说你觉得STL的map实在是太傻比了，于是你自己写了一个，然后有了一个这样子的函数：</font> <div style="border-bottom:#cccccc 1px solid;border-left:#cccccc 1px solid;padding-bottom:5px;background-color:#f5f5f5;padding-left:5px;padding-right:5px;border-top:#cccccc 1px solid;border-right:#cccccc 1px solid;padding-top:5px"><pre><font size="2"><span style="color:#008000">//</span><span style="color:#008000"> exception版本</span>
Symbol* SymbolMap::Lookup(<span style="color:#0000ff">const</span> wstring&amp;</font><font size="2"><span style="color:#000000"> name);

</span><span style="color:#008000">//</span><span style="color:#008000"> error code版本</span>
<span style="color:#0000ff">int</span> SymbolMap::Lookup(<span style="color:#0000ff">const</span> wstring&amp; name, Symbol*&amp;</font><font size="2"><span style="color:#000000"> result);

</span><span style="color:#008000">//</span><span style="color:#008000"> 其实COM就是你们最喜欢的error code风格了，写起来应该很开心才对呀，你们的双重标准真严重</span>
HRESULT ISymbolMap::Lookup(BSTR name, ISymbol** result);</font></pre></div></p>
<p><font size="2" face="微软雅黑">于是拿到了Lookup函数之后，我们就要开始来完成一个任务了，譬如说拿两个key得到两个symbol然后组合出一个新的symbol。函数的错误处理逻辑是这样的，如果key失败了，因为业务的原因，我们要告诉函数外面说key不存在的。调用了一个ComposeSymbol的函数丢出什么IndexOutOfRangeException显然是不合理的。但是合并的那一步，因为业务都在同一个领域内，所以suppose里面的异常外面是可以接受的。如果出现了计划外的异常，那我们是处理不了的，只能丢给上面了，外面的代码对于不认识的异常只需要报告任务失败了就可以了。于是我们的函数就会这么写：</font>
<div style="border-bottom:#cccccc 1px solid;border-left:#cccccc 1px solid;padding-bottom:5px;background-color:#f5f5f5;padding-left:5px;padding-right:5px;border-top:#cccccc 1px solid;border-right:#cccccc 1px solid;padding-top:5px"><pre><font size="2">Symbol* ComposeSymbol(<span style="color:#0000ff">const</span> wstring&amp; a, <span style="color:#0000ff">const</span> wstring&amp; b, SymbolMap*</font><font size="2"><span style="color:#000000"> map)
{
    Symbol</span>* sa=<span style="color:#800080">0</span></font><font size="2"><span style="color:#000000">;
    Symbol</span>* sb=<span style="color:#800080">0</span></font><font size="2"><span style="color:#000000">;
    </span><span style="color:#0000ff">try</span></font><span style="color:#000000">
<font size="2">    {
        sa</font></span><font size="2">=map-&gt;</font><font size="2"><span style="color:#000000">Lookup(a);
        sa</span>=map-&gt;</font><font size="2"><span style="color:#000000">Lookup(b);
    }
    </span><span style="color:#0000ff">catch</span>(<span style="color:#0000ff">const</span> IndexOutOfRangeException&amp;</font><font size="2"><span style="color:#000000"> ex)
    {
        </span><span style="color:#0000ff">throw</span></font><font size="2"><span style="color:#000000"> SymbolKeyException(ex.GetIndex());
    }
    </span><span style="color:#0000ff">return</span></font><span style="color:#000000"><font size="2"> CreatePairSymbol(sa, sb);
}</font></span></pre></div></p>
<p><font size="2" face="微软雅黑">看起来还挺不错。现在我们可以开始考虑error code的版本了。于是我们需要思考几个问题。首先第一个就是Lookup失败的时候要怎么报告？直接报告key的内容是不可能的，因为error code是个int。</font></p>
<p><font size="2" face="微软雅黑">题外话，error code当然可以是别的什么东西，如果需要返回丰富内容的错误的话，那怎样都得是一个指针了，这个时候你们就会面临下面的问题——这已经他妈不满足谁构造谁释放的原则了呀，而且我这个指针究竟直接返回出去外面理不理呢，如果只要有一个环节不理了，那内存岂不是泄露了？如果我要求把错误返回在参数里面的话，我每次调用函数都要创建出那么个结构来保存异常，不仅有if的复杂度，还有创建空间的复杂度，整个代码都变成了屎。所以还是老老实实用int吧……</font></p>
<p><font size="2" face="微软雅黑">那我们要如何把key的信息给编码在一个int里面呢？因为key要么是来自于a，要么是来自于b，所以其实我们就需要两个code了。那Lookup的其他错误怎么办呢？CreatePairSymbol的错误怎么办呢？万一Lookup除了ERROR_KEY_NOT_FOUND以外，或者是CreatePairSymbol的错误刚好跟a或者b的code重合了怎么办？对于这个问题，我只能说：</font></p>
<p><font size="2" face="微软雅黑">要不你们team的人先<strong><font size="5">开会讨论一下</font></strong>最后记录在文档里面备查以免后面的人看了傻眼了……</font></p>
<p><font size="2" face="微软雅黑">好了，现在假设说会议取得了圆满成功，会议双方加深了互相的理解，促进了沟通，最后还写了一个白皮书出来，有效的落实了对a和b的code的指导，于是我们终于可以写出下面的代码了：</font>
<div style="border-bottom:#cccccc 1px solid;border-left:#cccccc 1px solid;padding-bottom:5px;background-color:#f5f5f5;padding-left:5px;padding-right:5px;border-top:#cccccc 1px solid;border-right:#cccccc 1px solid;padding-top:5px"><pre><font size="2"><span style="color:#0000ff">#define</span> SUCCESS 0 <span style="color:#008000">//</span><span style="color:#008000"> global error code for success</span>
<span style="color:#0000ff">#define</span> ERROR_COMPOSE_SYMBOL_WRONG_A 1
<span style="color:#0000ff">#define</span> ERROR_COMPOSE_SYMBOL_WRONG_B 2

<span style="color:#0000ff">int</span> ComposeSymbol(<span style="color:#0000ff">const</span> wstring&amp; a, <span style="color:#0000ff">const</span> wstring&amp; b, SymbolMap* map, Symbol*&amp;</font><font size="2"><span style="color:#000000"> result)
{
    </span><span style="color:#0000ff">int</span> code=</font><font size="2"><span style="color:#000000">SUCCESS;
    Symbol</span>* sa=<span style="color:#800080">0</span></font><font size="2"><span style="color:#000000">;
    Symbol</span>* sb=<span style="color:#800080">0</span></font><font size="2"><span style="color:#000000">;
    </span><span style="color:#0000ff">switch</span>(code=map-&gt;</font><font size="2"><span style="color:#000000">Lookup(a, sa))
    {
    </span><span style="color:#0000ff">case</span></font><font size="2"><span style="color:#000000"> SUCCESS:
        </span><span style="color:#0000ff">break</span></font><font size="2"><span style="color:#000000">;
    </span><span style="color:#0000ff">case</span></font><font size="2"><span style="color:#000000"> ERROR_SYMBOL_MAP_KEY_NOT_FOUND:
        </span><span style="color:#0000ff">return</span></font><font size="2"><span style="color:#000000"> ERROR_COMPOSE_SYMBOL_WRONG_A;
    </span><span style="color:#0000ff">default</span></font><font size="2"><span style="color:#000000">:
        </span><span style="color:#0000ff">return</span></font><font size="2"><span style="color:#000000"> code;
    }
    </span><span style="color:#0000ff">switch</span>(code=map-&gt;</font><font size="2"><span style="color:#000000">Lookup(b, sb))
    {
    </span><span style="color:#0000ff">case</span></font><font size="2"><span style="color:#000000"> SUCCESS:
        </span><span style="color:#0000ff">break</span></font><font size="2"><span style="color:#000000">;
    </span><span style="color:#0000ff">case</span></font><font size="2"><span style="color:#000000"> ERROR_SYMBOL_MAP_KEY_NOT_FOUND:
        </span><span style="color:#0000ff">return</span></font><font size="2"><span style="color:#000000"> ERROR_COMPOSE_SYMBOL_WRONG_B;
    </span><span style="color:#0000ff">default</span></font><font size="2"><span style="color:#000000">:
        </span><span style="color:#0000ff">return</span></font><font size="2"><span style="color:#000000"> code;
    }
    </span><span style="color:#0000ff">return</span></font><span style="color:#000000"><font size="2"> CreatePairSymbol(sa, sb, result);
}</font></span></pre></div></p>
<p><font size="2" face="微软雅黑">啊，好像太长，干脆我还是不负责任一点吧，反正代码写的好也涨不了工资，干脆不认识的错误都返回ERROR_COMPOSE_SYMBOL_UNKNOWN_ERROR好了，于是就可以把代码变成下面这样……都到这份上了不要叫自己程序员了，叫<strong>程序狗</strong>吧……</font>
<div style="border-bottom:#cccccc 1px solid;border-left:#cccccc 1px solid;padding-bottom:5px;background-color:#f5f5f5;padding-left:5px;padding-right:5px;border-top:#cccccc 1px solid;border-right:#cccccc 1px solid;padding-top:5px"><pre><font size="2"><span style="color:#0000ff">#define</span> SUCCESS 0 <span style="color:#008000">//</span><span style="color:#008000"> global error code for success</span>
<span style="color:#0000ff">#define</span> ERROR_COMPOSE_SYMBOL_WRONG_A 1
<span style="color:#0000ff">#define</span> ERROR_COMPOSE_SYMBOL_WRONG_B 2
<span style="color:#0000ff">#define</span> ERROR_COMPOSE_SYMBOL_UNKNOWN_ERROR 3

<span style="color:#0000ff">int</span> ComposeSymbol(<span style="color:#0000ff">const</span> wstring&amp; a, <span style="color:#0000ff">const</span> wstring&amp; b, SymbolMap* map, Symbol*&amp;</font><font size="2"><span style="color:#000000"> result)
{
    Symbol</span>* sa=<span style="color:#800080">0</span></font><font size="2"><span style="color:#000000">;
    Symbol</span>* sb=<span style="color:#800080">0</span></font><font size="2"><span style="color:#000000">;
    </span><span style="color:#0000ff">if</span>(map-&gt;Lookup(a, sa)!=</font><font size="2"><span style="color:#000000">SUCCESS)
        </span><span style="color:#0000ff">return</span></font><font size="2"><span style="color:#000000"> ERROR_COMPOSE_SYMBOL_UNKNOWN_ERROR;
    </span><span style="color:#0000ff">if</span>(map-&gt;Lookup(b, sb)!=</font><font size="2"><span style="color:#000000">SUCCESS)
        </span><span style="color:#0000ff">return</span></font><font size="2"><span style="color:#000000"> ERROR_COMPOSE_SYMBOL_UNKNOWN_ERROR;
    </span><span style="color:#0000ff">if</span>(CreatePairSymbol(sa, sb, result)!=</font><font size="2"><span style="color:#000000">SUCCESS)
        </span><span style="color:#0000ff">return</span></font><font size="2"><span style="color:#000000"> ERROR_COMPOSE_SYMBOL_UNKNOWN_ERROR;
    </span><span style="color:#0000ff">return</span></font><span style="color:#000000"><font size="2"> SUCCESS;
}</font></span></pre></div></p>
<p><font face="微软雅黑"><font size="2">当然，如果大家都一样不负责任的话，还是exception完爆error code：</font>
<div style="border-bottom:#cccccc 1px solid;border-left:#cccccc 1px solid;padding-bottom:5px;background-color:#f5f5f5;padding-left:5px;padding-right:5px;border-top:#cccccc 1px solid;border-right:#cccccc 1px solid;padding-top:5px"><pre><font size="2">Symbol* ComposeSymbol(<span style="color:#0000ff">const</span> wstring&amp; a, <span style="color:#0000ff">const</span> wstring&amp; b, SymbolMap*</font><font size="2"><span style="color:#000000"> map)
{
    </span><span style="color:#0000ff">return</span> CreatePairSymbol(map-&gt;Lookup(a), map-&gt;</font><span style="color:#000000"><font size="2">Lookup(b));
}</font></span></pre></div></font></p>
<p><font size="2" face="微软雅黑">大部分人人只会用在当前条件下最容易写的方法来设计软件，而不是先设计出软件然后再看看怎样写比较容易，这就是为什么我说，只要你一个月给程序员还给不到一狗半，还是老老实实在政策上落实exception吧。至少exception写起来还不会让人那么心烦，可以把程序写得坚固一点。</font></p>
<p><font face="微软雅黑"><font size="2">好了，单线程下面至少你还可以争吵说究竟exception好还是error code好，但是到了异步程序里面就完全不一样了。现在的异步程序都很多，譬如说有良心的手机app啦，譬如说javascript啦，metro程序等等。一个try根本没办法跨线程使用所以一个这样子的函数（下面开始用C#，C++11的future/promise我用的还不熟）：</font>
<div style="border-bottom:#cccccc 1px solid;border-left:#cccccc 1px solid;padding-bottom:5px;background-color:#f5f5f5;padding-left:5px;padding-right:5px;border-top:#cccccc 1px solid;border-right:#cccccc 1px solid;padding-top:5px"><pre><span style="color:#0000ff"><font size="2">class</font></span><font size="2"><span style="color:#000000"> Normal
{
    </span><span style="color:#0000ff">public</span> <span style="color:#0000ff">string</span> Do(<span style="color:#0000ff">string</span></font><span style="color:#000000"><font size="2"> args);
}</font></span></pre></div></font></p>
<p><font face="微软雅黑"><font size="2">最后就会变成这样：</font>
<div style="border-bottom:#cccccc 1px solid;border-left:#cccccc 1px solid;padding-bottom:5px;background-color:#f5f5f5;padding-left:5px;padding-right:5px;border-top:#cccccc 1px solid;border-right:#cccccc 1px solid;padding-top:5px"><pre><span style="color:#0000ff"><font size="2">class</font></span><font size="2"><span style="color:#000000"> Async
{
    </span><span style="color:#008000">//</span><span style="color:#008000"> before .NET 4.0</span>
    IAsyncResult BeginDo(<span style="color:#0000ff">string</span> args, Action&lt;IAsyncResult&gt;</font><font size="2"><span style="color:#000000"> continuation);
    </span><span style="color:#0000ff">string</span></font><font size="2"><span style="color:#000000"> EndDo(IAsyncResult ar);

    </span><span style="color:#008000">//</span><span style="color:#008000"> after .NET 4.0</span>
    Task&lt;<span style="color:#0000ff">string</span>&gt; DoAsync(<span style="color:#0000ff">string</span></font><span style="color:#000000"><font size="2"> args);
}</font></span></pre></div></font></p>
<p><font size="2" face="微软雅黑">当你使用BeginDo的时候，你可以在continuation里面调用EndDo，然后得到一个string，或者得到一个exception。但是因为EndDo的exception不是在BeginDo里面throw出来的，所以无论你EndDo返回string也好，返回Tuple&lt;string, Exception&gt;也好，对于BeginDo和EndDo的实现来说其实都一样，没有上文所说的exception和error code的区别。</font></p>
<p><font face="微软雅黑"><font size="2">不过.NET从BeginDo/EndDo到DoAsync经历了一个巨大的进步。虽然形式上都一样，但是由于C#并不像Haskell那样可以完美的操作函数，C#还是面向对象做得更好，于是如果我们吧Task&lt;T&gt;看成下面的样子，那其实两种写法是没有区别的：</font>
<div style="border-bottom:#cccccc 1px solid;border-left:#cccccc 1px solid;padding-bottom:5px;background-color:#f5f5f5;padding-left:5px;padding-right:5px;border-top:#cccccc 1px solid;border-right:#cccccc 1px solid;padding-top:5px"><pre><font size="2"><span style="color:#0000ff">class</span> Task&lt;T&gt;</font><span style="color:#000000">
<font size="2">{
    </font></span><font size="2"><span style="color:#0000ff">public</span> IAsyncResult BeginRun(Action&lt;IAsyncResult&gt;</font><font size="2"><span style="color:#000000"> continuation);
    </span><span style="color:#0000ff">public</span></font><span style="color:#000000"><font size="2"> T EndRun(IAsyncResult ar);
}</font></span></pre></div></font></p>
<p><font face="微软雅黑"><font><font size="2">不过如果还是用BeginRun/EndRun这种方法来调用的话，使用起来还是很不方便，而且也很难把更多的Task组合在一起。所以最后.NET给出的Task是下面这个样子的（Comonad！）：</font>
<div style="border-bottom:#cccccc 1px solid;border-left:#cccccc 1px solid;padding-bottom:5px;background-color:#f5f5f5;padding-left:5px;padding-right:5px;border-top:#cccccc 1px solid;border-right:#cccccc 1px solid;padding-top:5px"><pre><font size="2"><span style="color:#0000ff">class</span> Task&lt;T&gt;</font><span style="color:#000000">
<font size="2">{
    </font></span><font size="2"><span style="color:#0000ff">public</span> Task&lt;U&gt; ContinueWith&lt;U&gt;(Func&lt;Task&lt;T&gt;, U&gt;</font><span style="color:#000000"><font size="2"> continuation);
}</font></span></pre></div></font></font></p>
<p><font face="微软雅黑"><font size="2">尽管真实的Task&lt;T&gt;要比上面那个复杂得多，但是总的来说其实就是围绕着基本简单的函数建立起来的一大堆helper function。到这里C#终于把CPS变换在异步处理上的应用的这一部分给抽象出来了。在看CPS的效果之前，我们先来看一个同步函数：</font></font><font style="background-color:#f5f5f5" face="微软雅黑"><font style="background-color:#ffffff" size="2"></font></font></p>
<p style="border-bottom:#cccccc 1px solid;border-left:#cccccc 1px solid;padding-bottom:5px;background-color:#f5f5f5;padding-left:5px;padding-right:5px;border-top:#cccccc 1px solid;border-right:#cccccc 1px solid;padding-top:5px"><pre><font size="2"><span style="color:#0000ff">void</span> button1_Clicked(<span style="color:#0000ff">object</span></font><font size="2"><span style="color:#000000"> sender, EventArgs e)
{
        </span><span style="color:#008000">//</span><span style="color:#008000"> 假设我们有string Http.Download(string url);</span>
        <span style="color:#0000ff">try</span></font><span style="color:#000000">
<font size="2">        {
                </font></span><font size="2"><span style="color:#0000ff">string</span> a =</font><font size="2"><span style="color:#000000"> Http.Download(url1);
                </span><span style="color:#0000ff">string</span> b =</font><font size="2"><span style="color:#000000"> Http.Download(url2);
                textBox1.Text</span>=a+</font><font size="2"><span style="color:#000000">b;
        }
        </span><span style="color:#0000ff">catch</span></font><font size="2"><span style="color:#000000">(Exception ex)
        {
                textBox1.Text</span>=</font><span style="color:#000000"><font size="2">ex.Message;
        }
}</font></span></pre></p>
<p><font style="background-color:#f5f5f5" size="2" face="微软雅黑"></font><font face="微软雅黑"><font size="2">这段代码显然是一个GUI里面的代码。我们如果在一个GUI程序里面这么写，就会把程序写得跟QQ一样卡了。所以实际上这么做是不对的。不过为了表达程序需要做的所有事情，就有了这么一个同步的版本。那么我们尝试吧这个东西修改成异步的把！</font>
<div style="border-bottom:#cccccc 1px solid;border-left:#cccccc 1px solid;padding-bottom:5px;background-color:#f5f5f5;padding-left:5px;padding-right:5px;border-top:#cccccc 1px solid;border-right:#cccccc 1px solid;padding-top:5px"><pre><font size="2"><span style="color:#0000ff">void</span> button2_Clicked(<span style="color:#0000ff">object</span></font><font size="2"><span style="color:#000000"> sender, EventArgs e)
{
    </span><span style="color:#008000">//</span></font><font size="2"><span style="color:#008000"> 假设我们有Task&lt;string&gt; Http.DownloadAsync(string url);
    </span><span style="color:#008000">//</span><span style="color:#008000"> 需要MethodInvoker是因为，对textBox1.Text的修改只能在GUI线程里面做</span>
    Http.DownloadAsync(url1).ContinueWith(ta=&gt;<span style="color:#0000ff">new</span> MethodInvoker(()=&gt;</font><span style="color:#000000">
<font size="2">    {
        </font></span><span style="color:#0000ff"><font size="2">try</font></span><span style="color:#000000">
<font size="2">        {
            </font></span><span style="color:#008000"><font size="2">//</font></span><font size="2"><span style="color:#008000"> 这个时候ta已经运行完了，所以对ta.Result的取值不会造成GUI线程等待IO。
            </span><span style="color:#008000">//</span><span style="color:#008000"> 而且如果DownloadAsync内部出了错，异常会在这里抛出来。</span>
            <span style="color:#0000ff">string</span> a=</font><font size="2"><span style="color:#000000">ta.Result;
            Http.DownloadAsync(url2).ContinueWith(tb</span>=&gt;<span style="color:#0000ff">new</span> MethodInvoker(()=&gt;</font><span style="color:#000000">
<font size="2">            {
                </font></span><span style="color:#0000ff"><font size="2">try</font></span><span style="color:#000000">
<font size="2">                {
                    </font></span><font size="2"><span style="color:#0000ff">string</span> b=</font><font size="2"><span style="color:#000000">tb.Result;
                    textBox1.Text</span>=a+</font><font size="2"><span style="color:#000000">b;
                }
<strong>                </strong></span><span style="color:#0000ff"><strong><font style="background-color:#ffff00">catch</font></strong></span></font><font size="2"><strong><span style="color:#000000"><font style="background-color:#ffff00">(Exception ex)
</font>                <font style="background-color:#ffff00">{</font>
                <font style="background-color:#ffff00">    textBox1.Text</font></span><font style="background-color:#ffff00">=</font></strong></font><font size="2"><span style="color:#000000"><strong><font style="background-color:#ffff00">ex.Message;
</font>                <font style="background-color:#ffff00">}</font></strong>
            })));
        }
<strong>        </strong></span><span style="color:#0000ff"><strong><font style="background-color:#ffff00">catch</font></strong></span></font><font size="2"><strong><span style="color:#000000"><font style="background-color:#ffff00">(Exception ex)
</font>        <font style="background-color:#ffff00">{</font>
        <font style="background-color:#ffff00">    textBox1.Text</font></span><font style="background-color:#ffff00">=</font></strong></font><span style="color:#000000"><font size="2"><strong><font style="background-color:#ffff00">ex.Message;
</font>        <font style="background-color:#ffff00">}</font></strong>
    })));
}</font></span></pre></div></font></p>
<p><font size="2" face="微软雅黑">我们发现，异步操作发生的异常，把优越的exception拉低到了丑陋的error code的同一个情况上面——我们需要不断地对每一个操作重复同样的错误处理过程！而且在这种地方我们连“不负责任”的选项都没有了，如果你不try-catch（或者不检查error code），那到时候程序就会发生一些莫名其妙的问题，在GUI那一层你什么事情都不知道，整个程序就变成了傻逼。</font></p>
<p><font size="2" face="微软雅黑">现在可以开始解释一下什么是CPS变换了。CPS变换就是把所有g(f(x))都给改写成f(x, r=&gt;g(r))的过程。通俗一点讲，CPS变换就是帮你把那个同步的button1_Click给改写成异步的button2_Click的这个过程。尽管这么说可能不太严谨，因为button1_Click跟button2_Click所做的事情是不一样的，一个会让GUI卡成qq，另一个不会。但是我们讨论CPS变换的时候，我们讨论的是对代码结构的变换，而不是别的什么东西。</font></p>
<p><font size="2" face="微软雅黑">现在就是激动人心的一步了。既然CPS可以把返回值变换成lambda表达式，那反过来我们也可以把所有的<strong>以这种形式存在的</strong>lambda表达式都改写成返回值嘛。现在我们滚回去看一看button2_Click，会发现这个程序其实充满了下面的pattern：</font>
<div style="border-bottom:#cccccc 1px solid;border-left:#cccccc 1px solid;padding-bottom:5px;background-color:#f5f5f5;padding-left:5px;padding-right:5px;border-top:#cccccc 1px solid;border-right:#cccccc 1px solid;padding-top:5px"><pre><font size="2"><span style="color:#008000">//</span><span style="color:#008000"> lambda的参数名字故意起了跟前面的变量一样的名字（previousTask）因为其实他们就是同一个东西</span>
previousTask.ContinueWith(previousTask=&gt;<span style="color:#0000ff">new</span> MethodInvoker(()=&gt;</font><span style="color:#000000">
<font size="2">{
    </font></span><span style="color:#0000ff"><font size="2">try</font></span><span style="color:#000000">
<font size="2">    {
        continuation(previousTask.Result);
    }
    </font></span><span style="color:#0000ff"><font size="2">catch</font></span><font size="2"><span style="color:#000000">(Exception ex)
    {
        textBox1.Text</span>=</font><span style="color:#000000"><font size="2">ex.Message;
    }
})));</font></span></pre></div></p>
<p><font face="微软雅黑"><font size="2">我们可以“发明”一个语法来代表这个过程。C#用的是await关键字，那我们也来用await关键字。假设说上面的代码永远等价于下面的这个代码：</font>
<div style="border-bottom:#cccccc 1px solid;border-left:#cccccc 1px solid;padding-bottom:5px;background-color:#f5f5f5;padding-left:5px;padding-right:5px;border-top:#cccccc 1px solid;border-right:#cccccc 1px solid;padding-top:5px"><pre><span style="color:#0000ff"><font size="2">try</font></span><span style="color:#000000">
<font size="2">{
    </font></span><font size="2"><span style="color:#0000ff">var</span> result=<span style="color:#0000ff">await</span></font><font size="2"><span style="color:#000000"> previousTask;
    continuation(result);
}
</span><span style="color:#0000ff">catch</span></font><font size="2"><span style="color:#000000">(Exception ex)
{
    textBox1.Text</span>=</font><span style="color:#000000"><font size="2">ex.Message;
}</font></span></pre></div></font></p>
<p><font face="微软雅黑"><font size="2">两段代码的关系就跟i++;和i=i+1;一样是可以互相替换的，只是不同的写法而已。那我们就可以用相同的方法来把button2_Click给替换成下面的button3_Click了：</font>
<div style="border-bottom:#cccccc 1px solid;border-left:#cccccc 1px solid;padding-bottom:5px;background-color:#f5f5f5;padding-left:5px;padding-right:5px;border-top:#cccccc 1px solid;border-right:#cccccc 1px solid;padding-top:5px"><pre><font size="2"><span style="color:#0000ff">void</span> button3_Click(<span style="color:#0000ff">object</span></font><font size="2"><span style="color:#000000"> sender, EventArgs e)
{
    </span><span style="color:#0000ff">try</span></font><span style="color:#000000">
<font size="2">    {
        </font></span><font size="2"><span style="color:#0000ff">var</span> a=<span style="color:#0000ff">await</span></font><font size="2"><span style="color:#000000"> Http.DownloadAsync(url1);
        </span><span style="color:#0000ff">try</span></font><span style="color:#000000">
<font size="2">        {
            </font></span><font size="2"><span style="color:#0000ff">var</span> b=<span style="color:#0000ff">await</span></font><font size="2"><span style="color:#000000"> Http.DownloadAsync(url2);
            textBox1.Text</span>=a+</font><font size="2"><span style="color:#000000">b;
        }
        </span><span style="color:#0000ff">catch</span></font><font size="2"><span style="color:#000000">(Exception ex)
        {
            textBox1.Text</span>=</font><font size="2"><span style="color:#000000">ex.Message;
        }
    }
    </span><span style="color:#0000ff">catch</span></font><font size="2"><span style="color:#000000">(Exception ex)
    {
        textBox1.Text</span>=</font><span style="color:#000000"><font size="2">ex.Message;
    }
}</font></span></pre></div></font></p>
<p><font size="2" face="微软雅黑">聪明的读者立刻就想到了，两个try其实是重复的，那为什么不把他们合并成一个呢！当然我想告诉大家的是，异常是在不同的线程里面抛出来的，只是我们用CPS变换把代码“改写”成这种形式而已。理论上两个try是不能合并的。<font size="5"><strong>但是！</strong></font>我们的C#编译器君是很聪明的。正所谓<strong>语言的抽象高级了一点，那么编译器对你的代码也就理解得更多了一点</strong>。如果编译器发现你在try里面写了两个await，马上就明白了过来他需要帮你复制catch的部分——或者说他可以帮你自动的复制catch的部分，那情况就完全不同了，最后就可以写成：
<div style="border-bottom:#cccccc 1px solid;border-left:#cccccc 1px solid;padding-bottom:5px;background-color:#f5f5f5;padding-left:5px;padding-right:5px;border-top:#cccccc 1px solid;border-right:#cccccc 1px solid;padding-top:5px"><pre><span style="color:#008000"><font size="3">//</font></span><font size="3"><span style="color:#008000"> C#要求函数前面要加一个async来允许你在函数内使用await
</span><span style="color:#008000">//</span></font><font size="3"><span style="color:#008000"> 当然同时你的函数也就返回Task而不是void了
</span><span style="color:#008000">//</span></font><font size="3"><span style="color:#008000"> 不过没关系，C#的event也可以接受一个标记了async的函数，尽管返回值不一样
</span><span style="color:#008000">//</span><span style="color:#008000"> 设计语言这种事情就是牵一发而动全身呀，加个await连event都要改</span>
<span style="color:#0000ff">async</span> <span style="color:#0000ff">void</span> button4_Click(<span style="color:#0000ff">object</span></font><font size="3"><span style="color:#000000"> sender, EventArgs e)
{
    </span><span style="color:#0000ff">try</span></font><span style="color:#000000">
<font size="3">    {
        </font></span><font size="3"><span style="color:#0000ff">string</span> a=<span style="color:#0000ff">await</span></font><font size="3"><span style="color:#000000"> Http.DownloadAsync(url1);
        </span><span style="color:#0000ff">string</span> b=<span style="color:#0000ff">await</span></font><font size="3"><span style="color:#000000"> Http.DownloadAsync(url2);
        textBox1.Text</span>=a+</font><font size="3"><span style="color:#000000">b;
    }
    </span><span style="color:#0000ff">catch</span></font><font size="3"><span style="color:#000000">(Exception ex)
    {
        textBox1.Text</span>=</font><span style="color:#000000"><font size="3">ex.Message;
    }
}</font></span></pre></div></font></p>
<p><font size="2" face="微软雅黑">把两个await换成回调已经让我们写的够辛苦了，那么如果我们把await写在了循环里面，事情就不那么简单了。CPS需要把循环翻译成递归，那你就得把lambda表达时拿出来写成一个普通的函数——这样他就可以有名字了——然后才能递归（写出一个用于CPS的Y-combinator是一件很困难的事情，尽管并没有比Y-combinator本身困难多少）。这个例子就复杂到爆炸了，我在这里就不演示了。</font></p>
<p><font size="2" face="微软雅黑">总而言之，C#因为有了CPS变换（await），就可以把button4_Click帮你写成button3_Click然后再帮你写成button2_Click，最后把整个函数变成异步和回调的形式（真正的做法要更聪明一点，大家可以反编译去看）在异步回调的写法里面，exception和error code其实是一样的。但是CPS+exception和CPS+error code就跟单线程下面的exception和error code一样，有着重大的区别。这就是为什么文章一开始会说，我只会在带CPS变换的语言（Haskell/F#/etc）里面使用error code。</font></p>
<p><font size="2" face="微软雅黑">在这类语言里面利用相同的技巧，就可以不是异步的东西也用CPS包装起来，譬如说monadic parser combinator。至于你要选择monad还是comonad，基本上就是取决于你要自动提供错误处理还是要手动提供错误处理。像上面的Task.ContinueWith，是要求你手动提供错误处理的（因为你catch了之后可以干别的事情，Task无法自动替你选择最好的措施），所以他就把Task.ContinueWith写成了comonad的那个样子。</font></p>
<p><font size="2" face="微软雅黑">写到这里，不禁要同情写前端的那帮javascript和自以为可以写后端的node.js爱好者们，你们因为小小的eval的问题，不用老赵的windjs（windjs给javascript加上了await但是它不是一个altjs所以得显式调用eval），是一个多大的损失……</font></p><img src="http://www.cppblog.com/vczh/aggbug/200920.html" width="1" height="1"><br><br><div align="right"><a style="text-decoration:none" href="http://www.cppblog.com/vczh/">陈梓瀚(vczh)</a> 2013-06-10 15:01 <a href="http://www.cppblog.com/vczh/archive/2013/06/10/200920.html#Feedback" style="text-decoration:none">发表评论</a></div>