# 一个转化的 PHP 开发人员的自白:关于可见性和隐私

> 原文：<https://www.sitepoint.com/php-developer-on-visibility-and-privates/>

好了，同学们——今天我在这里谈论 PHP 和 Ruby 在对象属性、方法及其可见性方面的区别和相似之处——如何在类中创建变量，以及如何与它们交互。

## 快速回顾一下我们所知道的

在 PHP 中，对象的属性可以设置为三种可见性之一；`public`、`protected`和`private`。没有可见性声明的属性默认为 public。这些声明中的每一个都改变了对类本身、它的父对象和子对象以及其他与它交互的代码可用的信息。让我们回顾一下它们是如何快速工作的。

*   公共变量随处可见
*   受保护的变量仅在类及其父类或子类中可用
*   私有变量只在指定它们的类中可用，对扩展它的类不可用

为了处理与受保护和私有属性的交互，通常创建 getter 和 setter 访问器方法。这些是允许您设置和检索属性值的函数，通常看起来都非常相似——除非它们实际上是对存储的数据进行操作，否则通常会将它们视为返回或设置属性的单行函数:

```
<?PHP
&nbsp;
class animal
{
  private $_species;
  protected $_name;
&nbsp;
  public function get_species()
  {
    return $this->_species;
  }
&nbsp;
  public function get_name()
  {
    return $this->_name;
  }
}
&nbsp;
class pterodactyl
  extends animal
{
  public function __construct()
  {
    $this->_name = $name;
    $this->_species = 'dinosaur';
  }
}
&nbsp;
$my_big_birdie = new pterodactyl('Daryl');
&nbsp;
var_dump($my_big_birdie->get_name()); // string(5) 'Daryl'
var_dump($my_big_birdie->get_species()); // NULL
```

调用`get_name`方法返回我们设置的名称，因为该变量受到保护并且在子类中可用。通过在子类中设置`_species`，它创建了一个名为 _species 的新公共属性，并将父类中的 private _species 属性保留为 NULL。一个`$my_big_birdie`的完整垃圾场将向我们展示这一点:

```
Object(pterodactyl)#1 (3) {
  ["_species":"animal":private]=> NULL
  ["_name":protected]=> string(5) "Daryl"
  ["_species"]=> string(8) "dinosaur"
}
```

Ruby 并没有我们想象中的属性。相反，您必须为您想要访问的任何实例变量定义 getter 和 setter 方法。这些人通常被称为读者和作者。(前面带有@符号的变量是实例变量，这意味着它们在类的范围内可用。)

```
class Animal
  def initialize(name)
    @name = name
  end
&nbsp;
  def name
    @name
  end
&nbsp;
  def name=(name)
    @name = name
  end
&nbsp;
  def species
    @species
  end
end
```

在这个例子中，我们在 Ruby 中实现了 PHP 中的 animal 类。请注意 setter 是如何通过在方法名的末尾添加一个等号来定义的。

由于编写 getters 和 setters 可能是一个重复的过程，Ruby 简化了这个过程，它允许我们声明哪些实例变量可以作为属性使用:

```
class Animal
  attr_reader :species
  attr_accessor :name
&nbsp;
  def initialize(name)
    @name = name
  end
end
```

我们现在拥有和以前一样的类，除了我们用 Ruby `attr_reader`和`attr_accessor`创建我们的 getter 和 setter。`attr_reader`只创建一个 getter 方法，`attr_writer`(此处未使用)只创建一个 setter 方法，`attr_accessor`创建两个方法。90%的情况下，我们需要做的就是将变量公开为属性，如果需要更多的功能，可以简单地使用标准的方法构造。

Ruby 也有不同的 Public、Private 和 Protected 实现。默认情况下，所有的方法都是`public`，但是你可以通过一个`protected`或者`private`语句来声明可见性，然后后面的任何方法都将具有该可见性。这允许您将具有相同可见性的方法分组在一起:

```
class MyClass
  # the default visibility is public
  def my_public_method
  end
&nbsp;
  protected
  def my_protected_method
  end
&nbsp;
  def my_other_protected_method
  end
&nbsp;
  private
  def my_private_method
  end
end
```

Ruby 的 protected 和 private 的实现与 PHP 的非常不同，与对象继承没有任何关系。Ruby 中的 Private 更类似于 PHP 中的 Protected。Ruby 中的私有方法可以在对象或任何继承对象的上下文中访问。

```
class Foo
  private
  def bar
    puts "bar called"
  end
end
&nbsp;
class Blarg < Foo
  def initialize
    bar
  end
end
```

因为`Blarg`类扩展了`Foo`，所以它能够调用私有方法栏。

Ruby 中 protected 的实现与 PHP 中的完全不同。受保护的方法可以由同一类的任何对象调用:

```
class Person
  attr_writer :mother
&nbsp;
  def initialize(name)
    @name = name
  end
&nbsp;
  def mother_name
    @mother.name if 
  end
&nbsp;
  protected
  def name
    @name
  end
end
&nbsp;
me = Person.new('Mal')
mum = Person.new('Jeni')
&nbsp;
me.mother = mum
&nbsp;
puts me.mother_name # => Jeni
puts me.name        # => NoMethodError: protected method `name' called for #
```

这里我们有一个 person 类，它只允许对名字进行受保护的访问。当我创建两个实例并将一个实例的母亲属性设置为另一个实例时，那么`mother_name`方法能够调用母亲变量的`name`属性，但是我不能调用对象本身范围之外的`name`属性。

这是 PHP 和 Ruby 的类属性和方法可见性之间的一些基本差异的简要概述——您能想到任何其他值得一提的核心差异吗？

## 分享这篇文章