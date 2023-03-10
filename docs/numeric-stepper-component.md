# Flash 的隐藏宝石:NumericStepper 组件

> 原文：<https://www.sitepoint.com/numeric-stepper-component/>

**也许 Flash MX 2004 中最容易被忽略的组件之一隐藏在组件面板的 *UI 组件*部分，并带有一个无趣的名字 NumericStepper。**

也许你正在开发实验效果，需要屏幕上的控件来改变这些效果中的动态反馈。也许您正在构建允许用户快速选择数字数据的复杂表单。不管怎样，现在是时候看看 NumericStepper 组件了。

让我们看一个例子。下图中电影的所有动态都是由 NumericStepper 的实例控制的。

![1386_4](img/e20b5e33407273c10cc35d2d1b77f655.png)

NumericStepper 是一个非常有价值的组件，但最基本的是，它被设计为递增或递减一系列数字。我们可以获取和设置组件的几个属性。我们将在本文中探讨的这些极其有用的属性如下:

*   **`minimum`** :元件的最小值
*   **`maximum`** :组件的最大值

**`nextValue`** :组件的下一个增量值(只读)

*   **`previousValue`** :组件的前一增量值(只读)
*   **`stepSize`** :构件的增量变化值
*   **`value`** :组件当前选择的值

NumericStepper 组件的潜在用途包括:

*   用作表单元素，便于快速选择数值
*   用作日期选择元素(月、年等)
*   在实验应用中用于控制复杂值

除了方法、属性和事件之外，NumericStepper 和 ComboBox 组件之间的主要区别在于易用性。

虽然您可以使用 ComboBox 组件实现类似的效果，但 NumericStepper 组件允许您通过“向上”和“向下”箭头或键盘上的“向上”和“向下”键快速向上或向下增加选择。使用组合框，您需要单击组合框，等待它显示选项，然后从下拉列表中选择所需的值。

NumericStepper 解决方案显然比 ComboBox 快得多，其功能允许在一些独特的应用程序中轻松有效地实现和使用 NumericStepper。

##### 数字步进器基础

![1386_1](img/d68504e640e64a21153bdcab52c6d26c.png)

为了了解这个组件，让我们向舞台添加一个 NumericStepper 实例。然后，我们将用 NumericStepper 组件的几个属性填充一些动态文本框。

