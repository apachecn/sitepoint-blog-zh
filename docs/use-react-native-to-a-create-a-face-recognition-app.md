# 使用 React Native 创建人脸识别应用程序

> 原文：<https://www.sitepoint.com/use-react-native-to-a-create-a-face-recognition-app/>

在本教程中，我将使用 [Microsoft Face API](https://www.microsoft.com/cognitive-services/en-us/face-api) 创建一个带有 React Native 的人脸识别应用程序。我将假设您已经构建了一个 React 本机应用程序，因此不会涵盖代码的所有部分。如果你是 React Native 的新手，我推荐你阅读我之前的教程“用 React Native 构建一个 Android 应用程序[”。你可以在](https://www.sitepoint.com/build-android-app-react-native/) [Github](https://github.com/sitepoint-editors/RNSimilar) 上找到本教程应用的完整源代码。

## 获取 API 密钥

第一步是从微软 Azure 获取一个 API 密匙。如果你不知道微软 Azure，它是微软的云计算平台。如果您还没有 Microsoft Live 帐户,请注册一个[。对于那些刚接触 Azure 的人，注册一个](https://signup.live.com/)[免费试用版](https://azure.microsoft.com/en-us/free/)。这将给你 30 天的时间免费使用 Azure，前提是你不能超过免费层的 HTTP 请求。如果您之前已经创建了一个帐户，您可以使用现有的订阅计划。如果你没有，选择[的现收现付](https://azure.microsoft.com/en-us/offers/ms-azr-0003p/)计划，这样你只需为你使用的东西付费。

注册账户后，登录并点击屏幕顶部的*搜索*图标，搜索“认知服务”。从显示的下拉菜单中，点击*认知服务账户(预览)*，您应该会看到以下屏幕:

![cognitive services accounts](img/09d80df7469b255a1f353bd440287be7.png)

点击*添加*，将显示以下屏幕:

![create account](img/7a3e0eff5dfec01c00351bfbf0eec18b.png)

填写必填字段。您可以输入任何您想要的帐户名称，如果您是新用户，订阅应该是“免费试用”，如果您是现有用户，订阅应该是“现收现付”。*资源组*可以是您想要的任何名称。*资源组位置*应该是在你当前位置附近的一个服务器，但是如果你愿意的话，你可以坚持使用默认位置。对于 *API 类型*，为*定价层*选择“Face API”，然后选择“free”。这将确保你不会花费任何东西。填写完所有内容后，单击 *create* 按钮，等待资源完成部署。

一旦资源部署完毕，点击左侧工具条上的 *All resources* 菜单。您创建的资源应该在那里列出，否则刷新页面。单击该资源，您应该会看到以下屏幕:

![resource](img/04f96a2aed874694f7fc185a07ff58e4.png)

点击*键*查看 API 键。记下这些键中的一个，因为稍后向 API 发出请求时会用到它们。

## 构建应用程序

您将要构建的应用程序将完成以下任务:

*   **创建面孔列表**:面孔列表是面孔的容器。可以把它想象成一组相关面孔的数据库。
*   **添加人脸到人脸列表**:人脸列表是一个容器，你需要添加包含单张人脸的照片。这些将用于后面的比较。
*   **获取相似人脸**:用于根据照片上检测到的人脸，从人脸列表中获取相似人脸。

流程是先创建一个人脸列表，将人脸的照片添加到其中，然后根据提供的一张照片得到一个相似人脸的列表。

以下是用户上传照片并按下按钮获得相似面孔后该应用程序的外观:

![final output](img/9e7637eaf4a8d50f1bb4416d1bc8e88b.png)

### 安装依赖项

首先创建一个新的 React 本地项目:

```
react-native init RNSimilar 
```

**注意**:我在本教程中使用的是 React 原生版本 0.25.1。React Native 项目进展很快，所以如果在撰写本文时已经有了更高的版本，那么对于 React Native 的这个版本，所使用的依赖项可能会中断。如果你在以后读到这篇文章，我推荐 [rninit](https://www.npmjs.com/package/rninit) ，它是 React Native CLI 工具的替代工具，允许你安装 React Native 的特定版本。您可以使用以下命令进行全局安装:

```
npm install -g rninit 
```

安装后，您可以使用以下命令创建一个新的 React 本地项目:

```
rninit init RNSimilar --source react-native@0.25.1 
```

这将安装 React 原生版本 0.25.1 和 React 版本 0.14.8。

在*r similar*文件夹中导航，打开 *package.json* ，你应该看到以下内容:

```
{
  "name": "RNSimilar",
  "version": "0.0.1",
  "private": true,
  "scripts": {
    "start": "react-native start"
  },
  "dependencies": {
    "react": "^0.14.8",
    "react-native": "^0.25.1"
  }
} 
```

在`react-native`下方增加以下内容:

```
"react-native-button": "^1.6.0",
"react-native-fetch-blob": "^0.4.0",
"react-native-image-picker": "^0.18.15" 
```

运行`npm install`安装刚刚添加的模块。

您使用 **react-native-button** 依赖项来创建按钮，**react-native-image-picker**用于从用户照片库(或设备摄像头)中挑选图像， **react-native-fetch-blob** 用于将挑选的图像的 blob 发送到 API。

接下来，全局安装 [React 本地包管理器](https://github.com/rnpm/rnpm):

```
npm install rnpm -g 
```

安装完成后，当您仍然在项目的根目录下时，运行`rnpm link`来自动配置项目，以添加必要的 android 权限并链接文件，以便第三方模块工作。

### 入口点文件

打开 *index.android.js* ，用以下代码替换默认代码:

```
import React, { Component } from 'react';
import {
  AppRegistry,
  StyleSheet,
  View
} from 'react-native';

import SimilarFaces from './components/SimilarFaces';

const image_picker_options = {
  title: 'Select Photo',
  takePhotoButtonTitle: 'Take Photo...',
  chooseFromLibraryButtonTitle: 'Choose from Library...',
  cameraType: 'back',
  mediaType: 'photo',
  maxWidth: 480,
  quality: 1,
  noData: false,
};

//the API Key that you got from Microsoft Azure
const api_key = 'YOUR FACE API KEY';

class RNSimilar extends Component {

  render() {
    return (
      <View style={styles.container}>
        <SimilarFaces imagePickerOptions={image_picker_options} apiKey={api_key} />
      </View>
    );
  }

}

const styles = StyleSheet.create({
  container: {
    flex: 1,
    justifyContent: 'center',
    alignItems: 'center',
    backgroundColor: '#ccc',
  }
});

AppRegistry.registerComponent('RNSimilar', () => RNSimilar); 
```

分解上面的代码。首先导入`SimilarFaces`组件，在这里可以找到大部分的应用程序逻辑。

```
import SimilarFaces from './components/SimilarFaces'; 
```

声明图像拾取器将使用的选项。这些大部分都很清楚，所以我不打算在这里解释。你可以访问这个组件的 [Github repo，看看你还能提供哪些选项。](https://github.com/marcshilling/react-native-image-picker)

```
const image_picker_options = {
  title: 'Select Photo',
  takePhotoButtonTitle: 'Take Photo...',
  chooseFromLibraryButtonTitle: 'Choose from Library...',
  cameraType: 'back',
  mediaType: 'photo',
  maxWidth: 480,
  quality: 1,
  noData: false,
}; 
```

添加之前的 API 密钥:

```
const api_key = 'YOUR FACE API KEY'; 
```

在主应用程序组件中，使用您将很快创建的`SimilarFaces`组件。将图像拾取器选项和 API 键作为属性传入，以便可以在组件内部访问它们。

```
class RNSimilar extends Component {

  render() {
    return (
      <View style={styles.container}>
        <SimilarFaces imagePickerOptions={image_picker_options} apiKey={api_key} />
      </View>
    );
  }

} 
```

### 相似面组件

创建一个*components/similar faces . js*文件，并添加以下内容:

```
import {
  AppRegistry,
  StyleSheet,
  Text,
  View,
  Image,
  TextInput,
  ScrollView
} from 'react-native';

import React, { Component } from 'react';

import NativeModules, { ImagePickerManager } from 'NativeModules';
import Button from 'react-native-button';

import Requestor from '../lib/Requestor';

let facelist_id = 'facelist_005';
let facelist_data = {
  name: 'My 5th facelist'
};

let face_api_base_url = 'https://api.projectoxford.ai';

export default class SimilarFaces extends Component {
  constructor(props) {
    super(props);
    this.state = {
      name: '',
        photo_style: {
            width: 480,
            height: 480
        },
        photo: null,
      similar_photo: null,
      message: ''
    };
  }

  render() {
    return (
      <ScrollView>
        <View style={styles.container}>
          <Button
            containerStyle={styles.button}
            onPress={this._createFaceList.bind(this)}>
            Create Face List
          </Button>

          <Image
            style={this.state.photo_style}
            source={this.state.photo}
            resizeMode={"contain"}
          />

          <Button
            containerStyle={styles.button}
            onPress={this._pickImage.bind(this)}>
            Pick Image
          </Button>

          <TextInput
            style={styles.text_input}
            onChangeText={this._changeName.bind(this)}
            value={this.state.name}
            placeholder={"name"}
          />

            <Button
                containerStyle={styles.button}
                onPress={this._addFaceToFaceList.bind(this)}>
                Add Face to Face List
            </Button>

            <Button
                containerStyle={styles.button}
                onPress={this._getSimilarFace.bind(this)}>
                Get Similar Face
            </Button>

          <Image
            style={this.state.photo_style}
            source={this.state.similar_photo}
            resizeMode={"contain"}
          />

          <Text style={styles.message}>{this.state.message}</Text>
        </View>
      </ScrollView>

    );
  }

  _changeName(text) {
    this.setState({
      name: text
    });
  }

  _pickImage() {

    ImagePickerManager.showImagePicker(this.props.imagePickerOptions, (response) => {

      if(response.error){
        alert('Error getting the image. Please try again.');
      }else{

        let source = {uri: response.uri};

        this.setState({
          photo_style: {
            width: response.width,
            height: response.height
          },
          photo: source,
          photo_data: response.data
        });

      }
    });

  }

  _createFaceList() {

    Requestor.request(
      face_api_base_url + '/face/v1.0/facelists/' + facelist_id,
      'PUT',
      this.props.apiKey,
      JSON.stringify(facelist_data)
    )
    .then(function(res){
      alert('Face List Created!');
    });

  }

  _addFaceToFaceList() {

    var user_data = {
      name: this.state.name,
      filename: this.state.photo.uri
    };

    Requestor.upload(
      face_api_base_url + '/face/v1.0/facelists/' + facelist_id + '/persistedFaces',
      this.props.apiKey,
      this.state.photo_data,
      {
        userData: JSON.stringify(user_data)
      }
    )
    .then((res) => {

      alert('Face was added to face list!');

    });

  }

  _getSimilarFace() {

    Requestor.upload(
      face_api_base_url + '/face/v1.0/detect',
      this.props.apiKey,
      this.state.photo_data
    )
    .then((facedetect_res) => {

      let face_id = facedetect_res[0].faceId;

      let data = {
        faceId: face_id,
        faceListId: facelist_id,
        maxNumOfCandidatesReturned: 2
      }

      Requestor.request(
        face_api_base_url + '/face/v1.0/findsimilars',
        'POST',
        this.props.apiKey,
        JSON.stringify(data)
      )
      .then((similarfaces_res) => {

        let similar_face = similarfaces_res[1];

        Requestor.request(
          face_api_base_url + '/face/v1.0/facelists/' + facelist_id,
          'GET',
          this.props.apiKey
        )
        .then((facelist_res) => {

          let user_data = {};
          facelist_res['persistedFaces'].forEach((face) => {
            if(face.persistedFaceId == similar_face.persistedFaceId){
              user_data = JSON.parse(face.userData);
            }
          });

          this.setState({
            similar_photo: {uri: user_data.filename},
            message: 'Similar to: ' + user_data.name + ' with confidence of ' + similar_face.confidence
          });

        });

      });

    });

  }
}

const styles = StyleSheet.create({
  container: {
    flex: 1,
    alignItems: 'center'
  },
  button: {
    padding: 10,
    margin: 20,
    height: 45,
    overflow: 'hidden',
    backgroundColor: 'white'
  },
  text_input: {
    height: 40,
    borderColor: 'gray',
    borderWidth: 1,
    backgroundColor: '#FFF'
  },
  message: {
    fontSize: 20,
    fontWeight: 'bold'
  }
});

AppRegistry.registerComponent('SimilarFaces', () => SimilarFaces); 
```

分解上面的代码。首先是通常的导入语句:

```
import {
  AppRegistry,
  StyleSheet,
  Text,
  View,
  Image,
  TextInput,
  ScrollView
} from 'react-native'; 
```

包括**反应本地按钮**和**反应本地图像拾取器**:

```
import NativeModules, { ImagePickerManager } from 'NativeModules';
import Button from 'react-native-button'; 
```

包括定制库，用于向 Face API 发出 HTTP 请求。稍后您将在`Requestor`部分创建它。

```
import Requestor from '../lib/Requestor'; 
```

声明在创建面列表、将面添加到面列表和获得相似面时使用的数据。`facelist_id`是面部列表的唯一 ID，而`facelist_data`是您想要赋予面部列表的描述性名称。如果需要，您可以更改这些值。

```
let facelist_id = 'facelist_003';
let facelist_data = {
  name: 'My facelist'
}; 
```

为 Face API 添加基本 URL:

```
let face_api_base_url = 'https://api.projectoxford.ai'; 
```

为将在组件内部使用的数据添加默认值:

```
constructor(props) {
  super(props);
  this.state = {
    name: '', //the name of the person to add
    photo_style: { //default styling for the selected photo
      position: 'relative',
      width: 480,
      height: 480
    },
    photo: null, //the source uri of the selected photo
    similar_photo: null, //the source uri of the similar photo
    message: '' //the message to display once a similar face is returned
  };
} 
```

`render`方法返回 app 的 UI。这包含用于创建面部列表的按钮，显示用户选择的照片，用于选择照片的按钮，用于输入添加的人的姓名的文本输入，用于获得相似面部的按钮，相似面部的照片和自定义消息。

```
render() {
  return (
    <ScrollView>
      <View style={styles.container}>
        <Button
          containerStyle={styles.button}
          onPress={this._createFaceList.bind(this)}>
          Create Face List
        </Button>

        <Image
          style={this.state.photo_style}
          source={this.state.photo}
          resizeMode={"contain"}
        />

        <Button
          containerStyle={styles.button}
          onPress={this._pickImage.bind(this)}>
          Pick Image
        </Button>

        <TextInput
          style={styles.text_input}
          onChangeText={this._changeName.bind(this)}
          value={this.state.name}
          placeholder={"name"}
        />

        <Button
          containerStyle={styles.button}
          onPress={this._addFaceToFaceList.bind(this)}>
          Add Face to Face List
        </Button>

        <Button
          containerStyle={styles.button}
          onPress={this._getSimilarFace.bind(this)}>
          Get Similar Face
        </Button>

        <Image
          style={this.state.photo_style}
          source={this.state.similar_photo}
          resizeMode={"contain"}
        />

        <Text style={styles.message}>{this.state.message}</Text>
      </View>
    </ScrollView>

  );
} 
```

`_changeName`函数更新用于输入人名的文本字段的新值的状态。

```
_changeName(text) {
  this.setState({
    name: text
  });
} 
```

`_pickImage`功能允许用户从相机或图库中选择图像。这使用了**react-native-image-picker**库。收到响应后，用所选图像的尺寸更新图像容器的样式。还将文件路径和图像的 base64 表示设置为状态。这样，您可以在以后向面孔列表添加面孔或获取相似面孔时使用它。

```
_pickImage() {

  ImagePickerManager.showImagePicker(this.props.imagePickerOptions, (response) => {

    if(response.error){
      alert('Error getting the image. Please try again.');
    }else{

      let source = {uri: response.uri};

      this.setState({
        photo_style: {
          width: response.width,
          height: response.height
        },
        photo: source, //file path of the image
        photo_data: response.data //base64 representation of the image
      });

    }
  });

} 
```

接下来是`_createFaceList`函数，它向 Face API 发送一个请求，请求[创建一个新的面孔列表](https://dev.projectoxford.ai/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524b)。`facelist_data`是您之前在文件顶部附近声明的硬编码对象。

```
_createFaceList() {

  Requestor.request(
    face_api_base_url + '/face/v1.0/facelists/' + facelist_id,
    'PUT',
    this.props.apiKey,
    JSON.stringify(facelist_data)
  )
  .then(function(res){
    alert('Face List Created!');
  });

} 
```

`_addFaceToFaceList`函数[将面部数据添加到面部列表](https://dev.projectoxford.ai/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250)，这次使用来自请求者库的`upload`函数。它接受将请求发送到的 URL 作为第一个参数，API 键作为第二个参数，所选照片的 base64 表示作为第三个参数，用户数据作为第四个参数。`userData`的值被转换成一个字符串，因为`upload`函数将它作为查询参数发送。这也是为什么您被限制发送最大长度为 1KB 的最少数据。

```
_addFaceToFaceList() {

  var user_data = {
    name: this.state.name,
    filename: this.state.photo.uri
  };

  Requestor.upload(
    face_api_base_url + '/face/v1.0/facelists/' + facelist_id + '/persistedFaces',
    this.props.apiKey,
    this.state.photo_data,
    {
      userData: JSON.stringify(user_data)
    }
  )
  .then((res) => {

    alert('Face was added to face list!');

  });

} 
```

`_getSimilarFace`功能首先向[人脸检测](https://dev.projectoxford.ai/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)端点发送请求。这将为照片中检测到的面部分配一个唯一的 ID，并返回它们以及宽度、高度、顶部和左侧位置，以构建一个包围检测到的面部的框。但是在本教程中，您只使用了面 ID。

```
_getSimilarFace() {

  Requestor.upload(
    face_api_base_url + '/face/v1.0/detect',
    this.props.apiKey,
    this.state.photo_data
  )
  .then((facedetect_res) => {

    ...

  });

} 
```

以下是一个示例回答，让您了解您正在处理的数据是什么样的:

```
[
    {
        "faceId": "c5c24a82-6845-4031-9d5d-978df9175426",
        "faceRectangle": {
            "width": 78,
            "height": 78,
            "left": 394,
            "top": 54
        }
    }
] 
```

一旦收到响应，就从第一行数据中提取`faceId`。下面的代码假设照片中只有一张脸，这就是为什么它只提取第一行。

```
let face_id = facedetect_res[0].faceId; 
```

接下来，构造包含[寻找相似面孔端点](https://dev.projectoxford.ai/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524f)所需数据的对象。这包括从 face detect 调用收到的`face_id`、face API 将要执行搜索的 Face 列表的 ID 以及要返回的相似面孔的最大数量。在这种情况下坚持两个。

您指定了`2`，尽管您将只显示一张相似的脸，因为指定`1`将返回最匹配的照片。这意味着如果您将 me.jpg 和 me2.jpg 的*添加到人脸列表中，然后再次选择*me.jpg*以获得相似的人脸，如果您仅指定`1`作为`maxNumOfCandidatesReturned`的值，它将返回*me.jpg*。*

如果您指定`2`，然后从结果中提取第二行，您将得到*me2.jpg*。反之亦然(将*me2.jpg*提供给相似面端点返回*me.jpg*)。所有这一切意味着你得到的是第二个最接近的匹配，所以 API 不会返回相同的照片。

```
let data = {
  faceId: face_id,
  faceListId: facelist_id,
  maxNumOfCandidatesReturned: 2
} 
```

将请求发送到 find similar faces 端点，将构造的数据转换成 string，因为这是 Face API 所期望的。

```
Requestor.request(
  face_api_base_url + '/face/v1.0/findsimilars',
  'POST',
  this.props.apiKey,
  JSON.stringify(data) //convert data to a string
)
.then((similarfaces_res) => {

    ...

}); 
```

收到响应后，从返回的结果中提取第二行。然后向[端点发出请求，以获取特定的面部列表](https://dev.projectoxford.ai/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524c)。这包含用户添加的所有面部数据。遍历它，找到与相似面端点返回的面 ID 相同的面。

**注意**:您需要解析`userData`，因为它被保存为字符串。然后，您使用这些数据来更新带有相似照片的状态，以及包含相似面孔的名称和置信度的消息。置信度介于 0 和 1 之间。数字越接近 1 意味着用户提供的照片与 API 返回的照片是同一个人的可能性越高。

```
let similar_face = similarfaces_res[1];

Requestor.request(
  face_api_base_url + '/face/v1.0/facelists/' + facelist_id,
  'GET',
  this.props.apiKey
)
.then((facelist_res) => {

  let user_data = {};
  facelist_res['persistedFaces'].forEach((face) => {
    if(face.persistedFaceId == similar_face.persistedFaceId){
      user_data = JSON.parse(face.userData);
    }
  });

  this.setState({
    similar_photo: {uri: user_data.filename},
    message: 'Similar to: ' + user_data.name + ' with confidence of ' + similar_face.confidence
  });

}); 
```

以下是来自“查找相似面孔”端点的示例响应:

```
[
    {
        "persistedFaceId" : "015839fb-fbd9-4f79-ace9-7675fc2f1dd9",
        "confidence" : 0.82
    },
    ...
] 
```

添加样式:

```
const styles = StyleSheet.create({
  container: {
    flex: 1,
    alignItems: 'center'
  },
  button: {
    padding: 10,
    margin: 20,
    height: 45,
    overflow: 'hidden',
    backgroundColor: 'white'
  },
  text_input: {
    height: 40,
    borderColor: 'gray',
    borderWidth: 1,
    backgroundColor: '#FFF'
  },
  message: {
    fontSize: 30,
    fontWeight: 'bold'
  }
}); 
```

### 请求者

请求者库向 Face API 发出 HTTP 请求。创建一个 *lib/Requestor.js* 文件，并添加以下内容:

```
import RNFetchBlob from 'react-native-fetch-blob';

module.exports = {
    upload: function(url, api_key, photo, query_params){

        var ret = [];
        for(var d in query_params){
            ret.push(encodeURIComponent(d) + "=" + encodeURIComponent(query_params[d]));
        }

        var url = url + "?" + ret.join("&");

        return RNFetchBlob.fetch('POST', url, {
            'Accept': 'application/json',
            'Content-Type': 'application/octet-stream',
            'Ocp-Apim-Subscription-Key': api_key
        }, photo)
        .then((res) => {
            return res.json();
        })
        .then((json) => {
            return json;
        })
        .catch(function (error) {
            console.log(error);
        });
    },

    request: function(url, method, api_key, data){

        let headers = {
            'Content-Type': 'application/json',
            'Ocp-Apim-Subscription-Key': api_key
        };

        let options = {
            'method': method,
            'headers': headers
        };

        if(typeof data != 'undefined'){
            options.body = data;
        }

        return fetch(url, options)
        .then((res) => {
            return res.json();
        })
        .then((json) => {
            return json;
        })
        .catch(function(error){
            console.log(error);
        });
    }

} 
```

分解上面的代码。`upload`函数处理所有上传文件到 Face API 的 HTTP 请求。这使用了 **react-native-fetch-blob** 库来发送照片的 base64 表示以及 Face API 所需的头信息。

```
upload: function(url, api_key, photo, query_params){

  if(typeof query_params != 'undefined'){
    //construct the query parameter from the query_params object
    let ret = [];
    for(let d in query_params){
      ret.push(encodeURIComponent(d) + "=" + encodeURIComponent(query_params[d]));
    }

    let url = url + "?" + ret.join("&"); //combine the query parameters with the URL
  }

  return RNFetchBlob.fetch('POST', url, {
    'Accept': 'application/json',
      'Content-Type': 'application/octet-stream',
      'Ocp-Apim-Subscription-Key': api_key
  }, photo)
  .then((res) => {
    return res.json();
  })
  .then((json) => {
    return json;
  })
  .catch(function (error) {
    console.log(error);
  });
}, 
```

接下来是`request`方法，它处理简单的 HTTP 请求，比如向服务器发送字符串数据。

```
request: function(url, method, api_key, data){

    let headers = {
        'Content-Type': 'application/json',
        'Ocp-Apim-Subscription-Key': api_key
    };

    let options = {
        'method': method,
        'headers': headers
    };

    if(typeof data != 'undefined'){
        options.body = data;
    }

    return fetch(url, options)
    .then((res) => {
        return res.json();
    })
    .then((json) => {
        return json;
    })
    .catch(function(error){
        console.log(error);
    });
} 
```

## 玩应用程序

启动应用程序。你需要做的第一件事是从网上下载人脸照片。确保每张照片包含一张脸，并且每个人至少应该有两张照片。

下载照片后，将照片复制到您的设备中，然后运行应用程序。

点击*创建面部列表*按钮。您应该会看到一个警告，提示面部列表已创建。接下来，点击*选择图像*按钮，选择一张照片。输入该人的姓名并点击*添加面对面列表*按钮。您应该会看到一个警告，提示该人脸已被添加。想做多少次就做多少次，但要确保每个人至少有两张照片。

最后，再次点击*选择图像*按钮，选择您想要用来获得相似面部的照片。一旦选中，点击*按钮得到相似的脸*。这将返回您之前添加的那个人的一张照片，但不是您选择的同一张照片。

## 面向未来

就是这样！在本教程中，您使用了 Microsoft Face API。具体包括人脸检测和相似人脸搜索功能。使用 Face API 还可以做其他一些很酷的事情，下面是一些值得研究的特性:

*   **面部检测**:这是获得相似面部的先决条件，这就是为什么我没有介绍面部检测功能，如估计一个人的年龄或猜测他们的性别。你也可以确定面部特征，比如一个人是否戴眼镜，或者面部毛发。
*   **人脸分组**:根据相似度对人脸进行分组。
*   **人脸验证**:对检测到的两张人脸进行认证。您可以在 React 本机应用程序中将此用作一种身份验证形式。用户可以自拍登录，而不是输入电子邮件和密码。
*   **人脸识别**:从检测到的人脸中识别人。像 similar faces API 一样，您必须先添加 faces，然后才能使用它。

*任何其他意见或问题，请在下面*告诉我。

## 分享这篇文章