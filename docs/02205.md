# AtoZ CSS 截屏:Hover 伪类

> 原文：<https://www.sitepoint.com/atoz-css-screencast-hover-pseudo-class/>

这个截屏是我们 AtoZ CSS 系列的一部分。你可以在这里找到系列[的其他条目。](https://www.sitepoint.com/blog/)

## 副本

Hover 是一个伪类，可用于样式化状态。

当用户的鼠标进入元素的边界框时，悬停状态是活动的，当用户的鼠标离开时，悬停状态是不活动的。

在这一集里，我们将看看文本链接上的`:hover`伪类，也包括一些其他的链接伪类。我们还将看看悬停的一些缺点和一些非鼠标用户的变通办法。最后，我们将创建一个由`hover`触发的 CSS 下拉菜单，并使用用于触摸设备的 jQuery 进行增强。

### 链接

大概`:hover`最常见的用途是当鼠标悬停在链接和按钮上时向用户提供视觉反馈。

链接的悬停状态在 CSS 中的样式如下:

```
a:hover {color: red;}
```

`a`旁边的冒号表示一个伪类。我们将在这个视频和整个系列的剩余部分中看到其中的一些——你可以在*第 5 集:`ID Selector`*中查看`:enabled`和`:disabled`伪类。

在这个例子中，只有颜色被样式化，但是*任何*属性都可以被改变、覆盖或取消。如果你想的话，你可以增加字体大小并将链接旋转 180 度——但是不要这样做。

还有其他一些伪类对于设计不同的链接状态也很有用。

还有`a:visited`用于设计用户浏览器历史中的链接。还有`a:active`用于设计当前被点击链接的样式。最后，还有一个`a:focus`,是一个当前有键盘焦点的链接——ie。用户使用`tab`键在页面上的可点击项目中跳转，如链接和表单输入。

我喜欢将这四种链接状态组合在一起，作为重置样式表的一部分，为整个项目设置合理的默认值。

```
a {color: blue;}
a:hover {color: lightblue;}
a:visited {color: darkblue;}
a:focus {outline: 1px dotted grey;}
a:active {color: lightblue;}
```

### 不利方面

悬停状态可以应用于任何元素——不仅仅是链接——这使得它们非常通用。但在触摸设备上，没有鼠标来启动悬停。

如果用户在链接或按钮上轻轻点击，悬停样式有时会被应用，但这并不可靠，并且跨平台和设备也不一致。它经常会令人困惑，我们的工作是改善用户体验，而不是让它变得更糟！

在触摸设备的情况下，避免关键信息隐藏在悬停交互之后。如果做不到这一点，可以使用 JavaScript 将悬停替换为点击。

一些用户不能使用鼠标，我们可以通过在使用`:hover`时总是应用`:focus`和`:active`样式来改善他们的体验。我们可以在 CSS 中用逗号分隔它们，如下所示:

```
a:hover,
a:focus,
a:active {
  /* styles */
}
```

如果您使用像 Sass 这样的 CSS 预处理器，您可以创建一个 mixin，它会为您输出这三个逗号分隔的伪代码，而不必一直将它们写出来。

```
@mixin hover-focus-active() {
  &:hover,
  &:focus,
  &:active { @content }
}
/* usage */
a {
  /*styles*/
  @include hover-focus-active {
    /* styles */
  }
}
```

现在，基于状态的样式将在键盘交互期间显示在可以聚焦或激活的元素上。

### CSS 下拉菜单

我们已经看到了`:hover`在与链接和按钮交互时给出一些视觉反馈的基本用法。web 上另一种常见的设计模式是站点主导航中的下拉菜单。

通过使用更复杂的 CSS 选择器，我们可以使用悬停交互来显示和隐藏子菜单。为了使这种交互在触摸设备上表现良好，需要少量的 JavaScript 来将悬停事件转换为点击事件——但我们将在稍后研究这一点。

诀窍是当鼠标悬停在顶层菜单项上时切换子菜单的`display`属性。由于`:hover`是一个 CSS 选择器，它可以与链中的其他选择器结合起来创建更复杂的行为。更多关于`display`房产的信息，请查看*第四集:`Display`*。

在这个例子中，我有一个水平的无序链接列表，每个链接下面都有一个子菜单。子菜单是另一个无序的列表，但是列表项相互堆叠。

这个 CSS 设置了这个初始布局。颜色和字体属性已经被移到我的`page-styles.css`文件中，以保持工作区的整洁。

```
.menu > li {
  position: relative;
  display: inline-block;
}
.menu .sub-menu {
  position: absolute;
  top: 100%;
  left: 0;
}
```

子菜单可以用`display:none`隐藏，然后当菜单项被悬停时设置为`display:block`。

```
.menu li:hover .sub-menu {
  display: block;
}
```

这将显示所有的子菜单，但是我们可以通过使用`>`子选择器调整这个选择器来限制显示和隐藏单个子菜单。关于这个和其他组合子选择器的更多信息，请查看上一集的通用兄弟选择器。

```
.menu li:hover > .sub-menu { 
  display: block; 
}
```

如果你不喜欢突然打开或关闭子菜单，你可以将`opacity`和`transition`结合起来，以获得更多的淡入淡出效果。我将把它作为一个练习留给你去玩——做一个笔，给我发一条推特；我很想看看你有什么发现。

为了让这个下拉菜单在触摸设备上播放得更好，我们可以添加一点 JavaScript。这个 jQuery 代码片段将在点击而不是悬停时激活下拉菜单；第二次点击将允许导航到顶层菜单中的任何链接，除非它是一个空链接。

```
if ('ontouchstart' in document.documentElement) {
  $('.menu > li').has('.sub-menu').on('click', 'a', function(e) {
    var $menuItem = $(this);
    var target = $menuItem.attr('href');

    if (!target || target === '#') {
      return false;
    }
    if ($menuItem.is('.js-active')) {
      return true;
    }

    e.preventDefault();

    $menuItem.addClass('js-active').siblings().removeClass('js-active');
    $('.sub-menu').hide();
    $menuItem.find('.sub-menu').show();
  });
}
```

这个菜单的一个工作示例包括用于触摸设备的 jQuery，可以在 Codepen 上找到[。](http://codepen.io/SitePoint/pen/dpYAbG)

请关注我们即将发布的快速技巧文章！

## 分享这篇文章