# JavaScript 重构技术:特定于通用代码

> 原文：<https://www.sitepoint.com/javascript-refactoring-techniques-specific-to-generic-code/>

*本文由[丹王子](https://www.sitepoint.com/author/dprince)同行评审。感谢 SitePoint 的所有同行评审员使 SitePoint 的内容尽可能做到最好！*

在 SitePoint 论坛上最近的一个[帖子](https://www.sitepoint.com/community/t/display-element-when-specific-option-is-selected/226616)中，给出了一些代码，让一个下拉框控制另一个下拉框何时可见。尽管代码运行得很好，但我意识到它还有许多不足之处。它很脆弱，甚至无法承受伴随 HTML 的微小变化。

下面是原始的 CSS 代码:

```
#second { display: none; }
#second.show { display: block; } 
```

和原始的 JavaScript 代码:

```
document.getElementById("location").onchange = function () {
  if (this[this.selectedIndex].value === "loc5") {
    document.getElementById("second").className = "show";
  } else {
    document.getElementById("second").className = "";
  }
}; 
```

在本文中，我将演示一些简单的 JavaScript 重构技术，这些技术可以应用到上述代码中，以使代码更容易重用，更适应未来的变化。

## 知道该走哪条路

JavaScript 有许多方法来完成相同的任务，其中一些方法比其他方法更有效。现在有没有方法来改进代码，这样我们以后就不用回头再来看它了？当然可以！但是，当做一件事有几种可能的方法时，我们如何确定哪一种可能是最好的呢？

改进代码的一个常用技术是消除重复(使用[不要重复自己](https://en.wikipedia.org/wiki/Don%27t_repeat_yourself)的原则)。从那里开始，从特定代码到更通用的代码会更有用，这允许我们处理更广泛的情况。

当处理未来的变化时，特定的代码往往是脆弱的。代码并不存在于真空中，需要随着周围和 HTML 代码中的其他动作而变化。但是，根据过去的经验，我们可以看到发生的常见变化和改进，从而减少我们需要重新访问代码的次数。你会发现这总是意味着使代码更加通用。

但是要小心！很容易使我们的代码过于一般化，以至于变得难以理解。在泛型和可读性之间取得良好的平衡是我们发现改进代码的地方。

## JavaScript 重构技术:特定于通用

在[测试驱动开发](https://en.wikipedia.org/wiki/Test-driven_development) (TDD)的过程中，作为过程的一部分，你会不可避免地遇到这个原则:

> 随着测试变得更加具体，代码变得更加通用。

罗伯特·c·马丁的《TDD 周期》很好地涵盖了这一思想。这里的主要好处是泛型代码最终能够处理更广泛的情况和场景。

> 请注意，如果您想了解更多关于 TDD 的知识，请查看我们的简短迷你课程[使用 Node.js](https://www.sitepoint.com/premium/courses/test-driven-development-in-node-js-2932/?utm_source=sitepoint&utm_medium=blog&utm_campaign=learning_js_tdd_by_example) 进行测试驱动开发。

查看上面的代码，一些明显的特定于一般的改进立即可用。

*   将字符串存储在变量中有助于我们从一个地方管理它们。
*   `onchange`事件处理程序是有问题的，因为它可能被覆盖。我们应该考虑用`addEventListener`来代替。
*   `className`属性将覆盖现有的类名。我们应该考虑用`classList`来代替。

在做了所有这些改进之后，我们最终会得到对未来变化更有弹性、更容易更新的代码。所以让我们开始吧…

## 使用变量来防止重复

下拉框的 ID(“location”)和它的触发器值(“loc5”)是保存在一起的有用参考。第二个`<select>`元素也被引用了两次，我们可以将它提取到一个单独的变量中以防止混乱，而[提供了更容易的维护](https://www.sitepoint.com/importance-of-code-that-humans-can-read/)。

例如，如果元素的 ID 发生更改，则需要更改对同一元素的两个引用，而不是:

```
// bad code
if (...) {
  document.getElementById("second").className = "show";
} else {
  document.getElementById("second").className = "";
} 
```

我们可以在变量中存储对该元素的引用，将未来的更改限制在该变量被赋值的位置:

```
// good code
var target = document.getElementById("second");
if (...) {
  target.className = "show";
} else {
  target.className = "";
} 
```

通过将这些字符串一起放在代码的顶部，并分离出 if 条件的各个部分，特定于一般的技术会使代码更易于维护，无论是现在还是将来。如果任何标识符或选项值发生变化，都可以很容易地在一个地方找到它们，而不是在代码中寻找它们的所有出现。

```
// improved code
var source = document.getElementById("location");
var target = document.getElementById("second");
var triggerValue = "loc5";

source.onchange = function () {
  var selectedValue = this[this.selectedIndex].value;
  if (selectedValue === triggerValue) {
    target.className = "show";
  } else {
    target.className = "";
  }
}; 
```

## 改进事件处理

传统的事件处理程序仍然很流行(并且在本例中得到了正确的使用)，但是它们有一些问题。其中最主要的是，当以这种方式为元素设置事件处理程序时，您将覆盖同一事件的任何以前的处理程序。

```
// bad code
source.onchange = function () {
  // ...
}; 
```

目前，上面的代码工作。我们可以通过测试来证明这一点。

* * *

### 关于测试的快速说明

哲学:测试是确保你写的代码按照你期望的方式运行的好方法。它们降低了您对代码所做的更改导致代码中其他地方出错的可能性。不幸的是，对测试的介绍超出了本文的范围(尽管 SitePoint 在这个主题上有[很多很棒的内容](https://www.sitepoint.com/blog/))。在你的生活中，即使你没有写测试，你也可以跟着做。

**语法**:下面的测试使用了 [Jasmine](http://jasmine.github.io/) 测试框架。Jasmine 测试(又名 specs)是通过调用全局 Jasmine `it`函数来定义的，该函数接受一个字符串和另一个函数作为参数。字符串是规范的标题，函数是规范本身。你可以在[项目的主页](http://jasmine.github.io/2.1/introduction.html)上了解更多关于 Jasmine 的信息。

> 请注意，本文将着重于测试前端代码。如果你正在寻找专注于后端的东西，一定要看看我们的课程:[node . js 中的测试驱动开发](https://www.sitepoint.com/premium/courses/test-driven-development-in-node-js-2932)

* * *

### 运行测试

给定我们代码的先前状态，下面两个测试将通过:

```
it("should add the 'show' class name when the 'loc5' option is selected", function() {
  changeSelectTo(source, "loc5");
  expect(target.classList.contains("show")).toBe(true);
});

it("should remove the 'show' class name when an option value different from 'loc5' is selected", function() {
  changeSelectTo(source, "loc2");
  expect(target.classList.contains("show")).toBe(false);
}); 
```

`changeSelectTo`函数改变`<select>`元素的值，而期望(使用 Jasmine 的`expect`函数构建)确定该元素具有正确的类名。

但是一旦改变了`onchange`处理程序——这是任何其他代码都能够做到的——改变类名的函数就丢失了，事情开始出错。我们可以通过进一步的测试来证明这一点:

```
it("should toggle the class name even when the onchange event is replaced", function () {
  changeSelectTo(source, "loc2");
  expect(target.classList.contains("show")).toBe(false);

  // Overwrite the onchange handler
  source.onchange = function doNothing() { return; };

  changeSelectTo(source, "loc5");
  expect(target.classList.contains("show")).toBe(true); // fails
}); 
```

这个测试失败了，从[这个代码笔](http://codepen.io/SitePoint/pen/mAbzLL)中可以看出。请注意，茉莉的具体代码是在另外一支笔里，在这里找到了。

### 重构我们的代码以通过测试

我们可以通过使用 [addEventListener](https://developer.mozilla.org/en-US/docs/Web/API/EventTarget/addEventListener) 轻松通过这个测试，它允许任意数量的函数被分配给一个事件。`false`参数说明事件捕获(当`true`时)或事件冒泡(当`false`时)是否用于事件的顺序。Quirksmode 很好地概述了事件的[事件顺序](http://www.quirksmode.org/js/events_order.html)。

```
// good code
source.addEventListener("change", function (evt) {
  // ...
}, false); 
```

下面是这一变化对代码的影响:

```
// improved code
var source = document.getElementById("location");
var target = document.getElementById("second");
var triggerValue = "loc5";

source.addEventListener("change", function () {
  var selectedValue = this[this.selectedIndex].value;
  if (selectedValue === triggerValue) {
    target.className = "show";
  } else {
    target.className = "";
  }
}, false); 
```

随着`addEventListener`线激活，所有测试现在都通过了。

在 [CodePen](http://codepen.io) 上通过 SitePoint ( [@SitePoint](http://codepen.io/SitePoint) )看笔 [mAbzLL](http://codepen.io/SitePoint/pen/mAbzLL/) 。