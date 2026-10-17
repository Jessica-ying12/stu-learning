## 一、WeakMap 与 WeakSet（弱引用集合）

### 概念
- **是什么**  
  `WeakMap` 和 `WeakSet` 是 ES6 引入的两种新的集合类型，它们分别类似于 `Map` 和 `Set`，但对其元素持有的是“弱引用”。  
  - `WeakMap` 的键必须是对象，值可以是任意类型。  
  - `WeakSet` 的值必须是对象。

- **为什么需要**  
  在常规的 `Map` 和 `Set` 中，只要集合本身存在，其中的键或值就会一直被强引用，即使程序的其他部分不再需要这些对象，它们也无法被垃圾回收，可能导致内存泄漏。`WeakMap` 和 `WeakSet` 通过弱引用解决了这一问题，让不再被外部引用的对象能够被自动回收，从而优化内存管理。

- **解决什么问题**  
  主要解决因对象引用未被及时清除而导致的内存泄漏问题。特别适用于需要为临时对象关联额外数据或标记的场景，且这些数据的生命周期应与对象本身一致。

### 原理
- **底层实现**  
  JavaScript 引擎在实现 `WeakMap` 和 `WeakSet` 时，内部维护了一个弱引用表。当对象作为键或值存入时，引擎记录的是该对象的弱引用，不会增加对象的引用计数，也不会阻止垃圾回收器（GC）在回收阶段将其清除。

- **工作流程**  
  1. 对象被添加到 `WeakMap` 或 `WeakSet` 中。  
  2. 如果该对象在其他地方没有被任何强引用指向，GC 会在下一次垃圾回收周期中将其回收。  
  3. 回收后，该对象会自动从 `WeakMap` 或 `WeakSet` 中移除，且不会留下任何痕迹。  
  4. 由于 GC 的时机不确定，集合的内容可能随时变化，因此它们不支持迭代操作。

- **关键技术点**  
  - 弱引用：不干扰 GC 的正常工作。  
  - 不可枚举：无法遍历，没有 `size` 属性，也不支持 `forEach`、`keys()` 等方法。  
  - 键/值必须为对象：保证弱引用的语义明确。

### 使用方法
- **基本用法**  
  ```javascript
  // WeakMap
  const wm = new WeakMap();
  const obj1 = {};
  wm.set(obj1, 'some value');
  wm.get(obj1); // 'some value'
  wm.has(obj1); // true
  wm.delete(obj1);

  // WeakSet
  const ws = new WeakSet();
  const obj2 = {};
  ws.add(obj2);
  ws.has(obj2); // true
  ws.delete(obj2);
  ```

- **常见场景**  
  1. **存储私有数据**：为每个实例关联私有属性，实例销毁后数据自动清理。  
  2. **缓存计算结果**：缓存与对象相关的计算结果，避免内存泄漏。  
  3. **DOM 元素元数据**：为 DOM 元素存储监听器或其他状态，元素移除后元数据自动消失。  
  4. **对象标记**：使用 `WeakSet` 标记对象是否已被处理过。

- **最佳实践**  
  - 仅在确实需要弱引用且不关心集合内容可枚举时使用。  
  - 避免试图遍历 `WeakMap` 或 `WeakSet`，应通过其他方式维护必要的引用。  
  - 配合闭包或类使用，实现真正的“私有”属性。

### 注意事项
- **常见错误**  
  - 尝试使用原始值作为 `WeakMap` 的键或添加到 `WeakSet` 中，会抛出 `TypeError`。  
  - 误以为可以像普通集合一样获取大小或遍历，导致代码出错。

- **性能问题**  
  - 弱引用的维护本身对性能影响极小，但频繁的对象创建和销毁会增加 GC 负担，应合理设计对象生命周期。  
  - 由于不支持迭代，某些操作（如批量清理）无法直接完成，需要借助外部引用。

- **兼容性**  
  - ES6 特性，现代浏览器和 Node.js 均支持。在旧环境（如 IE）中需要使用 polyfill，但 polyfill 无法完全模拟弱引用的行为（因为无法控制 GC），因此谨慎使用。

