# Flash 选集:酷炫效果和实用动作脚本-第 5 章:声音效果

> 原文：<https://www.sitepoint.com/flash-anthology-5-sound-effects/>

**可悲的是，Flash 项目中的声音通常是事后才被包括进来的——如果真的包括的话。匆忙的、最后一分钟的声音添加通常只不过是一个毫无意义的陈词滥调。如果你发现自己在一个项目中导入一个声音剪辑，却不清楚为什么要这么做，停下来重复 Flash 设计者的口头禅:*糟糕的声音使用会破坏整个用户体验*。**

在这个数字媒体时代，声音可以也应该成为网络环境的一部分。一点点声音对增强和巩固用户体验大有帮助。只要使用方法正确，即使是简单的“咔嗒”声也能提供有价值的听觉反馈。但是要小心，不要让你的项目“为了声音而声音”因此，声音的使用是一个平衡的问题。

请注意，如果您喜欢离线阅读此内容，您可以[下载 pdf 格式的本章内容](https://www.sitepoint.com/show-modal-popup-after-time-delay/)。

##### 什么时候应该在项目中使用声音？

这是一个黄金问题，答案取决于你正在开发的项目类型。一些作品在单独观看时不需要任何声音；然而，当合并到一个更大的项目中时，它们需要某种程度的听觉增强。如果一个项目与声音有很大关系，那么它肯定应该包含适当种类的音频剪辑。

永远站在用户的角度考虑你的工作:声音的加入会增加他们对项目的享受吗？包含或长或短的音乐循环、声音字节或叙述可以大大增加界面的吸引力，但也可能分散其他内容的注意力，过一会儿就会变得非常烦人。考虑以下可能的 Flash 项目示例，并思考声音可能对它们有益。你的选择可以包括音效、音乐循环、叙事，或者，当然，什么都不是。

*   循环动画
*   单级菜单系统
*   多级菜单系统
*   多页 Flash 网站
*   Flash 表单
*   流式视频内容

在脑子里快速浏览这些例子。不难看出，对于不同的项目，什么行得通，什么行不通。随着时间的推移，您会越来越擅长决定何时以及如何向项目添加声音。

##### 选择声音剪辑

数字声音的主要好处之一是它的灵活性。音频处理软件，如 Steinberg Wavelab，可以轻松地编辑、修改、循环和添加声音效果。您可以从一个音频循环中取出一个 10 秒钟的部分，粘贴到另一个音频循环中，或者跨多个轨道粘贴，与编辑 Word 文稿一样简单。现在可用的高级声音处理工具，结合麦克风和您自己的想象力，允许您创建大量的音频剪辑。如果您时间紧迫或需要额外的灵感，有许多在线资源可供您下载免费的剪辑或购买剪辑集以用于您的项目。

不应轻视声音在项目中的应用。如果效果想要成功，你需要注意许多因素——最显著的是时机、音量和构图。记住，你是在寻求增强你的视觉信息，而不是与之对抗，所以你对声音的选择很重要。在 Flash 表单的提交按钮上附加一个疯狂的技术循环，很可能会诱使访问者关闭浏览器。另一方面，如果你给用户一些微妙的反馈，告诉他们表单已经提交，他们很可能会对界面有信心，并继续交互。

如你所见，这一切都与上下文有关。将声音恰当地整合到项目中可以支持信息的高效传达。

##### 导入和导出声音剪辑

Flash MX 2004 提供了内置的声音编辑器，非常适合简单的编辑任务。然而，这个工具对于大多数需求来说太基础了:例如，它不允许你给你的声音选择添加效果。我绝对主张使用更强大的声音处理器，尤其是在修剪、复制和循环剪辑时。

您可以像导入任何其他媒体类型一样，从“文件”菜单导入声音剪辑。导入的声音可以直接应用于按钮和按钮状态(向上、向下、向上等)。)，通过流式外部文件合并，通过动作面板中的 ActionScripted 控件包含，或通过其他方式添加。

向按钮的按下状态添加“点击”声音来模拟真实世界的鼠标点击是基本声音效果的一个例子。《T2》不需要动作脚本知识，但它肯定可以通过脚本来创作。在下面的示例中，将必要的代码应用于名为`myButton`的按钮，该按钮引用在“库”面板中具有链接标识符`mySound`的声音剪辑:

```
myButton.onPress = function () 

{ 

  newSound = new Sound (); 

  newSound.attachSound ("mySound"); 

  newSound.start (); 

}
```

对于更高级的效果，包括流和抽象回放机制，我们需要看看 ActionScript `Sound`类的内部，看看我们如何从接口和代码条件中控制声音。这是一个令人兴奋的舞台，在这里你只需要一点点想象力就可以快速创造出有趣的戏剧效果。

由于这本书集中于通过 ActionScript 创建效果，我们不会讨论将声音效果集成到按钮和时间轴上的帧中。有关这方面的示例，请参考 Flash MX 2004 文档。

让我们从一个链接到动画的有趣的音量控件开始。

##### 动态音量控制

在下面的演示中，我们动态缩放电影剪辑的垂直高度，将与剪辑相关的声音音量与其高度联系起来。这是一个重要的例子，因为它构成了你将来创造的许多效果的基础。

要编辑效果的完整版本，请在代码归档中找到`volcontrol.fla`。
设置场景

1.  创建一个 500 像素宽、400 像素高的新 Flash 文档。将帧速率设置为 24 fps(修改>文档…)。

*   重命名默认图层操作，并在“库”面板中创建两个新文件夹:“影片剪辑”和“声音”。*   在影片剪辑文件夹中创建一个包含任意内容的新影片剪辑元件。代码归档中的示例包括一个静态文本区域，其中显示文本“sound effect”。*   将影片剪辑的一个实例拖到舞台上，并将其命名为`clipper`。*   选择文件>导入到库…并从代码存档中选择`reverb.mp3`，或者导入您选择的另一个声音文件。把它放在声音文件夹里。

这里使用的文件是一个中等质量的三秒钟的短片，主要是因为它是一个小文件。

*   从“库”面板中选择声音剪辑，右键单击并选择“链接...”。选中“导出 ActionScript”复选框，然后输入“混响”作为标识符。

添加了要为 ActionScript 导出制作动画和链接声音剪辑的元素后，我们现在可以添加控制代码了。

##### 添加动作脚本

*   选择动作层的第一帧，并将以下代码添加到动作面板中:

**例 5.1。`volcontrol.fla`行动:1(节选)**

```
MovieClip.prototype.WaveFade = function (minValue, maxValue, 

    period) 

{ 

  this.period = period; 

  this.minValue = minValue; 

  this.maxValue = maxValue; 

  this.count = 0; 

  this.onEnterFrame = function () 

  { 

    var value = (1 + Math.cos (this.count++ * 2 * Math.PI / 

        this.period)) / 2; 

    this._yscale = this.minValue + value * 

        Math.abs (this.maxValue - this.minValue); 

  }; 

}; 

clipper.WaveFade (20, 100, 48);
```

这里，我们用一个叫做`WaveFade`的新方法扩展了电影剪辑类，它以一种平滑、规则的模式垂直地上下缩放剪辑。该方法相对简单，只是需要小心使用余弦函数来产生控制缩放的值。

该方法有三个参数:`minValue`，剪辑的最小比例(百分比)；`maxValue`，为剪辑的最大刻度；以及`period`，一次振荡的帧数(从最大刻度到最小刻度再回来)。

使用余弦函数给`value`变量赋值。我们加 1，然后将结果除以 2，得到在 0 和 1 之间振荡的值。(余弦在 1 和-1 之间振荡。)然后，我们将其乘以最大和最小缩放值之间的差值，以确定每帧剪辑的垂直缩放。

*   保存并预览您的工作。

请注意，影片剪辑的`_yscale`从其最大高度平稳振荡至 20%，然后再次上升。现在我们已经有了动画，我们可以添加声音剪辑:

*   将粗体显示的行添加到“动作”面板中的现有代码中:

**例 5.2。`volcontrol.fla`行动:1**

