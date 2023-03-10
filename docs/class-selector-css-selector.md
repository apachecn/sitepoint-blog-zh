# 类选择器(CSS 选择器)

> 原文：<https://www.sitepoint.com/class-selector-css-selector/>

## 句法

```
.className {
declaration block
}

```

## 描述

基于类名选择元素是 CSS 中非常常见的技术。属性选择器语法`[class~="warning"]`相当笨拙，但是谢天谢地有一个更简单更简短的形式:类选择器。

下面是一个简单的例子，它选择了所有具有包含值“`warning`”的`class`属性的元素:

```
.warning {
  ⋮ declarations
}
```

这个例子还演示了隐式通用选择器的使用——它相当于`*.warning.`。注意，空格字符不能出现在句点之后，也不能出现在元素类型选择器或显式通用选择器与句点之间。例如，下面的选择器将匹配所有具有包含值“`warning`”的`class`属性的`p`元素:

```
p.warning {
  ⋮ declarations
}
```

一个简单的选择器可以包含不止一个属性选择器和/或类选择器；在这种情况下，选择器模式匹配其属性包含所有指定组件的元素。这里有一个例子:

```
div.foo.bar {
  ⋮ declarations
}
div.foo.bar[title^="Help"] {
  ⋮ declarations
}
```

上面的第一个示例选择器匹配其`class`属性值包含单词`"foo"`和`"bar"`的`div`元素。第二个示例选择器匹配`div`元素，其`class`属性值包含单词`"foo"`和`"bar"`，其标题属性值以字符串`"Help"`开头。为了进一步澄清，与上述 [CSS 选择器](https://www.sitepoint.com/css-selectors/)匹配的 html 可能如下:

```
<div class="foo bar">Matches first example</div>
<div class="foo bar" title="Help">Matches second example</div> 
```

## 例子

下面的选择器将匹配所有具有包含值`"intro"`的`class`属性的`p`元素:

```
p.intro {
  ⋮ declarations
}
```

## 分享这篇文章