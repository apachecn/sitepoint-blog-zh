# 对 Redux 的深入研究

> 原文：<https://www.sitepoint.com/redux-deep-dive/>

构建有状态的现代应用程序是复杂的。随着状态的变化，应用程序变得不可预测且难以维护。这就是 Redux 的用武之地。Redux 是一个处理状态的轻量级库。可以把它想象成一个状态机。

在本文中，我将通过构建一个工资处理引擎来深入研究 Redux 的状态容器。该应用程序将存储工资单，以及所有额外的东西，如奖金和股票期权。我将用普通的 JavaScript 和 TypeScript 来保存解决方案，以便进行类型检查。由于 Redux 是超级可测试的，所以我也将使用 Jest 来验证该应用程序。

出于本教程的目的，我将假设对 JavaScript、Node 和 npm 的熟悉程度适中。

首先，您可以用 npm 初始化这个应用程序:

```
npm init 
```

当被问及测试命令时，按下`jest`。这意味着`npm t`将启动 Jest 并运行所有单元测试。主文件将是`index.js`，以保持简洁明了。请尽情回答其余的`npm init`问题。

我将使用 TypeScript 进行类型检查并确定数据模型。这有助于将我们试图构建的东西概念化。

要开始使用 TypeScript:

```
npm i typescript --save-dev 
```

我将在`devDependencies`中保留开发工作流中的依赖关系。这清楚地表明哪些依赖项是开发人员的，哪些是生产人员的。准备好 TypeScript 后，在`package.json`中添加一个`start`脚本:

```
"start": "tsc && node .bin/index.js" 
```

在`src`文件夹下创建一个`index.ts`文件。这将源文件与项目的其余部分分开。如果你做了一个`npm start`，解决方案将无法执行。这是因为您需要配置 TypeScript。

使用以下配置创建一个`tsconfig.json`文件:

```
{
  "compilerOptions": {
    "strict": true,
    "lib": ["esnext", "dom"],
    "outDir": ".bin",
    "sourceMap": true
  },
  "files": [
    "src/index"
  ]
} 
```

我可以把这个配置放在一个`tsc`命令行参数中。比如`tsc src/index.ts --strict ...`。但是将所有这些放在一个单独的文件中要干净得多。注意`package.json`中的`start`脚本只需要一个`tsc`命令。

以下是明智的编译器选项，它们将为我们提供一个良好的起点，以及每个选项的含义:

*   **严格**:启用所有严格类型检查选项，如`--noImplicitAny`、`--strictNullChecks`等。
*   **lib** :编译中包含的库文件列表
*   **outDir** :将输出重定向到这个目录
*   **sourceMap** :生成对调试有用的源文件
*   **文件**:提供给编译器的输入文件

因为我将使用 Jest 进行单元测试，所以我将继续添加它:

```
npm i jest ts-jest @types/jest @types/node --save-dev 
```

`ts-jest`依赖给测试框架增加了类型检查。一个问题是在`package.json`中添加一个`jest`配置:

```
"jest": {
  "preset": "ts-jest"
} 
```

这使得测试框架能够获取类型脚本文件，并知道如何转换它们。这样做的一个好处是在运行单元测试时可以进行类型检查。为了确保这个项目已经准备好，创建一个包含一个`index.test.ts`文件的`__tests__`文件夹。然后，做一个理智检查。例如:

```
it('is true', () => {
  expect(true).toBe(true);
}); 
```

做`npm start`和`npm t`现在运行没有任何错误。这告诉我们现在已经准备好开始构建解决方案了。但在此之前，让我们向项目添加 Redux:

```
npm i redux --save 
```

这种依赖关系到生产。所以，不需要用`--save-dev`包含。如果你检查你的`package.json`，它会进入`dependencies`。

## 运行中的工资单引擎

工资单引擎将包含以下内容:工资、报销、奖金和股票期权。在 Redux 中，不能直接更新状态。相反，调度动作来通知存储任何新的变化。

因此，这给我们留下了以下动作类型:

```
const BASE_PAY = 'BASE_PAY';
const REIMBURSEMENT = 'REIMBURSEMENT';
const BONUS = 'BONUS';
const STOCK_OPTIONS = 'STOCK_OPTIONS';
const PAY_DAY = 'PAY_DAY'; 
```

