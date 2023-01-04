# 使用来自 Google Sheets 的数据的交互式 JavaScript 图表

> 原文：<https://www.sitepoint.com/interactive-javascript-charts-using-data-from-google-sheets/>

*这篇文章由[杰夫·史密斯](https://www.sitepoint.com/author/jeffsmith)进行了同行评审。感谢 SitePoint 的所有同行评审员使 SitePoint 的内容尽可能做到最好！*

见见我的朋友珍妮。她最近开始编写网站代码。她热爱自己的工作，非常快乐，直到她遇到了史蒂夫，他是一个，比方说，名声不好，不太好相处的客户。

Steve 在电子表格中有很多数据，他想在自己的网站上显示这些数据。我们的朋友(现在珍妮也是你的朋友了！)建议史蒂夫用 Excel 制作图表，并以图片形式上传到网站。

但是史蒂夫就是史蒂夫，他希望图表是互动的。不仅如此，他还希望每当他更改电子表格中的数据时，图表都能得到更新。

珍妮不知道如何处理这个问题，所以她来找我。作为我的好朋友，我给了她这个建议:

首先让你的客户把他的数据转移到 Google Sheets(因为现在所有酷的人都在这么做)。然后我们就可以轻松实现他需要的特性——交互式 JavaScript 图表和动态更新。

她的客户同意了(幸运的是！)，Jenny 和我编写了解决方案的其余部分。但是怎么做呢？嗯，这就是本教程要教你的。

我将本教程分为五个易于遵循的步骤:

1.  从 Google Sheets 导出数据
2.  通过 Ajax 从 Google Sheets 获取 JSON
3.  重构数据
4.  使用 FusionCharts 创建图表。
5.  自定义图表

所以事不宜迟，让我们开始吧！

## 将 Google Sheets 数据导出为 JSON

在深入研究如何导出数据之前，让我们先创建一个工作表。假设你有一个谷歌账户，你可以通过进入[谷歌工作表页面](https://docs.google.com/spreadsheets/)并点击*开始一个新的电子表格*按钮来完成。在打开的电子表格中，创建两列:*演员*和*收入*。然后用一些数据填充新创建的工作表。我从这里拿我的:【2015 年全球收入最高的演员。

[https://docs.google.com/spreadsheets/d/1Aoz_GcPYoEIMMNd1N_meYNOp8TJ0fCXpp1AoUhCpwZo/pubhtml?gid=0&single=true&widget=false&chrome=false](https://docs.google.com/spreadsheets/d/1Aoz_GcPYoEIMMNd1N_meYNOp8TJ0fCXpp1AoUhCpwZo/pubhtml?gid=0&single=true&widget=false&chrome=false)

如您所见，左边一列包含图表的标签，右边一列包含与这些标签相对应的值。对于那些在家跟进的人来说，你可以[在这里](https://docs.google.com/spreadsheets/d/1Aoz_GcPYoEIMMNd1N_meYNOp8TJ0fCXpp1AoUhCpwZo/edit#gid=0)拿一份该表的副本(转到*文件* > *复制一份*)。

Google Sheets 中可用的数据可以导出为多种格式，如 JSON、XML 等。一旦导出为任何一种格式，就可以很容易地通过网络访问。

要在 web 上打开您的文档，您需要对文档设置进行以下更改:

*   将文档的共享设置为在网络上*公开*，或者*任何有链接的人*。为此，点击右上角的*分享*按钮，然后点击出现在弹出窗口右下角的*高级*选项。
*   将文档发布到 web。此选项在*文件下* > *发布到网上*可用

做了这两处修改，文档中的数据就可以通过:https://spreadsheets . Google . com/feeds/list/spread sheet/od6/public/basic？alt=json

您需要用您的 Google 工作表的 ID 替换`SPREADSHEET`，在我们的例子中是`1Aoz_GcPYoEIMMNd1N_meYNOp8TJ0fCXpp1AoUhCpwZo`。你可以在这里看到[的结果](https://spreadsheets.google.com/feeds/list/1Aoz_GcPYoEIMMNd1N_meYNOp8TJ0fCXpp1AoUhCpwZo/od6/public/basic?alt=json)。

## 使用 jQuery 从 Google Sheets 获取 JSON

我们将使用 [jQuery 的 get()](https://api.jquery.com/jquery.get/) 方法从 Google Sheets 中获取数据。您可以包含来自 CDN 的 jQuery，如下所示:

```
<script type="text/javascript" src="https://code.jquery.com/jquery-1.12.0.min.js"></script> 
```

添加了`<script>`标记后，在我们创建的电子表格 URL 上调用`$.get`。

```
var spreadsheetId = "1Aoz_GcPYoEIMMNd1N_meYNOp8TJ0fCXpp1AoUhCpwZo",
    url = "https://spreadsheets.google.com/feeds/list/" +
          spreadsheetId +
          "/od6/public/basic?alt=json";

$.get({
  url: url,
  success: function(response) {
    console.log(response);
  }
}); 
```

上面的 jQuery 代码向 Google Sheets URL 发出一个 Ajax 请求，在成功获取数据后，它调用`success`函数。这个函数记录从服务器返回到控制台的数据。我建议您运行这个脚本，花点时间检查一下返回的内容。

## 重构数据

来自 Google Sheets 的 JSON 数据需要稍微进行重构，以匹配我们的可视化库所接受的数据格式。在本教程中，我们将使用[融合图像](http://www.fusioncharts.com)。

FusionCharts 接受 JSON 数据作为包含`label`和`value`键的对象数组。看起来是这样的:

```
[{
  "label": "Robert Downey Jr.",
  "value": "80"
}
...] 
```

为了实现这一点，我们需要在前面定义的 jQuery `get`函数的`success`回调中运行下面这段代码。

```
var data = response.feed.entry,
    len = data.length,
    i = 0,
    parsedData = [];

for (i = 0; i < len; i++) {
  parsedData.push({
    label: data[i].title.$t,
    value: data[i].content.$t.replace('Income: ', '')
  });
} 
```

我们在这里做的是迭代出现在`response.feed.entry`中的每个 Google 工作表数据对象，并从中提取`label`和`value`。然后我们将它们存储在一个新的数组`parsedData`中，图表将使用这个数组。

`label`在数据对象的`title.$t`键中可用，值在`content.$t`键中可用。尽管该值包含表列名，但这不是必需的，因此我们将其去掉。可视化不需要 Google 表单中的其余数据。

`parsedData`数组现在包含 FusionCharts 能够理解的格式的数据，我们可以继续创建可视化。

## 步骤 3:创建图表

现在我们的数据已经准备好并且可以访问了，我们可以继续创建图表了。创建图表包括以下步骤:

*   创建一个将在其中呈现图表的`<div>`。
*   使用来自 Google Sheet 的解析数据创建 FusionCharts 实例。
*   调用 FusionCharts 实例上的`render`方法以在`div`中呈现它。

在标记中，我们通过 CDN 包含了 FusionCharts 库:

```
<script src="http://static.fusioncharts.com/code/latest/fusioncharts.js"></script> 
```

以及图表的包含元素。这可以包含一些占位符文本，这些文本将在图表呈现之前显示。

```
<div id="chart-container">
  <p>The chart will render here!</p>
</div> 
```

为了实际创建一个基本的水平条形图并将其呈现到`#chart-container`中，我们将把下面的代码添加到`success`回调中:

```
new FusionCharts({
  type: 'bar2d',
  renderAt: 'chart-container',
  width: '100%',
  height: '300',
  dataFormat: 'json',
  dataSource: {
    "chart": {
      "caption": "Highest Paid Actors",
      "yAxisName": "Annual Income (in milion USD)",
      "numberPrefix": "$"
    },
  "data": parsedData
  }
}).render(); 
```

让我们看看我们为图表定义的一些配置选项:

*   `type`:定义我们要绘制的图表类型——本例中为`bar2d`。
*   `height`:图表的高度，以像素为单位。
*   `width`:图表的宽度。
*   在这里我们可以设置标题、两个轴的标签、值前缀等等。
*   `data`:构建图表的数据。

这是我们目前所得到的演示:

在 [CodePen](http://codepen.io) 上通过 SitePoint ( [@SitePoint](http://codepen.io/SitePoint) )查看 Pen [JavaScript 图表+ Google Sheets](http://codepen.io/SitePoint/pen/rxorZd/) 。