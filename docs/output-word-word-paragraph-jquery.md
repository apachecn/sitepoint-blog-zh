# 使用 jQuery 逐词输出段落

> 原文：<https://www.sitepoint.com/output-word-word-paragraph-jquery/>

以指定的时间间隔输出句子中每个单词的 jQuery 代码片段。您可以通过以毫秒为单位的速度参数来更改输出速度。您可以查看演示或在下面的 jsfiddle 中加载代码。

## 演示

[https://jsfiddle.net/jquery4u/t6rWB/show/](https://jsfiddle.net/jquery4u/t6rWB/show/)

## jQuery

```
$(document).ready(function () {
    var $el = $('div'),
        text = $el.text(),
        speed = 1000; //ms

    $el.empty();

    var wordArray = text.split(' '),
        i = 0;

    INV = setInterval(function () {
        if (i >= wordArray.length - 1) {
            clearInterval(INV);
        }
        $el.append(wordArray[i] + ' ');
        i++;
    }, speed);
});
```

## 超文本标记语言

```
Lorem ipsum dolor sit amet, consectetur adipisicing elit. Modi libero aliquam aut commodi illum fuga consequatur labore iste necessitatibus quidem atque aliquid iusto numquam tempora rerum excepturi officiis maiores. Ab?
```

[载入 jsfiddle](https://jsfiddle.net/jquery4u/)

## 打字机插件

有一个非常好的[打字机 jQuery 插件](http://www.idangero.us/preview/?item=32)，它的作用就像一台打字机，可能会有用。

## 分享这篇文章