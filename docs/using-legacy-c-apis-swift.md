# 在 Swift 中使用传统 C APIs

> 原文：<https://www.sitepoint.com/using-legacy-c-apis-swift/>

Swift 的类型系统旨在通过对我们的代码中能做什么和不能做什么实施严格的规则，使我们的生活更加轻松。这无疑是一件好事，它鼓励程序员写出更好、更正确的代码。然而，当与遗留代码库，尤其是基于 C 的库进行交互时，这似乎是非常禁止的。事实上，许多 C 库滥用类型的方式与 Swift 编译器不兼容。诚然，苹果的 Swift 团队已经竭尽全力支持 C 的一些更基本的功能，如 C 字符串，但在 Swift 中使用遗留 C 库时仍有许多问题。以下是处理它们的方法。

在我们开始之前，我想指出，本文中的许多操作本质上是不安全的，因为它们完全绕过了 Swift 编译器的类型系统。我鼓励您仔细阅读，不要复制和粘贴本文中代码。这不是堆栈溢出，如果使用不当，这些代码片段确实有可能损坏内存，导致内存泄漏，或者干脆使您的应用程序崩溃。

## 基础知识

大多数情况下，C 指针以两种方式之一导入 Swift:

```
UnsafePointer<T>
```

或者

```
UnsafeMutablePointer<T>
```

其中 *T* 是原 C 型的等价 Swift 型。在 C 中声明为*常量*的指针作为`UnsafePointer`导入，未声明为*常量*的指针作为`UnsafeMutablePointer`导入。

这里有几个例子

```
C:
void myFunction(const int *myConstIntPointer);

Swift:
func myFunction(myConstIntPointer: UnsafePointer<Int32>)

C:
void myOtherFunction(unsigned int *myUnsignedIntPointer);

Swift:
func myOtherFunction(myUnsignedIntPointer: UnsafeMutablePointer<UInt32>)

C:
void iTakeAVoidPointer(void *aVoidPointer);

Swift:
func iTakeAVoidPointer(aVoidPointer: UnsafeMutablePointer<Void>)
```

如果 Swift 不知道指针的类型，例如因为它是一个前向声明，就用一个`COpaquePointer`来表示。

```
C:
struct SomeThing;
void iTakeAnOpaquePointer(struct SomeThing *someThing);

Swift:
func iTakeAnOpaquePointer(someThing: COpaquePointer)
```

## 传递指向 Swift 对象的指针

在许多情况下，这就像使用`inout`操作符一样简单，它与 C 中熟悉的`address-of`操作符,“与”符号相同。

```
Swift:
let myInt: = 42
myFunction(&myInt)

var myUnsignedInt: UInt = 7
myOtherFunction(&myUnsignedInt)
```

这里有两个非常重要但微妙的细节。

