# 使用 jQuery 和本地存储构建待办事项列表

> 原文：<https://www.sitepoint.com/building-list-jquery-local-storage/>

我们都有这样的日子，我们有很多事情要完成，但没有办法知道我们是否会完成所有的事情。在这些情况下，待办事项清单非常有用。有很棒的移动应用程序，在线工具，甚至 WordPress 插件来完成这个任务，但是你有没有想过自己创建一个要花多长时间？

在本教程中，我们将创建一个基本的待办事项列表。任务分为三类——待定、进行中和已完成。我们的应用程序将允许创建新任务，默认情况下，这些任务将被归入 pending 类别。使用拖放技术，任务可以很容易地在类别之间移动。当然，任务也可以删除。

## 先决条件

该项目中使用了以下库。

*   jQuery 1.10
*   jQuery UI 1.10.3
*   引导程序 2.2.2

## 创建标记和 CSS

在通过 JavaScript 添加功能之前，让我们创建应用程序的基本外观。

```
<div class="task-list" id="pending">
  <h3>Pending</h3>
  <!-- Sample task added manually to check look -->
  <div class="todo-task">
    <div class="task-header">Sample Header</div>
    <div class="task-date">25/06/1992</div>
    <div class="task-description">Lorem Ipsum Dolor Sit Amet</div>
  </div>
</div>

<div class="task-list" id="inProgress">
  <h3>In Progress</h3>
</div>

<div class="task-list" id="completed">
  <h3>Completed</h3>
</div>

<div class="task-list">
  <h3>Add a task</h3>
  <form id="todo-form">
    <input type="text" placeholder="Title" />
    <textarea placeholder="Descrtipion"></textarea>
    <input type="text" placeholder="Due Date (dd/mm/yyyy)" />
    <input type="button" class="btn btn-primary" value="Add Task" />
  </form>

  <input type="button" class="btn btn-primary" value="Clear Data" />

  <div id="delete-div">Drag Here to Delete</div>
</div>
```

接下来，使用下面的 CSS 为元素添加一些样式。

```
.task-list {
  width: 250px;
  float: left;
  margin: 0 5px;
  background-color: #e3e3e3;
  min-height: 240px;
  border-radius: 10px;
  padding-bottom: 15px;
}

.task-list input, .task-list textarea {
  width: 240px;
  margin: 1px 5px;
}

.task-list input {
  height: 30px;
}

.todo-task {
  border-radius: 5px;
  background-color: #fff;
  width: 230px;
  margin: 5px;
  padding: 5px;
}

.task-list input[type="button"] {
  width: 100px;
  margin: 5px;
}

.todo-task > .task-header {
  font-weight: bold;
}

.todo-task > .task-date {
  font-size: small;
  font-style: italic;
}

.todo-task > .task-description {
  font-size: smaller;
}

h3 {
  text-align: center;
}

#delete-div {
  background-color: #fff;
  border: 3px dotted #000;
  margin: 10px;
  height: 75px;
  line-height: 75px;
  text-align: center;
}
```

我们的静态待办事项页面应该如下图所示。

![Todo list demo](img/b3be2dc34387c8ebe6f7bc70e7a63891.png "Todo list demo")

## 定义 JavaScript 常量

在本教程中，我们将引用某些常量来避免硬编码值。这些常量如下所示。

```
var defaults = {
  // CSS selectors and attributes that would be used by the JavaScript functions
  todoTask: "todo-task",
  todoHeader: "task-header",
  todoDate: "task-date",
  todoDescription: "task-description",
  taskId: "task-",
  formId: "todo-form",
  dataAttribute: "data",
  deleteDiv: "delete-div"
}, codes = {
  "1" : "#pending", // For pending tasks
  "2" : "#inProgress",
  "3" : "#completed"
};
```

## 创建任务

任务是使用以下 JavaScript 函数创建的。

```
// Add Task
var generateElement = function(params) {
  var parent = $(codes[params.code]),
      wrapper;

  if (!parent) {
    return;
  }

  wrapper = $("<div />", {
    "class" : defaults.todoTask,
    "id" : defaults.taskId + params.id,
    "data" : params.id
  }).appendTo(parent);

  $("<div />", {
    "class" : defaults.todoHeader,
    "text": params.title
  }).appendTo(wrapper);

  $("<div />", {
    "class" : defaults.todoDate,
    "text": params.date
  }).appendTo(wrapper);

  $("<div />", {
    "class" : defaults.todoDescription,
    "text": params.description
  }).appendTo(wrapper);
};
```

