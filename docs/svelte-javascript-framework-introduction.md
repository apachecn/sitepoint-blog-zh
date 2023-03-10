# Svelte 3:一个激进的基于编译器的 JavaScript 框架

> 原文：<https://www.sitepoint.com/svelte-javascript-framework-introduction/>

在本文中，我们将探索 [Svelte 3](https://svelte.dev/) ，这是一个前端 JavaScript 框架，采用了一种与框架略有不同的方法。像 React 这样的框架提供了一大堆 JavaScript，而 Svelte 应用程序是由 Svelte 编译器编译成 JavaScript 的，据称比 React 代码要小得多。而且因为代码是通过 Svelte 编译器运行的，所以也可以优化。

Svelte 还采用了一种非常不同的方法来管理数据，并对 DOM 进行了外科手术式的更新——看不到任何挂钩——而且使用起来非常有趣。即使你是 React 或任何其他流行框架的忠实粉丝，Svelte 也值得一试。在这篇简介中，我们将构建一个小的示例应用程序来体验一下 Svelte 能提供什么。我们开始吧！

## 苗条身材入门

在本教程中，我们不会过多地深入捆绑和苗条应用程序的基础设施，所以我们将按照苗条教程来启动和运行应用程序。

我们需要在本地安装[节点](https://www.sitepoint.com/quick-tip-multiple-versions-node-nvm/)和 [Git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git) 。然后我们可以运行:

```
npx degit sveltejs/template github-repository-searcher 
```

这将把 Svelte 模板存储库克隆到`github-repository-searcher`文件夹中(我们正在构建的应用程序将使用 GitHub API 来搜索存储库),并为我们设置所有工具。如果你正专注于学习 Svelte，我强烈推荐这种方法:它会让你一头扎进框架，而不会陷入构建配置中。

一旦上面的命令完成，您可以通过`cd github-repository-searcher`切换到那个目录，然后运行`npm install`来安装所有的依赖项。完成后，`npm run dev`将启动并运行应用程序，使用 Rollup bundler 构建应用程序。访问 [http://localhost:5000](http://localhost:5000) 应该会呈现给你一个纤细的 Hello World 页面，现在我们可以开始构建了！

## 构建一个苗条的组件

在我们开始构建更苗条的组件之前，让我们看看模板附带的现有组件。首先要注意的是，细长的组件是在一个`.svelte`文件中定义的。`App.svelte`(位于`src`文件夹中)分为三部分:

```
<script> export let name; </script>

<style> /* CSS removed to save space */ </style>

<main>
  <h1>Hello {name}!</h1>
  <p>
    Visit the
    <a href="https://svelte.dev/tutorial">Svelte tutorial</a>
    to learn how to build Svelte apps.
  </p>
</main> 
```

如果你的编辑能够理解这些纤细的文件，并且能够正确地突出显示它们，那么处理它们将会容易得多。Svelte 提供了一个 VS 代码扩展，这是我使用的，但如果你使用不同的编辑器，我建议你在谷歌上搜索。Svelte 有一个相当大的社区，所以很可能大多数流行的编辑器都有插件。

细长组件分为三个部分:

1.  `script`标签是编写组件的所有 JavaScript 的地方。
2.  标签是定义所有组件 CSS 的地方。在一个苗条的组件中，默认情况下所有的 CSS 都作用于该组件，所以这里的任何样式*只适用于该组件，而不是全局*。
3.  组件中提供的任何其他内容都被视为组件输出的 HTML。Svelte 还提供模板逻辑来支持条件呈现、数组循环等等。

要运行我们的应用程序，请使用`npm run dev`。这将运行我们的 run Rollup，以及一个小型 HTTP 服务器，它将在端口 5000 上为我们的应用程序提供服务。

## 要求用户输入 GitHub 用户名

我们的应用程序的第一步是要求用户提供一个 GitHub 用户名。然后，我们将使用这个名称在 GitHub 中搜索用户拥有的存储库列表。让我们更新`App.svelte`来做到这一点。

首先，在`script`块中，删除`export let name`行。这就是我们在《苗条》中定义道具的方式，就像《反应》中的道具一样。这里的`export`关键字声明这个值是一个将由组件的父代提供的属性。但是在我们的例子中，我们的组件没有任何属性，所以我们可以删除它。你还需要更新`src/main.js`来删除`props: {...}`代码，因为我们的`App`组件不需要任何道具。完成后，`main.js`应该是这样的:

```
import App from './App.svelte';

const app = new App({
  target: document.body,
});

export default app; 
```

这个文件实际上包含了应用程序的入口点。如果你熟悉 React 的话，可以把它想象成等于`ReactDOM.render`。

让我们用我们想要的 HTML 更新`App.svelte`。我们将创建一个简单的表单，要求用户输入用户名:

```
<script>  </script>

<style> main {
    width: 80%;
    max-width: 800px;
    margin: 20px auto;
    padding: 20px;
  }

  label {
    font-weight: bold;
  }

  input {
    width: 80%;
  } </style>

<main>
  <form>
    <label for="username">Enter a GitHub username:</label>
    <input type="text" name="username" placeholder="jackfranklin" />
    <button type="submit">Load repositories</button>
  </form>
</main> 
```

本教程我们不会重点讨论 CSS(我不是设计师！)，但是我已经应用了少量的 CSS 来使事情看起来更好一些。现在我们有了表单，让我们看看如何将它与 Svelte 连接起来。首先要注意的是，没有显式的`useState`钩子或类似的东西；Svelte 采用的方法更接近于 Vue 或 Angular 等其他框架，在这些框架中，您将输入绑定到一个值。这是 Svelte 的一个常见主题，不应该感到惊讶，因为它的一个明确目标是[允许开发人员编写更少的代码](https://svelte.dev/blog/write-less-code)。

让我们为输入声明一个变量:

```
let usernameInputField = ''; 
```

然后在模板中使用 Svelte 的`bind:value`指令:

```
<input type="text" name="username" placeholder="jackfranklin" bind:value={usernameInputField}> 
```

Svelte 将为我们完成剩下的工作:当用户输入时，变量`usernameInputField`将被更新并保持同步。

一旦用户输入了用户名，我们需要监听他们提交表单的时间。Svelte 使用以下语法绑定事件侦听器:

```
<form on:submit={onSubmit}> 
```

这将在用户提交表单时调用`onSubmit`函数。不过，Svelte 还有一个锦囊妙计，那就是[事件修改器](https://svelte.dev/tutorial/event-modifiers):

```
<form on:submit|preventDefault={onSubmit}> 
```

现在，当 Svelte 在这个表单上看到一个提交事件时，它会自动为我们调用`event.preventDefault()`。我喜欢这一点:我们少了一件要担心的事情，多了一件可以交给框架的事情。

回到我们的`script`标签，我们可以定义这个`onSubmit`函数。它将获取用户名并调用 GitHub API 来获取存储库列表(它将返回前 30 个存储库，因此如果您想要获取所有存储库，您需要对它们进行分页，但是我们现在将把它留在这里):

```
async function onSubmit() {
  const url = `https://api.github.com/users/${usernameInputField}/repos`;

  const response = await fetch(url);
  const repositories = await response.json();
  console.log('loaded repositories', repositories)
} 
```

一旦我们有了这些存储库，我们希望在页面上列出它们，并允许用户搜索给定的存储库。让我们创建一个名为`Search.svelte`的新组件，而不是在`App.svelte`组件中完成所有这些。它将获取存储库列表，并为用户提供一个输入来搜索他们想要的存储库。

在现有目录中创建一个名为`Search.svelte`的新文件。我喜欢用少量的样板文件开始我的组件，只是为了检查我已经设置好了一切:

```
<script>  </script>

<style>  </style>

<p>Search component</p> 
```

然后，当我在页面上呈现这个组件时，我将能够判断它是否正确呈现。

## 在细长组件和条件渲染之间传递道具

搜索组件将获取的存储库列表作为一个属性。为了声明一个组件接受一个属性，我们声明一个导出的变量。在`Search.svelte`中，将该行添加到组件的`<script>`部分:

```
export let repositories; 
```

如果你想设置一个默认值，你也可以把它初始化为一个值，以防父节点不把它传入。这可能看起来有点奇怪，而且确实需要一点时间来适应，因为您并不是在传统的 es 模块意义上真正地导出变量，而是更多地声明您希望您的父组件传入一些存储库。

我们希望呈现新的`Search.svelte`组件，但是只有当用户提交了表单并且我们已经获取了存储库时。Svelte 的模板化支持条件渲染，形式为 [`#if blocks`](https://svelte.dev/tutorial/if-blocks) 。如果你是 React 用户，这可能需要一些时间来适应，因为你不像在 JSX 那样使用常规的 JS 条件，而是使用简单的模板语言。

我们希望有条件呈现的任何 HTML 都可以放在一个`#if`块中:

```
{#if someCondition}
  <p>someCondition is true!</p>
{/if} 
```

我们可以在`App.svelte`中创建一个默认为`undefined`的`repositories`变量，然后当我们加载存储库时，我们会将它设置为获取的存储库列表。只有当我们有了这些存储库时，我们才能渲染`Search.svelte`。更新`App.svelte`原来如此:

```
let usernameInputField = "";
let repositories = undefined;

async function onSubmit() {
  const url = `https://api.github.com/users/${usernameInputField}/repos`;

  const response = await fetch(url);
  repositories = await response.json();
} 
```

通过将`repositories`变量移出函数，它在整个组件中都是可用的，我们也可以在模板中引用它。让我们也更新`App.svelte`并导入我们的搜索组件。将这个添加到`App.svelte`中的 JavaScript 的顶部:

```
import Search from './Search.svelte' 
```

一旦组件被导入，我们就可以在模板中呈现它。获取存储库后，让我们的模板呈现搜索组件:

```
<main>
  <form on:submit|preventDefault={onSubmit}>
    <!-- snipped to save space -->
  </form>

  {#if repositories}
    <Search repositories={repositories} />
  {/if}
</main> 
```

如果你以前使用过 JSX，那么创建一个组件并向其中传递道具看起来会非常熟悉。尽管如此，苗条允许一个进一步的捷径。以这段代码为例:

```
<Search repositories={repositories} /> 
```

我们可以把它变成:

```
<Search {repositories} /> 
```

当 prop 名称和您想要作为 prop 传递的变量同名时，您可以省略第一部分，并传递用一对大括号括起来的变量。这是一个很好的快捷方式，可以减少打字量！

现在，如果你加载应用程序，输入用户名并点击`enter`，你应该会看到页面上呈现的“搜索组件”文本。现在我们已经完成了这项工作，我们准备开始工作，列出这些存储库，并允许用户过滤它们。

## 每个线圈都很纤细

为了遍历我们的存储库，我们可以使用 [`#each blocks`](https://svelte.dev/tutorial/each-blocks) ，它接受一个数组并为数组中的每一项输出一些 HTML。

在`Search.svelte`中，添加一个循环，输出我们找到的每个存储库的名称。记住，在一个苗条的模板中，很像 JSX，我们使用`{}`将动态内容插入 HTML。`Search.svelte`现在应该是这个样子:

```
<script> export let repositories; </script>

<style>  </style>

{#each repositories as repository}{repository.name}{/each} 
```

输出是混乱的，但是如果你加载应用程序，你应该看到一个我们找到的所有存储库的大列表。在我们做其他事情之前，让我们把这个看起来干净一点。在这里，您可以随意使用自己的 CSS，但这是我最后使用的代码及其外观:

![List of repositories](img/90b69c15d8612b4fcfb4dfe9d21595f3.png)

```
<script> export let repositories; </script>

<style> ul {
    list-style: none;
    margin: 0;
    padding: 0;
  }
  li {
    padding: 10px 5px;
  }

  li:nth-child(odd) {
    background-color: lightblue;
  }

  code {
    display: block;
  } </style>

<ul>
  {#each repositories as repository}
    <li><strong>{repository.name}</strong> <code>{repository.url}</code></li>
  {/each}
</ul> 
```

这是一个真正让我眼前一亮的领域:一个 svelet 组件中的所有 CSS 都默认作用于该组件。所以我可以直接设计元素的样式，而不用担心这些样式会影响组件外的其他匹配元素。

这并不意味着我不使用类、id 或其他选择器来精确定义我在苗条组件中设计的元素，但是很棒的是我不用担心默认的全局样式。作为一个额外的奖励，如果我写了一些未使用的 CSS，Svelte 会为我突出显示它。因为 CSS 的范围仅限于组件，Svelte 可以自信地检测未使用的 CSS 并提示您删除它。

## 搜索存储库

让我们给`Search.svelte`添加一个搜索框，这样我们就可以允许用户搜索存储库的名称。就像我们要求用户输入 GitHub 用户名的表单一样，我们将值绑定到一个变量，这样它会随着用户的输入自动更新。我还添加了一些额外的样式和 CSS，只是为了让事情看起来更好(随意改变样式，以适应您的喜好):

```
<script> export let repositories;

  let userSearchTerm = ""; </script>

<style> /* All the CSS from the previous step is still present, but removed from this code snippet to save space */
  .search-wrapper {
    border: 1px solid #ccc;
    border-radius: 10px;
    padding: 5px;
    margin: 10px auto;
  }

  .search-form input {
    border-top-left-radius: 10px;
    border-top-right-radius: 10px;
    width: 100%;
  } </style>

<div class="search-wrapper">
  <form class="search-form">
    <input
      type="text"
      bind:value={userSearchTerm}
      placeholder="search for repositories" />
  </form>
  <!-- list of repositories here as per previous code sample -->
</div> 
```

现在，用户可以在框中键入内容，但我们现在要做的是在用户键入内容时过滤我们的存储库列表。那么，当用户更新输入时，我们如何运行代码呢？答案在于 Svelte 如何处理反应性。

在一个细长的组件中，考虑这样一行:

```
console.log(userSearchTerm) 
```

如果您添加它，您将会看到它只被注销一次，当组件第一次被创建和运行时。但是尝试在这一行前面加上前缀`$:`，就像这样:

```
$: console.log(userSearchTerm) 
```

如果你加载应用程序并在搜索框中输入，你会在每次输入时看到这个代码。Svelte 使用这种语法让你告诉 Svelte 编译器，你希望这段代码在它引用的任何东西改变的时候运行。您可能认为这个语法看起来很奇怪——您可能是对的——但是它是完全有效的 JavaScript，尽管 JavaScript 语法很少被使用。(它被称为[标记语句](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/label)。)

如果要运行多行代码，可以用一对大括号将它括起来，以创建一个块:

```
$: {
 console.log(userSearchTerm)
 console.log('and again', userSearchTerm)
} 
```

当您需要基于他人更新或创建新值时，这非常有用。例如:

```
$: value = x * 2; 
```

这段代码会将`value`设置为`x`的两倍，但也会确保将来`x`更新时`value`也会更新。

因此，对于我们的特定用例，我们可以定义一个新变量`filteredRepos`，当`userSearchTerm`发生变化时，它会更新，从而过滤存储库，只过滤那些名称与用户搜索的内容相匹配的存储库:

```
$: filteredRepos = repositories.filter((repo) => {
  return repo.name.toLowerCase().includes(userSearchTerm.toLowerCase());
}); 
```

当用户更新搜索词时，或者即使我们在一组新的存储库中通过，Svelte 也会自动为我们重新运行。

现在我将更新模板的每一行以使用这个新的数组，`filteredRepos`:

```
{#each filteredRepos as repository} 
```

现在，当我们在搜索字段中键入内容时，它会正确更新，您应该会看到您现在能够搜索存储库了！

## 用户键入时搜索的替代解决方案

我们使用 Svelte 的`$:`语法在用户输入时更新存储库，但是我们也可以稍微改变代码的结构来避免它。我们知道，当数据改变时，Svelte 会自动重新呈现模板，所以我们可以考虑这一点。我们可以定义一个`filter`方法，它获取我们的存储库和搜索词，并返回匹配结果:

```
function filter(repositories, userSearchTerm) {
  return repositories.filter((repo) => {
    return repo.name.toLowerCase().includes(userSearchTerm.toLowerCase());
  });
} 
```

现在我们可以在模板中直接调用这个函数:

```
{#each filter(repositories, userSearchTerm) as repository} 
```

这仍然可以正常工作。我不确定我个人是否喜欢这种方法。我不喜欢过滤调用被深埋在模板中，我喜欢让显式的`$: filteredRepos = ...`行向任何阅读代码的人清楚地表明我们的存储库随着用户输入而更新。

另一个解决方案是使用事件侦听器。我们可以绑定到文本字段的`on:input`事件，并在获得输入事件时过滤我们的存储库。首先，我们绑定到模板中的事件:

```
<input
  type="text"
  bind:value={userSearchTerm}
  on:input={onUserSearchInput}
  placeholder="search for repositories" /> 
```

然后我们编写一个函数，当用户输入:

```
let filteredRepositories = repositories;

function onUserSearchInput() {
  filteredRepositories = repositories.filter((repo) => {
    return repo.name.toLowerCase().includes(userSearchTerm.toLowerCase());
  });
} 
```

最后，我们在模板中使用新变量:

```
{#each filteredRepositories as repository} 
```

然而，这种方法给我们带来了一个错误。如果`repositories`属性更新，我们的`filteredRepositories`列表不会更新，因为我们只是在事件监听器中对它进行了初始设置。你可以在我们的应用程序中搜索一个用户名，然后再搜索另一个用户名。当您第二次搜索时，您将看不到更新的存储库列表。

我们可以通过将初始声明`filteredRepositories`更新为反应式来解决这个问题:

```
$: filteredRepositories = repositories; 
```

但是现在我们有了另一个 bug，如果在应用过滤器时存储库列表发生了变化，那么过滤器不会应用到新的存储库列表中。

让我们回到最初的解决方案:

```
$: filteredRepositories = repositories.filter((repo) => {
  return repo.name.toLowerCase().includes(userSearchTerm.toLowerCase());
}); 
```

我上面提到的两个错误都不会发生，一切都会如您所料地更新。我发现学习 Svelte 的特性并利用它对反应性的支持会使你的代码更干净(注意，这个解决方案比我们自己绑定事件监听器的方案使用的代码少得多),并减少 UI 与状态不同步时出现错误的机会。

## 为生产捆绑苗条

现在我们的应用程序功能齐全了，让我们把它打包用于生产。Svelte starter 模板将`npm run build`定义为一个命令，您可以运行该命令来捆绑您的应用程序以供生产使用。在我们的应用程序上运行它会生成大小为 6kB 的`bundle.js`和大小为 1kB 的`bundle.css`。虽然 6kB 听起来可能很多，并且您可以在没有框架帮助的情况下编写这样一个简单的应用程序，但请注意，6kB 捆绑包的大部分成本是固定的:您已经支付了捆绑包的费用，因此随着应用程序的增长，文件大小不会变得太大。当然，它会随着您编写的所有代码而增长，但是就框架成本而言，这是非常小的。您还可以进一步使用代码分割和其他技术来尽可能减小初始包的大小。

## 苗条提供了一个有前途的替代反应和 Vue

我希望这篇文章能让你明白:我真的很喜欢苗条！我真的很喜欢使用这个框架，也很喜欢这个苗条团队做出的决定，他们创建了一个框架，在幕后为我做了很多工作。svelet 框架的一个明确目标是减少开发人员编写的代码量，在这个许多开发人员觉得他们编写了大量样板文件的世界里，svelet 就像一股清新的空气。

## 分享这篇文章