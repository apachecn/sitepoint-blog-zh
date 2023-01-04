# 用 React Native 和 Pusher 构建一个有状态的实时应用

> 原文：<https://www.sitepoint.com/build-a-stateful-real-time-app-with-react-native-and-pusher/>

用户现在希望应用程序能够实时更新并对他们的行为做出反应。幸运的是，现在有很多语言变体和库可以帮助你创建这些高度动态的应用程序。在本教程中，您将学习如何使用 Pusher、React-native 和 Redux 构建一个实时聊天应用程序来管理应用程序的状态。

你可以在 GitHub 上找到完整的项目。

## 安装依赖项

### 推进器

Pusher 是一个实时通信平台，用于通过订阅频道向听众广播消息。听众订阅一个频道，消息被广播到该频道，并且所有听众接收消息。

您首先需要[创建一个账户](http://pusher.com)，然后使用以下命令安装 Pusher npm 模块:

```
npm init
npm install pusher -g
npm install pusher-js -g 
```

在 Pusher 项目的 *App Keys* 部分下，记下`app_id`、`key`和`secret`的值。

### 反应自然

React Native 是一个构建丰富、快速和本地移动应用的框架，其原理与使用 [React.js](https://facebook.github.io/react) 构建 web 应用的原理相同。React(对我来说)提供了一种更好的构建 ui 的方法，值得一试，以便更好地理解本教程，并使您的前端生活更加轻松。如果你以前没有用过 React Native，SitePoint 有很多[教程](https://www.sitepoint.com/?s=react+native)，包括一个[快速提示让你开始](https://www.sitepoint.com/quick-tip-installing-react-native/)。

### Redux

Redux 是一个简单的状态容器(这是我迄今为止使用的最简单的容器),它使用状态到 UI 组件和从 UI 组件返回到 Redux 状态树的单向流动来帮助在 React.js(和 React Native)应用程序中保持状态。更多细节，请观看 Redux 创始人制作的[精彩视频教程](https://egghead.io/courses/getting-started-with-redux)。你会学到很多 Javascript 的函数式编程原理，它会让你从不同的角度看待 Javascript。

## App Backend

首先，该应用程序需要一个后端来发送聊天消息，并作为聊天消息广播到所有听众的点。您将使用 Express.js 构建这个后端，这是一个运行在 node.js 上的极简 web 框架。

使用以下命令安装 Express:

```
npm install express -g 
```

为名为 *ChatServer* 的项目创建一个文件夹，并在其中创建一个 *index.js* 文件。

在 *index.js* 中，需要必要的库并创建一个在端口`5000`上运行的 express app。

```
var express = require('express');
var Pusher = require('pusher');
var app = express();

app.set('port', (process.env.PORT || 5000)); 
```

通过向 Pusher 库传递`app_id`、`key`和`secret`值来创建您自己的 Pusher 库实例:

```
...

const pusher = new Pusher({
   appId: 'YOUR PUSHER APP_ID HERE',
   key:    'YOUR PUSHER KEY HERE',
   secret: 'YOUR PUSHER SECRET HERE'
}) 
```

创建一个接收聊天消息的端点，并将它们发送给 pusher，以便向聊天频道上的所有听众进行广播操作。您还需要为 set 端口上的连接设置一个监听器。

```
...

app.get('/chat/:chat', function(req,res){
  const chat_data = JSON.parse(req.params.chat);
  pusher.trigger('chat_channel', 'new-message', {chat:chat_data});
});

app.listen(app.get('port'), function() {
  console.log('Node app is running on port', app.get('port'));
}); 
```

## 移动应用程序

现在转到移动应用程序，向上移动一级并运行以下命令来创建一个新的 React 本地项目:

```
react-native init PusherChat
cd PusherChat 
```

该应用程序需要一些其他依赖项:

*   [Axios](https://github.com/mzabriskie/axios)–用于对后端的承诺和异步请求。
*   [异步存储](https://facebook.github.io/react-native/docs/asyncstorage.html)–用于本地存储聊天信息。
*   [时刻](http://momentjs.com/)——用于设置每条聊天消息发送的时间，并根据该时间排列消息。
*   [推杆-js](https://github.com/pusher-community/pusher-websocket-js-iso)–用于连接推杆。
*   [Redux](https://github.com/reactjs/redux)–状态容器
*   Redux-thunk-一个简单的中间件，有助于动作调度。
*   [React-redux](https://github.com/reactjs/react-redux)–为 Redux 反应绑定。

你应该早就已经安装了`pusher-js`，而且`AsyncStorage`是 React native 的一部分。通过运行以下命令安装其余部分:

```
npm install --save redux redux-thunk moment axios react-redux 
```

现在，您已经准备好构建聊天应用程序了，首先构建应用程序将执行的操作。

使用 Redux，您必须创建应用程序动作类型，因为当您将动作分派给 reducers(状态管理器)时，您发送了要执行的动作(动作类型)和执行动作所需的任何数据(有效负载)。对于这个应用程序的行动是发送聊天，获得所有聊天，并收到一条消息

在 *src/actions/index.js* 中创建一个新文件，并添加以下内容:

```
import axios from 'axios'
import { AsyncStorage } from 'react-native'
import moment from 'moment'
import Pusher from 'pusher-js/react-native';

export const SEND_CHAT = "SEND_CHAT";
export const GET_ALL_CHATS = "GET_ALL_CHATS";
export const RECEIVE_MESSAGE = " RECEIVE_MESSAGE"; 
```

您还需要 helper 函数，这些函数封装并在被调用时返回适当的`action_type`,这样，当您想要发送聊天时，您就可以调度`sendChat`函数及其有效负载:

```
const sendChat = (payload) => {
    return {
        type: SEND_CHAT,
        payload: payload
    };
};

const getChats = (payload) => {
    return {
        type: GET_ALL_CHATS,
        payload: payload
    };
};

const newMessage = (payload) => {
    return {
        type: RECEIVE_MESSAGE,
        payload: payload
    };
}; 
```

您还需要一个订阅 pusher 并监听新消息的功能。对于该函数接收的每个新消息，将其添加到设备`AsyncStorage`中，并分派一个新的消息动作，以便更新应用程序状态。

```
// function for adding messages to AsyncStorage
const addToStorage = (data) => {
    AsyncStorage.setItem(data.convo_id+data.sent_at, JSON.stringify(data), () => {})
}

// function that listens to pusher for new messages and dispatches a new
// message action
export function newMesage(dispatch){
    const socket = new Pusher("3c01f41582a45afcd689");
    const channel = socket.subscribe('chat_channel');
    channel.bind('new-message',
        (data) => {
            addToStorage(data.chat);
            dispatch(newMessage(data.chat))
        }
    );
} 
```

您还有发送聊天信息的功能。这个函数需要两个参数，发送者和消息。在理想的聊天应用中，你应该通过设备或登录来了解发送者，但对于这种输入，发送者:

```
export function apiSendChat(sender,message){
    const sent_at = moment().format();
    const chat = {sender:sender,message:message, sent_at:sent_at};
    return dispatch => {
        return  axios.get(`http://localhost:5000/chat/${JSON.stringify(chat)}`).then(response =>{
        }).catch(err =>{
            console.log("error", err);
        });
    };
}; 
```

最后是一个从设备`AysncStorage`获取所有聊天消息的功能。当第一次打开聊天应用程序、从设备存储器加载所有消息并开始收听新消息时，这是需要的。

```
export function apiGetChats(){
    //get from device async storage and not api

    return dispatch => {
        dispatch(isFetching());
        return AsyncStorage.getAllKeys((err, keys) => {
            AsyncStorage.multiGet(keys, (err, stores) => {
                let chats = [];
                stores.map((result, i, store) => {
                    // get at each store's key/value so you can work with it
                    chats.push(JSON.parse(store[i][1]))
                });
                dispatch(getChats(chats))
            });
        });
    };
} 
```

下一步是创建减速器。理解 reducer 做什么的最简单的方法是把它想象成一个银行出纳员，根据你给他们的任何单据(动作类型)对你的银行账户执行动作。如果你给他们一张取款单(操作类型)，上面有设定的金额(有效负载)要提取(操作)，他们会从你的银行账户(状态)中删除该金额(有效负载)。你也可以用存款单(动作类型)把钱(动作+有效负载)加到你的账户(状态)。

总之，reducer 是一个基于调度的动作影响应用程序状态的函数，动作包含其类型和有效负载。基于动作类型，减速器影响应用程序的状态。

创建一个名为 *src/reducers/index.js* 的新文件，并添加以下内容:

```
import { combineReducers } from 'redux';
import { SEND_CHAT, GET_ALL_CHATS, RECEIVE_MESSAGE} from './../actions'

// THE REDUCER

const Chats = (state = {chats:[]}, actions) => {
    switch(actions.type){
       case GET_ALL_CHATS:
            return Object.assign({}, state, {
                process_status:"completed",
                chats:state.chats.concat(actions.payload)
            });

        case SEND_CHAT:
        case NEW_MESSAGE:
            return Object.assign({}, state, {
                process_status:"completed",
                chats:[...state.chats,actions.payload]
            });

        default:
            return state;
    }
};

const rootReducer = combineReducers({
    Chats
})

export default rootReducer; 
```

接下来创建商店。继续银行出纳的类比，商店就像存储所有银行账户(状态)的仓库。现在你有一个状态，聊天，并且在你需要的时候可以访问它。

创建一个新的*src/store/configure store . js*文件，并添加以下内容:

```
import { createStore, applyMiddleware } from 'redux'
import thunkMiddleware from 'redux-thunk'
import createLogger from 'redux-logger'
import rootReducer from '../reducers'

const createStoreWithMiddleware = applyMiddleware(
    thunkMiddleware,
    createLogger()
)(createStore)

export default function configureStore(initialState) {
    const store = createStoreWithMiddleware(rootReducer, initialState)
    return store
} 
```

现在让我们创建主聊天组件，它呈现所有聊天消息，并允许用户通过输入他们的消息来发送聊天消息。这个组件使用 React Native `ListView`。

创建一个新的*src/screens/conversation screen . js*文件，并添加以下内容:

```
import React, { Component, View, Text, StyleSheet, Image, ListView, TextInput, Dimensions} from 'react-native';
import Button from './../components/button/button';
import { Actions } from 'react-native-router-flux';
import KeyboardSpacer from 'react-native-keyboard-spacer';
import { connect } from 'react-redux';
import moment from 'moment';
import { apiSendChat, newMesage } from './../actions/';

const { width, height } = Dimensions.get('window');

const styles = StyleSheet.create({
    container: {
        flex: 1
    },
    main_text: {
        fontSize: 16,
        textAlign: "center",
        alignSelf: "center",
        color: "#42C0FB",
        marginLeft: 5
    },
    row: {
        flexDirection: "row",
        borderBottomWidth: 1,
        borderBottomColor: "#42C0FB",
        marginBottom: 10,
        padding:5
    },
    back_img: {
        marginTop: 8,
        marginLeft: 8,
        height: 20,
        width: 20
    },
    innerRow: {
        flexDirection: "row",
        justifyContent: "space-between"
    },
    back_btn: {},
    dp: {
        height: 35,
        width: 35,
        borderRadius: 17.5,
        marginLeft:5,
        marginRight:5
    },
    messageBlock: {
        flexDirection: "column",
        borderWidth: 1,
        borderColor: "#42C0FB",
        padding: 5,
        marginLeft: 5,
        marginRight: 5,
        justifyContent: "center",
        alignSelf: "flex-start",
        borderRadius: 6,
        marginBottom: 5
    },
    messageBlockRight: {
        flexDirection: "column",
        backgroundColor: "#fff",
        padding: 5,
        marginLeft: 5,
        marginRight: 5,
        justifyContent: "flex-end",
        alignSelf: "flex-end",
        borderRadius: 6,
        marginBottom: 5
    },
    text: {
        color: "#5c5c5c",
        alignSelf: "flex-start"
    },
    time: {
        alignSelf: "flex-start",
        color: "#5c5c5c",
        marginTop:5
    },
    timeRight: {
        alignSelf: "flex-end",
        color: "#42C0FB",
        marginTop:5
    },
    textRight: {
        color: "#42C0FB",
        alignSelf: "flex-end",
        textAlign: "right"
    },
    input:{
        borderTopColor:"#e5e5e5",
        borderTopWidth:1,
        padding:10,
        flexDirection:"row",
        justifyContent:"space-between"
    },
    textInput:{
        height:30,
        width:(width * 0.85),
        color:"#e8e8e8",
    },
    msgAction:{
        height:29,
        width:29,
        marginTop:13
    }
});
const username = 'DUMMY_USER';

function mapStateToProps(state) {
    return {
        Chats: state.Chats,
        dispatch: state.dispatch
    }
}

class ConversationScreen extends Component {

    constructor(props) {
        super(props);
        const ds = new ListView.DataSource({rowHasChanged: (r1, r2) => r1 != r2});
        this.state = {
            conversation: ds,
            text:"",
            username
        }
    }

    componentDidMount(){
        const {dispatch, Chats} = this.props;
        const chats = Chats;
        chats.sort((a,b)=>{
                return moment(a.sent_at).valueOf() - moment(b.sent_at).valueOf();
            });
            this.setState({
                conversation: this.state.conversation.cloneWithRows(chats)
            })
    }
    componentWillReceiveProps(nextProps) {
        const {dispatch, Chats} = this.props;
        const chats = Chats;
        chats.sort((a,b)=>{
                return moment(a.sent_at).valueOf() - moment(b.sent_at).valueOf();
            });
            this.setState({
                conversation: this.state.conversation.cloneWithRows(chats)
            })

    }

    renderSenderUserBlock(data){
        return (
            <View style={styles.messageBlockRight}>
                <Text style={styles.textRight}>
                    {data.message}
                </Text>
                <Text style={styles.timeRight}>{moment(data.time).calendar()}</Text>
            </View>
        )
    }
    renderReceiverUserBlock(data){
        return (
            <View style={styles.messageBlock}>
                <Text style={styles.text}>
                    {data.message}
                </Text>
                <Text style={styles.time}>{moment(data.time).calendar()}</Text>
            </View>
        )
    }
    renderRow = (rowData) => {
        return (
            <View>
                {rowData.sender == username ? this.renderSenderUserBlock(rowData) : this.renderReceiverUserBlock(rowData)}
            </View>
        )
    }

    sendMessage = () => {

        const message = this.state.text;
        const username =  this.state.username;

        const {dispatch, Chats} = this.props;
        dispatch(apiSendChat(username,message))

    }

    render() {
        return (
            <View style={styles.container}>
                <View style={styles.row}>
                    <Button
                        style={styles.back_btn}
                        onPress={() => Actions.pop()}>
                        <Image source={require('./../assets/back_chevron.png')} style={styles.back_img}/>
                    </Button>
                    <View style={styles.innerRow}>
                        <Image source={{uri:"https://avatars3.githubusercontent.com/u/11190968?v=3&s=460"}} style={styles.dp}/>
                        <Text style={styles.main_text}>GROUP CHAT</Text>
                    </View>
                </View>

                <ListView
                    renderRow={this.renderRow}
                    dataSource={this.state.conversation}/>

                <View style={styles.input}>

                    <TextInput
                        style={styles.textInput}
                        onChangeText={(text) => this.setState({username:text})}
                        placeholder="Send has?"/>
                    <TextInput
                        style={styles.textInput}
                        onChangeText={(text) => this.setState({text:text})}
                        placeholder="Type a message"/>
                    <Button
                        onPress={this.sendMessage}>
                        <Image source={require('./../assets/phone.png')} style={styles.msgAction}/>
                    </Button>
                </View>
                <KeyboardSpacer/>
            </View>
        )
    }
}

export default connect(mapStateToProps)(ConversationScreen) 
```

React Native 为您提供了一个生命周期函数，`componentWillReceiveProps(nextProps)`每当组件将要接收新的属性(props)时就会被调用，在这个函数中，您可以用聊天消息更新组件的状态。

`renderSenderUserBlock`功能呈现用户发送的聊天消息，而`renderReceiverUserBlock`功能呈现用户接收的聊天消息。

`sendMessage`函数从用户想要发送的状态、接收者的用户名中获取消息，并调度一个动作来发送聊天消息。

传递给`Listview`组件的`renderRow`函数包含属性并呈现`Listview`中每一行的数据。

您需要将状态传递给应用程序组件，并将使用 React-redux 库来完成这项工作。这允许您将组件连接到 redux 并访问应用程序状态。

React-Redux 为您提供了两样东西:

1.  “Provider”组件，允许您将存储作为属性传递给它。
2.  允许组件连接到 redux 的“连接”功能。它将组件连接到的 redux 状态作为组件的属性进行传递。

最后，创建 *app.js* 将所有内容联系在一起:

```
import React, { Component, StyleSheet, Dimensions} from 'react-native';
import { Provider } from 'react-redux'
import configureStore from './store/configureStore'

const store = configureStore();

import ConversationScreen from './screens/conversation-screen';

const styles = StyleSheet.create({
    container: {
        flex: 1,
        backgroundColor: "#fff",
    },
    tabBarStyle: {
        flex: 1,
        flexDirection: "row",
        backgroundColor: "#95a5a6",
        padding: 0,
        height: 45
    },
    sceneStyle: {
        flex: 1,
        backgroundColor: "#fff",
        flexDirection: "column",
        paddingTop:20
    }
})

export default class PusherChatApp extends Component {
    render() {
        return (
            <Provider store={store}>
            <ConversationScreen />
                </Provider>
        )

    }
} 
```

并引用 *index.android.js* 和 *index.ios.js* 中的 *app.js* ，替换当前任何内容:

```
import React, {
   AppRegistry,
   Component,
   StyleSheet,
   Text,
   View
 } from 'react-native';

 import PusherChatApp from './src/app'

 AppRegistry.registerComponent('PusherChat', () => PusherChatApp); 
```

## 跟我说话

就是这样，一个可扩展和高性能的实时应用程序，您可以根据需要轻松添加和增强。如果您有任何问题或意见，请在下面告诉我。

## 分享这篇文章