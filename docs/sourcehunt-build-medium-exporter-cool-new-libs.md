# Sourcehunt 建立一个中型出口商+酷新图书馆！

> 原文：<https://www.sitepoint.com/sourcehunt-build-medium-exporter-cool-new-libs/>

是时候让我们的[每月寻找新的开源库来使用并贡献给](https://www.sitepoint.com/sourcehunt-open-source-week-edition/)了！

如果您是 Sourcehunt 的新手，这是我们的月度帖子，旨在推广那些有趣或有前途的开源项目，并在 Github stars 或 pull 请求方面提供帮助。

这是我们回馈社会的方式——推广我们使用(或可能使用)的项目，以便它们获得足够的曝光率来吸引更广泛的受众、强大的社区以及可能的新贡献者或赞助商。

![Sourcehunt logo](img/407bcf1bded70a8c020759619779150b.png)

这个月我们收到了很多精彩的投稿，所以让我们开始吧！

* * *

### [重新思考 php/json-validator](https://github.com/rethinkphp/json-validator) [28 ★]

> 一个 Json 验证器，设计优雅且易于使用。

json-validator 是由 [Blink framework](https://www.sitepoint.com/can-php-be-even-faster-light-speed-with-the-blink-framework/) 的作者开发的，它的唯一目的是确保数据结构符合预定义的规范。它目前支持 int、double、bool、string、number、array 和 object，但也允许您定义完全自定义的类型，如下所示:

```
$validator->defineType('User', [
    'name' => 'string',
    'gender' => 'string',
    'age' => '?integer',
]); 
```

您也可以在可调用中定义它们:

```
$validator->defineType('timestamp', function ($value) {
    if ((!is_string($value) && !is_numeric($value)) || strtotime($value) === false) {
        return false;
    }

    $date = date_parse($value);

    return checkdate($date['month'], $date['day'], $date['year']);
}); 
```

该库非常新，可以使用贡献——从文档到功能的一切都需要工作，所以这是一个贡献的好机会。

* * *

### [书呆子/博客](https://github.com/Nerdmind/Blog)【20】

这个极简且移动友好的博客引擎，虽然在视觉方面还有很多需要改进的地方，但对于任何想要开始一个博客而没有更大系统负担的人来说，是一个实用的快速解决方案。

对于作者来说，这看起来确实有点像是一次学习经历，所以为什么不直接投入进去，找出一些问题(许多反模式)，并在您工作的时候修复一些问题呢？也许你们俩都能从这次经历中吸取教训。

* * *

### [violet-PHP/streaming-JSON-encoder](https://github.com/violet-php/streaming-json-encoder)

这个流 JSON 编码器允许您处理和发送大型 JSON 文件，而不会达到内存限制。如自述文件中所述:

> Streaming JSON Encoder 是一个 PHP 库，它提供了一组类来帮助以流的方式编码 JSON，即允许您一点一点地编码 JSON 文档，而不是一次编码整个文档。与内置的 json_encode 函数相比，有两个主要优点:
> 
> *   您不需要将整个数据集加载到内存中，因为编码器支持遍历数组和任何类型的迭代器，比如生成器。
> *   您不需要将生成的整个 JSON 文档加载到内存中，因为 JSON 文档将被逐个值地编码，并且可以逐段输出编码后的文档。

它还为那些种类的框架提供了一个 PSR-7 兼容的流。

这个包可以使用更多的教程、测试和使用示例，所以如果你渴望的话就开始吧—[我们会付钱的](https://www.sitepoint.com/write-for-us/)！

* * *

### dsentker/phpinsights [2 ★]

这个简单但有效的 Google PageSpeed Insights 包装器对于测试管道非常有用，当你想跟踪你的 PageSpeed 分数时。

```
$uri = 'http://example.com';
$caller = new \PhpInsights\InsightsCaller('your-google-api-key-here', 'de');
$response = $caller->getResponse($uri, \PhpInsights\InsightsCaller::STRATEGY_MOBILE);
$result = $response->getMappedResult();

var_dump($result->getSpeedScore()); // 100 
var_dump($result->getUsabilityScore()); // 100 
```

注意，在使用它之前，您需要从 Google Dev 控制台获取一个 API 密钥。

现在，它需要更多的测试，所以如果你的 TDD-fu 很弱，这是一个刷新它的机会！

* * *

### lazy chaser/laravel-nested set[847★]

laravel-nestedset 已经是一个相当受欢迎的库了，它为 laravel 用户提供了一个非常容易接近的、非常通用的处理嵌套集合的解决方案。

正如 repo 所述，这是一种在关系表中有效存储分层数据的方法，并且在树很少更新时表现出良好的性能。它被调整为快速获取相关节点，非常适合为商店构建多深度菜单或类别。

该库功能丰富，包括但不限于反向树检索、追加和前置到任何父级、处理兄弟、展平等等。

现在，它有 15 个问题需要关注——如果可以的话，去帮他们一把，好吗？

* * *

### [jme path/jme path . PHP](https://github.com/jmespath/jmespath.php)[238★]

趁我们还在 JSON 火车上，让我们跳上这辆马车。

由 [Guzzle](https://www.sitepoint.com/guzzle-php-http-client/) 成名的 [Michael Dowling](https://github.com/mtdowling) 开发的这个包允许你“*声明性地指定如何从 JSON 文档*中提取元素”。这在代码中的确切含义如下:

```
$expression = 'foo.*.baz';

$data = [
    'foo' => [
        'bar' => ['baz' => 1],
        'bam' => ['baz' => 2],
        'boo' => ['baz' => 3]
    ]
];

JmesPath\search($expression, $data);
// Returns: [1, 2, 3] 
```

本质上，您定义了表达式，通过这些表达式，您可以毫不费力地遍历和提取非常复杂的 JSON 结构。

有一些悬而未决的问题和拉请求可以考虑，但我们更感兴趣的是[为复杂的使用示例](https://www.sitepoint.com/write-for-us/)付费。潜进去！

* * *

### [卢本斯/拉瓦尔-分解](https://github.com/lubusIN/laravel-decomposer)【182】

就像自述文件简洁地指出的那样:

> Laravel Decomposer 分解并列出所有已安装的包及其依赖项，以及 Laravel &应用程序运行的服务器环境细节。Decomposer 还可以从这些细节中生成一个降价报告，用于故障排除，它还允许你在代码中的任何地方生成与数组相同的报告。

虽然肯定是一个利基产品，但这个包无疑会在各种非技术场景中找到它的用途，在这些场景中，项目的依赖设置需要与客户或开发团队共享，服务器环境变量不明显并且需要公开，当用户收集 bug 上下文时，等等——一个简单的公开端点发布这些数据将为开发人员在调试时必须向他们的客户提出的许多问题提供答案。

我可以看到它在基于 Laravel 的 CMS 环境中特别有用，在这种环境中，插件和其他细节可能被隐藏在数百个其他包中。

路线图上有一些*待办事项*，所以去做吧！

* * *

### 紧线器/竖锯 [532 ★]

由[拉勒维尔叶片](https://www.sitepoint.com/laravel-blade-recursive-partials/)驱动的静态现场发电机。Jigsaw 类似于 [Sculpin](https://www.sitepoint.com/sculpin-extended-customizing-your-static-site-blog/) 或 [Spress](https://www.sitepoint.com/building-an-spress-svbtle-theme-responsive-static-blogs/) ，但是基于 Laravel 而不是 Symfony——这意味着对刀片插件和语法的支持，以及构建资产的灵丹妙药。Jigsaw 也支持 Markdown，就像它的竞争对手一样。

有一些问题和公关要处理，他们的文档也很缺乏，所以开始吧！另外，我们需要一些关于它的教程！

* * *

### slevomat/编码标准 [93 ★]

这个编码标准是 PHP _ code sniffer[consistency 编码标准](https://github.com/consistence/coding-standard)的扩展，并在已有的基础上增加了一些有用的嗅探。

当它们在 [README](https://github.com/slevomat/coding-standard) 中被很好地描述时，在这里列出它们是没有用的——去看看吧！嗅探是有选择性的，所以你可以选择不使用那些不适用于你的项目。然而，大多数都是非常方便的跨项目规则，每个专业开发团队都应该执行。

现在就开始使用它，并将其添加到您开发环境和测试管道中！

* * *

### brain maestro/composer-git-hooks[95★]

> 在您的 composer 配置中轻松管理 git 挂钩。这个包使得在项目范围内实现一致的 git 钩子使用变得很容易。在 composer 文件中指定钩子使得项目团队的每个成员都可以使用它们。这为每个人提供了一致的环境和行为。

如果你不熟悉基于 git-hooks 的工作流程，我们推荐[这篇文章](https://www.sitepoint.com/git-hooks-fun-profit/)，并建议你跟进[这些](https://www.sitepoint.com/introduction-git-hooks/)——你会很快发现它们非常有用。

这个包使得它们的维护和管理更加容易。

* * *

三月到此为止。找到你能咬进去的东西了吗？

和往常一样，请把你的链接和 [#sourcehunt](https://twitter.com/search?q=#sourcehunt) 标签一起扔给我们！如果您用我们提到的项目构建了一些东西，或者如果您提交了一个您想要谈论的详细的拉请求，请向我们大喊一声，我们将确保全世界都知道它！

就像上次的，(顺便说一句，这个挑战仍然无人问津——如果你完成了，就有 500 美元奖励给你！)，我们使用上面的软件包作为灵感来创建一个潜在有用的应用程序:

**App+本月教程创意:**构建一个应用，它采用 zip 格式的[媒体数据导出](https://help.medium.com/hc/en-us/articles/214043918-Export-content-from-Medium)，在内部对其进行处理，使用流 JSON 库将其转换为 JSON，并使用验证器验证该 JSON，然后将该 JSON 转换为可由 NerdMind/Blog 或 TigthenCo/Jigsaw(用户选择)使用的格式。该项目需要使用 Slevomat/coding-standard 进行探测，最好是自动探测，并且需要有一个用户界面，用户可以在其中加载上述 JSON 格式(再次使用流库)并使用 jmespath.php 应用提取表达式来获取一些数据。[联系](mailto:bruno.skvorc@sitepoint.com)看看这对我们来说值多少钱！

编码快乐！

## 分享这篇文章