```
MovieClip.prototype.WaveFade = function (minValue, maxValue, 

    period) 

{ 

  this.period = period; 

  this.minValue = minValue; 

  this.maxValue = maxValue; 

  this.count = 0; 

  this.reverb = new Sound (); 

  this.reverb.attachSound ("reverb"); 

  this.reverb.start (0, 999); 

  this.onEnterFrame = function () 

  { 

    var value = (1 + Math.cos (this.count++ * 2 * Math.PI / 

        this.period)) / 2; 

    this._yscale = this.minValue + value * 

        Math.abs (this.maxValue - this.minValue); 

    this.reverb.setVolume (this.minValue + value * 

        Math.abs (this.maxValue - this.minValue)); 

  }; 

}; 

clipper.WaveFade (20, 100, 48);
```

我们首先创建一个新的`Sound`对象，并附加来自库面板的声音。我们从开头(0)开始剪辑，并将其设置为循环`999`次。

同时，在 onEnterFrame 事件处理程序中，我们使用与 _yscale 相同的计算方法来设置声音剪辑的百分比音量:

`this.reverb.setVolume (this.minValue + value *
       Math.abs (this.maxValue - this.minValue));`

这是我们用来控制剪辑的 _yscale 的相同代码；理论上，我们可以用以下内容来代替它:

`this.reverb.setVolume(this._yscale);`

然而，我们避免使用这段代码，因为它可读性较差。当您以后重新访问代码时，如果看到声音属性链接到影片剪辑属性，您可能会很容易感到困惑。

*   保存并预览您的工作。

请注意，随着剪辑比例的降低，声音的音量也会降低。规模越大，体积也越大。脚本声音的添加增强了这种简单的效果，从而强化了信息并创造了更强大的体验。

##### 动态平移控制

在这个例子中，我们在屏幕上显示一个对象的动画，并根据对象的位置改变声音的平移。声相包括立体声中声音的移动，这甚至可以给单个连续的音调添加动态。这建立在前一个例子的基础上，使用一种新的方法:`setPan`来提高您的声音类技能。

默认情况下，声音的声相设置为`0`，但是我们可以使用`setPan`在`-100`(所有声音到左声道)和`+100`(所有声音到右声道)之间改变。

让我们用将声音从左声道移动到右声道的效果来模拟一个简单动画从左到右的移动。

要向前跳过并修改这个效果，请在代码归档中找到`pancontrol.fla`。

##### 设置场景

1.  创建一个 500 像素宽、400 像素高的新 Flash 文档。将帧速率设置为 24 fps(修改>文档…)。

*   重命名默认图层操作，并在“库”面板中创建新的两个文件夹:“影片剪辑”和“声音”。*   在影片剪辑文件夹中创建一个包含任意内容的新影片剪辑元件。代码归档中的示例使用了一个静态文本区域，其中显示了文本“Sound Effect”。*   将影片剪辑的一个实例拖到舞台上，并将其命名为`clipper`。*   选择文件>导入到库…并从代码存档中选择`reverb.mp3`，或者导入您选择的声音文件。*   从“库”面板中选择声音剪辑，然后右键单击并选择“链接...”。选中“为 ActionScript 导出”复选框，并输入`reverb`作为标识符。

##### 添加动作脚本

我们已经布置好场景了。是时候添加代码来将它们整合在一起了。

*   选择动作层的第一帧，并在动作面板中添加以下代码。由于这与上一个示例非常相似，我用粗体突出显示了不同之处:

**例 5.3。`pancontrol.fla`行动:1**

```
MovieClip.prototype.**WavePan** = function (minValue, maxValue,  

    period)  

{  

  this.period = period;  

  this.minValue = minValue;  

  this.maxValue = maxValue;  

  this.count = 0;  

  this.reverb = new Sound ();  

  this.reverb.attachSound ("reverb");  

  this.reverb.start (0, 999);  

  this.onEnterFrame = function ()  

  {  

    var value = (1 + Math.cos (this.count++ * 2 * Math.PI /  

        this.period)) / 2;  

    **this._x** = this.minValue + value *  

        Math.abs (this.maxValue - this.minValue);  

    **this.reverb.setPan** (-100 + value * 200);  

  };  

};  

**clipper.WavePan (50, Stage.width - 100, 48);**
```

我们给这个方法起了一个新名字(`WavePan`)，我们设置了电影剪辑的水平位置(而不是垂直比例)在最大值和最小值之间振荡，并在左右扬声器之间进行声音平移。

在对`WavePan`的调用中，我们传递了最小值和最大值，以确保电影剪辑覆盖了舞台的大部分:

```
clipper.WavePan (50, Stage.width - 100, 48);
```

剩下的就是试驾效果了！

*   保存您的 Flash 文档并预览您的工作。

将扬声器移开或戴上耳机，这样您就可以充分享受这种效果。

这个简单的效果在您的项目中有多种用途。它真的可以让你的作品栩栩如生，为动画、导航系统等等增添活力！

##### 迷你音响播放器

是时候伸伸腿了！我们已经讲述了声音控制的基本原理。现在，让我们进入一个全功能的声音播放设备的开发。在这个例子中，我们将创建一个随机声音播放器，提供关于它播放的随机声音剪辑的各种属性的视觉反馈，如图 5.1 所示。

![1366_ch5001](img/9f526246c94517bc2740b8170caaf804.png)
图 5.1。创建一个迷你声音播放器。

要向前跳转并编辑此效果，请在代码归档中找到`miniplayer.fla`。

##### 设置场景

1.  创建一个 450 像素宽、100 像素高的新 Flash 文档。将帧速率设置为 24 fps(修改>文档…)。

*   创建三个新层，命名为顶层动作，中间元素和底部背景。*   选择背景层，创建一个包围控件的背景元素，如图 5.1 所示。锁定背景层。*   创建四个按钮(作为库中的元件)，直观地反映以下功能:播放、停止、提高音量和降低音量。参见代码档案中的`miniplayer.fla`或图 5.1 中的示例。*   将按钮的实例拖到元素层的第一帧，命名为`PlayClip`、`StopClip`、`VolumeUp`和`VolumeDown`。排列按钮，使它们位于您在步骤 3 中创建的背景的边界内。*   在每个按钮下添加一个静态文本框，以标识它们执行的任务。在示例文件中，我将它们分别标记为`play`、`stop`、`volume +`和`volume -`。*   在代码档案中找到以下文件，并将其导入到您的库中(文件>导入到库…): `choir1.mp3`、`choir2.mp3`、`choir3.mp3`、`choir4.mp3`和`choir5.mp3`。*   在“库”面板中选择每个声音剪辑，然后右键单击并选择“链接...”。选中“为 ActionScript 导出”复选框，并接受默认标识符值(应该是`choir1`、`choir2`等)。).

正如我之前解释的，这允许我们动态地访问声音剪辑，而不必将它们拖到舞台上。

我们已经成功地创建了对象，并导入了实现基本功能所需的声音。剩下的工作就是添加控制应用程序的 ActionScript。

##### 添加动作脚本

首先，我们将添加控制我们导入的声音回放的代码。

*   选择动作层的第一帧，并在“动作”面板中添加以下代码:

**例 5.4。`miniplayer.fla`行动:1(节选)**

```
function randomBetween (a, b)  

{  

  return Math.min (a, b) + random (Math.abs (a - b) + 1);  

}  

PlayClip.onPress = function ()  

{  

  stopAllSounds ();  

  _root.orchestra = new Sound ();  

  _root.orchestra.attachSound ("choir" + randomBetween (1, 5));  

  _root.orchestra.start ();  

};
```

随着音乐播放器随机播放片段，我们熟悉的`randomBetween`功能就派上了用场。

作为这个例子的核心，`PlayClip`按钮的 onPress 事件处理程序肯定值得进一步研究。它做的第一件事是停止任何可能已经在播放的声音，这样如果重复点击`PlayClip`按钮，就不会同时播放多个声音剪辑。内置的`stopAllSounds`函数处理这个问题:

`stopAllSounds ();`

接下来，我们创建一个新的`Sound`对象，并将其存储在主电影(`_root`)中一个名为`orchestra`的变量中。然后我们使用`attachSound`从库中加载五种声音中的一种开始播放:

`_root.orchestra = new Sound ();  
 _root.orchestra.attachSound ("choir" + randomBetween (1, 5));  
 _root.orchestra.start ();`

*   保存并预览您的工作。

