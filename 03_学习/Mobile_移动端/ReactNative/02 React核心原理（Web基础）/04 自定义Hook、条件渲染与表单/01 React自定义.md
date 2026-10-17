## 一、Hooks通用规则（Rules of Hooks）

### 概念
- **是什么**：Hooks通用规则是React为保证Hooks正确工作而设定的一套强制性规范，所有Hooks（包括内置Hook和自定义Hook）都必须遵守。
- **为什么需要**：React依赖Hooks的调用顺序来为组件保存和管理状态。如果调用顺序发生变化，React将无法正确地将某个state与对应的`useState`关联，导致数据错乱或难以追踪的Bug。
- **解决什么问题**：确保组件在多次渲染中状态和行为的一致性，避免因条件或循环调用Hooks而引发的不可预测错误。

### 原理
- **底层实现**：React在组件内部维护一个**链表**，每个Hook对应一个节点。每次渲染时，React按照调用顺序依次从链表中读取状态。如果顺序改变，后续Hook将取到错误的状态。
- **工作流程**：
  1. 组件首次渲染时，React按调用顺序创建Hook节点并存入链表。
  2. 后续渲染时，React按相同顺序从链表中取出对应的状态和更新函数。
  3. 如果调用顺序变化（例如某次渲染跳过了某个Hook），React无法匹配链表节点，导致状态错乱。
- **关键技术点**：调用顺序的稳定性、链表结构、状态索引。

### 使用方法
- **基本用法**：在React函数组件或自定义Hook的**顶层**作用域调用所有Hook，不得置于循环、条件、嵌套函数或`try/catch/finally`块中。
  ```javascript
  function Component() {
    // ✅ 顶层调用
    const [count, setCount] = useState(0);
    useEffect(() => { /* ... */ }, [count]);
    // ...
  }
  ```
- **常见场景**：任何使用Hook的地方都需要遵循此规则，例如在函数组件中管理状态、副作用、上下文等。
- **最佳实践**：
  - 使用ESLint插件`eslint-plugin-react-hooks`自动检查规则。
  - 如果需要在条件分支中使用某个Hook，可将条件逻辑移到Hook内部实现，而非条件性调用Hook。

### 注意事项
- **常见错误**：
  - 在条件语句中调用Hook。
  - 在事件处理函数或普通JavaScript函数中调用Hook。
  - 在`useEffect`的异步回调中调用Hook（实际上不允许）。
- **性能问题**：违反规则通常不直接导致性能问题，但会造成逻辑错误或应用崩溃。
- **兼容性**：适用于所有支持Hooks的React版本（16.8+）。

### 代码示例
```javascript
// 🔴 错误示例
function BadComponent({ show }) {
  if (show) {
    const [theme, setTheme] = useState('light'); // ❌ 条件中调用
  }
  // ...
}

// ✅ 正确示例
function GoodComponent({ show }) {
  const [theme, setTheme] = useState('light'); // ✅ 顶层调用
  useEffect(() => {
    if (show) {
      // 将条件放在Hook内部
      document.body.className = theme;
    }
  }, [show, theme]);
  // ...
}
```

---

## 二、自定义Hook基础概念

### 概念
- **是什么**：自定义Hook是一个JavaScript函数，其名称以`use`开头，内部可以调用其他Hook（包括内置Hook和其他自定义Hook）。它是React提供的**复用状态逻辑**的机制。
- **为什么需要**：当多个组件需要执行相同的非可视逻辑（如表单处理、数据获取、订阅）时，可以将这部分逻辑抽取出来，避免代码重复，实现关注点分离。
- **解决什么问题**：将组件逻辑从UI中解耦，提高代码的可复用性、可读性和可维护性。

### 原理
- **底层实现**：自定义Hook本质上就是一个普通函数，但由于其遵循Hooks规则，React运行时能够正确识别其内部调用的Hook，并将这些Hook的状态关联到**调用该自定义Hook的组件**上。
- **工作流程**：
  1. 组件调用自定义Hook。
  2. 自定义Hook内部依次调用内置或其他自定义Hook，这些调用被React记录到当前组件的Hook链表中。
  3. 当组件重新渲染时，再次调用自定义Hook，React按相同顺序执行内部Hook，并返回最新的值。
