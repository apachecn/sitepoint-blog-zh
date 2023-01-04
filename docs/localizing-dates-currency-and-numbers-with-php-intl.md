# 用 Php-Intl 本地化日期、货币和数字

> 原文：<https://www.sitepoint.com/localizing-dates-currency-and-numbers-with-php-intl/>

本系列的第一部分介绍了 PHP Intl 扩展以及如何本地化应用程序的消息。在这一部分，我们将学习本地化数字、日期、日历和类似的复杂数据。我们开始吧！

![Globe stock illustration](img/d0eda263397baa2bc145547c264e1161.png)

## 本地化小数

这听起来可能有点奇怪，但我在格式化数字时主要关心的是小数点，因为它们因地而异。查看[维基百科](https://en.wikipedia.org/wiki/Decimal_mark#Examples_of_use)了解更多关于不同十进制符号变化的细节。

| 风格 |
| --- |
| One million two hundred and thirty-four thousand five hundred and sixty-seven point eight nine |
| One million two hundred and thirty-four thousand five hundred and sixty-seven point eight nine |
| 1234567,89 |
| 1,234,567·89 |
| 1.234.567,89 |
| 1˙234˙567,89 |
| 12,34,567.89 |
| 1’234’567.89 |
| 1’234’567,89 |
| 1.234.567’89 |
| 123,4567.89 |

PHP Intl 扩展有一个处理数字本地化的 [`NumberFormatter`](http://php.net/manual/en/numberformatter.create.php) :

```
$numberFormatter = new NumberFormatter( 'de_DE', NumberFormatter::DECIMAL );
var_dump( $numberFormatter->format(123456789) );

$numberFormatter = new NumberFormatter( 'en_US', NumberFormatter::DECIMAL );
var_dump( $numberFormatter->format(123456789) );

$numberFormatter = new NumberFormatter( 'ar', NumberFormatter::DECIMAL );
var_dump( $numberFormatter->format(123456789) );

$numberFormatter = new NumberFormatter( 'bn', NumberFormatter::DECIMAL );
var_dump( $numberFormatter->format(123456789) ); 
```

```
string(11) "123.456.789"
string(11) "123,456,789"
string(22) "١٢٣٬٤٥٦٬٧٨٩"
string(30) "১২,৩৪,৫৬,৭৮৯" 
```

第一个参数是地区代码，第二个是格式样式。在这种情况下，我们格式化小数。

### 格式样式

格式化风格描述了我们的数字应该如何格式化:十进制，货币，持续时间等。查看文档中可用格式样式的列表。让我们尝试一些不同风格的例子:

```
$numberFormatter = new NumberFormatter( 'en_US', NumberFormatter::DECIMAL );
$numberFormatter->setAttribute(NumberFormatter::FRACTION_DIGITS, 2);
var_dump( $numberFormatter->format(1234.56789) );

$numberFormatter = new NumberFormatter( 'en_US', NumberFormatter::DECIMAL );
$numberFormatter->setAttribute(NumberFormatter::FRACTION_DIGITS, 2);
var_dump( $numberFormatter->format(1234) ); 
```

```
string(8) "1,234.57"
string(8) "1,234.00" 
```

指定小数部分属性时，使用向上模式将值向上舍入。我们可以通过指定舍入样式来改变这一点。

```
$numberFormatter = new NumberFormatter( 'en_US', NumberFormatter::DECIMAL );
$numberFormatter->setAttribute(NumberFormatter::MAX_FRACTION_DIGITS, 2);
$numberFormatter->setAttribute(NumberFormatter::ROUNDING_MODE, NumberFormatter::ROUND_CEILING);
var_dump($numberFormatter->format(1234.5678) );

$numberFormatter = new NumberFormatter( 'en_US', NumberFormatter::DECIMAL );
$numberFormatter->setAttribute(NumberFormatter::MAX_FRACTION_DIGITS, 2);
$numberFormatter->setAttribute(NumberFormatter::ROUNDING_MODE, NumberFormatter::ROUND_DOWN);
var_dump($numberFormatter->format(1234.5678) ); 
```

```
string(8) "1,234.57"

string(8) "1,234.56" 
```

第一部分的`spellout`和`duration`选项也可用于以下选项:

```
$numberFormatter = new NumberFormatter( 'en_US', NumberFormatter::DURATION );
var_dump( $numberFormatter->format(123) );

$numberFormatter = new NumberFormatter( 'en_US', NumberFormatter::SPELLOUT );
var_dump( $numberFormatter->format(123) ); 
```

```
string(4) "2:03"
string(24) "one hundred twenty-three" 
```

我们还可以解析字符串，从中获得格式化的值:

```
$numberFormatter = new NumberFormatter( 'en_US', NumberFormatter::DURATION );
var_dump( $numberFormatter->parse("4:03") );

$numberFormatter = new NumberFormatter( 'en_US', NumberFormatter::SPELLOUT );
var_dump( $numberFormatter->parse("one hundred") ); 
```

```
float(243)

float(100) 
```

尽管我们完全可以使我们的应用程序不受语言环境的限制，但是我们应该在语言环境之间切换，以测试各种变化。

## 货币本地化

将数字格式化为货币并没有很大的不同，我们只是改变了格式化类型并添加了货币代码。

```
$numberFormatter = new NumberFormatter( 'en_US', NumberFormatter::CURRENCY );
var_dump( $numberFormatter->formatCurrency(1234.56789, "USD" ) ); 
```

```
string(9) "$1,234.57" 
```

我们可以通过在`NumberFormatter`实例上调用`getSymbol`方法来避免为每个地区输入货币代码。

```
$numberFormatter = new NumberFormatter( 'en_US', NumberFormatter::CURRENCY );
var_dump( $numberFormatter->formatCurrency(1234.56789, $numberFormatter->getSymbol(NumberFormatter::INTL_CURRENCY_SYMBOL)) );

$numberFormatter = new NumberFormatter( 'fr_FR', NumberFormatter::CURRENCY );
var_dump( $numberFormatter->formatCurrency(1234.56789, $numberFormatter->getSymbol(NumberFormatter::INTL_CURRENCY_SYMBOL)) ); 
```

```
string(9) "$1,234.57"

string(14) "1 234,57 €" 
```

我们也可以将前面提到的属性(`$numberFormatter->setAttribute(NumberFormatter::MAX_FRACTION_DIGITS, 2);`)用于货币。

## Timezones

在使用 PHP 国际日历之前，我们需要复习时区，并尝试一个简单的例子:

> 时区是出于法律、商业和社会目的而遵守统一标准时间的区域。时区倾向于遵循国家及其分支机构的边界，因为这样便于商业或其他交流密切的地区保持相同的时间。
> 
> —维基百科

[IntlTimeZone](http://php.net/manual/en/class.intltimezone.php) 类负责创建和管理时区。这与 [DateTimeZone](http://php.net/manual/en/class.datetimezone.php) 类中的时区没有什么不同:

```
$timezone = IntlTimeZone::createDefault();
var_dump($timezone, $timezone->getDisplayName());

$timezone = IntlTimeZone::countEquivalentIDs("GMT");
var_dump($timezone);

$timezone = IntlTimeZone::createTimeZone("GMT");
var_dump($timezone); 
```

```
object(IntlTimeZone)#1 (4) {
  ["valid"]=>
  bool(true)
  ["id"]=>
  string(3) "UTC"
  ["rawOffset"]=>
  int(0)
  ["currentOffset"]=>
  int(0)
}

string(3) "GMT"

int(10)

object(IntlTimeZone)#1 (4) {
  ["valid"]=>
  bool(true)
  ["id"]=>
  string(3) "GMT"
  ["rawOffset"]=>
  int(0)
  ["currentOffset"]=>
  int(0)
} 
```

## 日历

PHP Intl 扩展有一个很好的、富于表现力的 API 来执行[日历操作](http://php.net/manual/en/class.intlcalendar.php):

```
$calendar = IntlCalendar::createInstance();
var_dump($calendar->getTimeZone()->getId()); 
```

```
string(3) "UTC" 
```

[`createInstance`](http://php.net/manual/en/intlcalendar.createinstance.php) 方法接受一个时区(来自上一节)和一个地区代码。我们还可以从一个`DateTime`实例创建一个日历实例。

```
$calendar = IntlCalendar::fromDateTime( (new DateTime) ); 
```

我们可以在日历日期之间做各种比较。

```
$calendar1 = IntlCalendar::fromDateTime( DateTime::createFromFormat('j-M-Y', '11-Apr-2016') );
$calendar2 = IntlCalendar::createInstance();
$durationFormatter = new NumberFormatter( 'en_US', NumberFormatter::DURATION );

$diff = $calendar1->fieldDifference($calendar2->getTime(), IntlCalendar::FIELD_MILLISECOND);

var_dump(
    $calendar1->equals($calendar2), 
    $diff,
    $durationFormatter->format( $diff )
); 
```

```
bool(true)
int(595)
string(4) "9:55" 
```

*重要提示*:我们的`calendar1`变量由`diff`值推进，所以在`fieldDifference`方法调用后日历是相等的。在使用这些类时，请记住这种可变性！

`IntlCalendar::FIELD_MILLISECOND`常量定义了比较的类型，年、月、周等。查看[文档](http://php.net/manual/en/class.intlcalendar.php#intlcalendar.constants)获取完整列表。

如果你以前用过 [`briannesbitt/carbon`](https://www.sitepoint.com/suggesting-carbon-with-composer-date-and-time-the-right-way/) 包，你可能会喜欢这种富于表现力的日期导航语法。我们可以使用`IntlCalendar`类做同样的事情。

```
$calendar1 = IntlCalendar::createInstance();
var_dump(IntlDateFormatter::formatObject($calendar1));

$calendar1->add(IntlCalendar::FIELD_MONTH, 1);
var_dump(IntlDateFormatter::formatObject($calendar1));

$calendar1->add(IntlCalendar::FIELD_DAY_OF_WEEK, 1);
var_dump(IntlDateFormatter::formatObject($calendar1));

$calendar1->add(IntlCalendar::FIELD_WEEK_OF_YEAR, 1);
var_dump(IntlDateFormatter::formatObject($calendar1)); 
```

```
string(25) "Apr 12, 2016, 12:06:22 AM"
string(25) "May 12, 2016, 12:06:22 AM"
string(25) "May 15, 2016, 12:06:22 AM"
string(25) "May 22, 2016, 12:06:22 AM" 
```

查看[文档](http://php.net/manual/en/class.intlcalendar.php)了解更多细节和示例。

## 结论

在这个由两部分组成的系列文章中，我们发现了 PHP Intl 扩展和 ICU 库。该扩展还有一些其他部分，如[整理器](http://php.net/manual/en/class.collator.php)、[欺骗检查器](http://php.net/manual/en/class.spoofchecker.php)、 [UConverter](http://php.net/manual/en/class.uconverter.php) 等。我们将在随后的帖子中重点讨论这些，但同时，如果你有任何问题或评论，请在下面留下！

## 分享这篇文章