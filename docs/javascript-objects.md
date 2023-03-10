# 重新思考 JavaScript 对象

> 原文：<https://www.sitepoint.com/javascript-objects/>

任何使用 JavaScript 工作过一段时间的开发人员都熟悉创建定制 JavaScript 对象的艰巨任务。作为一个 Java 背景的人，我花了很多时间试图弄清楚如何让 JavaScript 对象在各方面都更像 Java 对象。我们定义 JavaScript 对象的方式似乎有太多的重复，我发现这令人沮丧。经过一些思考和大量阅读，我想出了一些方法来帮助消除困扰我编码多年的重复。

##### 反思继承

众所周知，JavaScript 使用基于原型的对象继承方法。这说明如果我有一个名为 ClassA 的“类”,如果我将 ClassB 的原型制作成 ClassA 的实例，ClassB 可以继承它，如下所示:

```
function ClassA () { 

} 

function ClassB() { 

} 

ClassB.prototype = new ClassA;
```

这种方法通过“对象伪装”技术得到了增强，该技术用于将一个对象的属性(而不是其方法)复制到另一个对象。为了完全继承 ClassA 到 ClassB 的所有内容，我们实际上需要这样做:

```
function ClassA () { 

} 

function ClassB() { 

        this.superclass = ClassA; 

        this.superclass(); 

        delete this.superclass; 

} 

ClassB.prototype = new ClassA;
```

要完成像这样用 Java 处理的事情，需要大量的代码:

```
Class ClassA { 

} 

Class ClassB extends ClassA { 

}
```

在我的开发过程中，我一次又一次地重复这一点，我发现自己越来越讨厌重复和浪费的 JavaScript 代码，我不得不编写这些代码只是为了从一个类继承到另一个类的属性和方法。然后我在网景的 DevEdge 网站上发现了一个有趣的概念。

