---
layout: post
title:  "How To Get Rails 3 and RSpec 2 Running Specs Fast (From Scratch)"
date:   2011-02-27 10:42:38
author: Peter Cooper
categories: program
---

## How To Get Rails 3 and RSpec 2 Running Specs Fast (From Scratch)
### by Peter Cooper
### at 2011-02-27 10:42:38
### original <http://feedproxy.google.com/~r/RubyInside/~3/9kQfKhEconY/how-to-rails-3-and-rspec-2-4336.html>

<p><img src="http://www.rubyinside.com/wp-content/uploads/2011/02/rarrr.png" alt="" title="rarrr" width="120" height="120" style="float:left;margin-right:16px;margin-bottom:8px;border:1px solid #555"><a href="http://www.rubyinside.com/rails-3-0-released-and-22-free-videos-to-bring-you-up-to-speed-3733.html">Rails 3</a> is great. <a href="http://www.rubyinside.com/rspec-2-0-released-rubys-leading-bdd-framework-grows-up-3880.html">RSpec 2</a> is great. And <a href="http://www.rubyinside.com/ruby-1-9-2-released-3700.html">Ruby 1.9.2</a> is <em>really</em> great. Getting them all running together <em>and quickly</em>, however, isn't entirely straightforward. In this post I demonstrate how to get everything ticking over along with automatically running, super-snappy test runs.</p>
<p>The ultimate outcome is using Ruby 1.9.2 (though much of this is relevant to 1.8 still) to create a Rails 3 app, hook up RSpec 2, and be able to run specs <em>quickly.</em> The first two parts are easy(ish) but the "quickly" part requires some tinkering. Grab a coffee and carry on..</p>
<h3>Create a new Rails 3 app</h3>
<p>Got Rails 3 installed? If not, <code>gem install rails</code> will see you good. Then head on down to your favorite project folder with your shell and create a new Rails 3 app like so:</p>
<pre>rails new myapp --skip-test-unit</pre>
<p>You can retroactively bring RSpec 2 into an existing Rails 3 project, of course, but it's easier for this walkthrough to start afresh in case of application-specific issues.</p>
<h3>Hooking up RSpec 2 with RSpec-Rails</h3>
<p>Edit the <code>Gemfile</code> file in your new Rails project (<code>myapp/Gemfile</code> in this example) and add the following block to the bottom:</p>
<div>
<pre><span>group</span> <span>:development</span><span>,</span> <span>:test</span> <span>do</span>
  <span>gem</span> <span>&#39;rspec-rails&#39;</span>
<span>end</span>
</pre>
</div>
<p>This tells Bundler (a gem management and dependency tool Rails 3 likes to lean on) we want to use the <a href="https://github.com/dchelimsky/rspec-rails">rspec-rails</a> gem which will get RSpec running with Rails 3.0 for us. Next, we get Bundler to do its thing:</p>
<pre>bundle</pre>
<p>This will install all of the gems referenced in <code>Gemfile</code>, including <code>rspec-rails</code>. (You can use <code>bundle install</code> instead, if you prefer, but <code>bundle</code> on its own works too.)</p>
<p>Last but not least, we need to run RSpec's 'generator' that rspec-rails has put in place for us:</p>
<pre>rails generate rspec:install</pre>
<p>The generator creates a few files. Namely:</p>
<ul>
<li><code>.rspec</code> - a config file where we can store extra command line options for the <code>rspec</code> command line tool. By default it contains <code>--colour</code> which turns on colored output from RSpec.</li>
<li><code>spec</code> - a directory that will store all of the various model, controller, view, acceptance and other specs for your app</li>
<li><code>spec/spec_helper.rb</code> - a file that's loaded by every spec (not in any automatic way but most have <code>require 'spec_helper'</code> at the top). It sets the test environment, contains app level RSpec configuration items, loads support files, and more.</li>
</ul>
<p>We still can't run <code>rake</code> and see anything interesting yet because we don't have a database or any models initialized.</p>
<h3>Creating a model to test</h3>
<p>Let's take the easy way out and use the <code>scaffold</code> generator to flesh out something for us to test (as well as to see what spec files can be generated automatically):</p>
<pre>rails generate scaffold Person name:string age:integer zipcode:string</pre>
<p>It's worth noting that when you generate the scaffold numerous spec files are also created (thanks to <code>rspec-rails</code>):</p>
<pre>spec/models/person_spec.rb
spec/controllers/people_controller_spec.rb
spec/views/people/edit.html.erb_spec.rb
spec/views/people/index.html.erb_spec.rb
spec/views/people/new.html.erb_spec.rb
spec/views/people/show.html.erb_spec.rb
spec/helpers/people_helper_spec.rb
spec/routing/people_routing_spec.rb
spec/requests/people_spec.rb</pre>
<p>Now bring the database up to speed with the migration for the new model:</p>
<p><code>rake db:migrate</code></p>
<p>Now let's run <code>rake</code> - finally! The result:</p>
<pre>...............**............

