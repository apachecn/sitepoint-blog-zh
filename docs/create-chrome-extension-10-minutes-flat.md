# 如何在 10 分钟内创建一个 Chrome 扩展

> 原文：<https://www.sitepoint.com/create-chrome-extension-10-minutes-flat/>

关于使用 Chrome 扩展的更多信息，请观看我们的视频教程[从你的 Chrome 扩展](https://www.sitepoint.com/premium/screencasts/interacting-with-browser-content-from-your-chrome-extension)与浏览器内容互动。

Chrome 浏览器最让我喜欢的一点是它的可扩展性。似乎有一个 Chrome 插件可以满足你所有的需求。

但是，你有没有想过创建自己的 Chrome 扩展？你有没有想过这个过程会有多困难，或者需要做些什么？好吧，事实证明这非常简单——可能比你想象的要简单得多。

在本教程中，我将向你展示如何在大约 5 分钟内创建一个基本的 Chrome 扩展——不是开玩笑！

## 我们将要建造的东西

我对我的网站[http://simpleprogrammer.com](http://simpleprogrammer.com)的速度非常着迷，所以我经常利用像 [GTmetrix](http://gtmetrix.com) 这样的网站来检查我的网站速度，只是为了确保它没有变慢。

![GTmetrix screenshot](img/4784e37668eb753d695aa0b03dcb20e3.png)

我也会经常查看我所在的其他网站，这样我就可以看到它们之间的比较。

嗯，如果有一个 Chrome 扩展，允许你使用 GTmetrix 检查你碰巧浏览的任何网站的网站速度，只需点击一个按钮，这不是很好吗？

我检查了 Chrome 网络商店，没有一个扩展可以执行这个操作，所以这正是我们今天要做的。

## 什么是 Chrome 扩展？

在我们开始构建我们的扩展之前，对什么是 Chrome 扩展以及 Chrome 扩展如何工作有一个基本的了解可能是一个好主意。

在非常基本的层面上，Chrome 扩展只是一些 HTML、CSS 和 JavaScript，允许你通过 Chrome 公开的一些 JavaScript APIs 向 Chrome 添加一些功能。一个扩展基本上只是一个网页，托管在 Chrome 中，可以访问一些额外的 API。

在本教程中，我将向你展示如何创建一个叫做*浏览器动作*扩展的基本 Chrome 扩展。这种扩展在 Chrome 工具栏中放置了一个按钮，点击后会显示一个 HTML 页面，并选择性地执行一些 JavaScript。

通过使用*页面动作*，Chrome 扩展也可以被创建为仅在特定页面上工作，它们可以使用*背景页面*在后台运行代码，它们甚至可以使用*内容脚本*修改浏览器中加载的现有页面。但是对于本教程，我们将保持简单。

如果你有兴趣学习更多关于 Chrome 扩展的知识，请查看 Chrome 的扩展文档。

## 步骤 1:创建项目

我们需要做的第一件事是创建项目和扩展所需的所有文件。让我们首先创建一个新目录，我们称之为“GTmetrix Extension”我们会把扩建所需的所有文件放在这个新文件夹里。Chrome 允许我们通过指向包含扩展文件的文件夹来加载插件。

所有的 Chrome 扩展都需要一个清单文件。清单文件告诉 Chrome 它需要知道的一切，以便在 Chrome 中正确加载扩展。因此，我们将创建一个`manifest.json`文件，并将其放入我们创建的文件夹中。您可以暂时将清单文件留空。

接下来，我们需要一个扩展图标。这只是需要一个 19x19px 的 PNG 文件。你可以从谷歌的演示项目中得到一个[样本图标，你可以修改它。](http://developer.chrome.com/extensions/examples/tutorials/getstarted/icon.png)

接下来，我们需要一个 HTML 页面来显示用户何时单击我们的浏览器操作，因此我们将在“GTmetrix Extension”目录中创建一个`popup.html`文件和一个`popup.js`文件。

由于安全限制，我们不能将内联 JavaScript 放入 Chrome 扩展中的 HTML 文件，所以我们必须创建一个单独的文件来保存我们需要的任何 JavaScript 代码，我们将从 HTML 文件中引用它。

## 步骤 2:创建清单文件

现在我们已经有了基本的项目结构，我们需要将代码添加到我们的清单文件中来描述我们的 Chrome 插件。

打开 manifest.json 文件，输入以下代码:

```
{
  "manifest_version": 2,

  "name": "GTmetrix Analyzer Plugin",
  "description": "This extension will analyze a page using GTmetrix",
  "version": "1.0",

  "browser_action": {
   "default_icon": "icon.png",
   "default_popup": "popup.html"
  },
  "permissions": [
   "activeTab"
   ]
}
```

这个 JSON 文件中的大多数字段都是不言自明的，所以我不会浪费您的时间来解释一切，但是请注意`browser_action`部分，在这里我们指定了默认图标是什么，以及当单击浏览器动作按钮时应该显示什么 HTML 页面。

您还会注意到我添加了一个`permissions`部分，指定我们需要有访问 activeTab 的权限。这是必需的，以便我们能够获取当前选项卡的 URL，并将其传递给 GTmetrix。

Chrome 为您提供的许多 API 都要求您指定所需的权限。

## 步骤 3:创建用户界面

下一步是创建用户界面，当我们的浏览器动作被点击时将显示该界面。

![GTmetrix Button](img/9ce1f60d53e87308105478eabee17ea6.png)

我们的用户界面将非常简单，由一些显示“GTmetrix Analyzer”的文本组成，后跟一个按钮，用户可以单击该按钮在当前页面上执行分析。

打开`popup.html`页面，添加以下内容:

```
<!doctype html>
<html>
  <head>
    <title>GTmetrix Analyzer</title>
    <script src="popup.js"></script>
  </head>
  <body>
    <h1>GTmetrix Analyzer</h1>
    <button id="checkPage">Check this page now!</button>
  </body>
</html>
```

你会注意到在这个 HTML 中我包含了`popup.js`脚本。这是我们放置扩展逻辑的地方，当点击 id 为`checkPage`的按钮时，扩展将执行。

## 步骤 4:实现逻辑

创建插件我们需要做的最后一件事是实现当用户点击“现在检查这个页面！”时应该执行的逻辑选项卡内的按钮。

我们想要添加一个事件监听器来监听`checkPage`按钮上的`click`事件。当它被点击时，我们需要创建一个新的表单提交给 GTmetrix，它包含当前页面的 URL，提交它，然后显示结果。

打开`popup.js`文件并添加以下代码:

```
document.addEventListener('DOMContentLoaded', function() {
  var checkPageButton = document.getElementById('checkPage');
  checkPageButton.addEventListener('click', function() {

    chrome.tabs.getSelected(null, function(tab) {
      d = document;

      var f = d.createElement('form');
      f.action = 'http://gtmetrix.com/analyze.html?bm';
      f.method = 'post';
      var i = d.createElement('input');
      i.type = 'hidden';
      i.name = 'url';
      i.value = tab.url;
      f.appendChild(i);
      d.body.appendChild(f);
      f.submit();
    });
  }, false);
}, false);
```

我借用了 GTmetrix 网站上提供的 bookmarklet 中的大部分代码来创建和提交表单。我只是修改了代码，从当前活动的选项卡中获取 URL。

如果您检查上面的代码，您会看到我们首先为`checkPage`按钮上的`click`事件注册了一个处理程序。然后，当单击按钮时，我们获得当前选择的选项卡，并执行一些 JavaScript 来创建一个带有一些隐藏字段的表单，该表单被提交给 GTmetrix。我们使用当前选项卡中的 URL 来告诉 GTmetrix 对哪个页面执行测试。

## 测试它

在 Chrome 中测试一个新的扩展真的很容易。在选项卡中键入“chrome://extensions”以打开扩展页面。

![Chrome Extensions Page](img/238324947894c623727cff610b5ce94c.png)

在此页面上，选中“开发人员模式”以允许加载未打包的扩展。这将允许您从文件夹中加载您的扩展。最后，单击“Load unpacked extension”或简单地将“GTmetrix Extension”文件夹拖到页面上以加载扩展。您应该立即看到该扩展作为浏览器操作出现，并且您的图标出现在当前选项卡的工具栏窗口中。

要测试该扩展，请导航到您想要用 GTmetrics 测试的页面。然后，单击我们的 GTmetrix 扩展的图标。当 HTML 页面出现时，点击“立即检查此页面！”您应该会立即看到当前页面显示的请求和结果。

就是这样！如果你有任何问题或疑问，请随时加入下面的讨论。我希望这篇关于创建 Chrome 扩展的介绍足以让你入门。

*通过我们的截屏教程[继续构建 Chrome 扩展，与来自你的 Chrome 扩展的浏览器内容进行交互](https://www.sitepoint.com/premium/screencasts/interacting-with-browser-content-from-your-chrome-extension)。*

## 分享这篇文章