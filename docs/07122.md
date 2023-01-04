# 用 jQuery Mobile 和 Cordova 构建一个货币转换器:3

> 原文：<https://www.sitepoint.com/build-a-currency-converter-with-jquery-mobile-and-cordova-part-3/>

在[系列的第二部分](https://www.sitepoint.com/build-a-currency-converter-with-jquery-mobile-and-cordova-part-2/ "Build a Currency Converter with jQuery Mobile and Cordova: Constructing The Interface")中，我向您展示了两个 HTML 页面的代码和我们的“货币转换器”应用程序的自定义 CSS 文件。在本文中，我将向您展示 jQuery Mobile 定制配置和一些实现业务逻辑的 JavaScript 文件。在描述 JavaScript 文件的同时，我还将详细演示 Cordova APIs。


### jQuery 移动自定义配置

jQuery Mobile 有一个默认配置，对于大多数有基本需求的项目来说，这个配置已经足够好了。但是，您可能仍然需要设置一些特定的自定义规则来满足您的应用程序的要求。正如我在[系列的第一部分](https://www.sitepoint.com/build-a-currency-converter-with-jquery-mobile-and-cordova/ "Build a Currency Converter with jQuery Mobile and Cordova: Introduction")中所说的，当我展示组成我们应用程序的文件列表时，我们有一个名为`jquery.mobile.config.js`的文件，这正是我们要指定配置的地方。在这一节中，我将向您展示我将更改哪些选项。

(请注意，当您创建这样的文件时，您必须在库文件本身的之前包含它**。事实上，当 jQuery Mobile 启动时，它会触发一个名为`mobileinit`的事件，该事件用于覆盖默认设置。)**

您有两种不同的方法来指定设置，但是我将使用改变`$.mobile`对象属性的方法。我将只修改其中的一些，但是如果你需要所有 jQuery Mobile 属性的概述，你可以阅读 [jQuery Mobile 配置文档](http://jquerymobile.com/demos/1.2.0/docs/api/globalconfig.html)。我们的文件将更改关于页面加载器小部件、主题和那些允许跨域请求的选项。允许跨域请求的设置是最有趣的，值得讨论。

正如您在应用程序要求中了解到的，“货币转换器”将调用欧洲中央银行的 RSS 提要来更新货币汇率，因此我们需要能够向外部源发出 HTTP 请求。当您将 Cordova 框架与 jQuery Mobile 结合使用时，为了能够进行跨域请求，您需要在的`config.xml`文件中为第一个框架(Cordova)设置一个属性，为第二个框架(jQuery Mobile)设置两个属性。

要更改的 jQuery Mobile 属性是`$.support.cors`和`$.mobile.allowCrossDomainPages`。第一个是布尔值，您需要将它设置为`true`，以告知浏览器支持跨域请求。该项目还链接到外部页面(在学分页面)，所以，我们还必须设置`$.mobile.allowCrossDomainPages`属性为`true`。事实上，当 jQuery Mobile 试图加载外部页面时，请求会通过`$.mobile.loadPage()`方法运行，如果引用的属性设置为`true`，则该方法只允许跨域请求。默认情况下，它的值是`false`，因为根据文档,<q cite="http://jquerymobile.com/demos/1.2.0/docs/pages/phonegap.html">框架在浏览器的位置散列中跟踪正在查看的页面，所以如果有问题的 XSS 代码可以操作散列并将其设置为自己选择的跨域 URL</q>，就有可能发生跨站点脚本(XSS)攻击。该选项必须在发出请求之前设置，文档建议将其放入初始配置中。我们会采取相应的行动。

现在您已经知道了这些选项的含义，您已经准备好查看`jquery.mobile.config.js`的完整源代码了。

```
$(document).on(
  'mobileinit',
  function()
  {
    // Page Loader Widget
    $.mobile.loader.prototype.options.text = 'Loading...';
    $.mobile.loader.prototype.options.textVisible = true;

    // Bypass Access-Control-Allow-Origin
    $.support.cors = true;
    $.mobile.allowCrossDomainPages = true;

    // Theme
    $.mobile.page.prototype.options.theme  = 'b';
    $.mobile.page.prototype.options.headerTheme = 'b';
    $.mobile.page.prototype.options.contentTheme = 'b';
    $.mobile.page.prototype.options.footerTheme = 'b';
    $.mobile.page.prototype.options.backBtnTheme = 'b';
  }
);
```

### 翻译课

回想一下项目的需求，你会记得“货币转换器”是一个多语言的应用程序。因为它最重要的部分是`index.html`，我将为该页面创建翻译，并忽略`aurelio.html`的翻译。我认为只有英语学分就足够了。

为了在`translation.js`文件中存储不同语言的字符串，我使用了一个调用变量`Translation`的对象。该对象具有与您想要支持的口语一样多的属性。因此，因为我们将把它翻译成意大利语、法语和西班牙语，我们将有三个属性，它们的值是包含字符串的对象。为了简化翻译过程，这些对象具有将被翻译为它们的属性名的元素的 id，以及将被改变为它们的值的字符串。要了解`Translation`是如何制作的，只看一种语言就足够了，所以我只给你看意大利语，但你会发现其他语言在应用程序库中也受支持。

```
var Translation = {
  'it': {
    'app-description': 'Currency Converter è una semplice applicazione che ti aiuta a convertire da una valuta ad un'altra. È anche possibile aggiornare i tassi di cambio ogni volta che si desidera così da avere sempre dei tassi di conversione aggiornati.',
    'convert-title': 'Converti',
    'from-value-label': 'Valore:',
    'from-type-label': 'Da Valuta:',
    'to-type-label': 'A Valuta:',
    'result-title': 'Risultato',
    'copyright-title': 'Creata da Aurelio De Rosa',
    'credits-button': 'Crediti',
    'update-button': 'Aggiorna dati',
    'submit-button': 'Converti',
    'reset-button': 'Azzera dati',
    'last-update-label': 'Ultimo aggiornamento cambi:'
  }
  // other languages here
}
```

### 用户设置

在本系列的介绍性文章中，我指出:

> 该应用程序将保存上次用于转换的货币，因此在下次启动该应用程序时，用户将找到他们上次使用的货币。此外，最后一次更新的日期和时间也将被存储，以便用户知道他的转换率是最近的(或旧的)。

因此，我们需要管理和存储这些数据。为了实现这个目标，我们将使用 [Cordova 存储 API](http://docs.phonegap.com/en/2.3.0/cordova_storage_storage.md.html#Storage) ，它构建在 [Web 存储 API](http://dev.w3.org/html5/webstorage/) 和 Cordova 之上。对于具有内置支持的设备，它使用本机实现而不是自己的实现，这与 W3C 规范兼容。

在这一节中，我将描述名为`settings.js`的文件，它包含名为`Settings`的类。让我们仔细看看这个文件。

正如您已经知道的，这个类有三个公共属性:`lastUpdate`、`fromCurrency`和`toCurrency`。它还有两个私人的:`_db`和`_tableName`。前三个属性的目的应该是不言自明的，所以我将跳过它们的解释。`_db`是对`window.localStorage`属性的引用，该属性将用于调用 Web 存储 API 的方法来存储和检索来自浏览器(或者，在我们的混合应用程序中，来自设备)的数据。请记住，这个 API 不像 DBMS 那样使用真正的表。相反，它有一组属性值是基本类型(如字符串或数字)或 JSON 编码的对象的属性。话虽如此，应该清楚的是`_tableName`并不是一个属性，它的值是一个包含实际表名的字符串，而是我们将存储包含用户设置的 JSON 编码对象的属性名。

下面的代码显示了到目前为止我所解释的内容。

```
function Settings(lastUpdate, fromCurrency, toCurrency)
{
  var _db = window.localStorage;
  var _tableName = 'settings';

  this.lastUpdate = lastUpdate;
  this.fromCurrency = fromCurrency;
  this.toCurrency = toCurrency;
}
```

现在，我们需要两个方法:一个用于存储数据，一个用于检索数据。在我的另一个惊人的创造性时刻，我分别将这些方法命名为`save()`和`load()`。这些方法除了包装对`getItem()`和`setItem()`方法的调用之外什么也不做，正如您通过查看下面的代码所看到的。

```
this.save = function()
{
  _db.setItem(_tableName, JSON.stringify(this));
}

this.load = function()
{
  return JSON.parse(_db.getItem(_tableName));
}
```

有些已经使用过 Web 存储 API 的人可能想知道为什么我使用了`setItem()`和`getItem()`而不是点符号。原因是 Windows Phone 7 不支持后者，所以为了确保所有操作系统的兼容性，我们必须使用前者。

这个类还有另一个方法要讨论，它更像是一个实用工具，而不是一个必须的方法。它叫做`getSettings()`，是一个静态方法。当您使用`load()`时，检索到的对象不是`Settings`类的一个实例，而是等价的`Object`的一个实例。现在，假设您想要基于您存储的设置更改当前用户的设置。在这种情况下，有一个`Settings`实例就容易多了，这样就可以修改属性，然后直接调用`save()`方法。为此，我创建了`getSettings()`，它调用`load()`，然后将结果转换为`Settings`实例。该实用程序的来源如下:

```
Settings.getSettings = function()
{
  var settings = new Settings().load();
  return (settings === null) ?
     {} :
     new Settings(
       settings.lastUpdate,
       settings.fromCurrency,
       settings.toCurrency
     );
}
```

### 结论

在本系列的第三篇文章中，我描述了“货币转换器”的定制配置以及允许跨域请求的选项的重要性。此外，我展示了用于翻译应用程序的类和用于保存用户设置的类。在本系列的下一部分中，我将说明另外两个 JavaScript 文件:`functions.js`(它具有检索汇率、更新货币选择框和初始化应用程序的功能)，以及`currency.js`，它是包含存储、加载、排序和转换检索到的货币的类的文件。

## 分享这篇文章