# Angular Devs 的 10 个基本打字技巧和窍门

> 原文：<https://www.sitepoint.com/10-essential-typescript-tips-tricks-angular/>

在本文中，我们将深入探讨一组技巧和窍门，这些技巧和窍门在处理 TypeScript 的每个 Angular 项目中都会派上用场。

近年来，JavaScript 中对静态类型的需求迅速增加。越来越多的前端项目、越来越复杂的服务和复杂的命令行实用程序推动了 JavaScript 世界对防御性编程的需求。此外，在实际运行之前编译应用程序的负担并没有被视为一个弱点，而是一个机会。虽然出现了两个强大的政党(TypeScript 和 Flow ),但许多趋势实际上表明只有一个政党可能会占上风——TypeScript。

除了营销声明和众所周知的属性之外， [TypeScript](https://www.sitepoint.com/typescript-tutorial-for-beginners/) 还有一个惊人的社区，里面有非常活跃的贡献者。它背后还有一个在语言设计方面最好的团队。在安德斯·海尔斯伯格的带领下，该团队已经成功地将大规模 JavaScript 项目的前景完全转变为一个几乎完全由打字稿驱动的业务。凭借非常成功的项目，如 VSTS 或 Visual Studio Code，微软本身就是这项技术的坚定信徒。

但是，不仅是 TypeScript 的特性使这种语言具有吸引力，而且 TypeScript 提供的可能性和框架也很有吸引力。Google 决定完全接受 TypeScript 作为 Angular 2+的语言选择，这被证明是一个双赢的局面。不仅 TypeScript 获得了更多的关注，Angular 本身也是如此。使用静态类型，编译器已经可以给我们提供信息性的警告和有用的解释，说明为什么我们的代码不能工作。

## TypeScript 提示 1:提供您自己的模块定义

TypeScript 是 JavaScript 的超集。因此，可以利用每个现有的 npm 包。虽然 TypeScript 生态系统非常庞大，但并不是所有的库都提供了合适的类型。更糟糕的是，对于一些(较小的)包，甚至不存在单独的声明(以`@types/{package}`的形式)。此时，我们有两个选择:

1.  使用 TypeScript 技巧 7 引入遗留代码
2.  自己定义模块的 API。

后者肯定是首选。我们不仅必须查看模块的文档，而且将它打印出来可以防止开发过程中的简单错误。此外，如果我们真的对我们刚刚创建的类型感到满意，我们可以随时将它们提交给`@types`以便将它们包含在 npm 中。因此，这也回报了社区对我们的尊重和感激。不错！

提供我们自己的模块定义的最简单的方法是什么？只需在源目录中创建一个`module.d.ts`(或者它也可以像包一样命名——例如，`unknown-module.d.ts`代表 npm 包`unknown-module`)。

让我们为这个模块提供一个示例定义:

```
declare module 'unknown-module' {
  const unknownModule: any;
  export = unknownModule;
} 
```

显然，这只是第一步，因为我们根本不应该使用`any`。(这有很多原因。TypeScript 技巧 5 展示了如何避免它。)然而，教授 TypeScript 关于模块的知识并防止诸如“未知模块' unknown-module '”之类的编译错误就足够了。这里的`export`符号是指经典的`module.exports = ...`类型的包。

下面是这种模块在 TypeScript 中的潜在消耗:

```
import * as unknownModule from 'unknown-module'; 
```

如前所述，整个模块定义现在都放在导出常数的类型声明中。如果导出的内容是一个函数，声明可能如下所示:

```
declare module 'unknown-module' {
  interface UnknownModuleFunction {
    (): void;
  }
  const unknownModule: UnknownModuleFunction;
  export = unknownModule;
} 
```

当然，也可以使用使用 ES6 模块语法导出功能的包:

```
declare module 'unknown-module' {
  interface UnknownModuleFunction {
    (): void;
  }
  const unknownModule: UnknownModuleFunction;
  export const constantA: number;
  export const constantB: string;
  export default unknownModule;
} 
```

## TypeScript 提示 2:枚举与常量枚举

TypeScript 向 JavaScript 引入了枚举的概念，它确实代表了一组常量。之间的区别

```
const Foo = {
  A: 1,
  B: 2,
}; 
```

和

```
enum Foo {
  A = 1,
  B = 2,
} 
```

不仅仅是 TypeScript 中的语法性质。虽然两者都将被编译成一个对象(即第一个将保持原样，而后者将由 TypeScript 转换)，但 TypeScript `enum`是受保护的，只包含常量成员。因此，不可能在运行时定义它的值。此外，TypeScript 编译器不允许更改这些值。

这也体现在签名上。后者有一个恒定的签名，类似于

```
interface EnumFoo {
  A: 1;
  B: 2;
} 
```

当对象被一般化时:

```
interface ConstFoo {
  A: number;
  B: number;
} 
```

因此，我们在 IDE 中看不到这些“常量”的值。现在`const enum`给了我们什么？首先，让我们看看语法:

```
const enum Foo {
  A = 1,
  B = 2,
} 
```

这其实是一样的——但是注意，前面有一个`const`。这个小小的关键词带来了巨大的不同。为什么？因为在这种情况下，TypeScript 不会编译任何东西。因此，我们有以下级联:

*   对象是不变的，但会生成一个隐式的通用形状声明(接口)
*   将生成一些样板对象初始化器以及一个特殊的形状声明
*   除了专门的形状声明之外，不生成任何东西。

那么后者在代码中是如何使用的呢？通过简单的替换。考虑以下代码:

```
enum Foo {
  A = 1,
  B = 2
}

const enum Bar {
  A = 1,
  B = 2
}

console.log(Bar.A, Foo.B); 
```

这里我们以 JavaScript 结束，结果如下:

```
var Foo;
(function (Foo) {
  Foo[Foo["A"] = 1] = "A";
  Foo[Foo["B"] = 2] = "B";
})(Foo || (Foo = {}));
console.log(1 /* A */, Foo.B); 
```

请注意，仅针对`enum Foo`生成了 5 行，而`enum Bar`仅导致了简单的替换(恒定注入)。因此`const enum`是一个编译时特性，而最初的`enum`是一个运行时+编译时特性。大多数项目都非常适合`const enum`，但也可能有`enum`更受青睐的情况。

## 类型脚本技巧 3:类型表达式

大多数时候，我们满足于使用`interface`来定义物体的新形状。然而，有时候一个简单的界面已经不够了。考虑下面的例子。我们从一个简单的界面开始:

```
interface StatusResponse {
  issues: Array<string>;
  status: 'healthy' | 'unhealthy';
} 
```

`'healthy' | 'unhealthy'`中的符号表示常量字符串为`healthy`或另一个常量字符串等于`unhealthy`。好了，这是声音接口的定义。然而，现在我们的代码中也有了一个方法，它想要变异一个类型为`StatusResponse`的对象:

```
function setHealthStatus(state: 'healthy' | 'unhealthy') {
  // ...
} 
```

到目前为止，一切顺利，但是现在将它改为`'healthy' | 'unhealthy' | 'unknown'`已经导致了两个变化(一个在接口定义中，一个在函数中的参数类型定义中)。不酷。实际上，我们到目前为止看到的表达式已经是类型表达式了，我们只是没有“存储”它们——也就是说，给它们一个名字(有时称为*别名*)。让我们这样做:

```
type StatusResponseStatus = 'healthy' | 'unhealthy'; 
```

当`const`、`var`和`let`在运行时从 JS 表达式创建对象时，`type`在编译时从 ts 表达式(所谓的类型表达式)创建类型声明。然后可以使用这些类型声明:

```
interface StatusResponse {
  issues: Array<string>;
  status: StatusResponseStatus;
} 
```

在我们的工具带中有了这样的别名，我们可以很容易地随意重构类型系统。使用 TypeScript 强大的类型推断只是相应地传播更改。

## 打字技巧 4:使用鉴别器

类型表达式的用途之一是以前引入的几个(简单)类型表达式的联合，即类型名或常量。当然，联合不限于简单的类型表达式，但是为了可读性，我们不应该使用这样的结构:

```
type MyUnion = {
  a: boolean,
  b: number,
} | {
  c: number,
  d: {
    sub: string,
  }
} | {
  (): void;
}; 
```

相反，我们想要一个简单明了的表达式，例如:

```
type MyUnion = TypeA | TypeB | TypeC; 
```

如果所有类型都公开了至少一个具有相同名称但不同(常量)值的成员，则这样的联合可以用作所谓的有区别的联合。假设我们有三种类型，比如这些:

```
interface Line {
  points: 2;
  // other members, e.g., from, to, ...
}

interface Triangle {
  points: 3;
  // other members, e.g., center, width, height
}

interface Rectangle {
  points: 4;
  // other members, e.g., top, right, bottom, left
} 
```

这些类型之间有区别的联合可能是这样的:

```
type Shape = Line | Triangle | Rectangle; 
```

这种新类型现在可以用在函数中，在函数中我们可以使用鉴别器上的一些验证来访问特定的成员，这将是`points`属性。例如:

```
function calcArea(shape: Shape) {
  switch (shape.points) {
    case 2:
      // ... incl. return
    case 3:
      // ... incl. return
    case 4:
      // ... incl. return
    default:
      return Math.NaN;
  }
} 
```

自然地，语句对于这项任务来说非常方便，但是也可以使用其他的验证方法。

有区别的联合在各种场景中都很方便——例如，在处理 JSON 文件时遍历类似 AST 的结构，这些 JSON 文件的模式中有类似的分支机制。

## 打字技巧 5:避免任何错误，除非它确实是任何错误

我们都经历过这种情况:我们确切地知道应该写什么代码，但是我们无法让 TypeScript 编译器接受我们的代码数据模型。嗯，幸运的是，我们总是可以求助于`any`来挽救局面。但是我们不应该。`any`应该只用于实际上可以是任何类型的类型。(例如，`JSON.parse`故意返回`any`，因为根据我们正在解析的字符串，结果可能是任何东西。)

例如，在我们的一个数据存储中，我们明确定义了某个字段`custom`将保存类型为`any`的数据。我们不知道将在那里设置什么，但是消费者可以自由选择数据(以及数据类型)。我们既不想也不能阻止这种事情发生，所以`any`型是真的。

然而，在大多数场景中(即我们的代码专门涵盖的所有场景中)，`any`通常是一种或多种类型。我们只需要找出我们确切期望的类型，以及如何构造这样一个类型来给 TypeScript 提供所有必要的信息。

使用前面的一些技巧，例如，TypeScript 技巧 4 和 TypeScript 技巧 3，我们已经可以解决一些最大的问题:

```
function squareValue(x: any) {
  return Math.pow(x * 1, 2);
} 
```

我们宁愿尽可能地约束输入:

```
function squareValue(x: string | number) {
  return Math.pow(+x, 2);
} 
```

现在有趣的部分是前一个表达式`x * 1`在`any`中是允许的，但一般情况下是不允许的。然而，`+x`给了我们想要的`number`的强制施法。为了检查我们的造型是否适用于给定的类型，我们需要具体说明。问题“什么类型可以进入这里？”在 TypeScript 能够为我们提供有用的信息之前，我们需要回答这个问题。

## TypeScript 技巧 6:有效地使用泛型

TypeScript 意味着静态类型，但静态类型并不意味着显式类型。TypeScript 具有强大的类型推断功能，在真正使用 TypeScript 之前，必须使用并完全理解这种功能。就我个人而言，我认为我使用 TypeScript 比使用普通的 JavaScript 更有效率，因为我没有在打字上花太多时间，但是一切似乎都很到位，几乎所有的小错误都已经被 TypeScript 检测到了。这种生产力提升背后的驱动力之一是仿制药。泛型给了我们将类型作为变量引入的能力。

让我们考虑以下经典 JS 助手函数的情况:

```
function getOrUpdateFromCache(key, cb) {
  const value = getFromCache(key);

  if (value === undefined) {
    const newValue = cb();
    setInCache(key, newValue);
    return newValue;
  }

  return value;
} 
```

将它直接转换成 TypeScript 留给我们两个`any`:一个是从回调中检索的数据，一个是从函数本身检索的数据。然而，这不需要看起来像那样，因为我们显然知道类型(我们传入`cb`):

```
function getOrUpdateFromCache<T>(key: string, cb: () => T) {
  const value: T = getFromCache(key);

  if (value === undefined) {
    const newValue = cb();
    setInCache(key, newValue);
    return newValue;
  }

  return value;
} 
```

上面代码中唯一麻烦的地方是对调用`getFromCache`函数的结果的显式类型赋值。在这里，我们暂时必须相信我们的代码始终只对相同的键使用相同的类型。在 TypeScript 技巧 10 中，我们学习了如何改善这种情况。

大多数情况下，使用泛型只是为了“传递”一个类型，也就是说，告诉 TypeScript 某些参数类型之间的关系(在前一种情况下，结果的类型与回调的返回类型相关联)。向 TypeScript 讲授这种关系还会受到进一步约束，然后由 TypeScript 将这些约束放置到位。

虽然泛型很容易与接口、类型、类和标准函数一起使用，但它们似乎不太容易与箭头函数一起使用。根据定义，这些函数是匿名的(它们需要被赋给一个变量，以便通过名字来访问)。

作为一条经验法则，我们可以遵循这种方法:只需考虑一个普通的，但是匿名的函数声明。这里只有名字不见了。因此，`<T>`自然就放在括号之前。我们最终会得到:

```
const getOrUpdateFromCache = <T>(key: string, cb: () => T) => /* ...*/; 
```

然而，一旦我们在 TSX 文件中引入这一点(无论什么原因)，我们会以一个错误*结束:未关闭的`T`标签*。强制转换也出现了同样的问题(通过使用`as`操作符解决了这个问题)。现在我们的解决方法是明确地告诉 TypeScript 该语法是为泛型设计的:

```
const getOrUpdateFromCache = <T extends {}>(key: string, cb: () => T) => /* ...*/; 
```

## TypeScript 技巧 7:引入遗留代码

将现有代码迁移到 TypeScript 的关键是一组调整良好的 TypeScript 配置参数——例如，允许隐式`any`和禁用严格模式。这种方法的问题是转换后的代码从遗留状态进入冻结状态，这也会影响正在编写的新代码(因为我们禁用了一些最有用的编译器选项)。

一个更好的替代方法是在`tsconfig.json`文件中使用`allowJs`,紧挨着通常的(相当强的)参数:

```
{
  "compilerOptions": {
    "allowJs": true,
    // ...
  }
} 
```

现在，我们不再将现有文件从`.js`重命名为`.ts`，而是尽可能长时间地保留现有文件。只有当我们认真处理内容，使代码完全从 JavaScript 转换成满足我们设置的类型脚本变体时，我们才会重命名。

## TypeScript 提示 8:创建具有属性的函数

我们已经知道，使用接口来声明函数的形状是一种很好的方法。此外，这种方法允许我们给给定的函数类型附加一些属性。让我们先来看看这在实践中是怎样的:

```
interface PluginLoader {
  (): void;
  version: string;
} 
```

定义这一点很简单，但不幸的是，使用它并不简单。让我们通过创建一个实现该接口的对象来尝试使用该接口:

```
const pl: PluginLoader = () => {};
pl.version = '1.0.0'; 
```

哎哟:我们过不了申报关。TypeScript(正确地)报错说`version`属性丢失了。好吧，那么下面的解决方法怎么样:

```
interface PluginLoaderLight {
  (): void;
  version?: string;
}

const pl: PluginLoaderLight = () => {};
pl.version = '1.0.0'; 
```

完美。这是可行的，但是它有一个主要的缺点:即使我们知道在`pl.version`赋值之后`version`属性将一直存在于`pl`，TypeScript 并不知道这一点。因此从它的角度来看，任何对`version`的访问都可能是错误的，需要首先对照`undefined`进行检查。换句话说，在当前的解决方案中，我们用于产生这种类型的对象的接口必须不同于用于消费的接口。这并不理想。

幸运的是，有一种方法可以解决这个问题。让我们回到最初的`PluginLoader`界面。让我们用一个声明为“相信我，我知道我在做什么”的类型脚本来尝试一下。

```
const pl = <PluginLoader>(() => {});
pl.version = '1.0.0'; 
```

这样做的目的是告诉 TypeScript，“看到这个函数，我知道它会是这个给定的形状(`PluginLoader`)”。TypeScript 仍然检查这个*是否还能满足*。由于没有冲突定义可用，它将接受这种转换。石膏应该是我们最后的防线。我不认为`any`是一条可能的防线:要么类型是真正的`any`(可以一直是——我们只是接受任何东西，完全没问题)，要么它不应该被使用，必须由特定的东西来替换(参见类型脚本技巧 5)。

虽然这种铸造方法可以解决上述问题，但在一些非角度环境中可能不可行(例如，反应元件)。这里，我们需要选择转换的替代变量，即`as`操作符:

```
const pl = (() => {}) as PluginLoader;
pl.version = '1.0.0'; 
```

就我个人而言，我总是会选择由`as`驱动的演员阵容。它们不仅总是有效，甚至对于没有打字背景的人来说也非常可读。对我来说，一致性和可读性是每一个代码库的核心原则。它们可以被打破，但这样做必须有充分的理由。

## 打字技巧 9:运算符的关键

TypeScript 实际上非常擅长处理类型。因此，它为我们提供了一些武器，可以用来为实际生成界面内容编写一些代码。同样，它也为我们提供了迭代接口内容的选项。

考虑以下接口:

```
interface AbstractControllerMap {
  user: UserControllerBase;
  data: DataControllerBase;
  settings: SettingsControllerBase;
  //...
} 
```

潜在地，在我们的代码中，我们有一个具有相似结构的对象。这个对象的键是神奇的:它的字符串在许多迭代中使用，因此在许多场合使用。很可能我们在某个地方使用这些键作为参数。

显然，我们可以这样表述一个函数:

```
function actOnAbstractController(controllerName: string) {
  // ...
} 
```

不利的一面是，我们肯定有更多的知识，这些知识是我们不与 TypeScript 共享的。因此，更好的版本应该是这样的:

```
function actOnAbstractController(controllerName: 'user' | 'data' | 'settings') {
  // ...
} 
```

然而，正如在 TypeScript 技巧 3 中已经提到的，我们希望对重构具有弹性。这是没有弹性的。如果我们添加另一个键(即，在上面的例子中映射另一个控制器)，我们将需要在多个位置编辑代码。

`keyof`操作符提供了一个很好的解决方法，它适用于任何类型。例如，上面的`AbstractControllerMap`键的别名如下所示:

```
type ControllerNames = keyof AbstractControllerMap; 
```

现在，我们可以改变我们的函数，以真正变得对原始地图上的重构具有弹性。

```
function actOnAbstractController(controllerName: ControllerNames) {
  // ...
} 
```

最酷的事情是`keyof`实际上会尊重接口合并。无论我们把`keyof`放在哪里，它总是与它所应用的类型的“最终”版本相对应。当考虑工厂方法和它们的有效接口设计时，这也是非常有用的。

## TypeScript 技巧 10:有效的回调定义

比预期更经常出现的一个问题是事件处理程序的类型。让我们看一下下面的界面:

```
interface MyEventEmitter {
  on(eventName: string, cb: (e: any) => void): void;
  off(eventName: string, cb: (e: any) => void): void;
  emit(eventName: string, event: any): void;
} 
```

回顾之前所有的招数，我们知道这样的设计既不理想，也不可接受。那么我们能做些什么呢？让我们从这个问题的简单近似开始。第一步当然是定义所有可能的事件名称。我们可以像 TypeScript 技巧 3 中介绍的那样使用类型表达式，但是更好的是像上一篇技巧中那样映射到事件类型声明。

因此，我们从地图开始，应用 TypeScript 技巧 9 来获得以下内容:

```
interface AllEvents {
  click: any;
  hover: any;
  // ...
}

type AllEventNames = keyof AllEvents; 
```

这已经产生了一些效果。以前的接口定义现在变成了:

```
interface MyEventEmitter {
  on(eventName: AllEventNames, cb: (e: any) => void): void;
  off(eventName: AllEventNames, cb: (e: any) => void): void;
  emit(eventName: AllEventNames, event: any): void;
} 
```

稍微好一点，但是我们仍然有`any`在所有有趣的位置上。现在可以应用 TypeScript 提示 6，让 TypeScript 对输入的`eventName`有更多的了解:

```
interface MyEventEmitter {
  on<T extends AllEventNames>(eventName: T, cb: (e: any) => void): void;
  off<T extends AllEventNames>(eventName: T, cb: (e: any) => void): void;
  emit<T extends AllEventNames>(eventName: T, event: any): void;
} 
```

这很好，但还不够。当我们输入时，TypeScript 现在知道了`eventName`的确切类型，但是我们不能将存储在`T`中的信息用于任何事情。除此之外，我们可以将它与另一个强大的类型表达式一起使用:应用于接口的索引操作符。

```
interface MyEventEmitter {
  on<T extends AllEventNames>(eventName: T, cb: (e: AllEvents[T]) => void): void;
  off<T extends AllEventNames>(eventName: T, cb: (e: AllEvents[T]) => void): void;
  emit<T extends AllEventNames>(eventName: T, event: AllEvents[T]): void;
} 
```

这似乎是很强大的东西，除了我们现有的声明都被设置为`any`。所以让我们改变这一点。

```
interface ClickEvent {
  leftButton: boolean;
  rightButton: boolean;
}

interface AllEvents {
  click: ClickEvent;
  // ...
} 
```

真正强大的部分是现在接口合并仍然有效。也就是说，我们可以通过再次使用相同的接口名称来扩展我们的事件定义:

```
interface AllEvents {
  custom: {
    field: string;
  };
} 
```

这使得类型表达式更加强大，因为扩展性以一种美妙而优雅的方式集成在一起。

## 进一步阅读

*   [(原创，2012)引入 TypeScript — JavaScript 的类固醇](https://www.sitepoint.com/microsoft-typescript-javascript-precompiler/)
*   [打字稿简介](https://www.sitepoint.com/introduction-to-typescript/)
*   [关于歧视性结合的打字稿手册](https://basarat.gitbooks.io/typescript/docs/types/discriminated-unions.html)
*   [官方打字稿博客](https://blogs.msdn.microsoft.com/typescript/)
*   [使用 TypeScript 开始使用 Angular 2](https://www.sitepoint.com/getting-started-with-angular-2-using-typescript/)

## 结论

希望这些打字技巧中有一个或多个对您来说是新的，或者至少是您希望在以后的文章中看到的。这个列表并不完整，但是应该给你一个很好的起点来避免一些问题并提高生产力。

有哪些小技巧让你的代码大放异彩？你在哪里感觉最舒服？请在评论中告诉我们！

## 分享这篇文章