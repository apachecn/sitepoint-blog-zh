# 红宝石高尔夫:结果！

> 原文：<https://www.sitepoint.com/ruby-golf-the-results/>

回到 11 月，我写了一篇关于 Ruby Golf 的文章。在文章的最后，我设置了一个 5 洞的挑战，让读者尝试使用最少的字节数来完成。有一个了不起的反应，看到了一些令人印象深刻的“镜头”。

截止日期在 2011 年底过去了，从那时起，我一直在浏览所有参赛作品，寻找每个洞的获胜者。有一些很棒的解决方案展示了 Ruby 的一些有见地的用法。公平地说，虽然它们中的大多数乍一看像令人难以置信的天书…虽然我认为这是代码高尔夫的课程！

除了用这篇文章来宣布获胜者之外，我认为回顾一下每个获奖作品中使用的一些技巧可能是有用的。因此，如果你觉得自己陷入了一个粗糙的代码补丁或精神沙坑，那么请继续阅读——这里有很多东西可以学习。前进！

## 示例孔

作为本文的一部分，我将它作为 Ruby Golf 的一个例子:

写一个方法，找出一个给定数字的所有倍数的和，直到一个给定值。例如，sum(5，24)将计算 5 到 24 的所有倍数之和(即 5 + 10 + 15 + 20)。这是我的方法:

```
def sum(n,t)
  n*(1..t/n).to_a.inject(&:+)
end
```

不用说，我的缺陷肯定可以更好，而且有许多改进，读者只是太热衷于指出来。

Kyle Putnam 发现不需要`to_a`方法，因为`inject`是 ranges 继承自的可枚举类的方法，节省了 5 个字节:

```
def sum(n,t)
  n*(1..t/n).inject(&:+)
end
```

Rein Henrichs 还指出，在 Ruby 1.9 中不需要&符号，也可以使用 reduce 方法，而不是 inject(它有相同的字母数，但可能有更多的含义):

```
def sum(n,t)
  n*(1..t/n).reduce(:+)
end
```

但是凯尔·迪恩用一点初等数学就打出了惊人的一杆进洞。已经有一个对数字求和的公式，可以用来代替在一个范围内迭代:

```
def sum(n,t)
  k=t/n;n*k*(k+1)/2
end
```

这是一个很好的例子，它避免了花哨的方法和晦涩的技巧，而是仅仅依靠老式的数学来完成工作。

## 第一洞:嘶嘶的嗡嗡声

给定一个数字，如果它是 3 的倍数，函数返回“Fizz ”,如果它是 5 的倍数，函数返回“Buzz ”,如果它是 15 的倍数，函数返回“FizzBuzz”。如果数字不是 3 或 5 的倍数，则数字以字符串形式返回。

这是一个竞争激烈的洞，但 Marcin Mańk 用这种重量为 41 字节的努力直击球道。

```
def fizzbuzz n
    n%3<1&&f=:Fizz;n%5&lt;1?"#{f}Buzz":"#{f||n}"
  end
```

这个条目使用模运算符来检查 n 是否是 3 的倍数。如果是，那么 n%3 实际上将是 0，但是由于我们只处理整数，所以可以通过测试它是否小于 1 ( `n%3<1`)而不是等于 0 ( `n%3==0`)来节省一个字节。

如果 n%3<1 的值为 true(即 n 是 3 的倍数)，第一条语句使用&&运算符来设置 f=:Fizz(注意这里使用了符号，因为它比字符串少使用一个字节)。如果`n%3<1`为假(即不是 3 的倍数),则第二部分不会到达，因此变量`f`不会被设置为任何值。

在表示一行代码结束的`;`之后，进行类似的测试，看看 n 是否是 5 的倍数。三元运算符用于显示一个结果(如果为真),另一个结果(如果为假)。如果 n 是 5 的倍数，则返回以下字符串:

```
"#{f}Buzz"
```

