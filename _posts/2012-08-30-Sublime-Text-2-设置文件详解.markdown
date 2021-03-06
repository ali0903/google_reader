---
layout: post
title:  "Sublime Text 2 设置文件详解"
date:   2012-08-30 10:13:18
author: 杨玉廷
categories: program
---

## Sublime Text 2 设置文件详解
### by 杨玉廷
### at 2012-08-30 10:13:18
### original <http://hp.dewen.org/?p=1361>

<p><a href="http://www.sublimetext.com/">Sublime Text 2</a>是那种让人会一眼就爱上的编辑器，不仅GUI让人眼前一亮，功能更是没的说，拓展性目前来说也完全够用了，网上<a href="http://www.appinn.com/sublime-text-2/">介绍软件的文章</a>和<a href="http://www.qianduan.net/essential-to-sublime-the-text-2-plugins.html">推荐插件的文章</a>也不少，而且很不错，大家可以去找找自己需要的。<span></span></p>
<p>之前想设置什么都是直接在网上搜，但最近想调行距，这个把我给难住了，软件上的设置没找到，网上搜也没有，最后的最后在<a href="http://hp.dewen.org/?p=1361">Sublime</a>的<a href="http://www.sublimetext.com/forum/viewtopic.php?f=4&amp;t=54">官方论坛</a>找到了，个人觉得行距还是很影响视觉体验的，看看下面的对比图就知道了:</p>
<div style="text-align:center;margin-bottom:5px;margin-top:5px"><img src="http://hp.dewen.org/wp-content/uploads/2012/08/Sublime-Text-2-line-height.png" width="600" alt="Sublime Text 2 设置文件详解" title="Sublime Text 2 设置文件详解" border="0"></div>
<p>看来想驾驭好这软件不弄清楚配置文件是不行了，周末找了时间把配置文件的每条配置信息都加上了中文注释，现在贴出来和大家共享，里面有解释不清楚的也欢迎大家伙来互相讨论:)</p>
<p>另外，这也是我的第一篇博客，以后我会多写些前端方面的文章和大家共享，欢迎一起讨论:)</p>
<p>Preferences.sublime-settings文件：</p>
<p><span style="color:#999999">// While you can edit this file, it’s best to put your changes in<br>
// “User/Preferences.sublime-settings”, which overrides the settings in here.<br>
//<br>
// Settings may also be placed in file type specific options files, for<br>
// example, in Packages/Python/Python.sublime-settings for python files.</span><br>
{<br>
// Sets the colors used within the text area<br>
// 主题文件的路径<br>
“color_scheme”: “Packages/Color Scheme – Default/Monokai.tmTheme”,</p>
<p><span style="color:#999999">// Note that the font_face and font_size are overriden in the platform<br>
// specific settings file, for example, “Preferences (Linux).sublime-settings”.<br>
// Because of this, setting them here will have no effect: you must set them<br>
// in your User File Preferences.</span><br>
// 设置字体和大小，必须在Settings-User里重写，这里设置没有任何效果<br>
“font_face”: “Consolas”,<br>
“font_size”: 12,</p>
<p><span style="color:#999999">// Valid options are “no_bold”, “no_italic”, “no_antialias”, “gray_antialias”,<br>
// “subpixel_antialias” and “no_round” (OS X only)</span><br>
// 字体选项：no_bold不显示粗体字，no_italic不显示斜体字，no_antialias和no_antialias关闭反锯齿<br>
// subpixel_antialias和no_round是OS X系统独有的<br>
“font_options”: [],</p>
<p><span style="color:#999999">// Characters that are considered to separate words</span><br>
// 在文字上双击会全选当前的内容，如果里面出现以下字符，就会被截断<br>
“word_separators”: “./\\()\”‘-:,.;&lt;&gt;~!@#$%^&amp;*|+=[]{}`~?”,</p>
<p><span style="color:#999999">// Set to false to prevent line numbers being drawn in the gutter</span><br>
// 是否显示行号<br>
“line_numbers”: true,</p>
<p><span style="color:#999999">// Set to false to hide the gutter altogether</span><br>
// 是否显示行号边栏<br>
“gutter”: true,</p>
<p><span style="color:#999999">// Spacing between the gutter and the text</span><br>
// 行号边栏和文字的间距<br>
“margin”: 4,</p>
<p><span style="color:#999999">// Fold buttons are the triangles shown in the gutter to fold regions of text</span><br>
// 是否显示代码折叠按钮<br>
“fold_buttons”: true,</p>
<p><span style="color:#999999">// Hides the fold buttons unless the mouse is over the gutter</span><br>
// 不管鼠标在不在行号边栏，代码折叠按钮一直显示<br>
“fade_fold_buttons”: true,</p>
<p><span style="color:#999999">// Columns in which to display vertical rulers</span><br>
//列显示垂直标尺，在中括号里填入数字，宽度按字符计算<br>
“rulers”: [],</p>
<p><span style="color:#999999">// Set to true to turn spell checking on by default</span><br>
// 是否打开拼写检查<br>
“spell_check”: false,</p>
<p><span style="color:#999999">// The number of spaces a tab is considered equal to</span><br>
// Tab键制表符宽度<br>
“tab_size”: 4,</p>
<p><span style="color:#999999">// Set to true to insert spaces when tab is pressed</span><br>
// 设为true时，缩进和遇到Tab键时使用空格替代<br>
“translate_tabs_to_spaces”: false,</p>
<p><span style="color:#999999">// If translate_tabs_to_spaces is true, use_tab_stops will make tab and</span><br>
// backspace insert/delete up to the next tabstop<br>
// translate_tabs_to_spaces设置为true，Tab和Backspace的删除/插入作用于制表符宽度<br>
// 否则作用于单个空格<br>
“use_tab_stops”: true,</p>
<p><span style="color:#999999">// Set to false to disable detection of tabs vs. spaces on load</span><br>
// false时禁止在载入的时候检测制表符和空格<br>
“detect_indentation”: true,</p>
<p><span style="color:#999999">// Calculates indentation automatically when pressing enter</span><br>
// 按回车时，自动与制表位对齐<br>
“auto_indent”: true,</p>
<p><span style="color:#999999">// Makes auto indent a little smarter, e.g., by indenting the next line<br>
// after an if statement in C. Requires auto_indent to be enabled.</span><br>
//针对C语言的<br>
“smart_indent”: false,</p>
<p><span style="color:#999999">// Adds whitespace up to the first open bracket when indenting. Requires<br>
// auto_indent to be enabled.</span><br>
// 需要启用auto_indent，第一次打开括号缩进时插入空格？（没测试出来效果…）<br>
“indent_to_bracket”: true,</p>
<p><span style="color:#999999">// Trims white space added by auto_indent when moving the caret off the<br>
// line.</span><br>
// 显示对齐的白线是否根据回车、tab等操作自动填补<br>
“trim_automatic_white_space”: true,</p>
<p><span style="color:#999999">// Disables horizontal scrolling if enabled.<br>
// May be set to true, false, or “auto”, where it will be disabled for<br>
// source code, and otherwise enabled.</span><br>
// 是否自动换行，如果选auto，需要加双引号<br>
“word_wrap”: false,</p>
<p><span style="color:#999999">// Set to a value other than 0 to force wrapping at that column rather than the<br>
// window width</span><br>
// 设置窗口内文字区域的宽度<br>
“wrap_width”: 0,</p>
<p><span style="color:#999999">// Set to false to prevent word wrapped lines from being indented to the same<br>
// level</span><br>
// 防止被缩进到同一级的字换行<br>
“indent_subsequent_lines”: true,</p>
<p><span style="color:#999999">// Draws text centered in the window rather than left aligned</span><br>
// 如果没有定义过，则文件居中显示（比如新建的文件）<br>
“draw_centered”: false,</p>
<p><span style="color:#999999">// Controls auto pairing of quotes, brackets etc</span><br>
// 自动匹配引号，括号等<br>
“auto_match_enabled”: true,</p>
<p><span style="color:#999999">// Word list to use for spell checking</span><br>
// 拼写检查的单词列表路径<br>
“dictionary”: “Packages/Language – English/en_US.dic”,</p>
<p><span style="color:#999999">// Set to true to draw a border around the visible rectangle on the minimap.<br>
// The color of the border will be determined by the “minimapBorder” key in<br>
// the color scheme</span><br>
// 代码地图的可视区域部分是否加上边框，边框的颜色可在配色方案上加入minimapBorder键<br>
“draw_minimap_border”: false,</p>
<p><span style="color:#999999">// If enabled, will highlight any line with a caret</span><br>
// 突出显示当前光标所在的行<br>
“highlight_line”: false,</p>
<p><span style="color:#999999">// Valid values are “smooth”, “phase”, “blink”, “wide” and “solid”.</span><br>
// 设置光标闪动方式<br>
“caret_style”: “smooth”,</p>
<p><span style="color:#999999">// Set to false to disable underlining the brackets surrounding the caret</span><br>
// 是否特殊显示当前光标所在的括号、代码头尾闭合标记<br>
“match_brackets”: true,</p>
<p><span style="color:#999999">// Set to false if you’d rather only highlight the brackets when the caret is<br>
// next to one</span><br>
// 设为false时，只有光标在括号或头尾闭合标记的两端时，match_brackets才生效<br>
“match_brackets_content”: true,</p>
<p><span style="color:#999999">// Set to false to not highlight square brackets. This only takes effect if<br>
// match_brackets is true</span><br>
// 是否突出显示圆括号，match_brackets为true生效<br>
“match_brackets_square”: false,</p>
<p><span style="color:#999999">// Set to false to not highlight curly brackets. This only takes effect if<br>
// match_brackets is true</span><br>
// 是否突出显示大括号，match_brackets为true生效<br>
“match_brackets_braces”: false,</p>
<p><span style="color:#999999">// Set to false to not highlight angle brackets. This only takes effect if<br>
// match_brackets is true</span><br>
// 是否突出显示尖括号，match_brackets为true生效<br>
“match_brackets_angle”: false,</p>
<p><span style="color:#999999">// Enable visualization of the matching tag in HTML and XML</span><br>
// html和xml下突出显示光标所在标签的两端，影响HTML、XML、CSS等<br>
“match_tags”: true,</p>
<p><span style="color:#999999">// Highlights other occurrences of the currently selected text</span><br>
// 全文突出显示和当前选中字符相同的字符<br>
“match_selection”: true,</p>
<p><span style="color:#999999">// Additional spacing at the top of each line, in pixels</span><br>
// 设置每一行到顶部，以像素为单位的间距，效果相当于行距<br>
“line_padding_top”: 1,</p>
<p><span style="color:#999999">// Additional spacing at the bottom of each line, in pixels</span><br>
// 设置每一行到底部，以像素为单位的间距，效果相当于行距<br>
“line_padding_bottom”: 1,</p>
<p><span style="color:#999999">// Set to false to disable scrolling past the end of the buffer.<br>
// On OS X, this value is overridden in the platform specific settings, so<br>
// you’ll need to place this line in your user settings to override it.</span><br>
// 设置为false时，滚动到文本的最下方时，没有缓冲区<br>
“scroll_past_end”: true,</p>
<p><span style="color:#999999">// This controls what happens when pressing up or down when on the first<br>
// or last line.<br>
// On OS X, this value is overridden in the platform specific settings, so<br>
// you’ll need to place this line in your user settings to override it.</span><br>
// 控制向上或向下到第一行或最后一行时发生什么（没明白也没试出来）<br>
“move_to_limit_on_up_down”: false,</p>
<p><span style="color:#999999">// Set to “none” to turn off drawing white space, “selection” to draw only the<br>
// white space within the selection, and “all” to draw all white space</span><br>
// 按space或tab时，实际会产生白色的点（一个空格一个点）或白色的横线（tab_size设置的制表符的宽度），选中状态下才能看到<br>
// 设置为none时，什么情况下都不显示这些点和线<br>
// 设置为selection时，只显示选中状态下的点和线<br>
// 设置为all时，则一直显示<br>
“draw_white_space”: “selection”,</p>
<p><span style="color:#999999">// Set to false to turn off the indentation guides.<br>
// The color and width of the indent guides may be customized by editing<br>
// the corresponding .tmTheme file, and specifying the colors “guide”,<br>
// “activeGuide” and “stackGuide”</span><br>
// 制表位的对齐白线是否显示，颜色可在主题文件里设置（guide，activeGuide，stackGuide）<br>
“draw_indent_guides”: true,</p>
<p><span style="color:#999999">// Controls how the indent guides are drawn, valid options are<br>
// “draw_normal” and “draw_active”. draw_active will draw the indent<br>
// guides containing the caret in a different color.</span><br>
// 制表位的对齐白线，draw_normal为一直显示，draw_active为只显示当前光标所在的代码控制域<br>
“indent_guide_options”: ["draw_normal"],</p>
<p><span style="color:#999999">// Set to true to removing trailing white space on save</span><br>
// 为true时，保存文件时会删除每行结束后多余的空格<br>
“trim_trailing_white_space_on_save”: false,</p>
<p><span style="color:#999999">// Set to true to ensure the last line of the file ends in a newline<br>
// character when saving</span><br>
// 为true时，保存文件时光标会在文件的最后向下换一行<br>
“ensure_newline_at_eof_on_save”: false,</p>
<p><span style="color:#999999">// Set to true to automatically save files when switching to a different file<br>
// or application</span><br>
// 切换到其它文件标签或点击其它非本软件区域，文件自动保存<br>
“save_on_focus_lost”: false,</p>
<p><span style="color:#999999">// The encoding to use when the encoding can’t be determined automatically.<br>
// ASCII, UTF-8 and UTF-16 encodings will be automatically detected.</span><br>
// 编码时不能自动检测编码时，将自动检测ASCII, UTF-8 和 UTF-16<br>
“fallback_encoding”: “Western (Windows 1252)”,</p>
<p><span style="color:#999999">// Encoding used when saving new files, and files opened with an undefined<br>
// encoding (e.g., plain ascii files). If a file is opened with a specific<br>
// encoding (either detected or given explicitly), this setting will be<br>
// ignored, and the file will be saved with the encoding it was opened<br>
// with.</span><br>
// 默认编码格式<br>
“default_encoding”: “UTF-8″,</p>
<p><span style="color:#999999">// Files containing null bytes are opened as hexadecimal by default</span><br>
// 包含空字节的文件被打开默认为十六进制<br>
“enable_hexadecimal_encoding”: true,</p>
<p><span style="color:#999999">// Determines what character(s) are used to terminate each line in new files.<br>
// Valid values are ‘system’ (whatever the OS uses), ‘windows’ (CRLF) and<br>
// ‘unix’ (LF only).</span><br>
// 每一行结束的时候用什么字符做终止符<br>
“default_line_ending”: “system”,</p>
<p><span style="color:#999999">// When enabled, pressing tab will insert the best matching completion.<br>
// When disabled, tab will only trigger snippets or insert a tab.<br>
// Shift+tab can be used to insert an explicit tab when tab_completion is<br>
// enabled.</span><br>
// 设置为enabled时，在一个字符串间按Tab将插入一个制表符<br>
// 设置为true时，按Tab会根据前后环境进行代码自动匹配填补<br>
“tab_completion”: true,</p>
<p><span style="color:#999999">// Enable auto complete to be triggered automatically when typing.</span><br>
// 代码提示<br>
“auto_complete”: true,</p>
<p><span style="color:#999999">// The maximum file size where auto complete will be automatically triggered.</span><br>
// 代码提示的大小限制<br>
“auto_complete_size_limit”: 4194304,</p>
<p><span style="color:#999999">// The delay, in ms, before the auto complete window is shown after typing</span><br>
// 代码提示延迟显示<br>
“auto_complete_delay”: 50,</p>
<p><span style="color:#999999">// Controls what scopes auto complete will be triggered in</span><br>
// 代码提示的控制范围<br>
“auto_complete_selector”: “source – comment”,</p>
<p><span style="color:#999999">// Additional situations to trigger auto complete</span><br>
// 触发代码提示的其他情况<br>
“auto_complete_triggers”: [ {&quot;selector&quot;: &quot;text.html&quot;, &quot;characters&quot;: &quot;&lt;&quot;} ],</p>
<p><span style="color:#999999">// By default, auto complete will commit the current completion on enter.<br>
// This setting can be used to make it complete on tab instead.<br>
// Completing on tab is generally a superior option, as it removes<br>
// ambiguity between committing the completion and inserting a newline.</span><br>
// 设为false时，选择提示的代码按回车或点击可以输出出来，但选择true时不会输出而是直接换行<br>
“auto_complete_commit_on_tab”: false,</p>
<p><span style="color:#999999">// Controls if auto complete is shown when snippet fields are active.</span><br>
// Only relevant if auto_complete_commit_on_tab is true.<br>
// auto_complete_commit_on_tab必须为true，控制代码提示的活跃度（没明白…）<br>
“auto_complete_with_fields”: false,</p>
<p><span style="color:#999999">// By default, shift+tab will only unindent if the selection spans<br>
// multiple lines. When pressing shift+tab at other times, it’ll insert a<br>
// tab character – this allows tabs to be inserted when tab_completion is<br>
// enabled. Set this to true to make shift+tab always unindent, instead of<br>
// inserting tabs.</span><br>
// 设置为false，使用Shift + tab总是插入制表符<br>
“shift_tab_unindent”: true,</p>
<p><span style="color:#999999">// If true, the selected text will be copied into the find panel when it’s<br>
// shown.<br>
// On OS X, this value is overridden in the platform specific settings, so<br>
// you’ll need to place this line in your user settings to override it.</span><br>
// 选中的文本按Ctrl + f时，自动复制到查找面板的文本框里<br>
“find_selected_text”: true,</p>
<p><span style="color:#999999">//<br>
// User Interface Settings<br>
//</span></p>
<p><span style="color:#999999">// The theme controls the look of Sublime Text’s UI (buttons, tabs, scroll bars, etc)</span><br>
// Data\Packages\Theme – Default\Default.sublime-theme控制软件的主题<br>
“theme”: “Default.sublime-theme”,</p>
<p><span style="color:#999999">// Set to 0 to disable smooth scrolling. Set to a value between 0 and 1 to<br>
// scroll slower, or set to larger than 1 to scroll faster</span><br>
// 滚动的速度<br>
“scroll_speed”: 1.0,</p>
<p><span style="color:#999999">// Controls side bar animation when expanding or collapsing folders</span><br>
// 左边边栏文件夹动画<br>
“tree_animation_enabled”: true,<br>
// 标签页的关闭按钮<br>
“show_tab_close_buttons”: true,</p>
<p><span style="color:#999999">// OS X 10.7 only: Set to true to disable Lion style full screen support.<br>
// Sublime Text must be restarted for this to take effect.</span><br>
// 针对OS X<br>
“use_simple_full_screen”: false,</p>
<p><span style="color:#999999">// Valid values are “system”, “enabled” and “disabled”</span><br>
// 水平垂直滚动条：system和disabled为默认显示方式，enabled为自动隐藏显示<br>
“overlay_scroll_bars”: “system”,</p>
<p><span style="color:#999999">//<br>
// Application Behavior Settings<br>
//</span></p>
<p><span style="color:#999999">// Exiting the application with hot_exit enabled will cause it to close<br>
// immediately without prompting. Unsaved modifications and open files will<br>
// be preserved and restored when next starting.<br>
//<br>
// Closing a window with an associated project will also close the window<br>
// without prompting, preserving unsaved changes in the workspace file<br>
// alongside the project.</span><br>
// 热推出功能！退出时不会提示是否保存文件，而是直接退出<br>
// 下次打开软件时，文件保持退出前的状态，没来得及保存的内容都在，但并没有真实的写在原文件里<br>
“hot_exit”: true,</p>
<p><span style="color:#999999">// remember_open_files makes the application start up with the last set of<br>
// open files. Changing this to false will have no effect if hot_exit is<br>
// true</span><br>
// 软件使用最后的设定打开文件，hot_exit为true时没有效果<br>
“remember_open_files”: true,</p>
<p><span style="color:#999999">// OS X only: When files are opened from finder, or by dragging onto the<br>
// dock icon, this controls if a new window is created or not.</span><br>
// 针对OS X<br>
“open_files_in_new_window”: true,</p>
<p><span style="color:#999999">// Set to true to close windows as soon as the last file is closed, unless<br>
// there’s a folder open within the window. This is always enabled on OS X,<br>
// changing it here won’t modify the behavior.</span><br>
// 针对OS X<br>
“close_windows_when_empty”: true,<br>
// 哪些文件会被显示到边栏上<br>
<span style="color:#999999">// folder_exclude_patterns and file_exclude_patterns control which files<br>
// are listed in folders on the side bar. These can also be set on a per-<br>
// project basis.</span><br>
“folder_exclude_patterns”: [".svn", ".git", ".hg", "CVS"],<br>
“file_exclude_patterns”: ["*.pyc", "*.pyo", "*.exe", "*.dll", "*.obj","*.o", "*.a", "*.lib", "*.so", "*.dylib", "*.ncb", "*.sdf", "*.suo", "*.pdb", "*.idb", ".DS_Store", "*.class", "*.psd", "*.db"],<br>
// These files will still show up in the side bar, but won’t be included in<br>
// Goto Anything or Find in Files<br>
“binary_file_patterns”: ["*.jpg", "*.jpeg", "*.png", "*.gif", "*.ttf", "*.tga", "*.dds", "*.ico", "*.eot", "*.pdf", "*.swf", "*.jar", "*.zip"],</p>
<p><span style="color:#999999">// List any packages to ignore here. When removing entries from this list,<br>
// a restart may be required if the package contains plugins.</span><br>
// 删除你想要忽略的插件，需要重启<br>
“ignored_packages”: ["Vintage"]<br>
}</p>
<p>延伸阅读：<a href="http://hp.dewen.org/?p=962">Sublime Text代码编辑器-初学者不知道的那些事</a></p>
<p><b>本文作者：<a href="http://www.feelcss.com/">Hey@feelcss</a><br>
原文地址：<a href="http://www.feelcss.com/sublime-text-2-settings.html">Sublime Text 2 设置文件详解</a></b></p>