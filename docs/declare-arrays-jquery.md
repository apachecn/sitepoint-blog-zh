# 在 jQuery 中声明数组

> 原文：<https://www.sitepoint.com/declare-arrays-jquery/>

简单的 JavaScript 代码片段来描述一个数组。像其他 JavaScript 变量一样，您不必在使用数组之前声明它们。我喜欢声明它们，这样我就可以很容易地阅读发生了什么，这是一个很好的练习！

## 示例 1–数组构造函数

```
// Declare an array (using the array constructor)
var arlene1 = new Array();
var arlene2 = new Array("First element", "Second", "Last");
```

## 示例 2–文字注释

```
// Declare an array (using literal notation)
var arlene1 = [];
var arlene2 = ["First element", "Second", "Last"];
```

## 示例 3–隐式声明

```
// Create an array from a method's return value
var carter = "I-learn-JavaScript";
var arlene3 = carter.split("-");
```

为了避免脚本错误，您应该养成在声明数组时初始化数组的习惯，如下所示:

```
// Declare an empty array using literal notation:
var arlene = [];
// The variable now contains an array of length zero
```

在检查了我在 jslint.com 上的代码后，我发现上面说用数组构造函数声明数组被认为是不好的做法！它建议使用文字符号。

## 分享这篇文章