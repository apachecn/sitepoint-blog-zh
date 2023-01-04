# Yii 的组件架构，第 2 部分

> 原文：<https://www.sitepoint.com/yii-under-the-hood-2/>

再次欢迎来到 Yii 框架的`CComponent`类之旅。这个由三部分组成的系列演示了 Yii 如何使用基于组件的架构，以及该类如何实现属性、配置、事件和行为。在第 1 部分中，你看到了 Yii 如何使用 PHP 的神奇方法来实现组件属性。本文是本系列的第 2 部分，我将向您展示如何用 PHP 进行基于事件的编程。我还将向您展示组件类是如何实现这一点的。

## 事件

应用程序事件是其他代码可能感兴趣的事件。大多数 GUI 应用程序中的标准事件是“点击”事件，但是没有限制，你定义什么事件完全取决于你自己。例如，当一个用户注册时，你经常想给她发一封电子邮件，欢迎她加入你的应用程序。您可以将“注册完成”定义为一个事件。该模块可以简单地引发一个事件并忽略任何特定的实现细节，而不是硬编码电子邮件逻辑，甚至在代码中设置不同的用户属性。细节可以由特定于应用程序的模块提供，这允许您将单独的需求与可重用的代码分开。事件允许您在不改变核心模块和组件的情况下附加无限的功能。

实施事件通常有三个步骤:

1.  定义事件
2.  将自定义功能附加到事件
3.  当事件发生时触发事件

### 定义事件
首先你需要定义允许 Yii 附加功能的事件。继续以用户注册为例，您将创建一个新的事件，名为`onUserRegistered`，它在用户成功注册后被引发。这将在 my your user 模块的代码中定义。

```
<?php
public function onUserRegistered($event) {
    $this->raiseEvent("onUserRegistered", $event);
}
```

Yii 中的事件是通过简单地在方法名前添加一个前缀为“on”的函数来定义的。为了将一个函数附加到一个对象上，你需要能够从应用程序的其他区域访问该对象。该事件被添加到用户组件中，这样就可以通过`Yii::app()->user`在整个应用程序中访问它。

### 触发事件

用户模块的控制器类将负责在事件实际发生时触发事件。

```
<?php
public function actionUserRegister() {
    // code to register a user and add to the database
    // ...
    // user has been successfully registered so lets raise an event to tell the world
    $e = new CEvent($this, array("user" => $user));
    Yii::app()->user->onUserRegistered($e);
}
```

一旦用户成功注册并添加到您的数据库中，您就会想要告诉全世界。这段代码引发事件。事件不知道它们调用的函数的具体细节，所以所有的处理函数必须有相同的接口。在 Yii 中，所有的处理函数都需要一个参数，这个参数是一个`CEvent`对象。`CEvent`对象需要两个参数:一个是对引发事件的对象的引用，另一个是可选的参数数组，用于存储与特定事件相关的特定信息(这些参数可以在以后由您的处理函数访问)。在这个例子中，我希望所有的处理函数都能够访问刚刚注册的用户对象。为此，我传递了一个`array("user" => $user)`数组，其中`$user`是一个表示我们最近注册的用户的对象。然后，我触发事件，通过调用函数`onUserRegistered()`并传入事件对象来调用 Yii 的`raiseEvent()`函数。

这就是需要添加到用户模块的所有代码。现在，应用程序中任何地方的任何附加代码都可以附加附加功能，以便在新用户注册时执行。

### 附加事件

继续这个例子，让我们看看如何附加一个事件的回调。

```
<?php
public function init() {
    Yii::app()->user->onUserRegistered = array($this, "sendMyEmail");

}

public function sendMyEmail($event) {
    $user = $event->params["user"];
    mail($user->email, "Welcome to this amazing event app", "Hello...");
}
```

我已经将`sendMyEmail()`方法附加到了`onUserRegistered`事件上，所以现在当一个新用户注册时，将会调用`sendMyEmail()`函数。或者，在 PHP 5.3 或更高版本中，您可以指定一个匿名函数。Yii 支持给一个事件赋值，如果传递给 PHP `is_callable()`函数，这个事件也将返回 true。

## 阿一的魔法

现在我们来看看 Yii 是如何实现事件的；管理事件都是由`CComponent`类中的小聪明处理的。为了实现事件，组件类必须实现三个主要概念:

*   定义事件–Yii 需要存储或能够查找定义的事件。
*   触发事件——当事件发生时，Yii 需要调用所有与事件相关的 PHP 函数。
*   附加事件——Yii 需要一种机制来存储针对事件的有效 PHP 回调列表。

Yii 中定义事件的机制是简单地创建一个带有“on”前缀的函数，就像前面看到的那样。触发事件的机制是在定义的事件中添加`$this->raiseEvent("onMethodName");`,并在代码中事件发生时简单地调用方法。这给我们留下了两个实现细节:

*   将函数附加到事件。
*   当事件被触发时，调用附加到事件的所有函数。

要附加事件，您使用代码`onMyEventName = callback`，这意味着将函数附加到事件的实现必须在组件魔术`__set`方法中处理。

```
<?php
public function __set($name, $value){
    if (strncasecmp($name, "on", 2) === 0 && method_exists($this, $name)) {
        $name = strtolower($name);
        if (!isset($this->_e[$name])) {
            $this->_e[$name] = new CList();
        }
        return $this->_e[$name]->add($value);
    }
}
```

该实现首先检查`$name`的值是否以文本“on”开始，以及是否存在与该值同名的方法。如果是的话，Yii 假设`$value`是一个回调的表示，它需要将这个回调附加到由`$name`定义的事件上。Yii 有一个私有成员变量`$_e`，它保存了一个由事件名称作为键的回调数组，它只是将回调添加到特定事件键的列表中。

```
$_e => array(
    'onUserRegistered' => array(
        0 => array(object, 'sendMyEmail')
    ),
    'onSomeOtherEvent'=>array(
        0 => function(){}
        1 => ...
    )
)
```

现在剩下的就是当事件被触发时调用附属的函数。通过查看存储事件的数组，这可能就像在`$_e`变量中查找事件一样简单，遍历数组中的每个回调并运行它。

```
public function raiseEvent($name, $event) {
    foreach ($this->_e[$name] as $handler) {
        if (is_array($handler)) {
            // an array: 0 - object, 1 - method name
            list($object, $method) = $handler;
            $object->$method($event);
        }
        else {
            // PHP 5.3+ anonymous function
            call_user_func($handler, $event);
        }
    }
}
```

我已经重构了`raiseEvent()`方法，使它更容易阅读我们的演示，但是 Yii 实现有更多的错误检查和处理更多的回调类型，比如静态方法。

## 摘要

对于健壮、灵活的代码来说，事件是一个很好的实现概念。在本文中，您学习了如何创建可重用的组件，这些组件可以通过使用事件来扩展它们的功能，并且看到了 Yii 框架的`CComponent`类如何在 PHP 中实现和管理事件。当然，使用和实现细节可能是 Yii 特有的，但是这个概念本身是通用的。

这就结束了我们这个三部分系列的第二篇文章。最后一篇文章介绍了行为的创建和实现，这是在保持组件易于重用的同时扩展组件功能的另一个好方法。

图片 via [菲利普丘克奥列格瓦西洛维奇](http://www.shutterstock.com/gallery-81464p1.html)/[Shutterstock](http://www.shutterstock.com)

## 分享这篇文章