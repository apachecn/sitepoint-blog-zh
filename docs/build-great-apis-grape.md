# 用 Grape 构建优秀的 API

> 原文：<https://www.sitepoint.com/build-great-apis-grape/>

![big_grape](img/a8dc6e2df1507b8467cfdab717e07b63.png)

出于各种原因需要 API，例如移动应用程序需要与后端 CRUD 操作进行交互。创建可维护的 API 不是一项简单的任务。随着应用程序的增长，对 API 版本化和维护的需求也在增长。

在本教程中，我们将看看 [Grape](https://github.com/intridea/grape) ，这是一个在 Ruby 中创建可维护的类似 REST 的 API 的微框架。

来自 grape 的 Github 页面:

> Grape 是 Ruby 的一个类似 REST 的 API 微框架。它旨在通过提供一个简单的 DSL 来轻松开发 RESTful APIs，从而在机架上运行或补充现有的 web 应用程序框架，如 Rails 和 Sinatra。它内置了对常见约定的支持，包括多种格式、子域/前缀限制、内容协商、版本控制等等。

## 我们的项目

我们将使用 [Rails](http://rubyonrails.org/) 和 grape 一起创建 API。它将向其他应用程序公开雇员模型，消费该模型的后端 CRUD 操作。

丰富的 web 应用程序可以使用该 API，如使用 AngularJS、Backbone.js 等创建的应用程序。或本地移动应用程序来创建、编辑、更新和删除员工。

以下是我们将创建的 API 端点:

```
//Get all employee details
GET /emp_api       

//Add an employee
POST /emp_api/  {name:"Jay", address:"Kerala, India", age:"34"}             

//Delete an employee         
DELETE /emp_api/1

//Update an employee
PUT /emp_api/1
```

## 入门指南

让我们从创建一个 Rails 应用程序并安装 Grape gem 开始。我用的是 Rails 4.1.6。

创建一个新的 Rails 项目，跳过`bundle install`。

```
rails new emp_api --skip-bundle
```

一旦创建了所有项目文件，导航到项目目录(`emp_api`)。打开**宝石文件**，将葡萄宝石包含在文件末尾。

```
gem 'grape'
```

接下来，使用 Bundler 安装所有需要的 gem:

```
bundle install
```

为 API 公开的基本 CRUD 操作生成一个员工模型:

```
rails g model EmpData name:string address:string age:integer
rake db:migrate
```

所有的 API 文件都将存在于 **app** 文件夹中。在 **emp_api/app/** 里面新建一个名为 **api** 的文件夹。

在 **emp_api/app/api** 中创建另一个名为 **employee** 的文件夹，以及该目录中名为 **data.rb** 的文件。这个文件包含了访问雇员模型的类。

在 **emp_api/app/api/** 中创建另一个名为 **api.rb** 的文件，我们将在其中挂载在**EMP _ API/app/API/employee/data . Rb**中定义的类。

根据 [Grape GitHub 页面](https://github.com/intridea/grape)上的说明，因为我们已经将 API 代码放在了 **app/app** 中，所以需要将该目录添加到加载/自动加载路径中。在 **config/application.rb** 中设置，如下图所示:

```
require File.expand_path('../boot', __FILE__)

require 'rails/all'

Bundler.require(*Rails.groups)

module EmpApi
  class Application < Rails::Application
    ## Newly Added code to set up the api code
    config.paths.add File.join('app', 'api'), glob: File.join('**', '*.rb')
    config.autoload_paths += Dir[Rails.root.join('app', 'api', '*')]
  end
end
```

## 创建 API

Grape APIs 是通过子类化`Grape::API`创建的框架应用程序。让我们从创建第一个 API 端点来检索所有员工详细信息开始。打开 **app/api/employee/data.rb** ，创建模块类，如图所示:

```
module Employee
  class Data < Grape::API

    resource :employee_data do
      desc "List all Employee"

      get do
        EmpData.all
      end

    end

  end
end
```

我们需要访问 API 类中的`Employee::Data`类功能。因此，我们将使用`mount`使`Employee::Data`类可以在`API`类中访问。

所以，打开 **app/api/api.rb** ，挂载`Employee::Data`

```
class API < Grape::API
  prefix 'api'
  version 'v1', using: :path
  mount Employee::Data
end
```

打开 **app/config/routes.rb** 并包含以下代码以将 API 调用路由到我们的根地址:

```
Rails.application.routes.draw do
  mount API => '/'
end
```

启动 rails 服务器:

```
rails server
```

打开一个新终端并尝试:

```
curl http://localhost:3000/api/v1/employee_data.json
```

您将看到`[]`作为回应，因为我们还没有任何数据。我们没有办法添加员工，所以让我们来解决这个问题。

添加另一个 API 端点来创建新员工。我们需要某些值来创建一个雇员，比如姓名、地址和年龄。Grape 允许我们定义这些参数在 API 本身中是必需的还是可选的。

打开 **app/api/employee/data.rb** ，添加以下代码，为员工创建创建 api 端点。

```
desc "create a new employee"
## This takes care of parameter validation
params do
  requires :name, type: String
  requires :address, type:String
  requires :age, type:Integer
end
## This takes care of creating employee
post do
  EmpData.create!({
    name:params[:name],
    address:params[:address],
    age:params[:age]
  })
end
```

重启服务器并使用`curl`向端点发送请求:

```
curl http://localhost:3000/api/v1/employee_data.json -d "name=jay;address=delhi;age=25"
```

现在，我们应该有一个员工:

```
curl http://localhost:3000/api/v1/employee_data.json

[{"id":1,"name":"jay","address":"delhi","age":25,"created_at":"2014-10-27T16:43:49.303Z","updated_at":"2014-10-27T16:43:49.303Z"}]
```

答对了。

雇员模型的删除和更新 API 遵循与我们的读取和创建端点相似的模式。
删除端点需要员工 ID:

```
# app/api/employee/data.rb

 desc "delete an employee"
 params do
   requires :id, type: String
 end
 delete ':id' do
   EmpData.find(params[:id]).destroy!
 end
```

重启 Rails 服务器，通过删除我们之前创建的雇员来测试 API。

```
curl -X DELETE http://localhost:3000/api/v1/employee_data/1.json

{"id":1,"name":"jay","address":"delhi","age":25,"created_at":"2014-10-27T16:43:49.303Z","updated_at":"2014-10-27T16:43:49.303Z"}
```

现在，如果您检查雇员列表 api，您应该得到一个空响应。

接下来，为了更新雇员的详细信息，我们需要传递雇员 ID 和要更新的值。添加以下代码以创建一个 API 端点来更新员工地址:

```
# app/api/employee/data.rb

desc "update an employee address"
params do
  requires :id, type: String
  requires :address, type:String
end
put ':id' do
  EmpData.find(params[:id]).update({
    address:params[:address]
  })
end
```

但是我们没有员工来更新。请记住，我们删除了最后一名员工，因此我们需要添加一名员工来更新它。

重启服务器，首先添加一名新员工，

```
curl http://localhost:3000/api/v1/employee_data.json -d "name=roy;address=kerala;age=25"

{"id":2,"name":"roy","address":"kerala","age":25,"created_at":"2014-10-27T16:59:54.090Z","updated_at":"2014-10-27T16:59:54.090Z"}
```

现在，使用以下命令更新新雇员的地址:

```
curl -X PUT http://localhost:3000/api/v1/employee_data/2.json -d "address=mumbai"

true
```

## 包装它

在本教程中，我们开始使用 Grape 框架创建一个简单的 CRUD API。关于 Grape 的详细文档可以在他们的 [GitHub 页面](https://github.com/intridea/grape)上找到。

本教程的代码可以在 [GitHub](https://github.com/jay3dec/GrapeAPISample) 上获得。

## 分享这篇文章