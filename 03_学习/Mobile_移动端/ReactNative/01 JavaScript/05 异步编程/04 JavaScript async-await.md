## 一、async 函数

### 概念
- **是什么**  
  `async` 是用于声明一个异步函数的关键字。在函数定义前加上 `async`，该函数就成为一个异步函数，它总是返回一个 Promise 对象。

- **为什么需要**  
  在 Promise 出现后，虽然解决了回调地狱的问题，但链式调用（`.then()`）仍然不够直观，尤其在处理复杂异步逻辑时代码可能变得冗长。`async` 函数提供了一种更接近同步代码的书写方式，结合 `await` 让异步代码的阅读和维护更加自然。

- **解决什么问题**  
  - 将异步操作的结果以同步形式返回，简化了 Promise 的链式调用。  
  - 配合 `await`，使异步代码的流程控制（如条件分支、循环）与同步代码完全一致。  
  - 统一了异步函数的返回值类型（始终为 Promise），便于组合和错误处理。

### 原理
- **底层实现**  
  `async` 函数是基于 Promise 和生成器（Generator）的语法糖。JavaScript 引擎在遇到 `async` 函数时，会将其转换为一个状态机，内部通过 Promise 和微任务队列来控制执行流程。

- **工作流程**  
  1. 调用 `async` 函数时，函数体开始同步执行。  
  2. 如果遇到 `return` 或函数结束，返回值会被 `Promise.resolve()` 包装成一个 resolved 状态的 Promise。  
  3. 如果函数内抛出同步错误，则返回一个 rejected 状态的 Promise。  
  4. 如果函数内使用了 `await`，则函数的剩余部分会被暂停，等待 Promise 敲定后通过微任务恢复执行。

- **关键技术点**  
  - **返回值自动封装**：任何非 Promise 的返回值都会被隐式转换为 Promise。  
  - **执行顺序**：`async` 函数内的同步代码会立即执行，直到遇到第一个 `await` 才交出控制权。  
  - **错误处理**：函数内抛出的错误会导致返回的 Promise 变为 rejected。

### 使用方法
- **基本用法**  
  ```javascript
  async function fetchData() {
      return 'data'; // 等价于 Promise.resolve('data')
  }
  fetchData().then(console.log); // 输出 'data'
  ```

- **常见场景**  
  - 定义需要等待异步操作后才能返回结果的函数。  
  - 在事件处理函数、回调函数中使用（需注意作用域）。  
  - 与箭头函数结合：`const fn = async () => { ... };`

- **最佳实践**  
  - 所有需要用到 `await` 的函数都必须标记为 `async`。  
  - 尽量让 `async` 函数只做一件事，保持单一职责。  
  - 不要在不需要异步操作的函数上滥用 `async`（会增加微任务开销）。

### 注意事项
- **常见错误**  
  - 忘记在调用 `async` 函数时使用 `await` 或 `.then()`，导致得到的是 Promise 而不是实际值。  
  - 在 `async` 函数中直接返回一个 rejected Promise，但未处理，导致外层无法捕获（需使用 `try/catch` 或 `.catch()`）。

- **性能问题**  
  - 每个 `async` 函数都会创建一个 Promise 包装器，但现代引擎优化得很好，性能损失可忽略。  
  - 过度使用串行 `await` 可能导致性能下降（应使用并行）。

- **兼容性**  
  - 所有现代浏览器和 Node.js 均支持。  
  - 旧环境（如 IE）需要使用 Babel 等工具转译。

### 代码示例
```javascript
// 基本定义
async function getUser(id) {
    // 模拟异步操作
    return { id, name: 'Alice' };
}
getUser(1).then(user => console.log(user));

// 抛出错误
async function fail() {
    throw new Error('Something went wrong');
}
fail().catch(err => console.error(err.message));
```

## 二、await 表达式

### 概念
- **是什么**  
  `await` 是一个操作符，用于等待一个 Promise 完成。它只能在 `async` 函数内部（或模块顶层）使用，会暂停当前 `async` 函数的执行，直到 Promise 敲定（settled）。

- **为什么需要**  
  为了以同步的方式获取异步操作的结果，避免嵌套的 `.then()` 回调，使代码更易读。