`PAY_DAY`动作类型对于在发薪日兑现支票和跟踪工资历史很有用。当我们充实工资引擎时，这些动作类型指导设计的其余部分。它们捕获州生命周期中的事件，例如，设置基本工资金额。这些动作事件可以附加到任何东西上，无论是点击事件还是数据更新。Redux 动作类型是抽象的，以至于分派来自哪里并不重要。状态容器可以在客户端和/或服务器上运行。

## 以打字打的文件

使用类型理论，我将根据状态数据确定数据模型。对于每个工资单操作，假设有一个操作类型和一个可选金额。金额是可选的，因为`PAY_DAY`不需要钱来处理薪水支票。我的意思是，它可以向客户收费，但暂时不考虑(可能在第二版中引入)。

例如，把这个放在`src/index.ts`:

```
interface PayrollAction {
  type: string;
  amount?: number;
} 
```

对于工资存根状态，我们需要一个基本工资、奖金等属性。我们还将使用该州维护工资历史记录。

这个 TypeScript 接口应该可以做到:

```
interface PayStubState {
  basePay: number;
  reimbursement: number;
  bonus: number;
  stockOptions: number;
  totalPay: number;
  payHistory: Array<PayHistoryState>;
} 
```

`PayStubState`是一个复杂类型，这意味着它依赖于另一个类型契约。所以，定义`payHistory`数组:

```
interface PayHistoryState {
  totalPay: number;
  totalCompensation: number;
} 
```

对于每个属性，注意 TypeScript 使用冒号指定类型。比如`: number`。这解决了类型协定并增加了类型检查器的可预测性。具有显式类型声明的类型系统增强了 Redux。这是因为 Redux 状态容器是为可预测的行为而构建的。

