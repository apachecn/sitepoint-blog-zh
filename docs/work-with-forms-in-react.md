# 在 React 中使用表单

> 原文：<https://www.sitepoint.com/work-with-forms-in-react/>

几乎每个应用程序都需要在某个时候接受用户输入，这通常是通过古老的 HTML 表单及其输入控件集合来实现的。如果你最近才开始学习 React，你可能已经到了这样一个地步，你现在会想，“那么我如何处理表单呢？”

本文将带您了解在 React 中使用表单来允许用户添加或编辑信息的基本知识。我们将看看使用输入控件的两种不同方式以及每种方式的优缺点。我们还将了解如何处理验证，以及一些第三方库的更高级的用例。

## 不受控制的输入

React 中使用表单的最基本方式是使用被称为“不受控制的”表单输入。这意味着 React 不跟踪输入的状态。HTML 输入元素作为 DOM 的一部分自然地跟踪它们自己的状态，因此当提交表单时，我们必须从 DOM 元素本身读取值。

为了做到这一点，React 允许我们创建一个“ref”(引用)来关联一个元素，从而提供对底层 DOM 节点的访问。让我们看看如何做到这一点:

```
class SimpleForm extends React.Component {
  constructor(props) {
    super(props);
    // create a ref to store the DOM element
    this.nameEl = React.createRef();
    this.handleSubmit = this.handleSubmit.bind(this);
  }

  handleSubmit(e) {
    e.preventDefault();
    alert(this.nameEl.current.value);
  }

  render() {
    return (
      <form onSubmit={this.handleSubmit}>  <label>Name: <input type="text" ref={this.nameEl} />  </label>  <input type="submit" name="Submit" />  </form>
    )
  }
} 
```

正如你在上面看到的，对于一个基于类的组件，你通过调用`React.createRef`在构造函数中初始化一个新的 ref，把它赋给一个实例属性，这样它在组件的生命周期中是可用的。

为了将 ref 与输入相关联，它被作为特殊的`ref`属性传递给元素。一旦完成，就可以通过`this.nameEl.current`访问输入的底层 DOM 节点。

让我们看看这在功能组件中是怎样的:

```
function SimpleForm(props) {
  const nameEl = React.useRef(null);

  const handleSubmit = e => {
    e.preventDefault();
    alert(nameEl.current.value);
  };

  return (
     <form onSubmit={handleSubmit}>  <label>Name: <input type="text" ref={nameEl} />  </label>  <input type="submit" name="Submit" />  </form>
   );
} 
```

除了用`useRef`钩子替换掉`createRef`之外，这里没有太大的区别。

### 示例:登录表单

```
function LoginForm(props) {
  const nameEl = React.useRef(null);
  const passwordEl = React.useRef(null);
  const rememberMeEl = React.useRef(null);

  const handleSubmit = e => {
    e.preventDefault();

    const data = {
      username: nameEl.current.value,
      password: passwordEl.current.value,
      rememberMe: rememberMeEl.current.checked,
    }

    // Submit form details to login endpoint etc.
    // ...
  };

  return (
     <form onSubmit={handleSubmit}>  <input type="text" placeholder="username" ref={nameEl} />  <input type="password" placeholder="password" ref={passwordEl} />  <label>  <input type="checkbox" ref={rememberMeEl} /> Remember me </label>  <button type="submit" className="myButton">Login</button>  </form>
   );
} 
```

