# C 语言中的控制语句

> 原文：<https://www.sitepoint.com/control-statements-in-c-part-2/>

控制语句使我们能够指定程序控制的流程——即程序中指令必须执行的顺序。它们使决策、重复执行任务或从一段代码跳到另一段代码成为可能。

## 控制语句的类型

C 中有四种类型的控制语句:

1.  决策陈述(if，if-else)
2.  选择语句(开关盒)
3.  迭代语句(for，while，do-while)
4.  跳转语句(break、continue、goto)

## 决策语句:if-else 语句

if-else 语句用于执行逻辑测试，然后根据测试结果(即结果是真还是假)采取两种可能的操作之一。

**语法:**

```
if (condition)
{
  // statements
}
  else
{
  // statements
}
```

如果 If 语句中指定的条件计算结果为 true，则执行 if 块中的语句，然后将控制权转移到 if 块后面的语句。即使条件为假并且不存在 else 块，控制也会立即转移到 if 块之后的语句。

只有当条件评估为假时需要执行某个指令序列时，else 部分才是必需的。需要注意的是，条件总是在圆括号中指定，并且将 if 块或 else 块中的语句用大括号括起来是一个好习惯，无论它是单个语句还是复合语句。

下面的程序检查输入的数字是正数还是负数。

```
#include<stdio.h>
int main( ) {
  int a;
  printf("n Enter a number:");
   scanf("%d", &a);

   if(a>0)
    { 
     printf( "n The number %d is positive.",a);
    }
    else
    {
     printf("n The number %d is negative.",a);
    }

  return 0;
   }
```

下面的程序比较两个字符串，检查它们是否相等。

```
 #include <string.h>

int main( ) {
    char a[20] ,  b[20];

    printf("n Enter the first string:");
    scanf("%s",a);
    printf("n Enter the second string:");
    scanf("%s",b);

    if((strcmp(a,b)==0))
      {
       printf("nStrings are the same");
       }
    else
      {
      printf("nStrings are different");
      }

  return 0;
}
```

上面的程序比较两个字符串，检查它们是否相同。`strcmp`功能用于此目的。它在`string.h` 文件中声明为:

`int strcmp(const char *s1, const char *s2);`

它将由`s1`指向的字符串与由`s2`指向的字符串进行比较。`strcmp`函数返回一个大于、等于或小于零的整数，相应地，当`s1`指向的字符串大于、等于或小于`s2`指向的字符串时。

因此在上面的程序中，如果两个字符串`a` 和`b`相等，`strcmp`函数应该返回 0。如果它返回 0，字符串是相同的；除此之外，它们是不同的。

### 嵌套的 if 和 if-else 语句

也可以将**嵌入**或**嵌套** if-else 语句。当需要从几个不同的操作中选择一个时，嵌套非常有用。

嵌套 if-else 语句的一般格式是:

```
if(condition1)
{
// statement(s);
}
else if(condition2)
{
//statement(s);
}
.
.
else if (conditionN)
{
//statement(s);
}
else
{
//statement(s);
}
```

以上也称为 **if-else 阶梯**。在嵌套 if-else 语句的执行过程中，一旦遇到评估为 true 的条件，将执行与该特定 if 块相关联的语句，并绕过其余的嵌套 if-else 语句。如果两个条件都不成立，要么执行最后一个 else 块，要么如果 else 块不存在，则控制转移到紧跟在 else-if 阶梯之后的下一条指令。

下面的程序利用嵌套的 if-else 语句来查找三个数中的最大值。

```
#include<stdio.h>

int main( ) {
    int a, b,c;
    a=6,b= 5, c=10;
    if(a>b)
      {
        if(b>c)
          {
            printf("nGreatest is: " , a);
          }
        else if(c>a)
           {
             printf("nGreatest is: ", c);
           }
      }
    else if(b>c)     //outermost if-else block
       {
         printf("nGreatest is: " , b);
       }
      else
      {
        printf( "nGreatest is: " , c);
      }
    return 0;
}
```

上面的程序比较了三个整数，并打印出最大的。第一个 if 语句比较了`a`和`b`的值。如果`a>b`为真，程序控制被转移到嵌套在 If 块中的 if-else 语句，其中`b`与`c`进行比较。如果`b>c`也为真，则打印`a`的值；否则比较`c`和 `a`的值，如果`c>a`为真，则打印`c`的值。在这之后，if-else 阶梯的其余部分被绕过。

然而，如果第一个条件`a>b`为假，则控制直接转移到最外面的 else-if 块，其中`b`的值与`c`进行比较(因为`a`不是最大的)。如果`b>c`为真，则打印`b`的值，否则打印`c`的值。注意嵌套、大括号的使用和缩进。所有这些都是保持清晰所必需的。

