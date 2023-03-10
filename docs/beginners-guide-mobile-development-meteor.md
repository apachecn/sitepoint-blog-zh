# 使用 Meteor 进行移动开发的初学者指南

> 原文：<https://www.sitepoint.com/beginners-guide-mobile-development-meteor/>

开箱即用，Meteor JavaScript 框架包括 [Cordova](http://cordova.apache.org) ，

> “一组设备 API，允许移动应用程序开发人员从 JavaScript 访问本机设备功能，如摄像头或加速度计”。

如果你是一个想要在 iOS 和 Android 上发布他们的作品(同时利用这些平台的特性)的 web 开发人员，你不必学习一门新的语言或全新的概念。你只需要对 Meteor 有一个基本的把握，然后对具体到移动开发的细节有一个基本的把握。

## 第 1 步:准备使用 Meteor 进行移动开发。

显然，如果你想用它进行开发，你需要在你的电脑上安装 Meteor。如果没有安装，请在终端中输入以下命令:

```
curl https://install.meteor.com/ | sh
```

你需要对 Meteor 有一个基本的了解，所以要么查看官方网站的“学习资源”部分，要么查看我为初学者写的书。

要为 iOS 开发，需要在您的系统上安装 Xcode 的副本。这可以从 Mac 应用商店免费下载[。](https://itunes.apple.com/en/app/xcode/id497799835?mt=12)

## 步骤 2:向项目添加移动支持。

科尔多瓦是包含在流星本身，但必须手动添加到任何特定的流星项目。这避免了用可能不需要的代码膨胀每个项目。您可以通过添加特定的平台来添加对 Cordova 的支持。

例如，要添加对 iOS 的支持，请在终端中输入以下内容:

```
meteor add-platform ios
```

或者要添加对 Android 的支持，请在终端中输入以下内容:

```
meteor add-platform android
```

当添加对 Android 的支持时，系统会提示您安装任何尚未安装的相关软件。

## 步骤 3:创建一个移动配置文件。

在您的项目文件夹中，创建一个`mobile-config.js`文件。在这个文件中，我们能够为应用程序的移动部分设置许多配置选项，包括:

*   元数据，如应用程序名称和描述。
*   首选项，如应用程序的默认方向。
*   特定 Cordova 插件的附加首选项。

你可以在流星官方文档中看到这个配置的例子:

```
// This section sets up some basic app metadata,
// the entire section is optional.
App.info({
  id: 'com.example.matt.uber',
  name: 'über',
  description: 'Get über power in one button click',
  author: 'Matt Development Group',
  email: 'contact@example.com',
  website: 'http://example.com'
});

// Set up resources such as icons and launch screens.
App.icons({
  'iphone': 'icons/icon-60.png',
  'iphone_2x': 'icons/icon-60@2x.png',
  // ... more screen sizes and platforms ...
});

App.launchScreens({
  'iphone': 'splash/Default~iphone.png',
  'iphone_2x': 'splash/Default@2x~iphone.png',
  // ... more screen sizes and platforms ...
});

// Set PhoneGap/Cordova preferences
App.setPreference('BackgroundColor', '0xff0000ff');
App.setPreference('HideKeyboardFormAccessoryBar', true);

// Pass preferences for a particular PhoneGap/Cordova plugin
App.configurePlugin('com.phonegap.plugins.facebookconnect', {
  APP_ID: '1234567890',
  API_KEY: 'supersecretapikey'
});
```

有关可用选项的完整运行，请查看 Cordova 文档的“[config . XML 文件](http://cordova.apache.org/docs/en/4.0.0/config_ref_index.md.html#The%20config.xml%20File)”部分。

## 第 4 步:编写纯 Cordova 代码。

通过使用 Meteor 制作移动应用程序，您可以一次编写大部分功能。大部分代码可以跨平台运行。但是并不是所有的代码*都应该*跨平台运行，同样，我们可以用`isClient`和`isServer`条件控制某些代码是在客户端还是在服务器上运行，还有一个`isCordova`条件:

```
if(Meteor.isCordova){
    // code goes here
}
```

上面的代码块只有在 Cordova 移动环境中执行时才会运行。(在本教程中，我们不会用到这段代码，但很快就会发现这段代码派上用场。)

## 第 5 步:添加移动包到你的应用程序中。

目前，有三个官方的 Meteor 包可以轻松地将移动功能添加到您的应用程序中:

*   [相机](https://github.com/meteor/mobile-packages/blob/master/packages/mdg:camera/README.md)，用于在移动设备或台式电脑上拍照。
*   [地理定位](https://github.com/meteor/mobile-packages/blob/master/packages/mdg:geolocation/README.md)，用于通过设备的 GPS 追踪用户的位置。
*   [Resume 上的 Reload](https://github.com/meteor/mobile-packages/blob/master/packages/mdg:reload-on-resume/README.md)，它可以在应用程序有更新时通知用户，并鼓励他们重新启动以查看更改。

此外，非官方的软件包可以在[atmospherejs.com](https://atmospherejs.com/?q=mobile)找到。

上面的链接提供了关于如何使用所有这些包的文档，它们足够简单，无需进一步解释你就可以理解它们。作为一个例子，我们来玩一下“相机”包。

我们要做的是创建一个按钮，当点击它时，打开用户设备(Android、iOS 或桌面)上的相机，并允许他们拍照。如果他们拍了一张照片，数据将被返回到应用程序，我们就可以对照片做任何我们想做的事情。

首先，将“Camera”包添加到项目中:

```
meteor add mdg:camera
```

然后删除项目的默认 HTML 文件的内容，并替换为以下内容:

```
<head>
  <title>Camera Example</title>
</head>
<body>
  {{> takePhoto}}
</body>

<template name="takePhoto">
  <p><input type="button" class="capture" value="Take Photo"></p>
</template>
```

这里，我们创建了一个包含“拍照”按钮的界面。

在默认的 JavaScript 文件中，删除当前内容并替换为以下内容:

```
if(Meteor.isClient){
  Template.takePhoto.events({
    'click .capture': function(){
      console.log("Button clicked.");
    }
  });
}
```

由于我们现在创建的这个事件，每当点击按钮时(或者在智能手机的情况下，每当点击按钮*)就会在 JavaScript 控制台内出现一条消息。*

 *在此事件中，编写以下内容:

```
MeteorCamera.getPicture();
```

这是内置在“相机”包中的功能，允许我们接入用户的硬件来捕捉照片。它接受两个参数:

*   选项，如设置照片的宽度和高度。
*   一个回调函数，用于检索照片的数据。

目前，我们不会讨论任何选项:

```
MeteorCamera.getPicture({});
```

但是我们将传递一个回调函数作为第二个参数:

```
MeteorCamera.getPicture({}, function(error, data){
  // something goes here
});
```

由于这个回调函数，我们现在可以检索任何错误，以及捕获的照片的数据。要查看实际情况，请使用一条 log 语句:

```
MeteorCamera.getPicture({}, function(error, data){
  console.log(data);
});
```

在支持网络摄像头的计算机上测试该应用程序，注意在您捕获照片后，控制台中会出现一个 URL。我们可以使用这个 URL 将照片嵌入到界面中。

首先，将图像数据存储在一个会话中:

```
MeteorCamera.getPicture({}, function(error, data){
  Session.set('photo', data);
});
```

然后为“拍照”模板创建一个`helpers`块:

```
Template.takePhoto.helpers({
  'photo': function(){
    /// code goes here
  }
});
```

这里，我创建了一个“photo”帮助器，稍后我们将把它嵌入到模板中，但是我们需要返回“photo”会话的值:

```
Template.takePhoto.helpers({
  'photo': function(){
    return Session.get('photo');
  }
});
```

然后，在“takePhoto”模板中，简单地引用这个助手:

```
<template name="takePhoto">
  <p><input type="button" class="capture" value="Take Photo"></p>
  <p>{{photo}}</p>
</template>
```

现在，当我们捕获一张照片时，一个类似的字符串将出现在界面中，该字符串将在一个`img`标记的`src`属性中工作得非常好:

```
<template name="takePhoto">
  <p><input type="button" class="capture" value="Take Photo"></p>
  <p><img src="{{photo}}"></p>
</template>
```

当然，我们目前只是在计算机上执行这些代码，而我们真正想要的是在移动应用程序中执行。

## 步骤 6:测试你的应用程序。

通过在命令行中输入以下命令，您可以在 iOS 模拟器中运行该应用程序:

```
meteor run ios
```

请注意，照片功能实际上不会在 iOS 模拟器中工作。它可以在手机本身、Android 模拟器和桌面浏览器上运行，但不能在 iOS 模拟器上运行。(不过，你也可以使用其他手机套餐，所以我建议你可以尝试一下。)

要在 Android 模拟器中运行应用程序，请使用以下命令:

```
meteor run android
```

如果你还没有使用`meteor add-platform android`命令，你将不得不安装一些额外的软件，但是终端会引导你完成这个过程。

## 结论

在本教程中，我们只介绍了用 Meteor 创建一个移动友好应用程序的基础，但我希望这足以吸引您进一步深入研究。Meteor 是一个非常有趣的框架，虽然在许多情况下本地构建移动应用程序可能更有意义，但 Cordova 集成仍然为那些对学习其他技术不感兴趣的人提供了一个优雅的选择。

## 分享这篇文章*