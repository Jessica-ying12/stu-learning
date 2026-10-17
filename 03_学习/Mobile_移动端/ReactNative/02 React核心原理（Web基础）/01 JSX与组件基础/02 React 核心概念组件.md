# React 核心概念完整学习笔记

## 一、函数组件

### 1. 概念
- **是什么**  
  函数组件是使用 JavaScript 函数定义的 React 组件。它接收一个 `props` 对象作为参数，并返回用于描述 UI 的 JSX。
- **为什么需要**  
  函数组件是定义 React 组件最简单的方式，代码量少、易于理解和测试。在 React Hooks 出现之前，它只能用于纯展示（无状态），但现在通过 Hooks 它可以拥有状态和生命周期，成为官方推荐的组件编写方式。
- **解决什么问题**  
  解决了类组件写法冗长、`this` 指向复杂的问题，同时保持了组件的纯粹性，更容易进行逻辑复用。

### 2. 原理
- **底层实现**  
  函数组件本质上是一个普通的 JavaScript 函数。React 在渲染时调用这个函数，传入 `props` 对象，并执行函数体中的代码，最终返回一个 React 元素（虚拟 DOM 树）。
- **工作流程**  
  1. 组件初次渲染：React 调用函数组件，执行其内部逻辑（包括 Hooks 的初始化），返回 JSX，React 将其转换为真实 DOM。
  2. 组件更新：当组件的 `props` 或 `state` 发生变化时，React 会再次调用函数组件，生成新的 JSX，并与之前的虚拟 DOM 进行对比（diff 算法），最后更新真实 DOM。
- **关键技术点**  
  - Hooks（如 `useState`、`useEffect`）的实现依赖于 React 内部的“纤程”（Fiber）架构，在函数组件每次调用时按照固定顺序执行，以维持状态的正确性。
  - 函数组件没有实例，因此不能像类组件那样通过 `ref` 直接访问组件实例（但可以通过 `forwardRef` 转发 ref 到子元素）。

### 3. 使用方法
- **基本用法**  
  ```jsx
  function Welcome(props) {
    return <h1>Hello, {props.name}</h1>;
  }

  // 或使用箭头函数
  const Welcome = (props) => <h1>Hello, {props.name}</h1>;
  ```
- **常见场景**  
  - 展示型组件（Presentational Components）
  - 使用 Hooks 处理状态和副作用
  - 自定义 Hook 的逻辑封装
- **最佳实践**  
  - 保持函数纯净：相同的输入（`props`、`state`、`context`）应始终返回相同的 JSX，不要在渲染中执行副作用。
  - 合理拆分组件：遵循单一职责原则，将大组件拆分为多个小型函数组件。
  - 使用默认导出或具名导出时保持一致性。

### 4. 注意事项
- **常见错误**  
  - 在函数组件内部直接修改 `props` 或 `state`（违反不可变性）。
  - 忘记在事件处理函数中阻止默认行为（如表单提交）。
  - Hooks 使用顺序不一致（违反 Hooks 规则）。
- **性能问题**  
  - 函数组件在每次渲染时都会重新执行内部所有代码，如果计算量较大，可以使用 `useMemo` 或 `useCallback` 进行优化。
  - 避免在渲染函数中创建新的对象或函数作为 props 传递给子组件，可能导致子组件不必要的重渲染（可使用 `useMemo`、`useCallback` 或 `React.memo` 优化）。
- **兼容性**  
  - 函数组件本身支持所有现代浏览器，但若使用了最新的 JavaScript 语法，可能需要 Babel 转译。
  - Hooks 需要 React 16.8 或更高版本。

### 5. 代码示例
```jsx
import { useState, useEffect } from 'react';

function Timer() {
  const [seconds, setSeconds] = useState(0);

  useEffect(() => {
    const interval = setInterval(() => {
      setSeconds(prev => prev + 1);
    }, 1000);
    return () => clearInterval(interval); // 清理副作用
  }, []);

  return <div>Timer: {seconds}s</div>;
}

export default Timer;
```

---

## 二、类组件

### 1. 概念
- **是什么**  
  类组件是使用 ES6 class 语法定义的 React 组件，必须继承自 `React.Component` 或 `React.PureComponent`，并实现一个 `render` 方法，该方法返回 JSX。
- **为什么需要**  
  在 React Hooks 出现之前，类组件是唯一能够拥有内部状态（`state`）和访问生命周期方法的方式，适合编写复杂交互的组件。
