# 好的 jQuery 插件模板

> 原文：<https://www.sitepoint.com/good-jquery-plugin-template/>

这是您下一个 jQuery 插件的良好起点。我已经创建了一个框架示例[https://jsfiddle.net/jquery4u/kp8bS/](https://jsfiddle.net/jquery4u/kp8bS/)供你在 jsfiddle 上查看，可能对你也有帮助。

## jQuery 插件模板代码

```
/*!
  jQuery [name] plugin
  @name jquery.[name].js
  @author [author name] ([author email] or @[author twitter])
  @version 1.0
  @date 01/01/2013
  @category jQuery Plugin
  @copyright (c) 2013 [company/person name] ([company/person website])
  @license Licensed under the MIT (http://www.opensource.org/licenses/mit-license.php) license.
*/
(function($){

  var myPlugin, defaultOptions, __bind;

  __bind = function(fn, me) {
    return function() {
      return fn.apply(me, arguments);
    };
  };

  // Plugin default options.
  defaultOptions = {
    myvar1: 1,
    myvar2: 2,
    myvar3: 3
    resizeDelay: 50
    //etc...
  };

  myPlugin = (function(options) {

    function myPlugin(handler, options) {
      this.handler = handler;

      // plugin variables.
      this.resizeTimer = null;

      // Extend default options.
      $.extend(true, this, defaultOptions, options);

      // Bind methods.
      this.update = __bind(this.update, this);
      this.onResize = __bind(this.onResize, this);
      this.init = __bind(this.init, this);
      this.clear = __bind(this.clear, this);

      // Listen to resize event if requested.
      if (this.autoResize) {
        $(window).bind('resize.myPlugin', this.onResize);
      };
    };

    // Method for updating the plugins options.
    myPlugin.prototype.update = function(options) {
      $.extend(true, this, options);
    };

    // This timer ensures that layout is not continuously called as window is being dragged.
    myPlugin.prototype.onResize = function() {
      clearTimeout(this.resizeTimer);
      this.resizeTimer = setTimeout(this.resizeFunc, this.resizeDelay);
    };

    // Example API function.
    myPlugin.prototype.resizeFunc = function() {
        //...do something when window is resized
    };

    // Main method.
    myPlugin.prototype.init = function() {
        //...do something to initialise plugin
    };

    // Clear event listeners and time outs.
    myPlugin.prototype.clear = function() {
      clearTimeout(this.resizeTimer);
      $(window).unbind('resize.myPlugin', this.onResize);
    };

    return myPlugin;
  })();

  $.fn.myPlugin = function(options) {
    // Create a myPlugin instance if not available.
    if (!this.myPluginInstance) {
      this.myPluginInstance = new myPlugin(this, options || {});
    } else {
      this.myPluginInstance.update(options || {});
    }

    // Init plugin.
    this.myPluginInstance.init();

    // Display items (if hidden) and return jQuery object to maintain chainability.
    return this.show();
  };
})(jQuery);
```

参考文献:
[https://raw . github . com/GBKS/wook mark-jQuery/master/jQuery . wook mark . js](https://raw.github.com/GBKS/Wookmark-jQuery/master/jquery.wookmark.js)
[https://github.com/GBKS/Wookmark-jQuery](https://github.com/GBKS/Wookmark-jQuery)

## 分享这篇文章