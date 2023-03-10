# 逃脱的是:Flash 中的图像加载器和随机链接应用

> 原文：<https://www.sitepoint.com/image-loader-random-link-flash/>

**在我为收录在[Flash 选集:酷炫效果&实用动作脚本](https://www.sitepoint.com/books/flashant1/)中而开发的例子和解决方案中，有几个没有写进书里。从本教程开始，我将在接下来的几个月中介绍许多这样的解决方案。**

在这里，我们将看看两个非常实用的解决方案:使用组件加载图像，并为您的站点创建一个随机的“当日链接”显示。

在我们开始之前，请确保您[下载了我们将在这些示例中看到的所有代码](https://www.sitepoint.com/examples/gotaway/gotaway1.zip)的档案。

##### 使用组件加载图像

此解决方案需要:Flash MX 2004

我认为创建一个利用 Flash MX 2004 中的一些内置组件的动态图像加载器是一个好主意，但我想确保信息以结构化的方式显示。

下面是实际应用程序。

![1395_image1](img/55755accee6da909dd0fc044307f5a3c.png)

在此应用程序中，照片下拉菜单根据类别下拉列表中的选择进行填充。当用户选择一张照片时，图像会显示在右侧。

##### 设置场景

此示例的源文件是 Dynamicimageloader.fla 和 dynamic image loader _ graphics . zip，它们可以从代码归档中获得。

1.  创建一个新的 500 x 320 像素的 Flash 文档。接受默认帧速率，然后单击确定。

3.  重命名默认图层动作，并在下面添加另外两个图层，名为组件和背景。

5.  选择组件层的第一帧。将 ComboBox 组件的两个实例从“组件”面板的“UI 组件”部分拖到舞台上，将第二个实例放在第一个实例的下面。将实例命名为 parentCombo 和 childCombo。我们将使用几个数组中的值动态填充 childCombo 实例。

7.  选择 parentCombo 实例后，将下表中的信息添加到组件检查器的参数部分:

![1395_table1](img/065ba09e9bf354a75ff14cbfcd1a9e48.png)

类别组合框的标签将显示为 Phireworx、Work 和 Home。我们将使用“数据”参数来触发 childCombo 组合框组件的填充。

10.  从“组件”面板的“UI 组件”部分拖动三个标签组件。将一个放在`parentCombo`组合框上方，一个放在`childCombo`组合框上方，一个放在第一个标签的右侧。选择每个标签组件，并将属性检查器中的文本参数分别更改为类别、照片和图像。

12.  将一个加载器组件从“组件”面板的 UI 组件部分拖到组件层的第一帧中，并将该实例命名为`imageLoader`。
    我们将使用 Loader 组件作为稍后将动态加载的图像的存储库。

14.  使应用程序更有吸引力:在背景图层中添加一个圆角矩形，并带有平滑的线性渐变背景。锁定背景层。

我们都做完了。让我们添加将应用程序粘合在一起的 ActionScript！

##### 添加动作脚本

本例的 ActionScript 围绕三个多维数组展开，这些数组保存“父”类别的“子”信息。通过仔细的修改，以及附加到 ComboBox 组件的事件侦听器，我们可以触发一系列事件，从而显示相关的图像。

18.  选择动作层的第一帧，并在“动作”面板中添加以下代码:

```
var PhireworxInfo = new Array(["Phireworx Logo", "pw001.jpg"], ["Foto Logo", "pw002.jpg"], ["Juno Logo", "pw003.jpg"]); 

var WorkInfo = new Array(["Work Logo", "wk001.jpg"], ["Work Image 001", "wk002.jpg"], ["Work Image 002", "wk003.jpg"]); 

var HomeInfo = new Array(["Home Image", "hm001.jpg"], ["Home Image 001", "hm002.jpg"], ["Home Image 002", "hm003.jpg"]); 

function populateChild() { 

  childCombo.removeAll(); 

  var childArray = eval(parentCombo.getSelectedItem().data); 

  for (var i = 0; i < childArray.length; i++) { 

    childCombo.addItem(childArray[i][0]); 

  } 

} 

function showImage(img) { 

  loadMovie(img, "imageLoader"); 

} 

// Parent Combo Listener 

var parentComboListener = new Object(); 

parentComboListener.change = function() { 

  populateChild(); 

}; 

parentCombo.addEventListener("change", parentComboListener); 

// Child Combo Listener 

var childComboListener = new Object(); 

childComboListener.change = function() { 

  var childArray = eval(parentCombo.getSelectedItem().data); 

  showImage(childArray[childCombo.selectedIndex][1]); 

}; 

childCombo.addEventListener("change", childComboListener); 

// First run when loaded, populate combo's 

populateChild(); 

var childArray = eval(parentCombo.getSelectedItem().data); 

showImage(childArray[childCombo.selectedIndex][1]);
```

让我们再次获得代码手术刀，看看它在 ActionScript 操作台上是什么样子。首先，我们创建三个多维数组。

```
var PhireworxInfo = new Array(["Phireworx Logo", "pw001.jpg"], ["Foto Logo", "pw002.jpg"], ["Juno Logo", "pw003.jpg"]); 

var WorkInfo = new Array(["Work Logo", "wk001.jpg"], ["Work Image 001", "wk002.jpg"], ["Work Image 002", "wk003.jpg"]); 

var HomeInfo = new Array(["Home Image", "hm001.jpg"], ["Home Image 001", "hm002.jpg"], ["Home Image 002", "hm003.jpg"]);
```

这些数组包含图像的描述及其文件名，如下所示:

```
["Phireworx Logo", "pw001.jpg"]
```

请注意数组名称(`PhireworxInfo`、`WorkInfo`等)。)与 parentCombo 组合框组件中的“数据”值相同。您马上就会看到，我们使用 parentCombo 组合框中的“data”属性来触发`childCombo`组合框的填充。

然后我们向`parentCombo`组合框添加一个事件监听器，它等待它的值改变。

```
/ Parent Combo Listener 

var parentComboListener = new Object(); 

parentComboListener.change = function() 

{ 

  populateChild (); 

}; 

parentCombo.addEventListener("change", parentComboListener);
```

当值被注册为变化时，我们触发`populateChild()`功能。

```
function populateChild() 

{ 

  childCombo.removeAll(); 

  var childArray = eval(parentCombo.getSelectedItem().data); 

  for (var i = 0; i < childArray.length; i++) 

  { 

    childCombo.addItem(childArray[i][0]); 

  } 

}
```

首先，这个函数删除了`childCombo`组合框中的所有项目，使它变得漂亮整洁。

```
childCombo.removeAll();
```

然后，我们将`parentCombo`组合框的数据参数的内容转移到一个名为`childArray`的变量中，该变量基本上采用我们在代码的第 1-3 行定义的数组，并将其与`parentCombo`组合框的数据参数进行匹配。我们用它来填充`childArray`变量。

然后，我们使用一个基于数组长度的 for 循环将项目添加到`childCombo`组合框中。添加的项目构成了多维数组的第一部分(描述)。

```
childCombo.addItem(childArray[i][0]);
```

填充了`childCombo` ComboBox 后，我们可以继续处理值改变时会发生什么的代码部分。

我们再次设置了一个事件监听器，这次是针对`change`事件的`childCombo`实例。

```
// Child Combo Listener 

childComboListener = new Object(); 

childComboListener.change = function() 

{ 

  var childArray = eval(parentCombo.getSelectedItem().data); 

  showImage(childArray[childCombo.selectedIndex][1]); 

}; 

childCombo.addEventListener("change", childComboListener);
```

然而，这一次触发了一个不同的功能——`showImage()`功能。我们将多维数组的第二部分(图像名)作为参数传递给它。

注意:由于 ComboBox 的 selectedIndex 属性和`childCombo`实例的“data”参数都是基于数组的，我们可以使用`childCombo` ComboBox 的 selectedIndex 值来为当前分配的数组选择图像名称。

`showImage(childArray[childCombo.selectedIndex][1]);`

`showImage()`函数是 ActionScript 中最简单的部分。它只是触发了`loadMovie()`函数，将所讨论的图像加载到`imageLoader`加载器组件中:

```
function showImage(img) 

{ 

  loadMovie (img, "imageLoader"); 

}
```

当电影在没有选择的情况下第一次运行时，我们只需使用以下 ActionScript 触发子填充和图像检索:

```
// First run when loaded, populate combo's 

populateChild(); 

var childArray = eval(parentCombo.getSelectedItem().data); 

showImage(childArray[childCombo.selectedIndex][1]);
```

44.  将文档保存到您选择的位置并导出您的 SWF，确保在“ActionScript 版本”下拉列表中选择了“ActionScript 2.0”选项。

46.  将 dynamicimageloader _ graphics . zip 文件的内容从代码归档文件中提取到与导出的 SWF 相同的目录中。我们需要将图像提取到与 SWF 文件相同的目录中，因为它在 showImage()函数中被引用为位于相同的目录中

48.  预览导出的 SWF 文件。

这是一个伟大的技术！它也是可伸缩的——只需修改数组信息、`parentCombo`组合框(如果你需要更多的类别)和导入的实际图像。该技术利用了 Flash MX 2004 中的内置组件，并在 ComboBox 实例之间创建了一个巧妙的交互。

如果您正在处理较大的文件，您可以很容易地添加一个 ProgressBar 组件来为您提供关于加载了多少文件的反馈，并使用 XML 数据来填充父组合框和子组合框，但是我将由您来扩展这种效果！

##### 每日随机链接

![1395_image2](img/0c5f58256f0961893e92eea5ebbbff8f.png)

在这个例子中，我要做的是创建一个随机链接，该链接是从一个循环中的多维数组中随机选择的，然后使用简单的 JavaScript 行为在一个新的 chromeless 浏览器窗口中启动。这种效果非常好，并且利用了一些内置的 JavaScript 函数，比如关闭嵌入了 SWF 文件的父窗口，以及触发页面打印。

扩展这种效果有很大的空间，但这就是学习 Flash 的意义所在:尝试各种效果并根据自己的需要扩展它们。

要编辑这种效果，请在代码归档中找到 RandomLinkOfTheDay.fla。

##### 设置场景

1.  创建一个 510 像素宽、105 像素高的新 Flash 文档。将默认帧速率更改为 24 fps，然后单击“确定”。

3.  重命名默认层动作，并在其下添加另外两个层，称为界面和背景。

5.  创建一个背景矩形，将应用程序框在背景层的第一帧内，如错误所示！未找到参考源..锁定背景层。

7.  选择界面层的第一帧，并创建一个名为 linkMC 的新电影剪辑。将实例命名为 linkMC。在 linkMC 影片剪辑中嵌入一个大约 445 像素宽、18 像素高的动态文本框。将实例命名为 randomlink。

9.  选择“随机链接”文本框，然后在属性检查器中单击“字符”。单击指定范围复选框，并选择大写、小写、数字和标点符号。在“包含这些字符”部分下添加/和。单击确定。从属性检查器
    中单击“显示文本周围的边框”选项。稍后，我们将通过 ActionScript 创建的数组的一部分中的一系列 URL 将引用并填充这个动态文本框。

11.  将 linkMC 影片剪辑移至舞台中央，留出足够的空间在影片剪辑的上方和下方添加更多内容。

13.  创建一个新的动态文本框，其宽度大约为 160 像素，并与 linkMC 影片剪辑的右上边缘对齐。将实例命名为 reccount。从属性检查器中选择“字符”，单击“指定范围”复选框，然后选择大写、小写、数字和标点符号。单击确定。在“属性”检查器的色样上，将文本颜色更改为#990000。
    当 ActionScript 循环运行时，该动态文本框将不断更新，并将提供关于链接是否正在被选择的反馈。

15.  创建另一个大约 160 像素宽的动态文本框。将其与命名实例标题的 linkMC 影片剪辑的右下边缘对齐。从属性检查器中选择“字符”，指定范围复选框，然后选择大写字母、小写字母、数字和标点符号。单击确定。这个动态文本框将保存我们随机选择的数组的另一部分，并将保存“randomlink”动态文本框中 URL 的标题。

17.  在 linkMC 影片剪辑上方创建一个新的静态文本框。将它与剪辑的左上角边缘对齐，并添加当天的文本特色链接。

19.  将按钮组件的三个实例从“动作”面板的 UI 组件拖到界面层的第一个帧中。排列两个实例，使它们在 linkMC 影片剪辑的左下边缘组合在一起；将另一个与右下边缘对齐。从左到右命名实例`closeParent`、`printPage`、`windowLauncher`。

21.  选择每个按钮实例后，在属性检查器中从左到右更改标签参数，如下所示:关闭父项、打印、启动。在示例的后面，我们将使用这些按钮来执行某些 JavaScript 函数。

23.  保存您的工作。

我们已经添加了为效果设置场景所需的所有动态文本框和按钮，现在我们需要将 ActionScript 添加到电影中，以使它们融合在一起。

##### 添加动作脚本

本例的 ActionScript 探索了外部 JavaScript 函数的控制和 Flash 中多维数组的巧妙使用。事不宜迟，让我们开始添加 ActionScript 吧！

*   选择动作层的第一帧，并在“动作”面板中添加以下代码:

```
//Randomizer Function for Array  

function randomBetween(min, max) {  

  return (min+Math.floor(Math.random()*(max-min+1)));  

}  

//Create New Pseudo MultiDimensional Array  

RandomLinkArray = new Array(  

["Front Page", "https://www.phireworx.com/"],   

["Register for Free Commands", "https://www.phireworx.com/content/commands/registration.asp"],  

["Phireworx Tutorials", "https://www.phireworx.com/content/tutorials/default.asp"],  

["Crew Bios", "https://www.phireworx.com/content/crew/crew_bios.asp"],   

["Quick Tips", "https://www.phireworx.com/content/quicktips/default.asp"],  

["Tutorial 1", "https://www.phireworx.com/tutorial_jumper.asp?id=D2615715-D19B-49A8-91C2-52A0DA859E57"],  

["Tutorial 2", "https://www.phireworx.com/tutorial_jumper.asp?id=3E301402-DD6F-4A26-AC27-7E8C8FDCED9C"],  

["Tutorial 3", "https://www.phireworx.com/tutorial_jumper.asp?id=41163698-FD96-4DF4-A3E4-8217CBB80B22"]);  

//Set Initial LoopCount   

_root.linkMC.loopCount = 0;  

_root.linkMC.onEnterFrame = function() {  

  if (this.loopCount<=50) {  

    //Change Header Text Whilst Incrementing Throught the Loop  

    _root.reccount.text = "Selecting Link";  

    //Create Random Array Position  

    var placer = randomBetween(0, RandomLinkArray.length-1);  

    //Set the Random Link Text  

    this.randomlink.text = RandomLinkArray[placer][1];  

    //Set Random Link Title  

    _root.title.text = RandomLinkArray[placer][0];  

  } else {  

    //Control for Text Feedback Area  

    _root.reccount.text = "Link Selected";  

  }  

  this.loopCount++;  

};  

//Miscellaneous Button Functions  

windowLauncher.onPress = function() {  

  //Open New Browser Window with random array position  

  getURL("javascript:openNewWindow('"+_root.linkMC.randomlink.text+"','','scrollbars=yes,resizable=yes,width=600,height=600')");  

};  

closeParent.onPress = function() {  

  getURL("javascript:window.close()");  

};  

printPage.onPress = function() {  

  getURL("javascript:window.print()");  

};
```

让我们检查一下我们在做什么，因为这个脚本乍一看似乎有点复杂。首先，我们创建一个新的全局变量来保存循环计数的值，并添加我们常用的 randomBetween 函数。我们马上会看到，这个 loopcount 全局变量用于确保一个条件只循环给定的次数。

```
//Set Initial LoopCount   

loopCount = 0;  

//Randomizer Function for Array  

function randomBetween(min, max) {  

  return (min+Math.floor(Math.random()*(max-min+1)));  

}  

Next up, we create an array called RandomLinkArray.   

//Create New Pseudo MultiDimensional Array  

RandomLinkArray = new Array(  

["Front Page", "https://www.phireworx.com/"],   

["Register for Free Commands", "https://www.phireworx.com/content/commands/registration.asp"],  

["Phireworx Tutorials", "https://www.phireworx.com/content/tutorials/default.asp"],   

["Crew Bios", "https://www.phireworx.com/content/crew/crew_bios.asp"],  

["Quick Tips", "https://www.phireworx.com/content/quicktips/default.asp"],  

["Tutorial 1", "https://www.phireworx.com/tutorial_jumper.asp?id=D2615715-D19B-49A8-91C2-52A0DA859E57"],   

["Tutorial 2", "https://www.phireworx.com/tutorial_jumper.asp?id=3E301402-DD6F-4A26-AC27-7E8C8FDCED9C"],   

["Tutorial 3", "https://www.phireworx.com/tutorial_jumper.asp?id=41163698-FD96-4DF4-A3E4-8217CBB80B22"]);  

};
```

为了创建多维数组，我们将一个数组放在另一个数组中。我们使用这个结构来保存 URL 的标题和实际 URL，格式如下:

```
new Array (["Title", "URL"], ["Title", "URL"]);
```

这样，当引用一个随机数组元素时，我们可以同时获得标题和 URL。在这个例子中，我们使用来自 phireworx.com 的页面，由于 SWF 一加载就创建了`RandomLinkArray`数组，我们准备在 linkMC 电影剪辑的`onEnterFrame`事件处理程序中引用该数组。

```
_root.linkMC.onEnterFrame = function() {  

  if (loopCount<=50) {  

    //Change Header Text Whilst Incrementing Throught the Loop  

    _root.reccount.text = "Selecting Link";  

    //Create Random Array Position  

    var placer = randomBetween(0, RandomLinkArray.length-1);  

    //Set the Random Link Text  

    this.randomlink.text = RandomLinkArray[placer][1];  

    //Set Random Link Title  

    _root.title.text = RandomLinkArray[placer][0];  

  } else {  

    //Control for Text Feedback Area  

    _root.reccount.text = "Link Selected";  

  }  

  loopCount++;  

};
```

我们检查变量`loopCount`的值是否小于 50；如果是，我们输入条件中的代码。`loopCount`变量在循环结束时递增，当`loopCount`值小于 50 时，`reccount`动态文本框显示文本选择链接，当`loopCount`变量达到 50 时，前进到链接选择。

linkMC 影片剪辑的事件处理函数中的下一段代码负责选择一个随机数组位置，方法是使用`randomBetween()`函数用 0(数组中的第一个位置)和最终数组位置(我们使用数组长度来确定)之间的数字填充一个变量(placer)。

注意:当`randomBetween()`函数给生成的数字加 1 时，我们减去生成的随机数的一个长度

```
var placer = randomBetween(0, RandomLinkArray.length-1);
```

还记得我们是如何创造出`RandomLinkArray`的吗？下面的代码提取数组成员的 URL，并填充 randomlink 动态文本框。

```
this.randomlink.text = RandomLinkArray[placer][1];
```

以下代码提取标题并填充标题动态文本框:

```
_root.title.text = RandomLinkArray[placer][0];
```

这段代码位于 linkMC 电影剪辑的`onEnterFrame`事件处理程序中。因此，这种随机抽取数组成员并写入`randomLink`动态文本框和标题文本框的操作以电影的帧速率(24 fps)进行。此外，我们只循环了 50 次，而循环计数器全局变量的值小于 50。因此，在 linkMC 影片剪辑最终停留在一个随机值之前，我们有大约 2 秒钟的时间。

有一些漂亮的代码来创建一个随机的链接和标题当然很好，但是我们该怎么做呢？

因为我们想要在单击`windowLauncher`按钮时打开一个新的浏览器窗口，所以我们将下面的代码添加到`windowLauncher`按钮的 onPress 事件中。

```
windowLauncher.onPress = function() {  

  //Open New Browser Window with random array position  

  getURL("javascript:openNewWindow('"+_root.linkMC.randomlink.text+"','','scrollbars=yes,resizable=yes,width=600,height=600')");  

};
```

当按下`windowLauncher`按钮时，我们将调用一个名为`openNewWindow();`的外部 JavaScript 函数。这是一个打开窗口的简单函数。有了它，我们将传递我们用来填充 randomLink 动态文本框的随机 URL。

我们在外部调用的`openNewWindow()`函数包含在嵌入了 SWF 文件的页面的代码中，因此我们传递了 URL 和一些可选的窗口大小、滚动条和大小调整手柄的特征值。

能够调用普通浏览器支持的原生 JavaScript 函数，以及我们可能希望包含在包含 SWF 的网页中的任何自定义 JavaScript 函数，这非常棒。为此，我们必须添加以下代码，以允许浏览器窗口关闭，并允许用户通过按下相关按钮进行打印。

```
closeParent.onPress = function() {  

  getURL("javascript:window.close()");  

};  

printPage.onPress = function() {  

  getURL("javascript:window.print()");  

};
```

*   保存并预览您的工作。

这是一个令人愉快的循环效果，可以创建随机的 URL 并显示 URL 的类别。然而，尽管单击 Launch 按钮确实会在地址栏中打开一个加载了 JavaScript 的新浏览器窗口，但它不会产生正确的效果。

为了利用外部 JavaScript 函数，我们必须将 SWF 文件导出并嵌入到包含自定义函数的 HTML 页面中。

*   将 Flash 电影作为 RandomLinkOfTheDay.swf 导出到您选择的位置，确保将 ActionScript 版本设置为 ActionScript 2.0。

##### 嵌入到网页中

我们将要创建的网页的源文件名为 URLJumper.htm。它可以位于代码档案中。

*   打开 HTML 编辑器，创建一个新的。HTML 页面，并键入以下代码:

```
<html>  

<head>  

<title>Flash URL Jumper</title>  

<meta http-equiv="Content-Type" content="text/html; charset=iso-8859-1">  

<script language="JavaScript" type="text/JavaScript">  

<!--  

function openNewWindow(url,windowName,chrome) {   

  window.open(url,windowName,chrome);  

}  

//-->  

</script>  

</head>  

<body>  

<object classid="clsid:D27CDB6E-AE6D-11cf-96B8-444553540000" codebase="http://download.macromedia.com/pub/shockwave/cabs/flash/swflash.cab#version=6,0,29,0" width="550" height="110">  

  <param name="movie" value="RandomLinkOfTheDay.swf">  

  <param name="quality" value="high">  

  <embed src="RandomLinkOfTheDay.swf" quality="high" pluginspage="http://www.macromedia.com/go/getflashplayer" type="application/x-shockwave-flash" width="550" height="110"></embed>  

</object>  

</body>  

</html>
```

正如你所注意到的，我们已经以标准的方式在网页中嵌入了 SWF，但是我们还添加了`openNewWindow()`函数声明，它就在那里等着我们去调用它！

*   将您的网页另存为 URLJumper.htm，保存位置与之前导出的 RandomLinkOfTheDay.swf 相同*   双击 URLJumper.htm 文件预览效果。

现在，当您预览嵌入的 SWF 文件时，在它选择了一个随机 URL 后单击“启动”按钮将打开一个新的浏览器窗口——但没有我们在 Flash 中指定的大部分“chrome”大小！

单击“关闭父项”窗口，嵌入了 SWF 文件的浏览器页面将愉快地关闭(在警告您之后！).点击“打印”按钮将打开打印对话框。

这只是您可以调用的 JavaScript 函数的一小部分。当然，您可以调用任何自定义的 JavaScript 函数，这些函数与正在查看的 SWF 位于同一个页面中。

## 分享这篇文章