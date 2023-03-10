# iPhone 应用程序开发–内存管理

> 原文：<https://www.sitepoint.com/ios-application-development-memory-management/>

随着 iOS5 自动引用计数的出现，开发人员的内存管理变得更加简单和容易。在本教程中，我们将了解内存管理的基本概念以及 ARC 的重要性。

在 iOS5 之前，内存管理使用手动引用计数来执行。

*   如果您创建了一个对象，那么您就是该对象的所有者，当您不再需要该对象时，您有责任释放该对象的内存。
*   您还可以通过向对象发送“保留”消息来获得对象的所有权。保留的对象也应由其所有者释放。
*   可以通过向对象发送“释放”或“自动释放”消息来释放对象的内存。
*   如果我们想在将来回收对象的内存，可以发送自动释放消息。当对象被自动释放时，它会被添加到自动释放池中。当池被清空时，池中的对象将被释放。
*   使用引用计数来跟踪对象的引用或所有者的数量。
*   每当一个对象被初始化或被保留，它的引用计数就增加 1，当它被释放时就减少 1。
*   当对象的引用计数变为零时，对象的内存被回收。换句话说，它被取消分配。

样本 1:

查看以下示例中的引用计数:

```
NSString *str1 = [[NSString alloc] init]; //Reference count is 1

[str1 retain]; // Reference count increments to 2

[str1 release]; Reference count decrements to 1
[str1 release]; Reference count  becomes 0 and memory gets reclaimed
```

样本 2:

```
-(NSString *)sample{
    NSString *str = [[[NSString alloc] initWithString:@"Test"] autorelease];
    return str;
}
```

在示例 2 中，如果我们对`str`对象调用 release 方法，该对象将在返回之前被释放。这不符合该职能的目的。在这种情况下，我们将使用自动释放或便利方法。

方便的方法自己负责初始化和释放对象。下面的示例中的`stringWithString`是一种方便的方法。

```
-(NSString *)sample{
    NSString *str = [NSString stringWithString:@"Test"];
    return str;
}
```

当对象没有所有者时，它的 dealloc 方法被触发。因此，我们应该在我们创建的自定义类中实现 dealloc 方法，并清除它的所有资源。

在下面的示例中，所有属性变量都在 dealloc 方法中释放。

```
@interface Customclass : NSObject

@property (nonatomic,retain) NSString *str1;
@property (nonatomic,retain) NSString *str2;

@end

@implementation Customclass
@synthesize str1,str2;

-(void) dealloc
{
    [str1 release];
    [str2 release];
    [super dealloc];
}
@end
```

在手动内存管理中，如果在保留和释放调用之间有一个完美的平衡，就不会有问题。

否则，可能会出现以下威胁

*   如果您尝试使用较早发布的对象，或者发布消息的数量超过了对象的保留消息数，应用程序可能会崩溃。
*   如果释放消息少于保留消息，应用程序中可能会发生内存泄漏。

多亏了 iOS5，平衡释放和保留的噩梦被它的自动引用计数彻底解决了。

概念是相同的，但是编译器为开发人员完成了平衡发布和保留的乏味任务。

我们不应使用保留、释放或自动释放方法。为了让编译器知道何时释放或保留一个对象，我们应该在头文件或引用变量中使用带有属性的所有权限定符。

### 可变生存期限定符

`__strong`指针变量有助于保留对象，只要指针指向它。默认限定符。

```
NSArray * __strong arrSample;
```

`__weak`指针变量指定当没有对对象的强引用时，可以回收对象的内存。当没有对对象的强引用时，该对象被设置为 nil。

```
NSString * __weak strSample = [[NSString alloc] initWithString:@”hello”];
NSLog(@”%@”,strSample);
```

在上面的例子中，输出为零。这是因为弱变量`strSample`没有其他对它的强引用。

限定符类似于弱限定符，除了对象没有被设置为 nil，而是作为一个悬空指针。此限定符仅可用于支持 iOS4，其中弱限定符不可用。不要使用这个限定符，因为它会因为悬空指针而导致崩溃。

`__autoreleasing`用于作为引用传递的参数。

### 属性生存期限定符

除了`autoreleasing`之外，上述所有限定符都可以用于头文件中的属性。这将有助于编译器在 dealloc 方法中添加适当的释放调用。

下面给出了一些其他限定词。

属性不是线程安全的。`Atomic`是它的对立面。

`Assign`属性用于给变量赋值，因此用于原始数据类型。

当你觉得对象是可变的时，就使用属性。当您不希望该值被对象的其他所有者更改时，可以使用此选项。

强时间限定符和弱时间限定符有助于避免强引用或循环引用循环。在父子关系中，父母应该对孩子有强引用，而孩子应该有弱引用以避免强引用循环。

**ParentClass.h**

```
#import "ChildClass.h"
@interface ParentClass : NSObject<SampleDelegate>

@property(nonatomic,strong) ChildClass *child;

@end
```

**ParentClass.m**

```
@implementation ParentClass
@synthesize child;
-(void) sampleMethod
{
    child = [[ChildClass alloc] init];
    child.delegate = self;

}
```

**ChildClass.h**

```
@protocol SampleDelegate<NSObject>

@end

@interface ChildClass : NSObject

@property(nonatomic,weak) id<SampleDelegate> delegate;
@end
```

**ChildClass.m**

```
@implementation ChildClass
@synthesize delegate;

@end
```

委托模式是保留周期的一个很好的例子。`ParentClass`有一个对`ChildClass`对象的强引用。`ChildClass`的代表就是`ParentClass`本身。这导致了循环引用。如果`ChildClass`的委托是强的，那么父节点和子节点都不能被释放。这反过来会导致内存泄漏。因此委托的生存期限定符应该设置为弱。

尽管 ARC 有助于为您保留和释放对象，但在某些情况下，您应该将指针设置为 nil。如果不这样做，您的应用程序可能会耗尽内存。如果您一直使用对您创建的对象的强引用，ARC 将无法释放它们。所以总是试图理解对象的范围，如果需要的话，将它们设置为零。

借助 XCode 附带的 Instruments 工具，可以检测内存泄漏并优化性能。探索 Instruments 工具，在将应用程序提交到 Appstore 之前，不要忘记监控应用程序的性能。

## 结论

我希望您已经对 iOS 中的内存管理有所了解。管理记忆的专业知识只能通过经验获得。让你的探索从这里开始。

## 分享这篇文章