# 在 Unity 中构建 Pong 克隆:UI 和游戏性

> 原文：<https://www.sitepoint.com/building-a-pong-clone-in-unity-ui-and-gameplay/>

在本教程的第 1 部分——[复古革命:在 Unity](https://www.sitepoint.com/retro-revolution-building-a-pong-clone-in-unity/) 中构建 Pong 克隆——我们用基本的人工智能(AI)和几乎没有用户界面(UI)创建了一个 Pong 克隆。

在本教程中，我们将建立在我们以前创建的克隆体的基础上，并对其进行更新，使其看起来与经典的 Pong 相同，具有基本的 UI，并改进了游戏性。

![Pong](img/10a45bf492ddfa1d19e97cc30cc101e6.png)

让我们直接进入主题，但是首先，如果你想继续的话，请浏览原始教程，或者从 GitHub 下载结果项目。(您也可以查看[工作演示](http://quarlwithcode.itch.io/pong-tutorial-part-1-game)。或者，如果你愿意，玩一下[改进版游戏](http://quarlwithcode.itch.io/pong-tutorial-2-game)，这就是本文的成果。

## 设计游戏

传统的 Pong 是黑白的，所以我们必须去掉 Pong 中的有色元素。

这也意味着我们需要将新的精灵导入到 Unity 项目中(通过将它们拖动到资产窗格中的精灵文件夹中)。如果您正在跟进，请随意使用演示代码中的这些例子。

将新精灵导入 Unity 后，我们应该在资产窗格中选择两个精灵，并将它们的单位像素更改为`64`。我们还必须点击**应用**以使更改生效。

现在我们有了我们的图像，我们可以开始更新我们的 Pong 克隆。首先，我们应该将主摄像头的背景颜色改为黑色。这可以通过点击主相机的检查器窗格中单词**背景**旁边的颜色条来完成。接下来，我们需要改变桨的颜色。我们应该在层次窗格中选择玩家游戏对象，并将白色正方形 sprite 拖动到 Sprite 渲染器元素的 Sprite 属性值中。现在我们应该对敌人的游戏对象做同样的事情。

为了在 Pong 中创建漂亮的中间栏(*见下文*，我们需要创建一个空的游戏对象(在**层级中右击- >创建空的**)并将其命名为<q>中间栏</q>。中间的条形游戏对象应该有一个 X 为`0`和 Y 为`0`，这样它就位于屏幕的中心。现在，我们可以将白色方块精灵拖动到中间的条形游戏对象上，使白色方块成为中间条形游戏对象的子对象。所有孩子的 X 尺度都应该很低，这样他们看起来就像又瘦又白的棍子。

![Middle Bar Example](img/ecb7b7e16b92d743db0a1786233a030e.png)

最后，我们需要改变球的精灵，使它不再是一个灰色的圆圈，而是一个白色的圆圈。如果我们去球预置(位于预置文件夹中)，我们可以选择它，并改变它的精灵，就像我们对玩家和敌人所做的那样，除了使用白色圆圈精灵。

## 让碰撞变得更好

当玩上一个教程中的 [Pong 克隆时，我们可以观察到一些碰撞的错误。有时球可能会以一个奇怪的角度射出，或者球撞击物体的反应可能会使球直接飞回来，而不是以一个角度飞回来。为了解决这个问题，我们需要给球添加代码，允许球计算它击中球拍的角度，并根据该角度反弹球拍。让我们打开脚本文件夹中的 BallController 脚本。随着 BallController 脚本的打开，我们应该使它的`OnCollisionEnter2D`方法看起来如下所示:](http://quarlwithcode.itch.io/pong-tutorial-part-1-game)

```
void OnCollisionEnter2D(Collision2D col) {

        //tag check
        if (col.gameObject.tag == "Enemy") {
            //calculate angle
            float y = launchAngle(transform.position,
                                col.transform.position,
                                col.collider.bounds.size.y);

            //set angle and speed
            Vector2 d = new Vector2(1, y).normalized;
            rig2D.velocity = d * speed * 1.5F;
        }

        if (col.gameObject.tag == "Player") {
            //calculate angle
            float y = launchAngle(transform.position,
                                col.transform.position,
                                col.collider.bounds.size.y);

            //set angle and speed
            Vector2 d = new Vector2(-1, y).normalized;
            rig2D.velocity = d * speed * 1.5F;
        }
    }

    //calculates the angle the ball hits the paddle at
    float launchAngle(Vector2 ball, Vector2 paddle,
                    float paddleHeight) {
        return (ball.y - paddle.y) / paddleHeight;
    }
```

我们现在需要做的就是为敌人的球拍创建一个标签，并为球员的球拍添加一个标签，这样球就可以知道哪个是哪个。如果我们从层级窗格中选择敌人的游戏对象，并在敌人名称下的检查器窗格中单击**未标记的**，将会出现一个下拉菜单。我们可以点击**添加标签**来查看标签和图层菜单。通过单击+符号，我们可以将名为“敌人”的标签添加到标签列表中。现在让我们再次选择敌人的游戏对象，单击未标记，然后单击敌人将其标记设置为敌人。对于玩家游戏对象，我们所要做的就是选择游戏对象，点击 untagged，然后点击玩家标签，因为 Unity 会在项目第一次创建时为我们创建一个玩家标签。

## 提高敌方人工智能

前一个教程的敌人 AI 是我们将在本教程中使用的 AI 的简化版本。旧的人工智能根据球的 Y 位置移动，但速度很低，这样球拍就不会反弹或抖动。在新的人工智能中，我们仍然会使用球的 Y 位置作为我们代码的基础，但是我们会让球拍根据时间移动。这使得桨能够快速移动，人眼几乎无法跟随。如果时间和速度值做得正确(甚至足够接近完美)，敌人的桨的运动将看起来几乎完全平滑。

我们还将增加一点敌人球拍的移动范围，这样球拍的移动范围就不会直接等于球的 Y 轴位置，而是有一个微小的范围，仍然允许球拍击球，也允许球击中球拍上的不同点。

如果我们打开 EnemyController 脚本并将其更改为如下所示的代码，我们将得到我们想要的结果:

```
// Use this for initialization
    void Start () {
        //Continuously Invokes Move every x seconds (values may differ)
        InvokeRepeating("Move", .02F, .02F);
    }

    // Movement for the paddle
    void Move () {

        //finding the ball
        if(ball == null){
            ball = GameObject.FindGameObjectWithTag("Ball").transform;
        }

        //setting the ball's rigidbody to a variable
        ballRig2D = ball.GetComponent<Rigidbody2D>();

        //checking x direction of the ball
        if(ballRig2D.velocity.x < 0){

            //checking y direction of ball
            if(ball.position.y < this.transform.position.y-.5F){
                //move ball down if lower than paddle
                transform.Translate(Vector3.down*speed*Time.deltaTime);
            } else if(ball.position.y > this.transform.position.y+.5F){
                //move ball up if higher than paddle
                transform.Translate(Vector3.up*speed*Time.deltaTime);
            }

        }

        //set bounds of enemy
        if(transform.position.y > topBound){
            transform.position = new Vector3(transform.position.x, topBound, 0);
        } else if(transform.position.y < bottomBound){
            transform.position = new Vector3(transform.position.x, bottomBound, 0);
        }
    }
```

*注意:使调用重复方法的时间更长会减慢桨的速度，但会增加抖动效果。从我的测试来看，混合桨的速度在 1 到 4(浮点数)之间，调用重复的时间在 0.1 到 0.3 之间效果最好。*

即使这个更新的代码比旧代码更好，我们仍然希望敌人的桨能够在球第一次产生时到达球。为了解决这个问题，我们需要敌人的球拍在球被摧毁后重置它的位置。我们可以通过打开 BoundsController 脚本并将其更改为如下所示来实现这一点:

```
//enemy transform
public Transform enemy;

    void OnTriggerEnter2D(Collider2D other){
        if(other.gameObject.tag == "Ball"){

            //Destroys other object
            Destroy(other.gameObject);

            //sets enemy's position back to original
            enemy.position = new Vector3(-6,0,0);

            //pauses game
            Time.timeScale = 0;
        }
    }
```

我们还需要确保将敌人的桨从层次中拖动到两个边界对象上的敌人变换值中，否则我们将得到一个空引用异常。

## 创建暂停菜单

这个 Pong 克隆的暂停功能将与我的 [Dodger 教程](https://www.sitepoint.com/building-a-dodger-game-clone-in-unity/)中的功能略有不同。由于这个游戏有文字，当球被破坏和玩家暂停游戏时会出现，我们必须使用 Unity 的`gameObject.name`变量来设置它的活动状态。我们还将在游戏中添加一个来自字体松鼠的自定义字体，给它一个更复古的感觉。

### 制作复古暂停菜单

为了给 Unity 添加自定义字体，我们需要将其下载到我们的计算机上，然后通过拖放将其导入 Unity。首先，我们需要[下载丝印](http://www.fontsquirrel.com/fonts/Silkscreen?q%5Bterm%5D=pixel&q%5Bsearch_check%5D=Y) ( *不是下载链接*)然后解压下载的文件。解压文件后，我们可以看到有许多不同类型的丝印。出于本教程的考虑，我们将使用 **slkscreb.ttf** 。我们不需要在电脑上安装这种字体。相反，让我们在“资源”窗格中创建一个名为“字体”的文件夹。现在我们可以打开 Fonts 文件夹，将`slkscreb.ttf`文件拖到 Assets 窗格中。

随着 Unity 增加了 SilkScreen，我们现在可以开始制作暂停菜单了。我们的菜单将与顶部对齐(见下文),并将有两个按钮和上一篇文章中的文本:

![Pause Menu](img/9ce8e8d201da6a8030c13f795a8f03ff.png)

我们现在应该创建一个名为 ResetButton 的按钮游戏对象。为了坚持我们为自己设置的传统主题，我们应该用白色方形精灵替换按钮的源图像属性(位于检查器中的图像元素下)。我们可以通过从 Sprites 文件夹中拖动白色正方形 sprite 到 Source Image 属性的值中来实现。现在我们应该看到按钮是一个没有圆角的白色矩形。

接下来，我们需要将按钮上的字体改为像素化程度更高的丝印字体。如果我们从层次结构中选择子文本对象，我们可以使文本对象的文本属性看起来像下面的值:

![ResetButton's text values](img/049d2a6f244022b818b0d43ff08e3a2f.png)

*注意:要更改文本的字体，我们可以简单地将 slkscreb.ttf 文件从 Assets 窗格拖动到 Font 属性的值中。还要注意，字体的大小可能因人而异。*

要创建主菜单按钮，我们可以在层级中选择重置按钮并复制它(`command/ctrl` + `d`或**右键- >复制**)。现在我们要做的就是将复制的按钮命名为 MainMenuButton。我们应该将每个按钮与屏幕上相应的区域对齐(重置到左上方，主菜单到右上方)。

接下来，我们可以设置上一节课创建的文本的样式。如果文本还没有命名为 PauseText，我们应该将其改为 PauseText。我们还应该将字体属性从 Arial 更改为丝印字体。让我们确保文本是白色的，并且居中。最后，我们需要将它对齐到屏幕的中间，略高于中心。

### 向暂停菜单添加功能

我们已经创建了一个复古风格的暂停菜单，但是现在当游戏运行时，它会一直出现在屏幕上，并且当它们被点击时，按钮不会做任何事情。为了给菜单添加功能，我们应该创建一个名为 UIManager 的空游戏对象。现在让我们创建一个名为 UIManager 的脚本，并在我们的 ide 中打开它。为了获得完整的功能，我们可以将以下代码添加到我们的脚本中:

```
GameObject[] pauseObjects;

    // Use this for initialization
    void Start () {
        pauseObjects = GameObject.FindGameObjectsWithTag("ShowOnPause");
    }

    // Update is called once per frame
    void Update () {

        //uses the p button to pause and unpause the game
        if(Input.GetKeyDown(KeyCode.P))
        {
            if(Time.timeScale == 1)
            {
                Time.timeScale = 0;
                showPaused();
            } else if (Time.timeScale == 0){
                Time.timeScale = 1;
                hidePaused();
            }
        }

        if(Time.timeScale == 0){
            //searches through pauseObjects for PauseText
            foreach(GameObject g in pauseObjects){
                if(g.name == "PauseText")
                    //makes PauseText to Active
                    g.SetActive(true);
            }
        } else {
            //searches through pauseObjects for PauseText
            foreach(GameObject g in pauseObjects){
                if(g.name == "PauseText")
                    //makes PauseText to Inactive
                    g.SetActive(false);
            }
        }
    }

    //Reloads the Level
    public void Reload(){
        Application.LoadLevel(Application.loadedLevel);
    }

    //controls the pausing of the scene
    public void pauseControl(){
        if(Time.timeScale == 1)
        {
            Time.timeScale = 0;
            showPaused();
        } else if (Time.timeScale == 0){
            Time.timeScale = 1;
            hidePaused();
        }
    }

    //shows objects with ShowOnPause tag
    public void showPaused(){
        foreach(GameObject g in pauseObjects){
            g.SetActive(true);
        }
    }

    //hides objects with ShowOnPause tag
    public void hidePaused(){
        foreach(GameObject g in pauseObjects){
            g.SetActive(false);
        }
    }

    //loads inputted level
    public void LoadLevel(string level){
        Application.LoadLevel(level);
    }
```

这个 UIManager 非常类似于 Dodger 教程系列中的 UIManager。主要的区别是在`Update()`方法内部。我们添加了代码，通过使用 foreach 循环查找名为 pause text 的对象，在`pauseObjects`数组中查找暂停文本。一旦找到，它将根据游戏的时间尺度设置暂停文本的活动状态。

现在我们已经写好了脚本，让我们将它添加到 Unity 中的 UIManager 游戏对象，方法是将它拖到游戏对象上。为了在屏幕暂停时隐藏菜单，我们应该让按钮和暂停文本的标签显示暂停。这意味着我们需要为每个对象创建一个新的标记 ShowOnPause。

添加标签并将 UIManager 脚本附加到 UIManager 游戏对象后，我们现在可以暂停游戏来查看我们的菜单。我们还可以看到暂停文字出现在游戏暂停或者球被破坏的时候。

为了完成菜单，我们需要将我们的方法添加到按钮中。我们可以选择重置按钮，并点击按钮元素下检查器中 OnClick()菜单上的+符号。现在我们可以将 UIManager 游戏对象从层次窗格拖动到第一个输入框中。如果我们单击第二个下拉框并选择 **UIManager - > Reload** ，按钮上将添加`Reload()`功能。OnClick()菜单应该如下所示:

![Reset OnClick() menu](img/c2e22954800f5f048a481d1b1b303ca5.png)

对于主菜单按钮，我们可以做和上面一样的事情，但是添加了`LoadLevel()`方法。添加了`LoadLevel()`方法后，我们会看到一个对话框，允许我们添加字符串参数。在框内我们可以输入 MainMenu，这样当按钮被点击时我们的主菜单场景就会被加载。主菜单按钮的 OnClick()菜单应该如下所示:

![Main Menu OnClick() menu](img/788ded4373a28382306aed62dc921bd8.png)

### 修复暂停功能

现在我们已经添加了我们的菜单和 UIManager，我们已经修复了一些问题并添加了一个问题。如果我们试图暂停游戏，我们可能会注意到(这可能取决于 Unity 版本)暂停不起作用。这是因为我们有两个不同的脚本来设置屏幕的时间刻度，从而导致它们相互抵消。要解决这个问题，我们可以打开 PlayerController 脚本并删除下面的代码:

```
//pauses or plays game when player hits p
        if(Input.GetKeyDown(KeyCode.P) && Time.timeScale == 0){
            Time.timeScale = 1;
        } else if(Input.GetKeyDown(KeyCode.P) && Time.timeScale == 1){
            Time.timeScale = 0;
        }
```

## 添加主菜单

对于这个 Pong 克隆，我们将创建一个有趣的主菜单。这个场景不会是只有文字和一个**播放**按钮的静态场景。我们将在主菜单的背景中添加模拟游戏。这意味着当玩家第一次打开游戏时，他们会看到我们放在场景中的 UI 元素，以及两个相互对抗的 AI 桨。

### 创建场景

为了开始制作我们的主菜单，我们需要一个新的场景。我们将复制当前场景，而不是创建一个全新的场景。在我们继续之前，我们应该确保我们的场景被保存。

保存场景后，我们现在可以在资产窗格中选择它并复制它(`command/ctrl` + `d`)。现在让我们将副本重命名为 MainMenu 并打开它(通过双击)。

在主菜单场景中，我们应该禁用/删除层次结构中的球所有者游戏对象、中间条游戏对象、向左游戏对象、向右游戏对象和暂停菜单游戏对象。我们删除这些是因为它们不是必需的，并且可能会妨碍我们将要添加的 UI。

### 添加用户界面

主菜单的用户界面将保持简约。我们将有一个显示单词 Pong 的大标题文本和一个显示 **play** 的按钮。

我们可以从创建一个名为 TitleText 的文本对象开始。标题文本的文本元素应该具有如下所示的属性值:

![TitleText text attributes](img/5afc1880afe6c48cf07e7a69292f68f4.png)

接下来，我们可以创建一个按钮对象，并将其命名为 PlayButton。我们应该将**播放**按钮的源图像改为白色方形精灵。让我们也添加`LoadLevel()`方法到**播放**按钮，字符串参数是我们播放场景的名称。我们可以通过在资产窗格中找到或者转到**文件- >构建设置**来检查我们的游戏场景的名称(游戏场景应该是唯一列出的场景；如果不是，那么就是最顶层的场景)。子文本对象的属性应该与标题文本的属性相同，只是文本应该保持灰色，并且字体大小不同。

最后，我们需要将 UI 元素对齐到我们喜欢的位置，如下所示:

![Pong Main Menu](img/517c7fd087ab9ed443532e8ca650f3cb.png)

### 添加模拟游戏

为了在我们的主菜单背景中模拟游戏，我们需要给游戏手柄添加人工智能，类似于我们在游戏关卡中为敌人使用的人工智能。我们还需要添加一个球，以便人工智能桨可以玩。

首先，让我们添加一个球到场景中，方法是转到“资源”窗格中的“预设”文件夹，然后添加球预设。我们应该确保它的变换位置是`X:0 Y:0 Z:0`，这样它就在屏幕的中心。

在 Assets 窗格的 Scripts 文件夹中，我们需要创建一个名为 AutoPlayer 的新脚本，然后在 ide 中打开它。自动播放脚本应该包含以下代码:

```
//Speed of the AI
    public float speed = 2.75F;

    //the ball
    Transform ball;

    //the ball's rigidbody 2D
    Rigidbody2D ballRig2D;

    //bounds of AI
    public float topBound = 4.5F;
    public float bottomBound = -4.5F;

    // Use this for initialization
    void Start () {
        //Continuously Invokes Move every x seconds (values may differ)
        InvokeRepeating("Move", .02F, .02F);
    }

    // Movement for the paddle
    void Move () {

        //finding the ball
        if(ball == null){
            ball = GameObject.FindGameObjectWithTag("Ball").transform;
        }

        //setting the ball's rigidbody to a variable
        ballRig2D = ball.GetComponent<Rigidbody2D>();

        //checking x direction of the ball
        if(ballRig2D.velocity.x > 0){

            //checking y direction of ball
            if(ball.position.y < this.transform.position.y-.3F){
                //move ball down if lower than paddle
                transform.Translate(Vector3.down*speed*Time.deltaTime);
            } else if(ball.position.y > this.transform.position.y+.3F){
                //move ball up if higher than paddle
                transform.Translate(Vector3.up*speed*Time.deltaTime);
            }

        }

        //set bounds of AI
        if(transform.position.y > topBound){
            transform.position = new Vector3(transform.position.x, topBound, 0);
        } else if(transform.position.y < bottomBound){
            transform.position = new Vector3(transform.position.x, bottomBound, 0);
        }
    }
```

我们现在可以从层次中的玩家游戏对象中删除 PlayerController 脚本，并添加自动玩家脚本。

在我们的脚本文件夹中，让我们创建另一个名为 AutoEnemy 的脚本，并在 ide 中打开它。AutoEnemy 脚本应该包含以下代码:

```
/Speed of the AI
    public float speed = 2.75F;

    //the ball
    Transform ball;

    //the ball's rigidbody 2D
    Rigidbody2D ballRig2D;

    //bounds of AI
    public float topBound = 4.5F;
    public float bottomBound = -4.5F;

    // Use this for initialization
    void Start () {
        //Continuously Invokes Move every x seconds (values may differ)
        InvokeRepeating("Move", .02F, .02F);
    }

    // Movement for the paddle
    void Move () {

        //finding the ball
        if(ball == null){
            ball = GameObject.FindGameObjectWithTag("Ball").transform;
        }

        //setting the ball's rigidbody to a variable
        ballRig2D = ball.GetComponent<Rigidbody2D>();

        //checking x direction of the ball
        if(ballRig2D.velocity.x < 0){

            //checking y direction of ball
            if(ball.position.y < this.transform.position.y-.3F){
                //move ball down if lower than paddle
                transform.Translate(Vector3.down*speed*Time.deltaTime);
            } else if(ball.position.y > this.transform.position.y+.3F){
                //move ball up if higher than paddle
                transform.Translate(Vector3.up*speed*Time.deltaTime);
            }

        }

        //set bounds of AI
        if(transform.position.y > topBound){
            transform.position = new Vector3(transform.position.x, topBound, 0);
        } else if(transform.position.y < bottomBound){
            transform.position = new Vector3(transform.position.x, bottomBound, 0);
        }
    }
```

为了得到好的模拟游戏，我们需要改变玩家和敌人的速度值。

如果我们点击播放，我们应该看到背景已经模拟游戏。然而，如果我们点击**播放**按钮，我们会注意到它不起作用。这是因为我们必须将场景添加到构建设置中。我们可以通过点击**文件- >构建设置**并将场景从资产窗格拖到菜单中，或者点击**添加当前**按钮来完成。将主菜单场景添加到构建设置后，我们需要将它拖到场景列表的顶部。

## 添加玩家分数界面

对于我们的克隆体，我们将让游戏在玩家或人工智能达到 7 分时结束。在我们对游戏的功能进行编码之前，我们需要跟踪这两个分数。

我们现在可以保存主菜单场景并打开播放场景。让我们添加一个文本对象，并将其命名为 ScoreText。乐谱文本的属性应该如下图所示:

![ScoreText Properties](img/0c70b5fc5a84fd1c883429870607b278.png)

我们应该将乐谱对齐到屏幕顶部的中央。现在，让我们打开 BoundController 脚本，使它看起来像下面的代码:

```
//enemy transform
    public Transform enemy;

    public int enemyScore;
    public int playerScore;

    void Start(){
        enemyScore = 0;
        playerScore = 0;
    }

    void OnTriggerEnter2D(Collider2D other){
        if(other.gameObject.tag == "Ball"){
            if(other.gameObject.GetComponent<Rigidbody2D>().velocity.x > 0){
                enemyScore++;
            } else {
                playerScore++;
            }

            //Destroys other object
            Destroy(other.gameObject);

            //sets enemy's position back to original
            enemy.position = new Vector3(-6,0,0);
            //pauses game
            Time.timeScale = 0;
        }
    }
```

我们需要将敌人游戏对象从层次结构中拖动到左右边界对象的边界控制器敌人属性中。敌人的变形将被用来在球被摧毁后重置敌人的位置。将敌人游戏对象添加到边界后，我们可以创建一个名为 PointCounter 的新脚本，并在 IDE 中打开它。我们应该让 PointCounter 脚本看起来像下面的代码:

```
using UnityEngine;
using UnityEngine.UI;
using System.Collections;

public class PointCounter : MonoBehaviour {

    public GameObject rightBound;
    public GameObject leftBound;
    Text text;
    // Use this for initialization
    void Start () {
        text = GetComponent<Text>();
        text.text = rightBound.GetComponent<BoundController>().enemyScore + "\t\t" +
            leftBound.GetComponent<BoundController>().playerScore;
    }

    // Update is called once per frame
    void Update () {
        text.text = rightBound.GetComponent<BoundController>().enemyScore + "\t\t" +
            leftBound.GetComponent<BoundController>().playerScore;
    }
}
```

我们应该将 PointCounter 脚本附加到分数文本中。我们还需要将左右绑定的游戏对象拖动到适当的位置，作为 PointCounter 脚本的 right bound 和 LeftBound 变量的值。

## 结束游戏

我们要完成 Pong 克隆的最后一件事是创建游戏功能，这样玩家就知道他们是输给了 AI 还是赢了它。

### 创建菜单

让我们首先创建一个名为 ShowOnFinish 的新标记。创建这个标签后，我们可以创建一个名为 GameOverText 的文本对象。我们可以将文本上的游戏标签设置为 ShowOnFinish。我们可以将游戏的文本属性设置为与下图相同:

![GameOverText text properties](img/2f0144049e36d0fd48e1ff57ef08ee61.png)

现在我们可以将文本上的游戏对齐到屏幕中央。

我们可以复制主菜单和重置按钮，并将它们的标签设置为 ShowOnFinish，而不是为游戏结束状态创建所有新按钮。我们可以将新的主菜单按钮命名为 FinishMainMenuButton，新的重置按钮命名为 FinishResetButton。游戏结束菜单现在已经创建好了，但是我们还没有添加任何功能。

### 添加功能

让我们打开 UIManager 脚本，使代码看起来如下所示:

```
//arrays for pause and game over objects
    GameObject[] pauseObjects, finishObjects;

    //variables for the bounds
    public BoundController rightBound;
    public BoundController leftBound;

    //game over variables
    public bool isFinished;
    public bool playerWon, enemyWon;

    // Use this for initialization
    void Start () {
        pauseObjects = GameObject.FindGameObjectsWithTag("ShowOnPause");
        finishObjects = GameObject.FindGameObjectsWithTag("ShowOnFinish");
        hideFinished();
    }

    // Update is called once per frame
    void Update () {

        //checks to make sure the current level is play level
        if(Application.loadedLevel == 1){
            if(rightBound.enemyScore >= 7 && !isFinished){
                isFinished = true;
                enemyWon = true;
                playerWon = false;
            } else if (leftBound.playerScore >= 7  && !isFinished){
                isFinished = true;
                enemyWon = false;
                playerWon = true;
            }

            if(isFinished){
                showFinished();
            }
        }

        //uses the p button to pause and unpause the game
        if(Input.GetKeyDown(KeyCode.P) && !isFinished)
        {
            pauseControl();
        }

        if(Time.timeScale == 0  && !isFinished){
            //searches through pauseObjects for PauseText
            foreach(GameObject g in pauseObjects){
                if(g.name == "PauseText")
                    //makes PauseText to Active
                    g.SetActive(true);
            }
        } else {
            //searches through pauseObjects for PauseText
            foreach(GameObject g in pauseObjects){
                if(g.name == "PauseText")
                    //makes PauseText to Inactive
                    g.SetActive(false);
            }
        }
    }

    //Reloads the Level
    public void Reload(){
        Application.LoadLevel(Application.loadedLevel);
    }

    //controls the pausing of the scene
    public void pauseControl(){
        if(Time.timeScale == 1)
        {
            Time.timeScale = 0;
            showPaused();
        } else if (Time.timeScale == 0){
            Time.timeScale = 1;
            hidePaused();
        }
    }

    //shows objects with ShowOnPause tag
    public void showPaused(){
        foreach(GameObject g in pauseObjects){
            g.SetActive(true);
        }
    }

    //hides objects with ShowOnPause tag
    public void hidePaused(){
        foreach(GameObject g in pauseObjects){
            g.SetActive(false);
        }
    }

    //shows objects with ShowOnFinish tag
    public void showFinished(){
        foreach(GameObject g in finishObjects){
            g.SetActive(true);
        }
    }

    //hides objects with ShowOnFinish tag
    public void hideFinished(){
        foreach(GameObject g in finishObjects){
            g.SetActive(false);
        }
    }

    //loads inputted level
    public void LoadLevel(string level){
        Application.LoadLevel(level);
    }
```

我们现在可以将左右边界游戏对象添加到附加到 UIManager 游戏对象的 UIManager 脚本的适当变量中。我们还需要创建一个名为 GameOver 的新脚本。让我们打开它，使代码看起来像这样:

```
using UnityEngine;
using UnityEngine.UI;
using System.Collections;

public class GameOver : MonoBehaviour {

    public UIManager uiManager;
    private Text text;
    // Use this for initialization
    void Start () {
        text = GetComponent<Text>();
    }

    // Update is called once per frame
    void Update () {
        if(uiManager.playerWon){
            text.text = "GAME OVER!\nPLAYER WON!";
        } else if(uiManager.enemyWon){
            text.text = "GAME OVER!\nENEMY WON!";
        }
    }
}
```

现在我们要做的就是将脚本添加到文本游戏对象中，并将 UIManager 游戏对象拖到 UIManager 变量中。如果我们玩游戏并故意输了，我们应该看到输了之后文字会改变，按钮会出现。

*注意:如果单词没有出现或被截断，这意味着 GameOverText 的 Rect 变换的宽度和高度应该变大。此外，如果暂停菜单不能正常工作，请尝试重新创建按钮。*

## 结论

祝贺你，我们已经正式制造了一个完整的，无声的 Pong 克隆体！

这花了一段时间，但希望这个教程足够平易近人，让你在 Unity 中开始尝试自己的基本 2D 物理游戏。让我们知道你想出了什么！

*记住你可以[在 GitHub](https://github.com/quarlwithcode/pongtutorialpt2) 上下载完整的项目。*

## 分享这篇文章