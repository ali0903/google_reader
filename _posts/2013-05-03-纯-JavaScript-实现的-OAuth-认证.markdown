---
layout: post
title:  "纯 JavaScript 实现的 OAuth 认证"
date:   2013-05-03 21:05:49
author: 童海波
categories: program
---

## 纯 JavaScript 实现的 OAuth 认证
### by 童海波
### at 2013-05-03 21:05:49
### original <http://blog.jobbole.com/39166/?utm_source=rss&utm_medium=rss&utm_campaign=%25e7%25ba%25af-javascript-%25e5%25ae%259e%25e7%258e%25b0%25e7%259a%2584-oauth-%25e8%25ae%25a4%25e8%25af%2581>

<p>英文原文：<a href="http://www.codeproject.com/Articles/579564/JavaScript-oAuth">JavaScript oAuth</a> 编译：<a href="http://www.oschina.net/translate/javascript-oauth">oschina</a></p>
<p><strong>引言</strong></p>
<p>现在，很多的应用程序都在使用HTML和JavaScript, 这是一个非常明智的选择,让你跟上目前的趋势. 一些主要实体工具因为客户端验证和授权等原因提供了API. 当前网站对于验证的一个广受欢迎的功能是”单点登录”. 这让用户可以通过其它一些社交媒体网站上的身份认证直接登录你的网站. 这篇文章介绍用纯JavaScript通过一个简单的方法让你从三个身份认证提供者上登录到你的网站.</p>
<p><strong>背景</strong></p>
<p>为了使用身份认证提供者的API, 你要在他们的网站上创建一个应用，完成后它将提供一个API key让你使用.</p>
<ul>
<li>谷哥 API: <a href="https://code.google.com/apis/console/" rel="nofollow">https://code.google.com/apis/console/</a></li>
<li>Facebook: <a href="https://developers.facebook.com/apps" rel="nofollow">https://developers.facebook.com/apps</a></li>
<li>Twitter: <a href="https://dev.twitter.com/apps" rel="nofollow">https://dev.twitter.com/apps</a></li>
</ul>
<p><strong>使用这些代码</strong></p>
<p>如果你不打算长篇阅读，你可以去直接下载源代码文件然后随便你怎么使用/修改。</p>
<p>首先，我们包含一些文件在我们的HTML页面的头部。</p>
<pre>&lt;!--Register Jquery--&gt;
&lt;script src=&quot;http://code.jquery.com/jquery-1.9.1.js&quot;&gt;&lt;/script&gt;
&lt;script src=&quot;http://code.jquery.com/jquery-migrate-1.1.0.js&quot;&gt;&lt;/script&gt;
&lt;!--Register Google Api--&gt;
&lt;script src=&quot;https://apis.google.com/js/client.js?onload=handleClientLoad&quot;&gt;&lt;/script&gt;</pre>
<p>最近的时间，Twitter已经废弃使用可能会导致开发者变得畏畏缩缩的单点客户端认证。然而，感谢YUI（由Yahoo！提供的API）我们能够使用Javascript这样去模拟一个服务端请求到Twitter。所以我们也可以把YUI的库包含在头部。</p>
<pre>&lt;!--Register YUI Api--&gt;
&lt;script type=&quot;text/javascript&quot; 
  src=&quot;http://yui.yahooapis.com/3.3.0/build/yui/yui-min.js&quot;&gt;&lt;/script&gt;</pre>
<p>我们现在创建DOM元素来触发请求。这些元素可以是你的任意选择。</p>
<pre>&lt;div id=&quot;fbLogin&quot; onclick=&quot;&quot;&gt;Facebook&lt;/div&gt;
&lt;div id=&quot;twitterlogin&quot;&gt;Twitter&lt;/div&gt;
&lt;div id=&quot;googlelogin&quot;&gt;Google&lt;/div&gt;</pre>
<p>现在我们看下脚本。我们从Facebook的登陆开始。</p>
<p><strong>Facebook</strong></p>
<p>我们要创建的第一个函数用来向 Facebook oAuth API 发送初始化请，函数将发送客户端 ID（由你的Facebook 应用给出），重定向 URL （必须同你注册应用时填写的一样），还有请求类型（此处是访问令牌）。访问令牌是用户验证所必须的，并也会用来访问 Facebook 的图形 API，以及获取用户个人信息。</p>
<pre>var appID = &lt;YOUR CLIENT ID&gt;;
 
