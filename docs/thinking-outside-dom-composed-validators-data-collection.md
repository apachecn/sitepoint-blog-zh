# 在 DOM 之外思考:组合验证器和数据收集

> 原文：<https://www.sitepoint.com/thinking-outside-dom-composed-validators-data-collection/>

在这个迷你系列的第 1 部分[中，我们讨论了许多 JavaScript 代码库共有的一个问题:紧耦合代码。然后，我向您介绍了分离正交关注点的好处。作为概念验证，我们开始开发一个表单验证系统，它不局限于表单，甚至可以在 DOM 之外工作。](https://www.sitepoint.com/thinking-outside-dom-concepts-setup/)

在第二部分也是最后一部分，我们将讨论组合验证器，如何从表单中收集数据，以及如何报告错误。最后，我将为您提供一个到 GitHub 资源库的链接，该资源库包含了本迷你系列中开发的所有代码。

## 组合验证器

在上一篇文章中，我们开发了一个验证单个字段的系统。一次用一个规则逐个验证字段是很好的，但是有很多情况需要更多的思考。您可以用一个非常长的正则表达式来验证一个电子邮件地址，但是这样做只能让您告诉您的用户该电子邮件是否可以接受。更好的方法是分别验证电子邮件地址的几个部分，并提供有针对性的电子邮件验证错误。

这在当前的设计中是可能的:

```
var rules = [
  pattern('email', /@/, 'Your email is missing an @'),
  pattern('email', /^\S+@/, 'Please enter the username in your email address',
  // ...
];
```

虽然这可以工作，但它可能会为电子邮件地址生成多个错误消息。它还要求我们为具有电子邮件语义的每个字段手动重复每个步骤。即使我们还没有讨论错误消息的呈现，有一个抽象将多个验证器分组，只显示第一个违反规则的结果也是不错的。事实证明，这就是`&&`操作符的确切语义。进入`and`验证器。这个验证器将接受多个验证器作为它的参数，并应用它们直到找到一个失败的验证器:

```
function and() {
  var rules = arguments;

  return function (data) {
    var result, l = rules.length;

    for (var i = 0; i < l; ++i) {
      result = rules[i](data);
      if (result) {
        return result;
      }
    }
  };
}
```

现在，我们可以用一种方式来表达我们的电子邮件验证程序，这样一次只会出现一条错误消息:

```
var rules = [and(
  pattern('email', /@/, 'Your email is missing an @'),
  pattern('email', /^\S+@/, 'Please enter the username in your email address',
  // ...
)];
```

这可以被编码为一个单独的验证器:

```
function email(id, messages) {
  return and(
    pattern('email', /@/, messages.missingAt),
    pattern('email', /^\S+@/, messages.missingUser)
    // ...
  );
}
```

当我们谈到电子邮件地址的话题时，人们经常犯的一个错误是在我们的国家顶级域名中键入 Hotmail 和 Gmail 地址(例如“…@hotmail.no”)。如果能够在这种情况发生时提醒用户，将会非常有帮助。换句话说:有时我们希望只在满足某些标准时才执行某些检查。为了解决这个问题，我们将引入`when`函数:

```
function when(pred, rule) {
  return function (data) {
    if (pred(data)) {
      return rule(data);
    }
  };
}
```

如您所见，`when`是一个验证器，就像`required`一样。您用一个谓词(一个将接收要验证的数据的函数)和一个验证器来调用它。如果谓词函数返回`true`，我们评估验证器。否则，`when`视为成功。

我们需要解决 Hotmail 难题的谓词是一个检查值是否与模式匹配的谓词:

```
function matches(id, re) {
  return function (data) {
    return re.test(data[id]);
  };
}
```

这非常接近我们的`pattern`验证器，除了这不是一个验证器。同样值得注意的是，这些函数中的大多数是多么小，当它们组合在一起时，而不是单独使用时，它们是如何真正发光的。有了这个难题的最后一块，我们可以创建一个对最终用户真正有用的电子邮件验证程序:

```
function email(id, messages) {
  return and(
    pattern(id, /@/, messages.missingAt),
    pattern(id, /^\S+@/, messages.missingUser),
    pattern(id, /@\S+$/, messages.missingDomain),
    pattern(id, /@\S+\.\S+$/, messages.missingTLD),
    when(matches(id, /@hotmail\.[^\.]+$/),
      pattern(id, /@hotmail\.com$/, messages.almostHotmail)
    ),
    when(matches(id, /@gmail\.[^\.]+$/),
      pattern(id, /@gmail\.com$/, messages.almostGmail)
    )
  );
}
```

它可以这样使用:

```
email('email', {
  missingAt: 'Missing @',
  missingUser: 'You need something in front of the @',
  missingDomain: 'You need something after the @',
  missingTLD: 'Did you forget .com or something similar?',
  almostHotmail: 'Did you mean hotmail<strong>.com</strong>?',
  almostGmail: 'Did you mean gmail<strong>.com</strong>?'
});
```

如果你想玩这个功能，我已经为你创建了一个[代码笔](http://codepen.io/SitePoint/pen/zxVMOm)。

## 提取数据

既然我们可以验证数据，我们还需要从表单中获取数据，以解决表单验证的初始问题。基本上，我们需要把这个:

```
<form action="/doit" novalidate>
  <label for="email">
    Email
    <input type="email" name="email" id="email" value="christian@cjohansen.no">
  </label>
  <label for="password">
    Password
    <input type="password" name="password" id="password">
  </label>
  <label class="faded hide-lt-pad">
    <input type="checkbox" name="remember" value="1" checked>
    Remember me
  </label>
  <button type="submit">Login</button>
</form>
```

变成这样:

```
{
  email: 'christian@cjohansen.no',
  password: '',
  remember: '1'
}
```

通过测试逐步实现这一点相当简单，但是需要 DOM 元素。以下是这些测试的示例:

```
describe('extractData', function () {
  it('fetches data out of a form', function () {
    var form = document.createElement('form');
    var input = document.createElement('input');
    input.type = 'text';
    input.name = 'phoneNumber';
    input.value = '+47 998 87 766';
    form.appendChild(input);

    assert.deepEqual(extractData(form), {'phoneNumber': '+47 998 87 766'});
  });
});
```

这并没有那么糟糕，通过另一个小的抽象，我们可以把它收紧一点:

```
it('fetches data out of a form', function () {
  var form = document.createElement('form');
  addElement(
    form,
    'input',
    {type: 'text', name: 'phoneNumber', value: '+47 998 87 766'}
  );

  assert.deepEqual(extractData(form), {'phoneNumber': '+47 998 87 766'});
});
```

提取数据就是选择表单中的所有`input`、`select`和`textarea`元素，并提取它们的`name`属性和当前值。从复选框和单选按钮中提取正确的值需要一些特殊的处理。主函数看起来像这样:

```
function extractData(form) {
  return getInputs(form).reduce(function (data, el) {
    var val = getValue[el.tagName.toLowerCase()](el);
    if (val) { data[el.name] = val.trim(); }
    return data;
  }, {});
};
```

从这个代码片段中可以看出，`extractData()`函数依赖于`getInputs()`函数。这个支持函数的目的是获得一个以参数形式传递的 DOM 元素数组。在这篇文章中，我不打算涉及它，因为这个函数依赖于其他小函数，我想避免初始效应。但是，如果您想了解更多，可以看看我创建的 [GitHub 库](https://github.com/sitepoint-editors/validate)，它包含了上一期和本期的所有文件。

现在让我们看看如何报告错误。

## 错误报告

为了报告错误，我们可以设计一个接受表单和错误数组的函数。然而，有一个挑战需要解决:为了避免 DOM 中的重复错误，函数要么需要保持状态，以便知道它已经呈现了哪些错误，要么需要假设当呈现新的集合时，表单中的每个错误都可以被清除。哪种解决方案合适将取决于您的具体使用情形。

我不会深入渲染实现的细节，但建议以下简化的解决方案:

```
function renderErrors(form, errors) {
  removeErrors(form);
  errors.forEach(function (error) {
    renderError(form, error);
  });
}
```

为了呈现一个错误，我们找到与它相关的输入，并在它之前插入一个元素。我们只呈现第一个错误。这是一个非常基本的渲染策略，但是效果很好:

```
function renderError(form, error) {
  var input = form.querySelector("[name=" + error.id + "]");
  var el = document.createElement("div");
  el.className = "error js-validation-error";
  el.innerHTML = error.messages[0];
  input.parentNode.insertBefore(el, input);
}
```

在上面的代码中，你可以看到我给元素分配了两个类:`error`和`js-validation-error`。前者仅用于造型目的。后者旨在作为内部机制，由以下`removeErrors()`功能使用:

```
function removeErrors(form) {
  var errors = form.querySelectorAll(".js-validation-error");

  for (var i = 0, l = errors.length; i < l; ++i) {
    errors[i].parentNode.removeChild(errors[i]);
  }
}
```

我们在本节中构建的错误报告系统的基本演示由[这个代码笔](http://codepen.io/SitePoint/pen/LEKXPo)显示。

## 将它们连接在一起

我们现在有了所有的部分(的一个版本):从 DOM 读取，验证纯数据，并将验证结果返回到 DOM。我们现在只需要一个高级接口将它们绑定在一起:

```
validateForm(myForm, [
  required("login", "Please choose a login"),
  email("email", i18n.validation.emailFormat),
  confirmation("password", "password-confirmation", "Passwords don't match")
], {
  success: function (e) {
    alert("Congratulations, it's all correct!");
  }
});
```

与渲染一样，这种高级连接既可能愚蠢简单，也可能相当复杂。在大部分代码源自的项目中，`validateForm()`函数不会执行验证，直到用户第一次尝试提交表单。如果有验证错误，它会进入一种“智能实时验证模式”:被修复的错误会被尽快删除(例如在`keyup`上)，但新的错误只会在`blur`上添加。这种模式在即时反馈和唠叨之间取得了良好的平衡(没有人喜欢在他们完成打字之前听到“你的电子邮件不正确”)。

现在我已经完成了这最后一部分的描述，我邀请你看看包含在[GitHub 库](https://github.com/sitepoint-editors/validate)中的演示。它包括我们讨论过的所有代码，以及完整的测试用例。

## 结论

该模型的优势在于外部输入/输出机制如何与“规则”实现完全分离，而“规则”实现实际上是库的核心。这个模型可以很容易地用于其他类型的数据验证。规则引擎也可能被扩展以包括关于成功纠正错误的信息(例如，通过返回类似于`{id: 'name', ok: true}`的东西，或者更多的细节),以允许在成功完成的元素旁边有绿色复选标记。也许允许规则引擎处理异步操作也是有意义的。

最后两个组件，渲染器和`validateForm()`函数包含了通常将各种验证库区分开来的功能。增加一些工作使它们更加灵活，或者甚至为应用程序的不同部分或跨应用程序提供替代实现都是微不足道的。这意味着包含所有验证逻辑的引擎可以保持非常稳定，需要频繁更改的代码越少，引入新错误的机会就越少。

## 分享这篇文章