Bob Clary，一个网景的传道者，写了一个如此简单的函数，我想知道为什么我自己没有想到它。在他的文章[“inherit from-A Simple Method of Inheritance on Demand”](http://devedge.netscape.com/toolbox/examples/2003/inheritFrom/)中，他定义了一个名为`inheritFrom()`的简单方法，可用于将一个对象的所有属性和方法复制到另一个对象。

与 JavaScript 中编写的每个克隆方法基本相同，这个函数使用一个`for..in`循环遍历给定对象的属性和方法，并将它们复制到另一个对象。电话应该是这样的:

```
inheritFrom(ClassB, ClassA);
```

虽然这是个好主意，但它不适合我的编码风格。我仍然认为 JavaScript 构造函数是类，我编写它们时更多考虑的是定义 Java 类而不是 JavaScript 构造函数。我突然想到，如果我扩展原生 JavaScript Object 类来包含一个做同样事情的方法，所有对象都将自动获得这个方法，我就可以编写出看起来和感觉上非常类似于 Java 逻辑的东西。我的解决方案是:`extends()`方法。

就像 Clary 的解决方案一样，`extends()`方法的工作基础是所有属性和方法都可以使用括号符号进行迭代，如下所示:

```
object["Property"];  //Same as object.Property
```

该方法本身如下所示:

```
Object.prototype.extends = function (oSuper) { 

        for (sProperty in oSuper) { 

                this[sProperty] = oSuper[sProperty]; 

        } 

}
```

该方法接受一个参数`oSuper`，它是我们想要继承(或“扩展”)的类的实例化。内部代码基本上与 Clary 的`inhertFrom()`函数相同，除了:

*   我使用 this 关键字来表示接收复制属性的对象，并且
*   我取消了尝试..catch 块，以便该方法也可以用于 Netscape Navigator 4.x。

定义了此方法后，我们现在可以执行以下操作:

```
function ClassA () {  

}  

function ClassB() {  

        this.extends(new ClassA());  

}
```

需要注意的是，这个方法应该在你的类(构造函数)定义中首先被调用。任何补充都应在初次通话后进行。还要注意，你必须实例化一个你想继承的类的对象；您不能简单地传入类名本身。例如，这是不正确的:

```
function ClassA () {  

}  

function ClassB() {  

        this.extends(ClassA);   //INCORRECT!!!!  

}
```

这个强大的新函数还提供了从两个不同的类继承并保留这两个类的所有属性和方法的联合的可能性。假设 ClassZ 想同时继承 ClassY 和 ClassX。在这种情况下，我们的代码如下所示:

```
function ClassX (sMsg) {  

    this.message = sMsg;  

}  

function ClassY (sName) {  

    this.name = sName  

}  

function ClassZ() {  

        this.extends(new ClassX("Hello World"));  

        this.extends(new ClassY("Nicholas C. Zakas"));  

}  

var oTest = new ClassZ();  

alert(oTest.message);  

alert(oTest.name);
```

这种方法大大减少了我花在调试继承问题上的时间，因为现在只有一行代码，而不是四行代码。

##### 重新思考属性

在 Java 中，我们通常不允许人们直接访问属性。例如，你很少会看到这样的事情:

```
Class ClassA {  

      public string message;  

}  

ClassA Test = new ClassA();  

Test.message = "Hello world";
```

相反，类通常为每个属性(本身是私有的)定义了 getters 和 setters，如下所示:

```
Class ClassA {  

      private string message;  

      public void setMessage(String msg) {  

          this.message = msg;  

      }  

      public String getMessage() {  

          return this.message;  

      }  

}  

ClassA Test = new ClassA();  

Test.setMessage("Hello world");
```

这是处理对象属性的更好方法，因为它提供了对数据的额外控制措施。然而在 JavaScript 中，我们经常看到这样的情况:

```
function ClassA() {  

    this.message = "";  

}  

var Test = new ClassA();  

Test.message = "Hello world";
```

为了使我的 JavaScript 类更像 Java，我得出结论，如果我能定义一个属性并自动创建一个 getter 和 setter，这个过程会变得更简单。

经过一番思考，我想到了本机 JavaScript 对象的`addProperty()`方法:

```
Object.prototype.addProperty = function (sName, vValue) {   

        this[sName] = vValue;   

        var sFuncName = sName.charAt(0).toUpperCase() + sName.substring(1, sName.length);   

        this["get" + sFuncName] = function () { return this[sName] };   

        this["set" + sFuncName] = function (vNewValue) {   

                        this[sName] = vNewValue;   

        };   

}
```

这个方法有两个参数:sName 是参数的名称，vValue 是它的初始值。该方法做的第一件事是将属性赋给对象，并赋予它初始值 vValue。接下来，我创建 sFunc name 作为 getter 和 setter 方法的一部分使用…这只是将属性名中的第一个字母大写，以便它看起来适合放在“get”和“set”旁边(即，如果属性名是“message”，方法应该是“getMessage”和“setMessage”)。接下来的几行为这个对象创建了 getter 和 setter 方法。

这可以这样使用:

```
function ClassA () {   

    this.addProperty("message", "Hello world");   

}   

var Test = new ClassA();   

alert(Test.getMessage());    //outputs "Hello world"   

Test.setMessage("Goodbye world");   

alert(Test.getMessage());    //outputs "Goodbye world"
```

像这样定义属性要容易得多，但是我意识到在某些时候我可能需要从这些方法中获取一些信息。毕竟一个“套”不准怎么办？我可以重写这个方法，但是我必须对每个属性都这样做。

相反，我选择创建一些代码来模拟 IE 的 onpropertychange 事件。也就是说，将定义一个 onpropertychange()方法，每当对象的任何属性发生更改时，将使用描述该事件的对象调用该方法。但是，我的自定义事件对象只有几个属性:

*   `propertyName`已更改的属性的名称
*   `propertyOldValue`属性的旧值
*   `propertyNewValue`属性的新值
*   `returnValue`-默认情况下为真，可在`onpropertychange()`方法中设置为假以取消更改

代码现在看起来像这样:

```
Object.prototype.addProperty = function (sName, vValue) {   

        this[sName] = vValue;   

        var sFuncName = sName.charAt(0).toUpperCase() + sName.substring(1, sName.length);   

        this["get" + sFuncName] = function () { return this[sName] };   

        this["set" + sFuncName] = function (vNewValue) {   

                var vOldValue = this["get" + sFuncName]();   

                var oEvent = {    

                        propertyName: sName,    

                        propertyOldValue: vOldValue,    

                        propertyNewValue: vNewValue,    

                        returnValue: true    

                        };   

                this.onpropertychange(oEvent);   

                if (oEvent.returnValue) {   

                        this[sName] = oEvent.propertyNewValue;   

                }   

        };   

}   

//default onpropertychange() method â€“ does nothing   

Object.prototype.onpropertychange = function (oEvent) {   

}
```

如您所见，只有 setter 方法发生了变化。它现在做的第一件事是通过调用相应的 getter 方法获取属性的旧值。接下来，创建自定义事件对象。四个属性中的每一个都被初始化，然后对象被传递给`onpropertychange()`方法。

默认情况下，`onpropertychange()`方法什么都不做。它旨在当定义新类时被覆盖。如果自定义事件对象从`onpropertychange()`方法返回，并且`returnValue`仍然设置为 true，那么属性被更新。否则，该属性不会更新，实际上使其成为只读属性。

有了这些代码，我们现在可以做以下事情:

```
function ClassB() {    

    this.addProperty("message", "Hello world");    

    this.addProperty("name", "Nicholas C. Zakas");    

}    

ClassB.prototype.onpropertychange = function(oEvent) {    

    if (oEvent.propertyName == "name") {    

        oEvent.returnValue = false;  //don't allow name to be changed    

    }    

}    

var Test = new ClassB();    

alert(Test.getMessage()); //outputs "Hello world"    

Test.setMessage("Goodbye world");    

alert(Test.getMessage());  //outputs "Goodbye world"    

alert(Test.getName());      //outputs "Nicholas C. Zakas"    

Test.setName("Michael A. Smith");     

alert(Test.getName());       //outputs "Nicholas C. Zakas"
```

为了允许检测存储在属性中的值的类型，可以做一点补充。实际上，我们正在向使用`addProperty()`方法添加的任何属性添加类型检查:

```
Object.prototype.addProperty = function (sType, sName, vValue) {    

        if (typeof vValue != sType) {    

            alert("Property " + sName + " must be of type " + sType + ".");    

            return;    

        }    

        this[sName] = vValue;    

        var sFuncName = sName.charAt(0).toUpperCase() + sName.substring(1, sName.length);    

        this["get" + sFuncName] = function () { return this[sName] };    

        this["set" + sFuncName] = function (vNewValue) {    

                 if (typeof vNewValue != sType) {    

                    alert("Property " + sName + " must be of type " + sType + ".");    

                    return;    

                }    

                var vOldValue = this["get" + sFuncName]();    

                var oEvent = {     

                        propertyName: sName,     

                        propertyOldValue: vOldValue,     

                        propertyNewValue: vNewValue,     

                        returnValue: true     

                        };    

                this.onpropertychange(oEvent);    

                if (oEvent.returnValue) {    

                        this[sName] = oEvent.propertyNewValue;    

                }    

        };    

}
```

这里，我添加了一个参数`sType`，它定义了属性保存的数据类型。我把它作为第一个参数，因为这和 Java 类似。我还使用 JavaScript typeof 操作符添加了两个检查:一个检查初始值的赋值，另一个检查属性何时被更改(实际上，这些应该会抛出错误，但是为了与 Netscape 4.x 兼容，我选择了警告)。对于那些不知道的人，typeof 运算符返回下列值之一:

*   “未定义”值不存在。
*   "字符串"
*   "数字"
*   “功能”
*   "对象"
*   "布尔型"

参数 sType 必须与这些值中的一个相匹配，以使这是一个有效的检查。在大多数情况下，这应该足够好了(如果不行，您总是可以编写自己的函数来代替 typeof 使用)。值得注意的是，null 值将从 typeof 运算符返回“object”。

更新前面的示例，我们现在可以这样做:

```
function ClassB() {    

    this.addProperty("string", "message", "Hello world");    

    this.addProperty("string", "name", "Nicholas C. Zakas");    

    this.addProperty("number", "age", 25);    

}    

ClassB.prototype.onpropertychange = function(oEvent) {    

    if (oEvent.propertyName == "name") {    

        oEvent.returnValue = false;  //don't allow name to be changed    

    }    

}    

var Test = new ClassB();    

alert(Test.getMessage()); //outputs "Hello world"    

Test.setMessage("Goodbye world");    

alert(Test.getMessage());  //outputs "Goodbye world"    

alert(Test.getName());      //outputs "Nicholas C. Zakas"    

Test.setName("Michael A. Smith");     

alert(Test.getName());       //outputs "Nicholas C. Zakas"    

alert(Test.getAge());      //outputs 25    

Test.setAge("45");         //generates error message    

alert(Test.getName());       //outputs 25
```

##### 结论

虽然 JavaScript 在创建自定义对象和类方面给我们留下了一些严重的限制，但它也足够灵活，可以找到解决方案。JavaScript 似乎正朝着在语法和实现上更接近 Java 的方向前进(参见( [JavaScript 2.0 提案](http://www.mozilla.org/js/language/js20/))，但同时本文中的代码应该会让您的开发工作不那么痛苦。

我已经在 Netscape Navigator 4.79、Internet Explorer 6.0 和 Netscape 7.0 (Mozilla 1.0.1)上测试了本文中的代码，但我相信它应该可以在大多数现代浏览器中工作。

## 分享这篇文章