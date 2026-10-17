### 一、 CommonJS 规范
#### 知识点：CommonJS 规范（require 导入，module.exports 导出）

##### 概念
- **是什么**：CommonJS 是一种主要用于服务器端（如 Node.js）的 JavaScript 模块化规范。它定义了一套同步加载模块的 API，每个文件就是一个模块，拥有独立的作用域。
- **为什么需要**：在 Node.js 出现之前，JavaScript 缺乏内置的模块系统，无法有效管理文件之间的依赖和隔离作用域，导致全局污染和代码难以维护。
- **解决什么问题**：解决了全局命名冲突、依赖管理混乱、代码复用性差的问题，为服务端 JavaScript 开发提供了标准的模块化方案。

##### 原理
- **底层实现**：Node.js 在编译每个模块时，会将模块代码包装在一个函数中，例如：
  ```javascript
  (function(exports, require, module, __filename, __dirname) {
      // 模块代码
  });
  ```
  这样每个模块的作用域就被隔离了。`module` 对象代表当前模块，`module.exports` 是真正的导出对象，`exports` 是它的引用。
- **工作流程**：
  1. **路径解析**：`require()` 根据传入的模块标识符（路径或名称）查找目标文件。
  2. **加载与编译**：读取文件内容，并执行上述包装函数。
  3. **缓存**：模块在第一次加载后会被缓存到 `require.cache` 中，后续 `require` 直接返回缓存结果，避免重复执行。
- **关键技术点**：
  - `module.exports` 决定导出内容。
  - `exports` 是 `module.exports` 的快捷方式，但直接赋值 `exports` 会切断引用。
  - 模块缓存机制。

##### 使用方法
- **基本用法**：
  - **导出**：使用 `module.exports` 或 `exports`。
    ```javascript
    // 导出一个对象
    module.exports = { name: 'MyModule' };
    // 导出一个函数
    module.exports = function() { ... };
    // 使用 exports 导出多个属性
    exports.add = (a, b) => a + b;
    exports.PI = 3.14;
    ```
  - **导入**：使用 `require()` 同步加载模块。
    ```javascript
    const math = require('./math.js');
    console.log(math.add(1, 2));
    ```
- **常见场景**：
  - 在 Node.js 中组织代码，拆分功能到不同文件。
  - 引入核心模块（如 `fs`、`path`）或第三方包（如 `express`）。
- **最佳实践**：
  - 优先使用 `module.exports` 导出单一功能（如一个类），使用 `exports` 导出多个工具函数。
  - 避免循环依赖，若必须，确保导出时机合理。

##### 注意事项
- **常见错误**：
  - 直接对 `exports` 赋值：`exports = { ... }` 会覆盖引用，导致导出失败。
  - 忘记导出：定义了函数但未导出，其他模块无法访问。
- **性能问题**：`require` 是同步的，在浏览器端会导致阻塞，因此 CommonJS 不适合浏览器环境。
- **兼容性**：Node.js 原生支持，浏览器不支持（除非借助打包工具如 Webpack）。

##### 代码示例
```javascript
// ---- math.js ----
const PI = 3.14159;
function add(a, b) {
    return a + b;
}
// 导出多个
module.exports = { PI, add };

// 或者使用 exports
exports.PI = PI;
exports.add = add;

// ---- app.js ----
const math = require('./math.js');
console.log(math.PI);       // 3.14159
console.log(math.add(2, 3)); // 5

// 验证缓存
const math2 = require('./math.js');
console.log(math === math2); // true，同一个对象
```

---

### 二、 ES Module 规范

#### 知识点：ES Module 规范（import 导入，export/export default 导出）

##### 概念
- **是什么**：ES Module（ESM）是 ECMAScript 官方标准定义的 JavaScript 模块系统，旨在统一浏览器和服务器端的模块化方案。它采用静态导入/导出语法，支持编译时静态分析。
- **为什么需要**：CommonJS 是社区规范，浏览器不支持且是动态的，无法进行 tree-shaking 等优化。ES Module 旨在解决这些问题，成为语言原生的模块标准。
- **解决什么问题**：提供了静态模块结构，支持 tree-shaking、循环依赖更好处理、异步加载等高级特性，同时适用于浏览器和 Node.js。

