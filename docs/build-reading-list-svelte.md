# 建立一个苗条的阅读清单

> 原文：<https://www.sitepoint.com/build-reading-list-svelte/>

在过去的两年里，Svelte 受到了很多赞扬，它远非“仅仅是另一个前端框架”。它在 2019 年的 JS 调查中获得了“年度突破”，随后在 2020 年的满意度排名中名列第一。在 Stack Overflow 2021 调查中，它还被评为最受欢迎的 web 框架。

Svelte 以其较小的包大小、非常好的性能和易用性的组合吸引了开发人员。同时，它也包含了许多好东西。已经提供了一个简单的状态管理解决方案，以及随时可用的过渡和动画。这篇介绍性教程将阐明 Svelte 是如何做到这一点的。本系列的后续教程将更详细地介绍如何使用 Svelte 提供的各种可能性来实现 Svelte 应用程序。

## 苗条的背景故事

但首先，一个关于苗条身材的小背景故事。虽然它在 21 世纪 20 年代初才进入主流，但它已经存在很长时间了。

对 GitHub 的第一次承诺是在 2016 年末。它的创建者是 Rich Harris，一个开源向导，他最突出的另一项发明是 Rollup，一个现代捆绑器。里奇·哈里斯当时在新闻杂志《卫报》担任图形编辑。他的日常工作是为网站创建交互式可视化，他希望有一个工具，让他能够轻松地编写这些内容，而不会影响包的大小或速度。与此同时，他想要一些可接近的东西，以便其他不太懂技术的同事能够快速创建可视化效果。

出于这些需求，Svelte 诞生了。从新闻室开始，Svelte 很快在开源社区中聚集了一小群追随者。但直到 2019 年 4 月，斯维尔特才真正被世人所知。这一天标志着版本 3 的发布，这是一个完全重写的版本，重点是开发人员的体验和可接近性。从那以后，Svelte 的人气上升了很多，更多的维护人员加入了这个团队，Rich Harris 甚至加入了 Vercel 全职从事 Svelte 的工作。

