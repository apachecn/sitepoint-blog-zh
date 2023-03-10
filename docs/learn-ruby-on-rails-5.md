# 学习 Ruby on Rails:终极初学者教程

> 原文：<https://www.sitepoint.com/learn-ruby-on-rails-5/>

## 运行 Ruby 文件

对于我们到目前为止已经试验过的简单的 Ruby 基础，交互式 Ruby shell ( `irb`)是我们选择的工具。我相信您会同意，在类似 shell 的环境中进行实验，我们可以看到立竿见影的效果，这是学习语言的一个很好的方法。

然而，我们接下来将讨论控制结构，对于如此复杂的任务，您将希望在文本编辑器中工作。这种环境将允许您多次运行一段代码，而无需重新键入。

一般来说，Ruby 脚本是包含 Ruby 代码的简单文本文件，扩展名为`.rb`。这些文件被传递给 Ruby 解释器，由它执行您的代码，如下所示:

```
$ ruby myscript.rb
```

为了使用下面的例子，我建议您在您最喜欢的文本编辑器中打开一个新的文本文件(这可能是我在第 2 章《入门》中推荐的一个编辑器),然后边走边键入代码——这确实是最好的学习方法。然而，我承认有些人对把所有的东西都打出来不感兴趣，他们只想开门见山。这些不耐烦的读者可以下载本书的代码档案，其中包含所有这些例子。您可以直接在 Ruby 解释器中执行这段代码。

如上所述，要从命令行运行文件，只需键入`ruby`，后跟文件名。

## 控制结构

Ruby 有一套丰富的特性来控制应用程序的流程。条件句是关键字，用于根据对一个或多个条件的评估来决定是否执行某些语句；循环是多次执行语句的构造；块是封装功能的一种方式(例如，在循环中执行)。

为了演示这些控制结构，让我们利用一些我们之前定义的 Car 类。键入以下类定义并保存文件(或从代码存档中加载)；在本节中，我们将在此基础上探索一些控制结构。

```
Example 3.1\. 01-car-classes.rb

class Car
@@wheels = 4              # class variable
@@number_of_cars = 0      # class variable
def initialize
@@number_of_cars = @@number_of_cars + 1
end
def self.count
@@number_of_cars
end
def mileage=(x)           # mileage writer
@mileage = x
end
def mileage               # mileage reader
@mileage
end
end

class StretchLimo < Car
@@wheels = 6              # class variable
@@televisions = 1         # class variable
def turn_on_television
# Invoke code for switching on on-board TV here
end
end

class PontiacFirebird < Car
end

class VolksWagen < Car
end
```

## 条件式

Ruby 中有两个基本的条件构造:`if`和`unless`。这些结构中的每一个都可以用来根据给定的条件执行一组语句。

**`if`构造**

一个`if`构造包装了只有在满足特定条件时才被执行的语句。关键字`end`定义了`if`结构的结束。条件和`end`关键字之间包含的语句仅在满足条件时执行。

```
Example 3.2\. 02-if-construct.rb (excerpt)

if Car.count.zero?
puts "No cars have been produced yet."
end
```

可以通过添加一个`else`块来提供第二个条件:当条件满足时，执行第一个块；否则，执行`else`块。这种控制流对您来说可能很熟悉。这就是它的作用:

```
Example 3.3\. 03-if-else-construct.rb (excerpt)

if Car.count.zero?
puts "No cars have been produced yet."
else
puts "New cars can still be produced."
end
```

最复杂的例子涉及另一种情况。如果不满足第一个条件，则评估第二个条件。如果两个条件都不满足，则执行`else`块:

```
Example 3.4\. 04-if-elsif-else.rb (excerpt)

if Car.count.zero?
puts "No cars have been produced yet."
elsif Car.count >= 10
puts "Production capacity has been reached."
else
puts "New cars can still be produced."
end
```

如果`count`方法返回`5`，上面的代码将产生以下输出:

```
New cars can still be produced.
```

传统的`if`条件的另一种选择是`if`语句修饰符。语句修饰符就是这样做的——它修改它所属的语句。`if`语句修饰符的工作方式与常规的`if`条件完全一样，但是它位于受影响的行的末尾，而不是在代码块之前:

```
Example 3.5\. 05-if-statement-modifier.rb (excerpt)

puts "No cars have been produced yet." if Car.count.zero?
```

当要有条件执行的代码只包含一行时，通常使用 if 条件的这个版本。有能力创造这样的条件会产生比其他结构更严格的编程语言更像英语的代码。

