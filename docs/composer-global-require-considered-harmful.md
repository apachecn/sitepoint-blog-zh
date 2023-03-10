# Composer Global Require 被认为有害？

> 原文：<https://www.sitepoint.com/composer-global-require-considered-harmful/>

在之前，我们已经讨论过 Composer 最佳实践[，我一直提倡在安装可以跨多个项目使用的包时使用`composer global require`——尤其是命令行工具。然后，前几天，我碰到了](https://www.sitepoint.com/mastering-composer-tips-tricks/)[这个讨论](https://github.com/composer/composer/issues/5390)。

![Sad Composer](img/9208d6eeee11f0ee0761a6cbc3fbf120.png)

简而言之——大多数人现在似乎认为`global require`是一个糟糕的实践，除非全球安装的软件包没有依赖性。从技术上讲，当一个人对所有项目使用一个[单一环境](https://github.com/composer/composer/issues/5333)时，这是有意义的，但是正如我在那个讨论中所评论的，当对每个项目使用一个[VM](https://www.sitepoint.com/quick-tip-get-homestead-vagrant-vm-running/)或者一个像 Docker 或类似的适当隔离的环境时，那么这个问题是没有意义的，并且`global`实际上不会造成伤害。

OP 对此问题的建议解决方案是:

> 或者，用户应该使用 composer require 将每个命令行工具安装到它自己的本地项目中，并手动管理它们的`$PATH`或二进制文件(例如，通过从已经在`$PATH`中的 bin 目录创建符号链接)。

对我来说，这是完全不能接受的复杂情况。Composer 一直是 PHP 的骄傲，因为它很容易使用，而且对新手很友好，使得包管理——本地*或*全球。不得不符号链接周围的东西(特别是考虑到像 Windows 这样的非符号链接操作系统)会增加乏味。然后，OP 进一步建议改变`global`命令的工作方式:

> 可以将一个“全局”但独立的项目安装到~/中。作曲/全局/【某事物】；它的 vendor 和 bin 目录会出现在它们通常的位置，而~/的内容。composer/global/[something]/bin 目录可以在~/中镜像(通过符号链接)。composer/vendor/bin，或者更好的选择就是~/。作曲/斌。有各种方法可以选择字符串[某物];最简单的就是 org/project(尽管这意味着长路径，比如~/。将存在组合者/全局/组织/项目/供应商/组织/项目)。

我完全同意这种做法，这似乎是两全其美的。显然，这可能会导致一些向后兼容性问题，但这并不是说这不会在 Composer 2.0 版本中发生。泰勒·奥特威尔在下文中对这一观点作了进一步的回应:

> 完全同意。让每个 composer 全局安装包安装到它自己的独立目录中，并且有它自己的独立依赖项，而不是可能与其他全局安装包冲突，这将是令人惊奇的。

遵循这一切，本着真正的开源精神，OP 随后构建了替代的`global`实现作为一个单独的工具: [cgr](https://github.com/consolidation-org/cgr) 。让我们来看看它是如何工作的。

## CGR-作曲家全球需要替代方案

*我将在一个[家园改进版](https://www.sitepoint.com/quick-tip-get-homestead-vagrant-vm-running/)实例*上执行以下所有命令

为了开始使用 CGR，我们将其安装为一个[全局包](https://en.wikipedia.org/wiki/Irony)。

```
composer global require consolidation/cgr 
```

如果您的作曲者的`bin`文件夹不在 PATH 变量中，请添加它:

```
echo "export PATH=\$PATH:\$HOME/.composer/vendor/bin/" >> ~/.bashrc
echo "export CGR_BIN_DIR=\$HOME/.composer/vendor/bin" >> ~/.bashrc
source ~/.bashrc 
```

以上命令扩展了`$PATH` env。可变的路径到作曲家的全球 bin 目录(默认位置在[家园改进](https://www.sitepoint.com/quick-tip-get-homestead-vagrant-vm-running/)——你的可能会有所不同)。第二个命令配置`bin`目录供`cgr`使用，而第三个命令加载这些更改。这些也将在你每次作为这个用户运行终端界面时自动加载(在我的例子中，通过`vagrant ssh`漫游)。

然后 CGR 应该可以通过运行`cgr`来访问，并且应该输出 Composer 的一般帮助文件。

### 以正确的方式安装 global Composer 软件包

```
cgr phpunit/phpunit 
```

在 [Homestead Improved](https://www.sitepoint.com/quick-tip-get-homestead-vagrant-vm-running/) 上，配置了一个有用的别名，其中键入`phpunit`扩展为`vendor/bin/phpunit`，这在 phpunit 按项目安装时很方便，因此它可以从根文件夹运行。为了测试 PhpUnit 的全局安装，我们需要先删除这个别名(在`~/.bash_aliases`注释的适当行)，然后退出 shell 并重新进入，这样别名就可以重新加载了。然后，运行这个新全局安装的带有版本输出的 PhpUnit，应该会产生类似如下的结果:

```
vagrant@homestead:~$ phpunit --version
PHPUnit 5.4.2 by Sebastian Bergmann and contributors. 
```

现在让我们试着安装两个不兼容的包。

```
cgr laravel/installer
cgr wp-cli/wp-cli 
```

果然，他们都安装得很好。让我们检查他们是否工作。

```
vagrant@homestead:~$ wp --version
WP-CLI 0.23.1
vagrant@homestead:~$ laravel --version
Laravel Installer version 1.3.2 
```

一切都好！以前由于依赖关系不匹配而冲突的全局包现在可以并行存在，并且可以在操作系统范围内顺利使用！

### 工具应该/不能做什么？

在某些情况下，您可能想要安装 Composer 插件。正如[限制部分所述](https://github.com/consolidation-org/cgr#limitations)，由于 CGR 将每个全局包安装到自己的文件夹中，并带有自己的依赖树，因此这些包不会在所有全局项目中全局可用。因此，如果你想安装改变 composer 一般行为的插件，你仍然应该使用`composer global require`而不是`cgr`。例如，CGR 本身就是一个这样的插件。

## 下一步是什么？

测试测试测试！如果你经常使用`global require`命令，我建议你测试一下这个新工具，并且[给 Greg Anderson 一些反馈](https://github.com/composer/composer/issues/5390#issuecomment-223761168),告诉他它在满足你的全球需求方面有多好，还有哪些地方需要改进。

请注意，这个工具仍然只是一个概念验证，实现可能会也可能不会被重命名、重新打包、最终实现到 Composer 的核心等等。换句话说，尽可能地使用它，但是不要太依赖它。

当你的全球软件包正在安装时，为什么不告诉我们你对`composer global require`的感觉呢？它是否像现在许多人认为的那样有害，或者仅仅是一个小心谨慎和拥有孤立的开发环境的问题？还有别的吗？下面插话！

## 分享这篇文章