按下播放按钮，随机选择的剪辑将开始播放；再按一次，另一个随机剪辑就会播放。一切正常。

现在，我们将添加停止按钮的控制代码。这与我们使用播放按钮删除电影剪辑并停止舞台上所有声音的代码非常相似:

*   在您已经拥有的代码下添加以下代码:

**例 5.5。`miniplayer.fla`行动:1(节选)**

```
StopClip.onPress = function ()  

{  

  stopAllSounds ();  

  _root.orchestra = null;  

};
```

我们使用`stopAllSounds`来停止任何播放声音，并丢弃我们存储在`orchestra`变量中的`Sound`对象。这将释放对象正在使用的内存。

*   保存并预览您的工作。

当您按下播放按钮时，声音播放；当你按下停止按钮，它就会停止。让我们添加音量控制按钮，并完成这个例子。

*   在您已经拥有的代码下插入以下代码:

**例 5.6。`miniplayer.fla`行动:1(节选)**

```
VolumeUp.onPress = function ()  

{  

  if (_root.orchestra.getVolume () < 100)  

  {  

    _root.orchestra.setVolume (_root.orchestra.getVolume () +  

        10);  

  }  

};  

VolumeDown.onPress = function ()  

{  

  if (_root.orchestra.getVolume () > 0)  

  {  

    _root.orchestra.setVolume (_root.orchestra.getVolume () -  

        10);  

  }  

};
```

当声音剪辑的音量小于 100 时，每按一次`VolumeUp`按钮，音量就会增加 10。相反，当音量大于零时，每次按下`VolumeDown`按钮，音量会以十为单位减小。

*   保存并预览您的工作。

您可以通过按下“播放”按钮来选择一个随机剪辑，使用“停止”按钮来停止动态载入的剪辑，并使用音量控制来增大或减小剪辑的音量。您可能想试验一下自己的声音剪辑，因为代码档案中分发的声音剪辑非常短。

##### 修改

我们已经成功地创建了一个播放随机声音剪辑的界面！我们可以很容易地扩展它，以提供关于加载曲目的持续时间、剩余播放时间、播放剪辑的名称和音量级别的视觉反馈。

要编辑这些附加内容，请在代码归档中找到`miniplayer-feedback.fla`。

*   根据前面的示例，在 Actions 层下面添加一个新层，并将其命名为 Labels。这将保存我们的文本标签，它提供关于正在发生的事情的反馈。*   选择标签层的第一帧，并在音量按钮的右侧创建一个新的动态文本字段，如图 5.1 所示。将实例命名为`volume`。*   选中文本框后，在属性检查器中单击“字符…”并选择“基本拉丁文(Flash MX 中的所有字符)”。单击确定。*   参考图 5.1 中的位置，使用静态文本区域创建剩余的标题`clip length`和`time`。*   在相关标题旁边插入三个动态文本字段，命名为字段`clipname`、`cliplength`和`timeleft`。对每个文本框重复步骤 3。确保每个盒子足够大，能够容纳所需的信息。*   选择动作层的第一帧，并用以下代码替换现有代码。变化范围很广，但我再次用粗体突出显示了这些变化:

**例 5.7。`miniplayer-feedback.fla`行动:1**

```
function roundNumber (toRound, numDecimals)  

{  

  return Math.round (toRound * Math.pow (10, numDecimals)) /  

      Math.pow (10, numDecimals);  

}  

function randomBetween (a, b)  

{  

  return Math.min (a, b) + random (Math.abs (a - b) + 1);  

}  

_root.createEmptyMovieClip ("tracker", 1);  

PlayClip.onPress = function ()  

{  

  stopAllSounds ();  

  _root.orchestra = new Sound ();  

  var clipnumber = randomBetween (1, 5);  

  _root.orchestra.attachSound ("choir" + clipnumber);  

  _root.orchestra.start ();  

  _root.tracker.onEnterFrame = function ()  

  {  

    _root.timeleft.text = roundNumber (  

        (_root.orchestra.duration - _root.orchestra.position)  

        / 1000, 2) + " seconds";  

  };  

  _root.cliplength.text = roundNumber (  

      _root.orchestra.duration / 1000, 2) + " seconds";  

  _root.clipname.text = "Choir" + clipnumber;  

  _root.volume.text = _root.orchestra.getVolume ();  

};  

StopClip.onPress = function ()  

{  

  stopAllSounds ();  

  _root.orchestra = null;  

  _root.cliplength.text = "";  

  _root.clipname.text = "";  

  _root.timeleft.text = "";  

};  

VolumeUp.onPress = function ()  

{  

  if (_root.orchestra.getVolume () < 100)  

  {  

    _root.orchestra.setVolume (_root.orchestra.getVolume () +  

        10);  

    _root.volume.text = _root.orchestra.getVolume ();  

  }  

};  

VolumeDown.onPress = function ()  

{  

  if (_root.orchestra.getVolume () > 0)  

  {  

    _root.orchestra.setVolume (_root.orchestra.getVolume () -  

        10);  

    _root.volume.text = _root.orchestra.getVolume ();  

  }  

};
```

让我们仔细分析一下我们所做的改变，这样我们就能理解它是如何工作的了！

首先，我们引入一个新的数学函数`roundNumber`，它将数字四舍五入到指定的小数位数。这将用于显示每个剪辑的剩余播放时间和剪辑的总持续时间。

```
function roundNumber (toRound, numDecimals)  

{  

  return Math.round (toRound * Math.pow (10, numDecimals)) /  

      Math.pow (10, numDecimals);  

}
```

我们将更新电影每一帧的剩余跟踪时间，因此我们需要一个 onEnterFrame 事件处理程序。不幸的是，我们没有任何显而易见的电影剪辑来钩住这个处理程序。我们可以将它附加到界面中的一个按钮上，但是创建一个名为`tracker`的空电影剪辑来存放它会更整洁:

```
_root.createEmptyMovieClip ("tracker", 1);
```

然后，我们将下面的代码添加到 PlayClip 按钮的 onPress 处理程序中，该处理程序设置事件处理程序本身:

`_root.tracker.onEnterFrame = function ()  
 {  
   _root.timeleft.text = roundNumber ((_root.orchestra.duration -  
       _root.orchestra.position) / 1000, 2) + " seconds";  
 };`

这将持续更新我们之前创建的动态文本字段。我们不断地使用`Sound`对象的持续时间和位置属性来监控声音剪辑的位置，以毫秒为单位计算剩余时间。然后，我们将它转换成以秒为单位的值，在我们的`roundNumber`函数的帮助下，四舍五入到两位小数。

我们还更新了其他动态文本字段，这些字段保存了正在播放的剪辑的长度、名称和音量。

`_root.cliplength.text = roundNumber (_root.orchestra.duration /  
     1000, 2) + " seconds";  
 _root.clipname.text = "Choir" + clipnumber;  
 _root.volume.text = _root.orchestra.getVolume ();`

在`StopClip`按钮的 onPress 事件处理程序中，我们包含了以下代码，它清除了与先前播放的剪辑相关的信息的动态文本字段:

```
_root.cliplength.text = "";  

_root.clipname.text = "";  

_root.timeleft.text = "";
```

最后，`VolumeUp`和`VolumeDown` onPress 事件处理程序被更新。这确保了对剪辑音量的任何更改都会反映在`volume`动态文本栏中。

```
_root.volume.text = _root.orchestra.getVolume ();
```

代码更改到此为止。

*   保存您的 Flash 文档并预览它。

那里！我们有一个完全成熟的随机剪辑播放器，可以提供当前剪辑的持续时间、名称和剩余播放时间的信息。

通过向库中添加更多的剪辑，可以很容易地扩展这个示例。导出它们以供 ActionScript 参考，并在`PlayClip`按钮的 onPress 事件处理程序中修改选择要播放的剪辑的代码。

***使用数组！***
一个有用的变化是将可用剪辑的链接名称存储在一个数组中，这样可用剪辑的数量就不必硬编码了。

##### 随机轨道音序器效果

这个效果和书中其他的不一样的地方在于它没有界面！没什么可看的，但有很多可听的。我们的目标是覆盖随机轨道，以创造一种独特的声音——与用户通常听到的声音略有不同。作文包含四个 10 秒的循环，包括从以下类别中随机选择:

*   背景:选择五个背景床轨道
*   鼓:五个鼓轨道的选择
*   吉他:五个吉他循环的选择
*   覆盖图:不同覆盖图的选择

加载电影时，会从每个类别中随机选择一个。然后将这些音轨组合起来，创造出 625 个可能的音轨之一。

介绍到此为止，让我们开始吧！要编辑此效果，请在代码归档中找到`random.fla`。

##### 设置场景

1.  创建一个宽 200 像素、高 200 像素的新 Flash 文档。将帧速率修改为 24 fps(修改>文档…)。

*   将图层 1 重命名为动作。*   选择文件>导入>导入到库…并从代码档案中选择以下 MP3 文件:`Background1.mp3`、`Background2.mp3`、`Background3.mp3`、`Background4.mp3`、`Background5.mp3`、`Drum1.mp3`、`Drum2.mp3`、`Drum3.mp3`、`Drum4.mp3`、`Drum5.mp3`、`Guitar1.mp3`、`Guitar2.mp3`、`Guitar3.mp3`、`Guitar4.mp3`、`Guitar5.mp3`、`Overlay1.mp3`、`Overlay2.mp3`、`Overlay3.mp3`、`Overlay4.mp3`和`Overlay5.mp3`。*   在“库”面板中选择这些声音剪辑，然后右键单击并选择“链接...”。选中“为 ActionScript 导出”复选框，接受提供的标识符(如果您使用的是 Flash MX 2004，请删除`.mp3`扩展名)，然后单击“确定”。

这就是我们在这个例子中需要做的所有设置。因为我们没有添加界面或允许任何用户交互，所以除了产生声音合成的 ActionScript 之外，不需要包含其他元素。

##### 添加动作脚本

*   选择动作层的第一帧，并将以下代码添加到动作面板中:

**例 5.8。`random.fla`行动:1**

```
function randomBetween (a, b)   

{   

  return Math.min (a, b) + random (Math.abs (a - b) + 1);   

}   

MovieClip.prototype.PlayQueue = function (clip)   

{   

  var orchestra = new Sound (this);   

  orchestra.attachSound (clip);   

  orchestra.start ();   

};   

function PlayClips ()   

{   

  var background = "background" + randomBetween (1, 5);   

  var drums = "drum" + randomBetween (1, 5);   

  var guitar = "guitar" + randomBetween (1, 5);   

  var overlay = "overlay" + randomBetween (1, 5);   

  var clips = new Array (background, drums, guitar, overlay);   

  for (var i = 0; i < clips.length; i++)   

  {   

    var mc = createEmptyMovieClip ("SoundHolder" + i, i);   

    mc.PlayQueue (clips[i]);   

  }   

}   

PlayClips ();
```

这是一段相当简洁的代码。预览电影并跳过代码演练，或者继续阅读本质细节——选择权在你！

让我们看看`PlayClips`函数，看看它们是如何组合在一起的。对于每个音轨，我们创建一个变量(如`drums`)，用库中声音剪辑的链接标识符之一填充，使用`randomBetween`函数生成一个 1 到 5 之间的随机数。然后，我们将这些变量组合成一组声音剪辑:

`var clips = new Array (background, drums, guitar, overlay);`

从这里开始，我们使用一个 **for** 循环为数组的每个元素创建一个空的电影剪辑。这些剪辑随机播放选定的声音剪辑。这是通过调用`PlayQueue` movie clip 方法(我们一会儿会研究它)并将声音剪辑链接标识符作为参数传递来完成的。

`for (var i = 0; i < clips.length; i++)  
 {  
   var mc = createEmptyMovieClip ("SoundHolder" + i, i);  
   mc.PlayQueue (clips[i]);  
 }`

我们为每个由`PlayClips`函数创建的新电影剪辑调用`PlayQueue`方法。这将在每个电影剪辑中嵌入声音剪辑，然后播放它。

```
MovieClip.prototype.PlayQueue = function (clip)   

{   

  var orchestra = new Sound (this);   

  orchestra.attachSound (clip);   

  orchestra.start ();   

};
```

因为声音片段是在运行时快速连续创建的，所以效果是带有背景的单个复合轨道，加上鼓、吉他和叠加部分。

*   保存并预览您的文档。

你可能会注意到，每次播放电影，你都会得到不同的构图。有些工作得很好，而有些听起来相当笨拙！这都是关于实验和看看什么是可能的。

##### 随机轨道覆盖效应

这种效果基于前面的“headless”示例，但是在它的基础上进行了大量的构建，利用了`CheckBox`组件、自定义事件处理程序，并为用户提供了打开或关闭音轨的能力。用户界面如图 5.2 所示。

![1366_ch5002](img/4b1654eaa4bd380e8aecced779558f2e.png)
图 5.2。这种随机轨道叠加效果建立在前面的示例上。

***复选框不兼容***

在开发这种使用`CheckBox`组件的效果时，我困惑地发现我为 Flash MX 准备的代码不能在 Flash MX 2004 上工作。我通过参考面板对此进行了调查，发现对于 Flash MX 2004 版本的`CheckBox`组件没有`getValue`方法。此外，`CheckBox`不再允许变更事件处理程序。

一位在 Macromedia 工作的工程师朋友告诉我，这些功能不仅仅是被废弃了，它们已经被完全删除了！

下面的代码和步骤是针对 Flash MX 的，而 2004 版本的更改是作为补充提供的。代码档案中提供的示例是使用组件的 Flash MX 版本开发的。

要修改这种效果，请在代码归档中找到`random-gui.fla`。

##### 设置场景

1.  创建一个 550 像素宽、100 像素高的新 Flash 文档。将帧速率修改为 24 fps(修改>文档…)。

*   将图层 1 重命名为`Actions`，并在其下创建三个图层，分别命名为复选框、元素和背景。*   选择文件>导入到库…并在代码档案中选择以下 MP3 文件:`Background1.mp3`、`Background2.mp3`、`Background3.mp3`、`Background4.mp3`、`Background5.mp3`、`Drum1.mp3`、`Drum2.mp3`、`Drum3.mp3`、`Drum4.mp3`、`Drum5.mp3`、`Guitar1.mp3`、`Guitar2.mp3`、`Guitar3.mp3`、`Guitar4.mp3`、`Guitar5.mp3`、`Overlay1.mp3`、`Overlay2.mp3`、`Overlay3.mp3`、`Overlay4.mp3`和`Overlay5.mp3`。*   在“库”面板中选择每个声音剪辑，然后右键单击并选择“链接...”。选中“为 ActionScript 导出”复选框，接受提供的唯一标识符(如果您使用的是 Flash MX 20004，请删除`.mp3`扩展名)，然后单击“确定”。*   从“组件”面板(“窗口”>“开发面板”>“组件”)中，选择 Flash UI 组件(或 Flash MX 2004 中的 UI 组件),并将四个`CheckBox`组件实例拖到舞台的复选框层上。如有必要，使用对齐面板从左至右排列它们以设置它们的间距(窗口>设计面板>对齐)。*   从左到右依次选择舞台上的每个`CheckBox`组件，应用图 5.3 所示的实例名称和参数。

![1368_bkgnd](img/c0cfb137791cf45e3452fddac613f2bb.png)

![1368_drums](img/0e11bfe961c16607b17a50aee8c21ff3.png)

![1368_guitar](img/a92337676f57e323db9c73101dcfd3a4.png)

![1368_overlay](img/208665504ef870f2e0602501a9151873.png)
图 5.3。四个`CheckBox`组件的属性显示在 Flash 中。

***Flash MX 2004 中没有更改处理程序***
每个`CheckBox`的更改处理程序参数在这里尤其重要，因为当相应的复选框未选中时，它将用于使轨道静音。Flash MX 2004 中不存在此参数，因此我们稍后将添加一些 ActionScript 来处理该版本中的这些事件。

*   选择元素层的第一帧，插入一个 270×4 像素的矩形。将其转换为名为`Progress`的影片剪辑元件，并将该实例命名为`progress`。这将作为一个快速进度条，指示乐曲播放的进度。*   选择背景层的第一个框架，并在控件周围创建一个框架。如果你没有灵感，请看图 5.2。一旦你对框架感到满意，锁定这一层。

现在，让我们添加代码使效果生效。

##### 添加动作脚本

