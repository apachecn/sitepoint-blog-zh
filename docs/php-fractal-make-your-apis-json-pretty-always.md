# PHP Fractal——让你的 API 的 JSON 永远漂亮！

> 原文：<https://www.sitepoint.com/php-fractal-make-your-apis-json-pretty-always/>

*本文由 [Viraj Khatavkar](https://www.sitepoint.com/author/vkhatavkar/) 进行了同行评审。感谢 SitePoint 的所有同行评审员使 SitePoint 的内容尽可能做到最好！*

* * *

如果您以前构建过 API，我敢打赌您习惯于直接转储数据作为响应。如果做得正确，它可能没有害处，但是有实际的替代方法可以帮助解决这个小问题。

可用的解决方案之一是[分形](http://fractal.thephpleague.com/)。它允许我们在将模型作为响应返回之前，为模型创建一个新的转换层。它非常灵活，易于集成到任何应用程序或框架中。

![Image of fractals with PHP embedded in them](img/4685e65d82c7973e55519cb068cc5234.png)

## 装置

我们将使用 Laravel 5.3 应用程序来构建一个示例，并将 Fractal 包与它集成在一起，所以继续使用安装程序或通过 Composer 创建一个新的 Laravel 应用程序。

```
laravel new demo 
```

或者

```
composer create-project laravel/laravel demo 
```

然后，在文件夹中，我们需要分形包。

```
composer require league/fractal 
```

## 创建数据库

我们的数据库包含一个`users`和`roles`表。每个用户都有一个角色，每个角色都有一个权限列表。

```
// app/User.php

class User extends Authenticatable
{
    protected $fillable = [
        'name',
        'email',
        'password',
        'role_id',
    ];

    protected $hidden = [
        'password', 'remember_token',
    ];

    /**
     * @return \Illuminate\Database\Eloquent\Relations\BelongsTo
     */
    public function role()
    {
        return $this->belongsTo(Role::class);
    }
} 
```

```
// app/Role.php

class Role extends Model
{
    protected $fillable = [
        'name',
        'slug',
        'permissions'
    ];

    /**
     * @return \Illuminate\Database\Eloquent\Relations\HasMany
     */
    public function users()
    {
        return $this->hasMany(User::class);
    }
} 
```

## 创建变形金刚

我们将为每个模型创建一个转换器。我们的`UserTransformer`类是这样的:

```
// app/Transformers/UserTransformer.php

namespace App\Transformers;

use App\User;
use League\Fractal\TransformerAbstract;

class UserTransformer extends TransformerAbstract
{
    public function transform(User $user)
    {
        return [
            'name' => $user->name,
            'email' => $user->email
        ];
    }
} 
```

是的，这就是创造一个变形金刚所需要的一切！它只是以一种开发人员可以管理的方式转换数据，而不是留给 ORM 或存储库。

我们扩展了`TransformerAbstract`类，并定义了将由`User`实例调用的`transform`方法。同样的事情也适用于`RoleTransformer`级。

```
namespace App\Transformers;

use App\Role;
use League\Fractal\TransformerAbstract;

class RoleTransformer extends TransformerAbstract
{
    public function transform(Role $role)
    {
        return [
            'name' => $role->name,
            'slug' => $role->slug,
            'permissions' => $role->permissions
        ];
    }
} 
```

## 创建控制器

我们的控制器应该在将数据发送回用户之前对其进行转换。我们将处理`UsersController`类，目前只定义`index`和`show`动作。

```
// app/Http/Controllers/UsersController.php

class UsersController extends Controller
{
    /**
     * @var Manager
     */
    private $fractal;

    /**
     * @var UserTransformer
     */
    private $userTransformer;

    function __construct(Manager $fractal, UserTransformer $userTransformer)
    {
        $this->fractal = $fractal;
        $this->userTransformer = $userTransformer;
    }

    public function index(Request $request)
    {
        $users = User::all(); // Get users from DB
        $users = new Collection($users, $this->userTransformer); // Create a resource collection transformer
        $users = $this->fractal->createData($users); // Transform data

        return $users->toArray(); // Get transformed array of data
    }
} 
```

index 操作将查询数据库中的所有用户，创建一个包含用户列表和转换器的资源集合，然后执行实际的转换过程。

```
{
  "data": [
    {
      "name": "Nyasia Keeling",
      "email": "crooks.maurice@example.net"
    },
    {
      "name": "Laron Olson",
      "email": "helen55@example.com"
    },
    {
      "name": "Prof. Fanny Dach III",
      "email": "edgardo13@example.net"
    },
    {
      "name": "Athena Olson Sr.",
      "email": "halvorson.jules@example.com"
    }
    // ...
  ]
} 
```

当然，一次返回所有用户是没有意义的，我们应该为此实现一个分页器。

### 页码

Laravel 倾向于把事情简单化。我们可以像这样完成分页:

```
$users = User::paginate(10); 
```

但是为了让 Fractal 实现这一点，我们可能需要在调用分页器之前添加更多的代码来转换我们的数据。

```
// app/Http/Controllers/UsersController.php

class UsersController extends Controller
{
    // ...

    public function index(Request $request)
    {
        $usersPaginator = User::paginate(10);

        $users = new Collection($usersPaginator->items(), $this->userTransformer);
        $users->setPaginator(new IlluminatePaginatorAdapter($usersPaginator));

        $users = $this->fractal->createData($users); // Transform data

        return $users->toArray(); // Get transformed array of data
    }
} 
```

第一步是对模型中的数据进行分页。接下来，我们像前面一样创建一个资源集合，然后在集合上设置分页器。

Fractal 为 Laravel 提供了一个 paginator 适配器来转换`LengthAwarePaginator`类，它也为 Symfony 和 Zend 提供了一个。

```
{
    "data": [
        {
            "name": "Nyasia Keeling",
            "email": "crooks.maurice@example.net"
        },
        {
            "name": "Laron Olson",
            "email": "helen55@example.com"
        },
        // ...
    ],
    "meta": {
        "pagination": {
            "total": 50,
            "count": 10,
            "per_page": 10,
            "current_page": 1,
            "total_pages": 5,
            "links": {
                "next": "http://demo.vaprobash.dev/users?page=2"
            }
        }
    }

} 
```

注意，它为分页细节添加了额外的字段。您可以在[文档](http://fractal.thephpleague.com/pagination/)中阅读更多关于分页的内容。

### 包括子资源

现在我们已经熟悉了分形，是时候学习如何在用户请求时将子资源(关系)包含在响应中了。

我们可以请求在响应中包含额外的资源，就像这样`http://demo.vaprobash.dev/users?include=role`。我们的转换器可以自动检测请求的内容并解析`include`参数。

```
// app/Transformers/UserTransformer.php

class UserTransformer extends TransformerAbstract
{
    protected $availableIncludes = [
        'role'
    ];

    public function transform(User $user)
    {
        return [
            'name' => $user->name,
            'email' => $user->email
        ];
    }

    public function includeRole(User $user)
    {
        return $this->item($user->role, App::make(RoleTransformer::class));
    }
} 
```

属性告诉转换器，我们可能需要在响应中包含一些额外的数据。如果`include`查询参数请求用户角色，它将调用`includeRole`方法。

```
// app/Http/Controllers/UsersController.php

class UsersController extends Controller
{
    // ...

    public function index(Request $request)
    {
        $usersPaginator = User::paginate(10);

        $users = new Collection($usersPaginator->items(), $this->userTransformer);
        $users->setPaginator(new IlluminatePaginatorAdapter($usersPaginator));

        $this->fractal->parseIncludes($request->get('include', '')); // parse includes
        $users = $this->fractal->createData($users); // Transform data

        return $users->toArray(); // Get transformed array of data
    }
} 
```

`$this->fractal->parseIncludes`行负责解析包含查询参数。如果我们请求用户列表，我们应该看到类似这样的内容:

```
{
    "data": [
        {
            "name": "Nyasia Keeling",
            "email": "crooks.maurice@example.net",
            "role": {
                "data": {
                    "name": "User",
                    "slug": "user",
                    "permissions": [ ]
                }
            }
        },
        {
            "name": "Laron Olson",
            "email": "helen55@example.com",
            "role": {
                "data": {
                    "name": "User",
                    "slug": "user",
                    "permissions": [ ]
                }
            }
        },
        // ...
    ],
    "meta": {
        "pagination": {
            "total": 50,
            "count": 10,
            "per_page": 10,
            "current_page": 1,
            "total_pages": 5,
            "links": {
                "next": "http://demo.vaprobash.dev/users?page=2"
            }
        }
    }
} 
```

如果每个用户都有一个角色列表，我们可以将转换器修改成这样:

```
// app/Transformers/UserTransformer.php

class UserTransformer extends TransformerAbstract
{
    protected $availableIncludes = [
        'roles'
    ];

    public function transform(User $user)
    {
        return [
            'name' => $user->name,
            'email' => $user->email
        ];
    }

    public function includeRoles(User $user)
    {
        return $this->collection($user->roles, App::make(RoleTransformer::class));
    }
} 
```

当包含一个子资源时，我们可以通过使用 a *点符号*来*嵌套*关系。假设每个角色都有一个权限列表，存储在一个单独的表中，我们希望列出用户及其角色和权限。我们可以这样做。

有时，我们需要默认包含一些必要的关系，例如地址关系。我们可以通过在转换器中使用`$defaultIncludes`属性来实现。

```
class UserTransformer extends TransformerAbstract
{
    // ...

    protected $defaultIncludes = [
        'address'
    ];

    // ...
} 
```

我非常喜欢 Fractal 包的一点是能够传递参数来包含参数。文档中的一个很好的例子是`order by`。我们可以将它应用到我们的示例中，如下所示:

```
// app/Transformers/RoleTransformer.php

use App\Role;
use Illuminate\Support\Facades\App;
use League\Fractal\ParamBag;
use League\Fractal\TransformerAbstract;

class RoleTransformer extends TransformerAbstract
{
    protected $availableIncludes = [
        'users'
    ];

    public function transform(Role $role)
    {
        return [
            'name' => $role->name,
            'slug' => $role->slug,
            'permissions' => $role->permissions
        ];
    }

    public function includeUsers(Role $role, ParamBag $paramBag)
    {
        list($orderCol, $orderBy) = $paramBag->get('order') ?: ['created_at', 'desc'];

        $users = $role->users()->orderBy($orderCol, $orderBy)->get();

        return $this->collection($users, App::make(UserTransformer::class));
    }
} 
```

这里最重要的部分是`list($orderCol, $orderBy) = $paramBag->get('order') ?: ['created_at', 'desc'];`，它将尝试从用户 include 中获取订单参数，并将其应用于查询构建器。

我们现在可以通过传递参数(`/roles?include=users:order(name|asc)`)来对包含的用户列表进行排序。你可以阅读更多关于在[文档](http://fractal.thephpleague.com/transformers/)中包含资源的信息。

但是，如果用户没有附加任何角色呢？它将因错误而停止，因为它需要有效数据而不是 null。让我们从响应中删除关系，而不是用空值显示它。

```
// app/Transformers/UserTransformer.php

class UserTransformer extends TransformerAbstract
{
    protected $availableIncludes = [
        'roles'
    ];

    public function transform(User $user)
    {
        return [
            'name' => $user->name,
            'email' => $user->email
        ];
    }

    public function includeRoles(User $user)
    {
        if (!$user->role) {
            return null;
        }

        return $this->collection($user->roles, App::make(RoleTransformer::class));
    }
} 
```

### 急切装载

因为雄辩在访问模型时会偷懒加载模型，我们可能会遇到 [*n+1* 问题](https://leanpub.com/sn1php)。这可以通过立即加载关系以优化查询来解决。

```
class UsersController extends Controller
{

    // ...

    public function index(Request $request)
    {
        $this->fractal->parseIncludes($request->get('include', '')); // parse includes

        $usersQueryBuilder = User::query();
        $usersQueryBuilder = $this->eagerLoadIncludes($request, $usersQueryBuilder);
        $usersPaginator = $usersQueryBuilder->paginate(10);

        $users = new Collection($usersPaginator->items(), $this->userTransformer);
        $users->setPaginator(new IlluminatePaginatorAdapter($usersPaginator));
        $users = $this->fractal->createData($users); // Transform data

        return $users->toArray(); // Get transformed array of data
    }

    protected function eagerLoadIncludes(Request $request, Builder $query)
    {
        $requestedIncludes = $this->fractal->getRequestedIncludes();

        if (in_array('role', $requestedIncludes)) {
            $query->with('role');
        }

        return $query;
    }
} 
```

这样，我们在访问模型关系时就不会有任何额外的查询。

## 结论

我在阅读 Phil Sturgeon 的[构建你不会讨厌的 APIs】时偶然发现了分形，这是一本非常棒且内容丰富的读物，我真心推荐。](https://www.sitepoint.com/build-apis-wont-hate-review/)

你在构建你的 API 的时候用变形金刚吗？你有没有做同样工作的首选包，或者你只是扔掉这些？请在下面的评论区告诉我们！

## 分享这篇文章