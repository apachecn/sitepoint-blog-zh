# 向工具箱添加 Web 控制台，第 2 部分

> 原文：<https://www.sitepoint.com/add-a-web-console-to-your-toolbox-part-2/>

一个*控制台*是一个软件工件，用于从键盘读取面向行的文本输入，并将面向行的文本输出写到屏幕上。这个由两部分组成的系列的第 1 部分向您介绍了用于在 web 页面中嵌入控制台的控制台库，并通过浏览器外壳演示了这种控制台的有用性。第 2 部分向您展示了控制台库是如何实现的。

## 了解控制台的工作原理

控制台库相当完整，但是您可能希望用新的功能来扩展它(例如，在输入密码时回显星号)。或者，您可能希望提高性能或实现错误检查。不管你的目的是什么，你都需要了解这个库是如何工作的。获得这些知识的第一步是掌握库的整体架构。清单 1 给出了一个概述。

```
var Console = 
{
   init: function(canvasName, numCols, numRows)
         {
         },

   clear: function()
          {
          },

   getLine: function(callback)
            {
            },

   echo: function(msg)
         {
         },

   render: function()
           {
           },

   writeChar: function(ch)
              {
              },

   scroll: function()
           {
           }
}
```

**清单 1:** 控制台库的框架结构

清单 1 展示了一个名为`Console`的全局对象，由七个函数属性组成。前四个属性构成了公共 API，而最后三个属性应该被认为是私有的，不能被访问。这个属性列表并不完整，因为`init(canvasName, numCols, numRows)`函数引入了额外的属性。

| 注 |
| 我可以通过在`init(canvasName, numCols, numRows)`中引入像`Console.writeChar = function(ch) { /* code here */ }`这样的表达式来“隐藏”最后三个属性。我选择不这样做是为了不让`init(canvasName, numCols, numRows)`继续下去。 |

### 发现`init(canvasName, numCols, numRows)`

清单 2 展示了`init(canvasName, numCols, numRows)`。

```
init: function(canvasName, numCols, numRows)
      {
         var canvas = document.getElementById(canvasName); Console.numCols = numCols; Console.numRows = numRows; Console.ctx = canvas.getContext("2d"); Console.ctx.font = "20px/20px monospace"; Console.ctx.textBaseline = "top"; Console.charWidth = Console.ctx.measureText("m").width; Console.charHeight = 20; canvas.width = Console.charWidth*numCols+10; canvas.height = Console.charHeight*numRows+10; Console.buffer = document.createElement("canvas"); Console.buffer.width = canvas.width; Console.buffer.height = canvas.height; Console.bufferCtx = Console.buffer.getContext("2d"); Console.bufferCtx.font = "20px/20px monospace"; Console.bufferCtx.textBaseline = "top"; Console.screen = new Array(numRows); for (var row = 0; row < numRows; row++) Console.screen[row] = new Array(numCols); Console.keyQueue = new Array(); function keyDown(event) { // This function is called by all browsers for backspace. if (event.keyCode == 8) // backspace? { Console.keyQueue.push("b"); // The following code is needed by Chrome to prevent backspace // from moving back in page history. event.preventDefault(); } } canvas.addEventListener("keydown", keyDown, true); function keyPress(event) { if (event.keyCode == 8) { // The following code is needed by Opera to prevent backspace // from moving back in page history. event.preventDefault(); return; } if (event.keyCode == 13) // return? { Console.keyQueue.push("n"); return; } var ch = (event.keyCode == 0) ? event.charCode : event.keyCode; if (ch >= 32 && ch < 127) Console.keyQueue.push(String.fromCharCode(ch)); } canvas.addEventListener("keypress", keyPress, true); canvas.tabIndex = 0; // Place canvas in tab order. canvas.focus(); // Give keyboard focus to canvas. Doesn't work on // Internet Explorer. Console.cursorOn = true; Console.cursorCounter = 0; Console.cursorCounterMax = 5; Console.line = ""; Console.clear(); }
```

**清单 2:** 初始化控制台库

