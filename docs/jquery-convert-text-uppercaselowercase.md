# jQuery 将文本转换为大写/小写

> 原文：<https://www.sitepoint.com/jquery-convert-text-uppercaselowercase/>

jQuery 代码片段将网页上的文本转换为大写或小写。可以在不使用 css 的情况下改变网页上的文本样式(尽管下面也有一个 css 示例)。

## 使用 jQuery 的大写/小写

```
//Lowercase
$('jqueryselector').val($(this).val().toLowerCase());

//Uppercase
$('jqueryselector').val($(this).val().toUpperCase());
```

## 使用 CSS 大写

如果你想大写每个单词的第一个字母，你可以使用 CSS。

```
.capitalise
{
    text-transform:capitalize;
}
```

然后将其附加到元素上。

```
$('jqueryselector').addClass('capitalise');
```

## 分享这篇文章