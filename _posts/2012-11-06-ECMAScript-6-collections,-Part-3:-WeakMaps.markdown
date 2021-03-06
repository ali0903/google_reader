---
layout: post
title:  "ECMAScript 6 collections, Part 3: WeakMaps"
date:   2012-11-06 23:00:06
author: Nicholas C. Zakas
categories: program
---

## ECMAScript 6 collections, Part 3: WeakMaps
### by Nicholas C. Zakas
### at 2012-11-06 23:00:06
### original <http://feedproxy.google.com/~r/nczonline/~3/Vn8N6rumssM/>

<p>Weakmaps are similar to regular maps in that they map a value to a unique key. That key can later be used to retrieve the value it identifies. Weakmaps are different because the key must be an object and cannot be a primitive value. This may seem like a strange constraint but it’s actually the core of what makes weakmaps different and useful.</p>
<p>A weakmap holds only a weak reference to a key, which means the reference inside of the weakmap doesn’t prevent garbage collection of that object. When the object is destroyed by the garbage collector, the weakmap automatically removes the key-value pair identified by that object. The canonical example for using weakmaps is to create an object related to a particular DOM element. For example, jQuery maintains a cache of objects internally, one for each DOM element that has been referenced. Using a weakmap would allow jQuery to automatically free up memory associated with a DOM element when it is removed from the document.</p>
<p>The ECMAScript 6 <code>WeakMap</code> type is an unordered list of key-value pairs where the key must be a non-null object and the value can be of any type. The interface for <code>WeakMap</code> is very similar to that of <code>Map</code> in that <code>set()</code> and <code>get()</code> are used to add data and retrieve data, respectively:</p>
<pre><code>var map = new WeakMap(),
    element = document.querySelector(&quot;.element&quot;);

map.set(element, &quot;Original&quot;);

// later
var value = map.get(element);
console.log(value);             // &quot;Original&quot;

// later still - remove reference
element.parentNode.removeChild(element);
element = null;

value = map.get(element);
console.log(value);             // undefined</code></pre>
<p>In this example, one key-value pair is stored. The key is a DOM element used to store a corresponding string value. That value was later retrieved by passing in the DOM element to <code>get()</code>. If the DOM element is then removed from the document and the variable referencing it is set to <code>null</code>, then the data is also removed from the weakmap and the next attempt to retrieve data associated with the DOM element fails.</p>
<p>This example is a little bit misleading because the second call to <code>map.get(element)</code> is using the value of <code>null</code> (which <code>element</code> was set to) rather than a reference to the DOM element. You can’t use <code>null</code> as a key in weakmaps, so this code isn’t really doing a valid lookup. Unfortunately, there is no part of the interface that allows you to query whether or not a reference has been cleared (because the reference no longer exists).</p>
<p>Note: The weakmap <code>set()</code> method will throw an error if you try to use a primitive value as a key. If you want to use a primitive value as a key, then it’s best to use <code>Map</code> instead.</p>
<p>Weakmaps also have <code>has()</code> for determining if a key exists in the map and <code>delete()</code> for removing a key-value pair.</p>
<pre><code>var map = new WeakMap(),
    element = document.querySelector(&quot;.element&quot;);

map.set(element, &quot;Original&quot;);

console.log(map.has(element));   // true
console.log(map.get(element));   // &quot;Original&quot;

map.delete(element);
console.log(map.has(element));   // false
console.log(map.get(element));   // undefined</code></pre>
<p>Here, a DOM element is once again used as the key in a weakmap. The <code>has()</code> method is useful for checking to see if a reference is currently being used as a key in the weakmap. Keep in mind that this only works when you have a non-null reference to a key. The key is forcibly removed from the weakmap by using <code>delete()</code>, at which point <code>has()</code> returns <code>false</code> and <code>get()</code> returned <code>undefined</code>.</p>
<h2>Browser Support</h2>
<p>Both Firefox and Chrome have implemented <code>WeakMap</code>, however, in Chrome you need to manually enable ECMAScript 6 features: go to <code>chrome://flags</code> and enable &quot;Experimental JavaScript Features&quot;. Both implementations are complete per the current strawman<sup>[1]</sup> specification (though the current ECMAScript 6 spec also defines a <code>clear()</code> method).</p>
<h2>Uses and Limitations</h2>
<p>Weakmaps have a very specific use case in mind, and that is mapping values to objects that might disappear in the future. The ability to free up memory related to these objects is useful for JavaScript libraries that wrap DOM elements with custom objects such as jQuery and YUI. There’ll likely be more use cases discovered once implementations are complete and widespread, but in the short term, don’t feel bad if you can’t figure out a good spot for using weakmaps.</p>
<p>In many cases, a regular map is probably what you want to use. Weakmaps are limited in that they aren’t enumerable and you can’t keep track of how many items are contained within. There also isn’t a way to retrieve a list of all keys. If you need this type of functionality, then you’ll need to use a regular map. If you don’t, and you only intend to use objects as keys, then a weakmap may be the right choice.</p>
<h2>References</h2>
<ol>
<li><a href="http://wiki.ecmascript.org/doku.php?id=harmony:weak_maps">WeakMaps Strawman</a> (ECMA)</li>
</ol>
<div>
<a href="http://feeds.feedburner.com/~ff/nczonline?a=Vn8N6rumssM:13H9Ci_pIzM:yIl2AUoC8zA"><img src="http://feeds.feedburner.com/~ff/nczonline?d=yIl2AUoC8zA" border="0"></a> <a href="http://feeds.feedburner.com/~ff/nczonline?a=Vn8N6rumssM:13H9Ci_pIzM:V_sGLiPBpWU"><img src="http://feeds.feedburner.com/~ff/nczonline?i=Vn8N6rumssM:13H9Ci_pIzM:V_sGLiPBpWU" border="0"></a> <a href="http://feeds.feedburner.com/~ff/nczonline?a=Vn8N6rumssM:13H9Ci_pIzM:qj6IDK7rITs"><img src="http://feeds.feedburner.com/~ff/nczonline?d=qj6IDK7rITs" border="0"></a> <a href="http://feeds.feedburner.com/~ff/nczonline?a=Vn8N6rumssM:13H9Ci_pIzM:F7zBnMyn0Lo"><img src="http://feeds.feedburner.com/~ff/nczonline?i=Vn8N6rumssM:13H9Ci_pIzM:F7zBnMyn0Lo" border="0"></a>
</div><img src="http://feeds.feedburner.com/~r/nczonline/~4/Vn8N6rumssM" height="1" width="1">