# 用 Web Speech API 制作一个声控音频播放器

> 原文：<https://www.sitepoint.com/voice-controlled-audio-player-web-speech-api/>

*这篇文章由[埃德温·雷诺索](https://www.sitepoint.com/author/ereynoso/)和[马克·布朗](http://twitter.com/markbrown4)进行了同行评审。感谢 SitePoint 的所有同行评审员使 SitePoint 的内容尽可能做到最好！*

[Web 语音 API](https://dvcs.w3.org/hg/speech-api/raw-file/tip/speechapi.html) 是一个 JavaScript API，使 Web 开发人员能够将语音识别和合成集成到他们的网页中。

这样做有很多原因。例如，增强残障人士的体验(特别是视力有问题的用户，或移动手能力有限的用户)，或者允许用户在执行不同任务(如驾驶)时与 web 应用程序进行交互。

如果你从未听说过网络语音 API，或者你想快速入门，那么阅读奥雷里奥·德·罗萨的文章[介绍网络语音 API](https://www.sitepoint.com/introducing-web-speech-api/) 、[语音合成 API](https://www.sitepoint.com/talking-web-pages-and-the-speech-synthesis-api/) 和[谈话形式](https://www.sitepoint.com/experimenting-web-speech-api/)可能是个好主意。

## 浏览器支持

浏览器厂商直到最近才开始实现[语音识别 API](http://caniuse.com/#feat=speech-recognition) 和[语音合成 API](http://caniuse.com/#feat=speech-synthesis) 正如你所看到的，对这些的支持还远远不够完善，所以如果你跟随这个教程，请使用合适的浏览器。

此外，语音识别 API 目前需要互联网连接，因为语音通过网络传递，结果返回给浏览器。如果连接使用 HTTP，用户必须允许站点在每次请求时使用他们的麦克风。如果连接使用 HTTPS，那么这只需要一次。

## 语音识别库

库可以帮助我们管理复杂性，并确保我们保持向前兼容。例如，当另一个浏览器开始支持语音识别 API 时，我们不必担心添加供应商前缀。

一个这样的库是 [Annyang](https://www.talater.com/annyang/) ，它非常容易使用。 *[多说说](#)* 。

为了初始化 Annyang，我们将他们的脚本添加到我们的网站:

```
<script src="//cdnjs.cloudflare.com/ajax/libs/annyang/1.6.0/annyang.min.js"></script> 
```

我们可以像这样检查 API 是否受支持:

```
if (annyang) { /*logic */ } 
```

并使用对象添加命令，将命令名作为键，将回调作为方法。：

```
var commands = {
  'show divs': function() {
    $('div').show();
  },
  'show forms': function() {
    $("form").show();
  }
}; 
```

最后，我们只需添加它们，并使用以下命令开始语音识别:

```
annyang.addCommands(commands);
annyang.start(); 
```

## 声控音频播放器

在本文中，我们将构建一个语音控制的音频播放器。我们将同时使用语音合成 API(通知用户哪首歌曲正在开始，或者命令未被识别)和语音识别 API(将语音命令转换为触发不同应用程序逻辑的字符串)。

使用 Web Speech API 的音频播放器的优点是，用户可以在浏览器中浏览其他页面，或者最小化浏览器并做其他事情，同时仍然可以在歌曲之间切换。如果我们在播放列表中有很多歌曲，我们甚至可以请求一首特定的歌曲，而无需手动搜索(当然，如果我们知道它的名字或歌手)。

我们不会依赖第三方库来进行语音识别，因为我们想展示如何在不增加项目依赖性的情况下使用 API。声控音频播放器将只支持支持 [interimResults 属性](https://dvcs.w3.org/hg/speech-api/raw-file/tip/speechapi.html#dfn-interimresults)的浏览器。最新版本的 Chrome 应该是一个安全的赌注。

和以往一样，你可以在 GitHub 上找到[的完整代码，在 CodePen](https://github.com/sitepoint-editors/Voice-controlled-Audio-Player) 上找到[的演示。](http://codepen.io/SitePoint/pen/vNdKVp)

## 开始—播放列表

让我们从静态播放列表开始。它由一个数组中包含不同歌曲的对象组成。每首歌曲都是一个新对象，包含文件路径、歌手姓名和歌曲名称:

```
var data = {
  "songs": [
    {
      "fileName": "https://www.ruse-problem.org/songs/RunningWaters.mp3",
      "singer" : "Jason Shaw",
      "songName" : "Running Waters"
    },
    ... 
```

我们应该能够向`songs`数组添加一个新的对象，并让新歌自动包含到我们的音频播放器中。

## 音频播放器

现在我们来看播放器本身。这将是一个包含以下内容的对象:

*   一些设置数据
*   与用户界面相关的方法(例如，填充歌曲列表)
*   与语音 API 相关的方法(例如，识别和处理命令)
*   与音频操作相关的方法(如播放、暂停、停止、上一页、下一页)

### 设置数据

这是相对直接的。

```
var audioPlayer = {
  audioData: {
    currentSong: -1,
    songs: []
  }, 
```

`currentSong`属性指的是用户当前所在歌曲的索引。这很有用，例如，当我们必须播放下一首/上一首歌曲，或者停止/暂停歌曲时。

`songs`数组包含用户听过的所有歌曲。这意味着下次用户听同一首歌时，我们可以从数组中加载它，而不必下载它。

你可以在这里看到[的完整代码](https://github.com/sitepoint-editors/Voice-controlled-Audio-Player/blob/master/js/playlist.js)。

### 用户界面方法

UI 将由可用命令列表、可用轨道列表和上下文框组成，以通知用户当前操作和前一个命令。我不会详细讨论 UI 方法，而是提供一个简要的概述。你可以在这里找到这些方法的代码。

#### 负荷

这将遍历我们之前声明的播放列表，并将歌曲名和艺术家名添加到可用曲目列表中。

#### changeCurrentSongEffect

这表示当前正在播放哪首歌曲(通过将其标记为绿色并在旁边添加一对耳机)以及那些已经结束播放的歌曲。

#### 播放歌曲

这向用户指示歌曲正在播放，或者何时结束。这是通过`changeStatusCode`方法实现的，该方法将该信息添加到盒子中，并通过语音 API 通知用户这一变化。

#### 变更状态代码

如上所述，这更新了上下文框中的状态消息(例如，指示正在播放一首新歌),并利用`speak`方法向用户通告这一变化。

#### changeLastCommand

更新最后一个命令框的小助手。

#### 陀螺旋转器

隐藏或显示微调图标的小助手(它向用户指示他的语音命令当前正在处理)。

### 玩家方法

播放器将负责你可能期望的事情，即:开始、停止和暂停播放，以及在曲目中前后移动。同样，我不想详细讨论这些方法，而是想把你引向我们的 GitHub repo。

#### 玩

这将检查用户是否已经听过一首歌曲。如果没有，它就开始播放歌曲，否则它就调用我们之前讨论过的关于当前缓存的歌曲的`playSong`方法。它位于`audioData.songs`中，对应于`currentSong`索引。

#### 鲍赛松

这将暂停或完全停止(将回放时间返回到歌曲的开头)一首歌曲，具体取决于作为第二个参数传递的内容。它还更新状态代码，通知用户歌曲已经停止或暂停。

#### 停止

这会根据歌曲的第一个也是唯一的参数暂停或停止歌曲:

#### 上一个

这将检查前一首歌曲是否被缓存，如果是，它将暂停当前歌曲，递减`currentSong`并再次播放当前歌曲。如果新歌曲不在数组中，它也这样做，但是它首先从对应于递减的`currentSong`索引的文件名/路径加载歌曲。

#### 然后

如果用户以前听过一首歌，这个方法会尝试暂停它。如果我们的`data`对象(即我们的播放列表)中有下一首歌曲，它会加载并播放它。如果没有下一首歌曲，它只是改变状态码，并通知用户他们已经到达最后一首歌曲。

#### searchSpecificSong

它将一个关键字作为参数，在播放第一个匹配项之前，对歌曲名称和艺术家进行线性搜索。

### 语音 API 方法

Speech API 非常容易实现。事实上，只需要两行代码就可以让 web 应用程序与用户对话:

```
var utterance = new SpeechSynthesisUtterance('Hello');
window.speechSynthesis.speak(utterance); 
```

我们在这里所做的是创建一个`utterance`对象，其中包含我们希望朗读的文本。`speechSynthesis`接口(在窗口对象上可用)负责处理这个`utterance`对象并控制结果语音的回放。

请在您的浏览器中尝试一下。就这么简单！

#### 说话

我们可以在我们的`speak`方法中看到这一点，该方法大声读出作为参数传递的消息:

```
speak: function(text, scope) {
  var message = new SpeechSynthesisUtterance(text.replace("-", " "));
  message.rate = 1;
  window.speechSynthesis.speak(message);
  if (scope) {
    message.onend = function() {
      scope.play();
    }
  }
}
```

如果有第二个参数(`scope`)，我们在消息播放完毕后调用`scope`(可能是一个音频对象)上的 play 方法。

#### 过程命令

这种方法并不令人兴奋。它接收命令作为参数，并调用适当的方法来响应它。它检查用户是否想要用正则表达式播放特定的歌曲，否则，它输入 switch 语句来测试不同的命令。如果没有一个与收到的命令相对应，它会通知用户该命令未被理解。

你可以在这里找到它的代码。

## 把东西绑在一起

到目前为止，我们已经有了一个代表播放列表的`data`对象，以及一个代表播放器本身的`audioPlayer`对象。现在我们需要编写一些代码来识别和处理用户输入。请注意，这只适用于 webkit 浏览器。

让用户与你的应用对话的代码和以前一样简单:

```
var recognition = new webkitSpeechRecognition();
recognition.onresult = function(event) {
  console.log(event)
}
recognition.start(); 
```

这将邀请用户允许页面访问他们的麦克风。如果您允许访问，您可以开始讲话，当您停止讲话时,`onresult`事件将被触发，使得语音捕获的结果作为 JavaScript 对象可用。

参考:[html 5 语音识别 API](http://shapeshed.com/html5-speech-recognition-api/)

我们可以在应用程序中实现这一点，如下所示:

```
if (window['webkitSpeechRecognition']) {
  var speechRecognizer = new webkitSpeechRecognition();

  // Recognition will not end when user stops speaking
  speechRecognizer.continuous = true;

  // Process the request while the user is speaking
  speechRecognizer.interimResults = true;

  // Account for accent
  speechRecognizer.lang = "en-US";

  speechRecognizer.onresult = function (evt) { ... }
  speechRecognizer.onend = function () { ... }
  speechRecognizer.start();
} else {
  alert("Your browser does not support the Web Speech API");
} 
```

如你所见，我们测试了`window`对象上`webkitSpeechRecognition`的存在。如果有，那么我们就可以开始了，否则我们会通知用户浏览器不支持它。如果一切顺利，我们就设置几个选项。在这些`lang`中，有一个很有趣的例子，它可以根据你来自哪里来改进识别结果。

然后，在使用 start 方法开始之前，我们为`onresult`和`onend`事件声明处理程序。

### 处理结果

当语音识别器得到结果时，至少在语音识别的当前实现和我们的需求的上下文中，我们想要做一些事情。每次有结果时，我们希望将它保存在一个数组中，并设置一个等待三秒钟的超时时间，这样浏览器就可以收集任何进一步的结果。三秒钟后，我们希望使用收集到的结果，并以相反的顺序遍历它们(较新的结果更有可能准确)，并检查识别出的脚本是否包含我们可用的命令之一。如果是，我们执行命令并重新启动语音识别。我们这样做是因为等待最终结果可能需要一分钟，这使得我们的音频播放器看起来没有反应，没有意义，因为只需点击一个按钮就可以更快。

```
speechRecognizer.onresult = function (evt) {
  audioPlayer.toggleSpinner(true);
  results.push(evt.results);
  if (!timeoutSet) {
    setTimeout(function() {
      timeoutSet = false;
      results.reverse();
      try {
        results.forEach(function (val, i) {
          var el = val[0][0].transcript.toLowerCase();
          if (currentCommands.indexOf(el.split(" ")[0]) !== -1) {
            speechRecognizer.abort();
            audioPlayer.processCommands(el);
            audioPlayer.toggleSpinner();
            results = [];
            throw new BreakLoopException;
          }
          if (i === 0) {
            audioPlayer.processCommands(el);
            speechRecognizer.abort();
            audioPlayer.toggleSpinner();
            results = [];
          }
        });
      }
      catch(e) {return e;}
    }, 3000)
  }
  timeoutSet = true;
} 
```

因为我们没有使用库，所以我们必须编写更多的代码来设置我们的语音识别器，循环遍历每个结果，并检查其副本是否与给定的关键字匹配。

最后，语音识别一结束，我们就重新启动:

```
speechRecognizer.onend = function () {
  speechRecognizer.start();
} 
```

你可以在这里看到这部分的完整代码。

仅此而已。我们现在有一个功能齐全的声控音频播放器。我强烈要求[从 Github](https://github.com/sitepoint-editors/Voice-controlled-Audio-Player) 下载代码并玩一玩，或者查看一下 [CodePen 演示](http://codepen.io/SitePoint/pen/vNdKVp)。我也提供了[一个通过 HTTPS](https://ruse-problem.org/audio-player/) 提供的版本。

## 结论

我希望这个实用教程已经成为一个关于 Web Speech API 的健康介绍。我认为，随着实现的稳定和新特性的增加，我们将会看到这个 API 的使用越来越多。例如，我看到了一个完全由语音控制的未来 YouTube，在那里我们可以观看不同用户的视频，播放特定的歌曲，只需通过语音命令就可以在歌曲之间切换。

Web Speech API 还可以在许多其他领域带来改进，或者开启新的可能性。例如，浏览电子邮件、浏览网站或搜索网页——所有这些都用您的声音来完成。

你在你的项目中使用这个 API 吗？我很乐意在下面的评论中听到你的意见。

## 分享这篇文章