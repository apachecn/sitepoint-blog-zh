# 用 1KB JS 微框架 Hyperapp 建立一个待办事项列表

> 原文：<https://www.sitepoint.com/hyperapp-todo-list/>

在本教程中，我们将使用 Hyperapp 来构建一个待办事项应用程序。如果你想学习函数式编程原理，但又不想陷入细节，请继续阅读。

Hyperapp 现在很火。它最近在 GitHub 上超过了 *11，000* 颗星，并在 [2017 JavaScript 新星](https://risingstars.js.org/2017/en/#section-framework)的前端框架部分获得第 5 名。最近，当它推出 1.0 版本时，它也是 SitePoint 上的特色[。](https://www.sitepoint.com/hyperapp-1-kb-javascript-library/)

Hyperapp 受欢迎的原因可以归结为它的实用主义和超轻的大小(1.4 kB)，同时实现了类似 React 和 Redux 的开箱即用的效果。

## 那么，什么是 HyperApp 呢？

Hyperapp 允许您构建动态的单页面 web 应用程序，方法是利用虚拟 DOM 以类似的反应方式快速有效地更新网页上的元素。它还使用一个对象负责跟踪应用程序的状态，就像 Redux 一样。这使得管理应用程序的状态更容易，并确保不同的元素不会彼此不同步。Hyperapp 背后的主要影响是榆树建筑。

Hyperapp 的核心有三个主要部分:

*   **状态**。这是一个单一的对象树，存储了关于应用程序的所有信息。
*   **动作**。这些方法用于更改和更新状态对象中的值。
*   **查看**。这是一个返回编译成 HTML 代码的虚拟节点对象的函数。它可以使用 JSX 或类似的模板语言，并且可以访问`state`和`actions`对象。

这三个部分相互作用产生一个动态的应用程序。动作由页面上的事件触发。然后，该操作更新状态，然后触发对视图的更新。这些更改是对虚拟 DOM 进行的，Hyperapp 用它来更新 web 页面上的实际 DOM。

## 入门指南

为了尽快开始，我们将使用 CodePen 来开发我们的应用程序。您需要确保将 JavaScript 预处理器设置为 *Babel* ，并且使用以下链接将 Hyperapp 包作为外部资源加载:

```
https://unpkg.com/hyperapp 
```

要使用 Hyperapp，我们需要导入`app`函数和`h`方法，Hyperapp 用它来创建 VDOM 节点。将以下代码添加到 CodePen 中的 JavaScript 窗格中:

```
const { h, app } = hyperapp; 
```

我们将使用 JSX 作为视图代码。为了确保 Hyperapp 了解这一点，我们需要在代码中添加以下注释:

```
/** @jsx h */ 
```

`app()`方法用于初始化应用程序:

```
const main = app(state, actions, view, document.body); 
```

它将`state`和`actions`对象作为它的前两个参数，`view()`函数作为它的第三个参数，最后一个参数是 HTML 元素，应用程序将被插入到您的标记中。按照惯例，这通常是由`document.body`代表的`<body>`标签。

为了便于开始，我在 CodePen 上创建了一个样板 Hyperapp 代码模板，其中包含了上面提到的所有元素。通过[点击这个链接](https://codepen.io/SitePoint/pen/aYJRYv)可以分叉。

## 你好 Hyperapp！

让我们用 Hyperapp 玩一玩，看看它是如何工作的。`view()`函数接受`state`和`actions`对象作为参数，并返回一个虚拟 DOM 对象。我们将使用 JSX，这意味着我们可以编写看起来更像 HTML 的代码。下面是一个返回标题的示例:

```
const view = (state, actions) => (
  <h1>Hello Hyperapp!</h1>
); 
```

这实际上将返回以下 VDOM 对象:

```
{
  name: "h1",
  props: {},
  children: "Hello Hyperapp!"
} 
```

每次`state`对象改变时，调用`view()`函数。Hyperapp 然后将基于已经发生的任何变化构建一个新的虚拟 DOM 树。Hyperapp 将通过比较新的虚拟 DOM 和存储在内存中的旧 DOM 的差异，以最有效的方式更新实际的 web 页面。

## 成分

组件是返回虚拟节点的纯函数。它们可以用来创建可重用的代码块，然后插入到视图中。它们可以像其他函数一样接受参数，但是它们不能像视图那样访问`state`和`actions`对象。

在下面的例子中，我们创建了一个名为`Hello()`的组件，它接受一个对象作为参数。在返回包含这个值的标题之前，我们使用析构从这个对象中提取出`name`值:

```
const Hello = ({name}) => <h1>Hello {name}</h1>; 
```

我们现在可以在视图中引用这个组件，就好像它是一个名为`<Hello />`的 HTML 元素。我们可以像向 React 组件传递属性一样向该元素传递数据:

```
const view = (state, actions) => (
  <Hello name="Hyperapp" />
); 
```

注意，当我们使用 JSX 时，组件名必须以大写字母开头或者包含一个句点。

## 状态

状态是一个普通的旧 JavaScript 对象，包含关于应用程序的信息。这是应用程序的“唯一真实来源”,只能通过操作来改变。

让我们为应用程序创建状态对象，并设置一个名为`name`的属性:

```
const state = {
  name: "Hyperapp"
}; 
```

view 函数现在可以访问该属性。将代码更新为以下内容:

```
const view = (state, actions) => (
  <Hello name={state.name} />
); 
```

由于视图可以访问`state`对象，我们可以使用它的`name`属性作为`<Hello />`组件的属性。

## 行动

动作是用于更新状态对象的函数。它们以一种特殊的形式编写，返回另一个接受当前状态并返回更新的部分状态对象的 curried 函数。这部分是出于风格上的考虑，但也确保了`state`对象保持不变。一个全新的`state`对象是通过将一个动作的结果与之前的状态合并而创建的。这将导致调用`view`函数并更新 HTML。

下面的例子展示了如何创建一个名为`changeName()`的动作。这个函数接受一个名为`name`的参数，并返回一个用于用这个新名称更新`state`对象中的`name`属性的简化函数。

```
const actions = {
  changeName: name => state => ({name: name})
}; 
```

为了看到这个动作，我们可以在视图中创建一个按钮，并使用一个`onclick`事件处理程序来调用这个动作，参数为“Batman”。为此，将`view`函数更新如下:

```
const view = (state, actions) => (
  <div>  <Hello name={state.name} />  <button onclick={() => actions.changeName('Batman')}>I'm Batman</button>  </div>
); 
```

现在试着点击按钮，看名字变了！

你可以在这里看到一个[活生生的例子](https://codepen.io/SitePoint/pen/JLWeXJ?editors=0010)。

## 超级列表

现在是时候建立一些更实质性的东西了。我们将构建一个简单的待办事项应用程序，允许您创建列表，添加新项目，将它们标记为完成和删除项目。

首先，我们需要在 CodePen 上开始一个新的笔。添加以下代码，或者简单地分叉我的 HyperBoiler 笔:

```
const { h, app } = hyperapp;
/** @jsx h */

const state = {

};

const actions = {

};

const view = (state, actions) => (

);

const main = app(state, actions, view, document.body); 
```

您还应该在 CSS 部分添加以下内容，并将其设置为 SCSS:

```
// fonts
@import url("https://fonts.googleapis.com/css?family=Racing+Sans+One");
$base-fonts: Helvetica Neue, sans-serif;
$heading-font: Racing Sans One, sans-serif;

// colors
$primary-color: #00caff;
$secondary-color: hotpink;
$bg-color: #222;

* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}

body {
  padding-top: 50px;
  background: $bg-color;
  color: $primary-color;
  display: flex;
  height: 100vh;
  justify-content: center;
  font-family: $base-fonts;
}

h1 {
  color: $secondary-color;
  & strong{ color: $primary-color; }
  font-family: $heading-font;
  font-weight: 100;
  font-size: 4.2em;
  text-align: center;
}

a{
  color: $primary-color;
}

.flex{

  display: flex;
  align-items: top;
  margin: 20px 0;

  input {
    border: 1px solid $primary-color;
    background-color: $primary-color;

    font-size: 1.5em;
    font-weight: 200;

    width: 50vw;
    height: 62px;

    padding: 15px 20px;
    margin: 0;
    outline: 0;

    &::-webkit-input-placeholder {
      color: $bg-color;
    }

    &::-moz-placeholder {
      color: $bg-color;
    }

    &::-ms-input-placeholder {
      color: $bg-color;
    }

    &:hover, &:focus, &:active {
      background: $primary-color;
    }
  }

  button {
    height: 62px;
    font-size: 1.8em;
    padding: 5px 15px;
    margin: 0 3px;
  }
}

ul#list {
  display: flex;
  flex-direction: column;
  padding: 0;
  margin: 1.2em;
  width: 50vw;
  li {
    font-size: 1.8em;
    vertical-align: bottom;
    &.completed{
      color: $secondary-color;
      text-decoration: line-through;
      button{
        color: $primary-color;
      }
    }
      button {
        visibility: hidden;
        background: none;
        border: none;
        color: $secondary-color;
        outline: none;
        font-size: 0.8em;
        font-weight: 50;
        padding-top: 0.3em;
        margin-left: 5px;
    }
    &:hover{
      button{
        visibility: visible;
      }
    }
  }
}

button {
    background: $bg-color;
    border-radius: 0px;
    border: 1px solid $primary-color;
    color: $primary-color;

    font-weight: 100;

    outline: none;

    padding: 5px;

    margin: 0;

    &:hover, &:disabled {
      background: $primary-color;
      color: #111;
    }
    &:active {
      outline: 2px solid $primary-color;
    }
    &:focus {
      border: 1px solid $primary-color;
    }
  } 
```

这些只是为应用程序添加了一点风格和 Hyperapp 品牌。

现在让我们开始构建实际的应用程序吧！

## 初始状态和视图

首先，我们将设置初始的`state`对象和一个简单的视图。

当创建初始的`state`对象时，考虑应用程序在整个生命周期中想要跟踪哪些数据和信息是很有用的。在我们的列表中，我们需要一个数组来存储待办事项，还需要一个字符串来表示输入实际待办事项的输入字段中的内容。这将如下所示:

```
const state = {
  items: [],
  input: '',
}; 
```

接下来，我们将创建`view()`函数。首先，我们将关注添加项目所需的代码。添加以下代码:

```
const view = (state, actions) => (
  <div>  <h1><strong>Hyper</strong>List</h1>  <AddItem add={actions.add} input={actions.input} value={state.input} />  </div>
); 
```

这将显示一个标题，以及一个名为`<AddItem />`的元素。这不是一个新的 HTML 元素，而是我们需要创建的一个组件。让我们现在就开始吧:

```
const AddItem = ({ add, input, value }) => (
  <div class='flex'>
    <input type="text" value={value}
           onkeyup={e => (e.keyCode === 13 ? add() : null)}
           oninput={e => input({ value: e.target.value })}
    />
    <button onclick={add}>＋</button>
  </div>
); 
```

这将返回一个用于输入待办事项的`<input>`元素，以及一个用于将待办事项添加到列表中的`<button>`元素。组件接受一个对象作为参数，我们从中提取三个属性:`add`、`input`和`value`。

正如您所料，`add()`函数将用于向我们的待办事项列表中添加一个项目。如果按下`Enter`键(它的`KeyCode`为 13)或点击按钮，就会调用该函数。`input()`函数用于更新当前状态项的值，并在文本字段接收到用户输入时调用。最后，`value`属性是用户在输入字段中输入的任何内容。

注意，`input()`和`add()`函数是动作，作为道具传递给`<AddItem />`组件:

```
<AddItem add={actions.add} input={actions.input} value={state.input} /> 
```

您还可以看到,`value`道具取自州的`input`属性。因此，显示在输入字段中的文本实际上存储在状态中，并在每次按键时更新。

为了将所有东西粘在一起，我们需要添加`input`动作:

```
const actions = {
  input: ({ value }) => ({ input: value })
} 
```

现在，如果您开始在输入字段中键入内容，您应该会看到它显示您正在键入的内容。这演示了 Hyperapp 循环:

1.  当用户在输入字段中键入文本时，触发`oninput`事件
2.  调用`input()`动作
3.  该动作更新状态中的`input`属性
4.  状态的变化导致调用`view()`函数并更新 VDOM
5.  然后对实际的 DOM 进行 VDOM 中的更改，并重新呈现页面以显示按下的键。

试一试，您应该会看到输入的内容出现在输入栏中。不幸的是，按下`Enter`或点击“+”按钮目前没有任何作用。这是因为我们需要创建一个向列表中添加项目的操作。

## 添加任务

在我们开始创建列表项之前，我们需要考虑如何表示它们。JavaScript 的对象符号非常完美，因为它允许我们将信息存储为键值对。我们需要考虑列表项可能具有哪些属性。例如，它需要一个值来描述需要做什么。它还需要一个属性来声明该项是否已经完成。一个例子可能是:

```
{
  value: 'Buy milk',
  completed: false,
  id: 123456
} 
```

注意，该对象还包含一个名为`id`的属性。这是因为 Hyperapp 中的 VDOM 节点需要一个唯一的键来标识它们。我们将为这个使用一个时间戳。

现在，我们可以尝试创建一个添加项目的操作。我们的第一项工作是将输入字段重置为空。这是通过将`input`属性重置为空字符串来完成的。然后我们需要向`items`数组添加一个新对象。这是使用`Array.concat`方法完成的。这与`Array.push()`方法类似，但是它返回一个新的数组，而不是改变它所作用的数组。请记住，我们希望创建一个新的状态对象，然后将其与当前状态合并，而不是简单地直接改变当前状态。`value`属性被设置为包含在`state.input`中的值，它代表在输入字段中输入的内容:

```
add: () => state => ({
  input: '',
  items: state.items.concat({
    value: state.input,
    completed: false,
    id: Date.now()
  })
}) 
```

注意，这个动作包含两种不同的状态。第二个函数的参数代表了当前的状态。还有*新的*状态，这是第二个函数的返回值。

为了演示这一点，让我们假设应用程序刚开始时有一个空的商品列表，用户在输入字段中输入文本“购买牛奶”并按下`Enter`，触发了`add()`动作。

在行动之前，状态看起来是这样的:

```
state = {
  input: 'Buy milk',
  items: []
} 
```

该对象作为参数传递给`add()`动作，该动作将返回以下状态对象:

```
state = {
  input: '',
  items: [{
    value: 'Buy milk',
    completed: false,
    id: 1521630421067
  }]
} 
```

现在我们可以在状态下给`items`数组添加条目，但是看不到！要解决这个问题，我们需要更新我们的视图。首先，我们需要创建一个组件来显示列表中的项目:

```
const ListItem = ({ value, id }) => <li id={id} key={id}>{value}</li>; 
```

这使用了一个`<li>`元素来显示一个值，该值作为一个参数提供。还要注意的是，`id`和`key`属性具有相同的值，这是该项目的惟一 ID。`key`属性由 Hyperapp 内部使用，因此不会显示在呈现的 HTML 中，因此使用`id`属性显示相同的信息也很有用，尤其是因为该属性共享相同的惟一性条件。

现在我们有了列表项的组件，我们需要实际显示它们。JSX 让这变得非常简单，因为它将遍历一个值数组并依次显示每个值。问题是`state.items`不包含 JSX 码，所以我们需要用`Array.map`把数组中的每个 item 对象都换成 JSX 码，就像这样:

```
state.items.map(item => ( <ListItem id={item.id} value={item.value} /> )); 
```

这将迭代`state.items`数组中的每个对象，并创建一个包含`ListItem`组件的新数组。现在我们只需要将它添加到视图中。将`view()`函数更新为以下代码:

```
const view = (state, actions) => (
  <div>  <h1><strong>Hyper</strong>List</h1>  <AddItem add={actions.add} input={actions.input} value={state.input} />  <ul id='list'>  { state.items.map(item => ( <ListItem id={item.id} value={item.value} /> )) }  </ul>  </div>
); 
```

这只是将新的`ListItem`组件数组放在一对`<ul>`标签中，因此它们显示为一个无序列表。

现在，如果您尝试添加项目，您应该看到它们出现在输入字段下方的列表中！

## 将任务标记为已完成

我们的下一项工作是能够切换待办事项的`completed`属性。点击一个未完成的任务应该将其`completed`属性更新为`true`，点击一个已完成的任务应该将其`completed`属性切换回`false`。

这可以通过使用以下操作来完成:

```
toggle: id => state => ({
  items: state.items.map(item => (
    id === item.id ? Object.assign({}, item, { completed: !item.completed }) : item
  ))
}) 
```

这一行动相当复杂。首先，它接受一个名为`id`的参数，这个参数指的是每个任务的唯一 ID。然后这个动作遍历数组中的所有条目，检查提供的 ID 值是否与每个列表条目对象的属性匹配。如果是，它使用求反运算符`!`将`completed`属性更改为与当前相反的属性。这种改变是通过使用`Object.assign()`方法实现的，该方法创建一个新的对象，并与旧的`item`对象和更新后的属性进行浅层合并。记住，我们*从不*直接更新状态中的对象。相反，我们创建一个新版本的状态，覆盖当前状态。

现在我们需要将这个动作连接到视图。我们通过更新`ListItem`组件来做到这一点，这样它就有一个`onclick`事件处理程序来调用我们刚刚创建的`toggle`动作。更新`ListItem`组件代码，如下所示:

```
const ListItem = ({ value, id, completed, toggle, destroy }) => (
  <li class={completed && "completed"} id={id} key={id} onclick={e => toggle(id)}>{value}</li>
); 
```

眼尖的人会发现组件获得了一些额外的参数，并且在`<li>`属性列表中也有一些额外的代码:

```
class={completed && "completed"} 
```

这是 Hyperapp 中的一种常见模式，用于在特定条件为真时插入额外的代码片段。如果`completed`参数为真，它使用*短路*或*惰性*评估将类设置为完成。这是因为当使用`&&`时，如果两个操作数都为真，那么操作的返回值将是第二个操作数。因为字符串`"completed"`总是为真，所以如果第一个操作数—`completed`参数为真，就会返回这个值。这意味着，如果任务已经完成，它将具有“已完成”类，并可以相应地进行样式化。

我们最后的工作是更新`view()`函数中的代码，将额外的参数添加到`<ListItem />`组件中:

```
const view = (state, actions) => (
  <div>  <h1><strong>Hyper</strong>List</h1>  <AddItem add={actions.add} input={actions.input} value={state.input} />  <ul id='list'>  {
        state.items.map(item => (
          <ListItem id={item.id} value={item.value} completed={item.completed} toggle={actions.toggle} />
        ))
      }  </ul>  </div>
); 
```

现在，如果您添加一些项目并尝试点击它们，您应该会看到它们被标记为完成，并有一条线穿过它们。再次单击，它们将恢复为未完成状态。

## 删除任务

我们的 list 应用程序目前运行得很好，但如果我们能删除列表中不再需要的任何项目，那就更好了。

我们的第一项工作是添加一个`destroy()`动作，该动作将从处于状态的`items`数组中删除一个项目。我们不能用`Array.slice()`方法做到这一点，因为这是一个作用于原始数组的破坏性方法。相反，我们使用`filter()`方法，该方法返回一个新数组，该数组包含通过指定条件的所有 item 对象。这个条件是`id`属性不等于作为参数传递给`destroy()`动作的 ID。换句话说，它返回一个新的数组，这个数组不包含我们想要删除的项。当状态更新时，这个新列表将替换旧列表。

将以下代码添加到`actions`对象中:

```
destroy: id => state => ({ items: state.items.filter(item => item.id !== id) }) 
```

现在我们必须再次更新`ListItem`组件来添加一个触发这个动作的机制。我们将通过添加一个带有`onclick`事件处理程序的按钮来实现这一点:

```
const ListItem = ({ value, id, completed, toggle, destroy }) => (
  <li class={completed && "completed"} id={id} key={id} onclick={e => toggle(id)}>  {value}  <button onclick={ () => destroy(id) }>x</button>  </li>
); 
```

请注意，我们还需要添加另一个名为`destroy`的参数，它表示单击按钮时我们想要使用的动作。这是因为组件不能像视图那样直接访问同一 was 中的`actions`对象，所以视图需要显式地传递任何动作。

最后，我们需要更新视图，将`actions.destroy`作为参数传递给`<ListItem />`组件:

```
const view = (state, actions) => (
  <div>  <h1><strong>Hyper</strong>List</h1>  <AddItem add={actions.add} input={actions.input} value={state.input} />  <ul id='list'>  {state.items.map(item => (
        <ListItem
          id={item.id}
          value={item.value}
          completed={item.completed}
          toggle={actions.toggle}
          destroy={actions.destroy}
        />
      ))}  </ul>  </div>
); 
```

现在，如果你添加一些项目到你的列表中，当你把鼠标放在它们上面时，你会注意到“x”按钮。点击这个，他们应该消失在以太中！

## 删除所有已完成的任务

我们将添加到 list 应用程序的最后一个功能是一次删除所有已完成任务的能力。这使用了我们之前使用的相同的`filter()`方法——返回一个数组，该数组只包含具有`completed`属性值`false`的项目对象。将以下代码添加到`actions`对象中:

```
clearAllCompleted: ({items}) => ({ items: items.filter(item => !item.completed) }) 
```

要实现这一点，我们只需添加一个带有`onclick`事件处理程序的按钮来调用视图底部的这个动作:

```
const view = (state, actions) => (
  <div>  <h1><strong>Hyper</strong>List</h1>  <AddItem add={actions.add} input={actions.input} value={state.input} />  <ul id='list'>  {state.items.map(item => (
        <ListItem
          id={item.id}
          value={item.value}
          completed={item.completed}
          toggle={actions.toggle}
          destroy={actions.destroy}
        />
      ))}  </ul>  <button onclick={() => actions.clearAllCompleted({ items: state.items })}> Clear completed items </button>  </div>
); 
```

现在尝试添加一些项目，将其中一些标记为完成，然后按下按钮将它们全部清除。厉害！

在 [CodePen](https://codepen.io) 上通过 SitePoint ( [@SitePoint](https://codepen.io/SitePoint) )看到笔[超链接列表](https://codepen.io/SitePoint/pen/XEMGpN/)。