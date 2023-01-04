# Yii 的组件架构，第 3 部分

> 原文：<https://www.sitepoint.com/under-the-hood-of-yiis-component-architecture-part-3/>

欢迎阅读这个由三部分组成的 Yii 框架组件类旋风之旅的最后一篇文章。本系列的目标是向您展示 Yii 如何实现基于组件的架构，以及它的`CComponent`类如何通过巧妙利用 PHP 的神奇方法来处理实现细节。

在第一篇文章中，您了解了 Yii 如何实现属性和一个简单但有效的配置系统。在第二篇文章中，您学习了如何在项目中实现基于事件的编程，以及 Yii 如何使用神奇的方法来声明和绑定事件。在这篇文章中，我想重点介绍如何在运行时使用行为来动态修改对象的功能。

Yii 中所谓的*行为*，是一种在运行时组合对象以扩展对象功能的方式。行为是分离代码并保持不断扩展的系统可维护性的一种极好的方式。

在 PHP 对象模型中，您可以通过扩展基类来重用功能，新类继承了父类的功能和属性。继承是一个很好的编程工具，但是在复杂的系统中，它会很快变得受限。当您有两个或更多的类，并且它们的功能与一个新的类相关时，会发生什么呢？我们不能有效地重用我们的代码，因为 PHP 不支持多重继承。Yii 行为系统是通过实现 [mixins](http://en.wikipedia.org/wiki/Mixin) 实现多重继承的一种方式。

## 构建用户的一部分

我们举个简单的例子。您正在构建的新应用程序要求用户连接到第三方帐单和信用卡管理服务。您可以将该功能转储到一个通用的用户类中，但是不久之后，您的用户类就会变得非常庞大和混乱。相反，最好是隔离功能，然后只在使用时才添加到用户对象中。但是怎么做呢？

啊哈！我知道！让我们给行为添加功能！我打赌你没想到会这样。

首先我们创建一个`UserAccountBehavior`类。行为需要从`CBehavior`基类扩展，`CBehavior`从`CComponent`扩展，意味着行为本身也可以有行为、事件和属性——但是我们不要太得意忘形。

```
<?php
Class UserAccountBehavior extends CBehavior 
{
    public function getAccountInfomation() {
        // connect to a service to get credit card details and payment info
        return $paymentService->getDetails($this->owner->id);
    }
}
```

在行为内部，`$this->owner`返回该行为已经附加到的对象。因为你将只把这个行为附加到用户对象，你知道`$this->owner`将是一个用户对象(在 Yii 中是`CWebUser`类的一个实例)。因此，您可以使用`$this->owner->id`访问用户 ID。

现在，您需要在运行时将行为附加到用户对象。在 Yii 中，`CWebUser`类代表登录的用户。Yii 创建了一个`CWebUser`的静态实例，并将其存储在应用程序的`user`属性中，您可以使用`Yii::app()->user`来引用它。

```
<?php
$user = Yii::app()->user;
$user->attachBehavior("account", new UserAccountBehavior());
echo $user->getAccountInformation();
```

现在，首先使用`Yii::app()->user`检索用户对象，然后调用`attachBehavior()`方法并传入要附加的行为对象。用户对象现在可以访问由行为定义的附加方法。在这个例子中，`getAccountInformation()`在用户对象上被调用，用户对象又调用了`UserAccountBehavior`对象的`getAccountInformation()`方法。

您还可以使用`$user->account`来访问`UserAccountBehavior`对象本身，因此`$user->account->getAccountInformation()`将与调用`$user->getAccountInformation()`相同。

## 利用行为和事件的乐高积木

行为最有用的一个很好的例子是与活动记录模式相结合。使用行为，您可以通过在一系列块中构建行为来定义运行时记录行为。

例如，表示博客文章的活动记录类可能需要有几个类似的东西。您可能希望它具有“可标记”的行为，以便用户可以添加标记并使用它们搜索记录。一个可标记的行为可能会提供一个易于使用的`findAllByTags()`、`getTags()`和`setTags()`方法。

您可能还需要一个软删除行为，这样当用户删除一个记录时，会添加一个已删除标志，但该记录不会从数据库本身删除，以保留历史数据。该行为可以与其父对象(在本例中是 post 记录)交互，并修改标准的删除功能，但它也可能公开一个新的`deleteForever()`方法来永久删除记录。

这里值得注意的是，行为不能像通常的类继承那样覆盖其父类中的方法，但是您可以编写代码来允许事件处理这种情况。例如，在运行类的默认删除方法之前，您可以首先检查删除是否已经被处理。如果有，您知道某处的某个代码(可能在某个行为中)已经处理了删除，并且一切都在控制之下。如果您回想一下本系列的第二部分，您会看到我在引发事件时传递了一个`CEvent`对象；这个对象有一个 handled 属性，在这里你可以检查它是真还是假。例如:

```
<?php
public function beforeDelete() {
    $event = new CEvent;
    // raises and calls events attached to "onBeforeDelete"
    $this->onBeforeDelete($event);
    if ($event->handled) {
        return true;
    }
    else {
        return $this->_delete();
    }
}
```

现在，对于软删除行为，您可以附加 onBeforeDelete()方法，在数据库中设置 deleted 属性，并将事件对象的`handled`属性设置为 true。

Yii 提供了一个`CActiveRecordBehavior`行为，它将方法公开并自动绑定到以下事件:`onBeforeSave`、`onAfterSave`、`onBeforeDelete`、`onAfterDelete`、`onBeforeFind`和`onAfterFind`。为活动记录对象创建行为时，可以从此类扩展。注意，您还可以操作`beforeFind()`和`afterFind()`方法来修改检索查询标准，以防止被标记为删除的记录被返回。

另一个例子可能是，您希望这个记录表示树中的节点，这样您就可以创建帖子的层次结构；您可以添加一个树行为来访问其他方法，如`addChild()`、`getChildren()`、`getAncestors()`等。行为的列表可以一直持续下去，但最好的部分是这些行为可以存储为单独的文件，就像小乐高积木一样，使您可以非常快速地从现有代码中构建具有高级功能的对象。

## 阿一的魔法

现在我将直接进入 Yii 如何创建它的行为系统的实现细节。这将使您了解正在发生的事情，以便您可以更好地理解如何在自己的编程中使用行为。您还将看到如何在自己的框架或项目中实现自己的行为系统。

行为系统需要以下要素:

*   将对象附加为行为的方法
*   存储对象上所有附加行为的方法
*   一些神奇的方法来判断功能是否被委托给一个附加的行为对象
*   一种移除行为的方法

让我们从附加行为的方法开始:

```
<?php
public function attachBehavior($name, $behavior) {
    $behavior->attach($this);
    return $this->_m[$name]=$behavior;
}
```

这个函数附加一个行为是相当简单的；它调用行为的 attach()方法，该方法由 Yii 中的`CBehavior`基类定义。它接收一个指向当前对象`$this`的指针，以允许行为跟踪它附加到哪个对象。

在所有行为都必须扩展的`CBehavior`类中是`attach()`方法的定义:

```
<?php 
public function attach($owner) {
    $this->owner = $owner;
}
```

正如我前面提到的，这允许您在行为对象中使用`$this->owner`来引用它的所有者(它当前附加到的对象)。

接下来，需要一个存储所有附加行为的方法。这实际上已经在代码为`$this->_m[$name] = $behavior`的`attachBehavior()`方法中演示过了。要存储行为列表，您只需将它们保存到一个以行为名称为键的关联数组中。现在你可以将行为附加到子类`CComponent`的任何对象上。

现在需要一种方法来实现与这些行为的交互，并在必要时调用正确的方法。PHP magic `__call`方法非常适合这一点。

```
<?php
public function __call($name,$parameters) {
    if ($this->_m!==null) {
        foreach($this->_m as $object) {
            if (method_exists($object,$name)) {
                return call_user_func_array(array($object, $name), $parameters);
        }
    }
    throw new Exception(get_class($this) . " and its behaviors do not have a method named '" . $name . "'");
}
```

当调用一个对象的未定义方法时，PHP 会调用神奇的`__call`方法。被调用方法的名称作为`$name`参数传入，方法调用中的参数作为`$parameters`传入。现在所有代码需要做的就是简单地循环所有附加的行为(存储在`$this->_m`中)并检查它们是否包含与刚刚调用的方法同名的方法。如果该方法确实存在于某个行为中，则调用该方法并返回结果。如果在任何行为中找不到匹配的方法，就会抛出一个异常，对此进行抱怨。

简而言之，就是这样——你自己的行为系统。这些例子主要基于 Yii 框架的`CComponent`类中的代码，为了本文的目的，我对它们进行了适当的简化，但是您可以随意下载该框架并亲自阅读。

## 摘要

我们一起踏上了探索更好地封装代码和制作可重用组件的旅程。Yii 的组件类显示了在 PHP 中相对容易地实现非常复杂和巧妙的模式是多么容易，从简单的属性和配置到事件和行为。

我希望您喜欢这个由三部分组成的系列，并且现在您的编程工具箱中又多了一些工具。如果您以前从未在编程中使用过事件或行为，那么绝对值得尝试一下这些想法。与大多数编程一样，这些概念只有在您亲自尝试并将其应用于现实世界的情况下才能完全理解。

## 分享这篇文章