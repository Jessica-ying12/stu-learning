## 一、Props 概述

#### 1.1 Props 基本概念

##### 概念
- **是什么**：Props（Properties 的缩写）是 React 组件之间通信的机制，是父组件传递给子组件的数据对象。它可以包含任何 JavaScript 值（数字、字符串、对象、函数等）。
- **为什么需要**：React 应用由多个组件构成，组件之间需要共享数据和功能。Props 提供了一种标准化的方式，让父组件能够向子组件传递数据，实现组件的复用和定制。
- **解决什么问题**：解决了组件间数据传递的问题，使得组件可以像函数一样接收参数，从而根据不同的输入渲染不同的 UI。

##### 原理
- **底层实现**：在 React 内部，当渲染一个组件时，React 会将 JSX 中声明的属性收集成一个对象（即 props 对象），并将其作为参数传递给组件函数（或类组件的实例）。对于函数组件，props 是第一个参数；对于类组件，props 会被挂载到 `this.props` 上。
- **工作流程**：
  1. 父组件在 JSX 中使用子组件，并通过属性传递数据。
  2. React 解析 JSX，生成描述 UI 结构的 React 元素（对象），其中包含了传递给组件的 props。
  3. 当组件渲染时，React 将 props 对象传递给组件函数或类实例，组件内部通过读取 props 来动态生成内容。
  4. 如果父组件的 props 发生变化，React 会重新渲染子组件，并传入新的 props。
- **关键技术点**：单向数据流（数据只能从父组件流向子组件）、不可变性（子组件不能修改 props）、纯函数原则（组件对于相同的 props 应返回相同的 UI）。

##### 使用方法
- **基本用法**：
  ```jsx
  // 函数组件
  function Welcome(props) {
    return <h1>Hello, {props.name}</h1>;
  }
  
  // 类组件
  class Welcome extends React.Component {
    render() {
      return <h1>Hello, {this.props.name}</h1>;
    }
  }
  
  // 使用组件并传递 props
  <Welcome name="Alice" />
  ```
- **常见场景**：
  - 传递静态配置数据（如标题、描述）。
  - 传递用户信息、列表数据等动态数据。
  - 传递回调函数以实现子组件向父组件通信。
  - 通过 `children` 传递嵌套内容。
- **最佳实践**：
  - 使用解构赋值直接读取需要的 props，使代码更简洁。
  - 为 props 提供默认值（通过默认参数或 `defaultProps`）。
  - 对 props 进行类型检查（使用 `propTypes`）以提高健壮性。
  - 避免传递不必要的 props，尤其是使用展开语法时。

##### 注意事项
- **常见错误**：
  - 在子组件中尝试修改 props（会导致不可预期的行为）。
  - 忘记为必传 props 提供值（可能导致组件异常或空白）。
  - 将对象或数组直接作为 props 传递，如果对象是每次渲染新创建的，可能引发不必要的子组件重渲染（可通过 `useMemo`/`useCallback` 优化）。
- **性能问题**：props 变化会导致组件重新渲染，如果 props 中包含大型数据结构或频繁变化的数据，可能影响性能。可使用 `React.memo` 对组件进行浅比较优化。
- **兼容性**：`propTypes` 仅在开发模式下生效，生产环境中会被自动移除，不影响性能。

##### 代码示例
```jsx
// 示例：展示用户信息
function UserCard({ name, age, email }) {
  return (
    <div className="card">
      <h2>{name}</h2>
      <p>Age: {age}</p>
      <p>Email: {email}</p>
    </div>
  );
}

function App() {
  const user = { name: '张三', age: 30, email: 'zhangsan@example.com' };
  return <UserCard name={user.name} age={user.age} email={user.email} />;
}
```

---

## 二、传递数据（父组件向子组件传递 Props）

#### 2.1 通过 Props 传递数据

##### 概念
- **是什么**：这是 Props 的核心功能，即父组件通过 JSX 属性将数据传递给子组件，子组件通过 props 对象接收。
- **为什么需要**：为了实现组件之间的数据共享，让子组件能够根据父组件提供的数据进行渲染。
- **解决什么问题**：解决了组件间数据依赖的问题，使得组件可以独立复用，同时又能从外部接收配置。