- **解决什么问题**  
  解决了函数组件无法独立管理状态和生命周期的限制，提供了更完整的能力来处理组件的挂载、更新、卸载等过程。

### 2. 原理
- **底层实现**  
  类组件会创建组件实例，该实例存储了组件的状态（`state`）和方法。React 在渲染时通过 `new` 关键字实例化类，并调用 `render` 方法获取虚拟 DOM。组件的生命周期方法会在特定时机被 React 调用。
- **工作流程**  
  1. 挂载阶段：执行构造函数 `constructor` → `getDerivedStateFromProps` → `render` → 更新 DOM 和 refs → `componentDidMount`。
  2. 更新阶段：`getDerivedStateFromProps` → `shouldComponentUpdate` → `render` → `getSnapshotBeforeUpdate` → 更新 DOM → `componentDidUpdate`。
  3. 卸载阶段：`componentWillUnmount` 被调用，执行清理工作。
- **关键技术点**  
  - 内部状态 `this.state` 是对象，必须通过 `this.setState` 更新，且更新可能是异步的。
  - 生命周期方法提供了在不同阶段执行逻辑的能力。
  - `this` 绑定问题：事件处理函数需要手动绑定 `this` 或使用箭头函数。

### 3. 使用方法
- **基本用法**  
  ```jsx
  import React from 'react';

  class Welcome extends React.Component {
    constructor(props) {
      super(props);
      this.state = { count: 0 };
    }

    handleClick = () => {
      this.setState({ count: this.state.count + 1 });
    };

    render() {
      return (
        <div>
          <h1>Hello, {this.props.name}</h1>
          <p>Count: {this.state.count}</p>
          <button onClick={this.handleClick}>Increment</button>
        </div>
      );
    }
  }

  export default Welcome;
  ```
- **常见场景**  
  - 需要管理复杂状态（在 Hooks 出现前）。
  - 需要使用生命周期方法（如数据获取、订阅、手动 DOM 操作）。
  - 维护旧的 React 代码库。
- **最佳实践**  
  - 在构造函数中初始化状态并绑定方法。
  - 避免在 `render` 中创建新的函数或对象，以减少子组件不必要的重绘。
  - 使用 `shouldComponentUpdate` 或继承 `PureComponent` 进行性能优化。

### 4. 注意事项
- **常见错误**  
  - 忘记调用 `super(props)` 导致 `this.props` 为 undefined。
  - 直接修改 `this.state`（例如 `this.state.count = 1`）而不是使用 `setState`。
  - 在 `setState` 中依赖当前状态时，没有使用函数式更新（例如 `this.setState({ count: this.state.count + 1 })` 可能导致批量更新问题）。
- **性能问题**  
  - 类组件的实例化带来轻微开销，但在大多数应用中可忽略。
  - 不合理地使用 `shouldComponentUpdate` 或滥用 `setState` 可能导致性能下降。
- **兼容性**  
  - 类组件需要 ES6 支持，旧浏览器需要 polyfill。
  - 生命周期方法 `UNSAFE_*` 在 React 17+ 中标记为弃用，需避免使用。

### 5. 代码示例
```jsx
import React from 'react';

class Clock extends React.Component {
  constructor(props) {
    super(props);
    this.state = { date: new Date() };
  }

  componentDidMount() {
    this.timerID = setInterval(() => this.tick(), 1000);
  }

  componentWillUnmount() {
    clearInterval(this.timerID);
  }

  tick() {
    this.setState({ date: new Date() });
  }

  render() {
    return (
      <div>
        <h1>Current Time</h1>
        <h2>{this.state.date.toLocaleTimeString()}</h2>
      </div>
    );
  }
}

export default Clock;
```

---

## 三、组件组合

### 1. 概念
- **是什么**  
  组件组合是指将多个组件嵌套在一起以构建更复杂 UI 的模式。React 推荐使用组合而不是继承来复用代码。组合可以通过 `props.children` 或自定义属性传递组件。
- **为什么需要**  
  组件化开发要求 UI 被拆分为独立、可复用的模块。组合允许开发者像搭积木一样构建界面，提高代码的可维护性和可读性。
- **解决什么问题**  
  解决了组件间复用逻辑和布局的问题，避免了多层 props 传递（prop drilling）的复杂性，同时提供了灵活的方式来定制组件内容。

