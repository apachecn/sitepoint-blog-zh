# 介绍 Web 语音 API

> 原文：<https://www.sitepoint.com/introducing-web-speech-api/>

获得学士学位后，我开始在一个名为 NLP 的小组工作。顾名思义，我们专注于自然语言处理(NLP)技术。当时，最流行的两种技术是 VoiceXML 标准和 Java 小程序。他们俩都有问题。第一个只有 Opera 支持。第二个用于将数据发送到服务器，并根据用户发出的命令执行操作，需要大量代码和时间。今天情况不同了。由于引入了专用的 JavaScript API，使用语音识别变得前所未有的简单。本文将向您介绍这个 API，称为 Web Speech API。

语音识别有几个实际应用。由于 Siri 和 S-Voice 等软件，越来越多的人熟悉了这个概念。这些应用程序可以极大地改善用户(尤其是残障人士)执行任务的方式。在网站中，用户可以使用他们的声音来导航页面或填充表单域。用户还可以在开车时与页面互动，而无需将视线从路上移开。这些都不是微不足道的用例。

## 什么是网络语音 API？

2012 年底推出的[网络语音 API](https://dvcs.w3.org/hg/speech-api/raw-file/tip/speechapi.html) ，允许网络开发者在网络浏览器中提供语音输入和文本到语音输出功能。通常，使用标准语音识别或屏幕阅读器软件时，这些功能不可用。这个 API 负责保护用户的隐私。在允许网站通过麦克风访问语音之前，用户必须明确授予权限。有趣的是，许可请求与[的 getUserMedia API](https://www.sitepoint.com/introduction-getusermedia-api) 相同，尽管它不需要网络摄像头。如果运行此 API 的页面使用 HTTPS 协议，浏览器只要求一次权限，否则每次新进程启动时都会这样做。

Web Speech API 定义了一个复杂的接口，叫做`SpeechRecognition`，它的结构可以在[这里看到](https://dvcs.w3.org/hg/speech-api/raw-file/tip/speechapi.html#speechreco-section)。由于两个主要原因，本文不会涵盖规范中描述的所有属性和方法。第一个是，如果你已经看到了接口，它太复杂了，无法在一篇文章中涵盖。其次，正如我们将在下一节中看到的，只有一个浏览器支持这个 API，并且它的实现非常有限。因此，我们将只讨论实现的方法和属性。

该规范声称<q cite="https://dvcs.w3.org/hg/speech-api/raw-file/tip/speechapi.html" target="_blank">API 本身不知道底层的语音识别和合成实现，并且可以支持基于服务器和基于客户端/嵌入式的识别和合成。</q>它支持两种类型的识别:一次性识别和连续识别。在第一种类型中，用户一停止说话识别就结束，而在第二种类型中，当调用`stop()`方法时识别就结束。在第二种情况下，我们仍然可以允许用户通过附加一个调用`stop()`方法的处理程序(例如通过一个按钮)来结束识别。识别的结果作为假设的列表提供给我们的代码，以及每个假设的其他相关信息。

Web Speech API 的另一个有趣的特性是它允许您指定一个[语法对象](https://dvcs.w3.org/hg/speech-api/raw-file/tip/speechapi.html#speechreco-speechgrammar)。详细解释什么是语法，超出了本文的范围。你可以把它想象成一套定义语言的规则。使用语法的好处是，由于语言可能性的限制，它通常会导致更好的结果。

这个 API 可能不会让你感到惊讶，因为 Chrome 11 中已经引入了已经实现的`x-webkit-speech`属性。主要的区别是 Web Speech API 允许你实时看到结果，并利用语法。你可以通过查看[如何使用 HTML5 语音输入字段](https://www.sitepoint.com/html5-speech-input-fields/)来了解更多关于这个属性的信息。

现在，您应该对这个 API 是什么以及它能做什么有了一个很好的概述，让我们检查一下它的属性和方法。

### 方法和属性

要实例化语音识别器，使用如下所示的函数`speechRecognition()`:

```
var recognizer = new speechRecognition();
```

该对象公开下列方法:

*   `onstart`:设置一个回调，当识别服务开始监听音频以进行识别时触发该回调。
*   `onresult`:设置语音识别器返回结果时触发的回调。该事件必须使用 [`SpeechRecognitionEvent`](https://dvcs.w3.org/hg/speech-api/raw-file/tip/speechapi.html#speechreco-event) 界面。
*   `onerror`:设置语音识别错误发生时触发的回调。该事件必须使用 [`SpeechRecognitionError`](https://dvcs.w3.org/hg/speech-api/raw-file/tip/speechapi.html#speechreco-error) 界面。
*   `onend`:设置服务断开时触发的回调。无论什么原因，会话结束时都必须生成事件。

除了这些方法，我们还可以使用下列属性配置语音识别对象:

*   `continuous`:设置识别的类型(单次或连续)。如果它的值被设置为`true`,我们有一个连续的识别，否则一旦用户停止说话，该过程就结束。默认设置为`false`。
*   `lang`:指定识别语言。默认情况下，它对应于浏览器语言。
*   `interimResults`:指定我们是否需要中期结果。如果它的值被设置为`true`，我们将可以访问中间结果，并向用户展示以提供反馈。如果值是`false`，我们只有在识别结束后才能得到结果。默认设置为`false`。

作为`result`事件处理程序的参数，我们接收一个类型为`SpeechRecognitionEvent`的对象。后者包含以下数据:

*   `results[i]`:包含识别结果的数组。每个数组元素对应一个已识别的单词。
*   `resultIndex`:当前识别结果索引。
*   `results[i].isFinal`:布尔值，表示结果是最终结果还是临时结果。
*   `results[i][j]`:一个 2D 数组，包含可选的已识别单词。第一个元素是最有可能被识别的单词。
*   `results[i][j].transcript`:已识别单词的文本表示。
*   `results[i][j].confidence`:结果正确的概率。该值的范围从 0 到 1。

## 浏览器兼容性

上一节指出，网络语音 API 的提案是在 2012 年末提出的。到目前为止，唯一支持该 API 的浏览器是 Chrome，从版本 25 开始，该规范的子集非常有限。此外，Chrome 支持使用 webkit 前缀的 API。因此，在 Chrome 中创建一个语音识别对象，如下所示:

```
var recognizer = new webkitSpeechRecognition();
```

## 演示

本节提供了一个 Web Speech API 的运行演示。演示页面包含一个只读字段和三个按钮。需要该字段来显示已识别语音的转录。前两个按钮启动和停止识别过程，而第三个按钮清除操作和错误消息的日志。该演示还允许您使用两个单选按钮在单次模式和连续模式之间进行选择。

因为只有 Chrome 支持这个 API，我们执行一个检查，如果失败，我们显示一个错误消息。一旦支持得到验证，我们就初始化语音识别对象，这样我们就不必在每次用户单击“播放演示”按钮时都执行这个操作。我们还附加了一个处理程序来启动识别过程。注意，在处理程序内部，我们还设置了识别模式。我们需要在处理程序中选择模式，以确保它反映了用户的选择(每次新的识别开始时都需要刷新)。

代码的现场演示可从[这里](http://aurelio.audero.it/demo/web-speech-api-demo.html)获得。哦，只是为了好玩，试着说一句脏话。

```
<!DOCTYPE html>
<html>
  <head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0"/>
    <title>Web Speech API Demo</title>
    <style>
      body
      {
        max-width: 500px;
        margin: 2em auto;
        font-size: 20px;
      }

      h1
      {
        text-align: center;
      }

      .buttons-wrapper
      {
        text-align: center;
      }

      .hidden
      {
        display: none;
      }

      #transcription,
      #log
      {
        display: block;
        width: 100%;
        height: 5em;
        overflow-y: scroll;
        border: 1px solid #333333;
        line-height: 1.3em;
      }

      .button-demo
      {
        padding: 0.5em;
        display: inline-block;
        margin: 1em auto;
      }
    </style>
  </head>
  <body>
    <h1>Web Speech API</h1>
    <h2>Transcription</h2>
    <textarea id="transcription" readonly="readonly"></textarea>

    <span>Results:</span>
    <label><input type="radio" name="recognition-type" value="final" checked="checked" /> Final only</label>
    <label><input type="radio" name="recognition-type" value="interim" /> Interim</label>

    <h3>Log</h3>
    <div id="log"></div>

    <div class="buttons-wrapper">
      <button id="button-play-ws" class="button-demo">Play demo</button>
      <button id="button-stop-ws" class="button-demo">Stop demo</button>
      <button id="clear-all" class="button-demo">Clear all</button>
    </div>
    <span id="ws-unsupported" class="hidden">API not supported</span>

    <script>
      // Test browser support
      window.SpeechRecognition = window.SpeechRecognition       ||
                                 window.webkitSpeechRecognition ||
                                 null;

      if (window.SpeechRecognition === null) {
        document.getElementById('ws-unsupported').classList.remove('hidden');
        document.getElementById('button-play-ws').setAttribute('disabled', 'disabled');
        document.getElementById('button-stop-ws').setAttribute('disabled', 'disabled');
      } else {
        var recognizer = new window.SpeechRecognition();
        var transcription = document.getElementById('transcription');
        var log = document.getElementById('log');

        // Recogniser doesn't stop listening even if the user pauses
        recognizer.continuous = true;

        // Start recognising
        recognizer.onresult = function(event) {
          transcription.textContent = '';

          for (var i = event.resultIndex; i < event.results.length; i++) {
            if (event.results[i].isFinal) {
              transcription.textContent = event.results[i][0].transcript + ' (Confidence: ' + event.results[i][0].confidence + ')';
            } else {
              transcription.textContent += event.results[i][0].transcript;
            }
          }
        };

        // Listen for errors
        recognizer.onerror = function(event) {
          log.innerHTML = 'Recognition error: ' + event.message + '<br />' + log.innerHTML;
        };

        document.getElementById('button-play-ws').addEventListener('click', function() {
          // Set if we need interim results
          recognizer.interimResults = document.querySelector('input[name="recognition-type"][value="interim"]').checked;

          try {
            recognizer.start();
            log.innerHTML = 'Recognition started' + '<br />' + log.innerHTML;
          } catch(ex) {
            log.innerHTML = 'Recognition error: ' + ex.message + '<br />' + log.innerHTML;
          }
        });

        document.getElementById('button-stop-ws').addEventListener('click', function() {
          recognizer.stop();
          log.innerHTML = 'Recognition stopped' + '<br />' + log.innerHTML;
        });

        document.getElementById('clear-all').addEventListener('click', function() {
          transcription.textContent = '';
          log.textContent = '';
        });
      }
    </script>
  </body>
</html>
```

## 结论

本文介绍了 Web Speech API，并解释了它是如何帮助改善用户体验的，尤其是对于那些有残疾的用户。这个 API 的实现还处于非常早期的阶段，只有 Chrome 提供了有限的功能。这个 API 的潜力是不可思议的，所以请关注它的发展。最后一点，别忘了玩试玩，真的很有娱乐性。

## 分享这篇文章