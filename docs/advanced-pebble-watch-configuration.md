# 高级 Pebble 手表配置

> 原文：<https://www.sitepoint.com/advanced-pebble-watch-configuration/>

对于小时候看过《权力流浪者》的人来说，梦想拥有一块让他们感觉像超级英雄的精美手表，智能手表是一个梦想成真。我可能是前面提到的权力游侠梦想家之一。再加上充斥在我血管中的开发欲望，你就有了一个 90 年代的孩子，他很快就会变成像《回到未来》中的埃米特·布朗博士一样的灰色、疯狂的科学家

我最近写了一篇关于用 JavaScript 开发 Pebble Watch 的基础知识的文章。总之，我们制作了一个 watchface，它使用 Foursquare API 随时告诉用户最近的星巴克在哪里。这是它实际运行的样子:

![The Find Me Starbucks app in action](img/2f31524066005e434a2caf74ee9e0315.png)

我提到了 JavaScript 还可以做更多的事情，这是我在那篇文章中无法介绍的。这是我的后续文章，将涵盖 PebbleKit JavaScript 框架的其他特性。

在本文中，我将介绍如何扩展我们在上一篇文章中开发的“查找我的星巴克”应用程序，以允许用户选择应用程序将为他们查找的位置类型。不是每个人都像我一样是星巴克的超级粉丝！我们将添加一个配置屏幕，允许他们输入自己感兴趣的地方，并在每次使用应用程序时为他们保存设置。

如果您想继续学习本教程，您需要:

*   安装并运行 Pebble 应用程序的 Android 手机或 iPhone。
*   一块[鹅卵石手表](http://www.getpebble.com/)。
*   FourSquare API 的 API 凭证。
*   可用的 Wi-Fi 网络将您的应用程序传输到您的移动设备和 Pebble watch。
*   JavaScript 的基础知识。
*   愿意使用命令行并尝试 C 代码。
*   最好已经阅读过[之前的教程](https://www.sitepoint.com/pebble-watch-development-javascript/)或了解使用 PebbleKit JavaScript 框架开发 Pebble 应用程序的基础知识。
*   一个在线的网络主机来在线存储一个文件，这个文件就是你的配置页面。

## 本教程的完整代码

你可以在 GitHub 上查看[“给我找星巴克”的代码，或者通读](https://github.com/patcat/FindMeStarbucks)[之前的教程](https://www.sitepoint.com/pebble-watch-development-javascript/)，而不是把我们上一篇教程中的所有代码都扔进本文。我们更新的应用程序的代码也可以在 GitHub 的[“找到我任何东西”上找到，所以如果你想下载代码并跟随——请随意！](https://github.com/patcat/FindMeAnything)

## Pebble 应用程序世界中的配置工作方式

在 SDK 的 2.0 版本中引入 PebbleKit JavaScript 框架之前，Pebble apps 需要在用户的手机上安装一个配套应用程序，以便接受任何自定义设置。如果用户希望天气应用程序向他们显示摄氏度而不是华氏度的天气，他们需要首先安装配套的应用程序，然后打开它来更改设置(或者在 watchface 应用程序本身上有一个粗略的设置窗口)。

### 启用 JavaScript 的设置页面

借助新的 SDK，我们现在可以在 Pebble 的 Android/iOS 应用程序本身中定义一个可访问的配置页面。用户可以在安装和整理手表应用的同一个地方访问设置。从用户的角度来看更清晰、更合理。

### 启用 JavaScript 的本地存储

新的 SDK 的 JavaScript 也使用 W3C 推荐的 Web 存储，允许你在应用程序中存储设置以供将来使用。如果用户要求设置摄氏/华氏温度，我们可以使用网络存储来记住用户的偏好。在我们的例子中，我们希望存储用户感兴趣的位置类型。

### 它是如何工作的

设置页面是 web 上的一个页面，其中包含一个用于切换应用程序设置的表单。应用程序中的 JavaScript 等待任何调整后的设置通过表单提交回应用程序的 JavaScript，并将接收到的设置存储在本地存储中。

听起来很困惑？在某些情况下，这并不是一个理想的设置，因为这意味着你必须在网上的某个地方建立自己的设置页面，以便其他人访问。似乎 Pebble 计划在未来将该设置页面与应用程序捆绑在一起，这对一些应用程序来说非常好。当前的设置确实有好处——如果你想单独在设置页面上重设皮肤或调整某些东西，没有必要更新应用程序本身，因为设置页面是独立的。

## 更新你的`appinfo.json`

首先，为了在您的应用程序上启用设置页面，我们将`"configurable"`添加到 capabilities 数组中，我们之前使用该数组通过`"location"`关键字来定义对地理位置 API 的访问:

```
"capabilities": ["location", "configurable"]
```

我们还为我们的应用程序添加了一个名为`"searchingFor"`的额外键，这个字符串可以是“Starbucks ”,也可以是用户请求的自定义位置类型:

```
"appKeys": {
  "location": 0,
  "searchingFor": 1
}
```

## 创建您的设置页面

如上所述，您需要托管一个带有表单的网页，供用户更改他们搜索的内容。我的看起来是这样的:

![Find Me Anything settings screen](img/f355dcad75362847ee10f5e1d3580206.png)

您需要的基本 HTML 看起来是这样的(为了简单起见，我去掉了样式和框架相关的标记):

```
<label for="searchingFor">Whatcha wanna find?</label>
<textarea cols="40" rows="8" name="searchingFor" id="searchingFor"></textarea>
<button type="submit" id="b-cancel">Cancel</button>
<button type="submit" id="b-submit">Submit</button>
```

然后，我们使用设置页面上的 JavaScript 将任何更改提交回 Pebble 的 JavaScript，并预填充用户的当前设置。

### 取消或提交设置

我们提供了两个选项，他们可以取消-阻止他们的任何更改保存，或者他们可以提交表单，将这些更改传递到应用程序进行保存。

为了取消他们的更改，我们将`document.location`设置为`pebblejs://close`。这将向我们的 Pebble JavaScript 代码发送一个空响应，不会改变任何事情:

```
$('#b-cancel').click(function() {
  document.location = 'pebblejs://close';
});
```

如果他们选择提交他们的更改，我们将文本区域的输入传递到一个字符串中。下面的`saveOptions()`函数将 textarea 中的值放入一个对象中:

```
function saveOptions() {
  var options = {
    searchingFor: $('#searchingFor').val()
  }
  return options;
}
```

我们的 click 函数通过将对象转换成字符串并对其字符进行编码来提交我们的数据，这样我们就可以将它放在我们用`document.location`定位的 URL 中(任何单个空格都将被编码成`%20`，任何其他特殊字符也将发生变化):

```
$('#b-submit').click(function() {
  var location = 'pebblejs://close#' + encodeURIComponent(JSON.stringify(saveOptions()));
  document.location = location;
});
```

这会成功地将我们想要的设置发送到 Pebble JavaScript。

### 如果用户已经选择了什么呢？

如果用户已经对设置进行了更改，比如说他们已经要求应用程序为他们找到 gelato，我们希望他们之前的设置“gelato”在他们打开设置时出现在文本字段中。为此，我们将在 URL 中向 config HTML 页面发送一个名为`searchingFor`的变量。URL `http://www.yourpebbleappsettingpage.com?searchingFor=gelato`就是这样一个例子。

我有一个很久以前找到的函数，我经常重复使用它来从 URL 中获取变量:

```
function getURLVariable(name)  {
  name = name.replace(/[\[]/,"\\\[").replace(/[\]]/,"\\\]");
  var regexS = "[\\?&]"+name+"=([^&#]*)",
      regex = new RegExp(regexS),
      results = regex.exec(window.location.href);
  if (results == null) return "";
  else return results[1];
}
```

当页面准备好时，我们检查一个变量，并将该变量的文本放入我们的 textfield。该变量被解码，以便任何格式化为在 URL 字符串中工作的字符将正确地出现在文本区域中。为此，我们使用了`decodeURI()`函数。这将把我们的`%20`和其他特殊字符转换回它们实际的显示字符:

```
$(document).ready(function() {
  var priorSearch = getURLVariable('searchingFor');
  priorSearch = decodeURI(priorSearch);

  if (priorSearch) {
    $('#searchingFor').html(priorSearch);
  }
```

请随意设计屏幕样式，让它看起来更漂亮，更吸引眼球！你可以根据自己的喜好来设计它的结构和样式，因为它是一个平面的 HTML 页面。Pebble 示例设置页面使用了 jQuery Mobile，所以我坚持使用它，只是重新设计了一些东西，使它更整洁。使用您喜欢的任何前端框架或 JavaScript 验证。权力是你的！

## 调整我们的 JavaScript 以接受设置

我们的 Pebble 应用知道我们想要允许设置被改变，我们在我们的`appinfo.json`文件中告诉它。我们的应用程序现在甚至有了一个很好的配置页面，看起来很有前景。问题是——我们的 Pebble 应用程序不知道在哪里可以找到我们承诺的配置屏幕。它甚至不知道我们想要在哪里使用我们来回发送的这`searchingFor`位数据——只知道我们打算有一个。这是我们更新`pebble-js-app.js`文件以连接一切的地方。

首先，我们需要 JavaScript 中的一个新变量。让我们称它为`searchingFor`,以匹配我们在`appinfo.json`文件中的键(没有必要将其命名为相同的名称，但是为了一致和清晰，我已经这样做了)。这个变量需要做的是存储用户在 Pebble 增强的日常生活中对我们要寻找的位置类型的偏好。

### Pebble 应用程序中的网络存储

为了在我们的应用程序中保存用户的偏好，我们可以访问 Pebble 为我们提供的 localStorage API。Pebble 很好地遵循了 W3C 关于网络存储的推荐标准，所以对于过去使用过网络存储的人来说，它的语法不会太陌生。为了在本地存储器中存储一些东西，我们称之为:

```
window.localStorage.setItem('ourVariableName', 'Our variable contents');
```

要检索它:

```
window.localStorage.getItem('ourVariableName');
```

因此，在我们的例子中，我们希望将我们的`searchingFor`数据存储在我们的 Pebble 应用程序的本地存储中。我们将使用 JavaScript 变量在整个应用程序中访问它，但有时当我们第一次加载应用程序或从配置屏幕返回时，我们将从本地存储中设置这个`searchingFor`变量。

我们将在 JavaScript 运行时查看本地存储。如果在本地存储中没有找到我们的`searchingFor`数据，那么我们将默认值设置为“Starbucks”。这被整齐地放置在`pebble-js-app.js`文件开头的`locationOptions`定义之后。

```
var locationOptions = {timeout: 15000, maximumAge: 60000},
    searchingFor = window.localStorage.getItem('searchingFor') ? window.localStorage.getItem('searchingFor') : 'Starbucks';
```

然后，我们将这个变量添加到 AJAX 请求中，代替 Starbucks:

```
req.open('GET', 'https://api.foursquare.com/v2/venues/search?client_id='+clientId+'&client_secret='+clientSecret+'&v='+version+'&ll='+latitude+','+longitude+'&query='+searchingFor, true);
```

到目前为止，我们的应用程序将做和以前一样的事情——为我们找到星巴克。我们需要让它知道当用户按下应用程序的“设置”按钮时该做什么。我们用`showConfiguration`事件监听器来做这件事:

```
Pebble.addEventListener('showConfiguration', function() {
  Pebble.openURL('http://www.patrickcatanzariti.com/find_me_anything/configurable.html?searchingFor=' + searchingFor);
});
```

这将把用户带到您的页面，其中包含填写这些设置的表单。当他们提交他们的新设置时(例如上面提到的“gelato”)，我们希望我们的 JavaScript 在`pebble-js-app.js`中准备好并等待数据。

为此，我们使用了`webviewclosed`事件监听器。在这里，我们从我们的 URI 字符串(用`%20`表示空格等的编码版本)将其解码，并再次解析为一个 JSON 对象。我们从 JSON 对象中获取`searchingFor`值，并再次对它进行编码，现在它不在我们的 JSON 字符串中了。最后一点编码是存储它，准备放入前面讨论的 Foursquare API 调用的 URL 字符串中(`https://api.foursquare.com/v2/venues/search?client_id='+clientId+'&client_secret='+clientSecret+'&v='+version+'&ll='+latitude+','+longitude+'&query='+searchingFor`请求):

```
Pebble.addEventListener('webviewclosed', function(e) {
  var options = JSON.parse(decodeURIComponent(e.response));
  searchingFor = encodeURIComponent(options.searchingFor);
```

如果我们的值表现为`undefined`(似乎有很多原因会发生这种情况，但都是边缘情况)，我有一个最后的办法，把它切换回“Starbucks”。将来可能会有更好的方式来处理这个问题，可能会出错，但是为了简化这个演示，我在最坏的情况下默认使用 Starbucks:

```
if (searchingFor == 'undefined') {
  searchingFor = 'Starbucks';
}
```

一旦我们理解了`searchingFor`请求并准备好存储，我们就使用上面提到的`window.localStorage.setItem()`函数将它存储在本地存储中。这样，下次他们打开应用程序时，它仍然会在那里。

```
window.localStorage.setItem('searchingFor', searchingFor);
```

现在我们已经收到了新的设置请求，我们用新的值再次运行`window.navigator.geolocation.watchPosition()`函数。这将在 Foursquare API 关闭设置窗口时，使用他们新请求的设置运行我们的地理位置搜索。

```
locationWatcher = window.navigator.geolocation.watchPosition(fetch_location_data, fetch_location_error, locationOptions);
});
```

我们的 JavaScript 现在理解了这个新设置，但是还有更多的事情要做。

## 对我们的 C 代码的一些补充

我们的`find_me_anything.c`档不需要改动太多。我做了以下的补充和修改。

### 增加消息缓冲区大小

我将消息缓冲区的大小增加到 124 字节。我相信这是你在 Pebble 手表上可以达到的最大值。额外的缓冲区大小使得我可以发送潜在的更大的位置名称和查询。随着新设置的推出，我们的数据长度有了更大的变化，所以我们希望为此提供尽可能多的余地！

当定义`sync_buffer`时，在文件开始处进行了更改:

```
static AppSync sync;
static uint8_t sync_buffer[124];
```

当我们打开消息同步时，我们的`init()`功能也发生了进一步的变化:

```
app_message_open(124, 124);
```

### 向我们的应用程序添加新密钥

我们还需要添加将在 Pebble JavaScript 和 C 代码之间传递的新键:`searchingFor`设置。在 C 代码中，我遵循惯例，使用带下划线的大写字母来分隔单词。我们将`1`定义为十六进制的`0x1`，这意味着我们现在可以在 C 代码中提到第二个键/值对时引用`SEARCHING_FOR`:

```
enum {
  OUR_LOCATION = 0x0,
  SEARCHING_FOR = 0x1
};
```

我更新了`init_location_search()`函数，因此它将`SEARCHING_FOR`值的初始值定义为`''`。它是空白的，因为一旦我们的 JavaScript 开始运行并告诉 C 我们在寻找什么，它就会被填充。

```
Tuplet initial_values[] = {
  TupletCString(OUR_LOCATION, "Loading..."),
  TupletCString(SEARCHING_FOR, "")
};
```

最后，我们更新我们的`sync_tuple_changed_callback()`函数，以便它知道在接收到更新的第二个键/值对时做出响应。当它出现时，我们需要 C 代码做的就是将手表上的标签改为“最近的冰淇淋”或其他新设置，而不是“最近的星巴克”:

```
switch (key) {
  case OUR_LOCATION:
    text_layer_set_text(text_layer, new_tuple->value->cstring);
    break;
  case SEARCHING_FOR:
    text_layer_set_text(label_layer, new_tuple->value->cstring);
    break;
}
```

所有这些都准备好了，现在你应该能够使用配置窗口来改变你的应用程序的设置。当您提交设置时，Pebble 应用程序应该会更新，您将拥有一个定制的应用程序，如下所示:

![Find Me Anything running](img/0fbf297d8e7f50c041344cd1db09a1c3.png)

## 我们 JavaScript 代码的一个额外补充

我在实现这个新特性时发现的一件事是，有时 FourSquare API 实际上没有我们在每个地点需要的地址或值。一些场馆缺少这些数据。当我们在手表上显示时，它返回“未定义”。不好！所以我重新编写了一点我们的`req.onload`函数来检查我们的条目，并使用第一个有地址和城市的条目:

```
if (response && response.meta.code == '200' && response.response) {
  var venues = response.response.venues,
      venue = undefined,
      venueIndex = 0;

  // Look for the first instance of a venue with an address
  while (venues[venueIndex] !== undefined && venue === undefined) {
    if (venues[venueIndex] && venues[venueIndex].location.address !== undefined && venues[venueIndex].location.city !== undefined) {
      venue = venues[venueIndex];
    }

    venueIndex++;
  }

  if (venue && venue.location.address !== undefined && venue.location.city !== undefined) {
    Pebble.sendAppMessage(...);
```

我在上面所做的是设置一个名为`venue`的变量，它以`undefined`开始。我遍历从 Foursquare 返回的`response.response.venues`数据中的每一项，如果我找到一项包含地址和城市，那么我将该对象保存在`venue`中，我们不再搜索响应的其余部分。

### 我们现在是一个稍微更令人兴奋的应用程序！

多亏了 PebbleKit JavaScript 框架的强大功能，我们现在让 Pebble watch 用户决定如何使用我们的应用程序。它是定制的，更加个性化。我们现在还使用本地存储，所以我们的应用程序可以记住事情。这是一个比以前更有价值的应用程序，我们充分利用了 JavaScript 在 Pebble watch 上的功能。

最后，我的用户现在可以沉迷于他们自己的痴迷，而不是我个人的星巴克之一。即使你不喜欢制作一个能给你地址的时钟，希望这个例子能给你的应用程序添加个性化提供一些指导。尝试让人们将背景从黑色改为白色，或者让他们选择不同的 API 或 feed 来获取数据。让他们决定最终让你的应用程序变得更有用、更吸引人的小事情。

花一个周末的时间疯狂编码，或者在回家的火车上写点东西！使用 JavaScript 知识做一些你从未想过可能的事情，并在这个过程中获得乐趣！

## 更多资源

*   [用 JavaScript 开发 Pebble Watch](https://www.sitepoint.com/pebble-watch-development-javascript/)——我的上一篇文章《给我找星巴克》就是在这里诞生的。对于那些刚刚起步的人，我在这里有很多资源。
*   [使用 PebbleKit JavaScript 框架](https://developer.getpebble.com/2/guides/javascript-guide.html)——Pebble 团队关于使用其 JavaScript 框架的更多信息

## 分享这篇文章