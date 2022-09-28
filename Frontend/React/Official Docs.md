# 核心概念

## JSX

JSX 标签语法既不是字符串也不是 HTML。

### 大括号 {}

在大括号中，可以放入任何 JavaScript 表达式。

```react
const name = 'Josh Perez';
const element = <h1>Hello, {name}</h1>;

const element = (
  <h1>
    Hello, {formatName(user)}!
  </h1>
);
```

使用 () 包裹 JSX，可以避免自动插入分号。

编译之后，JSX 表达式会被转为普通的 JavaScript 函数调用，并且对其取值后得到 JavaScript 对象。可以在代码块中使用 JSX，可以将 JSX 赋值给变量，也可以把 JSX 作为参数传入，以及从函数中返回 JSX。

### JSX 中指定属性

使用**引号**，将属性值指定为**字符串字面量**：

```react
const element = <a href="https://www.reactjs.org"> link </a>;
```

使用**大括号 {}**，在属性值中插入一个 **JavaScript 表达式**：

```react
const element = <img src={user.avatarUrl}></img>;
```

因为 JSX 语法上更接近 JavaScript 而不是 HTML，所以 React DOM 使用 `camelCase`（小驼峰命名）来定义属性的名称，而不使用 HTML 属性名称的命名约定。

例如，JSX 里的 `class` 变成了 `className`，而 `tabindex` 则变为 `tabIndex`。

### JSX 防止注入攻击（XSS）

React DOM 在渲染所有输入内容之前，默认会进行转义，可以有效地防止 XSS 攻击。

### JSX 表示对象

Babel 会把 JSX 转译成一个名为 `React.createElement()` 函数调用。

以下两种示例代码完全等效：

```react
const element = (
  <h1 className="greeting">
    Hello, world!
  </h1>
);

const element = React.createElement(
  'h1',
  {className: 'greeting'},
  'Hello, world!'
);
```

## 元素渲染

React 元素是创建开销极小的普通对象。React 元素是**不可变对象**。一旦被创建，你就无法更改它的子元素或者属性。一个元素就像电影的单帧：它代表了某个特定时刻的 UI。更新 UI 唯一的方式是创建一个全新的元素，并将其传入 `root.render()`。

## 组件 & Props

组件，从概念上类似于 JavaScript 函数。它接受任意的入参（即 “props”），并返回用于描述页面展示内容的 React 元素。

### 函数组件与 class 组件

```react
/** 函数组件
	该函数是一个有效的 React 组件，因为它接收唯一带有数据的 “props”（代表属性）对象与并返回一个 React 元素。这类组件被称为“函数组件”，因为它本质上就是 JavaScript 函数。
*/
function Welcome(props) {
  return <h1>Hello, {props.name}</h1>;
}

/** class 组件 */
class Welcome extends React.Component {
  render() {
    return <h1>Hello, {this.props.name}</h1>;
  }
}
```

**所有 React 组件都必须像纯函数一样保护它们的 props 不被更改。**

## State & 生命周期

 state 是私有的，并且完全受控于当前组件。

每次组件更新时 `render` 方法都会被调用，但只要在相同的 DOM 节点中渲染 `<Clock />` ，就仅有一个 `Clock` 组件的 class 实例被创建使用。

### 正确地使用 State

- **不要直接修改 State**。构造函数是唯一可以给 `this.state` 赋值的地方。

  ```react
  // Wrong
  this.state.comment = 'Hello';
  // Correct
  this.setState({comment: 'Hello'});
  ```

- **State 的更新可能是异步的**。出于性能考虑，React 可能会把多个 `setState()` 调用合并成一个调用。

  ```react
  // Wrong
  this.setState({
    counter: this.state.counter + this.props.increment,
  });
  // Correct
  this.setState((state, props) => ({
    counter: state.counter + props.increment
  }));
  ```

  可以让 `setState()` 接收一个函数而不是一个对象。这个函数用上一个 state 作为第一个参数，将此次更新被应用时的 props 做为第二个参数。

- **State 的更新会被合并**。调用 `setState()` 的时候，React 会把你提供的对象合并到当前的 state。

## 事件处理

- React 事件的命名采用小驼峰式（camelCase），而不是纯小写。
- 使用 JSX 语法时你需要传入一个函数作为事件处理函数，而不是一个字符串。

```react
// HTML
<button onclick="activateLasers()">
  Activate Lasers
</button>

// React
<button onClick={activateLasers}>  
  Activate Lasers
</button>
```

### 阻止事件默认行为

在 React 中，你不能通过返回 `false` 的方式阻止默认行为。你必须显式地使用 `preventDefault`。

