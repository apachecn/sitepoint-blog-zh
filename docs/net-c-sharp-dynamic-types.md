# 如何在 C# 4.0 中使用动态类型

> 原文：<https://www.sitepoint.com/net-c-sharp-dynamic-types/>

代码纯粹主义者可能会感到震惊，但是 C# 4.0 中已经出现了动态类型。在 PHP 和 JavaScript 等语言中，变量是松散类型的；不需要声明变量将保存什么类型的数据，所以对可以使用的操作符或方法没有限制，例如:

```
 // JavaScript code
var myvar = "Hello";
myvar += 1;
alert(myvar); // outputs "Hello1" 
```

C#和 VisualBasic.NET 是强类型；然而，它们都提供了通用的`object`类型，例如:

```
 // C# code
object myvar = "Hello";
myvar += 1;
Console.WriteLine(myvar); 
```

此代码将引发编译时错误；虽然`myvar`可以设置为任何值，但是编译器知道在你试图添加`1`的地方`myvar`是一个字符串。

C# 4.0 中引入的动态类型略有不同:

```
 // C# code
dynamic myvar = "Hello";
myvar += 1;
Console.WriteLine(myvar); 
```

编译器不会检查动态变量的当前状态，因此不会发生编译时错误。但是，运行时会引发异常，因为 1 不能添加到字符串中。

动态变量在运行时有效地继承了适合其类型的属性和方法。下面的代码将会运行，因为在使用`Length`属性时`myvar`是一个字符串，例如:

```
 // C# code
dynamic myvar = "Hello";
Console.WriteLine(myvar.Length); 
```

尽管动态变量提供了灵活性，但也存在许多风险:

1.  **无智能**:编译器不知道动态变量最终会变成什么类型，所以它无法在 Visual Studio 中提供方法或属性代码提示。
2.  **没有编译时错误**:可以使用不存在的属性或方法，比如`myvar.DoesNotExist()`。您的代码仍然可以编译和运行，但是在尝试调用时会引发异常。

我建议你只有在没有其他选择的时候才使用动态类型；比如用松散类型的 API(微软 Office 就是一个很好的例子)调用 COM 对象的时候，或者调用 Python、Ruby 等动态语言的时候。

## 分享这篇文章