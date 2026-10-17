## 一、严格模式

### 概念
- **是什么**：严格模式是ECMAScript 5引入的一种特性，通过使用 `"use strict"` 指令，选择进入一种受限的JavaScript变体，它改变了语言的一些行为，将静默错误转变为抛出错误，并修复了一些引擎难以优化的缺陷。
- **为什么需要**：传统的JavaScript（稀松模式）存在一些设计缺陷和不安全的行为，例如意外创建全局变量、对不可写属性赋值静默失败、使用 `with` 语句导致性能下降等。严格模式旨在提供一个更安全、更规范、更利于优化的JavaScript子集。
- **解决什么问题**：
  - 消除代码中的静默错误，使其显式抛出异常，便于调试。
  - 禁止使用一些未来ECMAScript版本中可能保留的关键字或语法，确保代码向前兼容。
  - 提高JavaScript引擎的优化能力，有时能使代码运行更快。

### 原理
- **底层实现**：严格模式通过解析器的一个特殊标志来启用。当解析器遇到 `"use strict"` 指令时，会将当前作用域（脚本或函数）标记为严格模式，在后续的词法分析和执行阶段，按照严格模式的规则进行代码检查和行为约束。
- **工作流程**：
  1. 解析阶段检测到 `"use strict"` 指令，标记当前作用域。
  2. 执行阶段，对于涉及严格模式变更的操作（如变量赋值、属性删除、参数处理等），引擎会执行额外的检查和不同的语义。
  3. 如果违反严格模式规则（例如给未声明的变量赋值），引擎会抛出相应的错误（如 `ReferenceError`）而不是静默失败。
- **关键技术点**：
  - 作用域标记：严格模式只在其出现的脚本或函数内生效，不影响外部。
  - 语法限制：禁止某些语法（如八进制字面量、`with` 语句、重复参数名等）。
  - 运行时检查：对不可写属性赋值、删除不可配置属性等操作抛出 `TypeError`。

### 使用方法
- **基本用法**：
  - 为整个脚本启用：在脚本文件第一行放置 `"use strict";`。
  - 为单个函数启用：在函数体开头放置 `"use strict";`。
  - 在模块和类中，严格模式自动启用，无需声明。
- **常见场景**：
  - 编写新的JavaScript项目时，在全局或函数开头使用严格模式，以提高代码质量。
  - 在已有的旧代码中逐步引入严格模式，可以先在函数级别使用，测试无误后再推广到整个脚本。
  - 在ES6模块或类中，严格模式是默认的，无需额外指令。
- **最佳实践**：
  - 始终在严格模式下编写代码，除非需要兼容非常老的浏览器（IE9及以下）。
  - 如果函数使用了默认参数、剩余参数或解构，则不能在函数体内使用 `"use strict"`，此时应将严格模式应用于包围函数的外部作用域。

### 注意事项
- **常见错误**：
  - 忘记在严格模式下声明变量，导致 `ReferenceError`。
  - 使用 `delete` 操作符删除变量、函数或不可配置属性，抛出语法错误或类型错误。
  - 在函数参数重复命名时，抛出语法错误。
  - 在 `with` 语句中抛出语法错误。
- **性能问题**：严格模式通常不会导致性能下降，反而可能让引擎更好地优化代码。但启用严格模式会增加一些额外的检查，这些开销通常可以忽略不计。
- **兼容性**：严格模式在IE10及以上、所有现代浏览器中都支持。在IE9及以下，`"use strict"` 会被忽略（视为普通字符串），因此代码在不支持严格模式的浏览器中可能仍然运行，但部分安全特性会失效。

### 代码示例
```javascript
// 全局严格模式
"use strict";
v = 1; // ReferenceError: v is not defined

// 函数级严格模式
function fn(a, a) { // SyntaxError: duplicate formal argument
  "use strict";
}

// 模块和类自动严格模式
class C {
  constructor() {
    delete Object.prototype; // TypeError
  }
}

// 带有默认参数的函数不能使用内部严格模式
function sum(a = 1, b = 2) {
  // "use strict"; // SyntaxError
  return a + b;
}
```

## 二、错误处理

### 概念
- **是什么**：错误处理是指程序在运行过程中发生异常时，通过特定的语法结构（`try...catch...finally`）捕获并处理这些异常，防止程序崩溃，并提供友好的错误反馈。
- **为什么需要**：程序运行时可能遇到各种不可预料的错误（如网络失败、用户输入非法、资源不存在等）。如果没有错误处理，这些错误会导致程序崩溃，用户体验差。通过错误处理，可以优雅地恢复或提示用户。
- **解决什么问题**：
  - 捕获同步代码中抛出的异常，阻止错误向上传播导致程序终止。
  - 区分不同类型的错误，分别处理。
  - 无论是否发生错误，都能执行必要的清理工作（如关闭文件、释放资源）。

