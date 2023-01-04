# 基于特性的 Vavr 测试

> 原文：<https://www.sitepoint.com/property-based-testing-with-vavr/>

### 目录 

*   [在我们开始之前](#beforewestart)
*   [基于属性的测试](#propertybasedtesting)
*   [反向列表示例](#reverselistexample)
*   [属性检查 FizzBuzz](#propertycheckingfizzbuzz)
*   [每三个元素应该以 Fizz 开始](#everythirdelementshouldstartwithfizz)
*   [增加样本量](#increasethesamplesize)
*   [每个 5 的倍数都必须以 Buzz 结束](#everymultipleof5mustendwithbuzz)
*   [每一个非第三和非第五元素都应该是一个数字](#everynonthirdandnonfifthelementshouldremainanumber)
*   [其他属性](#otherproperties)
*   [利与弊](#benefitsanddisadvantages)
*   [总结](#summary)
*   [评论](#comments)

通常的单元测试是基于例子的:我们为我们的单元定义输入并检查预期的输出。因此，我们可以确保对于特定的样本，我们的代码工作正常。然而，这并不能证明所有可能输入的正确性。基于属性的测试，也称为属性检查，通过坚持科学理论[的可证伪性](https://en.wikipedia.org/wiki/Falsifiability)采取了另一种方法:只要没有证据表明我们代码的一个提议属性是假的，它就被认为是真的。在本文中，我们使用 [Vavr](http://www.vavr.io/) ( [以前称为 Javaslang](https://twitter.com/vavr_io/status/853014593303609346) )进行基于属性的测试，以验证一个简单的 FizzBuzz 实现。

## 开始之前

我正在使用 Vavr `Stream` s 来生成一个无限的 FizzBuzz 序列，它应该满足某些属性。与 Java 8 `Stream` s 相比，本文范围内的主要好处是 Vavr 变体提供了一个很好的 API，可以用[的`get`函数](http://static.javadoc.io/io.javaslang/javaslang/2.0.3/javaslang/collection/Stream.html#get-int-)来检索单个元素。

## 基于属性的测试

基于属性的测试(PBT)允许开发者根据程序应该满足的[不变量](https://en.wikipedia.org/wiki/Invariant_(computer_science)来指定程序的高级行为。PBT 框架创建测试用例来确保程序按照指定的方式运行。

*属性*是不变量与输入值生成器的组合。对于每个生成的值，不变量被视为谓词，并检查它是否为该值生成 true 或 false。

只要有一个值产生假，该属性就被认为是*伪造的*，并且检查被中止。如果在特定数量的样本数据之后，一个属性不能被证伪，则该属性被假定为满足*。*

 *### 反向列表示例

以一个反转列表的函数为例。为了提供正确的功能，它有几个必须保持的隐式不变量。其中之一是，一个列表的第一个元素是同一个列表的最后一个元素。一个相应的属性将这个不变量与一个生成器结合起来，该生成器生成具有随机值和长度的列表。

假设发生器产生以下输入值:`[1,2,3]`、`["a","b"]`和`[4,5,6]`。list reverse 函数为`[1,2,3]`返回`[3,2,1]`。列表的第一个元素是同一个列表的最后一个元素。检查结果为真。list reverse 函数为`["a", "b"]`返回`["a", "b"]`。列表的第一个元素是**而不是**同一个列表的最后一个元素被反转。检查结果为假。财产是伪造的。

在 Vavr 中，尝试伪造属性的默认次数是 1000。

## 属性检查 FizzBuzz

FizzBuzz 是一种从 1 开始计数的儿童游戏。每个孩子依次大声说出当前的数字。如果当前数字能被 3 整除，孩子应该说“嘶嘶”，如果能被 5 整除，答案应该是“嗡嗡”，如果能被 3 和 5 整除，答案应该是“嘶嘶”。游戏是这样开始的:

> 1，2，嘶嘶，4，嘶嘶，嘶嘶，7，8，嘶嘶，嘶嘶，11，嘶嘶，13，14，嘶嘶，…

FizzBuzz 经常被用作求职面试中的一个样本练习，应聘者应该从 1 到 100 用 fizzbuzz 说出数字。FizzBuzz 也可以用作一个足够简单的形，用于关注语言、库或工具等环境，而不是业务逻辑。

流似乎是一个建模 FizzBuzz 的好方法。我们将从一个空的开始，并逐步改进它以通过我们的测试。

```
public Stream<String> fizzBuzz() {
    Stream.empty();
} 
```

### 每三个元素中就应该有一个以 Fizz 开头

从对 FizzBuzz 的描述中，我们可以得出 FizzBuzz `Stream`的第一个属性:每三个元素应该以 *Fizz* 开头。Vavr 的属性检查实现提供了流畅的 API 来创建生成器和属性。

```
@Test
public void every_third_element_starts_with_Fizz) {
    Arbitrary<Integer> multiplesOf3 = Arbitrary.integer()
        .filter(i -> i > 0)
        .filter(i -> i % 3 == 0);

    CheckedFunction1<Integer, Boolean> mustStartWithFizz = i ->
        fizzBuzz().get(i - 1).startsWith("Fizz");

    CheckResult result = Property
        .def("Every third element must start with Fizz")
        .forAll(multiplesOf3)
        .suchThat(mustStartWithFizz)
        .check();

    result.assertIsSatisfied();
} 
```

让我们一个一个来看:

*   `Arbitrary.integer()`生成任意整数，使用`filter`过滤掉 3 的正倍数。
*   函数`mustStartWithFizz`是不变量:对于给定的索引，流中相应的元素(`get`的索引为 0)必须以 *Fizz* 开始。对下面的`suchThat`的调用需要一个`CheckedFunction1`，它类似于一个函数，但是可能抛出一个检查异常(这里我们不需要)。
*   `Property::def`进行简短描述并创建一个`Property`。对`forAll`的调用需要一个`Arbitrary`，并确保`suchThat`中的不变量必须适用于由`Arbitrary`生成的所有输入值。
*   `check`函数使用生成器生成 0 到 100 范围内的 3 的倍数。它返回一个可以查询结果或断言的`CheckResult`。

属性不满足，因为对于每一个生成的 3 的倍数，对应的元素在空的`Stream`中不存在。让我们通过提供一个合适的流来解决这个问题。

```
public Stream<String> fizzBuzz() {
    return Stream.from(1).map(i -> i % 3 == 0 ? "Fizz": "");
} 
```

创建一个无限流，从给定的数字开始计数。每个 3 的倍数都被映射到 *Fizz* 。生成的流中每三个元素中就有一个*气泡*。一旦我们再次检查该属性，它就会得到满足，结果会打印在控制台上:

> 每三个元素必须以 Fizz 开头:OK，在 111 毫秒内通过 1000 次测试。

![Property Based Testing with Vavr](img/0f1c54fcdec52306f0b2868837660eb5.png)

### 增加样本量

默认情况下，`check`试图伪造一个属性 1000 次。每次，给定的`Arbitrary`产生一个取决于`size`参数的样本值。默认情况下，size 参数设置为 100。对于`Arbitrary.integer()`,负尺寸参数定义下限，正尺寸参数定义生成值的上限。这意味着，生成的整数的值介于-100 和 100 之间。我们进一步限制生成的值只能是正数，并且是 3 的倍数。这意味着，我们在 1 到 100 的范围内测试 3 的每个倍数大约 30 次。

幸运的是，Vavr 允许我们通过向 check 函数提供相应的参数来调整尝试的大小和次数。

```
CheckResult result = Property
    .def("Every third element must start with Fizz")
    .forAll(multiplesOf3)
    .suchThat(mustStartWithFizz)
    .check(10_000, 1_000); 
```

现在，生成的 3 的倍数是从 1 到 10'000 的范围内抽取的，并且属性被检查 1000 次。

### 每个 5 的倍数都必须以 Buzz 结尾

从 FizzBuzz 描述中可以得出的第二个属性是，5 的每个倍数必须以 *Buzz* 结尾。

```
@Test
public void every_fifth_element_ends_with_Buzz() {
    Arbitrary<Integer> multiplesOf5 = Arbitrary.integer()
        .filter(i -> i > 0)
        .filter(i -> i % 5 == 0);

    CheckedFunction1<Integer, Boolean> mustEndWithBuzz = i ->
        fizzBuzz().get(i - 1).endsWith("Buzz");

    Property.def("Every fifth element must end with Buzz")
        .forAll(multiplesOf5)
        .suchThat(mustEndWithBuzz)
        .check(10_000, 1_000)
        .assertIsSatisfied();
} 
```

这一次，属性检查和断言被结合在一起。

这个属性是伪造的，就像最初检查 *Buzz* 的属性一样。但是这次不是因为没有元素(自从上次测试以来,`fizzBuzz`函数创建了一个无限流),而是因为每第五个元素是一个空字符串，或者有时*嘶嘶作响*但从不*嗡嗡作响*。

让我们通过为每第五个元素返回 *Buzz* 来解决这个问题。

```
public Stream<String> fizzBuzz() {
    return Stream.from(1).map(i ->
        i % 3 == 0 ? "Fizz" : (i % 5 == 0 ? "Buzz" : "")
    );
} 
```

当我们再次检查该属性时，它在一些通过的测试后被伪造:

> 每五个元素必须以 Buzz 结尾:在 56 毫秒内通过 7 次测试后被证伪。

`AssertionError`中的信息描述了伪造属性的样本:

> 预期满意的检查结果，但被伪造(propertyName =每第五个元素必须以 Buzz 结尾，count = 8，sample = (30))。

原因是，30 可以被 3 和 5 整除，但是在上述`fizzBuzz()`实现中，被 3 整除的优先级高于被 5 整除的优先级。然而，改变优先顺序并不能解决问题，因为第一个属性，即每三个元素都应该以 *Fizz* 开头，将会被证伪。一个解决方案是返回 *FizzBuzz* 如果它能被 3 和 5 整除。

```
private Stream<String> fizzBuzz() {
    return Stream.from(1).map(i -> {
        boolean divBy3 = i % 3 == 0;
        boolean divBy5 = i % 5 == 0;

        return divBy3 && divBy5 ? "FizzBuzz" :
            divBy3 ? "Fizz" :
            divBy5 ? "Buzz" : "";
        });
} 
```

这两种特性都满足这种方法。

### 每一个非第三和非第五元素都应该是一个数字

从 FizzBuzz 描述中可以得到的第三个属性是，每个非第三和非第五元素都应该是一个数字。

```
@Test
public void every_non_fifth_and_non_third_element_is_a_number() {
    Arbitrary<Integer> nonFifthNonThird = Arbitrary.integer()
        .filter(i -> i > 0)
        .filter(i -> i % 5 != 0)
        .filter(i -> i % 3 != 0);

    CheckedFunction1<Integer, Boolean> mustBeANumber = i ->
        fizzBuzz().get(i - 1).equals(i.toString());

    Property.def("Non-fifth and non-third element must be a number")
        .forAll(nonFifthNonThird)
        .suchThat(mustBeANumber)
        .check(10_000, 1_000)
        .assertIsSatisfied();
    } 
```

对该属性的检查失败了，因为到目前为止，`fizzBuzz`函数将非 *Fizz* 和非 *Buzz* 数字映射到空字符串。返回数字解决了这个问题。

```
private Stream<String> fizzBuzz() {
    return Stream.from(1).map(i -> {
    boolean divBy3 = i % 3 == 0;
    boolean divBy5 = i % 5 == 0;

    return divBy3 && divBy5 ? "FizzBuzz" :
        divBy3 ? "Fizz" :
        divBy5 ? "Buzz" : i.toString();
    });
} 
```

### 其他属性

检查这三个属性就足以塑造`fizzBuzz`函数了。不需要特殊的 *FizzBuzz* 属性，因为这个案例已经包含在 *Fizz* 和 *Buzz* 属性中。然而，我们可以添加另一个属性，它应该*而不是*适用于所有元素。例如， *FizzBuzz* 中的 *Fizz* 和 *Buzz* 之间不应有空白。我认为这是一个很好的练习，我让你，我亲爱的读者，来定义和检查这样一个属性。

然而，我们的测试中还有另一个缺陷:我们的属性不够具体。检查一个元素是以 *Fizz* 开始还是以 *Buzz* 结束，对于 3 和 5 的任意倍数，只包含 *FizzBuzz* 的流也能满足。你能修理我们的财产吗？

## 优点和缺点

如前所述，PBT 的优势在于可以专注于指定程序的预期行为，而不是定义每个边界情况。这将测试转移到一个更加声明性的层次。

与[其他实现相比](https://github.com/pholser/junit-quickcheck) Vavr 提供了一个很好的流畅的 API，它对于所使用的测试框架来说并不显眼。它与 JUnit 4、 [JUnit 5](https://www.sitepoint.com/junit-5-state-of-the-union/) 和 TestNG 一起工作。Spock 有 BDD 思维，我不确定术语*规范*在 Spock 和 PBT 中是否有相同的含义，但这可以在另一篇文章中探讨。这似乎是对数据驱动方法的一个很好的补充。

一个缺点是罕见的错误可能在第一次运行时没有被发现，并且可能会阻碍后面的构建。此外，覆盖报告可能会因构建而异。然而，这是微不足道的。

PBT 的关键部分是底层随机数生成器(RNG)的性能如何，以及它的播种性能如何。一个坏的 RNG 产生一个坏的测试样本分布，这可能导致被测代码的“盲点”。Vavr 依赖于`ThreadLocalRandom`，这不仅仅是好的。

Vavr 目前支持的类型数量有限。支持像 time 这样的通用子域并提供一种生成核心域的随机实体和值对象的方法将是有益的。

## 摘要

我们已经看到，基于属性的测试是基于可证伪性理论的。我们了解到，Vavr 有一个流畅的 API，它将这一想法整理成文。它使我们能够将性质定义为不变量和输入值生成器的组合。默认情况下，会针对 1000 个生成的样本检查一个不变量。

这使我们能够在更具声明性的层面上思考 FizzBuzz。我们定义并检查了三个属性，并讨论了结果。

最后，我们讨论了 PBT 的优点和缺点。

## 分享这篇文章*