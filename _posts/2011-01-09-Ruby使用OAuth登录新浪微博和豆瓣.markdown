---
layout: post
title:  "Ruby使用OAuth登录新浪微博和豆瓣"
date:   2011-01-09 12:49:03
author: 
categories: program
---

## Ruby使用OAuth登录新浪微博和豆瓣
### by 
### at 2011-01-09 12:49:03
### original <http://www.javaeye.com/topic/866280>

<span style="font-size:medium">首先需要安装oauth这个gem包</span>
<br><pre name="code">
gem install oauth
</pre>
<br>
<br><span style="font-size:medium">新浪微博OAuth</span>
<br>申请新浪微博API key: <a href="http://open.t.sina.com.cn/wiki/index.php/%E6%96%B0%E6%89%8B%E6%8C%87%E5%8D%97">http://open.t.sina.com.cn/wiki/index.php/%E6%96%B0%E6%89%8B%E6%8C%87%E5%8D%97</a>
<br><pre name="code">
require &#39;rubygems&#39;
require &#39;oauth&#39;
# your api key here
sina_api_key = &quot;&quot;
# your api key secret here
sina_api_key_secret = &quot;&quot;

@consumer = OAuth::Consumer.new(
  sina_api_key,
  sina_api_key_secret,
  {
    :site=&gt;&quot;http://api.t.sina.com.cn&quot;,
  }
)
# 1. get request_token
@request_token = @consumer.get_request_token

# 2. authorize &amp; get oauth_verifier
puts &quot;Copy this url to your browser to authorize,  and get the oauth verifier code:&quot;
puts @request_token.authorize_url
@oauth_verifier = &quot;&quot; # put the verfifier code here

# 3. get access_token
@access_token = @request_token.get_access_token(:oauth_verifier =&gt; @oauth_verifier)

# 4. API Example: get current user info
puts @access_token.get &quot;/account/verify_credentials.xml&quot;

# API result:
&lt;?xml version=&quot;1.0&quot; encoding=&quot;UTF-8&quot;?&gt;
  &lt;user&gt;
    &lt;id&gt;1835404525&lt;/id&gt;
    &lt;screen_name&gt;wendait&lt;/screen_name&gt;
    &lt;name&gt;wendait&lt;/name&gt;
    &lt;province&gt;11&lt;/province&gt;
    &lt;city&gt;1000&lt;/city&gt;
    &lt;location&gt;北京&lt;/location&gt;
    &lt;description&gt;wenda.it - 做国内最好的IT专业知识问答网站&lt;/description&gt;
    &lt;url&gt;http://1&lt;/url&gt;
    &lt;profile_image_url&gt;http://tp2.sinaimg.cn/1835404525/50/1293540256/1&lt;/profile_image_url&gt;
    &lt;domain&gt;plzdonttalkwithme&lt;/domain&gt;
    &lt;gender&gt;m&lt;/gender&gt;
    &lt;followers_count&gt;17&lt;/followers_count&gt;
    &lt;friends_count&gt;103&lt;/friends_count&gt;
    &lt;statuses_count&gt;307&lt;/statuses_count&gt;
    &lt;favourites_count&gt;0&lt;/favourites_count&gt;
    &lt;created_at&gt;Thu Oct 21 00:00:00 +0800 2010&lt;/created_at&gt;
    &lt;following&gt;false&lt;/following&gt;
    &lt;verified&gt;false&lt;/verified&gt;
    &lt;allow_all_act_msg&gt;false&lt;/allow_all_act_msg&gt;
    &lt;geo_enabled&gt;true&lt;/geo_enabled&gt;
    &lt;status&gt;
      &lt;created_at&gt;Sun Jan 09 10:38:41 +0800 2011&lt;/created_at&gt;
      &lt;id&gt;5087310493&lt;/id&gt;
      &lt;text&gt;分享图片&lt;/text&gt;
      &lt;source&gt;
        &lt;a href=&quot;http://t.sina.com.cn/mobile/android.php&quot;&gt;Android客户端&lt;/a&gt;
      &lt;/source&gt;
      &lt;favorited&gt;false&lt;/favorited&gt;
      &lt;truncated&gt;false&lt;/truncated&gt;
      &lt;geo/&gt;
      &lt;in_reply_to_status_id&gt;&lt;/in_reply_to_status_id&gt;
      &lt;in_reply_to_user_id&gt;&lt;/in_reply_to_user_id&gt;
      &lt;in_reply_to_screen_name&gt;&lt;/in_reply_to_screen_name&gt;
      &lt;thumbnail_pic&gt;http://ww1.sinaimg.cn/thumbnail/6d660cedjw6dd604n2phwj.jpg&lt;/thumbnail_pic&gt;
      &lt;bmiddle_pic&gt;http://ww1.sinaimg.cn/bmiddle/6d660cedjw6dd604n2phwj.jpg&lt;/bmiddle_pic&gt;
      &lt;original_pic&gt;http://ww1.sinaimg.cn/large/6d660cedjw6dd604n2phwj.jpg&lt;/original_pic&gt;
      &lt;annotations/&gt;
    &lt;/status&gt;
  &lt;/user&gt;
