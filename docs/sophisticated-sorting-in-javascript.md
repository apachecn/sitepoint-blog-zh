# JavaScript 中复杂的排序

> 原文：<https://www.sitepoint.com/sophisticated-sorting-in-javascript/>

JavaScript 的排序机制是一个简单的模型，然而在表面之下是一些令人难以置信的灵活和强大的功能。使用`sort`可以组织数组，不仅仅是按照字母或数字的顺序，而是按照*任何我们可以用条件逻辑表达的*预定的排列。

## 排序函数的工作原理

*→如果你已经知道了基本知识，那么你可能想[跳过](#skip-the-basics)。*

如果调用`sort()`时没有参数，那么数组按照字典顺序**排序**——当每个值被视为一个字符串时，按照字典顺序:

```
var letters = ["R","O","F","L"];

letters.sort();

alert(letters);    //produces ["F","L","O","R"]
```

否则，`sort`的参数是一个**比较函数**，它根据返回的方式定义排序行为。比较函数本身接受两个参数，通常称为`a`和`b`，它们代表在每个操作中被比较的两个值。然后:

1.  如果函数*返回的值小于零*，则将`a`排在`b`之前
2.  如果函数*返回大于零的值*，则将`b`排在`a`之前
3.  如果函数*返回零*，则`a`和`b`相对于彼此保持不变

**The specification defines the rules in a confusing way** **JavaScript 规范将第一个排序条件称为<q>排序`b`到比`a`</q> 更低的索引。但这实际上意味着，*"排序`b`在列表中比`a` "* 更低，从数字索引的角度来看，它是一个比更高的*，而不是更低的索引。它以一种非常混乱的方式使用了单词*“index”*；我是如何表达上述条件的，希望能更清楚。***  **因此，使用比较函数的正常方式是执行并返回一个简单的评估，产生所需的排序。例如，如果函数返回`(a - b)`，那么将产生一个**数字排序**:

```
var numbers = [8,5];

numbers.sort(function(a, b)
{
    return a - b;	
});

alert(numbers);    //produces [5,8]
```

我们可以用值的例子来解决这个问题:自`a = 8`和`b = 5`，然后是`(a - b) == 3`；三大于零，所以`b`将在`a`之前排序，产生顺序`[5,8]`。

因此，可以简单地通过颠倒等式来产生逆数值顺序:

```
var numbers = [4,3,5,9];

numbers.sort(function(a, b)
{
    return b - a;	
});

alert(numbers);    //produces [9,5,4,3]
```

我们还可以创建一个产生字典排序的比较函数，通过定义三个比较来评估每对字符串——在计算方面，`"a"`小于`"b"`,因此我们可以像这样直接比较字符串，然后返回三个排序值之一:

```
var letters = ["R","O","F","L"];

letters.sort(function(a, b)
{
    var x = a.toLowerCase(), y = b.toLowerCase();

    return x < y ? -1 : x > y ? 1 : 0;
});
```

请注意我们是如何将每个字符串预先转换成小写的，这确保了我们得到一个不区分大小写的排序(如果我们不这样做，那么小写和大写字母将被分开排序)。我们还将这些操作的结果赋给新的变量，因为一些浏览器反对重写参数。

## 多维排序

如果`a`和`b`本身就是数组，那么，直接用数学求值比较数组不会产生我们想要的结果；但是我们可以比较他们的*内在价值*并进行排序。这就是我们如何排序多维数组，使用每个内部数组的值作为排序标准。可以说，所有其他内部值只是“凑热闹”，这样我们就可以对包含混合值的数组进行排序。以下示例将根据每个形状的边数对矩阵进行排序:

```
var shapes = [
    [5, "Pentagon"],
    [3, "Triangle"],
    [8, "Octagon"],
    [4, "Rectangle"]
    ];

shapes.sort(function(a, b)
{
    return a[0] - b[0];
});
```

## 多标准排序

如果我们可以只使用其中一个值对多维数组进行排序，难道我们不能使用*和*两个值作为独立的标准对它们进行排序吗？答案当然是，是的，我们可以，只需在比较函数的逻辑中添加更多的条件。例如，使用值`[0]`进行初级排序，但是如果两个值相等，则使用值`[1]`进行次级排序。以下示例再次使用形状，首先按边数排序，如果边数相等，再按形状的字母名称排序:

```
var shapes = [
    [4, "Trapezium"],
    [5, "Pentagon"],
    [3, "Triangle"],
    [4, "Rectangle"],
    [4, "Square"]
    ];

shapes.sort(function(a, b)
{
    if(a[0] === b[0])
    {
        var x = a[1].toLowerCase(), y = b[1].toLowerCase();

        return x < y ? -1 : x > y ? 1 : 0;
    }
    return a[0] - b[0];
});
```

原则可以扩展到我们需要的程度——如果主测试是相等的，那么按次测试排序；如果二级测试相同，则按三级测试排序；以此类推，尽可能多的进行比较。

## 排序对象数组

随着比较变得越来越复杂，最好放弃使用多维数组，转而使用对象文字的**数组。这使得在比较函数中更容易看到发生了什么，仅仅是因为我们对标准有了*直观的名字*。在 [CSSUtilities](http://www.brothercake.com/site/resources/scripts/cssutilities/) 库中可以看到一个简洁的例子，它解析文档 <abbr title="Cascading Style Sheets">CSS</abbr> 来创建自己的规则对象集合。**

整个规则集合存储为一个数组，它的每个成员都是一个对象，具有类似于`specificity`(规则的“强度”，由其选择器和继承上下文决定)、`index`(规则在规则集合中的整体位置)和`depth`(继承规则的数值，表示继承链的深度，即。从`<html>`继承的规则的值比从`<body>`继承的规则的值大 1。`specificity`本身也是一个由四个独立值组成的数组，每个独立值对应一个特性类别(详见 <abbr title="Cascading Style Sheets Level 3">CSS3</abbr> 规范中的 [<cite>计算选择器的特性</cite>](https://www.w3.org/TR/css3-selectors/#specificity) )。

那么，考虑到所有这些值，我们如何对规则对象进行排序，以获得一个符合特定性绝对顺序的数组呢？当然，第一件事是对我们试图实施的规则有一个清晰的认识:

1.  如果值不相等，则按特异性排序:
    1.  如果值不相等，则按第一个类别排序
    2.  否则，如果值不相等，则按第二个类别排序
    3.  否则，如果值不相等，则按第三个类别排序
    4.  否则按第四个也是最后一个类别排序
2.  否则，如果值不相等，则按索引排序
3.  否则按继承深度排序

这只是用代码表达的一个例子:

```
rules.sort(function(a, b)
{
    if(a.specificity.toString() === b.specificity.toString()) 
    { 
        if(a.index === b.index) 
        { 
            return b.depth - a.depth; 
        }
        return a.index - b.index; 
    }

    if(a.specificity[0] !== b.specificity[0]) 
    { 
        return a.specificity[0] - b.specificity[0]; 
    }
    if(a.specificity[1] !== b.specificity[1]) 
    { 
        return a.specificity[1] - b.specificity[1]; 
    }
    if(a.specificity[2] !== b.specificity[2]) 
    { 
        return a.specificity[2] - b.specificity[2]; 
    }
    return a.specificity[3] - b.specificity[3];
});
```

逻辑被稍微改动了一下，以至于一些规则被表达为相反的条件；这是为了提高函数的效率，所以它需要更少的代码来实现，并且它**尽快返回**。同样的条件可能有几种不同的编码方式。

## 关于稳定排序的一个注记

这种技术唯一真正的问题是**稳定排序**的问题，这意味着——如果`a`和`b`相同，那么它们相对于彼此不会改变。问题是稳定排序是针对**可排序值本身**；但是在这些例子中，`a`和`b`是*而不是*它们本身就是我们要进行排序的值，它们仅仅是被排序的值的*容器*。因此，不能保证稳定的排序，实际发生的情况在不同的浏览器中会有所不同(有些会离开它们，有些会移动它们)

就我个人而言，我从来没有发现这种情况有什么意义。但是如果你这样做了，防止它的方法是确保没有两个可排序的对象是完全相同的。例如，您可以为正在排序的每个对象分配一个数字索引属性，以反映它们在数组中的初始顺序。然后在您的比较函数中，添加一个最终条件，当所有其他条件相等时，该条件根据这些索引的值进行排序。因为它们反映了原始顺序，并且都是唯一的，所以只要没有其他排序发生，它们就会有效地维护顺序。

## 分类了！

要记住的最基本的事情是**排序比较函数并没有什么特别或不寻常的**，它只是另一个函数做一些事情然后返回。您可以加载外部数据，创建用于测试呈现的元素，或者执行任意数量的复杂操作。只要函数正确返回——小于零、大于零或零——那么对于如何到达那里就没有特别的限制！

*缩略图鸣谢:[【索伦】](http://www.flickr.com/photos/bricklovinfreakboy/57296165/)*

## 分享这篇文章**