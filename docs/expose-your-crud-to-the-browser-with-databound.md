# 通过数据绑定向浏览器公开 Rails CRUD

> 原文：<https://www.sitepoint.com/expose-your-crud-to-the-browser-with-databound/>

![](img/2ed85819fc36c42730d134f03d3a7062.png)

随着我们继续构建越来越多的 JavaScript 应用程序，对 API 的需求将会持续增长。编写一个成熟的 API 需要大量的计划和时间。但有时，我们在 API 中需要的只是普通的 CRUD 操作。为每一种资源编写 CRUD APIs 可能是一项非常乏味且重复的任务。Ruby 社区就是这样，一些有事业心的开发人员已经尝试解决这个乏味的问题。今天，我们将尝试一个 gem，它将简化在 Rails 应用程序中创建这些基于 CRUD 的 API。让我们开始吧。

## 创建 Rails 应用程序

我们将创建一个带有联系模型的基本 Rails 应用程序。该应用程序将使用 MRI Ruby、Rails 4.2 和 SQLite 来保持简单。首先，用 [RVM](https://rvm.io) 或 [rbenv](http://rbenv.org) 安装 MRI 红宝石。

切换到 Ruby 和`gem install rails`获得最新的 Rails gem。现在创建一个新的 Rails 应用程序，如下所示:

```
rails new databound_rails -T 
```

生成应用程序后，创建一个联系模型:

```
cd databound_rails
rails g model Contact name:string address:string city:string phone:string 
```

迁移数据库:

```
rake db:migrate 
```

现在我们已经有了接触模型。我们稍后将创建 CRUD 接口。

## 安装数据绑定

现在我们将把[数据绑定](http://databound.me/) gem 集成到我们的 Rails 应用程序中。我们还将添加“lodash-rails”gem，因为数据绑定依赖于它。像这样:

```
gem 'databound', '3.1.3'
gem 'lodash-rails', '3.10.1' 
```

出于兼容性原因，我们使用“lodash”的“3.x”版本。

安装两个 gem:

```
bundle install 
```

Databound 附带了一个生成器，用于将所需的文件添加到我们的应用程序中，所以运行它:

```
rails g databound:install 
```

这将 **databound.js** 添加到我们的资产管道中。您需要在**app/assets/JavaScript s/application . js**中手动添加“lodash ”,以便它被资产管道选中:

```
//= require lodash
//= require databound 
```

数据绑定已经添加到 **application.js** 中，但是你需要记住在数据绑定之前需要“lodash”。

## 配置数据绑定

让我们在联系模型中使用数据绑定。首先，修改 **config/routes.rb** 为联系模型添加数据绑定路由:

```
Rails.application.routes.draw do
  databound :contacts
end 
```

如您所见，我们为联系模型添加了一个数据绑定路由。

如果没有找到，Databound 将在运行时自动生成一个名为`ContactsController`的控制器。对于简单的模型，这个特性可以节省为每个模型编写控制器的时间。我们还可以在这里指定 API 可访问的列，如下所示:

```
databound :contacts, columns: [:name, :address, :city, :phone] 
```

但是我们将创建一个`ContactsController`，因为我们将探索由数据绑定提供的更高级的选项，因此将在控制器本身中配置列。

让我们用下面的代码`ContactsController`:

```
class ContactsController < ApplicationController
  databound do
    model :contact

    columns :name, :address, :city, :phone
  end
end 
```

这里我们将`model`定义为`:contact`，以及可以从 API 访问的`columns`，而不是在 **routes.rb** 中访问。现在，当我们调用 API 时，只有这些指定的列可以被访问或修改。

## 构建前端

我们已经准备好了后端。让我们创建一个简单的基于 jQuery 的前端来测试 API。首先，添加一个动作到我们的`ContactsController`来显示联系人页面。

在**app/controllers/contacts _ controller . Rb**中，添加以下内容:

```
def index
end 
```

您需要在 **config/routes.rb** 中为该动作添加一个路由:

```
get 'contacts' => 'contacts#index' 
```

使用以下代码在**app/views/contacts/index . html . erb**中创建动作视图:

```
<h1>Contacts</h1>
<table border="0">
  <tr>
    <td colspan="2"><h3>Create Contact</h3></td>
  </tr>
  <tr>
    <td><strong>Name:</strong></td>
    <td><input name="txtName" id="txtName" /></td>
  </tr>
  <tr>
    <td><strong>Address:</strong></td>
    <td><input name="txtAddress" id="txtAddress" /></td>
  </tr>
  <tr>
    <td><strong>City:</strong></td>
    <td><input name="txtCity" id="txtCity" /></td>
  </tr>
  <tr>
    <td><strong>Phone:</strong></td>
    <td><input name="txtPhone" id="txtPhone" /></td>
  </tr>
  <tr>
    <td colspan="2" align="center"><button name="createContact" id="createContact">Create Contact</button></td>
  </tr>
</table>

<table border="0" id="tblContacts">
  <thead>
    <th>Name</th>
    <th>Address</th>
    <th>City</th>
    <th>Phone</th>
  </thead>
  <tbody>
  </tbody>
</table> 
```

在这里，我们创建了一个简单的界面来创建一个新的联系人，并在下面的表格中显示它。现在，我们将在**应用程序/资产/JavaScript**中创建 **contacts.js** 来实现视图:

```
var Contact = new Databound('/contacts');
$(document).ready(function(){
  $('#createContact').on('click', function() {
    Contact.create({ name: $('#txtName').val(), address: $('#txtAddress').val(), city: $('#txtCity').val(), phone: $('#txtPhone').val() }).then(function(new_contact) {
      var table = $('#tblContacts tbody');
      var row = "<tr>";
      row += "<td>" + new_contact.name + "</td>";
      row += "<td>" + new_contact.address + "</td>";
      row += "<td>" + new_contact.city + "</td>";
      row += "<td>" + new_contact.phone + "</td>";
      row += "</tr>";
      $(table).append(row);
    });
  });
}); 
```

我们已经定义了`Contact` Javascript 对象，我们将使用它来调用联系模型上的 CRUD 操作:

```
var Contact = new Databound('/contacts'); 
```

接下来，`createContact`按钮有一个简单的事件处理程序来调用`Contact.create`，传递来自姓名、地址、城市和电话字段的数据。最后，将创建的记录追加到表中。

让我们测试一下这是否可行。启动 Rails 服务器:

```
rails s 
```

点击[http://localhost:3000/ConA tcts](http://localhost:3000/contacts)并创建一些记录。应该创建记录并在表中可见。

![](img/7de5e4269e8ca34e90e082753787b02b.png)

我们现在已经成功地将数据绑定集成到我们的 API 中。让我们探索一下数据绑定提供的其他功能。

## 搜索 API

数据绑定提供了三个客户端 API 来搜索记录。让我们一个一个来看。

### `where` API

```
Contact.where({ name: 'Devdatta' }).then(function(contacts) {
 alert('Contacts named Devdatta');
}); 
```

这里我们调用`where` API 来搜索具有与‘dev datta’匹配的`name`字段的联系人。匹配的记录在`contacts`对象中返回。

### `find` API

```
Contact.find(1).then(function(contact) {
  alert('Contact ID 1: ' + contact.name);
}); 
```

在这里，我们可以使用`find` API 找到带有主键的特定联系人。我们已经将主键作为' 1 '传递，并在`contact`对象中接收了相关记录。

### `findBy` API

```
Contact.findBy({ name: 'Devdatta' }).then(function(contact) {
  alert('Contact named Devdatta from ' + contact.city);
}); 
```

`findBy` API 与`where`相似，但是您只能指定一个字段进行搜索。在这里，我们使用与“Devdatta”匹配的`name`字段进行搜索。在`contact`对象中返回匹配记录。

我们还可以在初始化 API 时使用`extra_where_scopes`来指定默认的搜索范围。像这样

```
var Contact = new Databound('/contacts',
  { city: 'Pune' },
  { extra_where_scopes: [{ city: 'Mumbai' }] }
); 
```

## 更新和删除 API

Databound 还提供了分别用于编辑和删除记录的`update`和`destroy`API

### 更新 API

```
Contact.update({ id: 1, name: 'John' }).then(function(contact) {
  alert("My name has changed. I'm " + contact.name);
}); 
```

使用数据绑定的`update` API，我们可以按照指定更新记录的字段。只有可访问列的数据才会按照控制器中的指定进行更新。更新后，contact 对象将返回更新后的数据。

### 删除 API

```
Contact.destroy(1).then(function(status) {
  if (status.success) alert('Contact deleted');
}); 
```

使用`destroy` API 删除记录，该 API 接受记录的主键作为参数，并返回状态 true 或 false。

## 允许操作

数据绑定还允许根据可以使用 API 调用的特定条件来指定允许哪些操作。例如:

```
permit(:update, :destroy) do |params, record|
  record.user_id == current_user.id
end 
```

(因为我们在示例中没有设置任何身份验证机制，所以这不会直接在我们的应用程序中起作用)

## 包扎

今天，我们简要介绍了数据绑定以及如何在 Rails 应用程序中使用它。许多 ruby 爱好者可能不喜欢数据绑定的工作方式，因为它将你的前端代码与数据库绑定得相当紧密。然而，有时解决方案的简单性战胜了架构的纯粹性。在这种情况下，数据绑定值得仔细研究。它对于 API 的快速原型开发和小型应用程序都很有用。

希望你喜欢这个教程。

一如既往地欢迎评论和建议。

## 分享这篇文章