# 增强现实和 Meta 入门

> 原文：<https://www.sitepoint.com/getting-started-with-augmented-reality-and-meta/>

在上周备受期待的 ted 演讲展示了他们的下一代增强现实耳机 Meta 2 之后，Meta 最近一直在新闻中出现。这是我第一篇关于在 Unity 中开发元增强现实耳机的文章的绝佳时机！如果你有一个原始的元耳机，并且你一直在推迟构建 AR 应用程序——现在是一个很好的时间来掸掉耳机上的灰尘，做一些开发！

在本教程中，我们将着眼于创建一个增强现实蝴蝶体验。我们将从基础开始，并以一只简单的蝴蝶结束，我们可以抓住它并在我们的场景中移动。

## 你需要什么

为了完成本教程，您需要以下内容:

*   **Meta 1 开发者套件—**这些已经卖完了，所以如果你不够幸运没有一个，你需要找到一个拥有它的朋友，或者等待 Meta 2！
*   **Unity 5.2.1 (32 位)或 Unity 5.2.2 (64 位)–**对 Unity 4 的 Meta 支持即将结束，所以最好在这个时候升级到 Unity 5！我推荐通过 Meta 的链接下载，我们将在下面介绍。
*   **Windows 8.1 或 Windows 10—**遗憾的是，Mac OSX 不受支持，Windows 7 支持也将被淘汰。

## 下载 Unity 和 Meta SDK

获得 Unity 和 Meta SDK 的正确版本的最安全的方法是去 Meta Dev Center。从这里，您可以下载最新的 Meta SDK:

![Downloading the latest Meta SDK](img/66de4ca803119fbb5e2dba1372c640bb.png)

一旦下载完毕(或者如果你的网速比我快得多，可以同时下载！)，向下滚动下载 64 位或 32 位 Unity，具体取决于您的操作系统:

![Downloading either the 64 bit or 32 bit Unity](img/1bad708a6a5987a1d3656ccedde6ec11.png)

一旦 Unity 下载完毕，运行 Meta SDK 和 Unity 的安装过程。两者都很简单，所以我不会浪费时间在这里讨论它们！一旦两者都安装了，打开 Unity，我们就可以开始了！

## 创建我们的第一个项目

当我们第一次打开 Unity 时，欢迎屏幕的右上角有一个启动新项目的选项。单击“新建”开始新项目:

![Creating a new project by clicking New](img/f4373f477e22a14a083bc642b23a84cd.png)

然后，我们命名我们的项目(你可以随意命名你的项目，我们用“漂亮的蝴蝶”)并选择你想把它保存在你电脑上的什么地方。确保你在左下方选择了 3D！准备好后，点击“创建项目”:

![Setting up our Unity project settings](img/679d9d3899243f5b46a7bc0cb897147a.png)

在做任何其他事情之前，命名并保存您的初始场景是一个好主意。为此，请转到*文件>将场景另存为…* 。

![Saving our initial scene, File > Save Scene As](img/f04817ab8ecfd2574408958d2c072f36.png)

我们在项目的“资产”文件夹中为我们的场景添加了一个名为“场景”的新文件夹:

![Creating a new folder for our scene](img/020b9920151db60858b3332053c1aeb5.png)

然后将我们的场景命名为“蝴蝶场景”——你可以随意命名你的场景！

![Naming our scene](img/3d72866a84e391b8206b7becb24603e5.png)

## 导入元 SDK

既然我们已经保存并准备好了一个场景，是时候导入我们之前下载的 Meta SDK 了。为此，我们单击*资产>导入包>自定义包……*:

![Importing a custom package via Assets, Import package, Custom Package](img/baa761cf96c55f483d5ae74da9412bb3.png)

浏览文件系统，找到 Meta SDK。对我来说，这是在*C:/Program Files(x86)/Meta/Meta SDK*中。在此文件夹中，我们选择“元”Unity 包:

![Selecting the Meta unity package](img/105ac3d2964695f0373f3649031aa6ab.png)

加载一段时间后，会出现一个窗口，显示找到的所有 Meta SDK 资产。确保所有内容都已选中，然后单击“导入”:

![Importing Meta package ensuring everything is checked](img/1b333b62918fa57531a7bc77460ed5a8.png)

如果 Unity 弹出来说“这个项目包含使用过时 API 的脚本和/或程序集”，那也没关系。只需点击“我做了备份。去吧！”

