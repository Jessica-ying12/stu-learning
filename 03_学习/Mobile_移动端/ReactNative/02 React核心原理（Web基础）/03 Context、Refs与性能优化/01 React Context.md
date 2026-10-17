## 一、Context 概述
#### Context 概述
##### 概念
- **是什么**：Context 是 React 提供的一种跨组件树传递数据的机制，允许在组件之间共享“全局”数据，而无需通过 props 逐层手动传递。
- **为什么需要**：在 React 应用中，数据通常自上而下通过 props 传递，当组件层级较深或许多组件需要相同数据（如主题、用户信息）时，逐层传递 props 变得冗长且难以维护，这种现象称为“prop 逐层透传”（Prop Drilling）。
- **解决什么问题**：Context 解决了 Prop Drilling 问题，使父组件能够向其下层任意深度的任何组件提供信息，中间组件无需感知数据的存在。

##### 原理
- **底层实现**：React 内部维护了一个上下文栈，每个 Context 对象对应一个独立的数据通道。当组件使用 `useContext` 或 `Consumer` 时，React 会从当前组件向上查找最近的匹配 Provider，获取其提供的值。Provider 通过 `value` 属性向子树广播数据。
- **工作流程**：
  1. 创建 Context 对象（`createContext`）。
  2. 使用 `Provider` 包裹子组件树，并通过 `value` 属性提供数据。
  3. 下层组件通过 `useContext` 或 `Consumer` 订阅 Context 数据。
  4. 当 `Provider` 的 `value` 发生变化时，所有订阅该 Context 的组件自动重新渲染。
- **关键技术点**：
  - **发布-订阅模式**：Provider 作为发布者，消费者组件作为订阅者。
  - **引用比较**：React 使用 `Object.is` 比较新旧 `value` 以决定是否触发更新。
  - **不受 `shouldComponentUpdate` 影响**：即使祖先组件跳过更新，消费者组件仍能更新，确保数据一致性。

##### 使用方法
- **基本用法**：三步走——创建、提供、消费。
- **常见场景**：
  - 主题切换（深色/浅色模式）
  - 用户认证状态
  - 国际化语言设置
  - 路由状态
- **最佳实践**：
  - 将 Context 用于“全局”数据，避免滥用。
  - 考虑使用组件组合替代 Context 解决简单的 props 透传。
  - 合理拆分 Context，避免单一 Context 过于庞大。

##### 注意事项
- **常见错误**：将频繁变化的状态放入 Context，导致大量不必要的重新渲染。
- **性能问题**：Context 的消费者会在 `value` 变化时全部重新渲染，需注意性能优化。
- **兼容性**：Context API 在 React 16.3 版本后稳定，旧版本使用实验性 API。

##### 代码示例
```jsx
// 创建 Context
const ThemeContext = React.createContext('light');

// 提供数据
function App() {
  return (
    <ThemeContext.Provider value="dark">
      <Toolbar />
    </ThemeContext.Provider>
  );
}

// 消费数据
function Toolbar() {
  const theme = useContext(ThemeContext);
  return <div>当前主题：{theme}</div>;
}
```

---

## 二、createContext
#### createContext
##### 概念
- **是什么**：`createContext` 是一个 React 函数，用于创建一个 Context 对象，该对象包含 `Provider` 和 `Consumer` 组件。
- **为什么需要**：需要先有一个 Context 对象，才能在组件树中使用 Provider 提供数据和 Consumer 消费数据。
- **解决什么问题**：定义了数据通道的标识，并为未使用 Provider 的情况提供默认值。

##### 原理
- **底层实现**：`createContext` 返回一个包含 `$$typeof`、`Provider`、`Consumer` 等属性的对象。React 内部通过这个对象识别 Context 类型，并建立订阅关系。
- **工作流程**：调用 `createContext(defaultValue)` 后，生成一个独一无二的 Context 对象，后续的 Provider 和 Consumer 通过这个对象建立连接。
- **关键技术点**：
  - `defaultValue` 仅在组件**没有**被 `Provider` 包裹时生效。
  - 如果给 Provider 的 `value` 设置为 `undefined`，消费者读取的将是 `undefined`，而不是 `defaultValue`。

