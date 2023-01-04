# 掌握 JavaScript 的“this”关键字的最后步骤

> 原文：<https://www.sitepoint.com/mastering-javascripts-this-keyword/>

在[之前的一篇文章](https://www.sitepoint.com/inner-workings-javascripts-this-keyword/)中，我们学习了正确使用 JavaScript 的`this`关键字的基础知识。我们看到，决定`this`指的是什么的关键因素是找出当前的执行上下文。然而，在上下文以我们意想不到的方式改变的情况下，这项任务可能有点棘手。在这篇文章中，我将强调这种情况何时会发生，以及我们可以做些什么来补救。

## 修复常见问题

在这一节中，我们将探讨一些因使用`this`关键字而产生的最常见的问题，并学习如何解决它们。

### 1.在提取的方法中使用`this`

人们最常犯的一个错误是试图将一个对象的方法赋给一个变量，并期望`this`仍然指向原始对象。正如我们从下面的例子中看到的，这根本行不通。

```
var car = {
brand: "Nissan",
getBrand: function(){
console.log(this.brand);
}
};

var getCarBrand = car.getBrand;

getCarBrand(); // output: undefined
```

[JS 斌](https://jsbin.com/yucoto/2/embed?js,console)