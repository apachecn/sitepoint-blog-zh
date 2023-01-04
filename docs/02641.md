# 理解 Java 变量和数据类型

> 原文：<https://www.sitepoint.com/understanding-java-variables-and-data-types/>

Java 变量使程序员能够存储单个数据点和信息，以备后用。为了以后使用的效率，Java 变量有类型。这些类型被称为[数据类型](https://www.sitepoint.com/typing-versus-dynamic-typing/)，因为为了方便和可预测性，它们允许我们分别存储不同类型的数据。在进入更高级的主题之前，任何 Java 程序员都有必要理解 Java 中变量和数据类型的基础知识。

为了说明 Java 变量是如何工作的，让我们想象一个照片共享应用程序。该应用程序将存储关于我们的应用程序的状态及其用户共享的照片的一系列信息:用户数量、共享的照片数量和共享的评论总数。这些数据必须被存储起来，以便我们在必要的时候操作并显示给我们的用户。输入 Java 变量。

## Java 变量

变量可以保存数据，并且数据可以在程序的生命周期中改变。一个变量必须有一个类型，一个名字，并被提供一些数据来保存。Java 中使用最广泛的数据类型是字符串，由 Java 的`String`类表示。一个字符串，比如“SitePoint”就是一个`String`类的实例。

### 命名变量

有几条规则你*必须*遵守，也有几条你应该遵守。Java 的变量名区分大小写，可以是任意数量的字母和数字。然而，变量名必须以字母、下划线字符`_`或美元符号`$`开头。

在 Java 中创建变量时，最好遵循使用描述变量用途的数字和完整单词的惯例，同时避免使用下划线字符和美元符号。最后，变量应该使用小写字母，这是一种流行的编程惯例，规定第一个单词的第一个字母应该小写，后面的单词应该大写。

![beans](img/561e2fd7ce05216d96a2bca452974e59.png)

## 使用变量

让我们创建应用程序主类的框架，看看如何在`String`变量中存储关于应用程序的上述数据点:

```
public class SitePointGram {
    public static void main(String[] args) {
        String nameOfApp = "SitePointGram";
        String numberOfUsers = "";
        String numberOfPhotos;
        String numberOfComments = null;
        //...
    }
} 
```

那里发生了什么事？让我们跳到 Java 代码块的第三行。在每一行，我们创建一个新的类型为`String`的变量来存储关于我们的应用程序的单点信息。注意，要在 Java 中创建一个变量，我们首先声明要存储在变量中的数据的类型，然后是小写的变量名称，然后是赋值操作符`=`，最后是要存储在变量中的数据。

在我们的`main`方法的第一行，我们将应用程序的名称存储在`nameOfApp` `String`变量中，其中存储的数据是“SitePointGram”。下一行有一个`String`变量，它将存储我们的应用程序中的用户数量。注意，它存储了一个空字符串`""`。在我们转到下两行时，请保持这种想法。

Java 中的每个变量都有默认值；字符串变量的默认值为`null`，“无”。如果我们在声明时不知道变量的值，我们可以省略用值显式初始化它，允许 Java 隐式地为它提供一个适当的默认值。这正是我们对变量`numberOfPhotos`所做的。类似地，在第四行，我们显式地将`numberOfComments` `String`变量初始化为`null`，尽管我们并不必须这样做。重要的是要理解空字符串是实际的字符串，尽管是空的，而`null`意味着变量还没有有效数据。我们继续。

SitePointGram 越来越受欢迎，人们蜂拥而至。让我们用 Java 来代表我们的应用程序的增长:

```
public static void main(String[] args) {
    //...
    numberOfUsers = "500";
    numberOfPhotos = "1600";
    numberOfComments = "2430";
    //..
} 
```

在初始化我们的`String`变量之后，现在很明显我们的应用程序有 500 个用户，1600 张共享的照片，以及这些照片上总计 2430 条评论。我们做得很好，所以是时候学习如何在 Java 中使用数据类型了。

### Java 数据类型

我们目前将所有数据点存储在`String`变量中，尽管其中一些是数字。字符串适用于表示字符串，如文本，但当我们想要表示数字和其他类型的数据(并对这些数据执行操作)时，我们可以使用 Java 提供的数据类型或创建自己的数据类型。

让我们看看如何更恰当地将数值数据点存储在变量中，这样我们就可以按预期使用它们:

```
public static void main(String[] args) {
    String appName = "SitePointGram";
    boolean appIsAlive = true;

    int numUsers = 500;
    int numPhotos = 1600;
    int numComments = 2430;
    //...
} 
```

远离我们最初的`main`方法，我们有了一个新的代码块，其中包含了适当数据类型的新变量。在我们的`main`方法体的第一行，保存我们的应用程序名称的变量现在更精确了:我们用`appName`代替了`nameOfApp`。在下一行，我们有一个[布尔变量](https://www.sitepoint.com/boolean-data-type/)，它存储了我们的应用程序的状态。一个`boolean`只能是`true`或`false`，因此当你想存储一个代表有效性的数据点时最好；在我们的例子中，我们的应用程序确实是活动的，直到我们需要关闭它进行维护。接下来的三个变量是类型`int`。`int`数据类型在 Java 中表示整数值。遵循与`appName`相同的模式，而不是按照`numberOfX`的方式命名我们的数字变量，我们应该在保持可读性的同时做到`numX`的精确。

数据类型`int`、`boolean`和`double`是 Java 中八种[原始数据类型中的三种。原始数据类型是由 Java 提供的特殊值，而不是由类构造的对象。记住字符串是`String`类的实例，所以它们是对象，而不是原语。数值数据类型的默认值是`0`，而`boolean`的默认值是`false`。](https://docs.oracle.com/javase/tutorial/java/nutsandbolts/datatypes.html)

与我们以前的`main`方法不同，我们的新变量集适当地存储数字，所以我们能够如我们所期望的那样操纵它们。通过将数字数据点存储在表示数字的变量类型中，我们可以对它们执行数学运算:

```
//a new user joined, increase the number of users by 1
numUsers += 1;
//multiply the number of photos by 2
numPhotos = numPhotos * 2;
//get the average number of photos by doing division
double avgPhotosPerUser = 1.0 * numPhotos / numUsers; 
```

我们的`main`方法中的最后一个变量保存每个用户平均照片数量的浮点值，这由`double`数据类型表示。我们通过将照片数量除以用户数量得到了这个结果。请注意，我们将第一个数字乘以 1.0，这样结果就不会四舍五入为最接近的整数。我们可以将一个浮点数存储为一个`float`或一个`double`；这里唯一的区别是，`double` (64 位)比`float` (32 位)能容纳更大范围的数字，因此也更常用。

最后要做的事情是看看如何用我们自己的数据类型来表示我们的数据。

```
String user1 = "id: 1, username: LincolnWDaniel, isOnline: true"; 
```

虽然像我们在`user1`中那样创建一串保存用户信息的字符串很容易，但创建一个类来构造用户对象会更好:

### 自定义 Java 数据类型(类)

```
public class User {
    public static int numUsers = 0;

    private int id;
    private String username;
    private boolean isOnline;
    /*other information about this user,
    perhaps a list of associated photos*/

    public User(String username) {
        id = numUsers++;
        this.username = username;
        this.isOnline = true;
        System.out.println(username + " signed up!");
    }

    public void logOn() { 
        isOnline = true; 
        printOnlineStatus();
    }

    public void logOff() { 
        isOnline = false; 
        printOnlineStatus();
    }

    private void printOnlineStatus() {
        System.out.println(username + " is online: " + isOnline);
    }
    //...
} 
```

在那里，我们有一个名为`User`的类。这个类简单地定义了从它创建的实例可以展示的属性和行为。这个类的属性只是不同数据类型的变量，在我们的应用程序中保存关于用户的信息。一个`User`类的实例可以拥有关于它自己的信息，从它的标识号到它的用户名，它的在线状态保存在一个`boolean`变量中，这个变量可以在用户登录或注销时更新。当用户被创建、登录或注销时，我们将这些信息打印到控制台。

在我们的应用程序中，每创建一个新用户，`numUsers`变量的值就加 1，这样我们的应用程序就可以随时知道有多少用户。通过添加更多的实例变量，可以向该类添加更多的信息。现在，让我们在应用程序的 main 方法中创建新数据类型`User`的实例:

```
public static void main(String[] args) {
    String appName = "SitePointGram";
    boolean appIsAlive = true;

    /*declare a new variable of type User 
    and store a new User instance with the name of "Lincoln" in it*/
    User lincoln = new User("Lincoln");

    //log lincoln off
    lincoln.logOff();

    //print out the number of users in our app
    System.out.println("Number of users: " + User.numUsers);
    //...
} 
```

在这段代码中，我们再次修改了我们的`main`方法。前两条线路保持不变，但我们现在有三条新线路。该方法中的第三行创建了一个新的`User`实例或对象，并将其存储在一个名为“lincoln”的变量中，下一行从我们的应用程序中注销`lincoln`，下一行通过从`User`类中访问`public` `static` `numUsers`变量，打印出应用程序中`User`实例的数量。需要注意的是，一个类的`static`变量属于该类，而不是该类的实例，所以我们不需要一个`User`的实例来访问`numUsers`。

## 结论

就是这样！现在，您已经了解了开始构建自己的数据类型或类所需了解的所有 Java 变量和数据类型。在我们的 GitHub 库上看一下本教程的源代码[,看看如何在它的基础上进行构建。](https://github.com/sitepoint-editors/sitepoint-java-variables-datatypes)

参考资料:
[Oracle 关于 Java 字符串的文档](https://docs.oracle.com/javase/7/docs/api/java/lang/String.html)
[Oracle 关于 Java 原始数据类型的文档](https://docs.oracle.com/javase/tutorial/java/nutsandbolts/datatypes.html)

## 分享这篇文章