# 使用 Android Parcelable 在活动之间传输数据

> 原文：<https://www.sitepoint.com/transfer-data-between-activities-with-android-parcelable/>

一个 Android 应用程序通常由多个需要相互传递数据的活动组成。例如，具有元素列表的主要活动和显示这些元素细节的相应次要活动。要从一个活动转移到另一个活动，可以使用 [`intent`](https://developer.android.com/reference/android/content/Intent.html) 类将它们连接在一起。这让你可以启动一个活动，并且可以选择稍后返回(如果你是 intents 新手，在谷歌开发文档中有一个很好的资源解释这个过程)

流程的一部分是在活动之间发送数据，你可以通过我们的`intent`对象的`putExtra`和`getExtra`方法来完成。其目的是使传递不同类型数据的过程变得简单，如:

```
//Inside first activity
Intent intent = new Intent(MainActivity.this, DetailActivity.class);
intent.putExtra("name", "John Smith");
intent.putExtra("age", 26);

//inside second activity
Intent intent = getIntent();
String suburb = intent.getStringExtra("name");
Integer age = intent.getIntExtra("age"); 
```

这对于基本数据类型很有效，比如`string`、`bool`和`Integer`，但是对于对象无效。当您想将一个对象及其数据从一个活动传递到另一个活动时，应该怎么做？你使用`parcelable`界面

## 介绍 Parcelable 接口

因为对象可以包含任意数量的混合数据类型，所以不能使用`putExtra`来移动值。Java 拥有 [`serialize`](https://docs.oracle.com/javase/tutorial/jndi/objects/serial.html) 对象的能力已经有一段时间了，但这是一个缓慢的过程，因为系统需要执行繁重的工作来移动数据。Parcelable 是一个 Android 专用接口，用于序列化一个类，因此它的属性可以从一个活动转移到另一个活动。

## 实现 Parcelable

Parcelable 接口将方法添加到您希望能够在活动之间转移的所有类中。这些方法是 parcelable 如何在一个活动中解构对象，并在另一个活动中重建它。

对于这个例子，您将看到如何在一个简单的类中实现 parcelable。我在以前的一篇文章中使用了这个[类，在那里我创建了一个属性列表和一个相应的详细活动。这个想法是，当您在列表视图中选择一个属性时，您会被带到一个扩展的详细视图中以找到更多信息。](https://www.sitepoint.com/custom-data-layouts-with-your-own-android-arrayadapter/)

![Example Screens](img/cdb329b791bad7238663f184aa648d89.png)

### 创建属性类

首先创建一个类来保存每个属性的信息。首先，将以下内容添加到项目的新类文件中。

```
public class Property implements {
    //property basics
    private int streetNumber;
    private String streetName;
    private String suburb;
    private String state;
    private String description;
    private String image;
    private Double price;
    private int bedrooms;
    private int bathrooms;
    private int carspots;
    private Boolean featured;

    //main constructor
    public Property(int streetNumber, String streetName, String suburb, String state, String description, Double price, String image, int bedrooms, int bathrooms, int carspots, Boolean featured){

        this.streetNumber = streetNumber;
        this.streetName = streetName;
        this.suburb = suburb;
        this.state = state;
        this.description = description;
        this.price = price;
        this.image = image;
        this.bedrooms = bedrooms;
        this.bathrooms = bathrooms;
        this.carspots = carspots;
        this.featured = featured;
    }

    //getters
    public int getStreetNumber() { return streetNumber; }
    public String getStreetName() {return streetName; }
    public String getSuburb() {return suburb; }
    public String getState() {return state; }
    public String getDescription() {return description; }
    public Double getPrice() {return price; }
    public String getImage() { return image; }
    public int getBedrooms(){ return bedrooms; }
    public int getBathrooms(){ return bathrooms; }
    public int getCarspots(){ return carspots; }
    public Boolean getFeatured(){return featured; }
} 
```

该类包含一系列属性、getter 函数和一个构造函数来创建该类的单个对象。

## 添加可打包接口

下面是主要部分，实现接口。按如下方式更改类的声明:

```
public class Property implements Parcelable {
  ...
} 
```

在类中添加以下方法。我将很快介绍一下它们是如何工作的。

```
//write object values to parcel for storage
public void writeToParcel(Parcel dest, int flags){
    //write all properties to the parcle
}

//constructor used for parcel
public Property(Parcel parcel){
    //read and set saved values from parcel
}

//creator - used when un-parceling our parcle (creating the object)
public static final Parcelable.Creator<Property> CREATOR = new Parcelable.Creator<Property>(){

    @Override
    public Property createFromParcel(Parcel parcel) {
        return new Property(parcel);
    }

    @Override
    public Property[] newArray(int size) {
        return new Property[0];
    }
};

//return hashcode of object
public int describeContents() {
    return hashCode();
} 
```

Parcelable 需要这些代码才能运行。它处理将您的对象数据复制到一个`parcel`中以便在活动之间传输，然后在另一端重新创建对象的过程。

让我们来分析一下这一切是如何工作的。

### 书写方法—`writeToParcel`

在此方法中，您将所有的类属性添加到宗地中，为传输做准备。您使用每个`write`方法来添加您的每个属性。

```
//write object values to parcel for storage
public void writeToParcel(Parcel dest, int flags){
    dest.writeString(streetName);
    dest.writeString(suburb);
    dest.writeString(state);
    dest.writeString(description);
    dest.writeString(image);
    dest.writeInt(streetNumber);
    dest.writeValue(featured);
    dest.writeDouble(price);
    dest.writeInt(bedrooms);
    dest.writeInt(bathrooms);
    dest.writeInt(carspots);
} 
```

**注意事项**:

*   写这些值的顺序很重要。当稍后收集这些值时，您需要按照**相同的顺序**收集它们。
*   如果你要发送`boolean`值(例如`featured`属性)。你必须使用`writeValue`,然后强制将其转换为另一端的`boolean`,因为没有添加布尔值的本地方法。

### 阅读方法—`Property`

此方法是构造函数，在接收活动中调用，您将在其中收集值。

当辅助活动调用`intent`对象的`getParcelableExtra`方法来启动流程时。此构造函数是您收集值和设置对象属性的地方:

```
//constructor used for parcel
public Property(Parcel parcel){
    streetName = parcel.readString();
    suburb = parcel.readString();
    state = parcel.readString();
    description = parcel.readString();
    image = parcel.readString();
    streetNumber = parcel.readInt();
    featured = (Boolean) parcel.readValue(null);
    price = parcel.readDouble();
    bedrooms = parcel.readInt();
    bathrooms = parcel.readInt();
    carspots = parcel.readInt();
} 
```

此时，您已经用数据填充了对象。

如果你使用你自己的对象，那么这个函数的名字将匹配你自己的类名，例如`person`、`animal`、`place`，这个名字必须匹配类名。

### 创建者方法

Parcelable 需要这个方法将所有东西绑定在一起。这里您几乎不需要做什么，因为`createFromParcel`方法将返回您新填充的对象。

```
//used when un-parceling our parcel (creating the object)
public static final Parcelable.Creator<Property> CREATOR = new Parcelable.Creator<Property>(){

    @Override
    public Property createFromParcel(Parcel parcel) {
        return new Property(parcel);
    }

    @Override
    public Property[] newArray(int size) {
        return new Property[0];
    }
}; 
```

### 其他方法—`describeContents`

这个方法做的不多。

```
//return hashcode of object
public int describeContents() {
    return hashCode();
} 
```

## 在第一个活动中开始意图

在第一个活动中，创建您的`intent`对象，并使用`putExtra`方法将整个类添加为额外的类。这只有在你正确实现了`Parcelable`接口的情况下才有效(因为此时 parcelable 开始序列化你的对象)。

```
Intent intent = new Intent(MainActivity.this, DetailActivity.class);
intent.putExtra("Property", property);
startActivity(intent); 
```

如果这有效，新的活动将会打开。您需要确保将活动添加到您的清单文件中。

## 在次要活动中收集值

现在你在第二个活动中，你需要收集`intent`对象并提取已经转换成`parcel`的`property`类。一旦你完成了这些，你就可以调用标准方法来获取数据，比如房产名称、价格和描述。

```
//collect our intent
Intent intent = getIntent();
Property property = intent.getParcelableExtra("Property");

//now collect all property values
String streetName = property.getStreetName();
Integer streetNumber = property.getStreetNumber();
String suburb = property.getSuburb();
String state = property.getState();
String description = property.getDescription();
Double price = property.getPrice();
Integer bedrooms = property.getBedrooms();
Integer bathrooms = property.getBathrooms();
Integer carspots = property.getCarspots();
String image = property.getImage();
Integer imageID = this.getResources().getIdentifier(image, "drawable", this.getPackageName());
String address = streetNumber + " " + streetName + ", " + suburb + ", " + state; 
```

## 传包裹

如您所见，打包并运行还需要一些工作，但是一旦全部完成，回报是值得的。

您可以将整个对象从一个活动移动到另一个活动，同时保留所有的类方法和功能。如果您有一个转换数据的复杂方法，这将非常有用。

你不再需要记住通常作为额外数据传递给`intent`对象的不同名称，例如`String streetName = intent.getStringExtra("streetName");`。你可以得到你的目标，继续前进。

该过程是快速和优化的。这样做比使用 Java 的`serialize`功能更快，因为您隐式地告诉它如何保存和提取您的数据。

如果您有任何意见或问题，请告诉我。

## 分享这篇文章