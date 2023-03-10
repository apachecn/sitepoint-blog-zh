# 大规模使用 BEM 顶级开发人员的建议

> 原文：<https://www.sitepoint.com/working-bem-scale-advice-top-developers/>

今年年初，我写了一篇关于 BEM 和 SMACSS 的文章，重点是选择 CSS 方法的困惑。我联系了一系列不同的开发人员，得到了他们的建议，他们的成功故事和他们的恐怖故事，希望其他人可以从他们使用这些流行的 CSS 方法的经验中学习。

这篇文章很受欢迎，但有一个我在 BEM 上反复看到的问题，这篇文章没有清楚地涉及到:*你如何大规模地处理 BEM？*

说 BEM 是有益的当然很好(确实如此！)，但通常网页上的 BEM 指南都是坚持基础的。他们告诉你要把事情安排得井井有条，但是当事情变得一团糟时，并没有太多的指导。`block__element__subelement--modifier`可以吗？有没有排列 CSS 文件的最佳实践？应该使用`@extend`来继承父类的值还是应该在 HTML 中列出这些值？

在本文中，我想了解那些已经大规模使用 BEM 的开发人员的想法——他们是怎么做的？他们在这个过程中学到了什么经验教训，对我们事先了解会有好处？

你会发现有一些不同的观点——但没关系。我已经尽可能多地收录了这些回复，即使它们包含了相互矛盾的观点，这样读者就可以自己拿主意了。

## 一些背景

BEM 起源于俄罗斯搜索引擎 Yandex。从那以后，它演变成了两种不同的使用流——它可以是 Yandex 创建的整个 BEM 堆栈(CSS、JS、HTML 模板和开发工具),也可以是它背后的 CSS 方法论。

