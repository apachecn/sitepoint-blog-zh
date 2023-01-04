# 用 Jsoniter 在 Java 中进行 PHP 风格的 JSON 解析

> 原文：<https://www.sitepoint.com/php-style-json-parsing-in-java-with-jsoniter/>

### 目录 

*   [为什么 JSON 在 Java 中很难处理](#whyjsonishardtoprocessinjava)
*   [原因 1:类型不匹配](#reason1typemismatch)
*   [原因二:异构数据](#reason2heterogeneousdata)
*   [原因 3:性能和灵活性的平衡](#reason3performanceandflexibilitybalance)
*   [JSON ITER 如何解决 JSON 阻抗不匹配](#howjsonitersolvesthejsonimpedancemismatch)
*   [数据绑定](#databinding)
*   [任何数据类型](#anydatatype)
*   [迭代器 API](#iteratorapi)
*   [与任何人同乐](#funwithany)
*   [任何容器](#anycontainer)
*   [缺失值](#missingvalue)
*   [类型转换](#typeconversion)
*   [无模式部分 JSON 处理](#schemalesspartialjsonprocessing)
*   [总结](#summary)
*   [评论](#comments)

JSON 起源于一种弱类型的动态语言 Javascript。JSON 的动态性和 Java 的刚性类型之间存在阻抗不匹配。我发现现有的解决方案过于关注数据绑定的概念，这在某些情况下过于沉重。相比之下，在 PHP 中，我们有一体化的数据类型`Array`，只需一行`json_decode`就可以解析一个复杂的 JSON 文档。Jsoniter 是一个用 Java 编写的新库，决心通过一个类似的数据类型:`Any`，让 Java 中的 JSON 解析像 PHP 中一样简单。最显著的特性是底层的惰性解析技术，这使得解析不仅容易，而且非常快。

## 为什么 JSON 在 Java 中难以处理

使用现有的解析器很难处理 JSON 文档，原因有三。我称之为“JSON 阻抗不匹配”。

### 原因 1:类型不匹配

当 JSON 被用作 Java 和 PHP 等动态语言之间的数据交换格式时，对象字段类型可能会成为一个问题。例如，看看这个 JSON:

```
{
"order_id": 100098,
"order_details": {"pay_type": "cash"}
} 
```

99%的情况下，PHP 代码可能会返回我们期望的精确结构。但是对于不同的输入条件，它也可能返回稍微不同的 JSON，因为大多数 PHP 开发人员并不关心变量是 string 还是 int。

```
{
"order_id": "100098",
"order_details": []
} 
```

为什么`order_details`是空数组而不是空对象？在使用 PHP 时，这是一个常见的问题，因为所有的东西都是一个数组。用作非空映射的数组将像`{"key":"value"}`一样被编码，但空映射只是一个空数组，它将被编码为`[]`而不是`{}`。

这不是一个大问题，肯定是可以修复的，但是对于日志这样的历史数据，我们无论如何都要处理它。

### 原因 2:异构数据

在 Jave 中，我们习惯于同质数据。例如，`[1, 2, 3]`是一个`int`数组，`["1", "2", "3"]`是一个`String`数组。但是如何在 Java 中表示`[1, 2, "3"]`？对象数组`Object[]`很难使用。`[1, ["2", 3]]`怎么样？Java 没有一个方便的容器来保存这种数据。

此外，在 JSON 中，用稍微不同的结构来表示同一事物是很常见的。例如，成功响应:

```
{
"code": 0,
"data": "Success"
} 
```

但是对于错误响应:

```
{
"code": -1,
"error": {"msg": "Wrong Parameter", "stacktrace": "…"}
} 
```

如果我们想得到数据或错误信息，我们已经做了一些空检查。假设响应表示为`Map<String, Object>`，提取错误消息的代码将如下所示:

```
Object errorObj = response.get("error");
if (errorObj == null)
    return "N/A";

Map<String, Object> error = (Map<String, Object>)errorObj;
Object msgObj = errorObj.get("msg");
if (msgObj == null)
    return "N/A";

return (String)msgObj; 
```

类型转换和空值检查一点也不好玩。不幸的是，从五层深度的 JSON 中提取价值是很常见的！

### 原因 3:性能和灵活性的平衡

通过使用 JSON，我们已经选择了灵活性而不是原始性能。但是，把一个 JSON 文档解析成`Map<String, Object>`还是感觉不好，要知道这样做的代价会很大。我不是说我们应该选择性能而不是表现力。但故意妥协表现的罪恶感不断困扰着我。我经常发现自己处于一种两难的境地:

*   将 JSON 解析为`Map<String, Object>`并从中读取值。省去了定义模式类的麻烦，但是我们必须解组所有的字节，不管我们是否需要它们。
*   定义一个类并使用数据绑定。它可以跳过不必要的解析工作，并且访问对象比哈希映射更快。但是每次都这么麻烦值得吗？
*   一些 JSON 解析器带有一个流 API，但是它被认为太低级了。

在完全无类型解析和严格的数据绑定之间还有很长的路要走。如果我们在性能和灵活性之间有更多的选择，或者两者兼而有之，那就更好了。

![Parsing JSON in Java like in PHP with Jsoniter](img/2790eefa4b765b2eb3d49394103f53d7.png)

## Jsoniter 如何解决 JSON 阻抗失配

Jsoniter 是一个新的 Java JSON 库，设计时考虑到了上述问题。(声明:我是它的作者。)Jsoniter 通过以下技术来响应 JSON 阻抗不匹配:

*   数据绑定通过像`MaybeStringLongDecoder`这样的预定义解码器支持“模糊”类型。
*   `Any`数据类型表示 JSON 对象，类似于 PHP 数组的方式。
*   惰性解析只处理请求的字段，其他字节不变。

为了演示如何使用 Jsoniter，让我们先安装它。将以下依赖项添加到您的`pom.xml`(假设您使用的是 Maven):

```
<dependency>
    <groupId>com.jsoniter</groupId>
    <artifactId>jsoniter</artifactId>
    <version>0.9.8</version>
</dependency> 
```

或者你可以[直接下载 jar](http://central.maven.org/maven2/com/jsoniter/jsoniter/0.9.8/jsoniter-0.9.8.jar)。

Jsoniter 是一个灵活的解析器，有 3 个 API 可供选择

*   数据绑定到 Java 类，其中底层实现可以是基于反射的[，静态代码生成，或者动态代码生成](http://jsoniter.com/java-features.html#performance-is-optional)。
*   一个数据类型为`Any`的惰性解析 API，它用一小部分代码解析相同的 JSON。
*   迭代器 API，像 for 循环一样遍历 JSON，最适合处理大批量。

你也不会被迫一直坚持使用一个 API。为正确的工作使用正确的 API，[为复杂的情况组合它们](http://jsoniter.com/java-features.html#right-api-for-the-right-job)。现在我将向您展示如何使用这三个 API 轻松处理 JSON。

### 数据绑定

Jsoniter 不强迫您使用`Any`类型。在很多情况下，数据绑定仍然是最舒适的 API。

#### 简单的类

让我们绑定这个简单的例子:

```
{
"order_id": 100098,
"order_details": {"pay_type": "cash"}
} 
```

为此，我们将设计这样一个类:

```
public class Order {
    public long order_id;
    public OrderDetails order_details;
}

public class OrderDetails {
    public String pay_type;
} 
```

为了反序列化 JSON 输入，我们将使用`JsonIterator`:

```
Order order = JsonIterator.deserialize(input, Order.class); 
```

输入可以是`String`或`byte[]`。如果您需要使用一个`InputStream`作为输入，它会稍微冗长一点:

```
JsonIterator iter = JsonIterator.parse(input);
Order order = Iter.read(Order.class);
// you can close the underlying InputStream via Iter
// or directly (it does not have its own resource to dispose)
Iter.close(); 
```

#### 注释的情况

每个人都知道简单绑定是如何工作的。但是杂乱的输入怎么办？

```
{
"order_id": "100098",
"order_details": []
} 
```

在这种情况下，我们将需要注释支持。首先，我们通过以下方式启用这一可选功能:

```
JsoniterAnnotationSupport.enable(); 
```

这个只需要做一次，可以放在主函数或者静态初始化器里。现在向`Order`类添加注释

```
public class Order {
  @JsonProperty(decoder = MaybeStringLongDecoder.class)
  public long order_id;
  @JsonProperty(decoder = MaybeEmptyArrayDecoder.class)
  public OrderDetails order_details;
} 
```

通过使用 Maybe 解码器，我们可以在某些情况下使数据类型的绑定变得模糊。如果结构本身是“动态”的，我们最好用`Any`来代替。

### `Any`数据类型

我们可以使用`Any`数据类型，而不是定义描述数据模式的类。它基本上是`Map<String, Object>`或`List<Object>`的替代品。让我们像以前一样阅读相同的 JSON:

```
{
"order_id": 100098,
"order_details": {"pay_type": "cash"}
} 
```

下面是实现这一点的代码:

```
Any order = JsonIterator.read(input);
String payType = order.toString("order_details", "pay_type"); 
```

`toString`方法可能看起来很奇怪，所以让我解释一下:

*   获取`"order_details"`
*   然后从`"order_details"`中获取`"pay_type"`
*   然后将`"pay_type"`的值从任何类型转换成字符串

即使在以下情况下，代码仍然有效，因为它将`5`转换为`"5"`:

```
{
"order_details": {"pay_type": 5}
} 
```

如果输入不是我们期望的，例如:

```
{
"order_details": []
} 
```

代码`toString("order_tails", "pay_type")`不会抛出`NullPointerException`，而是返回空字符串。大多数时候，空字符串就是我们所期望的。

值得一提的是，解析是懒洋洋地完成的。对于那些不读取的部分，它们将以字节数组的形式保存，从而节省了完全反序列化的成本。`Any`非常强大，我们将在查看访问 JSON 的第三种方式后详细介绍它。

### 迭代器 API

迭代器 API 将 JSON 数据流公开为迭代器。您可以使用以下方法来驱动迭代过程:

*   `whatIsNext`:向前看下一个值的类型。它返回了一个`ValueType`枚举的实例，稍后我会再回到这个实例。使用这个方法是可选的——例如，如果你知道下一个值必须是一个字符串，你可以直接调用`readString`,而不用先检查`whatIsNext`。
*   `readObject`:读取下一个对象字段，返回字段名。
*   `readArray`:读取下一个数组元素，如果到达数组末尾，返回 false。
*   `readString`:以字符串形式读取单个值。

让我们使用这个输入示例:

```
{"numbers": ["1", "2", ["3", "4"]]} 
```

我编写了一个 JUnit 测试来演示迭代器 API:

```
JsonIterator iter = JsonIterator.parse(
        "{'numbers': ['1', '2', ['3', '4']]}"
            .replace('\'', '"'));
// start reading the first object ("number")
assertEquals("numbers", iter.readObject());
// start reading the array
assertTrue(iter.readArray());
assertEquals("1", iter.readString());
assertTrue(iter.readArray());
assertEquals("2", iter.readString());
// start reading the inner array
assertTrue(iter.readArray());
// you can know the type of next value before reading it
assertEquals(ValueType.ARRAY, iter.whatIsNext());
assertTrue(iter.readArray());
assertEquals(ValueType.STRING, iter.whatIsNext());
assertEquals("3", iter.readString());
assertTrue(iter.readArray());
assertEquals("4", iter.readString());
// end inner array
assertFalse(iter.readArray());
// end outer array
assertFalse(iter.readArray());
// end object "number"
assertNull(iter.readObject()); 
```

它实际上就像它的名字所暗示的那样，是一个迭代器:你调用一个方法，它就向前移动。

## 与`Any`同乐

很有趣，我们再来点。

### 任何容器

Any 是一个容器，可以容纳所有类型的值:

*   懒惰对象
*   惰性数组
*   懒惰字符串
*   懒惰替身
*   慵懒的歌
*   非惰性值(数组、对象、字符串、浮点、双精度、长整型、整型、真、假、空)

如果包含的值是一个对象或数组，我们可以提取元素而不需要转换成`List`或`Map`。

例如 _

```
[{"score":100}, {"score":102}] 
```

我们可以只使用路径来提取价值:

```
// will be 100
JsonIterator.deserialize(input).toInt(0, "score") 
```

第一个参数`0`获取数组中的第一个元素。第二个参数`"score"`获取对象的分数。

或者我们可以像集合一样迭代这个值:

```
Any records = JsonIterator.deserialize(input);
for (Any record : records) {
    Any.EntryIterator entryIterator = record.entries();
    while (entryIterator.next()) {
        System.out.println(
                entryIterator.key() + " / " + entryIterator.value());
    }
}
// output is:
// score / 100
// score / 102 
```

迭代器一直在进行解析。如果在中间停止循环，解析将部分完成。一旦我们提取了我们需要的值，这就避免了不必要的解析。

我们甚至可以在提取路径中使用通配符:

```
Any records = JsonIterator.deserialize(input);
// [100, 102]
records.get('*', "score") 
```

这将提取一个带有列表值的`Any`，包含每条记录的分数。

### 漏测值

让我们重温一下前面的例子

```
{
"order_details": []
} 
```

正如我之前解释的，`toString("order_tails", "pay_type")`将返回空字符串。这就是`toString`处理缺失值的方式。如果我们改成`get("order_details", "pay_type")`，它能告诉我们这个值实际上是缺失的:

```
Any payType = order.get("order_details", "pay_type");
if (payType.valueType() == ValueType.INVALID) {
    // not found
} 
```

如果试图使用无效的`Any`实例，将会抛出一个异常。在这种情况下，`Any`很像 [Java 8 的`Optional`](https://www.sitepoint.com/how-optional-breaks-the-monad-laws-and-why-it-matters/) 。可能的值类型有:

*   `INVALID`
*   `STRING`
*   `NUMBER`
*   `NULL`
*   `BOOLEAN`
*   `ARRAY`
*   `OBJECT`

我们可以看到，即使是“null”JSON 实际上也不是 Java 意义上的`null`。它将被表示为一个带有`valueType() == ValueType.NULL`的`Any`实例。从可能的返回值中删除 null 使得从深度嵌套的结构中提取值更加方便，因为不再需要一直检查 null。

通配符路径提取也支持缺失值:

```
// input is [{"score":100}, {"value":102}]
Any records = JsonIterator.deserialize(input);
// [100]
records.get('*', "score") 
```

因为在第二条记录中没有找到“score ”,所以它将被排除在结果之外。

### 类型变换

`toString`方法只是支持的转换之一，其他还有:

*   `toInt`
*   `toLong`
*   `toDouble`
*   `toFloat`
*   `toBoolean`

每次转换都会尽最大努力把原始值转换成你想要的类型。

除了简单类型，您还可以通过数据绑定将值转换为复杂类型。例如，我们可以使用`Any`提取值，然后绑定到一个对象。

```
// {"numbers": ["1", "2", ["3", "4"]]}
String[] numbers = JsonIterator
        .deserialize(input)
        .get("numbers", 2)
        .as(String[].class); 
```

`as` API 使用前面解释的数据绑定将`["3", "4"]`绑定到`String[]`对象。

### 无模式的部分 JSON 处理

`Any`也是可变的，可以序列化回 JSON。如果你只想把原来的输入改一点点再写回来，`Any`会真的得心应手。它将以原始字节的形式捕获输入，并按原样写回 JSON，不仅节省了反序列化的成本，还节省了序列化的成本。底层优化会自动发生，无需您的参与——您编写代码就像在使用`Map<String, Object>`或`List<Object>`一样:

```
List numbers = JsonIterator.deserialize("[1,[2, 3],4]").asList();
numbers.add(5);
// will be [1,[2,  3],4,5]
JsonStream.serialize(numbers); 
```

这是部分处理——很难注意到神奇之处。

*   调用`asList`时，列表将包含 3 个`Any`元素，分别代表原始字节数组中的`1`、`[2, 3]`和`4`，不进行解析。
*   当添加`5`时，前 3 个列表元素仍然是类型`Any`，但是第 4 个是类型`java.lang.Integer`。
*   当我们将列表序列化为 JSON 形式时，前 3 个元素没有序列化成本，因为它仍然是字节数组形式，将被直接字节复制。只有第 4 个元素会从整数转换成字符串。

这项技术提供了一种全新的处理 JSON 的方法。传统上，我们以这种形式编写我们的逻辑:

*   JSON = >对象图= >修改的对象图=> JSON

有了`Any`，我们可以保存很多对象:

*   JSON = >惰性对象图= >部分解析和修改的对象图=> JSON

## 摘要

JSON 是一种灵活的格式，用 PHP 等语言编写的代码产生的输出通常很难用 Java 处理。与大多数现有的解析器不同，Jsoniter 选择拥抱动态特性，而不是埋葬它。创新的`Any`数据类型使得在 Java 中解析具有不确定类型和不确定结构的 JSON 变得容易。对于惰性解析，这种无模式风格的解析甚至更有吸引力。虽然非常灵活，但性能并没有受到影响。

## 分享这篇文章