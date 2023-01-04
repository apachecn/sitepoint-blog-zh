# 用 YUI 构建一个 Ajax 树

> 原文：<https://www.sitepoint.com/yui-treeview-ajax/>

就 JavaScript 库而言，jQuery 可能是最酷的，但也有很多强有力的竞争者——尤其是雅虎的 YUI(雅虎用户界面)库。它包括全面的功能:DOM 操作、Ajax 助手、丰富的界面小部件…等等！

当你用 YUI 编码时，你正在利用大量免费的、经过验证的代码和支持文档。你应该认为自己使用由网络上最大的玩家之一支持的代码是相当安全的。

在这篇文章中，我们将看看 YUI 难题的两个独立部分，以及如何将它们结合起来创建一个很酷的功能。具体来说，我们将使用 [YUI 树视图](http://developer.yahoo.com/yui/treeview/)组件，这是一种在浏览器中呈现动态分层树的方式。但是我们也将研究 YUI 的 Ajax 助手，因为我们将通过 Ajax 调用动态地充实树的枝干。

我们将在本文中承担的项目将创建一个 TreeView 组件的实例，该实例只有一个文本节点。当用户单击这个节点时，会发出一个 Ajax 请求来查找所单击单词的同义词列表。我们的代码将为返回的每个同义词在树上创建新的节点。反过来，每个新节点都可以点击来创建自己的同义词子树。

在着手这个项目之前，您至少需要掌握一些 JavaScript 知识或技能，尤其是面向对象的 JavaScript(关于面向对象的详细介绍，您可以查看 Ryan Frishberg 的这篇文章)。

本教程的完整邮政编码档案可以从这里下载。

## `asyncRequest`

我们将在本教程中使用的核心方法是异步调用同义词搜索脚本。我们使用 YUI 连接管理器进行呼叫:

```
YAHOO.util.Connect.asyncRequest('GET', stringURL, objectCallBack, null);
```

由于这是一个异步请求，一旦进行了调用，控制流立即返回执行`asyncRequest`之后的语句。

让我们来看看这种方法的论据。前两个参数由请求的类型(在本例中为`GET`)和请求所指向的 URL 组成。我们一会儿将讨论第三个参数，第四个参数仅对于`POST`事务是必需的，所以在我们的例子中它仍然是`null`。

关键论点是 *`objectCallBack`* 。这是它的结构:

```
{  success: funcToDoOnSuccess,  failure: funcToDoOnFailure,  argument: { "argForSuccessOrFailureFuncs": myObject },  timeout: 7000};
```

我们首先需要定义在成功请求(`success`)或错误请求(`failure`)时运行的方法。在上面的例子中，`asyncRequest`方法调用函数`funcToDoOnSuccess`，如果它的`GET`请求成功；对于不成功的`GET`请求，它将调用函数`funcToDoOnFailure`(例如，在 404 页面未找到错误的情况下)。

在参数属性中，我们放置了这些方法完成工作所需的数据。当`asyncRequest`方法调用我们的回调函数(`funcToDoOnSuccess`或`funcToDoOnFailure`)时，它将使用您在这里指定的参数传递给那个函数。

在学习本教程的过程中，我们将用构建同义词树所需的参数替换上面的通用示例参数。

## 链接到 YUI 库

在我们使用任何 YUI 对象之前，我们必须链接到 YUI JavaScript 库。幸运的是，Yahoo 为所有需要的 JavaScript 和 CSS 文件提供了内容交付网络(CDN)托管，并为创建只包含所需功能的自定义链接提供了一个很好的界面。

前往[雅虎依赖配置器](http://developer.yahoo.com/yui/articles/hosting/)，从 YUI JavaScript 实用程序部分选择连接管理器完整版，从YUI 用户界面小部件部分选择树形视图控件。在页面的底部，您会看到如下代码片段:

```
<!-- Combo-handled YUI CSS files: --><link rel="stylesheet" type="text/css" href="…"><!-- Combo-handled YUI JS files: --><script type="text/javascript" src="…"></script>
```

我从上面的代码示例中省略了 URL，因为它们太长了，最好用您需要的功能构造您自己的 URL。这样做的好处是，您可以轻松地包含您需要的任何其他 YUI 组件，而无需添加额外的样式表或脚本文件。只要回到配置应用程序，并生成一组新的链接！

只需将收到的 HTML 片段复制到文档的`head`中，就可以开始使用 YUI 了。

## 创建初始树

第一步是创建一个构建 TreeView 小部件对象的函数。最初，它将只包含一个包含标签“apple”的文本节点当用户单击该节点时，我们的代码将在它下面构建一个子树，并用“apple”的同义词填充它

在下面的代码片段中，首先注意没有加粗的行。我们用 TreeView 的构造函数创建树，它的参数是我们将在其中构建树的 HTML 元素(`AjaxTreeDiv`)。`getRoot`调用接收对树根的引用，并将其传递给 TextNode 的构造函数。给新节点根连接树；它创建了一个指向父节点的链接。我们用树的渲染方法来绘制树。

我们从声明一些变量开始。`obNode`将是节点对象，`obAjaxTree`将是树对象，`treeRoot`将用于保存对树的根节点的引用。

我们调用`TreeView`的构造函数(`YAHOO.widget.TreeView`，传入我们想要在其中构建树的 HTML 元素(`AjaxTreeDiv`)。

突出显示的语句应该最能吸引我们的注意力。`setDynamicLoad`方法告诉树，当用户点击它的一个节点来展开它时，我们想要知道，并且它告诉树当这些点击发生时调用什么函数(`makeMoreNodes`，我们将很快编写它):

```
function buildAjaxTree() {   var obNode;  var obAjaxTree;  var treeRoot;    obAjaxTree = new YAHOO.widget.TreeView ("AjaxTreeDiv");  *obAjaxTree.setDynamicLoad(makeMoreNodes,0);*  treeRoot = obAjaxTree.getRoot();  obNode = new YAHOO.widget.TextNode("apple", treeRoot, false);  obAjaxTree.render();}
```

在设置了那个选项之后，我们在`treeRoot`中存储一个对树根的引用，并创建一个新的`TextNode`。将`treeRoot`变量传递给`TextNode`构造函数将节点与树连接起来。最后，我们调用`render`方法来显示树。

注意，所有这些代码都在一个函数中，我们称之为`buildAjaxTree`。下面是调用它的语句:

```
YAHOO.util.Event.onDOMReady(buildAjaxTree);
```

这是将要执行的代码的第一条语句。当 HTML 页面完全加载时,`onDOMReady`方法调用`buildAjaxTree`。在这之前运行我们的脚本会招致错误。

## 使用 Ajax 调用创建更多节点

现在让我们浏览一下`makeMoreNodes`函数。首先，回头参考本文开头描述的回调对象的概述。请记住，我们的 Ajax 调用(`asyncRequest`)需要一个带有`success`和`failure`方法的回调对象，因此它可以在完成数据收集任务后调用其中一个方法。`makeMoreNodes`中的大部分代码都是用来创建回调对象的。

这是我们将要使用的回调对象。将其与我们在介绍`asyncRequest`时看到的通用回调对象进行比较:

```
var obMkNodesCb = {  success: foundSynonyms,  failure: foundNoSynonyms,  argument: {     "node": nodeToAddTo  },  timeout: 7000};
```

成功和失败属性指的是`asyncRequest`查询我们的服务器端同义词库脚本后将调用的方法。如果 PHP 脚本成功获取了一些同义词，我们将调用`foundSynonyms`函数，如果 PHP 脚本搜索失败，我们将调用`foundNoSynonyms`回调函数。注意，超时属性是这个失败案例中的一个因素:`asyncRequest`如果在被调用的 7 秒钟(7000 毫秒)内没有收到结果，就标记一个失败。

## 论证属性

`asyncRequest`方法要求参数属性是回调对象的一部分。记住参数属性包含由`asyncRequest`调用的成功和失败函数所需的任何数据。对于我们的例子，在 Ajax 调用之前，我们将用户点击的节点存储在参数中。成功方法需要这个节点有两个原因。首先，构建新的同义词子树:为此需要一个根节点，用户单击的节点将是该根节点。其次，通过它的`loadComplete`方法告诉节点我们已经用完了它。如果我们不触发那个方法，树将会冻结，因为它的一个节点不知道什么时候继续监听用户的鼠标点击。

出于同样的原因，失败方法需要访问被点击的节点。即使失败方法没有向树中添加节点，用户点击的节点仍然需要调用它的`loadComplete`方法，因此它可以再次开始监听用户点击。

## 分享这篇文章