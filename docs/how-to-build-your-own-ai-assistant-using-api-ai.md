# 如何使用 Api.ai 构建自己的 AI 助手

> 原文：<https://www.sitepoint.com/how-to-build-your-own-ai-assistant-using-api-ai/>

如果你渴望了解更多关于人工智能的知识，请查看我们的截屏视频[微软认知服务和文本分析 API](https://www.sitepoint.com/premium/screencasts/microsoft-cognitive-services-and-the-text-analytics-api) ，了解你的机器人中的人工智能情感。

![How to Build Your Own AI Assistant Using Api.ai](img/c2409aaf2e970f0b81d53efc697a3681.png)

人工智能助手的世界正在增长——Siri、Cortana、Alexa、Ok Google、脸书 M、bix by——所有技术领域的大玩家都有自己的助手。然而，许多开发人员没有意识到，构建自己的人工智能助手也是非常容易的！您可以根据自己的需求、自己的物联网连接设备、自己的自定义 API 对其进行定制。天空是无限的。

*注意:本文于 2017 年更新，以反映 Api.ai 的最新变化*

早些时候，我在 2016 年整理了一份关于[构建人工智能的五种简单方法的指南，其中我涵盖了构建人工智能助手的一些简单选项。在这篇文章中，我想看看一个特定的服务，它使得通过很少的初始设置就可以非常简单地获得一个功能齐全的人工智能助手——](https://www.sitepoint.com/five-simple-ways-to-build-artificial-intelligence-in-2016/)[API . AI](https://api.ai/)。

## 用 Api.ai 构建 AI 助手

本文是系列文章之一，旨在帮助您使用 Api.ai 运行简单的个人助理:

1.  如何使用 Api.ai 构建自己的 AI 助手(这个！)
2.  [使用意图和上下文定制您的 Api.ai 助手](https://www.sitepoint.com/customizing-your-api-ai-assistant-with-intent-and-context/)
3.  [用实体赋予你的 Api.ai 助手](https://www.sitepoint.com/teaching-our-api-ai-assistant-concepts-via-entities/)
4.  [如何将您的 Api.ai 助手连接到物联网](https://www.sitepoint.com/how-to-connect-your-api-ai-assistant-to-the-iot/)

## 什么是 Api.ai？

Api.ai 是一种允许开发人员建立语音到文本、自然语言处理、人工智能系统的服务，你可以用自己的定制功能训练这些系统。他们有一系列现有的知识库，用 Api.ai 构建的系统可以自动理解这些知识库，称为“域”——这是我们将在本文中重点关注的。领域提供了一个百科知识、语言翻译、天气等等的完整知识库。在未来的文章中，我将介绍 Api.ai 的一些更高级的方面，这些方面允许您进一步个性化您的助手。

## Api.ai 入门

首先，我们将前往 Api.ai 网站并点击右上角的“免费开始”按钮或“免费注册”按钮。

然后我们会看到一个非常简单的注册表格:输入你的名字、邮箱和密码，然后点击“注册”。对于那些不想使用另一套登录凭证的人，你也可以使用右边的按钮注册 GitHub 或 Google 帐户。

随着 Api.ai 被谷歌收购，它已经转向只使用谷歌账户登录。因此，如果你是 Api.ai 的新手，你需要用你的谷歌账户登录:

![Signing in with your Google account](img/6ee2ab2d8fa66faff526dd20e75c0436.png)

在接下来的屏幕中，单击**允许**授予 Api.ai 访问您的 Google 帐户的权限:

![Granting access to your Google account](img/e3dde8de9431afdb4094a51bd6ffa667.png)

您还需要通读并同意他们的服务条款:

![Agreeing to the TOC](img/ed1300f45d9525c16094303eb1d2cde7.png)

一旦你注册了，你将被直接带到 Api.ai 界面，在那里你可以创建你的虚拟 ai 助手。您创建并教授特定技能的每个助手在 Api.ai 中称为“代理”。因此，首先，您可以通过单击左上角的“创建代理”按钮来创建您的第一个代理:

![Creating new agent in Api.ai](img/7ecd5a0a08eef7367176787d68e41003.png)

您可能需要再次授权 Api.ai，以获得您的 Google 帐户的额外权限。这很正常，好吧！单击“授权”继续:

![Authorizing Google](img/0a9725a055d8ae9f2a787db282bff756.png)

并允许:

![Allow authorization](img/b909b736548024891a9f0d97c16a8ce3.png)

在下一个屏幕上，输入您的代理的详细信息，包括:

*   **名称:**这只是供自己在 api.ai 接口区分代理时参考。你可以给代理起任何你喜欢的名字——一个人的名字(我选择了 Barry)或者一个代表他们正在帮助完成的任务的名字(例如灯光控制器)。
*   **描述:**人类可读的描述，这样您就可以记住代理的职责。这是可选的，如果您的代理名称不言自明，则可能不需要。
*   **语言:**代理工作时使用的语言。*一旦选择了就无法改变*——所以要明智地选择！对于本教程，选择英语，因为英语可以访问大多数 Api.ai 域。您可以在 Api.ai 文档的[语言表中查看每种语言的可用域名。](https://docs.api.ai/docs/domains#languages)
*   **时区:**正如您所料，这是您的代理的时区。很有可能它已经检测到了你的当前时区。

它还会自动为你的代理建立一个 Google 云平台项目，这方面你不需要做任何事情；都是自动化的！不过，很高兴知道这正在发生，所以如果你经历了许多测试并创建了许多代理，只要知道有许多正在创建的 Google 云平台项目，你可能希望有一天清理它们。

输入代理设置后，选择代理名称旁边的“保存”保存所有内容:

![Settings for your new agent in Api.ai](img/a645019fdf9228d4f3f437837990588f.png)

## 测试控制台

一旦创建了代理，您就可以使用右侧的测试控制台对其进行测试。您可以在顶部输入查询，它会将这些发送给您的代理，向您显示它听到这些语句后会返回什么。输入一个问题，如“你好吗？”看看它会返回什么。您的结果应该出现在它的下面:

![The Api.ai test console](img/6139c4f6bc2663359eb9e3d36046fce5.png)

如果你在结果的右边向下滚动，你会看到 Api.ai 如何解释你的请求的更多细节(如上面的截图所示)。在那下面，有一个按钮叫做“显示 JSON”。单击该按钮，查看 API 如何在您的应用程序中向您返回此类响应。

Api.ai 将打开 JSON 查看器，并向您显示一个类似如下的 JSON 响应:

```
{
  "id": "21345678",
  "timestamp": "2017-05-12T08:04:49.031Z",
  "lang": "en",
  "result": {
    "source": "agent",
    "resolvedQuery": "How are you?",
    "action": "input.unknown",
    "actionIncomplete": false,
    "parameters": {},
    "contexts": [],
    "metadata": {
      "intentId": "6320071",
      "webhookUsed": "false",
      "webhookForSlotFillingUsed": "false",
      "intentName": "Default Fallback Intent"
    },
    "fulfillment": {
      "speech": "Sorry, can you say that again?",
      "messages": [
        {
          "type": 0,
          "speech": "Sorry, could you say that again?"
        }
      ]
    },
    "score": 1
  },
  "status": {
    "code": 200,
    "errorType": "success"
  },
  "sessionId": "243c"
}
```

正如你将看到的……**你的经纪人不知道如何回应！**现在，它还不完全是“智能的”人工智能:它仍然需要加入智能。`action`字段中的`input.unknown`值告诉您不确定如何继续。上面，它在返回一条“对不起，你能再说一遍吗？”，这是它的默认回退之一。它不是告诉人类它不明白，而是让他们重复一遍……一遍又一遍。这并不理想，我更愿意把它改成在机器人不理解的时候能说清楚的东西。*如果你对这类事情也很挑剔，并且想改变这里所说的，你可以在“意图”页面上找到，点击那里的“默认回退意图”项。*

对于那些不久前已经使用过 Api.ai(或者已经看过它的运行)的人来说，有一点需要注意:你可能实际上一直期望它有更多现成的功能。以前，它能够回答诸如“谁是史蒂夫·乔布斯？”默认情况下。现在已经不是这样了！您必须添加自己与第三方 API 的集成，以采取行动和获取信息。Api.ai 提供了句子解析和解释功能。

## 添加闲聊

你可以添加一点默认功能，给你的机器人一点智能的暗示——“闲聊”功能。这提供了一系列常见问题的答案…包括“你好吗？”从上面。不过，默认情况下这并不开启。要打开它，请转到左侧的“聊天”菜单项，然后单击“启用”:

![Small talk enabling](img/c36b3a23ffc0d9a62fb473abb01b954c.png)

启用后，如果您向下滚动，您可以看到一系列常见闲聊短语的类别。找到“Hello/Goodbye”部分，单击它将其展开。对“你好吗？”这个问题添加一些不同的回答然后点击右上角的“保存”。添加短语后，您会看到“你好/再见”部分旁边的百分比数字增加，以显示您对聊天机器人的定制程度。

![Customizing your chatbot's small talk](img/559b35edd202c36c41c3f3165ec21d74.png)

如果你去测试控制台问它“你好吗？”同样，它现在应该用您输入的一个响应来回答！

![A successful small talk response](img/e0b9d43a888529e55450bf3f85bb6dad.png)

如果它没有正确响应，请在尝试之前检查您是否真的点击了“保存”!它不会自动保存。

理想情况下，你会希望尽可能多地定制你的闲聊回应:这将赋予你的 Api.ai 机器人更独特的个性。你可以选择它回应的语气和结构。是讨厌人类跟它说话的暴躁聊天机器人吗？是痴迷猫的聊天机器人吗？或者是一个可以在青少年网络/文本对话中做出反应的聊天机器人？你来决定！

现在，您至少有了一些闲聊的元素，您的代理现在可以将它们集成到您自己的 web 应用程序界面中了。要做到这一点，您需要获得您的 API 密匙来远程访问您的代理。

## 查找您的 Api.ai API 密钥

您需要的 API 密钥在代理的设置页面上。要找到答案，请单击您的代理姓名旁边的 cog 图标。在出现的页面上，复制并粘贴“客户端访问令牌”到安全的地方。这就是我们对 Api.ai 的服务进行查询所需的内容:

![Finding your Api.ai API keys](img/07976bd30768be0396dd6347cf099d92.png)

## 代码

如果你想看一看工作代码并尝试一下，可以在 [GitHub](https://github.com/sitepoint-editors/Api-AI-Personal-Assistant-Demo) 上找到。请随意使用它，并扩展您自己的人工智能个人助理的想法。

如果你想尝试一下，我有[巴里在这里跑](http://devdiner.com/demos/barry/)。尽情享受吧！

## 使用 JavaScript 连接到 Api.ai

你目前有一个运行在 Api.ai 的云中的个人助理。您现在需要一种从自己的界面与您的个人助理对话的方式。Api.ai 拥有一系列平台 SDK，可以与 Android、iOS、web 应用、Unity、Cordova、C++等平台兼容。你甚至可以将它集成到 Slack bot 或 Facebook Messenger bot 中！对于这个例子，您将使用 HTML 和 JavaScript 来制作一个简单的个人助理 web 应用程序。我的演示建立在 [Api.ai 的 HTML + JS 要点](https://gist.github.com/ystrot/e799f338ab9849c90b04)中显示的概念之上。

您的应用程序将执行以下操作:

*   在输入字段中接受书面命令，当您按下回车键时提交该命令。
*   或者，使用 HTML5 语音识别 API(这只在 Google Chrome 25 和更高版本上有效)，如果用户单击“Speak”，他们可以说出他们的命令，并自动将其写入输入字段。
*   收到命令后，您可以使用 jQuery 向 Api.ai 提交一个 AJAX POST 请求，Api.ai 会将其知识作为 JSON 对象返回，正如您在测试控制台中看到的那样。
*   您将使用 JavaScript 读取 JSON 文件，并在您的 web 应用程序上显示结果。
*   如果可用，您的 web 应用程序还将使用 Web Speech API(在 Google Chrome 33 和更高版本中可用)来口头回复您。

GitHub 上面的链接提供了整个 web 应用程序。请随意参考，看看我是如何设计事物和构建 HTML 的。我不会在本文中解释它是如何组合在一起的，主要集中在 Api.ai SDK 方面。我还将指出并简要解释哪些位正在使用 HTML5 语音识别 API 和 Web 语音 API。

您的 JavaScript 包含以下变量:

```
var accessToken = "YOURACCESSTOKEN",
    baseUrl = "https://api.api.ai/v1/",
    $speechInput,
    $recBtn,
    recognition,
    messageRecording = "Recording...",
    messageCouldntHear = "I couldn't hear you, could you say that again?",
    messageInternalError = "Oh no, there has been an internal server error",
    messageSorry = "I'm sorry, I don't have the answer to that yet.";
```

以下是每个选项的用途:

*   `accessToken`。这是您从 Api.ai 接口复制过来的 API 键。这些给了你访问 SDK 的许可，也告诉你你正在访问哪个代理。我要见我的私人经纪人巴里。
*   `baseUrl`。这是对 Api.ai SDK 的所有调用的基本 URL。如果 SDK 的新版本出来了，你可以在这里更新。
*   `$speechInput`。这存储了您的`<input>`元素，因此您可以在您的 JavaScript 中访问它。
*   `$recBtn`。这将存储您的`<button>`元素，当用户想要点击并对 web 应用说话时，您将使用该元素。
*   `recognition`。您将您的`webkitSpeechRecognition()`功能存储在这个变量中。这是用于 HTML5 语音识别 API 的。
*   `messageRecording`、`messageCouldntHear`、`messageInternalError`和`messageSorry`。当应用程序正在录制用户语音、听不到他们的语音、出现内部错误以及您的代理不理解时，这些信息会显示出来。你将它们存储为变量，这样你就可以在你的脚本的顶部很容易地改变它们，并且你也可以指定你不希望应用程序在以后大声说出哪些变量。

在这些代码行中，查找用户何时在输入字段中按下 Enter 键。当这种情况发生时，运行`send()`函数将数据发送到 Api.ai:

```
$speechInput.keypress(function(event) {
  if (event.which == 13) {
    event.preventDefault();
    send();
  }
});
```

接下来，观察用户是否点击录音按钮，要求应用程序听他们说话(或者，如果它在听，暂停听)。如果他们点击它，运行`switchRecognition()`功能从记录切换到不记录，反之亦然:

```
$recBtn.on("click", function(event) {
  switchRecognition();
});
```

最后，对于您的初始 jQuery 设置，您设置了一个按钮，该按钮位于屏幕的右下角，用于显示和隐藏 JSON 响应。这只是为了保持整洁:大多数情况下，您不希望看到传入的 JSON 数据，但有时如果发生意外情况，您可以单击此按钮来切换 JSON 是否可见:

```
$(".debug__btn").on("click", function() {
  $(this).next().toggleClass("is-active");
  return false;
});
```

## 使用 HTML5 语音识别 API

如上所述，您将使用 HTML5 语音识别 API 来倾听用户并转录他们所说的内容。目前这只在谷歌 Chrome 上有效。

我们的`startRecognition()`函数看起来是这样的:

```
function startRecognition() {
  recognition = new webkitSpeechRecognition();

  recognition.onstart = function(event) {
    respond(messageRecording);
    updateRec();
  };
  recognition.onresult = function(event) {
    recognition.onend = null;

    var text = "";
    for (var i = event.resultIndex; i 
```

这就是 HTML5 语音识别 API 的运行方式。都是用`webkitSpeechRecognition()`里面的函数。以下是一些提示:

*   `recognition.onstart`。当从用户麦克风开始录音时运行。您使用一个名为`respond()`的函数来显示您的消息，告诉用户您正在收听他们。我将很快更详细地介绍`respond()`功能。`updateRec()`将录音按钮的文本从“停止”切换到“朗读”。
*   `recognition.onresult`。当语音识别有结果时运行。您解析结果，并通过`setInput()`设置您的文本字段来使用该结果(该函数只是将文本添加到输入字段，然后运行您的`send()`函数)。
*   `recognition.onend`。当语音识别结束时运行。您将此设置为`recognition.onresult`中的`null`,以防止它在您获得成功结果时运行。这样，如果`recognition.onend`运行，你就知道语音识别 API 还没有理解用户。如果函数确实运行了，您可以回复用户，告诉他们您没有听清楚。
*   `recognition.lang`。设置您要查找的语言。在演示的情况下，它在寻找美国英语。
*   `recognition.start()`。开始整个过程！

你的`stopRecognition()`函数就简单多了。它停止你的`recognition`并将其设置为`null`。然后，它会更新按钮以显示您不再录音:

```
function stopRecognition() {
  if (recognition) {
    recognition.stop();
    recognition = null;
  }
  updateRec();
}
```

`switchRecognition()`通过检查`recognition`变量来切换是开始还是停止识别。这使您的按钮可以打开和关闭识别:

```
function switchRecognition() {
  if (recognition) {
    stopRecognition();
  } else {
    startRecognition();
  }
}
```

## 与 Api.ai 通信

要将您的查询发送到 Api.ai，您可以使用如下所示的`send()`函数:

```
function send() {
  var text = $speechInput.val();
  $.ajax({
    type: "POST",
    url: baseUrl + "query",
    contentType: "application/json; charset=utf-8",
    dataType: "json",
    headers: {
      "Authorization": "Bearer " + accessToken
    },
    data: JSON.stringify({query: text, lang: "en", sessionId: "runbarry"}),

    success: function(data) {
      prepareResponse(data);
    },
    error: function() {
      respond(messageInternalError);
    }
  });
}
```

这是一个使用 jQuery to `https://api.api.ai/v1/query`的典型 AJAX POST 请求。您要确保向它发送 JSON 数据，并期望从它那里得到 JSON 数据。您还需要设置两个头——`Authorization`和`ocp-apim-subscription-key`——作为 Api.ai 的 API 键。

当收到响应时，运行`prepareResponse()`。在这个函数中，您将格式化 JSON 字符串，并将其放入 web 应用程序的调试部分，然后取出 Api.ai 响应的`result.speech`部分，该部分为您提供助手的文本响应。您通过`respond()`和`debugRespond()`显示每条信息:

```
function prepareResponse(val) {
  var debugJSON = JSON.stringify(val, undefined, 2),
      spokenResponse = val.result.speech;

  respond(spokenResponse);
  debugRespond(debugJSON);
}
```

您的`debugRespond()`函数将文本放入 JSON 响应的字段中:

```
function debugRespond(val) {
  $("#response").text(val);
}
```

您的`respond()`函数还有几个步骤:

```
function respond(val) {
  if (val == "") {
    val = messageSorry;
  }

  if (val !== messageRecording) {
    var msg = new SpeechSynthesisUtterance();
    var voices = window.speechSynthesis.getVoices();
    msg.voiceURI = "native";
    msg.text = val;
    msg.lang = "en-US";
      window.speechSynthesis.speak(msg);
  }

  $("#spokenResponse").addClass("is-active").find(".spoken-response__text").html(val);
}
```

开始时，您检查响应值是否为空。如果是这样，你设置它说它不确定这个问题的答案，因为 Api.ai 没有给你返回一个有效的响应:

```
if (val == "") {
  val = messageSorry;
}
```

如果您确实有一条消息要输出，并且它不是您正在记录的那条消息，那么您可以使用 Web Speech API 使用`SpeechSynthesisUtterance`对象大声说出这条消息。我发现没有设置`voiceURI`和`lang`，我的浏览器默认语音是德语！这使得它的演讲很难理解，直到我改变了它。要实际说出信息，您可以使用`window.speechSynthesis.speak(msg)`功能:

```
if (val !== messageRecording) {
  var msg = new SpeechSynthesisUtterance();
  msg.voiceURI = "native";
  msg.text = val;
  msg.lang = "en-US";
  window.speechSynthesis.speak(msg);
}
```

*注意:不要让它说出文本的“录音…”部分，这一点很重要:如果你这样做了，麦克风将拾取该语音并将其添加到录音查询中。*

最后，显示您的响应框，并将文本添加到其中，以便用户也可以阅读:

```
$("#spokenResponse").addClass("is-active").find(".spoken-response__text").html(val);
```

## 托管您的 Web 界面

为了获得最佳效果，您可能需要将其托管在启用了 HTTPS 的 web 服务器上。你对 Api.ai 的请求是通过 HTTPS 的，所以最好也把你的 web 界面放在 HTTPS。如果你只是想把它作为一个原型，并且没有现成的 HTTPS 安全网络服务器，试试 Glitch.com！这是一个新的服务，可以托管包含前端和后端(Node.js)代码的代码片段。

举个例子，我让 Barry 也在 [https://barry.glitch.me](https://barry.glitch.me) 主持。故障主机目前是完全免费的！这是一个很好的服务，我强烈建议尝试一下。

如果你想做一个更大的项目，要么考虑[让我们加密](https://letsencrypt.org/)获得一个免费的 SSL/TLS 证书，要么考虑从你的网络主机购买一个。

## 在活动

如果您在 GitHub repo 中使用我的样式运行 web 应用程序，它看起来像这样:

![Barry, my AI assistant in action](img/62f417f72fe7e5725822cb00eba7abed.png)

如果你通过点击“说话”问它一个问题，并说“你好吗？”，它最初显示您正在录制:

![Our app whilst it is recording](img/477d6245d2725f5543cb9f2450cb1bb8.png)

(当您点击该按钮时，您可能需要授予 Chrome 访问您的麦克风的权限。很明显，这种情况每次都会发生，除非你以 HTTPS 的身份服务这个页面。)

然后，它以视觉方式回应(也说英语，这很难在截图中显示出来)，如下所示:

![Your first response from Api.ai](img/2dc61ba603618a7d30b60423b34c0da7.png)

你也可以点击右下角的按钮来查看 Api.ai 给你的 JSON 响应，以防你想调试结果:

![Viewing your JSON result to debug](img/44addbf9e002568be979722cf5c82a45.png)

如果你似乎主要是得到“我听不到你，你能再说一遍吗？”消息，请在浏览器中检查您的麦克风权限。如果你在本地加载页面(例如，如果你的地址栏以`file:///`开头)，Chrome 似乎根本不提供任何对麦克风的访问，因此无论如何你都会以这个错误结束！你需要在某个地方举办。(试试上面提到的 Glitch.com。)

![Interface not hearing you](img/22ca96e41b65f8eb17e91f3f30120641.png)

就我个人而言，我不喜欢闲聊中的一些默认短语，比如这个:

![Who are you AI](img/d4c8380ac66a446483e4f67f3db29182.png)

我已经在我们之前看到的那些设置中定制了一些。例如，我发现列表中的这个闲聊语句很奇怪，于是决定将其定制为:

![Asking your AI for a hug](img/9bb0c34da2ef603a4d3d1b5228acc073.png)

所以，走出去，定制你自己的聊天机器人吧！让它独一无二，玩得开心！

### 有问题吗？

我发现，偶尔，如果 Web 语音 API 试图说一些太长的话，Chrome 的语音就会停止工作。如果是这种情况，请关闭标签页，打开一个新标签页再试一次。

## 结论

我相信你可以看到，Api.ai 是一种非常简单的方式来启动和运行聊天机器人式的人工智能个人助理。

想继续开发你的 Api.ai bot？还有更多的事情可以做:[这是我在 SitePoint 上写的整个系列](https://www.sitepoint.com/series/building-an-ai-assistant-with-api-ai/)！

如果你用 Api.ai 构建自己的个人助理，我很想听听！你也给你的孩子取名叫巴里吗？你为它设置了哪些问题？请在下面的评论中告诉我，或者在 Twitter 上通过 [@thatpatrickguy](https://www.twitter.com/thatpatrickguy) 与我联系。

用情感工具赋予你的人工智能人情味。查看我们在[微软认知服务和文本分析 API](https://www.sitepoint.com/premium/screencasts/microsoft-cognitive-services-and-the-text-analytics-api) 上的截屏。

## 分享这篇文章