##### 原理
- **底层实现**：当 React 渲染父组件时，它会解析 JSX 中的子组件标签，将标签上的属性收集为一个对象。然后，React 在创建子组件的实例时，将这个对象作为 props 传入。在更新阶段，如果父组件重新渲染，React 会对比新旧 props，如果有变化，则触发子组件的更新。
- **工作流程**：
  1. 父组件执行渲染，生成包含子组件的虚拟 DOM。
  2. React 将父组件传递给子组件的属性打包成 props 对象。
  3. 子组件接收 props 并使用它们生成自己的虚拟 DOM。
  4. React 最终将虚拟 DOM 映射为真实 DOM。
- **关键技术点**：属性展开、children prop、不同类型数据的传递（函数、对象、数组等）。

##### 使用方法
- **基本用法**：
  ```jsx
  function Child(props) {
    return <div>{props.message}</div>;
  }
  
  function Parent() {
    return <Child message="Hello from parent" />;
  }
  ```
- **常见场景**：
  - 传递字符串、数字、布尔值：`<Button disabled={true}>Click</Button>`
  - 传递对象和数组：`<List data={items} />`
  - 传递函数：`<Input onChange={handleChange} />`
  - 传递 React 元素：`<Wrapper>{<span>content</span>}</Wrapper>`（通过 children）
- **最佳实践**：
  - 使用解构语法明确组件需要的 props。
  - 对于布尔值，可以省略值为 `true` 的属性，如 `<Input disabled />`。
  - 将复杂的配置对象作为单个 prop 传递，而不是拆分成多个基础类型 props，以简化组件接口。

##### 注意事项
- **常见错误**：
  - 拼写错误导致 props 未正确传递（React 不会报错，只会得到 `undefined`）。
  - 传递未定义的变量，导致子组件接收到 `undefined`。
  - 误用展开语法传递整个父组件的 props，可能传递了父组件内部的状态或方法，引发不必要的耦合。
- **性能问题**：如果传递的对象或函数在每次父组件渲染时都重新创建，子组件即使使用 `React.memo` 也无法跳过渲染，因为 props 的引用变化了。可以使用 `useMemo` 和 `useCallback` 进行优化。
- **兼容性**：所有 React 版本均支持，无特殊兼容性问题。

##### 代码示例
```jsx
// 传递各种类型数据
function Profile({ user, onFollow, isActive, children }) {
  return (
    <div>
      <h1>{user.name}</h1>
      {isActive && <span>Active</span>}
      <button onClick={() => onFollow(user.id)}>Follow</button>
      <div>{children}</div>
    </div>
  );
}

function App() {
  const user = { id: 1, name: 'Alice' };
  const handleFollow = (userId) => console.log('Follow', userId);
  return (
    <Profile user={user} onFollow={handleFollow} isActive>
      <p>Additional info</p>
    </Profile>
  );
}
```

---

## 三、Props 的只读性

#### 3.1 Props 的不可变性

##### 概念
- **是什么**：Props 是只读的，组件内部不能修改传入的 props。这意味着组件对于给定的 props，应该始终返回相同的 UI。
- **为什么需要**：确保数据流的可预测性。如果子组件可以随意修改 props，会导致应用状态混乱，难以调试。React 借鉴了函数式编程中的“纯函数”概念。
- **解决什么问题**：防止数据被意外修改，保证单向数据流的完整性，使应用更易于理解和维护。

##### 原理
- **底层实现**：React 在内部并不会强制执行 props 的不可变性（JavaScript 对象本身是可变的），而是通过设计约定和开发规范来约束开发者。在开发模式下，React 也不会主动阻止修改，但修改后的 props 不会触发组件重新渲染，且会导致后续渲染逻辑错误。React 官方文档明确要求不要修改 props。
- **工作流程**：
  1. 父组件传递 props 给子组件。
  2. 子组件接收 props 并用于渲染。
  3. 如果子组件尝试修改 props（如 `props.name = 'new'`），该修改只会在子组件内部生效，不会影响到父组件或其他地方，且 React 不会检测到 props 变化而重新渲染子组件，导致 UI 与数据不一致。
