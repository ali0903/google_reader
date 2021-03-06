---
layout: post
title:  "Let&#39;s Make a Framework: Asynchronous Resource Loading"
date:   2011-09-22 07:00:00
author: 
categories: program
---

## Let&#39;s Make a Framework: Asynchronous Resource Loading
### by 
### at 2011-09-22 07:00:00
### original <http://feedproxy.google.com/~r/dailyjs/~3/GTPZFARk75o/framework-81>

<div>
<p><em>Let’s Make a Framework</em> is an ongoing series about building a JavaScript framework from the ground up.</p>
<p>These articles are tagged with <a href="http://dailyjs.com/tags.html#lmaf">lmaf</a>.  The project we’re creating is called <a href="http://github.com/alexyoung/turing.js">Turing</a>.  Documentation is available at <a href="http://turingjs.com/">turingjs.com</a>.</p>
</div>
<p>The use of asynchronous script loaders has become very popular over the last few years.  Rather than bundling assets into monolithic files to improve performance, it’s possible to coax browsers to load several files at once.  There are different approaches to this, but in general JavaScript APIs are used rather than <code>script</code> elements.</p>
<p>I want to look at how asynchronous script loaders are built, what HTML5 provides, and then build a new asynchronous script loader as an advanced client-side tutorial.  This tutorial series will be split over several weeks.</p>
<h3>Popular Libraries</h3>
<p>There are a huge amount of asynchronous script loaders out there.  The diversity is largely due to the complexity of the problem space — different situations can be optimised, and browser support requirements play a huge role in the internal design of these libraries.</p>
<p>The first library I found and used actively was <a href="http://labjs.com/">LABjs</a> (GitHub: <a href="https://github.com/getify/LABjs">getify / LABjs</a>, License: <em><span>MIT</span></em>) by Kyle Simpson.  To understand what LABjs does, consider the following <span>HTML</span>:</p>
<div><pre><code><span>&lt;script </span><span>src=</span><span>&quot;jquery.js&quot;</span><span>&gt;&lt;/script&gt;</span>
<span>&lt;script </span><span>src=</span><span>&quot;jquery-ui.js&quot;</span><span>&gt;&lt;/script&gt;</span>
<span>&lt;script </span><span>src=</span><span>&quot;my-script.js&quot;</span><span>&gt;&lt;/script&gt;</span>
</code></pre>
</div><p>In the <a href="http://www.w3.org/TR/html4/interact/scripts.html#adef-src-SCRIPT">HTML4 spec</a>, it says:</p>
<blockquote>
<p>All <code>SCRIPT</code> elements are evaluated in order as the document is loaded</p>
</blockquote>
<p>That means if a framework like jQuery is specified first, then subsequent scripts can safely depend on it being loaded.  The downside of this, however, is downloading each of these scripts will block the loading of other page components like images and stylesheets.  This can create a noticeable visual delay when the page is loaded.</p>
<p>Using LABjs can solve this problem:</p>
<div><pre><code><span>&lt;script </span><span>type=</span><span>&quot;text/javascript&quot;</span><span>&gt;</span>
   <span>$LAB</span>
   <span>.</span><span>script</span><span>(</span><span>&quot;jquery.js&quot;</span><span>).</span><span>wait</span><span>()</span>
   <span>.</span><span>script</span><span>(</span><span>&quot;jquery-ui.js&quot;</span><span>).</span><span>wait</span><span>()</span>
   <span>.</span><span>script</span><span>(</span><span>&quot;my-script.js&quot;</span><span>);</span>
<span>&lt;/script&gt;</span>
</code></pre>
</div><p>Here, every script will be loaded in parallel.  The <code>wait()</code> method will cause a <em>execution</em> to delay until the script has finished loading and has been parsed.</p>
<p>Technically, code within the application’s client-side JavaScript could call <code>$LAB.script()</code> whenever a dependency is needed.  This means a single page app could have a lower footprint until certain functionality is required.</p>
<p>Another popular library is <a href="http://requirejs.org/">RequireJS</a> (GitHub: <a href="https://github.com/jrburke/requirejs">jrburke / requirejs</a>, License: <em>new <span>BSD</span> and <span>MIT</span></em>) by James Burke.  RequireJS is very different to LABjs, with a large feature set:</p>
<ul>
	<li>Plugins</li>
	<li>Server-side bundling and optimisation</li>
	<li>Module support</li>
	<li>Remote service dependencies (<span>JSONP</span>)</li>
	<li>Script order control (through a plugin)</li>
	<li>Web Worker support</li>
