# 使用 Vue 3.0 合成 API 构建购物清单应用程序

> 原文：<https://www.sitepoint.com/building-a-shopping-list-app-with-the-vue-composition-api/>

这篇文章将展示 Vue 组合 API 是如何让你的代码更具可读性和可维护性的好方法。作为选项 API 的替代，Vue 3 中引入的组合 API 更容易处理方法和组件状态。

Composition API 是在 Vue 3 应用程序中创建和组织组件的一种新的(可选的)方式。它允许对特定功能(例如搜索)的所有代码进行分组，从而更直观地定义反应组件逻辑。使用 Vue 组合 API 将使您的应用程序在几个组件之间更具可伸缩性和可重用性。

在本文中，我们将使用 Vue 组合 API 构建一个简单的购物清单应用程序。

![Shopping list app with Vue Composition API](img/b86ee35282e64cfc0634a621eb78ad75.png)

你可以看看我们正在开发的应用程序的现场演示。

## 先决条件

对于本教程，您需要:

*   对 HTML、CSS、JavaScript 和 Vue 有基本的了解
*   文本编辑器
*   网络浏览器
*   [Node.js](https://nodejs.org/en/)
*   vista CLI

## 设置 Vue 应用程序

现在，让我们从安装 Vue Cli 开始:

```
npm install -g vue-cli 
```

该命令将全局安装 Vue。

我们将使用 Vue CLI 构建一个简单的应用程序。为此，请打开您的终端并键入以下内容:

```
vue create vueshoppinglist 
```

安装后，使用`cd vueshoppinglist`进入文件夹并运行`npm run serve`。

![Your new Vue installation is ready](img/a12af6b252458f684ef84bf5bc9fd570.png)

这将启动一个开发服务器，允许您在 [localhost:8080](http://localhost:8080/) 上查看您的应用程序。

现在是时候建立一个好的 Vue 项目了。

## Vue 组合 API

要从项目的根目录安装复合 API，请运行以下命令:

```
npm install --save @vue/composition-api 
```

成功安装后，我们将把它导入到我们的项目中。

修改`src/main.vue`以在我们的应用程序中全局注册组合 API，这样我们就可以在我们所有的应用程序组件中使用它:

```
import Vue from 'vue'
import App from './App.vue'
import VueCompositionApi from '@vue/composition-api'
Vue.config.productionTip = false
Vue.use(VueCompositionApi)
new Vue({
  render: h => h(App),
}).$mount('#app') 
```

## 构建用户界面

我们需要一个组件，将容纳我们的应用程序的用户界面。在`src/components/`目录中创建一个新的`ShoppingList.vue`组件，并将以下内容粘贴到文件中:

```
<template>
  <section>
    <div class="form-container">
      <h2>Add Item</h2>
      <form>
        <div>
          <label>Product name</label>
          <br />
          <input type="text" />
        </div>
        <div>
          <button type="submit" class="submit">Add Item</button>
        </div>
      </form>
    </div>
    <div class="list-container">
      <ul>
        <li>
          Shopping List app
          <span style="float:right;padding-right:10px;">
            <button>X</button>
          </span>
        </li>
      </ul>
    </div>
  </section>
</template>
<script> export default {}; </script>
<style scoped> input {
  width: 20%;
  height: 30px;
  border: 2px solid green;
}
.submit {
  margin: 10px;
  padding: 10px;
  border-radius: 0px;
  border: 0px;
  background: green;
  color: white;
}
ul li {
  list-style: none;
  border: 2px solid green;
  width: 30%;
  margin-top: 10px;
} </style> 
```

上面的代码片段是我们 UI 的初始样板。我们现在将我们的新组件`ShoppingList.vue`导入到`App.vue`中，如下所示:

```
<template>
  <div id="app">
    <img alt="Shoppingd List" src="./assets/shopping.png">
    <shopping-list msg="Welcome to Your Vue.js App"/>
  </div>
</template>
<script> import ShoppingList from './components/ShoppingList.vue'
export default {
  name: 'App',
  components: {
    ShoppingList
  }
} </script>
<style> #app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
} </style> 
```

## 使用 Vue 合成 API

让我们从将组合 API 导入我们的组件`ShoppingList.Vue`开始:

```
<script> import { reactive } from "@vue/composition-api";
export default {}; </script> 
```

这里，我们从 Vue 组合 API 导入`reactive`。

现在，我们将通过将模板修改为以下形式，向应用程序添加一些事件侦听器:

```
<template>
  <section>
    <div class="form-container">
      <h2>My Shopping List App</h2>
      <form @submit.prevent="addItem">
        <div>
          <label> Product Name</label>
          <br />
          <input v-model="state.input" type="text" />
        </div>
        <div>
          <button type="submit" class="submit">Add Item</button>
        </div>
      </form>
    </div>
    <div class="list-container">
      <ul v-for="(Item,index) in state.Items" :key="index">
        <li>
          {{Item}}
          <span style="float:right;padding-right:10px;">
            <button @click="removeItem(index)">X</button>
          </span>
        </li>
      </ul>
    </div>
  </section>
</template> 
```

Vue 3 使用`data`和`methods`选项来定义状态和添加函数，而不是访问一个`setup`方法。这个方法在生命周期钩子`beforeCreate`之后、`create`之前被调用。

我们现在将在 setup 方法中声明`state`变量和方法，如下所示:

```
<script>
  import { reactive } from "@vue/composition-api";
  export default {
  setup() {
      const { state, addItem, removeItem } = ItemList();
      return { state, addItem, removeItem };
    }
  };
</script> 
```

声明变量和方法后，我们需要定义组件状态:

```
<script>
import { reactive } from "@vue/composition-api";
export default {
setup() {
  const { state, addItem, removeItem } = ItemList();
  return { state, addItem, removeItem };
}
};
function ItemList() {
let state = reactive({
  input: "",
  Items: ["Grocery"]
});
return { state };
</script> 
```

这里的状态作用于`ItemList`函数。我们定义了将绑定到输入字段的`input`状态，还定义了保存所有`Items`的`Items`数组。

让我们定义我们的`addItem`函数:

```
<script>
import { reactive } from "@vue/composition-api";
export default {
setup() {
    const { state, addItem, removeItem } = ItemList();
    return { state, addItem, removeItem };
  }
};
function ItemList() {
  let state = reactive({
    input: "",
    Items: ["Grocery"]
  });
 let addItem = () => {
    state.Items.push(state.input);
    state.input = "";
  };
  return { state, addItem };
</script> 
```

add 功能将获取输入字段中的数据，并使用 push 方法将其推入 Items 数组。

让我们实现删除一个项目。我们将获取`Item`的索引，然后使用拼接方法从用户数组中移除`Item`:

```
<script>
import { reactive } from "@vue/composition-api";
export default {
setup() {
    const { state, addItem, removeItem } = ItemList();
    return { state, addItem, removeItem };
  }
};
function ItemList() {
  let state = reactive({
    input: "",
    Items: ["Grocery"]
  });
 let addItem = () => {
    state.Items.push(state.input);
    state.input = "";
  };

 let removeItem = i => {
    state.Items.splice(i, 1);
  };
  return { state, addItem, removeItem };
</script> 
```

重要的是，我们希望能够从模板中访问的所有内容都由*`itemList`和`setup()`方法返回。在代码片段的最后，我们返回状态或方法。*

 *我们的购物清单应用程序现已完成:

![The shopping app ready to go](img/2ef64d00709623efd62cd3d4a43feb8b.png)

## 结论

最后，我们用 Vue 3 组合 API 构建了购物清单应用程序。看看 Vue 2 中如何使用 Composition API 很有意思。据我观察，它的一个主要优点是方法的处理，组件状态及其反应性变得更容易访问。

我希望你学到了一些关于 Vue 的东西。如果您有任何问题或反馈，请通过 [Twitter](https://twitter.com/devenrathore2) 联系我。

你可以随意添加新功能，因为这是一个很好的学习方式。你可以在这里查看源代码[。](https://github.com/Dunebook/ShoppingListapp)

## 分享这篇文章*