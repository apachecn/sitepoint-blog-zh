# JavaScript 面向对象编程第 2 部分文章

> 原文：<https://www.sitepoint.com/oriented-programming-2/>

在本系列的第 1 部分中，我们讨论了对象、对象方法和对象类别。现在，让我们继续。

##### 争论

在每个函数中，都会自动创建一个私有变量`argument`，保存传递给函数的参数数组。例如:

```
function testArg(){ 

  for(i=0;i<arguments.length;i++){ 

    alert("Argument "+i+" is "+arguments[i]); 

  } 

}
```

如上面的例子所示，我们可以访问在调用函数时传递的一组参数，这个函数的作用域中有一个`arguments`变量。这个例子表明，在定义函数时，我们可以访问函数中的所有参数，而无需将它们指定为参数。当我们不知道到底要传递多少个参数时，这特别有用。

因此，我们可以使用:

```
testArg("PageResource","SitePoint","JavaScriptCity", 

        "WebSite Abstraction");
```

…获得一些我最喜欢的 Web 开发站点的提醒。

##### 复杂示例

现在我们已经有了 JavaScript 中基于对象编程的基础，让我们构建一个复杂的基于对象的例子，一个库。我们只是跟踪一些基本信息，比如书名、作者、页数和价格。为了实现这一点，我们将有一个`Person`对象(代表每个作者)、一个`Book`对象和一个`Library`对象。首先，让我们创建`Person()`对象构造函数:

```
function Person(lastName, firstName){ 

  this.lastName = lastName; 

  this.firstName = firstName; 

}
```

现在，让我们创建一些`Person`对象的实例:

```
var DnnyGdmn = new Person("Goodman","Danny"); 

var DvdFlngn = new Person("Flanagan","David"); 

var TmMyrs = new Person("Myers","Tom"); 

var AlxNkmvsky = new Person("Nakhimovsky","Alexander");
```

接下来，让我们创建我们的`Book`对象。它的属性将是:

*   标题
*   页
*   价格
*   作者

最后，同一本书可以有多个作者，所以我们需要能够接受多个`Person`对象作为作者。为此，我们将创建一个数组来保存这本书的每个作者。

```
function Book(title, pages, price){ 

  this.title = title; 

  this.pages = pages; 

  this.price = price; 

  this.authors = new Array(arguments.length-3); 

  for(i=0;i<arguments.length-3;i++){ 

    this.authors[i] = arguments[i+3]; 

  } 

}
```

代码的第一部分应该看起来简单明了；但是，最后一部分可能不会。所以，让我们更仔细地研究一下:

```
this.authors = new Array(arguments.length-3);
```

这为我们的`Book`对象创建了一个`author`属性。`author`属性本身就是一个`Array`对象。当我们调用我们的`Book()`构造函数时，前三个参数分别是`title, pages`和`price`，所以在这些参数之后指定的参数是我们的作者。因此，如果我们传递五个参数，我们知道其中两个必须是作者。所以我们可以创建一个长度为`arguments.length-3`的`Array`对象。

```
for(i=0;i<arguments.length-3;i++){ 

  this.authors[i] = arguments[i+3]; 

}
```

这段代码循环遍历参数，并将它们分配给`Array`对象。现在，让我们看看如何创建这个`Book`对象的实例:

```
 var JavaNut = new Book("Java Foundation Classes in a  

Nutshell", 731, 29.95, DvdFlngn); 

  var JSTDR = new Book("Javascript: The Definitive Guide (3rd  

Edition)", 776, 39.95, DvdFlngn); 

  var JSBible = new Book("Javascript Bible, 4th Edition",  

1200, 49.99, DnnyGdmn); 

  var DHTMLTDR = new Book("Dynamic Html: The Definitive  

Reference", 1073, 44.95, DnnyGdmn); 

  var JSObj = new Book("JavaScript Objects", 450, 39.99,  

TmMyrs, AlxNkmvsky);
```

