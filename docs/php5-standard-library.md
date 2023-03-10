# 介绍 PHP 5 的标准库

> 原文：<https://www.sitepoint.com/php5-standard-library/>

围绕 PHP5 的很多讨论都集中在它新的面向对象的语法和功能，以及与 Java 的比较上。在这一切进行的同时，名为“标准 PHP 库”(SPL)的扩展悄悄地进入了核心 PHP 5 发行版。

尽管工作仍在进行中，但标准 PHP 库目前的产品极大地增加了 PHP 开发人员达成一致的机会(从而增加了代码重用的机会)。它也可能使你巧妙构造的类接口很容易被其他人使用，因为 SPL 扩展使“重载”基本 PHP 语法成为可能，并使对象看起来像普通的 PHP 数组。

在本教程中，我将介绍 SPL 扩展和 PHP5 的可用功能，并给出足够的例子来帮助您入门。注意:将会使用 PHP5 的语法。如果需要赶进度，可以试试 SitePoint 的 [PHP5 review](https://www.sitepoint.com/article/coming-soon-webserver-near/) 。

***今日迭代:***

*   介绍 SPL:它是关于什么的？
*   循环:有人说迭代器吗？
*   我们每个人的迭代:令人惊叹的因素
*   欣赏这棵树:SPL 类和接口的简短旅程
*   数组形式的对象:对网页设计者来说更容易
*   大问题:为什么你会喜欢它

不要忘记下载本文中包含的所有代码供自己使用。

##### 介绍 SPL

“标准 PHP 库”是由 Marcus Boerger 开发的 PHP 扩展，Marcus Boerger 是“解决标准问题的接口和类的集合”作为 PHP5 核心发行版的一部分，应该是“永远在线”的。

如果你已经接触过 PHP4，你会知道几乎每个新的 PHP 项目都在一些领域不断地重复发明轮子。标准化一些基础知识是让 PHP 开发人员在同一张纸上唱歌的好方法，并且增加了我们在项目 y 中重用项目 X 代码的机会。

今天，SPL 扩展解决了问题的一个子集:迭代器。SPL 迭代器实现的有趣之处不仅在于它定义了 PHP5 中每个人都可以使用的标准，还在于它“重载”了 PHP 语法的某些部分，如 *foreach* 构造和基本数组语法，使得处理类的对象更加容易。

##### 循环回路

那么，什么是迭代器呢？在这个上下文中，迭代器指的是软件“设计模式”，由“四人帮”在他们开创性的[设计模式](http://www.amazon.com/exec/obidos/ASIN/0201633612/)一书中提出。

迭代器的*意图*是“提供一个对象，该对象遍历一些集合结构，抽象出关于该结构实现的假设。”

如同所有的一般定义一样，乍一看，这句话的确切含义可能不太清楚。

我们所说的“聚合结构”基本上是指 PHP 中可能“循环”的任何东西，比如数据库结果集中的行、目录中的文件列表或文本文件中的每一个新行。

使用“普通的”PHP，您可以使用下面的代码来遍历一个 MySQL 查询:

```
// Fetch the "aggregate structure" 

$result = mysql_query("SELECT * FROM users"); 

// Iterate over the structure 

while ( $row = mysql_fetch_array($result) ) { 

    // do stuff with the row here 

}
```

要读取目录的内容，您可以使用:

```
// Fetch the "aggregate structure" 

$dh = opendir('/home/harryf/files'); 

// Iterate over the structure 

while ( $file = readdir($dh) ) { 

    // do stuff with the file here 

}
```

要读取文件的内容，您可以使用:

```
// Fetch the "aggregate structure" 

$fh = fopen("/home/hfuecks/files/results.txt", "r"); 

// Iterate over the structure 

while (!feof($fh)) { 

    $line = fgets($fh); 

    // do stuff with the line here 

}
```

看一下上面的例子就知道它们非常相似。尽管每一个都使用不同类型的*资源*，并使用特定于该资源的 PHP 函数，但口头禅很简单:“获取资源；循环内容”。

如果有可能从上面的例子中“抽象”出特定的 PHP 函数，并使用某种通用接口来代替，那么就有可能使循环数据的工作看起来相同，而不管所使用的资源的类型。由于不需要为不同的数据源修改循环，因此出现循环的代码(可能是生成 HTML 列表的函数)可以在其他地方重用。

这就是迭代器出现的地方。迭代器定义了一个抽象接口供您的代码使用。迭代器的特定实现负责处理您想要处理的每种不同类型的结构，而使用迭代器的代码不必关心细节。

这是迭代器的基本理论。如果你有兴趣了解更多，你可以在 [C2 维基](http://c2.com/cgi/wiki?IteratorPattern)和[维基](http://en.wikipedia.org/wiki/Iterator)找到起点。我的更多想法可以在 phpPatterns 的[迭代器模式](http://www.phppatterns.com/index.php/article/articleview/50/1/1/)和[PHP 选集-第二卷，应用](https://www.sitepoint.com/books/phpant1/)中找到。

##### 我们每个人的迭代

那么 SPL 迭代器有什么令人兴奋的呢？好吧，如果你已经编写了不止一两行 PHP 代码，你可能已经遇到过 [foreach](http://www.php.net/foreach) 构造，它用于简化数组循环的工作:

```
// A list of colors 

$colors = array ( 

    'red', 

    'green', 

    'blue', 

    ); 

foreach ( $colors as $color ) { 

    echo $color.'<br>'; 

}
```

如果所有的循环都这么简单，不管你循环的是什么，那不是很好吗？

这个怎么样？

```
<?php 

// A magic class... (explained in a moment) 

class DirectoryReader extends DirectoryIterator { 

  function __construct($path) { 

    parent::__construct($path); 

  } 

  function current() { 

    return parent::getFileName(); 

  } 

  function valid() { 

    if ( parent::valid() ) { 

      if ( !parent::isFile() ) { 

        parent::next(); 

        return $this->valid(); 

      } 

      return TRUE; 

    } 

    return FALSE; 

  } 

  function rewind() { 

    parent::rewind(); 

  } 

} 

// Create a directory reader for the current directory 

$Reader = new DirectoryReader('./'); 

// Loop through the files in the directory ?!? 

foreach ( $Reader as $Item ) { 

  echo $Item.'<br>'; 

} 

?>
```

文件名:directoryreader.php

如果您暂时忽略类本身，看看最后几行，您会看到我在 foreach 循环中使用了 DirectoryReader 对象。我不用调用它的任何方法就可以从中提取项目！只要你遵守一定的规则(我很快就会讲到)，SPL 扩展允许以同样的方式迭代你自己的类(如果合适的话)。

事实上，在上面的例子中，我已经陷入了困境！让我们后退几步，这样我才能解释这里到底发生了什么。

##### SPL 迭代

现在您的兴趣是 whet，您首先需要得到警告，PHP 手册目前缺乏完整记录 SPL 扩展所需的能力。它主要适用于记录本机函数，缺乏一种清晰的方法来完全描述类似内置类的东西；接口甚至没有被提及。

相反，您需要查看由 Marcus 维护的[生成的文档](http://www.php.net/~helly/php/ext/spl/)，并在 CVS 下搜索[源代码。还要注意，SPL 扩展是一个正在积极开发和扩展的移动目标。本教程中的代码是在 PHP 5.0.1 下测试的，但是如果你在很久以后阅读，你可能会发现部分代码已经过时了。](http://cvs.php.net/pecl/spl/)

SPL 扩展定义了类和接口的层次结构。其中一些已经加载到 PHP5 安装中了(请看 [get_declared_classes()](http://www.php.net/get_declared_classes) 出现了什么)。它们对应于这里定义的[接口和类定义](http://cvs.php.net/co.php/pecl/spl/spl.php)和这里定义的(一旦 Marcus 有时间用 C 实现它们，这里找到的 PHP 文件最终会消失)。在[示例目录](http://cvs.php.net/pecl/spl/examples/)中找到的一些类(带有。inc 扩展名)也是层次结构的一部分，但默认情况下不会加载；如果您希望使用它们，您需要确保包含的副本位于 PHP 包含路径中的某个位置。在[测试](http://cvs.php.net/pecl/spl/tests/)中可以找到更多使用这些类的例子，而在[http://www.wiki.cc/php/PHP5#Iterators](http://www.wiki.cc/php/PHP5#Iterators)中可以找到独立的例子。

虽然一开始层次结构中的类和接口的数量可能令人望而生畏，但是不要惊慌！迭代器的基本用法只需要一个接口。如果你对接口的概念不熟悉，可以看看 SitePoint 上关于接口的讨论。

我将在本教程的后面总结所有预加载的类和接口的用途，供您在闲暇时浏览。一旦您开始理解所提供的内容，您就会意识到 Marcus 在解决 PHP 中最常见的循环相关问题方面做得非常出色。生活会变得更容易…

让我们回到 DirectoryReader 示例。我是如何使用 foreach 迭代我的 DirectoryReader 对象的？神奇之处来自我扩展的类，[director Iterator](http://www.php.net/~helly/php/ext/spl/classDirectoryIterator.html)，它实现了一个由 SPL 扩展定义的名为[迭代器](http://www.php.net/~helly/php/ext/spl/interfaceIterator.html)的接口。

我编写的任何实现迭代器接口的类都可以在 foreach 循环中使用(注意[本文从 PHP 内部的角度解释了这是如何工作的](http://lxr.php.net/source/ZendEngine2/zend_iterators.h))。迭代器接口定义如下:

```
interface Iterator extends Traversable { 

    /** 

    * Rewind the Iterator to the first element. 

    * Similar to the reset() function for arrays in PHP 

    * @return void 

    */ 

    function rewind(); 

    /** 

    * Return the current element. 

    * Similar to the current() function for arrays in PHP 

    * @return mixed current element from the collection 

    */ 

    function current(); 

    /** 

    * Return the identifying key of the current element. 

    * Similar to the key() function for arrays in PHP 

    * @return mixed either an integer or a string 

    */ 

    function key(); 

    /** 

    * Move forward to next element. 

    * Similar to the next() function for arrays in PHP 

    * @return void 

    */ 

    function next(); 

    /** 

    * Check if there is a current element after calls to rewind() or next(). 

    * Used to check if we've iterated to the end of the collection 

    * @return boolean FALSE if there's nothing more to iterate over 

    */ 

    function valid(); 

}
```

注意，SPL 扩展向 Zend 引擎注册了迭代器继承的可遍历接口，以允许使用 foreach。可遍历的接口是*而不是*应该直接在 PHP 中实现，而是由其他内置的 PHP 类实现(目前，SimpleXML 扩展实现了这一点；SQLite 扩展可能会这样做，但是现在，它直接与 Zend API 对话。

要实现这个接口，您的类必须提供上面定义的所有方法。

为了向您展示这是如何工作的，我将从重新发明轮子并为原生 PHP 数组实现一个迭代器开始。显然，这是一个毫无意义的练习，但它帮助我们理解它是如何工作的，而不会迷失在具体的细节中。

首先，我定义一个类来管理迭代:

```
/** 

* An iterator for native PHP arrays, re-inventing the wheel 

* 

* Notice the "implements Iterator" - important! 

*/ 

class ArrayReloaded implements Iterator { 

    /** 

    * A native PHP array to iterate over 

    */ 

  private $array = array(); 

    /** 

    * A switch to keep track of the end of the array 

    */ 

  private $valid = FALSE; 

    /** 

    * Constructor 

    * @param array native PHP array to iterate over 

    */ 

  function __construct($array) { 

    $this->array = $array; 

  } 

    /** 

    * Return the array "pointer" to the first element 

    * PHP's reset() returns false if the array has no elements 

    */ 

  function rewind(){ 

    $this->valid = (FALSE !== reset($this->array)); 

  } 

    /** 

    * Return the current array element 

    */ 

  function current(){ 

    return current($this->array); 

  } 

    /** 

    * Return the key of the current array element 

    */ 

  function key(){ 

    return key($this->array); 

  } 

    /** 

    * Move forward by one 

    * PHP's next() returns false if there are no more elements 

    */ 

  function next(){ 

    $this->valid = (FALSE !== next($this->array)); 

  } 

    /** 

    * Is the current element valid? 

    */ 

  function valid(){ 

    return $this->valid; 

  } 

}
```

文件名:arrayreloaded.php

注意开头的“实现迭代器”。这表示我同意遵守迭代器“契约”,并将提供所有需要的方法。然后，该类提供每个方法的实现，使用 PHP 的原生数组函数执行必要的工作(注释解释了细节)。

当你写你自己的迭代器时，迭代器的设计有几点值得注意。在循环的一次迭代中，`current()`和`key()`迭代器方法可能会被多次调用，所以需要注意调用它们不会修改迭代器的状态。在这种情况下，这不成问题，但是当处理文件时，例如，在 current()方法中使用 [fgets()](http://www.php.net/fgets) ，这将推进文件指针。

否则，记住 valid()方法应该指示当前元素*是否有效，而不是下一个*元素*。这意味着，当在迭代器上循环时，我们实际上将前进一个元素到集合的末尾，并且只有在调用`valid()`时才发现这个事实。通常，`next()`和`rewind()`方法会真正移动迭代器，并负责跟踪当前元素是否有效。*

我现在可以如下使用这个类:

```
// Create iterator object 

$colors = new ArrayReloaded(array ('red','green','blue',)); 

// Iterate away! 

foreach ( $colors as $color ) { 

  echo $color."<br>"; 

}
```

非常好用！在幕后，foreach 构造调用我定义的方法，从`rewind()`开始。然后，只要`valid()`返回`TRUE`，它就调用`current()`填充`$color`变量，调用`next()`将迭代器向前移动一个元素。

和 foreach 一样，我也可以用从`key()`方法返回的值填充另一个变量:

```
// Display the keys as well 

foreach ( $colors as $key => $color ) { 

  echo "$key: $color<br>"; 

}
```

当然，没有什么要求我使用 foreach。我可以直接从我的代码中调用这些方法，就像这样:

```
// Reset the iterator - foreach does this automatically 

$colors->rewind(); 

// Loop while valid 

while ( $colors->valid() ) { 

    echo $colors->key().": ".$colors->current()."<br>"; 

    $colors->next(); 

}
```

这个例子可以帮助你了解 foreach 对你的对象做了什么。

请注意，我执行的粗略基准测试表明，直接调用方法比使用 foreach 更快，因为后者引入了另一层重定向，必须在运行时由 PHP 解决。

##### 欣赏这棵树

现在你已经看到了如何编写一个基本的迭代器，有必要总结一下 SPL 扩展内部提供的接口和类，这样你就知道它们的工作是什么了。这个列表将来可能会改变，但它总结了目前提供的内容。

***界面***

*   [Traversable](http://www.php.net/~helly/php/ext/spl/interfaceTraversable.html) :如上所述，这是 PHP 内部的迭代器接口。除非你正在写一个扩展，忽略这个。
*   [迭代器](http://www.php.net/~helly/php/ext/spl/interfaceIterator.html):如您所见，它定义了向前遍历集合的基本方法。
*   [IteratorAggregate](http://www.php.net/~helly/php/ext/spl/interfaceIteratorAggregate.html) :如果你想从你的“集合”对象中单独实现迭代器，那么实现迭代器聚合将允许你将迭代的工作委托给一个单独的类，同时仍然允许你在 foreach 循环中使用集合。
*   [RecursiveIterator](http://www.php.net/~helly/php/ext/spl/interfaceRecursiveIterator.html) :这定义了允许在分层数据结构上迭代的方法。
*   [SeekableIterator](http://www.php.net/~helly/php/ext/spl/interfaceSeekableIterator.html) :这定义了一个方法来搜索迭代器正在管理的集合。
*   这是另一个对 Zend 引擎有特殊意义的神奇接口。实现这个可以让你像对待一个普通 PHP 数组语法的数组一样对待你的对象。

***类***

*   这个迭代器可以管理本地 PHP 数组和一个对象的公共属性(稍后会详细介绍)。
*   [ArrayObject](http://www.php.net/~helly/php/ext/spl/classArrayObject.html) :这统一了数组和对象，允许你遍历它们并使用数组语法来访问内容。参见下面的“对象作为数组”(我们将用类似的行为来发展我们自己的类)。
*   [FilterIterator](http://www.php.net/~helly/php/ext/spl/classFilterIterator.html) :这是一个抽象类，可以扩展它来过滤正在被迭代的元素(可能是删除搜索中不需要的元素)。
*   [ParentIterator](http://www.php.net/~helly/php/ext/spl/classParentIterator.html) :当使用 ResursiveIterator 时，ParentIterator 允许你过滤掉没有子元素的元素。例如，如果您有一个 CMS，其中的文档可以放在类别树下的任何位置，ParentIterator 将允许您递归树，但只显示“类别节点”，忽略出现在每个类别下的文档。
*   [LimitIterator](http://www.php.net/~helly/php/ext/spl/classLimitIetrator.html) :这个类允许你指定迭代的元素范围，从一个键偏移量开始，并指定从该点开始访问的元素数量。这个概念与 MySQL 中的 LIMIT 子句相同。
*   [CachingIterator](http://www.php.net/~helly/php/ext/spl/classCachingIterator.html) :管理另一个迭代器(传递给它的构造函数)。它允许您在使用`next()`方法之前，使用`hasNext()`方法检查内部迭代器是否有更多的元素。我个人对名字不是 100%确定；也许 LookAheadIterator 会更准确？
*   cachingcrecursiveiterator:这与 CachingIterator 基本相同，但是允许对分层数据结构进行迭代。
*   [DirectoryIterator](http://www.php.net/~helly/php/ext/spl/classDirectoryIterator.html) :为了遍历文件系统中的一个目录，这个迭代器提供了一堆有用的方法，比如`isFile()`和`isDot()`，这样可以省去很多麻烦。
*   RecursiveDirectoryIterator :这个类允许在一个目录结构上迭代，这样你就可以进入子目录。
*   这使得 [SimpleXML](http://www.php.net/simplexml) 更加简单！目前，最好的例子可以在 SPL [测试](http://cvs.php.net/pecl/spl/tests/)中找到——参见以“sxe_*”开头的文件
*   RecursiveIteratorIterator :这有助于你做一些很酷的事情，比如“扁平化”一个层次数据结构，这样你就可以用一条 foreach 语句遍历它，同时仍然保留层次结构的知识。例如，这个类对于呈现树形菜单非常有用。

要查看它的运行情况，请尝试使用 [DirectoryTreeIterator](http://cvs.php.net/co.php/pecl/spl/examples/directorytreeiterator.inc) (它扩展了 RecursiveIteratorIterator)，如下所示:

```
$DirTree = new DirectoryTreeIterator('/some/directory');  

foreach ($DirTree as $node) {  

    echo "$noden";  

}
```

这总结了 SPL 扩展今天定义的核心类和接口。

##### 作为数组的对象

您已经看到了实现迭代器接口如何允许您“重载”foreach 构造。不过，SPL 扩展还有更多惊喜，首先是 ArrayAccess 接口。从 PHP 语法的角度来看，用一个类实现这个接口允许您将该类的对象视为数组。

这里有一个例子:

```
/**  

* A class that can be used like an array  

*/  

class Article implements ArrayAccess {  

  public $title;  

  public $author;  

  public $category;   

  function __construct($title,$author,$category) {  

    $this->title = $title;  

    $this->author = $author;  

    $this->category = $category;  

  }  

  /**  

  * Defined by ArrayAccess interface  

  * Set a value given it's key e.g. $A['title'] = 'foo';  

  * @param mixed key (string or integer)  

  * @param mixed value  

  * @return void  

  */  

  function offsetSet($key, $value) {  

    if ( array_key_exists($key,get_object_vars($this)) ) {  

      $this->{$key} = $value;  

    }  

  }  

  /**  

  * Defined by ArrayAccess interface  

  * Return a value given it's key e.g. echo $A['title'];  

  * @param mixed key (string or integer)  

  * @return mixed value  

  */  

  function offsetGet($key) {  

    if ( array_key_exists($key,get_object_vars($this)) ) {  

      return $this->{$key};  

    }  

  }  

  /**  

  * Defined by ArrayAccess interface  

  * Unset a value by it's key e.g. unset($A['title']);  

  * @param mixed key (string or integer)  

  * @return void  

  */  

  function offsetUnset($key) {  

    if ( array_key_exists($key,get_object_vars($this)) ) {  

      unset($this->{$key});  

    }  

  }  

  /**  

  * Defined by ArrayAccess interface  

  * Check value exists, given it's key e.g. isset($A['title'])  

  * @param mixed key (string or integer)  

  * @return boolean  

  */  

  function offsetExists($offset) {  

    return array_key_exists($offset,get_object_vars($this));  

  }  

}
```

文件名:arrayaccess1.php

以“offset”开头的四个方法是由我正在实现的 [ArrayAccess](http://www.php.net/~helly/php/ext/spl/interfaceArrayAccess.html) 接口定义的。

请注意，我使用了一些 PHP 运行时技巧来简化工作，比如检查对象变量是否已经通过自省进行了定义:

`function offsetSet($key, $value) {  
   if ( array_key_exists($key,get_object_vars($this)) ) {`

我还间接引用了它们，使用一个保存它们名称的变量:

`$this->{$key} = $value;`

当您看到现在如何使用这个类时，这个示例变得很有趣:

```
// Create the object  

$A = new Article('SPL Rocks','Joe Bloggs', 'PHP');  

// Check what it looks like  

echo 'Initial State:<pre>';  

print_r($A);  

echo '</pre>';  

// Change the title using array syntax  

$A['title'] = 'SPL _really_ rocks';  

// Try setting a non existent property (ignored)  

$A['not found'] = 1;  

// Unset the author field  

unset($A['author']);  

// Check what it looks like again  

echo 'Final State:<pre>';  

print_r($A);  

echo '</pre>';
```

除了我创建对象的第一行，代码是原生 PHP 数组的有效语法。以下是输出结果:

```
Initial State:  

Article Object  

(  

    [title] => SPL Rocks  

    [author] => Joe Bloggs  

    [category] => PHP  

)  

Final State:  

Article Object  

(  

    [title] => SPL _really_ rocks  

    [category] => PHP  

)
```

注意，我可以添加逻辑来操作正在读取的数据，方法是修改`offsetGet()`方法，如下所示:

`function offsetGet($key) {  
   if ( array_key_exists($key,get_object_vars($this)) ) {  
     return strtolower($this->{$key});  
   }  
 }`

这会将所有值转换成小写。

为了使对象可迭代，使用 foreach 或其他方法，我现在可以利用 SPL 的[array operator](http://www.php.net/~helly/php/ext/spl/classArrayIterator.html)类，结合 [IteratorAggregate](http://www.php.net/~helly/php/ext/spl/interfaceIteratorAggregate.html) 接口。

正如我前面提到的，当您不想将迭代器逻辑嵌入到包含要迭代的数据的对象中时，可以使用 IteratorAggregate 接口。这可能是保持这种逻辑分离的有用方法，但是更有趣的是，它允许您重用现有的迭代器。

首先，我修改 Article 类的第一行来声明接口实现:

```
class Article implements ArrayAccess, IteratorAggregate {
```

现在，我需要添加一个额外的方法:`getIterator()`，它返回用于迭代的对象:

`/**  
 * Defined by IteratorAggregate interface  
 * Returns an iterator for for this object, for use with foreach  
 * @return ArrayIterator  
 */  
 function getIterator() {  
   return new ArrayIterator($this);  
 }`

完成后，我可以遍历类中定义的属性:

```
$A = new Article('SPL Rocks','Joe Bloggs', 'PHP');  

// Loop (getIterator will be called automatically)  

echo 'Looping with foreach:<pre>';  

foreach ( $A as $field => $value ) {  

  echo "$field : $value<br>";  

}  

echo '</pre>';  

// Get the size of the iterator (see how many properties are left)  

echo "Object has ".sizeof($A->getIterator())." elements";
```

文件名:arrayaccess2.php

下面是它显示的内容:

```
$A = new Article('SPL Rocks','Joe Bloggs', 'PHP');  

// Loop (getIterator will be called automatically)  

echo 'Looping with foreach:<pre>';  

foreach ( $A as $field => $value ) {  

  echo "$field : $value<br>";  

}  

echo '</pre>';  

// Get the size of the iterator (see how many properties are left)  

echo "Object has ".count($A->getIterator())." elements";
```

这给了我:

```
Looping with foreach:  

title : SPL Rocks  

author : Joe Bloggs  

category : PHP  

Object has 3 elements
```

注意，我还可以对对象使用 [count](http://www.php.net/count) 函数来找出它有多少个元素。这允许我使用其他循环结构，而不需要调用迭代器方法:

```
$size = count($A);  

for($i = 0; $i < $size; $i++ ) {  

    echo $A[$i]."n";  

}
```

(还)不起作用的是 PHP 的[数组函数](http://www.php.net/array)对对象的应用(你会抱怨它不是数组)。然而，只要你没有使用类似于 [is_array()](http://www.php.net/is_array) 的东西进行类型检查，你应该能够重用你自己编写的代码的任何部分来期望一个数组。

##### 大事

我希望您已经渐渐感觉到，SPL 扩展在让 PHP 开发人员的生活变得更加轻松方面是一件“大事”。就我个人而言，SPL 已经提供的东西给我留下了深刻的印象，并对 Marcus Boerger 使之成为可能给予应有的信任。它还解决了我对 PHP5 中接口的一些疑虑，证明了它们作为一种机制的有用性，通过这种机制，我们可以在 PHP 引擎和用 PHP 编写的代码之间指定一个契约，允许 PHP 语法语义的改变产生很大的影响。

也许 SPL 今天所做的最重要的方面是它鼓励使用标准，首先通过定义一组在 PHP5 中“永远在线”的 API(那么为什么不使用它们呢？)，其次，还有额外的“胡萝卜”,即能够重载 PHP 语法和 foreach 之类的构造。

假设我们都同意使用 SPL 提供的类和接口，项目就可以开始向它们靠拢了。例如，考虑一下 [HTML_TreeMenu](http://pear.php.net/package/HTML_TreeMenu) ，这是一个 PEAR 库，旨在支持在 HTML 中生成基于 Javascript 的树形菜单。现在，它把大量的工作留给了开发人员。下面是一个用 HTML_TreeMenu 从目录结构中绘制树的例子:

```
require_once 'HTML/TreeMenu.php';  

$map_dir = 'c:/windows';  

$menu  = new HTML_TreeMenu('menuLayer', 'images', '_self');  

$menu->addItem(recurseDir($map_dir));  

function &recurseDir($path) {  

    if (!$dir = opendir($path)) {  

        return false;  

    }  

    $files = array();  

    $node = &new HTML_TreeNode(basename($path), basename($path), 'folder.gif');  

    while (($file = readdir($dir)) !== false) {  

        if ($file != '.' && $file != '..') {  

            if (@is_dir("$path/$file")) {  

                $addnode = &recurseDir("$path/$file");  

            } else {  

                $addnode = &new HTML_TreeNode($file, $file, 'document2.png');  

            }  

            $node->addItem($addnode);  

        }  

    }  

    closedir($dir);  

    return $node;  

}  

echo $menu->printMenu();
```

换句话说，我们需要按照正确的顺序准备数据并构建树。相反，HTML_Treemenu 可以提供一种机制，通过这种机制我们可以注册数据结构，然后让它为我们进行迭代。上面的例子可以简化为:

```
require_once 'HTML/TreeMenu.php';  

$map_dir = 'c:/windows';  

$menu  = new HTML_TreeMenu('menuLayer', 'images', '_self');  

// Register the tree data structure  

$menu->registerTree(new new RecursiveDirectoryIterator($map_dir);  

echo $menu->printMenu();
```

如果手头没有适合您的问题的 RecursiveIterator，您总是可以实现您自己的，让 HTML_Treemenu 利用[类型提示](https://www.sitepoint.com/coming-soon-webserver-near/)来确保您给出了它所需要的。

***更多标准请！***

问题是，还有什么可以标准化？毕竟，这个扩展被称为“标准 PHP 库”。在与 Marcus Boerger 进行了简短的电子邮件交流后，我很高兴向您报告他对未来的一些想法(取决于时间和帮助):

*   标准异常，如`RuntimeException`和`OutOfBoundsException`(相当于 Python 的[内置异常](http://docs.python.org/lib/module-exceptions.html)，异常以它们将被用来标记的问题命名)。
*   契约式设计 TM[的一些概念(可能会引入新的语言结构，如`requires()`和`ensures()`，以及相关的异常)。](http://en.wikipedia.org/wiki/Design_by_contract)
*   More pattern implementations, where possible. The [Observer pattern](http://c2.com/cgi/wiki?ObserverPattern) may be a particularly good candidate, for example, but to quote some of the questions Marcus has there:

    <q>可观测的问题是没有多重继承怎么做？被观察的事物需要有一个观察者的容器。但是我们只有接口。</q>

    此外，如何处理应该作为不同可观察对象的观察者的对象？我应该传递原始的可观测值吗？如果是，它将如何识别自己？

    所以你看有很多要考虑的。以及我需要更多输入和用例的领域。

*   [依赖注入](http://www.martinfowler.com/articles/injection.html)的一些概念(一种管理依赖于其他对象的对象的模式)。为了达到这个目的，新的反射 API 需要变得更容易访问。

如果你有兴趣了解更多信息或直接与 Marcus 交谈，可以去今年 11 月在法兰克福举行的[国际 PHP 大会](http://www.phpconference.de/2004/session-php5_en.php)上找到他，届时他将做一场关于 SPL 的演讲。

我受够了。获取迭代！

## 分享这篇文章