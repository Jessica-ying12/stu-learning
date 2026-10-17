### 知识点：constructor

#### 概念
- **是什么**：`constructor` 是 JavaScript 类（class）的构造函数，在 React 组件中，它是组件实例化时第一个被调用的生命周期方法。
- **为什么需要**：用于初始化组件的局部状态（`state`）以及绑定事件处理函数。
- **解决什么问题**：为组件的后续渲染准备初始数据和绑定上下文，确保方法内部的 `this` 指向正确。

#### 原理
- **底层实现**：React 在创建组件实例时，会通过 `new` 关键字调用 `constructor`，并传入 `props` 参数。子类构造函数必须调用 `super(props)`，否则 `this.props` 会为 `undefined`。
- **工作流程**：
    1. 调用 `super(props)`，将 `props` 挂载到实例上。
    2. 初始化 `state`（直接赋值 `this.state = ...`）。
    3. 绑定事件处理函数（如 `this.handleClick = this.handleClick.bind(this)`）。
- **关键技术点**：`constructor` 是唯一可以直接修改 `this.state` 的地方；在其他生命周期或方法中必须使用 `this.setState`。

#### 使用方法
- **基本用法**：
  ```jsx
  constructor(props) {
    super(props);
    this.state = {
      count: 0,
      name: props.initialName
    };
    this.handleClick = this.handleClick.bind(this);
  }
  ```
- **常见场景**：
  - 初始化基于 `props` 的 `state`（但需注意此操作可能导致后续更新问题）。
  - 绑定方法以避免 `undefined` 错误。
- **最佳实践**：
  - 避免将 `props` 直接赋值给 `state`（除非明确需要忽略后续 `prop` 更新），因为这会导致派生状态，推荐使用其他生命周期处理。
  - 不要在此处调用 `setState()`，应直接赋值 `this.state`。
  - 不要引入副作用（如网络请求），应放在 `componentDidMount`。

#### 注意事项
- **常见错误**：
  - 忘记调用 `super(props)`，导致 `this.props` 为 `undefined`。
  - 将 `props` 复制到 `state` 但未考虑更新，导致状态不同步。
- **性能问题**：`constructor` 只执行一次，通常不会引起性能问题，但应避免执行耗时操作。
- **兼容性**：ES6 类语法的标准部分，所有支持 ES6 的环境均可使用。

#### 代码示例
```jsx
class MyComponent extends React.Component {
  constructor(props) {
    super(props);
    this.state = {
      counter: 0,
      text: props.defaultText
    };
    this.increment = this.increment.bind(this);
  }

  increment() {
    this.setState(prevState => ({ counter: prevState.counter + 1 }));
  }

  render() {
    return (
      <div>
        <p>{this.state.text}: {this.state.counter}</p>
        <button onClick={this.increment}>增加</button>
      </div>
    );
  }
}
```

---

### 知识点：render

#### 概念
- **是什么**：`render` 是 React 组件中唯一必须实现的方法，用于描述组件的 UI 结构。
- **为什么需要**：作为组件的输出函数，将组件的 `props` 和 `state` 转换为 React 元素，从而渲染到页面上。
- **解决什么问题**：提供了声明式 UI 的定义方式，React 根据 `render` 返回的内容来更新 DOM。

#### 原理
- **底层实现**：`render` 方法应该是一个纯函数，它不应修改组件状态，每次调用应返回相同的结果。React 在协调（reconciliation）过程中会调用 `render` 获取新的元素树，并与之前的树进行对比（diffing），最终批量更新 DOM。
- **工作流程**：
    1. 在挂载阶段，`render` 被调用生成初始虚拟 DOM。
    2. 在更新阶段，当 `props` 或 `state` 变化时，`render` 再次被调用生成新的虚拟 DOM，React 进行 diff 并更新实际 DOM。
- **关键技术点**：
  - 返回类型：可以是 React 元素、数组、Fragment、Portals、字符串、数字、布尔值或 `null`。
  - `render` 应避免执行副作用，如直接操作 DOM 或发送请求。

#### 使用方法
- **基本用法**：
  ```jsx
  render() {
    return <div>Hello, {this.props.name}</div>;
  }
  ```
- **常见场景**：
  - 根据 `state` 和 `props` 渲染条件内容。
  - 渲染列表（使用 `map` 方法）。
- **最佳实践**：
  - 保持 `render` 简洁，不包含复杂逻辑，可将计算逻辑提取到方法中。
  - 避免在 `render` 中调用 `setState`，否则会触发无限循环。
  - 使用 Fragments 返回多个元素而不额外添加 DOM 节点。

#### 注意事项
- **常见错误**：
  - 在 `render` 中调用 `setState` 导致无限循环。
  - 返回 `undefined` 或 `null` 时未正确处理（React 允许返回 `null` 表示不渲染任何内容，但返回 `undefined` 会在控制台报错）。
- **性能问题**：`render` 频繁调用可能导致性能下降，应通过 `shouldComponentUpdate` 或 `PureComponent` 优化不必要的渲染。
- **兼容性**：始终存在，是类组件的核心方法。

