# Java Bean 验证的 validateProperty()和 validateValue()在三分钟内完成

> 原文：<https://www.sitepoint.com/java-bean-validations-validateproperty-validatevalue-tutorial/>

### 目录 

*   [字段级约束的选择性验证](#selectivevalidationoffieldlevelconstraints)
*   [validate property()方法](#thevalidatepropertymethod)
*   [validate value()方法](#thevalidatevaluemethod)
*   [总结](#summary)
*   [评论](#comments)

虽然并不广为人知， [Java Bean Validation](http://beanvalidation.org/) 附带了`validateProperty()`和`validateValue()`方法，它们可以用于验证受约束类的单个字段和值，甚至可以在分配它们之前验证*。*

我假设您至少已经对如何使用该标准来验证对象有了基本的了解，但是如果您想更深入地了解它是如何工作的，请随意查看我的[上一篇文章](https://www.sitepoint.com/effective-domain-model-validation-with-hibernate-validator)，在这篇文章中，我深入研究了 Bean Validation 的核心特性。

## 字段级约束的选择性验证

在一个典型的用例中，像下面这样的受约束的域类将在一个单一的步骤中用 Bean 验证实现进行验证，比如 [Hibernate Validator](http://hibernate.org/validator/) 。

```
public class User {

    @NotEmpty(message = "Name may not be empty")
    @Size(min = 2, max = 32,
            message = "Name must be between 2 and 32 characters long")
    private String name;

    @NotEmpty(message = "Email may not be empty")
    @Email(message = "Email must be a well-formed email address")
    private String email;

    public User(){}

    public User(String name, String email) {
        this.name = name;
        this.email = email;
    }

    // setters and getters for name / email

} 
```

但是使用`validateProperty()`和`validateValue()`可以更有选择性地执行验证过程。

### `validateProperty()`法

考虑这样一种情况，当您只想验证先前的`User`类的特定字段，而不是一次性检查所有字段。在这种情况下，您可能希望使用`validateProperty()`方法，该方法使用一个受约束的对象和要验证的 bean 属性的名称来调用:

```
User user = new User("", "john@domain.com");
validator
        .validateProperty(user, "name")
        .stream()
        .forEach(violation -> System.out.println(violation.getMessage())) 
```

这里，将引发一个约束冲突，因为一个空字符串作为值被传递给了`name`字段。

同样,`email`字段可以按如下方式进行验证:

```
User user = new User("", "john@domain.com");
validator
        .validateProperty(user, "email")
        .stream()
        .forEach(violation -> System.out.println(violation.getMessage())) 
```

这不会引起违规，因为`"john@domain.com"`看起来像是有效的电子邮件地址。名称仍然为空的事实无关紧要，因为调用`validateProperty(user, "email")`将只验证`email`字段。

### `validateValue()`法

正如我们刚刚看到的，`validateProperty()`方法使得验证特定的约束类字段变得很容易。这种方法的一个潜在缺点是必须创建目标类的实例并将值赋给字段，因此使得整个验证代码有点臃肿。

为了避免这种情况，同样可以通过`validateValue()`方法来完成，而不必创建这样的实例。它是用受约束的类(`Class`本身，*而不是*的一个实例)、bean 属性的名称和值来调用的:

```
validator
         .validateValue(User.class, "name", "")
         .stream()
         .forEach(violation -> System.out.println(violation.getMessage())); 
```

在这种情况下，考虑到`name`字段不接受空字符串，一条违反约束的消息将被打印到控制台。如您所见，没有必要实例化目标类。

对于每个受约束的类字段，几乎可以做到同样的事情。这简单地表明，通过遵循更具选择性的策略，Bean 验证可以用于验证目标类的字段。

![Use validateProperty() and validateValue() for instant Java Bean Validation](img/e87d4addda3de0ce25b3ba186219cb29.png)

## 摘要

正如我们刚刚了解到的，用一个受约束类的实例和一个特定类的字段名调用`validateProperty()`将指示验证器只检查那个字段。用受约束的类(*不需要它的实例*)、字段名和值来调用它的对应物`validateValue()`将验证该值对于该字段是否有效。

更简单地说，整个验证过程的核心在于用正确的参数调用这些方法。是的，真的很简单。

当然，在使用 Bean 验证 API 时，有选择地验证类的字段只是冰山一角，因为标准打包了许多其他值得一看的漂亮特性。请记住，Java Bean 验证正在以相当快的速度发展，所以请确保查看官方文档并了解 T2 的最新消息。

## 分享这篇文章