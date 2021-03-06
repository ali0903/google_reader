---
layout: post
title:  "AMD versus CJS. What’s the best format?"
date:   2011-10-01 12:46:23
author: John Hann
categories: program
---

## AMD versus CJS. What’s the best format?
### by John Hann
### at 2011-10-01 12:46:23
### original <http://unscriptable.com/2011/09/30/amd-versus-cjs-whats-the-best-format/>

<p>JavaScript Harmony will introduce native modules into JavaScript.  Finally.  And when IE9 finally dies a slow death, we’ll be able to use native modules in the browser.  <em>Yay corporate support contracts.</em>  In the mean time, there are two competing standards for modules: AMD and CJS Modules 1.1.1.  Which one is better?  Which one should you be using?</p>
<p>I’ve got an opinion and it may surprise you.</p>
<p><span></span></p>
<h2>What’s the difference?</h2>
<p>If you’re not aware of either format, I suggest you start by reading about <a href="https://github.com/amdjs/amdjs-api/wiki">Asynchronous Module Definition</a> and <a href="http://wiki.commonjs.org/wiki/Modules/1.1">CommonJS Modules 1.1</a>.</p>
<p>In a nutshell, AMD is designed for browser environments and CommonJS is targeted at server-side environments.  This fundamental difference has deep repercussions on the formats.  Here’s a brief (and probably incomplete) summary of the differences between these two environments:</p>
<ul>
<li>Scoping</li>
<li>Distance</li>
<li>Asynchrony</li>
</ul>
<h3>Scoping</h3>
<p>JavaScript that runs in the browser has no way to isolate code completely.  Sure, you can put your code in a function so your <code>var</code> statements don’t leak variables into the global scope, but it’s easy to make mistakes.  Who hasn’t done this? </p>
<p><code>var a = b = 0;</code></p>
<p>AMD can’t efficiently solve this problem, so it doesn’t.  Instead, it encourages local scoping by bringing external resources into a local function scope.  </p>
<p>CJS arose from server-side environments, such as node, that started with a clean slate.  In these environments, there is no global scope.</p>
<h3>Distance</h3>
<p>In the browser, most resources are remote, on  a server somewhere.  If you’re lucky, your resources are cached.  Regardless, it can take time to fetch a resource.  If your code depends on a function in another file, you can’t know for sure if it’s already loaded or if it may take a few seconds to arrive.  You can’t write “normal” code if you don’t know if the function is defined yet!</p>
<p>CJS gets around this problem by assuming that all resources are local and can be fetched in msec or, more likely, <em>microseconds</em>.</p>
<h3>Asynchrony</h3>
<p>A direct result of the distance issue is the need to run tasks asynchronously.  Browsers run JavaScript in a single thread.  If that thread were to wait for a remote resource to be fetched, nothing else could execute and the browser would appear to be unresponsive to the user.  </p>
<p>We get around this by initiating the fetch and requesting a callback when the resource is ready.  The callback is typically in the form of a function, and is typically called … wait for it .. a <em>callback function</em>.  (<em>JS devs are so freakin smart I tell you.</em>)</p>
<p>Node encourages asynchrony by emphasizing an event-driven programming model.  However, the node creators punted when designing the API to fetch modules and provided the same stale File I/O APIs for fetching other resources.  Personally, I think it would have strengthened the event-driven philosophy if they extended the event-driven philosophy to modules and file-based resources.  </p>
<p>In a nutshell, CJS fetches module resources synchronously.  I guess they thought it makes sense to not introduce the complexity of asynchrony if resources are only microseconds away.</p>
<h2>A Closer look at the formats</h2>
<p>So how do the rival formats handle their diverse target environments?  Let’s look at AMD:</p>