![Yes, I made a backup](img/f01ab7f1be298e598f4bd7bd31aa1cc2.png)

在我们的项目中，我们现在应该看到两个新的文件夹，“Meta”和“MetaPackage”:

![Meta assets now visible](img/2821736e78ce2b74b4b96f86486dd116.png)

## 将 megaworld 添加到我们的项目中

在我们的新资产中，我们有我们的“元世界”预设，它将提供我们需要的所有元相机和交互功能。如果你是 Unity 的新手——预置基本上就像模板对象，可以在多个项目和场景中重用以共享功能。

将它添加到场景中最简单的方法是在“项目”窗口中搜索。输入“元世界”，它应该会过滤我们的资产，向我们展示我们想要的东西。将“元世界”预设拖到你的项目层级中(你现在已经有了一个“主摄像机”和“平行光”):

![Dragging in MetaWorld Prefab](img/f68969a6ce385edcd44c29e28379b410.png)

“元世界”预设的实际位置是*Meta/Meta core/Resources/Prefabs/Meta world . prefab*。

“元世界”对象是我们的场景增强现实视图，代表我们的元耳机。确保它位于{0，0，0}处，并且没有旋转。这是我们的摄像机视图，所以我们可以删除现有的“主摄像机”。我们也可以删除“平行光”:

![MetaWorld positioning in our scene](img/6b08ecc6151dee1ce29f623013d2f343.png)

## 添加我们的蝴蝶

组织我们场景的最干净的方法是创建一个新的空的游戏对象，它将包含我们其余的元素。对于刚接触 Unity 的人来说，这为我们的对象创建了一个漂亮而简单的容器。为此，请转到*游戏对象>创建空的*。

![Creating an empty game object](img/447b679a875b8dc436838de56fd2795a.png)

我们将这个对象重命名为“Butterfly App”，并确保它定位在{0，0，0}处，没有旋转。这一点很重要，因为放置在其中的所有对象都将使用父对象的位置作为基础。

![Rename it to Butterfly App](img/fd4dbb8ee33f8798a05106426d80b55b.png)

为了得到一个漂亮的蝴蝶模型，我们将访问 Unity 资产商店。如果你不是蝴蝶迷(不太可能，但你永远不知道！)，你可以下载几乎所有的模型。实验去吧！

我们将单击“场景”和“游戏”选项卡旁边的“资产商店”选项卡，然后在搜索框中键入“butterfly ”:

![Searching for a butterfly](img/bd48882de2e6250082cb352184d25c5f.png)

如果我们稍微滚动一下，我们会发现一个非常适合我们演示的免费软件:

![The free butterfly download](img/37bbfbdf270622dc4d673044653c9a58.png)

点击“下载”开始为我们的蝴蝶下载文件:

![Butterfly download](img/882ac153119ec452c0fb8bb13e7fd18e.png)

在出现的导入窗口中，确保所有内容都已选中(就像我们在 Meta SDK 中导入时所做的那样)，然后单击“import”:

![Importing butterfly](img/cc505c13486e0e035558b97ed75ef57d.png)

如果我们回到“Assets”文件夹的根目录，我们现在可以看到一个包含新 butterfly 资产的新文件夹！

![Our new butterfly assets](img/651625d7d5426cff92461582fff0ad9e.png)

在这个新文件夹中，我们导航到*资产> GruffysAnimatedButterfly >预设*。我们现在将“蝴蝶 2”预设拖到我们的“蝴蝶应用”空游戏对象中。

![Putting butterfly into scene](img/3c2db3703c4103cb478ab684a8a08715.png)

如果你愿意，你可以调整蝴蝶的位置。我发现的最佳位置是让 Z 位置远离相机 0.4，但是 X 和 Y 保持在 0。我将蝴蝶在 Y 轴上旋转了 150 度，并在所有轴上缩放了 0.05 度。比例可以是你想要的任何东西，如果你想要一个更大的蝴蝶，请随意放大它(但是如果你放大得太大，你可能需要在 Z 轴上再向后移动一点才能正确地看到它！).

![Butterfly positioning](img/5d71de957220a6b56e0923c84fe19751.png)

如果你还没有，去掉 Unity 场景中的“方向灯”——我们不需要它。

## 赋予我们的蝴蝶元能力

