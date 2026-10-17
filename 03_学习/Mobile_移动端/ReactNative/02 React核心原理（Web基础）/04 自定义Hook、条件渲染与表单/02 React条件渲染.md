## 一、if语句条件渲染

### 概念
- **是什么**：在React函数组件或类组件的render方法中，直接使用JavaScript的`if`语句根据条件返回不同的JSX内容。
- **为什么需要**：当渲染逻辑复杂，涉及多个分支或需要提前返回时，`if`语句提供了最直观、可读性强的控制流。
- **解决什么问题**：解决多条件分支下的UI动态切换问题，尤其是在逻辑不能简洁地用一行表达式表达时。

### 原理
- **底层实现**：React组件本质上是函数（或类），`if`语句在函数体内执行，根据条件决定返回哪个JSX对象。JSX最终被编译为`React.createElement`调用，因此条件返回不同的`createElement`结果。
- **工作流程**：组件每次渲染时，函数重新执行，`if`语句重新判断，返回对应的JSX树，React对比虚拟DOM并更新实际DOM。
- **关键技术点**：利用JavaScript原生控制流；可以在条件内返回`null`以阻止渲染。

### 使用方法
- **基本用法**：
  ```jsx
  function Greeting({ isLoggedIn }) {
    if (isLoggedIn) {
      return <h1>欢迎回来！</h1>;
    }
    return <h1>请先登录。</h1>;
  }
  ```
- **常见场景**：
  - 多条件分支（如用户角色不同渲染不同界面）
  - 数据加载状态判断（加载中、成功、失败）
  - 权限控制（是否有权限渲染某模块）
- **最佳实践**：
  - 优先使用早期返回（early return）减少嵌套
  - 当分支超过两个时，`if...else if...else`比嵌套三元更清晰
  - 提取复杂逻辑到单独函数或变量中

### 注意事项
- **常见错误**：
  - 在`if`条件中错误地使用赋值（`=`）而非比较（`==`/`===`）
  - 忘记在条件分支中返回JSX，导致组件返回`undefined`（React会报错）
- **性能问题**：无显著性能影响，每次渲染都会重新执行条件判断。
- **兼容性**：完全依赖JavaScript语法，无兼容性问题。

### 代码示例
```jsx
function UserDashboard({ user }) {
  // 早期返回：未登录
  if (!user) {
    return <LoginPrompt />;
  }

  // 角色判断
  if (user.role === 'admin') {
    return <AdminPanel user={user} />;
  }

  if (user.role === 'editor') {
    return <EditorPanel user={user} />;
  }

  // 默认情况
  return <ViewerPanel user={user} />;
}
```

---

## 二、三元运算符条件渲染

### 概念
- **是什么**：在JSX中使用JavaScript的三元运算符（`condition ? exprIfTrue : exprIfFalse`）进行内联条件渲染。
- **为什么需要**：提供一种简洁的方式在JSX内部实现“二选一”的渲染，避免将条件逻辑分离到外部。
- **解决什么问题**：解决简单、紧凑的条件渲染需求，保持代码的线性结构。

### 原理
- **底层实现**：三元运算符是JavaScript表达式，在JSX的`{}`中执行，计算结果直接作为JSX的一部分被`React.createElement`处理。
- **工作流程**：渲染时，表达式被求值，返回对应的JSX片段（可以是字符串、数字、元素或元素数组），然后插入到父节点中。
- **关键技术点**：表达式必须返回有效的React节点（`ReactNode`），如JSX元素、字符串、数字、`null`等。

### 使用方法
- **基本用法**：
  ```jsx
  function Item({ name, isPacked }) {
    return <li>{isPacked ? name + ' ✅' : name}</li>;
  }
  ```
- **常见场景**：
  - 渲染两种不同样式的元素（如已读/未读）
  - 根据条件显示不同的文本或图标
  - 条件性地渲染属性值（如`className`）
- **最佳实践**：
  - 保持表达式简单，避免嵌套超过两层
  - 如果三元表达式变得复杂，考虑提取子组件或使用`if`语句
  - 可用括号包裹JSX提高可读性：`{condition ? <A /> : <B />}`

### 注意事项
- **常见错误**：
  - 忘记为`false`分支提供表达式，导致语法错误（三元必须有两个结果）
  - 在嵌套三元时造成可读性灾难
