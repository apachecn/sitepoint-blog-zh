# Vue 插槽综合指南

> 原文：<https://www.sitepoint.com/vue-slots-comprehensive-guide/>

组件是现代 web 应用程序开发的核心。每个应用程序都由许多组件组成，这些组件平滑地缝合在一起，以便作为一个整体工作。这些组件需要具有最大的灵活性和可重用性，以允许在不同的情况下甚至在不同的应用程序中使用它们。许多框架用来满足这些需求的主要机制之一——在特定的 Vue 中——被称为“插槽”。

插槽是一种强大而通用的内容分发和组合机制。您可以将插槽视为可定制的模板(例如，类似于 PHP 模板)，您可以在不同的地方使用它们，用于不同的用例，产生不同的效果。例如，在像 [Vuetify](https://vuetifyjs.com/en/) 这样的 UI 框架中，插槽被用来制作通用组件，如[警报组件](https://vuetifyjs.com/en/api/v-alert/#slots)。在这些类型的组件中，槽用作默认内容和任何附加/可选内容(如图标、图像等)的占位符。

插槽允许您向特定组件添加任何结构、样式和功能。通过使用插槽，开发人员可以大大减少单个组件中使用的道具数量，使组件更加整洁和易于管理。

在本教程中，我们将探索如何在 Vue 3 的环境中利用老虎机的力量。让我们开始吧。

## 插槽的基本用法

基本上，Vue 提供了两种插槽:简单插槽和作用域插槽。让我们从简单的开始。考虑下面的例子:

```
const app = Vue.createApp({})

app.component('primary-button', {
  template: ` <button>
      <slot>OK</slot>
    </button>`
})

app.mount('#app') 
```

这里，我们有一个主要的按钮组件。我们希望按钮的文本是可定制的，所以我们在`button`元素中使用了`slot`组件来添加文本的占位符。我们还需要一个默认的(后备)通用值，以防我们不提供自定义值。Vue 使用我们放在`slot`组件中的所有内容作为默认的槽内容。所以我们只在组件中放入文本“OK”。现在我们可以像这样使用组件:

```
<div id="app">
  <primary-button></primary-button>
</div> 
```

见笔 [Vue 3 槽:基本槽](https://codepen.io/SitePoint/pen/oNZyNyO)by site point([@ site point](https://codepen.io/SitePoint))
在 [CodePen](https://codepen.io) 上。