注意，我们将传递`Person`对象的实例作为最后的参数，以创建`Book`对象的`authors`属性。OOP 设计(如关系数据库设计)中的一个关键概念是避免数据中的重复。因此，我们为每个不同的作者创建一个`Person`对象。所以，即使大卫·弗拉纳根可能写了多本书，我们总是引用同一个`Person`对象。此外，如果 David 决定将他的名字改为“Bebop ”,我们可以很容易地为所有记录更改它，只需更改保存该信息的一个`Person`对象。另外，请注意，我们可以传递对象，而不是传递原始数据类型。例如，对于标题，我们可以传递一个`String`对象，对于页码，我们可以传递一个`Number`对象。然而，在这里，它们没有多大用处，所以我们使用了原始数据类型——这正好符合我们的需求。

现在，让我们转到可能是最难的对象构造函数，即`Library()`构造函数。我要把这个分成几个部分:

```
function Library(){ 

  this.books = new Array(arguments.length); 

  for(i=0;i<arguments.length;i++){  

    this.books[i] = arguments[i]; 

  }
```

关于这个函数，您可能注意到的第一件事是它没有参数。这是因为它只接受`Book`对象，尽管我们不知道有多少。它创建了`Library`对象的属性`books`，该属性存储了`Book`对象的`Array`。假设我们想要访问一本书的第一个列出的作者。我们可以使用:

```
this.books[*bookIndex*].authors[0]
```

我们首先访问库的`book`属性，它是一个`Array`对象。然后，我们访问一个特定的`Book`对象。从那个`Book`对象，我们访问它的`authors`属性，这是一个数组。最后，我们访问一个特定的`Person`对象。从那里，我们可以继续访问那个`Person`对象的`firstName`或`lastName`属性。注意 *`bookIndex`* 是我们要访问的书的索引。

现在，这是我们的`Library`对象将包含的唯一属性。剩下的将是方法:

```
this.totalPrice = function(){ 

  var totalCost = 0; 

  for(i=0;i<this.books.length;i++){ 

    totalCost += this.books[i].price; 

  } 

return totalCost; 

}
```

这个方法循环遍历我们的`books`属性，它是一个`Array`对象，获取每个`Book`对象的价格并将其相加，最后返回值。

```
this.averagePrice = new Function("return this.totalPrice 

()/this.books.length");
```

这个方法将我们所有书籍的总价除以我们拥有的书籍数量，得出我们书籍的平均价格。那么，一旦我们创建了一个`Library`对象，我们如何向它添加更多的书呢？我们将不得不创建另一个函数:

```
this.addBook = new Function("book", "this.books.push(book)");
```

这使用了`Array`的内置方法`push()`。`push()`方法将作为参数传递的值或对象添加到其`Array`对象，同时确保更改`Array`的`length`属性。最后，我们将创建一个方法来显示我们库中作者的名字。这个方法相当长，所以我把它分开:

```
this.getAuthors = function(){ 

  var toSay = "Your favorite authors are:n";
```

这创建了一个方法，我们将使用它来检索作者列表。`toSay`变量将保存该方法返回的字符串。

```
for(i=0;i<this.books.length;i++){ 

  for(j=0; j<this.books[i].authors.length;  

j++){ 

    var authName = 

    this.books[i].authors[j].firstName + " " + 

    this.books[i].authors[j].lastName;
```

这部分代码遍历所有的书，然后遍历该书的所有作者，将他们的名字放在`authName`变量中。

```
if(toSay.indexOf(authName)!=-1) continue;         

  toSay+="nt"+authName;
```

如果这个作者已经在`toSay`变量中，我们不想再添加他，所以我们继续循环这本书的作者。但是，如果作者还没有被列出，我们可以继续将他或她添加到`toSay`变量中。

```
 } 

    } 

    return toSay; 

  } 

}
```

这关闭了我们已经打开的两个`for`循环，并返回了`toSay`变量。它还关闭了我们已经定义的方法`getAuthors()`，以及`Library()`构造函数。现在，让我们把所有的代码放在一起，创建一个新的`Library`对象:

