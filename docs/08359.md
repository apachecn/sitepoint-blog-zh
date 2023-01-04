# jQuery console.log 当前函数名

> 原文：<https://www.sitepoint.com/jquery-console-log-current-function/>

下面是一些快速的 JavaScript 代码片段，用于找出并记录 JavaScript 中的当前函数名。

```
//way 1
var fName = arguments.callee.toString();
console.log(fName);
fName = fName.substr('function '.length);
console.log(fName);
fName = fName.substr(0, fName.indexOf('('));
console.log(fName);

//way 2
var fName = arguments.callee.toString().match(/functions+([^s(]+)/);
console.log(fName);
console.log(fName[1]);

//way 3
var fName = arguments.callee.toString(),
    regEx = /^functions+([^(]+)/
console.log(regEx.exec(fName)[1]);
```

有人知道获取对象字面语法函数的函数名的方法吗？？我觉得不太可能…

## 分享这篇文章