# Flash 脚本–创建重复的电影剪辑

> 原文：<https://www.sitepoint.com/create-duplicate-movieclips/>

 **<object type="application/x-shockwave-flash" data="https://i2.sitepoint.com/flash/duplicatemovie.swf" width="400" height="400" bgcolor="black"><param name="movie" value="https://i2.sitepoint.com/flash/duplicatemovie.swf"></object>**

[在此下载示例文件](https://www.sitepoint.com/examples/flashscript/duplicatemovie.zip)。

请注意，本教程假设您知道如何创建按钮和文本字段。

**1。**首先创建一个按钮，标签为“制作克隆”。

**2。**创建一个文本字段，如上图所示。给它一个变量名“no_clones”。

**3。**创建您选择的电影剪辑，我们可以复制它。我创建了一个带有渐变的圆形对象。

**4。**给影片剪辑一个实例名“克隆”。

**5。**现在，在默认层的第一个关键帧，插入这个动作:

```
// set variable clips=value in text field. 

clips = no_clones;
```

**6。**右键单击按钮并插入这些动作:

```
/on (release)  

{ 

// get the current x pos of the clone movie 

mov_x = getProperty("/clone",_x); 

//Check if the text field is empty if empty set it to 1 

if(no_clones == "") 

{ 

no_clones = 1; 

} 

// Use for loop to remove existing movie clip 

for(i=1; i<=clips; i=i+1) 

{ 

removeMovieClip ("/clone" add i); 

} 

// Use for loop to create duplicate movies. 

for (i=1; i<=no_clones; i=i+1) 

{ 

mov_x = mov_x + 50; 

duplicateMovieClip ("/clone", "clone" add i, i); 

setProperty ("/clone" add i, _x, mov_x); 

} 

//set variable clips = no_clones. 

clips = no_clones; 

}
```

我已经对上面的脚本进行了注释，以便您更容易理解。但这就是你需要做的:是时候测试电影了。

学习在 flash 中创建重复的电影剪辑非常有用。它可以用来创造很多效果——快乐的实验！

## 分享这篇文章