- **关键技术点**：函数命名约定（`use`前缀）、Hook规则在自定义Hook中的继承、状态与组件实例的绑定。

### 使用方法
- **基本用法**：创建一个以`use`开头的函数，内部调用其他Hook，最后返回需要暴露的数据或函数。
  ```javascript
  function useDocumentTitle(title) {
    useEffect(() => {
      document.title = title;
    }, [title]);
  }
  ```
- **常见场景**：
  - 封装数据获取（`useFetch`）。
  - 处理表单输入（`useFormInput`）。
  - 监听浏览器事件（`useWindowSize`）。
  - 使用本地存储（`useLocalStorage`）。
- **最佳实践**：
  - 每个自定义Hook应具有**单一职责**，只封装一个逻辑关注点。
  - 为自定义Hook编写清晰的文档注释（JSDoc/TSDoc）。
  - 返回值应尽量简单明了，通常返回数组（如`[value, setValue]`）或对象（如`{data, loading, error}`）。

### 注意事项
- **常见错误**：
  - 忘记以`use`开头命名，导致Lint规则失效。
  - 在自定义Hook内部调用普通函数而非Hook。
  - 将不相关的多个逻辑塞入同一个Hook（上帝Hook）。
- **性能问题**：自定义Hook本身不会引入额外性能开销，其内部使用的`useMemo`/`useCallback`等优化手段需合理使用。
- **兼容性**：与内置Hook相同，需要React 16.8+。

### 代码示例
```javascript
// 一个简单的自定义Hook：管理布尔开关状态
function useToggle(initialValue = false) {
  const [value, setValue] = useState(initialValue);
  const toggle = useCallback(() => setValue(v => !v), []);
  return [value, toggle];
}

// 在组件中使用
function MyComponent() {
  const [isOn, toggleIsOn] = useToggle(false);
  return <button onClick={toggleIsOn}>{isOn ? 'ON' : 'OFF'}</button>;
}
```

---

## 三、提取逻辑：创建自定义Hook的动机与方法

### 概念
- **是什么**：提取逻辑是指将组件中的重复或复杂逻辑封装成一个独立的、可复用的自定义Hook。
- **为什么需要**：随着组件复杂度增加，逻辑与UI混在一起会使组件臃肿难懂。提取逻辑有助于保持组件简洁，同时让逻辑可以在多个组件间共享。
- **解决什么问题**：解决代码重复问题，提升代码的模块化和可测试性。

### 原理
- **底层实现**：与自定义Hook的原理相同，提取逻辑本质上是将一组Hook调用封装到一个函数中，该函数以`use`命名。
- **工作流程**：
  1. 识别组件中可复用的逻辑片段（通常是一组相关的`useState`、`useEffect`等）。
  2. 将该片段移至一个新函数，并添加`use`前缀。
  3. 在原组件中调用该函数，替换原有逻辑。
- **关键技术点**：逻辑的边界划分、参数设计、返回值设计。

### 使用方法
- **基本用法**：
  1. 识别重复或过于复杂的逻辑。
  2. 创建新函数，将相关Hook调用复制过去。
  3. 确定Hook的输入（参数）和输出（返回值）。
  4. 在组件中调用Hook，并处理返回值。
- **常见场景**：
  - 表单输入值的收集与验证。
  - 媒体查询监听。
  - 本地存储同步。
  - 数据请求与缓存。
- **最佳实践**：
  - 提取前先梳理逻辑的依赖关系。
  - 保持Hook的参数为原始类型或稳定引用，避免不必要的重新执行。
  - 为提取的Hook编写单元测试。

### 注意事项
- **常见错误**：
  - 提取出的Hook仍然与UI耦合（例如返回JSX），这是不合理的，自定义Hook应只返回数据或函数。
  - 过度提取，将没必要复用的逻辑也做成Hook，增加复杂度。
- **性能问题**：确保Hook内部使用了`useMemo`/`useCallback`优化，避免每次渲染都重新创建大对象或函数。
- **兼容性**：无特殊兼容性问题。

