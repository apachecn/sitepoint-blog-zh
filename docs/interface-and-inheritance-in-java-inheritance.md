# Java 中的接口和继承:继承

> 原文：<https://www.sitepoint.com/interface-and-inheritance-in-java-inheritance/>

当一个类扩展另一个类时，这叫做继承。扩展的类称为子类，而被扩展的类称为超类。java 中任何不扩展任何其他类的类都隐式扩展了**对象**类。

换句话说，java 中的每个类都直接或间接地继承了 Object 类。

通过继承，一个类可以获得超类的所有公共的、受保护的属性和方法，不管子类出现在哪个包中。如果子类和超类存在于同一个包中，那么它也会获得包的私有属性和方法。一旦子类继承了超类的属性和方法，它就可以像定义它们一样对待它们。

通过使用继承，您可以重用现有的代码。如果你有一个已经写好的类(但是没有源代码),并且它缺少一些特性，那么你不必从头开始写所有的东西。只需扩展该类并添加一个满足您需求的新方法。

**注**:

私有方法和属性不被继承。但是，如果超类有一个私有变量和一个使用该变量的公共方法，那么该变量在子类的方法中是可用的。您还应该注意到，构造函数永远不会被继承。

## 如何延伸

使用**扩展**关键字来继承超类-

```
class A{

//properties and methods of A

}

class B extends A {

}
```

**注意:**一个类只能继承一个类。Java 不支持多重继承。

## 方法重写和隐藏

当一个子类定义了一个具有相同签名和返回类型(或者与超类方法的返回类型兼容)的方法时，它被称为**方法覆盖**。

示例:

```
class A{

int x;

public void printIt(){

System.out.println(“method in class A”);

}

}

class B extends A{

public void printIt(){

System.out.println(“method in class B”);

}

}

class Test{

public static void main(String[] args){

A a=new B();

a.printIt(); // prints “method in class B” without quotes

}

}
```

这里，类 B 扩展了类 A 并覆盖了方法`printIt()`。所以，当我们运行上面的代码时，被调用的方法是类 B 的`printIt()`，通过覆盖类 A 中定义的`printIt()`，类 B 可以为它提供一个不同的实现。它仍然可以通过使用 **super** 关键字来访问该方法的超类版本。

示例:

```
class B extends A{

public void printIt(){

super.printIt();

System.out.println(“method in class B”);

}

}
```

**注:**

在前面的例子中，我们使用了类型 A 的引用和 b 的对象。但是在编译时并没有决定调用谁的`printIt()`方法。Java 一直等到程序运行时，检查引用指向哪个对象。在这种情况下，对象属于 B 类。因此，执行的是 B 的`printIt()`方法。这被称为**动态方法分派**或**虚拟方法调用。**

现在让我们假设类 A 有一个静态方法`printStatic`，类 B 是 A 的子类，它定义了一个静态方法，与 A 的`printStatic`具有相同的签名。这是一个**方法隐藏**的案例。

示例:

```
class A{

public static void printStatic(){

System.out.println("In A");

}}

class B extends A{

public static void printStatic(){

System.out.println("In B");

}

}

class Test{

public static void main(String[] args){

A a=new B();

a.printStatic(); // prints “In A” without quotes

//We can also call like this A.printStatic()

}

}
```

在这种情况下，编译时 Java 会寻找引用类型，而不是被指向的对象。这里引用类型是 A，所以执行 A 类的`printStatic()`。

## 铸造物体

举个例子，A 类是超类，B 类是子类。我们已经知道，创建一个 B 类的对象并把它赋给 A 类的引用是可能的，但是通过这样做，你将只能调用在 A 类中定义的方法。为了调用由 B 类定义的方法，你需要转换引用。

示例:

```
A a=new B();

a.methodSpecificToB(); // illegal

B b=(B)a;

b.methodSpecificToB(); //legal
```

## 构造函数链接

当我们实例化一个子类时，超类构造函数也会运行。这是通过调用`super()`来实现的。当我们没有在子类构造函数中显式调用`super()`时，编译器会隐式地将`super()`作为每个重载构造函数中的第一条语句(像方法一样，构造函数也可以被重载),假设对`this()`的调用不在构造函数中。

如果你没有为你的类定义一个构造函数，编译器会创建一个默认的构造函数(没有参数)并调用`super()`。唯一的条件是你的超类应该有一个无参数的构造函数，否则会产生编译时错误。如果你有一个没有无参数构造函数的超类，那么你应该从你的子类构造函数中用合适的参数调用`super()`。

从下面的例子中可以清楚地理解这个概念:

```
class A{

A(){

System.out.println(“Constructor of A running”);

}

}

class B extends A{

B(){

//a call to super() is placed here

System.out.println(“Constructor of B running”);

}

}

public class Test{

public static void main(String[] args){

new B();

}

}
```

## 输出

运行的构造函数

B 运行的构造函数

**注意:**首先调用子类构造函数，但首先完成执行的是超类构造函数。

## 摘要

*   一个类只能扩展一个类。
*   *每个类都是对象的直接或间接子类。*
*   超类引用可以引用子类对象。
*   如果引用是超类，对象是子类，那么调用它的实例方法将导致子类中定义的方法的执行。
*   被覆盖的方法与超类方法具有相同的签名和返回类型(或与返回类型兼容)。
*   通过定义一个与超类方法具有相同签名或返回类型(或与返回类型兼容)的静态方法，我们可以隐藏在超类中定义的静态方法。
*   当子类构造函数运行时，超类构造函数也运行。这被称为构造函数链接。

## 分享这篇文章