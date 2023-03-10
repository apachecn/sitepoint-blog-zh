# Java 8 中的组合子模式

> 原文：<https://www.sitepoint.com/combinator-pattern-with-java-8/>

本文原载于 [gtrefs.github.io](https://gtrefs.github.io/code/combinator-pattern/) 。我们在这里重新发布它，因为它是 Java 8 如何让我们更容易接受强大的函数式编程概念的一个很好的例子。

在函数式编程中，[组合子模式](https://wiki.haskell.org/Combinator_pattern)是众所周知的。想法是将原语组合成更复杂的结构。在我上次在 [majug](http://www.majug.de/2016/07/21/functions-for-a-greater-good/) 的演讲中，我展示了一种如何在 Java 8 中使用这种模式的方法。在本帖中，我们将看看这个设计。

**在我们开始之前**

如果你知道 Java 8 中的函数和高阶函数，那么你已经准备好阅读这篇文章了。如果没有，推荐你看看这个[演示](https://github.com/lambdafy/presentation)。在那里你会发现由 [Artem](https://www.twitter.com/_akozlov) 对 Java 8 中函数的精彩介绍。**代码**可以在这里[找到。](https://github.com/gtrefs/blog-combinator)

## 关于原语和组合符

像函数式编程中的许多结构一样，原语和组合符是抽象的名称。维基百科提供了对语言原语的描述:

> 在计算中，语言原语是编程语言中最简单的元素。[……]语言中表达式的一个原子元素。
> ——[维基百科](https://en.wikipedia.org/wiki/Language_primitive)

同样，combinator 模式中使用的原语是域中最简单的元素。例如，整数域中的加法和乘法。组合器提供了将原语和/或已组合的结构组合成更复杂的结构的方法。比如结合乘法和加法。由于这种模式的声明性，代码本身使用了许多领域术语。

![Published by Caroline Davis2010 under CC-BY 2.0](img/27bc3ab84de0fb89175231212bf0b7d2.png)

[发表](https://www.flickr.com/photos/53416677@N08/4972914961/ "Source on Flickr")由 [Caroline Davis2010](https://www.flickr.com/photos/53416677@N08/ "Author on Flickr") 在[下 CC-BY 2.0](https://creativecommons.org/licenses/by/2.0/ "Link to Licence")

## 用组合子验证

假设我们的任务是验证用户。如果用户名不为空并且电子邮件包含一个`@`符号，则用户有效。对此建模的一个直接方法是向相应的实体添加查询方法:

```
public class User{
   public final String name;
   public final int age;
   public final String email;

   public User(String name, int age, String email){
     this.name = name;
     this.age = age;
     this.email = email; 
   }

   public boolean isValid(){
     return nameIsNotEmpty() && eMailContainsAtSign();
   }

   private boolean nameIsNotEmpty(){
     return !name.trim().isEmpty();
   }

   private boolean eMailContainsAtSign(){
     return email.contains("@");
   }
}

new User("Gregor", 30, "nicemail@gmail.com").isValid(); // true 
```

虽然这读起来不错，但也有一些缺点。当验证规则改变时会发生什么？例如，对于某些用例，用户必须大于 14 岁。在这种情况下，我们需要采用`isValid`方法。所以这看起来像是违反了 [SRP](https://en.wikipedia.org/wiki/Single_responsibility_principle) 。在我们重构并将验证部分提取到一个自己的类中之前，让我们坐下来想想我们想要表达什么。

当且仅当满足一组给定的规则时，用户才是有效的。规则描述了用户必须拥有的属性。不同的规则可以组合成更复杂的规则。应用规则会产生描述结果的验证结果。现在，让我们假设一个布尔值`true`表示一个用户是有效的。用更具功能性的术语来说，我们可以这样表达:

```
public interface UserValidation extends Function<User, Boolean>{}

UserValidation nameIsNotEmpty = user -> !user.name.trim().isEmpty();
UserValidation eMailContainsAtSign = user -> user.email.contains("@");
User gregor = new User("Gregor", 30, "nicemail@gmail.com");

nameIsNotEmpty.apply(gregor) && eMailContainsAtSing.apply(gregor); // true 
```

这里有一些样板代码。为什么我们必须自己创建`nameIsNotEmpty`和`eMailContainsAtSign`规则？为什么我们必须手动合并结果？为什么只有一个应用程序行有 4 行初始化代码？

尽管代码有点冗长，但它包含了组合子模式的所有构建模块:`nameIsNotEmpty`和`eMailContainsAtSign`是原语，`&&`是组合子。有了 Java 8，我们能够将原语和组合符移入`UserValidation`类型。

```
interface UserValidation extends Function<User, Boolean> {
    static UserValidation nameIsNotEmpty() {
        return user -> !user.name.trim().isEmpty();
    }

    static UserValidation eMailContainsAtSign() {
        return user -> user.email.contains("@");
    }

    default UserValidation and(UserValidation other) {
        return user -> this.apply(user) && other.apply(user);
    }
}

User gregor = new User("Gregor", 30, "nicemail@gmail.com");
nameIsNotEmpty().and(eMailContainsAtSign()).apply(gregor); // true 
```

原语被转换为静态方法，组合符被转换为默认方法。两个原语`nameIsNotEmpty`和`eMailContainsAtSign`都返回一个以`UserValidation`为目标类型的 lambda 表达式。与原语不同的是，`and`组合器通过使用`&&`操作符来合成`UserValidation`。

使用这种模式有两个重要的观察点。首先，合并期间没有应用程序。这意味着，首先构造一个`UserValidation`，然后执行它。这种区别使得一个`UserValidation`适用于许多用户。第二，`UserValidation`没有自己的国家。这意味着，一个`UserValidation`可以应用在没有竞争条件的并行环境中。例如，以下代码并行执行用户验证。

```
List<User> users = findAllUsers()
    .stream().parallel()
    .filter(nameIsNotEmpty().and(eMailContainsAtSign())::apply) // to Predicate
    .collect(Collectors.toList()); 
```

## 验证结果推理

你认为`Boolean`作为验证结果的类型是一个合理的选择吗？一般来说，内置语言类型通常不是表示领域信息的好选择。

两个原因:首先，在 Java 中，几乎不可能改编我们不拥有的代码。我们不能向`Boolean`类型添加查询方法。这使得很难确定哪些规则使结果无效。第二，内置类型的语义是隐式的，并且是上下文特定的。例如，在验证上下文中`true`意味着一个用户是有效的；即所有期望的特性都存在。当我们将验证结果传递给其他上下文时，这个假设可能不成立。

需要一种新的类型`ValidationResult`,包含用户在特定规则下无效的原因。

```
interface ValidationResult{
    static ValidationResult valid(){
        return ValidationSupport.valid();
    }

    static ValidationResult invalid(String reason){
        return new Invalid(reason);
    }

    boolean isValid();

    Optional<String> getReason();
}

private final static class Invalid implements ValidationResult {

    private final String reason;

    Invalid(String reason){
        this.reason = reason;
    }

    public boolean isValid(){
        return false;
    }

    public Optional<String> getReason(){
        return Optional.of(reason);
    }

    // equals and hashCode on field reason
}

private static final class ValidationSupport {
    private static final ValidationResult valid = new ValidationResult(){
        public boolean isValid(){ return true; }
        public Optional<String> getReason(){ return Optional.empty(); }
    };

    static ValidationResult valid(){
        // since all valid results are indistinguishable,
        // the same instance can be reused to reduce garbage
        return valid;
    }
} 
```

一个`ValidationResult`不是`valid`就是`Invalid`。两个`ValidationResult`的相等由它们的属性和类型的相等来定义；即两个`Invalid`实例相等当且仅当它们具有相同的无效原因。匿名单例实例在`valid`案例中产生相同的含义。这意味着，`valid`和`Invalid`是[值对象](https://en.wikipedia.org/wiki/Value_object)，为了使用新的`ValidationResult`，我们必须对`UserValidation`进行调整。

```
interface UserValidation extends Function<User, ValidationResult> {
    static UserValidation nameIsNotEmpty() {
        return holds(user -> !user.name.trim().isEmpty(), "Name is empty.");
    }

    static UserValidation eMailContainsAtSign() {
        return holds(user -> user.email.contains("@"), "Missing @-sign.");
    }

    static UserValidation holds(Predicate<User> p, String message){
        return user -> p.test(user) ? valid() : invalid(message);
    }

    default UserValidation and(UserValidation other) {
        return user -> {
            final ValidationResult result = this.apply(user);
            return result.isValid() ? other.apply(user) : result;
        };
    }
}

UserValidation validation = nameIsNotEmpty().and(eMailContainsAtSign());
User gregor = new User("", 30, "mail@mailinator.com");

ValidationResult result = validation.apply(gregor);
result.getReason().ifPresent(System.out::println); // Name is empty. 
```

原语`holds`测试给定的`Predicate`并返回`valid`或新的`Invalid`。原语`nameIsNotEmpty`和`eMailContainsAtSign`已经被相应地重构。如果`this`产生`valid`，则`and`组合器只计算`other`的结果。例如，如果`nameIsNotEmpty`已经是`Invalid`，则`eMailContainsAtSign`不会针对给定用户进行评估。

从开发人员的角度来看，与普通的`Boolean`值相比，这个小小的 API 变化传达了更多的信息。然而，设计中存在一些缺陷。例如，在一个 web 上下文中，人们想要评估所有的验证规则，以便得到一个全面的报告，但是`and`操作符是短路。因此，需要一个`all`组合器来评估所有的验证规则。

这留给我们以下练习:

> 实现一个`all`组合器，它执行所有给定的`UserValidation`并收集结果。使用下面的方法声明。
> 
> ```
> static UserValidtion all(UserValidation... validations){ 
>   // Your code here 
> } 
> ```

## 摘要

这篇文章展示了 Java 8 中 combinator 模式的一种可能的设计。我们讨论了作为构建模块的原语和组合符。一个验证用例展示了如何将模式应用到一个特定的领域。最后，我们对验证的结果进行了推理。

应用良好的组合子模式有一些好处。从开发人员的角度来看，API 是由领域术语组成的。例如，原语`nameIsNotEmpty`和`eMailContainsAtSign`清楚地说明了它们的用途。它加强了单一责任原则，使关注点更容易分离。此外，组合和应用阶段之间有明显的区别。首先构造一个实例，然后执行它。这使得该模式适用于并行环境。

## 分享这篇文章