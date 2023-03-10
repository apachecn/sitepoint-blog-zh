# 使用 LiveConnect 连接 Java 和 JavaScript

> 原文：<https://www.sitepoint.com/connect-java-to-javascript-with-liveconnect/>

LiveConnect 是一种允许 Java 和 JavaScript 相互通信的技术。它允许您的 Java 类调用 JavaScript 方法并访问 JavaScript 环境。JavaScript 也可以访问 Java 对象并调用它们的方法。LiveConnect 最早是在 Netscape 浏览器中实现的，目前 Mozilla Firefox 完全支持这一功能。在本教程中，您将学习如何编写代码，使 Java 和 JavaScript 之间的通信成为可能。

LiveConnect 有两个主要方面:

*   从 JavaScript 调用 Java 方法
*   在 Java 中使用 JavaScript 对象

## 从 JavaScript 访问 Java:

每当您需要访问 Java 对象、类、数组或包时，只需使用以下四个 LiveConnect 对象之一。

*   `JavaObject`–用于从 JavaScript 访问 Java 对象。
*   `JavaClass`–用作对 Java 类的引用。
*   `JavaArray`–用于访问 Java 数组。
*   `JavaPackage`–用作对 Java 包的引用。

### 使用`JavaObject`

您可以创建一个 Java 对象，并在关键字`new`的帮助下将它赋给 JavaScript 中的一个变量。当您创建一个 Java 类的实例时，JavaScript 会自动创建一个`JavaObject`对象。例如，您可以从 JavaScript 实例化一个 Java `String`，并将其赋给一个变量。然后可以使用点运算符来访问对象的`length()`方法，如下所示。

```
var myString=new java.lang.String(&quot;Test String&quot;);
alert(myString.length()); //prints 11
```

### 使用`JavaClass`

每当您在代码中引用一个 Java 类并将它赋给一个变量时，JavaScript 运行时会自动创建一个`JavaClass`对象。例如，下面的代码创建了一个`JavaClass`对象。

```
var myInteger=java.lang.Integer;
alert(myInteger.MIN_VALUE);
```

### 使用`JavaPackage`

类似地，每当您的 JavaScript 代码引用 Java 包时，JavaScript 运行时会自动创建一个`JavaPackage`对象。在本例中，我们正在创建一个类`MyClass`的实例，它位于`mypackage`包中。

```
var myVar=new Packages.mypackage.MyClass();
```

如果你的类不是任何包的一部分，可以直接实例化它，如下例所示。

```
var myVar=new Packages.MyClass();
```

常用的类，比如那些在`java`、`sun`和`netscape`包中的类，可以用下面的方法实例化。

```
var myVar=new java.lang.String();
```

上述代码等效于以下代码:

```
var myVar=new Packages.java.lang.String();
```

### 使用`JavaArray`

每当您在 JavaScript 代码中创建一个 Java 数组时，就会自动创建对象。例如，下面的代码创建了一个包含五个`String`的 Java 数组。

```
var myArray=java.lang.reflect.Array.newInstance(java.lang.String,5);
```

稍后，您可以使用`length`属性打印数组的长度。

```
var myArray=java.lang.reflect.Array.newInstance(java.lang.String,5);
alert(myArray.length);  //outputs 5
```

## 在 Java 代码中访问 JavaScript 对象

我们主要使用以下两个类从 Java 访问 JavaScript 环境。

*   `netscape.javascript.JSObject`–用于访问 JavaScript 方法和属性。
*   `netscape.javascript.JSException`–用于 Java 代码内部的异常处理。

默认情况下，代码无法访问这些类。要使这些类可访问，您需要将包含这些类的 jar 添加到您的`CLASSPATH`中。打开 JRE 安装目录，转到`lib`文件夹。你会发现一个名为`plugin.jar`的 jar 文件。将这个 jar 添加到您的类路径中，以便在运行您的应用程序时 JRE 可以使用上面的两个类。

### 使用`JSObject`

