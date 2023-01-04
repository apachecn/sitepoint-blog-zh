# JavaScript 设计模式:观察者模式

> 原文：<https://www.sitepoint.com/javascript-design-patterns-observer-pattern/>

**在 JavaScript 中，有一个经常出现的问题。您需要一种方法来使用这些事件提供的数据更新页面的某些部分以响应某些事件。比方说，您将用户输入投射到一个或多个组件中。这导致了代码中的大量推拉操作，以保持一切同步。**

这就是观察者设计模式可以帮助的地方——它支持元素之间的一对多数据绑定。这种单向数据绑定可以是事件驱动的。使用这种模式，您可以构建可重用的代码来满足您的特定需求。

在本文中，我将探讨观察者设计模式。它将帮助您解决在客户端脚本中常见的问题。也就是一对多、单向、事件驱动的数据绑定。当您有许多必须同步的元素时，这是一个经常出现的问题。

我将使用 ECMAScript 6 来说明这个模式。是的，会有类、箭头函数和常量。如果您还不熟悉这些主题，请随意探索。我将使用 ES6 中只引入语法糖的部分，所以如果需要的话，它可以移植到 ES5 中。

我将使用测试驱动开发(TDD)来处理这个模式。这样你就有办法知道每个组件是如何有用的。

ES6 中的新语言特性使得代码更加简洁。那么，我们开始吧。

## 事件观察者

模式的高级视图如下所示:

```
EventObserver
│ 
├── subscribe: adds new observable events
│ 
├── unsubscribe: removes observable events
|
└── broadcast: executes all events with bound data 
```

在我充实了观察者模式之后，我将添加一个使用它的字数统计。字数统计组件将获取该观察器并将其汇总在一起。

要初始化`EventObserver` do:

```
class EventObserver {
  constructor() {
    this.observers = [];
  }
} 
```

从观察到的事件的空列表开始，对每个新实例都这样做。从现在开始，让我们在`EventObserver`中添加更多的方法来充实设计模式。

### 订阅方法

要添加新事件，请执行以下操作:

```
subscribe(fn) {
  this.observers.push(fn);
} 
```

获取观察到的事件的列表，并将一个新项目推送到数组中。事件列表是回调函数的列表。

用普通 JavaScript 测试该方法的一种方法如下:

```
// Arrange
const observer = new EventObserver();
const fn = () => {};

// Act
observer.subscribe(fn);

// Assert
assert.strictEqual(observer.observers.length, 1); 
```

