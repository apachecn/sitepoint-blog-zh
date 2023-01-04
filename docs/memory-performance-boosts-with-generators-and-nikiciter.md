# 使用生成器和 Nikic/Iter 提升内存性能

> 原文：<https://www.sitepoint.com/memory-performance-boosts-with-generators-and-nikiciter/>

数组，通过扩展迭代，是任何应用程序的基础部分。就像我们的应用程序的复杂性一样，我们使用它们的方式也应该随着我们获得新工具而发展。

新工具，比如发电机。首先是数组。然后我们获得了定义我们自己的类似数组的东西的能力(称为迭代器)。但是从 PHP 5.5 开始，我们可以快速创建类似迭代器的结构，称为生成器。

![A loop illustration](img/1339dc708bf026c5a3bec1a235f6b096.png)

这些以函数的形式出现，但是我们可以把它们作为迭代器来使用。它们为我们提供了一个简单的语法来解释本质上可中断的、可重复的功能。他们太棒了！

我们将看看我们可以使用它们的几个领域。我们还会发现一些使用它们时需要注意的问题。最后，我们将研究一个由天才尼基塔·波波夫创建的辉煌图书馆。

你可以在 https://github.com/sitepoint-editors/generators-and-iter 找到示例代码。

## 问题

假设您有许多关系数据，并且您想进行一些急切的加载。也许数据是逗号分隔的，您需要加载每种数据类型，并将它们结合在一起。

你可以从简单的事情开始:

```
function readCSV($file) {
    $rows = [];

    $handle = fopen($file, "r");

    while (!feof($handle)) {
        $rows[] = fgetcsv($handle);
    }

    fclose($handle);

    return $rows;
}

$authors = array_filter(
    readCSV("authors.csv")
);

$categories = array_filter(
    readCSV("categories.csv")
);

$posts = array_filter(
    readCSV("posts.csv")
);
```

然后，您可能会尝试通过迭代或高阶函数来连接相关元素:

```
function filterByColumn($array, $column, $value) {
    return array_filter(
        $array, function($item) use ($column, $value) {
            return $item[$column] == $value;
        }
    );
}

$authors = array_map(function($author) use ($posts) {
    $author["posts"] = filterByColumn(
        $posts, 1, $author[0]
    );

    // make other changes to $author

    return $author;
}, $authors);

$categories = array_map(function($category) use ($posts) {
    $category["posts"] = filterByColumn(
        $posts, 2, $category[0]
    );

    // make other changes to $category

    return $category;
}, $categories);

$posts = array_map(function($post) use ($authors, $categories) {
    foreach ($authors as $author) {
        if ($author[0] == $post[1]) {
            $post["author"] = $author;
            break;
        }
    }

    foreach ($categories as $category) {
        if ($category[0] == $post[1]) {
            $post["category"] = $category;
            break;
        }
    }

    // make other changes to $post

    return $post;
}, $posts);
```

看起来不错，对吧？那么，当我们有大量的 CSV 文件需要解析时会发生什么呢？让我们分析一下内存使用情况…

```
function formatBytes($bytes, $precision = 2) {
    $kilobyte = 1024;
    $megabyte = 1024 * 1024;

    if ($bytes >= 0 && $bytes < $kilobyte) {
        return $bytes . " b";
    }

    if ($bytes >= $kilobyte && $bytes < $megabyte) {
        return round($bytes / $kilobyte, $precision) . " kb";
    }

    return round($bytes / $megabyte, $precision) . " mb";
}

print "memory:" . formatBytes(memory_get_peak_usage());
```

*示例代码包括`generate.php`，你可以用它来制作这些 CSV 文件……*

如果您有大的 CSV 文件，这段代码应该显示将这些数组链接在一起需要多少内存。它至少是你必须读取的文件的大小，因为 PHP 必须把它保存在内存中。

## 发电机来救援了！

改善这种情况的一种方法是使用发电机。如果你对它们不熟悉，现在是了解更多信息的好时机。

生成器将允许你一次加载少量的数据。使用发电机不需要做太多事情:

```
function readCSVGenerator($file) {
    $handle = fopen($file, "r");

    while (!feof($handle)) {
        yield fgetcsv($handle);
    }

    fclose($handle);
}
```

如果您遍历 CSV 数据，您会注意到您需要的内存量立即下降:

```
foreach (readCSVGenerator("posts.csv") as $post) {
    // do something with $post
}

print "memory:" . formatBytes(memory_get_peak_usage());
```

如果您之前看到的是使用的兆字节内存，现在您将看到千字节。这是一个巨大的进步，但也存在一些问题。

首先，`array_filter`和`array_map`不与发电机一起工作。你必须找到其他工具来处理这种数据。这里有一个你可以试试！

```
composer require nikic/iter
```

这个库引入了一些使用迭代器和生成器的函数。那么，如果不把这些数据保存在内存中，你怎么能得到所有这些相关的数据呢？

