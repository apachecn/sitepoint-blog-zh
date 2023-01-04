# 用 jQuery Mobile 和 Cordova 构建一个货币转换器:1

> 原文：<https://www.sitepoint.com/build-a-currency-converter-with-jquery-mobile-and-cordova/>

今年早些时候，我发表了一系列关于如何用 HTML5 和 Javascript 构建基于位置的移动应用的文章。如果您喜欢这个系列，您可以关注这个新系列，它将教您如何使用您的移动开发工具中已经拥有的一些技术来构建货币转换器。与之前的项目一样，这将是一个由 HTML、CSS 和 JavaScript 构建的混合应用程序，支持几个框架，如 jQuery、jQuery Mobile 和 Cordova(也称为 PhoneGap)。在一个非常有想象力的灵感时刻，我给这个应用程序起了一个非常有创意的名字“货币转换器”

对于那些没有准确理解什么是*混合应用*的人来说，它是使用 web 技术构建的<q cite="https://www.sitepoint.com/native-hybrid-or-web-apps/">，然后包装在特定于平台的外壳中，允许它像本地应用一样安装。</q>要深入了解这一主题以及本地、混合和 web 应用之间的差异，请查看[本地、混合或 Web 应用？](https://www.sitepoint.com/native-hybrid-or-web-apps/)。

本系列的目标不是用不同种类的应用程序复制上一个。虽然我们将讨论 Web 存储和通知 API，但是本系列将介绍两个新的 Cordova API:Globalization 和 InAppBrowser API。第一个已经在发行版 2.2.0 中引入，而第二个包含在最近的稳定版本(2.3.0)中。有关 2.2.0 版本的更多信息，请阅读[PhoneGap 2.2 中的新功能？](https://www.sitepoint.com/whats-new-in-phonegap-2-2/ "What’s New in PhoneGap 2.2?")

在这第一篇介绍性文章中，我将解释应用程序的特性、使用的技术及其结构。

### 应用程序要求

我说过，这个 app 很小，很好理解。我们正在开发一个货币转换器，允许开发人员根据欧洲中央银行( <abbr title="European Central Bank">ECB</abbr> )货币 RSS 提要更新所需的兑换率。最重要的特性之一是，一旦检索到货币兑换率，就使用 Web 存储 API 保存，因此用户可以离线使用该应用程序。此外，一旦用户启动“货币转换器”，应用程序将检查互联网连接，如果不可用，应用程序将搜索先前存储的汇率。

基于 <abbr title="European Central Bank">ECB</abbr> feed，我想强调的是，它将只支持 feed 提供的货币，不支持其他货币。为了增强用户体验，该应用程序将保存上次用于兑换的货币，因此在下次启动该应用程序时，用户将找到他们上次使用的货币。此外，最后一次更新的日期和时间也将被存储，以便用户知道他的转换率是最近的(或旧的)。

默认情况下，文本将以英语显示，但它将被开发为支持其他语言(这在开发国际货币应用程序时显然很重要)。如果移动设备使用“货币转换器”支持的语言，应用程序将在初始化时自动翻译。除了英语，最终版本还将支持意大利语、法语和西班牙语。对不起，但是我不能翻译成其他语言。然而，一旦我发布了这个库，你就可以自由地使用其他语言贡献额外的翻译了。

### 所涉及的技术

在简介中，我描述了将使用哪些技术来创建代码，但是下面的列表将让您更详细地了解我们将使用哪些技术:

*   **HTML5** :用于创建页面的标记。
*   CSS :大部分 CSS 改进将由 jQuery Mobile 完成，所以我将只使用几行定制的 CSS 代码。
*   **JavaScript** :这是用来写业务逻辑的语言，所以这真的是理解这个系列的必备技能。
*   jQuery :主要用于选择元素和附加事件处理程序。
*   **jQuery Mobile** :该框架用于自动增强应用程序界面。
*   **Cordova(PhoneGap)**:Cordova 将用于包装文件，因此您可以编译它们并安装结果，就像一个原生应用程序一样。

因为 jQuery Mobile 的 1.3.0 版本还不稳定(然而，在撰写本文时，它还处于“发布候选”状态)，所以我将使用 1.2.0 版本。如果您需要了解这些版本之间的区别，我在文章[中详细描述了它们，jQuery Mobile 1.2.0 的新特性是什么？](https://www.sitepoint.com/whats-new-in-jquery-mobile-1-2-0/ "What’s New in jQuery Mobile 1.2.0?")和[使用 jQuery Mobile](https://www.sitepoint.com/working-with-jquery-mobile-1-2-0/ "Build Lists and Popups in Minutes Using jQuery Mobile") 在几分钟内建立列表和弹出窗口。使用的 Cordova 版本将是 2.3.0 版，以使用以下新 API:

*   **全球化 API** :它为<q cite="http://docs.phonegap.com/en/2.3.0/cordova_globalization_globalization.md.html#Globalization">提供了一个对象来获取信息并执行特定于用户的地区和时区的操作。</q>例如，它提供了`getPreferredLanguage()`方法来获取客户端当前语言的字符串标识符，还提供了`numberToString()`方法来根据客户端的用户偏好返回一个字符串格式的数字。全球化官方文档中有关于这个 API 的更多信息。
*   **InAppBrowser API** :这是<q cite="http://docs.phonegap.com/en/2.3.0/cordova_inappbrowser_inappbrowser.md.html#InAppBrowser">一个网络浏览器，当你使用`window.open`调用</q>时，它会显示在你的应用程序中。你可以通过阅读 [InAppBrowser 官方文档](http://docs.phonegap.com/en/2.3.0/cordova_inappbrowser_inappbrowser.md.html#InAppBrowser)找到更多关于这个 API 的信息。
*   **网络存储 API** :提供对设备存储选项的访问。如果你想要更深入的解释，请阅读[Web 存储 API 概述](https://www.sitepoint.com/an-overview-of-the-web-storage-api/ "An Overview of the Web Storage API")。

就像前面的系列一样，“货币转换器”的开发假设编译将使用 Adobe 云服务完成，称为 [Adobe PhoneGap Build](http://build.phonegap.com "Adobe PhoneGap Build") 。服务本身将在编译时为每个平台包含正确的 Cordova 库。要发现使用这项技术背后的原因，请看一下本系列第一部分的[中的讨论。到最终完成的源代码的链接将会在这个系列的最后一部分给出，并存放在](https://www.sitepoint.com/build-a-location-based-mobile-app-with-html5-and-javascript-part-1/ "Build a Location-Based Mobile App With HTML5 and Javascript: Part 1") [BitBucket repository](https://bitbucket.org/AurelioDeRosa "Aurelio De Rosa's repository") 上。

### 项目的结构

要启动该项目，创建一个文件夹，并将其重命名为“货币转换器”。现在。在这个主文件夹中创建三个子文件夹:css、images 和 js。现在下载 jQuery(我将使用 1.8.3 版本)和 jQuery 移动库。将 jQuery 和 jQuery Mobile 的 JavaScript 文件放在“js”文件夹中，然后将 jQuery Mobile CSS 文件放在“CSS”文件夹中。最后，将 jQuery Mobile 捆绑图像放入“images”文件夹。

在项目文件夹的根目录下，我将创建 HTML 文件、 [Adobe PhoneGap 构建配置文件](https://build.phonegap.com/docs/config-xml "Adobe PhoneGap Build configuration file")、默认闪屏和应用程序图标。除了我们已经准备好的文件之外，我们将编写以下内容:

*   **index.html**:这是“货币转换器”的入口，也是用户将与之交互的唯一页面。作为入口点，我将链接到 index.html 文件的`<head>`部分中使用的库。它将有更新转换率的按钮，以及访问信用页面的按钮。
*   aurelio.html:这是演职员表页面，所以没什么特别的。
*   **style.css** :包含应用程序使用的 css 的几个自定义行的文件。
*   **jquery.mobile.config.js** :包含 jQuery Mobile 框架的自定义配置。
*   **currency.js** :这个文件表示一个货币对象，并且将拥有使用 Web 存储 API 检索、保存和加载货币的方法。此外，它将有一个用于排序的比较方法。
*   **functions.js** :这将包含初始化项目、翻译`index.html`页面、更新转换率的函数，以及其他一些实用函数，我将在本系列的后续文章中进一步解释。
*   **settings.js** :该文件将用于保存和加载前面提到的 app 设置，包括上次使用的货币和上次货币兑换率更新的时间。
*   **translation.js** :这将包含具有翻译`index.html`页面的字符串的对象。
*   **config.xml** :这个 xml 文件将包含应用程序的元数据，Adobe cloud service 将使用它来存储应用程序版本号和包内容等设置。

除了这个列表，“货币转换器”将有一些额外的图像存储在“图像”文件夹中，以及我开发的一个名为 Audero 文本转换器的插件。这是一个非常轻量级的 jQuery Mobile 插件，允许您轻松地更改页面上元素的文本，而不会破坏框架所做的增强。所以，正如你可能猜到的，我将使用这个插件将应用程序翻译成各种语言。如果你需要更多关于这个插件的信息，我在[用专门的插件定制你的 jQuery 移动接口](https://www.sitepoint.com/how-to-change-texts-in-your-jquery-mobile-apps-without-breaking-widgets/ "Customize Your jQuery Mobile Interface with Specialized Plugins")中已经写了。

### 结论

在第一部分中，我说明了“货币转换器”的特性和结构我还向您展示了实现它所涉及的技术。现在，您已经知道需要做些什么来完成本系列的其余部分，请花时间研究这些技术，以便为本系列的下一部分做好准备，在下一部分中，我将深入研究代码并描述如何创建 HTML 页面。

## 分享这篇文章