**`unless`构造**

`unless`条件是`if`条件的负版本。这对于当不满足某个条件时想要执行一组语句的情况很有用。

让我们创建几个实例(漫画迷会注意到，我把蝙蝠战车设计成了庞蒂亚克火鸟——事实上，多年来，斗篷十字军对交通工具的选择一直在变化，吸收了许多汽车行业不太常见的创新，包括从 1966 年林肯 Futura 到两栖坦克的一切。但是在这个例子中，我们将坚持使用 Pontiac。):

```
Example 3.6\. 06-unless-construct.rb (excerpt)

kitt = PontiacFirebird.new
kitt.mileage = 5667

herbie = VolksWagen.new
herbie.mileage = 33014

batmobile = PontiacFirebird.new
batmobile.mileage = 4623

larry = StretchLimo.new
larry.mileage = 20140
```

现在，如果我们想知道 KITT 可以带多少霹雳游侠粉丝去兜风，我们可以检查这个`kitt`对象是哪个类。与`if`表达式一样，`end`关键字定义了语句的结尾。

```
Example 3.7\. 06-unless-construct.rb (excerpt)

unless kitt.is_a?(StretchLimo)
puts "This car is only licensed to seat two people."
end
```

像`if`条件一样，`unless`条件可能有一个可选的`else`语句块，当条件满足时执行:

```
Example 3.8\. 07-unless-else.rb (excerpt)

unless kitt.is_a?(StretchLimo)
puts "This car only has room for two people."
else
puts "This car is licensed to carry up to 10 passengers."
end
```

因为 KITT 肯定不是加长豪华轿车，所以该代码将返回:

```
This car only has room for two people.
```

与`if`条件不同，`unless`条件不支持第二个条件。然而，像`if`条件一样，`unless`条件也可以作为语句修饰符。下面的代码显示了一个这样的例子。在这里，如果 KITT 的里程数少于 25000 英里，该信息将不会显示:

```
Example 3.9\. 08-unless-statement-modifier.rb (excerpt)

puts "Service due!" unless kitt.mileage < 25000
```

## 环

Ruby 提供了用于循环代码的`while`和`for`构造(即执行一组语句指定的次数，或者直到满足某个条件)。此外，许多实例方法可用于循环一个`Array`或`Hash`的元素；我们将在下一节讨论这些。

**和`until`循环**

只要满足指定的条件，一个`while`循环就会重复执行它所包含的语句。

```
Example 3.10\. 09-while-loop.rb (excerpt)

while Car.count < 10
Car.new
puts "A new car instance was created."
end
```

这个简单的`while`循环重复执行`Car.new`语句，只要汽车总数在十辆以下。当数字达到 10 时，它退出循环。

像`if`和`unless`之间的关系一样，`while`循环也有一个补充:`until`结构。如果我们使用`until`，循环中的代码将被执行，直到满足条件。我们可以像这样使用`until`重写上面的循环:

```
Example 3.11\. 10-until-loop.rb (excerpt)

until Car.count == 10
Car.new
puts "A new car instance was created."
end
```

## =和==的区别

在条件中使用赋值运算符(单等号)和等式运算符(双等号)时，注意它们之间的区别是很重要的。

如果要比较两个值，请使用等式运算符:

```
if Car.count == 10
...
end
```

如果要给变量赋值，请使用赋值运算符:

```
my_new_car = Car.new
```

如果您混淆了这两者，您可能会修改您只希望检查的值，这可能会带来灾难性的后果！

**`for`循环**

`for`循环允许我们迭代一个集合的元素——比如一个`Array`——并对每个元素执行一次一组语句。这里有一个例子:

```
Example 3.12\. 11-for-loop.rb (excerpt)

for car in [ kitt, herbie, batmobile, larry ]
puts car.mileage
end
```

上面的代码会产生以下输出:

```
5667
33014
4623
20140
```

这个简单的`for`循环遍历`Car`对象的`Array`并输出每辆车的里程。对于每次迭代，`car`变量被设置为`Array`的当前元素。第一次迭代将`car`设置为等同于`lincoln_towncar`，第二次迭代将其设置为`chrysler_voyager`，依此类推。

实际上，这里介绍的传统的`while`和`for`循环很少使用。相反，大多数人倾向于使用由`Array`和`Hash`类提供的实例方法，我们将在接下来讨论。

## 阻碍

