# 在 Unity 中构建一个道奇游戏克隆

> 原文：<https://www.sitepoint.com/building-a-dodger-game-clone-in-unity/>

在本教程中，我们将在 Unity 中构建一个简单的 Dodger 游戏。要查看和播放最终结果，请前往[这里](http://quarlwithcode.itch.io/dodger-tutorial-part-1)。

![Dodger clone screenshot](img/8a368740b31d15c3972d055b880cd1be.png)

## 分析游戏

Dodger 是一个简单的小游戏，不需要太多的逻辑来编程，并且几乎可以无限地定制。道奇只有几个关键的游戏元素:

1.  玩家输入
2.  玩家/敌人边界
3.  随机位置生成
4.  冲突
5.  失去条件

使用这五个元素，我们将创建一个简单的道奇克隆。本教程假设您已经安装了 Unity。

## 添加资产

创建一个新的 Unity2D 项目，并将其命名为“Dodger”或“DodgerExample”。

![Project start screen](img/5759e05e14677a521e15476fc76108cb.png)

首先，您需要从 GitHub 页面下载[图像(或者右键单击并将图像另存为…)。](https://github.com/quarlwithcode/dodgertutorial/tree/master/Dodger/Assets/Sprites)

![Player and enemy sprites](img/088c5ff1dd74a4603c9fd2aa3d383b83.png) ![enter image description here](img/3cea6e3a93d6682c4405f8f2284d8d28.png)

要在 Unity 中创建文件夹，您需要右键单击资产窗格并选择创建->文件夹。将新文件夹命名为`Images`，并创建另外两个名为`Prefabs`和`Scripts`的文件夹。

![Creating a folder in Unity](img/dc162eed44339e92b1b0172eb9b17eff.png)

创建文件夹后，您可以将从 Github 页面下载的图像拖到 images 文件夹中。选择图像文件夹中的两个图像，并将它们的单位像素值设置为 32，然后单击“应用”。

## 创建播放器

要添加玩家输入，我们首先要创建一个玩家游戏对象。您可以通过将绿色笑脸的图像拖到层级窗格中来创建玩家游戏对象。将游戏对象的名称设置为 Player，并将其 Y 位置设置为-3.5。

![Creating a player](img/dde00fe0f7f7ba90710fe0129219f596.png)

## 修改我们的相机

如果我们像这样离开相机，我们的精灵将不得不使用十进制数，以便像我们希望的那样留在屏幕内。Unity 允许我们改变主摄像头的大小及其背景颜色。选择相机并点按背景颜色。将颜色设置为漂亮的蓝色(R: 0 G : 223 B: 255)。

![Setting colors for the background](img/5bf43d68887d9d7ef34854b8e1470c70.png)

选择主摄像头的尺寸值，设置为 4.4。最后，在游戏面板中点击自由纵横比，并将纵横比设置为 4:3。

![Changing the aspect ratio](img/16b6f1d88cb85b992f8d0007971e0cf0.png)

## 获取玩家输入

我们将通过获取玩家输入来开始我们的编程。为了获得玩家输入，我们需要创建一个新的 C#脚本，并将其命名为玩家控制器。

![Creating a new C# script](img/8281cd8e03a9b5db580104ec14249149.png)

双击脚本，在 IDE 中打开它。IDE 打开后，将以下代码添加到脚本中:

```
 //Speed of the Player  public  float speed =  10.0F;  //bounds of player  public  float leftBound =  -5F;  public  float rightBound =  5F;  public  float upBound =  3.5F;  public  float downBound =  -3.5F;  // Use this for initialization  void  Start  ()  {  }  // Update is called once per frame  void  Update  ()  {  //Horizontal Speed  float movementSpeedX =  Time.deltaTime *  Input.GetAxis("Horizontal")  * speed;  //Vertical Speed  float movementSpeedY =  Time.deltaTime *  Input.GetAxis("Vertical")  * speed;  //Player Movement transform.Translate(movementSpeedX, movementSpeedY,  0);  //creates bounds around player  if(transform.position.x > rightBound){ transform.position =  new  Vector3(rightBound,transform.position.y,0);  }  else  if(transform.position.x < leftBound){ transform.position =  new  Vector3(leftBound,transform.position.y,0);  }  if(transform.position.y > upBound){ transform.position =  new  Vector3(transform.position.x,upBound,0);  }  else  if(transform.position.y < downBound){ transform.position =  new  Vector3(transform.position.x,downBound,0);  }  }
```

*注意:边界的实际值可能会有所不同。如果是这种情况，将适当的值添加到绑定变量*

通过将玩家控制器脚本从“资源”窗格拖动到玩家游戏对象上，将其添加到层次中的玩家游戏对象。如果你运行游戏并试图用箭头键控制玩家，你会看到玩家在屏幕上移动。

![Fine tuning the player object](img/ddd7c105a21ae97a30f5a31b02e03c0c.png)

## 敌人

接下来我们需要创造敌人。为了创造敌人，你需要将愤怒的绿色方块拖到层级窗格中。继续把游戏物体命名为敌人。点击游戏对象名称下显示*未标记*的地方，然后点击“添加标记”。创建一个名为“敌人”的标签，并将这个标签赋予敌人的游戏对象。选择玩家游戏对象，并给它加上标签“玩家”。

![The enemy is untagged](img/d8771a9c51966a5387a21d385f6518ba.png)

![Tagging the enemy](img/7f97c5d4ecc3652ff3f94e248363a1e6.png)

现在我们已经有了标签集，可以区分玩家和敌人，我们可以开始编码敌人的行为了。在“资源”窗格中打开“脚本”文件夹，并创建一个名为“EnemyController”的新 C#脚本。将 EnemyController 脚本添加到敌人游戏对象中。在 IDE 中打开该脚本，并将其输入到您的脚本中:

```
 //speed of Enemy  public  float speed =  8F;  // Use this for initialization  void  Start  ()  {  }  // Update is called once per frame  void  Update  ()  {  //Movement of Enemy transform.Translate(0,  -speed *  Time.deltaTime,  0);  }
```

## 添加碰撞

在 Unity 里面选择玩家游戏对象。在玩家游戏对象的检查器窗格中，单击添加组件按钮，搜索“圆形碰撞器 2D”，并将其添加到玩家游戏对象中。再次做同样的事情，除了搜索“刚体 2D”并将其添加到玩家游戏对象中。在刚体 2D 设置如下图所示的值。

![Adding a collider](img/d53acb1c463335ac02b070fb017e9b75.png) ![enter image description here](img/f3bbf57e988089ab593f3a8682376861.png)

接下来选择敌人的游戏对象，在它的检查面板中点击添加组件按钮。搜索“箱子对撞机 2D”，并将其添加到敌人的游戏对象。检查敌人游戏对象的盒子碰撞器 2D 的菜单中的“触发”。

在 ide 中的更新方法类型下打开 EnemyController 脚本:

```
void  OnTriggerEnter2D(Collider2D other){  //Checks if other gameobject has a Tag of Player  if(other.gameObject.tag ==  "Player"){  //Pauses gameplay  Time.timeScale =  0;  }  }
```

## 随机繁殖敌人

在 Unity 中，将敌人的游戏对象拖到你的资产窗格中的预设文件夹中。删除等级内的敌人，因为我们将随机生成所有繁殖的敌人的位置。右键单击层次窗格内部，并选择“创建空白”。将空游戏对象命名为“EnemySpawner”，并将其 Y 位置设置为 6。在 scripts 文件夹中创建一个名为“EnemySpawnerController”的新 C#脚本，并在 ide 中打开它。

在 EnemySpawnerController 脚本中键入:

```
 //rate at which enemies spawn  public  float spawnRate =  1;  //enemy prefab  public  GameObject enemy;  //bounds of spawner  public  float leftBound =  -5F;  public  float rightBound =  5F;  // Use this for initialization  void  Start  ()  {  //call SpawnEnemy based on spawnRate  InvokeRepeating("SpawnEnemy",  1, spawnRate);  }  // Update is called once per frame  void  Update  ()  {  }  void  SpawnEnemy(){  //a clone of the enemy prefab  GameObject enemyClone;  //spawns enemyClone at this location and rotation enemyClone =  Instantiate(enemy,  this.transform.position,  this.transform.rotation)  as  GameObject;  //randomly moves spawner along x axis  float x =  Random.Range(leftBound, rightBound); transform.position =  new  Vector3(x,this.transform.position.y,  0);  }
```

在 Unity 中，将敌人预设(从预设文件夹中)拖动到敌人 Spawner 控制器菜单下的敌人值中。在层级窗格中选择敌人并按下 COMMAND+D 键(或者右击游戏对象并选择复制)来复制游戏对象。将新的 enemy spowner 游戏对象命名为“EnemySpawner 2”，并将其 X 位置设置为 2。从层次窗格中选择 EnemySpawner 游戏对象，并将其 X 位置设置为 2。

## 添加基本用户界面

在层次窗格内，右键单击并选择“创建空”。将新游戏对象的名称设置为“UIManager”。在 scripts 文件夹中创建一个新的 C#脚本，并将其命名为“UIManager”。在 IDE 中打开脚本，并键入:

```
// Use this for initialization  void  Start  ()  {  Time.timeScale =  1;  }  //Reloads the Level  public  void  Reload(){  Application.LoadLevel(Application.loadedLevel);  }
```

在 Unity 的顶部菜单中，选择游戏对象-> UI ->按钮。将新按钮命名为“刷新按钮”，并将其 X 位置设置为 202，Y 位置设置为 196。点击层级窗格中刷新按钮旁边的下拉箭头，选择文本游戏对象。将游戏对象文本值设置为“刷新”。从“hierarchy”窗格中选择“Refresh”按钮，并在其按钮菜单中单击“On Click”()菜单末尾的加号。将单击方法的游戏对象设置为 UIManager 游戏对象，并将方法设置为 UIManager -> Reload。

![Adding the refresh game button](img/674e0473f445415cb541e272cc6bb4d1.png)

## 收尾工作

在“资源”窗格中创建一个“场景”文件夹。将我们当前的场景保存为“MainLevel ”,并保存到场景文件夹中。从顶部菜单中选择“文件”->“构建设置”,将 MainLevel 场景从“资源”窗格拖动到“构建设置”菜单中，或者点击菜单中的“添加当前”。最后，在 PC、Mac 或 Web 上构建游戏，享受游戏的乐趣。

## 结论

这个简短但切中要点的教程详细介绍了在 Unity 中创建流行的 Dodger 游戏的克隆版的步骤。希望你喜欢它，并渴望更多的 Unity 内容——我们保证，它很快就会出现！反馈？评论？把它们留在下面！

你可以在 [GitHub](https://github.com/sitepoint-editors/dodgertutorial) 下载完整的项目。

## 分享这篇文章