##### 原理
- **底层实现**：ES Module 在编译阶段就确定了模块的依赖关系，生成模块依赖图。导入的变量是原模块变量的**动态只读引用**，而非值的拷贝。
- **工作流程**：
  1. **构建**：解析模块文件，找出所有 `import` 和 `export` 语句，构建模块记录。
  2. **实例化**：为每个模块分配内存空间，并建立导出和导入的绑定（引用关系）。
  3. **求值**：执行模块代码，填充内存中的值。
- **关键技术点**：
  - 静态分析：`import` 和 `export` 必须位于顶层。
  - 动态引用：导入的值会随原模块变化而实时更新。
  - 模块只执行一次，结果被缓存。

##### 使用方法
- **基本用法**：
  - **导出**：使用 `export` 进行命名导出，使用 `export default` 进行默认导出。
    ```javascript
    // 命名导出
    export const name = 'ESM';
    export function sayHi() { ... }
    // 默认导出
    export default class MyClass { ... }
    ```
  - **导入**：使用 `import` 导入。
    ```javascript
    // 导入命名导出
    import { name, sayHi } from './module.js';
    // 导入默认导出
    import MyClass from './module.js';
    ```
- **常见场景**：
  - 现代前端项目（使用 Vite、Webpack 等构建工具）编写模块。
  - Node.js 中启用 ESM（`.mjs` 文件或 `package.json` 中 `"type": "module"`）后使用。
- **最佳实践**：
  - 优先使用命名导出，以支持 tree-shaking。
  - 默认导出适用于模块的主功能（如一个组件）。
  - 保持导出命名清晰，避免重名。

##### 注意事项
- **常见错误**：
  - 将 `import` 写在条件语句中（静态导入不允许，除非使用 `import()` 动态导入）。
  - 忘记写文件扩展名（浏览器和 Node.js 中 ESM 需要完整路径，包括扩展名）。
- **性能问题**：静态分析有助于打包工具优化，但动态导入可代码分割，提升首屏加载性能。
- **兼容性**：现代浏览器和 Node.js v12+ 支持，旧环境需编译转换。

##### 代码示例
```javascript
// ---- module.js ----
export const PI = 3.14;
export function sum(...args) {
    return args.reduce((a, b) => a + b, 0);
}
export default class Calculator {
    multiply(a, b) { return a * b; }
}

// ---- app.js ----
import Calculator, { PI, sum as addAll } from './module.js';
console.log(PI);               // 3.14
console.log(addAll(1, 2, 3));  // 6
const calc = new Calculator();
console.log(calc.multiply(2, 3)); // 6

// 动态引用特性
// ---- counter.js ----
export let count = 0;
export function inc() { count++; }

// ---- main.js ----
import { count, inc } from './counter.js';
console.log(count); // 0
inc();
console.log(count); // 1（实时更新）
```

---

### 三、 默认导出 vs 命名导出

#### 知识点：默认导出 vs 命名导出

##### 概念
- **是什么**：ES Module 提供了两种导出方式：命名导出（Named Exports）和默认导出（Default Export）。命名导出可以导出多个值，每个值都有自己的名称；默认导出只能导出一个值，可以不带名称，导入时可任意命名。
- **为什么需要**：命名导出适用于模块需要暴露多个 API 的场景；默认导出适用于模块只有一个核心功能的场景，简化导入语法。
- **解决什么问题**：提供了灵活性，让模块作者可以根据用途选择合适的暴露方式。

##### 原理
- **底层实现**：命名导出在模块记录中对应多个具名绑定；默认导出实际上是一个名为 `default` 的具名导出，即 `export default` 等同于 `export { something as default }`。
- **工作流程**：当导入命名导出时，必须使用花括号和正确的名称；导入默认导出时，可以直接使用任意名称，编译器会将其映射到 `default` 属性。