### 代码示例
```javascript
// 使用 WeakMap 存储私有数据
const _privateData = new WeakMap();

class Person {
  constructor(name) {
    _privateData.set(this, { name });
  }
  getName() {
    return _privateData.get(this).name;
  }
}

let p = new Person('Alice');
console.log(p.getName()); // Alice
p = null; // 当 p 被回收，其私有数据也会被回收

// 使用 WeakSet 标记已访问对象
const visited = new WeakSet();
let objA = { id: 1 };
let objB = { id: 2 };

function process(obj) {
  if (visited.has(obj)) {
    console.log('Already processed', obj.id);
  } else {
    console.log('Processing', obj.id);
    visited.add(obj);
  }
}

process(objA); // Processing 1
process(objA); // Already processed 1
objA = null; // 对象被回收后，visited 中对应的记录自动消失
```

## 二、Proxy 与 Reflect（元编程）

### 概念
- **是什么**  
  `Proxy` 和 `Reflect` 是 ES6 引入的一对用于元编程的内置对象。  
  - `Proxy` 用于创建一个对象的代理，从而可以拦截并自定义对象的基本操作（如属性读取、赋值、函数调用等）。  
  - `Reflect` 是一个静态对象，提供了一组与 `Proxy` 陷阱一一对应的默认操作方法，用于在代理内部执行默认行为。

- **为什么需要**  
  传统 JavaScript 中，无法直接拦截对象的基础操作。`Proxy` 使开发者能够介入对象底层行为，实现如数据验证、日志记录、响应式系统等高级功能。`Reflect` 则提供了一种统一、函数式的方式来执行这些默认操作，避免直接使用命令式语法（如 `in`、`delete`），并简化了 `Proxy` 陷阱的编写。

- **解决什么问题**  
  - 实现对对象操作的完全控制，扩展语言能力。  
  - 统一并规范化对象操作方法，增强代码可读性和可靠性。  
  - 为框架和库提供底层能力，如 Vue 3 的响应式系统。

### 原理
- **底层实现**  
  `Proxy` 在引擎层面拦截对象的基础操作。当对代理对象进行操作时，JavaScript 引擎会检查代理的 `handler` 对象中是否有对应的陷阱方法，如果有则调用该方法，否则执行对象的默认行为。

- **工作流程**  
  1. 创建 `Proxy` 实例，传入目标对象和处理器对象。  
  2. 当对代理对象进行读取、赋值、删除、函数调用等操作时，引擎会调用处理器中对应的陷阱（如果存在）。  
  3. 在陷阱内部，可以执行自定义逻辑，并通常通过调用 `Reflect` 的对应方法来执行原始操作。  
  4. `Reflect` 方法会执行对象的默认行为并返回结果，确保代理行为的一致性。

- **关键技术点**  
  - **陷阱（Trap）**：共 13 种，覆盖了几乎所有的对象基本操作（如 `get`、`set`、`has`、`deleteProperty`、`apply`、`construct` 等）。  
  - **`receiver` 参数**：在 `get` 和 `set` 陷阱中，`receiver` 指向最初调用操作的对象（通常是代理本身或继承自代理的对象），保证 `this` 的正确传递。  
  - **可撤销代理**：通过 `Proxy.revocable()` 创建，可在需要时撤销代理，使代理失效。

### 使用方法
- **基本用法**  
  ```javascript
  // Proxy
  const target = { message: 'hello' };
  const handler = {
    get(obj, prop) {
      console.log(`Getting ${prop}`);
      return obj[prop];
    }
  };
  const proxy = new Proxy(target, handler);
  console.log(proxy.message); // 输出 "Getting message" 和 "hello"

  // Reflect
  Reflect.get(target, 'message'); // 'hello'，等同于 target.message
  ```

- **常见场景**  
  1. **数据验证**：在 `set` 陷阱中验证属性值。  
  2. **日志与追踪**：记录所有属性访问和修改。  
  3. **响应式系统**：拦截数据变化，触发视图更新。  
  4. **权限控制**：限制对某些属性的访问。  
  5. **函数节流/防抖**：在 `apply` 陷阱中实现。  
  6. **可撤销代理**：用于保护敏感对象或实现临时访问权限。

