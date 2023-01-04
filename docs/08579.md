# 理解观察者模式

> 原文：<https://www.sitepoint.com/understanding-the-observer-pattern/>

最近，我被要求将第三方论坛软件集成到现有的门户网站中。问题是，两个应用程序都有自己独立的身份验证机制。从用户的角度来看，用户登录门户而不必单独登录论坛是理想的。在这种情况下，我不想对论坛代码进行不必要的修改，因为这将产生一个维护噩梦——我必须合并来自供应商的任何后续更新和错误修复，并注意不要覆盖我自己的修改。这就是观察者模式派上用场的地方。

在本文中，我将向您展示如何实现观察者模式。您将了解到模式中的各种类作为主体和观察者是如何相互关联的，主体如何通知观察者其状态的变化，以及如何识别适合在您自己的代码中使用观察者模式的场景。

## 观察者模式

观察者模式(也称为发布-订阅模式)是一种行为设计模式，它定义了对象之间的一对多关系，这样，当一个对象改变其状态时，所有依赖对象都会得到通知并自动更新。在我的例子中，我使用这种模式将门户的认证机制与论坛软件联系起来。登录门户的行为也自动触发用户登录论坛。

与对其状态感兴趣的其他对象具有一对多关系的对象称为*主体*或*发布者*。它的依赖对象被称为*观察者*或*订阅者*。只要对象的状态发生变化，观察者就会收到通知，并采取相应的行动。主题可以通知任意数量的依赖观察者，任意数量的观察者可以订阅主题来接收这样的通知。

## 观察者类

`Observer`类提供了一个`update()`方法，主题将调用这个方法来通知它主题的状态变化。在这个例子中，我将`update()`方法定义为一个具体的方法。如果您愿意，可以将这里的方法定义为`abstract`，然后在 observer 的子类中为它提供一个具体的实现。

```
<?php
abstract class Observer
{
    public function __construct($subject = null) {
        if (is_object($subject) && $subject instanceof Subject) {
            $subject->attach($this);
        }
    }

    public function update($subject) {
        // looks for an observer method with the state name
        if (method_exists($this, $subject->getState())) {
            call_user_func_array(array($this, $subject->getState()), array($subject));
        }
    }
}
```

`__construct()`方法接受一个可观察主体的实例，并将其自身附加到该主体上——我马上就要谈到这一点。`update()`方法检索主题的当前状态，并使用它调用与状态同名的子类 observer 方法。

因此，在我的特殊情况下，我需要使门户现有的`Auth`类成为一个可观察的 subject，并创建一个具体的 observer 子类来挂钩论坛的认证代码。我的子类也需要使用主体的状态来实现方法。

## 主题类

Subject 类也是一个抽象类，它定义了四个主要方法:`attach()`、`detach()`、`setState()`和`notify()`。为了方便起见，我还在这里添加了`getState()`和`getObservers()`方法。

```
<?php
abstract class Subject
{
    protected $observers;
    protected $state;

    public function __construct() {
        $this->observers = array();
        $this->state = null;
    }

    public function attach(Observer $observer) {
        $i = array_search($observer, $this->observers);
        if ($i === false) {
            $this->observers[] = $observer;
        }
    }

    public function detach(Observer $observer) {
        if (!empty($this->observers)) {
            $i = array_search($observer, $this->observers);
            if ($i !== false) {
                unset($this->observers[$i]);
            }
        }
    }

    public function getState() {
        return $this->state;
    }

    public function setState($state) {
        $this->state = $state;
        $this->notify();
    }

    public function notify() {
        if (!empty($this->observers)) {
            foreach ($this->observers as $observer) {
                $observer->update($this);
            }
        }
    }

    public function getObservers() {
        return $this->observers;
    }
}
```

`attach()`方法为观察者订阅主题，这样状态的任何变化都可以传递给它。`detach()`方法取消了观察者对主题的订阅，因此它不再观察主题的状态变化。

`setState()`方法设置主题的当前状态，并调用`notify()`来更新观察者，即向每个观察者发布通知。`notify()`方法通过遍历内部列表并调用每个成员的`update()`方法来更新每个订阅的对象。

`getState()`和`getObservers()`方法只是返回当前主题状态和观察者列表的辅助函数。

## 仔细观察……把它结合在一起

现在有了观察者和主题的抽象基类，我就能够将论坛软件集成到现有的 web 门户中了。我需要使门户的`Auth`类成为一个可观察的主题，并在用户登录或退出门户时设置它的可观察状态。

```
<?php
class Auth extends Subject
{
    function login() {
        // existing code to perform login authentication
        // ...

        // signal any observers that the user has logged in
        $this->setState("login");
    }

    function logout() {
        // existing code to perform some operation when user logs out
        // e.g. destroy session, etc...

        // signal any observers that the user has logged out
        $this->setState("logout");
    }
}
```

我扩展了`Subject`类，这样`Auth`就可以被观察到，然后在`login()`和`logout()`方法中添加了对`setState()`的调用。

为了子类化`Observer`，我创建了一个`Auth_ForumHook`类，负责调用论坛的 API 登录和注销函数。

```
<?php
class Auth_ForumHook extends Observer
{
    function login($subject) {
        // call the forum's API functions to log the user in
        // ...
    }

    function logout($subject) {
        // call the forum's API functions to log the user out
        // ...
    }
}
```

在代码库中的其他地方，门户的`Auth`类正在被实例化，我给它附加了一个`Auth_ForumHook,`实例，这样观察者就会被通知到`Auth`中的任何状态变化。

```
<?php
$auth = new Auth();
// attach an observer
$auth->attach(new Auth_ForumHook());
...
```

这就是我额外的编码需求——除了准备抽象的`Observer`和`Subject`类。由`Auth`的`login()`和`logout()`方法触发的任何状态变化都会通知`Auth_ForumHook`观察者，并自动让用户登录或退出论坛。

要添加新的观察者，比如一个登录跟踪器来记录用户何时登录或退出门户，只需提供一个具体的`Observer`类并将其附加到`Auth`主题，而无需进一步修改现有的`Auth`对象的`login()`和`logout()`方法。

## 摘要

观察者模式是一种合适的设计模式，适用于这样的情况:您有几个依赖于另一个对象的对象，当该对象的状态改变时需要执行某个操作，或者一个对象需要通知其他人，但不知道他们是谁或有多少人。

在本文中，我向您展示了基本的主体-观察者模式，并提供了具体的例子，说明如何使用这种行为模式来轻松扩展现有类的功能，而无需紧密耦合任何新的需求。这种模式使您能够在相关和依赖对象之间实现更高水平的一致性，而不会牺牲代码的可重用性。

<small>图片经由 [JPF](http://www.shutterstock.com/gallery-873394p1.html) / [快门](http://www.shutterstock.com)</small>

## 分享这篇文章