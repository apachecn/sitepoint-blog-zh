# Diffbot:用视觉机器学习爬行

> 原文：<https://www.sitepoint.com/diffbot-crawling-visual-machine-learning/>

你有没有想过，当你分享链接的时候，社交网络是如何把网址预览做得这么好的？他们如何知道要抓取哪些图像，引用谁作为作者，或者在预览中附加哪些标签？是不是所有的源代码都爬满了复杂的正则表达式？其实，更多的时候，并不是这样。源中定义的元信息可能不可靠，声誉不佳的网站经常使用它们作为关键词载体，试图让搜索引擎提高它们的排名。不管怎样，我们人类在我们面前看到的不是最重要的吗？

如果你想建立一个 URL 预览片段或新闻聚合器，网上有许多自动爬虫可用，既有专有的也有开源的，但你很少能找到像视觉机器学习这样的利基。这正是 [Diffbot](http://diffbot.com) 所做的——一个“视觉学习机器人”,它完整地呈现你请求的 URL，然后以视觉方式提取数据，根据需要帮助自己从页面源获取一些元数据。

在介绍了一些理论之后，在这篇文章中，我们将在 SitePoint 的一篇文章中做一个 API 调用演示。

## PHP 库

Diffbot 的 PHP 库有些过时，因此我们不会在这个演示中使用它。我们将执行原始 API 调用，在未来的一些帖子中，我们将为 API 交互构建自己的库。

如果你想看看 PHP 库，请看这里的，如果你对其他语言的库感兴趣，Diffbot 有一个[目录](https://diffbot.com/dev/docs/libraries/)。

***更新，2015 年 7 月**:自本文发表以来，已经开发了一个 PHP 库。这里看它的整个开发过程[，或者这里](https://www.sitepoint.com/starting-new-php-package-right-way/)看源代码[。](https://github.com/Swader/diffbot-php-client/)*

## JavaScript 内容

我们在介绍部分说过，Diffbot 完整地呈现请求，然后对其进行分析。但是，JavaScript 内容呢？现在的网站往往会在 fold 上面渲染一些 HTML，然后完成 CSS、JS，以及事后的动态内容加载。Diffbot API 能看出这一点吗？

事实上，是的。Diffbot 逐字完整地呈现页面，然后进行视觉检查，如我的 StackOverflow Q&A [here](http://stackoverflow.com/questions/24915127/does-diffbot-execute-javascript/24915128#24915128) 中所解释的。不过，还是有一些注意事项，所以请务必仔细阅读答案。

## 定价和 API 健康

Diffbot 有几个使用层。有一个免费的试用层，它会在 7 天或 10000 次调用后杀死你的 API 令牌，以先到者为准。商业代币可以以各种价格购买，永不过期，但是有限制。为开源和/或教育项目提供了一种特殊的逐案方法，该方法提供了一种旧的免费令牌模型——每月 10k 次调用，最多每秒一次，但永不过期。如果你认为你有资格，你需要直接联系他们。

Diffbot 保证了很高的正常运行时间，但有时确实会发生故障——尤其是在资源最密集的 API 中:Crawlbot。Crawlbot 用于爬行整个域，而不仅仅是单个页面，因此可靠性比其他 API 低。不是很多，但足以在 [API Health](http://status.diffbot.com/) 屏幕中被注意到——如果您的调用遇到问题或返回错误 500，您可以在该屏幕中查看 API 是否启动并运行或当前不可用。

## 演示

要准备您的环境，请启动一个 [Homestead 改进的](https://www.sitepoint.com/quick-tip-get-homestead-vagrant-vm-running/)实例。

### 创建项目

通过用`vagrant ssh`登录到 VM，进入`Code`文件夹，执行`composer create-project laravel/laravel Laravel --prefer-dist`，创建一个 starter Laravel 项目。这将允许您从主机浏览器通过`http://homestead.app:8000`访问 Laravel 问候页面。

### 添加路线和操作

在`app/routes.php`中添加以下路线:

```
Route::get('/diffbot', 'HomeController@diffbotDemo');
```

在`app/controllers/HomeController`中增加以下动作:

```
public function diffbotDemo() {
        die("hi");
    }
```

如果`http://homestead.app:8000/diffbot`现在在屏幕上输出“hi ”,我们就可以开始玩 API 了。

### 获得代币

要与 Diffbot API 交互，您需要一个令牌。在[他们的价格页面](http://diffbot.com/pricing/)上注册一个。为了这个演示，让我们称我们的令牌为`$TOKEN`，我们将在 URL 中这样称呼它。在适当的地方用您自己的值替换`$TOKEN`。

### 安装 Guzzle

我们将使用 Guzzle 作为我们的 HTTP 客户端。这不是必需的，但是我建议您通过我们的过去的一篇文章来熟悉它。

将`"guzzlehttp/guzzle": "4.1.*@dev"`添加到`composer.json`中，这样 require 块看起来就像这样:

```
"require": {
		"laravel/framework": "4.2.*",
        "guzzlehttp/guzzle": "4.1.*@dev"
	},
```

在项目根目录下，运行`composer update`。

### 获取文章数据

在第一个例子中，我们将使用 Diffbot 的默认文章 API 来抓取 SitePoint 帖子。为了做到这一点，我们参考了在解释工作流程方面做得很好的[文档](https://diffbot.com/dev/docs/)。将`diffbotDemo`动作的主体更改为以下代码:

```
public function diffbotDemo() {

        $token = "$TOKEN";
        $version = 'v3';

        $client = new GuzzleHttp\Client(['base_url' => 'http://api.diffbot.com/']);

        $response = $client->get($version.'/article', ['query' => [
            'token' => $token,
            'url' => 'https://www.sitepoint.com/7-mistakes-commonly-made-php-developers/'
        ]]);

        die(var_dump($response->json()));
    }
```

首先，我们设置令牌。然后，我们定义一个保存 API 版本的变量。接下来，由我们来创建一个新的 Guzzle 客户端，我们也给它一个基本 URL，这样我们就不必在每次发出另一个请求时都输入它。

接下来，我们通过向 API 的 URL 发送 GET 请求来创建一个响应对象，并以 key => value 格式添加一个查询参数数组。在这种情况下，我们只传入令牌和 URL，这是最基本的参数。

最后，由于 Diffbot API 返回 JSON 数据，我们使用 Guzzle 的`json()`方法将其自动解码为一个数组。然后我们把这些数据打印出来:

![](img/6948963ed3d340c121ed1d21c5b06377.png)

如你所见，我们很快得到了一些信息。使用的图标、文本预览、标题，甚至语言、日期和 HTML 都被返回。然而，你会注意到没有作者。让我们改变这一点，请求更多的值。

如果我们将“fields”参数添加到查询参数列表中，并给它一个值“tags ”, diff bot 将尝试从提供的 URL 中提取标签/类别。将这一行添加到`query`数组:

```
'fields' => 'tags'
```

然后将`die`部分改为:

```
$data = $response->json();
die(var_dump($data['objects'][0]['tags']));
```

刷新屏幕后，我们会看到:

![](img/4022d051879edb71eb62abb4a89143c4.png)

但是，本文的源代码提到了其他几个标记:

![](img/19fb3f68c1e7abfc75fd33f1b7585baa.png)

为什么结果差别这么大？正是由于我们在这篇文章第一段末尾提到的原因:我们人类看到的优先。Diffbot 是一个视觉学习机器人，因此它的人工智能从实际呈现的内容(它可以看到的内容)中推断出标签，而不是从查看源代码中推断出标签，这对于 SEO 来说太容易了。

但是，如果真的需要的话，有没有办法从源代码中获取标签呢？再者，能不能让 Diffbot 在 SitePoint 文章上认出作者？是的。使用自定义 API。

### 使用自定义 API 的元标签和作者

自定义 API 是一个特性，它不仅允许您通过添加新的字段和内容提取规则来调整现有的 Diffbot API，还允许您创建全新的 API(也可以通过专用的 URL 访问)来进行自定义内容处理。

转到[开发仪表板](http://diffbot.com/dev)并使用您的令牌登录。然后，进入“自定义 API”。激活底部的“创建规则”选项卡，将我们正在抓取的文章的 URL 输入到 URL 框中，然后单击测试。您的屏幕应该是这样的:

![](img/2226739d022afa35c92b35557df6c3a6.png)

您会立即注意到 Author 字段是空的。您可以调整作者搜索规则，方法是单击它旁边的 Edit，在打开的实时预览窗口中找到 author 元素，然后单击它以获得所需的结果。然而，由于 SitePoint 的一些不太完美的 CSS，很难为 Diffbot 的 API 提供一致的作者姓名路径，尤其是通过点击元素。而是手动添加以下规则:`.contributor--large .contributor_name a`并点击保存。

您会注意到预览窗口现在正确地填充了作者字段:

![](img/f3a8017feef7b6b5d89b42410553038a.png)

事实上，这个新规则会自动应用到您的令牌的所有 SitePoint 链接。如果你试着预览另一篇 SitePoint 文章，比如[这篇](https://www.sitepoint.com/social-network-authentication-twitter-facebook/)，你会注意到 Peter Nijssen 被成功提取:

![](img/fba2157779a3e103d192056d053844d0.png)

好了，让我们进一步修改 API。我们需要在源代码中可见的`article:tag`值。这样做需要两步过程。

#### 步骤 1:定义集合

集合顾名思义就是通过特定规则集获取的值的集合。我们将把我们的集合称为“MetaTags”，并给它以下选择器:`meta[property=article:tag]`。这意味着“在 HTML 中找到所有具有值为`article:tag`的`property`属性的元元素”。

#### 步骤 2:定义集合字段

集合字段是集合中的单个条目——在我们的例子中，是各种标签。单击“将自定义字段添加到此集合”，并添加以下值:

![](img/85deb06536215aa84af5bcb451b11f6f.png)

单击保存。您可以立即访问结果窗口中的标签列表:

![](img/ba5fbdc3921ed70cfd1304595787de29.png)

将`diffbotDemo()`动作的最终输出改为:

```
die(var_dump($data['objects'][0]['metaTags']));
```

如果您现在刷新我们用(`http://homestead.app:8000/diffbot`)测试的 URL，您会注意到 author 和 meta tags 值在那里。下面是上述代码行产生的输出:

![](img/86f5c938ddef4854e6e10beba05c2914.png)

我们有我们的标签！

## 结论

Diffbot 是一个强大的网络数据提取器——无论您需要将许多网站整合到一个单一的搜索索引中而不合并它们的后端，想要建立一个新闻聚合器，有一个 URL 预览 web 组件的想法，或者想要定期收集竞争对手的公共价目表的内容，Diffbot 都可以提供帮助。有了非常简单的 API 调用和高度结构化的响应，您很快就可以开始运行了。在后面的文章中，我们将构建一个全新的 API 来使用 PHP 和 Diffbot，并用它重复上面的调用。我们还将在 Packagist 上托管这个库，所以您可以很容易地用 Composer 安装它。敬请期待！

## 分享这篇文章