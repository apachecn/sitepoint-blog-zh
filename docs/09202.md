# 数组处理函数

> 原文：<https://www.sitepoint.com/array-handling-functions/>

在我的关于 PHP 数组的前一篇文章中，我提出了许多表格形式的东西，因此也可以用数组来表示。在本文中，我将使用一套扑克牌来探索 PHP 程序员最常需要的一些内置数组函数。

为了突出 PHP 提供的一些数组处理函数，我将使用 [Buraco](http://en.wikipedia.org/wiki/Buraco "Buraco - Wikipedia, the free encyclopedia") 的一些组件，这是一个在我所在的地区非常流行的游戏，与拉米纸牌游戏非常相似。现实世界中的布拉科牌有两副牌(104 张牌)和两张百搭牌。它也有一个存放所有未发给玩家的牌的堆栈，但是我不会在这里使用它们，所以你不必担心它们。

## 代表一副牌

卡片可能可以追溯到九世纪，那是中国第一次使用纸张的时候。他们沿着其他来自东方的发明的道路前进——首先是阿拉伯世界，然后被带到欧洲，后来进入新世界。目前最流行的形式是“法国包”，一副扑克牌有 52 张牌，分成四种花色，梅花(♣)、方块(♥)、红心(♠).)每种花色有 13 张牌，或面:A，2，3，4，5，6，7，8，9，10，J，Q 和 k。

您可以编写数组来保存花色和牌面，如下所示:

```
<?php
$suits = array ("clubs", "diamonds", "hearts", "spades");
$faces = array (1 => "A", "02", "03", "04", "05", "06", "07", "08", "09", "10", "11", "12", "13");
```

两者都是数字索引数组，也就是说，它们有基于整数的键。因为我在定义`$suits`的时候没有显式给任何键，所以 PHP 自动给它们分配从 0 开始的键。所以，`$suits[0]`的值是“梅花”，而`$suits[3]`的值是“黑桃”。然而，我提供了$faces 的第一个元素的关键。PHP 通过获取最大整数索引并加 1 来分配每个新键。`$faces[1]`是“A”，`$faces[2]`是“02”，`$faces[3]`是“03”，以此类推。你会注意到我强迫 PHP 用 1 开始索引`$faces`,所以数字卡片的表面和它们各自的键是一样的。

您可以使用两个`foreach`循环来创建所有 52 张牌的主数组，每张牌都以*牌面|花色*的格式表示为一个字符串，如下所示:

```
<?php
$deck = array();
foreach ($suits as $suit) {
    foreach ($faces as $face) {
        $deck[] = $face . "|" . $suit;
    }
}
```

上面代码的结果与您手动填充`$deck`的结果相同:

```
<?php
$deck = array("A|clubs", "02|clubs", "03|clubs", "04|clubs", ... "Q|spades", "K|spades");
```

一些更有经验的读者可能会问为什么不使用嵌套数组而不是字符串，例如:

```
<?php
$deck = array();
$deck["A"] = array("clubs", "diamonds", "hearts", "spades");
$deck["02"] = array("clubs", "diamonds", "hearts", "spades");
$deck["03"] = array("clubs", "diamonds", "hearts", "spades");
...
```

嗯，这就是它的妙处:字符串有时可以被视为非关联的、单级数组，但仍然是数组！事实上，用来计算数组中元素数量的函数`count()`也可以用来计算字符串中字符的数量！稍后您将看到如何将字符串转换成数组。

## 发牌

让我们从洗牌开始，随机发 11 张牌。为此，您可以使用函数`array_rand()`。它返回一个从原始数据中随机抽取的密钥数组。该函数被设计为返回*键*而不是*值*，因此它可以像处理单级数组一样有效地处理嵌套数组，并且如果您知道键，您总是可以获得值。

```
<?php
$myKeys = array_rand($deck, 11);
$myHand = array();
foreach ($myKeys as $key) {
    $myHand[] = $deck[$key];
    unset($deck[$key]);
}
```

最初，创建临时的`$myKeys`数组，其值是在`$deck`中找到的 11 个随机键。然后一个`foreach`循环使用`$myKeys`的值将相应的值从`$deck`获取到`$myHand`。当然，这并没有从原来的甲板上删除元素。如果你再次调用`array_rand()`,你完全有可能得到几个键来重新索引一些已经抽过的牌！为了确保这种情况不会发生，调用`unset()`从`$deck`中删除元素，以确保它不能被重用。

要找出一张牌，比如“06 |红心”(6♥)，是否在已发的那手牌中，可以使用`in_array()`功能。它首先接受一个*针*，要搜索的目标值，然后接受*干草堆*，要搜索的数组。

```
<?php
if (in_array("06|hearts", $myHand)) {
    echo "I found it!";
}
```

