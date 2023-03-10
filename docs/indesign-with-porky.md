# 使用 Porky 在 InDesign 中显示数据集

> 原文：<https://www.sitepoint.com/indesign-with-porky/>

作为一名多年的程序员，我坚信一次写完所有的东西，就可以在多个地方使用它。在编码领域，由于开放的数据格式和广泛支持的 API，我们通常可以有一个集中的内容集，并将其用于不同的应用程序和用例，所有这些都很好地同步。

在桌面设计领域，这在传统上更难，因为内容来源是 Word 文档、InDesign 文件，可能还有一些内容管理系统或电子表格。

想象一下当我偶然看到 [Porky](http://porky.io/) 时我的喜悦，这是一个开源的 InDesign 扩展(嗯，实际上是一个脚本)，它可以让你将你的 InDesign 文件连接到各种数据源。

## 装置

[下载当前版本的脚本](https://github.com/Schreiber-und-Freunde/porky/archive/master.zip)，将其解压缩，并将文件夹添加到 InDesign *脚本/脚本面板*文件夹中(在我的 Mac 上，这是在 *InDesign 应用程序包*中)，Porky 现在应该会显示在 InDesign 脚本面板中。

![Scripts Panel](img/a983dad0919296541bac4d35d046f182.png)

脚本面板

Porky 需要在你的本地计算机上安装 node.js，[完整的说明可以在这里找到](https://nodejs.org/en/)。当你安装了它，我们可以安装一些 Porky 需要的 node.js 的附件来处理它所支持的各种服务的连接。通过打开命令行并发出这两个命令来安装它们(这适用于安装了 InDesign CC 的 Mac，路径会因您的平台和版本而异):

```
cd /Applications/Adobe\ InDesign\ CC\ 2015/Scripts/Scripts\ Panel/porky
npm install
```

## 开始吃猪肉

双击脚本面板中的*start-porky-data-source-access . jsx*,这可能会打开一些终端窗口，现在就保留它们。

![Start Porky](img/7e91fe23baa1b219213fc16e35ab11bc.png)

## 使用猪肉

Porky 使用 Adobe 的 [ExtendScript](http://www.adobe.com/devnet/scripting.html) 来创建将 InDesign 连接到其支持的数据源的脚本。在 *Porky/src/examples* 文件夹中有一些例子，我推荐你下载 [ExtendScript 工具包](https://creative.adobe.com/products/download/estk)来编辑和创建你自己的。

我将一步一步地介绍如何连接和使用 Porky 支持的数据源，如果你想深入了解，请阅读 Porky 的文档。

### 结构化查询语言

我创建了一个包含一些 SitePoint 文章的基本 SQL 表，[你可以在这里找到文件](https://github.com/alexmwalker/porky.io-2/blob/master/SitePoint_2015-12-14.sql)。

```
//@include ../bin/porky.jsx;

settings.dataSource.type = "MySQL";
settings.dataSource.server = "127.0.0.1";
settings.dataSource.name ="SitePoint";
settings.dataSource.username = "root";
settings.dataSource.password = "";

var mySQLResult = "";

try{
    mySQLResult = connectToDataSource("SELECT * FROM articles");
    mySQLResult = JSON.parse(mySQLResult);
    alert(mySQLResult);
}catch(e){
    alert("Error\nCheck console window for details!");
}
```

### JSON

和我所有的教程一样，我将使用[漫威 API](http://developer.marvel.com/) 来展示 Porky 的 JSON 特性。

```
//@include ../bin/porky.jsx;

settings.dataSource.type = "JSON";
settings.dataSource.name = "http://gateway.marvel.com/v1/public/characters?ts=1448030805540&apikey=dfa06d77bc9c4f9f0ed01337848247e3&hash=0e54a94548adeb07b97e3e3428c6956f&limit=10";

// specify path in dot notation here or leave "" to request the entire object
var myJSONResult = connectToDataSource("data.results");

alert( JSON.parse(myJSONResult) );
alert( JSON.stringify(myJSONResult) );
```

### 战斗支援车

使用来自早期的 SQL 表的 [CSV 版本:](https://uploads.sitepoint.com/wp-content/uploads/2016/01/1453347338articles.csv)

```
//@include ../bin/porky.jsx;

// read an existing text file from a specific folder
var myCSVFileContent = readFile('articles.csv'); // Absolute file path

if(myCSVFileContent){
  alert('File read with content\n' + myCSVFileContent);
} else {
  alert('File does not exist');
}
```

## 显示数据

### 创建文本框架

```
//@include ../bin/porky.jsx;
...
var myTextframe = addFrame(50, 50, 500, 250, "");
...
```

这将创建一个新的文本框架，参数(按各自的顺序)等于 x 和 y 坐标以及框架的宽度和高度。最后一个参数是内容的字符串或文件，我们现在将其留空。

### 用数据填充帧

这个过程依赖于返回数据的结构，并且某些数据返回格式比其他格式更容易。我们使用以下函数:

```
//@include ../bin/porky.jsx;
...
var myTable = appendToFrame(myTextframe, myArray);
...
```

`myArray`变量需要一个字符串或二维数组，即一个行数组，每行包含一组内容值。获取这个数组可能会变得复杂。

#### JSON

JSON 可能很难，这取决于返回数据的格式以及它是 JSON 对象还是数组。在漫威 API 的例子中，这段代码部分地为我工作了:

```
...
var myArray = new Array(items.length);
myArray[0] = ["id", "Name", "Description"];

for(var i = 1; i Not the tidiest solution and you may experience some issues with escaping characters, but as I say, this all very much depends on your JSON result.

MySQL

Outputting results from a MySQL table is similar to JSON as the MySQL data set is returned as JSON:

[code language="javascript"]
var myArray = new Array(mySQLResult.length);
myArray[0] = ["id", "title", "channel", "url"];

for(var i = 1; i CSV

CSV is the easiest:

[code language="javascript"]
//@include ../bin/porky.jsx;
...
var myArray = csvToTwoDArray(myCSVFileContent, { rSep:'\n' , fSep:',' , quot:"'" , trim:true });

var myTextframe = addFrame(50, 50, 250, 250, "");

var myTable = appendToFrame(myTextframe, myArray);
```

根据连接和数据集的不同，您应该会看到类似这样的内容。

![Table of results](img/301e9ccad5b3816f60b696aaaf849e12.png)

### 结论

Porky 的主要问题是，如果你遇到任何问题，它背后的人更希望你付给他们咨询费。这不一定是一个问题，特别是在 Adobe 生态系统中，但这确实意味着文档经常感觉缺乏。

我花了一些时间让上面的简单例子工作起来，主要是因为缺乏关于 JavaScript 和 ExtendScript 之间的区别的定义以及缺乏文档。

还有几个其他功能我没有在这里介绍，如创建占位符图像和其他文本框功能，所以如果您愿意挖掘和试验，Porky 有可能在 InDesign 中处理大型数据集时为您节省大量重复的任务。

然而，它不适合胆小的人。

## 分享这篇文章