</pre>
<br>
<br><span style="font-size:medium">豆瓣OAuth</span>
<br>申请豆瓣API key: <a href="http://www.douban.com/service/apikey/apply">http://www.douban.com/service/apikey/apply</a>
<br><pre name="code">
require &#39;rubygems&#39;
require &#39;oauth&#39;
# your api key here
douban_api_key = &quot;&quot;
# your api key secret here
douban_api_key_secret = &quot;&quot;

@consumer = OAuth::Consumer.new(
  douban_api_key,
  douban_api_key_secret,
  {
    :site=&gt;&quot;http://www.douban.com&quot;,
    :request_token_path=&gt;&quot;/service/auth/request_token&quot;,
    :access_token_path=&gt;&quot;/service/auth/access_token&quot;,
    :authorize_path=&gt;&quot;/service/auth/authorize&quot;,
    :signature_method=&gt;&quot;HMAC-SHA1&quot;,
    :scheme=&gt;:header,
    :realm=&gt;&quot;http://yoursite.com&quot;
  }
)

# 1. get request_token
@request_token = @consumer.get_request_token

# 2. authorize
puts &quot;Copy this url to your browser to authorize:&quot;
puts @request_token.authorize_url

# 3. get access_token
@access_token = @request_token.get_access_token
@access_token = OAuth::AccessToken.new(
  OAuth::Consumer.new(
    douban_api_key,  
    douban_api_key_secret, 
    {
      :site=&gt;&quot;http://api.douban.com&quot;,
      :scheme=&gt;:header,
      :signature_method=&gt;&quot;HMAC-SHA1&quot;,
      :realm=&gt;&quot;http://yoursite.com&quot;
    }
  ),
  @access_token.token,
  @access_token.secret
)

# 4. API example: get current user info
puts @access_token.get(&quot;/people/%40me&quot;)

# API result:
&lt;?xml version=&quot;1.0&quot; encoding=&quot;UTF-8&quot;?&gt;
&lt;entry xmlns=&quot;http://www.w3.org/2005/Atom&quot; xmlns:db=&quot;http://www.douban.com/xmlns/&quot; xmlns:gd=&quot;http://schemas.google.com/g/2005&quot; xmlns:openSearch=&quot;http://a9.com/-/spec/opensearchrss/1.0/&quot; xmlns:opensearch=&quot;http://a9.com/-/spec/opensearchrss/1.0/&quot;&gt;
	&lt;id&gt;http://api.douban.com/people/1398276&lt;/id&gt;
	&lt;title&gt;hideto&lt;/title&gt;
	&lt;link href=&quot;http://api.douban.com/people/1398276&quot; rel=&quot;self&quot;/&gt;
	&lt;link href=&quot;http://www.douban.com/people/Hideto/&quot; rel=&quot;alternate&quot;/&gt;
	&lt;link href=&quot;http://img3.douban.com/icon/u1398276-1.jpg&quot; rel=&quot;icon&quot;/&gt;
	&lt;link href=&quot;http://hideto.javaeye.com&quot; rel=&quot;homepage&quot;/&gt;
	&lt;content&gt;http://wenda.it&lt;/content&gt;
	&lt;db:attribute name=&quot;n_mails&quot;&gt;0&lt;/db:attribute&gt;
	&lt;db:attribute name=&quot;n_notifications&quot;&gt;1&lt;/db:attribute&gt;
	&lt;db:signature&gt;&lt;/db:signature&gt;
	&lt;db:uid&gt;Hideto&lt;/db:uid&gt;
	&lt;uri&gt;http://api.douban.com/people/1398276&lt;/uri&gt;
&lt;/entry&gt;
</pre>
          
          <br><br>
          作者: <a href="http://hideto.javaeye.com">hideto</a> 
          <br>
          声明: 本文系JavaEye网站发布的原创文章，未经作者书面许可，严禁任何网站转载本文，否则必将追究法律责任！
          <br><br>
          <span style="color:red">
            <a href="http://www.javaeye.com/topic/866280" style="color:red">已有 <strong>0</strong> 人发表回复，猛击-&gt;&gt;<strong>这里</strong>&lt;&lt;-参与讨论</a>
          </span>
          <br><br><br>
<span style="color:#e28822">JavaEye推荐</span>
<br>
<ul><li><a href="http://www.iteye.com/clicks/433"><span style="color:red;font-weight:bold">—软件人才免语言低担保 赴美带薪读研！— </span></a></li></ul>
<br><br><br>