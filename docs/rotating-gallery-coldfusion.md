# 在 ColdFusion 中建立旋转画廊

> 原文：<https://www.sitepoint.com/rotating-gallery-coldfusion/>

他们来了，他们买了…但是他们回来了吗？一个电子商务网站最重要的访问者是那些真正放弃塑料的人，一旦你有了一个在线的活生生的人，你需要他们回来——并带上他们的朋友。怎么会？嗯，购物者也是人，人们喜欢感觉自己是某样东西的一部分。为什么不通过创建图片库让你的客户成为你网站的一部分呢？

我知道你在想什么:像这样的画廊往往会变得太大，当它们变大时，它们就开始失去吸引力。我的一个客户最近提出了一个有趣的解决方案:让画廊旋转。

##### 坐在你的车里旋转

我的客户希望他们的画廊一次只展示 12 幅图片，并且每天轮换出一个旧项目，一个新项目。这个客户销售街道杆零件和套件，他们的客户喜欢发送他们自己在 T 型桶的油门上，在公司停车场弹出车轮和惊吓祖母的照片。这里有一个例子展示了它的实际应用。此示例设置为每秒旋转一次，因此刷新几次浏览器以查看它的运行情况。

于是，我一边听着艾瑞克·达菲的音乐，一边在我住的弯弯曲曲的乡间小路上开车，这时我想出了如何让它运转起来。我打算在数据库中使用一个表，所以客户端可以通过管理部分添加图片。我们需要一种方法来每天向前移动一条记录，而不考虑记录数。我也可以看到，从最后一条记录到第一条记录会有重叠，需要消除。

##### 摇那个么

我想到，如果我有一个每天递增的大数字，我可以用它除以 recordcount 来计算从哪个记录开始。例如，如果我们计算自 2000 年 1 月 1 日以来的天数(让我们称之为 ticker ),然后我们将 ticker 除以 recordcount，余数将总是介于 0 和比 recordcount 小 1 之间。因为我们的心脏会稳定地增加(如果太阳升起)，我们的剩余部分也会以同样的速度增加。如果我们愿意，我们可以通过调整滚动条的计算方式，让图库每小时、每分钟或每秒旋转一次。

##### 入门指南

此示例假设您对 HTML 和 ColdFusion 语言有基本的了解。如果你刚刚开始接触 ColdFusion，请点击这里查看 Chris Beasley 的优秀 3 集系列。

