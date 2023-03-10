# GSwR IV:在数组和散列上走了弯路

> 原文：<https://www.sitepoint.com/gswr-iv-going-loopy-arrays-hashes/>

在上一篇文章中，我们讨论了数字和它们的方法。在这篇文章中，我们将看看 Ruby 的一些集合，比如数组、散列和范围。我们将看看它们是什么，它们是用来做什么的，它们能做什么。我们还将学习如何迭代集合以及如何创建循环。最后，我们将创建一个模拟洗牌的程序，然后将它开发成一个在线游戏，使用 Sinatra 正确玩牌。

## 数组

数组是对象的有序列表。您可以通过将对象放在方括号内以逗号分隔来创建数组文字:

```
primes = [2, 3, 5, 7, 11]
```

您可以在数组中放置任何类型的对象，如字符串:

```
fruits = ["apples" "pears" "bananas"]
```

你甚至不必使用相同类型的对象:

```
mixture = [1, "two", 3.0, "IV" ]
```

你甚至可以有一个数组的数组，称为多维数组。这可以用来创建一个坐标系统，如下所示:

```
coordinates = [[1,3],[4,2]]
```

## 数组方法

数组是 Ruby 工具包中一个强大的工具，有一些非常有用的方法。为了演示其中的一些，我将使用一组史塔克家的孩子:

```
starks = %w(Rob Sansa Arya Bran Rickon)
=> ["Rob","Sansa","Arya","Bran","Rickon"]
```

这使用稍微不同的语法来创建数组。如果您正在创建的数组是由一个单词的字符串组成的，那么您可以在开头使用%w 标志来表明这一点，然后省略数组内字符串上的引号以及分隔它们的逗号。

要访问数组中的特定值，我们将它在数组中的位置(称为索引)写在方括号中

```
starks[1]
=> "Sansa"
```

注意，这不是数组中的第一个词，因为编号是从零开始的。这个编号系统也使用负值从后面开始计数，所以`starks[-1]`将返回数组中的最后一个值:

```
starks[-1]
=> "Rickon"
```

还有专门查找数组中第一项和最后一项的方法:

```
starks[0]
=> "Rob"
starks.first
=> "Rob"
starks[-1]
=> "Rickon"
starks.last
=> "Rickon"
```

我们还可以通过指定第二个参数来返回数组的子集，该参数指示子数组的长度:

```
starks[2,3]
=> ["Arya","Bran","Rickon"]
```

或者，我们可以提供一个 Range 对象(稍后将详细介绍 ranges ),它将产生从一个索引到另一个索引的子集:

```
starks[2..4]
=> ["Arya","Bran","Rickon"]
```

如果我们想知道一个数组中有多少项，那么我们可以使用`length`方法，谢天谢地它没有从零开始计数:

```
starks.length
=> 5
```

使用`include?`方法找出数组是否包含对象:

```
starks.include?("Arya")
=> true
starks.include?("Jon")
=> false
```

要在数组末尾添加一个新值，使用`push`方法:

```
starks.push("Jon")
=> ["Rob","Sansa","Arya","Bran","Rickon","Jon"]
```

有一个常用的速记操作符可以做到这一点:

```
starks << "Jon"
=> ["Rob","Sansa","Arya","Bran","Rickon","Jon"]
```

我们还可以使用`pop`方法移除数组中的最后一个对象:

```
nightswatchman = starks.pop
=> "Jon"
```

我们可以对数组进行排序(默认情况下，字符串对象是按字母顺序排序的):

```
starks.sort
=> ["Arya","Bran","Rickon","Rob","Sansa"]
```

不过，这实际上并没有改变数组的顺序:

```
starks
=> ["Rob","Sansa","Arya","Bran","Rickon"]
```

为此，我们需要使用`sort`–`sort!`的“爆炸”方法:

```
starks.sort!
=> ["Arya","Bran","Rickon","Rob","Sansa"]
starks
=> ["Arya","Bran","Rickon","Rob","Sansa"]
```

