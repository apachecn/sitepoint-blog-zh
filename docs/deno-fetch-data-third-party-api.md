# 如何用 Deno 从第三方 API 获取数据

> 原文：<https://www.sitepoint.com/deno-fetch-data-third-party-api/>

**在本文中，我们将探索 [Deno](https://www.sitepoint.com/deno-guide) ，这是一个相对较新的工具，是 Node.js 的竞争对手/替代者，它提供了一个更安全的环境，并且自带了 TypeScript 支持。**

我们将使用 Deno 构建一个命令行工具，向第三方 API——[星球大战 API](https://swapi.dev/) —发出请求，并了解 Deno 提供了哪些功能，它与 Node 有何不同，以及它喜欢使用什么。

Deno 是一个更加固执己见的运行时，它用 TypeScript 编写，包括自己的代码格式化程序(`deno fmt`)，并使用 es 模块——看不到任何 CommonJS `require`语句。它在默认情况下也是非常安全的:你必须明确地给你的代码权限来发出网络请求，或者从磁盘读取文件，这是 Node 默认允许程序做的事情。在本文中，我们将介绍安装 Deno、设置我们的环境，以及构建一个简单的命令行应用程序来发出 API 请求。

和以往一样，你可以在 [GitHub](https://github.com/sitepoint-editors/deno-star-wars-api) 上找到这篇文章的代码。

## 安装 Deno

可以查看 [Deno 网站](https://deno.land/#installation)了解完整说明。如果您在 macOS 或 Linux 上，您可以将此命令复制到您的终端:

```
curl -fsSL https://deno.land/x/install/install.sh | sh 
```

您还需要将安装目录添加到您的`$PATH`中。

如果您使用的是 Windows，请不要担心，因为您可以通过软件包管理器(如 Chocolatey)安装 Deno:

```
choco install deno 
```

如果 Chocolately 不适合你， [deno_install](https://github.com/denoland/deno_install) 列出了多种安装方式，挑选一款最适合你的吧。

您可以通过运行以下命令来检查是否安装了 Deno:

```
deno -V 
```

这应该输出 Deno 版本。在写这篇文章的时候，最新的版本是 1.7.5，这是我正在使用的版本。

如果你用的是 VS 代码，我强烈推荐安装 [Deno VS 代码插件](https://github.com/denoland/vscode_deno)。如果你使用另一个编辑器，检查 [Deno 文档](https://deno.land/manual@v1.7.4/getting_started/setup_your_environment#editors-and-ides)找到正确的插件。

请注意，如果您使用的是 VS 代码，默认情况下，当您加载项目时，Deno 插件是不启用的。您应该在存储库中创建一个`.vscode/settings.json`文件，并添加以下内容来启用插件:

```
{
  "deno.enable": true
} 
```

同样，如果您不是 VS 代码用户，请查看上面的手册，为您选择的编辑器找到正确的设置。

## 写我们的第一个剧本

让我们确保 Deno 正常运行。创建`index.ts`并将以下内容放入其中:

```
console.log("hello world!"); 
```

我们可以用`deno run index.ts`来运行它:

```
$ deno run index.ts
Check file:///home/jack/git/deno-star-wars-api/index.ts
hello world 
```

注意，我们可能会在编辑器中看到一个 TypeScript 错误:

```
'index.ts' cannot be compiled under '--isolatedModules' 
because it is considered a global script file. Add an import, 
export, or an empty 'export {}' statement 
to make it a module.ts(1208) 
```

发生此错误是因为 TypeScript 不知道此文件将使用 ES 模块导入。很快就会，因为我们将添加导入，但同时如果我们想删除错误，我们可以在脚本的底部添加一个空的`export`语句:

```
export {} 
```

这将使 TypeScript 编译器相信我们正在使用 ES 模块并消除错误。我不会在博文中的任何代码示例中包含这一点，但如果我们添加它，除了消除 TypeScript 噪声之外，它不会改变任何事情。

## 正在获取 Deno

Deno 实现了对我们习惯在浏览器中使用的相同获取 API 的支持。它内置于 Deno 中，这意味着不需要安装或配置任何包。让我们来看看它是如何工作的，首先向我们将在这里使用的 API 发出请求，即[星球大战 API(或 SWAPI)](https://swapi.dev/) 。

向`https://swapi.dev/api/people/1/`发出请求会给我们带回卢克·天行者所需的所有数据。让我们更新我们的`index.ts`文件来发出请求。更新`index.ts`看起来是这样:

```
const json = fetch("https://swapi.dev/api/people/1");

json.then((response) => {
  return response.json();
}).then((data) => {
  console.log(data);
}); 
```

尝试在您的终端中使用`deno run`运行此命令:

```
$ deno run index.ts
Check file:///home/jack/git/deno-star-wars-api/index.ts
error: Uncaught (in promise) PermissionDenied: network access to "swapi.dev", run again with the --allow-net flag
    throw new ErrorClass(res.err.message); 
```

Deno 在默认情况下是安全的，这意味着脚本需要权限才能做任何被认为是危险的事情——比如读/写文件系统和发出网络请求。当 Deno 脚本运行时，我们必须给它们权限来执行这样的操作。我们可以使用`--allow-net`标志来启用我们的:

```
$ deno run --allow-net index.ts
Check file:///home/jack/git/deno-star-wars-api/index.ts
{
  name: "Luke Skywalker",
  ...(data snipped to save space)...
} 
```

但是这个标志允许脚本访问任何 URL。我们可以更明确一点，只允许我们的脚本访问我们添加到 allowlist 中的 URL:

```
$ deno run --allow-net=swapi.dev index.ts 
```

如果我们正在运行我们自己创作的脚本，我们可以相信他们不会做任何他们不应该做的事情。但是很高兴知道，在默认情况下，我们执行的任何 Deno 脚本都不能在未经我们允许的情况下做任何破坏性太大的事情。从现在开始，每当我在本文中谈到运行我们的脚本时，这就是我正在运行的命令:

```
$ deno run --allow-net=swapi.dev index.ts 
```

我们也可以使用[顶级等待](https://v8.dev/features/top-level-await)稍微不同地编写这个脚本，它让我们使用`await`关键字而不是处理承诺:

```
const response = await fetch("https://swapi.dev/api/people/1/");
const data = await response.json();
console.log(data); 
```

这是我喜欢的风格，也是我将在本文中使用的风格，但是如果你更愿意坚持承诺，请随意。

## 安装第三方依赖项

现在我们可以向星球大战 API 发出请求了，让我们开始考虑如何允许我们的用户使用这个 API。我们将提供命令行标志，让它们指定要查询的资源(比如人、电影或行星)以及过滤它们的查询。因此，对我们的命令行工具的调用可能如下所示:

```
$ deno run --allow-net=swapi.dev index.ts --resource=people --query=luke 
```

我们可以手动解析这些额外的命令行参数，或者使用第三方库。在 Node.js 中，最好的解决方案是 [Yargs](https://github.com/yargs/yargs) ，Yargs 也支持 Deno，所以我们可以使用 Yargs 来解析和处理我们想要支持的命令行标志。

然而，Deno 没有包管理器。我们不会创建一个`package.json`并安装一个依赖项。相反，我们从 URL 导入。Deno 软件包的最佳来源是 [Deno 软件包库](https://deno.land/x)，在那里你可以搜索你想要的软件包。大多数流行的 npm 包现在也支持 Deno，所以通常有很多选择，你很有可能找到你想要的。

在撰写本文时，在 Deno 存储库上搜索`yargs`,得到的是 yargs 16.2.0。要在本地使用它，我们必须从它的 URL 导入它:

```
import yargs from "https://deno.land/x/yargs/deno.ts"; 
```

当我们现在运行脚本时，我们将首先看到大量输出:

```
$ deno run --allow-net=swapi.dev index.ts
Download https://deno.land/x/yargs/deno.ts
Warning Implicitly using latest version (v16.2.0-deno) for https://deno.land/x/yargs/deno.ts
Download https://deno.land/x/yargs@v16.2.0-deno/deno.ts
Download https://deno.land/x/yargs@v16.2.0-deno/build/lib/yargs-factory.js
Download https://deno.land/x/yargs@v16.2.0-deno/lib/platform-shims/deno.ts
Download https://deno.land/std/path/mod.ts
Download https://deno.land/x/yargs_parser@v20.2.4-deno/deno.ts
...(more output removed to save space) 
```

Deno 第一次看到我们正在使用一个新模块时，它会下载并在本地缓存它，这样我们就不必每次使用该模块并运行脚本时都下载它。

请注意上面输出中的这一行:

```
Warning Implicitly using latest version (v16.2.0-deno) 
for https://deno.land/x/yargs/deno.ts 
```

这是 Deno 告诉我们，我们在导入 Yargs 时没有指定特定的版本，所以它只是下载了最新的版本。这对于快速的辅助项目来说可能没问题，但是一般来说，将我们的导入固定到我们想要使用的版本是一个好的实践。我们可以通过更新 URL 来做到这一点:

```
import yargs from "https://deno.land/x/yargs@v16.2.0-deno/deno.ts"; 
```

我花了点时间才找到那个网址。我是通过识别当我在 Deno 存储库上搜索“yargs”时被带到的 URL 是`https://deno.land/x/yargs@v16.2.0-deno`找到它的。然后我回头看控制台输出，意识到 Deno 实际上给了我确切的路径:

```
Warning Implicitly using latest version (v16.2.0-deno) 
for https://deno.land/x/yargs/deno.ts
Download https://deno.land/x/yargs@v16.2.0-deno/deno.ts 
```

我强烈建议像这样固定您的版本号。它将避免有一天一个令人惊讶的问题，因为你碰巧在一个新发布的依赖项之后运行。

## deno fmt

在我们继续构建我们的命令行工具之前，先简单提一下。Deno 带有一个内置的格式化程序， [`deno fmt`](https://deno.land/manual/tools/formatter) ，它自动将代码格式化为一致的风格。把它想象成更漂亮的，但专门为 Deno 设计的，内置的。这是我被吸引到 Deno 的另一个原因；我喜欢那些无需任何配置就能为您提供所有这一切的工具。

我们可以在本地运行格式化程序，如下所示:

```
$ deno fmt 
```

这将格式化当前目录中的所有 JS 和 TS 文件，或者我们可以给它一个要格式化的文件名:

```
$ deno fmt index.ts 
```

或者，如果我们已经获得了 VS 代码扩展，我们可以转而进入`.vscode/settings.json`，在这里我们之前启用了 Deno 插件，并添加这两行:

```
{
  "deno.enable": true,
  "editor.formatOnSave": true,
  "editor.defaultFormatter": "denoland.vscode-deno"
} 
```

这将配置 VS 代码在我们保存文件时自动运行`deno fmt`。完美！

## 使用纱线

我不会深入讨论 Yargs 的全部细节(如果您想熟悉它的所有功能，可以阅读文档)，但是我们在这里声明，我们希望采用两个必需的命令行参数:`--resource`和`--query`:

```
import yargs from "https://deno.land/x/yargs@v16.2.0-deno/deno.ts";

const userArguments: {
  query: string;
  resource: "films" | "people" | "planets";
} = yargs(Deno.args)
  .describe("resource", "the type of resource from SWAPI to query for")
  .choices("resource", ["people", "films", "planets"])
  .describe("query", "the search term to query the SWAPI for")
  .demandOption(["resource", "query"])
  .argv;

console.log(userArguments); 
```

*注意:现在我们有了一个`import`语句，我们不再需要`export {}`来消除打字错误。*

不幸的是，在编写 TypeScript 时，它似乎没有选择所有的类型定义:`yargs(Deno.args)`的返回类型被设置为`{}`，所以让我们稍微整理一下。我们可以定义自己的 TypeScript 接口，覆盖我们所依赖的 Yargs API 的所有部分:

```
interface Yargs<ArgvReturnType> {
  describe: (param: string, description: string) => Yargs<ArgvReturnType>;
  choices: (param: string, options: string[]) => Yargs<ArgvReturnType>;
  demandOption: (required: string[]) => Yargs<ArgvReturnType>;
  argv: ArgvReturnType;
} 
```

在这里，我声明了我们正在使用的函数，它们返回相同的 Yargs 接口(这就是让我们链接调用的原因)。我还采用了一个泛型类型，`ArgvReturnType`，它表示我们在 Yargs 处理完参数后得到的参数的结构。这意味着我可以声明一个`UserArguments`类型并将`yargs(Deno.argv)`的结果转换成它:

```
interface Yargs<ArgvReturnType> {
  describe: (param: string, description: string) => Yargs<ArgvReturnType>;
  choices: (param: string, options: string[]) => Yargs<ArgvReturnType>;
  demandOption: (required: string[]) => Yargs<ArgvReturnType>;
  argv: ArgvReturnType;
}

interface UserArguments {
  query: string;
  resource: "films" | "people" | "planets";
}

const userArguments = (yargs(Deno.args) as Yargs<UserArguments>)
  .describe("resource", "the type of resource from SWAPI to query for")
  .choices("resource", ["people", "films", "planets"])
  .describe("query", "the search term to query the SWAPI for")
  .demandOption(["resource", "query"])
  .argv; 
```

我确信在未来 Yargs 可能会提供这些开箱即用的类型，所以如果您使用的是比 16.2.0 更高版本的 Yargs，那么值得检查一下。

## 查询星球大战 API

现在我们有了接受用户输入的方法，让我们编写一个函数，它接受输入的内容并正确地查询星球大战 API:

```
async function queryStarWarsAPI(
  resource: "films" | "people" | "planets",
  query: string,
): Promise<{
  count: number;
  results: object[];
}> {
  const url = `https://swapi.dev/api/${resource}/?search=${query}`;
  const response = await fetch(url);
  const data = await response.json();
  return data;
} 
```

我们将采用两个参数:要搜索的资源和搜索词本身。星球大战 API 返回的结果将返回一个对象，该对象包括一个`count`(结果数)和一个`results`数组，该数组是来自我们 API 查询的所有匹配资源的数组。我们将在文章的后面讨论如何提高这种类型的安全性，但是现在我选择了`object`来开始。这不是一个很好的使用类型，因为它非常自由，但有时我更喜欢让一些东西工作，然后在以后改进这些类型。

现在我们有了这个函数，我们可以获取由 Yargs 解析的参数并获取一些数据！

```
const result = await queryStarWarsAPI(
  userArguments.resource,
  userArguments.query,
);
console.log(`${result.count} results`); 
```

现在让我们运行这个:

```
$ deno run --allow-net=swapi.dev index.ts --resource films --query phantom
Check file:///home/jack/git/deno-star-wars-api/index.ts
1 results 
```

我们看到我们得到了一个结果(我们很快会处理不正确的复数形式！).让我们根据用户搜索的资源做一些工作来获得更好的输出。首先，我将做一些 TypeScript 工作来改进返回类型，这样我们就可以在编辑器中从 TypeScript 获得更好的支持。

首先要做的是创建一个新的类型，表示我们让用户查询的资源:

```
type StarWarsResource = "films" | "people" | "planets"; 
```

然后我们可以使用这种类型，而不是复制它，第一次是当我们将它传递给 Yargs 时，第二次是当我们定义`queryStarWarsAPI`函数时:

```
interface UserArguments {
  query: string;
  resource: StarWarsResource;
}

// ...

async function queryStarWarsAPI(
  resource: StarWarsResource,
  query: string,
): Promise<{
  count: number;
  results: object[];
}>  { ... } 
```

接下来，让我们来看看星球大战 API，并创建表示我们将从不同资源中获得什么的接口。这些类型并不详尽(API 返回更多)。我只是为每种资源挑选了几个项目:

```
interface Person {
  name: string;
  films: string[];
  height: string;
  mass: string;
  homeworld: string;
}

interface Film {
  title: string;
  episode_id: number;
  director: string;
  release_date: string;
}

interface Planet {
  name: string;
  terrain: string;
  population: string;
} 
```

一旦我们有了这些类型，我们就可以创建一个函数来处理每种类型的结果，然后调用它。我们可以使用类型转换告诉 TypeScript】(它认为是`object[]`)实际上是我们的接口类型之一:

```
console.log(`${result.count} results`);

switch (userArguments.resource) {
  case "films": {
    logFilms(result.results as Film[]);
    break;
  }
  case "people": {
    logPeople(result.results as Person[]);
    break;
  }
  case "planets": {
    logPlanets(result.results as Planet[]);
    break;
  }
}

function logFilms(films: Film[]): void { ... }
function logPeople(people: Person[]): void { ... }
function logPlanets(planets: Planet[]): void { ... } 
```

一旦我们用一些日志来填充这些函数，我们的 CLI 工具就完成了！

```
function logFilms(films: Film[]): void {
  films.forEach((film) => {
    console.log(film.title);
    console.log(`=> Directed by ${film.director}`);
    console.log(`=> Released on ${film.release_date}`);
  });
}
function logPeople(people: Person[]): void {
  people.forEach((person) => {
    console.log(person.name);
    console.log(`=> Height: ${person.height}`);
    console.log(`=> Mass: ${person.mass}`);
  });
}
function logPlanets(planets: Planet[]): void {
  planets.forEach((planet) => {
    console.log(planet.name);
    console.log(`=> Terrain: ${planet.terrain}`);
    console.log(`=> Population: ${planet.population}`);
  });
} 
```

让我们最终解决它输出`1 results`而不是`1 result`的事实:

```
function pluralise(singular: string, plural: string, count: number): string {
  return `${count}  ${count === 1 ? singular : plural}`;
}

console.log(pluralise("result", "results", result.count)); 
```

现在我们的 CLI 输出看起来不错！

```
$ deno run --allow-net=swapi.dev index.ts --resource planets --query tat
Check file:///home/jack/git/deno-star-wars-api/index.ts
1 result
Tatooine
=> Terrain:      desert
=> Population:   200000 
```

## 整理

现在，我们所有的代码都是一个大的`index.ts`文件。让我们创建一个`api.ts`文件，并将大部分 API 逻辑移入其中。

不要忘记将`export`添加到该文件中所有类型、接口和函数的前面，因为我们将需要在`index.ts`中导入它们:

```
// api.ts
export type StarWarsResource = "films" | "people" | "planets";

export interface Person {
  name: string;
  films: string[];
  height: string;
  mass: string;
  homeworld: string;
}

export interface Film {
  title: string;
  episode_id: number;
  director: string;
  release_date: string;
}

export interface Planet {
  name: string;
  terrain: string;
  population: string;
}

export async function queryStarWarsAPI(
  resource: StarWarsResource,
  query: string,
): Promise<{
  count: number;
  results: object[];
}> {
  const url = `https://swapi.dev/api/${resource}/?search=${query}`;
  const response = await fetch(url);
  const data = await response.json();
  return data;
} 
```

然后我们可以从`index.ts`导入它们:

```
import {
  Film,
  Person,
  Planet,
  queryStarWarsAPI,
  StarWarsResource,
} from "./api.ts" 
```

现在我们的`index.ts`看起来干净多了，我们已经将 API 的所有细节移到了一个单独的模块中。

## 分配

假设我们现在想把这个脚本分发给一个朋友。我们可以与他们共享整个存储库，但是如果他们只想运行脚本，那就太过分了。

我们可以使用`deno bundle`将所有代码捆绑到一个 JavaScript 文件中，并安装所有的依赖项。这样，共享脚本就是共享一个文件:

```
$ deno bundle index.ts out.js 
```

我们可以把这个脚本传递给`deno.run`，就像之前一样。现在的区别是 Deno 不需要做任何类型检查，也不需要安装任何依赖项，因为这些都已经为我们放到了`out.js`中。这意味着运行这样的捆绑脚本可能比从 TypeScript 源代码运行更快:

```
$ deno run --allow-net=swapi.dev out.js --resource films --query phantom
1 result
The Phantom Menace
=> Directed by George Lucas
=> Released on 1999-05-19 
```

另一个选项是使用 [`deno compile`](https://deno.land/manual@v1.7.4/tools/compiler) 生成一个可执行文件。请注意，在撰写本文时，这被认为是实验性的，所以要小心行事，但是我希望将它包括在内，因为我希望它在未来会变得稳定和更常见。

我们可以运行`deno compile --unstable --allow-net=swapi.dev index.ts`来请求 Deno 为我们构建一个自包含的可执行文件。`--unstable`标志是必需的，因为这个特性是实验性的，尽管将来不应该是。它的伟大之处在于，我们在编译时传递安全标志——在我们的例子中，允许访问星球大战 API。这意味着，如果我们将这个可执行文件交给用户，他们不需要知道如何配置标志:

```
$ deno compile --unstable --allow-net=swapi.dev index.ts
Check file:///home/jack/git/deno-star-wars-api/index.ts
Bundle file:///home/jack/git/deno-star-wars-api/index.ts
Compile file:///home/jack/git/deno-star-wars-api/index.ts
Emit deno-star-wars-api 
```

我们现在可以直接运行这个可执行文件:

```
$ ./deno-star-wars-api --resource people --query jar jar
1 result
Jar Jar Binks
=> Height: 196
=> Mass:   66 
```

我怀疑在未来这将成为发布用 Deno 编写的命令行工具的主要方式，希望不久它就会失去实验状态。

## 结论

在本文中，通过构建一个 CLI 工具，我们学习了如何使用 Deno 从第三方 API 获取数据并显示结果。我们看到了 Deno 如何实现对我们习惯在浏览器中使用的相同 Fetch API 的支持，如何将`fetch`构建到 Deno 标准库中，以及如何在程序的顶层使用`await`,而不必将所有东西都包装在 IFFE 中。

我希望你会同意我的观点，关于 Deno 有许多值得喜爱的地方。它提供了一个现成的非常高效的环境，包括 TypeScript 和格式化程序。没有包管理器的开销是很好的，特别是在编写小的助手工具时，编译成一个可执行文件的能力意味着与你的同事和朋友分享这些工具非常容易。

## 分享这篇文章