### 2. 原理
- **底层实现**  
  当在 JSX 中嵌套子元素时，React 会将这些子元素作为 `props.children` 传递给父组件。父组件在返回的 JSX 中通过 `{props.children}` 占位，React 会递归渲染这些子元素。
- **工作流程**  
  1. 父组件渲染时，其 `children` 属性包含了子组件的 React 元素（不是真实 DOM）。
  2. 父组件在 JSX 中通过 `{props.children}` 或自定义属性将子元素插入指定位置。
  3. React 在协调阶段处理这些嵌套关系，最终生成完整的虚拟 DOM 树。
- **关键技术点**  
  - `props.children` 可以是任何 React 节点：字符串、数字、元素、数组、函数等。
  - 可以通过自定义属性（如 `left`、`right`）实现多“插槽”布局。
  - 高阶组件（HOC）和 Render Props 也是组合的具体形式。

### 3. 使用方法
- **基本用法**  
  ```jsx
  // 包含关系：使用 children
  function Card({ children }) {
    return <div className="card">{children}</div>;
  }

  function App() {
    return (
      <Card>
        <h2>Title</h2>
        <p>Content goes here.</p>
      </Card>
    );
  }
  ```
- **常见场景**  
  - 布局组件（如卡片、模态框、侧边栏、网格）。
  - 提供可定制内容的容器组件（如 `Dialog`、`Panel`）。
  - 通过自定义属性实现多个出口（例如 `SplitPane`）。
- **最佳实践**  
  - 明确组件的职责：容器组件负责布局，子组件负责具体内容。
  - 使用 `children` 使组件更通用，避免硬编码子组件。
  - 对于多个插槽，使用具名 props（如 `header`、`footer`）提高可读性。

### 4. 注意事项
- **常见错误**  
  - 忘记在父组件中渲染 `{props.children}`，导致嵌套内容丢失。
  - 在 `children` 中传递过多逻辑，导致组件耦合度高。
  - 错误地修改 `children`（`children` 是不可变元素，不能直接修改）。
- **性能问题**  
  - 当父组件重新渲染时，即使子组件没有变化，也可能导致子组件重新渲染。可以使用 `React.memo` 或 `shouldComponentUpdate` 优化。
  - 如果 `children` 是动态生成的（如数组映射），确保给每个元素添加稳定的 `key`。
- **兼容性**  
  - `children` 是 React 内置 prop，适用于所有版本。

### 5. 代码示例
```jsx
// 多插槽组合
function SplitPane({ left, right }) {
  return (
    <div className="split-pane">
      <div className="left-pane">{left}</div>
      <div className="right-pane">{right}</div>
    </div>
  );
}

function App() {
  return (
    <SplitPane
      left={<Contacts />}
      right={<Chat />}
    />
  );
}

// 特例关系：通过配置实现具体化
function Dialog({ title, message, children }) {
  return (
    <div className="dialog">
      <h2>{title}</h2>
      <p>{message}</p>
      {children}
    </div>
  );
}

function WelcomeDialog() {
  return (
    <Dialog title="Welcome" message="Thank you for visiting!">
      <button>OK</button>
    </Dialog>
  );
}
```

---

## 四、受控组件与非受控组件

### 1. 概念
- **是什么**  
  **受控组件**：表单元素的值由 React 的 state 控制，并通过 `onChange` 事件更新 state。  
  **非受控组件**：表单数据由 DOM 自身管理，通过 `ref` 从 DOM 节点获取值，使用 `defaultValue` 设置初始值。
- **为什么需要**  
  处理表单是前端开发常见需求。受控组件提供了单一数据源，使表单状态可预测；非受控组件简化了简单表单的代码，并方便与外部非 React 代码集成。
- **解决什么问题**  
  受控组件解决了表单状态与 UI 同步的问题，支持实时验证、条件控制等；非受控组件解决了在不需要频繁控制的情况下简化代码的问题。

### 2. 原理
- **底层实现**  
  - **受控组件**：React 的 state 作为“唯一数据源”。输入框的 `value` 属性绑定到 state，`onChange` 监听输入事件并调用 `setState` 更新 state，触发重新渲染，从而更新输入框显示的值。
  - **非受控组件**：React 不干预输入值的更新。初始值通过 `defaultValue`（或 `defaultChecked`）设置，后续用户输入直接修改 DOM 的 `value` 属性。需要获取值时，通过 `ref` 访问 DOM 节点读取 `value`。
