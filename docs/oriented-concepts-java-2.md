# Java 中面向对象的概念—第 2 部分

> 原文：<https://www.sitepoint.com/oriented-concepts-java-2/>

****面向对象编程** (OOP)是当今软件开发领域的一股强大力量。有人说(在我看来非常正确),如果没有今天的面向对象语言所带来的**模块化**和**代码重用**,像 Microsoft Office 这样的大型软件项目背后的开发工作根本不可能管理。其他人更喜欢 OOP，因为它更好，更有趣！**

不管你学习 OOP 原理的原因是什么，Java 都是一门理想的语言。它对初学者来说足够友好，如果您熟悉基本的编程概念，就不会被复杂的语法所淹没，然而它是一种真正完整的面向对象的语言，不像 Perl、PHP 和 ASP 等许多其他 Web 开发语言，它们只提供面向对象的特性。

本文是 SitePoint 关于用 Java 开发动态网站的系列文章的第四篇，也是两个部分的第二篇，这两个部分的重点是教您需要了解的关于面向对象编程的知识，以便充分利用 Java 语言所提供的优势。如果您还没有阅读本系列的前几篇文章，我肯定会建议您从[开始，因为这里介绍的概念依赖于您对之前所有内容的了解。](http://www.webmasterbase.com/article.php/364)

在[第一部分](http://www.webmasterbase.com/article.php/397)中，我们看了**类**、**对象**、**属性**和**方法**的基本概念。我们开发了一个名为`Tree`的简单类以及一个程序来**实例化**几棵树并测试它们的`height`属性和它们的生长方法。在简单了解了**继承**之后，我们开发了`Tree`的一个子类叫做`CoconutTree`，我们看到了当**在 Java 中复制**和**比较**对象时你将面临的问题。在第二部分中，我们将继续学习一些更高级的方法和属性。我们将学习如何用这些来设计更好的类，展示好的面向对象软件设计的一些重要特征。此外，我们将了解一些高级的类设计概念，我将提供一个类包的解释，它让你将类组织成组。

手续办完了，让我们开始吧！

##### 传递参数和返回值

到目前为止，我们所看到的大多数方法都是一种特殊的类型。下面是这样一个方法的声明，我们在第一部分中开发的`CoconutTree`类的`pickNut`方法:

```
 public void pickNut() {  

    numNuts = numNuts â€“ 1;  

  }
```

这个方法和我们到目前为止看到的其他方法的特别之处在于，它不需要任何参数，也不需要**返回值**。当我们在本系列后面的文章中查看更实际的 Java 类示例时，您会发现大多数方法都做到了其中一项或两项。

参数是在调用函数以完全指定要采取的动作时必须提供的信息。例如，如果我们想让上面的`pickNut`方法更强大，我们可以给它一个参数来指示要采摘的坚果数量:

```
 public void pickNut(int numberToPick) {  

    numNuts = numNuts â€“ numberToPick;  

  }
```

在这个新版本的`pickNut`方法中，我们已经指定该函数接受一个整数(`int`)参数，其值存储在一个名为`numberToPick`的变量中。然后，该方法的代码使用它作为要从`numNuts`属性中减去的数字。因此，我们现在只需调用一次`pickNut`方法，就可以从`CoconutTree`中挑选尽可能多的坚果。下面是几个调用`pickNut`的例子:

```
CoconutTree ct = new CoconutTree(); // New tree  

// Presumably we grow a few nuts first...  

ct.pickNut(1); // Picks one nut  

ct.pickNut(5); // picks five nuts  

ct.pickNut(0); // Doesn't do anything  

int nuts = 10;  

ct.pickNut(nuts); // Picks ten nuts  

ct.pickNut(-1); // Picks -1 nut (??)
```

正如最后一行所示，这个方法有一个问题。因为它接受任何整数作为要拾取的坚果数量，所以没有什么可以阻止程序使用它来拾取负数的坚果。看看我们方法的代码，这实际上只是给树增加了更多的坚果，但是我们不应该允许对我们的对象进行在现实世界中没有意义的操作。另一个没有意义的操作是采摘比树上可采摘的更多的坚果！对于现有的代码，这将导致我们的树报告负的坚果数量——这几乎不是一个现实的情况。

这两个问题揭示了在设计需要参数的方法时的一个重要问题。在使用方法之前，应该始终确保传递给方法的值是有意义的。即使你只是计划在你自己的程序中使用一个类，当你不自动检查这些值时，很容易忘记什么值会和不会引起问题。

下面的修改版本的`pickNut`检查参数以确保它不是负的，并且不大于树上的坚果数:

```
 public void pickNut(int numberToPick) {  

    if (numberToPick < 0) return; // Cannot pick negative number  

    if (numberToPick > numNuts) return; // Not enough nuts  

    numNuts = numNuts â€“ numberToPick;  

  }
```

`return`命令立即终止该方法。因此，挑选坚果的操作(从`numNuts`属性中减去)只有在`if`语句中的两个条件都为假时才会发生。这确保了在我们允许提货操作继续进行之前，我们的两个约束得到满足。

这里还有一个问题。调用`pickNut`方法的代码如何知道挑选操作是否成功？毕竟，如果采摘操作因为其中一个约束条件不满足而失败，我们不希望我们的程序像能够采摘坚果一样继续运行。要解决这个问题，我们必须再次改变`pickNut`；这一次，我们将让它返回一个值:

```
 public boolean pickNut(int numberToPick) {  

    if (numberToPick < 0) return false;  

    if (numberToPick > numNuts) return false;   

    numNuts = numNuts â€“ numberToPick;  

    return true;  

  }
```

方法不仅可以在被调用时接收参数值，还可以通过将值指定为 return 命令的一部分来发回值。在这个新版本的代码中，我们将方法声明中的 void 替换为 boolean。这表明函数在终止时将返回一个布尔值(真/假)。在这个特殊的例子中，如果挑选操作成功，我们选择返回 true，如果由于任何原因失败，我们选择返回 false。这使我们能够将调用该方法的程序的代码组织成如下形式:

```
if (!ct.pickNut(10)) {  

  System.out.println("Error: Could not pick 10 nuts!");  

  System.exit();  

}  

nutsInHand = nutsInHand + 10;
```

`if`语句的条件调用参数值为 10 的`pickNut`，然后检查它的返回值看是否为假(注意`!`操作符)。如果是，将打印出一条错误消息。然后，`System.exit`方法立即终止程序，这是对意外错误的合理反应。否则，程序照常进行。

返回值的另一个常见用途是创建执行一些常见计算并返回结果供程序使用的方法。在本系列的剩余部分中会有更多的例子供您学习。

##### 课程包

在第一部分的大部分时间里，我们使用了一个名为`Tree`的类。现在，虽然`Tree`不是一个特别原创的类名，但是它非常适合这个类。问题是相同的名字可能也适合另一个类，这样你就有了命名冲突。当您开始编写自己的类时，这种冲突并不太严重；然而，当你需要引入一组别人为你的程序编写的类时，事情会变得混乱。

例如，考虑一下，如果您已经设计了所有的类来处理一个程序的逻辑，该程序将跟踪衣服纽扣的销售，会发生什么情况。在这种情况下，拥有一个名为 Button 的类是很自然的，但是你的老板告诉你他或她想要一个漂亮的图形用户界面。令您沮丧的是，您发现内置于 Java 中用于在用户界面上创建按钮的类被称为(您猜对了)Button。如何解决这种冲突，而不必重新检查代码并更改对按钮类的每个引用？

班包来救援了！Java 提供了类包(通常称为“包”)，作为根据类的用途、编写它们的公司或您喜欢的任何其他标准将类分组的一种方式。只要您确保您的 Button 类与 Java 的内置 Button 类不在同一个包中，您就可以在程序中使用这两个类，而不会产生任何冲突。

默认情况下，您创建的类驻留在**默认包**中，这是一个未命名的包，所有未被分配包的类都位于该包中。对于大多数程序来说，将类保留在默认包中是安全的。所有 Java 的内置类，以及您在互联网上和其他软件供应商那里找到的大多数类都被分组到包中，所以您通常不必担心您的类的名称与默认包中其他类的名称冲突。

如果您打算在将来的项目中重用您的类(其中新的类名可能与您想要重用的类名冲突)，或者如果您想要分发它们供其他开发人员使用(其中他们的类名可能与您自己的类名冲突)，您将需要将您的类分组到包中。要把你的类放在一个包中，你只需要在你的文件顶部的一行给出这个包的名字。约定是使用“com”后跟您公司的名称作为您的软件包名称。例如，我们在 SitePoint.com 开发的类被分组到`com.sitepoint`包中，方法是在 java 文件的顶部添加下面一行:

```
package com.sitepoint;
```

请注意，当编译驻留在包中的类时，类文件将被放置在基于包名的目录中。例如，编译属于包`com.sitepoint`的`Button.java`会在当前目录的`com/sitepoint/`子目录中创建`Button.class`文件。要运行或使用这样一个包中的类，你应该像在包含`com`子目录的目录中一样引用它。因此，要运行`com.sitepoint.MyProgram`类，您应该转到包含`com`的目录(该目录包含`sitepoint`，该目录又包含`MyProgram.class`文件)并键入:

```
C:javadev> java com.sitepoint.MyProgram
```

Java 会自动寻找`com/sitepoint/MyProgram.class`。

事实证明，内置到 Java 中的 Button 类实际上在`java.awt`包中，该包还包含了所有其他用于在 Java 中创建基本图形用户界面的类(AWT 代表抽象窗口工具包，以防您感到疑惑)。这样，Java 的按钮类的**全限定名**就是`java.awt.Button`。要使用这个类而不让你的程序认为你在引用你自己的按钮类，你可以使用这个全名。例如:

```
// Create a Java Button   

java.awt.Button b = new java.awt.Button();
```

事实上，Java *要求*你使用与当前类不在同一个包中的任何类的全名！

但是如果你的程序没有*和*一个按钮类来与 Java 内置的冲突呢？每次拼出完整的类名意味着大量的额外输入。为了避免这种麻烦，您可以**将下面一行代码放在您的。java 文件(就在`package`行下面，如果有的话):**

```
import java.awt.Button;
```

一旦它被导入，您就可以通过它的简称(按钮)来使用该类，就好像它是您的类所在的同一个包的一部分一样。

另一个方便的特性允许您将整个类包导入到当前包中。这在为你的程序创建用户界面时再次派上了用场，因为要创建一个像样的界面，你可能需要使用来自`java.awt`包的十几个或更多的类，并且在单独的`import`行中按名称列出每个类会变得像在你的代码中键入类的全名一样乏味。要导入整个`java.awt`包以便在一个类中使用，而不必键入它们的全名，可以在文件的顶部添加下面一行:

```
import java.awt.*;
```

包含 Java 语言所有最基本的类的`java.lang`包(例如，`System`类，我们一直以`System.out.println()`方法的形式在屏幕上显示文本)被自动导入到每个 Java 文件中。

在您可以导入或使用一个类的完全限定名从另一个包中访问它之前，它必须被声明为`**public**`。默认情况下，类只能由同一包中的代码使用。显然，如果您计划将您的代码分发给其他人使用，或者在多个项目中重用您的类，这不是一个理想的限制；因此，任何你认为对类包之外的代码有用的类都应该是公共的。这样做很简单，只需在类声明的开头添加关键字`public`。例如，将我们的树类声明为 public 是有意义的:

```
package com.sitepoint;   

public class Tree {   

  ...   

}
```

同样的道理也适用于`CoconutTree`:

```
package com.sitepoint;   

public class CoconutTree extends Tree {   

  ...   

}
```

##### 访问控制修饰符

正如我们刚刚看到的，一个类可以被公开，以允许它的包之外的代码使用它。类**成员**，包括属性和方法，可以类似地修改来控制对它们的访问。像类一样，成员有一个**默认访问**设置，限制对同一包内代码的访问。考虑以下示例声明:

```
 int numNuts = 0;    

  boolean pickNut(int numberToPick) {    

    ...    

  }
```

即使类被声明为公共的，上面的`numNuts`属性的值(假设它被声明为对象类的属性)也只能被同一个包中的代码访问或修改。类似地，上面显示的`pickNut`方法只能由与类在同一个包中的代码调用。

与类一样，属性和方法可以声明为 **`public`** :

```
 public int numNuts = 0;    

  public boolean pickNut(int numberToPick) {    

    ...    

  }
```

任何代码(来自任何包中的任何类)都可以访问和修改公共属性值或调用公共方法。

属性和方法有两个类没有的附加访问设置。第一个是 **`private`** :

```
 private int numNuts = 0;    

  private boolean pickNut(int numberToPick) {    

    ...    

  }
```

只有同一类中的代码才能访问私有属性和方法。如果您想在一个对象中存储仅对该对象本身或同一类的其他对象有用的信息，或者如果您想限制对信息的访问(正如我们将在下一节中看到的，这是良好的类设计的一部分！)，那么你应该为它使用一个私有属性。同样，执行内部计算或对其他类无用的方法应该声明为私有。

方法和属性可以采用的最终访问设置是`**protected**`:

```
 protected int numNuts = 0;    

  protected boolean pickNut(int numberToPick) {    

    ...    

  }
```

`protected`模式与默认模式非常相似，因为它拒绝访问类的包之外的代码，但是它引入了一个例外:当前类的子类(即扩展该类的类)也可以访问受保护的成员。

区分每种访问控制设置适用的情况需要一些经验。一开始很容易把所有事情都声明出来，这样你就不用担心什么时候可以访问什么时候不可以。虽然这肯定可行，但这绝对不符合面向对象编程的精神。尤其是您计划重用或分发的代码，将受益于被分配最严格的适当的访问控制设置。这其中的一个原因将在下一节中说明。

##### 用访问器封装

之前，我们修改了`pickNut`方法，这样它就不会接受太高的数字，这会导致我们的`CoconutTree`认为它包含负数的椰子。但是有一个更简单的方法来产生这种不现实的情况:

```
CoconutTree ct = new CoconutTree();     

ct.numNuts = -10;
```

我们如何保护对象属性不被赋予像这样没有意义的值呢？解决方案是将属性本身设为私有，并且只允许使用方法来访问它们。下面是我们的`CoconutTree`类的更新版本，它利用了这种技术:

```
1  package com.sitepoint;     

2     

3  public class CoconutTree extends Tree {     

4    private int numNuts = 0;      

5     

6    public void growNut() {     

7      numNuts = numNuts + 1;     

8    }     

9     

10   public boolean pickNut(int numToPick) {     

11     if (numToPick < 0) return false;     

12     if (numToPick > numNuts) return false;     

13     numNuts = numNuts â€“ numToPick;     

14     return true;     

15   }     

16     

17   public int getNumNuts() {     

18     return numNuts;     

19   }     

20     

21   public boolean setNumNuts(int newNumNuts) {     

22     if (newNumNuts < 0) return false;     

23     numNuts = newNumNuts;     

24     return true;     

25   }     

26 }
```

正如您在第 4 行看到的，`numNuts`属性现在是私有的，这意味着只有这个类中的代码才允许访问它。`growNut`和`pickNut`属性保持不变；他们可以继续直接更新`numNuts`属性(`pickNut`中的约束确保`numNuts`的值保持合法)。因为我们仍然希望代码能够确定树中坚果的数量，所以我们添加了一个公共的`getNumNuts`方法，该方法简单地返回`numNuts`属性的值。至于设置螺母数量，我们增加了一个`setNumNuts`方法，该方法以整数值为参数。检查该值以确保它是正数或零(因为我们不能有负数的螺母)，然后将`numNuts`属性设置为这个新值。

这两个新方法，`getNumNuts`和`setNumNuts`，被称为**访问器方法**；也就是说，它们是用于访问属性的方法。访问器是设计良好的对象的典型特征。即使在任何值都可以接受的情况下，也应该将对象的属性设为私有，并提供访问方法来访问它们。这样做可以让你的程序展示面向对象编程的一个重要特性，叫做封装。

**封装**意味着一个对象的内部表示与它呈现给程序中其他对象的接口是分离的。换句话说，使用你的类的程序员只需要知道*公共方法做什么，而不是它们如何工作。这样做的好处是，您可以更改类的工作方式来提高性能或添加新功能，而无需破坏依赖于原始类提供的方法的任何代码。*

例如，如果您决定将每个椰子表示为类`Coconut`的一个单独的对象，而不是使用一个整数变量来计数，那么您可以进行必要的更改，并且仍然拥有与上面的实现相同的四个方法。用原始接口编写的旧代码将像以前一样继续工作，而新代码可以利用新特性(当然是由新方法提供的)。

作为练习，重写`Tree`类，使其正确封装其`height`属性。

##### 构造器

构造函数是一种特殊类型的方法，在创建对象时会自动调用。构造函数允许您指定属性值，以及其他类似的初始化细节。

再次考虑我们的`Tree`类；具体来说，它的`height`属性的声明(现在应该是私有的，并带有访问器方法):

```
 private int height = 0;
```

在这里，我们关心的是“`= 0`”部分。为什么所有新树的高度都应该为零？使用构造函数，我们可以让这个类的用户指定树的初始高度。它看起来是这样的:

```
 private int height;      

  public Tree(int height) {      

    if (height < 0) this.height = 0;      

    else this.height = height;      

  }
```

乍一看，这看起来只是一个普通的方法。然而，有两个不同之处:

*   构造函数从不返回值；因此，它们没有返回类型(`void`、`int`、`boolean`等)。)在他们的宣言中。
*   构造函数与它们用来初始化的类同名。既然我们写的是`Tree`类，那么它的构造函数也必须命名为`Tree`。按照惯例，这是方法名应该大写的唯一情况。

所以一行一行地分析，我们声明了一个公共构造函数，它接受一个参数并将其值赋给一个整型变量`height`。注意，这是*而不是*对象属性`height`，我们马上就会看到。第二行检查`height`变量是否小于零。如果是，我们将树的`height`属性设置为零(因为我们不想允许负的树高)。如果没有，我们将参数的值赋给属性。

注意，由于我们有一个名为`height`的局部变量，我们必须将当前对象的`height`属性称为`this.height`。`this`是 Java 中的一个特殊变量，总是指当前代码正在其中执行的对象。如果这让你困惑，你可以把构造函数的参数命名为类似于`newHeight`的名字。然后，您就可以简单地将对象属性称为`height`。

因为`Tree`类现在有一个带参数的构造函数，所以在创建新树时，您必须为该参数指定一个值:

```
Tree myTree = new Tree(10); // Initial height 10
```

##### 重载方法

有时候同一方法有两个不同的版本是有意义的。例如，当我们修改了`CoconutTree`类中的`pickNut`方法，要求一个指定要挑选的坚果数量的参数时，我们失去了通过调用`pickNut()`就能挑选单个坚果的便利。Java 实际上允许您并排声明方法的两个版本，并通过调用方法时传递的参数的数量和类型来确定使用哪个版本。像这样用多个版本声明的方法被称为**重载方法**。

下面是如何声明两个版本的`pickNut`方法:

```
 public boolean pickNut() {       

    if (numNuts == 0) return false;       

    numNuts = numNuts â€“ 1;       

    return true;       

  }       

  public boolean pickNut(int numToPick) {       

    if (numToPick < 0) return false;       

    if (numToPick > numNuts) return false;       

    numNuts = numNuts â€“ numToPick;       

    return true;       

  }
```

一种节省输入的方法是注意到`pickNut()`实际上只是`pickNut(int numToPick)`的一个特例；也就是说，调用`pickNut()`和调用`pickNut(1)`是一样的，所以只需进行等价的调用就可以实现`pickNut()`:

```
 public boolean pickNut() {       

    return pickNut(1);       

  }       

  public boolean pickNut(int numToPick) {       

    if (numToPick < 0) return false;       

    if (numToPick > numNuts) return false;       

    numNuts = numNuts â€“ numToPick;       

    return true;       

  }
```

这不仅节省了两行代码，而且如果您改变了`pickNut`方法的工作方式，您只需要调整一个方法，而不是两个。

构造函数可以像普通方法一样重载。如果您错过了创建高度为零的新树的便利，您可以声明第二个不带参数的构造函数:

```
 private int height;       

  public Tree() {       

    this(0);       

  }       

  public Tree(int height) {       

    if (height < 0) this.height = 0;       

    else this.height = height;       

  }
```

注意，我们通过调用更复杂方法的一个特例(`Tree(0)`)来实现更简单的方法(`Tree()`)，再次节省了一些输入。然而，在构造函数的情况下，你用一个特殊的名字`this`来称呼它。

##### 高级继承:重写方法

我在本文的第一部分讨论了继承，但是为了简洁起见，我忽略了一个高级问题，现在我想讨论一下:**重写方法**。如你所知，类`CoconutTree`的对象继承了它所基于的`Tree`类的所有特性。因此，`CoconutTree`和`Tree`一样有`grow`的方法。

但是如果你想让椰子在生长时长出新芽呢？当然，您可以在每次导致`CoconutTree`增长时调用`growNut`方法，但是如果您能以完全相同的方式对待`Tree`和`CoconutTree`并让它们在它们类型的对象增长时都做它们应该做的事情，那就更好了。

要让相同的方法在子类中做不同的事情，你必须用子类中的新定义**覆盖**那个方法。简单地说，您可以在`CoconutTree`类中重新声明`grow`方法，让它做一些不同的事情！这里有一个关于`grow`的新定义，你可以添加到你的`CoconutTree`类中:

public void grow(){
height = height+1；
grown ut()；
}

简单吧？但是如果您在`Tree`类中的`grow`方法中添加了新的功能会怎么样呢？你如何确保这是由`CoconutTree`类继承的？就像在我们对重载方法的讨论中，我们通过调用更复杂方法的特例来实现简单方法，我们可以通过引用超类中方法的定义来实现子类中方法的新定义:

```
 public void grow() {        

    super.grow();        

    growNut();        

  }
```

`super.grow()`行调用了超类中定义的`grow`版本，这样我们就不用重新发明轮子了。当您创建一个扩展了没有源代码的类的类时(例如，另一个开发人员提供的类文件)，这尤其方便。通过简单地调用您正在重写的方法的超类版本，您可以确保您的对象没有丢失任何功能。

构造函数可以像普通方法一样被重写。下面是一组用于`CoconutTree`类的构造函数，以及不带初始值的`numNuts`属性的新声明:

```
 private int numNuts;        

  public CoconutTree() {        

    super();        

    numNuts = 0;        

  }        

  public CoconutTree(int height) {        

    super(height);        

    numNuts = 0;        

  }        

  public CoconutTree(int height, int numNuts) {        

    super(height);        

    if (numNuts < 0) this.numNuts = 0;        

    else this.numNuts = numNuts;        

  }
```

前两个构造函数覆盖了它们在`Tree`类中的对等物，而第三个是全新的。注意，我们将超类的构造函数称为`super()`。我们的三个构造函数都调用超类中的一个构造函数，以确保我们不会丢失任何功能。

##### 静态成员

如果你回头检查我们到目前为止看到的每一个代码示例，应该只有一个关键词让你困惑。令人惊讶的是，它出现在本系列的第一篇文章中，并且出现在我们迄今为止编写的每一个 Java 程序的开头:

```
 public static void main(String[] args) {
```

以防你没发现，问题中的关键词是`**static**`。方法和属性都可以声明为静态的。静态成员属于类，而不属于该类的对象。在我解释为什么 main 方法被声明为 static 之前，让我们看一个更简单的例子。

知道我们的程序中已经创建的`Trees`的总数可能是有用的。为此，我们可以在`Tree`类中创建一个名为`totalTrees`的静态属性，并修改构造函数，使其值在每创建一棵树时增加 1。然后，使用一个名为`getTotalTrees`的静态方法，我们可以通过调用`Tree.getTotalTrees()`随时检查该值。

下面是修改后的`Tree`类的代码:

```
public class Tree {         

  private static int totalTrees = 0;           

  private int height;         

  public Tree() {         

    this(0);         

  }         

  public Tree(int height) {         

    totalTrees = totalTrees + 1;         

    if (height < 0) this.height = 0;         

    else this.height = height;         

  }         

  public static int getTotalTrees() {         

    return totalTrees;         

  }         

  ...         

}
```

静态成员主要在两种情况下有用:

*   当您想要跟踪某个类的所有成员共享的一些信息时(如上所述)。
*   当一个属性或方法有多个实例没有意义时。

`main`函数是后一种情况的一个例子。因为拥有不止一个`main`函数的实例是没有意义的(即一个程序只能有一个程序)，所以它被声明为静态的。

我们见过的另一个静态成员的例子是`System`类的`out`属性(也称为`System.out`，我们在很多场合使用过它的`println`方法)。因为只有一个系统在运行这个程序，所以用一个包含静态属性(如`out`)和方法(如`exit`)的类来表示，而不是用一个类的实例来表示。

如果您不能完全理解在程序和`System`类中使用静态成员背后的原因，不要太担心。只要您能够理解上面的树计数示例是如何跟踪所创建的`Tree`对象的总数的，您就处于良好的状态。

##### 摘要

我不会责怪你在这一点上感到有点不知所措。如果你和我一样，你对 Java 面向对象编程(OOP)的第一印象是它非常强大，非常复杂，与你以前接触过的任何东西都非常不同。

幸运的是，你习惯了。

随着您继续阅读本系列的其余部分，本文中介绍的有时令人恐惧的概念将融入到软件组件的自然工作方式中，这些软件组件对现实世界的事物进行建模，并以合理的方式协同工作以实现预期的结果。你看的 Java 越多，自己写的 Java 越多，你就越习惯这种工作方式，你就越不想回到过去的工作方式。最终，面向对象编程更有趣！

现在，您已经完全掌握了 Java 语言本身。在你能够将这些知识付诸实践之前，你需要学习的是一组类。Java 包括用于执行各种任务的类包，从构建图形用户界面到创建动态网站。在本系列的下一篇文章中，我将向您介绍与构建 Java Servlets 相关的类，Java Servlets 是 Java 支持的动态网站的最基本组件。

## 分享这篇文章