Pending:
  PeopleHelper add some examples to (or delete) /Users/peter/dev/rails/myapp/spec/helpers/people_helper_spec.rb
    # Not Yet Implemented
    # ./spec/helpers/people_helper_spec.rb:14
  Person add some examples to (or delete) /Users/peter/dev/rails/myapp/spec/models/person_spec.rb
    # Not Yet Implemented
    # ./spec/models/person_spec.rb:4

Finished in 0.31043 seconds
29 examples, 0 failures, 2 pending</pre>
<p>Rock and roll. We're up and running. Sort of. Let's put in some "real" specs to be sure things are working nicely.</p>
<p>Change <code>spec/models/person_spec.rb</code> to the following rather contrived pair of specs:</p>
<div>
<pre><span>require</span> <span>&#39;spec_helper&#39;</span>

<span>describe</span> <span>Person</span> <span>do</span>
  <span>it</span> <span>&quot;can be instantiated&quot;</span> <span>do</span>
    <span>Person</span><span>.</span><span>new</span><span>.</span><span>should</span> <span>be_an_instance_of</span><span>(</span><span>Person</span><span>)</span>
  <span>end</span>

  <span>it</span> <span>&quot;can be saved successfully&quot;</span> <span>do</span>
    <span>Person</span><span>.</span><span>create</span><span>.</span><span>should</span> <span>be_persisted</span>
  <span>end</span>
<span>end</span>
</pre>
</div>
<p>Not the most useful things to spec out, admittedly, but you get a little database action and get rid of a <code>pending</code> spec we had cluttering things up. We haven't got anything else we can seriously test yet anyway.</p>
<p>Now let's run <code>rake spec:models</code> to focus our efforts on what we've just done:</p>
<pre>..

Finished in 0.09378 seconds
2 examples, 0 failures</pre>
<h3>How to have specs run automatically with Watchr</h3>
<p>Let's assume we've progressed with developing our app and we're working on models and controllers, testing along the way. Rather than running <code>rake</code> or <code>bundle exec rspec</code> all of the time, wouldn't it be great to have the relevant spec run <em>automatically</em> when we either edit the spec or a model/controller that has a spec? Well, with <a href="https://github.com/mynyml/watchr">watchr</a>, we can. <em>(Note: Some people prefer <a href="http://ph7spot.com/musings/getting-started-with-autotest">autotest</a>. I find watchr more flexible and useful for other things beyond just running specs.)</em></p>
<p><em>But if you really want to use autotest, Mike Bethany explains <a href="http://mikbe.tk/2011/02/10/blazingly-fast-tests/">how to set it up in a similar scenario</a> in a post of his own, along with autotest-growl for OS X notifications.</em></p>
<p>Add <code>watchr</code> to your <code>Gemfile</code>'s testing and production gem section:</p>
<div>
<pre><span>group</span> <span>:development</span><span>,</span> <span>:test</span> <span>do</span>
  <span>gem</span> <span>&#39;rspec-rails&#39;</span>
  <span>gem</span> <span>&#39;watchr&#39;</span>