- **工作流程**  
  - **受控组件**：用户输入 → 触发 `onChange` → 执行 `setState` → 组件重新渲染 → 输入框显示新的 state 值。
  - **非受控组件**：用户输入 → DOM 值变化 → 需要时通过 `ref` 读取当前值。
- **关键技术点**  
  - 受控组件需要为每个输入编写 `onChange` 处理函数，代码量较多。
  - 非受控组件需要 `ref` 来获取值，通常使用 `useRef` 或 `React.createRef`。
  - 对于复选框、单选框，受控组件使用 `checked` 属性，非受控组件使用 `defaultChecked`。

### 3. 使用方法
- **基本用法**  
  **受控组件**：
  ```jsx
  function ControlledForm() {
    const [value, setValue] = useState('');

    const handleChange = (e) => setValue(e.target.value);
    const handleSubmit = (e) => {
      e.preventDefault();
      alert('提交的值: ' + value);
    };

    return (
      <form onSubmit={handleSubmit}>
        <input type="text" value={value} onChange={handleChange} />
        <button type="submit">提交</button>
      </form>
    );
  }
  ```
  **非受控组件**：
  ```jsx
  function UncontrolledForm() {
    const inputRef = useRef(null);

    const handleSubmit = (e) => {
      e.preventDefault();
      alert('提交的值: ' + inputRef.current.value);
    };

    return (
      <form onSubmit={handleSubmit}>
        <input type="text" defaultValue="initial" ref={inputRef} />
        <button type="submit">提交</button>
      </form>
    );
  }
  ```
- **常见场景**  
  - **受控组件**：需要实时验证输入（如密码强度）、动态禁用提交按钮、强制格式（如电话号格式化）、联动其他表单元素。
  - **非受控组件**：简单表单，只需在提交时获取一次值；集成第三方非 React 库（如 jQuery 插件）时。
- **最佳实践**  
  - 大多数情况下推荐使用**受控组件**，保持状态可预测。
  - 对于文件上传 `<input type="file">`，由于其值是只读的，必须使用非受控组件。
  - 避免在同一表单中混用受控和非受控组件，以免混乱。

### 4. 注意事项
- **常见错误**  
  - **受控组件**：忘记绑定 `onChange` 导致输入框无法输入；将 `value` 设为 `undefined` 或 `null` 导致输入框变为可编辑但不受控。
  - **非受控组件**：使用 `value` 而不是 `defaultValue` 导致输入框变为受控且可能被锁定；忘记通过 `ref` 获取最新值。
- **性能问题**  
  - 受控组件在每次输入时都会触发 `setState` 和重新渲染，对于大型表单或高频输入可能有性能影响，可考虑使用防抖或 `useDeferredValue` 优化。
  - 非受控组件不触发额外渲染，性能略好。
- **兼容性**  
  - 两种方式在所有支持 React 的浏览器中均正常工作。
  - 对于移动端虚拟键盘，受控组件可能因重新渲染导致键盘闪烁，需要适当优化。

### 5. 代码示例
```jsx
// 受控组件：实时验证和条件控制
function PasswordForm() {
  const [password, setPassword] = useState('');
  const [isValid, setIsValid] = useState(false);

  const handleChange = (e) => {
    const pwd = e.target.value;
    setPassword(pwd);
    setIsValid(pwd.length >= 8);
  };

  return (
    <form>
      <label>
        密码（至少8位）：
        <input type="password" value={password} onChange={handleChange} />
      </label>
      {!isValid && password && <span style={{ color: 'red' }}>密码太短</span>}
      <button type="submit" disabled={!isValid}>提交</button>
    </form>
  );
}

// 非受控组件：文件上传
function FileUpload() {
  const fileInput = useRef(null);

  const handleSubmit = (e) => {
    e.preventDefault();
    alert(`Selected file: ${fileInput.current.files[0].name}`);
  };

  return (
    <form onSubmit={handleSubmit}>
      <label>
        Upload file:
        <input type="file" ref={fileInput} />
      </label>
      <button type="submit">上传</button>
    </form>
  );
}
```

---

**总结**：本笔记涵盖了 React 的核心组件知识，包括函数组件、类组件、组件组合以及表单处理中的受控与非受控组件。每个知识点都按照概念、原理、使用方法、注意事项和代码示例的结构展开，旨在帮助开发者系统掌握 React 的基础与最佳实践。