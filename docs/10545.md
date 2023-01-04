# 条件内的赋值

> 原文：<https://www.sitepoint.com/assignment-inside-a-condition/>

在 <abbr title="HyperText Pre-Processor">PHP</abbr> 中看到这样写的代码是很常见的:

```
if($summary = get_post_summary())
{
    return $summary;
}
```

我指的是条件中的**赋值。足够狡猾的是，它也可以在 JavaScript 中完成，尽管它远不那么常见:**

```
if(summary = document.getElementById("post-summary"))
{
    return summary.innerHTML;
}
```

乍一看，你认为这是一个打印错误是情有可原的！事实上，一些调试器会用一个警告来标记它，询问你是否打算测试相等性(`==`)并且把它错误地输入为赋值(`=`)。

但这不是一个错误——理解它的关键是理解两件事:

首先，**赋值返回一个值**(你赋值的值)。第二，也是最重要的，**它返回的值评估为真或假**，并最终确定条件是否通过。

现在，在 <abbr title="Document Object Model">DOM</abbr> 评估和遍历的世界中，这种技术是安全且可预测的，因为 **<abbr title="Document Object Model">DOM</abbr> 节点要么存在，要么它们是`null—`，并且`null`需要评估为`false`** 。因此，在上面的代码示例中，如果`"#post-summary"`元素存在，条件将评估为`true`，如果不存在，则评估为`false`。

同样，您可以使用相同的技巧来迭代未知的层次结构，使用`while()`遍历连续的父级。这个例子为事件目标和`#document`之间的每个节点名构建了一个数组:

```
var names = [], node = e ? e.target : event.srcElement;
do
{
    names.push(node.nodeName);
}
while(node = node.parentNode)
```

但是在 JavaScript 的其他地方，你可能会发现自己处于一个不太可靠的领域，因为很多值的真与假根本不是直观的。

例如，正数和负数都计算到除零之外的`true`和 <abbr title="Not A Number">NaN</abbr> 。奇怪的是，用`Boolean`构造函数*创建的`object`总是*求值为`true`，即使它被创建为`new Boolean(false)`！

所以要小心！像这样的语法不适合胆小的人；也不应该漫不经心地使用它，过度依赖类型转换和任意值的强制转换。但是如果在适当的环境下使用得当，它可以简化许多条件，从而产生更快、更精简的代码。

*缩略图鸣谢:[sbwoodside](http://www.flickr.com/photos/sbwoodside/3383458339/)*

## 分享这篇文章