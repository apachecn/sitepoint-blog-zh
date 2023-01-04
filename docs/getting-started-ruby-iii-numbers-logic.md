# Ruby 入门，III:数字和逻辑

> 原文：<https://www.sitepoint.com/getting-started-ruby-iii-numbers-logic/>

本系列的最后一部分研究了字符串及其方法，以及将信息存储为变量。我们还编写了一个欢迎程序和 Madlibs 程序，使用 Sinatra 制作了它们的 web 版本。在这篇文章中，重点是如何在 Ruby 中使用数字并创建逻辑语句。

请记住，如果您想继续做这些示例，请从您的命令提示符启动`irb`。

## 该算账了！

```
4 + 5
=> 9
```

4 和 5 都是对象，而+实际上是方法。我们在上一篇文章中看到了如何使用点语法调用一个方法，你也可以用`+`方法做同样的事情:

```
4.+(5)
```

这是一个向方法添加参数的例子。`4`是调用`+`方法的对象，`5`是参数。Ruby 让我们用一种更自然的方式来写这个(比如写出算术)。这被称为*句法糖*，所以`4 + 5`被视为等同于`4.+(5)`。

我们可以用类似的方式做很多不同的算术:

```
10 - 4
  => 6
4 * 8
  => 32
9 / 2
  => 4
```

…坚持住！最后那个总数是不正确的。这是因为 9 和 2 都是整数。这意味着 9/2 的解只会给你答案的整数部分。为了得到正确的答案，我们需要使用一种不同类型的数字，称为浮点数(因为他们使用浮点小数)。要使用浮点数而不是整数，只需将 9 写成 9.0 即可(这就是众所周知的浮点数)

```
> 9.0/2
=> 4.5
```

那更好！

Ruby 为我们提供了确定一个数字是奇数还是偶数的方法:

```
> 4.odd?
=> false
> 5.odd?
=> true
```

在上一篇文章中，我们学习了“爆炸方法”。`odd?`和`even?`方法是*布尔方法*的例子。这些方法只返回`true`或`false`的结果。他们通常有一个问号('？')字符放在方法的末尾。

Ruby 还可以做一些很酷的数学工作，比如使用`gcd`方法找到两个数字的最大公约数。一个数字调用方法(下例中的 12)，另一个数字是方法的参数(下例中的 20):

```
> 12.gcd(20)
=> 20
```

还有一个`lcm`方法，可以找到两个数的最小公倍数:

```
> 15.lcm(20)
=> 60
```

我最喜欢的方法之一是`times`方法，如下例所示:

```
> 5.times do
>  puts "Hello!"
> end
Hello!
Hello!
Hello!
Hello!
Hello!
```

我喜欢它封装 Ruby 编程语言可读性的方式。它真的像罐头上说的那样，读起来几乎像一个英语句子。顺便说一下，`do`和`end`语句之间的那段代码被称为*块*，我们将在以后的文章中更多地介绍它们。

我们要看的最后一个`Integer`方法是`to_s`方法，它代表‘to string’。它接受一个整数，并将其转换为该整数的字符串表示形式，例如:

```
> 7.to_s
=> "7"
```

这允许您将数字视为字符串。还有一个针对字符串的等效方法叫做`to_i`，它将一个字符串转换成一个整数:

```
"42".to_i
=> 42
```

你必须小心这一点，因为字符串必须在开头有一些数字，否则它将返回`0`(你可以在这里看到更多的)。

## 生成随机数

编程时生成一个随机数通常很有用。在 Ruby 中使用`rand`方法很容易做到这一点。这将生成一个介于 0 和 1.0 之间的浮点数，如下所示:

```
> rand
=> 0.29635454177765397
```

如果您提供一个整数参数，那么它将生成一个介于 0 到之间的随机整数，但不包括该参数。例如:

```
> rand(6)
=> 2
```

上面的代码将生成一个从 0 到 5 的随机数。我们可以用这个(以及我们之前学过的一些算法)来模拟骰子的滚动:

```
> rand(5) + 1
=> 4
```

查看 Robert Qualls 关于 randoms 和 Ruby 的这篇非常深入的文章,了解更多信息。

## 逻辑

我们可以使用`if`和`then`语句在 Ruby 中创建逻辑语句。例如，我们可以玩一个在骰子上掷出 6 的游戏:

