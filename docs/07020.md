# Node.js 事件和事件发射器

> 原文：<https://www.sitepoint.com/nodejs-events-and-eventemitter/>

Node.js 速度高的原因之一是它是围绕事件编码的。Node 不需要读取每个请求所需的所有文件(如 PHP)，只需启动服务器，初始化大部分变量，声明函数，然后等待事件发生。

虽然 Node.js 有一些有用的内置事件，比如 request 事件，但是如果能够创建我们自己的事件并自己触发它们，难道不是很有用吗？这就是我这篇文章要讲的。

首先，我将向您展示正常事件是如何发出的。举个例子，当一个人走进商店时，铃会响，表示他在。它的行为有点像[观察者模式](http://en.wikipedia.org/wiki/Observer_pattern),其中我们的事件就像主题，所有附加到事件的功能就像观察者。所以商店的例子:

```
var events = require('events');
var eventEmitter = new events.EventEmitter();

var ringBell = function ringBell()
{
  console.log('ring ring ring');
}
eventEmitter.on('doorOpen', ringBell);

eventEmitter.emit('doorOpen');
```

首先，我们加载[事件模块](http://nodejs.org/api/events.html)，它是 Node.js 核心的一部分。然后我们创建一个`EventEmitter`类的新实例(稍后我们将扩展它)。之后，我们将`ringBell`函数放入一个变量中，这样就可以这样调用它了。它只是在我们的控制台上打印出“铃铃铃”。

现在有趣的事情来了。我们将我们的`ringBell`函数添加到`doorOpen`事件的函数列表中。这是通过`eventEmitter.on()`方法完成的，第一个参数是事件，第二个参数是要添加的函数。这实际上并没有做什么，它只是注册了我们的函数。真正的魔法发生在那之后，当我们发出事件时。调用`emit()`方法将执行用`on`方法注册的所有函数。

这没什么意思，如果我们想让铃声响起，可以只调用那个函数。但这正是事件如此有趣的原因:您可以注册任意多的函数。

我们也可以这样做，例如:

```
eventEmitter.on('doorOpen', ringBell);
eventEmitter.on(‘doorOpen’, doSomething);
eventEmitter.on(‘doorOpen’, doSomethingElse);

eventEmitter.emit('doorOpen');
```

这也是可行的，并且会更多地使用 EventEmitter 提供给我们的功能。

我们也可以使用带参数的函数作为监听器:

```
eventEmitter.on(‘doorOpen’, function(ring)
{
    Console.log(ring);
}
eventEmitter.emit(‘doorOpen’, ‘ringeling’);
```

我们只是在`emit()`方法中传递参数。

虽然这非常强大，但是节点社区中的一个常见做法是从 eventEmitter 类继承。我们可以通过拥有一个门类来实现这一点，这个门类有一个发出`doorOpen`事件的`open()`方法。看一下这段代码:

```
var events = require('events');

function Door(colour) {
  this.colour = colour;
  events.EventEmitter.call(this);

  this.open = function()
  {
  this.emit('open');
  }
}

Door.prototype.__proto__ = events.EventEmitter.prototype;

var frontDoor = new Door('brown');

frontDoor.on('open', function() {
    console.log('ring ring ring');
  });
frontDoor.open();
```

在我们的`Door`对象的构造函数中，我们设置门的颜色，并且我们使用我们的 EventEmitter 对象的`call()`方法，它执行 EventEmitter 的构造函数方法。然后我们声明我们的 open 方法，它发出' open '事件。这一行:

```
Door.prototype.__proto__ = events.EventEmitter.prototype;
```

将所有 EventEmitter 属性复制到 Door 对象。

然后，我们创建我们的前门，这是一个门的实例，它有一个棕色的颜色。然后我们添加一个事件监听器，最后我们打开门，并在控制台上打印一条消息。我希望你们都能看到这个事件模块非常强大和有用！

最后，events 模块为我们提供了一种列出所有事件侦听器的方法，以及一种删除事件侦听器的方法。

```
var ring = function()
{
    console.log('ring');
}
frontDoor.on('open', ring);

console.log(require('util').inspect(frontDoor.listeners('open'))); // Outputs ring
```

您可以通过使用`listeners`属性来做到这一点。当然，这只在你没有使用匿名函数作为事件监听器时才有效。

如果我们愿意的话，我们可以把门上的铃拿掉:

```
frontDoor.removeListener('open', ring);
```

或者我们甚至可以删除所有的监听器:

```
frontDoor. .removeAllListeners(‘open’);
```

感谢你阅读本指南，我希望你已经学到了一些东西。下次见！

## 分享这篇文章