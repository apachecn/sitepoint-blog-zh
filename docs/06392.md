# PHP 中的集合类

> 原文：<https://www.sitepoint.com/collection-classes-in-php/>

集合类是传统数组数据结构的 OOP 替代品。与数组非常相似，集合包含成员元素，尽管这些元素往往是对象，而不是简单的类型，如字符串和整数。

集合类的一般特征是:

*   在对象数组周围建立包装。
*   集合是可变的——可以添加新元素，也可以修改或删除现有元素。
*   排序算法是不稳定的(这意味着相等元素的顺序是不确定的)。
*   可以使用惰性实例化来节省系统资源。

## 数组问题

应用程序经常有包含一组其他对象的对象，这是利用集合的好地方。例如，假设我们决定创建一个书店系统。假设我们已经编写了一个 customer 类，其中包含了客户想要购买的书籍列表:

```
<?php
class Customer 
{
    public $items = array();
    // ...
}

$customer = new Customer(1234);
foreach ($customer->items as $item) {
    echo $item->name;
}
```

如果最明显的方法(使用数组)是最好的方法，我不会写这篇文章。上面的例子有这些问题:

*   我们打破了封装——数组被公开为一个公共成员变量。
*   在索引以及如何遍历数组来查找特定项方面存在模糊性。

此外，为了确保数组对任何可能访问它的代码都可用，我们必须在填充客户信息的同时填充数据库中的信息列表。这意味着，即使我们只想打印客户的姓名，我们也必须获取所有的商品信息，这不必要地增加了数据库的负载，并有可能使整个应用程序陷入瘫痪。

我们可以通过创建一个集合类作为数组的 OOP 包装器并使用惰性实例化来解决这些问题。惰性实例化是一种机制，通过这种机制，我们只在实际需要时才在数组中创建元素。之所以称之为“懒惰”，是因为对象自己决定何时实例化组件对象，而不是在实例化时盲目地创建它们。

## 基本集合类

集合类需要公开允许我们添加、检索和删除项目的方法，拥有一个让我们知道集合大小的方法很有帮助。所以，一个基本的类会像这样开始:

```
<?php
class Collection 
{
    private $items = array();

    public function addItem($obj, $key = null) {
    }

    public function deleteItem($key) {
    }

    public function getItem($key) {
    }
}
```

`$items`数组提供了一个存储集合成员对象的位置。`addItem()`让我们向集合中添加一个新对象，`deleteItem()`移除一个对象，`getItem()`返回一个对象。

使用`addItem()`，我们将一个对象添加到集合中，方法是将它放在`$items`数组中由`$key`指定的指定位置(如果没有提供键，我们让 PHP 选择下一个可用的索引)。如果试图使用已经存在的键添加对象，则应引发异常，以防止无意中覆盖现有信息:

```
public function addItem($obj, $key = null) {
    if ($key == null) {
        $this->items[] = $obj;
    }
    else {
        if (isset($this->items[$key])) {
            throw new KeyHasUseException("Key $key already in use.");
        }
        else {
            $this->items[$key] = $obj;
        }
    }
}
```

`deleteItem()`和`getItem()`方法将密钥作为一个参数，指示哪些项目是要移除或检索的目标。如果提供了无效的键，应该会引发异常。

```
public function deleteItem($key) {
    if (isset($this->items[$key])) {
        unset($this- >items[$key]);
    }
    else {
        throw new KeyInvalidException("Invalid key $key.");
    }
}

public function getItem($key) {
    if (isset($this->items[$key])) {
        return $this->items[$key];
    }
    else {
        throw new KeyInvalidException("Invalid key $key.");
    }
}
```

因为`addItem()`方法的$key 参数是可选的，所以我们不需要知道集合中每一项使用的键。添加一个可以为任何可能需要的外部代码提供键列表的方法是一个好主意。这些键可以作为数组返回:

```
public function keys() {
    return array_keys($this->items);
}
```

知道集合中有多少项也可能是有帮助的。

```
public function length() {
    return count($this->items);
}
```

因为如果传递了无效的键，`getItem()`和`deleteItem()`会抛出异常，所以确定集合中是否存在给定的键也是一个好主意。

```
public function keyExists($key) {
    return isset($this->items[$key]);
}
```

要使用现在的`Collection`类，创建文件`Collection.php`并将`Collection`类的代码保存在其中。也为`KeyInvalidException`和`KeyhasUseException`类创建文件(它们可以是基类`Exception`的简单子类)。如果您没有使用自动加载器，请确保添加 require 语句，然后尝试以下测试代码:

```
<?php
require_once "Collection.php";

class Salut
{
    private $name;
    private $number;

    public function __construct($name, $number) {
        $this->name = $name;
        $this->number = $number;
    }

    public function __toString() {
        return $this->name . " is number " . $this->number;
    }
}

$c = new Collection();
$c->addItem(new Salut("Steve", 14), "steve");
$c->addItem(new Salut("Ed", 37), "ed");
$c->addItem(new Salut("Bob", 49), "bob");

$c->deleteItem("steve");

try {
    $c->getItem("steve");
}
catch (KeyInvalidException $e) {
    print "The collection doesn't contain Steve.";
}
```

这个例子可能不是特别有趣，但是它应该让您了解这个类是如何使用的。

## 结论

集合可以被看作是一种更加专门化的处理列表的方式，对于这些列表，某些契约是有保证的。集合类是传统数组的一个非常有用的面向对象替代方案，并且可以在您可能构建的几乎任何应用程序中实现。它提供了对其成员的精心管理和一致的 API，使得编写使用该类的代码变得容易。

## 分享这篇文章