<span>end</span>
</pre>
</div>
<p>Then run <code>bundle</code> to install it.</p>
<p>Next, create a file called <code>.watchr</code> in your app's root folder and populate it with this code:</p>
<div>
<pre><span>def</span> <span>run_spec</span><span>(</span><span>file</span><span>)</span>
  <span>unless</span> <span>File</span><span>.</span><span>exist?</span><span>(</span><span>file</span><span>)</span>
    <span>puts</span> <span>&quot;</span><span>#{</span><span>file</span><span>}</span><span> does not exist&quot;</span>
    <span>return</span>
  <span>end</span>

  <span>puts</span> <span>&quot;Running </span><span>#{</span><span>file</span><span>}</span><span>&quot;</span>
  <span>system</span> <span>&quot;bundle exec rspec </span><span>#{</span><span>file</span><span>}</span><span>&quot;</span>
  <span>puts</span>
<span>end</span>

<span>watch</span><span>(</span><span>&quot;spec/.*/*_spec\.rb&quot;</span><span>)</span> <span>do</span> <span>|</span><span>match</span><span>|</span>
  <span>run_spec</span> <span>match</span><span>[</span><span>0</span><span>]</span>
<span>end</span>

<span>watch</span><span>(</span><span>&quot;app/(.*/.*)\.rb&quot;</span><span>)</span> <span>do</span> <span>|</span><span>match</span><span>|</span>
  <span>run_spec</span> <span>%{spec/</span><span>#{</span><span>match</span><span>[</span><span>1</span><span>]</span><span>}</span><span>_spec.rb}</span>
<span>end</span>
</pre>
</div>
<p>This 'watchr script' directs a running watchr process to do a few things:</p>
<ul>
<li>If any file ending in <code>_spec.rb</code> under the <code>spec/</code> directory changes, run the <code>run_spec</code> method with its filename.</li>
<li>If any <code>.rb</code> file under the <code>app/</code> directory changes, call the <code>run_spec</code> method with an equivalently named <code>_spec.rb</code> file under <code>spec</code>.</li>
<li><code>run_file</code> accepts a filename for a spec file, checks it exists, and tells the system to run it (using <code>system</code>)</li>
</ul>
<p>If you now run <code>watchr .watchr</code> to use the <code>.watchr</code> script, not much will happen. But if you make any change (or even just re-save) to, say, <code>spec/models/person_spec.rb</code>, that spec will run automatically. Make a change to <code>app/models/person.rb</code> and it's the same deal. To stop watchr, CTRL+C saves the day.</p>
<p>Watchr can be used for a lot more than this but this is just for starters ;-)</p>
<p>Optionally, you might also like to create <code>lib/tasks/watchr.rake</code> and include the following code so you can just remember to run <code>rake watchr</code> instead (it's nice to have anything you run within a project contained in one place):</p>
<div>
<pre><span>desc</span> <span>&quot;Run watchr&quot;</span>
<span>task</span> <span>:watchr</span> <span>do</span>
  <span>sh</span> <span>%{bundle exec watchr .watchr}</span>
<span>end</span>
</pre>
</div>
<h3>How to get faster spec runs with Spork</h3>
<p>We've got Rails 3 running with RSpec 2 and watchr's giving us some automatically-running-spec love. But do you notice how slow it is? Specs run quickly once they're loaded but there are several seconds of waiting beforehand.</p>
<p><img src="http://www.rubyinside.com/wp-content/uploads/2011/02/yslow.png" alt="" title="yslow" width="130" height="106" style="float:right;margin-left:18px">If you run <code>time rake spec:models</code> with Ruby 1.9.2, you'll probably see a wallclock time of over 5 seconds (5.204s on my machine and I'm SSDed up) - holy splingledoops! If not, you're lucky, but it's <a href="http://groups.google.com/group/rubyonrails-core/browse_thread/thread/88519ef5a53088a1/c01ba447c6dc0de7?lnk=raot">a commonly reported problem</a> with some improvements expected in Ruby 1.9.3. We can't wait that long though..</p>
<p>Enter <a href="https://github.com/timcharper/spork">Spork</a>. Spork is a tool that loads the Rails environment and then <em>forks</em> each time you want to run some specs (or tests, it can be set up to run with <code>Test::Unit</code> too). In this way, the whole Rails initialization process is skipped, shaving valuable seconds off of your spec runs.</p>
<p><img src="http://www.rubyinside.com/wp-content/uploads/2011/02/horriblediagram1.png" alt="" title="horriblediagram" width="250" height="211" style="float:right;margin-left:16px;margin-bottom:8px">Edit your <code>Gemfile</code> again and include Spork:</p>
<pre>gem &#39;spork&#39;, &#39;~&gt; 0.9.0.rc&#39;</pre>
<p>Run <code>bundle</code> to install Spork.</p>
<p>Next, Spork needs to make some changes to your <code>spec/spec_helper.rb</code> file. Because it only initializes the Rails environment once and then forks it, you might have initialization features that you <em>need</em> to run on each test run. Spork will let you do this but it needs to make those changes first. Run:</p>
<pre>spork --bootstrap</pre>
<p>The result:</p>
<pre>Using RSpec
Bootstrapping /Users/peter/dev/rails/myapp/spec/spec_helper.rb.
Done. Edit /Users/peter/dev/rails/myapp/spec/spec_helper.rb now with your favorite text editor and follow the instructions.</pre>
<p>Bring up <code>spec/spec_helper.rb</code>. All <code>spork --bootstrap</code> has done is add some extra code to the top of the file. Read the comments there to get a better feel for what to do and what Spork requires and keep them in mind as we progress (in case you want to do something differently).</p>
<p>Get rid of <code>require 'rubygems'</code> from the first line - we're using Bundler so it's not necessary.</p>
<p>Next, <em>cut</em> and paste all of the 'old' contents of <code>spec_helper.rb</code> into the <code>Spork.prefork</code> block. Since we're running an empty(ish) project, there's nothing special we've added that we need to run on each run using the <code>Spork.each_run</code> block. We can leave that empty.</p>
<p>You'll end up with a <code>spec_helper.rb</code> file that looks like this:</p>
<div>
<pre><span>require</span> <span>&#39;spork&#39;</span>

