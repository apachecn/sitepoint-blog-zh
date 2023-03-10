# jQuery 组 DOB 规则表单验证

> 原文：<https://www.sitepoint.com/jquery-group-rules-form-validation-tip/>

如果你有一个以上的字段要作为一个组进行验证，并且不显示 3 个单独的验证信息(如出生日期、地址等)，你可以这样做！将您的验证规则分组到一条验证消息中，很高兴知道！

```
$("form").validate({
  rules: {
      DayOfBirth: { required: true },
      MonthOfBirth: { required: true },
      YearOfBirth: { required: true }
  },
  groups: {
      DateofBirth: "DayOfBirth MonthOfBirth YearOfBirth"
  },
  errorPlacement: function(error, element) {
     if (element.attr("name") == "DayOfBirth" || element.attr("name") == "MonthOfBirth" || element.attr("name") == "YearOfBirth") 
      error.insertAfter("#YearOfBirth");
     else 
      error.insertAfter(element);
}});
```

## 分享这篇文章