### 代码示例
```javascript
// 原始组件：包含在线状态监听逻辑
function StatusBar() {
  const [isOnline, setIsOnline] = useState(true);
  useEffect(() => {
    function handleOnline() { setIsOnline(true); }
    function handleOffline() { setIsOnline(false); }
    window.addEventListener('online', handleOnline);
    window.addEventListener('offline', handleOffline);
    return () => {
      window.removeEventListener('online', handleOnline);
      window.removeEventListener('offline', handleOffline);
    };
  }, []);
  return <div>{isOnline ? '✅ Online' : '❌ Offline'}</div>;
}

// 提取后的自定义Hook
function useOnlineStatus() {
  const [isOnline, setIsOnline] = useState(true);
  useEffect(() => {
    function handleOnline() { setIsOnline(true); }
    function handleOffline() { setIsOnline(false); }
    window.addEventListener('online', handleOnline);
    window.addEventListener('offline', handleOffline);
    return () => {
      window.removeEventListener('online', handleOnline);
      window.removeEventListener('offline', handleOffline);
    };
  }, []);
  return isOnline;
}

// 简化后的组件
function StatusBar() {
  const isOnline = useOnlineStatus();
  return <div>{isOnline ? '✅ Online' : '❌ Offline'}</div>;
}
```

---

## 四、实战案例：useLocalStorage

### 概念
- **是什么**：`useLocalStorage`是一个自定义Hook，用于将React状态自动同步到浏览器的`localStorage`中，实现数据的持久化存储，并支持跨标签页同步。
- **为什么需要**：手动操作`localStorage`需要在`useEffect`中读写，且容易忘记处理JSON序列化、SSR兼容等问题。封装后可以像使用`useState`一样方便地使用持久化数据。
- **解决什么问题**：简化本地存储操作，自动处理序列化、反序列化、跨标签页同步，并提供响应式状态。

### 原理
- **底层实现**：
  1. **惰性初始化**：使用`useState`传入一个函数，该函数从`localStorage`读取初始值，避免每次渲染都读取。
  2. **状态同步**：提供`setValue`函数，同时更新React state和`localStorage`。
  3. **跨标签页同步**：监听`storage`事件，当其他标签页修改同一key时，更新当前state。
  4. **同标签页同步**：手动派发自定义事件，使同一页面内多个使用该Hook的组件也能同步更新。
  5. **清理**：`useEffect`返回函数移除事件监听。
- **工作流程**：
  1. 组件调用`useLocalStorage(key, initialValue)`。
  2. Hook内部读取`localStorage`初始化state。
  3. 组件渲染使用该state。
  4. 当调用`setValue`时，更新state并写入`localStorage`，同时触发自定义事件。
  5. 其他标签页通过`storage`事件更新，同一页面通过自定义事件更新。
- **关键技术点**：`JSON.parse/stringify`、`useCallback`稳定函数引用、`storage`事件、自定义事件。

### 使用方法
- **基本用法**：`const [storedValue, setStoredValue] = useLocalStorage(key, initialValue);`
- **常见场景**：
  - 用户偏好设置（主题、语言）。
  - 表单草稿保存。
  - 购物车数据持久化。
- **最佳实践**：
  - 处理SSR环境（如Next.js），避免在服务器端访问`window`。
  - 使用`useCallback`包装读写函数，防止不必要的子组件重渲染。
  - 支持函数式更新（`setValue(prev => newValue)`）以保持与`useState`一致。

### 注意事项
- **常见错误**：
  - 未处理`JSON.parse`异常导致应用崩溃。
  - 在SSR中直接使用`window`导致错误。
  - 忘记处理跨标签页同步，导致数据不一致。
- **性能问题**：每次`setValue`都会触发state更新和写入操作，写入`localStorage`是同步的，可能影响性能（但通常值不大，可忽略）。
- **兼容性**：`localStorage`在IE8+支持，但需注意隐私模式下的写入限制。

