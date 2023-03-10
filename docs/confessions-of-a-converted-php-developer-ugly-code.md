# 一个转化的 PHP 开发人员的自白:丑陋的代码

> 原文：<https://www.sitepoint.com/confessions-of-a-converted-php-developer-ugly-code/>

我已经做了将近十年的 PHP 开发人员，今年一月开始用 Ruby 开发。虽然最初我是一个怀疑论者，但现在我转变了——我在这里与你分享我的经历。我并不自称是 gun Rails 的开发者；我是 Rails 新手，这也是我写这篇文章的目的。我可能不会直截了当地称呼每件事，但你会明白其中的要点。

改变的想法常常令人害怕。从你所知道和信任的安全的语言转向一种新的语言不应该是轻率的。我认为自己是一名相当优秀的 PHP 开发人员，迁移到 Rails 的想法既令人害怕又令人兴奋。我之所以冒险，是因为我们在工作中开发了一个新网站，现在我可以自信地说，我已经皈依了 Ruby 之道。Ruby 的语言和结构非常漂亮，Rails 提供的开发速度让我的生活变得更加轻松。

这不是一个容易的转变。我的同事可以证明，我有好几天沮丧地对着我的笔记本电脑破口大骂，通常是在尝试做简单的任务时。在 Ruby 看来，这些挫折可以通过发现其他事情有多容易来很好地弥补——任何学习过程都有挫折。对我来说，抒情一点很容易，但是让我们看一个真实的例子。

## 被物化了

Ruby 中的一切都是对象。一切。甚至什么都不是对象；nil 是 NilClass 的一个实例。这意味着，在处理任何类型的变量时，您可以简单地链接您的方法调用，而不是您必须在 PHP 中执行的过程性“嵌套”或无休止的变量创建。“哦，太好了，但这对我意味着什么？”我听到你问了。好吧，继续读下去。

假设我有一个逗号分隔的电子邮件地址字符串，我想要该字符串中所有唯一域名的列表。在 PHP 中，这将变成一个相当重要的过程:

```
<?php
  // Our list of emails
  $string = "mal@sitepoint.com, mal@learnable.com, kevin@sitepoint.com";
  &nbsp;
  // Explode into an array of emails
  $emails = explode(', ', $string);
  &nbsp;
  // Create a function to remove anything which isn’t the domain portion of the email
  $leave_domain = function($subject){
      return preg_replace("/^(.*)@/","", $subject);
  };
  &nbsp;
  // Apply the function we just created to the emails array
  $domains = array_map($leave_domain, $emails);
  &nbsp;
  // Remove any repeated domains
  $unique_domains = array_unique($domains);
  &nbsp;
  print_r($unique_domains);
?>
```

这给了我们:

```
Array
(
    [0] => sitepoint.com
    [1] => learnable.com
)
```

所以我先把我们的字符串分解成一个数组。然后我映射一个函数，它可以替换除电子邮件的域部分之外的任何内容，确保我们只有唯一的值。

注意:我对调用`array_map`使用了匿名函数。这只适用于 PHP 5.3。你可以使用一个`foreach`循环作为替代。

对于一个简单的概念来说，这是相当多的代码。您可以通过嵌套函数将所有这些都放入一个命令中；然而，这是以可读性为代价的:

```
<?php
  $string = "mal@sitepoint.com, mal@learnable.com, kevin@sitepoint.com";
  &nbsp;
  $unique_domains = array_unique( array_map( function($subject){ return preg_replace("/^(.*)@/","", $subject); }, explode(', ', $string)));
?>
```

这是一些该死的丑陋的代码。试着向别人解释，但不要让他们头疼！这里的主要问题是，它要么是嵌套的，失去了可读性，要么包含一堆名副其实的变量来分隔过程的每个部分。

在 Ruby 中，因为一切都是对象，所以我们可以将函数调用链接在一起:

```
string = "mal@sitepoint.com, mal@learnable.com, kevin@sitepoint.com"
unique_domains = string.split(', ').map{ |e| e.gsub(/^(.*)@/,'') }.uniq
```

所以现在很明显发生了什么。我们将字符串分割成一个数组，并将我们的替换映射到数组中的每个元素(`gsub`是 Ruby 正则表达式替换方法)。那么我们只返回唯一的值。