本例的 FLA 源文件名为`NumericStepperBasics.fla`，可以在[本文的可下载代码档案](https://www.sitepoint.com/examples/numericstepper/numericsteppersource.zip)中找到。

##### 搭建舞台

1.  首先创建一个新的 Flash 文档。接受默认的宽度、高度和帧速率设置。

3.  重命名默认层动作，并在其下添加另一个名为组件的层。将 NumericStepper 组件的一个实例从“组件”面板的“UI 组件”部分拖动到“组件”层的第一个帧中。将实例命名为`StepperMain`。

5.  选择 NumericStepper 组件实例，并在属性检查器中添加以下值:`maximum:100`、`minimum:0`；`stepSize:5`，`value:10`。

*注意:这里，我们已经通过属性检查器添加了组件的属性值，但是如果我们愿意，我们也可以通过 ActionScript 以编程方式添加这些值(稍后将详细介绍)。*

7.  Create two dynamic text boxes one above the other. Name the instances `previousTextBox` and `nextTextBox`.

    当我们单击 NumericStepper 组件时，这些文本框将用组件的`next`和`previous`值填充。我们稍后将添加实现这一点的 ActionScript。

9.  如果愿意，可以在组件和文本框中添加相关标签，如上面的屏幕截图所示。

我们继续吧。现在，我们将添加 ActionScript，它允许我们查看组件的运行情况…

##### 添加动作脚本

1.  选择动作层的第一帧，在“动作”面板中添加以下代码:

```
var palletteArray = new Array(); 

palleteArray = ["0x7FAAD4", "0xFFDF7F"]; 

function setTheme() { 

  _global.style.setStyle("themeColor", palleteArray[0]); 

  _global.style.setStyle("backgroundColor", palleteArray[1]); 

  _global.style.setStyle("fontSize", 11); 

  _global.style.setStyle("fontWeight", "bold"); 

  _global.style.setStyle("color", palleteArray[0]); 

} 

setTheme(); // Called at startup 

var stepsListener = new Object(); 

stepsListener.change = function() { 

  populateTextBoxes(); 

}; 

stepperMain.addEventListener("change", stepsListener); 

function populateTextBoxes() { 

  if ((stepperMain.value<stepperMain.maximum) && (stepperMain.value>stepperMain.minimum)) { 

    nextTextBox.text = stepperMain.nextValue; 

    previousTextBox.text = stepperMain.previousValue; 

  } 

}
```

我们在这里做的第一件事是创建一个名为'`palletteArray`'的新数组，我们用为我们的界面选择的几个颜色代码填充它:一个漂亮的淡黄色(`#7FAAD4`)和一个互补的蓝色(`#FFDF7F`)。

注意:创建一个颜色数组可以让我们在需要时更容易地切换这些颜色:我们只需改变不同数组成员的值。这个解决方案使我们避免了搜索代码，在找到十六进制值时改变它们。

然后我们有一个名为`setTheme()`的函数，在启动时调用。它使用我们之前设置的`palletteArray`数组为 NumericStepper 组件(或舞台上的任何其他 Flash MX 2004 组件)设置主题。

然后我们创建一个新的`listener`对象，监听 NumericStepper 组件值的变化。当检测到变化时，调用`populateTextBoxes()`函数。它将组件的`nextValue`和`previousValue`属性推送到相关的动态文本框中。

7.  Save the Flash document to a location of your choice and preview your work in Flash.

    如您所见，单击 NumericStepper 实例会增加或减少组件的值，并将`next`和`previous`值推入动态文本区域。

    这个例子向我们简单介绍了这个组件。我们现在可以在这些基础上创建更复杂的作品用于你的项目。

##### 日期选择

![1386_3](img/2c9c7849c0c7ad0c0c7bc395eeacf930.png)

本例的源文件是`NumericStepperDates.fla`。

此示例略有不同，因为它使用 NumericStepper 组件在应用程序中创建简单的日期控件。这些日期控件可能用在在线应用程序表单或 Flash 驱动的博客搜索应用程序中。在这个例子中，我们只是将一个函数的输出发送到输出面板。

##### 搭建舞台

我们将使用一些与前一个例子中相同的核心代码，并在此基础上创建一个不同的结果；此外，在此示例中，我们将使用 ActionScript 填充 NumericStepper 组件，而不是通过属性检查器对其内容进行硬编码。

1.  使用默认属性创建一个新的 Flash 文档，并将默认图层重命名为`Actions`。在它下面，再添加一层叫做`Components`的图层。

3.  将 NumericStepper 组件的两个实例从“组件”面板的“UI 组件”部分拖动到“组件”层的第一个帧中。将实例命名为`StepperYear`和`StepperMonth`。

5.  在每个组件上方添加相关的文本标签(例如，选择年份、选择月份)。

##### 添加动作脚本

现在是时候用我们想要的月份和年份的值填充组件了。当任一组件实例中的值发生更改时，选定的日期将被推送到“输出”面板。

1.  选择'`Actions`'图层的第一帧，并在动作面板中添加以下代码:

*注意:用粗体突出显示的代码与前面的例子有所不同。*

```
**var monthArray = new Array();  

monthArray = ["January", "February", "March", "April", "May", "June", "July", "August", "September", "October", "November", "December"];**  

var palletteArray = new Array();  

palleteArray = ["0x7FAAD4", "0xFFDF7F"];  

function setTheme() {  

  _global.style.setStyle("themeColor", palleteArray[0]);  

  _global.style.setStyle("backgroundColor", palleteArray[1]);  

  _global.style.setStyle("fontSize", 11);  

  _global.style.setStyle("fontWeight", "bold");  

  _global.style.setStyle("color", palleteArray[0]);  

}  

setTheme();  

var stepsListener = new Object();  

stepsListener.change = function() {  

  **pushDate();  

};  

stepperYear.addEventListener("change", stepsListener);  

stepperMonth.addEventListener("change", stepsListener);  

function pushDate() {  

  trace("The selected year is: "+stepperYear.value+  

    "; the selected Month is: "+monthArray[stepperMonth.value-1]);  

}  

function populateYearStepper() {  

  var now = new Date();  

  stepperYear.minimum = now.getUTCFullYear();  

  stepperYear.maximum = now.getUTCFullYear()+10;  

  stepperYear.stepSize = 1;  

  stepperYear.value = now.getUTCFullYear();  

}  

populateYearStepper();  

function populateMonthStepper() {  

  var now = new Date();  

  stepperMonth.minimum = 1;  

  stepperMonth.maximum = 12;  

  stepperMonth.stepSize = 1;  

  stepperMonth.value = now.getUTCMonth()+1;  

}  

populateMonthStepper();**
```

最初，我们创建一个名为`monthArray`的新数组，它保存一年中各月的名称；我们将在后面的示例中使用它从`pushDate()`函数中获取月份名称。

*注意，在这个例子中，我们将组件的两个实例绑定到同一个`listener`对象。每当任一实例的值发生变化时，就会调用`pushDate()`函数。*

```
stepperYear.addEventListener("change", stepsListener);  

stepperMonth.addEventListener("change", stepsListener);
```

在这个例子中我们有两个函数(`populateYearStepper()`和`populateMonthStepper()`，这两个函数都在启动时被调用。它们分别动态填充`stepperYear`和`stepperMonth` NumericStepper 实例。

`populateYearStepper()`函数创建一个新的`date`对象，然后使用`getUTCFullYear()`方法的各种迭代将`minimum`、`maximum`、`stepSize`和`value`属性推入到`stepperYear`组件实例中。这会产生一个`minimum`值，即当前年份，和一个`maximum`值，即当前年份加 10。它将步长设置为 1，并将 NumericStepper 实例的初始值设置为当前年份。

类似的过程发生在`populateMonthStepper()`函数中，该函数用月份号(1-12)填充`stepperMonth`实例。然后，它使用以下代码将初始值设置为当前月份:

```
now.getUTCMonth()+1
```

`getUTCMonth()`方法返回一个 0 到 11 之间的整数，表示`Date`对象中的月份值(一月将显示为 0，十二月显示为 11，以此类推。).我们在此基础上加 1，得到我们希望在 NumericStepper 中显示的数字。

当 NumericStepper 实例的任何一个值发生变化时，就会调用`pushDate()`函数。我们使用这个函数来追踪所选择的日期。

追溯年份非常简单，因为我们可以使用`stepperYear.value.`轻松获得`stepperYear` NumericStepper 实例的值。但是，为了将从零开始的月份值转换成月份名，我们需要在我们之前启动的`monthArray`数组中查找它的位置。

```
trace("The selected year is: "+stepperYear.value+  

  "; the selected Month is: "+monthArray[stepperMonth.value-1]);
```

16.  Save your Flash document and preview your work.

    当您单击 NumericStepper 实例中的任何一个时，就会调用`pushDate()`函数，所选的月份和年份会以一种方便的方式显示在输出面板上。

NumericStepper 组件可以从这个示例中派生出多次迭代和多次使用，所以我建议您尝试一下，看看如何在自己的项目中应用这个组件。

##### 实验控制

正如我们已经提到的，NumericStepper 组件的另一个有趣的应用是将其用作实验效果的控件，如本教程开头的截图所示。

在这个例子中，我们将通过`createEmptyMovieClip()`和`createTextField()`方法动态地使用组件来创建文本字段的多个动态迭代。

NumericStepper 实例的 value 属性将被检索并用于循环访问，这将创建一系列文本字段。在组件值的每次后续更改中，屏幕会重新绘制，显示反映组件中值的文本字段的数量。

![1386_2](img/5661e3f2490d4ae261eddb21a67385e0.png)

本例的源文件名为`NumericStepperDynamicText.fla`，可以在本文的代码档案中找到。

*注意:代码归档包括这个 FLA 的另一个例子，其中动态创建被随机放置在舞台上。这个文件的名字叫做`NumericStepperDynamicRandomText.fla`*

虽然这个示例展示了如何动态控制电影剪辑和文本字段的创建和销毁，但我将浏览一下动态创建代码。相反，我将专注于示例中的逻辑流程，这样您就可以继续剖析示例并根据需要进行修改。

##### 搭建舞台

1.  使用默认属性创建一个新的 Flash 文档，并将默认图层重命名为`Actions`。在它下面加一层叫做`Components`。

3.  将 NumericStepper 组件的一个实例从“组件”面板的“UI 组件”部分拖动到“组件”层的第一个帧中。将实例命名为`StepperMain`。

5.  With the NumericStepper component instance selected, add the following values within the Property Inspector: `maximum:100`, `minimum:0`; `stepSize:5`, `value:10`.

    从设置的角度来看，这就是我们需要做的。该示例的其余部分通过 ActionScript 控制。

##### 添加动作脚本

1.  选择`Actions`图层的第一帧，并在动作面板中添加以下代码:

```
var palletteArray = new Array();  

palleteArray = ["0x7FAAD4", "0xFFDF7F"];  

function setTheme() {  

  _global.style.setStyle("themeColor", palleteArray[0]);  

  _global.style.setStyle("backgroundColor", palleteArray[1]);  

  _global.style.setStyle("fontSize", 11);  

  _global.style.setStyle("fontWeight", "bold");  

  _global.style.setStyle("color", palleteArray[0]);  

}  

setTheme();  

var stepsListener = new Object();  

stepsListener.change = function() {  

  **removeDeadClips();  

  createTextBoxes();**  

};  

stepperMain.addEventListener("change", stepsListener);  

**function createTextBoxes() {  

  if (stepperMain.value>stepperMain.minimum) {  

    var yPos = 30;  

    var yStart = stepperMain._y;  

    var yStep = 20;  

    var xStart = 150;  

    var textMC = _root.createEmptyMovieClip("textMC", 10);  

    for (var i=1; i<=stepperMain.value; i++) {  

      var field = textMC.createTextField("sampleText"+i, i, xStart, yStart+yPos, 200, 25);  

      field.multiline = true;  

      field.wordWrap = true;  

      field.border = false;  

      //Format Text  

      formatText(field, i);  

      var yPos += yStep;  

    }  

  }  

}  

createTextBoxes();  

function formatText(field, number) {  

  var textFormatter = new TextFormat();  

  textFormatter.color = palleteArray[0];  

  textFormatter.bullet = false;  

  textFormatter.underline = false;  

  textFormatter.font = "_sans";  

  textFormatter.size = 12;  

  textFormatter.bold = true;  

  field.text = "Dynamic Text Creation #: "+number;  

  field.setTextFormat(textFormatter);  

}  

function removeDeadClips() {  

  for (z in _root) {  

    if (typeof (_root[z]) == "movieclip") {  

      _root[z].removeMovieClip();  

    }  

  }  

}**
```

和以前一样，我们设置了变更事件处理程序。这一次，当 NumericStepper 组件的值改变时，它触发两个函数(`removeDeadClips()`和`createTextBoxes()`)。

在`setTheme()`之后，启动时要调用的第一个函数是`createTextBoxes()`，它取`stepperMain`实例的初始值，并创建一个空的`movie clip`。然后，它使用`stepperMain.value`属性作为限制值，遍历一个 for 循环。

当我们遍历 for 循环时，使用`formatText()`函数创建并格式化一系列文本字段。这将设置文本字段的一般属性，包括来自`palletteArray`数组的颜色，并添加一些简单的动态文本。

当`stepperMain` NumericStepper 实例值改变时，从事件处理程序调用`removeDeadClips()`函数。它移除了我们为保存文本字段而创建的空的`movie clip`。

*注意:虽然只有一个`Movie Clip`是在舞台上动态创建的，并且我们可以使用`removeMovieClip()`简单地删除它，但是在这个例子中我选择使用这个函数。这种方法提供了额外的好处，如果我希望以后创建多个`Movie Clips`，我可以简单地修改函数来满足我的需要，并将其指向特定的级别或嵌套的`Movie Clip`实例。*

清理完阶段后，我们再次调用`createTextField()`函数，创建空的`Movie Clip`和动态文本字段。

9.  Save your Flash document and preview your work.

    就像从自行车上摔下来一样简单！此示例显示了使用组件作为触发动态对象创建的起点的基本操作。

##### 仅仅是开始…

虽然本教程中提供的示例展示了 NumericStepper 组件的几种不同用法，但可能性确实是无限的。使用您的想象力和您在这里开发的技能，您可以创建一些有趣的组件应用程序。

当您使用该组件创建一些有趣的应用程序时，不要羞于与 SitePoint 社区的其他人分享它们。我们期待看到你所做的一切！

## 分享这篇文章