</ul>
<p>The basic usage looks a bit like LABjs:</p>
<div><pre><code><span>&lt;script </span><span>src=</span><span>&quot;scripts/require.js&quot;</span><span>&gt;&lt;/script&gt;</span>
<span>&lt;script&gt;</span>
<span>require</span><span>([</span><span>&#39;script1.js&#39;</span><span>,</span> <span>&#39;script2.js&#39;</span><span>],</span> <span>function</span><span>(</span><span>someModule</span><span>)</span> <span>{</span>
  <span>// This optional callback runs when all dependencies are loaded</span>
<span>});</span>
<span>&lt;/script&gt;</span>
</code></pre>
</div><p>By embracing concepts like CommonJS Modules and the <a href="https://github.com/amdjs/amdjs-api/wiki/AMD">Asynchronous Module Definition</a> <span>API</span>, RequireJS offers a way to not only avoid blocking when loading scripts, but also add structure to projects.</p>
<p>Yet another approach used by <a href="http://www.modernizr.com/docs/#load">Modernizr</a> and <a href="http://yepnopejs.com/">yepnope.js</a> is to use a test to see if a script should be loaded:</p>
<div><pre><code><span>yepnope</span><span>({</span>
  <span>test</span><span>:</span> <span>window</span><span>.</span><span>JSON</span><span>,</span>
  <span>nope</span><span>:</span> <span>&#39;json2.js&#39;</span><span>,</span>
  <span>complete</span><span>:</span> <span>function</span> <span>()</span> <span>{</span>
    <span>var</span> <span>data</span> <span>=</span> <span>window</span><span>.</span><span>JSON</span><span>.</span><span>parse</span><span>(</span><span>&#39;{ &quot;json&quot; : &quot;string&quot; }&#39;</span><span>);</span>
  <span>}</span>
<span>});</span>
</code></pre>
</div><blockquote>
<p>The power here, is that you were able to parse a <span>JSON</span> string, but if your browser already had built in support for <span>JSON</span> parsing, it didn’t require you to load anything extra!</p>
</blockquote>
<h3>HTML5</h3>
<p>JavaScript-powered asynchronous loading isn’t the end of the story.  In HTML5, the <a href="http://dev.w3.org/html5/spec/Overview.html#the-script-element"><code>script</code></a> element has been updated to support <code>async</code> and <code>defer</code> attributes.  This is already available in recent WebKit-based browsers and Firefox 3.6.</p>
<p>These attributes effectively let us do what LABjs offers:</p>
<ul>
	<li>If <code>async</code> is present, the script will be executed asynchronously, as soon as it is available</li>
	<li>If <code>defer</code> is present, the script is executed when the page has finished parsing</li>
	<li>If neither are present, the script is fetched and executed immediately before the page has finished parsing</li>
</ul>
<p>From <a href="https://developer.mozilla.org/En/HTML/Element/Script">Mozilla’s documentation on the script element</a>:</p>
<blockquote>
<p>In older browsers that don’t support the async attribute, parser-inserted scripts block the parser; script-inserted scripts execute asynchronously in IE and WebKit, but synchronously in Opera and pre-4.0 Firefox.</p>
</blockquote>
<p>Tony Gentilcore compared this approach to JavaScript libraries on the Surfin’ Safari Blog:</p>
<blockquote>
<p>There are many clever techniques for working around this performance bottleneck, but they all involve extra code and browser-specific hacks. Instead, scripts which do not require synchronous execution can now be marked as either <code>async</code> or <code>defer</code>.</p>
</blockquote>
<h3><span>AMD</span></h3>
<p>As well as HTML5, asynchronous script loading is addressed by <a href="https://github.com/amdjs/amdjs-api/wiki/AMD">the Asynchronous Module Definition</a> <span>API</span>.  To understand why this is useful, consider <a href="http://www.commonjs.org/specs/modules/1.0/">CommonJS Modules</a>:</p>
<div><pre><code><span>var</span> <span>fn</span> <span>=</span> <span>require</span><span>(</span><span>&#39;my-module&#39;</span><span>).</span><span>fn</span><span>;</span>
<span>fn</span><span>();</span>
</code></pre>
</div><p>How would this work in a browser?  Assuming <code>'my-module'</code> is loaded through script tags, it would be difficult to control execution and parsing.  With <span>AMD</span> it’s possible to do this instead:</p>
<div><pre><code><span>define</span><span>(</span><span>&#39;my-module&#39;</span><span>,</span> <span>[</span><span>&#39;require&#39;</span><span>,</span> <span>&#39;exports&#39;</span><span>,</span> <span>&#39;mydep&#39;</span><span>],</span> <span>function</span> <span>(</span><span>require</span><span>,</span> <span>exports</span><span>,</span> <span>mydep</span><span>)</span> <span>{</span>
  <span>exports</span><span>.</span><span>fn</span> <span>=</span> <span>function</span><span>()</span> <span>{</span>
    <span>return</span> <span>&#39;hello from my-module:fn&#39;</span><span>;</span>
  <span>}</span>
<span>});</span>
</code></pre>
</div><h3>References</h3>
<ul>
	<li><a href="http://javascript.crockford.com/script.html">JavaScript and <span>HTML</span> Script Tags by Douglas Crockford</a></li>
	<li><a href="http://labjs.com/">LABjs</a></li>
	<li><a href="http://dev.w3.org/html5/spec/Overview.html#the-script-element">HTML5 script element specification</a></li>
	<li><a href="https://developer.mozilla.org/En/HTML/Element/Script">Mozilla script element documentation</a></li>
	<li><a href="http://www.webkit.org/blog/1395/running-scripts-in-webkit/">Running scripts in WebKit</a></li>
</ul><img src="http://feeds.feedburner.com/~r/dailyjs/~4/GTPZFARk75o" height="1" width="1">