# jQuery AJAX 实用程序助手函数

> 原文：<https://www.sitepoint.com/jquery-ajax-utility-helper-function/>

早上好，jQuery 的粉丝们，今天我和你们分享一个我编写的 ajax 助手函数,它可以接受一些基本的 ajax 设置，或者将数据本地存储在 JavaScript 对象上，或者在 ajax 成功时动态运行 JavaScript 回调函数。使用 ajax 实用函数将**节省你在多个文件中编写 ajax 函数的时间**。它还**将你的 ajax 定义调用保存在一个地方**，以防你需要 ajax 的特定需求，比如添加加载图像或特定的错误处理程序。

相关帖子:

*   **6 个 jQuery Ajax 实例**
*   [**jQuery AJAX GET vs POST 的区别**](http://www.jquery4u.com/ajax/key-differences-post/)

## AJAX 实用程序助手函数

这是 ajax helper 函数，可以包含在您的 JavaScript 实用程序对象中。

```
/**
 *  JQUERY4U.COM
 *
 *  Provides utility functions to other JavaScript Objects.
 *
 *  @author      Sam Deering
 *  @copyright   Copyright (c) 2012 JQUERY4U
 *  @license     http://jquery4u.com/license/
 *  @since       Version 1.0
 *  @filesource  js/jquery4u.util.js
 *
 */

(function($,W,D)
{
    W.JQUERY4U = W.JQUERY4U || {};

    W.JQUERY4U.UTIL =
    {
        /**
          * AJAX helper function which can be used dynamically to store data or run a function upon success.
          * @param callback - 'store' to store data locally or 'run' to run a callback function.
          * @param callbackAction - where to store the data.
          * @param subnamespace - what namespace to store the data/run the function.
          */
        ajax: function(type, url, query, async, returnType, data, callback, callbackAction, subnamespace)
        {
            $.ajax(
            {
                type: type,
                url: url + query,
                async: async,
                dataType: returnType,
                data: data,
                success: function(data)
                {
                    switch(callback)
                    {
                    case 'store':
                      JQUERY4U[subnamespace]["data"][callbackAction] = data; //store data
                      break;
                    case 'run':
                      JQUERY4U[subnamespace][callbackAction](data); //run function with data
                      break;
                    default:
                      return true;
                    }
                },
                error: function(xhr, textStatus, errorThrown)
                {
                    alert('ajax loading error...');
                    return false;
                }
            });
         }
    }

})(jQuery,window,document);
```

## 如何使用 AJAX 实用函数

这就是使用 ajax 实用函数的方法:

1)使用 ajax 获取数据，并将其存储在您的 JS 对象上
2)使用 ajax 获取数据，并运行一个回调函数，该函数会传递数据

```
/**
 *  JQUERY4U.COM
 *
 *  Example JavsScript object to make use of the AJAX utility function.
 *
 *  @author      Sam Deering
 *  @copyright   Copyright (c) 2012 JQUERY4U
 *  @license     http://jquery4u.com/license/
 *  @since       Version 1.0
 *  @filesource  js/jquery4u.module.js
 *
 */

(function($,W,D)
{
    W.JQUERY4U = W.JQUERY4U || {};

    W.JQUERY4U.MODULE =
    {
        data:
        {
            ajaxData: '' //used to store ajax data
        },

        init: function()
        {
            this.getData(); //store data test
            this.runFunc(); //run function test
        },

        //example function to call ajax and then save data after ajax success
        getData: function()
        {
            JQUERY4U.UTIL.ajax('GET', 'jquery4u.com/data.php', '?param=value&param2=value2', false, 'HTML', '', 'store', 'ajaxData', 'MODULE');
            //data from ajax is stored in JQUERY4U.MODULE.data.ajaxData after ajax success
        },

        //example function to call ajax and then run a function after ajax success
        runFunc: function()
        {
            var data = ['some data passed', 'to server side script'];
            JQUERY4U.UTIL.ajax('POST', 'jquery4u.com/data.php', '', true, 'HTML', data, 'run', 'ajaxCallbackFunction', 'MODULE');
            //JQUERY4U.MODULE.ajaxCallbackFunction is called after ajax success
        },

        //function that is called after ajax success
        ajaxCallbackFunction: function(data)
        {
            //do something with returned data
        }
    }

    $(D).ready(function() {
        JQUERY4U.MODULE.init();
    });

})(jQuery,window,document);
```

这个 ajax 功能完全工作，但它的工作正在进行中，我在这里和那里调整它，所以我会尽量保持代码更新。

## 分享这篇文章