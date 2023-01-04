# PHP 中的 OCR:用 Tesseract 从图像中读取文本

> 原文：<https://www.sitepoint.com/ocr-in-php-read-text-from-images-with-tesseract/>

光学字符识别(OCR)是将印刷文本转换为数字表示的过程。它有各种各样的实际应用——从数字化印刷书籍，创建收据的电子记录，到车牌识别，甚至绕过基于图像的验证码。

![Robotic eye](img/1dc67af4a3ec14b9bfc78e7c781d2a2f.png)

[Tesseract](https://github.com/tesseract-ocr/tesseract) 是一个用于执行 OCR 的开源程序。你可以在*Nix 系统、Mac OSX 和 Windows 上运行它，但是使用一个库，我们可以在 PHP 应用程序中使用它。本教程旨在向您展示如何操作。

## 装置

### 准备

为了保持简单和一致，我们将使用一个虚拟机来运行这个应用程序，我们将使用 Vagrant 来提供它。这将负责安装 PHP 和 Nginx，尽管我们将单独安装 Tesseract 来演示这个过程。

如果你想在你自己现有的基于 Debian 的系统上安装 Tesseract，你可以跳过下一部分——或者访问[README](https://code.google.com/p/tesseract-ocr/wiki/ReadMe)获得在其他*nix 系统上的安装说明，Mac OSX(提示——使用 [MacPorts](https://www.macports.org/) ！)或者 Windows。

### 流浪者设置

要设置 vagger 以便您可以跟随教程，请完成以下步骤。或者，你可以简单地从 Github 获取代码[。](https://github.com/lukaswhite/ocr-php-tesseract-example)

输入以下命令将[家园改良版](https://github.com/Swader/homestead_improved)流浪者配置下载到一个名为`ocr`的目录下:

```
git clone https://github.com/Swader/homestead_improved ocr
```

让我们将`Homestead.yml`中的 Nginx 配置从:

```
sites:
    - map: homestead.app
      to: /home/vagrant/Code/Project/public
```

…到…

```
sites:
    - map: homestead.app
      to: /home/vagrant/Code/public
```

您还需要将以下内容添加到您的 hosts 文件中:

```
192.168.10.10 homestead.app
```

### 安装宇宙魔方二进制文件

下一步是安装宇宙魔方二进制文件。

因为 Homestead Improved 使用的是基于 Debian 的 Linux 发行版，所以我们可以在用`vagrant ssh`登录 VM 后使用`apt-get`来安装它。它就像运行以下命令一样简单:

```
sudo apt-get install tesseract-ocr
```

正如我上面提到的，自述文件中有针对其他操作系统[的说明。](https://code.google.com/p/tesseract-ocr/wiki/ReadMe)

### 测试和定制安装

我们将使用一个 PHP 包装器，但是在我们开始构建之前，我们可以使用命令行测试 Tesseract 的工作情况。

首先，右键单击并保存[该图像](https://s3.amazonaws.com/f.cl.ly/items/1a273i420z2f2o2z340A/sign.png)。

(*图片由[剪贴画熊猫](http://www.clipartpanda.com/categories/caution-sign-clipart)* 提供)

在虚拟机(`vagrant ssh`)中，运行以下命令来“读取”图像并执行 OCR 过程:

```
tesseract sign.png out
```

这将在当前文件夹中创建一个名为`out.txt`的文件，如果一切正常，应该包含单词“小心”。

现在试着用文件[sign2.jpg](https://s3.amazonaws.com/f.cl.ly/items/030k0S190r140C2K1d0l/sign2.jpg):

(*图片是[这张](http://www.orgelseite.de/Sonstiges/Kurios/Einbahnstrasse.jpg)* 的改编版)。

```
tesseract sign2.jpg out
```

这一次，你会发现它产生了“Einbahnstral'ie”这个词。很接近，但不正确—尽管图像中的文本非常清晰，但它无法识别 eszett ()字符。

为了让 Tesseract 正确地读取字符串，我们需要安装一些新的语言文件——在本例中是德语。

这里有一个完整的可用语言文件列表，但是让我们直接下载合适的文件:

```
wget https://tesseract-ocr.googlecode.com/files/tesseract-ocr-3.02.deu.tar.gz
```

…提取它…

```
tar zxvf tesseract-ocr-3.02.deu.tar.gz
```

然后将文件复制到以下目录:

```
/usr/share/tesseract-ocr/tessdata
```

例如

```
cp deu-frak.traineddata /usr/share/tesseract-ocr/tessdata
cp deu.traineddata /usr/share/tesseract-ocr/tessdata
```

现在再次运行之前的命令，但使用如下的`-l`开关:

```
tesseract sign2.jpg out -l deu
```

> “deu”是德语的 ISO 639-3 代码。

这一次，文本应被正确识别为“Einbahnstraß”。

重复此过程，随意添加其他语言。

## 设置应用程序

我们将使用[这个包装器库](https://github.com/thiagoalessio/tesseract-ocr-for-php)来使用 PHP 中的 Tesseract。

我们将创建一个非常简单的 web 应用程序，允许人们上传图像，并查看 OCR 过程的结果。我们将使用 [Silex 微框架](http://silex.sensiolabs.org)来实现它——尽管如果您不熟悉它也不用担心，因为应用程序本身会非常简单。

记住本教程的所有代码都在 Github 上[可用。](https://github.com/lukaswhite/ocr-php-tesseract-example)

第一步是使用 Composer 安装依赖项:

```
composer require silex/silex twig/twig thiagoalessio/tesseract_ocr:dev-master
```

现在创建以下三个目录:

```
-  public  - uploads - views
```

我们需要一个上传表格(`views\index.twig`):

```
<html>
  <head>
    <title>OCR</title>
  </head>
  <body>

    <form action="" method="post" enctype="multipart/form-data">
      <input type="file" name="upload">
      <input type="submit">
    </form>

  </body>
</html>
```

以及结果页面(`views\results.twig`):

```
<html>
  <head>
    <title>OCR</title>
  </head>
  <body>

    <h2>Results</h2>

    <textarea cols="50" rows="10">{{ text }}</textarea>

    <hr>

    <a href="/">&larr; Go back</a>

  </body>
</html>
```

现在创建骨骼 Silex 应用程序(`public\index.php`):

```
<?php 

require __DIR__.'/../vendor/autoload.php'; 

use Symfony\Component\HttpFoundation\Request; 

$app = new Silex\Application(); 

$app->register(new Silex\Provider\TwigServiceProvider(), [
  'twig.path' => __DIR__.'/../views',
]);

$app['debug'] = true; 

$app->get('/', function() use ($app) { 

  return $app['twig']->render('index.twig');

}); 

$app->post('/', function(Request $request) use ($app) { 

    // TODO

}); 

$app->run(); 
```

如果您在浏览器中访问该应用程序，您应该会看到一个文件上传表单。如果您正在使用 Homestead Improved，您可以在以下网址找到它:

```
http://homestead.app/
```

下一步是执行文件上传。Silex 让这变得非常简单；`$request`对象包含一个`files`组件，我们可以用它来访问任何上传的文件。下面是处理上传文件的一些代码(注意这是在 POST 路径中):

```
// Grab the uploaded file
$file = $request->files->get('upload'); 

// Extract some information about the uploaded file
$info = new SplFileInfo($file->getClientOriginalName());

// Create a quasi-random filename
$filename = sprintf('%d.%s', time(), $info->getExtension());

// Copy the file
$file->move(__DIR__.'/../uploads', $filename); 
```

如您所见，我们正在生成一个准随机文件名，以最大限度地减少文件名冲突——但最终在这个应用程序的上下文中，我们如何命名上传的文件并不重要。

一旦我们在本地文件系统上有了该文件的副本，我们就可以创建一个 Tessearct 库的实例，将我们想要分析的图像的路径传递给它:

```
// Instantiate the Tessearct library
$tesseract = new TesseractOCR(__DIR__ . '/../uploads/' . $filename);
```

对图像执行 OCR 非常简单。我们简单地称之为`recognize()`方法:

```
// Perform OCR on the uploaded image
$text = $tesseract->recognize();
```

最后，我们可以呈现结果页面，将 OCR 的结果传递给它:

```
return $app['twig']->render(
    'results.twig',
    [
        'text'  =>  $text,
    ]
);
```

在一些图像上尝试一下，看看它的表现如何。如果你让它识别图像有困难，你可能会发现参考关于提高质量的[指南](https://github.com/tesseract-ocr/tesseract/wiki/ImproveQuality)很有用。

## 实际例子

让我们来看看 OCR 技术的一个更实际的应用。在本例中，我们将尝试查找并格式化嵌入图像中的电话号码。

看看下面的图片，试着把它上传到你的应用程序中:

![An image containing a telephone number](img/537cde31ed534017cc7ded7c4e89da9d.png)

结果应该是这样的:

```
:ii‘i Customer  Service  Helplines  British  Airways  Helpline  09040  490  541
```

它没有拾取正文，由于图像质量不佳，我们可能会想到这一点。它识别了电话号码，但也有一些额外的“噪音”。

为了尝试提取相关信息，我们可以做一些事情。

您可以告诉 Tesseract 将其输出限制在特定的字符范围内。因此，我们可以使用下面的代码行告诉它只返回数字:

```
$tesseract->setWhitelist(range(0,9));
```

然而，这有一个问题。它通常将字母解释为数字，而不是忽略非数字字符。例如，姓名“Bob”可以解释为数字“808”。

相反，让我们使用一个两阶段过程:

1.  尝试提取数字串，其中*可能*是电话号码
2.  使用库依次验证每个候选人，一旦找到有效的电话号码就停止

对于第一部分，我们可以使用基本的正则表达式。为了尝试确定一串数字是否是有效的电话号码，我们可以使用 [Google 的 libphonenumber](https://github.com/googlei18n/libphonenumber) 。

***注意**:我已经在 Sitepoint 上写了关于 libphonenumber 的文章，作为标题为[用 JavaScript 处理电话号码](https://www.sitepoint.com/working-phone-numbers-javascript/)的文章的一部分。*

让我们将 libphonenumber 库的 [PHP 端口](https://github.com/giggsey/libphonenumber-for-php)添加到我们的`composer.json`文件中:

```
"giggsey/libphonenumber-for-php":  "~7.0"
```

别忘了更新:

```
composer update
```

现在我们可以编写一个函数，它接受一个字符串，并尝试从中提取一个有效的电话号码:

```
/**
 * Parse a string, trying to find a valid telephone number. As soon as it finds a 
 * valid number, it'll return it in E1624 format. If it can't find any, it'll 
 * simply return NULL.
 * 
 * @param  string   $text           The string to parse
 * @param  string   $country_code   The two digit country code to use as a "hint"
 * @return string | NULL
 */
function findPhoneNumber($text, $country_code = 'GB') {

  // Get an instance of Google's libphonenumber
  $phoneUtil = \libphonenumber\PhoneNumberUtil::getInstance();

  // Use a simple regular expression to try and find candidate phone numbers
  preg_match_all('/(\+\d+)?\s*(\(\d+\))?([\s-]?\d+)+/', $text, $matches);

  // Iterate through the matches
  foreach ($matches as $match) {

    foreach ($match as $value) {

      try {

        // Attempt to parse the number
        $number = $phoneUtil->parse(trim($value), $country_code);    

        // Just because we parsed it successfully, doesn't make it vald - so check it
        if ($phoneUtil->isValidNumber($number)) {

          // We've found a telephone number. Format using E.164, and exit
          return $phoneUtil->format($number, \libphonenumber\PhoneNumberFormat::E164);

        }

      } catch (\libphonenumber\NumberParseException $e) {

        // Ignore silently; getting here simply means we found something that isn't a phone number

      }

    }
  }

  return null;

}
```

希望注释能解释这个函数在做什么。注意，如果库无法将一串数字解析为电话号码，它将抛出一个异常。这本身不是问题；我们简单地忽略它，继续下一个候选人。

> 如果我们找到一个电话号码，我们将以 [E.164](https://en.wikipedia.org/wiki/E.164) 格式返回它。这提供了一个国际认可的号码版本，我们可以用它来拨打电话或发送短信。

现在我们可以如下使用它:

```
$text = $tesseract->recognize();

$number = findPhoneNumber($text, 'GB');
```

我们需要为 libphonenumber 提供一个“提示”,说明电话号码所在的国家。你可能希望为你自己的国家改变这一点。

我们可以用一条新的路线把这些都包起来:

```
$app->post('/identify-telephone-number', function(Request $request) use ($app) { 

  // Grab the uploaded file
  $file = $request->files->get('upload'); 

  // Extract some information about the uploaded file
  $info = new SplFileInfo($file->getClientOriginalName());

  // Create a quasi-random filename
  $filename = sprintf('%d.%s', time(), $info->getExtension());

  // Copy the file
  $file->move(__DIR__.'/../uploads', $filename); 

  // Instantiate the Tessearct library
  $tesseract = new TesseractOCR(__DIR__ . '/../uploads/' . $filename);

  // Perform OCR on the uploaded image
  $text = $tesseract->recognize();

  $number = findPhoneNumber($text, 'GB');

  return $app->json(
    [
      'number'     =>  $number,
    ]
  );

}); 
```

我们现在有了一个简单 API 的基础——因此有了 JSON response——例如，我们可以将它用作一个简单移动应用程序的后端，用于添加联系人或从打印的电话号码拨打电话。

## 摘要

OCR 有许多应用程序，并且比您想象的更容易集成到您的应用程序中。在本文中，我们安装了一个开源的 OCR 包；并且，使用一个包装器库，将其集成到一个非常简单的 PHP 应用程序中。我们只是触及了可能的表面，但是希望这已经给了你一些关于如何在你自己的应用中使用这项技术的想法。

## 分享这篇文章