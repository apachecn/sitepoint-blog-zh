# 掌握积木

> 原文：<https://www.sitepoint.com/getting-to-grips-with-blocks/>

当从 PHP 背景切换到 Ruby 时，对我来说最灰暗的领域之一就是块。在 Ruby 中，块无处不在，如果没有很好地理解它们，将会阻碍你越过“新手”阶段的学习。当你第一次开始使用 Ruby 时，你不可避免地会使用专门用于迭代的块。

### 你好布洛克斯

```
3.times {|n| puts "Printed #{n} times" }
```

上面的代码肯定是块的“hello world ”,从这一行代码中我们可以了解到很多关于 Ruby 的内容。我们有一个数字(`Fixnum`)和一个方法(`times`)，它接受一个块来做一些有用的事情。如果您错过了重要的一点，“一个方法正在接受一个块”。目前，只是盲目接受块只是一段代码，我们可以传递，只能在方法调用后定义，它可以接受参数，有两种语法风格。我们有如上定义在花括号中或定义在`do`和`end`中的块。如果块的内容是单行的，使用花括号是标准的做法(尽管 Avdi Grimm】根据块正在做的事情为混合这些内容做了很好的论证)。不管怎样，上面的代码写成这样也是有效的:

```
3.times do |n|
  puts "Printed #{n} times"
end
```

在 PHP 中，我们根本不习惯使用块。事实上，对于如此琐碎的事情，使用我们的 trusty for(each)循环要好得多。但是有太多的时候我遇到了下面的情况。

```
for($i = 0; $i < count($values); $i++)
```

现在，这一行代码中存在许多问题(它现在正在您身边的生产中使用)。对于初学者来说,`count()`在 For 循环中几乎是不可饶恕的大罪，它在每次循环运行时都计算 count。但这还不是我最大的不满。变量`$i`的使用，我们知道它的意思是索引，但是我们没有改变作用域`$i`对于我们所在的函数是有效的局部变量。为什么要使用这些晦涩的名字呢？我觉得是 C 自己的错，`$i`几乎出现在所有循环结构的教科书示例中。

看看过去的作用域、语义，或者甚至把它实现为 foreach 循环，我们仍然有一个外来的循环结构干扰我们的数据。我以前每天都写这种东西。但是最近花了一些时间在 Ruby 上之后，使用循环结构比如`for`就感觉不太对了。通过将业务放在一个块中，它更干净，我们可以免费获得一个闭包(块的局部变量只在块的范围内)。

### PHP 闭包

在 PHP 5.3 中引入了闭包。这使得我们期待的 Ruby 的`times`操作成为可能。

```
class MyInt {
  protected $value = 0;

  public function __construct($value) {
    $this->value = $value;
  }

  public function times($pBlock) {
    for($i = 0; $i < $this->value; $i++) {
      $pBlock($i);
    }
  }
}

$echo_statement = function($value) {
  echo $value;
};

$val = new MyInt(3);
$val->times($echo_statement);
```

所以我们基本上创建了一个新的类，它将充当 Ruby `Fixnum`，创建了一个 times 函数(带有强制的可怕的`$i`变量),它并不真正关心它做什么，它只知道做一定次数。这是非常强大的东西，但是看看我们为了得到像 Ruby 块一样简洁的东西而不得不跳过的代码圈。

### 泄露方法的秘密

如果我们在 Ruby 中创建自己的`times`实现，它看起来很可能是这样的:

```
class Fixnum
  def times
    for i in (1..self)
      yield i
    end
  end
end

5.times { |n| puts "Im Hi #{n}" }
```

上面我们重新打开了`Fixnum`类，并决定我们不喜欢`times`方法是如何实现的。所以我们写了一个 for 循环，在 1 到任意值的范围内迭代。上面最有趣的一行是`yield i`。在`yield`方法中，正常的程序执行暂停，传递给该方法的块临时接管控制权。

等一下。我们刚刚在哪里经过了那个街区？好吧，每个方法都接受一个块(不管你自己有没有定义)。所以我们甚至不需要声明这个块作为我们的`times`方法的参数。它知道那里可能有一个街区。但是我们选择使用`yield`让它运行。

如果我们忘记通过一个街区会发生什么？答案是`LocalJumpError : no block given`。然而，我们可以使用`block_given?`赋予这个时间方法更多的灵活性。

```
class Fixnum
  def times
    if block_given?
      for i in (1..self)
        yield i
      end
    else
      Enumerable::Enumerator.new self
    end
  end
end

5.times { |n| puts "Im Hi #{n}" }
Im Hi 0
Im Hi 1
Im Hi 2
Im Hi 3
Im Hi 4
=> 1..5

5.times
=> #<Enumerable::Enumerator:0xb737acb4>
```

如果没有给定块，我们通过返回一个新的`Enumerable::Enumerator`对象添加了一点曲线球。这主要是为了模拟`times`的真实实现。它将返回一个枚举器对象，让我们做类似这样的事情:

```
enumerator = 5.times
=> #<Enumerable::Enumerator:0xb737acb4>
enumerator.max
=> 4
enumerator.min
=> 0
enumerator.minmax
=> [0, 4]
```

### 块范围

块是闭包，它们占用大量代码和它的本地环境(变量等)。)并存储它以供以后执行。所以我们最好谈谈积木对周围环境有什么影响。与我们自然期望的一致，在一个块中定义的变量在该块被执行后被丢弃。但是根据我们在定义块时使用的 Ruby 版本，有一些未扩展的行为:

```
n = 10
y = 0
[1,2,3].each do |n|
  x = n
  y = y + n
end

y.inspect
# => "6"
n.inspect
# => "3" -- In 1.9.X this will be 10
defined? x
# => nil
```

正如所料，变量`x`已经超出了块的范围。但是我们可以看到`n`，它是在块调用被块操作修改之前定义的。我不知道你怎么想，但我觉得这种行为是不自然的。我听说过这实际上是一种“特征”的情况，但是我还没有遇到过这种情况。最后，在块前定义并在块内修改的`y`维护块外的修改。

我提到这种行为是因为块参数( n 被修改)对于那些玩 Ruby 1.8.x 的人来说。在 Ruby 1.9.x 中，块没有改变参数 n 。我更喜欢这种行为，并建议您也使用最新最好的 Ruby 版本。

### 包扎

*   块是一段代码，被放起来以后执行(继续，就说“闭包”)
*   默认情况下，Ruby 中的所有方法都接受一个块，我们可以选择使用`yield`调用这个块
*   积木在它们自己封闭的环境中运行
*   块的局部变量在执行后被丢弃
*   在块定义之前定义的变量被包装在块范围内

可以肯定地说，我们已经在块学习的道路上取得了良好的进展，下一次我们将看看`lamda`、`Proc`的神奇之处，当然，在看块的一些实际用途之前，我们会解释它们的区别。

## 分享这篇文章