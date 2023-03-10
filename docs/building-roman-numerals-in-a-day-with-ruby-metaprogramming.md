# 用 Ruby 元编程在一天内构建罗马数字

> 原文：<https://www.sitepoint.com/building-roman-numerals-in-a-day-with-ruby-metaprogramming/>

![](img/073142a3fcd154828f653d4f51ad6ce8.png)

我最喜欢 Ruby 的一点是，它是一种非常纯粹的面向对象语言，一切都是对象。这个事实，加上 Ruby 也是一种运行时语言的事实，可以让你对代码库中的标准库做一些非常酷的编辑。

在本文中，我将简要讨论 Ruby 元编程中被称为动态方法的部分。这些方法是为标准库的代码库中已经存在的 Ruby 类编写的。

对于本教程，我们是在你的机器上安装了 Ruby 的基础上工作的。

## 来自 IRB

了解 Ruby 中任何新概念的最快方法是在 IRB 终端中输入。在终端窗口中，键入以下内容:

```
$ irb 
```

现在你应该在 IRB 终端。接下来，我们将向名为 William 的字符串类添加一个方法。这个方法只是简单地返回我的名字，但是它应该让你了解什么是动态方法。

```
class String
  def william
    return "William"
  end
end 
```

现在，在 IRB 终端中，我们可以执行以下操作:

```
irb> "string".william
=> "William" 
```

从这个例子中可以看出，动态方法是在运行时创建的方法。您可以在代码执行时向类中添加另一个方法。当谈到解决常见的编码挑战时，动态方法打开了一个全新的范例。让我们看看如何使用动态方法来创建一个将小数字转换成罗马数字的程序。

## 使用动态方法解决罗马数字挑战

编程是艺术和科学的混合体。不幸的是，当谈到编码挑战时，有许多正确的答案引起了激烈的争论。我看到的一个常见的挑战来自于罗马数字的形式。

我们如何编写一个计算机程序，使用动态方法将一个数字转换成罗马数字？

让我们来看看一个可能的解决方案，远至 5000。让我们从终端窗口创建一个新项目

```
mkdir roman_numeral 
```

现在我们可以创建一个新文件:

```
touch roman_numerals.rb 
```

我喜欢为我的代码编写测试，所以我们将安装 RSpec 并创建一个规格表:

```
gem install rspec 
```

在我们安装了 RSpec gem 之后，我们可以初始化 RSpec

```
rspec --init 
```

在我们的规格目录中，我们可以创建罗马数字规格表。

```
touch spec/roman_numeral_spec.rb 
```

现在我们已经做好了启动项目的一切准备。让我们从编写大量的测试开始。将以下内容添加到我们的**spec/Roman _ numeric _ spec . Rb**中:

```
require_relative "../roman_numerals.rb"

RSpec.describe Numeric, "#roman_numeral" do
  context "#convert number into roman numeral" do
    it "1.roman_numeral returns I" do
      expect(1.roman_numeral).to eq "I"
    end
    it "5.roman_numeral returns I" do
      expect(5.roman_numeral).to eq "V"
    end
    it "4.roman_numeral returns IV" do
      expect(4.roman_numeral).to eq "IV" 
    end
    it "6.roman_numeral returns IV" do
      expect(6.roman_numeral).to eq "VI"
    end
    it "7.roman_numeral returns VII" do
      expect(7.roman_numeral).to eq "VII"
    end
    it "8.roman_numeral returns VII" do
      expect(8.roman_numeral).to eq "VIII"
    end
    it "9.roman_numeral returns IX" do
      expect(9.roman_numeral).to eq "IX"
    end
    it "10.roman_numeral returns IX" do
      expect(10.roman_numeral).to eq "X"
    end
    it "13.roman_numeral returns XIII" do
      expect(13.roman_numeral).to eq "XIII"
    end
    it "15.roman_numeral returns XV" do
      expect(15.roman_numeral).to eq "XV"
    end
    it "18.roman_numeral returns XVIII" do
      expect(18.roman_numeral).to eq "XVIII"
    end
    it "19.roman_numeral returns XIX" do
      expect(19.roman_numeral).to eq "XIX"
    end
    it "30.roman_numeral returns XXX" do
      expect(30.roman_numeral).to eq "XXX"
    end
    it "50.roman_numeral returns L" do
      expect(50.roman_numeral).to eq "L"
    end
    it "51.roman_numeral returns LI" do
      expect(51.roman_numeral).to eq "LI"
    end
    it "89.roman_numeral returns LXXXIX" do
      expect(89.roman_numeral).to eq "LXXXIX"
    end
    it "99.roman_numeral returns XCIX" do
      expect(99.roman_numeral).to eq "XCIX"
    end
    it "145.roman_numeral returns CXLV" do
      expect(145.roman_numeral).to eq "CXLV"
    end
    it "459.roman_numeral returns CDLIX" do
      expect(459.roman_numeral).to eq "CDLIX"
    end
    it "1984.roman_numeral returns MCMLXXXIV" do
      expect(1984.roman_numeral).to eq "MCMLXXXIV"
    end
    it "1545.roman_numeral returns MDXLV" do
      expect(1545.roman_numeral).to eq "MDXLV"
    end
    it "4936.roman_numeral returns MMMMCMXXXVI" do
      expect(4936.roman_numeral).to eq "MMMMCMXXXVI"
    end
  end
end 
```

