# jQuery 捕获复制、粘贴和剪切事件

> 原文：<https://www.sitepoint.com/jquery-capture-copy-paste-cut-events/>

这就是你如何捕捉到有人复制、剪切或粘贴到一个输入文本区域。

## 演示

从/向下面的文本区域复制、剪切或粘贴文本。

<form action="#"><input id="textinput" type="text" size="50" value="Copy, paste or cut message here.">
</form>

## 代码

```
(function($)
{
    $(document).ready(function() {

        $("#textinput").bind({
            copy : function(){
                $('#eventresult').text('copy behaviour detected!');
            },
            paste : function(){
                $('#eventresult').text('paste behaviour detected!');
            },
            cut : function(){
                $('#eventresult').text('cut behaviour detected!');
            }
        });

    });
})(jQuery);
```

## 分享这篇文章