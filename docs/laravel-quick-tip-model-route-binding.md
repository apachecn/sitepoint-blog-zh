# Laravel 快速提示:模型路由绑定

> 原文：<https://www.sitepoint.com/laravel-quick-tip-model-route-binding/>

Laravel 提供的最棒的东西之一是易于使用的路由组件。它提供了简单的 URL、参数、分组、命名和事件保护路由组，等等。

![Laravel Logo](img/b24fd9db18c1157e55823b1b5d7e1e96.png)

让我们假设我们有一个数据库中的类别列表，管理员可以从后端管理类别。以下是您的 routes 文件的外观。

```
Route::group(['namespace' => 'Admin', 'prefix' => 'admin', 'middleware' => 'admin'], function () {
    Route::resource('categories', 'CategoriesController');
}); 
```

在您的`CategoriesController`类中，您将拥有七个资源方法。在编辑操作中，我们应该检查数据库中是否存在正在编辑的类别，否则我们会返回一条错误消息。

```
public function edit($id)
{
    $category = Category::find($id);
    if (!$category) {
        return redirect()->route('admin.categories.index')->withErrors([trans('errors.category_not_found')]);
    }

    // ...
} 
```

## 模型绑定

这是通常的做法，但是 Laravel 还有一个更好的方法来优化这个重复的任务，叫做[路由模型绑定](https://www.laravel.com/docs/5.2/routing#route-model-binding)。您基本上是键入提示模型名称，而不是 ID 参数。

如果您列出可用的路线，它应该看起来像这样。

```
+--------+-----------+------------------------------------+------------------------------------+----------------------------------------------------------------------+-----------------+
| Domain | Method    | URI                                | Name                               | Action                                                               | Middleware      |
+--------+-----------+------------------------------------+------------------------------------+----------------------------------------------------------------------+-----------------+
|        | GET|HEAD  | admin/categories                   | admin.categories.index             | App\Http\Controllers\Admin\CategoriesController@index                | web,admin       |
|        | POST      | admin/categories                   | admin.categories.store             | App\Http\Controllers\Admin\CategoriesController@store                | web,admin       |
|        | GET|HEAD  | admin/categories/create            | admin.categories.create            | App\Http\Controllers\Admin\CategoriesController@create               | web,admin       |
|        | GET|HEAD  | admin/categories/{categories}      | admin.categories.show              | App\Http\Controllers\Admin\CategoriesController@show                 | web,admin       |
|        | PUT|PATCH | admin/categories/{categories}      | admin.categories.update            | App\Http\Controllers\Admin\CategoriesController@update               | web,admin       |
|        | DELETE    | admin/categories/{categories}      | admin.categories.destroy           | App\Http\Controllers\Admin\CategoriesController@destroy              | web,admin       |
|        | GET|HEAD  | admin/categories/{categories}/edit | admin.categories.edit              | App\Http\Controllers\Admin\CategoriesController@edit                 | web,admin       | 
```

您可以看到路由参数是`{categories}`,如果您愿意，您可以保留它。然而，Laravel 提供了一个改变它的选项。

```
Route::resource('categories', 'CategoriesController', [
    'parameters' => 'singular',
]); 
```

```
+--------+-----------+------------------------------------+------------------------------------+----------------------------------------------------------------------+-----------------+
| Domain | Method    | URI                                | Name                               | Action                                                               | Middleware      |
+--------+-----------+------------------------------------+------------------------------------+----------------------------------------------------------------------+-----------------+
|        | GET|HEAD  | admin/categories                   | admin.categories.index             | App\Http\Controllers\Admin\CategoriesController@index                | web,admin       |
|        | POST      | admin/categories                   | admin.categories.store             | App\Http\Controllers\Admin\CategoriesController@store                | web,admin       |
|        | GET|HEAD  | admin/categories/create            | admin.categories.create            | App\Http\Controllers\Admin\CategoriesController@create               | web,admin       |
|        | GET|HEAD  | admin/categories/{category}        | admin.categories.show              | App\Http\Controllers\Admin\CategoriesController@show                 | web,admin       |
|        | PUT|PATCH | admin/categories/{category}        | admin.categories.update            | App\Http\Controllers\Admin\CategoriesController@update               | web,admin       |
|        | DELETE    | admin/categories/{category}        | admin.categories.destroy           | App\Http\Controllers\Admin\CategoriesController@destroy              | web,admin       |
|        | GET|HEAD  | admin/categories/{category}/edit   | admin.categories.edit              | App\Http\Controllers\Admin\CategoriesController@edit                 | web,admin       | 
```

*注意:* Laravel 5.3 默认使用单数。

```
public function edit(Category $category)
{
    return view('admin.categories.edit', [
        'category'      => $category
    ]);
} 
```

现在，Laravel 将使用 ID 参数自动解析类别，如果模型不存在，将抛出异常。

*注意:*它使用`findOrFail`口才方法来解析参数，除非参数有默认值。

目前一切看起来都很好，因为我们从所有方法中移除了对模型的检查。尽管如此，我们仍然需要捕捉异常并采取适当的措施。

## 处理异常

`App\Exceptions\Handler@render`方法负责将异常转换成 HTTP 响应。我们将使用它来处理`ModelNotFoundException`并重定向到 404 not found 页面。

`render`方法有一个请求和异常参数，我们可以用它来决定做什么。

```
public function render($request, Exception $e)
{
    if ($e instanceof ModelNotFoundException) {
        $view = view("admin.404");

        if ($e->getModel() == Category::class) {
            $view->withErrors(['Category not found'])->render();
        }

        return response($view, 404);
    } else {
        // handle other exceptions
        return parent::render($request, $e);
    }
} 
```

我们测试抛出的异常是否是`ModelNotFoundException`的实例。我们还可以测试模型名称，以显示正确的错误消息。为了避免为我们的所有模型添加多个 if 测试，我们可以创建一个消息的索引数组，并使用模型类名来提取适当的消息。

## 解析参数

Laravel 使用名称和类型提示来解析路由参数。如果参数类型是模型，它将尝试使用 ID 在数据库中查找记录，如果没有找到记录，它将失败。

作为一个良好的实践，我们倾向于避免向最终用户公开我们的内部 id，这个问题通常通过使用通用唯一标识符(UUID)来解决。但是由于 Laravel 是使用表主键来解析绑定参数，所以总是会抛出错误！

为了解决这个问题，Laravel 让我们从父模型类中覆盖`getRouteKeyName`方法。该方法应该返回属性名，在本例中是`uuid`。

```
class Category extends Model
{
    // ...

    public function getRouteKeyName()
    {
        return "uuid";
    }
} 
```

现在，如果我们尝试使用 UUID 编辑一个特定的类别，它应该像预期的那样工作，例如`http://local.dev/admin/categories/b86266d4-63c7-11e6-8c98-08002751e440/edit`。

有更多的小贴士吗？和我们分享一些吧！

## 分享这篇文章