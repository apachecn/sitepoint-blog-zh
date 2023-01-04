# 关于振动 API 的讨论

> 原文：<https://www.sitepoint.com/the-buzz-about-the-vibration-api/>

随着移动网络的不断发展，专门针对移动设备的 API 正在被设计出来。一个主要的例子是 W3C 的[振动 API](https://www.w3.org/TR/vibration/ "Vibration API") ，它让开发者可以访问设备的振动硬件。这对游戏有明显的影响，因为它可以提供更身临其境的体验。这篇文章将教你如何在你的应用程序中添加触觉反馈，让你的用户兴奋不已。


## 检测支架

振动 API 仍然主要不受支持。Firefox 16+是目前唯一支持 API 的浏览器。因此，在尝试使用振动之前，您必须检查浏览器支持。以下函数通过检查 API 的唯一函数`[vibrate()](https://developer.mozilla.org/en-US/docs/DOM/window.navigator.vibrate "window.navigator.vibrate - Document Object Model (DOM) | MDN")`来检测浏览器支持。请注意，即使底层设备实际上不包含任何振动硬件，浏览器也有可能支持振动 API。在这种情况下，制造振动将没有效果。

```
function supportsVibration() {
  return !!window.navigator.vibrate;
}
```

## 制造振动

使用`window.navigator.vibrate()`功能产生振动。`vibrate()`函数接受一个参数`pattern`，它指定一个或多个振动持续时间。如果`pattern`是整数，那么会产生单个振动。振动将持续`pattern`毫秒。例如，下面的代码将产生一秒钟的振动。

```
window.navigator.vibrate(1000);
```

`pattern`参数也可以是整数数组。在这种情况下，数组元素表示交替的振动和暂停持续时间。使用这种语法，前面的示例可以重写，如下所示。

```
window.navigator.vibrate([1000]);
```

因为前面的例子只指定了一个振动，所以没有必要指定任何暂停。然而，下一个例子创造了一秒、两秒和三秒的振动。在这个例子中，振动之间有一秒钟的停顿。

```
window.navigator.vibrate([1000, 1000, 2000, 1000, 3000]);
```

### 消除振动

如果在设备执行振动模式时调用`vibrate()`，则执行模式被中止，新模式开始。此行为允许您更新振动模式。如果新模式是零、空数组或零数组，它还允许您取消振动模式。例如，以下所有模式将消除任何现有的振动模式。

```
window.navigator.vibrate(0);
window.navigator.vibrate([]);
window.navigator.vibrate([0]);
```

## 结论

Vibration API 非常简单，但它代表了 Web 应用程序设计方式的重大转变。API 不仅迎合了移动设备，还让开发人员能够访问客户端设备的硬件。这是本机应用程序和 Web 应用程序之间界限越来越模糊的又一个例子。

*   查看振动 API 的[在线演示。](http://www.cjihrig.com/development/html5/vibration.htm "The Vibration API Example")

## 分享这篇文章