现在我们已经编写了一些测试，让我们开始在`Fixnum`类上使用动态方法，将我们的数字转换成罗马数字。从我们的测试中可以看到，我们有以下例子:

```
it "1984.roman_numeral returns MCMLXXXIV" do
  expect(1984.roman_numeral).to eq "MCMLXXXIV"
end 
```

动态方法允许我们创建一个可以在`Fixnum`类的实例上调用的方法。Ruby 有一些有用的方法，比如将数字转换成字符串的`to_s`。在这种情况下，我们从 Ruby 约定中获得灵感，并在不改变任何 Ruby 库的情况下添加一个方法。我们只是添加了一个新方法。让我们看看这是如何做到的。

我们做的第一件事是定义 Fixnum 类。即使已经有了一个名为 Fixnum 的 Ruby 类，执行以下操作也不会覆盖实际的类。在我们的**Roman _ numbers . Rb**文件中:

```
class Fixnum
end 
```

现在我们可以通过添加一个额外的方法来修改`Fixnum`类。为了让我们的测试通过，我们需要一个可以对整数调用的`to_roman_numeral`方法。在我们的**Roman _ numbers . Rb**文件中写入以下内容:

```
class Fixnum
  def roman_numeral
    return "The Romans have no zeros just heros. https://www.theguardian.com/notesandqueries/query/0,5753,-1358,00.html " if self == 0
    symbols = {1000 => "M",900 => "CM", 500 => "D",400 => "CD", 100 => "C",90 => "XC", 50 => "L",40 =>"XL", 10 => "X",9 => "IX", 5 => "V",4 => "IV",  1=> "I"}
     multiplier = self
     symbol = []
     count = 0
     symbols.each do |num, sym|
       symbol.push(sym * (multiplier/num))
       multiplier = multiplier % num
       count += 1
     end
    return symbol.join
 end
end 
```

当您运行 RSpec 时，您应该看到所有的测试都通过了。从当前目录中，运行以下命令:

```
rspec 
```

您现在已经看到了动态方法的作用。我们现在可以在任何号码上拨打`.to_roman_numeral`，就像我们打`.to_s`一样。动态方法对于像这样的小程序来说是很棒的，但是要注意，如果你不小心的话，像这样把动态方法添加到你的应用程序中会有很大的负面影响。

## 警告:覆盖现有方法

动态方法最大的潜在缺点是用您自己的方法覆盖生态系统中现有的方法。这可能导致宝石破碎，无声的错误，甚至过于复杂的代码。

我们再上一次`Fixnum`课。如果我们不小心编写了自己的`.to_s`方法，那么我们就有可能从应用程序中移除核心的、预期的功能。因为代码将在运行时执行，所以我们不会看到错误发生，直到它被调用，即使这样，它可能会导致一个无声的错误。

当涉及到动态方法时，做加法是值得的。比起覆盖方法，更安全的方法是添加并快速检查您没有覆盖现有的方法。与此同时，我强烈推荐测试覆盖您的应用程序，这样您就可以快速发现什么时候出了问题。

## 结论

现在您已经快速了解了 Ruby 元编程的一个方面，那么您将如何使用它呢？你认为动态方法有用吗？还是对一个人来说太危险了？我很想在下面的评论中听到你的意见。

## 分享这篇文章