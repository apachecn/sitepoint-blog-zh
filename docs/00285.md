# 什么是 JavaScript 国际化 API (I18n)？

> 原文：<https://www.sitepoint.com/javascript-internationalization-api-i18n/>

英语是世界上使用最广泛的语言，然而只有七分之一的人会说英语。它是 3.79 亿人的第一语言，但 9.17 亿人说普通话，4.6 亿人说西班牙语，3.41 亿人说印地语。

许多不讲英语的人居住在互联网飞速发展的新兴市场。如果您的 web 应用程序可以全球翻译，您的潜在目标市场可能会增加 700%！

JavaScript 国际化 API(也称为 i18n)允许您以这样一种方式设计 web 页面和应用程序，即它们可以很容易地适应支持讲不同语言的用户的需求。

在本文中，我们将研究 API 提供的各种方法，以及如何在您的代码中实现它们，以获得更广泛、更国际化的受众。

## 国际化(I18n)可能很棘手

国际化看起来很容易……*直到你尝试去做*。

基于拉丁语的语言可能表面上很相似。例如，请求姓名、电子邮件和日期的表单翻译如下:

*   西班牙文:姓名、电子邮件、日期
*   法语:姓名、电子邮件、日期
*   德语:姓名、电子邮件、数据

Gettext 国际化和本地化系统已经存在了几十年，大多数编程语言都有可用的库。

在更简单的情况下，可以使用某种形式的标记化。例如，以包含以下内容的 HTML 模板为例:

```
<label for="name">{{ NAME }}</label> 
```

当用户将英语设置为其主要语言时，这将被动态替换为*‘name’*。不幸的是，这就是用户界面问题的开始:

1.  同一种语言可以有不同的变体。在西班牙说的西班牙语和在南美洲说的不一样。
2.  一种语言中的单词在另一种语言中可能要长得多。例如，“电子邮件”在俄语中翻译成“электронное письмо”。
3.  文本并不总是从左向右。有些是从右向左书写的，如阿拉伯语、希伯来语、库尔德语和意第绪语。其他的可以从上到下写，比如中文，韩文，日文，台湾文。

许多问题可以通过保持文本最少并采用 CSS 属性如 [`direction`](https://developer.mozilla.org/docs/Web/CSS/direction) 、 [`writing-mode`](https://developer.mozilla.org/docs/Web/CSS/writing-mode) 和[逻辑维度](https://developer.mozilla.org/docs/Web/CSS/CSS_Logical_Properties)进行布局来解决。

## 术语混乱

当您的应用程序需要显示日期、时间、数字、货币或单位时，会出现进一步的混乱。

考虑显示为“12/03/24”的日期。它将被读作:

*   使用 MDY 格式的美国居民的“2024 年 12 月 3 日”
*   使用 DMY 格式的欧洲、南美和亚洲居民的“2024 年 3 月 12 日”，以及
*   “2012 年 3 月 24 日”由加拿大、中国、日本和匈牙利居民发起，他们选择了更加实用的 YMD 格式。

*(请注意，日期分隔符斜线并非在所有语言中都通用！)*

数字“1，000”将读作:

*   美国、英国、加拿大、中国和日本的“一千”，以及
*   在西班牙、法国、德国和俄罗斯，一个数的小数部分用逗号分隔。

单单用英语，情况甚至会很复杂。术语“1000 米”是指:

*   距离美国居民 1 公里(0.62 英里)
*   英国、加拿大和澳大利亚的一千个测量仪器的集合！

## JavaScript Intl API

鲜为人知的 JavaScript [`Intl`对象](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Intl)在大多数现代浏览器和运行时中实现了 ECMAScript 国际化 API。[支持一般都不错](https://kangax.github.io/compat-table/esintl/)，甚至 IE11 还有很多更有用的方法。对于较老的浏览器，有一个 [polyfill](https://github.com/andyearnshaw/Intl.js/) ，API 可以这样检测:

```
if (window.Intl) {
  // Intl supported
} 
```

API 有点不寻常。它为日期、时间、数字和列表提供了几个对象构造函数，传递给它们一个区域设置和一个包含配置参数的可选对象。例如，这里有一个指定美国英语的`DateTime`对象:

```
const dateFormatter = new Intl.DateTimeFormat('en-US'); 
```

这个对象可以被多次使用来调用各种方法，这些方法被传递了一个 [`Date()`值](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Date)(或者一个 [ES6 临时值](https://www.sitepoint.com/javascript-temporal-api-introduction/)(如果可用的话)。 [`format`方法](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Intl/DateTimeFormat/format)通常是最实用的选择。例如:

```
const valentinesDay = dateFormatter.format( new Date('2022-02-14') );
// returns US format "2/14/2022"

const starwarsDay = dateFormatter.format( new Date('2022-05-04') );
// returns US format "5/4/2022" 
```

或者，您可以创建`Intl`对象并在一行代码中运行一个方法:

```
const starwarsDay = new Intl.DateTimeFormat('en-US').format( new Date('2022-05-04') ); 
```

除了`format()`方法，一些对象也支持这些:

*   [`formatToParts()`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Intl/DateTimeFormat/formatToParts) :返回包含格式化字符串的对象数组，如`{ type: 'weekday', value: 'Monday' }`
*   [`resolvedOptions()`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Intl/DateTimeFormat/resolvedOptions) :返回一个新对象，其属性反映所使用的区域设置和格式选项，如`dateFormatter.resolvedOptions().locale`。

## 定义区域设置

所有的`Intl`对象都需要一个[区域参数](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Intl#locales_argument)。这是一个字符串，用于标识:

*   语言子标签
*   脚本子标签(可选)
*   区域(或国家)子标签(可选)
*   一个或多个变体子标签(可选)
*   一个或多个 BCP 47 延伸序列(可选)
*   私人使用的扩展序列(可选)

语言和地域往往就足够了。比如`"en-US"`、`"fr-FR"`等等。

除了使用字符串之外， [`Intl.locale`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Intl/Locale/Locale) 对象还可以用于构建区域设置，例如 12 小时制的美国英语:

```
const us = new Intl.Locale('en', {
  region: 'US', hourCycle: 'h12', calendar: 'gregory'
}); 
```

这可以在另一个`Intl`构造函数中使用。例如:

```
new Intl.DateTimeFormat(us, { timeStyle: 'medium' })
  .format( new Date('2022-05-04T13:00:00') );

// "1:00:00 PM" 
```

如果没有定义区域设置，则使用设备的当前语言和区域设置。例如:

```
new Intl.DateTimeFormat().format( new Date('2022-05-04') ); 
```

这在具有美国设置的设备上返回`"5/4/2022"`,在具有英国设置的设备上返回`"04/05/2022"`。

## 日期和时间

以下工具显示了使用 [`Intl.DateTimeFormat()`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Intl/DateTimeFormat/DateTimeFormat) *格式化的日期和时间的示例(如果没有列出您的语言或地区，请原谅！)*:

参见 [CodePen](https://codepen.io) 上的笔 [i18n 日期时间格式化工具](https://codepen.io/SitePoint/pen/zYobRQb)by SitePoint([@ SitePoint](https://codepen.io/SitePoint))
。