#### 代码示例
```jsx
render() {
  const { items } = this.state;
  return (
    <div>
      <h1>待办事项</h1>
      <ul>
        {items.map(item => (
          <li key={item.id}>{item.text}</li>
        ))}
      </ul>
      {items.length === 0 && <p>暂无待办事项</p>}
    </div>
  );
}
```

---

### 知识点：componentDidMount

#### 概念
- **是什么**：`componentDidMount` 是组件挂载后（即插入 DOM 树后）立即调用的生命周期方法。
- **为什么需要**：它提供了组件已渲染到真实 DOM 的时机，适合执行依赖于 DOM 的操作或发起网络请求。
- **解决什么问题**：解决了在组件渲染完成后执行副作用的需求，确保 DOM 节点已存在，避免因 DOM 未就绪导致的错误。

#### 原理
- **底层实现**：在 React 的提交阶段（commit phase），当组件对应的 DOM 节点被插入容器后，React 会调用 `componentDidMount`。该方法仅在客户端执行，在服务器端渲染（SSR）时不会触发。
- **工作流程**：
    1. React 完成初次渲染，将元素树渲染为真实 DOM。
    2. React 调用 `componentDidMount` 钩子。
    3. 开发者可以在该方法中执行异步操作、设置订阅或直接操作 DOM。
- **关键技术点**：此时可以安全地访问 `refs`，因为 DOM 已存在。

#### 使用方法
- **基本用法**：
  ```jsx
  componentDidMount() {
    // 发送请求
    fetchData().then(data => this.setState({ data }));
    // 设置定时器
    this.timerID = setInterval(() => this.tick(), 1000);
    // 操作 DOM
    this.node.scrollIntoView();
  }
  ```
- **常见场景**：
  - 发起 Ajax 请求获取初始数据。
  - 添加事件监听（如 `window.addEventListener('resize', this.handleResize)`）。
  - 初始化第三方 DOM 库（如 D3、Chart.js）。
  - 设置定时器或订阅。
- **最佳实践**：
  - 在此处调用 `setState` 会触发额外渲染，但用户通常感知不到，应谨慎使用以避免性能问题。
  - 如果订阅了外部数据源，记得在 `componentWillUnmount` 中取消订阅。

#### 注意事项
- **常见错误**：
  - 忘记在 `componentWillUnmount` 中清理订阅或定时器，导致内存泄漏。
  - 在 `componentDidMount` 中同步调用 `setState` 导致不必要的重新渲染。
- **性能问题**：如果 `componentDidMount` 中执行耗时操作，可能会延迟用户交互（如动画），应尽量异步处理。
- **兼容性**：所有 React 版本均支持，是稳定可靠的生命周期。

#### 代码示例
```jsx
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
    return <div>{this.state.date.toLocaleTimeString()}</div>;
  }
}
```

---

### 知识点：componentDidUpdate

#### 概念
- **是什么**：`componentDidUpdate` 是组件更新完成后（DOM 更新后）立即调用的生命周期方法。首次渲染不会执行。
- **为什么需要**：允许在组件更新后执行 DOM 操作、发起网络请求（基于 props 变化）或处理更新后的状态。
- **解决什么问题**：解决了需要响应 props 或 state 变化而执行副作用的需求，并提供了之前 props 和 state 的引用用于比较。

#### 原理
- **底层实现**：在每次更新（包括 `setState` 引起的更新和父组件重新渲染导致的 props 更新）的提交阶段，React 在更新真实 DOM 后调用 `componentDidUpdate`。
- **工作流程**：
    1. 组件因 `props` 变化、`setState` 或 `forceUpdate` 触发重新渲染。
    2. 经过协调阶段，React 将变更应用到 DOM。
    3. React 调用 `componentDidUpdate(prevProps, prevState, snapshot)`，传入更新前的 props、state 以及 `getSnapshotBeforeUpdate` 返回的 snapshot 值（如果有）。
- **关键技术点**：可选的第三个参数 `snapshot` 仅在使用 `getSnapshotBeforeUpdate` 时才会有值。

#### 使用方法
- **基本用法**：
  ```jsx
  componentDidUpdate(prevProps, prevState) {
    // 典型用法：比较 props 变化后发起请求
    if (this.props.userID !== prevProps.userID) {
      this.fetchData(this.props.userID);
    }
  }
  ```
- **常见场景**：
  - 根据新 props 更新数据（如用户 ID 变化后重新获取详情）。
  - 操作更新后的 DOM（如基于新内容调整滚动位置）。
  - 与第三方库集成，当数据变化时更新库的配置。
- **最佳实践**：
  - 必须将更新逻辑包裹在条件语句中，避免无限循环（例如在 `componentDidUpdate` 中调用 `setState` 且没有条件判断）。
  - 如果需要基于 DOM 更新前捕获信息（如滚动位置），配合 `getSnapshotBeforeUpdate` 使用。

#### 注意事项
- **常见错误**：
  - 在 `componentDidUpdate` 中无条件调用 `setState` 导致无限循环。
  - 忽略 `prevProps` 比较，导致每次更新都执行相同操作，浪费性能。
