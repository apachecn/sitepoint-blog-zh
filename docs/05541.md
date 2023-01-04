# PHP storm 8–新特性

> 原文：<https://www.sitepoint.com/phpstorm-8-new-features/>

Jetbrains 因其开发的 IDE 上的早期访问程序而闻名 IDE 的预发布版本在发布时间前几个月公开发布，因此人们有时间测试它，报告错误并提供反馈。就像 beta，只是不是我们今天习惯的 beta(现代的“beta”是一种产品长期吸的借口)。EAP 完成后，他们进入测试阶段，全面测试他们实现的功能并修复错误，然后才发布。

![](img/8553609ef16d6bbe15e90f2c2cdd783b.png)

PhpStorm 是我个人选择的 IDE，也是我们在之前[介绍过的一个，现在即将推出第 8 版，并且自 2014 年 3 月 6 日起已经推出了 EAP 版本。让我们来看看他们的变更日志和 EAP 帖子，并通过链接和/或说明来解释最重要的功能(我们不会涉及错误修复，请阅读发布日志)。](https://www.sitepoint.com/phpstorm-top-productivity-hacks-shortcuts/)

### Drupal 8 支持和多游标

EAP [134.1456](https://blog.jetbrains.com/phpstorm/2014/03/phpstorm-8-early-access-program-started/) 介绍了 EAP 的开端，并宣布了几项备受期待的功能，如:

*   多个光标(详见帖子[此处](https://blog.jetbrains.com/phpstorm/2014/03/working-with-multiple-selection-in-phpstorm-8-eap/))
*   Drupal 8 支持(见这里的详细帖子)
*   改进了链式调用的格式
*   实时模板的快速文档
*   所选函数、类或方法的本地历史记录
*   新的 PHP 格式化选项:类字段对齐
*   控制结构的展开/移除操作
*   新的 PHP 调试特性:复制数组/对象值路径(在变量上下文菜单中)
*   为一个字段生成多个 getter/setter
*   类型推断:带有或的 instanceof，通过常量支持数组访问
*   先进的 AngularJS 支架(详情[此处](https://blog.jetbrains.com/phpstorm/2014/05/angularjs-support-in-phpstorm/)
*   Spy-js JavaScript 跟踪工具集成(详情[此处](https://blog.jetbrains.com/phpstorm/2014/06/tracing-debugging-and-profiling-javascript-code-with-spy-js-in-phpstorm/)
*   JavaScript 和 Node.js 调试器中新的实时控制台允许您输入命令和代码
*   Bower 集成:搜索 Bower 注册表，管理 IDE 中的组件
*   要求 JS 别名支持
*   黄瓜和小于 1.6 的支持
*   代码完成中现在建议使用实时模板
*   代码块在花括号结束后自动缩进

在此或 EAP 的原始帖子上找到其余的[。](http://youtrack.jetbrains.com/issues/WI?q=%23Fixed+%23Verified+%23%7BEAP+134.1456%7D+order+by%3A+votes#_ga=1.101940609.1451145283.1385979112)

### PhpUnit 4.0 支持

两周之后，EAP [136.1255](https://blog.jetbrains.com/phpstorm/2014/03/phpstorm-8-eap-136-1255/) 引入了以下变更:

*   WordPress 支持(见教程[这里](https://confluence.jetbrains.com/display/PhpStorm/WordPress+Development+using+PhpStorm#_ga=1.117608969.1451145283.1385979112)和公告贴[这里](https://blog.jetbrains.com/phpstorm/2014/03/wordpress-support-in-phpstorm-8-eap/))
*   PhpUnit 4.0 支持(详情[此处](https://blog.jetbrains.com/phpstorm/2014/05/phpunit-4-in-phpstorm-8-eap/))
*   结构搜索和替换现在与捆绑在一起
*   编码风格中的常数对齐选项
*   使用 node-webkit 调试
*   更新了 Dart 和 Spy-js 插件
*   自动完成现在也建议输入 type=file

..还有更多。要查看完整列表，请查看原始 EAP 帖子或[发布说明](https://confluence.jetbrains.com/display/PhpStorm/PhpStorm+EAP+136.1255+Release+Notes#_ga=1.75144722.1451145283.1385979112)。

### 远程口译员

在最后一个 EAP 之后的另一个星期， [136.1382](https://blog.jetbrains.com/phpstorm/2014/03/phpstorm-8-eap-136-1382/) 发布了，并增加了对远程 PHP 解释器的支持(这允许开发人员通过 SSH 使用 PHP 解释器，远程运行调试和测试工具)——在此查看如何使用这个[——并引入了通过 SSH 对 PHPUnit 的全面支持(具有多种配置)。同样，WordPress 和 Composer 命令行工具现在可以处理纯可执行文件。要获得这些微小变化的详细列表，请阅读完整的 EAP 帖子或](https://blog.jetbrains.com/phpstorm/2014/04/php-remote-interpreters-support-in-phpstorm-8-eap/)[发布说明](https://confluence.jetbrains.com/display/PhpStorm/PhpStorm+EAP+136.1382+Release+Notes#_ga=1.117027334.1451145283.1385979112)。

### PhpDoc 注释块中的降价支持

4 月 3 日，在 EAP [136.1476](https://blog.jetbrains.com/phpstorm/2014/04/phpstorm-8-eap-136-1476/) 中，一个值得注意的新特性被加入其中 PhpDoc 注释块中的降价支持。你可以在[这篇文章](https://blog.jetbrains.com/phpstorm/2014/04/phpstorm-8-markdown-support-in-phpdoc-blocks/)中阅读这个实现的细节，以及看到一个演示用例。Markdown 的实现是由于 PhpDocumentor 对 MarkDown 的支持，正如[在这里所说的](https://docs.phpdoc.org/references/phpdoc/basic-syntax.html)，但是公平地说，这有点违背了初衷，暴露了对特定工具的偏见。PhpDocumentor 的主要竞争对手， [ApiGen](https://www.sitepoint.com/generate-documentation-with-apigen/) ，缺少这方面的内容。

其他改进包括现在本地和远程运行的多个 PHPUnit 配置，一个丢失的 [@throws report](http://youtrack.jetbrains.com/issue/WI-11805#_ga=1.42121509.1451145283.1385979112) 修复，等等。更多的特性和错误修正可以在[的发布说明](https://confluence.jetbrains.com/display/PhpStorm/PhpStorm+EAP+136.1476+Release+Notes#_ga=1.147487415.1451145283.1385979112)中看到。

### PHPUnit 对远程解释器的覆盖

在最后一个 EAP 之后不到一周，Jetbrains 团队发布了 EAP [136.1575](https://blog.jetbrains.com/phpstorm/2014/04/phpstorm-8-eap-136-1575/) ，增加了对远程解释器的 PHPUnit 4.0 覆盖支持——这是一个仅在两个 EAP 版本之前引入的功能——并极大地改善了索引性能，提高了大型项目的工作流流畅度。还添加了 node-webkit 调试，并添加了一个新的文本编辑快捷方式，在该快捷方式中，command/ctrl + backspace 删除直到该行的开头/结尾。

### 新 PSR-0/PSR-4 检查

EAP [136.1672](https://blog.jetbrains.com/phpstorm/2014/04/phpstorm-8-eap-136-1672/) 引入了对类路径中名称空间/名称不匹配的新检查，对以前 EAP 中的@throws 注释做了进一步的改进，包括对 PHPUnit 和跳过或不完整测试的过滤堆栈跟踪，为构造函数中的字段赋值添加了“添加字段”意图，[等等](https://confluence.jetbrains.com/display/PhpStorm/PhpStorm+EAP+136.1672+Release+Notes#_ga=1.142635957.1451145283.1385979112)。

在 Webstorm 方面，grunt 现在给出了关于成功或不成功构建的反馈，并且您可以编辑 JS 文档 URL(到目前为止，您只能添加或删除它们)。调试方面，断点现在可以有组和名称，它们也显示在右边的空白处。

### 禁用拖放

主要是一个修复版本， [EAP 136.1768](https://blog.jetbrains.com/phpstorm/2014/04/phpstorm-8-eap-136-1768/) 增加了在项目视图中禁用拖放的选项——当使用不可靠的输入法(触控板等)或摄入过多咖啡因时，这一功能往往会变得非常乏味:)它还在 Grunt 的控制台上添加了一个重新启动按钮和一些代码折叠改进，允许你在 if-else 块中只折叠“if”或“else”，而不是全部。无论如何，没什么大不了的，但是你可以在这里阅读剩下的。

### 在 foreach 语句中自动创建@var 声明

如果你像我一样坚持文档良好的代码，你会欣赏 [EAP 138.38](https://blog.jetbrains.com/phpstorm/2014/04/phpstorm-8-eap-138-38/) 带来的变化。这个版本增加了为 foreach 块中的临时变量自动生成@var 声明的选项——这是我每次“foreach”时手动做的，只是为了在循环中使用变量时获得自动完成帮助。一个小但非常有用的升级。还添加了一个新的意图，用 NOWDOC/HEREDOC 替换引号。如果你对错误修复感兴趣，请看这里的。

### 能够从引用远程文件的文件中通过 URL 打开远程文件

[EAP 138.84](https://blog.jetbrains.com/phpstorm/2014/05/phpstorm-8-eap-138-84/) 是对之前 EAP 版本中的一个缺陷的修复版本，并引入了一个有用的新功能——通过 URL 打开的文件可以打开它们在源代码中引用的文件。例如，打开一个远程 URL，该文件的源代码将加载到 IDE 中。现在，您可以在上述源代码(例如 CSS 或 JS)中找到资源，并通过 URL 打开它们，只需按住 CTRL 键并单击它们。完整的问题在这里[描述](http://youtrack.jetbrains.com/issue/WEB-6584)，而完整的发布日志在这里[描述](https://confluence.jetbrains.com/display/PhpStorm/PhpStorm+EAP+138.84+Release+Notes#_ga=1.188880555.1451145283.1385979112)。

### 行为支持

[EAP 138.184](https://blog.jetbrains.com/phpstorm/2014/05/phpstorm-8-eap-138-184/) 是第一个引入初始行为支持的 EAP。关于 PHP 的行为驱动开发框架 Behat 的教程即将在 SitePoint 上发布。Behat 配置的配置方式与 PHPUnit 相同。

![](img/2027cc76806bda074b54d0d682c035d5.png)

为 XDebug 添加了“Skip Into ”,以便您可以在调试时跳过某些函数，为 foreach 循环添加了对实现数组操作符或迭代器的变量的类型推断，一些新的格式化选项，默认情况下折叠某些区域的选项，以及一个新的 PhpDoc 标记:@uses 和@used-by，指示文件的用法，以便 PhpStorm 知道，例如，在您告诉它删除它之前询问您。在这里阅读其余的[。](https://confluence.jetbrains.com/display/PhpStorm/PhpStorm+EAP+138.184+Release+Notes#_ga=1.78732820.1451145283.1385979112)

在[下一个 EAP](https://blog.jetbrains.com/phpstorm/2014/05/phpstorm-8-eap-138-379/) 中，Behat 支持通过添加 intellisense 得到了进一步改善，增加了创建文档块的意图，并引入了更新块，该更新块在升级到 IDE 的新主要版本之前检查插件兼容性——这是人们一直渴望的，尤其是我们这些使用大量插件并容易看到它们崩溃的人。

## 结论

这个列表并不详尽，但是它包含了我认为最重要的更新和新特性。一个新的 EAP 正在路上，当它发布时，这篇文章会相应地更新。这些特征中哪一个让你最快乐？你是否已经在使用 EAP 版本并测试新产品了，或者你更像是一个稳定版本的开发者？

## 分享这篇文章