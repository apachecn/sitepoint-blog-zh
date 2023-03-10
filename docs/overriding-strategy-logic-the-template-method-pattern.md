# 模板方法模式–覆盖策略逻辑

> 原文：<https://www.sitepoint.com/overriding-strategy-logic-the-template-method-pattern/>

第一次看[策略模式](http://en.wikipedia.org/wiki/Strategy_pattern "Strategy pattern - Wikipedia, the free encyclopedia")是一见钟情。这种模式的逻辑似乎是一种神话般的灵丹妙药，在这种情况下，组合对继承的使用不仅得到了最充分的利用，而且实现如此干净优雅，其迷人的影响令人难以抗拒。将给定契约的几个实现者传递给一个或多个消费者，允许他们在运行时通过多态来消化不同的算法，还有什么比这更吸引人的呢？

当然，当我开始编写策略代码时，“第一次约会”的魔力消失了。我意识到我的一些策略类几乎没有状态，甚至更糟，彼此共享大量重复的实现，这些都是糟糕设计的糟糕症状。

事后看来，C. S. Lewis 的这句聪明的话给我上了宝贵的一课:“经验是最残酷的老师。”我从使用策略类中学到的第一个艰难的教训是，它们迟早会遭遇根植于模式内在本质的无状态问题，不幸的是，如果没有激烈的重构技术，它们是无法解决的。

虽然在这里处理一些无状态的问题，而且有些事情我可以忍受，即使是以被指责在类构造中编写过程性代码为代价，但是当相同的逻辑出现在多个类中时，事情就完全不同了。这个方面与模式的本质或其他一些复杂的原因没有任何关系……它只是笨拙设计的一个令人讨厌的结果，很多时候是由于不愿意走继承的道路造成的。

这将我们带回到这样一个问题:通过继承而不是切换到简单的组合来消除重复的策略逻辑是否可行。的确是这样，清理过程可以通过一种普遍存在的模式进行，这种模式被称为[模板方法](http://en.wikipedia.org/wiki/Template_method_pattern "Template method - Wikipedia, the free encyclopedia")设计模式。

尽管名字很华丽，但模板方法模式背后的逻辑非常容易理解。简单地说，有一个基类(通常是抽象的)，它声明了一个具体的方法(也称为模板),负责概述某个算法的步骤或钩子。大多数情况下，基类为其中一些步骤提供了样板实现，其余的则委托给子类。反过来，子类型覆盖未实现的步骤并提供它们自己的实现，这样就创建了算法的精华版本，而共享的实现则整齐地保留在基类中。

除了它的简单本质，模板方法确实证明了当涉及到重用策略逻辑的公共块时，继承可以是一种强大的方法。在本文中，我将展示一些具体的例子，展示如何使用模式的力量以编程方式呈现一些基本的基于 jQuery 的图像滑块。

## 构建基于 jQuery 的图像滑块

尽管模板方法在一些框架中大量出现，这些框架为给定的组件提供部分实现，同时让子类型根据更精确和特定的需求覆盖抽象方法，但在互联网上很难找到展示如何在实际用例中利用模式功能的例子。我决不打算填补这个空白，因为它可以由别人来更好地填补。我在这里的目的仅仅是说明如何在生成必须遵循给定序列的标记段时使用该模式。一个很好的例子是 jQuery 驱动的图像滑块的呈现，它的“底层策略算法”可以被分解成以下步骤，当然有一些微妙的倾斜:

*   在块级元素中呈现目标图像，并与“id”或“class”属性挂钩。
*   包括 jQuery。
*   包括滑块的 jQuery 插件。
*   呈现启动和运行滑块的 JavaScript。

有了这些概述的通用步骤，创建一个基本抽象类并在其中加入一个模板方法应该很容易，该方法部分地实现了其中的一些步骤，同时将其余步骤的实现委托给几个子类。为了保持简洁，我将在教学过程中使用的插件将是漂亮的[循环](http://jquery.malsup.com/cycle/ "JQuery Cycle Plugin")。

下面是前面提到的基类的样子:

```
<?php
namespace LibraryViewHelper;

abstract class AbstractCycleSlider
{
    protected $images = array();

    public function __construct(array $images) {
        if (empty($images)) {
            throw new InvalidArgumentException(
                "No images were supplied.");
        }
        foreach ($images as $image) {
            $extension = pathinfo($image, PATHINFO_EXTENSION);
            if (!in_array($extension, array("gif", "jpg", "png"))) {
                throw new OutOfRangeException(
                    "Only GIF/JPG/PNG files are allowed.");
            }
        }
        $this->images = $images;
    }

    // Hook method for rendering the target images
    protected function renderImages() {
        $output = '<div id="slider">';
        foreach ($this->images as $image) {
            $output .= '<img src="' . $image . '">';
        }
        $output .= "</div>";
        return $output;
    }

    // Hook method for including jQuery and the Cycle plug-in
    protected function renderLibraries() {
        return <<<ENDHTML
<script src="https://ajax.googleapis.com/ajax/libs/jquery/1.7.1/jquery.min.js"></script>
<script src="http://cloud.github.com/downloads/malsup/cycle/jquery.cycle.all.latest.js></script>
ENDHTML;
    }

    // Hook method for rendering a transition effect (overridden by
    // subclasses)
    abstract protected function renderEffect();

    // Template method for rendering the slider
    public final function render() {
        return $this->renderImages()
            . $this->renderLibraries()
            . $this->renderEffect();
    }
}
```

`AbstractCycleSlider`类的唯一职责是为渲染一个基于元素周期的滑块所需的一些步骤提供实现。在这种情况下，整个渲染序列由`render()`方法定义，该方法有效地充当了所述步骤的实际“模板”。

当然，这个类在它的 slim API 背后封装了公共策略逻辑，做得相当不错，但是就其本身而言，它并不是非常有用。但是我们先不要急着下判断，因为模板方法模式提供的功能依赖于层次结构提供的好处，在层次结构中，子类型负责提供基本类型在默认情况下不能提供的策略逻辑。

如果你浏览一下`AbstractCycleSlider`，你会发现这里正是`renderEffect()`方法发挥作用的地方，它应该由一个或多个子类来实现，这样就生成了不同的——但功能齐全的——版本的基本滑块。

## 基础滑块的优化实现

创建一些工作周期驱动的滑块是一个非常简单的过程，可以归结为子类化基类`AbstractCycleSlider`，然后为它的`renderEffect()`方法提供具体的实现。

以下子类正是这样做的:

```
<?php
namespace LibraryViewHelper;

class FadeSlider extends AbstractCycleSlider
{
    protected function renderEffect() {
        return <<<ENDHTML
<script>
jQuery(document).ready(function () {
    jQuery("#slider").cycle({fx: "fade"});
});
</script>
ENDHTML;
    }
}
```

```
<?php
namespace LibraryViewHelper;

class ScrollSlider extends AbstractCycleSlider
{
    protected function renderEffect() {
        return <<<ENDHTML
<script>
jQuery(document).ready(function () {
    jQuery("#slider").cycle({fx: "scrollDown"});
});
</script>
ENDHTML;
    }
}
```

上述子类提供的`renderEffect()`方法的实现呈现了一些 JavaScript 片段，这些片段负责生成与 Cycle 插件捆绑在一起的一些过渡效果(渐变和滚动)。这展示了如何将模板方法中定义的策略逻辑的实现委托给一些子类型。

如果您想让子类发挥作用，请用一组漂亮的示例图像武装自己，并尝试以下方法:

```
<?php
use LibraryLoaderAutoloader,
    LibraryViewHelperFadeSlider,
    LibraryViewHelperScrollSlider;

require_once __DIR__ . "/Library/Loader/Autoloader.php";
$autoloader = new Autoloader();
$autoloader->register();

$images = array(
    "sample_image1.jpg",
    "sample_image2.jpg",
    "sample_image3.jpg",
    "sample_image4.jpg",
    "sample_image5.jpg"
}; 

$slider = new FadeSlider($images);
echo $slider->render();
```

如果一切按预期进行，上面的脚本应该呈现一个漂亮的图像滑块，通过使用柔和的淡入/淡出过渡效果，目标图像在滑块中循环。

如果您改变主意，想要设置一个漂亮的滚动滑块，只需创建一个`ScrollSlider`类的实例，而不是它的对应物`FadeSlider`。消费现有的滑块，或者最终创建一些新的滑块，归结起来就是为`renderEffect()`方法提供定制的实现。

另一方面，坏消息是每个 slider 类都只是其他类的细微变化，这意味着`renderEffect()`将总是被重复的代码污染其生态系统。

这并不意味着使用模板方法设计模式是一件坏事。只是在这种情况下，它的定制实现彼此太相似了，因为我们每次都在处理相同的 jQuery 插件！如果我们也基于不同的插件来呈现滑块，那么整个设计将会更有效率。

## 使用不同的插件

为了重现上面的场景，假设我们想要渲染两个不同的 i1mage 滑块:第一个将使用已经熟悉的循环，第二个将使用[轨道](http://www.zurb.com/playground/orbit-jquery-image-slider "Orbit: jQuery Image Slider Plugin from ZURB")。在这种情况下，我们应该首先重构前面的`AbstractCycleSlider` 类来定义一个更细粒度的模板方法:

```
<?php
namespace LibraryViewHelper;

abstract class AbstractSlider
{
    protected $images = array();

    public function __construct(array $images) {
        // the same constructor implementation
    }

    // Hook method for rendering the target images
    protected function renderImages() {
        $output = '<div id="slider">';
        foreach ($this->images as $image) {
            $output .= '<img src="' . $image . '">';
        }
        $output .= "</div>";
        return $output;
    }

    // Hook method for including jQuery
    protected function renderjQuery() {
        return <<<ENDHTML
<script src="https://ajax.googleapis.com/ajax/libs/jquery/1.7.1/jquery.min.js">
</script>
ENDHTML;
    }

    // Hook method for including the slider's dependencies
    // (overridden by subclasses)
    abstract protected function renderDependencies();

    // Hook method for rendering a sliding effect (overridden by
    // subclasses)
    abstract protected function renderEffect();

    // Template method for rendering the slider
    public final function render() {
        return $this->renderImages()
            . $this->renderjQuery()
            . $this->renderDependencies()    
            . $this->renderEffect();
    }
}
```

这绝对看起来更好。由于`render()`方法现在已经被分解成更原子、更细粒度的步骤，因此创建它的细化实现很简单。

下面的子类是基于循环的滑块的基本包装器:

```
<?php
namespace LibraryViewHelper;

class Cycle extends AbstractSlider
{
    protected function renderDependencies() {
        return <<<ENDHTML
<script src="http://cloud.github.com/downloads/malsup/cycle/jquery.cycle.all.latest.js">
</script>
ENDHTML;
    }

    protected function renderEffect() {
        return <<<ENDHTML
<script>
jQuery(document).ready(function () {
    jQuery("#slider").cycle({fx: "fade"});
});
</script>
ENDHTML;
    }
}
```

这个带来了一个基于轨道的滑块:

```
<?php
namespace LibraryViewHelper;

class Orbit extends AbstractSlider
{
    protected function renderDependencies() {
        return <<<ENDHTML
<link rel="stylesheet" href="/public/css/orbit.css">
<script src="/public/js/orbit.min.js">
</script>
ENDHTML;
    }

    protected function renderEffect() {
        return <<<ENDHTML
<script>
jQuery(document).ready(function () {
    jQuery("#slider").orbit();
});
</script>
ENDHTML;
    }
}
```

现在，这些子类型为`renderDependencies()`和`renderEffect()`方法提供了自定义实现，每个插件都可以通过这样的脚本独立呈现给浏览器:

```
<?php
$orbit = new Orbit($images);
echo $orbit->render();

$cycle = new Cycle($images);
echo $cycle->render();
```

我不一定是这种在 HTML 文档中添加行为层的方法的积极支持者，尽管这种方法简洁而不唐突，因为这个过程比仅仅解析几个与视图类相关联的模板或类似的东西要麻烦得多。尽管如此，它还是简要地展示了模板方法模式下的实际内容，以及如何在一些可定制的视图助手的实现中利用它的细微之处。

## 结束语

说策略逻辑传统上是通过合成来实现和消费的并不是什么新闻。为消费者提供一组独立的多态算法，这些算法遵循给定契约的形式，这使得这条道路非常有吸引力，而且不会暴露基本类型/子类型范式所带来的典型奇怪现象。

但另一方面，当某个算法的步骤应该被子类型有选择地覆盖，同时保持公共策略逻辑安全地封装在基本类型中时，基于继承的解决方案(如模板方法模式提供的解决方案)的实现可能是有效的。

就哪种模式最符合要求展开一场激烈的争论根本没有什么意义；只要确保选择一个你觉得最符合你手头需求的。

图片 via[Fotolia](http://us.fotolia.com/?utm_source=sitepoint&utm_medium=website_link&utm=campaign=sitepoint "Royalty Free Stock Photos at Fotolia.com")

## 分享这篇文章