# React 16 有什么新功能？

> 原文：<https://www.sitepoint.com/react-16-new-features/>

在这篇文章中，我们将学习如何使用 React 16 中的一些新功能创建一个音乐播放器。

在实现这个音乐播放器时，我们将了解 React 16 中的一些变化。有相当多的变化，所以我们不会涵盖所有的，但我们会涵盖那些重要的，你可以实现今天。

这篇文章的完整来源可以在 GitHub 的[上找到。](https://github.com/fullstackreact/react-daily-ui)

要启动该应用程序，请将代码`cd`下载到项目目录中，并键入:

```
npm install
npm start 
```

## React 应用程序中的状态

所有 React 应用程序都包含一个名为`state`的属性，该属性决定应该如何显示哪些组件(以及与这些组件相关的任何数据)。

我们的音乐播放器有一个包含两条重要信息的`state`属性:一个变量指定播放器是否在播放音乐—`playing`布尔值，另一个变量跟踪当前音轨的状态—`currentTrackIndex`变量。

```
this.state = {
  playing: false,
  currentTrackIndex: 0
}; 
```

### 什么是状态？

当我们提到组件的**状态**时，我们指的是页面上组件实例的快照。

React 的组件可以定义自己的状态，我们将在本文中用到。当我们在 React 组件中使用状态时，该组件被称为是**有状态的**。React 组件可以使用一个用于处理有状态组件的`state`属性来定义自己的状态，比如我们的音乐播放器。

当用户点击**播放**、**暂停**、**下一个**和**上一个**按钮以及播放器中的曲目时，我们的组件将更新其当前状态。

### 道具 vs 状态

对于 React 应用程序，理解**属性**和**状态**之间的区别很重要。我们的音乐播放器有两个`state`变量，它们决定了我们的应用程序在给定时间点的显示方式。`App`组件是我们的主组件，它驱动我们的子组件——`Controls`组件和`TrackList`组件的显示。为了让这两个组件接收关于应用程序状态的信息，`App`组件将把信息作为道具传递给子组件。然后，可以在子组件中使用这些道具来正确显示应用程序的各个部分。另一个需要理解的重要事情是，每次我们的`App`组件更新时，我们的`Controls`组件和`TrackList`组件也会更新，因为它们依赖于来自`App`组件的信息。

## 控制

我们的`Controls`组件是我们的`App`组件的第一个子组件。`Controls`组件被赋予两个道具:`onClick`和`playing`。`onClick` prop 允许我们将在`App`组件中定义的`handleClick`函数传递给`Controls`组件。当用户点击我们的`Controls`组件中的一个按钮时，就会调用`handleClick`函数。`playing` prop 允许`Controls`组件知道播放器的当前状态，这样我们就可以正确地呈现**播放**图标或**暂停**图标。

让我们探索一下如何在`Controls`组件中呈现按钮和处理点击。

在我们的`Controls`组件中，我们有三个重要的按钮:

1.  **< <** (上一首)按钮—一个指向左侧的箭头图标—用于选择列表中的上一首曲目
2.  **播放/暂停**按钮，播放和暂停音乐
3.  **> >** (下一首)按钮—一个指向右侧的箭头图标—用于选择列表中的下一首曲目。

当每个按钮被点击时，我们调用从`App`组件传入的点击处理函数。我们的音乐播放器应用程序中的每个按钮都有一个`id`,它将帮助我们决定如何处理特定的点击。

在`handleClick`函数的内部，我们使用一个`switch`语句，该语句使用被点击按钮的`id`—`e.target.id`来决定如何处理按钮的动作。在下一节中，我们将看看在 switch 语句的每种情况下会发生什么。

### 播放按钮

当点击播放按钮时，我们需要更新应用程序的一些部分。我们需要将播放图标切换到暂停图标。如果当前设置为 0，我们还需要更新`currentTrackIndex`。为了改变应用程序的这两个部分，我们将调用`setState`，这是一个对每个 React 组件都可用的函数。

所有 React 组件都可以使用`setState`函数，这是我们更新音乐播放器状态的方式。`setState`函数中的第一个参数可以是对象，也可以是函数。如果我们不依赖应用程序的当前状态来计算下一个状态，使用一个对象作为第一个参数是一个非常好的方法，如下所示:`this.setState({currentState:'newState'})`。在我们的例子中，我们依靠应用程序的当前状态来确定应用程序的下一个状态，所以我们需要使用一个函数。React 文档说明了这一点的重要性:

> React 可能会将多个`setState()`调用批处理到单个更新中以提高性能。因为`this.props`和`this.state`可能会异步更新，所以您不应该依赖它们的值来计算下一个状态。

随着 React 16 开启更多的功能(包括异步渲染)，理解这种区别将变得更加重要。

当点击播放按钮并调用`setState`时，我们传递一个函数，因为我们依赖于状态变量`currentTrackIndex`的当前值。传递给函数的第一个参数是应用程序的前一个状态，第二个参数是当前的`props`。在我们的例子中，我们只需要应用程序的前一个状态来确定下一个状态:

```
case "play":
  this.setState((state, props) => {
    let currentTrackIndex = state.currentTrackIndex;
    if (currentTrackIndex === 0) {
      currentTrackIndex = 1;
    } 
```

一旦我们基于`currentTrackIndex`的先前值正确地设置了`currentTrackIndex`，我们就返回一个包含我们想要更新的值的对象。在点击**播放**按钮的情况下，我们将`playing`布尔值更新为`true`，并设置`currentTrackIndex`的值:

```
return {
  playing: true,
  currentTrackIndex: currentTrackIndex
}; 
```

传递给`setState`函数的第二个参数是在`setState`函数完成后调用的回调函数。当点击**播放**按钮时，我们的应用程序的状态被更新，我们想要开始播放音乐。我们将`this.playAudio`函数作为第二个参数传递给我们的`setState`函数。

```
},this.playAudio); 
```

当`playAudio`按钮被调用时，我们引用`audio`标签并通过[网络音频 API](https://developer.mozilla.org/en-US/docs/Web/API/Web_Audio_API) 调用我们可用的`load`和`play`功能。

```
playAudio(){
  this.audioElement.load();
  this.audioElement.play();
} 
```

### `ref`到一个 DOM 元素

为了引用实际的音频 DOM 元素来播放音频，我们需要使用一个对所有 React 组件都可用的特殊属性,`ref`属性。从 React 文档中:

> 当在 HTML 元素上使用`ref`属性时，`ref`回调接收底层 DOM 元素作为它的参数。

在我们的情况下，我们将`ref`属性添加到我们的`audio` DOM 元素，这允许我们播放每个音轨的音频:

```
<audio ref={(audio)=>{this.audioElement = audio}} src={"/songs/"+this.state.currentTrackIndex+".mp3"}/> 
```

### 暂停按钮

当点击**暂停**按钮时，我们调用`this.setState`并将我们的`playing`布尔值设置为`false`。

```
case "pause":
  this.setState({ playing: false },this.pauseAudio);
  break; 
```

我们的`setState`函数调用的第二个参数是我们的`this.pauseAudio`函数，它引用了`audio`元素并调用了`pause()`函数。

```
pauseAudio(){
  this.audioElement.pause();
} 
```

### <

当点击 **< <** 图标时，上一个按钮的`id`与 switch 语句的“prev”大小写相匹配，因此执行与“prev”大小写相关的代码。在“prev”的情况下，我们再次调用`this.setState()`函数，就像我们播放和暂停应用程序一样。这一次，我们使用`currentTrackIndex`的前一个值来递减该值，并返回一个对象来将`currentTrackIndex`设置为新值。

```
case "prev":
  this.setState((state, props) => {
    let currentIndex = state.currentTrackIndex - 1;
    if (currentIndex <= 0) {
      return null;
    } else {
      return { playing:true,currentTrackIndex: currentIndex };
    }
  },this.playAudio); 
```

### 从`setState`返回`null`

React 16 中的一个新变化是，当我们从一个`setState`函数返回`null`时，我们的应用程序将不会被重新呈现。我们的曲目列表有 11 首可用的曲目。如果用户继续点击 **< <** 按钮，`currentTrackIndex`将递减，直到变为 0。一旦它达到 0，我们不再想减少`currentTrackIndex`，我们不再需要重新渲染我们的应用程序。当我们的 **> >** 图标被点击时，我们也做同样的事情。如果`currentTrackIndex`等于(或大于)我们列表中的曲目数量(11)，我们从`setState`返回`null`。

### `>>`(下一步)按钮

当 **> >** 按钮被调用时，我们有一个类似于 **< <** 按钮的功能。每当用户点击 **> >** 时，我们增加`currentTrackIndex`并检查`currentTrackIndex`是否大于曲目列表的长度。如果是，我们在我们的`setState`函数调用中返回`null`。

```
case "next":
  this.setState((state, props) => {
    let currentIndex = state.currentTrackIndex + 1;
    if (currentIndex > data.tracks.length) {
      return null;
    } else {
      return { playing:true,currentTrackIndex: currentIndex };
    }
  },this.playAudio);
  break; 
```

## 曲目列表

为了便于理解本文中的概念，我们将曲目列表数据硬编码在一个 JSON 文件中。我们从顶部的 JSON 文件导入数据，在我们的生命周期方法`componentDidMount`中，我们设置我们的`TrackList`组件的状态。我们的`TrackList`组件的状态包含一个变量，即`tracks`变量。

### 生命周期方法`componentDidMount`和`componentDidUpdate`

除了`setState`函数，每个 React 组件都有可用的生命周期方法。我们的`TrackList`组件使用了其中的两个，`componentDidMount`和`componentDidUpdate`。当 DOM 中的 React 组件可用时，调用`componentDidMount`。在这种情况下，我们希望向组件添加一些数据，因此在`componentDidMount`中调用`setState`是合适的时机。

当我们的`App`组件更新`currentTrackIndex`时，我们的`TrackList`组件中的`componentDidUpdate`方法被触发，因为`TrackList`组件正在获取新数据。当`TrackList`组件获得新数据时，我们希望确保当前选择的轨迹在我们的视口中，因此我们进行一些计算来确定当前选择的轨迹在 DOM 中的位置，并使它出现在轨迹列表容器的视图中。

```
componentDidUpdate() {
  if (this.activeTrack) {
    let topOfTrackList = this.trackList.scrollTop;
    let bottomOfTrackList =
      this.trackList.scrollTop + this.trackList.clientHeight;
    let positionOfSelected = this.activeTrack.offsetTop;
    if (
      topOfTrackList > positionOfSelected ||
      bottomOfTrackList < positionOfSelected
    ) {
      this.trackList.scrollTop = positionOfSelected;
    }
  }
} 
```

### 显示曲目列表

我们使用 JavaScript `map`函数循环遍历我们的轨道数组，并为数组中的每个元素调用一个函数。我们调用的函数是`renderListItem`，它包含一些逻辑来确定`currentTrackIndex`是否是我们正在渲染的数组中的当前元素。如果是，我们需要确保`li`上的`className`的值包括`selected`字符串。这将确保选定轨迹的样式与列表中的其他轨迹不同。

```
renderListItem(track, i) {
  let trackClass = this.props.currentTrackIndex === track.id
    ? "selected"
    : "";
  return (
    <li
      key={track.id}
      className={trackClass}
      ref={cur => {
        if (this.props.currentTrackIndex === track.id) {
          this.activeTrack = cur;
        }
      }}
      onClick={()=>{this.props.selectTrackNumber(track.id)}}
    >  <div className="number">{track.id}</div>  <div className="title">{track.title}</div>  <div className="duration">{track.duration}</div>  </li>
  );
} 
```

`li`元素还包含其他一些重要的属性:

*   每当我们有一个列表时，我们都需要包含这个属性，这样列表才能正确呈现。关于在 React 中使用列表键的更多信息，请查看 React 文档中的这篇文章。

*   `className`:如果是当前选中的音轨，确保`li`有附加的`selected`类。

*   `ref`:我们使用`ref`属性来计算曲目列表容器的正确位置。如果当前轨迹不可见，我们计算当前轨迹的位置并使其可见。我们需要访问实际的 DOM 元素来进行正确的计算。

*   `onClick`:当用户选择了一个特定的曲目，我们调用这个函数，这个函数调用`this.props.selectTrackNumber`。这个函数从我们的父组件`App`传递到`TrackList`组件，就像`Controls`组件的点击处理程序一样。当这个函数被调用时，我们的应用程序的状态被更新，`currentTrackIndex`被设置为用户选择的音轨编号。

```
selectTrackNumber(trackId){
  this.setState({currentTrackIndex:trackId,playing:true},this.playAudio);
} 
```

## 试试吧！

查看 Codepen 示例。这张专辑的艺术来自一个名为玻璃动物的乐队的专辑。由于我们不能合法地播放“玻璃动物”的原声音乐，我们挑选了一些免版税的音乐来代替它，这样我们就可以获得音乐播放器的全部效果。

参见 [CodePen](https://codepen.io) 上杰克奥利弗([@杰克奥利弗](https://codepen.io/jackoliver))的笔[React daily ui–009–音乐播放器](https://codepen.io/jackoliver/pen/zBgppO/)。