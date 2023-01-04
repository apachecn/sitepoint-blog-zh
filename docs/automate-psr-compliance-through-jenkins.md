# 通过 Jenkins 实现 PSR 合规自动化

> 原文：<https://www.sitepoint.com/automate-psr-compliance-through-jenkins/>

> 我们的社会能够运转的原因之一是我们有能力以一种标准进行交流？良好的形式和普遍的%虽然有地方+文化的变化%可以理解的方式)这不仅要求符号，而且他们的用法也要一致？不管是含蓄的还是明确的？如果这种一致性存在的话？一个人可以随心所欲地写作，如果不同的人对文件有不同的解释？我们将生活在一种混乱的交流状态中+我们集体的智力成就~ ~不可能=

这一段很难理解，因为一些标点符号和格式约定被改变了:用加号来表示连词“和”，用问号代替逗号，在应该是句号的地方用双右括号。如果使用我们公认的写作规则，该段落应该是这样的:

> 我们的社会能够运转的原因之一是我们能够以一种标准的、形式良好的、普遍的——尽管有地方和文化差异——可以理解的方式进行交流。这就要求信息的发送者和接收者不仅要对符号，而且要对符号的使用达成一致，无论是明示的还是暗示的。
> 
> 如果没有这种一致性，如果一个人可以随心所欲地写作，如果不同的人以不同的方式解释文件，我们将生活在一种混乱的交流状态中，我们的集体智力成就将是不可能的。

人们早已认识到技术交流中对标准(包括图纸中的符号)的需求，国际标准化组织(ISO)及其在所有工业化国家中的兄弟组织就是为了满足这种需求而存在的。在艺术领域，事情没有那么严格，但有一些规则叫做“风格”。建筑师和诗人经常被捆绑在一起。散文作家和画家有更多的自由，但仍然遵守一些规则。

一种特殊类型的艺术作家，就像你和我，是代码作家，软件开发人员。在我们的贸易中，事情并不总是明朗的。像医学一样，计算机编程是部分科学，部分技术，部分艺术。我们必须遵守一些规则，以便我们为之编写的愚蠢的机器能够理解我们的愿望。但除此之外，我们完全可以自由地以我们想要的方式写我们想要的东西。

或者，我们是吗？