- **最佳实践**  
  - 在 `Proxy` 陷阱中，总是调用对应的 `Reflect` 方法，以确保默认行为被正确执行，并且能正确处理 `receiver`。  
  - 避免过度使用 `Proxy`，因为代理会增加额外的开销，仅在必要时使用。  
  - 使用 `Proxy.revocable()` 管理临时访问权限，提高安全性。

### 注意事项
- **常见错误**  
  - 在 `set` 陷阱中忘记返回 `true`（严格模式下必须返回布尔值）。  
  - 在 `get` 陷阱中返回 `undefined` 而非抛出错误，导致逻辑错误。  
  - 错误地处理 `receiver` 导致 `this` 绑定混乱。

- **性能问题**  
  - `Proxy` 会引入额外的间接层，频繁操作代理对象可能比直接操作对象稍慢，应避免对性能关键路径使用。  
  - 每个陷阱调用都会带来函数调用开销，合理设计代理层数。

- **兼容性**  
  - ES6 特性，现代浏览器和 Node.js 均支持。无法完全 polyfill，因此在老旧环境中需避免使用或提供降级方案。

### 代码示例
```javascript
// 使用 Proxy 实现数据验证和默认值
const handler = {
  get(target, prop) {
    if (prop in target) {
      return target[prop];
    }
    return 'default'; // 返回默认值
  },
  set(target, prop, value) {
    if (prop === 'age') {
      if (!Number.isInteger(value)) {
        throw new TypeError('Age must be an integer');
      }
      if (value < 0) {
        throw new RangeError('Age must be positive');
      }
    }
    target[prop] = value;
    return true;
  }
};

const user = new Proxy({}, handler);
user.name = 'Alice';
user.age = 25; // 正常
console.log(user.age); // 25
console.log(user.country); // 'default'
// user.age = -5; // 抛出 RangeError

// 使用 Reflect 确保正确行为
const obj = { x: 1 };
const p = new Proxy(obj, {
  get(target, prop, receiver) {
    console.log(`Get ${prop}`);
    return Reflect.get(target, prop, receiver); // 确保 this 指向正确
  }
});
console.log(p.x); // Get x \n 1
```

## 三、Generator 函数与 yield（可控执行流）

### 概念
- **是什么**  
  Generator 函数是 ES6 提供的一种特殊函数，可以在执行过程中暂停，并在未来恢复执行。它通过在 `function` 关键字后添加星号 `*` 定义，函数体内使用 `yield` 关键字来暂停并返回一个值。

- **为什么需要**  
  传统的函数一旦执行就必须从头到尾完成，无法中途暂停。Generator 使函数具有了可中断性，为处理异步流程、惰性求值和状态机等场景提供了更灵活的解决方案。

- **解决什么问题**  
  - 简化异步编程（在 `async/await` 出现之前是主流方案）。  
  - 实现惰性计算，按需生成数据，节省内存。  
  - 构建复杂的迭代器和状态机。

### 原理
- **底层实现**  
  Generator 函数返回一个 Generator 对象，该对象同时实现了迭代器协议（`next()`、`return()`、`throw()` 方法）和可迭代协议（`[Symbol.iterator]` 属性）。当调用 `next()` 时，函数体开始执行，直到遇到 `yield` 表达式，此时执行上下文被保存，并返回 `{ value, done }`。下次调用 `next()` 时，从上一次暂停的位置恢复执行。

- **工作流程**  
  1. 调用 Generator 函数，返回一个暂停的 Generator 对象。  
  2. 调用 `next()` 启动或恢复执行，遇到 `yield` 暂停，并返回 `yield` 后的值。  
  3. 调用 `next(arg)` 可以传入一个参数，该参数会成为上一个 `yield` 表达式的返回值。  
  4. 调用 `return()` 或 `throw()` 可以提前结束或让 Generator 抛出错误。  
  5. 当函数执行到 `return` 或函数体结束，返回的 `done` 为 `true`。

- **关键技术点**  
  - **`yield` 表达式**：暂停点，可返回一个值。  
  - **`yield*` 表达式**：委托给另一个可迭代对象，将其产生的值依次产出。  
  - **执行上下文保存**：Generator 内部维护了函数的作用域链和指令指针，以便恢复执行。

