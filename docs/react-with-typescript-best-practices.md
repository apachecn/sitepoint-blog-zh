# 使用 TypeScript 作出反应:最佳实践

> 原文：<https://www.sitepoint.com/react-with-typescript-best-practices/>

React 和 TypeScript 是当今许多开发人员使用的两项了不起的技术。知道如何做事情会变得棘手，有时很难找到正确的答案。别担心。我们将最佳实践与示例放在一起，以澄清您可能有的任何疑问。

让我们开始吧！

## React 和 TypeScript 如何协同工作

在开始之前，让我们回顾一下 React 和 TypeScript 是如何协同工作的。React 是“用于构建用户界面的 JavaScript 库”，而 TypeScript 是“编译成普通 JavaScript 的 JavaScript 的类型化超集。”通过一起使用它们，我们基本上使用 JavaScript 的类型化版本来构建我们的 ui。

您可能一起使用它们的原因是为了让您的 UI 获得静态类型语言(TypeScript)的好处。这意味着更多的安全和更少的错误运送到前端。

### TypeScript 会编译我的 React 代码吗？

回顾一下一个常见问题总是有好处的，那就是 TypeScript 是否编译了您的 React 代码。TypeScript 的工作方式类似于这种交互:

**TS** :“喂，这都是你的 UI 代码吗？”
**反应**:“是啊！”
**TS** :“爽！我要编译一下，确保你没有漏掉什么。”
**反应**:“听起来不错！”

所以答案是肯定的，确实如此！但是稍后，当我们讨论`tsconfig.json`设置时，大多数时候你会想要使用`"noEmit": true`。这意味着 TypeScript **在编译后不会**发出 JavaScript。这是因为通常情况下，我们只是利用 TypeScript 来进行类型检查。

在 CRA 设置中，输出由`react-scripts`处理。我们运行`yarn build`和`react-scripts`捆绑生产的输出。

概括地说，TypeScript 编译 React 代码来对代码进行类型检查。它不发出任何 JavaScript 输出(在大多数情况下)。输出仍然类似于非 TypeScript React 项目。

### TypeScript 可以与 React 和 webpack 一起使用吗？

