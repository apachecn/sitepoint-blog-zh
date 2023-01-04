# 普通 JavaScript 中的 DOM 操作基础(没有 jQuery)

> 原文：<https://www.sitepoint.com/dom-manipulation-vanilla-javascript-no-jquery/>

> 本文收录于我们的文集[现代 JavaScript](https://www.sitepoint.com/premium/books/modern-javascript) 。如果你想在一个地方获得所有现代 JavaScript 的速度，注册 SitePoint Premium 并下载一份。

![DOM Manipulation in Vanilla JavaScript](img/4e044a4813bb1ad56c3b0ede4cb01a23.png)

每当我们需要执行 DOM 操作时，我们都会很快使用 jQuery。然而，vanilla JavaScrpt DOM API 实际上凭借其自身的能力相当强大，并且自从 IE < 11 has been [正式放弃](https://www.microsoft.com/en-us/WindowsForBusiness/End-of-IE-support)以来，它现在可以毫无顾虑地使用了。

在本文中，我将演示如何用普通 JavaScript 完成一些最常见的 DOM 操作任务，即:

*   查询和修改 DOM，
*   修改类和属性，
*   倾听事件，还有
*   动画。

最后，我将向您展示如何创建您自己的超薄 DOM 库，您可以将它放入任何项目中。在这个过程中，您将了解到用普通 JS 进行 DOM 操作并不是一件难事，事实上许多 jQuery 方法在原生 API 中都有直接的对等物。

所以让我们开始吧…

## DOM 操作:查询 DOM

请注意:我不会详细解释 Vanilla DOM API，而只是浅尝辄止。在使用示例中，您可能会遇到我没有明确介绍的方法。在这种情况下，只需参考优秀的 [Mozilla 开发者网络](https://developer.mozilla.org/en-US/)了解详情。

可以使用`.querySelector()`方法查询 DOM，该方法将一个任意的 CSS 选择器作为参数:

```
const myElement = document.querySelector('#foo > div.bar') 
```

这将返回第一个匹配项(深度优先)。相反，我们可以检查一个元素是否匹配一个选择器:

```
myElement.matches('div.bar') === true 
```

如果我们想得到所有的事件，我们可以使用:

```
const myElements = document.querySelectorAll('.bar') 
```

如果我们已经有了对父元素的引用，我们可以只查询该元素的子元素，而不是整个`document`。像这样缩小上下文后，我们可以简化选择器并提高性能。

```
const myChildElemet = myElement.querySelector('input[type="submit"]')

// Instead of
// document.querySelector('#foo > div.bar input[type="submit"]') 
```

那为什么还要使用其他不太方便的方法，比如`.getElementsByTagName()`？嗯，一个重要的区别是，`.querySelector()`的结果是*而不是*，所以当我们动态添加一个匹配选择器的元素(详见[第 3 节](#modifyingthedom))时，集合不会更新。

```
const elements1 = document.querySelectorAll('div')
const elements2 = document.getElementsByTagName('div')
const newElement = document.createElement('div')

document.body.appendChild(newElement)
elements1.length === elements2.length // false 
```

另一个考虑是，这样的动态集合不需要预先拥有所有的信息，而`.querySelectorAll()`会立即将所有信息收集到一个静态列表中，这使得[的性能不如](https://www.nczonline.net/blog/2010/09/28/why-is-getelementsbytagname-faster-that-queryselectorall/)。

### 与小说家合作

现在有两个关于`.querySelectorAll()`的常见问题。第一个问题是，我们不能在结果上调用节点方法并将它们传播到它的元素(就像您可能从 jQuery 对象中使用的那样)。相反，我们必须显式地迭代这些元素。这是另一个问题:返回值是一个节点列表，而不是一个数组。这意味着通常的数组方法不能直接使用。有一些相应的 NodeList 实现，比如`.forEach`，但是仍然不被任何 IE 支持。所以我们必须先把列表转换成数组，或者从数组原型“借用”那些方法。

```
// Using Array.from()
Array.from(myElements).forEach(doSomethingWithEachElement)

// Or prior to ES6
Array.prototype.forEach.call(myElements, doSomethingWithEachElement)

// Shorthand:
[].forEach.call(myElements, doSomethingWithEachElement) 
```

每个元素还有几个引用“family”的不言自明的只读属性，所有这些属性都是活动的:

```
myElement.children
myElement.firstElementChild
myElement.lastElementChild
myElement.previousElementSibling
myElement.nextElementSibling 
```

由于[元素](https://developer.mozilla.org/en-US/docs/Web/API/element)接口继承了[节点](https://developer.mozilla.org/en-US/docs/Web/API/Node)接口，因此还可以使用以下属性:

```
myElement.childNodes
myElement.firstChild
myElement.lastChild
myElement.previousSibling
myElement.nextSibling
myElement.parentNode
myElement.parentElement 
```

前者仅引用元素，后者(除了`.parentElement`)可以是任何类型的节点，例如文本节点。然后我们可以检查给定节点的[类型](https://developer.mozilla.org/en-US/docs/Web/API/Node/nodeType)，例如

```
myElement.firstChild.nodeType === 3 // this would be a text node 
```

与任何对象一样，我们可以使用`instanceof`操作符检查节点的原型链:

```
myElement.firstChild.nodeType instanceof Text 
```

## 修改类和属性

修改元素类非常简单:

```
myElement.classList.add('foo')
myElement.classList.remove('bar')
myElement.classList.toggle('baz') 
```

你可以在 Yaphi Berhanu 的这篇[快速技巧文章中阅读关于如何修改类的更深入的讨论。可以像访问任何其他对象的属性一样访问元素属性](https://www.sitepoint.com/add-remove-css-class-vanilla-js/)

```
// Get an attribute value
const value = myElement.value

// Set an attribute as an element property
myElement.value = 'foo'

// Set multiple properties using Object.assign()
Object.assign(myElement, {
  value: 'foo',
  id: 'bar'
})

// Remove an attribute
myElement.value = null 
```

注意还有方法`.getAttibute()`、`.setAttribute()`和。`removeAttribute()`。这些函数直接修改元素的 *HTML 属性*(与 DOM 属性相反)，从而导致浏览器重绘(您可以通过使用浏览器的开发工具检查元素来观察变化)。这种浏览器重绘不仅比设置 DOM 属性更昂贵，而且这些方法还会产生意想不到的结果。

根据经验，只对没有相应 DOM 属性的属性(比如`colspan`)使用它们，或者如果你真的想将这些更改“持久化”到 HTML 中(比如在克隆一个元素或者修改其父元素的`.innerHTML` —参见[第 3 节](#modifyingthedom))时保留它们)。

### 添加 CSS 样式

CSS 规则可以像任何其他属性一样应用；不过请注意，JavaScript 中的属性是驼峰式大小写的:

```
myElement.style.marginLeft = '2em' 
```

如果我们想要某些值，我们可以通过`.style`属性获得这些值。然而，这只会给我们显式应用的样式。为了得到计算出的值，我们可以使用`.window.getComputedStyle()`。它获取元素并返回一个 [CSSStyleDeclaration](https://developer.mozilla.org/en-US/docs/Web/API/CSSStyleDeclaration) ，其中包含元素本身的所有样式以及从其父元素继承的样式:

```
window.getComputedStyle(myElement).getPropertyValue('margin-left') 
```

## 修改 DOM

我们可以像这样移动元素:

```
// Append element1 as the last child of element2
element1.appendChild(element2)

// Insert element2 as child of element 1, right before element3
element1.insertBefore(element2, element3) 
```

如果我们不想移动元素，而是插入一个副本，我们可以像这样克隆它:

```
// Create a clone
const myElementClone = myElement.cloneNode()
myParentElement.appendChild(myElementClone) 
```

`.cloneNode()`方法可选地接受一个布尔值作为参数；如果设置为 true，将创建一个*深度*副本，这意味着它的子代也被克隆。

当然，我们也可以创建全新的元素或文本节点:

```
const myNewElement = document.createElement('div')
const myNewTextNode = document.createTextNode('some text') 
```

然后我们可以如上图所示插入它。如果我们想删除一个元素，我们不能直接这样做，但是我们可以从父元素中删除子元素，就像这样:

```
myParentElement.removeChild(myElement) 
```

这为我们提供了一个很好的解决方案，意味着实际上可以通过引用父元素来间接移除元素:

```
myElement.parentNode.removeChild(myElement) 
```

### 元素属性

每个元素也有属性`.innerHTML`和`.textContent`(还有`.innerText`，类似于`.textContent`，但有一些[重要区别](http://perfectionkills.com/the-poor-misunderstood-innerText/))。它们分别保存 HTML 和纯文本内容。它们是可写属性，意味着我们可以直接修改元素及其内容:

```
// Replace the inner HTML
myElement.innerHTML = ` <div>
    <h2>New content</h2>
    <p>beep boop beep boop</p>
  </div> `

// Remove all child nodes
myElement.innerHTML = null

// Append to the inner HTML
myElement.innerHTML += ` <a href="foo.html">continue reading...</a>
  <hr/> ` 
```

如上所示将标记附加到 HTML 通常不是一个好主意，因为我们会丢失之前对受影响的元素所做的任何属性更改(除非我们将这些更改作为 HTML 属性持久化，如[第 2 节](#modifyingclassesandattributes)所示)和绑定的事件侦听器。设置`.innerHTML`有利于完全丢弃标记并用其他东西替换它，例如服务器呈现的标记。所以追加元素最好这样做:

```
const link = document.createElement('a')
const text = document.createTextNode('continue reading...')
const hr = document.createElement('hr')

link.href = 'foo.html'
link.appendChild(text)
myElement.appendChild(link)
myElement.appendChild(hr) 
```

然而，使用这种方法，我们会导致两次浏览器重绘——每个附加元素一次——而改变`.innerHTML`只会导致一次。作为解决这个性能问题的一种方法，我们可以首先将所有节点组装到一个[文档片段](https://developer.mozilla.org/en-US/docs/Web/API/DocumentFragment)中，然后只添加一个片段:

```
const fragment = document.createDocumentFragment()

fragment.appendChild(text)
fragment.appendChild(hr)
myElement.appendChild(fragment) 
```

## 收听事件

这可能是绑定事件侦听器的最佳方式:

```
myElement.onclick = function onclick (event) {
  console.log(event.type + ' got fired')
} 
```

但是这通常应该避免。这里，`.onclick`是元素的一个属性，这意味着您可以更改它，但是不能使用它来添加额外的侦听器——通过重新分配一个新函数，您将覆盖对旧函数的引用。

相反，我们可以使用强大得多的`.addEventListener()`方法来添加尽可能多类型的事件。它有三个参数:事件类型(比如`click`)、每当事件发生在元素上时被调用的函数(该函数传递一个事件对象)和可选的配置对象(将在下面进一步解释)。

```
myElement.addEventListener('click', function (event) {
  console.log(event.type + ' got fired')
})

myElement.addEventListener('click', function (event) {
  console.log(event.type + ' got fired again')
}) 
```

在监听器函数中，`event.target`指的是触发事件的元素(就像`this`一样，当然，除非我们使用的是[箭头函数](https://www.sitepoint.com/es6-arrow-functions-new-fat-concise-syntax-javascript/))。因此，您可以像这样轻松地访问它的属性:

```
// The `forms` property of the document is an array holding
// references to all forms
const myForm = document.forms[0]
const myInputElements = myForm.querySelectorAll('input')

Array.from(myInputElements).forEach(el => {
  el.addEventListener('change', function (event) {
    console.log(event.target.value)
  })
}) 
```

### 防止默认操作

注意`event`在 listener 函数中总是可用的，但是最好在需要的时候以任何方式明确地传递它(当然，我们可以随意命名它)。不详细说明[事件](https://developer.mozilla.org/en-US/docs/Web/API/Event)接口本身，一个特别值得注意的方法是`.preventDefault()`，它将阻止浏览器的默认行为，比如跟随一个链接。另一个常见的用例是，如果客户端表单验证失败，有条件地阻止表单的提交。

```
myForm.addEventListener('submit', function (event) {
  const name = this.querySelector('#name')

  if (name.value === 'Donald Duck') {
    alert('You gotta be kidding!')
    event.preventDefault()
  }
}) 
```

另一个重要的事件方法是`.stopPropagation()`，它将防止事件在 DOM 中冒泡。这意味着，如果我们在一个元素上有一个停止传播的 click 侦听器，并且在它的一个父元素上有另一个 click 侦听器，那么在子元素上触发的 click 事件不会在父元素上触发——否则，它会在两个元素上都被触发。

现在`.addEventListener()`将一个可选的 config 对象作为第三个参数，它可以具有以下任意布尔属性(所有属性都默认为`false`):

*   `capture`:在 DOM 中，该元素的事件将在它下面的任何其他元素之前被触发(事件捕获和冒泡是一篇独立的文章，更多细节请看这里的
*   正如你可能猜到的，这表明该事件只会被触发一次
*   `passive`:这意味着`event.preventDefault()`将被忽略(并且通常会在控制台中产生一个警告)

最常见的选项是`.capture`；事实上，这种情况非常普遍，有一种简单的方法:不用在 config 对象中指定它，只需在这里传入一个布尔值:

```
myElement.addEventListener(type, listener, true) 
```

可以使用`.removeEventListener()`移除事件侦听器，它接受事件类型和对要移除的回调函数的引用；例如，`once`选项也可以这样实现

```
myElement.addEventListener('change', function listener (event) {
  console.log(event.type + ' got triggered on ' + this)
  this.removeEventListener('change', listener)
}) 
```

### 事件委托

另一个有用的模式是*事件委托*:假设我们有一个表单，想要为它的所有`input`子表单添加一个`change`事件监听器。一种方法是使用如上所示的`myForm.querySelectorAll('input')`对它们进行迭代。然而，当我们可以将它添加到表单本身并检查`event.target`的内容时，这是不必要的。

```
myForm.addEventListener('change', function (event) {
  const target = event.target
  if (target.matches('input')) {
    console.log(target.value)
  }
}) 
```

这种模式的另一个优点是，它还会自动考虑动态插入的子节点，而不必将新的侦听器绑定到每个子节点。

## 动画

通常，执行动画最干净的方式是应用带有`transition`属性的 CSS 类，或者使用 CSS `@keyframes`。但是如果你需要更多的灵活性(比如游戏)，这也可以用 JavaScript 来实现。

天真的方法是让一个`window.setTimeout()`函数调用自己，直到所需的动画完成。然而，这低效地迫使快速文档回流；这种*布局抖动*会很快导致口吃，尤其是在移动设备上。相反，我们可以使用`window.requestAnimationFrame()`来同步更新，将所有当前的更改安排到下一个浏览器重画帧。它接受一个回调作为接收当前(高分辨率)时间戳的参数:

```
const start = window.performance.now()
const duration = 2000

window.requestAnimationFrame(function fadeIn (now)) {
  const progress = now - start
  myElement.style.opacity = progress / duration

  if (progress < duration) {
    window.requestAnimationFrame(fadeIn)
  }
} 
```

这样我们可以实现非常流畅的动画。关于更详细的讨论，请看看马克·布朗的这篇文章。

## 编写自己的助手方法

的确，与 jQuery 简洁且可链接的`$('.foo').css({color: 'red'})`语法相比，总是需要迭代元素来做一些事情可能相当麻烦。那么，为什么不简单地为这类事情编写我们自己的速记方法呢？

```
const $ = function $ (selector, context = document) {
const elements = Array.from(context.querySelectorAll(selector))

  return {
    elements,

    html (newHtml) {
      this.elements.forEach(element => {
        element.innerHTML = newHtml
      })

      return this
    },

    css (newCss) {
      this.elements.forEach(element => {
        Object.assign(element.style, newCss)
      })

      return this
    },

    on (event, handler, options) {
      this.elements.forEach(element => {
        element.addEventListener(event, handler, options)
      })

      return this
    }

    // etc.
  }
} 
```

这样我们就有了一个超级精简的 DOM 库，里面只有我们真正需要的方法，没有向后兼容的负担。通常我们在集合的原型中会有这些方法。这里有一个(更详细的)要点以及一些如何实现这种助手的想法。或者，我们可以保持简单

```
const $ = (selector, context = document) => context.querySelector(selector)
const $$ = (selector, context = document) => context.querySelectorAll(selector)

const html = (nodeList, newHtml) => {
  Array.from(nodeList).forEach(element => {
    element.innerHTML = newHtml
  })
}

// And so on... 
```

## 演示

作为本文的结尾，这里有一个 CodePen，它演示了上面解释的许多概念，以实现一个简单的 lightbox 技术。我鼓励你花些时间浏览源代码，如果你有任何意见或问题，请在下面的评论中告诉我。

通过 [CodePen](http://codepen.io) 上的 SitePoint ( [@SitePoint](http://codepen.io/SitePoint) )看笔 [Tightbox](http://codepen.io/SitePoint/pen/aJaggB/) 。