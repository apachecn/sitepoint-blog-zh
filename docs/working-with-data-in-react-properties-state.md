# 在 React 中使用数据:属性和状态

> 原文：<https://www.sitepoint.com/working-with-data-in-react-properties-state/>

本文是微软网站开发系列的一部分。感谢您对使 SitePoint 成为可能的合作伙伴的支持。

管理数据对于任何应用程序都是必不可少的。通过应用程序的用户界面(UI)编排数据流是一项挑战。通常，今天的 web 应用程序具有复杂的 UI，以至于修改 UI 一个区域中的数据会直接或间接地影响 UI 的其他区域。通过 Knockout.js 和 Angular.js 进行双向数据绑定是解决这个问题的流行方法。

对于某些应用程序(尤其是简单的数据流)，双向绑定可能是一个足够快速的解决方案。对于更复杂的应用程序，双向数据绑定可能被证明是不够的，并且是有效 UI 设计的障碍。React 没有解决更大的应用程序数据流问题(虽然 [Flux 解决了](https://www.sitepoint.com/building-a-react-universal-blog-app-implementing-flux/))，但是它确实解决了单个组件内的数据流问题。

在单个组件的上下文中，React 既解决了数据流的问题，又更新了 UI 以反映数据流的结果。UI 更新的第二个问题是使用一个名为 reconcile 的模式解决的，这个模式包含了一些创新的想法，比如虚拟 DOM。下一篇文章将详细讨论协调。本文主要关注数据流的第一个问题，以及 React 在其组件中使用的数据类型。

## 组件数据的种类

React 组件中的数据存储为属性或状态。

属性是组件的输入值。它们在呈现组件和初始化状态时使用(稍后讨论)。在实例化组件之后，属性应该被认为是不可变的。属性值只能在实例化组件时设置，然后当组件在 DOM 中重新呈现时，React 将比较新旧属性值，以确定需要哪些 DOM 更新。

下面是一个设置属性值并根据更新的属性值更新 DOM 的演示。

参见 [CodePen](http://codepen.io) 上 SitePoint ( [@SitePoint](http://codepen.io/SitePoint) )的 Pen [React.js 属性更新演示](http://codepen.io/SitePoint/pen/XKqQqN/)。