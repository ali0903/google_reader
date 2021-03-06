---
layout: post
title:  "Taking Over console.log"
date:   2012-07-27 12:00:00
author: 
categories: program
---

## Taking Over console.log
### by 
### at 2012-07-27 12:00:00
### original <http://tobyho.com/2012/07/27/taking-over-console-log/>

<p>Let's say you want to intercept all calls to <code>console.log</code>, <code>console.warn</code>, and <code>console.error</code>, do something sneaky, and then proxy the call back to the original methods so that the messages get printed out as normal and no one ever has to notice. How would you do that?</p>

<h2>Attempt #1</h2>

<p>If you are a seasoned Javascript programmer, you would probably go to <a href="http://en.wikipedia.org/wiki/Monkey_patch">monkeypatching</a> and maybe write something like</p>

<div><pre><code><span>function</span> <span>takeOverConsole</span><span>(){</span>
    <span>var</span> <span>original</span> <span>=</span> <span>window</span><span>.</span><span>console</span>
    <span>window</span><span>.</span><span>console</span> <span>=</span> <span>{</span>
        <span>log</span><span>:</span> <span>function</span><span>(){</span>
            <span>// do sneaky stuff</span>
            <span>original</span><span>.</span><span>log</span><span>.</span><span>apply</span><span>(</span><span>original</span><span>,</span> <span>arguments</span><span>)</span>
        <span>}</span>
        <span>,</span> <span>warn</span><span>:</span> <span>function</span><span>(){</span>
            <span>// do sneaky stuff</span>
            <span>original</span><span>.</span><span>warn</span><span>.</span><span>apply</span><span>(</span><span>original</span><span>,</span> <span>arguments</span><span>)</span>
        <span>}</span>
        <span>,</span> <span>error</span><span>:</span> <span>function</span><span>(){</span>
            <span>// do sneaky stuff</span>
            <span>original</span><span>.</span><span>error</span><span>.</span><span>apply</span><span>(</span><span>original</span><span>,</span> <span>arguments</span><span>)</span>
        <span>}</span>
    <span>}</span>
<span>}</span>
</code></pre>
</div>


<p>This works on all browsers except for IE. On IE, <code>console.log</code> is implemented as a native method, and as such doesn't support the <code>apply</code> method. This is illustrated if you try to run the following in IE</p>