- **性能问题**：`componentDidUpdate` 中的操作可能引起额外渲染，应尽量优化（如使用条件判断）。
- **兼容性**：所有 React 版本均支持，参数 `snapshot` 在 React 16.3 后引入。

#### 代码示例
```jsx
class ScrollToTop extends React.Component {
  componentDidUpdate(prevProps) {
    // 当路径变化时，滚动到顶部
    if (this.props.location.pathname !== prevProps.location.pathname) {
      window.scrollTo(0, 0);
    }
  }

  render() {
    return this.props.children;
  }
}
```

---

### 知识点：componentWillUnmount

#### 概念
- **是什么**：`componentWillUnmount` 是组件卸载及销毁之前直接调用的生命周期方法。
- **为什么需要**：提供清理机会，防止内存泄漏，确保组件移除后不再执行不必要的操作。
- **解决什么问题**：解决因组件销毁而残留的定时器、网络请求、事件监听等导致的内存泄漏问题。

#### 原理
- **底层实现**：当组件从 DOM 中移除时，React 在提交阶段调用此方法，随后组件实例将被销毁，不再可用。
- **工作流程**：
    1. 组件因条件渲染、路由切换等原因被移除。
    2. React 调用 `componentWillUnmount`。
    3. 开发者执行清理逻辑，如取消订阅、清除定时器。
    4. 组件实例被销毁，其状态和 DOM 节点被垃圾回收。
- **关键技术点**：在此方法中不应调用 `setState`，因为组件将不会重新渲染。

#### 使用方法
- **基本用法**：
  ```jsx
  componentWillUnmount() {
    clearInterval(this.timerID);
    window.removeEventListener('resize', this.handleResize);
    this.unsubscribe && this.unsubscribe(); // 取消订阅
  }
  ```
- **常见场景**：
  - 清除定时器（`setInterval`、`setTimeout`）。
  - 取消网络请求（如取消 fetch 或 axios 的 abort）。
  - 移除全局事件监听。
  - 清理第三方库创建的实例。
- **最佳实践**：
  - 确保在 `componentDidMount` 中添加的订阅、监听、定时器都有对应的清理逻辑。
  - 对于可能未完成的异步操作，使用标志位或取消机制避免在组件卸载后调用 `setState`。

#### 注意事项
- **常见错误**：
  - 忘记清理定时器或监听，导致组件卸载后回调依然执行，引发错误或内存泄漏。
  - 在 `componentWillUnmount` 中调用 `setState` 导致 React 警告。
- **性能问题**：及时清理可以释放资源，避免不必要的回调执行，间接提升性能。
- **兼容性**：所有 React 版本均支持，是稳定的清理钩子。

#### 代码示例
```jsx
class Timer extends React.Component {
  componentDidMount() {
    this.timer = setInterval(this.tick, 1000);
  }

  componentWillUnmount() {
    clearInterval(this.timer);
  }

  tick = () => {
    console.log('tick');
  }

  render() {
    return <div>Timer 运行中...</div>;
  }
}
```

---

### 知识点：shouldComponentUpdate

#### 概念
- **是什么**：`shouldComponentUpdate` 是一个生命周期方法，在渲染之前调用，用于决定是否允许组件重新渲染。
- **为什么需要**：通过控制重新渲染的条件，优化性能，避免不必要的虚拟 DOM 比较和渲染。
- **解决什么问题**：解决因父组件频繁更新或 `setState` 调用导致的无意义渲染问题，提升应用性能。

#### 原理
- **底层实现**：当组件的 props 或 state 发生变化时，React 会在更新阶段调用 `shouldComponentUpdate`，传入新的 `nextProps` 和 `nextState`。如果返回 `false`，则 React 会跳过该次更新（包括 `render` 及之后的生命周期），但不会影响子组件的状态。
- **工作流程**：
    1. 组件接收到新 props 或调用了 `setState`。
    2. React 调用 `shouldComponentUpdate` 进行询问。
    3. 若返回 `true`（默认），继续更新流程；若返回 `false`，则停止更新。
- **关键技术点**：
  - 首次渲染或调用 `forceUpdate` 时不会触发此方法。
  - 仅作为性能优化手段，不应依赖它来阻止渲染，因为可能导致 bug。

#### 使用方法
- **基本用法**：
  ```jsx
  shouldComponentUpdate(nextProps, nextState) {
    // 仅当 props 或 state 的某个属性变化时才更新
    return nextProps.value !== this.props.value || nextState.count !== this.state.count;
  }
  ```
- **常见场景**：
  - 实现自定义的浅比较（shallow compare），优化大量数据的组件。
  - 与不可变数据配合，判断数据是否真正改变。
- **最佳实践**：
  - 优先考虑使用 `React.PureComponent`（自动进行浅比较）代替手动编写。
  - 返回 `false` 不会阻止子组件在其自身状态变化时的重新渲染。
  - 如果数据结构较深，考虑使用不可变数据或配合 `shouldComponentUpdate` 进行深度比较（但深度比较可能影响性能）。

#### 注意事项
- **常见错误**：
  - 返回 `false` 但实际上需要更新，导致 UI 不一致。
  - 在方法中进行复杂比较，拖慢更新判断本身。
