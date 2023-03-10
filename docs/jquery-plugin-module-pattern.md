# 使用模块模式的基本 jQuery 插件

> 原文：<https://www.sitepoint.com/jquery-plugin-module-pattern/>

在这篇文章中，我将向你展示如何使用**模块模式**来创建一个基本的 jQuery 插件，它可以在不同的选项中重用。模块模式的主要焦点是保护您的选项和方法，并提供一个很好的公共 API 用于对象。它通过使用带有封装变量的对象文字语法来实现这一点，这些封装变量包含私有和公共对象名称空间。该插件是一个非常简单的**解释的例子，下面的演示**是给你创建一个健壮的 jQuery 插件的基础。

**相关帖子:**

*   [**jQuery 函数名称间距**](http://www.jquery4u.com/javascript/jquery-function-namespacing-plain-english/)
*   [**10 JavaScript 速记编码技巧**](http://www.jquery4u.com/javascript/shorthand-javascript-techniques/)

## 你管视频插件

这个插件的目的是创建 youtube 视频的不同实例，你可以为它们指定诸如标题和 url 之类的选项。

![basic-jquery-plugin](img/60b12244d5b9e30b2d44391adde127d3.png "basic-jquery-plugin")

### 让我们看一下代码…

插件中使用了 3 个主要变量:保存私有 API 的 priv、保存公共 API 的 plugin 和保存默认插件设置的 defaults。

```
var priv = {}, // private API
    Plugin = {}, // public API

    // Plugin defaults
    defaults = {
      id : '',
      name : '',
      url : ''
    };
```

存储在“默认值”中的默认插件设置会被新设置覆盖。记得美元。extend()函数将一个对象与另一个对象合并，所以这里 options 与默认值合并，新对象存储在 priv.options 中。

```
// Public initialization
Plugin.init = function(options)
{
    ...
    $.extend(priv.options, defaults, options);
    ...
}
```

这里我们可以使用“this.options”直接调用 Plugin priv 对象上的选项。

```
priv.options = {}; //private options

priv.method1 = function()
{
    console.log('Private method 1 called...');
    $('#videos').append('

# +this.options.name+'

');
    priv.method2(this.options);
};
```

这里可以实现插件的公共 API，因为我们返回插件对象，可以访问公共方法。

```
// Return the Public API (Plugin) we want to expose
return Plugin;
```

运行代码，我们可以清楚地看到，在 Firebug 中，对象上的选项被设置，私有/公共方法被正确调用。

![basic-jquery-plugin2](img/851477d88308211756b4fe2ab36a6c1b.png "basic-jquery-plugin2")

## 演示

## 完整的插件

```
/**
 *  A basic jQuery plugin using the Module Pattern
 * *
 *  @author      Sam Deering
 *  @copyright   Copyright (c) 2012 jQuery4u
 *  @license     http://jquery4u.com/license/
 *  @since       Version 1.0
 *
 */

!function(exports, $, undefined)
{
    var Plugin = function()
    {

        /*-------- PLUGIN VARS ------------------------------------------------------------*/

        var priv = {}, // private API

            Plugin = {}, // public API

            // Plugin defaults
            defaults = {
                id : '',
                name : '',
                url : ''
            };

        /*-------- PRIVATE METHODS --------------------------------------------------------*/

        priv.options = {}; //private options

        priv.method1 = function()
        {
            console.log('Private method 1 called...');
            $('#videos').append('

# +this.options.name+'

');
            priv.method2(this.options);
        };

        priv.method2 = function()
        {
            console.log('Private method 2 called...');
            $('#'+priv.options.id).append('

+this.options.url+'

'); // append title
            $('#'+priv.options.id).append(''); //append video
        };

        /*-------- PUBLIC METHODS ----------------------------------------------------------*/

        Plugin.method1 = function()
        {
            console.log('Public method 1 called...');
            console.log(Plugin);

            //options called in public methods must access through the priv obj
            console.dir(priv.options);
        };

        Plugin.method2 = function()
        {
            console.log('Public method 2 called...');
            console.log(Plugin);
        };

        // Public initialization
        Plugin.init = function(options)
        {
            console.log('new plugin initialization...');
            $.extend(priv.options, defaults, options);
            priv.method1();
            return Plugin;
        }

        // Return the Public API (Plugin) we want
        // to expose
        console.log('new plugin object created...');
        return Plugin;
    }

    exports.Plugin = Plugin;

}(this, jQuery);

jQuery(document).ready( function()
{
    console.log('doc rdy');

    // EXAMPLE OF CREATING PLUGIN OBJECTS WITH CUSTOM SETTINGS

    console.log('--------------------------------------------');

    var myPlugin1 = new Plugin;
    myPlugin1.init(
    {
        /* custom options */
        id : 'vid01',
        name : 'Video 1',
        url : 'http://www.youtube.com/embed/dXo0LextZTU?rel=0'
    });

    //call to public methods
    myPlugin1.method1();
    myPlugin1.method2();

    console.log('--------------------------------------------');

    var myPlugin2 = new Plugin;
    myPlugin2.init(
    {
        /* custom options */
        id : 'vid02',
        name : 'Video 2',
        url : 'http://www.youtube.com/embed/nPMAUW-4lNY?rel=0'
    });

    //call to public methods
    myPlugin2.method1();
    myPlugin2.method2();

    // console.log('--------------------------------------------');

});
```

## 分享这篇文章