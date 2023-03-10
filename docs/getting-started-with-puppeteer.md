# 木偶师入门

> 原文：<https://www.sitepoint.com/getting-started-with-puppeteer/>

浏览器开发工具为深入研究网站和网络应用程序提供了一系列令人惊叹的选项。这些功能可以通过第三方工具进一步增强和自动化。在本文中，我们将看看[木偶师](https://pptr.dev/)，一个与 Chrome/Chromium 一起使用的基于节点的库。

木偶师网站将木偶师描述为

> 一个节点库，它提供了一个高级 API 来通过 DevTools 协议控制 Chrome 或 Chrome。默认情况下，Puppeteer 是无头运行的，但是可以配置为运行完整的(非无头)Chrome 或 Chrome。

木偶师是由谷歌 Chrome 背后的团队制作的，所以你可以非常肯定它会得到很好的维护。它让我们可以在 Chromium 浏览器上执行常见的操作，通过简单易用的 API，用 JavaScript 编程。

使用木偶师，您可以:

*   抓取网站
*   生成包括 SVG 和 Canvas 在内的网站截图
*   创建网站的 pdf
*   爬网 SPA(单页应用程序)
*   使用标准的 DOM API 访问网页并提取信息
*   生成预先呈现的内容，即服务器端呈现
*   自动化表单提交
*   自动化性能分析
*   自动化 UI 测试，如 [Cypress](https://cypress.io)
*   测试 chrome 扩展

Puppeteer 并没有比 [Selenium](https://www.seleniumhq.org/) 、 [PhantomJS](https://phantomjs.org/) (现在已被否决)等做得更新，但它提供了一个简单易用的 API，并提供了一个很好的抽象，因此我们在处理它时不必担心具体的细节。

由于 Chromium 支持 ECMAScript，它也得到积极的维护，所以我们获得了 ECMAScript 的所有新特性。

## 先决条件

对于本教程，您需要 JavaScript、ES6+和 Node.js 的基础知识。

您还必须安装了最新版本的 [Node.js](https://nodejs.org/) 。

我们将在整个教程中使用`yarn`。如果你还没有安装`yarn`，从[这里](https://yarnpkg.com/en/docs/install)安装。

为了确保我们在同一页上，这些是本教程中使用的版本:

*   节点 12.12.0
*   纱线 1.19.1
*   木偶师 2.0.0

## 装置

要在您的项目中使用 Puppeteer，请在终端中运行以下命令:

```
$ yarn add puppeteer 
```

*注意:当你安装木偶师时，它会下载一个最新版本的 Chromium(～170 MB MAC OS，282MB Linux，280MB Win)，这个版本保证能与 API 一起工作。要跳过下载，请参见[环境变量](https://pptr.dev/#?product=Puppeteer&version=v1.18.1&show=api-environment-variables)。*

如果你不需要下载 Chromium，那么你可以安装`puppeteer-core`:

```
$ yarn add puppeteer-core 
```

是一个轻量级版本的木偶师，用于启动现有的浏览器安装或连接到远程浏览器。确保您安装的 puppeter-core 版本与您要连接的浏览器兼容。

*注:`puppeteer-core`仅从 1.7.0 版本发布。*

## 使用

Puppeteer 至少需要 Node v6.4.0，但我们将使用 async/await，它只在 Node v7.6.0 或更高版本中受支持，所以请确保将 Node.js 更新到最新版本以获得所有好处。

让我们深入一些使用木偶师的实际例子。在本教程中，我们将:

1.  使用木偶师生成 Unsplash 的截图
2.  使用木偶师创建黑客新闻的 PDF
3.  使用木偶师登录脸书

### 1.使用木偶师生成 Unsplash 的截图

用木偶师真的很容易做到。继续在项目的根目录下创建一个`screenshot.js`文件。然后粘贴以下代码:

```
const puppeteer = require('puppeteer')

const main = async () => {
  const browser = await puppeteer.launch()
  const page = await browser.newPage()
  await page.goto('https://unsplash.com')
  await page.screenshot({ path: 'unsplash.png' })

  await browser.close()
}

main() 
```

首先，我们需要`puppeteer`包。然后我们在它上面调用初始化实例的`launch`方法。这个方法是异步的，因为它返回一个`Promise`。所以我们用`await`为它获取了`browser`实例。

然后我们在上面调用`newPage`并转到 [Unsplash](https://unsplash.com) 并对其进行截图，并将截图保存为`unsplash.png`。

现在，通过键入以下命令在终端中运行上面的代码:

```
$ node screenshot 
```

![Unsplash - 800px x 600px resolution](img/473ff8addb569898a5111985fe427372.png)

现在，5-10 秒后，您将在项目中看到一个包含 Unsplash 截图的`unsplash.png`文件。请注意，视口设置为 800px x 600px，因为 Puppeteer 将此设置为初始页面大小，这定义了屏幕截图的大小。可以使用 [Page.setViewport()](https://pptr.dev/#?product=Puppeteer&version=v1.18.1&show=api-pagesetviewportviewport) 自定义页面大小。

让我们改变视口为 1920 像素 x 1080px 像素。在`goto`方法前插入以下代码:

```
await page.setViewport({
  width: 1920,
  height: 1080,
  deviceScaleFactor: 1,
}) 
```

现在继续，在`screenshot`方法中将文件名从`unsplash.png`改为`unsplash2.png`，如下所示:

```
await page.screenshot({ path: 'unsplash2.png' }) 
```

整个`screenshot.js`文件现在应该看起来像这样:

```
const puppeteer = require('puppeteer')

const main = async () => {
  const browser = await puppeteer.launch()
  const page = await browser.newPage()
  await page.setViewport({
    width: 1920,
    height: 1080,
    deviceScaleFactor: 1,
  })
  await page.goto('https://unsplash.com')
  await page.screenshot({ path: 'unsplash2.png' })

  await browser.close()
}

main() 
```

![Unsplash - 1920px x 1080px](img/c90ca1039d5c0a533744b3c2c663f1db.png)

### 2.使用木偶师创建黑客新闻的 PDF

现在创建一个名为`pdf.js`的文件，并将以下代码粘贴到其中:

```
const puppeteer = require('puppeteer')

const main = async () => {
  const browser = await puppeteer.launch()
  const page = await browser.newPage()
  await page.goto('https://news.ycombinator.com', { waitUntil: 'networkidle2' })
  await page.pdf({ path: 'hn.pdf', format: 'A4' })

  await browser.close()
}

main() 
```

我们只修改了`screenshot`代码中的两行。

首先，我们用[黑客新闻](https://news.ycombinator.com)替换了网址，然后添加了`networkidle2`:

```
await page.goto('https://news.ycombinator.com', { waitUntil: 'networkidle2' }) 
```

对于进行长时间轮询或任何其他附带活动的页面，如果至少 500 毫秒内没有超过两个网络连接，则认为导航已经完成，这就很方便了。

然后我们调用`pdf`方法来创建一个 PDf，并将其命名为`hn.pdf`，我们将其格式化为`A4`大小:

```
await page.pdf({ path: 'hn.pdf', format: 'A4' }) 
```

就是这样。我们现在可以运行该文件来生成一个 PDF 格式的黑客新闻。让我们继续在终端中运行以下命令:

```
$ node pdf 
```

这将在项目的根目录下生成一个 A4 大小的名为`hn.pdf`的 PDF 文件。

### 3.使用木偶师登录脸书

用以下代码创建一个名为`signin.js`的新文件:

```
const puppeteer = require('puppeteer')

const SECRET_EMAIL = 'example@gmail.com'
const SECRET_PASSWORD = 'secretpass123'

const main = async () => {
  const browser = await puppeteer.launch({
    headless: false,
  })
  const page = await browser.newPage()
  await page.goto('https://facebook.com', { waitUntil: 'networkidle2' })
  await page.waitForSelector('#login_form')
  await page.type('input#email', SECRET_EMAIL)
  await page.type('input#pass', SECRET_PASSWORD)
  await page.click('#loginbutton')
  // await browser.close()
}

main() 
```

我们已经创建了两个变量，`SECRET_EMAIL`和`SECRET_PASSWORD`，它们应该被你的电子邮件和脸书的密码所取代。

然后我们`launch`浏览器，并将`headless`模式设置为`false`，以启动 Chromium 浏览器的完整版本。

然后我们去[脸书](https://facebook.com)等待，直到所有东西都装好。

在脸书上，有一个可以通过 DevTools 访问的`#login_form`选择器。这个选择器包含登录表单，所以我们使用`waitForSelector`方法等待它。

然后我们必须键入我们的`email`和`password`，所以我们从 DevTools 获取选择器`input#email`和`input#pass`，并传入我们的`SECRET_EMAIL`和`SECRET_PASSWORD`。

之后，我们点击`#loginbutton`登录脸书。

最后一行被注释掉，这样我们可以看到输入`email`和`password`并点击登录按钮的整个过程。

通过在终端中键入以下命令来运行代码:

```
$ node signin 
```

这将启动一个完整的 Chromium 浏览器，然后登录到脸书。

## 结论

在本教程中，我们制作了一个项目，在指定的视口中创建任何给定页面的屏幕截图。我们还建立了一个项目，我们可以创建任何网站的 PDF 文件。然后，我们通过编程成功登录到脸书。

Puppeteer 最近发布了版本 2，这是一个很好的软件，它通过一个简单易用的 API 来自动化琐碎的任务。

你可以在它的[官网](https://pptr.dev)了解更多关于木偶戏的信息。文档非常好，有大量的例子，一切都有据可查。

现在继续前进，用木偶师自动化你日常生活中无聊的任务。

## 分享这篇文章