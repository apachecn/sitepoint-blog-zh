# 用 SoundManager 2 创建一个简单的音频播放器

> 原文：<https://www.sitepoint.com/creating-audio-player-soundmanager/>

HTML5 中引入的两个非常有用的特性是`audio`和`video`元素，它们允许音频和视频在浏览器中本地播放。在这一创新之前，想要在网站上提供视频或音频的人不得不依赖 Flash 等第三方软件。

幸运的是，`audio`和`video`已经解决了这个问题，至少*部分解决了*。我说部分是因为两个主要原因。首先，并不是所有要求开发人员支持的浏览器都实现了这些元素，比如一些旧的移动浏览器和旧版本的 Internet Explorer。第二个原因是浏览器还没有就实现的格式达成一致，所以今天我们有不同的浏览器支持不同的格式。这是由于“政治”原因而不是技术原因，但无论是什么原因，我们都必须面对这两个问题。

记住这一点，如果你想在一个网站中使用这些元素中的任何一个，你不仅要为你想播放的每个音频或视频提供不同的格式，而且你还需要为不支持`audio`和`video`的浏览器提供一个后备。为了实现这一目标，您可以使用本机方法，然后安装一个库，为您提供一个具有自己的功能和 API 的基于 Flash 的播放器，或者您可以使用一个 all-in-one 库，该库公开相同的 API，并根据浏览器的功能提供本机解决方案或基于 Flash 的解决方案。

在本文中，我们将通过讨论一个名为 SoundManager 2 的一体化库来研究第二种选择。

## 什么是 SoundManager 2？

正如在 SoundManager 2 网站上所描述的，这个库<q>在单个 JavaScript API 下提供简单、可靠的跨平台音频。</q> SoundManager 2 兼容的浏览器数量惊人，我敢打赌，你必须支持所有这些浏览器。为了让你有个概念，下面是测试过的浏览器和平台列表:

*   Firefox(所有版本)，Windows/Mac
*   Safari 1.3+ (Mac) /所有 Windows 版本
*   移动 Webkit: iOS 4.0 设备、iPad 3.2(最初的 iPad iOS 版本)和更新版本
*   安卓(2.3+，2.3.3 确认。)
*   谷歌浏览器(所有版本/操作系统)
*   Internet Explorer 5.0+和 Windows
*   Opera 9.10(略有漏洞，9.5+理想)，Windows/Mac
*   网景 8.0 以上，Windows/Mac
*   火狐 1.5+，Linux (Flash 9 beta)。

你能相信吗？甚至支持 Internet Explorer 5！

SoundManager 2 包装并扩展了 HTML 音频和 Flash 音频 API，为 JavaScript 提供了一个统一的声音 API。无论使用何种技术来播放声音，API 都是一致的。

既然您已经了解了这个库是什么以及为什么您可能想要使用它，我就不列出可用的方法和属性了，而是想指导您创建一个使用 SoundManager 2 开发的小项目。

## 用 SoundManager 2 创建一个简单的音频播放器

在这一节中，我们将在 SoundManager 2 的支持下，使用 HTML、CSS 和 JavaScript 开发一个简单而实用的音频播放器。为了让事情尽可能简单，我们的播放器将允许用户播放给定的音频文件，我们将硬编码到源代码中。您可以很容易地修改源，以允许用户选择他们想要播放的音频，也许可以使用选择菜单。

事不宜迟，让我们开始编写支持我们的播放器的标记。

### 加价

我们的播放器使用户能够:

*   播放和停止音频文件
*   暂停和继续播放音频文件
*   调高和调低音量
*   将音频文件的当前位置前后移动 30 秒
*   知道文件音频的持续时间和从它开始以来经过的时间
*   知道当前的音量。

基于这个列表，你可以看到我们需要六个按钮:播放/暂停/继续、停止、音量调高、音量调低、后退和前进。第一个按钮“播放”根据音频文件的状态执行各种任务。如果音频文件正在播放，按钮允许用户暂停音频；如果音频文件暂停，该按钮允许音频继续播放；如果音频文件已停止或尚未播放，该按钮允许从头开始播放音频。每个按钮都将与一个图标相关联，这样我们的理想用户将会有一个愉快的体验。

