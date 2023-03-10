# 用 Meteor 构建多人游戏 TicTacToe

> 原文：<https://www.sitepoint.com/building-multiplayer-tictactoe-game-with-meteor/>

![Wooden game of tic-tac-toe. Build a multiplayer game of tic-tac-toe with Meteor](img/126e703dac5b24575667528efe3c086c.png)

[Meteor](https://www.meteor.com/) 是一个流行的全栈 web 框架，[使你的想法原型化](https://en.wikipedia.org/wiki/Meteor_%28web_framework%29)变得非常容易，并且从开发到生产非常快。它的反应特性和对 [DDP](https://en.wikipedia.org/wiki/Distributed_Data_Protocol) 的使用，使它成为构建简单的多人浏览器游戏的绝佳选择。

在本教程中，我将向你展示如何用**流星**构建一个**多人游戏 TicTacToe** ，使用它默认的前端模板引擎 [Blaze](http://blazejs.org/) 。我假设您已经使用过 Meteor，当然，您对使用 JavaScript 编码感到很舒服。

> 如果你对 Meteor 毫无经验，我建议你首先遵循 Meteor 官方网站上的 [TODO 应用教程](https://www.meteor.com/tutorials/blaze/creating-an-app)。

你可以在 GitHub repo 附带的[中找到完整应用的代码。](https://github.com/sitepoint-editors/meteor-tic-tac-toe)

## 创建应用程序

如果你没有安装 Meteor，你应该根据你的操作系统遵循他们网站上的说明。

### 生成脚手架

现在安装了 Meteor，打开您的终端并运行以下命令:

```
meteor create TicTacToe-Tutorial 
```

这将创建一个以你的应用程序命名的文件夹(在本例中为 **TicTacToe-Tutorial** )。这个新文件夹包含应用程序的基本文件结构。里面其实有一个示例应用程序。

导航到文件夹:

```
cd TicTacToe-Tutorial 
```

现在运行应用程序:

```
meteor 
```

我知道，我知道…这是一个非常难记的命令，而且你会经常使用它，所以你应该开始记住它！

如果现在一切顺利，控制台应该正在构建应用程序。完成后，打开你的网页浏览器，进入 http://localhost:3000 查看应用程序的运行情况。如果您以前从未这样做过，我建议您试用一下示例应用程序。试着弄清楚它是如何工作的。

让我们来看看文件结构。打开你的应用文件夹。我们现在唯一关心的是客户机文件夹和服务器文件夹。客户端文件夹中的文件将由客户端下载并执行。服务器文件夹中的文件只能在服务器上执行，客户端无法访问它们。

以下是新文件夹中的内容:

```
client/main.js        # a JavaScript entry point loaded on the client
client/main.html      # an HTML file that defines view templates
client/main.css       # a CSS file to define your app's styles
server/main.js        # a JavaScript entry point loaded on the server
package.json          # a control file for installing NPM packages
.meteor               # internal Meteor files
.gitignore            # a control file for git 
```

## 构建董事会

TicTacToe 板是一个简单的三乘三的桌子；不要太花哨，这对我们的第一个多人游戏来说很好，所以我们可以专注于功能。

客户端将下载该板，因此我们将编辑客户端文件夹中的文件。让我们从删除 main.html 上的内容开始，用下面的内容替换它:

**client/main.html**

```
<head>
  <title>tic-tac-toe</title>
</head>

<body>
  <table id="board">
    <tr>
      <td class="field"></td>
      <td class="field"></td>
      <td class="field"></td>
    </tr>
    <tr>
      <td class="field"></td>
      <td class="field"></td>
      <td class="field"></td>
    </tr>
    <tr>
      <td class="field"></td>
      <td class="field"></td>
      <td class="field"></td>
    </tr>
  </table>
</body> 
```

更改后不要忘记保存文件！否则，它们不会被流星认可。

现在让我们添加一些 css 到我们的板上。打开 **main.css** 文件，添加以下内容:

**client/main.css**

```
table
{
  margin: auto;
  font-family: arial;
}

.field
{
  height: 200px;
  width: 200px;
  background-color: lightgrey;
  overflow: hidden;
}

#ui
{
  text-align: center;
}

#play-btn
{
  width: 100px;
  height: 50px;
  font-size: 25px;
}

.mark
{
  text-align: center;
  font-size: 150px;
  overflow: hidden;
  padding: 0px;
  margin: 0px;
}

.selectableField
{
  text-align: center;
  height: 200px;
  width: 200px;
  padding: 0px;
  margin: 0px;
} 
```

我们还添加了一些额外的 id 和类，我们将在本教程的后面使用。

最后，删除 **client/main.js** ，因为我们不再需要它，并在浏览器中打开应用程序，看看它看起来如何。

这很好，但不是最佳解决方案。下面通过介绍 [**Blaze 模板**](https://www.meteor.com/tutorials/blaze/templates) 来做一些重构。

### 创建模板

模板是 HTML 代码片段，具有自己的功能，您可以在应用程序中的任何地方重复使用。这是将你的应用程序分解成可重用组件的好方法。

在创建第一个模板之前，我们将在客户端文件夹中添加两个文件夹。我们将一个叫做 **html** ，另一个叫做 **js** 。

在 html 文件夹中，创建一个新的**board.html**文件，内容如下:

**client/html/board.html**

```
<template name="board">
  <table id="board">
    <tr>
      <td class="field"></td>
      <td class="field"></td>
      <td class="field"></td>
    </tr>
    <tr>
      <td class="field"></td>
      <td class="field"></td>
      <td class="field"></td>
    </tr>
    <tr>
      <td class="field"></td>
      <td class="field"></td>
      <td class="field"></td>
    </tr>
  </table>
</template> 
```

现在，在**main.html**文件夹中，用下面的代码替换 body 标签中的内容:

**client/main.html**

```
<head>
  <title>tic-tac-toe</title>
</head>

<body>
  {{>board}}
</body> 
```

这将在`body`标签中插入我们的带有属性`name="board"`的模板。

但这和我们之前的硬编码板是一样的。只是现在，它在一个模板中，所以让我们利用**模板助手**来动态构建我们的板。

### 使用助手

我们将在 board 模板中声明一个[助手](https://www.meteor.com/tutorials/blaze/templates),它将为我们提供一个长度与我们希望我们的电路板拥有的尺寸相同的数组。

在 **js** 文件夹中创建一个名为 **board.js** 的文件，内容如下:

**client/js/board.js**

```
import { Meteor } from 'meteor/meteor';
import { Template } from 'meteor/templating';

Template.board.helpers({
  sideLength: () => {
    let side = new Array(3);
    side.fill(0);

    return side;
  }
}); 
```

现在，我们将在 board 的模板 HTML 中使用这个助手，为助手提供的数组中的每个元素重复一行。为了帮助我们，我们将使用 [Each-in](http://blazejs.org/guide/spacebars.html#Each-in) Spacebars block 助手。

用以下内容替换**board.html**文件中的内容:

**client/html/board.html**

```
<template name="board">
  <table id="board">
    {{#each sideLength}}
      {{#let rowIndex=@index}}
      <tr>
        {{#each sideLength}}
        <td class="field" id="{{rowIndex}}{{@index}}">
          {{{isMarked rowIndex @index}}}
        </td>
        {{/each}}
      </tr>
      {{/let}}
    {{/each}}
  </table>
</template> 
```

请注意，我们在数组中循环了两次，一次是对**行**的循环，一次是对**列**的循环，在循环过程中实例化相应的标签(`tr`或`td`)。我们还将它们的`id`属性设置为第**行**的[@ index](https://docs.meteor.com/v1.3.5/packages/spacebars.html#Each)+**@第**列**的 index** 。我们得到的是一个两位数的数字，这将帮助我们识别该元素，以及它在棋盘上的位置。

在 http://localhost:3000 查看这个应用程序，看看目前为止它看起来怎么样。

### 用户界面

现在我们有了一个好看的面板，我们需要一个播放按钮和一个标签来显示当前游戏的信息。

让我们从在 **html** 文件夹中创建**ui.html**文件开始…你知道该怎么做。现在，向其中添加以下内容:

**client/html/ui.html**

```
<template name ="ui">
  <div id="ui">
    {{#if inGame}}
      <p id="status">
      {{status}}
      </p>
    {{else}}
      <button id="play-btn">Play</button>
    {{/if}}
  </div>
</template> 
```

如你所见，我们使用了 [#if](http://blazejs.org/guide/spacebars.html#If-Unless) 空格键阻止辅助对象和`inGame`辅助对象(我们还没有定义)作为条件。在`p`标签中还有一个`status`助手。我们稍后也会定义它。

它是如何工作的？`#if``inGame`助手返回`true`，玩家会看到`status`助手里的东西。否则，我们将只显示播放按钮。

别忘了，要显示这个组件，我们需要将它添加到我们的主客户端模板中:

**client/main.html**

```
<head>
  <title>tic-tac-toe</title>
</head>

<body>
  {{>ui}}
  {{>board}}
</body> 
```

### 登录

我们不会处理任何登录用户界面。我们将安装一个非常有用的软件包，名为[Brett le:accounts-anonymous-auto](https://github.com/brettle/meteor-accounts-anonymous-auto)，它将自动让所有用户匿名登录我们的应用程序。

前往您的控制台，运行以下命令:

```
meteor add brettle:accounts-anonymous-auto 
```

现在，当你在添加此包后首次打开该应用时，它将创建一个新用户，每次你在同一浏览器上打开该应用时，它都会记住你。如果我们不保留上述用户的任何数据，最好在他们注销时删除。但是我们不会在本教程中讨论这个问题。

## 构建游戏

最后，我们将开始构建游戏本身！让我们回顾一下我们将要实现的功能，以便清楚地了解接下来会发生什么。

我们需要以下功能:

*   创建游戏
*   加入现有游戏
*   采取行动
*   建立获胜条件
*   向玩家显示游戏状态
*   破坏一个完成的游戏实例

为了利用 Meteor 的[延迟补偿](https://www.discovermeteor.com/blog/latency-compensation/)，我们将把大部分代码放在客户机和服务器都可以访问的地方。

为此，我们将在项目的根目录下创建一个名为 **lib** 的文件夹。无论我们放什么进去，客户都会下载，所以我们必须非常小心。您不希望无意中向客户端提供任何 API 键或对隐藏功能的访问。

### 游戏收藏

流星使用[蒙哥系列](https://www.meteor.com/tutorials/blaze/collections)。如果你对 [Mongo](https://www.mongodb.com/) 不是很熟悉，但是你使用过任何其他的[面向文档的数据库](https://en.wikipedia.org/wiki/Document-oriented_database)你就没问题了。否则，请将集合视为表，其中每一行都独立于下一行。一行可以有六列，而同一表中的另一行可以有四个完全不同的列。

我们需要创建一个集合，并且客户端和服务器都可以访问它。因此，我们将在 lib 文件夹中创建一个 **games.js** 文件，在那里我们将创建一个名为 **"games"** 的集合实例，并将其存储在一个全局变量中，`Games`:

**lib/games.js**

```
import { Mongo } from 'meteor/mongo';

Games = new Mongo.Collection("games"); 
```

到现在为止，你可能想知道为什么我们给玩家访问数据库和游戏逻辑的权限。我们只给玩家本地权限。Meteor 为客户端提供了一个本地迷你 mongo 数据库 T1，我们只能用 T2 的发布-订阅模式 T3 来填充这个数据库，稍后我会向您展示。这是客户唯一能接触到的东西。即使客户端写入本地数据库，如果信息与服务器数据库中的信息不匹配，它也会被覆盖。

也就是说，Meteor 默认安装了几个非常不安全的包。一个叫做[自动发布](https://atmospherejs.com/meteor/autopublish)，它自动发布你所有的收藏并订阅客户端。另一个叫做[不安全的](https://atmospherejs.com/meteor/insecure)，它给予客户端对数据库的写访问权。

这两个包都非常适合原型开发，但是我们应该现在就卸载它们。转到控制台，运行以下命令:

```
meteor remove insecure
meteor remove autopublish 
```

这样一来，现在我们需要一种方法来同步我们在客户端和服务器端所做的事情。进入**流星战法**。

### 游戏方法

[Meteor.methods](https://guide.meteor.com/methods.html) 是一个我们可以注册方法的对象，这些方法可以被客户端用 [Meteor.call](https://docs.meteor.com/api/methods.html#Meteor-call) 函数调用。它们将首先在客户机上执行，然后在服务器上执行。因此，由于本地 Mongo 数据库，客户将能够立即看到发生的变化。然后，服务器将在主数据库上运行相同的代码。

让我们在`games`集合下创建一个空的`games.play`方法:

**lib/games.js**

```
Meteor.methods({
  "games.play"() {

  }
}); 
```

### 创建游戏

在 lib 文件夹中创建一个名为 **gameLogic.js** 的文件，在这个文件中我们将使用`newGame`方法创建`GameLogic`类，在这里我们将[向我们的游戏集合中插入](https://docs.mongodb.com/manual/reference/method/db.collection.insert/#db.collection.insert)一个新文档:

**lib/gameLogic.js**

```
class GameLogic
{
  newGame() {
    if(!this.userIsAlreadyPlaying()) {
      Games.insert({
        player1: Meteor.userId(),
        player2: "",
        moves: [],
        status: "waiting",
        result: ""
      });
    }
  }
} 
```

在这段代码中，我们在插入新游戏之前会询问玩家是否已经在玩了，因为我们不会一次为每个玩家支持多个游戏。这是非常重要的一步，否则我们可能会面临一个巨大的 bug。

让我们在`newGame()`下面添加`userIsAlreadyPlaying`方法:

**lib/gameLogic.js**

```
userIsAlreadyPlaying() {
  const game = Games.findOne({$or:[
    {player1: Meteor.userId()},
    {player2: Meteor.userId()}]
  });

  if(game !== undefined)
    return true;

  return false;
} 
```

让我们回顾一下开始一个新游戏的过程。

当玩家点击播放按钮时，我们会寻找一个现有的游戏加入他们。如果所述玩家找不到要加入的游戏，将创建一个新游戏。在我们的模型中，`player1`是创建游戏的玩家，`player2`是一个空字符串，`status`默认为“等待”。

因此，如果另一个玩家点击 play 按钮，他们将寻找一个带有空的`player2`字段和带有值“等待”的`status`字段的游戏。然后我们将该玩家设置为`player2`，并相应地更改`status`。

现在我们必须让我们的`GameLogic`类可以被 **games.js** 中的 Meteor 方法访问。我们将导出我们类的一个实例，然后导入到 **games.js** 文件中。在 **gameLogic.js** 文件的底部添加这一行，在类之外:

```
export const gameLogic = new GameLogic(); 
```

在 **games.js** 文件的顶部添加以下行:

```
import { gameLogic } from './gameLogic.js'; 
```

现在，我们可以向空的 **games.play()** 方法添加逻辑。首先，我们寻找一个状态为**“等待中”**的游戏，如果找不到其他游戏，我们就调用`newGame()`:

**lib/games.js**

```
Meteor.methods({
  "games.play"() {
    const game = Games.findOne({status: "waiting"});

    if(game === undefined) {
      gameLogic.newGame();
    }
  }
}); 
```

### 出版物

为了找到一个游戏，我们需要给客户端访问`games`集合的权限。为此，我们将创建一个[发布](https://guide.meteor.com/data-loading.html)。出版物让我们向客户展示我们希望他们看到的数据。然后我们**为**客户订阅**出版物**，以便让他们访问这些数据。

为了让玩家访问游戏集，我们将创建一个**‘Games’**出版物。但是当玩家被添加到一个新游戏中时，我们会给他们访问这个特定游戏中所有字段的权限。因此，也将有一个**【我的游戏】**的出版物。

转到服务器文件夹中的 **main.js** 文件，并将其内容替换为以下内容:

**server/main.js**

```
import { Meteor } from 'meteor/meteor';

Meteor.publish('Games', function gamesPublication() {
  return Games.find({status: "waiting"}, {
    fields:{
      "status": 1,
      "player1": 1,
      "player2": 1
    }
  });
});

Meteor.publish('MyGame', function myGamePublication() {
  return Games.find({$or:[
      {player1: this.userId},
      {player2: this.userId}]
    });
}); 
```

现在我们需要订阅“游戏”刊物。我们将在 UI 模板的 onCreated 方法回调中这样做。

用下面的代码在**客户端/js/** 中创建一个 **ui.js** 文件:

```
import { Meteor } from 'meteor/meteor';
import { Template } from 'meteor/templating';

Template.ui.onCreated(() => {
  Meteor.subscribe('Games');
}); 
```

### 播放事件

模板提供了一个[事件](http://blazejs.org/api/templates.html#Event-Maps)对象，我们可以在其中注册…你猜怎么着？答对了。事件。我们将在 UI 模板中创建一个事件。每当玩家点击 ID 为“play-btn”的 DOM 元素时，我们将设置一个会话变量`inGame`为真，我们将调用`games.play`方法，并订阅`MyGame`集合。

[Session](https://docs.meteor.com/api/session.html) 变量可以在客户端代码的任何地方使用，甚至可以在模板之间使用。要使用它们，我们需要添加[会话包](https://atmospherejs.com/meteor/session):

```
meteor add session 
```

转到 **ui.js** 文件，在`onCreated`方法后添加以下几行:

**client/js/ui.js**

```
Template.ui.events({
  "click #play-btn": () => {
    Session.set("inGame", true);
    Meteor.call("games.play");
    Meteor.subscribe('MyGame');
  }
}); 
```

导入我们在每个文件中使用的包是一个好习惯。因为我们使用了 **ui.js** 文件中的`Session`包，我们应该导入它。只需在顶部添加以下行:

```
import { Session } from 'meteor/session'; 
```

很好！现在我们需要添加几个助手。还记得，**ui.html**？快速浏览一下。我们使用了一个`inGame`助手和一个`status`助手。下面我们来声明它们的`events`对象:

**client/js/ui.js**

```
Template.ui.helpers({
  inGame: () => {
    return Session.get("inGame");
  },
  status: () => {

  }
}); 
```

如您所见，`inGame`助手返回存储在`inGame`会话变量中的值。我们暂时将`status`助手留空。

### 加入游戏

毕竟，你已经做了这么多，加入一个游戏应该很简单。

首先我们将把`joinGame`方法添加到`GameLogic`类中:

**lib/gameLogic.js**

```
joinGame(game) {
  if(game.player2 === "" && Meteor.userId() !== undefined) {
    Games.update(
      {_id: game._id},
      {$set: {
        "player2": Meteor.userId(),
        "status": game.player1
        }
      }
    );      
  }
} 
```

如你所见，我们传递了一个游戏变量，我们将`player2`字段设置为玩家的`_id`，将`status`字段设置为`player1`的`_id_`。这样我们就能知道该轮到谁了。

现在我们从`games.play()`开始调用这个方法。转到 **games.js** 文件，用以下内容替换`games.play`方法的内容:

**lib/games.js**

```
Meteor.methods({
  "games.play"() {
    const game = Games.findOne({status: "waiting"});

    if(game === undefined) {
      gameLogic.newGame();
    } else if(game !== undefined && game.player1 !== this.userId && game.player2 === "") {
      gameLogic.joinGame(game);
    }
  }
}); 
```

所以现在我们加了一个 **else if** 有三个条件:如果我们发现一个游戏*`player1`不是这个玩家*`player2`是空串，我们加入这个游戏。**

 **### 采取行动——逻辑

当我们为每个新游戏定义模型时，我们用一个空数组(`[]`)声明了一个 moves 字段作为默认值。一个**移动**将是一个 JSON 对象，由移动的玩家的`_id`和选择的位置组成。

转到 **games.js** 文件，在`games.play()`下面添加下面的方法。记住，`Meteor.methods`接受一个 JSON 对象，所以方法应该用逗号分隔:

**lib/games.js**

```
"games.makeMove"(position) {
  check(position, String);

  gameLogic.validatePosition(position);

  let game = Games.findOne({status: this.userId});

  if(game !== undefined) {
    gameLogic.addNewMove(position);

    if(gameLogic.checkIfGameWasWon()) {
      gameLogic.setGameResult(game._id, this.userId);
    } else {
      if(game.moves.length === 8) {
        gameLogic.setGameResult(game._id, "tie");
      } else {
        gameLogic.updateTurn(game);
      }
    }
  }
} 
```

让我们一行一行地检查这个方法。它接受一个字符串`position`作为参数。首先，我们使用[检查包](https://docs.meteor.com/api/check.html)来确保我们收到的是一个字符串，而不是一些可能损害我们服务器的恶意代码，然后我们验证这个位置。

在这之后，我们找到一个游戏，其中的`status`字段与玩家移动的`_id`相同；这样我们就知道轮到他们了。如果我们找到了那个游戏，或者换句话说，如果轮到那个玩家了，我们将把这步棋添加到我们的`moves`数组中。然后我们检查这一步之后游戏是否赢了。如果确实赢了，那么我们将当前玩家设置为赢家。否则，如果没有赢，但是数组中已经有八步棋了，那么我们宣布平局。如果还没有八步棋，我们更新回合让下一个玩家走。

就像我们处理 **ui.js** 文件中的`Session`包一样。我们应该在 **games.js** 文件中导入`check`包。你知道是怎么回事…在顶部加上下面一行。

```
import { check } from 'meteor/check'; 
```

我们使用了一堆来自`GameLogic`类的方法，这些方法我们还没有定义。所以，让我们开始吧。

转到 **gameLogic.js** ，在`GameLogic`类中添加以下方法:

**验证位置()**

```
validatePosition(position) {
  for (let x = 0; x < 3; x++) {
    for (let y = 0; y < 3; y++) {
      if (position === x + '' + y)
        return true;
    }
  }

  throw new Meteor.Error('invalid-position', "Selected position does not exist... please stop trying to hack the game!!");
} 
```

在这里，我们简单地在一个 3×3 的网格中移动，以确保发送的位置在其限制范围内。如果我们在网格中找不到客户端发送的位置，就会抛出一个错误。

**addNewMove()**

```
addNewMove(position) {
  Games.update(
    {status: Meteor.userId()},
    {
      $push: {
        moves: {playerID: Meteor.userId(), move: position}
      }
    }
  );
} 
```

在这里，我们使用 [$push](https://docs.mongodb.com/manual/reference/operator/update/push/) Mongo 操作符，将包含当前玩家`_id`和`position`的新招式推入数组。

**setGameResult()**

```
setGameResult(gameId, result) {
  Games.update(
    {_id: gameId},
    {
      $set: {
        "result": result,
        "status": "end"
      }
    }
  );
} 
```

再次使用 [$set](https://docs.mongodb.com/manual/reference/operator/update/set/) 操作符，我们将结果字段更新为`result`参数的值，该值可以是某个玩家的`_id`或“平局”，我们将`status`设置为“结束”。

**updateTurn()**

```
updateTurn(game) {
  let nextPlayer;

  if(game.player1 === Meteor.userId())
    nextPlayer = game.player2;
  else
    nextPlayer = game.player1;

  Games.update(
    {status: Meteor.userId()},
    {
      $set: {
        "status": nextPlayer
      }
    }
  );
} 
```

这个相当简单。我们将两个玩家都作为参数，并找出哪个是当前玩家，然后我们将`status`字段设置为另一个玩家的`_id`。

### 赢得比赛

从`games.makeMove`方法中还剩下一个方法需要声明；获胜的算法。还有其他更有效的方法来计算谁在 **TicTacToc** 游戏中获胜，但我决定在本教程中采用我能想到的最直观、最简单的解决方案。

转到 **gameLogic.js** 文件，在`GameLogic`类中添加以下方法:

**lib/gameLogic.js**

```
checkIfGameWasWon() {
  const game = Games.findOne({status: Meteor.userId()});

  const wins = [
  ['00', '11', '22'],
  ['00', '01', '02'],
  ['10', '11', '12'],
  ['20', '21', '22'],
  ['00', '10', '20'],
  ['01', '11', '21'],
  ['02', '12', '22']
  ];

  let winCounts = [0,0,0,0,0,0,0];

  for(let i = 0; i < game.moves.length; i++) {
    if(game.moves[i].playerID === Meteor.userId()) {
      const move = game.moves[i].move;

      for(let j = 0; j < wins.length; j++) {
        if(wins[j][0] == move || wins[j][1] == move || wins[j][2] == move)
        winCounts[j] ++;
      }
    }
  }

  for(let i = 0; i < winCounts.length; i++) {
    if(winCounts[i] === 3)
      return true;
  }

  return false;
} 
```

让我们仔细看看这个方法。

首先，我们找到当前的游戏。然后，我们声明一个包含所有可能的 win 组合的矩阵和另一个包含七个零的数组的变量:每个组合一个零。之后，我们将循环当前玩家的所有移动，并与每个组合的每个位置进行比较。对于每一个重合，我们将相应的`winCount`索引位置加 1。如果任何一个`winCount`指数加起来是 3，我们就知道当前玩家赢了。

如果你第一次没有得到它，不要担心。休息一会儿，喝点咖啡，然后用一双新鲜的眼睛再读几遍。对代码的解释可能会令人困惑。有时候，仅仅阅读代码并弄清楚它做什么甚至更好。

### 采取行动-控制者

我们这个游戏的玩家控制器只不过是一个简单的点击。所以实施起来应该是小菜一碟。让我们转到 **board.js** 文件，并将事件模板对象添加到我们的文件中的`helpers`之后:

**client/js/board.js**

```
Template.board.events({
  "click .selectableField": (event) => {
    Meteor.call("games.makeMove", event.target.id);
  }
}); 
```

简单吧？当玩家点击一个类为' selectableField '的 DOM 元素时，我们调用`games.makeMove`方法，将 DOM 元素的 id 作为位置参数传递。记住，我们是根据元素在网格中的位置来命名 id 的。如果需要的话，看看 board.html T2 的文件来帮你回忆一下。

### 显示移动

现在，在同一个文件中，我们将创建一个名为`isMarked`的助手，它将在`mark`和`selectableFields`之间切换。这样，我们将能够看到哪些位置已被选中，并让空位置被选中。

将该助手添加到`sideLength`助手下:

**client/js/board.js**

```
isMarked: (x, y) => {
  if(Session.get("inGame")) {
    let myGame = Games.findOne();

    if(myGame !== undefined && myGame.status !== "waiting") {
      for(let i = 0; i < myGame.moves.length; i++) {
        if(myGame.moves[i].move === x + '' + y) {
          if(myGame.moves[i].playerID === Meteor.userId())
            return "<p class='mark'>X</p>";
          else
            return "<p class='mark'>O</p>";
        }
      }
      if(myGame.status === Meteor.userId())
        return "<div class='selectableField' id='"+x+y+"'></div>";
    }
  }
} 
```

并将助手添加到模板:

**client/html/board.html**

```
...
<td class="field" id="{{rowIndex}}{{@index}}">
  {{{isMarked rowIndex @index}}}
</td>
... 
```

让我们检查一下这个函数。我们将一行和一列作为参数(x，y)。如果我们是`inGame`，我们寻找那个游戏。如果我们发现*和*的`status`是‘等待’，我们循环所有的移动，如果给定的**行+列**匹配我们的`moves`之一，我们将在棋盘上画一个 **X** 。如果它与另一个玩家的移动相匹配，我们将画一个 **O** 。

在每场比赛中，我们的走法总是一个 **X** ，而我们的对手是一个 **O** 。虽然，你的对手会看到他们的移动被画成一个 **X** 。我们并不真正关心谁有 **X** 或 **O** ，因为我们在不同的设备上玩，甚至可能在不同的国家。重要的是每个玩家都知道哪些是自己的，哪些是对手的。

### 显示状态

我们快完成了！还记得 **ui.js** 文件中的空`status`助手吗？用以下代码填充它:

**client/js/ui.js**

```
status: () => {
  if(Session.get("inGame")) {
    let myGame = Games.findOne();

    if(myGame.status === "waiting")
      return "Looking for an opponent...";
    else if(myGame.status === Meteor.userId())
      return "Your turn";
    else if(myGame.status !== Meteor.userId() && myGame.status !== "end")
      return "opponent's turn";
    else if(myGame.result === Meteor.userId())
      return "You won!";
    else if(myGame.status === "end" && myGame.result !== Meteor.userId() && myGame.result !== "tie")
      return "You lost!";
    else if(myGame.result === "tie")
      return "It's a tie";
    else
      return "";
  }
} 
```

这个很明显，但我还是会解释一下以防万一。如果我们是`inGame`，我们寻找当前的游戏。如果`status`等于‘等待’，我们告诉玩家等待对手。如果`status`等于玩家的`_id`，我们告诉他们该轮到他们了。如果`status`不是他们的`_id`，比赛还没有结束，我们告诉他们该轮到对手了。如果结果等于玩家的`_id`，我们告诉玩家他们赢了。如果比赛结束了，结果不是他们的`_id`和没什么“平局”，那么他们就输了。如果结果等于“平局”，我们告诉他们这是一个平局…咄！；)

像现在这样，你可以带着它转一圈。是啊！继续打开一个普通的浏览器窗口和一个私人标签，和你自己玩。试着不要玩得太开心，否则你会孤独终老(我发誓这是真的)。

### 注销

但是，我们还没有完成。没有。如果我们断开连接，让其他玩家自己呆着怎么办？那些已经完成的游戏占据了我们数据库的宝贵空间怎么办？我们需要跟踪玩家的连接，并采取相应的行动。

但是首先我们需要一种方法来让[移除](https://docs.mongodb.com/manual/reference/method/db.collection.remove/)游戏，让**移除**玩家。转到 **gamesLogic.js** 并在`GameLogic`类中添加以下方法:

**lib/gameLogic.js**

```
removeGame(gameId) {
  Games.remove({_id: gameId});
}

removePlayer(gameId, player) {
  Games.update({_id: gameId}, {$set:{[player]: ""}});
} 
```

`removeGame`方法将一个`gameId`作为参数并移除它。
`removePlayer()`将一个`gameId`和一个`player`(可以是`player1`或`player2`的字符串)作为参数，并清空该玩家在该特定游戏中的字段。

为了跟踪用户的连接，我们将安装一个名为 [mizzao:user-status](https://github.com/mizzao/meteor-user-status) 的有用包。到控制台，用 <key>ctrl</key> + <key>c</key> 关闭正在运行的 app，运行以下命令:

```
meteor add mizzao:user-status 
```

这个包有一个`connectionLogout`回调函数，它为一个参数提供了重要的信息，比如断开用户的`userId`。

转到 server 文件夹中的 **main.js** 文件，在底部添加以下回调。

**/server/main.js**

```
UserStatus.events.on("connectionLogout", (fields) => {
  const game = Games.findOne(
  {$or:[
    {player1: fields.userId},
    {player2: fields.userId}]
  });

  if(game != undefined) {
    if(game.status !== "waiting" && game.status !== "end") {
      if(game.player1 === fields.userId) {
        gameLogic.setGameResult(game._id, game.player2);
        gameLogic.removePlayer(game._id, "player1");
      } else if(game.player2 === fields.userId) {
        gameLogic.setGameResult(game._id, game.player1);
        gameLogic.removePlayer(game._id, "player2");
      }
    } else {
      if(game.player1 === "" || game.player2 === "") {
        gameLogic.removeGame(game._id);
      } else {
        if(game.player1 === fields.userId)
          gameLogic.removePlayer(game._id, "player1");
        else if(game.player2 === fields.userId)
          gameLogic.removePlayer(game._id, "player2");
      }
    } 
  }
}); 
```

因此，如果我们可以找到一个游戏，其中断开连接的玩家要么是`player1`要么是`player2`，我们检查该游戏的状态是否不是“等待”，并且该游戏还没有结束。如果有，我们将胜利给予对手，并移除断开连接的玩家。否则，我们要么删除游戏(如果任何玩家字段为空),要么。如果不是这种情况，我们将断开连接的玩家从游戏中移除。

就像我们对其他包所做的那样，我们应该导入`UserStatus`包。我们还在`connectionLogout`回调中使用了来自`GameLogic`类的一些方法，所以继续将它们都导入到 **server/main.js** 文件的顶部:

```
import { UserStatus } from 'meteor/mizzao:user-status';
import { gameLogic } from '../lib/gameLogic.js'; 
```

## 包扎

最后，你应该有一个工作游戏！事实上，你可以上传它，和你的朋友们一起试用…或者你自己。

如果我们所做的任何事情对你来说毫无意义，不要担心；如果你继续研究代码，很快就会明白的。你只是需要一些时间来理解一些概念。那是一个完全自然的过程。如果你卡住了，不要忘记[检查完成的应用程序](https://github.com/sitepoint-editors/meteor-tic-tac-toe)的代码。

当您对代码感到足够舒适时，您应该开始尝试添加一些功能。也许实现一个不同的获胜算法可以让你增加棋盘的大小。也许为玩家实现持久化以保存统计数据和游戏记录。你甚至可以实现一个登录界面，让玩家选择一个用户名。挑战一个朋友怎么样？当然，你也可以使用相同的概念来创建一个完全不同的游戏。

我很想看看你有什么想法，所以请告诉我！我希望你喜欢这个教程，在评论中留下你的疑问和评论。下一集再见！

## 分享这篇文章**