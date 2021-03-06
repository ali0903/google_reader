---
layout: post
title:  "CoffeeScript编译器（一）"
date:   2012-10-29 09:56:56
author: island205
categories: program
---

## CoffeeScript编译器（一）
### by island205
### at 2012-10-29 09:56:56
### original <http://island205.com/2012/10/29/coffeescript%e7%bc%96%e8%af%91%e5%99%a8%ef%bc%88%e4%b8%80%ef%bc%89/>

<p><a href="http://coffeescript.org/">CoffeeScript</a>越来越流行，接触CoffeeScript也已经一年有余，最近正在看它的编译器实现，在这里把体会记录下来。</p>
<p>目前，CoffeeScript编译器是使用CoffeeScript编写的，很多人会问，这是蛋生鸡呢，还是鸡生蛋？ 其实CoffeeScript编译器最初是使用Ruby编写的，CoffeeScript团队于2010年2月21号发布了<a href="http://coffeescript.org/#changelog">v0.5.0</a>，使用CoffeeScript重写了编译器。<span></span></p>
<h1>CoffeeScript编译器的整体架构</h1>
<p>就目前的CoffeeScript编译器来说，整体架构如下图：</p>
<div>
<pre><code>
       -------------     -----------------
       -           -     -               -
cs ---&gt;-   Lexer   - ---&gt;- Parser(Jison) ----&gt;AST---&gt;node.compile()---&gt;js
       -           -     -               -
       -------------     -----------------
                                 ^
                                 -
                                 -
                           -------------
                           -           -
                           -   Nodes   -
                           -           -
                           -------------

</code></pre>
</div>
<p>概括来讲（通常了解点编译器的人都会说的），CoffeeScript编译器也不例外，主要也分为三大部分：词法解析器（Lexer），语法解析器（Parser）以及构造AST（抽象语法树），遍历语法树生成相应的代码。</p>
<p>下面首先对这三块进行简单的介绍：</p>
<ul>
<li><strong>Lexer(<a href="http://coffeescript.org/documentation/docs/lexer.html">lexer.coffee</a>)</strong>：词法解析器就是接受输入的cs代码，然后输出token。虽然CoffeeScript语法解析是基于Jison的，Jison同时也可以做词法解析，但CoffeeScript却自己实现了词法解析器。为什么CoffeeScript要自己实现一个Lexer呢？因为CoffeeScript的语法是LALR(1)的，无法满足CoffeeScript简洁语法的需要，因此需要自定义Lexer，且使用<a href="http://coffeescript.org/documentation/docs/rewriter.html">rewriter.coffee</a>来处理这些语法，在token流中插入补充token；</li>
<li><strong>Parser(<a href="http://coffeescript.org/documentation/docs/grammar.html">grammar.coffee</a>)</strong>：语法解析器就是根据CoffeeScript的语法规则，接受Lexer的输出的token作为输入产生AST。生成AST并不是所有Parser都需要做的。在语法解析的过程中，使用不同的语法规则进行移入token，规约即可。只是可以定义规约时的操作，结合<a href="http://coffeescript.org/documentation/docs/nodes.html">nodes.coffee</a>（其中定义了AST不同类型的节点）生成CoffeeScript所需的AST；<br>
在源码中并没有一个名为<code>parser.coffee</code>的文件。Parser是使用Jison基于<code>grammar.coffee</code>生成的；</li>
<li><strong>AST</strong>：抽象语法树是Parser的产物，也就是<code>nodes.coffee</code>所定义的诸多节点组成的一棵树。每个节点都有<code>compile</code>和<code>compileNode</code>方法。从根节点开始，通过<code>compile</code>方法的调用，深度优先遍历整个AST，从而生成js代码。</li>
</ul>
<p>调用CoffeeScript提供出来的接口，可以看到产生的一些中间变量，例如：</p>
<div>
<pre><code>code = &#39;&#39;&#39;
do -&gt;
    String::trim || String::trim = -&gt;
        @replace /^\s+|\s+$/g,&#39;&#39;
    return