- **解决什么问题**  
  - 将异步结果直接赋值给变量，简化数据处理流程。  
  - 让异步代码的书写顺序与实际执行顺序一致。  
  - 配合 `try/catch` 统一处理同步和异步错误。

### 原理
- **底层实现**  
  `await` 实际上是语法层面的暂停机制。当引擎遇到 `await` 时，会将 `async` 函数的剩余代码包装成一个回调，注册到当前 Promise 的微任务队列中。然后立即退出函数，让出线程。

- **工作流程**  
  1. 计算 `await` 后面的表达式，得到一个值（可能是 Promise 或普通值）。  
  2. 如果是 Promise，则等待其敲定。  
     - 如果 Promise resolved，`await` 返回 resolved 值，函数继续执行。  
     - 如果 Promise rejected，则 `await` 抛出 rejected 原因。  
  3. 如果是非 Promise 值，则将其转换为已 resolved 的 Promise，并立即继续（实质是微任务延后）。

- **关键技术点**  
  - **暂停但不阻塞**：`await` 只暂停当前 `async` 函数，不影响主线程或其他任务。  
  - **微任务队列**：`await` 后面的代码总是在当前微任务队列清空后执行（即使 Promise 已 resolved）。  
  - **thenable 兼容**：`await` 会识别任何包含 `.then` 方法的对象，并将其当作 Promise 处理。

### 使用方法
- **基本用法**  
  ```javascript
  async function getData() {
      let response = await fetch('/api/data');
      let data = await response.json();
      console.log(data);
  }
  ```

- **常见场景**  
  - 等待 HTTP 请求完成。  
  - 等待文件读写（Node.js）。  
  - 等待定时器（如 `await new Promise(r => setTimeout(r, 1000))`）。  
  - 在循环中依次处理异步任务（需注意性能）。

- **最佳实践**  
  - 仅对返回 Promise 的表达式使用 `await`，避免无意义的包装。  
  - 对于无依赖的异步任务，不要串行 `await`，应使用 `Promise.all`。  
  - 在 `try/catch` 中包裹 `await` 以处理可能的错误。

### 注意事项
- **常见错误**  
  - 在普通函数中使用 `await`（导致语法错误）。  
  - 忘记 `await` 导致拿到 Promise 对象而非值。  
  - 在循环中错误使用 `await` 导致串行，而本可并行。

- **性能问题**  
  - 每个 `await` 都会引入微任务队列的额外开销，但通常可忽略。  
  - 大量连续 `await` 会导致代码执行变慢（应考虑并行）。

- **兼容性**  
  - 同 `async` 函数，现代环境完全支持，旧环境需转译。  
  - 顶层 `await` 需 ES2022 支持。

### 代码示例
```javascript
// 等待 Promise
async function example() {
    let value = await Promise.resolve(123);
    console.log(value); // 123
}

// 等待非 Promise
async function demo() {
    let str = await 'hello'; // 等价于 await Promise.resolve('hello')
    console.log(str); // 'hello'
}

// 注意执行顺序
async function order() {
    console.log('1');
    await null;
    console.log('3');
}
console.log('0');
order();
console.log('2');
// 输出: 0, 1, 2, 3
```

## 三、错误处理（try...catch）

### 概念
- **是什么**  
  在 `async/await` 中，可以使用传统的 `try...catch` 语句来捕获 `await` 表达式抛出的错误（包括 Promise 的 rejection 和同步错误）。

- **为什么需要**  
  异步操作（如网络请求、文件读取）随时可能失败，需要优雅地处理错误，避免程序崩溃或出现未处理的 Promise rejection。

- **解决什么问题**  
  - 统一处理同步错误和异步错误，使错误处理逻辑集中。  
  - 避免在每个 Promise 链末端都加 `.catch()` 的繁琐。  
  - 允许在失败时执行清理、重试或回退操作。

### 原理
- **底层实现**  
  当 `await` 后面的 Promise 被拒绝时，JavaScript 引擎会像处理 `throw` 语句一样，在 `async` 函数内部抛出一个异常。如果这个异常没有被 `try/catch` 捕获，则会导致函数返回的 Promise 被拒绝。

