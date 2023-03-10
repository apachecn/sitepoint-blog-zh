# 对方法参数和返回值使用 Java Bean 验证

> 原文：<https://www.sitepoint.com/using-java-bean-validation-method-parameters-return-values/>

### 目录 

*   [验证方法参数](#validatingmethodarguments)
*   [验证方法返回值](#validatingmethodreturnvalues)
*   [验证构造函数参数](#validatingconstructorarguments)
*   [使用 Java EE 拦截器进行自动验证](#automaticvalidationwithjavaeeinterceptors)
*   [总结](#summary)
*   [评论](#comments)

Java 的静态类型系统是一个健壮的机制，允许你指定一个方法的前置条件和后置条件(从调用者的角度来看:提供什么和期望什么),编译器保证这些条件[在运行时](https://www.sitepoint.com/types-are-mightier-than-tests/)得到满足。Java Bean Validation 使这种约束模型更加接近特定的领域。它提供了一个成熟的验证 API，不仅允许通过一组直观的验证约束来验证类的字段。正如我们将在这篇文章中看到的，它还让开发人员能够在常规方法和构造函数中重用参数和返回值中的这些约束，并以[契约设计](https://en.wikipedia.org/wiki/Design_by_contract)的方式验证它们——手动或通过 [Java EE 拦截器自动验证。](http://docs.oracle.com/javaee/6/tutorial/doc/gkeed.html)

如果你想一窥该标准的核心特性，看看[这篇介绍性文章](https://www.sitepoint.com/effective-domain-model-validation-with-hibernate-validator/)。那里解释的验证使用的是`Validator` API，而本文调用的是新的`ExecutableValidator`接口提供的方法——两者都是符合标准的实现的一部分，如 [Hibernate Validator](http://hibernate.org/validator/) 和 [Apache BVal](http://bval.apache.org/) 。

## 验证方法参数

我们将在这篇综述中介绍的第一个方法是`validateParameters()`，它验证给定方法的参数。考虑下面的例子，它将一些基本约束绑定到下面的`User`类的设置器:

```
public class User {

    private String name;
    private String email;

    public User(){}

    public void setName(
            @NotEmpty(message = "Name may not be empty") String name) {
        this.name = name;
    }

    public void setEmail(
            @Email(message = "Email must be a well-formed email address") String email) {
        this.email = email;
    }

    // getters for name and email

} 
```

跟踪目标类与调用者达成的契约真的很容易，而不必从上到下扫描该类并更深入地查看方法实现。简单来说，`setName()`的前提条件是非空字符串，而`setEmail()`的前提条件是一个格式良好的电子邮件地址。

当然，契约必须在某个时候得到验证。为了实现这一点，该标准提供了前述的`validateParameters()`方法:

```
ExecutableValidator executableValidator = Validation
        .buildDefaultValidatorFactory()
        .getValidator()
        .forExecutables();
User user = new User();
try {
    Method setName = user.getClass()
            .getMethod("setName", String.class);
    executableValidator
            .validateParameters(user, setName, new Object[]{""}).stream()
            .map(ConstraintViolation::getMessage)
            .forEach(System.out::println);
} catch (NoSuchMethodException e) {
    e.printStackTrace();
} 
```

这里首先要强调的是，所有执行方法参数和返回值验证的方法(这也适用于构造函数)都是`ExecutableValidator` API 的一部分，而不是传统的`Validator`。第二个是验证过程本身的机制:一旦创建了`ExecutableValidator`的实现者，整个验证过程就归结为用所需的参数调用正确的验证方法。

在上面的例子中，`setName()`的前提条件通过`validateParameters()`方法进行验证，该方法采用目标类的一个实例、要验证的方法以及存储在对象的[数组中的参数。考虑到在这种情况下，数组只包含一个空字符串，不满足`setName()`的前提条件，这引发了一个约束冲突。](https://www.sitepoint.com/beginning-java-data-types-variables-and-arrays/)

不如改为验证`setEmail()`的前置条件？

```
try {
    Method setEmail = user.getClass()
            .getMethod("setEmail", String.class);
    executableValidator
            .validateParameters(user, setEmail, new Object[]{""}).stream()
            .map(ConstraintViolation::getMessage)
            .forEach(System.out::println);
} catch (NoSuchMethodException e) {
    e.printStackTrace();
} 
```

这很容易理解。

## 验证方法返回值

同样，可以指定一个方法的后置条件，并用`validateReturnValue()`方法验证它。为了演示验证方法的返回值有多容易，让我们重构前面的`User`类，如下所示:

```
public class User {

    private String name;
    private String email;

    public User(){}

    // setter and getter for name, setter for email

    @Email(message = "Email must be a well-formed email address")
    public String getEmail() {
        return email;
    }

} 
```

在这种情况下，`getEmail()`方法的后置条件是一个格式良好的电子邮件地址。下面是如何用`validateReturnValue()`方法验证方法的后置条件:

```
User user = new User();
try {
    Method getEmail = user.getClass().getMethod("getEmail");
    executableValidator
            .validateReturnValue(user, getEmail, "no-email").stream()
            .map(ConstraintViolation::getMessage)
            .forEach(System.out::println);
} catch (NoSuchMethodException e) {
    e.printStackTrace();
} 
```

正如所料，这也会弹出一个违反约束的情况，因为字符串`"no-email"`不满足`getEmail()`方法的后置条件，该字符串被传递给`validateReturnValue()`。

## 验证构造函数参数

与方法类似，该标准允许通过`validateConstructorParameters()`方法验证构造函数中的参数。它确实做了表面上承诺的事情:是的，它验证了构造函数参数！这是设计良好的 API 的又一个明显标志。

为了使用所讨论的方法，`User`类应该按如下方式重构:

```
public class User {

    private String name;
    private String email;

    public User(){}

    public User(
            @NotEmpty(message = "Name may not be empty") String name,
            @Email(message = "Email must be a well-formed email address") String email) {
        this.name = name;
        this.email = email;
    }
    // setters and getters for name and email

} 
```

如上所示，构造函数参数已经通过使用几个注释进行了约束，正如我们刚才对方法参数和返回值所做的那样。因此，以下是验证它们的方法:

```
User user = new User("", "");
try {
    Constructor constructor = user.getClass()
            .getConstructor(new Class[] {String.class, String.class});
    executableValidator
            .validateConstructorParameters(constructor, new Object[]{"", ""})
            .stream()
            .map(ConstraintViolation::getMessage)
            .forEach(System.out::println);
} catch (NoSuchMethodException e) {
    e.printStackTrace();
} 
```

使用`validateConstructorParameters()`方法非常简单，只需向它传递目标类的构造函数以及相应的参数。在本例中，我故意传入了一个空字符串数组，这导致该方法触发了两个违反约束的情况。

至此，很容易理解为什么 Bean Validation 是一个功能丰富的框架，在验证对象时有大量的选项可供选择。它完全支持方法参数、返回值和构造函数参数的验证，所有这些便利的特性都是围绕一个简洁的契约式设计模型实现的。我们还能要求什么？

!["Use Java Bean Validation to validate method calls"](img/10cc2bbc5257448df2a4211fc8518b3c.png)

## 使用 Java EE 拦截器进行自动验证

虽然我们到目前为止所讨论的听起来都很好，但是让我们想想:如果我们最终总是手动调用它们，那么拥有如此强大的验证方法库又有什么意义呢？呀！

好消息是，我们不必经历手动验证的负担，除非我们故意想用这种不必要的努力来惩罚自己！正如我在介绍中提到的，解决这个问题的典型方法是使用 Java EE 拦截器启用自动方法验证。

虽然名字听起来吓人，但是拦截器只是用来拦截一个或多个目标类的方法调用或生命周期事件的常规类。我们可以让这个功能为我们工作，使用一个拦截器将验证逻辑封装在一个地方，并拦截对目标对象中受约束方法的调用，以便验证相应的约束。简而言之，我们将实现期待已久的自动方法验证！

看看下面的域类:

```
public class User {

    private String name;
    private String email;

    public User(){}

    @Interceptors(MethodInterceptor.class)
    public void setName(@NotEmpty(message = "Name may not be empty") String name) {
        this.name = name;
    }

    public String getName() {
        return name;
    }

    public void setEmail(String email) {
        this.email = email;
    }

    @Interceptors(MethodInterceptor.class)
    @Email(message = "Email must be a well-formed address")
    public String getEmail() {
        return email;
    }

} 
```

如上所示，`User`类现在有一个受约束的 setter 和一个受约束的 getter。这里唯一值得注意的细节是，受约束的方法通过`@Interceptors`注释绑定到一个`MethodInterceptor`拦截器，后面是拦截器类，用括号括起来。注释将受约束的方法链接到拦截器，这意味着无论何时调用方法，都可以拦截调用，并且可以通过遵循特定的策略来验证约束。

下面是如何实现一个典型的方法拦截器:

```
@InterceptorBinding
@Retention(RetentionPolicy.RUNTIME)
@Target({ElementType.METHOD, ElementType.TYPE})
public @interface MethodInterceptorBinding { }

@Interceptor
@MethodInterceptorBinding
public class MethodInterceptor {

    @Inject
    private Validator validator;

    @AroundInvoke
    public Object validateMethodInvocation(InvocationContext ctx)
            throws Exception {
        Set<ConstraintViolation<Object>> violations;
        ExecutableValidator executableValidator = validator.forExecutables();
        violations = executableValidator.validateParameters(
                ctx.getTarget(), ctx.getMethod(), ctx.getParameters());
        processViolations(violations);
        Object result = ctx.proceed();
        violations = executableValidator.validateReturnValue(
                ctx.getTarget(), ctx.getMethod(), result);
        processViolations(violations);
        return result;
    }

    private void processViolations(Set<ConstraintViolation<Object>> violations) {
        violations.stream()
                .map(ConstraintViolation::getMessage)
                .forEach(System.out::println);
    }

} 
```

这个例子看起来很难理解，但是让我们用几个简单的步骤来剖析它，以理解它是如何工作的。

1.  *[`@InterceptorBinding`](http://docs.oracle.com/javaee/6/api/javax/interceptor/InterceptorBinding.html)注释:*需要将元数据，如 [`RetentionPolicy`](https://docs.oracle.com/javase/7/docs/api/java/lang/annotation/RetentionPolicy.html) 和适用的[目标](https://docs.oracle.com/javase/7/docs/api/java/lang/annotation/ElementType.html)绑定到自定义拦截器注释`@MethodInterceptorBinding`。
2.  *拦截器类:*一个普通类，用 [`@Interceptor`](http://docs.oracle.com/javaee/6/api/javax/interceptor/Interceptor.html) 和`@MethodInterceptorBinding`标注，标记为拦截器。
3.  *[`@AroundInvoke`](http://docs.oracle.com/javaee/6/api/javax/interceptor/AroundInvoke.html)注释*:用于定义一个*拦截器，该拦截器拦截对绑定到*的方法的调用(可以创建更多类型的拦截器，但为了简单起见，这里省略了它们)。在这种情况下，`validateMethodInvocation()`方法介于对`User`类的受约束方法和调用者的调用之间。
4.  *拦截器实现:*用 [CDI](https://www.sitepoint.com/introduction-contexts-dependency-injection-cdi/) 将`Validator`对象注入拦截器类，用于获取`ExcecutableValidator`的实例，验证方法参数和返回值中的约束。
5.  *拦截器输出:*当拦截器验证受约束的方法时，潜在的违反约束的情况会通过`processViolations()`方法输出到控制台。

关于`processViolations()`打印到控制台，[官方文档](http://beanvalidation.org/specification/)强烈鼓励抛出一个`ConstraintViolationException`，当一个方法没有通过验证时，从拦截器内部包装违规，这很有意义，因为这保证了以下几点:

1.  只有当客户端代码满足方法的前提条件时，控制流才会到达方法体。

2.  如果方法的 post 条件得到保证，控制流将返回到客户端代码。

我在这里这样做不是为了让这个例子更容易理解。

也就是说，下面是一个很好的方式来看看拦截器的使用是如何自动触发方法验证的:

```
public class Application {

    @Inject
    private User user;

    public void execute() {
        user.setName("John");
        user.setEmail("no-email");
        user.getEmail();
    }

} 
```

正如所料，由于拦截器的透明操作，调用`setEmail()`和`getEmail()`将导致两个约束冲突被打印到控制台。

如果你觉得心情不错，想要实现你自己的拦截器，或者甚至修改上面显示的拦截器，首先要确保阅读一下[官方文档](https://docs.oracle.com/javaee/7/tutorial/interceptors002.htm)。

## 摘要

在这篇文章中，我们学习了如何使用属于`ExecutableValidator`接口的方法，以便在常规方法和构造函数中验证参数和返回值。此外，我们看到了将标准与 CDI 和 Java EE 拦截器集成并触发自动方法验证的可能性。

起初，集成过程看起来相当复杂，但是实现额外一层拦截器的努力在很大程度上得到了补偿，因为将验证逻辑封装在一个地方并实现了[控制反转](https://www.sitepoint.com/three-design-patterns-that-use-inversion-of-control/)。最后但同样重要的是，请记住 Bean 验证的功能不仅限于验证方法参数和返回值，因为它还提供了许多其他值得一看的漂亮特性，比如用一个方法调用分别验证类字段和对象图。因此，如果你有兴趣学习如何做所有这些事情而不必爬上陡峭的学习曲线，你可以查看这些较短的帖子来学习[如何验证单个属性和字段](https://www.sitepoint.com/java-bean-validations-validateproperty-validatevalue-tutorial/)以及[如何使用`@Valid`来验证整个对象图](https://www.sitepoint.com/java-bean-validation-object-graphs-valid-annotation/)。

## 分享这篇文章