##### 使用方法
- **基本用法**：
  - **命名导出**：
    ```javascript
    // 导出
    export const a = 1;
    export function b() {}
    // 导入
    import { a, b } from './module.js';
    ```
  - **默认导出**：
    ```javascript
    // 导出
    export default function() {}
    // 导入
    import anyName from './module.js';
    ```
  - **混合导出**：
    ```javascript
    // 导出
    export default class Main {}
    export const helper = () => {};
    // 导入
    import Main, { helper } from './module.js';
    ```
- **常见场景**：
  - 命名导出：工具库（如 `lodash` 的各个函数）。
  - 默认导出：React 组件、一个类或单一主函数。
- **最佳实践**：
  - 在一个模块中尽量保持单一出口风格：要么全部命名导出，要么一个默认导出加上少量命名导出，避免混乱。
  - 默认导出的名称可以简化导入，但可能降低代码可读性，需平衡。

##### 注意事项
- **常见错误**：
  - 一个模块中同时有多个默认导出（语法错误）。
  - 默认导出时，直接写 `export default const a = 1;` 会报错，因为 `default` 后应跟表达式或函数声明，不能跟变量声明。正确写法：`const a = 1; export default a;`。
- **性能问题**：命名导出有助于 tree-shaking，未使用的命名导出可以被摇掉；默认导出通常导出整个对象，可能无法 tree-shaking。
- **兼容性**：两种方式都是标准语法，所有支持 ESM 的环境都支持。

##### 代码示例
```javascript
// ---- utils.js ----
// 命名导出
export const add = (x, y) => x + y;
export const PI = 3.14;

// 默认导出（一个主要函数）
export default function multiply(x, y) {
    return x * y;
}

// ---- app.js ----
// 导入默认和命名
import multiply, { add, PI } from './utils.js';
console.log(multiply(2, 3)); // 6
console.log(add(1, 2));      // 3
console.log(PI);              // 3.14

// 也可以整体导入默认和命名
import * as utils from './utils.js';
console.log(utils.default(2, 3)); // 6（default 是默认导出的名称）
console.log(utils.add(1, 2));     // 3
```

---

### 四、 导入别名

#### 知识点：导入别名（import { a as b }）

##### 概念
- **是什么**：在 ES Module 中，导入时可以使用 `as` 关键字为导入的变量指定一个新的名称，避免命名冲突或简化名称。
- **为什么需要**：当导入多个模块时，可能出现名称冲突；或者希望将导入的变量命名为更符合当前模块上下文的名称。
- **解决什么问题**：提供了灵活的导入命名方式，增强代码可读性和可维护性。

##### 原理
- **底层实现**：别名只是在当前模块作用域中创建一个新绑定，指向原模块的导出绑定。不影响原模块。
- **工作流程**：编译器在解析 `import` 语句时，会将指定的新名称与模块记录中的导出名称建立映射。

##### 使用方法
- **基本用法**：
  - 命名导出重命名：
    ```javascript
    import { originalName as newName } from './module.js';
    ```
  - 默认导出重命名（默认导出本质上可以任意命名，不需要 `as`）：
    ```javascript
    import anyName from './module.js'; // 已经是别名效果
    ```
  - 同时使用多个别名：
    ```javascript
    import { a as x, b as y } from './module.js';
    ```
- **常见场景**：
  - 解决不同模块中同名冲突。
  - 将长名称简化为短名称。
  - 在整体导入时，重命名某些属性（通常配合 `import * as` 使用）。
- **最佳实践**：
  - 别名应具有语义，避免过度简化导致难以理解。
  - 在团队中保持一致的命名风格。

##### 注意事项
- **常见错误**：忘记花括号，写成 `import a as b from 'module'`（语法错误，命名导入必须用花括号）。
- **性能问题**：无影响。
- **兼容性**：标准语法，所有支持 ESM 的环境都支持。

