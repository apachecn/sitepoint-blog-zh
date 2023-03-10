# 如何使用 JavaScript 存储 Adobe AIR 应用程序首选项

> 原文：<https://www.sitepoint.com/adobe-air-pref-javascript/>

在我的上一篇文章中，我非常兴奋地开始了我的第一个 AIR 应用程序，名为 Harpoon，仅使用 HTML、CSS 和 JavaScript。我没有时间讨论的一个特性是如何使用 XML 文件存储和检索应用程序参数。

如果您下载并安装了 Harpoon，您可能会注意到带有选择列表的首选项面板。

![The Harpoon Preferences panel](img/4afdc6edf54659f2bbc135673c3f01ea.png)

以下是该字段及其标签的 HTML:

```
<label>Refresh every (minutes)</label> 

<select id="pref_refresh" name="pref_refresh"> 

  <option value="1">1</option> 

  <option value="2">2</option> 

  <option value="3">3</option> 

  <option value="4">4</option> 

  ... 

  <option value="10">10</option> 

</select>
```

如果你还记得的话，Harpoon 注定是一个 [Flippa](http://flippa.com/) 网站的拍卖观看应用。最终，这将控制 Flippa 多久检查一次与您的查询匹配的新拍卖。当然，您希望 Harpoon 记住这个设置，所以我们将把选择的值存储在一个 XML 文件中。

我们的任务是这样的:

*   当 Harpoon 打开时，我们检查首选项文件是否可用。
*   如果不可用，Harpoon 应该创建文件并写入默认值。
*   如果可用，Harpoon 应该读取它并设置字段值。
*   每当字段值改变时，Harpoon 都应该将新值写入首选项文件。

这听起来比实际要复杂得多。我们开始吧，这样我就可以给你看了。

首先对我们的`Harpoon`对象进行一些调整。(如果你需要刺激你的记忆，绕道而行，[参考我以前的文章](http://https://www.sitepoint.com/article/css-desktop-adobe-air/)。)我们希望在`Harpoon`对象中存储刷新值和一些其他重要的值:

```
var Harpoon = { 

  ... 

  prefsFile : null, 

  storePath : null, 

  prefs : { 

    refresh : 10 

  } 

}
```

一会儿我会对`prefsFile`和`storePath`进行寻址，但是您也可以看到具有一个名为`refresh`的属性的`prefs`对象。这是我们存储刷新值的地方，我们可以从`Harpoon.prefs.refresh`的应用程序中访问它。

现在我们回到`prefsFile`和`storePath`属性。出于安全原因，每个 AIR 应用程序都有自己的存储目录，可以从中读取和写入文件。这是我们的首选项文件将驻留的地方。这两个属性将存储应用程序存储目录的路径和首选项文件的路径。

如果您还记得上一篇文章，Harpoon 有一个用于初始化的`init`函数，我们在这里调用了`setupWindow`函数。我们将添加几行代码:

```
init : function() { 

  Harpoon.setupWindow(); 

  Harpoon.storePath = air.File.applicationStorageDirectory; 

  Harpoon.prefsFile = Harpoon.storePath.resolvePath("prefs.xml"); 

  Harpoon.getPrefs(); 

  ... 

},
```

我们使用`air.File.applicationStorageDirectory`来获取 Harpoon 的存储目录。然后，我们使用`resolvePath`方法检索首选项文件`prefs.xml`的路径，并将其存储为一个`File`对象。下一行，`Harpoon.getPrefs()`，是对我们将要编写的一个新函数的调用。

如果文件存在，`getPrefs`函数将从文件中检索应用程序首选项，否则将创建一个新文件。默认首选项文件如下所示:

```
<?xml version="1.0" encoding="utf-8"?> 

<preferences> 

  <refresh>10</refresh> 

</preferences>
```

下面是`getPrefs`函数:

```
getPrefs : function() { 

  if (Harpoon.prefsFile.exists) { 

    ... 

  } else { 

    ... 

  } 

},
```

首先，我们使用保存在`init`函数中的`File`对象的`exists`属性，检查首选项文件是否存在。如果它确实存在，那么我们使用一个自定义函数读取内容，您马上就会看到这个函数叫做`readXML`——基本上它接受一个文件路径并返回一个 XML 文档:

```
 if (Harpoon.prefsFile.exists) { 

    var xml = Harpoon.readXML(Harpoon.prefsFile);
```

接下来，我们使用一些 jQuery 选择器技巧在 XML 文档中找到第一个`refresh`元素，并将其文本值作为一个整数存储在我们的`Harpoon`对象中:

```
 Harpoon.prefs.refresh = parseInt($(xml).find('refresh:eq(0)').text());
```

jQuery 中的`:eq(index)`是一个选择器过滤器，通过索引匹配元素。在上面的代码中,`refresh:eq(0)`将匹配第一个`refresh`元素。

这个分支的最后一步是设置选择字段的值，同样使用 jQuery:

```
 $('#pref_refresh option[value=' + Harpoon.prefs.refresh + ']').attr("selected","selected");
```

另一方面，如果没有首选项文件，我们希望创建一个:

```
 } else { 

    Harpoon.savePrefs(); 

  }
```

`savePrefs`函数是我们的另一个自定义函数:

```
savePrefs : function() { 

  var cr = air.File.lineEnding; 

  var prefsXML =   '<?xml version="1.0" encoding="utf-8"?>' + cr 

      + '<preferences>' + cr  

      + '  <refresh>' + Harpoon.prefs.refresh + "</refresh>" + cr 

      + '</preferences>'; 

  Harpoon.writeXML(Harpoon.prefsFile, prefsXML); 

},
```

这是一个简单的函数，它构建一个表示 XML 标记的字符串，并使用尚未看到的`writeXML`函数将其保存到首选项文件中。`Harpoon.prefs.refresh`的当前值被连接成 XML 字符串。你可能会注意到在上面的函数中使用了`air.File.lineEnding`；这是一个表示平台行结束字符的属性，在 Mac OS X、Windows 和 Linux 上是不同的。

现在来看看那些之前命名为`readXML`和`writeXML`的函数。在 Adobe AIR 中，如果你想用 JavaScript 读写文件，你需要使用一个`FileStream`对象。首先，`readXML`文件创建一个新的`FileStream`对象，并在读取模式下打开我们传递给它的`File`对象的首选项文件。`FileStream`作为字符串读取，存储在`xml`变量中，并关闭:

```
readXML : function(path) { 

  var stream = new air.FileStream(); 

  stream.open(path, air.FileMode.READ); 

  var xml = stream.readUTFBytes(stream.bytesAvailable); 

  stream.close();

Next we use a DOMParser object to convert the string into an XML document and return it:

```
 var domParser = new DOMParser(); 

  xml = domParser.parseFromString(xml, "text/xml"); 

  return xml; 

},
```

`writeXML`函数也使用一个`FileStream`对象，但是以写模式打开它。该函数采用一个`File`对象和 XML 源字符串，并将该字符串写入文件:

```
writeXML : function(path, xml) {   

  var stream = new air.FileStream(); 

  stream.open(path, air.FileMode.WRITE); 

  stream.writeUTFBytes(xml); 

  stream.close(); 

},
```

我们忘记什么了吗？还有一个任务。当用户改变界面中的参数值时，我们希望 Harpoon 保存这个参数值。它只需要一个简单的事件监听器:

```
$('#pref_refresh').change(function(){ 

  Harpoon.prefs.refresh = this.options[this.selectedIndex].value; 

  Harpoon.savePrefs(); 

});
```

上面的代码绑定了一个在每次选择列表值改变时执行的函数。该函数存储新的偏好值并调用`savePrefs`函数。我们将它添加到我们在第一篇文章中创建的`setupWindow`函数中。
现在我们有了。在 Adobe AIR 应用程序中使用 JavaScript 读写 XML 文件的首选项:简单！可以下载[源代码](https://i2.sitepoint.com/examples/harpoon/Harpoon.zip)以及[打包应用](https://i2.sitepoint.com/examples/harpoon/Harpoon.air)。希望这已经为您提供了足够的代码，让您可以在自己的 AIR 应用程序中实现首选项。

```

## 分享这篇文章