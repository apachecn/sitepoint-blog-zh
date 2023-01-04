# 构建 Drupal 8 模块——配置和服务容器

> 原文：<https://www.sitepoint.com/building-drupal-8-module-configuration-management-service-container/>

请注意，由于撰写本文时 Drupal 8 正在经历开发过程，部分代码可能已经过时。看一下[这个库](https://github.com/upchuk/d8-demo-modules)，我试图在其中更新示例代码，并使其与最新的 Drupal 8 版本兼容。

在关于 Drupal 8 模块开发的[上一篇文章中，我们已经看到了创建块类型和表单。我们已经看到了块现在是可重用的，以及我们定义块类型所需的一切是如何在一个类中发生的。类似地，表单生成函数也归入一个类，用特定的方法执行类似于我们在 Drupal 7 中所习惯的任务。](https://www.sitepoint.com/building-drupal-8-module-blocks-forms/)

在本教程中，我将从我们停止的地方继续。我将说明如何通过 Drupal 8 配置系统将我们的`DemoForm`转换成用于存储值的形式。接下来，我们将通过举例的方式谈一谈服务容器和依赖注入。

不要忘记，如果你想获得我们在本教程系列中编写的所有代码，你可以查看这个库。

# 配置表单

当我们第一次定义我们的`DemoForm`时，我们扩展了[表单库](https://api.drupal.org/api/drupal/core%21lib%21Drupal%21Core%21Form%21FormBase.php/class/FormBase/8)类，这是界面的[的最简单实现。然而，Drupal 8 还附带了一个](https://api.drupal.org/api/drupal/core%21lib%21Drupal%21Core%21Form%21FormInterface.php/interface/FormInterface/8) [ConfigFormBase](https://api.drupal.org/api/drupal/core%21lib%21Drupal%21Core%21Form%21ConfigFormBase.php/class/ConfigFormBase/8) ，它提供了一些额外的功能，使得与配置系统的交互变得非常容易。

我们现在要做的是将`DemoForm`转换成一个用来存储用户输入的电子邮件地址的文件。我们应该做的第一件事是用`ConfigFormBase`替换扩展类(当然*使用*它):

```
use Drupal\Core\Form\ConfigFormBase;

class DemoForm extends ConfigFormBase {
```

在我们继续修改表单中的其他内容之前，让我们先了解一下 Drupal 8 中的简单配置是如何工作的。我说*简单*是因为还有更复杂的配置实体，我们今天不讨论。现在，模块(核心或贡献)提供的配置存储在 YAML 文件中。在启用一个模块时，这些数据被导入到数据库中(为了在使用它时获得更好的性能)。通过 UI，我们可以更改此配置，然后可以轻松地导出到 YAML 文件，以便跨不同站点进行部署。

模块可以在位于模块根目录下的`config/install`文件夹中的 YAML 文件中提供默认配置。命名这个文件的惯例是在它前面加上模块名。所以让我们创建一个叫做`demo.settings.yml`的。在这个文件中，让我们粘贴以下内容:

```
demo:
  email_address: demo@demo.com
```

这是一个嵌套结构(就像 PHP 中的关联数组)。在键`demo`下，我们有另一个键|值对。通常我们使用一个点(`.`)来访问这些嵌套的值。在我们的情况下`demo.email_address`。

一旦我们有了这个文件，你需要记住的一件重要的事情是，这个文件只有在模块被安装的时候才会被导入。所以继续重新安装吧。现在我们可以返回到我们的表单，逐一查看需要修改的方法。

现在,`buildForm()`方法应该是这样的:

```
public function buildForm(array $form, array &$form_state) {

  $form = parent::buildForm($form, $form_state);

  $config = $this->config('demo.settings');

  $form['email'] = array(
    '#type' => 'email',
    '#title' => $this->t('Your .com email address.'),
    '#default_value' => $config->get('demo.email_address')
  );

  return $form;
}
```

首先，与`FormBase`相反，`ConfigFormBase`类也实现了这个方法，以便向表单数组添加元素(一个提交按钮)。所以我们可以在添加自己的元素之前使用父元素。

现在是配置部分。Drupal 8 提供了一个[配置](https://api.drupal.org/api/drupal/core%21lib%21Drupal%21Core%21Config%21Config.php/class/Config/8)对象，我们可以用它来与配置交互。一些类已经通过依赖注入提供了它。`ConfigFormBase`就是这样一个类。

如您所见，我们使用父类的 [config()](https://api.drupal.org/api/drupal/core%21lib%21Drupal%21Core%21Form%21ConfigFormBase.php/function/ConfigFormBase%3A%3Aconfig/8) 方法来检索用我们的`demo.settings`简单配置填充的`Config`对象。然后，对于电子邮件表单元素的`#default_value`，我们使用`Config`对象的 [get()](https://api.drupal.org/api/drupal/core%21lib%21Drupal%21Core%21Config%21Config.php/function/Config%3A%3Aget/8) 方法来检索电子邮件地址的值。

接下来，我们只需要更改提交处理程序，因为`validateForm()`方法现在可以保持不变:

```
public function submitForm(array &$form, array &$form_state) {

  $config = $this->config('demo.settings');
  $config->set('demo.email_address', $form_state['values']['email']);
  $config->save();

  return parent::submitForm($form, $form_state);
}
```

在这个方法中，我们首先为我们的配置检索`Config`对象(就像我们之前做的那样)。然后，我们使用它的 [set()](https://api.drupal.org/api/drupal/core%21lib%21Drupal%21Core%21Config%21Config.php/function/Config%3A%3Aset/8) 方法将`email_address`的值更改为用户提交的值。然后我们使用 [save()](https://api.drupal.org/api/drupal/core%21lib%21Drupal%21Core%21Config%21Config.php/function/Config%3A%3Asave/8) 方法保存配置。最后，我们扩展了父提交处理程序，因为它确实包含一些功能(在本例中，它在屏幕上设置了一条 Drupal 消息)。

差不多就是这样。你可以清空缓存，尝试一下。通过提交新的电子邮件地址，您可以将其存储在配置中。模块`demo.settings.yml`文件当然不会改变，但是您可以导出`demo.settings`配置并将其导入到另一个站点。

## 服务容器和依赖注入

我们接下来要看的是服务容器。服务背后的思想是将功能分成可重用的组件。因此，服务是一个 PHP 类，它执行一些全局操作，并向服务容器注册以便被访问。

依赖注入是我们将对象传递给其他对象以确保解耦的方式。每个服务需要处理一件事，如果它需要另一个服务，后者可以注入前者。但是我们一会儿就能看到。

接下来，我们将创建一个非常简单的服务，并将其注册到容器中。它只有一个返回简单值的实方法。然后，我们将把该服务作为依赖注入到我们的`DemoController`中，并利用该服务提供的值。

为了注册一个服务，我们需要在模块的根目录下创建一个`demo.services.yml`文件，包含以下内容:

```
services:
    demo.demo_service:
        class: Drupal\demo\DemoService
```

文件命名约定是`module_name.services.yml`。

第一行创建了一个服务数组。第二行定义了第一个服务(名为`demo_service`，以模块名为前缀)。第三行指定将为此服务实例化的类。接下来在我们模块的`src/`文件夹中创建`DemoService.php`类文件。这就是我的服务所做的事情(其实没什么，只是为了说明如何使用它):

```
<?php

/**
 * @file
 * Contains Drupal\demo\DemoService.
 */

namespace Drupal\demo;

class DemoService {

  protected $demo_value;

  public function __construct() {
    $this->demo_value = 'Upchuk';
  }

  public function getDemoValue() {
    return $this->demo_value;
  }

}
```

不需要在这里解释任何事情，因为这是非常基本的。接下来，让我们转向我们的`DemoController`并使用该服务。有两种方法可以做到这一点:通过`\Drupal`类全局访问容器，或者使用依赖注入将这个类的对象传递给我们的控制器。最佳实践表明我们应该用第二种方法，所以我们就这么做。但是有时你需要全局访问一个服务。为此，您可以这样做:

```
$service = \Drupal::service('demo.demo_service');
```

现在`$service`是我们刚刚创建的类`DemoService`的一个对象。但是让我们看看如何将我们的服务作为依赖注入到`DemoController`类中。我将首先解释需要做什么，然后您将看到整个控制器以及对它所做的所有更改。

首先，我们需要访问服务容器。有了控制器，这真的很容易。我们可以扩展 [ControllerBase](https://api.drupal.org/api/drupal/core%21lib%21Drupal%21Core%21Controller%21ControllerBase.php/class/ControllerBase/8) 类，它给我们提供了一些其他的助手。或者，我们的控制器可以实现[container injection interface](https://api.drupal.org/api/drupal/core%21lib%21Drupal%21Core%21DependencyInjection%21ContainerInjectionInterface.php/interface/ContainerInjectionInterface/8)，它也给了我们访问容器的权限。但是我们将坚持使用`ControllerBase`，所以我们需要*使用*那个类。

接下来，我们还需要*使用*Symfony 2[container interface](https://api.drupal.org/api/drupal/core%21vendor%21symfony%21dependency-injection%21Symfony%21Component%21DependencyInjection%21ContainerInterface.php/interface/ContainerInterface/8)作为`create()`方法的需求，该方法实例化我们的控制器类的另一个对象，并向它传递我们想要的服务。

最后，我们需要一个构造函数来获取传递的服务对象(即`create()`返回的对象)并将它们分配给属性以备后用。`create()`方法返回对象的顺序需要反映在它们传递给构造函数的顺序中。

那么让我们来看看我们修改后的`DemoController`:

```
<?php

/**
 * @file
 * Contains \Drupal\demo\Controller\DemoController.
 */

namespace Drupal\demo\Controller;

use Drupal\Core\Controller\ControllerBase;
use Symfony\Component\DependencyInjection\ContainerInterface;

/**
 * DemoController.
 */
class DemoController extends ControllerBase {

  protected $demoService;

  /**
   * Class constructor.
   */
  public function __construct($demoService) {
    $this->demoService = $demoService;
  }

  /**
   * {@inheritdoc}
   */
  public static function create(ContainerInterface $container) {
    return new static(
      $container->get('demo.demo_service')
    );
  }

  /**
   * Generates an example page.
   */
  public function demo() {
    return array(
      '#markup' => t('Hello @value!', array('@value' => $this->demoService->getDemoValue())),
    );
  }
}
```

如你所见，所有的步骤都在那里。`create()`方法创建了一个新的控制器类实例，并将从容器中检索到的服务传递给它。最后，`DemoService`类的一个实例存储在`$demoService`属性中，我们可以用它来调用它的`getDemoValue()`方法。然后这个值被用在 *Hello* 消息中。清空您的缓存并尝试一下。转到`demo/`路径，你应该看到页面上打印的`Hello Upchuk!`。

我相信您可以看到服务容器的强大之处，因为我们现在可以编写解耦的功能，并将其传递到需要的地方。我没有向您展示如何操作，但是您也可以在注册服务时声明依赖项。这意味着当 Drupal 实例化一个服务对象时，它也会对它的所有依赖项进行实例化，并将它们传递给它的构造函数。你可以在这个[文档页面](https://drupal.org/node/2133171)上阅读更多关于如何做的信息。

## 结论

在这篇文章中，我们看到了很多很酷的东西。我们已经看到了配置系统如何管理简单的配置，以及我们有什么可用的*表单方式的*用于此。我鼓励你探索一下`ConfigFormBase`是如何实现的，如果你扩展它，你会得到什么。此外，您应该在 UI 中尝试在站点之间导入/导出配置。从现在开始，这将是部署过程的一个巨大改进。

然后，我们看了服务，它们是什么以及它们是如何工作的。这是一种维护可重用和可从任何地方访问的解耦功能的好方法。我真的希望依赖注入的概念不再那么可怕(如果是对你来说的话)。它基本上相当于将参数传递给过程函数，但使用构造函数方法(或 setters)完成，在幕后由 Symfony 及其强大的服务容器完成。

## 分享这篇文章