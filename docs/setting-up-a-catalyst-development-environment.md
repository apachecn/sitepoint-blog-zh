# 设置 Catalyst 开发环境

> 原文：<https://www.sitepoint.com/setting-up-a-catalyst-development-environment/>

除此之外，您还可以在这里找到更多关于设置 Catalyst 开发环境的注释/意见和提示。

即使你不了解 Perl，你也可能听说过 [CPAN](http://www.cpan.org) ，它是 PHP 的 [PEAR](http://pear.php.net) 的灵感来源。与 PEAR 相比，CPAN 是世界上最大的编程库和实用程序库——实际上，你可能需要在 Perl 中做的任何“基础水平”的事情都会有一个 CPAN 库，使之变得更容易。正如 Perl 编码人员可能已经厌倦说的那样，CPAN 是这些天使用 Perl 的最大原因——其他语言有比 Perl 更圆滑的语法，但是要完成真正的工作，很难与 CPAN 争论(这也是我对 Catalyst 感兴趣的基本原因)。

获得催化剂最简单的方法是通过 CPAN，就像`$ cpan install Bundle::Catalyst`。Catalyst 有很多依赖项，所以如果您有一个相对较新的 Perl 安装，可以在这里花一些时间。

**Windows Joys**

当然，这是假设您首先拥有 Perl，如果您使用的是 Windows，这可能是个问题。IMO Perl 一直认为 Unix 是它的主要平台，Windows 是“如果我们必须这样做的话”的主要平台。这里也可能是像 PHP 和 Ruby 这样的语言蚕食 Perl 份额的真正关键——尽管事实上它们都没有对 Unicode 的真正支持或任何真正可以与 CPAN 相比的东西，如果更容易进入“Hello World！”在大多数网络开发者使用的平台上，这可能决定了用户的接受程度。同时比较[这个](http://www.py2exe.org/)和[这个](http://www.indigostar.com/perl2exe.htm)的价格。具体到 Catalyst，我认为一大进步将是为 Windows 构建的 [CPAN::迷你](http://cpan.uwinnipeg.ca/module/CPAN::Mini)快照——已经有了…

反正 Perl for Windows 就是 [ActivePerl](http://www.activestate.com/Products/ActivePerl/) ( [下载](http://www.activestate.com/Products/ActivePerl/))。对于 ActivePerl，通常使用他们的[包管理器](http://aspn.activestate.com/ASPN/docs/ActivePerl/5.8/faq/ActivePerl-faq2.html) (PPM)而不是`cpan`外壳。我自己的经验是，如果你安装了 Visual C++，在 Windows 上使用`cpan` shell 是可能的，尽管有些模块在没有干预的情况下无法构建。

就 Catalyst 而言，有 ppm 构建的 Windows 版本可用[这里是](http://home.ngmedia.net/chansen/catalyst/ppms/)——一个进一步的指令——如果`ppm`找到一个包的多个版本，你必须使用你想要安装的包的编号(PPM 报告给你)再次执行`install`命令——使用这些能够得到一个运行在 Windows 上的 *base* Catalyst。那是在第一次尝试`cpan`路径之后——当它安装先决条件 [Data::FormValidator](http://cpan.uwinnipeg.ca/module/Data::FormValidator) 时失败了，并且对找出原因不感兴趣。

在 Windows 上更大的问题是，一旦你安装了 Catalyst，并不是所有的 Catalyst 插件都可以在 Windows 上以 PPM 的形式获得(据我所知)。这意味着使用`cpan`来构建它们。类似的情况也适用于运行这里的一些[示例，其中大部分都引入了进一步的 CPAN 依赖项，如](http://dev.catalyst.perl.org/) [Plucene](http://cpan.uwinnipeg.ca/dist/Plucene) 。

底线是 Catalyst 在 Windows 上感觉不稳定，你必须做好准备知道你在做什么。

解决方案很简单——[在](http://www.linuxdevcenter.com/pub/a/linux/2005/11/17/ubuntu_laptop.html)[可以正常工作的地方](http://www.intertwingly.net/blog/2005/11/01/It-Just-Works)使用 Ubuntu 。实际上还有更多——例如，是否使用 Ubuntu 软件包库或 CPAN？也不记得现在是否有其他的 Ubuntu 包需要安装才能让 CPAN“工作”，比如构建工具。我会在其他时间补上。

无论如何——下一次先决条件知识和选择(即，哪个模板引擎、依赖项和文档等)。)

## 分享这篇文章