### 原理
- **底层实现**：JavaScript引擎在执行代码时维护一个调用栈。当 `throw` 语句执行或运行时错误发生时，引擎会创建一个错误对象，并沿着调用栈向上查找最近的 `catch` 块。如果找到，则执行 `catch` 块并将错误对象传递进去；如果直到全局作用域都没有找到 `catch`，则触发全局的未捕获错误处理（如 `window.onerror`）并可能终止程序。
- **工作流程**：
  1. 执行 `try` 块中的代码。
  2. 如果 `try` 块中抛出异常，立即停止 `try` 块的后续代码，转到对应的 `catch` 块（如果有），并将错误对象作为参数传入。
  3. 执行 `catch` 块中的处理逻辑。
  4. 无论是否发生异常，最后都会执行 `finally` 块（如果存在）。
  5. 如果 `finally` 块中包含了 `return`、`throw`、`break` 或 `continue` 等语句，它们会覆盖 `try` 或 `catch` 中的相应语句。
- **关键技术点**：
  - 错误对象：包含错误名称、消息、堆栈等信息。
  - 异常传播：错误沿调用栈向上冒泡，直到被捕获或到达顶层。
  - `finally` 的保证执行：即使 `try` 或 `catch` 中有 `return`，也会先执行 `finally`。

### 使用方法
- **基本用法**：
  ```javascript
  try {
    // 可能抛出异常的代码
  } catch (error) {
    // 处理异常
  } finally {
    // 清理代码
  }
  ```
- **常见场景**：
  - 处理用户输入验证，抛出并捕获自定义验证错误。
  - 网络请求（如 `fetch`）中处理 HTTP 错误或解析错误。
  - 文件操作、数据库操作等 I/O 操作中捕获底层错误。
  - 在 async/await 中使用 `try...catch` 替代 Promise 的 `.catch()`。
- **最佳实践**：
  - 尽量捕获特定的错误类型，使用 `instanceof` 区分。
  - 不要在 `catch` 块中完全忽略错误，至少应记录日志或提示用户。
  - 在 `finally` 中释放资源（如清除定时器、关闭文件句柄）。
  - 对于意料之外的错误，可以重新抛出，让上层处理。
  - 避免在 `finally` 中使用 `return`，以免覆盖返回值或异常。

### 注意事项
- **常见错误**：
  - `try...catch` 无法捕获异步代码中的错误（如 `setTimeout` 回调中抛出的错误），需要使用 Promise 或 async/await 的错误处理机制。
  - 忘记处理 `catch` 中的错误，导致错误被吞没。
  - 在 `finally` 中使用 `return` 导致 `try` 或 `catch` 中的返回值丢失。
- **性能问题**：`try...catch` 块本身对性能影响很小，但抛出异常的成本较高（因为需要构建错误对象和解开堆栈）。应避免将异常用于正常的流程控制。
- **兼容性**：`try...catch...finally` 在所有现代浏览器和 Node.js 中都支持。`finally` 块在 ES3 中就已存在，兼容性很好。

### 代码示例
```javascript
// 基本 try...catch...finally
function divide(a, b) {
  try {
    if (b === 0) {
      throw new Error("除数不能为0");
    }
    return a / b;
  } catch (e) {
    console.error("发生错误:", e.message);
    return NaN;
  } finally {
    console.log("除法运算结束");
  }
}

// finally 覆盖 return
function test() {
  try {
    return 1;
  } finally {
    return 2; // 覆盖 try 中的 return
  }
}
console.log(test()); // 2

// 异步错误无法捕获
try {
  setTimeout(() => {
    throw new Error("异步错误");
  }, 100);
} catch (e) {
  // 不会执行
}
// 正确方式：使用 Promise
```

## 三、Error对象及其子类型

### 概念
- **是什么**：`Error` 对象是JavaScript中所有错误对象的基类。当运行时错误发生时，引擎会创建 `Error` 的实例；开发者也可以手动创建 `Error` 对象并使用 `throw` 抛出。ECMAScript 规范还定义了多个内置的错误子类型，如 `TypeError`、`ReferenceError` 等。
- **为什么需要**：统一的错误对象结构使得错误处理更加规范，开发者可以通过检查错误对象的属性（如 `name`、`message`、`stack`）来获取错误信息，并通过 `instanceof` 区分错误类型，实现精细化处理。
- **解决什么问题**：
  - 提供标准化的错误信息格式（名称、消息、堆栈跟踪）。
  - 允许开发者创建自定义错误类型，扩展错误信息。
  - 支持错误链（通过 `cause` 属性），保留原始错误上下文。