- **性能问题**：`shouldComponentUpdate` 本身也是开销，应保持轻量。使用 `PureComponent` 可自动优化。
- **兼容性**：所有 React 版本均支持。

#### 代码示例
```jsx
class ListItem extends React.Component {
  shouldComponentUpdate(nextProps) {
    // 仅当 item 的 id 或选中状态变化时才更新
    return nextProps.item.id !== this.props.item.id ||
           nextProps.isSelected !== this.props.isSelected;
  }

  render() {
    const { item, isSelected } = this.props;
    return (
      <li className={isSelected ? 'selected' : ''}>
        {item.name}
      </li>
    );
  }
}
```

---

### 知识点：static getDerivedStateFromProps

#### 概念
- **是什么**：`static getDerivedStateFromProps` 是一个静态生命周期方法，在调用 `render` 之前调用，包括初始挂载和后续更新。
- **为什么需要**：用于让组件的 `state` 能够根据 `props` 的变化而更新，即“派生状态”。
- **解决什么问题**：解决了在 props 变化时需要同步更新 state 的场景，替代了已废弃的 `componentWillReceiveProps`。

#### 原理
- **底层实现**：它是一个静态方法，无法访问 `this`。React 在渲染阶段调用它，接收最新的 `props` 和当前的 `state`，应返回一个对象用于更新 state，或返回 `null` 表示不更新。
- **工作流程**：
    1. 组件挂载或更新时，React 调用 `getDerivedStateFromProps`。
    2. 方法返回的对象会与当前 state 合并（浅合并）。
    3. 随后调用 `render`。
- **关键技术点**：该方法是静态的，不能访问组件实例，因此不能调用 `this.setState` 或 `this.props`。它的存在应尽量少用，因为容易导致代码复杂。

#### 使用方法
- **基本用法**：
  ```jsx
  static getDerivedStateFromProps(props, state) {
    // 当传入的 userId 变化时，重置状态
    if (props.userId !== state.prevUserId) {
      return {
        prevUserId: props.userId,
        messages: []
      };
    }
    return null;
  }
  ```
- **常见场景**：
  - 组件 state 依赖于 props 的变化，例如表格在筛选条件变化时重置选中项。
  - 实现类似“受控组件”但需要在内部缓存 props 的场景。
- **最佳实践**：
  - 尽量少用。大多数情况下，你需要的是：完全受控组件、完全非受控组件，或在 `componentDidUpdate` 中处理副作用。
  - 如果使用，必须配合存储上一次的 props 值（如上述代码中的 `prevUserId`），以便比较。
  - 保持方法纯净，不应产生副作用。

#### 注意事项
- **常见错误**：
  - 滥用该方法导致状态同步逻辑复杂且易错。
  - 忘记比较新旧 props，每次更新都返回新对象，造成不必要的渲染。
- **性能问题**：此方法在每次渲染前都会调用，应保持轻量。
- **兼容性**：React 16.3 引入，替代了 `componentWillReceiveProps`。

#### 代码示例
```jsx
class EmailInput extends React.Component {
  state = {
    email: this.props.defaultEmail,
    prevPropsEmail: this.props.defaultEmail
  };

  static getDerivedStateFromProps(props, state) {
    // 当 defaultEmail 变化时更新 email
    if (props.defaultEmail !== state.prevPropsEmail) {
      return {
        prevPropsEmail: props.defaultEmail,
        email: props.defaultEmail
      };
    }
    return null;
  }

  handleChange = (e) => {
    this.setState({ email: e.target.value });
  };

  render() {
    return <input value={this.state.email} onChange={this.handleChange} />;
  }
}
```

---

### 知识点：getSnapshotBeforeUpdate

#### 概念
- **是什么**：`getSnapshotBeforeUpdate` 在最近一次渲染输出（提交到 DOM 节点）之前调用。它使得组件能在发生更改之前从 DOM 中捕获一些信息（例如滚动位置）。
- **为什么需要**：在 DOM 更新前获取当前 DOM 状态，以便在更新后恢复到正确的位置。
- **解决什么问题**：解决在异步渲染中，需要在 DOM 变化前获取快照的场景，例如聊天界面维持滚动位置。

#### 原理
- **底层实现**：在 React 的提交阶段，在变更 DOM 之前，同步调用 `getSnapshotBeforeUpdate`。它的返回值将作为第三个参数传递给 `componentDidUpdate`。
- **工作流程**：
    1. 组件更新，`render` 被调用生成新的虚拟 DOM。
    2. React 准备提交更新到真实 DOM，但在修改 DOM 前，调用 `getSnapshotBeforeUpdate(prevProps, prevState)`。
    3. 开发者可以在此访问旧 DOM（如 `this.listRef.current.scrollTop`），返回一个快照值（任意类型）。
    4. React 更新 DOM。
    5. 调用 `componentDidUpdate(prevProps, prevState, snapshot)`，将快照传入。
- **关键技术点**：该方法与 `componentDidUpdate` 配合使用，用于处理需要基于更新前 DOM 状态调整更新后状态的场景。