##### 使用方法
- **基本用法**：
  ```jsx
  const MyContext = React.createContext(defaultValue);
  ```
- **常见场景**：
  - 创建主题 Context：`const ThemeContext = React.createContext('light');`
  - 创建用户 Context：`const UserContext = React.createContext(null);`
- **最佳实践**：
  - 为 Context 设置合理的 `defaultValue`，方便独立测试和错误处理。
  - 可设置 `displayName` 便于 React DevTools 调试：
    ```jsx
    MyContext.displayName = 'MyContext';
    ```

##### 注意事项
- **常见错误**：忘记提供 Provider，导致组件使用了默认值但不符合预期。
- **性能问题**：无直接影响。
- **兼容性**：自 React 16.3.0 起稳定可用。

##### 代码示例
```jsx
// 创建 Context 并提供默认值
const LocaleContext = React.createContext('zh-CN');

// 设置 displayName 便于调试
LocaleContext.displayName = 'LocaleContext';
```

---

## 三、Context.Provider
#### Context.Provider
##### 概念
- **是什么**：`Context.Provider` 是每个 Context 对象自带的 React 组件，用于向其子树提供 Context 的值。
- **为什么需要**：只有通过 Provider 提供值，下层组件才能消费到动态数据。
- **解决什么问题**：将数据注入组件树，并控制数据的更新和传播范围。

##### 原理
- **底层实现**：Provider 组件内部维护了一个订阅列表，当它的 `value` 属性变化时，会遍历所有订阅者（消费者）并强制它们重新渲染。
- **工作流程**：
  1. 渲染 Provider 时，将 `value` 存入内部状态。
  2. 当子树中的消费者组件挂载时，向 Provider 注册自身。
  3. Provider 的 `value` 变化时，通知所有注册的消费者更新。
- **关键技术点**：
  - **可嵌套**：相同 Context 的 Provider 可以嵌套，内层 Provider 的值会覆盖外层。
  - **值比较**：使用 `Object.is` 比较新旧 `value`，只有变化时才触发更新。

##### 使用方法
- **基本用法**：
  ```jsx
  <MyContext.Provider value={/* some value */}>
    {children}
  </MyContext.Provider>
  ```
- **常见场景**：
  - 提供用户信息：`<UserContext.Provider value={user}>`
  - 提供主题和切换函数：`<ThemeContext.Provider value={{ theme, toggleTheme }}>`
- **最佳实践**：
  - 使用 `useMemo` 缓存 `value` 对象，避免不必要的重新渲染。
  - 将 Provider 放在组件树中尽可能靠近使用位置，减少无关组件的重渲染。

##### 注意事项
- **常见错误**：直接在 render 中创建新对象导致每次渲染都触发消费者更新：
  ```jsx
  // ❌ 错误
  <Provider value={{ theme, toggleTheme }}> // 每次 render 新对象
  // ✅ 正确
  const value = useMemo(() => ({ theme, toggleTheme }), [theme]);
  <Provider value={value}>
  ```
- **性能问题**：Provider 的 `value` 变化会导致所有消费者重新渲染，需合理拆分 Context。
- **兼容性**：同 `createContext`。

##### 代码示例
```jsx
function App() {
  const [theme, setTheme] = useState('light');
  const toggleTheme = () => setTheme(prev => prev === 'light' ? 'dark' : 'light');

  // 缓存 value 对象，避免无意义重渲染
  const value = useMemo(() => ({ theme, toggleTheme }), [theme]);

  return (
    <ThemeContext.Provider value={value}>
      <MainContent />
    </ThemeContext.Provider>
  );
}
```

---

## 四、useContext
#### useContext
##### 概念
- **是什么**：`useContext` 是一个 React Hook，用于在函数组件中读取 Context 的值。
- **为什么需要**：简化函数组件中 Context 的消费方式，避免使用 `Consumer` 的 render props 模式。
- **解决什么问题**：让函数组件能够订阅 Context 的变化，并在值变化时自动重新渲染。

