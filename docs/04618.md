# Medoo 入门–使用示例

> 原文：<https://www.sitepoint.com/getting-started-medoo-examples-use/>

在本文中，我将带您了解 Medoo，这是一个 PHP 轻量级数据库抽象库。其主要特点包括:

*   **支持多个数据库**——支持 MySQL、MariaDB、Sybase、MS SQL、PostgreSQL、Oracle。
*   **安全**–阻止 SQL 注入，它使用 PDO。
*   **易于使用**–其 API 非常直观。

虽然 Medoo 不是什么革命性的东西，而且它的文件大小很小这一事实也没什么大不了的，但它仍然是一个有趣的项目，从被彻底抛弃到被模糊地接受，正如这些帖子中所显示的。它正在上升，这就是我们去看它的原因。

![medoo-logo-256](img/802474e3d731a8e8ef0770970347f644.png)

## 装置

尽管网站建议通过下载一个文件并将其包含在您的项目中来安装它，但是您应该使用 Composer 来安装它。

## 连接到数据库

创建 Medoo 的新实例需要将一个数组作为参数传入。该数组应包含以下项目:

*   **database _ type**–您想要连接的数据库的类型。这可以是以下任何一个:mysql、mariadb、pgsql、sybase、oracle、mssql、sqlite。
*   **数据库名称**–数据库的名称。
*   **服务器**–服务器的名称或 IP 地址。
*   **用户名**–用于登录数据库的用户的用户名。
*   **密码**–用户的密码。

```
$db = new medoo(array(
    'database_type' => 'mysql',
    'database_name' => 'pokemon',
    'server' => 'localhost',
    'username' => 'ash_ketchum',
    'password' => 'pikachu'
));
```

medoo 使用的默认端口是端口 3306。如果您的数据库服务器使用不同的东西，您也可以传入`port`并指定正确的端口作为值。

## 选择数据

