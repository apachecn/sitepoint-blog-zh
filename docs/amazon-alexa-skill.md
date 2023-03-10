# 如何建立你的第一个亚马逊 Alexa 技能

> 原文：<https://www.sitepoint.com/amazon-alexa-skill/>

开箱即用，Alexa 支持许多内置的技能，例如向您的购物清单添加商品或请求一首歌曲。然而，开发者可以通过使用 Alexa 技能工具包(ASK)建立新的自定义技能。

ASK 是 API 和工具的集合，处理与语音接口相关的繁重工作，包括语音识别、文本到语音编码和自然语言处理。ASK 帮助开发人员快速轻松地建立技能。

简而言之，Alexa 能够理解用户语音命令的唯一原因是它有定义的技能。每一项 Alexa 技能都是一个旨在理解语音命令的软件。此外，每个 Alexa 技能都有自己的逻辑定义，为语音命令创建适当的响应。为了让您了解一些现有的 Alexa 技能，它们包括:

*   在达美乐披萨店点披萨
*   呼吁优步
*   告诉你你的星座

如前所述，我们可以使用 Alexa Skill Kit 开发我们自己的定制技能，这是一个为此目的设计的 API 和工具的集合。ASK 包括语音识别、文本到语音编码和自然语言处理等工具。该工具包可以让任何开发人员快速开始开发他们自己的定制技能。

