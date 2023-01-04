# 从 WAV 文件中提取摘录

> 原文：<https://www.sitepoint.com/extract-an-exceprt-from-a-wav-file/>

尽管 PHP 以构建网页和应用程序而闻名，但它能做的不止这些。我最近需要从 WAV 文件中即时提取一段音频，并让用户通过浏览器下载。我试图找到一个符合我需要的库，但没有成功，不得不自己编写代码。这是一个深入研究 WAV 文件是如何制作的好机会。在这篇文章中，我将给你一个 WAV 文件格式的简要概述，并解释我开发的库， [Audero Wav Extractor](https://github.com/AurelioDeRosa/Audero-Wav-Extractor "Audero Wav Extractor repository") 。

## WAV 格式概述

波形音频文件格式，也称为 WAVE 或 WAV，是用于存储数字音频数据的 Microsoft 文件格式标准。WAV 文件由一组代表音频文件不同部分的不同类型的块组成。你可以把这种格式想象成一个 HTML 页面:第一个块就像网页的`<head>`部分，所以在它里面你会找到几条关于文件本身的信息，而包含音频数据本身的块就在页面的`<body>`部分。在这种情况下，单词“chunk”指的是文件中包含的数据部分。

最重要的格式块是“RIFF”和“Data”，前者包含文件的字节数，“Fmt”包含重要信息，如采样率和通道数，后者实际上包含音频流数据。每个块必须至少有两个字段，id 和大小。此外，每个有效的 WAV 必须至少有两个块:Fmt 和 Data。第一个通常在文件的开头，但是在 RIFF 之后。

每个块都有自己的格式和字段，一个字段构成了块的一个子部分。WAV 格式在过去没有被指定，这导致文件头没有严格遵循规则。因此，当您处理音频时，您可能会发现一个音频有一个或多个字段，甚至最重要的字段被设定为零或错误的值。

为了让你知道一个块里面有什么，每个 WAV 文件的第一个是 RIFF。它的前 4 个字节包含字符串“RIFF ”,接下来的 4 个字节包含文件的大小减去用于这两段数据的 8 个字节。RIFF 块的最后 4 个字节包含字符串“WAVE”。你可能会猜测这些数据的目的是什么。在这种情况下，您可以使用它们来识别您正在解析的文件是否确实是 WAV 文件，就像我在我的库的`Wav`类的`setFilePath()`方法中所做的那样。

另一个需要解释的有趣的事情是 WAV 文件的持续时间是如何计算的。您需要的所有信息都可以从前面提到的两个必备块中检索到，它们是:数据块大小、采样速率、通道数和每个样本的位数。以秒为单位计算文件时间的公式如下:

```
time = dataChunkSize / (sampleRate * channelsNumber * bitsPerSample / 8)
```

假设我们有:

```
dataChunkSize = 4498170
sampleRate = 22050
channelsNumber = 16
bitsPerSample = 1

```

将这些值应用到公式中，我们得到:

```
time = 4498170 / (22050 * 1 * 16 / 8)
```

结果是 102 秒(四舍五入)。

深入解释 WAV 文件的结构超出了本文的范围。如果你想进一步研究它，读读我在做这个的时候看到的这几页:

*   [https://ccrma.stanford.edu/courses/422/projects/WaveFormat/](https://ccrma.stanford.edu/courses/422/projects/WaveFormat/)
*   [http://www-mmsp . ECE . mcgill . ca/documents/audio formats/WAVE/WAVE . html](http://www-mmsp.ece.mcgill.ca/documents/AudioFormats/WAVE/WAVE.html)

## 什么是奥德罗 Wav 提取器

Audero Wav Extractor 是一个 PHP 库，允许你从 Wav 文件中提取一个 exceprt。您可以将提取的摘录保存到本地硬盘，通过用户的浏览器下载，或者以字符串形式返回供以后处理。该库唯一的特殊要求是 PHP 5.3 或更高版本，因为它使用名称空间。

该库的所有类都在`WavExtractor`目录中，但是您会注意到还有一个额外的目录`Loader`，在那里您可以找到该库的自动加载器。开发人员的切入点是`AuderoWavExtractor`类，它有项目的三个主要方法:

*   `downloadChunk()`:下载摘录
*   `saveChunk()`:保存在硬盘上
*   `getChunk()`:以字符串形式检索 exceprt

所有这些方法都有相同的前两个参数:`$start`和`$end`，它们分别代表要提取的部分的开始和结束时间，以毫秒为单位。此外，`downloadChunk()`和`saveChunk()`都接受可选的第三个参数来设置提取片段的名称。如果没有提供名称，则该方法会以“InputFilename-Start-End.wav”的格式自动生成一个名称。

在`WavExtractor`目录中有两个子文件夹:`Utility`，包含有一些实用方法的`Converter`类，和`Wav`。后者包含了`Wav`、`Chunk`和`ChunkField`类。如您所料，第一个表示 WAV 文件，由一个或多个块(属于`Chunk`类型)组成。这个类允许你获取 WAV 文件头，音频的持续时间，以及其他一些有用的信息。它最相关的方法是`getWavChunk()`，通过从文件中读取字节来检索指定的音频部分。

`Chunk`类代表 WAV 文件的一部分，它由包含在`Chunk`文件夹中的专门类扩展。后者不支持所有现有的块类型，只支持最重要的类型。未识别的部分由泛型类管理，在整个过程中会被忽略。

描述的最后一个类是`ChunkField`。正如我指出的，每个块都有自己的类型和字段，并且每个都有不同的长度(以字节为单位)和格式。这是非常重要的信息，因为您需要传递正确的参数来使用 PHP 的`pack()`和`unpack()`函数正确解析字节，否则您会收到一个错误。为了帮助管理数据，我决定将它们包装到一个类中，保存每个字段的格式、大小和值。

## 如何使用 Audero Wav 提取器

您可以通过 Composer 获得“au dero Wav Extractor”[，在您的`composer.json`文件中添加以下行并运行其安装命令。](https://www.sitepoint.com/php-dependency-management-with-composer/ "PHP Dependency Management with Composer")

```
"require": {
"audero/audero-wav-extractor": "2.1.*"
}
```

Composer 将下载该库并将其放在项目的`vendor/audero`目录中。

或者，您可以直接从库下载库[。](https://github.com/AurelioDeRosa/Audero-Wav-Extractor "Audero Wav Extractor repository")

要提取一个 exceprt 并强制下载到用户的浏览器，您需要编写类似如下的代码:

```
<?php
// include the Composer autoloader
require_once "vendor/autoload.php";

$inputFile = "sample1.wav";
$outputFile = "excerpt.wav";
$start = 0 * 1000; // from 0 seconds
$end = 2 * 1000; // to 2 seconds

try {
$extractor = new AuderoWavExtractorAuderoWavExtractor($inputFile);
$extractor->downloadChunk($start, $end, $outputFile);
echo "Chunk extraction completed. ";
}
catch (Exception $e) {
echo "An error has occurred: " . $e->getMessage();
}
```

在第一行中，我包含了 Composer autoloader，然后设置了我将要使用的值。如您所见，我提供了源文件、输出路径，包括文件名和我想要提取的时间范围。然后我创建了一个`AuderoWavExtractor`的实例，给源文件作为参数，然后调用`downloadChunk()`方法。请注意，因为输出路径是通过引用传递的，所以您总是需要将其设置为一个变量。

让我们看另一个例子。我将向您展示如何选择时间范围并将文件保存到本地硬盘。此外，我将使用项目中包含的自动加载器。

```
<?php
// set include path
set_include_path(get_include_path() . PATH_SEPARATOR . __DIR__ . "/../src/");

// include the library autoloader
require_once "AuderoLoaderAutoLoader.php";

// Set the classes' loader method
spl_autoload_register("AuderoLoaderAutoLoader::autoload");

$inputFile = "sample2.wav";
$start = 0 * 1000; // from 0 seconds
$end = 2 * 1000; // to 2 seconds

try {
$extractor = new AuderoWavExtractorAuderoWavExtractor($inputFile);
$extractor->saveChunk($start, $end);
echo "Chunk extraction completed.";
}
catch (Exception $e) {
echo "An error has occurred: " . $e->getMessage();
}
```

除了加载器配置之外，该代码片段与前面的非常相似。事实上，我只做了两处修改:第一处是方法名为`saveChunk()`而不是`downloadChunk()`，第二处是我没有设置输出文件名(将使用前面解释的默认格式)。

## 结论

在这篇文章中，我向你展示了“Audero Wav Extractor ”,以及如何轻松地从给定的 Wav 文件中提取一个或多个片段。我为一个工作项目编写了这个库，该项目要求使用非常窄的一组图块，因此如果 WAV 或其标题严重损坏，那么这个库可能会失败，但是我编写了代码来尝试在可能的情况下从错误中恢复。请随意使用演示和库中包含的文件，因为我已经在 [CC BY-NC 3.0 许可证](http://creativecommons.org/licenses/by/3.0/ "Creative Commons Attribution NonCommercial 3.0")下发布了它。

## 分享这篇文章