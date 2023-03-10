# Rust 教程:JavaScript 开发人员 Rust 入门

> 原文：<https://www.sitepoint.com/rust-tutorial-introduction-javascript-devs/>

Rust 是一种编程语言，起源于 2010 年的 Mozilla Research。今天，它被所有的大公司使用。

亚马逊和 T2、微软都认可它是他们系统中 C/C++的最佳替代品。但是铁锈并不止于此。像 [Figma](https://www.figma.com/blog/webassembly-cut-figmas-load-time-by-3x/) 和 [Discord](https://blog.discord.com/why-discord-is-switching-from-go-to-rust-a190bbca2b1f) 这样的公司现在也在他们的客户端应用中使用 Rust。

这篇 Rust 教程旨在简要介绍 Rust，如何在浏览器中使用它，以及何时应该考虑使用它。我将从比较 Rust 和 JavaScript 开始，然后带您完成在浏览器中启动和运行 Rust 的步骤。最后，我将对我的使用 Rust 和 JavaScript 的 COVID simulator web 应用程序进行快速性能评估。

## 简而言之就是生锈

Rust 在概念上与 JavaScript 非常不同。但也有相似之处需要指出。让我们看看硬币的两面。

### 类似

两种语言都有一个现代的包管理系统。JavaScript 有 npm，Rust 有 Cargo。Rust 没有使用`package.json`，而是使用`Cargo.toml`进行依赖管理。要创建一个新项目，使用`cargo init`，要运行它，使用`cargo run`。不会太陌生吧？

Rust 中有很多你已经从 JavaScript 中了解到的很酷的特性，只是语法略有不同。以这个常见的 JavaScript 模式为例，将闭包应用于数组中的每个元素:

```
let staff = [
   {name: "George", money: 0},
   {name: "Lea", money: 500000},
];
let salary = 1000;
staff.forEach( (employee) => { employee.money += salary; } ); 
```

在《铁锈》中，我们会这样写:

```
let salary = 1000;
staff.iter_mut().for_each( 
    |employee| { employee.money += salary; }
); 
```

诚然，习惯这种语法需要时间，用竖线(`|`)代替括号。
但是在克服了最初的尴尬之后，我发现读起来比另一组括号更清晰。

作为另一个例子，这里有一个 JavaScript 中的对象析构:

```
let point = { x: 5, y: 10 };
let {x,y} = point; 
```

类似地，在铁锈中:

```
let point = Point { x: 5, y: 10 };
let Point { x, y } = point; 
```

主要区别在于，在 Rust 中，我们必须指定类型(`Point`)。更一般地说，Rust 需要在编译时知道所有类型。但是与大多数其他编译语言相比，编译器尽可能自己推断类型。

为了进一步解释这一点，下面是在 C++和许多其他语言中有效的代码。每个变量都需要显式的类型声明:

```
int a = 5;
float b = 0.5;
float c = 1.5 * a; 
```

在 JavaScript 和 Rust 中，这段代码都是有效的:

```
let a = 5;
let b = 0.5;
let c = 1.5 * a; 
```

共享的功能不胜枚举:

*   Rust 有`async` + `await`语法。
*   数组的创建可以像`let array = [1,2,3]`一样简单。
*   代码被组织在具有显式导入和导出的模块中。
*   字符串文字用 Unicode 编码，处理特殊字符没有问题。

我还可以继续列举下去，但是我想我的观点现在已经很清楚了:Rust 有一系列丰富的特性，这些特性也在现代 JavaScript 中使用。

### 差异

Rust 是一种编译语言，这意味着没有运行时来执行 Rust 代码。一个应用程序只有在编译器(`rustc`)施展了魔法之后才能运行。这种方法的好处通常是更好的性能。

幸运的是，Cargo 负责为我们调用编译器。有了 webpack，我们还可以将`cargo`隐藏在`npm run build`后面。有了这个指南，一旦为项目设置了 Rust，就可以保留 web 开发人员的正常工作流程。

Rust 是一种强类型语言，这意味着所有类型在编译时必须匹配。例如，不能用错误类型或错误数量的参数调用函数。在运行时遇到错误之前，编译器会为您捕捉到错误。比较明显的是 TypeScript。如果你喜欢 TypeScript，那么你可能会喜欢 Rust。

但是不要担心:如果你不喜欢 TypeScript，Rust 可能仍然适合你。Rust 是最近几年从头开始构建的，考虑到了人类在过去几十年中对编程语言设计的所有了解。结果是一种令人耳目一新的干净的语言。

Rust 中的模式匹配是我最喜欢的功能。其他语言有`switch`和`case`来避免像这样的长链:

```
if ( x == 1) { 
    // ... 
} else if ( x == 2 ) {
    // ...
}
else if ( x == 3 || x == 4 ) {
    // ...
} // ... 
```

Rust 使用更优雅的`match`工作方式如下:

```
match x {
    1 => { /* Do something if x == 1 */},
    2 => { /* Do something if x == 2 */},
    3 | 4 => { /* Do something if x == 3 || x == 4 */},
    5...10 => { /* Do something if x >= 5 && x <= 10 */},
    _ => { /* Catch all other cases */ }
} 
```

我认为这非常棒，我希望 JavaScript 开发人员也能欣赏这种语法扩展。

不幸的是，我们还得谈谈铁锈的阴暗面。直截了当地说，使用严格的类型系统有时会感觉非常麻烦。如果你认为 C++或 Java 的类型系统是严格的，那么准备好迎接 Rust 的艰难旅程吧。

就我个人而言，我喜欢铁锈那部分。我依赖于类型系统的严格性，因此可以关闭我大脑的一部分——每当我发现自己在写 JavaScript 时，这部分就会剧烈地刺痛。但我明白，对于初学者来说，一直和编译器较劲可能会很烦。我们将在这个 Rust 教程的后面看到一些。

## 你好，铁锈

现在，让我们在浏览器中运行 Rust 的`hello world`。我们首先要确保安装了所有必要的工具。

### 工具

1.  使用 rustup 安装 Cargo + rustc。Rustup 是安装 Rust 的推荐方式。它将为 Rust 的最新稳定版本安装编译器(rustc)和包管理器(Cargo)。它还可以管理测试版和夜间版本，但是对于这个例子来说这不是必需的。
    *   通过在终端中键入`cargo --version`来检查安装。您应该会看到类似于`cargo 1.48.0 (65cbdd2dc 2020-10-14)`的内容。
    *   还要检查生锈情况:`rustup --version`应该产生`rustup 1.23.0 (00924c9ba 2020-11-27)`。
2.  [安装 wasm-pack](https://rustwasm.github.io/wasm-pack/installer/#) 。这是将编译器与 npm 集成在一起。
    *   通过键入`wasm-pack --version`来检查安装，应该会得到类似于`wasm-pack 0.9.1`的结果。
3.  我们还需要节点和 npm。我们有一篇完整的[文章](https://www.sitepoint.com/quick-tip-multiple-versions-node-nvm/)解释安装这两个软件的最佳方式。

### 编写 Rust 代码

现在一切都安装好了，让我们创建项目。最终代码也可以在 GitHub 库中找到[。我们从一个可以编译成 npm 包的 Rust 项目开始。导入该包的 JavaScript 代码将随后出现。](https://github.com/sitepoint-editors/rust-wasm-hello-world)

要创建名为`hello-world`的 Rust 项目，请使用`cargo init --lib hello-world`。这将创建一个新目录，并生成 Rust 库所需的所有文件:

```
├──hello-world
    ├── Cargo.toml
    ├── src
        ├── lib.rs 
```

Rust 代码会放在`lib.rs`里面。在此之前，我们必须调整`Cargo.toml`。它使用 [TOML](https://github.com/toml-lang/toml) 定义依赖关系和其他包信息。对于浏览器中的 hello world，在您的`Cargo.toml`中添加以下行(例如，在文件的末尾):

```
[lib]
crate-type = ["cdylib"] 
```

这告诉编译器在 C 兼容模式下创建一个库。在我们的例子中，我们显然没有使用 C。C-compatible 仅仅意味着*而不是 Rust-specific* ，这是我们从 JavaScript 使用库所需要的。

我们还需要两个外部库。将它们作为单独的行添加到*依赖关系*部分:

```
[dependencies]
wasm-bindgen = "0.2.68"
web-sys = {version = "0.3.45", features = ["console"]} 
```

这些依赖项来自于 [crates.io](https://crates.io/) ，Cargo 使用的默认包存储库。

[wasm-bindgen](https://crates.io/crates/wasm-bindgen) 是创建一个入口点所必需的，我们稍后可以从 JavaScript 调用它。(你可以在这里找到[的完整文档。)值`"0.2.68"`指定版本。](https://rustwasm.github.io/docs/wasm-bindgen/)

[web-sys](https://crates.io/crates/web-sys) 包含所有 Web APIs 的 Rust 绑定。它会让我们进入浏览器控制台。注意，我们必须明确选择*控制台*功能。我们最终的二进制文件将只包含像这样选择的 Web API 绑定。

接下来是实际的代码，在`lib.rs`里面。可以删除自动生成的单元测试。只需用以下代码替换文件的内容:

```
use wasm_bindgen::prelude::*;
use web_sys::console;

#[wasm_bindgen]
pub fn hello_world() {
    console::log_1("Hello world");
} 
```

顶部的`use`语句用于从其他模块导入项目。(这类似于 JavaScript 中的`import`。)

`pub fn hello_world() { ... }`声明一个函数。`pub`修饰符是“public”的缩写，其作用类似于 JavaScript 中的`export`。注释`#[wasm_bindgen]`是针对 Rust 编译到 [WebAssembly (Wasm)](https://webassembly.org/) 的。我们在这里需要它来确保编译器向 JavaScript 公开一个包装函数。

在函数体中，“Hello world”被打印到控制台。Rust 中的`console::log_1()`是调用`console.log()`的包装器。(此处阅读更多[。)](https://rustwasm.github.io/wasm-bindgen/api/web_sys/console/fn.log_1.html)

注意到函数调用时的`_1`后缀了吗？这是因为 JavaScript 允许可变数量的参数，而 Rust 不允许。为了解决这个问题，`wasm_bindgen`为每个参数生成一个函数。是的，那会很快变得难看！但这很有效。在 [web-sys 文档](https://rustwasm.github.io/wasm-bindgen/api/web_sys/console/index.html)中提供了可以从 Rust 内部调用的控制台功能的完整列表。

我们现在应该一切就绪。尝试用下面的命令编译它。这会下载所有依赖项并编译项目。第一次可能需要一段时间:

```
cd hello-world
wasm-pack build 
```

哼！Rust 编译器对我们不满意:

```
error[E0308]: mismatched types
 --> src\lib.rs:6:20
  |
6 |     console::log_1("Hello world");
  |                    ^^^^^^^^^^^^^ expected struct `JsValue`, found `str`
  |
  = note: expected reference `&JsValue`
             found reference `&'static str 
```

*注意:如果您看到一个不同的错误(`error: linking with cc failed: exit code: 1`)，并且您在 Linux 上，那么您缺少交叉编译依赖。`sudo apt install gcc-multilib`应该可以解决这个问题。*

正如我前面提到的，编译器是严格的。当它期望引用一个`JsValue`作为函数的参数时，它不会接受静态字符串。显式转换对于满足编译器是必要的。

```
 console::log_1(&"Hello world".into()); 
```

方法 [into()](https://doc.rust-lang.org/std/convert/trait.Into.html) 将把一个值转换成另一个值。Rust 编译器足够聪明，可以推迟转换中涉及的类型，因为函数签名只留下一种可能性。在这种情况下，它将转换为`JsValue`，这是 JavaScript 管理的值的包装器类型。然后，我们还必须添加`&`来通过引用而不是通过值传递它，否则编译器会再次抱怨。

尝试再次运行`wasm-pack build`。如果一切顺利，打印的最后一行应该是这样的:

```
[INFO]: :-) Your wasm pkg is ready to publish at /home/username/intro-to-rust/hello-world/pkg. 
```

如果你已经做到了这一步，你现在可以手动编译 Rust 了。接下来，我们将把它与 npm 和 webpack 集成在一起，它们会自动完成这项工作。

### JavaScript 集成

对于这个例子，我决定将`package.json`放在`hello-world`目录中。我们也可以为 Rust 项目和 JavaScript 项目使用不同的目录。这是口味的问题。

下面是我的`package.json`文件。最简单的方法是复制它并运行`npm install`。或者运行`npm init`，只复制开发依赖项:

```
{
    "name": "hello-world",
    "version": "1.0.0",
    "description": "Hello world app for Rust in the browser.",
    "main": "index.js",
    "scripts": {
        "build": "webpack",
        "serve": "webpack serve"
    },
    "author": "Jakob Meier <inbox@jakobmeier.ch>",
    "license": "(MIT OR Apache-2.0)",
    "devDependencies": {
        "@wasm-tool/wasm-pack-plugin": "~1.3.1",
        "@webpack-cli/serve": "^1.1.0",
        "css-loader": "^5.0.1",
        "style-loader": "^2.0.0",
        "webpack": "~5.8.0",
        "webpack-cli": "~4.2.0",
        "webpack-dev-server": "~3.11.0"
    }
} 
```

如你所见，我们使用的是 webpack 5。Wasm-pack 也适用于旧版本的 webpack，甚至不需要捆绑器。但是每个设置的工作原理都有所不同。我建议你在遵循这个 Rust 教程时使用完全相同的版本。

另一个重要的依赖是`wasm-pack-plugin`。这是一个专门加载用 wasm-pack 构建的 Rust 包的 webpack 插件。

接下来，我们还需要创建`webpack.config.js`文件来配置 webpack。它应该是这样的:

```
const path = require('path');
const webpack = require('webpack');
const WasmPackPlugin = require("@wasm-tool/wasm-pack-plugin");

module.exports = {
    entry: './src/index.js',
    output: {
        path: path.resolve(__dirname, 'dist'),
        filename: 'index.js',
    },
    plugins: [
        new WasmPackPlugin({
            crateDirectory: path.resolve(__dirname, ".")
        }),
    ],
    devServer: {
        contentBase: "./src",
        hot: true,
    },
    module: {
        rules: [{
            test: /\.css$/i,
            use: ["style-loader", "css-loader"],
        }, ]
    },
    experiments: {
        syncWebAssembly: true,
    },
}; 
```

所有路径都被配置为并排包含 Rust 代码和 JavaScript 代码。`index.js`将在`lib.rs`旁边的`src`文件夹中。如果您喜欢不同的设置，请随意调整。

你还会注意到我们使用了 [webpack 实验](https://webpack.js.org/configuration/experiments/)，这是 webpack 5 中引入的一个新选项。确保`syncWebAssembly`设置为真。

最后，我们必须创建 JavaScript 入口点，`src/index.js`:

```
import("../pkg").catch(e => console.error("Failed loading Wasm module:", e)).then(
    rust =>
        rust.hello_world()
); 
```

我们必须异步加载 Rust 模块。调用`rust.hello_world()`将调用一个生成的包装函数，该包装函数又调用在`lib.rs`中定义的 Rust 函数`hello_world`。

运行`npm run serve`现在应该可以编译所有东西并启动一个开发服务器。我们没有定义 HTML 文件，所以页面上不显示任何内容。你可能还需要手动进入[http://localhost:8080/index](http://localhost:8080/index)，因为 [http://localhost:8080](http://localhost:8080) 只是列出文件而不执行任何代码。

有了空白页后，打开开发人员控制台。应该会有一个 *Hello World* 的日志条目。

好吧，对于一个简单的 hello world 来说，这是相当多的工作。但是现在一切就绪，我们可以轻松地扩展 Rust 代码，而不用担心这些。保存对`lib.rs`的更改后，您应该会在浏览器中自动看到重新编译和实时更新，就像 JavaScript 一样！

## 何时使用铁锈

Rust 不是 JavaScript 的通用替代品。它只能通过 Wasm 在浏览器中运行，这大大限制了它的实用性。即使你真的想用 Rust 替换几乎所有的 JavaScript 代码，这也不是一个好主意，也不是 Wasm 的初衷。例如，Rust 并不适合与网站的用户界面进行交互。

我认为 Rust + Wasm 是一个额外的选项，可以用来更有效地运行 CPU 繁重的工作负载。以较大的下载量为代价，Wasm 避免了 JavaScript 代码面临的解析和编译开销。这一点，再加上编译器的强大优化，可能会带来更好的性能。这通常是公司为特定项目挑选铁锈的原因。选择 Rust 的另一个原因可能是语言偏好。但这是一个完全不同的讨论，我不会在这里进入。

## 电晕感染模拟器

是时候创建一个真正的应用程序来揭示浏览器中 Rust 的真正威力了！下面是一个现场 CodePen 演示。如果你想看完整尺寸的，点击这里。代码为的[库也是可用的。](https://github.com/sitepoint-editors/covid-sim)

参见 [CodePen](https://codepen.io) 上 site point([@ site point](https://codepen.io/SitePoint))
的笔 [电晕模拟器铁锈演示 App](https://codepen.io/SitePoint/pen/GRjNmjP) 。