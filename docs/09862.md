# PHP 中的简单对象迭代器

> 原文：<https://www.sitepoint.com/php-simple-object-iterators/>

如果你已经用 PHP 编程一段时间了，你可能对 [foreach](http://php.net/manual/en/control-structures.foreach.php) 循环很熟悉。它提供了一种简单的方法来分析数组中的每一项，例如

```
 $myArray = array();
$myArray[] = "First item";
$myArray[] = "Second item";
$myArray[] = "Third item";

foreach ($myArray as $i) {
	echo "<p>$i</p>";
} 
```

和数组一样，也可以循环遍历一个对象。如果您的对象包含一组项，则可以使用 foreach 循环来循环访问每一项。不管集合中有什么，也不管如何检索，例如

*   数据库中的记录
*   导航链接
*   目录中文件的名称
*   从文件中读取的文本行
*   特定商店类别中的产品对象

迭代器是一个让许多开发人员感到恐惧的主题。它们听起来很复杂，通常用难以理解的抽象参考来解释。最好用一个简单的例子来解释，所以我们将创建一个基本类来定义一系列 web 技术:

```
 class WebTechnologies
{
	private $tech;

	// constructor
	public function __construct() {
		$this->tech = explode( ',', 'PHP,HTML,XHTML,CSS,JavaScript,XML,XSLT,ASP,C#,Ruby,Python') ;
	}

} 
```

不能在类外部访问私有$tech 数组。您可以将其公开或返回其内容，但是迭代器提供了一个更好的选择。

假设数组中已经有数据，使对象可遍历的最快方法是实现 IteratorAggregate 接口:

```
 class WebTechnologies implements IteratorAggregate
{... 
```

为了完成代码，我们必须在我们的类中定义一个公共的 getIterator()函数。这必须返回可以迭代的东西—比如一个 ArrayIterator 对象，我们的$tech 数组被传递给它的构造函数:

```
 // return iterator
public function getIterator() {
	return new ArrayIterator( $this->tech );
} 
```

我们现在可以迭代$tech 数组中的所有项目。完整代码:

```
 class WebTechnologies implements IteratorAggregate
{

	private $tech;

	// constructor
	public function __construct() {
		$this->tech = explode( ',', 'PHP,HTML,XHTML,CSS,JavaScript,XML,XSLT,ASP,C#,Ruby,Python' );
	}

	// return iterator
	public function getIterator() {
		return new ArrayIterator( $this->tech );
    }

}

// create object
$wt = new WebTechnologies();

// iterate over collection
foreach ($wt as $n => $t) {
	echo "<p>Technology $n: $t</p>";
} 
```

太好了。但是如果我们对象的集合不是一个数组呢？在这种情况下，我们需要更复杂的迭代器……请关注即将推出的完整教程。

## 分享这篇文章