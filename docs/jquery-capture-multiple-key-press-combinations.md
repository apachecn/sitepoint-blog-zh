# jQuery 捕获多个按键组合

> 原文：<https://www.sitepoint.com/jquery-capture-multiple-key-press-combinations/>

你可能已经读过一篇关于如何使用 jQuery 捕获单次按键的文章。在这个例子中，你可以捕捉多个按键的组合。

数组(arrShortCut)定义了以下内容:

1.  一个名字(仅供参考)
2.  键盘密码
3.  要在变量 iShortCutControlKey 中执行的函数

如果控制键激活状态“如果控制键为“关”状态，如果验证了一个键按下“无控制”到“激活状态”查看数组中是否有该键的预置，如果函数调用“execShortCut”执行该函数；

```
//to test use CTRL + Z = alert (2)

var arrShortCut = [{ name: 'test1', key: 15, fx: 'alert(1);' }, { name: 'test2', key: 90, fx: 'alert(2);'}];

var iShortCutControlKey = 17; // CTRL;
var bIsControlKeyActived = false;

$(document).keyup(function(e) {
    if (e.which == iShortCutControlKey) bIsControlKeyActived = false;
}).keydown(function(e) {
    if (e.which == iShortCutControlKey) bIsControlKeyActived = true;
    if (bIsControlKeyActived == true) {
        jQuery.each(arrShortCut, function(i) {
            if (arrShortCut[i].key == e.which) {
                execShortCut(arrShortCut[i].fx);
                return;
            }
        });
    }
});

function execShortCut(fx) {
    eval(fx);
}
```

## 分享这篇文章