现在数组的顺序已经永远改变了。

`reverse`方法(它做它在锡上所说的)有一个类似的爆炸方法:

```
starks.reverse
=> ["Sansa", "Rob", "Rickon", "Bran", "Arya"]
starks
=> ["Arya", "Bran", "Rickon", "Rob", "Sansa"]
starks.reverse!
=> ["Sansa", "Rob", "Rickon", "Bran", "Arya"]
starks
=> ["Sansa", "Rob", "Rickon", "Bran", "Arya"]
```

我们可以使用`join`方法将一个数组的所有元素连接成一个字符串。这需要一个参数来指示您希望用作分隔符的内容:

```
starks.join(",")
=> "Sansa,Rob,Rickon,Bran,Arya"
```

## 混杂

散列是键和值对的列表。我们可以通过将散列文字放在花括号内来创建它

```
stark = { :name => "Eddard Stark" }
```

使用键的符号是一种常见的做法，因为它们。从 Ruby 1.9 开始，有一种简写方式可以使用:

```
stark = {   name: "Eddard Stark",
            lady: "Catelyn Stark",
            sigil: "Direwolf",
            motto: "Winter is Coming",
            residence: "Winterfell",
            children: ["Rob","Sansa","Arya","Bran","Rickon"]
        }
```

要访问散列中的值，只需引用键:

```
stark[:motto]
=> "Winter is Coming"
```

您甚至可以使用嵌套散列:

```
houses = { :stark => { sigil: "Direwolf", residence: "Winterfell" },
       :lannister => {sigil: "Lion", residence: "Casterly Rock" }
     }
```

嵌套散列中的值可以通过按顺序引用每个键来访问:

```
houses[:lanister][:sigil]
=> "Lion"
```

## 范围

范围可用于表示一系列值。您可以通过用 2 点(含)或 3 点(不含)分隔范围的起点和终点来创建范围。比如`1 .. 10`是 1 到 10 的整数范围，包括 10，`1 ... 10`是 1 到 9 的整数范围(不包括 10)。

我们还可以创造一系列字母:

```
alphabet = "a".."z"
=> "a".."z"
```

不幸的是，您不能像访问数组和散列那样访问一个范围的单个值:

```
alphabet[2]
NoMethodError: undefined method `[]' for "a".."z":Range
  from (irb):26
  from /home/daz/.rvm/rubies/ruby-2.0.0-p247/bin/irb:16:in `<main>'
```

但是有一个叫做`to_a`(to Array 的缩写)的简便方法可以将我们的范围转换成一个数组:

```
alphabet.to_a
=> ["a", "b", "c", "d", "e", "f", "g", "h", "i", "j", "k", "l", "m", "n", "o", "p", "q", "r", "s", "t", "u", "v", "w", "x", "y", "z"]
```

