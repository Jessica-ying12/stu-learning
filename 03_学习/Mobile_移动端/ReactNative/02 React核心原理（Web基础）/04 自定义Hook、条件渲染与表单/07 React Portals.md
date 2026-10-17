#### React Portals
##### 概念
- **是什么**  
  React Portals 是 React 提供的一种将子节点渲染到父组件 DOM 层次结构之外的 DOM 节点的机制。通过 `ReactDOM.createPortal(child, container)`，开发者可以将 JSX 内容“传送”到任意指定的 DOM 容器中，同时在 React 组件树中仍保持原有的逻辑位置。

- **为什么需要**  
  在标准 React 应用中，组件渲染结果会按照嵌套关系挂载到最近的父 DOM 节点下。这种严格的 DOM 层级在某些场景下会成为限制，例如：
  - 当父容器设置了 `overflow: hidden` 或 `transform` 时，其内部的绝对/固定定位元素可能被意外裁剪。
  - 当需要实现模态框、工具提示等浮动 UI 时，`z-index` 可能受到父容器影响而失效。
  - 从语义化 HTML 角度，模态框等应作为 `<body>` 的直接子元素，而非深埋在某个组件内部。

- **解决什么问题**  
  Portals 主要解决以下问题：
  - 突破 CSS 限制：允许内容脱离父容器的样式约束，渲染到任何 DOM 位置。
  - 保持 React 特性完整：虽然 DOM 结构改变，但 React 的事件系统、Context 等依然按组件树逻辑工作。
  - 提升组件可复用性：使开发者可以构建独立于父容器样式的 UI 组件（如模态框、提示框等）。

##### 原理
- **底层实现**  
  `ReactDOM.createPortal(child, container)` 是 React 提供的顶层 API。其内部实现利用了 React 的渲染器（renderer）机制，在协调（reconciliation）阶段将 `child` 对应的 Fiber 节点标记为 Portal 类型，并在提交（commit）阶段将其挂载到指定的 `container` DOM 节点上。

- **工作流程**  
  1. 组件调用 `createPortal` 返回一个 React 元素（包含特殊类型 `REACT_PORTAL_TYPE`）。
  2. 在渲染阶段，React 会构建 Fiber 树，Portal 节点的父 Fiber 仍然是调用它的组件，但 `stateNode` 会关联到外部 DOM 容器。
  3. 在提交阶段，React 将 Portal 的子节点渲染到 `container` 中，而不是常规的父 DOM 节点。
  4. 当组件更新或卸载时，React 会自动更新或移除 Portal 中的内容。

- **关键技术点**  
  - **事件冒泡的 React 树特性**：虽然 Portal 内容渲染到外部 DOM 节点，但其事件冒泡仍遵循 React 组件树结构。例如，点击 Portal 内的按钮，事件会沿着 React 树向上冒泡到调用 Portal 的祖先组件，而不是按照 DOM 树结构冒泡。这是通过 React 的事件代理机制实现的：React 在根节点监听事件，根据 Fiber 树结构模拟事件传播。
  - **Context 的保持**：由于 Portal 节点在 React 树中的位置不变，因此它可以正常访问祖先组件提供的 Context 值，不受 DOM 结构变化影响。
  - **Fiber 节点类型**：Portal 在 Fiber 树中对应 `HostPortal` 类型，其 `child` 指向 Portal 内容的子 Fiber，而 `return` 指向 React 树中的父 Fiber。

##### 使用方法
- **基本用法**  
  1. 在 HTML 中定义 Portal 目标容器（通常放在 `<body>` 内）：
     ```html
     <body>
       <div id="root"></div>
       <div id="portal-root"></div>
     </body>
     ```
  2. 在组件中使用 `ReactDOM.createPortal` 渲染内容：
     ```jsx
     import ReactDOM from 'react-dom';

     function Modal({ isOpen, children }) {
       if (!isOpen) return null;
       return ReactDOM.createPortal(
         <div className="modal">{children}</div>,
         document.getElementById('portal-root')
       );
     }
     ```

- **常见场景**  
  - **模态框（Modal）**：将模态框内容渲染到 `body` 下，避免被父容器裁剪或 `z-index` 干扰。
  - **工具提示（Tooltip）**：确保提示框始终可见，不被 `overflow: hidden` 裁剪。
  - **全局通知（Toast）**：统一在页面顶部或底部显示通知，与触发组件位置无关。
  - **下拉菜单（Dropdown）**：避免菜单被父容器裁剪，同时利用事件冒泡实现点击外部关闭。
  - **悬浮卡片（Hovercard）**：类似于工具提示，但可能包含更复杂的内容。

