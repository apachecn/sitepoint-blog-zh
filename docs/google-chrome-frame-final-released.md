# 铬框架最终发布

> 原文：<https://www.sitepoint.com/google-chrome-frame-final-released/>

Chrome 框架已经退出测试版，稳定版可从 google.com/chromeframe/获得。

## 什么是 Chrome 边框？

Chrome Frame 是谷歌“修复”ie 浏览器的尝试。许多组织，尤其是政府和大型组织，使用为 IE6 编写的任务关键的遗留内部网应用程序，而这些应用程序在其他浏览器中无法运行。如果他们不能或不愿升级应用程序，用户必须保留 IE。因此，Web 开发人员无法在新的应用程序中利用 HTML5、CSS3、canvas 和 SVG 等现代技术的力量。

最终用户可以有两个或更多的浏览器。然而，分发多个浏览器、维护更新、培训和支持的成本并不总是划算的——尤其是在当前的经济环境下。很少有用户知道什么是浏览器，支持团队可能会因为员工对他们正在访问的应用程序使用错误的浏览器而不堪重负。

Chrome 框架是解决这一问题的一种新颖方法。它是一个 IE 插件，可以将浏览器切换到 Chrome 的 webkit 渲染引擎:

1.  企业可以为遗留应用保留旧版本的 IE。
2.  用户保留一个浏览器、收藏夹和 cookies，因此培训和支持最少。
3.  Web 开发人员可以配置 web 应用程序，在 IE 界面中切换到 Chrome 的视图。

## 用户如何安装 Chrome 框架？

插件可以从[google.com/chromeframe/](http://www.google.com/chromeframe/)下载。IT 管理员可以[下载一个 MSI 安装程序](http://www.google.com/chromeframe/eula.html?msi=true)，用于在网络上部署 Chrome Frame。

谷歌打算像 Chrome 浏览器一样定期更新插件。更新在后台悄悄进行——很少有用户会意识到他们安装了 Chrome Frame。

## 开发者如何使用 Chrome 框架？

如果你想让 IE 用户在 Chrome 的渲染模式下查看你的应用程序，你有几个选择…

**1。添加一个 meta 标签**
将下面的 meta 标签添加到你的 HTML 页面`<head>`:

```
 <meta http-equiv="X-UA-Compatible" content="chrome=1"> 
```

**2。使用 HTTP 头**
在服务页面时使用以下 HTTP 头:

```
 X-UA-Compatible: chrome=1 
```

这可以由您的 web 服务器在整个站点范围内发送。对于 Apache，确保启用了 mod_headers 和 mod_setenvif，并将以下内容添加到 httpd.conf 配置文件中:

```
 <IfModule mod_setenvif.c>
  <IfModule mod_headers.c>
    BrowserMatch chromeframe gcf
    Header append X-UA-Compatible "chrome=1" env=gcf
  </IfModule>
</IfModule> 
```

IIS 7.0 和更高版本的用户可以在应用程序的 web.config 文件中设置标头:

```
 <configuration>
  <system.webServer>
     <httpProtocol>
        <customHeaders>
           <add name="X-UA-Compatible" value="chrome=1" />
        </customHeaders>
     </httpProtocol>
  </system.webServer>
</configuration> 
```

更多信息可从 [Chrome Frame 开发者中心](http://code.google.com/chrome/chromeframe/)获得。Google 还提供了一个 JavaScript 库来检测 Chrome 框架并提示用户安装插件。

## 机构会采用 Chrome 框架吗？

与其他解决方案相比，Chrome Frame 带来的人力和技术障碍更少，更多的组织应该考虑这个现已退出测试的插件。它的速度和稳定性都得到了提高，像 HootSuite、T2、github、T4、YouTube 和谷歌的其他系统都在转向 Chrome 框架。

虽然这是一个聪明的想法，但不能保证公司会在整个企业中安装这个插件。许多人需要一个令人信服的财务理由——不仅仅是因为他们的网页设计师想要使用时髦的 CSS3 效果。

你的公司还在使用 IE 的旧版本吗？你在考虑 Chrome 边框吗？

## 分享这篇文章