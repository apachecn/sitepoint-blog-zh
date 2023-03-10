# 不再有 var _ dump–引入 Symfony VarDumper！

> 原文：<https://www.sitepoint.com/var-dump-introducing-symfony-vardumper/>

最近，Symfony 从 Zend 式的膨胀和僵化走向了极端的解耦和模块化。通过新的[开发者体验](http://symfony.com/blog/making-the-symfony-experience-exceptional)计划，Symfony 已经做了一个 Laravel 风格的 180 度大转弯，直接进入到使其组件对最终用户更加友好、其文档更加完整、其[应用捆绑包被解除捆绑](http://knpuniversity.com/blog/AppBundle)的过程中，简化了入门和进一步开发，几乎是指数级的。考虑到用户友好，它离“最好的朋友友好”还有很长的路要走，但它肯定不再是敌对的。对这个因素有很大贡献的一个因素是他们不断推出在 Symfony 环境之外非常有用的新组件。其中一个组件是新的 [VarDumper](https://github.com/symfony/var-dumper) 。

![symfony-logo](img/2ee97df5b613acecba0787f696eb5c2f.png)

## 为什么？

你在开发一个功能。你要么不想写测试，要么你正在开发的东西需要在函数中间进行一些变量测试——这是一个测试无法涵盖的事情。不可避免地，你会求助于类似`die(var_dump($var));`的东西。即使你已经把它抽象成一种类似于`vddd($var)`的速记方法，它仍然和它生成的单色输出一样笨拙和不可读。

在这个问题上几乎没有选择——有时我们只是需要我们的`vddd` s。当然，如果你是一个 Xdebug 用户，你可能习惯了比原始 PHP 打印稍微好一点的输出。尽管如此，几乎没有什么好的解决方案可以为我们美化这个输出，使它值得安装一个 dev 依赖项。直到 VarDumper。

## 什么是 VarDumper？

Symfony VarDumper 是一个旨在取代你的`var_dump`的组件。它执行本质上相同的功能，但以更漂亮的格式为你提供更多、更多的信息。是你一直想要的`var_dump`。

根据[他们的文档](http://symfony.com/doc/current/components/var_dumper/introduction.html)，通过使用它，您将获得:

*   每个对象和资源类型的专用视图，例如，在转储单个代理实体时过滤掉原则内部，或者通过 stream_get_meta_data 获得关于打开的文件的更多洞察；
*   可配置的输出格式:HTML 或彩色命令行输出；
*   能够转储内部引用，无论是软引用(对象或资源)还是硬引用(数组或对象属性上的= & on)。相同的对象/数组/资源不会重复出现。此外，您将能够检查数据的引用结构；
*   能够在输出缓冲处理程序的上下文中操作。

## 安装和使用

让我们快速地将它安装到我们的 [Homestead 改进的](https://www.sitepoint.com/quick-tip-get-homestead-vagrant-vm-running/)实例中，并运行几个测试。如果您还不熟悉 HI，请花 5 分钟时间了解一下，这样您就可以继续进行测试。

### 装置

与任何现代的 PHP 项目一样，安装就像运行一样简单

```
composer require symfony/var-dumper
```

### 用法示例

它通过新公开的`dump`函数使用:

```
$var1 = "test";

dump($var1);
```

![01](img/27f7aac4dcba1072de90838915cd9077.png)

现在让我们试试更复杂的东西。

```
$a = [
    'ak1' => 'av1',
    'ak2' => 'av2',
    'ak3' => 'av3',
    'ak4' => 'av4',
    'ak5' => 'av5',
];

$b = [
    'bk1' => 'bv1',
    'bk2' => 'bv2',
    'bk3' => 'bv3',
    'bk4' => 'bv4',
    'bk5' => 'bv5',
];

$object = new \stdClass();
$object->prop1 = 10;
$object->prop2 = 20;
$object->prop3 = 30;
$object->prop4 = 40;

$c = [
    'a' => &$a,
    'b' => $b,
    $object
];

dump($c);
```

![01](img/e7b18f2389214ec6ac4fb194662f49b0.png)

如您所见，VarDumper 出色地导出了我们定义的变量，冗长地声明了所有内容——所有内容都配有一些实用的 CSS，不仅语法突出显示了所有内容，还允许我们扩展和折叠数据转储的各个部分。嗯，但是`stdObject`属性旁边的那些加号是什么？公共财产？那它怎么展示私人的呢？真的吗？让我们看看。

```
class Test {
    public $prop1 = 10;
    private $prop2 = 20;
    protected $prop3 = 30;
    private $prop4 = 40;

    public function __construct($value) {
        $this->undefinedProp = $value;
    }
}

$t = new Test(50);

dump($t);
```

![02](img/46481dd16dfadcdff521c3ad20e4cc19.png)

因此，公共财产是加号，私有财产是减号，受保护的财产是散列值。不仅如此，每个转储的属性还有一个标题提示，当您将鼠标悬停在它们上面时，它会显示更多信息:

![02](img/b21f56528fe1f3e58fe3a8528bbf4198.png)

此外，运行时添加的属性在提示和视觉上都有明确的定义——它们被引号括起来。

运行时添加的方法呢？让我们看看。

```
class Test {
    public $m1;
    protected $m2;

    public function __construct() {
        $this->m2 = function() {
            return "I'm method 2";
        };
    }

    public function buildFunction() {
        $this->m3 = function() {
            return "I'm method 3";
        };
    }

    public function __call($method, $args)
    {
        if (isset($this->$method)) {
            $func = $this->$method;
            return call_user_func_array($func, $args);
        }
    }

}

$t = new Test();
$m1 = function() {
    return "I'm method 1";
};
$t->m1 = $m1;
$t->buildFunction();
$t->m1();

dump($t);
```

![03](img/f3eb9ede3585c0f628e1b4b58fe451ab.png)

您可以看到 VarDumper 使用反射公开了比典型的 var_dump 更多的关于对象的信息——甚至是定义方法的代码行。

这些只是 VarDumper 锦囊妙计中的一部分——至于其他的，以及它们的截图，请查看发布的[公告。](http://symfony.com/doc/current/components/var_dumper/introduction.html)

## 结论

VarDumper 是一个很好的工具，用于快速和肮脏的安全和健全性检查，当与 Symfony 的 DebugBundle 结合使用时，它变得更加强大——事实上，从版本 2.6 开始，它默认包含在 Symfony 安装的开发和调试版本中。

有些人可能会认为，将它与一个简单的无命名空间的`dump()`命令一起使用可能会导致与项目中的其他实用方法发生冲突，而且乍一看，它掩盖了它的来源。虽然用一个可更改的函数名来调用该实用程序可能会消除那些担心，但我相信我们在这里处理的是非常基本的、对生产无关紧要的功能，因此这个非问题是可以原谅的。

既然您已经了解了 VarDumper，那么您会将它添加到您自己的项目中以进行动态调试吗？

## 分享这篇文章