- **最佳实践**  
  - **动态创建容器**：为避免污染全局 HTML，可以在组件挂载时动态创建容器元素，并在卸载时清理：
    ```jsx
    useEffect(() => {
      const container = document.createElement('div');
      document.body.appendChild(container);
      // 使用 container 作为 portal 目标
      return () => document.body.removeChild(container);
    }, []);
    ```
  - **焦点管理**：对于模态框等交互组件，应管理焦点（打开时聚焦到模态框内，关闭时返回触发元素）以提升无障碍性。
  - **事件阻止**：在 Portal 内部，如果不想让点击事件冒泡到父组件，可调用 `e.stopPropagation()`。
  - **避免样式冲突**：由于 Portal 内容渲染到外部，其样式不会继承 React 父组件的 CSS 上下文。建议使用 CSS Modules 或 CSS-in-JS 隔离样式，或为 Portal 内容编写独立的样式文件。
  - **结合 Context**：利用 Context 保持状态一致性，例如在模态框内访问主题或用户信息。

##### 注意事项
- **常见错误**  
  - **忘记处理容器不存在**：在 SSR 或测试环境中，`document` 可能不存在，需先判断 `typeof document !== 'undefined'`。
  - **未清理动态创建的容器**：如果手动创建容器但忘记在卸载时移除，会导致内存泄漏。
  - **误用事件冒泡**：以为 Portal 内容在 DOM 树外部，父组件就无法捕获事件，但实际上事件依然可以冒泡到 React 父组件，可能导致意外行为。
  - **样式继承问题**：Portal 内容不会继承父组件的 CSS 类或作用域样式，需确保正确引入样式。

- **性能问题**  
  Portals 本身不会引入显著性能开销。React 会像处理普通节点一样对 Portal 内容进行协调和更新。但如果 Portal 内容频繁创建/销毁（如每次打开模态框都动态创建容器），建议复用容器或使用状态控制渲染。

- **兼容性**  
  - **React 版本**：`createPortal` 自 React 16 起稳定可用（React 15 有非官方支持的 `unstable_renderSubtreeIntoContainer`，但不推荐）。
  - **浏览器支持**：支持所有现代浏览器及 IE 11（需注意 `Element` 相关 API 的 polyfill）。
  - **服务端渲染（SSR）**：在 SSR 中，`document` 不存在，因此需在客户端渲染时（如 `useEffect` 或组件挂载后）执行 Portal 渲染，或使用条件判断避免服务端报错。

##### 代码示例
以下是一个完整的、遵循最佳实践的模态框组件示例，包含焦点管理、动态容器创建和事件处理：

```jsx
import React, { useEffect, useRef, useState } from 'react';
import ReactDOM from 'react-dom';
import './Modal.css'; // 独立样式

const Modal = ({ isOpen, onClose, title, children }) => {
  const modalRef = useRef(null); // 用于焦点管理
  const containerRef = useRef(null); // 用于存放动态创建的容器
  const [mounted, setMounted] = useState(false);

  // 动态创建容器（仅在客户端）
  useEffect(() => {
    if (typeof document !== 'undefined') {
      const div = document.createElement('div');
      document.body.appendChild(div);
      containerRef.current = div;
      setMounted(true);
    }
    return () => {
      if (containerRef.current) {
        document.body.removeChild(containerRef.current);
      }
    };
  }, []);

  // 打开时保存上一个焦点元素，关闭时恢复
  useEffect(() => {
    if (isOpen) {
      const previouslyFocused = document.activeElement;
      if (modalRef.current) {
        modalRef.current.focus();
      }
      return () => {
        if (previouslyFocused && previouslyFocused.focus) {
          previouslyFocused.focus();
        }
      };
    }
  }, [isOpen]);

  // 如果未挂载或关闭，不渲染内容
  if (!mounted || !isOpen) return null;

  return ReactDOM.createPortal(
    <div
      className="modal-overlay"
      onClick={onClose}
      role="dialog"
      aria-modal="true"
      aria-labelledby="modal-title"
    >
      <div
        className="modal-content"
        ref={modalRef}
        tabIndex={-1}
        onClick={(e) => e.stopPropagation()} // 防止点击内容区关闭
      >
        {title && <h2 id="modal-title">{title}</h2>}
        <div className="modal-body">{children}</div>
        <button onClick={onClose} className="modal-close-btn">
          关闭
        </button>
      </div>
    </div>,
    containerRef.current
  );
};

export default Modal;
```

**使用示例**：
```jsx
function App() {
  const [modalOpen, setModalOpen] = useState(false);
  return (
    <div>
      <button onClick={() => setModalOpen(true)}>打开模态框</button>
      <Modal
        isOpen={modalOpen}
        onClose={() => setModalOpen(false)}
        title="温馨提示"
      >
        <p>这是一个通过 Portal 渲染的模态框，它位于 body 下，但事件仍能被 App 组件捕获。</p>
      </Modal>
    </div>
  );
}
```

该示例展示了：
- 动态创建容器并自动清理
- 焦点管理（打开时聚焦到模态框内容）
- 防止点击内容区关闭（通过 `stopPropagation`）
- ARIA 属性增强无障碍性
- 独立样式隔离