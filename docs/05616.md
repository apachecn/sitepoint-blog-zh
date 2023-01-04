# 在 Drupal 中构建自己的定制实体——实现

> 原文：<https://www.sitepoint.com/build-custom-drupal-entities/>

欢迎回到本教程的第二部分，我们将探索 Drupal 中的定制实体世界。如果你还没有，我强烈建议你阅读第一部分，但是让我们做一个简短的回顾。

在前一篇文章中，我们已经为我们的实体类型定义了模式，并向 Drupal 注册了它。我们还覆盖了`EntityAPIController`来为我们的实体构建显示。

在教程的这一部分，我们将继续讨论一些我们可以用 Drupal 中的实体做的很酷的事情。首先，我们将快速设置页面，在这里我们可以显示单个项目实体。接下来，我们将构建一个简单但非常强大的管理界面来管理它们。然后，我们将使我们的实体类型可字段化，这样我们就可以通过 UI 添加字段。最后，我们将向视图公开它，这样我们就可以创建项目实体的适当列表。

如果你愿意，你可以跟随来自第一个分支 [Git 库](https://bitbucket.org/upchuk/entities-demo)的源代码，或者看一眼第二个分支，它包含了我们今天要讨论的所有代码。

## 单个实体页面

我们要做的第一件事是创建显示单个项目实体的页面。我们将从向我们的`hook_menu()`实现添加一个新项目开始:

```
 $items['project/%'] = array(
    'title' => 'Project',
    'page callback' => 'demo_view_project',
    'page arguments' => array(1),
    'access arguments' => array('access content'),
  ); 
```

我们注册了一个路径(`project/id`)和一个回调函数(`demo_view_project()`)，并向其传递通配符 URL 参数(项目的 ID)。至于访问权限，任何拥有`access content`权限的人都可以看到这个页面。

接下来，让我们编写上述回调函数(记住，这只是一个简单的示例，仅用于演示目的):

```
/**
 * Callback function for displaying the individual project page
 */
function demo_view_project($id) {

  $projects = entity_load('project', array($id));
  $project = $projects[$id];

  drupal_set_title($project->name);
  $output = entity_view('project', array($project));

  return $output;

} 
```

这也非常简单:我们加载带有从 URL 传递的 ID 的实体，我们设置页面的标题，通过`entity_view()`运行实体对象，并将其作为页面输出返回。当我们列出我们的项目时，我们已经涵盖了这些实体 API 概念。现在您可以清空缓存并导航到`project/1`，您应该会看到 ID 为 1 的项目。如果您看到项目名称两次，不要担心，在下一节中，当我们让 Drupal 知道哪个是项目实体的默认 URI 回调时，这将变得很清楚。

## 管理员用户界面

现在我们可以显示单个实体了，让我们利用实体 API 模块的能力来建立一个快速管理用户界面来管理它们。为此，我们需要采取一些简单的步骤。

首先，让我们为我们的实体类型编辑我们的`hook_entity_info()`实现，并添加以下内容(我将在后面解释):

```
...

'access callback' => 'demo_access_callback',
'uri callback' => 'entity_class_uri',
'admin ui' => array(
  'path' => 'admin/projects',
  'controller class' => 'EntityDefaultUIController',
),

... 
```

替换这一行:

```
'entity class' => 'Entity', 
```

有了这个:

```
'entity class' => 'ProjectEntity', 
```

通过这些修改，我们做了 4 件事:

1.  我们为实体类型指定一个访问回调函数。我们将需要这个用于管理用户界面，我们将在一分钟内声明回调函数。
2.  我们将`uri callback`设置为由实体类提供的缺省值(我将在第 4 点回到这个问题)。
3.  我们设置了`admin ui`信息:UI 页面的路径和将处理它的控制器类。`EntityDefaultUIController`是实体 API 附带的默认 UI 类，它在`entity.ui.inc`文件中声明。
4.  我们将该实体类型的实体类的名称改为一个尚不存在的名称。我们现在将通过扩展前面的方法来创建它，这样我们就可以覆盖它的`defaultUri()`方法:

    ```
    /**
     * Project entity class extending the Entity class
     */
    class ProjectEntity extends Entity {

      /**
       * Change the default URI from default/id to project/id
       */
      protected function defaultUri() {
        return array('path' => 'project/' . $this->identifier());
      }

    } 
    ```

正如你所看到的，我们基本上改变了这个类方法返回的单个项目实体的路径。到时候，我会指出为什么这是方便的，但这将是我在第 2 点提到的默认设置。现在，让我们快速声明我们在第 1 点提到的访问回调函数:

```
/**
 * Access callback for project entities.
 */
function demo_access_callback($op, $project = NULL, $account = NULL) {
  if ($op == 'view' || $op == 'update' || $op == 'create' || $op == 'delete') {
    return TRUE;
  }
  else {
    return FALSE;
  }
} 
```

正如您所看到的，这不是一个很好的访问回调函数，因为它对所有内容都返回 true。在这里，您通常会执行适当的访问检查，但对于我们的演示目的来说，它工作得很好。

现在，为了使用我们的管理界面，我们还需要做最后一件事:为项目实体及其提交处理程序声明一个简单的添加/编辑表单:

```
/**
 * Form definition for adding / editing a project.
 */
function project_form($form, &$form_state, $project = NULL) {

  $form['name'] = array(
    '#title' => t('Project name'),
    '#type' => 'textfield',
    '#default_value' => isset($project->name) ? $project->name : '',
    '#required' => TRUE,
  );

  $form['description'] = array(
    '#title' => t('Project description'),
    '#type' => 'textarea',
    '#default_value' => isset($project->description) ? $project->description : '',
    '#required' => TRUE,
  );

  $form['deadline'] = array(
    '#title' => t('Project deadline'),
    '#type' => 'textfield',
    '#default_value' => isset($project->deadline) ? $project->deadline : '',
    '#required' => TRUE,
  );  

  $form['submit'] = array(
    '#type' => 'submit', 
    '#value' => isset($project->id) ? t('Update project') : t('Save project'),
    '#weight' => 50,
  );

  return $form;
}

/**
 * Submit handler for the project add/edit form.
 */
function project_form_submit($form, &$form_state) {
  $project = entity_ui_form_submit_build_entity($form, $form_state);
  $project->save();
  drupal_set_message(t('The project: @name has been saved.', array('@name' => $project->name)));
  $form_state['redirect'] = 'admin/projects';
} 
```

对于`deadline`字段，我使用了一个简单的文本字段，只是为了让演示更快。通常，您会希望为一个漂亮的小部件使用 contrib [Date](https://drupal.org/project/date) 模块，然后将该值保存为时间戳。但是这对于我们的例子来说已经足够了。

至于其他的，没什么大事。不过，有几件事你应该记住。为了让实体 API 自动识别这是项目实体的形式，声明的函数名需要准确无误。如果实体类型被称为`chocolate`，那么函数名应该是`chocolate_form()`。您还会注意到，我们将`$project`实体作为第三个参数传递。这将由编辑表单的实体 API 来处理。最后，在提交处理程序中，您会注意到 entity _ ui _ form _ submit _ build _ entity()函数的使用。这是一个很好的助手，它将从`$form_state`数组(用户输入)中获取所有值，并用它们填充实体属性。

仅此而已。我们可以清除缓存并导航到`http://example.com/admin/projects`，在那里我们应该看到一个项目实体的表格，带有查看、编辑和删除它们的链接+一个用于添加新实体的附加链接。

前面我提到过，我会让你知道为什么我们必须覆盖`Entity`类并改变实体类型的默认 URI。Drupal 放在项目名称后面的链接是基于这些信息生成的。因此，只要路径存在，您现在就应该有一个完整的 CRUD 系统+页面来显示各个实体。还不错。

## 菲尔茨

接下来我们将看到如何使我们的实体可字段化。让我们再次编辑`hook_entity_info()`实现，并在那里添加更多的信息:

```
...

'fieldable' => TRUE,
'bundles' => array(
  'project' => array(
    'label' => t('Project'),
    'admin' => array(
      'path' => 'admin/projects',
    ),
  ),
),

... 
```

在第一行中，我们告诉 Drupal 这个实体类型应该使用字段，在接下来的几行中，我们定义了一个可以附加字段的包。捆绑是我们在 Drupal 中附加到字段上的，任何实体类型都可以有多个捆绑。例如，在全新的 Drupal 安装中，节点实体拥有文章和页面包(内容类型)。

我们定义一个单独的，叫做`project`并指定一些基本信息。现在，如果您清空缓存并转到`admin/projects`，您应该会在页面顶部看到两个新的选项卡，类似于您向常规内容类型添加字段时看到的内容。现在我们可以向我们的实体添加字段。

您还会注意到，在您通过 UI 添加一些字段并尝试编辑或添加项目之后，您在表单中看不到这些字段。那是因为我们需要把他们包括进来。编辑我们之前编写的`project_form()`函数，就在 submit 元素之前，粘贴以下内容:

```
field_attach_form('project', $project, $form, $form_state); 
```

使用字段 API [field_attach_form()](https://api.drupal.org/api/drupal/modules%21field%21field.attach.inc/function/field_attach_form/7) ，我们将所有通过 UI 声明的必要元素附加到表单上。现在再次进入表单，您应该会看到新的字段。甚至在单个项目实体显示页面上，当您通过 UI 配置字段值时，它们也会被呈现出来。

## 向视图公开实体类型

我们要看的最后一件事是如何向视图展示我们的项目实体。在这方面，我们可以做的最基本的事情是编辑`hook_entity_info()`实现，并指定另一个控制器类来处理该功能:

```
...

'views controller class' => 'EntityDefaultViewsController',

... 
```

现在，您可以清除缓存并创建一个新视图。确保您选择了`Project`而不是`Content`,您将拥有所有可用的实体属性。需要注意的一点是，视图不能解释我们存储在那些属性中的值的种类。例如，`deadline`字段实际上是一个日期，而不是一个简单的整数。因此，如果您添加该字段，它会将其视为一个简单的数值。

为了解决这个问题，我们需要实现`hook_entity_property_info()`并准确指定项目属性存储的数据类型:

```
/**
 * Implements hook_entity_property_info().
 */
function demo_entity_property_info() {

  $info = array();

  $info['project']['properties']['id'] = array(
    'label' => t('Project ID'),
    'description' => t('The ID of the project.'),
    'type' => 'integer',
    'schema field' => 'id',
  );

  $info['project']['properties']['name'] = array(
    'label' => t('Project name'),
    'description' => t('Name of the project.'),
    'type' => 'text',
    'schema field' => 'name',
  );
  $info['project']['properties']['description'] = array(
    'label' => t('Project description'),
    'description' => t('Description of the project.'),
    'type' => 'text',
    'schema field' => 'description',
  );
  $info['project']['properties']['deadline'] = array(
    'label' => t('Deadline'),
    'description' => t('Project deadline.'),
    'type' => 'date',
    'schema field' => 'deadline',
  );

  return $info;
} 
```

正如您所看到的，这是一个大的嵌套数组，对于`project`实体，我们将其属性映射到它们的模式列，并指定在那里找到什么类型的数据。现在，如果您清除缓存并将这些字段添加到视图中，视图将知道属性中存储了什么值。对于`deadline`字段，这次它将使用它的日期处理程序，并将时间戳转换为一个日期字符串。

在实现这个钩子时要记住一件重要的事情:你不能只为一个属性这样做。一旦您实现并描述了您的一个属性，您还需要添加其余的。否则视图将不再显示其余部分。

## 结论

我们已经到达了探索 Drupal 实体的终点。尽管我们已经讨论了很多，但是您还可以做更多的事情来完善和定制您的实体类型。在这一部分中，我们已经看到了如何与视图集成，如何向我们的实体添加字段，甚至如何为它们创建一个管理界面。但是您还可以做更多的事情:查看模式、修改等。

我们已经编写了一些示例代码，并创建了一些非常棒的功能。是的，从一个严格的“男孩这是一个漫长的阅读”的角度来看，这是非常激烈和复杂的。但是如果你后退一步，想想我们用我向你保证的几乎没有代码所取得的成就，你就会理解为什么 Drupal 实体系统如此伟大。

## 分享这篇文章