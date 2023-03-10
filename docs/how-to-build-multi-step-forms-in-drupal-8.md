# 如何在 Drupal 8 中构建多步表单

> 原文：<https://www.sitepoint.com/how-to-build-multi-step-forms-in-drupal-8/>

在本文中，我们将研究如何在 Drupal 8 中构建多步表单。为简洁起见，该表单将只有两个步骤，形状为两个完全独立的表单。为了跨这些步骤持久化值，我们将使用 Drupal 核心提供的功能来跨多个请求存储临时和私有数据。

![Drupal 8 logo](img/a935c48e877a70a6250c9604c8474c63.png)

在 Drupal 7 中，使用 [cTools 对象缓存](http://www.webomelette.com/ctools-object-cache-multi-step-form)可以实现类似的方法。或者，也可以选择通过`$form_state`数组保存数据，如本教程中的[所示。](https://www.sitepoint.com/building-multi-page-wizard-like-form-drupal/)

我们在本文中编写的代码可以在这个库中找到，以及我们到目前为止所做的 Drupal 8 工作。我们将会经常处理表单，所以我建议查看一下[之前关于 Drupal 8](https://www.sitepoint.com/building-drupal-8-module-blocks-forms/) 的一篇文章，其中我们讨论了表单。

## 这个计划

正如我上面提到的，我们的多步形式将由两个独立的形式组成，每个形式有两个简单的元素。用户可以填写第一个表单，然后转到第二个表单，在第二个表单中，他们可以返回到上一步，也可以填写表单并点击提交。在不同步骤之间导航时，先前提交的值被存储并用于预填充表单字段。但是，如果提交了最后一个表单，数据将被处理(本文没有涉及)并从临时存储中清除。

从技术上来说，这两种表单都将从一个我们称之为`MultistepFormBase`的抽象表单类继承通用功能。这个类将负责注入必要的依赖项、搭建表单、处理最终结果以及其他任何需要的和两者共有的东西。

我们将所有的表单类组合在一起，并将它们放在一个名为`Multistep`的新文件夹中，该文件夹位于我们的`demo`模块的`Form`插件目录中(在旧的`DemoForm`旁边)。这纯粹是为了有一个清晰的结构，并且能够快速地分辨出哪些表单是我们多步表单过程的一部分。

## 代码

我们将从窗体基类开始。在我们看到代码后，我会解释这是怎么回事。

**MultistepFormBase.php:**

```
/**
 * @file
 * Contains \Drupal\demo\Form\Multistep\MultistepFormBase.
 */

namespace Drupal\demo\Form\Multistep;

use Drupal\Core\Form\FormBase;
use Drupal\Core\Form\FormStateInterface;
use Drupal\Core\Session\AccountInterface;
use Drupal\Core\Session\SessionManagerInterface;
use Drupal\user\PrivateTempStoreFactory;
use Symfony\Component\DependencyInjection\ContainerInterface;

abstract class MultistepFormBase extends FormBase {

  /**
   * @var \Drupal\user\PrivateTempStoreFactory
   */
  protected $tempStoreFactory;

  /**
   * @var \Drupal\Core\Session\SessionManagerInterface
   */
  private $sessionManager;

  /**
   * @var \Drupal\Core\Session\AccountInterface
   */
  private $currentUser;

  /**
   * @var \Drupal\user\PrivateTempStore
   */
  protected $store;

  /**
   * Constructs a \Drupal\demo\Form\Multistep\MultistepFormBase.
   *
   * @param \Drupal\user\PrivateTempStoreFactory $temp_store_factory
   * @param \Drupal\Core\Session\SessionManagerInterface $session_manager
   * @param \Drupal\Core\Session\AccountInterface $current_user
   */
  public function __construct(PrivateTempStoreFactory $temp_store_factory, SessionManagerInterface $session_manager, AccountInterface $current_user) {
    $this->tempStoreFactory = $temp_store_factory;
    $this->sessionManager = $session_manager;
    $this->currentUser = $current_user;

    $this->store = $this->tempStoreFactory->get('multistep_data');
  }

  /**
   * {@inheritdoc}
   */
  public static function create(ContainerInterface $container) {
    return new static(
      $container->get('user.private_tempstore'),
      $container->get('session_manager'),
      $container->get('current_user')
    );
  }

  /**
   * {@inheritdoc}.
   */
  public function buildForm(array $form, FormStateInterface $form_state) {
    // Start a manual session for anonymous users.
    if ($this->currentUser->isAnonymous() && !isset($_SESSION['multistep_form_holds_session'])) {
      $_SESSION['multistep_form_holds_session'] = true;
      $this->sessionManager->start();
    }

    $form = array();
    $form['actions']['#type'] = 'actions';
    $form['actions']['submit'] = array(
      '#type' => 'submit',
      '#value' => $this->t('Submit'),
      '#button_type' => 'primary',
      '#weight' => 10,
    );

    return $form;
  }

  /**
   * Saves the data from the multistep form.
   */
  protected function saveData() {
    // Logic for saving data goes here...
    $this->deleteStore();
    drupal_set_message($this->t('The form has been saved.'));

  }

  /**
   * Helper method that removes all the keys from the store collection used for
   * the multistep form.
   */
  protected function deleteStore() {
    $keys = ['name', 'email', 'age', 'location'];
    foreach ($keys as $key) {
      $this->store->delete($key);
    }
  }
}
```

我们的抽象表单类从默认的 Drupal `FormBase`类扩展而来，因此我们可以使用它提供的一些功能和它使用的特征。我们使用[依赖注入](http://www.webomelette.com/drupal-8-dependency-injection-service-container-and-all-jazz)来注入一些需要的服务:

*   `PrivateTempStoreFactory`给我们一个临时存储，它是当前用户的私有存储(`PrivateTempStore`)。我们将在此存储中保留表单步骤中提交的所有数据。在构造函数中，我们也立即保存了`store`属性，该属性包含了对我们将用于该流程的`multistep_data`键/值集合的引用。工厂上的`get()`方法要么创建不存在的存储，要么从存储中检索它。
*   `SessionManager`允许我们为匿名用户启动一个会话。
*   `CurrentUser`允许我们检查当前用户是否匿名。

在`buildForm()`方法中，我们主要做两件事。首先，我们为匿名用户启动一个会话(如果还不存在的话)。这是因为如果没有会话，我们就不能在多个请求之间传递临时数据。为此，我们使用会话管理器。其次，我们创建一个基本的提交操作按钮，它将出现在所有的实现表单上。

将从一个或多个实现表单中调用`saveData()`方法，该方法负责在多步流程完成后保存临时存储中的数据。我们不会深入这个实现的细节，因为它完全取决于您的用例(例如，您可以从每个提交中创建一个配置实体)。然而，一旦数据被持久化，我们就处理存储中所有项目的删除。请记住，这些类型的逻辑检查不应该在基类中执行。您应该像往常一样遵从专用的服务类，或者使用类似的方法。

现在该是表示流程中各个步骤的实际表单的时候了。我们从名为`MultistepOneForm.php`的文件中的第一个类开始:

```
/**
 * @file
 * Contains \Drupal\demo\Form\Multistep\MultistepOneForm.
 */

namespace Drupal\demo\Form\Multistep;

use Drupal\Core\Form\FormStateInterface;

class MultistepOneForm extends MultistepFormBase {

  /**
   * {@inheritdoc}.
   */
  public function getFormId() {
    return 'multistep_form_one';
  }

  /**
   * {@inheritdoc}.
   */
  public function buildForm(array $form, FormStateInterface $form_state) {

    $form = parent::buildForm($form, $form_state);

    $form['name'] = array(
      '#type' => 'textfield',
      '#title' => $this->t('Your name'),
      '#default_value' => $this->store->get('name') ? $this->store->get('name') : '',
    );

    $form['email'] = array(
      '#type' => 'email',
      '#title' => $this->t('Your email address'),
      '#default_value' => $this->store->get('email') ? $this->store->get('email') : '',
    );

    $form['actions']['submit']['#value'] = $this->t('Next');
    return $form;
  }

  /**
   * {@inheritdoc}
   */
  public function submitForm(array &$form, FormStateInterface $form_state) {
    $this->store->set('email', $form_state->getValue('email'));
    $this->store->set('name', $form_state->getValue('name'));
    $form_state->setRedirect('demo.multistep_two');
  }
}
```

这个表单看起来会像这样:

![Drupal 8 Multistep Forms 1](img/ae3cffc97fcd8a3be7619fff82920652.png)

在`buildForm()`方法中，我们定义了两个虚拟表单元素。请注意，我们首先从父类中检索现有的表单定义。这些字段的缺省值被设置为在存储库中找到的那些键的值(以便用户在返回时可以看到他们在这一步填写的值)。最后，我们将 action 按钮的值改为`Next`(以表明此表单不是最终表单)。

在`submitForm()`方法中，我们将提交的值保存到存储中，然后重定向到第二个表单(可以在 route `demo.multistep_two`中找到)。请记住，我们在这里不做任何验证来保持代码简洁。但是大多数用例需要一些输入验证。

既然我们已经谈到了路线的问题，让我们更新我们的`demo`模块中的路线文件，并为我们的表单创建两条新路线:

**demo.routing.yml** :

```
demo.multistep_one:
  path: '/demo/multistep-one'
  defaults:
    _form: '\Drupal\demo\Form\Multistep\MultistepOneForm'
    _title: 'First form'
  requirements:
    _permission: 'access content'
demo.multistep_two:
  path: '/demo/multistep-two'
  defaults:
    _form: '\Drupal\demo\Form\Multistep\MultistepTwoForm'
    _title: 'Second form'
  requirements:
    _permission: 'access content'
```

关于这个文件的更多信息，你可以阅读以前的 Drupal 8 文章中的一篇[，它也解释了路线。](https://www.sitepoint.com/build-drupal-8-module-routing-controllers-menu-links/)

最后，我们可以创建第二个表单(在一个名为`MultistepTwoForm`的文件中):

```
/**
 * @file
 * Contains \Drupal\demo\Form\Multistep\MultistepTwoForm.
 */

namespace Drupal\demo\Form\Multistep;

use Drupal\Core\Form\FormStateInterface;
use Drupal\Core\Url;

class MultistepTwoForm extends MultistepFormBase {

  /**
   * {@inheritdoc}.
   */
  public function getFormId() {
    return 'multistep_form_two';
  }

  /**
   * {@inheritdoc}.
   */
  public function buildForm(array $form, FormStateInterface $form_state) {

    $form = parent::buildForm($form, $form_state);

    $form['age'] = array(
      '#type' => 'textfield',
      '#title' => $this->t('Your age'),
      '#default_value' => $this->store->get('age') ? $this->store->get('age') : '',
    );

    $form['location'] = array(
      '#type' => 'textfield',
      '#title' => $this->t('Your location'),
      '#default_value' => $this->store->get('location') ? $this->store->get('location') : '',
    );

    $form['actions']['previous'] = array(
      '#type' => 'link',
      '#title' => $this->t('Previous'),
      '#attributes' => array(
        'class' => array('button'),
      ),
      '#weight' => 0,
      '#url' => Url::fromRoute('demo.multistep_one'),
    );

    return $form;
  }

  /**
   * {@inheritdoc}
   */
  public function submitForm(array &$form, FormStateInterface $form_state) {
    $this->store->set('age', $form_state->getValue('age'));
    $this->store->set('location', $form_state->getValue('location'));

    // Save the data
    parent::saveData();
    $form_state->setRedirect('some_route');
  }
}
```

这个看起来像这样，同样非常简单:

![Drupal 8 Multistep Forms 1](img/2a7f84ef4b02a6992b03a128fde3d7a2.png)

同样，我们从基类扩展，就像我们对第一个表单所做的那样。然而，这一次我们有了不同的表单元素，我们在 submit 按钮旁边添加了一个新的 action 链接。这将允许用户导航回表单流程的第一步。

在`submitForm()`方法中，我们再次将值保存到存储中，并委托父类以它认为合适的任何方式保存这些数据。然后，我们重定向到我们想要的任何页面(我们这里使用的路径是一个虚拟路径)。

差不多就是这样。我们现在应该有一个工作的多步骤表单，它使用`PrivateTempStore`来保持数据在多个请求中可用。如果我们需要更多的步骤，我们所要做的就是创建更多的表单，将它们添加到现有的表单之间，并进行一些调整。当然，您可以通过不在链接和重定向中硬编码路由名称来使这变得更加灵活，但是我把这部分留给了您。

## 结论

在本文中，我们研究了一种在 Drupal 8 中创建多步表单的简单方法。当然，您可以在这种方法的基础上创建高度复杂和动态的流程，不仅涉及表单，还涉及利用交叉请求数据的其他步骤。因此，这篇文章的目的是展示多步表单和`PrivateTempStore`的强大功能。如果您和我一样，认为 cTools 对象缓存在 Drupal 7 中非常强大，那么您将会对它的 Drupal 8 版本非常感兴趣。

## 分享这篇文章