这个的巧妙之处在于使用了字符串插值在 Buzz 这个词前面插入 f 的值。如果 f 被设置为:Fizz(即 n 也是 3 的倍数，因此是 15 的倍数)，那么它会自动转换为一个字符串，并插入单词 Buzz 的前面。如果 f 没有被设置(即，如果 n 不是 3 的倍数，但仍然是 5 的倍数)，那么`#{f}`被简单地忽略，返回的只是字符串“Buzz”。

如果 n 不是 5 的倍数，则返回以下字符串:

```
"#{f||n}"
```

这是对`||`操作符的巧妙使用，给出了两个可能的选项。如果设置了 f，将显示“Fizz ”(作为字符串而不是符号，因为再次使用了插值)。条件的第二部分只有在第一部分返回 false(即 f 未被设置)时才会被满足，然后将显示输入的数字(同样，由于插值而显示为字符串)。

一个建立在赛勒斯作品基础上的绝妙解决方案。能打吗？

## 第二洞:凯撒密码

实现凯撒移位密码

示例:

```
caesar("hello",3) => "khoor"
```

你也应该能够产生负的变化。

我喜欢代码和密码，所以对这个问题的解决方案特别感兴趣。最好的一个只有 29 字节，多亏了乔希·齐克:

```
def caesar(s,n)
    s.gsub(/./){|c|(c.ord+n).chr}
  end
```

这是一个非常干净有效的方法，它使用正则表达式`/./`和一个将每个字符前移 n 位的块，简单地使用`gsub`方法来替换每个字母。Ruby 的弱点表明，要添加到一个字符串中，必须首先使用`ord`方法将其转换为整数表示，然后使用`chr`方法将其变回字符串。

## 第三洞:石头、布、剪刀游戏

编写一个简单的“玩”这个游戏的方法，玩家输入他们的“移动”作为方法的参数。如果玩家输入了一个无效的选项，那么结果应该是“输”。计算机应该随机选择它的移动。输出给出了计算机的“移动”和以逗号分隔的字符串形式的结果。

这是我最喜欢的洞，也是参赛人数最多的洞。获奖作品是一件美丽的事情，我无法理解如何将这个无处不在的儿童游戏浓缩成一个小小的 84 个字符:

```
def play(i)
  m=%w(Rock Paper Scissors);m [/c]+，+%w(平局赢输)[(m . index(I)| | c-1)-c)% 3]
end

它首先使用创建三个“移动”的数组的快捷表示法，并将其赋给一个变量。赋值通常是高尔夫编程中要避免的事情，因为它浪费了几个字节。但是在这种情况下，这是必要的，因为稍后将再次使用该数组。

然后，下一条语句只是构建将由该方法返回的字符串。它首先从移动数组(m)中随机选择计算机移动，使用`rand`方法计算计算机的“移动”,但是使用一个巧妙的技巧将随机数赋给变量 c，同时使用它来引用移动数组的索引。这意味着以后可以再次使用 c:

```
接下来，一个逗号连接到字符串。逗号是使用快捷方式`?,`创建的，它比更明显的字符串文字`','`少一个字节。

```
+?,

```

最后，游戏的结果——赢、输或平——被连接到最后。结果由以下代码位决定:

```
+%w(Draw Win Lose)[((m.index(i)||c-1)-c)%3]

```

`m.index(i)`找到玩家移动的索引。如果这不是“石头”、“布”或“剪刀”，那么它被替换为比计算机移动少 1(在该方法的前面被设置为 c)。这将保证玩家会输，你很快就会看到。接下来是一个聪明的小算法，使用模 3 算法计算你是赢了，还是输了。如果你从你自己的移动指数中减去计算机移动的指数，那么根据游戏的结果答案是不同的:

```
eg 
You play Rock (index = 0). Computer plays Rock(index = 0). This is a draw:
0 - 0 = 0 = 0 mod 3
You play Rock (index = 0). Computer plays Scissors(index = 2). This is a win:
0 - 2 = - 2 = 1 mod 3
You play Rock (index = 0). Computer plays Paper(index = 1). This is a loss:
0 - 1 = - 1 = 2 mod 3
```

