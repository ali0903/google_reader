---
layout: post
title:  "兼容各浏览器的event以及srcElement,fromElement,toElement属性"
date:   2010-07-06 23:23:18
author: aslan
categories: program
---

## 兼容各浏览器的event以及srcElement,fromElement,toElement属性
### by aslan
### at 2010-07-06 23:23:18
### original <http://item.feedsky.com/~feedsky/popo4j/~8243992/393778786/6347082/1/item.html>

<p>
	在 IE 中获取事件对象很容易，直接用 event,event.srcElement,event.fromElement,event.toElement 就行了。在 FireFox 中获得触发事件的元素可以用 event.target，但其他两个 fromElement 和 toElement ,属于IE的专属方法,如果要兼容IE的用法就要费些周折了</p>
<p>
	虽然IE经常我行我素,但无可否认,在这里,srcElement,fromElement,toElement.这三个方法很好用!</p>
<p>
	所以，为了保持一致的使用方式，也为了保持原有的使用习惯,我们来为非IE浏览器也加上这三个方法,我们通过继承prototype来实现这三个函数</p>
<pre>
if(window.addEventListener) { 
FixPrototypeForGecko();//标准浏览器就FIX
 }  

  function  FixPrototypeForGecko()  
  {  
      HTMLElement.prototype.__defineGetter__(&quot;runtimeStyle&quot;,element_prototype_get_runtimeStyle);  
      window.constructor.prototype.__defineGetter__(&quot;event&quot;,window_prototype_get_event);  
      Event.prototype.__defineGetter__(&quot;srcElement&quot;,event_prototype_get_srcElement);  
      Event.prototype.__defineGetter__(&quot;fromElement&quot;,  element_prototype_get_fromElement);  
      Event.prototype.__defineGetter__(&quot;toElement&quot;, element_prototype_get_toElement);
      
  }  

  function  element_prototype_get_runtimeStyle() { 
return  this.style;
 }  
  function  window_prototype_get_event() { 
return  SearchEvent();
 }  
  function  event_prototype_get_srcElement() {
 return  this.target;
 }  

  function element_prototype_get_fromElement() {  
      var node;  
      if(this.type == &quot;mouseover&quot;) node = this.relatedTarget;  
      else if (this.type == &quot;mouseout&quot;) node = this.target;  
      if(!node) return;  
      while (node.nodeType != 1) 
          node = node.parentNode;  
      return node;  
  }

  function  element_prototype_get_toElement() {  
          var node;  
          if(this.type == &quot;mouseout&quot;)  node = this.relatedTarget;  
          else if (this.type == &quot;mouseover&quot;) node = this.target;  
          if(!node) return;  
          while (node.nodeType != 1)  
             node = node.parentNode;  
          return node;  
  }
   
  function  SearchEvent()  
  {  
      if(document.all) return  window.event;  
       
      func = SearchEvent.caller;  

      while(func!=null){  
          var  arg0=func.arguments[0];  
          
          if(arg0 instanceof Event) {  
              return  arg0;  
          }  
         func=func.caller;  
      }  
      return   null;  
  }
</pre>
<p>
	好了，现在不管是在 IE 中还是在 FireFox 中，触发事件后都有 event、event.srcElement、event.fromElement、event.toElement 这些属性了</p>
<p>
	测试代码：</p>
<pre>
&lt;script&gt;
  function test(){
      alert(&quot;event:&quot; + event +&quot;, srcElement:&quot;+event.srcElement.innerHTML+
	    &quot;, fromElement:&quot;+event.fromElement.innerHTML + &quot;, toElement:&quot;+event.toElement.innerHTML)
  }
&lt;/script&gt;

&lt;button onmouseout=&quot;test()&quot;&gt;MouseOut&lt;/button&gt;&lt;button onmouseover=&quot;test()&quot;&gt;MouseOver&lt;/button&gt;</pre>
<p>
	OK，一切正常,在firefox和google浏览器中都可以使用event.srcElement、event.fromElement、event.toElement 这些属性了</p><img src="http://www1.feedsky.com/t1/393778786/popo4j/feedsky/s.gif?r=http://item.feedsky.com/~feedsky/popo4j/~8243992/393778786/6347082/1/item.html" border="0" height="0" width="0"><p><a href="http://www1.feedsky.com/r/l/feedsky/popo4j/393778786/art01.html"><img border="0" ismap src="http://www1.feedsky.com/r/i/feedsky/popo4j/393778786/art01.gif"></a></p><p><a href="http://feed.feedsky.com/~flare/popo4j?a=f1988c8e33f76c6db2c007da166a41cb"><img src="http://feed.feedsky.com/~flare/popo4j?i=f1988c8e33f76c6db2c007da166a41cb" border="0"></a><a href="http://feed.feedsky.com/~flare/popo4j?a=e54c2c8df7aae186c16cdc64169fedd5"><img src="http://feed.feedsky.com/~flare/popo4j?i=e54c2c8df7aae186c16cdc64169fedd5" border="0"></a><a href="http://feed.feedsky.com/~flare/popo4j?a=1b8e1c88aeb8c0deb9921d78fe8ab16b"><img src="http://feed.feedsky.com/~flare/popo4j?i=1b8e1c88aeb8c0deb9921d78fe8ab16b" border="0"></a></p>