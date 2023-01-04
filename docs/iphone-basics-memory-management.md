# iPhone 基础知识:内存管理

> 原文：<https://www.sitepoint.com/iphone-basics-memory-management/>

新 iPhone 开发者最大的困惑之一是内存管理。如果您习惯于使用方便的、垃圾收集的脚本语言，您可能想知道为什么内存管理甚至是必要的。尽管 Cocoa 确实支持垃圾收集，但对于电话平台来说，这被认为是一个太大的开销。因此，对于开发人员来说，知道如何正确地分配和释放对象是很重要的。通过遵循一些简单的准则，您可以避免大多数内存管理问题。 **1。如果你分配了一个对象，你应该在完成后释放它。**当您分配一个对象时，您就“拥有”了该对象，并且当您用完它时，您有责任释放它:

```
Widget* widget = [[Widget alloc] init];// Do something with widget...[widget release];
```

你可以认为`release`是在说*放弃这个物体*的所有权，而不是*销毁这个物体*。在幕后，Cocoa 执行引用计数来跟踪所有权。如果没有人声明所有权，对象将被销毁，它所占用的内存将被回收。 **2。如果你没有直接分配一个对象，你应该*而不是*释放它。一旦你掌握了`release`的窍门，你就会很容易在不应该的时候释放对象。一个常见的例子是当您使用工厂方法时:**

```
NSString* label = [NSString stringWithFormat:@"Title: %@", widget.title];// Do something with label[label release] // Whoops, incorrect.
```

在这个例子中，创建了一个新的 string 对象，但是我们没有分配它，也不拥有它。因此，我们不能在这里调用`release`——这样做会导致过度释放，这会使你的应用崩溃。

**tip:** How is this object released?

在后台，`NSString`在返回字符串之前已经调用了字符串上的`autorelease`,稍后我们将会看到这一点。

**3。如果你需要一个对象，你应该`retain`它(当你完成时`release`它)。**当我们收到别人创建的对象时，这条规则适用。例如，考虑一个 setter 方法:

```
- (void)setName:(NSString*)newName;{    [newName retain];   // we need the new string object.    [name release];     // we don't need the old string any more.    name = newName;}
```

`retain`表示我们对该物体拥有所有权；原主人释放时不会销毁。但是，现在我们拥有了对象，当我们不再需要它时，我们必须释放它。在后台，`retain`和`release`更新对象的引用计数，分别递增和递减。遵循这种模式可以大大简化内存管理。当将一个对象传递给一个方法时，我们可以假设它将被保留(如果需要的话)，然后我们可以立即释放它:

```
UIButton* button = [[UIButton alloc] initWithFrame:myFrame];[self.view addSubview:button]    // Here we can assume that view                                 // has retained button,[button release]                 // we can safely release it.
```

另一个常见的例子出现在我们使用集合对象时，集合对象保留了添加到其中的对象:

```
NSMutableArray* widgetList = [[NSMutableArray alloc] init];// ...Widget* widget = [[Widget alloc] init];[widgetList addObject:widget];           // retains widget[widget release];                        // we can release widget//...[widgetList release] // objects in collection will be released.
```

**4。如果你创建了一个对象并且没有保留对它的控制，使用`autorelease`** 。假设您想为小部件创建自己的工厂方法。这里，我们创建一个新的小部件对象，并从一个类方法中返回它:

```
+ (Widget*)createBetterWidget{    Widget* widget = [[Widget alloc] init];    [widget setAwesome:11];    return [widget autorelease];}
```

我们仍然负责释放对象，所以我们在返回它之前调用`autorelease`。`autorelease`表示对象应该“最终”被释放对象被添加到在应用程序运行循环结束时被清除的池中。作为内存管理的解决方案，自动释放所有内容似乎很诱人，但不建议这样做，因为在自动释放池耗尽之前，您的应用程序可能会耗尽内存。最好的方法是采用简单、明确的方法来分配和释放对象。遵循这些简单的规则，你将在创建无 bug 且高效的代码的道路上一帆风顺。对于这些主题的深入报道，[苹果内存管理编程指南](http://developer.apple.com/mac/library/documentation/cocoa/conceptual/MemoryMgmt/MemoryMgmt.html)是新开发人员的必备读物。图片来源: [Dezeen](http://www.dezeen.com/category/events/dmy-berlin-09/)

## 分享这篇文章