# 开发一个简单的 jQuery 游戏来提高你的记忆力

> 原文：<https://www.sitepoint.com/developing-a-simple-jquery-game-to-improve-your-memory/>

本教程将向你展示如何使用 jQuery 和 HTML 创建一个简单的游戏。由此产生的游戏旨在帮助您提高记忆技能。我们将有一个 8×6 的正方形网格作为游戏窗口。一旦你开始游戏，鸟类的图像将出现在六个方格中，几秒钟后消失。你的目标是记住显示图像的位置并点击这些方块。

一旦你点击了六个方块，你将会根据你的准确度得到分数，而鸟将会在随机的位置再次显示。这一过程将持续到分配的时间到期。每准确点击一次，你将获得一分，如果你正确点击所有六只鸟，将获得 100 分的奖励。现在已经确定了游戏规则，我们可以继续创建开始屏幕。

## 设计屏幕布局

首先，游戏的 HTML 文件如下所示。游戏的所有统计数据都放在`game_results`元素中。您可以在此区域看到剩余时间、分数和某一关的点击次数。所有与游戏相关的窗口都放在`game_window`元素中。这包括初始启动屏幕，它包含在`start_window`元素中。一旦游戏开始，开始屏幕被隐藏，游戏在`play_window`元素中进行。最后，结果显示在`end_window`元素中。您可以在 styles 部分找到必要的 CSS 代码。

```
<html>
<head>
  <title>Simple jQuery Memory Game</title>
  <script src='jquery-latest.js'></script>
</head>
<body>
  <div id="game_results">
    <ul>
      <li>
        <div class="field_label">Time Left</div>
        <div class="field_value">
          <div id="timer">40</div>
        </div>
      </li>
      <li>
        <div class="field_label">Your Score</div>
        <div class="field_value">
          <div id="score">0</div>
        </div>
      </li>
      <li>
        <div class="field_label">Clicks Left</div>
        <div class="field_value">
          <div id="clicks"></div>
        </div>
      </li>
      <li>
        <div id="bonus" class="bonus">100 Bonus</div>
      </li>
    </ul>
  </div>
  <div id='game_window'>
    <div id='start_window'>
      <div id='msg_game'>jQuery Memory Checker Game</div>
      <div id='start_game'>Start</div>
    </div>
    <div id='end_window'>
      <div id='complete_game'></div>
    </div>
    <div id='play_window'></div>
  </div>
</body>
</html>
```

## 创建游戏窗口

首先，在进入任何功能之前，我们必须为游戏窗口创建元素。如前所述，`play_window`将用于游戏。在这个部分中，我们必须在 8 列 6 行上创建 48 个正方形。一些方块的代码如下所示。

```
<div id="game_0_0" data-x="0" data-y="0" class="game_box"></div>
<div id="game_0_1" data-x="0" data-y="1" class="game_box"></div>
<div id="game_0_2" data-x="0" data-y="2" class="game_box"></div>
<div id="game_0_3" data-x="0" data-y="3" class="game_box"></div>
<div id="game_0_4" data-x="0" data-y="4" class="game_box"></div>
<div id="game_0_5" data-x="0" data-y="5" class="game_box"></div>
<div id="game_0_6" data-x="0" data-y="6" class="game_box"></div>
<div id="game_0_7" data-x="0" data-y="7" class="game_box"></div>
```

上面的代码代表了播放窗口的第一行。我为`id`、`data-x`和 `data-y`属性使用了一个命名约定，这将在后面的章节中派上用场。现在我们已经设计好了游戏窗口，让我们继续创建启动游戏的函数。

## 开始游戏

一旦我们点击开始按钮，所有其他窗口需要关闭，以便游戏窗口可以显示。考虑下面的代码。

```
var game_interval;
var activeBirds = [];
var activeCount = 0;
var activeStatus = 1;

$("#start_game").click(function(){
  game_interval = setInterval(updateTimer,1000);
  $("#play_window").show();
  $("#start_window").hide();
  displayBirds();
});
```

前四行定义了全局变量。`game_interval`将保存`setInterval()`返回的值，而`activeBirds`是一个数组，将保存当前级别中鸟的位置。`activeCount`是单个关卡内的点击次数，`activeStatus`用于检查点击状态。在开始按钮的 click handler 函数中，我们以一秒的间隔调用`updateTimer()`函数来更新剩余的游戏时间。`updateTimer()`功能的代码如下所示。

```
function updateTimer(){
  var time_remaining = parseInt($("#timer").html());
  if(time_remaining == 0){
    window.clearInterval(game_interval);
    $("#play_window").hide();
    $("#start_window").hide();
    $("#end_window").show();
    $("#complete_game").html("You Have Scored "+$("#score").html());
  }else{
    $("#timer").html(--time_remaining);
  }
}
```

