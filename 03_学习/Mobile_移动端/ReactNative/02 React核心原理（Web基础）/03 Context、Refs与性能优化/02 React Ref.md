## 一、useRef 基础

### 知识点名称：useRef

#### 概念
- **是什么**：`useRef` 是 React 提供的一个 Hook，它返回一个可变的 ref 对象，该对象在组件的整个生命周期内保持不变。其 `.current` 属性可以被初始化为任意值，并且修改 `.current` 不会触发组件重新渲染。
- **为什么需要**：在函数组件中，我们需要一种方式来存储那些不需要触发 UI 更新的数据，或者直接访问 DOM 元素。传统的变量在每次渲染时都会被重置，而 state 的更新又会引起重新渲染，因此需要 `useRef` 来填补这个需求。
- **解决什么问题**：解决了跨渲染周期保存数据的问题，以及命令式访问 DOM 元素的需求。

#### 原理
- **底层实现**：`useRef` 本质上是在 React 的 Fiber 节点上挂载一个对象，该对象在组件的多次渲染之间共享同一个引用。当组件卸载时，这个对象会被垃圾回收。
- **工作流程**：
  1. 在组件首次渲染时，`useRef(initialValue)` 创建一个 `{ current: initialValue }` 的对象，并存储在组件的 Fiber 节点中。
  2. 后续渲染中，`useRef` 直接返回同一个对象，保证引用不变。
  3. 对 `ref.current` 的修改是同步的，但不会触发调度更新，因此不会导致组件重新渲染。
- **关键技术点**：利用闭包和 Fiber 架构维持引用不变；`.current` 属性是可变的，允许直接赋值。

#### 使用方法
- **基本用法**：
  ```jsx
  import { useRef } from 'react';

  function MyComponent() {
    const myRef = useRef(initialValue);
    // 读取：myRef.current
    // 写入：myRef.current = newValue
  }
  ```
- **常见场景**：
  - 存储定时器 ID
  - 保存上一次的 props 或 state
  - 保存第三方库实例
  - 与 DOM 元素交互（详见下文）
- **最佳实践**：
  - 只在副作用或事件处理函数中读写 `ref.current`，避免在渲染期间读写。
  - 对于开销大的初始值，采用懒初始化：`if (!ref.current) ref.current = new SomeClass()`。

#### 注意事项
- **常见错误**：
  - 在渲染函数中直接修改 `ref.current`，可能导致渲染结果不可预测。
  - 将 ref 用作依赖项传递给 useEffect，因为 ref 本身不变，但 .current 变化不会触发 effect。
- **性能问题**：无显著性能问题，但频繁读写 .current 不会影响渲染性能。
- **兼容性**：`useRef` 自 React 16.8（Hooks 引入）起可用，支持所有现代浏览器。

#### 代码示例
```jsx
import { useRef, useEffect } from 'react';

function Timer() {
  const intervalRef = useRef(null);

  useEffect(() => {
    intervalRef.current = setInterval(() => {
      console.log('tick');
    }, 1000);

    return () => clearInterval(intervalRef.current);
  }, []);

  return <div>Check console for ticks</div>;
}
```

---

## 二、访问 DOM 元素

### 知识点名称：访问 DOM 元素

#### 概念
- **是什么**：通过将 `useRef` 创建的 ref 对象赋值给 React 元素的 `ref` 属性，可以在组件挂载后获取到对应的真实 DOM 节点，并对其进行命令式操作。
- **为什么需要**：某些情况下需要直接操作 DOM，例如管理焦点、媒体播放、测量元素尺寸、集成第三方非 React 库等。React 的声明式模型无法完全覆盖这些命令式场景。
- **解决什么问题**：提供了一种安全、便捷的方式与底层 DOM 交互，同时保持 React 的渲染一致性。

#### 原理
- **底层实现**：当 React 渲染一个包含 `ref` 属性的元素时，在 commit 阶段会将对应的 DOM 节点赋值给 `ref.current`。如果 ref 是函数，则会调用该函数并传入 DOM 节点。在卸载时，会将 `ref.current` 置为 `null`（或调用函数传入 `null`）。
- **工作流程**：
  1. 组件渲染，JSX 中带有 `ref={refObject}`。
  2. React 在构建 Fiber 树时记录该 ref。
  3. 在浏览器完成布局与绘制后，React 将真实 DOM 节点赋值给 `refObject.current`。
  4. 组件卸载或 ref 变更时，`refObject.current` 被置为 `null`。
