# Sourcehunt: PHP7 的唯一替代品，Laravel，HPKP，等等

> 原文：<https://www.sitepoint.com/sourcehunt-php7-only-alternative-to-laravel-hpkp-and-more/>

又到了推广一些开源项目的时候了！这次我们有了 Laravel 的替代品，但是需要 PHP7(太棒了！)，一些真正关心请求验证和查询参数过滤的包，HPKP(人人安全升级！)，一个让你的对象更严格的包，最后是 Symfony 的制作人的一份厚礼！

* * *

![Sourcehunt logo](img/407bcf1bded70a8c020759619779150b.png)

* * *

## [巴拉圭/hpkp-builder](https://github.com/paragonie/hpkp-builder)【15】

这个库旨在使在 PHP 项目中构建 HTTP 公钥锁定头变得容易，并且至少需要 PHP 7。

HTTP 公钥锁定或 HPKP 是一种通过 HTTP 响应报头传递的安全策略，很像 HSTS 和 CSP。它允许主机向用户代理提供关于将来应该从主机接受哪些加密身份的信息。这可以保护主机网站免受证书颁发机构的安全威胁，该机构可能会为您的主机名颁发恶意证书。

点击阅读更多关于 HPKP 的信息。

* * *

## [Rican 7/传入](https://github.com/Rican7/Incoming) [137 ★]

Incoming 是一个 PHP 库，旨在简化和抽象松散、复杂的输入数据到一致、强类型数据结构的转换。

```
// Create our incoming processor
$incoming = new Incoming\Processor();

// Process our raw form/request input into a User model
$user = $incoming->process(
    $_POST,            // Our HTTP form-data array
    new User(),        // Our model to hydrate
    new UserHydrator() // The hydrator above
); 
```

解释它的任何细节超出了这篇短文的范围，但本质上它允许我们精确地定义什么样的输入信息通过和水合物我们的模型，拒绝，过滤，或转换其他一切。

就像分形，反过来。(分形确保输出匹配设定的结构，而不是输入)

该库目前有一个突出的问题——它是围绕一个特性的讨论——但肯定需要一些用户和反馈！甚至可能是一个关于它的 SitePoint 帖子？

* * *

## [linito/input](https://github.com/LinioIT/input)【18】

还有另一种输入过滤机制，Linio 框架的这个组件(？！)旨在抽象 HTTP 请求输入处理，允许与您的域模型无缝集成。该组件负责:

*   解析请求正文内容
*   验证输入数据
*   将输入数据合并到对象中

所以，在某种程度上，就像上面传入的一样。

您使用输入处理程序来指定您希望从请求中得到哪些数据:

```
class RegistrationHandler extends InputHandler
{
    public function define()
    {
        $this->add('referrer', 'string');
        $this->add('registration_date', 'datetime');

        $user = $this->add('user', 'Linio\Model\User');
        $user->add('name', 'string');
        $user->add('email', 'string');
        $user->add('age', 'integer');
    }
} 
```

然后在控制器中引用它:

```
class RegistrationController
{
    public function registerAction(Request $request): Response
    {
        $input = new RegistrationHandler();
        $input->bind($request->request->all());

        if (!$input->isValid()) {
            return new Response($input->getErrorsAsString());
        }

        $data = $input->getData();
        $data['referrer']; // string
        $data['registration_date']; // \DateTime
        $data['user']; // Linio\Model\User

        return new Response(['message' => 'Valid!']);
    }
} 
```

该库还支持类型、约束、转换器等等——你可以在[文档](https://github.com/LinioIT/input)中阅读所有相关内容。

* * *

## [mpscholten/请求解析器](https://github.com/mpscholten/request-parser) [18 ★]

类似地，请求解析器做一些更轻量级的事情。

```
public function index()
{
    $page = $this->queryParameter('page')->int()->required();
    $order = $this->queryParameter('order')->oneOf(['asc', 'desc'])->required();
    $createdAt = $this->queryParameter('createdAt')->dateTime()->defaultsTo(null);
} 
```

简单吧？直接在您的当前请求上定义必需的和可选的参数以及类型。当然，还有更多，但这是它的要点。

只有 18 颗星，这个库肯定需要一些关注——无论是在用户方面，还是在贡献者/测试者方面。

* * *

## 富裕 PHP/富裕

富裕可能是 Laravel 一直在等待的竞争。这是一个全栈框架，最低要求是 PHP 7.0。正如医生所说:

> Opulence 是一个 PHP web 应用程序框架，它简化了创建和维护一个安全的、可伸缩的网站的困难部分。有了 population，像数据库管理、缓存、ORM、页面模板和路由这样的事情就不在话下了。它是在考虑定制、性能和最佳实践的情况下编写的。多亏了测试驱动开发(TDD ),这个框架是可靠的，并且经过了彻底的测试。Opulence 被拆分成多个组件，这些组件可以单独安装，也可以捆绑在一起安装。

像 Laravel 一样，它到处都有自己的组件，它的作者决心保持它的前沿。

前几天，富裕在 Reddit 上爆红，收集了普遍好评，所以明星数量不像我们在这篇文章中提到的其他项目那样低，但如果我们想给它提供所需的惯性，让它咬拉韦尔的脚跟，它仍然可以使用贡献者。

* * *

## [海山/耶](https://github.com/zeeshanu/yell)【7∝)

Yell 是一个 PHP 包，当你试图访问或设置一些未定义的属性时，它使你的对象变得严格并抛出异常。

```
use Zeeshanu\Yell\Scream;

class Person
{
    use Scream;

    public $name;
    public $age;
}

$person = new Person();

$person->name = 'John Doe';
$person->age = 23;

// An exception will be thrown when showing message "Trying to set undefined property $profession in class Person" 
$person->profession = 'Teacher'; 
```

* * *

## sensio labs-de/deptrac[355★]

Deptrac 是 Sensiolabs (Symfony)的新工具，是一个静态代码分析工具，有助于实施软件层之间依赖关系的规则。这具体是什么意思？例如，您可以定义一个类似“控制器可能不依赖于模型”的规则。为了确保这一点，deptrac 会分析您的代码，以找到控制器中模型的任何用法，并向您显示在哪里违反了这条规则。

作为介绍，这是他们准备的一个方便的视频:

[https://www.youtube.com/embed/T5oWc4ujmOo](https://www.youtube.com/embed/T5oWc4ujmOo)

不可否认，这个包比其他的少一点独立，但似乎足够有趣，值得推广——我们肯定希望在教程中看到它的一些用例。介意写一些吗？

* * *

六月到此为止——一如既往，请用标签 [#sourcehunt](https://twitter.com/search?q=#sourcehunt) 和 [#php](https://twitter.com/search?q=#php) 把你的链接扔给我们——这是组合的[链接。当然，如果你想寻找用另一种语言编写的项目，就要做相应的改变。](https://twitter.com/search?q=#sourcehunt%20AND%20#php)

编码快乐！

## 分享这篇文章