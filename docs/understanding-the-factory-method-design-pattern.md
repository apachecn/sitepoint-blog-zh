# 理解工厂方法设计模式

> 原文：<https://www.sitepoint.com/understanding-the-factory-method-design-pattern/>

当您无法预测需要创建什么类型的对象或如何创建它们时，构建东西会很困难。以一家生产大量产品的工厂为例。每个产品都可以由库存中任意数量的组件组成。工人们知道库存里有什么，但不一定事先知道他们将生产什么样的产品。工厂方法设计模式可以应用于编程中的类似情况，您有一组组件类，但直到运行时才确切知道需要实例化哪一个。在本文中，我将向您展示如何使用工厂方法模式来创建不同的对象，而不需要预先知道它需要创建什么类型的对象或者对象是如何创建的。

## 工厂方法

工厂方法模式是一种设计模式，用于定义创建对象的运行时接口。它被称为*工厂*,因为它创建各种类型的对象，而不一定知道它创建什么类型的对象或者如何创建它。

这里有一个工厂模式如何工作的例子。假设您有一个`ProductFactory`类，它创建了一种新的产品类型:

```
<?php
class ProductFactory
{
    public static function build($type) {
        // assumes the use of an autoloader
        $product = "Product_" . $type;
        if (class_exists($product)) {
            return new $product();
        }
        else {
            throw new Exception("Invalid product type given.");
        }
    } 
}
```

通过将`build()`定义为工厂方法，您现在有了一个接口，通过它您可以动态地创建不同的产品。

```
<?php
// build a new Product_Computer type
$myComputer = ProductFactory::build("Computer");
// build a new Product_Tablet type
$myTablet = ProductFactory::build("Tablet");
```

工厂方法模式通常用于以下情况:

*   一个类不能预先预测它需要创建的对象的类型。
*   一个类需要它的子类来指定它创建的对象。
*   您希望本地化逻辑来实例化复杂对象。

当您需要将对象的创建从其实际实现中抽象出来时，工厂方法模式非常有用。假设工厂将生产一种“移动设备”产品。移动设备可以由任意数量的组件组成，根据技术的进步，其中一些组件可能会发生变化。

```
<?php
class Product_MobileDevice
{
    private $components;

    public function __construct() {
        // this device uses a 7" LCD
        $this->addComponent(ProductFactory::build("LCD", 7));
        // and features an 1GHz ARM processor  
        $this->addComponent(ProductFactory::build("CPU_ARM", 1));
    }
...
}

// build a new Product_MobileDevice type
$myDevice = ProductFactory::build("MobileDevice");
$myDevice->use();
```

创建一个`Product_MobileDevice`对象的逻辑已经被封装到类本身中。如果以后您想将 7 英寸的 LCD 屏幕换成 10 英寸的 Touch_Screen，您可以在 MobileDevice 类中进行单独的更改，而不会影响应用程序的其余部分。

## 使用其他工厂的工厂

因为对象的实例化是封装的，所以它本身也可以使用工厂。为了进一步扩展抽象对象创建的思想，让我们使用一个非软件工程的类比。汽车制造厂生产特定品牌、型号和颜色的汽车，但它可能不生产制造汽车所需的所有必要零件。换句话说，它将这些零件的生产委托给其他工厂，然后用它们来制造新的汽车。

在这种情况下，车辆工厂可能如下所示:

```
<?php
class VehicleFactory
{
    public static function build($make, $model, $color) {
        $vehicle = new Vehicle;

        // vehicle needs a chassis which is produced by another factory
        $vehicle->addPart(VehicleChassisFactory::build($make, $model));
        // needs an engine built by someone else
        $vehicle->addPart(VehicleEngineFactory::build($make, $model));
        // it needs a bodykit made by another factory
        $vehicle->addPart(VehicleBodyFactory::build($make, $model, $color));
        // and interiors are made by... you guessed it, someone else 
        $vehicle->addPart(VehicleInteriorFactory::build($make, $model, $color));

        // ...add more parts

        return $vehicle;
    }
}

// build a new white VW Amarok
$myCar = VehicleFactory::build("Volkswagon", "Amarok", "white");
$myCar->drive();
```

瞧啊。一辆闪亮的新车。`VehicleFactory`级生产特定品牌、型号和颜色的车辆，但获得通过其他工厂方法生产的各种零件。

## 摘要

在本文中，您了解了如何使用工厂方法模式来本地化不同对象的构造，并允许您在事先不知道具体需要什么类型的情况下创建对象。您还看到了工厂方法可以使用其他工厂方法来创建对象对象并定义它们生成的对象。

<small>图片 via [俞明林](http://www.shutterstock.com/gallery-650362p1.html)/[Shutterstock](http://shutterstock.com)</small>

## 分享这篇文章