```
// define our Person() constructor 

function Person(lastName, firstName){ 

  this.lastName = lastName; 

  this.firstName = firstName; 

} 

// define our Book() constructor 

function Book(title, pages, price){ 

  this.title = title; 

  this.pages = pages; 

  this.price = price; 

  this.authors = new Array(arguments.length-3); 

  for(i=0;i<arguments.length-3;i++){ 

    this.authors[i] = arguments[i+3]; 

  } 

} 

//define our Library() constructor 

function Library(){ 

  this.books = new Array(arguments.length); 

  for(i=0;i<arguments.length;i++){ 

    this.books[i] = arguments[i]; 

  } 

  this.totalPrice = function(){ 

    var totalCost = new Number(0); 

    for(i=0;i<this.books.length;i++){ 

      totalCost += this.books[i].price; 

    } 

  return totalCost; 

  } 

  this.averagePrice = new Function("return  

this.totalPrice()/this.books.length"); 

  this.addBook = new  

Function("book","this.books.push(book)"); 

  this.getAuthors = function(){ 

    var toSay = "Your favorite authors are:n"; 

    for i=0;i<this.books.length;i++){ 

      for(j=0;j<this.books[i].authors.length;j++){ 

        var authName =  

        this.books[i].authors[j].firstName + " " +  

        this.books[i].authors[j].lastName; 

        if(toSay.indexOf(authName)!=- 

1)continue; 

        toSay+="nt"+authName; 

      } 

    } 

  return toSay; 

  } 

} 

// create some Person objects 

DnnyGdmn = new Person("Goodman","Danny"); 

DvdFlngn = new Person("Flanagan","David"); 

TmMyrs = new Person("Myers","Tom"); 

AlxNkmvsky = new Person("Nakhimovsky","Alexander"); 

// create some Book objects 

  JavaNut = new Book("Java Foundation Classes in a  

Nutshell",731,29.95,DvdFlngn); 

  JSTDR = new Book("Javascript: The Definitive Guide (3rd 

Edition)",776,39.95,DvdFlngn); 

  JSBible = new Book("Javascript Bible, 4th  

Edition",1200,49.99,DnnyGdmn); 

  DHTMLTDR = new Book("Dynamic Html: The Definitive  

Reference",1073,44.95,DnnyGdmn); 

  JSObj = new Book("JavaScript 

Objects",450,39.99,TmMyrs,AlxNkmvsky); 

// create a Library object 

myLib = new Library(JavaNut,JSTDR,JSBible,DHTMLTDR);
```

糟糕，我们漏掉了 JavaScript Objects 这本书。我们最好补充一下:

```
myLib.addBook(JSObj);
```