#### 使用方法
- **基本用法**：
  ```jsx
  getSnapshotBeforeUpdate(prevProps, prevState) {
    if (prevProps.list.length < this.props.list.length) {
      const list = this.listRef.current;
      return list.scrollHeight - list.scrollTop;
    }
    return null;
  }

  componentDidUpdate(prevProps, prevState, snapshot) {
    if (snapshot !== null) {
      const list = this.listRef.current;
      list.scrollTop = list.scrollHeight - snapshot;
    }
  }
  ```
- **常见场景**：
  - 在列表添加新项时保持当前可见内容的滚动位置。
  - 捕获更新前文本框的焦点位置，更新后恢复。
- **最佳实践**：
  - 仅在确实需要读取 DOM 信息并在更新后使用的场景中使用。
  - 快照可以是任何值，通常为数字或对象。

#### 注意事项
- **常见错误**：
  - 未在 `componentDidUpdate` 中处理快照，导致捕获信息丢失。
  - 在 `getSnapshotBeforeUpdate` 中修改 DOM，应只读取不修改。
- **性能问题**：此方法是同步执行的，应保持轻量。
- **兼容性**：React 16.3 引入，用于替代 `componentWillUpdate` 的部分功能。

#### 代码示例
```jsx
class ScrollingList extends React.Component {
  constructor(props) {
    super(props);
    this.listRef = React.createRef();
  }

  getSnapshotBeforeUpdate(prevProps, prevState) {
    // 如果新消息添加，捕获当前滚动位置
    if (prevProps.messages.length < this.props.messages.length) {
      const list = this.listRef.current;
      return list.scrollHeight - list.scrollTop;
    }
    return null;
  }

  componentDidUpdate(prevProps, prevState, snapshot) {
    // 根据快照恢复滚动位置，使列表保持在相同位置
    if (snapshot !== null) {
      const list = this.listRef.current;
      list.scrollTop = list.scrollHeight - snapshot;
    }
  }

  render() {
    return (
      <div ref={this.listRef}>
        {this.props.messages.map(msg => <div key={msg.id}>{msg.text}</div>)}
      </div>
    );
  }
}
```

---

### 知识点：componentDidCatch

#### 概念
- **是什么**：`componentDidCatch` 是一个生命周期方法，在后代组件抛出错误时被调用，用于捕获错误并执行副作用（如日志记录）。
- **为什么需要**：配合 `static getDerivedStateFromError` 一起构成错误边界（Error Boundaries），防止整个应用崩溃，并提供降级 UI。
- **解决什么问题**：解决 React 组件树中某一部分的 JavaScript 错误导致整个应用白屏的问题，实现局部错误的隔离和优雅处理。

#### 原理
- **底层实现**：`componentDidCatch` 在提交阶段调用，接收两个参数：`error`（抛出的错误）和 `info`（带有 `componentStack` 属性的对象，包含组件栈信息）。它会捕获子组件（包括所有后代）在生命周期、渲染、构造函数中抛出的错误。
- **工作流程**：
    1. 子组件在渲染或生命周期中抛出错误。
    2. 错误冒泡到最近的错误边界组件。
    3. 首先调用 `static getDerivedStateFromError` 更新状态（显示降级 UI）。
    4. 随后调用 `componentDidCatch` 记录错误信息。
- **关键技术点**：错误边界只能捕获子组件树的错误，不能捕获自身内部的错误。也不能捕获事件处理器中的错误（需用 `try/catch`）。

#### 使用方法
- **基本用法**：
  ```jsx
  componentDidCatch(error, info) {
    // 将错误记录到监控服务
    logErrorToService(error, info.componentStack);
  }
  ```
- **常见场景**：
  - 将错误上报到 Sentry 等日志系统。
  - 在开发环境下输出详细错误信息。
- **最佳实践**：
  - 通常与 `static getDerivedStateFromError` 一起使用，后者用于渲染备用 UI。
  - 错误边界应放置在应用中合适的层级，如根组件或路由级别，以捕获不同范围的错误。

#### 注意事项
- **常见错误**：
  - 在 `componentDidCatch` 中调用 `setState` 导致无限循环，应确保更新是条件性的（通常搭配 `getDerivedStateFromError` 使用）。
  - 试图捕获事件处理中的错误（错误边界不处理事件处理器内的错误）。
- **性能问题**：错误处理本身不影响正常渲染，但记录错误可能涉及网络请求，应异步处理。
- **兼容性**：React 16 引入，是错误边界的核心方法。

#### 代码示例
```jsx
class ErrorBoundary extends React.Component {
  constructor(props) {
    super(props);
    this.state = { hasError: false };
  }

  static getDerivedStateFromError(error) {
    // 更新 state 使下次渲染显示降级 UI
    return { hasError: true };
  }

  componentDidCatch(error, info) {
    // 记录错误信息
    console.error('捕获错误:', error, info.componentStack);
    // 可上报到服务器
    reportError(error, info);
  }

  render() {
    if (this.state.hasError) {
      return <h1>出错了，请稍后重试。</h1>;
    }
    return this.props.children;
  }
}

// 使用
<ErrorBoundary>
  <MyWidget />
</ErrorBoundary>
```

