# 五分钟后 Java 的二分搜索法 API

> 原文：<https://www.sitepoint.com/javas-binary-search-api-tutorial/>

### 目录 

*   [用二分搜索法搜索](#searchingwithbinarysearch)
*   [搜索数组](#searchinganarray)
*   [搜索范围](#searchingarange)
*   [用比较器搜索](#searchingwithacomparator)
*   [搜索列表](#searchingalist)
*   [用于插入的二分搜索法](#binarysearchforinsertion)
*   [结束](#wrappingup)
*   [评论](#comments)

二分搜索法是一种非常有效的搜索算法，它通过反复将搜索范围减半来在排序后的数组中查找项目。因此，运行时间是对数的，即 *O* (log *n* )。这篇文章教你如何使用 Java 的`Arrays.binarySearch`和`Collections.binarySearch`来搜索排序数组或数组列表，这比使用线性搜索方法如`for`循环或`List.contains`要快得多。

至少，只需要知道[数组](https://www.sitepoint.com/beginning-java-data-types-variables-and-arrays/)和[列表](https://docs.oracle.com/javase/tutorial/collections/interfaces/list.html)的工作知识。了解一下 [`Comparable`和`Comparator`](http://www.mkyong.com/java/java-object-sorting-example-comparable-and-comparator/) 就派上用场了。

## 用二分搜索法搜索

Java 的二分搜索法函数可以在`java.util.Arrays`和`java.util.Collections`API 中找到。`Arrays` API 提供了数组的实现。

### 搜索数组

最简单的形式是，静态方法`Arrays.binarySearch`接受一个任意原始类型(以及对象或引用类型)的数组和一个搜索关键字，例如:

*   [T2`static int binarySearch(int[] a, int key)`](https://docs.oracle.com/javase/8/docs/api/java/util/Arrays.html#binarySearch-int:A-int-)
*   [T2`static int binarySearch(char[] a, char key)`](https://docs.oracle.com/javase/8/docs/api/java/util/Arrays.html#binarySearch-char:A-char-)
*   [T2`static int binarySearch(Object[] a, Object key)`](https://docs.oracle.com/javase/8/docs/api/java/util/Arrays.html#binarySearch-java.lang.Object:A-java.lang.Object-)

这些方法返回一个代表在数组中找到的搜索关键字的索引的`int`,或者如果没有找到，返回一个负值(我稍后会解释它的意义)。

下面是一个代码示例:

```
int[] numbers = {2, 3, 5, 7};
System.out.println("index of 5 is "
        + Arrays.binarySearch(numbers, 5));
// index of 5 is 2 
```

在使用二分搜索法函数之前，确保数组已经按升序排序(例如，用`Arrays.sort`方法)，否则结果将是意想不到的。

### 搜索范围

如果应该只在指定数组的某个范围内搜索搜索关键字，可以使用此重载:

*   [T2`static int binarySearch(int[] a, int fromIndex, int toIndex, int key)`](https://docs.oracle.com/javase/8/docs/api/java/util/Arrays.html#binarySearch-int:A-int-int-int-)

请注意，`toIndex`是专用于搜索的。在前面的示例中，如果您只想从索引 0 到 2(包括 0 和 2)搜索数组，您可以编写:

```
Arrays.binarySearch(numbers, 0, 3, 5); 
```

### 使用比较器搜索

如果对一个`Object`数组使用二分搜索法方法，数组的元素必须要么实现`Comparable`接口，要么必须指定一个`Comparator`:

*   [T2`static int binarySearch(T[] a, T key, Comparator<? super T> c)`](https://docs.oracle.com/javase/8/docs/api/java/util/Arrays.html#binarySearch-T:A-T-java.util.Comparator-)

二分搜索法使用`Comparator`对象来比较两个对象的顺序，以便确定在搜索过程中一个对象在数组中是在另一个对象之前还是之后。换句话说，API 没有将`Object`数组限制为升序/自然顺序。

该数组可以是任何排序顺序，只要您可以提供一个`Comparator`，它强制一个与该数组的排序顺序一致的顺序。在最简单的情况下，你已经用同一个比较器用 [`Arrays.sort(T[] a, Comparator<? super T> c)`](https://docs.oracle.com/javase/8/docs/api/java/util/Arrays.html#sort-T:A-java.util.Comparator-) 对数组进行了排序。

下面是一个按降序搜索一个`Integer`数组的例子:

```
Integer[] numbers = {2, 3, 5, 7};
// sort numbers into reverse natural order, i.e. {7, 5, 3, 2}
Arrays.sort(numbers, Collections.reverseOrder());

// search using a reverse natural order comparator
System.out.println("index of 5 is " +
        Arrays.binarySearch(numbers, 5, Collections.reverseOrder()));
// index of 5 is 1 
```

### 搜索列表

另一方面，Java 的`Collections`类提供了在`List`上操作的二分搜索法函数。重载的`Collections.binarySearch`方法的两个版本是:

*   [T2`static int binarySearch(List<? extends Comparable<? super T>> list, T key)`](https://docs.oracle.com/javase/8/docs/api/java/util/Collections.html#binarySearch-java.util.List-T-)
*   [T2`static int binarySearch(List<? extends T> list, T key, Comparator<? super T> c)`](https://docs.oracle.com/javase/8/docs/api/java/util/Collections.html#binarySearch-java.util.List-T-java.util.Comparator-)

这些方法的用法与`Arrays.binarySearch`相似，除了这些方法接受一个`List`对象而不是一个数组。

这里有一个例子:

```
List<Integer> numbers = Arrays.asList(2, 3, 5, 7);
System.out.println("index of 5 is "
        + Collections.binarySearch(numbers, 5));
// index of 5 is 2 
```

> 警告:在实现只允许顺序访问其元素的`LinkedList`上使用二分搜索法是不明智的，这会导致二分搜索法算法性能非常差。

## 插入用二分搜索法

当在指定的数组或列表中找不到给定的搜索关键字时，`Arrays.binarySearch`和`Collections.binarySearch`的 API 都有一个特殊的行为:在这种情况下，两个类中的二分搜索法方法都返回一个等价于`- <insertion point> - 1`的负值，其中插入点被定义为搜索关键字将被插入数组或列表的索引。

这里有一个例子:

```
List<Integer> numbers = new ArrayList<>(
        Arrays.asList( 2, 3, 5, 7, 11, 13, 17, 19, 23, 29));

System.out.println("index of 5 is "
        + Collections.binarySearch(numbers, 5));
System.out.println("index of 23 is "
        + Collections.binarySearch(numbers, 23));
System.out.println("index of 9 is "
        + Collections.binarySearch(numbers, 9));
System.out.println("index of 31 is "
        + Collections.binarySearch(numbers, 31)); 
```

输出:

```
index of 5 is 2
index of 23 is 8
index of 9 is -5
index of 31 is -11 
```

在这个例子中，整数`9`和`31`不存在于列表中。使用返回的负值，`9`将被插入到索引`4`处，将索引`4`处的原始条目向后推一个槽。`31`的值将被插入到列表末尾的索引`10`处。如果我们要将`9`插入到列表中，我们可以编写这样的代码:

```
int pos = Collections.binarySearch(numbers, 9);
if (pos < 0) {
    numbers.add(Math.abs(pos)-1, 9);
} 
```

二分搜索法的这一特性对于实现某种优先级队列的插入功能特别有用，例如，它使用一个数组来按升序存储其条目。插入点可用于将新条目相应地插入队列。

![Binary search an array or list](img/31d031f166096c39e673eb87f57e4494.png)

## 包扎

Java 在`java.util.Arrays`和`java.util.Collections`上的标准二分搜索法方法允许你在一个数组或列表中找到一个项目或定位一个新项目的插入点。二分搜索法只对排序后的数组或列表有效。它具有对数运行时间，这使得它对于大数据结构非常有效:给定一个大小为 100，000 的排序数组，二分搜索法查找一个项目所需的最大步骤是 log(100，000) / log(2) ≈ 17。在小型数据结构上，增加的开销会使二分搜索法比线性扫描慢。

## 分享这篇文章