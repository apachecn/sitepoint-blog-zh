# 查找祖先 DOM 节点

> 原文：<https://www.sitepoint.com/finding-an-ancestor-node/>

在这个小巧可爱的函数系列的第八篇文章中，我将关注一个名为`ancestor()`的函数。顾名思义，这个函数根据标签名和/或类匹配来获取对给定节点祖先的引用。

下面是`ancestor()`函数的代码:

```
function ancestor(node, match)
{
  if(!node)
  {
    return null;
  }
  else if(!node.nodeType || typeof(match) != 'string')
  {
    return node;
  }
  if((match = match.split('.')).length === 1)
  {
    match.push(null);
  }
  else if(!match[0])
  {
    match[0] = null;
  }
  do
  {
    if
    (
      (
        !match[0]
        ||
        match[0].toLowerCase() == node.nodeName.toLowerCase())
      &&
      (
        !match[1]
        ||
        new RegExp('( |^)(' + match[1] + ')( |$)').test(node.className)
      )
    )
    {
      break;
    }
  }
  while(node = node.parentNode);

  return node;
}
```

第一个参数是对原始节点的引用——它可以是任何类型的 <abbr title="Document Object Model">DOM</abbr> 节点，但通常是一个元素。第二个参数是一个标识祖先的字符串——或者是一个简单的标记名，如`"ul"`，或者是一个类选择器，如`".menu"`，或者是两者的组合，如`"ul.menu"`。该函数将从原始节点向上迭代，并返回与字符串模式匹配的第一个祖先节点，如果找不到这样的祖先，则返回`null`。

## 该功能的用途是什么

此功能最常见的用例是在事件处理代码中——从事件目标中识别包含元素，而不需要知道中间还有哪些节点；也许我们甚至不知道祖先是什么类型的元素。`ancestor()`函数通过根据我们所拥有的任何信息反复检查父节点来处理这个问题。

例如，假设我们将`focus`事件绑定到一组菜单链接，处理程序代码需要获得对包含列表项的引用。动态菜单通常需要在它们支持的标记类型上非常灵活，不仅仅考虑像这样的简单项目:

```
<li>
  <a>...</a>
</li>
```

但也包括更复杂的项目，添加额外的元素以获得额外的语义或作为样式挂钩:

```
<li>
  <h3>
    <span>
      <a>...</a>
    </span>
  </h3>
</li>
```

将添加 JavaScript 来处理链接`focus`事件(必须单独添加，因为[焦点事件不会冒泡](http://www.quirksmode.org/dom/events/blurfocus.html "blur and focus (QuirksMode)")):

```
var links = menu.getElementsByTagName('a');

for(var len = links.length, i = 0; i < len; i ++)
{
  links[i].addEventListener('focus', function(e)
  {
    var link = e.target;

  }, false);
}
```

然后`ancestor()`函数可以处理目标转换:

```
var item = ancestor(link, 'li');
```

第二个参数的灵活性允许不同的信息情况，例如，我们知道包含菜单将有一个`"menu"`的`class`，但是我们不知道它将是一个`<ul>`还是`<ol>`元素:

```
var menu = ancestor(link, '.menu');
```

或者，也许我们有一个更深层次的嵌套结构，其中单独的子菜单是无序列表(`<ul class="menu">`)，而顶级导航栏是一个有序列表，具有相同的`class`名称(`<ol class="menu">`)。我们可以在匹配中定义标记名和`class`,以获得我们想要的特定引用:

```
var navbar = ancestor(link, 'ol.menu');
```

在这种情况下，任何数量的其他`"menu"`元素都将被忽略，只有当祖先元素与标签名和`class`的*都匹配时，才会被返回。*

## 该功能如何工作

基本的功能仅仅是通过 DOM 的**向上迭代。我们从原始节点开始，然后检查每个`parentNode`直到指定的祖先被匹配，或者如果我们用完了节点(即，如果我们到达了`#document`却没有找到想要的节点)就放弃迭代。然而，我们也有一些测试代码来确保两个参数都被正确定义:**

```
if(!node)
{
  return null;
}
else if(!node.nodeType || typeof(match) != 'string')
{
  return node;
}
```

如果输入的`node`参数未定义或`null`，那么函数返回`null`；或者如果输入`node`不是一个节点，或者输入`match`不是一个字符串，那么函数返回原来的节点。这些只是安全条件，通过减少对发送给它的数据进行预先测试的需要，使功能更加健壮。

接下来，我们处理`match`参数来创建一个包含两个值的数组——第一个是指定的标记名(如果没有指定，则为`null`),而第二个是指定的类名(如果没有指定，则为`null`):

```
if((match = match.split('.')).length === 1)
{
  match.push(null);
}
else if(!match[0])
{
  match[0] = null;
}
```

最后，我们可以进行迭代检查，将每次迭代中的当前引用节点与`match`数组中定义的标准进行比较。如果`match[0]`(标签名)是`null`，那么*任何*元素都将匹配，否则我们只匹配具有指定标签名的元素(将两者都转换成小写，所以匹配不区分大小写)。同样，如果`match[1]`(类名)是`null`，那么一切都好，否则元素必须包含指定的`class`:

```
do
{
  if
  (
    (
      !match[0]
      ||
      match[0].toLowerCase() == node.nodeName.toLowerCase())
    &&
    (
      !match[1]
      ||
      new RegExp('( |^)(' + match[1] + ')( |$)').test(node.className)
    )
  )
  {
    break;
  }
}
while(node = node.parentNode);
```

如果*两个*条件都满足，我们中断迭代，返回当前参考节点；否则我们继续下一个`parentNode`。如果当两个`match`值都是`null`时，我们允许代码进行到这一步，最终结果将是我们返回原来的`node`，这正是开始时的安全条件已经做的。

关于迭代本身的一个有趣的事情是`do...while`的使用:

```
do
{
  ...
}
while(node = node.parentNode);
```

在`while`评估中，我们利用了在评估中定义赋值的能力。每次求值时，`node`引用都被转换成它的`parentNode`并被重新分配。该赋值返回被赋值的`node`。如果父代不存在，`node`引用将是`null`，因此它不会通过`while`条件，因此迭代将停止，函数将返回`null`。然而，如果父节点*存在*，它将通过`while`条件，因此迭代将继续，因为任何节点引用的计算结果为`true`，而`null`的计算结果为`false`。

因为我们要测试的节点数量是未知的，所以只要父节点存在，我们就必须使用一个`while`语句进行迭代。但是，通过使用`do...while`而不是简单的`while`，我们在转换为其父节点之前评估原始节点*(因为`do`在第一个`while`之前评估)。最终，这意味着如果原始节点*已经通过了*匹配条件，它将被立即返回，这使我们不必在迭代之前定义单独的`if`条件。*

## 结论

`ancestor()`函数不会赢得任何复杂的奖项！但是简单功能的抽象是编程的基础，提供了可重用的代码，避免了重复输入相同的基本逻辑。

## 分享这篇文章