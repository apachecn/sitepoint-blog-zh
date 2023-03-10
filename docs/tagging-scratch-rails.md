# 在 Rails 中从头开始标记

> 原文：<https://www.sitepoint.com/tagging-scratch-rails/>

![Price tag icon](img/c471b6ea8edadffdf01c50a82905d723.png)

标签有点像类别，描述一条信息(内容)并允许用户再次搜索它。在本教程中，您将看到如何在 Rails 中从头开始构建一个简单的标记系统。

本教程假设读者对 Ruby on Rails 有基本的了解。我会尽量保持简单。

我们都知道，标签的一个常见用途是通过 Twitter 收集与(hash)标签相关的某个主题的推文。

### **工具**

*   导轨 4.1.4
*   Ruby 2.0.1
*   基础 5

### **入门**

这个应用程序的原始源代码可以在这个[存储库](https://github.com/NouranMahmoud/TaggingTut)中找到

首先，创建您的 Rails 项目:

```
rails new TaggingTut
```

然后，将**基轨**宝石添加到宝石文件中，移除`turbolinks`宝石。运行`bundle install`。

> 我们将使用 sqlite，Rails 使用的默认数据库

从**app/assets/JavaScript/application . js .**中删除这一行，因为我们删除了`turbolinks` gem。

```
//= require turbolinks
```

### 创建标签

为具有单一属性的标签生成模型，`name`:

```
rails g model tag name:string
```

我们将对 name 属性(`index: true`)进行索引，以加速我们对这些标签的搜索。我推荐这篇关于在 Rails 关联中使用索引的[教程](https://tomafro.net/2009/08/using-indexes-in-rails-index-your-associations)。

标签有许多文章，文章可以有多个标签。因此，这种关系将是*多对多*。我们可以用两种方式在 Rails 中表示这种关联:

*   **第一个**:使用一个`has_and_belongs_to_many`关联。这将在数据库中生成连接表，但是不会为连接生成*模型*。因此，您将无法向连接添加验证或任何其他属性。

*   **第二个**:使用`has_many, through`，需要为连接表创建一个模型。这种方式在大多数情况下是首选的，所以我们将使用它。

创建模型“发布”和“标记”

```
rails g model post author:string content:text
rails g model tagging post:belongs_to tag:belongs_to
```

创建这些模型后，运行`rake db:migrate`。

现在，我们将通过 ActiveRecord 创建帖子和标签之间的关联，如下所示:

**app/models/post.rb**

```
has_many :taggings
has_many :tags, through: :taggings
```

**应用/模型/标签. rb**

```
has_many :taggings
has_many :posts, through: :taggings
```

**app/models/tagging.rb** 会这样生成:

```
belongs_to :post
belongs_to :tag
```

现在，我们通过 taggings 连接表在帖子和标签之间建立了一个连接。

现在，我们需要处理标签的创建，作为 post `create`动作的一部分。因此，我们将定义一个方法来获取所有输入的标签，剥离它们，然后将每个标签写入数据库。

帖子模型有两个属性，*作者*和*内容*，它们也在当前表单中定义。 **all_tags** 的属性也将被添加到表单数据中。在 Rails 4 中，使用强参数添加所需的(在本例中是虚拟的)属性。**虚拟属性**非常简单，在本例中定义为 getter 和 setter 方法。`strip`功能用于删除空白

**app/models/post.rb**

```
def all_tags=(names)
  self.tags = names.split(",").map do |name|
      Tag.where(name: name.strip).first_or_create!
  end
end

def all_tags
  self.tags.map(&:name).join(", ")
end
```

**all_tags** 函数将被定制为呈现所有用逗号分隔的标签。

在创建控制器和视图之前，安装 Zurb Foundation:

```
rails g foundation:install
```

现在，定制控制器和视图来呈现包含所有标签的帖子。通过键入以下命令创建**app/controllers/posts _ controller . Rb**:

```
rails g controller posts index create
```

如下指定强参数，包括我们的虚拟属性*来保存通过视图输入的所有标签:*

```
private
def post_params
  params.require(:post).permit(:author, :content, :all_tags)
end
```

`permit`方法创建了一个允许通过的参数白名单。阅读更多关于**强参数**这里

让我们创建一个带有标签文本字段的表单。我们将使用 AJAX 创建帖子。这很简单:

**app/views/posts/_ new . html . erb**

```
<div class="row text-center">
  <%= form_for(Post.new, remote: true) do |f| %>
    <div class="large-10 large-centered columns">
      <%= f.text_field :author, placeholder: "Author name" %>
    </div>
    <div class="large-10 large-centered columns">
      <%= f.text_area :content, placeholder: "Your post", rows: 5 %>
    </div>
    <div class="large-10 large-centered columns">
      <%= f.text_field :all_tags, placeholder: "Tags separated with comma" %>
    </div>
    <div class="large-10 large-centered columns">
      <%= f.submit "Post", class: "button"%>
    </div>
  <% end %>
</div>
```

`__remote: true__`是一个属性，它告诉表单通过 AJAX 提交，而不是通过浏览器的正常提交机制提交。

创建我们的帖子后，`create`将重定向到`index`操作并查看现有的帖子。

**app/controllers/posts _ controller . Rb**

```
def index
  @posts = Post.all
end
```

**app/views/posts/index . html . erb**

```
<div class="row">
  <div class="large-8 columns">
    <%= render partial: "posts/new" %>
  </div>
</div>
```

别忘了处理路线。
**config/routes.rb**

```
root 'posts#index'
resources :posts, only: [:create]
```

给视图添加一些非常简单的样式，如下:
**app/assets/样式表/posts.css.scss**

```
.tags-cloud {
  margin-top: 16px;
  padding: 14px;
}

.top-pad {
  padding: 25px;
}
.glassy-bg{
  box-shadow: 0px 3px 8px -4px rgba(0,0,0,0.15);
  background: white;
  border-radius: 4px;
  padding-bottom: 12px;
}

.mt{
  margin-top: 10px;
}
.mb{
  margin-bottom: 10px;
}

.pt{
  padding-top: 10px;
}
.pb{
  padding-bottom: 10px;
}
```

跑一跑，看看我们有什么。
![Form for post](img/f324b692f876aef6dc732e20d886a70d.png)

哎呀，没有帖子！。我们从未写过`create`动作。

**app/controllers/posts _ controller . Rb**

```
def create
  @post = Post.new(post_params)
  respond_to do |format|
    if @post.save
      format.js # Will search for create.js.erb
    else
      format.html { render root_path }
    end
  end
end
```

这个代码片段用用户指定的参数创建了一个新的 Post，检查它是否有效并返回结果。因为表单是用 AJAX 提交的，所以响应格式是`js`。

现在，我们需要创建 **create.js.erb** 文件来保存创建帖子后将运行的 JavaScript:
**app/views/posts/create . js . erb**

```
var new_post = $("<%= escape_javascript(render(partial: @post))%>").hide();
$('#posts').prepend(new_post);
$('#post_<%= @post.id %>').fadeIn('slow');
$('#new_post')[0].reset();
```

这段代码呈现了新创建的帖子的局部视图，`prepend`函数允许它以`fadeIn`效果呈现在旧帖子的顶部。

创建将呈现每个帖子的分部:

**app/views/posts/_ post . html . erb**

```
<%= div_for post do %>
  <div class="large-12 columns border border-box glassy-bg mt pt">
    <strong><%= h(post.author) %></strong><br />
    <sup class="text-muted">From <%= time_ago_in_words(post.created_at)%></sup><br />
    <div class="mb pb">
      <%= h(post.content) %>
    </div>
    <div class="tags">
      <%=raw post.all_tags %>
    </div>
  </div>
<% end %>
```

在我们检查输出之前，修改`index`视图来保存帖子的部分内容:

**app/views/index.html.erb**

```
<div class="row mt pt">
  <div class="large-5 columns">
    <div class="top-pad glassy-bg">
      <%= render partial: "posts/new" %>
    </div>
  </div>
  <div class= "large-7 columns" id="posts">
    <%= render partial: @posts.reverse %>
  </div>
</div>
```

文章将从上到下逆序排列，也就是说，最近输入的文章将排在最前面。

在这个阶段，我们使用两个表将带有标签的帖子存储在数据库中，*标签*和*标签*。*标签*表保存了帖子和标签之间的关联。我们的帖子如下所示:

![posts](img/3e3ff82c816b09e2c8cdb7a36c45c3a3.png)

### 基于标签的搜索

在本节中，我们将创建基于标签名称范围的搜索。

创建一个名为`tagged_with(name)`的类方法，它将获取指定标签的名称，并搜索与之相关的文章。

**app/model/post.rb**

```
def self.tagged_with(name)
  Tag.find_by_name!(name).posts
end
```

在控制器上创建一个保存结果的实例变量。

**app/controllers/posts _ controller . Rb**

```
def index
  if params[:tag]
    @posts = Post.tagged_with(params[:tag])
  else
    @posts = Post.all
  end
end
```

添加一个`get`路由来保存标记名，并指向`posts_controller#index`方法:

**config/routes.rb**

```
get 'tags/:tag', to: 'posts#index', as: "tag"
```

之后，将每篇文章的标签更改为“index”方法的链接，如下所示:

**app/views/_post.html.erb**

```
<%=raw tag_links(post.all_tags)%>
```

`tag_links(tags)`是一个帮助器方法，它包含将标签转换成链接的逻辑。

**app/helpers/posts _ helper . Rb**

```
def tag_links(tags)
  tags.split(",").map{|tag| link_to tag.strip, tag_path(tag.strip) }.join(", ") 
end
```

耶！现在，我们有了基于标签的帖子搜索！

![tag-based search](img/a596429d8874e785848dce5dd2526ba0.png)

## 标签云

让我们基于统计每个标签在所有帖子中出现的次数，生成一个很酷的**标签云**。

首先，创建一个方法来统计与帖子相关的所有标签:

**应用/模型/标签. rb**

```
def self.counts
  self.select("name, count(taggings.tag_id) as count").joins(:taggings).group("taggings.tag_id")
end
```

该查询对 taggings 连接表中匹配的`tag_ids`进行分组并计数。

我们将通过创建一个名为`tag_cloud`的 helper 方法来根据它们的计数对它们进行样式化，该方法接受调用`counts`函数和 CSS 类的结果。

**app/helpers/posts _ helper . Rb**

```
def tag_cloud(tags, classes)
  max = tags.sort_by(&:count).last
  tags.each do |tag|
    index = tag.count.to_f / max.count * (classes.size-1)
    yield(tag, classes[index.round])
  end
end
```

这个 helper 方法将获得具有最大计数的标签。然后，它在每个标签上循环计算`index`，T0 将根据取整后的值选择 **CSS 类**。然后，将执行传递的块。

我们需要添加不同尺寸的样式，如下所示:

**app/assets/tags.css.scss**

```
.css1 { font-size: 1.0em;}
.css2 { font-size: 1.2em;}
.css3 { font-size: 1.4em;}
.css4 { font-size: 1.6em;}
```

别忘了给 **application.css** 添加`*= require tags`。

最后，添加代码以在视图中显示标签，并对它们应用 CSS 类。

**app/views/posts/index . html . erb**

```
<div class="tags-cloud glassy-bg">
  <h4>Tags Cloud</h4>
  <% tag_cloud Tag.counts, %w{css1 css2 css3 css4} do |tag, css_class| %>
    <%= link_to tag.name, tag_path(tag.name), class: css_class %>
  <% end %>
</div>
```

快看，我们的标签在云中！
![tag cloud](img/1f5759de4e3c47d9204faf06685e9ee0.png)

### 将*作为*可标记 _ 开启

读完这篇文章后，你应该可以毫无问题地处理宝石上的**标记。你可以在它的 [github repo](https://github.com/mbleigh/acts-as-taggable-on/) 上了解更多信息。**

### 结论

我希望这篇教程能帮助你理解创建一个基本的标签系统需要什么。泰格，就是你了！:)

## 分享这篇文章