---

### 知识点：static getDerivedStateFromError

#### 概念
- **是什么**：`static getDerivedStateFromError` 是一个静态生命周期方法，在后代组件抛出错误后被调用，用于更新状态以显示降级 UI。
- **为什么需要**：它是错误边界的一部分，用于响应错误并渲染备用界面。
- **解决什么问题**：在发生错误时，通过更新状态让组件可以渲染备用的 UI，避免整个应用崩溃。

#### 原理
- **底层实现**：它是一个静态方法，接收错误对象作为参数，应返回一个对象以更新 state。React 在渲染阶段调用它，不允许副作用。
- **工作流程**：
    1. 子组件抛出错误。
    2. React 调用最近的错误边界的 `getDerivedStateFromError`。
    3. 返回的对象与当前 state 合并，触发重新渲染。
    4. 然后调用 `componentDidCatch`。
- **关键技术点**：因为是静态方法，无法访问 `this`，只能返回 state 更新对象。

#### 使用方法
- **基本用法**：
  ```jsx
  static getDerivedStateFromError(error) {
    return { hasError: true };
  }
  ```
- **常见场景**：
  - 配合 `componentDidCatch` 实现完整的错误边界。
- **最佳实践**：
  - 仅用于显示降级 UI，不要在此处执行日志记录等副作用。
  - 可以返回包含错误信息的 state，以便在备用 UI 中展示给用户。

#### 注意事项
- **常见错误**：
  - 在此方法中执行副作用（如发送日志），应放在 `componentDidCatch` 中。
  - 返回的 state 更新可能被后续正常更新覆盖，需确保每次错误都能重新触发。
- **性能问题**：无显著性能影响。
- **兼容性**：React 16.6 开始稳定支持。

#### 代码示例
```jsx
class ErrorBoundary extends React.Component {
  state = { error: null };

  static getDerivedStateFromError(error) {
    return { error };
  }

  render() {
    if (this.state.error) {
      return <div>发生错误：{this.state.error.message}</div>;
    }
    return this.props.children;
  }
}
```

---

### 知识点：setState

#### 概念
- **是什么**：`setState` 是 React 组件中用于更新状态（state）的核心 API，它会触发组件及其子组件的重新渲染。
- **为什么需要**：状态是 React 组件的动态数据，更新状态需要通知 React 重新渲染 UI，`setState` 提供了安全、高效的状态更新机制。
- **解决什么问题**：解决了直接修改 `this.state` 不会触发渲染的问题，确保状态变化与 UI 同步。

#### 原理
- **底层实现**：`setState` 会将传入的状态更新放入一个队列中，React 会对多次 `setState` 调用进行批处理（batch），然后合并更新，最后触发协调过程更新 UI。
- **工作流程**：
    1. 调用 `setState(updater, callback)`。
    2. React 将更新加入队列，并在合适的时机执行批量更新。
    3. 在事件处理函数和生命周期中，React 会自动批处理；在异步代码（如 `setTimeout`）中，则不会批处理。
    4. 更新完成后，如果提供了 `callback`，则会执行。
- **关键技术点**：
  - `setState` 可能是异步的，不要依赖当前 state 计算下一个 state，应使用函数式更新。
  - 更新会浅合并（shallow merge）对象。

#### 使用方法
- **基本用法**：
  ```jsx
  // 对象式
  this.setState({ count: this.state.count + 1 });

  // 函数式（推荐）
  this.setState((prevState, props) => ({
    count: prevState.count + 1
  }));
  ```
- **常见场景**：
  - 响应用户输入更新表单值。
  - 处理异步数据返回后更新展示。
  - 切换 UI 状态（如 loading、折叠）。
- **最佳实践**：
  - 始终使用函数式 `setState` 当新状态依赖于旧状态时，以避免异步问题。
  - 避免在 `setState` 中深度合并，应保持状态扁平化。
  - 将不直接用于渲染的数据放在实例属性上（如 `this.timer`），而不是 state 中。

#### 注意事项
- **常见错误**：
  - 直接修改 `this.state` 然后调用 `setState` 导致意外行为，应始终提供新的对象。
  - 在 `setState` 后立即读取 `this.state` 可能得到旧值，应使用回调函数或 `componentDidUpdate`。
- **性能问题**：频繁调用 `setState` 可能导致性能下降，应合理设计状态结构，避免不必要的更新。
- **兼容性**：所有 React 版本均支持。

#### 代码示例
```jsx
class Counter extends React.Component {
  state = { count: 0 };

  increment = () => {
    this.setState(prev => ({ count: prev.count + 1 }), () => {
      console.log('更新后计数：', this.state.count);
    });
  };

  render() {
    return <button onClick={this.increment}>{this.state.count}</button>;
  }
}
```

---

### 知识点：forceUpdate

#### 概念
- **是什么**：`forceUpdate` 是 React 组件的一个方法，强制组件重新渲染，即使 `props` 和 `state` 没有变化。
- **为什么需要**：在某些特殊情况下，当组件依赖于 `props` 和 `state` 之外的数据（如实例属性）且这些数据变化需要更新 UI 时，可以使用 `forceUpdate`。
- **解决什么问题**：解决脱离 React 数据流（非 `props`/`state`）的更新需求。