<div><div><pre style="font-family:monospace"><span style="color:#006600;font-style:italic">// define() is a necessary wrapper.</span>
define<span style="color:#009900">(</span>
    <span style="color:#006600;font-style:italic">// dependencies are specified in advance.</span>
    <span style="color:#009900">[</span><span style="color:#3366cc">'pkgA/modA'</span><span style="color:#339933">,</span> <span style="color:#3366cc">'pkgA/modB'</span><span style="color:#339933">,</span> <span style="color:#3366cc">'pkgZ/modC'</span><span style="color:#009900">]</span><span style="color:#339933">,</span>
    <span style="color:#006600;font-style:italic">// the module is declared within a definition function.</span>
    <span style="color:#006600;font-style:italic">// dependencies are mapped into function parameters.</span>
    <span style="color:#003366;font-weight:bold">function</span> <span style="color:#009900">(</span>modA<span style="color:#339933">,</span> modB<span style="color:#339933">,</span> modC<span style="color:#009900">)</span> <span style="color:#009900">{</span>
        <span style="color:#006600;font-style:italic">// inside here is the module's code.</span>
        <span style="color:#006600;font-style:italic">// the module is exported to the outside world via the </span>
        <span style="color:#006600;font-style:italic">// the definition function's returned value.</span>
        <span style="color:#000066;font-weight:bold">return</span> modC <span style="color:#339933">?</span> modA <span style="color:#339933">:</span> modB<span style="color:#339933">;</span>
    <span style="color:#009900">}</span>
<span style="color:#009900">)</span><span style="color:#339933">;</span></pre></div></div>

<p>Contrast this with CJS:</p>

<div><div><pre style="font-family:monospace"><span style="color:#006600;font-style:italic">// there is no explicit wrapper, we just write the module's code.</span>
<span style="color:#006600;font-style:italic">// this is not global scope! the scope is limited to this file only.</span>
<span style="color:#006600;font-style:italic">// &quot;free&quot; variables (`require`, `module`, and `exports`) are </span>
<span style="color:#006600;font-style:italic">// declared to help us define our module and fetch resources.</span>
 
<span style="color:#006600;font-style:italic">// dependencies are specified as needed</span>
<span style="color:#003366;font-weight:bold">var</span> modC <span style="color:#339933">=</span> require<span style="color:#009900">(</span><span style="color:#3366cc">'pkgZ/modC'</span><span style="color:#009900">)</span><span style="color:#339933">;</span>
 
<span style="color:#006600;font-style:italic">// the module is exported by decorating the `exports` free variable.</span>
exports.<span style="color:#660066">foo</span> <span style="color:#339933">=</span> modC <span style="color:#339933">?</span> require<span style="color:#009900">(</span><span style="color:#3366cc">'pkgA/modA'</span><span style="color:#009900">)</span> <span style="color:#339933">:</span> require<span style="color:#009900">(</span><span style="color:#3366cc">'pkgA/modB'</span><span style="color:#009900">)</span><span style="color:#339933">;</span></pre></div></div>

