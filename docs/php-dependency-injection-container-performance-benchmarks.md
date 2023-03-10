# PHP 依赖注入容器性能基准

> 原文：<https://www.sitepoint.com/php-dependency-injection-container-performance-benchmarks/>

大多数框架和较大的 PHP 应用程序利用依赖注入容器，目标是更易维护的代码库。但是，这可能会对性能产生影响。由于加载时间至关重要，保持网站速度一如既往地重要。今天我将对几个 PHP 依赖注入容器进行基准测试，看看它们的相对性能如何。

对于那些不熟悉这个概念的人来说，依赖注入容器是一个自动构建对象树的软件。例如，考虑一个需要数据库实例的用户对象。

```
$user = new User(new Database());
```

依赖注入容器可用于自动构建对象树，而无需手动提供参数:

```
$user = $container->get('User');
```

每次调用这个函数时，都会创建一个用户对象，并“注入”数据库对象。

有几个众所周知(也不太为人所知)的容器可用于 PHP:

*   PHP-DI ，一个流行的 DI 容器
*   [Symfony \ Dependency Injection](https://github.com/symfony/DependencyInjection)，Symfony 框架提供的依赖注入容器
*   Zend 框架提供的依赖注入容器
*   [Orno\Di](https://github.com/orno/di) ，一种鲜为人知的容器，功能有限，但开发时考虑到了性能
*   [Dice](http://r.je/dice.html) ，另一个鲜为人知的容器，专注于轻量级。完全公开，我是这个容器的作者，但在这个分析中我会完全客观。
*   [气场。Di](https://github.com/auraphp/Aura.Di) ，一个相当流行的具有最小特性的容器

关于 Pimple:虽然 Pimple 被宣传为依赖注入容器，但是从容器中检索对象总是返回相同的实例，这使得 Pimple 成为服务定位器而不是依赖注入容器，因此无法进行测试。

尽管所有的容器都支持不同的特性，但是这个基准测试将涵盖依赖注入容器所需的基本功能。也就是说，创建对象并在需要的地方注入依赖关系。

### 依赖注入的哪些方面将被度量？

1.  执行时间
2.  内存使用
3.  包含的文件数量。尽管这对性能的影响很小，但这是一个很好的指标，表明了一个库是多么的轻量级和可移植。如果因为您的 DI 选择，您的项目必须包含数百个文件，这会严重影响您自己的应用程序的整体重量。

### 测试环境

所有测试都是在运行 Arch Linux (3.15 内核)、PHP 5.5.13 和截至 2014 年 3 月 7 日每个容器的最新版本的同一台机器上运行的。

*所有显示的执行时间数字都是 10 次运行的平均值，其中任何一次运行都比最快的运行慢 20%以上。*

### 测试 1–创建一个对象实例

这个测试使用每个容器创建一个简单的对象 10，000 次

如果没有依赖注入容器，这将被写成:

```
for ($i = 0; $i < 10000; $i++) {
      $a = new A;
    }
```

测试代码(在 github 上): [Aura](https://github.com/TomBZombie/php-dependency-injection-benchmarks/blob/master/aura/test1.php) ， [Dice](https://github.com/TomBZombie/php-dependency-injection-benchmarks/blob/master/dice/test1.php) ， [Orno\Di](https://github.com/TomBZombie/php-dependency-injection-benchmarks/blob/master/orno/test1.php) ， [PHP-DI](https://github.com/TomBZombie/php-dependency-injection-benchmarks/blob/master/php-di/test1.php) ，[Symfony \ dependency injection](https://github.com/TomBZombie/php-dependency-injection-benchmarks/blob/master/symfonydi/test1.php)， [Zend\Di](https://github.com/TomBZombie/php-dependency-injection-benchmarks/blob/master/zend-di/test1.php)

![Test 1 - Execution Time](img/d3e67771581b02bc2911d00d1d66d57f.png)

如你所见，这里有两个明显的阵营。Aura、Dice 和 Orno 大约比 PHP-DI、Symfony 和 Zend\DI 快十倍。

![Test 1 - Memory Usage](img/11f7fb5b5380e2d76cacfd3f3221eeee.png)

类似于执行时间，有两个不同的群体，Symfony 处于中间地带。

![Test 1 - Number of Files](img/d10ed77095cc16847e1633cd5b57cbb3.png)

这很好地说明了每个容器的轻量级，并在一定程度上解释了内存使用的差异。应该注意的是，Zend\Di 使用的许多文件都是通用的框架文件，所以如果您使用 Zend Framework，那么使用 Zend\Di 不会产生相同的内存开销，因为文件可能会在应用程序的其他地方重用。

类似地，PHP-DI 严重依赖于教义库。如果您在项目中使用教条，那么 PHP-DI 的内存开销就会减少。

然而，很高兴看到 Symfony\DependencyInjection 尽管是框架堆栈的一部分，但却完全独立，不依赖于其他 Symfony 项目。

Aura、Dice 和 Orno 没有任何外部依赖性，这有助于减少它们的文件数量。

### 测试 2–忽略自动加载

由于加载文件会影响性能，而且 Zend 和 PHP-DI 都加载了大量文件，所以我们忽略自动加载时间进行了相同的测试，首先创建一个类实例，确保在测量时间之前自动加载了所有需要的类。

*这也可能触发了由容器完成的任何内部缓存，但是为了保持公平，对每个容器都应用了相同的处理方式*

等效的 PHP 代码:

```
for ($i = 0; $i < 10000; $i++) {
          $a = new A;
    }
```

测试代码(在 github 上): [Aura](https://github.com/TomBZombie/php-dependency-injection-benchmarks/blob/master/aura/test2.php) ， [Dice](https://github.com/TomBZombie/php-dependency-injection-benchmarks/blob/master/dice/test2.php) ， [Orno\Di](https://github.com/TomBZombie/php-dependency-injection-benchmarks/blob/master/orno/test2.php) ， [PHP-DI](https://github.com/TomBZombie/php-dependency-injection-benchmarks/blob/master/php-di/test2.php) ，[Symfony \ dependency injection](https://github.com/TomBZombie/php-dependency-injection-benchmarks/blob/master/symfonydi/test2.php)， [Zend\Di](https://github.com/TomBZombie/php-dependency-injection-benchmarks/blob/master/zend-di/test2.php)

![Test 2 - Execution Time](img/1976d7fa75149d3eba90c6f636a2dd8e.png)

![Test 2 - Execution Time](img/efd0ee11c4742816e2298f56546f4ace.png)

![Test 2 - Execution Time](img/53e3626167af25203a06c1ea0e21e131.png)

正如预期的那样，由于没有测量自动加载时间，内存使用量没有变化，性能也略有提高。但是，这说明 PHP-DI，即使加载 42 个文件对总执行时间的影响也可以忽略不计，相对性能保持不变，加载几十个文件并不是 PHP-DI 和 Zend\DI 性能相对较慢的原因。

即使忽略加载文件的开销，这里仍然有两个不同的大概。Aura、Dice 和 Orno 在性能和内存使用方面非常相似，而 PHP-DI、Zend 和 Symfony 只是相互竞争。

所有测试都将忽略自动装载时间，以确保真正测量的是集装箱的性能。

### 测试 3–深层对象图

这个测试是通过让容器构建这组对象 10，000 次来完成的:

```
for ($i = 0; $i < 10000; $i++) {
        $j = new J(new I(new H(new G(new F(new E(new D(new C(new B(new A())))))))));
    }
```

测试代码(在 github 上): [Aura](https://github.com/TomBZombie/php-dependency-injection-benchmarks/blob/master/aura/test3.php) ， [Dice](https://github.com/TomBZombie/php-dependency-injection-benchmarks/blob/master/dice/test3.php) ， [Orno\Di](https://github.com/TomBZombie/php-dependency-injection-benchmarks/blob/master/orno/test3.php) ， [PHP-DI](https://github.com/TomBZombie/php-dependency-injection-benchmarks/blob/master/php-di/test3.php) ，[Symfony \ dependency injection](https://github.com/TomBZombie/php-dependency-injection-benchmarks/blob/master/symfonydi/test3.php)， [Zend\Di](https://github.com/TomBZombie/php-dependency-injection-benchmarks/blob/master/zend-di/test3.php)

*注意:正如你在测试代码中看到的，Symfony、PHP-DI 和 Aura 需要比其他容器多得多的配置代码来执行这个测试。测试中不包括配置时间。*

![Test 3 - Execution Time](img/b223a5e5691bbbe9ac68a2de93237dbf.png)

同样，前 3 名之间的差别很小，骰子比光环快 20%，比奥尔诺快 70%。这三个都比 Zend、PHP-DI 和 Symfony 快得多。三个顶级容器之间的实际差异如此之小，以至于你永远不会注意到像这样的人工基准之外的速度差异。

Zend、PHP-DI 和 Symfony 在这里速度较慢。Zend 执行 Dice 在 1 秒内完成的任务需要 37 秒；当然不是一个微不足道的差别。再一次，Symfony 在大牌容器中处于领先地位。

![Test 3 - Memory Usage](img/51c5a783d09118ac8affd95f37ca9b5b.png)

![Test 3 - Memory Usage](img/1225638a5c805de1db6a6158de463f4e.png)

内存和文件数量与我们在其他测试中看到的一致。

### 测试 4–从容器中获取服务

DI 容器还必须存储和检索将在整个应用程序中重用的服务。这个测试重复地从容器中获取一个实例。

纯 PHP 等价物:

```
$a = new A;
    for ($i = 0; $i < 10000; $i++) {
      $a1 = $a;
    }
```

测试代码(在 github 上): [Aura](https://github.com/TomBZombie/php-dependency-injection-benchmarks/blob/master/aura/test4.php) ， [Dice](https://github.com/TomBZombie/php-dependency-injection-benchmarks/blob/master/dice/test4.php) ， [Orno\Di](https://github.com/TomBZombie/php-dependency-injection-benchmarks/blob/master/orno/test4.php) ， [PHP-DI](https://github.com/TomBZombie/php-dependency-injection-benchmarks/blob/master/php-di/test4.php) ，[Symfony \ dependency injection](https://github.com/TomBZombie/php-dependency-injection-benchmarks/blob/master/symfonydi/test4.php)， [Zend\Di](https://github.com/TomBZombie/php-dependency-injection-benchmarks/blob/master/zend-di/test4.php)

![Test 4 - Execution Time](img/65bc500e3a77620da5f31a57939b4bb6.png)

根据以前的结果，这是出乎意料的。除了 Zend 和 Symfony 之外，所有容器都大致相等，前 4 个结果之间只有 0.01 秒的差距。Symfony 紧随其后，但 Zend 比其他人慢十倍以上。

![Test 4 - Memory Usage](img/dc40bbe748926f7cf6a75fbb6250d885.png)

![Test 4 - Number of Files](img/0097dd70c7638b17a4eb7ecb0f3a40ac.png)

内存使用和文件数量的结果变得可以预测，容器之间的划分也是一样的，我们在整个执行时间中都看到了这一点。

### 测试 5–注入服务

最后一个测试是看一个对象构造和注入一个服务有多快。这采用以下格式:

```
$a = new A;
    for ($i = 0; $i < 10000; $i++) {
        $b = new B($a);
    }
```

测试代码(在 github 上): [Aura](https://github.com/TomBZombie/php-dependency-injection-benchmarks/blob/master/aura/test5.php) ， [Dice](https://github.com/TomBZombie/php-dependency-injection-benchmarks/blob/master/dice/test5.php) ， [Orno\Di](https://github.com/TomBZombie/php-dependency-injection-benchmarks/blob/master/orno/test5.php) ， [PHP-DI](https://github.com/TomBZombie/php-dependency-injection-benchmarks/blob/master/php-di/test5.php) ，[Symfony \ dependency injection](https://github.com/TomBZombie/php-dependency-injection-benchmarks/blob/master/symfonydi/test5.php)， [Zend\Di](https://github.com/TomBZombie/php-dependency-injection-benchmarks/blob/master/zend-di/test5.php)

![Test 5 - Execution Time](img/f65b1d689d173ef205e71500156b0b23.png)

有趣的是，Aura 在这次测试中已经略微领先。然而，这不是一个完全对等的测试，因为 Symfony 和 Aura 需要几行显式配置，而其他容器会自动解决依赖性。配置容器所花费的时间不是基准的一部分。

令人惊讶的是，PHP-DI 在这项任务上是最慢的，Zend 第一次领先于 PHP-DI 和 Symfony。

![Test 5 - Memory Usage](img/f6ab5d75ac0d3fa5df34adaa546fb105.png)

![Test 5 - Number of Files](img/95b92721acc284efe390fcd77d624c85.png)

### 结论

单论性能，Dice、Aura、Orno 都是强劲的竞争对手，Dice 整体最快，Aura 在最终测试中最快。这两个不同组之间的差异是显而易见的，但是比较每个容器的特征是很有趣的。特性的数量和性能并不像您预期的那样密切相关。PHP-DI 和 Dice 都包含独特的特性，但是 PHP-DI 这样做对性能有很大影响。Aura 虽然速度很快，但需要大量的手动配置，正如您所料，它的功能非常少，而 Dice 和 Orno 的性能非常相似，但需要的配置代码要少得多。

Symfony 在所有测试中都处于中间位置，尽管像 Aura 一样配置它是一项非常困难的任务，因为两种支持类型都没有暗示参数。如果你正在寻找一个知名项目的容器，那么如果性能很重要的话，Symfony 就是你的首选。

也就是说，如果纯粹的性能是你所追求的，那么骰子和光环是明显的赢家，或没有非常接近。然而，值得看一看每一个的配置语法和特性，看看你更喜欢使用哪一个，因为对于任何实际的应用来说，Dice、Aura 和 Orno 之间的性能差异是可以忽略的。

所有测试的代码都可以在 github 的[上获得。请注意:github 库包含测试的库的副本，而不是使用 composer 将它们包含在项目中，这是为了确保您可以使用我测试的确切版本运行代码，并获得相同的结果。](https://github.com/TomBZombie/php-dependency-injection-benchmarks)

## 分享这篇文章