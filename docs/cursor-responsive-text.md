# Flash 脚本–光标响应文本

> 原文：<https://www.sitepoint.com/cursor-responsive-text/>

****1。**创建一个文本字段，将其命名为“txt”，然后将其转换为 movieclip。**

**2。**给影片剪辑一个实例名“a1”。

![1065_tool1](img/ddb82454d2f8c957a31de93d97151619.png)

**3。**创建一个新的 movieclip，并给它一个实例名“a0”。

**4。**如下图创建图层。

![1082_tool2](img/fb80bb3facaa6014a756e5c68014a449.png)

**5。**在动作层的第一个关键帧中，插入以下动作:

```
x = new Array();  

y = new Array();  

text = "Flashcircle.com";  

// mx x value of movieclip  

mx = getProperty("/a1", _x);  

txtlength = length(text);  

/a1:txt = "F";  

//loop through to create duplicate movieclip  

for (i=2; i<=txtlength; i++)  

{  

duplicateMovieClip ("/a1", "a" add i, i);  

setProperty ("/a" add Number(i), _x, mx);  

// substring to put individual letter into movieclip  

set ("/a" add Number(i) add ":txt", substring(text, i, 1));  

} 
```

**6。**在动作层的第三个关键帧中，插入这些动作:

```
//code to make the text follow  

for(i=1; i<=txtlength; i++)  

{  

x[i] = getProperty("/a" add Number(i-1),_x);  

y[i] = getProperty("/a" add Number(i-1),_y);  

x[i] = x[i]+10;  

} 
```

**7。**在动作层的第四个关键帧中，插入以下内容:

```
//code to make the text follow  

for (i=1; i<=txtlength; i++)  

{  

setProperty ("/a" add i, _x, x[i]);  

setProperty ("/a" add i, _y, y[i]);  

}  

gotoAndPlay (2); 
```

**8。**右键单击“a0”实例电影剪辑，并插入这些动作:

```
onClipEvent (load)  

{  

startDrag ("", true);  

} 
```

**9。很简单，不是吗？按 ctrl+enter 测试您的电影。**

## 分享这篇文章