### 使用方法
- **基本用法**  
  ```javascript
  function* gen() {
    yield 1;
    yield 2;
    return 3;
  }
  const g = gen();
  console.log(g.next()); // { value: 1, done: false }
  console.log(g.next()); // { value: 2, done: false }
  console.log(g.next()); // { value: 3, done: true }
  ```

- **常见场景**  
  1. **异步流程控制**：与 Promise 配合，通过自动执行器（如 co 库）写出同步风格的异步代码。  
  2. **生成无限序列**：如斐波那契数列、ID 生成器。  
  3. **状态机**：通过 `yield` 在不同状态间切换。  
  4. **迭代器生成**：轻松实现自定义迭代器。  
  5. **数据流控制**：例如在 Redux-Saga 中管理副作用。

- **最佳实践**  
  - 使用 `yield*` 委托给其他可迭代对象，避免手动迭代。  
  - 在异步场景中，优先使用 `async/await`，但理解 Generator 有助于深入理解异步原理。  
  - 避免在 Generator 内部执行耗时同步操作，否则会阻塞 `next()` 调用。

### 注意事项
- **常见错误**  
  - 忘记在 Generator 函数名后加 `*`。  
  - 误以为 Generator 是异步的，实际上它是同步的，只是可暂停。  
  - 在 `yield` 表达式中使用 `return`，会导致提前结束。

- **性能问题**  
  - Generator 的每次暂停和恢复都有少量开销，但对于大多数场景可忽略。  
  - 大量嵌套或递归使用 Generator 可能导致性能下降。

- **兼容性**  
  - ES6 特性，现代环境均支持。在旧环境需要转译（如 Babel）。

### 代码示例
```javascript
// 使用 Generator 实现斐波那契数列生成器
function* fibonacci() {
  let a = 0, b = 1;
  while (true) {
    yield a;
    [a, b] = [b, a + b];
  }
}
const fib = fibonacci();
console.log(fib.next().value); // 0
console.log(fib.next().value); // 1
console.log(fib.next().value); // 1
console.log(fib.next().value); // 2
// 可以一直取下去

// 使用 yield* 委托
function* letters() {
  yield 'a';
  yield 'b';
}
function* all() {
  yield* [1, 2];
  yield* letters();
  yield* 'hi';
}
console.log([...all()]); // [1, 2, 'a', 'b', 'h', 'i']

// 异步流程示例（配合 Promise 自动执行器）
function run(generator) {
  const g = generator();
  function step(value) {
    const result = g.next(value);
    if (!result.done) {
      Promise.resolve(result.value).then(step);
    }
  }
  step();
}
run(function* () {
  const data1 = yield fetch('/api/data1').then(r => r.json());
  const data2 = yield fetch('/api/data2').then(r => r.json());
  console.log(data1, data2);
});
```

## 四、Symbol（唯一标识符）

### 概念
- **是什么**  
  `Symbol` 是 ES6 引入的一种新的原始数据类型，表示独一无二的值。通过 `Symbol()` 函数创建，每次调用都会返回一个全新的唯一值。

- **为什么需要**  
  JavaScript 对象的属性名只能是字符串，这可能导致属性名冲突（尤其是在扩展第三方对象时）。`Symbol` 提供了真正的私有属性名，保证每个 Symbol 值都是唯一的，从而避免命名冲突。

- **解决什么问题**  
  - 为对象添加安全的、不会冲突的属性。  
  - 定义对象的“隐藏”属性，避免被常规方法遍历。  
  - 扩展语言内部行为，通过“知名 Symbol”自定义对象在特定上下文中的行为。

### 原理
- **底层实现**  
  `Symbol` 值在引擎内部以唯一标识符的形式存在，不与其他任何值相等。其描述符仅用于调试，不影响唯一性。全局 Symbol 注册表（通过 `Symbol.for` 访问）维护了一个从字符串键到 Symbol 的映射，实现跨作用域的 Symbol 共享。