##### 代码示例
```javascript
// ---- moduleA.js ----
export const name = 'ModuleA';
export function log() { console.log('A'); }

// ---- moduleB.js ----
export const name = 'ModuleB';
export function log() { console.log('B'); }

// ---- app.js ----
import { name as nameA, log as logA } from './moduleA.js';
import { name as nameB, log as logB } from './moduleB.js';
console.log(nameA); // ModuleA
logA();             // A
console.log(nameB); // ModuleB
logB();             // B
```

---

### 五、 整体导入

#### 知识点：整体导入（import * as obj from 'module'）

##### 概念
- **是什么**：整体导入使用 `import * as <obj>` 语法，将一个模块的所有命名导出（不包括默认导出）作为一个对象的属性导入。
- **为什么需要**：当需要批量使用一个模块的多个导出时，整体导入可以避免逐一列出，简化代码。
- **解决什么问题**：提供了便捷的方式来访问模块的所有命名 API，类似于 CommonJS 的 `require` 返回整个对象。

##### 原理
- **底层实现**：`import * as obj` 会创建一个模块命名空间对象，该对象包含模块的所有命名导出（包括通过 `export { name }` 导出的，但不包括 `default`）。属性是只读的，不可重新赋值。
- **工作流程**：当执行导入时，该对象被冻结，其属性与模块内的变量保持动态绑定。

##### 使用方法
- **基本用法**：
  ```javascript
  import * as utils from './utils.js';
  console.log(utils.add(1, 2));
  console.log(utils.PI);
  ```
- **常见场景**：
  - 导入工具库的所有函数，但又不希望使用默认导出。
  - 需要以对象形式访问模块导出，方便传递。
- **最佳实践**：
  - 整体导入可能会引入未使用的导出，不利于 tree-shaking，应谨慎使用。
  - 在大型项目中，优先使用具名导入，以明确依赖关系。

##### 注意事项
- **常见错误**：
  - 误以为整体导入会包含默认导出。实际上，默认导出可以通过 `obj.default` 访问，但通常不推荐这样使用。
  - 尝试修改整体导入对象的属性（会抛出错误，因为是只读的）。
- **性能问题**：整体导入可能使 tree-shaking 失效，因为打包工具难以静态分析对象属性的使用情况。
- **兼容性**：标准语法，所有支持 ESM 的环境都支持。

##### 代码示例
```javascript
// ---- math.js ----
export const add = (a, b) => a + b;
export const subtract = (a, b) => a - b;
export const PI = 3.14;
export default function multiply(a, b) { return a * b; }

// ---- app.js ----
import * as math from './math.js';
console.log(math.add(2, 3));      // 5
console.log(math.PI);              // 3.14
// 默认导出可以通过 default 访问
console.log(math.default(2, 3));   // 6

// 注意：不能修改整体导入对象
math.PI = 3.14159; // TypeError: Cannot assign to read only property 'PI'
```

---

### 六、 动态导入

#### 知识点：动态导入（import('module')）

##### 概念
- **是什么**：动态导入是使用 `import()` 函数形式的语法，它返回一个 `Promise`，可以在运行时按需加载模块。与静态 `import` 不同，它可以在代码的任何位置调用。
- **为什么需要**：静态导入必须在顶层，无法实现按需加载（如根据用户操作加载某个模块）。动态导入允许代码分割和懒加载，提升应用性能。
- **解决什么问题**：解决了静态导入缺乏灵活性的问题，使得模块可以在需要时才加载，减少初始加载体积。

##### 原理
- **底层实现**：`import()` 是一个类似于函数的操作符（但并非 `Function` 的子类），它由 JavaScript 引擎特殊处理。调用时，会发起一个异步网络请求（浏览器）或文件读取（Node.js）来获取模块，然后解析并执行，最后通过 Promise 解析为模块对象。
- **工作流程**：
  1. 调用 `import(moduleSpecifier)`。
  2. 引擎开始异步加载并编译模块。
  3. 加载完成后，Promise 兑现为一个模块命名空间对象（类似于 `import * as` 得到的对象）。
  4. 如果加载失败，Promise 拒绝。
- **关键技术点**：
  - 支持 `await` 使用。
  - 模块标识符可以是动态生成的字符串。
  - 返回的模块对象包含所有导出（包括默认导出，通过 `default` 属性访问）。

