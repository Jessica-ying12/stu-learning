## 一、useLayoutEffect 基础概念

### useLayoutEffect 基础概念

##### 概念
- **是什么**：`useLayoutEffect` 是 React 提供的一个 Hook，它是 `useEffect` 的一个版本，但其**执行时机**截然不同——它在**浏览器重新绘制屏幕之前同步触发**。
- **为什么需要**：在浏览器进行任何视觉更新（绘制）**之前**，有时必须完成某些操作，以确保用户看到的 UI 是最终且完美的状态，避免出现视觉上的不一致或“闪烁”。
- **解决什么问题**：解决需要在渲染前读取布局或同步修改 DOM 的问题，例如根据元素尺寸调整位置、在用户看到元素前聚焦输入框、初始化需要直接操作 DOM 的第三方库等，防止出现中间状态导致的视觉抖动。

##### 原理
- **底层实现**：React 在**提交阶段（commit phase）** 同步执行 `useLayoutEffect` 的回调。此时 DOM 已根据最新状态完成更新，但浏览器尚未开始绘制屏幕像素。
- **工作流程**：
  1. React 计算出需要变更的 DOM 节点并更新内存中的 DOM。
  2. **同步调用** `useLayoutEffect`（此时浏览器未绘制）。
  3. 浏览器根据最终 DOM 结构和样式绘制屏幕，用户看到新内容。
  4. **异步调用** `useEffect`（绘制完成后执行）。
- **关键技术点**：利用浏览器的事件循环机制，在微任务之后、绘制之前执行，从而**阻塞渲染**，确保副作用在用户看到界面之前完成。

##### 使用方法
- **基本用法**：`useLayoutEffect(setup, dependencies?)`，其函数签名与 `useEffect` 完全一致。
  ```javascript
  useLayoutEffect(() => {
    // 副作用逻辑（同步执行，阻塞绘制）
    return () => {
      // 清理函数（可选）
    };
  }, [dependencies]);
  ```
- **常见场景**：
  - 测量 DOM 节点的尺寸或位置（如 `getBoundingClientRect`）。
  - 同步修改 DOM（如手动滚动、聚焦输入框）。
  - 初始化需要直接操作 DOM 的第三方库。
  - 避免因异步执行导致的 UI 闪烁。
- **最佳实践**：默认优先使用 `useEffect`，只有在确实需要“在绘制前完成”的特性时才改用 `useLayoutEffect`。使用前应评估是否真的会因延迟导致可见的 UI 问题。

##### 注意事项
- **常见错误**：
  - 在服务端渲染（SSR）中使用 `useLayoutEffect`，会导致警告：“`useLayoutEffect` does nothing on the server”。
  - 在循环或条件语句中调用 Hook（违反 Hook 规则）。
- **性能问题**：因为会阻塞浏览器绘制，如果回调中包含耗时操作，会显著降低页面响应速度。应避免在 `useLayoutEffect` 中执行复杂计算或大量 DOM 操作。
- **兼容性**：所有支持 Hook 的 React 版本（16.8+）均支持 `useLayoutEffect`。但在 SSR 框架（如 Next.js）中需要特殊处理（见第三部分）。

##### 代码示例
```javascript
import { useLayoutEffect, useRef } from 'react';

function AutoFocusInput() {
  const inputRef = useRef(null);

  useLayoutEffect(() => {
    // 在浏览器绘制前，输入框已被聚焦
    inputRef.current.focus();
  }, []); // 空依赖，仅在挂载时执行

  return <input ref={inputRef} type="text" placeholder="自动聚焦" />;
}
```

---

## 二、useLayoutEffect 核心用途与实战

### useLayoutEffect 核心用途与实战

##### 概念
- **是什么**：深入探讨 `useLayoutEffect` 的实际应用场景，特别是那些必须同步执行才能保证视觉一致性的情况。
- **为什么需要**：许多布局相关的副作用（如根据元素尺寸调整位置）如果放在 `useEffect` 中执行，用户会先看到错误的布局，然后瞬间被纠正，产生令人不悦的“闪烁”。`useLayoutEffect` 通过阻塞渲染，将中间状态隐藏起来。
- **解决什么问题**：解决布局测量、避免闪烁、同步 DOM 操作等问题，确保用户始终看到的是最终稳定的 UI。

