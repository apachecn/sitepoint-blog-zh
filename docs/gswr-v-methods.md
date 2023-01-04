# GSwR V:疯狂的方法

> 原文：<https://www.sitepoint.com/gswr-v-methods/>

在过去的三篇文章中，我们已经介绍了 Ruby 最重要的一些对象类型——字符串、整数和浮点数，以及数组、范围和散列等集合。我们还研究了赋予这些对象功能的方法。在这篇文章中，我们将看看如何创建我们自己的方法。

为了定义一个方法，我们使用`def`关键字，后跟方法名。在用关键字`end`结束之前，接下来是该方法的代码。让我们看看 IRB 中的一些例子:

```
def say_hello
  "Hello Ruby!"
end
```

这个方法被称为`say_hello`，返回字符串“Hello Ruby！”。Ruby 中一个方法的最后一行是它的*返回值*，它是这个方法被调用时返回的值。要调用一个方法，只需输入它的名称:

```
say_hello
=> Hello Ruby!
```

方法是有用的。它们可以使你的代码更容易阅读(只要你给你的方法起一个描述性的名字),并且意味着你不必一遍又一遍地写重复的代码块。此外，如果您希望更改某些功能，那么您只需要在一个地方更新该方法。这就是所谓的 *DRY(不要重复自己)*原则，在编程时记住这一点很重要。

## 添加参数

通过包含*参数*，方法可以变得更加有效。这些是提供给方法使用的值。例如，我们可以通过添加“名称”参数来改进`say_hello`方法:

```
def say_hello(name)
  "Hello #{name}!"
end
```

参数被添加到方法定义的方法名之后(括号是可选的，但建议使用)。在函数体中，参数的名称相当于一个变量，当调用该方法时，该变量等于作为*参数*传递给该方法的值:

```
say_hello "Sinatra" # Again, parentheses are optional
=> "Hello Sinatra!"
```

在这种情况下，字符串“Sinatra”作为参数提供，我们使用字符串插值将参数插入到该方法返回的字符串中。

我们可以通过使参数等于方法定义中的默认值来为参数提供默认值:

```
def say_hello(name="Ruby")
  "Hello #{name}"
end
```

这意味着我们现在可以调用带有或不带参数的`say_hello`方法:

```
say_hello
=> "Hello Ruby!"

say_hello "DAZ"
=> "Hello DAZ!"
```

我们可以添加更多的参数，一些有默认参数，一些没有(但是没有的必须放在前面)。下面是另一个名为`greet`的函数，它使用多个参数:

```
def greet(name,job,greeting="Hello")
  "#{greeting} #{name}, Your job as a #{job} sounds fun"
end

greet("Walt","Cook")
=> "Hello Walt, Your job as a Cook sounds fun"

greet("Jessie","Cook","Hey")
=> "Hey Jessie, Your job as a Cook sounds fun"
```

我们还可以通过在方法定义中的最后一个参数前放置一个“*”来创建带有不确定数量的参数的方法。然后，任意数量的参数将被存储为一个与参数同名的数组。下面的方法将任意数量的名称作为参数，并将它们存储在一个名为`names`的数组中:

```
def say_hello(*names)
  names.each { |name| puts "Hello #{name}!" }
end

say_hello("Walt","Skylar","Jessie")
Hello Walt!
Hello Skylar!
Hello Jessie!
 => ["Walt", "Sklar", "Jessie"]

say_hello "Sherlock", "John"
Hello Sherlock!
Hello John!
 => ["Sherlock", "John"]

say_hello "Heisenberg"
Hello Heisenberg!
 => ["Heisenberg"]
```

注意，该方法的返回值是`names`数组。

另一种选择是使用关键字参数(尽管这些参数只在 Ruby 版以后才可用)。这些函数通过对参数使用类似散列的语法来起作用，如我们的`greet`方法的更新版本所示:

```
def greet(name="Ruby", job: "programming language", greeting: "Hello")
  "#{greeting} #{name}, Your job as a #{job} sounds fun"
end
```

然后可以使用关键字以任何顺序输入参数:

```
greet greeting: "Hi"
=> "Hi Ruby, Your job as a programming language sounds fun"

greet "Sherlock", job: "detective", greeting: "Greetings"
=> "Greetings Sherlock, Your job as a detective sounds fun"

greet "John", greeting: "Good Day", job: "doctor"
=> "Good Day John, Your job as a doctor sounds fun"
```

请注意，关键字参数的顺序并不重要，如果它们中的一些被省略，那么将使用默认值。

我们还可以在末尾添加一个额外的参数，前面有`**`。这将在与参数同名的散列中收集方法定义中未指定的任何额外关键字参数:

```
def greet(name="Ruby", job: "programming language", greeting: "Hello", **options)
  "#{greeting} #{name}, Your job as a #{job} sounds fun. Here is some extra information about you: #{options}"
end

greet "Saul", job: "Colonel", human: false
=> "Hello Saul, Your job as a Colonel sounds fun. Here is some extra information about you: {:human=>false}"

greet "Kara", job: "Viper Pilot", callsign: "Starbuck", human: true
=> "Hello Kara, Your job as a Viper Pilot sounds fun. Here is some extra information about you: {:callsign=>\"Starbuck\", :human=>true}"
```

也可以将一个块作为参数添加到一个方法中，方法是在它的名字前加上一个`&`。然后可以在方法定义中通过引用该块来访问它。如果您希望在调用方法时运行一些特定的代码，这将非常有用。下面是一个名为`repeat`的方法的基本示例，它接受一个代码块以及一个告诉它运行该代码多少次的参数:

```
def repeat(number=2, &block)
  number.times { block.call }
end

repeat(3) { puts "Ruby!" }
Ruby!
Ruby!
Ruby!
 => 3
```

这个块是可选的，有一个方便的方法叫做`block_given?`，它允许我们在调用这个方法时检查这个块是否是给定的。这里有一个名为`roll_dice`的方法，它将骰子的边数作为一个参数，同时也是一个块，可以用来处理骰子上滚动的值:

```
def roll_dice(sides=6,&block)
  if block_given?
    block.call(rand(1..sides))
  else
    rand(1..sides)
  end
end
```

如果在没有块的情况下调用该方法，则它会简单地返回骰子上掷出的数字:

```
roll_dice
=> 6
```

如果我们想模仿一个 20 面骰子，那么我们可以输入一个`sides`参数:

```
roll_dice(20)
=> 13
```

现在说我们要掷骰子，但是然后把结果翻倍然后再加 5。我们可以使用一个块来做这件事:

```
roll_dice { |result| 2 * result + 5 }
```

使用块的另一个例子可能是如果我们想要返回掷骰子的结果是奇数还是偶数:

```
roll_dice do |result|
  if result.odd?
    "odd"
  else
    "even"
  end
end
```

使用块作为参数可以使方法变得非常灵活和强大。Sinatra 中的路由处理器将块作为参数。一个`GET`请求的方法定义如下所示:

```
def get(route,options={},&block)
  ... code goes here
end
```

route 参数是一个字符串，它告诉我们要匹配的路由。这后面是一个选项散列，默认情况下设置为空散列。最后，该方法采用一个块，这是我们希望在访问路由时运行的代码。

## 重构打好你的牌

我们将尝试*重构*我们在上一个教程中创建的“正确玩牌”Sinatra 应用程序的代码。重构代码是在不改变其行为的情况下改进其结构和可维护性的过程。

我们要做的是用方法替换一些代码块。这将使代码更容易理解和维护——如果我们想改变功能，我们只需要在一个地方改变方法。

Sinatra 使用*助手*方法来描述在路由处理器和视图中使用的方法。它们被放在一个`helpers`块中，该块可以放在代码中的任何地方，但通常放在开头附近，如下所示:

```
helpers do

  # helper methods go here

end
```

首先，我们将使用方法的名称来描述我们想要的行为，从而重写应用程序。创建一个名为“play _ your _ cards _ right _ refactored . Rb”的新文件，并添加以下代码:

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

helpers do
 # helper methods will go here
end

get '/' do
  set_up_game
  redirect to('/play')
end

get '/:guess' do
  card = draw_card
  value = value_of card

  if player_has_a_losing value
    game_over card
  else
    update_session_with value
    ask_about card
  end
end
```

这与最后一段代码非常相似(并且具有完全相同的功能)，只是更容易看到每个路由处理程序中发生了什么。这是因为我们用方法替换了大量的 Ruby 代码，并选择了一些描述性的方法名，使得代码更具可读性。它几乎看起来像是[伪代码](http://en.wikipedia.org/wiki/Pseudocode)。

让我们依次看看每个路由处理程序，看看它是做什么的:

```
get '/' do
  set_up_game
  redirect to('/play')
end
```

这个路由处理器设置游戏，然后重定向到“/play”路由…事实上，这甚至不需要解释，因为它就在代码中！方法名告诉我们到底发生了什么——所有的代码都被提取到一个需要创建的名为`set_up_game`的方法中。将以下内容放入 helpers 块中:

```
def set_up_game
  session[:deck] = settings.deck.shuffle
  session[:guesses] = -1
  session[:value] = 0
end
```

这将设置我们在游戏中需要的会话变量。洗牌，猜测被设置为-1(因为它在第一次玩牌时增加 1，即使没有做出正确的猜测)，值变量被设置为 0。

`redirect`和`to`都是内置在 Sinatra 中的方法，它们被特意命名，以便路由处理程序中的代码读起来几乎像英语。

现在让我们来看看处理游戏的路由处理器的开始:

```
get '/:guess' do
  card = draw_card
  value = value_of card

    # more code follows
