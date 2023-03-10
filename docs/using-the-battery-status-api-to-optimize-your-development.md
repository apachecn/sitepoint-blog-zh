# 使用电池状态 API 优化您的开发

> 原文：<https://www.sitepoint.com/using-the-battery-status-api-to-optimize-your-development/>

![battery icon](img/148ca429ab98a5314999c67c3a2bcaf1.png)

想象一下，如果您可以对您的应用程序进行编程，以适应最终用户的设备，例如，如果用户的设备电池电量低，则关闭某些进程。你可以的。

[`Device APIs Working Group`](https://www.w3.org/2009/dap/#roadmap) 是一系列规范，旨在为开发人员提供对用户设备的附加信息的访问，以便创建更定制的体验。这个工作组包含几个发展中的标准，包括我们将在本教程中看到的 [`Battery API`](https://www.w3.org/TR/battery-status/) 。

Battery API 现在在一些桌面和移动浏览器上都得到了支持，所以现在是一个很好的时机来看看 Battery API，看看它是什么，它在哪里得到支持，以及为什么您应该考虑在您即将到来的项目中利用它。

## 电池 API:简介

电池状态 API (Battery API)就像它听起来的那样:一个让开发人员访问当前用户电池信息的界面。

这是一个 JavaScript 级别的 API，使开发人员能够挂钩用户设备的各种事件和属性。例如，如果我们可以访问 API，我们可以获得当前的电池百分比，并基于该值设置良好的默认设置。例如，如果用户的设备处于低电量状态，我们可以删除所有后台任务，只手动执行。

自 2011 年初以来，该 API 一直处于开发阶段，在 W3C 从工作草案到候选推荐状态之间来回移动。在此期间，有几个浏览器已经采用了这种支持——尽管有些比其他的多。

## API 如何帮助我们？

由于规范尚未最终确定，我们不能指望开发完全依赖于电池 API 的网站或 web 应用程序。然而，我们*所能做的*是提供额外的功能，或者更加定制化的体验，如果当前的浏览器支持 API 的话——也就是说，以渐进增强的方式使用它。

### 我们可以获取哪些信息？

因为我们可以访问用户的电池状态，所以我们可以确定他们当前的电池电量以及设备是在充电还是放电。此外，我们有时可以获得设备的大致充电或放电时间(直到设备充电或死亡的时间)。

有了这些信息，我们就可以开始改变用户体验来帮助用户。

### 我们能用这些信息做什么呢？

尽管电池优化通常不在开发人员的考虑范围内，但它是一个应该考虑的因素，尤其是在一切都向移动方向发展的情况下。

网站提供了一系列有用但昂贵的操作，可以调整这些操作，以便用户在网站上花费更少的电量。(没有人喜欢拆你电池的网站或网络应用。)

让我们来看看一些节省电池电量的方法:

*   我们可以**定制地理定位元素**,使它们具有较低的精确度，并且默认情况下不请求使用内置 GPS。我们可以向用户显示两个选项，一个是正常请求，另一个是高精度请求。
*   我们可以**将视频流或回放设置为默认的较低质量**水平，使数据传输更快(从而减少设备的工作量)。可以提供下载高质量流的选项。
*   我们可以**减少复杂的 CSS 动画**或界面元素，提供更基本的界面。
*   我们可以使用电池电量来确定后台任务运行的默认时间间隔，比如 Ajax 和 Web Workers。我们可以使用状态来确定我们的任务应该被调用的频率，以便最小化网络使用。提供一个选项或设置，让用户可以设置他们想要的也是一个好主意。

总的来说，这个想法是我们可以使用电池 API 来提供一些节省电池电量的好的默认设置，同时提供更多与网站交互的选项。

## 浏览器支持

由于电池 API 还没有最终确定，它还没有得到所有浏览器的普遍支持。

从积极的一面来看，很多浏览器现在都支持它**(截至 2015 年 11 月)**，所以现在是开始使用这个 API 的最佳时机。

### 移动设备

![Mobile browser support for battery status API](img/f4a61379721f5af58aaeee972cff4c2d.png)

移动浏览器是支持最重要的地方。FireFox mobile 的支持时间最长，可以追溯到 18 版本的早期(2013 年 1 月)。Opera 在 25 版(2014 年 10 月)跟进，最后在 40 版(2015 年 4 月)推出 Chrome。

*   Firefox 18+
*   Opera Mobile 25+
*   铬 42 +

### 桌面

![Desktop browser support for battery status API](img/e2e8fb5e4afd59b1dd144b1878902960.png)

虽然电池 API 对移动设备(及其浏览器)最有意义，但支持跨传统浏览器的 API 仍然很重要，因为它们通常用于笔记本电脑和其他便携式设备(在这些设备上 API 很有用)。

Firefox 的支持历史也是最长的，可以追溯到 18 版(2012 年推出)。Chrome 在第 39 版(2014 年 10 月)中增加了支持，紧随其后的是 Opera 的第 25 版(2014 年 10 月)

*   Firefox 18+
*   铬 39+
*   歌剧 25+

## 一个实际的例子:减少后台 API 调用

如果你想跳过这一部分，你可以直接进入这个演示的完整代码。

使用 API 的一个好方法是检测用户当前的电池状态，并为数据和处理器密集型任务设置默认间隔。

网站通常会动态地请求新信息，在用户进行正常的网站交互时，使用 Ajax 或其他技术来拉或推数据。

这些事件通常由用户启动，例如当他们想要加载附加内容时。其他时候，它们在后台发生，没有任何用户输入。

通过查看电池状态，我们可以为用户选择最佳的默认体验。

![Example overview of the CatAPI and battery API. Showcases what the end user will see](img/89edaf2f5b359830c712574a6b9b3706.png)

[点击此处查看此示例的实况](http://code.runnable.com/VkfyK7VTxa1Jq_si/using-the-battery-status-api-cat-api-for-php-curl-and-curl)。该示例在支持该 API 的设备和浏览器上效果最佳。

## 示例概述

该网站将显示来自[卡特彼勒 API](http://thecatapi.com/) 的随机图像，并将它们展示给我们的用户。一旦用户加载页面，它将不断地从 API 中提取图像并替换我们现有的图像。

**我们接下来要做的是:**

1.  创建一个新的 PHP 类来处理与 CatAPI 的交互。

2.  扩展一个新类，它将用于从 CatAPI 获取一个图像，并将其返回给我们的浏览器进行处理。(这个过程将通过 Ajax 执行。)

3.  构建我们的基本登录页面。该页面最初将从 CatAPI 获取一些图像，并显示关于电池状态和电池 API 的有用信息。

4.  创建与`battery status API`的 JavaScript 交互。正是在这里，大部分有趣的事情将会发生——当我们检查 API 支持以及我们如何与之交互时。

如果你是 cURL 的新手，最好先[了解一下它。](https://www.sitepoint.com/using-curl-for-remote-requests/)

如果你想直接得到代码，请随意从 SitePoint GitHub 帐户获取一份副本。

## CatAPI:快速概述

Cat API 让我们获取猫的随机图像，这将完美地工作，因为我们所需要的只是根据当前电池状态每 X 秒传回给我们的基本随机图像。

我们将使用 API 的 [get 方法来收集图像。此方法概述了几个选项，您可以使用它们来修改您的请求。然而，为了我们的需要，我们将](http://thecatapi.com/docs.html#get)[保持它相当简单](http://thecatapi.com/docs.html#images-get):

```
/aimg/get
```

![CatAPI parameters](img/403d484347006549a8415d7d34c3c25f.png)

**注意:**通常，你不需要使用 PHP 来连接你的 API 您只需构建您的 URL 并用 JavaScript 连接到 API。然而，CatAPI 目前不支持 JSON，所以我们也可以在服务器上使用 cURL 来获取我们的信息。

## 电池 API 等级

我们与 CatAPI 的主要交互将通过`battery_api_class.php`文件进行。

在这个类中，我们定义了几个方法，我们将使用这些方法与 CatAPI 进行交互，并显示关于电池状态 API 的有用信息。让我们快速浏览一下这些是做什么的。

### get_image_items($method，$arguments = array())

这个函数用 CatAPI 初始化一个 cURL 会话。

它接受用于 API 的方法以及一组参数。

对于我们的例子，方法将是`/aimg/get?`，我们的数组将有我们的选项(比如选择返回图像的大小、返回格式、图像的类型等等。).

我们现在用 [`curl_init()`](http://php.net/manual/en/function.curl-init.php) 开始我们的 cURL 请求，用 [`curl_exec`](http://php.net/manual/en/function.curl-exec.php) 抓取它的内容。完成后，我们关闭与 [`curl_close`](http://php.net/manual/en/function.curl-close.php) 的连接。

我们用 [`curl_error`](http://php.net/manual/en/function.curl-error.php) 和 [`curl_errno`](http://php.net/manual/en/function.curl-errno.php) 检查错误状态和错误代码号。如果我们没有错误消息，并且我们有内容，我们就开始处理返回的信息。对于我们的例子，我们将从 XML 中获取数据，所以我们从 cURL 内容中创建一个新的 [`SimpleXMLElement`](http://php.net/manual/en/simplexmlelement.construct.php) ，然后遍历每个记录。

一旦我们得到了我们需要的信息，我们就返回图像，或`false`，这取决于我们的 cURL 请求的进展。

### 显示 _ 图像 _ 项目($图像 _ 数组)

该函数显示我们在`get_image_items()`函数中收集的所有保存的信息。如果我们有图像，我们显示一个手动加载按钮来获取图像。(这将在我们使用关键电池且不运行后台任务时显示。)

我们遍历记录并打印每张图片的标记。我们使用了几个内部容器，因为我们利用了`object-fit`和`object-position` CSS 属性为我们提供了完美的方形、完全响应的图像元素。

### 显示 _ 电池 _ 示例 _ 描述()

这个函数为用户显示介绍性文本，告诉他们这个网页及其工作原理。相当标准。

### 显示 _ 电池 _ 信息 _ 摘要()

此功能显示设备支持电池 API 的所有摘要信息。它指示设备是否支持 API、当前充电百分比、设备是否正在充电，以及最重要的是，下一次调用 CatAPI 的当前时间间隔。

此外，我们还定义了可用于触发其他电池状态的模拟按钮(这样您就可以模拟低电池状态)。

## 电池 API 子类

基于设备当前的电池电量，我们的 JavaScript 会定期调用`battery_api_single.php`类。较高的电池电量会导致对此文件的调用更加频繁。

这个类的目的是扩展父类`battery_api`并使用它的`constructor`从 CatAPI 获取图像。我们首先加载我们的父类，并基于它扩展一个新类。

我们首先在`$arguments`变量中定义一个选项数组。我们将数据类型设置为 XML，并从 CatAPI 中提取一个小的 JPG。我们将`$method`定义为`/aimg/get?`的`get`方法

然后我们调用`get_image_items()`函数，传入我们的`$method`和`$arguments`。我们将返回一组图像信息。然后，我们使用 [`json_encode`函数](http://php.net/manual/en/function.json-encode.php)将一个经过编码的信息字符串传回我们的网站。

## 网站登录页面

`index.php`是我们访问示例时将到达的登录页面。该文件本身是基本的，充当我们不同功能的包装器，因此我们可以显示我们的示例描述、信息摘要和 CatAPI 中初始图像的网格。

我们加载`battery_api_class.php`文件，然后调用 CatAPI 来获取初始的八个图像序列，以便在页面加载时显示。

## JavaScript 与电池状态 API 的交互

可以说，该示例最重要的部分是与电池状态 API 和各种其他函数的交互，这些函数有助于将所有内容组合在一起。

`battery_api_scripts.js`文件包含几个相互连接的函数，这些函数提供与电池状态 API 的连接，并在我们的示例中设置/更新各个区域。

### 全局变量

在文件的最顶端，我们创建了我们需要的所有变量。这些变量最终将包含字符串、布尔值、对象等。这里有一个快速分类:

| 可变的 | 描述 |
| 电池 | 电池对象本身(电池管理器对象或假) |
| 电池 _ 支持 | 如果我们支持电池 API(对或错) |
| 电池电量 | 当前电池电量(满分 100) |
| 电池正在充电 | 设备当前正在充电吗(对还是错) |
| 电池 _ 状态 | 电池状态(使用此状态确定其他操作) |
| api _ 操作 _ 间隔 _ 项目 | 保存用于调用 CatAPI 的 setInterval 函数的变量 |
| api _ 操作 _ 计数器 _ 项目 | 用于计算下一次调用 CatAPI 之前剩余时间的变量 |
| api_action_interval_time | 系统当前执行 CatAPI 调用的时间(3000 毫秒、5000 毫秒、8000 毫秒或 false) |
| api_action_counter_time | 我们将更新计数器的时间(以 X 秒显示，我们将调用 CatAPI) |

我们在整个示例中广泛使用了这些，并且大多数用于触发其他操作。

### 检测对电池 API 的支持

页面一准备好，我们就执行`battery_start()`函数。其目的是测试电池 API 支持。由于 API 在不断变化，我们需要检查两种不同的方式，看看我们是否有支持。

*   旧的 [`navigator.battery`](https://developer.mozilla.org/en-US/docs/Web/API/Navigator/battery) 值存在于旧的浏览器中，如果我们有对象的话，它会直接返回`batteryManger`对象。如果我们有支持，我们将`battery`变量设置为`true`，并开始执行我们的函数。

*   更新的 [`navigator.getBattery`](https://developer.mozilla.org/en-US/docs/Web/API/Navigator/getBattery) 函数只存在于更新的浏览器中，它不是直接给你 [`batteryManager`](https://developer.mozilla.org/en-US/docs/Web/API/BatteryManager) 对象，而是执行一个`promise`尽快给你对象。一旦我们得到这个承诺的值，我们将变量`battery`设置为`true`，并开始执行我们的函数。

如果没有支持，我们将变量`battery`设置为`false`并继续我们的设置操作。

### 设置/更新电池状态信息

在我们的流程中，下一步将调用`update_status_information()`函数。它调用了一大堆其他帮助函数，这些函数将基于 API 的支持来设置我们的变量。

因为有许多元素需要跟踪，所以我们偶尔会调用这个函数来更新值——比如当我们提高充电百分比时，或者当我们切换模拟电池状态时。

*   `get_battery_support()`:检查我们的`battery`变量，看看我们是否有一个电池管理器对象(如果我们支持 API，我们就会有)。如果我们有支持，我们把它设置为真，否则我们把它设置为假。
*   `get_battery_level()`:检查是否有我们的`battery`变量。如果我们这样做，它使用电池管理器对象的`level`属性来设置我们的`battery_level`变量。(级别值代表 0 到 1 之间的值，因此我们将该值乘以 100，得到我们的百分比。)
*   `get_battery_status()`:这决定了设备将被设置成什么样的`state`字符串。这个状态保存在我们的`battery_status`变量中，并在网站的其他区域使用(比如我们的主 Ajax 函数)。如果我们可以访问 API，我们将检查当前的`battery_level`以确定设备是否将处于`high`、`medium`、`low`或`critical`状态。如果我们没有访问 API 的权限，我们将它设置为`no_support`。
*   `get_battery_charging()`:如果我们支持 API，我们看一下`charging`属性。这个属性让我们知道设备是否正在充电。我们相应地设置`battery_is_charging`的值。
*   `set_background_colour()`:检查`battery_status`变量的当前状态，并为`HTML`元素设置一个类。这主要用于演示，每个状态在背景上设置不同的颜色。
*   这个函数决定了我们调用 Ajax 函数从 CatAPI 中获取新图像的频率。我们检查`battery_support`变量，如果我们有支持，然后我们将`battery_status`变量与我们的范围进行比较。根据电池状态，我们将把我们的`api_action_interval_time`变量设置为`3000`、`5000`、`8000`或`false`。如果我们不支持 API，我们将它设置为默认值`3000`。
*   这是一个帮助器函数，用来决定我们更新可视计数器的速度，以毫秒为单位(这个元素告诉我们在下一次 Ajax 调用之前还有 X 秒)。我们将`api_action_counter_time`变量设置为 100(意味着它将每秒更新 10 次)。

### 直观显示电池信息

`display_status_information()`函数是我们需要向用户显示或更新电池信息时调用的。我们收集我们的变量——比如`battery_level`、`battery_support`等等——然后更新我们的信息。这个函数经常在各种电池事件发生时被调用(比如我们的电池电量变化时)。

![Battery status crucial information](img/3c3dab554bf8e479ebc734c16e77d405.png)

如果我们支持电池 API，我们还会显示我们的按钮来模拟电池状态(这样您就可以看到更新间隔变为不同的值)。

### 处理电池 API 事件

如果我们有对电池 API 的支持，我们也希望挂钩到[各种电池事件](https://developer.mozilla.org/en-US/docs/Web/API/BatteryManager)，以便我们可以在事情发生变化时做出反应。调用`battery_event_handlers()`函数并寻找两个特定的事件。

*   [`levelchange`事件](https://developer.mozilla.org/en-US/docs/Web/Events/levelchange):每当我们的设备增加或减少电量时都会调用这个事件。当我们改变这个状态时，我们再次调用`update_status_information()`函数，因为级别可能会改变设备现在所处的状态(这会影响 Ajax 被调用的频率等)。之后，我们用`display_status_information()`更新前端。
*   [`chargingchange`事件](https://developer.mozilla.org/en-US/docs/Web/Events/chargingchange):当设备停止或开始充电时调用。我们调用`get_battery_charging()`函数获取新状态，然后用`display_status_information()`更新前端。

### 创建主循环函数

这个序列的最后一步是调用`start_ajax_api_task()`函数。在这里，我们检查`battery_status`，看看它是否是关键的。如果是，我们将不会有任何 Ajax 调用(这只是手动更新)。

如果我们不在临界状态，我们就创建两个循环。第一个循环将被分配给`api_action_interval_item`变量，它将用于每 X 秒调用一次`replace_grid_image()`函数(其中时间是根据您的电池状态确定的)。第二个循环用作计数器，因此您可以直观地看到下一次更新将在何时发生。

### 调用 CatAPI 来替换图像

每隔 X 秒调用一次`replace_grid_image()`函数，这样我们就可以从 CatAPI 中提取一个新图像。

我们使用 [jQuery 的`$.ajax`](http://api.jquery.com/jquery.ajax/) 函数向我们的 PHP 文件发送一个请求，并从 CatAPI 返回一个图像。这是后来添加到我们的画廊。我们使用了一些简单的函数，这样一个随机的图像将被替换，它将很好地淡入(这只是为了演示)。

下面是 API 运行时的 GIF 表示:

![the API in action, dynamic images](img/bd9b1fbc393a6c650b65402a7c403cfe.png)

你可以修改这个例子，这样你就可以做别的事情了。然而，主要的一点是，您现在是根据电池状态有条件地执行这个函数。

## 把一切都包起来

现在，您已经看到了如何使用电池状态 API 中的附加信息，您可以定制每个用户的体验，以匹配他们设备的可用电量。

您可以做一些微妙的事情，比如在低电量时设置保守的默认设置，或者您可以选择完全改变用户的体验。

同样，你可以 **[查看 GitHub 库](https://github.com/sitepoint-editors/Battery-Status-API---the-CatAPI)** 来查看这个演示中使用的所有代码。

您还可以通过 **[查看一个工作演示](http://code.runnable.com/VkfyK7VTxa1Jq_si/using-the-battery-status-api-cat-api-for-php-curl-and-curl)** 来了解它的运行情况。(在各种设备上以各种电池电量进行测试，以比较结果！)

如果你已经使用了电池 API，或者你打算用它来构建一些令人兴奋的东西，请在评论中告诉我们！我很想知道你对它的看法。

## 分享这篇文章