---
layout: post
title:  "ASDF 筆記 –– Common Lisp 系統定義設施"
date:   2013-05-10 12:02:39
author: 
categories: program
---

## ASDF 筆記 –– Common Lisp 系統定義設施
### by 
### at 2013-05-10 12:02:39
### original <http://lisp.tw//2013/04/06/asdf-notes/>

<p><span>
作者：<a href="https://twitter.com/JuanitoFatas">Juanito Fatas</a>
<span>Lisp Taiwan</span>
</span></p>
<img src="http://lisp.tw/images/asdf.png" width="400" title="系統定義設施">

<a name="more"></a>

<h1>ASDF Another System Definition Facility</h1>
<blockquote>
<p>A tool for specifying how systems of Common Lisp software are comprised of components (sub-systems and files), and how to operate on these components in the right order so that they can be compiled, loaded, tested, etc. —— ASDF manual</p>
</blockquote>
<hr>

<blockquote>
<p>ASDF (Another System Definition Facility) is an extensible build facility for Common Lisp software. —— CLiki.net</p>
</blockquote>
<p>保持高度開發狀態的 ASDF，目前由在 Google Inc., 工作的 <a href="http://fare.tunes.org/">François-René Rideau</a> 維護（<a href="http://gclsg.lisp.tw/">Google Common Lisp 代碼風格指南</a>的作者）。</p>
<h2>當前版本 (Current Releases)</h2>
<ul>
<li>2010.05.31 ASDF2 released.</li>
<li>2013.01.31 ASDF3 released.</li>
<li>2013.02.01 ASDF3 (v2.28)</li>
<li>2013.02.16 ASDF3 (v2.29)</li>
</ul>
<p>幾乎所有主流 Common Lisp 實現都包含了 ASDF2。目前官方已經不再支援 ASDF1 與 ASDF2，所以升級到 ASDF3 吧！</p>
<p>這裡 ASDF2 與 ASDF3 並不是說 ASDF 的版本是 2.0 與 3.0。</p>
<p>ASDF3 實際上是版本 2.28 以上的 ASDF。</p>
<h2>ASDF 與 ASDF-INSTALL</h2>
<p>注意 ASDF 並不會幫你自動去下載你要用的函式庫。</p>
<blockquote>
<p>asdf-install used to be a program for downloading and installing lisp packages.</p>
</blockquote>
<p>ASDF-INSTALL <strong>不是</strong> ASDF 的一部分，並且是個 <strong>過時的</strong> 產物，沒有人在維護了，絕對不要再用了！</p>
<p>推薦使用目前處於積極開發狀態的 <a href="http://lisp.tw/quicklisp.org">Quicklisp</a> 來取代，更進階的用途推薦使用 <a href="http://www.common-lisp.net/project/clbuild/">CLBUILD</a>。</p>
<h2>加載 ASDF (Loading ASDF)</h2>
<p>Common Lisp 實現通常附有一份 ASDF2，很快的將會內建 ASDF3。</p>
<p>要在 Common Lisp 裡加載 ASDF，使用 <code>require</code> 函數:</p>
<table><tr><td><pre>1
</pre></td><td><pre><span>(<span>require</span><span> <span>"asdf"</span>)</span></span>
</pre></td></tr></table>