清单 2 首先获得对命名的`<canvas>`元素的引用，并将列数和行数保存在`Console`属性中，供其他函数使用。然后，它获得在画布上绘图的上下文，并将该上下文初始化为 20 像素大小的等宽字体。文本基线被设置在字体的顶部，这样字符的坐标相对于它的左上角。

既然已经指定了字体，就要计算它的字符宽度和高度，以便字符可以在画布上正确定位。这个信息然后被用来计算画布的宽度和高度。宽度和高度增加了额外的 10 个像素，因此画布周围有一个 5 个像素的边框(防止在 Internet Explorer 中查看时光标在底行不可见)。

接下来的几行创建并初始化一个缓冲区，以支持双缓冲。目标是通过绘制到缓冲区，然后将缓冲区内容复制到画布来避免闪烁。各种博客帖子(例如，[http://stackoverflow.com/<wbr>questions/2795269/does-html 5-<wbr>canvas-support-double-<wbr>buffering](http://stackoverflow.com/questions/2795269/does-html5-canvas-support-double-buffering))建议当前的浏览器自动支持双缓冲，而较老的浏览器通常不支持。

现在创建了一个二维`screen`数组，用于存储回显到控制台的字符。JavaScript 将二维数组实现为一维列数组的一维行数组。`Array`对象用于创建行数组，然后为每个行元素创建列数组，列数组的引用被分配给行数组元素。

尽管该表中的每一行都可能存储不同数量的列(这被称为*不规则数组*，但我选择将列数固定为传递给`Array`的构造函数的值。通过语法`Console.screen[*row*][*col*]`访问`Screen`数组中的元素——行和列索引是从零开始的。

接下来，`Array`对象用于创建一个`keyQueue`数组来存储字符和特殊的击键(例如，换行)。这个数组就像一个队列，在队列的一端添加击键，在另一端移除击键。将击键添加到该队列的代码包含在一对事件处理函数中，这些函数向画布注册以响应按键和按键事件。

key-down 事件处理程序只响应退格键。我更喜欢通过按键来处理这个键，但是当在 Internet Explorer、Chrome 或 Safari 上下文中按下 backspace 时，不会调用这个事件处理程序。将`b`添加到队列后，key-down 执行`event.preventDefault()`来防止当前页面被 Chrome 页面历史中的前一个页面替换。

按键事件处理程序也响应 Firefox 和 Opera 的退格键。它在这些浏览器下忽略这个键(在队列中添加第二个`b`代码不是个好主意)，但是执行`event.preventDefault()`来防止当前页面被 Opera 页面历史中的前一个页面替换。

按键事件处理程序还通过向队列添加一个换行符来响应 Enter/Return 键，并通过对代码调用`String`的`fromCharCode()`函数并将等效字符添加到队列来响应代码范围从 32 到 126 的键。在 Firefox 上，`keyCode`包含一个字符键的 0(如 A)，必须从`charCode`中获得合适的代码。

| 注 |
| Opera 不支持`charCode`，但是`keyCode`在按键上下文中区分大小写字符。 |

HTML 提供了一个`tabindex`属性和一个`tabIndex` DOM 属性，用于按 tab 键顺序放置元素。为此，零被分配给画布的`tabIndex`属性。接下来，canvas 的`focus()`函数被调用，为这个元素提供键盘焦点。尽管 Firefox 提供了焦点，但 Internet Explorer 却没有——您必须按一次 Tab 键或在画布上单击鼠标。

画布通过`cursorOn`、`cursorCounter`和`cursorCounterMax`属性管理光标。当`true`被分配给`cursorOn`(默认值)时，光标可见，并且光标保持可见，直到`cursorCounter`到达`cursorCounterMax`，此时光标被重置为 0。然后它变得不可见，并保持同样的持续时间。

还有两个最后的任务需要`init(canvasName, numCols, numRows)`去完成。首先，它将空字符串分配给`line`属性，这是一个缓冲区，用于存储字符，直到按下 Enter/Return 键。其次，它调用`clear()`函数来清空控制台，并将光标的位置重置到左上角的字符位置。

### 发现`clear()`

清单 3 展示了`clear()`。

```
clear: function()
       {
          for (var row = 0; row < Console.numRows; row++)
             for (var col = 0; col < Console.numCols; col++)
                Console.screen[row][col] = " ";
          Console.row = 0;
          Console.col = 0;
          Console.render();
       }
```

**清单 3:** 清除控制台

清单 3 通过给每个`screen`数组元素分配一个空格来清空控制台。(虽然不是很有表现力，但我强调的是清晰。我或许可以通过利用`Array`的`splice()`功能来加速代码。)然后它将当前光标位置(由`Console`的`col`和`row`属性指示)重置为左上角的字符位置，并将`screen`的内容呈现到画布上。(我稍后讨论`render()`。)

### 发现`getLine(callback)`

清单 4 展示了`getLine(callback)`。

```
getLine: function(callback)
         {
            Console.render(); // update cursor

            if (Console.keyQueue.length == 0)
            {
               if (Console.line.length == 0)
               {
                  if (callback != undefined)
                     callback();
               }
               return null;
            }
            var ch = Console.keyQueue.shift();
            if (ch == "b") // handle backspace
            {
               if (Console.line.length != 0)
               {
                  Console.line = Console.line.substr(0, 
                                                     Console.line.length-1);
                  Console.echo(ch);
               }
               return null;
            }
            Console.echo(ch);
            if (ch == "n") // handle newline
            {
               var temp = Console.line;
               Console.line = "";
               return temp;
            }
            else
               Console.line += ch;
            return null;
         }
```

**清单 4:** 获取一行输入

清单 4 描述了一个轮询函数，它不断地检查输入，并一次处理一个字符。第一个任务是显示或隐藏光标，这个任务是通过调用`Console.render()`来完成的。因为控制台演示和浏览器外壳应用程序会不断调用`getLine()`，所以会保持光标闪烁的假象。

| 注 |
| 光标的闪烁速率取决于传递给`setInterval()`的延迟值。延迟值越大，光标闪烁越慢。 |

下一个任务是确定队列中是否存在任何字符。如果队列为空，`getLine()`可以返回。但是，它首先需要调用任何作为参数传递的回调函数，但只能在`line`缓冲区为空时调用该函数(一行输入没有进行中)，以防止像讨论浏览器外壳时演示的那样搞乱输入行。

此时，队列包含一个随后被删除的字符。如果这个字符是退格符，并且如果`line`缓冲区不为空，则从缓冲区中移除最右边的字符，退格符被回显到控制台以保持`screen`数组同步，并且因为还没有完整的输入行，所以返回`null`。

将字符回显到控制台后，`getLine()`检查当前字符是否是换行符。如果是这样，`line`缓冲区在预期下一行输入时被重置为空字符串，并返回其先前的内容。否则，当前字符被追加到这个缓冲区，并且因为还没有完整的输入行，所以返回`null`。

### 发现`echo(msg)`

清单 5 展示了`echo(msg)`。

```
echo: function(msg)
      {
         for (var i = 0; i < msg.length; i++)
            Console.writeChar(msg.charAt(i)); Console.render(); }
```

**清单 5:** 向控制台回显一个字符串

清单 5 将一个字符串一次一个字符地回显到控制台，在这个过程中更新当前的光标位置。代码为此使用了`writeChar(ch)`，我将很快解释这个函数。写出字符串后，调用`Console.render()`用`screen`数组的内容更新画布。

### 发现`render()`

清单 6 展示了`render()`。

```
render: function()
        {
           Console.bufferCtx.fillStyle = "#000"; // black
           Console.bufferCtx.fillRect(0, 0, Console.ctx.canvas.width, 
                                      Console.ctx.canvas.height);
           Console.bufferCtx.fillStyle = "#0f0"; // green
           var y = 0;
           for (var row = 0; row < Console.numRows; row++)
           {
              var x = 0;
              for (var col = 0; col < Console.numCols; col++)
              {
                  var s = Console.screen[row][col]+"";
                  Console.bufferCtx.fillText(s, x+5, y+5);
                  x += Console.charWidth;
              }
              y += Console.charHeight;
           }
           if (Console.cursorOn)
              Console.bufferCtx.fillStyle = "#0f0"; // green
           else
              Console.bufferCtx.fillStyle = "#000"; // black
           Console.bufferCtx.fillText("_", Console.col*Console.charWidth+5, Console.row*Console.charHeight+5); if (++Console.cursorCounter == Console.cursorCounterMax) { Console.cursorCounter = 0; Console.cursorOn = !Console.cursorOn; } Console.ctx.drawImage(Console.buffer, 0, 0); }
```

**清单 6:** 将`screen`数组渲染到缓冲区(等等)

清单 6 负责将`screen`数组呈现到缓冲区并更新光标。它呈现到后台缓冲区，并最终将该缓冲区复制到画布，以避免可能出现闪烁的浏览器出现闪烁。背景缓冲区首先被清除为黑色，以移除来自先前渲染的潜在垃圾。

将绘制颜色设置为绿色后，`render()`遍历`screen`数组，调用 Canvas API 的`fillText()`函数来绘制每个字符。五个像素的偏移量被添加到角色的左上角，以支持在控制台周围绘制的空边框。字符在水平方向由`charWidth`像素分隔，在垂直方向由`charHeight`像素分隔。

下一段代码负责呈现光标。根据`cursorOn`的值选择合适的填充样式:真时为绿色，假时为黑色。然后在当前光标位置显示绿色或黑色下划线。黑色下划线会完全擦除之前显示的内容。

最后，建立光标的开/关间隔:光标在对`render()`的五次调用中可见，在对此函数的五次调用中不可见。因为`render()`被`getLine()`调用，并且`getLine()`在`setInterval()`的控制下被应用程序反复调用，所以观察到一个闪烁的光标。

### 发现`writeChar(ch)`

清单 7 展示了`writeChar(ch)`。

```
writeChar: function(ch)
           {
              if (ch == "b")
              {
                 if (Console.col == 0 && Console.row == 0)
                    return; // cannot backspace past the upper-left corner
                 Console.col--;
                 if (Console.col < 0)
                 {
                    Console.col = Console.numCols-1; 
                    Console.row--;
                 }
                 Console.screen[Console.row][Console.col] = " "; return; } if (ch == "n") { Console.col = 0; if (++Console.row >= Console.numRows) Console.scroll(); return; } Console.screen[Console.row][Console.col] = ch; if (++Console.col >= Console.numCols) { Console.col = 0; if (++Console.row >= Console.numRows) Console.scroll(); } }
```

**清单 7:** 向`screen`数组中写入一个字符

清单 7 将其单字符参数写入到`screen`数组，并更新当前光标位置。如果字符是退格字符，则删除`screen`数组中的适当元素。否则，如果字符是换行符，则当前行前进，控制台在必要时垂直向上滚动。否则，字符将存储在当前位置，向前移动并可能向上滚动。

### 发现`scroll`

清单 8 展示了`scroll()`。

```
scroll: function()
        {
           Console.row = Console.numRows-1;
           for (var row = 0; row < Console.numRows-1; row++)
               for (var col = 0; col < Console.numCols; col++)
                  Console.screen[row][col] = Console.screen[row+1][col];
           for (var col = 0; col < Console.numCols; col++)
              Console.screen[Console.numRows-1][col] = " "; }
```

清单 8: 将控制台向上滚动一行

清单 8 执行了一个简单的滚动操作，将`screen`数组的内容向上移动一行。第一行的内容被第二行的内容替换，最后一行被设置为空格。为了清楚起见，我编写了这个函数，但是从性能的角度来看，它还可以改进。

## 结论

控制台的实现还有很大的改进空间。您可以添加缺失的功能(例如，输入密码时回显星号)，提高面向`screen`的循环性能(可能通过`Array`的`splice()`函数)，并通过参数验证(例如，比较通过`undefined`传递的内容)和异常抛出使库更加健壮。玩得开心。

| 注 |
| 与本文相关的所有文件都位于 [code.zip](https://www.sitepoint.com/wp-content/uploads/2012/05/code.zip) 中。 |

## 分享这篇文章