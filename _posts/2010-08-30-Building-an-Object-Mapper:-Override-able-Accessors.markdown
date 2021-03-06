---
layout: post
title:  "Building an Object Mapper: Override-able Accessors"
date:   2010-08-30 07:05:53
author: John Nunemaker
categories: program
---

## Building an Object Mapper: Override-able Accessors
### by John Nunemaker
### at 2010-08-30 07:05:53
### original <http://feedproxy.google.com/~r/railstips/~3/hya0Z5m5cEM/>

<p>There are several things I have learned building object mappers that I now take for granted. Last week while pairing with <a href="http://jasonseifer.com/">Jason</a>, I was explaining a trick and he said I should blog about it, so here goes nothing.</p>
<p>Let’s say you are building a new object mapper named TacoMapper. A sensible place to start is with attribute accessors. One other thing to note is that we don’t care about old news, so we won’t support Rails 2 in any fashion. Deciding this, we can take advantage of ActiveModel and new features in ActiveSupport.</p>
<h2>First Goal</h2>
<p>First, let’s think about <span>API</span>. Our first goal will be to make the following work:</p>
<pre><code>class User
  include TacoMapper
  attribute :email
end

user = User.new
user.email = 'John@Doe.com'
puts user.email # "John@Doe.com"</code></pre>
<h2>First Solution</h2>
<p>The first thing we need is a class method named attribute.</p>
<pre><code>require 'active_model'
require 'active_support/all'

module TacoMapper
  extend ActiveSupport::Concern

  module ClassMethods
    def attribute(name)
      attr_accessor(name)
    end
  end
end

class User
  include TacoMapper
  attribute :email
end

user = User.new
user.email = 'John@Doe.com'
puts user.email # "John@Doe.com"</code></pre>
<p>ActiveSupport::Concern is a handy little ditty that does a lot out of the box. In our case, it will automatically call extend(ClassMethods) and add our attribute class method whenever our TacoMapper module gets included. With just a tiny bit of code, we have met our first goal.</p>
<h2>Second Goal</h2>
<p>Now, I am going to throw a kink in the mix. The goal of this post is to create override-able accessors. Let’s say we want to <strong>override the email writer</strong> method and make sure that <strong>we always get lowercase</strong> emails. If we override our attribute accessors right now, we have to set the instance variable for things to work and we get no benefit from TacoMapper.</p>
<pre><code>require 'active_model'
require 'active_support/all'

module TacoMapper
  extend ActiveSupport::Concern

  module ClassMethods
    def attribute(name)
      attr_accessor(name)
    end
  end
end

class User
  include TacoMapper
  attribute :email

  def email=(value)
    @email = value.to_s.downcase
  end
end

user = User.new
user.email = 'John@Doe.com'
puts user.email # "john@doe.com"</code></pre>
<p>In this simple example, that might be ok. But what if other things were in the mix, like dirty tracking, typecasting, etc.? Those other things would immediately stop working. <strong>Would it not be nice</strong> if we could just override our accessor and call super to get all the normal functionality of TacoMapper? I am glad you agree.</p>
<h2>Second Solution</h2>
<p>If you haven’t yet, you might want to read <a href="http://railstips.org/blog/archives/2009/08/20/lookin-on-up-to-the-east-side/">Lookin’ on Up…To the East Side</a>, a post I wrote on how Ruby’s method lookups work. In it, I explain that if you include a module, you can override methods that were in the module and call super. We’ll do the same in TacoMapper so we can make things a bit more robust.</p>
<pre><code>require &#39;active_model&#39;
require &#39;active_support/all&#39;

module TacoMapper
  extend ActiveSupport::Concern

  module ClassMethods
    def attribute_accessors_module
      @attribute_accessors_module ||= Module.new.tap { |mod| include(mod) }
    end

    def attribute(name)
      attribute_accessors_module.module_eval &lt;&lt;-CODE
        def #{name}
          @#{name}
        end

        def #{name}=(value)
          @#{name} = value
        end
      CODE
    end
  end
end

class User
  include TacoMapper
  attribute :email

  def email=(value)
    super(value.to_s.downcase)
  end
end

user = User.new
user.email = &#39;John@Doe.com&#39;
puts user.email # &quot;john@doe.com&quot;</code></pre>
<p>Note that we get the same result as the previous example, except that now we can just call super and still take advantage of all the loveliness that TacoMapper will eventually provide. We changed a couple of key things, so lets cover the differences in detail.</p>
<p>First, we created a method (<code>attribute_accessors_module</code>) that returns a memoized module and includes it in the current class. No matter how many calls you make to this method, it will return the first module we created and it will only be included once, since we memoized it (<code>||=</code>).</p>
<p>Second, since we have a module and it is included in our class, all we have to do is module_eval our accessor methods into it. This is what is happening in the attribute method.</p>
<p>Third, instead of setting the email instance variable in the email= method, we just call super, which will call the method we module_eval’d into our accessors module.</p>
<p>Pretty sweet, eh?</p>
<h2>Third Solution</h2>
<p>We could stop there, but we said we were going to use ActiveModel a bit, right? ActiveModel has a module for attribute accessors that does the same thing as above and a bit more  (although a bit confusing).</p>
<pre><code>require &#39;set&#39;
require &#39;active_model&#39;
require &#39;active_support/all&#39;