- **性能问题**：无额外性能开销。
- **兼容性**：ES5语法，兼容所有浏览器。

### 代码示例
```jsx
function StatusBadge({ isActive }) {
  return (
    <span className={isActive ? 'badge-active' : 'badge-inactive'}>
      {isActive ? '活跃' : '离线'}
    </span>
  );
}

// 避免深度嵌套示例（不推荐）：
// return <div>{a ? (b ? 'B' : 'A') : (c ? 'C' : 'D')}</div>;
```

---

## 三、逻辑与(&&)条件渲染

### 概念
- **是什么**：利用JavaScript逻辑与（`&&`）运算符的特性，在JSX中实现“条件为真时渲染，否则不渲染”。
- **为什么需要**：当只需要在条件满足时显示内容，否则无任何输出时，`&&`比三元更简洁（无需`: null`）。
- **解决什么问题**：解决“显示/隐藏”类需求，如根据条件展示徽章、提示信息或额外UI元素。

### 原理
- **底层实现**：JavaScript中`expr1 && expr2`的求值规则：如果`expr1`为真，返回`expr2`；否则返回`expr1`。React渲染时，如果返回`false`、`null`、`undefined`，则渲染空（不输出任何内容），但注意`0`和`NaN`会被渲染。
- **工作流程**：条件为真时，右侧JSX被求值并渲染；条件为假时，左侧的假值被返回，React根据假值类型决定是否渲染。
- **关键技术点**：理解真值/假值概念，以及React对不同类型的处理差异。

### 使用方法
- **基本用法**：
  ```jsx
  function Notification({ count }) {
    return (
      <div>
        你有 {count} 条新消息
        {count > 0 && <span className="badge">{count}</span>}
      </div>
    );
  }
  ```
- **常见场景**：
  - 根据数据是否存在渲染列表或占位
  - 权限控制显示特定按钮
  - 加载状态显示“加载中”提示
- **最佳实践**：
  - 左侧条件应始终为布尔值或可安全转换为布尔值的表达式
  - 当条件可能为`0`时，显式转换为布尔：`{count > 0 && ...}`

### 注意事项
- **常见错误**：
  - 左侧使用数字：`{count && <span>{count}</span>}`当`count=0`时渲染`0`
  - 左侧使用空字符串：`{text && <p>{text}</p>}`当`text=''`时渲染空（但空字符串不会被渲染，与数字不同）
- **性能问题**：无。
- **兼容性**：依赖JavaScript逻辑与运算符，广泛支持。

### 代码示例
```jsx
function TodoList({ todos }) {
  return (
    <div>
      <h2>我的待办</h2>
      {todos.length > 0 ? (
        <ul>
          {todos.map(todo => <li key={todo.id}>{todo.text}</li>)}
        </ul>
      ) : (
        <p>暂无待办，休息一下！</p>
      )}
      {/* 用 && 显示已完成数量 */}
      {todos.filter(t => t.completed).length > 0 && (
        <p>已完成 {todos.filter(t => t.completed).length} 项</p>
      )}
    </div>
  );
}
```

---

## 四、逻辑或(||)条件渲染

### 概念
- **是什么**：在JSX中使用逻辑或（`||`）运算符为变量或表达式提供默认值。
- **为什么需要**：简化默认值的设置，避免编写冗长的条件判断。
- **解决什么问题**：解决当值可能为假（如`null`、`undefined`、空字符串）时，需要回退显示默认内容的问题。

### 原理
- **底层实现**：`expr1 || expr2`规则：如果`expr1`为真，返回`expr1`；否则返回`expr2`。在JSX中，这个结果被直接渲染。
- **工作流程**：左侧表达式求值，如果是假值，则使用右侧默认值；否则使用左侧值。
- **关键技术点**：假值包括`false`、`null`、`undefined`、`0`、`NaN`、`''`。注意`0`也是假值，因此数字`0`会被默认值覆盖。

### 使用方法
- **基本用法**：
  ```jsx
  function Welcome({ name }) {
    return <h1>你好，{name || '游客'}！</h1>;
  }
  ```
- **常见场景**：
  - 设置组件prop的默认显示
  - 为可能为空的数组提供默认数组
  - 在渲染属性或样式时提供后备值
