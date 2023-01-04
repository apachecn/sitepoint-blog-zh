# Flash 脚本–星空效果

> 原文：<https://www.sitepoint.com/script-star-field-effect/>

 **<object type="application/x-shockwave-flash" data="https://i2.sitepoint.com/flash/starfield.swf" width="400" height="400" bgcolor="black"><param name="movie" value="https://i2.sitepoint.com/flash/starfield.swf"></object>**

创造一个星域效果很容易！首先，[在这里下载示例文件](https://www.sitepoint.com/examples/flashscript/starfield.zip)。

**1。**创建一个小星星形状，并将其转换为电影剪辑。给它一个实例名 star。

**2。**将现有图层扩展三帧。然后，在现有图层上新建一个图层，在这个图层上创建三个空白帧。

**3。**在第一层，插入动作:

```
numstars = 100; 

speed = 5; 

star_field = new Array (numstars); 

//x is x axis, y is y axis, speed is speed of star 

function star(x,y,z) 

{ 

  this.x = x; 

  this.y = y; 

  this.speed = speed; 

} 

for (i=0;i<numstars;i++) 

{ 

 star_field[i] = new star(random(300),random(200),random(speed)+1); 

 duplicateMovieClip ("star", "star_" add i, i+10); 

}
```

**4。**在第二个关键帧中，插入这个动作:

```
for (i=0;i<numstars;i++) 

{ 

 star_field[i].x+= star_field[i].speed; 

 if (star_field[i].x>300) { star_field[i].x = 0; } 

 setProperty ("star_" add i , _x , star_field[i].x ) 

 setProperty ("star_" add i , _y , star_field[i].y ) 

 setProperty ("star_" add i , _alpha , star_field[i].speed*40 ) 

}
```

**5。**最后，在第三段中插入:

```
gotoAndPlay (2);
```

就是这样！测试电影！

## 分享这篇文章