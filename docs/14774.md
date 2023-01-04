# 在 Flash MX 中构建一个射击游戏

> 原文：<https://www.sitepoint.com/shoot-em-up-game-flash-mx/>

你有没有幻想过在你选择的目标上发泄你的挫折感，构建你自己的太空入侵者游戏，或者甚至成为暴徒的一员并在芝加哥市中心一决雌雄？嗯，以这个简单的教程为基础，一切皆有可能！

在本教程中，我们将使用 flash MX 和 ActionScript 来构建一个可定制的 shoot-em-up(你可以在这里下载完整的 swf 和 Fla 文件[)。你还需要使用 Photoshop 或类似的绘画软件包来创作你的图形。你可能已经有了合适的东西，或者决定使用网上的东西。如果你决定使用自己以外的图片，一定要检查版权。](https://www.sitepoint.com/examples/shootemup/shootemup.zip)

适量的编程知识在本教程中是有帮助的，但不是必需的。解释了 ActionScript 代码的所有方面。

解决了这个问题，是时候开始了。首先:一壶咖啡！虽然本教程并不难，但如果您是一名新程序员，有些概念可能需要一些时间才能完全理解。当然，您可以将代码复制到 Flash 中，并更改相关变量。但是，如果你打算在未来将它个性化，为什么不现在就花时间去理解它呢？

##### 游戏如何运作

虽然这远不是构建 Flash 游戏的高级教程，但有几个要素可以将其归入中级类别。

游戏有 3 个目标。这些目标被放在一个数组中，然后随机选择。只有 3 个目标，我不能保证你永远不会玩同一个序列超过一次，但随机功能有助于使它有趣。每个目标都有另外两个相关联的图形。第一个是“活动目标”图形——一个简单的红色边框，指示要瞄准哪个目标；第二个是一个黑色的盒子来盖住它，表示它已经不在游戏中了。当然，利用你自己的想象力，你可以做得比这些快速的想法好得多！

##### 制图法

正如我刚才提到的，很多图形方面的东西取决于你。你可以随心所欲地发挥创造力，但是，至少，你需要具备以下条件:

*   目标图像
*   要瞄准的十字线
*   活动目标图像
*   指示目标被摧毁的图像

你的目标可以是你喜欢的任何图像——最简单的形式是，十字准线只是一个中心有垂直线和水平线相交的非填充圆。这会给你子弹的精确坐标。至于列表中的最后两个图形，嗯——它们也可以一样简单。

我假设您将为我们构建的三个目标中的每一个使用相同的图像。活动目标图像是一个可以围绕目标的非填充红色正方形；对于被破坏的目标图像，您只需要一个黑色方块来覆盖图像。在 Flash 中，您可以创建正方形，然后删除中心填充，或者简单地创建一个透明填充。但是，由于本教程不是关于图像创建，我们不会在这个问题上花太多时间。

假设您已经创建并保存了上面描述的图像，现在我们可以开始制作 Flash 电影了。

##### 构建 Flash 电影

启动 Flash MX，如果您还没有这样做，请打开一个新文件。点击保存，给你的电影命名(你可以是原创的，命名为 shootemup.fla)。

现在，命名你的层为“目标”，在第一帧，导入你的目标图像到舞台。

文件-导入或(ctrl+R [pc])浏览图像-打开

随着你的目标在主舞台上，确保它被选中，并使其成为一个单独的电影剪辑(F8 [pc])。当“创建元件”对话框出现时，将电影剪辑(MC)命名为“目标”，并确保选中“电影剪辑”单选按钮。在“属性检查器”中，将实例命名为“target1”。此时，将 TargetMC 的另外两个实例拖到舞台上，并分别为它们指定实例名“target2”和“target3”。单击第 2 帧并按 F5 复制该帧。我们现在将坚持使用三个静态目标，但是，在本教程之外，添加多少目标以及它们如何移动完全取决于您。

创建另一个层，命名为“文本”，在第一帧，使用文本工具，设置选项为“动态文本”，创建 2 个大约 30 个字符大小的框沿屏幕顶部。字体大小和颜色由你决定。您可能还想花一些时间来调整和对齐。

从属性检查器中选择“单行”和“文本边框”选项。在属性实例字段中，分别将它们命名为“hits”和“info”。移动到时间轴上的第 2 帧，创建一个新的关键帧(F6)，然后在舞台上的适当空白位置，添加一些“游戏结束”之类的文本。

创建另一层，命名为“行动”。打开面板(窗口菜单-操作或(F9 [PC])并将以下代码复制到第 1 帧:

```
(ActionScript (AS) for frame 1)  

stop();  

//Set global highScore variables  

highScore = 0;  

//Set the properties for the game elements  

_root.lastFrame = false; //Stop bullets  

         //See AS for bullet MC for details  

setProperty(_root.crosshair, _visible, true);   

SetProperty(_root.bullet, _visible, false); //  

//Individual destroyed target images hidden until needed  

setProperty(_root.target1d, _visible, false);  

setProperty(_root.target2d, _visible, false);  

setProperty(_root.target3d, _visible, false);  

//As above. Individual highlighted target images hidden until needed  

setProperty(_root.target1Image, _visible, false);  

setProperty(_root.target2Image, _visible, false);  

setProperty(_root.target3Image, _visible, false);  

mouse.hide(); //hide mouse to use cross-hairs  

//select a target to shoot at random then add that target  

//to the array of selected targets so that it isn't used  

//again in this game.  

//When the number of direct hits against the selected  

//target has been reached. Find another random target.  

//Check whether it exists in the array. If yes, select  

//again. If no, make visible.  

_root.targets = new Array(); //create array on the main  

              //timeline so that it is  

              //easily accessed by all MCs  

_root.targets[0] = "target1";  

_root.targets[1] = "target2";  

_root.targets[2] = "target3"; //3 targets we are using in   

                //tutorial  

n = _root.targets.length; //get length of array  

ran = random(n); //get random number location to select from array  

target = _root.targets[ran]; //show random array location  

_root.activeTarget = target; //create target variable  

setProperty("_root."+target+"Image", _visible, true); //show target  

_root.targets.splice(ran, 1); //remove the random element  

          //from the array. splice  

          //the random number found and  

            //to the depth of one so only  

            //one element is removed.  

//-------------------------------------------------------//
```

(关于行动框架 2)

点击动作层的第 2 帧，创建一个空白的关键帧(F7)。然后，在“动作”面板中输入以下代码:

```
stop();  

_root.lastFrame = true; //Stop bullets  

        //See AS for bullet MC for details  

mouse.show(); //reshow mouse  

setProperty(_root.crosshair, _visible, false); //Hide cross hair  

target1Hits = 0; //reset the hits for each target in case another  

         //game is played.  

target2Hits = 0;  

target3Hits = 0;  

//-------------------------------------------------------//
```

##### 创建十字线

在“目标”层上创建一个名为“瞄准/射击”的新层。通过按(Ctrl + F8)创建一个新的电影剪辑，并将其命名为“十字准线”。该符号应该已经打开编辑，所以创建一个额外的层，并将其命名为“行动”。在第 1 帧中，添加一个停止动作:

```
stop();
```

将原始图层命名为“Image ”,并将十字线图像导入第 1 帧，将其与舞台中心对齐(用十字表示)。现在，通过创建新的关键帧(F6)，将此图像复制到第 2 帧和第 3 帧。在第 2 帧上，将图像放置在比其他两个图像高几个像素的位置，就好像子弹已经射出一样。这里需要注意的是，如果在第 2 帧的新图层上也导入声波，十字线会更加真实。互联网上有很多这方面的资料，但一个好的起点是 www.flashkit.com。

点击时间线下方的“场景 1”链接，返回主时间线。现在，从库中拖动一个 crosshairMC 实例，并将其放置在舞台的左侧。在属性检查器的“实例”字段中，选择 MC 并将其命名为“crosshair”。然后，打开“操作”面板，输入以下内容:

```
(ActionScript (AS) for the cross-hair)   

onClipEvent(enterFrame) { //When this frame(start)of the   

            //movie is entered place this   

           //MC wherever the mouse is.   

this._x = _root._xmouse;   

this._y = _root._ymouse;   

}   

//-------------------------------------------------------//
```

##### 创造子弹

这是整个教程中最简单的部分。按照上述步骤创建新符号。创建一个新的符号，并将其命名为“子弹”。然后，当你编辑 MC 时，你所要做的就是用画笔在第一帧上创建一个黑色的小弹孔图像。不一定要很整齐。选择这个，把它做成一个电影剪辑(F8)，并把它命名为“弹孔”。单击时间轴的第 30 帧，并按(F5)键填充其间的所有帧。然后点击(F6)使第 30 帧成为关键帧。选中第 30 帧中舞台上的图像，转到属性检查器，将颜色更改为“Alpha”和“0%”。

现在，右键单击时间轴上 1 到 30 之间的任何帧，并选择“创建补间动画”。这将补间帧，以便当影片剪辑播放到第 30 帧时，项目符号将慢慢消失。如果您不希望这种情况发生，只需将子弹图像放在第 1 帧中，并保持不变。

现在，创建一个新层，命名为“行动”。点击该层时间轴中的第 30 帧，创建一个新的空白关键帧(F7)。选择“动作”面板并输入停止动作:

```
stop();
```

像以前一样，通过单击“场景 1”链接返回到主时间轴，然后将子弹的一个实例拖离十字线附近的舞台左侧。

选择舞台上的项目符号实例，将该实例命名为“项目符号”，然后在“动作”面板中输入以下内容:

```
(ActionScript (AS) for the bullets)   

onClipEvent(mouseDown) {   

  if(!_root.lastFrame) { //if not in the lastFrame   

  i++; //increase the number of i each time so that the   

    //duplicateMCs have a new depth to load to.   

  this.duplicateMovieClip(\\"bulletnew\\", i); //create new   

                   //duplicate   

                   //bulletMC   

  _root.crosshair.play(); //play the crosshair movie   

  }   

}   

onClipEvent(load) { //show bullets wherever mouse is as   

        //each new mc is loaded. Initial bullet   

        //as game loads won't show as it is   

             //hidden in the actions for the    

        //first frame.   

  if(!_root.lastFrame) {   

    this._x = _root._xmouse;   

    this._y = _root._ymouse;   

  }   

}   

//-------------------------------------------------------//
```

##### 目标的 ActionScript

还和我在一起吗？你的电影不会像现在这样鼓舞人心。我们需要做的是开始向目标添加一些行动。

ActionScript 对所有目标几乎都是一样的，所以您可以简单地剪切和粘贴。请注意，当使用“目标 2”或“目标 3”的代码时，您需要找到“目标 1”并用“目标 2”或“目标 3”替换。这些都在代码中突出显示，以帮助您。您还需要更新“activeTarget”变量。

左键单击主舞台上 TargetMC 的“Target1”实例，并打开动作面板(窗口菜单-动作或(F9 [pc])。将以下代码复制到其中。

```
(AS for individual targets)    

onClipEvent(mouseDown) {    

hitsNeeded = 5; //hits needed to destroy target - this    

      //can be any number you want    

//**CHANGE target1.hitTest, target1Hits and activeTarget to read target2 //or target3 for other targets and target1 to read 2 or 3 as in the //array we set up.**    

  if(_root.target1.hitTest(_root._xmouse, _root._ymouse, true) && (_root.target1Hits<hitsNeeded) && (_root.activeTarget == \\"target1\\")) {    

  //OK From the top on this one:    

      //Using the AS hitTest() method    

      //If the specified MC is clicked on and    

      //if the hits needed on this MC have not    

      //reached the maximum and    

      //*if* it is the active target (activeTarget               

//**CHANGE target1Hits to read target2Hits or target3Hits for other //targets**    

    _root.target1Hits++; //increase MC hits variable by 1    

    _root.directHits++; //increase direct hits variable    

    _root.hits.text = _root.directHits+\\" of \\"+hitsNeeded+\\" needed.\\";     //display current hits for target in a text    

      //field on the main stage    

  highScore = _root.highScore;    

  highScore = highScore + 10; //increase high score by                  //however many points you                  //want for a hit.    

  _root.info.text = highScore;    

  _root.highScore = highScore; //update the variable on    

                //the main stage    

//**CHANGE target1Hits to target2Hits or target3Hits for other //targets**    

  if(_root.target1Hits == hitsNeeded) { //if hits needed total    

                //is reached    

_root.directHits = 0; //reset direct hits counter for future           

    //targets    

  //**CHANGE to target2Image or target3Image for other targets**    

  setProperty(_root.target1Image, _visible, false);     

//hide target    

  //**CHANGE to target2d or target3d for other targets**    

     setProperty(_root.target1d, _visible, true);    

      //Show the target destroyed image    

         //Check if array is now empty    

      //If so, endgame    

  n = _root.targets.length;    

  if(n == 0) {    

    _root.gotoAndPlay(2);    

  }                    

  //If it wasn't empty...    

  //generate a random target    

  n = _root.targets.length //get the current array    

//length    

  ran = random(n); //get random number location to     

          //select from array    

  target = _root.targets[ran]; //create target variable    

  _root.activeTarget = target; //set active target    

  setProperty(\\"_root.\\"+target+\\"Image\\", _visible, true);    

                //show target    

  _root.targets.splice(ran, 1); //remove the random    

              //element from array    

                 //so that target cannot    

              //be picked again in     

              //this game    

  _root.hits.text = \\"target destroyed!\\"; //update the    

                    //text field    

    }    

}    

}    

//-------------------------------------------------------//
```

在你准备好测试游戏和添加你自己的元素之前，只有两件事要做！正如我在本教程开始时提到的，你将需要每个目标的另外 2 个图像，以显示每个目标何时活跃，何时被摧毁。对于本教程，我们将只使用简单的盒子，如前所述。

##### 创建活动目标图像

这个又好又简单！只需创建一个名为“活动”的新电影剪辑，然后创建一个足够大的红色矩形，用透明填充覆盖目标。

在目标层上创建一个名为“活动目标”的新层，并将此影片剪辑的三个实例放在第 1 帧中目标的顶部。在属性检查器中，分别将实例命名为“target1Image”、“target2Image”和“target3Image”。

创建目标破坏图像

这个和上面的差不多，真的。创建一个新的电影剪辑称为“摧毁”，然后创建一个新的层以上的最后一个，称为“摧毁的目标”。添加 3 个实例，分别称为“target1d”、“target2d”和“target3d”。

##### 就是这样！除了…

如果你已经完成了本教程，你应该已经具备了开始玩游戏的所有要素。您可能希望添加一两个其他元素，如“再次播放”按钮，这可以像使用 Flash 捆绑的公共库中的标准按钮一样简单(窗口-公共库-按钮)。将按钮放在第 2 帧上，然后选择它，并将以下内容插入动作面板:

`on (press) {    
 prevFrame();    
}`

在这个过程中，你可能已经想到了可以添加到游戏中的东西——你在这里学到的只是开始。祝游戏愉快！

## 分享这篇文章