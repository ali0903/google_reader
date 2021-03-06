---
layout: post
title:  "Caching With Mongo"
date:   2010-07-15 21:00:04
author: John Nunemaker
categories: program
---

## Caching With Mongo
### by John Nunemaker
### at 2010-07-15 21:00:04
### original <http://feedproxy.google.com/~r/railstips/~3/O0SwjNXhez4/>

<p>For those of you that do not <a href="http://twitter.com/jnunemaker">follow me on Twitter</a> or <a href="http://github.com/jnunemaker">Github</a>, a while back I released <a href="http://github.com/jnunemaker/bin">Bin</a>, an ActiveSupport MongoDB cache store. Since I have been quiet here, I thought I would talk a bit about it to help get back in the swing of things.</p>
<p>Using Bin is just like using any other AS cache store.</p>
<pre><code>connection = Mongo::Connection.new
db = connection['bin_cache']

Rails::Initializer.run do |config|
  config.cache_store = Bin::Store.new(db)
end</code></pre>
<p>Once you have set things up, you can use all the typical Rails.cache methods.</p>
<pre><code>Rails.cache.write('foo', 'bar')
Rails.cache.read('foo') # 'bar'

Rails.cache.fetch('foo') do
  # some expensive thing
end</code></pre>
<p>The list goes on, but in the interest of brevity, I will just link you to the <a href="http://github.com/jnunemaker/bin/blob/master/spec/bin/store_spec.rb">specs</a>. The cool thing about bin is that it supports both ActiveSupport 2 and 3 along with Ruby 1.8.7 and 1.9.1. Oh, and it supports expires with the same <span>API</span> as the memcache store.</p>
<p>That pretty much covers the basics, feel free to go kick the tires or hang around here a bit to learn how I made Bin work with AS2 and AS3.</p>
<h2>Supporting AS2/3</h2>
<p>In both ActiveSupport 2 and 3, you inhert from ActiveSupport::Cache::Store to create a new store. The difference between the version is quite subtle though. In AS3, you override the methods read, write, etc. as needed and use super with a block to get the inherited functionality. In AS2, you do the same thing, but super does not accept a block. Being that as a community we are now mugwumps (mug on Rails 2 and wumps on Rails 3), I thought it would be nice to support both.</p>
<p>In order to make this happen, I knew all I need to do was shim compatibility for Rails 2. So what I did is create a compatibility class that inherits from ActiveSupport::Cache::Store for AS3 and then if active support’s version is less than 3, I reopen the class and add in the compatibility stuff to make it work like 3. Here is the code in its entirety:</p>
<pre><code># encoding: UTF-8
module Bin
  class Compatibility &lt; ActiveSupport::Cache::Store
    def increment(key, amount=1)
      yield
    end

    def decrement(key, amount=1)
      yield
    end
  end

  if ActiveSupport::VERSION::STRING &lt; &#39;3&#39;
    class Compatibility
      def write(key, value, options=nil, &amp;block)
        super(key, value, options)
        yield
      end

      def read(key, options=nil, &amp;block)
        super
        yield
      end

      def delete(key, options=nil, &amp;block)
        super
        yield
      end

      def delete_matched(matcher, options=nil, &amp;block)
        super
        yield
      end

      def exist?(key, options=nil, &amp;block)
        super
        yield
      end
    end
  end
end</code></pre>
<p>So then Bin::Store just inherits from Compatibility:</p>
<pre><code>module Bin
  class Store &lt; Compatibility
    # ... stuff
  end
end</code></pre>
<p>I cringe using a specific version string comparison like above, but it was simple and worked so I went with it. The last piece of the puzzle was setting up rake tasks to run the specs against different active support versions.</p>
<pre><code>namespace :spec do
  Spec::Rake::SpecTask.new(:all) do |t|
    t.ruby_opts &lt;&lt; &#39;-rubygems&#39;
    t.verbose = true
  end

  task :as2 do
    sh &#39;ACTIVE_SUPPORT_VERSION=&quot;&lt;= 2.3.8&quot; rake spec:all&#39;
  end

  task :as3 do
    sh &#39;ACTIVE_SUPPORT_VERSION=&quot;&gt;= 3.0.0.beta3&quot; rake spec:all&#39;
  end
end

desc &#39;Runs all specs against Active Support 2 and 3&#39;
task :spec do
  Rake::Task[&#39;spec:as2&#39;].invoke
  Rake::Task[&#39;spec:as3&#39;].invoke
end</code></pre>
<p>Note that I make an all task to run the specs then two distinct tasks to run against AS2 and AS3. All those tasks do is set an environment variable that I use in the test to force a particular version.</p>
<pre><code>gem 'activesupport', ENV['ACTIVE_SUPPORT_VERSION']</code></pre>
<p>Now when I run rake, it runs the tests against a 2.3 and a 3.0+ version of ActiveSupport so I know when something goes wrong with either. No flipping gem sets or other shenanigans. As always, if you have improvements or other way so doing stuff like this, please let me know. I am here to learn people.</p>
<p>Using Bin on the last project I worked on to cache large fragments of the layout significantly reduced response times. Always fun to see numbers like that drop after a deploy!</p><div>
<a href="http://feeds.feedburner.com/~ff/railstips?a=O0SwjNXhez4:IZuvIToWM1k:yIl2AUoC8zA"><img src="http://feeds.feedburner.com/~ff/railstips?d=yIl2AUoC8zA" border="0"></a> <a href="http://feeds.feedburner.com/~ff/railstips?a=O0SwjNXhez4:IZuvIToWM1k:dnMXMwOfBR0"><img src="http://feeds.feedburner.com/~ff/railstips?d=dnMXMwOfBR0" border="0"></a> <a href="http://feeds.feedburner.com/~ff/railstips?a=O0SwjNXhez4:IZuvIToWM1k:7Q72WNTAKBA"><img src="http://feeds.feedburner.com/~ff/railstips?d=7Q72WNTAKBA" border="0"></a>
</div><img src="http://feeds.feedburner.com/~r/railstips/~4/O0SwjNXhez4" height="1" width="1">