- **关键技术点**：纯函数原则、不可变数据、状态提升（当需要修改数据时，应将数据提升到父组件的 state 中，并通过回调通知父组件修改）。

##### 使用方法
- **基本用法**：只需记住“不要修改 props”。将 props 视为只读输入。
- **常见场景**：
  - 当需要基于 props 的初始值维护内部可变数据时，应使用 state。
  - 当需要修改数据时，通过父组件传递的回调函数请求父组件更新 state，从而实现数据流动。
- **最佳实践**：
  - 避免对 props 对象进行任何赋值操作。
  - 使用 `const { prop1, prop2 } = props` 解构，防止误操作。
  - 如果需要对 props 的值进行转换，可以在组件内部定义新变量，而不是修改原 prop。

##### 注意事项
- **常见错误**：
  - 直接修改 props，如 `props.count++`（虽然不报错，但会导致 bug）。
  - 在子组件中将 props 赋值给 state 但忘记在 props 更新时同步（可以通过 `useEffect` 监听 props 变化来更新 state）。
- **性能问题**：不涉及直接性能问题，但错误使用会导致渲染不一致。
- **兼容性**：所有 React 版本通用。

##### 代码示例
```jsx
// 错误示例：直接修改 props
function BadCounter(props) {
  props.count++; // 不要这样做！
  return <div>{props.count}</div>;
}

// 正确示例：使用 state 管理内部变化
function GoodCounter({ initialCount }) {
  const [count, setCount] = useState(initialCount);
  return (
    <div>
      <p>{count}</p>
      <button onClick={() => setCount(count + 1)}>Increment</button>
    </div>
  );
}
```

---

## 四、默认属性：defaultProps

#### 4.1 设置默认 Props

##### 概念
- **是什么**：`defaultProps` 是一个静态属性，用于为组件的 props 定义默认值。当父组件没有传递某个 prop 时，组件会使用默认值。
- **为什么需要**：提高组件的健壮性，避免因缺少某些 props 而导致组件异常或空白显示。
- **解决什么问题**：解决了 props 缺失时的后备显示问题，同时让组件的使用更灵活（部分 props 可省略）。

##### 原理
- **底层实现**：在组件渲染前，React 会将组件的 `defaultProps` 对象与传入的 props 对象进行合并。如果传入的 props 中没有某个属性，则从 `defaultProps` 中获取对应值。合并过程是浅合并。
- **工作流程**：
  1. 定义组件的 `defaultProps`。
  2. 使用组件时，父组件传入 props（可能部分缺失）。
  3. React 在调用组件函数或创建类实例前，将 `defaultProps` 与传入的 props 合并，生成最终的 props 对象。
  4. 组件内部接收的 props 已包含默认值。
- **关键技术点**：函数组件可以直接在参数解构时赋予默认值（推荐），类组件使用 `static defaultProps` 或 `ComponentName.defaultProps`。

##### 使用方法
- **基本用法**：
  - **函数组件（推荐）**：
    ```jsx
    function Button({ type = 'button', children }) {
      return <button type={type}>{children}</button>;
    }
    ```
  - **函数组件 + defaultProps**：
    ```jsx
    function Button({ type, children }) {
      return <button type={type}>{children}</button>;
    }
    Button.defaultProps = { type: 'button' };
    ```
  - **类组件**：
    ```jsx
    class Button extends React.Component {
      static defaultProps = { type: 'button' };
      render() {
        return <button type={this.props.type}>{this.props.children}</button>;
      }
    }
    ```
- **常见场景**：
  - 设置样式相关的 props 默认值，如 `size`、`color`。
  - 设置回调函数的默认空函数，避免组件内部调用时因未定义而报错。
  - 设置布尔值的默认值，如 `disabled={false}`。
- **最佳实践**：
  - 优先使用函数参数默认值，代码更简洁。
  - 默认值应该是有意义的值，避免使用 `null` 或 `undefined` 作为默认值（除非特定含义）。
  - 对于必传的 props，不设置默认值，并通过 `propTypes` 标记 `isRequired` 来强制要求。

