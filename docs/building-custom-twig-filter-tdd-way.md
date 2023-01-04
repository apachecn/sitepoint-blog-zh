# 以 TDD 方式构建自定义树枝过滤器

> 原文：<https://www.sitepoint.com/building-custom-twig-filter-tdd-way/>

Twig 是一个强大而又容易掌握的模板引擎。这也是我个人最喜欢的，因为我所有的网络开发都是基于 [Symfony](http://symfony.com) 或者 [Silex](http://silex.sensiolabs.org/) 的。

![Twig Header](img/571f26210a8af00e8465ff16087cdf9e.png)

除了核心语法(`{{ ... }}`和`{% ... %}`)，Twig 还内置了对各种过滤器的支持。过滤器就像一个“转换器”。它接收某些原始数据(字符串、数字、日期等)，并通过应用转换，以新的形式(作为字符串、数字、日期等)输出数据。例如，`number_format`过滤器可以将一个数字转换成可读性更好的数字:

```
{{price|number_format(2, '.', ',')}}
```

假设`price`是一个值为`1234567.12345`的变量，过滤操作后，页面中的输出将是`1,234,567.12`:小数点后 2 位，“”作为小数点，而“，”作为千位分隔符。这使得它更具可读性。

作为另一个例子，`capitalize`将使句子中一个单词的每个首字母大写，其他字母小写:

```
{{title|capitalize}}
```

假设`title`是一个值为`this tutorial is nice`的变量，经过滤波运算后，输出将是`This Tutorial Is Nice`。

Twig 有许多内置的过滤器。完整的名单可以在官方文件中找到。

### 为什么要选择滤镜？

有些人可能会认为上述功能在 PHP 中也是可行的；这是真的。我们还经常发现内置过滤器所提供的功能非常有限且不充分。那么，为什么要用滤镜呢？

在 MVC 环境中，模型层负责提供数据(例如，图书价格或文章标题)。视图层负责显示数据。在控制器中进行过滤器式的数据转换是不可取的，因为这违背了控制器的设计角色，而在模型中进行数据转换实际上会改变数据，这是不好的。在我看来，观点是唯一可行的选择。

此外，由于在模板(以及各种模板)中的许多地方可能需要对来自各种来源的相同数据进行特定的数据转换，因此每次需要这种转换时，最好在模板中调用该过滤器，而不是在控制器中调用函数。代码会更加整洁。

让我们考虑使用过滤器和 PHP 函数调用(使用 Symfony 2 +原则)比较下面的代码段。我们可以很容易地看到优雅和可用性的差异。

过滤器版本:

```
...
<em>{{ book.title|capitalize }}</em> has {{book.pages|number_format(2, ".", ",")}}
and costs ${{ book.price|number_format(2, ".", ",")}}.  
...
```

对于这种方法，我们在控制器中要做的是:

```
$book=$repo->findById('00666');
...
return $this->render('A_Template', ['book'=>$book]);
```

找到书(数据)并将其传递给视图进行显示。

但是如果我们使用 PHP 函数调用，代码可能看起来像这样:

```
//Using PHP function within a Symfony framework and Doctrine
$book=$repo->findById('00666');
$book['tmpTitle'] = ucwords($book->getTitle);
$book['tmpPage'] = number_format($book->getPages(), 2, ".", ",");
$book['tmpPrice'] = number_format($book->getPrice(), 2, ".", ",");
...
return $this->render('A_Template', ['book'=>$book]);
```

..然后在模板中

```
<em>{{ book.tmpTitle }}</em> has {{book.tmpPages}}
and costs ${{ book.tmpPrice}}.
```

您可以看到，过滤方法更加简洁，也更容易管理，中间没有笨拙的临时变量。

### 让我们建立一个过滤器

我们将构建一个过滤器，以一种更有趣的方式显示一篇文章的发表日期/时间。也就是不再说什么“贴在`2015-03-14 13:34`”之类的话，而是将这个时间戳转换成类似于“*刚才*”、“*几个小时前*”、“*几天前*”、“*相当一段时间前*”、“*长，很久以前*”等。

我们将以 TDD 的方式构建它。要了解 TDD，请参见[这篇文章](https://www.sitepoint.com/basic-tdd-new-php-package/)和其中的链接，但是我们在本教程中采用的方法应该足够容易理解，即使事先不了解 TDD。

首先，通过执行以下 Composer 命令安装`PHPUnit`:

```
composer global require phpunit/phpunit
```

这将在全局范围内安装最新版本的 PHPUnit，使它可以在您的整个机器上从任何文件夹访问。我们将使用 PHPUnit 来运行测试，并断言所有的期望都得到满足。

#### 设定期望值

用例很清楚:我们想要将一个日期/时间对象(`2014-03-19 12:34`)转换成类似于“*刚才的*”、“*几个小时前的*”、“*几天前的*”、“*很久以前的*”、“*长，很久以前的*”等，这取决于日期/时间离当前时刻有多远。

没有固定的规则来确定日期/时间应该有多远，以便可以显示为“*相当一段时间以前*”。这是一个主观问题，因此我们将为我们的应用程序定义一个定制的规则集，这些规则将反映在我们的预期中:

| 多久以前 | 被认为是 |
| --- | --- |
| `< 1 minute` | 刚才 |
| `< 10 minutes` | 几分钟前 |
| `< 1 hour` | 一小时内 |
| `< 16 hours` | 几小时前 |
| `< 24 hours` | 一天之内 |
| `< 3 days` | 一段时间以前 |
| `< 10 days` | 从前 |
| `> 10 days` | 来自火星 |

让我们将这些期望转化成一个测试脚本，这样我们就可以用 PHPUnit 测试它了。该脚本保存在`src/AppBundle/Tests/Twig/timeUtilTest.php`中:

```
<?php

namespace AppBundle\Tests\Twig;
use AppBundle\Twig\AppExtension;

class timeUtilTest extends \PHPUnit_Framework_TestCase
{
    /**
     * @dataProvider tsProvider
     */
    public function testtssFilter($testTS, $expect)
    {
        $tu=new AppExtension();
        $output=$tu->tssFilter($testTS);
        $this->assertEquals($output, $expect);
    }

    public static function tsProvider()
    {
        return [
            [date_sub(new \DateTime(), new \DateInterval("PT50S")), "Just now"],
            [date_sub(new \DateTime(), new \DateInterval("PT2M")), "Minutes ago"],
            [date_sub(new \DateTime(), new \DateInterval("PT57M")), "Within an hour"],
            [date_sub(new \DateTime(), new \DateInterval("PT13H1M")), "A few hours ago"],
            [date_sub(new \DateTime(), new \DateInterval("PT21H2M")), "Within one day"],
            [date_sub(new \DateTime(), new \DateInterval("P2DT2H2M")), "Some time back"],
            [date_sub(new \DateTime(), new \DateInterval("P6DT2H2M")), "Ages ago"],
            [date_sub(new \DateTime(), new \DateInterval("P13DT2H2M")), "From Mars"],
        ];
    }
}
```

如果我们现在运行这个测试:

```
phpunit -c app/
```

测试不会运行，因为我们还没有定义`AppBundle\Twig\AppExtension`。我们可以快速创建一个骨架文件:`src/AppBundle/Twig/AppExtension.php`。事情可以这么简单:

```
namespace AppBundle\Twig;

class AppExtension extends \Twig_Extension
{
    public function getFilters()
    {
        return [
            new \Twig_SimpleFilter('tss', [$this, 'tssFilter']),
        ];
    }

    public function getName()
    {
        return 'app_extension';
    }

	public function tssFilter(\DateTime $timestamp)
    {
    	// to be implemented 
    }
}
```

现在我们可以运行测试脚本了。所有的测试(期望)都会失败，因为我们没有做任何事情来实现`tssFilter`函数。

**注意:** Symfony2 与 PHPUnit 配合得非常好。在默认的 Symfony2 设置中，项目的`app`文件夹中有一个`phpunit.xml.dist`文件。上述命令将自动使用该文件作为 PHPUnit 的配置文件。通常情况下，无需进一步调整。

下面列出了`tssFilter`函数的完整代码:

```
public function tssFilter(\DateTime $timestamp)
    {
        $TSS=['Just now','Minutes ago','Within an hour','A few hours ago','Within one day','Some time back','Ages ago', 'From Mars'];

        $i=-1;
        $compared = new \DateTime();

        $ts1=$timestamp->getTimestamp();
        $co1=$compared->getTimestamp();

        $diff=$ts1-$co1;
        if($diff<0 ) // Diff is always <0, so always start from index 0
        {
            $i++;
        }

        if($diff<-1*60 ) //within one minute
        {
            $i++;
        }

        if($diff<-10*60) // within ten minues
        {
            $i++;
        }
        if($diff<-60*60)
        {
            $i++;
        }

        if($diff<-16*60*60)
        {
            $i++;
        }

        if($diff<-24*60*60)
        {
            $i++;
        }

        if($diff<-3*24*60*60)
        {
            $i++;
        }

        if($diff<-10*24*60*60)
        {
            $i++;
        }

        return $TSS[$i];
    }
```

代码将驻留在`tssFilter`中。它接受一个`DateTime`对象，这样程序就可以根据现在的距离`timestamp`确定应该返回`$TSS`中的哪个字符串。

就是这样！运行测试，一切都应该通过！

### 将其集成到 Symfony 中

`tssFilter`仍然与 Symfony 框架隔离。为了在我们的模板中使用它，我们需要在`services.yml`文件中注册它:

```
services:
    app.twig_extension:
        class: AppBundle\Twig\AppExtension
        tags:
            - { name: twig.extension }
```

我们必须提供过滤器类的完全限定名:`AppBundle\Twig\AppExtension`。

最后，我们可以在 Twig 模板中这样使用它:

```
{{post.author|capitalize}} posted "{{post.title|capitalize}}" (posted {{post.creation|tss}})
```

过滤器名称(`tss`)源自`src/AppBundle/Twig/AppExtension.php`文件的`tssFilter()`函数名称，与其他 Symfony 组件一样，“过滤器”被剥离。

### 包扎

在这个快速教程中，我们介绍了一些内容:

1.  树枝过滤器以及为什么使用它们比单纯的 PHP 调用更好。
2.  如何用 PHPUnit 以 TDD 方式构建自定义过滤器？
3.  如何将过滤器集成到 Symfony 框架中？

在下面留下你的评论和想法，分享你的成就吧！

## 分享这篇文章