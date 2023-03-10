# 用 HTML5 和 Javascript: 5 构建一个基于位置的移动应用

> 原文：<https://www.sitepoint.com/build-a-location-based-mobile-app-with-html5-and-javascript-part-5/>

在我们系列的前一部分[中，我解释了如何使用 Google Maps API 在地图上显示存储的位置，并追踪从用户当前位置到其汽车的路线。我还演示了几个在应用程序开发过程中有用的实用函数。在这篇倒数第二篇文章中，我将描述最终的效用函数和那些完成“我在哪里停车”的函数。在本系列的最后一部分，我们将演示如何优化运行我们完成的 Cordova 应用程序。
](https://www.sitepoint.com/build-a-location-based-mobile-app-with-html5-and-javascript-part-4/ "Build a Location-Based Mobile App With HTML5 and Javascript: Part 4")

### 效用函数

如简介中所述，还有一个实用函数需要解释，那就是在 URL 中检索参数值的函数。

#### 检索 URL 参数的值

HTTP 协议有几种请求方法，但最常用的两种是 GET 和 POST。您很可能知道，GET 方法通过 URL 发送数据，在问号后面附加变量和值(www.website.com？variable one = build & variable 2 = mobile)。不幸的是，jQuery Mobile 没有提供本地方法来检索 URL 的单个参数。事实上，唯一做类似事情的方法是`$.mobile.path.parseUrl()`。然而，这个方法返回一个具有名为`search`的属性的对象，该属性为您*提供所有的*参数，而不仅仅是所需的参数。

为了满足我们的开发需求，我创建了以下函数。

```
function urlParam(name)
{
   var results = new RegExp('[?&amp;]' + name + '=([^&amp;#]*)').exec(window.location.href);
   if (results != null && typeof results[1] !== 'undefined')
      return results[1];
   else
      return null;
}
```

### 初始化应用程序

现在您已经看到了所有的实用函数，我将解释两个初始化函数，这两个函数支持“我把车停在哪里”。我们要看的第一个函数叫做`initApplication()`，它决定了应用程序元素的行为，比如主页按钮(`index.html`)。持仓历史页面(`positions.html`)。`initApplication()`负责管理其他几个元素，您将在接下来的章节中看到更多细节。

#### 有条件地禁用按钮

在我们的系列的[第 4 部分中，我举例说明了我们的应用程序的需求，向您展示了测试它们是否都被满足的功能(`checkRequirements()`)。当用户运行“我停车的地方”时，如果测试失败，设置位置和查找汽车的按钮将被禁用，用户将被通知连接问题。(在这种情况下，他们缺乏使用谷歌地图 API 所需的互联网接入。)这是使用以下代码完成的:](https://www.sitepoint.com/build-a-location-based-mobile-app-with-html5-and-javascript-part-4/ "Build a Location-Based Mobile App With HTML5 and Javascript: Part 4")

```
$('#set-car-position, #find-car').click(function() {
   if (checkRequirements() === false)
   {
      $(this).removeClass('ui-btn-active');
      return false;
   }
});
```

#### 负责

为了增强应用程序界面并使其具有响应性，我创建了函数`updateIcons()`，我将使用它作为`pagebeforecreate`和`orientationchange`事件的处理程序。

```
$(document).on('pagebeforecreate orientationchange', updateIcons);
```

#### 获取或设置位置

下一段代码是整个应用程序最重要的部分之一，是附加到`map-page`元素的`pageshow`事件的回调函数。它负责检查页面是否需要设置或获取汽车的位置，并使用`urlParam()`功能做出相应的动作。如果变量`requestType`的值是`set`，我们将使用地理定位 API 的`getCurrentPosition()`方法来检索位置，然后使用 Web 存储 API 将其存储在 app 数据库中。保存位置后，我将使用我们的`Map`类的`requestLocation()`和`displayMap()`方法来检索汽车位置的地址，并显示用标记指示用户位置的地图。

另一方面，如果`requestType`的值是`get`，我们将使用地理定位 API 的`watchPosition()`方法来轮询用户位置，以不断刷新到他的汽车的路线。

下面是必要的代码。

```
$('#map-page').live(
   'pageshow',
   function()
   {
      var requestType = urlParam('requestType');
      var positionIndex = urlParam('index');
      var geolocationOptions = {
         timeout: 15 * 1000, // 15 seconds
         maximumAge: 10 * 1000, // 10 seconds
         enableHighAccuracy: true
      };
      var position = new Position();

      $.mobile.loading('show');
      // If the parameter requestType is 'set', the user wants to set
      // his car position else he want to retrieve the position
      if (requestType == 'set')
      {
         navigator.geolocation.getCurrentPosition(
            function(location)
            {
               // Save the position in the history log
               position.savePosition(
                  new Coords(
                     location.coords.latitude,
                     location.coords.longitude,
                     location.coords.accuracy
                  )
               );
               // Update the saved position to set the address name
               Map.requestLocation(location);
               Map.displayMap(location, null);
               navigator.notification.alert(
                  'Your position has been saved',
                  function(){},
                  'Info'
               );
            },
            function(error)
            {
               navigator.notification.alert(
                  'Unable to retrieve your position. Is your GPS enabled?',
                  function(){
                     alert("Unable to retrieve the position: " + error.message);
                  },
                  'Error'
               );
               $.mobile.changePage('index.html');
            },
            geolocationOptions
         );
      }
      else
      {
         if (position.getPositions().length == 0)
         {
            navigator.notification.alert(
               'You have not set a position',
               function(){},
               'Error'
            );
            $.mobile.changePage('index.html');
            return false;
         }
         else
         {
            navigator.geolocation.watchPosition(
               function(location)
               {
                  // If positionIndex parameter isn't set, the user wants to retrieve
                  // the last saved position. Otherwise he accessed the map page
                  // from the history page, so he wants to see an old position
                  if (positionIndex == undefined)
                     Map.displayMap(location, position.getPositions()[0]);
                  else
                     Map.displayMap(location, position.getPositions()[positionIndex]);
               },
               function(error)
               {
                  console.log("Unable to retrieve the position: " + error.message);
               },
               geolocationOptions
            );
         }
      }
   }
);
```

#### 初始化职位的历史页面

一旦用户需要文件`positions.html`，jQuery Mobile 将开始增强页面的组件。jQuery 框架完成工作后，我们需要检索用户以前的位置，并以列表的形式显示出来。因此，我将为`position-page`元素的`pageinit`事件设置一个回调函数。回调函数将简单地调用`createPositionsHistoryList()`函数，稍后将对其进行说明。

```
$('#positions-page').live(
   'pageinit',
   function()
   {
      createPositionsHistoryList('positions-list', (new Position()).getPositions());
   }
);
```

### 创建职位的历史列表

如前所述，`createPositionsHistoryList()`使用存储的位置历史和网络存储 API 逐项创建一个列表。列表中的每个条目都有两个用户可以运行的操作。一旦用户触摸到先前位置的地址文本，就会执行第一个动作，这将在地图上显示它，就像单击“查找汽车”时显示当前汽车位置一样。这是通过将相同的值(`get`)发送到用于`requestType`参数的地图文件(`map.html`)并添加一个额外的值(称为`index`)来实现的，该值指示必须处理位置历史的哪一项。如果连接不可用并且选择了一个位置，用户将被通知连接问题，并且不会发生任何其他操作。

通过触摸列表中每个项目地址右侧显示的删除图标，执行第二个操作。当然，这将从列表和数据库中删除选中的项目。如果删除项目有任何问题，用户将会收到警告。

下面列出了`createPositionsHistoryList()`的完整源代码。

```
/**
 * Create the positions' history list
 */
function createPositionsHistoryList(idElement, positions)
{
   if (positions == null || positions.length == 0)
      return;

   $('#' + idElement).empty();
   var $listElement, $linkElement, dateTime;
   for(var i = 0; i < positions.length; i++)
   {
      $listElement = $('<li>');
      $linkElement = $('<a>');
      $linkElement
      .attr('href', '#')
      .click(
         function()
         {
            if (checkRequirements() === false)
               return false;

            $.mobile.changePage(
               'map.html',
               {
                  data: {
                     requestType: 'get',
                     index: $(this).closest('li').index()
                  }
               }
            );
         }
      );

      if (positions[i].address == '' || positions[i].address == null)
         $linkElement.text('Address not found');
      else
         $linkElement.text(positions[i].address);

      dateTime = new Date(positions[i].datetime);
      $linkElement.text(
         $linkElement.text() + ' @ ' +
         dateTime.toLocaleDateString() + ' ' +
         dateTime.toLocaleTimeString()
      );

      // Append the link to the <li> element
      $listElement.append($linkElement);

      $linkElement = $('<a>');
      $linkElement.attr('href', '#')
      .text('Delete')
      .click(
         function()
         {
            var position = new Position();
            var oldLenght = position.getPositions().length;
            var $parentUl = $(this).closest('ul');

            position.deletePosition($(this).closest('li').index());
            if (oldLenght == position.getPositions().length + 1)
            {
               $(this).closest('li').remove();
               $parentUl.listview('refresh');
            }
            else
            {
               navigator.notification.alert(
                  'Position not deleted. Something gone wrong so please try again.',
                  function(){},
                  'Error'
               );
            }

         }
      );
      // Append the link to the <li> element
      $listElement.append($linkElement);

      // Append the <li> element to the <ul> element
      $('#' + idElement).append($listElement);
   }
   $('#' + idElement).listview('refresh');
}
```

### 运行应用程序

在最后一节中，构建了应用程序的所有部分，所有的 HTML、CSS 和 JavaScript 文件都放在了它们的位置上。因此，现在您已经准备好构建和部署“我停车的地方”。但是，您必须为整个应用程序设置入口函数。正如你可能已经猜到的，这个函数将会是`initApplication()`，一旦 Cordova 完全加载，它就会运行。这样，您可以安全地调用 Cordova APIs。

为了实现这个目标，我们将`initApplication()`设置为`deviceready`事件的回调函数。这是通过将下面的代码添加到`index.html`文件中来完成的，因为你已经在[系列的第二部分](https://www.sitepoint.com/build-a-location-based-mobile-app-with-html5-and-javascript-part-2/ "Build a Location-Based Mobile App With HTML5 and Javascript: Part 2")中列出了它的源代码。

```
<script>
   $(document).one('deviceready', initApplication);
</script>
```

### 结论

在本文中，我解释了文件`function.js`的最后几个功能。在本系列的下一篇也是最后一篇文章中，您将看到如何创建配置文件(`config.xml`),在[的第一篇文章](https://www.sitepoint.com/build-a-location-based-mobile-app-with-html5-and-javascript-part-1/ "Build a Location-Based Mobile App With HTML5 and Javascript: Part 1")中有描述，用于 [Adobe PhoneGap Build](http://build.phonegap.com) 。配置文件将帮助我们指定应用程序的某些属性，如作者、加载屏幕、权限等。正如您所料，我还将发布到资源库的链接，您可以在那里下载完整的源代码，以便可以使用它。我还将分享一些最终想法，关于您可以添加哪些功能来进一步改进应用程序，以及我希望 Cordova 团队在下一个版本中实现哪些功能，从而将我们的应用程序提升到一个新的水平。

## 分享这篇文章