是的，TypeScript 可以与 React 和 webpack 一起使用。幸运的是，webpack 文档在这方面有一个[指南](https://webpack.js.org/guides/typescript/)。

希望这能给你一个温和的复习，让你了解两者是如何一起工作的。现在，让我们来看看最佳实践！

## 最佳实践

我们已经研究了最常见的问题，并将 React with TypeScript 的最常见用例列表放在一起。这样，您可以在自己的项目中使用这篇文章作为参考。

### 配置

开发中最无趣但最重要的部分之一是配置。我们如何在最短的时间内设置好一切，以提供最大的效率和生产力？我们将讨论项目设置，包括:

*   `tsconfig.json`
*   埃斯林特
*   较美丽
*   VS 代码扩展和设置。

**项目设置**

启动 React/TypeScript 应用程序的最快方法是使用带有 TypeScript 模板的`create-react-app`。您可以通过运行以下命令来实现:

```
npx create-react-app my-app --template typescript 
```

这将为您提供开始使用 TypeScript 编写 React 的最低要求。一些值得注意的差异是:

*   `.tsx`文件扩展名
*   `tsconfig.json`
*   `react-app-env.d.ts`

`tsx`代表“打字稿 [JSX](https://www.sitepoint.com/an-introduction-to-jsx/) ”。`tsconfig.json`是 TypeScript 配置文件，它有一些缺省设置。`react-app-env.d.ts`引用了`react-scripts`的类型，并有助于诸如允许 SVG 导入之类的事情。

**tsconfig.json**

幸运的是，最新的 React/TypeScript 模板为我们生成了`tsconfig.json`。然而，他们添加了最低限度的开始。我们建议您修改您的，以符合下面的一个。我们还添加了注释来解释每个选项的用途:

```
{
  "compilerOptions": {
    "target": "es5", // Specify ECMAScript target version
    "lib": [
      "dom",
      "dom.iterable",
      "esnext"
    ], // List of library files to be included in the compilation
    "allowJs": true, // Allow JavaScript files to be compiled
    "skipLibCheck": true, // Skip type checking of all declaration files
    "esModuleInterop": true, // Disables namespace imports (import * as fs from "fs") and enables CJS/AMD/UMD style imports (import fs from "fs")
    "allowSyntheticDefaultImports": true, // Allow default imports from modules with no default export
    "strict": true, // Enable all strict type checking options
    "forceConsistentCasingInFileNames": true, // Disallow inconsistently-cased references to the same file.
    "module": "esnext", // Specify module code generation
    "moduleResolution": "node", // Resolve modules using Node.js style
    "isolatedModules": true, // Unconditionally emit imports for unresolved files
    "resolveJsonModule": true, // Include modules imported with .json extension
    "noEmit": true, // Do not emit output (meaning do not compile code, only perform type checking)
    "jsx": "react", // Support JSX in .tsx files
    "sourceMap": true, // Generate corrresponding .map file
    "declaration": true, // Generate corresponding .d.ts file
    "noUnusedLocals": true, // Report errors on unused locals
    "noUnusedParameters": true, // Report errors on unused parameters
    "incremental": true, // Enable incremental compilation by reading/writing information from prior compilations to a file on disk
    "noFallthroughCasesInSwitch": true // Report errors for fallthrough cases in switch statement
  },
  "include": [
    "src/**/*" // *** The files TypeScript should type check ***
  ],
  "exclude": ["node_modules", "build"] // *** The files to not type check ***
} 
```

额外的建议来自 [react-typescript-cheatsheet 社区](https://github.com/typescript-cheatsheets/react-typescript-cheatsheet)，解释来自官方 typescript 手册中的[编译器选项文档](https://www.typescriptlang.org/docs/handbook/compiler-options.html)。如果您想了解其他选项及其作用，这是一个非常好的资源。

**ESL/prettier**

为了确保您的代码遵循项目或您的团队的规则，并且风格一致，建议您设置 ESLint 和 Prettier。为了让他们玩得更好，请按照以下步骤进行设置。

1.  安装所需的开发依赖项:

    ```
    yarn add eslint @typescript-eslint/parser @typescript-eslint/eslint-plugin eslint-plugin-react --dev 
    ```

2.  在根目录下创建一个`.eslintrc.js`文件，并添加以下内容:

    ```
    module.exports =  {
      parser:  '@typescript-eslint/parser',  // Specifies the ESLint parser
      extends:  [
        'plugin:react/recommended',  // Uses the recommended rules from @eslint-plugin-react
        'plugin:@typescript-eslint/recommended',  // Uses the recommended rules from @typescript-eslint/eslint-plugin
      ],
      parserOptions:  {
      ecmaVersion:  2018,  // Allows for the parsing of modern ECMAScript features
      sourceType:  'module',  // Allows for the use of imports
      ecmaFeatures:  {
        jsx:  true,  // Allows for the parsing of JSX
      },
      },
      rules:  {
        // Place to specify ESLint rules. Can be used to overwrite rules specified from the extended configs
        // e.g. "@typescript-eslint/explicit-function-return-type": "off",
      },
      settings:  {
        react:  {
          version:  'detect',  // Tells eslint-plugin-react to automatically detect the version of React to use
        },
      },
    }; 
    ```

3.  添加更漂亮的依赖项:

    ```
    yarn add prettier eslint-config-prettier eslint-plugin-prettier --dev 
    ```

4.  在根目录下创建一个`.prettierrc.js`文件，并添加以下内容:

    ```
    module.exports =  {
      semi:  true,
      trailingComma:  'all',
      singleQuote:  true,
      printWidth:  120,
      tabWidth:  4,
    }; 
    ```

5.  更新`.eslintrc.js`文件:

    ```
    module.exports =  {
      parser:  '@typescript-eslint/parser',  // Specifies the ESLint parser
      extends:  [
        'plugin:react/recommended',  // Uses the recommended rules from @eslint-plugin-react
        'plugin:@typescript-eslint/recommended',  // Uses the recommended rules from the @typescript-eslint/eslint-plugin
    +   'prettier/@typescript-eslint',  // Uses eslint-config-prettier to disable ESLint rules from @typescript-eslint/eslint-plugin that would conflict with prettier
    +   'plugin:prettier/recommended',  // Enables eslint-plugin-prettier and displays prettier errors as ESLint errors. Make sure this is always the last configuration in the extends array.
      ],
      parserOptions:  {
      ecmaVersion:  2018,  // Allows for the parsing of modern ECMAScript features
      sourceType:  'module',  // Allows for the use of imports
      ecmaFeatures:  {
        jsx:  true,  // Allows for the parsing of JSX
      },
      },
      rules:  {
        // Place to specify ESLint rules. Can be used to overwrite rules specified from the extended configs
        // e.g. "@typescript-eslint/explicit-function-return-type": "off",
      },
      settings:  {
        react:  {
          version:  'detect',  // Tells eslint-plugin-react to automatically detect the version of React to use
        },
      },
    }; 
    ```

这些建议来自一个名为“[Using ESLint and prettle in a TypeScript Project](https://www.robertcooper.me/using-eslint-and-prettier-in-a-typescript-project)”的社区资源，作者是 Robert Cooper。如果您访问这个资源，您可以阅读更多关于这些规则和配置背后的“为什么”。

**VS 代码扩展和设置**

我们已经添加了 ESLint 和 Prettier，下一步改进 DX 是在保存时自动修复/美化我们的代码。

首先，为 VS 代码安装 [ESLint 扩展](https://marketplace.visualstudio.com/items?itemName=dbaeumer.vscode-eslint)和[beautiful 扩展](https://marketplace.visualstudio.com/items?itemName=esbenp.prettier-vscode)。这将允许 ESLint 与您的编辑器无缝集成。

接下来，通过将以下内容添加到您的`.vscode/settings.json`来更新您的工作区设置:

```
{
    "editor.formatOnSave": true
}
```

这将允许 VS 代码发挥它的魔力，并在你保存时修复你的代码。太美了！

这些建议也来自之前的链接文章“在 TypeScript 项目中使用 ESLint 和 Prettier”，作者是 Robert Cooper。

*注:要了解更多关于`React.FC`的信息，请点击[查看](https://github.com/typescript-cheatsheets/react#function-components)，点击[查看](https://github.com/typescript-cheatsheets/react#useful-react-prop-type-examples)查看`React.ReactNode`。*

### 成分

React 的核心概念之一是组件。这里，我们将引用 React v16.8 中的标准组件，即使用钩子而不是类的组件。

一般来说，基本组件有很多需要关注的地方。让我们看一个例子:

```
import React from 'react'

// Written as a function declaration
function Heading(): React.ReactNode {
  return <h1>My Website Heading</h1>
}

// Written as a function expression
const OtherHeading: React.FC = () => <h1>My Website Heading</h1> 
```

请注意这里的关键区别。在第一个例子中，我们将函数写成一个*函数声明*。我们用`React.Node`注释*返回类型*，因为这就是它返回的内容。相反，第二个例子使用了一个*函数表达式。*因为第二个实例返回一个函数，而不是一个值或表达式，我们用`React.FC`为 React“函数组件”注释*函数类型*。

记住这两者可能会令人困惑。这主要是设计选择的问题。无论你选择在你的项目中使用什么，都要坚持使用。

### 小道具

我们要讨论的下一个核心概念是道具。你可以使用接口或者类型来定义你的属性。让我们看另一个例子:

```
import React from 'react'

interface Props {
  name: string;
  color: string;
}

type OtherProps = {
  name: string;
  color: string;
}

// Notice here we're using the function declaration with the interface Props
function Heading({ name, color }: Props): React.ReactNode {
  return <h1>My Website Heading</h1>
}

// Notice here we're using the function expression with the type OtherProps
const OtherHeading: React.FC<OtherProps> = ({ name, color }) =>
  <h1>My Website Heading</h1> 
```

当涉及到类型或接口时，我们建议遵循由`react-typescript-cheatsheet`社区提供的指南:

*   "创作库或第三方环境类型定义时，总是使用公共 API 定义的接口."
*   "考虑为你的 React 组件属性和状态使用类型，因为它更受约束."

你可以阅读更多的讨论，并在这里看到一个方便的比较类型和接口的表格。

让我们再看一个例子，这样我们可以看到一些更实际的东西:

```
import React from 'react'

type Props = {
  /** color to use for the background */
  color?: string;
  /** standard children prop: accepts any valid React Node */
  children: React.ReactNode;
  /** callback function passed to the onClick handler*/
  onClick: ()  => void;
}

const Button: React.FC<Props> = ({ children, color = 'tomato', onClick }) => {
   return <button style={{ backgroundColor: color }} onClick={onClick}>{children}</button>
} 
```

在这个`<Button />`组件中，我们为我们的道具使用了一个类型。每个道具上面都有一个简短的描述，为其他开发人员提供更多的上下文。名为`color`的道具后的`?`表示它是可选的。`children` prop 接受一个`React.ReactNode`，因为它接受组件的所有有效返回值([在这里阅读更多信息](https://github.com/typescript-cheatsheets/react-typescript-cheatsheet#useful-react-prop-type-examples))。为了说明可选的`color`属性，我们在析构它的时候使用了一个默认值。这个例子应该包括基础知识，并显示你必须为你的道具编写类型，并使用可选值和默认值。

通常，在 React 和 TypeScript 项目中编写道具时，请记住以下几点:

*   始终使用 TSDoc 符号`/** comment */`为你的道具添加描述性注释。
*   无论你使用类型还是接口作为你的组件属性，都要保持一致。
*   道具可选时，适当处理或使用默认值。

### 钩住

幸运的是，在使用钩子时，TypeScript 类型推断工作得很好。这意味着你没什么可担心的。例如，举这个例子:

```
// `value` is inferred as a string
// `setValue` is inferred as (newValue: string) => void
const [value, setValue] = useState('') 
```

TypeScript 推断出由`useState`钩子使用的值。这是 React 和 TypeScript 一起工作的一个领域，它非常漂亮。

在极少数情况下，你需要用一个空值初始化一个钩子，你可以使用一个泛型并传递一个联合来正确地键入你的钩子。请参见此实例:

```
type User = {
  email: string;
  id: string;
}

// the generic is the < >
// the union is the User | null
// together, TypeScript knows, "Ah, user can be User or null".
const [user, setUser] = useState<User | null>(null); 
```

TypeScript 的另一个亮点是使用了`userReducer`，在这里您可以利用[区别联合](https://www.typescriptlang.org/docs/handbook/advanced-types.html#discriminated-unions)。这里有一个有用的例子:

```
type AppState = {};
type Action =
  | { type: "SET_ONE"; payload: string }
  | { type: "SET_TWO"; payload: number };

export function reducer(state: AppState, action: Action): AppState {
  switch (action.type) {
    case "SET_ONE":
      return {
        ...state,
        one: action.payload // `payload` is string
      };
    case "SET_TWO":
      return {
        ...state,
        two: action.payload // `payload` is number
      };
    default:
      return state;
  }
} 
```

*来源:`react-typescript-cheatsheet` [钩节](https://github.com/typescript-cheatsheets/react-typescript-cheatsheet#hooks)*

这里的美妙之处在于区别对待的结合的有用性。请注意`Action`是如何将两个看起来相似的对象合并在一起的。属性`type`是一个[字符串文字](https://www.typescriptlang.org/docs/handbook/advanced-types.html#string-literal-types)。这与类型`string`的区别在于，该值必须匹配类型中定义的*文字*字符串。这意味着你的程序是额外安全的，因为开发者只能调用一个将`type`键设置为`"SET_ONE"`或`"SET_TWO"`的动作。

正如您所看到的，钩子并没有给 React 和 TypeScript 项目的本质增加太多的复杂性。如果说有什么不同的话，那就是他们把自己很好地借给了二人组。

### 常见使用案例

本节将涵盖在使用带有 React 的 TypeScript 时人们会犯错误的最常见的用例。我们希望通过分享这些，你可以避免陷阱，甚至与他人分享这些知识。

**处理表单事件**

最常见的情况之一是正确键入表单中输入字段上使用的`onChange`。这里有一个例子:

```
import React from 'react'

const MyInput = () => {
  const [value, setValue] = React.useState('')

  // The event type is a "ChangeEvent"
  // We pass in "HTMLInputElement" to the input
  function onChange(e: React.ChangeEvent<HTMLInputElement>) {
    setValue(e.target.value)
  }

  return <input value={value} onChange={onChange} id="input-example"/>
} 
```

**扩展组件道具**

有时你想把为一个组件声明的组件属性扩展到另一个组件上。但是你可能想修改一两个。好吧，还记得我们如何看待两种类型化组件属性、类型或接口的方法吗？取决于您使用的决定了您如何扩展组件属性。我们先来看看使用`type`的方式:

```
import React from 'react';

type ButtonProps = {
    /** the background color of the button */
    color: string;
    /** the text to show inside the button */
    text: string;
}

type ContainerProps = ButtonProps & {
    /** the height of the container (value used with 'px') */
    height: number;
}

const Container: React.FC<ContainerProps> = ({ color, height, width, text }) => {
  return <div style={{ backgroundColor: color, height: `${height}px` }}>{text}</div>
} 
```

如果你使用了一个`interface`来声明你的 props，那么我们可以使用关键字`extends`来“扩展”这个接口，但是要做一两处修改:

```
import React from 'react';

interface ButtonProps {
    /** the background color of the button */
    color: string;
    /** the text to show inside the button */
    text: string;
}

interface ContainerProps extends ButtonProps {
    /** the height of the container (value used with 'px') */
    height: number;
}

const Container: React.FC<ContainerProps> = ({ color, height, width, text }) => {
  return <div style={{ backgroundColor: color, height: `${height}px` }}>{text}</div>
} 
```

两种方法都解决了这个问题。用哪个由你决定。就个人而言，扩展一个接口感觉更可读，但最终，这取决于你和你的团队。

您可以在 TypeScript 手册中阅读关于这两个概念的更多内容:

*   [路口类型](https://www.typescriptlang.org/docs/handbook/advanced-types.html#intersection-types)
*   [扩展接口](http://www.typescriptlang.org/docs/handbook/interfaces.html#extending-interfaces)

### 第三方库

无论是对于像 [Apollo](https://www.apollographql.com/) 这样的 GraphQL 客户端，还是使用像 [React 测试库](https://github.com/testing-library/react-testing-library)这样的东西进行测试，我们经常发现自己在 React 和 TypeScript 项目中使用第三方库。当这种情况发生时，您要做的第一件事就是查看是否有一个带有 TypeScript 类型定义的`@types`包。您可以通过运行以下命令来实现:

```
#yarn
yarn add @types/<package-name>

#npm
npm install @types/<package-name> 
```

例如，如果您正在使用 [Jest](https://jestjs.io/) ，您可以通过运行:

```
#yarn
yarn add @types/jest

#npm
npm install @types/jest 
```

这将为您在项目中使用 Jest 时增加类型安全。

`@types`名称空间是为包类型定义保留的。他们生活在一个名为[的明确类型化的](https://github.com/DefinitelyTyped/DefinitelyTyped)的库中，这个库部分由 TypeScript 团队维护，部分由社区维护。

**这些在我的`package.json`里应该存为`dependencies`还是`devDependencies`？**

简短的回答是“视情况而定”。大多数情况下，如果你正在构建一个 web 应用程序，它们可以放在`devDependencies`下面。然而，如果您正在用 TypeScript 编写 React 库，您可能希望将它们作为`dependencies`包含在内。

在[栈溢出](https://stackoverflow.com/questions/45176661/how-do-i-decide-whether-types-goes-into-dependencies-or-devdependencies)中有一些关于这个问题的答案，你可以查看以获得更多信息。

如果他们没有@types 包会怎么样？

如果您在 npm 上没有找到`@types`包，那么您基本上有两个选择:

1.  添加基本声明文件
2.  添加完整的声明文件

第一个选项意味着根据包名创建一个文件，并把它放在根目录下。例如，如果我们需要包`banana-js`的类型，那么我们可以在根目录下创建一个名为`banana-js.d.ts`的基本声明文件:

```
declare module 'banana-js'; 
```

这不会为您提供类型安全，但会解除对您的阻止。

更彻底的声明文件是为库/包添加类型的地方:

```
declare namespace bananaJs {
    function getBanana(): string;
    function addBanana(n: number) void;
    function removeBanana(n: number) void;
} 
```

如果你从未编写过声明文件，那么我们建议你看一下官方打字手册中的[指南。](https://www.typescriptlang.org/docs/handbook/declaration-files/introduction.html)

## 摘要

由于信息量大，以最佳方式一起使用 React 和 TypeScript 需要一点学习，但是从长远来看好处是巨大的。在本文中，我们讨论了配置、组件、道具、钩子、常见用例以及第三方库。尽管我们可以更深入地研究许多单独的领域，但这应该涵盖了帮助您遵循最佳实践所需的 80%。

如果你想看实际操作，你可以[在 GitHub](https://github.com/jsjoeio/react-ts-example) 上看到这个例子。

如果您想联系我，分享对本文的反馈，或者讨论如何一起使用这两种技术，您可以通过 Twitter [@jsjoeio](https://twitter.com/jsjoeio) 联系我。

## 进一步阅读

如果您想深入了解，我们建议您参考以下资源:

### react-typescript-cheatsheet

许多这些建议都直接来自于[react-typescript-cheat sheet](https://github.com/typescript-cheatsheets/react-typescript-cheatsheet)。如果您正在寻找任何 React-TypeScript 的具体示例或细节，这是一个好去处。我们也欢迎投稿！

### 官方打字手册

另一个很棒的资源是[打字手册](https://www.typescriptlang.org/docs/handbook/basic-types.html)。这由 TypeScript 团队保持最新，并提供了该语言内部工作方式背后的示例和深入解释。

### 打字游戏场

你知道你可以直接在浏览器中测试 React 的类型脚本代码吗？你要做的就是导入 React。这里有一个[链接让你开始](https://www.typescriptlang.org/play/index.html?jsx=2#code/JYWwDg9gTgLgBAJQKYEMDG8BmUIjgcilQ3wG4BYAKCquADsYkpN0k4AhAVxhgjoAUcYAM5wA3nCpxpcNBAA20AFxxhMKPQDmFSjLh0UIJCrUa62qgF8aleo2as4AYT4wU9JoIgi4SAB6MdAAmolw8fF4+YlIyABZIwJqxMCp0nCAARkw6egDuwEEwsanpWVA61tSUcnRqzq7udEwqyOgwAHQAYk4APC4MjZ5CwgB8cAC8cAAUEnKKUAA0cPGJyUv5hbFLBkZwlgCUE2PRunBEMJxQdHA9QcAAbqowAJ7ySONiEhnoANaaOJxgi55io5tAliskik4AADAAkYkhyUsYD8MPWBSKKnhYg2RRRaMslhGYh2SEsPQA9Hd7iMrDYXmA2AAJJDyRQTcQxaRyBQmdRaCoM55MuAAeSKTE5rPZEDgADIuadpBBJVB+WYLJRKlQanUALLPAAqsS0LWIHW6PQl8SgY0mMzgqttS158j2h3Gx0kyrOSAuVxuNJJbop1IedO1QA)。

### 提高打字技能的实用方法

阅读我们的指南[提升打字技能的实用方法](https://www.sitepoint.com/advance-typescript-skills-practical-ways/)让自己在前进的过程中不断学习。

## 分享这篇文章