- **工作流程**  
  1. 调用 `Symbol()` 创建一个新的、唯一的 Symbol 值。  
  2. 可以作为对象属性的键，由于唯一性，不会与现有字符串键冲突。  
  3. 通过 `Symbol.for(key)` 在全局注册表中查找或创建 Symbol，实现共享。  
  4. 使用 `Symbol.keyFor(sym)` 获取全局注册表中 Symbol 的键。

- **关键技术点**  
  - 唯一性：每次调用 `Symbol()` 都产生新值。  
  - 不可枚举：Symbol 属性不会出现在 `for...in` 或 `Object.keys()` 中，但可以通过 `Object.getOwnPropertySymbols()` 获取。  
  - 知名 Symbol：如 `Symbol.iterator`、`Symbol.toStringTag` 等，用于定制语言行为。

### 使用方法
- **基本用法**  
  ```javascript
  const sym1 = Symbol();
  const sym2 = Symbol('description'); // 可选描述
  console.log(sym1 === sym2); // false

  const obj = {};
  obj[sym1] = 'value';
  console.log(obj[sym1]); // 'value'
  ```

- **常见场景**  
  1. **作为唯一属性键**：向对象添加元数据而不与已有属性冲突。  
  2. **定义常量**：如事件类型、状态枚举，保证唯一性。  
  3. **实现迭代器**：通过 `[Symbol.iterator]` 方法使对象可迭代。  
  4. **自定义类型标签**：使用 `Symbol.toStringTag` 定制 `Object.prototype.toString` 的输出。  
  5. **扩展内置行为**：如使用 `Symbol.hasInstance` 自定义 `instanceof`。

- **最佳实践**  
  - 为 Symbol 添加描述，方便调试。  
  - 使用 `Symbol.for` 实现跨模块共享 Symbol。  
  - 避免过度依赖 `Object.getOwnPropertySymbols`，除非确有必要。  
  - 结合 `WeakMap` 实现真正的私有属性。

### 注意事项
- **常见错误**  
  - 误以为 Symbol 是字符串，尝试用点号访问属性（如 `obj.sym` 而不是 `obj[sym]`）。  
  - 使用 `JSON.stringify()` 时会忽略 Symbol 属性。  
  - 认为 Symbol 属性是私有的，实际上仍可通过 `Object.getOwnPropertySymbols()` 获取。

- **性能问题**  
  - Symbol 的创建和属性访问与字符串属性相当，性能差异可忽略。  
  - 全局注册表查找有一定开销，但通常不是性能瓶颈。

- **兼容性**  
  - ES6 特性，现代环境支持。在旧环境（如 IE）需要使用 polyfill，但无法完全模拟唯一性。

### 代码示例
```javascript
// 使用 Symbol 作为唯一属性键
const id = Symbol('id');
const user = { name: 'Alice' };
user[id] = 12345;
console.log(user[id]); // 12345
console.log(Object.keys(user)); // ['name']，Symbol 属性不会被枚举
console.log(Object.getOwnPropertySymbols(user)); // [ Symbol(id) ]

// 知名 Symbol：自定义迭代器
const iterableObj = {
  data: [1, 2, 3],
  [Symbol.iterator]: function* () {
    for (let item of this.data) {
      yield item;
    }
  }
};
console.log([...iterableObj]); // [1, 2, 3]

// 自定义 toStringTag
class MyClass {
  get [Symbol.toStringTag]() {
    return 'MyClass';
  }
}
const obj = new MyClass();
console.log(Object.prototype.toString.call(obj)); // "[object MyClass]"

// 全局共享 Symbol
const s1 = Symbol.for('shared');
const s2 = Symbol.for('shared');
console.log(s1 === s2); // true
console.log(Symbol.keyFor(s1)); // 'shared'
```

---

**总结**：以上四个知识点是 JavaScript 进阶开发中不可或缺的核心特性。`WeakMap`/`WeakSet` 帮助我们精细控制内存，避免泄漏；`Proxy`/`Reflect` 赋予我们操作对象底层行为的能力，是实现框架级功能的基石；`Generator` 提供了可暂停的执行流，是异步编程和状态管理的重要工具；`Symbol` 则用独一无二的标识符保障了属性安全，并扩展了语言的内置行为。深入理解并灵活运用这些特性，将显著提升代码的质量与健壮性。