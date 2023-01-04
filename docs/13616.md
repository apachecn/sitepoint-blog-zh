# 你的动态语言有多严格？

> 原文：<https://www.sitepoint.com/how-strict-is-your-dynamic-language/>

考虑“四大”动态的、过程化的语言；Perl、Python、PHP 和 Ruby，在某种程度上它们大同小异，只是在同一主题上提供了一些小的变化(忽略 PHP 对函数式编程的缺乏支持)。但是有时候一些小事情会造成很大的不同，也许最重要的是当你的代码被输入了一些它并没有被设计的内容。做了一个简单的例子来比较它们在这方面的差异…

您已经有了一个函数，它接受一个哈希值(一个关联数组)并对其内容做一些事情——对于一个数据库驱动的应用程序来说，这是相当典型的逻辑，其中行是通用的。为了简单起见，假设您的输入是一个姓名列表，每个姓名都用关键字“first”和“given”分解成一个散列。问题是，如果散列不具有您所期望的结构(比如名字不见了)，您的函数将如何处理——没有非标准的功能来使语言更加严格……

## Perl 语言

首先处理 Perl，这里有一个工作示例…

```
 #!/usr/bin/perl -w
use strict;

my $names = [
            {'first'=>'Bob','given'=>'Smith'},
            {'given'=>'Lukas'},
            {'first'=>'Mary','given'=>'Doe'},
        ];

sub printName {
    my $name = shift;
    print "Name is ".$name->{'first'}." ".$name->{'given'}."n"
}

foreach my $name ( @{$names} ) {
    printName($name);
} 

```

注意姓名列表——第二个“行”缺少“第一个”键。运行此程序的输出是:

```
Name is Bob Smith
Use of uninitialized value in concatenation (.) or string at names.pl line 12.
Name is  Lukas
Name is Mary Doe

```

注意输出中的第二行——Perl 抱怨试图去引用一个不存在的变量。但是，在抱怨之后，执行*继续* …

## 服务器端编程语言（Professional Hypertext Preprocessor 的缩写）

PHP 也是如此…

```
 #!/usr/bin/php
<?php
$names = array(
    array('first'=>'Bob','given'=>'Smith'),
    array('given'=>'Lukas'),
    array('first'=>'Mary','given'=>'Doe'),
);

function printName($name) {
    print "Name is ".$name['first']." ".$name['given']."n";
}

foreach ($names as $name ) {
    printName($name);
} 

```

输出是…

```
Name is Bob Smith

Notice: Undefined index:  first in /home/harryf/php/names.php on line 9
Name is  Lukas
Name is Mary Doe

```

PHP 做的事情和 Perl 差不多——报错，但是*继续执行*。当然，这个错误是作为一个通知出现的——许多默认的 PHP 安装都禁用了这个错误级别，这意味着您不会收到任何错误消息。另一个有趣的地方——PHP 抱怨数组*索引*丢失，而不是*值*在某种程度上不可用。

## 红宝石

又来了，这次是在鲁比…

```
 #!/usr/bin/ruby
names = [
            {'first'=>'Bob','given'=>'Smith'},
            {'given'=>'Lukas'},
            {'first'=>'Mary','given'=>'Doe'},
        ];

def printName(name)
    puts("Name is "+name['first']+" "+name['given']+"n")
end

names.each {
    |name| printName(name)
} 

```

输出…

```
Name is Bob Smith
names.rb:10:in `+': can't convert nil into String (TypeError)
    from names.rb:10:in `printName'
    from names.rb:14
    from names.rb:13

```

相比之下，Ruby 在遇到缺少散列键时，*会暂停*的执行(引发一个没有被处理的异常)。有趣的是原因:Ruby 说解引用一些不存在的东西会产生一个 nil 值，你不能把它连接到一个字符串上，因此出现了异常——一个 TypeError。

## 计算机编程语言

最后是 Python…

```
 #!/usr/bin/python
names = [
            {'first':'Bob','given':'Smith'},
            {'given':'Lukas'},
            {'first':'Mary','given':'Doe'},
        ];

def printName(name):
    print("Name is "+name['first']+" "+name['given']+"n")

for name in names:
    printName(name) 

```

