# PHP 的最小依赖注入容器

> 原文：<https://www.sitepoint.com/bucket-is-a-minimal-dependency-injection-container-for-php/>

我受够了 PHP 缺少一个像样的二进制容器，所以今天我创建了 [Bucket](http://github.com/troelskn/bucket/tree/master) 。明确地说，我认为当前可用的容器(至少我知道)的问题是它们要么不稳定，被抛弃，需要在外部 XML 文件中进行大量预先配置，要么紧密集成到一些全栈框架中。我曾一度对 [Phemto](http://phemto.sourceforge.net/) 寄予厚望，但似乎进展已经停滞。

Bucket 做的不多，但那其实就是本意。这意味着它是功能完整的，并且可能没有错误。而且不像 [Twittee](http://github.com/fabpot/twittee/tree/master) ，它实际上是按原样有用的。

Bucket 主要是我大约一年前在[中描述的处理依赖关系](https://www.sitepoint.com/dealing-with-dependencies/)的代码的总结。它增加了一些现成的附加功能。值得注意的是:

*默认工厂使用反射/类型提示来确定依赖关系*。所以你可以走了:

```
class Foo {
}
class Bar {
  function __construct(Foo $foo) {}
}
$container = new bucket_Container();
$container->get('Bar'); 
```

通过接口:

```
interface Zap {
}
class Foo implements Zap {
}
class Bar {
  function __construct(Zap $zap) {}
}
$container = new bucket_Container();
$container->registerImplementation('Zap', 'Foo');
$container->get('Bar'); 
```

*可以对工厂使用回调*。现在还不是很有用，但是当 PHP 5.3(希望很快)问世时，您可以使用:

```
$bucket = new bucket_Container(
  array(
    'pdo' => function($container) {
      return new PDO("mysql:host=localhost;dbname=addressbook", "root", "secret");
    }
  )
); 
```

你也可以使用基于类的工厂。这可以与回调结合使用，用于覆盖方法。如果你创建了一个回调，它优先于 class 方法。在 5.3 之前，您可能希望将工厂写成类方法:

```
class MyFactory {
  function new_pdo($container) {
    return new PDO("mysql:host=localhost;dbname=addressbook", "root", "secret");
  }
}
$bucket = new bucket_Container(new MyFactory()); 
```

*支持子作用域*。意味着您可以创建一个不向其父容器泄漏状态的本地容器。对于标准 web 应用程序来说，这可能不是最重要的特性，但是对于长时间运行的脚本来说，这是一个不错的特性。用法是:

```
$container = new bucket_Container();
$scope = $container->makeChildContainer(); 
```

它只有 153 行代码，没有外部要求，所以请到:[http://github.com/troelskn/bucket/tree/master](http://github.com/troelskn/bucket/tree/master)获取您的副本

## 分享这篇文章