- **关键技术点**：Ref 的附着发生在 DOM 更新之后，因此可以在 `useEffect` 或事件处理中安全地访问 `ref.current`。

#### 使用方法
- **基本用法**：
  ```jsx
  import { useRef, useEffect } from 'react';

  function TextInput() {
    const inputRef = useRef(null);

    useEffect(() => {
      inputRef.current.focus(); // 组件挂载后自动聚焦
    }, []);

    return <input ref={inputRef} type="text" />;
  }
  ```
- **常见场景**：
  - 焦点管理（如自动聚焦输入框、管理模态框焦点）
  - 媒体控制（视频播放、暂停）
  - 与第三方 DOM 库集成（如 D3、Chart.js）
  - 获取元素位置或尺寸（配合 `getBoundingClientRect`）
- **最佳实践**：
  - 尽量使用 React 的声明式方式控制 UI，仅在必要时使用 ref 操作 DOM。
  - 在 `useEffect` 或事件处理函数中访问 `ref.current`，因为此时 DOM 已就绪。
  - 避免在渲染期间直接访问 `ref.current`，因为它可能尚未赋值。

#### 注意事项
- **常见错误**：
  - 在组件初次渲染时（未挂载）就访问 `ref.current`，得到 `null`。
  - 对函数组件使用 `ref` 属性而不使用 `forwardRef`，导致 ref 无法附着到内部 DOM。
- **性能问题**：直接操作 DOM 可能绕过 React 的差异更新，应谨慎使用，避免引起布局抖动或与 React 状态不一致。
- **兼容性**：完全兼容所有浏览器，ref 附着机制与 React 版本相关，但基本行为一致。

#### 代码示例
```jsx
import { useRef } from 'react';

function VideoPlayer() {
  const videoRef = useRef(null);

  const play = () => videoRef.current.play();
  const pause = () => videoRef.current.pause();

  return (
    <div>
      <video ref={videoRef} src="video.mp4" />
      <button onClick={play}>Play</button>
      <button onClick={pause}>Pause</button>
    </div>
  );
}
```

---

## 三、保存可变值

### 知识点名称：保存可变值

#### 概念
- **是什么**：利用 `useRef` 的 `.current` 属性可以存储任意值，且这些值在组件的多次渲染之间保持不变，修改它们不会触发重新渲染。
- **为什么需要**：有些数据需要跨渲染周期保留，但与 UI 无关，例如定时器 ID、订阅句柄、前一次渲染的 props/state 等。如果使用 state 存储，每次修改都会导致不必要的重绘。
- **解决什么问题**：提供了一种“实例变量”的替代方案，让函数组件拥有类似类组件实例属性的能力。

#### 原理
- **底层实现**：`useRef` 返回的对象在组件整个生命周期内引用不变，其 `.current` 属性可以自由修改。React 并不会追踪 `.current` 的变化，因此不会触发更新。
- **工作流程**：
  1. 首次渲染时，`useRef` 初始化一个对象并保存。
  2. 后续渲染中，始终返回同一个对象。
  3. 组件卸载后，该对象被垃圾回收。
- **关键技术点**：利用 JavaScript 对象引用不变性，实现跨渲染周期的数据存储。

#### 使用方法
- **基本用法**：
  ```jsx
  import { useRef } from 'react';

  function MyComponent() {
    const latestValueRef = useRef(initialValue);
    // 更新：latestValueRef.current = newValue;
    // 读取：const value = latestValueRef.current;
  }
  ```
- **常见场景**：
  - 存储定时器 ID 以便清除。
  - 保存上一次的 props 或 state 用于比较（如 `usePrevious` 自定义 Hook）。
  - 存储 WebSocket 连接、ResizeObserver 实例等。
  - 在多次渲染间共享数据，而不引起额外渲染。
- **最佳实践**：
  - 仅在副作用或事件处理中更新 ref，避免在渲染过程中修改。
  - 如果需要根据最新 props 更新 ref，可以在 `useEffect` 中同步。
  - 将 ref 用于性能优化，避免不必要的重新计算或渲染。

#### 注意事项
- **常见错误**：
  - 在渲染函数中依赖 `ref.current` 进行条件判断，可能导致渲染结果与数据不同步（因为 ref 变化不会触发重绘）。
  - 忘记清理 ref 中保存的订阅或定时器，造成内存泄漏。