而 Python 是如何应对的？

```
Name is Bob Smith

Traceback (most recent call last):
  File "names.py", line 12, in ?
    printName(name)
  File "names.py", line 9, in printName
    print("Name is "+name['first']+" "+name['given']+"n")
KeyError: 'first'

```

像 Ruby 一样，Python *在遇到问题 hash (dict)时暂停*的执行，引发一个异常。不像 Ruby，但更像 PHP，Python 抱怨缺少散列键——它引发了一个 KeyError 异常。

## 那又怎样？

好的——这是一个微不足道的例子，但我也认为它说明了一个常见的反复出现的问题以及这两种语言之间的基本哲学差异。

在构建原型时，您可能会使用自己创建的样本数据，这可能是“完美”的输入。但是当涉及到将您的代码投入生产，并让它在版本号的几次跳跃中幸存下来时，真实的数据开始流动，函数/类开始在不同的上下文中应用(可能不是由您应用)，从而导致这种“意外输入”问题。

是的，你应该总是编写[测试](http://en.wikipedia.org/wiki/Unit_test)，但是当涉及到处理复杂数据结构的[粗粒度 API](http://en.wikipedia.org/wiki/Granularity)时，你总是会错过一些测试。事实上，我认为这一类问题是“动态代码”中最大的错误来源之一——这种语言鼓励快速原型开发，这意味着那些[额外的 48 行代码](http://www.joelonsoftware.com/items/2003/10/13.html)要使它真正健壮要晚得多，如果有的话。所以一种语言的默认行为对我来说很重要。

因此，这里的一条分界线是是否应该引发某种致命错误。默认情况下，当 Ruby 和 Python 暂停时，Perl 和 PHP 继续执行，除非您显式地处理这个问题。哪个更好？

我认为两者都有利弊。对于 web 应用程序，至少在涉及非关键数据的情况下，让您的代码“保持运行”,尽管处于“半中断”状态，通常是一件好事——它让您有时间稍后解决问题，而用户不会看到页面“关闭”。同时你也不知道问题的*后果*是什么。在这个例子中，它们是微不足道的，Perl / PHP 的行为导致了一些仍然可用的东西，但是如果这是一个数据/状态正在改变的操作，我会感到很不*自信——最好一切都完美地工作，或者什么都不做，而不是以某种不一致的状态结束——事务等等。*

这里的另一条分界线是这些语言实际上在抱怨什么。Perl 和 Ruby 担心*值*在某种程度上是不好的。Python 和 PHP 要明确得多，精确地抱怨缺少散列键。

从稍后在代码中定位问题的角度来看，纯粹基于日志文件中的错误消息，我认为 Python / PHP 方式更好——它确切地告诉你*哪里出错了。另外，具体到 Ruby，它引发了一个异常*看起来*在一定程度上是好运气——我试图将值嵌入到一个字符串中，这立即引发了错误。但是，如果我用零值做了一些可以接受的事情——也许只是把它赋给另一个值，会怎么样呢？这个异常可能会在某个地方出现，但不会马上出现，因为我已经取消了对它的引用——调试我想象中的快乐(如果我错了，就解雇我)。*

无论如何，只是为了好玩(阅读:trolling)，这里有一个主观严格排名…

1.  Python(最严格)
2.  红宝石
3.  Perl / PHP

这似乎与 Python 的哲学一致。我把 Perl 和 PHP 放在最后，因为 PHP 允许您关闭那些 E_NOTICE 错误——否则会把 PHP 放在比 Perl 更严格的位置，因为它发现了丢失的散列键。

如果有人愿意，我很有兴趣将这个例子与 [IO](http://www.iolanguage.com/) 、 [Lua](http://www.lua.org) 和 [Erlang](http://www.erlang.org/) 进行比较，如果可能的话(旁注:我确实[明白了这一点](https://www.sitepoint.com/php-to-the-rescue/)——只是为了好玩才按下 PHP 蜂鸣器——我很想看看 [Damien](http://damienkatz.net/) 在 [CouchDB](http://couchdb.net/) 中提出了什么，尤其是关于“具有双向冲突检测和解决的健壮、增量复制”的部分).

## 分享这篇文章