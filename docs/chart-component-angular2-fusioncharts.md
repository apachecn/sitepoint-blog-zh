# 使用 Angular 2 和 FusionCharts 构建图表组件

> 原文：<https://www.sitepoint.com/chart-component-angular2-fusioncharts/>

*这篇文章由[维尔丹·索蒂奇](https://www.sitepoint.com/author/vildansoftic)进行了同行评审。感谢 SitePoint 的所有同行评审员使 SitePoint 的内容尽可能做到最好！*

作为一个 web 开发者，如果有什么东西是你不能错过的，那就是 [Angular 2](https://angular.io/) 。这是对 Google 流行的 JavaScript 框架的完全重写，并且因为各种正确的原因不断出现在新闻中。它对以前的版本进行了一些重大改进，这就是为什么我们今天选择它来制作一些漂亮的图表。

对于图表，我们将使用由 [FusionCharts](http://www.fusioncharts.com/) 提供的 JavaScript 图表库。它提供了一个很好的图表集合，并且兼容所有主流浏览器。虽然 FusionCharts 为 Angular 提供了一个专用的[插件，但它还不兼容 Angular 2。所以我不打算使用它，而是直接用 JavaScript 和 Angular 2 编码。(*注意:如果你在你的应用*中使用 Angular 1，建议你使用该插件)。](http://www.fusioncharts.com/angularjs-charts/)

我们将要绘制的图表将描绘一个有趣的统计数据——五大科技公司(亚马逊、苹果、脸书、谷歌和微软)的收入，并将有一个在 2014 年和 2015 年收入数据之间切换的选项。我们将首先通过 Angular 2 一步一步地创建图表。在构建了一个基本图表后，我们将讨论一些高级主题，如添加注释和更新图表数据。

和以往一样，你可以从我们的 [GitHub repo](https://github.com/sitepoint-editors/angular2-fusioncharts) 下载本教程的代码，或者你可以在文章的最后跳转到[一个成品图表的演示。](#demo)

## 角度 2 对角度 1.x

Angular 2 与之前的主要版本(Angular 1.x)相比有一些重大变化，例如它对 TypeScript 和 Dart 等语言的支持，以及它计算 DOM 更新的方式。如果你想了解更多关于 Angular 1 的概念和技术如何映射到 Angular 2，你可以查看官方的[快速参考](https://angular.io/docs/ts/latest/cookbook/a1-a2-quick-reference.html)。如果你有兴趣将你的 app 从 Angular 1.x 迁移到 Angular 2，可以阅读[官方迁移指南](https://angular.io/docs/ts/latest/cookbook/a1-a2-quick-reference.html)。

虽然 Angular 2 支持 TypeScript 和 Dart，但因为熟悉，所以在本教程中我们将使用原生 JavaScript 来编写 Angular 2 应用程序。使用 TypeScript 或 Dart 也会引入不必要的构建步骤。

## 设置

有许多方法可以启动并运行 Angular 2 项目。最简单的方法可能是去官方网站，跟随他们的 [5 分钟快速入门教程](https://angular.io/docs/js/latest/quickstart.html)。

但是，对这种方法有一点需要注意的是，它依赖于您的机器上安装了 Node 和 npm。我们确实有这个的[指南，但是如果你更喜欢按照这个教程而不安装这些，你可以使用下面的模板:](https://www.sitepoint.com/quick-tip-multiple-versions-node-nvm/)

```
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8">
    <title>Angular 2 FusionCharts Demo</title>

    <!-- 1\. Load custom CSS & fonts-->
    <link rel="stylesheet" href="styles.css">
    <link href='https://fonts.googleapis.com/css?family=Source+Sans+Pro:300' rel='stylesheet'>

    <!-- 2\. Load Angular 2 specific libraries -->
    <script src="https://code.angularjs.org/2.0.0-beta.17/angular2-polyfills.js"></script>
    <script src="https://code.angularjs.org/2.0.0-beta.17/Rx.umd.js"></script>
    <script src="https://code.angularjs.org/2.0.0-beta.17/angular2-all.umd.dev.js"></script>

    <!-- 3\. Load FusionCharts library-->
    <script src="https://static.fusioncharts.com/code/latest/fusioncharts.js"></script>

    <!-- 4\. Load component -->
    <script src='main.js'></script>
  </head>
  <body>

    <!-- 5\. Display the application -->
    <angular-chart>Loading...</angular-chart>
  </body>
</html> 
```

## 创建图表组件

组件是任何 Angular 2 应用程序的构建模块。它们是由一个视图和一些逻辑组成的可重用代码片段。如果你熟悉 Angular 1，你可以把它们想象成带有模板和控制器的指令。

以下是我们图表组件的基础:

```
(function(chartApp){
  chartApp.AppComponent = ng.core.Component({
    selector: 'angular-chart',
    template: '<div>Chart will render here</div>'
  }).Class({
    constructor: function(){}
  });

  document.addEventListener('DOMContentLoaded', function() {
    ng.platform.browser.bootstrap(chartApp.AppComponent);
  });
})(window.chartApp || (window.chartApp = {})); 
```

让我们花点时间看看发生了什么。

我们从 life([立即调用函数表达式](https://www.sitepoint.com/demystifying-javascript-closures-callbacks-iifes/#immediately-invoked-function-expressions-iifes))开始，我们用它来命名我们的应用程序。我们将它作为参数传递`window.chartApp`，如果它没有被定义，它将被初始化为一个空对象。这就是我们的应用程序将要存在的地方——在全局对象的单个属性中。

在生命内部，我们通过链接`ng.core`(Angular 的核心组件集合)中的[组件](https://angular.io/docs/ts/latest/api/core/Component-decorator.html)和[类](https://angular.io/docs/ts/latest/api/core/Class-function.html)方法来创建我们的组件(`AppComponent`)。我们向`Component`方法传递一个包含以下属性的配置对象:

*   一个简单的 CSS 选择器，它指定了一个主机 HTML 元素。每当 Angular 遇到与该选择器匹配的 HTML 元素时，它将创建并显示组件的一个实例。

*   `template`:渲染组件时使用的模板。目前，我们正在传递一个包含占位符`<div>`元素的字符串，但理想情况下，我们应该把它移到自己的模板中。

`Class`方法是我们为模板添加行为和事件绑定的地方。

定义了我们的基本组件后，我们使用 Angular 的[浏览器引导函数](https://angular.io/docs/js/latest/api/platform/browser/bootstrap-function.html)初始化它。

此时，您应该能够在浏览器中运行代码，并看到消息“Chart will render here”。

## 创建图表

让我们继续创建图表并显示 2014 年的一些数据。

为此，我们需要使用 [FusionCharts 构造函数](http://www.fusioncharts.com/dev/api/fusioncharts.html),我们向其传递一个包含图表实例所有配置参数的对象:

*   `type`:我们希望创建的图表类型
*   我们的图表将被渲染到的 DOM 选择器
*   `width`和`height`:图表尺寸
*   `id`:生成的图表的 ID
*   `dataFormat`:传递给`dataSource`选项的数据格式
*   `dataSource`:实际图表的配置，以及应显示的数据

```
new FusionCharts({
  "type": "column2d",
  "renderAt": "chart-container",
  "width": "550",
  "height": "400",
  "id": "revenue-chart",
  "dataFormat": "json",
  "dataSource": {
    "chart": {
      "yAxisName": "Revenue (In USD Billion)",
      "yAxisMaxValue": "200",
      ...
    },
    "data": [{
      "label": "Amazon",
      "value": "88.99"
    }, {
      "label": "Apple",
      "value": "182.8"
    }
    ...
    ]
  }
}); 
```

这里是[完整的配置文件](https://run.plnkr.co/plunks/qbDs5R/config.json)。

如果您不确定任何`chart`选项实际上是做什么的，或者如果您想知道图表的外观可以如何配置，您可以参考 FusionCharts 文档中的[图表属性](http://www.fusioncharts.com/dev/chart-attributes.html?chart=column2d)页面。

我们必须做的另一件事是更新我们的模板，以包含我们的图表应该呈现到的容器。您可以通过为组件的`template`属性指定一个字符串来实现这一点(正如我们之前所做的)，或者通过将模板移动到它自己的文件中并使用`templateUrl`来引用它。

```
chartApp.AppComponent = ng.core.Component({
  selector: 'angular-chart',
  templateUrl: 'chart.html'
}).Class({
  ...
}); 
```

不管怎样，这就是我们的模板应该的样子。

```
<div class="container">
  <h1>Revenue of Top Tech Companies (2014)</h1>
  <div id ="chart-container"></div>
</div> 
```

这是我们目前的演示:

[https://run.plnkr.co/plunks/qbDs5R/](https://run.plnkr.co/plunks/qbDs5R/)

<br>Loading plunk&hellip；<br>

你可以在 Plunker 上查看这个演示的代码。

如果您点击 Plunker 上的演示，在文件`main.js`中，您可能会注意到我们已经将 FusionCharts 配置数据分离到它自己的文件中，然后我们使用 [Angular 的 HTTP 类](https://angular.io/docs/js/latest/api/http/Http-class.html)获取该文件。这是为了清晰起见(这使得特定于角度的代码更容易理解)，还因为请求数据是您在现实生活中通常会做的事情。

然而，这并不是绝对必要的，直接在`chartApp`构造函数中做任何事情都会得到相同的结果:

```
(function(chartApp) {
  chartApp.AppComponent = ng.core.Component({
    selector: 'angular-chart',
    template: '<div class="container"><h1>Revenue of Top Tech Companies (2014)</h1><div id ="chart-container"></div></div>'
  }).Class({
    constructor: function() {
      FusionCharts.ready(function(){
        var revenueChart = new FusionCharts({
          // config data here
        }).render();
      });
    }
  });

  document.addEventListener('DOMContentLoaded', function() {
    ng.platform.browser.bootstrap(chartApp.AppComponent);
  });
})(window.chartApp || (window.chartApp = {})); 
```

另外唯一要提到的是，初始化代码被包装在`FusionCharts.ready`方法中。这可以防止在加载 FusionCharts 库之前调用图表实例化代码。

基本图表准备就绪后，是时候添加更多功能了，例如使用公司徽标而不是名称，并用 2015 年的新数据更新图表。

## 添加注释

为了将公司徽标添加到 x 轴，我们将使用 FusionCharts 的强大功能之一— *annotations* 。FusionCharts 对象上的批注允许您在图表上的指定位置绘制自定义形状或图像。

假设您想在图表中心添加您的公司徽标。你可以使用[注释](http://www.fusioncharts.com/dev/advanced-chart-configurations/annotations/introduction.html)和[宏](http://www.fusioncharts.com/dev/advanced-chart-configurations/annotations/positioning-annotations-using-macros.html)来完成。宏将为您提供图表中心的坐标，注释将允许您在该位置添加图像。

例如，当您使用动态注释来获取依赖于图表数据的位置信息时，事情就变得有趣了。假设你想在列的末端画点东西。你可以用动态标注宏`$dataset.0.set.1.endX`和`$dataset.0.set.1.endY`来确定列终点的 x 和 y 坐标，然后在那边画点什么。你可以在[这个 FusionCharts 文档页面](http://www.fusioncharts.com/dev/advanced-chart-configurations/annotations/introduction.html)上了解更多关于注释以及如何使用它们的信息。

对于我们的图表，我们将使用动态注释宏来获取每一列的开始和结束坐标，然后我们将在这里绘制各自的公司徽标。我们还将使用图表属性`"showLabels": "0"`禁用默认的 x 轴标签。

要实现上述目标，请将以下代码添加到图表的配置中:

```
{
  "type": "column2d",
  ...
  "dataSource": {
    "chart": {
      "showLabels": "0",
      ...
    },
    "data": [{ ... }],
    "annotations": {
      "groups": [{
        "id": "logo-images",
        "xScale": "30",
        "yScale": "30",
        "showBelow": "0",
        "items": [{
          "type": "image",
          "url": "https://uploads.sitepoint.com/wp-content/uploads/2016/06/1465735364amazon.jpg",
          "x": "$dataset.0.set.0.startx + 25",
          "y": "$dataset.0.set.0.endY + 10"
        }, {
          "type": "image",
          "url": "https://uploads.sitepoint.com/wp-content/uploads/2016/06/1465735362apple.jpg",
          "x": "$dataset.0.set.1.startx + 85",
          "y": "$dataset.0.set.1.endY + 10"
        }, {
          "type": "image",
          "url": "https://uploads.sitepoint.com/wp-content/uploads/2016/06/1465735369facebook.jpg",
          "x": "$dataset.0.set.2.startx + 20",
          "y": "$dataset.0.set.2.endY + 10"
        }, {
          "type": "image",
          "url": "https://uploads.sitepoint.com/wp-content/uploads/2016/06/1465735358google.jpg",
          "x": "$dataset.0.set.3.startx + 5",
          "y": "$dataset.0.set.3.endY + 5"
        }, {
          "type": "image",
          "url": "https://uploads.sitepoint.com/wp-content/uploads/2016/06/1465735372microsoft.jpg",
          "x": "$dataset.0.set.4.startx + 30",
          "y": "$dataset.0.set.4.endY + 10"
        }]
      }]
    }
  }
} 
```

在上面的代码中:

*   `type`正在设置标注的类型。
*   `url`正在设置图像的地址。
*   `x`和`y`设置图像的起始 x 和 y 坐标。

添加上述代码后，您应该会看到公司徽标呈现在 x 轴上。要了解更多关于使用注释和其他可能的内容，请参考[文档页面](http://www.fusioncharts.com/dev/advanced-chart-configurations/annotations/introduction.html)(如上所述)。

## 在数据集之间切换

我们要实现的最后一件事是允许用户在年份之间切换，根据选择的年份(2014 年或 2015 年)查看不同的数据集。

### 结构化数据。

因此，我们需要考虑如何组织我们的数据，以便为不同的年份定义不同的数据集。如前所述，FusionCharts 期望配置选项包含一个`data`属性，它应该是一个包含标签/值对集合的数组。

```
{
  "type": "column2d",
  ...
  "dataSource": {
    "chart": {},
    "data": [
      {
        "label": "whatever",
        "value": "a value"
      }
    ]
  }
} 
```

处理多个数据集的一种方法是在构造函数的顶部定义一个`dataSet`对象，并使用别名将其附加到构造函数上。

```
var chartComp= this;
chartComp.dataSet = {
  "2014": [{
    "label": "Amazon",
    "value": "88.99"
  }, {
    "label": "Apple",
    "value": "182.8"
  }, {
    "label": "Facebook",
    "value": "12.47"
  }, {
    "label": "Google",
    "value": "65.67"
  }, {
    "label": "Microsoft",
    "value": "86.83"
  }],
  "2015": [{
    "label": "Amazon",
    "value": "107.01"
  }, {
    "label": "Apple",
    "value": "233.72"
  }, {
    "label": "Facebook",
    "value": "17.93"
  }, {
    "label": "Google",
    "value": "74.54"
  }, {
    "label": "Microsoft",
    "value": "93.58"
  }]
} 
```

然后，在我们传递给 FusionCharts 构造函数的配置选项中，我们可以:

```
"data": chartComp.dataSet['2014'], 
```

### 切换时更新图表数据

我们还希望当有人点击 *2015* 按钮并切换回显示 2014 年的数据时，当点击 *2014* 按钮时，图表会更新为 2015 年的数据。

让我们添加两个按钮，它们将被用来执行这个动作，并给它们[一些样式](https://run.plnkr.co/plunks/xs4Zx7/style.css)。按如下方式修改组件模板:

```
<div class="container">
  <h1>Revenue of Top Tech-companies</h1>

  <div class="actions">
    <button (click)="yearChange(2014)"
            [ngClass] = "{selected: selectedYear== 2014}">2014
    </button>
    <button (click)="yearChange(2015)"
            [ngClass] = "{selected: selectedYear== 2015}">2015
    </button>
  </div>
  <div id ="chart-container"></div>
</div> 
```

请注意在 Angular 2 中添加事件监听器和添加 [ngClass 指令](https://angular.io/docs/js/latest/api/common/NgClass-directive.html)的新语法。除了一些大括号和圆括号之外，它们几乎与 Angular 1 相同。

我添加了一个`ngClass`指令，通过对 button 元素应用一个`selected` CSS 类来突出显示当前选择的年份。这是基于组件的`selectedYear`属性，该属性在点击按钮时得到更新。

当组件呈现时，我们可以将当前选定的年份设置为 2014 年，方法是在构造函数的顶部添加以下行:

```
chartComp.selectedYear = 2014; 
```

处理按钮点击的逻辑将被添加到新的`yearChange`功能中。

```
.Class({
  constructor: function(){ ... },
  yearChange: function(){ // button logic here }
); 
```

为此，我们将使用 FusionChart 的 [setChartData](http://www.fusioncharts.com/dev/api/fusioncharts/fusioncharts-methods.html#setChartData) 方法，该方法需要图表配置选项和实际的图表数据。我们将使用`getChartData`方法从已经呈现的图表中获取图表属性，并用特定于年份的数据修改该数据，而不是先存储图表属性，然后再引用它们。

```
yearChange: function(year) {
  var revenueChart = FusionCharts.items['revenue-chart'];
  var chartJson = revenueChart.getChartData('json');
  chartJson.data = this.dataSet[year];
  revenueChart.setChartData(chartJson);
  this.selectedYear = year;
} 
```

在添加了按钮的 HTML 和上面这些按钮的单击处理程序之后，单击这些按钮应该会为图表加载当年的数据。

## 演示

这是最后一个演示。

[https://run.plnkr.co/plunks/xs4Zx7/](https://run.plnkr.co/plunks/xs4Zx7/)

<br>Loading plunk&hellip；<br>

你可以在 Plunker 上查看这个演示的代码。或者你可以从我们的 [GitHub repo](https://github.com/sitepoint-editors/angular2-fusioncharts) 下载代码。

如果你点击链接器，你会看到我们已经在 [config.json](https://run.plnkr.co/plunks/xs4Zx7/config.json) 文件中直接定义了`dataSet`属性。这使得我们的组件更加整洁。

## 结论和下一步措施

我们从构建一个简单的角度图开始，然后继续使用注释和其他 FusionCharts 的 API 向它添加更多功能。但这只是冰山一角，使用 Angular 2 和 FusionCharts 还可以做更多的事情。一些你可以自己探索的事情:

*   **包含更多图表:**柱形图并不总是表示数据集的最佳方式。根据您的使用情况，您可能希望使用不同的图表，如瀑布图、雷达图、量表等。因此，尝试使用本教程中解释的过程来绘制一个[不同的图表](http://www.fusioncharts.com/charts/)，看看你是否能够成功。

*   **在你的应用中包含图表:**如果你喜欢制作混合移动应用，那么你必须知道[Ionic 2](http://ionicframework.com/docs/v2/getting-started/installation/)(Ionic 的最新版本)是基于 Angular 2 的。这是一个好消息，因为你也可以使用本教程作为基础，为你的 Ionic 应用程序创建图表。

*   **探索更多事件:**在本教程中，我解释了如何使用`setChartData`方法，但是还有更多[事件](http://www.fusioncharts.com/dev/advanced-chart-configurations/events/introduction-to-events.html)和[方法](http://www.fusioncharts.com/dev/api/fusioncharts/fusioncharts-methods.html)可以用来增强应用程序的用户体验。查看以上链接页面，了解 FusionCharts 提供的各种活动和方法的更多信息。

如果你在尝试自己制作图表时遇到任何困难，请参考 [Angular](https://angular.io/docs/ts/latest/) 或 [FusionCharts](http://www.fusioncharts.com/dev/) 的文档(视问题而定)，或者在下面留下评论。我非常乐意帮忙！

## 分享这篇文章