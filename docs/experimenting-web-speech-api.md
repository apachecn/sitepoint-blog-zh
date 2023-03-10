# 试用 Web 语音 API

> 原文：<https://www.sitepoint.com/experimenting-web-speech-api/>

几天前，我在 2014 年 WebTech 大会上做了一个题为 [<cite>谈论和收听网页</cite>](https://speakerdeck.com/aurelioderosa/talking-and-listening-to-web-pages-webtech-conference-2014) 的演讲，在演讲中我讨论了 Web Speech API 以及开发人员如何利用它来改善用户体验。这个演讲的灵感来自于我为 SitePoint 写的两篇文章，标题为 [<cite>，介绍网络语音 API</cite>](https://www.sitepoint.com/introducing-web-speech-api/) 和 [<cite>会说话的网页以及语音合成 API</cite>](https://www.sitepoint.com/talking-web-pages-and-the-speech-synthesis-api/) 。

在本教程中，我们将在所学知识的基础上，开发一个使用该 API 定义的两个接口的演示。如果你需要 Web Speech API 的介绍，我推荐你阅读前面提到的两篇文章，因为这篇文章假设你对它有很好的了解。玩得开心！

## 开发互动形式

本文的目标是构建一个用户可以用自己的声音填充的交互式表单。为了这个例子，我们将开发一个注册表单，但是您可以将相同的概念应用到任何您想要的表单。要记住的一个重要概念是**语音的使用永远不应该是输入的唯一来源**因为无论语音识别器有多精确，它永远不会是完美的。因此，用户应该总是能够修改任何字段来修复识别器所犯的任何错误。

在这个演示中，我们将提供一个按钮，一旦点击，将开始向用户提问，然后用户说出答案，交互继续进行。识别器将语音转换为放在文本字段中的文本。一旦交互完成，这意味着我们的表单的所有字段都已被填充，我们的应用程序将礼貌地感谢用户。

最后一点，请记住，在撰写本文时，Web Speech API 还处于实验阶段，完全由 Chrome 提供支持。因此，我们的实验只能在这个浏览器中进行。事不宜迟，让我们开始构建注册表单的标记。

## 注册表单的 HTML

为了使事情尽可能简单，我们的表单将只包含三个字段，但是您可以根据需要添加任意多个字段。特别是，我们将要求我们的用户填写姓名、姓氏和国籍。如果你有 HTML 的基础知识，执行这个任务应该很容易。我建议你在看下面的代码之前尝试实现它(我的实现):

```
<form>
   <label for="form-demo-name">Name:</label>
   <input id="form-demo-name" />
   <label for="form-demo-surname">Surname:</label>
   <input id="form-demo-surname" />
   <label for="form-demo-nationality">Nationality:</label>
   <input id="form-demo-nationality" />
   <input id="form-demo-voice" type="submit" value="Start" />
</form>
```

前面的代码只显示了一个只能使用键盘或类似输入设备填充的经典表单。因此，我们需要找到一种方法来指定我们要对表单中定义的每个字段提出的问题。一个好的简单的解决方案是使用 HTML5 的`data-*`属性。特别是，我们将为每个`label`–`input`对指定一个`data-question`属性。我已经决定将属性设置为与`input`相关联的`label`，但是您可以很容易地更改演示来定义`input`元素的属性。

生成的代码如下所示:

```
<form>
   <label for="form-demo-name" data-question="What's your name?">Name:</label>
   <input id="form-demo-name" />
   <label for="form-demo-surname" data-question="What's your surname?">Surname:</label>
   <input id="form-demo-surname" />
   <label for="form-demo-nationality" data-question="What's your nationality?">Nationality:</label>
   <input id="form-demo-nationality" />
   <input id="form-demo-voice" type="submit" value="Start" />
</form>
```

无论您是否感到惊讶，这就是我们创建交互表单所需的所有标记。现在让我们通过讨论 JavaScript 代码来深入我们演示的核心。

## 添加业务逻辑

为了开发表单的业务逻辑，我们需要三个组件:语音合成器、语音识别器和**承诺**。我们需要一个语音合成器来发出声音，询问用户我们使用`data-question`属性定义的问题。语音识别器用于将用户的响应转换为文本，该文本将被设置为每个字段的值。最后，我们需要承诺<q>避免回调地狱！</q>。

WebSpeech API 是由异步操作驱动的，所以我们需要一种方法来同步所有的操作。我们需要在问题被问完之后开始识别用户的语音*，并且我们必须在用户说出答案*之后问一个新的问题*，并且识别器已经完成了它的工作。因此，我们需要同步一组可变的连续(串行)异步操作。通过在代码中采用承诺，我们可以很容易地解决这个问题。如果你需要什么是承诺的初级读本，SitePoint 已经为你提供了文章[<cite>JavaScript 承诺概述</cite>](https://www.sitepoint.com/overview-javascript-promises/) 。杰克·阿奇博尔德写了另一篇非常好的文章，标题是 [<cite>JavaScript Promises:去而复返</cite>](http://www.html5rocks.com/en/tutorials/es6/promises/) 。*

我们的代码将在逻辑上分为两部分:一个支持库，它处理 Web 语音 API 并充当承诺的生产者，另一个代码将消费承诺。在本文的下两节中，我们将讨论它们。

### 开发支持库

如果你对 Web Speech API 的工作原理有所了解，理解支持库就不会很难。

我们将定义一个对象文字，并将其赋给一个名为`Speech`的变量。这个对象有两个方法:`speak`和`recognize`。前者接受文本来说话，并将负责发出音频，还创建与此操作相关的承诺。如果没有错误发生(`error`事件)，承诺将被解决；如果`error`事件被触发，承诺将被拒绝。如果浏览器不支持 API，该承诺也将被拒绝。`recognize`方法用于识别用户的语音。它不接受任何参数，并通过将文本传递给所创建承诺的 resolve 方法来返回已识别的文本。正如你将看到的,`recognize`比`speak`稍微复杂一些，因为它需要处理更多的情况。由`recognize`创建的承诺将在最终结果可用时解决，或者在出现任何错误的情况下被拒绝。请注意，代码还将负责处理[一个我几天前在 Windows 8.1 上发现的问题(#428873)](https://code.google.com/p/chromium/issues/detail?id=428873) 。

我们支持库的完整代码如下所示:

```
var Speech = {
   speak: function(text) {
      return new Promise(function(resolve, reject) {
         if (!SpeechSynthesisUtterance) {
            reject('API not supported');
         }

         var utterance = new SpeechSynthesisUtterance(text);

         utterance.addEventListener('end', function() {
            console.log('Synthesizing completed');
            resolve();
         });

         utterance.addEventListener('error', function (event) {
            console.log('Synthesizing error');
            reject('An error has occurred while speaking: ' + event.error);
         });

         console.log('Synthesizing the text: ' + text);
         speechSynthesis.speak(utterance);
      });
   },
   recognize: function() {
      return new Promise(function(resolve, reject) {
         var SpeechRecognition = SpeechRecognition        ||
                                 webkitSpeechRecognition  ||
                                 null;

         if (SpeechRecognition === null) {
            reject('API not supported');
         }

         var recognizer = new SpeechRecognition();

         recognizer.addEventListener('result', function (event) {
            console.log('Recognition completed');
            for (var i = event.resultIndex; i < event.results.length; i++) {
               if (event.results[i].isFinal) {
                  resolve(event.results[i][0].transcript);
               }
            }
         });

         recognizer.addEventListener('error', function (event) {
            console.log('Recognition error');
            reject('An error has occurred while recognizing: ' + event.error);
         });

         recognizer.addEventListener('nomatch', function (event) {
            console.log('Recognition ended because of nomatch');
            reject('Error: sorry but I could not find a match');
         });

         recognizer.addEventListener('end', function (event) {
            console.log('Recognition ended');
            // If the Promise isn't resolved or rejected at this point
            // the demo is running on Chrome and Windows 8.1 (issue #428873).
            reject('Error: sorry but I could not recognize your speech');
         });

         console.log('Recognition started');
         recognizer.start();
      });
   }
};
```

### 把所有的碎片放在一起

有了我们的支持库，我们需要编写代码来检索我们指定的问题，并与支持库交互以创建交互表单。

我们需要做的第一件事是检索表单的所有`label` s，因为我们将使用它们的`for`属性来检索`input` s 和`data-question`属性来提问。该操作由下面的语句执行:

```
var fieldLabels = [].slice.call(document.querySelectorAll('label'));
```

回想一下我们是如何编写标记的，我们可以通过保持`label`–`input`对(即问答对)的耦合来缩短必要的代码。我们可以通过使用一个叫做`formData`的支持函数来做到这一点。它的目标是返回每一对`label`—`input`产生的新承诺。将表单中的每一个`label`和`input`视为一个独特的组件，而不是不同的实体，这允许我们减少所需的代码，因为我们可以提取一个更抽象的代码并遍历它们。

`formData`函数的代码及其调用方式如下所示:

```
function formData(i) {
   return promise.then(function() {
              return Speech.speak(fieldLabels[i].dataset.question);
           })
           .then(function() {
              return Speech.recognize().then(function(text) {
                  document.getElementById(fieldLabels[i].getAttribute('for')).value = text;
              });
           });
}

for(var i = 0; i < fieldLabels.length; i++) {
   promise = formData(i);
}
```

因为我们已经耦合了承诺，如`formData`函数所示，所以我们需要一个初始承诺，该承诺被解析以允许其他承诺开始。这个任务是通过创建一个在前面代码片段的循环之前立即解决的承诺来实现的:

```
var promise = new Promise(function(resolve) {
   resolve();
});
```

最后，我们要感谢您，我们的用户，但也要抓住我们的过程中可能产生的任何错误:

```
promise.then(function() {
   return Speech.speak('Thank you for filling the form!');
})
.catch(function(error) {
  alert(error);
});
```

至此，我们的代码差不多完成了。最后一步是将这一部分的所有代码放在用户单击按钮时执行的函数中。

### 结果呢

正如你注意到的，我没有讨论这个演示的风格，因为它完全不相关，你可以自由地写你自己的。作为附加说明，我还想提到，在下面的演示中，我还创建了一个简单的微调器，以便在识别器准备好完成工作时提供视觉反馈。

代码开发的结果如下所示，但它也可以作为 JSBin 获得:

[表单演示](http://jsbin.com/faguji/1/embed?html,css,js,output)