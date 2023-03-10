# 用 PHP 寻找图像中的差异

> 原文：<https://www.sitepoint.com/finding-differences-in-images-with-php/>

我最近偶然发现了一个有趣的问题:我如何判断一幅图像是否发生了显著变化？作为 PHP 开发人员，我们必须处理的最麻烦的图像问题是如何在可接受的质量损失下调整上传的大小。

![Two similar images with hidden differences](img/e56b1d24379c5eec00a4723964d31615.png)

最后，我发现了在我之前的许多人所拥有的——考虑到一些基本数学原理的应用，这个问题变得相对简单。跟我一起去了解他们吧…

你可以在[https://github.com/undemanding/difference](https://github.com/undemanding/difference)找到本教程的代码。

## 位图

关于图像有两种流行的思考方式。第一种是单个像素的网格，由不同级别的颜色和对比度组成。通常，我们将这些颜色分解成它们的成分红、绿、蓝值。我们也可以认为它们是色调、饱和度和亮度。

思考图像的第二种方式是根据向量。线条不是像素，而是起点和终点，中间有一些描述笔画的元数据。我们将把重点放在位图上，因为它们会使整个过程更容易。

我们可以将任何图像分解成这个位图网格，代码如下:

```
$image = imagecreatefrompng($path);
$width = imagesx($image);
$height = imagesy($image);

$map = [];

for ($y = 0; $y < $height; $y++) {
    $map[$y] = [];

    for ($x = 0; $x < $width; $x++) {
        $color = imagecolorat($image, $x, $y);

        $map[$y][$x] = [
            "r" => ($color >> 16) & 0xFF,
            "g" => ($color >> 8) & 0xFF,
            "b" => $color & 0xFF
        ];
    }
} 
```

给定图像的宽度和高度，我们可以使用一个名为`imagecolorat`(在图像资源上)的函数来获得该像素处红色、绿色和蓝色的单个整数值。然后，我们可以使用位移和屏蔽从单个整数值中获得每个的单个值。

*每个红色、绿色和蓝色值都在从`0`到`255`的范围内。在二进制中，这个范围可以表示为`00000000`到`11111111`。单个整数值可以代表这些二进制值的`3`集合，而移位是从第一、第二或第三组 8 位中获得二进制值的一种方式。*

如果我们从几幅图像中创建这些网格，怎么比较呢？我们在 2300 年前就得到了答案…

## 三维距离

你还记得如何计算一条线的长度吗？你可以在纸上画的任何一条线都可以认为是三角形的斜边(长边)。为了测量它，我们可以对斜边构成的直角三角形的水平边和垂直边求平方，并计算出它们的总平方根:

```
$start = [$x = 10, $y = 15];
$end = [$x = 20, $y = 30];

$width = $end[0] - $start[0];
$width *= $width;

$height = $end[1] - $start[1];
$height *= $height;

$distance = sqrt($width + $height); // ≈ 18.03 
```

如果这条线是三维的，我们就必须在方程中加入第三个分量。这种距离测量有一个通用的数学原理，叫做[欧几里德距离](https://en.wikipedia.org/wiki/Euclidean_distance#Three_dimensions)。它的旧形式被称为毕达哥拉斯度量，因为它与我们刚刚做的斜边计算有密切的关系。

该公式可以扩展到我们想要的任意多个维度，但我们只需要三个维度:

```
$first = [$red = 100, $green = 125, $blue = 150];
$second = [$red = 125, $green = 150, $blue = 175];

$red = $second[0] - $first[0];
$red *= $red;

$green = $second[1] - $first[1];
$green *= $green;

$blue = $second[2] - $first[2];
$blue *= $blue;

$distance = sqrt($red + $green + $blue); // ≈ 43.30 
```

我们可以将这个原则应用于位图的每个像素，直到我们得到第三个不同值的位图。我们试试吧…

## 简单的图像差异

我们可以用很少的代码来应用这些原则。让我们创建一个类来加载图像，创建它们的位图并计算像素差异图:

```
class State
{
    private $width;
    private $height;
    private $map = [];

    public function __construct($width, $height)
    {
        $this->width = $width;
        $this->height = $height;
    }
} 
```

每个地图都有定义的宽度和高度。为了填充地图，我们需要将图像载入内存:

```
private static function createImage($path)
{
    $image = null;

    $info = getimagesize($path);
    $type = $info[2];

    if ($type == IMAGETYPE_JPEG) {
        $image = imagecreatefromjpeg($path);
    }
    if ($type == IMAGETYPE_GIF) {
        $image = imagecreatefromgif($path);
    }
    if ($type == IMAGETYPE_PNG) {
        $image = imagecreatefrompng($path);
    }

    if (!$image) {
        throw new InvalidArgumentException("image invalid");
    }

    return $image;
} 
```

我们可以使用 GD 图像库来读取多种图像格式。这个函数试图加载一个文件路径，可以是 JPEG、GIF 或 PNG。如果这些都不起作用，我们可以抛出一个异常。为什么这个方法是静态的？我们将在另一个静态方法中使用它:

```
public static function fromImage($path)
{
    if (!file_exists($path)) {
        throw new InvalidArgumentException("image not found");
    }

    $image = static::createImage($path);
    $width = imagesx($image);
    $height = imagesy($image);

    $map = [];

    for ($y = 0; $y < $height; $y++) {
        $map[$y] = [];

        for ($x = 0; $x < $width; $x++) {
            $color = imagecolorat($image, $x, $y);

            $map[$y][$x] = [
                "r" => ($color >> 16) & 0xFF,
                "g" => ($color >> 8) & 0xFF,
                "b" => $color & 0xFF
            ];
        }
    }

    $new = new static($width, $height);
    $new->map = $map;

    return $new;
} 
```

这个静态函数允许我们通过静态调用`State::fromImage("/path/to/image.png")`来创建新的图像状态(或映射)。如果路径不存在，我们可以引发另一个异常。然后，我们有了之前看到的相同的图像网格构造逻辑。最后，我们创建一个新的`State`，它具有定义的宽度、高度和我们构建的地图。

现在，让我们做一个比较多个图像的方法:

```
public function withDifference(State $state, callable $method)
{
    $map = [];

    for ($y = 0; $y < $this->height; $y++) {
        $map[$y] = [];

        for ($x = 0; $x < $this->width; $x++) {
            $map[$y][$x] = $method(
                $this->map[$y][$x],
                $state->map[$y][$x]
            );
        }
    }

    return $this->cloneWith("map", $map);
}

private function cloneWith($property, $value)
{
    $clone = clone $this;
    $clone->$property = $value;

    return $clone;
} 
```

我们遍历每幅图像中的每个像素。我们将它们传递给一个差分函数，并将结果值赋给一个新的映射。最后，我们创建当前`State`的克隆，因此它保持了宽度和高度，但是设置了一个新的状态。这确保了我们不会修改现有的`State`实例，而是访问一个拥有自己映射的新实例。

差异函数是什么样的？

```
class EuclideanDistance
{
    public function __invoke(array $p, array $q)
    {
        $r = $p["r"] - $q["r"];
        $r *= $r;

        $g = $p["g"] - $q["g"];
        $g *= $g;

        $b = $p["b"] - $q["b"];
        $b *= $b;

        return sqrt($r + $g + $b);
    }
} 
```

事实证明，如果我们给它们一个`__invoke`方法，我们可以将类用作函数。在这个类中，我们可以把之前看到的欧几里德距离逻辑。我们可以像这样把所有这些片段放在一起:

```
$state1 = State::fromImage("/path/to/image1.png");
$state2 = State::fromImage("/path/to/image2.png");

$state3 = $state1->withDifference(
    $state2,
    new EuclideanDistance()
); 
```

这种方法非常适合几乎完全相同的图像。当我们试图找出非常相似的照片或几乎相同的图像的有损版本的差异时，我们会看到许多细微的差异。

## 标准偏差

为了解决这个问题，我们需要消除干扰，只关注最大的问题。我们可以通过计算差异的分布来做到这一点。这种衡量数字分布的方法被称为标准偏差。

![Standard deviation graph](img/376dc80b2f644ce2d0cb65a44755843d.png)

*图片来自[维基百科](https://en.wikipedia.org/wiki/Standard_deviation)*

图像之间的大多数小差异都在标准偏差范围内(或-1σ和 1σ之间的深蓝色带)。如果我们消除标准差内的所有小差异，那么我们应该留下大差异。为了计算出哪些像素在标准偏差内，我们需要计算出平均像素值:

```
public function average()
{
    $average = 0;

    for ($y = 0; $y < $this->height; $y++) {
        for ($x = 0; $x < $this->width; $x++) {
            if (!is_numeric($this->map[$y][$x])) {
                throw new LogicException("pixel is not numeric");
            }

            $average += $this->map[$y][$x];
        }
    }

    $average /= ($this->width * $this->height);

    return $average;
} 
```

平均很容易！把所有东西加在一起，然后除以你加在一起的东西的数量。两件东西的平均值是它们的总价值除以二。400 x 300 东西的平均值就是它们的总价值除以 120，000。

*注意到我们在计算过程中对数值的期望了吗？这意味着我们首先需要生成纯数字状态(在上面的例子中是`$state3`)，或者由`EuclideanDistance`差分函数生成的状态。*

```
public function standardDeviation()
{
    $standardDeviation = 0;
    $average = $this->average();

    for ($y = 0; $y < $this->height; $y++) {
        for ($x = 0; $x < $this->width; $x++) {
            if (!is_numeric($this->map[$y][$x])) {
                throw new LogicException(
                    "pixel is not numeric"
                );
            }

            $delta = $this->map[$y][$x] - $average;
            $standardDeviation += ($delta * $delta);
        }
    }

    $standardDeviation /= (($this->width * $this->height) - 1);
    $standardDeviation = sqrt($standardDeviation);

    return $standardDeviation;
} 
```

这个计算有点类似于我们之前做的距离计算。基本思想是我们计算出所有像素的平均值。然后我们计算出每个距离平均值有多远，然后计算出所有这些距离的平均值。

我们可以将此应用回`State`:

```
public function withReducedStandardDeviation()
{
    $map = array_slice($this->map, 0);
    $deviation = $this->standardDeviation();

    for ($y = 0; $y < $this->height; $y++) {
        for ($x = 0; $x < $this->width; $x++) {
            if (abs($map[$y][$x]) < $deviation) {
                $map[$y][$x] = 0;
            }
        }
    }

    return $this->cloneWith("map", $map);
} 
```

这样，如果差异在标准偏差范围内，我们会将其排除在新的`State`之外。结果是一幅更清晰的变化图。

```
public function boundary()
{
    $ax = $this->width;
    $bx = 0;
    $ay = $this->width;
    $by = 0;

    for ($y = 0; $y < $this->height; $y++) {
        for ($x = 0; $x < $this->width; $x++) {
            if ($this->map[$y][$x] > 0) {
                if ($x > $bx) {
                    $bx = $x;
                }

                if ($x < $ax) {
                    $ax = $x;
                }

                if ($y > $by) {
                    $by = $y;
                }

                if ($y < $ay) {
                    $ay = $y;
                }
            }
        }
    }

    if ($ax > $bx) {
        throw new LogicException("ax is greater than bx");
    }

    if ($ay > $by) {
        throw new LogicException("ay is greater than by");
    }

    $ax = ($ax / $this->width) * $this->width;
    $bx = ((($bx + 1) / $this->width) * $this->width) - $ax;
    $ay = ($ay / $this->height) * $this->height;
    $by = ((($by + 1) / $this->height) * $this->height) - $ay;

    return [
        "left" => $ax,
        "top" => $ay,
        "width" => $bx,
        "height" => $by
    ];
} 
```

拼图的最后一块是一个函数，它计算出变化的边界在哪里。我们可以用这个函数来画一个方框，把一个图像和另一个图像的变化圈起来。该框从网格的边缘开始，慢慢向内移动，直到到达网格中的变化处。

## 结论

我开始这个实验是为了找出两幅图像之间的差异。你看，我想在自动化测试过程中截取一个界面的截屏，并告诉你是否有重大的变化。

应用于位图的欧几里德距离告诉我每一个改变的像素。然后，我想允许细微的变化(如小的文字或颜色变化)，所以我应用了标准差噪声消除，这样只有重大的变化会通过。最后，我可以精确地计算出不同像素占屏幕上像素总数的百分比。我可以判断一个截图是否在测试设备图像的 10%或 20%之内。

也许你需要这个来做一些完全不同的事情。也许你知道如何改进。请在评论中告诉我们！

## 分享这篇文章