##### 注意事项
- **常见错误**：
  - 默认值设置不当导致逻辑错误，例如默认 `true` 但预期应该是 `false`。
  - 在函数参数解构时设置默认值，同时又在组件上定义了 `defaultProps`，两者可能冲突（函数参数默认值优先级更高）。
  - 默认值对于对象和数组是引用类型，如果直接在 `defaultProps` 中定义对象字面量，所有组件实例将共享同一个引用，可能导致意想不到的修改。推荐在组件内部处理或使用工厂函数。
- **性能问题**：无显著性能问题。
- **兼容性**：`defaultProps` 在 React 所有版本均支持；函数参数默认值是 ES6 特性，现代浏览器和构建工具均支持。

##### 代码示例
```jsx
// 展示默认 props 的优先级
function Greeting({ name = 'Guest' }) {
  return <h1>Hello, {name}</h1>;
}

// 当父组件不传递 name 时，显示 "Hello, Guest"
<Greeting />  // 输出 "Hello, Guest"

// 当父组件传递 name 时，覆盖默认值
<Greeting name="Alice" />  // 输出 "Hello, Alice"
```

---

## 五、类型检查：propTypes

#### 5.1 使用 PropTypes 进行类型检查

##### 概念
- **是什么**：`propTypes` 是 React 提供的一种运行时类型检查机制，用于声明组件期望接收的 props 的类型和必要性。
- **为什么需要**：在开发阶段及早捕获因 props 类型错误导致的 bug，提高代码的健壮性和可维护性。它相当于为组件定义了一个简单的接口契约。
- **解决什么问题**：解决了动态类型语言中类型不匹配难以发现的问题，帮助开发者快速定位错误。

##### 原理
- **底层实现**：React 在开发模式下，会在组件渲染时检查传入的 props 是否符合 `propTypes` 定义。如果不符合，会在控制台输出警告（不会中断程序）。生产环境下，`propTypes` 会被自动移除，不影响性能。其实现基于 `prop-types` 库，通过遍历 `propTypes` 对象并比对实际 props 来完成验证。
- **工作流程**：
  1. 定义组件的 `propTypes` 对象。
  2. 当组件被使用时，React 在渲染阶段调用 `checkPropTypes` 函数（仅开发环境）。
  3. 该函数遍历 `propTypes` 中的每个规则，与实际传入的 props 进行比对，若不符合则输出警告。
- **关键技术点**：`PropTypes` 验证器、`isRequired` 修饰符、自定义验证器。

##### 使用方法
- **基本用法**：
  ```jsx
  import PropTypes from 'prop-types';
  
  function User({ name, age, isAdmin }) {
    return (
      <div>
        <p>Name: {name}</p>
        <p>Age: {age}</p>
        <p>Admin: {isAdmin ? 'Yes' : 'No'}</p>
      </div>
    );
  }
  
  User.propTypes = {
    name: PropTypes.string.isRequired,   // 必传字符串
    age: PropTypes.number,                // 可选数字
    isAdmin: PropTypes.bool,              // 可选布尔值
  };
  ```
- **常见场景**：
  - 基础类型验证：`string`、`number`、`bool`、`func`、`array`、`object`。
  - 复杂类型验证：`PropTypes.shape({...})` 用于对象结构，`PropTypes.arrayOf(PropTypes.string)` 用于数组元素类型。
  - 指定枚举值：`PropTypes.oneOf(['small', 'medium', 'large'])`。
  - 任意类型但必须存在：`PropTypes.any.isRequired`。
- **最佳实践**：
  - 为所有公共组件编写 `propTypes`，尤其是团队协作的项目。
  - 对于复杂的对象结构，使用 `shape` 明确其内部属性类型。
  - 将 `propTypes` 定义在组件文件底部，保持代码可读性。
  - 结合 TypeScript 可以替代 `propTypes`，提供更强大的静态类型检查。

