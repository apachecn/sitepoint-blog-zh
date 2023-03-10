# 建立一个苗条的游戏:一个有趣的井字游戏项目

> 原文：<https://www.sitepoint.com/svelte-tic-tac-toe/>

苗条的 T2 是构建用户界面的下一代方式。

虽然 React、Vue 和 Angular 等框架在浏览器中完成了大部分工作，但 Svelte 将其提升到了一个新的水平。当你构建应用程序的时候，它就开始工作了，它把你的苗条的应用程序编译成高效的普通 JavaScript。所以你可以两全其美。你用苗条的方式写你的代码，这使得它容易阅读，重用和你使用框架时得到的所有其他好处，并且它有助于一个非常快的 web 应用程序，因为它符合普通的 JavaScript，所以你没有你正在使用的 JavaScript 框架的开销。

苗条可以让你写更少的代码。它也没有使用 React 推广的虚拟 DOM 的概念。相反，当应用程序的状态发生变化时，它会像外科手术一样更新 DOM，以便应用程序快速启动并保持快速运行。

## 先决条件

对于本教程，您需要 HTML、CSS 和 JavaScript 的基础知识。

您还必须安装了最新版本的 [Node.js](https://nodejs.org/) 。

我们还将使用默认随 Node.js 一起安装的`npx`。

在整个教程中，我们将使用`yarn`。如果你还没有安装`yarn`，从[这里](https://yarnpkg.com/en/docs/install)安装。

为了确保我们在同一页上，这些是本教程中使用的版本:

*   节点 12.10.0
*   npx 6.11
*   纱线 1.17.3

## 苗条身材入门

在本教程中，我们将建立一个苗条的井字游戏。到最后，你将能够快速启动并运行 Svelte，并开始在 Svelte 中构建自己的应用程序。

首先，我们必须使用 [degit](https://github.com/Rich-Harris/degit) 搭建我们的应用程序。`degit`和`git clone`差不多，但是要快得多。你可以在这里了解更多关于[的信息。](https://github.com/Rich-Harris/degit/blob/master/README.md)

继续，通过在终端中键入以下命令来创建一个新项目:

```
$ npx degit sveltejs/template tic-tac-toe-svelte
```

`npx`允许您使用`degit`命令，而无需全局安装。

在`npx`之前，我们必须执行以下两个步骤来获得相同的结果:

```
$ npm install --global degit
$ degit sveltejs/template tic-tac-toe-svelte
```

感谢`npx`，我们没有膨胀我们的全局名称空间，我们总是使用最新版本的`degit`。

`degit`将回购[https://github.com/sveltejs/template](https://github.com/sveltejs/template)克隆到`tic-tac-toe-svelte`文件夹中。

继续进入`tic-tac-toe-svelte`目录，通过在终端中键入以下命令来安装依赖项:

```
$ cd tic-tac-toe-svelte
$ yarn
```

现在，通过在终端中键入以下命令来运行应用程序:

```
$ yarn dev
```

现在打开浏览器，转到 [http://localhost:5000](http://localhost:5000) ，您应该会看到以下内容:

![Svelte - Hello World](img/5e269bdf95233ce45fbed88535c57e77.png)

如果你进入`src/`文件夹，你会看到两个文件，`App.svelte`和`main.js`。`main.js`是一个苗条 app 的入口。

打开`main.js`，您应该会看到以下内容:

```
import App from './App.svelte';

const app = new App({
    target: document.body,
    props: {
        name: 'world'
    }
});

export default app;
```

上面的文件导入了`App.svelte`，并使用一个`target`元素实例化了它。它将组件放在 DOM 的`document.body`中。它还将`name`道具传递给`App`组件。此道具将在`App.svelte`接入。

Svelte 中的组件是使用包含 HTML、CSS 和 JavaScript 的`.svelte`文件编写的。如果你和 [Vue](https://vuejs.org/) 一起工作过，这看起来会很熟悉。

现在打开`App.svelte`，您应该会看到以下内容:

```
<script>
    export let name;
</script>

<style>
    h1 {
        color: purple;
    }
</style>

<h1>Hello {name}!</h1>
```

首先，我们内部有`script`标记，其中有一个名为`name`的命名导出。这应该类似于`main.js`中提到的道具。

然后我们有了一个`style`标签，让我们可以对特定文件中的所有元素进行样式化，该文件的范围仅限于该文件，因此不存在级联问题。

然后，在底部，我们有一个`h1`标签，里面有`Hello {name}!`。花括号中的`name`将被实际值代替。这就是所谓的价值插值。所以屏幕上才会印上`Hello world!`。

## 细长组件的基本结构

所有的`.svelte`文件基本上具有以下结构:

```
<script>
    /* Javascript logic */
</script>

<style>
    /* CSS styles */
</style>

<!-- HTML markup -->
```

HTML 标记会有一些额外的特定语法，但其余的只是普通的 HTML、CSS 和 JavaScript。

## 苗条地做井字游戏

让我们开始构建我们的井字游戏。

用以下内容替换`main.js`:

```
import App from './App.svelte'

const app = new App({
  target: document.body,
})

export default app
```

我们基本上已经从`App`组件实例化中移除了`props`属性。

现在用以下内容替换`App.svelte`:

```
<script>
  const title = "Tic Tac Toe";
</script>

<svelte:head>
  <title>{title}</title>
</svelte:head>

<h1>{title}</h1>
```

这里，我们用一个字符串`Tic Tac Toe`初始化一个常量变量`title`。

然后，在下面的标记中，我们使用一个特殊的简单语法`svelte:head`，来设置`head`标签中的`title`属性。

基本上类似于这样做:

```
<head>
    <title>Tic Tac Toe</title>
</head>
```

但是使用`svelte:head`语法的优点是`title`可以在运行时改变。

然后我们在我们的`h1`标签中使用相同的`title`属性。它现在应该是这样的:

![Svelte - Tic Tac Toe](img/209cdcdbfff7cfde00899366909df34b.png)

现在在`src/`目录下创建另外两个文件，分别命名为`Board.svelte`和`Square.svelte`。

打开`Square.svelte`并粘贴以下内容:

```
<script>
  export let value;
</script>

<style>
  .square {
    flex: 1 0 25%;
    width: 50px;
    height: 70px;
    background-color: whitesmoke;
    border: 2px solid black;
    margin: 5px;
    padding: 5px;
    font-size: 20px;
    text-align: center;
  }

  .square:hover {
    border: 2px solid red;
  }
</style>

<button class="square">{value}</button>
```

基本上，我们正在创建一个按钮并设计它的样式。

现在打开`Board.svelte`并粘贴以下内容:

```
<script>
  import Square from "./Square.svelte";
  let squares = [null, null, null, null, null, null, null, null, null];
</script>

<style>
  .board {
    display: flex;
    flex-wrap: wrap;
    width: 300px;
  }
</style>

<div class="board">
  {#each squares as square, i}
    <Square value={i} />
  {/each}
</div>
```

这里我们已经导入了`Square`组件。我们还初始化了`squares`数组，它将包含我们的`X`和`0`的数据，目前是`null`。

## 每一圈都很苗条

在 HTML 标记中，我们使用了一种特殊的简单的循环语法。这类似于普通 JavaScript 中的`Array.forEach`循环。语法如下所示:

```
{#each squares as square, i}
    <Square value={i} />
{/each}
```

它循环了九次，因为`squares.length`等于`9`。第一次迭代中`square`的值是`null`，因为第一项是`null`，而`i`的值是`0`，因为第一次索引是`0`。在每次迭代中，`square`的值保持为`null`，因为`squares`数组的所有值当前都是`null`，但是`i`的值从`0`到`8`一直递增，因为它是索引值。

然后，它将`i`的值作为`value`属性传递给`Square`组件。

它现在应该是这样的:

![Svelte - Tic Tac Toe Board](img/ea12e021431b3f0402de918dff39b72d.png)

现在我们已经有了合适的方块，让我们添加一些逻辑。

首先，我们需要显示下一步棋，无论是`X`还是`0`。同样，让我们确保可以用`X`和`0`点击方块。

为此，将以下代码添加到`Board.svelte`:

```
<script>
  .
  .
  .

  let xIsNext = true;
  $: status = "Next Player: " + (xIsNext ? "X" : "0");

  function handleClick(i) {
    if (!squares[i]) {
      squares[i] = xIsNext ? "X" : "0";
      xIsNext = !xIsNext;
    }
  }
</script>

<style>
  h3 {
    color: red;
  }
  .
  .
  .
</style>

<h3>{status}</h3>

<div class="board">
  {#each squares as square, i}
    <Square value={square} handleClick={() => handleClick(i)} />
  {/each}
</div>
```

上面的代码将变量`xIsNext`初始化为布尔值。这个布尔将继续切换到下一步。

如果`X`是下一步棋，那么`xIsNext`将是`true`。如果`0`是下一步棋，那么`xIsNext`将是`false`。默认设置为`true`。

然后我们有了`status`变量。这是一种特殊类型的变量，称为反应式赋值。如果你在它前面放一个美元($)符号，它就会对这些变化做出反应。因此，如果`xIsNext`再次改变，那么`status`将被更新为它的新值。当`xIsNext`等于`true`时，`status`将为`Next Player: X`，当`xIsNext`等于`false`时，`status`将为`Next Player: 0`。

然后我们将`value`道具作为`square`传递。这将根据内容显示`X`或`0`。

然后我们有一个`handleClick`函数，当一个方块被点击时它被调用。`handleClick`函数需要作为道具传递给`Square`组件，在那里它被实现来监听点击。所以不改变`Square.svelte`，方块上的点击就不行了。

总之，`handleClick`函数传递了一个索引`i`,因为我们必须将棋盘的内容存储在我们的`square`数组中，以便比较某人是赢还是输，或者检查是否是平局。

第一行是一个`if`条件，用于确保我们不会改变已经填充的方块的内容。如果已经是`null`，那么才会进入`if`条件体。

第二行检查`xIsNext`的值，如果是`true`，那么它将`X`作为方块的值，如果是`false`，那么它将`0`作为方块的值。

最后，`xIsNext`切换玩下一步棋。

现在编辑`Square.svelte`如下:

```
<script>
  .
  .
  .
  export let handleClick;
</script>

<style>
  .
  .
  .
</style>

<button class="square" on:click={handleClick}>{value || ""}</button>
```

我们在这里添加的唯一东西是一个命名的导出`handleClick`，和一个`button`上的`on:click`处理程序，它指向`handleClick`。这是从`Board.svelte`传来的。没有上面的代码，点击就不行。此外，只有当按钮不是`null`时，我们才显示按钮内的内容——也就是说，只有当`X`和`0`存在时，我们才显示。

现在看起来是这样的:

![Svelte - Tic Tac Toe Board Empty](img/bfecfed8811267d544811d52b8d8dcbb.png)

如果您尝试在方块内单击，那么`X`和`0`将会正确显示。您还可以看到状态的适当变化，如下所示:

![Svelte - Tic Tac Toe Board Full](img/c09605ef9c00b7b4836d4afae64d8e8a.png)

## 寻找赢家

现在我们可以玩这个游戏了，让我们弄清楚如何找到一个赢家，一个输家或者检查它是否是一个平局。

打开`Board.svelte`并添加以下内容:

```
<script>
  let winner = null;
  .
  .
  .
  function handleClick(i) {
    if (!squares[i]) {
      squares[i] = xIsNext ? "X" : "0";
      xIsNext = !xIsNext;
      winner = calculateWinner(squares);
    }
  }

  function calculateWinner(squares) {
    const winningCombo = [
      [0, 1, 2],
      [3, 4, 5],
      [6, 7, 8],
      [0, 3, 6],
      [1, 4, 7],
      [2, 5, 8],
      [0, 4, 8],
      [2, 4, 6]
    ];
    for (let i = 0; i < winningCombo.length; i++) {
      const [a, b, c] = winningCombo[i];
      if (squares[a] && squares[a] === squares[b] && squares[a] === squares[c])
        return `Winner: ${squares[a]}`;
    }

    const isDraw = squares.every(square => square !== null);
    return isDraw ? "It's a draw" : null;
  }
</script>

.
.
.

{#if winner}
  <h3>{winner}</h3>
{:else}
  <h3>{status}</h3>
{/if}

<div class="board">
  .
  .
  .
</div>
```

在上面的代码中，我们初始化了一个`winner`变量来跟踪获胜者。最初，是`null`。然后在每次点击后，我们调用`calculateWinner`，正如你在`handleClick`函数中看到的，并将其返回值设置为`winner`。

`calculateWinner`是一个接受`squares`数组并决定获胜者的函数。`winningCombo`是一个决定获胜组合的数组。要赢得一场`X`和`0`的比赛，你需要要么有一条纵横的直线，要么有一条对角线。上面的数组是一个可能的获胜组合列表。

在`for`循环中，我们通过比较索引来检查`squares`数组中的值是在一条直线上(水平或垂直)还是对角线上。如果是的话，我们宣布获胜者。如果不是，我们检查是否所有的值都被填满，如果是真的，那就是平局，因为我们没有更多的地方玩`X`和`0`。否则，如果游戏还在进行，我们返回`null`。

最后，我们为`if...else`添加了简单的特定语法。如果`winner`不是`null`，则显示`winner`，否则显示`status`。

如果有人赢了，现在应该是这样的:

![Svelte - Tic Tac Toe Board Winner](img/ca6ad9a372e027722e31bfca4806414d.png)

如果是平局，现在应该是这样的:

![Svelte - Tic Tac Toe Board Draw](img/000b026fac5024e98a6478e32c4304e7.png)

现在我们来添加一个重启游戏的方法。

在同一个`Board.svelte`文件中，添加以下内容:

```
<script>
  .
  .
  .
  function restartGame() {
    squares = [null, null, null, null, null, null, null, null, null];
    xIsNext = true;
    winner = null;
  }
  .
  .
  .
</script>

.
.
.

{#if winner}
  <button on:click={restartGame}>Restart Game</button>
{/if}
```

上面的代码将向应用程序添加一个按钮`Restart Game`，该按钮将调用`restartGame`函数来重置所有必要的值以重新开始。

因此，如果您赢得一场比赛或获得一场平局，您应该会看到带有`Restart Game`按钮的以下屏幕:

![Svelte - Tic Tac Toe Board Restart](img/35db7c738885d642b4280dd52f8f3acc.png)

整个`Board.svelte`文件现在应该看起来像这样:

```
<script>
  import Square from "./Square.svelte";

  let winner = null;
  let squares = [null, null, null, null, null, null, null, null, null];
  let xIsNext = true;
  $: status = "Next Player: " + (xIsNext ? "X" : "0");

  function restartGame() {
    squares = [null, null, null, null, null, null, null, null, null];
    xIsNext = true;
    winner = null;
  }

  function handleClick(i) {
    if (!squares[i]) {
      squares[i] = xIsNext ? "X" : "0";
      xIsNext = !xIsNext;
      winner = calculateWinner(squares);
    }
  }

  function calculateWinner(squares) {
    const winningCombo = [
      [0, 1, 2],
      [3, 4, 5],
      [6, 7, 8],
      [0, 3, 6],
      [1, 4, 7],
      [2, 5, 8],
      [0, 4, 8],
      [2, 4, 6]
    ];
    for (let i = 0; i < winningCombo.length; i++) {
      const [a, b, c] = winningCombo[i];
      if (squares[a] && squares[a] === squares[b] && squares[a] === squares[c])
        return `Winner: ${squares[a]}`;
    }

    const isDraw = squares.every(square => square !== null);
    return isDraw ? "It's a draw" : null;
  }
</script>

<style>
  h3 {
    color: red;
  }

  .board {
    display: flex;
    flex-wrap: wrap;
    width: 300px;
  }
</style>

{#if winner}
  <h3>{winner}</h3>
{:else}
  <h3>{status}</h3>
{/if}

<div class="board">
  {#each squares as square, i}
    <Square value={square} handleClick={() => handleClick(i)} />
  {/each}
</div>

{#if winner}
  <button on:click={restartGame}>Restart Game</button>
{/if}
```

现在，您可以通过在终端中键入以下内容来构建生产代码:

```
$ yarn build
```

这将生成一个`public/bundle.js`文件，其中包含为生产准备的优化代码。

完整的代码可以在 [Github](https://github.com/deadcoder0904/tic-tac-toe-svelte) 上找到。

你可以在这里找到应用程序的演示。

## 结论

在本教程中，我们已经使用 Svelte 构建了一个完整的游戏。通过构建我们的井字游戏，我们学到了很多东西。

Svelte 是一个全新的编译器，可以用来创建超快的网络应用。它与传统框架有很大的不同，因为它将所有东西都转换成普通的 JavaScript，这使得它非常快。

如果你使用过 React 或 Vue 这样的框架，那么学习 Svelte 应该非常容易。试一试，你不会失望的。

当然，这只是冰山一角。查看[官方网站](https://svelte.dev/)以获得更多精彩内容和惊人的[示例部分](https://svelte.dev/examples)，其中包含大量 REPL 的示例。

## 分享这篇文章