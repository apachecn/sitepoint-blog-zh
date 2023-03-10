# 使用 Java Bean 验证的@Valid 注释验证对象图

> 原文：<https://www.sitepoint.com/java-bean-validation-object-graphs-valid-annotation/>

### 目录 

*   [用@Valid 一步完成对象图验证](#objectgraphvalidationinonestepwithvalid)
*   [没有@Valid 的臃肿代码](#bloatedcodewithoutvalid)
*   [使用@Valid 验证对象图](#usingvalidtovalidateanobjectgraph)
*   [总结](#summary)
*   [评论](#comments)

Java Bean 验证的 [`@Valid`约束注释](http://beanvalidation.org/1.1/spec/#constraintdeclarationvalidationprocess-validationroutine-graphvalidation%29.)确保当一个对象被验证时，验证递归到所有用`@Valid`注释的字段。这使得验证整个对象图的复杂任务变得非常容易。

为了理解这篇文章，你应该知道如何使用 Java Bean 验证的基础知识。

## 用`@Valid`一步完成对象图验证

为了更好地理解为什么`@Valid`注释是如此有价值的特性，我将从向您展示如何验证不使用它的对象图开始。

### 没有`@Valid`的臃肿代码

考虑一个受约束的类`A`声明依赖于另一组受约束的类`B`和`C`的情况。我们这里有一个简单的对象图，其中这个关联的每个合作者都有自己的一组验证约束。如果没有`@Valid`注释，验证图是一项非常麻烦的任务，因为`A`、`B`和`C`的每个实例都必须通过多次调用`Validator`接口实现者的`validate()`方法来单独验证。

考虑以下类:

```
public class AccountProcessor {

    @NotEmpty(message = "Account number may not be empty")
    @Size(min = 8, max = 8,
            message = "Account number must be an 8-digit string")
    private String accountNumber;

    public AccountProcessor(String accountNumber) {
        this.accountNumber = accountNumber;
    }

    public void processAccount() {
        // process the account here
    }

}

public class PaymentService {

    private AccountProcessor accountProcessor;

    @NotEmpty(message = "Payment method may not be empty")
    private String paymentMethod;

    public PaymentService(AccountProcessor accountProcessor, String paymentMethod) {
        this.accountProcessor = accountProcessor;
        this.paymentMethod = paymentMethod;
    }

} 
```

此时，我们已经使用`AccountProcessor`创建了一个带有`PaymentService`的基本对象图。没有`@Valid`注释，图形必须在每个对象的基础上进行验证，如下所示:

```
Validator validator = Validation.buildDefaultValidatorFactory().getValidator();
AccountProcessor accountProcessor = new AccountProcessor("");
PaymentService paymentService = new PaymentService(accountProcessor, "");
validator
        .validate(accountProcessor)
        .stream()
        .forEach(violation -> System.out.println(violation.getMessage()));
validator
        .validate(paymentService)
        .stream()
        .forEach(violation -> System.out.println(violation.getMessage())); 
```

在这种情况下，`AccountProcessor`实例和它的`PaymentService`都没有被正确初始化，两者加在一起会产生三个违反约束的情况。

虽然验证按预期工作，但由于必须为每个实例单独调用`validate`，代码变得臃肿。

### 使用`@Valid`验证对象图

为了改进验证代码，让我们重构`PaymentService`类并将`@Valid`注释绑定到它的`AccountProcessor`字段:

```
public class PaymentService {

    @Valid
    private AccountProcessor accountProcessor;

    // other class members and methods as before

} 
```

使用`@Valid`注释的影响可以在这里一目了然:

```
Validator validator = Validation.buildDefaultValidatorFactory().getValidator();
AccountProcessor defaultAccountProcessor = new DefaultAccountProcessor("");
PaymentService paymentService = new PaymentService(defaultAccountProcessor, "");
validator
        .validate(paymentService)
        .stream()
        .forEach(violation -> System.out.println(violation.getMessage())); 
```

代码更加紧凑和精简，但会产生与之前更长的代码块相同的验证结果。每当`PaymentService`的实例被验证时，`AccountProcessor`将被递归验证。我们一石二鸟！

从客户端代码的角度来看，验证显然是通过对`validate()`的一次调用来执行的，这是正确的方式，因为这巧妙地向开发人员隐藏了扫描整个对象图结构所涉及的复杂性。不过，在内部，当验证器从根对象到叶对象遍历树时，该方法实际上是被递归调用的。此外，`@Valid`的使用不仅限于类字段，因为它也可以在集合和数组中使用。

这使得对象图验证变得容易。

![Validate an entire object graph (did somebody say tree?) with the @Valid annotation](img/0fc1c4ed826495674c1b2663036cbc5d.png)

## 摘要

`@Valid`注释是 Bean 验证的一个关键特性，因为它允许通过对验证器的一次调用来验证对象图。为了利用它，所有应该递归检查的字段都应该用`@Valid`进行注释。

超越基本 bean 验证的另一个有趣特性是[用`validateProperty()`和`validateValue()`](https://www.sitepoint.com/java-bean-validations-validateproperty-validatevalue-tutorial) 验证单个字段和值。API 当然还能提供更多，所以请确保查看[官方文档](http://beanvalidation.org)并了解[最新消息](http://beanvalidation.org/news/news.atom)。

## 分享这篇文章