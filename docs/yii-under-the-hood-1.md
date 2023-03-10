# Yii 的组件架构，第 1 部分

> 原文：<https://www.sitepoint.com/yii-under-the-hood-1/>

围绕框架的使用已经有一段时间了，现在有许多很好的 PHP 框架可供选择。Yii 框架中基本组件类的简单性和强大功能让我惊叹不已。这个班是做什么的？

*   **属性**–向类添加 getter 和 setter 属性
*   **配置**–支持优雅的类和应用级级联配置系统
*   **事件**–增加了引发和调用自定义事件的能力
*   **行为**–增加使用行为的能力(也称为混合)

框架中的每个类都从`CComponent`类扩展而来，这意味着所有子类都作为组件工作，可以引发和处理事件，并且是可重用和可配置的。对于这样一个小班级来说，这很有冲击力！

我想和你们分享 Yii 框架的核心组件，我把对`CComponent`类的介绍分成了三篇文章。在这篇文章中，我将讨论 Yii 如何利用 PHP 魔术方法来创建类属性和一个优雅的配置系统。在下一篇文章中，我将讨论如何使用基于事件的编程，以及 Yii component 类如何实现这一点来简化事件的引发和处理。最后一篇文章将介绍使用行为(也称为 mix-in)，它们是什么，如何使用它们，以及 Yii 如何实现它们。

让我们开始我们的第一个话题。

## 性能

属性定义了类的可配置方面；它公开了类的接口，使你能够操纵它来完成你的命令。例如，图像上传程序类的属性可能是“文件路径”和“允许的最大文件大小”。这些属性可以通过编写 setter 和 getter 方法来公开和操作，比如`setFilePath()`和`getFilePath()`，或者简单地在类上添加一个公共属性，比如`public $filePath`。

然而，这有一个小问题。如果将属性定义为公共变量，则应用程序中可能会有许多区域可以直接引用该属性，并且您无法控制应用程序的其他部分可以为其分配什么。当设置`filePath`属性时，我可能想先检查它是否是一个有效的路径，如果不是，就抛出一个错误消息。为了做到这一点，我需要创建一个`setFilePath()`方法。使用`$uploaderObject->filePath = "/file/path/"`并不能解决问题。

Yii 的组件类实现了神奇的`__get()`和`__set()`函数，它们实现了一个极其简单的约定，允许你创建 getter 和 setter 方法，但仍然可以像访问公共变量一样访问属性。基本上，Yii 假设一个以“get”或“set”开头的方法引用一个属性，该属性与不带“get”或“set”前缀的方法同名。这使您可以在以后轻松地扩展代码。当你第一次开始构建你的类时，你可以继续添加你的公共变量；不用担心添加 getter 和 setter 方法。然后，您可以添加 getter 和 setter 方法，并将原始属性设为私有，但您不必重构其余代码。

```
<?php
echo $uploaderObject->filePath;
```

会打电话给:

```
<?php
echo $uploader->getFilePath();
```

并且使用…

```
<?php
$uploaderObject->filePath = "my/path";
```

会打电话:

```
<?php
$uploaderObject->setFilePath("my/path");
```

当然，如果你愿意，你也可以直接调用`getFilePath()`或`setFilePath()`。

让我们看看 Yii 是如何做到这一点的。

## 阿一的魔法

深入 Yii 的`CComponent`类，你会发现这种魔法的两种魔法方法。先说第一个`__get()`函数。

```
<?php
public function __get($name){
    $getter = "get" . $name;
    if (method_exists($this, $getter)) {
        return $this->$getter();
    }
    throw new CException("Property '$name' is not defined.");
}
```

当你调用`echo $uploader->filePath;`时，PHP 寻找公共的`filePath`属性。如果找不到它(或者它是私有的)，PHP 将委托给神奇的方法`__get()`。PHP 调用`__get()`函数并传递给它属性的名称。在这个例子中，`$name`将存储值“文件路径”。然后 Yii 执行以下操作:

*   将文本“get”添加到`$name`变量的值前，并将其赋给`$getter`变量，使`$getter`等于“getfilePath”(记住，PHP 中的函数名不区分大小写)。
*   检查该对象中是否定义了名为`getfilePath()`的方法。
*   如果方法存在，则调用该方法。在这个例子中，代码`$this->$getter();`实际上是对`$this->getfilePath()`的调用。
*   如果找不到方法，就会抛出一个异常，抱怨说，你猜对了，属性找不到了。

