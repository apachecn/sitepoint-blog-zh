# 用 JavaScript 构建 3D 引擎

> 原文：<https://www.sitepoint.com/building-3d-engine-javascript/>

*这篇文章由[蒂姆·塞韦里恩](https://twitter.com/TimSeverien)和[西蒙·科灵顿](https://www.sitepoint.com/author/scodrington/)进行了同行评审。感谢 SitePoint 的所有同行评审员使 SitePoint 的内容尽可能做到最好！*

在网页中显示图像和其他平面形状非常容易。然而，当涉及到显示三维形状时，事情变得不那么容易，因为三维几何比 2D 几何更复杂。要做到这一点，你可以使用专门的技术和库，例如 [WebGL](https://en.wikipedia.org/wiki/WebGL) 和 [Three.js](http://threejs.org/) 。

然而，如果您只想显示一些基本的形状，如立方体，这些技术并不是必需的。此外，它们不会帮助你理解它们是如何工作的，以及我们如何在平面屏幕上显示 3D 形状。

本教程的目的是解释我们如何在没有 WebGL 的情况下为 web 构建一个简单的 3D 引擎。我们将首先看看如何存储 3D 形状。然后，我们将看到如何在两个不同的视图中显示这些形状。

## 存储和转换 3D 形状

### 所有的形状都是多面体

虚拟世界在一个主要方面不同于现实世界:没有什么是连续的，一切都是离散的。例如，你不能在屏幕上显示一个正圆。你可以通过画一个有很多边的正多边形来接近它:你的边越多，你的圆就越“完美”。

在 3D 中，这是同样的事情，每个形状都必须用多边形的 3D 等价物来处理:一个[多面体](https://en.wikipedia.org/wiki/Polyhedron)(一个 3D 形状，其中我们只能找到平面，而不能像在球体中那样找到弯曲的边)。当我们谈论一个已经是多面体的形状时，比如立方体，这并不奇怪，但是当我们想要显示其他形状时，比如球体，这是要记住的。

![Sphere](img/108823328a9a920c5d10da74a5fe5667.png)

### 存储多面体

为了猜测如何存储多面体，我们必须记住在数学中如何识别这样的东西。你肯定已经在学校学过一些基本的几何知识了。例如，为了识别一个正方形，你称它为`ABCD`，用`A`、`B`、`C`和`D`指代组成正方形每个角的顶点。

对于我们的 3D 引擎来说，也是一样的。我们将从存储我们形状的每个顶点开始。然后，这个形状将列出它的面，每个面将列出它的顶点。

为了表示一个顶点，我们需要正确的结构。这里我们创建一个类来存储顶点的坐标。

```
var Vertex = function(x, y, z) {
    this.x = parseFloat(x);
    this.y = parseFloat(y);
    this.z = parseFloat(z);
}; 
```

现在，可以像创建任何其他对象一样创建顶点:

```
var A = new Vertex(10, 20, 0.5); 
```

接下来，我们创建一个代表多面体的类。我们以一个立方体为例。这个类的定义在下面，解释在后面。

```
var Cube = function(center, size) {
    // Generate the vertices
    var d = size / 2;

    this.vertices = [
        new Vertex(center.x - d, center.y - d, center.z + d),
        new Vertex(center.x - d, center.y - d, center.z - d),
        new Vertex(center.x + d, center.y - d, center.z - d),
        new Vertex(center.x + d, center.y - d, center.z + d),
        new Vertex(center.x + d, center.y + d, center.z + d),
        new Vertex(center.x + d, center.y + d, center.z - d),
        new Vertex(center.x - d, center.y + d, center.z - d),
        new Vertex(center.x - d, center.y + d, center.z + d)
    ];

    // Generate the faces
    this.faces = [
        [this.vertices[0], this.vertices[1], this.vertices[2], this.vertices[3]],
        [this.vertices[3], this.vertices[2], this.vertices[5], this.vertices[4]],
        [this.vertices[4], this.vertices[5], this.vertices[6], this.vertices[7]],
        [this.vertices[7], this.vertices[6], this.vertices[1], this.vertices[0]],
        [this.vertices[7], this.vertices[0], this.vertices[3], this.vertices[4]],
        [this.vertices[1], this.vertices[6], this.vertices[5], this.vertices[2]]
    ];
}; 
```

使用这个类，我们可以通过指出它的中心和边的长度来创建一个虚拟立方体。

```
var cube = new Cube(new Vertex(0, 0, 0), 200); 
```

`Cube`类的构造函数从生成立方体的顶点开始，从指定的中心位置开始计算。一个模式将会更清楚，所以看下面我们生成的八个顶点的位置:

![Cube](img/47140a11ba68173fbba2e88129340c09.png)

然后，我们列出这些面孔。每个面都是正方形，所以我们需要为每个面指定四个顶点。这里我选择用一个数组来表示一张脸，但是如果你需要，你可以为它创建一个专用的类。

当我们创建一个面时，我们使用四个顶点。我们不需要再次指出它们的位置，因为它们存储在`this.vertices[i]`对象中。这很实际，但我们这样做还有另一个原因。

默认情况下，JavaScript 试图使用尽可能少的内存。为此，它不复制作为函数参数传递的对象，甚至不复制存储在数组中的对象。对我们来说，这是完美的行为。

事实上，每个顶点包含三个数字(它们的坐标)，如果我们需要添加它们，还会加上几个方法。如果，对于每一个面，我们存储一个顶点的副本，我们会使用大量的内存，这是没有用的。这里，我们所有的都是引用:坐标(和其他方法)被存储一次，而且只有一次。由于每个顶点由三个不同的面使用，通过存储引用而不是副本，我们将所需的内存除以三(或多或少)！

### 我们需要三角形吗？

如果你已经玩过 3D(比如用 Blender 之类的软件，或者用 WebGL 之类的库)，也许你听说过我们应该用三角形。在这里，我选择不使用三角形。

这一选择背后的原因是，本文是对该主题的介绍，我们将展示基本形状，如立方体。在我们的例子中，使用三角形来显示正方形比其他任何东西都要复杂。

然而，如果你计划构建一个更完整的渲染器，那么你需要知道，一般来说，三角形是首选的。这有两个主要原因:

1.  纹理:为了在人脸上显示图像，我们需要三角形，出于一些数学原因；
2.  怪异的面孔:三个顶点总是在同一平面。但是，您可以添加不在同一平面上的第四个顶点，并且可以创建连接这四个顶点的面。在这种情况下，要画它，我们没有选择:我们必须把它分成两个三角形(就用一张纸试试吧！).通过使用三角形，你可以控制并选择在哪里进行分割(感谢 [Tim](https://www.sitepoint.com/author/tseverien/) 的提醒！).

### 作用于多面体

存储引用而不是副本还有一个好处。当我们想要修改一个多面体时，使用这样的系统也会将所需的操作次数除以三。

为了理解为什么，让我们再一次回忆一下我们的数学课。当你想翻译一个正方形的时候，你并没有真的翻译它。事实上，你平移四个顶点，然后加入平移。

在这里，我们会做同样的事情:我们不会碰脸。我们在每个顶点上应用想要的操作，我们就完成了。当面使用参考时，面的坐标会自动更新。例如，看看我们如何翻译先前创建的立方体:

```
for (var i = 0; i < 8; ++i) {
    cube.vertices[i].x += 50;
    cube.vertices[i].y += 20;
    cube.vertices[i].z += 15;
} 
```

## 渲染图像

我们知道如何存储 3D 对象，以及如何对它们进行操作。现在是时候看看如何看待它们了！但是，首先我们需要一个理论的小背景，以便理解我们要做什么。

### 推断

目前，我们存储三维坐标。然而，一个屏幕只能显示 2D 坐标，所以我们需要一种方法将我们的三维坐标转换成 2D 坐标:这就是我们在数学中所说的投影。三维到 2D 投影是一种抽象的操作，由一个叫做虚拟相机的新对象完成。这个相机获取一个 3D 对象，并将其坐标转换为 2D 坐标，发送到渲染器，渲染器将在屏幕上显示这些坐标。这里我们假设我们的相机被放置在我们的 3D 空间的原点(所以它的坐标是`(0,0,0)`)。

从本文开始，我们已经讨论了坐标，用三个数字表示:`x`、`y`和`z`。但是定义坐标，我们需要一个依据:`z`是纵坐标吗？它是向上还是向下？没有普遍的答案，也没有惯例，因为事实是你可以选择任何你想要的。你唯一需要记住的是，当你作用于 3D 对象时，你必须保持一致，因为公式会根据它而改变。在这篇文章中，我选择了你可以在上面的立方体的模式中看到的基础:`x`从左到右，`y`从后到前，`z`从下到上。

现在，我们知道该怎么做了:我们有了`(x,y,z)`坐标系中的坐标，为了显示它们，我们需要将它们转换成`(x,z)`坐标系中的坐标:因为它是一个平面，我们将能够显示它们。

不只有一个投影。更糟糕的是，存在无限多种不同的投影！在这篇文章中，我们将看到两种不同类型的投影，这是实践中最常用的一种。

### 如何渲染我们的场景

在投影我们的对象之前，让我们编写显示它们的函数。该函数接受一个[数组作为参数，该数组列出了要呈现的对象](https://singlethread.io/post/find-the-intersection-of-two-arrays/)、必须用来显示对象的画布的上下文以及在正确位置绘制对象所需的其他细节。

该数组可以包含几个要呈现的对象。这些对象必须考虑一件事:拥有一个名为`faces`的公共属性，这是一个列出对象所有面的数组(就像我们之前创建的立方体)。这些面可以是任何形状(正方形、三角形，如果你愿意，甚至可以是十二边形):它们只需要是列出它们顶点的数组。

让我们看看函数的代码，后面是解释:

```
function render(objects, ctx, dx, dy) {
    // For each object
    for (var i = 0, n_obj = objects.length; i < n_obj; ++i) {
        // For each face
        for (var j = 0, n_faces = objects[i].faces.length; j < n_faces; ++j) {
            // Current face
            var face = objects[i].faces[j];

            // Draw the first vertex
            var P = project(face[0]);
            ctx.beginPath();
            ctx.moveTo(P.x + dx, -P.y + dy);

            // Draw the other vertices
            for (var k = 1, n_vertices = face.length; k < n_vertices; ++k) {
                P = project(face[k]);
                ctx.lineTo(P.x + dx, -P.y + dy);
            }

            // Close the path and draw the face
            ctx.closePath();
            ctx.stroke();
            ctx.fill();
        }
    }
} 
```

这个函数值得解释一下。更准确地说，我们需要解释这个`project()`函数是什么，这些`dx`和`dy`自变量是什么。剩下的基本上就是列出物体，然后画出每一张脸。

顾名思义，`project()`函数是用来将 3D 坐标转换成 2D 坐标的。它接受 3D 空间中的一个顶点，并返回 2D 平面中的一个顶点，我们可以定义如下。

```
var Vertex2D = function(x, y) {
    this.x = parseFloat(x);
    this.y = parseFloat(y);
}; 
```

我没有将坐标命名为`x`和`z`,而是在这里将`z`坐标重命名为`y`,以保持我们在 2D 几何中常见的经典惯例，但如果您愿意，也可以保留`z`。

`project()`的确切内容是我们将在下一节看到的:它取决于您选择的投影类型。但是不管这个类型是什么，`render()`函数都可以保持现在的样子。

一旦我们有了平面上的坐标，我们就可以在画布上显示它们，这就是我们所做的…用一个小技巧:我们并不真正画出`project()`函数返回的实际坐标。

事实上，`project()`函数返回虚拟 2D 平面上的坐标，但是与我们为 3D 空间定义的坐标具有相同的原点。然而，我们希望原点在画布的中心，这就是我们转换坐标的原因:顶点`(0,0)`不在画布的中心，但是`(0 + dx,0 + dy)`在，如果我们明智地选择`dx`和`dy`。因为我们希望`(dx,dy)`在画布的中心，我们没有真正的选择，我们定义了`dx = canvas.width / 2`和`dy = canvas.height / 2`。

最后，最后一个细节:为什么我们直接使用`-y`而不是`y`？答案就在我们选择的基础上:T2 轴指向顶部。然后，在我们的场景中，具有正 z 坐标的顶点将向上移动。然而，在画布上，`y`轴指向底部:具有正 y 坐标的顶点将向下移动。这就是为什么我们需要将画布上的 y 坐标定义为场景的 z 坐标的倒数。

既然`render()`函数已经清楚了，是时候看看`project()`了。

### 正视图

让我们从正投影开始。因为这是最简单的，所以理解我们要做什么是最完美的。

我们有三个坐标，我们只想要两个。在这种情况下，最简单的办法是什么？移除其中一个坐标。这就是我们在正交视图中所做的。我们将删除代表深度的坐标:`y`坐标。

```
function project(M) {
    return new Vertex2D(M.x, M.z);
} 
```

您现在可以测试我们从本文开始以来编写的所有代码:它工作了！恭喜你，你刚刚在平面屏幕上显示了一个 3D 物体！

这个函数在下面的例子中实现，你可以用鼠标旋转立方体来与它交互。

在 [CodePen](http://codepen.io) 上通过 SitePoint ( [@SitePoint](http://codepen.io/SitePoint) )看到笔 [3D 正交视图](http://codepen.io/SitePoint/pen/obapXL/)。