范围最常用于整数和字符串，但实际上可以用于任何可比较的对象(也就是说，它有一种确定顺序的方法，[更多信息请参见此处](http://ruby-doc.org/core-2.0.0/Comparable.html))。

## 其他的

还有其他的 Ruby 集合，一个是`Set`。这与数组相同，但只能包含唯一的值。事实上，通过包含`Enumerable`模块，任何 Ruby 类都可以表现得像一个集合，这实际上是我们刚刚看到的数组、散列和范围的大部分方法的来源。

## 环

循环是一种控制结构，它会重复一段代码，直到满足某个条件。一个代码块以命令`do`开始，以`end`结束。下面是一个使用`until`条件的循环示例:

```
bottles = 10
until bottles == 0 do
  puts "There were #{bottles} beer bottles hanging on the wall ... "
  bottles -= 1
  puts "And if one beer bottle should accidently fall ..."
  puts "There'd be #{bottles} beer bottles hanging on the wall." 
end
```

这里的条件是瓶子的数量等于零。在此之前，`do`和`end`之间的代码块将一直运行。注意，每次运行程序块时，`bottles`变量中的值都会减少，所以它最终会结束循环。确保循环结束是很重要的，否则你会陷入无限循环。

上面的代码可以改为使用`while`条件编写:

```
bottles = 10
while bottles > 0 do
  puts "There were #{bottles} beer bottles hanging on the wall ... "
  bottles -= 1
  puts "And if one beer bottle should accidently fall ..."
  puts "There'd be #{bottles} beer bottles hanging on the wall." 
end
```

`while`和`until`条件几乎做同样的事情，但是从不同的角度来看。Ruby 同时提供了这两者，这样你就可以编写更具表现力的代码，读起来更像英语。

## 迭代程序

迭代器方法允许我们遍历数组、散列或范围(或者任何包含`Enumerable`模块的类)。最常见的迭代器方法是`each`方法。这将遍历集合中的每个对象，然后运行一段代码。例如，下面这段代码打印出前五个平方数:

```
numbers = [1,2,3,4,5]
numbers.each do |number|
  puts number * number
end
```

请注意，该块接受一个参数，该参数位于|pipes|中，紧跟在`do`语句之后，在本例中是`|number|`。这表示在循环的每个阶段数组中的值。这个循环将把`number`设置为 1，然后打印 1 乘以 1 的结果。在下一次迭代中，`number`将为 2，并且重复该过程，直到数组的每个元素都被迭代完。

创建块的另一种方法是用花括号来表示开始和结束，而不是用`do`和`end`。因此，上面的代码可以更简洁地写成:

```
numbers = 1 .. 5
numbers.each { |number| puts number * number }
```

我还使用了一个范围而不是一个数组，只是为了说明`each`方法对范围和数组一样有效。

Ruby 社区的普遍共识是对单行块使用`{ .. }`块，对多行块使用`do .. end`块。

当使用`each`遍历一个散列时，可以输入两个参数来表示键和值对。下面是使用之前的 stark 散列的例子:

```
stark = {   
    name: "Eddard Stark",
    lady: "Catelyn Stark",
    sigil: "Direwolf",
    motto: "Winter is Coming",
    residence: "Winterfell",
    children: ["Rob","Sansa","Arya","Bran","Rickon"]
}
stark.each {|key,value| puts "#{key}: #{value}" }
```

这会产生以下输出:

```
name: Eddard Stark
lady: Catelyn Stark
sigil: Direwolf
motto: Winter is Coming
residence: Winterfell
children: ["Rob", "Sansa", "Arya", "Bran", "Rickon"]
```

另一个有用的迭代器方法是`map`方法(注意，`collect`方法做完全相同的事情)。这会用块的返回值替换数组中的每个值。因此，我们可以使用以下代码创建一个平方数数组:

```
numbers = [1,2,3,4,5]
=> [1, 2, 3, 4, 5]
squares = numbers.map { |number| number * number }
=> [1, 4, 9, 16, 25]
```

注意，`numbers`的值没有被`map`方法改变，它仍然是`[1,2,3,4,5]`。一个名为`squares`的新数组保存了`map`方法的结果。如果你想实际改变原始数组，那么使用`map!`方法代替:

```
numbers = [1,2,3,4,5]
numbers.map! { |number| number * number }
=> [1, 4, 9, 16, 25]
```

现在`numbers`变量中的值已经改变。

## 创建并洗牌

现在，我们将使用上面学到的一些方法来编写一个 Ruby 程序，该程序将创建并洗牌。打开您最喜欢的文本编辑器，将以下内容保存为“cards.rb”:

```
deck = []
suits = %w[ Hearts Diamonds Clubs Spades ]
values = %w[ Ace 2 3 4 5 6 7 8 9 10 Jack Queen King ]
suits.each do |suit|
  values.each do |value|
  deck << "#{value} of #{suit}"
  end
end
deck.shuffle!
p deck
```

我们在这里做的是创建一个名为`deck`的空数组来存放这副牌。然后我们创建一个所有花色的数组和一个 13 张牌值的数组(从 a 到 k)。然后我们迭代每种花色，然后对于每种花色，我们迭代每个值，创建一个字符串来命名每张牌(例如“黑桃 a”)，然后使用简写方式 `operator.`将该字符串推入牌组数组

如果您通过在终端中键入`ruby cards.rb`来运行下面的代码，您应该会看到一个类似于下面的数组，但是顺序不同(如果纸牌被正确洗牌的话)。

```
["6 of Spades", "9 of Diamonds", "6 of Hearts", "4 of Spades", "Queen of Spades", "Queen of Hearts", "Ace of Hearts", "8 of Diamonds", "King of Hearts", "2 of Clubs", "3 of Spades", "8 of Clubs", "4 of Diamonds", "4 of Clubs", "10 of Hearts", "5 of Spades", "2 of Spades", "Queen of Clubs", "5 of Diamonds", "9 of Hearts", "10 of Spades", "6 of Diamonds", "7 of Diamonds", "9 of Spades", "7 of Hearts", "2 of Hearts", "8 of Spades", "10 of Diamonds", "7 of Clubs", "8 of Hearts", "King of Clubs", "Jack of Hearts", "5 of Clubs", "3 of Clubs", "King of Spades", "Ace of Clubs", "Ace of Diamonds", "10 of Clubs", "6 of Clubs", "Jack of Spades", "3 of Hearts", "4 of Hearts", "Jack of Clubs", "3 of Diamonds", "Queen of Diamonds", "King of Diamonds", "Ace of Spades", "7 of Spades", "5 of Hearts", "Jack of Diamonds", "9 of Clubs", "2 of Diamonds"]daz@batfink:~/Dropbox/rubysource/drafts/jones$ ruby cards.rb 
["8 of Diamonds", "5 of Clubs", "6 of Clubs", "2 of Hearts", "4 of Hearts", "8 of Hearts", "8 of Spades", "Jack of Hearts", "King of Diamonds", "Queen of Spades", "Queen of Diamonds", "9 of Spades", "6 of Diamonds", "6 of Spades", "9 of Clubs", "3 of Spades", "Ace of Spades", "9 of Hearts", "10 of Diamonds", "7 of Spades", "King of Clubs", "7 of Hearts", "5 of Spades", "7 of Clubs", "Jack of Diamonds", "3 of Hearts", "4 of Diamonds", "9 of Diamonds", "Ace of Hearts", "8 of Clubs", "2 of Diamonds", "2 of Spades", "3 of Clubs", "Queen of Hearts", "Jack of Spades", "10 of Hearts", "6 of Hearts", "Jack of Clubs", "5 of Hearts", "Ace of Diamonds", "2 of Clubs", "King of Spades", "3 of Diamonds", "4 of Clubs", "5 of Diamonds", "4 of Spades", "10 of Clubs", "Ace of Clubs", "King of Hearts", "10 of Spades", "Queen of Clubs", "7 of Diamonds"]
```

这个数组现在可以用来创建各种精彩的纸牌游戏。说到这个…

## 在网上玩牌

我年轻的时候很喜欢看[正确玩牌](http://en.wikipedia.org/wiki/Play_Your_Cards_Right)游戏秀，所以我想我会用我们上面创建的纸牌代码做一个基于此的网页游戏。

创建一个名为“play *your* cards_right.rb”的文件，并添加以下代码:

```
require 'sinatra'
enable :sessions

configure do
  set :deck, []
  suits = %w[ Hearts Diamonds Clubs Spades ]
  values = %w[ Ace 2 3 4 5 6 7 8 9 10 Jack Queen King ]
  suits.each do |suit|
    values.each do |value|
      settings.deck << "#{value} of #{suit}"
    end
  end
end
```

这从要求 sinatra 宝石和启用会话开始(我们将需要它们在我们玩的时候跟踪牌)。

接下来，我们需要一个路由处理器来让我们开始做一些设置。我们将把它放在根 URL ('/')中:

```
get '/' do
  session[:deck] = settings.deck.shuffle
  session[:guesses] = -1
  redirect to('/play')
end
```

这使用了我们在`configure`块中设置的`settings.deck`数组，对其进行洗牌，并使用关键字“:deck”将其放入会话散列中。我们还设置了另一个会话变量来跟踪玩家已经猜测了多少次。完成后，我们重定向到`/play`路线，这是主游戏发生的地方。

现在让我们为此设置一个路由处理器:

```
get '/:guess' do
  card = session[:deck].pop

  value = case card[0]
    when "J" then 11
    when "Q" then 12
    when "K" then 13
    else card.to_i
  end

  if (value < session[:value] and params[:guess] == 'higher') or (value > session[:value] and params[:guess] == 'lower')
    "Game Over! The card was the #{ card }. You managed to make #{session[:guesses]} correct guess#{'es' unless session[:guesses] == 1}. <a href='/'>Play Again</a>"
  else
    session[:value] = value
    session[:guesses]  += 1
    "The card is the #{ card }. Do you think the next card wil be <a href='/higher'>Higher</a> or <a href='/lower'>Lower</a>?"
  end
end
```

首先，您可能认为这个路由处理器实际上并不对应于`/play`。但事实如此。路径的“猜测”部分是一个名为参数的*。路由中在`/`之后的字符串将存储在 params hash 中，关键字为`:guess`。它在游戏中用于检查玩家是否猜中了‘更高’或‘更低’。*任何*单词都可以输入该路线。在这种情况下，单词“play”被用作一个虚拟词，因为玩家实际上还没有做出猜测。*

我们在这个处理程序中做的第一件事是使用`pop`方法从数组中移除最后一张卡片，并将其存储在一个名为`card`的变量中。这相当于从一副牌的顶部发牌。

下一段代码计算出卡片的数值。这使用了一个`case`语句来检查卡片字符串中的第一个字母(这是使用`card[0]`获得的，像数组一样，字符串从零开始计数)。然后，它检查它是否是一个杰克，皇后或国王，并分配值分别为 11，12 和 13。对于所有其他的卡，使用`to_i`方法就足够了，因为它取一个字符串中的第一个整数值(奖励——你能想出为什么这对 ace 仍然有效吗？).

然后是一个大的`if`语句，检查玩家是否猜错了。首先，检查使用关键字`:value`保存在会话散列中的值是小于还是大于卡的值。然后，将其与存储在`params`哈希中作为`params[:guess]`的玩家猜测进行比较。`session[:card]`中持有的值是前一张卡的值。如果玩家猜错了，就会显示“游戏结束”的信息，告诉玩家这张牌是什么，以及他们猜对了多少次。

else 条件包括玩家是否猜对，或者更准确地说，如果猜对了。如果`params[:guess]`像游戏开始时一样被设置为‘play ’,这将允许代码运行。首先发生的是当前卡的值存储在`session[:value]`中，这样它可以与下一张卡进行比较，然后正确猜测的次数(存储在会话[:guests]中)增加 1。然后会显示一条信息，告诉玩家这张卡的价值，并提供链接让玩家选择下一张卡的价值是高还是低。

通过在终端中键入`ruby play_your_cards_right.rb`来启动一个正在运行的服务器，然后在浏览器中导航到 [http://localhost:4567](http://localhost:4567) ，尽情地玩吧！

## 这是所有的乡亲

这就把我们带到了 Ruby 入门系列的这一期的结尾。数组是任何编程工具包中非常强大的一部分，没有多少程序不包含某种迭代器。散列也经常出现在许多 Ruby 程序中(例如，它们经常被用来为 Rails 中的方法提供选项)。希望现在对 Sinatra 中 session 和 params 散列的工作方式有一点清楚了。

如果你想了解更多关于数组的知识，请务必看看 Robert Qualls 的这篇[精彩文章](https://www.sitepoint.com/guide-ruby-collections-part-arrays/)，里面有很多关于数组的更有用的东西。

我们现在已经讨论了字符串、数字和集合以及它们的方法。在本系列的下一部分，当我们看到如何编写自己的方法时，事情开始变得有趣起来。

## 分享这篇文章