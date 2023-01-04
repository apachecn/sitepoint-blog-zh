# jquery 按索引对数组排序

> 原文：<https://www.sitepoint.com/sort-array-index/>

如何使用 JavaScript 对数组按索引值排序的快速示例。为了从性能方面分析这样做的最佳方法，我查看了一个用于排序对象的 [JS 性能测试](http://jsperf.com/sort-array-of-objects)。

```
var data = Array();
data[0] = {"apples":1, "pears":2, "oranges":3};
data[1] = {"apples":3, "pears":3, "oranges":5};
data[2] = {"apples":4, "pears":1, "oranges":6};

console.log(data);

data.sort(function(a, b){
    var a1= a.pears, b1= b.pears;
    if(a1== b1) return 0;
    return a1> b1? 1: -1;
});

console.log(data);
```

在这里，您可以看到我们已经按照“梨”值进行了排序。第一行在排序之前，第二行在排序之后:梨 1，梨 2，梨 3。
![jquery sort array by index](img/7ac3a4e2ea8a7bb44aa13225136bf733.png "jquery sort array by index")

### 对象的 JS 排序

```
//objects
var array = [{id:'12', name:'Smith', value:1},{id:'13', name:'Jones', value:2}];
array.sort(function(a, b){
    var a1= a.name, b1= b.name;
    if(a1== b1) return 0;
    return a1> b1? 1: -1;
});
```

### 数组上的 JS 排序

```
//arrays
var array =[ ['12', ,'Smith',1],['13', 'Jones',2]];
array.sort(function(a, b){
    var a1= a[1], b1= b[1];
    if(a1== b1) return 0;
    return a1> b1? 1: -1;
});
```

## 分享这篇文章