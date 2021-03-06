---
layout: post
title:  "AMD is better for the web than CommonJS modules"
date:   2011-09-30 23:56:50
author: Miller Medeiros
categories: program
---

## AMD is better for the web than CommonJS modules
### by Miller Medeiros
### at 2011-09-30 23:56:50
### original <http://blog.millermedeiros.com/2011/09/amd-is-better-for-the-web-than-commonjs-modules/>

<p>I’ve seen a few libraries and tools later using different kinds of ways to<br>
handle dependency management, some of them are very similar to the way that<br>
<a href="http://wiki.commonjs.org/wiki/Modules/1.1">CommonJS modules</a> looks like:</p>
<p><span></span></p>
<pre>
//use another module
var myLib = require('myPackage/myLib');

function foo(){
    console.log('foo');
    myLib.doSomething();
}

//expose module API
exports.foo = foo;
</pre>
<p>The <em>beauty</em> of CommonJS modules is how simple they are, you simply <code>require</code> something synchronously and that module can be used right away <small>(just like <em>magic</em>)</small>.</p>
<p>The same code written in <em>traditional</em> AMD <small>(AMD modules are flexible and can be written in different ways)</small> would look like:</p>
<pre>
define(['myPackage/myLib'], function(myLib){

    function foo(){
        console.log('foo');
        myLib.doSomething();
    }

    //expose module API
    return {
        foo : foo
    };

});
</pre>
<p><ins><strong>Edit:</strong></ins> Or even using this “simplified CommonJS wrapper” syntax:</p>
<pre>
define(function(require, exports, module){

    var myLib = require('myPackage/myLib');

    function foo(){
        console.log('foo');
        myLib.doSomething();
    }

    //expose module API
    exports.foo = foo;

});
</pre>
<p>In my opinion AMD modules are way better for the web right now than CJS modules, the asynchronous nature of AMD make it slightly more complex but it also expands the AMD <em>power</em> to a level that CJS modules can only <em>dream of</em>…</p>
<h3>AMD advantages</h3>
<ul>
<li>AMD modules are flexible.</li>
<li><strong>Plugin support</strong> (extremely useful and powerful).</li>
<li><strong>Can load more than just JavaScript files</strong>.</li>
<li><strong>Path aliases</strong> and other advanced config settings to simplify path resolution and dependency listing.</li>
<li><strong>Works in the browser without a build</strong> <small>(most popular AMD loaders supports this feature)</small>.</li>
<li>Is asynchronous by nature.</li>
<li>Works in current browsers, no need to wait for Harmony.</li>
<li>Dependencies are usually listed on the same location making it easy to identify what are the dependencies.</li>
<li>Avoid globals by default since modules are wrapped by closures.</li>
<li>Can run the same code on both environments by simply using an AMD loader that works on a CJS environment <small>(see r.js)</small>.</li>
<li>It’s being adopted by popular JavaScript libraries like Dojo <small>(1.6+)</small>, Mootools <small>(2.0)</small>, jQuery <small>(1.7)</small>…</li>
<li><strong>Lazy-load scripts if needed.</strong></li>
</ul>
<h3>Examples</h3>
<p>Path alias to simplify module look-up and file versioning <small>(really important feature)</small>:</p>
<pre>
//configure RequireJS/curljs paths
require.config({
    //set base folder of all dependencies
    baseUrl : 'js',
    paths : {
        //avoid typing long path all the time
        'foo' : 'lib/lorem-ipsum/dolor/foo',
        //version file for cache busting
        'lorem/ipsum' : 'lorem/ipsum-v23'
    }
});

