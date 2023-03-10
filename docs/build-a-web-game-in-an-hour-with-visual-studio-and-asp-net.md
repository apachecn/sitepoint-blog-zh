# 用 Visual Studio 和 ASP.NET 在一个小时内构建一个网页游戏

> 原文：<https://www.sitepoint.com/build-a-web-game-in-an-hour-with-visual-studio-and-asp-net/>

本文是微软网站开发系列的一部分。感谢您对使 SitePoint 成为可能的合作伙伴的支持。

**本文讨论:**

*   基本游戏开发理念
*   使用网络技术进行游戏开发
*   增加游戏控制和人工智能

**讨论的技术:**

*   ASP.NET Visual Studio 2013 社区的 Visual Studio 2013 专业版
*   [代码下载](http://download.microsoft.com/download/9/0/9/909383C3-E5F2-4EF8-BDE5-7D8B6C9AD164/Code_OneppoGame0315.zip)(。zip)

开发游戏不需要全新的技能。其实你目前在 HTML、JavaScript、CSS 等等方面的 Web 开发技能，对于各种各样的游戏来说刚刚好。当你用网络技术开发一款游戏时，它几乎可以在任何装有浏览器的设备上运行。

为了证明这一点，我将演示使用 Web 技术和两个外部库从头开始构建一个游戏，我将在不到一个小时的时间内完成。我将涵盖各种游戏开发主题，从基本设计和布局，控制和精灵，到一个简单对手的人工智能。我甚至打算开发这款游戏，让它能在个人电脑、平板电脑和智能手机上运行。如果你有 Web 开发人员或其他开发领域的编程经验，但没有编写游戏的经验，这篇文章将帮助你入门。如果你给我一个小时，我保证带你熟悉环境。

## 开始运行

我将在 [Visual Studio](http://www.visualstudio.com/?WT.mc_id=13419-DEV-sitepoint-article43) 中完成所有开发，这将允许在我进行更改时快速执行 Web 应用程序。一定要有最新版本的 Visual Studio，这样你才能跟上。我用的是 Visual Studio 2013 Pro，但是用 Visual Studio 2013 社区更新了代码。此外，如果你有 Mac 或 Linux， [Visual Studio 代码](http://code.visualstudio.com/?WT.mc_id=13419-DEV-sitepoint-article43)现在可以跨平台使用。

这个应用程序不需要服务器代码，所以我首先在 Visual Studio 中创建新的空网页项目。在选择“文件”|“新建”|“ASP.NET 空网站”之后，我将选择“Visual C#选项”,从而为网站使用空的 C#模板。

index HTML 文件只需要三个资源:jQuery、一个主样式表和一个主 JavaScript 文件。我将一个名为 style.css 的空 CSS 文件和一个名为 ping.js 的空 JavaScript 文件添加到项目中，以避免加载页面时出现错误:

```
<!DOCTYPE html>
<html>
<head>
  <script src="http://ajax.aspnetcdn.com/ajax/jQuery/jquery-2.1.1.min.js"></script>
  <script src="ping.js"></script>
  <link rel="stylesheet" href="style.css"></script>
</head>
<body>
</body>
</html> 
```

此外，不要忘记在不同的浏览器和设备上测试这个应用程序(或其他任何应用程序)。虽然我写的代码可以与 Chrome、Firefox 和 [Microsoft Edge](http://blogs.windows.com/msedgedev/2015/05/06/a-break-from-the-past-part-2-saying-goodbye-to-activex-vbscript-attachevent/?WT.mc_id=13419-DEV-sitepoint-article43) 等现代浏览器互操作，但最好还是仔细检查一下。现在你可以用[免费虚拟机](http://dev.modern.ie/tools/vms/?utm_source=SitePoint&utm_medium=article43&utm_campaign=SitePoint)和其他工具如[http://www.browserstack.com](http://www.browserstack.com)来做这件事。

## 基本设计

我正在开发的游戏是 Pong 的一个变种，我称之为 Ping。乒乓球和乒乓球的规则基本相同，除了双方都可以在球来到自己面前时抓住球，然后直接或上下倾斜地将球击回。通常最好是在构建游戏之前，先画出你想要的游戏外观。对于这款游戏，我想看到的整体布局如下图。

![Figure 1: Overall design of Ping](img/767c4e3c39a6ab395599f59e1a20eeb2.png)

一旦我开发了游戏设计布局，就只需要将每个元素添加到 HTML 中来构建游戏了。不过，有一点需要注意，我将对记分板和控件进行分组，以确保它们放在一起。所以一个接一个，你可以看到我已经添加了元素，如下所示:

```
<div id="arena">
  <div id="score">
    <h1>
      <span id="playerScore">0</span>
     <span id="opponentScore">0</span>
   </h1>
 </div>
 <div id="player"></div>
 <div id="opponent"></div>
 <div id="ball"></div>
 <div id="controls-left">
   <div id="up"></div>
   <div id="down"></div>
  </div>
  <div id="controls-right">
    <div id="left"></div>
    <div id="right"></div>
  </div>
</div> 
```

## 玩转时尚

如果您要加载这个页面，您将看不到任何内容，因为没有应用任何样式。我已经在我的 HTML 中设置了一个到 main.css 文件的链接，所以我将把我所有的 css 放在一个同名的新文件中。我要做的第一件事是在屏幕上定位一切。页面的主体需要占据整个屏幕，所以我先设置一下:

```
body {
  margin: 0px;
  height: 100%;
} 
```

第二，我需要用竞技场背景图片(见下图)填充整个屏幕:

```
#arena {
  background-image: url(arena.png);
  background-size: 100% 100%;
  margin: 0px;
  width: 100%;
  height: 100%;
  overflow: hidden;
} 
```

![Figure 2: Arena Background Image](img/c952ce087b3ae06df0f1a487b212bd70.png)

接下来，我将定位记分板。我希望它出现在其他元素的顶部和中间。命令 position: absolute 允许我将它放置在任何我想要的地方，left: 50%将其放置在窗口顶部的中间，但从记分板元素的最左侧开始。为了确保它完全居中，我使用了 transform 属性和 z-index 属性来确保它始终位于顶部:

```
#score {
  position: absolute;
  z-index: 1000;
  left: 50%;
  top: 5%;
  transform: translate(-50%, 0%);
} 
```

我也希望文本字体是复古主题。大多数现代浏览器都允许我包含自己的字体。我从 codeman38 (zone38.net)找到了合适的按下开始 2P 字体。要将字体添加到记分板，我必须创建一个新的字体:

```
@font-face {
  font-family: 'PressStart2P';
  src: url('PressStart2P.woff');
} 
```

现在，分数在一个 h1 标签中，所以我可以为所有 h1 标签设置字体。以防字体丢失，我将提供一些备份选项:

```
h1 {
  font-family: 'PressStart2P', 'Georgia', serif;
} 
```

对于其他元素，我将使用图像的 sprite 表。一个精灵表包含了我在游戏中需要的所有图片(见下图)。

![Figure 3: Sprite Sheet for Ping](img/b2da66c8b9446f7c70ce5c0592e17c30.png)

任何在此工作表中有图像的元素都将被分配一个 sprite 类。然后，对于每个元素，我将使用`background-position`来定义我想要显示 sprite 表的哪一部分:

```
.sprite {
  background-image: url("sprites.png");
  width: 128px;
  height: 128px;
} 
```

接下来，我将把`sprite`类添加到所有使用 sprite 工作表的元素中。为此，我必须简单地跳回 HTML:

```
<div id="player" class="sprite"></div>
<div id="opponent" class="sprite"></div>
<div id="ball" class="sprite"></div>
<div id="controls-left">
  <div id="up" class="sprite"></div>
  <div id="down" class="sprite"></div>
</div>
<div id="controls-right">
  <div id="left" class="sprite"></div>
  <div id="right" class="sprite"></div>
</div> 
```

现在我需要为每个元素指出每个 sprite 在工作表中的位置。同样，我将使用背景位置:

```
#player {
  position: absolute;
  background-position: 0px 128px;
}
#opponent {
  position: absolute;
  background-position: 0px 0px;
}
#ball {
  position: absolute;
  background-position: 128px 128px;
}
#right {
  background-position: 64px 192px;
}
#left {
  background-position: 64px 0px;
}
#down {
  background-position: 128px 192px;
}
#up {
  background-position: 128px 0px;
} 
```

位置:玩家、对手和球的绝对属性将允许我使用 JavaScript 移动它们。如果你现在看这个页面，你会看到控制和球有不必要的部分附在上面。这是因为精灵的尺寸小于默认的 128 像素，所以我将把它们调整到合适的尺寸。只有一个球，所以我直接设置它的大小:

```
#ball {
  position: absolute;
  width: 64px;
  height: 64px;
  background-position: 128px 128px;
} 
```

有四个控制元素(用户可以按下按钮来移动播放器)，所以我应该为它们创建一个特殊的类。我还会添加页边空白，以便它们周围有一点空间:

```
.control {
  margin: 16px;
  width: 64px;
  height: 64px;
} 
```

添加这个类后，游戏有了更好看的控件:

```
<div id="controls-left">
  <div id="up" class="sprite control"></div>
  <div id="down" class="sprite control"></div>
</div>
<div id="controls-right">
  <div id="left" class="sprite control"></div>
  <div id="right" class="sprite control"></div>
</div> 
```

我需要做的最后一件事是，当页面在移动设备上运行时，将控件放置在用户的手边。我会把它们贴在底部的角落:

```
#controls-left {
  position: absolute;
  left: 0; bottom: 0;
}
#controls-right {
  position: absolute;
  right: 0; bottom: 0;
} 
```

这种设计的一个好处是一切都是相对位置设置的。这意味着屏幕可以有许多不同的尺寸，同时仍然使游戏看起来很好。

## 跟着弹跳球走

现在我要让球四处移动。对于 JavaScript 代码，我在 HTML 中引用了一个名为 ping.js 的文件，就像我在 CSS 中做的那样。我将把这段代码添加到一个同名的新文件中。我将为球和每个球员制作对象，但我将使用工厂模式制作对象。

这是一个简单的概念。当你调用球函数时，它会创建一个新球。没有必要使用新的关键字。该模式通过澄清可用的对象属性，减少了围绕 This 变量的一些混乱。因为我只有一个小时来制作这个游戏，我需要尽量减少任何混淆的概念。

这种模式的结构，正如我制作的这个简单的球类:

```
var Ball = function( {
  // List of variables only the object can see (private variables).
  var velocity = [0,0];
  var position = [0,0];
  var element = $('#ball');
  var paused = false;
  // Method that moves the ball based on its velocity. This method is only used
  // internally and will not be made accessible outside of the object.
  function move(t) {
  }
  // Update the state of the ball, which for now just checks
  // if the play is paused and moves the ball if it is not.
  // This function will be provided as a method on the object.
  function update(t) {
    // First the motion of the ball is handled
    if(!paused) {
      move(t);
    }
  }
  // Pause the ball motion.
  function pause() {
    paused = true;
  }
  // Start the ball motion.
  function start() {
    paused = false;
  }
  // Now explicitly set what consumers of the Ball object can use.
  // Right now this will just be the ability to update the state of the ball,
  // and start and stop the motion of the ball.
  return {
    update:       update,
    pause:        pause,
    start:        start
} 
```

要创建一个新球，我只需调用我定义的函数:

```
var ball = Ball(); 
```

现在我想让球在屏幕上移动和弹跳。首先，我需要每隔一段时间调用更新函数来创建球的动画。现代浏览器为此提供了一个名为 requestAnimationFrame 的功能。它将一个函数作为参数，并在下次运行动画循环时调用传入的函数。当浏览器准备好进行更新时，这可以让球平稳地移动。当它调用传入的函数时，它会以秒为单位给出页面加载后的时间。这对于确保动画长时间保持一致至关重要。在游戏中，`requestAnimationFrame`的使用出现如下:

```
var lastUpdate = 0;
var ball = Ball();

function update(time) {
  var t = time - lastUpdate;
  lastUpdate = time;
  ball.update(t);
  requestAnimationFrame(update);
}

requestAnimationFrame(update); 
```

注意`requestAnimationFrame`在函数中被再次调用，因为球已经完成更新。这确保了连续的动画。

虽然这段代码可以工作，但可能会出现一个问题，即在页面完全加载之前脚本就开始运行了。为了避免这种情况，我将在页面加载时使用 jQuery 启动代码:

```
var ball;
var lastUpdate;
$(document).ready(function() {
  lastUpdate = 0;
  ball = Ball();
  requestAnimationFrame(update);
}); 
```

因为我知道球的速度(velocity)和自上次更新以来的时间，所以我可以做一些简单的物理操作来使球向前移动:

```
var position = [300, 300];
var velocity = [-1, -1];
var move = function(t) {
  position[0] += velocity[0] \* t;
  position[1] += velocity[1] \* t;
  element.css('left', position[0] + 'px');
  element.css('top', position[1] + 'px');
} 
```

试着运行代码，你会看到球以一个角度移动并离开屏幕。这在一秒钟内很有趣，但是一旦球离开屏幕边缘，这种乐趣就停止了。所以下一步是让球从屏幕边缘反弹，如图 7 所示。添加此代码，运行应用程序将显示一个不断弹跳的球。

## 可移动的运动员

现在是时候让玩家对象了。充实玩家类的第一步是让 move 函数改变玩家的位置。side 变量将指示球员将居住在球场的哪一边，这将决定如何水平定位球员。传递到 move 函数中的 y 值将是玩家向上或向下移动的幅度:

```
var Player = function (elementName, side) {
  var position = [0,0];
  var element = $('#'+elementName);
  var move = function(y) {
  }
  return {
    move: move,
    getSide:      function()  { return side; },
    getPosition:  function()  { return position; }
  }
} 
```

然后，我们可以安排玩家的移动，如果玩家精灵到达窗口的顶部或底部，就停止移动。

```
var move = function(y) {
  // Adjust the player's position.
  position[1] += y;
  // If the player is off the edge of the screen, move it back.
  if (position[1] <= 0)  {
    position[1] = 0;
  }
  // The height of the player is 128 pixels, so stop it before any
  // part of the player extends off the screen.
  if (position[1] >= innerHeight - 128) {
    position[1] = innerHeight - 128;
  }
  // If the player is meant to stick to the right side, set the player position
  // to the right edge of the screen.
  if (side == 'right') {
    position[0] = innerWidth - 128;
  }
  // Finally, update the player's position on the page.
  element.css('left', position[0] + 'px');
  element.css('top', position[1] + 'px');
} 
```

我现在可以创建两个玩家，并让他们移动到屏幕上相应的位置:

```
player = Player('player', 'left');
player.move(0);
opponent = Player('opponent', 'right');
opponent.move(0); 
```

## 键盘输入

所以理论上你可以移动玩家，但是没有指令它不会动。给左边的播放器添加一些控件。你需要两种方法来控制播放器:使用键盘(在个人电脑上)和点击控制(在平板电脑和手机上)。

为了确保各种平台上触摸输入和鼠标输入的一致性，我将使用伟大的统一框架 Hand.js (handjs.codeplex.com)。首先，我将脚本添加到 HTML 的 head 部分:

```
<script src="hand.minified-1.3.8.js"></script> 
```

然后，当你按下键盘上的 A 和 Z 键时，或者当你点击控制键时，我将使用`Hand.js`和 jQuery 来控制播放器。

```
var distance = 24;  // The amount to move the player each step.
$(document).ready(function() {
  lastUpdate = 0;
  player = Player('player', 'left');
  player.move(0);
  opponent = Player('opponent', 'right');
  opponent.move(0);
  ball = Ball();
  // pointerdown is the universal event for all types of pointers -- a finger,
  // a mouse, a stylus and so on.
  $('#up')    .bind("pointerdown", function() {player.move(-distance);});
  $('#down')  .bind("pointerdown", function() {player.move(distance);});
  requestAnimationFrame(update);
});
$(document).keydown(function(event) {
  var event = event || window.event;
  // This code converts the keyCode (a number) from the event to an uppercase
  // letter to make the switch statement easier to read.
  switch(String.fromCharCode(event.keyCode).toUpperCase()) {
    case 'A':
      player.move(-distance);
      break;
    case 'Z':
      player.move(distance);
      break;
  }
  return false;
}); 
```

## 接住球

当球弹来弹去的时候，我想让球员接住它。当球被抓住时，它有一个主人，它会跟随主人的运动。我将在球的移动方法中添加功能，允许球有一个主人，然后球会跟随它:

```
var move = function(t) {
  // If there is an owner, move the ball to match the owner's position.
  if (owner !== undefined) {
    var ownerPosition = owner.getPosition();
    position[1] = ownerPosition[1] + 64;
    if (owner.getSide() == 'left') {
      position[0] = ownerPosition[0] + 64;
    } else {
      position[0] = ownerPosition[0];
    }
  // Otherwise, move the ball using physics. Note the horizontal bouncing
  // has been removed -- ball should pass by a player if it
  // isn't caught.
  } else {
    // If the ball hits the top or bottom, reverse the vertical speed.
    if (position[1] - 32 <= 0 || position[1] + 32 >= innerHeight) {
      velocity[1] = -velocity[1];
    }
    position[0] += velocity[0] \* t;
    position[1] += velocity[1] \* t;
  }
  element.css('left', (position[0] - 32) + 'px');
  element.css('top',  (position[1] - 32) + 'px');
} 
```

目前，没有办法获得 Player 对象的位置，所以我将向 Player 对象添加`getPosition`和`getSide`访问器:

```
return {
  move: move,
  getSide:      function()  { return side; },
  getPosition:  function()  { return position; }
} 
```

现在，如果球有了主人，它就会跟着主人到处跑。但是我如何确定所有者呢？总得有人去接球。让我们确定一个玩家精灵何时触球。当这种情况发生时，我会将球的所有者设置为该球员。

```
var update = function(t) {
// First the motion of the ball is handled.
if(!paused) {
    move(t);
}
// The ball is under control of a player, no need to update.
if (owner !== undefined) {
    return;
}
// First, check if the ball is about to be grabbed by the player.
var playerPosition = player.getPosition();
  if (position[0] <= 128 &&
      position[1] >= playerPosition[1] &&
      position[1] <= playerPosition[1] + 128) {
    console.log("Grabbed by player!");
    owner = player;
}
// Then the opponent...
var opponentPosition = opponent.getPosition();
  if (position[0] >= innerWidth - 128 &&
      position[1] >= opponentPosition[1] &&
      position[1] <= opponentPosition[1] + 128) {
    console.log("Grabbed by opponent!");
    owner = opponent;
} 
```

如果你现在尝试玩这个游戏，你会发现球从屏幕的顶部弹起，你可以移动玩家去接住它。现在，你怎么扔？这就是右手控制的作用——瞄准球。让我们为播放器添加一个“火”功能，以及一个 aim 属性。

```
var aim = 0;
var fire = function() {
  // Safety check: if the ball doesn't have an owner, don't not mess with it.
  if (ball.getOwner() !== this) {
    return;
  }
  var v = [0,0];
  // Depending on the side the player is on, different directions will be thrown.
  // The ball should move at the same speed, regardless of direction --
  // with some math you can determine that moving .707 pixels on the
  // x and y directions is the same speed as moving one pixel in just one direction.
  if (side == 'left') {
    switch(aim) {
    case -1:
      v = [.707, -.707];
      break;
    case 0:
      v = [1,0];
      break;
    case 1:
      v = [.707, .707];
    }
  } else {
    switch(aim) {
    case -1:
      v = [-.707, -.707];
      break;
    case 0:
      v = [-1,0];
      break;
    case 1:
      v = [-.707, .707];
    }
  }
  ball.setVelocity(v);
  // Release control of the ball.
  ball.setOwner(undefined);
}
// The rest of the Ball definition code goes here...
return {
  move: move,
  fire: fire,
  getSide:      function()  { return side; },
  setAim:       function(a) { aim = a; },
  getPosition:  function()  { return position; },
} 
```

然后我们可以增加键盘功能来设置玩家的瞄准和射击功能。瞄准的工作方式略有不同。当释放瞄准键时，瞄准将返回到直接瞄准。

```
$(document).keydown(function(event) {
  var event = event || window.event;
  switch(String.fromCharCode(event.keyCode).toUpperCase()) {
    case 'A':
      player.move(-distance);
      break;
    case 'Z':
      player.move(distance);
      break;
    case 'K':
      player.setAim(-1);
      break;
    case 'M':
      player.setAim(1);
      break;
    case ' ':
      player.fire();
      break;
  }
  return false;
});
$(document).keyup(function(event) {
  var event = event || window.event;
  switch(String.fromCharCode(event.keyCode).toUpperCase()) {
    case 'K':
    case 'M':
      player.setAim(0);
      break;
  }
  return false;
}); 
```

最后增加的将是所有控件的触摸支持。我会让右边的控件改变玩家的目标。我还会让它在屏幕上的任何地方都非常感人:

```
$('#left')  .bind("pointerdown", function() {player.setAim(-1);});
$('#right') .bind("pointerdown", function() {player.setAim(1);});
$('#left')  .bind("pointerup",   function() {player.setAim(0);});
$('#right') .bind("pointerup",   function() {player.setAim(0);});
$('body')   .bind("pointerdown", function() {player.fire();}); 
```

## 保留得分

当球经过一个球员时，我想改变比分，把球给那个球员。我将使用自定义事件，这样我就可以将计分与任何现有对象分开。更新函数越来越长，所以我将添加一个名为 checkScored 的新私有函数:

```
function checkScored() {
  if (position[0] <= 0) {
    pause();
    $(document).trigger('ping:opponentScored');
  }
  if (position[0] >= innerWidth) {
    pause();
    $(document).trigger('ping:playerScored');
  }
} 
```

下面的代码对这些事件作出反应，更新分数并交出球。将这段代码添加到 JavaScript 文档的底部。

```
$(document).on('ping:playerScored', function(e) {
  console.log('player scored!');
  score[0]++;
  $('#playerScore').text(score[0]);
  ball.setOwner(opponent);
  ball.start();
});
$(document).on('ping:opponentScored', function(e) {
  console.log('opponent scored!');
  score[1]++;
  $('#opponentScore').text(score[1]);
  ball.setOwner(player);
  ball.start();
}); 
```

现在，当球越过你的对手时(这并不困难，因为对手没有移动)，你的分数将会上升，球将会被交给对手。然而，对手只会抓住球不放。

## 变聪明

你几乎有一个游戏。要是有人陪你玩就好了。作为最后一步，我将展示如何用简单的人工智能控制对手。当球来回移动时，对手会尽量与球保持平行。如果对手接住球，它会随机移动，随机向一个方向发射球。为了让人工智能感觉更像人类，我会在每件事情上添加延迟。请注意，这不是高度智能的人工智能，但它将是游戏中的对手。

设计这种系统时，最好考虑状态。对手 AI 有三种可能的状态:跟随、瞄准/射击、等待。我会在接下来的动作中加入更多人性化的元素。从人工智能对象开始:

```
function AI(playerToControl) {
  var ctl = playerToControl;
  var State = {
    WAITING: 0,
    FOLLOWING: 1,
    AIMING: 2
  }
  var currentState = State.FOLLOWING;
} 
```

根据人工智能的状态，我想让它做不同的动作。就像球一样，我将创建一个可以在`requestAnimationFrame`中调用的更新函数，让 AI 根据它的状态进行操作:

```
function update() {
  switch (currentState) {
    case State.FOLLOWING:
      // Do something to follow the ball.
      break;
    case State.WAITING:
      // Do something to wait.
      break;
    case State.AIMING:
      // Do something to aim.
      break;
  }
} 
```

`FOLLOWING`状态很简单。对手在球的垂直方向上移动，AI 转换到`WAITING`状态，以注入一些减慢的反应时间。下面的代码显示了这两种状态:

```
function moveTowardsBall() {
  // Move the same distance the player would move, to make it fair.
  if(ball.getPosition()[1] >= ctl.getPosition()[1] + 64) {
    ctl.move(distance);
  } else {
    ctl.move(-distance);
  }
}
function update() {
  switch (currentState) {
    case State.FOLLOWING:
      moveTowardsBall();
      currentState = State.WAITING;
    case State.WAITING:
      setTimeout(function() {
        currentState = State.FOLLOWING;
      }, 400);
      break;
    }
  }
} 
```

人工智能在必须跟随球和等待瞬间之间交替。现在将代码添加到游戏范围的更新函数中:

```
function update(time) {
  var t = time - lastUpdate;
  lastUpdate = time;
  ball.update(t);
  ai.update();
  requestAnimationFrame(update);
} 
```

当你运行游戏时，你会看到对手跟随球的运动——在不到 30 行代码中，这是一个不错的 AI。当然，如果对手接住了球，它也不会怎么样。所以这一小时的最后一个技巧，是时候处理`AIMING`状态的动作了。

我希望人工智能随机移动几次，然后朝随机方向发射球。让我们添加一个私有函数来完成这个任务。将`aimAndFire`函数添加到`AIMING` case 语句中，就形成了一个可以用来玩游戏的全功能 AI。

```
function repeat(cb, cbFinal, interval, count) {
  var timeout = function() {
    repeat(cb, cbFinal, interval, count-1);
  }
  if (count <= 0) {
    cbFinal();
  } else {
    cb();
    setTimeout(function() {
      repeat(cb, cbFinal, interval, count-1);
    }, interval);
  }
}

function aimAndFire() {

  // Repeat the motion action 5 to 10 times.

  var numRepeats = Math.floor(5 + Math.random() \* 5);
  function randomMove() {
    if (Math.random() > .5) {
      ctl.move(-distance);
    } else {
      ctl.move(distance);
    }
  }

  function randomAimAndFire() {

    var d = Math.floor( Math.random() \* 3 - 1 );
    opponent.setAim(d);
    opponent.fire();

    // Finally, set the state to FOLLOWING.

    currentState = State.FOLLOWING;
  }

  repeat(randomMove, randomAimAndFire, 250, numRepeats);

} 
```

## 包扎

现在，你已经有了一个成熟的网页游戏，可以在个人电脑、智能手机和平板电脑上运行。这个游戏有很多可能的改进。例如，在智能手机的纵向模式下，它看起来有点尴尬，所以你需要确保你在横向模式下拿着手机才能正常工作。这只是一个小小的例子，展示了网络游戏开发的可能性。

*感谢技术专家 Mohamed Ameen Ibrahim 审阅本文。*

## JavaScript 的更多实践

这篇文章是微软技术倡导者的 web 开发系列的一部分，内容涉及实用的 JavaScript 学习、开源项目和互操作性最佳实践，包括[微软 Edge](http://blogs.windows.com/msedgedev/2015/05/06/a-break-from-the-past-part-2-saying-goodbye-to-activex-vbscript-attachevent/?WT.mc_id=13419-DEV-sitepoint-article43) 浏览器和新的 [EdgeHTML 渲染引擎](http://blogs.windows.com/msedgedev/2015/02/26/a-break-from-the-past-the-birth-of-microsofts-new-web-rendering-engine/?WT.mc_id=13419-DEV-sitepoint-article43)。

我们鼓励您使用 [dev.modern.IE](http://dev.modern.ie/tools/?utm_source=SitePoint&utm_medium=article43&utm_campaign=SitePoint) 上的免费工具跨浏览器和设备进行测试，包括 Windows 10 的默认浏览器 Microsoft Edge:

*   [扫描你的网站，寻找过时的库、布局问题和可访问性](http://dev.modern.ie/tools/staticscan/?utm_source=SitePoint&utm_medium=article43&utm_campaign=SitePoint)
*   [在 Mac、Linux 和 Windows 上使用虚拟机](http://dev.modern.ie/tools/vms/windows/?utm_source=SitePoint&utm_medium=article43&utm_campaign=SitePoint)
*   [在您自己的设备上远程测试 Microsoft Edge](https://remote.modern.ie/?utm_source=SitePoint&utm_medium=article43&utm_campaign=SitePoint)
*   [GitHub 编码实验室:跨浏览器测试和最佳实践](https://github.com/deltakosh/interoperable-web-development)

来自我们的工程师和布道者的关于 Microsoft Edge 和 Web 平台的深入技术学习:

*   【2015 年微软 Edge 网络峰会(对新浏览器、新支持的网络平台标准以及来自 JavaScript 社区的演讲嘉宾有何期待)
*   哇，我可以在 Mac 电脑上测试 Edge & IE 浏览器& Linux！(来自雷伊·班戈)
*   [在不破坏网络的情况下推进 JavaScript】(来自 Christian Heilmann)](http://channel9.msdn.com/Events/WebPlatformSummit/2015/Advancing-JavaScript-without-breaking-the-web/?WT.mc_id=13419-DEV-sitepoint-article43)
*   使网络正常工作的边缘渲染引擎(Jacob Rossi)
*   [用 WebGL 释放 3D 渲染](https://channel9.msdn.com/Events/WebPlatformSummit/2015/Unleash-3D-rendering-with-WebGL-and-Microsoft-Edge/?WT.mc_id=13419-DEV-sitepoint-article43)(来自大卫·卡图赫，包括[伏龙。JS](http://vorlonjs.com) 和 [babylonJS](http://babylonjs.com) 项目)
*   [托管网络应用和网络平台创新](https://channel9.msdn.com/Events/WebPlatformSummit/2015/Hosted-web-apps-and-web-platform-innovations/?WT.mc_id=13419-DEV-sitepoint-article43)(来自律师奶爸和基里尔·赛克谢诺夫，包括[流形。JS](http://manifold.js.com) 项目)

## 更多面向网络平台的免费跨平台工具和资源:

*   [适用于 Linux、MacOS 和 Windows 的 Visual Studio 代码](https://code.visualstudio.com/?WT.mc_id=13419-DEV-sitepoint-article43)
*   [用节点编码。JS](https://www.microsoftvirtualacademy.com/en-US/training-courses/building-apps-with-node-js-jump-start-8422/?WT.mc_id=13419-DEV-sitepoint-article43) 和[在 Azure 上免费试用](https://azure.microsoft.com/en-us/pricing/free-trial/?WT.mc_id=13419-DEV-sitepoint-article43)

## 分享这篇文章