同样的过程也适用于神奇的`__set()`函数。当你给`setFilePath`赋值时，如下所示:

```
<?php
$uploader->filePath = 'my/path';
```

PHP 首先搜索公共属性。如果不存在(或者是私有的)，PHP 调用神奇的`__set()`函数。`__set()`函数的工作方式与`__get()`相似，尽管 PHP 也传递您正在设置的值，所以在示例中`$value`会存储“my/path”。

```
<?php
public function __set($name, $value) {
    $setter = "set" . $name;
    if (method_exists($this, $setter)) {
        return $this->$setter($value);
    }

    if (method_exists($this, "get" . $name)) {
        throw new CException("Property '$name' is read only.");
    }
    else {
        throw new CException("Property '$name' is not defined.");
    }
}
```

`__set()`功能的实现:

*   将`$name`的值加上“set ”,并将其分配给`$setter`变量，该变量成为“setfilePath”。
*   检查是否存在名为`setfilePath`的方法。
*   如果方法存在，那么就调用 like `$this->setfilePath("my/path");`。
*   如果`set`方法不存在，那么检查属性是否有 getter 方法。如果有，则该属性是只读的，并且会引发一个异常来说明这一点。如果没有 getter 和 setter 方法，则会抛出一个异常，指出该属性不存在。

Yii 只用了几行代码就实现了一个非常好的属性系统，它基于 PHP 神奇的`__get()`和`__set()`函数。

## 配置

使用这种方法定义属性的另一个好处是，如果您愿意，可以很容易地使用数组来配置您的类:

```
<?php
$config = array(
    "myProperty" => 1234,
    "anotherProperty" => 5678);
```

只需执行以下操作，就可以用上述数组配置组件类:

```
<?php
foreach ($config as $property => $value) {
    $this->$property = $value;
}
```

代码使用数组键作为属性，将它们设置为等于数组中定义的值。这非常简单，也是 Yii 自己处理应用程序级配置的方式。下面是一个非常基本的演示 Yii 配置文件的示例:

```
<?php
return array(
    "name" => "My Amazing App",
    "timezone" => "Europe/London",
    "components" => array(
        "db" => array(
            "username" => "root",
            "password" => ""
        ),
        "user" => array(
            "allowAutoLogin" => true
        )
    )
);
```

数组被传递给 Yii 应用程序类，然后该类遍历每个配置键。应用程序类必须具有为“名称”、“时区”和“组件”定义的属性。

```
<?php
class MyApplication extends CComponent
{
    public $name;
    public $timezone;

    public function setComponents(array $components) {
        // handle the array of components
    }
}
```

components 键调用一个需要数组的`setComponents()`函数。这个函数加载每个类，并传递给它自己的配置数组，如此类推，直到所有的配置属性都设置好为止。它非常快，完全没有干扰，并且不需要分散在代码中的单独的配置方法。

如果需要，以后您可以使用 setter 和 getter 方法来扩展您的类。

```
<?php
class MyApplication extends CComponent
{
    private $name;
    private $timezone;

    public setName($name) {
        // do something with the $name
    }

    public setTimezone($timezone) {
        // do something with $timezone
    }

    // TODO: Add getter functions 

    public function setComponents(array $components) {
        // handle the array of components
    }
}
```

## 摘要

在本文中，我简要介绍了如何在 Yii 的组件类中使用魔法方法来创建属性和一个简单而强大的配置系统。当然 Yii 不会就此罢休。它还实现了神奇的`__isset()`和`__unset()`方法，这意味着您可以使用`isset()`来确定一个属性是否有值，并使用`unset()`来销毁该属性。

在第 2 部分中，我将讨论事件，这是在基于组件的架构中创建高度可重用代码的另一个关键原则。我将向您展示如何在 PHP 中使用基于事件的编程，以及 Yii 如何使组件的子类能够引发和处理事件。

图片 via [菲利普丘克奥列格瓦西洛维奇](http://www.shutterstock.com/gallery-81464p1.html)/[Shutterstock](http://www.shutterstock.com)

## 分享这篇文章