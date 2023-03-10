# 使用交易 API 向您的易贝商店添加产品

> 原文：<https://www.sitepoint.com/adding-products-ebay-store-trading-api/>

在易贝交易 API 系列的第三部分，也是最后一部分，我们将在应用程序中添加功能来构建产品。

![](img/6fb2f6735c29aaf491b97067ac905445.png)

## 创造新产品

既然我们已经完成了商店设置，我们可以开始为控制器编写代码，该控制器将处理产品的创建。在`controllers`目录下，创建一个新文件，命名为`Product.php`。

```
<?php
class Product extends \SlimController\SlimController{

}
```

接下来，创建`newAction`方法。这将在`templates/product`目录下呈现`new.twig`文件。这是呈现用于创建新产品的表单的视图。

```
public function newAction(){   
    $page_data = array(
        'is_productpage' => 'true'
    );
    $this->render('product/new', $page_data);       
}
```

接下来创建视图:

```
{% extends "/base.twig" %}
{% block content %}
{{ parent() }}
<div class="row">
  <div class="col-md-4">  
    <div class="alert alert-{{ flash.message.type }}">
      {{ flash.message.text }}

      {% for r in flash.message.data %}
      <li>{{ r[0] }}</li>
      {% endfor %}
    </div>
  </div>
</div>
<div class="row">
  <div class="col-md-6">
    <form class="form-horizontal" method="POST" action="{{ baseUrl }}/products/create">
      <fieldset>
        <legend>Create new Product</legend>
        <div class="form-group">
          <label for="title" class="col-lg-2 control-label">Title</label>
          <div class="col-lg-10">
            <input type="text" class="form-control" id="title" name="title" value="{{ flash.form.title }}">
          </div>
        </div>
        <div class="form-group">
          <label for="category" class="col-lg-2 control-label">Category</label>
          <div class="col-lg-10" id="categories-container">

          </div>
        </div>
        <div class="form-group">
          <label for="price" class="col-lg-2 control-label">Price</label>
          <div class="col-lg-10">
            <input type="text" class="form-control" id="price" name="price" value="{{ flash.form.price }}">
          </div>
        </div>
        <div class="form-group">
          <label for="quantity" class="col-lg-2 control-label">Quantity</label>
          <div class="col-lg-10">
            <input type="text" class="form-control" id="quantity" name="quantity" value="{{ flash.form.quantity }}">
          </div>
        </div>      
        <div class="form-group">
          <label for="brand" class="col-lg-2 control-label">Brand</label>
          <div class="col-lg-10">
            <input type="text" class="form-control" id="brand" name="brand" value="{{ flash.form.brand }}">
          </div>
        </div>
        <div class="form-group">
          <label for="description" class="col-lg-2 control-label">Description</label>
          <div class="col-lg-10">
            <textarea class="form-control" rows="3" id="description" name="description" value="{{ flash.form.description }}">{{ flash.form.description }}</textarea>
          </div>
        </div>

        <div class="form-group">
          <div class="col-lg-10 col-lg-offset-2">
            <button type="submit" class="btn btn-primary">Add Product</button>
          </div>
        </div>
      </fieldset>
    </form>
  </div>

  <div class="col-md-6">
    <h5>Upload Photos</h5>
    <form action="{{ baseUrl }}/upload" method="POST" class="dropzone" id="photosdropzone" enctype="multipart/form-data"></form>
  </div>
</div>

{% include 'partials/categories.html' %}
{% endblock %}
```

在开始的时候，我们输出警告信息。稍后当我们添加创建产品的代码时，我们将传递这些值。`flash`是一个全局变量，我们传递的会话数据临时保存在这里:

```
<div class="alert alert-{{ flash.message.type }}">
    {{ flash.message.text }}
    {% for r in flash.message.data %}
    <li>{{ r[0] }}</li>
    {% endfor %}
</div>
```

