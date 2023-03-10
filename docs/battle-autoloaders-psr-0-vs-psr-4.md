# 自动装弹机之战:PSR 0 比 PSR-4

> 原文：<https://www.sitepoint.com/battle-autoloaders-psr-0-vs-psr-4/>

如果你已经过了 PHP 培训的初级阶段，你就会听说过[PSR-0](https://github.com/php-fig/fig-standards/blob/master/accepted/PSR-0.md)——一种自动加载标准，它定义了在你的代码中自动包含 PHP 类的方法，而不需要使用像`require`和`include`这样的语句。

## PSR-0

PSR-0 查看一个类的名称空间，并从这些信息中辨别出它在硬盘上的位置。例如，类`\Zend\Mail\Message`将导致`/path/to/project/lib/vendor/Zend/Mail/Message.php`。

PSR-0 还支持在类名中使用下划线作为替代，以使从 5.2 和更早版本的过渡更加容易。`Zend_Mail_Message`也会导致`/path/to/project/lib/vendor/Zend/Mail/Message.php`。

## 设计者

当 [Composer](https://www.sitepoint.com/re-introducing-composer/) 出现并席卷 PHP 包管理世界时，事情发生了变化。由于它的一些规则，当通过 Composer 查看 PSR-0 级安装时，文件夹经常重复并变得太深。例如，一些文件夹结构最终是这样的:

```
vendor/
    vendor_name/
        package_name/
            src/
                Vendor_Name/
                    Package_Name/
                        ClassName.php       # Vendor_Name\Package_Name\ClassName
            tests/
                Vendor_Name/
                    Package_Name/
                        ClassNameTest.php   # Vendor_Name\Package_Name\ClassNameTest
```

这充其量是混乱的，因为:

> “src”和“tests”目录必须包含供应商和包目录名。这是一个符合 PSR-0 的人工制品。

因此，一些高水平的 PHP 开发人员聚集在一起，提出了一个新标准的建议:PSR-4。

## PSR-4

PSR-4 旨在必要时补充 PSR-0 并与之协同工作，而不是完全取代它。它可以，但不是必须。PSR-4 的主要目标是完全删除 PSR-0 和 5.3 天之前版本的残余内容，并允许更简洁的文件夹结构。对于 PSR-4，上面的文件夹树看起来像这样:

```
vendor/
    vendor_name/
        package_name/
            src/
                ClassName.php       # Vendor_Name\Package_Name\ClassName
            tests/
                ClassNameTest.php   # Vendor_Name\Package_Name\ClassNameTest
```

升级 PSR-0 不是一个选项

> 因为 PSR-0 不允许类名的任何部分之间有中间路径

这是非常重要的——这意味着实施 PSR-4，虽然允许更干净的包，但实施起来要复杂得多。我们称 PSR-4 为面向包的自动装载，因为它在简单性之前支持包的整洁性。

## 选择的方法

建议的目标如下:保持 PSR-0 规则，即所有包必须包含至少两个名称空间级别(供应商和包)，确保供应商包组合可以映射到任何文件夹，并允许供应商包组合和完全限定类名的其余部分之间的文件夹中缀。

这意味着我们将能够把我们的类放在包代码中对我们人类有意义的任何地方，并且仍然可以在 PHP 中流畅地使用它们，而不需要编写替代的加载技术或者求助于手动加载。

此外，草案明确指出，PSR-4 自动加载器应该**永远不要**仅仅因为可能注册了多个自动加载器而抛出异常或引发错误，如果一个自动加载器未能加载一个类，其他自动加载器应该有机会加载——抛出错误并停止流程会破坏这种兼容性。如果需要关于故障的额外信息，应该使用 [PSR-3](https://github.com/php-fig/fig-standards/blob/master/accepted/PSR-3-logger-interface.md) 兼容记录器或其他任意手段。

如示例文件所示，使用 PSR-4 自动加载程序从以下结构加载类:

```
 /path/to/packages/foo-bar/
          src/
              Baz.php             # Foo\Bar\Baz
              Qux/
                  Quux.php        # Foo\Bar\Qux\Quux
          tests/
              BazTest.php         # Foo\Bar\BazTest
              Qux/
                  QuuxTest.php    # Foo\Bar\Qux\QuuxTest
```

会是这样的:

```
 <?php
    // instantiate the loader
    $loader = new \Example\Psr4AutoloaderClass;

    // register the autoloader
    $loader->register();

    // register the base directories for the namespace    prefix
    $loader->addNamespace('Foo\Bar', '/path/to/packages/foo-bar/src');
    $loader->addNamespace('Foo\Bar', '/path/to/packages/foo-bar/tests');
```

其中调用`new \Foo\Bar\Qux\Quux;`将尝试从第一个注册目录加载，而`new \Foo\Bar\Qux\QuuxTest;`将尝试从第二个注册目录加载。

该示例还说明了每个名称空间使用多个文件夹的情况。

## 结论

自动装弹没有灵丹妙药。每种方法都有一些优点和缺点——PSR-4 允许更简单的文件夹结构，但是会阻止我们仅仅通过查看完全限定名来知道一个类的确切路径。另一方面，PSR-0 在硬盘上是混乱的，但是支持那些停留在过去的开发人员(类名称中的下划线用户),并帮助我们通过查看类的名称来辨别类的位置。

你对 PSR-4 有什么感觉？请在下面的评论中告诉我们，或者在众多的[辩论](https://groups.google.com/forum/#!topicsearchin/php-fig/autoload/php-fig/SJTL1ec46II)中发表你的观点。

不管怎样——毫无疑问，面向包的自动加载将会继续存在。如果没有被正式接受为标准，那么由需要它的人自定义实现。这取决于我们加入讨论并充分改进概念以达到这种正式状态。

## 分享这篇文章