# jQuery 获取元素的层次结构路径

> 原文：<https://www.sitepoint.com/jquery-hierarchy-path-element/>

jQuery 函数通过确定元素的祖先并遍历其父元素直到到达树的根来获取元素的层次路径。

```
/*jQuery function to create path function used to get the path of the node in the tree*/
jQuery.fn.extend({
    getPath: function (path) { /*The first time this function is called, path won't be defined*/
        if (typeof path == 'undefined') path = ''; /*Add the element name*/
        var cur = this.get(0).nodeName.toLowerCase();
        var id = this.attr('id'); /*Add the #id if there is one*/
        if (typeof id != 'undefined') { /*escape goat*/
            if (id == 'browser') {
                return path;
            }
        }
        var html = this.html();
        if (html.search('
```

*   `' + path);         } else {             return this.parent().getPath(path);         }     } });`

## 分享这篇文章