# 在 Swift 中使用 MeasurementFormatter

> 原文：<https://www.sitepoint.com/using-measurementformatter/>

# 了解度量格式器

`MeasurementFormatter`类提供了单位和度量的格式化和本地化表示。当面向全球受众时，您应该以本地单位呈现这些数据。假设您的应用程序向终端用户显示两点之间的距离。假设距离是英制单位:英尺、码和英里。当面向全球受众时，您应该以本地单位呈现这些数据。请考虑以下情况:

`The distance between New York and Paris is 3,626.81 miles`

在法语中，您不仅要翻译字符串的文本，还要翻译其中包含的度量单位:

`La distance entre New York et Paris est de 5 836,78 km`

您应该利用 Apple Foundation 的强大功能，而不是尝试编写自己的实用程序类来执行这些转换。

## 运行正常的

在处理距离测量时，`MeasurementFormatter`在零配置下表现出色。当用户安装您的应用程序时，用户的设备已经有了默认的区域设置。使用上面的距离测量，我们可以转换为用户的本地化标准:

```
// distance in user's default locale
// this would print with different formats in various locales:
// 3626.81 miles for en_US
// 5.836,77 km for en_IT
// 5 836,77 km for en_FR
// 5,836.77 km for en_JP
// To find out your locale: NSLocale.current
import Foundation
let distanceInMiles = Measurement(value: 3626.81, unit: UnitLength.miles)
MeasurementFormatter().string(from: distanceInMiles)
```

考虑到简单性，很明显使用`MeasurementFormatter`是正确的方法。作为开发人员，您应该养成总是使用基础格式化程序来呈现数据的习惯。这将导致面向未来的代码和更少的重构。

## 了解区域设置

编程中的区域设置描述了语言、文化和技术的约定和标准。大多数测量单位属于三种系统之一:英制、美制或公制(又名:SI 系统)。由语言环境封装的信息示例可能包括:

*   数字和货币中使用的符号，例如:
    *   1，200.99 美元和 1，200.99 美元
    *   1 200.99 欧元和 1 234 567.89 欧元
*   日期格式，例如:
    *   日期顺序:年/月/日对年/月/日
    *   展示日期:2016 年 11 月 23 日或 2016 年 11 月 23 日
    *   国际假日的命名
*   测量单位，例如:
    *   英制与公制测量
    *   这种(米、米等)的拼法
    *   卡路里的缩写，如“cal”和“C”

虽然我们可能习惯于自己的一套衡量标准和符号，但世界上许多地区都习惯于自己的标准和符号。拼写、缩写和符号因国家和语言而异。这导致了一组非常复杂的规则和转换。以下是 Foundation 通常处理的一些单位变化的快速示例:

*   *单元的命名和拼写*
    *   e.g .:米、米、米、米等
*   *单位尺寸*
    *   例如:美制加仑(. 26 升)，英制加仑(. 22 升)
*   *单位缩写*
    *   例如:cal、kcal、cal 或 C
*   *自然尺寸*
    *   例如:英尺对英里，米对公里

您还可以设置区域设置并存储用户的偏好。下面是一个在现实世界中如何使用它的例子，基于上面的代码:

```
import Foundation

// Create a local variable instance of MeasurementFormatter which we can reuse and configure
let formatter = MeasurementFormatter()
let distanceInMiles = Measurement(value: 3626.81, unit: UnitLength.miles)

// You should collect this for UIPickerView or similar input
// and replace "en_FR" with the result of the picker
// This will save the string to the "locale" key in device storage
UserDefaults.standard.set("en_FR", forKey: "locale")

// From this point on, we want to refer to the saved preference
// If the User default is nil, we failover to "en_FR"
let localeIdentifier = UserDefaults.standard.object(forKey: "locale") ?? "en_FR"

// Last we initiate our Locale class with this identifier
let locale = Locale(identifier: localeIdentifier as! String)

// Optionally set the `locale` property of our instance.
// If we do not set this, it will default to the user's device locale
formatter.locale = locale
formatter.string(from: distanceInMiles) // prints "5 836,77 km"
```

您的应用程序应该允许用户全局设置他们的本地化首选项。这在处理数据时尤其重要。开发人员经常依赖用户的设备设置，但这可能会导致问题。例如:想象一个居住在法国的美国侨民。他们的设备可能被设置为法语地区，因为他们可能知道这种语言。这仍然是一个问题，因为他们可能没有掌握公制度量衡。基于每个应用程序的本地化首选项是一个受欢迎的特性。

## 单位风格

`MeasurementFormatter`的`unitStyle`属性是其中最基本的。该属性需要来自`UnitStyle`枚举的案例，该枚举提供以下内容:

*   `.short`
    *   将度量单位格式化为最短的缩写版本或符号。
    *   例如: **10 英尺**将被格式化为**10’**
*   `.medium`
    *   如果没有指定，这是默认值。
    *   将度量单位格式化为缩写
    *   例如: **10 英尺**将被格式化为 **10 英尺**
*   `.long`
    *   以完整的书面形式格式化度量单位。
    *   例如: **10 英尺**将被格式化为 **10 英尺**

```
import Foundation

// You can optionally set the `unitStyle` property
// Expects enum case from `MeasurementFormatter.UnitStyle`
// Defaults to `.medium`

// You can simply pass the case value: `.long`
// or you can write it out as shown below

let formatter = MeasurementFormatter()
let distanceInMiles = Measurement(value: 3626.81, unit: UnitLength.miles)

formatter.unitStyle = MeasurementFormatter.UnitStyle.long
formatter.string(from: distanceInMiles) // 3,626.81 miles
```

## 单位选项

`unitOptions`属性类似于`unitStyle`属性。它还需要一个枚举事例，其值为:

*   `.providedUnit`–显示提供的测量单位，不进行转换。
*   给我们施了些魔法。苹果将典型的测量单位映射为更容易被人类理解的数值。例如，一个人不会将 10 英尺描述为 0.00189394 英里。因此，naturalScale 属性通常适用于转换这种度量。
*   非常简单明了。有时，您可能想要使用或显示没有测量单位的温度。这与其他方法不同，因为温度的转换往往保持不变或者是自定义的。

```
let formatter = MeasurementFormatter()

// Working with meters and evaluating the results
let distanceInMeters = Measurement(value: 2, unit: UnitLength.meters)
formatter.string(from: distanceInMeters) // prints "0,002 kilometres"

// However, we can see the class is aware of the original unit of measure
formatter.string(from: UnitLength.meters) // prints "metres"

// Optionally set the `unitOptions` property
// This can be: .providedUnit, .naturalScale, or .temperatureWithoutUnit
// Options provided by MeasurementFormatter.UnitOptions
// This defaults to a privately defined format if unset
formatter.unitOptions = .naturalScale
formatter.string(from: distanceInMeters) // prints "2 metres"
```

## 格式化数字

使用 measure 时，更改数字的格式并不罕见。numberFormatter 属性就是为此而存在的。与`MeasurementFormatter`类似的一个类是`NumberFormatter`类。它们一起工作，让您对字符串输出进行更精细的控制。我们可以实例化并配置`NumberFormatter`，然后将`MeasuremFormatter.numberFormatter`设置为`NumberFormatter`实例:

```
import Foundation
let formatter = MeasurementFormatter()
formatter.unitOptions = .naturalScale
formatter.unitStyle = .long
formatter.locale = Locale(identifier: "en_FR")

let distanceInMeters = Measurement(value: 2, unit: UnitLength.meters) // 2.0 m

// Optionally set the `numberFormatter` property
// Expects an instance of `NumberFormatter`
// You could set the `NumberFormatter.numberStyle` property to:
//   .none
//   .decimal
//   .currency
//   .percent
//   .scientific
//   .spellOut
// Defaults to .decimal if unset

let numberFormatter = NumberFormatter()
numberFormatter.numberStyle = .spellOut
formatter.numberFormatter = numberFormatter

formatter.string(from: distanceInMeters) // two meters
```

这为我们提供了对各种应用的精细控制。

## 细微差别和 Gotchya 的

度量转换是简单的算术，但表示不是。核心库并不总是遵循你所期望的惯例。基础文档和 Swift 源代码由许多不同国家的开发人员编写。因此，许多注释和输出似乎有一些小的不一致。

### 单位名称不一致

尽管地区保持不变，你可能会发现“米”写成了“米”。我相信这只是疏忽，一些社区贡献者已经在 Swift 的未来版本中打开了修复这些问题的雷达。

### 自然尺度并不完美

您可能会发现`naturalScale`属性的转换超出了您的预期范围。在我自己的开发中，我发现在我没有预料到的时候，英寸会转换成英尺，反之亦然。然而，尽管有这些细微差别，我会在 99%的时间里信任苹果，而不是处理边缘案件。这些库随着每个版本不断发展和改进。

## 包扎

`MeasurementFormatter`类是基础框架中更简单的实用类之一。尽管它是一个更基础的类，但它是一个强大的类，也是一个很容易学习的类。

## 进一步阅读

*   [MeasurementFormatter API 文档](https://developer.apple.com/reference/foundation/measurementformatter)
*   [用户默认 API 文档](https://developer.apple.com/reference/foundation/userdefaults)
*   [NSLocale API 文档](https://developer.apple.com/reference/foundation/nslocale)
*   马特·汤普森对 NSLocale 的看法

## 分享这篇文章