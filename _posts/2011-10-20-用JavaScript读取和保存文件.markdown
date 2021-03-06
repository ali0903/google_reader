---
layout: post
title:  "用JavaScript读取和保存文件"
date:   2011-10-20 05:02:04
author: keakon
categories: program
---

## 用JavaScript读取和保存文件
### by keakon
### at 2011-10-20 05:02:04
### original <http://www.keakon.net/2011/10/20/%E7%94%A8JavaScript%E8%AF%BB%E5%8F%96%E5%92%8C%E4%BF%9D%E5%AD%98%E6%96%87%E4%BB%B6>

话说今天只是粗略浏览了一下<a href="https://code.google.com/p/switchysharp/">Proxy SwitchySharp的源码</a>，就收获了不少东西，其中就包括本文要介绍的读取和保存文件。<br>因为Google还不提供同步插件数据的功能，所以导入和导出插件配置就必须和文件打交道了。而出于安全原因，只有IE才提供访问文件的API；但随着HTML 5的到来，其他浏览器也纷纷支持了。<br><br>首先说读取文件。W3C提供了一些<a href="http://www.w3.org/TR/FileAPI/">File API</a>，其中最重要的是<a href="https://developer.mozilla.org/en/DOM/FileReader">FileReader</a>这个类。<br><br>先列出需要用到的HTML标签：<br><pre><code>&lt;input type=&quot;file&quot; id=&quot;file&quot; onchange=&quot;handleFiles(this.files)&quot;/&gt;</code></pre>当选择了一个文件时，就会把包含这个文件的列表（一个FileList对象）作为参数传给handleFiles()函数了。<br>这个FileList对象类似一个数组，可以知道文件的数目，而它的元素就是File对象了。<br>从这个File对象可以获取name、size、lastModifiedDate和type等属性。<br>把这个File对象传给FileReader对象的读取方法，就能读取文件了。<br><br>FileReader共有4种读取方法：<br><ul><li>readAsArrayBuffer(file)：将文件读取为<a href="https://developer.mozilla.org/en/JavaScript_typed_arrays/ArrayBuffer">ArrayBuffer</a>。<br></li><li>readAsBinaryString(file)：将文件读取为二进制字符串<br></li><li>readAsDataURL(file)：将文件读取为<a href="http://www.keakon.net/2010/02/02/%E5%8E%9F%E6%9D%A5%E6%B5%8F%E8%A7%88%E5%99%A8%E8%BF%98%E6%94%AF%E6%8C%81dataURI%E5%8D%8F%E8%AE%AE">Data URL</a><br></li><li>readAsText(file, [encoding])：将文件读取为文本，encoding缺省值为'UTF-8'<br></li></ul>此外，abort()方法可以停止读取文件。<br><br>FileReader对象在读取文件后，还需要进行处理。为了不阻塞当前线程，API采用了事件模型，可以注册这些事件：<br><ul><li>onabort：中断时触发<br></li><li>onerror：出错时触发<br></li><li>onload：文件成功读取完毕时触发<br></li><li>onloadend：文件读取完毕时触发，无论是否失败<br></li><li>onloadstart：文件开始读取时触发<br></li><li>onprogress：当文件读取时，周期性地触发<br></li></ul><br>有了这些方法以后，就可以处理文件了。<br>先来试试读取文本文件：<br><pre><code>function handleFiles(files) {
	if (files.length) {
		var file = files[0];
		var reader = new FileReader();
		if (/text\/\w+/.test(file.type)) {
			reader.onload = function() {
				$(&#39;&lt;pre&gt;&#39; + this.result + &#39;&lt;/pre&gt;&#39;).appendTo(&#39;body&#39;);
			}
			reader.readAsText(file);
		}
	}
}</code></pre>这里的this.result实际上就是reader.result，也就是读取出来的文件内容。<br>测试一下你会发现这个文件的内容被添加到网页中了。如果是用Chrome的话，必须把网页放在服务器上或插件里，file协议下会失败。<br><br>再来试试图片，因为浏览器可以直接显示Data URI协议的图片，所以这次就添加图片：<br><pre><code>function handleFiles(files) {
	if (files.length) {
		var file = files[0];
		var reader = new FileReader();
		if (/text\/\w+/.test(file.type)) {
			reader.onload = function() {
				$(&#39;&lt;pre&gt;&#39; + this.result + &#39;&lt;/pre&gt;&#39;).appendTo(&#39;body&#39;);
			}
			reader.readAsText(file);
		} else if(/image\/\w+/.test(file.type)) {
			reader.onload = function() {
				$(&#39;&lt;img src=&quot;&#39; + this.result + &#39;&quot;/&gt;&#39;).appendTo(&#39;body&#39;);
			}
			reader.readAsDataURL(file);
		}
	}
}</code></pre><br>其实input:file控件还支持选择多个文件：<br><pre><code>&lt;input type=&quot;file&quot; id=&quot;files&quot; multiple=&quot;&quot; onchange=&quot;handleFiles(this.files)&quot;/&gt;</code></pre>这样handleFiles()里就需要遍历处理files了。<br><br>如果只想读取部分数据的话，File对象还有webkitSlice()或mozSlice()方法，用于生成Blob对象。这个对象可以和File对象一样被FileReader读取。这2个方法接收3个参数：第1个参数是起始位置；第2个是结束位置，省略时则读到文件结尾；第3个是content type。<br>例子可以参考<a href="http://www.html5rocks.com/en/tutorials/file/dndfiles/">《Reading local files in JavaScript》</a>。<br><br>当然，除了导入数据和显示文件以外，它还可以用来做AJAX上传，代码可以参考<a href="https://developer.mozilla.org/en/Using_files_from_web_applications">《Using files from web applications》</a>。<br><br><br>接下来说保存文件。<br>实际上<a href="http://www.w3.org/TR/file-writer-api/">File API: Writer</a>提供了4个接口，但目前只有部分浏览器（Chrome 8+和Firefox 4+）实现了BlobBuilder，其余接口都不可用。<br>对于不支持的浏览器，可以使用<a href="https://github.com/eligrey/BlobBuilder.js">BlobBuilder.js</a>和<a href="https://github.com/eligrey/FileSaver.js">FileSaver.js</a>来获得支持。<br>我研究了一下，发现了其中的奥秘。<br><br>BlobBuilder可以创建一个Blob对象。把这个Blob对象传递给<a href="https://developer.mozilla.org/en/DOM/window.URL.createObjectURL">URL.createObjectURL()</a>方法，就可以拿到一个object URL。而这个object URL就是这个Blob对象的下载地址。<br>拿到下载地址后，创建一个a元素，将下载地址赋值给href属性，文件名赋值给download属性（Chrome 14+支持）。<br>然后再创建一个click事件，交给这个a元素处理，就会导致浏览器开始下载这个Blob对象了。<br>最后，用<a href="https://developer.mozilla.org/en/DOM/window.URL.revokeObjectURL">URL.revokeObjectURL()</a>来释放这个object URL，通知浏览器可以不必继续引用这个文件了。<br><br>下面就是一段化简的代码：<br><pre><code>var BlobBuilder = BlobBuilder || WebKitBlobBuilder || MozBlobBuilder;
var URL = URL || webkitURL || window;

function saveAs(blob, filename) {
	var type = blob.type;
	var force_saveable_type = &#39;application/octet-stream&#39;;
	if (type &amp;&amp; type != force_saveable_type) { // 强制下载，而非在浏览器中打开
		var slice = blob.slice || blob.webkitSlice || blob.mozSlice;
		blob = slice.call(blob, 0, blob.size, force_saveable_type);
	}

	var url = URL.createObjectURL(blob);
	var save_link = document.createElementNS(&#39;http://www.w3.org/1999/xhtml&#39;, &#39;a&#39;);
	save_link.href = url;
	save_link.download = filename;

	var event = document.createEvent(&#39;MouseEvents&#39;);
	event.initMouseEvent(&#39;click&#39;, true, false, window, 0, 0, 0, 0, 0, false, false, false, false, 0, null);
	save_link.dispatchEvent(event);
	URL.revokeObjectURL(url);
}

var bb = new BlobBuilder;
bb.append(&#39;Hello, world!&#39;);
saveAs(bb.getBlob(&#39;text/plain;charset=utf-8&#39;), &#39;hello world.txt&#39;);</code></pre>测试时会提示保存一个文本文件。Chrome需要把网页放在服务器上或插件里。