<p>還可使用 <code>(require &#39;asdf)</code>、<code>(require :asdf)</code>、<code>(require &quot;ASDF&quot; )</code> 來加載（除了 CLISP)， <strong>為了保持可移植性最大化，一致使用 <code>(require &quot;asdf&quot;)</code>。</strong></p>
<p>提供 ASDF 2 的實現:</p>
<ul>
<li><a href="http://lisp.tw/">ABCL</a></li>
<li><a href="http://lisp.tw/">ALLEGRO</a></li>
<li><a href="http://lisp.tw/">CCL</a></li>
<li><a href="http://lisp.tw/">CLISP</a></li>
<li><a href="http://lisp.tw/">CMUCL</a></li>
<li><a href="http://lisp.tw/">ECL</a></li>
<li><a href="http://lisp.tw/">LISPWORKS</a></li>
<li><a href="http://lisp.tw/">MKCL</a></li>
<li><a href="http://lisp.tw/">SBCL</a></li>
<li><a href="http://lisp.tw/">XCL</a></li>
</ul>
<p>即將提供的 ASDF 的實現</p>
<ul>
<li><a href="http://www.scieneer.com/scl/">SCL</a></li>
</ul>
<p>過時的 Common Lisp 實現:</p>
<ul>
<li><a href="http://lisp.tw/">CORMANLISP</a></li>
<li><a href="http://lisp.tw/">GCL</a></li>
<li><a href="http://lisp.tw/">GENERA MCL</a></li>
</ul>
<h3>檢查 ASDF 的版本:</h3>
<p>可以輸入代碼來檢查 ASDF 版本：</p>
<table><tr><td><pre>1
2
</pre></td><td><pre><span>(<span>asdf</span><span><span>:asdf-version</span>)</span></span> <span>; =&gt; 2.20</span>
<span>; 實際版本根據你所使用的 CL 實現不同。</span>
</pre></td></tr></table>

<p>或是寫一個函數：</p>
<table><tr><td><pre>1
2
3
4
5
6
7
8
9
</pre></td><td><pre><span>(<span>defun</span><span> my-asdf-version <span>(<span>)</span></span>
  <span>(<span>when</span><span> <span>(<span>find-package</span><span> <span>:asdf</span>)</span></span>
    <span>(<span>let</span><span> <span>(<span><span>(<span>ver</span><span> <span>(<span>symbol-value</span><span> <span>(<span>or</span><span> <span>(<span>find-symbol</span><span> <span>(<span>string</span><span> <span>:*asdf-version*</span>)</span></span> <span>:asdf</span>)</span></span>
                                 <span>(<span>find-symbol</span><span> <span>(<span>string</span><span> <span>:*asdf-revision*</span>)</span></span> <span>:asdf</span>)</span></span>)</span></span>)</span></span>)</span></span>)</span></span>
      <span>(<span>etypecase</span><span> ver
        <span>(<span>string</span><span> ver)</span></span>
        <span>(<span>cons</span><span> <span>(<span>with-output-to-string</span><span> <span>(<span>s</span><span>)</span></span>
                <span>(<span>loop</span><span> for <span>(<span>n</span><span> . m)</span></span> on ver do <span>(<span>princ</span><span> n s)</span></span> <span>(<span>when</span><span> m <span>(<span>princ</span><span> <span>"."</span> s)</span></span>)</span></span>)</span></span>)</span></span>)</span></span>
        <span>(<span>null</span><span> <span>"1.0"</span>)</span></span>)</span></span>)</span></span>)</span></span>)</span></span>
</pre></td></tr></table>

<table><tr><td><pre>1
2
3
</pre></td><td><pre><span>(<span>my-asdf-version</span><span>)</span></span> <span>; =&gt; &quot;version-number&quot;</span>
<span>; Clozure Common Lisp Version 1.8-r15286M  (DarwinX8664)!</span>
<span>; (my-asdf-version) =&gt; &quot;2.20&quot;</span>
</pre></td></tr></table>

<p>若返回的字串為 <code>&quot;1.0&quot;</code>，則可能是 <strong>ASDF 1.77</strong> 之前的版本（充滿 bug 的 1.x 版本)。</p>
<h2>獲得 ASDF</h2>
<ol>
<li>直接下載 <a href="http://common-lisp.net/project/asdf/asdf.lisp">ASDF.lisp</a></li>
<li>下載完整的 <a href="http://common-lisp.net/project/asdf/asdf.tar.gz">tarball</a></li>
<li>使用 Git:</li>
</ol>
<p>注意：master 分支為開發版本（dev），stable 分支為穩定版本。</p>
<table><tr><td><pre>1
</pre></td><td><pre>git clone git://common-lisp.net/projects/asdf/asdf.git
</pre></td></tr></table>

