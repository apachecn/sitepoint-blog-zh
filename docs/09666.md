# 在线 JSON 树查看器工具–教程

> 原文：<https://www.sitepoint.com/online-json-tree-viewer/>

我开发了一个有用的工具插件，它可以接受 JSON 对象，并将数据呈现在树层次结构中。非常适合在大型 JSON 对象中查找节点的路径。

## 特征

*   通过上传 JSON 文件或复制粘贴来输入您的 JSON。
*   可展开的树视图。
*   将鼠标悬停在节点上以查看变量的路径。
*   单击以复制节点的完整路径。
*   为复制节点路径指定自定义分隔符。

### 示例创建 JSON 树调用

如果你想创建你自己的树，这就是你创建它们的方法。JSONTREEVIEWER 是主命名空间。

```
$(function () {
    //Initialise JQUERY4U JSON Tree Viewer
    JSONTREEVIEWER.init();

    //Events to load example files
    $('#example1').bind('click', function () {
        JSONTREEVIEWER.processJSONTree('one-level.json');
    });
});
```

### 处理 JSON 树的主函数

这个函数决定了从哪里获取 JSON)文件上传；或者 2)复制和粘贴；3)示例文件。

```
/*Load the JSON file either by upload or example file and process tree*/
processJSONTree: function (filename) {
    $('#loading').show();
    var data = '',
        branches = '';
    if (filename === 'copyandpastejson') {
        var copypastejson = $('#copyandpastejson').val(); /*validate JSON*/
        if (JSONTREEVIEWER.isValidJSON(copypastejson)) {
            data = copypastejson;
        } else {
            return false;
        }
        if (data === false) {
            return false;
        } /*Build JSON Tree*/
        JSONTREEVIEWER.buildTree(JSONTREEVIEWER.processNodes(jQuery.parseJSON(data)), filename);
    } else {
        //get the JSON file from file upload
        $.ajax({
            type: 'GET',
            url: filename,
            async: false,
            beforeSend: function (x) {
                if (x && x.overrideMimeType) {
                    x.overrideMimeType('application/j-son;charset=UTF-8');
                }
            },
            dataType: 'json',
            success: function (data) { /*Build JSON Tree*/
                JSONTREEVIEWER.buildTree(JSONTREEVIEWER.processNodes(data), filename);
            },
            error: function (e) { /*Invalid JSON*/
                alert('Invalid JSON: ' + e.responseText);
                JSONTREEVIEWER.showErrorMsg();
                return false;
            }
        });
    }
},
```

### 构建树函数

这个函数从分支构造树，并在页面上显示它们。

```
/*Build JSON Tree*/
buildTree: function (branches, filename) {
    //console.log('branches' + branches);
    if (typeof branches !== 'undefined' || branches !== '') {
        $('#browser').empty().html(branches);
        $('#browser').treeview({
            control: '#treecontrol',
            add: branches
        });
        $('#selected_filename').html('(' + filename + ')');
        $('#loading').hide();
        $('#browser-text').hide();
    } else {
        $('#selected_filename').html('Please select JSON file above...');
        $('#loading').hide();
    }
},
```

### JSON 分支递归函数

这个函数相当复杂，需要一段时间来编写代码。它基本上递归地获取每个 JSON 对象，确定其类型并为分支创建 HTML。

```
/*Process each node by its type (branch or leaf)*/
processNodes: function (node) {
    var return_str = '';
    switch (jQuery.type(node)) {
    case 'string':
        if ($('#hierarchy_chk').is(':checked')) {
            return_str += '

*   ' +节点+ ' 

';
        } else {
            return_str += '

*   ' +节点+ ' 

';
        }
        break;
    case 'array':
        $.each(node, function (item, value) {
            return_str += JSONTREEVIEWER.processNodes(this);
        });
        break;
    default:
        /*object*/
        $.each(node, function (item, value) {
            if ($('#hierarchy_chk').is(':checked')) {
                return_str += '

*   '+item+''；return _ str+= jsontreeviewer . process nodes(this)；return_str += '

';
            } else {
                return_str += JSONTREEVIEWER.processNodes(this);
            }
        });
    } /*Clean up any undefined elements*/
    return_str = return_str.replace('undefined', '');
    return return_str;
},
```

### 检查 JSON 是否有效

帮助器函数来检查 JSON 是否有效，如果无效就显示一条消息。

```
/*Helper function to check if JSON is valid*/
isValidJSON: function (jsonData) {
    try {
        jsonData = jQuery.parseJSON(jsonData);
        //console.log('valid json');
        return true;
    } catch (e) {
        //console.log('invalid json');
        alert(e);
        JSONTREEVIEWER.showErrorMsg();
        return false;
    }
},
```

### 获取节点的路径

这个函数递归地搜索 HTML 来构造节点的分支路径。

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
        if (html.search('*   +路径)；} else { return this.parent()。getPath(路径)；} } });
```

### 事件

当用户上传一个 JSON 文件或者悬停在树上时处理事件的几个函数。

```
/*change event when user changes file upload input*/
$('#loadfile').live('change', function () {
    JSONTREEVIEWER.processJSONTree($(this).val());
});

/*store nodepath value to clipboard	(copy to top of page)*/
$('#browser li').live('click', function () {
    var path = $('#pathtonode').html();
    var pathdelim = $('#pathdelim_chk').val();
    path = path.replace(/ &gt; /g, pathdelim);
    JSONTREEVIEWER.addtoppath(path);
});

/*mouse IN hover to show path of node*/
$('#browser li span').live('mouseenter', function () {
    $('#pathtonode').html(JSONTREEVIEWER.getNodePath(this));
    $('#pathtonode').show();
});

/*mouse OUT hover to show path of node*/
$('#browser li span').live('mouseleave', function () {
    $('#pathtonode').empty();
    $('#pathtonode').hide();
});
```

### 要求

我已经使用了 jquery.treeview.async.js 插件来创建可扩展的树视图。

## 分享这篇文章