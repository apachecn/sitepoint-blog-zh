# 费尔康 2.0 阿尔法着陆

> 原文：<https://www.sitepoint.com/phalcon-2-0-alpha-landing/>

我以前写过关于 Phalcon 的文章，我们已经发表了各种关于这个框架的文章，如果你访问一下 [Phalcon 类别](https://www.sitepoint.com/php/phalcon-php/)，就会发现这一点。事实上，就在不久前，我[写了一篇关于 Zephir](https://www.sitepoint.com/zephir-build-php-extensions-without-knowing-c/) 的文章，这是 Phalcon 的一项高尚的倡议，旨在让每个人都可以构建 PHP 扩展。

今天，团队已经达到了一个新的里程碑，一个绝对值得讨论的里程碑。Phalcon 团队[提前宣布了即将发布的 Phalcon 2.0 alpha 1](http://blog.phalconphp.com/post/73525793120/phalcon-2-0-alpha-1-released) 。

### 费尔康 2

Phalcon 2 完全重写了最初的 Phalcon 框架，使用的是他们的新语言 Zephir。正如[之前讨论的](https://www.sitepoint.com/zephir-build-php-extensions-without-knowing-c/)，Zephir 是 PHP 的混合类型衍生物(静态和动态类型都支持)，它允许开发人员编写“近似 PHP”的代码，并将其编译成基于 C 的 PHP 扩展，可以像任何其他扩展一样安装(iconv、imap、imagick 等)。Phalcon 2 几乎完全是用这种语言构建的，现在比以往任何时候都更容易维护、更新，最重要的是接受来自社区的贡献。作为工作概念的证明，Phalcon 团队正在运行他们在 Phalcon 2 [的网站。](http://two.phalconphp.com/website)

Phalcon 2.0 正在与当前版本 1.3 同步(它引入了一些 2.0 中没有但却需要的优化)，并且围绕现有的 1.3 类构建，以确保向后兼容。要查看目前已经移植了哪些类，请观察下面的[表](https://github.com/phalcon/cphalcon/wiki/Progress-2.0)。如果你想帮助转移，提交一个你认为合适的拉请求——特别是单元测试。大多数移植的组件还没有经过测试，甚至在完全开发之后。

### Zephir 和创建扩展

Zephir 现在的版本是 0.3，公众可以用它来编写 PHP 的自定义扩展。Zephir 支持您习惯的所有标准逻辑结构(除了“else if”)，并允许您按类型预声明变量，以便更容易编译成高度优化的 C 代码。值得注意的是，Zephir 是一种超前的编译语言，这意味着如果您习惯于快速迭代和测试，编译步骤可能只会让您效率更低。然而，长期收益是值得的。

Zephir 是内存安全的，这意味着它没有指针或手动内存管理支持。对于这种低级操作，应该坚持使用 C。至于功能，Zephir 相对较弱，但比 C 更加用户友好。你可以把它看作是 C 和 PHP 的混合体，更倾向于 PHP。

那么我能用它做什么呢？一直想关闭你的 PHP 应用程序？想知道如果在 C 中运行，贵公司后端的统计计算会快多少？现在，您可以做到所有这些，甚至更多。首先，看看这个关于制作你的第一个 Zephir 扩展的 Vimeo 截屏。

[//player.vimeo.com/video/84180223](//player.vimeo.com/video/84180223)

在 [Vimeo](https://vimeo.com) 上用来自 [Phalcon 框架](https://vimeo.com/phalconphp)的 Zephir 创建你的第一个 PHP 扩展。

接下来，跟随[教程](http://zephir-lang.com/tutorial.html)，阅读[文档](http://zephir-lang.com/)，然后四处游玩。如果你最终制作了教程以外的东西，我们很乐意出版你的分步指南。

一个像这样的 Hello World class 示例:

```
namespace  Test;  /**
 * This is a sample class
 */  class  Hello  {  /**
     * This is a sample method
     */  public  function say()  { echo "Hello World!";  }  }
```

会编译成这个:

```
#ifdef HAVE_CONFIG_H #include  "config.h"  #endif  #include  "php.h"  #include  "php_test.h"  #include  "test.h"  #include  "kernel/main.h"  /**
 * This is a sample class
 */ ZEPHIR_INIT_CLASS(Test_Hello)  { ZEPHIR_REGISTER_CLASS(Test,  Hello, hello, test_hello_method_entry,  0);  return SUCCESS;  }  /**
 * This is a sample method
 */ PHP_METHOD(Test_Hello, say)  { php_printf("%s",  "Hello World!");  }
```

注意，Zephir 要求你使用类和名称空间——没有它们的代码是不允许的——这是为了加强良好的编码习惯，正确的面向对象代码和可靠的封装。

### 贡献的

您可以像往常一样安装 Phalcon 2.0，只从 [2.0.0 开始这样做，从而有助于更快的开发和更好的测试覆盖率。分支](https://github.com/phalcon/cphalcon/tree/2.0.0)。请注意，这将替换您当前的 Phalcon 安装——所以只在开发环境中使用它，最好是像流浪者之类的环境。关于在一个流浪的虚拟机上安装 Phalcon 的更多细节，请看[这篇文章](https://www.sitepoint.com/provisioning-phalcon-tools-vagrant/)。

完整的安装过程如下:

```
git clone http://github.com/phalcon/cphalcon git checkout 2.0.0 cd ext
sudo ./install-test
```

在任何*nix 环境中，这只是克隆通常的 repo，切换到 2.0.0 分支，然后照常安装。API 在很大程度上与以前是一样的，所以转换应该不会很困难——如果您遇到任何错误，Phalcon 团队鼓励在 GitHub 问题页面上报告错误，最好是在单元测试之后演示错误。关于在 GitHub 上为开源项目做贡献的一个很好的概述，请参见[这里](https://www.sitepoint.com/contributing-open-source-dillinger-case-study/)和[这里](https://www.sitepoint.com/contributing-open-source-updating-phalconphp-com/)。

为了进一步毫不费力地为 Phalcon **做出贡献**，考虑投票支持[这一期](https://code.google.com/p/googleappengine/issues/detail?id=9336)——它的目标是将 Phalcon 安装在谷歌应用引擎上，如这里所描述的，并将产生迄今为止最强大的 PHP 环境，甚至超过 [HHVM](https://www.sitepoint.com/hhvm-revisited/) 。

## 结论

Phalcon 前进的速度比任何人预期的都要快。我们可以肯定地期待在 2014 年 Q2 发布完全稳定的版本，标志着 Zephir 开发和 Phalcon2 发布的一年。在那之前很久，这个框架在生产应用中使用可能是安全的。

如果你正在试用 Phalcon 2 或 Zephir，请给我们一个惊喜——我们很乐意发布一些你写的教程。如果你只是有一个想法，或者已经有了一些东西，但觉得你写得不好或者你的英语不好，尽管如此，还是要联系我们——我们会找到办法的。

## 分享这篇文章