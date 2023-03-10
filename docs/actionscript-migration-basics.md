# 将 ActionScript 1.0 迁移到 ActionScript 2.0，第 1 部分:基础知识

> 原文：<https://www.sitepoint.com/actionscript-migration-basics/>

ActionScript 2.0 (AS 2.0)是在 Flash 的最新版本(Flash MX 2004)中引入的，虽然 ActionScript 1.0 (AS 1.0)已经被开发人员和实验人员所接受，但它从来都不是真正的面向对象语言——它更基于对象。因此，它没有引起主流应用程序开发人员的注意。另一方面，AS 2.0 很像其他面向对象的语言，如 C#和 Java，它们使用新的 ECMA JavaScript 2.0 标准。

这使得开发更具可扩展性、健壮性和模块化的项目变得更加容易。

在 AS 1.0 中，创建原型来扩展功能是一个学习过程，而不是逻辑过程。此外，如果您意外地从一种数据类型转换为另一种数据类型(数字转换为字符串，反之亦然)，尽管应用程序可能已经工作，但任何错误都不会通过输出面板报告回来。

让我们先简要讨论一下 ActionScript 2.0 中的主要增强，然后再看一些简单的、真实的 AS 2.0 实例。请注意，您可以下载本教程的源文件。

##### 强数据类型

在 AS 2.0 中创建变量和对象时，您应该养成声明数据类型的习惯。这与 AS 1.0 的不同之处在于，以前，您可以使用松散类型的变量，这可能会在应用程序中引入故障点。

在 AS 2.0 中编译电影或检查脚本错误时，会检查可变数据类型，任何错误都会作为错误消息发送到“输出”面板。考虑以下代码:

```
var NotANumber:String = 15; 

var NotAString:Number = "Fifteen";
```

这段代码将输出 2 条错误消息，因为变量`NotANumber`的赋值是一个数字，而不是一个字符串。同样，变量`NotAString`的赋值是一个字符串，而不是`Number`声明的数据类型。

这是一个非常简单的程序，一旦你习惯了，它就会成为你的第二天性。

##### 区分大小写

如果您不习惯在编码时考虑大小写，那么当您的 AS 2.0 项目停止工作时，您可能会感到有点震惊。考虑以下代码:

```
var TextField_001 = "Some Information"; 

var textfield_001 = "Different Information"; 

trace(TextField_001); 

trace(textfield_001);
```

如果您使用 AS 2.0 在 Flash 中预览电影，您将在“输出”面板中看到这两个变量，因为它们被解释为单独的变量；在 AS 1.0 中，您只会看到一个值，因为 AS 1.0 在很大程度上是不区分大小写的。

##### 创建 AS 2.0 类

在 AS 1.0 中，最佳实践是将您的`ActionScript`外部化到一个外部`.as`文件中，而不是将代码包含在时间轴的一个帧中。在 AS 2.0 中，这一点更进了一步。当创建新的类时，您不再有选择—所有的类必须位于一个单独的`.as`文件中。

您可以使用 Flash MX 2004 Professional、记事本、PrimalScript 或其他类似的编辑工具轻松创建您的`.as`文件。每个`.as`文件只能定义一个类，并且文件名必须与类定义的名称完全匹配。例如:

```
class AlphaFade{ 

  // Class Properties, Constructors and Methods go here! 

}
```

类别`AlphaFade`必须保存在本地设置文件夹中名为`AlphaFade.as`的文件中。在 Windows 上，文件必须保存在以下位置:

```
[Drive]:Documents and Settings[Username]Local SettingsApplication DataMacromediaFlash MX 2004[Language]ConfigurationClasses
```

其中:

```
[Drive] = Installation Drive  

[Username] = Your Login Account  

[Language] = Flash MX 2004 localization language (usually for International English, it is 'en')
```

