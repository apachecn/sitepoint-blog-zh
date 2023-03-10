# 午休时重构代码:Codemods 入门

> 原文：<https://www.sitepoint.com/getting-started-with-codemods/>

![DeLorean in a park at night. Use codemods to go back and change your code.](img/f16ecd82711454ff19252dd4e3be8100.png)

对于任何开发人员来说，维护代码库都是令人沮丧的经历，尤其是 JavaScript 代码库。随着不断变化的标准、语法和第三方包的突破性变化，可能很难跟上。

近年来，JavaScript 领域已经发生了翻天覆地的变化。核心 JavaScript 语言的进步意味着即使是最简单的变量声明任务也发生了变化。 [ES6](https://www.sitepoint.com/premium/screencasts/a-look-into-es2016) 引入了 let 和 const，[箭头函数](https://www.sitepoint.com/javascript-arrow-functions/)，以及更多的核心变化，每一项都为开发者及其应用带来了改进和好处。

开发人员生产和维护经得起时间考验的代码的压力越来越大。本文将向您展示如何使用 codemods 和 JSCodeshift 工具自动化大规模重构任务，例如，允许您轻松地更新代码以利用新的语言特性。

## codedom

Codemod 是脸书开发的一个工具，用来帮助重构大规模代码库。它使开发人员能够在短时间内重构大型代码库。在某些情况下，开发人员可能会使用 IDE 来执行类或变量名的重构，但是，这通常一次只限于一个文件。开发人员重构工具包中的下一个工具是全局查找和替换。在许多情况下，这可以通过使用复杂的正则表达式来实现。许多情况不适合这种方法；例如，当有多个实现需要更改时。

Codemod 是一个 Python 工具，它接受许多参数，包括您希望匹配的表达式和替换。

```
codemod -m -d /code/myAwesomeSite/pages --extensions php,html \
    '<font *color="?(.*?)"?>(.*?)</font>' \
    '<span style="color: \1;">\2</span>' 
```

在上面的例子中，我们用 span 替换了标签`<font>`的用法，并内联了颜色样式。前两个参数是表示多行匹配的标志(-m)和开始处理的目录(-d /code/myAwesomeSite/pages)。我们还可以限制处理的扩展名(–扩展名 php、html)。然后我们提供匹配表达式和替换。如果没有提供替换，我们将在运行时得到提示。该工具可以工作，但它与现有的正则表达式匹配工具非常相似。

## JSCodeshift

JSCodeshift 是重构工具包的下一步。也是由脸书开发的，它是一个跨多个文件运行*代码模块*的工具。作为一个节点模块，JSCodeshift 提供了一个干净易用的 API，并在引擎盖下使用了[重铸](https://github.com/benjamn/recast)。Recast 是一个 ast 到 AST(抽象语法树)的转换工具。

### 重铸

[Recast](https://github.com/benjamn/recast) 是一个节点模块，它公开了一个解析和重印 JavaScript 代码的接口。它可以解析字符串格式的代码，并由此生成一个遵循 AST 结构的对象。这允许我们检查代码的模式，比如函数声明。

```
var recast = require("recast");

var code = [
    "function add(a, b) {",
    "  return a + b",
    "}"
].join("\n");

var ast = recast.parse(code);
console.log(ast);
//output
{
    "program": {
        "type": "Program",
        "body": [
            {
                "type": "FunctionDeclaration",
                "id": {
                    "type": "Identifier",
                    "name": "add",
                    "loc": {
                        "start": {
                            "line": 1,
                            "column": 9
                        },
                        "end": {
                            "line": 1,
                            "column": 12
                        },
                        "lines": {},
                        "indent": 0
                    }
                },
        ........... 
```

从上面的例子中我们可以看到，我们为一个将两个数相加的函数传入了代码字符串。当我们解析和记录对象时，我们可以看到 AST。我们看到了`FunctionDeclaration`和函数名等等。因为这只是一个 JavaScript 对象，所以我们可以根据需要修改它。然后我们可以触发 print 函数来返回更新后的代码字符串。

### 抽象语法树

如前所述，Recast 从我们的代码字符串构建一个 ast。AST 是源代码抽象语法的树形表示。树的每个节点代表源代码中的一个构造，并且该节点提供关于该构造的重要信息。ASTExplorer 是一个基于浏览器的工具，可以帮助解析和理解你的代码树。

使用 ASTExplorer，我们可以查看一个简单代码示例的 AST。从我们的代码开始，我们将声明一个名为 foo 的常量，它将等于字符串“bar”。

```
const foo = 'bar'; 
```

这导致以下 AST:

![Screenshot of the resulting AST](img/5f969b4a25c6acaf614018ca755d69cd.png)

我们可以在 body 数组下看到 VariableDeclaration，它包含我们的 const。所有变量声明都有一个 id 属性，包含我们的重要信息，如名称等。如果我们正在构建一个 codemod 来重命名所有的`foo`实例，我们可以使用这个 name 属性并遍历所有的实例来更改名称。

### 安装和使用

使用上面的工具和技术，我们现在可以充分利用 JSCodeshift。由于 JSCodeshift 是一个节点模块，我们可以在项目或全局级别安装它。

```
npm install -g jscodeshift 
```

一旦安装，我们可以使用 JSCodeshift 现有的 codemods。我们必须提供一些参数来告诉 JSCodeshift 我们想要实现什么。基本语法是用我们希望转换的一个或多个文件的路径调用`jscodeshift`。基本参数是转换的位置(-t)。这可以是本地文件，也可以是 codemod 文件的 URL。transform 参数默认在当前目录中查找一个`transform.js`文件。

其他有用的参数包括模拟运行(-d)，它将应用转换但不更新文件，以及详细运行(-v)，它将记录有关转换过程的所有信息。转换是 codemods，导出函数的简单 JavaScript 模块。该函数接受以下参数:

*   文件信息
*   美国石油学会(American Petroleum Institute)
*   选择

FileInfo 保存当前正在处理的文件的所有信息，包括路径和源。Api 是一个对象，它提供对 JSCodeshift 辅助函数的访问，比如 findVariableDeclarators 和 renameTo。我们的最后一个参数是 options，它允许我们将选项从 CLI 传递到 codemod。例如，如果我们在一个部署服务器上运行，并且想要将代码版本添加到所有文件中，我们可以通过 CLI `jscodeshift -t myTransforms fileA fileB --codeVersion=1.2`传递它。选项将包含`{codeVersion: '1.2'}`。

在我们公开的函数中，我们必须将转换后的代码作为字符串返回。例如，如果我们有代码字符串`const foo = 'bar'`,我们想转换它，用 const bar 替换 const foo，我们的 codemod 应该是这样的:

```
export default function transformer(file, api) {
  const j = api.jscodeshift;

  return j(file.source)
    .find(j.Identifier)
    .forEach(path => {
      j(path).replaceWith(
        j.identifier('bar')
      );
    })
    .toSource();
} 
```

如您所见，我们将许多函数链接在一起，并在最后调用`toSource()`来生成转换后的代码字符串。

返回代码时，我们必须遵循一些规则。返回不同于输入的字符串将触发成功的转换。如果字符串与输入相同，则转换将会失败，如果没有返回任何内容，则转换将是不必要的。JSCodeshift 然后在处理转换的统计数据时使用这些结果。

### 现有代码模块

在大多数情况下，开发人员不需要编写自己的 codemod。许多常见的重构操作已经被转化为代码模块。

一些例子包括 [js-codemod no-vars](https://github.com/cpojer/js-codemod/#no-vars) ，它将根据变量的用法将`var`的所有实例转换成`let`或`const`。例如，如果变量在以后被重新分配，则为 let，如果变量从未被重新分配，则为 const。

js-codemod template-literals 将用模板文字替换字符串连接的实例，例如

```
const sayHello = 'Hi my name is ' + name;
//after transform
const sayHello = `Hi my name is ${name}`; 
```

### codemods 是如何编写的

我们可以从上面的无变量 codemod 开始，分解代码，看看一个复杂的 codemod 是如何工作的。

```
const updatedAnything = root.find(j.VariableDeclaration).filter(
            dec => dec.value.kind === 'var'
        ).filter(declaration => {
            return declaration.value.declarations.every(declarator => {
                return !isTruelyVar(declaration, declarator);
            });
        }).forEach(declaration => {
            const forLoopWithoutInit = isForLoopDeclarationWithoutInit(declaration);
            if (
                declaration.value.declarations.some(declarator => {
                    return (!declarator.init && !forLoopWithoutInit) || isMutated(declaration, declarator);
                })
            ) {
                declaration.value.kind = 'let';
            } else {
                declaration.value.kind = 'const';
            }
        }).size() !== 0;
    return updatedAnything ? root.toSource() : null; 
```

上面的代码是 no-vars codemod 的核心。首先，对所有变量声明运行过滤器，包括 var、let 和 const。过滤器只返回 var 声明。这将调用自定义函数`isTruelyVar`。这用于确定 var 的性质(例如，var 是在闭包内还是声明了两次，或者是可能被提升的函数声明)。这将决定在 var 上进行转换是否安全。对于每个通过`isTruelyVar`过滤器的变量，它们都在 For each 循环中被处理。

在循环内部，对 var 进行检查，如果 var 在循环内部，例如

```
for(var i = 0; i < 10; i++) {
    doSomething();
} 
```

要检测变量是否在循环中，可以检查父类型。

```
const isForLoopDeclarationWithoutInit = declaration => {
        const parentType = declaration.parentPath.value.type;
        return parentType === 'ForOfStatement' || parentType === 'ForInStatement';
    }; 
```

如果 var 在一个循环中并且没有变异，那么它可以被改变为 const。可以通过过滤 var 节点 AssignmentExpression 和 UpdateExpression 来检查突变。分配表达式将显示何时何地将 var 分配给

```
var foo = 'bar'; 
```

UpdateExpression 将显示 var 更新的时间和地点，例如

```
var foo = 'bar';
foo = 'Foo Bar'; //Updated 
```

如果变量在一个有突变的循环中，那么就使用 let，因为 let 在被实例化后可以被重新分配。codemod 中的最后一行检查是否有任何更新，例如任何变量的改变。如果是，则返回新的文件源，否则返回 null，这告诉 JSCodeshift 没有进行任何处理。codemod 的完整源代码可以在[这里](https://github.com/cpojer/js-codemod/blob/master/transforms/no-vars.js)找到。

脸书团队还添加了许多 codemods，用于更新 React 语法和处理 React API 的更改。一些代码模块包括 [react-codemod sort-comp](https://github.com/reactjs/react-codemod#sort-comp) ，它对 react 生命周期方法进行排序，以匹配 [ESlint sort-comp 规则](https://github.com/yannickcr/eslint-plugin-react/blob/master/docs/rules/sort-comp.md)。

最近和最流行的 React codemod 是[React-prop types-to-prop-types](https://github.com/reactjs/react-codemod#react-proptypes-to-prop-types)，它有助于最近从核心 React 团队到 move React 的变化。PropTypes 到它自己的节点模块中。这意味着从 React v16 开始，如果开发者希望继续在组件中使用 propTypes，他们将需要安装 [prop-types](https://www.npmjs.com/package/prop-types) 。这是 codemod 用例的一个很好的例子。使用 PropTypes 的方法并不是一成不变的。

以下都是有效的:

从默认导入导入 React 和访问 PropTypes:

```
import React from 'react';

class HelloWorld extends React.Component {

    static propTypes = {
        name: React.PropTypes.string,
    }
    ..... 
```

导入 React 和 PropTypes 的命名导入:

```
import React, { PropTypes, Component } from 'react';

class HelloWorld extends Component {

    static propTypes = {
        name: PropTypes.string,
    }
    ..... 
```

导入 React 和 PropTypes 的命名导入，但在无状态组件上声明 PropTypes:

```
import React, { PropTypes } from 'react';

const HelloWorld = ({name}) => {
    .....
}

HelloWorld.propTypes = {
    name: PropTypes.string
}; 
```

有三种方法来实现同一个解决方案，使得执行正则表达式来查找和替换变得特别困难。如果我们的代码库中有以上三个，我们可以通过运行以下代码轻松升级到新的 PropTypes 模式:

```
jscodeshift src/ -t transforms/proptypes.js 
```

在本例中，我们从 react-codemods repo 中提取了 PropTypes codemod，并将其添加到项目的 transforms 目录中。codemod 会将`import PropTypes from 'prop-types';`添加到每个文件中，并用`PropTypes`替换`React.PropTypes`的任何实例。

## 结论

脸书在代码维护方面处于领先地位，使开发人员能够根据不断变化的 API 和代码实践进行调整。JavaScript 疲劳已经成为一个很大的问题，正如我所展示的，拥有可以帮助减轻更新现有代码的压力的工具可以帮助减轻这种疲劳。

在依赖数据库的服务器端开发领域，开发人员定期创建迁移脚本来维护数据库支持，并确保用户拥有最新版本的数据库。当主要版本发布时，JavaScript 库维护者可以提供 codemod 作为*迁移脚本*,具有突破性变化的 codemod 可以处理升级过程。

这将适合现有的迁移过程，因为可以运行 npm install 的脚本。让 codemod 在安装/升级时自动运行可以加速升级，并为消费者提供更多的信心。将这一点包含到发布过程中不仅对消费者有益，还可以减少维护人员在更新示例和指南时的开销。

在本文中，我们看到了 codemods 和 JSCodeshift 的强大特性，以及它们如何快速更新复杂的代码。从 Codemod 工具开始，到 ASTExplorer 和 JSCodeshift 等工具，我们现在可以构建 code mod 来满足自己的需求。利用已经广泛存在的预制 codemods，开发人员可以与大众同步前进。

你用过 codemods 吗？你的工具箱里有什么？对于 codemods 来说，还有哪些重构是很有用的？请在评论中告诉我！

*这篇文章由 [Graham Cox](https://www.sitepoint.com/author/gcox) 和 [Michael Wanyoike](https://www.sitepoint.com/author/mwanyoike/) 进行了同行评审。感谢 SitePoint 的所有同行评审员使 SitePoint 的内容尽可能做到最好！*

## 分享这篇文章