作为对 needles 和 haystacks 的补充说明，其他语言的传播者喜欢挑 PHP 的毛病(当然反过来也是如此！).我唯一无法反驳的批评是 PHP 相似函数之间令人恼火的不一致的参数顺序。有些函数像`in_array()`先收针，有些函数先收草堆。我认识一些长期的 PHP 开发人员，他们仍然很难记住一些函数使用的顺序，所以如果你发现自己总是需要查看在线文档，不要太担心。

`in_array()`只返回是否在数组中找到了什么，而不是值的键。大多数时候这就足够了。但是如果您也需要知道密钥，可以考虑使用`array_search()`。

良好的管理是很重要的，由于用*面|套*的方式来表示卡片，分类就像使用`sort()`一样简单。此函数按字母和数字的升序对数组元素进行排序:

```
<?php
sort($myHand);
```

函数的独特之处在于它根据自己的参数进行操作！如果您想保留`$myHand`的原始顺序，您必须在排序前将其复制到另一个变量:

```
<?php
$preservedHand = $myHand;
sort($myHand);
```

## 融合和丢弃

Buraco 和 rummy 一样，是一种跑步游戏——游戏顺序放在桌子上。将牌从手中移到一轮的行为被称为*融合*。例如，如果牌 9、10、J 和 Q 在`$myHand`中，则它们可以被合并成一个回合。从程序上来说，这意味着从`$myHand`中取出卡片，并将它们放入一个新的数组`$myRuns`。您可以使用函数`array_slice()`，它从数组中返回一系列元素的副本，非常类似于`substr()`处理字符串的方式。假设序列占据了`$myHand`中的位置 0 到 3:

```
<?php
$myRuns = array();
$myRuns[] = array_slice ($myHand, 0, 4);
$myHand = array_slice($myHand, 5, 7);
```

第一行创建了数组`$myRuns`，第二行添加了一个由从索引 0 开始的`$myHand`的 4 个元素组成的子数组，索引 0 是数组中的第一个位置。`array_slice()`不从原始数组中删除切片，这是一个问题，因为你需要从你的手牌中取出你融合或丢弃的牌。这里的解决方案是再次使用`array_slice()`将`$myHand`的其他元素重新分配给它自己。

在他的回合开始时，一名玩家必须从牌堆中拿一张牌，或从弃牌堆中拿所有牌，并将它们加到他的手上。这两个选项都可以使用`array_merge()`功能完成。该函数返回一个数组，该数组由给定数组中的所有元素按照提供的顺序组成。例如，假设游戏的状态是这样的:

**玩家的手:**、【7】、
**运行:**【9】、【10】、【j】、
**拨号堆叠:**

 **要捡起弃牌堆中的所有牌，并将它们添加到玩家手中，您可以写:

```
<?php
$myHand = array_merge($myHand, $discarded);
```

之后，`$myHand`将包含:

```
array("02|clubs", "5|hearts", "06|spades", "06|hearts", "07|diamonds", "08|hearts", "13|spades", "07|spades", "11|spades", "04|hearts", "04|diamonds")
```

## 展示你的手

在浏览器中显示一张或多张卡片会很好。我的策略是使用 *face|suit* 符号来创建一些显示脸部和西装图形的 HTML 图像标签。虽然它不太可能是现实世界应用程序的有效解决方案，但它确实为我提供了一个机会，向您展示最后一个功能，`explode()`。

```
<?php
foreach ($myHand as $card) {
    $tmpArray = explode("|", $card);
    echo '<img src="img/face_' . $faces[$tmpArray[0]] . '.png">";
    echo '<img src="img/suit_' . $suits[$tmpArray[1]] . '.png">";
    echo "&nbsp;"; // Just a blank space for visual separation 
}
```

`foreach`遍历`$myHand`中的所有牌，并在每次迭代中返回一个*面|花色*值作为`$card`。`explode()`函数获取每个`$card`值，并使用竖线作为分隔符将其分成两部分。然后，它返回一个数组，数组中的元素数与部件数一样多。我在这里使用竖线，但是`explode()`可以处理任何字符或字符序列。

## 摘要

PHP 有超过 70 个与数组相关的函数。在本教程中，您已经看到了如何使用其中的一小部分来执行一些基本的数组操作。这些函数是你经常用来解决编程问题的工具，或者，我更愿意称之为挑战。熟悉它们是很重要的，这样当你计划你的剧本时，你可以根据你需要的结果做出合理的决定。

<small>图片 via [辛迪·哈格蒂](http://www.shutterstock.com/gallery-1011p1.html "Stock Photos | Shutterstock: Royalty-Free Subscription Stock Photography & Vector Art")/[Shutterstock](http://www.shutterstock.com "Stock Photos and Royalty-Free Images by Subscription")</small>

## 分享这篇文章**