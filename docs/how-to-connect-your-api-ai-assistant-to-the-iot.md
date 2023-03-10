# 如何将您的 Api.ai 助手连接到物联网

> 原文：<https://www.sitepoint.com/how-to-connect-your-api-ai-assistant-to-the-iot/>

如果你渴望了解更多关于人工智能的知识，请查看我们的截屏视频[微软认知服务和文本分析 API](https://www.sitepoint.com/premium/screencasts/microsoft-cognitive-services-and-the-text-analytics-api) ，了解你的机器人中的人工智能情感。

![How to Connect Your Api.ai Assistant to the IoT](img/48b158468d0a28ebc22641cac75368fc.png)

当个人助理通过物联网访问个人数据和真实世界时，它的潜力令人兴奋。新的可能性出现了——从请求你的助手打开你的灯到问它你睡得怎么样。作为一个例子，我们将把你的 Api.ai 助手连接到 Jawbone Up API。

*注意:本文于 2017 年更新，以反映 Api.ai 的最新变化*

## 你需要什么

本文建立在我们在 SitePoint 之前的文章中已经介绍过的各种概念之上。为了轻松地学习本教程，您需要以下内容。

*   **一个 Api.ai 代理连接到一个简单的 HTML web app。**如果你想了解这个过程，请参见[这篇文章。否则，您也可以从本指南下载代码并使用它。](https://www.sitepoint.com/how-to-build-your-own-ai-assistant-using-api-ai)
*   一个被教导“睡眠”实体的代理。我们在[创建了这个，让你的 Api.ai 助手拥有实体](https://www.sitepoint.com/teaching-our-api-ai-assistant-concepts-via-entities)。它应该理解像“我昨晚睡了多长时间？”以及“我有多长时间的快速眼动睡眠？”如果你想让它适应你自己的物联网设备，你需要创建自己的定制实体，了解你的物联网功能。
*   **Node . js 和运行节点服务器的一般知识。**没有它，你将无法让服务器运行！
*   关于如何使用 Jawbone UP API(或者你打算使用的其他 API)的知识。我们已经在之前的[中介绍了 Jawbone Up API，通过 Node.js](https://www.sitepoint.com/connecting-jawbone-up-api-node-js/) 连接到 Jawbone Up API，我将从头到尾引用那篇文章中的部分。
*   在 HTTPS 上运行您的网站的 SSL 证书。如果使用 Jawbone Up API，您将需要它。正如在本系列开始时提到的，总体来说，在 HTTPS 上做这些事情要容易一些。如果你感兴趣的话，我们会在 Jawbone Up API 的文章中介绍[如何建立自签名证书](https://www.sitepoint.com/connecting-jawbone-up-api-node-js#using-https)，但这并不是目前最简单的选择。您可以使用本系列第一篇文章中提到的 Let's Encrypt 非常容易地做到这一点。Glitch.com 也提供了一个很棒的原型开发环境，这是 HTTPS 默认提供的。

## 代码

这个演示的所有代码都可以供您下载和使用！你可以在 GitHub 上找到它[。](https://github.com/sitepoint-editors/Api-AI-Personal-Assistant-IoT-Demo)

## 这是如何工作的

您的 Api.ai 助手已经连接到一个简单的 web 应用程序，该应用程序通过 HTML5 语音识别 Api 接受语句。从这里开始，您需要添加一个新的功能来监听来自 Api.ai 代理的特定操作。在你的情况下，这是“睡眠时间”的行动。

每当您的 JavaScript 检测到这个动作，就会触发一个单独的对 Node.js 应用程序的调用，向 Jawbone API 请求该数据。一旦 web 应用程序接收到这些数据，您的 web 应用程序会将其转化为一个漂亮的句子并读出它——为您的助理提供一个全新的智能范围！

## 您的项目结构

我已经将应用程序从最初的纯 HTML 结构调整为使用 EJS 视图的结构，这样当你通过 OAuth 登录 Jawbone Up API 时，你可以在你的 web 应用程序中切换页面。实际上，您实际上只有一个页面，但这种方法允许您在将来为其他物联网设备添加更多页面。这张单视图位于`/views/index.ejs`。然后，您将节点服务器作为`server.js`放在根文件夹中，证书文件也放在根文件夹中。为了保持相对简单和包容，所有前端 JavaScript 和 CSS 都是内联的。你可以随意将它们移入 CSS 和 JS 文件中，缩小它们，让它们看起来更漂亮。

![Your file structure](img/896b0c15229a2ae40977e8cf6177d0c7.png)

## 在 JavaScript 中响应 Api.ai 操作

您可能还记得上一篇文章，当 Api.ai 返回一个响应时，它会提供一个如下所示的 JSON 对象:

```
{
  "id": "6b42eb42-0ad2-4bab-b7ea-853773b90219",
  "timestamp": "2016-02-12T01:25:09.173Z",
  "result": {
    "source": "agent",
    "resolvedQuery": "how did I sleep last night",
    "speech": "I'll retrieve your sleep stats for you now, one moment!",
    "action": "sleepHours",
    "parameters": {
      "sleep": "sleep"
    },
    "metadata": {
      "intentId": "25d04dfc-c90c-4f55-a7bd-6681e83b45ec",
      "inputContexts": [],
      "outputContexts": [],
      "contexts": [],
      "intentName": "How many hours of @sleep:sleep did I get last night?"
    }
  },
  "status": {
    "code": 200,
    "errorType": "success"
  }
}
```

在这个 JSON 对象中，您需要使用两位数据— `action`和`parameters.sleep`:

```
"action": "sleepHours",
"parameters": {
  "sleep": "sleep"
},
```

`action`是用户触发的 Api.ai 动作的名称。在你的睡眠例子中，你把它命名为“睡眠时间”。`parameters`在你的句子中包含可以改变一些细节的变量。在睡眠的情况下，你的参数告诉你什么类型的睡眠——“睡眠”、“深度睡眠”、“浅睡眠”或“快速眼动睡眠”(或只是“快速眼动”)。

最初，在关于 Api.ai 的一篇早期文章中，`prepareResponse()`函数从 Api.ai 获取 JSON 响应，将整个内容放入右下角的调试文本字段，并取出 Api.ai 的口头响应以显示在 web 应用程序中。你完全依赖你的 Api.ai 代理所说的，没有添加任何你自己的功能:

```
function prepareResponse(val) {
  var debugJSON = JSON.stringify(val, undefined, 2),
    spokenResponse = val.result.speech;

  respond(spokenResponse);
  debugRespond(debugJSON);
}
```

这一次，注意动作字段，如果动作包含`"sleepHours"`，运行自己的函数`requestSleepData()`。在这个函数中，传入 sleep 参数，这样您就知道正在请求哪种类型的睡眠:

```
function prepareResponse(val) {
  var debugJSON = JSON.stringify(val, undefined, 2),
    spokenResponse = val.result.speech;

  if (val.result.action == "sleepHours") {
    requestSleepData(val.result.parameters.sleep);
  } else {
    respond(spokenResponse);
  }

  debugRespond(debugJSON);
}
```

在`requestSleepData()`中，从 Node.js 服务器请求所有睡眠数据，然后通过查看返回的数据数组中的第一个值来过滤它(`data.items[0].details`):这将是昨晚的睡眠。在这些细节中，你有`data.items[0].details.rem`的快速眼动睡眠、`data.items[0].details.sound`的深度睡眠、`data.items[0].details.light`的浅度睡眠和`data.items[0].details.duration`的睡眠总量记录:

```
function requestSleepData(type) {
  $.ajax({
    type: "GET",
    url: "/sleep_data/",
    contentType: "application/json; charset=utf-8",
    dataType: "json",
    success: function(data) {
      console.log("Sleep data!", data);

      if (data.error) {
        respond(data.error);
        window.location.replace("/login/jawbone");
      }

      switch (type) {
        case "REM sleep":
          respond("You had " + toHours(data.items[0].details.rem) + " of REM sleep.");
          break;
        case "deep sleep":
          respond("You had " + toHours(data.items[0].details.sound) + " of deep sleep.");
          break;
        case "light sleep":
          respond("You had " + toHours(data.items[0].details.light) + " of light sleep.");
          break;
        case "sleep":
          respond("You had " + toHours(data.items[0].details.duration) + " of sleep last night. That includes " + toHours(data.items[0].details.rem) + " of REM sleep, " + toHours(data.items[0].details.sound) + " of deep sleep and " + toHours(data.items[0].details.light) + " of light sleep.");
          break;
      }
    },
    error: function() {
      respond(messageInternalError);
    }
  });
}
```

`toHours()`是一个简单快捷的功能，可以将你的时间格式化成句子，如“1 小时 53 分 59 秒”:

```
function toHours(secs) {
  hours = Math.floor(secs / 3600),
  minutes = Math.floor((secs - (hours * 3600)) / 60),
  seconds = secs - (hours * 3600) - (minutes * 60);

  hourText = hours + (hours > 1 ? " hours, " : " hour, ");
  minuteText = minutes + (minutes > 1 ? " minutes " : " minute ");
  secondText = seconds + (seconds > 1 ? " seconds" : " second");

  return hourText + minuteText + "and " + secondText;
}
```

正如您将在查看`requestSleepData()`函数时看到的，最终结果是对`respond()`的调用——这个函数之前接受了 Api.ai 的语音响应。您可以重用现有的功能来将语音加入到您自己的响应中，允许您的助理在准备好之后告诉用户这些信息。

前端 JavaScript 需要指出的最后一个方面是错误处理。如果您对 Jawbone 返回数据的方式有问题(通常是因为没有登录到服务中)，您的服务器会用一个格式为`{"error" : "Your error message"}`的 JSON 值来响应。助手会看到这一点，并自动将用户带到您的 OAuth 登录页面:

```
if (data.error) {
  respond(data.error);
  window.location.replace("/login/jawbone");
}
```

## 您的 Node.js 服务器

你的 Node.js 服务器是基于用 Node.js 连接到 Jawbone UP API 的[中使用的服务器。如果任何代码看起来令人困惑，请随意参考之前的文章，因为它解释了关于通过 OAuth 连接到 Jawbone API 并设置 HTTPS 服务器来运行它的所有内容。如果你没有下巴，同样的概念可以用于其他物联网设备。您只需要添加自己的方法来响应带有不同数据的 GET 请求(并且您可能不需要担心 OAuth)。这里的颚骨向上数据只是一个例子。](https://www.sitepoint.com/connecting-jawbone-up-api-node-js)

您的 Jawbone 数据已经过调整，以提供简单的 JSON 响应，而不是将其全部格式化为表格模板视图。`up`和`options`的变量已经被移到全局变量中，这样它们可以在对 API 的多个请求中重用(在另一个 SitePoint 示例中，我们每次只请求一大块数据)。

要通过 OAuth 登录 Jawbone API，用户可以转到`/login/jawbone`。然而，正如你在上面看到的，他们不需要知道这样做。如果发现他们没有登录，您的助理也可以重定向他们。你也可以在你的 Api.ai 代理中添加一个新的 intent，它可以理解“让我登录到我的 Jawbone Up 数据”这个短语，如果你想让它真正无缝的话。Node.js 中的登录路径如下所示:

```
app.get("/login/jawbone",
  passport.authorize("jawbone", {
    scope: ["basic_read","sleep_read"],
    failureRedirect: "/"
  })
);
```

一旦你通过`passport.use("jawbone", new JawboneStrategy())`登录到 Jawbone API，将这个访问权分配给你的`up`变量，并将用户导向`/barry`。您可以将用户重定向到您选择的任何路径，只要它不同于您的根目录(这似乎只会导致我的服务器无休止的斗争)。我选择了`/barry`，因为我将我的助手命名为 Barry，并认为它不言自明(页面显示完全相同的索引视图，因此没有任何不同)。如果您愿意，您也可以使用这种方式为成功登录 Jawbone 设备的用户提供不同的视图。一旦登录，用户可以返回到根`https://localhost:5000`页面并使用功能。

### 发回您的物联网数据

一旦收到对`/sleep_data`的 GET 请求，就可以非常简单地检索 Jawbone 数据。检查是否定义了`up`变量:如果没有，您的用户还没有登录，您告诉 web 应用程序这一点，以便它可以执行重定向并告诉用户他们需要登录。如果 Jawbone 在调用`up.sleeps.get()`时返回任何错误，并且如果`jawboneData.items`没有被定义，那么做同样的事情:

```
app.get("/sleep_data", function(req, resp) {
  if (up !== undefined) {
    up.sleeps.get({}, function(err, body) {
      if (err) {
        console.log("Error receiving Jawbone UP data");
        resp.send({"error": "Your sleep tracker isn't talking to me. Let's try logging in again."});
      } else {
        var jawboneData = JSON.parse(body).data;

        if (jawboneData.items) {
          resp.send(jawboneData);
        } else {
          console.log("Error: " + jawboneData);
          resp.send({"error": "Your sleep tracker isn't talking to me. Let's try logging in again."});
        }
      }
    });
  } else {
    console.log("Up is not ready, lets ask to log in.");
    resp.send({"error": "Your sleep tracker isn't talking to me. Let's try logging in again."});
  }
});
```

这里的错误也可能是由其他因素引起的，但为了简单起见，我将重点放在让他们再次尝试登录上。在生产级别的应用程序中，您会希望研究各种原因并调整您的响应。

如果一切正常，并且您收到了有效的响应，那么将它作为 JSON 响应发送回 web 应用程序，以便很好地阅读和解析:

```
if (jawboneData.items) {
  resp.send(jawboneData);
}
```

通过 web 应用程序和 Node.js 服务器的协同工作，您现在应该能够从 Jawbone Up 设备中检索睡眠数据了。所以让我们试一试。

## 在活动

用通常的`node server.js`运行你的服务器。记住，你需要为你的 npm 模块运行`npm install`，并且需要在你的服务器上有一个[证书来通过 HTTPS 运行它。](https://www.sitepoint.com/connecting-jawbone-up-api-node-js#using-https)

在你的网络浏览器中访问`https://localhost:5000`应该会把你带到你的人工智能助手那里。(如果你用的是 Glitch 之类的服务，你会得到一个 Glitch URL。)问它你睡了多少觉:

![Your assistant redirecting you to log in](img/b84a410645b6ede985a0cf67365aeb75.png)

原来你还没有登录。它会引导您进入 Jawbone Up OAuth 登录屏幕。登录并同意提供对您数据的访问权限，然后单击“同意”:

![Logging into the Jawbone Up API](img/5643ea980a95525fe707edff00d545eb.png)

如果这次你问这个问题，你会得到正确的答案:

![Your Api.ai assistant telling you your sleep patterns](img/16e92f7e481ad2993244c6d8b26a17ee.png)

你还可以问它一些更具体的问题，比如“我得到了多少雷姆？”要测试参数:

![Your Api.ai assistant showing how much REM sleep you've gotten](img/dc875ce7c0db2b4a3086fc8df0b6654c.png)

## 结论

这就结束了对 Api.ai 功能的各种探索。您可以扩展这个例子，让它理解日期范围(例如，“我星期二睡了多久？”)或者稍微更好地格式化时间(注意到其中一个响应中的一个小错误吗？).你也可以用更好更简短的方式来表达你的回答。个性化它—让它成为你自己的！

正如您所看到的，通过使用这些方法，您可以将任何 Node.js 兼容或 web API 兼容的服务连接到您的 Node.js 服务器，在您的 Api.ai 代理中将它与一个意图挂钩，并教会它各种事情！你可以通过 IFTTT 、[连接大量的](https://www.sitepoint.com/connecting-lifx-light-bulbs-iot-using-ifttt)[物联网设备，通过 IFTTT](https://www.sitepoint.com/connecting-the-iot-and-node-js-to-ifttt/) 连接你的 LIFX 智能灯，甚至连接[你自己的节点机器人](https://www.sitepoint.com/an-introduction-to-nodebots/)。可能性只受您所拥有的设备的限制！

如果你一直在使用 Api.ai 构建你自己的个人助理，我很想听听你是怎么做的！你把它和什么联系起来了？请在下面的评论中告诉我，或者在 Twitter 上通过 [@thatpatrickguy](https://www.twitter.com/thatpatrickguy) 与我联系。

用情感工具赋予你的人工智能人情味。查看我们在[微软认知服务和文本分析 API](https://www.sitepoint.com/premium/screencasts/microsoft-cognitive-services-and-the-text-analytics-api) 上的截屏。

## 分享这篇文章