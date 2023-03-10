# 使用 Electron Forge 和 React 构建安全的桌面应用程序

> 原文：<https://www.sitepoint.com/electron-forge-react-build-secure-desktop-app/>

在本文中，我们将使用 Electron 和 React 创建一个简单的桌面应用程序。它将是一个名为“scratchpad”的小文本编辑器，在你键入时自动保存更改，类似于从零开始的[](https://fromscratch.rocks)**。我们将注意通过使用电子团队提供的最新构建工具 Electron Forge 来确保应用程序的安全性。**

 **[electronic Forge](https://www.electronforge.io/)是“一个创建、发布和安装现代电子应用程序的完整工具”。它提供了一个方便的开发环境，并配置了为多个平台构建应用程序所需的一切(尽管在本文中我们不会涉及这一点)。

我们假设你知道什么是电子和反应，尽管你不需要知道这些来理解这篇文章。

你可以在 [GitHub](https://github.com/sitepoint-editors/scratchpad-example) 上找到完成的应用程序的代码。

## 设置

本教程假设您的计算机上安装了 Node。如果不是这样，请前往[官方下载页面](https://nodejs.org/en/download/)并为您的系统获取正确的二进制文件，或者使用一个[版本管理器](https://www.sitepoint.com/quick-tip-multiple-versions-node-nvm/)，如 nvm。我们还将假设一个 [Git](https://git-scm.com/downloads) 的工作安装。

下面我将使用的两个重要术语是“main”和“renderer”。电子应用程序由 Node.js JavaScript 文件“管理”。这个文件被称为“主”进程，它负责任何与操作系统相关的事情，并负责创建浏览器窗口。这些浏览器窗口运行 Chromium，被称为 Electron 的“渲染器”部分，因为它是真正将某些东西渲染到屏幕上的部分。

现在让我们开始建立一个新项目。既然我们想使用 Electron Forge 和 React，我们就去 Forge 的网站看看[集成 React 的指南](https://www.electronforge.io/guides/framework-integration/react)。

首先，我们需要用 webpack 模板设置电子锻造。下面是我们如何在一个终端命令中做到这一点:

```
$ npx create-electron-app scratchpad --template=webpack 
```

运行这个命令需要一点时间，因为它设置和配置了从 Git 到 webpack 再到一个`package.json`文件的所有内容。完成后，我们进入该目录，这就是我们看到的内容:

```
➜  scratchpad git:(master) ls
node_modules
package.json
src
webpack.main.config.js
webpack.renderer.config.js
webpack.rules.js 
```

我们将跳过`node_modules`和`package.json`，在我们查看`src`文件夹之前，让我们看一下 webpack 文件，因为有三个。这是因为 Electron 实际上运行两个 JavaScript 文件:一个用于 Node.js 部分，称为“main”，它在这里创建浏览器窗口并与操作系统的其余部分进行通信；另一个是 Chromium 部分，称为“renderer”，它实际上是显示在屏幕上的部分。

第三个 webpack 文件是为了避免重复而在 Node.js 和 Chromium 之间设置的任何共享配置。

好了，现在该看一下`src`文件夹了:

```
➜  src git:(master) ls
index.css
index.html
main.js
renderer.js 
```

不太难:一个 HTML 和 CSS 文件，一个用于主文件和渲染器的 JavaScript 文件。看起来不错。我们将在文章的后面打开这些。

### 添加 React

配置 webpack 可能相当令人生畏，所以幸运的是我们可以很大程度上遵循将 React 集成到 electronic 的[指南。我们将从安装我们需要的所有依赖项开始。](https://www.electronforge.io/guides/framework-integration/react)

第一，`devDependencies`:

```
npm install --save-dev @babel/core @babel/preset-react babel-loader 
```

后跟 React 和 React-dom 作为常规依赖项:

```
npm install --save react react-dom 
```

安装完所有的依赖项后，我们需要教会 webpack 支持 JSX。我们可以在`webpack.renderer.js`或`webpack.rules.js`中这样做，但是我们将按照指南将下面的加载器添加到`webpack.rules.js`中:

```
module.exports = [
  ...
  {
    test: /\.jsx?$/,
    use: {
      loader: 'babel-loader',
      options: {
        exclude: /node_modules/,
        presets: ['@babel/preset-react']
      }
    }
  },
]; 
```

好的，应该可以。让我们通过打开`src/renderer.js`并用以下内容替换它的内容来快速测试它:

```
import './app.jsx';
import './index.css'; 
```

然后创建一个新文件`src/app.jsx`并添加如下内容:

```
import React from 'react';
import ReactDOM from 'react-dom';

ReactDOM.render(<h2>Hello from React in Electron!</h2>, document.body); 
```

我们可以通过在控制台中运行`npm start`来测试这是否可行。如果它打开一个窗口说“你好，来自电子反应！”，万事俱备。

您可能已经注意到，当窗口显示时，devtools 是打开的。那是因为`main.js`文件中的这一行:

```
mainWindow.webContents.openDevTools(); 
```

暂时先放着它，因为它会在我们工作时派上用场。我们将在本文的后面配置它的安全性和其他设置。

至于控制台中的错误和警告，我们可以放心地忽略它们。在`document.body`上安装 React 组件确实会有问题，因为第三方代码会干扰它，但我们不是网站，也不会运行任何不属于我们的代码。电子也给了我们一个警告，但是我们以后会处理它。

## 构建我们的功能

提醒一下，我们将构建一个小的便签簿:一个小应用程序，可以保存我们输入的任何内容。

首先，我们将添加 [CodeMirror](https://www.npmjs.com/package/codemirror) 和 [react-codemirror](https://www.npmjs.com/package/react-codemirror) ，这样我们就得到一个易于使用的编辑器:

```
npm install --save react-codemirror codemirror 
```

让我们设置 CodeMirror。首先，我们需要打开`src/renderer.js`并导入和要求一些 CSS。CodeMirror 附带了几个不同的主题，所以选择一个你喜欢的，但是对于这篇文章，我们将使用材料主题。您的 renderer.js 现在应该如下所示:

```
import 'codemirror/lib/codemirror.css';
import 'codemirror/theme/material.css';
import './app.jsx';
import './index.css'; 
```

*注意我们如何在 code mirror CSS 之后导入我们自己的文件**。我们这样做是为了以后可以更容易地覆盖默认样式。***

然后在我们的`app.jsx`文件中，我们将导入我们的`CodeMirror`组件，如下所示:

```
import CodeMirror from 'react-codemirror'; 
```

在`app.jsx`中创建一个新的 React 组件，添加 CodeMirror:

```
const ScratchPad = () => {
  const options = {
    theme: "material"
  };

  const updateScratchpad = newValue => {
    console.log(newValue)
  }

  return <CodeMirror
    value="Hello from CodeMirror in React in Electron"
    onChange={updateScratchpad}
    options={options} />;
} 
```

还要替换 render 函数来加载我们的草稿栏组件:

```
ReactDOM.render(<ScratchPad />, document.body); 
```

当我们现在启动应用程序时，我们应该会看到一个文本编辑器，文本为“你好，来自电子反应中的 CodeMirror”。当我们输入时，更新会显示在我们的控制台上。

我们还看到有一个白色的边框，我们的编辑器实际上并没有填满整个窗口，所以让我们做一些事情。当我们这样做的时候，我们将在我们的`index.html`和`index.css`文件中做一些整理工作。

首先，在`index.html`中，让我们删除 body 元素中的所有内容，因为我们不需要它。然后我们把标题改成“Scratchpad”，这样标题栏就不会写“Hello World！”当应用程序加载时。

我们还会添加一个`Content-Security-Policy`。这意味着什么在本文中要处理的太多了( [MDN 有一个很好的介绍](https://developer.mozilla.org/en-US/docs/Web/HTTP/CSP))，但它本质上是一种防止第三方代码做我们不希望发生的事情的方法。这里，我们告诉它只允许来自我们的源(文件)的脚本，不允许其他的。

总而言之，我们的`index.html`将会非常空，看起来像这样:

```
<!DOCTYPE html>
<html>
  <head>
    <meta charset="UTF-8">
    <title>Scratchpad</title>
    <meta http-equiv="Content-Security-Policy" content="script-src 'self';">
  </head>
  <body></body>
</html> 
```

现在我们转到`index.css`。我们可以把所有的东西都拿掉，换成这个:

```
html, body {
  position: relative;
  width:100vw;
  height:100vh;
  margin:0;
  background: #263238;
}

.ReactCodeMirror,
.CodeMirror {
  position: absolute;
  height: 100vh;
  inset: 0;
} 
```

这做了几件事:

*   它删除了 body 元素默认拥有的边距。
*   它使 CodeMirror 元素与窗口本身具有相同的高度和宽度。
*   它将相同的背景色添加到 body 元素中，因此融合得很好。

*注意我们是如何使用 [inset](https://caniuse.com/mdn-css_properties_inset) 的，它是上、右、下和左值的 CSS 属性简写。因为我们知道我们的应用程序总是要在 Chromium 版本中运行，所以我们可以使用现代 CSS 而不用担心支持！*

这很好:我们有一个可以启动的应用程序，可以让我们输入内容。太棒了。

除此之外，当我们关闭应用程序并重新启动时，一切又都消失了。我们希望写入文件系统，以便保存我们的文本，并且我们希望尽可能安全地这样做。为此，我们现在将重点转移到`main.js`文件。

现在，您可能已经注意到，即使我们给`html`和`body`元素添加了背景色，当我们加载应用程序时，窗口仍然是白色的。这是因为加载我们的`index.css`文件需要几毫秒的时间。为了改善外观，我们可以在创建浏览器窗口时将其配置为特定的背景颜色。所以让我们去我们的`main.js`文件，并添加一个背景颜色。把你的`mainWindow`改成这样:

```
const mainWindow = new BrowserWindow({
  width: 800,
  height: 600,
  backgroundColor: "#263238",
}); 
```

现在当你开始的时候，白色的闪光应该消失了！

### 在磁盘上保存我们的便签簿

当我在本文前面解释电子时，我把它简化了一点。虽然 Electron 有一个主进程和一个渲染器进程，但近年来实际上出现了第三种上下文，即预加载脚本。

preload 脚本背后的想法是，它充当 main(可以访问所有 Node.js APIs)和 renderer(绝对不应该！).在我们的预加载脚本中，我们可以添加能够与主进程对话的函数，然后以不影响应用程序安全性的方式将它们暴露给渲染器进程。

因此，让我们来大致了解一下我们想要做的事情:

*   当用户做出更改时，我们希望将其保存到磁盘。
*   当应用程序启动时，我们希望从磁盘加载回存储的内容，并确保它显示在我们的 CodeMirror 编辑器中。

首先，我们将编写代码，让我们将内容加载和存储到磁盘的`main.js`文件中。该文件已经导入了节点的`path`模块，但是我们还需要导入`fs`来处理文件系统。将此添加到文件的顶部:

```
const fs = require('fs'); 
```

然后，我们需要为存储的文本文件选择一个位置。这里，我们将使用`appData`文件夹，这是为您的应用程序自动创建的存储信息的地方。您可以通过`app.getPath`特性获得它，所以让我们在`createWindow`函数之前给`main.js`文件添加一个`filename`变量:

```
const filename = `${app.getPath('userData')}/content.txt`; 
```

之后，我们将需要两个函数:一个读取文件，一个存储文件。我们称它们为`loadContent`和`saveContent`，下面是它们的样子:

```
const loadContent = async () => {
  return fs.existsSync(filename) ? fs.readFileSync(filename, 'utf8') : '';
}

const saveContent = async (content) => {
  fs.writeFileSync(filename, content, 'utf8');
} 
```

它们都是使用内置`fs`方法的一行程序。对于`loadContent`，我们首先需要检查文件是否已经存在(因为我们第一次启动它时它不在那里！)如果没有，我们可以返回一个空字符串。

`saveContent`甚至更简单:当它被调用时，我们用文件名和内容调用`writeFile`,并确保它被存储为 UTF8。

现在我们有了这些函数，我们需要把它们连接起来。而这些通信的方式是通过 [IPC](https://www.electronjs.org/docs/api/ipc-main) ，进程间通信。让我们接下来设置它。

### 设置 IPC

首先，我们需要从 electronic 导入`ipcMain`，所以确保您在`main.js`中的`require('Electron')`行如下所示:

```
const { app, BrowserWindow, ipcMain } = require('electron'); 
```

IPC 允许您从渲染器向 main 发送消息(反之亦然)。在`saveContent`函数的正下方，添加以下内容:

```
ipcMain.on("saveContent", (e, content) =>{
  saveContent(content);
}); 
```

当我们从渲染器接收到一个`saveContent`消息时，我们用得到的内容调用`saveContent`函数。很简单。但是我们如何调用这个函数呢？这就是事情变得有点复杂的地方。

我们不希望渲染器文件访问所有这些，因为那将非常不安全。我们需要添加一个能够与`main.js`文件和渲染器文件对话的中介。这就是预加载脚本所能做到的。

让我们在`src`目录中创建`preload.js`文件，并在我们的`mainWindow`中链接它，如下所示:

```
const mainWindow = new BrowserWindow({
  width: 800,
  height: 600,
  backgroundColor: "#263238",
  webPreferences: {
    preload: path.join(__dirname, 'preload.js'),
  }
}); 
```

然后，在我们的预加载脚本中，我们将添加以下代码:

```
const { ipcRenderer, contextBridge } = require("electron");

contextBridge.exposeInMainWorld(
  'scratchpad',
  {
    saveContent: (content) => ipcRenderer.send('saveContent', content)
  }
) 
```

`contextBridge.exposeInMainWorld`让我们在我们的`renderer.js`文件中添加一个函数`saveContent`，而不使整个电子和节点可用。这样，呈现器只知道`saveContent`，而不知道内容是如何保存的，或者保存在哪里。第一个参数“scratchpad”是全局变量，`saveContent`将在其中可用。在我们的 React 应用中，我们称之为`window.scratchpad.saveContent(content);`。

让我们现在做那件事。我们打开我们的`app.jsx`文件并像这样更新`updateScratchpad`函数:

```
const updateScratchpad = newValue => {
  window.scratchpad.saveContent(newValue);
}; 
```

就是这样。现在，我们所做的每一项更改都会写入磁盘。但是当我们关闭并重新打开应用程序时，它又是空的。我们也需要在第一次开始时加载内容。

### 当我们打开应用程序时加载内容

我们已经在`main.js`中编写了`loadContent`函数，所以让我们把它挂在我们的 UI 上。我们使用 IPC `send`和`on`来保存内容，因为我们不需要得到响应，但是现在我们需要从磁盘中获取文件并将其发送给呈现器。为此，我们将使用 IPC `invoke`和`handle`功能。`invoke`返回一个承诺，无论`handle`函数返回什么，该承诺都会得到解决。

我们将开始在我们的`main.js`文件中编写处理程序，就在`saveContent`处理程序的下面:

```
ipcMain.handle("loadContent", (e) => {
  return loadContent();
}); 
```

在我们的`preload.js`文件中，我们将调用这个函数，并将其暴露给我们的 React 代码。在我们的`exporeInMainWorld`属性列表中，我们添加了第二个名为`content`的属性:

```
contextBridge.exposeInMainWorld(
  'scratchpad',
  {
    saveContent: (content) => ipcRenderer.send('saveContent', content),
    content: ipcRenderer.invoke("loadContent"),
  }
); 
```

在我们的`app.jsx`中，我们可以用`window.scratchpad.content`得到它，但那是一个承诺，所以我们需要在加载前对它进行`await`。为此，我们将 ReactDOM 渲染器包装在异步 IFFE 中，如下所示:

```
(async () => {
  const content = await window.scratchpad.content;
  ReactDOM.render(<ScratchPad text={content} />, document.body);
})(); 
```

我们还更新了我们的`ScratchPad`组件，使用文本属性作为我们的起始值:

```
const ScratchPad = ({text}) => {
  const options = {
    theme: "material"
  };

  const updateScratchpad = newValue => {
    window.scratchpad.saveContent(newValue);
  };

  return (
    <CodeMirror
      value={text}
      onChange={updateScratchpad}
      options={options}
    />
  );
}; 
```

现在你知道了:我们已经成功地集成了 Electron 和 React，并创建了一个用户可以输入的小应用程序，它会自动保存，而不会给我们的 scratchpad 任何我们不想给它的对文件系统的访问。

我们完了，对吧？嗯，我们可以做一些事情让它看起来更像“应用程序”。

### “更快”装载

你可能已经注意到，当你打开应用程序时，需要一段时间才能看到文本。这看起来不太好，所以最好等到应用程序加载完毕，然后再显示出来。这将使整个应用程序感觉更快，因为你不会看到一个不活跃的窗口。

首先，我们将`show: false`添加到我们的`new BrowserWindow`调用中，并将监听器添加到`ready-to-show`事件中。在这里，我们展示并聚焦我们创建的窗口:

```
const mainWindow = new BrowserWindow({
  width: 800,
  height: 600,
  backgroundColor: "#263238",
  show: false,
  webPreferences: {
    preload: path.join(__dirname, 'preload.js'),
  }
});

mainWindow.once('ready-to-show', () => {
  mainWindow.show();
  mainWindow.focus();
}); 
```

当我们在`main.js`文件中时，我们也将删除`openDevTools`调用，因为我们不想向用户显示:

```
mainWindow.webContents.openDevTools(); 
```

现在，当我们启动应用程序时，应用程序窗口会显示已经存在的内容。好多了！

## 构建和安装应用程序

现在应用程序已经完成，我们可以构建它了。电子锻造已经为此创建了一个命令。运行`npm run make`，Forge 将为你当前的操作系统构建一个应用程序和安装程序，并把它放在“out”文件夹中，无论是`.exe`、`.dmg`还是`.deb`，你都可以随时安装。

*如果你在 Linux 上，得到一个关于`rpmbuild`的错误，安装“rpm”包，比如在 Ubuntu 上用`sudo apt install rpm`。如果你不想制作一个 rpm 安装程序，你也可以从你的`package.json`中的制造商那里移除“@electron-forge/maker-rpm”块。*

这将错过一些基本的东西，如代码签名、公证和自动更新，但我们将这些留到后面的文章。

这是整合电子和反应的一个很小的例子。我们可以对应用程序本身做更多的事情。这里有一些想法供你探索:

*   添加酷酷的桌面图标。
*   基于操作系统设置，通过媒体查询或使用由 Electron 提供的 nativeTheme api 来创建黑暗和光明模式支持。
*   用类似于 [mousetrap.js](https://www.npmjs.com/package/mousetrap) 或者用电子的[菜单加速器](https://www.electronjs.org/docs/api/menu-item)和[全局快捷方式](https://www.electronjs.org/docs/api/global-shortcut#globalshortcut)添加快捷方式。
*   存储和恢复窗口的大小和位置。
*   与服务器同步，而不是与磁盘上的文件同步。

而且别忘了，你可以在 [GitHub](https://github.com/sitepoint-editors/scratchpad-example) 上找到完成的应用。

## 分享这篇文章**