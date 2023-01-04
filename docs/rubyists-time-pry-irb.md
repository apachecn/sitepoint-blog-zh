# Rubyists，是时候摆脱 IRB 了！

> 原文：<https://www.sitepoint.com/rubyists-time-pry-irb/>

![tOTeiZo](img/25b980c58e55f2ba20ec065a498fba5e.png)

每个 Rubyist 都知道`irb`。交互式 Ruby Shell 本质上是一个 REPL(读取-评估-打印循环)。输入一些表达式，结果会立即返回。那么 Pry 是如何融入的呢？Pry bills 本身是标准 IRB shell 的强大替代方案。但远不止如此。

在本文中，我将引导您了解 Pry 的一些最佳特性。其中许多功能将从根本上改变您的工作流程。你对 Pry 了解得越多，你就越想知道它一直在哪里。

让我们开始窥探吧！

## 装置

窥探很简单:

```
% gem install pry pry-doc                                                                       
Fetching: pry-0.9.12.2.gem (100%)
Successfully installed pry-0.9.12.2
Parsing documentation for pry-0.9.12.2
Installing ri documentation for pry-0.9.12.2
Fetching: pry-doc-0.4.6.gem (100%)
Successfully installed pry-doc-0.4.6
Parsing documentation for pry-doc-0.4.6
Installing ri documentation for pry-doc-0.4.6
2 gems installed
```