```
function getAuthors() {
    $authors = readCSVGenerator("authors.csv");

    foreach ($authors as $author) {
        yield formatAuthor($author);
    }
}

function formatAuthor($author) {
    $author["posts"] = getPostsForAuthor($author);

    // make other changes to $author

    return $author;
}

function getPostsForAuthor($author) {
    $posts = readCSVGenerator("posts.csv");

    foreach ($posts as $post) {
        if ($post[1] == $author[0]) {
            yield formatPost($post);
        }
    }
}

function formatPost($post) {
    foreach (getAuthors() as $author) {
        if ($post[1] == $author[0]) {
            $post["author"] = $author;
            break;
        }
    }

    foreach (getCategories() as $category) {
        if ($post[2] == $category[0]) {
            $post["category"] = $category;
            break;
        }
    }

    // make other changes to $post

    return $post;
}

function getCategories() {
    $categories = readCSVGenerator("categories.csv");

    foreach ($categories as $category) {
        yield formatCategory($category);
    }
}

function formatCategory($category) {
    $category["posts"] = getPostsForCategory($category);

    // make other changes to $category

    return $category;
}

function getPostsForCategory($category) {
    $posts = readCSVGenerator("posts.csv");

    foreach ($posts as $post) {
        if ($post[2] == $category[0]) {
            yield formatPost($post);
        }
    }
}

// testing this out...

foreach (getAuthors() as $author) {
    foreach ($author["posts"] as $post) {
        var_dump($post["author"]);
        break 2;
    }
}
```

这可能不太详细:

```
function filterGenerator($generator, $column, $value) {
    return iter\filter(
        function($item) use ($column, $value) {
            return $item[$column] == $value;
        },
        $generator
    );
}

function getAuthors() {
    return iter\map(
        "formatAuthor",
        readCSVGenerator("authors.csv")
    );
}

function formatAuthor($author) {
    $author["posts"] = getPostsForAuthor($author);

    // make other changes to $author

    return $author;
}

function getPostsForAuthor($author) {
    return iter\map(
        "formatPost",
        filterGenerator(
            readCSVGenerator("posts.csv"), 1, $author[0]
        )
    );
}

function formatPost($post) {
    foreach (getAuthors() as $author) {
        if ($post[1] == $author[0]) {
            $post["author"] = $author;
            break;
        }
    }

    foreach (getCategories() as $category) {
        if ($post[2] == $category[0]) {
            $post["category"] = $category;
            break;
        }
    }

    // make other changes to $post

    return $post;
}

function getCategories() {
    return iter\map(
        "formatCategory",
        readCSVGenerator("categories.csv")
    );
}

function formatCategory($category) {
    $category["posts"] = getPostsForCategory($category);

    // make other changes to $category

    return $category;
}

function getPostsForCategory($category) {
    return iter\map(
        "formatPost",
        filterGenerator(
            readCSVGenerator("posts.csv"), 2, $category[0]
        )
    );
}
```

每次都要重新读取每个数据源，这有点浪费。考虑在内存中保存较小的相关数据(如作者和类别……

## 其他有趣的事情

说到尼基奇的图书馆，那只是冰山一角！有没有想过扁平化一个数组(或者迭代器/生成器)？

```
$array = iter\toArray(
    iter\flatten(
        [1, 2, [3, 4, 5], 6, 7]
    )
);

print join(", ", $array); // "1, 2, 3, 4, 5"
```

您可以使用像`slice`和`take`这样的函数返回可迭代变量的片段:

```
$array = iter\toArray(
    iter\slice(
        [-3, -2, -1, 0, 1, 2, 3],
        2, 4
    )
);

print join(", ", $array); // "-1, 0, 1, 2"
```

随着您更多地使用生成器，您可能会发现您不能总是重用它们。考虑下面的例子:

```
$mapper = iter\map(
    function($item) {
        return $item * 2;
    },
    [1, 2, 3]
);

print join(", ", iter\toArray($mapper));
print join(", ", iter\toArray($mapper));
```

如果您尝试运行该代码，您将会看到一个异常:“无法遍历已经关闭的生成器”。该库中的每个迭代器函数都有一个可重绕的对应函数:

```
$mapper = iter\rewindable\map(
    function($item) {
        return $item * 2;
    },
    [1, 2, 3]
);
```

您可以多次使用这个映射函数。您甚至可以将自己的发生器做成可倒带的:

```
$rewindable = iter\makeRewindable(function($max = 13) {
    $older = 0;
    $newer = 1;

    do {
        $number = $newer + $older;

        $older = $newer;
        $newer = $number;

        yield $number;
    }
    while($number < $max);
});

print join(", ", iter\toArray($rewindable()));
```

你从这得到的是一个可重复使用的发电机！

## 结论

对于您需要考虑的每一个循环，生成器可能是一个选项。它们甚至对其他事情也很有用。在语言有缺陷的地方，Nikic 的库用大量的高阶函数介入。

你用发电机了吗？您是否希望看到更多关于如何在您自己的应用中实现它们以获得一些性能升级的示例？让我们知道！

## 分享这篇文章