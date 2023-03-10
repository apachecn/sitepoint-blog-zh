# 如何使用 HTML5 电池状态 API

> 原文：<https://www.sitepoint.com/html5-battery-status-api/>

如果幸运的话，在电池耗尽之前，你可以使用智能手机或平板电脑七八个小时。预计在笔记本电脑上大约只有一半。电池技术没有跟上移动计算的发展步伐。十年前，你可以指望你的 Palm Pilot 用两节 AAA 电池就能坚持一个月。现在你每天晚上都在喂饲设备，你的房间就像闪烁的红色和绿色 LED 版本的拉斯维加斯一样亮起来。

在开发网络应用程序时，我们很少有人担心电池续航时间，但现在[移动接入已经达到五分之一的用户](/browser-trends-december-2013-blink-browsers-bite-back/)，或许我们应该更加考虑周全。幸运的是， [W3C 电池状态 API](https://www.w3.org/TR/battery-status/) 已经被 Mozilla 和 Google 实现了——而且使用起来非常简单。

## 有什么不那么简单的…

我们可以检测电池何时达到临界水平，但我们能做些什么呢？这将取决于您的应用，但功耗的主要原因是…

**屏幕**
你的背光屏幕是最大的果汁饕餮。当电池电量达到临界水平时，您可以考虑几个选项:

*   切换到亮暗主题
*   禁用非关键的 CSS3 和 JavaScript 动画
*   尽可能避免 DOM 更改

**网络活动**
Wi-fi 和移动网络需要耗电的无线电波通信。考虑:

*   减慢或停止频繁的 Ajax 轮询
*   使用 [AppCache 并创建离线应用](/creating-offline-html5-apps-with-appcache/)
*   使用[网络存储](/html5-web-storage/)在客户端存储数据
*   避免对图像等非关键资产的请求。

**声音和触觉输出**
声音和[振动](/use-html5-vibration-api/)会杀死电池；您可以使用较短的效果或完全禁用它。

**处理**
高负荷处理对电池寿命和手机发热有明显影响！除了快速动作游戏，很少有 web 应用程序需要复杂的持续客户端计算。我怀疑除了建议用户插件继续之外，你几乎没有什么可以做的。

您还应该注意到，这些选项中的许多可能适用于[页面可见性 API](/introduction-to-page-visibility-api/) 。例如，如果游戏没有在活动标签中运行，它可以暂停，直到用户返回。

## 浏览器支持

在撰写本文时，只有 Firefox 桌面版和移动版提供了对电池 API 的无前缀支持(版本 10 到 16 使用了 moz 前缀)。在 Chromium 的最新版本中，它也有 webkit 前缀。

我们可以使用以下内容创建跨浏览器电池对象:

```
var battery = navigator.battery || navigator.webkitBattery || navigator.mozBattery || navigator.msBattery;

if (battery) {
	// battery API supported
}
```

这是用于 [**电池的 API 演示…**](https://blogs.sitepointstatic.com/examples/tech/battery-api/index.html)

## 基本属性

您可以使用`navigator.battery.level`属性获取电池电量，该属性返回一个介于 0 和 1 之间的值，例如

```
console.log( "battery level: ", Math.floor(battery.level * 100) + "%" );
```

然而，如果设备正在充电，则电流水平可能不那么重要。我们可以使用`navigator.battery.charging`属性来检查状态，如果充电，则返回 true，如果放电，则返回 false:

```
console.log( "device is ", (battery.charging ? "charging" : "discharging") );
```

因此，当电池电量下降到 25%以下，并且设备没有充电时，您可能希望引入功率提升选项，例如

```
var enableEffects = (battery.charging || battery.level > 0.25);

// vibrate for one second
if (enableEffects) navigator.vibrate(1000);
```

您可能会发现其他一些有用的属性:

*   `navigator.battery.chargingTime` —充电达到 100%的时间，单位为秒。
*   `navigator.battery.dischargingTime` —电池电量完全耗尽且设备关闭之前的时间，以秒为单位。

在当前的浏览器中，这两个属性似乎都不能像您所期望的那样工作。Firefox 返回零或无穷大，这不是特别有用。

## 电池状态事件

电池对象可以触发四个事件:

*   **充电变化** —设备从充电变为放电，反之亦然
*   **电量变化** —电池电量已经变化
*   **充电时间改变** —电池充满电的时间已经改变
*   **放电时间改变** —电池完全放电的时间已经改变

例如，使用我们的跨浏览器`battery`对象:

```
battery.onlevelchange = function() {

	var ee = enableEffects;
	enableEffects = (battery.charging || battery.level > 0.25);

	if (enableEffects != ee) {
		if (enableEffects) {
			console.log( "Battery power is OK." );
		}
		else {
			console.log( "Battery power is critical!" );
		}
	}

}
```

有人会说你的网络应用不应该无谓地消耗电池。然而，电池 API 允许我们做出更复杂的决定，什么时候可以依赖动画、效果和 Ajax 调用。

[**查看电池 API 演示…**](https://blogs.sitepointstatic.com/examples/tech/battery-api/index.html) *(火狐和 Chromium)*

## 分享这篇文章