```
number = rand(5) + 1
if  number == 6 then puts "Congratulations, you rolled a six!" end
```

大多数问题都很容易解决，但是也有一些问题。首先注意一下`=`和`==`的区别。在第一种情况下，我们使用`=`进行*赋值*，即设置一个变量，在本例中为“数字”，等于一个值，一个从 1 到 6 的随机数。在第二种情况下，我们使用`==`来测试等式。在这个例子中，我们测试 number 的值是否等于 6。注意不要混淆这些，因为这样写:

```
if  number = 6 then puts "Congratulations, you rolled a six!" end
```

实际上将 number 的值设置为 6，因此将始终为真！

还有一种`else`说法:

```
number = rand(5) + 1
if number.odd?
  puts "You rolled an odd number"
else
  puts "You rolled an even number"
end
```

请注意，您实际上不必使用`then`语句。我们还可以使用`elsif`语句嵌套许多语句(不是拼写！):

```
number = rand(5) + 1
if number == 1
  puts "You rolled a 1"
elsif number == 2
  puts "You rolled a 2"
elsif number == 3
  puts "You rolled a 3"
elsif number == 4
  puts "You rolled a 4"
elsif number == 5
  puts "You rolled a 5"
else
  puts "You rolled a 6"
end
```

## 猜猜我的数字程序

现在我们已经了解了一些数字是如何工作的，我们将编写一个简短的程序，邀请用户从 1 到 100 中随机选择一个整数。将以下代码保存在一个名为“guess *my* number.rb”的文件中:

```
number = rand(100) + 1
puts "I'm thinking of a number between 1 and 100, try and guess what it is"
guess = gets.to_i
attempts = 1
until guess == number do
  if guess < number then puts "Too small, try again"
  elsif guess > number then puts "Too big, try again"
  end
  guess = gets.to_i
  attempts += 1
end
puts "Well done, you guessed my number in #{attempts} attempt#{'s' if attempts > 1}!"
```

首先我们设置一个名为`number`的变量，它是一个介于 1 和 100 之间的随机数，要被猜测。我们使用参数为 100 的`rand`方法，然后加 1 来设置它。

在这之后，`puts`方法被用来打印一个解释玩家必须做什么的字符串。
然后`gets`方法从玩家那里得到一个猜测，这个猜测存储在一个名为`guess`的变量中。用户的任何输入都被存储为一个字符串(即使你输入了一个数字)，所以我们需要使用`to_i`方法来确保该值被视为一个整数。
接下来，使用变量`attempts`将尝试次数设置为 1。这将用于跟踪用户在猜对之前尝试了多少次。
然后，就有了声明`until guess == number do`。这是一段很好的 Ruby 语句:在猜测值等于数字之前，执行以下操作。这是一个循环的例子，我们将在本系列的下一部分更详细地研究它。

在`do`语句之后，我们有另一个代码块的例子。直到用户猜出正确的数字。

我们使用`if`和`elsif`语句来检查猜测值是低于还是高于数字，然后给出一些适当的反馈。之后，再猜一次，尝试次数加 1。这是通过使用语句`attempts += 1`完成的，它是写作`attempts = attempts + 1`的简写。

一旦数字猜对了，程序就跳出循环并显示一条消息。我们在上一篇文章中学到的插值技巧用于将`attempts`变量插入字符串。请注意，如果猜测的次数多于一次，我们有一个简洁的方法来使用`if`语句使猜测次数多元化。

## 在网上猜我的号码