在这篇文章中，你将学习如何创建一个基本的“获取事实”的 Alexa 技能。简而言之，我们可以要求 Alexa 为我们呈现一个随机的猫事实。完成我们任务的完整代码可以在 [GitHub](https://github.com/michielmulders/alexa-cat-fact-skill) 上找到。在我们开始之前，让我们确保理解 Alexa 技能术语。

## 掌握 Alexa 技能术语

首先，让我们学习用户如何与自定义技能进行交互。这对理解与技能相关的不同概念很重要。

为了激活一个特定的技能，用户必须调用 Alexa 并要求打开一个技能。比如:“Alexa，开猫事实”。通过这样做，我们调用技能的**调用名**。基本上，调用名可以看作是应用程序的名称。

现在我们已经开始了正确的技能，我们可以使用技能理解的声音意图/命令。因为我们想让事情变得简单，所以我们定义了一个“获取 Cat 事实”意图。然而，我们需要提供例句来触发意图。一个意图可以由许多例句触发，也称为**话语**。例如，用户可能会说“给出一个事实”。因此，我们定义以下例句:

*   “说出一个事实”
*   “给猫一个事实”
*   “举一个事实”

甚至可以将调用名称与这样的意图结合起来:“Alexa，请 Cat Fact 给出一个事实”。

现在我们知道了调用名称和意图之间的区别，让我们继续创建你的第一个 Alexa 技能。

## 创建亚马逊开发者账户

首先，我们需要一个亚马逊开发者账户。如果有，可以跳过这一节。

注册一个亚马逊开发者账户需要三个步骤。亚马逊需要一些个人信息，接受服务条款，并提供支付方式。注册亚马逊开发者账户的好处是，你可以获得大量其他亚马逊服务。注册成功完成后，您将看到 Amazon 开发者仪表盘。

登录仪表板，点击右上角的*开发者控制台*按钮。

![Open Developer Console](img/ce01306adc761d35bec25067d05cc3b1.png)

接下来，我们要打开 Alexa 技能包。

![Open Alexa Skills Kit](img/6ff6c949b377a4a2c71d00d519dfe135.png)

如果您无法打开 Alexa 技能工具包，请使用[此链接](https://developer.amazon.com/alexa/console/ask)。

在接下来的部分中，我们将创建我们的实际技能。

## 创建我们的第一个自定义 Alexa 技能

好了，我们准备创建我们的第一个自定义 Alexa 技能。点击蓝色按钮*创建技能*打开创建新技能的菜单。

![Create Skill Button](img/48b3754315798740073f1d61878d8210.png)

首先，它会提示我们技能的名称。正如你已经知道的，我们想要随机的猫事实，因此将该技能称为*自定义猫事实*(我们不能使用*猫事实*，因为那是 Alexa 设备的内置技能)。接下来，它会提示我们为您的技能选择一个模型。我们可以在一些预定义的模型之间进行选择，也可以选择一个给我们带来充分灵活性的定制模型。由于我们不想处理我们不需要的代码，我们选择了*定制*选项。

注意:如果你选择了一个预定义的技能，你会得到一个交互模型和例句(话语)的列表。然而，即使是自定义技能也配备了最基本的意图，如**取消**、**帮助**、**导航首页**、**停止**。

 *![Pick Skill name](img/e7eb27a0c324366596b88180f9f9821a.png)

接下来，我们需要选择一种方法来展示我们的技能。同样，我们不想让事情过于复杂，选择了 *Alexa-Hosted (Node.js)* 选项。这意味着我们不必自己运行后端，这需要一些努力来使它“兼容 Alexa”。这意味着你必须根据亚马逊 Alexa 标准格式化响应，以便设备理解这一点。Alexa 托管选项将:

> 您帐户中的主机技能达到 AWS 自由层限制，并让您开始使用 Node.js 模板。您将获得对 AWS Lambda 端点、每月 15 GB 数据传输的 5 GB 媒体存储和会话持久性表的访问权。

![Pick host method](img/342d56cf473f8a152cd9430dbccccd0a.png)

好了，现在所有的设置都已经就绪，你可以点击屏幕右上角的*创建技能*按钮了。这个按钮将在我们的亚马逊开发者帐户中产生实际技能。

## 修改你的第一个 Alexa 技能

现在如果你导航到 [Alexa 开发者控制台](https://developer.amazon.com/alexa/console/ask)，你会发现你的技能列在那里。点击*编辑*按钮开始修改技能。

![Modify Alexa Skill](img/a5dbd07931cd9b24a322a813b8b17a3d.png)

接下来，亚马逊将显示**猫事实**技能的*构建*标签。在左侧，您会发现为该技能定义的意图列表。如前所述，默认情况下，Alexa 技能工具包会生成一个*取消*、*停止*、*帮助*和*导航主页*意图。前三个对于想要放弃技能或者不知道如何使用它的用户是有帮助的。最后一个， *NavigateHome* ，只用于涉及多个步骤的复杂技能。

![Interaction model](img/3342722a7830fa0f89e8dbba06d10c25.png)

### 步骤 1:验证调用名

首先，让我们验证技能的调用名是否正确。名称应该是“定制猫事实”。

如果您更改名称，请确保点击页面顶部的*保存模型*按钮。

![Invocation name](img/31138d5be132a937086ddba24c3759f7.png)

### 第二步:改变“HelloWorldIntent”

第二步，我们想要修改默认意图 *HelloWorldIntent* 。

通过点击意图，我们得到一个漂亮的图形界面，允许我们改变意图名称或添加示例话语。

![HelloWorldIntent overview](img/d6a16e0cfaf4a10d72642daefe2a0259.png)

就我个人而言，我更喜欢使用 JSON 编辑器，你可以在左边的菜单中找到它。当打开 JSON 编辑器时，您会看到下面的 JSON 对象。

![JSON editor for intents](img/901d3a3583c734913933cd6923265360.png)

JSON 编辑器的好处是您可以一次修改所有意图。我们先添加一个名为“GetFactIntent”的新意图。

接下来，我们想添加一些示例语句，如下图所示。

![Edit intents in JSON editor](img/6e4c39bd1db77d66ce7d3e5f9537289f.png)

修改之后，JSON 对象应该是这样的。现在，我们不想为其他意图定义示例话语。默认情况下，Alexa 技能工具包会将一个“帮助”句子链接到“帮助意图”。

*注意:不要忘记点击**保存模型**按钮，然后点击**建立模型**按钮。Alexa 将基本上建立模型，并根据您使用样本话语定义的模型来训练自己。*

![Save and build model](img/bb5905778a56b53529b55457a752f942.png)

### 步骤 3:测试“定制猫事实”技能

让我们转到水平菜单中的*测试*选项卡。现在，将“技能测试已启用”下拉菜单切换到*开发*。这将允许我们测试技能。

现在，让我们试着激活技能，看看事情是否正确。我们可以通过调用调用名来激活技能，如下图所示。

![Test Invocation Skill: say hello or help](img/0ea35703174db21f30d77a9df3b5c1b6.png)

### 步骤 4:修改 GetFactIntent 的代码

接下来，我们将深入代码选项卡。当你点击*代码*标签时，你会得到一个带有默认代码的在线编辑器。

首先，我们想要复制`HelloWorldIntentHandler`的代码，并将其重命名为`GetFactIntentHandler`。

之后，我们必须指定这个处理程序可以处理哪种类型的意图。第 21 行寻找一个名为`GetFactIntent`的意图。

现在我们已经找到了意图，让我们定义一个静态响应:“我将讲述一个事实！”我们将其赋给变量`speakOutput`。

![Add GetFact Intent](img/7a44b46408636c0431e0e44049e7cadd.png)

最后，我们必须将`GetFactIntentHandler`添加到其他请求处理程序中。我们可以简单地通过将它添加到`addRequestHandlers`函数中来做到这一点。*不要忘记保存和构建意图！*

![Add GetFact handler](img/4a9a31ad2936b4395ac6e8eaf861c680.png)

现在技能已经保存并建立，让我们简单验证一下反应。如果我们问技能告诉一个事实，它应该回答“我会告诉一个事实！”

![Verify skill](img/819fa86722bcb74c8be0c341fbe823c3.png)

如果一切正常，就可以进入下一步了。

### 步骤 5:查询 API 以检索卡特彼勒事实

最后一步，我们想要查询一个简单的 API 来返回 cat 事实。为此，我使用下面的 API 端点:`https://catfact.ninja/fact`。

这个 API 端点返回一个事实和事实的长度。

```
{
  "fact": "Smuggling a cat out of ancient Egypt was punishable by death. Phoenician traders eventually succeeded in smuggling felines, which they sold to rich people in Athens and other important cities.",
  "length": 192
} 
```

因为`https`包是为 Node.js 内置的，所以我们可以在代码中使用它从 API 中检索数据。我们是这样做的:

![HTTPS Get](img/e70e4520585e7325260dedf6b954d631.png)

实际代码可以在这里找到:

```
const https = require('https');

const httpGet = () => {
  return new Promise(((resolve, reject) => {
    var options = {
        host: 'catfact.ninja',
        port: 443,
        path: '/fact',
        method: 'GET',
    };

    const request = https.request(options, (response) => {
      response.setEncoding('utf8');
      let returnData = '';

      response.on('data', (chunk) => {
        returnData += chunk;
      });

      response.on('end', () => {
        resolve(JSON.parse(returnData));
      });

      response.on('error', (error) => {
        reject(error);
      });
    });
    request.end();
  }));
} 
```

现在我们已经准备好了 helper 方法，我们可以将它添加到`GetFactIntentHandler`中。注意，因为我们想使用`await`来请求，所以我们必须使用`handle`函数`async`。除此之外，我们调用`httpGet`函数来获得 cat 事实响应，并将`response.fact`赋给`speakOutput`变量。接下来，`speakOutput`变量被传递给 speak 命令，由 Alexa 使用:

```
const GetFactIntentHandler = {
    canHandle(handlerInput) {
        return Alexa.getRequestType(handlerInput.requestEnvelope) === 'IntentRequest'
            && Alexa.getIntentName(handlerInput.requestEnvelope) === 'GetFactIntent';
    },
    async handle(handlerInput) {
        // const speakOutput = 'I will tell a fact!';
        const response = await httpGet();
        const speakOutput = response.fact;
        return handlerInput.responseBuilder
            .speak(speakOutput)
            //.reprompt('add a reprompt if you want to keep the session open for the user to respond')
            .getResponse();
    }
}; 
```

![Add HTTP call to handler](img/0bb0297b1b9ffa890b233767eded958c.png)

我们已经准备好修改代码了。*再次保存代码，然后点击部署按钮。*

### 第六步:最终技能测试

作为最后一步，让我们验证技能是否从 API 获得随机的猫事实。同样，让我们转到*测试*选项卡。

通过调用*开猫事实*开始技能。接下来，你可以要求*讲一个事实*。如果一切顺利，你应该会得到一个事实作为回应。

![Get Cat Fact Response](img/32917745ca9e5ea04c9d68344b572a6e.png)

成功！

### 步骤 7(可选):在真实的 Alexa 设备上测试

为了在支持 Alexa 的设备上测试你的自定义技能，你需要在你的亚马逊开发者帐户的同一电子邮件地址下注册 Alexa 设备。

更多信息可在 [Alexa 文档](https://developer.amazon.com/docs/devconsole/test-your-skill.html#h2_register)中找到:

> 要使用支持 Alexa 的设备(如 Amazon Echo)进行测试，请使用您在 Amazon Developer Portal 上注册开发人员帐户时使用的相同电子邮件地址注册该设备。然后你可以用唤醒词和你的调用名来调用你的技能:“Alexa，打开<invocation name="">”</invocation>
> 
> 如果您的设备是使用开发人员帐户之外的帐户设置的，请重置设备，然后使用开发人员帐户重新注册。

## 了解基础知识

这只是用 Alexa 建立技能的开始。更高级的例子是可能的，比如传递带有意图的参数(选项)。此外，许多其他类型的技能也可以培养，如控制智能家居设备的技能。

如果您想了解更多关于 Alexa 技能发展的信息，请查找以下资源:

*   建立端到端的技能— [亚马逊 Alexa 文档](https://developer.amazon.com/docs/hosted-skills/build-a-skill-end-to-end-using-an-alexa-hosted-skill.html)
*   [Alexa 的技能样本 Node.js 项目](https://github.com/alexa/skill-sample-nodejs-fact)
*   [语音设计指南](https://developer.amazon.com/designing-for-voice/)

## 分享这篇文章*