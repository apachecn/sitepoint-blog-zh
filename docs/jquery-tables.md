# jQuery 表:用于创建和管理表的插件

> 原文：<https://www.sitepoint.com/jquery-tables/>

*这篇关于 jQuery 表格的热门文章于 2016 年 11 月 10 日更新，以反映 jQuery 表格插件的当前状态。*

表格是 HTML 中最古老的元素之一。曾经有一段时间，表格被广泛用于内容和网页布局。令人欣慰的是，基于表格的布局基本上已经过时了(除了 HTML 电子邮件)，现在当你需要表格时，它的最初用途就是显示表格数据。

作为 HTML 规范的一部分，表格通常不适合以移动为中心的设计，也不提供任何现成的高级功能。表格基本上只是显示你的数据，对于一些用例来说，这很好。

然而，您通常会希望表格更加现代化，要么通过提供响应性布局来完全支持移动生态系统，要么通过提供搜索、过滤、排序或其他高级功能来支持移动生态系统。

在本文中，我们将通过一系列基于 jQuery 的插件来创建、扩展或增强您的表。这些插件的范围从轻量级的重新设计到全功能的解决方案。

## 动态表

Dynatable 是一个非常强大的深度表格插件，它创建的表格具有强大的功能，如过滤、排序和分页。Dynatable 非常注重开发人员，让您控制表格的呈现方式、过滤和搜索的操作方式，并为您提供高度可定制的解决方案。

![A table showing how Dynatable operates](img/d4154a5a2533465286ef845970ad4eb2.png)

