# C 语言中的数据输入和输出，第 2 部分

> 原文：<https://www.sitepoint.com/data-input-and-output-in-c-part-2/>

上一篇关于输入和输出函数的文章讨论了函数`getchar()`、`putchar()`和`scanf()`。本文将讨论用于在标准输出设备上显示数据的功能`printf()`和`puts()`。它还将讨论一些演示输入/输出函数使用的基本程序。

## 写入输出数据:printf 函数

`printf`函数用于输出数值、单个字符和字符串的任意组合。`printf`函数的一般形式是:

**printf(控制字符串，arg1，arg2，…，argN)**

**控制串**由以下一项或多项组成:

*   按原样简单印刷的字符；即消息或提示
*   转换规范—字段宽度和格式说明符
*   转义序列—用于打印某些非打印字符，如换行符、制表符和铃声警报

控制字符串指示后面的参数类型。参数 **arg1、arg2、…、argN** 是根据控制字符串的规范格式化和打印其值的变量。参数的数量、顺序和类型必须符合格式规范。

示例:

```
#include<stdio.h>

int main()

{

int marks=412;

float percent=85.4;

printf("n Marks= %d,n Percentage=%f ",marks, percent);

return 0;

}

Output:

Marks= 412

Percentage= 85.4
```

在上面的程序中，转义序列`n`被用来在多行上打印输出。格式说明符 `%d`和 `%f` 用于表示参数`marks`和`percent` 分别是整型参数和浮点型参数。`printf`函数的参数代表**实际值**，而不是像`scanf`中的情况那样代表地址。

### 在 printf 中指定字段宽度

最小字段宽度可以通过在转换字符前加一个无符号整数来指定。如果相应数据项中的字符数小于指定的字段宽度，数据项前面将有足够的前导空格来填充指定的字段。如果字符数超过字段宽度，将为数据项分配额外的空间，以便可以显示整个数据项。这与指定最大字段宽度的`scanf`功能正好相反。

以下程序说明了最小字段宽度功能的使用。

```
#include<stdio.h>

int main()

{

int i = 1234;

float x= 348.45;

char a[];

printf( " %3d %5d %8d" , i, i, i);

printf("%7f  %7.1f  %7.3f ", x,x,x);

printf ("%10s %13s %10.5s",a,a,a);

return 0;

}
```

第一个`printf`语句显示一个具有不同最小字段宽度规格的十进制整数。整个整数值将显示在每个字段中，即使指定的字段宽度较小(与第一个字段一样)。

第一个`printf`语句的输出是:

`1234    _1234  _ _ _ _1234`

下划线标记实际上不会被打印出来。它们用于显示符合最小字段宽度规范的前导空格数。

第二个`printf`语句显示一个具有不同字段宽度规格的浮点数。可打印的最大小数位数定义了字段的**精度**。**精度**是一个无符号整数，在遵循最小字段宽度规范的小数点后指定。例如，在第二个`printf`语句中，第二个字段的最小字段宽度为 7 位，可打印的小数位数为 1。

第二个`printf`语句的输出是:

`348.450000    _ _348.5    348.450`

请注意，第二个字段中的值已被四舍五入，以符合精度规范。

精度规格也可以应用于字符串。精度将决定可以显示的最大字符数。如果精度规格小于字符串中的字符总数，则不会显示超出的最右侧字符。即使最小字段宽度大于整个字符串，也会发生这种情况，从而导致向截断的字符串添加前导空格。

假设字符数组`a`存储字符串“precision”。因此，第三个`printf`语句的输出是:

`_precision  _ _ _ _precision _ _ _ _ _preci`

### 在 printf 中指定标志:

除了字段宽度、精度和转换字符之外，控制字符串中的每个字符组可以包括一个影响输出外观的**标志**。

一些最常用的标志是:

*   `-`数据项在字段内左对齐(填充最小字段宽度所需的空格将在数据项的后添加**。默认情况下，数字右对齐打印。**
*   `+`每个带符号的数字数据项前会有一个符号(+或-)。如果没有此标志，只有负数据项前面会有一个符号。
*   `0`使前导零而不是前导空格出现。仅适用于最小字段宽度大于数据项的字段内右对齐的数据项。
*   每个正数前都有一个空格。如果两者都存在，此标志将被+标志覆盖。
*   `#`带 o 和 x 类型转换)使八进制和十六进制数据项分别以 0 和 0x 开头。
*   `#`(带 e、f、g 类型转换)使小数点出现在所有浮点数中，即使它是一个整数。还防止在 g 类型转换中截断尾随零。