我们要做的第一件事是从数据库中选择数据。如果您想继续学习，可以在这里下载示例数据库[。](https://gist.github.com/Swader/4268c7fadaabf4dea2b0)

您可以使用`select`方法从特定表格中选择数据。这涉及以下论点:

*   表的名称
*   连接条件(可选)
*   要选择的字段
*   where 条件(可选)

首先，让我们从数据库中选择一些教练:

```
$trainers = $db->select(
    'trainers', 
    array('id', 'name', 'pokemon_count', 'region')
);
```

Medoo 以数组的形式返回结果:

```
Array
(
    [0] => Array
        (
            [id] => 1
            [name] => Brock
            [pokemon_count] => 7
            [region] => Kanto
        )

    [1] => Array
        (
            [id] => 2
            [name] => Marshal
            [pokemon_count] => 8
            [region] => Unova
        )
...
```

上面的查询选择了所有的教练。如果我们只想从特定地区挑选培训师，该怎么办？我们可以通过指定 where 条件来做到这一点:

```
$trainers = $db->select(
    'trainers', 
    array('id', 'name', 'pokemon_count', 'region'), 
    array('region' => 'Kanto')
);
```

上面的查询只返回 Kanto 地区的培训师。

如果我们只想选择在特定地区击败特定数量教练的教练呢？我们是这样做的:

```
$trainers = $db->select('trainers', 
    array('id', 'name', 'trainers_defeated', 'region'), 
    array('AND' => array('trainers_defeated[>]' => 150, 'region' => 'Johto'))
);
```

在使用 where 条件时，您只需记住指定一个字段数组及其相应的条件，作为您要使用的关键字的值。在这种情况下，我们希望两者的条件都返回 true，所以我们使用了关键字`AND`。

如果需要使用除了`=`之外的关系操作符，需要在字段名之后指定，并用括号`[]`括起来。以下是一些例子:

*   选择击败超过 150 名对手的教练:

```
'trainers_defeated[>]' => 150
```

*   选择`badges_count`不等于零的训练器:

```
'badges_count[!]' => 0
```

*   选择在 100 到 300 范围内击败教练的教练:

```
'trainers_defeated[<>]' => array(100, 300)
```

*   选择`badges_count`小于 8 的教练:

```
'badges_count[<]' => 8
```

回到过去，如果我们想选择来自“Kanto”或“Unova”且`badges_count`为 5 及以上或`trainers_defeated`为 20 及以上的培训师，该怎么办？我们是这样做的:

```
$trainers = $db->select(
    'trainers',
    array('id', 'name', 'trainers_defeated', 'badges_count', 'region'),
    array('AND' => array(
        'region' => array('Kanto', 'Unova'),
        'OR' => array('badges_count[>=]' => 5, 'trainers_defeated[>=]' => 20)
    ))
);
```

我们所要做的就是将我们的条件包含在关键字`AND`中。对于第一个条件，我们在数组中传递。在本例中，它是我们想要选择的区域的名称。在第二个条件中，我们必须将它包装在`OR`中，这意味着它里面的条件将返回它们中任何一个的结果。

现在我们想知道哪个训练师抓到的口袋妖怪数量最多。我们可以通过使用`get`方法来实现。与`select`方法不同，这将只从我们正在获取的表中返回一行。

```
$most_caught = $db->get(
    'trainers', 
    array('name', 'pokemon_count'), 
    array('ORDER' => 'pokemon_count DESC')
);
```

您也可以将`ORDER`与`select`方法一起使用。例如，我们希望选择所有的训练者，并按照他们抓到的口袋妖怪的数量进行排序。

```
$trainers_most_caught = $db->select(
    'trainers', 
    array('name', 'pokemon_count', 'trainers_defeated'), 
    array('ORDER' => 'pokemon_count DESC')
);
```

我们也可以按多个字段排序。我们不是传入一个字符串作为`ORDER`的值，而是传入一个数组:

```
$trainers_most_caught = $db->select(
    'trainers', 
    array('name', 'pokemon_count', 'trainers_defeated'), 
    array('ORDER' => array('pokemon_count DESC', 'trainers_defeated DESC'))
);
```

如果我们想知道每个地区有多少培训师呢？Medoo 还没有内置这种功能，所以我们必须使用一个原始查询:

```
$trainer_count_per_region = $db->query(
    'SELECT COUNT(name) as trainers, region FROM trainers GROUP BY region'
    )->fetchAll(PDO::FETCH_ASSOC);
```

在幕后，Medoo 使用 PDO 的[获取方法](http://php.net/manual/en/pdostatement.fetch.php)。这意味着它也使用 PDO fetch 方法使用的默认获取样式。所以我们必须将获取样式作为参数传递给`fetchAll`方法。在本例中是它的`PDO::FETCH_ASSOC`，这意味着它将只返回字段名和它们的值。如果我们将其留空，它将返回关联列和索引列。以下是我们使用`PDO::FETCH_ASSOC`作为获取样式时得到的结果:

```
Array
(
    [0] => Array
        (
            [trainers] => 2
            [region] => Hoenn
        )

    [1] => Array
        (
            [trainers] => 4
            [region] => Johto
        )

    [2] => Array
        (
            [trainers] => 2
            [region] => Kalos
        )

    [3] => Array
        (
            [trainers] => 1
            [region] => Kanto
        )

    [4] => Array
        (
            [trainers] => 3
            [region] => Unova
        )

)
```

如果我们将其留空，结果将如下所示:

```
Array
(
    [0] => Array
        (
            [trainers] => 2
            [0] => 2
            [region] => Hoenn
            [1] => Hoenn
        )

    [1] => Array
        (
            [trainers] => 4
            [0] => 4
            [region] => Johto
            [1] => Johto
        )

    [2] => Array
        (
            [trainers] => 2
            [0] => 2
            [region] => Kalos
            [1] => Kalos
        )

    [3] => Array
        (
            [trainers] => 1
            [0] => 1
            [region] => Kanto
            [1] => Kanto
        )

    [4] => Array
        (
            [trainers] => 3
            [0] => 3
            [region] => Unova
            [1] => Unova
        )

)
```

最后，让我们谈谈连接。我们可以使用以下连接语法:

*   `>`–左连接
*   `<`–右连接
*   `><`–内部连接
*   `<>`–完全加入

下面是一个使用内部连接来选择所有口袋妖怪及其相应类型名称的示例:

```
$db->select(
    'pokemon', 
    array('[><]types' => array('type_id' => 'id')),
    array('pokemon.name', 'types.name(type)')
);
```

就像常规的`select`方法一样，第一个参数是表的名称。在这种情况下，它是我们要从中选择数据的主表。第二个参数是包含连接条件的数组。我们提供连接类型和要连接的表的名称作为键。值是一个数组，主表上的字段作为键，辅助表上的字段作为值。第三个参数是要返回的字段数组。最好总是在字段名之前指定表名。目前 medoo [不支持表别名](https://github.com/catfan/Medoo/issues/97)，所以我们现在必须使用表的全名。如果要选择的字段名称相同，则需要使用列别名。否则，只有您指定的最后一个同名字段会被选中。在上面的示例中，我们使用以下语法指定了别名:

```
table_name.field_name(alias)
```

## 插入数据

接下来，我们将一些数据添加到`pokemon`表中。为此，我们将使用[口袋妖怪 API](http://pokeapi.co/) 作为我们的数据源。

首先用`composer require guzzlehttp/guzzle:~5.0`安装 guzzle

创建一个新的 guzzle 客户端实例:

```
use GuzzleHttp\Client;

$client = new Client();
```

接下来，我们从数据库中选择所有的教练和口袋妖怪类型。当我们将数据插入到`pokemon`表中时，我们将会用到这些。

```
$trainers = $db->select('trainers', array('id', 'pokemon_count'));

$types = $db->select('types', array('id', 'name'));
```

接下来，我们向 Pokemon API 中的 pokedex 资源发出请求。这将返回一个 JSON 字符串，因此我们使用 guzzle 的`json`方法将其转换为一个数组。pokedex 资源返回所有口袋妖怪的数组。

```
$pokedex_response = $client->get('http://pokeapi.co/api/v1/pokedex/1');
$pokedex_data = $pokedex_response->json();

$pokemon = $pokedex_data['pokemon'];
$total_pokemon = count($pokemon) - 1; //were zero-indexed
```

接下来，我们遍历所有的训练者，得到他们抓到的口袋妖怪的数量，然后根据这个数量创建一个 for 循环。pokedex 资源返回口袋妖怪的名字和一个资源 URI，我们可以在其中获得关于它的进一步信息。我们还需要获得口袋妖怪的主要类型和它可以做的动作列表，因此我们也向口袋妖怪资源发出请求。一旦我们得到数据，我们需要得到口袋妖怪类型的 id。我们已经有了一个包含所有 pokemon 类型的列表的表，所以我们只需通过循环它来确定 id，如果从 API 返回的类型名称与数据库中的类型名称相同，我们只需获得它的 id。之后，我们用`mt_rand`给口袋妖怪一个随机等级。接下来，我们调用 Medoo 的`insert`方法将 Pokemon 数据插入数据库。它将表名作为第一个参数，然后将数据数组作为第二个参数。最后，我们给每个口袋妖怪分配一些动作。当您调用`insert`方法时，它返回最后一次插入的 id。我们使用这个最后插入的 id 来确定由数据库分配的 pokemon 的 id，然后将其与移动的名称一起插入到`pokemon_moves`表中。

```
foreach($trainers as $trainer){
    $trainer_id = $trainer['id'];
    $pokemon_count = $trainer['pokemon_count'];

    for($x = 0; $x < $pokemon_count; $x++){

        $pokemon_id = mt_rand(0, $total_pokemon);
        $pokemon_name = $pokemon[$pokemon_id]['name'];
        $pokemon_resource = $pokemon[$pokemon_id]['resource_uri'];

        $pokemon_response = $client->get('http://pokeapi.co/' . $pokemon_resource);
        $pokemon_data = $pokemon_response->json();

        $pokemon_types = $pokemon_data['types'];

        //pokemon types in the database starts with a capital letter
        $type_name = ucfirst($pokemon_types[0]['name']); 

        $type_id = null;
        foreach($types as $t){ //determine type id
            if($t['name'] == $type_name){
                $type_id = $t['id'];
            }
        }

        $level = mt_rand(1, 100); //give a random level between 1 and 100

        $pokemon_db_id = $db->insert(
            'pokemon',
            array(
                'name' => $pokemon_name,
                'trainer_id' => $trainer_id,
                'type_id' => $type_id,
                'level' => $level 
            )
        );

        //assign some moves
        $pokemon_moves = $pokemon_data['moves'];
        if(!empty($pokemon_moves)){
            $move_count = count($pokemon_moves) - 1;
            $move_limit = 4; //each pokemon can only have 4 moves
            for($z = 0; $z < $move_limit; $z++){
                $move_id = mt_rand(0, $move_count); 
                $move_name = $pokemon_moves[$move_id]['name'];

                $db->insert(
                    'pokemon_moves', 
                    array(
                        'pokemon_id' => $pokemon_db_id, 
                        'move_name' => $move_name
                    )
                );
            }
        }

    }
}
```

运行上面的脚本后，我们现在应该在数据库中有一些口袋妖怪和他们的动作了。

假设训练师萝西抓到了 3 个新的口袋妖怪:Drapion，Toxicroak 和 Crobat。我们如何将它们一次全部插入到我们的`pokemon`表中呢？`insert`方法也支持多次插入，所以我们可以提供一个数组，包含我们想要插入的所有数据行。

首先，我们从`trainers`表中获得培训师 id:

```
$trainer_id = $db->get('trainers', 'id',  array('name' => 'Roxie'));
```

让我们假设这 3 个口袋妖怪具有相同的类型，所以我们继续从数据库中获取类型 id:

```
$type_id = $db->get('types', 'id', array('name' => 'Poison'));
```

pokemon API 并没有真正的搜索方法，所以我们所能做的就是直接从浏览器访问`pokedex`资源:

```
http://pokeapi.co/api/v1/pokedex/1/
```

然后寻找我们想要得到的`resource_uri`。我已经做了，所以你不用做了。我们需要的 id 是:452、454 和 169。然后我们循环遍历这些数据，得到所有必要的数据。这一次，我们没有在循环的每次迭代中进行`insert`调用，而是将它存储在一个数组中。然后我们调用一次`insert`方法，并提供存储口袋妖怪数据的数组。

```
$ids = array(452, 454, 169);

$pokemon_caught = array();
foreach($ids as $id){

    $response = $client->get('http://pokeapi.co/api/v1/pokemon/' . $id);
    $data = $response->json();

    $name = $data['name'];

    $pokemon_caught[] = array(
        'name' => $name,
        'trainer_id' => $trainer_id,
        'type_id' => $type_id,
        'level' => mt_rand(1, 100)
    );

}

$db->insert('pokemon', $pokemon_caught);
```

## 更新数据

现在 Roxie 又多了 3 个口袋妖怪，但是她的训练师数据还没有更新。我们需要在她现在的`pokemon_count`上加 3。为此，我们调用了`update`方法。这涉及以下论点:

*   表的名称
*   数据
*   where 条件

我们是这样更新的:

```
$db->update(
    'trainers', 
    array('pokemon_count[+]' => 3), 
    array('id' => $trainer_id)
);
```

看到我们在那里做了什么吗？Medoo 附带了一个很好的实用程序，您可以在其中对正在更新的特定字段进行以下数学运算:

*   `+`–给当前值增加一个特定值。
*   `-`–从当前值中减去特定值。
*   `*`–将特定值乘以当前值。
*   `/`–将特定值除以当前值。

## 删除数据

现在我们想发布 Drapion，因为它太烂了。我们可以使用`delete`方法将他从`pokemon`表中删除。它将表名作为第一个参数，将一组条件作为第二个参数。

```
$db->delete('pokemon', array('name' => 'Drapion'));
```

我们也可以基于两个条件删除。在这里，我们想删除所有的口袋妖怪是'正常'的类型，并低于 60 级:

```
$type_id = $db->get('types', 'id', array('name' => 'Normal'));
$db->delete(
    'pokemon', 
    array('AND' => array('level[<]' => 60, 'type_id' => $type_id))
);
```

请注意，调用 delete 不会返回被删除行的 id。这意味着，如果你想用那个 id 做什么，你必须自己想办法得到它。

## 聚合函数

Medoo 还附带了一些聚合函数。

例如，我们想从`trainers`表中获得培训师的总数。为此，我们使用`count`方法:

```
$total_trainers = $db->count('trainers');
```

如果我们想知道口袋妖怪数量最多/最少的教练，也有一个功能。为了获得最大收益，我们使用了`max`方法。这将占用您想要使用的表和字段的名称:

```
$db->max('trainers', 'pokemon_count');
```

为了得到最少，我们使用`min`方法:

```
$db->min('trainers', 'pokemon_count');
```

我们也可以使用`avg`方法，如果我们想知道`trainers`表中每个教练拥有的口袋妖怪的平均数量:

```
$db->avg('trainers', 'pokemon_count');
```

我们还可以通过使用`sum`方法获得所有训练者拥有的口袋妖怪的总数:

```
$db->sum('trainers', 'pokemon_count');
```

注意，使用`count`、`min`、`max`和`sum`方法，我们还可以指定一些附加条件。我们提供这些作为最后的论据。例如，我们只想知道在‘Hoenn’地区训练者拥有的口袋妖怪的最大数量:

```
$db->max('trainers', 'pokemon_count', array('region' => 'Hoenn'));
```

## 排除故障

Medoo 还提供了用于检查错误和调试的工具。请注意，当您尝试做一些不能产生成功结果的事情时，medoo 不会返回任何错误，这就是为什么有时您必须明确地检查它。

例如，我们为`trainers`表打了一个错别字。我们输入了`trainerw`，而不是`trainers`:

```
$db->update(
    'trainerw', 
    array('pokemon_count[-]' => 1), 
    array('id' => 99)
);
```

执行上面的代码不会让 medoo 抱怨它。它只是无声地失败了。为了检查错误，我们需要在有问题的代码后立即调用`error`方法:

```
$db->error();
```

这将返回如下所示的数组:

```
Array ( 
    [0] => 42S02 
    [1] => 1146 
    [2] => Table 'pokemon.trainerw' doesn't exist 
)
```

第一项是 MySQL 返回的错误码。您可以在页面的[上看到错误代码列表。第二项是 SQL 状态。第三项是人类可读的错误描述。](http://dev.mysql.com/doc/refman/5.5/en/error-messages-server.html)

如果错误是传递给你正在使用的特定方法的参数的错误，medoo 会返回一个错误，所以如果发生了，你实际上不需要检查这些错误。

如果调用`error`方法时没有返回任何错误，那么它可能不是错误。相反，可能是 medoo 生成的查询没有完成您希望它完成的任务。在这些情况下，您需要使用`last_query()`方法。顾名思义，调用这个方法将返回 medoo 执行的最后一个查询。让我们来看一些例子:

*   对于 id 为 1 的教练，将`pokemon_count`减少 1:

```
$db->update(
    'trainers', 
    array('pokemon_count[-]' => 1), 
    array('id' => 1)
);

echo $db->last_query();

/*
returns: 
UPDATE "trainers" SET "pokemon_count" = "pokemon_count" - 1 WHERE "id" = 1
*/
```

*   选择名为“virizion”的口袋妖怪:

```
$db->get(
    'pokemon', 
    array('name', 'level', 'type'), array('name' => 'virizion')
);

echo $db->last_query();
/*
returns:
SELECT "name","level","type" FROM "pokemon" WHERE "name" = 'virizion' LIMIT 1
*/
```

*   选择所有口袋妖怪及其相应的类型:

```
$db->select(
    'pokemon', 
    array('[><]types' => array('type_id' => 'id')),
    array('pokemon.name', 'types.name(type)')
);

echo $db->last_query();
/*
returns:
SELECT "pokemon"."name","types"."name" AS "type" FROM "pokemon" INNER JOIN "types" ON "pokemon"."type_id" = "types"."id"
*/
```

## 结论

就是这样！在本教程中，我们学习了 Medoo，这是一个轻量级且易于使用的 PHP 数据库抽象库。我们学习了如何在数据库中选择、更新、删除和插入数据。我们还学习了一些调试技术，如果出现问题，我们可以使用这些技术。如果你想了解更多，请查看 meedo 的官方文档。

你在用 Medoo 做什么吗？你认为这个项目怎么样？让我们知道！

## 分享这篇文章