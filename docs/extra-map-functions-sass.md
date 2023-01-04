# Sass 中的额外地图功能

> 原文：<https://www.sitepoint.com/extra-map-functions-sass/>

萨斯地图太棒了。他们让很多以前不可能的事情变成了可能。例如，他们有一个聪明的方法来定义动态变量。它们也是复杂配置、框架或网格系统的完美容器。

幸运的是，Sass 提供了一些操作地图的函数。然而，如果您继续使用 Sass，您可能会遇到内置 API 没有涵盖的边缘情况。

也许您需要一种方法来获得嵌套在几个映射中的键？如果你想更新这个密钥呢？或者从 jQuery 扩展类似`$.extend`的嵌套映射？这里或那里的一些问题可以很容易地用我们今天要研究的函数来解决。

## 地图深度获取

当你需要访问一个属于映射的键，而这个映射恰好嵌套在另一个(或几个)映射中时，你不能再依赖于`map-get`。至少不会像现在这样。

```
/// Fetch nested keys
/// @param {Map} $map - Map
/// @param {Arglist} $keys - Keys to fetch
/// @return {*}
@function map-deep-get($map, $keys...) {
 @each $key in $keys {
 $map: map-get($map, $key);
  }

 @return $map;
}
```

### 例子

```
$grid-configuration: (
  'columns': 12,
  'layouts': (
    'small': 800px,
    'medium': 1000px,
    'large': 1200px,
  ),
);

// Without `map-deep-get`
$medium: map-get(map-get($grid-configuration, 'layouts'), 'medium');

// With `map-deep-get`
$medium: map-deep-get($grid-configuration, 'layouts', 'medium');
```

## 地图深度设置

如您所见，构建一个从嵌套映射中获取键的函数并不难编写。另一方面，在一个钥匙链的末端设置一个值的函数并不容易构建…因为它的复杂性，我们不会深入研究它的代码。

然而，我们将仔细观察签名，因为我对这个签名有点疯狂。为了让我们的功能发挥作用，我们需要三样东西:

*   地图；
*   钥匙链，可以包含从一把钥匙到亿万把钥匙；
*   密钥链中最后一个密钥的新值。