当单独编写像小型预算网站这样的独立应用程序时，遵循自己的私有编程规则(例如使用 camelCase 变量名)当然很好。然而，协作工作需要一个共同规则的协议，或者你知道会发生什么——如果你碰巧不知道，我建议你看看我在文章[持续集成(与 Jenkins)](https://www.sitepoint.com/continuous-integration-with-jenkins-1/ "Continuous Integration (with Jenkins), Part 1") 中讲述的故事。

尽管如此，如果 CI 可以治愈团队开发的典型弊病，它却无力处理不同应用程序集成中的潜在问题，在这些应用程序中，完全不同的风格可能会使理解“源代码”变得困难(除了最初的开发人员之外)，甚至会导致名称冲突。为了满足这一需求，一组开发人员提出了一个用于 PHP 的标准，称为 PSR-X，正如哈里·K·T 的文章[中所述，PSR-1 和 PSR-2 将被批准为标准](https://www.sitepoint.com/psr-1-and-psr-2-to-be-approved-as-standards/ "PSR-1 and PSR-2 to be Approved as Standards")。

虽然现在保证 PSRs 将被广泛采用作为编写严肃的 PHP 应用程序的事实上的标准还为时过早，但有趣的是，一个寻找代码偏差的代码嗅探器和修复程序是由 Symfony 框架的创始人杨奇煜·普登西耶开发的。好吧，他不是法国人！在本文的其余部分，我们将了解他的 PHP-CS-Fixer 是做什么的，以及它如何与像 Jenkins 这样的 CI 工具集成。

## 固定器安装和基本使用

根据其作者的说法，PSR-1 和 PSR-2 工具的 PHP 编码标准修复程序“当你想遵循 PSR-1 和 PSR-2 文档中定义的 PHP 编码标准时，可以修复你代码中的大多数问题。”它不能解决所有问题；它并没有声称找到了所有与标准的偏差，但是，再次引用杨奇煜的话，它“带有相当多的内置修复程序和查找程序，但是非常欢迎每个人贡献更多的修复程序和查找程序。”

固定器安装很简单，或者说，根本没有。你所要做的就是从[这个 Sensiolabs 网站](http://cs.sensiolabs.org "The PHP Coding Standards Fixer for PSR-1 and PSR-2")(Symfony 背后的公司)下载最新版本的`php-cs-fixer.phar`。因为 fixer 被方便地打包成 PHAR 归档文件，所以您所要做的就是将它放在一个方便的文件夹中，比如开发服务器的根目录，并从命令行运行它:

```
jajeronymo@desktop:~/www$ php php-cs-fixer.phar fix /path/to/dir
```

这将在`/path/to/dir`结束时查找并修复目录(文件夹)中所有文件的 PSR 问题。如果您只想检查/修复一个文件，请使用该文件的路径:

```
jajeronymo@desktop:~/www$ php php-cs-fixer.phar fix /path/to/file
```

为了测试修复程序，我写了一个“Hello World”脚本，其中有三处故意违反了 PSR:一个没有“php”指示符的开始标记。一个用于缩进而不是四个空格的制表符，以及一个仅用于 PHP 的脚本中的结束标记:

```
&lt;?
	echo &quot;Hello World&quot;;
?&gt;
```

该文件保存在与`fixer-test.php`相同的文件夹中。然后我跑了:

```
jajeronymo@desktop:~/www$ php php-cs-fixer.phar fix fixer-test.php
```

我在终端上收到的唯一回应是:

```
1) /home/prospero/www/fixer-test.php 
```

然而，我检查了`fixer-test.php`的属性，发现在我运行修复程序时它已经被修改了。打开脚本，我注意到修复程序在开始标记后添加了“php”指示符，用两个空格替换了缩进标记(哦，我期望是四个！)，并且没有删除结束标记，而是在它后面添加了一个换行符——这符合标准的要求，即文件必须总是以空行符结尾。出于好奇，我第二次运行修复程序，发现结束标签已经被删除。

在这一点上，我们已经成功地让 PHP-CS-Fixer 工作，虽然只是在基本模式。该脚本有许多可供用户探索的选项。有关更多详细信息，请参见应用程序网页的使用部分。

## 詹金斯，你能帮我看着这个吗？

正如我在本文开篇提到的文章中所解释的，Jenkins 是一个自动化一系列任务的工具，包括测试，这是持续集成所需要的。如果您没有 Jenkins，请参考第 2 部分的[持续集成(与 Jenkins)中的设置 Jenkins 部分。安装并启动 Jenkins，然后回到这里。](https://www.sitepoint.com/continuous-integration-with-jenkins-2/ "Continuous Integration (with Jenkins), Part 2")

运行詹金斯的 war 文件，将浏览器指向`http://localhost:8080`。在“创建新工作”中单击，然后选择“构建自由风格的软件项目”。给作业起个名字。(自从 1980 年我在 Burroughs 大型机上学习编程以来，我把我的测试工作称为“Trotter”。我想知道我的读者中是否有人能猜到原因。)

选中“定期构建”并在 schedule 框中写“*/5 * * * *”，每五分钟运行一次测试。现在有两条路可以走。要运行 PHP-CS-Fixer，我们需要执行一个 shell 命令(或者，在 Windows 中，一个批处理文件), Jenkins 将充当 cron 作业控制的代理。为了集成一组更复杂的测试和任务，可以添加一个 Ant 脚本。

要遵循第一个选项，请在构建部分的“添加构建步骤”下拉按钮中选择“执行 shell”。在文本框中输入命令，使用文件或文件夹的绝对路径来执行修复程序。我的测试脚本是:

```
php /home/jajeronymo/www/php-cs-fixer.phar fix /home/jajeronymo/www/fixer-test.php
```

保存配置。在停止或关闭之前，Jenkins 将每五分钟运行一次文件`fixer-test.php`上的修复程序。要查看运行中的修复程序，在测试文件中创建一些偏差，比如抑制开始标记中的“php ”,然后等待 Jenkins 运行修复程序。然后打开测试文件，检查所做的更正。

如果没有发生任何更改，或者 Jenkins dashboard 中的 builds 列表显示红色球，则说明发生了错误。请单击作业的时间以打开其记录，然后单击“控制台输出”链接以查看正在进行或未进行的操作。

最后，要从 Ant 脚本中运行修复程序，请将其添加到现有内容中:

```
&lt;project name=&quot;user&quot;&gt;
...
 &lt;exec executable=&quot;php&quot; failonerror=&quot;true&quot;&gt;
  &lt;arg line=&quot;/home/prospero/www/php-cs-fixer.phar fix /home/prospero/www/fixer-test.php&quot; /&gt;
 &lt;/exec&gt;
...
&lt;/project&gt;
```

## 摘要

采用编码标准对于降低复杂 web 应用程序的开发成本以及保证投资是永久性的并且不依赖于最初的编程团队是至关重要的。PSR 的提议可以成为实现这一目标的一种方式，它对像 Jenkins 这样的 CI 平台的易用性是通过 PHP-CS-Fixer 这样的工具实现的。

图片 via[Fotolia](http://us.fotolia.com/?utm_source=sitepoint&utm_medium=website_link&utm=campaign=sitepoint "Royalty Free Stock Photos at Fotolia.com")

## 分享这篇文章