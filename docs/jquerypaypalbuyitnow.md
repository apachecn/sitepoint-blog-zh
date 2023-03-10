# jQuery Paypal 立即购买插件

> 原文:# t0]https://www . sitepoint . com/jquerypaypalbuyitnow/

[![paypal-plugin](img/f983d2f5e3478c55da0d5574913eb21a.png "paypal-plugin")](http://samdeering.com/jquerypaypalbuyitnow/)

我只是快速编写了一个 jQuery 插件的初始版本，以动态创建一个 Paypal 立即购买按钮。这只是 1.0 版，只是为了快速取胜，我很快会添加更多的功能。有一个[演示](http://samdeering.com/jquerypaypalbuyitnow/)，我已经将该项目提交给 [GitHub](https://github.com/sdeering/jquerypaypalbuyitnow) 。欢迎改进。我已经为插件使用了基本的 jQuery 名称空间模式，我可能会在下一版本中升级到稍微重一点的模式。敬请关注。

![paypal-checkout](img/5bd9ab94f66d3a5974af183be07a7895.png "paypal-checkout")

## 使用

这很容易使用，你只需在下拉框中指定你的 paypal 电子邮件地址，项目名称和你想要的选项。

```
;(function($)
{

     $(document).ready(function()
     {
        var options =
        {
            formId: 'myPaypalButton',
            itemName: 'Buy a T-Shirt',
            email: 'paypalemailadress@gmail.com',
            basePrice: 59.00,
            options:
            {
                sizes:
                {
                    name: 'Please select your size:',
                    type: 'select',
                    values:
                    {
                        'Size - Small' : 60.00,
                        'Size - Medium'   : 69.00,
                        'Size - Large'   : 79.99,
                        'Size - Extra Large'   : 89.00,
                        'Size - Too Big'   : 199.95
                    }
                }

            },
            countryCode: 'au',  //au, uk
            currencySymbol: '$',
            currencyCode: 'AUD'
        };

        $('#demo1').paypalBuyButton(options);

     });

})(jQuery);
```

## 插件代码

这是插件的开发版，缩小版只有 2kb！

```
/*
    jQuery Paypal Buy It Now Plugin v1.0
    Copyright (c) 2012 Sam Deering, jquery4u.com

    Permission is hereby granted, free of charge, to any person obtaining
    a copy of this software and associated documentation files (the
    "Software"), to deal in the Software without restriction, including
    without limitation the rights to use, copy, modify, merge, publish,
    distribute, sublicense, and/or sell copies of the Software, and to
    permit persons to whom the Software is furnished to do so, subject to
    the following conditions:

    The above copyright notice and this permission notice shall be
    included in all copies or substantial portions of the Software.

    THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND,
    EXPRESS OR IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF
    MERCHANTABILITY, FITNESS FOR A PARTICULAR PURPOSE AND
    NONINFRINGEMENT. IN NO EVENT SHALL THE AUTHORS OR COPYRIGHT HOLDERS BE
    LIABLE FOR ANY CLAIM, DAMAGES OR OTHER LIABILITY, WHETHER IN AN ACTION
    OF CONTRACT, TORT OR OTHERWISE, ARISING FROM, OUT OF OR IN CONNECTION
    WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE SOFTWARE.

    DEMO: http://samdeering.com/jquerypaypalbuyitnow/
    DOWNLOAD: https://github.com/sdeering/jquerypaypalbuyitnow
    ARTICLE: http://jquery4u.com/plugins/jquerypaypalbuyitnow/

*/
;(function($){
    $.fn.extend({
        paypalBuyButton: function(options)
        {
            this.defaultOptions = {};
            var settings = $.extend({}, this.defaultOptions, options);

            //construct form
            var formHTML = '

<form id="'+settings.formId+'" name="_xclick" action="https://www.paypal.com/'+settings.countryCode+'/cgi-bin/webscr" method="post">
            <input type="hidden" name="cmd" value="_xclick">
            <input type="hidden" name="business" value="'+settings.email+'">
            <input type="hidden" name="currency_code" value="'+settings.currencyCode+'">
            <input type="hidden" name="item_name" value="'+settings.itemName+'">
            <input type="hidden" name="amount" value="'+settings.basePrice+'">';

            //add options
            $.each(settings.options, function(i, option)
            {
                switch(option.type)
                {
                    case "select":
                        formHTML += '<select name="select-'+i+'">';
                        $.each(option.values, function(key, val)
                        {
                            formHTML += '<option value="'+val+'">'+key+' (/option>+val+')</option>';
                        });
                        formHTML += '</select>';

                        //select box change handler
                        $('select[name="select-'+i+'"]').live('change', function(e)
                        {
                            console.log($(this).val());
                            $('#'+settings.formId+' input[name="amount"]').val($(this).val());
                        });
                        break;
                    default:
                }
            });

            formHTML += '

';

            formHTML += '

<input type="image" border="0" alt="PayPal — The safer, easier way to pay online." name="submit" src="https://www.paypalobjects.com/en_AU/i/btn/btn_buynowCC_LG.gif" style="width: 107px; height: 47px;">

</form>

';

            //add form to container
            $(this).html(formHTML);

            return this.each(function()
            {
                var $this = $(this);
            });
        }
    });
})(jQuery);
```

[查看演示](http://samdeering.com/jquerypaypalbuyitnow/) [查看 GitHub 项目](https://github.com/sdeering/jquerypaypalbuyitnow) [缩小版(2kb)](http://samdeering.com/jquerypaypalbuyitnow/js/jquery-paypal-buyitnow-plugin-1.0.min.js)

## 分享这篇文章