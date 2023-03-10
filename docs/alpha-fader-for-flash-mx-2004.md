# Flash MX 2004 的 Alpha 推子

> 原文：<https://www.sitepoint.com/alpha-fader-for-flash-mx-2004/>

![](img/c82149cad53d6cd183cc179154d7b001.png)

[花了几秒钟创建的编码示例示例！](https://www.phireworx.com/sitepoint/blog/25082004/25082004.html)

通过 ActionScript 在 Flash 中完成的最常见的任务之一是不透明度渐变，如果巧妙地使用，可以为您的 Flash 项目增添一抹亮色。

我倾向于通过 ActionScript 大量使用不透明淡入淡出，并且厌倦了键入电影剪辑的名称和添加控制代码。说我懒？不，我只是想转移到项目的更大部分，在项目的整个运行过程中，这些在这里和那里节省下来的宝贵时间真的会累积起来。

我创建了这个面板，以减轻我的痛苦，它做得很好，只需从舞台中选择一个或一组电影剪辑(不管它们是否是命名的实例，因为代码会处理未命名的实例)，并使用以下参数进行播放

事件处理程序复选框:与电影剪辑的交互类型
开始不透明度:效果的开始不透明度
结束不透明度:效果的结束不透明度
淡入速度:淡入速度
淡出速度:淡出速度

一旦你对你的设置满意，点击“生成代码”按钮，复制外部化的。作为文件，我们使用它将电影剪辑原型代码保存到您正在使用的 FLA 的工作目录中，并将必要的代码添加到当前选定的帧中。

使用这种方法和扩展，只需要 2-3 秒就可以将这一大堆电影剪辑变成很好的褪色电影剪辑…

![](img/8c76fc55a6d9b72ce040491a66836139.png)

…消除了将这些代码添加到框架中的麻烦，因为这一切都是自动完成的。

 `#include "alpha.as"
Symbol3.onRollOver = function(){
this.alpha(5,100);
}
Symbol3.onRollOut = function(){
this.alpha(5,10);
}`

 `symbol 6 . onrollover = function(){
this . alpha(5，100)；
}
symbol 6 . onrollout = function(){
this . alpha(5，10)；
}

symbol 9 . onrollover = function(){
this . alpha(5，100)；
}
symbol 9 . onrollout = function(){
this . alpha(5，10)；
}

symbol 12 . onrollover = function(){
this . alpha(5100)；
}
symbol 12 . onrollout = function(){
this . alpha(5，10)；
}

symbol 2 . onrollover = function(){
this . alpha(5，100)；
}
symbol 2 . onrollout = function(){
this . alpha(5，10)；
}

symbol 5 . onrollover = function(){
this . alpha(5，100)；
}
symbol 5 . onrollout = function(){
this . alpha(5，10)；
}

symbol 8 . onrollover = function(){
this . alpha(5，100)；
}
symbol 8 . onrollout = function(){
this . alpha(5，10)；
}

symbol 11 . onrollover = function(){
this . alpha(5100)；
}
symbol 11 . onrollout = function(){
this . alpha(5，10)；
}

symbol 10 . onrollover = function(){
this . alpha(5100)；
}
symbol 10 . onrollout = function(){
this . alpha(5，10)；
}

symbol 7 . onrollover = function(){
this . alpha(5，100)；
}
symbol 7 . onrollout = function(){
this . alpha(5，10)；
}

symbol 4 . onrollover = function(){
this . alpha(5，100)；
}
symbol 4 . onrollout = function(){
this . alpha(5，10)；
}

symbol 1 . onrollover = function(){
this . alpha(5，100)；
}
symbol 1 . onrollout = function(){
this . alpha(5，10)；
}

符号 3。_ alpha = 10

符号 6。_ alpha = 10

符号 9。_ alpha = 10

符号 12。_ alpha = 10

符号 2。_ alpha = 10

符号 5。_ alpha = 10

符号 8。_ alpha = 10

符号 11。_ alpha = 10

符号 10。_ alpha = 10

符号 7。_ alpha = 10

符号 4。_ alpha = 10` 

`Symbol1._alpha = 10;`

阿尔法推子可以在这里下载>>，希望你觉得有用！

## 分享这篇文章