<span>Spork</span><span>.</span><span>prefork</span> <span>do</span>
  <span># Loading more in this block will cause your tests to run faster. However, </span>
  <span># if you change any configuration or code from libraries loaded here, you&#39;ll</span>
  <span># need to restart spork for it take effect.</span>
  <span># This file is copied to spec/ when you run &#39;rails generate rspec:install&#39;</span>
  <span>ENV</span><span>[</span><span>&quot;RAILS_ENV&quot;</span><span>]</span> <span>||=</span> <span>&#39;test&#39;</span>
  <span>require</span> <span>File</span><span>.</span><span>expand_path</span><span>(</span><span>&quot;../../config/environment&quot;</span><span>,</span> <span>__FILE__</span><span>)</span>
  <span>require</span> <span>&#39;rspec/rails&#39;</span>

  <span># Requires supporting ruby files with custom matchers and macros, etc,</span>
  <span># in spec/support/ and its subdirectories.</span>
  <span>Dir</span><span>[</span><span>Rails</span><span>.</span><span>root</span><span>.</span><span>join</span><span>(</span><span>&quot;spec/support/**/*.rb&quot;</span><span>)</span><span>].</span><span>each</span> <span>{</span><span>|</span><span>f</span><span>|</span> <span>require</span> <span>f</span><span>}</span>

  <span>RSpec</span><span>.</span><span>configure</span> <span>do</span> <span>|</span><span>config</span><span>|</span>
    <span># == Mock Framework</span>
    <span>#</span>
    <span># If you prefer to use mocha, flexmock or RR, uncomment the appropriate line:</span>
    <span>#</span>
    <span># config.mock_with :mocha</span>
    <span># config.mock_with :flexmock</span>
    <span># config.mock_with :rr</span>
    <span>config</span><span>.</span><span>mock_with</span> <span>:rspec</span>

    <span># Remove this line if you&#39;re not using ActiveRecord or ActiveRecord fixtures</span>
    <span>config</span><span>.</span><span>fixture_path</span> <span>=</span> <span>&quot;</span><span>#{</span><span>::</span><span>Rails</span><span>.</span><span>root</span><span>}</span><span>/spec/fixtures&quot;</span>

    <span># If you&#39;re not using ActiveRecord, or you&#39;d prefer not to run each of your</span>
    <span># examples within a transaction, remove the following line or assign false</span>
    <span># instead of true.</span>
    <span>config</span><span>.</span><span>use_transactional_fixtures</span> <span>=</span> <span>true</span>
  <span>end</span>
