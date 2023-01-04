# October CMS CRUD–构建团队/项目管理插件

> 原文：<https://www.sitepoint.com/octobercms-crud-building-a-teamproject-management-plugin/>

[到目前为止](https://www.sitepoint.com/getting-started-with-october-cms-static-pages/)，我们已经介绍了十月 CMS 的不同方面。这是一篇后续文章，探讨如何将 OctoberCMS 用于 CRUD 应用程序，并详细介绍如何使用模型、关系和控制器。让我们开始吧。

![OctoberCMS logo](img/7698446cdabe74ce83f203af547fbe5b.png)

## 要求

我假设您已经知道如何设置 OctoberCMS 的工作安装。如果没有，可以查看[介绍文章](https://www.sitepoint.com/introducing-octobercms-laravel-based-cms/)，或者阅读[文档](http://octobercms.com/docs/setup/installation)中的安装部分。

## 我们正在建造的东西

我们将建立一个项目管理插件，你可以添加不同的用户到团队，并分配给他们的项目。你可以在 [GitHub](https://github.com/Whyounes/OctoberCMS_Sitepoint_plugin_demo/) 上查看最终结果，并随时建议对插件进行编辑或添加。

## 设置插件

让我们从使用`create:plugin` scaffolding 命令创建初始插件结构开始，并用我们的插件细节定义`Plugin::pluginDetails`方法。

```
php artisan create:plugin rafie.sitepointDemo 
```

```
// Plugin.php

public function pluginDetails()
{
    return [
        'name'        => 'Project management',
        'description' => 'Manage your teams and projects.',
        'author'      => 'RAFIE Younes',
        'icon'        => 'icon-leaf'
    ];
} 
```

## 创建数据库表

每个团队都有一个名字，一个用户和项目列表。

```
php artisan create:model rafie.sitepointdemo Team 
```

```
// models/team.php

class Team extends Model
{
    // ...
    public $table = 'rafie_sitepointDemo_teams';

    public $hasMany = [
        'projects'  => '\Rafie\SitepointDemo\Projects',
        'users'      => '\Backend\Models\User'
    ];

    // ...
} 
```

```
// updates/create_teams_table.php

class CreateTeamsTable extends Migration
{

    public function up()
    {
        Schema::create('rafie_sitepointDemo_teams', function($table)
        {
            $table->engine = 'InnoDB';
            $table->increments('id');
            $table->string('name', 100);
            $table->timestamps();
        });
    }

    public function down()
    {
        Schema::dropIfExists('rafie_sitepointDemo_teams');
    }
} 
```

每个项目都属于一个团队，有名称、描述和结束日期。

```
php artisan create:model rafie.sitepointdemo Project 
```

```
// models/project.php

class Project extends Model
{

    // ...

    public $table = 'rafie_sitepointDemo_projects';

    public $belongsTo = [
        'team' => '\Rafie\SitepointDemo\Models\Team'
    ];

    // ...
} 
```

```
// updates/create_projects_table.php

class CreateProjectsTable extends Migration
{

    public function up()
    {
        Schema::create('rafie_sitepointDemo_projects', function($table)
        {
            $table->engine = 'InnoDB';
            $table->increments('id');
            $table->string('name', 100);
            $table->text('description');
            $table->datetime('ends_at');
            $table->integer('team_id')->unsigned();
            $table->timestamps();
        });
    }

    // ...
} 
```

因为 OctoberCMS 已经有一个用于后端的 users 表，所以我们应该添加`team_id`列。我们将创建一个迁移来添加我们的团队索引。

```
// updates/add_team_to_users.php

class AddTeamToUsers extends Migration
{
    public function up()
    {
        if(!Schema::hasColumn('backend_users', 'team_id'))
        {
            Schema::table('backend_users', function($table)
            {
                $table->integer('team_id')->unsigned()->index()->nullable();
            });
        }
    }

    public function down()
    {
        if(Schema::hasColumn('backend_users', 'team_id'))
        {
            Schema::table('backend_users', function($table)
            {
                $table->dropColumn('team_id');
            });
        }
    }
} 
```

然后，我们需要用一个新的关系定义链接到它。

```
// Plugin.php

class Plugin extends PluginBase
{
    // ...
    public function boot()
    {
        User::extend(function($model){
            $model->belongsTo['team'] = ['Rafie\SitepointDemo\Models\Team'];
        });
    }
} 
```

我们的插件版本文件如下所示:

```
// updates/version.yaml

1.0.1: 
    - First version of Sitepoint demo
    - add_team_to_users.php
    - create_teams_table.php
    - create_projects_table.php 
```

## 管理团队

在您的模型文件夹中，您可以看到每个模型类都有一个包含两个文件的配置文件夹:

*   `columns.yaml`:保存列出表格记录时要使用的表格列。
*   `fields.yaml`:与列一样，但是它用于配置表单以创建和更新记录。

为了管理团队，我们需要创建一个控制器来对某些事件采取行动。我们使用下面的脚手架命令。

```
php artisan create:controller rafie.sitepointDemo Teams 
```

如果您遵循命名约定，控制器将自动映射到模型。如果您在浏览器中查看`backend/rafie/sitepointDemo/teams/create` URL，您会看到新的记录表单。

在`config_form.yaml`中，你会看到`form`和`modelClass`属性被映射到我们的团队模型。新团队表单仅显示 ID 的禁用输入。我们可以使用模型中的`fields.yaml`文件添加其他输入。

```
// models/team/fields.yaml

fields:
    name:
        label: Name
        type: text
        required: true
    users:
        label: Users
        type: checkboxlist 
```

每个团队都有一个名字和一个用户列表。名称是一个简单的文本值，而用户是使用`checkboxlist`组件列出的。您可以在[文档](http://octobercms.com/docs/backend/forms#form-field-options)中查看字段类型列表。

您还可以在字段类型中使用[表单小部件](https://octobercms.com/docs/backend/forms#form-widgets)。小部件是丰富的组件，如 WYSWYG 编辑器、颜色选择器、媒体查找器等。这里剩下的唯一部分是创建一个`Team::getUsersOptions`方法来填充用户复选框列表。

```
// models/team.php

class Team extends Model
{
    // ...
    public function getUsersOptions()
    {
        return \Backend\Models\User::lists('login', 'id');
    }
} 
```

![Create team](img/ee2901b93f52b36a6687f952728b6dcb.png)

从上面的截图可以看到，标记为`required`的字段在输入名称后有一个星号。但是，这并不意味着验证是为您处理的。您仍然需要在您的模型中添加验证。

```
// models/Team.php

class Team extends Model
{
    use \October\Rain\Database\Traits\Validation;

    public $rules = [
        'name' => 'required'
    ];

    // ...
} 
```

如果找到，配置文件中的字段会自动映射到模型。如果没有，我们需要使用`create_onSave`和`update_onSave`方法来改变保存策略。

```
// controllers/teams.php

class Teams extends Controller
{
    // ...
    public function create_onSave()
    {
        $inputs = post('Team');

        // save team
        $teamModel = new \Rafie\SitepointDemo\Models\Team;
        $teamModel->name = $inputs['name'];
        $teamModel->save();

        // update users team_id
        \Backend\Models\User::whereIn('id', $inputs['users'])
                            ->update(['team_id' => $teamModel->id]);

        \Flash::success("Team saved successfully");

        return $this->makeRedirect('update', $teamModel);
    }
} 
```

`post`方法是一个帮助函数，用来避免从容器中解析请求对象。保存团队模型并更新用户关系后，我们显示一条成功消息，并使用`FormController::makeRedirect`方法创建一个重定向响应。

```
// controllers/teams.php

class Teams extends Controller
{
    // ...
    public function update_onSave($recordId)
    {
        $inputs = post('Team');

        // update team
        $teamModel = \Rafie\SitepointDemo\Models\Team::findOrFail($recordId);
        $teamModel->name = $inputs['name'];
        $teamModel->save();

        \Backend\Models\User::where('team_id', $teamModel->id)
                            ->update(['team_id' => 0]);

        // update users team_id
        \Backend\Models\User::whereIn('id', $inputs['users'])
                            ->update(['team_id' => $teamModel->id]);

        \Flash::success("Team updated successfully");
    }
} 
```

`update_onSave`方法有一个包含更新记录 ID 的参数。我们相应地更新团队和附属用户。另一种实现方法是利用`FormController::update_onSave`方法。它负责将表单字段映射到模型并保存它。

```
// controllers/teams.php

class Teams extends Controller
{
    // ...
    public function update_onSave($recordId)
    {
        $inputs = post('Team');

        \Backend\Models\User::where('team_id', $recordId)
                            ->update(['team_id' => 0]);

        // update users team_id
        \Backend\Models\User::whereIn('id', $inputs['users'])
                            ->update(['team_id' => $recordId]);

        $this->asExtension('FormController')->update_onSave($recordId, $context);
    }
} 
```

剩下的唯一部分就是删除记录。您可以使用`update_onDelete`方法重置 users 表上的`team_id`,然后删除团队。

```
// controllers/teams.php

class Teams extends Controller
{
    // ...
    public function update_onDelete($recordId)
    {
        $teamModel = \Rafie\SitepointDemo\Models\Team::findOrFail($recordId);
        \Backend\Models\User::where('team_id', $teamModel->id)
                            ->update(['team_id' => 0]);
        $teamModel->delete();
        \Flash::success("Team deleted successfully");

        return $this->makeRedirect('delete', $teamModel);
    }
} 
```

或者你可以只使用`formAfterDelete`来重置`team_id`。

```
// controllers/teams.php

class Teams extends Controller
{
    // ...
    public function formAfterDelete($model)
    {
        \Backend\Models\User::where('team_id', $model->id)
                            ->update(['team_id' => 0]);
    }
} 
```

如果您注意到了，更新表单不会自动选择附属于团队的用户。我们可能需要使用`Teams`控制器中的`formExtendFields`方法手动完成。`getContext`方法返回用户是创建还是更新模型。

```
// controllers/teams.php

class Teams extends Controller
{
    // ...
    public function formExtendFields($form)
    {
        if( $form->getContext() === 'update')
        {
            $team = $form->model;
            $userField = $form->getField('users');
            $userField->value = $team->users->lists('id');
        }
    }
} 
```

## 管理项目

我们遵循相同的项目管理步骤:我们从定义表单字段开始。

```
models/project/fields.yaml

fields:
    name:
        label: Name
        type: text
        required: true
    description:
        label: Description
        type: textarea
        required: true
    ends_at:
        label: Ends At
        type: datepicker
        required: true
    team_id:
        label: Team
        type: dropdown 
```

我们定义要在团队下拉列表中显示的团队列表。

```
// models/project.php

class Project extends Model
{
    // ...
    public function getTeamIdOptions()
    {
        $teams = \Rafie\SitepointDemo\Models\Team::all(['id', 'name']);
        $teamsOptions = [];

        $teams->each(function($team) use (&$teamsOptions) {
            $teamsOptions[$team->id] = $team->name;
        });

        return $teamsOptions;
    }
} 
```

![Create Project](img/1740dac51e0075f723fd574cb28c60f1.png)

因为所有的表单域都被映射到模型，所以我们不需要挂钩到保存过程来更新一些关系。在这种情况下，`create`、`update`和`delete`动作被自动处理。

## 列表

OctoberCMS 使列表记录变得非常简单和可扩展。您可以显示和隐藏列、搜索、排序、过滤、格式化列值等。查看[文档](https://octobercms.com/docs/backend/lists)以获得选项的完整列表。

### 列出团队

`controllers/teams/config_list.yaml`文件包含我们的列表选项。每个属性都有描述其用法的注释。

```
// controllers/teams/config_list.yaml

# Model List Column configuration
list: $/rafie/sitepointdemo/models/team/columns.yaml

# Model Class name
modelClass: Rafie\SitepointDemo\Models\Team

# List Title
title: Manage Teams

# Link URL for each record
recordUrl: rafie/sitepointdemo/teams/update/:id

# Message to display if the list is empty
noRecordsMessage: backend::lang.list.no_records

# Records to display per page
recordsPerPage: 20

# Displays the list column set up button
showSetup: true

# Displays the sorting link on each column
showSorting: true

//... 
```

您可以看到,`list`属性指向了`columns.yaml`文件，该文件定义了应该显示的列。`showSetup`选项让用户选择在列表中显示哪些列。

![List Setup](img/835a7b460b85925d497320760f7afe07.png)

```
// models/team/columns.yaml

columns:
    id:
        label: ID
        searchable: true
    name:
        label: Name
    users:
        label: Users
        relation: users
        select: login
        searchable: false 
```

`id`和`name`属性是不言自明的。默认情况下，`searchable`属性被设置为`true`，这就是为什么我们没有在`name`属性中指定它。

OctoberCMS 有一个`relation`属性，可以用来显示相关模型的值。在这种情况下，我们从`users`关系模型中选择`login`属性。您可以查看[文档](https://octobercms.com/docs/backend/lists#column-options),了解可用色谱柱选项的完整列表。

![Teams List](img/1ea099afff57d3ec47f076666b3522ce.png)

### 列出项目

我们按照相同的步骤列出项目:我们有一个名称，一个描述，一个结束日期和一个团队。

```
// models/project/columns.yaml

columns:
    id:
        label: ID
        searchable: true
    name:
        label: Name
    description:
        label: Description
        type: text
    ends_at:
        label: End At
        type: datetime
    team:
        label: Team
        relation: team
        select: name 
```

为了帮助在我们的列表中正确格式化结束日期，我们将列类型指定为`datetime`。这可能会抛出一个异常，因为您需要在模型的`$dates`数组中添加`ends_at`属性。查看[文档](https://octobercms.com/docs/backend/lists#column-types)中可用列类型的完整列表。

```
// models/project.php

class Project extends Model
{
    // ...
    protected $dates = ['ends_at'];
    // ...
} 
```

对于团队列，我们指定关系类型，并从模型中选择 name 属性。最后的结果看起来是这样的。

![Projects listing](img/e12796592e5be99d8576127c7d59e131.png)

### 扩展列表

如果您想改变列表行为，您可以覆盖控制器和`index.htm`视图中的`index`方法。我们现在要做的是截断描述列。你可以查看[文档](https://octobercms.com/docs/backend/lists#overriding-action)以获得更多关于扩展列表行为的细节。

```
// controllers/projects.php

class Projects extends Controller
{
    // ...
    public function listOverrideColumnValue($record, $columnName)
    {
        if( $columnName == "description" && strlen($record->description) > 20 )
        {
            $description = substr($record->description, 0, 20);

            return "<span title='{$record->description}'>{$description}...</span>";
        }
    }
} 
```

您可能会考虑扩展用户列表来显示他们当前的团队。我们在插件定义文件中使用`boot`方法来扩展其他插件。

```
// Plugin.php

class Plugin extends PluginBase
{
    // ...
    public function boot()
    {
        // ...

        \Backend\Controllers\Users::extendListColumns(function ($list) {
            $list->addColumns([
                'team' => [
                    'label' => 'Team',
                    'relation' => 'team',
                    'select' => 'name'
                ]
            ]);
        });
    }
} 
```

![Listing Users](img/d9b62c5e35e8784d4e3edab6750da2a5.png)

### 过滤

在 OctoberCMS 中过滤列表很容易。首先，在`config_list.yaml`文件中引用过滤器配置文件。

```
// controllers/projects/config_list.yaml

// ...
filter: config_filter.yaml
// ... 
```

在您的`config_filter.yaml`文件中，您定义了一个您想要使用的作用域列表。

```
// controllers/projects/config_filter.yaml

scopes:
    team:
        label: Team
        modelClass: \Rafie\SitepointDemo\Models\Team
        nameFrom: name
        conditions: team_id = :filtered 
```

我们的范围被命名为`team`，并将使用指定的`modelClass`和`nameFrom`属性列出我们可用的团队。条件将筛选出`team_id`等于所选团队的项目；您可能会认为它是一个原始的 SQL where 语句。下面的截图显示了`Backend`团队拍摄的项目列表。

![Team filtered projects](img/da4901147367875518d185aa4d271723.png)

OctoberCMS 有两种作用域类型。第一个是组类型，这是我们之前使用的类型。第二个是复选框类型，用于布尔情况。我们将使用后者来隐藏列表中的过期项目。

```
// controllers/projects/config_filter.yaml

scopes:
    // ...
    hide_past_due:
        label: Hide past due
        type: checkbox
        conditions: ends_at > now() 
```

列表中唯一过期的项目是 id 为`2`的项目。

![Past due filtered projects](img/a3ece3d00eb20bfc5ff59d6532f99148.png)

## 许可

如果不涉及权限和如何保护你的控制器，我们就不能谈论 CRUD 操作。结合 Laravel 的简单性，OctoberCMS 允许你为你的插件定义一个权限列表，并将它们分组到一个特定的标签下，然后用它们来保护你的控制器。

```
// Plugin.php

class Plugin extends PluginBase
{
    // ...
    public function registerPermissions()
    {
        return [
            'rafie.sitepointDemo.manage_teams' => [
                'label' => 'Manage Teams',
                'tab' => 'SitepointDemo'
            ],
            'rafie.sitepointDemo.manage_projects' => [
                'label' => 'Manage Projects',
                'tab' => 'SitepointDemo'
            ]
        ];
    }
} 
```

如果您访问更新或创建用户页面并选择 permissions 选项卡，您将看到保存插件权限的`SitepointDemo`选项卡。

![User Permissions](img/38f71cb9c649cbca935a1ee4f7e83109.png)

现在，在项目和团队控制器中，您添加了`$requiredPermission`属性。

```
// controllers/teams.php

class Teams extends Controller
{
    // ...
    public $requiredPermissions = ['rafie.sitepointDemo.manage_teams'];
    // ...
} 
```

```
// controllers/projects.php

class Projects extends Controller
{
    // ...
    public $requiredPermissions = ['rafie.sitepointDemo.manage_projects'];
    // ...
} 
```

如果您想限制对控制器内部某个动作的访问，您可以使用`User::hasAccess`和`User::hasPermissions`方法。查看文档了解更多关于[权限](https://octobercms.com/docs/backend/users#features)的细节。

```
// controllers/teams.php

class Teams extends Controller
{
    // ...
    public function update()
    {
        if( !$this->user->hasPermissions(['rafie.sitepointDemo.update_teams']) )
        {
            // redirect Unauthorized 401
        }
    }
} 
```

## 结论

每个 CMS 都试图让 CRUD 操作对新手来说更容易、更直接，我认为 OctoberCMS 通过使它的每个方面都清晰和可扩展，成功地实现了这个目标。

你可以在 GitHub 上查看最终结果，如果你有任何问题或意见，请在评论中告诉我！

## 分享这篇文章