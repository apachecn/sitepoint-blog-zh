# 如何使用 Stripe & Syncano 构建每日确认短信服务

> 原文：<https://www.sitepoint.com/how-to-build-a-daily-affirmations-sms-service-with-stripe-syncano/>

*本文由[辛卡诺](https://www.syncano.io/)赞助。感谢您对使 SitePoint 成为可能的赞助商的支持。*

Syncano 为实时应用提供了一个基于云的平台。它存储数据、微服务代码、自动化代码执行的时间表、用户帐户、通过 HTTP 访问这些功能的 webhooks 等等。他们甚至有一个代码片段的开源社区，并支持各种运行时环境，包括 Node、Python、Go 和 Ruby。

从开发人员的角度来看，Syncano 提供了许多后端基础设施，让您更容易上手，否则您需要自己动手组装。在本文中，我们将了解使用 Syncano 设置 web 应用程序并将其链接到第三方服务所涉及的内容。

## 你需要什么

为了能够跟随本教程，掌握 HTML、CSS 和 JavaScript 的基础知识对你来说会很方便。

在演示应用程序中，我们使用 jQuery 将一些简单的原型屏幕和动作放在一起。我想强调一点——在一个合适的应用程序中，你应该使用一种更加可靠和简洁的方式来构建你的 web 应用程序。我推荐一个 JavaScript 框架，比如 React、Angular、Meteor 等等。对于这个演示，我们有意避免使用这样的 JavaScript 框架，以保持简单，并专注于 Syncano 在其中的作用。使用我们的 Syncano 功能片段，并根据您自己喜欢的框架调整前端 JavaScript！

## 我们的演示应用程序

为了从初学者的角度探索 Syncano 的可能性，我们将开发一个 web 应用程序，以每月 1 美元的低价每天向注册用户发送积极的肯定！我们将探索用户注册、安排活动、通过 Twilio 发送短信和设置支付。

## 代码

这个演示的代码都可以在 [GitHub](https://github.com/patcat/SyncanoDemo) 上找到。

## Syncano 入门

如果你没有 Syncano 账户，你可以在[Syncano.com](http://www.syncano.com)免费登录。你会在右上角找到一个“注册”按钮:

![The Syncano signup button on the top right](img/e02212126e1411a902d6f9558084e5b6.png)

填写您的电子邮件地址和首选密码，然后点击“创建我的帐户”:

![Filling in account details](img/28c9998ce62cf96e2de03e0ed30c2789.png)

注册后，您将可以访问您的 Syncano 仪表盘。您可以通过访问 [dashboard.syncano.io](https://dashboard.syncano.io) 随时访问。当您登录后，您将拥有一个“实例”,我们将在演示应用程序中使用它。如果您已经在使用 Syncano，并且想要创建一个新的，单击右下角的“+”图标(注意，您不能给实例一个自定义名称，除非您使用他们的 [API explorer](http://docs.syncano.io/v0.1/docs/api-explorer-usage) ，但是这超出了本文的范围！):

![The initial dashboard upon signing up](img/805f31cab593fc4dfbee3e84926e0c59.png)

### 生成 Syncano API 密钥

单击该实例将其打开。一开始看起来会很空，左边有很多选择。你要做的第一件事是点击“API 密匙”菜单选项来获得你的 API 密匙:

![Going to the Get API Key screen](img/2854323be054cdadce68c0dbd0e68c26.png)

然后，通过单击“生成 API 密钥”为该实例生成 API 密钥:

![Generating an API key](img/6f00b6120ebb7017f296fceff3a868fd.png)

Syncano 会弹出一个窗口来提供 API 键设置。您可以更新三种设置:

*   **描述—**您的 API 密钥的描述。我们称我们的为“肯定”。
*   **忽略 ACL–**这将忽略我们应用程序中为资源设置的任何权限，我们不需要它。
*   **用户注册–**这允许人们在这个 API 密钥内注册，我们将希望勾选此选项！

输入所有这些设置后，单击“确认”:

![Inputting in API key settings](img/529c358754a8f153703b078b1c7399dc.png)

现在你应该看到你的 API 密匙被列出来了，把那个密匙复制到某个地方，准备在你的代码中使用它。如果您丢失了它，只需回到仪表板中的“API 密钥”页面:

![Copy that API key](img/b315636d4599e7ff995a652d1b618ecd.png)

### 查找您的 Syncano 帐户密钥

为了在 Syncano 中进行一些调用，您需要生成一个 API 密钥。我们可以通过帐户屏幕来完成。为此，我们将点击右上角的个人资料图片，然后点击带有我们电子邮件的区域，进入帐户屏幕:

![The account icon](img/46ff3363ad09cb82c42f5865c3cd2874.png)

在这里，我们单击左侧菜单中的“身份验证”项，然后单击“复制”来复制显示的帐户密钥。暂时把它存放在安全的地方！

![The account key screen](img/e98a6c6a004e2ed5b93629d561f46b25.png)

我们希望尽可能坚持使用 API 密匙，尤其是在 Syncano 生态系统之外的任何地方。

### Syncano 的 JavaScript 入门

Syncano 提供了多种方式将他们的 JavaScript 库包含到您的应用中:

*   **直接从 GitHub 下载 JavaScript 文件——**你可以在他们的 GitHub 上找到最新的 JavaScript 库([从这个链接](https://github.com/Syncano/syncano-js-lib/archive/master.zip)下载 zip 文件)。您将在`dist/syncano.min.js`下找到您需要的 JavaScript 文件。
*   **包括他们的 npm 模块—**如果你喜欢使用 npm(通过 Node，Browserify…等)，你可以通过`npm install syncano --save`安装他们的模块
    *   然后，您可以通过`var Syncano = require('syncano');`将它包含在您的项目中
*   **包括他们的凉亭模块—**如果你喜欢凉亭，你可以通过`bower install syncano`安装他们的模块
    *   通过`<script src="path/to/bower_components/syncano/dist/syncano.min.js">
        </script>`包含此内容

## Syncano 中的用户帐户

Syncano 提供了用户帐户功能，您可以立即使用。如果我们点击左边的菜单项，进入“Classes”页面，我们可以看到我们的`user_profile`类已经准备好，等待存储用户详细信息:

![User profile ready and waiting](img/b37a384603cca1b22aeb2b79bc38fee5.png)

一旦我们在系统中有了一个用户，我们还可以在“用户”页面上看到我们的用户。“用户”页面将显示他们的电子邮件地址，以及他们是否被分配到任何组(在本演示中我们不会分配组)，带有`user_profile`类的“类”页面将向我们显示更多细节，包括我们将为我们的应用程序特别添加的自定义字段。

## 添加自定义 Syncano 类

为了存储我们的断言，我们将在 Syncano 中创建一个新的类来存储它们。为此，请转到“类别”页面，单击加号图标创建一个新类别:

![Adding a new class](img/ae066e84ee25c6e8f2a563efff2b73fb.png)

在这个屏幕中，我们将该类命名为“Affirmation ”,并对其进行描述。在“Schema”下面，我们为这个类设置了一个单独的字段，我们也称之为“affirmation”。我们将其设置为一个文本字段，然后单击“添加”。然后我们点击“确认”来保存新的类。确保你点击“添加”和“确认”来保存所有内容！

![Setting up the Affirmation class](img/8a6707c9713c8c79d0f0c989af02f3b0.png)

我们的`affirmation`类现在可以在“Classes”页面上看到了。单击它进入一个屏幕，您可以在其中添加一些确认:

![Affirmations added to the Classes page](img/20091df2c9e7185795ad2a75734ec409.png)

一旦你添加了一些，屏幕应该是这样的:

![Affirmations list](img/0a59110cc157ba7b1a576ab5a0f6603d.png)

## 整合短信

我们的应用程序的主要特点是，它会每天向用户发送一条积极快乐的肯定短信。我们将使用 Twilio 发送这些短信，Twilio 是一种提供语音和短信服务的服务。

### 设置 Twilio 号码

前往 [Twilio](https://www.twilio.com/) ，点击右上角的“注册”按钮，注册免费试用。

![The Twilio homepage](img/10eb7a814111d7b9e574f68ad27e4fc7.png)

按照提示创建您的帐户。完成后，你可能会看到一个关于设置语音的屏幕——不要设置！我们不需要语音功能。我们在寻找信息。这是你不想要的屏幕:

![The Twilio voice screen](img/b0e41e87c1d6ad62b19d01ee695adfcf.png)

取而代之的是，进入 [Twilio“开始使用”屏幕发送信息](https://www.twilio.com/user/account/messaging/getting-started)。点击屏幕上显示“获得您的第一个 Twilio 号码”的红色按钮:

![The Twilio Getting Started screen](img/1870258d99e0e139700cd245aa0d381c.png)

如果下一个屏幕显示一个带有 SMS 功能的号码，请直接点击“选择该号码”。

然而，如果你不小心点击了语音屏幕上的一个号码(或出于其他未知原因)，你可能会发现 Twilio 建议的号码没有提到短信功能。如果只显示“语音”，点击“不喜欢这个？搜索不同的号码”:

![The Twilio Get Number screen](img/2ebbb2fd98256372118836e65166fcd9.png)

在下一个屏幕上，勾选“短信”，然后点击“搜索”:

![Searching for an SMS number](img/de62ede5560a6540683e622c40038796.png)

然后选择任何号码，只要它有短信功能(由于我们之前的选择，他们应该有):

![Choosing an SMS number](img/a89b81956ba1265633111af1116c407a.png)

Twilio 现在将向您展示您的 Twilio 号码！请将该号码复制到安全的地方，因为我们很快就会用到它。

![Successful selection of SMS number](img/0cfc6a9492bb1df7531d9e6d2bceb465.png)

### 查找您的 Twilio API 凭据

在用于消息传递的 [Twilio“入门”屏幕上，还有我们的应用程序需要的最后一条信息——我们的 Twilio API 证书。要找到这些，请单击右上角的“显示 API 凭据”链接。然后你会看到你的“帐户 SID”和“认证令牌”。将这些拷贝到安全的地方(最好是你拷贝电话号码的地方)。我们很快也会在我们的应用中使用它们。](https://www.twilio.com/user/account/messaging/getting-started)

![Your Twilio API credentials](img/3d785fc78214520d8e769949a77c3516.png)

### Syncano 解决方案

Syncano 的一个非常好的特性是它的开源解决方案库，只需点击几下就可以添加到您的项目中。这些解决方案甚至不需要和你的应用程序的其他部分用同样的语言编写！他们会添加您需要的所有功能，您只需要提供您的应用程序需求的细节。使用 Twilio 发送短信非常简单，因为已经有了一个名为“发送短信”的 Syncano 解决方案。

要安装我们的“发送短信”解决方案，我们点击右上角的“解决方案”链接，然后找到左边的“短信”标签进行过滤。通过这种方式找到“发送短信”解决方案要容易得多！一旦我们有了它，点击它的盒子右下角的箭头图标来安装它。

![Installing the Send SMS solution](img/e6f5c76d58a869e18658ca67507510d6.png)

将出现一个弹出窗口，询问我们要将它保存在哪个实例中(到目前为止我们只有一个实例，所以这很简单！)以及我们要保存解决方案的哪个版本。让它们保持原样，然后单击“确认”:

![Installing the Send SMS solution](img/ecdc63455da243408a55deb207f3136c.png)

### Syncano 密码箱

我们可以在 Syncano 托管的应用程序中运行的每个代码片段都被称为“代码箱”。现成的代码箱将作为解决方案的一部分出现，您也可以自己制作。我们将在本指南中做到这两点！

如果我们点击“代码箱”菜单链接，我们会发现“发送短信”解决方案为我们创建了一个名为“发送短信”的代码箱。旁边的图标告诉我们代码盒是用 Python 写的。幸运的是，正如我前面提到的，我们可以让用不同语言编写的组件一起工作。这是一种非常有趣的工作和分享功能的方式！

![The Send SMS CodeBox appears](img/1c3a798ec555dce67fdb15e419b3e5f9.png)

如果我们单击 CodeBox 项，Syncano 将为它打开代码编辑器。这将允许我们编辑其中的一些变量，以匹配我们的 Twilio 凭据和设置。我们需要更新以下内容:

*   `SYNCANO_API_KEY`–将此更新为我们之前复制的 Syncano API 密钥。
*   `ENTER_INSTANCE_NAME_HERE`–将其更新为您的 Syncano 实例名称(例如 bold-rain–1234)。
*   `ENTER_YOUR_ACCOUNT_SID_HERE`–将此更新至您的 Twilio 帐户 SID。
*   `ENTER_YOUR_AUTH_TOKEN_HERE`–将此更新到您的 Twilio 授权令牌。
*   `from_number`–此变量需要等于您之前选择的 Twilio 电话号码。

![Customising the Send SMS CodeBox](img/98f9939a8a5ad029b9ff7b9307bb6cbc.png)

查看代码框，我们可以看到它需要三个参数才能运行。这些用`ARGS.get('argument_name', None)` ( `None`是它的初始值)表示:

```
message_body = ARGS.get('body', None)  # Arg 'body' is passed to CodeBox
media_url = ARGS.get('media_url', None)  # Arg 'media_url' -- gif, jpeg, or png
to_number = ARGS.get('to_number', None)  # Arg 'to_number' is receiving number, ie; "+13475555717"
```

### 我们预定的誓词密码箱

为了为我们的每个用户运行这个，我们将创建我们自己的代码盒，它将触发带有我们的用户详细信息和消息正文确认的`sendsms`代码盒。

要创建新的代码箱，我们单击“代码箱”页面右下角的加号图标:

![The Add New CodeBox button](img/1e32cc5b80ac0ea928bd3247f305236f.png)

在出现的屏幕上，我们将新代码箱命名为“Scheduled Affirmation”，如果愿意，我们可以给它一个描述，为运行时环境选择“nodejs”，然后单击“Confirm”:

![Adding a new Scheduled Affirmation CodeBox](img/983ba27c4412975d4ed2fd263a0f4b44.png)

我们的自定义代码箱代码看起来会像这样:

```
var Syncano = require('syncano');
var _ = require('lodash');
var account = new Syncano({accountKey: 'YOURKEYHERE'});

account.instance('bold-rain-5584').class('affirmation').dataobject().list()
.then(function(res){
  var randomId = _.random(0, res.objects.length - 1),
      messageToSend = res.objects[randomId].affirmation;

  console.log('Sending message of ', messageToSend);

  var filter = {
    "query": {"subscribed":{"_eq":true}}
  };

  account.instance('bold-rain-5584').class('user_profile').dataobject().list(filter, function(err, res) {
    if (err) {
      console.log('Error!');
      console.log(err); return;
    }
    _.each(res.objects, function(user) {
      var payload = {"payload":{'body': messageToSend, 'to_number': user.phone}};

      console.log({"body": messageToSend, "to_number": user.phone});

      account.instance('bold-rain-5584').codebox(2).run(payload, function(err, res) {
        console.log('Just sent that SMS out.');
      });
    });
  });
})
.catch(function(err) {
  console.log('Error!');
  console.log(err); return;
});
```

我将解释这段代码的每一部分是做什么的。这与任何其他 Node.js 代码片段完全一样，只是它运行在 Syncano 的云中。(注意:我们还没有在 Syncano 中为此设置所有其余的部分，所以如果您试图直接运行它，这段代码将无法工作！)

就像任何其他节点应用程序一样，我们`require()`我们的依赖关系。我们将使用来自`lodash`的一些实用函数，所以我们包括了它(在撰写本文时，Syncano 没有可用的下划线模块，只有 lodash):

```
var _ = require('lodash');
```

由于 Syncano CodeBox 使用隔离的 Docker 映像来运行，所以我们需要包含 Syncano 模块来使用诸如查找类和用户之类的功能。要做到这一点，我们必须用我们的帐户密钥初始化 Syncano，这是我们前面找到的:

```
var Syncano = require('syncano');
var account = new Syncano({accountKey: 'YOURKEYHERE'});
```

然后，我们将使用`account.instance('bold-rain-5584').class('affirmation').dataobject().list()`函数遍历`affirmation`类中的所有断言。一旦确认列表加载完毕，我们使用 JavaScript 中的承诺通过返回的变量`res.objects`来`then`访问这些确认。

```
account.instance('bold-rain-5584').class('affirmation').dataobject().list()
.then(function(res){
  // In here we will look at res.objects
```

我们要做的第一件事是使用 lodash 得到一个介于零和我们所拥有的肯定数之间的随机数(减去 1，因为我们从零开始)。这就是我们如何选择我们的随机肯定。然后，我们通过将随机 ID 处的确认对象分配给变量`messageToSend`来选择该确认。

```
var randomId = _.random(0, res.objects.length - 1),
    messageToSend = res.objects[randomId].affirmation;

console.log('Sending message of ', messageToSend);
```

为了向我们的用户发出随机的肯定，我们需要首先列出他们。请记住，我们只想将此邮件发送给通过 Stripe 订阅了我们的确认服务的用户。我们还没有设置 Stripe 服务，但是我们将跟踪用户是否订阅的方法是在我们的`user_profile`类中切换一个名为`subscribed`的自定义值(我们也将很快设置它)。

Syncano 允许我们过滤我们的查询，以便我们只获得那些`subscribed`等于`true`的用户。我们通过我们的`user_profile`类列出，就像我们对`affirmation`类做的那样，但是通过传递一个 JSON 对象作为第一个参数来使用这个过滤，就像这样:

```
var filter = {
  "query": {"subscribed":{"_eq":true}}
};

account.instance('bold-rain-5584').class('user_profile').dataobject().list(filter, function(err, res) {
// We will look into our users in the res.objects variable here next!
```

在`user_profile`类列表中，我们首先检查任何错误。如果你想更彻底的话，也可以在`affirmation`类列表中添加同样的错误检查。总是捕捉错误是一个好习惯！

```
if (err) {
  console.log('Error!');
  console.log(err); return;
}
```

如果我们没有错误，那么我们使用 lodash 遍历每个用户，并将 JSON 与`messageToSend`(我们的确认)和通过`user.phone`在这个循环迭代中为用户找到的电话号码放在一起。我们也将很快设置自定义`phone`字段。

```
_.each(res.objects, function(user) {
  var payload = {"payload":{'body': messageToSend, 'to_number': user.phone}};
```

最后，我们通过`codebox(2).run()`函数运行我们的`sendsms`代码盒。`2`是我们之前记下的代码箱的 ID。一旦它完成运行，它会向控制台记录一条消息，让我们知道它运行成功。

```
account.instance('bold-rain-5584').codebox(2).run(payload, function(err, res) {
        console.log('Just sent that SMS out.');
      });
    });
  });
})
```

最后，在我们最初的承诺结束时，我们会发现任何错误:

```
.catch(function(err) {
  console.log('Error!');
  console.log(err); return;
});
```

### 为 SMS 准备用户帐户

我们现在需要将这些自定义字段添加到我们的用户详细信息中，以便我们的应用程序能够拥有向用户发送短信所需的所有信息。

回到我们之前在“课程”页面查看的仪表板的`user_profile`区域，单击三个圆点图标并选择“编辑课程”:

![Editing the user_profile class](img/6cf81aa9c859aff4444a9d340f1a124d.png)

在出现的屏幕中，我们能够向我们的`user_profile`类添加定制字段。在“Schema”下的区域中，我们添加任何我们自己需要的字段。对于我们的 SMS 功能，我们需要两个字段:

*   `subscribed`–一个布尔值，用于跟踪用户是否对我们的服务进行了付费订阅(我们将很快集成付费部分)。我们只想发送给我们的付费用户的肯定！
*   `phone`–存储每个用户电话号码的字符串。我们需要知道在哪里给他们发短信。

我们将通过键入名称、设置适当的类型来添加每一项，然后单击“add”完成该字段。一旦你添加了两个字段(确保你点击了“添加”！)，然后单击屏幕底部的“确认”保存这些更新。

![Updating our user_profile for SMSes](img/371638cacbcfeeeb9fd131d40b2b18b1.png)

## 我们的 HTML

为了使我们的 HTML 简单，我们将使用 Foundation 的框架模板。你可以从基础下载页面下载那个[。如果你不是 Foundation 的粉丝，或者更喜欢不同的框架，那完全没问题。我们只是使用框架作为一个起点和一个简单的模板的形式等。您可以在您喜欢的模板中做同样的事情(或者从头开始！)如果你愿意。这只是为了避免我们的原型看起来像是建立在 90 年代默认的 HTML 风格上。在 HTML 代码示例中，我将避开基础标记，但是请注意，这是使事情看起来比默认情况更好的原因！](http://foundation.zurb.com/sites/download.html/)

## 我们的登录/注册表单

我们有一个非常简单的 HTML 表单用于登录和注册表单，还有一个 ID 为`#message`的 div，显示一些基本的指令。下面是我们的注册表单中的基本 HTML 示例，为了简单起见，去掉了基础框架元素:

```
<h1>Register</h1>
<div id="message">To register, please enter your email and password.</div>

<form id="register">
  <label>E-mail
    <input id="email" type="email" placeholder="Your email address">
  </label>
  <label>Password
    <input id="password" type="password" placeholder="Your password">
  </label>
  <button type="submit" class="button">Sign Up!</button>
</form>
```

如果你想看基础框架版本，请随意浏览我们演示的 GitHub。

我们授权我们的`app.js`前端代码通过我们之前保存的 Syncano API 密钥访问我们的 Syncano 实例:

```
instance = new Syncano({
  instance: 'bold-rain-5584',
  apiKey: '10133b1f19bbd71a11a8055a8357ffd3b233697d'
});
```

然后，每当我们需要访问该实例时，我们就通过`instance`变量来引用它。

注册或登录用户的代码的最后一行(两个代码片段非常相似)是操作实际开始的地方。下面的注册码查找用户并通过 Syncano 登录。如果 Syncano 没有返回错误，那么它运行我们的`callback()`函数，告诉我们的前端一切正常。

在注册表单中，我们还读入了一个名为`phone`的定制字段，用于存储用户注册时的电话号码。用户注册成功后，我们的 JavaScript 通过 AJAX 执行一个基本的 POST 请求。这个请求发送给 Syncano webhook，它接受用户的 ID 和电话号码，并将电话号码保存到该帐户。

```
$('#main').on('submit', '#register', function(e) {
  var $form = $(this),
      username = $('#email').val(),
      password = $('#password').val(),
      phone = $('#phone').val(),
      data = 'username=' + username + '&password=' + password + '&phone=' + phone;

  function callback(resp) {
    $.ajax({
      type: "POST",
      url: "https://api.syncano.io/v1/instances/bold-rain-5584/webhooks/p/f5bb236b40f560a44dbc930a7bebaf87ea18e6d1/savephonenumber/",
      data: {"phone": phone, "user_id": resp.profile.id}
    })
    .done(function(msg) {
      console.log(msg);
        console.log('Phone number saved ', resp);
      userLoggedIn(resp);
    });
  }
  function error(resp) {
      console.log('Register failure... ', resp);
  }

  instance.user().add({"username": username, "password": password})
    .then(callback).catch(error);

  e.preventDefault();
});
```

## 创建 Syncano Webhooks

作为如何创建一个基本 Webhook 的例子，我们将创建上面提到的 Webhook，它将用户的电话号码保存到他们的帐户中。Syncano 中的 Webhook 是一种通过 HTTP 请求运行代码盒的方式。它可以是公共请求，也可以是需要先进行身份验证的请求。我们的 Webhooks 将通过 POST 请求公开访问。

我们的代码盒，我们称之为“保存电话号码”，接受两个参数`phone`和`user_id`。然后更新该 ID 的`user_profile`数据对象:

```
var Syncano = require('syncano');
var account = new Syncano({accountKey: 'YOURACCOUNTKEY'});

var phone = ARGS.POST.phone;
var userId = ARGS.POST.user_id;

var details = {
  "phone": phone
};

account.instance('bold-rain-5584').class('user_profile').dataobject(userId).update(details, function(err, res) {
    console.log("Phone number " + phone + "added to " + userId + "!");
});
```

我们使用帐户密钥而不是 API 密钥的原因是 API 密钥不能改变`user_profile`数据。我们也不应该在我们的公共 JavaScript 中清楚地显示帐户密钥，所以我们选择只在 Syncano 代码盒中使用它们。

要在 Syncano 仪表板中为此代码箱创建 webhook，请转到“Webhooks”菜单项。在“Webhooks”页面上，单击右下角的箭头图标打开“添加 Webhook”窗口。从这里开始，给你的 Webhook 起一个 URL 友好的名字，如果你愿意的话给它一个描述，然后选择你希望它运行的代码框。请确保您选中了“公开此 Webhook？”以便您可以通过 POST 请求访问 Webhook。

![Creating a Webhook in Syncano's Dashboard](img/29ccbfe2e2ff2b9ae46c68a9cde4920e.png)

## 集成支付

我们已经设置了用户帐户，确认列表和代码，准备通过短信发送这些确认。现在我们只需要一种方式让这些用户订阅，并支付我们这些日常肯定！为了简单起见，我们将使用在线支付提供商 Stripe。

报名的话我们去[条纹网站](https://www.stripe.com)点击“登录”:

![The Stripe homepage log in button](img/efc932646262ea8b8287e0d84be7b5ce.png)

然后点击底部的“注册”链接:

![Stripe sign up link](img/2d776ffa54e6fe7489552beb38e928cf.png)

按照他们的提示操作，直到帐户设置成功，我们自己的控制面板出现在我们眼前:

![New Stripe Dashboard](img/a0a0e43b40a5f752bc271b9df3caae9b.png)

在这里，我们需要找到并复制用于 Stripe 的 API 密钥。这可以在你的账户设置中找到。点击右上角的“您的帐户”，然后点击“帐户设置”。

![Stripe Account Settings](img/1b2c79998f3f984e773b6b36f84345e6.png)

然后单击“API 密钥”选项卡，复制“测试密钥”旁边的密钥。这是我们访问条带测试环境所需要的。把它拷贝到安全的地方，很快就可以在密码箱里使用了。

![Finding your API key](img/cc8745eb3c9615814f2f72dd28dc48fb.png)

### 订阅新客户

Syncano 目前在其“解决方案”集合中确实有一个条带解决方案，但是它仅用于单个交易。我们可以在一个预定的任务中使用它，但是这会变得混乱，因为我们需要为每个人同时运行那个任务。如果有人在我们下一次计划跑步之前加入呢？我们是不是只在任务来临时才收费？

相反，我们以一种更好的方式消除了所有这些。Stripe 已经有了基于订阅的支付服务。Syncano 可以为用户订阅该服务，然后 Stripe 会为我们处理每月付款事宜。我们在 Syncano 的系统需要管理的只是它们是否被订阅。容易多了！

### 条带订阅

为此，我们需要首先在 Stripe 中定义订阅类型。我们将返回到我们的 Stripe 控制面板，单击左侧“Subscriptions”下的“Plans”菜单项。从这里，点击“创建您的第一个计划”:

![Adding a new subscription plan in Stripe](img/98f0c652074e54ab8bfac268ccad531c.png)

在出现的屏幕上，我们将输入以下内容:

*   **ID**–affirmationsubscription(我们将在代码中使用该 ID 来引用我们的订阅)
*   **Name**–Affirmations(这仅在 Stripe 的仪表板中使用)
*   **货币**–我留的是澳元，但你可能想要用你当地的货币！
*   **金额**–1 美元(如果您觉得您的确认更有价值，请随意收取更多费用)
*   **间隔**–每月(如果您愿意，可以更改计费频率)
*   **试用期天数**–我们不提供免费试用期，但如果你愿意，可以在这里免费试用。
*   **声明 desc**–“生活是美好的”(这是出现在用户银行对账单上的内容)

![Stripe Plan Settings](img/c21a4a0910b58e813dd7a3a8b65677f4.png)

然后我们点击“创建计划”。这样，我们在条带侧的设置就完成了。

### 我们的订阅代码箱

为了在 Syncano 端设置我们的订阅管理，我们将创建一个名为“Stripe Subscribe New Customer”的新代码箱。我们的代码盒看起来会像这样:

```
var Syncano = require('syncano');
var account = new Syncano({accountKey: 'YOURACCOUNTKEY'});
var _ = require('lodash');

var stripe = require("stripe")("sk_test_YOURSTRIPEKEY");
var stripeToken = ARGS.POST.stripe_token;
var email = ARGS.POST.email;

stripe.customers.create({
  source: stripeToken,
  plan: "affirmationsubscription",
  email: email
}, function(err, customer) {
  account.instance('bold-rain-5584').user().list()
    .then(function(res){
        console.log(res);

        _.each(res.objects, function(user) {
            if (user.username == email) {
                console.log("USER:");
                console.log(user);

                var details = {
                  "subscribed": true,
                  "stripe_id": customer.id,
                  "subscription_id": customer.subscriptions.data[0].id
                };

                account.instance('bold-rain-5584').class('user_profile').dataobject(user.profile.id).update(details, function(err, res) {
                    console.log(customer.id + ',' + customer.subscriptions.data[0].id);
                });
            }
        });
    })
    .catch(function(err) {
        console.log("Error! ", err);
    });
});
```

我们将浏览每个部分，解释每个部分的作用:

开始与我们之前的代码箱非常相似，使用一个帐户密钥并要求`lodash`:

```
var Syncano = require('syncano');
var account = new Syncano({accountKey: 'YOURACCOUNTKEY'});
var _ = require('lodash');
```

然后，我们需要 Stripe 的 npm 模块，`stripe`,并向其传递我们之前从 Stripe 复制的密钥:

```
var stripe = require("stripe")("sk_test_YOURTESTKEY");
```

我们为代码箱设置了两个参数，一个 Stripe 令牌和用户的电子邮件。Stripe token 是 Stripe 在用户将他们的支付信息输入 Stripe 后提供给我们的令牌。这将发生在 JavaScript 的前端。然后，我们获取这些详细信息，并使用这些信息通过`stripe.customers.create()`订阅我们的“确认订阅”计划:

```
var stripeToken = ARGS.POST.stripe_token;
var email = ARGS.POST.email;

stripe.customers.create({
  source: stripeToken,
  plan: "affirmationsubscription",
  email: email
}, function(err, customer) {
  // Our callback function will be here!
}
```

在我们的回调函数中，我们列出了我们的用户，并用数据库中相同的电子邮件更新了用户的字段。我们保存他们的 Stripe ID (Stripe 中的每个用户都有一个唯一的 ID，我们将来可以使用它来管理他们)和他们的订阅 ID(Stripe 还为每个订阅提供了自己的 ID，因此我们也可以管理它)。我们还为他们设置了`subscribed`到`true`，这样我们就知道他们应该会收到确认短信。我发现在回调中包含一个`console.log`非常有用。这将在测试时记录两个条带 id，以确保正确的 id 通过。

```
account.instance('bold-rain-5584').user().list()
    .then(function(res){
        console.log(res);

        _.each(res.objects, function(user) {
            if (user.username == email) {
                console.log("USER:");
                console.log(user);

                var details = {
                  "subscribed": true,
                  "stripe_id": customer.id,
                  "subscription_id": customer.subscriptions.data[0].id
                };

                account.instance('bold-rain-5584').class('user_profile').dataobject(user.profile.id).update(details, function(err, res) {
                    console.log(customer.id + ',' + customer.subscriptions.data[0].id);
                });
            }
        });
    })
```

最后，我们捕获并记录过程中发生的任何错误:

```
.catch(function(err) {
        console.log("Error! ", err);
    });
});
```

要访问这个代码盒，在 Syncano 中创建一个名为`subscribecustomer`的 Webhook 来运行它(创建方法与我们在上面创建的 Webhook 相同)。我们很快就会用到它。

### 触发订阅

Stripe 知道我们的订阅服务，Syncano 知道如何通过 Stripe 为我们订阅。剩下的一块拼图是一个按钮，用户单击它就可以开始整个订阅过程。

我们通过 Stripe 的结账服务开始这个过程。我们将创建一个自定义条纹结帐按钮。这个按钮实际上并不进行订阅。它可以用于支付，但是我们最初将只使用它来获取用户的信用卡信息和电子邮件。它需要这些，然后才会让我们为它们订阅我们的计划。

为了访问 Stripe 的 API，我们在我们的`index.html`文件底部包含了他们的 JavaScript:

```
<script src="https://checkout.stripe.com/checkout.js"></script>
```

在我们的`app.js`中，我们使用`StripeCheckout.configure()`功能准备订阅:

```
var handler = StripeCheckout.configure({
  key: 'pk_test_YOURSTRIPEKEY',
  locale: 'auto',
  panelLabel: 'Subscribe',
  email: username,
  token: function(token) {
    stripeToken = token;

    console.log("Stripe token is " + JSON.stringify(token));

    $.ajax({
      type: "POST",
      url: "https://api.syncano.io/v1/instances/bold-rain-5584/webhooks/p/1254a339e4544e4c36ae4c5fcf67f4249413b3f2/subscribecustomer/",
      data: {"stripe_token": stripeToken.id, "email": username}
    })
    .done(function(msg) {
      paid = true;
      ids = msg.result.stdout.split(',');
      stripeId = ids[0];
      subscriptionId = ids[1];

      goToPage('myaccount');
    });
  }
});
```

这个函数的开始包含我们之前使用的 Stripe 键，`locale`用用户的本地语言设置弹出窗口(一种很好的方式)，`panelLabel`改变他们的信用卡详细信息表单下面的支付按钮将显示的内容(我们希望它显示“Subscribe”)，我们通过`email`传递我们已经从他们的 Syncano 帐户中知道的电子邮件地址，以便它被预填充:

```
var handler = StripeCheckout.configure({
  key: 'pk_test_YOURSTRIPEKEY',
  locale: 'auto',
  panelLabel: 'Subscribe',
  email: username,
```

当 Stripe 拥有用户的详细信息时，`token`函数运行，我们将令牌和电子邮件发送到名为`subscribecustomer`的 Syncano Webhook。这将它们传递到我们的代码盒中，供我们在实际的订阅过程中使用。

```
token: function(token) {
    stripeToken = token;

    $.ajax({
      type: "POST",
      url: "https://api.syncano.io/v1/instances/bold-rain-5584/webhooks/p/1254a339e4544e4c36ae4c5fcf67f4249413b3f2/subscribecustomer/",
      data: {"stripe_token": stripeToken.id, "email": username}
    })
```

当 POST 请求完成时，我们设置本地 JavaScript 变量，这样我们就知道当前用户已经支付了费用，并且知道他们的 Syncano 用户 id、条带 ID 和订阅 ID。然后，我们运行一个功能，将用户带到应用程序的“我的帐户”页面，该页面将检查详细信息，并告诉他们他们已经订阅。记住，使用你选择的框架，你可以(也应该)做得更好。

```
.done(function(msg) {
      paid = true;
      ids = msg.result.stdout.split(',');
      stripeId = ids[0];
      subscriptionId = ids[1];

      goToPage('myaccount');
    });
  }
});
```

为了真正启动条带结帐处理程序，我们有一个简单的`handler.open()`函数，它触发一个条带弹出窗口来请求支付细节:

```
$('#main').on('click', '#paymentButton', function(e) {
  handler.open({
    name: "Life is Good Affirmations",
    description: "A monthly subscription to daily affirmations!",
    currency: "aud"
  });
  e.preventDefault();
});
```

如果他们单击弹出窗口中导致 Stripe 触发`popstate`事件的部分，弹出窗口将关闭:

```
$(window).on('popstate', function() {
  handler.close();
});
```

## 退订客户

为了让客户退订，我们简化了订阅流程。我们最初不需要 Stripe 来获取客户的详细信息或类似信息，因为我们已经获得了他们的 Stripe ID 和 Stripe 订阅 ID。相反，我们需要的是一个代码盒，它可以获取这些细节，并告诉 Stripe 取消该用户的订阅。我们将创建一个名为“条纹退订客户”的代码箱。

在我们的“Stripe Subscribe Customer”代码框中，我们有与“Stripe Subscribe Customer”代码框相同种类的`require`语句。我们接受当前用户的 Syncano ID 以及他们的 Stripe IDs，这样我们也可以在我们这边更改他们的列表，将`subscribed`设置为`false`，并从我们的数据库中删除他们的订阅 ID。

这个代码框中唯一可能与我们已经做的有所不同的是`stripe.customers.cancelSubscription()`，它是我们对 Stripe 取消该用户订阅的调用。我们传入条带 ID 和条带订阅 ID，然后在成功回调中，我们对自己的数据库进行更改。

```
var Syncano = require('syncano');
var account = new Syncano({accountKey: 'YOURACCOUNTKEY'});
var _ = require('lodash');

var stripe = require("stripe")("sk_test_YOURSTRIPEKEY");

var userId = ARGS.POST.user_id;
var stripeId = ARGS.POST.stripe_id;
var subscriptionId = ARGS.POST.subscription_id;

stripe.customers.cancelSubscription(
  stripeId,
  subscriptionId,
  function(err, confirmation) {
    var details = {
      "subscribed": false,
      "subscription_id": ""
    };

    account.instance('bold-rain-5584').class('user_profile').dataobject(userId).update(details, function(err, res) {
        console.log("User set to unsubscribed");
    });
  }
);
```

创建一个名为“取消订阅客户”的 Webhook，通过与我们的“订阅客户”Webhook 相同的方法触发它，您的订阅设置就完成了！

## 计划任务

既然我们可以接受客户，我们需要做的最后一件事就是我们预定的确认。我们已经准备好了名为“预定确认”的密码箱，我们只需要设置一个计时器。

转到“任务”页面，点击右下角有一个看起来有点像计时器的圆形按钮。在出现的弹出窗口中，为您的计划选择一个名称，选择“计划确认”作为我们要计划的代码框，并选择您希望它运行的频率(它出现在目前，每天午夜一次是我们唯一的选择-还没有一种方法来设置时区，所以这不会是每个人的午夜！).点击“确认”,您的计划将自动运行！

![Creating a Syncano schedule](img/aed2a44cff94f0e6c1d404af25b01fae.png)

## 一些旁注

在我们的演示前端 JavaScript 中，我们通过一个相当粗糙的`goToPage()`函数来管理页面，就像你偶尔在上面看到的那样。我们将避免详细解释这一切是如何工作的，因为您应该实现您最喜欢的、合适的框架来代替它！

在观看实际演示之前，最后一个重要注意事项是:

在实时网站上，你会希望通过 HTTPS 运行这个应用程序，因为你正在收集非常敏感的信息！

## 在活动

现在来尝试一下吧！您可以在任何简单的 web 服务器上运行前端 HTML。在我的个人网络服务器上，我通过`http://localhost/lab/syncano/public/`运行它。为了运行我们相当粗糙的 JavaScript，避免在 URL 中包含`index.html`。

![Our app homepage](img/9ffe17b45ff2f8510750ab3fd48deed7.png)

我们点击注册，并输入我们的详细信息(确保您输入了自己的有效电话号码，否则这个测试不会太令人兴奋！):

![Registration page](img/bae5ef9ac900530daf55af7a59855a1e.png)

不出所料，系统会提示我们注册订阅该服务:

![Subscription request page](img/f76ee20e92a04eb159f7ede3da6d59fb.png)

当我们点击“Subscribe”时，Stripe Checkout 弹出窗口出现，要求支付详细信息。Stripe 的一个测试信用卡号是`4242 4242 4242 4242`，这是我下面用过的。你可以在这里找到更多条纹测试号码的[列表。](https://stripe.com/docs/testing#how-do-i-test-sending-transfers)

![Subscription pop up](img/1aff5e7c1612c25a5ab43a5c385226af.png)

当订阅成功时，页面将显示这一点并提供一个取消订阅选项(以及一个简单的注销选项，该选项只是清除 JavaScript 中存储的用户 ID 并返回到主页):

![Subscription successful](img/f92018560f28a1e61a244c94bea8e6fb.png)

如果我们转到我们的 Stripe 控制面板，单击“计划”,然后单击“确认计划”,我们可以看到有关该计划的详细信息。特别是，我们可以看到我们的新订户！

![Subscriber visible in Stripe](img/30afe138e875a3143c37d52b3f2b9c24.png)

我们可以打开“预定确认书”代码框，点击右侧的“播放”按钮，立即运行确认书，而不是等待确认书的预定运行:

![Running our CodeBox](img/d5ce048d8f6e09f410b2be5441d685d6.png)

在很短的时间内，你应该会收到一条来自你的应用程序的短信:

![A successful text message affirmation](img/9ddc158a850b84ab2fbc6fdfe54a0066.png)

如果我们决定取消订阅，我们单击取消订阅并返回到上一个屏幕:

![Subscription request page](img/f76ee20e92a04eb159f7ede3da6d59fb.png)

回头看看 Stripe，如果我们刷新页面，我们应该会发现该订阅不再有任何订户:

![Subscriber removed](img/f30d4f45a4a255b6fbea869aa1f8817b.png)

我们的应用程序运行良好！

## 查看日志

如果由于任何原因你的应用没有很好地工作，你想看日志，你可以通过打开代码盒并点击“跟踪”标签从代码盒中找到日志。然后单击您想要查看日志的记录:

![Viewing trace logs](img/813359d9302b90c24fc1c56d26a8d635.png)

对于通过 Webhooks 运行的代码盒，您可以转到 Webhooks 页面，点击您想要查看日志的 web hooks，找到它们的日志:

![Viewing Webhook logs](img/14c0d9d6691115b94691bdbbb3057d5d.png)

## 结论

Syncano 是一个平台，它可以让整合应用程序的任务变得更快、更容易管理。它的代码盒和解决方案的概念也鼓励将任务分成更小的块，这样可以保持整洁和可重用。如果你已经有了一个正在考虑开发的原型应用，为什么不试着把它扔进 Syncano 试试呢？

## 分享这篇文章