我们已经在[之前的文章](https://www.sitepoint.com/sass-multiple-arguments-lists-or-arglist/)中看到了为什么在处理未知数量的参数时使用 arglist 更好，这正是我们在这里使用`$keys`的原因。问题是，arglist 后面不能有额外的参数，所以为了解决这个问题，我们有两种方法来编写函数。

我们可以先放入新值，然后放入钥匙链，但是在更新之前放入新值对我来说有点奇怪。

```
@function map-deep-set($map, $value, $keys...) {}
```

或者我们只有两个实际的参数，并认为最后一个值是我们的新值。

```
@function map-deep-set($map, $keys.../*, $value */) {}
```

这是我决定要用的。

```
/// Update a key deeply nested
/// @author Hugo Giraudel
/// @param {Map} $map - Map to update
/// @param {Arglist} $keys - Keys to access to value to update
/// @param {*} $value - New value (last member of `$keys`)
/// @return {Map} - Updated map
@function map-deep-set($map, $keys.../*, $value */) {
 $map-list: ($map,);
 $result: null;

 @if length($keys) == 2 {
 @return map-merge($map, (nth($keys, 1): nth($keys, -1)));
  }

 @for $i from 1 through length($keys) - 2 {
 $map-list: append($map-list, map-get(nth($map-list, -1), nth($keys, $i)));
  }

 @for $i from length($map-list) through 1 {
 $result: map-merge(nth($map-list, $i), (nth($keys, $i): if($i == length($map-list), nth($keys, -1), $result)));
  }

 @return $result;
}
```

### 例子

```
$grid-configuration: (
  'columns': 12,
  'layouts': (
    'small': 800px,
    'medium': 1000px,
    'large': 1200px,
  ),
);

// Without `map-deep-set`
$grid-configuration: map-merge($grid-configuration, map-merge(map-get($grid-configuration, 'layouts'), ('large': 1300px)));

// With `map-deep-set`
$medium: map-deep-set($grid-configuration, 'layouts', 'medium', 1300px);
```

## 地图深度

在关于[调试 Sass 映射](https://www.sitepoint.com/debugging-sass-maps/)的文章中，我们看到了如何构建一个函数来检索 Sass 映射的最大深度。让我在这里为子孙后代再贴一次:

```
/// Compute the maximum depth of a map
/// @param {Map} $map
/// @return {Number} max depth of `$map`
@function map-depth($map) {
 $level: 1;

 @each $key, $value in $map {
 @if type-of($value) == "map" {
 $level: max(map-depth($value) + 1, $level);
    }
  }

 @return $level;
}
```

### 例子

```
$grid-configuration: (
  'columns': 12,
  'layouts': (
    'small': 800px,
    'medium': 1000px,
    'large': 1200px,
  ),
);

// Maximum depth
$depth: map-depth($grid-configuration);
// -> 2
```

## 地图有键

Sass 提供了一个函数来检查映射是否有给定的键。我们可以扩展这个函数来测试 Sass 是否有几个键，你说呢？正如你所看到的，这实际上非常容易做到。

```
/// Test if map got all `$keys` at first level
/// @author Hugo Giraudel
/// @param {Map} $map - Map
/// @param {Arglist} $keys - Keys to test
/// @return {Bool}
@function map-has-keys($map, $keys...) {
 @each $key in $keys {
 @if not map-has-key($map, $key) {
 @return false;
    }
  }

 @return true;
}
```

### 例子

```
$grid-configuration: (
  'columns': 12,
  'layouts': (
    'small': 800px,
    'medium': 1000px,
    'large': 1200px,
  ),
);

$depth: map-has-keys($grid-configuration, 'columns', 'layouts');
// -> true

$depth: map-has-keys($grid-configuration, 'columns', 'options');
// -> false
```

## 映射有嵌套键

同样，默认的`map-has-key`函数在处理嵌套地图时不能使用，因为它只深入一层。让我们卷起袖子来建一个深`map-has-key`。

```
/// Test if map got all `$keys` nested with each others
/// @author Hugo Giraudel
/// @param {Map} $map - Map
/// @param {Arglist} $keys - Keys to test
/// @return {Bool}
@function map-has-nested-keys($map, $keys...) {
 @each $key in $keys {
 @if not map-has-key($map, $key) {
 @return false;
    }
 $map: map-get($map, $key);
  }

 @return true;
}
```

### 例子

```
$grid-configuration: (
  'columns': 12,
  'layouts': (
    'small': 800px,
    'medium': 1000px,
    'large': 1200px,
  ),
);

$depth: map-has-keys($grid-configuration, 'layouts', 'medium');
// -> true

$depth: map-has-keys($grid-configuration, 'layouts', 'huge');
// -> false
```

## 地图邮政编码

这是我不止一次想做的事情，但没有内置的方法来实现:合并两个列表来构建一个映射，其中第一个列表作为键，第二个列表作为值。

如果两个列表的长度不同，多余的值将被丢弃，函数将发出警告来解释发生了什么。

```
/// An equivalent of `zip` function but for maps.
/// Takes two lists, the first for keys, second for values.
/// @param {List} $keys - Keys for map
/// @param {List} $values - Values for map
/// @return {Map} Freshly created map
/// @see http://sass-lang.com/documentation/Sass/Script/Functions.html#zip-instance_method
@function map-zip($keys, $values) {
 $l-keys: length($keys);
 $l-values: length($values);
 $min: min($l-keys, $l-values);
 $map: ();

 @if $l-keys != $l-values {
 @warn "There are #{$l-keys} key(s) for #{$l-values} value(s) in the map for `map-zip`. "
 + "Resulting map will only have #{$min} pairs.";
  }

 @if $min == 0 {
 @return $map;
  }

 @for $i from 1 through $min {
 $map: map-merge($map, (nth($keys, $i): nth($values, $i)));
  }

 @return $map;
}
```

### 例子

```
$layout-names: 'small', 'medium', 'large', 'huge';
$layout-values: 600px, 900px, 1200px, 1500px;

$breakpoints: map-zip($layout-names, $layout-values);
// -> ('small': 600px, 'medium': 900px, 'large': 1200px, 'huge': 1500px)
```

## 地图扩展

您可能知道`map-merge`有两个用途:向一个地图添加一个键，并将两个地图合并在一起。到今天为止，我认为`map-merge`可能有两个缺陷:

*   它不是递归的，这意味着它可以处理嵌套映射；
*   只需要两张地图，不能再多了。

我们当然可以做得更好，对吗？我来介绍一下`map-extend`函数， [ala jQuery](http://api.jquery.com/jquery.extend/) 。至于`map-deep-set`，我们使用一个签名黑客将`$deep`参数作为`$maps`链的最后一个值。

```
/// jQuery-style extend function
/// About `map-merge()`:
/// * only takes 2 arguments
/// * is not recursive
/// @param {Map} $map - first map
/// @param {ArgList} $maps - other maps
/// @param {Bool} $deep - recursive mode
/// @return {Map}
@function map-extend($map, $maps.../*, $deep */) {
 $last: nth($maps, -1);
 $deep: $last == true;
 $max: if($deep, length($maps) - 1, length($maps));

  // Loop through all maps in $maps...
 @for $i from 1 through $max {
    // Store current map
 $current: nth($maps, $i);

    // If not in deep mode, simply merge current map with map
 @if not $deep {
 $map: map-merge($map, $current);
    } @else {
      // If in deep mode, loop through all tuples in current map
 @each $key, $value in $current {

        // If value is a nested map and same key from map is a nested map as well
 @if type-of($value) == "map" and type-of(map-get($map, $key)) == "map" {
          // Recursive extend
 $value: map-extend(map-get($map, $key), $value, true);
        }

        // Merge current tuple with map
 $map: map-merge($map, ($key: $value));
      }
    }
  }

 @return $map;
}
```

### 例子

```
$grid-configuration-default: (
  'columns': 12,
  'layouts': (
    'small': 800px,
    'medium': 1000px,
    'large': 1200px,
  ),
);

$grid-configuration-custom: (
  'layouts': (
    'large': 1300px,
    'huge': 1500px
  ),
);

$grid-configuration-user: (
  'direction': 'ltr',
  'columns': 16,
  'layouts': (
    'large': 1300px,
    'huge': 1500px
  ),
);

// Not deep
$grid-configuration: map-extend($grid-configuration-default, $grid-configuration-custom, $grid-configuration-user);
// -> ("columns": 16, "layouts": (("large": 1300px, "huge": 1500px)), "direction": "ltr")

// Deep
$grid-configuration: map-extend($grid-configuration-default, $grid-configuration-custom, $grid-configuration-user, true);
// -> ("columns": 16, "layouts": (("small": 800px, "medium": 1000px, "large": 1300px, "huge": 1500px)), "direction": "ltr")
```

## 最后的想法

就是这样，伙计们！显然，您不会在日常的样式表中使用它们，但是偶尔这些函数会派上用场。至少，你知道在哪里可以找到他们。；)

## 分享这篇文章