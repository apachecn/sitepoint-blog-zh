# 来自. NET POV 的 Ruby 中的属性和方法

> 原文：<https://www.sitepoint.com/properties-and-methods-in-ruby-from-a-net-pov/>

C#开发者习惯于使用*字段*、*属性*和*方法*。但是在理解这些在 Ruby 中是如何工作的时候会有一些困惑。让我们快速复习一下 C#吧。

## C#中的字段、属性和方法

*字段*是作用域为对象(实例字段)或类(静态字段)的变量。作为最佳实践，开发人员不要公开公开字段，因为这样做会公开太多的类内部信息。实例字段声明如下:

```
public class Customer
{
  private int _age;
}
```

请注意，“年龄”前的下划线只是一些 C#开发人员遵循的一个常见约定。为了公开这样一个字段，需要创建一个“属性”，它被定义为一对 *getter* 和 *setter* 方法:

```
public class Customer
{
  private int _age;

  public int Age 
  {
    get { return _age; }
    set { _age = value; }
  }
}
```

一个属性只能用一个 *get* 方法来定义，以防它只能被“访问”，而不能被“分配”。如果 getters 和 setters 只是简单地获取或设置一个字段的值，那么可以使用 C#的一个特性，叫做*自动属性*:

```
public class Customer
{
  public int Age { get; set; } 
}
```

当一个属性被这样定义时，C#编译器产生类似于我之前展示的代码(它创建了支持字段，以及 getter/setter 的实现)。

## 现在，在 Ruby 中这看起来像什么？

下面的帖子简要介绍了实例和类变量: [NET to Ruby:方法和变量](https://www.sitepoint.com/net-to-ruby-methods-and-variables/)。为了声明一个实例变量，我们在一个类的初始化器(它的*构造器*)中定义它，在它前面加上一个@:

```
class Customer

  def initialize
    @age = 18
  end
end
```

在这一点上，@age 与 C#中的私有字段非常相似，因为它可以在定义它的类内的任何地方被访问，但不能在类外被访问。为了从外部访问或分配它，我们必须为它定义访问器方法:

```
class Customer

  def initialize
    @age = 18
  end

  def age
    @age
  end

  def age=(value)
    @age = value
  end
end
```

与在 C#中定义包含 set 和 get 方法的 Age 属性不同，在这里我们创建了一个返回@age 实例变量的值的 *age* 方法，以及一个接受一个值并将其赋给实例变量的 *age=* 方法。

与 C#的 *auto properties* 类似，Ruby 也提供了一个允许轻松创建访问器的构造。上面的代码可以重写如下:

```
class Customer

  def initialize
    @age = 18
  end

  attr_accessor :age
end
```

*attr_accessor* 不是 Ruby 中的关键字；它被称为“类宏”，是一种向类添加动态行为的元编程技术。在这种情况下，添加的是*读者*和*作者*访问器。我们还可以分别使用 attr_reader 或 attr_writer 来添加 reader 或 write 访问器。

## 做更多事情的存取器…

在 C#中创建一个属性是很常见的，在这个属性中，访问器可以做的不仅仅是获取和/或设置实例变量的值。例如，getter 可能需要在返回之前连接值:

```
public class Customer
{
  private string _firstName;
  private string _lastName;

  public string FullName 
  {
    get { return string.Format("{0} {1}", _firstName, _lastName); }
  }
}
```

在上面的例子中，FullName 属性有一个连接 _firstName 和 _lastName 字段的 getter。这种做法在 Ruby 中也很常见:

```
class Customer

  def initialize
    @first_name = "Claudio"
    @last_name = "Lassala"
  end

  def full_name
    "#{@first_name} #{@last_name}"
  end
end
```

请记住，圆括号在 Ruby 中是可选的，所以像上面的 full_name 这样的方法可以这样调用:

```
cust = Customer.new
puts cust.first_name
```

## 用元编程清理代码

我们经常给一个类添加布尔属性，这样我们可以向一个对象询问某些事情。举个例子:

```
public class User
{
  private string _profile;

  public User(string profile)
  {
    _profile = profile;
  }

  public bool IsDoctor
  {
    get { return _profile == "doctor"; }
  }

  public bool IsPatient
  {
    get { return _profile == "patient"; }
  } 
}
```

现在假设除了“医生”和“病人”之外，还有更多类型的配置文件，那么在这种情况下，User 类最终将为每种类型的配置文件提供一个属性。类似的类可以在 Ruby 中创建，如下所示:

```
class User

  def initialize(profile)
    @profile = profile
  end

  def doctor?
    @profile == 'doctor'
  end

  def patient?
    @profile == 'patient'
  end
end
```

这个“？”是怎么回事*医生的后缀？*和*病人？*方法？通常，当 Ruby 中的一个方法要返回一个布尔值时，这个方法被称为*谓词方法*，通常的做法是用“？”后缀。所以我们不写“if user.is_doctor”这样的代码，而是写“if user.doctor？”。

如果用户类真的有可能拥有几个方法，比如 *doctor？*和*病人？*(每种类型的概要文件一个)，该类可以被重写以利用一些元编程。这是一个可能的例子:

```
class User

  def initialize(profile)
    @profile = profile
  end

  def method_missing(method_name, *args, &block)
    method_name = method_name.to_s
    if method_name.ends_with?("?")
      return @profile == method_name.chop
    end

    super if self.responds_to?(method_name)
  end
end
```

注意到这两位*医生了吗？*和*病人？*方法不见了，增加了一个*方法 _ 缺失*方法。现在的情况是，每当一些代码查询用户对象时，比如“if user.doctor？”，类中缺少这样的方法，调用 *method_missing* 方法。在上面的例子中，method_missing 检查对对象(“method_name”)调用的方法是否以“？”结尾，如果是，它将@profile 与它进行比较。如果支持新的概要文件，就不需要向 User 类添加属性，因为它已经准备好处理它了。

## 摘要

理解这一事实很重要，Ruby 中没有真正的“属性”(而是有方法)，这些属性可以自动创建(使用 attr_accessor、attr_reader、attr_writer 类宏)或手动创建(通过简单地定义方法)，因为这些东西在每个应用程序中都经常使用。

我希望您现在对 C#和 Ruby 在属性方面的比较有了更好的理解。如果您有任何问题或见解，请发表评论。

## 分享这篇文章