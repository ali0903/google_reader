---
layout: post
title:  "jQuery Roundup: AMD-Utils, jquery.mentionsInput, Echo Nest API Plugin, i18next"
date:   2011-12-20 08:00:00
author: 
categories: program
---

## jQuery Roundup: AMD-Utils, jquery.mentionsInput, Echo Nest API Plugin, i18next
### by 
### at 2011-12-20 08:00:00
### original <http://feedproxy.google.com/~r/dailyjs/~3/YJpM8ieCUe8/jquery-roundup>

<div>
<p>Note: You can send your plugins and articles in for review through our <a href="http://dailyjs.com/contact.html">contact form</a> or <a href="http://twitter.com/dailyjs">@dailyjs</a>.</p>
</div>
<h3><span>AMD</span>-Utils</h3>
<p><a href="http://millermedeiros.github.com/amd-utils/"><span>AMD</span>-Utils</a> (GitHub: <a href="https://github.com/millermedeiros/amd-utils/">millermedeiros / amd-utils</a>, License: <em><span>MIT</span></em>) by Miller Medeiros is a set of modular utilities written using the <a href="https://github.com/amdjs/amdjs-api/wiki/AMD"><span>AMD</span></a> <span>API</span>.</p>
<blockquote>
<p>All code is library agnostic and consist mostly of helper methods that aren’t directly related with the <span>DOM</span>, the purpose of this library isn’t to replace Dojo, jQuery, <span>YUI</span>, Mootools, etc, but to provide modular solutions for common problems that aren’t solved by most of them.</p>
</blockquote>
<p>The modules written so far include some of the things covered on my <a href="http://dailyjs.com/tags.html#lmaf">Let’s Make a Framework</a> tutorial series, like a functional style <code>Array</code> module, and other commonly required number and string utility functions often added by large frameworks.</p>
<h3>jquery.mentionsInput</h3>
<p><img src="http://dailyjs.com/images/posts/mentionsinput.png" alt=""></p>
<p><a href="http://podio.github.com/jquery-mentions-input/">jquery.mentionsInput</a> (GitHub: <a href="https://github.com/podio/jquery-mentions-input">podio / jquery-mentions-input</a>, License: <em><span>MIT</span></em>) by Kenneth Auchenberg and the Podio Dev Team is a UI component for handling <em>@mentions</em>.  It’ll display an autocomplete list for matching names and allow one to be selected.  Once a name is selected, it’ll change colour in the text box, as shown above.</p>
<p>The authors have packaged it with <span>CSS</span>, so it’s easy to get started right away.  It does expect some data, rather than automatically searching an <span>API</span> like Twitter’s, so the expected format looks like this:</p>
<div><pre><code><span>$</span><span>(</span><span>&#39;textarea.mention&#39;</span><span>).</span><span>mentionsInput</span><span>({</span>
  <span>onDataRequest</span><span>:</span><span>function</span> <span>(</span><span>mode</span><span>,</span> <span>query</span><span>,</span> <span>callback</span><span>)</span> <span>{</span>
    <span>var</span> <span>data</span> <span>=</span> <span>[</span>
      <span>{</span> <span>id</span><span>:</span><span>1</span><span>,</span> <span>name</span><span>:</span><span>&#39;Kenneth Auchenberg&#39;</span><span>,</span> <span>&#39;avatar&#39;</span><span>:</span><span>&#39;http://cdn0.4dots.com/i/customavatars/avatar7112_1.gif&#39;</span><span>,</span> <span>&#39;type&#39;</span><span>:</span><span>&#39;contact&#39;</span> <span>},</span>
      <span>{</span> <span>id</span><span>:</span><span>2</span><span>,</span> <span>name</span><span>:</span><span>&#39;Jon Froda&#39;</span><span>,</span> <span>&#39;avatar&#39;</span><span>:</span><span>&#39;http://cdn0.4dots.com/i/customavatars/avatar7112_1.gif&#39;</span><span>,</span> <span>&#39;type&#39;</span><span>:</span><span>&#39;contact&#39;</span> <span>},</span>
      <span>{</span> <span>id</span><span>:</span><span>3</span><span>,</span> <span>name</span><span>:</span><span>&#39;Anders Pollas&#39;</span><span>,</span> <span>&#39;avatar&#39;</span><span>:</span><span>&#39;http://cdn0.4dots.com/i/customavatars/avatar7112_1.gif&#39;</span><span>,</span> <span>&#39;type&#39;</span><span>:</span><span>&#39;contact&#39;</span> <span>},</span>
      <span>{</span> <span>id</span><span>:</span><span>4</span><span>,</span> <span>name</span><span>:</span><span>&#39;Kasper Hulthin&#39;</span><span>,</span> <span>&#39;avatar&#39;</span><span>:</span><span>&#39;http://cdn0.4dots.com/i/customavatars/avatar7112_1.gif&#39;</span><span>,</span> <span>&#39;type&#39;</span><span>:</span><span>&#39;contact&#39;</span> <span>},</span>
      <span>{</span> <span>id</span><span>:</span><span>5</span><span>,</span> <span>name</span><span>:</span><span>&#39;Andreas Haugstrup&#39;</span><span>,</span> <span>&#39;avatar&#39;</span><span>:</span><span>&#39;http://cdn0.4dots.com/i/customavatars/avatar7112_1.gif&#39;</span><span>,</span> <span>&#39;type&#39;</span><span>:</span><span>&#39;contact&#39;</span> <span>},</span>
      <span>{</span> <span>id</span><span>:</span><span>6</span><span>,</span> <span>name</span><span>:</span><span>&#39;Pete Lacey&#39;</span><span>,</span> <span>&#39;avatar&#39;</span><span>:</span><span>&#39;http://cdn0.4dots.com/i/customavatars/avatar7112_1.gif&#39;</span><span>,</span> <span>&#39;type&#39;</span><span>:</span><span>&#39;contact&#39;</span> <span>}</span>
    <span>];</span>

    <span>data</span> <span>=</span> <span>_</span><span>.</span><span>filter</span><span>(</span><span>data</span><span>,</span> <span>function</span><span>(</span><span>item</span><span>)</span> <span>{</span> <span>return</span> <span>item</span><span>.</span><span>name</span><span>.</span><span>toLowerCase</span><span>().</span><span>indexOf</span><span>(</span><span>query</span><span>.</span><span>toLowerCase</span><span>())</span> <span>&gt;</span> <span>-</span><span>1</span> <span>});</span>

    <span>callback</span><span>.</span><span>call</span><span>(</span><span>this</span><span>,</span> <span>data</span><span>);</span>
  <span>}</span>
<span>});</span>
</code></pre>
</div>
<h3>Echo Nest <span>API</span> Plugin</h3>
<p><a href="https://github.com/Rodeoclash/Echonest-jQuery-Plugin">Echonest-jQuery-Plugin</a> by Samuel Richardson is a plugin for <a href="http://the.echonest.com/">The Echo Nest</a> which is a real-time <span>API</span> for accessing music data.  Song data and audio fingerprinting are just some of the cool things that this <span>API</span> provides.</p>
<p>Let’s say I needed to get a set of album images.  All I’d have to do is this:</p>
<div><pre><code><span>var</span> <span>echonest</span> <span>=</span> <span>new</span> <span>EchoNest</span><span>(</span><span>&#39;YOUR_API_KEY&#39;</span><span>);</span>
<span>echonest</span><span>.</span><span>artist</span><span>(</span><span>&#39;Radiohead&#39;</span><span>).</span><span>images</span><span>(</span><span>function</span><span>(</span><span>imageCollection</span><span>)</span> <span>{</span>
  <span>$</span><span>(</span><span>&#39;body&#39;</span><span>).</span><span>prepend</span><span>(</span><span>imageCollection</span><span>.</span><span>to_html</span><span>(</span><span>&#39;&lt;img src=&quot;${url}&quot;&gt;&#39;</span><span>));</span>
<span>});</span>
</code></pre>
</div>
<p>Combined with a templating system, this makes working with music data extremely convenient.  The only issue with this approach is the <span>API</span> key is exposed.  Echo Nest uses the <span>API</span> key for enforcing rate limits, so it’s not safe to expose it publicly.  As it stands, I’d probably use client-side Echo Nest <span>API</span> code as a way of rapidly prototyping a music service built on this platform, then create my own server-side bridge to obscure the <span>API</span> key.</p>
<h3>i18next</h3>
<p><a href="http://jamuhl.github.com/i18next/">i18next</a> (GitHub: <a href="https://github.com/jamuhl/i18next">jamuhl / i18next</a>, License: <em><span>MIT</span></em>) by Jan Mühlemann is a client-side translation plugin with lots of features, including: plurals, <code>localStorage</code>, namespaces, and variables.  <span>JSON</span> resource files can be used to store translations, and then i18next will load them as required.</p>
<p>Given a suitable file at <code>/locales/en-US/translation.json</code>:</p>
<div><pre><code><span>{</span>
  <span>&quot;app&quot;</span><span>:</span> <span>{</span>
    <span>&quot;name&quot;</span><span>:</span> <span>&quot;i18n&quot;</span>
  <span>},</span>
  <span>&quot;creator&quot;</span><span>:</span> <span>{</span>
    <span>&quot;firstname&quot;</span><span>:</span> <span>&quot;Jan&quot;</span><span>,</span>
    <span>&quot;lastname&quot;</span><span>:</span> <span>&quot;Mühlemann&quot;</span>
  <span>}</span>
<span>}</span>
</code></pre>
</div>
<p>Then <code>$.i18n.init</code> can be used to load the resource and access translations:</p>
<div><pre><code><span>$</span><span>.</span><span>i18n</span><span>.</span><span>init</span><span>({},</span> <span>function</span><span>(</span><span>t</span><span>)</span> <span>{</span> <span>// will init i18n with default settings and set language from navigator</span>
  <span>var</span> <span>appName</span> <span>=</span> <span>t</span><span>(</span><span>&#39;app.name&#39;</span><span>);</span> <span>// -&gt; i18n</span>
  <span>var</span> <span>creator</span> <span>=</span> <span>t</span><span>(</span><span>&#39;creator.firstname&#39;</span><span>)</span> <span>+</span> <span>&#39; &#39;</span> <span>+</span> <span>t</span><span>(</span><span>&#39;creator.lastname&#39;</span><span>);</span> <span>// -&gt; Jan Mühlemann</span>
<span>});</span>
</code></pre>
</div>
<p>The <a href="http://jamuhl.github.com/i18next/">i18next documentation</a> contains more examples and shows how to change languages, organise translations with nesting, and use variables.</p><img src="http://feeds.feedburner.com/~r/dailyjs/~4/YJpM8ieCUe8" height="1" width="1">