现在，我们可以获得信息，例如我们图书馆的图书价格，一本书的平均价格，以及我们拥有的各种书籍的作者姓名。就是这样！我们已经用 JavaScript 完成了一个复杂的 OOP 例子。你可能想回顾一下你不理解的代码，或者在 SitePointForums.com[的](http://www.sitepointforums.com/)[客户端脚本](http://www.sitepointforums.com/forumdisplay.php?s=&forumid=15)论坛上提出问题。

##### 原型

每个对象构造函数都有一个特殊的属性`prototype`。此属性允许您向从该对象构造函数创建的所有对象添加属性/方法。听起来很困惑？不是的。让我们看一些例子:

```
function Square(){  

}  

var squareObj = new Square();  

Square.prototype.side = 5;  

var squareObj2 = new Square();  

alert(squareObj.side); // displays 5  

alert(squareObj2.side); // displays 5
```

这样做的是给所有的`Square`对象添加一个侧属性，初始值为 5，不管它们已经被创建，还是还没有被创建。在对象构造器做任何事情之前，`prototype`对象(实际上是一个对象)被加载。所以，这段代码:

```
function Square(){  

  this.side=5;  

}  

var squareObj = new Square();  

Square.prototype.side = 4;  

var squareObj2 = new Square();  

alert(squareObj.side); // displays 5  

alert(squareObj2.side); // displays 5
```

返回 5，因为首先加载`prototype`对象中的所有内容(甚至在`Square()`对象构造函数运行之前)，所以构造函数中定义的属性和方法将覆盖它。因此，使用`prototype`属性，您不能覆盖对象的构造函数(创建对象的函数)中定义的任何属性或方法。使用`String`的`prototype`属性，我们可以向`String`对象添加新方法。考虑这个例子:

```
function consonantize(){  

  var consonants ="";  

  for(i=0;i<this.length;i++){  

    var l = this.charAt(i);  

    if(l!="a" && l!="A" && l!="e" && l!="E" &&  

    l!="i" && l!="I" && l!="o" && l!="O" && l!="u" && l!="U" && l!="  

"){  

      consonants+=l;  

    }  

  }  

  return consonants;  

}
```

上面的函数遍历一个字符串，删除所有元音和空格，只返回辅音。现在，我们可以在任何`String`对象或任何`String`原始数据上使用它:

```
String.prototype.consonantize = consonantize;  

var dg = "Danny Goodman";  

var df = new String("David Flanagan");  

alert(dg.consonantize());  

alert(df.consonantize());
```

很整洁，是吧？注意这个新方法，就像其他的`String`方法一样，如何被一个`String`对象或者一个`String`原始数据类型使用。因此，通过使用对象构造器的`prototype`方法，我们可以向本地对象和用户定义的对象添加属性和方法。

##### 构造器

对象的每个实例都有一个构造函数属性。它返回创建该对象实例的`Function`对象。例如:

```
function myConstructor(){   

}   

var str = new String("Some String");   

var obj = new Object();   

var myObj = new myConstructor();   

alert(str.constructor); // the native String() constructor   

alert(String) // the native String() constructor   

alert(obj.constructor); // the native Object() constructor   

alert(Object) // the native Object() constructor   

alert(myObj.constructor); // the user-defined myConstructor() constructor   

alert(myConstructor); // the user-defined myConstructor() constructor
```

我建议您运行这个示例，看看它会返回什么。注意每个警告是如何返回创建该对象实例的 **`Function`对象**的。另外，请注意 JavaScript 的本机对象返回“[本机代码]”。当您检索一个构造函数属性的`typeof`时，您会发现它与创建它的`Function`对象的`function`相同:

```
alert(typeof str.constructor); // "function"   

alert(typeof String) // "function"   

alert(typeof obj.constructor); // "function"   

alert(typeof Object) // "function"   

alert(typeof myObj.constructor); // "function"   

alert(typeof myConstructor); // "function"
```

以上均返回“`function`”。因为构造函数属性返回对创建它的`Function`对象的引用，所以构造函数实际上是一个构造函数方法:

```
function myConstructor(){   

  var x = "y";   

  this.x = "x";   

  return x;   

}   

var myObj = new myConstructor();   

alert(myObj.constructor); // the myConstructor() function object   

alert(myObj.constructor()); // "y"
```

注意，在这个例子中，我们返回局部变量`x`，而不是对象的属性`this.x`。所以，如果每个对象都有一个构造函数方法，并且每个方法都是一个`Function`对象，那么`Function`对象的构造函数是什么呢？

```
alert(myConstructor.constructor);   

alert(myObj.constructor.constructor);   

alert(myConstructor.constructor.constructor);   

alert(myObj.constructor.constructor.constructor);
```

所有这些都返回本机`Function()`对象构造函数。虽然这很琐碎，但我个人认为这很有趣——我想你可能也是，这让我想到了另一点。构造函数既是“对象的类型”，也是对象本身(更具体地说，`Function`对象)。因此，日期既是一个对象(一个`Function`对象)，也是一种“对象类型”，从中可以创建`Date`对象或`Date`对象的实例。这适用于所有本机对象和用户定义的对象。

所有这些的实际价值在于，通过一个对象的构造函数方法，我们可以判断出它是什么类型的对象。我们可以看到它是否是一个从本机`String`构造函数创建的`String`对象；无论是从原生的`Object`构造函数创建的`Object`对象；或者它是否是一个用户定义的对象，由用户定义的构造函数创建。

除了是对象的方法之外，`constructor()`还是一个原始数据类型的方法。那么它返回什么呢？毕竟，没有运行真正的构造函数来创建原始数据类型:

```
var primitiveString1 = "This is a primitive string";   

var primitiveString2 = String("This is a primitive string");   

var stringObject = new String("This is a String object");   

primitiveString1.prop = "This is a property";    

primitiveString2.prop = "This is a property";   

stringObject.prop = "This is a property";   

alert(primitiveString1.prop) // "undefined"   

alert(primitiveString2.prop) // "undefined"   

alert(stringObject.prop) // "This is a property"   

alert(typeof primitiveString1); // "string"   

alert(typeof primitiveString2); // "string"   

alert(typeof stringObject) // "object"   

alert(primitiveString1.constructor); // "function String(){    

[native code] }"   

alert(primitiveString2.constructor); // "function String(){    

[native code] }"   

alert(stringObject.constructor); // "function String(){    

[native code] }"
```

正如我们所见，`String`原始数据类型和`String`对象都有相同的`constructor()`，本地`String()`构造函数。注意`constructor()`是原始数据类型拥有的唯一属性/方法，所以这些数据类型可以访问在本地对象构造函数中定义的属性/方法。例如，一个原始的`String`数据类型(以及一个`String`对象)可以访问本地`String()`构造函数中定义的许多属性/方法，包括:

*   长度
*   锚点()
*   大()
*   粗体()
*   夏拉特()
*   charCodeAt()
*   concat()
*   indexOf()
*   lastIndexOf()
*   sub()
*   substr()
*   子字符串()

然而，`String`对象也可能包含该对象特有的属性/方法。例如:

```
var myStringObj = new String("This is a String object");   

myStringObj.prop = "This is a property of the object I created";   

alert(myStringObj.prop) // "This is a property of the object I created"
```

正如[艾力克斯·文森特指出的](http://www.wsabstract.com/javatutors/proplit.shtml)，有时你会想把一个原始数据类型变成一个对象。例如，假设我们有这样一个函数:

```
function myFunc(param){   

  param.property = "I want to add this property";   

  alert(param.property); // "undefined"   

}
```

如果我们决定使用这个函数并给它传递一个原始数据类型，我们就不能给它添加属性，因为它不是一个对象。无论如何，传递一个对象是相当麻烦的:

```
myFunc(new String("This is a String object"));   

myFunc(new Number(5));
```

亚历克斯指出，克服这一点的一个方法如下:

```
function myFunc(param){   

  param = new param.constructor(param);   

  param.property = "I want to add this property";   

  alert(param.property); // returns "I want to add this property"   

}
```

那条新的线看起来令人困惑，但是让我们后退一步。假设我们想将一个原始数字转换成一个新的`Number`对象。我们可以使用:

```
var myNum = 5;   

myNum = new Number(5);
```

现在让我们更进一步:

```
var myNum = 5;   

myNum = new myNum.constructor(5);
```

你一定要记住`myNum.constructor()`和`Number()`是一样的。然后，我们可以用`myNum`代替 5，因为那也是 5:

```
var myNum = 5;   

myNum = new myNum.constructor(myNum);
```

这同样适用于字符串基本数据类型——就像它适用于所有基本数据类型一样。因此，当我们将任何原始数据类型作为参数传递给我们的函数时，我们会自动将其转换为对象，以便我们可以向其添加属性/方法。

##### 原型重访

让我们返回并重新访问`Function`对象的`prototype`属性。在 Java 中，一个流行的、众所周知的特性是扩展一个类；然而，在 JavaScript 中，大多数人并不知道您可以这样做——但是您可以！例如，假设我们有一个`Car`对象。一辆`Corvette`和一辆`Ares`是两种不同类型的车，但它们都是静止的车。这样，它们具有相似的属性/方法，并在`Car`对象上扩展。

让我们创建将要使用的三个对象—`Car, Corvette`和`Ares`。然后，我们将讨论后两者继承`Car`对象的属性/方法的方式。

```
function Car(color){    

  this.wheels = 4;    

  this.doors = 4;    

  this.color = color;    

  this.speed = 0;    

  this.accelerate = function(){    

    this.speed+=20;    

  }    

  this.brake = function(){    

    this.speed-=20;    

  }    

}    

function Corvette(color){    

  // all of Car properties/methods    

  this.doors = 2;    

  this.color = color;    

  this.accelerate = function(){    

    this.speed+=40;    

  }    

}    

function Ares(color){    

  // all of Car properties/methods    

  this.doors = 2;    

  this.color = color;    

  this.accelerate = function(){    

    this.speed+=10;    

  }    

  this.brake = function(){    

    this.speed-=10;    

  }    

}    

var myCar = new Car("white");    

var myCorvette = new Corvette("black");    

var myAres = new Ares("red");
```

因为克尔维特是一辆特别快的车，我们提高了它的加速速度，因为道奇战神是一辆摇摇晃晃的老爷车，我们把它做得刹车不好，加速不快(无意冒犯道奇战神的车主)。现在，我们可以使用`Corvette()`和`Ares()`原型属性，并向每个属性添加来自`Car`对象的我们希望它们继承的属性/方法。然而，这可能是一个令人困惑和乏味的任务，尤其是如果有许多属性/方法的话。为了克服这一点，我们需要再次检查`prototype`属性。

`prototype`属性是一个没有初始属性/方法的对象。当我们向该对象添加属性/方法时，我们会自动将它们添加到该对象的所有实例中。然而，我们可以用一个已经有了我们想要的属性/方法的对象来替换原型对象，而不是向`prototype`对象添加属性/方法。例如，不使用:

```
Corvette.prototype.wheels = 4;    

Corvette.prototype.speed = 0;    

Corvette.prototype.brake = function(){    

  this.speed-=20;    

}
```

我们可以更容易地使用:

```
Corvette.prototype = new Car();
```

我们可以对`Ares`对象做同样的事情:

```
Ares.prototype = new Car();
```

`Corvette`和`Ares`对象现在都拥有了`Car`的所有属性/方法，这些属性/方法可以被每个对象构造函数中定义的属性/方法覆盖。例如，在`Corvette`和`Ares`对象中，门属性被覆盖为`2`。总之，我们现在的代码看起来像:

```
function Car(color){    

  this.wheels = 4;    

  this.doors = 4;    

  this.color = color;    

  this.speed = 0;    

  this.accelerate = function(){    

    this.speed+=20;    

  }    

  this.brake = function(){    

    this.speed-=20;    

  }    

}    

function Corvette(color){    

  this.doors = 2;    

  this.color = color;    

  this.accelerate = function(){    

    this.speed+=40;    

  }    

}    

Corvette.prototype = new Car();    

function Ares(color){    

  this.doors = 2;    

  this.color = color;    

  this.accelerate = function(){    

    this.speed+=10;    

  }    

  this.brake = function(){    

    this.speed-=10;    

  }    

}    

Ares.prototype = new Car();    

var myCar = new Car("white");    

var myCorvette = new Corvette("black");    

var myAres = new Ares("red");
```

现在，从`Corvette`和`Ares`对象中，我们可以检索适当的属性并运行对应于这些对象的`accelerate()`和`brake()`方法。这样，在 JavaScript 中，对象继承并不难实现。

##### 总结

通过这篇教程，我希望你已经了解了 JavaScript 是如何运行的。此外，我希望您已经掌握了 OOP 的基础知识，并且理解了 JavaScript 作为一种基于对象的语言的强大功能。我建议你在 [SitePoint 论坛](http://www.sitepointforums.com/forumdisplay.php?s=&forumid=15)上发表你可能有的任何问题；然而，如果你似乎找不到你的 JavaScript 对象问题的答案，如果你发邮件到 arielladog@yahoo.com[给我，我会很乐意尝试一下](mailto:arielladog@yahoo.com)

已经有很多人帮我写过这个教程了。不过，我要特别感谢艾力克斯·文森特、杰森·戴维斯和贾里德，他们帮助我理解了 JavaScript 的对象能力的优点。

## 分享这篇文章