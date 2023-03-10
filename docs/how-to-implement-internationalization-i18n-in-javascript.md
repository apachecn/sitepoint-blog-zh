# 如何在 JavaScript 中实现国际化(i18n)

> 原文：<https://www.sitepoint.com/how-to-implement-internationalization-i18n-in-javascript/>

*这篇文章由[朱利安·莫茨](https://www.sitepoint.com/author/jmotz/)、[帕纳伊奥蒂斯·韦利萨拉科斯](https://www.sitepoint.com/author/pvelisarakos/)、[维尔丹·索蒂奇](https://www.sitepoint.com/author/vildansoftic/)和[蒂姆·塞韦里恩](https://www.sitepoint.com/author/tseverien/)进行了同行评议。感谢 SitePoint 的所有同行评审员使 SitePoint 的内容尽可能做到最好！*

![Cartoon flying saucer approaching a planet](img/37c954a605df154752bac1a39bf4b1b9.png)

所以，你是一个开发下一个大项目的开发人员。你的客户喜欢你的产品，它在所有的社交网络上好评如潮。更好的是，该公司的首席执行官刚刚获得了 1000 万美元的资金，用于拓展新市场。但是在走向全球之前，产品必须更新以支持不同的语言、货币、日期格式等等。你猜怎么着？你和你的团队是实现这一目标的负责人。用技术术语来说，你的软件必须国际化，然后本地化。

国际化(也称为 T2)是创造或改造产品和服务的过程，以便它们可以很容易地适应特定的当地语言和文化。**本地化**(也称 *L10n* )是将国际化软件改编为特定地区或语言的过程。换句话说，国际化是调整您的软件以支持多种文化(货币格式、日期格式等)的过程，而本地化是实现一种或多种文化的过程。

这两个过程通常被在不同国家有兴趣的公司所采用，然而它们也可能对在他们自己的站点上工作的单个开发者有用。例如，你可能知道，我是意大利人，我拥有一个网站。我的网站目前是英文的，但我可能会决定将其国际化，然后将其本地化为意大利语。这对那些以意大利语为母语并且不太习惯英语的人来说是有益的。

在本文中，我将向您介绍由 jQuery 团队的一些成员开发的国际化和本地化 JavaScript 库 [Globalize](https://github.com/jquery/globalize) 。本文中展示的所有片段都可以在我们的 GitHub 资源库中找到[。](https://github.com/sitepoint-editors/introducing-globalize)

但是在深入研究全球化之前，我想用这个项目的领导者 Rafael Xavier de Souza 的话来结束这个简短的介绍。

> 开发者认为 i18n 是关于非英语语言的翻译。只有在将当前应用扩展到多个国家或市场时才需要 i18n。我总是试图解释 i18n 一般是关于“说话”的。每个应用程序在某个时候都必须与用户“交谈”。为了与用户交流，应用程序可能需要复数支持、性别变化、日期格式、数字格式和货币格式。即使是在英语中，要正确地做到这一点也可能很棘手。

## 什么是全球化？

[Globalize](https://github.com/jquery/globalize) 是<q cite="https://github.com/jquery/globalize">一个用于国际化和本地化的 JavaScript 库，利用了官方的 Unicode CLDR JSON 数据。</q>这个库是开源的，由 [Rafael Xavier de Souza](https://github.com/rxaviers) 和 jQuery 团队的一些成员一起开发。

Globalize 基于[Unicode Consortium 的公共区域数据存储库(CLDR)](http://cldr.unicode.org/) ，这是可用的最大和最广泛的区域数据标准存储库。因此，与嵌入地区数据的库不同，如果您使用 Globalize，那么总是使用最新的 CLDR 数据是非常容易的。

该库既可用于浏览器，也可用作 Node.js 模块。Globalize 1.0 支持所有主流浏览器，包括 IE9+、Chrome、Firefox、Safari 5.1+和 Opera 12.1+。

该图书馆的主要特点是:

*   数字格式化和解析
*   日期和时间格式和解析
*   相对时间格式
*   货币格式
*   消息格式
*   多元支持
*   单位支持

我最喜欢 Globalize 的一点是它的每个特性都有一个模块。开发人员可能不需要整个库，因此可以挑选所需的模块。另一个有趣的特性是，与其他库不同，它通过不在库中托管或嵌入任何语言环境数据来保持代码与内容的分离。

但是全球化并不是城里唯一的展览。如果你对一些替代品感兴趣，[拉斐尔有一个专门的页面](http://rxaviers.github.io/javascript-globalization/)。最引人注目的选择是 [i18next](http://i18next.com/) 。

## 全球化和 JavaScript 国际化 API

对于你们中的一些人来说，这可能会感到惊讶，但是 JavaScript 以[国际化 API](http://www.ecma-international.org/ecma-402/1.0/) (也称为 ECMA-402)的形式提供了对国际化的本地支持。`Intl`对象是在`window`对象上可用的对象，充当国际化 API 的名称空间。这个 API 目前提供了格式化数字和日期的方法，以及比较特定语言中的字符串的方法。

既然您知道国际化 API 的存在，您可能会认为 Globalize 在幕后使用它。这种方法肯定会带来更好的日期和数字格式化性能。然而，因为[支持度低并且在浏览器](http://rxaviers.github.io/globalize-modern-apps/#/0/17)中非常不一致，所以库没有使用它。

在我们继续讨论全球化之前，我想让您体验一下国际化 API。

### 格式化日期

我要展示的第一个例子使用国际化 API 来格式化几个地区的日期:IT、US 和 GB。

```
// 30th of June 2016
var date = new Date(2016, 5, 30);

// "30/6/2016"
console.log(new Intl.DateTimeFormat('it-IT').format(date));

// "6/30/2016"
console.log(new Intl.DateTimeFormat('en-US').format(date));

// "30/06/2016"
console.log(new Intl.DateTimeFormat('en-GB').format(date)); 
```

在这个例子中，我使用了`DateTimeFormat`构造函数来创建一个新的日期格式化程序，它使用了指定的语言环境(“it-IT”、“en-US”和“en-GB”)。然后，我调用`format`方法来格式化日期对象。

[上面的代码也可以作为 JSBin](https://jsbin.com/kiyibac/edit?js,console) 获得。

### 格式化数字

如前所述，API 还允许您格式化数字。下面是一个使用`NumberFormat`构造函数的例子:

```
var number = 1302.93;

// "1.302,93"
console.log(new Intl.NumberFormat('it-IT').format(number));

// "1,302.93"
console.log(new Intl.NumberFormat('us-US').format(number));

// "1,302.93"
console.log(new Intl.NumberFormat('en-GB').format(number)); 
```

通过查看第二个代码片段的输出，也是作为 JSBin 提供的[，您可以注意到，在意大利，与美国和英国相比，我们对数字的格式化有所不同。](https://jsbin.com/magopu/edit?js,console)

正如我前面提到的，对这个 API 的支持很低，但是如果你想使用它，你可以在你的应用程序中使用这个聚合填充。

既然我已经让你对国际化和本地化如何工作有了更好的了解，让我们来讨论全球化。

## 安装和使用 Globalize

Globalize 可以通过 npm 轻松安装:

```
npm install globalize cldr-data --save 
```

该命令还会安装 CLDR 数据，这是加载 Globalize 将使用的语言环境数据(例如，数字或日期如何在特定语言中格式化)所必需的。安装了这两个包之后，我们就可以使用这个库了。

**注**:以下例子假设 Node。如果你对在浏览器中使用 Globalize 感兴趣，我推荐从项目主页上的[例子开始。](https://github.com/jquery/globalize#examples) [webpack 示例](https://github.com/jquery/globalize/tree/master/examples/app-npm-webpack)使快速启动和运行变得特别容易。

接下来，我将使用 Globalize 重写上一节中列出的两个代码片段。

### 格式化日期

第一个示例可以如下所示实现:

```
// Include the Globalize library
var Globalize = require('globalize');

// Include the CLDR data
var cldrData = require('cldr-data');

// Loads the supplemental data
Globalize.load(cldrData.entireSupplemental());

// Loads the data of the specified locales
Globalize.load(cldrData.entireMainFor('it', 'en', 'en-GB'));

// 30th of June 2016
var date = new Date(2016, 5, 30);

// "30/6/2016"
console.log(Globalize('it').formatDate(date));

// "6/30/2016"
console.log(Globalize('en').formatDate(date));

// "30/06/2016"
console.log(Globalize('en-GB').formatDate(date)); 
```

尽管很简单，上面的代码允许我涵盖几个主题。第一次玩 Globalize 时，我感到有点奇怪的第一件事是，CLDR 数据使用的一些语言代码只使用了两个字母。为了保持一致，我希望所有的地区都要求完整版本的 [ISO 3166 标准](http://www.iso.org/iso/country_codes)(例如“it-it”和“en-US”)而不是简短版本(例如“IT”和“en”)。虽然将意大利语作为缺省值似乎是合理的(毕竟意大利语起源于意大利)，但对于英语来说却是令人困惑的。事实上，“en”代表的是美式英语，而不是英式英语。如果你想确保不犯和我一样的错误，我建议你看一看[这张表](https://github.com/jquery/globalize#locales)。

另一个值得概述的概念是`entireSupplemental`方法(代码的第三条语句)。这将加载包含某个国家或其地区数据的补充信息的所有文件。例如，电话国家代码(意大利为 39)，人口，一些众所周知的缩写，如何拼写其他国家的货币，等等。

我想介绍的最后一点是第四条语句，在这里我调用了`entireMainFor`方法。这允许加载所需国家的地区数据(在上面的例子中是意大利、美国和英国)。

### 格式化数字

为了格式化数字，Globalize 提供了`formatNumber`方法。该方法的签名是

```
formatNumber(value[, options]) 
```

其中`value`是要格式化的数字，`options`是用于定制方法返回值的对象。您可以指定的选项示例如下:

*   `round`:定义如何对数字进行四舍五入。它的值可以是下列任意一个:`ceil`、`floor`、`round`或`truncate`
*   `useGrouping`:布尔值，表示是否应该使用分组分隔符
*   `minimumIntegerDigits`:非负整数，表示要使用的最小整数位数。

[可用选项的完整列表可在文档](https://github.com/jquery/globalize/blob/master/doc/api/number/number-formatter.md#parameters)中找到。

现在我们已经了解了更多关于`formatNumber`方法的知识，让我们看看它的实际应用。

```
// Include the Globalize library
var Globalize = require('globalize');

// Include the CLDR data
var cldrData = require('cldr-data');

// Loads the supplemental data
Globalize.load(cldrData.entireSupplemental());

// Loads the data of the specified locales
Globalize.load(cldrData.entireMainFor('it', 'en', 'en-GB'));

var number = 1302.93;

// "1.302,93"
console.log(Globalize('it').formatNumber(number));

// "1,302.93"
console.log(Globalize('en').formatNumber(number));

// "1,302.93"
console.log(Globalize('en-GB').formatNumber(number)); 
```

### 格式化货币值

这个库提供了一个`currencyFormatter`方法来帮助你格式化货币值。该方法支持许多选项，允许您定义是否要舍入数字、是否要使用货币符号(例如“$”)或其代码(例如“USD”)等等。

使用`currencyFormatter()`的例子如下所示:

```
// Include the Globalize library
var Globalize = require('globalize');

// Include the CLDR data
var cldrData = require('cldr-data');

// Loads the supplemental data
Globalize.load(cldrData.entireSupplemental());

// Loads the data of the specified locales
Globalize.load(cldrData.entireMainFor('en'));

var enGlobalize = Globalize('en');

var value = 229.431;

var usdFormatter = enGlobalize.currencyFormatter('USD');
// "$229.43"
console.log(usdFormatter(value));

var eurFormatter = enGlobalize.currencyFormatter('EUR', {
  style: 'code',
  round: 'ceil'
});
// "229.44 EUR"
console.log(eurFormatter(value)); 
```

### 解析数字

解析数字也可能是您必须执行的任务，可能是在处理用户输入时。以下示例演示了如何做到这一点:

```
// Include the Globalize library
var Globalize = require('globalize');

// Include the CLDR data
var cldrData = require('cldr-data');

// Loads the supplemental data
Globalize.load(cldrData.entireSupplemental());

// Loads the data of the specified locales
Globalize.load(cldrData.entireMainFor('en'));

// Set default locale
var enGlobalize = Globalize('en');

var numberParser = enGlobalize.numberParser();
// "229.431"
console.log(numberParser('229,431.00'));

var percentParser = enGlobalize.numberParser({style: 'percent'});
// "0.5341"
console.log(percentParser('53.41%')); 
```

### 格式化相对日期

现代 web 应用程序的另一个非常普遍的特征是用相对术语显示时间和日期。例如，您通常会找到诸如“昨天”和“上周”之类的标签，而不是显示一天的完整日期。由于采用了`relativeTimeFormatter`方法，用 Globalize 完成这项任务非常简单。下面显示了一个使用示例:

```
// Include the Globalize library
var Globalize = require('globalize');

// Include the CLDR data
var cldrData = require('cldr-data');

// Loads the supplemental data
Globalize.load(cldrData.entireSupplemental());

// Loads the data of the specified locales
Globalize.load(cldrData.entireMainFor('en'));

// Set default locale
var enGlobalize = Globalize('en');

var dayFormatter = enGlobalize.relativeTimeFormatter('day');
// "yesterday"
console.log(dayFormatter(-1));

var yearFormatter = enGlobalize.relativeTimeFormatter('year');
// "next year"
console.log(yearFormatter(1)); 
```

Globalize 提供了许多我在本文中没有介绍的其他方法。但是，这里涵盖的主题应该已经为您提供了足够的信息，可以让您开始工作。此外，图书馆的文件非常详细。

## 结论

在本文中，我讨论了什么是国际化和本地化，以及为什么它们对扩大产品市场很重要。我通过提到一些受支持的特性向您简要介绍了国际化 API，然后展示了一些使用它们的例子。

在本文的第二部分，我向您介绍了 Globalize，这是一个由 jQuery 团队开发的用于国际化和本地化的 JavaScript 库。这个库非常强大，提供了国际化项目所需的所有方法，例如:解析数字、格式化日期和格式化货币值的方法。如果你认为图书馆节省了你的时间，那就为这个项目做点贡献吧。

我想再次提醒您，本文中演示的所有片段都可以在我们的 GitHub 库中找到[。](https://github.com/sitepoint-editors/introducing-globalize)

## 分享这篇文章