##### 注意事项
- **常见错误**：
  - 忘记导入 `prop-types` 库导致报错。
  - 使用了错误的验证器，例如对数字类型使用了 `PropTypes.string`。
  - 对可选 props 设置了 `isRequired`，导致未传递时出现警告。
  - 在 `shape` 中定义了过多的非必要属性，导致警告泛滥（可以结合 `.isRequired` 控制）。
- **性能问题**：`propTypes` 仅在开发环境执行，不影响生产性能。
- **兼容性**：需要安装额外的 `prop-types` 库，但它是 React 官方维护的，兼容所有主流 React 版本。

##### 代码示例
```jsx
// 完整示例：各种验证器
import PropTypes from 'prop-types';

function Product({ id, name, price, tags, inStock, discount }) {
  return (
    <div>
      <h3>{name}</h3>
      <p>Price: ${price}</p>
      <p>Tags: {tags.join(', ')}</p>
      <p>{inStock ? 'In stock' : 'Out of stock'}</p>
      {discount && <p>Discount: {discount}%</p>}
    </div>
  );
}

Product.propTypes = {
  id: PropTypes.number.isRequired,
  name: PropTypes.string.isRequired,
  price: PropTypes.number.isRequired,
  tags: PropTypes.arrayOf(PropTypes.string),
  inStock: PropTypes.bool,
  discount: PropTypes.oneOfType([PropTypes.number, PropTypes.oneOf([null])]),
};

Product.defaultProps = {
  tags: [],
  inStock: true,
  discount: null,
};
```

---

## 六、子组件向父组件通信

#### 6.1 通过回调函数实现子传父

##### 概念
- **是什么**：这是利用 props 传递函数的一种模式，父组件将一个函数作为 prop 传递给子组件，子组件在适当时候调用该函数，并传递数据给父组件，从而实现子组件向父组件通信。
- **为什么需要**：由于 React 的数据流是单向的（父传子），子组件无法直接修改父组件的状态。通过回调函数，子组件可以间接触发父组件的状态更新。
- **解决什么问题**：解决了子组件需要与父组件交互（如传递表单输入值、点击事件等）的需求。

##### 原理
- **底层实现**：本质上利用了 JavaScript 的函数是一等公民的特性，可以将函数作为值传递。父组件定义的函数内部可以访问父组件的状态和方法（闭包），当子组件调用该函数时，就能通过参数将数据传回父组件。
- **工作流程**：
  1. 父组件定义一个回调函数，该函数接收来自子组件的数据作为参数，并在函数体内更新父组件的 state 或执行其他操作。
  2. 父组件通过 props 将该函数传递给子组件。
  3. 子组件在合适的时机（如按钮点击、表单提交）调用这个函数，并将需要传递的数据作为参数传入。
  4. 父组件的回调函数执行，接收到数据，从而更新状态或触发副作用。
- **关键技术点**：回调函数命名约定（通常以 `on` 开头，如 `onClick`、`onChange`）、`this` 绑定（类组件中）、性能优化（使用 `useCallback` 缓存函数引用）。

##### 使用方法
- **基本用法**：
  ```jsx
  // 父组件
  function Parent() {
    const [message, setMessage] = useState('');
    
    const handleMessage = (data) => {
      setMessage(data);
    };
    
    return (
      <div>
        <p>Message from child: {message}</p>
        <Child onSendMessage={handleMessage} />
      </div>
    );
  }
  
  // 子组件
  function Child({ onSendMessage }) {
    const sendData = () => {
      onSendMessage('Hello from child!');
    };
    return <button onClick={sendData}>Send Message</button>;
  }
  ```
- **常见场景**：
  - 表单组件将用户输入的值回传给父组件。
  - 列表项中的操作按钮（如删除、编辑）通知父组件执行对应操作。
  - 子组件内部状态变化需要通知父组件（如开关状态切换）。
- **最佳实践**：
  - 回调函数 props 以 `on` 开头，如 `onChange`、`onSubmit`。
  - 如果回调函数需要传递多个参数，建议使用对象形式传递，方便后续扩展。
  - 在类组件中，确保回调函数的 `this` 正确绑定（使用箭头函数或 bind）。
  - 使用 `useCallback` 缓存回调函数，避免因函数重新创建导致子组件不必要的重渲染。