块可能是 Ruby 最吸引人的特性。然而，对于 Ruby 新手来说，它们也是需要一段时间才能“点击”的东西之一。在我们深入研究如何创建块之前，让我们先来看看 Ruby 使用块的一些核心特性。

我们在上一节中看到了一些循环结构，这是一种探索可用工具的有用方式。然而，在使用其他 Ruby 脚本的工作中，您可能永远也不会遇到许多这样的结构，因为使用一个块来执行相同的任务几乎总是容易得多。一个块，结合由`Array`和`Hash`类提供的 each 方法，是遍历数据的一种非常强大的方法。

让我用一个例子来说明这一点。考虑一下我们刚才使用的 for 循环。我们可以重写代码来使用`each`方法，它是`Array`类的一个实例方法，如下所示:

```
Example 3.13\. 12-simple-block.rb (excerpt)

[ kitt, herbie, batmobile, larry ].each do |car_name|
puts car_name.mileage
end
```

让我们来分析一下:这个块包含了关键字`do`和`end`之间的代码。一个块能够接收参数，这些参数放置在块开始的竖线(`|`)之间。多个参数用逗号分隔。因此，这段代码执行与我们之前看到的`for`循环相同的操作，但是方式更加简洁。

我们再举一个例子。为了遍历一个`Hash`的元素，我们使用了`each`方法，并将两个参数传递给该块——`key`(`car_name`)和`value`(`color`)——如下所示:

```
Example 3.14\. 13-block-with-params.rb (excerpt)

car_colors = {
'kitt' => 'black',
'herbie' => 'white',
'batmobile' => 'black',
'larry' => 'green'
}
car_colors.each do |car_name, color|
puts "#{car_name} is #{color}"
end
```

该代码产生以下输出:

```
kitt is black
herbie is white
batmobile is black
larry is green
```

`Integer`类也有许多使用块的方法。例如，`Integer`对象的`times`方法恰好执行一个块 n 次，其中 n 是对象的值。

```
Example 3.15\. 14-block-integer.rb (excerpt)

10.times { Car.new }
puts "#{Car.count} cars have been produced."
```

上面的代码产生以下输出:

```
10 cars have been produced.
```

这里要注意的最后一点是花括号的交替块语法。对于非常短的代码块，花括号是首选语法，而不是我们在前面的例子中使用的关键字`do...end`,就像在前面的例子中一样。

这里是`Integer`类的另一个方法——根据时代精神，`upto`方法从对象的值开始计数，直到传递给该方法的参数。

```
Example 3.16\. 15-block-upto.rb

5.upto(7) { |i| puts i }
```

此代码产生如下所示的输出:

```
5
6
7
```

按照 Ruby 的说法，对象`i`是块的一个参数。块的参数包含在竖线中，通常只能在块中使用。如果我们有不止一个参数，我们用逗号分隔它们，就像这样:`|parameter1, parameter2|`。在上面的例子中，一旦代码块执行完毕，我们将不再能够访问`i`。

在本书中，我们将结合 Rails 核心类探索块的更多用途。

## 摘要

哇，这一章我们讲了很多！首先，我们浏览了一堆面向对象编程理论——可能相当于计算机科学入门课程！这为我们探索 Ruby 编程语言的基础打下了良好的基础，而交互式 Ruby Shell ( `irb`)是进行这种探索的一种有趣方式。

我们还研究了 Ruby shell 中的许多 Ruby 核心类，比如`String`、`Array`和`Hash`。然后，我们从 shell 转移到创建和保存适当的 Ruby 文件，并使用这些文件，我们试验了控制结构，如条件、循环和块。

在下一章中，我们将研究构成 Rails 框架的主要基石——集成测试设施——以及开发、测试和生产环境所扮演的角色。

**Go to page:** [1](https://sitepoint.com/learn-ruby-on-rails-1/) | [2](https://sitepoint.com/learn-ruby-on-rails-2/) | [3](https://sitepoint.com/learn-ruby-on-rails-3/) | [4](https://sitepoint.com/learn-ruby-on-rails-4/) | [5](https://sitepoint.com/learn-ruby-on-rails-5/) | [6](https://sitepoint.com/learn-ruby-on-rails-6/) | [7](https://sitepoint.com/learn-ruby-on-rails-7/) | [8](https://sitepoint.com/learn-ruby-on-rails-8/) | [9](https://sitepoint.com/learn-ruby-on-rails-9/) | [10](https://sitepoint.com/learn-ruby-on-rails-10/)

## 分享这篇文章