# 使用 Unity 5 开发 3D 游戏

> 原文：<https://www.sitepoint.com/developing-3d-games-with-unity-5/>

如果有比玩游戏更酷的事情，那就是制作游戏。

无论你是在开发《愤怒的小鸟》这样的 2D 游戏，还是《反恐精英》这样的 3D 游戏，你都可以通过在 Play Store、App Store 或网络上发布它们来赚钱。

## 如何开始构建游戏

有许多用于创建游戏的工具，在本文中，我们将了解非常受欢迎的 [Unity 5](https://unity3d.com/) ，这是一个游戏平台，可以为各种平台(如 OS X、Linux、Windows、Unity Webplayer、Android、iOS、BlackBerry 10、Windows Phone、Tizen、WebGL、PlayStation、Wii U、任天堂、Xbox、Android TV、三星智能电视、Oculus Rift、HTC Vive 和 Gear VR)提供 2D 和 3D 游戏开发。

在这篇文章中，我们将看看如何建立一个充满障碍，敌人，机动和射击的 3D 宇宙飞船游戏。在我们完成游戏的构建后，它看起来会像这样:

![Spaceship Game Final](img/9ac04a0a73c3a40dffb561f830797f7c.png)

我们将介绍 Unity3D 个人版的安装过程，开发一个采用 3D 建模、Unity 资源丰富的资产存储、脚本(JavaScript)和 Unity 强大的物理引擎的 3D 游戏。

学习本教程没有先决条件，尽管 JavaScript 的基础知识会有所帮助。

## 装置

安装 Unity 就像从[这里](http://docs.unity3d.com/Manual/InstallingUnity.html)下载各自平台的安装程序一样简单。

下载管理器是一个小文件，提示您选择可以下载的组件。这些下载通常很大(以千兆字节为单位)，所以在安装过程完成之前，您可能想坐下来或通读本教程。

安装完成后，打开 Unity 5，在创建项目选项下选择**新建项目**。输入项目名称并选择 3D 选项导入 3D 游戏的资源包，最后点击**创建项目**。

![Create Project](img/26dbfa2c1d376ea48c85410454d802cf.png)

## 了解 3D 模型和相机

如果你成功地完成了安装部分的所有步骤，你将会得到一个创建游戏的 Unity 5 界面。因为我们正在构建一个 3D 游戏，我们需要在游戏中使用 3D 物体。您可以通过选择**游戏对象> 3D 对象**将 3D 模型插入游戏。我在场景中插入了一个立方体。您的屏幕现在应该看起来像这样:

![3D Models](img/6d3e44b9fd16a21290fc2da2f68823ac.png)

点击立方体后，你会看到许多与你选择的 3D 对象相关的选项——如**变换**、**网格**、**箱式碰撞器**和**网格渲染器**。

这些 3D 模型使用所谓的物理引擎与游戏互动。你可以通过点击并选择**组件>物理>刚体**将物理规则应用于任何 3D 对象。

这将导致 Unity 的游戏引擎将该对象视为真实世界的**刚体**，并对其应用各种真实世界的交互，如碰撞和重力。

Unity 使用摄像机来渲染游戏空间中的物体。当你进入**游戏**模式查看你的游戏场地时，你将能够从摄像机的视角查看它——这也是游戏玩家将会看到的。

下图显示了摄像机的视野和摄像机预览，可通过单击摄像机对象打开:

![Camera](img/a15a53193e0c864e299bd39c396eb609.png)

尝试一下 3D 模型和相机是值得的，因为你对它们越熟悉，你就越能理解接下来的步骤。

## 使用 Unity 资产商店

由于我们刚刚开始，初学者很难开发非常复杂的玩家以及游戏的场景。Unity 的[资产商店](https://www.assetstore.unity3d.com)来帮助我们解决这个问题。

Unity 的资产商店是各种模型、材料、声音、动画等的集合。资产可以是多种类型，许多模型可能会附带动画和声音，以使您的工作更容易。

就像任何其他商店一样，有免费和付费资产可用于构建游戏。既然我们正在构建一个宇宙飞船游戏，让我们为我们的游戏找一个看起来很酷的宇宙飞船。

你可以通过点击**游戏**标签旁边的**资产商店**标签来打开资产商店。我为游戏选择了一艘宇宙飞船，我的屏幕看起来是这样的:

![Asset Store](img/df04a5abf446b17762c067c9a5fe06df.png)

您也可以点击此处的下载该资产集。现在点击 **Import** 将这个资产集导入到您的项目中。你可以通过点击 **omega_fighter_01 >模型**并将 **omega_fighter** 拖到屏幕左侧的层级窗格中来将飞船带入游戏。你的模型现在应该在游戏区了。

你现在应该通过点击飞船并在右窗格的**变换**菜单中输入各自的值，将飞船重新定位在原点`(0, 0, 0)`。您还需要使用相同的技术重新定位相机。

对我来说,`(25, 0, 0)`旋转的`(0, 0.8, -1.5)`效果很好。点击**游戏**标签进入游戏模式，并将刚体角色添加到飞船中。不要忘记去掉右窗格中**刚体**选项下**使用重力**选项的勾号(你不会想让你的飞船从天上掉下来吧？).您的屏幕现在应该看起来像这样:

![Game View](img/ee5f4e8f9c8af851cba3d79b282c76a6.png)

## 构建游戏环境

我们通过设置一些障碍来构造一个简单的游戏设定。借助**组件> 3D 对象>平面**选项放置一个地面并添加一些柱子。

你可以开发一个迷宫，你的飞船必须避开所有的障碍，必须到达尽头的墙，必须触摸它以避免被击中。给它们都添加刚体组件。这是我的游戏场地俯视图:

![Top View](img/78843d60550912e0b98acebee5c123aa.png)

## 向对象添加行为

你的游戏现在需要逻辑来运行。你必须编写飞船如何移动，飞船碰撞时会发生什么等等的程序。

### 摄像机控制器

你必须设定你的主摄像机随着你的玩家移动，否则一旦你的飞船离开摄像机的视野，你将无法跟踪它。

从菜单栏中选择**资产>创建> Javascript** 来创建一个 Javascript 文件。现在点击**主摄像机**并从右窗格底部选择**添加组件>脚本>摄像机控制器**按钮。

```
#pragma strict

// variable offset of the type Vector3 
var offset : Vector3;

// variable player of the type GameObject
var player : GameObject;

function Start () {
    offset = transform.position - player.transform.position;
}

function LateUpdate () {
    transform.position = player.transform.position + offset;
}
```

上面的代码将计算相机对象和游戏对象的位置之间的差异，并将根据玩家的位置更新相机的位置(使用`LateUpdate()`函数)。

现在，向 Player 对象添加一个脚本。

```
#pragma strict
import UnityEngine.UI;

var loseText : Text;
var winText : Text;
var rb : Rigidbody;
var speed : float;
var tilt : float;

// initialising variables
function Start () {
    rb = GetComponent.<Rigidbody> ();
    loseText.text = "";
    winText.text = "";
}

function Update () {

  function FixedUpdate () {

    // take input from keyboard
    var moveHorizontal : float = Input.GetAxis ("Horizontal");
    var moveVertical : float = Input.GetAxis ("Vertical");

    // map the movement with Vector3
    var movement : Vector3 = new Vector3 (moveHorizontal, 0.0f, moveVertical);

    // add force to the object
    rb.AddForce (movement * speed);

    // add rotation to the object
    rb.rotation = Quaternion.Euler (0.0f, 0.0f, rb.velocity.x * -tilt);
}

// works when two objects collide with each other
function OnTriggerEnter(other : Collider) {
    if (other.tag == "Obstacles") {
        Destroy(gameObject);
        Destroy(other.gameObject);
        loseText.text = "You Lose!"; 
    }
    if (other.tag == "Win") {
        Destroy(gameObject);
        Destroy(other.gameObject);
        winText.text = "You Win!";
    }
}
```

这里我们导入了`UnityEngine.UI`来导入数据类型`Text`，它存在于 Unity 的 UI 工具包中。上面的代码用箭头键(使用`Input.GetAxis`函数)映射飞船(玩家对象)的运动，并使用`AddForce`函数移动它。

当宇宙飞船向左或向右移动时，它也会使宇宙飞船倾斜。`Quaternion.Euler`函数用于根据作用在刚体上的力来旋转飞船。`rotation`和`AddForce`方法都利用 Vector3 对象来获取移动方向的信息。

我们已经引入了逻辑，如果玩家触摸一个带有标签`Obstacles`的物体，`Destroy`功能会摧毁飞船和与之碰撞的物体，然后<q>你就输了！</q>显示文本。如果它接触到带有标签`Win`的物体，它会显示<q>你赢了！</q>。后一个物体是迷宫的最后一道墙。

现在，回到游戏窗口，你会注意到脚本已经被附加到各自的对象上。

你现在需要创建文本对象，这可以通过选择**游戏对象> UI >文本**来实现。相应地定位文本。现在点击**玩家对象**，看看脚本部分的设置。

应该有类似**输文本**和**赢文本**的选项。通过将右游戏对象从左窗格中的层级结构下拖动到空白区域，为空白区域分配适当的对象。设置速度`8.5`和倾斜度`4`对我来说效果很好。类似地，向相机对象添加适当的选项。您现在可以通过点击窗口顶部(中间)的 **Play** 图标来测试游戏。

## 编译和构建独立的

你全新的宇宙飞船游戏现在已经完成了。点击**文件>保存场景为…** 。现在选择**文件>构建设置**。选择你的操作系统并点击**构建**按钮。您的屏幕应该是这样的:

![Building Standalone](img/6dcd3beac7d4753d7543434bad63fea0.png)

这将为您的操作系统创建一个独立的文件。你的游戏现在可以开始玩和发行了。这是游戏的视频:

[https://www.youtube.com/embed/yLbk5Msff1Q?rel=0](https://www.youtube.com/embed/yLbk5Msff1Q?rel=0)

正如你在视频结尾看到的，我在游戏中放了一架巨大的直升机——来自资产商店——并给它添加了`Win`标签。这样，如果我的飞船到达了直升机，游戏就结束了——你是赢家！

## 结论

Unity 5 为感兴趣的人提供了一个开发 2D 和 3D 游戏的绝佳机会。正如我们所见，游戏开发工作流程——包括脚本——非常简单，您可以使用相同的工作流程轻松开发不同复杂程度的游戏。

你可以发行和销售的游戏的一个例子是一个宇宙飞船游戏，在这个游戏中，敌人向你开火，你向他们还击。游戏可能由几个级别或阶段组成。

为 Unity 开发职业游戏的学习曲线不是很陡。Unity 资产商店是为您的游戏寻找有趣资产的绝佳资源，因此您不必从头开始开发新游戏。

*你在 Unity 的帮助下开发过什么游戏吗？你最喜欢的游戏引擎是什么？*

## 分享这篇文章