为了能够在场景中捡起并移动我们的蝴蝶，我们需要蝴蝶本身成为一个“变形体”。这是 Meta SDK 附带的一个组件，很容易添加到 Unity 的任何对象中。

要将任何 Unity 对象转换为支持元体的对象，请单击该对象(如本例中的“butterfly2”)，单击右侧的“添加组件”并搜索“元体”。出现“元几何体”时，单击它:

![Adding MetaBody](img/de553a37f018b3f6b0b85a06800c7194.png)

在现在为我们的对象显示的设置中，勾选以下选项:

*   **可抓取并移动**——这使我们能够在场景中捡起并移动我们的蝴蝶。
*   **箭头**–这在元界面上提供了一个方向箭头，当对象不在屏幕上时，它指向对象所在的位置。这对于用户可以在场景中移动的物体非常有用——因为人们倾向于放下物体而看不到它们去了哪里！箭头节省了大量的时间和混乱！

如果我们现在运行应用程序，我们将很难看到蝴蝶，可能会有点困惑(我知道我最初是这样的)。这并不是因为我们在 Meta SDK 或 Unity 中做错了什么。如果你一直一步一步地跟着做，你会处于同样的情况——这是由于我们的蝴蝶上默认有一个着色器，使它的翅膀很好地融入场景。

为了使它对我们的元耳机可见，我们需要改变这个着色器。为此，单击以展开项目层次中的“butterfly2”游戏对象，并选择“Butterfly Mesh”。选中此项后，单击右侧的着色器下拉列表:

![Butterfly shader dropdown](img/d73e68dfd371f2ae061577a2883a6d80.png)

在出现的菜单中，进入*移动>粒子*并选择“添加剂”。这给了我们一个不同的着色器，使我们的蝴蝶对象在我们的场景中闪耀！更好的是，它实际上在增强现实中也给了它一种几乎全息风格的感觉。

![Changing the butterfly shader](img/75fa719c606099c8932d9724e1de4d87.png)

## 在活动

这样，我们保存了我们的场景，现在准备测试它！连接您的元耳机，并按下现场的播放按钮。

![Click play](img/4ca1b138d64b32931046d381f501731d.png)

如果你没有像你希望的那样看到一只蝴蝶，你可能需要做一些事情:

*   没看见你面前的蝴蝶？您可能需要点击 F4 重新调整您的视图，或者跟随箭头！
*   **没有在耳机中看到两张并排的图像？**确保您点击了 Meta developer kit 控制框左下方的 3D 按钮。
*   **在 Unity 的预览中没有看到两张并排的图片？**按 F2 键切换到并排模式。
*   **在耳机中看到非常小的图像？**按 F3 切换到校正后的视图。

如果你成功地显示了它，你应该可以看到一只蝴蝶在你的场景中漂浮！你可以用你自己的手抓住它并移动它。抓取现实中并不存在的物体可能需要一点时间来适应:

*   确保你张开的手放在眼镜前面，直到你看到一个黄色圆点。
*   握紧拳头，圆圈会变成绿色。
*   移动你的拳头来移动场景中的蝴蝶。
*   再次张开你的拳头，让它自由！

这是我的蝴蝶在并排视图中的样子(通过耳机观看时，此视图看起来是 3D 的):

![In action with double vision](img/39ae5f8e4db78434764abbdce804c1ea.png)

这是一个只有一个屏幕的视图，显示了佩戴耳机的人将会看到的内容:

![In action with single view](img/3f16bf2734704141c2ce405e04c09dc5.png)

## 结论

增强现实是一个非常迷人的领域，有很多机会！如果你没有 Meta 耳机，并渴望参与其中，Meta 2 耳机的更多细节将于太平洋标准时间 3 月 2 日上午 9 点在 Meta 网站上发布。这是我现在最喜欢的 AR 耳机开发选择！这里希望当他们宣布更多细节的时候会有足够的开发者工具包。新耳机看起来比 Meta 1 有更大的视野和更高的分辨率。我热切地希望自己也能得到一双！

你是当前在这个平台上开发的 Meta 先锋吗？或者是一个现在又有兴趣拿出耳机做一些开发的先锋？你已经有原型了吗？读完这篇文章后，你是否有了新的想法？我很想听听你在忙什么！请在下面的评论中告诉我，或者在 Twitter 上通过 [@thatpatrickguy](https://www.twitter.com/thatpatrickguy) 与我联系。

## 分享这篇文章