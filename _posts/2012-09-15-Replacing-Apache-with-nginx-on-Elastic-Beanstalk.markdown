---
layout: post
title:  "Replacing Apache with nginx on Elastic Beanstalk"
date:   2012-09-15 03:13:33
author: Nicholas C. Zakas
categories: program
---

## Replacing Apache with nginx on Elastic Beanstalk
### by Nicholas C. Zakas
### at 2012-09-15 03:13:33
### original <http://feedproxy.google.com/~r/nczonline/~3/l4kAv-8I6fY/>

<p>WellFurnished has been using Amazon’s Elastic Beanstalk<sup>[1]</sup> service for some time now with one of the default configurations. For those who are unaware, Elastic Beanstalk is Amazon’s answer to services like Heroku and Google App Engine. You set up an application and one or more environments made up of a load balancer and any number of EC2 instances. There are several default instance types you can select from such as Apache with Tomcat 6 or 7, Apache with PHP, and Apache with Python (all are available in either 32-bit or 64-bit configurations).</p>
<p>Once you have created an application, you can update any environment using Git. We’ve been using this for our deploys since the start. WellFurnished is written in Java using the Play framework<sup>[2]</sup> so we have been using the 32-bit configuration with Apache and Tomcat 7. Play can export an exploded WAR file of the application which we then checked into the Elastic Beanstalk Git repository for the application. Then, we sit back and watch as the update rolls out to the environments we specified.</p>
<p>It’s a really nice service that costs nothing over and above the AWS resources that you’re using. That being said, the number of configuration options is limited. Amazon says that you are always free to make modifications but there are no good tutorials or examples of how to do that. Over the past couple of days, I’ve gone through the process of replacing Apache with nginx in our Elastic Beanstalk instances. I did so by piecing together information I found spread across the Internet. This is my attempt to explain how I did it in the hopes that this will help others with the process.</p>
<h2>Understanding how it works</h2>
<p>Before modifying a configuration, it helps to understand exactly how Elastic Beanstalk works. Each of the default AMIs for Elastic Beanstalk comes configured with a Ruby on Rails web application called hostmanager. This application is responsible for interacting with Elastic Beanstalk including health checks and deployment of changes, among other things. As long as hostmanager is functioning properly, an EC2 instance will work properly within an Elastic Beanstalk application. It sounds simple, but in reality, this is usually where the problem is. Ensuring that hostmanager continues to work in the way that it used to is the key to creating custom EC2 configurations that still work with Elastic Beanstalk.</p>
<p>The hostmanager runs at <code>http://localhost:8999</code> on the machine and is accessible publicly at <code>/_hostmanager</code>. No matter what you do, you must ensure that <code>/_hostmanager</code> continues to work properly. The Rails app for hostmanager starts automatically so all you need to do is make sure that it’s publicly accessible. </p>
<p>One of Apache’s jobs on an Elastic Beanstalk instance is to make sure that hostmanager is accessible. It’s set up as a reverse proxy where <code>/_hostmanager</code> points to <code>http://localhost:8999</code>. Since my goal was to replace Apache with nginx, I had to make sure that this setting was preserved.</p>
<h2>Modifying an AMI</h2>
<p>To get started, it’s easiest to have an Elastic Beanstalk environment already running. As mentioned before, we were using the 32-bit Apache/Tomcat 7 configuration on a small instance. This configuration is stored in an Amazon produced AMI. In order to create a custom AMI based on that configuration, you need to create a standalone EC2 instance using that same AMI. </p>
<p>With Elastic Beanstalk already running, locate the EC2 instance in the EC2 section of the AWS console. Even though you’re using Elastic Beanstalk, it’s still just creates regular EC2 instances. If you’re unsure which EC2 instances being used then take a look at the elastic load balancer associated with that particular environment. That will give you the EC2 instance ID. In the EC2 section of the AWS Console, right click on the instance and select “Launch more likes this”. You’ll be taken through the EC2 instance creation process and in about a minute you’ll have an instance based off of the Elastic Beanstalk AMI.</p>
<p>Then you can SSH into the new instance and configure it however you want. By default, neither Apache nor tomcat are running. You will need to manually start the in order to have a working environment:</p>
<pre><code>sudo service httpd start
sudo service tomcat7 start</code></pre>
<p>Now everything is working just as it is in an instance used by Elastic Beanstalk. You’ll have to manually add in your application code if you want to test that, but otherwise the instance is completely functional.</p>
<h2>Installing and configuring nginx</h2>
<p>Swapping nginx for Apache is a simple procedure in theory. Since both are being used as reverse proxies, it’s a matter of ensuring that the nginx configuration is equivalent to the Apache configuration. Start by installing nginx:</p>
<pre><code>yum -y install nginx</code></pre>
<p>The next step is to modify the number of worker processes that nginx will use. By default it’s set to 1 and it’s a good idea to change it to 4. So find this line in <code>/etc/nginx/nginx.conf</code>:</p>
<pre><code>worker_processes  1;</code></pre>
<p>And change it to this:</p>
<pre><code>worker_processes  4;</code></pre>
<p>Next, setup proxy configuration for nginx (exact configuration taken from <cite>Hacking Elastic Beanstalk</cite> (O’Reilly)<sup>[3]</sup>. Create a file called <code>/etc/nginx/conf.d/proxy.conf</code> and place this inside:</p>
<pre><code>proxy_redirect            off;
proxy_set_header          Host            $host;
proxy_set_header          X-Real-IP       $remote_addr;
proxy_set_header          X-Forwarded-For $proxy_add_x_forwarded_for;
client_max_body_size      10m;
client_body_buffer_size   128k;
client_header_buffer_size 64k;
proxy_connect_timeout     90;
proxy_send_timeout        90;
proxy_read_timeout        90;
proxy_buffer_size         16k;
proxy_buffers             32              16k;
proxy_busy_buffers_size   64k;</code></pre>
<p>Note that you need to manually set some headers using nginx whereas Apache sets those for you when using the <code>ProxyPassReverse</code> directive.</p>
<p>There’s a default server setup with nginx when it’s installed, so remove that:</p>
<pre><code>sudo rm /etc/nginx/conf.d/default.conf</code></pre>
<p>Create a file called <code>/etc/nginx/conf.d/beanstalk.conf</code> and fill it with this (also from <cite>Hacking Elastic Beanstalk</cite><sup>[3]</sup>:</p>
<pre><code>server {
    listen 80;
    server_name _;
    access_log /var/log/httpd/elasticbeanstalk-access_log;
    error_log /var/log/httpd/elasticbeanstalk-error_log;

    #set the default location
    location / {
        proxy_pass         http://127.0.0.1:8080/;
    }

    # make sure the hostmanager works
    location /_hostmanager/ {
        proxy_pass         http://127.0.0.1:8999/;
    }
}</code></pre>
<p>These settings mimic the settings from Apache write down to the location of the logs. This ensures that nginx is working almost exactly like Apache (for our purposes on WellFurnished, we put in a few more modifications, but this is what you need to get started).</p>
<p>Now that nginx is set up, you can safely remove Apache and start nginx:</p>
<pre><code>sudo yum remove httpd
sudo service nginx start</code></pre>
<p>As one final measure, make sure that nginx will start automatically when the server starts:</p>
<pre><code>sudo /sbin/chkconfig nginx on</code></pre>
<p>With that, nginx is ready to go as an Apache replacement.</p>
<h2>Modifying hostmanager</h2>
<p>The tricky part of the configuration is that hostmanager actually interacts with Apache. Since you just removed Apache, that’s going to make hostmanager quite confused and angry. Thankfully, <cite>Hacking Elastic Beanstalk</cite><sup>[3]</sup> has a bash script that modifies hostmanager to deal with nginx instead of Apache (this must be run as sudo):</p>
<pre><code>cd /opt/elasticbeanstalk/srv/hostmanager/lib/elasticbeanstalk/hostmanager
cp utils/apacheutil.rb utils/nginxutil.rb
sed -i 's/Apache/Nginx/g' utils/nginxutil.rb
sed -i 's/apache/nginx/g' utils/nginxutil.rb
sed -i 's/httpd/nginx/g' utils/nginxutil.rb
cp init-tomcat.rb init-tomcat.rb.orig
sed -i 's/Apache/Nginx/g' init-tomcat.rb
sed -i 's/apache/nginx/g' init-tomcat.rb</code></pre>
<p>The script creates a utility to deal with nginx and then modifies <code>init-tomcat.rb</code>, The code that starts up Tomcat, so that it also keeps track of nginx.</p>
<h2>Creating a custom AMI</h2>
<p>Now that the instances set up with appropriate modifications it’s time to create a custom AMI. Go back to the EC2 instances section of the AWS console and find your modified instance. Right click on it and select “Create Image (EBS AMI)”. It will ask you for an image name an optional description, just make sure you put something in that make sense to you. You can make any other modifications that you want (or just use the defaults) and then click “Yes, Create”. When your AMI is ready it will be listed in the AMIs list.</p>
<p><strong>Important:</strong> You might be wondering why you can’t just go into an instance that Elastic Beanstalk started, modify that, and create a custom AMI directly from there. That would seem to be the logical thing to do because it requires less steps. However, Elastic Beanstalk write some configuration information to the instance when it starts up. If you create an AMI from that instance that it will always have that configuration and that will actually prevents hostmanager from starting properly (trust me, I tried). </p>
<h2>Using the custom AMI</h2>
<p>Go to the AMI list in the EC2 section of the AWS console and find your new AMI. Copy the AMI ID (not the name). In your Elastic Beanstalk environment, click on the Actions menu and select “Edit/Load Configuration”. Paste your custom AMI ID into the “Custom AMI” field and click “Apply Changes”. Elastic Beanstalk will then start deploying instances using the new AMI. After a couple of minutes, you should have a fully functioning environment with your new AMI.</p>
<h2>Conclusion</h2>
<p>Creating a custom AMI for Elastic Beanstalk is a little bit tricky and takes a lot of patience. I hope that someday Amazon will add nginx default configurations so that custom AMIs will no longer be needed. One thing to be aware of when using a custom AMI is that the instances won’t receive automatic updates, so you’ll need to keep on top of security fixes and other critical updates. Other than that, as long as hostmanager is running and accessible, you should be able to make any changes that you want.</p>
<p>I created a bash script with all of the steps mentioned in this post as a <a href="https://gist.github.com/3718880">gist</a> that you can copy from. Note that the script must be run as sudo.</p>
<h2>References</h2>
<ol>
<li><a href="http://aws.amazon.com/elasticbeanstalk/">Elastic Beanstalk</a> (Amazon)</li>
<li><a href="http://playframework.org">Play Framework</a> (Play)</li>
<li><a href="http://my.safaribooksonline.com/book/programming/java/9781449309558/hacking-elastic-beanstalk/hackingelectric#X2ludGVybmFsX0ZsYXNoUmVhZGVyP3htbGlkPTk3ODE0NDkzMDk1NTgvSV9zZWN0MTRfZDFlMjAyNQ==">Hacking Elastic Beanstalk</a> (Safari Online)</li>
</ol>
<div>
<a href="http://feeds.feedburner.com/~ff/nczonline?a=l4kAv-8I6fY:_MlcuqGjJMU:yIl2AUoC8zA"><img src="http://feeds.feedburner.com/~ff/nczonline?d=yIl2AUoC8zA" border="0"></a> <a href="http://feeds.feedburner.com/~ff/nczonline?a=l4kAv-8I6fY:_MlcuqGjJMU:V_sGLiPBpWU"><img src="http://feeds.feedburner.com/~ff/nczonline?i=l4kAv-8I6fY:_MlcuqGjJMU:V_sGLiPBpWU" border="0"></a> <a href="http://feeds.feedburner.com/~ff/nczonline?a=l4kAv-8I6fY:_MlcuqGjJMU:qj6IDK7rITs"><img src="http://feeds.feedburner.com/~ff/nczonline?d=qj6IDK7rITs" border="0"></a> <a href="http://feeds.feedburner.com/~ff/nczonline?a=l4kAv-8I6fY:_MlcuqGjJMU:F7zBnMyn0Lo"><img src="http://feeds.feedburner.com/~ff/nczonline?i=l4kAv-8I6fY:_MlcuqGjJMU:F7zBnMyn0Lo" border="0"></a>
</div><img src="http://feeds.feedburner.com/~r/nczonline/~4/l4kAv-8I6fY" height="1" width="1">