# 狂热:类固醇上的 Laravel 模型

> 原文：<https://www.sitepoint.com/ardent-laravel-models-steroids/>

我不喜欢 Laravel 的一点是，你不能轻易地将验证代码从你的控制器转移到你的模型上。当我写软件时，我喜欢应用“胖模型，瘦控制器”的原则。所以，对我来说，在控制器里写验证代码并不是一件好事。

为了解决这个问题，我想介绍一下 Laravel 4 的一个很棒的包**Arden**。更准确地说，[Arden](https://github.com/laravelbook/ardent)将自己描述为“Laravel Framework 4 的雄辩 ORM 的自我验证智能模型”。“换句话说；正是我们所需要的！

正如你所想象的，它基本上是雄辩类的扩展。这个包附带了一些新的功能、工具和方法，专门用于输入验证和其他小事情。

## 我们的测试应用程序

为了更好地了解您在使用 Ardent 时可以享受的优势，我们将设置一个小的测试应用程序。没什么复杂的:一个简单的待办事项应用程序。

当然，我不是要实现一个完整的应用:我只是想解释一些原理，所以我会做一些控制器和模型——没有视图。之后，我将使用 Ardent“翻译”代码。

我们的待办事项列表将计算两种不同的实体:

*   **用户**

    *   身份证明（identification）
    *   名字
    *   姓氏
    *   电子邮件
    *   密码
*   **任务**

    *   身份证明（identification）
    *   名字
    *   状态(完成/未完成)

一个非常基础的项目。但是，如果您不想写代码，不要担心:我已经准备了一个迁移，您可以用它来生成数据库。用它！

使用命令创建迁移文件

```
php artisan migrate:make todo_setup
```

然后，用以下代码填充该文件:

```
<?php

    use Illuminate\Database\Schema\Blueprint;
    use Illuminate\Database\Migrations\Migration;

    class TodoSetup extends Migration {

        /**
         * Run the migrations.
         *
         * @return void
         */
        public function up()
        {
            Schema::create('users', function(Blueprint $table)
            {
                $table->increments('id')->unsigned();

                $table->string('first_name');
                $table->string('last_name');
                $table->string('email');
                $table->string('password', 60);

                $table->timestamps();
            });

            Schema::create('tasks', function(Blueprint $table)
            {
                $table->increments('id');

                $table->string('name');
                $table->boolean('status');

                $table->integer('user_id')->unsigned();

                $table->timestamps();

                $table->index('user_id');
            });
        }

        /**
         * Reverse the migrations.
         *
         * @return void
         */
        public function down()
        {
            Schema::dropIfExists('users');
            Schema::dropIfExists('tasks');
        }

    }
```

现在我们有桌子了。是时候创建我们的模型了。即使在这里，我们也只有很少的几行可以写。这里是`User`型号(也是默认型号)。

```
<?php

    use Illuminate\Auth\UserTrait;
    use Illuminate\Auth\UserInterface;
    use Illuminate\Auth\Reminders\RemindableTrait;
    use Illuminate\Auth\Reminders\RemindableInterface;

    class User extends Eloquent implements UserInterface, RemindableInterface {

        use UserTrait, RemindableTrait;

        /**
         * The database table used by the model.
         *
         * @var string
         */
        protected $table = 'users';

        /**
         * The attributes excluded from the model's JSON form.
         *
         * @var array
         */
        protected $hidden = array('password', 'remember_token');

        public function tasks()
        {
            return $this->hasMany('Task');
        }

    }
```

我只是添加了`tasks`方法来描述与`Task`模型的关系。

```
<?php

    class Task extends \Eloquent {
        protected $fillable = [];

        public function user()
        {
            return $this->belongsTo('User');
        }
    }
```

我们刚刚到达起点。从现在开始，安装完成后，我们将看到两种不同的情况:一是“正常”版本的代码，没有热情。紧接着，我们将与“改进”版本进行比较。你会注意到不同的，相信我。

开始吧！

## 安装 Ardent

使用 Composer 安装 Ardent 非常容易。只需将依赖项添加到项目的`composer.json`文件中。

```
{
        "require": {
            "laravelbook/ardent": "2.*"
        }
    }
```

然后，在更新之后，您只需在您的模型中扩展`Ardent`类，如下所示:

```
<?php
    class User extends \LaravelBook\Ardent\Ardent {
        // model code here!
    }
```

…您已经准备好了！

## 数据有效性

要做的第一件事是分析在实现验证时，热心是如何让我们的生活变得更容易的。我们已经知道如何用拉勒维尔做到这一点。让我们举一个经典的例子:POST 方法将处理来自表单的数据。

在“正常”情况下，我们会做出这样的事情:

```
<?php

    public function postSignup()
    {
        $rules = array(
            'first_name' => 'required',
            'last_name' => 'required',
            'email' => 'required|email|unique:users',
            'password' => 'required|min:8'
        );

        $messages = array(
            'first_name.required' => 'First name is required.',
            'last_name.required' => 'Last name is required.',
            'email.required' => 'Email is required.',
            'password.required' => 'Password is required.',

            'email.email' => 'Use a real email address!',
            'email.unique' => 'This email address already exists!',
            'password.min' => 'Password must be at least 8 character long.'
        );

        $validator = Validator::make(Input::all(), $rules, $messages);

        if($validator->fails())
        {
            return Redirect::to('user/signup')->with('errors', $validator->messages());
        }

        $user = new User;

        $user->first_name = Input::get('first_name');
        $user->last_name = Input::get('last_name');
        $user->email = Input::get('email');
        $user->password = Hash::make(Input::get('password'));

        if($user->save())
        {
            $status = 1;
        }
        else
        {
            $status = 0;
        }

        return Redirect::to('user/signup')->with('status', $status);
    }
```

……那么“热情”呢？

有了热心，事情会有一点点改变。首先，你可以很容易地想象，验证规则将被直接移到模型中:我们将从这里开始我们的“重新设计”。因此，打开模型文件，像这样修改它:

```
<?php

    use Illuminate\Auth\UserTrait;
    use Illuminate\Auth\UserInterface;
    use Illuminate\Auth\Reminders\RemindableTrait;
    use Illuminate\Auth\Reminders\RemindableInterface;

    class User extends \LaravelBook\Ardent\Ardent implements UserInterface, RemindableInterface {

        public static $rules = array(
            'first_name' => 'required',
            'last_name' => 'required',
            'email' => 'required|email|unique:users',
            'password' => 'required|min:8'
        );

        public static $customMessages = array(
            'first_name.required' => 'First name is required.',
            'last_name.required' => 'Last name is required.',
            'email.required' => 'Email is required.',
            'password.required' => 'Password is required.',

            'email.email' => 'Use a real email address!',
            'email.unique' => 'This email address already exists!',
            'password.min' => 'Password must be at least 8 character long.'
        );

        use UserTrait, RemindableTrait;

        /**
         * The database table used by the model.
         *
         * @var string
         */
        protected $table = 'users';

        /**
         * The attributes excluded from the model's JSON form.
         *
         * @var array
         */
        protected $hidden = array('password', 'remember_token');

        public function tasks()
        {
            return $this->hasMany('Task');
        }

    }
```

发生了什么事？不多:我们改变了模型的基类(从`Eloquent`到`Ardent`)，并且我们移动了(使用相同的语法，只是一个副本)这里的验证规则，以及定制的错误消息。

所以，现在的问题是:我们要在控制器中写什么？

让我们来看看:

```
<?php

    class UserController extends \BaseController {

        public function postSignup()
        {
            $user = new User;

            $user->first_name = Input::get('first_name');
            $user->last_name = Input::get('last_name');
            $user->email = Input::get('email');
            $user->password = Hash::make(Input::get('password'));

            if(!$user->save())
            {
                return Redirect::to('user/signup')->with('errors', $user->errors()->all());
            }

            return Redirect::to('user/signup')->with('status', 1);
        }

    }
```

这里没有更多的验证说明。他们都消失了。然而，这不仅仅是一个“移动”:如果在验证阶段出现一些问题，`$user->save()`方法将返回`false`。然后，您将能够使用`$user->errors()->all()`方法来检索错误。没有奇怪的类:返回的对象将是一个经典的 [`MessageBag`](http://laravel.com/api/class-Illuminate.Support.MessageBag.html) ，你可能在与 Laravel 合作时已经见过了。

但是，如果您愿意，也可以使用`$user->validationErrors`属性。在这种情况下，或者如果您想检索特定于字段的错误，只需使用`$user->validationErrors->get('field_name')`。

现在，您很可能在想“好吧，但是除了更少的代码行之外，真正的优势是什么？”

让我们从最重要的开始:更好的代码组织意味着更好的项目可维护性。在简单的应用程序中，你不能感觉到它是一个优先事项，但是当涉及到更大的项目时，事情很容易因为一个错误的决定而搞砸。让我举一个真实世界的例子。我们开发了令人敬畏的待办事项列表应用程序，它发展得非常快。我们确实需要一个用于移动应用的 RESTful API。以“正常”方式使用控制器意味着为 API 编写另一个注册例程。两个代码相同的不同块！这不好。好的老干(不要重复自己)原则在哪里？

因此，最佳实践是在模型中编写一个`signup()`方法来处理所有事情。使用热心意味着处理**真正的一切**:从验证到保存过程。没有它，我们不能完成第一阶段。

第二个优势更实用:模拟自动水合物。让我们一起来发现吧。

## 模型自动水合物

我们的`postSignup()`方法只计算十三行代码。即使这看起来很困难，热心可以进一步降低这个数字。看一下这个例子:

```
<?php

    class UserController extends \BaseController {

        public function postSignup()
        {
            $user = new User;
            if(!$user->save())
            {
                return Redirect::to('user/signup')->with('errors', $user->errors()->all());
            }

            return Redirect::to('user/signup')->with('status', 1);
        }

    }
```

这里没有错误。你可能已经明白发生了什么。

Ardent 具有典型的自动水合功能。这意味着当您创建对象并调用`$user->save()`方法时，每个字段都会自动填充输入的对象数据。当然，您必须相应地在每个表单字段中给出正确的名称。

所以，这段代码:

```
$user = new User;

    $user->first_name = Input::get('first_name');
    $user->last_name = Input::get('last_name');
    $user->email = Input::get('email');
    $user->password = Hash::make(Input::get('password'));

    $user->save();
```

将具有与...相同的效果

```
$user = new User;
    $user->save();
```

整个注册过程中，我们从三十条线减少到七条线。

要使用此功能，您必须将其激活。只需将模型中的`$autoHydrateEntityFromInput`切换到 true，就像这样:

```
<?php
    ...
    class User extends \LaravelBook\Ardent\Ardent {
        ...
        public $autoHydrateEntityFromInput = true;
        ...
    }
```

搞定了。

您还经常会有一些业务逻辑不需要的冗余数据。想想`_confirmation`字段或 CSRF 代币。嗯，我们可以用模型的`$autoPurgeRedundantAttributes`属性丢弃它们。和以前一样，把它切换到`true`。

```
<?php
    ...
    class User extends \LaravelBook\Ardent\Ardent {
        ...
        public $autoHydrateEntityFromInput = true;
        public $autoPurgeRedundantAttributes = true;
        ...
    }
```

现在程序比以前更干净。

## 模型挂钩

另一个值得一提的好特性是引入了模型钩子。本质上，它们是一些方法的列表，如果实现了这些方法，它们将在特定的执行时刻被调用。所以，举个例子，每次调用`update()`之前，都会调用`afterUpdate()`方法。在每次验证之前都会调用`beforeValidate()`方法，依此类推。

以下是所有这些方法的列表:

*   `beforeCreate()`
*   `afterCreate()`
*   `beforeSave()`
*   `afterSave()`
*   `beforeUpdate()`
*   `afterUpdate()`
*   `beforeDelete()`
*   `afterDelete()`
*   `beforeValidate()`
*   `afterValidate()`

一个典型的例子是在保存过程之前的一些数据加工。像这样:

```
<?php
    public function beforeSave() {
        $this->slug = Str::slug($this->title);
        return true;
    }
```

每个“before”方法都有一个布尔返回值。如果为真，则正常执行以下操作。如果方法返回 false，操作将被停止。在上面的方法中，我们用`beforeSave()`方法生成了一个 slug(并填充了适当的字段),就在验证之后。

关于`beforeSave()`和`afterSave()`还有一个特别的提示:你可以在运行时声明它们。看:

```
$user->save(array(), array(), array(),
        function ($model) {
            // beforeSave() code here...
            return true;
        },
        function ($model) {
            // afterSave() code here...
        }
    );
```

让我们考虑一下这些方法在我们的应用程序中的几种可能的用法。

也许一个`beforeSave()`来处理密码哈希？

```
<?php

    public function beforeSave()
    {
        $this->password = Hash::make($this->password);
    }
```

或者在用户删除程序之前使用清理钩？

```
<?php

    public function beforeDelete()
    {
        $this->tasks()->delete();
    }
```

有很多种可能。

## 定义关系(热情的方式)

使用 Ardent，您还可以用比以前更短的方式定义关系。让我们看看我们实际上是如何定义模型之间的关系的:下面的例子展示了`User`模型中的`tasks()`方法。

```
<?php

    public function tasks()
    {
        return $this->hasMany('Task');
    }
```

使用 Ardent 定义关系意味着定义一个简单的数组，称为`$relationsData`。

```
<?php

    class User extends \LaravelBook\Ardent\Ardent {

        ...

        public static $relationsData = array(
            'tasks'  => array(self::HAS_MANY, 'Task')
        );

        ...

    }
```

这有完全相同的效果。Ardent 使用相同的命名约定来绑定名称和方法，而不必一个一个地编写。

但是，您可以进行许多自定义:

```
<?php

    class User extends \LaravelBook\Ardent\Ardent {
        public static $relationsData = array(
            'books'  => array(self::HAS_MANY, 'Book', 'table' => 'users_have_books')
        );
    }
```

`$relationsData`中的每个元素都有一个键(是的，关系的方法名)和一个带参数的数组。

*   第一个参数(没有键，只是第一个)描述关系类型(hasOne，hasMany，belongsTo，belongsToMany，morphTo，morphOne，morphMany)。
*   第二个参数(没有键，只是秒)定义了当前关系的目的模型；
*   进一步的参数没有具体的位置，但是有一个键。它们可能是:
    *   foreignKey:可选，用于 hasOne、hasMany、belongsTo 和 belongsToMany
    *   table、otherKey、timestamps 和 pivotKeys:可选，用于 belongsToMany。
    *   名称、类型和 id:与 morphTo、morphOne 和 morphMany 一起使用；

## 结论

凭借一大堆优势(还有 126k 的下载量和频繁的更新)，很难找到一个理由在你的下一个应用中不使用 Ardent。它适用于各种项目，并且作为雄辩模型的扩展，它实现了与 Laravel 项目的完全兼容。

我绝对推荐。你知道吗？你试过了吗？请在下面的评论中告诉我们！

## 分享这篇文章