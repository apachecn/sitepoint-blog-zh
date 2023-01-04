# 高阶组件:React 应用程序设计模式

> 原文：<https://www.sitepoint.com/react-higher-order-components/>

在本文中，我们将讨论如何使用高阶组件来保持 React 应用程序整洁、结构良好且易于维护。我们将讨论纯函数如何保持代码整洁，以及如何将这些相同的原则应用于 React 组件。

## 纯函数

如果一个函数符合下列性质，它就被认为是纯的:

*   它处理的所有数据都被声明为参数
*   它不会改变给它的数据或任何其他数据(这些通常被称为*副作用*)
*   给定相同的输入，它将*总是*返回相同的输出。

比如下面的`add`函数就是纯的:

```
function add(x, y) {
  return x + y;
} 
```

然而，下面的函数`badAdd`是不纯的:

```
let y = 2;
function badAdd(x) {
  return x + y;
} 
```

这个函数不是纯函数，因为它引用了没有直接给出的数据。因此，可以用相同的输入调用这个函数，得到不同的输出:

```
let y = 2;
badAdd(3) // 5
y = 3;
badAdd(3) // 6 
```

要阅读更多关于纯函数的内容，你可以阅读马克·布朗的“[合理纯编程简介](https://www.sitepoint.com/an-introduction-to-reasonably-pure-functional-programming/)”。

虽然纯函数非常有用，并使应用程序的调试和测试更加容易，但有时您需要创建有副作用的不纯函数，或者修改无法直接访问的现有函数的行为(例如，库中的函数)。要做到这一点，我们需要看看高阶函数。

## 高阶函数

高阶函数是在被调用时返回另一个函数的函数。通常他们也接受一个函数作为参数，但是这并不是高阶函数所必需的。

假设我们有上面的`add`函数，我们想写一些代码，这样当我们调用它时，我们在返回结果之前将结果记录到控制台。我们无法编辑`add`函数，因此我们可以创建一个新函数:

```
function addAndLog(x, y) {
  const result = add(x, y);
  console.log(`Result: ${result}`);
  return result;
} 
```

我们认为记录函数的结果是有用的，现在我们想用一个`subtract`函数做同样的事情。与其重复上面的内容，我们可以编写一个*高阶函数*，它可以接受一个函数并返回一个新函数，这个新函数调用给定的函数并在返回之前记录结果:

```
function logAndReturn(func) {
  return function(...args) {
    const result = func(...args)
    console.log('Result', result);
    return result;
  }
} 
```

现在，我们可以使用这个函数向`add`和`subtract`添加日志记录:

```
const addAndLog = logAndReturn(add);
addAndLog(4, 4) // 8 is returned, ‘Result 8’ is logged

const subtractAndLog = logAndReturn(subtract);
subtractAndLog(4, 3) // 1 is returned, ‘Result 1’ is logged; 
```

`logAndReturn`是高阶函数，因为它以一个函数为自变量，返回一个我们可以调用的新函数。这些对于包装不能改变行为的现有函数非常有用。有关这方面的更多信息，请查阅 M. David Green 的文章“[JavaScript 中的高阶函数](https://www.sitepoint.com/higher-order-functions-javascript/)”，这篇文章对这一主题进行了更详细的讨论。

参见 [CodePen](https://codepen.io) 上 site point([@ site point](https://codepen.io/SitePoint))
的笔 [高阶函数](https://codepen.io/SitePoint/pen/akKmYp)。