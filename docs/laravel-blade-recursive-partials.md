# 带有@each 的 Laravel 刀片递归分音

> 原文：<https://www.sitepoint.com/laravel-blade-recursive-partials/>

在本教程中，我们将通过`@each`命令在 Laravel 的[刀片](http://laravel.com/docs/5.0/templates)模板引擎中实现递归部分的过程。这将允许我们呈现具有任意数量嵌套子元素的数据结构，而不需要知道数组的最大深度。

![](img/05dc7e1f46783acbfcdf01feaea4a4d5.png)

## 数据

我所说的数据是像文件夹结构这样的数据，它可以深入到许多层次。对于我们的例子，假设我们正在处理一个 todo 应用程序中“项目”的预定义数据集，比如 [Todoist](https://todoist.com) 。请随意从[的这个要点](https://gist.github.com/Swader/1074dc0e3c850481adc2)或下面嵌入的代码中获取样本数据:

```
$a = array(
            0 => array(
                'indent' => 1,
                'name' => 'Inbox',
                'color' => '#dddddd',
                'is_deleted' => 0,
                'collapsed' => 0,
                'inbox_project' => true,
                'archived_date' => null,
                'item_order' => 0,
                'is_archived' => 0,
                'archived_timestamp' => 0,
                'user_id' => 3840103,
                'id' => 138837507,
                'children' => array(),
                'parent' => 'root',
            ),
            1 => array(
                'indent' => 1,
                'name' => 'Personal',
                'color' => '#fc603c',
                'is_deleted' => 0,
                'collapsed' => 0,
                'archived_date' => null,
                'item_order' => 1,
                'is_archived' => 0,
                'archived_timestamp' => 0,
                'user_id' => 3840103,
                'id' => 138837508,
                'children' => array(),
                'parent' => 'root',
            ),
            2 => array(
                'indent' => 1,
                'name' => 'Work',
                'color' => '#a8c9e5',
                'is_deleted' => 0,
                'collapsed' => 0,
                'archived_date' => null,
                'item_order' => 2,
                'is_archived' => 0,
                'archived_timestamp' => 0,
                'user_id' => 3840103,
                'id' => 138837509,
                'children' => array(
                    0 => array(
                        'indent' => 2,
                        'name' => 'Work indent 1-1',
                        'color' => '#a8c9e5',
                        'is_deleted' => 0,
                        'collapsed' => 0,
                        'archived_date' => null,
                        'item_order' => 3,
                        'is_archived' => 0,
                        'archived_timestamp' => 0,
                        'user_id' => 3840103,
                        'id' => 139576614,
                        'children' => array(
                            0 => array(
                                'indent' => 3,
                                'name' => 'Work indent 1-2',
                                'color' => '#dddddd',
                                'is_deleted' => 0,
                                'collapsed' => 0,
                                'archived_date' => null,
                                'item_order' => 4,
                                'is_archived' => 0,
                                'archived_timestamp' => 0,
                                'user_id' => 3840103,
                                'id' => 139576626,
                                'children' => array(),
                                'parent' => 139576614,
                            ),
                            1 => array(
                                'indent' => 3,
                                'name' => 'Work indent 1-2 2nd',
                                'color' => '#dddddd',
                                'is_deleted' => 0,
                                'collapsed' => 0,
                                'archived_date' => null,
                                'item_order' => 5,
                                'is_archived' => 0,
                                'archived_timestamp' => 0,
                                'user_id' => 3840103,
                                'id' => 139576629,
                                'children' => array(),
                                'parent' => 139576614,
                            ),
                        ),
                        'parent' => 138837509,
                    ),
                    1 => array(
                        'indent' => 2,
                        'name' => 'Work indent 2-1',
                        'color' => '#a8c9e5',
                        'is_deleted' => 0,
                        'collapsed' => 0,
                        'archived_date' => null,
                        'item_order' => 6,
                        'is_archived' => 0,
                        'archived_timestamp' => 0,
                        'user_id' => 3840103,
                        'id' => 139576622,
                        'children' => array(
                            0 => array(
                                'indent' => 3,
                                'name' => 'Work indent 2-2',
                                'color' => '#dddddd',
                                'is_deleted' => 0,
                                'collapsed' => 0,
                                'archived_date' => null,
                                'item_order' => 7,
                                'is_archived' => 0,
                                'archived_timestamp' => 0,
                                'user_id' => 3840103,
                                'id' => 139576636,
                                'children' => array(),
                                'parent' => 139576622,
                            ),
                        ),
                        'parent' => 138837509,
                    ),
                    2 => array(
                        'indent' => 2,
                        'name' => 'Work indent 3-1',
                        'color' => '#dddddd',
                        'is_deleted' => 0,
                        'collapsed' => 0,
                        'archived_date' => null,
                        'item_order' => 8,
                        'is_archived' => 0,
                        'archived_timestamp' => 0,
                        'user_id' => 3840103,
                        'id' => 139576646,
                        'children' => array(),
                        'parent' => 138837509,
                    ),
                ),
                'parent' => 'root',
            ),
            3 => array(
                'indent' => 1,
                'name' => 'Errands',
                'color' => '#74e8d4',
                'is_deleted' => 0,
                'collapsed' => 0,
                'archived_date' => null,
                'item_order' => 9,
                'is_archived' => 0,
                'archived_timestamp' => 0,
                'user_id' => 3840103,
                'id' => 138837510,
                'children' => array(),
                'parent' => 'root',
            ),
            4 => array(
                'indent' => 1,
                'name' => 'Shopping',
                'color' => '#dddddd',
                'is_deleted' => 0,
                'collapsed' => 0,
                'archived_date' => null,
                'item_order' => 10,
                'is_archived' => 0,
                'archived_timestamp' => 0,
                'user_id' => 3840103,
                'id' => 138837511,
                'children' => array(),
                'parent' => 'root',
            ),
            5 => array(
                'indent' => 1,
                'name' => 'Movies to watch',
                'color' => '#e3a8e5',
                'is_deleted' => 0,
                'collapsed' => 0,
                'archived_date' => null,
                'item_order' => 11,
                'is_archived' => 0,
                'archived_timestamp' => 0,
                'user_id' => 3840103,
                'id' => 138837512,
                'children' => array(),
                'parent' => 'root',
            ),
        );
```

## 普通的老式 PHP

当使用普通的 PHP 输出这样的数据时，人们可能会使用这样的方法:

```
public function output($projects)
    {
        $string = "<ul>";
        foreach ($projects as $i => $project) {
            $string .= "<li>";
            $string .= $project['name'];
            if (count($project['children'])) {
                $string .= $this->output($project['children']);
            }
            $string .= "</li>";
        }
        $string .= "</ul>";
        return $string;
    }
```

恶。它是可行的，但是它非常不灵活，并且把表达和逻辑混在了一起。我们不要这样。

## 刀片 Foreach

有了 Blade，事情变得简单了一些。我们可以使用`foreach`构造来帮助我们。

```
@if (count($projects) > 0)
    <ul>
    @foreach ($projects as $project)
        @include('partials.project', $project)
    @endforeach
    </ul>
@else
    @include('partials.projects-none')
@endif
```

由于 Blade 并不真正支持定义函数，因此不允许我们像上面的`output`函数那样递归地调用它们，我们需要定义部分函数并让它们调用自己:

*   `partials/project.blade.php`

    ```
    <li>{{ $project['name'] }}</li>
    	@if (count($project['children']) > 0)
    	    <ul>
    	    @foreach($project['children'] as $project)
    	        @include('partials.project', $project)
    	    @endforeach
    	    </ul>
    	@endif
    ```

*   `partials/projects-none.blade.php`

    ```
    You have no projects!
    ```

但是……这么简单的东西却有这么多代码。没有办法进一步缩短吗？

## 每个刀片

Laravel Blade 有一个未记录的特性，可以帮助我们减少模板文件中的 LoC 数量，使我们的开发人员和设计人员的工作变得更加容易。该功能为`@each`，用途如下:

```
@each('viewfile-to-render', $data, 'variablename','optional-empty-viewfile')
```

第一个参数是要呈现的模板。这通常是局部的，就像我们的`project.blade.php`。第二个是可迭代数据集，在我们的例子中是`$projects`。第三是迭代时元素将使用的变量名。例如，在`foreach ($data as $element)`中，这个参数将是`element`(没有`$`)。第四个参数是可选的——它是当第二个参数(`$data`)为空时应该呈现的模板文件的名称，即没有要迭代的内容。如果我们将所有这些应用到我们的案例中，我们可以替换整个模块:

```
@if (count($projects) > 0)
    <ul>
    @foreach ($projects as $project)
        @include('partials.project', $project)
    @endforeach
    </ul>
@else
    @include('partials.projects-none')
@endif
```

随着

```
@each('partials.project', $projects, 'project', 'partials.projects-none')
```

![](img/f44c6627f47d59435f6210636fdaeabb.png)

## 结论

在这个简短的教程中，我们看到了如何利用 Laravel Blade 的一个未被记录的特性来大幅减少模板代码的行数。通过使用`@each`,依靠 partials 和它们递归调用自己的能力，我们有了一个惊人的工具库，可以输出各种数据——这只是把积木放在正确的顺序上。

您可以使用这种部分递归的方法来回显目录树、内容管理类别、员工目录等等。

你知道`@each`吗？你知道其他隐藏的宝石吗？请在评论中告诉我们！

## 分享这篇文章