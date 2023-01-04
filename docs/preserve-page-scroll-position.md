# 更多可用的表单–控制滚动位置

> 原文：<https://www.sitepoint.com/preserve-page-scroll-position/>

我一直在寻找提高和改进我的网络应用程序可用性的方法。特别是，我总是试图找到使我的表单更好的方法，因为，让我们面对它:如果你正在构建 Web 应用程序，你将构建表单。最有可能的是，您将构建许多表单。

任何观察过普通用户尝试完成一个表单或一系列表单的人都知道，这种体验通常令人沮丧。采取额外的措施来提高你的表单的可用性——即使是很小的方面——这样你就可以从整体上提高你的应用程序的用户友好性。

提高表单可用性的一种方法是在回发后保持页面滚动。例如，您可能希望在搜索页面中使用该功能，用户可以在该页面上按列对结果进行排序。如果您在页面顶部有一个接受搜索参数的表单，并在下面显示结果，那么每当用户想要按给定的列对结果进行排序时，就必须向下滚动页面，越过参数，找到结果，这可能会非常烦人。幸运的是，我们可以对此做些什么。

##### 解决方案

在本教程中，我将向您展示当一个表单页面回发到自身时，如何轻松地保持页面的滚动位置。虽然我在这里用 ColdFusion 编写了这个例子，但是这项技术可以很容易地移植到其他语言。

为了让这项技术发挥作用，你只需要做两件事。您必须编写一些 JavaScript 来获取页面的当前 X 和 Y 滚动坐标，并将它们放入表单中，以便在您回发到当前页面时提交它们。然后，您必须获取这些 X 和 Y 滚动坐标，并通过服务器端和客户端脚本的组合，在页面重新加载后设置页面的滚动坐标。小菜一碟！

##### 表单代码

我在这里使用了一个基本的表单，这样你就可以清楚地关注这个过程是如何工作的，而不是创建一个我上面提到的那种更复杂的搜索页面。一旦你知道如何让事情运转起来，你就可以很容易地把这个例子应用到你自己的需要中，并且变得和你想的一样聪明。

```
<cfparam name="FORM.name" default="Testing"> 

<html> 

<head> 

   <title>Test</title> 

</head> 

<body> 

<form name="Form1"  

   method="POST"  

   action="<cfoutput>#CGI.SCRIPT_NAME#</cfoutput>"> 

   <p>1</p> <p>2</p> <p>3</p> <p>4</p> <p>5</p>  

   <p>6</p> <p>7</p> <p>8</p> <p>9</p> <p>10</p> 

   <hr width="2000"> 

   <nobr> 

   Name: <input type="text" name="name" value="<cfoutput>#FORM.name#</cfoutput>"> 

   <input type="submit" value="Submit"></nobr> 

   <hr width="2000"> 

   <p>11</p> <p>12</p> <p>13</p> <p>14</p> <p>15</p>  

   <p>16</p> <p>17</p> <p>18</p> <p>19</p> <p>20</p> 

</form> 

</body> 

</html>
```

这个表单页面目前不做任何特别的事情。它只是回发到自己，传递 name 参数。我已经对段落和数字进行了编码，这样我们在页面上就有了可滚动的内容，并且我们有了一个可视化的向导来帮助判断页面已经垂直滚动了多远(注意，这个脚本将保留垂直和水平滚动)。

##### 追踪卷轴

第一步是添加一些 JavaScript 来获取 X 和 Y 滚动值，并将它们放入表单中。为此，我们需要在表单中添加两个隐藏输入，以及几行 JavaScript 代码，这些代码将在提交表单时获取当前的滚动值，并将它们传递给隐藏输入。`saveScrollCoordinates()`函数将查看浏览器对`document.all`的支持，并使用正确的引用来获取滚动值并相应地更新表单输入。在本例中，我们将使用表单中的`onSubmit()`事件处理程序来调用它。

在这个例子中，我编写了将滚动值存储为文本字段的隐藏字段，所以如果您眼力快，就可以在提交表单之前看到值是如何变化的。

```
<cfparam name="FORM.name" default="Testing"> 

<html> 

<head> 

   <title>Test</title> 

<script language=javascript> 

function saveScrollCoordinates() { 

   document.Form1.scrollx.value = (document.all)?document.body.scrollLeft:window.pageXOffset; 

   document.Form1.scrolly.value = (document.all)?document.body.scrollTop:window.pageYOffset; 

} 

</script> 

</head> 

<body> 

<form name="Form1"  

   method="POST"  

   onSubmit="saveScrollCoordinates()"  

   action="<cfoutput>#CGI.SCRIPT_NAME#</cfoutput>"> 

   <p>1</p> <p>2</p> <p>3</p> <p>4</p> <p>5</p>  

   <p>6</p> <p>7</p> <p>8</p> <p>9</p> <p>10</p> 

   <hr width="2000"> 

   <nobr> 

   Name: <input type="text" name="name" value="<cfoutput>#FORM.name#</cfoutput>"> 

<input type="text" name="scrollx" value="0"> 

   <input type="text" name="scrolly" value="0"> 

   <input type="submit" value="Submit"></nobr> 

   <hr width="2000"> 

   <p>11</p> <p>12</p> <p>13</p> <p>14</p> <p>15</p>  

   <p>16</p> <p>17</p> <p>18</p> <p>19</p> <p>20</p> 

</form> 

</body> 

</html>
```