- **最佳实践**：
  - 仅当左侧预期为`null`或`undefined`时使用，若数字`0`是有意义的，应避免用`||`（因为`0`会被覆盖）
  - 对于更复杂的默认值逻辑，可使用`??`空值合并运算符（仅当左侧为`null/undefined`时采用默认值）

### 注意事项
- **常见错误**：
  - 误用`||`导致合法假值（如`0`、`''`）被替换
  - 在JSX属性中忘记加括号导致优先级问题（如`<div className={variant || 'default'}>`正确）
- **性能问题**：无。
- **兼容性**：ES5语法，全兼容；`??`需要ES2020或转译。

### 代码示例
```jsx
function Avatar({ src, alt }) {
  // 如果src为空，使用默认头像
  const imageSrc = src || '/default-avatar.png';
  return <img src={imageSrc} alt={alt || '头像'} />;
}

function Counter({ count }) {
  // 错误示例：count为0时显示“暂无计数”，但0本身可能是有效值
  // return <div>计数：{count || '暂无计数'}</div>;
  // 正确：使用 ?? 或显式判断
  return <div>计数：{count ?? '暂无计数'}</div>;
}
```

---

## 五、元素变量条件渲染

### 概念
- **是什么**：将JSX元素赋值给变量，通过条件逻辑修改变量的值，然后在JSX中插入该变量。
- **为什么需要**：当条件逻辑复杂，涉及大量JSX或需要在多个地方使用同一段条件性内容时，使用变量使主渲染逻辑更清晰。
- **解决什么问题**：解决条件渲染代码的模块化和可维护性问题，避免JSX内部过度臃肿。

### 原理
- **底层实现**：变量存储的是React元素（本质上是对象），在`return`中直接嵌入变量，React会将其作为子树渲染。
- **工作流程**：在渲染函数中，先声明变量，通过条件语句（如`if`、`switch`）给变量赋值为不同的JSX，最后在`return`中输出变量。
- **关键技术点**：变量类型必须为`ReactNode`，可以是单个元素、元素数组、字符串、数字等。

### 使用方法
- **基本用法**：
  ```jsx
  function LoginControl({ isLoggedIn }) {
    let button;
    if (isLoggedIn) {
      button = <LogoutButton />;
    } else {
      button = <LoginButton />;
    }
    return <div>{button}</div>;
  }
  ```
- **常见场景**：
  - 根据多个条件渲染不同的复杂UI片段
  - 避免在JSX中嵌入过多的`&&`或三元
  - 动态生成列表项或菜单项
- **最佳实践**：
  - 变量命名清晰，体现其内容
  - 可在条件分支中直接返回JSX，也可赋值给变量
  - 如果变量最终可能未赋值，确保所有路径都有有效值，否则可能导致渲染`undefined`

### 注意事项
- **常见错误**：
  - 忘记在所有分支中为变量赋值，导致渲染`undefined`（React不会渲染，但控制台会警告）
  - 变量作用域问题（如在`if`块内用`let`赋值但外部无法访问）
- **性能问题**：无额外开销。
- **兼容性**：完全依赖JavaScript。

### 代码示例
```jsx
function Dashboard({ user }) {
  let dashboardContent;

  switch (user.role) {
    case 'admin':
      dashboardContent = (
        <div>
          <AdminStats />
          <UserManagement />
        </div>
      );
      break;
    case 'editor':
      dashboardContent = (
        <div>
          <EditorPanel />
          <RecentPosts />
        </div>
      );
      break;
    default:
      dashboardContent = <div>您没有访问权限</div>;
  }

  return <div className="dashboard">{dashboardContent}</div>;
}
```

---

## 六、早期返回（Early Return）条件渲染

### 概念
- **是什么**：在函数组件开始处，用`if`语句提前返回JSX（或`null`），处理边界情况，避免后续逻辑执行。
- **为什么需要**：减少嵌套，使主要逻辑更清晰，提高代码可读性。
- **解决什么问题**：解决复杂组件中层层嵌套的`if`或三元运算符导致的“金字塔”问题，让正常路径扁平化。

### 原理
- **底层实现**：函数在执行到`return`语句后立即结束，后续代码不再执行。这纯粹是JavaScript函数的行为。
- **工作流程**：组件渲染时，先检查前置条件（如数据加载、权限），若条件不满足则提前返回占位UI或`null`，满足则继续执行主要渲染逻辑。
- **关键技术点**：每个`return`都会终止函数，所以必须确保每个分支都有返回值。

