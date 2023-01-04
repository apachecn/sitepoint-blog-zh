# 用香草 JavaScript 创建一个音乐果酱站

> 原文：<https://www.sitepoint.com/music-jam-station/>

*这篇文章由[克里斯·佩里](https://www.sitepoint.com/author/cperry)、[米凯拉·莱尔](https://www.sitepoint.com/author/mlehr)和[马特·伯内特](https://www.sitepoint.com/author/mburnett)进行了同行评审。感谢 SitePoint 的所有同行评审员使 SitePoint 的内容尽可能做到最好！*

HTML5 音频播放器已经产生了一些新的和令人兴奋的可能性，特别是当它涉及到音乐相关的网络应用。我希望通过向你介绍我是如何创建这个 [jam station](http://myguitarpal.com/jam-station) 的，向你介绍其中的一些可能性。这个项目最初是作为一个实验，但随着时间的推移演变成一个开放的实践和吉他演奏者的教学工具。

要学习本教程，对 Javascript 基础知识的扎实理解是必要的。

本着保持简洁的精神，我们将构建一个稍微简单一点的版本。这正是我们今天要做的。

通过[码笔](http://codepen.io)上的 SitePoint ( [@SitePoint](http://codepen.io/SitePoint) )看笔[卡位](https://codepen.io/SitePoint/pen/pbkdBk/)。

如果您对完全重新创建本教程不感兴趣，那也没关系。我在本教程中介绍的大部分内容适用于不同类型的音乐应用程序。

对音乐理论的基本理解将有助于你理解本教程。必须了解拍号、小节和节拍。

## 你需要什么

您选取的音频轨道需要录制到咔嗒声轨道或节拍器上。换句话说，每个节拍的长度需要在整个轨道上保持一致。

为了找到当前的节拍和小节，您需要了解一些关于音轨的事情。

*   每分钟节拍数(BPM 或节奏)
*   时间签名
*   赛道开始的时间
*   有多少措施用于计算(不总是适用)

如果你愿意，你可以使用我在 CodePen 演示中使用过的[音轨](http://myguitarpal.com/wp-content/uploads/2014/09/12-Bar-Blues-in-A-Version-1.mp3)(右击保存)。

这是那条赛道的相关数据。

*   BPM = 117
*   拍号= 4/4
*   第一拍的开始时间= 0.2s
*   用于 count in = 1 的度量

如果你正在使用自己的音轨，并且不确定其中的一些细节，像 [Audacity](http://www.audacityteam.org/) 这样的免费音频编辑器应该会有所帮助。它不能告诉你一切，但你可以找到第一次心跳的准确开始时间。如果您正在录制自己的曲目，最好从一开始就记下这些信息。

## 标记和 CSS

在进入有趣的东西之前，这里是我们将要使用的 HTML 和 CSS。

```
 <div class="wrapper">
   <div id="chord-diagram"></div>
   <audio id="jam-track"  src="https://myguitarpal.com/wp-content/uploads/2014/09/12-Bar-Blues-in-A-Version-1.mp3" controls></audio>
  <br>

  <label>Beat: </label>
  <div class="data" id="beat"></div>

  <label>Measure: </label>
  <div class="data" id="measure"></div>

  <label>Section: </label>
  <div class="data" id="section"></div>

  <label>Chord: </label>
  <div class="data" id="chord"></div>

  <div id="chord-progression"></div>
</div> 
```

```
.wrapper {
  max-width: 400px;
}

#chord-progression {
  margin-top: 20px;
  padding-top: 20px;
  border-top: 1px solid lightgray;
}

.section {
  margin-bottom: 20px;
  display: none;
}

.measure {
  width: 25%;
  display: inline-block;
}

.m-line {
  float: right;
  width: 10px;
}

audio {
  width: 100%;
}

.data {
  display: inline;
  margin-right: 10px;
}

label {
  font-weight: bold;
} 
```

## 设置变量

首先，我们将获取我们讨论过的所有跟踪数据，并设置一些变量。

```
// beats per minute
var BPM = 117;

// beats per second
var BPS = 60 / BPM;

// measures used for count in
var measuresCount = 1;

// time the track starts
var offsetSeconds = 0.2;

// time signature
var timeSigTop = 4;
var timeSigBottom = 4; 
```

## 和弦进行

出于本教程的目的，我将和弦进行数据模型保持得非常简单，并使用了多维数组。

顶层数组是和弦进行部分。这些可以是诗句，合唱，桥梁等。

嵌套数组在其各自的部分包含每个小节的和弦。

```
var sectionOne = ['A7', 'A7', 'A7', 'A7', 'D7', 'D7', 'A7', 'A7', 'E7', 'D7', 'A7', 'E7'];
var sectionTwo = ['A7', 'A7', 'A7', 'A7', 'D7', 'D7', 'A7', 'A7', 'E7', 'D7', 'A7', 'A7', 'A7', 'A7', 'A7', 'A7', 'A7', 'A7'];

var chordProgression = [
  sectionOne,
  sectionOne,
  sectionOne,
  sectionTwo
]; 
```

音乐经常重复某些部分，比如合唱部分，所以为了保持干爽，将每个部分如上所示存储在一个变量中，然后将这些部分包含在`chordProgression`数组中。

## 经营果酱站

这个果酱站使用[时间更新](https://developer.mozilla.org/en-US/docs/Web/Events/timeupdate)事件，类似于使用游戏循环。每次`timeupdate`事件触发时(曲目播放时每秒几次)，我们将运行一个函数并更新一些数据，如当前节拍、小节和和弦。只有当曲目暂停时，数据才会更新。

当`timeupdate`事件触发时，我们将运行`jamStation`函数。播放音频时，每秒钟将调用该函数几次。

```
var audio = document.getElementById('jam-track');

audio.ontimeupdate = function() {
  jamStation();
}; 
```

## 有趣的部分

这个函数应该只处理数据，而不是表示。我们稍后将使用另一个函数(`renderJamStation`)来处理表示。

为了找到当前的节拍，我们将使用表达式`(audio.currentTime - offsetSeconds + BPS) / BPS`，并将该值存储在变量`beat`中。

然后我们可以用表达式`(beat - 1) / timeSigTop`找到当前的度量，我们将把它存储在变量`measure`中。

```
function jamStation() {
  var beat = (audio.currentTime - offsetSeconds + BPS) / BPS; 
  var measure = (beat - 1) / timeSigTop;
} 
```

`beat`和`measure`变量现在应该向下取整。这将使我们在进行比较时更容易处理数字。然而，如果你要做一个更复杂的应用程序，需要使用几分之一拍，我不建议你这么做。

我们将这些整数存储在`cleanBeat`和`cleanMeasure`中。

如果你想在一个小节中显示当前的节拍，你可以使用下面的表达式:`((cleanBeat - 1) % timeSigTop) + 1`。假设我们在第 13 拍。表达式的值将会是:`((13 - 1) % 4) + 1`。我们加 1 是因为没有节拍 0 这种东西。

现在不是让节拍无限增加，而是只增加到拍号顶部的数字。`measureBeat`将包含小节内节拍的值。所以，`cleanMeasure`会数到 1，2，3，4，5，6，7，8 等。而`measureBeat`会数到 1，2，3，4，1，2，3，4 等等。

```
function jamStation() {
  var beat = (audio.currentTime - offsetSeconds + BPS) / BPS; 
  var measure = (beat - 1) / timeSigTop;

  // round down for beat and measure
  var cleanBeat = Math.floor(beat);
  cleanMeasure = Math.floor(measure);

  // find the current beat within the measure
  measureBeat = ((cleanBeat - 1) % timeSigTop) + 1;
} 
```

不要把`var`放在`cleanMeasure`前面，因为我们已经在函数外部声明了这个变量(参见[代码打开](https://codepen.io/SitePoint/pen/pbkdBk/?editors=1010)),因为我们想从`jamStation()`外部访问这个值。

此时，我们实际上已经获得了我们需要的最重要的数据。棘手的部分是如何处理和弦进行。

`jamStation`函数的下一部分用于确定两件事:T1 和 T2。

首先，我们需要使用 if…else 语句来测试我们是否已经过了第一个度量。如果是，我们将找到当前部分和当前和弦的值。如果不是，那么`currentSection`和`currentChord`将被设置为空。

为了找到当前部分和当前和弦，我们循环通过这些部分，然后运行嵌套循环，循环通过每个部分中的测量。

每当我们循环通过每个部分的测量时，变量`count`被设置并增加 1。现在，如果`cleanMeasure`等于`count`，我们知道我们已经找到了我们正在进行的部分和测量。因为我们已经找到了正确的部分，并测量了音轨当前所在的位置，所以我们需要存储这些值，并且我们需要中断两个循环，以便正确的数据不会在下次循环运行时被覆盖。

不要在`currentSection`或`currentChord`前使用`var`，因为这些变量已经在`jamStation()`外声明，所以我们可以在函数间共享它们。

```
function jamStation() {
  var beat = (audio.currentTime - offsetSeconds + BPS) / BPS; 
  var measure = (beat - 1) / timeSigTop;

  // round down for beat and measure
  var cleanBeat = Math.floor(beat);
  cleanMeasure = Math.floor(measure);

  // find the current beat within the measure
  measureBeat = ((cleanBeat - 1) % timeSigTop) + 1;

  if (cleanMeasure > 0) {
    // find the currentSection and currentChord
    var count = 0;
    var br = false;
      for (var s = 0; s < chordProgression.length; s++) {
        for (var m = 0; m < chordProgression[s].length; m++) {
          count++;
          if (cleanMeasure == count) {
            currentSection = s + 1;
            currentChord = chordProgression[s][m];
            br = true;
            break;
          }
        }
        if (br === true) {
          break;
        }
      }
  } else {
    currentSection = null;
    currentChord = null;
  }

  // display the jam station and its data
  renderJamStation();
} 
```

现在，我们拥有了所需的所有数据，并且可以在全球范围内访问。

在`jamStation`功能结束时，您可以看到`renderJamStation()`正在运行。该函数仅用于演示目的，我们稍后会介绍它。

## 渲染和弦进行

我们需要展示和弦进行。让我们将它包装在一个名为`renderChordProgression`的函数中，以保持整洁。该功能将仅运行一次，因为和弦进行数据从不更新。

首先我们循环和弦进行中的各个部分。每次循环时，我们创建一个 div，其类为“section”，id 为“section-[number]”。每个部分都有一个唯一的 id，所以我们可以在播放时显示特定的部分。

```
// take the chordProgression array and render the HTML
function renderChordProgression() {
  var progression = document.getElementById('chord-progression');

  // make the sections
  for (var s = 0; s < chordProgression.length; s++) {
    progression.innerHTML += '<div class="section" id="section-' + (s + 1) + '"></div>';
  }
} 
```

接下来，我们再次遍历这些部分，这样我们就可以在一个嵌套循环中遍历所有的度量。然后，每项措施将被纳入其各自的部分。

您可能注意到 count 变量被设置并递增。这样做是为了增加每个部分中的每个度量。

完整的函数如下所示:

```
// take the chordProgression array and render the HTML
function renderChordProgression(){
  var progression = document.getElementById('chord-progression');

  // make the sections
  for (var s = 0; s < chordProgression.length; s++){
    progression.innerHTML += '<div class="section" id="section-' + (s + 1) + '"></div>';
  }

  var count = 0;

  for (var s = 0; s < chordProgression.length; s++) {
    for (var m = 0; m < chordProgression[s].length; m++) {
      count++;
      var section = document.getElementById('section-' + (s + 1));
      section.innerHTML += '<div class="measure" id="measure-' + count + '">' 
                        + chordProgression[s][m] 
                        + '<div class="m-line">|</div></div>';
    }
  }
} 
```

您可能会想要设计和弦进行的风格。你可以参考这篇文章顶部代码栏中的 CSS。

## 介绍会；展示会

从函数`jamStation`内部调用函数`renderJamStation`。

```
function renderJamStation() {

  // show the beat within the measure, not overall
  document.getElementById('beat').innerHTML = measureBeat;

  // show the current measure, but only if the jam track is past the count in measures
  var measureElem = document.getElementById('measure');

  // only show the current measure if it's > 0
  if (cleanMeasure > 0) {
    measureElem.innerHTML = cleanMeasure;
  } else {
    measureElem.innerHTML = '';
  }

  // show the section number
  document.getElementById('section').innerHTML = currentSection;
  // show the current chord name
  document.getElementById('chord').innerHTML = currentChord;

  // hide all sections before displaying only the section we want to see
  var allSections = document.getElementsByClassName('section');

  for (var i = 0; i < allSections.length; i++) {
    allSections[i].style.display = 'none';
  }

  // show the currently playing section
  if (currentSection != null) {
    document.getElementById('section-' + currentSection).style.display = 'block';
  } else {
    allSections[0].style.display = 'block';
  }

  // style the current chord in the chord progression
  if (cleanMeasure > 0) {
    // style all measures black
    var measures = document.getElementsByClassName('measure');
    for (var i = 0; i < measures.length; i++) {
       measures[i].style.color = 'black';
    }
    // style current measure red
    document.getElementById('measure-' + cleanMeasure).style.color = 'red';
  }
} 
```

我不会涵盖这个函数的每个部分，因为如果您熟悉 JavaScript，它的大部分应该是不言自明的。在大多数情况下，它获取一些数据，如当前和弦和当前小节，并显示出来。

您应该密切关注的这个函数的主要部分是它如何显示正确的部分。

```
// hide all sections before displaying only the section we want to see
var allSections = document.getElementsByClassName('section');

for (var i = 0; i < allSections.length; i++) {
  allSections[i].style.display = 'none';
}

// show the currently playing section
if (currentSection != null) {
  document.getElementById('section-' + currentSection).style.display = 'block';
} else {
  allSections[0].style.display = 'block';
} 
```

你可以看到每个部分首先被隐藏。如果没有做到这一点，每个部分将出现时，它的时间准备好了，但没有播放的部分仍将可见。

为了显示正在播放的当前部分，我们通过 ID 选择它，并将其显示属性设置回 block。

如果`currentSection`为空，我们显示第一部分。如果我们不这样做，在播放曲目和到达应该看到该部分的时间点之前，第一部分将不可见。

## 呈现初始显示

最后，我们运行两个函数。`renderChordProgression()`需要运行一次以渲染和弦进行，而`jamStation()`也应该运行一次。是的，函数`jamStation()`会在`timeupdate`触发时运行，但它也应该自己运行一次，否则和弦进行一开始不会渲染。

```
renderChordProgression();
jamStation(); 
```

## 进一步的想法和观点

如果您对显示乐器的和弦图感兴趣，那么您已经有了许多所需的数据，最重要的是和弦名称和类型。

假设您想要在和弦进行位于适当和弦上时显示和弦图图像。我们已经将当前和弦存储在变量`currentChord`中。

您可以创建一个 chord 对象数组。

```
var chords = [
  {
    name: 'A',
    type: '7',
    src:img/a7.jpg',
  },
  {
    name: 'D',
    type: '7',
    src:img/d7.jpg',
  },
  {
    name: 'E',
    type: '7',
    src:img/e7.jpg',
  }
]; 
```

然后，在`jamStation`函数中运行这个逻辑，它将在正确的时间显示正确的和弦。

```
for (var i = 0; i < chords.length; i++) {
  if (chords[i].name + chords[i].type == currentChord) {
    document.getElementById('chord-diagram').innerHTML = '<img src="' + chords[i].src + '"/>';
  }
} 
```

## 结论

也许你不想完全按照我今天所讲的去做，这没关系。我可以想到很多方法将我在本文中介绍的内容应用到所有不同类型的项目中。这里有一些想法:

*   画廊或滑块的新观点。某些节拍或小节上的过渡图像。
*   将图像制作成有节奏的动画。跺脚？有人跳舞的姿势？
*   音频可视化。
*   与一首音乐同步的用于教学目的的注释或图表。
*   你可以用这个神奇的开源 JavaScript 音乐符号 API 做各种各样的事情

感谢你的阅读，我希望你能学到一些东西。如果这篇教程给了你一个很酷的想法，请在评论中告诉我！

## 分享这篇文章