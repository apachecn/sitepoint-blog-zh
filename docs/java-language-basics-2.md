# Java 语言基础文章

> 原文：<https://www.sitepoint.com/java-language-basics-2/>

##### 变量(续))

一旦创建并赋值，变量就可以在任何可能用到它所包含的值的地方使用。让我们看一个简单的程序，它打印出存储在变量中的室温。打开您的文本编辑器，键入以下内容(记住，不要键入行号)，然后将文件保存为`PrintRoomTemp.java`:

```
1  /**  

2   * PrintRoomTemp.java  

3   * A simple Java program that prints out the temperature  

4   */  

5    

6  class PrintRoomTemp {  

7    public static void main(String[] args) {  

8      int roomTemp = 20; // Room temperature  

9    

10     // Print out the temperature  

11     System.out.print("Current room temperature: ");  

12     System.out.print(roomTemp);  

13     System.out.print(" degrees Celsiusn");  

14   }  

15 }
```

这个程序很像我们上面看到的`Hello`程序，除了几个明显的例外:

*   **第 8 行:**这个程序做的第一件事是创建一个名为`roomTemp`的变量，并给它赋值 20。
*   **第 11-13 行:**在`Hello`中，我们使用`System.out.println(...)`在屏幕上一次打印一行信息。在这个节目中，我们用了`System.out.print(...)`来代替。这两个命令的唯一区别是`println`在其输出的末尾打印一个换行符，这样屏幕上要显示的下一个字符将出现在下一行的开头。`print`没有添加这个换行符，所以几个`print`命令可以串在一起在屏幕上打印一行。
*   **第 12 行:**这是一个在正常情况下应该有值的地方使用变量的例子。在我们使用`print`或`println`的任何地方，我们都输入一串文本(用双引号括起来)作为要打印的精确值。在这种情况下，我们给了它一个变量名。为了让`print`命令知道输出什么，它必须在变量内部寻找存储的值。所以这一行只是打印出当时存储在`roomTemp`变量中的任何值。
*   **第 13 行:**由于这个`print`语句表示我们想要显示的文本行的结尾，您可以使用一个`println`来输出必要的换行符。相反，我选择坚持使用`print`语句来演示如何在不使用`println`的情况下输出换行符。注意，这一行要输出的文本字符串以`n`结尾。这是一个特殊的字符代码，当被 Java 打印出来时，会被转换成一个换行符。如果你想打印出一个反斜杠后面跟着字母“n”，你必须在反斜杠前面加上第二个反斜杠(即“`\n`”)。第一个反斜杠取消了第二个反斜杠的特殊含义。换句话说，`\`是一个特殊的字符代码，在打印出来时会被转换成一个反斜杠，所以 Java 不再将`n`视为特殊的字符代码。

通过在命令行键入`javac PrintRoomTemp.java`照常编译程序，然后，假设编译器没有指出任何键入错误，运行程序的编译版本:

```
D:javaJavaLanguageBasics> **java PrintRoomTemp**

Current room temperature: 20 degrees Celsius
```

虽然这个程序中有一些新的技巧，但它的主要目的是演示如何使用存储在变量中的值。在本例中，我们打印出了值，但是正如您将在后面的示例中看到的，变量还可以以许多其他方式使用。

**Go to page:** [1](https://sitepoint.com///java-language-basics) | [2](https://sitepoint.com/java-language-basics-2/) | [3](https://sitepoint.com/java-language-basics-3/) | [4](https:sitepoint.com/java-language-basics-4/) | [5](https://sitepoint.com/java-language-basics-5/) | [6](https://sitepoint.com/java-language-basics-6/) | [7](https://sitepoint.com/java-language-basics-7/)

## 分享这篇文章