# 开始用 TypeScript 编写基于类的 Vue.js 应用程序

> 原文：<https://www.sitepoint.com/class-based-vue-js-typescript/>

去年 9 月，尤雨溪(Vue.js 的创建者)宣布了该库下一个主要版本的[计划。Vue 3.0 将为](https://medium.com/the-vue-point/plans-for-the-next-iteration-of-vue-js-777ffea6fabf) [TypeScript](https://www.sitepoint.com/typescript-tutorial-for-beginners/) 用户提供改进的体验，包括对基于类的组件的原生支持，以及在编写代码时对类型推断的更好支持。

好消息是，你不必等到 3.0 版本发布(预计在 2019 年第三季度)才开始用 TypeScript 编写 Vue 应用程序。Vue 的命令行工具 Vue CLI 提供了启动项目的选项，并预先配置了 TypeScript 构建工具，还包括官方支持的 [vue-class-component](https://github.com/vuejs/vue-class-component) 模块，允许您将 Vue 组件编写为 TypeScript 类。

本文假设您对 Vue 和 TypeScript 的基础知识有所了解。现在让我们来看看如何在代码中利用静态类型和基于类的组件。

## 启动 Vue + TypeScript 项目

开始使用 TypeScript 的一个障碍可能是配置必要的构建工具。谢天谢地，Vue 用 [Vue CLI](https://cli.vuejs.org/) 为我们做了介绍。我们可以用它来创建一个项目，其中 TypeScript 编译器已经设置好并准备就绪。

让我们简要地介绍一下如何创建一个支持 TypeScript 的新 Vue 项目。

从终端/命令行(假设您安装了 Node.js)，运行以下命令以全局安装 Vue CLI:

```
npm install -g @vue/cli 
```

接下来，让我们创建一个新项目，指定项目的名称:

```
vue create vue-typescript-demo 
```

这也将是项目安装到的子文件夹的名称。一旦你点击<key>回车</key>，你将被提示选择**默认**预设，或者手动选择你想要安装的选项。

选择 manual 选项，您将看到一组进一步的选项。很明显，基本选项是 TypeScript，但是您可能也想选择 Vuex，因为我们稍后将检查一些特定于 Vuex 的 decorators。

选择了项目选项后，下一个屏幕将询问您是否想要使用**类风格的组件语法**。答应这个吧。然后你会被询问是否要“将 Babel 和 TypeScript 一起用于自动检测的聚合填充”。对于支持旧浏览器的项目来说，这是一个好主意。回答您认为合适的其余问题，安装过程应该开始。

### 关于编辑器/IDE 支持的说明

许多代码编辑器和 ide 现在都支持 TypeScript。在付费解决方案中，JetBrains 软件(如 [WebStorm](https://www.jetbrains.com/webstorm/) 、 [PhpStorm](https://www.jetbrains.com/phpstorm/) )对 Vue 和 TypeScript 都有很好的支持。如果你正在寻找免费的替代品，我推荐微软的 [Visual Studio Code](https://code.visualstudio.com/) :结合 [Vetur](https://vuejs.github.io/vetur/) 扩展，它提供了强大的自动完成和类型检查功能。

## 基于类的组件

让我们从如何使用类编写 Vue 组件开始。虽然这一特性并不局限于 TypeScript，但是使用基于类的组件有助于 ts 提供更好的类型检查，并且在我看来，有助于创建更干净、更易维护的组件。

让我们来看看语法。如果你按照上一节的步骤，使用 Vue CLI 创建一个新项目，进入项目目录，进入 *src* 子文件夹，打开 *App.vue* 。这里我们感兴趣的是`<script>`部分，因为它是唯一不同于标准 Vue 单文件组件(SFC)的部分。

```
<script lang="ts">
import { Component, Vue } from 'vue-property-decorator';
import HelloWorld from './components/HelloWorld.vue';

@Component({
  components: {
    HelloWorld,
  },
})
export default class App extends Vue {}
</script> 
```

注意，`<script>`标签本身有一个 **ts** 集合的`lang`属性。这对于构建工具和编辑器将代码正确解释为 TypeScript 非常重要。

为了声明基于类的组件，需要创建一个扩展`vue`的类(这里是从`vue-property-decorator`包导入的，而不是直接从 vue 模块导入的)。

类声明之前需要有`@Component` decorator:

```
@Component
class MyComponent extends Vue {} 
```

正如您可能已经注意到的，在来自 **App.vue** 组件的代码中，装饰器也可以接受一个对象，该对象可以用于指定组件的`components`、`props`和`filters`选项:

```
@Component({
  components: { MyChildComponent },
  props: {
    id: {
      type: String,
      required: true
    }
  },
  filters: {
    currencyFormatter
  }
})
class MyComponent extends Vue {} 
```

### 数据属性

当声明基于对象的组件时，您将熟悉必须将组件的数据属性声明为返回数据对象的函数:

```
{
  data: () => ({
    todos: [],
  })
} 
```

…而对于基于类的组件，我们可以将数据属性声明为普通的类属性:

```
@Component
class TodoList extends Vue {
  todos: [];
} 
```

### 计算属性

使用类作为组件的另一个优点是使用 getter 方法声明计算属性的语法更简洁:

```
@Component
class TodoList extends Vue {
  // ...

  get uncompletedTodos() {
    return this.todos.filter(todo => todo.done === false);
  }
} 
```

同样，您可以使用 setter 方法创建可写的计算属性:

```
 set fullName(value: string) {
    let names = newValue.split(' ');
    this.firstName = names[0];
    this.lastName = names[names.length - 1];
  } 
```

### 方法

组件方法可以用类似的简洁方式声明，如类方法:

```
@Component
class TodoList extends Vue {
  // ...

  addTodo(text) {
    this.todos.push({ text, done: false });
  }
} 
```

在我看来，声明方法、数据属性和计算属性的简单语法使得编写和读取基于类的组件比最初的基于对象的组件更好。

## 装修工

我们可以更进一步，使用由 [vue-property-decorator](https://github.com/kaorun343/vue-property-decorator) 包提供的附加装饰器。它为创作基于类的组件提供了六个额外的装饰器:

*   [T2`@Emit`](https://github.com/kaorun343/vue-property-decorator#Emit)
*   [T2`@Inject`](https://github.com/kaorun343/vue-property-decorator#Provide)
*   [T2`@Model`](https://github.com/kaorun343/vue-property-decorator#Model)
*   [T2`@Prop`](https://github.com/kaorun343/vue-property-decorator#Prop)
*   [T2`@Provide`](https://github.com/kaorun343/vue-property-decorator#Provide)
*   [T2`@Watch`](https://github.com/kaorun343/vue-property-decorator#Watch)

让我们来看看其中三个，你可能会发现最有用的。

### @道具

您可以使用`@Props`装饰器将您的道具声明为类属性，而不是将`props`配置对象传递给`@Component`装饰器。

```
@Component
class TodoItem extends Vue {
  @Prop
  todo;
} 
```

与其他装饰器一样，`@Prop`可以接受各种参数，包括一个类型、一个类型数组或一个选项对象:

```
@Prop(String)
name;

@Prop([String, Null])
title;

@Prop({ default: true })
showDetails; 
```

当使用 with TypeScript 时，应该在属性名后面加上非空运算符(！idspnonenote)。)告诉编译器该属性将有一个非空值(因为 TS 不知道这些值将在组件初始化时被传递到组件中):

```
@Prop(String) name!: string; 
```

注意，如上所示，如果你愿意，把装饰器和属性声明放在一行是完全可以的。

### @Emit

另一个方便的装饰器是`@Emit`，它允许你从任何类方法中发出一个事件。发出的事件将使用该方法的名称(camelCase 名称被转换为 kebab-case)，除非将一个替代的事件名称传递给 decorator。

如果该方法返回一个值，它将作为事件的有效负载发出，同时还有传递给该方法的任何参数。

```
@Emit()
addTodo() {
  return this.newTodo;
} 
```

上面的代码将发出一个“add-todo”事件，以`this.newTodo`的值作为有效负载。

### @手表

用这个装饰器创建观察器很好也很简单。它有两个参数:被观察属性的名称和一个可选的 options 对象。

```
@Watch('myProp')
onMyPropChanged(val: string, oldVal: string) {
  // ...
}

@Watch('myObject', { immediate: true, deep: true })
onMyObjectChanged(val: MyObject, oldVal: MyObject) { } 
```

## 总结

我希望这篇文章向您展示了开始用 TypeScript 编写 Vue 应用程序并不是一件令人头疼的事情。通过使用 CLI 启动新项目，您可以快速设置必要的构建工具。包含的对基于类的组件的支持和额外的装饰器将让您立刻编写出干净、习惯的类型脚本！

*想从头学起 Vue.js？使用 SitePoint Premium 获得涵盖基础知识、项目、技巧和工具的 Vue 书籍的完整收藏&。[现在就加入，每月仅需 9 美元](https://www.sitepoint.com/premium/products/Z2lkOi8vbGVhcm5hYmxlL1Byb2R1Y3QvMzA3?ref_medium=home&ref_source=premium)或[试试我们的 7 天免费试用](https://www.sitepoint.com/premium/sign-up?step=0)。*

## 分享这篇文章