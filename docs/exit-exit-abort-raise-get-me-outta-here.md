# 出口，出口！中止，提高…让我离开这里！

> 原文：<https://www.sitepoint.com/exit-exit-abort-raise-get-me-outta-here/>

## 出口，舞台左边。

每次进入 irb 会话、启动 ruby 脚本或运行测试运行程序时，都是在启动一个进程。这适用于系统上的任何东西，不仅仅是 Ruby 代码。例如，类似于`grep`、`cat`或`tail`的 shell 命令也是如此。

我们花费了大量的时间和精力来谈论编写代码的正确方法，并确保它有效地执行，但是它是如何给世界留下印记的呢？当流程退出时，这是一个有效交流信息的宝贵机会。

本文将阐明为什么正确退出很重要，以及如何在 Ruby 程序中这样做。

## 代码，出口类型

当一个进程退出时，它总是使用退出代码。退出代码是一个介于 0 和 255 之间的数值。在 Unix 世界中有一个约定，退出代码等于 0 意味着程序成功了。任何其他退出代码都表示某种失败。

## 为什么退出代码很重要？

退出代码是通信。

退出代码的基本情况是成功，您希望您的进程每次都成功退出。在出现问题的情况下，您可以指定多达 255 种不同的失败原因。退出代码对于提供一种通用的方法来理解并向用户报告失败案例是至关重要的，这样用户就可以纠正操作。

顺便说一句，如果你不相信退出代码是值得关心的事情，考虑一下 HTTP 状态代码；在失败响应的情况下，有大量的状态代码表示请求失败的原因，告诉用户下一步应该做什么。你认为是什么激发了 HTTP 状态代码？

## 非 Ruby 示例

我们首先看一下`grep`命令的例子，然后看看在 Ruby 上做同样的事情。注意，我使用 zsh 和`setopt printexitvalue`来查看非零退出代码。

```
$ echo foo | grep bar
zsh: done echo foo |
zsh: exit 1 grep bar
```

在这个例子中，我们打印了字符串“foo ”,并用 grep 表示“bar”。很明显，不会搭配。zsh 告诉我们,`echo`命令已经“完成”,也就是成功退出。`grep`命令退出，代码为 1。这意味着它不成功。对于`grep`命令，退出代码 1 意味着它不能匹配任何输入。

与之相比:

```
$ echo foo | grep foo

foo
```

在本例中，它能够匹配“foo”字符串，因此成功退出。此外，zsh 没有告诉我们一切都很成功。

现在看看当我们向`grep`发送一个无效选项时会发生什么:

```
$ echo foo | grep --whodunnit foo
grep: unrecognized option `--whodunnit'
Usage: grep [OPTION]... PATTERN [FILE]...
Try `grep --help' for more information.
zsh: done       echo foo |
zsh: exit 2     grep --whodunnit foo
```

这次我们向`grep`传递了一个未知选项，它的退出代码是 2。

所以我们知道，对于`grep`，如果退出代码是 1，那么没有找到匹配；如果退出代码是 2，则该命令使用不当。

## 基于退出代码的部署

在我们深入研究如何在 Ruby 中指定退出代码之前，再举一个例子来说明退出代码的重要性。

你曾经这样部署过你的代码吗？

```
rake test && cap deploy
```

您可能对您的 shell 中的`&&`很熟悉。它将两个命令链接在一起，指定只有在第一个命令成功退出时才应该执行第二个命令。成功退出意味着退出代码为 0。

任何 Ruby 测试运行程序都足够聪明，如果测试失败，它会用一个非零退出代码退出。这是一个将错误打印到控制台还不够的例子。正确使用退出代码允许命令在脚本中协同工作，并允许任务自动化。

## 基本情况

如果您从未编写过测试运行程序或命令行程序，那么很有可能您从未需要使用显式方法之一来退出 Ruby 程序。那么，如果你不用它会怎么样呢？

当 Ruby 在执行完所有代码后退出时，基本情况是成功退出！这并不奇怪。如果不指定，您的进程将退出，代码为 0。

## 提前保释

第一种需要显式使用 exit 语句的情况可能是，在执行所有代码之前需要退出一个进程。

假设您有一个名为`hasit`的 Ruby 脚本。该脚本将一个模式作为第一个参数，并从 STDIN 中获取一些数据。如果输入中的任何一行与模式匹配，那么脚本将成功退出。如果它到达末尾，没有找到匹配，那么它退出，代码为 1。

这是完整的剧本。

```
#!/usr/bin/env ruby

pattern = ARGV.shift
data = ARGF.read

data.each_line do |line|
  exit if line.match(pattern)
end

exit 1
```

如果找到匹配，它会使用`Kernel.exit`提前退出。不带参数调用该方法将退出，退出代码为 0。

## 失败

您可以在脚本的最后一行看到，您可以向`Kernel.exit`传递一个整数参数来指定一个不成功的退出代码。

如果我们重视这种情况下的错误消息，而不仅仅是退出代码呢？`Kernel.abort`就是为此而生的。调用`abort`并传递一个字符串作为参数将导致您的进程以状态码 1 退出，并将该字符串打印到 STDERR。让我们更新脚本以退出并显示一条消息:

```
...

data.each_line do |line|
  exit if line.match(pattern)
end

abort "No matches found"
```

使用这两种方法中的任何一种，我们的`hasit`脚本都可以在 shell 结构中正确使用，如下所示:

```
$ hasit debugger lib/* && echo "Don't commit debuggers..."
$ hasit debugger lib/* || cap deploy
```

## 自己打扫卫生

与正确的退出代码密切相关是 Ruby: at_exit 处理程序的一个鲜为人知但很有用的特性。

通过向`Kernel.at_exit`传递一个块，您可以指定在进程退出之前应该执行的代码，这是一个清理任何在正常执行中不会被清理的代码的好地方。

```
...
at_exit {
  # cleanup temp files
  # close open connections
}

data.each_line do |line|
  exit if line.match(pattern)
end

abort "No matches found"
```

现在，无论您的进程使用什么方法退出，您都可以在退出前调用该代码块。除了一种情况！有一个方法`Kernel.exit!`(注意砰的一声)的行为与`Kernel.exit`完全相同，除了它在退出之前跳过任何 at_exit 处理程序。此方法可用于立即退出进程，跳过中途的任何退出处理程序。

## 从裂缝中滑落

退出进程的最后一种方式是未处理的异常。这是您永远不希望在生产应用程序中发生的事情，但是它经常发生，例如在运行测试时。

未处理的异常会将您的退出代码设置为 1，并将异常详细信息打印到 STDERR。

## 我优雅的退场

一般来说，如果你正在编写一个命令行应用程序，你会希望使用`Kernel.exit`或`Kernel.abort`来处理你的退出进程的需求。在极端情况下，你会用到`Kernel.exit!`。`hasit`脚本的完整源代码可以在[这里](http://vangeijt.home.xs4all.nl/opera/css/swiss.css)找到。

最好记住，任何查看您的退出代码的人肯定会喜欢不同错误之间的一点区别，这样他们就可以找出错误出在哪里。给你的命令与其他普通命令相同的约定会使你的命令更容易使用。

出口 0

## 分享这篇文章