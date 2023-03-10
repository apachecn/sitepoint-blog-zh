# 使用 Phaser 的 JavaScript 游戏编程

> 原文：<https://www.sitepoint.com/javascript-game-programming-using-phaser/>

Phaser 是一个用于桌面和移动的 HTML5 游戏框架。它快速、免费、开源。Phaser 目前在版本 2.0.7 中[。它同时支持 WebGL 和 Canvas。它有很多功能可以帮助你开发游戏。它就像 ActionScript 3 的 Flixel 游戏框架。在本文中，我们将利用`Phaser.State`用 Phaser 构建一个游戏框架。为了简单起见，我们不会使用任何 Phaser 游戏对象，如精灵或群组。我也会提到一点补间动画。](http://www.html5gamedevs.com/topic/7944-phaser-207-amadicia-released/)

## 安装相位器

通过 bower 使用以下命令可以获得 Phaser:

```
bower install phaser-official --save
```

或者，你可以直接从 [GitHub](https://github.com/photonstorm/phaser) 获取文件。Phaser 的完整版本在`build`目录中。还有一些定制的构建，比如没有物理引擎的 Phaser，位于`build/custom`目录中。

有许多社区教程和入门指南。为了熟悉 Phaser，我建议您查看其中的一些。我最喜欢的一部是关于 Flappy Bird 的四集系列片。在[官方 Phaser 网站](http://phaser.io/)上可以找到大量的教程、[示例](http://examples.phaser.io/)和[文档](http://docs.phaser.io/)。

## 脚手架相位器

用于相位器的基于 Grunt 的约曼发电机可用于脚手架项目。您可以使用以下命令安装它:

```
npm install -g generator-phaser-official
yo phaser-official
```

这个生成器非常适合快速启动和运行，但是，出于本教程的目的，我们将从头开始。

## Phaser 目录结构

我们的 Phaser 项目将使用如下所示的目录结构。`main.js`开始游戏，而`app.js`定义我们的 Phaser 应用程序。`prefabs`目录用于你的游戏对象，而`states`目录用于你的游戏状态。

```
|-- app.js
|-- main.js
|-- prefabs
`-- states
    |-- boot.js
    |-- level_intro.js
    |-- level_master.js
    |-- level_round.js
    |-- main_intro.js
    |-- main_menu.js
    `-- preload.js
```

如果你用普通的`script`标签包含这些文件，你必须注意你包含它们的顺序。我更喜欢使用 RequireJS，另一种选择是 Browserify。

## `Phaser.State`

本节将重点介绍 [`Phaser.State`](http://docs.phaser.io/Phaser.State.html) 的使用。相位器状态封装了游戏的不同状态。游戏状态的例子有预加载、主菜单、1 级、2 级、帮助、暂停等。当一个状态开始时，你创建与该状态相关的游戏对象。稍后，您可以切换到不同的状态，Phaser 将清理您的旧游戏对象，以便您可以创建新的游戏对象并显示它们。

通过用一些[钩子方法](http://www.html5gamedevs.com/topic/1372-phaser-function-order-reserved-names-and-special-uses/)定义一个对象来定义一个状态。重要的有:

*   `init`–状态开始时调用的方法。它被传递一个参数以允许在状态之间共享数据。
*   `preload`–状态开始时调用的方法。它用于在加载任何东西之前加载资产。
*   `create`–在`preload`之后调用的方法，用于创建游戏对象。
*   `update`–为每个帧调用的方法，用于用户轮询和冲突检测。
*   `shutdown`–关闭状态时调用的方法，用于清理游戏对象。

## 组织状态流

这是相位器状态的状态图。`Boot`和`Preload`状态是用于设置配置和加载游戏资产的实际状态。`MainMenu`状态用于显示主菜单。其他级别状态用于实际游戏以及在不同级别和回合之间切换。这些级别被分成几轮。每一关都有几轮，一旦玩过，你就可以进入下一关。

![Phaser Game States](img/2e6d51e74501dc60c0abc90e6ea98663.png)

### 游戏状态

**引导**状态在`preload`钩子方法中加载预加载器资产，并通过`create`方法设置 Phaser 游戏设置，如缩放和输入指针。

`File: states/boot.js`

```
function Boot() {};

Boot.prototype = {
  preload: function() {
    // load preloader assets
  },
  create: function() {
    // setup game environment
    // scale, input etc..

    this.game.state.start('preload');
  }
};
```

**预加载**状态加载所有游戏资产，然后切换到`main-intro`状态。

`File: states/preload.js`

```
Preload.prototype = {
  preload: function() {
    // load all game assets
    // images, spritesheets, atlases, audio etc..
  },
  create: function() {
    this.game.state.start('main-intro');
  }
};
```

**MainIntro** 状态显示游戏介绍、徽标、片尾字幕等。它不需要一个`preload`方法，因为它补间一个对象两秒钟，然后切换到`main-menu`状态。我在这里添加了一个补间，只是为了给你一个概念，你可以使用渐变、收缩和滑动等效果来补间你的对象。

`File: states/main_intro.js`

```
function MainIntroState() {};

MainIntroState.prototype = {
  create: function() {
    // add main intro assets into the world
    this.tweenFadeState();
  },

  tweenFadeState: function() {
    this.game.add.tween({})
      .to({alpha: 1}, 2000)
      .onComplete.add(function() {
        this.game.state.start('main-menu');
      }, this);
  }
};
```

**主菜单**状态显示主菜单。然后，用户可以与菜单中的项目进行交互。为了简单起见，我添加了一个单独的键盘事件，它将触发一系列补间动画，并在结束时切换到`level-master`状态。链接补间对于组合动画很有用，如缩小菜单，然后淡化显示。

`File: states/main_menu.js`

```
MainMenuState.prototype = {
  create: function() {
    this.enterKey = this.game.input.keyboard
        .addKey(Phaser.Keyboard.ENTER);

    this.enterKey.onDown.add(this.tweenPlayState, this);
  },
  tweenPlayState: function() {
    var tweenMenuShrink = this.game.add.tween({})
          .to({x: 0, y: 0}, 200);

    var tweenFadeIn = this.game.add.tween({})
          .to({alpha: 1}, 2000);

    tweenFadeIn.onComplete.add(function() {
      this.game.state.start('level-master');
    }, this);

    tweenMenuShrink.chain(tweenFadeIn);
    tweenMenuShrink.start();
  }
};
```

请注意，在示例中，我没有添加任何有用的内容。为了简单起见，您应该在那里插入您的游戏对象。此外，我不创建任何游戏对象，但你可以在`create`方法中这样做。有关更多信息，请查看 Phaser 示例和文档。

**LevelMaster** 状态是一个无头状态，它决定切换到哪个状态。它在游戏世界里不显示任何东西。它的唯一目的是决定是否应该切换到`level-round`状态或`level-intro`状态，最重要的是，它在状态之间更新和传递游戏数据(`this.levelData`)。

`File: states/level_master.js`

```
LevelMasterState.prototype = {
  init: function(levelData) {
    if (!levelData) {
      levelData = {
        level: 0,
        round: 1,
        players: [
          { score: 0, skill: 1 },
          { score: 0, skill: 1 }
        ]
      };
    }

    this.levelData = levelData;
    this.winScore = 2;
  },

  create: function() {
    this.decideLevelState();
  }
};
```

当一个新的关卡开始时，`level-intro`状态开始。`level-intro`状态显示新关卡的介绍，就像显示你在哪个关卡。在`level-intro`之后，它切换到`level-round`，这是实际游戏发生的地方。

一轮结束后，要么是新的`level-round`要么是新的关卡。这个逻辑发生在我们的`decideLevelState`函数中。如果是第一关，或者我们有一关的赢家，我们就切换到下一关，否则就切换到下一轮。

`this.levelData`保存游戏数据，如游戏级别、游戏回合和玩家分数。我们在逻辑中更新它并传递状态。

`File: states/level_master.js`

```
LevelMasterState.prototype = {
  decideLevelState: function() {
    if (this.isFirstLevel() || this.getWinningPlayer() !== -1) {
      this.nextLevel();
    } else {
      this.nextRound();
    }
  },
  nextLevel: function() {
    this.levelData.level++;

    this.levelData.players.forEach(function(p) {
      p.score = 0;
    }, this);

    this.levelData.round = 1;

    this.game.state.start('level-intro', true, false, this.levelData);
  },
  nextRound: function() {
      this.levelData.round++;
      this.game.state.start('level-round', true, false, this.levelData);
  }
};
```

**关卡介绍**状态显示关卡介绍信息，比如你在哪个关卡，还有一些介绍动画。我们传递保存游戏数据的参数`levelData`。在`create`方法中，如果是游戏的第一关，我们通过显示*技能菜单*来使用`levelData`。我说的技能菜单是指玩家选择他们想玩的技能的菜单，但这取决于你。最后，它切换到`level-round`状态。

`File: states/level_intro.js`

```
LevelIntroState.prototype = {
  init: function(levelData) {
    this.levelData = levelData;
  },
  create: function() {
    var tweenIntro = this.tweenIntro();

    if (this.levelData.level === 1) {
      var tweenSkillMenuPop = this.tweenSkillMenuPop();

      tweenIntro.chain(tweenSkillMenuPop);
      tweenSkillMenuPop.onComplete.add(this.levelStart, this);
    } else {
      tweenIntro.onComplete.add(this.levelStart, this);
    }
  },
  levelStart: function() {
    this.game.state.start('level-round', true, false, this.levelData);
  },
  tweenIntro: function() {
    var tween = this.game.add.tween({})
      .to({alpha: 0}, 1000, Phaser.Easing.Linear.None, true);

    return tween;
  },
  tweenSkillMenuPop: function() {
    var tween = this.game.add.tween({})
      .to({x: 1, y: 1}, 500, Phaser.Easing.Linear.None, true);

    return tween;
  }
};
```

最后，**关卡回合**状态是实际游戏发生的地方。如果需要可以使用它的`update`方法。为了简单起见，我添加了一个简单的按键交互，当按下 Enter 时结束状态。这里重要的一点是，它切换回`level-master`，传递最初从`level-master`获得的`levelData`。

`File: states/level_round.js`

```
LevelRoundState.prototype = {
  init: function(levelData) {
    this.levelData = levelData;
  },
  create: function() {
    this.enterKey = this.game.input.keyboard
      .addKey(Phaser.Keyboard.ENTER);

    this.enterKey.onDown.add(this.roundEnd, this);
  },
  roundEnd: function() {
    this.nextRound();
  },
  nextRound: function() {
    this.game.state.start('level-master', true, false, this.levelData);
  }
};
```

这就完成了我们的状态流。所有这些为我们提供了一个水平状态循环，看起来像这样:

```
Boot -> Preload ->
main-intro -> main-menu ->
level-master -> Level1 ->
level-master -> L1 Round1 ->
level-master -> L1 Round2 ->
level-master -> Level2 ->
level-master -> L2 Round1 ->
```

您可以通过切换到`main-menu`状态的动作，在`level-round`状态下退出该循环。

## 启动相位器

现在，我们将开始相位游戏。将这个`div`放入你的页面。相位器会把它的画布放在那里。

`File index.html`

```
<div id="game-area"></div>
```

我们必须创建一个`Phaser.Game`，将我们所有的状态添加到`StateManager`，并开始引导状态。

`File: app.js`

```
function Game() {}

Game.prototype = {
  start: function() {
    var game = new Phaser.Game(640, 480, Phaser.AUTO, 'game-area');

    game.state.add('boot', BootState);
    game.state.add('preload', PreloadState);
    game.state.add('main-intro', MainIntroState);
    game.state.add('main-menu', MainMenuState);
    game.state.add('level-master', LevelMasterState);
    game.state.add('level-intro', MainIntroState);
    game.state.add('level-round', LevelRoundState);
    game.state.start('boot');
  }
};
```

最后，使用下面的代码启动游戏。

`File: main.js`

```
var game = new Game();
game.start();
```

## 结论

我们的文章到此结束。这只是一个相位游戏的框架。Phaser 还为您提供了许多其他东西，如精灵、动画、声音、物理、缩放等等。你可以在 [GitHub](https://github.com/eguneys/phaser_states) 上找到我们例子的回购。

## 分享这篇文章