# jQuery/HTML5 输入焦点和光标位置

> 原文：<https://www.sitepoint.com/jqueryhtml5-input-focus-cursor-positions/>

下面是一些代码片段和例子，说明如何使用 **jQuery & HTML5 来设置光标输入焦点和光标位置**，这是登录表单等常见的操作。欢迎评论、改进和建议。

![cursor-focus](img/0a3929c2dc94ba2ee6ce8c42c0f407de.png)

## jQuery 输入焦点

只需调用 focus()函数将焦点设置为输入。

```
//set focus on input
$('input[name=firstName]').focus();
```

[jsfiddle.net/z9Ldt/](https://jsfiddle.net/z9Ldt/)

## HTML5 输入焦点

HTML5 提供了很棒的功能……在 http://html5please.com/上找不到，但我测试过它在 Chrome 和 Firefox 上可以用，但在 IE9 或更低版本上不行。

```
//AUTO INPUT FOCUS
<input type="text" name="myInput" autofocus />
```

[jsfiddle.net/89PHL/](https://jsfiddle.net/89PHL/)

## jQuery 设置光标位置

jQuery 函数将光标位置设置到输入字段中的特定字符位置。

```
//SET CURSOR POSITION
$.fn.setCursorPosition = function(pos) {
  this.each(function(index, elem) {
    if (elem.setSelectionRange) {
      elem.setSelectionRange(pos, pos);
    } else if (elem.createTextRange) {
      var range = elem.createTextRange();
      range.collapse(true);
      range.moveEnd('character', pos);
      range.moveStart('character', pos);
      range.select();
    }
  });
  return this;
};
```

### 用法示例

在第一个字符后设置光标位置。

```
$('#username').setCursorPosition(1);
```

[jsfiddle . net/tazs/](https://jsfiddle.net/tAZSs/)

## jQuery 设置光标位置

jQuery 函数自动选择输入字段中的文本(特定数量的字符)。

```
//SELECT TEXT RANGE
$.fn.selectRange = function(start, end) {
    return this.each(function() {
        if (this.setSelectionRange) {
            this.focus();
            this.setSelectionRange(start, end);
        } else if (this.createTextRange) {
            var range = this.createTextRange();
            range.collapse(true);
            range.moveEnd('character', end);
            range.moveStart('character', start);
            range.select();
        }
    });
};
```

### 用法示例

选择输入中的前 5 个字符。

```
$('#username').selectRange(0,5);
```

[jsfiddle.net/yMUx5/](https://jsfiddle.net/yMUx5/)

## 分享这篇文章