##### 原理
- **底层实现**：利用**两步渲染**策略：第一次渲染（占位）→ 测量 → 同步更新状态 → 第二次渲染（最终）→ 绘制。
- **工作流程**（以 Tooltip 为例）：
  1. **首次渲染**：渲染一个临时占位的 Tooltip（例如高度为 0 或默认位置），此时用户**看不到**，因为 `useLayoutEffect` 阻塞了绘制。
  2. **同步测量**：在 `useLayoutEffect` 中通过 `ref` 获取 Tooltip 的真实尺寸。
  3. **更新状态**：调用 `setState` 触发同步重渲染。
  4. **第二次渲染**：使用计算出的正确位置渲染最终 Tooltip。
  5. **浏览器绘制**：用户直接看到位置正确的 Tooltip。
- **关键技术点**：结合 `useRef` 访问真实 DOM，利用 `useState` 触发同步更新，借助 `useLayoutEffect` 的阻塞特性隐藏中间状态。

##### 使用方法
- **基本用法**：通常与 `useState` 和 `useRef` 配合使用，在 `useLayoutEffect` 中读取 DOM 属性并更新状态。
- **常见场景**：
  - **Tooltip / Popover 位置自适应**：根据触发元素和自身尺寸计算最佳展示位置。
  - **响应式导航菜单**：在有限宽度内决定哪些菜单项隐藏到“更多”下拉中。
  - **初始化第三方库**：如图表库、拖拽库，确保库在首次绘制前接管 DOM。
  - **同步滚动位置**：例如聊天窗口滚动到底部。
- **最佳实践**：
  - 将测量逻辑封装成自定义 Hook，提高复用性。
  - 依赖数组要准确，避免不必要的重复测量。
  - 如果测量结果依赖窗口大小变化，可结合 `resize` 事件监听。

##### 注意事项
- **常见错误**：
  - 在 `useLayoutEffect` 中触发状态更新后未正确处理依赖，导致无限循环。
  - 忘记清理副作用（如事件监听、定时器）。
- **性能问题**：多次测量（例如在动画中频繁执行）可能影响性能，应尽量减少测量次数，或使用 `requestAnimationFrame` 等优化手段。
- **兼容性**：纯客户端渲染场景无问题，SSR 场景需使用动态导入或客户端专属方案（见第三部分）。

##### 代码示例
```javascript
import { useLayoutEffect, useRef, useState } from 'react';
import { createPortal } from 'react-dom';

function Tooltip({ children, targetRect }) {
  const ref = useRef(null);
  const [tooltipHeight, setTooltipHeight] = useState(0);

  // 测量真实高度并同步更新状态
  useLayoutEffect(() => {
    const { height } = ref.current.getBoundingClientRect();
    setTooltipHeight(height);
  }, []); // 只在挂载时测量一次

  // 计算最终位置
  let tooltipX = 0;
  let tooltipY = 0;
  if (targetRect) {
    tooltipX = targetRect.left;
    tooltipY = targetRect.top - tooltipHeight;
    if (tooltipY < 0) {
      // 上方空间不够，放在下面
      tooltipY = targetRect.bottom;
    }
  }

  return createPortal(
    <div
      ref={ref}
      style={{
        position: 'absolute',
        left: tooltipX,
        top: tooltipY,
        backgroundColor: 'black',
        color: 'white',
        padding: '5px',
        borderRadius: '4px',
      }}
    >
      {children}
    </div>,
    document.body
  );
}
```

---

## 三、useLayoutEffect 注意事项与最佳实践

### useLayoutEffect 注意事项与最佳实践