除了按钮之外，我们还需要三个元素来显示经过的时间、音频文件的总持续时间和播放器的音量(初始化为 100)。

这些要求的可能实现如下所示:

```
<div class="player">
   <div class="player__audio-info">
      <div>
         Played
         <span class="player__time-elapsed">-</span> of
         <span class="player__time-total">-</span>
         <button class="player__previous button button--small">Move back</button>
         <button class="player__next button button--small">Move forth</button>
      </div>
      <div>
         Volume: <span class="player__volume-info">100</span>
         <button class="player__volume-down button button--small">Volume down</button>
         <button class="player__volume-up button button--small">Volume up</button>
      </div>
   </div>
   <button class="player__play button button--large">Play</button>
   <button class="player__stop button button--large">Stop</button>
</div>
```

如您所见，我使用了 [BEM 符号](http://bem.info/)来命名用于样式化标记元素的类。你们中的一些人可能会说，对于这样一个简单的例子来说，这太夸张了。虽然这肯定是真的，但我坚信好习惯始于使用一项技术或一种简单示例的方法，然后在此基础上构建。我的观点是，对于一个刚开始学习的人来说，从一个复杂的项目开始并不理想。这解释了为什么我在这个项目中使用它。

除了 BEM 符号，您可能已经注意到我使用了`button`元素来标记按钮。这似乎很明显，但事实并非如此。在这种情况下，许多开发人员会使用`a`元素或【this 但是`a`元素应该引导用户到某个地方，而`button`是当一个元素*应该做*某事时使用的元素。我们的玩家需要做些什么。

现在我们已经有了标记，让我们来设计它的样式。

### 添加一点风格

我会保持这个项目非常简单的风格。该播放器将有一个灰色的背景，和一个黑色的边界，以突出其边界。我还将“重置”默认的`button`样式，使它们看起来不像典型的按钮，而是显示相关的图标。最后，为了从“播放”按钮切换到“暂停”按钮，我将创建一个`is-playing`类，通过改变显示的图标来清楚地标记音频文件的状态。

我们球员的完整风格如下所示:

```
.player
{
   display: inline-block;
   width: 300px;
   padding: 5px;
   background-color: #E3E3E3;
   border: 1px solid #000000;
}

.player span
{
   font-weight: bold;
}

.button
{
   text-indent: 200%;
   white-space: nowrap;
   overflow: hidden;
   border: none;
   padding: 0;
   background: rgba(255,255,255,0);
   cursor: pointer;
   vertical-align: bottom;
}

.button--small
{
   width: 19px;
   height: 19px;
}

.button--large
{
   width: 48px;
   height: 48px;
}

.player__audio-info
{
   padding-bottom: 5px;
   border-bottom: 1px dotted #000000;
}

.player__audio-info div + div
{
   margin-top: 10px;
}

.player__volume-info
{
   display: inline-block;
   width: 1.5em;
}

.player__play
{
   background-image: url("http://i60.tinypic.com/14mbep2.png");
}

.player__play.is-playing
{
   background-image: url("http://i57.tinypic.com/idyhd2.png");
}

.player__stop
{
   background-image: url("http://i61.tinypic.com/35mehdz.png");
}

.player__previous
{
   background-image: url("http://i60.tinypic.com/sdihc5.png");
}

.player__next
{
   background-image: url("http://i57.tinypic.com/2s1nm77.png");
}

.player__volume-down
{
   background-image: url("http://i60.tinypic.com/331nom0.png");
}

.player__volume-up
{
   background-image: url("http://i60.tinypic.com/ekkc1t.png");
}
```

### 发展行为

我们终于到达了项目的核心，业务逻辑。正如您将看到的，它并不复杂，但是为了拥有更易维护的代码，我们将创建一个名为`formatMilliseconds`的支持函数和一个名为`player`的对象。顾名思义，该函数用于将给定的毫秒数转换为字符串。更具体地说，该字符串将被格式化为“H:MM:SS:mmm ”,因为我们将使用它来显示音频文件的总持续时间和经过的时间。对象`player`将用于存储播放器的元素，这样我们就不必每次都检索它们。这允许我们提高项目的性能。最后，我们将使用一个`audio`变量来存储表示我们的音频文件的对象实例，该对象是使用 SoundManager 2 创建的。

我们刚刚描述的函数和变量如下所示:

```
function formatMilliseconds(milliseconds) {
   var hours = Math.floor(milliseconds / 3600000);
   milliseconds = milliseconds % 3600000;
   var minutes = Math.floor(milliseconds / 60000);
   milliseconds = milliseconds % 60000;
   var seconds = Math.floor(milliseconds / 1000);
   milliseconds = Math.floor(milliseconds % 1000);

   return (hours > 0 ? hours : '0') + ':' +
      (minutes < 10 ? '0' : '') + minutes + ':' +
      (seconds < 10 ? '0' : '') + seconds + ':' +
      (milliseconds < 100 ? '0' : '') + (milliseconds < 10 ? '0' : '') + milliseconds;
}

var player = {
   btnPlay: document.querySelector('.player__play'),
   btnStop: document.querySelector('.player__stop'),
   btnPrevious: document.querySelector('.player__previous'),
   btnNext: document.querySelector('.player__next'),
   btnVolumeDown: document.querySelector('.player__volume-down'),
   btnVolumeUp: document.querySelector('.player__volume-up'),
   timeElapsed: document.querySelector('.player__time-elapsed'),
   timeTotal: document.querySelector('.player__time-total'),
   volume: document.querySelector('.player__volume-info')
};
var audio = null;
```

此时，我们必须创建一个新的对象来表示我们的音频文件，这意味着我们必须给我们的`audio`变量赋值。我们将通过使用库提供的 [`createSound()`](http://www.schillmania.com/projects/soundmanager2/doc/#soundmanager-createsound) 方法来实现。它允许我们定义几个属性，但最重要的是`id`，它为音频文件分配一个标识符，以及`url`，在这里您可以设置音频文件的 URL。

该对象的创建是在一个匿名函数中执行的，该函数在库的 [`ready`](http://www.schillmania.com/projects/soundmanager2/doc/#soundmanager-onready) 事件被触发时执行，这意味着库已经执行了所有的操作，可以使用了。我们可以通过向 [`setup()`](http://www.schillmania.com/projects/soundmanager2/doc/#soundmanager-setup) 方法传递一个对象文字来指定当`ready`事件被触发时做什么，以及其他设置。

这也是你应该指向 SoundManager 2 的 Flash 播放器的地方，作为一个后备。它在下面的代码中设置:

```
soundManager.setup({
   useFastPolling: true,
   useHighPerformance: true,
   onready: function() {
      audio = soundManager.createSound({
         id: 'audio',
         url: 'http://freshly-ground.com/data/audio/mpc/20090119%20-%20Untitled%20Groove.mp3',
         whileloading: function() {
            player.timeTotal.textContent = formatMilliseconds(audio.durationEstimate);
         },
         whileplaying: function() {
            player.timeElapsed.textContent = formatMilliseconds(audio.position);
         },
         onload: function() {
            player.timeTotal.textContent = formatMilliseconds(audio.duration);
         },
         onfinish: function() {
            var event;
            try {
               // Internet Explorer does not like this statement
               event = new Event('click');
            } catch (ex) {
               event = document.createEvent('MouseEvent');
               event.initEvent('click', true, false);
            }
            player.btnStop.dispatchEvent(event);
         }
      });
   }
});
```

一旦我们实例化了表示音频文件的对象，我们必须为播放器的每个按钮添加一个事件监听器。这就是我们的`player`对象发挥作用的地方。使用它，我们可以引用播放器的按钮和其他元素，而不必每次都执行新的选择。这也是 SoundManager 2 库展示其易用性的地方。比如说你要播放音频:你觉得库暴露了什么方法？`play()`当然是啦！如果我们想停止音频呢？为此我们有了`stop()`。现在，如果我们想知道音频文件是否暂停了呢？该库提供了一个名为`paused`的布尔属性。对于总持续时间，我们有一个`duration`属性。非常容易，不是吗？

要改变音量和移动音频的当前位置，我们有两种方法:`setVolume()`和`setPosition()`。它们中的每一个都接受一个数字来更新您想要更改的值。例如，如果您想将音量设置为 50(范围从 0 到 100)，您可以写:

```
audio.setVolume(50);
```

如果您想将位置从开始移动到 10 秒，您可以写:

```
audio.setPosition(10000);
```

提供的值是 10000，因为该方法接受毫秒。

实现我们已经描述的特性的代码的剩余部分如下所示:

```
player.btnPlay.addEventListener('click', function() {
   if (audio === null) {
     return;
   }

   if (audio.playState === 0 || audio.paused === true) {
     audio.play();
     this.classList.add('is-playing');
   } else {
     audio.pause();
     this.classList.remove('is-playing');
   }
});

player.btnStop.addEventListener('click', function() {
   if (audio === null) {
     return;
   }

   audio.stop();
   document.querySelector('.player__time-elapsed').textContent = formatMilliseconds(0);
   player.btnPlay.classList.remove('is-playing');
});

player.btnVolumeDown.addEventListener('click', function() {
   if (audio === null) {
     return;
   }

   var volume = audio.volume - 10 < 0 ? 0 : audio.volume - 10;
   audio.setVolume(volume);
   player.volume.textContent = volume;
});

player.btnVolumeUp.addEventListener('click', function() {
   if (audio === null) {
     return;
   }

   var volume = audio.volume + 10 > 100 ? 100 : audio.volume + 10;
   audio.setVolume(volume);
   player.volume.textContent = volume;
});

player.btnPrevious.addEventListener('click', function() {
   if (audio === null) {
     return;
   }

   var position = audio.position - 30000 < 0 ? 0 : audio.position - 30000;
   audio.setPosition(position);
   player.timeElapsed.textContent = formatMilliseconds(audio.position);
});

player.btnNext.addEventListener('click', function() {
   if (audio === null) {
     return;
   }

   var position = audio.position + 30000 > audio.duration ? audio.duration : audio.position + 30000;
   if (position === audio.duration) {
      var event;
      try {
         // Internet Explorer does not like this statement
         event = new Event('click');
      } catch (ex) {
         event = document.createEvent('MouseEvent');
         event.initEvent('click', true, false);
      }
      player.btnStop.dispatchEvent(event);
   } else {
      audio.setPosition(position);
      player.timeElapsed.textContent = formatMilliseconds(audio.position);   
   }
});
```

### 结果呢

我们已经完成了我们的任务，但在我们看到播放器运行之前，我们必须包括 SoundManager 2 库。你可以从 [SoundManager 2 网站](http://www.schillmania.com/projects/soundmanager2/)或者从 CDN 下载该库及其所有文件。

请记住，为了将基于 Flash 的播放器作为后备，您必须包含 SoundManager 2 库附带的 SWF 文件。一旦你做到了这一点，你就可以看到球员的生活。

我们项目的结果如下图所示:

[https://jsfiddle.net/nbu9qghe/embedded/](https://jsfiddle.net/nbu9qghe/embedded/)

## 结论

在本教程中，我描述了 sound manager 2——一个库，它允许您使用一组独特的 API 来处理支持`audio`元素及其 API 的浏览器和不支持的浏览器。如您所见，SoundManager 2 支持数量惊人的浏览器(包括 Internet Explorer 5！)，因此您可以在项目中可靠地使用它。

我们通过创建一个简单的播放器将 SoundManager 2 的一些方法付诸实践，该播放器可以执行基本任务，如播放和暂停音频文件、修改音量以及来回移动音频。如果你想了解更多关于 [SoundManager 2](http://www.schillmania.com/projects/soundmanager2/) 的信息，我建议你阅读[的大量文档](http://www.schillmania.com/projects/soundmanager2/doc/)。我希望你喜欢这个库和演示，并和我们分享你的观点。

## 分享这篇文章