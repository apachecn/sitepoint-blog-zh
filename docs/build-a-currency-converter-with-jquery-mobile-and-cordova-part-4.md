# 用 jQuery Mobile 和 Cordova 构建货币转换器:4

> 原文：<https://www.sitepoint.com/build-a-currency-converter-with-jquery-mobile-and-cordova-part-4/>

在本系列的前一篇文章中，我描述了我们应用程序的 [jQuery Mobile](https://www.sitepoint.com/javascript/jquery/ "Articles about jQuery Mobile") 定制配置，并强调了允许跨域请求下载最新货币汇率的重要性。我还描述了一个类，它将帮助我们将应用程序翻译成多种语言，我还概述了第二个类来管理用户设置。在第四篇文章中，我将向您展示保存、加载和完成货币操作的类，以及统一应用程序所有不同组件的类。

### 货币类别

当我谈到`Settings`类时，我描述了`_db`和`_tableName`变量的目的。`Currency`类也有这些变量，并且有相同的目的。当然，`_tableName`的价值是不同的，就像“货币”一样请记住，我们存储的不是一个对象——我们存储的是一个包含`Currency`实例的数组。我们必须存储超过上次更新或上次使用的货币的数据；我们还需要保存货币的名称(实际上是它的缩写)和兑换率本身。

我们正在从欧洲中央银行检索汇率，他们使用欧元作为参考货币。因此，当您解析提要时，您不会发现欧元汇率本身(假设汇率为 1.00)，并且这些汇率都是相对于欧元的。这导致了一种进行非欧元货币比较的棘手方法，我将在描述`convert()`方法时详细解释这一点。

根据我到目前为止的陈述，`Currency`类的开头如下:

```
function Currency(abbreviation, rate)
{
  var _db = window.localStorage;
  var _tableName = 'currencies';

  this.abbreviation = abbreviation;
  this.rate = rate;
}
```

就像`Settings`类一样，我们将拥有`save()`和`load()`方法。后者与已经讨论过的相同，而前者则略有不同，因为我们管理的是一个数组。因为我们不想要重复的货币，所以在添加到数组之前，我们需要检查给定的货币是否已经存储。如果货币已经存在，我们只需更新兑换率。否则，我们将把整个对象推入数组。为了实现这个目标，我将创建一个名为`getIndex()`的静态方法，如果找到货币，它将返回货币的索引，否则返回`false`。就像您看到的`Settings`类一样，我将编写一个实用的静态方法来检索包含名为`getCurrencies()`的`Currency`实例的数组。

既然您已经意识到了这种棘手的情况，我可以向您展示一下`save()`和`load()`方法的代码。

```
this.save = function()
{
 var currencyIndex = Currency.getIndex(this.abbreviation);
 var currencies = Currency.getCurrencies();

 if (currencyIndex === false)
   currencies.push(this);
 else
   currencies[currencyIndex] = this;

 _db.setItem(_tableName, JSON.stringify(currencies));
}

this.load = function()
{
 return JSON.parse(_db.getItem(_tableName));
}
```

下面是两个静态方法的代码:

```
Currency.getCurrencies = function()
{
  var currencies = new Currency().load();
  return (currencies === null) ? [] : currencies;
}

Currency.getIndex = function(abbreviation)
{
  var currencies = Currency.getCurrencies();
  for(var i = 0; i < currencies.length; i++)
  {
    if (currencies[i].abbreviation.toUpperCase() === abbreviation.toUpperCase())
      return i;
  }

  return false;
}
```

既然我们能够保存和加载货币，我们需要一种方法来进行数学货币转换。在向您展示这些方法的代码之前，我应该强调一点。正如我所说的，提要使用欧元作为参考货币，所以您不会为每种货币存储不同的兑换率——这是对内存的浪费。我们要设计的是一种方法，将欧元转换成每种所需货币类型的其他货币。

现在，假设你想从美元转换成澳元，你能做什么？解决办法是你先转换成欧元，然后再转换成你真正需要的货币。

要实现这个目标，你需要先除以起始汇率，然后乘以目标汇率。`convert()`方法需要三个参数:要转换的值、起始货币和目标货币。这个方法依赖于另一个叫做`getRate()`的方法，它使用`abbreviation`(货币名称的缩写)来检索适当的兑换率。`getRate()`依靠一种我还没有解释过的叫做`getCurrency()`的方法。正如您可能想象的那样，它与`getCurrencies()`非常相似，除了它接受一个缩写作为参数，如果搜索成功则返回一个`Currency`实例，如果搜索失败则返回`null`。

下面列出了这三种方法的源代码:

```
Currency.getCurrency = function(abbreviation)
{
  var index = Currency.getIndex(abbreviation);
  return (index === false) ? null : Currency.getCurrencies()[index];
}

Currency.getRate = function(abbreviation)
{
  var currency = Currency.getCurrency(abbreviation);
  return (currency === null) ? 0 : currency.rate;
}

Currency.convert = function(value, from, to)
{
  // Round up to the 2nd decimal
  return Math.round(value / Currency.getRate(from) * Currency.getRate(to) * 100) / 100;
}
```

最后两种方法是在更新选择框以选择货币并按字母顺序排序时使用的方法。他们的名字是`compareTo()`和`compare()`，他们的代码如下:

```
Currency.prototype.compareTo = function(other)
{
  return Currency.compare(this, other);
}

Currency.compare = function(currency, other)
{
  if (other == null)
    return 1;
  else if (currency == null)
    return -1;

  return currency.abbreviation.localeCompare(other.abbreviation);
}
```

### 效用函数

在这一节中，我将解释包含在`functions.js`文件中的实用函数。请注意，我将避免解释`updateIcons()`，因为它已经在以前的系列中讨论和提供了。因此，如果你想了解它的功能，你可以阅读“根据屏幕大小更新图标”一节，这一节来自[用 HTML5 和 Javascript 构建基于位置的移动应用:第 4 部分](https://www.sitepoint.com/build-a-location-based-mobile-app-with-html5-and-javascript-part-4/ "Build a Location-Based Mobile App With HTML5 and Javascript: Part 4")。

#### 需求测试

“货币转换器”没有很多要求。事实上，我们需要的唯一强制条件是使用 Web 存储 API 存储数据的可能性。虽然连接到互联网的能力很重要，但只有在用户第一次运行应用程序时才需要，因为他需要从欧洲央行的 RSS 提要中下载最新的利率。但是，在此之后，用户可以使用稍微过时的货币兑换率继续离线工作。显然，如果没有互联网连接，我们将无法更新价格。

为了测试需求，我将创建一个名为`checkRequirements()`的函数，它将测试 Web 存储 API 支持。如果设备不支持它，应用程序使用 Cordova 通知 API 通知用户并返回`false`，因此更新货币转换的按钮可以被禁用。这个函数的代码如下所示。

```
function checkRequirements()
{
  if (typeof window.localStorage === 'undefined')
  {
    console.log('The database is not supported.');
    navigator.notification.alert(
      'Your device does not support the database used by this app.',
      function(){},
      'Error'
    );
    return false;
  }

  return true;
}
```

如你所见，我还写了一个`console.log()`指令来帮助你调试项目，以防你需要。记住，使用`alert()`不是最佳实践。

#### 翻译页面

为了翻译`index.html`的元素，我将使用 Cordova 2.2.0 中引入的[全球化 API](http://docs.phonegap.com/en/2.3.0/cordova_globalization_globalization.md.html#Globalization "Globalization API documentation") 。，它允许您获取有关用户的地区和时区的信息，这要特别感谢像`getPreferredLanguage()`(获取客户端当前的口语)和`getLocaleName()`(检索客户端当前地区设置的标识符代码，例如 en_US 或 it_IT)这样的方法。

此外，这个 API 还提供了一些方法来执行一些操作，比如根据用户的设置，使用适当的本地格式将日期和数字转换成字符串。实现这些转换的方法分别是`dateToString()`和`numberToString()`。请注意，像其他 Cordova APIs 一样，这些方法是异步的，所以它们使用成功和失败回调函数。

在我们的项目中，我们将使用一些之前引用的方法，具体来说:`getLocaleName()`、`dateToString()`和`numberToString()`。这个 API 非常重要，因为与以前的版本相比，它允许您将用户体验向前推进一步。请注意，目前支持的平台是 Android、BlackBerry WebWorks (OS 5.0+)、iOS 和 Windows Phone 8。

正如您将在下面的代码中看到的，我使用了`getLocaleName()`方法来检索语言标识符，我将用它从`Translation`对象中获取正确的语言。一旦检索到，我将遍历文本字符串来翻译各种指令元素。现在，您应该终于明白为什么我使用元素的 id 作为属性名了。您还会注意到使用 Audero Text Changer 来更改元素的文本，我在本系列的第一部分中对此进行了说明。

```
/**
 * Translate the main page
 */
function translateMainPage()
{
  navigator.globalization.getLocaleName(
    function(locale)
    {
      var translation = Translation[locale.value.substring(0, 2)];
      if (typeof translation === 'undefined')
        return;

      for(var key in translation)
        $('#' + key).auderoTextChanger(translation[key]);
    },
    function()
    {
      console.log('An error has occurred with the translation');
    }
  );
}
```

### 结论

在第四篇文章中，我描述了`Currency`类及其所有方法。正如您所了解的，它有几个实用方法，允许您用很少的代码行编写新方法。我还展示了如何测试 app 的最低需求，并简要概述了全球化 API，以及我们项目中使用的具体方法。为了帮助我们管理界面元素的翻译，我使用了 [Audero 文本转换器](https://www.sitepoint.com/how-to-change-texts-in-your-jquery-mobile-apps-without-breaking-widgets/ "Customize Your jQuery Mobile Interface with Specialized Plugins")插件。在本系列的下一部分，我将向您展示`functions.js`文件的其余函数，以结束讨论。

## 分享这篇文章