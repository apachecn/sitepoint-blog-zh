# Drupal 8 队列 API——强大的手动和 Cron 队列

> 原文：<https://www.sitepoint.com/drupal-8-queue-api-powerful-manual-and-cron-queueing/>

![Drupal 8 logo](img/86e2dfe837ba6b0ca72e19e81899e0a5.png)

Drupal 中的队列 API 允许我们在稍后阶段处理大量任务。这意味着我们可以将*个项目*放入一个队列中，这个队列将在未来某个时间运行，并且*在那个时间至少处理*个项目一次。通常，这发生在 [CRON](https://en.wikipedia.org/wiki/Cron) 运行时，Drupal 8 允许快速设置基于 cronjob 的队列。然而，不一定非得是 CRON。

在本文中，我们将通过探索两个简单的例子来研究在 Drupal 8 中使用队列 API。第一个将看到由 Cron 触发的队列，而第二个将允许我们自己手动这样做。然而，实际的处理将由一个类似的工作器来处理。如果你想继续，克隆这个 git 库的[，在那里你可以找到我们将在本文中编写的`npq`模块。](https://github.com/upchuk/d8-demo-modules/tree/master/npq)

我们将使用的模块称为*节点发布队列*，它会自动将未发布的新创建节点添加到一个队列中，以便稍后发布。我们将在稍后的中看到*如何成为下一个 CRON 运行或者站点管理员触发的手动操作。首先，让我们了解一些关于 Drupal 8 中队列的基本概念。*

## 该理论

Drupal 8 中的队列 API 由几个组件组成。

这个 API 中最重要的角色由代表队列的`QueueInterface`实现扮演。Drupal 8 附带的默认队列类型目前是`DatabaseQueue`，这是一种可靠的队列类型，它确保所有的项目至少被处理一次，并且按照它们原来的顺序( [FIFO](https://en.wikipedia.org/wiki/FIFO_(computing_and_electronics)) )。这与不可靠的队列形成对比，不可靠的队列只有*尽最大努力*来实现这一点(有效的用例确实存在)。

队列对象的典型作用是创建项目，稍后*从队列中认领*它们，并在它们被处理后删除它们。此外，如果加工未完成或者另一个*工人*需要在删除前再次加工，它可以*释放*项。

在通用`QueueFactory`的帮助下，`QueueInterface`实现被实例化。在`DatabaseQueue`的情况下，前者也使用`DatabaseQueueFactory`。队列在使用之前也需要*创建*。然而，`DatabaseQueue`在 Drupal 第一次安装时就已经创建好了，所以不需要额外的设置。

队列工作人员负责在收到队列项目时对其进行处理。在 Drupal 8 中，这些是实现`QueueWorkerInterface`的`QueueWorker`插件。使用`QueueWorkerManager`，我们创建这些插件的实例，并在队列需要运行时处理这些项目。

## 节点发布队列模块

现在我们已经介绍了 Drupal 8 中队列 API 的基本概念，让我们动手创建介绍中描述的功能。我们的 *npq.info.yml* 文件可以很简单:

```
name: Node Publish Queue
description: Demo module illustrating the Queue API in Drupal 8
core: 8.x
type: module 
```

### 队列项目创建

在`npq.module`文件中，我们负责在节点被保存和未发布时创建队列项的逻辑:

```
use Drupal\Core\Entity\EntityInterface;
use Drupal\Core\Queue\QueueFactory;
use Drupal\Core\Queue\QueueInterface;

/**
 * Implements hook_entity_insert().
 */
function npq_entity_insert(EntityInterface $entity) {
  if ($entity->getEntityTypeId() !== 'node') {
    return;
  }

  if ($entity->isPublished()) {
    return;
  }

  /** @var QueueFactory $queue_factory */
  $queue_factory = \Drupal::service('queue');
  /** @var QueueInterface $queue */
  $queue = $queue_factory->get('cron_node_publisher');
  $item = new \stdClass();
  $item->nid = $entity->id();
  $queue->createItem($item);
} 
```

在这个基本的`hook_entity_insert()`实现中，我们做了一个非常简单的任务。我们首先从服务容器中检索`QueueFactoryInterface`对象，并用它来*获取*一个名为`cron_node_publisher`的队列。如果我们追踪一下，我们会注意到`DatabaseQueueFactory`上的`get()`方法只是用我们传递给它的名字创建了一个新的`DatabaseQueue`实例。

最后，我们创建一个包含节点 ID 的小 PHP 对象，并用该数据在队列中创建一个项目。简单。

### CRON 队列工作器

接下来，让我们创建一个`QueueWorker`插件，它将在 Cron 运行时处理队列项。然而，因为我们知道我们也将需要一个手动处理来做同样的事情，我们将在一个基本抽象类中添加大部分逻辑。所以在我们模块的`Plugin/QueueWorker`命名空间中，我们可以有`NodePublishBase`类:

```
/**
 * @file
 * Contains Drupal\npq\Plugin\QueueWorker\NodePublishBase.php
 */

namespace Drupal\npq\Plugin\QueueWorker;

use Drupal\Core\Entity\EntityStorageInterface;
use Drupal\Core\Plugin\ContainerFactoryPluginInterface;
use Drupal\Core\Queue\QueueWorkerBase;
use Drupal\node\NodeInterface;
use Symfony\Component\DependencyInjection\ContainerInterface;

/**
 * Provides base functionality for the NodePublish Queue Workers.
 */
abstract class NodePublishBase extends QueueWorkerBase implements ContainerFactoryPluginInterface {

  /**
   * The node storage.
   *
   * @var \Drupal\Core\Entity\EntityStorageInterface
   */
  protected $nodeStorage;

  /**
   * Creates a new NodePublishBase object.
   *
   * @param \Drupal\Core\Entity\EntityStorageInterface $node_storage
   *   The node storage.
   */
  public function __construct(EntityStorageInterface $node_storage) {
    $this->nodeStorage = $node_storage;
  }

  /**
   * {@inheritdoc}
   */
  public static function create(ContainerInterface $container, array $configuration, $plugin_id, $plugin_definition) {
    return new static(
      $container->get('entity.manager')->getStorage('node')
    );
  }

  /**
   * Publishes a node.
   *
   * @param NodeInterface $node
   * @return int
   */
  protected function publishNode($node) {
    $node->setPublished(TRUE);
    return $node->save();
  }

  /**
   * {@inheritdoc}
   */
  public function processItem($data) {
    /** @var NodeInterface $node */
    $node = $this->nodeStorage->load($data->nid);
    if (!$node->isPublished() && $node instanceof NodeInterface) {
      return $this->publishNode($node);
    }
  }
} 
```

马上我们可以看到我们正在使用依赖注入将`NodeStorage`注入到我们的类中。要了解更多关于依赖注入和服务容器的信息，请随意查看我的文章。

在这个基类中，我们有两个方法:`publishNode()`和强制的`processItem()`。前者发布并保存传递给它的节点。后者使用包含在`$data`对象中的节点 ID 加载节点，如果它未发布，就发布它。

现在，让我们创建一个`CronNodePublisher`插件，它将在 Cron 运行中使用这个逻辑:

```
namespace Drupal\npq\Plugin\QueueWorker;

/**
 * A Node Publisher that publishes nodes on CRON run.
 *
 * @QueueWorker(
 *   id = "cron_node_publisher",
 *   title = @Translation("Cron Node Publisher"),
 *   cron = {"time" = 10}
 * )
 */
class CronNodePublisher extends NodePublishBase {} 
```

仅此而已。除了基类中已经存在的逻辑，我们不需要任何其他逻辑。注意，在注释中，我们告诉 Drupal Cron 需要使用这个 worker 在 10 秒内处理尽可能多的项目。这是怎么发生的？

每当 Cron 运行时，它使用`QueueWorkerManager`来加载它所有的插件定义。然后，如果他们中的任何一个在注释中有`cron`键，那么就会加载一个与工人 ID 同名的队列**进行处理。最后，队列中的每一项都被工人认领和处理，直到指定的时间过去。**

如果我们现在保存一个未发布的节点，它很可能会在下一次 Cron 运行时发布。

## 体力劳动者

让我们也为手动处理队列创造可能性。首先，让我们修改之前的`hook_entity_insert()`实现，并修改以下代码行:

```
$queue = $queue_factory->get('cron_node_publisher'); 
```

对此:

```
$queue = $queue_factory->get('manual_node_publisher'); 
```

当然，您可以提供一个管理屏幕来配置应用程序应该使用哪种类型的节点发布者。

其次，让我们创建我们的`ManualNodePublisher`插件:

```
namespace Drupal\npq\Plugin\QueueWorker;

/**
 * A Node Publisher that publishes nodes via a manual action triggered by an admin.
 *
 * @QueueWorker(
 *   id = "manual_node_publisher",
 *   title = @Translation("Manual Node Publisher"),
 * )
 */
class ManualNodePublisher extends NodePublishBase {} 
```

这几乎与 CRON 示例相同，但是没有`cron`键。

第三，让我们创建一个表单，在这个表单中，我们可以看到有多少项在`manual_node_publisher`队列中，并通过按一个按钮来处理它们。在模块根文件夹中的`npq.routing.yml`内:

```
demo.form:
  path: '/npq'
  defaults:
    _form: '\Drupal\npq\Form\NodePublisherQueueForm'
    _title: 'Node Publisher'
  requirements:
    _permission: 'administer site configuration' 
```

我们在`/npq`处定义了一个路径，该路径应该使用该名称空间中的指定形式，我们可以这样定义:

```
/**
 * @file
 * Contains \Drupal\npq\Form\NodePublisherQueueForm.
 */

namespace Drupal\npq\Form;

use Drupal\Core\Form\FormBase;
use Drupal\Core\Form\FormStateInterface;
use Drupal\Core\Queue\QueueFactory;
use Drupal\Core\Queue\QueueInterface;
use Drupal\Core\Queue\QueueWorkerInterface;
use Drupal\Core\Queue\QueueWorkerManagerInterface;
use Drupal\Core\Queue\SuspendQueueException;
use Symfony\Component\DependencyInjection\ContainerInterface;

class NodePublisherQueueForm extends FormBase {

  /**
   * @var QueueFactory
   */
  protected $queueFactory;

  /**
   * @var QueueWorkerManagerInterface
   */
  protected $queueManager;

  /**
   * {@inheritdoc}
   */
  public function __construct(QueueFactory $queue, QueueWorkerManagerInterface $queue_manager) {
    $this->queueFactory = $queue;
    $this->queueManager = $queue_manager;
  }

  /**
   * {@inheritdoc}
   */
  public static function create(ContainerInterface $container) {
    return new static(
      $container->get('queue'),
      $container->get('plugin.manager.queue_worker')
    );
  }

  /**
   * {@inheritdoc}.
   */
  public function getFormId() {
    return 'demo_form';
  }

  /**
   * {@inheritdoc}.
   */
  public function buildForm(array $form, FormStateInterface $form_state) {
    /** @var QueueInterface $queue */
    $queue = $this->queueFactory->get('node_publisher');

    $form['help'] = array(
      '#type' => 'markup',
      '#markup' => $this->t('Submitting this form will process the Manual Queue which contains @number items.', array('@number' => $queue->numberOfItems())),
    );
    $form['actions']['#type'] = 'actions';
    $form['actions']['submit'] = array(
      '#type' => 'submit',
      '#value' => $this->t('Process queue'),
      '#button_type' => 'primary',
    );

    return $form;
  }

  /**
   * {@inheritdoc}
   */
  public function submitForm(array &$form, FormStateInterface $form_state) {
    /** @var QueueInterface $queue */
    $queue = $this->queueFactory->get('manual_node_publisher');
    /** @var QueueWorkerInterface $queue_worker */
    $queue_worker = $this->queueManager->createInstance('manual_node_publisher');

    while($item = $queue->claimItem()) {
      try {
        $queue_worker->processItem($item->data);
        $queue->deleteItem($item);
      }
      catch (SuspendQueueException $e) {
        $queue->releaseItem($item);
        break;
      }
      catch (\Exception $e) {
        watchdog_exception('npq', $e);
      }
    }
  }
} 
```

我们再次使用依赖注入来注入`QueueFactory`和`QueueWorker`插件的管理器。在`buildForm()`内部，我们正在创建一个基本的表单结构，并在队列中使用`numberOfItems()`方法来告诉用户他们将要处理多少项。最后，在`submitForm()`方法中，我们负责处理。但是我们怎么做呢？

首先，我们加载队列并实例化一个队列工作器(在这两种情况下，我们都使用`manual_node_publisher` id)。然后我们运行一个`while`循环，直到所有的项目都被处理完。`claimItem()`方法负责阻止一个队列项被另一个队列占用，并将其返回进行处理。在它被工人处理之后，我们删除它。在下一次迭代中，返回下一个项目，如此继续，直到没有项目留下。

虽然我们没有使用它，但是`SuspendQueueException`意味着在处理项目的过程中，工作人员发现了一个问题，这个问题很可能会使队列中的所有其他项目都失败。由于这个原因，继续下一个项目是没有意义的，所以我们打破了循环。然而，我们也释放该项目，以便当我们稍后再次尝试时，该项目是可用的。其他异常也会被捕获并记录到看门狗中。

现在，如果我们创建了几个节点并且没有发布它们，如果我们导航到`/npq`，我们将在消息中看到它们的计数。通过点击提交按钮，我们逐一处理(发布)它们。

这只是一个演示示例。考虑到处理大量项目的潜在负载总是很重要的，要么限制它，使您的请求不会超时，要么使用批处理 API 将它们分成多个请求。

## 结论

在本文中，我们研究了 Drupal 8 中的队列 API。我们已经了解了一些关于它是如何构建和工作的基本概念，但我们也看到了一些如何使用它的例子。也就是说，我们使用了两个用例，通过这两个用例，我们可以在 Cron 运行期间发布未发布的节点，也可以通过用户执行的操作手动发布。

你试过 Drupal 8 中的队列 API 吗？让我们知道进展如何！

## 分享这篇文章