module TacoMapper
  extend ActiveSupport::Concern
  include ActiveModel::AttributeMethods

  included do
    attribute_method_suffix(&#39;&#39;, &#39;=&#39;)
  end

  module ClassMethods
    def attributes
      @attributes ||= Set.new
    end

    def attribute(name)
      attributes &lt;&lt; name.to_s
    end
  end

  module InstanceMethods
    def attribute(key)
      instance_variable_get(&quot;@#{key}&quot;)
    end

    def attribute=(key, value)
      instance_variable_set(&quot;@#{key}&quot;, value)
    end

    def attributes
      self.class.attributes
    end
  end
end

class User
  include TacoMapper
  attribute :email

  def email=(value)
    super(value.to_s.downcase)
  end
end

user = User.new
user.email = &#39;John@Doe.com&#39;
puts user.email # &quot;john@doe.com&quot;</code></pre>
<p>Note that again, we get the same result and that we are using super as before. So what changed this time?</p>
<p>First, we included <code>ActiveModel::AttributeMethods</code>. We then took advantage of the <code>attribute_method_suffix</code> method it provides to declare that we would have a reader (<code>''</code>) and a writer (<code>'='</code>). Now all our attribute class method has to do is add the attribute to the set of attributes.</p>
<p>Lastly, we define methods that implement the suffix methods we defined (<code>attribute</code> and <code>attribute=</code>). Note that we also define the attributes instance method so that ActiveModel knows when it is dealing with one of our attributes. Now, it takes only a few more lines of code to add a boolean presence method.</p>
<pre><code>require &#39;set&#39;
require &#39;active_model&#39;
require &#39;active_support/all&#39;

module TacoMapper
  extend ActiveSupport::Concern
  include ActiveModel::AttributeMethods

  included do
    attribute_method_suffix(&#39;&#39;, &#39;=&#39;, &#39;?&#39;)
  end

  module ClassMethods
    def attributes
      @attributes ||= Set.new
    end

    def attribute(name)
      attributes &lt;&lt; name.to_s
    end
  end

  module InstanceMethods
    def attribute(key)
      instance_variable_get(&quot;@#{key}&quot;)
    end

    def attribute=(key, value)
      instance_variable_set(&quot;@#{key}&quot;, value)
    end

    def attribute?(key)
      instance_variable_get(&quot;@#{key}&quot;).present?
    end

    def attributes
      self.class.attributes
    end
  end
end

class User
  include TacoMapper
  attribute :email

  def email=(value)
    super(value.to_s.downcase)
  end
end

user = User.new
puts user.email? # false
user.email = &#39;John@Doe.com&#39;
puts user.email? # true</code></pre>
<p>Using ActiveModel like this makes adding things like dirty tracking take a few minutes instead of a few hours. That said, the main point of this post is how the internals of ActiveModel actually work and how <strong>you can do it on your own</strong> if you so choose.</p>
<p>For those of you that are <strong>MongoMapper users</strong>, it has the same functionality built in though in a not as elegant way (which I will be updating soon). Also, your accessors are not used when loading things from the database, as that is handled internally. This means you can make your public accessors do whatever you want and it will not foobar the loading of your documents.</p>
<p>Hope this helps others trying to grok ActiveModel or build your own object mapper. If <strong>you enjoyed this post</strong> and <strong>would like to learn more</strong> about building an object mapper, <strong>let me know</strong> with a comment and I will try to round up a few more posts on the topic.</p><div>
<a href="http://feeds.feedburner.com/~ff/railstips?a=hya0Z5m5cEM:svTAnOrbqTM:yIl2AUoC8zA"><img src="http://feeds.feedburner.com/~ff/railstips?d=yIl2AUoC8zA" border="0"></a> <a href="http://feeds.feedburner.com/~ff/railstips?a=hya0Z5m5cEM:svTAnOrbqTM:dnMXMwOfBR0"><img src="http://feeds.feedburner.com/~ff/railstips?d=dnMXMwOfBR0" border="0"></a> <a href="http://feeds.feedburner.com/~ff/railstips?a=hya0Z5m5cEM:svTAnOrbqTM:7Q72WNTAKBA"><img src="http://feeds.feedburner.com/~ff/railstips?d=7Q72WNTAKBA" border="0"></a>
</div><img src="http://feeds.feedburner.com/~r/railstips/~4/hya0Z5m5cEM" height="1" width="1">