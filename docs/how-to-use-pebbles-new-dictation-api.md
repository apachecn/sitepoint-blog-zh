# 如何使用 Pebble 的新听写 API

> 原文：<https://www.sitepoint.com/how-to-use-pebbles-new-dictation-api/>

本月早些时候，Pebble 发布了他们的 SDK 的 3.6 版本。当我发现它包括访问他们的听写 API 时，我睁大了眼睛。此 API 允许所有 Pebble 开发者通过 Pebble Time 麦克风进行听写。我迫不及待地打开了 CloudPebble 编辑器并开始实验！

在本文中，我们将通过组装 watchapp 来探索听写 API，watch app 接受听写的消息并通过 IFTTT 将其发送到 Slack 通道。

## 你需要什么

为了逐步完成本指南，您需要以下内容:

*   Pebble Time 手表——老式的 Pebble 手表没有麦克风，所以这个功能对它们不起作用！
*   Pebble 应用程序开发的一般知识——如果您是 Pebble 开发的新手，请阅读我的初学者教程《用 JavaScript 开发 Pebble 手表》。
*   [一个 IFTTT 账户](https://ifttt.com)
*   [发布到](https://slack.com)的 Slack 账户和 Slack 频道–如果你不是 Slack 用户，你可以将它切换到任何其他允许你发送消息的服务。

## 代码

有些开发人员更喜欢直接进入代码。如果你是那种类型的开发者，所有的代码都可以在 [GitHub](https://github.com/sitepoint-editors/PebbleSlackDictation) 上找到。

我们的 watchapp 将有两个主要文件:

*   `main.c`–我们的 C 代码管理听写、指令显示和输入信息显示。
*   这管理我们与 IFTTT 的通信。

## IFTTT

我建议首先设置 IFTTT Maker 通道和 Slack 规则，这样我们在此之后设置的代码更有意义。

我之前在文章[中讨论过使用 IFTTT](https://www.sitepoint.com/connecting-lifx-light-bulbs-iot-using-ifttt/) 将 LIFX 灯泡连接到物联网，以及[将物联网和 Node.js 连接到 IFTTT](https://www.sitepoint.com/connecting-the-iot-and-node-js-to-ifttt/) 中讨论过 IFTTT(第二篇文章讨论了 Maker 频道)。因此，如果你想更深入地了解如何使用 IFTTT 和 Maker 频道，可以通读一下。

这里有一个你需要设置的快速流程:

*   如果您还没有一个 IFTTT 帐户,请创建一个[。](https://ifttt.com/)
*   将 [Maker 频道](https://ifttt.com/maker)和 [Slack 频道](https://ifttt.com/slack)连接到您的账户。
*   [以制作器频道为触发器创建新配方](https://ifttt.com/myrecipes/personal/new)。
*   选择“接收 web 请求”触发选项，并将事件命名为“slack_message”。
*   选择松弛渠道作为 IFTTT 规则的活动渠道，并选择“发布到渠道”活动。
*   选择您希望消息进入的空闲频道，并将消息设置为仅包含`{{Value1}}`。
*   您可以删除标题，或者选择类似“收到 Pebble 消息”的内容。我没填。我也把缩略图网址留为空白。
*   创造你的行动，你应该准备好了！IFTTT 准备好将它从该事件收到的任何消息发送到您指定的松弛通道。

## 我们的 C 代码

`main.c`文件看起来是这样的:

```
#include <pebble.h>

static Window *s_main_window;
static TextLayer *message_layer;

static DictationSession *s_dictation_session;
static char display_message[512];

static void handle_message(char *slack_message) {
  DictionaryIterator *iter;
  app_message_outbox_begin(&iter);

  dict_write_cstring(iter, 0, slack_message);

  app_message_outbox_send();
}

static void dictation_session_callback(DictationSession *session, DictationSessionStatus status, char *transcription, void *context) {
  if(status == DictationSessionStatusSuccess) {
    snprintf(display_message, sizeof(display_message), "Message sent!\n\n\"%s\"", transcription);
    text_layer_set_text(message_layer, display_message);
    handle_message(transcription);
  } else {
    static char error_message[128];
    snprintf(error_message, sizeof(error_message), "Error code:\n%d", (int)status);
    text_layer_set_text(message_layer, error_message);
  }
}

static void select_click_handler(ClickRecognizerRef recognizer, void *context) {
  dictation_session_start(s_dictation_session);
}

static void click_config_provider(void *context) {
  window_single_click_subscribe(BUTTON_ID_SELECT, select_click_handler);
}

static void window_load(Window *window) {
  Layer *window_layer = window_get_root_layer(window);
  GRect bounds = layer_get_bounds(window_layer);

  message_layer = text_layer_create(GRect(bounds.origin.x, (bounds.size.h - 72) / 2, bounds.size.w, bounds.size.h));
  text_layer_set_text(message_layer, "Press select and tell me your Slack message :)");
  text_layer_set_text_alignment(message_layer, GTextAlignmentCenter);
  text_layer_set_font(message_layer, fonts_get_system_font(FONT_KEY_GOTHIC_24_BOLD));
  layer_add_child(window_layer, text_layer_get_layer(message_layer));
}

static void window_unload(Window *window) {
  text_layer_destroy(message_layer);
}

static void init() {
  s_main_window = window_create();
  window_set_click_config_provider(s_main_window, click_config_provider);
  window_set_window_handlers(s_main_window, (WindowHandlers) {
    .load = window_load,
    .unload = window_unload,
  });
  window_stack_push(s_main_window, true);

  app_message_open(app_message_inbox_size_maximum(), app_message_outbox_size_maximum());

  s_dictation_session = dictation_session_create(sizeof(display_message), dictation_session_callback, NULL);
}

static void deinit() {
  dictation_session_destroy(s_dictation_session);

  window_destroy(s_main_window);
}

int main() {
  init();
  app_event_loop();
  deinit();
}
```

### 我们的听写呼叫

Pebble 开发者会注意到的第一个新代码是设置`DictationSession`的代码行。这是我们存储转录数据和与听写 API 相关的其他数据的位置:

```
static DictationSession *s_dictation_session;
```

接下来，我们定义一个简单的`char`数组来存储我们最新成功转录的消息:

```
static char display_message[512];
```

在我们的`init()`函数中，我们设置了实际的听写会话，这样它就准备好了，等待我们请求它。它被设置为提供空间来存储我们口述的与`display_message`的长度相匹配的消息。一旦我们完成听写，它还会将回调函数设置为`dictation_session_callback()`。

```
s_dictation_session = dictation_session_create(sizeof(display_message), dictation_session_callback, NULL);
```

### 点击运行听写

我们希望将我们的听写设置为在用户点击 Pebble 上的选择按钮时运行。我们通过`window_set_click_config_provider()`设置点击功能。

```
window_set_click_config_provider(s_main_window, click_config_provider);
```

这告诉我们的 Pebble 我们的点击功能是在`click_config_provider()`中定义的。在此过程中，我们通过按下选择按钮(在 Pebble 的 SDK 中定义为`BUTTON_ID_SELECT`)来初始化听写过程。该按钮动作通过`window_single_click_subscribe()`功能设置。

```
static void click_config_provider(void *context) {
  window_single_click_subscribe(BUTTON_ID_SELECT, select_click_handler);
}
```

我们的`select_click_handler()`功能只有一个任务——使用`dictation_session_start()`开始我们的听写会话:

```
static void select_click_handler(ClickRecognizerRef recognizer, void *context) {
  dictation_session_start(s_dictation_session);
}
```

当我们的听写会话完成后，用户通过麦克风给了我们他们的信息，我们告诉 Pebble 运行`dictation_session_callback()`。在这个函数中，我们首先检查听写是否成功。API 通过`DictationSessionStatus status`变量向我们提供这些信息:

```
static void dictation_session_callback(DictationSession *session, DictationSessionStatus status, char *transcription, void *context) {
  if(status == DictationSessionStatusSuccess) {
    // Dictation worked!
  } else {
    // Dictation failed :(
  }
}
```

如果听写成功，我们在应用程序上显示转录的消息(主要是为了给用户视觉反馈，应用程序有他们的消息)，然后我们将转录的消息传递到`handle_message()`(我们将很快介绍该功能如何工作):

```
snprintf(display_message, sizeof(display_message), "Message sent!\n\n\"%s\"", transcription);
text_layer_set_text(message_layer, display_message);
handle_message(transcription);
```

如果有错误，我们会在屏幕上显示错误消息代码，而不是转录:

```
static char error_message[128];
snprintf(error_message, sizeof(error_message), "Error code:\n%d", (int)status);
text_layer_set_text(message_layer, error_message);
```

### 将消息传递给 JavaScript

我们之前使用的`handle_message`函数获取一条消息，并将其放入我们应用程序的字典中。这允许我们通过 Pebble 的 AppMessage 功能将数据发送给 JavaScript 代码。我们通过`DictionaryIterator`访问字典，然后打开收件箱，准备使用`app_message_outbox_begin()`向我们的 JavaScript 发送消息。对于这个应用程序，我们的字典中只有一个键，它存储听写消息，所以当我们写入字典时，我们将消息写入`dict_write_cstring()`中的位置`0`。一旦消息保存在那个键中，我们就通过`app_message_outbox_send()`发送它。

```
static void handle_message(char *slack_message) {
  DictionaryIterator *iter;
  app_message_outbox_begin(&iter);

  dict_write_cstring(iter, 0, slack_message);

  app_message_outbox_send();
}
```

对于以前从事过 Pebble 应用程序开发的任何人来说，C 代码的其余部分都是显而易见的，所以我们将继续讨论 JavaScript！

## 我们的 JavaScript 代码

等式的 JavaScript 部分稍微短一点，看起来像这样:

```
var host = "https://maker.ifttt.com/trigger/",
  key = "PUTYOURKEYHERE";

function send_message(message) {
  console.log(host + "slack_message/with/key/" + key);
  var url = host + "slack_message/with/key/" + key,
    data = {"value1": message};

  sendPOSTRequest(url, data, function() {
    console.log("Error!");
  });
}

function sendPOSTRequest(url, data, fallback) {
  var req = new XMLHttpRequest();

  req.onreadystatechange = function(e) {
    if (req.readyState == 4 && req.status == 200) {
      var response = JSON.parse(req.responseText);
      console.log("Response was ", response);

      if (response !== undefined && !response.error) {
        console.log("Message sent successfully.");
      } else {
        console.log(response.error);
        if (fallback) fallback();
      }
    } else if (req.status == 404 || req.status == 500) {
      console.log("Error " + req.status);
      if (fallback) fallback();
    }
  };

  req.open("POST", url);
  req.setRequestHeader("Content-Type", "application/json");
  req.send(JSON.stringify(data));
}

Pebble.addEventListener("appmessage", function(e) {
  var message = e.payload["0"];
  console.log("Received message: " + message);
  send_message(message);
});
```

JavaScript 的开始为我们想要从 IFTTT 调用的 URL 设置了初始 URL 设置。该调用应该采用`https://maker.ifttt.com/trigger/{event}/with/key/{yourkey}`的格式。我们将 URL 的初始部分存储在`host`中，并将您的特定密钥存储在`key`中。你可以在[创客频道 IFTTT 页面](https://ifttt.com/maker)找到你的钥匙。其余的将在下一个函数中设置。

```
var host = "https://maker.ifttt.com/trigger/",
  key = "PUTYOURKEYHERE";
```

当我们想要向 IFTTT 发送消息时，我们将调用的函数已经被恰当地命名为`send_message()`。这将我们的`host`和`key`变量放在 IFTTT URL 中，还有我们的事件名`slack_message`。`data`变量包含一个键为`"value1"`的 JSON 对象，IFTTT 在研究如何响应 POST 请求时会查找这个对象。如果你想传入更多的值，你也可以传入`value2`和`value3`。

```
function send_message(message) {
  console.log(host + "slack_message/with/key/" + key);
  var url = host + "slack_message/with/key/" + key,
    data = {"value1": message};

  ...
```

然后在我们的函数中，我们将这个`url`和`data`传递给一个`sendPOSTRequest()`函数，它将执行真正的 HTTP 请求魔术。它有一个出错时将运行的错误回调函数(在这种情况下，我们只是将它记录到控制台)。

```
sendPOSTRequest(url, data, function() {
    console.log("Error!");
  });
}
```

`sendPOSTRequest()`函数使用典型的`XMLHttpRequest` JavaScript 格式。这对于任何包含 JSON 数据的 POST 请求来说都非常相似。解释这是如何工作的细节超出了本文的范围，但这在网络上很常见:

```
function sendPOSTRequest(url, data, fallback) {
  var req = new XMLHttpRequest();

  req.onreadystatechange = function(e) {
    if (req.readyState == 4 && req.status == 200) {
      var response = JSON.parse(req.responseText);
      console.log("Response was ", response);

      if (response !== undefined && !response.error) {
        console.log("Message sent successfully.");
      } else {
        console.log(response.error);
        if (fallback) fallback();
      }
    } else if (req.status == 404 || req.status == 500) {
      console.log("Error " + req.status);
      if (fallback) fallback();
    }
  };

  req.open("POST", url);
  req.setRequestHeader("Content-Type", "application/json");
  req.send(JSON.stringify(data));
}
```

最后，我们有一点代码触发我们的信息。我们监听由 C 代码中的`app_message_outbox_send()`触发的`appmessage`事件。当这个应用程序发现它时，它会以`{"0": "yourmessage"}`的格式发送数据。我们关注`"0"`的键，并将其发送给我们的`send_message()`函数。

```
Pebble.addEventListener("appmessage", function(e) {
  var message = e.payload["0"];
  console.log("Received message: " + message);
  send_message(message);
});
```

## 运行我们的应用

最好是在您的实际 Pebble Time 手表上运行该应用程序。您也可以在终端中使用 Pebble API 命令，通过 SDK 使用仿真器，但是我发现通过 CloudPebble 直接与我的 Pebble 对话要容易得多！构建应用程序时要记住的主要事情是确保项目设置中的“构建应用程序”是未选中的。我们的听写演示无法在 Aplite 平台上运行(这是第一个 Pebble watchface 平台)。您的设置应该如下所示:

![Pebble App Settings With Aplite Unchecked](img/5a8029255aa1cee093b28d170b23987e.png)

## 正在运行的应用程序！

当我们第一次打开应用程序时，它应该是这样的:

![The Pebble Slack App](img/b2502cd50fc91d3957c6d0d9b7bbf623.png)

然后，当您单击选择按钮(右手边中间的那个)时，Pebble 的听写屏幕应该会出现，等待您的声音。一旦你说了话，它就会收到你的信息，它会使用第三方服务 Nuance 来转录你的信息。一旦有了它，Pebble 听写屏幕将要求您确认消息是否正确。

![Pebble Dictation](img/2a835d83b3e4429f68bfcb5caaa6aa87.png)

如果是，接受它，它会向您的应用程序提供输入:

![Pebble Dictation Message Sent](img/2b2e7ca006e7fb2792553a9c97fea791.png)

然后，如果你看看你的松弛渠道，该消息也应该出现在那里！

![Message received in Slack](img/7698cec8598172305b7ebc13a0fb430c.png)

## 结论

新的听写 API 在 Pebble 应用程式中的实作非常简单。您可以将它连接到任意数量的 web APIs(或其他 IFTTT 通道)来完成一系列任务。有很多潜力可以分析口述的信息文本，并根据用户所说的内容设置不同的操作。

请随意使用此代码作为您自己的 Pebble 听写想法的起点。试一试吧，将语音驱动的智能手表应用程序放在一起并观看它的运行，这是令人难以置信的授权！如果你从本教程中有所收获，请在评论中分享或在 Twitter 上联系我( [@thatpatrickguy](http://www.twitter.com/thatpatrickguy) )。我很想听听。

如果你正在寻找关于 Pebble 开发或特别是听写 API 的进一步链接，我在我的 [Dev Diner Pebble 开发指南](http://devdiner.com/guides/pebble/)上有一组精选的链接。如果你有我没有列出的其他好资源，也请告诉我！

## 分享这篇文章