这个想法既不疯狂也不激进。在 [*学习还原*，第一章](https://www.sitepoint.com/premium/books/learning-redux/read?bookmark=div%5Bid%3D%27reeedr%27%5D%20%3E%20div%5Bid%3D%27reeedr-body%27%5D%20%3E%20div%3Anth-of-type(2)%20%3E%20p%3Anth-of-type(5)&utm_source=blog&utm_medium=referralarticle&utm_campaign=camilo)(仅限 [SitePoint Premium](https://www.sitepoint.com/premium/library/?utm_source=blog&utm_medium=referralarticle&utm_campaign=camilo) 会员)中有很好的解释。

随着应用程序的变化，类型检查增加了一层额外的可预测性。类型理论也有助于应用程序的扩展，因为它更容易重构大部分代码。

现在，使用类型概念化引擎有助于创建以下操作函数:

```
export const processBasePay = (amount: number): PayrollAction =>
  ({type: BASE_PAY, amount});
export const processReimbursement = (amount: number): PayrollAction =>
  ({type: REIMBURSEMENT, amount});
export const processBonus = (amount: number): PayrollAction =>
  ({type: BONUS, amount});
export const processStockOptions = (amount: number): PayrollAction =>
  ({type: STOCK_OPTIONS, amount});
export const processPayDay = (): PayrollAction =>
  ({type: PAY_DAY}); 
```

有趣的是，如果你试图做`processBasePay('abc')`，类型检查器会对你咆哮。破坏类型协定增加了状态容器的不可预测性。我使用像`PayrollAction`这样的单个动作契约来使工资处理器更可预测。注意`amount`是通过 ES6 属性简写在动作对象中设置的。比较传统的做法是`amount: amount`，比较啰嗦。像`() => ({})`这样的箭头函数是编写返回对象文字的函数的一种简洁方式。

## 减速器作为一个纯粹的功能

减速器功能需要一个`state`和一个`action`参数。`state`应该有一个默认值的初始状态。那么，你能想象我们最初的状态会是什么样子吗？我想应该从零开始，用一个空的支付记录列表。

例如:

```
const initialState: PayStubState = {
  basePay: 0, reimbursement: 0,
  bonus: 0, stockOptions: 0,
  totalPay: 0, payHistory: []
}; 
```

类型检查器确保这些是属于该对象的正确值。初始状态就绪后，开始创建 reducer 函数:

```
export const payrollEngineReducer = (
  state: PayStubState = initialState,
  action: PayrollAction): PayStubState => { 
```

Redux reducer 有一个模式，所有的动作类型都由一个`switch`语句处理。但是在讨论所有开关情况之前，我将创建一个可重用的局部变量:

```
let totalPay: number = 0; 
```

注意，如果不改变全局状态，改变局部变量也没关系。我用一个`let`操作符来表示这个变量在未来会发生变化。变异的全局状态，如`state`或`action`参数，会导致缩减器不纯。这个函数范式非常关键，因为 reducer 函数必须保持纯净。如果你正在与这种范式作斗争，请查看来自 [*JavaScript 新手到忍者*，第 11 章](https://www.sitepoint.com/premium/books/javascript-novice-to-ninja-2nd-edition/read?bookmark=div%5Bid%3D%27reeedr%27%5D%20%3E%20div%5Bid%3D%27reeedr-body%27%5D%20%3E%20div%3Anth-of-type(12)%20%3E%20h3%5Bid%3D%27pure-functions%27%5D&utm_source=blog&utm_medium=referralarticle&utm_campaign=camilo)的解释(仅限 [SitePoint Premium](https://www.sitepoint.com/premium/library/?utm_source=blog&utm_medium=referralarticle&utm_campaign=camilo) 会员)。

启动 reducer 的 switch 语句来处理第一个用例:

```
switch (action.type) {
  case BASE_PAY:
    const {amount: basePay = 0} = action;
    totalPay = computeTotalPay({...state, basePay});

    return {...state, basePay, totalPay}; 
```

我使用 ES6 `rest`操作符来保持状态属性不变。比如`...state`。您可以在新对象中重写 rest 运算符之后的任何属性。`basePay`来自析构，这很像其他语言中的模式匹配。`computeTotalPay`功能设置如下:

```
const computeTotalPay = (payStub: PayStubState) =>
  payStub.basePay + payStub.reimbursement
  + payStub.bonus - payStub.stockOptions; 
```

注意，你要扣除`stockOptions`，因为这笔钱将用于购买公司股票。假设您想要处理报销:

```
case REIMBURSEMENT:
  const {amount: reimbursement = 0} = action;
  totalPay = computeTotalPay({...state, reimbursement});

  return {...state, reimbursement, totalPay}; 
```

因为`amount`是可选的，所以确保它有一个默认值以减少意外。这就是 TypeScript 的闪光点，因为类型检查器发现了这个缺陷，并对您咆哮。类型系统知道某些事实，所以它可以做出合理的假设。假设您想要处理奖金:

```
case BONUS:
  const {amount: bonus = 0} = action;
  totalPay = computeTotalPay({...state, bonus});

  return {...state, bonus, totalPay}; 
```

这种模式使得 reducer 可读，因为它所做的只是维护状态。您获取动作的金额，计算总工资，并创建一个新的对象文字。处理股票期权没有太大的不同:

```
case STOCK_OPTIONS:
  const {amount: stockOptions = 0} = action;
  totalPay = computeTotalPay({...state, stockOptions});

  return {...state, stockOptions, totalPay}; 
```

为了在发薪日处理薪水支票，它将需要删除奖金和报销。这两处房产不再保留在政府名下。并且，添加一个条目到支付历史。基本工资和股票期权可以保持不变，因为它们不会像每次发工资时那样经常变化。考虑到这一点，`PAY_DAY`是这样进行的:

```
case PAY_DAY:
  const {payHistory} = state;
  totalPay = state.totalPay;

  const lastPayHistory = payHistory.slice(-1).pop();
  const lastTotalCompensation = (lastPayHistory
    && lastPayHistory.totalCompensation) || 0;
  const totalCompensation = totalPay + lastTotalCompensation;

  const newTotalPay = computeTotalPay({...state,
    reimbursement: 0, bonus: 0});
  const newPayHistory = [...payHistory, {totalPay, totalCompensation}];

  return {...state, reimbursement: 0, bonus: 0,
    totalPay: newTotalPay, payHistory: newPayHistory}; 
```

在类似于`newPayHistory`的数组中，使用一个`spread`操作符，与`rest`相反。与在对象中收集属性的 rest 不同，它将项目分散开来。所以，比如`[...payHistory]`。尽管这两个运算符看起来很相似，但它们并不相同。仔细看，因为这可能会出现在面试问题中。

在`payHistory`上使用`pop()`不会改变状态。为什么？因为`slice()`返回一个全新的数组。JavaScript 中的数组是通过引用复制的。将数组赋给新变量不会改变底层对象。因此，在处理这些类型的对象时必须小心。

因为有可能`lastPayHistory`是未定义的，所以我使用穷人的零合并将其初始化为零。注意要合并的`(o && o.property) || 0`模式。也许未来版本的 JavaScript 甚至 TypeScript 会有一种更优雅的方式来做这件事。

每个 Redux reducer 必须定义一个`default`分支。确保状态不会变为`undefined`:

```
default:
  return state; 
```

## 测试减速器功能

编写纯函数的众多好处之一是它们是可测试的。单元测试是一种您必须预期可预测行为的测试，在这种情况下，您可以将所有测试作为构建的一部分进行自动化。在`__tests__/index.test.ts`中，删除虚拟测试并导入所有感兴趣的功能:

```
import { processBasePay,
  processReimbursement,
  processBonus,
  processStockOptions,
  processPayDay,
  payrollEngineReducer } from '../src/index'; 
```

请注意，所有函数都设置了一个`export`，这样您就可以将它们导入。对于基本工资，启动工资引擎减速器并进行测试:

```
it('process base pay', () => {
  const action = processBasePay(10);
  const result = payrollEngineReducer(undefined, action);

  expect(result.basePay).toBe(10);
  expect(result.totalPay).toBe(10);
}); 
```

Redux 设置初始状态为`undefined`。因此，在 reducer 函数中提供一个默认值总是一个好主意。如何处理报销？

```
it('process reimbursement', () => {
  const action = processReimbursement(10);
  const result = payrollEngineReducer(undefined, action);

  expect(result.reimbursement).toBe(10);
  expect(result.totalPay).toBe(10);
}); 
```

这里的模式与处理奖金的模式相同:

```
it('process bonus', () => {
  const action = processBonus(10);
  const result = payrollEngineReducer(undefined, action);

  expect(result.bonus).toBe(10);
  expect(result.totalPay).toBe(10);
}); 
```

对于股票期权:

```
it('skip stock options', () => {
  const action = processStockOptions(10);
  const result = payrollEngineReducer(undefined, action);

  expect(result.stockOptions).toBe(0);
  expect(result.totalPay).toBe(0);
}); 
```

注意当`stockOptions`大于`totalPay`时`totalPay`必须保持不变。由于这个假设的公司是道德的，它不想从它的员工那里拿钱。如果你运行这个测试，注意`totalPay`被设置为`-10`，因为`stockOptions`被扣除。这就是我们测试代码的原因！让我们在它计算总工资的地方解决这个问题:

```
const computeTotalPay = (payStub: PayStubState) =>
  payStub.totalPay >= payStub.stockOptions
  ? payStub.basePay + payStub.reimbursement
    + payStub.bonus - payStub.stockOptions
  : payStub.totalPay; 
```

如果员工没有赚到足够的钱来购买公司股票，那就直接跳过扣除。此外，确保将`stockOptions`重置为零:

```
case STOCK_OPTIONS:
  const {amount: stockOptions = 0} = action;
  totalPay = computeTotalPay({...state, stockOptions});

  const newStockOptions = totalPay >= stockOptions
    ? stockOptions : 0;

  return {...state, stockOptions: newStockOptions, totalPay}; 
```

fix 计算出他们在`newStockOptions`中是否有足够的空间。有了这个，单元测试就通过了，代码是合理的，有意义的。我们可以测试有足够的钱进行扣除的正面用例:

```
it('process stock options', () => {
  const oldAction = processBasePay(10);
  const oldState = payrollEngineReducer(undefined, oldAction);
  const action = processStockOptions(4);
  const result = payrollEngineReducer(oldState, action);

  expect(result.stockOptions).toBe(4);
  expect(result.totalPay).toBe(6);
}); 
```

对于发薪日，测试多个状态，确保一次性交易不会持续:

```
it('process pay day', () => {
  const oldAction = processBasePay(10);
  const oldState = payrollEngineReducer(undefined, oldAction);
  const action = processPayDay();
  const result = payrollEngineReducer({...oldState, bonus: 10,
    reimbursement: 10}, action);

  expect(result.totalPay).toBe(10);
  expect(result.bonus).toBe(0);
  expect(result.reimbursement).toBe(0);
  expect(result.payHistory[0]).toBeDefined();
  expect(result.payHistory[0].totalCompensation).toBe(10);
  expect(result.payHistory[0].totalPay).toBe(10);
}); 
```

请注意我是如何调整`oldState`来验证`bonus`并将`reimbursement`重置为零的。

reducer 里的默认分支呢？

```
it('handles default branch', () => {
  const action = {type: 'INIT_ACTION'};
  const result = payrollEngineReducer(undefined, action);

  expect(result).toBeDefined();
}); 
```

Redux 在开头设置了一个类似`INIT_ACTION`的动作类型。我们所关心的是我们的减速器设置了一些初始状态。

## 把所有的放在一起

此时，您可能会开始怀疑 Redux 是否更像是一种设计模式。如果您回答说它既是一个模式，又是一个轻量级库，那么您是正确的。在`index.ts`中，导入 Redux:

```
import { createStore } from 'redux'; 
```

下一个代码样本可以围绕这个`if`语句。这是权宜之计，所以单元测试不会泄露到集成测试中:

```
if (!process.env.JEST_WORKER_ID) {
} 
```

我不建议在真实项目中这样做。模块可以放在单独的文件中以隔离组件。这使得它更具可读性，不会泄漏关注点。单元测试也受益于模块独立运行的事实。

用`payrollEngineReducer`开一家 Redux 商店:

```
const store = createStore(payrollEngineReducer, initialState);
const unsubscribe = store.subscribe(() => console.log(store.getState())); 
```

每个`store.subscribe()`返回一个后续的`unsubscribe()`函数，用于清理。当通过存储调度操作时，它取消订阅回调。在这里，我用`store.getState()`向控制台输出当前状态。

假设这个员工挣了`300`，有`50`的报销、`100`的奖金，以及`15`的公司股票:

```
store.dispatch(processBasePay(300));
store.dispatch(processReimbursement(50));
store.dispatch(processBonus(100));
store.dispatch(processStockOptions(15));
store.dispatch(processPayDay()); 
```

为了让它更有趣，再做一次`50`报销并处理另一份薪水支票:

```
store.dispatch(processReimbursement(50));
store.dispatch(processPayDay()); 
```

最后，运行另一个工资支票并取消订阅 Redux 商店:

```
store.dispatch(processPayDay());

unsubscribe(); 
```

最终结果如下所示:

```
{ "basePay": 300,
  "reimbursement": 0,
  "bonus": 0,
  "stockOptions": 15,
  "totalPay": 285,
  "payHistory":
   [ { "totalPay": 435, "totalCompensation": 435 },
     { "totalPay": 335, "totalCompensation": 770 },
     { "totalPay": 285, "totalCompensation": 1055 } ] } 
```

如图所示，Redux 在一个可爱的小包中维护状态、变异和通知订户。可以把 Redux 想象成一个状态机，它是状态数据的真实来源。所有这些，虽然包含了最好的编码所能提供的，比如一个健全的函数范例。

## 结论

Redux 对复杂的状态管理问题有一个简单的解决方案。它依赖于功能范式来减少不可预测性。因为 reducers 是纯函数，所以单元测试非常容易。我决定使用 Jest，但是任何支持基本断言的测试框架也可以。

TypeScript 通过类型理论增加了一层额外的保护。将类型检查与函数式编程结合起来，您就可以得到几乎不会崩溃的可靠代码。最棒的是，TypeScript 在增加价值的同时不碍事。如果你注意到了，一旦类型契约就位，就没有什么额外的编码了。类型检查器完成剩下的工作。像任何好的工具一样，TypeScript 在保持不可见的同时自动执行编码规程。TypeScript 伴随着响亮的吠声，但咬得很轻。

如果您想尝试一下这个项目(我希望您这样做)，您可以在 [GitHub](https://github.com/beautifulcoder/deep-dive-redux-with-typescript-jest) 上找到这篇文章的源代码。

## 分享这篇文章