在这里，我们安装*`pry`和`pry-doc`。 [`pry-doc`](https://github.com/pry/pry-doc) 提供了 MRI 核心文档和源代码。我们将在后面的例子中用到它。*

 *现在，为了确保一切正常:

```
% pry -v                                                                                                 
Pry version 0.9.12.2 on Ruby 2.0.0
```

在撰写本文时，我使用的是最新版本的 Pry。Pry 在 Ruby 1.9 和 Ruby 2.0 上都可以正常工作。请注意我用的是 Ruby MRI。

## 第一部分:让我们分解一些代码

就我个人而言，我发现学习 Pry 的最好方法是通过一些例子来工作。在这一节中，我们来看看 Pry 提供给我们的显示文档和源代码的工具。在这个过程中，我们还将看到 Pry 如何使用 shell 导航命令来导航对象状态。

让我们开始我们的牧师会议:

```
% pry                                                                                                     
[1] pry(main)>
```

### 用`show-doc`显示文件

假设我需要回忆一下`Array#map`和`Array#map!`的区别。`show-doc`命令让这变得很容易:

```
[2] pry(main)> show-doc Array#map

From: array.c (C Method):
Owner: Array
Visibility: public
Signature: map()
Number of lines: 11

Invokes the given block once for each element of self.

Creates a new array containing the values returned by the block.

See also Enumerable#collect.

If no block is given, an Enumerator is returned instead.

   a = [ "a", "b", "c", "d" ]
   a.map { |x| x + "!" }   #=> ["a!", "b!", "c!", "d!"]
   a                       #=> ["a", "b", "c", "d"]

[3] pry(main)> show-doc Array#map!

From: array.c (C Method):
Owner: Array
Visibility: public
Signature: map!()
Number of lines: 10

Invokes the given block once for each element of self, replacing the
element with the value returned by the block.

See also Enumerable#collect.

If no block is given, an Enumerator is returned instead.

        a = [ "a", "b", "c", "d" ]
        a.map! {|x| x + "!" }
        a #=>  [ "a!", "b!", "c!", "d!" ]
```

Pry 为`show-doc`–`?`提供了一个非常方便的快捷方式:

```
[3] pry(main)> ? Array#map!
```

### 用`cd`和`ls`导航状态

这无疑是 Pry 最酷的特性之一。假设我有一个数组:

```
[4] pry(main)> arr = [1, 2, 3]
=> [1, 2, 3]
```

我们可以`cd`变成一个物体，就像这样:

```
[5] pry(main)> cd arr
[6] pry(#<Array>):1>
```

注意当我们进入`arr`时，提示变成了`pry(#<array>):1></array>`。这告诉我们，*当前的*对象是一个`Array` *实例*，用`#`符号表示。"

现在，试试看。

```
[6] pry(#<Array>):1> ls
Enumerable#methods:
  all?            each_entry        find_all  max      minmax_by     sort_by
  any?            each_slice        flat_map  max_by   none?
  chunk           each_with_index   grep      member?  one?
  collect_concat  each_with_object  group_by  min      partition
  detect          entries           inject    min_by   reduce
  each_cons       find              lazy      minmax   slice_before
Array#methods:
  &            count       include?            reject                slice
  *            cycle       index               reject!               slice!
  +            delete      insert              repeated_combination  sort
  -            delete_at   inspect             repeated_permutation  sort!
  <<           delete_if   join                replace               sort_by!
  <=>          drop        keep_if             reverse               take
  ==           drop_while  last                reverse!              take_while
  []           each        length              reverse_each          to_a
  []=          each_index  map                 rindex                to_ary
  assoc        empty?      map!                rotate                to_s
  at           eql?        pack                rotate!               transpose
  bsearch      fetch       permutation         sample                uniq
  clear        fill        place               select                uniq!
  collect      find_index  pop                 select!               unshift
  collect!     first       pretty_print        shelljoin             values_at
  combination  flatten     pretty_print_cycle  shift                 zip
  compact      flatten!    product             shuffle               |
  compact!     frozen?     push                shuffle!
  concat       hash        rassoc              size
self.methods: __pry__
locals: _  __  _dir_  _ex_  _file_  _in_  _out_  _pry_
```

`ls`还做什么？让我们来问问 Pry:

```
[14] (pry) main: 0> ls -h
Usage: ls [-m|-M|-p|-pM] [-q|-v] [-c|-i] [Object]
       ls [-g] [-l]

ls shows you which methods, constants and variables are accessible to Pry. By default it shows you the local variables defined in the current shell, and any public methods or instance variables defined on the current object.

The colours used are configurable using Pry.config.ls.*_color, and the separator is Pry.config.ls.separator.

Pry.config.ls.ceiling is used to hide methods defined higher up in the inheritance chain, this is by default set to [Object, Module, Class] so that methods defined on all Objects are omitted. The -v flag can be used to ignore this setting and show all methods, while the -q can be used to set the ceiling much lower and show only methods defined on the object or its direct class.

options:

    -m, --methods               Show public methods defined on the Object (default)
    -M, --instance-methods      Show methods defined in a Module or Class
    -p, --ppp                   Show public, protected (in yellow) and private (in green) methods
    -q, --quiet                 Show only methods defined on object.singleton_class and object.class
    -v, --verbose               Show methods and constants on all super-classes (ignores Pry.config.ls.ceiling)
    -g, --globals               Show global variables, including those builtin to Ruby (in cyan)
    -l, --locals                Show locals, including those provided by Pry (in red)
    -c, --constants             Show constants, highlighting classes (in blue), and exceptions (in purple)
    -i, --ivars                 Show instance variables (in blue) and class variables (in bright blue)
    -G, --grep                  Filter output by regular expression
    -h, --help                  Show this message.
```

让我们回到我们的`arr`对象。一旦我们在中*了一个对象，我们就可以直接调用它的方法，就像这样:*

```
[7] pry(#<Array>):1> min
=> 1
[8] pry(#<Array>):1> max
=> 3
[9] pry(#<Array>):1> reverse
=> [3, 2, 1]
```

### 使用`show-method`查看信号源

想知道`Array#map!`是如何实现的吗？回想一下，既然我们已经在一个对象中，我们可以简单地用`show-source map!`代替`show-source Array#map!`。

```
[10] pry(#<Array>):1> show-source map!

From: array.c (C Method):
Owner: Array
Visibility: public
Number of lines: 12

static VALUE
rb_ary_collect_bang(VALUE ary)
{
    long i;

    RETURN_SIZED_ENUMERATOR(ary, 0, 0, rb_ary_length);
    rb_ary_modify(ary);
    for (i = 0; i < RARRAY_LEN(ary); i++) {
                        rb_ary_store(ary, i, rb_yield(RARRAY_PTR(ary)[i]));
    }
    return ary;
}
```

MRI 是用 C 实现的，这就解释了这个稍微有点神秘的清单。要获得 Ruby 代码清单，你必须切换到 Rubinius，因为它的大部分核心库都是用 Ruby 构建的。用 Pry 研究 Rubinius 代码库是学习用 Ruby 而不是 c 语言实现 Ruby 细节的极好方法。

还有其他一些方法可以达到同样的效果。但在此之前，让我们先上一层楼:

```
[11] pry(#<Array>):1> cd ..
[12] pry(main)>
```

以下都是等效的:

```
[13] pry(main)> show-source Array#map!
[14] pry(main)> show-source arr.map!
```

就像`show-doc`和`?`一样，`show-source`的等价物是`$`:

```
[15] pry(main)> $ Array#map!
[16] pry(main)> $ arr.map!
```

## 第二部分:深入探究 Pry 的调试工具

到目前为止，我们已经看到 Pry 为我们提供了一个非常好的帮助系统，以及导航和查看对象状态的方法。但是撬能做更多，更多。在这一节中，我们来看看 Pry 的调试工具。

### 先决条件

我们需要告诉 Pry 我们的默认编辑器应该是什么。在您的主目录中创建一个名为`.pryrc`的文件。因为我爱 Vim:

```
Pry.config.editor = 'vim'
```

如果您想继续学习，现在是获取示例文件的好时机:

```
class Order
  def initialize
    @line_items = []
  end

  def add_line_item(line_item)
    @line_items << line_item
  end

  def total
    subtotals = @line_items.each { |li| li.quantity * li.price }
    subtotals.reduce(:+)
  end
end

class LineItem
  attr_reader :quantity, :price

  def initialize(quantity, price)
    @price    = price
    @quantity = quantity
  end
end

order = Order.new
order.add_line_item LineItem.new(2, 3.00)
order.add_line_item LineItem.new(4, 1.00)
puts order.total
```

这里有一个非常简单的`Order`和`LineItem`类。一个`Order`可以加很多个`LineItem`，每个都有一个`quantity`和`price`。一个`Order`对象也可以计算出`total`。

运行这个程序，看它崩溃:

```
% ruby order.rb
order.rb:12:in `each': undefined method `+' for #<LineItem:0x007fb4e2013350 @price=3.0, @quantity=2> 
(NoMethodError)
        from order.rb:12:in `reduce'
        from order.rb:12:in `total'
        from order.rb:28:in `<main>'
```

虽然这个错误可能很容易解决，但请耐心等待——因为有趣的部分来了。

### 用`binding.pry`设置断点

因为我们知道我们的程序在第 14 行崩溃了，让我们在前面一行*放一个断点。修改`totals`方法如下:*

```
def total
  subtotals = @line_items.each { |li| li.quantity * li.price }
  binding.pry # <-- Add this
  subtotals.reduce(:+)
end
```

这一次，我们以稍微不同的方式运行我们的程序。注意`-r pry`标志:

```
% ruby -r pry  order.rb

From: /Users/rambo/Desktop/store/order.rb @ line 14 Order#total:

    12: def total
    13:   subtotals = @line_items.each { |li| li.quantity * li.price }
 => 14:   binding.pry
    15:     subtotals.reduce(:+)
    16: end

[1] pry(#<Order>)>
```

刚刚发生了什么？首先，你的程序*没有*崩溃。相反，启动了一个窥探会话，程序的执行在我们放置`binding.pry`——我们的断点——的地方停止。在那之后，我们看到`pry(#<order>)></order>`，这意味着我们在一个`Order`实例中。这是因为`binding.pry`导致窥探会话在我们的`Order`实例的范围内开始。

让我们回忆一下`ls`是做什么的:

```
[1] pry(#<Order>)> ls
Order#methods: add_line_item  line_items  total
instance variables: @line_items
locals: _  __  _dir_  _ex_  _file_  _in_  _out_  _pry_  subtotals
```

我们的`line_items`是否被正确填充？

```
[2] pry(#<Order>)> line_items
=> [#<LineItem:0x007f80d25b62b0 @price=3.0, @quantity=2>,
 #<LineItem:0x007f80d25b6288 @price=1.0, @quantity=4>]
```

到目前为止，一切顺利。仔细看看`total`方法。由于`binding.pry`发生在之后*`subtotals`变量，我们当然可以得到:*

```
[3] pry(#<Order>)> subtotals
=> [#<LineItem:0x007f80d25b62b0 @price=3.0, @quantity=2>,
 #<LineItem:0x007f80d25b6288 @price=1.0, @quantity=4>]
```

啊哈！`subtotals`的结果和`line_items`一模一样。我们应该用`map`而不是`each`！还记得我们之前是如何配置编辑器的吗？现在我们要好好利用这一点。

### 制作`edit` s

Pry 允许您在不离开会话的情况下编辑方法。我们现在知道必须用一个`map`替换`each`。让我们开始吧:

```
[4] pry(#<Order>)> edit total
```

您会注意到`vim`(或者您在`.pryrc`中配置的任何编辑器)将会启动，光标位于`total`的第一行。进行必要的更改:

```
def total
  subtotals = @line_items.map { |li| li.quantity * li.price }
  binding.pry
  subtotals.reduce(:+)
end
```

退出编辑器，您将被带回到窥探会话。现在，让我们看看`subtotals`包含什么:

```
[1] pry(#<Order>)> subtotals
=> [6.0, 4.0]
```

不错！当我们从编辑器中退出时，Pry 自动重新加载文件，并再次在`binding.pry`处停止。

### 从 Pry 运行 Shell 命令

在移除`binding.pry`之前，我们可以检查 `binding.pry`之后的线路*是否工作。因为我知道行号，所以我将继续运行该行:*

```
[2] pry(#<Order>)> play -l 15
10.0
```

成功！现在我们可以继续删除`binding.pry`。但在此之前，让我们看看我们做了哪些改变:

```
[3] pry(#<Order>)> .git diff
```

Pry 能够运行任意外壳命令。你所要做的就是给命令加上前缀`.`(点)，就像我们对`git diff`所做的那样:

```
diff --git a/order.rb b/order.rb
index c05aa7d..823ccac 100644
--- a/order.rb
+++ b/order.rb
@@ -10,7 +10,9 @@ class Order
   end

   def total
-    subtotals = @line_items.each { |li| li.quantity * li.price }
+    subtotals = @line_items.map { |li| li.quantity * li.price }
     subtotals.reduce(:+)
   end
 end

@@ -26,4 +28,4 @@ end
 order = Order.new
 order.add_line_item LineItem.new(2, 3.00)
 order.add_line_item LineItem.new(4, 1.00)
 puts order.total
```

### 用`wtf?`查看堆栈跟踪

让我们故意制造一些麻烦。通过添加另一个`LineItem`来修改代码。为了节省我们一点时间，让我们也把`binding.pry` *放在*之前。

```
order = Order.new
order.add_line_item LineItem.new(2, 3.00)
order.add_line_item LineItem.new(4, 1.00)
binding.pry
order.add_line_item LineItem.new(1/0, 100)
puts order.total
```

然后我们运行程序:

```
% ruby -r pry order.rb                                                 

From: /store/order.rb @ line 30 :

    25: end
    26:
    27: order = Order.new
    28: order.add_line_item LineItem.new(2, 3.00)
    29: order.add_line_item LineItem.new(4, 1.00)
 => 30: binding.pry
    31: order.add_line_item LineItem.new(1/0, 1.00)
    32: puts order.total
```

让我们继续播放第 31 行:

```
[1] pry(#<Order>)> play -l 31
```

不出所料，程序崩溃了。要查看详细的堆栈跟踪，请使用`wtf?`命令:

```
[2] pry(#<Order>)> wtf?
Exception: ZeroDivisionError: divided by 0
--
0: (pry):6:in `/'
1: (pry):6:in `total'
3: /usr/local/var/rbenv/versions/2.0.0-p247/lib/ruby/gems/2.0.0/gems/pry-0.9.12.2/lib/pry/pry_instance.rb:328:in `evaluate_ruby'
...
9: /usr/local/var/rbenv/versions/2.0.0-p247/lib/ruby/gems/2.0.0/gems/pry-0.9.12.2/lib/pry/pry_instance.rb:231:in `catch'
```

要查看一个*甚至*更长的堆栈跟踪，只需追加更多的`?`。例如，`wtf???`产生 30 行。开发人员确实很有幽默感。

### 用`cat --ex`分析堆栈轨迹

普里还有另一个锦囊妙计。`cat --ex`将您导向引发异常的实际行:

```
[3] pry(main)> cat --ex

Exception: ZeroDivisionError: divided by 0
--
From: (pry) @ line 1 @ level: 0 of backtrace (of 15).

 => 1: order.add_line_item LineItem.new(1/0, 1.00)
```

`cat --ex`还接受一个数字作为额外的参数，这实际上是沿着堆栈跟踪向上走。这个特性对于调试和跟踪较大的程序非常有用。(任何开发过 Rails 应用程序的人肯定会喜欢这个特性。)

## 包扎

我希望这篇文章已经说服你尝试一下 Pry(它听起来很不错，不是吗？).仍然有相当多的材料没有被覆盖。例如，我们没有考虑如何用 Pry 替换 Rails 控制台。此外，我们还没有看到 Pry 的[插件](https://github.com/pry/pry/wiki/Available-plugins)，它们为已经令人印象深刻的功能集增加了更强大的功能。

到目前为止，窥探的唯一缺点是启动会话所需的时间。然而，对于 Pry 赋予您的所有功能和灵活性来说，轻微的延迟只是一个小小的代价。

一定要看看官方的窥探维基。T2 的文档非常全面。还有几个链接可以让你了解更多关于 Pry 的知识，所以我在这里就不赘述了。

快乐窥探！

## 分享这篇文章*