define(['foo/bar', 'lorem/ipsum'], function(bar, ipsum){
    //this will load 'js/lib/lorem-ipsum/dolor/foo/bar.js'
    //and 'js/lorem/ipsum-v23.js'
});
</pre>
<p>Using plugins to load different kinds of dependencies:</p>
<pre>
//using plugins to load different file types
define([
        'text!./lipsum.md',
        'image!img/lol_cat.jpg'
    ],
    function(lipsum, lol_cat){
        console.log(lipsum);
        document.body.appendChild(lol_cat);
    }
);
</pre>
<p>Dynamic module loading + returning other types of data:</p>
<pre>
define(['require'], function(require){
    var mods = ['foo/bar', 'lorem', 'dolor'];

    function loadModuleByIndex(index){
        //dependency should be an Array
        require([ mods[index] ], function(m){
            //let's assume all modules have an init() method
            m.init();
        });
    }

    //modules can return any kind of data (string, object, function, etc..)
    return loadModuleByIndex;
});
</pre>
<h3>Notes</h3>
<p>The main reason for writing this post was a twit by <a href="http://unscriptable.com/">John Hann</a>:</p>
<blockquote>
<p>the more “universal module boilerplate” i see, the more i want to go with CJS 1.1.1 format. but then again, AMD has way more flexibility. hm – <a href="http://twitter.com/#!/unscriptable/statuses/119760801681776642">@unscriptable</a></p>
</blockquote>
<p>And also because I’ve seen that some new tools and libraries are trying to mimic the CJS module format <small>(I’m looking at you ender.js)</small> or that have some special notation to include other files during build <small>(google closure, sproutcore, …)</small> and I feel they are going to the “wrong way” since AMD is clearly more flexible and becoming more popular each day.</p>
<p>I’ve been using AMD <small>(RequireJS)</small> for almost 1 year and can’t imagine going back to the era of complex namespacing <small>(<code>MyApp.something.bar</code>)</small>, awkward script concatenation <small>(which can usually result in problems if concatened on the wrong order)</small>, adding  multiple script tags to the HTML, not being able to load scripts on demand, explaining to eveyone that they should wrap the code inside a self executing function to avoid generating globals, not being able to easily share code between projects, etc…</p>
<p>Don’t enforce a build step during development <small>(even if automatic)</small>, one of the beauties of developing JavaScript is that you can simply refresh the browser to see the updates, run the build task only for deployment <small>(combine and minify files to reduce number of requests and increase load performance)</small>.</p>
<p><strong>AMD greatest benefit isn’t being able to load scripts on-demand, as some people may think, the greatest benefit is the increase of the code organization/modularity and also the reduced need for globals/namespacing.</strong></p>
<h3>More</h3>
<p><a href="http://requirejs.org/">RequireJS</a> and <a href="https://github.com/unscriptable/curl">curl.js</a> are the most popular AMD loaders, check them out and use <a href="https://github.com/jrburke/r.js/">r.js</a> to optimize your AMD modules into a single file and do some pre-processing of external resources before deploy. r.js can also be used to run AMD modules inside node.js and Rhino and to convert CommonJS modules into an AMD compatible format.</p>
<p><a href="http://groups.google.com/group/requirejs">RequireJS mailing list</a> is a good place to ask questions. There is also the <a href="https://groups.google.com/group/amd-implement">AMD-implement list</a> where different AMD loader implementors are discussing about features and how things should work to increase compatibility of the code.</p>
<p>James Burke (RequireJS creator) wrote a <a href="http://tagneto.blogspot.com/2011/04/on-inventing-js-module-formats-and.html">good post</a> explaining why AMD is a good module format and why we should avoid the “bikesheding”, read it if you considering other module formats.</p>
<p>Check also a <a href="http://briancavalier.com/presentations/pgh-js-amd-10-2011/">really good presentation by Brian Cavalier</a> and <a href="http://unscriptable.com/index.php/2011/09/30/amd-versus-cjs-whats-the-best-format/">John Hann post</a> <small>(both created after my post)</small>.</p>
<p>PS: when I started coding JavaScript as my main programing language I really missed “Classes”, now I see that what I was really missing was being able to split my code between multiple files “in a sane way” and to share code across different applications, inheritance is almost unrelated with that… <small>(composition &gt; inheritance)</small></p>
<p><strong>Embrace AMD and be happy</strong></p>
<blockquote><p><strong>Edit 2011/09/30:</strong> added “simplified CommonJS wrapper” suggested by James Burke and also renamed packages suggested by John Hann. Also removed comments about node.js supporting basic AMD modules since it seems they removed the native support.</p></blockquote>
<blockquote><p><strong>Edit 2011/10/06:</strong> added link to John Hann’s post and Brian Cavalier’s presentation.</p></blockquote>
<h3>Related</h3>
<ul>
<li><a href="http://blog.millermedeiros.com/2011/10/amd-utils-modular-javascript-utilities/">amd-utils : modular JavaScript utilities</a></li>
<li><a href="http://blog.millermedeiros.com/2011/10/requirejs-plugins/">RequireJS plugins</a></li>
</ul>