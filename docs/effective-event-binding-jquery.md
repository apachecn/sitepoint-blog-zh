# 用 jQuery 实现有效的事件绑定

> 原文：<https://www.sitepoint.com/effective-event-binding-jquery/>

如果您曾经使用过 jQuery，那么您可能已经熟悉了事件绑定。这是相当基础的东西，但是再深入一点，你会发现让你的事件驱动代码不那么脆弱、更易于管理的机会。

## 更好的选择策略

先说一个基本的例子。下面是可以打开或关闭的导航菜单的 HTML:

```
<button class="nav-menu-toggle">Toggle Nav Menu</button>
<nav>
    <ul>
        <li><a href="/">West Philadelphia</a></li>
        <li><a href="/cab">Cab Whistling</a></li>
        <li><a href="/throne">Throne Sitting</a></li>
    </ul>
</nav>
```

下面是一些 JavaScript 代码，用于在点击按钮时切换导航菜单:

```
$('.nav-menu-toggle').on('click', function() {
    $('nav').toggle();
});
```

这可能是最常见的方法。可以用，但是很脆。JavaScript 依赖于具有`nav-menu-toggle`类的按钮元素。对于另一个开发人员，甚至是将来健忘的您来说，很容易意识不到这一点，并在重构时删除或重命名该类。

问题的核心是我们使用 CSS 类来表示和交互。这违反了[关注点分离](http://en.wikipedia.org/wiki/Separation_of_concerns#HTML.2C_CSS.2C_JavaScript)原则，使得维护更容易出错。

让我们尝试一种不同的方法:

```
<button data-hook="nav-menu-toggle">Toggle Nav Menu</button>
<nav data-hook="nav-menu">
    <ul>
        <li><a href="/">West Philadelphia</a></li>
        <li><a href="/cab">Cab Whistling</a></li>
        <li><a href="/throne">Throne Sitting</a></li>
    </ul>
</nav>
```

这次我们使用一个数据属性(`data-hook`)来标识元素。任何涉及 CSS 类的改变将不再影响 JavaScript，给我们更好的关注点分离和更健壮的代码。

我们只需要更新 jQuery 选择器来使用`data-hook`:

```
$('[data-hook="nav-menu-toggle"]').on('click', function() {
    $('[data-hook="nav-menu"]').toggle();
});
```

注意，我也选择使用`data-hook`作为`nav`元素。你不必这样做，但我喜欢它提供的洞察力:任何时候你看到`data-hook`，你都知道这个元素在 JavaScript 中被引用。

## 一些句法糖

我承认选择器不是最漂亮的。让我们通过用自定义函数扩展 jQuery 来解决这个问题:

```
$.extend({
    hook: function(hookName) {
        var selector;
        if(!hookName || hookName === '*') {
            // select all data-hooks
            selector = '[data-hook]';
        } else {
            // select specific data-hook
            selector = '[data-hook~="' + hookName + '"]';
        }
        return $(selector);
    }
});
```

有了这些，我们可以重写 JavaScript:

```
$.hook('nav-menu-toggle').on('click', function() {
    $.hook('nav-menu').toggle();
});
```

好多了。我们甚至可以在一个元素上有一个用空格分隔的钩子名称列表:

```
<button data-hook="nav-menu-toggle video-pause click-track">Toggle Nav Menu</button>
```

并在以下内容中查找任何挂钩名称:

```
$.hook('click-track'); // returns the button as expected
```

我们还可以选择页面上的所有挂钩元素:

```
// both are equivalent
$.hook();
$.hook('*');
```

## 避免匿名函数表达式

到目前为止，示例一直使用匿名函数表达式作为事件处理程序。让我们重写代码以使用声明的函数:

```
function toggleNavMenu() {
    $.hook('nav-menu').toggle();
}

$.hook('nav-menu-toggle').on('click', toggleNavMenu);
```

这使得执行事件绑定的代码行更容易阅读。`toggleNavMenu`函数名传达了意图，是自文档化代码的一个好例子。

我们还获得了可重用性，因为如果需要的话，其他代码领域也可以使用`toggleNavMenu`。

最后，这是自动化测试的一大胜利，因为声明函数比匿名函数表达式更容易进行单元测试。

## 处理多个事件

jQuery 为处理多个事件提供了方便的语法。例如，您可以指定由单个事件处理程序处理的以空格分隔的事件列表:

```
$.hook('nav-menu-toggle').on('click keydown mouseenter', trackAction);
```

如果需要用不同的事件处理程序处理多个事件，可以使用对象表示法:

```
$.hook('nav-menu-toggle').on({
    'click': trackClick,
    'keydown': trackKeyDown,
    'mouseenter': trackMouseEnter
});
```

另一方面，您也可以同时解除多个事件的绑定:

```
// unbinds keydown and mouseenter
$.hook('nav-menu-toggle').off('keydown mouseenter');

// nuclear option: unbinds everything
$.hook('nav-menu-toggle').off();
```

可以想象，不小心解除绑定可能会产生巨大的副作用。请继续阅读缓解这一问题的技巧。

## 小心解开

为一个元素上的同一个事件绑定多个事件处理程序并不罕见。让我们重温一下之前的按钮:

```
<button data-hook="nav-menu-toggle video-pause click-track">Toggle Nav Menu</button>
```

当按钮被点击时，不同的代码区域可能会发生一些变化:

```
// somewhere in the nav code
$.hook('nav-menu-toggle').on('click', toggleNavMenu);

// somewhere in the video playback code
$.hook('video-pause').on('click', pauseCarltonDanceVideo);

// somewhere in the analytics code
$.hook('click-track').on('click', trackClick);
```

不管使用不同的选择器，按钮现在都有三个 click 事件处理程序。现在想象一下，我们的分析代码已经不再关心按钮了:

```
// no good
$.hook('click-track').off('click');
```

哎呀，这实际上删除了所有的点击事件处理程序，而不仅仅是`trackClick`。我们应该更加敏锐，并指定要删除的特定事件处理程序:

```
$.hook('click-track').off('click', trackClick);
```

另一种选择是使用名称空间。在绑定或解除绑定时，任何事件都可以用名称空间限定，这为您提供了更好的控制:

```
// binds a click event in the "analytics" namespace
$.hook('click-track').on('click.analytics', trackClick);

// unbinds only click events in the "analytics" namespace
$.hook('click-track').off('click.analytics');
```

您甚至可以使用多个名称空间:

```
// binds a click event in both the "analytics" and "usability" namespaces
$.hook('click-track').on('click.analytics.usability', trackClick);

// unbinds any events in either the "analytics" OR "usability" namespaces
$.hook('click-track').off('.usability .analytics');

// unbinds any events in both the "analytics" AND "usability" namespaces
$.hook('click-track').off('.usability.analytics');
```

请注意，名称空间的顺序无关紧要。名称空间没有层次结构。

如果您有复杂的功能，需要跨多个元素绑定各种事件，那么名称空间是一种将它们组合在一起以便快速清理的简单方法:

```
// free all elements on the page of any "analytics" event handling
$('*').off('.analytics');
```

名称空间在编写插件时特别有用，因为您可以确保您的插件是一个好公民，只解除其自身事件处理程序的绑定。

## 离别赠言

jQuery 事件绑定很棒，因为它很容易开始，但是当你需要它的时候，它包含了大量的功能。希望我已经分享了一两个技巧，可以帮助您编写更健壮、更清晰、更易于管理的事件驱动 JavaScript。

感谢阅读！

## 分享这篇文章