<span>end</span>

<span>Spork</span><span>.</span><span>each_run</span> <span>do</span>
  <span># This code will be run each time you run your specs.</span>
<span>end</span>
</pre>
</div>
<p>Head back to your shell and the root of your project and run <code>spork</code>:</p>
<pre>Using RSpec
Loading Spork.prefork block...
Spork is ready and listening on 8989!</pre>
<p>Now we're cooking with gas. Open another shell, head to the root of your project, and run <code>watchr .watchr</code> too. Then head to <code>spec/models/person_spec.rb</code> in your text editor and re-save it (or even make a change if you want). Your specs run but.. they're no faster! What's wrong?</p>
<p>It turns out we need to make another change so that RSpec knows we're running Spork. Edit the <code>.rspec</code> file (mentioned earlier) and add <code>--drb</code> to the line (so it probably reads <code>--colour --drb</code>). <em>Now</em>, edit the spec again, save, and.. fast!</p>
<p>You should note that if you use <code>rake</code> at this point to run your entire suite, it'll still not be particularly fast because <em>rake</em> itself is initializing Rails in order to do its job. But if you want to run your entire suite quickly, just run:</p>
<pre>rspec spec</pre>
<p>With our dummy app and on my machine, this runs in a wallclock time of 0.759s - a serious improvement over 5.2 seconds.</p>
<p>We have Rails 3, RSpec 2, watchr, spork, and SUPER-DUPER FAST SPECS all running on Ruby 1.9.2. Score!</p>
<p>A minor snafu will remain, though. If you update <code>app/models/person.rb</code>, the change won't take effect in your tests since Spork has the <em>old</em> <code>Person</code> still in memory. One way around this is to edit <code>config/environments/test.rb</code> and change:</p>
<pre>config.cache_classes = true</pre>
<p>To:</p>
<pre>config.cache_classes = false</pre>
<p>Now your app's classes are reloaded when necessary.</p>
<p><center><img src="http://www.rubyinside.com/wp-content/uploads/2011/02/awyeah.jpeg" alt="" title="awyeah" width="348" height="232" style="text-align:center;margin-left:auto;margin-right:auto"></center></p>
<div>
<a href="http://feeds.feedburner.com/~ff/RubyInside?a=9kQfKhEconY:MOU_srPkVJM:qj6IDK7rITs"><img src="http://feeds.feedburner.com/~ff/RubyInside?d=qj6IDK7rITs" border="0"></a> <a href="http://feeds.feedburner.com/~ff/RubyInside?a=9kQfKhEconY:MOU_srPkVJM:3H-1DwQop_U"><img src="http://feeds.feedburner.com/~ff/RubyInside?i=9kQfKhEconY:MOU_srPkVJM:3H-1DwQop_U" border="0"></a>
</div><img src="http://feeds.feedburner.com/~r/RubyInside/~4/9kQfKhEconY" height="1" width="1">