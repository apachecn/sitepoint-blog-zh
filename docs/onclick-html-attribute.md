# onclick (HTML 属性)

> 原文：<https://www.sitepoint.com/onclick-html-attribute/>

## 描述

The
`onclick` event handler captures a click event from
the users’ mouse button on the element to which the
`onclick` attribute is applied. This action usually
results in a call to a script method such as a JavaScript function, like
this:

```
onclick="displayHelpInfo();"
```

然而，它也可以用来就地运行脚本:

```
onclick="alert('You are clicking on me');"
```

请注意，此事件属性不能应用于以下元素:

*   `applet`
*   `base`
*   `basefont`
*   `bdo`
*   `br`
*   `font`
*   `frame`
*   `frameset`
*   `head`
*   `html`
*   `iframe`
*   `isindex`
*   `meta`
*   `param`
*   `script`
*   `style`
*   `title`

## 例子

点击下面
`div`的任意位置将调用一个在别处定义的函数，
称为`showStats()`:

```
<div *onclick="showStats();"*>Figures for February’s racing.</div>
```

## 价值

该属性没有固定值。
这里包含的脚本由作者决定，可以是
对一个或多个已定义函数的调用，也可以是简单的
`alert()`语句。

但是，可能的值将类似于
this:

```
onclick="doMyFunction();"
```

您也可以像这样指定一个值:

```
onclick="doThisFunction();thenDoTheOtherFunction();"
```

您也可以使用这样的值:

```
onclick="alert('Hello world');window.close();"
```

注意
你可以把几个函数串在一起，用一个
分号分隔，如上面第二个和第三个例子
所示。

## 和睦相处

| 微软公司出品的 web 浏览器 | 火狐浏览器 | 旅行队 | 歌剧 |
| --- | --- | --- | --- |
| <abbr title="Internet Explorer 5.5">5.5</abbr> | <abbr title="Internet Explorer 6.0">6.0</abbr> | <abbr title="Internet Explorer 7.0">7.0</abbr> | <abbr title="Firefox 1.0">1.0</abbr> | <abbr title="Firefox 1.5">1.5</abbr> | <abbr title="Firefox 2.0">2.0</abbr> | <abbr title="Safari 1.3">1.3</abbr> | <abbr title="Safari 2.0">2.0</abbr> | <abbr title="Safari 3.0">3.0</abbr> | <abbr title="Opera 9.2">9.2</abbr> | <abbr title="Opera 9.5">9.5</abbr> |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| 全部 | 全部 | 全部 | 全部 | 全部 | 全部 | 全部 | 全部 | 全部 | 全部 | 全部 |

列出的每个浏览器都支持这个
属性。但是，应该避免这样的内联事件处理程序。
正如内联 CSS 样式不受欢迎，但外部
定义的 CSS 样式被认为是良好的实践一样，内联事件处理程序
应该被剥离，并替换为通过 DOM 不引人注目地附加的事件
。

## 分享这篇文章