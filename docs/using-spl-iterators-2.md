# 使用 SPL 迭代器，第 2 部分

> 原文：<https://www.sitepoint.com/using-spl-iterators-2/>

在本系列的第一部分中，我介绍了一些 SPL 迭代器以及如何使用它们。然而，有时提供的迭代器不足以满足您的需求，您可能需要开发自己的迭代器。幸运的是，SPL 提供了允许你这样做的接口。

对于在`foreach`循环中可遍历的对象，PHP 要求它是`[Traversable](http://www.php.net/manual/en/class.traversable.php)`的实例。然而，您不能直接实现这个接口(尽管您可以在`instaceof`检查中使用它)；相反，你需要实现 SPL 的`[Iterator](http://www.php.net/manual/en/class.iterator.php)`或`[IteratorAggregate](http://php.net/manual/en/class.iteratoraggregate.php)`接口。

## 迭代程序

`Iterator`允许你创建直接迭代的对象或创建外部迭代器。它规定了需要实现的五种方法:`rewind()`、`current()`、`key()`、`next()`和`valid()`。

假设我们有一个图书馆，我们希望能够迭代图书馆中的藏书。这里有一个通过实现`Iterator`来做到这一点的例子:

```
<?php
class Library implements Iterator
{
    // an internal pointer to the current position
    // in the dataset
    protected $position = 0;

    // an array of the titles of the books in the library
    protected $books = [
        "Professional PHP Programming",
        "Programming Perl",
        "A Byte of Python",
        "The Ruby Way"
    ];

    // this method takes the pointer back to the beginning
    // of the dataset to restart the iteration
    public function rewind() {
        echo "rewinding <br>";
        $this->position = 0;
    }

    // this method returns the value at the current
    // position of the dataset
    public function current() {
        echo "current <br>";
        return $this->books[$this->position];
    }

    // this should return the current value of the pointer
    public function key() {
        echo "key <br>";
        return $this->position;
    }

    // this method moves the pointer to the next value
    // in the data set
    public function next() {
        echo "next <br>";
        ++$this->position;
    }

    / /this returns a boolean indicating if the there
    // is data at the current position in the dataset
    public function valid() {
        echo "valid <br>";
        return isset($this->books[$this->position]);
    }
}

$library = new Library();
foreach ($library as $key => $value) {
    echo $key . ": " . $value . "<br>";
}
```

上述代码的输出是:

```
rewinding 
valid 
current 
key 
0: Professional PHP Programming
next 
valid 
current 
key 
1: Programming Perl
next 
valid 
current 
key 
2: A Byte of Python
next 
valid 
current 
key 
3: The Ruby Way
next 
valid
```

当迭代开始时，PHP 调用`rewind()`方法将指针带回数据集的开头。然后，它通过调用`valid()`来检查此时是否有有效数据。如果接收到 true，则调用`current()`来获取迭代中该点的值。因为您在循环结构中需要`$key`，所以 PHP 调用`key()`方法来获取当前键的值。PHP 然后调用`next()`来推进指针，并再次调用`valid()`来检查是否有有效数据。循环继续，直到 v `alid()`返回假。

给你从头实现非常强大的迭代器的能力，最适合于创建如上的内部迭代器。当然，上面的例子很简单，只是为了给你一个所需方法和它们如何工作的概述。理想情况下，对于像上面这样的数据集(一个数组)，使用`IteratorAggregate`会更好。

## 迭代器聚合

`IteratorAggregate`要求你实现一个方法，`getIterator()`。这个方法需要返回一个将用于迭代的外部迭代器。使用`IteratorAggregate`改写上面的例子给我们提供了:

```
<?php
class Library implements IteratorAggregate
{
    protected $books = [
        "Professional PHP Programming",
        "Programming Perl",
        "A Byte of Python",
        "The Ruby Way"
    ];

    // return an Iterator of your data
    public function getIterator() {
        echo "getIterator <br>";
        return new ArrayIterator($this->books);
    }
}

$library = new Library();
foreach($library as $key => $value) {
    echo $key . ": " . $value . "<br>";
}
```

上述代码的输出是:

```
getIterator 
0: Professional PHP Programming
1: Programming Perl
2: A Byte of Python
3: The Ruby Way
```

在迭代开始时，PHP 调用返回迭代器的`getIterator()`，在本例中是 SPL 的`ArrayIterator`。然后 PHP 对这个迭代器进行所有适当的调用。基本上，您将数据交给外部迭代器，它会为您完成所有工作，而您可以高枕无忧。

由于两个接口提供相同的功能，知道是使用`Iterator`还是`IteratorAggregate`有时会很棘手。作为一般的经验法则，当你需要从头开始定义迭代器的行为时，你应该使用`Iterator`，因为`IteratorAggregate`不是一个很好的选择。

## 摘要

在本文中，我向您介绍了两个允许您创建自己的迭代器的接口。希望您理解创建自己的迭代器是多么容易，并准备好将 SPL 的这一酷功能添加到您的武器库中。当然，如果你觉得我错过了什么或者做错了什么，或者只是想分享一些东西，请不要犹豫，在评论中告诉我！

<small>图片 via[musha kesa](http://www.shutterstock.com/gallery-96945p1.html)/[Shutterstock](http://www.shutterstock.com)</small>

## 分享这篇文章