##### 注意事项
- **常见错误**：
  - 忘记在子组件中调用回调函数，导致父组件未收到数据。
  - 在类组件中未正确绑定 `this`，导致回调函数执行时无法访问 `this.setState`。
  - 传递过多的回调函数导致 props 复杂，可考虑使用 context 或状态管理库。
- **性能问题**：如果父组件每次渲染都重新创建回调函数（未使用 `useCallback`），会导致子组件即使使用 `React.memo` 也无法跳过渲染，因为函数引用变化了。在函数组件中，使用 `useCallback` 可以避免这个问题。
- **兼容性**：所有 React 版本均支持。

##### 代码示例
```jsx
// 带多个参数的示例
function Parent() {
  const [userInput, setUserInput] = useState({ name: '', age: '' });
  
  const handleInputChange = (field, value) => {
    setUserInput(prev => ({ ...prev, [field]: value }));
  };
  
  return (
    <div>
      <p>Name: {userInput.name}</p>
      <p>Age: {userInput.age}</p>
      <ChildForm onFieldChange={handleInputChange} />
    </div>
  );
}

function ChildForm({ onFieldChange }) {
  return (
    <form>
      <input
        placeholder="Name"
        onChange={(e) => onFieldChange('name', e.target.value)}
      />
      <input
        placeholder="Age"
        type="number"
        onChange={(e) => onFieldChange('age', e.target.value)}
      />
    </form>
  );
}
```

---

## 七、Props 与 State 的区别

#### 7.1 Props 与 State 的对比

##### 概念
- **是什么**：Props 和 State 都是 React 组件中用于存储数据的机制，但它们的用途和特性不同。Props 是从外部传入的数据，State 是组件内部管理的数据。
- **为什么需要**：理解两者的区别有助于正确地设计组件的数据流，避免混淆。
- **解决什么问题**：明确数据的来源和变更责任，使应用架构更清晰。

##### 原理
- **底层实现**：
  - Props 是由父组件传入，组件自身不可修改。当父组件重新渲染并传入新 props 时，组件会更新。
  - State 是在组件内部通过 `useState`（函数组件）或 `this.state`（类组件）定义，并通过 `setState` 或更新函数修改。State 变化会触发组件重新渲染。
- **工作流程**：
  - Props 变化：父组件渲染 -> 新 props 传入 -> 子组件根据新 props 重新渲染。
  - State 变化：调用 setState -> 组件重新渲染。
- **关键技术点**：不可变性（props）、可变性（state）、触发渲染的时机。

##### 使用方法
- **基本用法对比**：
  | 特性 | Props | State |
  |------|-------|-------|
  | 可变性 | 不可变（只读） | 可变（通过 `setState` 修改） |
  | 所属 | 父组件传递，子组件接收 | 组件自身管理 |
  | 用途 | 组件间通信，传递配置数据 | 组件内部动态数据 |
  | 触发渲染 | 父组件重新渲染时，子组件接收新 props 触发重新渲染 | 调用 `setState` 触发重新渲染 |
- **常见场景**：
  - Props：传递列表数据、回调函数、样式配置。
  - State：存储表单输入值、开关状态、计数器等。
- **最佳实践**：
  - 尽量使用 props 而非 state，使组件更可预测。
  - 当数据需要在多个子组件间共享时，提升到它们的共同父组件的 state 中，通过 props 传递给子组件（状态提升）。
  - 避免将 props 直接复制到 state，除非有特殊需求且处理了更新同步问题。

##### 注意事项
- **常见错误**：
  - 混淆 props 和 state 的用途，将不该变的数据放入 state。
  - 在子组件中直接修改 props（应使用 state 替代）。
  - 在 state 初始化时直接使用 props，但未在 props 更新时更新 state（可通过 `useEffect` 或 `getDerivedStateFromProps` 处理，但通常应避免）。
- **性能问题**：State 变化会导致组件重新渲染，过度使用 state 可能导致性能问题。使用 `useMemo` 和 `React.memo` 优化。
- **兼容性**：所有 React 版本通用。

