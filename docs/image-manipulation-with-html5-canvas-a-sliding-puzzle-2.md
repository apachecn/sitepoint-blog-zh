# 用 HTML5 Canvas 操作图像:滑动拼图

> 原文：<https://www.sitepoint.com/image-manipulation-with-html5-canvas-a-sliding-puzzle-2/>

HTML5 包括许多功能，可以将多媒体原生集成到网页中。这些特性中包括 canvas 元素，它是一块空白的石板，可以用线条画、图像文件或动画来填充。

在本教程中，我将通过创建一个滑动拼图游戏来演示 HTML5 canvas 的图像处理功能。

要在网页中嵌入画布，请使用`<canvas>`标签。

```
  <canvas width="480px" height="480px"></canvas>
```

宽度和高度属性以像素为单位设置画布大小。如果未指定这些属性，则宽度默认为 300 像素，高度默认为 150 像素。

画布上的绘制是通过一个上下文完成的，这个上下文是通过 JavaScript 函数`getContext()`初始化的。W3C 规定的二维上下文被恰当地称为“2d”。因此，要初始化 ID 为“canvas”的画布的上下文，我们只需调用:

```
  document.getElementById("canvas").getContext("2d");
```

下一步是显示图像。JavaScript 对此只有一个函数，`drawImage()`，但是有三种方法可以调用这个函数。在最基本的形式中，这个函数有三个参数:图像对象和画布左上角的 x 和 y 偏移量。

```
  drawImage(image, x, y);
```

还可以添加两个参数 width 和 height 来调整图像的大小。

```
  drawImage(image, x, y, width, height);
```

最复杂的形式`drawImage()`需要 9 个参数。首先是图像对象。接下来的四个按顺序是源的 x、y、宽度和高度。其余四个按顺序是目标 x、y、宽度和高度。这个函数提取图像的一部分绘制在画布上，如果需要的话调整它的大小。这允许我们把图像当作一个精灵表。

```
  drawImage(image, sx, sy, sw, sh, dx, dy, dw, dh);
```

所有形式的`drawImage()`都有一些注意事项。如果图像为空，或者水平或垂直维度为零，或者源高度或宽度为零，`drawImage()`将抛出异常。如果图像不能被浏览器解码，或者在调用函数时没有完成加载，`drawImage()`将不会显示任何内容。

这就是 HTML5 canvas 图像处理的全部内容。现在让我们在实践中看看。

```
  <div id="slider">
    <form>
      <label>Easy</label>
      <input type="range" id="scale" value="4" min="3" max="5" step="1">
      <label>Hard</label>
    </form>
    <br>
  </div>
  <div id="main" class="main">
    <canvas id="puzzle" width="480px" height="480px"></canvas>
  </div>
```

这个 HTML 块包含另一个 HTML5 特性，range input，它允许用户使用滑块选择一个数字。稍后我们将看到范围输入是如何与谜题交互的。不过，预先警告一下:尽管大多数浏览器都支持范围输入，但在撰写本文时，两种更流行的浏览器——Internet Explorer 和 Firefox——仍然不支持。

现在，正如我上面提到的，要在画布上画画，我们需要一个上下文。

```
  var context = document.getElementById("puzzle").getContext("2d");
```

我们还需要一个图像。您可以使用下面引用的图像或适合(或可以调整大小以适合)画布的任何其他方形图像。

```
  var img = new Image();
  img.src = 'http://www.brucealderman.inimg/dimetrodon.jpg';
  img.addEventListener('load', drawTiles, false);
```

事件侦听器是为了保证在浏览器试图绘制图像之前完成图像的加载。如果图像还没有准备好，画布就不会显示图像。

我们将从拼图画布中获取棋盘大小，从范围输入中获取瓦片数。此滑块的范围从 3 到 5，数值表示行数和列数。

```
  var boardSize = document.getElementById('puzzle').width;
  var tileCount = document.getElementById('scale').value;
```

有了这两个数字，我们就可以计算出瓦片的大小。

```
  var tileSize = boardSize / tileCount;
```

现在我们可以创建电路板了。

```
  var boardParts = new Object;
  setBoard();
```

`setBoard()`函数是我们定义和初始化虚拟板的地方。表示棋盘的自然方式是二维数组。在 JavaScript 中，创建这样的数组并不是一个优雅的过程。我们首先声明一个平面数组，然后将数组的每个元素声明为一个数组。然后可以像访问多维数组一样访问这些元素。

对于滑动拼图游戏，每个元素将是一个具有 x 和 y 坐标的对象，这些坐标定义了它在拼图网格中的位置。因此，每个物体都有两组坐标。第一个是它在数组中的位置。这代表它在棋盘上的位置，所以我称之为棋盘方格。每个棋盘方格都有一个带有 x 和 y 属性的对象，代表它在拼图图像中的位置。我将这个位置称为拼图块。当棋盘方格的坐标与其拼图块的坐标相匹配时，拼图块就处于解决拼图的正确位置。

出于本教程的目的，我们将初始化每个拼图块，使其与拼图中正确的位置相对。例如，右上角的方块将位于左下角的棋盘方格中。