<div><pre><code><span>&gt;</span> <span>console</span><span>.</span><span>log</span><span>.</span><span>apply</span><span>(</span><span>console</span><span>,</span> <span>[</span><span>&#39;blah&#39;</span><span>])</span> 
<span>&quot;Object doesn&#39;t support property or method &#39;apply&#39;&quot;</span> 
</code></pre>
</div>


<h2>Attempt #2</h2>

<p><em>What to do?</em> Well, for the case of <code>console.log</code>, we can punt on passing through the variable length arguments exactly as they are, because we already know what the <code>console.log</code> is going to do with them: join them. Actually the way the join happens varies by browser, but let's just do everyone a favor and make them consistent here by joining them with a space as the separator. Long story short, I ended up with</p>

<div><pre><code><span>function</span> <span>takeOverConsole</span><span>(){</span>
    <span>var</span> <span>original</span> <span>=</span> <span>window</span><span>.</span><span>console</span>
    <span>function</span> <span>handle</span><span>(</span><span>method</span><span>,</span> <span>args</span><span>){</span>
        <span>var</span> <span>message</span> <span>=</span> <span>Array</span><span>.</span><span>prototype</span><span>.</span><span>slice</span><span>.</span><span>apply</span><span>(</span><span>args</span><span>).</span><span>join</span><span>(</span><span>&#39; &#39;</span><span>)</span>
        <span>// do sneaky stuff</span>
        <span>if</span> <span>(</span><span>original</span><span>)</span> <span>original</span><span>[</span><span>method</span><span>](</span><span>message</span><span>)</span>
    <span>}</span>
    <span>window</span><span>.</span><span>console</span> <span>=</span> <span>{</span>
        <span>log</span><span>:</span> <span>function</span><span>(){</span>
            <span>handle</span><span>(</span><span>&#39;log&#39;</span><span>,</span> <span>arguments</span><span>)</span>
        <span>}</span>
        <span>,</span> <span>warn</span><span>:</span> <span>function</span><span>(){</span>
            <span>handle</span><span>(</span><span>&#39;warn&#39;</span><span>,</span> <span>arguments</span><span>)</span>
        <span>}</span>
        <span>,</span> <span>error</span><span>:</span> <span>function</span><span>(){</span>
            <span>handle</span><span>(</span><span>&#39;error&#39;</span><span>,</span> <span>arguments</span><span>)</span>
        <span>}</span>
    <span>}</span>
<span>}</span>
</code></pre>
</div>


<p>This is works everywhere that I have tested, but there is one big flaw - the console object in Chrome and Firebug has more features than just <code>log</code>, <code>warn</code> and <code>error</code>: there're useful things like <code>console.profile</code>, <code>console.timeStamp</code>, <code>console.trace</code>, and lots more. My code effectively removes these extra features - not very gentlemanly.</p>

<h2>Attempt #3</h2>

<p>So, perhaps instead of replacing the <code>console</code> object, we should just replace the individual methods we want to intercept. I came up with this</p>

<div><pre><code><span>function</span> <span>takeOverConsole</span><span>(){</span>
    <span>var</span> <span>console</span> <span>=</span> <span>window</span><span>.</span><span>console</span>
    <span>if</span> <span>(</span><span>!</span><span>console</span><span>)</span> <span>return</span>
    <span>function</span> <span>intercept</span><span>(</span><span>method</span><span>){</span>
        <span>var</span> <span>original</span> <span>=</span> <span>console</span><span>[</span><span>method</span><span>]</span>
        <span>console</span><span>[</span><span>method</span><span>]</span> <span>=</span> <span>function</span><span>(){</span>
            <span>var</span> <span>message</span> <span>=</span> <span>Array</span><span>.</span><span>prototype</span><span>.</span><span>slice</span><span>.</span><span>apply</span><span>(</span><span>arguments</span><span>).</span><span>join</span><span>(</span><span>&#39; &#39;</span><span>)</span>
            <span>// do sneaky stuff</span>
            <span>original</span><span>.</span><span>call</span><span>(</span><span>console</span><span>,</span> <span>message</span><span>)</span>
        <span>}</span>
    <span>}</span>
    <span>var</span> <span>methods</span> <span>=</span> <span>[</span><span>&#39;log&#39;</span><span>,</span> <span>&#39;warn&#39;</span><span>,</span> <span>&#39;error&#39;</span><span>]</span>
    <span>for</span> <span>(</span><span>var</span> <span>i</span> <span>=</span> <span>0</span><span>;</span> <span>i</span> <span>&lt;</span> <span>methods</span><span>.</span><span>length</span><span>;</span> <span>i</span><span>++</span><span>)</span>
        <span>intercept</span><span>(</span><span>methods</span><span>[</span><span>i</span><span>])</span>
<span>}</span>
</code></pre>
</div>


<p>But this broke on IE again, on the line <code>original.call(console, message)</code>. The function's <code>call</code> method, like <code>apply</code>, is not supported by <code>console.log</code>. However, curiously - unlike the other browsers - it can be called directly without having its context set to <code>console</code>, so we can say</p>

<div><pre><code><span>original</span><span>(</span><span>message</span><span>)</span> <span>// this works on IE but breaks on Chrome</span>
</code></pre>
</div>


<p>So the solution as is so often the case is to do one thing on normal browsers, and do another on IE.</p>

<div><pre><code><span>function</span> <span>takeOverConsole</span><span>(){</span>
    <span>var</span> <span>console</span> <span>=</span> <span>window</span><span>.</span><span>console</span>
    <span>if</span> <span>(</span><span>!</span><span>console</span><span>)</span> <span>return</span>
    <span>function</span> <span>intercept</span><span>(</span><span>method</span><span>){</span>
        <span>var</span> <span>original</span> <span>=</span> <span>console</span><span>[</span><span>method</span><span>]</span>
        <span>console</span><span>[</span><span>method</span><span>]</span> <span>=</span> <span>function</span><span>(){</span>
            <span>var</span> <span>message</span> <span>=</span> <span>Array</span><span>.</span><span>prototype</span><span>.</span><span>slice</span><span>.</span><span>apply</span><span>(</span><span>arguments</span><span>).</span><span>join</span><span>(</span><span>&#39; &#39;</span><span>)</span>
            <span>// do sneaky stuff</span>
            <span>if</span> <span>(</span><span>original</span><span>.</span><span>call</span><span>){</span>
                <span>// Do this for normal browsers</span>
                <span>original</span><span>.</span><span>call</span><span>(</span><span>console</span><span>,</span> <span>message</span><span>)</span>
            <span>}</span><span>else</span><span>{</span>
                <span>// Do this for IE</span>
                <span>original</span><span>(</span><span>message</span><span>)</span>
            <span>}</span>
        <span>}</span>
    <span>}</span>
    <span>var</span> <span>methods</span> <span>=</span> <span>[</span><span>&#39;log&#39;</span><span>,</span> <span>&#39;warn&#39;</span><span>,</span> <span>&#39;error&#39;</span><span>]</span>
    <span>for</span> <span>(</span><span>var</span> <span>i</span> <span>=</span> <span>0</span><span>;</span> <span>i</span> <span>&lt;</span> <span>methods</span><span>.</span><span>length</span><span>;</span> <span>i</span><span>++</span><span>)</span>
        <span>intercept</span><span>(</span><span>methods</span><span>[</span><span>i</span><span>])</span>
<span>}</span>
</code></pre>
</div>


<p>So that's how it's done! Or not: <em>if you find a flaw in my code, please let me know in the comments!</em></p>

<h2><em>Update: Attempt #4</em></h2>

<p><em>Thanks to Jordan Reiter's comment</em></p>

<blockquote><p>console.log does not really just concatenate; at least, it doesn't in Firefox, Safari, and Chrome. It keeps them as the objects, so you can expand them and see their children. It'll be immediately obvious that something has been changed. Fix is really easy, though -- IE <em>does</em> concatenate, so use original(message) for IE and original.apply(console, arguments) for Safari, Firefox, Chrome, etc. and then it will be undetectable.</p></blockquote>

<p>Great catch, Jordan! The <em>improved</em> version is:</p>

<div><pre><code><span>function</span> <span>takeOverConsole</span><span>(){</span>
    <span>var</span> <span>console</span> <span>=</span> <span>window</span><span>.</span><span>console</span>
    <span>if</span> <span>(</span><span>!</span><span>console</span><span>)</span> <span>return</span>
    <span>function</span> <span>intercept</span><span>(</span><span>method</span><span>){</span>
        <span>var</span> <span>original</span> <span>=</span> <span>console</span><span>[</span><span>method</span><span>]</span>
        <span>console</span><span>[</span><span>method</span><span>]</span> <span>=</span> <span>function</span><span>(){</span>
            <span>// do sneaky stuff</span>
            <span>if</span> <span>(</span><span>original</span><span>.</span><span>apply</span><span>){</span>
                <span>// Do this for normal browsers</span>
                <span>original</span><span>.</span><span>apply</span><span>(</span><span>console</span><span>,</span> <span>arguments</span><span>)</span>
            <span>}</span><span>else</span><span>{</span>
                <span>// Do this for IE</span>
                <span>var</span> <span>message</span> <span>=</span> <span>Array</span><span>.</span><span>prototype</span><span>.</span><span>slice</span><span>.</span><span>apply</span><span>(</span><span>arguments</span><span>).</span><span>join</span><span>(</span><span>&#39; &#39;</span><span>)</span>
                <span>original</span><span>(</span><span>message</span><span>)</span>
            <span>}</span>
        <span>}</span>
    <span>}</span>
    <span>var</span> <span>methods</span> <span>=</span> <span>[</span><span>&#39;log&#39;</span><span>,</span> <span>&#39;warn&#39;</span><span>,</span> <span>&#39;error&#39;</span><span>]</span>
    <span>for</span> <span>(</span><span>var</span> <span>i</span> <span>=</span> <span>0</span><span>;</span> <span>i</span> <span>&lt;</span> <span>methods</span><span>.</span><span>length</span><span>;</span> <span>i</span><span>++</span><span>)</span>
        <span>intercept</span><span>(</span><span>methods</span><span>[</span><span>i</span><span>])</span>
<span>}</span>
</code></pre>
</div>