### 代码示例
```javascript
import { useState, useEffect, useCallback } from 'react';

function useLocalStorage(key, initialValue) {
  // 惰性初始化
  const readValue = useCallback(() => {
    if (typeof window === 'undefined') return initialValue;
    try {
      const item = window.localStorage.getItem(key);
      return item ? JSON.parse(item) : initialValue;
    } catch (error) {
      console.warn(`读取localStorage key "${key}" 失败:`, error);
      return initialValue;
    }
  }, [key, initialValue]);

  const [storedValue, setStoredValue] = useState(readValue);

  const setValue = useCallback((value) => {
    if (typeof window === 'undefined') {
      console.warn(`无法在SSR环境设置localStorage key "${key}"`);
    }
    try {
      const valueToStore = value instanceof Function ? value(storedValue) : value;
      setStoredValue(valueToStore);
      window.localStorage.setItem(key, JSON.stringify(valueToStore));
      // 触发自定义事件，同页面其他实例可同步
      window.dispatchEvent(new Event('local-storage-change'));
    } catch (error) {
      console.warn(`设置localStorage key "${key}" 失败:`, error);
    }
  }, [key, storedValue]);

  useEffect(() => {
    const handleStorageChange = (e) => {
      if (e.key === key && e.newValue !== JSON.stringify(storedValue)) {
        setStoredValue(e.newValue ? JSON.parse(e.newValue) : initialValue);
      }
    };
    window.addEventListener('storage', handleStorageChange);
    window.addEventListener('local-storage-change', handleStorageChange);
    return () => {
      window.removeEventListener('storage', handleStorageChange);
      window.removeEventListener('local-storage-change', handleStorageChange);
    };
  }, [key, initialValue, storedValue]);

  return [storedValue, setValue];
}
```

---

## 五、实战案例：useFetch

### 概念
- **是什么**：`useFetch`是一个自定义Hook，用于封装数据获取逻辑，统一管理请求的`loading`、`error`和`data`状态，并自动处理竞态条件和组件卸载时的请求取消。
- **为什么需要**：在函数组件中直接使用`fetch`需要手动管理`useState`和`useEffect`，且容易忘记清理请求导致内存泄漏。`useFetch`将这些重复工作标准化。
- **解决什么问题**：简化数据获取代码，避免竞态条件（过时响应覆盖最新状态），并防止在已卸载组件上调用`setState`。

### 原理
- **底层实现**：
  1. **状态管理**：使用三个`useState`分别存储`data`、`loading`、`error`。
  2. **副作用触发**：使用`useEffect`监听URL变化，当URL变化时重新执行请求。
  3. **取消请求**：利用`AbortController`创建一个`signal`传递给`fetch`，在清理函数中调用`abort()`取消未完成的请求。
  4. **防止内存泄漏**：在`useEffect`的清理函数中取消请求，确保组件卸载后不会更新状态。
- **工作流程**：
  1. 组件调用`useFetch(url)`。
  2. `useEffect`执行，创建`AbortController`，重置`loading`、`data`、`error`。
  3. 发起`fetch`请求，携带`signal`。
  4. 请求成功，更新`data`，`loading`设为`false`；请求失败，更新`error`。
  5. 如果组件卸载或URL改变，清理函数中止请求。
- **关键技术点**：`AbortController`、竞态条件处理、`useEffect`依赖数组。

### 使用方法
- **基本用法**：`const { data, loading, error } = useFetch(url);`
- **常见场景**：
  - 获取API数据并显示加载状态。
  - 依赖某个参数自动重新请求（如分页、搜索）。
  - 与`useEffect`组合实现更复杂的请求逻辑。
- **最佳实践**：
  - 支持自定义请求选项（如`method`、`headers`），可将`options`作为第二个参数。
  - 处理非JSON响应或错误状态码。
  - 考虑加入缓存或防抖功能。

### 注意事项
- **常见错误**：
  - 忘记处理`AbortError`，导致错误状态被错误设置。
  - 依赖数组中遗漏某个依赖（如`url`中的查询参数）。
  - 在请求完成后更新已卸载组件。
- **性能问题**：每次URL变化都会取消旧请求并创建新请求，频繁变化可能导致大量请求，可结合防抖或缓存优化。
- **兼容性**：`AbortController`在现代浏览器中支持良好，旧浏览器可能需要polyfill。