<p>AMD handles the asynchrony by having us declare our module inside of the definition function.  The definition function is essentially a callback function.  It’s called when all of our dependencies have been resolved.  Some of these may require fetching.  The execution of our module’s code is delayed until all of our resources are available.  (Note: you can execute code earlier if you simply place it outside of the <code>define()</code>.)</p>
<p>CJS on the other hand prefers to execute the module code as soon as possible, but load/execute the module dependencies only when needed.  In the sample code above, only one of ‘pkgA/modA’ or ‘pkgA/modB’ will be loaded and executed.  There’s an added bonus here!  Our code could be significantly more efficient if the environment doesn’t have to load and execute all of the modules.  </p>
<p>This is a clear win for CJS!  Or is it?  In the browser, there’s no way to have it both ways with this syntax.  We’d either have to fetch the dependency in advance or fetch it synchronously.</p>
<p>(Ha ha! AMD has an solution to this problem that works for many, but not all, use cases.  It’s called <a href="https://github.com/phiggins42/has.js/">has.js</a> and it has a sister plugin, called “has!”. (The exclamation mark is part of the plugin name; I didn’t append my emotion to it.  Well, it is my emotion, but that’s not what I meant to convey.  What I mean to say is… nm. whatevs.))</p>
<p>How about code size?  From the example above, CJS is the winner here, for sure.  … Or is it?  If you were to execute that code in a browser, you’d be declaring globals left and right while clobbering variables in every other module.  Clearly, we would need to at least wrap the module in an <a href="http://benalman.com/news/2010/11/immediately-invoked-function-expression/">IIFE</a> to prevent the module from running in the global scope.</p>
<p>Also, in the example above, I showed the advantage of delayed execution which allowed me to declare less variables.  If you were to map all of the <code>require()</code>-ed modules to variables (and wrap the module in an IIFE), you’d end up with nearly the same number of bytes.</p>
<h2>And the winner is….</h2>
<p>So, if you asked me which one is the winner, I’d have to say <em>neither</em>.  To be honest, I plan to use both formats.</p>
<p>AMD has some clear benefits in the browser world.  For a more complete list, check out Miller Medeiros’s recent <a href="http://blog.millermedeiros.com/2011/09/amd-is-better-for-the-web-than-commonjs-modules/">blog article on why AMD is better</a>.  I couldn’t say it any better than he did.</p>
<p>On the other hand, CJS advocates will argue that it’s critical to author modules in a format that doesn’t cater to the <em>transport</em> (more on this later).  I understand and <em>almost</em> agree with their argument, but this article would be way too tl;dr if I were to extrapolate on that argument.</p>
<p>I’m guessing that both formats will be around for a long, long time.  So here’s what I plan to do:</p>
<ol>
<li>write modules that could execute in a server environment in CJS format</li>
<li>write modules that could benefit from AMD’s browser-friendly features in AMD format</li>
</ol>
<p>Duh.</p>
<h2>One more thing…</h2>
<p>Did I mention that AMD supports wrapped CJS modules?  If you read the wikis I linked at the top of this article, I wouldn’t have to tell you that because you’d already know.  Here’s what one looks like:</p>

<div><div><pre style="font-family:monospace">define<span style="color:#009900">(</span>
    <span style="color:#009900">[</span><span style="color:#3366cc">'pkgA/modA'</span><span style="color:#339933">,</span> <span style="color:#3366cc">'pkgA/modB'</span><span style="color:#339933">,</span> <span style="color:#3366cc">'pkgZ/modC'</span><span style="color:#339933">,</span> <span style="color:#3366cc">'require'</span><span style="color:#339933">,</span> <span style="color:#3366cc">'exports'</span><span style="color:#339933">,</span> <span style="color:#3366cc">'module'</span><span style="color:#009900">]</span><span style="color:#339933">,</span>
    <span style="color:#003366;font-weight:bold">function</span> <span style="color:#009900">(</span>modA<span style="color:#339933">,</span> modB<span style="color:#339933">,</span> modC<span style="color:#339933">,</span> require<span style="color:#339933">,</span> exports<span style="color:#339933">,</span> module<span style="color:#009900">)</span> <span style="color:#009900">{</span>
 
<span style="color:#003366;font-weight:bold">var</span> modC <span style="color:#339933">=</span> require<span style="color:#009900">(</span><span style="color:#3366cc">'pkgZ/modC'</span><span style="color:#009900">)</span><span style="color:#339933">;</span>
 
exports.<span style="color:#660066">foo</span> <span style="color:#339933">=</span> modC <span style="color:#339933">?</span> require<span style="color:#009900">(</span><span style="color:#3366cc">'pkgA/modA'</span><span style="color:#009900">)</span> <span style="color:#339933">:</span> require<span style="color:#009900">(</span><span style="color:#3366cc">'pkgA/modB'</span><span style="color:#009900">)</span><span style="color:#339933">;</span>
<span style="color:#009900">}</span><span style="color:#009900">)</span><span style="color:#339933">;</span></pre></div></div>

