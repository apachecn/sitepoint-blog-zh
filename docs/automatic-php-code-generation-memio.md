# 用 Memio 自动生成 PHP 代码

> 原文：<https://www.sitepoint.com/automatic-php-code-generation-memio/>

有没有想过编写负责自动生成某些 PHP 类、方法和属性的代码？请继续阅读，详细了解自动代码生成在什么时候会有所帮助，以及最重要的是，如何使用 [Memio](http://memio.github.io/memio/) 库正确地实现它。

![Code Generation Hero Image](img/5b515f02dea958d29a63617bee7242e1.png)

## 这个概念

基本思想很简单。您编写的代码将创建代码的其他部分、函数、变量、类、文档块等。在编程语言(在我们的例子中是 PHP)中，可以指定参数、if-else 语句、循环等等。

当然，能够自动创建 PHP 代码并不意味着我们这些开发者会被取代。但是它可以用来形成一个基本的结构，这个结构以后会被人类进一步发展。例如，您可以使用生成器，而不是复制粘贴来准备应用程序中的一组初始类。

代码生成已经在各种框架中使用。参见 [Symfony2 GeneratorBundle](http://symfony.com/doc/current/bundles/SensioGeneratorBundle/index.html) 、 [CakePHP 控制台命令](http://book.cakephp.org/3.0/en/bake/usage.html)或 [Laravel Artisan](http://laravel.com/docs/5.0/artisan) 举例。

## 用 Memio 生成 PHP 类

如果您想编写自己的脚本来自动生成 PHP 代码，其中一个选项是使用 [Memio 库](http://memio.github.io/memio/)。Memio 的好处是它写得很好，使用面向对象的代码。你不需要用字符串写目标代码，连接字符串变量等等。一切都是通过创建类实例并调用它们的方法来完成的。输出代码本身的模板存储为 Twig 模板。

要开始在你的项目中使用 Memio，只需将它添加到`composer.json`文件中，正如在[文档](http://memio.github.io/memio/)中所写的。负责生成代码的核心类——`PrettyPrinter`——需要一个`Twig_Environment`实例作为构造函数参数。它应该按以下方式初始化:

```
$twigLoaderFilesystem = new Twig_Loader_Filesystem('vendor/memio/memio/templates');
$twigEnvironment = new Twig_Environment($twigLoaderFilesystem, []);

$memioPrettyPrinter = new \Memio\Memio\PrettyPrinter($twigEnvironment);
```

要用 Memio 生成一些 PHP 代码，您必须创建表示特定代码部分的对象，然后只需将它们传递给`PrettyPrinter`实例，您就会得到输出代码。代表自动生成代码的每个对象都是一个 Memio [`model`类](http://memio.github.io/memio/doc/01-model-tutorial.html)的实例。要定制输出代码，您需要在这些实例上调用特定的方法，Memio 库将在打印时完成剩下的工作。向方法添加主体，设置属性的可见性，设置类实现的接口——所有这些都是通过调用适当的方法来完成的。下面是一个生成用户类的示例:

```
$class = \Memio\Memio\Model\Object::make('User');

$nameProperty = \Memio\Memio\Model\Property::make('name');
$class->addProperty($nameProperty);

$getNameMethod = \Memio\Memio\Model\Method::make('getName')->setBody('return $this->name');
$class->addMethod($getNameMethod);

echo $memioPrettyPrinter->generateCode($class);
```

这将产生以下输出:

```
class User
{
    private $name;

    public function getName()
    {
        return $this->name;
    }
}
```

## 真实世界的例子:对象关系映射

当将数据库结构映射到 PHP 类时，经常使用自动生成 PHP 代码。数据库中的每个表都可以用 PHP 应用程序中的一个单独的类来表示。那么每个表列将由一个类属性来表示。让我们基于 MySQL 数据库结构，尝试编写一个简单的脚本来创建这样的类。

从我们的脚本开始，我们需要获取一个选定数据库中的表列表。我们还需要每个表中的列列表。为了获得所有这些数据，我们必须使用两个 MySQL 命令:开始时使用`SHOW TABLES`，然后对第一个查询中返回的每个表使用`DESC <table name>`。

下一步是为每个表生成一个单独的 PHP 类。类名将与表名相同，只是以大写字母开头:

```
foreach($tableNames as $table) {
    $class = new \Memio\Memio\Model\Object(ucfirst($table));

    //...
}
```

为了使我们的类更有用，我们将添加表示每个表列的类属性。假设我们将列名存储在一个平面数组中，代码如下所示:

```
foreach($columnNames as $column) {
    $property = \Memio\Memio\Model\Property::make($column);
    $class->addProperty($property);
}
```

还有……就这些了！要获得该类的输出代码，只需将`$class`变量传递给 Memio `PrettyPrinter@generateCode`方法:

```
$code = $memioPrettyPrinter->generateCode($class);
```

这让我们可以为数据库中的所有表自动生成类。

## 扩展模型生成器

上面的例子只是对使用 Memio 的简单介绍。为了使我们的类更有用，我们可以用许多方法来扩展它们。首先，让我们为每个属性生成 getters 和 setters。我们对表中列名的循环将如下所示:

```
foreach($columnNames as $column) {
    $property = \Memio\Memio\Model\Property::make($column);
    $class->addProperty($property);

    $getter = Method::make('get' . ucfirst($column))->setBody('return $this->' . $column . ';');
    $class->addMethod($getter);

    $setterArgument = Argument::make('string', $column);
    $setter = Method::make('set' . ucfirst($column))->addArgument($setterArgument)->setBody('$this->' . $column . ' = $' . $column . ';');
    $class->addMethod($setter);
}
```

如你所见，我们创建了两个变量来实例化 Memio `Method`类:`$getter`和`$setter`。将要生成的方法的名称是`get<Column>`和`set<Column>`。由于 setter 方法需要一个参数，我们需要创建 Memio `Argument`类的一个实例。然后我们通过调用`$setter`变量上的`addArgument()`将它传递给我们的 setter 方法。下一步是将主体添加到 getter 和 setter 方法中，只需调用`setBody()`方法。最后，我们通过调用`$class`变量上的`addMethod()`将这些方法添加到类中。

上面的例子展示了使用 Memio 的一个重要方面。请注意，我们总是将代表小部分代码的对象传递给更高级别的对象。首先是方法参数(`Argument`类)。然后我们创建一个方法(`Method`类)并将参数添加到方法中(`$method->addArgument()`)。方法应该放在一个类中，所以我们创建一个类(`Object`类)并将方法添加到类(`$class->addMethod()`)。所以一般的想法是从代码的小部分开始，将它们链接到更高层次的容器。

为了表示 Memio 中的整个代码结构，您还可以将输出类(`Object`类)放在一个文件中(`File`类的实例)。在 Memio 脚本中包含 File 类允许您生成带有名称空间声明、许可信息和输出开头的 PHP 开始标记的代码。参见[文档](http://memio.github.io/memio/doc/01-model-tutorial.html)来检查它是如何实现的，并尝试自己添加正确的代码。要获得基于上述示例的整个应用程序，只需检查与文章相关的 [Github repo](https://github.com/jacek-b/memiomodelgenerator) 。

## 后续步骤

在我们的例子中，我们只创建了一个简单的类生成器，它将数据库表映射到对象中。示例代码可以扩展成更高级的脚本。例如，您可以使用 MySQL 中关于列类型的信息，并验证传递给 setter 方法的变量。下一步，您可以通过将对象实例转移到 MySQL `INSERT`或`UPDATE`语句中，生成将对象持久化到数据库中的代码。

还要记住，可以通过更改默认模板来修改基于 Memio 的脚本的输出。例如，如果您想要生成符合项目中使用的编码标准的代码，您需要做的就是在模板中进行更改。然后，所有自动生成的代码都将基于您自己的编码风格和约定生成。模板文档包含了如何用你自己的文件替换默认模板的所有细节。

我鼓励你自己编写一些自动生成的脚本来检查 Memio 库的所有特性。在下面的评论中分享你的结果和想法。玩得开心！

## 分享这篇文章