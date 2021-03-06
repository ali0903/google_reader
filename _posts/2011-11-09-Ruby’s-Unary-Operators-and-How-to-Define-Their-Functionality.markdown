---
layout: post
title:  "Ruby’s Unary Operators and How to Define Their Functionality"
date:   2011-11-09 01:50:38
author: Peter Cooper
categories: program
---

## Ruby’s Unary Operators and How to Define Their Functionality
### by Peter Cooper
### at 2011-11-09 01:50:38
### original <http://feedproxy.google.com/~r/RubyInside/~3/B4Wup3_1oH4/rubys-unary-operators-and-how-to-redefine-their-functionality-5610.html>

<p><img src="http://www.rubyinside.com/wp-content/uploads/2011/11/unary.gif" alt="" title="unary" width="140" height="140" style="float:right;margin-left:18px;margin-bottom:18px;border:1px solid #458">In math, a <a href="http://en.wikipedia.org/wiki/Unary_operation">unary operation</a> is an operation with a single input. <strong>In Ruby, a unary operator is an operator which only takes a single 'argument' in the form of a <em>receiver</em>.</strong> For example, the <code>-</code> on <code>-5</code> or <code>!</code> on <code>!true</code>.</p>
<p>In contrast, a <em>binary operator</em>, such as in <code>2 + 3</code>, deals with <em>two</em> arguments. Here, 2 and 3 (which become one receiver and one argument in a method call to <code>+</code>).</p>
<p>Ruby only has a handful of <em>unary</em> operators, and while it's common to redefine binary operators like <code>+</code> or <code>[]</code> to give your objects some added syntactic sugar, unary operators are less commonly redefined. In my experience, many Rubyists aren't aware that unary operators <em>can</em> be redefined, so this article demonstrates how.</p>
<h3>A Quick Example with -@</h3>
<p>Let's ease into things with the <code>-</code> unary operator. The <code>-</code> unary operator is <strong>not the same thing as the - binary operator</strong> (where a binary operator has two operants). By default, the <code>-</code> unary operator is used as notation for a negative number, as in <code>-25</code>, whereas the <code>-</code> binary operator performs subtraction, as in <code>50 - 25</code>. <strong>While they look similar, these are different concepts, different operators, and resolve to different methods in Ruby.</strong></p>
<p>Using the - unary operator on a string in <em>irb</em>:</p>
<pre><code>ruby-1.9.3-p0 :001 &gt; <strong>-"this is a test"</strong>
NoMethodError: undefined method `<strong>-@</strong>' for "this is a test":String</code></pre>
<p>The String class doesn't have unary <code>-</code> defined but irb gives us a clue on where to go. Due to the conflict between the unary and binary versions of <code>-</code>, the unary version has a suffix of @. This helps us come up with a solution:</p>
<pre><code>str = &quot;This is my STRING!&quot;

def str.-@
  downcase
end

p str     # =&gt; &quot;This is my STRING!&quot;
p -str    # =&gt; &quot;this is my string!&quot;</code></pre>
<p>We've defined the unary <code>-</code> operator by defining its associated <code>-@</code> method to translate its receiving object to lower case.</p>
<h3>Some Other Operators: +@, ~, ! (and not)</h3>
<p>Let's try a larger example where we subclass String and add our own versions of several other easily overridden unary operators:</p>
<div>
<pre><span>class</span> <span>MagicString</span> <span>&lt;</span> <span>String</span>
  <span>def</span> <span>+@</span>
    <span>upcase</span>
  <span>end</span>

  <span>def</span> <span>-@</span>
    <span>downcase</span>
  <span>end</span>

  <span>def</span> <span>!</span>
    <span>swapcase</span>
  <span>end</span>

  <span>def</span> <span>~</span>
    <span># Do a ROT13 transformation - http://en.wikipedia.org/wiki/ROT13</span>
    <span>tr</span> <span>&#39;A-Za-z&#39;</span><span>,</span> <span>&#39;N-ZA-Mn-za-m&#39;</span>
  <span>end</span>
<span>end</span>

<span>str</span> <span>=</span> <span>MagicString</span><span>.</span><span>new</span><span>(</span><span>&quot;This is my string!&quot;</span><span>)</span>
<span>p</span> <span>+</span><span>str</span>         <span># =&gt; &quot;THIS IS MY STRING!&quot;</span>
<span>p</span> <span>!</span><span>str</span>         <span># =&gt; &quot;tHIS IS MY STRING!&quot;</span>
<span>p</span> <span>(</span><span>not</span> <span>str</span><span>)</span>    <span># =&gt; &quot;tHIS IS MY STRING!&quot;</span>
<span>p</span> <span>~</span><span>str</span>         <span># =&gt; &quot;Guvf vf zl fgevat!&quot;</span>
<span>p</span> <span>+~</span><span>str</span>        <span># =&gt; &quot;GUVF VF ZL FGEVAT!&quot;</span>
<span>p</span> <span>!</span><span>(</span><span>~</span><span>str</span><span>)</span>      <span># =&gt; &quot;gUVF VF ZL FGEVAT!&quot;</span>
</pre>
</div>
<p>This time we've not only redefined <code>-/-@</code>, but the <code>+</code> unary operator (using the <code>+@</code> method), <code>!</code> and <code>not</code> (using the <code>!</code> method), and <code>~</code>.</p>
<p>I'm not going to explain the example in full because it's as simple as I could get it while still being more illustrative than reams of text. Note what operation each unary operator is performing and see how that relates to what is called and what results in the output.</p>
<p><em>Note: You cannot redefine <code>!</code> in Ruby 1.8. This code performs as-is in Ruby 1.9 only. If you remove the <code>!</code> method and examples, the code otherwise works in Ruby 1.8. There's a longer 1.8 and 1.9 example later in this post.</em></p>
<h3>Special Cases: &amp; and *</h3>
<p><code>&amp;</code> and <code>*</code> are also unary operators in Ruby, but they're special cases, bordering on 'mysterious syntax magic.' What do they do?</p>
<h4>&amp; and to_proc</h4>
<p>Reg Braithwaite's <a href="http://weblog.raganwald.com/2008/06/what-does-do-when-used-as-unary.html">The unary ampersand in Ruby</a> post gives a great explanation of <code>&amp;</code>, but in short &amp; can turn objects into procs/blocks by calling the <code>to_proc</code> method upon the object. For example:</p>
<pre><code>p [&#39;hello&#39;, &#39;world&#39;].map(&amp;:reverse)  # =&gt; [&quot;olleh&quot;, &quot;dlrow&quot;]</code></pre>
<p>Enumerable#<code>map</code> usually takes a block instead of an argument, but <code>&amp;</code> calls Symbol#<code>to_proc</code> and generates a special proc object for the <code>reverse</code> method. This proc becomes the block for the <code>map</code> and thereby reverses the strings in the array.</p>
<p>You could, therefore, 'override' the <code>&amp;</code> unary operator (not to be confused by the equivalent binary operator!) by defining <code>to_proc</code> on an object, with the only restriction being that you <em>must</em> return a Proc object for things to behave. You'll see an example of this later on.</p>
<h4>* and splatting</h4>
<p>There's <a href="http://endofline.wordpress.com/2011/01/21/the-strange-ruby-splat/">a lot of magic to splatting</a> but in short,  <code>*</code> can be considered to be a unary operator that will 'explode' an array <em>or</em> an object that implements <code>to_a</code> and returns an array.</p>
<p>To override the unary <code>*</code> (and not the binary * - as in <code>20 * 32</code>), then, you can define a <code>to_a</code> method and return an array. The array you return, however, will face further consequences thanks to *'s typical behavior!</p>
<h3>A Full Example for Ruby 1.8 and Ruby 1.9</h3>
<p>We've reached the end of our quick tour through Ruby's unary operators, so I wanted to provide an example that shows how to override (or partially override) them that should stand as its own documentation:</p>
<div>
<pre><span>class</span> <span>MagicString</span> <span>&lt;</span> <span>String</span>
  <span>def</span> <span>+@</span>
    <span>upcase</span>
  <span>end</span>

  <span>def</span> <span>-@</span>
    <span>downcase</span>
  <span>end</span>

  <span>def</span> <span>~</span>
    <span># Do a ROT13 transformation - http://en.wikipedia.org/wiki/ROT13</span>
    <span>tr</span> <span>&#39;A-Za-z&#39;</span><span>,</span> <span>&#39;N-ZA-Mn-za-m&#39;</span>
  <span>end</span>

  <span>def</span> <span>to_proc</span>
    <span>Proc</span><span>.</span><span>new</span> <span>{</span> <span>self</span> <span>}</span>
  <span>end</span>

  <span>def</span> <span>to_a</span>
    <span>[</span><span>self</span><span>.</span><span>reverse</span><span>]</span>
  <span>end</span>

  <span>if</span> <span>RUBY_VERSION</span> <span>&gt;</span> <span>&quot;1.9.0&quot;</span>
    <span>eval</span> <span>%{def !</span>
<span>      swapcase</span>
<span>    end}</span>
  <span>end</span>
<span>end</span>

<span>str</span> <span>=</span> <span>MagicString</span><span>.</span><span>new</span><span>(</span><span>&quot;This is my string!&quot;</span><span>)</span>
<span>p</span> <span>+</span><span>str</span>                   <span># =&gt; &quot;THIS IS MY STRING!&quot;</span>
<span>p</span> <span>~</span><span>str</span>                   <span># =&gt; &quot;Guvf vf zl fgevat!&quot;</span>
<span>p</span> <span>+~</span><span>str</span>                  <span># =&gt; &quot;GUVF VF ZL FGEVAT!&quot;</span>
<span>p</span> <span>%w{a b}</span><span>.</span><span>map</span> <span>&amp;</span><span>str</span>       <span># =&gt; [&quot;This is my string!&quot;, &quot;This is my string!&quot;]</span>
<span>p</span> <span>*</span><span>str</span>                   <span># =&gt; &quot;!gnirts ym si sihT&quot;</span>

<span>if</span> <span>RUBY_VERSION</span> <span>&gt;</span> <span>&quot;1.9.0&quot;</span>
  <span>p</span> <span>!</span><span>str</span>                   <span># =&gt; &quot;tHIS IS MY STRING!&quot;</span>
  <span>p</span> <span>(</span><span>not</span> <span>str</span><span>)</span>              <span># =&gt; &quot;tHIS IS MY STRING!&quot;</span>
  <span>p</span> <span>!</span><span>(</span><span>~</span><span>str</span><span>)</span>                <span># =&gt; &quot;gUVF VF ZL FGEVAT!&quot;</span>
<span>end</span>
</pre>
</div>
<p>It's almost a cheat sheet of unary operators :-)</p>
<h3>A Further Example: The TestRocket</h3>
<p><a href="https://github.com/peterc/testrocket">TestRocket</a> is a tiny testing library I built for fun a few months ago. It leans heavily on unary operators. For example, you can write tests like this:</p>
<pre><code>+-&gt; { Die.new(2) }
--&gt; { raise }
+-&gt; { 2 + 2 == 4 }

# These two tests will deliberately fail
+-&gt; { raise }
--&gt; { true }

# A &#39;pending&#39; test
~-&gt; { &quot;this is a pending test&quot; }

# A description
!-&gt; { &quot;use this for descriptive output and to separate your test parts&quot; }</code></pre>
<p>The <code>-&gt; { }</code> sections are just Ruby 1.9 style 'stabby lambdas' but I've (with assistance from Christoph Grabo) added unary methods to them so that you can prefix <code>+</code>, <code>-</code>, <code>~</code>, or <code>!</code> to get different behaviors.</p>
<p>Hopefully you can come up with some more useful application for unary methods on your own objects, of course!</p>
<div>
<a href="http://feeds.feedburner.com/~ff/RubyInside?a=B4Wup3_1oH4:ia34VeyLBSg:qj6IDK7rITs"><img src="http://feeds.feedburner.com/~ff/RubyInside?d=qj6IDK7rITs" border="0"></a> <a href="http://feeds.feedburner.com/~ff/RubyInside?a=B4Wup3_1oH4:ia34VeyLBSg:3H-1DwQop_U"><img src="http://feeds.feedburner.com/~ff/RubyInside?i=B4Wup3_1oH4:ia34VeyLBSg:3H-1DwQop_U" border="0"></a>
</div><img src="http://feeds.feedburner.com/~r/RubyInside/~4/B4Wup3_1oH4" height="1" width="1">