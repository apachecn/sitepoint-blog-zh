# 用 Byebug 调试 Ruby 的来龙去脉

> 原文：<https://www.sitepoint.com/the-ins-and-outs-of-debugging-ruby-with-byebug/>

![](img/cefb5af618a703ef534b8d1cbf16063b.png)

Ruby on Rails 可以说是现代 web 开发中最优秀的创新之一。它对简单性和开发人员生产力的敏锐关注，加上用户满意度，催生了一种快速原型文化，这种文化非常注重单元测试(即使 DHH 从[起已经改变了他的立场](http://david.heinemeierhansson.com/2014/tdd-is-dead-long-live-testing.html))。)毫无疑问，Rails 是许多成功创业的催化剂(Twitter 就是其中之一)。对它的一个主要批评(尤其是来自使用它经验不足的人)是大量使用“魔法”。作为 Ruby 开发人员，我们敏锐地意识到 magic 仅仅是 DSL 和元编程，但这仍然会导致调试失败。输入:Byebug。

Rails 事实上的调试器是 Byebug。它可以与 Rails 一起工作，这是 Pry 所不具备的，由核心 Rails 团队推荐，甚至与 Rails 捆绑在一起。本文将带您了解基本的调试以及一些稍微高级一些的技术。

## 正在设置

Rails 的 5.0 版本附带了 Byebug，但是如果您没有它，您可以简单地将以下内容添加到您的 Gemfile 并运行`bundle`:

```
gem "byebug"" 
```

## 一个例子

我冒昧地创建了一个快速 Rails 项目来调试这里的。这是一个简单的应用程序，演示了一个损坏的快速排序算法，我们将使用 Byebug 来修复它。

Quicksort 以 O(n log n)执行，利用分治法对数组进行排序(事实上，它的性能如此之好，以至于 Ruby 在内部使用它作为其`array.sort`方法)。我可以整天抒情地谈论排序算法，但我的编辑会对我大喊大叫，所以我们最好回到 ByeBug:

克隆项目:

```
git clone https://github.com/disavowd/quicksorter.git 
```

运行以下命令:

```
cd quicksorter && rails s 
```

当您导航到 [http://localhost:3000](http://localhost:3000) 时，您应该会看到以下内容:

```
Unsorted: [77, 22, 66, 28, 39, 4, 54]

Sorted: [4, 28, 85] 
```

这是引我们到这里的一个相当人为的例子:

```
def quicksort(array)
  return array if array.length <= 1

  pivot_index = (array.length / 2).to_i
  pivot_value = array[pivot_index]
  array.delete_at(pivot_index)

  lesser = Array.new
  greater = Array.new

  array.each do |x|
    if x <= pivot_value
      lesser << x
    else
      greater << x
    end
  end

  return quicksort(lesser) + [pivot_value] - quicksort(greater)
end 
```

## 基础知识

您可以用与其他调试器类似的方式输入 byebug:在代码中选择一个点，在该点跳转到调试器并添加:

```
...code...
byebug
...code... 
```

或者:

```
...code...
debugger
...code... 
```

我已经在一个单独的控制器中为您完成了这项工作，所以如果您导航到[http://localhost:3000/debug](http://localhost:3000/debug)，您将在终端中直接进入 ByeBug，看起来就像这样:

```
 28:   def quicksort
 29:     @sorted = quicksort_algorithm(@unsorted)
 30:   end
 31:
 32:   def debug
 33:     byebug 
```

= > 34:@ sorted = quick sort _ algorithm(@ unsorted)
35:render ' quick sort '
36:end
37:end

不是最直观的界面。那我们现在怎么办？这里是 byebug 命令:

`n`(next)–执行下一行代码。`next`很棒，但它不会为你踏入一个功能。它将简单地执行函数，而不会带您浏览代码。

如果您想逐行查看，请使用:

`s`(步入)–这将继续到下一个堆栈帧，并跳转到相应的源。说到来源:

`l`(list)–输出当前执行行周围的源代码。你可以向它传递`-`来查看它之前的代码。您也可以传递用分号分隔的开始和结束行号:`l 2-6`将显示当前正在执行的文件中第二行到第六行的代码。

`c`(continue)–这将继续程序的执行，直到它结束或遇到另一个断点。

`pp`(漂亮的打印)–‘漂亮的打印’变量。当您处理嵌套散列或其他稍微复杂一点的数据结构时，这是非常宝贵的。

(退出)-这将退出 Byebug，并将执行控制权交还给程序。

有了这些对执行的控制，我们很容易忘记我们可以检查堆栈上到底有什么:

```
self.class 
```

会向你展示自我目前是`ApplicationController`对象。它有哪些方法？

```
m  ApplicationController 
```

`m`代表`method`，将产生:

```
debug
quicksort
quicksort_algorithm 
```

基本情况到此结束。让我们看一个与我们的问题相关的更实际的例子。

## 高级用法

我们的快速排序很好地对元素进行了排序——只是在排序过程中丢失了一半元素。如果能设置条件断点岂不是很棒？哦，等等…我们可以！当 pivot 值等于我们在浏览器中看到的数组中的中间值时，让我们进入 Byebug 并四处查看。

在第 25 行增加以下内容:

```
byebug if pivot_value == 28 
```

现在，在 byebug 控制台上，键入:

```
greater 
```

您应该看到:

```
[77, 66, 39, 54] 
```

嗯，那些看起来确实像我们丢失的数字！罪魁祸首显然是第 25 行的错误的`-`，所以将它改为`+`将产生一个函数(如果有点天真！)快速排序算法。

这个精心设计的例子清楚地说明了`byebug`只是一个常规的 Ruby 方法调用，这意味着当你驾驭它的对象模型时，它会受到 Ruby 提供的所有好东西的影响。了解何时以及何种条件附加到断点上，对于高效使用 Byebug 来说是一个巨大的好处。

## 其他高级技术

`f`(帧)–显示当前正在执行的堆栈帧的信息，包括文件和行号。这也需要一个整数，所以您可以看到在几个堆栈帧时间内将执行什么，例如:`f 2`。

`th`(线程)–允许您检查与线程相关的信息，并与线程进行交互。过去，`list`、`start`和`stop`都对我有所帮助。此外，您可以传递一个数字来将上下文切换到相应的线程。

`hist`(历史)–将显示您的所有历史记录。这(有益地)在调试会话之间持续。

`save`–将 byebug 历史保存到一个文件中(您可以用一个参数来指定，或者让它默认为您的主目录中的`.byebug_save`)

在我看来，对于高级用户来说，这是最好的特性之一。如果你有一堆 Byebug 命令、别名或者你经常使用的自定义函数，你可以直接把它们放到你的调试会话中。

这将使你进入一个 irb 会话。

`bt`(回溯)-显示回溯。

这是最通用的命令之一。将`args`、`variables`、`instance_variables`、`global_variables`、`locals`传递给 it 部门，以了解范围内的更多信息。将显示当前正在执行的文件、行数、断点位置列表、最后修改时间和 SHA1 摘要。当您不确定您的更改是否已经传播时，这绝对是无价的。

## 收尾工作

这可能是使用 Byebug 进行调试的坚实基础，但是我们仅仅触及了皮毛。我们还没有看到断点和捕捉点，遍历程序堆栈或显示。要了解更多信息，我推荐这款优秀的[备忘单](http://fleeblewidget.co.uk/2014/05/byebug-cheatsheet/)，或者更好的是，只需四处挖掘并使用它。在最初的学习曲线之后，它相当直观，您应该会发现这是对您的开发人员工具箱的一个有价值的补充。

Byebug 的一个伟大之处在于它可以和其他程序很好地协同工作。优秀的[撬 byebug](https://github.com/deivid-rodriguez/pry-byebug) 增加了`next`、`step`、`finish`、`continue`、`break`命令使用 byebug 撬。此外，在我使用 [minitest](https://github.com/seattlerb/minitest) 的任何项目中，我的默认宝贝之一是神奇的 [minitest-byebug](https://github.com/kaspth/minitest-byebug) 。如果您的一个测试失败了，这将直接把您踢到 Byebug 会话中。只要做一点工作，你就可以很好地使用 [Guard](https://github.com/guard/guard) 来创建一个强大的单元测试工具。最后，对于你 [Sublime](https://www.sublimetext.com/) 用户，有 [sublime_debugger](https://github.com/shuky19/sublime_debugger) ，它将 Byebug 包装在一个整洁的小 GUI 中。

调试愉快！

## 分享这篇文章