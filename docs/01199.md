# 是否重复:React 应用程序中构造状态的艺术

> 原文：<https://www.sitepoint.com/redux-not-art-structuring-state-react-apps/>

**我发现大多数 Redux 开发者的一个共同趋势是对`setState()`的憎恨。我们中的许多人(是的，我以前多次陷入这个陷阱)一看到`setState()`就退缩，并试图将所有数据保存在我们的 Redux 存储中。但是，随着应用程序复杂性的增加，这带来了一些挑战。**

在这篇文章中，我将带你通过各种策略来模拟你的状态，并深入探讨每一种策略何时可以使用。

## 入门指南

Redux 的工作原理是成为应用程序状态的唯一真实来源。新一季《权力的游戏》正在播出，我相信每个人都很想知道这将会如何展开。让我们构建一个有趣的《权力的游戏》粉丝列表页面，来详细理解这些概念。

*注意:我将使用`yarn`来运行这个应用程序。如果没有设置[纱线](https://www.sitepoint.com/yarn-vs-npm/)，用`npm`替换纱线。*

在我们开始之前，从[repo](https://github.com/skmvasu/redux-state-vs-local-state-sp)下载基本框架并运行:

```
yarn install
yarn run start 
```

您应该会看到一个基本的列表页面，其中列出了一些您最喜欢的角色。

*注意:我们将使用[鸭子模式](https://github.com/erikras/ducks-modular-redux)来编写我们的应用程序。它减少了不必要的模块导入，减少了大量的样板文件。*

## Redux 简介

本文的范围是帮助您构建 Redux 应用程序。它假设你对图书馆有基本的了解。我将简要概述 Redux 概念，这将帮助您更好地理解本文的其余部分。如果你熟悉这些是如何工作的，可以跳过这一部分。

所有 Redux 应用程序都利用了四个重要的构造:动作、reducers、存储和容器。

### 行动

一个**动作**是更新状态的意图。它可以由网络呼叫或用户点击按钮来触发。动作有两个部分:

1.  **动作类型**。代表操作的唯一标识符。
2.  **有效载荷**。与操作相关联的任何元数据。例如，如果我们发出一个获取电影列表的网络请求，来自服务器的响应就是有效负载。

对于这个例子，我们将使用一个名为`redux-actions`的库来创建动作。

### 还原剂

一个**缩减器**是一个监听一个动作并返回一个新的状态表示的函数。

### 商店

一个应用程序可以被分成许多 reducers，代表页面的不同部分。一个**商店**将所有这些集合在一起，并保持应用程序状态不变。

### 容器

**容器**将你的应用状态和动作与组件连接起来，将它们作为道具传递下去。

为了深入理解这是如何工作的，我建议你先看看 Dan Abramov 的[免费介绍系列。](https://egghead.io/courses/getting-started-with-redux)

## 拆分应用程序数据和用户界面状态

列表页面很好，但是名字没有给刚接触 GoT 领域的人任何上下文。让我们扩展组件来呈现角色描述:

```
//GoTCharacter.js

export const CharacterRow = ({character}) => (
  <div className="row">  <div className="name">{character.name}</div>  <div className="description">{character.description}</div>  </div>
); 
```

虽然这解决了问题，但我们的设计者觉得页面看起来很笨拙，最好将这些信息折叠起来，直到用户需要它。我们可以采取三种不同的方法来解决这个问题。

### `setState`方法

在 React 中实现这一点的最简单方法是使用`setState()`将数据存储在组件本身中:

```
//GoTCharacter.js

export class StatefulCharacterRow extends Component {
  constructor() {
    super();
    this.state = {
      show_description: false
    }
  }

  render() {
    const {character} = this.props;
    return (<div className="row">  <div className="name">{character.name}</div>  <a href="#" onClick={() => this.setState({
        show_description: !this.state.show_description})} >  {this.state.show_description ? 'collapse' : 'expand'}  </a>  {this.state.show_description &&
        <div className="description">{character.description}</div>}  </div>);
  }
}; 
```

### Redux 方法

只要我们处理的状态只是组件的局部状态，使用`setState()`就可以了。例如，如果我们想要设置一个“全部展开”功能，那么仅仅使用 React 很难做到这一点。

让我们看看如何将它转移到 Redux:

```
// FlickDuck.js

// …
export const toggleCharacterDescription = createAction(
  FlixActions.TOGGLE_CHARACTER_DESCRIPTION, (character) => ({character})
);

export default (current_state, action) => {
  const state = current_state || default_state;

  switch (action.type) {
    case FlixActions.TOGGLE_CHARACTER_DESCRIPTION:
      return {...state, characters: state.characters.map(char => {
        if (char.id === action.payload.character.id) {
          return {...char,show_description: !char.show_description};
        }

        return char;
      })}
    default:
      return state
  }
} 
```

```
// GoTCharactersContainer.js

import { connect } from 'react-redux';
import GoTCharacters from './GoTCharacters';
import {toggleCharacterDescription} from './FlickDuck';

const mapStateToProps = (state) => ({
  ...state.flick
});

const mapDispatchToProps = (dispatch) => ({
  toggleCharacterDescription : (data) => dispatch(toggleCharacterDescription(data))
});

export default connect(mapStateToProps, mapDispatchToProps)(GoTCharacters); 
```

```
// GoTCharacters.js

const GoTCharacters = ({characters,toggleCharacterDescription}) => {
  return (
    <div className="characters-list">  {characters.map(char => (
        <CharacterRow
          character={char}
          toggleCharacterDescription={toggleCharacterDescription}
          key={char.id}/>
      ))}  </div>
  );
};

export const CharacterRow = ({character, toggleCharacterDescription}) => (
  <div className="row">  <div className="name">{character.name}</div>  <a href="#" onClick={toggleCharacterDescription.bind(null, character)} >  {character.show_description ? 'collapse' : 'expand'}  </a>  {character.show_description &&
      <div className="description">{character.description}</div>}  </div>
); 
```

我们将描述字段的状态存储在角色对象中。我们现在的状态会是这样的:

```
state = {
  characters: [{
    id: 1,
    name: "Eddard Ned Stark",
    house: "stark",
    description: "Lord of Winterfell - Warden of the North - Hand of the King - Married to Catelyn (Tully) Stark",
    imageSuffix: "eddard-stark",
    wikiSuffix: "Eddard_Stark",
    show_description: true
  },
  {
    id: 2,
    name: "Benjen Stark",
    house: "stark",
    description: "Brother of Eddard Stark - First ranger of the Night's Watch",
    imageSuffix: "benjen-stark",
    wikiSuffix: "Benjen_Stark",
    show_description: false
  }]
} 
```

这是许多开发人员在开始使用 Redux 时遵循的一般模式。这种方法没有任何问题，对于较小的应用程序非常有效。

到目前为止，我们已经处理了《GoT》第一章中的角色，宇宙将会变得更大。当它出现时，我们的应用程序将变得缓慢。想象一下，遍历 1000 个字符来更新一行。

让我们看看如何针对更大的数据集进行扩展:

```
// FlickDuck.js

// …
case FlixActions.TOGGLE_CHARACTER_DESCRIPTION:
  const {character} = action.payload;
  return {
    ...state,
    character_show_description: {
      ...state.character_show_description,
      [character.id]: !state.character_show_description[character.id]
    }
  }
// … 
```

而在`GoTCharacters.js`:

```
export const CharacterRow = ({character, character_show_description, toggleCharacterDescription}) => (
  <div className="row">  <div className="name">{character.name}</div>  <a href="#" onClick={toggleCharacterDescription.bind(null, character)} >  {character_show_description[character.id] ? 'collapse' : 'expand'}  </a>  {character_show_description[character.id] &&
      <div className="description">{character.description}</div>}  </div>
); 
```

当用户点击**展开**链接时，我们用当前角色 id 更新`character_show_description`。现在的状态是这样的:

```
state = {
  characters: [...],
  character_show_description: {
    1: true,
    2: false
  }
} 
```

现在，我们可以更新 UI 状态，而无需遍历所有字符。

## 在 Redux 中管理表单状态

管理窗体状态是一件棘手的事情。在一个典型的应用程序中，我们将在提交过程中序列化表单数据一次，如果有效，就提交它。否则，我们将显示一条错误消息。很简单，对吧？

但是，在现实世界中，我们会有一些涉及表单的复杂交互。当表单上出现验证错误时，我们可能需要在页面顶部显示错误。我们甚至可能需要禁用页面其他部分的一些元素，这取决于 UX。这通常是通过从你父母的父母的父母那里传递随机回调来实现的，甚至是通过每次验证来操纵 dom。

让我们看看如何用 Redux 实现这一点:

```
// FlickDuck.js
// ============

const FlixActions = km({
  FETCH_CHARACTERS: null,
  TOGGLE_CHARACTER_DESCRIPTION: null,
  TOGGLE_CHARACTER_EDIT: null,
  SYNC_CHARACTER_EDIT_DATA: null,
  SAVE_CHARACTER_EDIT: null
});

const default_state = {
  characters: characters,
  character_show_description: {},
  show_character_edit: {},
  character_edit_form_data: {}
};

export const toggleEdit = createAction(
  FlixActions.TOGGLE_CHARACTER_EDIT, (character) => ({character})
);

export const syncCharacterEditData = createAction(
  FlixActions.SYNC_CHARACTER_EDIT_DATA, (character, form_data) => ({character, form_data})
);
export const editCharacterDetails = createAction(
  FlixActions.SAVE_CHARACTER_EDIT, (character) => ({character})
);

export default (current_state, action) => {
  // …

  switch (action.type) {
    // …

    case FlixActions.TOGGLE_CHARACTER_EDIT:
      character =  action.payload.character;
      const show_character_edit = !state.show_character_edit[character.id];
      return {
        ...state,
        show_character_edit: {
          ...state.show_character_edit,
          [character.id]: show_character_edit
        }, character_edit_form_data : {
          ...state.character_edit_form_data,
          [character.id]: show_character_edit ? {...character} : {}
        }
      }

    case FlixActions.SYNC_CHARACTER_EDIT_DATA:
      character =  action.payload.character;
      const {form_data} = action.payload;

      return {
        ...state,
        character_edit_form_data: {
          ...state.character_edit_form_data,
          [character.id]: {...form_data}
        }
      }

    case FlixActions.SAVE_CHARACTER_EDIT:
      character =  action.payload.character;
      const edit_form_data = state.character_edit_form_data[character.id];
      const characters = state.characters.map(char => {
        if (char.id === character.id) return {...char, name:edit_form_data.name, description: edit_form_data.description}

        return char;
      });

    return {
      ...state,
      characters,
      show_character_edit: {
        ...state.show_character_edit,
        [character.id]: false
      }
    }

    // …
  }
} 
```

```
// GotCharacters.js

export const CharacterRow = ({character, character_show_description, character_edit_form_data, show_character_edit, toggleCharacterDescription, toggleEdit, syncCharacterEditData, editCharacterDetails}) => {
  const toggleEditPartial = toggleEdit.bind(null, character);
  return (<div className="row">  <div className="name">{character.name}</div>  <a href="#" onClick={toggleCharacterDescription.bind(null, character)} >  {character_show_description[character.id] ? 'collapse' : 'expand'}  </a>  {!character_show_description[character.id] && <a href="#" onClick={toggleEditPartial} > edit </a>}  {character_show_description[character.id] &&
      <div className="description">{character.description}</div>}  {show_character_edit[character.id] &&
      <EditCharacterDetails character={character}
        cancelEdit={toggleEditPartial}
        syncCharacterEditData={syncCharacterEditData}
        editCharacterDetails={editCharacterDetails}
        edit_data={character_edit_form_data[character.id]}/>
    }  </div>);
}

export const EditCharacterDetails = ({character, edit_data, syncCharacterEditData, editCharacterDetails, cancelEdit}) =>  {
  const syncFormData = (key, e) => {
    const {value} = e.currentTarget;
    syncCharacterEditData(character, {
      ...edit_data,
      [key]: value
    });
  };

  const saveForm = (e) => {
    e.preventDefault();
    editCharacterDetails(character);
  };

  return (
    <form onSubmit={saveForm}>  <label>Name: </label>  <input name='name' value={edit_data.name} onChange={syncFormData.bind(null, 'name')}/>  <label>Description:</label>  <textarea name='description' value={edit_data.description} onChange={syncFormData.bind(null, 'description')}/>  <button type="reset" onClick={cancelEdit}> Cancel </button>  <button type="submit"> Submit </button>  </form>
  );
}; 
```

让我们扩展它来处理验证:

```
// FlickDuck.js
// ============

export const editCharacterDetails = createAction(
  FlixActions.VALIDATE_AND_SAVE_CHARACTER_EDIT, (dispatch, character, edit_form_data) => {
    const errors = validateCharacterForm(edit_form_data);
    if (Object.keys(errors).length) {
      return dispatch(showErrorMessage(character, errors));
    }

    return dispatch(saveCharacterEdit(character));
  }
);

export const showErrorMessage = createAction(
  FlixActions.VALIDATE_CHARACTER_EDIT, (character, errors) => ({character, errors, hasError: true})
);

export const saveCharacterEdit = createAction(
  FlixActions.SAVE_CHARACTER_EDIT, (character) => ({character})
);

switch (action.type) {
  // …

  case FlixActions.VALIDATE_CHARACTER_EDIT:
    character =  action.payload.character;
    const {errors, hasError} = action.payload;

    return {
      ...state,
      character_edit_form_errors: {
        ...state.character_edit_form_errors,
        [character.id]: {errors, hasError}
      }
    }
  // …
} 
```

这不是和我们上一节看到的例子很像吗？它在形式上有什么特别之处？

在开始之前，理解 Redux 内部是如何工作的很重要。当您的状态改变时，您不需要更新树中的任何一个点。相反，整个状态树被一个新的状态树取代。该树被传递给 React 组件，React 协调所有组件，以查看 DOM 是否需要更新。

表单状态比较特殊，因为状态树变化非常快。根据用户的打字速度，这可能是一个问题。由于状态更改会触发所有节点的协调，因此当用户键入时可能会有一点延迟。当处理一个包含几百个组件的大页面时，这一点变得非常明显。

让我们看看如何在不做大改动的情况下改造它:

```
export class StatefulCharacterRow extends Component {
  constructor() {
    super();

    this.toggleEditForm = this.toggleEditForm.bind(this);
    this.syncCharacterEditData = this.syncCharacterEditData.bind(this);
    this.state = {
      show_description: false,
      show_edit_form: false,
      edit_data: {}
    }
  }

  toggleEditForm() {
    const {name, description} = this.props.character;
    const show_edit_form = !this.state.show_edit_form;
    const edit_data = show_edit_form ? {name, description} : {};
    this.setState({show_edit_form, edit_data});
  }

  syncCharacterEditData(character, form_data) {
    this.setState({
      edit_data: {...this.state.edit_data, ...form_data}
    });
  }

  render() {
    const {character} = this.props;
    return (<div className="row">  <div className="name">{character.name}</div>  <a href="#" onClick={() => this.setState({
        show_description: !this.state.show_description})} >  {this.state.show_description ? 'collapse' : 'expand'}  </a>  {!this.state.show_edit_form && <a href="#" onClick={this.toggleEditForm} > edit </a>}  {this.state.show_description &&
        <div className="description">{character.description}</div>}  {this.state.show_edit_form &&
        <EditCharacterDetails character={character}
          cancelEdit={this.toggleEditForm}
          syncCharacterEditData={this.syncCharacterEditData}
          editCharacterDetails={this.props.editCharacterDetails}
          edit_data={this.state.edit_data}/> }  </div>);
  }
}; 
```

处理这个问题最简单的方法是在表单周围创建一个包装器组件(把它想象成一个容器)，并在那里存储状态。因此，当用户输入更改时，只有这个节点得到更新，而不会动摇整个树。

注意，我们只在 React 内部移动了表单状态，但是错误状态仍然保留在外部。如果我们想在表单范围之外处理这些错误，这将有助于减少不必要的混乱。

## 包扎

在决定使用 Redux 时在哪里存储状态之前，理解以下场景会有所帮助:

### 1.这是 UI 状态，还是应用程序状态？

角色名是应用程序状态，而跟踪一个动作是否正在进行是 UI 状态。虽然把他们结合在一起很有诱惑力，但从长远来看，把他们分开是值得的。

```
state = {
  characters: [{
    id: 1,
    name: Jon Snow,
    …
  }],
  ui_state: {
    1: {
      is_edit_in_progress: true,
      show_description: false
    }
  }
} 
```

### 2.如何决定什么进入组件状态，什么进入 Redux

通常一个页面上可以多次渲染 app 数据。例如，我们可以呈现所有角色的列表，并显示按角色所属的房子分组的角色数量。在 Redux 中管理它们是有意义的。

如果存在全局依赖，将 UI 状态存储在 Redux 中。否则，您最好用 React 的本地组件状态来处理它。

Redux 帮助我更好地构建了我的思想。对于 jQuery/Backbone，我关注的是如何操作 DOM 来达到预期的效果。对于 Redux，它是关于让你的应用程序状态正确。一旦你解决了这个问题，你的前端代码库的复杂性就会显著降低。

## 分享这篇文章