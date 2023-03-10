# 快速浏览一下 backbone.js 源代码

> 原文：<https://www.sitepoint.com/quick-backbone-js-source-code/>

**钻研 backbone.js 源代码**【http://documentcloud.github.com/backbone/backbone.js】
T3

今天，我决定快速浏览一下 backbone.js 的源代码，看看这个了不起的 MVC 框架背后发生了什么。

**对之前版本 Backbone.js 0.5.3(最新版本为 Backbone.js 0.9.1)**

```
 ## 第 32 行:要求('下划线')。_;

// Require Underscore, if we're on the server, and it's not already present.
var _ = root._;
if (!_ && (typeof require !== 'undefined')) _ = require('underscore')._;
```

*   首先，root 引用全局 JavaScript 对象。
*   Require 用于加载顶级 JavaScript 文件或在模块内部动态获取依赖关系。
*   JavaScript 全局对象的进一步阅读:[https://developer . Mozilla . org/en/JavaScript/Reference/Global _ Objects](https://developer.mozilla.org/en/JavaScript/Reference/Global_Objects)
*   似乎 backbone.js 可以和非浏览器 JavaScript 后端语言一起使用，这些语言遵循 CommonJS 规范，比如 Node.js。
*   CommonJS 是一个服务器端 JavaSCript 框架。它检查 CommonJS 模块规范中是否存在 require。意思是，如果全局对象不包含 _，尝试要求下划线模块(如果定义了 require)并从那里获取 _。
*   node.js 文档中 require()的进一步阅读:[http://nodejs . org/docs/v 0 . 4 . 2/API/modules . html # loading _ from _ the _ require . paths _ Folders](http://nodejs.org/docs/v0.4.2/api/modules.html#loading_from_the_require.paths_Folders)
    在 CommonJS 中，下划线现在可能只需要与:var _ = require("下划线")一起使用。
*   现在我们有了一个完整的函数列表，都以下划线变量名(eg _)开始。size()，_。toArray()等)

## 第 35 行:$ = root.jQuery

```
// For Backbone's purposes, jQuery or Zepto owns the `$` variable.
var $ = root.jQuery || root.Zepto;
```

Zepto.js 非常像 jQuery 的精简版，除了函数名略有不同，比如 ajaxJSONP()和其他一些函数名。只有 10kb 内存，它的主要焦点是移动开发，这可以在[源代码](/scripts/zepto.js)中看到。

```
['swipe', 'swipeLeft', 'swipeRight', 'swipeUp', 'swipeDown', 'doubleTap', 'tap', 'longTap'].forEach(function(m){
  $.fn[m] = function(callback){ return this.bind(m, callback) }
});
```

## 132 行:骨干。模型

```
// Backbone.Model
// --------------

// Create a new model, with defined attributes. A client id (`cid`)
// is automatically generated and assigned for you.
Backbone.Model = function(attributes, options) {
  var defaults;
  attributes || (attributes = {});
  if (defaults = this.defaults) {
    if (_.isFunction(defaults)) defaults = defaults.call(this);
    attributes = _.extend({}, defaults, attributes);
  }
  this.attributes = {};
  this._escapedAttributes = {};
  this.cid = _.uniqueId('c');
  this.set(attributes, {silent : true});
  this._changed = false;
  this._previousAttributes = _.clone(this.attributes);
  if (options && options.collection) this.collection = options.collection;
  this.initialize(attributes, options);
};
```

这是核心模型原型对象，其中为模型设置了所有属性。

```
this.cid = _.uniqueId('c');
```

这里，它还使用 [_ 为 cid 属性生成一个惟一的 id。uniqueId()](http://documentcloud.github.com/underscore/#uniqueId) 将前缀作为参数的函数，在本例中，c so 将返回 c104、c201 等

要设置模型的默认值，您可以执行以下操作:

```
var Meal = Backbone.Model.extend({
  defaults: {
    "appetizer":  "caesar salad",
    "entree":     "ravioli",
    "dessert":    "cheesecake"
  }
});

alert("Dessert will be " + (new Meal).get('dessert'));
```

## 第 150 行:_。延伸(骨干。模型.原型

```
_.extend(Backbone.Model.prototype, Backbone.Events, {
    ...
    methods: initialize(), escape(), set(), get() etc...
    ...
```

这只是简单地将方法和事件对象添加到模型原型对象中，这样它就拥有了使用 [extend()](http://www.jquery4u.com/function-demos/extend/) 函数(由下划线. js 提供)的所有功能。

## 414 行:骨干。收藏品

```
// Backbone.Collection
// -------------------

// Provides a standard collection class for our sets of models, ordered
// or unordered. If a `comparator` is specified, the Collection will maintain
// its models in sort order, as they're added and removed.
Backbone.Collection = function(models, options) {
  options || (options = {});
  if (options.comparator) this.comparator = options.comparator;
  _.bindAll(this, '_onModelEvent', '_removeReference');
  this._reset();
  if (models) this.reset(models, {silent: true});
  this.initialize.apply(this, arguments);
};
```

## 656 行:骨干。路由器

```
// Backbone.Router
// -------------------

// Routers map faux-URLs to actions, and fire events when routes are
// matched. Creating a new one sets its `routes` hash, if not set statically.
Backbone.Router = function(options) {
  options || (options = {});
  if (options.routes) this.routes = options.routes;
  this._bindRoutes();
  this.initialize.apply(this, arguments);
};
```

## 735 行:骨干。历史

```
// Backbone.History
// ----------------

// Handles cross-browser history management, based on URL fragments. If the
// browser does not support `onhashchange`, falls back to polling.
Backbone.History = function() {
  this.handlers = [];
  _.bindAll(this, 'checkUrl');
};
```

## 879 行:骨干。视角

```
// Backbone.View
// -------------

// Creating a Backbone.View creates its initial element outside of the DOM,
// if an existing element is not provided...
Backbone.View = function(options) {
  this.cid = _.uniqueId('view');
  this._configure(options || {});
  this._ensureElement();
  this.delegateEvents();
  this.initialize.apply(this, arguments);
};
```

## 第 1038 行:主干网

```
// Backbone.sync
// -------------

// Override this function to change the manner in which Backbone persists
// models to the server. You will be passed the type of request, and the
// model in question. By default, uses makes a RESTful Ajax request
// to the model's `url()`. Some possible customizations could be:
//
// * Use `setTimeout` to batch rapid-fire updates into a single request.
// * Send up the models as XML instead of JSON.
// * Persist models via WebSockets instead of Ajax.
//
// Turn on `Backbone.emulateHTTP` in order to send `PUT` and `DELETE` requests
// as `POST`, with a `_method` parameter containing the true HTTP method,
// as well as all requests with the body as `application/x-www-form-urlencoded` instead of
// `application/json` with the model in a param named `model`.
// Useful when interfacing with server-side languages like **PHP** that make
// it difficult to read the body of `PUT` requests.
Backbone.sync = function(method, model, options) {
  var type = methodMap[method];
```

## 第 1137 行:抛出新错误(

```
// Throw an error when a URL is needed, and none is supplied.
var urlError = function() {
  throw new Error('A "url" property or function must be specified');
};
```

这是一个 helper 函数，抛出一个新的自定义 JavaScript 错误。就像这样，但将自定义消息。

```
try{
  document.body.filters[0].apply()
}
catch(e){
  alert(e.name + "n" + e.message)
}
```

## 第 1153 行:var escapeHTML = function(string)

```
// Helper function to escape a string for HTML rendering.
var escapeHTML = function(string) {
  return string.replace(/&(?!w+;|#d+;|#x[da-f]+;)/gi, '&amp;').replace(//g, '&gt;').replace(/"/g, '&quot;').replace(/'/g, '&#x27;').replace(///g,'&#x2F;');
};
```

使用 regex 替换的 escapeHTML 的 Helper 函数。

这只是对 backbone.js 的一个快速浏览，我相信你们中的一些人已经看得更近了，并且想知道你们的想法。留下评论。

## 分享这篇文章