ColdFusion Server 5 现在对开发者免费开放；30 天试用期过后，试用版将成为单用户许可证。你可以在这里下载[。](http://www.macromedia.com/software/coldfusion/downloads/)

你需要这个压缩文件。

它包含:

*   rotating_gallery.cfm ColdFusion 模板
*   site point _ gallery . MDB Microsoft Access 数据库
*   26 个缩略图(。gif)

该数据库只包含一个名为“gallery”的表。其中的字段包括:

*   ID(自动增量，主键)
*   img _ name 图像的文件名(12.gif)
*   img _ x-图像宽度
*   img _ y-图像高度
*   img _ title 与此图像一起显示的文本

有 26 条记录，26 个缩略图各有一条。

解压缩到映射到 ColdFusion 服务器的文件夹。在 ColdFusion studio 中按 Alt-M 打开映射窗口，如果需要帮助，请阅读 ColdFusion 文档。您还需要创建一个名为'`sp_gallery`'的系统 DSN，它指向 access `.mdb`文件。同样，如果您需要帮助，请尝试文档。

将浏览器指向“`rotating_gallery.cfm`”文件。你应该会看到一个 12 张编号图片的小图库，类似于上面的例子[。](http://www.morgankelsey.com/sitepoint/rotating_gallery.cfm)

##### 拿出你的气枪

我们来看看'`rotating_gallery.cfm`'。

```
<cfprocessingdirective suppresswhitespace="Yes">  

<!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 4.0 Transitional//EN">  

<html>  

<head>  

  <title>Rotating Gallery</title>  

  <META HTTP-EQUIV="Content-Type" CONTENT="text/html;  

  charset=ISO-8859-1">  

</head>  

<body>
```

像其他任何页面一样开始页面，`cfprocessingdirective`标签有助于抑制下面循环产生的额外空白。当模板被解析并发送到浏览器时，这使得文件更小，从而加快了访问者的体验。

```
<cfquery name="qGallery" datasource="sp_gallery"   

cachedwithin="#CreateTimeSpan(0,0,10,0)#">  

  SELECT ID,pic_thumb,pic_x,pic_y,pic_title FROM gallery  

  ORDER BY ID;  

</cfquery>
```

我们运行一个简单的查询来检索 gallery 表中的所有记录。

```
<!--- initialize variables --->  

<cfset start_day = DateDiff("y","01/01/2000",Now())>
```

这是我们的股票价值，我们需要每天递增。我们使用 DateDiff 函数来告诉我们从 2000 年 1 月 1 日到今天已经过去了多少天(“`y`”)，并将其赋给变量“`start_day`”。该值必须大于您的 recordcount，因此如果您有大量项目需要循环，请将日期向后推。

```
<cfset total_items = 12>
```

将`total_items`设置为页面上显示的项目总数。我们将使用这个变量来确定停止点。

```
<cfset start_item = (start_day MOD qGallery.recordcount) + 1>
```

变量`start_item`将在第一个位置保存该项目的值。我们通过将变量`start_day`除以查询中的记录数的余数加 1 来找到它。为了找到除法方程的余数(或模数)，我们使用了`MOD`数学函数。ColdFusion (5 MOD 2)返回 1，即 5 / 2 的余数。模数总是比除数小的整数。想想看，任何一个数除以 4 总会产生一个余数(或模数)0、1、2 或 3。

```
<cfset end_item = start_item + (total_items - 1)>
```

我们还需要知道在哪个记录处停止，我们可以通过从'`total_items`'变量中减去 1 并将该值添加到'`start_item`'变量中来轻松找到它。我们将这个结果赋给变量“`end_item`”。

```
<cfif end_item GT qGallery.recordcount>  

  <cfset end_item = end_item - qGallery.recordcount>  

</cfif>
```

如果`start_item`是记录 20，只有 23 条记录会怎么样？我们把 11 相加得到 31！我们需要处理从最后一条记录到第一条记录的重叠部分。我们仍然需要知道在哪个记录处停止，所以如果'`end_item`'变量大于 recordcount，我们从'`end_item`'值中减去 recordcount，以找到正确的'`end_item`。

```
<cfset columns = 4>
```

将'`columns`'变量设置为所需的表格列数。

```
<cfset col_tick = 0>
```

当我们输出每一行中的表格单元格时，我们将递增'`col_tick`'变量。

```
<cfset count = 0>
```

Count 也会递增，当它达到我们的`total_items`变量的值时，我们就结束了。

现在我们的查询已经运行，所有必要的变量都已初始化。继续有趣的部分:建造画廊！

```
<div align="center">   

<table width="70%" border="0" cellpadding="0" cellspacing="0">    

<tr>
```

我们从一个表开始，打开一行。

```
<cfif start_item LT end_item>
```

如果我们的`end_item`值高于启动汽车，我们可以执行一个非常简单的输出。如果没有，我们必须处理从最后一条记录到第一条记录的重叠部分。

为了 startrow 和 endrow 属性提供的清晰性，我在下面使用了`CFLOOP`。然而，这也可以通过`CFOUTPUT`来实现。

```
<cfloop query="qGallery" startrow="#start_item#"    

endrow="#end_item#">
```

我们将`startrow`和`endrow`属性设置为我们的`start_item`和`end_item`值。

```
<cfset col_tick = IncrementValue(col_tick)>    

<cfset count = IncrementValue(count)>
```

我们将使用 count 变量跟踪输出的项目总数，当它与`total_items`匹配时，我们就完成了。`col_tick`变量用于计算表格的列数，当`col_tick`等于列数时，我们需要开始一个新的表格行并将`col_tick`重置为零。

```
<cfoutput>
```

我们打开一个`cfoutput`标签，因为我们知道需要将我们的变量值打印到浏览器中。

```
 <td width="40"><img src="#pic_thumb#" height="#pic_y#"    

  width="#pic_x#" alt="#pic_title#"></td>
```

最后，我们将 img 标签写入浏览器。

```
 <cfif count EQ total_items>   

    </tr></table>   

  <cfelseif col_tick EQ columns>   

    </tr><tr><cfset col_tick = 0>   

  </cfif>
```

这里我们将计数变量与`total_items`变量进行比较。当它们匹配时，我们就完成了，需要关闭我们的表。如果不为真，我们检查我们的`col_tick`变量是否匹配我们的 columns 变量。如果它们匹配，我们需要关闭当前的表行并开始一个新的表行。

```
</cfoutput>
```

关闭我们的`cfoutput`标签。

```
</cfloop>
```

关闭循环。

`<cfelse><!--- if start_item LT end_item --->`

如果我们的`end_item`值小于`start_item`值，我们将需要处理从最后一条记录到第一条记录的重叠部分。我们可以通过输出两次查询结果来做到这一点。一次到记录集的末尾，然后从记录集的开头到`end_item`值。

除了 cfloop 标签中的`startrow`和`endrow`值之外，下面的代码与上面的代码相同。

对于第一个输出，我们如上所述将`startrow`设置为`start_item`值，但是这次我们将`endrow`值设置为与查询中的`recordcount`相匹配。此外，我们不需要`<cfif count EQ total_items>`语句，因为我们知道这个循环不会完成画廊。

```
 <cfloop query="qGallery" startrow="#start_item#"    

  endrow="#qGallery.recordcount#">   

    <cfset col_tick = IncrementValue(col_tick)>   

    <cfset count = IncrementValue(count)>   

    <cfoutput>   

      <td width="40"><img src="#pic_thumb#" height="#pic_y#"   

      width="#pic_x#" alt="#pic_title#"></td>   

        <cfif col_tick EQ columns>   

          </tr><tr><cfset col_tick = 0>   

        </cfif>   

    </cfoutput>   

  </cfloop>
```

第二个输出将是从记录集的开始到`end_item`值。

` <cfloop query="qGallery" startrow="1" endrow="#end_item#">    
   <cfset col_tick = IncrementValue(col_tick)>    
   <cfset count = IncrementValue(count)>    
   <cfoutput>    
     <td width="40"><img src="#pic_thumb#" height="#pic_y#"    
     width="#pic_x#" alt="#pic_title#"></td>    
       <cfif count EQ total_items>    
         </tr></table>    
       <cfelseif col_tick EQ columns>    
         </tr><tr><cfset col_tick = 0>    
       </cfif>    
   </cfoutput>    
 </cfloop>    

</cfif><!--- close if start_item LT end_item --->`

现在我们结束 if 语句。

```
</div>    

</body>    

</html>    

</cfprocessingdirective>
```

关闭我们的页面，我们就完成了！

##### 考虑

这种代码可以很容易地应用于文本块或横幅。如果您希望它每隔一段时间而不是每天轮换一次，请调整以下代码行:

```
<cfset start_day = DateDiff("y","01/01/2000",Now())>
```

“`y`”返回 2000 年 1 月 1 日到现在的天数。要使画廊每小时旋转一次，请将“`y`”更改为“`h`”。有关`DateDiff`功能的完整讨论，请参见 ColdFusion 文档。

我们的循环内容都非常相似，如果我们保留在第二组输出中忽略的 if 语句，我们可以将这段代码放入包含文件中。

向数据库中添加更多的字段，您可以让缩略图变成更大的图片，并伴有描述性段落。

经过一些修改，这可以成为一个伟大的自定义标签！我用它来轮换其他网站的销售项目。随时修改代码，让我知道你想出了什么！

##### 结论

我们已经让我们的客户满意了，并且创造了一个更好的画廊。访问者在网站上看到自己照片的兴奋感只会因为照片会在网站上停留一段时间，消失一段时间，然后再回来而增加。如果你刚刚花了一年时间和 20 亿美元建造了一个带有尖叫动力装置和韦伯堆栈的太空管底盘 1923 T-Bucket，难道你不想向你的朋友展示你发布它的网站吗？客户现在正在为网站所有者进行有针对性的营销！

现在前进并繁衍——或者说，分而治之。

## 分享这篇文章