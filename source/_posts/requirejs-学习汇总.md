---
title: requirejs 学习汇总
date: 2017-04-06 22:55:42
categories: 笔记
tags:
  - requirejs
---

![require logo](/uploads/require/logo.png)

>下面是我之前在学习requirejs期间看了几篇博文，加上自己一点点理解将其汇总于此。

## 中文官网
* [中文官网](http://www.requirejs.cn/home.html)
* [中文官网API](http://www.requirejs.cn/docs/api.html)

## 初识requirejs
随着网站功能逐渐丰富，网页中的js也变得越来越复杂和臃肿，原有通过`script`标签来导入一个个的js文件这种方式已经不能满足现在互联网开发模式，我们需要团队协作、模块复用、单元测试等等一系列复杂的需求。

`RequireJS`是一个非常小巧的`JavaScript`模块载入框架，是`AMD`规范最好的实现者之一。最新版本的`RequireJS`压缩后只有14K，堪称非常轻量。它还同时可以和其他的框架协同工作，使用`RequireJS`必将使您的前端代码质量得以提升。

### requirejs好处

官方对requirejs的描述：

>RequireJS is a JavaScript file and module loader. It is optimized for 
>in-browser use, but it can be used in other JavaScript environments, like 
>Rhino and Node. Using a modular script loader like RequireJS will improve the 
>speed and quality of your code.

翻译过来大致意思：
在浏览器中可以作为js文件的模块加载器，也可以用在`Node`和`Rhino`环境，balabala...。这段话描述了`requirejs`的基本功能"模块化加载"。

什么是模块化加载？我们要从之后的篇幅中一一解释
先来看一段常见的场景，通过示例讲解如何运用`requirejs`

**正常编写方式**

`index.html`
```html
<!DOCTYPE html>
<html>
    <head>
        <script type="text/javascript" src="a.js"></script>
    </head>
    <body>
      <span>body</span>
    </body>
</html>
```

`a.js`
```javascript
function fun1(){
  alert("it works");
}

fun1();
```
可能更喜欢这样
```javascript
(function(){
    function fun1(){
      alert("it works");
    }

    fun1();
})()
```
第二种方法使用了块作用域来申明function防止污染全局变量，本质还是一样的，当运行上面两种例子时不知道你是否注意到，alert执行的时候，html内容是一片空白的，即`<span>`body`</span>`并未被显示，当点击确定后，才出现，这就是JS阻塞浏览器渲染导致的结果。

**requirejs写法**

当然首先要到requirejs的网站去[下载js](http://www.requirejs.cn/docs/download.html)

`index.html`
```html
<!DOCTYPE html>
<html>
    <head>
        <script type="text/javascript" src="require.js"></script>
        <script type="text/javascript">
            require(["a"]);
        </script>
    </head>
    <body>
      <span>body</span>
    </body>
</html>
```

`a.js`
```javascript
define(function(){
    function fun1(){
      alert("it works");
    }

    fun1();
})
```

浏览器提示了"it works"，说明运行正确，但是有一点不一样，这次浏览器并不是一片空白，body已经出现在页面中。

目前为止可以知道requirejs具有如下优点：

1. 防止js加载阻塞页面渲染
2. 使用程序调用的方式加载js，防出现如下丑陋的场景

```html
<script type="text/javascript" src="a.js"></script>
<script type="text/javascript" src="b.js"></script>
<script type="text/javascript" src="c.js"></script>
<script type="text/javascript" src="d.js"></script>
<script type="text/javascript" src="e.js"></script>
<script type="text/javascript" src="f.js"></script>
<script type="text/javascript" src="g.js"></script>
<script type="text/javascript" src="h.js"></script>
<script type="text/javascript" src="i.js"></script>
<script type="text/javascript" src="j.js"></script>
```

## requirejs基本知识

上面我们以非常简单的方式引入了requirejs，这一篇将讲述一下requirejs中的一些基本知识，包括API使用方式等。

### 基本API
requirejs会定义三个变量：`define`,`require`,`requirejs`，其中`require === requirejs`，一般使用`require`更简短

* define 从名字就可以看出这个api是用来定义一个模块
* require 加载依赖模块，并执行加载完后的回调函数

前一节中的a.js：
```javascript
define(function(){
    function fun1(){
      alert("it works");
    }

    fun1();
})
```
通过define函数定义了一个模块，然后再页面中使用：
```javascript
require(["js/a"]);
```

来加载该模块(注意require中的依赖是一个数组，即使只有一个依赖，你也必须使用数组来定义，require API的第二个参数是callback，一个function，是用来处理加载完毕后的逻辑，如：

```javascript
require(["js/a"],function(){
    alert("load finished");
})
```

### 定义模块
define方法用于定义模块，RequireJS要求每个模块放在一个单独的文件里。

按照是否依赖其他模块，可以分成两种情况讨论。第一种情况是定义独立模块，即所定义的模块不依赖其他模块；第二种情况是定义非独立模块，即所定义的模块依赖于其他模块。

1.独立模块

如果被定义的模块是一个独立模块，不需要依赖任何其他模块，可以直接用define方法生成。
```javascript
define({
    method1: function() {},
    method2: function() {},
});
```
上面代码生成了一个拥有method1、method2两个方法的模块。

另一种等价的写法是，把对象写成一个函数，该函数的返回值就是输出的模块。
```javascript
define(function () {
    return {
        method1: function() {},
        method2: function() {},
    };
});
```
后一种写法的自由度更高一点，可以在函数体内写一些模块初始化代码。

值得指出的是，define定义的模块可以返回任何值，不限于对象。

2.非独立模块

如果被定义的模块需要依赖其他模块，则define方法必须采用下面的格式。

```javascript
define(['module1', 'module2'], function(m1, m2) {
   ...
});
```
define方法的第一个参数是一个数组，它的成员是当前模块所依赖的模块。比如，['module1', 'module2']表示我们定义的这个新模块依赖于module1模块和module2模块，只有先加载这两个模块，新模块才能正常运行。一般情况下，module1模块和module2模块指的是，当前目录下的module1.js文件和module2.js文件，等同于写成['./module1', './module2']。

define方法的第二个参数是一个函数，当前面数组的所有成员加载成功后，它将被调用。它的参数与数组的成员一一对应，比如function(m1, m2)就表示，这个函数的第一个参数m1对应module1模块，第二个参数m2对应module2模块。这个函数必须返回一个对象，供其他模块调用。
```javascript
define(['module1', 'module2'], function(m1, m2) {

    return {
        method: function() {
            m1.methodA();
            m2.methodB();
        }
    };

});
```
上面代码表示新模块返回一个对象，该对象的method方法就是外部调用的接口，method方法内部调用了m1模块的methodA方法和m2模块的methodB方法。

需要注意的是，回调函数必须返回一个对象，这个对象就是你定义的模块。

如果依赖的模块很多，参数与模块一一对应的写法非常麻烦。
```javascript
define(
    [  'dep1', 'dep2', 'dep3', 'dep4', 'dep5', 'dep6', 'dep7', 'dep8'],
    function(dep1,   dep2,   dep3,   dep4,   dep5,   dep6,   dep7,   dep8){
        ...
    }
);
```
为了避免像上面代码那样繁琐的写法，RequireJS提供一种更简单的写法。
```javascript
define(
    function (require) {
        var dep1 = require('dep1'),
            dep2 = require('dep2'),
            dep3 = require('dep3'),
            dep4 = require('dep4'),
            dep5 = require('dep5'),
            dep6 = require('dep6'),
            dep7 = require('dep7'),
            dep8 = require('dep8');

            ...
    }

});
```
下面是一个define实际运用的例子。
```javascript
define(['math', 'graph'], 
    function ( math, graph ) {
        return {
            plot: function(x, y){
                return graph.drawPie(math.randomGrid(x,y));
            }
        }
    };
);
```
上面代码定义的模块依赖math和graph两个库，然后返回一个具有plot接口的对象。

### 加载文件
之前的例子中加载模块都是本地js，但是大部分情况下网页需要加载的JS可能来自本地服务器、其他网站或CDN，这样就不能通过这种方式来加载了，我们以加载一个jquery库为例：

```javascript
require.config({
    paths : {
        "jquery" : ["http://libs.baidu.com/jquery/2.0.3/jquery"]   
    }
})
require(["jquery","js/a"],function($){
    $(function(){
        alert("load finished");  
    })
})
```

这边涉及了require.config，require.config是用来配置模块加载位置，简单点说就是给模块起一个更短更好记的名字，比如将百度的jquery库地址标记为jquery，这样在require时只需要写["jquery"]就可以加载该js，本地的js我们也可以这样配置：

```javascript
require.config({
    paths : {
        "jquery" : ["http://libs.baidu.com/jquery/2.0.3/jquery"],
        "a" : "js/a"   
    }
})
require(["jquery","a"],function($){
    $(function(){
        alert("load finished");  
    })
})
```

通过paths的配置会使我们的模块名字更精炼，paths还有一个重要的功能，就是可以配置多个路径，如果远程cdn库没有加载成功，可以加载本地的库，如：

```javascript
require.config({
    paths : {
        "jquery" : ["http://libs.baidu.com/jquery/2.0.3/jquery", "js/jquery"],
        "a" : "js/a"   
    }
})
require(["jquery","a"],function($){
    $(function(){
        alert("load finished");  
    })
})
```

这样配置后，当百度的jquery没有加载成功后，会加载本地js目录下的jquery

由上注意两点：

1. 在使用requirejs时，加载模块时不用写.js后缀的
2. 上面例子中的callback函数中发现有$参数，这个就是依赖的jquery模块的输出变量，如果你依赖多个模块，可以依次写入多个参数来使用：
```javascript
require(["jquery","underscore"],function($, _){
    $(function(){
        _.each([1,2,3],alert);
    })
})
```
如果某个模块不输出变量值，则没有，所以尽量将输出的模块写在前面，防止位置错乱引发误解

### 全局配置

上面的例子中重复出现了require.config配置，如果每个页面中都加入配置，必然显得十分不雅，requirejs提供了一种叫"主数据"的功能，我们首先创建一个`main.js`：
```javascript
require.config({
    paths : {
        "jquery" : ["http://libs.baidu.com/jquery/2.0.3/jquery", "js/jquery"],
        "a" : "js/a"   
    }
})
```
然后再页面中使用下面的方式来使用requirejs：
```html
<script data-main="js/main" src="js/require.js"></script>
```
**解释：**加载requirejs脚本的script标签加入了data-main属性，这个属性指定的js将在加载完reuqire.js后处理，我们把require.config的配置加入到data-main后，就可以使每一个页面都使用这个配置，然后页面中就可以直接使用require来加载所有的短模块名。

data-main还有一个重要的功能，当script标签指定data-main属性时，require会默认的将data-main指定的js为根路径，是什么意思呢？如上面的data-main="js/main"设定后，我们在使用require(['jquery'])后(不配置jquery的paths)，require会自动加载js/jquery.js这个文件，而不是jquery.js，相当于默认配置了：
```javascript
require.config({
    baseUrl : "js"
})
```

### 第三方模块

通过require加载的模块一般都需要符合AMD规范即使用define来申明模块，但是部分时候需要加载非AMD规范的js，这时候就需要用到另一个功能：shim，shim解释起来也比较难理解，shim直接翻译为"垫"，看看下面3种用法

1.非AMD模块输出，将非标准的AMD模块"垫"成可用的模块，例如：在老版本的jquery中，是没有继承AMD规范的，所以不能直接require["jquery"],这时候就需要shim，比如我要是用underscore类库，但是他并没有实现AMD规范，那我们可以这样配置
```javascript
require.config({
    shim: {
        "underscore" : {
            exports : "_"
        }
    }
})
```
这样配置后，我们就可以在其他模块中引用underscore模块：
```javascript
require(["underscore"], function(_){
    _.each([1,2,3], alert);
})
```
**解释：** exports 相当“浏览器全局变量注入”，即类似你按普通引入js暴露给全局的那个变量，比如老版本jQuery，可以按照下面配置
```javascript
require.config({
    shim: {
        "jquery" : {
            exports: 'jQuery'
        }
    }
})
```
或者
```javascript
require.config({
    shim: {
        "jquery" : {
            exports: '$'
        }
    }
})
```
2.非AMD模块依赖, 例如backbone依赖underscore
```javascript
require.config({
    paths: {
        "backbone": "vendor/backbone",
        "underscore": "vendor/underscore"
    },
    shim: {
        "backbone": {
            deps: [ "underscore" ],
            exports: "Backbone"
        },
        "underscore": {
            exports: "_"
        }
    }
});
```
3.那些仅作为jQuery或Backbone的插件存在而不导出任何模块变量的"模块"们，shim配置可简单设置为依赖数组：
```javascript
requirejs.config({
    shim: {
        'jquery.colorize': ['jquery'],
        'jquery.scroll': ['jquery'],
        'backbone.layoutmanager': ['backbone']
    }
});
```
更建议的写法：
```javascript
requirejs.config({
    shim: {
        'jquery.colorize': {
            deps: ['jquery'],
            exports: 'jQuery.fn.colorize'
        },
        'jquery.scroll': {
            deps: ['jquery'],
            exports: 'jQuery.fn.scroll'
        },
        'backbone.layoutmanager': {
            deps: ['backbone']
            exports: 'Backbone.LayoutManager'
        }
    }
});
```
配置完使用：
```javascript
require.config(["jquery", "jquery.colorize"], function($){
    $(function(){
        //....
    })
})
```

### requirejs 插件
RequireJS允许使用插件，加载各种格式的数据。下面简单介绍常用的一种（完整参考[官方API](http://www.requirejs.cn/docs/api.html)）

下面是插入文本数据所使用的text插件的例子
```javascript
define([
    'backbone',
    'text!templates.html'
], function( Backbone, template ){
   // ...
});
```
上面代码加载的第一个模块是backbone，第二个模块则是一个文本，用'text!'表示。该文本作为字符串，存放在回调函数的template变量中。

### 优化器r.js
RequireJS提供一个基于node.js的命令行工具r.js，用来压缩多个js文件。它的主要作用是将多个模块文件压缩合并成一个脚本文件，以减少网页的HTTP请求数。

第一步是安装r.js（假设已经安装了node.js）。
```
npm install -g requirejs
```
然后，使用的时候，直接在命令行键入以下格式的命令。
```
node r.js -o <arguments>
```
<argument>表示命令运行时，所需要的一系列参数，比如像下面这样：
```
node r.js -o baseUrl=. name=main out=main-built.js
```
除了直接在命令行提供参数设置，也可以将参数写入一个文件，假定文件名为build.js。
```javascript
({
    baseUrl: ".",
    name: "main",
    out: "main-built.js"
})
```
然后，在命令行下用r.js运行这个参数文件，就OK了，不需要其他步骤了。
```
node r.js -o build.js
```
下面是一个参数文件的范例，假定位置就在根目录下，文件名为build.js。
```javascript
({
    appDir: './',
    baseUrl: './js',
    dir: './dist',
    modules: [
        {
            name: 'main'
        }
    ],
    fileExclusionRegExp: /^(r|build)\.js$/,
    optimizeCss: 'standard',
    removeCombined: true,
    paths: {
        jquery: 'lib/jquery',
        underscore: 'lib/underscore',
        backbone: 'lib/backbone/backbone',
        backboneLocalstorage: 'lib/backbone/backbone.localStorage',
        text: 'lib/require/text'
    },
    shim: {
        underscore: {
            exports: '_'
        },
        backbone: {
            deps: [
                'underscore',
                'jquery'
            ],
            exports: 'Backbone'
        },
        backboneLocalstorage: {
            deps: ['backbone'],
            exports: 'Store'
        }
    }
})
```
上面代码将多个模块压缩合并成一个main.js。

参数文件的主要成员解释如下：
>* appDir：项目目录，相对于参数文件的位置。
>* baseUrl：js文件的位置。
>* dir：输出目录。
>* modules：一个包含对象的数组，每个对象就是一个要被优化的模块。
>* fileExclusionRegExp：凡是匹配这个正则表达式的文件名，
>都不会被拷贝到输出目录。 
>* optimizeCss: 自动压缩CSS文件，可取的值包括“none”, “standard”, “standard.
>keepLines”, “standard.keepComments”, “standard.keepComments.keepLines”。
>* removeCombined：如果为true，合并后的原文件将不保留在输出目录中。
>* paths：各个模块的相对路径，可以省略js后缀名。
>* shim：配置依赖性关系。如果某一个模块不是AMD模式定义的，就可以用shim属性指定
>模块的依赖性关系和输出值。
>* generateSourceMaps：是否要生成source map文件。

更详细的解释可以参考[参考官方文档](https://github.com/jrburke/r.js/blob/master/build/example.build.js)

运行优化命令后，可以前往dist目录查看优化后的文件。

下面是另一个build.js的例子:
```javascript
({
    mainConfigFile : "js/main.js",
    baseUrl: "js",
    removeCombined: true,
    findNestedDependencies: true,
    dir: "dist",
    modules: [
        {
            name: "main",
            exclude: [
                "infrastructure"
            ]
        },
        {
            name: "infrastructure"
        }
    ]
})
```

上面代码将模块文件压缩合并成两个文件，第一个是main.js（指定排除infrastructure.js），第二个则是infrastructure.js。

### 涉及文章链接

[1] [JS模块化工具requirejs教程(一)：初识requirejs](http://www.runoob.com/w3cnote/requirejs-tutorial-1.html)

[2] [JS模块化工具requirejs教程(二)：基本知识](http://www.runoob.com/w3cnote/requirejs-tutorial-2.html)

[3] [RequireJS和AMD规范](http://javascript.ruanyifeng.com/tool/requirejs.html)

[4] [require中文官网API](http://www.requirejs.cn/docs/api.html)