##### 原理
- **底层实现**：`useContext` 内部会获取当前组件的 Fiber 节点，并从该节点向上查找最近的 Provider，返回其 `value`。同时将该组件标记为对应 Context 的观察者，当 Provider 更新时，触发该组件重新渲染。
- **工作流程**：
  1. 调用 `useContext(MyContext)`。
  2. React 从当前 Fiber 向上找到最近的 `MyContext.Provider`，获取其 `value`。
  3. 如果找不到 Provider，返回 `createContext` 时传入的 `defaultValue`。
  4. 当 Provider 的 `value` 变化时，React 会重新执行该组件。
- **关键技术点**：
  - **Hook 规则**：只能在函数组件或自定义 Hook 的顶层调用。
  - **自动订阅**：组件自动订阅 Context，无需手动管理。

##### 使用方法
- **基本用法**：
  ```jsx
  const value = useContext(MyContext);
  ```
- **常见场景**：
  - 在深层子组件中获取用户信息：`const user = useContext(UserContext);`
  - 获取主题切换函数：`const { theme, toggleTheme } = useContext(ThemeContext);`
- **最佳实践**：
  - 配合自定义 Hook 使用，封装错误处理：
    ```jsx
    function useTheme() {
      const context = useContext(ThemeContext);
      if (context === undefined) {
        throw new Error('useTheme must be used within a ThemeProvider');
      }
      return context;
    }
    ```

##### 注意事项
- **常见错误**：在条件语句或循环中调用 `useContext`，违反 Hook 规则。
- **性能问题**：`useContext` 会使组件在 Context 值变化时重新渲染，如果组件渲染成本高，需考虑 memo 或拆分。
- **兼容性**：需要 React 16.8.0 及以上版本（支持 Hooks）。

##### 代码示例
```jsx
import { useContext } from 'react';

function Profile() {
  const user = useContext(UserContext);
  if (!user) return <p>请登录</p>;
  return <div>用户名：{user.name}</div>;
}
```

---

## 五、Context.Consumer
#### Context.Consumer
##### 概念
- **是什么**：`Context.Consumer` 是每个 Context 对象自带的组件，用于在类组件或需要 render props 的场景下消费 Context 的值。
- **为什么需要**：在 Hook 出现前，这是函数组件消费 Context 的唯一方式；目前仍可用于类组件或同时消费多个 Context。
- **解决什么问题**：提供一种声明式的方式来订阅 Context，无需使用 Hooks。

##### 原理
- **底层实现**：`Consumer` 组件通过 render props 模式，将当前 Context 的值传递给作为子元素的函数。
- **工作流程**：`Consumer` 接收一个函数作为子元素，React 在渲染时会调用该函数，并传入从最近 Provider 获取的值（或默认值），然后将函数返回的内容渲染出来。
- **关键技术点**：
  - **订阅机制**：`Consumer` 内部同样订阅 Context，当值变化时重新执行传入的函数。
  - **可嵌套**：支持多个 Consumer 嵌套使用。

##### 使用方法
- **基本用法**：
  ```jsx
  <MyContext.Consumer>
    {value => /* 基于 value 渲染 JSX */}
  </MyContext.Consumer>
  ```
- **常见场景**：
  - 在类组件中消费 Context：
    ```jsx
    class MyComponent extends React.Component {
      render() {
        return (
          <ThemeContext.Consumer>
            {theme => <div className={theme}>内容</div>}
          </ThemeContext.Consumer>
        );
      }
    }
    ```
  - 同时消费多个 Context：
    ```jsx
    <UserContext.Consumer>
      {user => (
        <ThemeContext.Consumer>
          {theme => (
            <div className={theme}>Hello {user.name}</div>
          )}
        </ThemeContext.Consumer>
      )}
    </UserContext.Consumer>
    ```
- **最佳实践**：
  - 对于新代码，优先使用 `useContext` 更简洁。
  - 在类组件中，如果只需要一个 Context，也可使用 `static contextType` 简化。

##### 注意事项
- **常见错误**：忘记将 `Consumer` 的子元素写成函数，导致渲染错误。
- **性能问题**：`Consumer` 内部函数在每次渲染时重新执行，应避免在函数内执行昂贵计算。
- **兼容性**：自 React 16.3.0 起稳定。