根据比赛的结果(赢、输或平)，结果是不同的。这些结果对于其他 6 个可能的结果是相同的(试一试),这意味着如果您将数组构造为%w(平局赢输),那么索引与减去移动的结果相匹配。

## 孔 4:字符串计数器

编写一个方法，当给定一个字符串和子字符串时，返回该子字符串在该字符串中出现的次数(忽略大小写)。

这似乎是一个相对简单的解决方案，使用扫描方法很容易解决。Marcin Mańk 是第一个使用这种方法的人，但是他犯了一个小学生的错误，使用了长度方法而不是长度方法，因为长度方法要短 2 个字节。Christian Guenther 很快指出了这一点，因此他赢得了这个 20 字节解决方案的奖项:

```
def count(s,x) 
    s.scan(/#{x}/i).size
  end
```

这种方法的工作原理是使用内插法将所需的子字符串更改为正则表达式。然后，该字符串的每次出现都被放入由 scan 方法返回的数组中。要找到字符串出现的次数，只需找到这个数组的大小，所以 size 方法可以链接到最后。

## 第五洞:摇摆功能

写一个函数来代替“把你的钥匙放在罐子里”。该函数的参数是包含两个对象的数组的数组。该函数返回一个新数组，其中的对象对已经混合在一起。一个对象不应该以它的原始“伙伴”结束。

示例:

```
swingers([["Homer","Marge"],["Micky","Minnie"],["Fred","Wilma"],["Peter","Lois"],["George","Judy"]])
=> [["Homer","Wilma"],["Micky","Lois"],["Fred","Judy"],["Peter","Marge"],["George","Minnie"]]
```

Josh Cheek 的条目最少，有 30 个字节:

```
def swingers(s)
      f,l=s.transpose;f.zip l.rotate
    end
```

此解决方案使用 mass 赋值将夫妻数组转置的结果赋给两个变量 x 和 y。这实际上是将所有男性放在 x 中，将所有女性放在 y 中。然后使用 zip 函数将这两个数组合并在一起，但 rotate 方法首先应用于 y 数组(女性数组)。这有效地将每个女人转移到一个地方。这意味着第一个男人和下一个女人被放在一起。

这满足了指定的要求，尽管隐含的要求是混合应该是随机的。这很容易通过对原始数组应用`shuffle`方法来实现。这实际上是雷米早些时候做的，他因这个洞而获奖:

```
def swingers(a)
  x,y=a.shuffle.transpose;x.zip y.rotate
end
```

## 全部的

在如此激烈的竞争之后，我很高兴地宣布五个洞的总得分最低的总获胜者……而无冠红宝石夹克的获胜者……是……乔希·齐克！祝贺 2011 年 RubySource 大师赛冠军！

如果你是上面提到的个人获奖者之一，那么有一本 Sitepoint book 在等着你。你只需要在下面留下你的评论就可以了。祝贺所有获奖者，以及所有参与的人。请记住，这是试图找到一个有助于提高您的编码技能的解决方案的过程，就像在真正的高尔夫球场上一样，熟能生巧。通过通读所有的解决方案，我确实学到了很多 Ruby。看到使用多种不同的方法来达到同一个解决方案是很有趣的。我还喜欢这样一个事实，即许多参赛作品建立在以前的尝试基础上——相当于走相同的路线到达果岭，然后打一个长推杆赢得洞！但是这些是最好的答案吗？我们永远不能 100%确定一个解决方案是尽可能小的，所以有人能打败他们吗？你从这些条目中学到了什么新的很酷的 Ruby 技巧吗？或者你还有其他没有提到的技巧吗？请在评论中告诉我们。

但是 Ruby Golf 最棒的一点是，在一轮紧张的编程结束后，您仍然可以在一天结束时前往第[19 洞](http://en.wikipedia.org/wiki/Nineteenth_hole)！

