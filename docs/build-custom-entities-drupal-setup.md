# 在 Drupal-Setup 中构建您自己的定制实体

> 原文：<https://www.sitepoint.com/build-custom-entities-drupal-setup/>

实体是在 Drupal 中组织数据的一种很好的方式。如果您熟悉节点、分类术语、注释或用户，您也应该知道从 Drupal 7 开始，这些都是实体。关于它们的另一个重要方面是，它们可以通过 Field API*字段化*。

在本教程中，我将向您展示如何定义您自己的自定义实体类型，并开始使用它。为什么要这样做而不是使用节点呢？因为尽管它们很棒，但节点有时可能是多余的。有很多功能你可能不需要，比如修改或者评论。

出于本教程的目的，我们将定义我们自己的自定义实体类型`project`来表示我们关于项目的简单信息(标题、描述和截止日期)。然后我们将看一些关于使用这种类型的实体的事情。

为了继续学习，我假设您知道如何编写一个基本的定制模块(我的将被命名为`demo`)。你可以在这里找到关于这个主题的很棒的教程[。继续编写`.info`文件，并创建空的`.module`和`.install`文件。我还建立了一个 Git 存储库](https://www.sitepoint.com/building-drupal-7-module-show-latest-nodes/)，在这里你可以获得本教程的所有源代码(每个部分一个分支，将会发布两个部分)。

此外，您需要在您的站点上启用[实体 API](https://drupal.org/project/entity) contrib 模块，并将其设置为自定义模块的依赖项。实体 API 模块在处理实体时非常强大，因为它提供了 Drupal 核心所缺乏的许多功能。

## 定义我们自己的 Drupal 实体类型

要创建一个新的实体类型，我们需要做的第一件事是声明它的模式定义。也就是说，编写将为实体数据生成数据库表的代码。在我的`demo.install`文件中，我有以下代码:

```
/**
 * Implements hook_schema().
 */
function demo_schema() {

  $schema = array();

  $schema['demo_projects'] = array(
    'description' => 'The base table for the Project entity',
    'fields' => array(
      'id' => array(
        'description' => 'Primary key of the Project entity',
        'type' => 'serial',
        'unsigned' => TRUE,
        'not null' => TRUE,
      ),
      'name' => array(
        'description' => 'Project name.',
        'type' => 'varchar',
        'length' => 255,
        'not null' => FALSE,
      ),
      'description' => array(
        'description' => 'Project description.',
        'type' => 'text',
        'size' => 'big',
        'not null' => FALSE,
        'default' => NULL
      ),
      'deadline' => array(
        'description' => 'Project deadline.',
        'type' => 'int',
        'length' => 11,
        'not null' => FALSE,
      ),
    ),
    'primary key' => array('id'),
  );

  return $schema;
} 
```

这是对 [hook_schema()](https://api.drupal.org/api/drupal/modules%21system%21system.api.php/function/hook_schema/7) 的简单实现，通过它我们创建了一个`demo_projects`表，该表有 4 列:id、名称、描述和截止日期，第一列表示主键。没什么大事。

接下来我们需要做的是实现 [hook_entity_info()](https://api.drupal.org/api/drupal/modules%21system%21system.api.php/function/hook_entity_info/7) 。我们可以在这个钩子中指定很多选项，但这里是最基本和必需的选项(放在`demo.module`文件中):

```
/**
 * Implements hook_entity_info().
 */
function demo_entity_info() {

  $info = array();

  $info['project'] = array(
    'label' => t('Project'),
    'base table' => 'demo_projects',
    'entity keys' => array(
      'id' => 'id',
      'label' => 'name',
    ),
    'module' => 'demo',
  );

  return $info;
} 
```

通过这个钩子，我们返回了一个新的键，该键位于代表实体机器名称的数组`$info`中。在这个数组中，我们指定了选项(我们将在本教程中添加更多选项)。现在，我们将坚持使用`label`(实体类型的人类可读名称)、`base table`存储实体数据、`entity keys`作为实体标识符的属性以及`module`指定哪个模块定义实体类型。最后一个不是强制的，但是推荐。

这样我们就向 Drupal 注册了自己的基本实体类型。为了测试它是否工作，第一次启用该模块，并检查该表是否是在数据库中创建的。然后用几行数据填充它，以便进行处理:

```
INSERT INTO `demo_projects` (`id`, `name`, `description`, `deadline`)
VALUES
    (1, 'Summer House', 'Lorem ipsum dolor sit amet, consectetur adipisicing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua. Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo consequat.', 1397501105),
    (2, 'Winter House', 'Lorem ipsum dolor sit amet, consectetur adipisicing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua. Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo consequat.', 1397501132); 
```

最后，使用 [hook_menu()](https://api.drupal.org/api/drupal/modules%21system%21system.api.php/function/hook_menu/7) 向 Drupal 注册一个路径(仅用于测试的任何路径),并在其回调函数中粘贴以下内容:

```
$projects = entity_load('project', array(1, 2));
dpm($projects);
return 'Some string'; 
```

首先，我们使用 [entity_load()](https://api.drupal.org/api/drupal/includes%21common.inc/function/entity_load/7) 函数加载 id 为 1 和 2 的项目实体，然后我们使用 Devel `dpm()`函数将它们打印到屏幕上(因此确保在您的站点上启用了 [Devel](https://drupal.org/project/devel) 进行测试)。不要忘记页面的回调函数需要返回一些东西，否则它就不会构建。

现在，如果您导航到该页面，您将在 Krumo 中看到来自数据库中 2 个实体的数据。

或者，您可以使用 [EntityFieldQuery](https://api.drupal.org/api/drupal/includes!entity.inc/class/EntityFieldQuery/7) 类通过您想要的任何属性(不仅仅是 id)来查询新实体。关于这是如何工作的更多信息，你可以查看这个 [Sitepoint 教程](https://www.sitepoint.com/understanding-drupals-entityfieldquery/)，它将带你开始。

## 实体类和控制器

不幸的是，Drupal core 没有提供太多的助手函数来处理实体(`entity_load()`几乎是唯一的一个)。然而，实体 API 模块填补了这一空白。

为了利用它的功能，我们需要改变我们之前声明的实体信息，并指定可以用来处理实体的 PHP 类。现在，我们将在我们的`hook_entity_info()`声明中向数组 keyed `project`添加另外两个键:

```
...

'entity class' => 'Entity',
'controller class' => 'EntityAPIController',

... 
```

第一个是由实体 API 提供的基类，它将为实体提供一些包装功能。这个类是在模块的`entity.inc`文件中声明的，如果你仔细看，你会注意到它的许多方法调用另一个(控制器)类的方法。这是我们为`controller class`键指定的类。

`EntityAPIController`类(在模块的`entity.controller.inc`文件中)提供了一些合理的缺省值来处理实体。它扩展了默认的 Drupal 核心`DrupalDefaultEntityController`类，并负责执行 CRUD 操作。

这两个类都可以在您的定制模块中扩展以调整功能(如查询、加载或显示实体)。我们一会儿将看到如何做到这一点。

但是首先，我想告诉你如何保存一个新的实体。目前，在我的数据库中，我有 2 个 id 为 1 和 2 的记录。我想调整我们在上面的测试页面回调中编写的代码，以创建一个 id 为 3 的新实体，如果它还不存在的话。它可能看起来像这样:

```
 $projects = entity_load('project', array(1, 2, 3));

 if (!isset($projects[3])) {
   $entity = entity_create('project', array('id' => 3));
   $entity->name = t('Spring House');
   $entity->description = t('Some more lipsum.');
   $entity->save();
 }

 dpm($projects);

 return 'Some string'; 
```

如您所见，现在我们尝试加载 3 个项目实体，并检查第三个实体是否存在。如果它不存在，我们使用实体 API 提供的`entity_create()`辅助函数，将属性设置为一些随机值，然后使用实体上的`save()`方法将它保存到数据库中。这个方法是由`Entity`类提供的，它的工作是在我们上面定义的控制器类上调用`save()`方法。该方法将执行持久保存该实体所必需逻辑。但这一切都发生在幕后，我们不必担心。

如果您重新加载该页面，您应该只看到 2 个返回的项目实体，但是如果第二次加载，应该有 3 个。

## 覆盖实体类

在这部分教程中，我想告诉你的最后一件事是如何显示你的实体。为此，我们将坚持使用我们一直在使用的页面回调函数，并让它呈现我们的实体列表。

我们需要做的第一件事是覆盖默认`EntityAPIController`类的`buildContent()`方法。原因是控制器不能对我们的数据做出假设，所以我们需要提供一些关于如何显示数据的信息。首先，让我们声明我们的控制器类，它扩展了前面的类:

```
/**
 * Extending the EntityAPIController for the Project entity.
 */
class ProjectEntityController extends EntityAPIController {

} 
```

我选择了类名`ProjectEntityController`，您需要确保用这个名称替换您在`hook_entity_info()`实现中为`controller class`键设置的值。别忘了。

在这个类中，我们可以从原始方法中复制方法名，并让它返回与其父方法相同的结果:

```
public function buildContent($entity, $view_mode = 'full', $langcode = NULL, $content = array()) {

$build = parent::buildContent($entity, $view_mode, $langcode, $content);

// Our additions to the $build render array

return $build;

} 
```

因此，没有新的变化。但是现在我们可以将我们自己的数据添加到这个方法的返回值中，这个方法只不过是一个 Drupal 呈现数组。例如，我们可以在返回`$build`数组之前写这个:

```
$build['description'] = array(
  '#type' => 'markup',
  '#markup' => check_plain($entity->description),
  '#prefix' => '<div class="project-description">',
  '#suffix' => '</div>',
);
$build['deadline'] = array(
  '#type' => 'markup',
  '#markup' => date('d F, Y', check_plain($entity->deadline)),
  '#prefix' => '<p>Deadline: ',
  '#suffix' => '</p>',
); 
```

我们基本上向数组中添加了两个新项目。第一个将使用`<div class="project-description">`包装描述，第二个将在段落标记之间输出格式化的日期。这是基本的 Drupal 主题化，所以如果你不明白这里发生了什么，请温习一下。但是您会注意到项目名称丢失了。这将由 Drupal 自动呈现，因为我们在`hook_entity_info()`实现的`entity keys`中将它指定为`label`。

最后一步是转到我们的页面回调函数，并让它显示我们的实体。一种快速的方法(仅用于演示目的):

```
$projects = entity_load('project', array(1, 2, 3));

$list = entity_view('project', $projects);

$output = array();

foreach ($list['project'] as $project) {
  $output[] = drupal_render($project);
}

return implode($output); 
```

和以前一样，我们首先用各自的 id 加载我们的实体。然后，我们通过`entity_view()`助手函数运行它们，该函数将最终调用我们刚刚覆盖的`buildContent()`方法。该函数返回每个实体的渲染数组列表。我们渲染每一个并将结果存储在`$output`数组中，然后我们进行内爆并返回。

您可以刷新页面，应该会看到您加载的所有实体的列表。确保您清除了缓存，以便更改变得可见。

## 结论

在本教程中，我们通过在代码中定义我们自己的实体类型开始学习 Drupal 中的实体。我们已经看到了如何为它们表示的数据编写模式定义，以及如何向 Drupal 注册它们。然后，我们看到了使用实体 API contrib 模块以面向对象的方式处理实体的强大功能。

在本教程的第二部分，我们将着眼于三个主要方面。首先，我们将创建一些页面来显示各个项目实体，并创建一个管理界面来管理项目。其次，我们将通过 UI 使它们可字段化。第三，我们将把它们展示给视图，这样我们就可以做一些适当的查询和列表。敬请期待！

## 分享这篇文章