像往常一样，在这个系列中，我们将通过使用 [Sinatra](http://www.sinatrarb.com/) 把我们的节目放到网上来结束。对于这个程序，我们需要在每次请求后记下这个数字。web 使用的 [HTTP 协议](http://en.wikipedia.org/wiki/Hypertext_Transfer_Protocol)是无状态的，这意味着信息不会存储在请求之间。

解决这个问题的一个方法是使用会话。这在 Sinatra 很容易做到——你只需要启用它们。创建一个名为“web *guess* the_number.rb”的文件，并添加以下几行:

```
require 'sinatra'

enable :sessions
```

这需要 sinatra gem，也支持会话。会话使用*cookie*来存储信息。请注意，这些是不安全的，可能会被截获和解密。对于这个游戏来说，这不是太大的问题，但是如果你打算存储敏感信息，请记住这一点。

接下来我们要设置游戏，当用户请求网页时，我们将首先为根 URL(位于 [http://localhost:4567](http://localhost:4567) )创建一个路由处理程序。

```
get '/' do
  @message = "I'm thinking of a number between 1 and 100, try and guess what it is"
  session[:number] = rand(100) + 1
  session[:attempts] = 0
  erb :guess
end
```

首先，设置一个名为`@message`的实例变量。实例变量总是以一个`@`符号开始，它们在与路由处理器相关的视图中可用。在路由处理程序的末尾，我们使用 ERB 调用视图`guess`。在视图中，我们将能够引用`@message`变量。此外，我们在这个路由处理程序中设置了 number 和 attempts 变量。

如前所述，我们为此使用会话，否则信息将在每次请求后丢失。这是通过使用散列来存储信息来完成的，因此`session[:number]`保存数字的值，而`session[:attempts]`保存尝试次数的值。

我们需要一些使用 ERB 编写的 HTML 代码，下面的代码在文件的末尾:

```
__END__

@@guess
<!doctype html>
<html>
  <header>
    <title>Guess the Number</title>
  </header>
  <body>

    <p><%= @message %></p>
    <form method="POST" action="/">
      <input name="number">
      <input type="submit" value="Guess">
    </form>
  </body>
</html>
```

这是一个包含提交猜测表单的基本网页。该表单将使用 HTTP POST 方法发布，我们还需要提供它将被发布的路径。在这种情况下，当我们使用表单所在的同一个 URL(在这种情况下是站点的根目录“/”)时，我们正在进行所谓的回发。注意,`name`属性是`number`,因为我们需要它在表单提交时获取它的值。

当这种情况发生时，我们需要创建一个路由处理程序。因为表单是 post 的，所以我们将为路由处理程序使用 post 方法，路由是在表单的 action 属性中指定的。将以下代码放在另一个路由处理程序之后(但在 ERB 代码之前):

```
post '/' do
  number = session[:number]
  guess = params[:number].to_i
  session[:attempts] += 1
  redirect to('/success') if guess == number
  if guess < number then @message = "Too small, try again"
  elsif guess > number then @message = "Too big, try again"
  end
  erb :guess
end
```

这里我们检索保存在会话散列中的数字，然后从表单中获取猜测值。表单提交的所有值都是字符串，所以我们需要使用`to_i`方法将其转换为整数。我们还将猜测次数(也存储在会话哈希中)增加 1。

接下来，如果猜测值等于数字，我们使用一些`if`逻辑重定向到成功页面。注意，这与上面给出的例子写得正好相反(首先是`if`语句)，这样做的效果是使它更具可读性。

如果用户没有被重定向，那么他们的猜测一定是错误的，所以我们使用一些`if`和`elsif`逻辑来测试猜测是太大还是太小，并用适当的消息更新`@message`实例变量。我们通过在 ERB 再次显示`guess`视图来完成，这样用户可以提供另一个猜测。

包含`@message`变量的段落现在将根据猜测显示不同的消息，即使我们使用相同的视图。

剩下要做的就是创建一个成功的路由处理程序。这发生在最后一个路由处理程序之后:

```
get '/success' do
  attempts = session[:attempts]
  "Well done, you guessed my number in #{attempts} attempt#{'s' if attempts > 1}!"
end
```

这只是从会话散列中检索猜测的次数，然后显示一条祝贺消息，通知用户尝试了多少次。

在终端中输入`ruby web_guess_the_number`启动服务器，然后在浏览器中访问 [http://localhost:4567](http://localhost:4567) ，试试玩这个游戏。

## 这是所有的乡亲

在这篇文章中，我们介绍了整数和浮点数，并研究了它们的一些方法。我们还看了如何使用`if`、`then`、`else`和`elsif`语句构建逻辑语句。我们把所有这些放在一起，创建了一个“猜数字”程序，我们把它放在网上，使用会话来存储信息。在本系列的下一部分，我们将看看容器，如数组和散列，以及如何执行循环。与此同时，请仔细阅读我们到目前为止所介绍的内容，并确保在下面留下任何问题或评论。

## 分享这篇文章