##### 使用方法
- **基本用法**：
  ```javascript
  // 使用 Promise
  import('./module.js').then(module => {
      module.doSomething();
  });

  // 使用 async/await
  async function loadModule() {
      const module = await import('./module.js');
      module.doSomething();
  }
  ```
- **常见场景**：
  - 路由懒加载：在单页应用中，根据路由动态加载组件。
  - 按需加载功能模块：如点击按钮后才加载某个复杂功能。
  - 条件加载：根据不同环境或用户权限加载不同模块。
- **最佳实践**：
  - 配合代码分割工具（如 Webpack 的魔法注释）实现更细粒度的 chunk 命名。
  - 不要过度使用动态导入，只对较大或有明显延迟加载需求的模块使用。

##### 注意事项
- **常见错误**：
  - 忘记处理 Promise 的拒绝情况（网络错误）。
  - 将动态导入用于小的、频繁使用的模块，反而增加请求开销。
- **性能问题**：动态导入会增加额外的网络请求，但可减少首屏体积，需要权衡。合理使用缓存和预加载。
- **兼容性**：现代浏览器和 Node.js 支持。Node.js 中需要 ESM 环境。

##### 代码示例
```javascript
// ---- heavyModule.js ----
export function process(data) {
    console.log('Processing:', data);
    return data * 2;
}
export default function initialize() {
    console.log('Heavy module initialized');
}

// ---- app.js ----
button.addEventListener('click', async () => {
    try {
        const module = await import('./heavyModule.js');
        // 使用命名导出
        module.process(10); // 20
        // 使用默认导出
        module.default();    // Heavy module initialized
    } catch (error) {
        console.error('Failed to load module', error);
    }
});

// 使用动态导入实现条件加载
const lang = navigator.language.startsWith('zh') ? 'zh' : 'en';
const i18n = await import(`./locales/${lang}.js`);
```

---

### 七、 核心机制对比

#### 知识点：CommonJS 与 ES Module 核心机制对比

##### 概念
- **是什么**：CommonJS 和 ES Module 是 JavaScript 两种主要的模块化规范，它们在设计哲学、语法、加载机制等方面存在显著差异。
- **为什么需要**：了解两者的区别有助于在不同环境（Node.js、浏览器）中正确选择和使用模块系统，并理解打包工具的工作原理。
- **解决什么问题**：对比分析能帮助开发者避免混用导致的错误，优化代码性能。

##### 原理
- **加载方式**：
  - CommonJS：同步加载，运行时加载（依赖在执行阶段确定）。
  - ES Module：静态加载（编译时），支持异步动态加载。
- **导出绑定**：
  - CommonJS：导出值的拷贝（基本类型拷贝，对象拷贝引用）。
  - ES Module：导出值的动态只读引用。
- **缓存**：两者都缓存模块，但 CommonJS 缓存的是整个 `module.exports` 对象，ESM 缓存的是模块记录，导入的值始终是最新状态。
- **循环依赖处理**：
  - CommonJS：遇到循环依赖时，可能返回未完成的 `module.exports`。
  - ES Module：由于引用是动态的，循环依赖通常能正常工作，但需注意变量提升问题。

##### 使用方法
- **环境适配**：
  - Node.js：默认 CommonJS，可通过文件扩展名或 `package.json` 配置启用 ESM。
  - 浏览器：原生支持 ESM，不支持 CommonJS（除非借助打包工具）。
- **互操作**：
  - ESM 可以导入 CommonJS 模块（默认导出为 `module.exports` 的整体对象）。
  - CommonJS 不能通过 `require` 导入 ESM，但可以使用动态 `import()`。

##### 注意事项
- **常见错误**：
  - 在 Node.js 中混用两种规范时，忘记设置正确的文件扩展名或 `type` 字段。
  - 在 ESM 中使用 `__dirname` 等 CommonJS 全局变量（不存在，需使用 `import.meta.url` 替代）。