```react
function Form() {
  function handleSubmit(e) {
    e.preventDefault();    
    console.log('You clicked submit.');
  }

  return (
    <form onSubmit={handleSubmit}>
      <button type="submit">Submit</button>
    </form>
  );
}
```

在这里，`e` 是一个合成事件。React 根据 [W3C 规范](https://www.w3.org/TR/DOM-Level-3-Events/)来定义这些合成事件，所以你不需要担心跨浏览器的兼容性问题。React 事件与原生事件不完全相同。

### JSX 回调函数的 this

在 JavaScript 中，class 的方法默认不会绑定 `this`。

- 可以使用 [public class fields 语法](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Classes/Public_class_fields#public_instance_fields) 

  ```react
  class LoggingButton extends React.Component {
    // This syntax ensures `this` is bound within handleClick.
    handleClick = () => {
      console.log('this is:', this);
    };
    ...
  }
  ```

- 在回调中使用箭头函数

  ```react
  <button onClick={() => this.handleClick()}>
          Click me
  </button>
  ```

  此语法问题在于每次渲染 `LoggingButton` 时都会创建不同的回调函数。在大多数情况下，这没什么问题，但如果该回调函数作为 prop 传入子组件时，这些组件可能会进行额外的重新渲染。我们通常建议在构造器中绑定或使用 class fields 语法来避免这类性能问题。

### 向事件处理程序传递参数

```react
<button onClick={(e) => this.deleteRow(id, e)}>Delete Row</button>
<button onClick={this.deleteRow.bind(this, id)}>Delete Row</button>
```

上述两种方式是等价的，分别通过箭头函数和 `Function.prototype.bind` 来实现。

在这两种情况下，React 的事件对象 `e` 会被作为第二个参数传递。如果通过箭头函数的方式，**事件对象必须显式的进行传递**，而通过 `bind` 的方式，事件对象以及更多的参数将会被隐式的进行传递。

## 条件渲染

###  JSX 中内联条件渲染的方法

- 与运算符 &&

  `true && expression` 总是会返回 `expression`, 而 `false && expression` 总是会返回 `false`。falsy 表达式会使 `&&` 后面的元素被跳过，但会返回 falsy 表达式的值。

- 三目运算符 `condition ? true : false`

### 阻止组件渲染

隐藏组件，你可以让 `render` 方法直接返回 `null`，而不进行任何渲染。

```react
function WarningBanner(props) {
  if (!props.warn) {    
    return null;  
  }
  return (
    <div className="warning">
      Warning!
    </div>
  );
}
```

在组件的 `render` 方法中返回 `null` 并不会影响组件的生命周期。

## 列表 & Key	

key 帮助 React 识别哪些元素改变了，比如被添加或删除。因此你应当给数组中的每一个元素赋予一个确定的标识。

```react
const listItems = numbers.map((number) =>
  <li key={number.toString()}>
    {number}
  </li>
);
```

不建议使用索引来用作 key 值，因为这样做会导致性能变差，还可能引起组件状态的问题。

key 会传递信息给 React ，但不会传递给你的组件。如果你的组件中需要使用 `key` 属性的值，请用其他属性名显式传递这个值。

## 表单

### 受控组件

在 HTML 中，表单元素（如`<input>`、 `<textarea>` 和 `<select>`）通常自己维护 state，并根据用户输入进行更新。而在 React 中，可变状态（mutable state）通常保存在组件的 state 属性中，并且只能通过使用 `setState()`来更新。

我们可以把两者结合起来，使 React 的 state 成为“唯一数据源”。渲染表单的 React 组件还控制着用户输入过程中表单发生的操作。被 React 以这种方式控制取值的表单输入元素就叫做“受控组件”。

### 文件 input 标签

`<input type="file">` 允许用户从存储设备中选择一个或多个文件，将其上传到服务器，或通过使用 JavaScript 的 [File API](https://developer.mozilla.org/en-US/docs/Web/API/File/Using_files_from_web_applications) 进行控制。

```react
<input type="file" />
```

因为它的 value 只读，所以它是 React 中的一个**非受控**组件。

当需要处理多个 `input` 元素时，我们可以给每个元素添加 `name` 属性，并让处理函数根据 `event.target.name` 的值选择要执行的操作。

```react
handleInputChange(event) {
    const target = event.target;
    const value = target.type === 'checkbox' ? target.checked : target.value;
    const name = target.name;
    this.setState({
      [name]: value    });
  }
```

## 状态提升

通常，多个组件需要反映相同的变化数据，这时我们建议将共享状态提升到最近的共同父组件中去。

我们移除组件自身的 state，通过使用 `this.props.temperature` 替代 `this.state.temperature` 来读取温度数据。当我们想要响应数据改变时，我们需要调用 `Calculator` 组件提供的 `this.props.onTemperatureChange()`，而不再使用 `this.setState()`。

## 组合 vs 继承

我们推荐使用组合而非继承来实现组件间的代码重用。

组合类似于 Vue 的插槽（slot）。

```react
function FancyBorder(props) {
  return (
    <div className={'FancyBorder FancyBorder-' + props.color}>
      {props.children}    </div>
  );
}
```

```react
function WelcomeDialog() {
  return (
    <FancyBorder color="blue">
      // props.children start
      <h1 className="Dialog-title">        
        Welcome      
      </h1>      
      <p className="Dialog-message">        
        Thank you for visiting our spacecraft!      
      </p> 
      //  props.children end
    </FancyBorder>
  );
}
```

`<FancyBorder>` JSX 标签中的所有内容都会作为一个 `children` prop 传递给 `FancyBorder` 组件。因为 `FancyBorder` 将 `{props.children}` 渲染在一个 `<div>` 中，被传递的这些子组件最终都会出现在输出结果中。

将所需内容传入 props，并使用相应的 prop。

```react
function SplitPane(props) {
  return (
    <div className="SplitPane">
      <div className="SplitPane-left">
        {props.left}      
      </div>
      <div className="SplitPane-right">
        {props.right}      
      </div>
    </div>
  );
}

function App() {
  return (
    <SplitPane
      left={<Contacts />}
      right={ <Chat />} 
    />
  );
}
```

`<Contacts />` 和 `<Chat />` 之类的 React 元素本质就是对象（object），所以你可以把它们当作 props，像其他数据一样传递。这种方法可能使你想起别的库中“槽”（slot）的概念，但在 React 中没有“槽”这一概念的限制，**你可以将任何东西作为 props 进行传递**。

**组件可以接受任意 props，包括基本数据类型，React 元素以及函数**。

如果你想要在组件间复用非 UI 的功能，我们建议将其提取为一个单独的 JavaScript 模块，如函数、对象或者类。组件可以直接引入（import）而无需通过 extend 继承它们。

## React 哲学

根据单一功能原则来判定组件的范围。一个组件原则上只能负责一个功能。

只保留应用所需的可变 state 的最小集合，其他数据均由它们计算产生。

## ？无障碍

## 代码分割

### import()

在你的应用中引入代码分割的最佳方式是通过动态 `import()` 语法。

**使用之前：**

```javascript
import { add } from './math';

console.log(add(16, 26));
```

**使用之后：**

```javascript
import("./math").then(math => {
  console.log(math.add(16, 26));
});
```

### React.lazy

`React.lazy` 函数能让你像渲染常规组件一样处理动态引入（的组件）。

**使用之前：**

```
import OtherComponent from './OtherComponent';
```

**使用之后：**

```
const OtherComponent = React.lazy(() => import('./OtherComponent'));
```

此代码将会在组件首次渲染时，自动导入包含 `OtherComponent` 组件的包。

`React.lazy` 接受一个函数，这个函数需要动态调用 `import()`。它必须返回一个 `Promise`，该 Promise 需要 resolve 一个 `default` export 的 React 组件。

然后应在 `Suspense` 组件中渲染 lazy 组件，如此使得我们可以使用在等待加载 lazy 组件时做优雅降级（如 loading 指示器等）。

```react
import React, { Suspense } from 'react';

const OtherComponent = React.lazy(() => import('./OtherComponent'));

function MyComponent() {
  return (
    <div>
      <Suspense fallback={<div>Loading...</div>}>
        <OtherComponent />
      </Suspense>
    </div>
  );
}
```

`fallback` 属性接受任何在组件加载过程中你想展示的 React 元素。你可以将 `Suspense` 组件置于懒加载组件之上的任何位置。你甚至可以用一个 `Suspense` 组件包裹多个懒加载组件。

`startTransition`API 可用于在懒加载组件未成功之前，保留旧 UI 并进行交互。

**异常捕获边界**（Error boundaries）

如果模块加载失败（如网络问题），它会触发一个错误。你可以通过异常捕获边界（Error boundaries）技术来处理这些情况，以显示良好的用户体验并管理恢复事宜。

```react
import React, { Suspense } from 'react';
import MyErrorBoundary from './MyErrorBoundary';

const OtherComponent = React.lazy(() => import('./OtherComponent'));
const AnotherComponent = React.lazy(() => import('./AnotherComponent'));

const MyComponent = () => (
  <div>
    <MyErrorBoundary>
      <Suspense fallback={<div>Loading...</div>}>
        <section>
          <OtherComponent />
          <AnotherComponent />
        </section>
      </Suspense>
    </MyErrorBoundary>
  </div>
);
```

### 基于路由的代码分割

大多数网络用户习惯于页面之间能有个加载切换过程。你也可以选择重新渲染整个页面，这样您的用户就不必在渲染的同时再和页面上的其他元素进行交互。

```react
import React, { Suspense, lazy } from 'react';
import { BrowserRouter as Router, Routes, Route } from 'react-router-dom';

const Home = lazy(() => import('./routes/Home'));
const About = lazy(() => import('./routes/About'));

const App = () => (
  <Router>
    <Suspense fallback={<div>Loading...</div>}>
      <Routes>
        <Route path="/" element={<Home />} />
        <Route path="/about" element={<About />} />
      </Routes>
    </Suspense>
  </Router>
);
```

### 命名导出（Named Exports）

`React.lazy` 目前只支持默认导出（default exports）。如果你想被引入的模块使用命名导出（named exports），你可以创建一个中间模块，来重新导出为默认模块。这能保证 tree shaking 不会出错，并且不必引入不需要的组件。

```react
// ManyComponents.js
export const MyComponent = /* ... */;
export const MyUnusedComponent = /* ... */;
// MyComponent.js
export { MyComponent as default } from "./ManyComponents.js";
// MyApp.js
import React, { lazy } from 'react';
const MyComponent = lazy(() => import("./MyComponent.js"));
```

## Context

Context 提供了一种在组件之间共享值的方式，而不必显式地通过组件树的逐层传递 props。

Context 设计目的是为了共享那些对于一个组件树而言是“全局”的数据。

```react
// Context 可以让我们无须明确地传遍每一个组件，就能将值深入传递进组件树。
// 为当前的 theme 创建一个 context（“light”为默认值）。
const ThemeContext = React.createContext('light');
class App extends React.Component {
  render() {
    // 使用一个 Provider 来将当前的 theme 传递给以下的组件树。    
    // 无论多深，任何组件都能读取这个值。    
    // 在这个例子中，我们将 “dark” 作为当前的值传递下去。   
    return (
      <ThemeContext.Provider value="dark">
      	<Toolbar />
      </ThemeContext.Provider>
    );
  }
}

// 中间的组件再也不必指明往下传递 theme 了。
function Toolbar() {  
  return (
    <div>
      <ThemedButton />
    </div>
  );
}

class ThemedButton extends React.Component {
  // 指定 contextType 读取当前的 theme context。  
  // React 会往上找到最近的 theme Provider，然后使用它的值。  
  // 在这个例子中，当前的 theme 值为 “dark”。  
  static contextType = ThemeContext;
  render() {
    return <Button theme={this.context} />;  
  }
}
```

```react
const MyContext = React.createContext(defaultValue);
```

当 React 渲染一个订阅了这个 Context 对象的组件，这个组件会从组件树中离自身最近的那个匹配的 `Provider` 中读取到当前的 context 值。

**只有**当组件所处的树中没有匹配到 Provider 时，其 `defaultValue` 参数才会生效。

多个 Provider 也可以嵌套使用，里层的会覆盖外层的数据。当 Provider 的 `value` 值发生变化时，它内部的所有消费组件都会重新渲染。

## 错误边界

错误边界是一种 React 组件，这种组件**可以捕获发生在其子组件树任何位置的 JavaScript 错误，并打印这些错误，同时展示降级 UI**，而并不会渲染那些发生崩溃的子组件树。错误边界可以捕获发生在整个子组件树的渲染期间、生命周期方法以及构造函数中的错误。

错误边界**无法**捕获以下场景中产生的错误：

- 事件处理（[了解更多](https://zh-hans.reactjs.org/docs/error-boundaries.html#how-about-event-handlers)）
- 异步代码（例如 `setTimeout` 或 `requestAnimationFrame` 回调函数）
- 服务端渲染
- 它自身抛出来的错误（并非它的子组件）

如果一个 class 组件中定义了 [`static getDerivedStateFromError()`](https://zh-hans.reactjs.org/docs/react-component.html#static-getderivedstatefromerror) 或 [`componentDidCatch()`](https://zh-hans.reactjs.org/docs/react-component.html#componentdidcatch) 这两个生命周期方法中的任意一个（或两个）时，那么它就变成一个错误边界。当抛出错误后，请使用 `static getDerivedStateFromError()` 渲染备用 UI ，使用 `componentDidCatch()` 打印错误信息。

```react
class ErrorBoundary extends React.Component {
  constructor(props) {
    super(props);
    this.state = { hasError: false };
  }

  static getDerivedStateFromError(error) {    // 更新 state 使下一次渲染能够显示降级后的 UI    return { hasError: true };  }
  componentDidCatch(error, errorInfo) {    // 你同样可以将错误日志上报给服务器    logErrorToMyService(error, errorInfo);  }
  render() {
    if (this.state.hasError) {      // 你可以自定义降级后的 UI 并渲染      return <h1>Something went wrong.</h1>;    }
    return this.props.children; 
  }
}
    
// 然后你可以将它作为一个常规组件去使用：  
<ErrorBoundary>
  <MyWidget />
</ErrorBoundary>
```

**只有 class 组件才可以成为错误边界组件**。

注意**错误边界仅可以捕获其子组件的错误**，它无法捕获其自身的错误。如果一个错误边界无法渲染错误信息，则错误会冒泡至最近的上层错误边界，这也类似于 JavaScript 中 `catch {}` 的工作机制。

**自 React 16 起，任何未被错误边界捕获的错误将会导致整个 React 组件树被卸载。**

如果你需要在事件处理器内部捕获错误，使用普通的 JavaScript `try` / `catch` 语句：

```react
class MyComponent extends React.Component {
  constructor(props) {
    ...
  }

  handleClick() {
    try {      
      // 执行操作，如有错误则会抛出    
    } catch (error) {      
      this.setState({ error });    
    }  
  }
	...
}
```

## Refs 转发

Ref 转发是一项将 [ref](https://zh-hans.reactjs.org/docs/refs-and-the-dom.html) 自动地通过组件传递到其一子组件的技巧。

`FancyButton` 使用 `React.forwardRef` 来获取传递给它的 `ref`，然后转发到它渲染的 DOM `button`，第二个参数 `ref` 只在使用 `React.forwardRef` 定义组件时存在。常规函数和 class 组件不接收 `ref` 参数，且 props 中也不存在 `ref`。

```react
const FancyButton = React.forwardRef((props, ref) => (  
  <button ref={ref} className="FancyButton">   
    {props.children}
  </button>
));

// 你可以直接获取 DOM button 的 ref：
const ref = React.createRef();
<FancyButton ref={ref}>Click me!</FancyButton>;
```

这样，使用 `FancyButton` 的组件可以获取底层 DOM 节点 `button` 的 ref ，并在必要时访问，就像其直接使用 DOM `button` 一样。

如果你对 HOC 添加 ref，该 ref 将引用最外层的容器组件，而不是被包裹的组件。

我们可以使用 `React.forwardRef` API 明确地将 refs 转发到内部的 `FancyButton` 组件。`React.forwardRef` 接受一个渲染函数，其接收 `props` 和 `ref` 参数并返回一个 React 节点。例如：

```react
function logProps(Component) {
  class LogProps extends React.Component {
    componentDidUpdate(prevProps) {
      console.log('old props:', prevProps);
      console.log('new props:', this.props);
    }

    render() {
      const {forwardedRef, ...rest} = this.props;
      // 将自定义的 prop 属性 “forwardedRef” 定义为 ref
      return <Component ref={forwardedRef} {...rest} />;    }
  }

  // 注意 React.forwardRef 回调的第二个参数 “ref”。
  // 我们可以将其作为常规 prop 属性传递给 LogProps，例如 “forwardedRef”
  // 然后它就可以被挂载到被 LogProps 包裹的子组件上。
  return React.forwardRef((props, ref) => {    
    return <LogProps {...props} forwardedRef={ref} />;  
  });
}
```

## Fragments

React 中的一个常见模式是一个组件返回多个元素。Fragments 允许你将子列表分组，而无需向 DOM 添加额外节点。

```react
function Glossary(props) {
  return (
    <dl>
      {props.items.map(item => (
        // 没有`key`，React 会发出一个关键警告
        <React.Fragment key={item.id}>
          <dt>{item.term}</dt>
          <dd>{item.description}</dd>
        </React.Fragment>
      ))}
    </dl>
  );
}
```

**短语法**

```react
class Columns extends React.Component {
  render() {
    return (
      <>        
      	<td>Hello</td>
        <td>World</td>
      </>    
    );
  }
}
```

`key` 是唯一可以传递给 `Fragment` 的属性，但短语法不支持。

## 高阶组件

**高阶组件是参数为组件，返回值为新组件的函数。**组件是将 props 转换为 UI，而高阶组件是将组件转换为另一个组件。

HOC 不会修改传入的组件，HOC 通过将组件包装在容器组件中来组成新组件。HOC 是纯函数，没有副作用。

**注意事项：**

1. 不要在 render 方法中使用 HOC

   ```react
   render() {
     // 每次调用 render 函数都会创建一个新的 EnhancedComponent
     // EnhancedComponent1 !== EnhancedComponent2
     const EnhancedComponent = enhance(MyComponent);
     // 这将导致子树每次渲染都会进行卸载，和重新挂载的操作！
     // 重新挂载组件会导致该组件及其所有子组件的状态丢失。
     return <EnhancedComponent />;
   }
   ```

   在极少数情况下，你需要动态调用 HOC。你可以在组件的生命周期方法或其构造函数中进行调用。

2. 复制静态方法

   当你将 HOC 应用于组件时，原始组件将使用容器组件进行包装。这意味着新组件没有原始组件的任何静态方法。你可以在返回之前把这些方法拷贝到容器组件上：

   ```react
   function enhance(WrappedComponent) {
     class Enhance extends React.Component {/*...*/}
     // 必须准确知道应该拷贝哪些方法 :(
     Enhance.staticMethod = WrappedComponent.staticMethod;
     return Enhance;
   }
   ```

   除了导出组件，另一个可行的方案是再额外导出这个静态方法。

   ```react
   // 使用这种方式代替...
   MyComponent.someFunction = someFunction;
   export default MyComponent;
   
   // ...单独导出该方法...
   export { someFunction };
   
   // ...并在要使用的组件中，import 它们
   import MyComponent, { someFunction } from './MyComponent.js';
   ```

3. Refs 不会被传递

## JSX

JSX 仅仅只是 `React.createElement(component, props, ...children)` 函数的语法糖。如下 JSX 代码：

```react
<MyButton color="blue" shadowSize={2}>
  Click Me
</MyButton>
```

会编译为：

```react
React.createElement(
  MyButton,
  {color: 'blue', shadowSize: 2},
  'Click Me'
)
```

在 JSX 中，你可以使用点语法来引用一个 React 组件。

如果 `MyComponents.DatePicker` 是一个组件，你可以在 JSX 中直接使用：

```react
import React from 'react';

const MyComponents = {
  DatePicker: function DatePicker(props) {
    return <div>Imagine a {props.color} datepicker here.</div>;
  }
}

function BlueDatePicker() {
  return <MyComponents.DatePicker color="blue" />;
}
```

用户定义的组件必须以大写字母开头。

不能将通用表达式作为 React 元素类型。如果你想通过通用表达式来（动态）决定元素类型，你需要首先将它赋值给大写字母开头的变量。

```react
import React from 'react';
import { PhotoStory, VideoStory } from './stories';

const components = {
  photo: PhotoStory,
  video: VideoStory
};

function Story(props) {
  // 正确！JSX 类型可以是大写字母开头的变量。  
  const SpecificStory = components[props.storyType];  
  return <SpecificStory story={props.story} />;
}
```

`if` 语句以及 `for` 循环不是 JavaScript 表达式，所以不能在 JSX 中直接使用。但是，你可以用在 JSX 以外的代码中。比如：

```react
function NumberDescriber(props) {
  let description;
  if (props.number % 2 == 0) {    description = <strong>even</strong>;  } else {    description = <i>odd</i>;  }  return <div>{props.number} is an {description} number</div>;
}
```

### 字符串字面量

```react
<MyComponent message="hello world" />

<MyComponent message={'hello world'} />
```

### 属性展开

```react
function App1() {
  return <Greeting firstName="Ben" lastName="Hector" />;
}

function App2() {
  const props = {firstName: 'Ben', lastName: 'Hector'};
  return <Greeting {...props} />;
}
```

## 性能优化

### shouldComponentUpdate 的作用

生命周期方法 `shouldComponentUpdate` 会在重新渲染前被触发。其默认实现总是返回 `true`，让 React 执行更新。如果组件不需要更新，可以在 `shouldComponentUpdate` 中返回 `false` 来跳过整个渲染过程。

继承 `React.PureComponent` ，可以使用 `React.PureComponent` 来代替手写 `shouldComponentUpdate`，**但它只进行浅比较**。

```react
shouldComponentUpdate(nextProps, nextState) {
  if (this.props.color !== nextProps.color) {
    return true;
  }
  if (this.state.count !== nextState.count) {
    return true;
  }
  return false;
}
```

### 避免可变对象的产生

为了不改变原本的对象。

```react
// concat
handleClick() {
  this.setState(state => ({
    words: state.words.concat(['marklar'])
  }));
}

// ES6 数组支持扩展运算符
handleClick() {
  this.setState(state => ({
    words: [...state.words, 'marklar'],
  }));
};

//  Object.assign 方法
function updateColorMap(colormap) {
  return Object.assign({}, colormap, {right: 'blue'});
}
```

## Portals

Portal 提供了一种将子节点渲染到存在于父组件以外的 DOM 节点的优秀的方案。例如，对话框、悬浮卡以及提示框。

```react
render() {
  // React 并*没有*创建一个新的 div。它只是把子元素渲染到 `domNode` 中。
  // `domNode` 是一个可以在任何位置的有效 DOM 节点。
  return ReactDOM.createPortal(
    this.props.children,
    domNode  );
}
```

### 事件冒泡

由于 portal 仍存在于 *React 树*， 且与 *DOM 树* 中的位置无关，那么无论其子节点是否是 portal，像 context 这样的功能特性都是不变的。

这包含事件冒泡。一个从 portal 内部触发的事件会一直冒泡至包含 *React 树*的祖先，即便这些元素并不是 *DOM 树* 中的祖先。

```html
<html>
  <body>
    <div id="app-root"></div>
    <div id="modal-root"></div>
  </body>
</html>
```

在 `#app-root` 里的 `Parent` 组件能够捕获到未被捕获的从兄弟节点 `#modal-root` 冒泡上来的事件。

## Profiler API

`Profiler` 测量一个 React 应用多久渲染一次以及渲染一次的“代价”。 它的目的是识别出应用中渲染较慢的部分，或是可以使用[类似 memoization 优化](https://zh-hans.reactjs.org/docs/hooks-faq.html#how-to-memoize-calculations)的部分。

`Profiler` 能添加在 React 树中的任何地方来测量树中这部分渲染所带来的开销。 它需要两个 prop ：一个是 `id`(string)，一个是当组件树中的组件“提交”更新的时候被React调用的回调函数 `onRender`(function)。

```react
render(
  <App>
    <Profiler id="Panel" onRender={callback}>
      <Panel {...props}>
        <Profiler id="Content" onRender={callback}>
          <Content {...props} />
        </Profiler>
        <Profiler id="PreviewPane" onRender={callback}>
          <PreviewPane {...props} />
        </Profiler>
      </Panel>
    </Profiler>
  </App>
);
```

`onRender` 回调：

 React 会在 profile 包含的组件树中任何组件 “提交” 一个更新的时候调用这个函数。 它的参数描述了渲染了什么和花费了多久。

```react
function onRenderCallback(
  id, // 发生提交的 Profiler 树的 “id”
  phase, // "mount" （如果组件树刚加载） 或者 "update" （如果它重渲染了）之一
  actualDuration, // 本次更新 committed 花费的渲染时间
  baseDuration, // 估计不使用 memoization 的情况下渲染整棵子树需要的时间
  startTime, // 本次更新中 React 开始渲染的时间
  commitTime, // 本次更新中 React committed 的时间
  interactions // 属于本次更新的 interactions 的集合
) {
  // 合计或记录渲染时间。。。
}
```

## 自动绑定 this 

- 在 constructor 中绑定方法。
- 使用箭头函数，比如：`onClick={(e) => this.handleClick(e)}`。
- 继续使用 `createReactClass`。

## “diffing” 算法

### 通用方法 O(n 3 )

在某一时间节点调用 React 的 `render()` 方法，会创建一棵由 React 元素组成的树。在下一次 state 或 props 更新时，相同的 `render()` 方法会返回一棵不同的树。React 需要基于这两棵树之间的差别来判断如何高效的更新 UI，以保证当前 UI 与最新的树保持同步。

### React Diffing 算法 O(n) 

1. 两个不同类型的元素会产生出不同的树；
2. 开发者可以使用 `key` 属性标识哪些子元素在不同的渲染中可能是不变的。

#### 对比不同类型的元素

当对比两棵树时，React 首先比较两棵树的根节点。当根节点为不同类型的元素时，React 会拆卸原有的树并且建立起新的树。当一个元素从 `<a>` 变成 `<img>`，从 `<Article>` 变成 `<Comment>`，或从 `<Button>` 变成 `<div>` 都会触发一个完整的重建流程。

当卸载一棵树时，对应的 DOM 节点也会被销毁。组件实例将执行 `componentWillUnmount()` 方法。当建立一棵新的树时，对应的 DOM 节点会被创建以及插入到 DOM 中。组件实例将执行 `UNSAFE_componentWillMount()` 方法，紧接着 `componentDidMount()` 方法。所有与之前的树相关联的 state 也会被销毁。

#### 对比同一类型的元素

当对比两个相同类型的 React 元素时，React 会保留 DOM 节点，仅比对及更新有改变的属性。在处理完当前节点之后，React 继续对子节点进行递归。

#### 对比同类型的组件元素

当一个组件更新时，组件实例会保持不变，因此可以在不同的渲染时保持 state 一致。React 将更新该组件实例的 props 以保证与最新的元素保持一致，并且调用该实例的 `UNSAFE_componentWillReceiveProps()`、`UNSAFE_componentWillUpdate()` 以及 `componentDidUpdate()` 方法。

下一步，调用 `render()` 方法，diff 算法将在之前的结果以及新的结果中进行递归。

#### 对子节点进行递归

默认情况下，当递归 DOM 节点的子元素时，React 会同时遍历两个子元素的列表；当产生差异时，生成一个 mutation。

#### Keys

为了解决上述问题，React 引入了 `key` 属性。当子元素拥有 key 时，React 使用 key 来匹配原有树上的子元素以及最新树上的子元素。

## Refs and the DOM

### 创建 Refs

```react
class MyComponent extends React.Component {
  constructor(props) {
    super(props);
    this.myRef = React.createRef();  }
  render() {
    return <div ref={this.myRef} />;  }
}
```

### 访问 Refs

```react
const node = this.myRef.current;
```

- 当 `ref` 属性用于 HTML 元素时，构造函数中使用 `React.createRef()` 创建的 `ref` 接收底层 DOM 元素作为其 `current` 属性。
- 当 `ref` 属性用于自定义 class 组件时，`ref` 对象接收组件的挂载实例作为其 `current` 属性。
- **你不能在函数组件上使用 `ref` 属性**，因为他们没有实例。但可以在函数组件内部使用。

React 会在组件挂载时给 `current` 属性传入 DOM 元素，并在组件卸载时传入 `null` 值。`ref` 会在 `componentDidMount` 或 `componentDidUpdate` 生命周期钩子触发前更新。

### 回调 Refs

这个函数中接受 React 组件实例或 HTML DOM 元素作为参数，以使它们能在其他地方被存储和访问。

```react
class CustomTextInput extends React.Component {
  constructor(props) {
    super(props);

    this.textInput = null;
    this.setTextInputRef = element => {   
      this.textInput = element;   
    };
    this.focusTextInput = () => {      
      // 使用原生 DOM API 使 text 输入框获得焦点     
      if (this.textInput) this.textInput.focus();    
    };  
  }

  componentDidMount() {
    // 组件挂载后，让文本框自动获得焦点
    this.focusTextInput(); 
  }

  render() {
    // 使用 `ref` 的回调函数将 text 输入框 DOM 节点的引用存储到 React
    // 实例上（比如 this.textInput）
    return (
      <div>
        <input
          type="text"
          ref={this.setTextInputRef}
          />
        <input
          type="button"
          value="Focus the text input"
          onClick={this.focusTextInput}    
          />
      </div>
    );
  }
}
```

## Render Props

**render prop 是一个用于告知组件需要渲染什么内容的函数 prop。**

```react
// class Mouse 
render() {
  return (
    <div style={{ height: '100vh' }} onMouseMove={this.handleMouseMove}>

      {/*
          使用 `render`prop 动态决定要渲染的内容，
          而不是给出一个 <Mouse> 渲染结果的静态表示
        */}
      {this.props.render(this.state)}
    </div>
  );
}
// class MouseTracker
class MouseTracker extends React.Component {
  render() {
    return (
      <div>
        <h1>移动鼠标!</h1>
        <Mouse render={mouse => (
          <Cat mouse={mouse} />
        )}/>
      </div>
    );
  }
}
```

## 严格模式

严格模式不能自动检测到你的副作用，但它可以帮助你发现它们，使它们更具确定性。通过故意重复调用以下函数来实现的该操作：

- class 组件的 `constructor`，`render` 以及 `shouldComponentUpdate` 方法
- class 组件的生命周期方法 `getDerivedStateFromProps`
- 函数组件体
- 状态更新函数 (即 `setState` 的第一个参数）
- 函数组件通过使用 `useState`，`useMemo` 或者 `useReducer`

React 18 为严格模式引入了一个全新的仅用于开发环境的检查操作。每当第一次安装组件时，这个新的检查将自动卸载并重新安装每个组件，并在第二次挂载时恢复之前的 state。

# API 参考

## 组件 React.Component 或 React.PureComponent

React.Component 并未实现 `shouldComponentUpdate()`，而 React.PureComponent 中以浅层对比 prop 和 state 的方式来实现了该函数。某些情况下使用 React.PureComponent 可提高性能。React.PureComponent 中的 `shouldComponentUpdate()` 将跳过所有子组件树的 prop 更新。因此，请确保所有子组件也都是“纯”的组件。

React.memo 仅检查 props 变更。

# HOOK

