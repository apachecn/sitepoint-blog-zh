# 理解更少的保护和循环

> 原文：<https://www.sitepoint.com/understanding-less-guards-loops/>

开发人员希望将编程中的特性引入 CSS，这导致了 CSS 预处理程序的产生。它们允许我们像在 JavaScript 中一样定义变量和创建函数；但更重要的是，通过使用条件块和迭代块，我们可以使代码更加灵活。

这样的块可以使用[而不是](http://lesscss.org/)来创建，但是它们的使用语法与传统的`if..else`和`for`模式非常不同。与 [Sass](http://sass-lang.com/) 和 [Stylus](http://learnboost.github.io/stylus/) 相反，Less 试图尽可能地贴近原始 CSS。因此，为了构造条件和循环，它使用了从[媒体查询](https://developer.mozilla.org/en-US/docs/Web/Guide/CSS/Media_queries)借来的语法。一开始这可能有点令人困惑，但是一旦我们了解它是如何工作的，我们就会发现这只是说同样事情的另一种方式。

## 较少的混合防护

少称其为条件语句 [**米欣护卫**。为了构造一个条件块，我们需要结合使用](http://lesscss.org/features/#mixin-guards-feature) [mixins](http://lesscss.org/features/#mixins-feature) 和 guards。让我们用一个例子来演示主要方案:

```
.theme (@mode) when (@mode = "dark") {
  background-color: darkblue;
}
.theme (@mode) when (@mode = "light") {
  background-color: lightblue;
}

div {
  width: 50px;
  height: 50px;
  .theme("light");
}
```

这里，我们有一个只有一个参数`.theme(@mode)`的 mixin。我们检查该参数是否与特定条件匹配。只有当参数通过测试时，mixin 中的代码才会被执行。对于每个单独的条件，我们需要重复 mixin 的名称及其参数。在我们的例子中，我们有两个条件。正如我们所见，为了替代在许多其他编程语言中使用的关键字`if`, Less 使用了守卫。一个守卫是由`when`关键字加上一个特定的条件创建的。为了更容易理解，让我们看看上面的代码块在 JavaScript 中是什么样子的:

```
function theme(mode){
  if (mode == "dark"){
   element.style.backgroundColor = "darkblue";
 } else if (mode == "light"){
   element.style.backgroundColor = "lightblue";
  }
}
```

因此，mixin guard 只是一个嵌套了`if..else`语句的函数。尽管它是以不同的方式创造的。

在我们的`div`示例中，我们使用 mixin guard，并将“light”作为参数–`.theme("light")`传递。当代码被 Less 编译时，我们得到如下输出:

```
div {
  width: 50px;
  height: 50px;
  background-color: lightblue;
}
```

参见 [CodePen](http://codepen.io) 上 SitePoint ( [@SitePoint](http://codepen.io/SitePoint) )的 Pen [Less Guards and Loops 例 1](http://codepen.io/SitePoint/pen/pJNEeq/) 。