<p>In this hybrid module, we’re prefetching modules like AMD, but using <code>require()</code> like an R-Value as in CJS.  Funky!  The <code>require</code> and <code>exports</code> “free” variables are brought into the modules using reserved dependency names.  In this hybrid model (using CommonJS-ish terminology) AMD is the <em>transport format</em> and CommonJS is the <em>authoring format</em>.  (Thanks to <a href="http://twitter.com/tobie">@tobie</a> for reminding me of this.  Repeatedly.  The SproutCore guys have also given me a <em>tongue lashing</em> or two.)  </p>
<p><a href="http://requirejs.org">RequireJS</a>‘s companion script, r.js, will generate these wrappers for your CJS modules.</p>
<p>I’ve been experimenting with a slight variation on this.  It compacts better and executes a bit faster, but munges the transport and authoring formats in the process:</p>

<div><div><pre style="font-family:monospace">define<span style="color:#009900">(</span>
    <span style="color:#009900">[</span><span style="color:#3366cc">'pkgA/modA'</span><span style="color:#339933">,</span> <span style="color:#3366cc">'pkgA/modB'</span><span style="color:#339933">,</span> <span style="color:#3366cc">'pkgZ/modC'</span><span style="color:#339933">,</span> <span style="color:#3366cc">'require'</span><span style="color:#339933">,</span> <span style="color:#3366cc">'exports'</span><span style="color:#339933">,</span> <span style="color:#3366cc">'module'</span><span style="color:#009900">]</span><span style="color:#339933">,</span>
    <span style="color:#003366;font-weight:bold">function</span> <span style="color:#009900">(</span>modA<span style="color:#339933">,</span> modB<span style="color:#339933">,</span> modC<span style="color:#339933">,</span> require<span style="color:#339933">,</span> exports<span style="color:#339933">,</span> module<span style="color:#009900">)</span> <span style="color:#009900">{</span>
 
<span style="color:#006600;font-style:italic">// ok. this looks funny, but it's much easier and more efficient</span>
<span style="color:#006600;font-style:italic">// to simply replace require() with a variable name than to figure out</span>
<span style="color:#006600;font-style:italic">// which variable declarations can be removed and how to do that safely.</span>
<span style="color:#006600;font-style:italic">// I may experiment some more. : )</span>
<span style="color:#003366;font-weight:bold">var</span> modC <span style="color:#339933">=</span> modC<span style="color:#339933">;</span>
 
exports.<span style="color:#660066">foo</span> <span style="color:#339933">=</span> modC <span style="color:#339933">?</span> modA <span style="color:#339933">:</span> modB<span style="color:#339933">;</span>
<span style="color:#009900">}</span><span style="color:#009900">)</span><span style="color:#339933">;</span></pre></div></div>

<p>winning.</p>
<p><strong>[Update 2011-10-01]</strong></p>
<p>I’m trying not to wreck a nice ending on this blog post, but I just realized a big mistake on my part. I often get confused between CJS and the latest flavor of node. CJS is missing a very important feature, imho. </p>
<p>With CJS, you cannot have a module that returns a single function or constructor. If we are writing modules as focused and fine-grained as possible, then we should be able to do this!</p>
<p>Node allows us to assign our module to the `module.exports` property. A module that only exports a constructor could then look like this:</p>

<div><div><pre style="font-family:monospace"><span style="color:#003366;font-weight:bold">function</span> MyConstructor <span style="color:#009900">(</span><span style="color:#009900">)</span> <span style="color:#009900">{</span><span style="color:#009900">}</span>
module.<span style="color:#660066">exports</span> <span style="color:#339933">=</span> MyConstructor<span style="color:#339933">;</span></pre></div></div>

<p>CJS needs to add this for ms to be 100% on board. Hm. I guess I’m not really authoring CJS modules now. They’re node modules. </p>