function fbLogin() {
    var path = &#39;https://www.facebook.com/dialog/oauth?&#39;;
    var queryParams = [&#39;client_id=&#39; + appID,
    &#39;redirect_uri=&#39; + window.location,
    &#39;response_type=token&#39;];
    var query = queryParams.join(&#39;&amp;&#39;);
    var url = path + query;
    window.location.replace(url);
}</pre>
<p>相应将是一个附加形如 “#access_token=…….” 的哈希字符串的访问令牌的到你网站的重定向。要使用访问令牌，我们现在需要一个检查 URL 中访问令牌的函数。同时此函数也将对 Facebook 图形 API 发送一个带有访问令牌和一个以参数形势接受用户信息的回调函数的请求。</p>
<pre>function checkFbHashLogin() {
 
    if (window.location.hash.length &gt; 3) {
        var hash = window.location.hash.substring(1);
        if(hash.split(&#39;=&#39;)[0] == &#39;access_token&#39;)
        {
            var path = &quot;https://graph.facebook.com/me?&quot;;
            var queryParams = [hash, &#39;callback=displayUser&#39;];
            var query = queryParams.join(&#39;&amp;&#39;);
            var url = path + query;
 
            //use jsonp to call the graph
            var script = document.createElement(&#39;script&#39;);
            script.src = url;
            document.body.appendChild(script);
        }
       
    }
}</pre>
<p>第三步，我们的回调函数会将用户信息转化为我们可见的形势。</p>
<pre>function displayUser(user) {
    setTimeout(function () { }, 1000);
    if (user.id != null &amp;&amp; user.id != &quot;undefined&quot;) {
       //Do Stuff
       //You have access to user id, name, username, gender etc.
       //For more info visit https://developers.facebook.com/docs/
       //                      reference/login/public-profile-and-friend-list   
    }
    else {
        alert(&#39;user error&#39;);
    }
}</pre>
<p>最后，我们需要一个函数调用的触发器，且我们也需要一个在页面加载的时候检查哈希字符串的函数。</p>
<pre>$(function () {
 
    checkFbHashLogin();
 
    $(&#39;#fbLogin&#39;).click(function () {
        fbLogin();
    });
})</pre>
<p>现在我们已完成了Facebook的身份验证。</p>
<p><strong>Google</strong></p>
<p>首先，我将给出一个带有解释运作的注释的函数。如前所述，我们需要谷歌的客户端ID，我们的应用必须使用 API 密钥。</p>
<pre>var clientId = &lt;YOUR CLIENT ID&gt;;
var apiKey = &lt;YOUR API KEY&gt;;
var scopes = &#39;https://www.googleapis.com/auth/plus.me&#39;;  
 
 
// Our first function is used to set the api key and
// is run once the google api is loaded in the page header. 
function handleClientLoad() {
    gapi.client.setApiKey(apiKey);
}
 
//Gets the result after the authorization and if successful,
//it makes the api call to get the  //user&#39;s information.
function handleAuthResult(authResult) {
 
    if (authResult &amp;&amp; !authResult.error) {
        makeApiCall();
    } 
}
  
//Make api call on button click to authorize client
function handleAuthClick(event) { gapi.auth.authorize({ client_id: clientId, 
            scope: scopes, immediate: false }, handleAuthResult);
 
    return false;
}
 
// Load the API and make an API call.  Display the results on the screen.
function makeApiCall() {
    gapi.client.load(&#39;plus&#39;, &#39;v1&#39;, function () {
        var request = gapi.client.plus.people.get({
            &#39;userId&#39;: &#39;me&#39;
        });
 
        request.execute(function (resp) {
        //Do Stuff
         //You have access to user id, name, display name, gender, emails, etc.
        //For more info visit https://developers.google.com/+/api/latest/people#resource 

        });
    });
}
 
$(function () {
    var authorizeButton = document.getElementById(&#39;googlelogin&#39;);
    authorizeButton.onclick = handleAuthClick;
})</pre>
<p>这样就完成了谷歌的认证。</p>
<p><strong>Twitter</strong></p>
<p>正如之前所说，Twitter API 并不允许纯粹的客户端验证。因此需要使用 YUI 库。此库使用雅虎查询语言(YQL)制定对Twitter API 的服务器端请求。</p>
<p>例如，应用中可能使用的一条 YQL 如下：</p>
<pre>select * from twitter.oauth.requesttoken where oauth_callback=&quot;&lt;YOUR WEBSITE URL&gt;&quot;;</pre>
<p>你可以看到，select 语句同其它查询语言的 select 语句一样。select 语句的条件参数（oauth_callback）是你 Twitter 应用所注册的 URL。由于我们选择 URL 匹配，我们并不需要客户端ID。由于我们使用 JavaScript，我们可以使用 window.location 获取当前 URL 用于查询（在代码中已处理）。本质上，我们无需关注 YUI 和 YQL。因此，让我们看看 YUI 库的实现，以及它是如何调用 Twitter API 的。</p>
<p>首先是初始化 YUI 对象。使用此对象，我们将设定所用的模块以及它们的地址。在这里将使用 Twitter 和 YQL 模块。</p>
<pre>YUI({
    combine: false,
    filter: &quot;raw&quot;,
    debug: false,
    modules: {
        
        &#39;Twitter&#39;: {
           fullpath: &#39;js/twitter.js&#39;
        },
        &#39;myYQL&#39;: {
            fullpath: &#39;js/yql.js&#39;,
            requires: [&#39;jsonp&#39;, &#39;jsonp-url&#39;]
        }
    }
})</pre>
<p>下一步，我们将告知 YUI库我们将使用资源库中的哪个组件，并提供回调函数。我们通过调用 YUI 的 “use” 函数完成此操作，只需添加到初始化后的 YUI 对象之后。</p>
<pre>YUI({
    ...
}).use(&#39;Twitter&#39;, &#39;gallery-storage-lite&#39;, &#39;myYQL&#39;, &#39;node&#39;, &quot;event&quot;, function (Y) {});</pre>
<p>回调函数将在 YUI 库初始化完成并选定组件之后调用，我们将所有处理 Twitter API 用户验证的代码放置其中。首先要做的是获取触发Twitter 用户验证的元素，并将点击事件绑定的到一个函数上。这个函数将进行第一次 API 调用，获取 oAuth 请求令牌和密钥。这些将在一个查询字符串中传递到 Twitter 用户验证 API，oAuth 令牌验证将以同样的方式返回</p>
<pre>var twtBtn = Y.one(&#39;#twitterlogin&#39;);
 
twtBtn.on(&#39;click&#39;, function (e) {
    Y.Twitter.call({ type: &quot;request_token&quot; }, function (tokens) {
        Y.log(&quot;step 1&quot;);
        Y.log(tokens);
        Y.StorageLite.setItem(&#39;oauth_token&#39;, tokens.oauth_token);
        Y.StorageLite.setItem(&#39;oauth_token_secret&#39;, tokens.oauth_token_secret);
        window.setTimeout(function () {
            window.location = &quot;https://twitter.com/oauth/authenticate?oauth_token=&quot; + 
              tokens.oauth_token + &quot;&amp;oauth_token_secret=&quot; + tokens.oauth_token_secret;
        }, 10);
    });
});</pre>
<p>下面几步将结合嵌套的调用和响应，知道我们获得最终的响应。首先我们将检查查询字符串以获取oAuth 令牌验证。接着令牌和验证将被传递给 YUI Twitter调用以获取访问令牌。最后，访问令牌将被发送到一个调用，以索取用户信息。</p>
<pre>if (getQueryStringParameter(&#39;oauth_token&#39;)) {
 
    Y.StorageLite.setItem(&#39;oauth_token&#39;, getQueryStringParameter(&#39;oauth_token&#39;));
    Y.StorageLite.setItem(&#39;oauth_verifier&#39;, getQueryStringParameter(&#39;oauth_verifier&#39;));

    Y.Twitter.config({
        oauth_token: getQueryStringParameter(&#39;oauth_token&#39;),
        oauth_token_secret: getQueryStringParameter(&#39;oauth_token_secret&#39;)
    });

    Y.Twitter.call({ type: &quot;access_token&quot; }, function (tokens) {
        Y.Twitter.config({
            oauth_token: tokens.oauth_token,
            oauth_token_secret: tokens.oauth_token_secret
        });

        Y.Twitter.call({ type: &quot;credentials&quot; }, function (user) {
            Y.Twitter.config({
                screen_name: user.screen_name,
                user_id: user.id
            });

            //Do Stuff
        //You have access to user id, name, screenname, description, etc.
        //For more info visit https://dev.twitter.com/docs/api/1.1/get/account/verify_credentials 
       });
    });
}</pre>
<p>这将完成 Twitter 认证组件的所有请求。</p>
<pre>//Supporting function to parse the query string 
function getQueryStringParameter(key, queryString) {
    
    var queryString = queryString || window.location.href;
    key = key.replace(/[\[]/, &quot;\\\[&quot;).replace(/[\]]/, &quot;\\\]&quot;);
    var regex = new RegExp(&quot;[\\?&amp;]&quot; + key + &quot;=([^&amp;#]*)&quot;);
    var qs = regex.exec(queryString);
    if (qs)
        return qs[1];
    else
        return false;
}</pre>
<div></div>
<p><strong>两点</strong></p>
<p>现今，单点登录的使用率正迅速增长，很多大型网站，如雅虎和Hotmail都允许用户使用单点登录进行验证。例如，在配置Google API时，你可以将其它供应商同你的 Google API 密钥绑定。值得注意的是，所有请求都将使用各个验证提供者应用所登记域名，同时你将需要使用正确的域名和URL网址。因此，应用在你的 Localhost 将无法正常工作。</p>
<h4>相关文章</h4>
<ul>
<li><a href="http://blog.jobbole.com/23875/"><img src="http://blog.jobbole.com/wp-content/uploads/2013/02/icon_smile.gif" alt="李秉骏：在Phonegap下实现oAuth认证"></a><a href="http://blog.jobbole.com/23875/">李秉骏：在Phonegap下实现oAuth认证</a></li>
<li><a href="http://blog.jobbole.com/36084/"><img src="http://blog.jobbole.com/wp-content/uploads/2011/06/javascript-logo.png" alt="处理 JavaScript 异常的一个想法"></a><a href="http://blog.jobbole.com/36084/">处理 JavaScript 异常的一个想法</a></li>
<li><a href="http://blog.jobbole.com/8861/"><img src="http://blog.jobbole.com/wp-content/uploads/2011/06/javascript-logo.png" alt="高效的JavaScript单元测试 "></a><a href="http://blog.jobbole.com/8861/">高效的JavaScript单元测试 </a></li>
<li><a href="http://blog.jobbole.com/36327/"><img src="http://blog.jobbole.com/wp-content/uploads/2011/06/javascript-logo.png" alt="我眼中的技术高手"></a><a href="http://blog.jobbole.com/36327/">我眼中的技术高手</a></li>
<li><a href="http://blog.jobbole.com/12042/"><img src="http://blog.jobbole.com/wp-content/uploads/2011/06/javascript-logo.png" alt="JavaScript需要Blocks"></a><a href="http://blog.jobbole.com/12042/">JavaScript需要Blocks</a></li>
<li><a href="http://blog.jobbole.com/18987/"><img src="http://blog.jobbole.com/wp-content/uploads/2011/06/javascript-logo.png" alt="JavaScript面向对象15分钟教程"></a><a href="http://blog.jobbole.com/18987/">JavaScript面向对象15分钟教程</a></li>
<li><a href="http://blog.jobbole.com/8481/"><img src="http://blog.jobbole.com/wp-content/uploads/2011/06/javascript-logo.png" alt="JavaScript初学者应注意的七个细节"></a><a href="http://blog.jobbole.com/8481/">JavaScript初学者应注意的七个细节</a></li>
<li><a href="http://blog.jobbole.com/18513/"><img width="150" height="150" src="http://blog.jobbole.com/wp-content/uploads/2012/04/snippets-150x150.jpg" alt="50个jQuery代码段帮你成为更出色的JS开发者"></a><a href="http://blog.jobbole.com/18513/">50个jQuery代码段帮你成为更出色的JS开发者</a></li>
<li><a href="http://blog.jobbole.com/19203/"><img src="http://blog.jobbole.com/wp-content/uploads/2011/06/javascript-logo.png" alt="网易邮箱前端Javascript编码规范：类规范"></a><a href="http://blog.jobbole.com/19203/">网易邮箱前端Javascript编码规范：类规范</a></li>
<li><a href="http://blog.jobbole.com/1313/"><img src="http://blog.jobbole.com/wp-content/plugins/wordpress-23-related-posts-plugin/static/thumbs/16.jpg" alt="14个Google地图的JavaScript资源"></a><a href="http://blog.jobbole.com/1313/">14个Google地图的JavaScript资源</a></li>
</ul>
<p><a href="http://blog.jobbole.com/39166/">纯 JavaScript 实现的 OAuth 认证</a>，首发于<a href="http://blog.jobbole.com">博客 - 伯乐在线</a>。</p>