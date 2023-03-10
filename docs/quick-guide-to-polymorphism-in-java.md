# Java 多态性快速指南

> 原文：<https://www.sitepoint.com/quick-guide-to-polymorphism-in-java/>

多态性意味着“采取不同形式的能力”。当应用于 Java(和其他面向对象的编程语言)时，它描述了该语言通过一个接口处理各种类型和类的对象的能力。

多态性的一个重要例子是父类如何引用子类对象。当创建一个类来扩展另一个类时，就会出现多态性。例如，让我们考虑一个类`Animal`，让`Cat`成为`Animal`的子类。所以，任何猫**都是**动物。这里，`Cat`满足其自身类型`Cat`及其超类`Animal`的 IS-A 关系，因此是多态的。事实上，任何满足一个以上的对象都是——关系本质上是多态的。

子类[从它的超类(父类)继承](https://www.sitepoint.com/interface-and-inheritance-in-java-inheritance)属性和方法。当这个子类中定义的方法覆盖其父子类时，Java 的多态特性可以使它以不同的方式执行相同的动作(方法)。为了扩展我们的例子，如果方法`feedAnimal()`在`Cat`上被调用，它可以执行不同的动作，而不是在通用的`Animal`上被调用。

Java 中的多态性有两种类型:运行时多态性(动态绑定)和编译时多态性(静态绑定)。方法*重写*是动态多态的一个例子，而方法*重载*是静态多态的一个例子。

**注意:**说 Java 中的每个对象本质上都是多态的也是合法的，因为每个对象都通过了自身和`Object`类的 IS-A 测试。

## 动态多态性

假设一个子类覆盖了超类的一个特定方法。Java 的多态特性将使用 overriding 方法。假设我们创建了一个子类的对象，并将其分配给超类引用。现在，如果我们在超类引用上调用被覆盖的方法，那么该方法的子类版本将被调用。

看看下面的例子。

```
class Vehicle{
    public void move(){
    System.out.println(“Vehicles can move!!”);
    }
}

class MotorBike extends Vehicle{
    public void move(){
    System.out.println(“MotorBike can move and accelerate too!!”);
    }
}

class Test{
    public static void main(String[] args){
    Vehicle vh=new MotorBike();
    vh.move();    // prints MotorBike can move and accelerate too!!
    vh=new Vehicle();
    vh.move();    // prints Vehicles can move!!
    }
}
```

需要注意的是，在对`move()`的第一次调用中，引用类型是`Vehicle`，被引用的对象是`MotorBike`。因此，当调用`move()`时，Java 会一直等到运行时来确定引用实际指向的是哪个对象。在这种情况下，对象是类`MotorBike`。所以，将调用`MotorBike`类的`move()`方法。在对`move()`的第二次调用中，对象属于类`Vehicle`。所以会调用`Vehicle`的`move()`方法。

由于调用的方法是在运行时确定的，这被称为**动态绑定**或**后期绑定**。

## 静态多态性

在 Java 中，静态多态是通过方法重载实现的。方法重载意味着一个类中有几个方法具有相同的名称，但是参数的类型/顺序/数量不同。

在编译时，Java 通过检查方法签名知道调用哪个方法。所以，这叫做**编译时多态**或者**静态绑定**。从下面的例子中可以清楚地理解这个概念:

```
class DemoOverload{
    public int add(int x, int y){  //method 1
    return x+y;
    }

    public int add(int x, int y, int z){ //method 2
    return x+y+z;
    }

    public int add(double x, int y){ //method 3
    return (int)x+y;
    }

    public int add(int x, double y){ //method 4
    return x+(int)y;
    }
}

class Test{
    public static void main(String[] args){
    DemoOverload demo=new DemoOverload();
    System.out.println(demo.add(2,3));      //method 1 called
    System.out.println(demo.add(2,3,4));    //method 2 called
    System.out.println(demo.add(2,3.4));    //method 4 called
    System.out.println(demo.add(2.5,3));    //method 3 called
    }
}
```

在上面的例子中，有四个版本的`add`方法。第一种方法有两个参数，而第二种方法有三个参数。对于第三和第四种方法，参数的顺序有所改变。编译器查看方法签名，并决定在编译时为特定的方法调用调用哪个方法。

## 摘要

Java 中通过多个 IS-A 测试的对象本质上是多态的。Java 中的每个对象都至少通过两次 IS-A 测试:一次是针对其自身，一次是针对对象类。Java 中的静态多态是通过方法重载实现的。Java 中的动态多态性是通过方法覆盖来实现的。

## 分享这篇文章