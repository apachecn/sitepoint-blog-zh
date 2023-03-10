# 与积木亲密接触 2

> 原文：<https://www.sitepoint.com/getting-to-grips-with-blocks-pt-2/>

在上一篇文章中，我们参观了几个街区。它们是如何定义的，它们的用途是什么，以及各种 Ruby 版本之间的区别。这完全是学术性的。但实际上，我们从积木中能得到什么好处呢？是什么让它们成为 Ruby 开发人员如此强大的工具。

### Procs 和 Lambdas

在我们进入一些有用的模块示例之前，我们必须讨论一下`Proc`和`lambda`。这两个选项允许您使用`call`方法定义一个块供以后使用。

这些“可调用对象”旨在应用程序执行过程中使用。为了说明这一点，我们需要一个例子。任何与金钱有关的事情都会浮现在脑海中，汇率、不同客户的利率、折扣等等。

```
def discount( rate )
  Proc.new { |n| n - ( n * rate ) }
end

ten_percent_off = discount(0.1)
twenty_percent_off = discount(0.2)

ten_percent_off.call(100) # => 90.0
twenty_percent_off.call(60) # => 48.0
```

在上面的例子中，我们定义了一个方法`discount`，它接受一个比率并返回一个`Proc`对象。不执行任何代码(没有返回任何东西),我们所做的只是建立一个小环境，减少了一个百分比。我们设置了两个折扣，并用`call`调用它们的值 100 和 60。现在你知道了，使用`lambda`编写的`discount`方法同样有效:

```
def discount( rate )
  lambda { |n| n - ( n * rate ) }
end
```

### 为什么有两种定义过程的方法？

`Proc`和`lambda`有细微的区别。从前面的例子中，我们看到该块接受一个参数`n`,`lambda`对这些参数非常严格，如果我们像这样错误地调用它:

```
ten_percent_off.call(10, "This should not be here")
# => ArgumentError: wrong number of arguments (2 for 1)
```

`Proc`并不介意这种事情，它只是忽略了额外的参数。同样，如果我们没有传递足够的参数，`lambda`会抱怨，`Proc`也不会介意，只是将多余的参数分配给`nil`。听起来,`Proc`是一种不错且更灵活的做事方式，但是我很少用`Proc.new`,几乎总是用`lambda`,为什么？嗯，90%的情况下我喜欢强制执行 airity(参数数量),如果我做错了，就会抛出一个可怕的错误。如果这个理由似乎不充分，那么我们可以考虑`Proc`和`lambda`如何处理`return`。

```
def hello_me
  hiya = Proc.new { return "Hiya" }
  hiya.call
  puts "Woe is me, I will never fulfill my purpose"
end

hello_me
#=> Hiya

# Using a lambda
def hello_me
  hiya = lambda { return "Hiya" }
  hiya.call
  puts "Finally I fell like I have contributed"
end

hello_me
Finally I fell like I have contributed
```

正如所见，`Proc`从调用它的作用域返回，而`lambda`从块中返回，让我们最后的消息行完成它的目的。我不是说“选择一个，永远不要使用另一个”，远非如此。了解这两者的含义可以让我们在适当的时候使用正确的定义。尽管如此，我还是用了更多的`lambda`。

## 实用积木

所以现在我们想看看如何以一种实用的方式使用这些模块，而不仅仅是我们习惯的迭代。

### 简化界面

首先想到的是我在工厂方法中使用的一种技术。工厂是根据输入返回对象的一段代码。为标签印刷制作文本就是一个很好的例子。

```
class AddressLabel
  def initialize
    @lines = []
  end

  def self.generate &block
    label = AddressLabel.new
    label.instance_eval(&block)
    label.print
  end

  def print
    @lines.each do |line|
      puts line
    end
  end

  def address(lines)
    lines.each do |line|
      @lines << line
    end
  end
end

AddressLabel.generate do
  address ["Lets Be Avenue", "Glasgow"]
end

# Another way of creating the same label would be ...
label = AddressLabel.new
label.address ["Lets Be Avenue", "Glasgow"]
label.print
```

这里，`instance_eval`在`AddressLabel`的一个实例中评估块。这两种制作标签的方法都做了同样的事情，只是使用块格式更性感，读起来也更自然。毫无疑问，你会在许多可用的 gem 中看到这种类型的界面。

### 安装和拆卸

块的另一个常见用途是预处理和后处理。网络上最常见的例子可能是 Ruby 的标准库`File.open`

```
File.open('testfile', 'w') do |f|
  f.write 'Some text'
end

file = File.open('testfile2', 'w')
file.write 'More text'
file.close
```

第二个例子可能是你习惯使用的 PHP，但是我们可以看到这个漂亮的块实现负责关闭我们身后的文件。您可以在任何涉及拆除操作的地方应用这种实现。我记得写过一些创建 PDF 文件的 php 代码。代码是这样的

```
try {
  $pdf = new PDFlib();

  if ($pdf->begin_document("file_name", "") == 0) {
    die("Error: " . $pdf->get_errmsg());
  }

  $pdf->show(“hello world”);
  $pdf->end_page_ext(“”);
  $pdf->end_document(“”);
} catch (Exception $e) {
  exit();
}
```

这只是打开一个新的文件，写一些文本，然后再关闭它，有一些非常基本的异常处理来退出脚本，如果事情出错。它从来就不是最优雅的代码。然而，由于我们在 Ruby 标准库中有如此优雅的接口示例，用 Ruby 编写这样的代码会让人感觉非常陈旧“肮脏”。

它可以在 PHP 中被清除很多(用 PHP 匿名函数更是如此)，但是在 PHP5.3 之前，我们使用的工具无法胜任提取所有这些打开/关闭文档的杂务而不包装 PDFlib 库。

那么设置呢？我们刚才讨论的一切都是后处理，那么模块在设置上有什么好处呢？当创建开始使用大量条件逻辑来决定结果的方法时，我会考虑传递块。

```
def more_than_ten_rows? rows
  if rows.length > 10
    yield rows
  end
end

more_than_ten_rows? (1..10).to_a do |row|
  puts row
end
```

因此，这段简单的代码`more_than_ten_rows?`就像一个门，当我们拥有的行数超过要求时，允许块运行。备选方案是将条件逻辑移动到表示，或者将表示移动到逻辑，使用我们已经实现了一些基本分离的块。

## 包扎

我认为公平地说，除非你在学习 Ruby 时很好地掌握了 blocks，否则你永远不会真正发挥你的潜力。人们经常听到“积木无处不在”，这是真的，所以如果你不能和它们共度美好时光，你将会错过派对。

它们帮助我们开发人员以简洁明了的方式完成代码，随着时间的推移，使用它们会变得更加自然，一旦你到了那个阶段，你就会想知道没有它们你是怎么过的。

使用 PHP 等价物绝对是值得的，我从未使用过它们(即使我在生产环境中使用 5.3 版本)，直到我开始写这篇文章，语法比 Ruby 更冗长，但是，嘿，这是一个相当不错的交易，我完全相信理解 PHP 中这个相对较新的东西将帮助你掌握 Ruby 的核心基础。

## 分享这篇文章