- **性能问题**：无直接性能问题，但滥用 ref 存储大量数据可能导致内存占用。
- **兼容性**：React 16.8+ 支持。

#### 代码示例
```jsx
import { useRef, useEffect } from 'react';

// 自定义 Hook：获取前一次的 props 或 state
function usePrevious(value) {
  const ref = useRef();
  useEffect(() => {
    ref.current = value;
  }, [value]);
  return ref.current;
}

function Counter({ count }) {
  const prevCount = usePrevious(count);
  return (
    <div>
      Now: {count}, before: {prevCount}
    </div>
  );
}
```

---

## 四、forwardRef

### 知识点名称：forwardRef

#### 概念
- **是什么**：`forwardRef` 是一个 React 高阶组件（HOC），它允许函数组件接收一个 `ref` 参数，并将该 `ref` 转发给其内部的子组件（通常是 DOM 元素或另一个支持 ref 的组件）。
- **为什么需要**：在 React 中，函数组件默认不接受 `ref` 属性（除非使用 `forwardRef`）。如果父组件需要直接操作子组件内的 DOM 节点或组件实例，就需要通过 `forwardRef` 显式传递 ref。
- **解决什么问题**：解决了 ref 无法透传的问题，使得父组件能够访问子组件内部的特定元素，同时保持组件的封装性。

#### 原理
- **底层实现**：`forwardRef` 接收一个渲染函数 `(props, ref) => ReactNode`，返回一个组件。当这个组件被使用时，React 会将父组件传递的 `ref` 作为第二个参数传入渲染函数，然后由开发者决定将其附加到哪个元素上。
- **工作流程**：
  1. 父组件创建 ref 并使用 `ref={myRef}` 传递给子组件。
  2. 子组件由 `forwardRef` 包裹，其渲染函数接收到 `props` 和 `ref`。
  3. 在渲染函数内部，将接收到的 `ref` 附加到内部的某个元素（如 `<input ref={ref} />`）。
  4. 父组件即可通过 `myRef.current` 访问该内部元素。
- **关键技术点**：ref 参数的接收和转发；React 对 `forwardRef` 组件的特殊处理。

#### 使用方法
- **基本用法**：
  ```jsx
  import { forwardRef, useRef } from 'react';

  const MyInput = forwardRef((props, ref) => {
    return <input {...props} ref={ref} />;
  });

  function Parent() {
    const inputRef = useRef(null);
    const focusInput = () => inputRef.current.focus();

    return (
      <>
        <MyInput ref={inputRef} placeholder="Type here" />
        <button onClick={focusInput}>Focus</button>
      </>
    );
  }
  ```
- **常见场景**：
  - 封装可复用的表单组件，允许父组件直接操作内部输入框（如聚焦、选择文本）。
  - 在 HOC 中转发 ref，确保 ref 指向被包裹的组件。
  - 库作者需要暴露内部 DOM 节点给用户。
- **最佳实践**：
  - 明确文档说明通过 `ref` 暴露了哪些功能。
  - 如果子组件还使用了 `useImperativeHandle`，则与 `forwardRef` 配合使用。
  - 在 React 19 中可以直接将 `ref` 作为 prop 传递，无需 `forwardRef`，但旧版本仍需要。

#### 注意事项
- **常见错误**：
  - 忘记使用 `forwardRef` 导致 ref 被忽略（在 React 18 及以前）。
  - 将 ref 附加到错误的元素上，暴露了不该暴露的 DOM 节点。
  - 在类组件上使用 `forwardRef` 是不必要的，因为类组件本身可以接受 ref 指向实例。
- **性能问题**：`forwardRef` 本身不会引入性能开销，但滥用 ref 传递可能导致过度暴露内部结构，增加维护成本。
- **兼容性**：`forwardRef` 自 React 16.3 引入，在 React 19 中被标记为 deprecated，建议使用 prop 传递 ref。

#### 代码示例
```jsx
import { forwardRef, useRef } from 'react';

const FancyButton = forwardRef(({ children, ...props }, ref) => (
  <button ref={ref} className="fancy" {...props}>
    {children}
  </button>
));

function App() {
  const buttonRef = useRef(null);
  useEffect(() => {
    buttonRef.current.focus(); // 父组件可以聚焦按钮
  }, []);
  return <FancyButton ref={buttonRef}>Click me</FancyButton>;
}
```

---

## 五、useImperativeHandle

### 知识点名称：useImperativeHandle