##### 代码示例
```jsx
// Props 和 State 配合使用
function Counter({ initialValue }) {
  const [count, setCount] = useState(initialValue); // state 基于 props 初始化
  return (
    <div>
      <p>Count: {count}</p>
      <button onClick={() => setCount(count + 1)}>Increment</button>
    </div>
  );
}

// 状态提升示例
function Parent() {
  const [sharedValue, setSharedValue] = useState('');
  return (
    <div>
      <ChildA value={sharedValue} onValueChange={setSharedValue} />
      <ChildB value={sharedValue} />
    </div>
  );
}
```

---

## 八、Props 最佳实践（汇总）

#### 8.1 全局 Props 使用最佳实践

##### 概念
- **是什么**：总结在 React 开发中关于 Props 的一系列最佳实践，以提升代码质量、可维护性和性能。
- **为什么需要**：帮助开发者避免常见陷阱，写出更优雅、高效的 React 代码。
- **解决什么问题**：指导如何正确使用 Props，减少 bug，提高团队协作效率。

##### 原理
- **底层实现**：基于 React 的设计原则（如单向数据流、不可变性）以及 JavaScript 的性能特性。
- **工作流程**：贯穿于组件的设计、编码、优化全过程。
- **关键技术点**：解构 props、默认值、类型检查、性能优化（memo、useCallback）、命名规范等。

##### 使用方法
- **基本规范**：
  1. **解构 props**：在函数组件参数中直接解构需要的 props，提高可读性。
  2. **设置默认值**：为可选 props 提供默认值（函数参数默认值或 `defaultProps`）。
  3. **类型检查**：使用 `propTypes` 或 TypeScript 定义 props 类型。
  4. **命名规范**：回调函数 props 以 `on` 开头，如 `onClick`；数据 props 使用名词。
  5. **避免传递过多 props**：如果组件需要很多 props，考虑重构组件或使用组合。
  6. **使用 children 进行组合**：而不是通过 props 传递 React 元素，使组件更灵活。
  7. **缓存回调函数**：在函数组件中使用 `useCallback` 避免子组件不必要重渲染。
  8. **谨慎使用展开语法**：`{...props}` 容易传递无关属性，尽量明确列出。
- **常见场景**：适用于所有使用 props 的组件开发。
- **最佳实践**：这些本身就是最佳实践，无需额外说明。

##### 注意事项
- **常见错误**：
  - 过度使用展开语法传递无关 props。
  - 忘记使用 `useCallback` 导致性能下降。
  - 未对 props 进行类型检查，导致运行时错误。
  - 在组件内部修改 props 的引用（如 `props.someArray.push()`），虽然没有直接赋值，但修改了引用内部的数据，仍然违反了不可变性。
- **性能问题**：关注 props 的引用变化，使用 `React.memo` 配合 `useCallback` 和 `useMemo` 优化。
- **兼容性**：所有 React 版本通用。

##### 代码示例
```jsx
// 综合最佳实践示例
import React, { memo, useCallback } from 'react';
import PropTypes from 'prop-types';

const ListItem = memo(function ListItem({ item, onDelete }) {
  return (
    <li>
      {item.name}
      <button onClick={() => onDelete(item.id)}>Delete</button>
    </li>
  );
});

ListItem.propTypes = {
  item: PropTypes.shape({
    id: PropTypes.number.isRequired,
    name: PropTypes.string.isRequired,
  }).isRequired,
  onDelete: PropTypes.func.isRequired,
};

function List({ items }) {
  const handleDelete = useCallback((id) => {
    console.log('Delete', id);
    // 实际删除逻辑...
  }, []);
  
  return (
    <ul>
      {items.map(item => (
        <ListItem key={item.id} item={item} onDelete={handleDelete} />
      ))}
    </ul>
  );
}

List.propTypes = {
  items: PropTypes.arrayOf(
    PropTypes.shape({
      id: PropTypes.number.isRequired,
      name: PropTypes.string.isRequired,
    })
  ).isRequired,
};

export default List;
```