# jQuery 将参数添加到 url 函数

> 原文：<https://www.sitepoint.com/jquery-add-param-url-function/>

这个 jQuery 实用函数**检查当前页面 url 中是否存在一个参数，如果不存在，它添加它**并返回完整的 URL。如果您需要使用 ajax 请求用新的表单数据更新数据库，并且只是想重定向到相同的 url，但使用更新标志来显示更新框，这可能会很有用。

![updated-ajax-box](img/68225294a59fc4818f3607970237c527.png "updated-ajax-box")


```
(function($,W,D)
{
    var JQUERY4U = {};

    JQUERY4U.UTIL =
    {
        /**
          * Add a parameter to url if doesn't already exist
          * @param param - the parameter to add
          * @param value - the value of the parameter
          * @return url - the url with the appended parameter
          */
        addParamToUrl: function(param, value)
        {
            //check if param exists
            var result = new RegExp(param + "=([^&]*)", "i").exec(W.location.search);
            result = result && result[1] || "";

            //added seperately to append ? before params
            var loc = W.location;
            var url = loc.protocol + '//' + loc.host + loc.pathname + loc.search;

            //param doesn't exist in url, add it
            if (result == '')
            {
                //doesn't have any params
                if (loc.search == '')
                {
                    url += "?" + param + '=' + value;
                }
                else
                {
                    url += "&" + param + '=' + value;
                }
            }

            //return the finished url
            return url;
        }
    }

    //example usage
    var updatedUrl = JQUERY4U.UTIL.addParamToUrl('updated', 'true');
    console.log(updatedUrl);
    //input: http://jquery4u.com/index.php
    //output: http://jquery4u.com/index.php?updated=true

})(jQuery, window, document);
```

## 分享这篇文章