美妙之处在于，发生的过程和你如何读这一行是一样的。诚然，我在这里做了一个巨大的假设，但它读起来像你的正常语言。因为 string 变量包含 string 对象的一个实例，所以我们只是开始调用属于 string 类的方法。

这是 Ruby 和 PHP 之间的一个基本区别，也是 Ruby 是一种非常好的语言的原因之一。

## 这能行吗？

在学习 Ruby 的过程中，我遇到的最大的挫折之一就是我完全不知道哪些函数是可用的，以及它们的具体规范或要求。当我在学习 PHP 的时候，对我来说最简单的方法就是边写边编码；也就是说，通过反复试验。这是一项相对费力的任务，而且确实是一种高效的编码方式。

一旦你安装了 Ruby，你就可以使用美妙的交互式 Ruby Shell，简称 IRB。任何研究过 Ruby 的人都可能听说过它，但是你可能没有意识到它有多么有用。这是你回答简单的“这能行吗？”的默认位置问题。你可以很容易地进入一个 IRB 会话，只需在你的控制台上输入神奇的三个字母‘IRB ’,你就会进入一个命令提示符。在这里，你可以做任何你在普通 Ruby 脚本中会做的事情，一次一行。如果有任何问题，只需按“向上”并解决您的问题！PHP 缺少这种现成的东西，所以这对大多数 PHP 开发人员来说是一个新概念。

在写上面的例子时，我使用了 IRB 来确保语法是正确的。我启动了一个新的 IRB 会话，并设置了我的字符串变量:

```
irb > emails = "mal@sitepoint.com, mal@learnable.com, kevin@sitepoint.com"
 => "mal@sitepoint.com, mal@learnable.com, kevin@sitepoint.com"
```

那里的第一部分，一直到并包括>，就是我的 IRB 提示符。接下来，我将变量' emails '设置为一个字符串，按 enter 键，IRB 输出了前一行的响应；这只是告诉我这是一个字符串。

我的下一个动作是将字符串拆分成电子邮件，并替换电子邮件字符串的名称部分:

```
irb > emails.split(', ').gsub(/^(.*)@/,'')
NoMethodError: undefined method `gsub' for ["mal@sitepoint.com", "mal@learnable.com", "kevin@sitepoint.com"]:Array
```

这里发生了两件事。首先，我使用了之前创建的变量，这很好，我可以在这个会话中使用。其次，我对字符串变量运行 split，然后对它尝试 gsub 方法。

Ruby 随后正确地向我抛出了一个错误:`gsub`不是一个可以在数组上运行的方法。您需要对数组中的每个元素运行`gsub`，而不是数组本身。幸运的是，数组确实有一个函数可以帮我:`map`。map 函数获取一个块，并对数组中的每个元素运行它。

```
irb > unique_domains = emails.split(', ').map{ |e| e.gsub(/^(.*)@/,'')}
 => ["sitepoint.com", "learnable.com", "sitepoint.com"]
```

瞧啊。使用 map 值，我们已经正确地创建了一个只包含电子邮件地址中的域的数组。从那里可以很容易地添加 uniq 方法和 boom——来自一串电子邮件地址的唯一域名。

通过 IRB 这样做，我能够很容易地看到“这行得通吗？”问题并调试代码，直到它完成。

注意:积木很酷。它们是 Ruby 的伟大特性之一，因为它们强大而灵活，但是它们超出了我所谈论的范围。我将在这个网站即将发布的博客中解决这些问题，所以如果你还不知道的话，请继续关注 RubySource。

如果您想测试特定于 Rails 的代码，您可以使用 Rails 控制台；这是类似的，除了它加载整个 Rails 环境。在控制台窗口中，确保您位于 Rails 项目目录中，并键入“rails console”或别名“rails c”。加载需要一秒钟，但是一旦进入，您就可以在 Rails 环境中访问项目的所有模型、助手等等。

```
mal@mal:~/my/rails/project$ rails c
Loading development environment (Rails 3.0.5)
ruby-1.9.2-p180 :001 >
```

这只是我从 PHP 开始使用 Ruby 的一个技巧。我袖子里还有一些。最近开始用 Ruby 或者 Rails 了吗？如果有，有哪些小技巧可以分享？

## 分享这篇文章