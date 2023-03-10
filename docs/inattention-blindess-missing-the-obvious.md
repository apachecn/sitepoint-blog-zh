# 注意力不集中失明——错过明显的东西

> 原文：<https://www.sitepoint.com/inattention-blindess-missing-the-obvious/>

你的代码不起作用！你不知道为什么，你盯着它看了几个小时。你脾气不好。你落后于计划。你越来越生气了。为什么不管用？为什么看不到 bug？不会这么难找吧？

出于纯粹的沮丧(相对于理性思考)，你叫来了一个同事来帮你找到问题。几秒钟之内，他们就做了看起来超出你能力范围的事情——他们找到了问题的根源。更糟糕的是，它简单得令人痛苦。

您对代码的工作感到满意，而您那头发尖尖的老板也因为项目重回正轨而松了一口气。生活恢复了正常，你开始编写更多的代码。然而，尽管如此，有一个词在你的脑海深处溃烂。一个简单的词:为什么？为什么同事可以很快找到问题，而你却一个小时都找不到？

振作起来，我的朋友。这可能是我们所有人都容易受到的影响，是你感知和识别事物的能力出现了问题。我们来讨论一下这个有趣的现象，你可能不太熟悉:不注意盲视。

## 这是怎么回事？

如果你以前没听说过，根据维基百科的说法，[注意力不集中失明](http://en.wikipedia.org/wiki/Inattentional_blindness)(也称为知觉失明)是:

> …在执行其他需要注意力的任务时，未能注意到视野中的意外刺激…这通常是因为人类的刺激超载，不可能注意到周围环境中的所有刺激。这是因为他们没有意识到未被注意的刺激。

这个概念有很多内容，所以我会尽可能地总结一下。注意力不集中失明是一种与人们对通过感官接收的数据的感知有关的现象。

不管某样东西是否清晰可见，或者用俚语来说就是“就在他们眼前”，人们实际上可能不会察觉到它的存在。他们实际上看到了它，不管它是逻辑错误、缺少分号还是缺少语句。它记录在他们的意识中，但他们似乎没有识别出那里有什么(或者它足够重要，值得他们关注)。

最终结果？对他们来说，实际上它并不存在。

## 隐形大猩猩测试

关于这个主题最著名的例子之一是[隐形大猩猩测试](http://www.theinvisiblegorilla.com/)，它是由[丹尼尔·西蒙斯和克里斯多夫·查布里斯](http://www.theinvisiblegorilla.com/biographies.html)进行的。如果你不熟悉这个测试，[如果它在 YouTube 上](http://www.youtube.com/watch?v=vJG698U2Mvo)的话，你可以看看这个视频。受试者被要求观看录像，录像中有两个队互相传球，并计算一个队传球的次数。受试者没有被告知的是，在视频中，一个穿着大猩猩服装的人穿过人群，拍打着胸部，然后走开。你会认为这是显而易见的，对吗？报告有所不同。大约 50%的人没有表示他们看到了大猩猩。

很奇怪，对吧？一个穿着大猩猩服装的人清楚地走进来查看，并大摇大摆地走来走去。怎么会有人错过这么明显的东西？

在不深入研究的情况下，有许多理论可以解释其中的原因。科学家们提出了四种可能的原因:

*   **醒目:**如果一个项目对观看者来说不是特别明显或缺乏意义，它可能会被错过。
*   **脑力负荷与工作记忆:**人在给定的时间内只能有意识地集中于有限的信息量。
*   **期望:**大猩猩出乎意料，所以被过滤掉了。
*   **能力:**人在给定时间内集中注意力的能力是有限的。

我们的生活中有如此多的事情，尽管我们的经验需要集中和关注，但我们必须过滤掉大量的刺激才有意义，所以我们专注于重要和有意义的信息。

## 这如何应用于编程？

最近我读了一篇有趣(简短)的论文，题目是[什么让代码难以理解？迈克尔·汉森、罗伯特·l·戈德斯通和安德鲁·拉姆斯丁。摘要如下:](http://arxiv.org/pdf/1304.5257v2.pdf)

> 哪些因素影响代码的可理解性？先前的研究表明，期望一致的程序应该花费更少的时间来理解，并且不容易出错。我们展示了一个实验，其中有编程经验的参与者预测了十个小 Python 程序的准确输出。
> 
> 我们使用程序版本之间的细微差异来证明看似微不足道的符号变化会对正确性和响应时间产生深远的影响。我们的结果表明，在大多数情况下，经验会提高性能，但当违反了关于相关代码语句的基本假设时，可能会严重损害性能。

仔细记下摘要的后半部分:

> 我们的结果表明，在大多数情况下，经验会提高性能，但当违反了关于相关代码语句的基本假设时，可能会严重损害性能。

违反基本假设是如何对我们理解代码的能力产生负面影响的？好吧，我就不反刍论文了。相反，我将回顾它提出的一些观点，以开放式的方式依次讨论每一个观点。通过这样做，我希望当我们错过了显而易见的东西时，我们可以找出背后的一些原因，以及其他人是如何看起来不被我们狭隘的视野所阻碍的。

## 特定的语言体验/程序员期望

经验增加是阻碍还是帮助？这张有点模糊。随着我们了解的越来越多，我们是否会对情况和条件做出假设，比如哪些可行，哪些不可行，哪些初级开发人员不行？

我相信你对这种情况很熟悉，在这种情况下，更有经验的人，因为他们拥有的所有知识和经验，会假设事情会或不会工作。一个完全不熟悉主题的新人可能会很快提出建议，例如:

*   这样做怎么样？
*   你试过 X 吗？
*   你想做什么？

具有讽刺意味的是，增长的知识成了阻碍，而不是帮助。这里有一个具体的例子。我们是不是开始期望代码必须以某种方式编写，比如用 [K & R(克尼根和里奇)](http://en.wikipedia.org/wiki/Indent_style#K.26R_style)对[奥尔曼](http://en.wikipedia.org/wiki/Indent_style#Allman_style)的风格缩进？

结果，我们如此习惯于遵从惯例，以至于当事情不符合惯例时，我们很难读懂它们。我对此感到内疚。是吗？

## 运算符重载

在大多数语言中，特别是 PHP 和 JavaScript，操作符可以有多种用途，或者如果不小心使用，会产生意想不到的结果。如果你习惯了，这很好，但是如果你不习惯呢？如果你累了，压力大了，或者处于压力之下怎么办？

举下面的例子(来自 PHP 手册的[赋值操作符部分](http://www.php.net/manual/en/language.operators.assignment.php)):

```
$a = 3; 
$a += 5; 
$b = "Hello "; 
$b .= "There!";
```

我们可以很容易地看到，`$a`的结果是 8，而`$b`的结果是“你好！”。但是，如果我们按如下方式混合操作符会怎么样呢:

```
$a = 3; 
$a .= 5; 
$b = "Hello "; 
$b += "There!";
```

现在会有什么结果呢？

## 句法噪音

[根据马丁·福勒](http://martinfowler.com/bliki/SyntacticNoise.html)的说法，句法噪音是:

> 无关的字符并不是我们真正需要说的部分，而是为了满足语言定义。

他接着说，句法噪音是不好的，因为它“模糊了我们程序的意义，迫使我们去弄清楚它在做什么。”现在，我同意马丁的观察，这是主观的，所以很可能你和我会完全不同意。

然而，[这篇关于 SourceForge](http://xlr.sourceforge.net/concept/synnoise.html) 的文章指出，部分由复杂语法引入的语法噪音是双向的:

> 语法噪音使程序的维护变得复杂，并使学习曲线变得陡峭。然而，重要的是要注意复杂的语法规则可能使编译器更容易检测和报告错误。

让我们看一个“嘈杂”的 PHP 的例子。这是对 Paul W. Homer 使用的原始 Java 示例的修改[:](http://theprogrammersparadox.blogspot.de/2010/07/syntactic-noise.html)

```
public function calcIndex() 
{ 
    $weightedSum = 0; $bondCount = 0; $status = false;
    for ($i = 0; $i < count($this->bonds); $i++) {
        $status = $this->yieldCalc($this->bondInfo, self::YIELD, true,
            $this->bondFacts);
        if ($status != true) {
            throw new YieldCalculationException(
                $this->getCalcError()); 
        } 
        $weightedSum += $this->calcWeight($this->bondFacts,
            Weight::NORMAL); 
        $bondCount++;
    } 
    return $weightedSum / $bondCount; 
}
```

如果你熟悉所有的词典结构，上面的代码就相当简单了。但是对于经验不足的人来说，可能需要更多的时间来解读。让我们一步一步来。

```
$weightedSum = 0.0; $bondCount = 0; $status = false;
```

这里我们初始化了三个变量:`$weightedSum`、`$bondCount`和`$status`，它们都在一行中。如果我们在单独的行上分别初始化它们，可能会更清楚。

```
for ($i = 0; i < count($this->bonds); $i++) {
```

有一段时间，这个建筑是我最喜欢的建筑之一。它提供了初始化器、循环限制器和递增器。然而，那里发生了很多事情，有时，在匆忙中，我们可能没有像我们想象的那样组装它。

一种更简单的方法可能是用`foreach`来代替，它将是:

```
foreach ($this->bonds as $item) {
```

意图更容易理解，也更难引入错误。

```
$weightedSum += $this->calcWeight($this->bondFacts, Weight::NORMAL);
$bondCount++;
```

这里我们有一个紧凑的变量赋值(`+=`)和递增(`++`)的方法。是的，它占用的空间更少，但它一定是清晰的或指示性的吗？这取决于你，但也许下面可能会更清楚:

```
$weightedSum = ($weightedSum + $this->calcWeight($this->bondFacts,
    Weight::NORMAL)); 
$bondCount = ($bondCount + 1);
```

我之前说过，这部分是主观的，更不用说是琐碎的。但希望你能看到，有时我们使用的概念既能帮助我们，也能阻碍我们。

我相信我们都知道有些开发人员认为编码的唯一方式是尽可能简洁明了。不管是出于摇滚明星的身份还是为了保护工作，谁知道呢。但是只有极少数人能读懂他们的代码。当代码必须长期维护时，这不是正确的方法。

## 空白–垂直和水平

该论文指出，当代码被垂直地聚集在一起时，期望代码是逻辑相关的。以 Zend Framework 2 中的`ZendAuthenticationAdapterDbTable.php`为例:

```
public function __construct( 
    DbAdapter $zendDb, $tableName = null, 
    $identityColumn = null, $credentialColumn = null, 
    $credentialTreatment = null) 
{ 
    $this->zendDb = $zendDb; 

    if (null !== $tableName) { 
        $this->setTableName($tableName); 
    } 

    if (null !== $identityColumn) { 
        $this->setIdentityColumn($identityColumn); 
    } 

    if (null !== $credentialColumn) { 
        $this->setCredentialColumn($credentialColumn); 
    } 

    if (null !== $credentialTreatment) { 
        $this->setCredentialTreatment($credentialTreatment); 
    } 
}
```

通过很好地使用垂直空格，我们可以看到哪些代码是逻辑相关的。基于`$tableName`、`$identityColumn`、`$credentialColumn`和`$credentialTreatment`，我们可以很容易地识别各种条件。此外，代码对水平空格(缩进)的正确使用给了代码一个非常逻辑和清晰的结构。

由于代码的逻辑结构(是的，我对组织良好的代码很着迷),眼睛更容易跟随它的结构，从而很容易将其保存在短期记忆中。

想象一下，如果这些东西都聚集在一起，没有分组、缩进或一般的秩序感。会一样容易理解吗？

## 结论

本文中的例子故意显得琐碎，但我希望您不会因此而忽略了要点。也就是说，我们在识别有问题的代码时所面临的问题经常对我们隐藏起来，不是因为我们缺乏技能，而是因为像期望、假设或分心这样的小事。

当其他人能够解决我们绞尽脑汁寻找的问题时，这不一定是对我们的不良反映。他们只是比我们有新的(或者有时只是不同的)视角。

所以下次你因为没有找到丢失的分号而责备自己时，不要这样做。这种情况时有发生，现在你对原因有了更好的理解。如果你对这个主题感兴趣，我推荐以下资源供你进一步阅读:

*   [疏忽性失明——维基百科](http://en.wikipedia.org/wiki/Inattentional_blindness)
*   是什么让代码难以理解？
*   [注意力不集中失明——用户体验和界面设计师——着迷](http://blog.enchant.co/post/19214649861/inattention-blindness)
*   [疏忽性失明:在众目睽睽之下失踪——代码项目](http://www.codeproject.com/Articles/467719/Inattentional-Blindness-Missing-in-Plain-Sight)

图片由[戴夫·斯托克斯](http://www.flickr.com/photos/33909700@N02/3159666520/)通过 Flickr 提供

## 分享这篇文章