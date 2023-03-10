# Java 8 流:过滤、映射和归约操作介绍

> 原文：<https://www.sitepoint.com/java-8-streams-filter-map-reduce/>

通过我们在 SitePoint 上的教程[在 Android 开发中桥接 Android 和 Java](https://www.sitepoint.com/premium/screencasts/bridging-android-and-java-in-android-development)，了解更多关于 Java 的知识。

在躲避了多年之后，Java 终于在 2014 年春天接受了函数式编程构造。Java 8 包括对 lambda 表达式的支持，并提供了一个强大的 Streams API，允许您以一种全新的方式处理元素序列，比如列表和数组。

在本教程中，我将向您展示如何创建流，然后使用三种广泛使用的名为`map`、`filter`和`reduce`的高阶方法来转换它们。

这个帖子的代码可以在[这里](https://github.com/sitepoint-editors/MapFilterReduceOperations)找到。

## 创建流

从它的名字可以看出，流就是一系列的项目。虽然有很多创建流的方法，但是现在，我们将只关注从列表和数组生成流。

在 Java 8 中，每个实现了`java.util.Collection`接口的类都有一个`stream`方法，允许你将其实例转换成`Stream`对象。因此，将任何列表转换成流非常容易。下面是一个将`Integer`对象的`ArrayList`转换成`Stream`的例子:

```
// Create an ArrayList
List<Integer> myList = new ArrayList<Integer>();
myList.add(1);
myList.add(5);
myList.add(8);

// Convert it into a Stream
Stream<Integer> myStream = myList.stream(); 
```

如果你更喜欢数组而不是列表，你可以使用`Arrays`类中的`stream`方法将任何数组转换成一个流。这是另一个例子:

```
// Create an array
Integer[] myArray = {1, 5, 8};

// Convert it into a Stream
Stream<Integer> myStream = Arrays.stream(myArray); 
```

![stream](img/0fd72a065151486ac8f83e3ecd644585.png)

## `map`法

一旦有了一个`Stream`对象，就可以用各种方法将其转换成另一个`Stream`对象。我们要看的第一个这样的方法是`map`方法。它将 lambda 表达式作为唯一的参数，并使用它来改变流中的每个元素。它的返回值是一个包含已更改元素的新的`Stream`对象。

为了给你一个真实的例子，让我向你展示如何使用`map`将字符串数组中的所有元素转换成大写。

首先将数组转换成一个`Stream`:

```
String[] myArray = new String[]{"bob", "alice", "paul", "ellie"};
Stream<String> myStream = Arrays.stream(myArray); 
```

然后调用`map`方法，传递一个 lambda 表达式作为唯一的参数，该表达式可以将字符串转换成大写:

```
Stream<String> myNewStream = 
             myStream.map(s -> s.toUpperCase()); 
```

返回的`Stream`对象包含更改后的字符串。要将其转换成数组，可以使用它的`toArray`方法:

```
String[] myNewArray =
             myNewStream.toArray(String[]::new); 
```

此时，你有了一个字符串数组，所有的都是大写的。

我希望您现在开始意识到，使用这种编程风格，您可以消除循环，并且您编写的代码可以非常简洁和易读。

## `filter`法

在上一节中，您看到了`map`方法处理了`Stream`对象中的每个元素。你可能并不总是希望这样。有时，您可能只想处理元素的子集。为此，您可以使用`filter`方法。

就像`map`方法一样，`filter`方法期望一个 lambda 表达式作为它的参数。然而，传递给它的 lambda 表达式必须总是返回一个`boolean`值，该值决定被处理的元素是否应该属于结果`Stream`对象。

例如，如果您有一个字符串数组，并且您想要创建它的一个子集，该子集只包含那些长度超过四个字符的字符串，那么您必须编写以下代码:

```
Arrays.stream(myArray)
      .filter(s -> s.length() > 4)
      .toArray(String[]::new); 
```

上面的代码看起来比我们在前一个例子中编写的代码要简洁得多，因为我已经链接了所有的`Stream`方法。大多数开发人员更喜欢用这种方式编写函数代码，因为通常不需要存储对中间`Stream`对象的引用。

## 归约操作

归约操作允许您使用流中存在的所有元素来计算结果。归约操作也被称为终结操作，因为它们总是出现在一串`Stream`方法的末尾。我们已经在前面的例子中使用了一种归约方法:方法`toArray`。这是一个终端操作，因为它将一个`Stream`对象转换成一个数组。

Java 8 包括几个归约方法，比如`sum`、`average`、`count`，允许对`Stream`对象进行算术运算，得到数字作为结果。例如，如果要计算整数数组的总和，可以使用下面的代码:

```
int myArray[] = { 1, 5, 8 };
int sum = Arrays.stream(myArray).sum(); 
```

然而，如果你想执行更复杂的归约操作，你必须使用`reduce`方法。与`map`和`filter`方法不同，`reduce`方法需要两个参数:一个 identity 元素和一个 lambda 表达式。您可以将 identity 元素视为不会改变归约运算结果的元素。例如，如果您试图找出一个数字流中所有元素的乘积，那么 identity 元素将是数字 1。

传递给`reduce`方法的 lambda 表达式必须能够处理两个输入:归约操作的部分结果和流的当前元素。如果您想知道什么是部分结果，它是在处理当前元素之前的所有流元素后获得的结果。

下面是一个示例代码片段，它使用`reduce`方法连接一个`String`对象数组中的所有元素:

```
String[] myArray = { "this", "is", "a", "sentence" };
String result = Arrays.stream(myArray)
                .reduce("", (a,b) -> a + b); 
```

## 结论

现在你已经知道了足够的知识，可以开始在你的项目中使用`map`、`filter`和`reduce`方法。为了简洁起见，在本教程中，我只使用了串行流。如果您有计算密集型的地图操作，或者如果您希望您的流非常大，您应该考虑使用并行流。幸运的是，将任何流转换成它的并行等价物是非常容易的。你需要做的就是调用它的`parallel`方法。

我还想让你知道，如果你不喜欢在使用`map`、`filter`和`reduce`方法时使用 lambda 表达式，你总是可以使用方法引用来代替。

要了解更多关于 Streams API 和它所提供的其他方法，您可以参考它的[文档](https://docs.oracle.com/javase/8/docs/api/java/util/stream/package-summary.html)。

通过我们在 SitePoint 上的教程[在 Android 开发中桥接 Android 和 Java](https://www.sitepoint.com/premium/screencasts/bridging-android-and-java-in-android-development)，了解更多关于 Java 的知识。

## 分享这篇文章