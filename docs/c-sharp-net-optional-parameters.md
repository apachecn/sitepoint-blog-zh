# 如何在 C# 4.0 中使用可选参数

> 原文：<https://www.sitepoint.com/c-sharp-net-optional-parameters/>

可选参数是在没有特定参数的情况下调用函数或方法时使用的默认值。它们可以在 PHP 中使用…

```
 // PHP optional parameters
function DoSomething($num = 1, $str = "optional")
{
	// code
} 
```

以及 VisualBasic。网络:

```
 ' VB.NET optional parameters
Public Sub DoSomething(
	Optional ByVal num As Integer = 1, 
	Optional ByVal str As String = "optional"
	) 

	' code

End Sub 
```

类似的功能可以用 JavaScript 编写:

```
 // JavaScript optional parameters
function DoSomething(num, str)
{
	num = (typeof(num) != "undefined" ? num : 1);
	str = (typeof(str) != "undefined" ? str : "optional");

	// code
} 
```

在所有情况下，不带参数调用 DoSomething 会强制 num 为 1，str 为“可选”。用单参数调用 DoSomething 会设置 num，但是强制 str 为“可选”。用两个参数调用 DoSomething 会覆盖您的默认值。

尽管可选参数很有用，但直到 4.0 版才在 C#中实现。如果您最近升级了，现在可以编写如下代码:

```
 // C# optional parameters
void DoSomething(int num = 1, string str = "optional")
{
	// code
} 
```

那些使用早期版本 C#的人仍然可以实现可选的类似参数的代码，但这是通过重载实现的，例如

```
 // no parameters
void DoSomething()
{
	int num = 1;
	string str = "optional";

	// code
}
// 
// one parameter
void DoSomething(int num)
{
	string str = "optional";

	// code
}
// 
// two parameters
void DoSomething(int num, string str)
{
	// code
} 
```

重载可以提供更多一点的控制，但是对于一个过度工作的开发人员来说，要写更多的代码！

## 分享这篇文章