#### 原理
- **底层实现**：调用 `forceUpdate` 会使组件跳过 `shouldComponentUpdate`，直接调用 `render`，然后正常更新子组件。
- **工作流程**：
    1. 调用 `this.forceUpdate()`。
    2. React 跳过 `shouldComponentUpdate`，直接进入 `render` 阶段。
    3. 后续生命周期（除 `shouldComponentUpdate`）正常执行。
- **关键技术点**：`forceUpdate` 会触发子组件的正常生命周期，包括 `shouldComponentUpdate`（如果子组件有的话）。

#### 使用方法
- **基本用法**：
  ```jsx
  handleDataChange = () => {
    // 某些外部数据变化，需要更新视图
    this.forceUpdate();
  };
  ```
- **常见场景**：
  - 使用了非 React 的数据源（如全局事件总线、Redux 外部存储但未通过 props 传递）且希望更新。
  - 与第三方库集成，库内部修改了数据但未通过 props 传递。
- **最佳实践**：
  - 尽量避免使用 `forceUpdate`，优先考虑将外部数据源纳入 React 的数据流（如使用 context 或状态管理库）。
  - 如果必须使用，确保理解其影响，并配合 `shouldComponentUpdate` 可能导致的跳过行为。

#### 注意事项
- **常见错误**：
  - 滥用 `forceUpdate` 导致代码难以维护和性能问题。
  - 忘记 `forceUpdate` 不会影响子组件的 `shouldComponentUpdate` 逻辑。
- **性能问题**：强制渲染可能会造成不必要的渲染，应谨慎使用。
- **兼容性**：所有 React 版本均支持。

#### 代码示例
```jsx
class ExternalDataComponent extends React.Component {
  componentDidMount() {
    window.dataStore.subscribe(this.handleDataChange);
  }

  componentWillUnmount() {
    window.dataStore.unsubscribe(this.handleDataChange);
  }

  handleDataChange = () => {
    // 外部数据变化，强制重新渲染
    this.forceUpdate();
  };

  render() {
    // 直接从外部数据源读取最新数据渲染
    return <div>{window.dataStore.getData()}</div>;
  }
}
```

---

### 知识点：defaultProps

#### 概念
- **是什么**：`defaultProps` 是 React 组件的一个静态属性，用于定义 props 的默认值。
- **为什么需要**：当父组件未传递某个 prop 或传递的值为 `undefined` 时，使用默认值可以保证组件行为正常，避免因缺失 props 导致的错误。
- **解决什么问题**：解决 props 缺失时组件需要回退默认值的需求，提升组件的健壮性和易用性。

#### 原理
- **底层实现**：React 在解析组件时会检查 `defaultProps`，并在 props 为 `undefined` 时将其设置为默认值。如果传递了 `null`，则不会使用默认值（`null` 被视为显式空值）。
- **工作流程**：
    1. 定义组件的 `defaultProps` 对象。
    2. 组件实例化时，React 会合并 props：如果某个 prop 值为 `undefined`，则从 `defaultProps` 中取对应值。
- **关键技术点**：`defaultProps` 可以在类上作为静态属性定义，也可以在函数组件上直接设置。

#### 使用方法
- **基本用法**：
  ```jsx
  class Button extends React.Component {
    static defaultProps = {
      color: 'blue',
      disabled: false
    };

    render() {
      return <button style={{ color: this.props.color }} disabled={this.props.disabled}>{this.props.children}</button>;
    }
  }
  ```
- **常见场景**：
  - 为可选 props 提供默认值，如样式、回调函数的默认行为。
  - 确保某些必选 props 未传递时组件不会崩溃。
- **最佳实践**：
  - 默认值应合理，避免产生意料之外的行为。
  - 与 PropTypes 结合使用，可提供类型检查并标记必需 props。

#### 注意事项
- **常见错误**：
  - 混淆 `defaultProps` 和初始 state：state 的初始值可以基于 props 但应通过 `constructor` 或 `getDerivedStateFromProps` 处理。
  - 认为 `defaultProps` 会处理 `null`，实际上只有 `undefined` 会被替换。
- **性能问题**：无显著性能影响。
- **兼容性**：所有 React 版本均支持。

#### 代码示例
```jsx
class Greeting extends React.Component {
  static defaultProps = {
    name: '陌生人'
  };

  render() {
    return <h1>你好，{this.props.name}！</h1>;
  }
}

// 使用：<Greeting /> 显示 "你好，陌生人！"
// <Greeting name="张三" /> 显示 "你好，张三！"
```

---

### 知识点：displayName

#### 概念
- **是什么**：`displayName` 是 React 组件的一个可选属性，用于设置调试时显示的组件名称。
- **为什么需要**：在 React 开发者工具中，组件默认会显示为定义时的名称（如 `class MyComponent`），但当使用高阶组件（HOC）时，组件名称可能被混淆，设置 `displayName` 可以改善调试体验。
- **解决什么问题**：解决高阶组件包装后组件名称丢失或难以识别的问题，方便开发者定位问题。