以下示例说明了浮点和整数数量标志的使用。

```
#include<stdio.h>

int main()

{

int i= 123;

float x=12.0, y= -3.3;

// Default right justified printing of numbers without flags

printf (": %6d %7.0f  %10.1e:");

//Left justified display through - flag

printf("n :%-6d %-7.0f %-10.1e:");

//Addition of sign with default right justified display

printf("n :%+6d %+7.0f %+10.1e:");

//addition of sign(through + flag)  and left justification(through - flag)

printf(n :%-+6d %-+7.0f %-+10.1e:");

//prints floating point number without decimal and truncates 0s in g type conversion

printf(n :%7.0f %7g:");

// Including a decimal point in the floating point number and including trailing zeros in g type conversion:

printf("n : %#7.0f   %#7g:");

return 0;

}
```

输出是不言自明的。冒号分别表示每行第一个字段和最后一个字段的开始和结束。

:123 12 -3.3e+00:

:123 12 -3.3e+00:

:+123 +12 -3.3e+00:

:+123 +12 -3.3e+00:

:               12            -3.3:

:                12.         -3.30000:

–和+标志也可用于对齐字符串。

## 字符串输出:puts 函数

`puts`函数将其字符串参数写入屏幕，后跟一个换行符。

`puts`的一般形式是:

**puts(arrayname)**

其中`arrayname`是一个可以包含空白字符的字符类型数组。如果发生写错误，`puts`函数返回 EOF 否则，它返回一个非负值。

`puts`函数提供了一种打印字符串的便捷方式。尽管当% s 格式说明符与`printf`一起使用时，它允许打印字符串，`puts`提供了一种方便而简洁的方式来执行同样的工作。

类似于`puts`函数，以前也有一个`gets`函数。它用于读取包含空白字符的字符类型数组。然而，在 **C11 标准**生效后，`gets`功能不再使用。

除了这些基本的输入/输出功能，还有许多其他的输入/输出功能用于读写数据文件。错误检查机制也包含在文件输入/输出功能中。这些函数涉及到文件指针的使用，我们将在后面讨论如何在 c 中创建和处理文件时讨论。

以下程序说明了我们到目前为止讨论过的基本输入/输出函数的用法:

### 示例 1

设`a`和`b`为两个整数，其中 `a=10`和`b=20`。下面的程序交换这两个变量的值。

```
#include<stdio.h>
int main()
{
  int a=10;
  int b=20;
  int c;
  c=a;
  a=b;
  b=c;
  printf("After swapping values are:n");
  printf("A=%d",a);
  printf("nB=%d",b);
  return 0;
}
```

```
Output:

After swapping values are:
A=20
B=10
```

上面的程序使用一个临时变量来交换这两个值。下面的程序在不使用第三个变量的情况下完成了同样的工作。

### 示例 2

**不使用第三个变量交换值:**

```
#include<stdio.h>
int main()
{
  int a=10;
  int b=20;

  a=a+b; //a=30
  b=a-b;//b=30-10=20
  a=a-b; //a=30-20=10
  printf("A=%d",a);
  printf("nB=%d",b);
  return 0;
}

Output: 
A=20
B=10
```

上述程序也可以通过使用乘法(*)和除法(/)运算符来实现。

### 示例 3

打印字符的 ASCII 码，反之亦然:

```
#include<stdio.h>
int main()
{
  char letter='a';
  int number=97;
  printf("%d",letter);
  printf("n%c",number);
  return 0;
}
```

```
Output:
97
a
```

#### 说明

我们已经看到每个字符都有一个等价的 ASCII 码，并且字符和整数类型是兼容的。因此，在上面的程序中,%d 格式说明符打印出字母 a 的等价 ASCII 代码。

类似地,%c 格式说明符打印等效 ASCII 码为 97 的字符。这一知识可用于将字母从大写转换成小写。以下示例演示了同样的情况。

### **例 4**

将小写字母转换为大写字母:

```
#include<stdio.h>
int main()
{
  char c='a';
  int d;
  d=c-32; //d=97-32=65 (ASCII of 'A')
  printf("%c",d); //prints character equivalent of the integer value stored in d
  return 0;
}
Output:
A
```

可以修改上面的程序来改变整行文本的大小写。这可以通过使用循环来完成。

本系列的下一篇文章将从讨论控制结构开始，控制结构包括决策语句、选择语句和迭代语句(循环)。** 

## **分享这篇文章**