[在 CodePen 上查看](https://codepen.io/SitePoint/pen/rNLjKRB?editors=1111)

虽然不受控制的输入对于快速简单的表单来说工作得很好，但是它们也有一些缺点。您可能已经从上面的代码中注意到，我们必须在需要时从 input 元素中读取值。这意味着我们不能在用户输入时对字段进行即时验证，也不能强制执行自定义输入格式、有条件地显示或隐藏表单元素，或者禁用/启用提交按钮。

幸运的是，React 中有一种更复杂的方法来处理输入。

## 受控输入

当 React 负责维护和设置其状态时，输入被称为“受控的”。状态与输入的值保持同步，这意味着更改输入将会更新状态，而更新状态将会更改输入。

让我们通过一个例子来看看它是什么样子的:

```
class ControlledInput extends React.Component {
  constructor(props) {
    super(props);
    this.state = { name: '' };
    this.handleInput = this.handleInput.bind(this);
  }

  handleInput(event) {
    this.setState({
      name: event.target.value
    });
  }

  render() {
    return (
      <input type="text" value={this.state.name} onChange={this.handleInput} />
    );
  }
} 
```

如您所见，我们建立了一种循环数据流:从状态到输入值，从更改事件到状态，然后再返回。这个循环允许我们对输入进行大量的控制，因为我们可以即时对值的变化做出反应。正因为如此，受控输入不会受到非受控输入的限制，从而带来了以下可能性:

*   **即时输入验证**:我们可以给用户即时反馈，而不必等待他们提交表单(例如，如果他们的密码不够复杂)
*   **即时输入格式**:我们可以给货币输入添加适当的分隔符，或者对电话号码进行动态分组
*   **有条件地禁用表单提交**:我们可以在满足特定条件后启用提交按钮(例如，用户同意条款和条件)
*   **动态生成新的输入**:我们可以根据用户之前的输入向表单中添加额外的输入(例如，在酒店预订中添加额外人员的详细信息)

## 确认

正如我上面提到的，受控组件的连续更新循环使得在用户输入时对输入进行连续验证成为可能。附加到输入的`onChange`事件的处理程序将在每次击键时被触发，允许您立即验证或格式化值。

### 示例:信用卡验证

让我们看一个真实的例子，当用户在支付表单中输入信用卡号时，检查信用卡号。

该示例使用一个名为[信用卡类型](https://github.com/braintree/credit-card-type)的库来确定用户输入时的发卡方(如 Amex、Visa 或 Mastercard)。然后，该组件使用此信息在输入旁边显示发行者徽标的图像:

```
import  creditCardType  from  "credit-card-type";

function CreditCardForm(props) {
  const [cardNumber, setCardNumber] = React.useState("");
  const [cardTypeImage, setCardTypeImage] = React.useState(
    "card-logo-unknown.svg"
  );

  const handleCardNumber = (e) => {
    e.preventDefault();

    const value = e.target.value;
    setCardNumber(value);

    let suggestion;

    if (value.length > 0) {
      suggestion = creditCardType(e.target.value)[0];
    }

    const cardType = suggestion ? suggestion.type : "unknown";

    let imageUrl;

    switch (cardType) {
      case "visa":
        imageUrl = "card-logo-visa.svg";
        break;
      case "mastercard":
        imageUrl = "card-logo-mastercard.svg";
        break;
      case "american-express":
        imageUrl = "card-logo-amex.svg";
        break;
      default:
        imageUrl = "card-logo-unknown.svg";
    }

    setCardTypeImage(imageUrl);
  };

  return (
    <form>  <div className="card-number">  <input
          type="text"
          placeholder="card number"
          value={cardNumber}
          onChange={handleCardNumber}
        />  <img src={cardTypeImage} alt="card logo" />  </div>  <button type="submit" className="myButton"> Login </button>  </form>
  );
} 
```

输入的`onChange`处理程序用当前值调用`creditCardType()`函数。这将返回一个匹配数组(或空数组),可用于确定显示哪个图像。然后，图像 URL 被设置为一个状态变量，以呈现到表单中。

[https://codesandbox.io/embed/react-controled-input-example-yiggb?fontsize=14&hidenavigation=1&theme=dark](https://codesandbox.io/embed/react-controled-input-example-yiggb?fontsize=14&hidenavigation=1&theme=dark)

您可以使用[中的一些数字来测试输入。](https://stripe.com/docs/testing)

## 表单库

您可能已经注意到，在处理表单时，有一定数量的锅炉板代码，特别是必须将输入与它们的状态值和处理程序连接起来。如您所料，有各种各样的第三方库可以帮助您摆脱处理更大更复杂表单的痛苦。

为了让您对使用表单库有所了解，让我们快速看一下一个叫做 [Fresh](https://github.com/leveluptuts/fresh) 的表单库。这个库的目标是用一个简单易用的 API 覆盖 90%的常见用例。以下是您可能在 web 应用程序中找到的个人资料编辑表单示例:

```
import { Form, Field } from "@leveluptuts/fresh";

const securityQuestions = [
  "What is your mother's maiden name?",
  "What was the name of your first pet?",
  "What was the name of your first school?"
];

const handleSubmit = (data) => console.log(data);

function UserProfileForm() {
  return (
    <Form formId="user-profile" onSubmit={handleSubmit}>  <Field required>First Name</Field>  <Field required>Last Name</Field>  <Field required type="email"> Email </Field>  <Field required type="select" options={securityQuestions}> Security Question </Field>  <Field required>Security Answer</Field>  <Field type="textarea">Bio</Field>  </Form>
  );
} 
```

[https://codesandbox.io/embed/fresh-library-example-s8qi6?fontsize=14&hidenavigation=1&theme=dark](https://codesandbox.io/embed/fresh-library-example-s8qi6?fontsize=14&hidenavigation=1&theme=dark)

Fresh 提供了一些定制组件，使得创建表单变得非常简单。`Field`组件负责连接表单输入上的数据绑定，将您提供的标签转换成输入值的 camel-case 属性名。(例如，在表单的状态中，“姓氏”变成了`lastName`。)

`Form`组件包装所有字段，并接受一个`onSubmit`回调，该回调将表单数据作为一个对象接收。下面是该表单的输出示例:

```
{
  firstName: "Bill",
  lastName: "Gates",
  email: "billg@microsoft.com",
  securityQuestion: "What was the name of your first pet?",
  securityAnswer: "Fluffy",
  bio: "Bill Gates is a technologist, business leader, and philanthropist. He grew up in Seattle, Washington, with an amazing and supportive family who encouraged his interest in computers at an early age."
} 
```

如您所见，像这样的库确实可以加快处理表单的速度，并使您的组件不那么冗长。对于除了基本的小型表单之外的任何东西，我建议选择一个符合您需求的表单，因为从长远来看，它将节省您的时间。

## 结论

现在，您应该对如何在 React 中使用表单和输入有了深入的了解。您应该知道受控输入和非受控输入之间的区别，以及各自的优缺点，要知道受控输入的紧密更新循环允许您有许多选项来动态地格式化和验证值。最后，您应该知道有一些表单库可以防止您向 React 表单中添加大量重复和冗长的样板代码，这将有助于您成为一名更高效的开发人员。

## 分享这篇文章