1.  使用`inout`运算符时，用`var`声明的变量和用`let`声明的常量分别被转换成`UnsafePoiner`和`UnsafeMutablePointer`。如果你没有密切注意代码中的原始类型，这是很容易被忽略的。试图在需要一个`UnsafeMutablePointer`的地方传递一个`UnsafePointer`会导致一个看似神秘的编译器错误，所以要小心。
2.  This operator only works in the context of passing Swift values and references as function arguments that expect an `UnsafePointer` or `UnsafeMutablePointer`. You cannot get the pointer in any other context. For example, this is invalid and will result in a compiler error:

    ```
    Swift:
    let x = 42
    let y = &x
    ```

    有时，您需要与一个 API 交互操作，该 API 接受或返回一个空指针来代替显式类型。不幸的是，这在 C 中很常见，因为没有办法指定泛型类型。

    ```
    C:
    void takesAnObject(void *theObject);
    ```

    如果您知道函数所采用的预期类型，您可以使用`withUnsafePointer`和`unsafeBitCast`将一个对象强制转换成一个空指针。例如，假设`takesAnObject`实际上期望一个指向`int`的指针。

    ```
    var test = 42
    withUnsafePointer(&test, { (ptr: UnsafePointer<Int>) -> Void in
    var voidPtr: UnsafePointer<Void> = unsafeBitCast(ptr, UnsafePointer<Void>.self)
    takesAnObject(voidPtr)
    })
    ```

    我们来分析一下。首先，我们调用`withUnsafeMutablePointer`。这个通用函数有两个参数。

    第一个是类型为`T`的`inout`，第二个是类型为`(UnsafePointer) -> ResultType`的闭包。这个函数调用闭包的方法是，将一个指针指向函数的第一个参数，并将其作为闭包的唯一参数传递。然后，该函数返回闭包的结果。在上面的例子中，闭包被类型化为返回`Void`，因此不返回任何东西。我们可以很容易地做这样的事情:

    ```
    let ret = withUnsafePointer(&test, { (ptr: UnsafePointer<Int>) -> Int32 in
    var voidPtr: UnsafePointer<Void> = unsafeBitCast(ptr, UnsafePointer<Void>.self)
    return takesAnObjectAndReturnsAnInt(voidPtr)
    })

    println(ret)
    ```

    **注意**:如果你需要修改指针本身，有一个`withUnsafeMutablePointer`变量。

    为了方便起见，Swift 还有传递两个指针的变体:

    ```
    var x: Int = 7
    var y: Double = 4

    withUnsafePointers(&x, &y, { (ptr1: UnsafePointer<Int>, ptr2: UnsafePointer<Double>) -> Void in

    var voidPtr1: UnsafePointer<Void> = unsafeBitCast(ptr1, UnsafePointer<Void>.self)
    var voidPtr2: UnsafePointer<Void> = unsafeBitCast(ptr2, UnsafePointer<Void>.self)

    takesTwoPointers(voidPtr1, voidPtr2)
    })
    ```

    ### 关于 unsafeBitCast

    是一项极其危险的手术。文档将它描述为“对任何相同大小的东西进行残酷的比特转换”我们能够在上面安全地使用它的原因是因为我们只是在不同类型的指针之间进行强制转换，并且所有的指针在任何给定的平台上都是相同大小的。这就是为什么我们必须先调用`withUnsafePointer`来获得一个类型化的`UnsafePointer`*，然后再将*转换为 C API 定义的`UnsafePointer`。**

     *这一开始可能会令人困惑，特别是当使用与指针大小相同的类型时，比如 Swift 中的`Int`(无论如何，在所有当前可用的平台上，指针的大小是 1 个`Word`，1 个字是一个`Int`)。

    很容易犯这样的错误:

    ```
    var x: Int = 7
    let xPtr = unsafeBitCast(x, UnsafePointer<Void>.self)
    ```

    这是令人难以置信的误导，因为它*将编译并运行*，但会导致意外的错误，因为 C APIs 将接收位置为 *0x7* 的指针，而不是指向 x 的指针，或者垃圾。

    因为`unsafeBitCast`要求类型的大小相等，所以当试图转换除了`Int`之外的其他类型时，比如`Int8`或者一个单字节整数，这种方法就不那么阴险了。

    ```
    var x: Int8 = 7
    let xPtr = unsafeBitCast(x, UnsafePointer<Void>.self)
    ```

    这只会导致`unsafeBitCast`抛出一个异常并使你的程序崩溃！

    ## 与 C 结构交互

    让我们用一个具体的例子来解决这个问题。您想要检索有关您的计算机正在运行的系统的信息。有一个 C API，`uname(2)`，它获取一个指向结构的指针，并用系统信息填充所提供的对象中的信息，比如 OS 名称和版本或其硬件标识符。但是有一个问题，该结构被导入到 Swift 中:

    ```
    struct utsname {
    var sysname: (Int8, Int8, ...253 times..., Int8)
    var nodename: (Int8, Int8, ...253 times..., Int8)

    var release: (Int8, Int8, ...253 times..., Int8)
    var version: (Int8, Int8, ...253 times..., Int8)
    var machine: (Int8, Int8, ...253 times..., Int8)
    }
    ```

    哦不！Swift 将 C 数组文字作为元组导入！最重要的是，默认的初始化器要求每个字段都有值。所以如果你用正常的快捷方式，它会是这样的:

    ```
    var name = utsname(sysname: (0, 0, 0, ..., 0), nodename: (0, 0, 0, ..., 0), etc)
    utsname(&name)

    var machine = name.machine
    println(machine)
    ```

    这不是个好主意！但是还有一个问题。`utsname`的`machine`字段是一个元组，因此`println`将打印出 256 个`Int8`，其中只有前几个表示我们实际需要的字符串中字符的 ASCII 值。

    那么，我们该如何解决这个问题呢？

    Swift 的`UnsafeMutablePointer`提供了`alloc(Int)`和`dealloc(Int)`两种方法，分别用于手动分配和解除分配。参数分配或取消分配的`T`的数量。我们可以使用这些 API 来简化我们的代码。

    ```
    let name = UnsafeMutablePointer<utsname>.alloc(1)
    uname(name)

    let machine = withUnsafePointer(&name.memory.machine, { (ptr) -> String? in

    let int8Ptr = unsafeBitCast(ptr, UnsafePointer<Int8>.self)
    return String.fromCString(int8Ptr)
    })

    name.dealloc(1)

    if let m = machine {
    println(m)
    }
    ```

    第一步是调用`withUnsafePointer`，给它传递机器元组，告诉它我们的闭包将返回一个可选字符串。

    在闭包内部，我们获取提供的指针，并将其转换为`UnsafePointer`，这是相同值的一个基本等价的表示。除了 Swift 的`String`有一个从`UnsafePointer`初始化的类方法，其中`CChar`是`Int8`的*类型别名*！所以我们可以把新的指针传递给初始化器并返回值。

    在捕获了`withUnsafePointer`的结果(记住，它转发所提供的闭包的返回值)之后，我们可以测试是否使用了 conditional-let 语句获得了一个值，并打印结果。对我来说，这产生了预期的“x86_64”。* 

 *## 结论

有点免责声明。在 Swift 中使用不安全的 API 应该是最后的手段，因为它们本身就不安全！随着我们从传统的 C 和 Objective-C 代码过渡到 Swift，我们很可能会继续需要这些 API 来与我们现有的工具兼容。然而，当他们第一次使用 UnsafePointer 和 unsafeBitCast 时，应该始终保持怀疑。

新代码应尽可能地符合习惯，明确禁止使用 Swift 的不安全 API。作为软件开发人员，知道如何使用工具和知道何时何地不使用工具一样重要。Swift 带来了使大量软件开发现代化的好处，我们必须尊重它的意识形态，以使它真正产生影响。

## 分享这篇文章*