*   选择动作层的第一帧，将以下代码添加到“动作”面板中:

**例 5.9。`random-gui.fla`行动:1(节选)**

```
function randomBetween (a, b)   

{   

  return Math.min (a, b) + random (Math.abs (a - b) + 1);   

}   

function roundNumber (toRound, numDecimals)   

{   

  return Math.round (toRound * Math.pow (10, numDecimals)) /   

      Math.pow (10, numDecimals);   

}   

MovieClip.prototype.PlayQueue = function (clip)   

{   

  this.orchestra = new Sound (this);   

  this.orchestra.attachSound (clip);   

  this.orchestra.start ();   

};   

function PlayClips ()   

{   

  var background = "background" + randomBetween (1, 5);   

  var drums = "drum" + randomBetween (1, 5);   

  var guitar = "guitar" + randomBetween (1, 5);   

  var overlay = "overlay" + randomBetween (1, 5);   

  var clips = new Array (background, drums, guitar, overlay);   

  var mc;   

  for (var i = 0; i < clips.length; i++)   

  {   

    mc = createEmptyMovieClip ("SoundHolder" + i, i);   

    mc.PlayQueue (clips[i]);   

  }   

  mc.onEnterFrame = function ()   

  {   

    _root.progress._xscale = this.orchestra.position /   

        this.orchestra.duration * 100;   

  };   

  mc.orchestra.onSoundComplete = function ()   

  {   

    stopAllSounds ();   

    LoopClips ();   

  };   

}   

function LoopClips ()   

{   

  _root.SoundHolder0.orchestra.start ();   

  _root.SoundHolder1.orchestra.start ();   

  _root.SoundHolder2.orchestra.start ();   

  _root.SoundHolder3.orchestra.start ();   

}   

PlayClips ();
```

我们已经在本章前面使用了`randomBetween`和`roundNumber`函数，所以没有必要再讨论它们。

除了我们添加到上一个动态生成的电影剪辑中的几个事件处理程序之外，`PlayQueue`方法与上一个示例中使用的方法相同，还有`PlayClips`函数。

`mc.onEnterFrame = function ()  
 {  
   _root.progress._xscale = this.orchestra.position /  
       this.orchestra.duration * 100;  
 };  
 mc.orchestra.onSoundComplete = function ()  
 {  
   stopAllSounds ();  
   LoopClips ();  
 };`

在初始化声音对象、附加声音剪辑并开始播放声音剪辑后，我们动态跟踪最后一个声音对象的位置，该对象是使用附加到包含电影剪辑的 onEnterFrame 事件处理程序创建的。此事件处理程序通过改变其 _xscale 属性来控制进度条的宽度。当声音结束时，onSoundComplete 事件处理程序将调用`stopAllSounds`函数。在那之后，我们立即调用`LoopClips`来重新开始我们已经制作的四个轨道，创建一个无限循环的效果。

```
function LoopClips ()   

{   

  _root.SoundHolder0.orchestra.start ();   

  _root.SoundHolder1.orchestra.start ();   

  _root.SoundHolder2.orchestra.start ();   

  _root.SoundHolder3.orchestra.start ();   

}
```

到目前为止，一切顺利，对吧？当电影正在播放时，我们希望通过单击舞台上的复选框来动态切换不同的轨道。为此，每个`CheckBox`都有自己的事件处理程序来控制音轨的音量。现在来补充这些。

*   将以下代码添加到“动作”面板中现有代码的下方:

**例 5.10。`random-gui.fla`行动:1(节选)**

```
function backgroundChange (background)   

{   

  if (background.getValue ())   

  {   

    _root.SoundHolder0.Orchestra.setVolume (100);   

  }   

  else   

  {   

    _root.SoundHolder0.Orchestra.setVolume (0);   

  }   

}   

function drumsChange (drums)   

{   

  if (drums.getValue ())   

  {   

    _root.SoundHolder1.Orchestra.setVolume (100);   

  }   

  else   

  {   

    _root.SoundHolder1.Orchestra.setVolume (0);   

  }   

}   

function guitarChange (guitar)   

{   

  if (guitar.getValue ())   

  {   

    _root.SoundHolder2.Orchestra.setVolume (100);   

  }   

  else   

  {   

    _root.SoundHolder2.Orchestra.setVolume (0);   

  }   

}   

function overlayChange (overlay)   

{   

  if (overlay.getValue ())   

  {   

    _root.SoundHolder3.Orchestra.setVolume (100);   

  }   

  else   

  {   

    _root.SoundHolder3.Orchestra.setVolume (0);   

  }   

}
```

这些函数的名称对应于我们创建它们时分配给每个`heckBox`组件的变更处理程序参数值。所有这些事件处理程序都以相同的方式工作。他们使用`getValue`方法检查`CheckBox`是否被检查，然后设置相应声音剪辑的音量以匹配。每个`CheckBox`的初始状态是`true`(选中)。因此，当复选框被点击时，它的状态切换到`false`，并且对于那个特定的`Sound`对象，音量被设置为`0`。再次点击它，状态变为`true`，音量变为`100`。以这种方式使用变更处理程序，我们可以随意地删除单个的轨道并把它们带回来。

*   保存您的 Flash 文档并预览它。

你现在可能正在听一首时髦的曲子。点按复选框以删除不同的轨道，然后将它们带回来。如果你不喜欢这个构图，或者它对你的口味来说有点太狂野了，请再次预览这部电影，看看随机序列器得出了什么！

##### Flash MX 2004 的更改

如果您使用的是 Flash MX 2004，您会发现复选框并不像宣传的那样有效。这是因为`CheckBox`组件除了看起来更好之外，还缺少一个更改处理程序参数。因此，我们的事件处理程序方法没有与复选框挂钩。这是一件好事，因为这些方法使用了 Flash MX 2004 `CheckBox`组件中缺少的另一个功能:T2 方法。现在，让我们进行必要的更改，以使示例在 Flash MX 2004 中工作。

首先，更改每个事件处理函数，让它们检查适当的`CheckBox`的 selected 属性，而不是不再存在的`getValue`方法。例如，调整`backgroundChange`功能的这行:

`if (background.getValue ())`

用这一行替换它:

`if (background.selected)`

接下来，设置事件处理函数，以便在单击其中一个复选框时调用它们。

将以下代码添加到“动作”面板中所有其他代码的下方:

```
var cboxListener = new Object ();    

cboxListener.click = function()    

{    

  backgroundChange (background);    

  drumsChange (drums);    

  guitarChange (guitar);    

  overlayChange (overlay);    

};    

background.addEventListener ("click", cboxListener);    

drums.addEventListener ("click", cboxListener);    

guitar.addEventListener ("click", cboxListener);    

overlay.addEventListener ("click", cboxListener);
```

这用一个名为`click`的方法创建了一个新的`Object`，它被设置为舞台上四个`CheckBox`对象的*事件监听器*。当点击任何一个复选框时，就会调用`click`方法。反过来，它调用我们的四个事件处理程序来相应地更新卷。

***观看文件大小！***
这个例子包含了 20 个十秒钟的片段，总重量接近 3MB！这个项目更适合 CD 或 kiosk 部署，而不是通过互联网分发。如果剪辑更短，或者更少，也许可以通过互联网分发应用程序——只是要注意文件大小。别说我没警告你！

##### 修改

我们可以通过直接控制`Sound`对象的属性来使这种效果更具交互性。在下面的修改中，我们将使用 Flash MX 2004 附带的`NumericStepper`组件来控制各种声音剪辑的音量。界面如图 5.4 所示。

![1366_ch5004](img/e2bfc092c7eaf5f3752c27ae56fd1bda.png)
图 5.4。为动态声音剪辑添加音量控制。

由于我们将添加 Flash MX 2004 `NumericStepper`组件，我们将利用 Flash MX 2004 附带的新`CheckBox`组件，而不是我们在前面的示例中使用的 Flash MX 版本。

如果您正在使用 Flash MX，您也可以直接跳到下一个示例。

要编辑此效果，请在代码归档中找到`random-volume.fla`。

##### 设置场景

在前面示例的基础上，我们将添加几段代码来适应音量控制机制，并将现有的 Flash MX 复选框组件替换为其 Flash MX 2004 对应组件。我们还将在界面中添加新的 NumericStepper 组件，为用户提供音量控制。