&#39;&#39;&#39;
###
[
    [&quot;UNARY&quot;, &quot;do&quot;, 0],
    [&quot;-&gt;&quot;, &quot;-&gt;&quot;, 0],
    [&quot;INDENT&quot;, 4, 1],
    [&quot;IDENTIFIER&quot;, &quot;String&quot;, 1],
    [&quot;::&quot;, &quot;::&quot;, 1],
    [&quot;IDENTIFIER&quot;, &quot;trim&quot;, 1],
    [&quot;LOGIC&quot;, &quot;||&quot;, 1],
    [&quot;IDENTIFIER&quot;, &quot;String&quot;, 1],
    [&quot;::&quot;, &quot;::&quot;, 1],
    [&quot;IDENTIFIER&quot;, &quot;trim&quot;, 1],
    [&quot;=&quot;, &quot;=&quot;, 1],
    [&quot;-&gt;&quot;, &quot;-&gt;&quot;, 1],
    [&quot;INDENT&quot;, 4, 2],
    [&quot;@&quot;, &quot;@&quot;, 2],
    [&quot;IDENTIFIER&quot;, &quot;replace&quot;, 2],
    [&quot;CALL_START&quot;, &quot;(&quot;, 2],
    [&quot;REGEX&quot;, &quot;/^s+|s+$/g&quot;, 2],
    [&quot;,&quot;, &quot;,&quot;, 2],
    [&quot;STRING&quot;, &quot;&#39;&#39;&quot;, 2],
    [&quot;CALL_END&quot;, &quot;)&quot;, 3],
    [&quot;OUTDENT&quot;, 4, 3],
    [&quot;TERMINATOR&quot;, &quot;\n&quot;, 3],
    [&quot;RETURN&quot;, &quot;return&quot;, 3],
    [&quot;OUTDENT&quot;, 4, 3],
    [&quot;TERMINATOR&quot;, &quot;\n&quot;, 3]
]
###
console.log JSON.stringify CoffeeScript.tokens code
###
{
    &quot;expressions&quot;: [{
        &quot;args&quot;: [],
        &quot;isNew&quot;: false,
        &quot;isSuper&quot;: false,
        &quot;variable&quot;: {
            &quot;params&quot;: [],
            &quot;body&quot;: {
                &quot;expressions&quot;: [{
                    &quot;operator&quot;: &quot;||&quot;,
                    &quot;first&quot;: {
                        &quot;base&quot;: {
                            &quot;value&quot;: &quot;String&quot;
                        },
                        &quot;properties&quot;: [{
                            &quot;name&quot;: {
                                &quot;value&quot;: &quot;trim&quot;,
                                &quot;asKey&quot;: true
                            },
                            &quot;proto&quot;: &quot;.prototype&quot;,
                            &quot;soak&quot;: false
                        }]
                    },
                    &quot;second&quot;: {
                        &quot;variable&quot;: {
                            &quot;base&quot;: {
                                &quot;value&quot;: &quot;String&quot;
                            },
                            &quot;properties&quot;: [{
                                &quot;name&quot;: {
                                    &quot;value&quot;: &quot;trim&quot;,
                                    &quot;asKey&quot;: true
                                },
                                &quot;proto&quot;: &quot;.prototype&quot;,
                                &quot;soak&quot;: false
                            }]
                        },
                        &quot;value&quot;: {
                            &quot;params&quot;: [],
                            &quot;body&quot;: {
                                &quot;expressions&quot;: [{
                                    &quot;args&quot;: [{
                                        &quot;base&quot;: {
                                            &quot;value&quot;: &quot;/^s+|s+$/g&quot;
                                        },
                                        &quot;properties&quot;: []
                                    }, {
                                        &quot;base&quot;: {
                                            &quot;value&quot;: &quot;&#39;&#39;&quot;
                                        },
                                        &quot;properties&quot;: []
                                    }],
                                    &quot;soak&quot;: false,
                                    &quot;isNew&quot;: false,
                                    &quot;isSuper&quot;: false,
                                    &quot;variable&quot;: {
                                        &quot;base&quot;: {
                                            &quot;value&quot;: &quot;this&quot;
                                        },
                                        &quot;properties&quot;: [{
                                            &quot;name&quot;: {
                                                &quot;value&quot;: &quot;replace&quot;,
                                                &quot;asKey&quot;: true
                                            },
                                            &quot;proto&quot;: &quot;&quot;,
                                            &quot;soak&quot;: false
                                        }],
                                        &quot;this&quot;: true
                                    }
                                }]
                            },
                            &quot;bound&quot;: false
                        }
                    },
                    &quot;flip&quot;: false
                }, {}]
            },
            &quot;bound&quot;: false
        },
        &quot;do&quot;: true
    }]
}
###
console.log JSON.stringify CoffeeScript.nodes code
</code></pre>
</div>
<p>这一部分就说到这里，之后会更加深入到CoffeeScript的各个实现细节之中。</p>