- **工作流程**  
  1. 执行 `await promise`，如果 promise 被拒绝，则 `await` 抛出拒绝原因。  
  2. 如果抛出在 `try` 块内，控制权立即转移到 `catch` 块。  
  3. 在 `catch` 块中可以对错误进行处理（如记录日志、返回默认值）。  
  4. 如果 `catch` 块没有重新抛出，则函数正常返回，返回值可能是默认值或 undefined。

- **关键技术点**  
  - **单 `try/catch` 多 `await`**：任何一个 `await` 失败都会跳到 `catch`，后续代码不再执行。  
  - **混合同步错误**：`try/catch` 也能捕获 `async` 函数中的同步错误（如引用错误）。  
  - **未捕获错误**：如果没有 `try/catch`，错误会导致函数返回的 Promise 被拒绝，需在外层用 `.catch()` 或全局事件处理。

### 使用方法
- **基本用法**  
  ```javascript
  async function fetchData() {
      try {
          let response = await fetch('https://api.example.com');
          if (!response.ok) {
              throw new Error(`HTTP error! status: ${response.status}`);
          }
          let data = await response.json();
          return data;
      } catch (error) {
          console.error('Fetch failed:', error.message);
          return null; // 返回默认值
      }
  }
  ```

- **常见场景**  
  - 网络请求失败处理。  
  - 数据库操作异常处理。  
  - 用户输入验证失败。  
  - 多个并行任务中单独处理每个任务的错误。

- **最佳实践**  
  - 在可能失败的操作周围使用 `try/catch`，粒度适中（太粗会掩盖具体错误，太细会导致代码冗余）。  
  - 在 `catch` 中根据错误类型进行不同处理（可使用 `instanceof` 判断）。  
  - 如果无法恢复错误，应重新抛出或返回 rejected Promise 以通知调用者。  
  - 不要忽略错误（空的 `catch` 块会使调试困难）。

### 注意事项
- **常见错误**  
  - 忘记处理 HTTP 错误状态码（仅依赖 `fetch` 自动 reject）。  
  - 在 `catch` 中忘记 `return` 或 `throw`，导致函数返回 undefined，掩盖了失败。  
  - 误以为 `try/catch` 能捕获异步回调中的错误（例如 `setTimeout` 内的错误不会被外层的 `try/catch` 捕获）。

- **性能问题**  
  - `try/catch` 在大多数引擎中开销很小，但应避免在性能关键的循环内使用大量 `try/catch`。

- **兼容性**  
  - `try/catch` 是 JavaScript 的基础语法，无兼容性问题。

### 代码示例
```javascript
// 捕获异步错误
async function getUser(id) {
    try {
        let user = await database.findUser(id);
        if (!user) throw new Error('User not found');
        return user;
    } catch (err) {
        console.error('Error in getUser:', err.message);
        return null;
    }
}

// 分别处理多个 await
async function process() {
    let step1Result, step2Result;
    try {
        step1Result = await step1();
    } catch (err) {
        console.error('Step1 failed:', err);
        // 可能执行回退
        step1Result = 'default';
    }
    try {
        step2Result = await step2(step1Result);
    } catch (err) {
        console.error('Step2 failed:', err);
        throw err; // 无法恢复，向上传递
    }
    return step2Result;
}
```

## 四、并行执行（Promise.all 与相关方法）

### 概念
- **是什么**  
  并行执行指的是同时发起多个独立的异步操作，等待所有操作完成（或某个条件满足）后再继续处理。主要通过 `Promise.all`、`Promise.allSettled`、`Promise.race`、`Promise.any` 等方法实现。

- **为什么需要**  
  如果多个异步任务彼此没有依赖，串行执行会浪费大量等待时间。并行可以显著提高程序效率，特别是在 I/O 密集型场景（如同时请求多个 API）。

- **解决什么问题**  
  - 避免不必要的串行等待，提升性能。  
  - 管理多个 Promise 的复合状态（全部成功、任意成功、全部完成等）。  
  - 提供统一的错误处理策略。

