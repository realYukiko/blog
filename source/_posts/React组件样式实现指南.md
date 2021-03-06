---
title: React组件样式实现指南
categories:
- 技术翻译(Tech Translation)
tags:
- React
- Front-end
---

原文链接：[The best “styling in React” tutorial you’ve ever seen](https://blog.logrocket.com/the-best-styling-in-react-tutorial-youve-ever-seen-676f1284b945) 

作者:Neo Ighodaro

翻译:Yuki

阅读时间:<10min

## 前言

你大概已经读过很多介绍实现react样式的文章（虽然我觉得其实并没有那么多这样的文章），或者你从来没有读过，你也许会怀疑，这篇文章是否真的如标题所说，介绍的是最好的实现方式？答案是肯定的，而且你可以自由地使用指南里你认可的部分。

阅读本文，你将学习react组件中样式的几种实现方法，并能知道具体怎么实现它们。


## 要点 

这篇文章将介绍以下三种样式实现方式：
- Inline styling
- Styled Components
- CSS Modules

我们将通过一个Todo实例来介绍它们。如果你刚开始学习React，推荐你先去阅读[React官方文档](https://reactjs.org/)。

## 先准备好一个React应用

为了更方便地学习和理解这篇文章，我们可以通过[Create-React-App](https://github.com/facebookincubator/create-react-app)快速创建一个Todo应用，这样我们就可以跳过自行实现Todo应用的步骤，直接开始学习样式实现方式。

## Method #1: Inline styling of React components 内联方式

在HTML中我们可以写内联的CSS样式，而在React中也可以。

我们可以把CSS以属性（attributes）的方式写在React组件中，比如：

```
class ToDoApp extends React.Component {
  // ...
  render() {
    return (
      <div style={{ backgroundColor: "#44014C", width: "300px", minHeight: "200px"}}>
        <h2 style={{ padding: "10px 20px", textAlign: "center", color: "white"}}>ToDo</h2>
        <div>
          <Input onChange={this.handleChange} />
          <p>{this.state.error}</p>
          <ToDoList value={this.state.display} />
        </div>
      </div>
    )
  }
}
```

从代码中可以看到，如果你想使用内联方式，你需要将CSS属性放在双大括号之间。

首先，我们是通过JSX去渲染组件，而对于在JSX中渲染的JS表达式，它们必须被放在一对大括号里，第一对大括号正是将JS表达式放入JSX解析。而里面的那对大括号则创建了一个style对象实例，所以在这里style是作为一个对象传入组件。

> JSX是将XML字符加入JavaScript的一个预处理步骤，你当然可以只用React而不用JSX，只是JSX能让React代码更加优雅。JSX跟XML一样，具有标签，属性，子节点等概念。

其次，需要注意的是，因为在这里style是一个对象，所以其中的属性之间是用逗号分隔的。另外，因为这是js对象属性，所以属性使用驼峰命名，而不用横线。

当我们的组件需要更多更复杂的样式时，使用内联的方式确实不太好看。但是我们已经意识到，这里的style是一个对象，所以我们完全可以创建一个style的对象变量，然后把它传入组件。就让我们试一下吧。

## 创建一个style对象

与在JavaScript中创建对象无异，我们可以创建一个style对象并将它传给组件。

```
const TodoComponent = {
  width: "300px",
  margin: "30px auto",
  backgroundColor: "#44014C",
  minHeight: "200px",
  boxSizing: "border-box"
}

const Header = {
  padding: "10px 20px",
  textAlign: "center",
  color: "white",
  fontSize: "22px"
}

const ErrorMessage = {
  color: "white",
  fontSize: "13px"
}

class ToDoApp extends React.Component {
  // ...
  render() {
    return (
      <div style={TodoComponent}>
        <h2 style={Header}>ToDo</h2>
        <div>
          <Input onChange={this.handleChange} />
          <p style = {ErrorMessage}>{this.state.error}</p>
          <ToDoList value={this.state.display} />
        </div>
      </div>
    )
  }
}
```

在上面的代码中，我们创建了三个对象变量：TodoComponent、Header、ErrorMessage，并将它们放入了JSX中组件属性中。我们不再在属性标签中直接写样式。

> 注意，这里我们用的是单大括号了，因为这里对象变量本身已经是对象，不需要再用一对大括号创建一个对象。

在对象中，所有的样式属性都是驼峰命名，在css中，它们会被转为由横线分割的属性，如：

```
backgroundColor: "#44014C",
minHeight: "200px",
boxSizing: "border-box"
```

在css中被转换为：
```
background-color: #44014C;
min-height: 200px;
box-sizing: border-box;
```

> 注意，这里的转换只适用于样式属性名，而不会处理属性值，如上文的border-box

同时，你可以以变量的形式存储样式属性值并把它作为style对象属性：

```
const spacing = "10px 20px";

const Header = {
    margin: spacing,
    padding: spacing
    // ...
}
```

在 javaScript的世界里，我们并不推荐总是创建全局变量，但在React中，这不是什么大问题，因为文件模块是相互独立的，即使同一个命名，在不同的文件模块中并不会造成冲突。

## 多组件共享style对象

style对象与组件不需要放在用个文件，我们可以为它们创建各自分离的js文件，将style对象所在的文件抽离成模块，再在需要它们的组件文件中引入并使用。由此可以提高代码的复用率和可维护性。

首先我们创建一个单独的js文件 _style.js_ :
```
const TodoComponent = {
  width: "300px",
  margin: "30px auto",
  backgroundColor: "#44014C",
  minHeight: "200px",
  boxSizing: "border-box"
}

const Header = {
  padding: "10px 20px",
  textAlign: "center",
  color: "white",
  fontSize: "22px"
}

const ErrorMessage = {
  color: "white",
  fontSize: "13px"
}

const styles = {
  TodoComponent: TodoComponent,
  Header: Header,
  ErrorMessage: ErrorMessage
}
```

在上面的代码中，我们可以将所有style对象单独输出（export），如果这么做，也意味着你在使用的时候，也需要把每一个对象分别引入（import）进来，当style对象很多的时候，将会变得十分繁琐。

所以我们可以将整个style文件作为一个对象输出，这样导入会更加方便。

```

import React, { Component } from "react";

// Import the styles
import {styles} from "./styles";

class ToDoApp extends React.Component {
  // ...
  render() {
    return (
      <div style={styles.TodoComponent}>
        <h2 style={styles.Header}>ToDo</h2>
        <div>
          <Input onChange={this.handleChange} />
          <p style = {styles.ErrorMessage}>{this.state.error}</p>
          <ToDoList value={this.state.display} />
        </div>
      </div>
    )
  }
}
```

`import {styles} from "./styles";` 这一行我们导入了style并可以按需使用其中的样式对象，就像使用一般的javascript对象一样。而且它可以被导入其他组件进行复用。

这就是关于使用内联样式的部分了，下一部分是使用


## Method #2: Styled components 使用Styled components

使用Styled components，你可以真正地在js文件中写css代码。这意味着你可以使用所有css的功能，比如媒体查询（media queries）、伪类选择器（pseudo-selector）, 组合选择器(nesting)等等。

[Styled-components](https://github.com/styled-components/styled-components) 使用的是 ES6’s tagged template literals。使用Styled components，组件和样式之间的映射将被移除，这意味着当你在写样式代码的时候，你其实正在创建一个拥有这部分样式的组件。通过styled components, 我们可以创建一个可复用的具有样式的组件，下面将介绍具体的用法。

首先，我们需要安装Styled components，在项目根目录下，运行:
`$ npm install --save styled-components`

接下来让我们回到我们的 to-do 应用，并使用styled compnents 来实现它的样式。 首先我们需要导入styled-components包：

`import styled from 'styled-components';`

现在我们可以开始使用它了，我们先创建一个styled component，接着我们再来看我们应该如何使用它。


```
const TodoComponent = styled.div`
    background-color: #44014C;
    width: 300px;
    min-height: 200px;
    margin: 30px auto;
    box-sizing: border-box;
`;
```

通过上面的代码我们创建了一个具有样式的组件，对于其他react组件，我们同样可以这么实现。不过，值得留意的是，我们是在JS文件中写真正的css代码。那么接下来我们将使用这个组件。

```
class ToDoApp extends React.Component {
  // ...
  render() {
    return (
      <TodoComponent>
        <h2>ToDo</h2>
        <div>
          <Input onChange={this.handleChange} />
          <p>{this.state.error}</p>
          <ToDoList value={this.state.display} />
        </div>>
      </TodoComponent>
    )
  }
}
```
在上面的代码中，`<TodoComponent>`便会使用我们创建的对应的styled component，这个标签的用法跟其他一般的HTML元素基本一样，唯一不同的是它已经具有自己的样式，就是我们在前面的代码中定义的这部分样式。

对于其他元素，我们也可以为它们编写样式：
```
import styled from 'styled-components';

const TagComponent = styled.div`
  background-color: #44014C;
  width: 300px;
  min-height: 200px;
  margin: 30px auto;
  box-sizing: border-box;
`;

const Header = styled.h2`
  padding: 10px 20px;
  text-align: center;
  color: white;
  fontSize: 22px
`;

const ErrorMessage = styled.p`
  color: white;
  font-size: 13px;
`;

class ToDoApp extends React.Component {
  // ...
  render() {
    return (
      <TagComponent>
        <Header>ToDo</Header>
        <div>
          <Input onChange={this.handleChange} />
          <ErrorMessage>{this.state.error}</ErrorMessage>
          <ToDoList value={this.state.display} />
        </div>
      </TagComponent>
    )
  }
}
```
如果你想知道更多关于styled components的内容，你可以阅读[Style components官方文档](https://www.styled-components.com/)

接下来让我们来说一下最后一种方式。

## Method #3: CSS Modules CSS模块
在这里，一个CSS模块是指一个CSS文件，在CSS文件汇总，所有的类名和动画名称都会被限制在当前文件中。注意这句话，限制在当前文件中，现在我们将要破坏这个规则。

CSS 类名和动画名称将默认被放在全局中。如果是在一个比较大的样式文件中，这可能会导致大量冲突。一个样式的定义可能会被另一个所覆盖。而CSS模块解决了这个问题。CSS 类只会在用到它们的组件上起效。一个CSS模块实际上是一个编译过的`.css`文件。编译结束后我们将得到两个结果，其中一个是对类名加注版本号的CSS的文件，另一个则是一个将新类名与原始类名对应起咯爱的js对象。

让我们看一下它具体是怎么使用的。如果你想更多地了解这部分内容，你可以看[这篇文章](https://www.andrewhfarmer.com/what-are-css-modules/)。现在就让我们来创建一个给错误信息组件加样式的CSS类。这个CSS文件我们命名为 style.css :

```
.error-message {
    color: red;
    font-size: 16px;
} 
```
编译完之后，我们会产生类似以下的代码：

```
.error-message_jhys {
     color: red;
     font-size: 16px;
}
```

`_jhys`是我自己加的一个key，用于识别这个类。像之前所说的，这里将产生一个JS对象，我们可以在react文件中导入并使用它。


```
{
    error-message: error-message_jhys
}
```

让我们看一下应该怎么使用它：

```
import styles from './styles.css';
class Message extends React.Component {
     // ... 
    render() {
        return (
            <p className = {styles.error-message}>I am an error message</p> 
        )
    }
}
```
务必记得，我们使用CSS模块的目的是希望通过将CSS类与类之间相互隔离，防止造成命名冲突。

## 结束语

这篇文章到这里就结束了，我们介绍了三种方法来实现React组件的样式。所有的方法都是可行的，而具体采用哪一种方案，取决于我们的项目规模。


## 译者
第一次完整翻译一篇文章...确实比自己看费力多了，不过也比自己看仔细很多。希望能多翻译些较新的、有趣的技术文章，个人能力有限，我会选择一些适合碎片化阅读的技术文章，有什么推荐的资源都可以跟我说喔。