#### 原理
- **底层实现**：React 在渲染树和开发者工具中优先使用 `displayName` 属性作为组件显示名称，如果没有则回退到函数名或类名。
- **工作流程**：
    1. 定义组件时设置 `displayName` 静态属性。
    2. 当 React 开发者工具展示组件树时，读取该属性并显示。
- **关键技术点**：通常用于高阶组件中，将 `displayName` 设置为 `HOCName(ComponentName)` 的形式。

#### 使用方法
- **基本用法**：
  ```jsx
  class MyComponent extends React.Component {
    static displayName = 'CustomName';
    render() {
      return <div>Hello</div>;
    }
  }
  ```
- **常见场景**：
  - 高阶组件（HOC）中，为返回的组件设置 `displayName`。
  - 为匿名函数组件设置名称以便调试。
- **最佳实践**：
  - 在高阶组件中，始终设置 `displayName` 以便追踪原始组件。
  - 格式通常为：`withHOC(ComponentName)`。

#### 注意事项
- **常见错误**：忘记设置 `displayName` 导致调试时组件名称难以理解。
- **性能问题**：无性能影响。
- **兼容性**：所有 React 版本均支持。

#### 代码示例
```jsx
// 高阶组件示例
function withLogger(WrappedComponent) {
  class WithLogger extends React.Component {
    componentDidMount() {
      console.log('组件挂载');
    }
    render() {
      return <WrappedComponent {...this.props} />;
    }
  }
  WithLogger.displayName = `withLogger(${WrappedComponent.displayName || WrappedComponent.name || 'Component'})`;
  return WithLogger;
}

const EnhancedButton = withLogger(Button);
// 在开发者工具中会显示为 withLogger(Button)
```

---

### 知识点：已废弃的生命周期（UNSAFE_）

#### 概念
- **是什么**：指在 React 16.3 及之后版本中标记为“不安全”的生命周期方法，包括 `UNSAFE_componentWillMount`、`UNSAFE_componentWillReceiveProps` 和 `UNSAFE_componentWillUpdate`。
- **为什么需要**：这些旧方法容易在 React 新的异步渲染架构（Fiber）中被多次调用，导致难以追踪的 bug（如副作用重复执行）。
- **解决什么问题**：官方推荐使用更安全的替代方法，以适应未来并发模式。

#### 原理
- **底层实现**：这些方法原本在渲染阶段执行，可能被 React 的异步渲染打断或多次调用。React 在 16.3 版本为它们添加了 `UNSAFE_` 前缀，并在后续版本中通过警告提示开发者迁移。
- **工作流程**：
  - `UNSAFE_componentWillMount`：在 `render` 之前调用，现在推荐将初始化操作移到 `constructor` 或 `componentDidMount`。
  - `UNSAFE_componentWillReceiveProps(nextProps)`：在组件接收到新 props 时调用，现在推荐使用 `static getDerivedStateFromProps` 或 `componentDidUpdate`。
  - `UNSAFE_componentWillUpdate(nextProps, nextState)`：在 `render` 之前，更新之前调用，现在推荐使用 `getSnapshotBeforeUpdate` 和 `componentDidUpdate`。
- **关键技术点**：这些方法在 React 17+ 中仍然可用（需加 `UNSAFE_` 前缀），但官方强烈建议迁移。

#### 使用方法
- **基本用法**（不推荐）：
  ```jsx
  UNSAFE_componentWillReceiveProps(nextProps) {
    if (nextProps.value !== this.props.value) {
      this.setState({ derivedValue: nextProps.value });
    }
  }
  ```
- **迁移路径**：
  - `UNSAFE_componentWillMount` → 将初始化逻辑移到 `constructor`（非副作用）或 `componentDidMount`（副作用）。
  - `UNSAFE_componentWillReceiveProps` → 使用 `static getDerivedStateFromProps` 或 `componentDidUpdate`。
  - `UNSAFE_componentWillUpdate` → 使用 `getSnapshotBeforeUpdate` + `componentDidUpdate`。

#### 注意事项
- **常见错误**：在旧项目中继续使用这些方法而不迁移，可能导致未来 React 版本中的兼容性问题。
- **性能问题**：异步渲染下这些方法可能被多次调用，引起性能问题或状态不一致。
- **兼容性**：在 React 16.3+ 中，以 `UNSAFE_` 前缀存在，React 18+ 依然支持，但控制台会有警告。

#### 代码示例（演示迁移）
```jsx
// 旧代码
componentWillReceiveProps(nextProps) {
  if (nextProps.userId !== this.props.userId) {
    this.setState({ userId: nextProps.userId });
  }
}

// 新代码
static getDerivedStateFromProps(props, state) {
  if (props.userId !== state.prevUserId) {
    return { prevUserId: props.userId, userId: props.userId };
  }
  return null;
}
```

---

以上是 React 类组件生命周期及相关核心知识点的完整学习笔记，涵盖了每个方法的概念、原理、使用方法、注意事项和代码示例。希望这份资料能帮助你深入理解和掌握 React 类组件的生命周期体系。