##### 概念
- **是什么**：总结使用 `useLayoutEffect` 时必须注意的关键点和最佳实践，帮助开发者避免常见陷阱。
- **为什么需要**：尽管 `useLayoutEffect` 功能强大，但误用会导致性能问题、SSR 错误等。掌握这些注意事项能写出更健壮的代码。
- **解决什么问题**：解决因使用不当引发的性能下降、SSR 兼容性、无限循环等问题。

##### 原理
- **底层实现**：
  - React 调度机制：`useLayoutEffect` 在提交阶段同步执行，而 `useEffect` 在绘制后异步执行。
  - 严格模式下的额外执行：开发环境中，React 会故意在挂载前额外运行一次 `setup + cleanup`，以检测副作用清理是否正确。
- **关键技术点**：
  - 依赖数组：React 使用 `Object.is` 进行浅比较，决定是否重新运行 Effect。
  - 清理函数：在组件卸载或重新运行前执行，用于取消订阅、清除定时器。

##### 使用方法
- **基本用法**：正确设置依赖数组，确保 Effect 只在必要时运行；实现清理函数避免内存泄漏。
- **常见场景**：
  - **订阅外部事件**：如 `resize`、`scroll`，并在清理函数中取消订阅。
  - **定时器操作**：在清理函数中清除 `setTimeout`/`setInterval`。
  - **避免无限循环**：确保依赖数组包含所有响应式值，且状态更新不会触发不必要的重新运行。
- **最佳实践**：
  1. **默认选择 `useEffect`**：除非必须同步执行，否则优先使用 `useEffect`。
  2. **SSR 兼容性**：在服务端渲染中使用客户端专属方案，例如：
     - 动态导入组件并关闭 SSR（`next/dynamic` 的 `ssr: false`）。
     - 使用 `useEffect` 检测客户端后再渲染包含 `useLayoutEffect` 的组件。
  3. **使用 `useMemo`/`useCallback` 稳定依赖**：避免因对象/函数引用变化导致 Effect 频繁执行。

##### 注意事项
- **常见错误**：
  - **在 `useLayoutEffect` 中执行数据获取**：数据获取通常是异步的，且不需要阻塞绘制，应放在 `useEffect` 中。
  - **依赖数组遗漏**：导致 Effect 使用过时的值，产生 bug。
  - **对象/函数作为依赖**：如果每次渲染都创建新的引用，即使内容相同也会触发重新运行。
- **性能问题**：
  - `useLayoutEffect` 会阻塞绘制，应避免在其中执行复杂计算或同步网络请求。
  - 测量操作应尽量缓存结果，避免多次测量。
- **兼容性**：
  - **SSR 问题**：在服务端无法运行，会导致 hydration 不匹配错误。解决方案：
    ```javascript
    function ClientOnlyComponent() {
      const [isClient, setIsClient] = useState(false);
      useEffect(() => { setIsClient(true); }, []);
      if (!isClient) return <Fallback />;
      return <RealComponent />; // 这里可以使用 useLayoutEffect
    }
    ```
  - **React 版本**：需 React 16.8 及以上。

##### 代码示例
```javascript
import { useLayoutEffect, useRef, useState, useCallback } from 'react';

// 自定义 Hook：测量元素尺寸，并在窗口大小变化时重新测量
function useElementSize() {
  const ref = useRef(null);
  const [size, setSize] = useState({ width: 0, height: 0 });

  const measure = useCallback(() => {
    if (ref.current) {
      const { width, height } = ref.current.getBoundingClientRect();
      setSize({ width, height });
    }
  }, []);

  useLayoutEffect(() => {
    measure(); // 首次测量
    window.addEventListener('resize', measure);
    return () => window.removeEventListener('resize', measure);
  }, [measure]);

  return [ref, size];
}

// 使用示例
function ResizableComponent() {
  const [ref, size] = useElementSize();
  return (
    <div ref={ref} style={{ border: '1px solid black' }}>
      当前尺寸：{size.width} x {size.height}
    </div>
  );
}
```

---

以上笔记涵盖了 `useLayoutEffect` 的核心概念、原理、使用方法、注意事项和代码示例，确保知识点全面且结构清晰，可直接用于学习参考。