---
layout: post
title:  "AngularJS: Tests"
date:   2013-05-16 07:00:00
author: 
categories: program
---

## AngularJS: Tests
### by 
### at 2013-05-16 07:00:00
### original <http://feedproxy.google.com/~r/dailyjs/~3/eJX08OgI61M/angularjs-5>

<ul>
  <li><a href="http://dailyjs.com/2013/04/11/angularjs-1/">Part 1: Google, Twitter, and AngularJS</a></li>
  <li><a href="http://dailyjs.com/2013/04/18/angularjs-2/">Part 2: Let's Make a Feed Reader</a></li>
  <li><a href="http://dailyjs.com/2013/04/25/angularjs-3/">Part 3: Rendering Feeds</a></li>
  <li><a href="http://dailyjs.com/2013/05/09/angularjs-4/">Part 4: Managing Feeds</a></li>
  <li><a href="http://dailyjs.com/2013/05/16/angularjs-5/"><strong>Part 5: Tests</strong></a></li>
</ul>
<h3>Previously</h3>

<p>In the <a href="http://dailyjs.com/2013/05/09/angularjs-4/">last part</a> we changed the app to support multiple feeds.</p>

<p>This week you’ll learn how to write a short unit test to test the app’s main controller. This will involve mocking data.</p>

<p>If you get stuck at any part of this tutorial, check out the full source here: <a href="https://github.com/alexyoung/djsreader/commit/7b4bda96b787b7707582567db927f12cc80c5d27">commit 7b4bda</a>.</p>

<h3>Neat and Tidy Tests</h3>

<p>The goal of this tutorial is to demonstrate one method for writing neat and tidy tests. Ideally mocked data should be stored in separate files and loaded when required. What we absolutely don’t want is global variables littering memory.</p>

<p>To run tests with the Yeoman-generated app we’ve been working on, type <code>grunt test</code>. It’ll use <a href="http://karma-runner.github.io">Karma</a> and <a href="http://pivotal.github.io/jasmine/">Jasmine</a> to run tests through Chrome using WebSockets. The workflow in the console is effortless, despite Chrome appearing and disappearing in the background (it won’t trample on your existing Chrome session, it’ll make a separate process). It doesn’t steal focus away, which means you can invoke tests and continue working on code without getting interrupted.</p>
<div>
  <img src="http://dailyjs.com/images/posts/djsreader-5-1.png">
  <small>My workflow: mock, controller, test, and a terminal for running tests</small>
</div>
<p>The basic approach is to use <code>$httpBackend.whenJSONP</code> to tell AngularJS to return some mock data when the tests are run, instead of fetching the real feed data from Yahoo. That sounds simple enough, but there’s a slight compilation: leaving mock data in the test sucks. So, what do we do about this? The <code>karma.conf.js</code> file that was created for us by the Yeoman generator contains a line for loading files from a mocks directory: <code>&#39;test/mock/**/*.js</code>. These will be loaded <em>before</em> the tests, so let’s dump some JSON in there.</p>

<p>Interestingly, if you run <code>grunt test</code> right now it’ll fail, because the app makes a JSONP request, and the angular-mocks library will flag this as an error. Using <code>$httpBackend.whenJSONP</code> will fix this.</p>

<h3>JSON Mocks</h3>

<p>Open a file called <code>test/mock/feed.js</code> (you’ll need to <code>mkdir test/mock</code> first), then add this:</p>
<div><pre><code><span>&#39;use strict&#39;</span><span>;</span>