下面的代码示例显示了如何生成单个任务。

```
generateElement({
  id: "123",
  code: "1",
  title: "My Uber Important Task",
  date: "5/2/2014",
  description: "I have to do a lot of steps to implement this task!"
});
```

## 删除任务

删除任务相当简单，可以使用下面的函数来完成。

```
var removeElement = function(params) {
  $("#" + defaults.taskId + params.id).remove();
};
```

## 将任务保存在本地存储中

我们创建的任务可以使用数据库、cookies 或许多其他技术来存储。然而，在这个应用程序中，为了简单起见，我们将使用 HTML5 的本地存储。在 JavaScript 中，变量`localStorage`存储所有这些数据。下面的代码示例显示了如何从本地存储中检索待办事项列表数据。

```
var data = JSON.parse(localStorage.getItem("todoData"));
```

每个任务都存储在`data`变量中。下面显示了一个任务对象示例。

```
{
  id : id, // Unique ID; timestamp is used here
  code: "1", // Code identifying the category
  title: title, // Title of the task
  date: date, // Due date
  description: description // Description of the task
}
```

我们使用以下代码更新本地存储中保存的数据。

```
localStorage.setItem("todoData", JSON.stringify(data));
```

## 提交待办事项表单

当提交待办事项表单时，会创建一个新任务并将其添加到本地存储中，页面内容也会更新。下面的函数实现了这个功能。

```
var addItem = function() {
  var inputs = $("#" + defaults.formId + " :input"),
      errorMessage = "Title can not be empty",
      id, title, description, date, tempData;

  if (inputs.length !== 4) {
    return;
  }

  title = inputs[0].value;
  description = inputs[1].value;
  date = inputs[2].value;

  if (!title) {
    generateDialog(errorMessage);
    return;
  }

  id = new Date().getTime();

  tempData = {
    id : id,
    code: "1",
    title: title,
    date: date,
    description: description
  };

  // Saving element in local storage
  data[id] = tempData;
  localStorage.setItem("todoData", JSON.stringify(data));

  // Generate Todo Element
  generateElement(tempData);

  // Reset Form
  inputs[0].value = "";
  inputs[1].value = "";
  inputs[2].value = "";
};
```

## 实现拖放

jQuery UI 提供了拖放功能。我们需要让每个任务都是可拖动的，这三个类别都是可丢弃的。要删除一个任务，我们需要默认隐藏删除区域，并在拖动项目时显示它。因此，我们首先稍微修改一下`generateElement()`函数，首先使待办事项列表项可拖动，然后使删除区域在该项被药物处理时可见。

```
$("." + defaults.todoTask).draggable();

// Add Task
var generateElement = function(params) {
  wrapper.draggable({
    start: function() {
      $("#" + defaults.deleteDiv).show();
    },
    stop: function() {
      $("#" + defaults.deleteDiv).hide();
    }
  });
...
};
```

其次，我们需要为每个类别添加`droppable()`函数，因为元素应该放在这三个区域中的任何一个。

```
// Adding drop function to each category of task
$.each(codes, function(index, value) {
  $(value).droppable({
    drop: function(event, ui) {
      var element = ui.helper,
          css_id = element.attr("id"),
          id = css_id.replace(options.taskId, ""),
          object = data[id];

      // Removing old element
      removeElement(object);

      // Changing object code
      object.code = index;

      // Generating new element
      generateElement(object);

      // Updating Local Storage
      data[id] = object;
      localStorage.setItem("todoData", JSON.stringify(data));

      // Hiding Delete Area
      $("#" + defaults.deleteDiv).hide();
    }
  });
});
```

第三，我们需要添加一些代码，以便在任务被拖放到删除区域时删除它们。

```
// Adding drop function to delete div
$("#" + options.deleteDiv).droppable({
  drop: function(event, ui) {
    var element = ui.helper,
        css_id = element.attr("id"),
        id = css_id.replace(options.taskId, ""),
        object = data[id];

    // Removing old element
    removeElement(object);

    // Updating local storage
    delete data[id];
    localStorage.setItem("todoData", JSON.stringify(data));

    // Hiding Delete Area
    $("#" + defaults.deleteDiv).hide();
  }
});
```

## 结论

最终代码可在 [GitHub](https://github.com/sdaityari/to-do-list) 上获得。你也可以看看该项目的现场[演示](http://sdaityari.github.io/to-do-list/)。

## 分享这篇文章