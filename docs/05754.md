# PHP 下划线入门

> 原文：<https://www.sitepoint.com/getting-started-php-underscore/>

如果您曾经使用过 JavaScript 的主干框架，那么您应该已经熟悉了下划线。事实上，对于 JavaScript 开发人员来说，它变得非常有用。但是你知道它已经被移植到 PHP 了吗？

在这篇文章中，我将看看下划线，它能做什么，并提供一些例子来说明它可能有用的地方。

## 什么是下划线？

Underscore 将自己描述为“JavaScript 的工具带库，它提供了许多您在 Prototype.js(或 Ruby)中期望的函数式编程支持，但没有扩展任何内置的 JavaScript 对象。这条领带可以搭配 jQuery 的晚礼服和 Backbone.js 的吊带裤。

最值得注意的是，下划线提供了一系列用于处理集合和数组的工具，一些用于处理对象，基本的模板功能和许多其他有用的功能。

在处理 JSON 时，对集合和数组进行操作的函数特别有用，这使得它非常适合处理来自 web 服务的响应。

## 安装/下载

下载库最简单的方法是使用 [Composer](http://getcomposer.org/) :

```
{
    "require": {
        "underscore/underscore.php": "dev-master"
    }   
} 
```

或者，你可以手动下载，或者[从 Github](https://github.com/brianhaveri/Underscore.php) 克隆——你只需要`require`一个文件，`underscore.php`。

## PHP 下划线的语法

在最初的 JavaScript 库中，所有下划线的函数都以一个下划线和一个点为前缀；例如`_.each`、`_.map`、`_.reduce`。在 PHP 中，下划线通常被保留作为`gettext()`的别名(用于翻译字符串)，所以它使用双下划线。

组成这个库的所有函数都可以作为一个名为`__`的类的静态方法使用——例如，一个双下划线。

因此，如果我们希望将 JavaScript 函数映射到它们的 PHP 等价物:

```
JavaScript          PHP
_.each              __::each
_.map               __::map
_.reduce            __::reduce 
```

…等等。

您也可以以面向对象的方式使用它；

```
__(array(1, 2, 3))->map(function($n) { return $n * 2; }); 
```

相当于:

```
__::map(array(1, 2, 3), function($n) { return $n * 2; }); 
```

出于本文的目的，我将使用静态方法风格。

## 使用集合和数组

### 每个

您可以遍历一个数组，使用`__::each`对每一项应用一个函数。

例如:

```
$items = array(1, 2, 3, 4, 5);
__::each($items, function($item) { print $item; }); 
```

生产:

```
12345 
```

这里有一个(稍微)更有用的例子:

```
$student_records = array(
    array(
        'name'    =>  'Joe Bloggs',
        'id'      =>  1,
        'grade'   =>  72,
        'class'   =>  'A',
    ),
    array(
        'name'    =>  'Jack Brown',
        'id'      =>  2,
        'grade'   =>  67,
        'class'   =>  'B',
    ),
    array(
        'name'    =>  'Jill Beaumont',
        'id'      =>  3,
        'grade'   =>  81,
        'class'   =>  'B',
    ),
);

__::each($student_records, function($record) { 
    print $record['name'] . ' ' . $record['grade'] . '<br />'; 
}); 
```

这会产生:

```
Joe Bloggs A
Jack Brown B
Jill Beaumont B 
```

稍后，当我们深入研究模板时，我们将会看到一些更好的方法。

### 勇气

如果您有一个多维数组，并且您希望“提取”出某些值并将它们展平到一个一维数组中，您可以使用`__::pluck`。

脸书 API 提供了一个现实世界中的例子，说明这在什么时候是有用的。当您请求一个脸书用户的朋友列表时，结果(当`json_decode`变成一个多维数组时)如下所示:

```
$response = array(
    'data'  =>  array(
        array(
            'name'  =>  'Joe Bloggs',
            'id'        =>   123456789,
        ),
        array(
            'name'  =>  'Jack Brown',
            'id'        =>  987654321,
        ),
    )
... 
```

如果我们想将脸书用户 ID 提取到一个一维数组中，我们可以这样做:

```
$ids = __::pluck($response['data'], 'id');
// array(123456789, 98765432) 
```

### 最小值和最大值

根据我们之前的学生记录样本，我们可以使用`__::max`找到分数最高的学生:

```
__::max($student_records, function($student) { return $student['grade']; });
// returns array('name' => 'Jill Beaumont', 'id' => 3, 'grade' => 81, 'class' => 'B') 
```

或最低，通过使用`__::min`:

```
__::min($student_records, function($student) { return $student['grade']; });
// returns array('name' => 'Jack Brown', 'id' => 2, 'grade' => 67, 'class' => 'B') 
```

如您所见，这些函数不是简单地返回最高或最低的分数，而是从数组中返回相应的项——即学生记录。

### 过滤并拒绝

`filter`方法在集合或数组上运行真值测试，并只返回那些通过测试的项目。

作为一个例子，让我们回到之前的学生记录。假设及格分数是 70 分或以上。我们可以使用`__::filter`来应用一个简单的函数，这样我们就可以找出哪些学生通过了:

```
$passed = __::filter($student_records, function($student) { return $student['grade'] >= 70; }); 
```

`reject`功能和`filter`完全相反；它排除了通过真值检验的项目。

换句话说，以下两个函数产生相同的结果:

```
__::filter($student_records, function($student) { return $student['grade'] >= 70; });

__::reject($student_records, function($student) { return $student['grade'] < 70; }); 
```

### 索提比

函数使用迭代器函数对数组进行升序排序。这里有一个简单的例子:

```
$scores = array(476, 323, 1010, 567, 723, 1009, 600);
$sorted = __::sortBy($scores, function($score) { return $score; }); 
```

要按降序排序，只需对该值求反。为了得到一个记录列表，从得分最高的学生开始:

```
$ordered = __::sortBy($student_records, function($student) { return -$student['grade']; }); 
```

### 群组依据

假设我们想根据学生所在的班级重新组织我们的学生阵列。

这就是`groupBy`的用武之地。我们可以这样做:

```
var_dump( __::groupBy($student_records, 'class') ); 
```

这将产生以下输出:

```
array(2) {
["A"]=>
array(1) {
    [0]=>
    array(4) {
    ["name"]=>
    string(10) "Joe Bloggs"
    ["id"]=>
    int(1)
    ["grade"]=>
    int(72)
    ["class"]=>
    string(1) "A"
    }
}
["B"]=>
array(2) {
    [0]=>
    array(4) {
    ["name"]=>
    string(10) "Jack Brown"
    ["id"]=>
    int(2)
    ["grade"]=>
    int(67)
    ["class"]=>
    string(1) "B"
    }
    [1]=>
    array(4) {
    ["name"]=>
    string(13) "Jill Beaumont"
    ["id"]=>
    int(3)
    ["grade"]=>
    int(81)
    ["class"]=>
    string(1) "B"
    }
}
} 
```

### 减少

`reduce`函数用于将集合缩减为单个值。

例如，要获得一维数组的总和:

```
__::reduce(array(1, 2, 3), function($first, $second) { return $first + $second; }, 0); // 6 
```

如果我们把学生记录上的`reduce`和`pluck`结合起来，我们可以得出平均分数:

```
$average = round( ( __::reduce(__::pluck($student_records, 'grade'), function($first, $second) { return $first + $second; }, 0) / count($student_records) ), 2); 
```

我们在这里做的是使用`pluck`将分数提取为一个一维数组，使用简单的加法迭代器函数将它们简化为一个值，除以记录数，然后将其四舍五入为两位数。

### 发现

`find`函数遍历一个数组，对每一项执行一个函数，直到函数返回 true——换句话说，它返回第一个匹配的“记录”。

例如，要查找第一个分数低于 70 的学生，您可以这样做:

```
__::find($student_records, function($student) { return $student['grade'] < 70; }) 
```

如果您`var_dump`了结果，这将产生以下结果:

```
array(4) {
["name"]=>
string(10) "Jack Brown"
["id"]=>
int(2)
["grade"]=>
int(67)
["class"]=>
string(1) "B"
} 
```

假设我们想通过 ID 查找学生记录。我们可以这样做:

```
function findById($records, $id) {
return __::find($records, function($record) use ($id) { return ($record['id'] == $id); });
} 
```

如果你跑步:

```
var_dump(findById($student_records, 2)); 
```

你应该知道这个:

```
array(4) {
    ["name"]=>
    string(10) "Jack Brown"
    ["id"]=>
    int(2)
    ["grade"]=>
    int(67)
    ["class"]=>
    string(1) "B"
} 
```

注意包含了关键字`use`，所以`$id`在范围内。

## 模板

Backbone 广泛使用下划线的一个领域是通过其简单的模板功能。

它通常比字符串连接干净得多，并且您可以将它与其他下划线函数(如`__::each`)结合使用，使它更加强大。

在模板字符串中，您可以像这样回显值:

```
<%= $student['name'] %> 
```

您可以使用以下语法执行代码:

```
<% __::each($records, student) { %> … <% }) %> 
```

有两种常用的模板方法。一种是将其定义为字符串，使用上面的语法注入值或代码，并通过`__::template()`函数运行它:

```
$welcome = 'Hello <%= $name %>, welcome back!';
print __::template($welcome, array('name' => 'Jack')); 
```

或者，您可以通过定义一个变量并分配`__::template`函数的结果来“编译”一个模板，将模板定义为一个单独的字符串参数。
以下内容等同于上一个示例:

```
$compiled = __::template('Hello <%= $name %>, welcome back!');
print $compiled(array('name'=>'Jack'));

// Hello Jack, welcome back! 
```

下面是如何通过组合`__::template`和`__::each`来创建一个输出无序列表的简单模板:

```
$ul = __::template('<ul><% __::each($items, function($item)  { %><li><%= $item %></li><% }); %></ul>');

print $ul(array('items' => array('one', 'two', 'three'))); 
```

让我们构建一个编译过的模板，它接受一组学生记录，并创建一个无序的学生姓名列表:

```
$list_students = __::template('<ul><% __::each($records,  function($student) { %><li><%= $student["name"] %></li><% }); %></ul>'); 
```

然后，对其进行渲染:

```
print $list_students(array('records' => $student_records)); 
```

您应该得到以下输出:

```
<ul>
    <li>Joe Bloggs</li>
    <li>Jack Brown</li>
    <li>Jill Beaumont</li>
</ul> 
```

或者生成学生及其成绩表的模板:

```
$grades_table = __::template('<table><thead><tr><td>Student</td><td>Grade</td></tr></thead><tbody><% __::each($records, function($student) { %><tr><td><%= $student["name"] %></td><td><%= $student["grade"] %>%</td></tr><% }); %></tbody></table>');

print $grades_table(array('records' => $student_records)); 
```

当然，您可以传入多个参数，因此我们可以向表格添加一个标题，例如:

```
$grades_table = __::template('<h4><%= $title %></h4><table><thead><tr><td>Student</td><td>Grade</td></tr></thead><tbody><% __::each($records, function($student) { %><tr><td><%= $student["name"] %></td><td><%= $student["grade"] %>%</td></tr><% }); %></tbody></table>');

print $grades_table(array('title' => $title, 'records' => $student_records)); 
```

## 扩展下划线

您甚至可以使用 mixins 创建自己的函数。

```
__::mixin(array(
'capitalize'=> function($string) { return ucwords($string); },
'shout'      => function($string) { return strtoupper($string); }
));
__::capitalize('joe bloggs'); // 'Joe Bloggs'
__::shout('Joe bloggs');       // 'JOE BLOGGS' 
```

## 摘要

在本文中，我向您介绍了流行的“实用工具带”库下划线的 PHP 端口。我已经浏览了一些可用的功能；然而，有更多的探索。浏览一下[文档](http://brianhaveri.github.io/Underscore.php/)并玩一玩！

## 分享这篇文章