# 管理共享主机上的 Gettext 翻译

> 原文：<https://www.sitepoint.com/managing-gettext-translations-shared-hosting/>

如果你在一家大公司工作，你的雇主迟早会开始瞄准全球市场。有了这个雄心，就需要将公司的网站翻译成一种或多种语言。即使你不在大公司工作，你也可以用你的母语(假设你的母语不是英语)推出一项新的服务来瞄准你的本地市场，用英语来瞄准全球市场。作为开发人员，我们的角色不是翻译文本，而是准备网站来支持翻译。在 PHP 中最流行的方法是通过 Gettext。这是一个很好的方法，因为它允许将翻译从应用程序中分离出来，从而实现过程的并行化。它的问题是 Apache 缓存了翻译，所以除非你能重启引擎，否则翻译文件的任何更新都不会被看到。如果你在一个没有管理员权限的共享主机上工作，这个事实尤其令人讨厌。在本文中，我将详细描述这个问题，并解释我找到的避免它的解决方案。

**注意**:如果你不熟悉 I18N、翻译和 Gettext 的概念，我强烈建议你在进一步探索本文之前阅读[这个系列](https://www.sitepoint.com/localizing-php-applications-1/)。它将为您提供比这里的简要概述更多的细节，并帮助您更好地理解这些主题。

## 设置环境

在 PHP 中有很多使用翻译的方法。我记得的最简单的方法是用一个关联数组包含所有翻译后的字符串，然后用键来检索正确的字符串。正如您可能猜到的那样，这种解决方案伸缩性不好，应该避免使用，除非您正在从事一个非常非常小的项目(可能不超过 5 行代码)。对于严肃的翻译，我们可以使用 [Gettext](http://en.wikipedia.org/wiki/Gettext) 。这种方法使我们能够为每种目标语言拥有不同的文件，这有助于保持业务逻辑、表示层和翻译(我们可以将其视为表示层的一个附加组件)之间的分离。有了 Gettext，我们可以并行处理这个过程，因为当我们在处理网站的一些功能时，翻译人员仍然可以使用像 [Poedit](http://www.poedit.net/) 这样的软件进行翻译。

翻译应该存储在具有固定结构的路径中。首先，我们将有一个根据您的喜好命名的根文件夹(例如“语言”)。在里面，我们必须为每种目标语言创建一个文件夹，其名称必须符合 ISO 3166 标准。因此，意大利语翻译的有效名称可以是“it_IT”(意大利的意大利语)、“it_CH”(瑞士的意大利语)、“en_US”(美国的英语)，等等。在包含语言代码的文件夹中，我们必须有一个名为“LC_MESSAGES”的文件夹，最后，我们将在其中存储翻译文件。

Poedit 分析网站的来源，根据我们在软件中设置的一个或多个模式提取要翻译的字符串。它将字符串保存在一个文件中。po(可移植对象)作为它的扩展，这个软件(或一个等效的)将编译成二进制。mo 文件。后者是我们和 [PHP 的`gettext()`函数](http://www.php.net/manual/it/book.gettext.php)感兴趣的格式。的。mo 文件是我们必须放在前面创建的“LC_MESSAGES”目录中的文件。

下面是一个使用`gettext()`的示例代码(该代码被加上了注释，以便让您快速了解它做了什么):

```
<?php
   // The name of the root folder containing the translation files
   $translationsPath = 'languages';
       // The language into which to translate
       $language = 'it_IT';
   // The name of the translation file (referred as domain in gettext)
   $domain = 'audero';

   // Instructs which language will be used for this session
   putenv("LANG=" . $language); 
       setlocale(LC_ALL, $language);

   // Sets the path for the current domain
   bindtextdomain($domain, $translationsPath);
   // Specifies the character encoding
   bind_textdomain_codeset($domain, 'UTF-8');

   // Choose domain
   textdomain($domain);

   // Call the gettext() function (it has an alias called _())
   echo gettext("HELLO_WORLD"); // equivalent to echo _("HELLO_WORLD");
?> 
```

一旦你将之前的代码保存在页面中并加载到你的浏览器中，如果`gettext()`能够找到翻译文件，你将在屏幕上看到你所做的翻译。

到目前为止，一切顺利。坏消息是，一旦加载了翻译，Apache 就会缓存它。因此，除非我们可以重启引擎，否则对翻译文件的任何更新都不会被看到。如果我们在一个没有管理员权限的共享主机上工作，这尤其令人讨厌。如何解决这个问题？Audero 分享 Gettext 拯救世界！

## 奥德罗分享的 Gettext 是什么

[Audero Shared Gettext](https://github.com/AurelioDeRosa/Audero-Shared-Gettext) 是一个 PHP 库(实际上只是一个类，但让我想想),它允许你绕过翻译的问题，通过`gettext()`函数加载，由 Apache 缓存。本库采用了一个简单而有效的技巧，这样你就可以一直使用最新的翻译。Audero Shared Gettext 需要 PHP 5.3 或更高版本，因为它使用名称空间，并且存在上一节中描述的结构。它主要有两种方法:`updateTranslation()`和`deleteOldTranslations()`。前者是库的核心，也是实现诀窍的方法。但这是什么把戏？让我们看看它的代码，了解更多。为了充分理解它，值得强调的是，该类的构造函数接受存储翻译的路径、要翻译成的语言以及翻译文件的名称(域)。

```
/**
 * Create a mirror copy of the translation file
 *
 * @return string The name of the created translation file (referred as domain in gettext)
 *
 * @throws \Exception If the translation's file cannot be found
 */
public function updateTranslation()
{
    if (!self::translationExists()) {
        throw new \Exception('The translation file cannot be found in the given path.');
    }
    $originalTranslationPath = $this->getTranslationPath();
    $lastAccess = filemtime($originalTranslationPath);
    $newTranslationPath = str_replace(self::FILE_EXTENSION, $lastAccess . self::FILE_EXTENSION, $originalTranslationPath);

    if(!file_exists($newTranslationPath)) {
            copy($originalTranslationPath, $newTranslationPath);
    }

    return $this->domain . $lastAccess;
} 
```

该方法做的第一件事是测试原始的二进制翻译文件是否存在。mo 文件)。如果它不存在，这个方法抛出一个异常。然后，它根据给构造函数的参数和文件最后一次修改的时间戳来计算翻译文件的完整路径。之后，它创建一个新的字符串，将原始域与之前计算的时间戳连接起来。一旦完成，这里是诀窍，它创建翻译文件的镜像副本。如果同名文件已经存在，该类会很聪明地避免这种复制。最后，它返回我们将在`bindtextdomain()`、`bind_textdomain_codeset()`和`textdomain()`中使用的新名称。这样做，Apache 将看到翻译，就好像它与原始翻译不相关一样，从而避免了缓存问题。就像我说的，简单却有效！

“伟大的奥雷里奥！”，您会想，“但是这样一来，我的文件夹会因为这些复制而变得臃肿。”没错。这就是为什么我创造了`deleteOldTranslations()`。它会从所选翻译的文件夹中删除除最后一个以外的所有镜像副本。

现在你知道 Audero Shared Gettext 是什么，它能为你做什么，让我们看看如何获得它。

## 安装 Audero 共享 Gettext

您可以通过[作曲家](http://getcomposer.org/)在您的
`composer.json`中添加以下几行来获得“奥德罗共享 Gettext”:

```
"require": {
    "audero/audero-shared-gettext": "1.0.*"
} 
```

然后运行`install`命令来解析并下载依赖项:

```
php composer.phar install 
```

Composer 会将库安装到项目的`vendor/audero`目录中。

如果您不想使用 Composer(实际上您应该使用),您可以通过运行命令 [Git](http://git-scm.com/) 获得 Audero 共享 Gettext:

```
git clone https://github.com/AurelioDeRosa/Audero-Shared-Gettext.git 
```

您的最后一个选择是访问存储库，并将其下载为存档。

## 如何使用 Audero 共享 Gettext

假设您使用 Composer 获得了 Audero 共享 Gettext，那么您可以依靠它的自动加载器来动态加载该类。然后，您必须创建一个`SharedGettext`实例并调用您需要的方法。您可以使用前面提到的方法之一，如下例所示。

```
<?php
    // Include the Composer autoloader
    require_once 'vendor/autoload.php';

    $translationsPath = 'languages';
        $language = 'it_IT';
    $domain = 'audero';

    putenv('LC_ALL=' . $language);
        setlocale(LC_ALL, $language);

    try {
       $sharedGettext = new Audero\SharedGettext\SharedGettext($translationsPath, $language, $domain);
       // Create the mirror copy of the translation and return the new domain
       $newDomain = $sharedGettext->updateTranslation();

       // Sets the path for the current domain
       bindtextdomain($newDomain, $translationsPath);
       // Specifies the character encoding
       bind_textdomain_codeset($newDomain, 'UTF-8');

       // Choose domain
       textdomain($newDomain);
        } catch(\Exception $ex) {
       echo $ex->getMessage();
    }
?> 
```

## 结论

本文向您介绍了 Audero Shared Gettext，这是一个简单的库(emh…class ),它允许您绕过翻译问题，通过`gettext()`函数加载，由 Apache 缓存。Audero Shared Gettext 具有广泛的兼容性，因为它使用了名称空间，所以要求您至少拥有 PHP 5.3(现在已经发布了一段时间)。请随意使用演示和存储库中包含的文件，如果发现问题，请提交请求和问题。我已经在 CC BY-NC 4.0 许可下发布了 Audero 共享 Gettext，所以可以免费使用。

你遇到过这个问题吗？你是怎么解决的？不要害羞，在评论中发表你的解决方案吧！

## 分享这篇文章