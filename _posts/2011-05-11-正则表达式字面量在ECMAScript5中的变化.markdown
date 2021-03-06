---
layout: post
title:  "正则表达式字面量在ECMAScript5中的变化"
date:   2011-05-11 23:48:59
author: 秦歌
categories: program
---

## 正则表达式字面量在ECMAScript5中的变化
### by 秦歌
### at 2011-05-11 23:48:59
### original <http://dancewithnet.com/2011/05/11/what-is-new-about-regular-expression-literals-at-es5/>

<p>在<a href="http://book.douban.com/subject/3590768/">《JavaScript语言精粹》</a>的第72页有这样一段：</p>
<pre><code>用正则表达式字面量创建的RegExp对象来共享同一个单实例：
function make_a_matcher(  ) {
    return /a/gi;
}

var x = make_a_matcher(  );
var y = make_a_matcher(  );

// 注意：x 和 y 是同一个对象！

x.lastIndex = 10;

document.writeln(y.lastIndex);    // 10</code></pre>
<p>当你<a href="http://dancewithnet.com/lab/2011/regexp-literals/">在浏览器中运行这段代码</a>时，你会发现IE6-IE9、FireFox4、Chrome10、Safari5输出都是0，Firefox 3.6.16输出是10，原因可以在<a href="http://www.ecma-international.org/publications/files/ECMA-ST/ECMA-262.pdf">ECMAScript5规范</a>第24页和第247页找到：</p>
<p><span></span></p>
<blockquote><p>
A regular expression literal is an input element that is converted to a RegExp object (see 15.10) each time the literal is evaluated. Two regular expression literals in a program evaluate to regular expression objects that never compare as === to each other even if the two literals’ contents are identical. A RegExp object may also be created at runtime by new RegExp (see 15.10.4) or calling the RegExp constructor as a function (15.10.3).</p>
<p>7.8.5: Regular expression literals now return a unique object each time the literal is evaluated. This change is detectable by any programs that test the object identity of such literal values or that are sensitive to the shared side effects.</p></blockquote>
<p>也就是说在ECMAScript3规范中，用正则表达式创建的RegExp对象会共享同一个实例，而在ECMAScript5中则是两个独立的实例。《JavaScript语言精粹》出版时ECMAScript5还没有发布，在这个问题上书和ECMAScript3标准保持了一致。FireFox3.6遵循了ECMAScript3标准，所以结果与书中一致，而最新的Firefox4、Chrome和Safari5都遵循ECMAScript5标准，至于IE6-IE8都没有很好的遵循ECMAScript3标准，不过在这个问题上反而处理对了。很明显ECMAScript5的规范更符合开发者的期望，那就是相同的正则表达式字面量创建独立的RegExp对象会有不同的lastIndex，才方便分别处理。</p>
<p>在ECMAScript5规范的第247页还有两条来说明ECMAScript5和ECMAScript3在正则表达式字面量上的改变：</p>
<blockquote><p>7.8.5: Edition 5 requires early reporting of any possible RegExp constructor errors that would be produced when converting a RegularExpressionLiteral to a RegExp object. Prior to Edition 5 implementations were permitted to defer the reporting of such errors until the actual execution time creation of the object.<br>
7.8.5: In Edition 5 unescaped “/” characters may appear as a CharacterClass in a regular expression literal. In Edition 3 such a character would have been interpreted as the final character of the literal.</p></blockquote>
<p>第1个是在ECMAScript5中正则表达式字面量转化为RegExp对象时，任何RegExp构造器的错误都会尽早报告，而在之前的规范中是只有对象创建真正执行时才会报错。</p>
<p>第2个是说在ECMAScript5的正则表达式字面量中，未转义的正斜杠“/”可以直接用在正则表达式字符类中。而在ECMAScript3中它只能作为正则表达式字面量的开始和结束字符。<a href="http://dancewithnet.com/lab/2011/regexp-literals/">从IE6-IE9、Firefox3.6-Firefox4.0、Chrome和Safari都可以直接把未转义的正斜杠“/”用在正则表达式字符类中</a>。如：</p>
<pre><code>var my_regexp = /([8/5+4]*).{3}/g;
var str = &#39;8/5+4 is what!&#39;;

var result = my_regexp.exec(str); // the same in IE6-9,FF3.6-4.0,Chrome,Safari

for(var i = 0,n = result.length; i &lt; n; ++i){
  document.writeln(result[i]);
}
result[0] = 8/5+4 is
result[1] = 8/5+4
</code></pre>
<p>在《JavaScript语言精粹》第76页也指明在正则表达式的字符类中使用正斜杠“/”需要转义，也是基于ECMAScript3规范。由于正则表达式中需要转义的特殊字符比较多，当心存疑虑时对任何特殊字符都可以使用反斜杠“\”来使其字面化确保安全，不过这个规则不适宜字母和数字。</p>
<p>正则表达式字面量从ECMAScript3到ECMAScript5的改变也蛮符合<a href="http://www.cn-cuckoo.com/2010/10/21/the-design-of-html5-2151.html">HTML5设计原理</a>中提到的2条。一条是“一旦遇到冲突，最终用户优先，其次是作者，其次是实现者，其次标准制定者，最后才是理论上的完满”，另一条是“支持已有内容”。</p>
<p>最后推荐一下<a href="http://xregexp.com/">XRegExp</a>，它是一个非常优秀的正则表达式JavaScript库，兼容多个主流浏览器、ECMAScript3和ECMAScript5。<br>
<ul><li><a href="http://dancewithnet.com/2005/09/22/where-is-liao-peking-university-speech/" title="李敖北大演讲稿哪儿去了？">李敖北大演讲稿哪儿去了？</a></li><li><a href="http://dancewithnet.com/2006/09/28/2006-sun-tech-days-china/" title="2006 Sun Tech Days China">2006 Sun Tech Days China</a></li><li><a href="http://dancewithnet.com/2008/05/13/wrong-place-wrong-time-right-people/" title="天不时，地不利，人要和！">天不时，地不利，人要和！</a></li><li><a href="http://dancewithnet.com/2005/12/24/say-safe-on-chirstmas-eve/" title="平安夜、报平安、夜平安！">平安夜、报平安、夜平安！</a></li><li><a href="http://dancewithnet.com/2006/03/31/microsoft-bugs/" title="微软的便便">微软的便便</a></li><li><a href="http://dancewithnet.com/2007/04/10/scalded-pig-femur-with-yam/" title="山药炖汤骨">山药炖汤骨</a></li><li><a href="http://dancewithnet.com/2005/01/19/suiyue-wedding-feast/" title="随月喜筵">随月喜筵</a></li><li><a href="http://dancewithnet.com/2004/12/17/unripe-candidates/" title="未准备的应聘">未准备的应聘</a></li><li><a href="http://dancewithnet.com/2005/10/16/life-do-not-support-the-dread/" title="生命不能承载之重－－恐惧">生命不能承载之重－－恐惧</a></li><li><a href="http://dancewithnet.com/2008/01/13/travel-to-hot-spring-and-skiing-by-early-2008/" title="2008年初，温泉滑雪之旅">2008年初，温泉滑雪之旅</a></li></ul></p>