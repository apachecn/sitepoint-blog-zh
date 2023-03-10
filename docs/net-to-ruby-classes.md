# 。NET 到 Ruby:类

> 原文：<https://www.sitepoint.com/net-to-ruby-classes/>

在本系列[的上一篇文章中。NET 到 Ruby:Ruby 环境](https://www.sitepoint.com/net-to-ruby-the-ruby-environment/)，我们介绍了构成 Ruby 的基本工具。如果你还记得我们谈论过在命令行上运行 Ruby，使用 IRB，我们写了我们的第一个 Hello World 程序，并且弄清楚在幕后发生了什么。

在这篇文章中，我们将比较一个基本的面向对象特性:类。是的，就这样。这篇文章以类、方法和变量开始，但是它变得太大了——在讨论类的时候有很多内容要讲！所以让我们开始吧。

## 班级

Ruby 中的类就像。NET，它们的定义保持不变。两者都有

> 露比和。NET 类用于将相关的数据和方法封装(或分组)到一个简洁的对象中

在这两种情况下，我们都可以实例化一个类并获得它的一个实例。露比和。NET 使用 new 关键字创建对象实例，但语法略有不同:

```
// In C#
var baz = new FooBar();
```

```
# In Ruby
baz = FooBar.new
```

要真正定义一个类，在 C#中我们应该这样做:

```
public class FooBar {
    // Class implementation
}
```

在 Ruby 中，一个类是这样的:

```
class FooBar
  # Class implementation
end
```

非常简单直接，要注意的区别是 Ruby 的类定义缺少作用域。而类作用域是 Ruby 与。因为 Ruby 不允许私有的、受保护的或内部的类。Ruby 中的所有类都是公共的。

## 为什么 Ruby 的类都是公共的？

Ruby 有公共类，因为基本上所有 Ruby 的类都是**开放类**。这意味着我可以打开 String 类(或任何其他类)并向其添加功能。这里有一个例子:

```
class String
  def remove_vowels
    self.gsub(/[aeiou]/, '')
  end
end

puts "this is a sentence".remove_vowels #=> ths s  sntnc
```

现在，在任何人指责 Ruby 允许这样的暴行之前，让我们指出这一点。NET 允许以**扩展方法**的形式实现相同的功能。让我们把同样的代码写进去。网络:

```
static class StringExtensions
{
    public static string ReplaceVowels(this string value)
    {
        return new Regex("[aeiou]").Replace(value, string.Empty);
    }
}
```

Ruby 唯一公开课程的地方。NET 的扩展功能不同之处在于，使用 Ruby 可以打开一个类并定义类方法，而使用。NET 不能定义类方法扩展。

因为这一系列关于从。NET 到 Ruby 并不是要引发一场激烈的战争，我将让您来决定开放类和扩展是否值得它们引入的类封装冲突(如果您特别勇敢，请在评论中发表您的意见。)

如果你想看看应用良好的开放类的例子，可以去 GitHub，看看 [Rails core_ext](https://github.com/rails/rails/tree/master/activesupport/lib/active_support/core_ext) 。

## 遗产

Ruby 中的继承就像. NET 一样，用它我们可以定义一个超类，然后从超类继承来创建子类。如我们所料，子类继承了超类的所有数据和方法。在 C#中，我们会像这样使用继承:

```
public class Mammal {
    // Methods & data
}	

public class Human : Mammal {
}
```

在 Ruby 中，继承是这样的:

```
class Mammal
end

class Dog < Mammal
end
```

对于红宝石，小于符号 **<** 用于表示继承。

## 接口

先说接口。接口是另一个 Ruby 没有的特性。这是因为 Ruby 是鸭类型的。鸭子分型的基本定义是:

> 如果它走路像鸭子，游泳像鸭子，说话像鸭子，那么它一定是鸭子。

如果你不知道什么是鸭子打字，那只会让事情变得更糟。其核心是 duck typing 允许您调用任何对象实例上的方法，而不管它是什么类型，如果该实例对该方法做出响应，该实例就会执行该方法。让我们看一个例子:

```
class Duck
  def talk
    puts "quack"
  end
end

class Dog
  def talk
    puts "woof"
  end
end

duck = Duck.new 
duck.talk         #=> quack

dog = Dog.new
dog.talk          #=> woof
```

在这个例子中，我们使用了一个 Duck 实例和一个 Dog 实例，并在实例上调用了`talk`方法。因为两个实例都将响应`talk`,所以这个程序将在不抛出错误的情况下执行。但是如果一个实例没有被调用的方法呢？

```
class Truck
end

truck = Truck.new
truck.talk        
   #=> NoMethodError: undefined method `talk' for #<Truck:0x1011d1620>
```

从示例中可以看出，当在`truck`上调用`talk`时，会引发一个`NoMethodError`。这是鸭子类型:**不管底层类型是什么，如果一个实例能够响应一个方法，它就会响应，如果不能，它就会出错**。那么为什么 duck typing 允许 Ruby 放弃接口呢？因为 Ruby 不在乎类型。

英寸 NET 接口的使用使得我们可以将不同类型的对象实例传递给一个方法，编译器将检查该实例类型是否符合该接口。这允许我们定义对一个实例的期望，而不用说应该如何满足这些期望。但是因为 Ruby 是鸭类型的，所以每个实例都可以被传递给那个方法——你冒着得到一个`NoMethodError`的风险，但是这是使用动态语言时风险/回报权衡的一部分。

总而言之，因为 Ruby 是一种不处理类型安全检查的动态语言，并且因为它是鸭类型的，所以 Ruby 没有定义一种正式的、基于代码的方法来声明接口。

现在不要误解我，接口是一个重要的概念，它们的发明是有原因的。Ruby 采用了一种不同的方法，接口是以文档的形式非正式定义的。Rack 是代码的一个例子，[正式定义了一个接口](http://rack.rubyforge.org/doc/SPEC.html)(他们称之为协议)，任何使用 Rack 的代码都必须遵守这个接口。

你看 Ruby，Ruby 程序员喜欢[“不要重复自己”](http://en.wikipedia.org/wiki/Don%27t_repeat_yourself)原则。那么，为什么您要用代码编写一个接口，然后将它写入您的文档中呢？那是加倍的努力。相反，为你的代码编写真正好的文档，解释你的接口和它们的 rational，并让你的代码的消费者来实现接口。

综上所述，如果你真的想这么做，你可以在代码中定义一个 Ruby 接口。这不是大多数 Ruby 爱好者会做的事情，因为这违背了 Ruby 的惯例，但是你可以这样做。

关于 Duck typing 要说明的最后一点是，它有很多好处。我们将在稍后讨论编写单元测试模拟和进行元编程时看到其中的两个好处。

## 抽象类

转到抽象类，让我们概述一个基本的定义:

> 标记为抽象的类表示超类不能被实例化，而必须作为子类被实例化

这个定义的含义是，在超类中声明的功能被传递给子类。这个定义很重要，因为 Ruby 没有像。NET 有。

相反，Ruby 实现了一个叫做 **Mixins** 的东西。Mixins 很像抽象类，因为它们可以用来定义可以添加到类中的方法和数据。它们不能被直接实例化，而是包含在现有的类中。这里有一个简单的例子:

```
module Cheerful
    def greeting
      puts "It's nice to meet you!"
    end
  end

  class Man
    include Cheerful
  end

  bill = Man.new
  bill.greeting     #=> It's nice to meet you!
```

对于这段代码，我们声明一个名为`Cheerful`的`module`。模块也用于命名空间，我将在另一篇文章中详述。然后我们使用方法`include`将`Cheerful`模块与`Man`类混合。通过将`Cheerful`混合到`Man`中，我们能够调用任何方法，或者访问`Cheerful`中定义的任何数据。

这是一个非常基本的例子，但是 Mixins 的强大之处在于我们可以随意混合任意数量的模块。实际上，mixins 允许你编写一次通用代码——比如用于枚举集合的[代码——并让它应用于大量的类(如果你阅读了 Enumerable 的文档，那里也有一个接口定义！)](http://www.ruby-doc.org/core/classes/Enumerable.html)

因此，虽然 Ruby 不使用抽象类，但它提供了 Mixins，可以用来实现与抽象类相同的目的。

至此，这个关于类的帖子就完成了。这篇文章涉及了很多领域。我们谈到了类实现、开放类、继承、接口、Duck 类型、抽象类和混合。在下一篇关于从。NET 到 Ruby，我们将研究方法和变量在 Ruby 中是如何工作的。

一如既往地欢迎评论和讨论！

## 分享这篇文章