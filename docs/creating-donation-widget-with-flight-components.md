# 使用航班组件创建捐赠小部件

> 原文：<https://www.sitepoint.com/creating-donation-widget-with-flight-components/>

*这篇文章由[汤姆·格列柯](https://www.sitepoint.com/author/tgreco/)进行了同行评审。感谢 SitePoint 的所有同行评审员使 SitePoint 的内容尽可能做到最好！*

在本教程中，我将通过制作一个捐赠小部件来教你 Twitter 的 [Flight.js](https://flightjs.github.io/) 的基本知识，它也使用[物化](http://materializecss.com/)作为前端，使用[条纹](https://stripe.com/)来处理支付。我们将涵盖飞行的主要概念和方法。

Flight 是 Twitter 的一个事件驱动框架。基于组件，Flight 独立地将行为映射到 DOM 节点。与其他流行的框架不同，Flight 并没有规定如何呈现或获取数据的特定方法，但是它依赖于 jQuery。本质上，飞行是关于事件的。这些可以由 DOM 或其他 UI 组件中的人工触发器来触发。飞行基本上是做框架的框架。这听起来可能很复杂，但是虽然 Flight 不像 jQuery 那样是 1-2-3，但是它的学习曲线被夸大了，学习 Flight 肯定可以提高您的 JavaScript 技能。

## 为什么使用 Flight？

*   编写可读性更强的 jQuery。
*   编写可重用的组件。
*   您可以根据需要使用或多或少的其他库。
*   更好的代码结构。
*   支持甚至偏爱模块化 JS。

### 可读性:jQuery 与 Flight

假设我们正在监听按钮上的点击和悬停。使用 jQuery，您可能会这样做:

```
$('#button').on('click', function() {
  confirm('You clicked?');
});
$('#button').on('mouseover', function() {
  console.log('Oops');
}); 
```

但是使用 Flight，所有这些都变成了一个组件。

```
var Button = flight.component(function () {
  this.log = function () {
    console.log('Oops!');
  }
  this.confirm = function () {
    confirm('You clicked?');
  }
  this.after('initialize', function(){
    this.on('mouseover', this.log);
    this.on('click', this.confirm)
  })
});

Button.attachTo('#button'); 
```

当然，jQuery 需要的代码更少，但是有了 Flight，我们的代码结构更加清晰。在 jQuery 中，事件是两个不同的线程，但是实际上它们都包含在同一个组件中。我们可以很容易地看到，这个组件和它所连接的元素正在监听两个事件。但是，假设我们想在代码的后面 200 行添加那个`hover`事件监听器。使用 jQuery，您可能会将它添加到您的文件中。然而，使用 Flight，我们几乎被迫将它添加到现有组件中。

我做了一个模板，包含了你入门所需的一切，这样你就可以直接开始编码了。可以从 [CodePen](http://codepen.io/SitePoint/pen/BKKQqw) 分叉/下载。

想自己做模板？只需复制这些 CDN:

```
<script src="https://code.jquery.com/jquery-2.1.1.min.js"></script>
<script src="http://flightjs.github.io/release/latest/flight.min.js"></script>
<script src="https://checkout.stripe.com/checkout.js"></script> 
```

## 制作你的第一个组件

飞行由“组件”组成。组件是应用程序中独立的可重用代码块。创建组件与创建对象构造函数非常相似，只是组件在初始化后不能更改或访问。组件只能通过事件与您的应用程序通信。无论是触发它们，还是聆听它们。

飞行组件可以简单，也可以复杂。在下面的例子中，我们只监听按钮上的悬停(鼠标悬停)事件，并在发生时显示警告。

```
var Button = flight.component(function () {
  this.alert = function () {
    alert('Oops!');
  }
  this.after('initialize', function(){
    this.on('mouseover', this.alert);
  })
});

Button.attachTo('#button'); 
```

这显然不是 jQuery 做不到的，像这样组织代码现在看起来可能很费力，但是一旦你开始使用 Flight 处理悬停事件以外的事情，你就会理解它的好处。
只要你明白它在监听一个`mouseover`事件并触发一个内部功能，你现在就没事了。我将在本教程的后面解释其余部分的含义。

## 入门指南

如果您正在使用模板，那么您的`index.html`文件中的表单应该如下所示:

```
<form action="#">
    <input type="checkbox" id="accept" />
    <label for="accept">By proceeding your agree to our terms, which are completely unfair and well, not very real.</label>
    <br/>
    <button class="waves-effect btn" style="margin-top: 15px;" id="launch" disabled>Let's Go</button>
</form> 
```

我们希望在复选框被选中时启用按钮，否则禁用它。让我们转向 JavaScript 代码，创建我们的第一个组件。

```
var checkToEnable = flight.component(function () {
  // Magic
}); 
```

每个飞行组件由一些不同的方法组成。其中有些是必需的，有些不是。我们要添加的第一个方法是[属性()](https://github.com/flightjs/flight/blob/v1.x/doc/base_api.md#this.attributes)。`attributes()`方法包含一个或多个属性。属性是限定了范围的变量和/或参数。空属性(用值`null`声明的属性)需要在组件初始化时向其传递一个值。除非另有说明，否则其他属性将使用其默认值。属性通常用于保存元素引用。将以下代码插入到组件中:

```
this.attributes({
    button: null 
}); 
```

按钮`attribute`将作为我们想要启用的按钮的参考。我们要添加的下一个方法是 [initialize()](https://github.com/flightjs/flight/blob/v1.x/doc/base_api.md#this.initialize) 方法。

```
this.after('initialize', function () {
    this.on('change', this.enableButton); 
}); 
```

Flight 组件已经定义了默认的`initialize()`实现，我们希望扩展它而不是覆盖它，这就是为什么我们在这里使用了 [after()](https://github.com/flightjs/flight/blob/v1.x/doc/advice_api.md#thisafterexistingfuncname-customfunc) 方法。在回调函数内部，我们添加了一个[事件监听器](https://github.com/flightjs/flight/blob/v1.x/doc/base_api.md#this.on)。这将监听组件将要连接到的元素的变化，并因此触发`enableButton()`函数，这是我们接下来要创建的。

```
this.enableButton = function (e) {
    var buttonEl = document.getElementById(this.attr.button);
    switch (e.target.checked) {
        case true:
            buttonEl.disabled = false;
            break;
        case false: 
            buttonEl.disabled = true;
            break;
    }
}; 
```

这不做任何太花哨的事情。这只是一个简单的函数，当这个组件将被附加到的复选框被选中时，它将激活这个按钮，反之亦然。你可能已经注意到我们通过调用 this.attr 来访问`attribute`。这是一种不好的做法，稍后我会向你展示一个更好的解决方案。

我们的组件还没有工作。为了完成它，我们需要将它附加到 DOM。这发生在组件之外。一个组件可以附加到任意多的元素上，也可以附加到文档上，但是组件必须放在前面。我们将把对按钮元素的引用作为参数传入。

```
checkToEnable.attachTo('#accept', {
    button: 'launch'
}); 
```

太好了！您已经创建了您的第一个飞行组件。概括地说，它应该是这样的:

```
var checkToEnable = flight.component(function () {
    this.attributes({
        button: null 
    });

    this.enableButton = function (e) {
        var buttonEl = document.getElementById(this.attr.button);
        switch (e.target.checked) {
            case true:
                document.getElementById(this.attr.button).disabled = false;
                break;
            case false: 
                document.getElementById(this.attr.button).disabled = true;
                break;
        }
    };

    this.after('initialize', function () {
        this.on('change', this.enableButton); 
    });
}); 
```

## 潜入水中

到目前为止，您应该理解了三个最重要的概念:属性、回调函数和`initialize()`。大多数飞行部件都由这些零件组成。我们的下一个将使用飞行的核心概念:事件冒泡。事件冒泡听起来有点复杂，但实际上并没有那么难理解。例如，假设我有一个`button`，它的父节点是一个`div`。当`button`被点击时，它的事件将冒泡到`div`，假设我们的组件连接到了`div`。

这正是我们下一个组件的工作方式。它将以物化模型的形式附加到捐赠小部件，但是我们将监听来自其子部件的事件。首先，我们需要将模态的标记添加到我们的`index.html`文件中。将其插入到结束正文标记之前:

```
<div id="stripe-widget" class="modal">
    <div class="modal-content">
    <h4>Give us your money.</h4>
    <p>We'll use it well, we promise.</p>
    <form action="#">
        <p class="range-field">
        <input type="range" id="stripe-amount" value="10" min="0" max="100" />
    </p>
    </form>
    </div>
    <div class="modal-footer">
    <button class="btn blue waves-effect waves-blue" id="checkout" disabled>Donate <span data-amount=""></span> <i class="fa fa-cc-stripe"></i></button>
    <a href="#!" class=" modal-action modal-close waves-effect waves-red btn-flat">Close</a>
    </div>
</div> 
```

现在让我们创建我们的组件。

```
var getAmount = flight.component(function () {
    // Magic
}); 
```

为了更好地理解我们的组件，我们将以相反的顺序添加它的方法。首先添加`initialize`方法。

```
this.after('initialize', function () {
    this.on(this.attr.range,'change', this.onChange); 
    this.on(this.attr.checkout, 'click', this.onClick);
    }); 
```

看起来不一样了，不是吗？因为我们的组件附加到捐赠小部件，所以我们在事件侦听器中传递它的两个子部件。这可能现在没有意义，但很快就会有意义。让我们添加属性。

```
this.attributes({
    checkout: '#checkout',
    range: '#stripe-amount',
    display_amount: '[data-amount]'
}); 
```

这些属性与当前标记一起工作。将来您可能会将这个组件添加到一些不同的标记中，在这种情况下，您可以传入不同的选择器。接下来我们将添加`onChange()`函数。

```
this.onChange = function (event) {
    var amount = this.select('range').val();
    if (amount == 0) {
      alert('please enter an amount');
       this.select('checkout').prop('disabled', true);
    } else {
     this.select('checkout').prop('disabled', false);
      this.select('display_amount').text('$' + amount);
      this.select('checkout').attr('data-stripe-amount', amount);
     }
    }; 
```

最突出的一个方法是 [select()](https://github.com/flightjs/flight/blob/v1.x/doc/base_api.md#this.select) ，它很像 jQuery 的 find 方法，但是它的范围只包括附加元素(捐赠小部件)的子元素。需要理解的重要一点是，我们将属性名作为字符串引用。起初我对此感到困惑，所以请记住这一点，因为这实际上是 Flight 创建的快捷方式之一。

由于该按钮已被启用，它现在可以监听事件。现在我们来添加`onClick()`函数。

```
this.onClick = function (event) {
    var stripeAmount = this.select('checkout').attr('data-stripe-amount');
    stripeAmount = stripeAmount + 0 + 0;
    this.trigger('callStripe', {
        amount: stripeAmount
    });
}; 
```

首先，我们从按钮的属性中获取金额，然后通过添加两个零使其对 Stripe 有效。不过这并不新鲜。真正神奇的事情发生在[触发方法](https://github.com/flightjs/flight/blob/v1.x/doc/base_api.md#this.trigger)中，它触发一个定制事件，并将数量作为数据(也称为有效负载)传递。接下来，我们将创建一个组件来监听该事件。完成的组件应该如下所示:

```
var getAmount = flight.component(function () {
    this.attributes({
        checkout: '#checkout',
        range: '#stripe-amount',
        display_amount: '[data-amount]'
    });

    this.onChange = function (event) {
        var amount = this.select('range').val();
        if (amount == 0) {
            alert('please enter an amount');
            this.select('checkout').prop('disabled', true);
        } else {
            this.select('checkout').prop('disabled', false);
            this.select('display_amount').text('$' + amount);
            this.select('checkout').attr('data-stripe-amount', amount);
        }
    };

    this.onClick = function (event) {
        var stripeAmount = this.select('checkout').attr('data-stripe-amount');
        stripeAmount = stripeAmount + 0 + 0;
        this.trigger('callStripe', {
            amount: stripeAmount
        });
    };

    this.after('initialize', function () {
        this.on(this.attr.range,'change', this.onChange); 
        this.on(this.attr.checkout, 'click', this.onClick);
    });
}); 
```

在我们创建最后一个组件之前，我们必须`initialize`前一个组件。因为模型本身是动态初始化的，我们将在它被初始化后附加它。下面的代码只是我们在第一个组件中启用的按钮的一个简单的 click 事件侦听器，之后我们附加新的组件。`openModal()`方法属于物化。

```
$('#launch').on('click', function (event) {
    event.preventDefault();
    $('#stripe-widget').openModal();
    getAmount.attachTo('#stripe-widget'); 
}); 
```

## 相遇条纹

简而言之，Stripe 是你一直想象中的 PayPal，是为开发者设计的。许多网站和应用程序(Twitter 和 Kickstarter 等)都使用 Stripe 来处理支付。他们提供了一系列的服务或 API(无论你想怎么称呼他们)，但是我们将要使用的是 [Checkout](https://stripe.com/checkout) 。

Stripe 验证了某人的信用卡后，您的网站将收到一个令牌，该令牌需要与您的密钥一起发送给 Stripe。因为您的密钥是秘密的，所以这不可能发生在前端，因为 JavaScript 过去和现在都不应该是安全的，至少在它的原始形式中是安全的。要做到这一点，你可以使用 Stripe 的一个库，用于 [PHP](https://stripe.com/docs/checkout/guides/php) 、 [Sinatra](https://stripe.com/docs/checkout/guides/sinatra) 、 [Python (Flask)](https://stripe.com/docs/checkout/guides/flask) 、 [Rails](https://stripe.com/docs/checkout/guides/rails) 或 [Node.js](https://stripe.com/docs/api/node#intro) 。

我提到了钥匙，对吗？嗯，要获得密钥，你需要[注册](https://stripe.com/)一个免费的 Stripe 账户。你自己甚至不需要信用卡，一个普通的银行账户就可以了！在你得到你自己的密钥后，当调用 Stripe 时，你必须用你的密钥替换我的公钥(如下所示)。它们提供了“测试”和“真实”键，因此您可以轻松地测试您的应用程序。使用测试密钥时，Stripe 也将接受测试信用卡。

在你点击捐赠按钮之前，一切都应该正常，没有任何反应。让我们添加最后一点魔法。

```
var launchStripe = flight.component(function () {
    // Magic
}); 
```

这个组件将为我们启动 Stripe。为了做到这一点，我们首先必须在文档的顶部创建一个 Stripe 实例(不是在组件中)。

```
var handler = StripeCheckout.configure({
    key: 'pk_test_hue7wHe5ri0xzDRsBSZ9IBEC', //replace this with your key!
    image: 'http://freedesignfile.com/upload/2014/06/Cup-of-coffee-design-vector-material-03.jpg',
    locale: 'auto',
    token: function(token) {
        console.log(token);
        var html = 'Thank you! <i class="fa fa-beer"></i>';
        Materialize.toast(html, 3000);
        // Send to server
    }
}); 
```

虽然进入后端有点超出了本文的范围，但如上所述，Stripe 有一个 Node.js 库，对于熟悉 JavaScript 的人来说可能没那么可怕。您可以使用类似于 [Express](http://expressjs.com/) 的模块通过 HTTP 请求将您的密钥发送到节点服务器。在节点服务器对该请求的响应中，您应该这样做:

```
var stripe = require("stripe")(
  "sk_test_yourkeyhere"
);

stripe.customers.create({
  description: 'Coffeehouse Customer',
  source: "tok_yourtoken" // This comes from our front end JavaScript code
}, function(err, customer) {
  // asynchronously called
}); 
```

如果你想要一个使用 Stripe 和 Node.js 的更完整的例子，SitePoint 关于用 Stripe & Syncano 构建日常确认 SMS 服务的文章更详细地介绍了该过程的 Node.js 方面，甚至展示了如何使用 Syncano 这样的微服务平台从后端为你运行 Node.js 代码。

现在让我们回到我们的组件。我们将首先添加`initialize`方法，并让它监听我们在第二个组件中触发的事件。

```
this.after('initialize', function () {
    this.on('callStripe', this.launch);
}); 
```

我们基本上是在监听一个定制的、内部触发的事件，而不是 DOM 事件。我们现在要做的就是创建回调，这将启动 Stripe。

```
this.launch = function (event, data) {
    $('#stripe-widget').closeModal();
    handler.open({
        name: 'the Coffeehouse',
        description: 'Thank You!',
        currency: "usd",
        amount: data.amount
    });
}; 
```

我们的函数需要两个参数，事件和数据。该事件与往常一样，但是数据包括我们在前面的组件中最初触发事件时包含的有效负载。有效载荷就是我们的数量，因为这是我们添加到事件中的所有内容。但是在更复杂的情况下，这可能是来自 API 调用的完整响应。

我们正在做的另一件事是使用我们从前一个组件获得的金额启动 Stripe 的支付表单。你可以在这里找到完整的结帐文档。

最后，我们需要初始化最后一个组件。我们不是把它附加到一个特定的元素上，而是把它挂在文档上。

```
launchStripe.attachTo(document); 
```

我们完成的代码应该类似于[这个](http://www.nielsklom.eu/flight_demo/)。我们在不到 100 行的 JavaScript 代码中做了很多！

## 结论

希望飞行现在有点意义，但无论情况如何，你都可以在这里找到完整的文档。正如你所看到的，Flight 的组件系统产生了可读性极强的模块化代码。例如，您可以在任何想要处理付款的时候重用`launchStripe`组件，或者在任何想要在启用按钮之前选中复选框的时候重用`enableButton`组件。飞行就像这样很棒，因为它没有规定特定的方法，所以可能性是无限的。

如果你已经用 Flight 制作了一些有趣的东西，或者你只是想分享你的想法，请在下面评论！

## 分享这篇文章