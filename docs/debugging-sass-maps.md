# 调试 Sass 映射

> 原文：<https://www.sitepoint.com/debugging-sass-maps/>

地图很棒，但是地图并不那么容易调试。当您拥有简单的地图时，通常很容易理解正在发生的事情，但是当您使用巨大的和/或嵌套的、可能动态更新的地图时，就很难跟踪发生了什么。

…然后，突然之间，代码中的某个地方出现了一个错误。破坏你正在开发的网站。

不要害怕我的朋友，我有一个可能的解决办法。

## 那`inspect()`或者`@debug`呢？

前几天，网络开发人员 Stuart Trann [问](https://twitter.com/estwo/status/522504333967908864)我们如何调试一张地图，看看它看起来像什么。

我首先想到的是`inspect()`函数。这个功能几乎是在地图出现的同时添加的，目的是检查地图的内容。

您可能已经注意到，如果您尝试将地图显示为 CSS 值，您会收到以下消息:

> (sass: map)不是有效的 CSS 值。

该死。所以这就是为什么`inspect()`函数已经实现到 Sass core 了。所以你可以看到这样的东西:

```
test {
 inspect: (sass: map);
}
```

`inspect()`的一个问题是它将地图显示为一条线，这很不方便，尤其是当你有很多键的时候。人们可能会想到`@debug`，它打算在控制台中打印一些内容，但这并没有多大帮助，因为它也将内容保持在一行。

是时候建造一些能帮助我们的东西了。

## 混合所有的东西！

如果我们利用 CSS 在显示键/值对时非常简洁的事实，会怎么样呢？我们可以在 map 上循环，并将键和值显示为虚拟选择器中的声明。我们开始吧！

```
/// Prints a map as a CSS rule
/// @param {Map} $map

@mixin debug-map($map) {
 @at-root {
        __properties__ {
 @each $key, $value in $map {
 #{$key}: $value;
            }
        }
    }
}
```

*注意:我们使用`__properties__`来表明这是一些内部调试的东西，有点像 JavaScript 中的`__proto__`。*

还不错。现在，如果我们有一个嵌套的地图呢？Sass 将在`$value`显示屏上失败。所以我们可以想出一些奇怪而复杂的逻辑，或者我们可以保持简单，使用`inspect()`，就像这样:

```
/// Prints a map as a CSS rule
/// @param {Map} $map

@mixin debug-map($map) {
 @at-root {
        __properties__ {
 @each $key, $value in $map {
 #{$key}: inspect($value);
            }
        }
    }
}
```

这样，如果我们遇到嵌套映射，它将显示为任何其他值。如果我们现在想调试这个嵌套映射，我们应该直接在这个特定值上使用`debug-map` mixin。

## 添加一些额外的信息

如果我们决定添加一些额外的信息，比如长度、深度或被检查地图的字符串表示，会怎么样？现在，那可能是有帮助的，不是吗？

为此，我们需要稍微调整一下混音。我们可以打印一个专有的`@`-指令来分组我们需要的所有东西，而不是直接输出一个选择器，就像这样:

```
@mixin debug-map($map) {
 @at-root {
 @debug-map {
 __toString__: inspect($map);
 __length__: length($map);
 __keys__: map-keys($map);
            __properties__ {
 @each $key, $value in $map {
 #{$key}: inspect($value);
                }
            }
        }
    }
}
```

为了计算深度，我们需要一个额外的工具，因为没有内置的函数。原来不久前我也做了这样一个函数，但从未在博客上发表过。我想是时候了！

```
/// Compute the maximum depth of a map
/// @param {Map} $map
/// @return {Number} max depth of `$map`

@function depth($map) {
 $level: 1;

 @each $key, $value in $map {
 @if type-of($value) == "map" {
 $level: max(depth($value) + 1, $level);
    }
  }

 @return $level;
}
```

这是一个递归函数，遍历映射中的每个键/值对，直到找到一个映射，在这种情况下，它调用自己，以此类推。然后，它返回一个数字:地图的深度。让我们用这些信息更新我们的`debug-map` mixin:

```
@mixin debug-map($map) {
 @at-root {
 @debug-map {
 __toString__: inspect($map);
 __length__: length($map);
 __depth__: depth($map);
 __keys__: map-keys($map);
            __properties__ {
 @each $key, $value in $map {
 #{$key}: inspect($value);
                }
            }
        }
    }
}
```

我可以建议的最后一个添加是在相关的键之前打印 map 中每个值的类型。在某些情况下，这可能很方便:

```
@mixin debug-map($map) {
 @at-root {
 @debug-map {
 __toString__: inspect($map);
 __length__: length($map);
 __depth__: depth($map);
 __keys__: map-keys($map);
            __properties__ {
 @each $key, $value in $map {
                    #{'(' + type-of($value) + ') ' + $key}: inspect($value);
                }
            }
        }
    }
}
```

## 让我们试试这个！

让我们看看下面的测试地图:

```
$person: (
    'name': 'Hugo Giraudel',
    'location': 'France',
    'age': 22,
    'glasses': true,
    'kids': null,
    'skills': ('HTML', 'CSS', 'Sass', 'JavaScript'),
    'languages': (
        'French': 'native',
        'English': 'fluent',
        'Spanish': 'notions'
    )
);
```

现在，让我们来调试它！

```
@include debug-map($person);
```

一旦编译，它将产生以下结果:

```
@debug-map {
 __toString__: ("name": "Hugo Giraudel", "location": "France", "age": 22, "glasses": true, "has-kids": false, "skills": ("HTML", "CSS", "Sass", "JavaScript"), "languages": (("French": "native", "English": "fluent", "Spanish": "notions")));
 __length__: 7;
 __depth__: 2;
 __keys__: "name", "location", "age", "glasses", "has-kids", "skills", "languages";
  __properties__ {
    (string) name: "Hugo Giraudel";
    (string) location: "France";
    (number) age: 22;
    (bool) glasses: true;
    (null) kids: false;
    (list) skills: "HTML", "CSS", "Sass", "JavaScript";
    (map) languages: ("French": "native", "English": "fluent", "Spanish": "notions");
  }
}
```

## 最后的想法

给你，伙计们。我希望这可能会派上用场，如果你曾经与地图斗争。请随意改进或添加这个小 mixin 的额外功能。我的目标是让它尽可能简单，不要过于拥挤。在大多数情况下，即使是地图深度看起来也有点夸张，但是你知道…很好的补充！

## 分享这篇文章