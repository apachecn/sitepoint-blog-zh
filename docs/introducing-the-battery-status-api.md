# 电池状态 API 简介

> 原文：<https://www.sitepoint.com/introducing-the-battery-status-api/>

移动设备在所有网络流量中所占的份额越来越大。这种流量是如此巨大，以至于 API 和设计实践都是专门为迎合移动设备而创建的。W3C 的电池状态 API 就是一个例子，它允许应用程序检查设备电池的状态。本文探索了相对较新的 API，并向您展示了如何开始将它集成到您的应用程序中。


## 检测支架

电池支持 API 仍然基本不受支持。因此，在尝试使用它之前，您必须验证它是否存在。以下函数返回一个布尔值，表明用户的浏览器是否支持该 API。该函数检查`navigator.battery`对象是否存在。如果不存在，它还会检查 Mozilla 特定的`mozBattery`对象。我看到过[也检查`webkitBattery`](https://developer.mozilla.org/en-US/docs/DOM/window.navigator.battery "window.navigator.battery - MDN") 的代码，但我还无法验证它在 Chrome 中的存在。

```
function isBatteryStatusSupported() {
  return !!(navigator.battery || navigator.mozBattery);
}
```

## 检查电池

如果`battery`对象存在，它将包含以下四个只读属性。

*   `charging`–该布尔值表示系统电池当前是否正在充电。如果系统没有电池，或者无法确定值，那么这将是`true`。
*   `chargingTime`–该数值代表电池充满电前的剩余时间(秒)。如果电池充满，或者系统没有电池，则`chargingTime`设置为零。如果系统没有充电，或者无法确定剩余时间，则设置为`Infinity`。
*   `dischargingTime`–与`chargingTime`类似，该值表示电池完全放电且系统暂停前剩余的秒数。如果不能确定放电时间，没有电池，或者电池正在充电，那么该值被设置为`Infinity`。
*   `level`–该数值代表当前的电池电量。该值的范围从 0 到 1.0，对应于剩余电池寿命的百分比。值 1.0 表示电池已充满电、没有电池或电量无法确定。

## 检测电池事件

前面讨论的每个属性都与电池事件相关联。这些事件用于识别电池状态的变化。例如，插入系统会导致`charging`从`false`转换到`true`。下面列出了四种类型的电池事件。

*   `chargingchange`–这种类型的事件在`charging`更新时触发。这些事件可以用`onchargingchange()`事件处理程序来处理。
*   `chargingtimechange`–这些事件在`chargingTime`更新时触发。这些事件可以由`onchargingtimechange()`事件处理程序来处理。
*   `dischargingtimechange`–当`dischargingTime`被更新时，一个`dischargingtimechange`事件被触发。这些事件可以由`ondischargingtimechange()`事件处理程序来处理。
*   `levelchange`–对`level`的更新导致这些事件被触发。可以使用`onlevelchange()`事件处理程序来处理它们。

## 演示页面

以下代码显示了如何使用电池状态 API 属性和事件。该示例页显示每个属性的值，并在事件发生时更新它们。该页面也可以在线查看。

```
<!DOCTYPE html>
<html lang="en">
<head>
  <title>The Battery Status API - Example</title>
  <meta charset="UTF-8" />
  <script>
    window.addEventListener("load", function() {
      var battery = navigator.battery || navigator.mozBattery;

      function displayBatteryStats() {
        document.getElementById("charging").textContent = (battery.charging) ? "charging" : "not charging";
        document.getElementById("chargingtime").textContent = battery.chargingTime;
        document.getElementById("dischargingtime").textContent = battery.dischargingTime;
        document.getElementById("level").textContent = battery.level * 100;
      }

      if (battery) {
        displayBatteryStats();
        battery.addEventListener("chargingchange", displayBatteryStats, false);
        battery.addEventListener("chargingtimechange", displayBatteryStats, false);
        battery.addEventListener("dischargingtimechange", displayBatteryStats, false);
        battery.addEventListener("levelchange", displayBatteryStats, false);
      } else {
        document.getElementById("stats").textContent = "Sorry, your browser does not support the Battery Status API";
      }
    }, false);
  </script>
</head>
<body>
  <div id="stats">
    Your battery is currently <span id="charging"></span>.<br />
    Your battery will be charged in <span id="chargingtime"></span> seconds.<br />
    Your battery will be discharged in <span id="dischargingtime"></span> seconds.<br />
    Your battery level is <span id="level"></span>%.
  </div>
</body>
</html>
```

## 结论

本文提供了电池状态 API 的完整概要。虽然支持还没有普及，但这只是时间问题。鉴于移动网络的激增，开发者应该尽快开始将电池整合到他们的设计中。

## 分享这篇文章