### 原理
- **底层实现**  
  这些方法都是 `Promise` 的静态方法，内部维护一个计数器或状态机。  
  - `Promise.all`：当所有传入的 Promise 都 resolved 时，返回一个包含所有结果的数组；如果任意一个 rejected，则立即返回该拒绝原因。  
  - `Promise.allSettled`：等待所有 Promise 敲定（无论成功或失败），返回每个 Promise 的结果对象数组。  
  - `Promise.race`：返回第一个敲定的 Promise（成功或失败）的结果。  
  - `Promise.any`：返回第一个成功的 Promise 的结果；如果所有都失败，则返回一个 `AggregateError`。

- **工作流程**  
  1. 接收一个 Promise 可迭代对象。  
  2. 立即返回一个新的 Promise。  
  3. 根据方法的不同，监听每个 Promise 的敲定事件，当条件满足时解决或拒绝返回的 Promise。

- **关键技术点**  
  - **快速失败**：`Promise.all` 和 `Promise.race` 会因一个失败而立即失败。  
  - **不阻塞**：这些方法都是异步的，不会阻塞主线程。  
  - **结果顺序**：`Promise.all` 和 `Promise.allSettled` 的结果顺序与输入顺序一致。

### 使用方法
- **基本用法**  
  ```javascript
  async function loadMultiple() {
      const [users, posts] = await Promise.all([
          fetch('/api/users'),
          fetch('/api/posts')
      ]);
      // 处理结果
  }
  ```

- **常见场景**  
  - 同时加载页面所需的多份独立数据。  
  - 等待多个文件读取完成。  
  - 实现超时控制（`Promise.race` 结合定时器）。  
  - 批量处理任务，需要知道所有任务的最终状态（`Promise.allSettled`）。

- **最佳实践**  
  - 对于无依赖的任务，始终优先使用 `Promise.all` 而不是串行 `await`。  
  - 如果需要容忍单个任务失败，使用 `Promise.allSettled`。  
  - 使用 `Promise.race` 实现超时：`Promise.race([task, timeoutPromise])`。  
  - 注意错误处理：`Promise.all` 的 catch 只会捕获第一个失败，丢失其他任务的结果。

### 注意事项
- **常见错误**  
  - 误用 `Promise.all` 导致一个失败整个失败，而实际需求可能是忽略部分失败。  
  - 忘记 `await` 直接使用 `Promise.all`，导致得到的是 Promise 而不是结果。  
  - 在 `Promise.all` 中传入空数组，会立即 resolved（返回空数组）。  
  - 对于大量并发任务，可能耗尽系统资源（如同时发起成千上万个 HTTP 请求），需限制并发数。

- **性能问题**  
  - `Promise.all` 本身没有额外性能开销，但并行任务过多可能导致资源竞争或后端压力，应考虑分块控制并发。

- **兼容性**  
  - `Promise.all` 和 `Promise.race` 从 ES6 开始支持。  
  - `Promise.allSettled` 和 `Promise.any` 是较新的特性（ES2020、ES2021），现代环境已支持，旧环境需 polyfill。

### 代码示例
```javascript
// Promise.all 并行
async function parallelDemo() {
    try {
        const [user, posts] = await Promise.all([
            fetch('/user').then(r => r.json()),
            fetch('/posts').then(r => r.json())
        ]);
        console.log(user, posts);
    } catch (err) {
        console.error('至少一个请求失败', err);
    }
}

// Promise.allSettled 等待所有完成
async function settledDemo() {
    const results = await Promise.allSettled([
        fetch('/a'),
        fetch('/b')
    ]);
    results.forEach((result, i) => {
        if (result.status === 'fulfilled') {
            console.log(`请求 ${i} 成功`);
        } else {
            console.log(`请求 ${i} 失败:`, result.reason);
        }
    });
}

// Promise.race 超时示例
async function fetchWithTimeout(url, timeout = 5000) {
    const controller = new AbortController();
    const timeoutId = setTimeout(() => controller.abort(), timeout);
    try {
        const response = await Promise.race([
            fetch(url, { signal: controller.signal }),
            new Promise((_, reject) =>
                setTimeout(() => reject(new Error('Timeout')), timeout)
            )
        ]);
        clearTimeout(timeoutId);
        return response;
    } catch (err) {
        clearTimeout(timeoutId);
        throw err;
    }
}
```

## 五、顶层 await（Top-level await）