## 选择语句:switch-case 语句

switch 语句用于**多路选择**，它将根据变量或表达式的值分支成不同的代码段。此表达式或变量必须是整数数据类型。

**语法:**

```
switch (expression)
{
  case value1:
    code segment1;
    break;
  case value2:
    code segment2;
    break;
.
.
.
  case valueN:
    code segmentN;
    break;
  default:
    default code segment;
}
```

这个**表达式**的值要么在程序执行期间生成，要么作为用户输入读入。选择并执行与**表达式**的值相同的案例。可选的**默认**标签用于指定当表达式的值与任何案例值都不匹配时要执行的代码段。

`break`语句出现在每个案例的结尾。如果不是这样，执行将继续到下一个 case 的代码段，甚至不检查 case 值。例如，假设一条`switch`语句有五种情况，第三种情况的值与**表达式**的值匹配。如果在第三个案例的末尾没有 break 语句，那么案例 3 之后的所有案例也将与案例 3 一起执行。如果存在中断，则仅选择并执行所需的案例；之后，控制权会立即转移到 switch 语句之后的下一条语句。在`default`之后没有`break`,因为在默认情况下，控制将在 switch 之后立即转移到下一条语句。

示例:打印星期几的程序。

```
#include<stdio.h>

int main( ) {
  int day;
  printf("nEnter the number of the day:");
  scanf("%d",&day);

  switch(day)
    {
      case 1:
        printf("Sunday");
        break;
      case 2:
        printf("Monday");
        break;
      case 3:
        printf("Tuesday");
        break;
      case 4:
        printf("Wednesday");
        break;
      case 5:
        printf("Thursday");
        break;
      case 6:
        printf("Friday");
        break;
      case 7:
        printf("Saturday");
        break;
      default:
        printf("Invalid choice");
      }
   return 0;
}
```

这是一个非常基本的程序，它解释了 switch-case 结构的工作原理。根据用户输入的数字，选择并执行适当的案例。例如，如果用户输入是 5，那么将执行情况 5。案例 5 中出现的`break`语句将在案例 5 之后暂停 switch 语句的执行，控制将转移到 switch 之后的下一条语句，即:

`return 0;`

也可以在 switch 语句的 case 中嵌入复合语句。这些复合语句可能包含控制结构。因此，也可以通过将开关嵌入外壳中来实现嵌套开关。

所有使用 switch-case 语句编写的程序也可以使用 if-else 语句编写。然而，当您需要在评估一些简单或复杂的条件(可能涉及关系和逻辑表达式的组合)后采取一些行动时，使用 if 语句是一种很好的编程方式。

如果需要在一大组值中进行选择，switch 语句将比一组嵌套的 If 运行得快得多。开关与 if 的不同之处在于开关只能测试相等性，而 if 可以计算任何类型的布尔表达式。

当需要从一组给定的选项中进行选择时，必须使用 switch 语句。switch case 语句通常用于基于**菜单的应用**。switch-case 语句最常见的用途是在数据处理或文件处理中。大多数文件处理都包括一些常见的功能:创建文件、添加记录、删除记录、更新记录、打印整个文件或一些选择性记录。下面的程序说明了如何在数据处理中使用 switch case 语句。它不涉及文件处理的代码，因为我们只有在学习了指针、结构和联合等高级概念之后才能讨论 C 语言中的文件处理。

示例:数据文件处理中使用的 switch case 语句。

```
#include<stdio.h>

int main() { //create file &set file pointer .
  int choice;
  printf("n Please select from the following options:");
  printf("n 1\. Add a record at the end of the file.");
  printf("n 2\. Add a record at the beginning of the file:");
  printf("n 3\. Add a record after a particular record:";
  printf("nPlease enter your choice:(1/2/3)?");
  scanf("%d",&choice);

   switch(choice)
     {
       case 1:
         //code to add record at the end of the file
         break;
       case 2:
         //code to add record at the beginning of the file
         break;
       case 3:
         //code to add record after a particular record
         break;
       default:
         printf("n Wrong Choice");
      }
  return 0;
}
```

上面的 switch-case 示例通常涉及将 switch-case 构造嵌套在类似 do-while 的迭代构造中。

## 迭代语句

迭代语句用于重复执行一组特定的指令，直到满足特定的条件或迭代固定次数。

### 1.for 语句

for 语句或 for 循环重复执行构成 for 循环体的指令集，直到满足特定条件。

**语法:**

```
for(initialization; termination; increment/decrement) { //statements to be executed }
```

for 循环由三个表达式组成:

*   **初始化表达式**，初始化**循环索引**。循环索引控制循环操作。当循环开始时，初始化表达式只执行一次。
*   **终止表达式**，表示循环继续执行必须满足的条件。
*   在每次迭代后执行**递增/递减表达式**，以更新循环索引的值。

