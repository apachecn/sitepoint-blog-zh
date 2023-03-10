# Drupal 8 Hooks 和 Symfony 事件调度程序

> 原文：<https://www.sitepoint.com/drupal-8-hooks-symfony-event-dispatcher/>

请注意，由于撰写本文时 Drupal 8 正在经历开发过程，部分代码可能已经过时。看一下[这个库](https://github.com/upchuk/d8-demo-modules)，我试图在其中更新示例代码，并使其与最新的 Drupal 8 版本兼容。

随着许多 Symfony 组件被整合到 Drupal 的第八版中，我们看到了从许多 drupalism 向更现代的 PHP 架构决策的转变。例如，让人又爱又恨的钩子系统正在慢慢被取代。插件和注释减少了对`info`钩子的需求，Symfony Event Dispatcher 组件取代了一些*调用的*钩子。尽管它们在 Drupal 8 中仍然很强大，但是很有可能在 Drupal 9(或者 10)中钩子将被完全移除。

![](img/f9690f1e98b563da0de2ec95432340dd.png)

在本文中，我们将主要研究 Symfony 事件调度器组件在 Drupal 中是如何工作的。此外，我们还将看到如何在 Drupal 8 中调用并实现一个钩子来实现与前者相似的目标。

要跟进或快速入门，您可以在[这个库](https://github.com/upchuk/d8-demo-modules)中找到我们使用的所有代码。你可以只安装模块，你就可以走了。使用的 Drupal 8 版本是第一个测试版，所以最好使用那个版本以确保兼容性。阿尔法 15 应该也能正常工作。让我们开始吧。

### 什么是事件调度程序组件？

在 Symfony 网站的[上可以找到事件调度器组件的一个非常好的定义:](http://symfony.com/doc/current/components/event_dispatcher/introduction.html)

> EventDispatcher 组件提供了一些工具，允许您的应用程序组件通过调度事件和侦听事件来相互通信。

我建议仔细阅读该文档，以便更好地理解事件调度程序背后的原理。你会得到一个很好的介绍，它是如何在 Symfony 中工作的，所以我们不会在这里讨论。相反，我们将看到一个如何在 Drupal 8 中使用它的例子。

## Drupal 8 和事件调度程序

在本文的大部分内容中，我们将重点演示在 Drupal 8 中使用事件调度程序。为此，我们将创建一个简单的演示模块(`event_dispatcher_demo`)，它有一个配置表单，将两个值保存为配置。在保存这个表单时，我们将调度一个包含 config 对象的事件，该事件将允许应用程序的其他部分在保存之前截取和修改它。最后，我们将通过演示如何订阅(或收听)这些事件来做到这一点。

在 Drupal 7 中，这种类型的模块化只能通过钩子来实现。钩子被调用，模块可以选择实现它们并贡献自己的数据。在本文的最后，我们将看到如何在 Drupal 8 中做到这一点。但首先，让我们继续我们的演示模块。

如果您不了解 Drupal 8 模块开发的基础知识，我建议查看我在本系列中以前的文章。

### 表单

我们需要的第一件事是一个带有两个字段的简单配置表单。在位于`src/Form`文件夹中名为`DemoForm.php`的文件中，我们有以下内容:

```
<?php

/**
 * @file
 * Contains Drupal\event_dispatcher_demo\Form\DemoForm.
 */

namespace Drupal\event_dispatcher_demo\Form;

use Drupal\Core\Form\ConfigFormBase;
use Drupal\Core\Form\FormStateInterface;

class DemoForm extends ConfigFormBase {

  /**
   * {@inheritdoc}
   */
  public function getFormID() {
    return 'demo_form';
  }

  /**
   * {@inheritdoc}
   */
  public function buildForm(array $form, FormStateInterface $form_state) {
    $config = $this->config('event_dispatcher_demo.demo_form_config');
    $form['my_name'] = [
      '#type' => 'textfield',
      '#title' => $this->t('My name'),
      '#default_value' => $config->get('my_name'),
    ];
    $form['my_website'] = [
      '#type' => 'textfield',
      '#title' => $this->t('My website'),
      '#default_value' => $config->get('my_website'),
    ];
    return parent::buildForm($form, $form_state);
  }

  /**
   * {@inheritdoc}
   */
  public function submitForm(array &$form, FormStateInterface $form_state) {

    parent::submitForm($form, $form_state);

    $config = $this->config('event_dispatcher_demo.demo_form_config');

    $config->set('my_name', $form_state->getValue('my_name'))
      ->set('my_website', $form_state->getValue('my_website'));

    $config->save();
  }

}
```

让我们也为它创建一个路径(在`event_dispatcher_demo.routing.yml`文件中),这样我们可以在浏览器中访问表单:

```
event_dispatcher_demo.demo_form:
  path: 'demo-form'
  defaults:
    _form: '\Drupal\event_dispatcher_demo\Form\DemoForm'
    _title: 'Demo form'
  requirements:
    _permission: 'access administration pages'
```

所以现在如果你把浏览器指向`example.com/demo-form`，你应该会看到这个表单。提交它将创建并持久化一个名为`event_dispatcher_demo.demo_form_config`的配置对象，它包含两个字段:*我的名字*和*我的网站*。

### 事件调度程序

现在是时候处理表单提交处理程序(`formSubmit()`方法)并在表单保存时调度一个事件了。这是新方法的外观:

```
public function submitForm(array &$form, FormStateInterface $form_state) {

  parent::submitForm($form, $form_state);

  $config = $this->config('event_dispatcher_demo.demo_form_config');

  $config->set('my_name', $form_state->getValue('my_name'))
    ->set('my_website', $form_state->getValue('my_website'));

  $dispatcher = \Drupal::service('event_dispatcher');

  $e = new DemoEvent($config);

  $event = $dispatcher->dispatch('demo_form.save', $e);

  $newData = $event->getConfig()->get();

  $config->merge($newData);

  $config->save();
}
```

那么这里发生了什么？在我们像前面一样获取提交的值并将它们添加到 config 对象之后，我们从服务容器中检索事件调度器对象:

```
$dispatcher = \Drupal::service('event_dispatcher');
```

请记住，建议您将该服务注入到您的类中，但是为了简洁起见，我们将静态地检索它。你可以阅读这篇关于[依赖注入和服务容器](http://www.webomelette.com/drupal-8-dependency-injection-service-container-and-all-jazz)的文章来获得更多信息。

然后我们创建一个新的`DemoEvent`对象，并通过它的构造函数将`$config`传递给它(我们还没有创建`DemoEvent`类，我们马上就会完成)。接下来，我们使用 dispatcher 来调度我们类型的事件，并为这个动作分配标识符`demo_form.save`。这将在订阅事件时使用(我们稍后会看到)。`dispatch()`方法返回修改过的事件对象，这样我们就可以检索在其他地方修改过或没有修改过的配置值，并将它们合并到我们的原始配置中。最后，我们像最初一样保存这个对象。

在进入我们的应用程序的事件订阅部分之前，让我们创建我们刚刚在上面实例化的`DemoEvent`类。在位于我们模块的`src/`文件夹中的一个名为`DemoEvent.php`的文件中，我们有如下内容:

```
<?php

/**
 * @file
 * Contains Drupal\event_dispatcher_demo\DemoEvent.
 */

namespace Drupal\event_dispatcher_demo;

use Symfony\Component\EventDispatcher\Event;
use Drupal\Core\Config\Config;

class DemoEvent extends Event {

  protected $config;

  /**
   * Constructor.
   *
   * @param Config $config
   */
  public function __construct(Config $config) {
    $this->config = $config;
  }

  /**
   * Getter for the config object.
   *
   * @return Config
   */
  public function getConfig() {
    return $this->config;
  }

  /**
   * Setter for the config object.
   *
   * @param $config
   */
  public function setConfig($config) {
    $this->config = $config;
  }

}
```

如您所见，这是一个简单的类，它扩展了默认的`Event`类，并为我们将使用该事件传递的 config 对象定义了 setters 和 getters。因为我们创建了它，所以我们也要确保在定义表单的文件中使用了它:

```
use Drupal\event_dispatcher_demo\DemoEvent;
```

### 事件订阅者

既然我们的表单运行正常，并且在保存表单时调度了一个事件，我们应该利用这一点并订阅它。让我们从实现`EventSubscriberInterface`的事件订阅者类开始。在位于`src/EventSubscriber/`文件夹中的一个名为`ConfigSubscriber.php`(您选择的名称)的文件中，我们有以下内容:

```
<?php

/**
 * @file
 * Contains Drupal\event_dispatcher_demo\EventSubscriber\ConfigSubscriber.
 */

namespace Drupal\event_dispatcher_demo\EventSubscriber;

use Symfony\Component\EventDispatcher\EventSubscriberInterface;

class ConfigSubscriber implements EventSubscriberInterface {

  static function getSubscribedEvents() {
    $events['demo_form.save'][] = array('onConfigSave', 0);
    return $events;
  }

  public function onConfigSave($event) {

    $config = $event->getConfig();

    $name_website = $config->get('my_name') . " / " . $config->get('my_website');
    $config->set('my_name_website', $name_website);
  }

}
```

那么这里发生了什么？ [EventSubscriberInterface](https://api.drupal.org/api/drupal/core!vendor!symfony!event-dispatcher!Symfony!Component!EventDispatcher!EventSubscriberInterface.php/interface/EventSubscriberInterface/8) 只有一个名为`getSubscribedEvents()`的必需方法。此方法用于注册事件和对这些事件的回调。因此，上面我们用标识符`demo_form.save`将*可调用的* `onConfigSave()`(在下面的同一个类中找到)注册到事件调度中。在回调方法中，我们简单地向 config 对象添加另一个值(基于现有两个值的连接)。后面的部分只是为了我们的演示目的:在这里你可以做你想做的。

当我们订阅我们的`onConfigSave()`方法来监听`demo_form.save`事件时，我们传递了一个权重 0。如果您为同一个事件注册了多个回调，这个权重就变得很重要(数字越大，调用的时间就越早)。如果回调改变了与之前触发的值相同的值，它们将被覆盖。记住这一点就好。

现在为了让这个事件订阅者工作，我们需要将它定义为一个服务，并给它一个`event_subscriber`标签。因此，在我们模块的根文件夹中的一个名为`event_dispatcher_demo.services.yml`的文件中，我们将有这样一个:

```
services:
  event_dispatcher_demo.config_subscriber:
    class: Drupal\event_dispatcher_demo\EventSubscriber\ConfigSubscriber
    tags:
      - { name: event_subscriber }
```

这是一个带有正确标签的简单服务定义，只要调度程序在运行，它就会让容器自动实例化该类的一个对象。差不多就是这样。清除缓存，如果现在再次保存表单，保存的配置对象将始终包含基于前两个值的新值。

## 钩住

在本文的最后一部分，我们将演示如何使用钩子来实现类似的目标。

首先，让我们更改表单提交处理程序，而不是分派事件，我们将调用一个钩子并将配置值传递给它。这是新的`submitForm()`方法的样子:

```
public function submitForm(array &$form, FormStateInterface $form_state) {

	parent::submitForm($form, $form_state);

	$config = $this->config('event_dispatcher_demo.demo_form_config');

	$config->set('my_name', $form_state->getValue('my_name'))
	  ->set('my_website', $form_state->getValue('my_website'));

	$configData = $config->get();
	$newData = \Drupal::service('module_handler')->invokeAll('demo_config_save', array($configData));

	$config->merge($newData);

	$config->save();
}
```

我们没有使用任何事件对象，也没有使用 dispatcher 服务。相反，我们检索包含用于从所有模块调用钩子实现的`invokeAll()`方法的[模块处理程序](https://api.drupal.org/api/drupal/core!lib!Drupal!Core!Extension!ModuleHandler.php/class/ModuleHandler/8)服务。这实质上是取代了 Drupal 7[module _ invoke _ all()](https://api.drupal.org/api/drupal/includes!module.inc/function/module_invoke_all/7)助手。同样，建议注入这个服务，但是为了简洁起见，我们将静态地检索它。

在我们的例子中调用的钩子实现是`hook_demo_config_save`，它得到一个参数，一个从我们的配置对象中提取的值的数组。在`$newData`中，我们将有一个由这个钩子的所有实现合并而成的值数组。然后，我们将它合并到我们的配置对象中，最后保存它。

让我们快速看一个钩子实现的例子。和 Drupal 7 一样，这些只能在`.module`文件中:

```
/**
 * Implements hook_demo_config_save().
 */
function event_dispatcher_demo_demo_config_save($configValues) {

  $configValues['my_name_website'] = $configValues['my_name'] . " / " . $configValues['my_website'];

  return $configValues;

}
```

如您所见，我们向配置数组添加了一个新值，该值稍后将被合并到被持久化的对象中。我们拥有与事件调度程序基本相同的东西。

## 结论

在本文中，我们已经了解了在 Drupal 8 中 Symfony Event Dispatcher 组件是如何工作的。我们已经知道，当允许其他人扩展功能时，它使我们的应用程序变得多么灵活。此外，我们已经看到了在 Drupal 的新版本中被调用的钩子是如何工作的。自 Drupal 7 以来，除了使用频率之外，在这方面没有太大的变化。许多钩子已经被插件和注释取代，事件调度器组件也承担了 D7 中钩子的大部分责任。

尽管事件调度程序方法更加冗长，但这是推荐的前进方式。在可能的情况下，我们不再使用钩子特有的旧的过程方法，而是使用面向对象的、解耦的和可测试的解决方案。Symfony 在这方面帮助很大。

## 分享这篇文章