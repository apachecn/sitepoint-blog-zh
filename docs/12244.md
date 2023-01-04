# 用 PHP 创建一个时髦的宾果卡

> 原文：<https://www.sitepoint.com/build-a-buzzword-bingo-card-in-php/>

过去，我在政府部门做过相当多的工作。以下是我从那次经历中学到的三件事:

1.  政府工作人员喜欢开会
2.  政府工作人员也喜欢行话
3.  当你把会议和行话结合在一起时，你就具备了玩一场时髦的宾果游戏的绝佳条件

如果你还没有玩时髦的宾果游戏，你就错过了很棒的乐趣！这是一个游戏，你收集流行词汇、陈词滥调和含糊其辞的词汇，然后把它们排列成一个网格，在一个特别冗长的会议上，当你听到这些词汇时，就核对一下。当你检查完一行、一列或一条对角线时——答对了！

只需一点 PHP 初学者知识和大量的行话，你就可以制作自己的时髦词汇 bingo 卡，每次加载页面时都会随机生成。让我们来看看。

(*时髦词 Bingo 也有一个比 worksafe 更少的*名称，但因为我们希望这篇新闻简报不受任何诅咒过滤器的阻碍，所以我不能在这里重复它。)*

## 步骤 1:创建一个函数外壳

我们将创建一个名为`Bingo`的函数来生成一个随机的流行词 bingo 卡，然后我们可以将它包含在一个文档中。让我们从创建一个内部带有`Bingo`函数的文档外壳开始(我将我的外壳称为`bingo.php)`:

```
<?php
  function Bingo() {

  }
?>
```

## 第二步:编一些流行语

抓住一个你想在你的宾果卡片上看到的行话或短语的列表。你至少需要 25 英镑来填写一张宾果卡，所以请确保你有一个好的收藏。我刚刚花了几分钟时间在一个专门收集一些糟糕的管理用语的网站上闲逛，我挑选了一大袋我最喜欢的。

让我们将这些放入 PHP 函数内的数组中:

```
$buzzwords = array(
  "leverage",
  "synergy",
  "stakeholder",
  "touch-points",
  "knowledge initiatives",
  "cross-organizational collaboration",
  "strategic planning",
  "dynamics",
  "catalyst",
  "values-driven",
  "evangelize",
  "incentivize",
  "loop back",
  "let's take this offline",
  "360 degree thinking",
  "in the pipeline",
  "actioning",
  "paradigm",
  "2.0",
  "going forward",
  "game plan",
  "the end of the day",
  "thought leading",
  "on board",
  "monetize"
  );
```

## 第三步:洗牌

我们想确保我们的卡片有随机排列的流行词，所以我们将使用 [PHP 的`shuffle`函数](http://php.net/shuffle)来混淆数组中的条目:

```
shuffle($buzzwords);
```

## 第四步:准备一张桌子

宾果游戏就是在网格中填充行或列，所以我们将使用一个表格来排列项目。

让我们创建一个名为`$bingocard`的新变量，并开始准备表格标记。你可以看到我在中间留了一个空隙—这是我们稍后要放创建单元格和行的代码的地方。

```
$bingocard = "<table id='bingo'
summary='A random selection of 25 buzzwords
arranged in a bingo card'>";
$bingocard .= "<thead><tr>";
$bingocard .= "<th>B</th>
      <th>I</th><th>N</th>
      <th>G</th><th>O</th>";
$bingocard .= "</tr></thead>";
$bingocard .= "<tbody>";
$bingocard .= "<tr>";

// here's the gap 

$bingocard .= "</tr>";
$bingocard .= "</tbody>";
$bingocard .= "</table>";
```

## 步骤 5:创建单元格和行

我们现在需要从我们的 buzzwords 数组中的条目创建 25 个单元格。我们将使用一个`for`循环来遍历我们的混洗数组中的条目 25 次，并为每个条目创建一个表格单元格。那个`for`循环进入我们在上一步中留下的空隙。

我们的表格将是一个 5×5 的网格，所以我们还需要创建五行，每行有五个单元格。我们已经准备好了第一行的开始和最后一行的结束，所以我们还需要仔细检查以确保我们没有在最后一个单元格上这样做。

为了解决这个问题，我使用了一个变量`$rowend`，它是`$cell` + 1 除以 5 的余数。在我们创建每个单元格之后，有一个小的`if`语句来检查`$rowend`是否为零，并且它不是第 25 个单元格。如果是这种情况，我们将关闭并打开一个表格行元素。

这里是`for`循环:

```
for($cell=0; $cell<25; $cell++)
  {
    $rowend = ($cell + 1) % 5;
    $bingocard .= "<td>" 
     . $buzzwords[$cell] . "</td>";
    if($rowend == 0 && $cell < 24) {
      $bingocard .= "</tr>n<tr>";
    }
  }
```

## 第六步:回显表格

我们已经建立了一个数组，对它进行了洗牌，并把它变成了一个表格。现在剩下要做的就是打印它:

```
echo $bingocard;
```

…我们就快完成了！您现在应该有一个类似于[示例 1](https://www.sitepoint.com/examples/buzzwordbingo/bingo.phps) 的 PHP 文件。

## 第七步:把它放到一个文件中

现在让我们在网页中使用这个函数。在文档开头包含`bingo.php`文件:

```
<?php include ('/path/to/bingo.php'); ?>
```

现在，在需要放卡的地方调用`Bingo`功能:

```
<?php Bingo(); ?>
```

使用一些 CSS 样式的时髦词宾果表你喜欢的。你可以在[例 2](https://www.sitepoint.com/examples/buzzwordbingo/playbingo.phps) 中看到一个非常简单的宾果卡的例子，在[例 3](https://www.sitepoint.com/examples/buzzwordbingo/playbingo.php) 中看到结果。您甚至可能喜欢在一些 JavaScript 上分层，让玩家通过单击来检查每个单元格，或者在玩家完成宾果游戏时产生有趣的效果。

## 第八步:玩！

当你对卡片的外观感到满意时，就该把宾果卡的网址传给你的同事了。下次开会，给每个玩家打印一份，看谁先叫宾果！

## 分享这篇文章