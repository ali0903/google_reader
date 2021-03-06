---
layout: post
title:  "Useful Rewrites for Nginx"
date:   2011-03-30 00:40:49
author: Kevin Rutten
categories: program
---

## Useful Rewrites for Nginx
### by Kevin Rutten
### at 2011-03-30 00:40:49
### original <http://www.engineyard.com/blog/2011/useful-rewrites-for-nginx/>

<span style="font-weight:normal"><img src="http://static.howstuffworks.com/gif/diesel-locomotive-engine-2.jpg" alt="" width="400" height="300">I enjoy working in Rails but also believe in using the right tool for the job.  Many developers will add redirects to a Rails app, which is great on the development box but can end up hurting when the production traffic starts coming.  We use the <a title="Nginx" href="http://wiki.nginx.org/Main">Nginx</a> web server as a front end reverse proxy and it’s easy to move some common redirects up into Nginx freeing Rails to do what it does best.</span>

In the initial focus group, people tell you the name is hard to remember, the SEO gurus say the domain should include www in it, and your usability guy says you should catch the most common misspellings.  Initially you could make all these changes in Rails.  Detect if www is missing and return a 302.  Detect use of a misspelled domain you registered and again 302 correct it.  If your site grows in popularity and traffic increases and goes into queue, the redirects will get queued adding to the load.  Maybe it would be better to off-load these up to the Web Server.

These can all be put in the Rails app but they are just as easy to put into the front end.  We’re not going to do anything crazy in Nginx, just some common but useful rewrites today.

<address>NOTE:<span style="font-style:normal"> If you're using a cluster on AppCloud you will need to use “listen 81;” in the examples before.  Just look what is already in the Nginx configurations.</span></address> <address><span style="font-style:normal"><span></span></span></address>
<h3>Rewrite Old Domain to New Domain</h3>
You have decided to change the domain name from beta_domain.com to bedom.com which is shorter and has a cool ring to it.  There is no reason to pass this down to Rails and it’s very easy to handle in Nginx.
<pre>  server {
    listen 80;
    server_name beta_domain.com www.beta_domain.com;
    rewrite ^ $scheme://www.bedom.com$request_uri permanent;
    # or
    # rewrite ^ $scheme://www.bedom.com ;
  }</pre>
This is a simple block that does quite a bit.  Any requests matching the old domain are caught and redirected.  The first redirect line with a $request_uri means “when we redirect, copy the URL part” so when they visit http://www.beta_domain.com/about they will be redirected to http://www.bedom.com/about.  The permanent means for the browser to remember this redirect forever (status 301) can be a little dangerous as we will show below.  The second version, which is commented out, is a simpler “redirect everything to the homepage” which might be better if you revamped your site in addition to renaming it.  This second version also returns a temporary redirect (status 302) so the browser will check this URL again in the future. This block is where you could add common “misspellings” domain that you registered.
<h3>Add or Remove www From Domain</h3>
So, now your SEO guy is saying you need to add www to the domain and make sure all requests include it since &lt;insert SEO voodoo&gt;.  Or maybe today, thanks to Twitter, you&#39;re told to remove it since “Shorter names are now sexy”.  Well, we have easy solutions for you here.
<pre>  # Add a www with this block
  server {
    listen 80;
    server_name bedom.com;
    rewrite ^(.*)$ $scheme://www.bedom.com$1;
  }</pre>
<pre>  # Remove a www with this block instead
  server {
    listen 80;
    server_name www.bedom.com;
    rewrite ^(.*)$ $scheme://bedom.com$1;
  }</pre>
Or, if you prefer to drop the www from multiple host names, you can add into the server block that would catch the host names (note: where possible the above is better).
<pre>  if ($host ~* www\.(.*)) {
    set $host_without_www $1;
    rewrite ^(.*)$ $scheme://$host_without_www$1 permanent; #1
    #rewrite ^ $scheme://$host_without_www$1request_uri permanent; #2
  }</pre>