1.  如果您从上一个示例开始工作，请在复选框层中找到 Flash MX 复选框组件，并删除它们。

*   选择 CheckBoxes 层的第一帧，并从“组件”面板的“UI 组件”部分拖动四个`CheckBox`组件实例，使它们在舞台上均匀分布。列举这些实例，从左到右:`backgroundCBox`、`drumsCBox`、`guitarCBox`和`overlayCBox`。*   对于您刚刚创建的每个`CheckBox`实例，将属性检查器中的 selected 参数更改为`true`，这样复选框将被默认选中。*   更改每个`CheckBox`实例的标签参数。从左到右，它们应该是:`Background`、`Drums`、`Guitar`和`Overlay`。*   将`NumericStepper`组件的四个实例从“组件”面板的“UI 组件”部分拖动到 CheckBoxes 层的第一个帧中。对齐每个复选框下的每个`NumericStepper`。将实例命名为`vol1Control`、`vol2Control`、`vol3Control`和`vol4Control`，并将它们的宽度设置为 50 像素。*   对于每个`NumericStepper`组件实例，在属性检查器中设置参数，如下所示:

最大值
`100`

最小值
`0`

步长
`5`

值
`100`

*   在每个`NumericStepper`组件上方，添加一个包含文本“Volume”的静态文本框，以指示`NumericStepper`组件的用途。结果应该类似于图 5.4。*   改变电影和圆角矩形框架的宽度和高度，以容纳我们添加的额外控件。

现在我们可以添加额外的动作脚本来完成这个效果。

##### 添加动作脚本

*   选择动作层的第一帧。我们将保留数学和回放函数/方法(`randomBetween`、`roundNumber`、`PlayQueue`、`PlayClips`、`LoopClips`)，但用以下代码替换剩余的代码(复选框事件处理程序):

```
var backgroundOff = false;    

var drumsOff = false;    

var guitarOff = false;    

var overlayOff = false;    

function backgroundChange ()    

{    

  if (backgroundCBox.selected)    

  {    

    backgroundOff = false;    

    vol1Control.enabled = true;    

  }    

  else    

  {    

    backgroundOff = true;    

    vol1Control.enabled = false;    

    _root.SoundHolder0.orchestra.setVolume (0);    

  }    

}    

function drumsChange ()    

{    

  if (drumsCBox.selected)    

  {    

    drumsOff = false;    

    vol2Control.enabled = true;    

  }    

  else    

  {    

    drumsOff = true;    

    vol2Control.enabled = false;    

    _root.SoundHolder1.orchestra.setVolume (0);    

  }    

}    

function guitarChange ()    

{    

  if (guitarCBox.selected)    

  {    

    guitarOff = false;    

    vol3Control.enabled = true;    

  }    

  else    

  {    

    guitarOff = true;    

    vol3Control.enabled = false;    

    _root.SoundHolder2.orchestra.setVolume (0);    

  }    

}    

function overlayChange ()    

{    

  if (overlayCBox.selected)    

  {    

    overlayOff = false;    

    vol4Control.enabled = true;    

  }    

  else    

  {    

    overlayOff = true;    

    vol4Control.enabled = false;    

    _root.SoundHolder3.orchestra.setVolume (0);    

  }    

}    

function setCustomVolume ()    

{    

  if (!backgroundOff)    

  {    

    SoundHolder0.orchestra.setVolume (vol1Control.value);    

  }    

  if (!drumsOff)    

  {    

    SoundHolder1.orchestra.setVolume (vol2Control.value);    

  }    

  if (!guitarOff)    

  {    

    SoundHolder2.orchestra.setVolume (vol3Control.value);    

  }    

  if (!overlayOff)    

  {    

    SoundHolder3.orchestra.setVolume (vol4Control.value);    

  }    

}    

var volumeListener = new Object ();    

volumeListener.change = function ()    

{    

  setCustomVolume ();    

};    

var cboxListener = new Object ();    

cboxListener.click = function ()    

{    

  backgroundChange ();    

  drumsChange ();    

  guitarChange ();    

  overlayChange ();    

  setCustomVolume ();    

};    

vol1Control.addEventListener ("change", volumeListener);    

vol2Control.addEventListener ("change", volumeListener);    

vol3Control.addEventListener ("change", volumeListener);    

vol4Control.addEventListener ("change", volumeListener);    

backgroundCBox.addEventListener ("click", cboxListener);    

drumsCBox.addEventListener ("click", cboxListener);    

guitarCBox.addEventListener ("click", cboxListener);    

overlayCBox.addEventListener ("click", cboxListener);
```

同样，我们有处理复选框变化的函数(`backgroundChange`、`drumsChange`、`guitarChange`和`overlayChange`)。它们看起来都很相似。

```
function backgroundChange ()    

{    

  if (backgroundCBox.selected)    

  {    

    backgroundOff = false;    

    vol1Control.enabled = true;    

  }    

  else    

  {    

    backgroundOff = true;    

    vol1Control.enabled = false;    

    _root.SoundHolder0.orchestra.setVolume (0);    

  }    

}
```

这些功能通过分析相关`CheckBox`的选定属性来检测其当前状态。如果选择了`CheckBox`，我们将变量设置为`false`(在本例中，变量为`backgroundOff`)以指示相应的剪辑没有关闭。这四个变量首先被设置为`false`，因为所有四个声音剪辑在电影开始时都被打开。我们还启用相关的音量控制`NumericStepper` ( `vol1Control`)。

如果没有选择`CheckBox`，我们将变量设置为`true`并禁用相应的`NumericStepper`组件，表示声音剪辑被关闭。我们还将相关声音剪辑的音量设置为零。这将关闭剪辑，但允许它继续无声播放，保持其位置，以防我们稍后决定重新打开它。

`setCustomVolume`函数将每个剪辑的音量设置为由相关联的`NumericStepper`控件指定的值，除非标志变量指示它当前被关闭。

```
function setCustomVolume ()    

{    

  if (!backgroundOff)    

  {    

    SoundHolder0.orchestra.setVolume (vol1Control.value);    

  }    

  if (!drumsOff)    

  {    

    SoundHolder1.orchestra.setVolume (vol2Control.value);    

  }    

  if (!guitarOff)    

  {    

    SoundHolder2.orchestra.setVolume (vol3Control.value);    

  }    

  if (!overlayOff)    

  {    

    SoundHolder3.orchestra.setVolume (vol4Control.value);    

  }    

}
```

剩下的就是确保这些函数在适当的时候被调用。一个名为`volumeListener`的新监听器`Object`提供了一个名为`change`的方法来调用`setCustomVolume`函数。

```
var volumeListener = new Object ();    

volumeListener.change = function ()    

{    

  setCustomVolume ();    

};
```

通过从每个`NumericStepper`组件调用`addEventListener`方法，我们让这个对象处理剪辑音量的变化:

```
vol1Control.addEventListener ("change", volumeListener);    

vol2Control.addEventListener ("change", volumeListener);    

vol3Control.addEventListener ("change", volumeListener);    

vol4Control.addEventListener ("change", volumeListener);
```

如果任何一个`NumericStepper`组件值发生变化，就会调用`setCustomVolume`函数来处理它。

我们还以类似的方式为`CheckBox`组件设置了一个事件监听器:

```
cboxListener = new Object ();    

cboxListener.click = function ()    

{    

  backgroundChange ();    

  drumsChange ();    

  guitarChange ();    

  overlayChange ();    

  setCustomVolume ();    

};    

...    

backgroundCBox.addEventListener ("click", cboxListener);    

drumsCBox.addEventListener ("click", cboxListener);    

guitarCBox.addEventListener ("click", cboxListener);    

overlayCBox.addEventListener ("click", cboxListener);
```

当任何一个复选框被点击时，事件处理器在调用`setCustomVolume`来正确设置刚刚被打开的任何剪辑的音量之前，调用我们之前定义的四个函数(打开或关闭正确的声音剪辑)。

*   保存文档并将 SWF 文件导出到适当的位置。

***别忘了 ActionScript 2.0***
随 Flash MX 2004 分发的组件使用 ActionScript 2.0 的功能在内部工作。如果您尝试发布包含此类组件的电影，并为 ActionScript 1.0 配置发布设置，则这些组件将不起作用(它们看起来像带有黑边的白色矩形)。