我使用[节点断言](https://nodejs.org/api/assert.html)来测试 Node 中的这个组件。与[完全相同的断言存在，柴的断言](http://chaijs.com/api/assert/)也存在。

注意观察到的事件列表由简单的回调组成。然后，我们检查列表的长度，并断言回调在列表上。

### 退订方法

要删除事件，请执行以下操作:

```
unsubscribe(fn) {
  this.observers = this.observers.filter((subscriber) => subscriber !== fn);
} 
```

从列表中过滤出匹配回调函数的内容。如果不匹配，回调将留在列表中。过滤器返回一个新列表，并重新分配观察者列表。

要测试这个好方法，请执行以下操作:

```
// Arrange
const observer = new EventObserver();
const fn = () => {};

observer.subscribe(fn);

// Act
observer.unsubscribe(fn);

// Assert
assert.strictEqual(observer.observers.length, 0); 
```

回调必须匹配列表中的同一个函数。如果有匹配，unsubscribe 方法会将其从列表中删除。注意，测试使用函数引用来添加和删除它。

### 该广播方法

要调用所有事件，请执行以下操作:

```
broadcast(data) {
  this.observers.forEach((subscriber) => subscriber(data));
} 
```

这将遍历观察到的事件列表并执行所有回调。这样，您就获得了订阅事件的必要的一对多关系。您传入了使回调数据绑定的参数`data`。

ES6 用一个箭头函数使代码更有效。注意完成大部分工作的`(subscriber) => subscriber(data)`函数。这个单行箭头函数受益于这个简短的 ES6 语法。这无疑是 JavaScript 编程语言的一大进步。

要测试这种广播方法，请执行以下操作:

```
// Arrange
const observer = new EventObserver();

let subscriberHasBeenCalled = false;
const fn = (data) => subscriberHasBeenCalled = data;

observer.subscribe(fn);

// Act
observer.broadcast(true);

// Assert
assert(subscriberHasBeenCalled); 
```

使用`let`代替`const`,这样我们可以改变变量的值。这使得变量可变，允许我在回调中重新赋值。在你的代码中使用一个`let`向其他程序员发出一个信号，这个变量在某个点上正在改变。这增加了 JavaScript 代码的可读性和清晰性。

这个测试给了我必要的信心，以确保观察器按照我的预期工作。有了 TDD，一切都是关于用普通的 JavaScript 构建可重用的代码。用普通 JavaScript 编写可测试代码有很多好处。测试一切，保留对代码重用有益的东西。

这样，我们就充实了`EventObserver`。问题是，你能用这个造出什么？

## 观察者模式的应用:博客字数统计演示

对于演示来说，是时候发表一篇博客文章了，它会为你记录字数。你输入的每一个按键都会被观察者设计模式同步。可以把它想象成自由文本输入，每个事件都会更新你需要它去的地方。

要从自由文本输入中获得字数，可以这样做:

```
const getWordCount = (text) => text ? text.trim().split(/\s+/).length : 0; 
```

搞定了。在这个看似简单的纯函数中发生了很多事情，那么一个不起眼的单元测试怎么样？这样，我的意图就很清楚了:

```
// Arrange
const blogPost = 'This is a blog \n\n  post with a word count.     ';

// Act
const count = getWordCount(blogPost);

// Assert
assert.strictEqual(count, 9); 
```

注意`blogPost`中有些古怪的输入字符串。我打算让这个函数覆盖尽可能多的边缘情况。只要它给我一个适当的字数，事实上，我们正朝着正确的方向前进。

作为旁注，这是 TDD 的真正力量。人们可以重复这个实现，并覆盖尽可能多的用例。单元测试告诉你我期望它如何表现。如果行为有缺陷，无论什么原因，很容易迭代和调整它。有了这个测试，就有足够的证据让其他人做出改变。

是时候将这些可重用的组件连接到 DOM 了。在这一部分，您可以使用普通的 JavaScript 并将其直接融入到浏览器中。

一种方法是在页面上显示以下 HTML:

```
<textarea id="blogPost" placeholder="Enter your blog post..." class="blogPost">
</textarea> 
```

接下来是这段 JavaScript:

```
const wordCountElement = document.createElement('p');

wordCountElement.className = 'wordCount';
wordCountElement.innerHTML = 'Word Count: <strong id="blogWordCount">0</strong>';
document.body.appendChild(wordCountElement);

const blogObserver = new EventObserver();

blogObserver.subscribe((text) => {
  const blogCount = document.getElementById('blogWordCount');

  blogCount.textContent = getWordCount(text);
});

const blogPost = document.getElementById('blogPost');

blogPost.addEventListener('keyup', () => blogObserver.broadcast(blogPost.value)); 
```

将所有可重用的代码放在适当的位置，使用观察者设计模式。这将跟踪文本区域的变化，并给你一个字数统计。我使用 DOM API 中的`body.appendChild()`来添加这个新元素。然后，附加事件侦听器来赋予它生命。

注意使用[箭头函数](https://www.sitepoint.com/javascript-arrow-functions/)可以连接一行程序事件。事实上，您用这个向所有订阅者广播事件驱动的更改。这里的大部分工作都是由`() => blogObserver.broadcast()`完成的。它甚至将对文本区域的最新更改直接传递给回调函数。是的，客户端脚本非常酷。

没有一个演示是完整的，没有一个你可以触摸和调整，下面是代码笔:

通过[码笔](https://codepen.io)上的 SitePoint ( [@SitePoint](https://codepen.io/SitePoint) )看笔[观察者图案](https://codepen.io/SitePoint/pen/rzBEXP/)。