##### 代码示例
```jsx
class ThemedButton extends React.Component {
  render() {
    return (
      <ThemeContext.Consumer>
        {theme => (
          <button className={`btn-${theme}`}>
            {this.props.children}
          </button>
        )}
      </ThemeContext.Consumer>
    );
  }
}
```

---

## 六、更新上下文
#### 更新上下文
##### 概念
- **是什么**：更新上下文指的是通过改变 `Provider` 的 `value` 属性来触发订阅组件的重新渲染，从而实现动态数据更新。
- **为什么需要**：Context 本身是静态的，只有通过改变 `value` 才能让消费者响应数据变化。
- **解决什么问题**：使 Context 能够适应动态状态，如用户登录、主题切换等。

##### 原理
- **底层实现**：当 `Provider` 的 `value` 变化时，React 会对比新旧值（使用 `Object.is`），如果不同，则标记该 Provider 子树中的所有消费者组件需要更新，并在下一次渲染周期中执行。
- **工作流程**：
  1. 在 Provider 所在组件中，使用 state 或 reducer 管理数据。
  2. 将数据和更新函数一起作为 `value` 传递给 Provider。
  3. 消费者组件通过 `useContext` 获取数据和更新函数，调用更新函数修改 state。
  4. state 变化导致 Provider 重新渲染，传入新的 `value`，触发所有消费者更新。
- **关键技术点**：
  - **状态提升**：将状态提升到 Provider 所在组件，由该组件管理变化。
  - **组合 value**：通常将状态和修改函数组合成一个对象传递给 `value`。

##### 使用方法
- **基本用法**：
  ```jsx
  function ThemeProvider({ children }) {
    const [theme, setTheme] = useState('light');
    const toggleTheme = () => setTheme(prev => prev === 'light' ? 'dark' : 'light');
    const value = useMemo(() => ({ theme, toggleTheme }), [theme]);

    return (
      <ThemeContext.Provider value={value}>
        {children}
      </ThemeContext.Provider>
    );
  }

  function ThemeSwitcher() {
    const { theme, toggleTheme } = useContext(ThemeContext);
    return <button onClick={toggleTheme}>切换主题（当前：{theme}）</button>;
  }
  ```
- **常见场景**：
  - 用户登录/登出：Provider 管理用户状态和 dispatch 函数。
  - 购物车数量更新：Provider 管理购物车状态和添加/删除方法。
- **最佳实践**：
  - 使用 `useMemo` 缓存 value 对象，防止子组件不必要的重新渲染。
  - 考虑将 state 和 dispatch 分离到不同的 Context 中，避免无关组件因 value 中某个字段变化而全部重渲染。

##### 注意事项
- **常见错误**：直接在 value 中传递 setState 函数而不缓存，导致父组件重渲染时 value 对象地址变化。
- **性能问题**：如果 value 中包含多个独立状态，任何状态变化都会导致所有消费者重渲染。解决方案是拆分 Context 或使用 useReducer 分离 state 和 dispatch。
- **兼容性**：与 state 管理机制兼容，无额外兼容性问题。

##### 代码示例
```jsx
// 创建两个 Context 分别存储 state 和 dispatch
const CountStateContext = React.createContext();
const CountDispatchContext = React.createContext();

function CountProvider({ children }) {
  const [count, setCount] = useState(0);
  const increment = () => setCount(c => c + 1);
  const value = useMemo(() => ({ count, increment }), [count]);

  return (
    <CountStateContext.Provider value={count}>
      <CountDispatchContext.Provider value={increment}>
        {children}
      </CountDispatchContext.Provider>
    </CountStateContext.Provider>
  );
}

function Counter() {
  const count = useContext(CountStateContext);
  const increment = useContext(CountDispatchContext);
  return (
    <>
      <p>Count: {count}</p>
      <button onClick={increment}>+1</button>
    </>
  );
}
```

---