第二步是获取那些通过表单传递的值，并对它们进行处理。为此，我们需要添加几行带有少量 ColdFusion 的 Javascript。首先，我们将在页面顶部插入两个额外的`cfparam`标签，这样我们就可以在每次页面加载时使用一些默认的滚动值。然后，我们将定义`scrollToCoordinates()`函数来设置页面滚动，并在页面加载时使用`<body>`标签中的`onLoad()`事件处理程序调用它。

```
<cfparam name="FORM.name" default="Testing"> 

<cfparam name="FORM.scrollx" default="0"> 

<cfparam name="FORM.scrolly" default="0"> 

<html> 

<head> 

   <title>Test</title> 

<script language=javascript> 

function scrollToCoordinates() { 

   <cfoutput> 

   window.scrollTo(#FORM.scrollx#, #FORM.scrolly#); 

   </cfoutput> 

} 

function saveScrollCoordinates() { 

   document.Form1.scrollx.value = (document.all)?document.body.scrollLeft:window.pageXOffset; 

   document.Form1.scrolly.value = (document.all)?document.body.scrollTop:window.pageYOffset; 

} 

</script> 

</head> 

<body onload="javascript:scrollToCoordinates()"> 

<form name="Form1"  

   method="POST"  

   onSubmit="saveScrollCoordinates()"  

   action="<cfoutput>#CGI.SCRIPT_NAME#</cfoutput>"> 

   <p>1</p> <p>2</p> <p>3</p> <p>4</p> <p>5</p>  

   <p>6</p> <p>7</p> <p>8</p> <p>9</p> <p>10</p> 

   <hr width="2000"> 

   <nobr> 

   Name: <input type="text" name="name" value="<cfoutput>#FORM.name#</cfoutput>"> 

<input type="text" name="scrollx" value="0"> 

   <input type="text" name="scrolly" value="0"> 

   <input type="submit" value="Submit"></nobr> 

   <hr width="2000"> 

   <p>11</p> <p>12</p> <p>13</p> <p>14</p> <p>15</p>  

   <p>16</p> <p>17</p> <p>18</p> <p>19</p> <p>20</p> 

</form> 

</body> 

</html>
```

下面是详细的分析，描述了当用户单击按钮提交表单时会发生什么:

1.  `saveScrollCoordinates()`函数由表单的`onSubmit()`事件处理程序执行。

3.  `saveScrollCoordinates()`用适当的值更新`scrollx`和`scrolly`隐藏输入。

5.  表单回发到同一个页面。

7.  ColdFusion 表单参数`scrollx`和`scrolly`在`scrollToCoordinates()`功能中设置滚动坐标。

9.  `<body>`标签中的`onLoad()`事件处理程序调用`scrollToCoordinates()`函数。

11.  根据传递的坐标调整窗口的滚动属性。

测试表单，您会注意到，无论您滚动到哪里，当您提交页面时，无论是垂直还是水平方向，您都会回到开始时的位置。为了更好地测试水平滚动，请调整浏览器大小，使其非常窄，然后提交表单。你的 X 轴和 Y 轴的滚动会自动保留下来，另一种方法是使用 href 锚点，但是这样做不够精确和平滑。

##### 包扎

我上面给出的例子怎么样，我们有一个搜索页面，包含一个输入表单和可以按列排序的结果。在这种情况下，每当用户选择单击列标题进行排序时，您可能会提交表单并执行一段值略有不同的 SQL 语句。如果您正在用新的排序列和排序方向更新一些隐藏的输入，那么将这个特性添加到您的页面应该是一件容易的事情。您只需要在表单中插入额外的隐藏输入，并将这里展示的 JavaScript 合并到您自己的 JavaScript 中。排序链接可以使用`onClick()`事件处理程序来更新隐藏的滚动值并重新提交表单。

我给你们展示的只是这种技术的一个简单例子。这里的代码已经在 Mac 电脑上使用 Internet Explorer 6 和 Mozilla Firefox for Windows 以及 Safari 进行了测试。使用这种技术背后的思想作为起点。应用您自己的创造力和技能，为您在应用程序中使用的表单添加更加精致、直观的感觉。

## 分享这篇文章