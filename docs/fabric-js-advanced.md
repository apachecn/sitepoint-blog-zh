# Fabric.js:高级

> 原文：<https://www.sitepoint.com/fabric-js-advanced/>

在这个系列的第一部分[和第二部分](https://www.sitepoint.com/introduction-to-fabric-js/)和第二部分[中，我们已经介绍了大部分布料的基本知识。在本文中，我将继续讨论更高级的特性:组、序列化(和反序列化)和类。](https://www.sitepoint.com/fabric-js-the-fun-stuff/)

## 组

我要讲的第一个话题是组，这是 Fabric 最强大的特性之一。群组就像它们听起来的那样，是将 Fabric 对象分组到单个实体中的一种简单方法，以便您可以将这些对象作为一个单元来处理。(参见**图 1** 。)

![A Selection Becomes a Group in Fabric](img/3897ef1e42664daf2d0ca4e9b062e281.png)
**图一。选择成为 Fabric 中的一个组**

请记住，画布上的任意数量的织物对象都可以用鼠标分组，以形成单个选择。一旦分组，对象可以作为一个整体移动甚至修改。您可以缩放、旋转群组，甚至更改其外观属性，如颜色、透明度、边框等。

每次您在画布上选择这样的对象时，Fabric 都会在幕后隐式创建一个组。考虑到这一点，只有以编程方式提供对组的访问才有意义，这就是`fabric.Group`的用武之地。

让我们从两个对象创建一个组，一个圆和文本:

```
       var text = new fabric.Text('hello world', {
	  fontSize: 30
	});
	var circle = new fabric.Circle({
	  radius: 100,
	  fill: '#eef',
	  scaleY: 0.5
	});
	var group = new fabric.Group([ text, circle ], {
	  left: 150,
	  top: 100,
	  angle: -10
	});
	canvas.add(group);
```

首先，我创建了一个“hello world”文本对象。然后，我创建了一个半径为 100 px 的圆，填充“#eef”颜色，垂直挤压(scaleY=0.5)。接下来我创建了一个`fabric.Group`实例，用这两个对象传递给它一个数组，并给它一个 150/100 的位置，角度为-10 度。最后，我使用`canvas.add()`将该组添加到画布中，就像我处理任何其他对象一样。

瞧啊。您会在画布上看到一个对象，如**图 2** 所示，一个带标签的椭圆，现在您可以将该对象作为一个实体来处理。要修改该对象，您只需更改组的属性，在这里为其提供自定义的左、上和角度值。

![A Group Created Programmatically](img/b05fbe914329f62875004a205f96ec6e.png)
**图二**。**以编程方式创建的群组**

现在我们的画布上有了一个组，让我们稍微改变一下:

```
       group.item(0).set({
	  text: 'trololo',
	  fill: 'white'
	});
	group.item(1).setFill('red');
```

这里，我们通过 item 方法访问组中的单个对象，并修改它们的属性。第一个对象是文本，第二个是被挤压的圆。**图 3** 显示了结果。

![ Squeezed Red Circle with New Text](img/3e3423b98da73875e9ec0b665f52bd83.png)
**图三。带有新文本的挤压红圈**

你现在可能已经注意到了一个重要的想法，那就是一个组中的所有对象都是相对于组的中心定位的。当我更改文本对象的 text 属性时，即使我更改了它的宽度，它仍保持居中。如果您不想要这种行为，您需要指定一个对象的左/上坐标，在这种情况下，它们将根据这些坐标进行分组。

以下是如何创建和组合三个圆圈，使它们一个接一个地水平放置，如图 4 所示。

```
       var circle1 = new fabric.Circle({
	  radius: 50,
	  fill: 'red',
	  left: 0
	});
	var circle2 = new fabric.Circle({
	  radius: 50,
	  fill: 'green',
	  left: 100
	});
	var circle3 = new fabric.Circle({
	  radius: 50,
	  fill: 'blue',
	  left: 200
	});
	var group = new fabric.Group([ circle1, circle2, circle3 ], {
	  left: 200,
	  top: 100
	});
	canvas.add(group);
```

![A Group with Three Circles Aligned Horizontally](img/61e3c2aad5681ccbaa46dd6586886073.png)
**图 4。三个圆水平排列的一组**

使用组时要记住的另一点是对象的状态。例如，当用图像组成一个组时，您需要确保这些图像是完全加载的。因为 Fabric 已经提供了帮助方法来确保图像被加载，所以这个操作变得相当容易，正如您在这段代码和图 5 中所看到的。

```
       fabric.Image.fromURL('/assets/pug.jpg', function(img) {
	  var img1 = img.scale(0.1).set({ left: 100, top: 100 });
	  fabric.Image.fromURL('/assets/pug.jpg', function(img) {
	    var img2 = img.scale(0.1).set({ left: 175, top: 175 });
	    fabric.Image.fromURL('/assets/pug.jpg', function(img) {
	      var img3 = img.scale(0.1).set({ left: 250, top: 250 });
	      canvas.add(new fabric.Group([ img1, img2, img3],
	        { left: 200, top: 200 }))
	    });
	  });
	});
```

![A Group with Three Images](img/54eaa85b0c953814af5068ff9f7065ca.png)
**图五。一组三张图片**

其他几种方法也可用于处理组:

*   getObjects 的工作方式与 fabric 完全一样。Canvas#getObjects()并返回一个组中所有对象的数组
*   大小表示一个组中对象的数量
*   包含允许您检查特定对象是否在组中
*   item(您之前看到的)允许您从组中检索特定的对象
*   forEachObject 也反映了织物。Canvas#forEachObject，但与组对象相关
*   添加和删除分别从组中添加和删除对象

无论是否更新组尺寸和位置，都可以添加或删除对象。以下是几个例子:

要在组的中心添加一个矩形(left=0，top=0)，请使用以下代码:

```
       group.add(new fabric.Rect({
	  ...
	}));
```

要从组的中心添加一个 100 像素的矩形，请执行以下操作:

```
       group.add(new fabric.Rect({
	  ...
	  left: 100,
	  top: 100
	}));
```

要在组的中心添加一个矩形并更新组的尺寸，请使用以下代码:

```
       group.addWithUpdate(new fabric.Rect({
	  ...
	  left: group.getLeft(),
	  top: group.getTop()
	}));
```

要在距离组中心 100 像素处添加一个矩形并更新组的尺寸，请执行以下操作:

```
       group.addWithUpdate(new fabric.Rect({
	  ...
	  left: group.getLeft() + 100,
	  top: group.getTop() + 100
	}));
```

最后，如果您想要创建一个包含画布上已经存在的对象的群组，您需要首先克隆它们:

```
       // create a group with copies of existing (2) objects
	var group = new fabric.Group([
	  canvas.item(0).clone(),
	  canvas.item(1).clone()
	]);
	// remove all objects and re-render
	canvas.clear().renderAll();
	// add group onto canvas
	canvas.add(group);
```

## 序列化

一旦您开始构建某种有状态的应用程序——可能是允许用户将画布内容的结果保存在服务器上或将内容流式传输到不同的客户端——您就需要画布序列化。总是有一个选项可以将画布导出到图像，但是将大图像上传到服务器需要很大的带宽。就大小而言，没有什么比文本更好的了，这就是为什么 Fabric 为 canvas 序列化和反序列化提供了出色的支持。

## toObject，toJSON

Fabric 中画布序列化的支柱是`fabric.Canvas#toObject`和`fabric.Canvas#toJSON`方法。让我们看一个简单的例子，首先序列化一个空画布:

```
       var canvas = new fabric.Canvas('c');
	JSON.stringify(canvas); // '{"objects":[],"background":"rgba(0, 0, 0, 0)"}'
```

这里我使用的是 ES5 `JSON.stringify`方法，如果这个方法存在的话，它会隐式调用传递的对象上的 toJSON 方法。因为 Fabric 中的 canvas 实例有一个 toJSON 方法，所以我们好像调用了`JSON.stringify(canvas.toJSON())`。

请注意返回的字符串表示空画布。它是 JSON 格式的，本质上由“对象”和“背景”属性组成。“objects”属性当前为空，因为画布上什么也没有，“background”有一个默认的透明值(“rgba(0，0，0，0)”)。

让我们给画布一个不同的背景，看看事情是如何变化的:

```
canvas.backgroundColor = 'red';
	JSON.stringify(canvas); // '{"objects":[],"background":"red"}'
```

如您所料，画布表示反映了新的背景颜色。现在让我们添加一些对象:

```
       canvas.add(new fabric.Rect({
	  left: 50,
	  top: 50,
	  height: 20,
	  width: 20,
	  fill: 'green'
	}));
	console.log(JSON.stringify(canvas));
```

记录的输出如下:

```
'{"objects":[{"type":"rect","left":50,"top":50,"width":20,"height":20,"fill":"green","overlayFill":null,
"stroke":null,"strokeWidth":1,"strokeDashArray":null,"scaleX":1,"scaleY":1,"angle":0,"flipX":false,"flipY":false,
"opacity":1,"selectable":true,"hasControls":true,"hasBorders":true,"hasRotatingPoint":false,"transparentCorners":true,
"perPixelTargetFind":false,"rx":0,"ry":0}],"background":"rgba(0, 0, 0, 0)"}'
```

哇！乍一看，发生了很大的变化，但是仔细观察，您会发现新添加的对象现在是“objects”数组的一部分，序列化为 JSON。注意它的表示是如何包含所有视觉特征的——左、上、宽、高、填充、笔画等等。

如果我们要添加另一个对象，比如说，一个位于矩形旁边的红色圆圈，您会看到该表示相应地发生了变化:

```
canvas.add(new fabric.Circle({
	  left: 100,
	  top: 100,
	  radius: 50,
	  fill: 'red'
	}));
	console.log(JSON.stringify(canvas));
```

下面是记录的输出:

```
'{"objects":[{"type":"rect","left":50,"top":50,"width":20,"height":20,"fill":"green","overlayFill":null,
"stroke":null,"strokeWidth":1,"strokeDashArray":null,"scaleX":1,"scaleY":1,"angle":0,"flipX":false,"flipY":false,
"opacity":1,"selectable":true,"hasControls":true,"hasBorders":true,"hasRotatingPoint":false,"transparentCorners":true,
"perPixelTargetFind":false,"rx":0,"ry":0},"type":"circle","left":100,"top":100,"width":100,"height":100,"fill":"red",
"overlayFill":null,"stroke":null,"strokeWidth":1,"strokeDashArray":null,"scaleX":1,"scaleY":1,"angle":0,"flipX":false,
"flipY":false,"opacity":1,"selectable":true,"hasControls":true,"hasBorders":true,"hasRotatingPoint":false,
"transparentCorners":true,"perPixelTargetFind":false,"radius":50}],"background":"rgba(0, 0, 0, 0)"}'
```

请注意“type”:“rect”和“type:”circle”部分，这样您可以更好地看到这些对象的位置。尽管一开始看起来输出很多，但与图像序列化相比，这根本不算什么。只是为了好玩，看一下大概十分之一(！)的字符串，您可以使用`canvas.toDataURL('png')`:

```
data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAyAAAAK8CAYAAAAXo9vkAAAgAElEQVR4Xu3dP4xtBbnG4WPAQOQ2YBCLK1qpoQE1
/m+NVlCDwUACicRCEuysrOwkwcJgAglEItRQaWz9HxEaolSKtxCJ0FwMRIj32zqFcjm8e868s2fNWoJygl+e397rWetk5xf5pyZd13wPwIEC
BAgQIAAAQIECBxI4F0H+hwfQ4AAAQIECBAgQIAAgQsCxENAgAABAgQIECBAgMDBBATIwah9EAECBAgQIECAAAECAsQzQIAAAQIECBAgQIDAw
QQEyMGofRABAgQIECBAgAABAgLEM0CAAAECBAgQIECAwMEEBMjBqH0QAQIECBAgQIAAAQICxDNAgAABAgQIECBAgMDBBATIwah9EAECBAgQI
ECAAAECAsQzQIAAAQIECBAgQIDAwQQEyMGofRABAgQIECBAgAABAgLEM0CAAAECBAgQIECAwMEEBMjBqH0QAQIECBAgQIAAAQICxDNAgAABA
gQIECBAgMDBBATIwah9EAECBAgQIECAAAECAsQzQIAAAQIECBAgQIDAwQQEyMGofRABAgQIECBAgAABAgLEM0CAAAECBAgQIECAwMEEBMjBq
H0QAQIECBAgQIAAAQICxDNAgAABAgQIECBAgMDBBATIwah9EAECBAgQIECAAAECAsQzQIAAAQIECBAgQIDAwQQEyMGofRABAgQIECBAgAABA
gLEM0CAAAECBAgQIECAwMEEBMjBqH0QAQIECBAgQIAAAQICxDNAgAABAgQIECBAgMDBBATIwah9EAECBAgQIECAAAECAsQzQIAAAQIECBAgQ
IDAwQQEyMGofRABAgQIECBAgAABAgLEM0CAAAECBAgQIECAwMEEBMjBqH0QAQIECBAgQIAAAQICxDNAgAABAgQIECBAgMDBBATIwah9EAECB
AgQIECAAAECAsQzQIAAAQIECBAgQIDAwQQEyMGofRABAgQIECBAgAABAgLEM0CAAAECBAgQIECAwMEEBMjBqH0QAQIECBAgQIAAAQICxDNAg
AABAgQIECBAgMDBBATIwah9EAECBAgQIECAAAECyw+Qb134RU2fevC8q+5esGWESBAgAABAgQIEFiOwPLMC5AlvO0OBMCBAgQIECAAAECJxQ
QICcE9HYCBAgQIECAAAECBPYXECD7W3klAQIECBAgQIAAAQInFBAgJwT0dgIECBAgQIAAAQIE9hcQIPtbeSUBAgQIECBAgAABAicUECAnBPR
2AgQIECBAgAABAgT2FxAg+1t5JQECBAgQIECAAAECJxQQICcE9HYCBAgQIECAAAECBPYXECD7W3klAQIECBAgQIAAAQInFBAgJwTc9+3z49y
vmNd+dI7PzPHJOW6Y4wNzXD3HlXNc9pZdb85/vzbHK3P8aY7n5vj1HL+Y43dz417f97O9jgABAgQIECBAgMBSBATIKd2JCY5dWNwyx5fn+Pw
cV5U/6tXZ99M5fjjHk3Mjd6HifwQIECBAgAABAgQWLSBAirdnouP6WXfvHHfOcU1x9T6rXp4XPTLHA3NTX9jnDV5DgAABAgQIECBA4NACAuS
E4hMdl8+Kr83xzTmuO+G61ttfnEXfnuN7c4PfaC21hwABAgQIECBAgMBJBQTIJQpOeFw7b71/jtsvccWh3vbYfNB9c6NfOtQH+hwCBAgQIEC
AAAECFxMQIMd8No7C4+F5283HfOtZv/ypOYG7hMhZ3wafT4AAAQIECBDYtoAA2fP+H/1Vqwd3f4jf8y1Lfdkunu7xV7OWenucFwECBAgQIEB
g3QICZI/7O/Fxx7xs9wf3t36r3D3evciX7L7F7+6rIY8u8uycFAECBAgQIE
```

…还有大约 17，000 个字符。

你可能想知道为什么还有 `fabric.Canvas#toObject.`很简单，`toObject`返回与 toJSON 相同的表示，只是以实际对象的形式，没有字符串序列化。例如，使用前面的例子，一个只有绿色矩形的画布，`canvas.toObject`的输出如下:

```
       { "background" : "rgba(0, 0, 0, 0)",
	  "objects" : [
	    {
	      "angle" : 0,
	      "fill" : "green",
	      "flipX" : false,
	      "flipY" : false,
	      "hasBorders" : true,
	      "hasControls" : true,
	      "hasRotatingPoint" : false,
	      "height" : 20,
	      "left" : 50,
	      "opacity" : 1,
	      "overlayFill" : null,
	      "perPixelTargetFind" : false,
	      "scaleX" : 1,
	      "scaleY" : 1,
	      "selectable" : true,
	      "stroke" : null,
	      "strokeDashArray" : null,
	      "strokeWidth" : 1,
	      "top" : 50,
	      "transparentCorners" : true,
	      "type" : "rect",
	      "width" : 20
	    }
	  ]
	}
```

如您所见，toJSON 输出本质上是字符串化的`toObject`输出。现在，有趣的(也是有用的)是`toObject`输出很聪明，也很懒。你在“objects”数组中看到的是迭代所有 canvas 对象并委托给每个对象自己的`toObject`方法的结果。例如，`fabric.Path`有自己的`toObject`知道返回路径的“点”数组，而`fabric.Image`有一个`toObject`知道返回图像的“src”属性。在真正面向对象的方式中，所有对象都能够自我序列化。

这意味着当你创建自己的类，或者只是需要定制一个对象的序列化表示时，你需要做的就是使用`toObject`方法，要么完全替换它，要么扩展它。这里有一个例子:

```
var rect = new fabric.Rect();
	rect.toObject = function() {
	  return { name: 'trololo' };
	};
	canvas.add(rect);
	console.log(JSON.stringify(canvas));
```

记录的输出是:

```
'{"objects":[{"name":"trololo"}],"background":"rgba(0, 0, 0, 0)"}'
```

如您所见，objects 数组现在有了矩形的自定义表示。这种类型的覆盖表达了这一点，但可能不是很有用。相反，下面是如何用一个附加属性扩展矩形的`toObject`方法:

```
var rect = new fabric.Rect();
	rect.toObject = (function(toObject) {
	  return function() {
	    return fabric.util.object.extend(toObject.call(this), {
	      name: this.name
	    });
	  };
	})(rect.toObject);
	canvas.add(rect);
	rect.name = 'trololo';
	console.log(JSON.stringify(canvas));
```

下面是记录的输出:

```
'{"objects":[{"type":"rect","left":0,"top":0,"width":0,"height":0,"fill":"rgb(0,0,0)","overlayFill":null,
"stroke":null,"strokeWidth":1,"strokeDashArray":null,"scaleX":1,"scaleY":1,"angle":0,"flipX":false,
"flipY":false,"opacity":1,"selectable":true,"hasControls":true,"hasBorders":true,"hasRotatingPoint":false,
"transparentCorners":true,"perPixelTargetFind":false,"rx":0,"ry":0,"name":"trololo"}],
"background":"rgba(0, 0, 0, 0)"}'
```

我用额外的属性“name”扩展了对象现有的`toObject` 方法，这意味着该属性现在是`toObject` 输出的一部分，因此它出现在 canvas JSON 表示中。另一个值得一提的是，如果像这样扩展对象，您还需要确保对象的“类”(在本例中是“T2”)在“stateProperties”数组中有这个属性，这样从字符串表示加载画布就可以正确地解析并添加到对象中。

## toSVG

另一种有效的基于文本的画布表示是 SVG 格式。由于 Fabric 专门从事 SVG 解析和在画布上呈现，因此让这成为一个双向过程并提供画布到 SVG 的转换是有意义的。让我们将同一个矩形添加到画布上，看看从`toSVG`方法中返回了什么类型的表示:

```
       canvas.add(new fabric.Rect({
	  left: 50,
	  top: 50,
	  height: 20,
	  width: 20,
	  fill: 'green'
	}));
	console.log(canvas.toSVG());
```

记录的输出如下:

```
'<?xml version="1.0" standalone="no" ?><!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 20010904//EN" 
"https://www.w3.org/TR/2001/REC-SVG-20010904/DTD/svg10.dtd"><svg  
xmlns:xlink="https://www.w3.org/1999/xlink" version="1.1" width="800" height="700" 
xml:space="preserve"><desc>Created with Fabric.js 0.9.21</desc><rect x="-10" y="-10" 
rx="0" ry="0" width="20" height="20" style="stroke: none; stroke-width: 1; stroke-dasharray: ; fill: green; 
opacity: 1;" transform="translate(50 50)" /></svg>'
```

就像使用`toJSON`和`toObject`一样，`toSVG`方法——当在画布上调用时——将其逻辑委托给每个单独的对象，每个单独的对象都有自己的`toSVG`方法，该方法专用于该类型的对象。如果你需要修改或扩展一个对象的 SVG 表示，你可以用`toSVG`做同样的事情，就像我之前用`toObject`做的一样。

与 Fabric 专有的`toObject` / `toJSON`相比，SVG 表示的好处在于，您可以将它放入任何支持 SVG 的渲染器(浏览器、应用程序、打印机、相机等)，并且它应该能够正常工作。然而，使用`toObject` / `toJSON`，您首先需要将它加载到画布上。

说到将东西加载到画布上，现在您已经知道如何将画布序列化为有效的文本块，那么如何将这些数据加载回画布呢？

## 反序列化和 SVG 解析器

与序列化一样，从字符串加载画布有两种方式:从 JSON 表示或从 SVG。使用 JSON 表示时，有`fabric.Canvas#loadFromJSON`和`fabric.Canvas#loadFromDatalessJSON`方法。使用 SVG 时，有`fabric.loadSVGFromURL`和`fabric.loadSVGFromString`。

注意，前两个方法是实例方法，直接在 canvas 实例上调用，而另外两个方法是静态方法，在“fabric”对象上调用，而不是在 canvas 上调用。

这些方法大部分没什么好说的。它们完全按照您的预期工作。让我们以画布上之前的 JSON 输出为例，将其加载到一个干净的画布上:

```
       var canvas = new fabric.Canvas();
	canvas.loadFromJSON('{"objects":[{"type":"rect","left":50,"top":50,"width":20,"height":20, 
fill":"green","overlayFill":null,"stroke":null,"strokeWidth":1,"strokeDashArray":null,"scaleX":1,
"scaleY":1,"angle":0,"flipX":false,"flipY":false,"opacity":1,"selectable":true,"hasControls":true,
"hasBorders":true,"hasRotatingPoint":false,"transparentCorners":true,"perPixelTargetFind":false,
"rx":0,"ry":0},"type":"circle","left":100,"top":100,"width":100,"height":100,"fill":"red",
"overlayFill":null,"stroke":null,"strokeWidth":1,"strokeDashArray":null,"scaleX":1,"scaleY":1,
"angle":0,"flipX":false,"flipY":false,"opacity":1,"selectable":true,"hasControls":true,
"hasBorders":true,"hasRotatingPoint":false,"transparentCorners":true,"perPixelTargetFind":false,
"radius":50}],"background":"rgba(0, 0, 0, 0)"}');
```

两个物体都神奇地出现在画布上，如图**图 6** 所示。

![A Circle and a Square Rendered on Canvas](img/c5afd41d3310c7d4b8dd8fac7a9ee9d3.png)
**图六。画布上呈现的一个圆和一个正方形**

所以从字符串加载 canvas 非常容易，但是那个看起来很奇怪的`loadFromDatalessJSON`方法呢？和我们刚才用的`loadFromJSON`有什么不同？为了理解你为什么需要这个方法，看看一个序列化的画布，它有一个或多或少复杂的路径对象，如图 7 中的**所示。**

![A Complex Shape Rendered on Canvas](img/58b38e6ca1f272e9854b01d34c90e320.png)
**图 7。画布上呈现的复杂形状**

图 7 中**形状的 JSON.stringify(canvas)输出如下:**

```
{"objects":[{"type":"path","left":184,"top":177,"width":175,"height":151,"fill":"#231F20","overlayFill":null,
"stroke":null,"strokeWidth":1,"strokeDashArray":null,"scaleX":1,"scaleY":1,"angle":-19,"flipX":false,
"flipY":false,"opacity":1,"selectable":true,"hasControls":true,"hasBorders":true,"hasRotatingPoint":false,
"transparentCorners":true,"perPixelTargetFind":false,"path":[["M",39.502,61.823],["c",-1.235,-0.902,-3.038,
-3.605,-3.038,-3.605],["s",0.702,0.4,3.907,1.203],["c",3.205,0.8,7.444,-0.668,10.114,-1.97],["c",2.671,-1.302,
7.11,-1.436,9.448,-1.336],["c",2.336,0.101,4.707,0.602,4.373,2.036],["c",-0.334,1.437,-5.742,3.94,-5.742,3.94],
["s",0.4,0.334,1.236,0.334],["c",0.833,0,6.075,-1.403,6.542,-4.173],["s",-1.802,-8.377,-3.272,-9.013],["c",-1.468,
-0.633,-4.172,0,-4.172,0],["c",4.039,1.438,4.941,6.176,4.941,6.176],["c",-2.604,-1.504,-9.279,-1.234,-12.619,
0.501],["c",-3.337,1.736,-8.379,2.67,-10.083,2.503],["c",-1.701,-0.167,-3.571,-1.036,-3.571,-1.036],["c",1.837,
0.034,3.239,-2.669,3.239,-2.669],["s",-2.068,2.269,-5.542,0.434],["c",-3.47,-1.837,-1.704,-8.18,-1.704,-8.18],
["s",-2.937,5.909,-1,9.816],["C",34.496,60.688,39.502,61.823,39.502,61.823],["z"],["M",77.002,40.772],["c",0,0,
-1.78,-5.03,-2.804,-8.546],["l",-1.557,8.411],["l",1.646,1.602],["c",0,0,0,-0.622,-0.668,-1.691],["C",72.952,
39.48,76.513,40.371,77.002,40.772],["z"],["M",102.989,86.943],["M",102.396,86.424],["c",0.25,0.22,0.447,0.391,
0.594,0.519],["C",102.796,86.774,102.571,86.578,102.396,86.424],["z"],["M",169.407,119.374],["c",-0.09,-5.429,
-3.917,-3.914,-3.917,-2.402],["c",0,0,-11.396,1.603,-13.086,-6.677],["c",0,0,3.56,-5.43,1.69,-12.461],["c",
-0.575,-2.163,-1.691,-5.337,-3.637,-8.605],["c",11.104,2.121,21.701,-5.08,19.038,-15.519],["c",-3.34,-13.087,
-19.63,-9.481,-24.437,-9.349],["c",-4.809,0.135,-13.486,-2.002,-8.011,-11.618],["c",5.473,-9.613,18.024,-5.874,
18.024,-5.874],["c",-2.136,0.668,-4.674,4.807,-4.674,4.807],["c",9.748,-6.811,22.301,4.541,22.301,4.541],["c",
-3.097,-13.678,-23.153,-14.636,-30.041,-12.635],["c",-4.286,-0.377,-5.241,-3.391,-3.073,-6.637],["c",2.314,
-3.473,10.503,-13.976,10.503,-13.976],["s",-2.048,2.046,-6.231,4.005],["c",-4.184,1.96,-6.321,-2.227,-4.362,
-6.854],["c",1.96,-4.627,8.191,-16.559,8.191,-16.559],["c",-1.96,3.207,-24.571,31.247,-21.723,26.707],["c",
2.85,-4.541,5.253,-11.93,5.253,-11.93],["c",-2.849,6.943,-22.434,25.283,-30.713,34.274],["s",-5.786,19.583,
-4.005,21.987],["c",0.43,0.58,0.601,0.972,0.62,1.232],["c",-4.868,-3.052,-3.884,-13.936,-0.264,-19.66],["c",
3.829,-6.053,18.427,-20.207,18.427,-20.207],["v",-1.336],["c",0,0,0.444,-1.513,-0.089,-0.444],["c",-0.535,
1.068,-3.65,1.245,-3.384,-0.889],["c",0.268,-2.137,-0.356,-8.549,-0.356,-8.549],["s",-1.157,5.789,-2.758,
5.61],["c",-1.603,-0.179,-2.493,-2.672,-2.405,-5.432],["c",0.089,-2.758,-1.157,-9.702,-1.157,-9.702],["c",
-0.8,11.75,-8.277,8.011,-8.277,3.74],["c",0,-4.274,-4.541,-12.82,-4.541,-12.82],["s",2.403,14.421,-1.336,
14.421],["c",-3.737,0,-6.944,-5.074,-9.879,-9.882],["C",78.161,5.874,68.279,0,68.279,0],["c",13.428,16.088,
17.656,32.111,18.397,44.512],["c",-1.793,0.422,-2.908,2.224,-2.908,2.224],["c",0.356,-2.847,-0.624,-7.745,
-1.245,-9.882],["c",-0.624,-2.137,-1.159,-9.168,-1.159,-9.168],["c",0,2.67,-0.979,5.253,-2.048,9.079],["c",
-1.068,3.828,-0.801,6.054,-0.801,6.054],["c",-1.068,-2.227,-4.271,-2.137,-4.271,-2.137],["c",1.336,1.783,
0.177,2.493,0.177,2.493],["s",0,0,-1.424,-1.601],["c",-1.424,-1.603,-3.473,-0.981,-3.384,0.265],["c",0.089,
1.247,0,1.959,-2.849,1.959],["c",-2.846,0,-5.874,-3.47,-9.078,-3.116],["c",-3.206,0.356,-5.521,2.137,-5.698,
6.678],["c",-0.179,4.541,1.869,5.251,1.869,5.251],["c",-0.801,-0.443,-0.891,-1.067,-0.891,-3.473],...
```

…而这仅仅是全部产出的 20 %!

这是怎么回事？事实证明，这个`fabric.Path`实例——这个形状——实际上由数百条贝塞尔曲线组成，这些曲线决定了它将如何精确地被渲染。JSON 表示中的所有这些[“c”，0，2.67，-0.979，5.253，-2.048，9.079]组块对应于这些曲线中的每一条。当有数百个(甚至数千个)时，画布表现最终会变得非常巨大。

像这样的情况是`fabric.Canvas#toDatalessJSON`派上用场的地方。让我们来试试:

```
       canvas.item(0).sourcePath = '/assets/dragon.svg';
	console.log(JSON.stringify(canvas.toDatalessJSON()));
```

下面是记录的输出:

```
{"objects":[{"type":"path","left":143,"top":143,"width":175,"height":151,"fill":"#231F20","overlayFill":null,
"stroke":null,"strokeWidth":1,"strokeDashArray":null,"scaleX":1,"scaleY":1,"angle":-19,"flipX":false,
"flipY":false,"opacity":1,"selectable":true,"hasControls":true,"hasBorders":true,"hasRotatingPoint":false,
"transparentCorners":true,"perPixelTargetFind":false,"path":"/assets/dragon.svg"}],"background":"rgba(0, 0, 0, 0)"}
```

那当然更小了，那么发生了什么？注意，在调用`toDatalessJSO` N 之前，我给了 path (dragon shape)对象一个“/assets/dragon.svg”的`sourcePat` h 属性。然后，当我调用`toDatalessJSON`时，先前输出中的整个庞大的路径字符串(数百个路径命令)被替换为一个“dragon.svg”字符串。

当您处理大量复杂的形状时，`toDatalessJSON`允许您进一步减少画布表示，并用一个简单的 SVG 链接替换庞大的路径数据表示。

您可能会猜测,`loadFromDatalessJSON`方法只是允许您从画布表示的无数据版本加载画布。`loadFromDatalessJSON`方法非常清楚如何获取这些“路径”字符串(如“/assets/dragon.svg”)，加载它们，并使用它们作为相应路径对象的数据。

现在，让我们看看 SVG 加载方法。我们可以使用字符串或 URL。让我们先看看字符串示例:

```
       fabric.loadSVGFromString('...', function(objects, options) {
	  var obj = fabric.util.groupSVGElements(objects, options);
	  canvas.add(obj).renderAll();
	});
```

第一个参数是 SVG 字符串，第二个是回调函数。当 SVG 被解析和加载并接收两个参数——对象和选项时，回调被调用。第一个是 objects，包含从 SVG 解析的对象数组——路径、路径组(对于复杂对象)、图像、文本等等。为了将这些对象组合成一个内聚的集合——并使它们看起来像在 SVG 文档中一样——我们使用了`fabric.util.groupSVGElement` s 并向其传递对象和选项。作为回报，我们得到了一个`fabric.Path`或`fabric.PathGroup`的实例，然后我们可以将它添加到画布上。

`fabric.loadSVGFromURL`方法以同样的方式工作，除了您传递一个包含 URL 而不是 SVG 内容的字符串。注意，Fabric 将试图通过 XMLHttpRequest 获取 URL，因此 SVG 需要符合通常的 SOP 规则。

## 子类

因为 Fabric 是以真正面向对象的方式构建的，所以它的设计使得子类化和扩展简单而自然。正如本系列第一篇文章中所描述的，Fabric 中已经存在对象的层次结构。所有二维对象(路径、图像、文本等等)都继承自`fabric.Object`，一些“类”——比如`fabric.PathGroup`——甚至形成了第三级继承。

那么，如何在 Fabric 中创建一个现有“类”的子类，或者甚至创建一个自己的类呢？

对于这个任务，您需要`fabric.util.createClass`实用程序方法。这个方法只不过是对 JavaScript 原型继承的简单抽象。让我们先创建一个简单的点“类”:

```
       var Point = fabric.util.createClass({
	  initialize: function(x, y) {
	    this.x = x || 0;
	    this.y = y || 0;
	  },
	  toString: function() {
	    return this.x + '/' + this.y;
	  }
	});
```

`createClass`方法获取一个对象，并使用该对象的属性创建一个具有实例级属性的类。唯一经过特殊处理的属性是 initialize，它被用作构造函数。现在，当初始化 Point 时，我们将使用 x 和 y 属性以及`toString`方法创建一个实例:

```
       var point = new Point(10, 20);
	point.x; // 10
	point.y; // 20
	point.toString(); // "10/20"
```

如果我们想创建一个“Point”类的子类——比如一个彩色点——我们可以像这样使用`createClass`:

```
       var ColoredPoint = fabric.util.createClass(Point, {
	  initialize: function(x, y, color) {
	    this.callSuper('initialize', x, y);
	    this.color = color || '#000';
	  },
	  toString: function() {
	    return this.callSuper('toString') + ' (color: ' + this.color + ')';
	  }
	});
```

注意带有实例级属性的对象现在是如何作为第二个参数传递的。第一个参数接收 Point“class ”,它告诉`createClass`使用它作为这个类的父类。为了避免重复，我们使用了`callSuper`方法，它调用父类的方法。这意味着如果我们要更改`Point`，这些更改也会传播到`ColoredPoint`类。

以下是 ColoredPoint 的实际应用:

```
var redPoint = new ColoredPoint(15, 33, '#f55');
	redPoint.x; // 15
	redPoint.y; // 33
	redPoint.color; // "#f55"
	redPoint.toString(); "15/35 (color: #f55)"
```

现在，让我们看看如何使用现有的结构类。例如，让我们创建一个`LabeledRect`类，它本质上是一个矩形，有某种与之相关联的标签。当呈现在画布上时，该标签将被表示为矩形内的文本(类似于前面的带有圆形和文本的组示例)。当您使用 Fabric 时，您会注意到像这样的组合抽象可以通过使用组或使用自定义类来实现。

```
       var LabeledRect = fabric.util.createClass(fabric.Rect, {
	  type: 'labeledRect',
	  initialize: function(options) {
	    options || (options = { });
	    this.callSuper('initialize', options);
	    this.set('label', options.label || '');
	  },
	  toObject: function() {
	    return fabric.util.object.extend(this.callSuper('toObject'), {
	      label: this.get('label')
	    });
	  },
	  _render: function(ctx) {
	    this.callSuper('_render', ctx);
	    ctx.font = '20px Helvetica';
	    ctx.fillStyle = '#333';
	    ctx.fillText(this.label, -this.width/2, -this.height/2 + 20);
	  }
	});
```

看起来这里发生了很多事情，但实际上非常简单。首先，我们将父类指定为`fabric.Rect`，以利用它的渲染能力。接下来，我们定义 type 属性，将其设置为“`labeledRect`”。这只是为了保持一致性，因为所有 Fabric 对象都具有 type 属性(rect、circle、path、text 等等。)然后是我们已经熟悉的构造函数(initialize)，其中我们再次利用了`callSuper`。此外，我们将对象的标签设置为通过 options 传递的任何值。最后，我们剩下两种方法— `toObject`和`_render`。正如您在序列化部分已经知道的，t 方法负责实例的对象(和 JSON)表示。由于`LabeledRect`与常规`rect`有相同的属性，但也有一个标签，我们扩展了父方法`toObject`并简单地添加了一个标签。最后但同样重要的是，`_render`方法负责实例的实际绘制。其中还有另一个`callSuper`调用，用于呈现矩形，以及另外三行文本呈现逻辑。

如果你要渲染这样的对象，你要像下面这样做。**图 8** 显示了结果。

```
       var labeledRect = new LabeledRect({
	  width: 100,
	  height: 50,
	  left: 100,
	  top: 100,
	  label: 'test',
	  fill: '#faa'
	});
	canvas.add(labeledRect);
```

![Rendering of labeledRect](img/c1a333e140b397ff9fb6eb9aa263b7b3.png)
**图 8。labeledRect 的渲染**

改变标签值或任何其他常见的矩形属性显然会像预期的那样工作，正如您在这里和图 9 中看到的。

```
labeledRect.set({
	  label: 'trololo',
	  fill: '#aaf',
	  rx: 10,
	  ry: 10
	}
```

![Modified labeledRect](img/379f95be8aaabb7e9700b655df552880.png)
**图九。修改后的 labeledRect**

当然，现在你可以随意修改这个类的行为。例如，您可以将某些值设为默认值，以避免每次都将它们传递给构造函数，或者您可以在实例上提供某些可配置的属性。如果您确实要配置额外的属性，您可能希望在`toObject`和`initialize`中考虑它们，正如我在这里展示的:

```
       ...
	initialize: function(options) {
	  options || (options = { });
	  this.callSuper('initialize', options);
	  // give all labeled rectangles fixed width/height of 100/50
	  this.set({ width: 100, height: 50 });
	  this.set('label', options.label || '');
	}
	...
	_render: function(ctx) {
	  // make font and fill values of labels configurable
	  ctx.font = this.labelFont;
	  ctx.fillStyle = this.labelFill;
	  ctx.fillText(this.label, -this.width/2, -this.height/2 + 20);
	}
	...
```

## 包扎

这就结束了本系列的第三部分，在这一部分中，我已经深入探讨了织物的一些更高级的方面。在组、序列化和反序列化以及类的帮助下，你可以将你的应用提升到一个全新的水平。

## 分享这篇文章