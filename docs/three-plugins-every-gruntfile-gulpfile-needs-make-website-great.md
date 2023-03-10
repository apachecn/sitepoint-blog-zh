# 每个 Gruntfile 和 Gulpfile 都需要 3 个插件

> 原文：<https://www.sitepoint.com/three-plugins-every-gruntfile-gulpfile-needs-make-website-great/>

本文是微软网站开发系列的一部分。感谢您对使 SitePoint 成为可能的合作伙伴的支持。

Gruntfiles 和 gulpfiles 如今在 web 项目中无处不在。 [Grunt](http://gruntjs.com/) 和 [gulp](http://gulpjs.com/) ，两者都是 JavaScript 任务运行器(或工作流构建器)，几乎可以用于任何事情，例如 JavaScript/CSS 文件的缩小、CoffeeScript 或 TypeScript 编译、浏览器同步等。

他们使用 node.js，所以如果你不熟悉 npm 和 node.js 如何工作，阅读这个[教程系列](http://blogs.msdn.com/b/cdndevs/archive/2014/09/04/node-js-tutorial-series-a-chatroom-for-all-part-1-introduction-to-node.aspx?WT.mc_id=16546-DEV-sitepoint-article82)会是个好主意。随着你的 gruntfiles 和 gulpfiles 随着生态系统中的大量插件不断增长，你需要三个插件，不仅使你的网站变得伟大，而且使网络变得更好。

## JavaScript Lint 插件

一个 JavaScript 林挺 grunt 或 gulp 插件将帮助你写出更好的 JavaScript，更少的错误。在我看来，没有林挺插件，任何一个 web 构建工作流都是不完整的。一个林挺插件提醒你有问题的代码，并帮助你写更干净的代码。有四个流行的林挺插件: [JSLint](https://github.com/douglascrockford/JSLint) 、 [JSCS](http://jscs.info/) 、 [JSHint](http://jshint.com/) 和 [ESLint](http://eslint.org/) 。围绕哪个林挺插件是最好的，可能会有无休止的争论。我推荐 ESLint 有两个原因。

*   ESLint 可与您的定制插件一起插入。
*   ESLint 支持 JSX，如果你是一名 React 开发者，这会让你的生活变得更好。

首先，在您的`node_modules`中安装插件:

```
npm install grunt-eslint
```

或者:

```
npm install gulp-eslint
```

将插件添加到您的 gruntfile，不要忘记更改`target`属性:

```
 grunt.loadNpmTasks('grunt-eslint');
grunt.initConfig({
	eslint: {
		target: ['YOUR_JAVASCRIPT_FILES/*.js']
	}
});
grunt.registerTask('lint', ['eslint']); 
```

或者将插件添加到 gulpfile 中，不要忘记更改`src`参数:

```
 var eslint = require('gulp-eslint');
gulp.task('lint', function () {
	gulp.src(['YOUR_JAVASCRIPT_FILES/*.js'])
	.pipe(eslint())
	.pipe(eslint.format())
	.pipe(eslint.failAfterError());
}); 
```

运行`grunt lint`或`gulp lint`任务查看输出。

## Web 标准插件

web 标准 grunt 或 gulp 插件将帮助您开发更好的 HTML、CSS 和 JavaScript 代码，这些代码遵循现代 web 标准的最佳实践。遵循网络标准将帮助你实现一个不那么容易出错的网站，它可以与过去和未来的设备、浏览器、屏幕尺寸等兼容。我开发了一个[咕噜](https://github.com/MicrosoftDX/grunt-webstandards)和[吞咽](https://github.com/MicrosoftDX/gulp-webstandards)插件，帮助你遵循现代网络标准。上手超级简单。这是一个只读插件，所以它不会修改你的文件，它只会报告你的代码的潜在改进。

首先，在您的`node_modules`中安装插件:

```
npm install grunt-webstandards
```

或者:

```
npm install gulp-webstandards
```

将插件添加到您的 gruntfile，不要忘记更改`target`属性:

```
 grunt.loadNpmTasks('grunt-webstandards');
grunt.initConfig({
	webstandards: {
   		'src': ['dist/YOUR_COMPILED_FILES']
	}
});
grunt.registerTask('webstandards', ['webstandards']); 
```

或者将插件添加到 gulpfile 中，不要忘记更改`src`参数:

```
 var webstandards = require('gulp-webstandards');
gulp.task('webstandards', function(){
	return gulp.src('YOUR_COMPILED_FILES/**/*')
	.pipe(webstandards());
}); 
```

运行`grunt webstandards`或`gulp webstandards`任务以获得建议。

![Running grunt webstandards or gulp webstandards](img/ecc10c2cf37dda2d4589ad646cd1063c.png)

## JavaScript 单元测试插件

作为一个坚信单元测试必要性的人，每个 gruntfile 或 gulpfile 都应该有一个测试插件设置，你真的应该编写单元测试。有很多 JavaScript 测试者，似乎不同的框架会偏爱其中一个。我用[摩卡](http://mochajs.org/)测试，所以让我给你演示一下如何设置。

首先，在您的`node_modules`中安装插件:

```
npm install grunt-mocha-test
```

或者:

```
npm install gulp-mocha
```

将插件添加到您的 gruntfile，不要忘记更改`src`属性:

```
 grunt.loadNpmTasks('grunt-mocha-test');
grunt.initConfig({
	mochaTest: {
		test: {
			options: {
				reporter: 'spec',
				captureFile: 'results.txt', 
			},
			src: ['YOUR_JAVASCRIPT_FILES/**/*.js']
		}
	}
});
grunt.registerTask('mocha', 'mochaTest'); 
```

或者将插件添加到 gulpfile 中，不要忘记更改`src`参数:

```
 var mocha = require('gulp-mocha');
gulp.task('mocha', function(){
	return gulp.src('YOUR_JAVASCRIPT_FILES/**/*.js', {
		read: false
	})
	// gulp-mocha needs filepaths so you can't have any plugins before it
	.pipe(mocha({
		reporter: 'nyan'
	}));
}); 
```

运行`grunt mocha`或`gulp mocha`任务。

仅此而已！如果您有任何反馈或建议，请随时发推特 [@ramisayar](https://twitter.com/ramisayar) 给我。

## 更多的 Web 开发实践

这篇文章是微软布道者和工程师关于实用 JavaScript 学习、开源项目和互操作性最佳实践的 web 开发系列的一部分，包括[微软 Edge](http://blogs.windows.com/msedgedev/2015/05/06/a-break-from-the-past-part-2-saying-goodbye-to-activex-vbscript-attachevent/?WT.mc_id=16546-DEV-sitepoint-article82) 浏览器和新的 [EdgeHTML 渲染引擎](http://blogs.windows.com/msedgedev/2015/02/26/a-break-from-the-past-the-birth-of-microsofts-new-web-rendering-engine/?WT.mc_id=16546-DEV-sitepoint-article82)。

我们鼓励您在 dev.microsoftedge.com 使用免费工具进行跨浏览器和设备测试，包括 Windows 10 的默认浏览器 Microsoft Edge:

*   [扫描你的网站，寻找过时的库、布局问题和可访问性](http://dev.modern.ie/tools/staticscan/?utm_source=SitePoint&utm_medium=article82&utm_campaign=SitePoint)
*   [下载适用于 Mac、Linux 和 Windows 的免费虚拟机](http://dev.modern.ie/tools/vms/windows/?utm_source=SitePoint&utm_medium=article82&utm_campaign=SitePoint)
*   [跨浏览器检查网络平台状态，包括微软 Edge 路线图](https://dev.modern.ie/platform/status/?utm_source=SitePoint&utm_medium=article82&utm_campaign=SitePoint)
*   [在您自己的设备上远程测试 Microsoft Edge](https://remote.modern.ie/?utm_source=SitePoint&utm_medium=article82&utm_campaign=SitePoint)

**向我们的工程师和传道者进行更深入的学习:**

*   **互操作性最佳实践** ( [系列](https://channel9.msdn.com/Blogs/BeLux-Developer/Riding-the-Modern-Web-5-things-to-consider-as-a-web-developer/?WT.mc_id=16546-DEV-sitepoint-article82)):
    *   [如何避免浏览器检测](https://channel9.msdn.com/Blogs/BeLux-Developer/Riding-the-Modern-Web-Avoiding-Browser-Detection/?WT.mc_id=16546-DEV-sitepoint-article82)
    *   [使用 CSS 前缀的最佳实践](https://channel9.msdn.com/Blogs/BeLux-Developer/Riding-the-Modern-Web-CSS-Vendor-Prefixes?WT.mc_id=16546-DEV-sitepoint-article82)
    *   [保持你的 JS 框架&库更新](https://channel9.msdn.com/Blogs/BeLux-Developer/Riding-the-Modern-Web-Dealing-with-JavaScript-Libraries?WT.mc_id=16546-DEV-sitepoint-article82)
    *   [构建插件免费网络体验](https://channel9.msdn.com/Blogs/BeLux-Developer/Riding-the-Modern-Web-Dealing-with-Plugins?WT.mc_id=16546-DEV-sitepoint-article82)
*   [GitHub 编码实验室:跨浏览器测试和最佳实践](https://github.com/deltakosh/interoperable-web-development?WT.mc_id=16546-DEV-sitepoint-article82)
*   哇，我可以在 Mac 电脑上测试 Edge & IE 浏览器& Linux！(来自雷伊·班戈)
*   [在不破坏网络的情况下推进 JavaScript】(来自 Christian Heilmann)](http://channel9.msdn.com/Events/WebPlatformSummit/2015/Advancing-JavaScript-without-breaking-the-web?WT.mc_id=16546-DEV-sitepoint-article82)
*   [用 WebGL 释放 3D 渲染](https://channel9.msdn.com/Events/WebPlatformSummit/2015/Unleash-3D-rendering-with-WebGL-and-Microsoft-Edge?WT.mc_id=16546-DEV-sitepoint-article82)(来自 David Catuhe)
*   托管网络应用和网络平台创新

**我们的社区开源项目:**

*   伏龙。JS (跨设备远程 JavaScript 测试)
*   [manifoldJS](http://manifoldjs.com/?WT.mc_id=16546-DEV-sitepoint-article82) (部署跨平台托管的 web 应用)
*   [babylonJS](http://babylonjs.com/?WT.mc_id=16546-DEV-sitepoint-article82) (轻松制作 3D 图形)

**更多免费工具和后端 web 开发工具:**

*   [Visual Studio 代码](https://code.visualstudio.com/?WT.mc_id=16546-DEV-sitepoint-article82)(用于 Mac、Linux 或 Windows 的轻量级代码编辑器)
*   [Visual Studio 开发基础知识](https://www.visualstudio.com/en-us/products/visual-studio-dev-essentials-vs.aspx?WT.mc_id=16546-DEV-sitepoint-article82)(基于订阅的免费培训和云优势
*   [用节点编码。JS](https://www.microsoftvirtualacademy.com/en-US/training-courses/building-apps-with-node-js-jump-start-8422?WT.mc_id=16546-DEV-sitepoint-article82) 与[在蔚蓝云上试用](https://azure.microsoft.com/en-us/pricing/free-trial/?WT.mc_id=16546-DEV-sitepoint-article82)

## 分享这篇文章