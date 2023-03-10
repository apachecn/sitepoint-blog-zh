# HTML5 网络音频 API 教程:构建虚拟合成器垫

> 原文：<https://www.sitepoint.com/html5-web-audio-api-tutorial-building-virtual-synth-pad/>

万维网联盟的 [Web Audio 工作草案](http://webaudio.github.io/web-audio-api/)是一个高级 API，允许开发人员处理、合成和分析 HTML5 游戏或虚拟乐器等 Web 应用程序中的音频信号。

Web Audio 使用 AudioContext 接口来表示 AudioNodes。例如，在 AudioContext 中，一个音频文件连接到一个处理节点，该节点又连接到一个目的地，如您笔记本电脑上的扬声器。AudioContext 中的每个节点都是模块化的，因此 web 开发人员可以插入(或拔出)节点，就像蹒跚学步的孩子将乐高积木放在适当的位置来构建相对更复杂的结构一样。

熟悉 Web Audio API 的最好方法之一就是简单地使用它。在这篇文章中，我将描述如何建立一个非常基本的虚拟合成器垫，将播放音频样本，并提供一个基本的混响功能。这个 HTML synth pad 将远离专业音乐家使用的音调生成工具，但它将向我们展示如何:

*   创建音频上下文
*   加载音频文件
*   播放音频文件
*   添加音量控制
*   循环音频样本
*   停止音频播放
*   创建混响效果
*   创建音频过滤器

SitePoint 会不时删除存放在不同 HTML 页面上的多年前的演示。我们这样做是为了降低过时代码的风险，因为暴露的漏洞会给我们的用户带来风险。谢谢你的理解。

## 在我们的 HTML 中创建合成板

这个非常基本的虚拟合成板将出现在 web 浏览器中，所以让我们从标记开始，向页面添加四个“板”。

我通过 Google 的内容交付网络包含了 jQuery JavaScript 库。Web Audio API 并不需要 jQuery，但是它强大的选择器使得与 HTML pads 的交互更加容易。我还链接到一个本地 JavaScript 文件，该文件将包含使用 Web Audio API 的代码。

我已经为每个面板分配了一个数据属性，其中包含每个面板的相关声音文件的信息。下面是相关的 HTML:

```
<section id="sp">
<div id="pad1" data-sound="kick.wav"></div>
<div id="pad2" data-sound="snare.wav"></div>
<div id="pad3" data-sound="tin.wav"></div>
<div id="pad4" data-sound="hat.wav"></div>
</section>
```

我使用 CSS 在一个 2×2 的网格中布置四个 pad，因为这是一个小型 synth pad 的标准配置。我为`<section>`元素设置了一个宽度值，并让每个“pad”元素显示为内嵌块。

## 创建音频上下文

让我们开始编写脚本。我用一行代码创建了一个新的音频上下文。

```
var context = new AudioContext();
```

## 正在加载音频文件

下一个任务是编写一个加载音频文件的函数。该功能将:

*   接受音频文件的 URL
*   通过 XMLHttpRequest 加载文件
*   解码音频以在 AudioContext 中使用
*   提供一些访问解码源的方法。

这是:

```
function loadAudio( object, url) {

var request = new XMLHttpRequest();
request.open('GET', url, true);
request.responseType = 'arraybuffer';

request.onload = function() {
context.decodeAudioData(request.response, function(buffer) {
object.buffer = buffer;
});
}
request.send();
}
```

我为我们的虚拟合成器板编写的`loadAudio`函数接受两个参数。第一个参数是 pad 对象。第二个参数是函数将要加载的声音文件的 URL。

`request`变量被赋予一个新的 XMLHttpRequest 对象。我们将三个参数传递给请求的`open()`方法，指定通信的方法(在本例中为 GET)、音频文件的 URL 以及“true”来指定我们需要一个异步请求。

请求的响应类型设置为“arraybuffer”来处理二进制音频文件。

```
var request = new XMLHttpRequest();
request.open('GET', url, true);
request.responseType = 'arraybuffer';
```

当文件加载时，脚本将调用一个匿名函数，该函数又调用 AudioContext 的`decodeAudioData()`方法。此方法将异步解码音频文件。

`decodeAudioData()`方法有两个必需的参数。第一个是要解码的音频文件。在我们的脚本中，该文件存储为“request.response”。第二个必需的参数是回调函数。对于回调函数，我使用了第二个匿名函数向 pad 对象添加一个属性。这个新属性将是一种访问音频源节点的简单方法。

```
request.onload = function() {
context.decodeAudioData(request.response, function(buffer) {
object.buffer = buffer;
});
}
```

当然，请求的`send()`方法也被添加到脚本中。

## 点击键盘时播放音频文件

每个虚拟合成器面板在被点击时都应该播放一个音频文件，因此必须有一种方法将面板和声音关联起来。

有几种方法可以管理声音-面板关系，但最终，我决定扩展`<div>`元素对象，将音频相关属性添加到面板`<div>`本身，作为建立上述关联的一种方式。

因此，`addAudioProperties()`函数接受一个填充元素对象参数，并向该对象添加三个属性。当垫被“播放”时，添加第四个属性

```
function addAudioProperties(object) {
object.name = object.id;
object.source = $(object).data('sound');
loadAudio(object, object.source);
object.play = function () {
var s = context.createBufferSource();
s.buffer = object.buffer;
s.connect(context.destination);
s.start(0);
object.s = s;
}
}
```

函数中的第一行设置“name”属性的值，以便它匹配 pad 元素的`id`属性，具体来说就是“pad1”、“pad2”、“pad3”和“pad4”。

```
object.name = object.id;
```

函数中接下来的两行设置了“source”属性，以匹配我在 pad 的每个`<div>`元素中包含的 HTML `data-sound`属性的值，并将对象和源传递给`loadAudio`函数，有效地将声音文件加载到缓冲区。您可以将缓冲区视为系统内存中保存声音的地方，直到您准备好播放它们。

```
object.source = $(object).data('sound');
loadAudio(object, object.source);
```

接下来，该函数给 pad 对象一个`play`方法。这个方法有五个任务。

*   它调用 AudioContext 的`createBufferSource`方法，创建一个新的音频缓冲源节点
*   它设置节点的 source 属性
*   它将音源连接到电脑的扬声器
*   它会播放声音
*   它将音频源附加到 pad 对象的`s`属性

下面是函数:

```
object.play = function () {
var s = context.createBufferSource();
s.buffer = object.buffer;
s.connect(context.destination);
s.start(0);
object.s = s;
}
```

让我们更详细地考虑其中的几个任务。

首先，`createBufferSource()`方法在 AudioContext 中放置一个新节点。

其次，新节点连接到`context.destination`。这个目的地是一个特殊的节点，代表系统的默认声音输出。通常，这将是您电脑的默认扬声器，或者可能是一对插入您电脑的耳机。

还要注意，我使用了 jQuery 选择器和 jQuery `data()`方法来使访问`data-sound`属性变得更加容易。

现在，我们需要将我们的新功能和音频环境投入使用。

我使用 jQuery 创建了众所周知的匿名文档就绪函数，该函数会在页面加载时自动调用:

```
$(function() {

});
```

当页面加载后，我想继续扩展 pad 元素对象。这段代码使用 jQuery 选择每个 pad 元素并遍历每个元素，对每个元素调用`addAudioProperties()`函数。

```
$('#sp div').each(function() {
addAudioProperties(this);
});
```

文档就绪功能也开始监听 pad 元素上的点击事件。当点击事件发生时，虚拟合成器面板调用面板元素对象的`play()`方法。

```
$('#sp div').click(function() {
this.play();
});
```

这是到目前为止包含所有部分的文档就绪函数。

```
$(function() {
$('#sp div').each(function() {
addAudioProperties(this);
});

$('#sp div').click(function() {
this.play();
});
});
```

保存所有文件并在 Chrome、Firefox、Safari 或 Opera 中加载虚拟 synth pad 后，您现在应该有了一个功能性 synth pad。当你点击一个键盘时，会有声音播放。

## 添加音量控制

虽然虚拟合成板是功能性的，但它并不十分有趣。我们需要添加一些基本的控件，从音量控件开始。

这个控件将需要一点额外的 HTML 和 CSS 来添加一个控制面板部分和四个控件 div 元素，在我们现有的 pad 标记下面。每个控制面板元素的 HTML 如下所示:

```
<div data-pad="pad1">
<h2>TL Control</h2>
<h3>top left pad</h3>
<label for"volume 1">Volume</label>
<input type="range" min="0" max="5" step="0.1" value="1" data-control="gain" name="volume1">
</div>
```

注意，我为音量控制使用了一个范围输入元素。每个输入元素都有一个值为“gain”的`data-control`属性。在 Web Audio API 中，增益节点接口有效地表示音量的变化。

我们需要将增益或音量控制添加到 pad 元素对象中。这一增加将需要:

*   一个新的增益节点
*   更新`play()`方法以通过增益节点路由音频源。

AudioContext 有一个创建增益节点的简单方法。

```
object.volume = context.createGain();
```

在`play()`方法中，我简单地将源连接到增益节点，然后将增益节点连接到目的地。

```
s.connect(object.volume);
object.volume.connect(context.destination);
```

更新后的`addAudioProperties()`函数只增加了两行，如下面代码中的注释所示:

```
function addAudioProperties(object) {
object.name = object.id;
object.source = $(object).data('sound');
loadAudio(object, object.source);
object.volume = context.createGain(); // new line
object.play = function () {
var s = context.createBufferSource();
s.buffer = object.buffer;
s.connect(object.volume);
object.volume.connect(context.destination); // new line
s.start(0);
object.s = s;
}
}
```

在文档就绪函数中，我将添加一些代码来监控音量输入并更新音量。你会注意到我使用了一个 JavaScript `switch`语句，目前，这有点类似于使用 jackhamp 在墙上钉一个图钉，但我预见到有一天我们的控制面板中会有三个范围输入。

```
$('#cp input').change(function() {
var v = $(this).parent().data('pad'),
pad = $('#' + v)[0];
switch ($(this).data('control')) {
case 'gain':
pad.volume.gain.value = $(this).val();
break;
default:
break;
}
});
```

这个代码片段有四项任务。

*   它监控控制面板输入
*   它识别哪个垫与音量控制相关联
*   它使用 switch 语句来识别输入的目的
*   它会改变音量

jQuery 有一个`change()`方法，当其中一个 volume range 输入元素发生任何变化时就会触发该方法。`change()`方法接受一个回调函数作为参数，允许脚本采取一些行动——比如改变音量。

在控件的 HTML 中，我放置了一个数据属性来标识哪个虚拟 synth pad 与给定的控件相关联。pad 值(“pad1”、“pad2”、“pad3”或“pad4”)存储在变量`v`中，用于识别正确的合成器 pad。

```
$('#cp input').change(function()...
```

第二个变量`pad`被分配给填充元素对象。jQuery 允许这种连接选择器，其中“#”与填充值组合，例如“pad1”，被选择为“#pad1”

```
pad = $('#' + v)[0];
```

JavaScript `switch`语句考虑范围输入的`data-control`属性。当`data-control`属性的值为“gain”时，代码会更新 pad 元素对象的`volume.gain.value`属性，从而改变音量。

```
switch ($(this).data('control')) {
case 'gain':
pad.volume.gain.value = $(this).val();
break;
default:
break;
}
```

在这一点上，虚拟合成器垫有功能音量控制。

## 添加音频循环功能

虚拟合成器面板需要能够重复播放单个音频样本。所以我们要在控制面板上添加一个“循环”按钮。此循环功能将在结束时再次播放相关的音频样本。

我们需要再添加一点 HTML 来显示“循环”按钮。

```
<button type="button" class="loop-button" data-toggle-text="End Loop" value="false">Loop</button>
```

记下按钮的类、值和数据属性，因为所有这些都将在我们的 JavaScript 中引用。

为了方便循环特性，我对`addAudioProperties()`函数做了三处修改，向对象添加了一个新的`loop`属性；将源的`loop`属性设置为`play()`方法中 pad 对象的`loop`属性的值；并增加了一个`stop()`方法。记住，停止一个音频源也是我们在文章开头提到的目标之一，而且真的就这么简单。

```
function addAudioProperties(object) {
object.name = object.id;
object.source = $(object).data('sound');
loadAudio(object, object.source);
object.volume = context.createGain();
object.loop = false;
object.play = function () {
var s = context.createBufferSource();
s.buffer = object.buffer;
s.connect(object.volume);
object.volume.connect(context.destination);
s.loop = object.loop;
s.start(0);
object.s = s;
}
object.stop = function () {
if(object.s) object.s.stop();
}
}
```

在文档就绪函数中，我添加了一些代码来监听按钮点击。这个代码有七个任务。

*   识别相关的焊盘
*   将一个变量设置为按钮的文本值，在本例中为“Loop”
*   设置一个等于 pad div 元素对象的变量
*   使用 switch 语句来标识按钮的用途
*   停止播放音频源
*   用数据属性的值交换按钮文本
*   设定凸台元素对象的循环值

代码如下:

```
$('#cp button').click(function() {
var v = $(this).parent().data('pad'),
toggle = $(this).text(),
pad = $('#' + v)[0];

switch ($(this)[0].className) {
case 'loop-button':
pad.stop();
$(this).text($(this).data('toggleText')).data('toggleText', toggle);
($(this).val() === 'false') ? $(this).val('true') : $(this).val('false');
pad.loop = ($(this).val() == 'false') ? false : true;
break;
default:
break;
}
});
```

让我们更详细地看一下这些步骤。

首先，变量`v`被设置为焊盘名称的值。这正是我在上面添加音量控制时使用的技术。

```
var v = $(this).parent().data('pad'),
```

接下来的两个变量被赋予按钮文本的值，在本例中分别是“Loop”和 pad element 对象。jQuery 使得这些选择变得非常容易。

```
toggle = $(this).text(),
pad = $('#' + v)[0];
```

`switch`语句查看按钮的类名。我使用类名作为识别按钮用途的一种方式，如果你愿意的话。这里的`switch`语句又有点矫枉过正，但是我知道我们要在虚拟合成器面板上再添加两个按钮，所以现在使用它可以省去我们以后的麻烦。

```
switch ($(this)[0].className) {
case 'loop-button':
pad.stop();
$(this).text($(this).data('toggleText')).data('toggleText', toggle);
($(this).val() === 'false') ? $(this).val('true') : $(this).val('false');
pad.loop = ($(this).val() == 'false') ? false : true;
break;
default:
break;
}
```

“loop-button”案例的 switch 语句中的第一行调用 pad element 对象的`stop()`方法，这是我刚刚添加的。

如果你不是很熟悉 jQuery，下一行代码可能看起来很复杂。

```
$(this).text($(this).data('toggleText')).data('toggleText', toggle);
```

第一部分是一个简单的 jQuery 选择器，它捕获按钮元素(即“this”)。

这里的`text()`方法将按钮文本的值设置为按钮的“数据-切换-文本”属性的值。具体来说，这将使按钮显示为“结束循环”而不是“循环”

继续往下看，`data()`方法用于将`data-toggle-text`属性的值设置为变量 toggle 的值，就在不久前，我在修改按钮文本之前将其设置为该文本的值。实际上，我已经有了按钮文本，最初是“循环”，用`data-toggle-text`属性的值交换位置，最初是“结束循环”每次点击该按钮，“循环”和“结束循环”将交换位置。

接下来的两行代码一起更新 pad element 对象的 loop 属性。

```
($(this).val() === 'false') ? $(this).val('true') : $(this).val('false');
pad.loop = ($(this).val() == 'false') ? false : true;
```

条件三元运算符测试按钮的值。如果该值当前为 false，则将该值更改为 true。同样，如果当前值为 true，它将被更改为 false——因为按钮在单击之前的值代表了用户意图的反面。

看起来好像我现在可以将 pad 元素对象的 loop 属性的值设置为按钮的值，但这并不完全有效。按钮的值是一个字符串，但是 loop 属性需要一个布尔值。因此，我使用了第二个三元运算符来传递正确的布尔值。我想我也可以改变类型。

虚拟合成器面板现在有一个功能循环功能。

## 创建混响效果

在 Web Audio API 中，您可以使用卷积器节点创建混响效果。卷积器节点对源音频执行线性卷积。这个节点不涉及声音科学，它基本上获取您的源音频，将其与脉冲响应声音文件进行比较，并根据比较结果产生新的声音。您可能会认为脉冲响应声音文件是给定空间(如大房间)回声方式的表征。

对于虚拟合成器垫，我使用一个脉冲响应文件代表一个相当大的大厅。这个脉冲响应文件来自 Github 上的[克里斯·威尔逊的网络音频 API Playground 项目，在麻省理工学院的许可下可以免费使用。顺便说一下，克里斯·威尔逊是谷歌的开发者拥护者，也是](https://github.com/cwilso/WebAudio)[网络音频 API 工作草案](http://webaudio.github.io/web-audio-api/)的编辑。

和以前一样，我需要一些额外的 HTML 来在虚拟合成器面板页面上放置一个混响按钮。这里的 HTML 几乎与循环按钮的 HTML 相同。

```
<button type="button" class="reverb-button" data-toggle-text="No Rvrb" value=false>Reverb</button>
```

添加此节点的下一步是添加一个新函数，该函数将加载脉冲响应音频文件。该功能将创建一个混响对象，然后使用`laodAudio`功能将脉冲响应声音添加到缓冲区。这里没有新的概念。

```
function reverbObject (url) {
this.source = url;
loadAudio(this, url);
}
```

在`addAudioProperties()`函数中，我需要添加一行代码来创建一个属性来表示混响。

```
object.reverb = false;
```

pad div 元素对象的`play()`方法也需要更新。此时，音频源连接到增益节点，增益节点连接到扬声器。当用户单击混响按钮时，我们需要将卷积器节点插入到该链中，以便音频源连接到增益节点，增益节点连接到卷积器节点，卷积器节点连接到扬声器。

看一看这些变化之前的`play()`方法。

```
object.play = function () {
var s = context.createBufferSource();
s.buffer = object.buffer;
s.connect(object.volume);
object.volume.connect(context.destination);
s.loop = object.loop;
s.start(0);
object.s = s;
}
```

我将连接增益节点“object.volume”和扬声器的代码行替换为 if-else 结构。

```
object.play = function () {
var s = context.createBufferSource();
s.buffer = object.buffer;
s.connect(object.volume);
if (this.reverb === true) {
this.convolver = context.createConvolver();
this.convolver.buffer = irHall.buffer;
this.volume.connect(this.convolver);
this.convolver.connect(context.destination);
} else if (this.convolver) {
this.volume.disconnect(0);
this.convolver.disconnect(0);
this.volume.connect(context.destination);
} else {
this.volume.connect(context.destination);
}
s.loop = object.loop;
s.start(0);
object.s = s;
}
```

`if`语句的第一部分，检查以了解 pad 元素对象的 reverb 属性是否设置为 true。如果属性为 true，则创建卷积器节点，识别脉冲响应文件，并连接节点。

如果 reverb 属性为 false，该方法将检查以了解是否已经有一个卷积器节点连接到源。如果有一个卷积器节点，并且我们已经知道，reverb 属性为 false，那么用户必须单击 reverb 按钮来关闭它。因此，该脚本断开增益节点和卷积器节点，并将增益节点直接重新连接到扬声器。

如果 reverb 属性为 false，并且没有现有的卷积器节点，则增益节点将直接连接到扬声器。

混响特性也必须连接到 jQuery 文档就绪函数中。

这是文档就绪函数中监听循环按钮的部分，因为我们现在已经编写了虚拟合成器面板。

```
$('#cp button').click(function() {
var v = $(this).parent().data('pad'),
toggle = $(this).text(),
pad = $('#' + v)[0];
$(this).text($(this).data('toggleText')).data('toggleText', toggle);
($(this).val() === 'false') ? $(this).val('true') : $(this).val('false');
switch ($(this)[0].className) {
case 'loop-button':
pad.stop();
pad.loop = ($(this).val() == 'false') ? false : true;
break;
default:
break;
}
});
```

只需在`switch`语句中添加一个新案例。这个新案例的行为非常类似于为循环按钮创建的代码:

```
case 'reverb-button':
pad.stop();
pad.reverb = ($(this).val() == 'false') ? false : true;
break;
```

最后一步，在文档就绪函数中插入一行新代码，将脉冲响应文件添加到缓冲区。

```
irHall = new reverbObject('irHall.ogg');
```

虚拟合成器面板的混响功能现在起作用了。

## 创建音频过滤器

虚拟合成器垫开始变得有趣，但我想增加一个功能:音频过滤器。Web Audio API 有几种处理声音的方法，但我们将重点关注一个简单的例子，它有一个奇特的名称，具体来说就是一个低通双二阶滤波器节点。

在 HTML 中，我添加了一个新的“Filter”按钮和两个频率和质量范围输入。

```
<button type="button" class="filter-button" data-toggle-text="No Fltr" value=false>Filter</button>
<lable class="filter-group faded" for="frequency1">Frequency:</lable>
<input class="filter-group faded" type="range" min="0" max="10000" step="1" value="350" data-control="fq" name="frequency1">
<lable class="filter-group faded" for="quality1">Quality:</lable>
<input class="filter-group faded" type="range" min="0.0001" max="1000" step="0.0001" value="500" data-control="q" name="quality1">
```

务必注意频率和质量输入的范围。例如，品质因数被设置为双二阶滤波器节点的标称范围。也不是“褪色”类。当控件部分加载时，我希望音频过滤器的范围输入显示为淡入，表明它们不可用。如果你愿意，当用户点击过滤器按钮时，范围输入将变得生动起来。

pad 元素对象需要三个新属性来设置布尔值、设置默认频率值和设置默认质量值。当然，这些属性被添加到`addAudioProperties()`函数中。

```
object.filter = false;
object.fqValue = 350;
object.qValue = 500;
```

pad element 对象的`play()`方法也需要一些条件语句。这里的概念非常类似于我们添加了混响功能的`if`语句。代码需要根据是否使用循环、混响和过滤来正确连接节点。

```
if (this.filter === true) {
this.biquad = context.createBiquadFilter();
this.biquad.type = this.biquad.LOWPASS;
this.biquad.frequency.value = this.fqValue;
this.biquad.Q.value = this.qValue;

if (this.reverb === true) {
this.convolver.disconnect(0);
this.convolver.connect(this.biquad);
this.biquad.connect(context.destination);
} else {
this.volume.disconnect(0);
this.volume.connect(this.biquad);
this.biquad.connect(context.destination);
}

} else {
if (this.biquad) {
if (this.reverb === true) {
this.biquad.disconnect(0);
this.convolver.disconnect(0);
this.convolver.connect(context.destination);
} else {
this.biquad.disconnect(0);
this.volume.disconnect(0);
this.volume.connect(context.destination);
}
}
}
```

接下来，我们需要对文档就绪功能进行更改。第一个变化是增加了对过滤器按钮的支持。这将是`switch`语句中的一个新案例。注意，我添加了一点 jQuery 来切换我们添加到过滤器标签和输入中的“faded”类。

```
case 'filter-button':
pad.stop();
pad.filter = ($(this).val() == 'false') ? false : true;
$(this).parent().children('.filter-group').toggleClass('faded');
break;
```

我还向我们用于音量控制的输入开关语句添加了新的事例。

```
case 'fq':
pad.fqValue = $(this).val();
break;
case 'q':
pad.qValue = $(this).val();
break;
```

过滤器功能现在可以使用了。

## 结论和演示

本教程试图提供一个强大的 Web 音频 API 的基本介绍。如果你遵循它，你应该有一个虚拟的(和嘈杂的)合成器垫，以及更好地理解网络音频的基本功能。

你也可以[下载源文件](https://uploads.sitepoint.com/wp-content/uploads/2014/08/1407411016web-audio-tutorial-files.zip)或者[摆弄 CodePen](http://codepen.io/SitePoint/pen/f75d139536a5ee37467f344bcb96d8fc) 上的代码。有一点需要注意:CodePen 似乎会导致一个错误，阻止其中一个文件在 Chrome 中加载。这在 HTML 演示中不会发生，在 Firefox 的两个演示中应该都能正常工作。

所有现代桌面浏览器都支持网络音频 API，但 IE11 不支持。

## 分享这篇文章