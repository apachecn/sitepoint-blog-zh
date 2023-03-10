# twig——最流行的独立 PHP 模板引擎

> 原文：<https://www.sitepoint.com/twig-popular-stand-alone-php-template-engine/>

*这篇文章由[沃恩·安切塔](https://www.sitepoint.com/author/wancheta)进行了同行评审。感谢 SitePoint 的所有同行评审员使 SitePoint 的内容尽可能做到最好！*

* * *

Twig 是 PHP 的一个模板引擎。但是 PHP 本身不就是一个模板引擎吗？

是也不是！

尽管 PHP 最初是一个模板引擎，但它并没有进化成一个模板引擎，尽管我们仍然可以把它作为一个模板引擎使用，请告诉我你更喜欢哪个版本的“Hello world ”:

```
<?php echo "<p> Hello " . $name . "</p>"; ?> 
```

或者

```
<p> Hello {{ name }} </p> 
```

PHP 是一种冗长的语言，当试图输出 HTML 内容时，这种冗长会被放大。

现代模板系统将去掉一些冗长的内容，但仍然在上面添加了相当多的功能。安全性和调试特性是现代模板引擎的支柱。

今天，我们关注 Twig。

![Twig logo](img/9b36a17e3d423f5038197b0e050d6ed2.png)

Twig 是由 Sensio labs 和 Symfony 创建的模板引擎，Sensio labs 是 T2、Blackfire 和 Symfony 的幕后公司。让我们看看它的主要优势，以及如何在我们的项目中使用它。

## 装置

有两种安装 Twig 的方法。我们可以使用他们网站上的焦油球，或者我们可以使用 [Composer](https://www.sitepoint.com/re-introducing-composer/) ，就像我们一直做的和推荐的那样。

```
composer require twig/twig 
```

*我们假设你正在运行一个安装了 PHP 和全球安装了 [Composer](https://www.sitepoint.com/re-introducing-composer/) 的环境。你最好的选择是使用 [Homestead 改良版](https://www.sitepoint.com/quick-tip-get-homestead-vagrant-vm-running/)——它会让你在 5 分钟内开始使用我们正在使用的同一台机器，所以我们都在同一页上。如果你想了解更多关于 PHP 环境的知识，我们有一本很棒的关于 PHP 环境的高级书籍可以在这里购买。*

在我们继续下去之前，有些事情我们需要先弄清楚。

作为一个模板引擎，Twig 可以在项目的前端和后端运行。因此，我们可以用两种不同的方式来看待 Twig:面向模板设计者的 Twig 和面向开发人员的 Twig。

一方面，我们准备好所有需要的数据。另一方面，我们渲染所有的数据。

## 基本用法

为了举例说明 Twig 的基本用法，让我们创建一个简单的项目。首先，我们需要自举 Twig。让我们用以下内容创建一个`bootstrap.php`文件:

```
<?php

// Load our autoloader
require_once __DIR__.'/vendor/autoload.php';

// Specify our Twig templates location
$loader = new Twig_Loader_Filesystem(__DIR__.'/templates');

 // Instantiate our Twig
$twig = new Twig_Environment($loader); 
```

Twig 使用一个名为`Environment`的中心对象。此类的实例用于存储配置、扩展，以及从文件系统或其他位置加载模板。

随着 Twig 实例的引导，我们可以继续创建一个`index.php`文件，在这里我们将加载一些数据并将其传递给 Twig 模板。

```
<?php

require_once __DIR__.'/bootstrap.php';

// Create a product list
$products = [
    [
        'name'          => 'Notebook',
        'description'   => 'Core i7',
        'value'         =>  800.00,
        'date_register' => '2017-06-22',
    ],
    [
        'name'          => 'Mouse',
        'description'   => 'Razer',
        'value'         =>  125.00,
        'date_register' => '2017-10-25',
    ],
    [
        'name'          => 'Keyboard',
        'description'   => 'Mechanical Keyboard',
        'value'         =>  250.00,
        'date_register' => '2017-06-23',
    ],
];

// Render our view
echo $twig->render('index.html', ['products' => $products] ); 
```

这是一个简单的例子；我们正在创建一个包含产品的数组，比如我们的[机械键盘](https://www.sitepoint.com/best-mechanical-keyboards/)，我们可以在模板中使用它们。然后，我们使用`render()`方法，它接受一个模板名(这是我们之前定义的模板文件夹中的一个文件)和我们想要传递给模板的数据。

为了完成我们的例子，让我们进入我们的`/templates`文件夹并创建一个`index.html`文件。首先，我们来看看模板本身。

```
<!DOCTYPE html>
<html lang="pt-BR">
    <head>
        <meta charset="UTF-8">
        <title>Twig Example</title>
    </head>
    <body>
    <table border="1" style="width: 80%;">
        <thead>
            <tr>
                <td>Product</td>
                <td>Description</td>
                <td>Value</td>
                <td>Date</td>
            </tr>
        </thead>
        <tbody>
            {% for product in products %}
                <tr>
                    <td>{{ product.name }}</td>
                    <td>{{ product.description }}</td>
                    <td>{{ product.value }}</td>
                    <td>{{ product.date_register|date("m/d/Y") }}</td>
                </tr>
            {% endfor %}
        </tbody>
    </table>
    </body>
</html> 
```

在浏览器中打开`index.php`(通过访问`localhost`或`homestead.app`，这取决于您如何设置您的主机和服务器)现在应该会产生以下屏幕:

![Rendered table](img/edbb434accbecd3d354cd72ff826bc2a.png)

但是让我们回过头来仔细看看我们的模板代码。

有两种类型的分隔符:`{{ ... }}`用于打印表达式或操作的结果，`{% ... %}`用于执行条件和循环等语句。这些分隔符是 Twig 的主要语言结构，Twig 使用它们来“通知”模板它必须呈现 Twig 元素。

## 布局

为了避免模板中的重复元素(比如页眉和页脚)，Twig 为我们提供了在模板中嵌套模板的能力。这些被称为**块**。

为了举例说明这一点，让我们在例子中将实际内容从 HTML 定义中分离出来。让我们创建一个新的 HTML 文件，并将其命名为`layout.html`:

```
<!DOCTYPE html>
<html lang="pt-BR">
    <head>
        <meta charset="UTF-8">
        <title>Tutorial Example</title>
    </head>
    <body>
        {% block content %}
        {% endblock %}
    </body>
</html> 
```

我们创建了一个名为`content`的块。我们所说的是，从`layout.html`扩展的每个模板都可以实现一个将显示在那个位置的`content`块。这样我们可以多次重用布局，而不必重写它。在我们的例子中，`index.html`文件现在看起来像这样:

```
{% extends "layout.html" %}

{% block content %}
    <table border="1" style="width: 80%;">
        <thead>
            <tr>
                <td>Product</td>
                <td>Description</td>
                <td>Value</td>
                <td>Date</td>
            </tr>
        </thead>
        <tbody>
            {% for product in products %}
                <tr>
                    <td>{{ product.name }}</td>
                    <td>{{ product.description }}</td>
                    <td>{{ product.value }}</td>
                    <td>{{ product.date_register|date("m/d/Y") }}</td>
                </tr>
            {% endfor %}
        </tbody>
    </table>
{% endblock %} 
```

Twig 还允许我们只渲染一个单独的块。为此，我们需要首先加载一个模板，然后呈现该块。

```
$template = $twig->load('index.html');
echo $template->renderBlock('content', array('products' => $products)); 
```

在这一点上，我们仍然有相同的页面，但是我们通过分离上下文块降低了它的复杂性。

## 隐藏物

对象不仅仅可以用于加载模板。

如果我们传递带有关联目录的`cache`选项，Twig 将缓存编译后的模板，从而避免后续请求中的模板解析。编译后的模板将存储在我们提供的目录中。请注意，这是编译模板的缓存，而不是评估模板的缓存。这意味着 Twig 将解析、编译并保存模板文件。所有后续的请求仍然需要评估模板，但是第一步已经为您完成了。

让我们通过编辑`bootstrap.php`文件来缓存示例中的模板:

```
$twig = new Twig_Environment($loader, ['cache' => '/templates/cache']); 
```

## 环

在我们的例子中，我们已经看到了如何使用 Twig 完成循环。基本上，我们使用`for`标签，并为指定数组中的每个元素分配一个别名。在我们的例子中，我们为我们的`products`数组分配了别名`product`。之后，我们可以通过使用`.`操作符来访问每个数组元素中的所有属性。我们使用`endfor`标签来表示循环的结束。

我们也可以使用`..`操作符循环数字或字母。就像下面这样:

```
{% for number in 0..100 %}
     {{ number }}
{% endfor %} 
```

或者对于信件:

```
{% for letter in 'a'..'z' %}
     {{ letter }}
{% endfor %} 
```

这个操作符只是[范围](https://twig.symfony.com/doc/2.x/functions/range.html)函数的语法糖，它的工作方式就像本地 PHP [`range`函数](http://php.net/manual/en/function.range.php)。

向循环添加条件的选项也很有用。有了条件，我们就能过滤出想要迭代的元素。假设我们想遍历所有价值小于 250 的产品:

```
<tbody>
    {% for product in products if product.value < 250 %}
        <tr>
            <td>{{ product.name }}</td>
            <td>{{ product.description }}</td>
            <td>{{ product.value }}</td>
            <td>{{ product.date_register|date("m/d/Y") }}</td>
    </tr>
    {% endfor %}
</tbody> 
```

## 条件式

Twig 还以标签`if`、`elseif`、`if not`和`else`的形式为我们提供了条件句。就像在任何编程语言中一样，我们可以使用这些标签来过滤模板中的条件。

想象一下，在我们的例子中，我们只想显示价值超过 500 的产品。

```
<tbody>
    {% for product in products %}
    {% if product.value > 500 %}
                <tr>
                    <td>{{ product.name }}</td>
                    <td>{{ product.description }}</td>
                    <td>{{ product.value }}</td>
                    <td>{{ product.date_register|date("m/d/Y") }}</td>
        </tr>
    {% endif %}
    {% endfor %}
</tbody> 
```

## 过滤

过滤器允许我们过滤传递给模板的信息，以及以何种格式显示。让我们看看一些最常用和最重要的。树枝过滤器的完整列表[可以在这里找到](https://twig.sensiolabs.org/doc/2.x/filters/index.html)。

### 日期和日期 _ 修改

`date`过滤器将日期格式化为给定的格式。正如我们在示例中看到的:

```
<td>{{ product.date_register|date("m/d/Y") }}</td> 
```

我们以`month/day/year`格式显示我们的日期。在`date`过滤器之上，我们可以使用`date_modify`过滤器用一个修饰字符串来改变日期。例如，如果我们想给我们的日期增加一天，我们可以使用下面的:

```
<td>{{ product.date_register|date_modify("+1 day")|date("m/d/Y") }}</td> 
```

### 格式

通过替换所有占位符来格式化给定的字符串。例如:

```
<td>{{ "This product description is: %s"|format(product.description) }}</td> 
```

### 条状标签

`striptags`过滤器去除 SGML/XML 标签，并用一个空格替换相邻的空白:

```
{{ <p>Hello World</p>|striptags }}` 
```

### 逃跑

转义是最重要的过滤器之一。它过滤一个字符串，以便在最终输出中安全插入。默认情况下，它使用 HTML 转义策略，所以

```
{{ products.description|escape }} 
```

相当于

```
{{ products.description|escape('html') }} 
```

还有`js`、`CSS`、`URL`、`html_attr`的逃跑策略。它们分别对 Javascript、CSS、URI 和 HTML 属性上下文的字符串进行转义。

## 调试

最后，让我们看看调试。有时我们需要访问模板变量的所有信息。为此，Twig 具有`dump()`功能。默认情况下，此功能不可用。在创建 Twig 环境时，我们必须添加`Twig_Extension_Debug`扩展:

```
$twig = new Twig_Environment($loader, array('debug' => true));
$twig->addExtension(new Twig_Extension_Debug()); 
```

这一步是必需的，这样我们就不会意外地泄露生产服务器上的调试信息。配置好之后，我们可以使用`dump()`函数来转储关于模板变量的所有信息。

```
{{ dump(products) }} 
```

## 结论

希望这篇文章能给你一个坚实的 Twig 基础，并让你的项目马上开始！如果你想更深入的了解 Twig，官网提供了非常好的[文档和参考](https://twig.sensiolabs.org/doc/2.x/)可以参考。

你使用模板引擎吗？你觉得 Twig 怎么样？您会将它与 Blade 或 Smarty 等流行的替代产品进行比较吗？

## 分享这篇文章