This will check if the host name has a www in it and drop it.  You can now have a server line like:
<pre>  server_name www.bedom.com _ ;</pre>
or
<pre>  server_name domain1.com www.domain1.com domain2.com www.domain2.com ;</pre>
Depending on if you want to catch all or some domain names, you have a lot of control in what you do.  Also, the second version (commented out) will include the URI making it more seamless to the user.

<em>NOTE: </em>#1/#2 - The $1 is part of the regex and is the part caught between ()’s.  In the if statement that is the part *after* the 'www.' so the domain.com and in line #1 it’s everything between the start ‘^’ and the end ‘$’ on the regex.   This is a common form, but the second version #2 is better for that case since it doesn’t have Nginx process regex like we do in the if statement.
<h3>Rewrite All Domains To Proper Domain</h3>
One common request is to redirect all traffic so it uses the proper domain name.  You can hit 123.45.67.891 or ec2-123-45-67-891.compute-1.amazonaws.com directly but if you just have one site up, most people prefer to correct that to a domain name.
<pre>  server {
    listen 80 default;
    server_name _;
    rewrite ^ $scheme://www.bedom.com;
  }</pre>
This listens to all requests not caught by other server blocks and redirects them to http(s)://www.bedom.com which also helps with future SSL requests which would depend on the domain name.  You also don’t usually want to include the URI as other domains may have invalid URI’s (ones that wouldn’t match on your site).
<h3>Drop Obviously Bad Requests (.php/.aspx)</h3>
Sometimes the requests can go to the wrong site.  For example, an IP address is reused, a site is changed, or there is a typo in a configuration and you're getting requests that are obviously not for you.  Normally any request that isn’t for a static asset gets passed to your app.  You're running a Rails app so I’m 100% sure you won’t be using Active Server Pages and most likely won’t run PHP or .CGI files.  Adding this to your server block will drop the requests before they hit the Rails queue.
<pre>  # Drop requests to non-rails requests
  if ($request_filename ~* \.(aspx|php|jsp|cgi)$) {
    return 410;
  }</pre>
<pre>  # Since location blocks are usually preferred to "if"'s, this is a little better
  location ~ \.(aspx|php|jsp|cgi)$ {
    return 410;
  }</pre>
This ensures bad requests don’t overload the Rails app.  The 410 is a “don’t try this again” which should be better than a 404.
<h3>Different Types Of Rewrites and Gotchas</h3>
Earlier I mentioned that permanent redirects (status 301) can be dangerous.  Since the browser remembers this redirect you can have bad experiences if you are not careful.  An example that I have seen is:
<pre>  rewrite ^.*$ /coming_soon permanent;</pre>
This one was for a site that was going to do a relaunch and wanted all requests to go to a “coming soon” page.  The initial issue was that this caught all pages including coming_soon and ended up being an infinite redirect in the browsers cache.  That was bad.  They then used a location block to catch the coming_soon URL but then when they finally removed the rewrite and relaunched, they discovered many URLs that existed before were still redirecting to the coming_soon page.  The use of permanent meant that they had to get customers to clear their caches to view the relaunched site.  (Well, actually, I caught this shortly after they deployed it so it was primarily just the developers and testers who encountered this problem).
<pre>  rewrite ^(.*)$ https://$host$1 permanent;      #3
  rewrite ^ https://$host$request_uri permanent; #4</pre>
This second example was when a company used <code>ssl_requirement</code> in their app and set pages like about and company to force non-ssl.  After the Firesheep issue they added this line to force all requests to SSL, which is good, but they forgot to update <code>ssl_requirement</code> so when people went to the about page, they got bounced from http to https and back until the browser gave up.

<em>NOTE</em>: #3/#4 again both lines do the same thing but the second line is less work for Nginx.
<h3>Conclusion</h3>
This is just a brief introduction to some common redirects that you might use in your site.  You have many options available, just for rewriting domain names.  Rewrites are powerful and can be complex, but even a few simple ones can make a difference on your site.<p><a href="http://www.engineyard.com/blog"><img height="98" width="61" title="logo-engineyard" alt="" src="http://www.engineyard.com/blog/?getfile=4050"></a></p>