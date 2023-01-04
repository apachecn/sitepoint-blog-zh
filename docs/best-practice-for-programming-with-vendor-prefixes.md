# 使用供应商前缀编程的最佳实践

> 原文：<https://www.sitepoint.com/best-practice-for-programming-with-vendor-prefixes/>

供应商前缀使 web 开发人员能够在到达候选推荐阶段之前尝试新的标准。我之前[写过](http://blogs.msdn.com/b/ie/archive/2010/11/15/ie9-vendor-prefixes-and-developers.aspx)这些前缀也是浏览器厂商用来处理实现和规范之间时间冲突的一种机制。在为我们的 [IE 试驾](http://ie.microsoft.com/testdrive/)站点构建新功能的演示时，以及在各种演示中，我们 IE 团队中的许多人都广泛地使用了供应商前缀。

本文描述了一种模式，我们的团队使用这种模式在使用供应商前缀进行开发时大大简化了工作。我们希望与您分享，并听取您对这种方法或您认为是最佳实践的任何其他方法的想法。

### 易出错代码

当使用脚本访问带有供应商前缀的 CSS 属性时，很容易得到如下所示的代码:

```
var elm = document.getElementById("myElement");

elm.style.msTransitionProperty = "all";

elm.style.msTransitionDuration = "3s";

elm.style.msTransitionDelay = "0s";

elm.style.webkitTransitionProperty = "all";

elm.style.webkitTransitionDuration = "3s";

elm.style.webkitTransitionDelay = "0s";

elm.style.MozTransitionProperty = "all";

elm.style.MozTransitionDuration = "3s";

elm.style.MozTransitionDelay = "0s";

elm.style.OTransitionProperty = "all";

elm.style.OTransitionDuration = "3s";

elm.style.OTransitionDelay = "0s";
```

虽然功能强大，但它臃肿、丑陋且容易出错。

### 将供应商前缀的属性合并到一个名称中

更好的模式是定义一个方法，该方法循环遍历属性名列表，如果浏览器不支持任何属性，则返回第一个支持的属性或 null。

```
function FirstSupportedPropertyName(prefixedPropertyNames) {

    var tempDiv = document.createElement("div");

    for (var i = 0; i < prefixedPropertyNames.length; ++i) {

        if (typeof tempDiv.style[prefixedPropertyNames[i]] != 'undefined')

            return prefixedPropertyNames[i];

    }

    return null;

}
```

然后，我们为我们使用的每个带有供应商前缀的属性初始化一个变量，按照我们希望使用的顺序向它传递一个可能属性的数组。

```
var transformName = FirstSupportedPropertyName(["transform", "msTransform", "MozTransform", "WebkitTransform", "OTransform"]);

var backfaceVisibilityName = FirstSupportedPropertyName(["backfaceVisibility", "msBackfaceVisibility", "MozBackfaceVisibility", "WebkitBackfaceVisibility", "OBackfaceVisibility"]);

var transitionName = FirstSupportedPropertyName(["transition", "msTransition", "MozTransition", "WebkitTransition", "OTransition"]);

var animationName = FirstSupportedPropertyName(["animation", "msAnimation", "MozAnimation", "WebkitAnimation", "OAnimation"]);

var gridName = FirstSupportedPropertyName(["gridRow", "msGridRow", "MozGridRow", "WebkitGridRow", "OGridRow"]);

var regionsName = FirstSupportedPropertyName(["flowFrom", "msFlowFrom", "MozFlowFrom", "WebkitFlowFrom", "OFlowFrom"]);

var hyphensName = FirstSupportedPropertyName(["hyphens", "msHyphens", "MozHyphens", "WebkitHyphens", "OHyphens"]);

var columnName = FirstSupportedPropertyName(["columnCount", "msColumnCount", "MozColumnCount", "WebkitColumnCount", "OColumnCount"]);
```

那么整个站点中使用这些属性的代码就会变成这样:

```
var elm = document.getElementById("myElement");

if (transitionName) {

    elm.style[transitionName + "Property"] = "all";

    elm.style[transitionName + "Duration"] = "3s";

    elm.style[transitionName + "Delay"] = "0s";

}

else {

    // fallback for browsers without CSS3 transitions

}
```

注意简单的[特征检测](http://blogs.msdn.com/b/ie/archive/2010/04/14/same-markup-writing-cross-browser-code.aspx)通过在`FirstSupportedPropertyName`中返回`null`来启用。

当 CSS *属性*有供应商前缀时，这种模式也适用。对于 CSS *值*(例如，线性渐变)具有供应商前缀的情况，您可以使用稍微不同的模式:

```
function FirstSupportedFunctionName(property, prefixedFunctionNames, argString) {

    var tempDiv = document.createElement("div");

    for (var i = 0; i < prefixedFunctionNames.length; ++i) {

        tempDiv.style[property] = prefixedFunctionNames[i] + argString;

        if (tempDiv.style[property] != "")

            return prefixedFunctionNames[i];

    }

    return null;

}

var linearGradientName = FirstSupportedFunctionName("backgroundImage", ["-ms-linear-gradient", "-moz-linear-gradient", "-webkit-linear-gradient", "-o-linear-gradient"], "(top, black, white)");

var radialGradientName = FirstSupportedFunctionName("backgroundImage", ["-ms-radial-gradient", "-moz-radial-gradient", "-webkit-radial-gradient", "-o-radial-gradient"], "(50% 50%, circle cover, black, white)");
```

### 测试使用供应商前缀属性的站点

一个常见的问题是，如果某些浏览器尚不支持特性，或者如果没有浏览器支持不带前缀的基于标准的特性，应该使用什么特性名称。有几种方法，每种都有优点:

1.  始终包含所有预期的名称，即使它们在发货浏览器中还不可用。这种方式的好处是，当浏览器添加对厂商前缀的支持或者添加对非前缀属性的支持时，您的站点将“正常工作”,无需更改。风险在于网站会自动获取你从未测试过的行为。供应商前缀表示行为还没有最终确定，所有带前缀的属性和不带前缀的属性的行为可能不一样，所以当浏览器添加支持时，您的站点可能“只是*而不是*工作”
2.  仅包含您可以测试的属性名。这样做的好处是，即使浏览器增加了对新属性的支持，你的站点的行为还是和你第一次写的时候一样。风险在于您不必要地降低了功能。对于示例或演示站点，人们可以将其理解为一个完全没有任何功能的浏览器。

你需要为你的站点确定正确的路径。在我们的大多数演示中，我们希望在任何支持它的浏览器中展示新的 web 平台功能。由于这些演示中的小错误不会给用户带来大问题，我们通常选择选项 1。另一方面，如果你有一个生产场所，行为的改变会给你的业务带来问题，你可能会选择更规避风险的途径。

不管你选择哪条路，有一个不变的事实就是测试。当使用厂商前缀属性时，您利用的是早期的、通常不稳定的功能，即使在浏览器首次引入对属性的支持后，这些功能也可能发生变化，因此对每个浏览器更新进行测试以确保您的站点按预期运行是至关重要的。

## 分享这篇文章