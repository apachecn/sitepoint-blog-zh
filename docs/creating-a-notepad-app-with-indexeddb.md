# 使用 indexedDB 创建记事本应用程序

> 原文：<https://www.sitepoint.com/creating-a-notepad-app-with-indexeddb/>

indexedDB 是 HTML5 中的新特性，它允许开发人员在 web 浏览器中保存数据。因此，您的应用程序可以在线和离线运行，并具有强大的查询功能。indexedDB 不同于传统的关系数据库，它是一个`objectStore`而不是行和列的集合。您只需在 indexedDB 中创建一个`objectStore`并将 JavaScript 对象存储在该存储中。此外，对存储的对象执行 CRUD 操作非常容易。本教程概述了 indexedDB API，并解释了如何使用它来创建一个简单的记事本应用程序。

在开始之前，请注意 indexedDB API 规范还没有稳定下来。但是如果你的电脑上安装了最新的火狐浏览器或谷歌浏览器，你就可以使用了。要了解哪些浏览器版本支持 API，请参见[兼容性表](http://caniuse.com/indexeddb)。

indexedDB 规范中有两种类型的 API:同步和异步。然而，我们将把重点放在异步 API 上，因为目前这是浏览器支持的唯一 API。异步意味着您在数据库上执行操作，并通过 DOM 事件在回调中接收结果。

任何笔记制作应用都有四个简单的功能:创建、阅读、更新和删除。indexedDB 提供了非常简单的 API 来执行这些操作。但是在做任何事情之前，我们需要创建一个数据库并打开它。

## 设置:

由于规范还没有稳定下来，不同的浏览器在实现中使用前缀。因此，您需要正确检查以确保浏览器支持 indexedDB。使用以下代码确保浏览器支持 indexedDB。

```
window.indexedDB = window.indexedDB || window.mozIndexedDB || window.webkitIndexedDB || 
    window.msIndexedDB;
window.IDBTransaction = window.IDBTransaction || window.webkitIDBTransaction || 
    window.msIDBTransaction;
window.IDBKeyRange = window.IDBKeyRange || window.webkitIDBKeyRange || window.msIDBKeyRange;
if (!window.indexedDB) {
    alert("Sorry!Your browser doesn't support IndexedDB");
}
```

## 打开/创建数据库:

打开一个 indexedDB 数据库就是调用`indexedDB.open()`的事情。

`var request = window.indexedDB.open("notepad",1);`

`indexedDB.open()`接受两个参数。第一个代表数据库名称。如果数据库不存在，它会创建一个新的。第二个参数是版本号。如果您将来需要更新数据库模式，您需要使用比以前版本号更高的版本号来调用`indexedDB.open()function`。在这种情况下，您需要实现`onupgradeneeded`回调，在那里您可以更新数据库模式并创建/删除`objectStores`。

创建数据库是第一步。但是要存储一些东西，你需要有一个`objectStore`。当您创建一个数据库时，您可能也想创建一个`objectStore`。当一个数据库最初被创建时，`onupgradeneeded`回调被触发，在这里你可以创建一个`objectStore`。

```
var database;

var request = window.indexedDB.open("notepad",1);

request.onerror = function(event) {

console.log(event.target.errorCode);

};

request.onsuccess = function(event) {

    database=request.result;

};

request.onupgradeneeded = function(event) {

    var db = event.target.result;

    var objectStore = db.createObjectStore("notes", { keyPath:  "id",autoIncrement:true});

};
```

在上面的代码示例中，我们使用数据库名称 notepad 和版本号 1 调用`indexedDB.open()`。该方法返回一个`IDBOpenDBRequest`。当数据库打开请求成功时，触发`request.onsuccess`回调。请求的结果属性是一个`IDBDatabase`的实例，我们将它分配给变量数据库以备后用。

在`onupgradeneeded`回调中，我们得到一个对数据库的引用，并用它来创建一个名为`notes`的新`objectStore`。`createObjectStore()`函数还接受第二个参数。在这种情况下，我们定义了一个名为`id`的`keyPath`，它惟一地标识了我们商店中的一个对象。此外，我们还希望 id 为`autoIncrementing`。

## 在对象存储中添加/更新项目:

假设我们想在存储中保存一个便笺。该对象应该有字段，如:标题，正文和创建日期。要存储对象，请使用以下代码:

```
var note={title:”Test Note”, body:”Hello World!”, date:”01/04/2013”};

var transaction = database.transaction(["notes"], "readwrite");

var objectStore = transaction.objectStore("notes");

var request=objectStore.put(note);

request.onsuccess = function(event) {

    //do something here

};
```

`database.transaction()`将一个数组作为第一个参数，该数组表示该事务跨越的`objectStores`的名称。第二个参数决定交易的类型。如果你没有传递第二个参数，你将得到一个只读的事务。因为我们想添加一个新的条目，所以我们将`readwrite`作为第二个参数传递。由于这个调用，我们得到了一个事务对象。`transaction.objectStore()`选择一个`objectStore`进行手术。最后`objectStore.put()`将对象添加到存储中。您也可以使用`objectStore.add()`添加一个对象。但是如果我们试图添加一个与现有条目的`id`相同的新对象，前者将更新存储中的一个对象。

## 从商店中删除项目:

从存储中删除对象非常简单明了。

```
var request = database.transaction(["notes"], "readwrite") .objectStore("notes").delete(20);

request.onsuccess = function(event) {

    //handle success

};
```

上面的代码从存储中删除了一个`id`为 20 的对象。

## 查询存储中的所有对象:

对于任何数据库驱动的应用程序，显示所有存储的条目是很常见的。在 indexedDB 中，您可以获取存储在存储中的对象，并借助光标遍历它们。

```
var objectStore = database.transaction("notes").objectStore("notes");

objectStore.openCursor().onsuccess = function(event) {

    var cursor = event.target.result;

    if (cursor) {

        alert("Note id: "+cursor.key+", Title: "+cursor.value.title);

        cursor.continue();

    }

};
```

上面的代码非常简单易懂。`openCursor()`函数可以接受几个参数。您可以通过传递适当的参数来控制返回结果的范围和迭代的方向。游标对象是请求的结果。如果您期望有多个对象，并且想要遍历它们，那么应该调用`cursor.continue()`。这意味着只要有更多的数据`onsuccess`回调就被触发，前提是你调用`cursor.continue()`。

所以，这是你在使用 indexedDB 开发记事本应用之前应该知道的全部。现在，我将一步一步地展示如何创建应用程序。

## 初始 HTML 标记:

```
<html>

<head><title>Simple Notepad Using indexedDB</title>

</head>

<body>

<div id="container">

    <h3 id="heading">Add a note</h3>

    <input type="hidden" value="0" id="flag"/>

    <a href="#" id="add"><img src="add.png" onclick="createNote(0)"/> New</a>

    <a href="#" id="back"><img src="back.png" onclick="goBack()"/></a>

    <div id="notes"></div>

    <div id="editor" contenteditable="true"></div>

</div>

</body>

</html>
```

### 解释:

我们有两个部门:笔记和编辑器。第一个用于显示现有笔记的列表，第二个用作编写笔记的编辑器。编辑器 div 最初是不可见的。当用户点击**添加**按钮时，我们隐藏注释 div 并显示编辑器 div。你应该记住，通过设置`contenteditable="true"`我们使一个 div 可编辑。我们还有一个带有 id 标志的隐藏输入字段。这将在教程的后面使用。

## JavaScript:

```
<script type="text/javascript">

var database;

window.indexedDB = window.indexedDB || window.mozIndexedDB || window.webkitIndexedDB || 
    window.msIndexedDB;

window.IDBTransaction = window.IDBTransaction || window.webkitIDBTransaction || 
    window.msIDBTransaction;

window.IDBKeyRange = window.IDBKeyRange || window.webkitIDBKeyRange || window.msIDBKeyRange;

if (!window.indexedDB) {

    alert("Sorry!Your browser doesn't support IndexedDB");

}

function init(){

    var request = window.indexedDB.open("notepad",1);

    request.onerror = function(event) {

    console.log(event.target.errorCode);

};

request.onsuccess = function(event) {

    database=request.result;

    showNotes();

};

request.onupgradeneeded = function(event) {

    var db = event.target.result;

    var objectStore = db.createObjectStore("notes", { keyPath: "id",autoIncrement:true});

    };

}

function createNote(id){

    document.getElementById("editor").style.display="block";

    document.getElementById("editor").focus();

    document.getElementById("back").style.display="block";

    document.getElementById("add").style.display="none";

    document.getElementById("notes").style.display="none";

    if(parseInt(id)!=0){

    database.transaction("notes").objectStore("notes").get(parseInt(id))

    .onsuccess = function(event) {

document.getElementById("editor").innerHTML=event.target.result.body;

    document.getElementById("flag").value=id;

};

}

}

function goBack(){

    var note={};

    note.body=document.getElementById("editor").innerHTML;

    note.title=getTitle(note.body);

    note.date=getDate();

    var flag=parseInt(document.getElementById("flag").value);

    if(flag!=0)

      note.id=flag;

    if(note.title.trim()==="")

        window.location.href="index.html";

    else

        addNote(note);

    }

function getDate(){

    var date=new Date();

    var month=parseInt(date.getMonth())+1;

    return date.getDate()+"/"+month+"/"+date.getFullYear();

}

function getTitle(body){

    var body = body.replace(/(<([^>]+)>)/ig,"");

    if(body.length > 20) body = body.substring(0,20)+". . .";

        return body;

}

function addNote(note){

    var transaction = database.transaction(["notes"], "readwrite");

    var objectStore = transaction.objectStore("notes");

    var request=objectStore.put(note);

    request.onsuccess = function(event) {

        document.getElementById("flag").value="0";

        window.location.href="index.html";

        };

    }

function showNotes(){

var notes="";

var objectStore = database.transaction("notes").objectStore("notes");

objectStore.openCursor().onsuccess = function(event) {

    var cursor = event.target.result;

    if (cursor) {

        var link="<a class="notelist" id=""+cursor.key+"" href="#">"+cursor.value.title+"</a>"+" 
        <img class="delete" src="delete.png" height="30px" id=""+cursor.key+""/>";

        var listItem="<li>"+link+"</li>";

        notes=notes+listItem;

        cursor.continue();

    }

    else

    {

    document.getElementById("notes").innerHTML="<ul>"+notes+"</ul>";

    registerEdit();

    registerDelete();

    }

};

}

function deleteNote(id){

var request = database.transaction(["notes"], "readwrite")

        .objectStore("notes")

        .delete(id);

request.onsuccess = function(event) {

    window.location.href="index.html";

};

}

function registerEdit(){

var elements = document.getElementsByClassName('notelist');

for(var i = 0, length = elements.length; i < length; i++) {

    elements[i].onclick = function (e) {

        createNote(this.id);

    }

}

}

function registerDelete(){

var deleteButtons = document.getElementsByClassName('delete');

    for(var i = 0, length = deleteButtons.length; i < length; i++){

        deleteButtons[i].onclick=function(e){

        deleteNote(parseInt(this.id));

        }

    }

}

window.addEventListener("DOMContentLoaded", init, false);

</script>
```

### 解释:

`init`方法进行必要的初始化。它创建/打开数据库，并在第一次创建数据库时创建一个`objectStore`。数据库成功打开后，我们获得对它的引用，并将其存储在数据库变量中。

`showNotes()`功能显示用户创建的便笺列表。我们启动一个事务，并获取存储中的 note 对象。然后我们创建一个注释标题的无序列表，并最终显示在包含`id`注释的 div 中。我们也称这两个函数为`registerEdit()`和`registerDelete()`。第一个函数将一个点击事件监听器附加到笔记标题上，笔记标题是具有类 notelist 的简单链接，这样当有人点击标题时就可以编辑笔记。后一个函数向笔记标题旁边的删除按钮(简单图像)添加了一个 click 事件监听器。这样，当有人点击删除按钮时，我们就可以删除一条注释。`deleteNote()`函数执行删除操作。

`createNote()`功能显示一个编辑器来创建一个新的注释或更新一个现有的注释。它接受一个参数。如果它是 0，我们知道我们想要创建一个新的笔记。否则，我们启动一个事务来获取现有笔记的内容。我们将便笺的`id`传递给`objectStore.get()`，在`onsuccess`中我们获取便笺的主体。然后，我们用获取的注释体填充编辑器。此外，我们将隐藏输入标志设置为在`goBack()`函数中使用的 id。当用户写完笔记后想返回时，就会触发这个方法。这是我们在商店里保存便条的地方。

在`goBack()`函数中，我们创建了一个新对象，并设置了它的标题、正文和日期属性。标题作为正文的前 20 个字符。然后找出隐藏标志的值。如果它不是 0，我们知道我们想要更新一个现有的笔记。因此，我们设置所创建对象的`id`属性。否则就不需要`id`属性，因为该对象将是商店中的一个新条目。最后`addNote()`函数被调用，以 note 对象作为参数。`addNote()`函数只是启动一个在存储中添加/更新对象的事务。如果交易成功，我们将用户带回到他/她可以看到已创建笔记列表的地方。

你可以在这里试用一下演示应用[。我已经在 Chrome 25 和 Firefox 20 中测试了该应用程序。](http://extremecss.com/demos/notepad)

indexedDB 是 HTML5 中一个很棒的 API，当它和 app cache 一起使用时会非常强大。Mozilla 有一些关于 indexedDB 的有趣且非常有用的信息。如果你想知道更多，一定要去看看。

如果你不能得到一些东西，请在评论中告诉我。

## 分享这篇文章