*注意:在我的 Flash 博客[这里](https://www.sitepoint.com/blog/)中概述了类文件应该保存在本地设置文件夹中的原因。*

##### 属性和方法特性

将变量或函数定义为私有或公共决定了方法或属性可用的范围。如果该属性被标记为 private，则只能通过该类进行访问，而如果它被标记为 public，则可以在该类之外进行访问。请考虑以下情况:

```
public function checkIfEmpty (incomingString:String):Boolean { 

  if ( incomingString.length == 0) 

  { 

    return true;  

  } else { 

    return false;  

  } 

}
```

函数`checkIfEmpty()`是一个公共函数，可以通过类从 Flash 电影的时间轴中访问(稍后将详细介绍)。

以下变量只能在类本身中访问:

```
private var incomingString:String;
```

##### 扩展属性

`extends`属性(我们马上会看到)用于从其他类继承公共和私有方法或属性。

在下面的例子中，我们可以简单地通过创建一个名为`ShiftingClip`的新类文件来扩展`MovieClip`类。

```
class ShiftingClip extends MovieClip { 

  // Properties, Constructor, Methods go here 

}
```

尽管还有其他重要的属性(动态、静态、getters 和 setters ),我们将在后面的文章中讨论它们。现在，我们只需要完成一些基本的基础工作，就可以让您开始运行了。

无需解释所有的 AS 2.0 OOP 结构，我们现在可以放心地继续看如何构造类文件并从我们的 Flash 电影中引用它们。

##### 将 AS 1.0 功能迁移到 AS 2.0 类

![1343_image1](img/fff010e916635324f77b466bbe63a76c.png)

每当给定编程语言的新版本出现时，通常意味着 API 已经升级、添加了新的例程或者已经重写。

在 AS 2.0 的例子中，原型链接已经被重写，还有很多其他的东西。这产生了一种更加模块化和可控的编程方法，允许相对容易地开发定制类和扩展已经创建的类。

因此，我们可以将基于 Flash 5 语法或 ActionScript 1.0 的代码毫无困难地迁移到新的 AS 2.0 OOP 环境中。

在这个例子中，我们将把一块验证代码从基于时间轴的位置迁移到外部类文件中。代码将检查字符串是否有内容，是否满足最小字符串长度，是否包含数字内容。将返回一个布尔值，它标识已满足的条件。

##### 设置场景

1.  在 Flash MX 2004 中，创建一个新的 Flash 文档，接受默认的大小和帧速率。

3.  重命名默认层`Actions`，下面再添加一层，命名为`Items`。

5.  将三个`TextInput`组件从组件面板拖动到`Items`层的第一帧，在舞台的右侧将它们上下对齐。

从上到下命名实例:`anyContent`、`minLength`和`anyNumeric`。

7.  从组件面板中拖动`TextInput`组件的另一个实例，并将其对齐到先前添加的`TextInput`组件的右侧。命名实例`username`。

我们将使用`username`文本区域来输入数据，使用三个垂直对齐的文本区域来提供我们正在检查的字符串的状态反馈。

9.  创建 3 个静态文本框，并将它们与每个`TextInput`组件实例的顶部对齐。在方框中输入以下文本，从上到下依次为:`'Empty?'`、`'Minimum Length?'`和`'Numeric Content?'`

11.  将 Flash 文档保存到您选择的位置。

既然我们已经将相关组件添加到舞台并设置好场景，我们就可以使用我们的 AS 1.0 代码并开始升级到 AS 2.0 的过程。

##### 迁移代码

下面显示的是我们想要迁移到 AS 2.0 的 AS 1.0 版本代码。在最初的 AS 1.0 版本中，我使用动态文本框来保存信息，但在这个版本中，我将使用一些 2.0 版本的组件，因为它们设置起来更快。

这是一个基本的、事件驱动的设置，其中我们有一个事件监听器附加到`username`2.0 版`TextInput`组件。当组件的内容改变时(即当有人开始输入时)，调用`simpleValidation()`函数。这充当包装器函数，调用三个验证函数。

这些函数的结果随后被推送到三个`TextInput`实例，因此我们可以检查数字内容、空字段和最小所需长度。又好看又简单！

```
validationListener = new Object(); 

validationListener.change = function() { 

  simpleValidation(); 

}; 

username.addEventListener("change", validationListener); 

function simpleValidation() { 

  minLength.text  = checkStringLength(username.text,10); 

  anyNumeric.text = checkContainsNumeric(username.text); 

  anyContent.text = checkIfEmpty(username.text); 

} 

function checkStringLength(incomingString, stringLength) { 

  if (incomingString.length >= stringLength) { 

    return true; 

  } else { 

    return false; 

  } 

} 

function checkContainsNumeric(incomingString) { 

  var invalidChars ="1234567890"; 

  for (i=0; i<incomingString.length && containsNumbers == false; i++) 

  { 

    var character= incomingString.charAt(i); 

    if (invalidChars.indexOf(character) != -1) 

    { 

      return true; 

    } 

  } 

  return false; 

} 

function checkIfEmpty(incomingString) { 

  if(incomingString.length == 0) 

  { 

    return true; 

  } else { 

    return false; 

  } 

}
```

我们现在拥有的是将基于 AS 1.0 的代码快速转换成基于 AS 2.0 的类的能力，使其更易于管理和扩展。

##### 创建类文件

类文件的结构非常简单；它看起来像这样:

```
class yourClassName {  

// Properties   

  var myString:String;  

  var myNumner:Number;  

  var myMovieClip:MovieClip;  

// Constructor function  

  function yourClassName() {  

  }  

// Methods  

  public function yourFunction(){  

  }  

}
```

我们一会儿将创建一个带有自定义属性、构造函数和方法的`custom`类文件，这样我们可以看到一个真实的类文件是如何操作的。

1.  在 Flash MX 2004 Professional(选择“文件”>“新建”>“ActionScript 文件”)或您选择的文本编辑器中，创建一个名为`Retro.as`的新文件。

*注意:只有 Flash MX 2004 Professional 本身支持在 Flash 中编辑外部`.as`类文件。*

*   添加以下代码:

```
class Retro {  

  private var incomingString:String;  

  private var stringLength:Number;  

  //Constructor  

  function Retro(incomingString:String) {}  

  //Methods  

  public function checkStringLength(incomingString:String,  

                                    stringLength:Number):Boolean {  

    this.incomingString = incomingString;  

    this.stringLength = stringLength;  

    if (this.incomingString.length >= stringLength) {  

      return true;  

    } else {  

      return false;  

    }  

  }  

  public function checkContainsNumeric(incomingString:String):Boolean {  

    this.incomingString = incomingString;  

    var invalidChars:String = "1234567890";  

    var i:Number;  

    for (i=0; i < this.incomingString.length && containsNumbers == false; i++)  

    {  

      var character:String = this.incomingString.charAt(i);  

      if (invalidChars.indexOf(character) != -1)  

      {  

        return true;  

      }  

    }  

    return false;  

  }  

  public function checkIfEmpty(incomingString:String):Boolean {  

    this.incomingString = incomingString;  

    if (this.incomingString.length == 0)  

    {  

      return true;  

    } else {  

      return false;  

    }  

  }  

}
```

*   将`Retro.as`保存到您的本地设置文件夹，或者与 FLA:

```
[Drive]:Documents and Settings[Username]Local SettingsApplication DataMacromediaFlash MX 2004[Language]ConfigurationClasses
```

其中:

```
[Drive] = Installation Drive   

[Username] = Your Login Account   

[Language] = Flash MX 2004 localization language (usually for International English, it is 'en')
```

现在我们已经创建了外部类文件，让我们看看它是如何组合在一起的。

首先，我们创建类名`Retro`并定义一些简单的私有属性。

请注意，构造函数`Retro()`与定义类共享相同的名称和大小写，并且对于类内的额外灵活性是必要的。

它还接受数据类型 String 的传入参数，称为`incomingString`。

```
class Retro {  

  private var incomingString:String;  

  private var stringLength:Number;  

  //Constructor  

  function Retro(incomingString:String) {}
```

然后我们继续这个类的方法，在这个例子中，是一个名为`checkString()`的公共函数。它接受两个传入参数:`incomingString`，其数据类型为`String`，以及`stringLength`，其数据类型为 Number。

`public function checkStringLength(incomingString:String, stringLength:Number):Boolean {`

除了传入的参数，我们还定义了作为`Boolean`返回的数据的数据类型。

在那里，我们使用`this.keyword`语法定义类的属性:

`this.incomingString = incomingString;  
   this.stringLength = stringLength;`

该值用于检查有效数据。在这种情况下，我们检查`incomingString`变量的长度是否大于或等于`stringLength`变量。然后我们将公共函数的值返回给`callee`(我们稍后会添加)。

`if(this.incomingString.length >= this.stringLength){  
     return true;  
   } else {  
     return false;  
   }  
 }`

类中的其他方法遵循类似的规则，但是检查缺少的和数字的内容，所以我们可以跳过它们。

##### 添加动作脚本

既然我们已经创建了外部类文件，剩下的工作就是添加与类挂钩的代码——然后，我们可以测试功能了！

1.  选择 FLA 中动作层的第一帧，并将以下代码添加到“动作”面板中:

```
var validateMe:Retro = new Retro();  

validationListener = new Object();  

validationListener.change = function() {  

  simpleValidation();  

};  

username.addEventListener("change", validationListener);  

function simpleValidation() {  

  minLength.text = validateMe.checkStringLength(username.text, 10);  

  anyNumeric.text = validateMe.checkContainsNumeric(username.text);  

  anyContent.text = validateMe.checkIfEmpty(username.text);  

}
```

您可以看到(从粗体代码中)我们已经创建了一个对类文件的新引用，名为`validateMe`。当框内的值改变时，我们调用`simpleValidation()`包装函数，就像我们在 AS 1.0 函数中所做的那样。然后，像以前一样，我们将函数的返回值推送到相关的`TextInput`实例。

*   保存 FLA 并选择“控制”>“测试影片”。

开始在文本框中键入之前，您应该看到三个反馈文本区域不包含任何信息。一旦开始输入，change 事件处理程序就会被触发，文本框的内容会被发送到三个公共函数。它们的返回值被返回到相关的反馈区域。

如果向文本区域添加超过 10 个字符，最小内容长度触发器将从 false 变为 true(尝试更改下面一行中的值以改变最小长度)。

```
validateMe.checkStringLength(username.text,10);
```

如果您在字段中添加数字内容，这也将在 SWF 中从`checkContainsNumeric()`方法得到反馈。

这就对了。毕竟，将我们的 AS 1.0 代码迁移到可伸缩、可重用和可移植的 AS 2.0 代码并不困难。

你可能会想，为什么我要用 AS 2.0 写东西呢？有什么好处？将现有代码从 AS 1.0 升级到 AS 2.0，或者用 AS 2.0 编写新应用程序的主要原因在于模块化、可重用性和可伸缩性，更不用说用面向对象编程语言开发代码将有助于开发更好的编码实践。

如果我们希望创建一系列的类来扩展`MovieClip`类的功能并允许动态不透明度渐变，我们可以创建下面的`.as`文件，保存为`FadeClip.as`，如下所示:

```
class FadeClip extends MovieClip{  

  //Properties  

  private var MCName:MovieClip;  

  private var currentAlpha:Number;  

  private var differenceAlpha:Number;  

  private var fadeStep:Number;  

  private var endPosition:Number;  

  private var stepSize:Number;  

  //Constructor  

  function FadeClip() {}  

  //Methods  

  public function AlphaFade(MCName:MovieClip, startAlpha:Number, finalAlpha:Number) {  

    currentAlpha = MCName._alpha;  

    differenceAlpha = finalAlpha-currentAlpha;  

    fadeStep = differenceAlpha / 10;  

    MCName._alpha = MCName._alpha + fadeStep;  

  }  

}
```

由于公共函数`AlphaFade()`接受一个`MovieClip`参数，以及起始和最终的 alpha 参数，我们可以很容易地将任何给定的`MovieClip`从一个值淡入另一个值。为此，我们将向时间轴上的一个帧添加以下代码。

*注意:这段代码引用了时间轴中名为`TitleToAnimate`的`MovieClip`实例。*

```
var fader:FadeClip = new FadeClip();  

_root.onEnterFrame = function() {  

  fader.AlphaFade(TitleToAnimate, 10, 100);  

}
```

通过将`TitleToAnimate MovieClip`实例的`_alpha`属性设置为任意值 10，我们可以将不透明度从起始值(10)渐变到最终值(100)。

因为 2.0 迁移似乎吓到了很多人，但是，一旦开始，就再也不会回头了！就像从自行车上摔下来一样简单…差不多！

## 分享这篇文章