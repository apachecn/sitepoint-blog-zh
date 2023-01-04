# 在 Vue 中使用组件的初学者指南

> 原文：<https://www.sitepoint.com/vue-components-intro/>

使用 Vue 的一大好处是它基于组件的方法来构建用户界面。这允许您将应用程序分成更小的、可重用的部分(组件),然后您可以用它们来构建更复杂的结构。

在本指南中，我将为您提供在 Vue 中使用组件的高级介绍。我将介绍如何创建组件，如何在组件之间传递数据(通过 props 和事件总线)以及如何使用 Vue 的`<slot>`元素在组件中呈现附加内容。

每个例子将伴随着一个可运行的 CodePen 演示。

## 如何在 Vue 中创建组件

组件本质上是带有名称的可重用 Vue 实例。有多种方法可以在 Vue 应用程序中创建组件。例如，在一个中小型项目中，您可以使用 [Vue.component](https://vuejs.org/v2/api/#Vue-component) 方法注册一个全局组件，如下所示:

```
Vue.component('my-counter', {
  data() {
    return {
      count: 0
    }
  },
  template: `<div>{{ count }}</div>`
})

new Vue({ el: '#app' }) 
```

组件名称为`my-counter`。它可以这样使用:

```
<div id="app">
  <my-counter></my-counter>
</div> 
```

命名组件时，可以选择烤肉串大小写(`my-custom-component`)或帕斯卡大小写(`MyCustomComponent`)。当从模板中引用组件时，您可以使用这两种变体，但是当在 DOM 中直接引用它时(如上例)，只有*的*kebab case 标签名称有效。

您可能还注意到，在上面的例子中，`data`是一个返回对象文字的函数(而不是对象文字本身)。这是为了让组件的每个实例接收自己的`data`对象，而不必与所有其他实例共享一个全局实例。

有几种方法可以定义组件模板。上面我们使用了一个模板文本，但是我们也可以使用一个标有`text/x-template`的`<script tag>`或者一个 in-DOM 模板。你可以在这里阅读更多关于[定义模板的不同方式](https://medium.com/js-dojo/7-ways-to-define-a-component-template-in-vuejs-c04e0c72900d)。

*想从头学起 Vue.js？这篇文章是从我们的优质图书馆摘录的。使用 SitePoint Premium 获得涵盖基础知识、项目、技巧和工具&的 Vue 书籍全集。[现在就加入，每月仅需 9 美元](https://www.sitepoint.com/premium/products/Z2lkOi8vbGVhcm5hYmxlL1Byb2R1Y3QvMzA3?utm_source=blog&utm_medium=articles)。*

### 单列组件

在更复杂的项目中，全局组件会很快变得难以处理。在这种情况下，让您的应用程序使用单文件组件是有意义的。顾名思义，这些是带有`.vue`扩展名的单个文件，包含一个`<template>`、`<script>`和`<style>`部分。

对于我们上面的例子，一个`App`组件可能看起来像这样:

```
<template>
  <div id="app">
    <my-counter></my-counter>
  </div>
</template>

<script>
import myCounter from './components/myCounter.vue'

export default {
  name: 'app',
  components: { myCounter }
}
</script>

<style></style> 
```

一个`MyCounter`组件可能看起来像这样:

```
<template>
  <div>{{ count }}</div>
</template>

<script>
export default {
  name: 'my-counter',
  data() {
    return {
      count: 0
    }
  }
}
</script>

<style></style> 
```

如您所见，当使用单文件组件时，可以在需要的组件中直接导入和使用它们。

在本指南中，我将展示所有使用`Vue.component()`方法注册组件的例子。

使用单文件组件通常涉及一个构建步骤(例如，使用 Vue CLI)。如果你想了解更多，请查看 Vue 系列中的“Vue CLI 初学者指南”。

## 通过 Props 向组件传递数据

Props 使我们能够将数据从父组件传递到子组件。这使得我们的组件可以分成更小的块来处理特定的功能。例如，如果我们有一个博客组件，我们可能希望显示诸如作者的详细信息、帖子的详细信息(标题、正文和图片)和评论等信息。

我们可以将这些组件分解成子组件，以便每个组件处理特定的数据，使组件树看起来像这样:

```
<BlogPost>
  <AuthorDetails></AuthorDetails>
  <PostDetails></PostDetails>
  <Comments></Comments>
</BlogPost> 
```

如果你仍然不相信使用组件的好处，花一点时间来认识这种组合是多么有用。如果您将来要重新访问这段代码，那么页面的结构以及应该在哪里(也就是在哪个组件中)寻找哪个功能就一目了然了。这种声明式的界面构建方式也让不熟悉代码库的人更容易理解并快速提高工作效率。

由于所有数据都将从父组件传递过来，因此可能如下所示:

```
new Vue({
  el: '#app',
  data() {
    return {
      author: {
        name: 'John Doe',
        email: 'jdoe@example.com'
      }
    }
  }
}) 
```

在上面的组件中，我们定义了作者详细信息和文章信息。接下来，我们必须创建子组件。让我们称子组件为`author-detail`。因此，我们的 HTML 模板将如下所示:

```
<div id="app">
  <author-detail :owner="author"></author-detail>
</div> 
```

我们传递子组件`author`对象作为名为`owner`的道具。注意这里的区别是很重要的。在子组件中，`owner`是我们用来从父组件接收数据的道具的名称。我们想要接收的数据称为`author`，我们已经在父组件中定义了它。

为了访问这些数据，我们需要在`author-detail`组件中声明 props:

```
Vue.component('author-detail', {
  template: `
    <div>
      <h2>{{ owner.name }}</h2>
      <p>{{ owner.email }}</p>
    </div>
  ´,
  props: ['owner']
}) 
```

我们还可以在传递 props 时启用验证，以确保传递的是正确的数据。这类似于 React 中的 [PropTypes](https://reactjs.org/docs/typechecking-with-proptypes.html) 。要在上面的示例中启用验证，请将我们的组件更改为如下所示:

```
Vue.component('author-detail', {
  template: `
    <div>
      <h2>{{ owner.name }}</h2>
      <p>{{ owner.email }}</p>
    </div>
  `,
  props: {
    owner: {
      type: Object,
      required: true
    }
  }
}) 
```

如果我们传递了错误的道具类型，您将会在您的控制台中看到一个错误，如下所示:

```
"[Vue warn]: Invalid prop: type check failed for prop 'text'. Expected Boolean, got String.
(found in component <>)" 
```

在 Vue 文档中有一个[官方指南，你可以用它来学习 prop 验证。](https://vuejs.org/v2/guide/components-props.html#Prop-Validation)

在 [CodePen](https://codepen.io) 上通过 SitePoint ( [@SitePoint](https://codepen.io/SitePoint) )查看 Pen[Vue components-Props](https://codepen.io/SitePoint/pen/NegVgK/)。