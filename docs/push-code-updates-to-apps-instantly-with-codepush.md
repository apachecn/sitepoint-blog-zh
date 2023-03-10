# 使用 CodePush 即时向应用推送代码更新

> 原文：<https://www.sitepoint.com/push-code-updates-to-apps-instantly-with-codepush/>

创建混合应用程序的框架允许开发人员编写一个代码库，他们可以跨多个平台和应用程序商店使用。这加快并简化了开发时间，但您仍然依赖应用商店提交流程来推出更新，从而减缓了您的开发周期。微软的 CodePush 允许你即时向应用推送代码更新，类似于我最近写的[虹吸](https://www.sitepoint.com/easy-app-publishing-with-react-native-and-siphon/)。CodePush 目前支持 cordova 和 react 本地应用程序，并有一个有用的命令行工具。

## 你会创造什么

在本教程中，我将向你展示如何创建一个简单的基于 cordova 的应用程序。主要重点将是如何配置应用程序来使用 CodePush 更新应用程序，而无需将更新发布到应用程序商店。

## 入门指南

SitePoint 为所有平台和移动设备安装和配置 cordova 提供了全面的指南。如果你以前没用过 cordova，我推荐你先看看。

接下来，安装 CodePush 命令行工具。

```
npm install -g code-push-cli 
```

您需要使用 Microsoft 或 GitHub 帐户注册 CodePush。使用以下命令打开身份验证选项卡:

```
code-push register 
```

## 创建科尔多瓦应用程序

使用以下命令创建 cordova 应用程序:

```
cordova create CordovaMobileApp 
```

导航到项目目录并添加 android 平台:

```
cordova platform add android 
```

构建默认应用程序:

```
cordova build android 
```

这将构建一个*。apk* 文件，您可以将其复制到设备上，或者立即运行，请使用:

```
cordova run android 
```

![Default Cordova Mobile App](img/52f5182ed63fd79718c48c1077869d8e.png)

我将使用 jQuery mobile 为移动应用程序创建一个简单的用户界面。打开 *CordovaMobileApp* 项目目录，在 *www* 文件夹中，编辑【index.html】T4，用以下代码替换现有代码:

```
<!DOCTYPE html>
<html>
<head>
    <title>My Page</title>
    <meta name="viewport" content="width=device-width, initial-scale=1">
    <link rel="stylesheet" href="css/jquery.mobile-1.2.0.min.css" />
    <script src="js/jquery-1.8.2.min.js"></script>
    <script src="js/jquery.mobile-1.2.0.min.js"></script>
</head>
<body>

<div data-role="page">

    <div data-role="header">
        <h1>Cordova Mobile App</h1>
    </div>

    <div data-role="content">    
        <p>Welcome to Cordova App, CodePush</p>        
    </div>

</div>

</body>
</html> 
```

保存更改并重新构建 cordova 应用程序以获得更新的*。apk* 文件。

将它部署到 android 设备上，您应该有更新的用户界面。

![Cordova Mobile App User Interface](img/526719e4f4fc6c9d6b21e9b5bc4bf49c.png)

## 向应用推送代码更新

您刚刚创建了一个简单的 Cordova 应用程序。现在你需要注册代码推送服务。

```
code-push app add CordovaMobileApp 
```

一旦您注册了应用程序，它将返回用于试运行和生产的部署密钥。记下你以后要用的键。注册应用程序后，安装所需的代码推送插件:

```
cordova plugin add cordova-plugin-code-push@latest 
```

插件安装完成后，就可以将代码推送部署密钥添加到移动应用程序的 *config.xml* 文件中了:

```
<platform name="android">
    <preference name="CodePushDeploymentKey" value="YOUR-DEPLOYMENT-KEY" />
</platform> 
```

现在，您可以添加生产密钥或分段密钥。为了确保应用程序可以访问符合“[内容安全策略](https://developer.mozilla.org/en-US/docs/Web/Security/CSP)”(CSP)的平台，请将以下元信息添加到 index.html 页面的*。*

```
<meta http-equiv="Content-Security-Policy" content="default-src https://codepush.azurewebsites.net 'self' data: gap: https://ssl.gstatic.com 'unsafe-eval'; style-src 'self' 'unsafe-inline'; media-src *" /> 
```

为了使应用程序能够同步来自代码推送服务器的更改，您需要在`deviceReady`事件中进行调用。打开 *www/js/index.js* ，将以下代码添加到`onDeviceReady`函数中:

```
onDeviceReady: function() {
    app.receivedEvent('deviceready');
    codePush.sync();
 } 
```

因为您已经将移动应用程序部署到设备上，所以在*index.html*中进行一些用户界面更改，并使用代码推送来推送更新版本。

如图所示，更改*index.html*中的信息:

```
 <p>Welcome to Cordova App, CodePush - UPDATE</p> 
```

接下来，准备应用程序发布。在官方文档中，我尝试使用以下代码来准备和发布移动应用程序:

```
code-push release-cordova <appName> <platform> 
```

但出于某种原因，它对我不起作用。如果它对你也不起作用，那么使用这两个命令来准备和发布应用程序:

```
cordova prepare android
code-push release CordovaMobileApp ./platforms/android/assets/www 0.0.1 
```

`0.0.1`是来自 *config.xml* 文件的版本号。

在您推送应用程序版本后，尝试重新启动安装在您设备上的应用程序，它应该会更新。

![Updated App](img/21c28514dee17a379cc6266f9a4619d1.png)

## 向前推

在本教程中，您学习了如何开始使用 CodePush，这是一种云服务，可以让您立即将代码更新推送到您的混合应用程序。请让我知道你的任何意见或问题。

## 分享这篇文章