- **性能问题**：CommonJS 同步加载不适用于浏览器；ESM 静态分析有利于 tree-shaking。
- **兼容性**：Node.js 从 v12 开始稳定支持 ESM，但需注意不同版本的行为差异。

##### 代码示例
```javascript
// ---- CommonJS 模块：cjs-module.js ----
exports.name = 'CJS';
exports.getTime = () => Date.now();

// ---- ESM 模块：esm-module.mjs ----
import { createRequire } from 'module';
const require = createRequire(import.meta.url);
const cjs = require('./cjs-module.js');
console.log(cjs.name); // CJS

// 导出 ESM
export const version = '1.0';
```

---

### 八、 环境配置与最佳实践

#### 知识点：模块化环境配置与最佳实践

##### 概念
- **是什么**：在不同的 JavaScript 运行环境（Node.js、浏览器）中，正确配置和使用模块系统，并遵循最佳实践，以确保代码的可维护性、性能和兼容性。
- **为什么需要**：不同的环境对模块规范的支持不同，错误的配置会导致模块加载失败或性能问题。
- **解决什么问题**：提供清晰的环境配置指南和编码规范，帮助开发者高效、正确地使用模块化。

##### 原理
- **Node.js 中的 ESM 启用**：
  - 使用 `.mjs` 文件扩展名。
  - 在 `package.json` 中设置 `"type": "module"`，使 `.js` 文件默认作为 ESM 处理。
  - 在 ESM 中无法直接使用 CommonJS 的 `require`、`__dirname` 等，但可以通过 `import.meta.url` 和 `module.createRequire()` 解决。
- **浏览器中的 ESM**：
  - 在 `<script>` 标签上添加 `type="module"`。
  - 模块文件必须通过服务器提供（遵守 CORS）。
  - 支持 `import maps` 来简化导入路径。
- **打包工具的作用**：
  - Webpack、Vite 等工具可以将 ESM 模块打包成兼容旧环境的代码，并实现代码分割、tree-shaking 等优化。

##### 使用方法
- **Node.js 项目配置**：
  ```json
  // package.json
  {
    "type": "module", // 启用 ESM
    "exports": {
      ".": "./index.js",
      "./utils": "./utils.js"
    }
  }
  ```
- **浏览器项目配置**：
  ```html
  <script type="module" src="/src/main.js"></script>
  <!-- 使用 import map -->
  <script type="importmap">
    {
      "imports": {
        "lodash": "/node_modules/lodash-es/lodash.js"
      }
    }
  </script>
  ```
- **最佳实践**：
  - 新项目优先使用 ES Module，以便利用现代工具链。
  - 在 Node.js 中，如果同时使用两种规范，明确区分文件扩展名（`.cjs` 和 `.mjs`）。
  - 导出时遵循单一职责原则，接口清晰。
  - 使用动态导入实现代码分割，优化加载性能。
  - 避免循环依赖，使用工具检测。

##### 注意事项
- **常见错误**：
  - Node.js 中 ESM 模式下使用 `require` 导致报错。
  - 浏览器中直接打开本地 HTML 文件，使用 `type="module"` 加载模块时遇到 CORS 错误（需通过服务器访问）。
- **性能问题**：合理使用 tree-shaking，避免不必要的导出；动态导入时设置合理的 chunk 名称，便于缓存。
- **兼容性**：旧浏览器需通过打包工具转换；Node.js 版本需 >=12。

##### 代码示例
```javascript
// ---- Node.js ESM 示例（使用 import.meta.url）----
import { fileURLToPath } from 'url';
import { dirname } from 'path';
const __filename = fileURLToPath(import.meta.url);
const __dirname = dirname(__filename);
console.log(__dirname);

// ---- 浏览器动态导入示例（结合 Vue 路由懒加载）----
const routes = [
    {
        path: '/about',
        component: () => import('./views/About.vue')
    }
];
```

---

以上笔记覆盖了您表格中列出的所有核心知识点，并补充了环境配置与最佳实践，确保内容的完整性和深度。每个知识点均按照模板结构编写，力求准确、详尽。希望对您的学习有所帮助！