后一种方法是从 Yandex 概念经由尼古拉斯·加拉格尔、[哈里·罗伯特](http://csswizardry.com/2013/01/mindbemding-getting-your-head-round-bem-syntax/)和许多其他人的[想法发展而来的。CSS-Tricks 的团队最近也在 CSS BEM 方法论上写了一篇很棒的](http://nicolasgallagher.com/about-html-semantics-front-end-architecture/)——值得一试！

本文将涉及两个世界，因为我认为最好包括两个视角。然而，相当大比例的受访者只关注 CSS 方法，而不是 Yandex BEM 堆栈。

## 有才华的开发人员让我了解他们的想法

我将首先介绍开发人员，他们非常友好地让我了解他们的想法，以获得一些关于大规模使用 BEM 的技巧和建议。我很幸运地找到了来自世界各地的 BEM 爱好者——我收到了来自荷兰、瑞典、英国、克里米亚和爱沙尼亚的回复！非常感谢他们花时间回答我的问题。

*   **Bob Donderwinkel** ，来自鹿特丹—[网站](http://www.bdcreations.nl/)、 [Twitter](https://twitter.com/bobdonderwinkel) 的前端开发人员
*   **Hamish Taplin** ，在加的夫为 Bluegg 工作的前端开发人员——[网站](http://www.bluegg.co.uk/)，[推特](https://twitter.com/hamishtaplin)
*   **Harry Roberts** ，来自英国的顾问前端架构师、设计师、开发者，绝对的 BEM 大师——[网站](http://csswizardry.com/)，[推特](https://twitter.com/csswizardry)
*   团队周-[网站](https://www.teamweek.com/)、[推特](https://twitter.com/_sergeh)的开发人员 Serge Herkül
*   **Yandex-[网站](https://en.bem.info/)、[推特](https://twitter.com/tadatuta)的 BEM 开发组负责人 Vladimir Grinenko**
*   **Vladimir Starkov** ，来自瑞典的前端开发人员，[getbem.com](http://getbem.com)的创始人之一，之前也在 Yandex—[网站](http://iamstarkov.com/)， [Twitter](https://twitter.com/iamstarkov) 工作

让我们开始看看这些开发者的建议吧！

## 小心筑巢

嵌套是我见过的开发团队使用 BEM 的最大危险之一。很容易走极端，使事情变得不必要的复杂。我想知道，给一个类命名可以吗？还是应该坚持不超过`.block__element--modifier`中的父子两级？不同的开发人员有不同的嵌套层次，所以我将从两个角度来阐述。

### 嵌套不能超过两层

这是我个人的偏好，也是我采访的开发人员最常遵循的实践。

**弗拉迪米尔·斯塔尔科夫**指出了他对`block__element__subelement--modifier`嵌套类的担忧，他说“这是错误的方式，你应该总是可以选择使用一个元素而不使用另一个。”

**Bob Donderwinkel** 也逐渐转移到这种方式:
“我一开始用的是`block__element__subelement—modifier`符号，但那不是最优的，有点多余。所以我现在试着坚持使用`block__element—modifier`，这样看起来也更舒服；)"

“它并没有真正达到目的，而且让你的 CSS 变得过于复杂。还有一些 Sass/LESS 嵌套风格可能会产生额外的文件大小。最终的 CSS 可能会变得非常庞大。”

Yandex 的 Vladimir Grinenko 也同意避免嵌套，他建议如果需要的话，将元素名加长，而不是增加更多的嵌套层次:

“没有必要在实体名称中进行类似的嵌套。DOM 结构就足够了。嵌套在未来可能会改变，在重构时避免重命名的最好方法是从一开始就给它们正确命名。

如果您真的需要在名称中实现嵌套，可以考虑使用更长的元素名称:`block__element-subelement`"

Harry Roberts 用我所见过的最好的方式向我解释了这个想法，所以我把它放在下面，几乎完全没有编辑过:

“关于 BEM，需要知道的一件重要事情是——这也是我认为会给许多开发人员带来问题的事情——不要遍历 DOM 的每一层。打个比方，以下说法不正确:

```
.person {}
.person__head {}
.person__head__face {}
.person__head__face__eye {}
.person__head__face__eye__pupil {}
```

“这是非常冗长的，而且还强加给我们一个 DOM 结构；它非常不灵活。相反，我们应该写:

```
.person {}
  .person__head {}
    .person__face {}
      .person__eye {}
        .person__pupil {}
```

“这要简洁得多；它没有把任何 DOM 结构强加给我们，我们可以直观地缩进规则集，向我们展示隐含的 DOM 结构。

“如果你确实需要将一个模块嵌套在另一个模块中，那么就使用传统的嵌套方式。继续我们的比喻:

```
.person {
  clothing: pyjamas;

  .outside & {
    clothing: covered;
  }
}
```

“这一次我们想要设计 Person 的样式，因为它是外部的——我们不需要在这里调用或使用任何 BEMmy。(注意:为了更好的封装，我们在这里使用了 Sass 的父选择器嵌套。)或者，我们可以这样使用:

```
.person {
  clothing: pyjamas;
}

.person--covered {
  clothing: covered;
}
```

“我们可以在这里使用 BEM 来创建一个人的变体，无论他们在哪里都可以被覆盖。”

Hamish Taplin 在这一点上并没有严格遵循规则，而是尽可能保持元素和子元素的一致性:

“我尽量让事情变得简单。我没有真正的偏好，因为这种情况很少发生，但我通常发现只需将元素/子元素分离成它自己的元素就足够了。这方面的一个例子是，当一个元素中有一些重复的元素时，这个元素有自己的组件(比如页眉/页脚)。例如，您可以这样做:

```
.list__header

.list__items

    .list__item
        .list__item__header
        .list__item__body

    .list__item
        .list__item__header
        .list__item__body

    .list__item
        .list__item__header
        .list__item__body

.list__footer
```

“但是，我更喜欢将这些元素分开:

```
.list__header

.list__items

    .list-item
        .list-item__header
        .list-item__body

    .list-item
        .list-item__header
        .list-item__body

    .list-item
        .list-item__header
        .list-item__body

.list__footer
```

“所以现在我们有了一个‘list’元素和一个不与‘list’绑定的‘list-item’元素。我真的不能评论这种方法的利弊，因为我还没有给予它太多的思考，但它迄今为止对我来说工作得很好！”

### 然而，并不是每个人都同意严格的方法

Serge Herkül 对嵌套限制有不同的方法，这值得一提，因为我知道其他团队也是这样做的。他的团队确实超越了两个层次。他说:

“我们在需要的时候使用`block__element__subelement--modifier`。试着不要对‘原始’的贝姆看待事物的方式过于严格，而要让事情符合你自己的需要。”

## 在开始块作用域时要小心

Harry Roberts 发现的一个常见错误是开发人员将他们的块范围设得太高。他举了以下不正确 CSS 的例子:

```
.room {}
  .room__floor {}
  .room__desk {}
    .room__laptop {}
```

Harry 解释道，“在这里，我们可以看到我们正在将笔记本电脑的范围扩大到房间的桌子上。这里的问题是，笔记本电脑可能在我的包里，在汽车的后备箱里；那张桌子可以搬到走廊里；笔记本电脑可以直接放在房间的地板上。”

相反，重点应该是分离出可以彼此独立存在的 DOM 元素。从“最小的、最自给自足的比特”开始你的块范围。作为一个例子，Harry 对上面不正确的 CSS 提供了以下解决方案:

```
.room {}
  .room__floor {}

.desk {}

.laptop {}
```

Harry 指出，“笔记本电脑现在不再依赖任何东西，桌子也是如此。这是应该的。”

## 将你的 CSS 分成易于理解的文件

**Serge Herkül** 专注于保持事物的模块化和独立文件，以帮助扩展边界元法。他说，“我们整个水疗中心都是由模块组成的。粗略地说，我们对每个模块或模块组都有一个 SCSS 文件(如果它是由更小的模块组成的)。”他为 CSS 和 JS 推荐了模块化的方法:“试着组合你的 JS 和 CSS 模块，这样它们就可以共享相同的名字(`profile_picture.js`和`profile_picture.scss`)

Harry Roberts 说“每个文件应该尽可能的小，但也要足够大”。

**Vladimir Starkov** 以前在 Yandex 工作时，曾按块、修饰符和元素分离文件，但他发现按块分离更容易:

“在 Yandex 中，我们通常将每个块的样式、其修饰符、其元素以及它们的修饰符分开。但在现实中，很难支持所有这些分离。这就是为什么在 pet 项目中，我和以前的 Yandexers 支持每个块一个文件的结构，这种结构非常灵活，足以支持和维护。最佳技巧是将块提取到不同的文件中，每个文件一个块。frontendbabel.info/articles/bem-with-css-preprocessors/[的 Vladimir (](http://frontendbabel.info/articles/bem-with-css-preprocessors/) [@mistakster](https://twitter.com/mistakster) )用英语写了一篇精彩的文章，解释了如何在块文件中维护选择器的结构

**Bob Donderwinkel** 根据事情的复杂程度逐个项目地考虑他的方法，但也采用了一些 SMACSS 概念:

“这取决于项目。我开始时为每个块使用不同的文件，然后可能为每个元素和修饰符使用不同的文件，如果它们变得很大的话。但是现在我也经常对文件使用 SMACSS 基线，并用 BEM CSS 填充它们。”

**Vladimir Grinenko** 表示，Yandex 的重点是通过块进行分离，必要时将元素和修饰符分开:

“我们将每个块存储在单独的文件夹中。对于元素和修改器，额外的文件夹是可选的(将它们放在不同的文件中使我们有可能构建一个只包含所需部分的项目)。例如

```
blocks/
    button/
        button.css
    header/
        __logo/
            header__logo.css
        header.css
    footer/
        footer.css
```

然后，构建工具会查看有哪些 BEM 实体(块、元素和修改器),并按正确的顺序连接所需的文件

**Hamish Taplin** 发现他构建 CSS 文件的风格已经随着时间的推移而改变，并提供了他的前进方向和原因的见解:

“以前，我在一个受 SMACSS 启发的结构中构建我的 Sass:

*   基础(排版、表格、表格、网格等的基础样式)
*   助手(Sass 函数和 mixins)
*   模块(我的 BEM 模块)
*   供应商(第三方材料)

“除了‘模块’之外，每个项目的内容都是一样的，只是在每个项目的基础上做了一些‘基础’设计。这将包括该项目的基本印刷样式(标题，正文等)，如表格和形式的东西将被样式化。

“其他所有东西都放在‘模块’中，每个模块一个文件。这可能类似于:

```
- modules
-- article
-- avatar
-- dropdown
-- feature
-- list
-- media
-- nav
-- panel
-- section
```

“然而，将所有东西都放在一个‘模块’中并不适合我，因为不是所有的样式都可以抽象成一个可重用的模块。显然，应该尽可能多地编写可重用的代码，但是期限和预算的现实决定了有时你只需要得到某种风格的东西，然后继续前进。我想寻找一种更好的方法，并尝试放弃 BEM 语法来设计不可重用的样式。我在几个项目中试用了这种方法，但并不十分满意。”

哈米什当时受到了哈里·罗伯茨的一篇关于命名空间的文章的影响，哈里本人在这篇文章中对此做了进一步的解释(当回答如此完美地联系在一起时真是太棒了！):

“几周前，Harry Roberts 发表了一篇关于使用命名空间来更好地控制大规模代码库的文章。尽管我没有完全采用他的方法，但他对他使用的抽象类型提出了一些非常好的观点。这让我想起了一些事情，我想出了一个新的方法来组织我的 Sass，最近我一直在尝试这个方法:

*   基础
*   成分
*   助手
*   目标
*   小贩

“到目前为止，唯一真正的区别是，我把以前称为‘模块’的东西分成了两个文件夹，分别称为‘对象’和‘组件’。基本上，“对象”是纯粹的抽象，如“媒体”对象和我自己的网格系统，而“组件”是自包含的样式实现，如“下拉菜单”或“注册表单”。这些组件甚至可能基于一个“对象”,但更有可能是特定于项目的，而“对象”在项目之间更具重用性。”

### ITCSS 和源订购

Harry Roberts 有他自己的构建 BEM CSS 的方法，叫做 ITCSS，它使事情更容易管理和扩展。Harry 将其解释为一种方法论，允许你“根据具体性、明确性和可及性来分解你的项目”。他专注于特定的 CSS 源代码顺序，将样式排列成不同层次的特性。更具体的样式是将层放在 CSS 的末尾，全局共享的变量、混合和样式放在开始的层中。

如果你想了解更多关于 ITCSS 框架的信息，Harry 的演讲[“用 ITCSS 管理 CSS 项目”](https://www.youtube.com/watch?v=1OKZOV-iLj4&hd=1)是一个很好的资源(如果你更喜欢幻灯片而不是 YouTube 链接，[它们可以在这里找到](https://speakerdeck.com/dafed/managing-css-projects-with-itcss))。ITCSS 是一个真正经过深思熟虑的框架，用于保持 CSS 的可管理性，绝对值得一看。

## 名称空间

**Harry Roberts** 建议用名称空间来扩充 BEM。这包括为主题类(换句话说，这个类正在重新设计子元素以匹配某个主题)、状态类(例如`is-active`)等等创建一组名称空间。我最近看到不同的开发人员以不同的方式使用命名空间，有些使用了 SMACSS 的思想(例如`is-active`的状态类和整体布局容器的布局类)。

哈利很好地解释了 BEM 和命名空间之间的有益关系。他说，“我用自己的一套名称空间来扩充 BEM。这给 BEM 给我们的有意义的命名增加了一个全新的层次，它用非相对术语表示类的角色:BEM 告诉我们类是如何相互关联的(例如，`.foo__bar`必须存在于`.foo`中)，namespacing 告诉我们一个类在绝对意义上做了什么(例如，`.t-*`告诉我们这个类是主题相关的)。”

Harry 的文章概述了他使用的名称空间(Hamish 之前提到的名称空间)，提供了更多的细节，可以在[使用名称空间的更透明的 UI 代码](http://csswizardry.com/2015/03/more-transparent-ui-code-with-namespaces#detecting-namespaces)找到。

## 不要害怕长的类名

Serge Herkül 还建议关注自我记录的类名。不要怕长班名！他的建议是——“使用长的、描述性的类名，而不要使用不记录自己的短名字。”

## 避免无礼' @extend

我向开发人员提供了我见过的人们使用 BEM 的两种不同格式，以获得他们对使用哪种格式以及为什么使用的想法。第一种风格类似于面向对象的 CSS:

```
<div class="block">
    <div class="block__element"></div>
    <div class="block__element"></div>
    <div class="block__element block__element--modifier"></div>
</div>
```

第二种是使用 Sass 处理继承的风格:

```
<div class="block">
    <div class="block__element"></div>
    <div class="block__element"></div>
    <div class="block__element--modifier"></div>
</div>
```

在这一点上，我得到了所有开发者的一致同意。尽可能不要使用`@extend`——使用第一个选项！

**Harry Roberts** 建议避免`@extend`，而是在你的标记中保持类内定义的样式(就像上面的第一个标记例子)。他对此的想法是:

“通过不在 Sass 中将类‘捆绑’在一起，您可以在视图中创建更多的组合。HTML 有更好的文档记录，因为你可以看到每个类对 DOM 的一部分进行操作。你的 CSS 变得更加简洁，因为你不必在每次创建新的 UI 时都创建新的占位符类(或组合它们的清单类)。”

Vladimir Starkov 同意避免第二种选择，因为这会导致更大的 CSS 文件。他更喜欢“HTML 中的声明性”和“干净最小的 CSS”。他建议使用自动生成的系统:

“如果您对有点混乱的 HTML 类感到困惑，那么您可以自动生成它们。你可以在 github 上尝试使用 [kizu/bemto 作为 jade 模板系统，在 github](https://github.com/kizu/bemto) 上尝试使用 [azproduction/b_ 作为任何基于 nodejs 的模板系统，以达到自动化的目标。”](https://github.com/azproduction/b_)

Bob Donderwinkel 也同意面向对象的选择:

“我个人使用面向对象的方法，因为它与技术无关。关于 Sass 方法，我实际上试图通过在一个 BEM 类名中使用多个修饰符([https://gist.github.com/BobD/e51edd989e43aaf3d74d](https://gist.github.com/BobD/e51edd989e43aaf3d74d))来使事情更加“有条理”。但是它生成的 CSS 非常糟糕。”

如果你需要更多的鼓励来避免萨斯的`@extend`，**哈米什·塔普林**提供了另一个很好的例子来说明为什么把事情组织成多个类更好:

“我非常鼓励在第一个例子中使用‘多班级’方法，而不是在第二个例子中使用‘单一班级’方法。在我看来,“单类”方法的最大问题与嵌套有关，并使它成为一种反模式。考虑下面的例子，我们想对一个`.signup-form`中的`.btn`做一个小的改变，使它成为全幅。如果我们对修饰符使用多类方法，我们可以做到以下几点:

```
.signup-form {
    .btn {
        width: 100%;
    }
}
```

这里，`.btn`的任何实例都将在相关上下文中进行相应的样式化。如果我们采用了单类模式，并且我们的`.signup-form`有一个修改过的`.btn--blue`，那么我们的 CSS 就会失败，我们必须将每种类型的`.btn`修饰符添加到我们的`.signup-form`样式上下文中。显而易见，这将是一场维护噩梦！”

## Lint 你的 CSS！

Harry Roberts 也有一个出色的系统来确保所有的 CSS 都遵循正确的命名惯例:他使用 [scss-lint](https://github.com/brigade/scss-lint) 来标记他的 CSS，以确保所有的团队成员都正确地遵循惯例。正如 Harry 所说，“告诉团队使用 BEM 是很好的，但是监督它也很重要。”

Harry 甚至编写了一个 regex 来和 scss-lint 一起使用，用来检测他的命名空间 BEM 类的风格。你可以在前面提到的带有名称空间的更加透明的 UI 代码中找到。

## 选择最适合您的工具和工作流程

Yandex 的 Vladimir Grinenko 热衷于指出 BEM 不仅仅是 CSS 类。Yandex 有一整套工具来补充 BEM 方法，并将其引入您的 JavaScript 和模板中。

“BEM 方法最重要的一点是，它不仅仅是关于 CSS 名称，而是关于与 Web 组件相同的事情:每个 BEM 块都知道自己的一切(样式、JS、模板、图像、测试、文档等)。

“我们在任何地方都使用面向对象的声明性方法:在 CSS、JavaScript 和模板中。

“此外，我们有像 mixes 这样强大的概念——当一个 DOM 节点上可能有不止一个 BEM 块时:`<div class="b1 b2 b3__elem1"></div>.`”

有关 Yandex 工具和 BEM 堆栈的详细信息，请参见 [bem.info](https://en.bem.info) 。

正如我上面提到的，我认识的许多开发人员更喜欢只关注 BEM 的 CSS 方面。然而，这并不意味着牺牲拥有全功能工作流的能力。Yandex 工具对于某些项目可能是有益的，但是对于那些喜欢使用不同工作流的人来说，也有其他的选择。Yandex 甚至提供研讨会，帮助开发人员使用 BEM 进行 Gulp、Angular 等操作。

**Vladimir Starkov** 更喜欢关注 CSS 方法，并建议寻找最适合您团队的工作流程:

“基本上 BEM 只是一种 CSS 方法论，Yandex 发明的所有东西只是他们让团队之间的代码共享更简单的方式。

“从这个角度来看，很好地理解了您不需要将完整的 yandex-bem-stack 与 bemjson、bh、priv 和 bem-tools 一起使用，因为这种方式非常不灵活，很难在其他工作流中实施。

“老实说，这是我们创造 gulp-bem 和 getbem.com 的原因之一，我们希望用最简单的方式将 bem 传递给人们。”

从我从我们的开发者那里收集的信息来看，有大量的资源和工具可以帮助你以最适合你的工作流程的方式使用 BEM。如果在你的团队中可行的话，在你的 JavaScript 和模板中使用一些方面。做一些研究，找到适合你的团队和项目的工具。把它们结合起来。用一些 Yandex 的，用一些其他开发者的。试试哈里·罗伯茨的一些很酷的东西。打造自己的！权力是你的。只要确保你有一个可靠的工作流程。

## 证明文件

Vladimir Grinenko 的建议的一个方面很早就引起了我的注意。BEM 为结构化组件(您的块)提供了一个很好的基础，以便在整个项目中记录和保持一致性。如果你能在你的站点的所有层次(CSS，JS，HTML 模板和文档)中保持你的命名和结构，并由块组件组织，你的项目将变得更容易理解和扩展。结合 Harry Robert 的 ITCSS 方法论中的优秀概念，一个文档化和结构化的网站应该会变得简单一些。

## 避免过度自动化

我认为 Bob Donderwinkel 的这条建议非常有价值:

“我想我的主要建议是，如果需要的话，让你的 BEM CSS 可以移植到其他项目。这意味着不要过度自动化，这样它们只在单个项目环境中有用。这并不是说你最终会重用 CSS 的特定部分，但至少它会产生一个干净的 CSS 方法。”

## 还没有尝试在你的团队中使用 BEM？

对于那些还没有开始尝试 BEM 的人，我想我应该用 Serge Herkül 关于他在 BEM 和 Teamweek 的积极经历的一些话来结束这篇文章:

“我们最初没有任何具体的 CSS 结构，一些东西开始失控。有很多 Sass 嵌套在进行，导致了很长的 CSS 规则。一些风格影响了其他元素，这是不应该的，等等。

“转向 BEM 是一个非常简单的过程，需要大量的体力劳动，但并不太难。最后，我想不出更好的方法，我们以前遇到的所有问题都消失了。”

## 结论

我希望这一系列的访谈为那些希望在大规模项目(甚至是那些总是以你最意想不到的方式呈指数级增长的小项目)上实现 BEM 的开发人员提供了一套很好的技巧和想法。如果你之前不确定在你的下一个项目中使用 BEM，也许这会给你一点动力去尝试一下——好处是值得的！

在大规模使用边界元法方面，你有什么没有提到的技巧或想法吗？欢迎在下面的评论中发表你的想法。

## 分享这篇文章