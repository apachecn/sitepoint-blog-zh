# 如何验证和测试您的移动网络设计

> 原文：<https://www.sitepoint.com/how-to-validate-test-your-designs-for-the-mobile-web/>

![](img/1195e8ab617b70b6f3b2cdaa4e822724.png "phones")

继我上一篇关于移动网络的[用户定位的文章之后，在这篇文章中，我们将着眼于移动验证和测试，以确保您的网站在智能手机和平板电脑上与在桌面上一样合规、无缺陷。验证和测试可能不是网页设计中最令人兴奋的领域，但它们和学习如何让东西看起来很棒一样重要。](https://www.sitepoint.com/user-targeting-for-the-mobile-web/)

### 确认

桌面浏览器可以相当宽容。这里或那里的一些放错位置的 HTML 标签，通常会被动态地修复，以便正确地呈现您的页面。然而，为了执行这种错误处理而内置到桌面浏览器中的“智能”相当于更多的代码，这意味着更大的安装和更强的处理能力。

另一方面，移动浏览器就不那么宽容了。在移动设备上运行的浏览器一般不会有 2 GHz 的处理器和 100MB 的磁盘空间。因此，您必须一次又一次地检查、验证、再检查您的标记。

移动网站的大部分检查和验证可以通过普通的桌面浏览器来完成。例如，如果您使用 XHTML 进行开发，您可以重用所有用于验证传统站点的工具:

*   W3C 验证器可以用来发现简单的标记错误——甚至有一个特定于移动设备的版本。
*   Ready.mobi 网站列出了一些额外的手机特定检查。

你会注意到，在这一点上，我们还没有讨论如何检查移动网站是否能在移动设备上正常工作。一旦你收到了几个验证者的绿灯，你就需要得到几个不同类型的手机来执行一些实际的测试。

### 测试

在台式电脑上用网络浏览器测试你的网站只能模拟移动体验。移动设备使用中有许多元素无法通过这种方式准确复制。例如，移动运营商可能会限制数据包的大小，使其小于您的预期，因此甚至不会发送您的网页或图像！此外，内容 mime 类型可能是浏览器之间的一个问题——您是否使用正确的文本/html 或应用程序/xml+xhtml 来提供页面？手机可以显示哪种图像格式？

由于内存和磁盘占用空间小，移动浏览器不如桌面浏览器健壮，所以最好的建议是测试，测试，测试！当然，并不是每个人都有能力在市场上的每部手机上进行测试，但还是有其他选择的:

#### 1.模拟器

有大量的[在线仿真器](http://mtld.mobi/emulator.php)和[离线仿真器](http://developer.openwave.com/dvl/tools_and_sdk/phone_simulator/)可以让你快速查看图像的上下文和总体布局，但它们不是真正的设备，因此它们有自己的怪癖和不同之处。这些工具可以作为找到常见问题的第一步。此外，它们可以免费使用，所以没有理由不在模拟器中查看，但你不能称在模拟器上查看的站点为“测试过的”。

#### 2.出租时间

租用时间是另一种选择。有些服务可以让你在多部手机上实时上传或观看内容。您可以远程控制不同手机的功能。这项服务确实要花钱，但仍比购买大量不同的手机便宜。对于一个基本的移动网站，这项服务可能是不需要的。

#### 3.买几部手机

购买一个小的子集或代表性的手机是可能的。如果你计划做更多的移动开发，预先花点钱真的会有帮助。你可能需要购买 5 或 6 部代表主要品牌和类型的手机。

你需要某种 Windows 智能手机——我们在 HTC Mogul PPS-6800 上使用 Windows CE 上的 Internet Explorer 进行了测试，它给出了所有 Windows Mobile 设备的代表性覆盖范围。你还需要一部诺基亚型号的手机。我们在运行 Symbian Series 6 操作系统、内置服务和可选 Opera 浏览器的诺基亚 6600 上进行测试。你需要一部索尼爱立信手机。我们用的型号是一款 T630，涵盖了索尼的互联网服务。最后，你应该测试在你的观众中最受欢迎的模型。你不需要这些确切的手机，但这些类型的设备将让你很快看到手机浏览器的细微差别，并涵盖大多数潜在客户。

#### 4.问问你的朋友

最后，你可以简单地问问你的朋友。我们都知道，在 east 5 或 6 个同事或同事会很乐意把他们的电话借给我们几分钟。一旦你用在线模拟器和验证器测试了你的网站，在借来的手机上测试它。预测试应该已经解决了除了每部手机特有的问题之外的所有问题。记笔记，改正错误，一遍又一遍地测试——一遍又一遍！—直到你满意为止。

根据你的预算，其中一个选项会适合你。花一点时间和金钱真的可以走很长的路——特别是如果这是你的第一个移动网站。获得正确的测试套件将允许您发现常见问题，同时帮助您构建一个工作 HTML、CSS 和 JavaScript 代码库，这些代码已知是经过测试的，并且可以在未来的项目中重用。

您使用了哪些资源和服务来进行移动站点验证和测试？在评论中分享吧！

## 分享这篇文章