下面的程序使用 for 循环打印序列:0，1，1，2，3，5，8，13 …到 n 项。

```
#include<stdio.h>
int main() {
  int i,n, a, b, sum=0;
  printf("Enter the number of terms:");
  scanf("%d",&n);
  a=0; b=1;
  printf("%dn %d",a ,b);

  for(i=2; i< n; i++)   { 
    sum=a+b;  
    printf("n%d",sum);
    a=b;
    b=sum;
  }

  return 0; 
} 
```

如果将前两个元素`0`和`1`排除在外，可以看到序列中接下来的每个元素都是前两个元素的总和。例如，第三个元素是前两个元素的和(0+1)，第四个元素是第二个和第三个元素的和(1+1=2)，第五个元素是第三和第四个元素的和(1+2=3)等等。因此，每次打印的都是前两个元素的总和。先前的`b`值成为新的`a`值，先前的`sum` 值成为新的 `b`值。新计算的总和成为下一个元素，随后被打印出来。重复执行这些步骤，直到循环索引`i`不小于用户输入的项数。循环索引`i`从 2 开始，因为前两个术语已经打印。它也可以从 0 到 1 小于 n-2，如:

```
for(i=0;i<(n-2);i++)
```

或者从 1 到 n-2 为:

```
for(i=1; i<(n-2);i++)
```

for 语句非常通用，可以用四种不同的方式编写:

#### 1.省略初始化表达式

在这种情况下，循环索引在 for 循环的之前被初始化**。因此，for 循环采用以下形式:**

```
i=0;/*initialization*/ 
for(; condition;increment/decrement expression)  {  
  //statements to be executed 
}
```

请注意，终止初始化表达式的分号出现在条件表达式之前。

#### 2.省略条件

在这种情况下，条件是在 for 循环体的内指定的**，通常使用 if 语句。while 或 do-while 语句也可用于指定条件。因此，for 循环采用以下形式:**

```
 for(initialization; ; increment/decrement expression)  {
   condition //statements to be executed
}
```

同样，可以看到终止条件的分号出现在 for 语句中。以下程序解释了如何省略该条件:

```
#include<stdio.h> 
int main()  {
  int i,n, a, b, sum=0;
  printf("Enter the number of terms:");
  scanf("%d",&n); 
  a=0;
  b=1;
  printf("%dn %d", a, b);

  for(i=2; ;i++)  {
    if(i==(n-1)) { //condition specified using if statement  
      break;
    }

    sum=a+b;
    printf("n%d",sum);
    a=b;
    b=sum;
  }

  return 0;
}
```

#### 3.省略递增/递减表达式:

在这种情况下，递增/递减表达式被写入 for 循环的主体内**。**

示例:

```
#include<stdio.h>
int main()  {
  int i,n, a, b, sum=0;
  printf("Enter the number of terms:");
  scanf("%d",&n);
  a=0;
  b=1;
  printf("%dn %d",a,b);

  for(i=2;i<n;)  {
    sum=a+b;
    printf("n%d",sum);
    a=b;  
    b=sum;
    i++;  //increment expression 
   }

   return 0;
}
```

#### 4.省略所有三个表达式:

也可以省略所有三个表达式，但是它们应该以上面讨论的方式出现。如果这三个表达式都被完全省略——也就是说，它们没有以上面讨论的方式被提及——那么 for 循环就变成了一个**无限或永无止境的循环**。在这种情况下，for 循环采用以下形式:

```
for(;;) {
  //statements 
}
```

### 2.while 语句

当特定条件为真时，while 语句重复执行语句块。

```
while (condition)  {
  //statement(s) to be executed 
}
```

重复执行这些语句，直到条件为真。

**例子:**程序计算一个数的位数之和(如 456；4+5+6 = 15)

```
#include<stdio.h>
int main() {
  int n, a,sum=0;
  printf("n Enter a number:");
  scanf("%d", &n);

  while(n>0) {
    a=n%10; //extract the digits of the number  
    sum=sum+a; //sum the digits  
    n=n/10; //calculate the quotient of a number when divided by 10\. 
   }   

  printf("n Sum of the digits=t %d",sum);
  return 0; 
}
```

上面的程序使用 while 循环来计算一个数字的位数之和。例如，如果数字是 456，while 循环将按如下方式通过四次迭代计算总和。记住%给出余数和/商是有帮助的。

**迭代 1:** n > 0 条件为真(n = 456)
a = n % 10 = 6；
sum = sum+a = 6；
n = n/10 = 45；n 的新值是 45。

**迭代 2:** n > 0 条件为真(n = 45)
a = n % 10 = 5；
sum = sum+a = 6+5 = 11；
n = n/10 = 4；n 的新值是 4。

