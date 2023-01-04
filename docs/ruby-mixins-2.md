# Ruby Mixins

> 原文：<https://www.sitepoint.com/ruby-mixins-2/>

你有没有想过将功能“混合”到你的类中意味着什么？你见过 Ruby 代码中使用的`include`和`extend`并想知道是怎么回事吗？如果是这样，那么你来对地方了。Mixins 可以为您的代码提供很大的灵活性，到本文结束时，您将知道如何使用 Ruby 来实现这一点。

## 什么是 Mixin？

一个 [mixin](http://en.wikipedia.org/wiki/Mixin) 基本上可以被认为是一组代码，它们可以被添加到一个或多个类中，从而在不使用继承的情况下增加额外的功能。在 Ruby 中，mixin 是封装在一个模块中的代码，一个类可以包含或扩展它(后面会详细介绍)。事实上，一个类可以有很多 mixins。

## 遗传呢？

我喜欢使用 mixins 的一个领域是日志记录。在我的 Rails 应用程序中，我希望每个模型都可以访问日志记录器，而不需要使用某种全局常量。最初，我考虑使用某种基本模型类(可以从 ActiveRecord::Base 继承)来提供对记录器的访问。然而，我真的不喜欢我的所有模型都必须扩展一个特殊的类来获得一个日志记录器的想法。是的，他们已经扩展了 ActiveRecord::Base，所以有什么区别吗？

那么，如果我想在我的应用程序的其他部分添加对记录器的访问，而这些部分不是从 ActiveRecord::Base(或一个特殊的子类)继承的，该怎么办呢？Ruby 只支持单一继承，所以 mixins 似乎是最好的解决方案。

## 包含与扩展

模块可以被包含或扩展，这取决于方法是分别作为实例方法还是类方法添加。

```
module Logging
        def logger
            @logger ||= Logger.new(opts)
        end
    end

    class Person
        include Logging

        def relocate
            logger.debug "Relocating person..."
            # do relocation stuff
        end
    end
```

在上面的例子中，定义了一个`Logging`模块，它通过`logger`方法为一个`Logger`对象提供了一个实例。`Person`类包含了`Logging`模块，该模块会将其方法作为实例方法添加到`Person`类中。值得注意的是，模块中定义的所有东西都被添加到类中，包括方法(公共的、受保护的、私有的)、属性(例如`attr_accessor :name`)和常量。

相反，如果我想通过一个类方法提供一个日志记录器，我会使用如下所示的`extend`方法。

```
module Logging
        def logger
            @@logger ||= Logger.new(opts)
        end
    end

    class Person
        extend Logging

        def relocate
            Person.logger.debug "Relocating person..."

            # could also access it with this
            self.class.logger.debug "Relocating person..."
        end
    end
```

最后两个代码片段有三个不同之处。在后者中，您可以看到“logger”方法创建了一个类变量`@@logger`，而不是一个实例变量。我这样做是因为我希望 Logger 被扩展到其他类中，而不是被包含在内。当然，我也使用了`extend`方法，而不是`include`。最后，对`logger`方法的访问是通过类而不是实例来完成的，比如`Person.logger.debug`。

还可以在运行时将一个模块混合到一个实例中，如下例所示。

```
module Logger
        def logger
            @logger ||= Logger.new(opts)
        end
    end

    class Person; end

    p = Person.new
    # p.logger -- this would throw a NoMethodError
    p.extend Logger
    p.logger.debug "just a test"
```

## 包含与扩展

在某些情况下，您可能需要知道您的模块何时被包含或扩展。一个用例可能是帮助加强可以包含您的模块的类的类型。例如，在 Rails 应用程序中，我有一个模块可以检查我的模型是否存在表。然而，我想确保我的模块只包含在继承自`ActiveRecord::Base`的类中，因为模块调用了`ActiveRecord::Base#table_exists?`方法。

```
module VerifyTableExistance
        def self.included(base)
            raise "#{self.name} can only be included into classes that inherit from ActiveRecord::Base, #{base.name} does not." unless (base &lt; ActiveRecord::Base)
            raise "#{base.name} does not have a table yet" unless base.table_exists?
        end
    end

    class Person &lt; ActiveRecord::Base
        include VerifyTableExistance
    end
```

`VerifyTableExistance`模块实现了`included`类方法，每当该模块被包含到另一个模块或类中时，就会调用该方法。这允许我验证包含该模块的类是一种`ActiveRecord::Base`，并且在这个例子中，验证该模型存在一个表。

另一种常见的模式是定义一个混合实例和类方法的模块。

```
module AcmeModel
        def self.included(base)
            base.extend(ClassMethods)
        end

        def brand
            "acme"
        end

        module ClassMethods
            def all
                # get all of the AcmeModel instances
                []
            end
        end
    end

    class Widget
        include AcmeModel
    end

    w = Widget.new
    w.brand	# "acme" is the output, 
    Widget.all	# invoke the class method that was added
```

在这个例子中，`AcmeModel`提供了一个名为`brand`的实例方法。更重要的是，它覆盖了由[模块](http://www.ruby-doc.org/core/classes/Module.html#M000458)类提供的`included`类方法。`included`方法是一个回调函数，每当模块被另一个类或模块包含时就会被调用。在这一点上，我们用`ClassMethods`模块扩展了包含`AcmeModel`的类，在这个例子中是`Widget`类。

最后，您还应该知道您的模块可以覆盖`extended`类方法，该方法在模块扩展时充当回调。

## 结论

希望您已经快速了解了什么是 mixins 以及如何在 Ruby 中使用它们。如果没有，或者你觉得我遗漏了什么，请留下评论。这是我的第一篇文章，我希望有更多。另外，如果你希望看到 Ruby 的某个特定领域被讨论，那么就留下你的评论吧。

## 分享这篇文章