#### 概念
- **是什么**：`useImperativeHandle` 是一个 Hook，它与 `forwardRef` 配合使用，允许你自定义通过 `ref` 暴露给父组件的实例值或方法。这样，父组件就无法访问到子组件内部的所有内容，只能访问你指定的方法。
- **为什么需要**：直接暴露整个 DOM 节点或组件实例给父组件可能带来安全风险或过度耦合。我们可能只想让父组件能够调用某些特定方法（如 `focus`、`scrollTo`），而不希望它随意修改内部结构。
- **解决什么问题**：解决了 ref 暴露粒度控制的问题，实现了更精细的封装，符合最少知识原则。

#### 原理
- **底层实现**：`useImperativeHandle` 在组件渲染时执行，它接收一个 ref（通常是 `forwardRef` 传下来的 ref）和一个创建句柄的函数。React 会将这个句柄对象设置为 `ref.current` 的值，从而覆盖原本可能指向 DOM 节点的引用。
- **工作流程**：
  1. 子组件使用 `forwardRef` 接收父组件传递的 ref。
  2. 子组件内部创建自己的内部 ref（如 `internalRef`），指向实际的 DOM 节点。
  3. 调用 `useImperativeHandle(ref, createHandle, deps)`，其中 `createHandle` 返回一个对象，包含想要暴露的方法（这些方法内部可以使用 `internalRef.current` 操作 DOM）。
  4. 父组件通过传递的 ref 只能访问到 `createHandle` 返回的对象中的方法，无法直接操作内部 DOM。
- **关键技术点**：通过自定义句柄覆盖原始 ref；依赖项数组控制句柄更新。

#### 使用方法
- **基本用法**：
  ```jsx
  import { forwardRef, useRef, useImperativeHandle } from 'react';

  const MyInput = forwardRef((props, ref) => {
    const inputRef = useRef(null);

    useImperativeHandle(ref, () => ({
      focus: () => {
        inputRef.current.focus();
      },
      clear: () => {
        inputRef.current.value = '';
      }
    }), []);

    return <input ref={inputRef} {...props} />;
  });

  function Parent() {
    const inputRef = useRef(null);
    return (
      <>
        <MyInput ref={inputRef} />
        <button onClick={() => inputRef.current.focus()}>聚焦</button>
        <button onClick={() => inputRef.current.clear()}>清空</button>
      </>
    );
  }
  ```
- **常见场景**：
  - 限制父组件对子组件的操作，只暴露必要的命令式 API（如 `focus`、`reset`、`scrollToTop`）。
  - 组合多个内部方法为一个复合方法（如 `validateAndSubmit`）。
  - 为第三方库封装组件时，提供受控的命令式接口。
- **最佳实践**：
  - 保持暴露的 API 简洁、明确。
  - 使用依赖项数组来控制句柄的更新，避免过时的闭包。
  - 优先考虑声明式 props 而非命令式方法，仅在必要时使用 `useImperativeHandle`。

#### 注意事项
- **常见错误**：
  - 忘记与 `forwardRef` 配合使用，导致 `ref` 未传递。
  - 在依赖项数组中遗漏了需要的值，导致方法中使用了过时的 props 或 state。
  - 暴露过多方法，破坏了组件的封装性。
- **性能问题**：`useImperativeHandle` 本身开销很小，但注意不要在依赖项变化时频繁重建句柄对象。
- **兼容性**：自 React 16.8 引入。

#### 代码示例
```jsx
import { forwardRef, useRef, useImperativeHandle, useState } from 'react';

const Counter = forwardRef((props, ref) => {
  const [count, setCount] = useState(0);
  const increment = () => setCount(c => c + 1);

  useImperativeHandle(ref, () => ({
    increment,
    getCount: () => count,
  }), [increment, count]); // 依赖 count 确保 getCount 返回最新值

  return <div>Count: {count}</div>;
});

function Parent() {
  const counterRef = useRef(null);
  return (
    <div>
      <Counter ref={counterRef} />
      <button onClick={() => counterRef.current.increment()}>Increment from parent</button>
      <button onClick={() => alert(counterRef.current.getCount())}>Show count</button>
    </div>
  );
}
```

---

以上便是围绕你提供的五个核心知识点整理的详细学习笔记，每个知识点都严格按照模板要求展开，确保内容完整、权威且实用。希望这份笔记能帮助你深入理解 React Ref 的各个方面。