Java 中出现的所有 JavaScript 对象都是类型`JSObject`。还记得我们讨论过从 JavaScript 调用 Java 方法吗？您可以从 JavaScript 代码中调用任何 Java 方法，并将 JavaScript 对象作为参数传递给该方法。然后这些对象在 Java 端被转换成类型`JSObject`。为了让这些方法工作，你需要在 Java 方法签名中定义类型为`JSObject`的形参。下面的例子展示了 Java 类`Player`如何通过其构造函数获取`JSObject`，并使用它来获取 JavaScript 对象的成员。

```
public class Player{
  public String name,age;
  public Player(JSObject js){
    this.name=(String)js.getMember("name");
    this.age=(String)js.getMember("age");
  }
}

```

接下来，我们将在 JavaScript 中创建一个名为`Player`的对象。

```
function Player(name,age){
  this.name=name;
  this.age=age;
}
```

现在可以创建一个 JavaScript 对象`Player`，并在实例化它时将其传递给 Java `Player`类的构造函数。

```
var player=new Packages.Player(new player(&quot;John Doe&quot;,&quot;20&quot;));
// instantiates Java class Player and passes the JavaScript Player object as argument.
```

### 使用`JSException`处理 Java 中的 JavaScript 错误

在访问 JavaScript 环境时，Java 代码可能会出错。在这种情况下，`JSException`类来拯救。下面的例子演示了`JSException`的用法。

```
public class TestException{
  public Object process(JSObject js, String method){
    try{
      js.eval(method);
      //tries to call a JavaScript method. If method is undefined it will throw an exception.
    }
    catch(JSException e){
      System.out.println(e);
    }
    return null;
  }
}

```

### 一个用法示例

既然我们已经知道了如何从 JavaScript 调用 Java 方法，以及如何在 Java 内部使用 JavaScript 对象，那么是时候创建一个小应用程序了，这样整体概念就清楚了。我们将创建一个非常简单的应用程序，要求用户输入他/她的姓名、年龄和选择的编程语言。根据所选择的语言，应用程序将显示一条消息，指明要学习的最佳框架。当我们使用 LiveConnect 构建应用程序时，我们在 Java 和 JavaScript 中都有一个`Programmer`类。

页面中还有一个 applet。这样做是因为 LiveConnect 可访问的第一个对象是公共 applet 实例。从 applet 中我们可以得到我们想要的对象。在我们的程序中，`LauncherApplet`有一个返回一个`Programmer`对象的方法。首先，我们需要调用这个方法在 JavaScript 代码中获得一个`Programmer`实例。基本思想是预先填充`Programmer`对象。然后，在加载 HTML 页面后，显示一条显示该对象各种属性的消息。然后，从 HTML 表单中获取用户输入，并构造一个 JavaScript `Programmer`对象。

下一步是将新构造的 JavaScript 对象传递给 Java 方法`setData`。Java 方法读取对象的属性并更新其属性。确保`Programmer`类被声明为公共的，否则无法从 JavaScript 访问它。最后一步，我们调用 Java 对象上的另一个方法`getAdvice()`，该方法返回关于用户应该使用哪个框架的个性化建议。

Java `Programmer`类的源代码如下所示。

```
import netscape.javascript.*;
public class Programmer {
  public String name="Sandeep";
  public String age="20";
  public String language="Java";
  public void setData(JSObject js){
    try{
      this.name=((String)js.getMember("name")).toLowerCase();
      this.age=((String)js.getMember("age")).toLowerCase();
      this.language=((String)js.getMember("language")).toLowerCase();
      System.out.println("Name= "+name+",Age= "+age+",language= "+language);
    }
    catch(JSException e){System.out.println(e);}
  }
  public String getAdvice(){
    String advice="";
    switch(language){
      case "php":
        advice=name+", you should definitely try out CodeIgniter.";
        break;
      case "java":
        advice=name+", you should definitely try out JavaServer Faces.";
        break;
      case "python":
        advice=name+", you should definitely try out Django.";
        break;
      default:
        advice="You language of choice is not any one of PHP, Java or Python";
    }
    return advice;
  }
}

```

我们的`LauncherApplet`类如下所示:

```
import java.applet.*;
import java.awt.*;
public class LauncherApplet extends Applet{
  public Programmer getProgrammer(){
    return new Programmer();
  }
}

```

我们的 HTML 页面的结构如下:

```
&lt;html&gt;
  &lt;head&gt;
    &lt;title&gt;Applet Test&lt;/title&gt;
    &lt;script type=&quot;text/javascript&quot; src=&quot;liveconnect.js&quot;/&gt;
  &lt;/head&gt;
  &lt;body onload=&quot;showProgrammer()&quot;&gt;
    &lt;applet id=&quot;app&quot; code=&quot;LauncherApplet&quot; height=&quot;1&quot; width=&quot;1&quot; MAYSCRIPT&gt;&lt;/applet&gt;
    &lt;table&gt;
      &lt;tr&gt;
        &lt;td&gt;Name&lt;/td&gt;
        &lt;td&gt;:&lt;/td&gt;
        &lt;td&gt;&lt;input type=&quot;text&quot; id=&quot;name&quot;/&gt;&lt;/td&gt;
      &lt;/tr&gt;
        &lt;td&gt;Age&lt;/td&gt;
        &lt;td&gt;:&lt;/td&gt;
        &lt;td&gt;&lt;input type=&quot;text&quot; id=&quot;age&quot;/&gt;&lt;/td&gt;
      &lt;/tr&gt;
        &lt;td&gt;Programming Language(PHP,Java or Python)&lt;/td&gt;
        &lt;td&gt;:&lt;/td&gt;
        &lt;td&gt;&lt;input type=&quot;text&quot; id=&quot;language&quot;/&gt;&lt;/td&gt;
      &lt;/tr&gt;
    &lt;/table&gt;
    &lt;input type=&quot;button&quot; onclick=&quot;processProgrammer()&quot; value=&quot;click&quot;/&gt;
  &lt;/body&gt;
&lt;/html&gt;
```

在`liveconnect.js`中，我们定义了两个函数和一个`Programmer`类。下面定义了`Programmer`类。

```
function Programmer(name,age,language){
  this.name=name;
  this.age=age;
  this.language=language;
}
```

接下来，我们定义一个函数`showProgrammer()`，它显示了页面加载时 Java 对象`Programmer`的属性。该函数的代码如下所示:

```
function showProgrammer(){
  try{
    var programmer=app.getProgrammer(); //app is the id of applet tag which refers to the applet instance.
    var data=&quot;Name= &quot;+programmer.name+&quot;,Age= &quot;+programmer.age+&quot;,Language= &quot;+programmer.language;
    alert(data);
  }
  catch(e){
    alert(e);
  }
}
```

最后，我们定义了读取姓名、年龄和编程语言选择输入的`processProgrammer()`函数。然后，它使用这些值创建一个 JavaScript `Programmer`对象，并在 Java 端调用`setData()`。JavaScript `Programmer`对象是`setData()`的参数。在下一步中，我们使用 Java `Programmer`对象的属性来准备建议。最后，建议被返回到 JavaScript 端。

```
function processProgrammer(){
  try{
    var name=document.getElementById(&quot;name&quot;).value;
    var age=document.getElementById(&quot;age&quot;).value;
    var language=document.getElementById(&quot;language&quot;).value;
    var programmer=app.getProgrammer();
    var myprog=new Programmer(name,age,language);
    programmer.setData(myprog);
    var advice=programmer.getAdvice();
    alert(advice);
  }
  catch(e){
    alert(e);
  }
}
```

## 结论

通过连接 JavaScript 和 Java，你可以创建非常强大的应用程序。但是，并非所有浏览器都完全支持 LiveConnect。上面的应用程序在 Firefox 11.0 中测试，Java 代码使用 JDK 7 编译。所以，如果你想测试代码样本，确保你的系统上安装了 JDK 7。要了解更多关于 LiveConnect 的信息，请访问 Mozilla 开发者网络上的 [LiveConnect。](https://developer.mozilla.org/en/docs/LiveConnect)

## 分享这篇文章