# 使用 Distill 提取战略档案

> 原文：<https://www.sitepoint.com/strategic-archive-extraction-distill/>

也许您正在构建一个依赖于档案的应用程序；例如，您必须不断地下载归档文件并从中提取文件。有许多图书馆可以帮助你从档案中提取文件，而一个新的玩家有能力做这项工作，那就是[distilt](https://github.com/raulfraile/distill)。

![](img/7a86e7a5a3a361aa39b1bbcde1c0d4d3.png)

使用 Distill，您可以轻松地将归档文件提取到指定的目录中。你也可以给多个档案提取，让它选择一个最佳的，按照你自己定义的策略。让我们深入研究一下代码，看看我们可以用 Distill 实现什么。

如果你想继续下去，你可以看看这个 Github 库来检查代码。

## 设置

在我们开始使用 Distill 之前，请注意，在撰写本文时，它只支持基于 Unix 的系统。原因是，Distill 使用命令行工具，这些工具目前只在基于 Unix 的系统上可用。

在[支持的格式](https://github.com/raulfraile/distill#format-support)部分，您可以清楚地看到哪些命令需要在命令行上可用。

为了向您的项目添加蒸馏，我们希望您已经有了一个使用 composer 运行的项目。您可以通过运行以下命令来安装 Distill:

```
composer require raulfraile/distill:~0.6.*
```

## 使用

首先，我们需要以几种不同的格式存档文件。如果您已经下载了上面提到的存储库，那么在`files`目录中会有 3 个归档文件。

我们从创建提取器类开始。我们在`src/SitePoint/Extractor`中创建一个名为`Extractor`的文件，内容如下。

```
namespace SitePoint\Extractor;

use Distill\Distill;

/**
 * Class to extract archived files
 */
class Extractor
{    
    /**
     * @var Distill
     */
    private $distiller;

    /**
     * Constructor
     */
    public function __construct()
    {
        $this->distiller = new Distill();
    }
}
```

我们首先创建一个从归档中提取所有文件的方法。我们需要一个实际的文件和一个解压目录。这个方法本身现在没有什么特别的。例如，您可以通过检查文件是否有效来扩展它。

该方法应该如下所示。

```
/**
 * Extract files into directory
 *
 * @param string $fromFile
 * @param string $toDirectory
 */
public function extract($fromFile, $toDirectory)
{
    $this->distiller->extract($fromFile, $toDirectory);
}
```

`fromFile`变量可以是文件所在的路径(绝对或相对)或 URL。`toDirectory`变量可以是任何要提取到的目录，绝对的也可以是相对的。蒸馏将为您完成剩下的工作。

提取归档文件是多个库都可以做的事情。distilt 的特别之处在于，您可以放入一组文件，在这些文件中，distilt 将做出最佳选择。为了创建这个方法，我们首先要向类中添加一些常量。

```
/**
 * Minimum size strategy
 */
const MINIMUM_SIZE = "\Distill\Strategy\MinimumSize";

/**
 * Uncompression speed strategy
 */
const UNCOMPRESSION_SPEED = "\Distill\Strategy\UncompressionSpeed";

/**
 * Random strategy
 */
const RANDOM = "\Distill\Strategy\Random";
```

当向 distilt 提供多个归档文件时，distilt 会根据所选择的策略选择最适合您的归档文件。使用`minimum size`策略，distill 将检查哪个文件最小，并使用那个文件。例如，当你想节省带宽时，你可以使用这个策略。

当速度对你很重要时，你应该使用`uncompression speed`策略。Distill 将检查哪个文件提取得最快，并将使用那个文件。

如果您不关心它使用哪个文件，您可以使用`random`策略为您随机选择一个文件。

因为我们也想立即提取文件，所以我们可以重用已经创建的提取方法。这就是你的方法可能的样子。

```
/**
 * Choose one of the files within the array and extract it to the given directory
 *
 * @param array  $fromFiles
 * @param string $toDirectory
 * @param string $preferredStrategy
 */
public function chooseAndExtract(array $fromFiles, $toDirectory, $preferredStrategy = self::MINIMUM_SIZE)
{
    $preferredFile = $this->distiller
        ->getChooser()
        ->setStrategy(new $preferredStrategy())
        ->setFiles($fromFiles)
        ->getPreferredFile();

    self::extract($preferredFile, $toDirectory);
}
```

基于蒸馏得到的文件数组，它会自动选择哪个文件是首选文件。这个文件将被提取到您选择的目录中。如果你照此进行，你现在应该有一个类似这样的类。

```
namespace SitePoint\Extractor;

use Distill\Distill;

/**
 * Class to extract archived files
 */
class Extractor
{
    /**
     * Minimum size strategy
     */
    const MINIMUM_SIZE = "\Distill\Strategy\MinimumSize";

    /**
     * Uncompression speed strategy
     */
    const UNCOMPRESSION_SPEED = "\Distill\Strategy\UncompressionSpeed";

    /**
     * Random strategy
     */
    const RANDOM = "\Distill\Strategy\Random";

    /**
     * @var Distill
     */
    private $distiller;

    /**
     * Constructor
     */
    public function __construct()
    {
        $this->distiller = new Distill();
    }

    /**
     * Extract files into directory
     *
     * @param string $fromFile
     * @param string $toDirectory
     */
    public function extract($fromFile, $toDirectory)
    {
        $this->distiller->extract($fromFile, $toDirectory);
    }

    /**
     * Choose one of the files within the array and extract it to the given directory
     *
     * @param array  $fromFiles
     * @param string $toDirectory
     * @param string $preferredStrategy
     */
    public function chooseAndExtract(array $fromFiles, $toDirectory, $preferredStrategy = self::MINIMUM_SIZE)
    {
        $preferredFile = $this->distiller
            ->getChooser()
            ->setStrategy(new $preferredStrategy())
            ->setFiles($fromFiles)
            ->getPreferredFile();

        self::extract($preferredFile, $toDirectory);
    }
}
```

让我们试试这个类是否工作正常。我们在项目的根目录下创建一个包含以下内容的`index.php`文件。

```
require_once __DIR__ . '/vendor/autoload.php';

$files = array(
    'files/sitepoint.zip',
    'files/sitepoint.tar.gz',
    'files/sitepoint.tar'
);

$extractor = new \SitePoint\Extractor\Extractor();
$extractor->extract(current($files), 'files/extracted/simple');
$extractor->chooseAndExtract($files, 'files/extracted/advanced', \SitePoint\Extractor\Extractor::RANDOM);
```

如果我们在终端中运行`php index.php`,我们将看到 SitePoint 徽标是从一个档案中提取出来的。

## 结论

Distill 是一个非常特殊的库，与其他归档操作工具相比，它可能缺少一些特性。但在它所专注的这个领域，它表现出色。如果您正在寻找一个可以帮助您节省带宽和/或时间的轻量级提取器，那么 Distill 可能就是您正在寻找的库。也许你甚至可以将它与一个压缩器结合起来，为你的应用程序的存档操作功能制作一个优秀的混合包？试一试，让我们知道它对你的效果如何。

## 分享这篇文章