# 使用 ngMessages 在 AngularJS 中实现简单的表单验证

> 原文：<https://www.sitepoint.com/easy-form-validation-angularjs-ngmessages/>

去年秋天， [ngMessages 模块](https://docs.angularjs.org/api/ngMessages/directive/ngMessages)随着 Angular 1.3 的发布而推出。这增加了对向用户显示消息的支持，通常是来自表单验证的错误消息。在`ngMessages`发布之前，开发者被迫依靠`ng-class`和`ng-show`等指令来显示这些错误。这导致了混乱、重复的代码。

然而现在，`ngMessages`提供了显示定制错误消息的能力，而不必违反 D.R.Y .编码原则——不要重复自己。随着 Angular 稳步迈向 2.0 版本，Angular 的开发团队继续[为其推出新功能](http://blog.thoughtram.io/2015/06/06/ng-messages-revisited.html)。因此，我认为展示一下`ngMessages`让 AngularJS 中的表单验证过程变得多么简单是有益的。

## 入门指南

这将是我们的基本模板:

```
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="utf-8" />
    <title>ngMessages demo</title>
  </head>
  <body ng-app="app">

    <script src="path/to/angular.min.js"></script>
    <script src="path/to/angular-messages.min.js"></script>
    <script>
      var app = angular.module('app', ['ngMessages']);
    </script>
  </body>
</html> 
```

正如你所看到的，在注入我们的应用程序之前，我们先加载 Angular，然后是`ngMessages`模块。对于那些希望在家学习的人来说，本教程的代码可以在 [GitHub](https://github.com/sitepoint-editors/SpNgMessages) 上找到。对于不耐烦的人，你也可以跳到文章末尾的[演示](#in-page-demo)。

接下来，我们将创建一个包含以下字段的表单:

1.  西方人名的第一个字
2.  姓
3.  电子邮件地址
4.  电话号码
5.  消息

我将为所有字段添加一个`required`属性(因为它们是强制的)，并使用一个`ng-model`指令将它们绑定到当前范围的属性。

```
<form name="exampleForm" class="myForm" required>
  <label>First Name:</label>
  <input type="text" name="userFirstName" ng-model="firstName" required />

  <label>Last Name:</label>
  <input type="text" name="userLastName" ng-model="lastName" required />

  <label>Email Address:</label>
  <input type="email" name="userEmail" ng-model="email" required />

  <label>Phone Number:</label>
  <input type="email" name="userPhoneNumber" ng-model="phoneNumber" required />

  <label>User Message:</label>
  <textarea type="text" name="userMessage" ng-model="message" required></textarea>
</form> 
```

## 输入 ng 消息

现在让我们深入代码，看看`ngMessages`如何允许我们使用我们在输入中指定的属性进行简单的表单验证。

### 必填字段

您应该注意的第一件事是表单名为`exampleForm`。当使用`ng-messages`指令时，我们向它传递一个角度表达式，该表达式计算一个键/值对象(通常是`ngModel`实例上的`$error`对象)。在我们的例子中，这将是表单的名称，链接到各自表单字段的`name`属性，链接到前面提到的`$error`对象。

```
<div ng-messages="exampleForm.userFirstName.$error"> 
```

一旦完成，您只需要在`ngMessages div`中嵌套一个包含`ng-message`属性的`div`。传递给`ng-message`属性的值将取决于我们添加到输入字段的指令；在这种情况下，该值将是`required`。

这将给我们:

```
<label>First Name:</label>
<input type="text" name="userFirstName" ng-model="firstName" required />
<div ng-messages="exampleForm.userFirstName.$error">
  <div ng-message="required">This field is required</div>
</div>

<label>Last Name:</label>
<input type="text" name="userLastName" ng-model="lastName" required />
<div ng-messages="exampleForm.userLastName.$error">
  <div ng-message="required">This field is required</div>
</div> 
```

### 电子邮件

接下来，我们要确保用户输入了有效的电子邮件地址。幸运的是，HTML5 让这个任务变得非常简单。在前面的例子中，输入字段的`name`属性取值为`text`，但是电子邮件地址取值为`email`。就像上面的例子一样，您只需将表单名称、输入名称和错误验证器链接起来，并将其传递给`ng-messages`属性，以便激活错误消息。然后我们可以添加一个额外的`div`，它包含接受`email`值的`ng-message`属性。通过这样做，我们可以像定位`required`值一样定位 email 值。

```
<label>Email Address:</label>
<input type="email" name="userEmail" ng-model="email" required />
<div ng-messages="exampleForm.userEmail.$error">
  <div ng-message="required">This field is required</div>
  <div ng-message="email">Your email address is invalid</div>
</div> 
```

### 正则表达式

随着我们的继续，我们将使用`ng-pattern`指令来验证用户的电话号码。对于那些不熟悉`ng-pattern`的人来说，它用于确保输入字段匹配传递给属性的正则表达式。在这种情况下，我们将通过向`ng-pattern`属性传递以下正则表达式来验证用户的电话号码:

```
/^[\+]?[(]?[0-9]{3}[)]?[-\s\.]?[0-9]{3}[-\s\.]?[0-9]{4,6}$/ 
```

这将匹配以下格式(并确保我们接受有效的十位数电话号码):

```
(123) 456-7890
123-456-7890
123.456.7890
1234567890
+31636363634
075-63546725 
```

通过将`pattern`的值传入`ng-message`来激活`ng-pattern`错误消息，这可以在下面的代码中看到:

```
<label>Phone Number:</label>
<input type="email" name="userPhoneNumber" ng-model="phoneNumber" 
       ng-pattern="/^[\+]?[(]?[0-9]{3}[)]?[-\s\.]?[0-9]{3}[-\s\.]?[0-9]{4,6}$/"
       required/>
<div ng-messages="exampleForm.userPhoneNumber.$error">
  <div ng-message="required">This field is required</div>
  <div ng-message="pattern">Must be a valid 10 digit phone number</div>
</div> 
```

用正则表达式验证电话号码有点棘手(老实说，越简单越好)。要了解这方面的更多信息，您可以查看[这篇 SitePoint 文章](https://www.sitepoint.com/working-phone-numbers-javascript/)，或者[这篇堆栈溢出线程](http://stackoverflow.com/questions/123559/a-comprehensive-regex-for-phone-number-validation)。要了解更多关于正则表达式的知识，你可以阅读 Mozilla 开发者网络上的[这篇文章](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Regular_Expressions)。

### Minlength 和 Maxlength

我们要做的最后一件事是验证用户的消息。该字段的名称是`userMessage`,和上面的例子一样是必需的。然而这一次，我们将利用`ng-minlength`和`ng-maxlength`属性。这两个属性都是整数值，等于设定的字符数——`ng-minlength`属性用于设置用户可以输入的字符数，而`ng-maxlength`属性设置用户可以输入的最大字符数。

在下面的例子中，你会看到我们将错误消息传递给了`minlength`和`maxlength`值。这样,`minlength`值的错误消息将会出现，直到用户达到所需的字符数。此外，一旦用户超过传入`ng-maxlength`属性的设定字符数，就会出现为`maxlength`定义的错误信息。

```
<label>User Message:</label>
<textarea type="text" name="userMessage" ng-model="message" 
          ng-minlength="100" ng-maxlength="1000" required>
</textarea>
<div ng-messages="exampleForm.userMessage.$error">
  <div ng-message="required">This field is required</div>
  <div ng-message="minlength">Message must be over 100 characters</div>
  <div ng-message="maxlength">Message must not exceed 1000 characters</div>
</div> 
```

现在我们已经验证了所有必需的字段，您应该有一个功能齐全的表单，它可以根据表单的输入标签显示您想要的任何消息！

通过 [CodePen](http://codepen.io) 上的 SitePoint ( [@SitePoint](http://codepen.io/SitePoint) )用 ngMessages 查看 AngularJS 中的笔[简易表单验证。](http://codepen.io/SitePoint/pen/NqQbaw/)