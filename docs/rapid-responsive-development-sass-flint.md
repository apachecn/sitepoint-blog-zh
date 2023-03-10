# 使用 Sass 和 Flint 进行快速响应开发

> 原文：<https://www.sitepoint.com/rapid-responsive-development-sass-flint/>

对于任何开发人员来说，速度是至关重要的。我们一直在寻找更有效的方法来完成我们的工作，作为程序员，我们经常创造一些工具来帮助我们做到这一点。我的团队使用的工具之一是我开发的名为 [Flint](http://flint.gs/) 的新网格系统。

## 燧石是什么？

Flint 是一个用 Sass 编写的响应式网格系统，创建它是为了让开发人员能够快速生成建立在复杂基础上的响应式布局。作为一名设计师，我花了大量的时间在语法上。Flint 是非常独特的，事实上它只使用一个 mixin 用于所有输出:`_( )`。是的，它真的只是一个简单的下划线。很好记，是吧？它消除了功能上的晦涩，让我们专注于最重要的方面:布局。

## 入门指南

启动并运行 Flint 很简单:您可以配置自己的网格，或者使用 Flint 附带的默认网格设置。开发 Flint 时，能够快速配置网格是我的首要目标；因此，它提供了一个巨大的配置图，这实际上是一个 Sass 图，显示了所有项目的网格设置和断点。你不必仅仅为了让你的项目启动并运行而去处理无止境的变量。相反，您只需创建一个名为`$flint`的变量:

```
$flint: (
    "config": (

        "desktop": (
            "columns": 16,
            "breakpoint": 80em
        ),

        "laptop": (
            "columns": 12,
            "breakpoint": 60em
        ),

        "tablet": (
            "columns": 8,
            "breakpoint": 40em
        ),

        "mobile": (
            "columns": 4,
            "breakpoint": 20em
        ),

        // ...
    )
);
```

为了可读性，我省略了配置图的设置区域，因为坦率地说，这本身就是一篇文章。要了解更多关于该部分的内容，请查看 Github 上的 [Flint 文档。](https://github.com/ezekg/flint/blob/master/README.md#documentation)

使用 Flint 的一个直接优势是，它允许您为项目创建无限数量的断点，可以使用您想要的任何别名。如果你喜欢用“高潮”或“前总统”这样疯狂的名字来称呼你的断点，我完全没意见。

在意识到并非每个项目都是相同的之后，我选择了允许无限量断点的路线；它们差别如此之大，以至于每次都使用相同的网格会限制设计者和开发者，从而限制最终产品。

## 基础知识

现在我们已经讨论了什么，并且稍微接触了一下 Flint 提供的配置，让我们开始做好的事情:制作东西！火石入门超级简单。你告诉它做什么，它就做什么。如果你认为我夸大其词，请继续阅读！这里有几个简单的例子:

```
.foo {
 @include _(4);
}
```

这是 CSS 输出:

```
.foo {
 display: block;
 float: left;
 width: 93.75%;
 margin-right: 3.125%;
 margin-left: 3.125%;
}

@media only screen and (min-width: 60.0625em) {
    .foo {
 width: 23.4375%;
 margin-right: 0.78125%;
 margin-left: 0.78125%;
    }
}

@media only screen and (min-width: 40.0625em) and (max-width: 60em) {
    .foo {
 width: 31.25%;
 margin-right: 1.04167%;
 margin-left: 1.04167%;
    }
}

@media only screen and (min-width: 20.0625em) and (max-width: 40em) {
    .foo {
 width: 46.875%;
 margin-right: 1.5625%;
 margin-left: 1.5625%;
    }
}
```

我们在这里做的是告诉 Flint 创建一个跨越所有断点的块。对于这样一个小声明来说，这是一个不错的代码量，是吧？我把这种类型的论点称为递归速记。你会注意到，*实际利润*得到输出；没有人造填充物。它还认为移动优先，尽管这完全可以通过我前面提到的配置选项进行定制。

还要注意，Flint 会自动为您输出`@media`查询。你不需要一个单独的插件来处理所有的断点。在弗林特，一切都是相互关联的，但我们稍后会对此进行更多讨论。

当我们谈到断点的话题时…如果我们需要每个断点有不同的跨度呢？弗林特也帮你搞定了。不，您不需要为每个断点创建一个声明。这不是这里的规矩。再说一次，告诉弗林特做你想做的事:

```
.foo {
 @include _(8 6 4 2);
}
```

输出是:

```
.foo {
 display: block;
 float: left;
 width: 43.75%;
 margin-right: 3.125%;
 margin-left: 3.125%;
}

@media only screen and (min-width: 60.0625em) {
    .foo {
 width: 48.4375%;
 margin-right: 0.78125%;
 margin-left: 0.78125%;
    }
}

@media only screen and (min-width: 40.0625em) and (max-width: 60em) {
    .foo {
 width: 47.91667%;
 margin-right: 1.04167%;
 margin-left: 1.04167%;
    }
}

@media only screen and (min-width: 20.0625em) and (max-width: 40em) {
    .foo {
 width: 46.875%;
 margin-right: 1.5625%;
 margin-left: 1.5625%;
    }
}
```

这里发生了什么事？我们告诉 Flint，我们想要创建一个跨越桌面上的`8 columns`、笔记本电脑上的`6 columns`、平板电脑上的`4`的模块；最后一点，`2 columns`在手机上。我们通过传入一个简单的用空格分隔的参数列表来做到这一点。我把这种类型的论点称为*变量速记。*如果您注意到，操作的顺序与我们的配置图中使用的顺序相匹配，它遵循 DESC 顺序。很酷吧。

弗林特不仅仅是简单地创造柱子。它实际上是为了使响应式设计的整个过程更容易管理，因此更容易做到。

我原本打算为这篇文章写一个简短的教程式的部分，但我决定改为谈论我在使用和构建 Flint 时开发的核心概念。不要为了这篇文章而创建一些包含所有这些概念的东西，让我们单独讨论这些概念中的每一个，以及 Flint 如何帮助简化这个特定的开发领域。

## 保持一切联系

我想让 Flint 做的一件重要的事情是保持响应式设计的各个领域的联系。让我解释一下。

在大多数现代网格系统中，你的断点和网格并没有按照它们应该的方式连接起来。你有一个断点插件，然后你有一个网格系统插件。在我看来，这造成了一个重大的脱节。我这么说是什么意思？

您必须配置断点，然后在这些断点内配置您的网格。配置断点本身就是一件令人头疼的事情，*尤其是*如果它不能处理某些断点组合所需的数学运算(想想两个大小之间的查询)。但是！这个问题也有一个插件，对吗？

明白我的意思了吗？我们浪费了太多的时间来配置这些东西，而所有这些东西实际上都应该是一件事情。现在，我知道情况并非总是如此，有些工作流需要这种类型的分离，但我的工作流不需要。

因此，将 Flint 整合到您的工作流程中可以解决这种脱节。它是怎么做到的？好了，女士们先生们，这让我们直接进入我们的第一个概念！(为了简洁起见，我将使用一些夸张的代码——请原谅。)

## 加快速度

好吧，终于！我们的第一个概念！我知道，我知道…这是一条漫长的路；但希望到现在为止，你已经能够理解我们将很快讨论的各种问题。第一个是可怕的容器。这种设置在很多事情上有所不同，比如你的网格是静态的，还是流动的；固定的，或有弹性的。你明白了。我们假设它是固定的。为了创建与我们的网格一起工作的真正断点，我们可能必须做这样的事情:

```
// Breakpoints
$desktop: 80em;
$laptop: 60em;
$tablet: 40em;
$mobile: 20em;

// Now the math...
$mobile-to-tablet: (min-width: ($mobile + 0.0625em)) and (max-width: $tablet);
$tablet-to-laptop: (min-width: ($tablet + 0.0625em)) and (max-width: $laptop);
$laptop-to-desktop: (min-width: $laptop + 0.0625em));

// Finally, the actual container!
.container {
 @include container(4); 

 @include breakpoint($mobile-to-tablet) { 
 @include container(8);
    }

 @include breakpoint($tablet-to-laptop) { 
 @include container(12); 
    }

 @include breakpoint($laptop-to-desktop) { 
 @include container(16);
    }
}
```

酷毙了。这需要大量的代码，并且有很大的出错空间。我知道对于普通读者来说这可能不是一个现实的设置，但是请记住，我们在这里夸大了(即使我以前见过这个确切的设置)。我们怎么用弗林特来做这个？很高兴你问了！

```
.container {
 @include _("container");
}
```

Flint 知道你的断点，有多少，以及与每个断点相关的列数；所以它也处理所有的数学、所有的设置和所有的输出。它知道当你告诉它制作一个容器时，*你想要为每个断点*准备一个容器。

好了，我说了快点，那么继续我们的下一个概念！

## 我们如何保持一切联系

我们已经讨论过为断点提供一个插件，然后为网格提供一个插件。我知道这是有原因的。你可能一直在想我遗漏了一些关于响应式设计的东西。比如断点不仅仅用于网格。比如特定于断点的样式。我同意。但是别担心，弗林特也会处理的！

```
.foo {
 background: black;

 @include _(tablet) {
 background: white;
    }
}
```

输出是:

```
.foo {
 background: black;
}

@media only screen and (min-width: 20.0625em) and (max-width: 40em) {
    .foo {
 background: white;
    }
}
```

酷毙了。那是*而不是*很多代码。对于这样的工作，你有一大堆的理由可以利用，最棒的是？没有数学！你告诉弗林特用人类的语言*做什么*，它就会去做。希望你注意到了这里的一个趋势。这里有一个*小*的例子，你可以用它来定义媒体查询。

```
.bar {
    // ...

 @include _(from tablet to laptop) {
        // Anything between tablet and laptop
    }

 @include _(less than laptop) {
        // Anything less than laptop
    }

 @include _(greater than mobile) {
        // Anything greater than mobile
    }

 @include _(10em greater than tablet) {
        // Anything 10em greater than tablet
    }
}
```

你看看那个！记得我说过*人类可读*吗？我是认真的。这是非常有益的，尤其是在一个开发团队中工作的时候。如果很容易理解，那么每个人都在同一页上。这真是太棒了。你甚至可以创建任意的媒体查询，比如`@include _(from 12em to 55em)`。

我希望现在你能看到拥有一个处理所有这些事情的系统的好处；不仅仅是因为效率和速度，还因为你的心智健全。我开玩笑，我开玩笑。

在我们结束之前，让我们讨论一下最后一个概念。这是一个大问题，是许多漫长、令人沮丧的夜晚的罪魁祸首。可怕的(甚至比容器更可怕的)上下文。

## 这都是有关联的，伙计

是啊，不是真的，伙计。如果您曾经使用过网格系统(我假设您使用过)，那么您应该知道当一个元素嵌套在另一个元素中时，必须有一个上下文参数。这是为了保持一致的装订线，不管元素嵌套有多深。通常看起来是这样的:

```
.foo {
 @include span(12);

    .bar {
 @include span(6, 12);
    }
}
```

这里没什么令人兴奋的。如果你使用一个像样的网格系统，它会这样做。这是件好事。弗林特也这样做。弗林特还能做的就是替你完成这个重复的任务。你看，在引擎盖下，燧石非常复杂。当您告诉它创建一个 span 时，它会输出您需要的内容，但在幕后，它*也会*创建一个日志，记录您刚刚所做的事情以及该任务中涉及的变量。

充其量，这可能是令人困惑的，所以让我澄清。Flint 有一个漂亮的小`debug-mode`，它将通过输出所有这些记录的变量来帮助您可视化我们在这里讨论的内容。那么，让我们启用调试模式来看看。出于可读性的原因，我还将切换到固定的网格——我不认为有人想尝试读取百分比。

```
$flint: (
    "config": (
        // ...

        "debug-mode": true
    )
);

.foo {
 @include _(desktop, 12);
}
```

启用调试模式时，输出:

```
@media only screen and (min-width: 60.0625em) {
    .foo {
 width: 73.4375%;
 margin-right: 0.78125%;
 margin-left: 0.78125%;
 -flint-instance-count: 1;
 -flint-parent-selector: none;
 -flint-key: desktop;
 -flint-breakpoint: 80em;
 -flint-columns: 16;
 -flint-span: 12;
 -flint-output-width: 73.4375%;
 -flint-output-margin-right: 0.78125%;
 -flint-output-margin-left: 0.78125%;
    }
}
```

这是 Flint 在整个样式表中跟踪的变量日志。根据元素的不同，甚至可能更多。注意那行`-flint-parent-selector: none`。它的值是`none`，因为选择器`.foo`没有父级。让我们给`.foo`生个孩子吧:

```
.foo {
 @include _(desktop, 12);

    .bar {
 @include _(desktop, 12, "auto"); // Equivalent to: _(desktop, 12, 12)
    }
}
```

输出:

```
@media only screen and (min-width: 80em) {

    .foo {
 width: 940px;
 margin-right: 10px;
 margin-left: 10px;
 -flint-instance-count: 1;
 -flint-parent-selector: none;
        // ... etc ...
    }

    .foo .bar {
 width: 920px;
 margin-right: 10px;
 margin-left: 10px;
 -flint-instance-count: 2;
 -flint-parent-selector: .foo::desktop;
 -flint-key: desktop;
 -flint-breakpoint: 1280px;
 -flint-columns: 16;
 -flint-span: 12;
 -flint-context: 12;
 -flint-output-width: 920px;
 -flint-output-margin-right: 10px;
 -flint-output-margin-left: 10px;
    }
}
```

注意，`.bar`有一个父节点，其中`.foo`的值为`none`。我们是如何做到的？我们传入了`"auto"`的上下文值。这告诉 Flint 自动找到嵌套元素*的上下文*。

我可能不需要强调这样的功能有多有用。想要更改列跨度吗？在一个地方改变它，而弗林特在其他地方处理它。又快又省力。觉得这好得不真实？看一下`-flint-context: 12`线。是家长的跨度值！*此外，*子对象已经根据父对象的宽度调整了它的宽度。很可爱，是吧？

如果你想了解更多，我之前已经写了一篇关于这个特性的更详细的博文，名为[固定网格](http://ezekielg.com/2014/05/22/fixing-fixed-grids/)。

## 结束语

就像我前面提到的，这种类型的系统并不适合所有的工作流程。有些要求您将这些不同的方面分开，但有些则不要求。我希望现在你能看到弗林特在解决响应式设计这个大问题时能带来的好处。如果你有兴趣了解更多，或者帮助开发 Flint，[访问 Flint 的主页](http://flint.gs/)，查看 Github 上的[项目](https://github.com/ezekg/flint)或者[在 Sassmeister 上玩它](http://sassmeister.com/gist/228449011362bcdfe38c)。

有没有一两个想法？我很想在评论里听到！

## 分享这篇文章