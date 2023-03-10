# 为了乐趣和利益而编写 JavaScript:第一部分

> 原文：<https://www.sitepoint.com/javascript-fun-profit-part-1/>

在过去的几年里，JavaScript 已经成为 web 开发和设计体验的重要组成部分。它可以让我们美化枯燥、静态的页面，避免页面刷新，并完成一些令人惊叹的界面工程壮举——这些事情仅仅使用 HTML 和 CSS 是不可能的。当然，Ajax 和 DOM 脚本现在被看作是家常便饭，是每个 web 开发人员在构建网站时的工具包的一部分。但是我们能把它推到什么程度呢？它是一种强大的面向对象的语言，有丰富的输出机制，所以我们当然可以用它来启动弹出窗口以外的东西。

那么任何一个自尊的极客在面对这样的问题时会怎么做呢？他们写了一个 2-D，侧滚平台游戏，当然！

在这个由两部分组成的系列中，您将学到足够多的 HTML、CSS 和 JavaScript，使您能够构建自己的 JavaScript 平台游戏。我在示例中使用了[原型](http://www.prototypejs.org/) JavaScript 库，仅仅是因为它是我所知道的——许多其他可用的 JavaScript 库可能都有相当的功能。

在我们开始有趣的东西之前，我们需要运行一些高级的 JavaScript 技术，这些技术将允许我们欺骗您的浏览器认为它是一个 8 位游戏控制台。

##### 建筑 101

JavaScript (JS)是一种原型化的面向对象编程(OOP)语言。这意味着我们可以将构造——例如，一个视频游戏角色——表示为代码中的一个对象。如果您熟悉一些更传统的 OOP 语言，构建一个 JS 类可能看起来有点奇怪。首先，不像 Ruby 那样所有东西都是对象，JS 中的所有东西都是数据类型。这些数据类型有一个内部数据类型——称为原型——告诉数据类型如何表现。因此，我们需要以这样的方式定义该类:

1.  知道它是一个类

3.  可以被创建并初始化为定义的初始状态

让我们来看一些 JS 代码，它们构建了一个新类，然后创建了一个新对象:

```
// Declare the class 

function WalkingSprite(element, x, y) { 

  this.x = x; 

  this.y = y; 

  this.element = element; 

} 

WalkingSprite.prototype = { 

  x: 0, 

  y: 0, 

  element: null, 

  walk: function(direction) { 

    this.x += direction; 

  } 

} 

koopa = new WalkingSprite(null, 10, 10); 

koopa.walk(20); 

alert(koopa.x + "," + koopa.y);
```

粗略浏览一下这段代码，可以看到我们已经构建了一个名为`WalkingSprite`的新类，它有三个属性(`element`、`x`和`y`)和一个名为`walk`的函数。如果我们*实例化*对象的新版本并调用它`walk`函数，我们的`koopa`对象现在将位于坐标点(`20, 30`)。以这种方式声明类有点麻烦——我们必须创建一个类，然后更新原型。幸运的是，Prototype(库)已经将它封装到一个名为`Class.create`的便利函数中。上面的代码变成了这样:

```
var WalkingSprite = Class.create({ 

  x: 0, 

  y: 0, 

  element: null, 

  initialize: function(element, x, y) { 

    this.element = element; 

    this.x = x; 

    this.y = y; 

  }, 

  walk: function(steps) { 

    this.x += steps; 

  } 

}); 

koopa = new WalkingSprite(null, 10, 10); 

koopa.walk(20); 

alert(koopa.x + "," + koopa.y);
```

##### 使用类继承

OOP 的另一个基本组件是继承的概念。基本上，如果你有一个包含某些变量和函数的基类，那么所有继承了*的类*继承了*这些变量和函数。然后，您可以添加额外的函数，甚至覆盖这些函数来做其他事情。这在我们的游戏中可能非常有用，因为我们所有的角色可能会表现出一些共同的属性——他们可能都能够在屏幕上行走——但可能只有一种类型的角色可以跳跃。听起来是继承的完美候选人。*

不幸的是，JavaScript 本身不支持继承。那么，为什么我浪费了最后一段来告诉你呢？嗯，通过一点小技巧，我们可以在 JavaScript 中模拟类继承。

因为 JavaScript 中的一切(包括类中的函数)都是变量，所以我们可以将它们的值赋给其他变量。所以，如果我们考虑一下什么是继承，我们需要做的就是模仿它，从父类复制属性和函数到子类。如果我们想继承上面创建的类，我们可以这样做:

```
// Declare the class 

function WalkingSprite(element, x, y) { 

  this.x = x; 

  this.y = y; 

  this.element = element; 

} 

WalkingSprite.prototype = { 

  x: 0, 

  y: 0, 

  element: null, 

  walk: function(direction) { 

    this.x += direction; 

  } 

} 

// Create the child class 

JumpingAndWalkingSprite = WalkingSprite; 

JumpingAndWalkingSprite.prototype = { 

  x: 0, 

  y: 0, 

  walk: WalkingSprite.prototype.walk 

  jump: function() { 

    y += 20; 

  } 

}
```

运行代码，您将得到一个新的类，它具有来自其父类的两个属性和一个函数，以及一个新函数:`jump`。唯一的问题是，像这样的编码并不能真正扩展；如果在父类中添加一个`duck`函数会怎么样？您必须遍历每个子类并添加函数签名。再一次，原型来拯救！我们之前学过的`Class.create`函数可以把另一个类作为它的第一个参数。这个提供的类将成为父类，它将动态地为我们找到所有的属性和函数，自动地将它们注入子类。所以上面会变成:

```
var JumpingAndWalkingSprite = Class.create(WalkingSprite); 

mario = new JumpingAndWalkingSprite(null, 10, 10); 

mario.walk(10): 

alert(mario.x + "," + mario.y); 

mario.jump(); 

alert(mario.x + "," + mario.y); 
```

不出所料，新类具有父类的所有相同属性！那么添加和覆盖属性和函数呢？我们在上面演示了如何手动完成这项工作，但是 Prototype 允许我们使用`Class.create`定义新的函数:

```
var JumpingAndWalkingSprite = Class.create(WalkingSprite, { 

  walk: function($super, steps) { 

    $super(steps * 2); 

  }, 

  jump: function() { 

    this.y += 20; 

  } 

}); 
```

这里，我们覆盖了`walk`函数并添加了一个`jump`函数。等等——倒车——那个`$super`变量是从哪里冒出来的？好问题！当使用继承时，运行父类版本的函数有时会很有用。在这种情况下，我们通过将输入变量加倍，并将这个新值传递给父类，使角色行走两倍于最初请求的距离。如果您将`$super`声明为函数签名的第一个参数，Prototype 将在`$super`变量中提供函数的父类版本。这使您可以轻松地从被覆盖的版本中调用函数的父版本。您会注意到新的`jump`函数没有`$super`变量；我们不用，所以不需要供应。如果我们确实需要它，我们可以把它作为函数签名的第一个参数。

##### 通过类名定义行为

现在我们已经写好了 JavaScript 类，如果我们可以通过给它一个特定的类名来告诉一个 HTML 元素成为一个`WalkingSprite`对象，这不是很酷吗？在 JavaScript 1.6 中，使用`document.getElementByClassName`函数可以很容易地找到具有某个类名的所有 DOM 元素。但是，大多数浏览器还不支持 1.6 版本。幸运的是，Prototype 为我们提供了`$$`函数——传递给它一个 CSS 选择器，它将返回所有匹配元素的数组。

看一下下面的代码:

```
var WalkingSprite = Class.create({  

  x: 0,  

  y: 0,  

  element: null,  

  initialize: function(element) {  

    this.element = element,  

    this.x = element.offsetLeft,  

    this.y = element.offsetTop  

  },  

  walk: function(steps) {  

    this.x += steps;  

  }  

});  

var KoopaSprite = Class.create(WalkingSprite, {});  

var koopas = new Array();  

var koopaElements = $$('koopa');  

for(el in koopaElements) {  

  koopas.push(new KoopaSpriteSprite(el));  

}
```

首先我们创建了`WalkingSprite`类，然后是使用`WalkingSprite`类作为其父类的`KoopaSprite`类。接下来，我们通过选择文档中类名为“koopa”的所有元素来创建一个`KoopaSprite`对象数组。

现在，我们有了一个由`KoopaSprite`对象组成的数组，引用了相应的 DOM 元素(这在后面变得很重要)。我们在这里所做的是*不引人注目的 JavaScript* 的基础。现在我们已经动态地找到了我们感兴趣的 HTML 元素，我们可以绑定事件(比如`onclick`和`onfocus`)，重新设计它们的样式，或者让它们消失！

##### 制作电影

因为我们不是在写一个文本驱动的冒险游戏，我们需要一种方式来激活我们的角色。这不仅仅是在屏幕上移动它们，这将在后面介绍。如果我们能让角色看起来像是在行走、跳跃或躲避，那也很好。为此，我们将调用一个老的 CSS 技巧:后台位置黑客。

这个想法很简单:我们建立一个图像带，形成我们的动画帧，然后通过将它们左右移动 *x* 个像素来循环播放。下面是一个背景图片示例:

![The background image for this demo](img/89075a56d559f1708ae284aca8b07d6c.png)

如你所见，我们在一幅图像中有 12 帧，每帧间隔 48 像素。如果我们有一个`mario`类的`div`，那么一些不同帧的 CSS 可能如下所示:

```
div.mario {  

  width: 45px;  

  height: 45px;  

  background-image: url(mario.gif);  

  background-repeat: no-repeat;  

  background-position: 0 0;  

}  

div.mario.jump-left {  

  background-position: -90px 0;  

}  

div.mario.duck-right {  

  background-position: -180px 0;  

}
```

你可能以前见过这种技术来创建无闪烁翻转。在过去，您可以使用一小段 JavaScript 创建图像翻转效果，当`onmouseover`事件触发时，这段 JavaScript 会改变图像标签的`src`值。然而，第一次这样做时，浏览器仍然需要从服务器下载图像，这经常会导致闪烁。可以预加载图像，但是有点笨重。卓越的 CSS 技术允许设计师在一个图像中加载所有的翻转状态，并使用`:hover`伪类创建一个单独的 CSS 规则来移动背景，无需 JavaScript 即可实现平滑过渡。

在我们的游戏引擎中，我们将使用 JavaScript 改变背景图片的位置。要在 JS 中设置背景位置，可以操作元素的`style.backgroundPosition`属性。下面的代码创建了一个名为`MarioSprite`的新类，它向父类`WalkingSprite`添加了一个渲染函数。这个新函数被重复调用，并有时间延迟，它将使用两帧来制作马里奥行走的动画:

```
var MarioSprite = Class.create(WalkingSprite, {  

  renderState: 0;  

  render: function() {  

    if(this.renderState == 0) {  

      this.element.backgroundPosition = '0px 0px';  

      this.renderState = 1;  

    } else {  

      this.element.backgroundPosition = '-48px 0px';  

      this.renderState = 0;  

    }  

  }  

});
```

##### 使用计时器

很明显，如果渲染函数在整个游戏中不被重复调用，它是没有用的。为了确保一秒钟触发几次，我们需要使用 JavaScript 计时器。有两种类型的计时器:一种将在计时器到期后触发一次，另一种将每隔 *t* 毫秒重复触发一次，直到我们告诉它停止。我们将使用`setInterval`函数实现后者:

```
mario = new MarioSprite(document.getElementById('mario');   

var timer = setInterval(function() { mario.render() }, 500);
```

这会让马里奥每秒走一步两次(500 毫秒等于半秒)。因为`setInterval`需要一个函数作为它的第一个参数，所以我们需要创建一个匿名函数来调用`mario.render`函数。

也许有必要解释一下 JS 的一个局限性，这个局限性稍后会反咬我们一口: **JavaScript 不是多线程的**。这意味着没有办法让两个代码块同时运行。你可以*通过设置一个间隔为一毫秒的单次定时器来中断*另一段代码，这会强制你的浏览器在下一个机会运行回调函数，但是被**中断的那段代码会被停止**，直到中断函数完成后才会继续执行。所以设置一个每一毫秒触发一次的定时器并不能保证你的函数会被那么快地调用。当我谈到这个循环时，我们会看到它的结果。

##### 允许用户输入

显然，游戏需要某种人类输入，无论是通过键盘、鼠标还是操纵杆。所以为了让我们的游戏不仅仅是静止的精灵在现场运行，我们需要对用户的输入做出反应；在 JavaScript 中，这被称为*事件监听*。
根据你的浏览器风格，有两种不同的事件模型(惊喜，惊喜)，尽管 Prototype 在封装两者的细微差别方面做得非常出色，但了解一下幕后发生的事情是值得的。

##### 泡泡

您可以选择事件是通过 DOM 向下移动到触发它的元素(事件捕获)，从元素向上移动(事件冒泡)，还是两者结合(官方的 W3C 模型)。下面是所发生情况的图示。那些在 Internet Explorer 领域的人受困于事件冒泡，而其他浏览器两者都支持。

如果您已经使用 Web 有一段时间了，那么您可能对使用属性如`onmouseover`或`onclick`的内联事件处理很熟悉。这种技术相当于在 CSS 中使用 style 属性——这是邪恶的，不要这样做。幸运的是，在 JavaScript 中有几种方法可以将事件动态绑定到元素。考虑以下代码:

```
function clicked() {   

  alert('You clicked me!');   

}   

function doubleclicked() {   

  alert('You double clicked me!');   

}   

var mario = document.getElementById('mario');   

var luigi = document.getElementById('luigi');   

var yoshi = document.getElementById('yoshi');   

mario.addEventListener('click', clicked, true);   

mario.addEventListener('doubleclick', doubleclicked, false);   

luigi.attachEvent('onclick', clicked);   

yoshi.onclick = clicked;
```

这里我们有三种不同的方法将事件附加到 DOM 中的元素。第一种——使用`addEventListener`——是 W3C 标准的做事方式；第一个参数是事件的名称，第二个是回调函数的名称，第三个是一个布尔值，它指示我们是在捕获(false)还是冒泡(true)。第二种——使用`attachEvent`——是 Internet Explorer 方式；它与 W3C 版本的签名基本相同，没有第三个参数，因为 IE 只支持事件冒泡。最后一种——使用元素的`onclick`属性——是一种适用于所有浏览器的方法。

像`mouseover`和`mouseout`这样的事件非常简单，但是键盘事件有点复杂，因为我们需要知道按下了什么键。在这种情况下，我们必须从 JavaScript `Event`对象中获取信息；要么将一个`Event`对象传递给回调函数，要么如果你在 IE 中，在窗口对象`window.event`中创建一个全局的`Event`对象，它包含我们需要的信息。

这里有一个例子:

```
function keypressHandler(e) {   

  e = window.event || e;   

  alert("Keycode: " + e.keyCode);   

}   

window.onkeypress = keypressHandler;
```

```
keypressHandler is our event callback function that is called when a keypress event is triggered. The first line represents a cross-browser method for obtaining the Event object. Once we have the Event object we can query the keyCode property and find out which key was pressed.正如我们所展示的，Prototype 使这类工作变得非常简单。Prototype 向`Event`对象添加了一些方法，为我们解决了所有跨浏览器的问题。我们可以将代码简化如下:

```
function keypressHandler(e) {   

  alert("Keycode: " + e.keyCode);   

}   

Event.observe(window, 'keypress', keypressHandler);
```

使用`Event.observe`设置我们的事件处理程序允许我们放弃条件测试，该测试通过函数参数或者从窗口事件检查我们是否有一个`Event`对象。Prototype 为我们无缝处理了这一切。
结论
至此，我们已经探索了 JavaScript 对象和类(包括继承之类的 OOP 概念)、如何使用 JavaScript 和 CSS 类来赋予元素行为、如何使用计时器来允许我们重复执行一项任务(如动画)以及监听事件的基础知识。这给了我们工具箱中足够的 JavaScript 来让我们进入构建平台游戏的核心。在下一篇文章中，我将介绍如何创建一个基本的碰撞引擎——动画循环——并向您展示一些滚动浏览器窗口的技巧，以获得 80 年代真实的侧滚效果。
同时，查看一下将上述理论付诸实践的[演示](https://i2.sitepoint.com/examples/js_game/demo.html)(提示:按下箭头键，看看会发生什么)。您可以下载本文的[代码档案——看看您能否自己扩展它，因为您需要了解本系列第二篇文章的内容。直到下次...](https://i2.sitepoint.com/examples/js_game/js_game_part_1.zip)

```

## 分享这篇文章