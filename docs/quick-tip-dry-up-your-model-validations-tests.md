# 快速提示:停止你的模型验证测试

> 原文：<https://www.sitepoint.com/quick-tip-dry-up-your-model-validations-tests/>

![](img/5e8620ab62ad9b336a888dd11a8c424e.png)

我正在开发一个应用程序，需要测试模型，这是很正常的做法。我不得不为每个领域和每个模型重复验证测试，导致大量重复的测试代码。所以，我将分享我对这个问题的解决方案，这将有助于我们避免对每个模型重复类似的验证测试。

我用的是 Ruby 2.3.0，Rails 4.2.4。以及我的测试套件中的 Minitest 和 FactoryGirl。我为一个站点模型编写了这个测试，比如:

```
class SiteTest < ActiveSupport::TestCase
  def test_should_require_customer_name
    site = Site.new
    refute site.valid?
    refute site.save
    assert_operator site.errors.count, :>, 0
    assert site.errors.messages.include?(:customer_name)
    assert site.errors.messages[:customer_name].include?("can't be blank")
  end

  def test_should_require_customer_email
    site = Site.new
    refute site.valid?
    refute site.save
    assert_operator site.errors.count, :>, 0
    assert site.errors.messages.include?(:customer_email)
    assert site.errors.messages[:customer_email].include?("can't be blank")
  end

  def test_should_require_host
    site = Site.new
    refute site.valid?
    refute site.save
    assert_operator site.errors.count, :>, 0
    assert site.errors.messages.include?(:host)
    assert site.errors.messages[:host].include?("can't be blank")
  end

  def test_should_require_host_to_be_unique
    theme = FactoryGirl.create(:theme)
    Site.skip_callback(:create, :after, :setup_components)
    existing_site = FactoryGirl.create(:site, theme: theme)
    Site.after_create(:setup_components)
    site = Site.new(host: existing_site.host)
    refute site.valid?
    refute site.save
    assert_operator site.errors.count, :>, 0
    assert site.errors.messages.include?(:host)
    assert site.errors.messages[:host].include?("has already been taken")
  end

  def test_should_require_theme
    site = Site.new
    refute site.valid?
    refute site.save
    assert_operator site.errors.count, :>, 0
    assert site.errors.messages.include?(:theme)
    assert site.errors.messages[:theme].include?("can't be blank")
  end

  def test_should_require_user
    site = Site.new
    refute site.valid?
    refute site.save
    assert_operator site.errors.count, :>, 0
    assert site.errors.messages.include?(:user)
    assert site.errors.messages[:user].include?("can't be blank")
  end

end 
```

这导致了:

```
$ ruby -Ilib:test test/models/site_test.rb
SiteTest
 test_should_require_user PASS (0.45s)
 test_should_require_host PASS (0.01s)
 test_should_require_customer_email PASS (0.01s)
 test_should_require_host_to_be_unique PASS (0.09s)
 test_should_require_theme PASS (0.01s)
 test_should_require_customer_name PASS (0.01s)
Finished in 0.58104s
6 tests, 30 assertions, 0 failures, 0 errors, 0 skips 
```

对于一个要测试的简单特性来说，代码太多了，是吧？没错。想象一下，如果您必须在所有模型中为您想要验证的所有字段重复它。

我不知道是否有任何宝石来**干**这个东西，因为我没有费心去搜索这个问题的现有解决方案。取而代之的是，我开始利用 Ruby 的 **OO** 善良来开创自己的解决方案。这是我想到的:

```
module TestModelValidations
  def self.included(klass)
    klass.class_eval do

      def self.test_validates_presence_of(*args)
        args.each do |field_name|
          define_method("test_should_require_#{field_name.to_s}") do
            model = self.class.model_klass.new
            assert_validation(model, field_name, "can't be blank")
          end
        end
      end

      def self.test_validates_uniqueness_of(existing_model, *args)
        args.each do |field_name|
          define_method("test_should_require_#{field_name.to_s}_to_be_unique") do
            params_hash = {}
            params_hash[field_name] = existing_model.send(field_name)
            model = self.class.model_klass.new(params_hash)
            assert_validation(model, field_name, "has already been taken")
          end
        end
      end

    private
      def assert_validation(model, field_name, error_message)
        refute model.valid?
        refute model.save
        assert_operator model.errors.count, :>, 0
        assert model.errors.messages.include?(field_name)
        assert model.errors.messages[field_name].include?(error_message)
      end

    end
  end

  def model_klass
    self.class.name.underscore.split("_test").first.camelize.constantize
  end
end 
```

您可以将该文件放在 **test/support/** 中，并通过在 **test/test_helper.rb** 中添加这一行来要求在测试开始之前所有文件都在 support 目录中:

```
Dir[Rails.root.join(‘test’, ‘support’, ‘*.rb’)].each { |f| require f } 
```

你只需要在每个测试文件中包含这个模块，就可以使用这个 **DRY** ed 版本的验证测试。此外，您可以更进一步，在 **test/test_helper.rb** 中查找这个代码块:

```
class ActiveSupport::TestCase
 ActiveRecord::Migration.check_pending!
end 
```

它打开了 **ActiveSupport::TestCase** 类并扩展了它，这是每个模型测试类继承的类。在那里加上这一行:

```
include TestModelValidations 
```

现在，该块应该看起来像这样:

```
class ActiveSupport::TestCase
 ActiveRecord::Migration.check_pending!
 include TestModelValidations
end 
```

现在我们准备展示我们之前看到的测试的**干燥**版本:

```
class SiteTest < ActiveSupport::TestCase
  test_validates_presence_of :customer_email, :customer_name, :host, :theme, :user
  test_validates_uniqueness_of FactoryGirl.create(:site), :host
end 
```

对，就是这样:)。这就是测试模型验证所需要的一切。现在，我们的测试类有了类宏，就像我们在实际模型中声明验证一样。这几乎是我们以前为实现同样的事情而必须编写的代码的一半。想象一下在所有的模型测试中使用它，这有望为您节省数百行代码和复制/粘贴工作。让我们再次运行测试，以确保一切都像以前一样正常工作:

```
$ ruby -Ilib:test test/models/site_test.rb
SiteTest
 test_should_require_customer_name PASS (0.34s)
 test_should_require_user PASS (0.01s)
 test_should_require_host PASS (0.01s)
 test_should_require_host_to_be_unique PASS (0.01s)
 test_should_require_theme PASS (0.01s)
 test_should_require_customer_email PASS (0.01s)
Finished in 0.39483s
6 tests, 30 assertions, 0 failures, 0 errors, 0 skips 
```

嘣！一切都像预期的那样工作，但是更加干净和方便。这使得模型验证测试变得更加容易。

我不知道我应该深入解释我的实现，也不知道你们中有多少人有兴趣知道这个实现是如何工作的。我就说到这里，如果有任何问题，我很乐意在评论区或任何其他交流媒介回答。

我觉得稍微解释一下会有好处的一件事是这个宏/行:

```
test_validates_uniqueness_of FactoryGirl.create(:site), :host 
```

该宏验证模型字段的唯一性。第一个参数是模型的持久化实例，将从该实例复制字段。在第一个参数之后，您可以提供任意数量的参数作为要验证唯一性的字段。

就是这样。期待您的评论和提问。我真的希望这对你有所帮助。

## 分享这篇文章