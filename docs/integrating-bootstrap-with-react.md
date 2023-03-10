# 集成 Bootstrap 和 React:开发者指南

> 原文：<https://www.sitepoint.com/integrating-bootstrap-with-react/>

将 Bootstrap 与 React 集成在一起，React 开发人员可以使用 Bootstrap 最先进的网格系统及其各种其他组件。

在本教程中，我们将:

*   探索为 React 支持的 web 应用程序构建具有 Bootstrap 外观的用户界面的工具和技术
*   使用 [reactstrap](https://reactstrap.github.io/) 构建 React 联系人列表应用程序。

React 是交互式 web 应用程序中最流行的 JavaScript 技术之一。它的流行源于其基于组件的模块化方法、可组合性和快速重新渲染算法。

然而，作为一个视图库，React 没有任何内置的机制来帮助我们创建响应迅速、流畅和直观的设计。这就是像 [Bootstrap](http://getbootstrap.com/) 这样的前端设计框架介入的地方。

## 为什么不能在 React 中包含引导组件

将 Bootstrap 与 React 结合起来并不像将`<link rel="stylesheet" />`添加到`index.html`文件中那么简单。这是因为 Bootstrap 依赖 jQuery 来驱动某些 UI 组件。jQuery 使用直接的 DOM 操作方法，这与 React 的[声明方法](https://codeburst.io/declarative-vs-imperative-programming-a8a7c93d9ad2)相矛盾。

如果我们只需要响应 12 列网格的 Bootstrap，或者不使用 jQuery 的组件，我们可以简单地求助于普通的 Bootstrap 样式表。除此之外，还有许多库负责弥合 React 和 Bootstrap 之间的差距。我们将比较这两种方法，以便更好地选择适合我们具体情况的方法。

我们开始吧！

## 用 React 设置引导样式表

让我们使用 [Create React App](https://github.com/facebook/create-react-app) CLI 来创建一个 React 项目，该项目需要零配置即可开始。

我们安装 Create React App 并运行以下命令来启动一个新项目并为开发构建提供服务:

```
$ create-react-app react-and-bootstrap
$ cd react-and-bootstrap
$ npm start 
```

下面是 Create React App 创建的目录结构:

```
.
├── package.json
├── public
│   ├── favicon.ico
│   ├── index.html
│   └── manifest.json
├── README.md
├── src
│   ├── App.css
│   ├── App.js
│   ├── App.test.js
│   ├── index.css
│   ├── index.js
│   ├── logo.svg
│   └── registerServiceWorker.js
└── yarn.lock 
```

接下来，让我们从 [Bootstrap 的官方网站](https://getbootstrap.com/docs/4.0/getting-started/download/)下载最新版本的 Bootstrap 库。这个包包括 CSS 和 JavaScript 的编译版本和精简版本。我们将复制 CSS 并将其放在`public/`目录中。对于只需要网格的项目，也包含了特定于网格的样式表。

接下来，我们在`public/`中为 css 创建一个新目录，将`bootstrap.min.css`粘贴到我们新创建的 css 目录中，并从`public/index.html`链接它:

```
<head>
<link rel="stylesheet" href="css/bootstrap.min.css">
</head> 
```

或者，我们可以使用 CDN 来获取缩小的 CSS:

```
<link rel="stylesheet" href= "https://maxcdn.bootstrapcdn.com/bootstrap/4.0.0/css/bootstrap.min.css"> 
```

让我们来看看在这种设置下什么可行，什么不可行。

## 在 React 中使用 Vanilla Bootstrap

一旦我们将引导样式表添加到 React 项目中，我们就可以在 JSX 代码中使用常规的引导类。让我们前往 Bootstrap 演示站点，复制一些随机的示例代码，以检查一切是否正常工作:

[https://codesandbox.io/embed/9y8qq85qqo](https://codesandbox.io/embed/9y8qq85qqo)

我们可以看到，表单看起来不错，但是 NavBar 不好看。这是因为 NavBar 依赖于[折叠 jQuery 插件](http://getbootstrap.com/docs/4.0/components/collapse/)，我们还没有导入它。除了不能切换导航链接之外，我们不能使用下拉菜单、可关闭的提醒和模态窗口等功能。

如果我们可以导入 Bootstrap 作为 React 组件来充分利用 React，这不是很棒吗？例如，想象一下，如果我们可以使用网格、行和列组件的组合来组织页面，而不是讨厌的 HTML 类:

```
<!-- Bootstrap with HTML classes -->

<div class="container">
  <div class="row">
    <div class="col-sm">
      One of three columns
    </div>
    <div class="col-sm">
      One of three columns
    </div>
    <div class="col-sm">
      One of three columns
    </div>
  </div>
</div>

<!-- Bootstrap with React Components -->

<Grid>
  <Row>
      <Col sm>
        One of three columns
    </Col>
    <Col sm>
        One of three columns
    </Col>
     <Col sm>
        One of three columns
    </Col>
  </Row>
</Grid> 
```

幸运的是，我们不必实现自己的库来服务于这个目的，因为已经有一些流行的解决方案可用。让我们来看看其中的一些。

## React 和 Bootstrap 的第三方库概述

有几个库试图创建一个特定于 React 的 Bootstrap 实现，这样我们就可以在使用 Bootstrap 风格的同时使用 JSX 组件。我已经编译了一些流行的引导模块列表，我们可以在 React 项目中使用它们。

React-Bootstrap 是目前最流行的将引导组件添加到 React 中的库之一。然而，在撰写本教程时，它的目标是 Bootstrap v3，而不是 Bootstrap 的最新版本。它正在积极开发中，当新版本发布时，API 可能会中断。

reactstrap 是另一个库，它让我们能够在 React 中使用引导组件。与 React-Bootstrap 不同，reactstrap 是为最新版本的 Bootstrap 构建的。该模块包括用于排版、图标、表单、按钮、表格、布局网格和导航的组件。它也在积极开发中，是构建基于 React 的引导程序的一个很好的选择。

GitHub 上还有一些其他的库，如 [React-UI](https://github.com/Lobos/react-ui) ，以及特定领域的模块，如 [React-bootstrap-table](http://allenfang.github.io/react-bootstrap-table/example.html) 和 [CoreUI-React Admin Panel](https://github.com/mrholek/CoreUI-React) ，它们为使用 React 创建出色的 UI 提供了扩展支持。

本教程的其余部分将关注 reactstrap，因为它是唯一使用最新版本的 Bootstrap 的流行模块。

## 设置 reactstrap 库

我们首先使用 npm 安装 reactstrap 库:

```
npm install --save reactstrap@next 
```

现在，我们可以从模块中导入相关组件，如下所示:

```
import { Container, Row, Col} from 'reactstrap'; 
```

然而，这个库不会像我们预期的那样工作。正如在 [reactstrap 网站](https://reactstrap.github.io/)上解释的那样，这样做的原因是 reactstrap 不包含引导 CSS，所以我们需要自己添加它:

```
npm install --save bootstrap 
```

接下来，我们在`src/index.js`文件中导入引导 CSS:

```
import 'bootstrap/dist/css/bootstrap.css'; 
```

## 引导网格基础

Bootstrap 有一个[响应迅速、移动优先的流体网格系统](http://getbootstrap.com/docs/4.0/layout/grid/),允许每页多达 12 列。要使用网格，我们需要导入容器、行和列组件。

**容器**接受一个`fluid`属性，该属性将固定宽度的布局转换为全角布局。本质上，它将相应的 [`.container-fluid`引导类](http://getbootstrap.com/docs/4.0/layout/overview/)添加到网格中。

至于`<Col>` s，我们可以配置它们接受类似于 Bootstrap 的`col-*`类的`xs`、`md`、`sm`、`lg`等道具——例如`<Col xs="6"> </Col>`

或者，我们可以将一个对象传递给具有可选属性的道具，如`size`、`order`和`offset`。`size`属性描述了列的数量，而`order`让我们对列进行排序，并接受一个从 1 到 12 的值。`offset`属性将列向右移动。

下面的代码演示了 reactstrap 中的一些网格特性:

[https://codesandbox.io/embed/1439ym99mq](https://codesandbox.io/embed/1439ym99mq)

## React 中的引导样式组件

既然 reactstrap 唾手可得，那么有大量的 Bootstrap 4 组件可以与 React 一起使用。涵盖所有这些超出了本教程的范围，但是让我们尝试几个重要的组件，并在实际项目中使用它们——比如说，一个联系人列表应用程序。

### 导航栏

[反应式导航条](https://reactstrap.github.io/components/navbar/)是反应式导航条组件。导航条可以有子组件，如 NavbarBrand、Nav、NavItem 等。为了更好地组织导航链接。

为了使导航条具有响应性，我们可以在我们的`<Navbar>`组件中包含一个`<NavbarToggler>`，然后将`<NavItems>`包装成一个`<Collapse>`组件。

看看下面的代码，它展示了我们如何使用 Navbar 组件和 [React state](https://reactjs.org/docs/state-and-lifecycle.html) 来存储切换数据:

```
export default class Example extends React.Component {
  constructor(props) {
    super(props);

    this.toggle = this.toggle.bind(this);
    this.state = {
      isOpen: false
    };
  }
  toggle() {
    this.setState({
      isOpen: !this.state.isOpen
    });
  }
  render() {
    return (
      <div>  <Navbar color="faded" light expand="md">  {/* Brandname */}  <NavbarBrand href="/"> Demo </NavbarBrand>  {/* Add toggler to auto-collapse */}  <NavbarToggler onClick={this.toggle} />  <Collapse isOpen={this.state.isOpen} navbar>  {/*Pull left */}  <Nav className="ml-auto" navbar>  <NavItem>  <NavLink href="/link/"> Left Nav Link </NavLink>  </NavItem>  </Nav>  {/* Pull right */}  <Nav className="mr-auto" navbar>  <UncontrolledDropdown nav inNavbar>  <DropdownToggle nav caret> Bob </DropdownToggle>  <DropdownMenu >  <DropdownItem> Account </DropdownItem>  <DropdownItem> Settings </DropdownItem>  <DropdownItem divider />  <DropdownItem> Logout </DropdownItem>  </DropdownMenu>  </UncontrolledDropdown>  </Nav>  </Collapse>  </Navbar>  </div>
    );
  }
} 
```

### 模态窗口

[reactstrap 模态组件](https://reactstrap.github.io/components/modals/)创建一个具有页眉、主体和页脚的引导模态。

模态组件接受一些道具和回调来使窗口具有交互性和可关闭性。

属性决定了模态是否可见。`toggle`回调可用于切换控制组件中`isOpen`的值。

添加过渡效果还有额外的道具。可用的回调包括`onEnter`、`onExit`、`onOpened`和`onClosed`:

```
{/*For the modal to open, this.state.show should become true which is usually triggered by an onClick event */}
{/* toggleModal would set state of show to false onClose*/}

<Modal isOpen={this.state.show} toggle={this.toggleModal} >  <ModalHeader toggle={this.toggle}> Modal title </ModalHeader>  <ModalBody> Lorem ipsum dolor sit amet, consectetur adipisicing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua. Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo consequat. </ModalBody>  <ModalFooter>  <Button color="primary" onClick={this.toggle}>Do Something</Button>{' '}  <Button color="secondary" onClick={this.toggle}>Cancel</Button>  </ModalFooter>  </Modal> 
```

### 形式

一个[反应堆`<Form>`](https://reactstrap.github.io/components/form/) 可以是*直列式*或*水平式*。

一个`Input`组件呈现一个`<input>`元素。我们可以将多个`Input`组件包装到一个`FormGroup`中，用于状态验证、适当的间距以及访问其他 FormGroup 特性。

使用`<Label>`设置标签总是一个好主意。表单还有很多，你应该查看表单的[引导文档。](https://reactstrap.github.io/components/form/)

下面是我们的 reactstrap 表单的代码:

```
 <Form>  <FormGroup row>  <Label for="exampleEmail" sm={2}>Email</Label>  <Col sm={10}>  <Input type="email" name="email" id="exampleEmail" placeholder="with a placeholder" />  </Col>  </FormGroup>  <FormGroup row>  <Label for="examplePassword" sm={2}>Password</Label>  <Col sm={10}>  <Input type="password" name="password" id="examplePassword" placeholder="password placeholder" />  </Col>  </FormGroup>  <FormGroup row>  <Label for="exampleSelect" sm={2}>Select</Label>  <Col sm={10}>  <Input type="select" name="select" id="exampleSelect" />  </Col>  </FormGroup>  <FormGroup row>  <Label for="exampleSelectMulti" sm={2}>Select Multiple</Label>  <Col sm={10}>  <Input type="select" name="selectMulti" id="exampleSelectMulti" multiple />  </Col>  </FormGroup>  <FormGroup row>  <Label for="exampleText" sm={2}>Text Area</Label>  <Col sm={10}>  <Input type="textarea" name="text" id="exampleText" />  </Col>  </FormGroup>  </Form> 
```

### 列表组

[reactstrap 列表组](https://reactstrap.github.io/components/listgroup/)使得在 React 中样式化和控制列表项变得更加容易。`ListGroup`包装了`ListGroupItems`，可以使用`onClick`回调使其交互。

代码如下所示:

```
<ListGroup>  <ListGroupItem>Item 1</ListGroupItem>  <ListGroupItem>Item 2</ListGroupItem>  <ListGroupItem>...</ListGroupItem>  </ListGroup>; 
```

### 小跟班

按钮是任何设计框架不可或缺的一部分。对于按钮，有一个[反应陷阱`<Button>`组件](https://reactstrap.github.io/components/buttons/)。除了`active`和`disabled`属性，我们可以使用`color`和`size`来设置按钮的样式(主要、成功等。)和大小(‘LG’、‘sm’等)。):

```
{/*ButtonToolbar helps to organize buttons */}

 <div>
    <Button color="primary">primary</Button>{' '}

    <Button color="secondary">secondary</Button>{' '}

    <Button color="success">success</Button>{' '}

    <Button color="info">info</Button>{' '}

    <Button color="warning">warning</Button>{' '}

    <Button color="danger">danger</Button>{' '}

    <Button color="link">link</Button>
 </div> 
```

## 演示:一个带有 reactstrap 的联系人列表应用程序 UI

这里有一个使用本教程中讨论的组件构建的示例演示。

查看并试验它，以熟悉 reactstrap:

[https://codesandbox.io/embed/xyjx85kyw](https://codesandbox.io/embed/xyjx85kyw)

## 摘要

现在，我们已经介绍了使用 React 充分利用 Bootstrap 所需要知道的一切。

用于集成 Bootstrap 和 React 的库并不缺乏，我们已经研究了一些最流行的选项。我们还探索了一个名为 reactstrap 的流行库。如果您有任何疑问，不要忘记查看文档页面以了解更多关于可用组件的信息。

那么，你对使用带有 React 的 Bootstrap 构建响应式 ui 有什么想法？如果你对 Bootstrap 或者某个你认为有用的库有任何建议，请在评论中分享。

如果你已经掌握了 Bootstrap 的基础知识，但想知道如何将 Bootstrap 技能提升到一个新的水平，请查看我们的[用 Bootstrap 4](https://www.sitepoint.com/premium/courses/building-your-first-website-with-bootstrap-4-2995) 建立你的第一个网站课程，快速而有趣地了解 Bootstrap 的强大功能。

## 分享这篇文章