end
```

首先我们需要画一张卡片，所以我们写一个方法来画。在`helpers`块中添加以下内容:

```
def draw_card
  session[:deck].pop
end
```

这是一个非常短的方法，但是它给了我们更多的描述性代码。

接下来我们要找出卡的价值。我们在[第 4 部分](https://www.sitepoint.com/gswr-iv-going-loopy-arrays-hashes/)中使用的代码是一个很长的`case`语句来检查图片卡。我们可以将其提取到一个方法中，该方法将一张卡片作为参数，然后返回该卡片的值。下面的代码也放在`helpers`块中:

```
def value_of card
  case card[0]
    when "J" then 11
    when "Q" then 12
    when "K" then 13
    else card.to_i
  end
end
```

接下来，我们有一个`if`语句来检查玩家的猜测是否正确:

```
if player_has_a_losing value
  game_over card
else
  update_session_with value
  ask_about card
end
```

这也使用了方法的名称，使代码更具描述性。第一个方法叫做`player_has_a_losing`，它有一个名为`value`的参数。名称和参数名的这种组合使得它读起来很好。这个方法也需要放在`helpers`块中:

```
def player_has_a_losing value
  (value < session[:value] and params[:guess] == 'higher') or (value > session[:value] and params[:guess] == 'lower')
end
```

这将返回`true`或`false`，这取决于作为参数输入的值与玩家的猜测值相比是高于还是低于存储在`session[:value]`中的值(前一张牌的值)，玩家的猜测值存储在关键字为`:guess`的`params`散列中。

如果`player_has_a_losing`方法返回`false`，那么我们继续执行另外两个函数。第一个是`update_session_with`，它有一个`value`的参数。这完全按照它所说的那样做，并且将猜测的次数更新 1。在`helpers`街区也是如此:

```
def update_session_with value
  session[:value] = value
  session[:guesses]  += 1
end
```

需要放入`helpers`块的下一个方法是`ask_about`。这只是问玩家牌是更高还是更低。它将当前卡片作为参数:

```
def ask_about card
  "The card is the #{ card }. Do you think the next card will be <a href='/higher'>Higher</a> or <a href='/lower'>Lower</a>?"
end
```

这是我们所有辅助方法中的最后一个。如果您尝试通过在终端中键入`ruby play_your_cards_right_refactored.rb`来运行代码，然后访问 [http://localhost:4567](http://localhost:4567) ，您应该会看到和以前一样的游戏。这正是我们在重构代码时希望发生的事情——外部没有变化，但内部代码更具可读性和可维护性。

## 范围

在我们刚刚使用的一些助手方法中，我们必须提供卡片或值作为方法的参数。你可能想知道，当`card`和`value`都已经作为变量存在时，我们为什么还要这样做。这是因为如果变量是在方法中创建的，或者是作为参数输入的，那么它只存在于方法中。这可以在下面的例子中看到(在 IRB 中检查):

```
name = "Walt"
job = "teacher"

def say_my_name
  name = "Heisenberg"
  job = "cook"
  puts "You're #{name} and you're a #{job}"
end

puts "You're #{name} and you're a #{job}"
=> You're Walt and you're a teacher

say_my_name
=> You're Heisenberg and you're a Cook
```

当`puts`在方法之外被调用时`name`是‘沃特’，`job`是‘老师’，但是当你一进入方法之内`name`就变成了‘海森堡’，`job`是‘库克’。

一个方法不能访问在它之外创建的任何变量(它们需要作为方法的参数输入)——你也不能从方法之外访问在方法内部创建的任何变量——任何你想在方法被调用后访问的值都应该由方法返回。代码中可以访问变量的地方被称为变量的*范围*。

## 这是所有的乡亲

希望本教程有助于介绍方法，并展示了它们在使您的代码更加灵活、可维护、可重用和易读方面是多么有用(只要它们的名称恰当)。

我们在本教程中编写的方法实际上更像是*函数*。正如我在第一篇文章中提到的，Ruby 实际上是一种面向对象的语言，方法应该是对象的方法。我们一直在编写的方法实际上都是特殊的`main`对象的方法(参见[Pat 的这篇文章](https://www.sitepoint.com/rubys-top-self-object/)以获得更多关于这方面的深入信息)。

在下一篇文章中，我们将变得更优雅，看看 Ruby 的职业系统是如何工作的。我们将讨论如何向现有的类(如字符串和整数)添加方法，以及如何用自己的公共和私有方法创建自己的类。同时，请在下面的评论区留下您的任何评论或问题。

## 分享这篇文章