如果你正在寻找一个能让你控制一切的插件，那么这个插件绝对值得一看。如果你对 [JavaScript](https://www.sitepoint.com/premium/books/javascript-novice-to-ninja-2nd-edition/?utm_source=blog&utm_medium=articles) /jQuery 完全陌生，那么一些例子和/或作者的文档可能会把你吓跑。然而，一切都可以通过使用默认配置来设置和运行，所以没有必要进一步定制。

一个令人惊讶的事情是，该插件相对较旧(上一次主要提交是在 2014 年)。在这一过程中已经有过几次提交，也有来自社区的参与，但是很难说这个插件是否还在开发中(这可能会影响到你是否想获得帮助或贡献)。除此之外，它是一个很好的插件，提供了大量的功能，其中大部分在任何网站上都可以很好地工作。

[GitHub](https://github.com/alfajango/jquery-dynatable)

## 引导表

CSS 框架仍然很受欢迎，Bootstrap 就在那里。Bootstrap Table 插件是一个功能丰富的轻量级即插即用表格插件，它以最小的开发工作量提供了您需要的所有功能。

这个插件的能力和范围令人印象深刻。您可以使用它来重新设计表格的样式，使它们更具响应性和美观，此外还可以添加新的功能，如过滤、搜索、有条件地显示列(例如，为了简单起见，您可以隐藏列)以及其他有用的功能，如导出到 CSV 和 JSON。

![An example of Bootstrap table in action](img/337a25105ccc3782484b997fe48f39f4.png)

[示例页面概述了如何配置这些元素](http://bootstrap-table.wenzhixin.net.cn/examples/)。您将不得不花一些时间来了解文档，但是当结果是一个像这些一样打包的功能强大的表时，这是非常值得的。

您要么需要运行一个引导项目，要么可选地将引导 CSS 和 JS 文件添加到您的项目中。该插件通过表格上的标准 HTMTL `data`属性(概述您想要的功能/选项)或者通过更传统的 JS 方法工作。如果你已经在一个引导项目中，这是一个很好的开始。如果你在做 Bootstrap 之外的东西，你需要权衡利弊。

这个插件已经被几十个贡献者和几千个提交者广泛维护了多年。这是一个持续发展的开源项目。很难说这是否会扩展到 Bootstrap 4 中(因为它需要 Bootstrap 2/3 ),但就目前而言，它将继续保持下去。

如果你正在寻找一个历史上有很大支持并不断迭代的插件(你可以假设他们已经解决了大部分错误)，那么这个插件可能适合你。你也可以在这里看到开发者为它制作的有趣的主题和模板。

[网站](https://bootstrap-table.com/)
[试玩](https://examples.bootstrap-table.com/)

## 固定标题表

固定标题表格的功能与您想象的完全一样:它在表格的顶部显示一个固定标题，这样当您滚动时就不会丢失列的轨迹。

![Fixed Header jQuery tables plugin example table](img/b04cb96377ea5caba9386753f525587e.png)

这个插件可以处理标准的 HTML 表格标记。它将使用您的`<thead>`或者可选地使用您的`<tfoot>`标签为您的表格创建一个固定的标题。还有一些其他的选项，比如可以将页眉复制到页脚，还可以显示/隐藏表格。总的来说，这个插件的目的很简单，用很少的元素来最小化复杂性。如果需要，样式和功能将需要手动添加。

如果您在寻找一个提供额外功能的解决方案，那么其他库可能更合适。另外，请注意这是一个较老的插件(从 2011 年开始)，所以可以肯定开发者不会主动更新或扩展这个插件。这取决于你是否喜欢使用一个遗留插件。

[网站/演示](http://www.fixedheadertable.com/#demo)

## 数据表

这个高度灵活的 jQuery 表格插件旨在扩展基本的 HTML 表格，使它们更加直观和有用。

数据表是一个广泛的插件，用于为你的表格提供额外的功能，如排序，过滤，分页和自定义主题。

![jQuery DataTables example image with search, pagination and number of entries](img/20acbc3e5c6fd05c0f15f9320f5ecb67.png)

正如你所期望的那样，该插件开箱即用，但它也提供了[全面的文档](https://datatables.net/manual/)，因此你可以控制你的表格的外观、感觉和操作。

广泛的特性和定制使它成为一个非常面向开发人员的扩展。这个插件不仅提供了几个选项，而且在他们的网站上有可靠的文档和强大的论坛系统支持。

与 CSS 框架集成以改变插件样式的能力令人印象深刻。它兼容 Bootstrap、Foundation、jQuery UI 等。其他方面，如通过 AJAX 访问事件、API 系统和动态数据，使数据表成为可靠的选择。

插件的另一个方面是他们提供额外的支持。大多数插件会通过 GitHub 错误报告或直接联系开发者来获得某种类型的支持，但这些人通过他们的论坛提供支持，你可以通过购买他们的许可证来访问他们的论坛。虽然有些人可能会觉得付费支持的想法令人不快，但从论坛来看，大多数问题都得到了相对快速的解决。

关于支持的话题，如果您对将数据表集成到企业系统或任务关键型系统感兴趣，您可以购买企业许可证，从开发人员那里获得直接支持并访问请求功能。

总的来说，这是一个有趣的插件，它提供了非常以开发者为中心的特性，如果你需要的话，还提供了可选的支持和帮助元素。

[网站/演示](https://datatables.net/)

## jQuery 高图表表

HighchartTable 是一个有用的插件，它通过从你的表格数据中动态创建一个[highcarts](http://www.highcharts.com/)交互式图表来工作。

![Simple table element with an extracted Highchart](img/934af457369336f8654e2cb8269827df.png)

这个插件在你的表格元素上放置了一系列的`data`属性。它利用 Highcharts 将您的表数据呈现为动态/交互式图表。

它的网站[展示了所有的选项](http://highcharttable.org/#documentation)，为每个选项列出了一个简单的 JSFiddle，这样你就可以看到它是如何工作的。您可以控制图形的类型、颜色、标签和其他交互式元素，以满足您的需求。

需要考虑的一点是，这个插件实际上并不做图表转换。这是由 Highcharts 处理的，这意味着您还需要包含他们的 JS 和 CSS 文件才能让它工作。此外，虽然这个插件有一些活动/开发，但它的开发者似乎并没有在 GitHub 上过于活跃，这意味着如果 Highcharts 有新的更新或功能出现，他们是否更新这个插件来支持它将取决于他们。

另一个要考虑的因素是 Highcharts 不能免费用于商业用途。要合法使用它，你需要一个许可证(如果你是为客户开发，你就属于这一类)。公平地说，Highcharts 相当花哨，提供了大量选项，所以如果你想从中获利，付费使用这个库是合理的。

[网站](http://highcharttable.org/)
[试玩](http://highcharttable.org/#demo)
[GitHub](https://github.com/highchartTable/jquery-highchartTable-plugin)

## 制表机

制表器是一个 jQuery 表格插件，用于从 HTML、JavaScript 数组、Ajax 数据源或 JSON 数据动态生成交互式、可扩展的表格。制表器具有广泛的功能，如过滤，排序，可调列宽，智能加载表格数据，分页和直接编辑表格输入。

![Showcases how Tabulator can be used to create interactive tables](img/6053bc27f8c86c7cf8105e3c76893b58.png)

由于其深入的文档、各种各样的特性和整体上的灵活性，制表器在这个列表中。它的网站概述了它可以提供的所有功能，带有易于理解的文档，与多个内联示例一起工作。尽管文档很长并且很复杂，但是它提供了你需要知道的关于启动和运行插件的一切。

插件的可扩展性意味着你可以按照你想要的方式定制你的表格。您可能希望将表中的行组合在一个标题下，创建简单的工具提示来解释悬停时的数据，对行内容进行分页，或者利用其他几个强大的功能来帮助改善用户体验。对于开发人员来说，这是一个很好的工具，我以前也用过几次。

制表器的开发者在 GitHub 上相当活跃，每几个月更新一次，一年中有大量的提交和 bug 修复。该插件的 2.7 版本于 2016 年 10 月发布，可以肯定的是，该插件本身将会随着时间的推移而不断发展。当你选择你的表格插件时，这可能是一个重要的因素，因为有时你可能需要支持或者遇到错误。

[网站](http://olifolkerd.github.io/tabulator/)
[演示](http://olifolkerd.github.io/tabulator/examples/)
[GitHub](https://github.com/olifolkerd/tabulator)

## FancyGrid

FancyGrid 为您的标准 jQuery 表提供了额外的功能和特性。无论您是想要一个简单的、样式简单的表格，还是一个具有过滤、排序或分页功能的表格，FancyGrid 都可以为您的用户创建一个良好的表格体验。

![A sample twin grid image showcasing FancyGrid](img/84c1da976d859ecb177300888abb6331.png)

根据你想如何使用这个插件，有几种方法可以使用花式网格。您可以使用它来设计表格的样式，提供新的功能，或者将它与 Highcharts 集成以提供数据可视化。

FancyGrid 的文档概述了您可以定制的各种表格功能，包括分页、分组、定制事件、数据验证等等。这里有一大堆文档，足以让您不知所措，但是它们提供了几个快速示例，可以让您快速上手。它的默认设置很好。

需要考虑的一件有趣的事情是，这个插件没有开放的 GitHub repo。你可以从他们的网站上免费下载供个人使用，但用于商业用途，你需要购买许可证才能使用。Highcharts(用于 2D 制图)的使用也需要许可，因此您需要考虑是否愿意为您的项目购买这些元素。

另外几个有趣的地方:我发现加载他们的网站时好时坏(有时会加载失败或者需要一段时间才能生成示例)。这很可能只是一个孤立的问题，但值得注意的是(没有人喜欢等待文档/示例加载的时间)。此外，移动支持时好时坏，没有记录。很难说这是因为这个插件从来就不是为手机设计的，还是因为文档不是最新的。如果你正在寻找一个无缝的，令人敬畏的支持移动的桌面体验，那么其他插件可能更合适。

然而，总的来说，FancyGrid 是一个相当全面的插件，你可以为你的网站提供广泛的定制选项。

[网站/演示](http://fancygrid.com/)

## jQuery KingTable

KingTable 是一个复杂的、以开发人员为中心的表格解决方案，您可以利用它来创建复杂的搜索/过滤条件，非常适合于帐户、项目管理或其他以数据为中心的活动。

![KingTable jQuery table plugin example](img/5658a5fa120e6b982817c84a1cec020d.png)

与我们的一些其他插件不同，KingTable 完全记录在 GitHub 上，[有一个专门的 Wiki 页面](https://github.com/RobertoPrevato/jQuery-KingTable/wiki/)，概述了该插件的所有各种设置和用例。

就功能而言，该插件附带了主题化支持(但总体目标是根据您自己的需要对其进行定制)，以及几个过滤器和事件，您可以将它们挂钩以使您的数据管理更容易。坦率地说，这个插件感觉非常像一个非常专注的开发者插件，假设你对[创建自己的定制过滤器和数据机制](https://github.com/RobertoPrevato/jQuery-KingTable/wiki/Implementing-custom-filters)感到很舒服。如果你正在寻找一个提供基本设置的插件，那么其他插件可能更合适。但是，如果您想要对表的操作方式进行强有力的控制，那么这是一个很好的解决方案。

移动响应能力是另一个值得讨论的因素。这个插件不会为移动设备做任何特别的事情，所以你的表格会像平常一样响应(比如在小设备上横向滚动或者挤压列)。你可以自己调整，但是插件不会帮你处理。在一个类似的话题上，KingTable 为传统键盘用户提供了出色的桌面支持，包括标签支持、通过左右(或 A/D)键的分页控制以及其他小的改进。

KingTable 已经开发了一段时间，一年中有几次提交和更新。它可能比这里提到的其他一些插件要小，但它仍然是一个健壮而有用的插件，你可能会在你即将到来的项目中发现它的用处。

[网站/GitHub](https://github.com/RobertoPrevato/jQuery-KingTable)

[文档(维基)](https://github.com/RobertoPrevato/jQuery-KingTable/wiki/Themes)

## Stackable.js

Stackable.js 是一个以移动为中心的 jQuery 插件，旨在通过为较小的设备调整表格的布局来使表格移动友好。

![Side by side comparison of desktop and mobile jQuery Stackable](img/144889bd21597b870c5b4cd400e1400c.png)

与其他添加额外功能的插件不同，stackable.js 只是用来确保在移动设备上您的表格元素是有用的。为了简单起见，它将每个行条目一个接一个地堆叠起来，并并排列出键/值元素。

选项很少，文档也很有限，但它的全部目的是将标准表元素转换成更便于移动的元素。

开发者在过去的一年里一直在维护和调整这个插件，所以它看起来不像一个遗留的插件。然而，由于它是一个相对小/基本的插件，我不期望有太多的功能变化即将到来。如果你正在寻找一个复杂的，无所不包的插件来给你额外的功能，你最好试试我们列表上的其他几个。

[GitHub](https://github.com/johnpolacek/stacktable.js/)
[网站/演示](http://johnpolacek.github.io/stacktable.js/)

## jquery 可重用

这个插件使你的表格更加移动响应，将每一行条目转换成一系列的`li`项来改善布局和可滚动性。

![Comparison image of jQuery ReStable on desktop and mobile](img/b0432a56ebc2f13d481c89a5c130681b.png)

很像 stackable.js，这个插件的目的是让你的桌子更加移动友好。该插件的工作原理是在小屏幕上查看时调整每行的布局。它将每行的列一个接一个地堆叠起来，使它们易于阅读。

插件本身只迎合移动调整:它不添加任何功能或有任何真正的选项来配置；这是一个简单的拖放式插件。只要您的 HTML 标记正确，并添加一些基本的 jQuery 来启动一切，一切都会迎刃而解。

ReStable 是一个相当小的插件，因此它在 GitHub 上有一些提交。这不是一个社区/团队项目，这本身是好的，但如果你需要帮助，你可能需要考虑这个因素。像大多数较小的项目一样，您应该“照原样”使用它，并抱最好的希望。

[网站/演示](https://codeb.it/restable/)
[GitHub](https://github.com/micc83/ReStable)

## 可行走的

FooTable 是一个受 Boostrap 启发的表格扩展插件，提供搜索、过滤和其他功能来增强你的表格。

![A simple jQuery FooTable](img/99ea9f51c7972e69fa39cbb49ecf6d9d.png)

FooTable 可能看起来像我们前面看到的 Boostrap 表，但 FooTable 的不同之处在于，它在有 Boostrap 和没有 Boostrap 的情况下都可以工作，这意味着不需要包含任何引导文件来启动和运行。

该插件支持过滤、分页、排序和内嵌编辑。所有这些功能一起使用时，您会得到一个很棒的表格，您可以将它用作前端表格编辑解决方案。

该网站概述了你可以使用的各种设置，但是 [FooTable 专用文档](http://fooplugins.github.io/FooTable/docs/jsdocs/index.html)是你可以找到你需要的大部分内容的地方。坦率地说，文档非常关注开发人员，并假设您对事件、设置和其他深入的元素感到满意。如果你正在寻找一个简单的插件，既可以作为一个简单的开箱即用的激活，也可以定制，这是一个很好的选择。

FooTable 是一个长期的标准项目，有几十个参与者提交了几百个项目。存在持续的活动和改进。如果你正在寻找一个经过测试、迭代和改进的插件(能够利用一个有经验的贡献者团队)，那么这个插件可能非常适合你。这个插件的第 3 版进行了全面的修改，所以你可能会发现一些偶然的错误/问题，但是就我所见，它几乎不需要什么配置就可以开箱即用。

[网站/演示](http://fooplugins.github.io/FooTable/index.html)
[GitHub](https://github.com/fooplugins/FooTable)

## tabella . js 表格

Tabella 是一个动态表格生成插件，主要提供触摸/滑动功能，以轻松滚动长/复杂的表格。

![An example of Tabella.js in use](img/f25250be11e0470fadd900b4c5b390b2.png)

与其他插件不同，您使用 Tabella 来生成您的表格。选择要用作表格容器的元素，然后将数据作为对象提供(概述标题和列值)。如果这个插件可以获取原始 JSON 数据或者在一个现有的表中传递以便抓取和呈现，那就太好了，但是总的来说它工作得很好，给了你一个移动友好的表。

如果你的主要目标是让你的长表格有响应性，那么这个插件提供了一个视觉上简单，但是交互的解决方案。

自 2015 年以来，Github 上的插件 repo 一直没有任何动作，但考虑到它的基本功能集，这完全没问题。如果你需要定制它或者需要支持，你可能不得不自己保护自己，但是这是小插件所期望的。

如果你想要一些花哨的东西来确保你的桌子在手机上工作，这绝对值得一看。

[GitHub](https://github.com/iliketomatoes/tabellajs)
[网站/演示](http://iliketomatoes.github.io/tabellajs/)

## 包扎

这些只是您可以在项目中使用的一些插件，用于扩展和改进表格的功能和外观。无论您是对简单的样式感兴趣，还是需要额外的功能，比如搜索、过滤、列排序、Ajax/JSON 加载，都有一个 jQuery 插件可以让您的生活更加轻松，并改善用户体验。

你还使用其他基于表格的插件吗？如果你使用了上面没有列出的很棒的表格插件，我们很乐意收到你的来信。

最后，如果你想提高你的 JavaScript 技能，看看我们的 [JavaScript 书籍库](https://www.sitepoint.com/premium/topics/all?q=&limit=400&offset=0&page=1&content_types%5B%5D=Book&slugs%5B%5D=javascript&states%5B%5D=available&order=)！

## 分享这篇文章