上面显示的函数将减少每秒的时间计数。当剩余时间变为零时，它通过调用 JavaScript 函数`clearInterval()`来停止计时器。然后，隐藏播放窗口，显示结果窗口。现在让我们转到 start 函数的其余部分。

```
$("#start_game").click(function(){
  game_interval = setInterval(updateTimer,1000);
  $("#play_window").show();
  $("#start_window").hide();
  displayBirds();
});
```

接下来，我们需要隐藏开始窗口，显示播放窗口。最后我们调用`displayBirds()`函数来显示小鸟，开始游戏。下一节将讨论`displayBirds()`功能。

## 展示要点击的鸟

一旦游戏开始，鸟会在每一关的六个随机位置出现。让我们考虑下面的`displayBirds()`函数。该函数循环执行，直到适当数量的鸟被添加到`activeBirds`数组中。在循环内部，生成随机的 X 和 Y 值来选择行号和列号。然后，我们检查生成的位置是否已经被一只鸟占据。如果这个职位有空缺，我们就接受。然后，鸟的图像被添加到生成的位置。

```
function displayBirds(){
  while (activeBirds.length < 6){
    var random_X = Math.floor(Math.random()*6);
    var random_Y = Math.floor(Math.random()*8);
    var array_index = $.inArray(random_X+""+random_Y, activeBirds);
    if(array_index == -1){
      activeBirds.push(random_X+""+random_Y);
    }
    $("#game_"+random_X+"_"+random_Y).html("<img src='bird.png' class='game_bird' />");
    level_interval = setTimeout(function(){
      $(".game_bird").remove();
      activeStatus = 1;
    },2000);
  }
}
```

最后，`setTimeout()`用于调用一个匿名函数，该函数隐藏了鸟。鸟儿会出现，然后两秒钟后消失。用户必须记住展示鸟类的地方，并开始点击以获得分数。下一节将重点介绍如何处理用户的点击并保持分数。

## 捕捉用户点击并生成点数

用户通过点击之前显示鸟类的方块来得分。一旦用户完成当前级别的点击，我们还必须为下一个级别生成鸟。处理点击事件的代码如下所示。

```
$(".game_box").click(function(){
  if(activeStatus == 1){
    // Section 1
    activeCount++;
    $("#clicks").html(6-activeCount);
    var clicked_x = $(this).attr("data-x");
    var clicked_y = $(this).attr("data-y");
    var clicked_box = clicked_x+""+clicked_y;
    // Section 2
    var array_index = $.inArray(clicked_box, activeBirds);
    if(array_index != -1){
      activeBirds.splice(array_index,1);
    }
    // Section 3
    if(activeCount == 6){
      var score = parseInt($("#score").html());
      score = score + (6-activeBirds.length);
      if(activeBirds.length == 0){
        score = score + 100;
        $("#bonus").slideDown("slow");
        $("#bonus").slideUp("slow");
      }
      $("#score").html(score);
      //Reset Variables
      activeCount = 0;
      activeBirds = [];
      displayBirds();
    }else{
      return;
    }
  }
});
```

首先，我使用`game_box`类选择器为每个方块的点击事件分配了一个函数。变量`activeCount`保存每个级别的用户点击次数。我们将在用户每次点击一个方块时增加它。然后，我们通过从六次点击中减去`activeCount`来显示剩余的点击。对于每次点击，我们还使用`data-x`和`data-y`属性获得方块的 X 和 Y 值。

接下来，我们使用 jQuery 的`inArray()`函数检查被点击的盒子是否在`activeBirds`数组中。每猜对一次，用户得到一分，该元素从`activeBirds`中移除。一旦用户没有点击，我们就使用`activeBirds`数组中剩余的条目来计算分数。如果所有的鸟都被匹配，用户将获得 100 点奖励。当所有的鸟都匹配时，我们使用 jQuery 的`slideDown()`和`slideUp()`函数在顶部显示一个默认隐藏的奖励标签。最后，我们重置所有变量并调用`displayBirds()`来显示下一关的鸟。

## 结论

在本教程中，我们创建了一个简单的 jQuery 游戏来提高您的记忆力。如果你对我们非常简单的游戏感到厌倦，你可以尝试添加以下一些功能。

*   添加具有不同点值的不同类型的鸟，而不是所有鸟的相同点数。
*   随着玩家对游戏的深入，增加每一关显示的鸟的数量。
*   为完成 500 或 1，000 分等目标提供额外的时间。

我希望你尝试一下这些功能。使用评论区让我知道可能改进游戏的新特性。尽情享受吧！

## 分享这篇文章