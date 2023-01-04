# 即将推出:原生 CSS 变量

> 原文：<https://www.sitepoint.com/native-css-variables/>

说出一个你想在 CSS 中看到的特性。想要变量的人请举手… *(我知道你们中的一些人非常渴望父母选择器，但你是少数！)*

这个问题源于我们需要在整个样式表中使用和重复使用相同的颜色和其他值。例如:

```
 section { border-color: #334; }
h1      { color: #334; }
p       { background-color: #334; } 
```

维护比它需要的更困难。我们需要记住十六进制/RGB 值的范围，不可避免地，挑剔的客户或设计师会决定#335 是“品牌上的”。搜索和替换可能有效，但如果您在某些地方使用了#333344 或在其他地方使用了 rgb(21，21，2c ),则可能无效。

CSS 变量解决了这个问题。您将#334 定义为一个单独的命名变量，并在整个代码中使用它。这通常是 CSS 预编译器中实现的第一个特性。例如，在[中减去](http://lesscss.org/):

```
 @mycolor: #334;
section { border-color: @mycolor; }
h1      { color: @mycolor; }
p       { background-color: @mycolor; } 
```

或者 [Sass](http://sass-lang.com/)

```
 $mycolor: #334;
section { border-color: $mycolor; }
h1      { color: $mycolor; }
p       { background-color: $mycolor; } 
```

幸运的是，原生 CSS 变量即将到来。在[https://www.w3.org/TR/css-variables/](https://www.w3.org/TR/css-variables/)有一个 W3C 规范草案，它揭示了当供应商开始实现支持时，我们可能如何编写 CSS 代码。

根据该文档，任何以“var-”前缀开头的属性名称都是可变属性。例如:

```
 :root
{
  var-mycolor: #334;
}
section { border-color: var(mycolor); }
h1      { color: var(mycolor); }
p       { background-color: var(mycolor); } 
```

也许这不像 LESS/Sass 那么简洁，但是这是一个很大的进步，可以缓解我们都遇到的维护问题。

这是一个令人兴奋的发展，但不要抱太大希望。不能保证它会被所有的供应商采用，即使他们采用了，这个特性也不能向后兼容旧的浏览器。不过，我们的曾孙们会喜欢的。

## 分享这篇文章