关于 Svelte 的深入教程，以及它与 React 和 Vue 的区别，请查看 [Svelte 3:一个基于激进编译器的 JavaScript 框架](https://www.sitepoint.com/svelte-javascript-framework-introduction/)。

## 建立一个简单的书单

让我们尽情享受吧！我们将建立一个小书单，允许我们在阅读列表中添加和删除书籍。最终结果将类似下图。

![Final App](img/a72f18ed51cc712bd22abbaa15e1d471.png)

我们将从从项目模板搭建我们的项目开始。我们将使用官方的[苗条模板](https://github.com/sveltejs/template/)。备选方案是使用 [Vite 驱动的模板](https://github.com/vitejs/vite/tree/main/packages/create-vite/template-svelte)或使用 [SvelteKit](https://kit.svelte.dev/) ，一个基于 Svelte 的框架，用于构建带有内置路由的成熟应用——但在本教程中，我们将尽可能保持它的准系统。

下载完模板后，切换到它的文件夹并运行`npm install`，这将下载我们需要的所有包。然后我们将切换到`App.svelte`，在这里我们将用一个纯 HTML 版本替换内容，以展示我们想要的视觉效果:

```
<h4>Add Book</h4>
<input type="text" />
<h4>My Books</h4>
<ul>
  <li>A book</li>
</ul> 
```

我们可以直接在 Svelte 文件的顶层写上面的代码；我们不需要添加任何包装元素。Svelte 的语法是 HTML 的超集，所以在 HTML 文件中有效的东西在 Svelte 文件中也有效。

现在的问题是如何将动态部件放入其中。我们将从向脚本添加一个静态列表开始，并通过一个循环来呈现它:

```
<script>
  let books = ['Learning Svelte', 'The Zen of Cooking Tea'];
</script>

<label>
  <h4>Add Book</h4>
  <input type="text" />
</label>
<h4>My Books</h4>
<ul>
  {#each books as book}
    <li>{book}</li>
  {/each}
</ul> 
```

我们添加了一个`script`标签，将与组件相关的 JavaScript 逻辑放入其中。每次安装组件时都会执行该逻辑。我们还用特殊的简单语法来增强 HTML，以创建一个循环并打印每本书的标题。如您所见，Svelte 对控制流块有独特的语法，不像 Vue 或 Angular，它们以特殊属性的形式添加这样的功能。这使得代码可读性更好，因为您可以更容易地发现它。如果希望在控制流块中包含多个顶级项，也不必创建包装元素。

用花括号将变量括起来，输出书名。一般来说，每当你在模板中遇到一个花括号，你就知道你在输入一些苗条相关的东西。我们将在本系列教程的第 2 部分更详细地研究模板语法。

## 对用户输入做出反应

我们现在可以呈现由我们的`books`变量定义的任意书名列表。增加一本新书怎么样？为此，我们需要增强`<script>`标签中的逻辑，并将其连接到`<input>`元素:

```
<script>
  let books = ['Learning Svelte', 'The Zen of Cooking Tea'];
  let newBook = '';

  function addBook(evt) {
    if (evt.key === 'Enter') {
      books = [...books, newBook];
      newBook = '';
    }
  }
</script>

<label>
  <h4>Add Book</h4>
  <input type="text" bind:value={newBook} on:keydown={addBook} />
</label>
<h4>My Books</h4>
<ul>
  {#each books as book}
    <li>{book}</li>
  {/each}
</ul> 
```

我们添加了一个名为`newBook`的新变量，它应该反映输入值。为此，我们通过编写`bind:value={newBook}`将其绑定到`<input>`。这建立了一个双向绑定，所以每次用户在`<input>`中输入文本时，`newBook`都会更新，如果在`<script>`标签中`newBook`被更新，`<input>`的显示值也会改变。我们可以用简单的动态属性做同样的事情，但是这种方式为我们节省了一些代码——这是一种你在苗条身材中经常遇到的思维模式。

当用户按下`enter`时，我们希望将新的书名添加到列表中。为此，我们添加了一个 DOM 事件监听器。为了告诉 Svelte 链接到事件，我们只需在`on`和事件名称的其余部分之间添加一个冒号——所以在这种情况下是`on:keydown`。之后，我们用花括号把函数名放在里面。每次事件触发时都会调用该函数。关于这个模板语法的更多信息可以在本教程系列的第 2 部分中找到。

在这种情况下要调用的函数是`addBook`，其中我们检查键盘事件，如果用户确实按下了`enter`，我们就更新`books`变量。注意缺少像 Angular 或 Vue 2 中的`this`上下文，或者像 Vue 3 中缺少特殊值对象，或者 React 中缺少`setState`。在这种情况下，Svelte 不需要额外的语法来知道变量已经更新。这可能感觉像魔术，但同时也像“只是简单的 JavaScript”。

为了理解 Svelte 如何做到这一点，我们需要看看引擎盖下。Svelte 实际上用一个`.svelte`文件做什么，它什么时候处理它？答案: *Svelte 其实是一个编译器！*在你的代码被载入浏览器之前，它已经完成了大部分工作。Svelte 解析代码并将其转换为常规 JavaScript。在解析过程中，可以看到模板中使用了像`newBook`这样的变量，所以对它的赋值会导致重新呈现。因此，编译输出将通过调用一个`$$invalidate`函数来包装这些赋值，该函数将为下一次浏览器绘制安排这个确切组件的重新呈现。这是 Svelte 出色性能的秘密:它预先知道哪些部分可以触发重新渲染，然后只需要在这些确切的地方工作，外科手术式地更新 DOM。这也是为什么 Svelte 应用程序的包大小如此之小的原因:所有不需要的东西都不会成为输出的一部分，所以 Svelte 可以省略掉它微小的运行时中不需要的每一部分。一个苗条的 Hello World！app 的捆绑包大小只有 2.5KB！

唯一需要注意的是，Svelte 只寻找任务。这就是为什么我们需要做`books = [...books, newBook];`或者`books.push(newBook); books = books;`。否则，苗条也不会知道`books`已经更新了。

![Compilation Output](img/848060ec2dc2b7ef7a2210696483213b.png)

## 收尾

我们做到了！我们现在可以查看并添加书籍到我们的列表！不过，它看起来并不漂亮，所以让我们对我们的 UI 做一些收尾工作。首先，我们将添加一些 CSS 来样式化我们的元素:

```
<!-- script and html code... -->

<style>
  input {
    padding: 5px 10px;
  }
  li {
    list-style: none;
  }
  ul {
    padding: 5px 0;
  }
</style> 
```

正如你所看到的，我们只是在我们的`.svelte`文件中添加了一个`<style>`标签，并继续在其中写入常规的 CSS。如果你担心上面的代码会影响整个应用程序中所有的`<input>`、`<li>`或`<ul>`标签，请相信它不会。默认情况下，瘦作用域样式，所以它们只适用于定义它们的组件。如果你想定义全局的东西，用`:global`函数包装选择器。例如，如果您想设计应用程序中所有的`<input>`的样式，代码应该是`:global(input) { padding: 5px 10px; }`。

现在造型更好了。让我们以一个更好的 UX 的过渡来结束:我们希望新的列表元素淡入。要做到这一点，我们只需要找到 Svelte 的内置过渡和动画并应用它们:

```
<script>
  import { fade } from 'svelte/transition';
  // ..
</script>

<!-- input ... -->
<h4>My Books</h4>
<ul>
  {#each books as book}
    <li transition:fade>{book}</li>
  {/each}
</ul>

<!-- styling ... --> 
```

就是这样！只需导入一个内置的过渡，并通过向元素添加`transition:fade`来应用它，我们就可以得到平滑的淡入过渡。我们的迷你应用程序现在已经完成。这还没有包含顶栏和背景渐变，但是现在你也应该很容易添加这个了。这是最终结果:

```
<script>
  import { fade } from 'svelte/transition';

  let books = ['Learning Svelte', 'The Zen of Cooking Tea'];
  let newBook = '';

  function addBook(evt) {
    if (evt.key === 'Enter') {
      books = [...books, newBook];
      newBook = '';
    }
  }
</script>

<label>
  <h4>Add Book</h4>
  <input type="text" bind:value={newBook} on:keydown={addBook} />
</label>
<h4>My Books</h4>
<ul>
  {#each books as book}
    <li transition:fade>{book}</li>
  {/each}
</ul>

<style>
  input {
    padding: 5px 10px;
  }
  li {
    list-style: none;
  }
  ul {
    padding: 5px 0;
  }
</style> 
```

## 架构考虑

我们已经看到了如何用 32 行代码编写一个短小的应用程序。当然，我们只是触及了表面。一个成熟的应用程序需要某种状态管理、多个组件以及将这些组件相互集成的方法。

例如，将一个待办事项的显示拆分到一个单独的组件中是有意义的，因为我们将添加一些功能，如就地编辑名称或将其标记为完成。随着时间的推移，将所有这些都放在一个组件中会变得难以维护。幸运的是，使用其他组件就像从另一个瘦文件默认导入一样简单，并以类似于我们已经看到的常规 DOM 元素的方式与之交互。我们将在本系列的第 5 部分更详细地研究组件交互。

另一个例子是待办事项的管理。现在，它们是在组件内部处理的，与后端没有任何联系。如果我们要添加 API 调用，我们会将 UI 逻辑与后端交互混合在一起，为了更好地分离关注点，后端交互通常在组件之外处理得更好。为此，我们可以使用苗条商店，这将在第 4 部分中讨论。

如您所见，Svelte 为我们所有的需求提供了解决方案，我们将在本系列的课程中探讨这些解决方案。

## 准备，设置…苗条？

那么，在你的下一个项目中使用 Svelte 安全吗？你的经理可能会问，未来几年 Svelte 是否还会存在，还是会像以前的 frontend framework 明星一样被淘汰。没有一家大公司像 Angular 和 React 那样支持 Svelte 的整个开发，但 Vue 已经表明这不是问题。此外，如开头所述，Svelte 的创造者 Rich Harris 现在正全职致力于此。随着 Svelte 的受欢迎程度不断上升，在未来几年内没有任何迹象表明它会走向任何地方。

选择框架的另一个方面是生态系统及其工具。与 React 相比，生态系统仍然很小，但每天都有新的库出现，并且已经有少数非常好的组件库。同时，由于 Svelte 非常接近普通的 HTML 和 JavaScript，所以可以非常容易地将任何现有的常规 HTML/JavaScript 库集成到您的代码库中，而不需要包装器库。

关于工具，苗条看起来相当不错。有一个积极维护的官方 VS 代码扩展，以及一个底层语言服务器，可以被许多其他 ide 用来集成智能感知。IntelliJ 也有一个用于苗条身材的插件，并且最近聘请了它背后的创造者在 JetBrains 工作。也有各种工具集成 Svelte 与各种 bundlers。是的，你也可以把 TypeScript 和 Svelte 一起使用。

如果你正在寻找建立一个成熟的网站或网络应用程序，你可能也有兴趣看看 [SvelteKit](https://kit.svelte.dev/) (见我们的[SvelteKit](https://www.sitepoint.com/a-beginners-guide-to-sveltekit/)初学者指南)。它提供了一流的开发体验，并带有灵活的基于文件系统的路由器。它还使您能够部署到许多不同的平台，如 Vercel、Netlify、您自己的节点服务器，或者只是一个很好的旧静态文件服务器，这取决于您的应用程序的特性和需求。

### 关于苗条身材的快速事实

简而言之，以下是关于苗条身材需要记住的要点:

*   它有一名专职维护人员
*   它有很好的工具
*   它的特点是稳定的
*   它的生态系统正在成长
*   SvelteKit 可用于快速构建应用程序

## 后续步骤

Svelte 绝对可以用于您的下一个项目！

这是 SitePoint Premium 上 [6 部分系列](https://www.sitepoint.com/premium/books/svelte-a-beginner-s-guide/)的第一部分。在第 2 部分中，我们将仔细看看模板语法。在第 3 部分中，我们将看看反应式语句，以及它们如何帮助我们对变量变化做出反应或导出计算变量。第 4 部分将讨论存储，它将帮助我们处理细长文件之外和之间的逻辑，我们也可以用它来进行状态管理。第 5 部分着眼于各种组件交互概念。最后，在第 6 部分，我们将研究测试苗条的应用程序。

这个系列也可以在亚马逊这里买到:[苗条:初学者指南](https://www.amazon.com/Svelte-Beginners-Guide-Simon-Holthausen-ebook/dp/B09SBYHRQ8)。

我们希望能激发你对苗条身材的兴趣！

## 分享这篇文章