### 概念
- **是什么**  
  顶层 `await` 允许在 ES 模块的顶层直接使用 `await` 关键字，而不必包裹在 `async` 函数中。

- **为什么需要**  
  在模块加载时，有时需要等待异步资源（如从服务器获取配置、动态导入模块）初始化后再导出内容。以前只能通过立即执行 `async` 函数或导出 Promise 的方式解决，但不够直观。顶层 `await` 使模块可以像 `async` 函数一样等待异步操作，同时不影响其他模块的加载。

- **解决什么问题**  
  - 简化模块初始化时的异步依赖处理。  
  - 允许模块在加载过程中暂停，直到异步资源准备就绪。  
  - 使模块导出值依赖于异步结果变得自然。

### 原理
- **底层实现**  
  顶层 `await` 改变了模块的执行顺序。当模块解析器遇到顶层 `await` 时，会暂停当前模块的执行，直到 `await` 的 Promise 敲定，然后继续执行模块的剩余部分。这种暂停不会阻塞其他模块的加载，但依赖该模块的其他模块会等待它完成。

- **工作流程**  
  1. 模块开始执行同步代码。  
  2. 遇到顶层 `await`，暂停模块执行，等待 Promise。  
  3. Promise 敲定后，恢复模块执行，后面的代码可以访问异步结果。  
  4. 模块导出在异步操作完成后才会被其他模块使用（依赖模块会等待）。

- **关键技术点**  
  - **仅在模块中有效**：必须是 ES 模块（`<script type="module">` 或 `.mjs` 文件）。  
  - **影响模块图**：使用顶层 `await` 的模块会成为异步模块，其依赖链会等待它。  
  - **可与动态 `import()` 结合**，实现按需加载。

### 使用方法
- **基本用法**  
  ```javascript
  // data.json 文件
  const data = await fetch('./data.json').then(res => res.json());
  export { data };
  ```

- **常见场景**  
  - 加载配置文件。  
  - 动态导入依赖（`const lodash = await import('lodash');`）。  
  - 初始化数据库连接。  
  - 在模块作用域内执行异步设置。

- **最佳实践**  
  - 谨慎使用，因为顶层 `await` 会延迟依赖模块的执行，可能导致应用启动变慢。  
  - 仅用于模块真正需要等待的异步操作，避免不必要的等待。  
  - 如果可能，将异步初始化放在导出的函数中，而不是模块顶层，以减少加载阻塞。

### 注意事项
- **常见错误**  
  - 在非模块脚本中使用顶层 `await` 导致语法错误。  
  - 过度使用顶层 `await` 导致模块加载串行化，降低性能。  
  - 忘记处理错误：顶层 `await` 的 Promise 如果被拒绝，会导致模块加载失败，且无法被 `try/catch` 包裹（但可以在顶层使用 `try/catch`）。

- **性能问题**  
  - 顶层 `await` 会阻塞当前模块及其依赖模块的执行，因此应尽量缩短等待时间。  
  - 多个顶层 `await` 在同一个模块中会串行执行，可考虑 `Promise.all` 并行。

- **兼容性**  
  - ES2022 特性，现代浏览器和 Node.js 14+ 已支持，旧环境需转译（但转译后可能无法完全模拟其行为）。

### 代码示例
```javascript
// moduleA.js
const config = await fetch('/config.json').then(r => r.json());
export const apiUrl = config.apiUrl;

// moduleB.js
import { apiUrl } from './moduleA.js'; // 等待 moduleA 加载完成
console.log(apiUrl); // 此时 apiUrl 已定义

// 错误处理
try {
    const data = await fetch('/data').then(r => r.json());
    export default data;
} catch (err) {
    console.error('Failed to load data');
    export default null;
}
```

## 六、async/await 与 Promise 的互操作及微任务机制

### 概念
- **是什么**  
  `async/await` 完全基于 Promise，两者可以无缝混合使用。`async` 函数返回 Promise，`await` 可以等待任何 Promise 或 thenable 对象。同时，`async/await` 内部也遵循 Promise 的微任务队列机制。

- **为什么需要**  
  理解 `async/await` 与 Promise 的关系有助于深入掌握异步编程，并在复杂场景下灵活选择合适的模式（如链式调用与 `await` 混用）。

