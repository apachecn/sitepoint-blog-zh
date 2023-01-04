# 构建 Drupal 8 模块:块和表单

> 原文：<https://www.sitepoint.com/building-drupal-8-module-blocks-forms/>

请注意，由于撰写本文时 Drupal 8 正在经历开发过程，部分代码可能已经过时。看一下[这个库](https://github.com/upchuk/d8-demo-modules)，我试图在其中更新示例代码，并使其与最新的 Drupal 8 版本兼容。

在这篇关于 Drupal 8 模块开发的文章[系列的第一部分](https://www.sitepoint.com/series/how-to-build-a-drupal-8-module/)中，我们从基础开始。我们已经看到了让 Drupal 了解我们的模块需要什么文件，路由过程是如何工作的，以及如何以编程方式创建菜单链接作为配置。

在本教程中，我们将进一步介绍在[这个库](https://github.com/upchuk/d8-demo-modules)中找到的沙盒模块，并查看两个新的重要功能:块和表单。为此，我们将创建一个自定义块来返回一些可配置的文本。之后，我们将创建一个简单的表单，用于将用户提交的值打印到屏幕上。

## Drupal 8 街区

D8 中对 block API 的一个很酷的新变化是通过使它们成为插件(一个全新的概念)来使 block 更加突出。这意味着它们是可重用的功能块(在引擎盖下),因为你现在可以在 UI 中创建一个块，并在整个站点中重用它——你不再局限于只使用一个块一次。

让我们继续创建一个简单的块类型，打印到屏幕上 *Hello World！*默认。我们需要处理的只是一个位于模块根目录的`src/Plugin/Block`文件夹中的类文件。让我们称我们的新块类型为`DemoBlock`，自然它需要驻留在一个名为`DemoBlock.php`的文件中。在这个文件中，我们可以从以下内容开始:

```
<?php

namespace Drupal\demo\Plugin\Block;

use Drupal\block\BlockBase;
use Drupal\Core\Session\AccountInterface;

/**
 * Provides a 'Demo' block.
 *
 * @Block(
 *   id = "demo_block",
 *   admin_label = @Translation("Demo block"),
 * )
 */

class DemoBlock extends BlockBase {

  /**
   * {@inheritdoc}
   */
  public function build() {    
    return array(
      '#markup' => $this->t('Hello World!'),
    );
  }

  /**
   * {@inheritdoc}
   */
  public function access(AccountInterface $account) {
    return $account->hasPermission('access content');
  }  

}
```

像所有其他类文件一样，我们从命名我们的类开始。然后我们*使用*的`BlockBase`类来扩展它，以及`AccountInterface`类来访问当前登录的用户。接下来是您在 Drupal 7 中绝对没有见过的东西:注释。

注释是一个 PHP 发现工具，位于与类定义相同的文件的注释块中。使用这些注释，我们让 Drupal 知道我们想要注册一个新的块类型(`@Block`)，id 为`demo_block`，演示块(通过翻译系统)的`admin_label`。

接下来，我们将`BlockBase`类扩展到我们自己的`DemoBlock`中，在其中我们实现了两个方法(您将实现的最常见的方法)。`build()`方法是最重要的，因为它返回块将打印出来的可呈现数组。`access()`方法控制查看该块的访问权限。传递给它的参数是`AccountInterface`类的一个实例，在本例中是当前用户。

需要注意的另一件有趣的事情是，我们不再全局使用`t()`函数进行翻译，而是引用了在父类中实现的`t()`方法。

就这样，您可以清除缓存并转到`Block layout`配置页面。最酷的事情是你在右边有块类型(你可以过滤)，你可以放置一个或多个块到网站的不同区域。

## Drupal 8 块配置

既然我们已经看到了如何从 UI 创建一个新的块类型来使用，那么让我们进一步进入 API 并为它添加一个配置表单。我们将这样做，以便您可以编辑该块，在文本字段中指定一个名称，然后该块将向该名称问好，而不是向*世界*问好。

首先，我们需要定义包含 textfield 的表单。所以在我们的`DemoBlock`类中，我们可以添加一个名为`blockForm()`的新方法:

```
/**
 * {@inheritdoc}
 */
public function blockForm($form, &$form_state) {

  $form = parent::blockForm($form, $form_state);

  $config = $this->getConfiguration();

  $form['demo_block_settings'] = array(
    '#type' => 'textfield',
    '#title' => $this->t('Who'),
    '#description' => $this->t('Who do you want to say hello to?'),
    '#default_value' => isset($config['demo_block_settings']) ? $config['demo_block_settings'] : '',
  );

  return $form;
}
```

这个表单 API 实现在 Drupal 7 中应该看起来非常熟悉。然而，这里正在发生一些新的事情。首先，我们从父类中检索`$form`数组(因此我们通过添加自己的字段来构建现有表单)。标准的面向对象的东西。然后，我们检索并存储该块的配置。`BlockBase`类定义了为我们做这件事的`getConfiguration()`方法。我们将`demo_block_settings`值作为`#default_value`，以防它已经被设置。

接下来，该表单的提交处理程序处理我们字段的值，并将其存储在块的配置中:

```
/**
* {@inheritdoc}
*/
public function blockSubmit($form, &$form_state) {

 $this->setConfigurationValue('demo_block_settings', $form_state['values']['demo_block_settings']);

}
```

这个方法也在`DemoBlock`类中，它所做的只是将`demo_block_settings`字段的值保存为块配置中的一个新项(为了一致性，使用相同的名称作为关键字)。

最后，我们需要修改我们的`build()`方法，以包含要问好的名字:

```
/**
 * {@inheritdoc}
 */
public function build() {

  $config = $this->getConfiguration();

  if (isset($config['demo_block_settings']) && !empty($config['demo_block_settings'])) {
    $name = $config['demo_block_settings'];
  }
  else {
    $name = $this->t('to no one');
  }

  return array(
    '#markup' => $this->t('Hello @name!', array('@name' => $name)),
  );  
}
```

到目前为止，这应该看起来相当容易。我们正在检索块的配置，如果我们的字段的值被设置，我们将在打印的语句中使用它。如果没有，使用一个通用的。您可以清除缓存，并通过编辑分配给区域的块并添加一个名称来对其进行测试。要记住的一点是，在打印到屏幕上时，您仍然要负责净化用户输入。为了简洁起见，我没有包括这些步骤。

## Drupal 8 表单

本教程中我们要探讨的最后一件事是如何创建一个简单的表单。由于篇幅的限制，我将不讨论它的配置管理方面(存储通过表单提交的配置值)。相反，我将举例说明一个简单的表单定义，提交的值被简单地打印在屏幕上，以表明它是有效的。

在 Drupal 8 中，表单定义函数都集中在一个类中。所以让我们在`src/Form/DemoForm.php`中定义简单的`DemoForm`类:

```
<?php

/**
 * @file
 * Contains \Drupal\demo\Form\DemoForm.
 */

namespace Drupal\demo\Form;

use Drupal\Core\Form\FormBase;

class DemoForm extends FormBase {

  /**
   * {@inheritdoc}.
   */
  public function getFormId() {
    return 'demo_form';
  }

  /**
   * {@inheritdoc}.
   */
  public function buildForm(array $form, array &$form_state) {

    $form['email'] = array(
      '#type' => 'email',
      '#title' => $this->t('Your .com email address.')
    );
    $form['show'] = array(
      '#type' => 'submit',
      '#value' => $this->t('Submit'),
    );

    return $form;
  }

  /**
   * {@inheritdoc}
   */
  public function validateForm(array &$form, array &$form_state) {

    if (strpos($form_state['values']['email'], '.com') === FALSE ) {
      $this->setFormError('email', $form_state, $this->t('This is not a .com email address.'));
    } 
  }

  /**
   * {@inheritdoc}
   */
  public function submitForm(array &$form, array &$form_state) {

    drupal_set_message($this->t('Your email address is @email', array('@email' => $form_state['values']['email'])));
  }

}
```

除了面向对象方面，Drupal 7 应该对所有的东西都非常熟悉。表单 API 几乎没有变化(除了添加了一些新的表单元素和这个类封装)。那么上面发生了什么？

首先，我们命名这个类，*使用*核心的`FormBase`类，这样我们就可以用我们自己的`DemoForm`类来扩展它。然后我们实现 4 个方法，其中 3 个应该看起来很熟悉。`getFormId()`方法是新的和强制的，只是用来返回表单的机器名。`buildForm()`方法也是强制的，它构建表单。怎么会？就像你在 Drupal 7 中习惯的那样。`validateForm()`方法是可选的，其目的从 D7 开始就应该很清楚了。最后，`submitForm()`方法进行提交处理。非常有逻辑性和组织性。

那么我们用这种形式想要达到什么目的呢？我们有一个希望用户填写的电子邮件字段(Drupal 8 中的一个新表单元素)。默认情况下，Drupal 检查输入的值是否实际上是一个电子邮件地址。但是在我们的验证函数中，我们确保它是一个`.com`电子邮件地址，如果不是，我们在字段中设置一个表单错误。最后，提交处理程序只是在页面上打印一条消息。

为了使用这个表单，我们需要做的最后一件事是为它提供一个路径。因此，编辑`demo.routing.yml`文件并添加以下内容:

```
demo.form:
  path: '/demo/form'
  defaults:
    _form: '\Drupal\demo\Form\DemoForm'
    _title: 'Demo Form'
  requirements:
    _permission: 'access content'
```

从[上一篇文章](https://www.sitepoint.com/build-drupal-8-module-routing-controllers-menu-links/)中我们路由了一个简单的页面，这应该看起来很熟悉。唯一大的不同是，我们使用`_form`来指定目标是一个表单类，而不是`defaults`下的`_content`。因此这个值就是我们刚刚创建的类名。

清除缓存并导航到`demo/form`查看表单并测试。

如果你熟悉`drupal_get_form()`并且想知道如何像我们以前在 Drupal 7 中那样加载表单，答案就在全局 [Drupal](https://api.drupal.org/api/drupal/core%21lib%21Drupal.php/class/Drupal/8) 类中。因此，要检索一个表单，可以使用它的`formBuilder()`方法，如下所示:

```
$form = \Drupal::formBuilder()->getForm('Drupal\demo\Form\DemoForm');
```

然后您可以返回`$form`，它将是表单的可呈现数组。

## 结论

在本文中，我们继续探索 Drupal 8 模块开发的两个新主题:块和表单。我们已经看到了如何创建我们自己的块类型，我们可以用它来创建 UI 中的块。我们还学习了如何向它添加自定义配置并存储值以备后用。关于表单的话题，我们已经看到了一个简单的`FormBase`类的实现，我们用它在屏幕上打印出用户提交的值。

在下一个教程中，我们将快速浏览一下配置表单。我们将保存用户使用 Drupal 8 配置系统提交的值。此外，我们将了解服务容器和依赖注入，以及它们在 Drupal 8 中是如何工作的。到时候见。

## 分享这篇文章