在 Flash MX 2004 中创建的新电影默认为 ActionScript 2.0，所以您在那里没问题。但是，如果您从代码归档中提供的上一个示例的完成版本开始，您将需要更改文件的 ActionScript 版本设置。只需单击电影的背景，然后单击属性检查器中的“设置…”按钮。您可以在“Flash”选项卡上调整 ActionScript 版本设置。

使用复选框来打开和关闭一些轨道；请注意它们是如何禁用和启用我们添加的音量控制的。尝试用音量控制来设置轨道之间的良好平衡，看看你能想出什么杰作！

您可以在此示例的基础上修改任意数量的 sound 对象属性。花一点时间，看看你能实现什么。

##### XML、MP3 和 ID3 标签阅读器

这个效果使用了我们在本章前面使用的相同的唱诗班声音剪辑，但是使用了稍微不同的方法。我们将从 XML 文件导入一个播放列表，并将 MP3 文件动态加载到 Flash 中。我们还将展示存储在 MP3 中的歌曲信息(包括专辑、艺术家和歌曲名称)，这些信息被称为 *ID3 标签*，如图 5.5 所示。

![1366_ch5003](img/0b330c34bb822f66e62f4fa7a59d5cc0.png)
图 5.5。使用 XML 和 ID3 数据创建一个随机 MP3 播放器。

由于 Flash Player 版本之间的差异，在 Flash MX 和 Flash MX 2004 中引用 ID3 标记信息的方式不同。Macromedia Flash Player 7 支持最新的 ID3 v2.2 和 v2.4 标签。由于数据存储在 MP3 文件的开头，因此在打开 MP3 流时会立即引用这些信息。另一方面，Macromedia Flash Player 6 仅支持 ID3 v1.0 和 v1.1 标签，它们存储在文件的末尾。在那个平台上，我们必须等到流被完全加载，然后才能检索 ID3 标签信息。

我们将首先为 Flash Player 的版本 6 创建一个 MP3 播放器，支持 ID3 v1.x 标签。然后，我们将修改效果，以利用 Flash Player 版本 7 中的 ID3 v2.x 标签支持。

要编辑此效果，请在代码归档中找到`mp3â€“mx.fla`。

它使用几个 MP3 文件:`choir1.mp3`、`choir2.mp3`、`choir3.mp3`、`choir4.mp3`、`choir5.mp3`。我们还需要充当播放列表的 XML 文件；它叫做`playlist.xml`，也可以在代码档案中找到。

##### 设置场景

对于这个项目，我们需要一个可点击的电影剪辑来加载一个随机的 MP3 文件和一个动态文本字段来显示 MP3 文件中的 ID3 数据。

1.  创建一个 500 像素宽、450 像素高的新 Flash 文档。接受默认帧速率，然后单击确定。

*   重命名默认层动作，并在其下添加两个新层，名为接口和背景。*   选择界面层的第一帧，创建一个名为`PlayRandomButton`的电影剪辑，其中包含一个大约 70 像素高、70 像素宽的按钮。将实例命名为 randomplayer，并将剪辑移动到舞台中央的左侧，如图 5.5 所示。

我们将使用这个片段随机加载一个 MP3 文件，并将 MP3 文件中的 ID3 标记信息传递到一个动态文本字段中。

*   在按钮右侧创建一个新的 320×160 像素动态文本字段。将字段命名为`trackID3Info`。*   在新字段的属性检查器中，单击“字符…”按钮，然后选择“基本拉丁文”(或“Flash MX 中的所有字符”)。

这个动态文本字段将充当一个容器，从动态加载的 MP3 文件中接收 ID3 标签信息。

现在，稍微装饰一下:

*   在背景图层的第一帧添加一个矩形，框住刚刚创建的文本区域。

你可以添加尽可能多的额外功能来增加界面的美感。灵感参考图 5.5！

*   保存您的工作。*   从代码档案中找到文件`choir1.mp3`、`choir2.mp3`、`choir3.mp3`、`choir4.mp3`和`choir5.mp3`，并将它们移动到保存文档的同一个文件夹中。

##### 创建 XML 播放列表

我们现在需要创建 XML 播放列表，引用我们计划加载的 MP3 文件。

***提示***
关于使用 XML 的更多信息，请参阅 Harry Fuecks 的 XML 简介。

*   在文本编辑器中键入以下代码，并在同一文件夹中将其另存为`playlist.xml`。

**例 5.11。`playlist.xml`行动:1**

```
<?xml version="1.0" encoding="iso-8859-1"?>     

<playlist>     

  <mp3file track="choir1.mp3"/>     

  <mp3file track="choir2.mp3"/>     

  <mp3file track="choir3.mp3"/>     

  <mp3file track="choir4.mp3"/>     

  <mp3file track="choir5.mp3"/>     

</playlist>
```

与使用预定义标签集的 HTML 不同，XML 允许您创建自定义数据结构。只要所有开始标记都有一个匹配的结束标记(或者，就像本例中的`mp3File`标记一样，它们会自行结束)，Flash 就会将数据结构作为 ActionScript XML 对象导入，从而允许您操作和访问文件中包含的信息。

这个 XML 文件结构简单。`playlist`标签是顶层节点，`mp3file`标签是它的子节点。这些子节点还包含一个名为`track`的属性，它保存了我们希望放入 Flash 的 MP3 文件的名称。

我们的 ActionScript 将导入这个 XML 文件并遍历数据结构，用它包含的 MP3 文件名填充一个数组。

##### 添加动作脚本

*   选择动作层的第一帧，并将以下代码添加到动作面板中:

**例 5.12。`mp3â€“mx.fla`行动:1(节选)**

```
var tracklist = new Array ();     

var mp3List = new XML ();     

mp3List.ignoreWhite = true;     

mp3List.onLoad = createPlayList;     

mp3List.load ("playlist.xml");     

function createPlayList (success)     

{     

  if (!success)     

  {     

    return;     

  }     

  var topLevel = null;     

  for (i = 0; i <= this.childNodes.length; i++)     

  {     

    if (this.childNodes[i].nodeValue == null &&     

        this.childNodes[i].nodeName == "playlist")     

    {     

      topLevel = this.childNodes[i];     

      break;     

    }     

  }     

  if (topLevel != null)     

  {     

    for (i = 0; i <= topLevel.childNodes.length; i++)     

    {     

      if (topLevel.childNodes[i].nodeName == "mp3file")     

      {     

        var track =     

            topLevel.childNodes[i].attributes["track"];     

        _root.tracklist.push (track);     

      }     

    }     

  }     

}     

function randomBetween (a, b)     

{     

  return Math.min (a, b) + random (Math.abs (a - b) + 1);     

}     

function playTrack ()     

{     

  var track = _root.track;     

  if (track.getBytesLoaded () == track.getBytesTotal () &&     

      track.duration > 0)     

  {     

    clearInterval (_root.checkLoaded);     

    trackID3Info.text = "";     

    trackID3Info.text += "Title: " + track.id3.songname +     

        newline;     

    trackID3Info.text += "Artist: " + track.id3.artist +     

        newline;     

    trackID3Info.text += "Album: " + track.id3.album +     

        newline;     

    trackID3Info.text += "Year: " + track.id3.year + newline;     

    trackID3Info.text += "Comments: " + track.id3.comment +     

        newline;     

  }     

}     

randomplayer.onPress = function ()     

{     

  stopAllSounds ();     

  var trackNo = randomBetween (0, _root.tracklist.length - 1);     

  _root.track = new Sound ();     

  _root.track.loadSound (_root.tracklist[trackNo], true);     

  _root.checkLoaded = setInterval (playTrack, 500);     

};
```

让我们将这些代码分成易于管理的几个部分，看看会发生什么。

首先，我们创建一个空数组来包含我们的 MP3 曲目列表。

```
var tracklist = new Array ();
```

接下来，我们创建一个名为`mp3List`的 XML 对象，并将其 ignoreWhite 属性设置为`true`，这样换行和空白就不会被解析为单独的节点。

```
var mp3List = new XML ();     

mp3List.ignoreWhite = true;
```

然后我们设置事件处理程序，它将在 XML 文件加载(`createPlayList`)并从`playlist.xml`加载数据时执行。