### 代码示例
```javascript
import { useState, useEffect } from 'react';

function useFetch(url) {
  const [data, setData] = useState(null);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState(null);

  useEffect(() => {
    setLoading(true);
    setData(null);
    setError(null);

    const abortController = new AbortController();
    const { signal } = abortController;

    const fetchData = async () => {
      try {
        const response = await fetch(url, { signal });
        if (!response.ok) {
          throw new Error(`HTTP error! status: ${response.status}`);
        }
        const result = await response.json();
        if (!signal.aborted) {
          setData(result);
        }
      } catch (err) {
        if (!signal.aborted && err.name !== 'AbortError') {
          setError(err.message);
        }
      } finally {
        if (!signal.aborted) {
          setLoading(false);
        }
      }
    };

    fetchData();

    return () => {
      abortController.abort();
    };
  }, [url]);

  return { data, loading, error };
}

export default useFetch;
```

---

## 六、组合多个Hook：高级组合模式

### 概念
- **是什么**：组合多个Hook是指在自定义Hook内部调用其他自定义Hook，将多个独立的逻辑单元组合成一个功能更强大的新Hook。
- **为什么需要**：现实业务逻辑往往是多个基础逻辑的组合。通过组合，可以构建高层次的抽象，让组件代码更加简洁，逻辑复用更加灵活。
- **解决什么问题**：实现逻辑的**分层抽象**，避免组件直接处理大量分散的底层逻辑，提高代码的可维护性和可读性。

### 原理
- **底层实现**：与普通自定义Hook相同，组合Hook只是调用其他Hook，并将它们的返回值组合起来返回。React运行时会将所有嵌套Hook的调用挂载到**同一个组件**的Hook链表中。
- **工作流程**：
  1. 组件调用组合Hook（如`useUser`）。
  2. `useUser`内部依次调用`useFetch`、`useLocalStorage`等。
  3. 这些内部Hook的状态都被关联到当前组件。
  4. `useUser`将结果整合成新的对象或数组返回。
- **关键技术点**：逻辑分层、依赖传递、返回值设计。

### 使用方法
- **基本用法**：在一个自定义Hook内部直接调用其他自定义Hook，就像使用内置Hook一样。
- **常见场景**：
  - 构建领域特定Hook（如`useUser`、`useCart`、`useCheckout`）。
  - 将多个通用Hook组合成业务专用Hook。
- **最佳实践**：
  - 组合Hook应保持清晰的命名，体现其业务含义。
  - 内部调用的Hook应尽量通用且可测试。
  - 避免过深的嵌套组合，适当拆分。

### 注意事项
- **常见错误**：
  - 组合Hook内部调用的Hook之间存在隐式依赖，导致顺序耦合。
  - 返回的对象过于庞大，包含过多不相关属性。
  - 忘记遵循Hook规则（如条件调用内部Hook）。
- **性能问题**：组合多个Hook本身不会引入额外开销，但需注意内部优化（如`useMemo`）的使用。
- **兼容性**：与普通自定义Hook相同。

### 代码示例
```javascript
// 基础Hook：useFetch、useLocalStorage
import { useFetch } from './useFetch';
import { useLocalStorage } from './useLocalStorage';

// 组合Hook：useUser
function useUser(userId) {
  // 获取用户数据
  const { data: user, loading, error } = useFetch(`/api/users/${userId}`);

  // 持久化用户主题偏好
  const [theme, setTheme] = useLocalStorage(`user-theme-${userId}`, 'light');

  // 派生状态
  const isLoggedIn = !!user && !error;

  return {
    user,
    loading,
    error,
    isLoggedIn,
    theme,
    setTheme,
  };
}

// 在组件中使用
function UserProfile({ userId }) {
  const { user, loading, isLoggedIn, theme, setTheme } = useUser(userId);

  if (loading) return <div>Loading...</div>;
  return (
    <div>
      <h1>{user?.name}</h1>
      <p>Status: {isLoggedIn ? 'Online' : 'Offline'}</p>
      <button onClick={() => setTheme(theme === 'light' ? 'dark' : 'light')}>
        Toggle Theme
      </button>
    </div>
  );
}
```

---