<p>可以到 <a href="http://common-lisp.net/gitweb?p=projects/asdf/asdf.git">gitweb</a> 瀏覽最近的開發狀態</p>
<p>下載歷史版本的 tarball: <a href="http://common-lisp.net/project/asdf/archives/">archives</a></p>
<h2>升級 ASDF</h2>
<p>將 ASDF.lisp 放到 <code>/path/to/new/asdf/</code> 目錄下</p>
<p>升級：</p>
<table><tr><td><pre>1
2
3
</pre></td><td><pre><span>(<span>require</span><span> <span>"asdf"</span>)</span></span>
<span>(<span>push</span><span> #p<span>"/path/to/new/asdf/"</span> asdf<span>:*central-registry*</span>)</span></span>
<span>(<span>asdf</span><span><span>:oos</span> 'asdf<span>:load-op</span> <span>:asdf</span>)</span></span>
</pre></td></tr></table>

<p>記得加上路徑後面的 <code>/</code> ，。</p>
<p>如果不知道何故，仍然升級不了系統的 ASDF，去官網下一個最新的 ASDF，把這行代碼加入到啟動腳本 (startup script)裡（如 <code>sbcl.rc</code>、<code>ccl-init.lisp</code>）：</p>
<table><tr><td><pre>1
</pre></td><td><pre><span>(<span>load</span><span> <span>"/path/to/new/asdf/asdf.lisp"</span>)</span></span>
</pre></td></tr></table>

<h3>升級 ASDF 的限制</h3>
<ul>
<li><p>先前加載的 ASDF 擴展都得重新加載，如: CFFI-Grovel。事實上很難知道啥擴展要重加載，ASDF 2.0.15 後自動替你完成，不用擔心。</p>
</li>
<li><p>總之有一大堆相容性的理由，就升級到 ASDF3 吧！（詳見 ASDF manual）</p>
</li>
<li><p>避免 ASDF 產生相依性問題</p>
</li>
</ul>
<p>系統定義 (<code>defsystem</code>) 裡不要有 <code>:depends-on (:asdf)</code> 或是 <code>:depends- on ((:version :asdf &quot;2.010&quot;))</code></p>
<p>應該用下面這段代碼來檢查，確保 ASDF 的版本夠新：</p>
<table><tr><td><pre>1
2
3
</pre></td><td><pre><span>(<span>unless</span><span> <span>(<span>or</span><span> #+asdf<span>2</span> <span>(<span>asdf</span><span><span>:version-satisfies</span>
                     <span>(<span>asdf</span><span><span>:asdf-version</span>)</span></span> <span>*required-asdf-version*</span>)</span></span>)</span></span>
  <span>(<span>error</span><span> <span>"FOO requires ASDF ~A or later."</span> <span>*required-asdf-version*</span>)</span></span>)</span></span>
</pre></td></tr></table>

<h2>配置 ASDF</h2>
<p>ASDF 就是幫我們加載及編譯系統的工具， 我們需要撰寫一個 <code>.asd</code> 文件，跟 ASDF 說系統的定義是什麼、依賴什麼文件阿、包放在哪阿…等等。</p>
<p>安裝 Common Lisp 軟件的默認位置為：</p>
<pre><code>~<span>/.local/share</span><span>/common-lisp/source</span><span>/
</span></code></pre>
<p>安裝在這就無需配置了！</p>
<p>要是安裝在別地方，你得給 ASDF 打個招呼才行。</p>
<p>但用 Quicklisp 安裝的朋友，<a href="http://xach.com/">Xach</a> 已經幫你打好招呼了…</p>
<p>要讓 ASDF 知道你的 <code>.asd</code> 文件在 <code>~/user/.asd-link-farm/</code> ，首先建個目錄：<code>/.config/common-lisp/source-registry.conf.d/</code>，並添加一個文件，以 <code>.conf</code> 結尾，如：</p>
<p><code>42-asd-link-farm.conf</code> 並添加內容：</p>
<pre><code><span>(<span><span>:directory</span> <span>"/home/luser/.asd-link-farm/"</span>)</span></span>
</code></pre>
<p>告訴 ASDF 來這裡搜尋 <code>.asd</code> 文件，若該目錄有子目錄存在，需要遞歸搜索，則使用：</p>
<pre><code><span>(<span><span>:tree</span> <span>"/home/luser/.asd-link-farm/"</span>)</span></span>
</code></pre>
<p>這邊命名有個小技巧，添加 42 當作前綴，要是你保持這個習慣，將會按照號碼搜索。</p>
<p><code>.conf</code> 使得編輯器不會給你添加一些有的沒有的備份文件，而用CLISP 同時要處理有＆無擴展名的文件是很痛苦的，所以用 <code>.conf</code> 吧。</p>
<p>這樣當要找某個系統時，ASDF 就會去掃描這些 <code>.conf</code> ，看看有沒有你需要的系統存在。</p>
<p>可以使用這個命令來重置 source-registry 的配置文件：</p>
<pre><code><span>(<span>asdf</span><span><span>:clear-source-registry</span>)</span></span>
</code></pre>
<p>實際上在 dump 一個 Lisp image 前，使用</p>
<pre><code><span>(<span>asdf</span><span><span>:clear-configuration</span>)</span></span>
</code></pre>
<p>就會自動重置 source-registry 的配置文件了。</p>
<h3>配置 ASDF 來找到系統（舊風格）</h3>
<p><code>push</code> 路徑到 <code>asdf:*central-registry*</code> 變量。</p>
<p>要在加載完 ASDF 後，使用某個系統前配置好這個變量。</p>
<p>（寫在你的啟動腳本裡，如 <code>~/.sbclrc</code>）</p>
<p>缺省 ASDF2 &amp; ASDF3 的 <code>asdf:*central-registry*</code> 為空，還保留這個變量是考慮到兼容性 (ASDF1)。</p>
<p>例子：告訴 ASDF 找到 <code>/home/me/src/foo/foo.asd</code></p>
<p><em>.sbclrc:</em></p>
<pre><code><span>(<span>push</span><span> <span>"/home/me/src/foo/"</span> asdf<span>:*central-registry*</span>)</span></span>
</code></pre>
<p>注意最後一個 <code>/</code> ， <strong>很重要</strong> ，一定要加上。因為 ASDF 在尋找系統時，會將 <code>central-registry</code> 裡的每個 entry 都求值，再 <code>coerce</code> 成一個路徑名，沒有加 <code>/</code> 的話，會被當成文件，而不是目錄。</p>
<p>通常 <code>.asd</code> 文件都存在不同的目錄下，通常的作法是用一個集中的目錄：比如 <code>asd-link-farm</code> ，目錄內放個 <code>.asd</code> 文件的 <em>symbolic links</em> ，再把 <code>asd-link-farm</code> 推至 <code>central-registry</code> 。</p>
<p><strong>MacOS 請不要使用 Alias，Alias 與 symbolics link 是不一樣的！</strong></p>
<p>例子：</p>
<p><code>#p&quot;/home/me/cl/systems/&quot;</code> 是 <code>asdf:*central-registry*</code> 的成員，則 <code>foo</code> 系統可以這麼配置:</p>
<table><tr><td><pre>1
2
</pre></td><td><pre>$ cd /home/me/cl/systems
$ ln -s ~/src/foo/foo.asd
</pre></td></tr></table>

<h3>配置 ASDF 存儲 object 文件的位置</h3>
<p>ASDF 讓你自己決定 object 文件存在哪裡，並帶有合理的默認值。</p>
<p>這使得不同版本的 Common Lisp 實現得以共享代碼庫，以不同的選項來編譯，也讓 object 文件不會污染了代碼庫。</p>
<p>從 ASDF2 開始，ASDF 加入了 <code>asdf-output-translations</code> 工具，用來控制 object 文件存放的位置。這個工具請參閱第八章（<a href="http://lisp.tw/">控制 ASDF 存放編譯後的文件</a>）</p>
<h4>object 文件？</h4>
<p>每個 Common Lisp 實現的編譯器，用二進制表示源文件文件（每個實現的表示方式不同，所以互相不兼容）</p>
<p><code>.fasl</code> for fast load</p>
<h4>什麼是 translation?</h4>
<p>路徑名的對應，比如將 <code>/foo/bar/baz/quux/</code> 對應到 <code>/where/i/want/my/fasls/</code> 。</p>
<p>要實現上面的對應，建立一個目錄 <code>~/.config/common-lisp/asdf-output-translations.conf.d/</code> ，並創建一個擴展名為 <code>.conf</code> 的文件，如：<code>42-bazquux.conf</code> （名字可隨便取），並添加以下內容：</p>
<pre><code><span>(<span><span>"/foo/bar/baz/quux/"</span> <span>"/where/i/want/my/fasls/"</span>)</span></span>
</code></pre>
<p>要是某個特定目錄下的路徑名不想做對應的話，創建一個文件 <code>40-disable-toto.conf</code>)：</p>
<pre><code><span>(<span><span>"/toto/tata/"</span>)</span></span>
</code></pre>
<p>要整個禁止目錄的 translation，可以創建一個文件 <code>00-disable.conf</code> :</p>
<pre><code><span>(<span>t</span><span> <span>t</span>)</span></span>
</code></pre>
<p>要是想重置 source-registry 的配置可以使用：</p>
<pre><code><span>(<span>asdf</span><span><span>:clear-output-translations</span>)</span></span>
</code></pre>
<p>你應該在 dump Lisp image 之前先將配置還原到默認設置。再一次強調，你應該在 dump image 之前執行：</p>
<pre><code><span>(<span>asdf</span><span><span>:clear-configuration</span>)</span></span>
</code></pre>
<p>最後要注意的是，ASDF2 以前，其他的 ASDF 擴充 （add-ons）同樣提供了這個功能，但每個實現的方式有著巧妙的差異，並且各自不兼容：ASDF-Binary-Locations, cl-launch, common-lisp-controller. ASDF-Binary-Locations 已經功成身退了，應該不要再使用它了。 cl-launch 3.000 以及 common-lisp-controller 7.2 已經被更新，好讓他們可以給 ASDF 提供這個功能。</p>
<h3>重置配置</h3>
<p>當 dump 並 restore image 時，會是你在實驗配置時，你有時會想重置配置。可以使用 <code>clear-configuration</code> 這個函數，這個函數將會 <em>UNDO</em> 所有的 ASDF 配置，包含了 source-registry 或是 output-translations。</p>
<p>如果你使用 SBCL, CMUCL 或是 SCL，可以用這段代碼再 dump 出 image 之前自動清空配置:</p>
<table><tr><td><pre>1
2
3
4
</pre></td><td><pre>#+(or cmu sbcl scl)
(pushnew ’<span>clear</span>-configuration
         #+(or cmu scl) ext:<span>*before</span>-save-initializations*
         #+sbcl sb-ext:<span>*save</span>-hooks<span>*)</span>
</pre></td></tr></table>


<h2>4. 使用 ASDF</h2>
<h3>4.1 載入系統</h3>
<p>通過對下面這個 Lisp 形式求值來載入 <code>foo</code> 系統：</p>
<table><tr><td><pre>1
</pre></td><td><pre><span>(<span>asdf</span><span><span>:load-system</span> <span>:foo</span>)</span></span>
</pre></td></tr></table>

<p>某些實現（ABCL, Allegro CL, Clozure CL, CMUCL, ECL, GNU CLISP, LispWorks, MKCL, SBCL 以及 XCL）提供了 ASDF hook 到 <code>CL:REQUIRE</code> 工具裡，你可以直接：</p>
<table><tr><td><pre>1
</pre></td><td><pre><span>(<span>require</span><span> <span>:foo</span>)</span></span>
</pre></td></tr></table>

<p>舊版的 ASDF 你需要使用：</p>
<table><tr><td><pre>1
</pre></td><td><pre><span>(<span>asdf</span><span><span>:oos</span> 'asdf<span>:load-op</span> <span>:foo</span>)</span></span>
</pre></td></tr></table>

<p>注意系統的名字可以是字串或符號（通常是關鍵字， <strong>小寫</strong> ）。</p>
<h3>4.2 其他操作</h3>
<p>ASDF 提供了三個基本操作：</p>
<ul>
<li><code>load-system</code></li>
<li><code>compile-system</code></li>
<li><code>test-system</code></li>
</ul>
<p>另外還有個 <code>require-system</code> ，另一種的 <code>load-system</code> ，會試著更新已經載入的系統。</p>
<p>ASDF 提供了一個通用函數（generic function）<code>operate</code> （通常縮寫成 <code>oos</code>），除了加載、編譯、測試之外的操作，使用 <code>oos</code>。</p>
<h3>4.3 總結</h3>
<p>要使用 ASDF：</p>
<ol>
<li><p>加載 ASDF 到 Lisp 鏡像：</p>
<pre><code> <span>(<span>require</span><span> <span>"asdf"</span>)</span></span>
</code></pre>
<p> 或</p>
<pre><code> <span>(<span>load</span><span> <span>"/path/to/asdf.lisp"</span>)</span></span>
</code></pre>
</li>
<li><p>確定 ASDF 可通過正確的 source-registry 配置，找到系統定義。</p>
</li>
<li><p>用 <code>(asdf:load-system :my-system)</code> 來加載系統</p>
</li>
</ol>
<h3>4.4 更進一步</h3>
<p>就這樣了！要加載別人的系統，你需要知道的就這麼多了。文章接下來會告訴你如何替你寫的 Common Lisp 軟件撰寫系統定義，知識包含了如何擴展 ASDF 來定義新的操作及組件類型（components types）。</p>