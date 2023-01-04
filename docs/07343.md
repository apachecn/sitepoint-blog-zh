# 用 HTML5 和 Javascript: 3 构建一个基于位置的移动应用

> 原文：<https://www.sitepoint.com/build-a-location-based-mobile-app-with-html5-and-javascript-part-3/>

在系列的[前一部分，我给大家展示了“我把车停在哪里”所有 HTML 页面的代码。然而，到目前为止它们都没有用，因为我们还没有编写它们背后的业务逻辑。在本文中，我们将开始向项目中添加几行](https://www.sitepoint.com/build-a-location-based-mobile-app-with-html5-and-javascript-part-2/ "Build a Location-Based Mobile App With HTML5 and Javascript: Part 2") [jQuery Mobile](https://www.sitepoint.com/javascript/jquery/ "Article on jQuery Mobile") 的配置，然后我将继续解释一些支持该应用程序的 JavaScript 文件，并描述几个 Cordova APIs。

### jQuery 移动配置

jQuery Mobile 为您做了很多事情，只需将它添加到您的项目中，无需任何特殊设置。但是，有时您希望修改或控制一些默认行为。您可以通过编写配置文件来实现这一点。正如在系列文章的第一部分[中所述，该应用程序有一个名为`jquery.mobile.config.js`的文件，正如你可能猜到的，它包含了 jQuery Mobile 的配置。你们中观察力最敏锐的人可能已经注意到，在`index.html`文件中，配置是在库之前加载的。您必须这样做，因为当 jQuery Mobile 启动时，它会触发一个名为`mobileinit`的事件，这也是您必须绑定以覆盖默认设置的事件。因此，您必须在 jQuery Mobile 启动之前，但在 jQuery 库启动之后附加处理程序。](https://www.sitepoint.com/build-a-location-based-mobile-app-with-html5-and-javascript-part-1/ "Build a Location-Based Mobile App With HTML5 and Javascript: Part 1")

您可以通过两种方式覆盖默认配置。第一种方法是使用 [jQuery `extend()`方法](http://api.jquery.com/jQuery.extend/),如下例所示。

```
$(document).bind('mobileinit', function() {
  $.extend($.mobile , {
    property1: value1,
    property2: value2
    // and so on...
  });
});
```

第二种方法是使用`$.mobile`对象设置属性。

```
$(document).bind('mobileinit', function() {
  $.mobile.property1 = value1;
  $.mobile.property2 = value2;
  // and so on...
});
```

解释所有 jQuery Mobile 属性超出了本文的范围，但是您可以通过阅读 [jQuery Mobile 配置文档](http://jquerymobile.com/demos/1.2.0/docs/api/globalconfig.html)来深入研究它们。

在 app 配置文件中，我不会改变很多属性。我将重点讨论改变页面的过渡、显示页面加载器小部件时显示文本的选项以及主题。下面列出了该文件的完整源代码(使用第二种方法来设置 jQuery Mobile 属性)。

```
$(document).on(
  'mobileinit',
  function()
  {
    // Default pages' transition effect
    $.mobile.defaultPageTransition = 'slide';

    // Page Loader Widget
    $.mobile.loader.prototype.options.textVisible = true;

    // Theme
    $.mobile.page.prototype.options.theme  = 'b';
    $.mobile.page.prototype.options.headerTheme = 'b';
    $.mobile.page.prototype.options.contentTheme = 'b';
    $.mobile.page.prototype.options.footerTheme = 'b';
    $.mobile.page.prototype.options.backBtnTheme = 'b';
  }
);
```

### API

从这一节开始，我将开始描述整个项目中使用的 API。除了 Google Maps API，另外两个——地理位置 API 和 Web 存储 API——是 W3C 规范。我们将通过 Cordova 框架使用这些 API，但是一些设备可能已经提供了这些 API 的实现。对于那些设备，Cordova 将使用它们的内置支持，而不是 Cordova 的实现，但是对于那些没有存储支持的设备，Cordova 实现已经被构建为保持与 W3C 规范兼容。

### 管理位置

在这一节中，我将向您展示名为`Position`的类，它负责管理位置。它的目的是使用由两个区域组成的 [Web 存储 API](https://www.w3.org/TR/webstorage/) 来创建、删除和加载位置:会话和本地。Cordova 使用本地存储，因为存储总是在应用程序级别。所有位置都将存储在一个名为“位置”的项目中

在继续之前，我想强调两个事实。首先，要使用 Web 存储 API 存储复杂的数据(如对象和数组)，必须使用 JSON 格式。因此，在`Position`类方法中，您将看到 JSON 类及其`parse()`和`stringify()`方法的使用。第二是 Windows Phone 7 不支持点符号，所以你必须使用`setItem()`和`getItem()`方法来确保所有设备的兼容性。这只是用 Cordova 开发时必须面对的许多怪癖中的第一个。由于“我在哪里停车”并不是针对一个特定的平台，你必须考虑到不同的支持和科尔多瓦 API 的怪癖。幸运的是，我们遇到的第一个并不太难对付。

出于应用程序的目的，我们需要保存用户和汽车的纬度和经度，但我们也需要测量的准确性，以便让用户知道我们的位置有多精确。我将把前三个数据包装成一个名为`Coords`的类。

读取坐标不是很用户友好，所以为了增强用户体验，我们将使用 Google Maps API 来检索当前位置的地址。该应用程序还将存储与 GPS 位置相关的日期和时间。请记住，应用程序将存储多达 50 个位置，因此如果达到限制，多余的位置(旧的)将使用 JavaScript `slice()`方法消除。

对于到目前为止所讨论的内容，`Position`类的起点是由下面的代码实现的。

```
function Position(position, address, datetime)
{
  var _db = window.localStorage;
  var MAX_POSITIONS = 50;

  this.getMaxPositions = function()
  {
    return MAX_POSITIONS;
  }

  this.position = position;
  this.address = address;
  this.datetime = datetime;
}

function Coords(latitude, longitude, accuracy)
{
  this.latitude = latitude;
  this.longitude = longitude;
  this.accuracy = accuracy;
}
```

您可能已经猜到，在面向对象方法的 JavaScript 意义上，`_db`属性是私有的，`MAX_POSITIONS`是常量。对于给定的框架，我们不能做太多事情，事实上，我们需要方法让应用程序与 Web 存储 API 接口。这些方法是

*   `savePosition()`存储汽车的位置
`*   `updatePosition()`如果对 Google Maps API 的回调成功，则使用地址更新最后检索到的位置*   `deletePosition()`允许用户删除之前保存的位置`*   `getPositions()`加载所有存储的位置``

 ``在所有引用的方法中，我将测试数据库变量(`_db`)是否为`null`，如果是，我将向用户显示一条错误消息。我测试这种状态是因为我喜欢尝试预测和管理意外的接口问题。为了显示消息，我将利用 [Cordova 通知 API](http://docs.phonegap.com/en/2.2.0/cordova_notification_notification.md.html#Notification) 的`alert()`方法。大多数受支持的平台都使用本地对话框，然而，其他平台(如 Bada 2。x)使用知名浏览器的`alert()`功能的观感，可定制性较差。科尔多瓦`alert()`函数最多接受四个参数:

1.  **message** :包含要显示的消息的字符串
2.  **alertCallback** :当警告对话框关闭时调用的回调
3.  **标题**:对话框的标题(默认为“Alert”)
4.  **buttonName** :对话框中包含的按钮文本(默认为“OK”)

在使用 PhoneGap 进行开发时，我们还必须考虑另一个怪癖。Windows Phone 7 忽略按钮名称，始终使用默认名称。而且，没有内置的浏览器提醒，所以如果你想使用`alert('message');`，你必须分配`window.alert = navigator.notification.alert`。有关移动操作系统之间差异的完整列表，请参考 [Cordova 通知`alert()`文档](http://docs.phonegap.com/en/2.2.0/cordova_notification_notification.md.html#notification.alert)。

如果你已经做到了这一步，你应该得到一个奖励:所描述的方法的完整源代码。您可以在`getMaxPositions()`方法之后或之前复制并粘贴`Position`类中的代码，这并不重要。

```
this.savePosition = function(position, address)
{
  if (!_db)
  {
    console.log('The database is null. Unable to save position');
    navigator.notification.alert(
      'Unable to save position',
      function(){},
      'Error'
    );
  }

  var positions = this.getPositions();
  if (positions == null)
    positions = [];

  positions.unshift(new Position(position, address, new Date()));
  // Only the top MAX_POSITIONS results are needed
  if (positions.length > this.MAX_POSITIONS)
    positions = positions.slice(0, this.MAX_POSITIONS);

  _db.setItem('positions', JSON.stringify(positions));

  return positions;
}

this.updatePosition = function(index, position, address)
{
  if (!_db)
  {
    console.log('The database is null. Unable to update position');
    navigator.notification.alert(
      'Unable to update position',
      function(){},
      'Error'
    );
  }

  var positions = this.getPositions();
  if (positions != null && positions[index] != undefined)
  {
    positions[index].coords = position;
    positions[index].address = address;
  }

  _db.setItem('positions', JSON.stringify(positions));

  return positions;
}

this.deletePosition = function(index)
{
  if (!_db)
  {
    console.log('The database is null. Unable to delete position');
    navigator.notification.alert(
      'Unable to delete position',
      function(){},
      'Error'
    );
  }

  var positions = this.getPositions();
  if (positions != null && positions[index] != undefined)
    positions.splice(index, 1);

  _db.setItem('positions', JSON.stringify(positions));

  return positions;
}

this.getPositions = function()
{
  if (!_db)
  {
    console.log('The database is null. Unable to retrieve positions');
    navigator.notification.alert(
      'Unable to retrieve positions',
      function(){},
      'Error'
    );
  }

  var positions = JSON.parse(_db.getItem('positions'));
  if (positions == null)
    positions = [];

  return positions;
}
```

### 结论

在第三部分中，您已经看到了如何配置“我停车的地方”来更改页面的过渡、显示页面加载器小部件时显示文本的选项以及主题。您还学习了如何使用 Web 存储 API 存储汽车的位置。在下一篇文章中，我将展示最后两个 JavaScript 文件:`maps.js`和`functions.js`。前者包含初始化应用程序的函数和一些其他实用函数，而后者包含使用 Google Maps API 绘制地图和汽车路线的函数。`` 

## ``分享这篇文章``