# 了解 Vue 3 中的新反应性系统

> 原文：<https://www.sitepoint.com/vue-3-reactivity-system/>

反应系统是现代前端框架的关键部分之一。它们是让应用程序具有高度交互性、动态性和响应性的魔杖。理解什么是反应系统，以及如何在实践中应用它，对于每个 web 开发人员来说都是一项至关重要的技能。

反应系统是一种机制，*自动*保持数据源(模型)与数据表示(视图)层同步。每次模型更改时，视图都会重新渲染以反映这些更改。

让我们以[一个简单的 Markdown 编辑器](https://v3.vuejs.org/examples/markdown.html)为例。它通常有两个窗格:一个用于编写 Markdown 代码(修改底层模型)，另一个用于预览编译后的 HTML(显示更新后的视图)。当您在“书写”窗格中书写内容时，它会立即自动在“预览”窗格中预览。当然，这只是一个简单的例子。事情往往要复杂得多。

在许多情况下，我们想要显示的数据依赖于其他一些数据。在这种情况下，会跟踪依赖关系，并相应地更新数据。例如，假设我们有一个`fullName`属性，它依赖于`firstName`和`lastName`属性。当它的任何依赖项被修改时，`fullName`属性被自动重新评估，结果显示在视图中。

现在我们已经确定了什么是反应性，是时候了解新的 Vue 3 反应性是如何工作的，以及我们如何在实践中使用它。但在我们这样做之前，我们将快速看一下旧的 Vue 2 反应性及其警告。

## Vue 2 反应性初探

Vue 2 中的反应性或多或少是“隐藏”的。无论我们在`data`对象中放入什么，Vue 都会让它隐式地起反应。一方面，这使得开发人员的工作更容易，但另一方面，这导致灵活性降低。

在幕后，Vue 2 使用 ES5[object . define property()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/defineProperty)将所有`data`对象的属性转换为*getter*和*setter*。对于每个组件实例，Vue 创建一个依赖观察器实例。在组件渲染期间作为依赖项收集/跟踪的任何属性都由观察器记录。稍后，当依赖项的 setter 被触发时，观察器会得到通知，组件会重新呈现并更新视图。这就是所有魔术的基本原理。不幸的是，有一些警告。

### 变更检测警告

因为`Object.defineProperty()`的限制，有些数据变化 Vue 是检测不到的。其中包括:

*   向/从对象添加/删除属性(如`obj.newKey = value`)
*   按索引设置数组项(如`arr[index] = newValue`)
*   修改数组的长度(如`arr.length = newLength`)

幸运的是，为了处理这些限制，Vue 为我们提供了 [Vue.set](https://vuejs.org/v2/api/#Vue-set) API 方法，该方法向一个反应性对象添加一个属性，确保新属性也是反应性的，从而触发视图更新。

让我们在下面的例子中探究上述案例:

```
<div id="app">
  <h1>Hello! My name is {{ person.name }}. I'm {{ person.age }} years old.</h1>
  <button @click="addAgeProperty">Add "age" property</button>
  <p>Here are my favorite activities:</p>
  <ul>
    <li v-for="item, index in activities" :key="index">
      {{ item }}
      <button @click="editActivity(index)">Edit</button>
    </li>
  </ul>
  <button @click="clearActivities">Clear the activities list</button>
</div> 
```

```
const App = new Vue({
  el: '#app',
  data: {
    person: {
      name: "David"
    },
    activities: [
      "Reading books",
      "Listening music",
      "Watching TV"
    ]
  },
  methods: { 
    // 1\. Add a new property to an object
    addAgeProperty() {
      this.person.age = 30
    },
    // 2\. Setting an array item by index
    editActivity(index) {
      const newValue = prompt('Input a new value')
      if (newValue) {
        this.activities[index] = newValue
      }
    },
    // 3\. Modifying the length of the array
    clearActivities() { 
      this.activities.length = 0 
    }
  }
}); 
```

这里有一个 [CodePen 的例子](https://codepen.io/SitePoint/pen/NWdOZGd?editors=1010)。

在上面的例子中，我们可以看到三种方法都不起作用。我们不能向`person`对象添加新属性。我们不能通过使用索引来编辑来自`activities`数组的项目。而且我们不能修改`activities`数组的长度。

当然，这些情况都有解决方法，我们将在下一个示例中探索它们:

```
const App = new Vue({
  el: '#app',
  data: {
    person: {
      name: "David"
    },
    activities: [
      "Reading books",
      "Listening music",
      "Watching TV"
    ]
  },
  methods: { 
    // 1\. Adding a new property to the object
    addAgeProperty() {
      Vue.set(this.person, 'age', 30)
    },
    // 2\. Setting an array item by index
    editActivity(index) {
      const newValue = prompt('Input a new value')
      if (newValue) {
        Vue.set(this.activities, index, newValue)
      }
    },
    // 3\. Modifying the length of the array
    clearActivities() { 
      this.activities.splice(0)
    }
  }
}); 
```

这里有一个 [CodePen 的例子](https://codepen.io/SitePoint/pen/wvgYLMo?editors=1010)。

在这个例子中，我们使用`Vue.set` API 方法向`person`对象添加新的`age`属性，并从 activities 数组中选择/修改特定的项目。在最后一种情况下，我们只是使用 JavaScript 内置的`splice()`数组方法。

正如我们所见，这是可行的，但是它有点粗糙，会导致代码库的不一致。幸运的是，在 Vue 3 中，这个问题已经解决了。让我们在下面的例子中看看这种神奇的效果:

```
const App = {
  data() {
    return {
      person: {
        name: "David"
      },
      activities: [
        "Reading books",
        "Listening music",
        "Watching TV"
      ]
    }
  },
  methods: { 
    // 1\. Adding a new property to the object
    addAgeProperty() {
      this.person.age = 30
    },
    // 2\. Setting an array item by index
    editActivity(index) {
      const newValue = prompt('Input a new value')
      if (newValue) {
        this.activities[index] = newValue
      }
    },
    // 3\. Modifying the length of the array
    clearActivities() { 
      this.activities.length = 0 
    }
  }
}

Vue.createApp(App).mount('#app') 
```

这里有一个 [CodePen 的例子](https://codepen.io/SitePoint/pen/WNRaqwj?editors=1010)。

在这个使用 Vue 3 的例子中，我们恢复了第一个例子中使用的内置 JavaScript 功能，现在所有的方法都非常好用。

在 Vue 2.6 中，引入了一个 [Vue.observable()](https://vuejs.org/v2/api/#Vue-observable) API 方法。在某种程度上，它暴露了反应系统，允许开发人员显式地使对象反应。实际上，这与 Vue 内部使用的包装`data`对象的方法完全相同，对于为简单场景创建最小的跨组件状态存储非常有用。但是尽管它很有用，但是这种单一的方法无法与 Vue 3 附带的完整的、功能丰富的反应性 API 的能力和灵活性相匹配。我们将在下一节中看到原因。

*注意:因为`Object.defineProperty()`是一个只有 ES5 且不可填充的特性，Vue 2 不支持 IE8 及以下版本。*

## Vue 3 反应性如何工作

Vue 3 中的反应系统被完全重写，以便利用 ES6 [代理](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Proxy)和[反射](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Reflect)API。新版本公开了一个功能丰富的反应 API，使系统比以前更加灵活和强大。

代理 API 允许开发人员拦截和修改目标对象上的低级对象操作。代理是对象(称为*目标)*的克隆/包装器，并提供特殊的函数(称为*陷阱*)，这些函数响应特定的操作并覆盖 JavaScript 对象的内置行为。如果您仍然需要使用默认行为，您可以使用相应的反射 API，顾名思义，它的方法反映了代理 API 的方法。让我们探索一个例子，看看这些 API 是如何在 Vue 3 中使用的:

```
let person = {
  name: "David",
  age: 27
};

const handler = {
  get(target, property, receiver) {
    // track(target, property)
    console.log(property) // output: name
    return Reflect.get(target, property, receiver)
  },
  set(target, property, value, receiver) {
    // trigger(target, property)
    console.log(`${property}: ${value}`) // output: "age: 30" and "hobby: Programming"
    return Reflect.set(target, property, value, receiver)
  }
}

let proxy = new Proxy(person, handler);   

console.log(person)

// get (reading a property value)
console.log(proxy.name)  // output: David

// set (writing to a property)
proxy.age = 30;

// set (creating a new property)
proxy.hobby = "Programming";

console.log(person) 
```

这里有一个 [CodePen 的例子](https://codepen.io/SitePoint/pen/MWJPMeQ?editors=0011)。

为了创建一个新的代理，我们使用了`new Proxy(target, handler)`构造函数。它有两个参数:目标对象(`person`对象)和处理程序对象，后者定义了哪些操作将被拦截(`get`和`set`操作)。在`handler`对象中，我们使用`get`和`set`陷阱来跟踪何时读取属性以及何时修改/添加属性。我们设置控制台语句来确保这些方法正确工作。

`get`和`set`陷阱采用以下参数:

*   `target`:被代理包裹的目标对象
*   `property`:物业名称
*   `value`:属性值(该参数仅用于设置操作)
*   `receiver`:操作发生的对象(通常是代理)

反射 API 方法接受与其对应的代理方法相同的参数。它们用于实现给定操作的默认行为，对于`get`陷阱，返回属性名，对于`set`陷阱，如果设置了属性，则返回`true`，如果没有设置，则返回`false`。

注释的`track()`和`trigger()`函数是 Vue 特有的，用于跟踪何时读取属性以及何时修改/添加属性。因此，Vue 会重新运行使用该属性的代码。

在这个例子的最后一部分，我们使用一个控制台语句来输出原始的`person`对象。然后我们使用另一条语句来读取`proxy`对象的属性`name`。接下来，我们修改`age`属性并创建一个新的`hobby`属性。最后，我们再次输出`person`对象，以查看它已经被正确更新。

简单来说，这就是 Vue 3 反应性的工作原理。当然，真正的实现要复杂得多，但是希望上面给出的例子足以让你掌握主要思想。

当您使用 Vue 3 反应性时，还有几个注意事项:

*   它只能在支持 ES6+的浏览器上运行
*   反应代理不等于原始对象

## 探索 Vue 3 反应性 API

最后，我们得到了 Vue 3 反应 API 本身。在接下来的部分中，我们将探索分成逻辑组的 API 方法。我将方法分组，因为我认为以这种方式呈现更容易记忆。让我们从基础开始。

### 基本方法

第一组包括控制数据反应性的最基本方法:

*   `ref`接受一个原始值或一个普通对象，并返回一个反应式可变引用对象。ref 对象只有一个属性`value`，它指向原始值或普通对象。
*   获取一个对象并返回该对象的反应副本。转换是深层的，会影响所有嵌套的属性。
*   `readonly`接受一个引用或一个对象(普通的或反应式的),并将一个只读对象返回给原始对象。转换是深层的，会影响所有嵌套的属性。
*   `markRaw`返回对象本身，并防止它被转换为代理对象。

现在让我们来看看这些方法的实际应用:

```
<h1>Hello, Vue 3 Reactivity API! :)</h1>
<hr>
<p><strong>Counter:</strong> {{ counter }}</p>
<button @click="counter++">+ Increment counter</button>
<br><br>
<button @click="counter--">- Decrement counter</button>
<hr>
<h3>Hello! My name is <mark>{{ person.name }}</mark>. I'm <mark>{{ person.age }}</mark> years old.</h3>
<p>Edit person's name
  <input v-model="person.name" placeholder="name" /> and age
  <input v-model="person.age" placeholder="age" />
</p>
<hr>
<p><strong>PI:</strong> {{ math.PI }}</p>
<button @click="math.PI = 6.28">Double PI</button> <span>(The console output after the button is clicked: <em>"Set operation on key 'PI' failed: target is readonly."</em>)</span>
<hr>
<h3>Alphabet Numbers</h3>
<table>
  <tr>
    <th>Letter</th>
    <th>Number</th>
  </tr>
  <tr v-for="(value, key) in alphabetNumbers">
    <td>{{ key }}</td>
    <td>{{ value }}</td>
  </tr>
</table>
<br>
<button @click="alphabetNumbers.B = 3">Change the value of B to 3</button><span> (Actually the letter B <em>is</em> changed to number 3 - <button @click="showValue">Show the value of B</button>, but it's <em>not</em> tracked by Vue.)</span> 
```

```
import { ref, reactive, readonly, markRaw, isRef, isReactive, isReadonly, isProxy, onMounted } from 'vue';

export default {
  setup () {
    const counter = ref(0)
    const person = reactive({
      name: 'David',
      age: 36
    })
    const math = readonly({
      PI: 3.14
    })
    const alphabetNumbers = markRaw({
      A: 1,
      B: 2,
      C: 3
    })

    const showValue = () => {
      alert(`The value of B is ${alphabetNumbers.B}`)
    }

    onMounted(() => {
      console.log(isRef(counter)) // true
      console.log(isReactive(person)) // true
      console.log(isReadonly(math)) // true
      console.log(isProxy(alphabetNumbers)) // false
    })

    return {
      counter,
      person,
      math,
      alphabetNumbers,
      showValue
    }
  }
}; 
```

参见 [CodePen](https://codepen.io) 上 site point([@ site point](https://codepen.io/SitePoint))
编辑的 Pen [Vue 3 反应性 API 1。](https://codepen.io/SitePoint/pen/abpRgJX)