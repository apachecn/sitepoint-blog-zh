# 用 Flex 实现数据可视化，第二部分

> 原文：<https://www.sitepoint.com/flex-data-visualization-part-2/>

在本系列的第一部分中，我们从 SitePoint 论坛生成了一个数据集，列出了一组关键字和论坛主题中出现的次数。我们还提取了显示这些关键词之间的联系的信息——也就是说，哪些关键词在同一个线程中一起被提及。本文将对我们上次构建的迷你应用程序进行扩展，并将其转化为适当的数据可视化。

这些说明将基于第一篇教程中的已完成代码，但是您可能希望下载 Flex Builder 项目档案，其中包含所有已完成代码。

再一次，我们有一个 Adobe 赞助的[文章测验](https://www.sitepoint.com/quiz/flex3/flex-data-visualization-part-2)来测试你对本教程的理解，所以当你完成时一定要检查一下！

## 问题

以前，我们将数据转储到一个数据网格中，如下所示:

**图一。我们的数据网格**

![Our data grid](img/9e6dbde101eab65e15f4d74417f1a7f7.png)

这使我们能够看到我们收集的数据，但它无法提供任何模式或关系的有用视图。在这个练习中，我们希望看到关键词之间的关系，以及它们之间的相对流行度。我们可以使用一个简单的图表来显示流行度排名，但这仍然不能显示关键词之间的关系。为了把我们想看的东西都集中到一个显示屏上，我们需要更好的视觉效果。

我们需要的是某种网络图，让我们看到关键词之间的联系，也让我们看到哪些关键词更受欢迎。进入弹簧图。spring graph 是一个网络关系显示组件，它显示带有互连线的实体，这些互连线标识它们之间的关系。它被称为 spring graph，因为它依赖于所表示的项目之间的一种弹性，自动将它们以扩散的方式展示出来，让我们可以轻松地看到它们之间的关系。这种可视化通常用于关系映射，例如，显示社交网络中人们之间的链接。

## 构建春季图表

幸运的是，我们有一个免费的组件[来创建 spring graphs](http://mark-shepherd.com/SpringGraph/) ，由 Mark Shepherd 友好地创建并开源。这个组件将为我们完成所有繁重的工作，我们需要做的就是输入我们收集的数据。

有两种方法可以将`SpringGraph`组件包含到您的项目中:您可以下载`.swc`并将其包含到您的项目库中，或者您可以下载整个源代码树并包含它。在大多数情况下，最简单的选择就是将`.swc`添加到您的项目中。从上面的网站下载存档文件并解压。在里面你会找到一个包含`.swc`文件的`/bin`文件夹——将该文件复制到你的 Flex Builder 项目的`lib`目录中。在某些情况下，你可能需要进入项目菜单，选择清理… 。通过在 navigator 窗口中右键单击项目并选择 Properties ，检查您的项目是否已经选择了库。

在属性对话框中，选择左侧菜单上的 Flex 构建路径，然后点击右侧的库路径标签。如果您展开显示的`libs`文件夹，它应该看起来有点像这样(也就是说，它应该包括`SpringGraph.swc`):

**图二。本工程的库路径视图，包括`SpringGraph.swc`**

![The Library path view for our project, including SpringGraph.swc](img/bde92af616c26581522fc1ea57d326a4.png)

现在我们的库中已经有了`SwingGraph.swc`，Flex Builder 将能够在其中找到我们需要的类。为了在我们的代码中使用它们，我们需要为`SpringGraph`类添加一个名称空间到`<mx:Application>`标签中。我们将命名空间称为`sg`:

```
<mx:Application   xmlns:mx="http://www.adobe.com/2006/mxml"  xmlns:sg="com.adobe.flex.extras.controls.springgraph.*"   layout="absolute"  creationComplete="service.send();">
```

下一步是删除我们的数据网格，并用一个 spring 图替换它。我们也不需要数据网格标签函数`listLinks()`，所以我们也可以删除它。像这样实例化一个 spring graph 对象:

```
<fc:SpringGraph   id="mySpringGraph"   backgroundColor="#FFFFFF"   lineColor="#6666ff"   repulsionFactor="5"  right="10" left="10" bottom="10" top="10">⋮ </fc:SpringGraph>
```

## 添加数据提供者

此时，您的应用程序应该成功编译，但是屏幕上什么也看不到。spring graph 就像许多标准的 Flex 数据显示组件一样工作，因为它需要一个数据提供者。我们的`keywordData`变量保存了我们的信息，但是它还不是 spring graph 可以理解的格式。幸运的是，在`SpringGraph`类中有许多可用的类和方法，这将让我们轻松地构建一个合适的数据提供者。

首先，通过将这些导入语句添加到脚本块的顶部，导入几个新的库供我们使用:

```
import com.adobe.flex.extras.controls.springgraph.Item;import com.adobe.flex.extras.controls.springgraph.Graph;
```

`Item`类定义了 spring 图形中的一个项目:一个表示数据的节点，它将被链接到其他节点。`Graph`类被用作`<SpringGraph>`标签的数据提供者，它包含允许我们向图表添加项目并在它们之间创建链接的方法。

让我们创建并实例化一个图形对象。将此添加到`keywordData`变量声明的下面:

```
private var myGraph: Graph = new Graph();
```

我们希望在组件加载时构建图表，因此我们需要一个设置方法。将以下代码添加到变量声明下的脚本块中:

```
private function setup() : void {  // add items  for each (var obj:Object in keywordData) {    var item: Item = new Item(obj.keyword);    myGraph.add(item);  }}
```

在这里，我们循环遍历`keywordData`中的每个关键字，并为每个关键字创建一个新的`Item`对象，通过将关键字作为构造函数参数传递，使用关键字本身作为项目标题。最后，我们将`Item`添加到图中并继续——这构建了一个集合，在我们的数据集中每个关键字包含一个`Item`对象。

接下来，我们需要在相关项目之间创建链接。将此代码添加到下一行的 setup 方法中:

```
// setup linksfor each (var lObj:Object in keywordData) {  var linkItem: Item = myGraph.find(lObj.keyword);  var links:Object = lObj.links;  for each (var link:String in links) {    myGraph.link(linkItem, myGraph.find(link));  }}
```

在这个片段中，我们再次循环关键字，但是这次我们从图形中获取实际的`Item`对象，使用关键字搜索它。这样，我们可以为链接指定“from”项。接下来，我们循环这个关键字链接到的其他关键字(记住它们存储在一个名为`links`的数组中，对应于`keywordData`中的每个关键字)。我们使用“from”项为每个对象创建一个链接，并在 graph 对象中进行第二次查找以获得“to”项。

最后，我们需要将图形设置为 spring 图形显示组件的数据提供者。在设置方法的末尾再添加一行:

```
mySpringGraph.dataProvider = myGraph;
```

现在保存并编译您的项目，然后在浏览器中启动它，您应该会看到一个如下图所示的图形:

**图 3。初步弹簧图**

![Preliminary spring graph](img/783e28cef68c2480c78286424862f199.png)

## 分享这篇文章