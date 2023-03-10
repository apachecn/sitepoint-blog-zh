# 对 Yii 2.0 有什么期待

> 原文：<https://www.sitepoint.com/expect-yii-2-0/>

Yii 2.0 于去年 4 月发布测试版，第一个稳定版本的目标是在 2014 年中期。在我写这篇文章的时候，GitHub 问题列表有 300 个未解决的问题和 2913 个已解决的问题，这两个数字还在增加。到 [2.0RC 里程碑](https://github.com/yiisoft/yii2/milestones)的进度为 99%。我的猜测是车队已经很接近了，但是我们可能还要再等一会儿。当我们都在等待的时候，让我们通过一个已经可用的例子来看看我们可以期待什么。

## 一点点历史

Yii 的第一个版本在 2008 年发布后迅速流行起来。它的创始人，[薛强](https://github.com/qiangxue)，之前在[普拉多框架](http://www.pradosoft.com/)工作，并从那里使用经验和反馈来建立 Yii。

Yii 使用了许多来自其他框架、语言和库的想法:Prado、Ruby、jQuery、Symfony 和 Joomla 都是公认的灵感来源。

Yii 2.0 的第一次提交可以追溯到 2011 年，但是开发是在去年开始的。该团队做了一次重写，目的是成为最新一代 PHP 开发框架。它采用了最新的技术和特性，比如 Composer、PSR、名称空间、traits 等等。

值得一提的是，根据[下载页面](http://www.yiiframework.com/download/)显示，Yii 1.1 版支持将于 2015 年 12 月 31 日结束，因此我们确实有一些时间来开始考虑进行过渡。

## 要求

[名称空间](http://www.php.net/manual/en/language.namespaces.php)和[特征](http://www.php.net/manual/en/language.oop5.traits.php)的使用将需求提升到了 PHP 5.4。此外，在开始开发时，您将需要 mb_string、PDO 和 intl 扩展。如果您运行自己的服务器，这应该不是问题，但是如果您在共享环境中，这可能是问题。当然，随着时间的推移，这个问题将会消失。

### 装置

Yii 现在可以从 Composer 安装。我们将很快介绍这种安装方法。

目前，有两个可用的应用示例。有一个包含几个页面的基本示例，一个联系页面和一个登录页面。高级示例添加了单独的前端和后端、数据库交互、注册和密码恢复。

## 入门指南

我将从基本的例子开始。如果你以前看过 Yii，你会发现 Yii 1.1 也有同样的基础 webapp。使用以下命令安装带有 Composer 的基本示例:

```
composer.phar create-project --prefer-dist --stability=dev yiisoft/yii2-app-basic
```

然后，您可以通过打开[http://localhost/yii 2-app-basic/requirements . PHP](http://localhost/yii2-app-basic/requirements.php)来检查您的服务器是否满足需求。实际的应用程序将从[http://localhost/yii 2-app-basic/web/](http://localhost/yii2-app-basic/web/)运行。

这是要注意的第一件重要的事情:这个想法是将应用程序的文档根设置为`/path/to/application/web`，就像 Symfony 一样。目录布局与 1.1 版相比有所改变。如果仔细观察，这种变化是有意义的，并将提高应用程序的安全性。

以前，所有的应用组件(模型、视图、控制器、框架和供应商库)都位于文档根目录下的`protected`文件夹中。这样，安全性依赖于要考虑的`.htaccess`文件，这意味着你的应用程序在 Nginx 上默认是 100%不安全的。将所有应用程序组件从文档根目录移开会阻止 web 服务器将您的应用程序组件发送给用户。

您可能会发现自己在寻找实际的框架源代码。该框架是使用 Composer 安装的组件，因此它将驻留在`vendor\yiisoft\yii`目录下。在这里你会发现更多，但现在，我们就让它这样吧。

现在，让我们更改本地 web 服务器配置，并将文档根目录设置为`/path/to/application/web`。我增加了一个 http://yii2-app-basic.localhost/[的虚拟主机](http://yii2-app-basic.localhost/)，但是你可以根据自己的情况做你认为合适的事情。默认配置设置为隐藏 URL 中的脚本文件。如果您正在使用 Apache，您将需要向 web 目录添加一个`.htaccess`文件来指示 Apache 进行重写，默认情况下它不在那里。

```
RewriteEngine on
RewriteCond %{REQUEST_FILENAME} !-f
RewriteCond %{REQUEST_FILENAME} !-d
RewriteRule . index.php
```

## 看看基本的 Yii 应用程序

既然我们已经运行了基本的应用程序，有些祝贺是理所当然的…

![Yii2_installed.png](img/a2296c9dc61163142c429f66f0a6ef98.png)

谢谢！目前还没有火箭科学。

您将从起始页开始，一个静态的关于页面，一个联系页面和一个登录页面。联系页面和登录表单具有与以前相同的可用功能；验证码，表单验证和两个用户可以登录。登录和以前一样；几乎没有。尽管如此，这是一个好的开始。

基本应用程序的设计发生了巨大的变化。以前你会得到一个建立在 Blueprint CSS 框架上的应用程序，而现在我们从 Twitter Bootstrap 开始。改善？它可能被比作 Blueprint，但是 Bootstrap 比 Blueprint 曾经尝试的要多得多。Bootstrap 将为您提供各种应用程序组件，并加快构建应用程序的速度。另一方面，有些人可能会争辩说，所有的网站用 Bootstrap 看起来都是一样的(主题只是部分地解决了这个问题)，而且它还会让你的网站变得更大。无论哪种方式，与 Yii 2.0 的集成都是通过 yii2-bootstrap 扩展完成的。这使得在视图中集成引导组件变得非常容易。

您会注意到的另一件事是底部的调试栏。它是默认安装并激活的，就像在 Symfony 中一样。它允许快速访问大量关于您的配置、请求和应用程序日志的信息。它还会保存带有调试信息的请求历史记录。

![Yii2 debugger](img/e1654195ac3ec9c42dd6430aed7a33ee.png)

Yii 处理错误的方式与 PHP 不同。Yii 将所有错误(致命的和非致命的)转换为异常。这些都是通过渲染一个有洞察力的输出来处理的，该输出指出了你出错的地方或者你的代码产生了一个通知。即使是解析错误，Yii 1.1 也会退回到基本的 PHP 错误，也能很好地呈现代码的概貌。这是我们大多数人都会欣赏的。

![Yii2 error handling](img/33e7aceb54a48a10920fa7599de721c5.png)

默认情况下，Gii 也会再次出现并被激活。

![Yii2 Gii](img/838d3ea3b73410266944466e6e472abf.png)

Gii 将通过为您生成代码来帮助您，这是另一个帮助您加速开发的伟大工具。它将为您生成模型和控制器。CRUD 生成器将更进一步，为所有动作生成一个完整的 MVC 集。Gii 还将通过在需要的地方立即插入`Yii::t()`函数来生成更适合国际化(i18n)的代码。

基本应用程序现在还附带了一个简单的命令行应用程序，您可以在此基础上进行构建。Yii 1.1 已经支持这一点，但是你必须从 Wiki 中得到一个例子。

这就是你将在基本应用程序中找到的。还有一个*高级*应用示例可用。它的结构稍有不同，但为您的应用程序增加了更多现成的功能:

*   用户授权、认证和密码恢复。
*   分为前端和后端的应用程序。

继续看基本版本，让我们更仔细地看一下，深入代码…

## 什么变了？

很多都变了。有些改变可能一开始会让你感到困惑，但我发现大多数改变都是有意义的，并且容易接受。以下是一些我觉得有趣、好玩或者莫名其妙的变化。

PHP 5.4 的需求使得一些改变成为可能；数组短标记是可用的。在视图中使用 echo 短标记也是安全的，因为它不再依赖于配置设置。

```
<?php
$elements = array(1,2,3,4); //Yii 1.1
$elements = [1,2,3,4]; //Yii 2.0
?>
<?php echo $someVar; ?> //Yii 1.1
<?= $someVar ?> //always safe to use in Yii 2.0
```

一个小的变化，但你会很快发现；以前，您会使用`Yii::app()`来访问应用程序实例及其组件。在 Yii 2.0 中，这从静态函数变成了静态变量`Yii::$app`。

翻译功能`Yii::t()`仍然存在。它指示 Yii 使用 i18n 组件将提供的文本翻译成当前使用的语言。你也可以指示它替换变量。

```
<?php
echo `Yii::t('app', 'Hello, {username}!', [
  'username' => $username,
]);
?>
```

占位符的格式和样式已经过认真修改，允许更多的格式选项。一些例子:

```
<?php
echo \Yii::t('app', '{n, number} is spelled as {n, spellout}', ['n' => 81]);
echo \Yii::t('app', 'You are {n, ordinal} in line, please hold.', ['n' => 3]); //Will echo "You are 3rd in line, please wait.".
echo \Yii::t('app', 'There {n, plural, =0{are no cats} =1{is one cat} other{are # cats}}!', array(
    'n' => 14,
));
?>
```

由于这种占位符格式，DateTimeFormatter 消失了:

```
<?php
//Previously in Yii 1.1
Yii::app()->dateFormatter->formatDateTime(time(), 'medium', 'medium');

//In Yii 2.0
echo \Yii::t('app', 'The date is {0, date, short}', time());    //uses the pre-defined 'short' notation (i18n save)
echo \Yii::t('app', 'The date is {0, date, YYYY-MM-dd}', time()); //or define your own notation
?>
```

该功能由 ICU 库提供。Yii 文档称[原始文档](http://icu-project.org/apiref/icu4c/classMessageFormat.html)为“相当神秘”。我谅你也不敢去读它，并试着去理解它…让我们希望 Yii 文档能及时包含一个可读性更好的版本。

### 控制器

以前，如果您想使用 Yii 访问控制功能，`accessControl()`将是控制器的一个功能。在 Yii 2.0 中，访问控制是控制器`behavior()`的一部分:

```
<?php
    public function behaviors()
    {
        return [
            'access' => [
                'class' => AccessControl::className(),
                'only' => ['logout','login','signup'],
                'rules' => [
                    [
                        'allow' => true,
                        'actions' => ['logout'],
                        'roles' => ['@'],
                    ],
                    [
                        'allow' => true,
                        'actions' => ['login', 'signup'],
                        'roles' => ['?'],
                    ],
                ],
            ],
        ];
    }
?>
```

这几乎与 Yii 1.1 中的方式相同。

我确实注意到示例代码(不是框架本身！)缺少许多文档块，并且有许多@inheritdoc 注释。这不是你所期望的例子，但我认为这个问题会及时得到解决。

### 模型

基本款(之前的 c model)变化不大。场景现在允许您更改验证规则的实施。您可以根据您当前的场景更改需要验证的内容(例如，从前端或后端使用时具有不同规则的模型)。

不过，派生的 ActiveRecord 经历了一些重大变化。使用 ActiveRecord 进行搜索的语法变得更像是编写查询，因为 CDbCriteria 已经不存在了。它已被 ActiveQuery 所取代，使得检索信息更加容易:

```
<?php
$authors = Authors::find()
    ->where(['sitepointChannel' => $channel])
    ->orderBy('lastName')
    ->all();
?>
```

关系的定义也发生了巨大的变化。让我们以一个有博客的站点为例，这些博客发布用户评论的文章。authors 表的关系定义描述如下。我将从它在 Yii 1.1 中的样子开始:

```
<?php
//Define the relations
public function relations()
{
  return array(
    'posts' => array(self::HAS_MANY, 'Posts', 'authorID'),
    'comments' => array(self::HAS_MANY, 'Comments', array('ID'=>'PostID'), 'through'=>'posts'),
  );
}

//Querying an author with posts and comments
$activity = Author::model()->with(array('posts','comments')->find('fullname = "Arno Slatius"');
$posts = $activity->posts;
$comments = $activity->comments;
?>
```

如您所见，您将在一个大数组中定义一个活动记录的所有关系。在 Yii 2.0 中，您必须为所有这些关系定义返回 ActiveQuery 对象的 getter 方法。您必须在关系中使用关键字“through”来定义中间表之间的关系。

您现在有两个选项来定义它；通常你会在关系函数中使用`via()`方法。如果只需要数据透视表之后的表中的数据，也可以使用`viaTable()`方法定义关系。与上面的示例相同，但现在用于 Yii 2.0:

```
<?php
//Define relations by creating getter functions
public function getPosts()
{
  return $this->hasMany(Posts::className(), ['authorID' => 'ID']);
}
public function getComments()
{
  return $this->hasMany(Comments::className(), ['ID' => 'PostID'])
    ->via('Posts');
}
//If you'd only need comments you'd define it at once:
public function getComments()
{
  return $this->hasMany(Comments::className(), ['ID' => 'PostID'])
    ->viaTable(Posts::className(), ['authorID' => 'ID']);
}

//Querying an author with posts and comments
$activity= Authors::findOne(['fullname' => 'Arno Slatius']);
$posts = $activity->posts;
$comments = $activity->comments;
?>
```

这是一个相当简单的例子。通过返回 ActiveQuery 对象的 getter 函数定义关系允许做更多的事情。例如，您可以添加一个特定的函数，通过在返回的 ActiveQuery 中添加一个`where()`调用来查询获得超过 50 条评论的帖子。

一个有趣的补充是定义交叉 DBMS 关系的可能性。例如，您可以定义 MySQL 和 MongoDB 或 Redis 之间的关系，并将它们作为一个对象在您的应用程序中使用。

### 视图

在视图中要注意的主要事情是`$this`不再引用控制器实例。在一个视图中，`$this`是`yii\web\View`对象的一个实例。控制器可通过`$this->context`访问。

如我之前所说；PHP 5.4 使得简短的 echo 标记始终可用。这使得由 PHP 和 HTML 混合组成的视图更具可读性；

```
<h1><?= Html::encode($this->title) ?></h1>
```

`render()`和`renderPartial()`功能也发生了变化。在它自动`echo`渲染输出之前，你必须添加一个额外的参数来获得字符串形式的渲染输出。Yii 2.0 将总是在类似于`render()`的调用中返回一个字符串，这使得它与小部件的行为方式更加一致。

## 从 Yii 1.1 升级

是否应该考虑及时将你的 Yii 1.1 应用升级到 Yii 2.0？

Bruno kv orc 最近在 SitePoint 上写了一篇关于[遗留代码](https://www.sitepoint.com/legacy-code-cancer/)的文章。他认为应该考虑在两个月内完成重写—*,尤其是*,如果你正在使用的软件是业务关键型的。我同意他的观点，如果你对你的应用程序很认真，并且想在 Yii 1.1 生命周期结束后继续维护它，我建议你考虑一下。但一如既往；这要看你的情况。

Yii 网站上有一个专门的页面[用来升级 Yii](http://www.yiiframework.com/doc-2.0/guide-intro-upgrade-from-v1.html) 。目前，最大的问题是你的扩展。如果你依赖大量的扩展，你会有一段艰难的时间，因为社区需要一些时间来为 Yii 2.0 编写扩展。如果你是一个真正的专业人士，你当然可以认真看看你正在使用的扩展，并考虑重新编写它们。

迁移手册中有一节是关于在应用中同时运行 Yii 1.1 和 Yii 2.0 的[。对于大型项目，这是创建安全迁移路径的好方法。将您的通用代码迁移到 Yii 2.0，并花时间处理更复杂或扩展填充的部分。](http://www.yiiframework.com/doc-2.0/guide-tutorial-yii-integration.html#using-yii-2-with-yii-1)

## 结论

浏览 Yii 2.0 的权威指南让我越来越热衷于开始使用 Yii 2.0。我已经不得不阻止自己在一个新项目中使用它，因为我不能冒预生产代码出现问题的风险。

Yii 2.0 的文档质量比 Yii 1.1 有所提高。几年前，当我开始使用 Yii 1.1 时，我很难搞清楚这些东西，现在我觉得文档比以前更广泛了。

你已经看过 Yii 2.0 了吗，或者已经用它做了些什么？请告诉我们你遇到了什么。我很想知道你的经历！

## 分享这篇文章