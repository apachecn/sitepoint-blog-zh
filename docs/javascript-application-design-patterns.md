# Javascript 应用程序设计模式，举例来说

> 原文：<https://www.sitepoint.com/javascript-application-design-patterns/>

Javascript 开发被像 Backbone、Spine 这样的库所主导，当然还有 jQuery(惊喜！).然而，问题不在于使用什么库，而在于如何使用它。

理解一些框架如何为看似不确定的 javascript 提供支架和架构是我们 javascript 技巧库中的一个关键组成部分。有了这些知识，通过让开发人员在逻辑上分离关注点和依赖项，从本质上打开了创建真正伟大的沉浸式 web 应用程序的最后一扇门。

在本文中，我们将介绍一些基本的 javascript 模式:

*   生活模块
*   Dojo 模块
*   jQuery 模块
*   AMD 模块
*   CommonJS 模块
*   门面模式
*   中介模块

### 模块模式—立即调用的表达式函数(IIEF)使用执行上下文来创建隐私。

```
var module = (function(){
    /**
     * private variables are declared only inside the module
     */
    var basket = [];

    /**
     * public variables are declared in the returned object
     */
    return {
        add: function(value){ ... },
        count: function() { ... }
    };
}());

module.add('a');
module.add('b');
module.add('c');

var total = module.count();
```

### 模块模式——Dojo

```
/**
 * traditional method
 */
var store = window.store || {};
store.basket = store.basket || {};

/**
 * dojo method
 */
dojo.setObject("store.basket.object", function(){
    var basket = [];
    function privateFunc(){ ... }
    return {
        add: function(value){ ... },
        count: function(){ ... }
    }
});
```

### 模块模式–jQuery

```
function library(module) {
    $(function(){
        if (module.init) {
            module.init();
        }
    });
    return module;
}

var myLibrary = library(
    function(){
        return {
            init: function() {
                /*implementation*/
            }
        };
    }()
);
```

### 更好——异步模块定义，或 AMD

```
/**
 * AMD: define()
 * define a signature with define(id /*optional*/, [dependencies], /*factory module instantiation of fn*/);
 */

define(
    /*module id*/
    'myModule',

    /*dependencies*/
    ['foo', 'bar;, 'baz'],

    /*definition for the module export*/
    function(foo, bar, baz){

        /*module object*/
        var module = {};

        /*module methods go here*/
        module.hello = foo.getSomething();
        module.goodbye = bar.getSomething();

        /*return the defined module object*/
        return module;
    }
);

/**
 * AMD: require()
 * load top-level code for JS files or inside modules for dynamically fetching dependencies
 */

/* top-level: the module exports (one, two) are passed as function arguments ot the object */
require(['one', 'two'], function(one, two){
    ...
});

/*inside: the complete example*/
define('three', ['one', 'two'], function(one, two){
    /**
     * require('string') can be used inside the function to get the module export
     * of a module that has already been fetched and evaluated
     */

    var temp = require('one');

    /*this will fail*/
    var four = require('four');

    /* return a value to define the module export */
    return function(){ ... };
});
```

### 最佳:CommonJS——广泛采用的服务器端格式

```
/**
 * basically contains two parts: an exports object that contains the objects a module wishes to expose
 * and a require function that modules can use to import the exports of other modules
 */

/* here we achieve compatibility with AMD and CommonJS using some boilerplate around the CommonJS module format*/
(function(define){
    define(function(require,exports){
         /*module contents*/
         var dep1 = require("foo");
         var dep2 = require("bar");
         exports.hello = function(){...};
         exports.world = function(){...};
    });
})( typeof define=="function" ? define : function(factory){ factory(require, exports) });

** Harmonious revelations: ES Harmony, the the successor to ECMAScript 5

/**
 * 1\. static scoping
 * 2\. simple
 * 3\. reusable modules
 */

// Basic module
module SafeWidget {
    import alert from Widget;
    var _private ="someValue";

    // exports
    export var document = {
        write: function(txt) {
            alert('Out of luck, buck');
        },
        ...
    };
}

// Remote module
module JSONTest from 'http://json.org/modules/json2.js';
```

### 模块的替代模式

模块经常在 MVC 应用程序中使用..但是也有其他模式可以让构建大型应用程序变得更容易。￼Remember 认为，jQuery 在大型应用程序中的作用通常比大多数人想象的要小。第二次。MD 日历和预订代码，以及聊天门户，没有 jQuery 也能轻松运行。

### 门面——对大量代码的高级接口，隐藏了大部分令人讨厌的复杂性

> “当你树立一个门面时，你通常是在创造一个隐藏着不同现实的外表。可以把它看作是简化呈现给其他开发者的 API”
> 
> **基本 Javascript 设计模式**

1.  通过有限的、更简单的 API 简化使用
2.  隐藏了库的内部工作方式，允许实现变得不那么重要
3.  让您在幕后更有创造力
4.  为应用程序安全提供了许多功能行为

```
var module = (function() {
    var _private = {
        i:5,
        get : function() {
            console.log('current value:' + this.i);
        },
        set : function( val ) {
            this.i = val;
        },
        run : function() {
            console.log('running');
        },
        jump: function(){
            console.log('jumping');
        }
    };

    /**
     * this part includes code imported above and provides an API to the returning module
     */
    return {
        facade : function( args ) {
            _private.set(args.val);
            _private.get();
            if ( args.run ) {
                _private.run();
            }
        }
    };
}());

module.facade({run: true, val:10}); //outputs current value: 10, running
```

### 中介器——通过为模块可以订阅的事件建立接口来促进松散耦合:

1.  允许模块广播或监听通知，而不用担心系统或繁琐的回调链。
2.  通知可以由任意数量的模块同时异步处理。
3.  由于代码的松散耦合特性，随时添加或删除特性要容易得多。

```
var mediator = (function(){
    var subscribe = function(channel, fn){
        if (!mediator.channels)mediator.channels = [];
        mediator.channels.push({ context: this, callback:fn });
        return this;
    },
    publish = function(channel){
        if (!mediator.channels) return false;
        var args = Array.prototype.slice.call(arguments, 1);
        for (var i = 0, l = mediator.channels.length; i&lt;l; i++) {
            var subscription = mediator.channels[i];
            subscription.callback.apply(subscription.context,args);
        }
        return this;
    };
    return {
        channels: {},
        publish: publish,
        subscribe: subscribe,
        installTo: function(obj){
            obj.subscribe = subscribe;
            obj.publish = publish;
        }
    };
}());
```

目前就这些了！请记住，组织代码和设计应用程序的方式可以将复杂的程序简化到近乎自然的程度。在这些方法上磨练您的技能，您将掌握真正成长为开发人员所需的技术！

## 分享这篇文章