## 七、自定义Hook的最佳实践与常见陷阱

### 概念
- **是什么**：最佳实践是一套指导原则，帮助开发者写出高质量、可维护、高性能的自定义Hook；常见陷阱则是开发者容易犯的错误，需主动规避。
- **为什么需要**：遵循最佳实践可以避免反模式，提高代码质量，减少Bug。
- **解决什么问题**：使自定义Hook更易于理解、测试和协作。

### 原理
- **底层实现**：基于React的设计哲学和Hooks的内部工作机制。
- **工作流程**：体现在代码的组织方式、优化手段的使用、依赖管理的策略上。
- **关键技术点**：单一职责、依赖数组、清理函数、性能优化。

### 使用方法
#### 最佳实践
1. **单一职责**：每个自定义Hook只做一件事，如果逻辑复杂，拆分为多个小Hook。
2. **显式命名**：名称清晰地描述功能（如`useWindowWidth`，而非`useResponsive`）。
3. **返回稳定引用**：使用`useCallback`和`useMemo`包装返回的函数和对象，避免不必要的子组件重渲染。
4. **处理清理**：对于订阅、定时器、请求等副作用，始终返回清理函数。
5. **依赖数组完整**：在`useEffect`、`useMemo`等中列出所有依赖，不要禁用`exhaustive-deps`规则。
6. **支持函数式更新**：对于setter，允许传入函数，与`useState`保持一致。
7. **编写文档**：使用JSDoc/TSDoc注释说明参数和返回值。
8. **测试Hook**：使用`@testing-library/react-hooks`等工具编写单元测试。

#### 常见陷阱
1. **陷阱：在条件中调用Hook** —— 违反Hook规则。
   - 解决：将条件移到Hook内部。
2. **陷阱：依赖数组缺失导致无限循环** —— `useEffect`中使用了某个值但未加入依赖。
   - 解决：添加依赖，或使用函数式更新。
3. **陷阱：闭包捕获过时值** —— 在异步操作中读取了旧的状态或props。
   - 解决：使用ref保存最新值，或确保依赖正确。
4. **陷阱：滥用useMemo/useCallback** —— 在不必要的地方使用，增加复杂度。
   - 解决：先写出清晰代码，遇到性能问题再优化。
5. **陷阱：忘记取消请求** —— 组件卸载后仍在更新状态。
   - 解决：使用AbortController或标志位。

### 注意事项
- **性能问题**：
  - 避免在自定义Hook中返回新的匿名对象或函数，除非用`useMemo`/`useCallback`包装。
  - 注意依赖数组中的引用类型变化，可能导致不必要重执行。
- **兼容性**：无特殊兼容性问题，但需注意某些浏览器API（如`localStorage`）的可用性。

### 代码示例
```javascript
// 最佳实践示例：useMediaQuery
import { useState, useEffect } from 'react';

function useMediaQuery(query) {
  // 使用useMemo缓存MediaQueryList对象，避免每次渲染都创建
  const mediaQueryList = useMemo(() => window.matchMedia(query), [query]);
  const [matches, setMatches] = useState(mediaQueryList.matches);

  useEffect(() => {
    const handler = (event) => setMatches(event.matches);
    mediaQueryList.addEventListener('change', handler);
    return () => mediaQueryList.removeEventListener('change', handler);
  }, [mediaQueryList]); // 依赖稳定的mediaQueryList

  return matches;
}

// 常见陷阱示例：过时闭包
function useCounter(initial) {
  const [count, setCount] = useState(initial);
  useEffect(() => {
    const id = setInterval(() => {
      setCount(count + 1); // 这里count是闭包捕获的旧值，每次加1但永远不会超过initial+1
    }, 1000);
    return () => clearInterval(id);
  }, []); // 依赖数组为空，导致闭包捕获初始count
  return count;
}
// 修正：使用函数式更新 setCount(c => c + 1);
```

---

以上笔记涵盖了React自定义Hook的完整知识体系，从基础规则到高级组合，每个知识点都按照模板结构化呈现，并提供了详尽的代码示例。希望这份笔记能帮助你全面掌握自定义Hook，并在实际项目中灵活运用。