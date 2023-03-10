# Redux vs MobX:哪个最适合你的项目？

> 原文：<https://www.sitepoint.com/redux-vs-mobx-which-is-best/>

对于很多 JavaScript 开发人员来说，对 Redux 最大的抱怨是实现特性所需的大量样板代码。一个更好的选择是 MobX，它提供了类似的功能，但是需要编写更少的代码。

对于 MobX 新手来说，快速看一下 MobX 的创作者写的这篇[简介](https://medium.com/@mweststrate/becoming-fully-reactive-an-in-depth-explanation-of-mobservable-55995262a254)。你也可以通过这个[教程](https://www.sitepoint.com/manage-javascript-application-state-mobx/)来获得一些实践经验。

本文的目标是帮助 JavaScript 开发人员决定这两种状态管理解决方案中哪一种最适合他们的项目。我已经将这个 [CRUD Redux 项目](https://www.sitepoint.com/crud-app-node-react-feathersjs/)迁移到了 [MobX](https://www.github.com/brandiqa/mobx-crud-example) 上，作为本文中的一个例子。我将首先讨论使用 MobX 的利与弊，然后我将展示两个版本的实际代码样本，以展示它们之间的区别。

本文中提到的项目代码可以在 GitHub 上找到:

*   [Redux CRUD 示例](https://github.com/sitepoint-editors/redux-crud-example)
*   [MobX CRUD 示例](https://github.com/sitepoint-editors/mobx-crud-example)

如果你喜欢这篇文章，你可能也想注册 SitePoint Premium，看看我们关于使用 React 和 Redux 处理表单的课程[。](https://www.sitepoint.com/premium/courses/forms-with-react-and-redux-2935)

![Pieces of data shaped like birds, migrating Redux to MobX](img/a176e43af8b03bea9da4c75d009ccd1d.png)

## Redux 和 MobX 有什么共同点？

首先，让我们看看他们有什么共同点。他们:

*   是开源库
*   提供客户端状态管理
*   通过 [redux-devtools-extension](https://github.com/zalmoxisus/redux-devtools-extension) 支持时间旅行调试
*   不局限于特定的框架
*   对 React/React 本机框架有广泛的支持。

## 使用 MobX 的 4 个理由

现在让我们看看 Redux 和 MobX 的主要区别。

### 1.易于学习和使用

对于初学者来说，只需 30 分钟就可以学会如何使用 MobX。一旦学会了基础，就这样了。你不需要学习任何新的东西。有了 Redux，基础也变得简单了。然而，一旦您开始构建更复杂的应用程序，您将不得不处理:

*   用 [redux-thunk](https://github.com/gaearon/redux-thunk) 处理异步动作
*   用 [redux-saga](https://github.com/redux-saga/redux-saga) 简化你的代码
*   定义选择器来处理计算值，等等。

有了 MobX，所有这些情况都被“神奇地”处理了。您不需要额外的库来处理这种情况。

### 2.需要编写的代码更少

要在 Redux 中实现一个特性，您至少需要更新四个工件。这包括为 reducers、动作、容器和组件编写代码。如果你在做一个小项目，这尤其令人讨厌。MobX 只要求您更新至少两个工件(即存储和视图组件)。

### 3.完全支持面向对象编程

如果您喜欢编写面向对象的代码，您会很高兴知道您可以使用 OOP 来实现 MobX 的状态管理逻辑。通过使用像`@observable`和`@observer`这样的装饰器，您可以很容易地使您的普通 JavaScript 组件和存储具有反应性。如果您喜欢函数式编程，没问题——它也是受支持的。另一方面，Redux 更倾向于函数式编程原则。然而，如果你想要一个基于类的方法，你可以使用 [redux-connect-decorator](https://github.com/evturn/redux-connect-decorator) 库。

### 4.处理嵌套数据很容易

在大多数 JavaScript 应用程序中，您会发现自己在处理关系数据或嵌套数据。为了能够在 Redux 商店中使用它，您必须先将规范化。接下来，您必须[编写更多的代码](http://redux.js.org/docs/recipes/reducers/UpdatingNormalizedData.html)来管理规范化数据中引用的跟踪。

在 MobX 中，[建议](https://mobx.js.org/best/store.html)以非规范化的形式存储数据。MobX 可以为您跟踪关系，并自动重新呈现更改。通过使用域对象存储您的数据，您可以直接引用在其他存储中定义的其他域对象。此外，您可以使用 [(@)计算装饰符](https://mobx.js.org/refguide/computed-decorator.html)和[可观察变量](https://mobx.js.org/refguide/modifiers.html)来轻松解决复杂的数据挑战。

## 不使用 MobX 的 3 个理由

### 1.太多的自由

Redux 是一个框架，它为如何编写状态代码提供了严格的指导。这意味着您可以轻松地编写测试和开发可维护的代码。MobX 是一个库，没有关于如何实现它的规则。这样做的危险在于，走捷径和应用快速修复非常容易导致不可维护的代码。

### 2.难以调试

MobX 的内部代码“神奇地”处理大量逻辑，使您的应用程序具有反应能力。在存储和组件之间有一个不可见的数据传递区域，这使得出现问题时很难调试。如果你直接在组件中改变状态，而不使用`@actions`，你将很难确定 bug 的来源。

### 3.MobX 可能有更好的替代品

在软件开发中，新的趋势一直在出现。在短短的几年内，当前的软件技术会很快失去势头。目前，有几个解决方案与 Redux 和 Mobx 竞争。几个例子就是[接力/阿波罗& GraphQL](https://www.sitepoint.com/react-data-fetching-with-relay/) 、 [Alt.js](http://alt.js.org/) 和[连体衣](https://jumpsuit.js.org/)。这些技术中的任何一种都有可能成为最受欢迎的技术。如果你真的想知道哪一种最适合你，你必须全部尝试。

## 代码比较:Redux vs MobX

理论够了，我们来看代码。首先，我们比较每个版本如何引导。

### 拔靴带

**Redux 版本:**
在 Redux 中，我们首先定义我们的商店，然后通过`Provider`传递给`App`。我们还需要定义`redux-thunk`和`redux-promise-middleware`来处理异步函数。`redux-devtools-extension`允许我们在时间旅行模式下调试我们的商店。

```
// src/store.js
import { applyMiddleware, createStore } from "redux";
import thunk from "redux-thunk";
import promise from "redux-promise-middleware";
import { composeWithDevTools } from 'redux-devtools-extension';
import rootReducer from "./reducers";

const middleware = composeWithDevTools(applyMiddleware(promise(), thunk));

export default createStore(rootReducer, middleware);

-------------------------------------------------------------------------------

// src/index.js
…
ReactDOM.render(
  <BrowserRouter>  <Provider store={store}>  <App />  </Provider>  </BrowserRouter>,
  document.getElementById('root')
); 
```

**MobX 版本:**
在 MobX 中，我们需要设置多个店铺。在本例中，我只使用了一个商店，我将它放在了名为`allStores`的集合中。然后用一个`Provider`将商店集合传递给`App`。

如前所述，MobX 不需要外部库来处理异步操作，因此代码更少。然而，我们确实需要`mobx-remotedev`来连接到`redux-devtools-extension`调试工具。

```
// src/stores/index.js
import remotedev from 'mobx-remotedev';
import Store from './store';

const contactConfig = {
  name:'Contact Store',
  global: true,
  onlyActions:true,
  filters: {
    whitelist: /fetch|update|create|Event|entity|entities|handleErrors/
  }
};

const contactStore = new Store('api/contacts');

const allStores = {
  contactStore: remotedev(contactStore, contactConfig)
};

export default allStores;

-------------------------------------------------------------------------------

// src/index.js
…
ReactDOM.render(
  <BrowserRouter>  <Provider stores={allStores}>  <App />  </Provider>  </BrowserRouter>,
  document.getElementById('root')
); 
```

这里的代码量在两个版本中大致相同。不过 MobX 的 import 语句更少。

### 道具注射

**Redux 版本:**
在 Redux 中，使用 react-redux 的`connect()`函数将状态和动作传递给道具。

```
// src/pages/contact-form-page.js
…
  // accessing props
  <ContactForm
    contact={this.props.contact}
    loading={this.props.loading}
    onSubmit={this.submit}
  />
…

// function for injecting state into props
function mapStateToProps(state) {
  return {
    contact: state.contactStore.contact,
    errors: state.contactStore.errors
  }
}

// injecting both state and actions into props
export default connect(mapStateToProps, { newContact,
  saveContact,
  fetchContact,
  updateContact
})(ContactFormPage); 
```

**MobX 版本:**
在 MobX 中，我们简单的注入`stores`集合。我们在容器或组件类的顶部使用`@inject`来做这件事。这使得`stores`在`props`中可用，这又允许我们访问特定的商店并将其传递给子组件。状态和动作都是通过`store`对象中的属性来访问的，因此不需要像 Redux 中那样分别传递它们。

```
// src/pages/contact-form-page.js

…
@inject("stores") @observer // injecting store into props
class ContactFormPage extends Component {
…
  // accessing store via props
  const { contactStore:store } = this.props.stores;
  return (
      <ContactForm
        store={store}
        form={this.form}
        contact={store.entity}
      />
  )
…
} 
```

MobX 版本似乎更容易阅读。但是，我们可以使用 [redux-connect-decorators](https://github.com/evturn/redux-connect-decorator) 来简化 redux 代码。在这种情况下，不会有明显的赢家。

### 定义存储、操作和减少器

为了保持本文的简洁，我将只向您展示一个动作的代码示例。

**Redux 版本:**
在 Redux 中，我们需要定义动作和还原器。

```
// src/actions/contact-actions.js
…
export function fetchContacts(){
  return dispatch => {
    dispatch({
      type: 'FETCH_CONTACTS',
      payload: client.get(url)
    })
  }
}
…

// src/reducers/contact-reducer
…
switch (action.type) {
    case 'FETCH_CONTACTS_FULFILLED': {
      return {
        ...state,
        contacts: action.payload.data.data || action.payload.data,
        loading: false,
        errors: {}
      }
    }

    case 'FETCH_CONTACTS_PENDING': {
      return {
        ...state,
        loading: true,
        errors: {}
      }
    }

    case 'FETCH_CONTACTS_REJECTED': {
      return {
        ...state,
        loading: false,
        errors: { global: action.payload.message }
      }
    }
}
… 
```

**MobX 版本:**
在 MobX 中，动作和减速器的逻辑是在一个类中完成的。我定义了一个异步动作，在收到`response`后调用另一个动作`entities fetched`。

由于 MobX 使用 OOP 风格，这里定义的`Store`类已经被重构，允许使用类构造函数轻松创建多个存储。因此，这里演示的代码是与特定域存储无关的基本代码。

```
// src/stores/store.js
…
@action
fetchAll = async() => {
  this.loading = true;
  this.errors = {};
  try {
    const response = await this.service.find({})
    runInAction('entities fetched', () => {
      this.entities = response.data;
      this.loading = false;
    });
  } catch(err) {
      this.handleErrors(err);
  }
}
… 
```

信不信由你，两个版本中定义的逻辑执行相同的任务，它们是:

*   更新用户界面加载状态
*   异步获取数据
*   捕捉异常并更新状态。

在 Redux 中，我们使用了 **33 行代码**。在 MobX 中，我们使用了大约 **14 行代码**来实现同样的结果！MobX 版本的一个主要好处是，您可以在几乎所有的域存储类中重用基本代码，只需很少或不需要修改。这意味着您可以更快地构建应用程序。

### 其他差异

为了在 Redux 中创建表单，我使用了 [redux-form](https://www.npmjs.com/package/redux-form) 。在 MobX 中，我用过 [mobx-react-form](https://www.npmjs.com/package/mobx-react-form) 。这两个库都很成熟，可以帮助您轻松处理表单逻辑。就个人而言，我更喜欢`mobx-react-form`，因为它允许你通过插件来验证字段。使用`redux-form`，您可以编写自己的验证代码，也可以导入一个验证包来为您处理验证。

MobX 的一个小缺点是不能直接访问可观察对象中的某些函数，因为它们不是真正的普通 JavaScript 对象。幸运的是，他们提供了函数`toJS()`,可以用来将可观察对象转换成普通的 JavaScript 对象。

## 结论

很明显，你可以看到 MobX 的代码库要精简得多。使用 OOP 风格和良好的开发实践，您可以快速构建应用程序。主要的缺点是很容易写出糟糕的、不可维护的代码。

另一方面，Redux 更受欢迎，非常适合构建大型复杂的项目。这是一个严格的框架，有安全保障，确保每个开发人员编写的代码易于测试和维护。然而，它不太适合小项目。

尽管 MobX 有缺点，但如果遵循良好的实践，您仍然可以构建大型项目。用阿尔伯特·爱因斯坦的话来说，“让一切尽可能简单，但不要更简单”。

我希望我已经提供了足够的信息来清楚地说明是迁移到 MobX 还是继续使用 Redux。最终，决定取决于你正在做的项目的类型，以及你可用的资源。

*这篇文章由 [Dominic Myers](https://github.com/annoyingmouse) 和 [Vildan Softic](https://www.sitepoint.com/author/vildansoftic/) 进行了同行评审。感谢 SitePoint 的所有同行评审员使 SitePoint 的内容尽可能做到最好！*

* * *

***如果你想升级你的 Redux 游戏，注册 SitePoint Premium，参加我们的课程 [Redux 设计问题和测试](https://www.sitepoint.com/premium/courses/redux-design-issues-and-testing-2962)。在本课程中，您将构建一个 Redux 应用程序，通过 websocket 连接接收按主题组织的推文。为了让你初步了解即将推出的产品，请查看下面的免费课程。***

## 分享这篇文章