接下来是产品标题和其他必需的产品详细信息。请注意，在大多数情况下，只需要产品名称、描述、数量、价格和产品图片。这里，我们添加了品牌，因为在某些情况下它是必需的。这种字段的通用术语是条件字段。这通常取决于产品的类别是否需要。有些类别比其他类别需要更多的字段，所以一定要检查关于 [AddItem API 调用](http://developer.ebay.com/devzone/xml/docs/reference/ebay/additem.html)的文档，以确保您没有遗漏任何内容。

```
<input type="text" class="form-control" id="title" name="title" value="{{ flash.form.title }}">
```

对于产品类别，我们稍后将通过 AJAX 加载它，因为它将取决于用户输入的产品标题。

```
<label for="category" class="col-lg-2 control-label">Category</label>
<div class="col-lg-10" id="categories-container">

</div>
```

接下来是用户将用于上传产品图片的表单:

```
<form action="{{ baseUrl }}/upload" method="POST" class="dropzone" id="photosdropzone" enctype="multipart/form-data"></form>
```

这使用了`/upload`路径来处理上传的文件。在`Product`类中声明处理该路径的方法:

```
public function uploadAction(){

    $storage = new \Upload\Storage\FileSystem('uploads');
    $file = new \Upload\File('file', $storage);

    $new_filename = uniqid();
    $file->setName($new_filename);

    $_SESSION['uploads'][] = $new_filename . '.' . $file->getExtension();

    $file->addValidations(array(
        new \Upload\Validation\Mimetype(array('image/png', 'image/gif', 'image/jpg')),
        new \Upload\Validation\Size('6M')
    ));

    $errors = array();

    try{
        $file->upload();
    }catch(Exception $e){
        $errors = $file->getErrors();
    }

    $response_data = array(
        'errors' => $errors
    );

    echo json_encode($response_data);

}
```

分解`uploadAction()`方法，首先我们指定上传路径。在这种情况下，我们使用的是`uploads`目录。请确保您为其提供了必要的目录权限，以便我们可以向其上传文件:

```
$storage = new \Upload\Storage\FileSystem('uploads');
```

接下来，我们指定用于上传文件的字段。回到上传的表单，我们没有在那里指定任何`<input type="file">`字段。那是因为我们不需要。默认情况下，dropzone 使用`file`作为上传文件的默认字段名称，因此我们将其指定为`File`方法的第一个参数，第二个参数是我们之前创建的存储:

```
$file = new \Upload\File('file', $storage);
```

接下来，我们给这个文件一个唯一的名字。如果你愿意，你可以在这里发挥创意，但是如果你没有创意，那么就使用`uniqid()`方法生成一个唯一的文件名:

```
$new_filename = uniqid();
$file->setName($new_filename);
```

将文件名推入到`uploads`会话项中，以便我们稍后使用。

```
$_SESSION['uploads'][] = $new_filename . '.' . $file->getExtension();
```

接下来，我们添加一些文件验证。我们只是告诉它只接受 png、gif 和 jpg 图像，最大文件大小应该只有 6Mb。

```
$file->addValidations(array(
        new \Upload\Validation\Mimetype(array('image/png', 'image/gif', 'image/jpg')),
        new \Upload\Validation\Size('6M')
    ));
```

最后，我们尝试上传文件，分配我们得到的任何错误，并将它们返回给客户端。但是我们不会真的在客户端利用这个错误，因为 dropzone 已经覆盖了我们。如果文件没有通过验证，它会为上传的文件显示一个 x 标记:

```
$errors = array();

try{
  $file->upload();
}catch(Exception $e){
  $errors = $file->getErrors();
}

$response_data = array(
  'errors' => $errors
);

echo json_encode($response_data);
```

回到添加新产品的模板，我们包含了一个部分。这是将用于显示建议产品类别的模板。

```
{% include 'partials/categories.html' %}
```

它包含以下内容:

```
{% raw %}
<script id="categories-template" type="text/x-handlebars-template"> {{#each categories}}
<div class="radio">
  <label>
    <input type="radio" name="category_id" id="category_id" value="{{id}}">
    {{name}}
  </label>
</div>
{{/each}} </script>
{% endraw %}
```

在上面的模板中，我们使用了车把。它的语法与 twig 语法冲突，所以我们必须将它包装在`{% raw %}`标签中，以确保 twig 不会解析它。

接下来，在`/assets/js`目录下创建`dropzone-options.js`文件，并添加以下内容:

```
(function(){

  Dropzone.options.photosdropzone = {

    acceptedFiles: "image/*",
    autoProcessQueue: true,
    uploadMultiple: false,
    maxFiles: 5
  };

})();
```

这允许我们为 dropzone 指定选项。以下是每个选项的分类:

*   `acceptedFiles`–可以上传的文件类型。
*   `autoProcessQueue`–用于指定是否立即处理队列的布尔值。
*   `uploadMultiple`–用于指定是否允许多个文件上传的布尔值。在这种情况下，我们选择了`false`，但这并不意味着我们不能上传多个图像。这意味着我们不能同时上传多张图片。
*   `maxFiles`–一个整数值，用于指定可上传文件的最大数量。

接下来，创建`new-product.js`文件并添加以下内容:

```
(function(){
  var categories_template = Handlebars.compile($("#categories-template").html());

  $('#title').blur(function(){
    var title = $(this).val();
    $.post(
      '/tester/ebay_trading_api/categories', 
      {
        'title' : title
      },
      function(response){
        var categories = JSON.parse(response);
        var html = categories_template({'categories' : categories});

        $('#categories-container').html(html);
      }
    );
  });
})();
```

上面的代码所做的是每当用户跳出产品标题字段时发出一个 POST 请求。然后，该请求返回一个包含基于产品标题的建议类别的对象数组。这个调用中使用的方法在`Product`类中。它所做的是调用`Ebay`类中的`getSuggestedCategories()`方法，将产品标题作为参数传入。然后，响应被转换成 JSON 字符串并回显出来:

```
public function categoriesAction(){
    $suggested_categories = $this->app->ebay->getSuggestedCategories($_POST['title']);
    echo json_encode($suggested_categories);
}
```

打开`Ebay`类并添加以下方法:

```
public function getSuggestedCategories($title){

  $requestBody = '<?xml version="1.0" encoding="utf-8"?>
    <GetSuggestedCategoriesRequest xmlns="urn:ebay:apis:eBLBaseComponents">
    <RequesterCredentials>
        <eBayAuthToken>' . $this->user_token . '</eBayAuthToken>
      </RequesterCredentials>
      <Query>' . $title . '</Query>
    </GetSuggestedCategoriesRequest>';

  $response = $this->request('GetSuggestedCategories', $requestBody);

  $suggested_categories = array();
  if($response->Ack == 'Success'){

    foreach($response->SuggestedCategoryArray->SuggestedCategory as $category){
      $suggested_categories[] = array(
        'id' => json_decode(json_encode($category->Category->CategoryID), true)[0],
        'name' => json_decode(json_encode($category->Category->CategoryName), true)[0]
      );
    }
  }

  return $suggested_categories;
}
```

上面的方法所做的是向[GetSuggestedCategories](http://developer.ebay.com/devzone/xml/docs/reference/ebay/GetSuggestedCategories.html)API 方法发出请求。这个调用接受产品标题作为它的参数，正如在`Query`字段中传递的那样。如果调用成功，它将返回一个建议类别的数组。我们只需要从它返回的结果中提取类别 id 和类别名称。

现在我们准备编写`createAction`方法。这是我们将产品细节保存到数据库并在 ebay 上创建产品的地方。

```
public function createAction(){

    $v = new Valitron\Validator($_POST);
    $v->rule('required', array('title', 'category_id', 'price', 'quantity', 'brand', 'description'));
    $v->rule('numeric', 'price');
    $v->rule('integer', 'quantity');
    if($v->validate()){

        if($query = $this->app->db->prepare("INSERT INTO products SET title = ?, category_id = ?, price = ?, qty = ?, brand = ?, description = ?")){

            $store_settings_result = $this->app->db->query("SELECT payment_profile, return_profile, shipping_profile, out_of_stock_control, get_it_fast, category_prefill,
                category_mapping, condition_type, country_code_type, currency_code, dispatch_time, optimal_picturesize,
                listing_duration, listing_type, item_location, postal_code, store_name, county,
                street, ebay_website, shippingservice_priority, shipping_service, shippingservice_cost, shippingservice_additionalcost
                FROM store_settings WHERE id = 1");
            $store_settings = $store_settings_result->fetch_object();

            $response = $this->app->ebay->addItem($store_settings, $_POST);

            if($response->Ack == 'Success'){

                $title = $_POST['title'];
                $category_id = $_POST['category_id'];
                $price = $_POST['price'];
                $qty = $_POST['quantity'];
                $brand = $_POST['brand'];
                $description = $_POST['description'];

                $query->bind_param("ssdiss", $title, $category_id, $price, $qty, $brand, $description);
                $query->execute();

                $this->app->flash('message', array('type' => 'success', 'text' => 'Product was created!'));
            }else{

                $long_message = json_decode(json_encode($response->Errors->LongMessage), true);
                $this->app->flash('message', array('type' => 'danger', 'text' => $long_message[0]));

            }

        }

    }else{

        $this->app->flash('form', $_POST);
        $this->app->flash('message', array(
            'type' => 'danger', 
            'text' => 'Please fix the following errors', 
            'data' => $v->errors())
        );

    }  

    $this->app->redirect('/tester/ebay_trading_api/products/new'); 
}
```

分解它。首先，我们声明 valitron 的一个新实例，传入当前存储在`$_POST`全局变量中的所有值:

```
$v = new Valitron\Validator($_POST);
```

接下来我们添加规则。基本上所有的字段都是必填的，价格应该是数值，数量应该是整数。我们指定使用`rule`方法，该方法将实际规则作为第一个参数，将字段名称作为第二个参数:

```
$v->rule('required', array('title', 'price', 'quantity', 'brand', 'description'));
$v->rule('numeric', 'price');
$v->rule('integer', 'quantity');
```

接下来，我们使用`validate()`方法进行验证。如果返回`true`，则验证通过，否则不通过。如果它返回`false`，那么我们将所有的`POST`数据和 valitron 返回的错误信息一起保存到会话中。

```
$this->app->flash('form', $_POST);
$this->app->flash('message', array(
  'type' => 'danger', 
  'text' => 'Please fix the following errors', 
  'data' => $v->errors())
);
```

如果验证通过，我们从数据库中检索存储设置，并将它们作为参数提供给`addItem()`方法和`$_POST`变量的内容。`addItem()`方法在易贝创造产品。如果调用成功，我们将创建一个准备好的语句，用于在数据库中添加产品的详细信息。完成后，我们在会话中闪现一条消息，说明产品已经创建。如果调用不成功，那么我们传递从 API 返回的响应以及一条消息，说明产品没有被创建。

```
$store_settings_result = $this->app->db->query("SELECT payment_profile, return_profile, shipping_profile, out_of_stock_control, get_it_fast, category_prefill,
    category_mapping, condition_type, country_code_type, currency_code, dispatch_time, optimal_picturesize,
    listing_duration, listing_type, item_location, postal_code, store_name, county,
    street, ebay_website, shippingservice_priority, shipping_service, shippingservice_cost, shippingservice_additionalcost
    FROM store_settings WHERE id = 1");
$store_settings = $store_settings_result->fetch_object();

$response = $this->app->ebay->addItem($store_settings, $_POST);

if($response->Ack == 'Success'){

    if($query = $this->app->db->prepare("INSERT INTO products SET title = ?, category_id = ?, price = ?, qty = ?, brand = ?, description = ?")){

        $title = $_POST['title'];
        $category_id = $_POST['category_id'];
        $price = $_POST['price'];
        $qty = $_POST['quantity'];
        $brand = $_POST['brand'];
        $description = $_POST['description'];

        $query->bind_param("ssdiss", $title, $category_id, $price, $qty, $brand, $description);
        $query->execute();

    }

    $this->app->flash('message', array('type' => 'success', 'text' => 'Product was created!'));
}else{
    $this->app->flash('message', array('type' => 'danger', 'response' => $response, 'text' => 'Product was not created!'));
}

$this->app->redirect('/tester/ebay_trading_api/products/new');
```

接下来，我们将在`Ebay`类中定义`addItem`方法。定义 2 个参数:`$store_settings`和`$item_data`。在方法内部声明一个名为`$boolean_value`的变量。这允许我们将整数值`0`转换为字符串`false`，将整数值`1`转换为字符串`true`。我们需要这样做，因为 API 只接受布尔参数的`true`或`false`。

接下来，将`uploads`会话项分配给一个变量。如果您还记得前面的内容，这个会话项是我们推送用户上传的图像的文件名的地方。接下来是`$baseupload_url`，它包含了所有上传图片的基本 URL。我们需要这个，因为`UploadSiteHostedPictures` API 方法只接受上传到 ebay 的 URL。这也意味着你需要一个可以通过公共互联网访问的真实的服务器。`UploadSiteHostedPictures`方法一次只接受一个 URL，所以我们必须遍历所有图像，并提供实际的 URL 作为`ExternalPictureURL`字段的值。如果请求成功，我们只需将返回的新图像 URL 添加到`$picture_details`变量中。这将被用作传递给呼吁在易贝上实际创建产品的参数之一。

接下来，我们请求将产品添加到易贝。这可以通过`AddItem`方法或`VerifyAddItem`方法完成。`AddItem`方法是您在生产中想要的方法，因为它将产品添加到 ebay。如果有任何费用，这些也适用。`VerifyAddItem`另一方面，它的行为就像一个沙盒方法，实际上并没有将商品添加到 ebay 上。如果你想测试`AddItem`呼叫，你可以使用它。当您调用它时，不收取任何费用，但是它让您知道将收取多少费用，或者请求实际上是否会成功。请注意，您不需要使用沙盒版本的 API 来调用`VerifyAddItem`方法。您甚至可以在 API 的实时版本上使用它。

```
public function addItem($store_settings, $item_data){

  $boolean_value = array('false', 'true');

  $product_images = $_SESSION['uploads'];

  $baseupload_url = 'http://somewhere.com/uploads/';

  $picture_details = '';
  if(!empty($product_images)){
    foreach($product_images as $img){

      $requestBody = '<?xml version="1.0" encoding="utf-8"?>
      <UploadSiteHostedPicturesRequest xmlns="urn:ebay:apis:eBLBaseComponents">
        <RequesterCredentials>
          <eBayAuthToken>' . $this->user_token . '</eBayAuthToken>
        </RequesterCredentials>
        <ExternalPictureURL>' . $baseupload_url . '' . $img . '</ExternalPictureURL>
      </UploadSiteHostedPicturesRequest>';

      $response = $this->request('UploadSiteHostedPictures', $requestBody);

      if(!empty($response->Ack) && $response->Ack != 'Failure'){
        $uploaded_img = json_decode(json_encode($response->SiteHostedPictureDetails->PictureSetMember[3]->MemberURL), true);
        $picture_details .= '<PictureURL>' . $uploaded_img[0] . '</PictureURL>';
      }
    }   
  }

  $requestBody = '<?xml version="1.0" encoding="utf-8"?>
    <AddItemRequest xmlns="urn:ebay:apis:eBLBaseComponents">
      <RequesterCredentials>
        <eBayAuthToken>' . $this->user_token . '</eBayAuthToken>
      </RequesterCredentials>
      <Item>
        <SellerProfiles> 
          <SellerPaymentProfile>
            <PaymentProfileID>' . $store_settings->payment_profile . '</PaymentProfileID>
          </SellerPaymentProfile>
          <SellerReturnProfile>
            <ReturnProfileID>' . $store_settings->return_profile . '</ReturnProfileID>
          </SellerReturnProfile>
          <SellerShippingProfile>
            <ShippingProfileID>' . $store_settings->shipping_profile . '</ShippingProfileID>
          </SellerShippingProfile>
        </SellerProfiles> 
        <OutOfStockControl>' . $store_settings->out_of_stock_control . '</OutOfStockControl>
        <GetItFast>' . $boolean_value[$store_settings->get_it_fast] . '</GetItFast>
        <CategoryBasedAttributesPrefill>' . $boolean_value[$store_settings->category_prefill] . '</CategoryBasedAttributesPrefill>
        <CategoryMappingAllowed>' . $boolean_value[$store_settings->category_mapping] . '</CategoryMappingAllowed>
        <ConditionID>' . $store_settings->condition_type . '</ConditionID>
        <Country>' . $store_settings->country_code_type . '</Country>
        <Currency>' . $store_settings->currency_code . '</Currency>
        <Description>' . $item_data['description'] . '</Description>
        <DispatchTimeMax>' . $store_settings->dispatch_time . '</DispatchTimeMax>
        <ListingDesigner>
          <OptimalPictureSize>' . $boolean_value[$store_settings->optimal_picturesize] . '</OptimalPictureSize>
        </ListingDesigner> 
        <ListingDuration>' . $store_settings->listing_duration . '</ListingDuration>
        <ListingType>' . $store_settings->listing_type . '</ListingType>
        <Location>' . $store_settings->item_location . '</Location>
        <PictureDetails>'
          . $picture_details . 
        '</PictureDetails>
        <PostalCode>' . $store_settings->postal_code . '</PostalCode>
        <PrimaryCategory>
          <CategoryID>' . $item_data['category_id'] . '</CategoryID>
        </PrimaryCategory>
        <Quantity>' . $item_data['quantity'] . '</Quantity>
        <SellerContactDetails>
          <CompanyName>' . $store_settings->store_name . '</CompanyName>
          <County>' . $store_settings->county . '</County>
          <Street>' . $store_settings->street . '</Street>
        </SellerContactDetails>
        <Site>' . $store_settings->ebay_website . '</Site>
        <StartPrice currencyID="' . $store_settings->currency_code . '">' . $item_data['price'] . '</StartPrice>
        <Title>' . $item_data['title'] . '</Title>
        <ShippingDetails>
        <ShippingServiceOptions>
          <ShippingServicePriority>' . $store_settings->shippingservice_priority . '</ShippingServicePriority>
          <ShippingService>' . $store_settings->shipping_service . '</ShippingService>
          <ShippingServiceCost currencyID="' . $store_settings->currency_code . '">' . $store_settings->shippingservice_cost . '</ShippingServiceCost>
          <ShippingServiceAdditionalCost currencyID="' . $store_settings->currency_code . '">' . $store_settings->shippingservice_additionalcost . '</ShippingServiceAdditionalCost>
        </ShippingServiceOptions>
        </ShippingDetails>
      </Item>
    </AddItemRequest>';

  $response = $this->request('AddItem', $requestBody);

  return $response; 

}
```

为了让事情更清楚，下面是我们传递给`AddItem`请求的参数的分类。这里我们传入卖家资料。每个卖家资料的唯一要求是资料 ID。当我们获取存储设置时，我们已经得到了它们，所以我们只需从数据库中获取它们。

```
<SellerProfiles> 
  <SellerPaymentProfile>
    <PaymentProfileID>' . $store_settings->payment_profile . '</PaymentProfileID>
  </SellerPaymentProfile>
  <SellerReturnProfile>
    <ReturnProfileID>' . $store_settings->return_profile . '</ReturnProfileID>
  </SellerReturnProfile>
  <SellerShippingProfile>
    <ShippingProfileID>' . $store_settings->shipping_profile . '</ShippingProfileID>
  </SellerShippingProfile>
</SellerProfiles>
```

`OutOfStockControl`是一个布尔值，用于指定在列表数量达到 0 后是否保持列表活动。将此项设置为`true`会将列表从搜索结果中排除，并且不会被查看。

```
<OutOfStockControl>' . $store_settings->out_of_stock_control . '</OutOfStockControl>
```

`GetItFast`是一个布尔值，用于指定是否启用“快速获取”发货。

```
<GetItFast>' . $boolean_value[$store_settings->get_it_fast] . '</GetItFast>
```

`CategoryBasedAttributesPrefill`是一个布尔值，用于指定是否根据所选类别预填充某些项目数据。

```
<CategoryBasedAttributesPrefill>' . $boolean_value[$store_settings->category_prefill] . '</CategoryBasedAttributesPrefill>
```

`CategoryMappingAllowed`是一个布尔值，用于指定易贝是否将查找映射到同一类别的当前类别 ID，并为列表使用新的类别 ID。

```
<CategoryMappingAllowed>' . $boolean_value[$store_settings->category_mapping] . '</CategoryMappingAllowed>
```

`ConditionID`允许您指定项目条件。易贝已经为每一个可能的项目条件分配了数值。查看页面上的表格[。](http://developer.ebay.com/devzone/guides/ebayfeatures/Development/Desc-ItemCondition.html)

```
<ConditionID>' . $store_settings->condition_type . '</ConditionID>
```

`Country`允许您指定物品所在的国家。您可以在页面查看国家代码列表及其对应的国家[。](http://developer.ebay.com/devzone/xml/docs/reference/ebay/types/countrycodetype.html)

```
<Country>' . $store_settings->country_code_type . '</Country>
```

`Currency`允许您指定表示产品价格的货币。

```
<Currency>' . $store_settings->currency_code . '</Currency>
```

`Description`是物品的描述。您也可以将 HTML 传递给这个字段。

```
<Description>' . $item_data['description'] . '</Description>
```

`DispatchTimeMax`是将物品发送或邮寄给客户的最大天数。指定`0`意味着一收到订单就将商品邮寄给客户。

```
<DispatchTimeMax>' . $store_settings->dispatch_time . '</DispatchTimeMax>
```

`OptimalPictureSize`是一个布尔值，用于指定产品图像是否将被放大以适合商品的描述。

```
<ListingDesigner>
  <OptimalPictureSize>' . $boolean_value[$store_settings->optimal_picturesize] . '</OptimalPictureSize>
</ListingDesigner>
```

`ListingDuration`允许您指定希望列表处于活动状态的天数。有效值可以在页面的[中找到。](http://developer.ebay.com/devzone/xml/docs/reference/ebay/types/ListingDurationCodeType.html)

```
<ListingDuration>' . $store_settings->listing_duration . '</ListingDuration>
```

`ListingType`允许您指定清单的类型。有效值可在[本页](http://developer.ebay.com/devzone/xml/docs/reference/ebay/types/ListingTypeCodeType.html)中找到。该字段的通常值为`Auction`或`FixedPriceItem`。

```
<ListingType>' . $store_settings->listing_type . '</ListingType>
```

`Location`是物品的位置。这是一个条件字段；如果您不知道`PostalCode`的值，那么您可以指定该字段的值。

```
<Location>' . $store_settings->item_location . '</Location>
```

`PictureDetails`是我们上传到易贝的产品图片的网址。

```
<PictureDetails>'
 . $picture_details . 
'</PictureDetails>
```

`PostalCode`是物品所在地区的邮政编码。您可以使用`Location`或该字段来指定项目的位置。

```
<PostalCode>' . $store_settings->postal_code . '</PostalCode>
```

`CategoryID`是项目主类别的 ID。

```
<PrimaryCategory>
  <CategoryID>' . $item_data['category_id'] . '</CategoryID>
</PrimaryCategory>
```

`Quantity`是一个整数值，表示您的库存中有多少件特定物品。

```
<Quantity>' . $item_data['quantity'] . '</Quantity>
```

`SellerContactDetails`是你指定卖家联络资料的地方。这是一个可选字段，它默认使用卖方信息。但是你可以用这个来覆盖设置。

```
<SellerContactDetails>
  <CompanyName>' . $store_settings->store_name . '</CompanyName>
  <County>' . $store_settings->county . '</County>
  <Street>' . $store_settings->street . '</Street>
</SellerContactDetails>
```

`Site`是产品上市的易贝网站的 ID。例如，US 代表美国，UK 代表英国。该字段的有效值列表可在本页找到。

```
<Site>' . $store_settings->ebay_website . '</Site>
```

`StartPrice`是物品首次刊登、修改或重新刊登时的起拍价。

```
<StartPrice currencyID="' . $store_settings->currency_code . '">' . $item_data['price'] . '</StartPrice>
```

`Title`是产品的标题。

```
<Title>' . $item_data['title'] . '</Title>
```

`ShippingDetails`是您指定产品运输细节的地方，例如要使用的运输服务和额外费用(如果有)。

```
<ShippingDetails>
  <ShippingServiceOptions>
    <ShippingServicePriority>' . $store_settings->shippingservice_priority . '</ShippingServicePriority>
      <ShippingService>' . $store_settings->shipping_service . '</ShippingService>
      <ShippingServiceCost currencyID="' . $store_settings->currency_code . '">' . $store_settings->shippingservice_cost . '</ShippingServiceCost>
      <ShippingServiceAdditionalCost currencyID="' . $store_settings->currency_code . '">' . $store_settings->shippingservice_additionalcost . '</ShippingServiceAdditionalCost>
  </ShippingServiceOptions>
</ShippingDetails>
```

## 结论

就是这样！在本教程中，我们通过易贝的交易 API。我们已经知道，我们可以通过为用户获取一个令牌来与他们的 API 对话。我们还将通过 API 获取用户设置、上传图片和添加产品。但这并不是 Ebay 交易 API 的全部功能。这是一个非常庞大的 API，几乎任何你能想到的可以用易贝做的关于销售产品的事情，你都可以用 API 来做。所以如果你好奇的话，一定要检查一下[文档](http://developer.ebay.com/devzone/xml/docs/Reference/ebay/index.html)。你可以在这个 [github 库](https://github.com/sitepoint-examples/sitepoint_codes)中查看我们构建的项目的源代码。

感谢反馈！

## 分享这篇文章