<span>angular</span><span>.</span><span>module</span><span>(</span><span>&#39;mockedFeed&#39;</span><span>,</span> <span>[])</span>
  <span>.</span><span>value</span><span>(</span><span>&#39;defaultJSON&#39;</span><span>,</span> <span>{</span>
    <span>query</span><span>:</span> <span>{</span>
      <span>count</span><span>:</span> <span>2</span><span>,</span>
      <span>created</span><span>:</span> <span>&#39;2013-05-16T15:01:31Z&#39;</span><span>,</span>
      <span>lang</span><span>:</span> <span>&#39;en-US&#39;</span><span>,</span>
      <span>results</span><span>:</span> <span>{</span>
        <span>entry</span><span>:</span> <span>[</span>
          <span>{</span>
            <span>title</span><span>:</span> <span>&#39;Node Roundup: 0.11.2, 0.10.6, subscribe, Omelette&#39;</span><span>,</span>
            <span>link</span><span>:</span> <span>{</span> <span>href</span><span>:</span> <span>&#39;http://dailyjs.com/2013/05/15/node-roundup&#39;</span> <span>},</span>
            <span>updated</span><span>:</span> <span>&#39;2013-05-15T00:00:00+01:00&#39;</span><span>,</span>
            <span>id</span><span>:</span> <span>&#39;http://dailyjs.com/2013/05/15/node-roundup&#39;</span><span>,</span>
            <span>content</span><span>:</span> <span>{</span> <span>type</span><span>:</span> <span>&#39;html&#39;</span><span>,</span> <span>content</span><span>:</span> <span>&#39;example&#39;</span> <span>}</span>
          <span>},</span>
          <span>{</span>
            <span>title</span><span>:</span> <span>&#39;jQuery Roundup: 1.10, jquery-markup, zelect&#39;</span><span>,</span>
            <span>link</span><span>:</span> <span>{</span> <span>href</span><span>:</span> <span>&#39;http://dailyjs.com/2013/05/14/jquery-roundup&#39;</span> <span>},</span>
            <span>updated</span><span>:</span> <span>&#39;2013-05-14T00:00:00+01:00&#39;</span><span>,</span>
            <span>id</span><span>:</span> <span>&#39;http://dailyjs.com/2013/05/14/jquery-roundup&#39;</span><span>,</span>
            <span>content</span><span>:</span> <span>{</span> <span>type</span><span>:</span> <span>&#39;html&#39;</span><span>,</span> <span>content</span><span>:</span> <span>&#39;example 2&#39;</span> <span>}</span>
          <span>}</span>
        <span>]</span>
      <span>}</span>
    <span>}</span>
  <span>});</span>
</code></pre>
</div>
<p>This uses <code>angular.module().value</code> to set a value that contains some JSON. I derived this JSON from Yahoo’s API by running the app and looking at the network traffic in WebKit Inspector, then edited out the <code>content</code> properties because they were huge (DailyJS has full articles in feeds).</p>

<h3>Loading the Mocked Value</h3>

<p>Open <code>test/spec/controllers/main.js</code> and change the first <code>beforeEach</code> to load <code>mockedFeed</code>:</p>
<div><pre><code><span>beforeEach</span><span>(</span><span>module</span><span>(</span><span>&#39;djsreaderApp&#39;</span><span>,</span> <span>&#39;mockedFeed&#39;</span><span>));</span>
</code></pre>
</div>
<p>The <code>beforeEach</code> method is provided by Jasmine, and will make the specified function run before each test. Now the <code>defaultJSON</code> value can be injected, along with the HTTP backend:</p>
<div><pre><code><span>var</span> <span>MainCtrl</span><span>,</span> <span>scope</span><span>,</span> <span>mockedFeed</span><span>,</span> <span>httpBackend</span><span>;</span>

<span>// Initialize the controller and a mock scope</span>
<span>beforeEach</span><span>(</span><span>inject</span><span>(</span><span>function</span><span>(</span><span>$controller</span><span>,</span> <span>$rootScope</span><span>,</span> <span>$httpBackend</span><span>,</span> <span>defaultJSON</span><span>)</span> <span>{</span>
  <span>// Set up the expected feed data</span>
  <span>httpBackend</span> <span>=</span> <span>$httpBackend</span><span>;</span>
  <span>$httpBackend</span><span>.</span><span>whenJSONP</span><span>(</span><span>/query.yahooapis.com/</span><span>).</span><span>respond</span><span>(</span><span>defaultJSON</span><span>);</span>

  <span>scope</span> <span>=</span> <span>$rootScope</span><span>.</span><span>$new</span><span>();</span>
  <span>MainCtrl</span> <span>=</span> <span>$controller</span><span>(</span><span>&#39;MainCtrl&#39;</span><span>,</span> <span>{</span>
    <span>$scope</span><span>:</span> <span>scope</span>
  <span>});</span>
<span>}));</span>
</code></pre>
</div>
<p>You should be able to guess what’s happening with <code>$httpBackend.whenJSONP(/query.yahooapis.com/)</code> – whenever the app tries to contact Yahoo’s service, it’ll trigger our mocked HTTP backend and return the <code>defaultJSON</code> value instead. Cool!</p>

<h3>The Test</h3>

<p>The actual test is quite a comedown after all that mock wrangling:</p>
<div><pre><code><span>it</span><span>(</span><span>&#39;should have a list of feeds&#39;</span><span>,</span> <span>function</span><span>()</span> <span>{</span>
  <span>expect</span><span>(</span><span>scope</span><span>.</span><span>feeds</span><span>.</span><span>length</span><span>).</span><span>toBe</span><span>(</span><span>1</span><span>);</span>
  <span>httpBackend</span><span>.</span><span>flush</span><span>();</span>
  <span>expect</span><span>(</span><span>scope</span><span>.</span><span>feeds</span><span>[</span><span>0</span><span>].</span><span>items</span><span>[</span><span>0</span><span>].</span><span>title</span><span>).</span><span>toBe</span><span>(</span><span>&#39;Node Roundup: 0.11.2, 0.10.6, subscribe, Omelette&#39;</span><span>);</span>
<span>});</span>
</code></pre>
</div>
<p>The test checks <code>$scope</code> has the expected data. <code>httpBackend.flush</code> will make sure the (fake) HTTP request has finished first. The <code>scope.feeds</code> value is the one that <code>MainCtrl</code> from last week derives from the raw JSON returned by Yahoo.</p>

<h3>Conclusion</h3>

<p>You should now be able to run <code>grunt test</code> and see some passing tests (just like in my screenshot). If not, check out <a href="https://github.com/alexyoung/djsreader">djsreader</a> on GitHub to see what’s different.</p>

<p>Most of the work for this part can be found in <a href="https://github.com/alexyoung/djsreader/commit/7b4bda96b787b7707582567db927f12cc80c5d27">commit 7b4bda</a>.</p>
   <img src="http://feeds.feedburner.com/~r/dailyjs/~4/eJX08OgI61M" height="1" width="1">