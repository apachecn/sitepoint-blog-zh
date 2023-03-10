# 在 PHP 中创建严格类型的数组和集合

> 原文：<https://www.sitepoint.com/creating-strictly-typed-arrays-collections-php/>

这篇文章最初出现在媒体的[上，经作者许可后在此转载。我们鼓励你在 Medium 上关注 Bert，并在那里给他一些喜欢！](https://medium.com/2dotstwice-connecting-the-dots/creating-strictly-typed-arrays-and-collections-in-php-37036718c921#.wx5byexjs)

* * *

PHP 5.6 中宣布的语言特性之一是添加了`...`标记来表示函数或方法接受可变长度的参数。

我很少提到的一点是，可以将这一特性与类型提示结合起来创建类型化数组。

例如，我们可以有一个电影类，它有一个方法来设置一个只接受`DateTimeImmutable`对象的播放日期数组:

```
<?php

class Movie {  
  private $dates = [];

  public function setAirDates(\DateTimeImmutable ...$dates) {
    $this->dates = $dates;
  }

  public function getAirDates() {
    return $this->dates;
  }
} 
```

我们现在可以将可变数量的单独的`DateTimeImmutable`对象传递给`setAirDates()`方法:

```
<?php

$movie = new Movie();

$movie->setAirDates(
  \DateTimeImmutable::createFromFormat('Y-m-d', '2017-01-28'),
  \DateTimeImmutable::createFromFormat('Y-m-d', '2017-02-22')
); 
```

如果我们要传递除了`DateTimeImmutable`之外的东西，比如一个字符串，就会抛出一个致命的错误:

![Catchable fatal error: Argument 1 passed to Movie::setAirDates() must be an instance of DateTimeImmutable, string given.](img/e10a92b54a3c218351d118c8c080a3be.png)

相反，如果我们已经有了一个要传递给`setAirDates()`的`DateTimeImmutable`对象数组，我们可以再次使用`...`令牌，但是这次要解包它们:

```
<?php

$dates = [
  \DateTimeImmutable::createFromFormat('Y-m-d', '2017-01-28'),
  \DateTimeImmutable::createFromFormat('Y-m-d', '2017-02-22'),
];

$movie = new Movie();
$movie->setAirDates(...$dates); 
```

如果数组包含一个非预期类型的值，我们仍然会得到前面提到的致命错误。

此外，从 PHP 7 开始，我们可以以同样的方式使用标量类型。例如，我们可以在我们的`Movie`类中添加一个方法来设置一个浮动的评级列表:

```
<?php

declare(strict_types=1);

class Movie {
  private $dates = [];
  private $ratings = [];

  public function setAirDates(\DateTimeImmutable ...$dates) { /* ... */ }
  public function getAirDates() : array { /* ... */ }

  public function setRatings(float ...$ratings) {
    $this->ratings = $ratings;
  }

  public function getAverageRating() : float {
    if (empty($this->ratings)) {
      return 0;
    }

    $total = 0;

    foreach ($this->ratings as $rating) {
      $total += $rating;
    }

    return $total / count($this->ratings);
  }
} 
```

同样，这确保了 ratings 属性将总是包含浮动，而我们不必遍历所有内容来验证它们。所以现在我们可以很容易地在`getAverageRating()`中对它们进行一些数学运算，而不必担心无效类型。

## 这种类型化数组的问题

将该特性用作类型化数组的一个缺点是，我们只能为每个方法定义一个这样的数组。假设我们希望有一个电影类，它期望在构造函数中有一个播出日期列表和一个评级列表，而不是在以后通过可选的方法来设置它们。这对于上面使用的方法是不可能的。

另一个问题是，当使用 PHP 7 时，我们的`get()`方法的返回类型仍然必须是“array ”,这通常过于一般化。

## 解决方案:集合类

要解决这两个问题，我们可以简单地在所谓的“集合”类中注入我们的类型化数组。这也改进了我们的关注点分离，因为我们现在可以将平均评级的计算方法转移到相关的集合类:

```
<?php

declare(strict_types=1);

class Ratings {
  private $ratings;

  public function __construct(float ...$ratings) {
    $this->ratings = $ratings;
  }

  public function getAverage() : float {
    if (empty($this->ratings)) {
      return 0;
    }

    $total = 0;

    foreach ($this->ratings as $rating) {
      $total += $rating;
    }

    return $total / count($this->ratings);
  }
} 
```

请注意，我们仍然在构造函数中使用了一个长度可变的类型化参数列表，这样就省去了循环检查每个评级的类型的麻烦。

如果我们希望能够在 foreach 循环中使用这个集合类，我们只需实现 [IteratorAggregate](http://php.net/manual/en/class.iteratoraggregate.php) 接口:

```
<?php

declare(strict_types=1);

class Ratings implements IteratorAggregate {
  private $ratings;

  public function __construct(float ...$ratings) {
    $this->ratings = $ratings;
  }

  public function getAverage() : float { /* ... */ }

  public function getIterator() {
     return new ArrayIterator($this->ratings);
  }
} 
```

接下来，我们还可以为我们的飞行日期列表创建一个集合:

```
<?php

class AirDates implements IteratorAggregate {
  private $dates;

  public function __construct(\DateTimeImmutable ...$dates) {
    $this->dates = $dates;
  }

  public function getIterator() {
     return new ArrayIterator($this->airdates);
  }
} 
```

在 Movie 类中将所有的拼图拼在一起，我们现在可以在构造函数中注入两个独立类型的集合。此外，我们可以在 get 方法上定义比“数组”更具体的返回类型:

```
<?php

class Movie {
  private $dates;
  private $ratings;

  public function __construct(AirDates $airdates, Ratings $ratings) {
    $this->airdates = $airdates;
    $this->ratings = $ratings;
  }

  public function getAirDates() : AirDates {
    return $this->airdates;
  }

  public function getRatings() : Ratings {
    return $this->ratings;
  }
} 
```

## 使用值对象进行自定义验证

如果我们想给我们的评级添加额外的验证，我们还可以更进一步，用一些自定义约束定义一个评级值对象。例如，评级可以限制在 0 到 5 之间:

```
<?php

declare(strict_types=1);

class Rating {
  private $value;

  public function __construct(float $value) {
    if ($value < 0 || $value > 5) {
      throw new \InvalidArgumentException('A rating should always be a number between 0 and 5!');
    }

    $this->value = $value;
  }

  public function getValue() : float {
    return $this->value;
  }
} 
```

回到我们的 Ratings 集合类，我们只需要做一些小的修改就可以使用这些值对象来代替 floats:

```
<?php

class Ratings implements IteratorAggregate {
  private $ratings;

  public function __construct(Rating ...$ratings) {
    $this->ratings = $ratings;
  }

  public function getAverage() : Rating {
    if (empty($this->ratings)) {
      return new Rating(0);
    }

    $total = 0;

    foreach ($this->ratings as $rating) {
      $total += $rating->getValue();
    }

    $average = $total / count($this->ratings);
    return new Rating($average);
  }

  public function getIterator() { /* ... */ }
} 
```

通过这种方式，我们得到了对单个集合成员的额外验证，**仍然不需要对每个注入的对象**进行循环。

## 优势

打出这些单独的集合类和值对象看起来似乎工作量很大，但是它们比一般的数组和标量值有几个优点:

*   在一个地方进行简单的类型验证。我们从来不需要手动循环数组来验证集合成员的类型；

*   无论我们在应用程序中的什么地方使用这些集合和值对象，我们都知道它们的值总是在构造时被验证。例如，任何评级将始终介于 0 和 5 之间；

*   我们可以很容易地为每个集合和/或值对象添加定制逻辑。例如`getAverage()`方法，我们可以在整个应用程序中重用它；

*   我们有可能在单个函数或方法中注入多个类型化列表，如果不首先在集合类中注入值，我们就无法使用`...`标记来实现这一点；

*   大大减少了在方法签名中混淆参数的可能性。例如，当我们想要注入一个收视率列表和一个播出日期列表时，在使用通用数组时，这两个列表很容易在构造时被意外混淆；

## 编辑呢？

到目前为止，您可能想知道如何在初始构造之后对集合和值对象的值进行更改。

虽然我们可以添加方法来方便编辑，但这很快会变得很麻烦，因为我们必须在每个集合上复制大多数方法来保持类型提示的优势。例如，`Ratings`上的`add()`方法应该只接受一个`Rating`对象，而`AirDates`上的`add()`方法应该只接受一个`DateTimeImmutable`对象。这使得这些方法的接口和/或重用非常困难。

相反，我们可以简单地保持我们的集合和值对象不变，并在需要更改时将它们转换为原始类型。完成更改后，我们可以用更新后的值简单地重构任何必要的集合或值对象。在(重新)构造时，所有类型都将被再次验证，以及我们可能已经定义的任何额外的验证。

例如，我们可以将一个简单的`toArray()`方法添加到我们的集合中，并进行如下更改:

```
<?php

// Initial Ratings collection
$ratings = new Ratings(
  new Rating(1.5),
  new Rating(3.5),
  new Rating(2.5)
);

// Convert the collection to an array.
$ratingsArray = $ratings->toArray();

// Remove the 2nd rating.
unset($ratingsArray[1]);
$ratingsArray = array_values($ratingsArray);

// Back to a (new) Ratings collection
$updatedRatings = new Ratings(...$ratingsArray); 
```

这样，我们还可以重用现有的阵列功能，如`array_filter()`。

如果我们真的需要对集合对象本身进行编辑，我们可以根据需要在任何需要的地方添加必要的方法。但是请记住，其中大多数还必须对给定的参数进行类型验证，所以很难在所有不同的集合类中重用它们。

## 重用泛型方法

正如你可能已经注意到的，通过在所有的集合类上同时实现`toArray()`和`getIterator()`，我们仍然会得到一些重复的代码。**幸运的是，这些方法足够通用，可以移到通用的父类**，因为它们都只是返回注入的数组:

```
<?php

abstract class GenericCollection implements IteratorAggregate
{
  protected $values;

  public function toArray() : array {
    return $this->values;
  }

  public function getIterator() {
    return new ArrayIterator($this->values);
  }
} 
```

在我们的集合类中，只剩下构造函数中的类型验证，以及特定于该集合的任何可选的额外逻辑，如下所示:

```
<?php

class Ratings extends GenericCollection
{
  public function __construct(Rating ...$ratings) {
    $this->values = $ratings;
  }

  public function getAverage() : Rating { /* ... */ }
} 
```

可选地，我们可以将集合**设为 final** ，以防止任何子类以可能撤销我们的类型验证的方式篡改 values 属性。

## 结论

尽管还远非完美，但随着 PHP 最新版本的发布，在集合和值对象中进行类型验证已经变得越来越容易了。

理想情况下，我们将在 PHP 的未来版本中获得某种形式的[泛型](https://en.wikipedia.org/wiki/Generic_programming),以进一步促进可重用集合类的创建。

除了 string 之外，将对象转换为不同的原语类型的能力将极大地提高值对象的使用。这很容易通过添加类似于`__toString()`的额外魔法方法来实现，比如`__toInt()`、`__toFloat()`等。

幸运的是，一些 RFC 正在开发中，可能会在以后的版本中实现这两个特性，所以还是祈祷吧！🤞

*   仿制药:[https://wiki.php.net/rfc/generics](https://wiki.php.net/rfc/generics)

*   通用数组:[https://wiki.php.net/rfc/generic-arrays](https://wiki.php.net/rfc/generic-arrays)

*   将对象转换为标量:[https://wiki.php.net/rfc/class_casting_to_scalar](https://wiki.php.net/rfc/class_casting_to_scalar)

* * *

如果你觉得这个教程很有帮助，请访问 Medium 上的[原始帖子](https://medium.com/2dotstwice-connecting-the-dots/creating-strictly-typed-arrays-and-collections-in-php-37036718c921#.fsigudnnv)并给它一些❤️.如果您有任何反馈、问题或评论，请在下面留下，或者作为对原始帖子的回应。

## 分享这篇文章