```
mp3List.onLoad = createPlayList;     

mp3List.load ("playlist.xml");
```

现在我们定义`createPlayList`函数，一旦加载了 XML 数据，就会调用它来处理 XML 数据。

```
function createPlayList (success)     

{     

  if (!success)     

  {     

    return;     

  }     

  var topLevel = null;     

  for (i = 0; i <= this.childNodes.length; i++)     

  {     

    if (this.childNodes[i].nodeValue == null &&     

        this.childNodes[i].nodeName == "playlist")     

    {     

      topLevel = this.childNodes[i];     

      break;     

    }     

  }
```

我们做的第一件事是检查 XML 文件是否加载成功，如参数`success`所示。如果没有，我们在试图处理它之前退出。

然后，我们根据 XML 对象(`this`)中顶级节点的数量为循环输入一个**。我们检查这些节点，直到找到具有`null` nodeValue 的节点，这意味着它是一个标签(而不是一段文本)，它的名字是`playlist`。这个节点存储在一个名为`topLevel`的变量中。然后我们从**中脱离出来，进入**循环。**

接下来，我们为循环输入另一个**，以便检查其子循环:**

`if (topLevel != null)    
 {    
   for (i = 0; i <= topLevel.childNodes.length; i++)    
   {    
     if (topLevel.childNodes[i].nodeName == "mp3file")    
     {    
       var track = topLevel.childNodes[i].attributes["track"];`

对于每个子节点，我们检查它是否是一个`mp3file`标签，然后用标签的`track`属性填充一个变量，这是一个 MP3 文件的名称。

最后，我们使用 push 方法填充`tracklist`数组，将文件名添加到数组的开头。

`_root.tracklist.push (track);`

它负责创建播放列表。现在，当点击`randomplayer`电影剪辑时，下面的代码开始运行:

```
randomplayer.onPress = function ()     

{     

  stopAllSounds ();     

  var trackNo = randomBetween (0, _root.tracklist.length - 1);     

  _root.track = new Sound ();     

  _root.track.loadSound (_root.tracklist[trackNo], true);     

  _root.checkLoaded = setInterval (playTrack, 500);     

};
```

首先，我们使用`stopAllSounds`函数停止所有当前运行的声音。然后我们用一个随机数填充一个名为`trackNo`的变量，以便在我们的`tracklist`数组中选择一首曲目。

然后我们创建一个新的`Sound`对象，并使用我们刚刚填充的`trackNo`变量加载一个随机的 MP3 文件。

最后，我们使用`setInterval`函数每半秒钟调用一次`playTrack`函数。我们将间隔标识符存储在电影根目录下一个名为`checkLoaded`的变量中，这样我们就可以在适当的时候取消它。

现在，剩下要讨论的就是`playTrack`函数:

```
function playTrack ()     

{     

  var track = _root.track;     

  if (track.getBytesLoaded () == track.getBytesTotal () &&     

      track.duration > 0)     

  {     

    clearInterval (_root.checkLoaded);     

    trackID3Info.text = "";     

    trackID3Info.text += "Title: " + track.id3.songname + newline;     

    trackID3Info.text += "Artist: " + track.id3.artist + newline;     

    trackID3Info.text += "Album: " + track.id3.album + newline;     

    trackID3Info.text += "Year: " + track.id3.year + newline;     

    trackID3Info.text += "Comments: " + track.id3.comment +     

        newline;     

  }     

}
```

每次调用这个函数时，我们都会检查 MP3 文件是否加载成功。一旦完成，我们取消设置为每半秒调用一次函数的间隔。然后我们可以用 MP3 文件中的 ID3 标签信息填充动态文本字段。

***ID3 v1.x 注意事项***
由于 Flash Player 6 和 Flash MX 仅支持 ID3 v1.0 和 v1.1 标签，我们必须确保 MP3 文件已完全加载，然后才能尝试从中提取 ID3 标签信息。这一点非常重要，因为 ID3 标签数据位于 MP3 文件的末尾。

在 Flash MX 2004 中，可以为 Flash Player 7 生成内容，并支持位于 MP3 文件开头的 ID3 v2.2 和 v2.4 标签信息，访问这些信息要容易得多。当我们修改这个例子时，我们将看到这对 Flash 开发人员意味着什么。

*   保存您的文档并预览您的工作。

当电影加载时，XML 文件被导入，`tracklist`数组被来自`playlist.xml`文件的所有轨道信息填充。单击`randomplayer`电影剪辑加载一个 MP3 文件，并在动态文本字段中显示其 ID3 标签数据。

##### 修改

正如我们之前讨论的，在 Flash Player 7 中引用 ID3 标记数据要容易得多。该播放器支持 v2.2 和 v2.4 ID3 标签，这些标签存储在 MP3 文件的开头而不是结尾。

***仅限 Flash MX 2004！***

由于您只能使用 Flash MX 2004 生成 Flash Player 7 SWF 文件，因此此修改仅适用于 Flash MX 2004。

要编辑此效果，请在代码存档中选择`locate mp3â€“mx2004.fla`。

我们只需要对影片进行一些简单的修改，就可以让 Flash MX 2004 读取 ID3 v2.2 和 2.4 标签信息。

*   选择动作层的第一帧，然后从动作面板中找到并删除`playTrack`功能。已经不需要了！*   用以下代码替换`randomPlayer`电影剪辑的 onPress 事件处理程序代码:

**例 5.13。`mp3â€“mx2004.fla`行动:1(节选)**

```
randomPlayer.onPress = function ()     

{     

  stopAllSounds ();     

  var trackNo = randomBetween (0, trackInfo.length - 1);     

  var track = new Sound ();     

  track.onID3 = function ()     

  {     

    trackID3Info.text = "";     

    trackID3Info.text += "Title: " + track.id3.TIT2 + newline;     

    trackID3Info.text += "Artist: " + track.id3.TPE1 + newline;     

    trackID3Info.text += "Album: " + track.id3.TALB + newline;     

    trackID3Info.text += "Year: " + track.id3.TYER + newline;     

    trackID3Info.text += "Comments: " + track.id3.COMM +     

        newline;     

  };     

  track.loadSound (tracklist[trackNo], true);     

};
```

这段代码本质上与前面的一样，但是它使用一个事件处理程序来处理 ID3 标记信息，而不是每半秒钟调用一个函数来检查它。

在我们创建了新的`Sound`对象(`track`)之后，我们引用一个新的事件处理程序，称为 onID3，它在 Flash Player 7 中可用。当检测到已加载的 MP3 文件的新 ID3 数据时，将调用该函数，这意味着在检查 ID3 标签数据之前，我们不需要确认 MP3 文件已完全加载。一旦调用了 onID3 事件处理程序，我们就可以继续用 ID3 2.0 标签填充`trackID3Info`动态文本字段。就这么简单！

***ID3 引用***

有关 Flash MX 2004 中受支持的 ID3 标签的完整列表，请在帮助面板中搜索`Sound.ID3`。

*   保存并预览您的工作。

现在，当您单击`randomPlayer`电影剪辑时，MP3 文件开始播放，ID3 标记数据几乎立即显示在动态文本字段中。您不再需要像在前面的示例中那样等待 MP3 文件完全加载。

##### 结论

谁说你对声音剪辑所能做的就是播放它们？当然不是我！我希望我们已经在这一章中涵盖了足够多的材料，以激发你创造一些真正有趣的基于声音的界面、实验效果和其他项目的欲望。

现在是时候看看我们在 Flash 中使用视频效果能完成什么了！

这标志着我们系列的结束，该系列包含 SitePoint 的[Flash 选集:酷效果&实用动作脚本](https://www.sitepoint.com/books/phpant1/)中的三个章节，其中包含 60 多个面向 Flash 开发人员和设计人员的实用动作脚本解决方案。

这本书总共有 10 章，提供了 60 多种 Flash 常见问题的解决方案。这本 455 页的参考资料涵盖了 Flash 项目所需的所有解决方案，从文本和视频效果，到在应用程序中调试和使用外部数据。书中包含的所有代码都可以在免费下载的档案中找到。

有关更多信息，[请参见本书第](https://www.sitepoint.com/books/phpant1/)页。

## 分享这篇文章