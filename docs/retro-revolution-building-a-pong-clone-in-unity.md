# 复古革命:在 Unity 中构建 Pong 克隆

> 原文：<https://www.sitepoint.com/retro-revolution-building-a-pong-clone-in-unity/>

*比赛开始前，你可以在[itch . io](http://quarlwithcode.itch.io/pong-tutorial-part-1-game)T3 观看比赛*

![](img/5ae718a68a9233c2dc5458a2795185dc.png)

## 分析 Pong

Pong 是最早制作的视频游戏之一，也是第一个成功的商业游戏。当 Pong 第一次被创建时，开发人员很可能为代码的逻辑而挣扎，然而，现在你可以用一个方法调用、碰撞器和精灵来创建一个简单的双人 Pong。一旦决定制作单人 Pong，Pong 就变得更难制作了。在本教程中，我们将创建 Pong 的基本游戏，并分解一个非常简单的 AI 替代方案，它仍然增加了游戏的价值。

我们必须要问，Pong 玩法的核心元素是什么？以下是这个问题的答案列表:

1.  **玩家输入**–我们希望玩家能够上下移动球拍，以便击球。
2.  **球碰撞**——当球撞击球拍或边界时，不能让球失去任何速度。
3.  **边界碰撞**–球必须能够从屏幕的顶部和底部弹开，这样它才不会离开游戏区。
4.  如果敌人坐在屏幕的另一端不动，游戏的游戏价值将接近于零。
5.  **产卵球**–当球击中挡板后的一个边界时，我们需要它重生，这样我们才能继续游戏。
6.  **球对球拍击打区域检测**–这允许球以独特的角度从球拍上弹开，这样当球被球拍击打时，我们能够更好地瞄准球。

有了这个列表，我们就可以开始游戏编程了。

*请注意，任何与游戏对象的位置、旋转、比例等相关的数字都是相对的，可能需要根据您的具体设置进行更改。*

## 设置游戏

现在我们已经分析了 Pong 的基本原理，我们可以开始设置游戏了。继续打开 Unity 并创建一个新的 2D 项目。编辑器打开后，将游戏屏幕的纵横比设置为 4:3。我们使用 4:3，因为这是最常见的屏幕比例之一，也是最接近标准的比例之一。在`Assets`窗格中创建四个文件夹，分别命名为`Scripts`、`Sprites`、`Prefabs`和`Materials`。这些文件夹将用于保存我们所有的游戏资产。

![Adding folders](img/5c967a7b2bed7cd40d4083a6976c7461.png)

为游戏下载合适的图片，并将它们添加到精灵文件夹中(这可以通过拖放来完成)。我们刚刚添加的图像将是游戏中使用的精灵，交互式游戏对象。

![Gray circle sprite](img/7d6ec4d0958206634f0a8d34f1f5c186.png)![Blue square sprite](img/8820b081b7f35241ec8778a9e5d8b813.png)

我们将需要改变精灵的单位像素，使它们符合标准。我通常使用每单位 64 像素，因为这给了大多数精灵一个清晰的外观，并保持他们的相对大小。你可以把每单位像素想象成 Unity 编辑器中 1×1 空间内分配的像素密度。

让我们将正方形的单位像素设置为 64，圆形的单位像素可以设置为 128。我们可以继续将这三个图像添加到层次窗格中。

![Hierarchy pane](img/6cc3615f80747f994567811747ab6274.png)

现在我们需要给每个资产一个名称，并设置它们的初始属性和标签。您可以将蓝色块命名为“玩家”，并将玩家的 x 位置设置为`6`，x 比例设置为`.2`。

我们将需要创建一个标签来区分桨游戏对象和其他游戏对象。从广义上来说，你可以把`Tags`看作游戏对象的类别。点击“未标记”(位于玩家姓名下)，选择“添加标记”。创建一个名为“Paddle”的新标签，重新选择玩家游戏对象并将其标签设置为 Paddle。

将红色方块命名为“敌人”。将敌人的 x 位置设置为`-6`，x 刻度设置为`.2`。制作敌人游戏对象的标签桨。

将灰色圆圈命名为“Ball ”,然后创建一个名为“Ball”的新标签。确保将球类游戏对象的标签设置为 ball。

## 添加玩家输入

在“资源”窗格中，打开“脚本”文件夹，创建一个名为“PlayerController”的新 C#脚本。在 IDE 中打开 PlayerController 脚本，并键入:

```
 //speed of player
	public float speed = 10;

	//bounds of player
	public float topBound = 4.5F;
	public float bottomBound = -4.5F;

	// Use this for initialization
	void Start () {
		Time.timeScale = 0;
	}

	void Update(){

		//pauses or plays game when player hits p
		if(Input.GetKeyDown(KeyCode.P) && Time.timeScale == 0){
			Time.timeScale = 1;
		} else if(Input.GetKeyDown(KeyCode.P) && Time.timeScale == 1){
			Time.timeScale = 0;
		}
	}

	// Update is called once per frame
	void FixedUpdate () {

		//get player input and set speed
		float movementSpeedY = speed * Input.GetAxis("Vertical") * Time.deltaTime;
		transform.Translate(0, movementSpeedY, 0);

		//set bounds of player
		if(transform.position.y > topBound){
			transform.position = new Vector3(transform.position.x, topBound, 0);
		} else if(transform.position.y < bottomBound){
			transform.position = new Vector3(transform.position.x, bottomBound, 0);
		}
	} 
```

通过将脚本拖动到游戏对象上，将 PlayerController 脚本添加到“层次”窗格中的玩家游戏对象。选择玩家游戏对象，点击检查面板底部的“添加组件”,为游戏对象添加一个盒子碰撞器 2D。

## 球碰撞

打开“资源”窗格中的“材质”文件夹。您可以通过右键单击并选择“创建”->“物理 2D 材质”来创建新的物理 2D 材质(如下图所示)。

![Creating a new Physics2D material](img/577bcc0a132db24930e15cbbe6789d05.png)

继续将新材质命名为“Bounce ”,并将其弹性设置为 1，摩擦力设置为 0。

![Friction and bounciness settings](img/0c58117be1646a2941a6862a34cbe850.png)

在层次窗格中选择球类游戏对象。将圆形碰撞器 2D 添加到球类游戏对象中。将反弹材质拖动到碰撞器的材质属性，以设置要反弹的材质。通过给球类游戏对象提供反弹材质，我们使得当球类游戏对象撞击边界时，它不会失去任何速度，并且会以正确的角度反弹。我们还需要添加一个刚体 2D 到球类游戏对象，并设置重力比例为 0，然后检查固定角度。

![Ball settings](img/8941e1744e65ffa3255e1d1f1bac8128.png)

打开“资源”窗格中的“脚本”文件夹，创建一个名为“BallController”的脚本。在 ide 中打开 BallController 脚本，并键入:

```
//speed of the ball
	public float speed = 3.5F;

	//the initial direction of the ball
	private Vector2 spawnDir;

	//ball's components
	Rigidbody2D rig2D;
	// Use this for initialization
	void Start () {
		//setting balls Rigidbody 2D
		rig2D = this.gameObject.GetComponent<Rigidbody2D>();

		//generating random number based on possible initial directions
		int rand = Random.Range(1,4);

		//setting initial direction
		if(rand == 1){
			spawnDir = new Vector2(1,1);
		} else if(rand == 2){
			spawnDir = new Vector2(1,-1);
		} else if(rand == 3){
			spawnDir = new Vector2(-1,-1);
		} else if(rand == 4){
			spawnDir = new Vector2(-1,1);
		}

		//moving ball in initial direction and adding speed
		rig2D.velocity = (spawnDir*speed);

	} 
```

## 边界碰撞

在层级窗格中，创建两个空游戏对象(右键单击->创建空)，并将新游戏对象命名为“RightBound”和“TopBound”。将右界的 x 位置设置为 7，并将一个长方体碰撞器 2D 添加到右界游戏对象中。在检查器面板中选中“是触发器”,并将碰撞器的 y 尺寸设置为 20。复制右边界的游戏对象(选择它并按 Command+D)。将副本命名为“LeftBound ”,并将其 y 位置设置为-7。

接下来，选择顶部绑定的游戏对象，将其 y 位置设置为 5.5，并向顶部绑定的游戏对象添加一个长方体碰撞器 2D。将碰撞器的 x 尺寸设置为 20。复制顶部绑定的游戏对象，并将副本命名为“底部绑定”。将底部游戏对象的 y 位置设置为-5.5。

![Pong wireframe](img/69b33ea90b7fed1d2f63c67305457489.png)

我们希望当球越过其中一个挡板时，左右边界可以摧毁球。在“资源”窗格中，打开“脚本”文件夹，创建一个名为“BoundController”的新 C#脚本。在您最喜欢的 ide 中打开 BoundController 并键入:

```
void OnTriggerEnter2D(Collider2D other){

		//Destroys gameobject if its tag is Ball
		if(other.gameObject.tag == "Ball"){
			Destroy(other.gameObject);
		}
} 
```

将 BoundController 脚本添加到左界游戏对象和右界游戏对象。

## 敌人 AI

选择敌人的游戏对象，并添加一个箱子碰撞 2D。在“资源”窗格中，打开“脚本”文件夹。创建一个名为“EnemyController”的新 C#脚本，并键入:

```
//Speed of the enemy
	public float speed = 1.75F;

	//the ball
	Transform ball;

	//the ball's rigidbody 2D
	Rigidbody2D ballRig2D;

	//bounds of enemy
	public float topBound = 4.5F;
	public float bottomBound = -4.5F;

	// Use this for initialization
	void Start () {

	}

	// Update is called once per frame
	void FixedUpdate () {

		//finding the ball
		ball = GameObject.FindGameObjectWithTag("Ball").transform;

		//setting the ball's rigidbody to a variable
		ballRig2D = ball.GetComponent<Rigidbody2D>();

		//checking x direction of the ball
		if(ballRig2D.velocity.x < 0){

			//checking y direction of ball
			if(ball.position.y < this.transform.position.y){
				//move ball down if lower than paddle
				transform.Translate(Vector3.down*speed*Time.deltaTime);
			} else if(ball.position.y > this.transform.position.y){
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

*注意:1.75 被用来表示敌人的速度，仅仅是因为它让敌人慢到无法抓住每一个向它飞来的球。*

另一个注意:我们使用固定更新是因为这种方法更适合移动，因为它以固定的速率更新游戏对象。

## 产卵球

在层次窗格中创建一个空的游戏对象，并将其命名为“BallSpawner”。接下来，将球类游戏对象添加到“资源”窗格内的 Prefabs 文件夹中。我们可以删除层次窗格中的球类游戏对象。

打开“资源”窗格中的“脚本”文件夹，创建一个名为“BallSpawnerController”的新 C#脚本。在 ide 中打开 BallSpawnerController 并键入:

```
public GameObject ball;

	// Use this for initialization
	void Start () {
		GameObject ballClone;
		ballClone = Instantiate(ball, this.transform.position, this.transform.rotation) as GameObject;
		ballClone.transform.SetParent(this.transform);
	}

	// Update is called once per frame
	void Update () {
		if(transform.childCount == 0){
			GameObject ballClone;
			ballClone = Instantiate(ball, this.transform.position, this.transform.rotation) as GameObject;
			ballClone.transform.SetParent(this.transform);
		}
	} 
```

将 BallSpawnerController 脚本添加到 BallSpawner 游戏对象。现在我们可以将球游戏对象从 Prefabs 文件夹拖动到 BallSpawnerController 的球值中。

![enter image description here](img/566476d2b2c1bd9ff8bb7b0c68e34b74.png)

## 添加基本文本

我们需要在屏幕上添加一些文本，告诉玩家按 P 来玩和暂停游戏，以便玩家知道如何开始和暂停游戏。

我们可以创建一个文本游戏对象(在层次窗格中右键单击-> UI -> Text)，并将该文本游戏对象命名为 PlayText。接下来，让我们将它的文本值设置为“按 P 播放或暂停”并居中对齐文本。现在我们可以将游戏对象对齐到屏幕顶部，然后锚定它。让我们把它的宽度设置为 200，这样所有的文本都是可见的。最后，将文本颜色设置为白色。

保存场景，并将其添加到构建设置中(通过拖动或选中添加当前场景)，就大功告成了！

![](img/5ae718a68a9233c2dc5458a2795185dc.png)

## 结论

现在你已经在 Unity2D 中正式创建了一个基本的单人 Pong 克隆体。为了更多的练习，试着想办法更新游戏，让它变得更好——增加球的加速度(打得越多，球越快)，增加球拍的惯性，通过增加敌人的速度来增加难度等等。

*你可以在[GitHub](https://github.com/quarlwithcode/pongtutorialpt1)T3 上下载项目*

## 分享这篇文章