# 快速提示:雄辩观察者的便利魔法

> 原文：<https://www.sitepoint.com/quick-tip-the-convenient-magic-of-eloquent-observers/>

如果您以前在中型到大型项目中使用过口才，您可能会遇到这样的情况:当您的模型发生问题时，您想要采取行动。雄辩术提供了一种方便的方法。

![Laravel Logo](img/b24fd9db18c1157e55823b1b5d7e1e96.png)

## 观察者模式

> 观察者模式是一种软件设计模式，在这种模式中，一个被称为主体的对象维护着一个被称为观察者的依赖者列表，并自动通知它们任何状态变化，通常是通过调用它们的方法之一。–维基百科

在我们的例子中，雄辩的模型可以通知我们关于给定模型的变化。

## 模型事件

雄辩提供了一些有用的事件给*监控*模型状态:`creating`、`created`、`updating`、`updated`、`deleting`、`deleted`、`saving`、`saved`、`restoring`、`restored`。

注意“ing/ed”的区别。

*   `creating`:保存新成员前调用。
*   `created`:保存成员后调用。

雄辩术也引发了我们可以*监听*的类似事件。下面的例子将一个监听器附加到`Member`模型上的`creating`事件。

```
Event::listen("eloquent.created: App\\Member", function(Member $member) {
    // do something
}); 
```

## 创建观察者

让我们从在`App\Observers`名称空间下创建一个新类开始，并开始定义我们的方法。

```
// app\Observers\MemberObserver.php

namespace App\Observers;

use App\Member;

class MemberObserver
{
    public function deleting(Member $member) {
        // do something
    }
} 
```

我们可以使用事件名作为每个事件的方法名。我们不必定义所有的方法，只需定义我们想要使用的方法。

每个成员都可以订阅多个服务，并且每个服务都包含许多成员。假设我们没有为关联的`members_services`表设置级联删除，我们需要在成员删除时删除关联的服务，以避免在访问服务的订阅成员时出错。

```
// app\Observers\MemberObserver.php

namespace App\Observers;

use App\Member;

class MemberObserver
{
    public function deleting(Member $member) {
        $member->services()->delete();
    }
} 
```

现在，最后一件事是将这个观测器附加到适当的模型上。我们可以在任何我们想要的地方这样做，但是实际的地方是把它放在 boot 方法中的`app\Providers\AppProvider.php`文件中。

```
class AppServiceProvider extends ServiceProvider
{
    /**
     * Bootstrap any application services.
     *
     * @return void
     */
    public function boot()
    {
        Member::observe("App\\Observers\\MemberObserver");
    }
} 
```

我知道级联删除示例很简单，可以在控制器中完成，也可以直接通过 MySQL 完成，但这只是一个概念证明。

雄辩的观察者的好处是，我们可以通过从回调方法返回一个 false 值来中止当前的操作:

```
class MemberObserver
{
    public function deleting(Member $member) {
        $member->deleted_at = Carbon::now();
        $member->save();

        return false;
    }
} 
```

在上面的例子中，我们软删除成员并返回`false`来中止实际的删除操作。

* * *

口才有很多隐藏的特点，这是其中之一。你会看到这在大型应用程序和 CMSes 中大量使用。如果你对口才有任何疑问或评论，一定要在下面贴出来！

## 分享这篇文章