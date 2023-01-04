# 用 Hibernate 验证器进行有效的域模型验证

> 原文：<https://www.sitepoint.com/effective-domain-model-validation-with-hibernate-validator/>

让我们面对它，[域模型](http://martinfowler.com/eaaCatalog/domainModel.html)验证一直是一个非常难驯服的野兽(很可能这不会很快改变),因为验证过程本身与它发生的上下文紧密相关。然而，通过使用一些外部类库来封装验证的机制是可行的，而不是让我们自己从头开始搞得一团糟。这正是 [Hibernate Validator](http://hibernate.org/validator) 发挥作用的地方，它是[Java Beans Validation 1.0/1.1](http://beanvalidation.org/1.0/spec/)(JSR 303)的参考实现，这是一个基于 Java EE 6 及更高版本附带的注释的健壮验证模型。

本文将带您以实用的方式使用 Hibernate Validator。

## 领域模型验证的上下文本质

独立于您选择的语言，构建一个丰富的领域模型是您作为开发人员可以处理的最具挑战性的任务之一。最重要的是，你必须确保水化模型的数据是有效的，从而保证它的完整性得到适当的维护。不幸的是，在许多情况下，使域对象在包含它的应用程序的上下文中有效绝非易事，这是由于验证过程本身固有的上下文特性。

换句话说:如果域对象的参数是由应用程序的环境提供的(例如，通过使用普通的[依赖注入](http://martinfowler.com/articles/injection.html)、[工厂](https://en.wikipedia.org/wiki/Factory_method_pattern)、[构建者](https://en.wikipedia.org/wiki/Builder_pattern)等等)，而不是由外部上层提供的，那么验证对象应该是简单的，并被限制在一个非常有限的范围内。

相反，如果讨论中的参数是从外层注入的(用户界面层就是一个很好的例子)，验证过程可能会很麻烦和乏味，在大多数情况下会导致大量样板代码分散在多个应用层。

最后，一切都归结为这个简单而基本的问题:是什么让一个域对象有效？在数据库中持久化或更新之前，或者在传递给其他层时，是否应该验证其状态？合乎逻辑的答案是:视情况而定。请记住，验证总是与上下文相关的！因此，无论您使用什么方法来决定您的域对象是否有效，Java Beans 验证都会使这个过程变得更容易。

## 用 JSR 303 介绍 Java Bean 验证

在 Java EE 6 之前，Java 没有提供一种通过集中机制来验证域类字段的标准方法。但自那以后，情况有所好转。Java Beans 验证规范使得有选择地验证类字段(甚至整个类)变得相当容易，方法是使用用一些直观的注释声明的约束。

在撰写本文的时候，JSR 303 只有两个兼容的实现，分别是 [Apache BVal](http://bval.apache.org) 和 [Hibernate Validator](http://hibernate.org/validator) 。后者是参考实现，可以作为独立的库使用，与流行的 ORM 框架完全分离。

说了这么多，现在让我们看看如何开始使用 Java Beans 验证/ Hibernate 验证器串联在现实世界中执行有效的域模型验证。

## 定义基本的领域模型

通常，展示如何利用 Java Beans 验证来验证域模型的一个好方法是用一个具体的例子。考虑到该标准实现了一个基于注释的验证模式，作为域模型一部分的类必须总是受到注释的约束。

在这种情况下，为了清楚起见，我想要验证的领域模型将只由一个天真的、贫血的类组成，它将是用户对象的蓝图:

```
public class User {

    private int id;

    @NotEmpty(message = "Name is mandatory")
    @Size(min = 2, max = 32,
            message = "Name must be between 2 and 32 characters long")
    private String name;

    @NotEmpty(message = "Email is mandatory")
    @Email(message = "Email must be a well-formed address")
    private String email;

    @NotEmpty(message = "Biography is mandatory")
    @Size(min = 10, max = 140,
            message = "Biography must be between 10 and 140 characters long")
    private String biography;

    public User(String name, String email, String biography) {
        this.name = name;
        this.email = email;
        this.biography = biography;
    }

    // Setters and Getters for name, email and biography

} 
```

除了在每个字段顶部声明的约束之外，没有什么值得讨论的。例如，`@NotEmpty(message = "Name is mandatory")`注释声明`name`字段必须是非空字符串。尽管这是不言自明的，但是`message`属性用于定义当受约束的字段在被验证时引发冲突时将显示的消息。更好的是，可以用参数定制许多约束，以便表达更精确的标准。考虑一下`@Size(min = 2, max = 32, message = "...")`注释。它准确地表达了信息的内容。不是火箭科学，对吧？

该规范提供了一些更方便的注释。如需完整列表，请点击查看[。](http://docs.oracle.com/javaee/6/tutorial/doc/gircz.html)

![Microscope Hibernate Validation](img/4e0f6a665f6e264625355dcabedd076d.png)

## 验证约束

至此，我们已经通过使用 Java Beans 验证成功定义了一个受约束的模型类，这一切都很好。但是你可能想知道这样做的实际好处是什么？简单的回答是:目前没有。当然，这个类已经准备好接受验证了，但是这里缺少的是一种机制，能够扫描注释，检查分配给受约束字段的值，并返回验证错误(或者用 JSR 303 术语来说，违反约束)。这正是 Hibernate Validator 的工作原理。

但是让我们坦率地说:如果我们看不到至少一个展示如何使用 Hibernate Validator 来验证`User`类的人为例子，那么上面的解释将只是技术上的胡言乱语:

```
Validator validator = Validation.buildDefaultValidatorFactory().getValidator();
User user = new User("John Doe", "no-mail", "");
validator
    .validate(user).stream()
    .forEach(violation -> System.out.println(violation.getMessage())); 
```

那真的很容易，不是吗？该代码片段首先通过静态的`buildDefaultValidatorFactory()`和`getValidator()`方法(Bean Validation API 的一部分)获取 Hibernate Validator 的一个实例，并使用`validate()`方法来验证一个可能无效的用户对象。在这种情况下，通过同时使用 streams 和 lambdas 在控制台中显示违反约束的情况。然而，通过使用标准的`for`循环，而不是更新的`forEach`，有可能得到相同的结果。

## 将验证逻辑封装在服务组件中

当然，开始到处删除`Validator`实例，并在多个地方验证受约束的类，甚至是在错误的地方，这非常简单(坦率地说，非常非常诱人)!但是这将是对[干原则](https://en.wikipedia.org/wiki/Don%27t_repeat_yourself)(也称为湿解决方案)的公然违反，这将导致跨几层的重复代码。是的，绝对是糟糕的设计。

相反，将 Hibernate Validator 封装在解耦服务组件的边界内会有效得多，因为它可能在任何地方都可以重用。简而言之，要将它包装在这样一个服务组件后面，我们需要做的只是一个简单的契约，通过一个接口定义，以及一个广泛通用的实现:

```
public interface EntityValidator<T> {

   Set<ConstraintViolation<T>> validate(T t);

}

public class BaseEntityValidator<T> implements EntityValidator<T> {

   protected final Validator validator;

   public BaseEntityValidator(Validator validator) {
       this.validator = validator;
   }

   public Set<ConstraintViolation<T>> validate(T t) {
       return validator.validate(t);
   }
} 
```

通过使用普通的[委托](https://en.wikipedia.org/wiki/Delegation_%28object-oriented_programming%29)，我们已经创建了一个有效的验证模块，它在构造函数中采用了一个符合 JSR 303 的实现，并使用它的`validate()`方法来验证一个给定的类型为`T`的对象。这里唯一值得指出的细节是，在对象被正确验证之后，所讨论的方法返回一个包含相应约束违反对象的[集合](https://docs.oracle.com/javase/7/docs/api/java/util/Set.html)。

乍一看，`BaseEntityValidator`类看起来相当简单。但这只是一个误导的印象，相信我。该类不仅利用了基于接口的多态性(又名[子类型多态性](https://en.wikipedia.org/wiki/Polymorphism_(computer_science)))，它还充当了验证器本身的适配器，因此可以有选择地向客户端代码公开验证器的本地方法，甚至添加特定于域的方法。

如果您想知道如何使用`BaseEntityValidator`类来验证一个用户对象，下面是应该如何执行这个过程:

```
// ideally the validator is injected but in this case we’ll create it explicitly
EntityValidator<User> userValidator =  new BaseEntityValidator<>(
        Validation.buildDefaultValidatorFactory().getValidator());
User user = new User("John Doe", "no-email", "");
validator
    .validate(user).stream()
    .forEach(violation -> System.out.println(violation.getMessage())); 
```

此外，在不同的上下文和场景中有足够的空间使用该类。例如，我们可以开发一个基本的 web 应用程序，类似于我在[Servlet API 教程](https://www.sitepoint.com/tutorial-building-web-app-with-java-servlets)中构建的应用程序，并以有效和高性能的方式验证通过 HTML 表单提交的用户数据。由于构建这样的 web 应用程序已经超出了本文的讨论范围，如果您想在 Web 领域中开始驾驭 Hibernate Validator，那么这项任务将留给您做家庭作业。

## 创建自定义验证约束和验证器

在这一点上，应该清楚 Hibernate Validator 是一个难以击败的竞争者，它以一种简单的方式验证域对象。但是还有更多:即使 JSR 303 默认带有一组健壮的约束违反，这将覆盖像你我这样的日常开发人员的最典型的验证需求，值得一提的是，它的核心功能可以通过自定义验证约束和验证器轻松扩展。

事实上，创建一个自定义验证约束和一个匹配的自定义验证器是一个显而易见的过程，可以归结为以下步骤:

1.  定义一个注释接口，它必须指定验证约束的目标、注释信息对编译器可用的时间长度(也称为[保留策略](http://docs.oracle.com/javase/6/docs/api/java/lang/annotation/Retention.html))，以及最后与约束相关联的自定义验证类。
2.  创建自定义验证类本身。

通常，动手操作的例子是掌握这一过程的基本逻辑的最佳方式。因此，假设我们想要对`User`类应用一个定制的电子邮件验证约束，而不是使用 JSR 303 中包含的默认约束。在这种情况下，首先我们需要定义相应的注释接口，如下所示:

```
@Target({ElementType.METHOD, ElementType.FIELD})
@Retention(RetentionPolicy.RUNTIME)
@Constraint(validatedBy = EmailValidator.class)
public @interface ValidEmail {

    String message()  default "Email must be a well-formed address";

    Class<?>[] groups() default {};

    Class<? extends Payload>[] payload() default{};
} 
```

如上所示，`ValidEmail`注释告诉编译器，约束违反应该保留到运行时(`@Retention(RetentionPolicy.RUNTIME)`)，并且适用于方法和字段`(@Target({ElementType.METHOD, ElementType.FIELD})`。这里可以清楚地看到,`@Constraint`注释将自定义约束绑定到了自定义的`EmailValidator`类，我们将在检查注释的方法后查看该类。

最后，`message()`方法当然定义了当违反约束时必须显示的消息。

此外，`groups()`方法允许指定在调用验证器时应该验证哪些约束，基于[每组](https://docs.jboss.org/hibernate/stable/validator/reference/en-US/html_single/#chapter-groups)。默认情况下，默认组中的所有约束都被选中，这意味着无论目标对象处于哪个生命周期阶段，每个约束都将被验证。然而，可以以简单接口的形式建立不同的组，并根据特定的生命周期阶段指定哪些约束必须被验证。为了简洁起见，上面的例子使用了默认的组。

最后，`payload()`方法可以用于附加[有效负载对象](http://grepcode.com/file/repository.jboss.com/maven2/javax.validation/validation-api/1.0.0.GA/javax/validation/Payload.java)，它保存了关于约束的附加信息，并且可以在验证目标对象时获取。

现在让我们转向实现实际验证的`EmailValidator`类。下面是一个简单的实现:

```
public class EmailValidator implements ConstraintValidator<ValidEmail, String> {

    private static final Pattern VALID_EMAIL_PATTERN = Pattern.compile(
            "^[A-Z0-9._%+-]+@[A-Z0-9.-]+\\.[A-Z]{2,6}$",
            Pattern.CASE_INSENSITIVE);

    @Override
    public void initialize(ValidEmail constraintAnnotation) {
        // can be used to set the instance up for validation
    }

    @Override
    public boolean isValid(
            String email, ConstraintValidatorContext constraintValidatorContext) {
        Matcher matcher = VALID_EMAIL_PATTERN.matcher(email);
        return matcher.find();
    }

} 
```

这里只有几个细节值得强调:第一个是定制验证器必须实现本机`ConstraintValidator`接口，并将定制约束的类型(这里是`ValidEmail`)和被约束的类型(`String`)指定为类型参数。第二个细节是`isValid()`方法本身，它验证所提供的电子邮件地址。

最后但同样重要的是，必须重构`User`类中的`email`字段的声明，这样它可以将自定义验证约束绑定到自定义`EmailValidator`类，如下所示:

```
@ValidEmail
private String email; 
```

最后，验证用户对象看起来与使用默认电子邮件验证程序时完全一样。

这很容易理解，对吧？当然，我并不是说您总是需要通过创建定制的约束和验证器来搅乱您的生活，因为这将使 Java Beans 验证的功能降级到几乎为零。然而，从设计的角度来看，知道该标准提供了一个不错的定制水平是很有用的。

## 结论

至此，您已经了解了如何并排使用 Java Beans 验证和 Hibernate 验证器的基本知识，以便以一种简单的方式验证您的域对象。此外，您看到了用定制约束和定制验证器来扩展规范的核心功能是非常容易的，在这些用例中，默认的约束和验证器不能满足您的个人需求。此外，请记住 JSR 303 正在以相当快的速度进行中(事实上 Java Beans Validation 2.0 的发布指日可待)，所以请确保及时了解[最新消息](http://beanvalidation.org/news)。

那么，在利用标准带来的功能时，我们需要知道的就是这些吗？嗯，从起点来看，确实如此。不过，与许多其他语言相关的特性一样，它并不是治愈您的代码可能遭受的所有潜在验证弊病的灵丹妙药，所以像往常一样，以正确和有意识的方式使用它取决于我们。

很有可能，使用规范的最大好处在于它允许容易地实现高度解耦的验证组件，这些组件可以在任何地方重用。如果这个唯一的理由还不足以让您立即开始使用 Java Beans 验证，那么肯定什么也不会有。那么，你还在等什么？

## 分享这篇文章