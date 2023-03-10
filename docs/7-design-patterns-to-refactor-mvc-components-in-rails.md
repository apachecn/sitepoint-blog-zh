# 重构 Rails 中 MVC 组件的 7 种设计模式

> 原文：<https://www.sitepoint.com/7-design-patterns-to-refactor-mvc-components-in-rails/>

![dphead](img/3bb2015448fe48545507f295b379b8c0.png)

在我们之前的文章[Rails 中 MVC 的基础](https://www.sitepoint.com/the-basics-of-mvc-in-rails-skinny-everything/)中，我们讨论了 MVC 设计模式的理论方面。我们定义了 MVC 代表什么，确定了每个 MVC 组件负责什么，解决了当一个组件包含冗余逻辑时会发生什么，最重要的是，我们引入了重构的概念。在本文中，正如所承诺的，我们将向您展示每个工作中的设计模式。

## 服务对象(和交互对象)

当执行以下操作时，创建服务对象:

*   很复杂(例如计算雇员的工资)
*   使用外部服务的 API
*   显然不属于一个模型(例如，删除过时的数据)
*   使用多个模型(例如，将数据从一个文件导入到多个模型中)

### 例子

在下面的示例中，工作由外部条带服务执行。Stripe 服务基于电子邮件地址和来源(令牌)创建一个 Stripe 客户，并将任何服务付款绑定到该帐户。

### 问题

*   外部服务的操作逻辑位于控制器中。
*   控制器为外部服务形成数据。
*   很难维护和扩展控制器。

```
class ChargesController  exception
    flash[:error] = exception.message
    redirect_to new_charge_path
  end
end 
```

为了解决这些问题，我们用外部服务封装我们的工作。

```
class ChargesController < ApplicationController
 def create
   CheckoutService.new(params).call
   redirect_to charges_path
 rescue Stripe::CardError => exception
   flash[:error] = exception.message
   redirect_to new_charge_path
 end
end

class CheckoutService
 DEFAULT_CURRENCY = 'USD'.freeze

 def initialize(options = {})
   options.each_pair do |key, value|
     instance_variable_set("@#{key}", value)
   end
 end

 def call
   Stripe::Charge.create(charge_attributes)
 end

 private

 attr_reader :email, :source, :amount, :description

 def currency
   @currency || DEFAULT_CURRENCY
 end

 def amount
   @amount.to_i * 100
 end

 def customer
   @customer ||= Stripe::Customer.create(customer_attributes)
 end

 def customer_attributes
   {
     email: email,
     source: source
   }
 end

 def charge_attributes
   {
     customer: customer.id,
     amount: amount,
     description: description,
     currency: currency
   }
 end
end 
```

结果是一个负责客户账户创建和支付的`CheckoutService`。但是，解决了控制器中逻辑过多的问题后，我们还有另一个问题要解决。如果外部服务抛出异常(例如，当信用卡无效时)，我们必须将用户重定向到另一个页面，会发生什么？

```
class ChargesController < ApplicationController
 def create
   CheckoutService.new(params).call
   redirect_to charges_path
 rescue Stripe::CardError => exception
   flash[:error] = exception.error
   redirect_to new_charge_path
 end
end 
```

为了处理这个场景，我们包含了一个`CheckoutService`调用，并用 Interactor 对象拦截异常。交互器用于封装业务逻辑。每个交互者通常描述一个业务规则。

交互模式通过使用普通的旧 Ruby 对象(POROs)帮助我们实现单一责任原则(SRP)——让模型只在持久性级别负责。交互器类似于服务对象，但是通常返回几个显示执行状态和其他信息的值(除了执行动作之外)。在 Interactor 对象中使用服务对象也是常见的做法。下面是这种设计模式用法的一个例子:

```
class ChargesController < ApplicationController
 def create
   interactor = CheckoutInteractor.call(self)

   if interactor.success?
 redirect_to charges_path
   else
 flash[:error] = interactor.error
 redirect_to new_charge_path
   end
 end
end

class CheckoutInteractor
 def self.call(context)
   interactor = new(context)
   interactor.run
   interactor
 end

 attr_reader :error

 def initialize(context)
   @context = context
 end

 def success?
   @error.nil?
 end

 def run
   CheckoutService.new(context.params)
 rescue Stripe::CardError => exception
   fail!(exception.message)
 end

 private

 attr_reader :context

 def fail!(error)
   @error = error
 end
end 
```

移动所有与卡错误相关的异常，我们可以实现一个瘦控制器。现在，控制器只负责将用户重定向到成功支付或不成功支付的页面。

## 价值对象

值对象设计模式鼓励简单的小对象(通常只包含给定的值)，并允许您根据给定的逻辑或简单地基于特定的属性(而不是基于它们的身份)来比较这些对象。values 对象的例子是以各种货币表示货币值的对象。然后，我们可以使用一种货币(如美元)来比较这些价值对象。例如，值对象也可以表示温度，并使用开尔文标度进行比较。

### 例子

让我们假设我们有一个带电加热的智能家居，加热器是通过网络界面控制的。控制器动作从温度传感器接收给定加热器的参数:温度(作为数值)和温标(华氏、摄氏或开尔文)。如果以另一种标度提供，该温度被转换成开尔文，并且控制器检查该温度是否低于 25°C 以及它是否等于或大于当前温度。

### 问题

控制器包含太多与温度值转换和比较相关的逻辑。

```
class AutomatedThermostaticValvesController < ApplicationController
  SCALES = %w(kelvin celsius fahrenheit)
  DEFAULT_SCALE = 'kelvin'
  MAX_TEMPERATURE = 25 + 273.15

  before_action :set_scale

  def heat_up
    was_heat_up = false
    if previous_temperature < next_temperature && next_temperature < MAX_TEMPERATURE
      valve.update(degrees: params[:degrees], scale: params[:scale])
      Heater.call(next_temperature)
      was_heat_up = true
    end
    render json: { was_heat_up: was_heat_up }
  end

  private

  def previous_temperature
    kelvin_degrees_by_scale(valve.degrees, valve.scale)
  end

  def next_temperature
    kelvin_degrees_by_scale(params[:degrees], @scale)
  end

  def set_scale
    @scale = SCALES.include?(params[:scale]) ? params[:scale] : DEFAULT_SCALE
  end

  def valve
    @valve ||= AutomatedThermostaticValve.find(params[:id])
  end

  def kelvin_degrees_by_scale(degrees, scale)
    degrees = degrees.to_f
    case scale.to_s
    when 'kelvin'
      degrees
    when 'celsius'
      degrees + 273.15
    when 'fahrenheit'
      (degrees - 32) * 5 / 9 + 273.15
    end
  end
end 
```

我们将温度比较逻辑移到了模型中，因此控制器只需将参数传递给`update`方法。但是这个模型仍然不理想——它太了解如何处理温度转换。

```
class AutomatedThermostaticValvesController < ApplicationController
  def heat_up
    valve.update(next_degrees: params[:degrees], next_scale: params[:scale])

    render json: { was_heat_up: valve.was_heat_up }
  end

  private

  def valve
    @valve ||= AutomatedThermostaticValve.find(params[:id])
  end
end

class AutomatedThermostaticValve < ActiveRecord::Base
  SCALES = %w(kelvin celsius fahrenheit)
  DEFAULT_SCALE = 'kelvin'

  before_validation :check_next_temperature, if: :next_temperature
  after_save :launch_heater, if: :was_heat_up

  attr_accessor :next_degrees, :next_scale
  attr_reader :was_heat_up

  def temperature
    kelvin_degrees_by_scale(degrees, scale)
  end

  def next_temperature
    kelvin_degrees_by_scale(next_degrees, next_scale) if next_degrees.present?
  end

  def max_temperature
    kelvin_degrees_by_scale(25, 'celsius')
  end

  def next_scale=(scale)
    @next_scale = SCALES.include?(scale) ? scale : DEFAULT_SCALE
  end

  private

  def check_next_temperature
    @was_heat_up = false
    if temperature < next_temperature && next_temperature <= max_temperature
      @was_heat_up = true
      assign_attributes(
        degrees: next_degrees,
        scale: next_scale,
      )
    end
    @was_heat_up
  end

  def launch_heater
    Heater.call(temperature)
  end

  def kelvin_degrees_by_scale(degrees, scale)
    degrees = degrees.to_f
    case scale.to_s
    when 'kelvin'
      degrees
    when 'celsius'
      degrees + 273.15
    when 'fahrenheit'
      (degrees - 32) * 5 / 9 + 273.15
    end
  end
end 
```

为了使模型更瘦，我们创建了值对象。初始化时，值对象采用度数和比例值。当比较这些物体时，宇宙飞船方法(`<=>`)比较它们的温度，转换成开尔文。

接收的值对象还包含一个用于属性批量赋值的`to_h`方法。值对象提供了工厂方法`from_kelvin`、`from_celsius`和`from_fahrenheit`，以便于创建一定比例的`Temperature`对象，例如`Temperature.from_celsius(0)`将创建一个温度为 0°C 或 273°к的对象。

```
class AutomatedThermostaticValvesController < ApplicationController
  def heat_up
    valve.update(next_degrees: params[:degrees], next_scale: params[:scale])
    render json: { was_heat_up: valve.was_heat_up }
  end

  private

  def valve
    @valve ||= AutomatedThermostaticValve.find(params[:id])
  end
end

class AutomatedThermostaticValve < ActiveRecord::Base
  before_validation :check_next_temperature, if: :next_temperature
  after_save :launch_heater, if: :was_heat_up

  attr_accessor :next_degrees, :next_scale
  attr_reader :was_heat_up

  def temperature
    Temperature.new(degrees, scale)
  end

  def temperature=(temperature)
    assign_attributes(temperature.to_h)
  end

  def next_temperature
    Temperature.new(next_degrees, next_scale) if next_degrees.present?
  end

  private

  def check_next_temperature
    @was_heat_up = false
    if temperature < next_temperature && next_temperature <= Temperature::MAX
      self.temperature = next_temperature
      @was_heat_up = true
    end
  end

  def launch_heater
    Heater.call(temperature.kelvin_degrees)
  end
end

class Temperature
  include Comparable
  SCALES = %w(kelvin celsius fahrenheit)
  DEFAULT_SCALE = 'kelvin'

  attr_reader :degrees, :scale, :kelvin_degrees

  def initialize(degrees, scale = 'kelvin')
    @degrees = degrees.to_f
    @scale = case scale
    when *SCALES then scale
    else DEFAULT_SCALE
    end

    @kelvin_degrees = case @scale
    when 'kelvin'
      @degrees
    when 'celsius'
      @degrees + 273.15
    when 'fahrenheit'
      (@degrees - 32) * 5 / 9 + 273.15
    end
  end

  def self.from_celsius(degrees_celsius)
    new(degrees_celsius, 'celsius')
  end

  def self.from_fahrenheit(degrees_fahrenheit)
    new(degrees_celsius, 'fahrenheit')
  end

  def self.from_kelvin(degrees_kelvin)
    new(degrees_kelvin, 'kelvin')
  end

  def <=>(other)
    kelvin_degrees <=> other.kelvin_degrees
  end

  def to_h
    { degrees: degrees, scale: scale }
  end

  MAX = from_celsius(25)
end 
```

结果就是一个瘦骨嶙峋的控制器和一个瘦骨嶙峋的模特。控制器不知道任何与温度相关的逻辑，模型也不知道任何温度转换，只使用温度值对象的方法。

## 表单对象

表单对象是一种设计模式，它封装了与验证和保存数据相关的逻辑。

### 例子

假设我们有一个典型的 Rails 模型和控制器动作来创建新用户。

### 问题

该模型包含所有的验证逻辑，因此它不能被其他实体重用，例如 Admin。

```
class UsersController < ApplicationController
  def create
    @user = User.new(user_params)

    if @user.save
      render json: @user
    else
      render json: @user.error, status: :unprocessable_entity
    end
  end

  private

  def user_params
    params
      .require(:user)
      .permit(:email, :full_name, :password, :password_confirmation)
  end
end

class User < ActiveRecord::Base
  EMAIL_REGEX = /@/ # Some fancy email regex

  validates :full_name, presence: true
  validates :email, presence: true, format: EMAIL_REGEX
  validates :password, presence: true, confirmation: true
end 
```

一个解决方案是将验证逻辑转移到一个单独的责任类，我们可以称之为`UserForm`:

```
class UserForm
  EMAIL_REGEX = // # Some fancy email regex

  include ActiveModel::Model
  include Virtus.model

  attribute :id, Integer
  attribute :full_name, String
  attribute :email, String
  attribute :password, String
  attribute :password_confirmation, String

  validates :full_name, presence: true
  validates :email, presence: true, format: EMAIL_REGEX
  validates :password, presence: true, confirmation: true

  attr_reader :record

  def persist
    @record = id ? User.find(id) : User.new

    if valid?
      @record.attributes = attributes.except(:password_confirmation, :id)
      @record.save!
      true
    else
      false
    end
  end
end 
```

在我们将验证逻辑移动到`UserForm`之后，我们可以像这样在控制器内部使用它:

```
class UsersController < ApplicationController
  def create
    @form = UserForm.new(user_params)

    if @form.persist
      render json: @form.record
    else
      render json: @form.errors, status: :unpocessably_entity
    end
  end

  private

  def user_params
    params.require(:user)
          .permit(:email, :full_name, :password, :password_confirmation)
  end
end 
```

因此，用户模型不再负责验证数据:

```
class User < ActiveRecord::Base
end 
```

## 查询对象

查询对象是一种设计模式，它允许我们将控制器和模型中的查询逻辑提取到可重用的类中。

### 例子

我们想要请求一个当前用户可以访问的、浏览量大于 100 的、类型为“video”的文章列表。

### 问题

所有查询逻辑都在控制器中(所有查询条件都强加在控制器中)；

*   这个逻辑是不可重用的
*   很难测试
*   对文章方案的任何更改都会破坏此代码

```
class Article < ActiveRecord::Base
    # t.string :status
    # t.string :type
    # t.integer :view_count
  end

 class ArticlesController < ApplicationController
    def index
      @articles = Article
                  .accessible_by(current_ability)
                  .where(type: :video)
                  .where('view_count > ?', 100)
    end
  end
```

重构这个控制器的第一步是隐藏和封装底层查询条件，并为查询模型提供一个简单的 API。在 Rails 中，我们可以通过创建作用域来做到这一点:

```
class Article < ActiveRecord::Base
  scope :with_video_type, -> { where(type: :video) }
  scope :popular, -> { where('view_count > ?', 100) }
  scope :popular_with_video_type, -> { popular.with_video_type }
end 
```

现在我们可以使用这个简单的 API 来查询我们需要的一切，而不用担心底层的实现。如果我们的文章方案发生变化，我们只需要对文章类进行修改:

```
class ArticlesController < ApplicationController
  def index
    @articles = Article
                .accessible_by(current_ability)
                .popular_with_video_type
  end
end 
```

这要好得多，但现在出现了一些新问题。我们需要为想要封装的每个查询条件创建范围，为不同的用例使用不同的范围组合来膨胀模型。另一个问题是作用域在不同的模型中不可重用，这意味着您不能只使用 Article 类的作用域来查询 Attachment 类。我们还打破了单一责任原则，将所有与查询相关的责任都放到了 Article 类中。这些问题的解决方案是使用查询对象。

```
class PopularVideoQuery
  def call(relation)
    relation
      .where(type: :video)
      .where('view_count > ?', 100)
  end
end

class ArticlesController < ApplicationController
  def index
    relation = Article.accessible_by(current_ability)
    @articles = PopularVideoQuery.new.call(relation)
  end
end 
```

哇，可重复使用！现在，我们可以使用这个类来查询任何其他具有类似方案的存储库:

```
class Attachment < ActiveRecord::Base
  # t.string :type
  # t.integer :view_count
end

PopularVideoQuery.new.call(Attachment.all).to_sql
# "SELECT \"attachments\".* FROM \"attachments\" WHERE \"attachments\".\"type\" = 'video' AND (view_count > 100)"
PopularVideoQuery.new.call(Article.all).to_sql
# "SELECT \"articles\".* FROM \"articles\" WHERE \"articles\".\"type\" = 'video' AND (view_count > 100)" 
```

还有，如果我们想把它们链起来，也很简单。我们唯一要考虑的是`call`方法应该符合`ActiveRecord::Relation`的接口:

```
class BaseQuery
  def |(other)
    ChainedQuery.new do |relation|
      other.call(call(relation))
    end
  end
end

class ChainedQuery < BaseQuery
  def initialize(&block)
    @block = block
  end

  def call(relation)
    @block.call(relation)
  end
end

class WithStatusQuery < BaseQuery
  def initialize(status)
    @status = status
  end

  def call(relation)
    relation.where(status: @status)
  end
end

query = WithStatusQuery.new(:published) | PopularVideoQuery.new
query.call(Article.all).to_sql
# "SELECT \"articles\".* FROM \"articles\" WHERE \"articles\".\"status\" = 'published' AND \"articles\".\"type\" = 'video' AND (view_count > 100)" 
```

我们现在有了一个可重用的类，它封装了所有的查询逻辑，有一个简单的接口，易于测试。

## 查看对象(序列化程序/演示者)

视图对象允许我们从控制器和模型中提取数据和计算，这些数据和计算只需要在视图中显示模型的表示——例如网站的 HTML 页面或来自 API 端点的 JSON 响应。

### 例子

视图中有各种动作(计算)发生。观点:

*   从协议主机和图像路径创建图像 URL
*   获取文章标题和描述；如果没有任何自定义值，则采用默认值
*   串联名字和姓氏以显示全名
*   为文章创建日期应用正确的格式

### 问题

视图包含太多计算逻辑。

```
#before refactoring
#/app/controllers/articles_controller.rb
class ArticlesController < ApplicationController
 def show
   @article = Article.find(params[:id])
 end
end

#/app/views/articles/show.html.erb
<% content_for :header do %>
 <title>
     <%= @article.title_for_head_tag || I18n.t('default_title_for_head') %>
 </title>
 <meta name='description' content="<%= @article.description_for_head_tag || I18n.t('default_description_for_head') %>">
  <meta property="og:type" content="article">
  <meta property="og:title" content="<%= @article.title %>">
  <% if @article.description_for_head_tag %>
    <meta property="og:description" content="<%= @article.description_for_head_tag %>">
  <% end %>
  <% if @article.image %>
     <meta property="og:image" content="<%= "#{request.protocol}#{request.host_with_port}#{@article.main_image}" %>">
  <% end %>
<% end %>

<% if @article.image %>
 <%= image_tag @article.image.url %>
<% else %>
 <%= image_tag 'no-image.png'%>
<% end %>
<h1>
 <%= @article.title %>
</h1>

<p>
 <%= @article.text %>
</p>

<% if @article.author %>
<p>
 <%= "#{@article.author.first_name} #{@article.author.last_name}" %>
</p>
<%end%>

<p>
 <%= t('date') %>
 <%= @article.created_at.strftime("%B %e, %Y")%>
</p> 
```

为了解决这个问题，我们创建一个基本的 presenter 类，然后创建一个`ArticlePresenter`类实例。`ArticlePresenter`方法通过适当的计算返回所需的标签:

```
#/app/controllers/articles_controller.rb
class ArticlesController 
```

太好了！我们有一个不包含任何与计算相关的逻辑的视图，所有组件都被移到演示者那里，并可在其他视图中重用，如下所示:

```
#/app/views/articles/show.html.erb
<% presenter @article do |article_presenter| %>
 <% content_for :header do %>
   <%= article_presenter.meta_title %>
   <%= article_presenter.meta_description %>
   <%= article_presenter.og_type %>
   <%= article_presenter.og_title %>
   <%= article_presenter.og_description %>
   <%= article_presenter.og_image %>
 <% end %>
 <%= article_presenter.image%>
 <h1> <%= article_presenter.title %> </h1>
 <p>  <%= article_presenter.text %> </p>
 <%= article_presenter.author_name %>
<% end %> 
```

## 策略对象

策略对象设计模式类似于服务对象，但是负责读操作，而服务对象负责写操作。策略对象封装了复杂的业务规则，可以很容易地被具有不同规则的其他策略对象所替代。例如，我们可以检查访客用户是否能够使用访客策略对象检索某些资源。如果用户是管理员，我们可以很容易地将这个来宾策略对象更改为包含管理规则的管理策略对象。

### 例子

在用户创建项目之前，控制器检查当前用户是否是经理，他们是否有创建项目的权限，当前用户项目的数量是否低于最大值，并检查在 Redis 键/值存储中创建项目时是否存在阻塞。

### 问题

*   只有控制者知道项目创建的策略
*   控制器包含过多的逻辑

```
class ProjectsController < ApplicationController
   def create
     if can_create_project?
       @project = Project.create!(project_params)
       render json: @project, status: :created
     else
       head :unauthorized
     end
   end

  private

  def can_create_project?
     current_user.manager? &&
       current_user.projects.count < Project.max_count &&
       redis.get('projects_creation_blocked') != '1'
   end

  def project_params
     params.require(:project).permit(:name, :description)
   end

  def redis
      Redis.current
    end
  end

  def User < ActiveRecord::Base
     enum role: [:manager, :employee, :guest]
  end 
```

为了使控制器更简洁，我们将策略逻辑移到模型中。结果，检查完全移出了控制器。但是现在用户类知道了 Redis 和项目类逻辑，所以模型变得很胖。

```
def User < ActiveRecord::Base
  enum role: [:manager, :employee, :guest]

  def can_create_project?
    manager? &&
      projects.count < Project.max_count &&
        redis.get('projects_creation_blocked') != '1'
  end

  private

  def redis
    Redis.current
  end
end

class ProjectsController < ApplicationController
  def create
    if current_user.can_create_project?
       @project = Project.create!(project_params)
       render json: @project, status: :created
    else
       head :unauthorized
    end
  end

  private

  def project_params
     params.require(:project).permit(:name, :description)
  end
end 
```

在这种情况下，我们可以通过将这个逻辑移动到一个策略对象来使模型和控制器变得精简:

```
class CreateProjectPolicy
  def initialize(user, redis_client)
    @user = user
    @redis_client = redis_client
  end

  def allowed?
    @user.manager? && below_project_limit && !project_creation_blocked
  end

 private

  def below_project_limit
    @user.projects.count < Project.max_count
  end

  def project_creation_blocked
    @redis_client.get('projects_creation_blocked') == '1'
  end
end

class ProjectsController < ApplicationController
  def create
    if policy.allowed?
       @project = Project.create!(project_params)
       render json: @project, status: :created
     else
       head :unauthorized
     end
  end

  private

  def project_params
     params.require(:project).permit(:name, :description)
  end

  def policy
     CreateProjectPolicy.new(current_user, redis)
  end

  def redis
     Redis.current
  end
end

def User < ActiveRecord::Base
   enum role: [:manager, :employee, :guest]
end 
```

结果是一个干净的控制器和模型。策略对象封装了权限检查逻辑，所有外部依赖关系都从控制器注入到策略对象中。所有班级都做自己的作业，不做别人的。

## 装修工

装饰器模式允许我们向单个对象添加任何类型的辅助行为，而不会影响同一类的其他对象。这种设计模式被广泛用于在不同的类之间划分功能，并且是遵循单一责任原则的子类的一个很好的替代方案。

### 例子

让我们假设视图中有许多操作(计算)发生:

*   根据`title_for_head`值的存在，标题显示有所不同
*   当没有提供自定义汽车图像 URL 时，视图显示默认汽车图像
*   当品牌、型号、注释、所有者、城市和所有者电话的值未定义时，视图显示默认文本
*   该视图显示了汽车状态的文本表示
*   该视图显示格式化的汽车对象创建日期

### 问题

视图包含太多计算逻辑。

```
#/app/controllers/cars_controller.rb
class CarsController < ApplicationController
 def show
   @car = Car.find(params[:id])
 end
end

#/app/views/cars/show.html.erb
<% content_for :header do %>
 <title>
   <% if @car.title_for_head %>
     <%="#{ @car.title_for_head } | #{t('beautiful_cars')}" %>
   <% else %>
     <%= t('beautiful_cars') %>
   <% end %>
 </title>
 <% if @car.description_for_head%>
   <meta name='description' content= "#{<%= @car.description_for_head %>}">
 <% end %>
<% end %>

<% if @car.image %>
 <%= image_tag @car.image.url %>
<% else %>
 <%= image_tag 'no-images.png'%>
<% end %>
<h1>
 <%= t('brand') %>
 <% if @car.brand %>
   <%= @car.brand %>
 <% else %>
    <%= t('undefined') %>
 <% end %>
</h1>

<p>
 <%= t('model') %>
 <% if @car.model %>
   <%= @car.model %>
 <% else %>
    <%= t('undefined') %>
 <% end %>
</p>

<p>
 <%= t('notes') %>
 <% if @car.notes %>
   <%= @car.notes %>
 <% else %>
    <%= t('undefined') %>
 <% end %>
</p>

<p>
 <%= t('owner') %>
 <% if @car.owner %>
   <%= @car.owner %>
 <% else %>
    <%= t('undefined') %>
 <% end %>
</p>

<p>
 <%= t('city') %>
 <% if @car.city %>
   <%= @car.city %>
 <% else %>
    <%= t('undefined') %>
 <% end %>
</p>
<p>
 <%= t('owner_phone') %>
 <% if @car.phone %>
   <%= @car.phone %>
 <% else %>
    <%= t('undefined') %>
 <% end %>
</p>

<p>
 <%= t('state') %>
 <% if @car.used %>
   <%= t('used') %>
 <% else %>
   <%= t('new') %>
 <% end %>
</p>

<p>
 <%= t('date') %>
 <%= @car.created_at.strftime("%B %e, %Y")%>
</p> 
```

我们可以用 Draper decorating gem 来解决这个问题，它将所有逻辑转移到`CarDecorator`方法:

```
#/app/controllers/cars_controller.rb
class CarsController < ApplicationController
 def show
   @car = Car.find(params[:id]).decorate
 end
end

#/app/decorators/car_decorator.rb
class CarDecorator < Draper::Decorator
 delegate_all
 def meta_title
   result =
     if object.title_for_head
       "#{ object.title_for_head } | #{I18n.t('beautiful_cars')}"
     else
       t('beautiful_cars')
     end
   h.content_tag :title, result
 end

 def meta_description
   if object.description_for_head
     h.content_tag :meta, nil ,content: object.description_for_head
   end
 end

 def image
   result = object.image.url.present? ? object.image.url : 'no-images.png'
   h.image_tag result
 end

 def brand
   get_info object.brand
 end

 def model
   get_info object.model
 end

 def notes
   get_info object.notes
 end

 def owner
   get_info object.owner
 end

 def city
   get_info object.city
 end

 def owner_phone
   get_info object.phone
 end

 def state
   object.used ? I18n.t('used') : I18n.t('new')
 end

 def created_at
   object.created_at.strftime("%B %e, %Y")
 end

 private

 def get_info value
   value.present? ? value : t('undefined')
 end
end 
```

结果是一个没有任何计算的简洁视图:

```
#/app/views/cars/show.html.erb
<% content_for :header do %>
 <%= @car.meta_title %>
 <%= @car.meta_description%>
<% end %>
​
<%= @car.image %>
<h1> <%= t('brand') %> <%= @car.brand %> </h1>
<p> <%= t('model') %> <%= @car.model %>  </p>
<p> <%= t('notes') %> <%= @car.notes %>  </p>
<p> <%= t('owner') %> <%= @car.owner %>  </p>
<p> <%= t('city') %> <%= @car.city %>    </p>
<p> <%= t('owner_phone') %> <%= @car.phone %> </p>
<p> <%= t('state') %> <%= @car.state %>   </p>
<p> <%= t('date') %> <%= @car.created_at%> </p> 
```

## 包扎

这些概念应该为您提供何时以及如何重构代码的基本理解。有许多管理代码复杂性的工具。通过从开发的开始就小心地放置逻辑，您可以减少需要花费在重构上的时间。

## 分享这篇文章