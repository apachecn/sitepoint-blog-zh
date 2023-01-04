# Vue 中计算属性、方法和观察器的区别

> 原文：<https://www.sitepoint.com/the-difference-between-computed-properties-methods-and-watchers-in-vue/>

*想从头学起 Vue.js？使用 SitePoint Premium 获得涵盖基础知识、项目、技巧和工具&的 Vue 书籍全集。[现在就加入，每月仅需 14.99 美元](https://www.sitepoint.com/premium/products/Z2lkOi8vbGVhcm5hYmxlL1Byb2R1Y3QvMzE3?utm_source=blog&utm_medium=articles)。*

对于那些刚开始学习 Vue 的人来说，对方法、计算属性和观察器之间的区别有点困惑。

尽管通常有可能使用它们中的每一个来完成或多或少相同的事情，但是知道每一个在哪些方面比其他的更出色是很重要的。

在这个快速技巧中，我们将看看 Vue 应用的这三个重要方面及其用例。我们将通过使用这三种方法中的每一种来构建相同的搜索组件。

## 方法

一个[方法](https://vuejs.org/v2/guide/events.html#Method-Event-Handlers)或多或少就是你所期望的——一个作为对象属性的函数。您可以使用方法对 DOM 中发生的事件做出反应，也可以从组件中的其他地方调用它们，例如，从计算属性或观察器中调用。方法用于对常见功能进行分组——例如，处理表单提交，或者构建可重用的功能，如发出 Ajax 请求。

您在 Vue 实例中的`methods`对象内创建一个方法:

```
new Vue({
  el: "#app",
  methods: {
    handleSubmit() {}
  }
}) 
```

当你想在模板中使用它时，你可以这样做:

```
<div id="app">
  <button @click="handleSubmit">
    Submit
  </button>
</div> 
```

我们使用 [v-on 指令](https://vuejs.org/v2/api/#v-on)将事件处理程序附加到我们的 DOM 元素，它也可以缩写为`@`符号。

现在每次点击按钮都会调用`handleSubmit`方法。例如，当您想要传递方法体中需要的参数时，您可以这样做:

```
<div id="app">
  <button @click="handleSubmit(event)">
    Submit
  </button>
</div> 
```

这里我们传递一个[事件对象](https://developer.mozilla.org/en-US/docs/Web/API/Event)，例如，它允许我们在表单提交的情况下阻止浏览器的默认动作。

然而，当我们使用一个指令来附加事件时，我们可以使用一个[修饰符](https://vuejs.org/v2/guide/events.html#Event-Modifiers)来更优雅地实现同样的事情:`@click.stop="handleSubmit"`。

现在让我们看一个使用方法过滤数组中数据列表的例子。

在演示中，我们想要呈现一个数据列表和一个搜索框。每当用户在搜索框中输入值时，呈现的数据都会发生变化。该模板将如下所示:

```
<div id="app">
  <h2>Language Search</h2>

  <div class="form-group">
    <input
      type="text"
      v-model="input"
      @keyup="handleSearch"
      placeholder="Enter language"
      class="form-control"
    />
  </div>

  <ul v-for="(item, index) in languages" class="list-group">
    <li class="list-group-item" :key="item">{{ item }}</li>
  </ul>
</div> 
```

如您所见，我们引用了一个`handleSearch`方法，每当用户在我们的搜索字段中键入一些内容时，都会调用这个方法。我们需要创建方法和数据:

```
new Vue({
  el: '#app',
  data() {
    return {
      input: '',
      languages: []
    }
  },
  methods: {
    handleSearch() {
      this.languages = [
        'JavaScript',
        'Ruby',
        'Scala',
        'Python',
        'Java',
        'Kotlin',
        'Elixir'
      ].filter(item => item.toLowerCase().includes(this.input.toLowerCase()))
    }
  },
  created() { this.handleSearch() }
}) 
```

`handleSearch`方法使用输入字段的值来更新列出的条目。需要注意的一点是，在`methods`对象中，不需要引用带有`this.handleSearch`的方法(就像在 React 中必须做的那样)。

参见 [CodePen](https://codepen.io) 上 SitePoint ( [@SitePoint](https://codepen.io/SitePoint) )的 Pen [Vue 方法](https://codepen.io/SitePoint/pen/qLXERO/)。