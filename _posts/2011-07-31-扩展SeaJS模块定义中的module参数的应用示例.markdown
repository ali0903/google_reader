---
layout: post
title:  "扩展SeaJS模块定义中的module参数的应用示例"
date:   2011-07-31 22:29:34
author: 
categories: program
---

## 扩展SeaJS模块定义中的module参数的应用示例
### by 
### at 2011-07-31 22:29:34
### original <http://limu.iteye.com/blog/1136712>

近三四个月公司有两个比较大的项目在忙,没怎么更新博客.现在一个项目已进入平台开发期,另一个即将上线,接下来会多拿出时间进行一些技术总结.已经预定了月中懒懒交流会上的分享,也会写一系列博文出来.即将上线的这个项目是一个OPOA应用,上一篇博客<a href="http://limu.iteye.com/blog/1064024">"让Mustache支持简单的IF语句"</a>和本篇都是这个项目的某个角落,它们有个统一的TAG:MagixJS,我会逐步揭开它.
<br>
<br><a href="http://seajs.com">SeaJS</a>是我的同事玉伯开发的一套小巧且强大的Module Loader.我想前端的朋友多多少少会有耳闻,就不多说了.接触Java还算多些,在我看来seajs的module就像是Java的class.模块与模块之间有着依存关系,seajs会保证你在使用一个模块时,已经将所依赖的其他模块载入到脚本运行环境中.
<br>
<br>在模块化的代码组织形式下,我们可以放心大胆的细粒度书写模块(详见<a href="http://limu.iteye.com/blog/845870">我之前关于模块静态编译的分析</a>).当前的OPOA项目已有近一百个模块.在开发过程中,我发现无论模块本身还是模块之间的依存关系,应该都可以更清楚的描述出来.比如:<ul>
<li>模块输出的是什么,构造器,静态对象还是某个的构造器的实例?
</li><li>为什么A模块依赖B模块,是因为A继承B,还是因为A是B的实例?
</li></ul><strong><span style="font-size:large">需求来了</span></strong>
<br>看一个具体场景吧:
<br><pre name="code">
Person = function(name){}
Stuff = function(name,id){}//extend Person
Developer = function(name,id,skills){}//extend Stuff
</pre>
<br>Developer继承Stuff继承Person继承Object这样的关系,我们将每个构造器写成一个模块.我们用YUI,KISSY使用的"Parasitic Combination Inheritance"模式(详见"JavaScript高级程序设计第2版"6.2.5,<a href="http://developer.yahoo.com/yui/3/api/oop.js.html">YUI代码</a>)来书写类,并将其封装成SeaJS模块形式,以Stuff为例,代码如下:
<br><pre name="code">
define(function(require, exports, module){
    var Person = require("./person");
    var Stuff = function(name, id){
        Stuff.superclass.constructor.apply(this, arguments);
        this.id = id;
    };
    extend(Stuff, Person, {
        getId: function(){
            return this.id;
        }
    });
    return Stuff;
});
</pre>
<br>我们可以马上写出这三个模块,我们把它们放在mptest文件夹下,加上seajs的全局alias指明mptest文件夹的http访问路径,那么我们就可以通过"mptest/person","mptest/stuff"和"mptest/developer"这三个模块模块名来使用它们.
<br>
<br>但这三个模块之间的继承关系并非一目了然.比如我在chrome的控制台下查看一个developer实例.
<br><img src="http://dl.iteye.com/upload/attachment/527607/d6809a10-f8c8-3a85-bcdb-f4944332fb71.png">
<br>图中两个"F",两个"Object"分别代表完全不同的四个东西,可我们很难简单识别出来,如果对象很多,层次复杂,这不易读,而这样的代码交给合作开发的partner,要费很多口舌.我们希望能够看到如下的这张图:
<br><img src="http://dl.iteye.com/upload/attachment/527611/299b6345-bab5-3033-a933-f90500d707fc.png">
<br>通过这张图,我们可以清晰的看出类之间的继承关系,不用唠叨,也不怕忘掉.在模块化背景下进一步深入下去,我们可以将框住的"Person","Stuff"处显示对应的模块名,那么一旦我们需要修改某个方法时,如Stuff的getId(),就能够快速找到代码所在文件stuff.js.如下
<br><img src="http://dl.iteye.com/upload/attachment/527613/2170131d-c82d-3980-9eb2-683397d040a1.png">
<br>
<br>我就是想在监视变量的时候能够看到上图这样的结果.
<br>我还想通过简单的类描述,能够用NodeJS脚本跑出一整个包的类图.
<br>我还想再Chrome控制台实现YUI Logger的日志分类显示以及过滤功能.
<br>这些都可以辅助提高开发调试效率,也让模块间结构更清晰.
<br>可是我又不想直接在构造器上做扩展,比如为Person指定静态属性,Person.modname="mptest-person",坏处不细说.
<br>
<br><strong><span style="font-size:large">扩展module解决问题</span></strong>
<br>我把需求拿出来和玉伯一起讨论,发现玉伯同学也在偷偷的做类似的提取模块特有信息的事.最终发现模块输出的内容位于module对象的exports属性中,而同时模块对象已经拥有了id,dependencies属性.而module是一个普通object对象,那么如果我们把module由普通object改为Module类的实例,那么我们就可以在Module.prototype内扩展一系列公共方法来完成不同的需求.于是就有了seajs的这个<a href="https://github.com/seajs/seajs/issues/67">issue</a>.玉伯同学很快就在SeaJS v0.9.5版本中实现了它,而我则答应写今天这篇文章来介绍这个issue的具体应用.
<br>
<br>首先我扩展了一个module.getName方法,可以根据module.id(即module地址)获取到module的shortname作为代号.
<br><pre name="code">
define(function(require, exports, module){
    module.constructor.prototype.getName = function(){
        var alias = seajs._data.config.alias;
		var id = this.id;
		var name = id.substr(id.lastIndexOf("/")+1).split(".")[0];
		for (a in alias){
			if(id.indexOf(alias[a])===0){
				name = id.split(alias[a])[1];
				name = (a+name.split(".")[0]);
			}
		}
		return name;
    };
});
</pre>
<br>(注:这个方法并不严谨,只特殊处理了alias中包含完整http地址的情况,仅做示意.)
<br>
<br>然后在实现object.create(<a href="http://ejohn.org/blog/ecmascript-5-objects-and-properties/">详见这里</a>)的时候用了eval,把模块名取代"F"作为局部function变量名.
<br><pre name="code">
var object = function(o){
    var cn = this.getName().split("/").join("_");
    eval("function " + cn + "(){}" + cn + ".prototype = o;var r = new " + cn + "();");
    return r;
};
</pre>
<br>(注:这个方法依然很不好,不应该使用eval,但是尚未找到替代方案.关于eval再说几句,eval可能会影响到Js引擎对代码的自动优化,会影响到代码的压缩混淆效果,所以这个里的实现不应该用于生产环境,只做开发调试辅助.)
<br>
<br>最后我们实现module.extend方法,通过这个方法实现继承.
<br><pre name="code">
define(function(require, exports, module){
    var Person = require("./person");
    var Stuff = function(name, id){
        Stuff.superclass.constructor.apply(this, arguments);
        this.id = id;
    };
    module.extend(Stuff, Person, {
        getId: function(){
            return this.id;
        }
    });
    return Stuff;
});
</pre>
<br>
<br>然而还有一个限制,在写Person类的时候,需要显示的指明继承自Object
<br><pre name="code">
define(function(require, exports, module){
    var Person = function(name){
        this.name = name;
    };
    module.extend(Person, Object, {
        getName: function(){
            return this.name;
        }
    });
    return Person;
});
</pre>
<br>
<br>可以看到这几段代码都还不尽人意,而且只能在Chrome浏览器下看到这张图,不过这却是一个介绍module原型扩展的很好示例.
<br>
<br><strong><span style="font-size:large">为module添加meta信息</span></strong>
<br>前面的例子,只是将name作为了局部变量,让debug时可以看得更清晰,我们在更多浏览器,或者离线状态下获取这些信息,我们需要将这些信息显式的存储在module实例里.因为module和exports的双生关系.我们可以在需要的时候把这些信息存在module实例中,不需要的时候(比如生产环境)将这些附加信息撤掉.比如Java的interface,只对开发和编译时校验有意义,运行时并无特殊意义.
<br>
<br>于是又实现了一个setMeta方法,这个方法现在接受两个参数,第一个是type用来标明模块输出内容是"constructor","static","instance","abstract"还是"interface".如果是"constructor",第二个参数指明继承自哪个对象.如果是"instance"第二个参数指明构造器.
<br><img src="http://dl.iteye.com/upload/attachment/527680/0a1dd62a-f0ee-308b-a017-56ada57c44fd.png">
<br>有了这些信息,我们可以辅助生成系统类图,甚至辅助生成文档.
<br>
<br>这些信息可以在开发时发挥作用,但是真的可以在线上去除么?现在通过显示的调用,module.setMeta()来写入meta数据时无法轻易做到线上剔除的.主要的问题是我们为模块输出内容时使用的是"="赋值语句或"return"语句.这种API的扩展性具有一定的局限性.如果是通过一个方法来输出模块内容,如module.export(exportsObject);这样就可以方便的重写export方法,在开发环境和生产环境选用不同的export实现.
<br>
<br><strong><span style="font-size:large">更好的Logger</span></strong>
<br>大大小小的框架喜欢提供log方法,可是有一个问题,在chrome的控制台下,所有日志输出都是log方法定义的那一行.无法快速定位到关注的代码.如图:
<br><img src="http://dl.iteye.com/upload/attachment/527687/e76fdeba-36c6-363e-8703-8508f3a4eddb.png">
<br>所以建议使用原生的console.log,对于没有console的浏览器,我们伪造一个console
<br><pre name="code">
console = window.console || {
    log:function(s){
        //alert(s);
    }
}
</pre>
<br>接下来,我们为所有module添加log方法,
<br><pre name="code">
define(function(require, exports, module){
    var mp = module.constructor.prototype;
    mp.log = function(s){
        if (typeof s == "string") {
            s = "[" + this.getName() + "]:  " + s;
        }
        return s;
    };
});
//usage
console.log(module.log(s));
</pre>
<br>这样的好处我们没有丢失行号,又可以给输出内容加上模块信息,继续扩展下去,可以指定哪些模块输出日志,哪些不输出.我的目标是把控制台改造成代行号的<a href="http://developer.yahoo.com/yui/3/console/">YUI Console</a>.如图:
<br><img src="http://dl.iteye.com/upload/attachment/527692/4e53bde4-c10d-38cf-bf01-30bc48fab692.png">
<br>不过还是可耻的失败了,因为即使module.log返回空,也会打一行日志出来.如图:
<br><img src="http://dl.iteye.com/upload/attachment/527695/ce82a9f3-fadc-3beb-9aae-d270da084728.png">
<br>我们想只打出stuff模块的信息,其他模块的log信息仍留了一些空行,不过点击行号可以立刻定位到代码,这个却非常不错.
<br>另外大家可能觉得这样输出日志太麻烦,这就要看大家的真实需求了,同时也可以向Java程序员打听一下如果输出一行日志需要写多少代码..
<br>
<br><strong><span style="font-size:large">总结一下</span></strong>
<br>今天的示例说到底都不太成功.
<br>三个示例都在使用的module.getName(),访问了seajs内置对象,且只考虑了alias情况.
<br>第一个对象监测示例,不得己动用了eval,而且只能应用于chrome下"寄生式"继承这一种继承模式.
<br>第二个meta信息示例,还无法方便的从生产系统中移除meta信息.
<br>第三个log扩展示例,更是因为会留空行,而无法把console改造的符合期望.
<br>但是这三个示例从三个角度展示了扩展module可以做到什么,大家可以发挥想象在各自的项目中有针对性的实现自己的功能.
<br>我们只要知道module和exports是一对双生,我们不方便修改exports中的业务对象,但却可以在module对象里添加对exports的描述,乃至对exports的操作.
<br>以上.<a href="http://limu.github.com/demo/moduleplus/">示例在此</a>,<a href="https://github.com/limu/limu.github.com/tree/master/demo/moduleplus">代码在此</a>.
<br>
              
              <br><br>
              <span style="color:red">
                <a href="http://limu.iteye.com/blog/1136712#comments" style="color:red">已有 <strong>1</strong> 人发表留言，猛击-&gt;&gt;<strong>这里</strong>&lt;&lt;-参与讨论</a>
              </span>
              <br><br><br>
<span style="color:#e28822">ITeye推荐</span>
<br>
<ul><li><a href="http://limu.iteye.com/clicks/433"><span style="color:red;font-weight:bold">—软件人才免语言低担保 赴美带薪读研！— </span></a></li></ul>
<br><br><br>