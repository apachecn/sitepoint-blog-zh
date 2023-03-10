# 茹:你壳里的红宝石

> 原文：<https://www.sitepoint.com/ru-ruby-shell/>

![ruby](img/4c1ee2132943f4f64ebf3e0b88d7a954.png)

古老的`sed`和`awk`是极其强大的文本处理工具。在大师的手中，这些电动工具可以将文本弯曲成几乎任何形状和形式。不幸的是，我不是大师。更重要的是，我真的不想投入太多时间去学习`sed`和`awk`(甚至`bash`！)，尤其是当我的大多数文本处理任务都是临时的。

输入 [Ru](https://github.com/tombenner/ru) 。Ru 让你这个懒惰的 Ruby 程序员在 shell 中使用你最喜欢的编程语言。这意味着你可以随心所欲地`map`、`sum`、`capitalize`。在本文中，我们将探索 Ru 让您做的一些漂亮的事情，以及它如何简化您的文本处理任务。

## 安装 Ru

Ru 被包装成一颗红宝石。要安装 Ru，请启动您的终端:

```
% gem install ru                                                            
Successfully installed ru-0.1.4
1 gem installed
```

## 基础知识

在开始精彩的部分之前，让我们先了解一下 Ru 是如何让我们使用 Ruby 与 shell 交互的。安装完 gem 后，我们可以用`ru`命令调用 Ru。与典型的 Unix 命令类似，`ru`从标准输入(STDIN)和文件中读取数据。

这是今天探索的第一个示例文件:

```
% cat a_tale.txt
It was the best of times, it was the worst of times, it was the age of wisdom, it was the age of foolishness, it was the epoch of belief, it was the epoch of incredulity, it was the season of Light, it was the season of Darkness, it was the spring of hope, it was the winter of despair…
```

`ru`可以把一个 Ruby 命令和一个文件名作为参数:

```
% ru 'map(&:upcase)' a_tale.txt                                   
IT WAS THE BEST OF TIMES, IT WAS THE WORST OF TIMES, IT WAS THE AGE OF WISDOM, IT WAS THE AGE OF FOOLISHNESS, IT WAS THE EPOCH OF BELIEF, IT WAS THE EPOCH OF INCREDULITY, IT WAS THE SEASON OF LIGHT, IT WAS THE SEASON OF DARKNESS, IT WAS THE SPRING OF HOPE, IT WAS THE WINTER OF DESPAIR…
```

该文件也可以使用管道传递:

```
% cat a_tale.txt | ru 'map(&:upcase)'
```

事实上，您可以将`ru`的输出通过管道传输到另一个命令中，这并不奇怪。假设我想数单词:

```
% cat a_tale.txt | ru 'map(&:upcase)' | wc -w
60
```

这非常强大，因为`ru`只是*的另一个 Unix 命令*，它接受输入并操纵输出。

基本的东西说完了，让我们开始有趣的东西。

## 儒赋的文本处理

了解 Ru 的最好方法是看一些例子，或者更好的方法是亲自尝试！下面是我们将在接下来的几个示例中使用的示例文件:

```
% cat chapters.txt
from zero to deploy
a toy app
mostly static pages
rails-flavored ruby
filling in the layout
modeling users
sign up
log in, log out
updating, showing, and deleting users
account activation and password resets
user microposts
following users
```

### 过滤

假设我想搜索可能过于冗长的章节标题:

```
% cat chapters.txt | ru 'select { |l| l.split.size == 5 }'
updating, showing, and deleting users
account activation and password resets
```

在内部，Ru 将文件读取为一个行数组。然后在数组上调用`select { ... }`。因为是数组，所以可以调用任何 [`Array`方法](http://ruby-doc.org/core-2.2.0/Array.html)。

### 航运公司

有时，了解行号是很有用的。在本例中，我想跳过奇数行，并删除任何空行:

```
% ru 'map.with_index { |line, index| index % 2 == 0 ? line : "" }' chapters.txt | grep -v '^$'
from zero to deploy
mostly static pages
filling in the layout
sign up
updating, showing, and deleting users
user microposts
```

同样，Ru 不会阻止您将输出与现有命令相结合。这里我们用`grep`过滤掉空行。

### 标题化和活动支持

Ru 内置了 ActiveSupport。那是超级甜蜜的，因为它增加了便利的功能，比如`titleize`。这对于那些对标题规则一无所知的临时文章作者来说非常有用:

```
% ru 'map(&:titleize)' chapters.txt
From Zero To Deploy
A Toy App
Mostly Static Pages
Rails Flavored Ruby
Filling In The Layout
Modeling Users
Sign Up
Log In, Log Out
Updating, Showing, And Deleting Users
Account Activation And Password Resets
User Microposts
Following Users
```

(好吧，也许它做得不完全正确，但它仍然很酷……)

### Unix VS Ruby

假设我想找到我最常用的 10 个命令。在一个完全 Unix 的世界里，我可能会这样做:

```
% history | awk '{print $2;}' | sort | uniq -c | sort -rn | head -10
```

这将返回如下输出:

```
1737 cd
1052 git
 880 ls
 857 vim
 608 gst
 310 mix
 263 exit
 248 gc
 240 fg
 224 iex
```

虽然我的大脑可以直观地理解正在发生的事情，但我不太确定是否需要所有额外的标志。还有，我作弊了，不得不在 StackOverflow 上搜索上面的咒语。

以下是以 Ru 为单位的等价形式:

```
history | ru 'inject(Hash.new(0)) { |h, l| h[l.split[1]] += 1; h }.sort_by { |k, v| -v }.map(&:first).take(10)'
```

Ruby 之所以伟大，是因为它的可读语法和表达能力，我这个 Rubyist 完全理解正在发生的事情，即使它看起来稍微有点冗长。

### JSON 格式化程序

在 web 开发过程中，我总是不得不美化 JSON。例如，以以下 JSON 为例:

```
% cat sample.json
{ "id": "0001", "type": "donut", "name": "Cake", "ppu": 0.55, "batters": { "batter": [ { "id": "1001", "type": "Regular" }, { "id": "1002", "type": "Chocolate" }, { "id": "1003", "type": "Blueberry" }, { "id": "1004", "type": "Devil's Food" } ] }, "topping": [ { "id": "5001", "type": "None" }, { "id": "5002", "type": "Glazed" }, { "id": "5005", "type": "Sugar" }, { "id": "5007", "type": "Powdered Sugar" }, { "id": "5006", "type": "Chocolate with Sprinkles" }, { "id": "5003", "type": "Chocolate" }, { "id": "5004", "type": "Maple" } ] }
```

Python 附带了一个漂亮的工具来完成这项工作:

```
cat sample.json | python -m json.tool
```

因此，我一直受到一些 Python 嫉妒的困扰。让我们尝试在 Ru 中实现类似的效果:

```
% cat sample.json | ru 'require "json"; JSON.pretty_generate(JSON.parse(files.join))'
```

这为您提供了预期的美化 JSON:

```
% cat sample.json | ru 'require "json"; JSON.pretty_generate(JSON.parse(files.join))'
{
  "id": "0001",
  "type": "donut",
  "name": "Cake",
  "ppu": 0.55,
  "batters": {
    "batter": [
      {
        "id": "1001",
        "type": "Regular"
      },
      {
        "id": "1002",
        "type": "Chocolate"
      },
      {
        "id": "1003",
        "type": "Blueberry"
      },
      {
        "id": "1004",
        "type": "Devil's Food"
      }
    ]
  },
  "topping": [
    {
      "id": "5001",
      "type": "None"
    },
    {
      "id": "5002",
      "type": "Glazed"
    },
    {
      "id": "5005",
      "type": "Sugar"
    },
    {
      "id": "5007",
      "type": "Powdered Sugar"
    },
    {
      "id": "5006",
      "type": "Chocolate with Sprinkles"
    },
    {
      "id": "5003",
      "type": "Chocolate"
    },
    {
      "id": "5004",
      "type": "Maple"
    }
  ]
}
```

这里有几件事需要注意。首先，我们可以像任何 Ruby 脚本一样执行`require`。看看我们是如何再次美化 JSON 的:

```
% cat sample.json | ru 'require "json"; JSON.pretty_generate(JSON.parse(files.join))'
```

到目前为止，我们假设第一个参数是隐式文件。然而，在这种情况下，文件中的行是在`files`方法中捕获的。在这种情况下，我们需要调用`join`，因为`JSON.parse`期望一个`String`作为输入。

显然，仅仅为了美化 JSON 而输入这么多是一件很麻烦的事情。在你开发任何 Python 羡慕你自己之前，Ru 有一个锦囊妙计。使用`ru save <command_name><command></command></command_name>`，您可以命名一个命令并保存它。例如，要保存 JSON prettifier:

```
%  ru save jsonify 'require "json"; JSON.pretty_generate(JSON.parse(files.join))'
Saved command: jsonify is 'require "json"; JSON.pretty_generate(JSON.parse(files.join))'
```

然后我们可以用`ru run`命令运行该命令:

```
%  cat sample.json | ru run jsonify
```

要获得已保存命令的列表，请键入`ru list`:

```
% ru list
Saved commands:
jsonify require "json"; JSON.pretty_generate(JSON.parse(files.join))
```

## Ru 帮助您简化工作流程

虽然我不经常使用 Ru，但我很高兴我的工具箱里总是有它。要了解更多关于 Ru 的信息，请前往官方[页面](https://github.com/tombenner/ru)。展示了一些优秀的例子，例如:

*   [对整数列表求和](https://github.com/tombenner/ru#sum-a-list-of-integers)
*   [根据响应时间对 Apache 日志进行排序](https://github.com/tombenner/ru#sort-an-apache-access-log-by-response-time-decreasing-with-time-prepended)

感谢阅读！

## 分享这篇文章