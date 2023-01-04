# 在 DOM 之外思考:概念和设置

> 原文：<https://www.sitepoint.com/thinking-outside-dom-concepts-setup/>

如果要我说一件大多数 JavaScript 代码基础都在努力解决的事情，那就是紧密耦合，尤其是与 DOM 的耦合。在对代码进行单元测试时，紧耦合会给开发人员带来麻烦和问题。

在这个由两部分组成的系列中，我将向您提供一些关于如何实现松散耦合代码的提示，并通过一个示例向您展示如何将代码从 DOM 中分离出来。在这第一部分中，我将向您介绍拥有紧密耦合代码的问题，并且我们还将经历一个真实世界的情况，在这个情况下我们可以应用所讨论的概念:表单的验证。

## 什么是耦合？

在许多应用程序中，代码与各种外部 API 进行交互。在 web 应用程序中，我们与 DOM API 交互，可能是网络(通过 XMLHttpRequest)、JSON 或 XML 进行数据交换，等等。在概念层面上，这些关注点是严格分开的。

如果您的应用程序与之交互的 REST API 做了一些结构上的改变，那么您需要更新与 REST 服务交互的代码是合理的。这要求更改 UI 呈现代码是不合理的。然而，通常情况下确实如此。当这种情况发生时，就有了所谓的“紧耦合”。

松耦合是紧耦合的反义词。在松散耦合的系统中，改变网络需求不会导致呈现代码的改变。改进的 CSS 样式表和新的类名规则不会导致数据序列化代码发生变化。这意味着更少的问题，以及更容易推理的代码库。

既然我已经给了你一些背景，让我们看看这在实践中意味着什么。

## 表单验证

表单验证可能是你能用 JavaScript 棍子打败的最致命的马。这是 JavaScript 最古老的用例之一，开源库已经解决了无数次，更不用说引入了 HTML5 属性，如`required`和`pattern`。然而，新的库仍然会弹出，这表明:

1.  我们没有创建正确的抽象，导致不断需要重写。
2.  JavaScript 开发人员真的很喜欢重新发明轮子(并将结果作为开源软件发布)。

对于后者，我真的帮不上什么忙，但我确实希望对前者有所了解，即使我自己也造成了目前的混乱局面。

表单验证在许多方面都“接近”DOM。我们正在测试一组对当前状态的期望，然后通过修改 DOM 向用户报告。然而，如果我们后退一步，我们可以很容易地想象出一些在较小程度上涉及 DOM 的相关用例:

*   向分析系统发送验证报告，以了解如何改进网站设计
*   验证通过网络获取的数据
*   验证拖动到浏览器的文件中的数据
*   使用诸如 [React](http://facebook.github.io/react/) 之类的库输出验证消息

即使大量涉及到 DOM，也有多种因素会发生变化:

*   何时触发验证？当`onsubmit`事件被触发时。`onblur`？`onchange`？通过 JavaScript 代码编程？
*   报告表单范围内的错误还是每个字段的错误？两者都有？
*   错误报告标记详细信息可能会有很大差异
*   根据上下文的不同，错误报告需求可能会有所不同

将输入-验证-输出的循环紧密地捆绑在一起，将很难解释所有可以想象的这些事情的组合。如果你提前计划得很好，你可以做出一个非常灵活的解决方案，但是我向你保证有人会带着一个非常困难的用例出现。相信我，这条路我也走过，掉进过沿途的每一条沟里。

似乎这还不够，考虑许多种类的有效性规则依赖于不止一个字段的事实。我们如何解决这些情况？首先分析我们需要完成什么，然后决定如何最好地完成，就可以找到答案:

*   从表单中读取数据(以 DOM 为中心)
*   根据一组规则验证数据(纯业务逻辑)
*   输出验证结果(可能以 DOM 为中心)

此外，我们还需要一个薄薄的代码层来组合各个部分，并在需要的时候触发验证。可能还需要考虑更多的方面，但是只要我们能够将这些实现为正交的关注点，我们就应该能够相对容易地在这个抽象上分层。

### 验证数据

任何验证库的核心都是它的验证函数集。这些函数应该适用于任何数据，而不仅仅是表单元素。毕竟，强制要求表单中的`name`字段与强制对象的`name`属性存在的唯一区别是我们如何访问值。验证逻辑本身是相同的。因此，明智的做法是将验证器函数设计为处理纯数据，然后提供不同的机制来提取值，以分别通过验证器运行。这也意味着我们的单元测试可以使用普通的 JavaScript 对象，这很好也很容易做到。

我们的验证器应该期待什么样的输入？我们需要为单个字段指定规则(以及复合规则，稍后会详细介绍)，并且将上下文错误消息与每个检查关联起来会非常有帮助。大概是这样的:

```
var checkName = required("name", "Please enter your name");
```

`required`函数返回一个检查所有数据并寻找`name`的函数。它可以被称为:

```
var result = checkName({name: 'Chris'});
```

如果提供给该函数的数据通过检查，它返回`undefined`。如果失败，该函数将返回描述问题的对象:

```
// returns {id: "name", msg: "Please enter your name"}
checkName({});
```

这些数据可以“在另一端”使用，例如将消息呈现到表单上。

为了实现这个功能，让我们制定一个测试:

```
describe('required', function () {
  it('does not allow required fields to be blank', function () {
    var rule = required('name', 'Name cannot be blank');

    assert.equals(rule({}), {
      id: 'name',
      msg: 'Name cannot be blank'
    });
  });
});
```

该函数检查非空值:

```
function required(id, msg) {
  return function (data) {
    if (data[id] === null ||
        data[id] === undefined ||
        data[id] === ''
    ) {
      return {id: id, msg: msg};
    }
  };
}
```

虽然调用单个验证函数很简单，但我们的主要用例是验证整个表单。为此，我们将使用另一个函数，它将接受一组*规则*(由各种验证器函数产生)并将它们与数据集进行匹配。结果将是一系列错误。如果数组为空，则验证成功。所以，我们可能会有这样的东西:

```
var rules = [
  required("name", "Please enter your name"),
  required("email", "Please enter your email")
];

var data = {name: "Christian"};

// [{id: "email", messages: ["Please enter your email"]}]
var errors = enforceRules(rules, data);
```

注意得到的`messages`属性是一个数组，因为`enforceRules`可能会遇到多个规则对同一个属性失败。因此，我们必须考虑每个属性名的多个错误消息。

这看起来是一个合理的设计:它简单明了，没有外部依赖性，并且不假设数据来自哪里，或者结果去往哪里。让我们尝试一个实现。我们从一个测试开始:

```
describe('required', function () {
  it('does not allow required fields to be blank', function () {
    var rules = [required('name', 'Name cannot be blank')];

    assert.equals(enforceRules(rules, {}), [
      {id: 'name', messages: ['Name cannot be blank']}
    ]);
  });
});
```

这个测试很好地描述了我们计划的设计。有一组规则，一个有数据的对象，结果是一组错误。这个功能没有副作用。这是一种有机会在不断变化的需求中幸存下来的设计。

在更多的测试之后，您可能会得到如下所示的`enforceRules`的实现:

```
function enforceRules(rules, data) {
  var tmp = {};

  function addError(errors, error) {
    if (!tmp[error.id]) {
      tmp[error.id] = {id: error.id};
      tmp[error.id].messages = [];
      errors.push(tmp[error.id]);
    }

    tmp[error.id].messages.push(error.msg);
  }

  return rules.reduce(function (errors, rule) {
    var error = rule(data);
    if (error) {
      addError(errors, error);
    }
    return errors;
  }, []);
}
```

在这一点上，我们已经有了一个实现新验证器非常简单的系统。例如，正则表达式测试在表单验证器中很常见，可以这样实现:

```
function pattern(id, re, msg) {
  return function (data) {
    if (data[id] && !re.test(data[id])) {
      return {id: id, msg: msg};
    }
  };
}
```

重要的是要注意，如果有问题的数据是空的/不存在的，那么这个验证器被设计为通过。如果我们在这种情况下失败了，验证器也将隐含地成为一个`required`检查。因为我们已经有了一个独立的版本，所以最好允许 API 的用户结合这些来满足他们的需求。

如果你想看看目前为止创建的代码的运行情况，看看[这个 codepen](http://codepen.io/SitePoint/pen/OPGjQL) 。

## 结论

在第一部分中，我们讨论了许多表单验证库共有的一个问题:紧密耦合的代码。然后，我描述了紧耦合代码的缺点，并展示了如何创建不存在这个问题的验证函数。

在下一期文章中，我将向您介绍复合验证器，以及其他相关问题:从 HTML 表单中收集数据并向用户报告错误。最后，我将把它们放在一起，得到一个完整的可视化示例，您可以使用它。

## 分享这篇文章