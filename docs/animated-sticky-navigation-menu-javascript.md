# 普通 Javascript:创建动画粘性导航菜单

> 原文：<https://www.sitepoint.com/animated-sticky-navigation-menu-javascript/>

在网页上添加导航菜单时，有很多事情需要考虑。例如在哪里定位它，如何设计它的风格，如何使它具有响应性。或者可能你想给它添加某种动画(当然是有品位的)。在这一点上，您可能会想找一个 jQuery 插件来帮您完成大部分工作。但那是不必要的！用几行代码创建自己的解决方案实际上非常简单。

在这篇文章中，我将演示如何用普通的 JavaScript、CSS 和 HTML 创建一个动画的、粘性的导航菜单。当你向下滚动页面时，最终产品会向上滑动，然后当你向上滚动时，它会滑回视图中(带有时尚的透明效果)。这是一种流行网站使用的技术，如 [Medium](https://medium.com/sitepoint/what-is-the-best-book-for-learning-javascript-973de4e2ef9e) 和 [Hacker Noon](https://hackernoon.com/how-to-build-a-todo-app-using-react-redux-and-webpack-1aa99dc2f45c) 。

读完之后，你将能够在你自己的设计中运用这种技术，希望效果很好。在文章的最后有一个[演示，供不耐烦的人使用。](#demo)

## 粘性导航菜单:基本 HTML 结构

下面是我们将要使用的框架 HTML 代码。这里没什么令人兴奋的事情。

```
<div class="container">
  <div class="banner-wrapper">
    <div class="banner">
      <div class="top">
        <!-- Navbar top row-->
      </div>
      <div class="nav">
        <!-- Links for navigation-->
      </div>
    </div>
  </div>

  <div class="content">
    <!-- Awesome content here -->
  </div>
</div> 
```

## 运用一点造型

让我们给主要元素添加一些样式。

### 主容器

我们需要删除任何固有的浏览器样式，并将容器的宽度设置为 100%。

```
*{
  box-sizing:border-box;
  padding: 0;
  margin: 0;
}

.container{
  width: 100%;
} 
```

### 横幅容器

这是导航菜单的包装器。当你垂直滚动你的页面时，它总是粘粘的和滑动的来隐藏或显示导航菜单。我们给它一个`z-index`值，以确保它出现在内容的顶部。

```
.banner-wrapper {
  z-index: 4;
  transition: all 300ms ease-in-out;
  position: fixed;
  width: 100%;
} 
```

### 横幅部分

这包含导航菜单。当页面向上或向下滚动时，位置和背景颜色的变化通过 CSS `transition`属性显示出来。

```
.banner {
  height: 77px;
  display: flex;
  flex-direction: column;
  justify-content: space-around;
  background: rgba(162, 197, 35, 1);
  transition: all 300ms ease-in-out;
} 
```

### 内容部分

此部分将包含背景图像和文本。在本文的后面部分，我们将为页面的这一部分添加视差效果。

```
.content {
  background: url(https://unsplash.it/1400/1400?image=699) center no-repeat;
  background-size: cover;
  padding-top: 100%;
} 
```

## 制作菜单动画

我们需要做的第一件事是给 scroll 事件附加一个事件处理程序，这样我们就可以在用户滚动时相应地显示和隐藏菜单。我们还将把所有东西都放在一个[life](https://www.sitepoint.com/demystifying-javascript-closures-callbacks-iifes/#immediately-invoked-function-expressions-iifes)中，以避免与同一页面上运行的其他代码发生冲突。

```
(() => {
  'use strict';

  const handler = () => {
    //DOM manipulation code here
  };

  window.addEventListener('scroll', handler, false);
})(); 
```

### 设置一些初始值

我们将使用一个`refOffset`变量来表示用户向下滚动页面的距离。这在页面加载时被初始化为`0`。我们将使用一个`bannerHeight`变量来存储菜单的高度，还需要引用`.banner-wrapper`和`.banner` DOM 元素。

```
let refOffset = 0;
let visible = true;
const bannerHeight = 77;

const bannerWrapper = document.querySelector('.banner-wrapper');
const banner = document.querySelector('.banner'); 
```

### 建立滚动方向

接下来，我们需要建立滚动方向，以便我们可以相应地显示或隐藏菜单。

我们将从一个名为`newOffset`的变量开始。在页面加载时，这将被设置为 [window.scrollY](https://developer.mozilla.org/en-US/docs/Web/API/Window/scrollY) 的值——文档当前垂直滚动的像素数(最初为`0`)。当用户滚动时，`newOffset`会相应增加或减少。如果它大于存储在`bannerHeight`中的值，那么我们知道我们的菜单已经被滚出视图。

```
const newOffset = window.scrollY;

if (newOffset > bannerHeight) {
  // Menu out of view
} else {
  // Menu in view
} 
```

向下滚动将使`newOffset`大于`refOffset`，导航菜单将向上滑动并消失。向上滚动将使`newOffset`小于`refOffset`，导航菜单将会以透明效果滑回视图。在执行这个比较之后，我们将需要用`newOffset`的值更新`refOffset`来跟踪用户已经滚动了多远。

```
if (newOffset > bannerHeight) {
  // Menu out of view
  if(newOffset > refOffset) {
    // Hide the menu
  } else if (newOffset < refOffset) {
    // Slide menu back down
  }

  refOffset = newOffset;
} else {
  // Menu in view
} 
```

## 制作菜单动画

最后，让我们添加一些动画来显示和隐藏菜单。我们可以使用下面的 CSS 来做到这一点:

```
.animateIn{
  transform: translateY(0px);
}

.animateOut{
  transform: translateY(-100%);
} 
```

我们还应该确保一旦到达页面顶部，透明效果就从菜单中移除。

```
if (newOffset > bannerHeight) {
  if (newOffset > refOffset) {
    bannerWrapper.classList.remove('animateIn');
    bannerWrapper.classList.add('animateOut');
  } else {
    bannerWrapper.classList.remove('animateOut');
    bannerWrapper.classList.add('animateIn');
  }
  banner.style.background = 'rgba(162, 197, 35, 0.6)';
  refOffset = newOffset;
} else {
  banner.style.backgroundColor = 'rgba(162, 197, 35, 1)';
} 
```

如你所见，我们相应地移除/应用了不同的 CSS 类。

## 演示

这是工作菜单的演示。

通过 [CodePen](http://codepen.io) 上的 SitePoint ( [@SitePoint](http://codepen.io/SitePoint) )看笔 [ZKJVdw](http://codepen.io/SitePoint/pen/ZKJVdw/) 。