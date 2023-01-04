# 移动 Web 应用程序:加载页面

> 原文：<https://www.sitepoint.com/mobile-web-apps-loading-pages/>

这是由[厄尔·卡斯尔丁](http://twitter.com/#!/mrspeaker)、[迈尔斯·埃夫托斯](http://twitter.com/#!/madpilot)和[马克斯·惠勒](http://twitter.com/#!/makenosound)所著的《为智能设备构建移动网站和应用》一书的第四个摘录。BuildMobile 正在独家发布这本书的一个完整章节，关于移动网络应用的章节，这一节叫做“加载页面”。

## 4.加载页面

既然我们已经学会了操作触摸屏的基本知识，并且通过优化我们的链接和表单获得了一些立竿见影的效果，那么是时候卷起袖子，着手开发几乎任何移动 web 应用程序的最大组件了。除非您的应用程序非常简单，否则您可能需要不止一个页面，因此，您需要考虑如何在页面之间切换。正如我们之前讨论的，我们的客户不太可能对我们应用程序中当前存在的类似 web 的页面重新加载留下深刻印象，所以我们需要一种方法来对用户隐藏请求/响应周期。我们有三种主要方法可以做到这一点:

1.  将所有内容放在一个页面上，然后根据需要隐藏和显示各个部分
2.  通过 Ajax 加载新页面
3.  包括应用程序的完整框架，然后根据需要引入数据

您采用的方法将在很大程度上取决于应用程序。我们将从第一种(也是最简单的)方法开始，预先加载我们所有的内容。这将让我们看看如何处理不同状态之间的转换。当然，对于一个依赖用户位置获取最新信息的真正应用程序，你可能会选择另外两种方法中的一种；我们将在这一章的最后看它们。

### 4.1.交换页面

如果我们所有的内容都加载在一个 HTML 页面中，那么从应用程序的角度来看，一个“页面”就不再是一个完整的 HTML 文档；它仅仅是一个我们用作容器的 DOM 节点。我们需要选择一个合适的容器和一种合适的方式来分组我们的页面，这样我们的脚本就可以一致地操作它们。

我们将从创建一个容器`div`(称为`pages`)开始，它包含了许多实际页面的子`div`元素。一次只能有一个页面可见，所以我们给这个元素一个`current`的`class`。该`class`将被传递给当前页面:

```
<div id="pages"> <div id="page-spots" class="current"> <!-- Spots Index --> </div> <div id="page-spot"> <!-- Spot Detail --> </div> <div id="page-sightings"> <!-- Add Sighting Form --> </div> <div id="page-stars"> <!-- Stars Index --> </div> <div id="page-star"> <!-- Star Detail --> </div> </div>
```

这个页面列表将位于标签栏下面——所以不需要改变我们的导航标记。然而，我们通过 id 属性将链接连接起来，指向各个部分。这将让我们在下一步中使用一个巧妙的技巧来显示页面:

```
<ul id="tab-bar"> <li> <a href="#spots">Spots</a> </li> <li> <a href="#sightings">Add a sighting</a> </li> <li> <a href="#stars">Stars</a> </li> </ul>
```

之后，我们需要一些隐藏和显示页面的样式。在我们的标记中，每个页面都是主`#pages`容器的第一级子容器，所以我们可以依赖这个事实并使用子选择器(`>`)。首先，我们将隐藏所有页面；然后我们将取消隐藏有`current class`的页面:

**例 4.15。`stylesheets/transitions.css`(节选)**

```
#pages > div { display: none; } #pages > div.current { display: block; }
```

为了实际选择一些页面，我们需要拦截导航菜单的点击。我们将使用之前编写的代码来捕获事件，并阻止浏览器导航到该链接:

**例 4.16。`javascripts/ch4/07-swap.js`(节选)**

```
$("#tab-bar a").bind('click', function(e) { e.preventDefault(); // Swap pages! });
```

这里有一个技巧:链接通过使用哈希符号(`#`)的锚语法指向我们的页面元素，后跟一个片段标识符。碰巧 jQuery 使用完全相同的语法通过`id`来选择元素，所以我们可以将`click`事件的哈希属性直接导入 jQuery 来选择目标页面。非常狡猾:

**例 4.17。`javascripts/ch4/07-swap.js`(节选)**

```
$("#tab-bar a").bind('click', function(e) { e.preventDefault(); var nextPage = $(e.target.hash); $("#pages .current").removeclass("current"); nextPage.addclass("current"); });
```

获得目标页面后，我们可以通过移除`current class`并将其传递给目标页面来隐藏当前页面。页面之间的交换现在可以像预期的那样工作，但是有一个小问题:当您导航到另一个页面时，标签栏中的选定图标不会改变。回头看看我们的 CSS，你会记得标签栏的出现是由于在包含的`ul`元素上设置了一个`class`;这是一个与当前页面`div`元素的`id`相同的`class`。所以我们需要做的就是从我们的字符串中切掉散列符号(使用`slice(1)`删除第一个字符)，并将其设置为`ul`的类:

**例 4.18。`javascripts/ch4/07-swap.js`(节选)**

```
$("#tab-bar a").bind('click', function(e) { e.preventDefault(); var nextPage = $(e.target.hash); $("#pages .current").removeclass("current"); nextPage.addclass("current"); $("#tab-bar").attr("className", e.target.hash.slice(1)); });
```

### 4.2.使用 WebKit 动画淡化

我们刚刚实现的页面交换非常简单。这有它的好处——首先，它不妨碍我们的用户。也就是说，页面间恰当的过渡不仅让你的应用程序更性感，还能为用户提供一个清晰的视觉提示，告诉他们要去哪里。

在最初的 iPhone 发布后，web 开发人员迅速在 JavaScript 中重新实现了原生过渡效果，但结果并不理想，经常包含非常明显的滞后和跳跃，让用户分心。解决方案很大程度上是抛弃移动大型 DOM 元素的 JavaScript，转而使用新的硬件加速的 CSS3 过渡和动画。

然而，在我们担心过渡之前，我们需要打下一些基础。为了随意使用 DOM 元素，我们需要能够随意显示、隐藏和定位它们:

**例 4.19。`stylesheets/transitions.css`(节选)**

```
#pages { position: relative; } #pages > div { display:none; position: absolute; top: 0; left: 0; width: 100%; }
```

通过绝对定位元素，我们将每一页都移到了左上角，给了我们一叠整洁的看不见的卡片，我们现在可以随意移动并制作动画。它们并非都是不可见的，尽管记得在我们的 HTML 中，我们给了默认页面`current`的`class`，它将其显示属性设置为`block`。

这次的不同之处在于，我们将把 CSS 动画应用到页面上。传入(新)页面和传出(当前)页面将被施加大小相等方向相反的力，以产生平滑的效果。为此需要三个步骤:

*   设置 CSS 动画。
*   通过在页面上设置适当的类来触发动画。
*   动画完成后，删除不需要的类，并返回到非动画状态。

让我们从 CSS 开始。有许多方法可以解决模拟本机页面转换的问题。我们将采用一种灵活的方法，这种方法改编自 jQTouch 库。这是一种模块化的方法，我们通过在每个页面上应用和删除动画的相关部分来控制过渡。

不过，在我们深入研究之前，先快速了解一下 CSS3 动画。这些目前仅在带有`-webkit-`供应商前缀的 WebKit 浏览器中受支持。CSS3 动画由一系列关键帧组成，这些关键帧组合成一个命名动画，使用`@-webkit-keyframes`规则创建。然后，我们使用-webkit-animation-name 属性将该动画应用于一个元素。我们还可以分别使用-webkit-animation-duration 和-WebKit-animation-timing-function 属性来控制动画的持续时间和缓和程度。如果你是动画新手，这可能会让你感到有点困惑；没关系，一旦你在实践中看到它，它会更清晰。

因此，让我们将一些动画应用到我们的元素中。首先，我们将为我们的动画设置一个计时功能和持续时间。这些规定了转场需要多长时间，以及页面从起点到终点是如何放松的:

**例 4.20。`stylesheets/transitions.css`(节选)**

```
.in, .out { -webkit-animation-timing-function: ease-in-out; -webkit-animation-duration: 300ms; }
```

我们将这些属性放在通用类中，这样我们就可以在将来创建的任何动画中重用它们。

接下来，我们需要创建我们的关键帧。首先，让我们简单地淡入新页面:

**例 4.21。`stylesheets/transitions.css`(节选)**

```
@-webkit-keyframes fade-in { from { opacity: 0; } to { opacity: 1; } }
```

在上面的规则中，`fade-in`是动画的名称，每当我们想要使用这些关键帧来制作一个元素的动画时，我们都会引用它。from 和 to 关键字允许我们声明动画的开始点和结束点，它们可以包含您想要制作动画的任意数量的 CSS 属性。如果您想要在开始和结束之间有更多关键帧，您可以用百分比来声明它们，如下所示:

**例 4.22。`stylesheets/transitions.css`(节选)**

```
@-webkit-keyframes fade-in-out { from { opacity: 0; } 50% { opacity: 1; } to { opacity: 0; } }
```

声明关键帧后，我们可以将它们与之前的方向类结合起来，以创建最终的效果。对于我们的淡入淡出，我们将使用上面定义的动画，并翻转页面上的`z-index`以确保正确的页面在前面:

**例 4.23。`stylesheets/transitions.css`(节选)**

```
.fade.in { -webkit-animation-name: fade-in; z-index: 10; } .fade.out { z-index: 0; }
```

通过声明-webkit-animation-name，我们告诉浏览器，只要一个元素匹配这个选择器，它就应该开始已命名的动画。

有了这个 CSS，我们就可以进入第二步了。我们首先将我们的动画应用到一个单独的导航项目上，然后扩大它，这样它就可以为我们的整个标签栏工作。

我们渐隐到的页面(`#sightings`)需要添加三个不同的类:current 使页面可见，fade 添加我们的动画，in 应用我们的计时功能和持续时间。我们正在淡出的页面(`#spots`)是可见的，所以它已经有了当前的类；我们只需要添加`fade`和`out`类:

```
var fromPage = $("#spots"), toPage = $("#sightings"); $("#tab-sighting a").click(function(){ toPage.addclass("current fade in"); fromPage.addclass("fade out"); });
```

当我们点击“添加一个景点”标签时，这给了我们一个很好的淡入淡出效果，但是现在页面被一个接一个的堆叠在一起。这是因为那些`class`名称仍然存在，所以页面现在有了`current`，它们都是可见的。是时候移除它们了！我们将通过绑定到`webkitAnimationEnd`事件来做到这一点，该事件在转换完成时触发。当这个事件触发时，我们可以从原始页面中删除所有三个类，并从新页面中删除`fade`和`in`类。此外，我们必须记住解除`webkitAnimationEnd`事件的绑定，这样我们就不会在下一次从页面中淡出时添加额外的处理程序:

```
var fromPage = $("#spots"), toPage = $("#sightings"); $("#tab-sighting a").click(function(){ toPage .addclass("current fade in") .bind("webkitAnimationEnd", function(){ // More to do, once the animation is done. fromPage.removeclass("current fade out"); toPage .removeclass("fade in") .unbind("webkitAnimationEnd"); }); fromPage.addclass("fade out"); });
```

我们走吧。我们的页面现在褪色很好；然而，我们的代码存在一些问题。首先是结构性的。如果我们必须为每一组我们想要转换到的页面复制相同的点击处理程序，这将变得非常难看！为了补救这一点，我们将创建一个名为`transition()`的函数，它将接受一个页面选择器，并从当前页面渐变到所提供的新页面。

在这个过程中，我们可以用 jQuery 的`one()`方法替换我们的`bind()`和`unbind()`调用。这个方法将完成同样的任务——它绑定一个事件，然后在它第一次被触发时解除绑定——但是它看起来更简洁:

**例 4.24。`javascripts/ch4/08-fade.js`(节选)**

```
function transition(toPage) { var toPage = $(toPage), fromPage = $("#pages .current"); toPage .addclass("current fade in") .one("webkitAnimationEnd", function(){ fromPage.removeclass("current fade out"); toPage.removeclass("fade in") }); fromPage.addclass("fade out"); }
```

### 一般化函数

您可能会发现我们在函数中硬编码了当前页面选择器。这使得我们的代码更小，但是降低了函数的可重用性。如果您正在构建一个更大的框架用于更广泛的用途，您可能也想接受来自 Page 的*作为参数。*

太好了。现在我们有了一个可重复使用的功能，可以在应用程序的任何页面之间淡入淡出。我们可以像以前一样将链接目标从标签栏中拉出，突然之间，每一次页面交换都变成了美丽的淡入淡出:

**例 4.25。`javascripts/ch4/08-fade.js`(节选)**

```
$("#tab-bar a").click(function(e) { e.preventDefault(); var nextPage = $(e.target.hash); transition(nextPage); $("#tab-bar").attr("className", e.target.hash.slice(1)); });
```

但是，仍然存在一个主要问题，如果您试图在缺乏动画支持的浏览器(如 Firefox)上测试这段代码，就会注意到这个问题。因为我们依靠`webkitAnimationEnd`事件从旧页面中移除`current class`，所以不支持动画的浏览器——因此永远不会触发该事件——永远不会隐藏原始页面。

### 浏览器测试

这个错误——它会使应用程序在非 WebKit 浏览器上完全不可用——突出了在尽可能多的浏览器上测试代码的重要性。虽然可以很容易地假设每个移动浏览器都包含最新版本的 WebKit(尤其是如果你拥有 iPhone 或 Android)，但真实的移动场景要多得多。

这个问题很容易解决。在我们的`transition()`函数的结尾，我们将放入一些特性检测代码，这些代码将在没有动画的情况下处理简化的页面交换:

**例 4.26。`javascripts/ch4/08-fade.js`(节选)**

```
function transition(toPage) { ... // For non-animatey browsers if(!("WebKitTransitionEvent" in window)){ toPage.addclass("current"); fromPage.removeclass("current"); return; } }
```

有了这些代码，我们的应用程序现在可以在 WebKit 浏览器上产生漂亮的淡入淡出过渡，但在其他浏览器上仍然可以有效地交换页面。

仍然有一个轻微的错误行为，如果你变得有点兴奋并开始疯狂点击，你可能会注意到它。如果你点击当前页面的链接——或者如果你快速点击以开始一个动画，而前一个还没有完成——我们用来管理应用程序状态的`class`属性将会处于不一致的状态。最终，我们会以有`current class`的*无*页面结束——此时我们会盯着一个空白屏幕。

防范这些情况相对容易。我们只需要确保我们的`toPage`不同于我们的`fromPage`，并且它上面还没有`current class`。这种保护措施出现在变量声明之后，任何类操作之前:

**例 4.27。`javascripts/ch4/08-fade.js`(节选)**

```
function transition(toPage) { var toPage = $(toPage), fromPage = $("#pages .current"); if(toPage.hasclass("current") || toPage === fromPage) { return; }; ...
```

### 4.3.滑

太棒了——我们的第一个动画过渡已经开始运行了！让我们继续下一个:一个极其常见的主从页面移动交互，比如我们的景点和明星列表。在移动设备上，详细信息页面从屏幕右侧滑入是很常见的，就好像它一直隐藏在那里一样。当用户返回到主页时，转换反向进行，为用户提供应用程序信息层次结构的清晰视觉模型。

既然我们已经学会了淡入淡出，那么创建一个幻灯片或“推”式过渡就非常容易了——我们只需要更新我们的 CSS 动画。要执行推送，我们需要将当前页面从屏幕向左移动，同时将新页面从右边移入。这比褪色更棘手，但也仅仅是。

首先，我们将创建一个移动当前屏幕的动画。我们将使用-webkit-transform 属性通过`translateX`命令来动画显示 div 元素的水平位置。

### CSS 转换

如果你不熟悉 CSS3 转换，不要担心。它们只是一种简单的方法，可以在元素呈现时对其位置和形状进行操作。除了我们在这里使用的 translateX 转换之外，您还可以访问 translateY(不足为奇)、translate(用于 X 轴和 Y 轴转换)以及旋转、倾斜和缩放功能。关于它们的完整列表和使用示例，请查看 W3C 的 CSS 2D 变换模块。

屏幕从 X=0 开始，然后通过平移到–100%移出视线。这是一个巧妙的把戏。如果我们平移一个负值，屏幕会向左移动。向左移动 100%，屏幕就消失了。一旦我们定义了关键帧，我们就可以将动画分配给`.push.out`选择器(记住我们现有的`out class`为我们的动画提供了默认的持续时间和计时功能):

**例 4.28。`stylesheets/transitions.css`(节选)**

```
/* Screen pushes out to left */ @-webkit-keyframes outToLeft { from { -webkit-transform: translateX(0); } to { -webkit-transform: translateX(-100%); } } .push.out { -webkit-animation-name: outToLeft; }
```

类似地，我们将为新屏幕定义一个从右侧飞入的动画，取代旧屏幕:

**例 4.29。`stylesheets/transitions.css`(节选)**

```
/* Screen pushes in from the right */ @-webkit-keyframes inFromRight { from { -webkit-transform: translateX(100%); } to { -webkit-transform: translateX(0); } } .push.in { -webkit-animation-name: inFromRight; }
```

对于 JavaScript，我们可以回收我们为 fade 制作的转换函数，例如，将其命名为`transition_push()`。然后我们只需要将所有的`fade`实例改为`push`。然后当我们想要实现翻转过渡时，再次为`flip`，等等。转念一想，将转换类型作为参数传递给我们的`transition()`函数会更好:

**例 4.30。`javascripts/ch4/09-slide.js`(节选)**

```
function transition(toPage, type) { ... toPage .addclass("current " + type + " in") .one("webkitAnimationEnd", function(){ fromPage.removeclass("current " + type + " out"); toPage.removeclass(type + " in"); }); fromPage.addclass(type + " out"); }
```

现在，当我们为新的过渡创建 CSS 动画时，它们将自动在我们的脚本中使用。我们只是传入新的名字:

```
transition(nextPage, "push");
```

我们希望 push 转换用于从列表向下导航到详细页面，因此我们需要为列表项添加一些新的点击处理程序:

**例 4.31。`javascripts/ch4/09-slide.js`(节选)**

```
$("#spots-list li").click(function(e){ e.preventDefault(); transition("#page-spot", "push"); }); $("#stars-list li").click(function(e){ e.preventDefault(); transition("#page-star", "push"); });
```

这样，详细页面将从右侧滑入，取代列表页面。正如我们之前所描述的，我们也希望当用户向后*向上*导航应用程序的层次结构时发生相反的情况。接下来，我们将研究如何构建该功能，并且在此过程中，添加对“返回”的支持

### 4.4.倒退

用户现在正在查看某个疯狂名人的详细信息页面，他们感到很无聊。他们想要一个新的疯狂的名人来阅读，所以他们去寻找后退按钮。但是返回不仅仅是再次交换源页面和目标页面，因为我们应用的动画需要反转:旧页面需要从左侧滑回到视图中。

但这超出了我们的想象。首先，我们需要一个后退按钮。我们已经在每个页面的标题中以 a 元素的形式提供了一个，样式看起来都像按钮:

**例 4.32。`ch4/10-back.html`(节选)**

```
<div class="header"> <h1>Spots</h1> <a href="#" class="back">Back</a> </div>
```

当然，我们必须有一个处理程序在按钮被点击时执行一个动作:

**例 4.33。`javascripts/ch4/10-back.js`(节选)**

```
$("#spot-details .back").click(function(){ // Do something when clicked ... });
```

接下来，我们需要重新创建我们所有的 CSS 动画——但是反过来。我们已经创建了`inFromRight`和`outFromLeft`动画；我们需要添加两个补充:`inFromLeft`和`outToRight`。一旦这些被定义，它们必须用 CSS 选择器附加到我们的元素上。我们将继续模块化方法，并使用类选择器的组合来利用我们现有的属性:

**例 4.34。`stylesheets/transitions.css`(节选)**

```
@-webkit-keyframes inFromLeft { from { -webkit-transform: translateX(-100%); } to { -webkit-transform: translateX(0); } } .push.in.reverse { -webkit-animation-name: inFromLeft; } @-webkit-keyframes outToRight { from { -webkit-transform: translateX(0); } to { -webkit-transform: translateX(100%); } } .push.out.reverse { -webkit-animation-name: outToRight; }
```

下一步是将新的`class`加入到我们的`transition()`函数中。我们将添加第三个参数， *reverse* ，它接受一个布尔值。如果值是`false`，或者根本没有提供，我们将进行向前版本的转换。如果值是`true`，我们将把`reverse class`附加到所有的类操作操作中:

**例 4.35。`javascripts/ch4/10-back.js`(节选)**

```
function transition(toPage, type, reverse){ var toPage = $(toPage), fromPage = $("#pages .current"), reverse = reverse ? "reverse" : ""; if(toPage.hasclass("current") || toPage === fromPage) { return; }; toPage .addclass("current " + type + " in " + reverse) .one("webkitAnimationEnd", function(){ fromPage.removeclass("current " + type + " out " + reverse); toPage.removeclass(type + " in " + reverse); }); fromPage.addclass(type + " out " + reverse); }
```

如果我们现在传入`true`，新页面将被赋予`class`属性`push in reverse`，旧页面将被赋予`push out reverse`——这将触发我们新的向后动画。为了看到它的运行，我们将在我们的返回按钮 hander 中添加一个对`transition()`的调用:

**例 4.36。`javascripts/ch4/10-back.js`(节选)**

```
$("#page-spot .back").click(function(e){ e.preventDefault(); transition("#page-spots", "push", true); });
```

### 4.4.1.管理历史

后退键可以，但是目前有点“手动”。对于我们应用程序中的每个页面，我们都必须连接一个单独的处理程序来返回。更糟糕的是，一些页面可以通过许多不同的路径到达，然而我们当前的解决方案只能返回到一个固定的页面。为了解决这些问题，我们将创建自己的历史记录系统，记录用户访问的每个页面，这样当他们点击后退按钮时，我们就知道应该把他们发送到哪里。

首先，我们将创建一个`visits`对象，它将包含一个历史数组和一些管理它的方法:

**例 4.37。`javascripts/ch4/11-history.js`(节选)**

```
var visits = { history: [], add: function(page) { this.history.push(page); } };
```

我们的`visits`对象将在历史数组中维护一个已访问页面的堆栈。`add()`方法获取一个页面并将其添加到堆栈中(通过 JavaScript `push()`函数，该函数将一个元素添加到数组的末尾)。我们将从我们的`transition()`函数内部调用这个方法，这样每个页面都会在显示之前被添加:

**例 4.38。`javascripts/ch4/11-history.js`(节选)**

```
function transition(toPage, type, reverse) { var toPage = $(toPage), fromPage = $("#pages .current"), reverse = reverse ? "reverse" : ""; visits.add(toPage); ... }
```

### 集中页面更改

假设每个转换都对应于一个页面变化对我们来说很方便，否则我们必须在每个转换的地方调用`visits.add()`。但是，有时您可能希望转换到新页面，但不希望将其作为页面更改包括在内——例如，如果您有某种上滑对话框。在这种情况下，您可以创建一个处理历史管理和转换的`changePage()`函数。我们将在下一节中讨论这一点。

下一个要考虑的项目是我们的后退按钮。我们只希望在有历史项目要恢复时才显示它。我们将为`visits`对象添加一个助手方法来为我们检查。因为历史中的第一页将是当前页，所以我们需要检查至少有两页:

**例 4.39。`javascripts/ch4/11-history.js`(节选)**

```
var visits = { ... hasBack: function() { return this.history.length > 1; } }
```

现在我们有了这个助手，我们可以在转换代码中使用它来相应地显示或隐藏 Back 按钮。jQuery 函数在这里非常有用；它接受布尔值，并根据该值显示或隐藏元素:

**例 4.40。`javascripts/ch4/11-history.js`(节选)**

```
function transition(toPage, type, reverse) { var toPage = $(toPage), fromPage = $("#pages .current"); reverse = reverse ? "reverse" : ""; visits.add(toPage); toPage.find(".back").toggle(visits.hasBack()); ...
```

很好！现在我们需要在我们的`visits`对象中使用一些逻辑来处理一个 back 事件。如果有历史，我们将弹出堆栈顶部的第一个项目(当前页面)。我们实际上并不需要这个页面——但是我们必须移除它才能到达下一个项目。这一项是上一页，也是我们返回的一页:

**例 4.41。`javascripts/ch4/11-history.js`(节选)**

```
var visits = { ... back: function() { if(!this.hasBack()){ return; } var curPage = this.history.pop(); return this.history.pop(); } }
```

### 推动和弹出

`push()`和`pop()`方法分别在数组末尾添加或删除元素。这两种方法都会就地修改原始数组。`pop()`方法返回已经移除的元素(在我们的例子中，我们用它来获取前一页)，而`push()`方法返回数组的新长度。

最后，我们可以连接应用程序的所有后退按钮。当发出返回的请求时，我们获取上一页，如果它存在，我们转换回它。我们只是用一个通用的点击处理程序替换了硬编码的点击处理程序:

**例 4.42。`javascripts/ch4/11-history.js`(节选)**

```
$(".back").live("click",function(){ var lastPage = visits.back(); if(lastPage) { transition(lastPage, "push", true); } });
```

尽管如此，仍然有一个问题:我们从未将*初始*页面添加到历史堆栈中，所以没有办法导航回它。这很容易解决——我们只需从初始的`div`中删除`current class`,并调用我们的转换函数在文档加载时显示第一页:

**例 4.43。`javascripts/ch4/11-history.js`(节选)**

```
$(document).ready(function() { ... transition($("#page-spots"), "show"); });
```

为了连接“显示”过渡，我们将重复使用渐变动画，但是持续时间非常短:

**例 4.44。`stylesheets/transitions.css`(节选)**

```
.show.in { -webkit-animation-name: fade-in; -webkit-animation-duration: 10ms; }
```

很多原生应用只跟踪主页和详情页之间的历史；例如，在我们的例子中，一个星星的列表导致了星星的详细信息，后退按钮允许你跳回到列表。如果您更改应用程序的区域(例如，通过单击其中一个主导航链接)，历史记录将被重置。我们可以通过添加一个`clear()`方法来模仿这种行为:

**例 4.45。`javascripts/ch4/11-history.js`(节选)**

```
var visits = { ... clear: function() { this.history = []; } }
```

这只会清除我们的历史堆栈。每当用户移动到一个新的部分时，我们将调用这个方法:

**例 4.46。`javascripts/ch4/11-history.js`(节选)**

```
$("#tab-bar a").click(function(e){ // Clear visit history visits.clear(); ... });
```

这有一种非常“应用”的感觉，而且，作为一个额外的好处，我们不必连接这么多的后退按钮事件！

### 4.4.2.背面有硬件按钮

我们目前的后退按钮系统很好，但它没有考虑到移动设备通常会有自己的后退按钮——无论是物理按钮还是浏览器中的软按钮。就目前情况而言，如果用户在点击我们应用程序中的几个内部链接后点击设备的后退按钮，浏览器将会简单地移动到它加载的最后一个 HTML 页面，或者完全退出。这肯定会打破我们的用户对我们的网站是一个成熟的应用程序的幻想，所以让我们看看我们是否能找到解决这个问题的办法。

我们真正需要的是能够听取和修改浏览器的内置历史，而不是我们自己定制的页面堆栈。为了做到这一点， [HTML5 历史 API](https://www.w3.org/TR/html5/history.html) 在这里帮助我们。

History API 允许我们向历史堆栈中添加页面，以及在堆栈中的页面之间向前和向后移动。为了添加页面，我们使用了`window.history.pushState()`方法。这个方法类似于我们之前的`visits.add()`方法，但是有三个参数:任何我们想要记住的关于页面的任意数据；页面标题(如果适用)；和页面的 URL。

我们将创建一个方法`changePage()`,它结合了使用历史 API 添加页面和进行常规转换。我们将跟踪历史中的转换，以便当用户按下 back 时，我们可以查看转换并执行相反的操作。这比我们以前的版本更好，在以前的版本中，我们只为后面的过渡做反向滑动。

下面是编写这个新方法的第一次尝试:

**例 4.47。`javascripts/ch4/12-hardware-back.js`(节选)**

```
function changePage(page, type, reverse) { window.history.pushState({ page: page, transition: type, reverse: !!reverse }, "", page); // Do the real transition transition(page, type, reverse) }
```

`pushState()`的第一个参数被称为*状态对象*。您可以使用它以 JavaScript 对象的形式在应用程序的页面之间传递任意数量的数据。在我们的例子中，我们传递页面、转换类型以及是否是反向转换。

为了在我们的代码中使用这个新函数，我们只需将所有出现的`transition()`更改为`changePage()`，例如:

```
changePage("#page-spots", "show");
```

现在，当用户在我们的应用程序中移动时，历史被存储起来。如果他们点击物理后退按钮，你可以在地址栏看到页面历史，但没有什么特别的事情发生。这是意料之中的:我们刚刚将一系列页面字符串放入历史堆栈，但我们还没有告诉应用程序如何导航回它们。

每当真正的页面加载事件发生时，或者当用户点击后退或前进时，就会触发`window.onPopState`事件。该事件被提供给一个名为*状态*的对象，该对象包含我们用`pushStack()`放在那里的状态对象(如果状态未定义，则意味着该事件是从页面加载中触发的，而不是从历史变化中触发的——所以这无关紧要)。让我们为这个事件创建一个处理程序:

**例 4.48。`javascripts/ch4/12-hardware-back.js`(节选)**

```
window.addEventListener("popstate", function(event) { if(!event.state){ return; } // Transition back - but in reverse. transition( event.state.page, event.state.transition, !event.state.reverse ); }, false);
```

### jQuery 在哪里？

对于这个例子，我们只是使用了一个标准的 DOM 事件监听器，而不是 jQuery `bind()`方法。这只是为了让`popstate`事件更加清晰。如果我们使用`$(window).bind("popstate", ...)`绑定它，传递给回调的事件对象将是一个 jQuery 事件对象，而不是浏览器的本机`popstate`事件。通常这就是我们想要的，但是 jQuery 的事件包装器不包含来自历史 API 的属性，所以我们需要调用`event.originalEvent`来检索浏览器事件。这没有什么错——您可以随意使用您认为最简单的方法。

太棒了。当我们点击浏览器的后退按钮时，所有的动画似乎都在反向工作…是这样吗？如果你仔细观察，你可能会注意到一些奇怪的事情。有时我们看到的“幻灯片”过渡应该是简单的“展示”过渡，反之亦然。这是怎么回事？

实际上，我们这里有一个错误:当向后移动时，我们不想使用我们正在从*过渡到*的页面的过渡，而是我们正在从过渡到*的页面。不幸的是，这意味着我们需要调用`pushState()`来处理发生的下一个转换。但是我们看不到未来…我们怎么知道接下来会发生什么转变？*

幸运的是，历史 API 为我们提供了另一个方法，replaceState()。它几乎与 pushState()相同，但它不是添加到堆栈中，而是用*替换堆栈中的当前(最顶层)页面。为了解决我们的问题，我们将抓住上一个`pushState()`的细节；然后，在我们添加下一项之前，我们将使用`replaceState()`用“下一个”转换来更新页面:*

**例 4.49。`javascripts/ch4/12-hardware-back.js`(节选)**

```
var pageState = {}; function changePage(page, type, reverse) { // Store the transition with the state if(pageState.url){ // Update the previous transition to be the NEXT transition pageState.state.transition = type; window.history.replaceState( pageState.state, pageState.title, pageState.url); } // Keep the state details for next time! pageState = { state: { page: page, transition: type, reverse: reverse }, title: "", url: page } window.history.pushState(pageState.state, pageState.title, pageState.url); // Do the real transition transition(page, type, reverse) }
```

当用户返回时，我们还需要更新我们的`pageState`变量；否则，它将与浏览器的历史记录不同步，我们的`replaceState()`调用将会在历史记录中插入虚假条目:

**例 4.50。`javascripts/ch4/12-hardware-back.js`(节选)**

```
window.addEventListener("popstate", function(event) { if(!event.state){ return; } // Transition back - but in reverse. transition( event.state.page, event.state.transition, !event.state.reverse ); pageState = { state: { page: event.state.page, transition: event.state.transition, reverse: event.state.reverse }, title: "", url: event.state.page } }, false);
```

我们走吧。物理后退按钮现在工作得很漂亮。但是我们的自定义应用程序后退按钮呢？我们可以将它连接起来以触发一个历史事件，因此可以通过快速调用`history.back()`来绑定到我们刚刚编写的所有历史 API jazz:

**例 4.51。`javascripts/ch4/12-hardware-back.js`(节选)**

```
$(".back").live("click",function(e){ window.history.back(); });
```

现在，我们的应用程序后退按钮就像浏览器或物理后退按钮一样工作。你也可以连接一个前进按钮并用`history.forward()`触发它，或者用`history.go(-3)`跳到堆栈中的特定页面。你可能已经注意到我们在前进按钮处理上有点安静。这有两个原因:第一，大部分手机浏览器缺少前进按钮，第二，无法知道 popstate 事件的发生是因为后退还是前进按钮。

解决这个难题的唯一方法是将 popstate 方法与我们在上一节中构建的手动历史管理系统结合起来，查看 URL 或其他数据来确定堆栈移动的方向。就可用性而言，这是一项回报很少的大量工作，所以我们将满足于我们已经构建的历史和回溯功能，并继续下一个挑战。

### 构建移动图书

您可以从 Sitepoint 购买《为智能设备构建移动网站和应用程序》一书。阅读完整的[第 4 章。移动网络应用](https://www.sitepoint.com/build-mobile-book/)，独家在 BuildMobile，在下面的部分是免费的。

*   [开店](https://www.sitepoint.com/mobile-web-apps-setting-up-shop/)
*   [事件](https://www.sitepoint.com/mobile-web-apps-events/)
*   [速赢](https://www.sitepoint.com/mobile-web-apps-quick-wins/)
*   加载页面
*   Ajax
*   [模板化](https://www.sitepoint.com/mobile-web-apps-templating/)
*   我们有一个应用程序！

## 分享这篇文章