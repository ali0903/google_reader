---
layout: post
title:  "Getting started with Heroku"
date:   2010-12-15 02:31:29
author: Ben Scofield
categories: program
---

## Getting started with Heroku
### by Ben Scofield
### at 2010-12-15 02:31:29
### original <http://feedproxy.google.com/~r/LearningRubyBlog/~3/WJCnFOZBj2Q/>

<p></p>
<div>
<h3>Getting started with Heroku</h3>
<p>This guest post is by <strong><a href="http://benscofield.com/about/">Ben Scofield</a></strong>, who is Heroku’s developer advocate, responsible for listening to the tens of thousands of developers deploying their Ruby applications to the cloud. He’s spoken at many conferences around the world, and in 2010 became the co-chair for RailsConf.</p>
<h3>Introduction</h3>
<p><img style="float:right;padding:4px;margin:0 0 2px 7px" src="http://rubylearning.com/images/ben-scofield.jpg" alt="Ben Scofield" title="Ben Scofield"> <span>H</span>eroku has been in the news a lot lately, and it’s been a popular choice for Ruby application developers for a few years.If you haven’t worked with it before, here’s your chance — it’s designed to be as painless as possible to get going, and to give a powerful, stable, and scalable platform for your code.</p>
<h3>Setting up</h3>
<p>If this is your first time working with Heroku, you’ll need to start by setting up an account. Visit <a href="https://api.heroku.com/signup">https://api.heroku.com/signup</a> and enter your email address. You’ll soon get an email to confirm your account; click on the confirmation link and choose a password, and you’re registered!</p>
<p>Next, you’ll want to create an app (or find an existing one you want to push). Heroku supports any Rack-based Ruby web framework — so you can use Rails, Sinatra, Camping, Ramaze, or pretty much anything you want. Let’s say you’re going to build a new Rails application:</p>
<pre>$ rails new myapp
$ cd myapp
</pre>
<p>After you’ve chosen (or created) your app, you’ll need to make sure it’s tracked in git:</p>
<pre>$ git init
$ git add .
$ git commit -m "initial commit"
</pre>
<p>Once you’ve got your app ready to go, you’ll want to install the heroku gem. As you’ll see, it’s a powerful tool for managing your apps from the command line.</p>
<pre>$ gem install heroku
</pre>
<p>And now, from your application route, run:</p>
<pre>$ heroku create
</pre>
<p>If this is your first time using the heroku CLI, it’ll prompt you for your username and password — on subsequent uses, it’ll pull  your username and API key for accessing Heroku from <b>~/.heroku/credentials</b>, but that doesn’t exist until you’ve logged in through the CLI. It will also upload your public SSH key, and finally it’ll create your new application on Heroku and add a git remote.</p>
<p>If you want to specify the name of your app (and thus the subdomain on Heroku), you can pass an argument:</p>
<pre>$ heroku create myapp # created myapp.heroku.com
</pre>
<p>Finally, to push your code to heroku, push it as you would to any git remote:</p>
<pre>$ git push heroku master
$ heroku rake db:migrate # you'll need to do this for any schema change
</pre>
<p>You’ll see feedback on the process, but by the end your code should be up and running on Heroku’s platform!</p>
<p>Of course, there’s a lot more to working with Heroku than just that, so here’s a little more information.</p>
<h3>CLI</h3>
<p>The heroku gem gives you a lot more than just ‘<b>heroku create</b>‘, though. It provides a full CLI for working with your application. Here’s an incomplete list of what you can do with it:</p>
<h4>rake</h4>
<p>You can run any rake task you like by prefacing it with ‘<b>heroku rake</b>‘:</p>
<pre>$ heroku rake db:migrate
$ heroku rake routes
</pre>
<p>(Note that heroku doesn’t run your migrations by default — when you change your schema, you’ll need to run ‘<b>heroku rake db:migrate</b>‘ to update your production database.)</p>
<h4>Resources</h4>
<p>You can change your resource allocation from the command-line, too. ‘<b>heroku dynos 5</b>‘ sets your application to 5 dynos; you can do the same with workers. As you’ll see below, this extends to add-ons, as well.</p>
<h4>config</h4>
<p>Many capistrano-deployed projects have sensitive configuration information (database.yml, etc.) in a shared folder on the server. When a new version of the code is deployed, those files get symlinked into the app. On Heroku, that’s not possible. Instead, the best practice is to use config variables.</p>
<pre>$ heroku config # lists all configuration variables
$ heroku config:add NAME=VALUE # set a new variable
$ heroku config:remove NAME # remove an existing variable
</pre>
<p>There’s also a ‘<b>heroku config:clear</b>‘ command, but it’s dangerous — it clears out all your environment variables, which includes those set by Heroku itself. If you do that, then there’s a very good chance you’ll lose information that you might not know (e.g., your DATABASE_URL).</p>
<h4>add-ons</h4>
<p>Heroku allows third-party developers to create add-ons for your application, providing for both infrastructure features (exception tracking through <a href="http://addons.heroku.com/exceptional">Exceptional</a> and <a href="http://addons.heroku.com/hoptoad">Hoptoad</a> and business features (email delivery through <a href="http://addons.heroku.com/sendgrid">SendGrid</a>, subscription billing through <a href="http://addons.heroku.com/recurly">Recurly</a>, etc.) As the owner of an application, you can manage your add-ons from the command line:</p>
<pre>$ heroku addons # lists addons
$ heroku addons:add newrelic:bronze # app monitoring for free? count me in!
$ heroku addons:remove piggyback_ssl
</pre>
<h4>plugins</h4>
<p>Add-ons extend your application’s functionality; plugins extend the heroku gem itself. You can see available plugins at <a href="http://herocutter.heroku.com/">Herocutter</a>, but some of our favorites are:</p>
<ul>
<li><a href="http://herocutter.heroku.com/plugins/1">colorize\_console</a> for wirble colors in the heroku console</li>
<li><a href="http://herocutter.heroku.com/plugins/13">tab\_complete\_console</a> for tab completion</li>
<li><a href="http://herocutter.heroku.com/plugins/19">heroku-accounts</a> for managing multiple Heroku accounts on the same machine</li>
</ul>
<h4>And more</h4>
<p>The heroku CLI provides even more functionality; take a look through the <a href="http://docs.heroku.com/heroku-command">documentation</a> or its own help (‘<b>heroku</b>‘)  to see more.</p>
<h3>Common problems</h3>
<p>Heroku imposes some constraints on your application; some of these stem from architectural decisions and are pretty much unavoidable, while others come from less fundamental decisions and can be worked around.</p>
<h4>Filesystem access</h4>
<p>Heroku’s architecture means that you can never be certain that your application will be running in the same space for two separate requests — two different dynos might serve later requests, and a single dyno might be moved from one EC2 instance to another for a variety of reasons. Because of that, Heroku doesn’t allow you access to the filesystem; it just doesn’t make sense.</p>
<p>To solve this, you should use an external service to host content that you might want to serve up. Filesystem page caching, for instance, can be replaced by properly using Heroku’s <a href="http://docs.heroku.com/http-caching#example-caching-barcode-images-generated-with-imagemagick">HTTP caching layer</a>. Uploaded assets should be <a href="http://docs.heroku.com/constraints#large-static-assets">saved to S3</a> or a similar service.</p>
<h4>Process timeouts</h4>
<p>Heroku has some opinions about acceptable HTTP behavior, and timeouts are a result of that. If you have a request that runs for more than 30 seconds, the platform will automatically kill it. For many apps, this might include hitting remote services (like the Twitter API) or doing file processing (with Paperclip or a similar tool).</p>
<p>The solution is to move those long-running processes into a background worker. You can read more about this in the <a href="http://docs.heroku.com/background-jobs">documentation</a>.</p>
<h4>Idling</h4>
<p>Heroku is fantastic for experimentation, which leads to a predictable conclusion:  a lot of abandoned applications on the platform. In order to keep them from chewing up an inordinate amount of resources, the platform treats single-dyno applications a little differently (with the assumption that an experiment is likely to be running on the free plan): if the app hasn’t been hit in a certain amount of time, it gets idled (or spun down). Then, on the next attempt to access it, the single dyno is unidled.</p>
<p>The effect of this is very similar to what you might do on your local development box. When you’re working on an app, you fire up a local app server (with rails server or something like that); when you stop working on it, you shut down the server. Then, when you next want to hit the app, you have to spend a few seconds starting the server again.</p>
<h4>Postgres migration</h4>
<p>Every application on Heroku gets its own database — by default, it’s a 5MB shared Postgres db, though you can pay to get larger (or dedicated) instances. This can cause problems, since the majority of Rubyists seem to use MySQL in development, and Postgres and MySQL aren’t always same in how they treat SQL and display messages. You can see some of the most common issues (and their solutions) in the <a href="http://docs.heroku.com/database#common-issues-migrating-to-postgresql">Heroku documentation</a>.</p>
<h3>Troubleshooting</h3>
<p>Every app runs into problems in production — and sometimes an exception tracker (like Exceptional or Hoptoad) don’t give you all the information you need to fix it. On a VPS or dedicated server, you might be accustomed to SSHing in and popping into an interactive console, digging through logs, or something similar. With Heroku, that isn’t an option — but we do have some alternatives, provided in the heroku gem.</p>
<h4>heroku console</h4>
<p>You might not be able to run rails server or irb on the server yourself, but ‘<b>heroku console</b>‘ gives you an interactive shell for your application. Once in the shell, you’re interacting directly with your production instance, so be as careful as you’d normally be when futzing with production data.</p>
<p>There are a few things to be aware of with this console. First, it runs over HTTP — every command you enter is pushed up to Heroku as an HTTP request, so it’s subject to the same restrictions as your web app. For instance, any process you start that runs longer than 30 seconds will be killed. Also, requests from your console session tie up a dyno, so if you’re running on a single dyno then your web app isn’t available to serve regular requests while you’re updating your database.</p>
<p>The other important thing to note is that each line you send is a separate HTTP request. This means that you can’t write multi-line code in the heroku console. Say you’re trying to do this:</p>
<pre>User.all.each do |user|
  puts user.email
end
</pre>
<p>When you hit enter after the first line, the console sends ‘<code>&gt;User.all.each do |user|</code> to the server, which isn’t a complete expression. Before you can start typing the next line, then, the system sends back an error. You can still run this code, but you have to rewrite it to be on a single line:</p>
<pre>User.all.each {|user| puts user.email}
</pre>
<h4>heroku ps</h4>
<p>Sysadmins live and die by process lists, so Heroku provides a tool to see what processes you have available and what state they’re in. If you run <b>heroku ps</b> for an active application, you’ll see something like the following:</p>
<pre>
    UPID     Slug          Command                     State       Since
    -------  ------------  --------------------------  ----------  ---------
    xxxxxxx  xxxxxxxxxxxx  dj                          up          16h ago
    xxxxxxx  xxxxxxxxxxxx  cron                        idle        43m ago
    xxxxxxx  xxxxxxxxxxxx  dyno                        up          16h ago
    xxxxxxx  xxxxxxxxxxxx  dyno                        up          16h ago
</pre>
<p>This is especially useful when combined with the Unix <a href="http://en.wikipedia.org/wiki/Watch_(Unix)">watch</a> command (if you’re on OS X, you may have to <a href="http://osxdaily.com/2010/08/22/install-watch-command-on-os-x/">install it manually</a>), which reruns the command periodically so you can see how things are changing in real-time.</p>
<h4>heroku logs</h4>
<p>And finally, the logs. Anyone who’s built a web app knows just how important logs are, so Heroku provides a set of tools to help review (and in some cases analyze) them.</p>
<p>To use Heroku’s logging, you have to install both a plugin and an add-on:</p>
<pre>$ heroku plugins:install http://github.com/heroku/heroku-logging.git
$ heroku addons:add logging
</pre>
<p>Once that’s done, anything your app pushes to STDOUT or STDERR is captured in your logs — if you’re using Rails, you should make sure to redirect your logger to STDOUT by adding this line to your config (application.rb or environment.rb, depending on what version of Rails you’re running):</p>
<pre>config.action_controller.logger = Logger.new(STDOUT)
</pre>
<p>The ‘<b>heroku logs</b>‘ command by itself will show you the last 20 lines of your log, looking something like this:</p>
<pre>
    2010-12-10T15:13:46-07:00 app[web.1]: Completed in 74ms (View: 31, DB: 40) | 200 OK [http://myapp.heroku.com/]
    2010-12-10T15:13:46-07:00 heroku[router]: GET myapp.heroku.com/posts queue=0 wait=0ms service=1ms bytes=975
    2010-12-10T15:13:47-07:00 app[worker.1]
</pre>
<p>You can filter the logs by source (-s) and process (-p), you can tail them in real-time with -t, and you can ask for a specific number of lines with -n. Perhaps most powerfully, you can also add syslog drains for your logs, pushing syslog packets to another server for long-term storage or analysis:</p>
<pre>$ heroku logs:drains add syslog://your.syslog.host
</pre>
<h3>Where to go for help</h3>
<p>This is just the tip of the iceberg, really — there’s a lot you can do with Heroku, and spending time digging into the platform is very worthwhile. Take a look at our <a href="http://docs.heroku.com">documentation</a>, and talk to other developers in our <a href="http://groups.google.com/group/heroku">Google group</a> and on <a href="javascript:void(0);">IRC</a>.</p>
<p><em>Feel free to ask questions and give feedback in the comments section of this post. Thanks and Good Luck!</em></p>
</div>
<p>Technorati Tags: <a href="http://technorati.com/tag/Programming" rel="tag">Programming</a>, <a href="http://technorati.com/tag/Ruby+programming" rel="tag">Ruby programming</a>, <a href="http://technorati.com/tag/Heroku" rel="tag"> Heroku</a></p>

<div>
<a href="http://feeds.feedburner.com/~ff/LearningRubyBlog?a=WJCnFOZBj2Q:tzQOvXhj2N0:yIl2AUoC8zA"><img src="http://feeds.feedburner.com/~ff/LearningRubyBlog?d=yIl2AUoC8zA" border="0"></a> <a href="http://feeds.feedburner.com/~ff/LearningRubyBlog?a=WJCnFOZBj2Q:tzQOvXhj2N0:MclgosYkiZA"><img src="http://feeds.feedburner.com/~ff/LearningRubyBlog?i=WJCnFOZBj2Q:tzQOvXhj2N0:MclgosYkiZA" border="0"></a> <a href="http://feeds.feedburner.com/~ff/LearningRubyBlog?a=WJCnFOZBj2Q:tzQOvXhj2N0:F7zBnMyn0Lo"><img src="http://feeds.feedburner.com/~ff/LearningRubyBlog?i=WJCnFOZBj2Q:tzQOvXhj2N0:F7zBnMyn0Lo" border="0"></a> <a href="http://feeds.feedburner.com/~ff/LearningRubyBlog?a=WJCnFOZBj2Q:tzQOvXhj2N0:qj6IDK7rITs"><img src="http://feeds.feedburner.com/~ff/LearningRubyBlog?d=qj6IDK7rITs" border="0"></a> <a href="http://feeds.feedburner.com/~ff/LearningRubyBlog?a=WJCnFOZBj2Q:tzQOvXhj2N0:gIN9vFwOqvQ"><img src="http://feeds.feedburner.com/~ff/LearningRubyBlog?i=WJCnFOZBj2Q:tzQOvXhj2N0:gIN9vFwOqvQ" border="0"></a> <a href="http://feeds.feedburner.com/~ff/LearningRubyBlog?a=WJCnFOZBj2Q:tzQOvXhj2N0:-BTjWOF_DHI"><img src="http://feeds.feedburner.com/~ff/LearningRubyBlog?i=WJCnFOZBj2Q:tzQOvXhj2N0:-BTjWOF_DHI" border="0"></a>
</div><img src="http://feeds.feedburner.com/~r/LearningRubyBlog/~4/WJCnFOZBj2Q" height="1" width="1">