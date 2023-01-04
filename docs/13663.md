# 使用路线导航器了解路线

> 原文：<https://www.sitepoint.com/understanding-routes-with-routing-navigator/>

Rick Olson 发布了一个 [Routing Navigator 插件](http://weblog.techno-weenie.net/2006/6/19/new-plugin-routing-navigator)来帮助理解 Rails 路由系统如何为你的应用工作(注意:它只支持 edge rails，不支持 v1.1.2)。

要测试它:

1.  创建一个新的 rails 应用程序

2.  将边缘导轨安装到供应商
    `$ cd routingtest
    $ rake rails:freeze:edge`
`*   安装路线导航器插件
    `$ script/plugin source http://svn.techno-weenie.net/projects/plugins
    $ script/plugin install routing_navigator`*   安装插件的 CSS 和 JS 文件
    `$ rake update_routing_navigator`*   启动您的应用程序
    `$ script/server`*   浏览至`/routing_navigator`*   修改您的路线，冲洗并重复*   如果您想在任何视图页面上查看路线，您还需要将以下内容添加到您的布局的`head`中:
    `<%= javascript_include_tag :defaults, 'routing_navigator' %>
    <%= stylesheet_link_tag 'routing_navigator' %>``

 `对于懒惰和好奇的人:为基础 Rails 应用程序生成的路线:

| 途径 | 重要键 | 要求 | 情况 |
| --- | --- | --- | --- |
| /:控制器/服务. wsdl/ | [:控制器，:动作] | {:action=>"wsdl"} | {} |
| /:控制器/:操作/:id/ | [:控制器，:操作，:id] | {} | {} |

…以及使用[simple _ RESTful 插件](http://microformats.org/wiki/rest/rails#Simply_RESTful)编写的简单 [RESTful](http://microformats.org/wiki/rest) 应用程序的路径:

| 途径 | 重要键 | 要求 | 情况 |
| --- | --- | --- | --- |
| /:控制器/服务. wsdl/ | [:控制器，:动作] | {:action=>"wsdl"} | {} |
| /人。:格式/ | [:格式，:动作，:控制器] | {:action=>"create "，:controller=>"people"} | {:method=>:post} |
| /人/ | [:动作，:控制器] | {:action=>"create "，:controller=>"people"} | {:method=>:post} |
| /人。:格式/ | [:格式，:动作，:控制器] | {:action=>"index "，:controller=>"people"} | {:method=>:get} |
| /人/ | [:动作，:控制器] | {:action=>"index "，:controller=>"people"} | {:method=>:get} |
| /人/新。:格式/ | [:格式，:动作，:控制器] | {:action=>"new "，:controller=>"people"} | {:method=>:get} |
| /people/new/ | [:动作，:控制器] | {:action=>"new "，:controller=>"people"} | {:method=>:get} |
| /people/:id/ | [:id，:动作，:控制器] | {:action=>"destroy "，:controller=>"people"} | {:method=>:delete} |
| /people/:id/ | [:id，:动作，:控制器] | {:action=>"update "，:controller=>"people"} | {:method=>:put} |
| /people/:id。:格式；编辑/ | [:id，:格式，:动作，:控制器] | {:action=>"edit "，:controller=>"people"} | {:method=>:get} |
| /people/:id；编辑/ | [:id，:动作，:控制器] | {:action=>"edit "，:controller=>"people"} | {:method=>:get} |
| /people/:id。:格式/ | [:id，:格式，:动作，:控制器] | {:action=>"show "，:controller=>"people"} | {:method=>:get} |
| /people/:id/ | [:id，:动作，:控制器] | {:action=>"show "，:controller=>"people"} | {:method=>:get} |
| /:控制器/:操作/:id/ | [:控制器，:操作，:id] | {} | {} |` 

## `分享这篇文章`