### 使用方法
- **基本用法**：
  ```jsx
  function Profile({ user, posts }) {
    if (!user) {
      return <div>请先登录</div>;
    }
    if (!posts || posts.length === 0) {
      return <div>该用户暂无文章</div>;
    }
    // 主渲染逻辑
    return (
      <div>
        <h1>{user.name}</h1>
        <ul>{posts.map(...)}</ul>
      </div>
    );
  }
  ```
- **常见场景**：
  - 加载状态（loading）处理
  - 错误边界或空状态展示
  - 权限校验
- **最佳实践**：
  - 将最紧急或最简单的条件放在前面
  - 避免早期返回过多，通常3-4个以内可接受
  - 可以结合自定义Hook封装复杂的状态检查

### 注意事项
- **常见错误**：
  - 忘记在早期返回后添加`return`，导致函数继续执行
  - 早期返回隐藏了后续逻辑，需确保每个路径都覆盖
- **性能问题**：无。
- **兼容性**：JavaScript标准行为。

### 代码示例
```jsx
function ArticlePage({ articleId }) {
  const { data, isLoading, error } = useArticle(articleId);

  if (isLoading) return <Spinner />;
  if (error) return <ErrorMessage message={error.message} />;
  if (!data) return <NotFound />;

  return <ArticleContent article={data} />;
}
```

---

## 七、阻止组件渲染（返回null）

### 概念
- **是什么**：让组件返回`null`而不是JSX，从而阻止该组件渲染任何内容。
- **为什么需要**：在某些情况下，组件不希望输出任何DOM，但仍需保留组件实例以执行副作用或逻辑。
- **解决什么问题**：解决条件性隐藏组件但又不想从父组件移除该组件的问题。

### 原理
- **底层实现**：React在渲染时，如果组件返回`null`，则不会创建DOM节点，也不会挂载该组件到树中。但组件的生命周期（如`useEffect`）仍然会在组件“渲染”时执行。
- **工作流程**：组件函数执行，返回`null`，React将其视为“无内容”，在父组件中不占据位置。
- **关键技术点**：返回`null`的组件仍然会被“调用”，其内部的Hooks仍会运行，但不会产生UI。

### 使用方法
- **基本用法**：
  ```jsx
  function MaybeShow({ show, children }) {
    if (!show) {
      return null;
    }
    return <div>{children}</div>;
  }
  ```
- **常见场景**：
  - 基于权限隐藏UI
  - 临时禁用某些组件
  - 根据窗口大小等条件决定渲染与否
- **最佳实践**：
  - 谨慎使用，因为父组件无法通过CSS选择器定位隐藏的元素
  - 与CSS `display: none`相比，返回`null`完全移除元素，有助于减少DOM节点数量

### 注意事项
- **常见错误**：
  - 误以为返回`null`的组件不会执行任何代码——实际上Hooks仍然运行
  - 在类组件中返回`null`同样有效
- **性能问题**：无显著影响，但频繁切换可能导致组件重新挂载（因为从DOM移除后再添加）
- **兼容性**：React核心特性。

### 代码示例
```jsx
function WarningBanner({ warn }) {
  useEffect(() => {
    if (warn) {
      console.log('警告已显示');
    } else {
      console.log('警告已隐藏');
    }
  }, [warn]);

  if (!warn) {
    return null;
  }

  return <div className="warning">注意：这是一个警告！</div>;
}

function App() {
  const [showWarning, setShowWarning] = useState(true);
  return (
    <div>
      <WarningBanner warn={showWarning} />
      <button onClick={() => setShowWarning(!showWarning)}>
        切换警告
      </button>
    </div>
  );
}
```

---

## 总结与选择指南

- **if语句**：适合多分支、复杂逻辑、早期返回。
- **三元运算符**：适合简单二选一、内联使用。
- **逻辑与 &&**：适合“条件为真时才渲染”。
- **逻辑或 ||**：适合提供默认值（注意假值陷阱）。
- **元素变量**：适合将条件逻辑提取出来，保持JSX干净。
- **早期返回**：适合处理前置条件、减少嵌套。
- **阻止渲染（返回null）**：适合完全隐藏组件但保留逻辑。

根据具体场景灵活运用这些方法，可编写出既高效又易维护的React组件。