- **解决什么问题**  
  - 解释 `async/await` 的执行顺序（微任务）。  
  - 说明如何将现有的 Promise 代码迁移到 `async/await`。  
  - 指导在什么情况下仍需要直接使用 Promise 方法（如 `Promise.all`、`.catch`）。

### 原理
- **底层实现**  
  - `async` 函数体内部的 `await` 会被转换为对 Promise 的 `.then()` 调用，剩余代码被包装在回调中。  
  - `async` 函数返回的 Promise 等价于函数体内所有 `await` 完成后最终的返回值。  
  - 微任务机制：当 Promise 敲定时，其回调会被推入微任务队列，在当前宏任务结束后、下一个宏任务开始前执行。`await` 后的代码始终通过微任务队列调度。

- **工作流程**  
  1. 调用 `async` 函数，同步执行到第一个 `await`。  
  2. `await` 后面的 Promise 如果已经 resolved，其 `.then()` 回调（即函数剩余代码）仍会被放入微任务队列，不会立即执行。  
  3. 函数返回一个 Promise，并退出。  
  4. 当前宏任务执行完毕，开始执行微任务队列中的任务，恢复函数执行。  
  5. 最终函数返回的 Promise 被 resolved 或 rejected。

- **关键技术点**  
  - **微任务延后**：即使 `await` 后面是已 resolved 的 Promise，后续代码也会在微任务中执行（比同步代码晚）。  
  - **混合使用**：可以在 `async` 函数中返回一个 Promise，外层可以直接 `.then()`；也可以在 Promise 链中调用 `async` 函数。  
  - **错误传递**：`async` 函数内部未捕获的错误会导致返回的 Promise 被拒绝，与 Promise 行为一致。

### 使用方法
- **基本用法**  
  ```javascript
  // 从 Promise 迁移
  function legacy() {
      return fetch('/data').then(res => res.json());
  }
  // 等价 async
  async function modern() {
      const res = await fetch('/data');
      return res.json();
  }
  ```

- **常见场景**  
  - 在 `async` 函数中调用返回 Promise 的旧代码。  
  - 对 `async` 函数的返回值使用 `.then()`/`.catch()`。  
  - 使用 `Promise.all` 等静态方法与 `await` 结合。

- **最佳实践**  
  - 当需要多个并行任务时，优先使用 `Promise.all` 而非串行 `await`。  
  - 当需要在异步操作完成后执行一些不依赖结果的代码（如日志），可以直接在 `await` 后写，无需再用 `.then()`。  
  - 如果需要在 `async` 函数中处理错误并继续执行，使用 `try/catch` 而不是 `.catch()`。

### 注意事项
- **常见错误**  
  - 混淆微任务顺序：认为 `await` 后的代码会立即执行，实际会在微任务中执行。  
  - 在 `async` 函数中混合使用 `.then()` 和 `await` 可能造成代码逻辑混乱，应保持风格一致。  
  - 忘记 `await` 一个 Promise 直接返回，导致函数返回一个 pending 状态的 Promise 但丢失了结果。

- **性能问题**  
  - 过度使用 `await` 微任务不会造成明显性能问题，但大量串行 `await` 会因等待而降低吞吐量。  
  - 使用 `Promise.all` 并行可提升性能。

- **兼容性**  
  - 同 Promise 和 `async/await`，现代环境完全支持。

### 代码示例
```javascript
// 混合使用
async function getUser(id) {
    let user = await database.findUser(id);
    return user;
}
getUser(1).then(user => console.log(user));

// 微任务顺序验证
async function microtaskDemo() {
    console.log('1');
    await Promise.resolve();
    console.log('3');
}
console.log('0');
microtaskDemo();
console.log('2');
// 输出: 0, 1, 2, 3

// 使用 Promise.all 与 await
async function allDemo() {
    const [a, b] = await Promise.all([getA(), getB()]);
    return a + b;
}
```

---

以上笔记涵盖了 `async/await` 的核心知识点，并按照模板进行了系统整理。每个部分都从概念、原理、使用方法、注意事项到代码示例进行了详细阐述，确保知识点的完整性和深度。