**迭代 3:** n > 0 条件为真(n = 4)
a = n % 10 = 4；
sum = sum+a = 11+4 = 15；
n = n/10 = 0；n 的 ew 值为 0。

**迭代 4:** n > 0 条件为假(n=0)。
在第四次迭代之后，控制退出 while 循环，并将总和打印为 15。

**例 2:** 程序检查输入的数字是否为回文。

回文是一个数字，当它的数字从右向左读或写时保持不变，反之亦然，例如 343 是回文，但 342 不是。下面的程序工作的逻辑是，如果数字的倒数与原始数字相同，那么输入的数字是一个回文，否则不是。

```
#include<stdio.h>  
int main() {
  int a, n, m, reverse=0;
  printf("n Enter a number:");
  scanf("%d", &n);  
  m=n;

  while(n>0) {
    a=n%10; reverse=reverse*10 +a;
    n=n/10;
   }  
  if (m== reverse) 
     {  
    printf(" n The number is a palindrome.");
     } 
    else  
     { 
    printf("n The number is not a palindrome.");
  } 

  return 0;
}
```

上面的程序使用的逻辑与关于数字和的程序几乎相同。如该程序所示，在最后一次迭代中，`n`变成了`0`。但是，我们需要比较`n`的原始值和数字的倒数来确定它是否是一个回文。因此，在进入 while 循环之前，`n`的值已经存储在`m`、**中。稍后将`m`的值与`reverse`进行比较，以确定输入的数字是否是回文。**

while 循环的工作方式如下:

设 n = 343

**迭代 1:**
a = n % 10 = 3；
反向=反向* 10+a = 0 * 10+3 = 3；
n = n/10 = 34；

**迭代 2:**
a = n % 10 = 4；
反向=反向* 10+a = 3 * 10+4 = 34；
n = n/10 = 3；

**迭代 3:**
a = n % 10 = 3；
反向=反向* 10+a = 34 * 10+3 = 343；
n = n/10 = 0；

**迭代 4:**
n > 0 条件假(n=0)。
控制从 while 循环中退出。

### 3.do-while 循环

在至少执行一次语句块后，do-while 语句在循环结束时计算条件。如果条件为真，循环继续，否则在第一次迭代后终止。
**语法:**

```
do { 
  //statements to be executed;  
} 
while(condition); 
```

注意结束 do-while 语句的分号。`while`和`do-while`的区别在于，while 循环是一个**入口控制的循环** —它在循环开始时测试条件，即使条件为假也不会执行一次，而 do-while 循环是一个**出口控制的循环** —它在完成第一次迭代后测试循环结束时的条件。

对于许多应用程序来说，在循环开始时测试循环的连续性比在循环结束时测试更为自然。因此，do-while 语句的使用频率低于 while 语句。

大多数使用 while 的程序也可以使用 do-while 来实现。除了使用 do-while 循环之外，下面的程序以相同的方式计算数字之和:

```
#include<stdio.h> 
int main() { 
  int n, a,sum=0;
  printf("n Enter a number:");
   scanf("%d", &n);  
   do {
     a=n%10;
     sum=sum+a;
     n=n/10;
    }
    while(n>0);

    printf("n Sum of the digits=t %d",sum);

    return 0;
  }
```

但是，do-while 语句应该只用于无论条件是否为真，循环都必须至少执行一次的情况。

do-while 循环的一个实际应用是在交互式菜单驱动程序中，其中菜单至少出现一次，然后根据用户的选择，再次显示菜单或终止会话。考虑我们在开关盒中看到的同一个例子。不使用像 do-while 这样的迭代语句，用户只能从菜单中选择一次选项。此外，如果错误地输入了一个错误的选项，用户就不能再次输入他的选项。这两个错误都可以通过使用 do-while 循环来纠正。

```
#include<stdio.h>

int main() {  //create file &set file pointer .  
  int choice;
  char ch;
  printf("n Main Menu ");
  printf("n 1\. Add a record at the end of the file."); 
  printf("n 2\. Add a record at the beginning of the file:"); 
  printf("n 3\. Add a record after a particular record:";  
  printf("nPlease enter your choice:(1/2/3)?");
  scanf("%d",&choice);

  do  {   
    switch(choice)  { 
      case 1: 
        //code to add record at the end of the file  break; 
      case 2:
        //code to add record at the beginning of the file break; 
      case 3: 
        //code to add record after a particular record  break;  
      default: printf("n Wrong Choice");  
    }  
    printf(" Do you want to continue updating records(y/n)?");
    scanf("%c", ch);
    }
    while (ch=='y'||ch=='Y');

    return 0;  
  }
```

## 分享这篇文章