## 七、Context 性能优化与最佳实践
#### Context 性能优化与最佳实践
##### 概念
- **是什么**：针对 Context 使用时可能出现的性能问题和代码维护性问题，总结的优化技巧和最佳实践。
- **为什么需要**：Context 虽然强大，但滥用或不当使用会导致性能下降和代码难以维护。
- **解决什么问题**：帮助开发者高效、正确地使用 Context，避免常见陷阱。

##### 原理
- **底层实现**：基于 React 的渲染机制和 Context 订阅原理，优化措施主要围绕减少不必要的重新渲染和保持代码清晰。
- **关键技术点**：
  - **引用稳定性**：通过 `useMemo` 和 `useCallback` 保持 value 引用稳定。
  - **Context 拆分**：按职责拆分为多个 Context，避免单一 Context 过大。
  - **组件组合**：利用 children 透传代替 Context，简化数据流。

##### 使用方法
- **基本优化策略**：
  1. **缓存 value 对象**：
     ```jsx
     const value = useMemo(() => ({ theme, toggleTheme }), [theme]);
     ```
  2. **拆分 Context**：
     ```jsx
     // 分离 state 和 dispatch
     const [UserStateContext, useUserState] = createStrictContext();
     const [UserDispatchContext, useUserDispatch] = createStrictContext();
     ```
  3. **创建自定义 Hook 封装 Context 逻辑**：
     ```jsx
     function useUser() {
       const state = useContext(UserStateContext);
       const dispatch = useContext(UserDispatchContext);
       if (!state || !dispatch) throw new Error('...');
       return { state, dispatch };
     }
     ```
  4. **使用严格 Context（检查 Provider 包裹）**：
     ```jsx
     function createStrictContext(errorMessage) {
       const Context = createContext(undefined);
       function useStrictContext() {
         const value = useContext(Context);
         if (value === undefined) throw new Error(errorMessage);
         return value;
       }
       return [Context, useStrictContext];
     }
     ```
- **常见场景优化**：
  - 主题切换：将 theme 和 toggleTheme 分开，避免使用 theme 的组件因 toggleTheme 变化而重渲染（但 toggleTheme 通常是稳定的，使用 `useCallback` 即可）。
  - 表单状态：使用多个 Context 分别存储表单值和验证状态。
- **最佳实践汇总**：
  - Provider 放置在合理层级，避免全局污染。
  - 默认值用于测试和降级，不应依赖它作为业务逻辑。
  - 对于频繁更新的数据（如动画），考虑使用其他状态管理方案（如 Zustand）或使用 `useSyncExternalStore`。

##### 注意事项
- **常见错误**：
  - 过度优化：在没有性能问题的地方过早拆分 Context，增加复杂度。
  - 忽略 children 组合：有时只需将组件作为 props 传递即可避免 Context。
- **性能问题**：
  - 每次 `value` 变化都会导致所有消费者重渲染，如果消费者组件渲染成本高，应使用 `React.memo` 并结合 Context 拆分。
  - 如果 Context 值包含数组或对象，确保使用 `useMemo` 缓存。
- **兼容性**：优化手段均基于 React 现有 API，兼容 React 16.8+。

##### 代码示例
```jsx
// 示例：组合优于 Context
function Page({ user }) {
  return (
    <Layout
      header={<Header user={user} />}
      content={<Content user={user} />}
    />
  );
}
// 无需 Context，通过 props 组合传递

// 示例：严格 Context + 自定义 Hook
const [UserContext, useUser] = createStrictContext('useUser must be used within UserProvider');

function UserProvider({ children }) {
  const [user, setUser] = useState(null);
  const login = (userData) => setUser(userData);
  const logout = () => setUser(null);
  const value = useMemo(() => ({ user, login, logout }), [user]);

  return <UserContext.Provider value={value}>{children}</UserContext.Provider>;
}

function Profile() {
  const { user, logout } = useUser(); // 自动检查 Provider
  return (
    <div>
      <p>Hello {user?.name}</p>
      <button onClick={logout}>Logout</button>
    </div>
  );
}
```

---

以上笔记覆盖了 React Context 的核心知识点，按照模板详细解释了每个知识点的概念、原理、使用方法、注意事项，并附有代码示例。希望对你学习 React Context 有所帮助！