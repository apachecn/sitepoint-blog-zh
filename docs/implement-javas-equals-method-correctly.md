# 如何正确实现 Java 的 equals 方法

> 原文：<https://www.sitepoint.com/implement-javas-equals-method-correctly/>

在 SitePoint，我们一直在寻求扩大我们涵盖的主题范围。最近，我们把目光投向了探索 Java 世界。如果你是一名优秀的 Java 开发人员，想为我们的报道做点贡献，[联系](mailto:editor@sitepoint.com)了解一些你想写的文章的想法。

任何 Java 类的一个基本方面是它的等式定义。它是由一个类的`equals`方法决定的，对于一个正确的实现需要考虑几件事情。让我们检查一下，这样我们才能做对！

**注意，实现`equals`总是意味着`hashCode`也必须被实现！我们将在[的另一篇文章](https://www.sitepoint.com/how-to-implement-javas-hashcode-correctly/)中讨论这个问题，所以请务必在这篇文章之后阅读。**

## 身份与平等

看看这段代码:

```
String some = "some string";
String other = "other string"; 
```

我们有两根弦，它们显然是不同的。

这两个呢？

```
String some = "some string";
String other = some;
boolean identical = some == other; 
```

这里我们只有一个字符串实例，`some`和`other`都引用了它。在 Java 中，我们说`some`和`other`是*相同的*，因此`identical`返回一个[布尔值](https://www.sitepoint.com/boolean-data-type/)，即`true`。

这个怎么样？

```
String some = "some string";
String other = "some string";
boolean identical = some == other; 
```

现在，`some`和`other`指向不同的实例，不再相同，所以`identical`为假。(本文我们先忽略[串实习](http://javatechniques.com/blog/string-equality-and-interning/)；如果这让您感到困扰，假设每个字符串都被包装在一个`new String(...)`中。)

但是他们*确实*有*一些*的关系，因为他们两个“有相同的价值”。用 Java 术语来说就是*等于*，用`equals`来校验:

```
String some = "some string";
String other = "some string";
boolean equal = some.equals(other); 
```

在这里，`equals`就是`true`。

**一个变量的*身份*(也叫*引用相等*)是由它持有的引用定义的。如果两个变量拥有相同的引用，则它们是相同的。这是用`==`检查的。**

**变量的*等式*由它引用的值定义。如果两个变量引用相同的值，它们的*等于*。这是用`equals`检查的。**

但是“同值”是什么意思呢？事实上，正是`equals`的实现决定了“相同”。在`Object`中定义了`equals`方法，因为所有的类都继承了它，所以所有的类都有那个方法。

`Object`中的实现检查同一性(注意相同的变量也是相等的)，但是许多类用更合适的东西覆盖了它。例如，对于字符串，它比较字符序列，对于日期，它确保两者都指向同一天。

许多数据结构，最著名的是 Java 自己的集合框架，使用`equals`来检查它们是否包含元素。

例如:

```
List<String> list = Arrays.asList("a", "b", "c");
boolean contains = list.contains("b"); 
```

变量`contains`是`true`,因为虽然`"b"`的实例不相同，但它们是相等的。

(这也是`hashCode`发挥作用的点。)

## 关于平等的思考

任何对`equals`的实现都必须遵守特定的契约，否则类的平等性将被模糊定义，各种意想不到的事情将会发生。我们一会儿将看形式定义，但是让我们首先讨论等式的一些性质。

当我们在日常生活中遇到它时，想想它可能会有帮助。假设我们比较笔记本电脑，如果它们有相同的硬件规格，就认为它们是平等的。

1.  有一个性质非常琐碎，几乎不值得一提:每一个事物都等于它自己。咄。
2.  还有另一个，并不太鼓舞人心:如果一个东西等于另一个，另一个也等于第一个。很明显，如果我的笔记本电脑和你的一样，你的也和我的一样。
3.  这个更有趣:如果我们有三个东西，第一个和第二个相等，第二个和第三个也相等，那么第一个和第三个也相等。同样，这在我们的笔记本电脑例子中很明显。

那是一次徒劳的练习，对吗？不是这样的！我们刚刚学习了等价关系的一些基本代数性质。不，等等，别走！这已经是我们所需要的了。因为任何具有上述三个性质的关系都可以称为等式。

是的，*我们可以想出的任何一种比较事物并具有上述三个性质的方法，都可能是我们确定这些事物是否相等的方法。相反，如果我们忽略任何东西，我们就不再有有意义的平等。*

![equality](img/b32f1bd9929fa18c02949d2e19f66ed9.png)

## `equals`合同

合同不过是我们在上面看到的一种形式。
引用[来源](https://docs.oracle.com/javase/8/docs/api/java/lang/Object.html#equals-java.lang.Object-):

> equals 方法在非空对象引用上实现等价关系:
> 
> *   它是*自反的*:对于任何非空的引用值`x`，`x.equals(x)`都应该返回`true`。
> *   是*对称*:对于任何非空的参考值`x`和`y`，`x.equals(y)`应该返回`true`当且仅当`y.equals(x)`返回 true。
> *   是*传递*:对于任何非空的参考值`x`、`y`、`z`，如果`x.equals(y)`返回`true`、`y.equals(z)`返回`true`，那么`x.equals(z)`应该返回`true`。
> *   是*一致*:对于任何非空的引用值`x`和`y`，多次调用`x.equals(y)`一致返回`true`或一致返回`false`，前提是`equals`对对象的比较中使用的信息没有被修改。
> *   对于任何非空的引用值`x`，`x.equals(null)`应该返回`false`。

到现在为止，前三个应该很熟悉了。其他要点更多的是技术性的:没有一致性，数据结构的行为就会不稳定，等于 null 不仅没有意义，而且会使许多实现变得复杂。

## 实施`equals`

对于具有字符串字段`firstName`和`lastName`的类`Person`，这将是实现`equals`的常见变体:

```
@Override
public boolean equals(Object o) {
    // self check
    if (this == o)
        return true;
    // null check
    if (o == null)
        return false;
    // type check and cast
    if (getClass() != o.getClass())
        return false;
    Person person = (Person) o;
    // field comparison
    return Objects.equals(firstName, person.firstName)
            && Objects.equals(lastName, person.lastName);
} 
```

让我们一个一个地过一遍。

### 签名

`equals`带个`Object`很重要！否则，会发生意外行为。

例如，假设我们将像这样实现`equals(Person)`:

```
public boolean equals(Person person) {
    return Objects.equals(firstName, person.firstName)
            && Objects.equals(lastName, person.lastName);
} 
```

在一个简单的例子中会发生什么？

```
Person elliot = new Person("Elliot", "Alderson");
Person mrRobot = new Person("Elliot", "Alderson");
boolean equal = elliot.equals(mrRobot); 
```

那么`equal`就是`true`。现在呢？

```
Person elliot = new Person("Elliot", "Alderson");
Object mrRobot = new Person("Elliot", "Alderson");
boolean equal = elliot.equals(mrRobot); 
```

现在是`false`。[窟](https://www.destroyallsoftware.com/talks/wat)？！也许和我们预想的不太一样。

原因是 Java 调用了`Person.equals(Object)`(从`Object`继承而来，它检查身份)。为什么？

Java 选择调用哪个重载方法的策略不是基于参数的运行时类型，而是基于其声明的类型。(这是一件好事，因为否则静态代码分析(如调用层次结构)将无法工作。)所以如果`mrRobot`被声明为`Object`，Java 调用`Person.equals(Object)`而不是我们的`Person.equals(Person)`。

请注意，大多数代码，例如所有集合，都将我们的 persons 作为对象来处理，因此总是调用`equals(Object)`。所以我们最好确保我们提供的实现带有那个签名！我们当然可以创建一个专门化的`equals`实现，如果我们更喜欢的话，可以从我们更通用的实现中调用它。

### 自检

相等是任何类的基本属性，它可能会被频繁调用，例如在查询集合的[紧密循环](https://en.wiktionary.org/wiki/tight_loop)中。因此，它的性能至关重要！在我们的实现开始时的自检就是这样:性能优化。

`java
if (this == o)
return true;` 

看起来它应该实现反身性，但是如果它不实现反身性，那么接下来的检查将会非常奇怪。

### 零检查

任何实例都不应该等于 null，所以我们要确保这一点。同时，它保护代码不受`NullPointerException` s 的攻击。

```
if (o == null)
    return false; 
```

它实际上可以包含在下面的检查中，就像这样:

```
if (o == null || getClass() != o.getClass())
    return false; 
```

### 类型检查和转换

接下来，我们必须确保我们看到的实例实际上是一个人。这是另一个棘手的细节。

```
if (getClass() != o.getClass())
    return false;
Person person = (Person) o; 
```

我们的实现使用了`getClass`，它返回了`this`和`o`所属的类。它要求它们是相同的！这意味着如果我们有一个类`Employee extends Person`，那么`Person.equals(Employee)`将永远不会返回`true`——即使两者有相同的名字。

这可能是意料之外的。

带有新字段的扩展类比较起来不太好可能是合理的，但是如果该扩展只添加了行为(可能是日志或其他非功能性细节)，它应该能够等同于其超类型的实例。如果一个框架在运行时旋转新的子类型(例如 Hibernate 或 Spring ),这就变得尤为重要，因为它可能永远不会等于我们创建的实例。

另一种方法是使用`instanceof`操作符:

```
if (!(o instanceof Person))
    return false; 
```

`Person`的子类型实例通过了检查。因此，它们继续进行现场比较(见下文),结果可能是相等的。这解决了我们上面提到的问题，但也带来了新的麻烦。

说`Employee extends Person`并添加一个附加字段。如果它覆盖了从`Person`继承的`equals`实现，并且包含了额外的字段，那么`person.equals(employee)`可以是`true`(因为`instanceof`)但是`employee.equals(person)`不能(因为`person`错过了那个字段)。这显然违反了对称性要求。

似乎有一种方法可以解决这个问题:`Employee.equals`可以检查它是否与具有该字段的实例相比较，然后才使用它(这有时被称为*片比较*)。

但这也行不通，因为它破坏了传递性:

```
Person foo = new Person("Mr", "Foo");
Employee fu = new Employee("Mr", "Foo", "Marketing");
Employee fuu = new Employee("Mr", "Foo", "Engineering"); 
```

显然这三个实例共享相同的名字，所以`foo.equals(fu)`和`foo.equals(fuu)`是`true`。通过传递性`fu.equals(fuu)`也应该是`true`，但如果第三个字段(显然是部门)包含在比较中，则不是这样。

真的没有办法在不违反自反性或传递性的情况下进行切片比较，这一点更难分析。(如果你认为你找到了，再检查一遍。然后让你的同事检查一下。如果还确定的话， [ping 我](https://twitter.com/nipafx)。；) )

所以我们最后有两个选择:

*   使用`getClass`并注意类型及其子类型的实例不能相等。
*   使用`instanceof`但使`equals`成为 final，因为没有办法正确地覆盖它。

哪一个更有意义真的要视情况而定。我个人更喜欢`instanceof`，因为它的问题(不能在继承类中包含新字段)发生在声明站点而不是使用站点。

### 现场比较

哇，工作量真大！我们所做的只是解决了一些棘手的案子！所以让我们最终进入测试的核心:比较字段。

不过，这很简单。在绝大多数情况下，所要做的就是挑选应该定义类的相等性的字段，然后对它们进行比较。将`==`用于基本体，将`equals`用于对象。

如果任何字段可以为空，额外的检查会大大降低代码的可读性:

```
return (firstName == person.firstName
        || firstName != null && firstName.equals(person.firstName))
    && (lastName == person.lastName
            || lastName != null && lastName.equals(person.lastName)) 
```

而这已经利用了`null == null`就是`true`这个非显而易见的事实。

使用 Java 的实用方法`Objects.equals`(或者，如果您还没有使用 Java 7，使用 Guava 的`Objects.equal`)要好得多:

```
return Objects.equals(firstName, person.firstName)
        && Objects.equals(lastName, person.lastName); 
```

它做完全相同的检查，但可读性更好。

## 摘要

我们已经讨论过同一性的区别(必须是同一个参照物；用`==`检查)和等式(可以是对“相同值”的不同引用)；检查了`equals`并继续仔细研究如何实现`equals`。

让我们把这些碎片拼起来:

*   确保覆盖`equals(Object)`,这样我们的方法总是被调用。
*   在简单边缘情况下，包括对提前返回的 self 和 null 检查。
*   使用`getClass`允许子类型有自己的实现(但不允许子类型之间的比较)，或者使用`instanceof`并使`equals`成为最终的(并且子类型可以相等)。
*   使用`Objects.equals`比较所需字段。

或者让您的 IDE 为您生成所有内容，并在需要的地方进行编辑。

## 最后的话

我们已经看到了如何正确地实现`equals`(很快[就会看到`hashCode`](https://www.sitepoint.com/how-to-implement-javas-hashcode-correctly/) )。但是如果我们使用的是我们无法控制的类呢？如果他们对这些方法的实现不符合我们的需求或者完全错误，该怎么办？

[LibFX](http://libfx.codefx.org) 来救援了！(声明:我是作者。)该库包含[转换集合](http://blog.codefx.org/libfx/transforming-collections/)，它们的一个特性是允许用户指定她需要的`equals`和`hashCode`方法。

## 分享这篇文章