```
  function setBoard() {
    boardParts = new Array(tileCount);
    for (var i = 0; i < tileCount; ++i) {
      boardParts[i] = new Array(tileCount);
      for (var j = 0; j < tileCount; ++j) {
        boardParts[i][j] = new Object;
        boardParts[i][j].x = (tileCount - 1) - i;
        boardParts[i][j].y = (tileCount - 1) - j;
      }
    }
    emptyLoc.x = boardParts[tileCount - 1][tileCount - 1].x;
    emptyLoc.y = boardParts[tileCount - 1][tileCount - 1].y;
    solved = false;
  }
```

`setBoard()`中的最后三个语句引入了我们还没有定义的变量。

我们需要跟踪空磁贴的位置，并记录用户点击的位置。

```
  var clickLoc = new Object;
  clickLoc.x = 0;
  clickLoc.y = 0;

  var emptyLoc = new Object;
  emptyLoc.x = 0;
  emptyLoc.y = 0;
```

最后一个变量是一个布尔值，表示谜题是否已经解决。

```
  var solved = false;
```

一旦所有的拼图都与各自的棋盘方格匹配，我们就将此设置为真。

现在我们只需要与解谜相关的函数。

首先，我们将设置由用户输入事件触发的函数。如果输入的范围改变了，我们需要在重绘棋盘之前重新计算方块的数量和大小。

```
  document.getElementById('scale').onchange = function() {
    tileCount = this.value;
    tileSize = boardSize / tileCount;
    setBoard();
    drawTiles();
  };
```

我们需要跟踪鼠标的移动来知道用户点击了哪些瓷砖。

```
  document.getElementById('puzzle').onmousemove = function(e) {
    clickLoc.x = Math.floor((e.pageX - this.offsetLeft) / tileSize);
    clickLoc.y = Math.floor((e.pageY - this.offsetTop) / tileSize);
  };

  document.getElementById('puzzle').onclick = function() {
    if (distance(clickLoc.x, clickLoc.y, emptyLoc.x, emptyLoc.y) == 1) {
      slideTile(emptyLoc, clickLoc);
      drawTiles();
    }
    if (solved) {
      alert("You solved it!");
    }
  };
```

在某些浏览器中，已解决的警告可能会在纸板完成重绘之前触发。为了防止这种情况，给警报一个短暂的延迟。

```
  if (solved) {
    setTimeout(function() {alert("You solved it!");}, 500);
  }
```

当一个方块被点击时，我们需要知道它是否在空白方块的旁边。当且仅当从被点击的区块到空白方块的总距离是 1 时，换句话说，如果被点击的区块和空白区块的 x 坐标之差加上被点击的区块和空白区块的 y 坐标之差是 1，则这是正确的。实现比描述容易。

```
  function distance(x1, y1, x2, y2) {
    return Math.abs(x1 - x2) + Math.abs(y1 - y2);
  }
```

`distance()`函数通过获取 x 坐标和 y 坐标之间差值的绝对值，并将它们相加来计算该距离。如果该值为 1，则可以将单击的图块移动到空白方块中。如果该值不是 1，则不应移动该图块。

要移动方块，我们只需将该棋盘方块的方块坐标复制到空白方块中。然后将移除的图块坐标复制到单击的图块中。

```
  function slideTile(toLoc, fromLoc) {
    if (!solved) {
      boardParts[toLoc.x][toLoc.y].x = boardParts[fromLoc.x][fromLoc.y].x;
      boardParts[toLoc.x][toLoc.y].y = boardParts[fromLoc.x][fromLoc.y].y;
      boardParts[fromLoc.x][fromLoc.y].x = tileCount - 1;
      boardParts[fromLoc.x][fromLoc.y].y = tileCount - 1;
      toLoc.x = fromLoc.x;
      toLoc.y = fromLoc.y;
      checkSolved();
    }
  }
```

一旦瓷砖被移动，我们需要检查是否解谜。我们将扫描瓷砖，看看它们是否都在正确的棋盘方格中。

```
  function checkSolved() {
    var flag = true;
    for (var i = 0; i < tileCount; ++i) {
      for (var j = 0; j < tileCount; ++j) {
        if (boardParts[i][j].x != i || boardParts[i][j].y != j) {
          flag = false;
        }
      }
    }
    solved = flag;
  }
```

如果有任何瓷砖错位，该函数将返回 false。否则默认为真。

最后，在新的位置重新绘制带有被点击的牌的棋盘。

```
  function drawTiles() {
    context.clearRect ( 0 , 0 , boardSize , boardSize );
    for (var i = 0; i < tileCount; ++i) {
      for (var j = 0; j < tileCount; ++j) {
        var x = boardParts[i][j].x;
        var y = boardParts[i][j].y;
        if(i != emptyLoc.x || j != emptyLoc.y || solved == true) {
          context.drawImage(img, x * tileSize, y * tileSize, tileSize, tileSize,
              i * tileSize, j * tileSize, tileSize, tileSize);
        }
      }
    }
  }
```

在绘制拼图时，该函数会阻止填充与 emptyLoc 坐标匹配的棋盘方块，直到设置了已解决标志。顺便提一下，因为每当移动范围滑块时，棋盘都会重新初始化，所以用户可以在解决难题后尝试另一个难度级别，而无需刷新页面。

这就是全部了！canvas 元素，加上一点 JavaScript 和数学知识，为 HTML5 带来了强大的原生图像操作。

你可以在 http://html5.brucealderman.info/sliding.html 找到滑动拼图的现场演示。

## 分享这篇文章