### 原理
- **底层实现**：`Error` 是一个构造函数，当使用 `new Error(message)` 创建实例时，会生成一个对象，包含 `message` 属性（传入的消息）和 `name` 属性（默认 "Error"）。引擎还会自动捕获当前调用栈的快照，并存储在非标准的 `stack` 属性中。子类型（如 `TypeError`）通过原型链继承自 `Error`，并设置自己的 `name` 属性。
- **工作流程**：
  1. 当引擎抛出错误时，会实例化相应的错误对象（如 `new TypeError(...)`）。
  2. 错误对象在 `catch` 块中可用，开发者可以访问其属性。
  3. 如果错误未被捕获，引擎可能会打印堆栈信息到控制台。
- **关键技术点**：
  - 原型链：所有内置错误类型都继承自 `Error.prototype`，因此可以使用 `instanceof Error` 检查。
  - 堆栈跟踪：`stack` 属性提供了错误发生时的调用堆栈，对调试非常有用。
  - `cause` 属性（ES2022）：用于链接错误，保留原始错误原因。

### 使用方法
- **基本用法**：
  ```javascript
  // 创建通用错误
  const err = new Error("出错了");
  console.log(err.name);    // "Error"
  console.log(err.message); // "出错了"
  console.log(err.stack);   // 堆栈信息

  // 使用内置错误类型
  throw new TypeError("类型错误");
  throw new RangeError("数值超出范围");
  throw new ReferenceError("引用错误");

  // 错误链
  try {
    // 一些操作
  } catch (originalError) {
    throw new Error("操作失败", { cause: originalError });
  }
  ```
- **常见场景**：
  - 在函数参数验证时抛出 `TypeError` 或 `RangeError`。
  - 在 `JSON.parse` 失败时捕获 `SyntaxError`。
  - 创建自定义错误类以携带额外的上下文（如字段名、错误码）。
  - 在库或框架中，使用错误链保留底层错误的堆栈信息，方便调试。
- **最佳实践**：
  - 总是抛出 `Error` 对象或其子类的实例，而不是抛出字符串或普通对象，以便保留堆栈和统一处理。
  - 使用 `instanceof` 检查错误类型，而不是依赖 `name` 属性（但 `name` 也可用于区分）。
  - 自定义错误应继承 `Error`，并正确设置 `name` 和原型链。
  - 在可能的情况下，使用 `cause` 将底层错误传递给上层，避免丢失原始错误信息。

### 注意事项
- **常见错误**：
  - 抛出字符串或数字，导致 `catch` 中无法获取堆栈和类型信息。
  - 忘记处理特定类型的错误，导致程序行为异常。
  - 自定义错误未正确设置原型链，导致 `instanceof` 失效。
- **性能问题**：创建错误对象（特别是捕获堆栈）有一定开销，不应在性能关键的循环中频繁创建。但在异常路径中，这是可以接受的。
- **兼容性**：
  - 所有 `Error` 内置类型在 ES3 起就存在，兼容性很好。
  - `stack` 属性是非标准的，但所有现代引擎都支持，不过格式可能略有差异。
  - `cause` 属性是 ES2022 新特性，在较新的浏览器和 Node.js 17+ 中支持。在不支持的环境中，可以手动附加 `cause` 属性。

### 代码示例
```javascript
// 内置错误类型的使用
try {
  JSON.parse("{非法json}");
} catch (e) {
  if (e instanceof SyntaxError) {
    console.log("JSON 语法错误:", e.message);
  } else {
    throw e;
  }
}

// 自定义错误类型
class DatabaseError extends Error {
  constructor(message, query) {
    super(message);
    this.name = "DatabaseError";
    this.query = query;
    if (Error.captureStackTrace) {
      Error.captureStackTrace(this, DatabaseError);
    }
  }
}

try {
  throw new DatabaseError("连接失败", "SELECT * FROM users");
} catch (e) {
  console.log(`${e.name}: ${e.message}`);
  console.log("查询语句:", e.query);
}

// 错误链
function readFile() {
  try {
    // 模拟文件读取失败
    throw new Error("文件不存在");
  } catch (err) {
    throw new Error("读取配置失败", { cause: err });
  }
}

try {
  readFile();
} catch (e) {
  console.log(e.message);        // "读取配置失败"
  console.log(e.cause.message);   // "文件不存在"
}
```