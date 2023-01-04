# 用 jQuery Mobile 和 Cordova 构建货币转换器:5

> 原文：<https://www.sitepoint.com/build-a-currency-converter-with-jquery-mobile-and-cordova-rate-updates/>

在本系列的前一篇文章中，我描述了在我们的应用程序中执行货币兑换所需的类和函数。我还举例说明了 [Cordova 全球化 API](http://docs.phonegap.com/en/2.3.0/cordova_globalization_globalization.md.html#Globalization) ，以及我们如何使用它来翻译“货币转换器”主页上的书面说明在下一期文章中，我将向您展示`functions.js`文件的其余函数，并简要概述 Cordova [连接](http://docs.phonegap.com/en/2.3.0/cordova_connection_connection.md.html#Connection "Cordova Connection API")和[InAppBrowser](http://docs.phonegap.com/en/2.3.0/cordova_inappbrowser_inappbrowser.md.html#InAppBrowser "Cordova InAppBrowser API")API。

### 效用函数

#### 更新“上次更新”标签

在本系列的介绍性文章中，我解释了我们的项目将存储下载的货币汇率，以便用户可以离线使用该应用程序。因为货币汇率每天都在变化，所以通知用户他的汇率有多旧是很重要的。如果他觉得它们太旧，他可以运行更新并下载最新的。因此，主页包含以下代码:

```
<label id="last-update-label">Last update of exchange rates:</label>
<span id="last-update"></span>
```

有`id="last-update"`的`<span>`是我们注入最后一次更新日期的地方。这个日期将被写成最适合由全球化 API 决定的用户偏好的格式，我在上一篇文章的[中解释过。回想一下第三篇文章](https://www.sitepoint.com/build-a-currency-converter-with-jquery-mobile-and-cordova-part-4/ "Build a Currency Converter with jQuery Mobile and Cordova: Translation and Conversion Logic")中我们谈到的用户设置，您应该记得，每次用户通过`Settings`类及其方法请求汇率更新时，我们都会更新并保存日期。

更新上述`<span>`标签文本的函数是`updateLastUpdate(),`，其标记如下:

```
function updateLastUpdate()
{
  if (typeof Settings.getSettings().lastUpdate === 'undefined')
  {
    $('#last-update').text('-');
    return;
  }

  // Show the last time the rates have been updated
  navigator.globalization.dateToString(
    new Date(Settings.getSettings().lastUpdate),
    function (date)
    {
      $('#last-update').text(date.value);
    },
    function ()
    {
      $('#last-update').text('-');
    }
  );
}
```

#### 更新费率

处理实际更新的方法是`updateExchangeRates()`。在该方法的最开始，有一个测试来验证该设备是否连接到互联网。测试是使用 Cordova 连接 API 完成的，这是一个可以访问设备的蜂窝和 wifi 连接信息的对象。它有一个名为`type`的属性，用于检查正在使用的活动网络连接，并可以采用以下值(作为常量):

*   联系。未知的
*   联系。以太网
*   联系。无线局域网（wireless fidelity 的缩写）
*   联系。单元格 _2G
*   联系。CELL_3G
*   联系。CELL_4G
*   联系。没有人

如果设备有活动连接，该函数调用欧洲中央银行 RSS 提要，使用 jQuery `ajax()`方法检索利率。检索后，汇率使用`Currency`类存储，用户设置的`lastUpdate`数据也会更新，因此用户知道他们使用的是最新的货币汇率。

主页的两个选择框和“上次更新”标签使用当前数据进行更新。然后，使用一个名为`fillCurrenciesSelection()`的函数填充两个选择菜单，稍后我将描述这个函数。最后要注意的是，如果速率更新失败，用户会收到使用 Cordova 通知 API 显示的失败警告。

那么，如果第一次测试失败，到底会发生什么？嗯，如果设备没有活跃的互联网连接，应用程序将检查是否有任何存储的费率。如果没有存储的货币汇率，用户会被通知这个问题，并且“Convert”按钮被禁用，因为没有任何方法来运行任何货币转换。

实现此功能的代码如下所示:

```
/**
 * Update the exchange rates using the ECB web service
 */
function updateExchangeRates()
{
  if (navigator.network.connection.type !== Connection.NONE)
  {
    $.mobile.loading(
      'show',
      {
        text: 'Updating rates...',
        textVisible: true
      }
    );

    $.get(
      'http://www.ecb.int/stats/eurofxref/eurofxref-daily.xml',
      null,
      function(data)
      {
        var $currenciesElements = $(data).find('Cube[currency]');
        // The EURO is the default currency, so it isn't in the retrieved data
        var currencies = [new Currency('EUR', '1')];

        var i;
        for(i = 0; i < $currenciesElements.length; i++)
        {
          currencies.push(
            new Currency(
              $($currenciesElements[i]).attr('currency'),
              $($currenciesElements[i]).attr('rate')
            )
          );
        }

        currencies.sort(Currency.compare);
        // Store the data
        for(i = 0; i < currencies.length; i++)
          currencies[i].save();

        // Update settings
        var settings = Settings.getSettings();
        if ($.isEmptyObject(settings))
          settings = new Settings();
        settings.lastUpdate = new Date();
        settings.save();

        fillCurrenciesSelection();
        updateLastUpdate();
        $('#submit-button').button('enable');
      },
      'XML'
    )
    .error(function() {
      console.log('Unable to retrieve exchange rates from the provider.');
      navigator.notification.alert(
        'Unable to retrieve exchange rates from the provider.',
        function(){},
        'Error'
      );
      if (Currency.getCurrencies().length === 0)
        $('#submit-button').button('disable');
    })
    .complete(function() {
      $.mobile.loading('hide');
    });
  }
  // Check if there are data into the local storage
  else if (Currency.getCurrencies().length === 0)
  {
    console.log('The connection is off and there aren't rates previously stored.');
    navigator.notification.alert(
      'Your device has the connection disabled and there aren't rates previously stored.n' +
      'Please turn on your connection.',
      function(){},
      'Error'
    );
    $('#submit-button').button('disable');
  }
}
```

#### 填充选择框

更新选择框的功能并不难掌握。它只是使用`Currency`类的`getCurrencies()`方法检索存储的货币，然后使用 jQuery `append()`方法插入每一种货币。值得注意的是，在插入所有货币后，该函数会搜索保存在用户首选项中的最后一个源货币和目的货币。如果找到了这些最后使用的货币，则会自动选择它们。在这个场景中，listview 小部件需要被刷新，这是通过使用方法`selectmenu()`并将字符串`refresh`传递给它来完成的。

`fillCurrenciesSelection()`的完整来源如下:

```
/**
 * Use the stored currencies to update the selection lists
 */
function fillCurrenciesSelection()
{
  var currencies = Currency.getCurrencies();
  var $fromCurrencyType = $('#from-type');
  var $toCurrencyType = $('#to-type');

  // Empty elements
  $fromCurrencyType.empty();
  $toCurrencyType.empty();

  // Load all the stored currencies
  for(var i = 0; i < currencies.length; i++)
  {
    $fromCurrencyType.append('
' +      currencies[i].abbreviation + '
');
    $toCurrencyType.append('
' +      currencies[i].abbreviation + '
');
  }

  // Update the selected option using the last currencies used
  var settings = Settings.getSettings();
  if (!$.isEmptyObject(settings))
  {
    var currency = $fromCurrencyType.find('[value="' + settings.fromCurrency + '"]');
    if (currency !== null)
      $(currency).attr('selected', 'selected');

    currency = $toCurrencyType.find('[value="' + settings.toCurrency + '"]');
    if (currency !== null)
      $(currency).attr('selected', 'selected');
  }

  $fromCurrencyType.selectmenu('refresh');
  $toCurrencyType.selectmenu('refresh');
}
```

#### 管理外部链接

正如你在[中看到的“货币转换器”的界面设计](https://www.sitepoint.com/build-a-currency-converter-with-jquery-mobile-and-cordova-part-2/ "Build a Currency Converter with jQuery Mobile and Cordova: Constructing The Interface"),`aurelio.html`文件中有几个外部链接。这些链接不是很有用，但是我需要解释一个重要的概念。

直到几个版本之前，Cordova 都在运行应用程序的同一个 Cordova WebView 中打开外部链接。因此，一旦它打开了一个链接，当用户点击“后退”按钮时，最后一个项目的页面就会完全按照用户离开之前的样子显示。但是，在框架的最新版本中，这种行为被改变了；现在，如果 URL 在你的应用程序的白名单中，外部链接默认使用 Cordova WebView 打开。不在您的白名单上的 URL 使用 InAppBrowser API 打开。

引用官方文档，<q cite="http://docs.phonegap.com/en/2.3.0/cordova_inappbrowser_inappbrowser.md.html#InAppBrowser">InAppBrowser 是一个网页浏览器，当你使用 window.open 调用时，它会显示在你的应用程序中。</q>这个 API 有三个方法:`addEventListener()`、`removeEventListener()`和`close()`。第一个允许您监听三个事件(`loadstart`、`loadstop`和`exit)`)，并允许您附加一个一旦这些事件被触发就运行的函数。正如您可能猜到的，第二种方法用于删除以前附加的侦听器。最后，使用`close()`方法关闭 InAppBrowser 窗口。

如果用户打开 InAppBrowser 或系统浏览器中显示的链接，然后“返回”到应用程序，所有 CSS 和 jQuery Mobile 增强功能都将丢失。发生这种情况是因为外部链接没有引用文件，当用户回到应用程序时；文件在第一次被请求时被加载，但不通过主页。显然，这是您想要避免的事情，这突出了 Cordova 配置文件中白名单的重要性，下一个函数出现在帮助中。

`openLinksInApp()`函数的目的是捕捉所有外部链接的点击(通过使用`target="_blank"`属性识别)，防止不必要的默认行为，并使用`window.open()`方法打开它们。正如您将在下面的代码中看到的，我将使用`_target`参数，这样链接将使用 WebView 打开，因为我将把这些 URL 放在 Cordova 白名单中。使用这种技术，我们将避免上述问题，您的应用程序将继续按照预期的方式运行。

```
/**
 * Open all the links as internals
 */
function openLinksInApp()
{
   $("a[target="_blank"]").on('click', function(event) {
      event.preventDefault();
      window.open($(this).attr('href'), '_target');
   });
}
```

### 结论

在本文中，我描述了`functions.js`文件的其他几个函数，强调了每个函数的要点。我还解释了 Cordova 连接和 InAppBrowser API 以及它们是如何工作的。正如我们所见，当您使用 jQuery Mobile 构建移动应用程序界面时，处理外部链接是非常重要的。在本系列的下一篇也是最后一篇文章中，我将向您展示初始化应用程序的函数，名为`initApplication()`，以及我们将与 [Adobe PhoneGap Build](http://build.phonegap.com/) 服务一起使用的 Cordova 配置文件，以打包我们的“货币转换器”应用程序。

## 分享这篇文章