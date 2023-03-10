# 用 PhpMetrics 可视化你的代码质量

> 原文：<https://www.sitepoint.com/visualize-codes-quality-phpmetrics/>

我们在 SitePoint 上研究代码质量检查工具已经有一段时间了——最近一次是在 Jenkins 的系列文章中，但是没有一个工具能做到我最近才发现的一个项目所能做到的。

[PhpMetrics](http://www.phpmetrics.org/) 使用 D3 和一些复杂的分析算法来扫描你的应用程序的代码，并输出[关于它的复杂报告](http://www.phpmetrics.org/documentation/index.html#metrics)。

![phpmetrics-maintenability](img/7468cff35d8b52044abe4c1f42fe2719.png)

## 安装和使用 PhpMetrics

如果没有看到合适的例子，谈论它有点困难，所以让我们安装并运行它，然后解释每一部分。

### 环境

为了测试它，我们需要一个隔离的环境。像往常一样，我们将使用我们可信赖的[家园改进版](https://www.sitepoint.com/quick-tip-get-homestead-vagrant-vm-running/)流浪箱，确保所有读者都有相同的测试环境。

### 安装参数

像任何现代 PHP 项目一样，安装非常简单。我们将全局安装它，使它对机器上的所有项目都可用。

```
sudo composer global require 'halleck45/phpmetrics'
```

### 获取代码

我们将在两个代码量很大的项目上测试 PHP metrics:Laravel 和 Symfony，在框架中，而不是项目形式。这意味着我们将不会使用 Composer 中的 create-project 命令，而是每个框架的裸源代码。

```
git clone https://github.com/symfony/symfony symframe
git clone https://github.com/laravel/framework lframe
```

我们这样做是为了避免任何依赖测试，并远离与框架无关的代码。

### 在大量代码的项目中使用 PhpMetrics

现在我们已经下载了项目，让我们对它们运行 PhpMetrics。

```
mkdir Laravel
mkdir Laravel/public
phpmetrics --report-html=/Laravel/public/report_symfony.html symframe
phpmetrics --report-html=/Laravel/public/report_laravel.html lframe
```

不要介意我们在这里为报告使用“Laravel”文件夹的事实，它们只是 Homestead 的缺省值，我们可以在我们的小例子中重用它们以减少配置步骤。这使我们可以通过以下 URL 轻松访问报告:

```
homestead.app:8000/report_symfony.html
homestead.app:8000/report_laravel.html
```

以下是 Laravel 的报告带给我们的信息:

![01](img/baa981a927a799ec792206a8cbf25f42.png)

这是我们通过分析 Symfony 得到的结果:

![02](img/87a6a366919240a2f20309da791bf0ee.png)

## 分析

PhpMetrics 提供了过多的[指标](http://www.phpmetrics.org/documentation/index.html#metrics)。让我们揭开这些圆圈和图形的神秘面纱，看看我们在看什么！

### 颜色编码

根据[如何阅读报告指南](http://www.phpmetrics.org/documentation/how-to-read-report.html)，我们可以立即注意到 Symfony 在橙红色一侧有点重，而 Laravel 一侧的草更绿。然而，需要注意的一件重要事情是 Symfony 包含的文件比 Laravel 多多少——你可以通过比较圆圈的密度清楚地看到这一点。

PhpMetrics 专为色盲人士设计，因此“色盲”复选框会将圆圈中的颜色从绿色、黄色、橙色更改为不同角度的条纹:

![03](img/2aec4b78b703de08bcbfaf13d60f6958.png)

颜色编码是你可以看到的第一个方面，也是寻找问题线索的第一个地方。

### 圈复杂度和可维护性指数

当仔细观察时，圆圈代表 CC 和 MI，正如文档所说，大的红色圆圈可能很难保持。

两者都很容易被谷歌搜索到，但是让我省去维基浏览，用通俗的语言解释它们。

[圈复杂度](http://en.wikipedia.org/wiki/Cyclomatic_complexity)本质上是衡量脚本逻辑的路径数量。可以选择的路径越多，文件就越复杂。避免高 CC 的一个好方法是确保你的代码尽可能模块化和原子化，当你遵循坚实的原则时，这就成了你的第二天性。

就像维基百科所说的那样，[可维护性指数](http://en.wikipedia.org/wiki/Maintainability)是根据[代码行度量](http://en.wikipedia.org/wiki/Lines_of_code)、[麦凯布度量](http://en.wikipedia.org/wiki/McCabe_Measure)和[霍尔斯特德复杂度度量](http://en.wikipedia.org/wiki/Halstead_complexity_measures)的特定公式计算出来的。代码行数度量是不言自明的——字面意思是计算代码行数。McCabe measures，听起来很像美国的政治承诺，实际上只是圈复杂度的另一个名称。 [Halstead complexity](http://en.wikipedia.org/wiki/Halstead_complexity_measures) 是一组公式，试图查看文件代码的语法，并推断出诸如难度、代码词汇、工作量等属性。如果你对具体的公式感兴趣，可以查看维基百科的文章。

考虑到所有这些因素，圆圈的大小和颜色被编码，表示代码复杂性和可维护性的所有标准度量的总和。

### 桌子

如果您想以表格形式查看所有数据，可以查看报告文件中的“Explore”选项卡:

![05](img/14f1710cc32ea54a471320d914881f1e.png)

该表将包括以上所有内容以及更多内容——从代码行数到类数等等——包括每个文件夹和文件。当你在一个地方寻找关于你的代码的非常精确的信息时，这份非常详细的报告是你应该参考的。

### 自定义图表

报告概览屏幕的中间区域包含自定义图表。

![06](img/0a66327d656fa5e1e645774b2d4f6a3a.png)

在上面的例子中，我们比较了每个文件的 CC(上面提到的)和 Lcom ( [在方法](http://eclipse-metrics.sourceforge.net/descriptions/pages/cohesion/LackOfCohesionInMethods.html)中缺乏内聚性)的比率，但是您可以很容易地将 X 或 Y 轴的值换成其他值。例如，这里有一个每行代码难度的比较——你可以注意到 Laravel 的趋势在某种程度上是成比例的 LoC 越多，假设难度就越大，而 Symfony 则倾向于到处都是某些文件。

![07](img/d086392f2aa9f955bef1f56ba54b7627.png)

例如，Symfony 文件`BinaryNode.php`的 150 行代码难度很高，而`Response.php`的 1275 行代码难度很低。探索产生奇怪结果的文件，看看您能从这些见解中发现什么。

### 抽象/不稳定

这个图在这里很好的解释了[，但是简单来说，平均对角线叫做平衡线，或者说，*主序*。我们的目标是让你的类尽可能地接近线条，因为这意味着它们在足够抽象和具体之间达到了*平衡*。我鼓励你阅读上面链接的内容——这是一个简单的阅读，应该可以把事情搞清楚。](http://pdepend.org/documentation/handbook/reports/abstraction-instability-chart.html)

出于某种原因，我无法让 A/I 在我的本地环境中与 Laravel 和 Symfony 一起正常工作。

### 估价

评估是一个网络图表，它取你的整个项目的各种属性的平均值，将这些数字与该工具到目前为止已经学习的其他项目的标准进行比较，然后将你的值叠加到这些值上。正如评估页面上的免责声明所说，这纯粹是装饰性的，没有任何实际价值。

![08](img/569adde1f9d609a46792b0db1cb82069.png)

![09](img/6d8a27ef585bff854d62e02a5c209505.png)

虽然该工具认为这两个框架都是可维护的，但这并不是一个令人震惊的结论，Laravel 对新开发人员更友好，算法更简单，更不用说更少的容量了。

### 关系图

关系图对于较小的项目更有用——它可以让你看到类之间的关系。如果一个类扩展或实现了另一个类，关系图会显示出来。由于这两个项目中的大量类和它们的连接，这个地图在这里没什么用处——事实上，它甚至没有为我加载 Symfony。但是，如果您继续在一个包含较少类的测试项目中进行尝试，您一定会看到好处。

### 再分

最后，重新分区屏幕以一种比 Explore 选项卡简单得多的格式汇总了所有数据，仅列出了一些总数:

symfony:

![10](img/b05aeb11ac8b203afb8f8595d3001bb8.png)

拉勒维尔:

![11](img/f66c30bb3e1d40642e9c7c5637e8d4c4.png)

## 比较结果

那么，从这些结果中可以得出什么结论呢？不用深入研究，我们很容易得出这样的结论:

1.  Laravel 对新开发者更友好，算法更简单，文件大小也不那么臃肿。
2.  Laravel 要轻得多。Symfony 拥有的东西*至少是 Laravel 的三倍——方法、类、文件、代码行……它的规模是 Laravel 的三倍。*
3.  Symfony 更加*相对*复杂(重新分区屏幕的中间栏)
4.  Laravel 在跨文件的复杂性方面要稳定得多——它们有相同的难度比和代码行数，而 Symfony 可以有很大的不同。
5.  Symfony 可以做一些工作，使一些更复杂的文件更加原子化——红色的大圆圈正在接管它的圆形图。

看看你自己玩图表和数据还能学到什么，然后告诉我们！

## 结论

PhpMetrics 是一长串代码质量分析器中的另一个，但是有一点不同。它不是看我们已经预料到的典型的代码标准问题，而是看一整套新的属性，一旦你学会了它的语言，它就用漂亮的数学向你解释你的代码。要了解每个属性的含义，请查阅图表。

你会使用 PhpMetrics 来扫描你的项目吗？你会把它安装到你的 IDE 中吗？你会把它看作是你的 CI 流程中的一个永久的附加部分吗？请在评论中告诉我们你的想法！

## 分享这篇文章