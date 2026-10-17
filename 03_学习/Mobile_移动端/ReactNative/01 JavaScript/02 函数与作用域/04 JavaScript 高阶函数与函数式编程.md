## 一、函数是一等公民

### ##### 概念
- **是什么**：在JavaScript中，函数被视为“一等公民”（First-Class Citizens），意味着函数可以像其他数据类型（数字、字符串、对象）一样被操作。
- **为什么需要**：这种特性使得函数可以作为值传递、赋值给变量、存储在数据结构中，从而支持更灵活的编程范式，特别是函数式编程。
- **解决什么问题**：它让代码更加模块化、可复用，允许将行为作为参数传递或作为结果返回，为高阶函数和回调机制奠定了基础。

### ##### 原理
- **底层实现**：JavaScript引擎将函数视为对象实例，拥有自己的属性和方法。每个函数都是一个`Function`类型的对象，因此可以被引用和传递。
- **工作流程**：函数定义时会在内存中创建函数对象，其引用可以被存储在变量、数组、对象属性中。当通过引用调用时，执行函数体。
- **关键技术点**：函数声明提升（仅函数声明形式）、函数表达式的赋值时机、箭头函数的特殊性（无自己的`this`、`arguments`）。

### ##### 使用方法
- **基本用法**：
  - 赋值给变量：`const fn = function() { ... };`
  - 存储在数组：`[fn1, fn2]`
  - 作为对象方法：`obj.method = function() { ... };`
- **常见场景**：
  - 回调函数：将函数传递给异步API。
  - 高阶函数：将函数作为参数或返回值。
  - 动态创建函数：根据条件返回不同函数。
- **最佳实践**：
  - 优先使用箭头函数简化语法，但注意其作用域绑定。
  - 使用有意义的函数名，便于调试。
  - 避免在循环中创建函数（可能造成性能问题）。

### ##### 注意事项
- **常见错误**：
  - 混淆函数声明与表达式，导致提升问题。
  - 在箭头函数中使用`arguments`对象（不存在）。
  - 在不需要时创建过多的匿名函数，影响内存。
- **性能问题**：函数创建本身开销较小，但大量动态创建可能影响性能，应考虑复用。
- **兼容性**：箭头函数在ES6中引入，旧环境需要转译。

### ##### 代码示例
```javascript
// 函数赋值给变量
const greet = function(name) {
    return `Hello, ${name}!`;
};

// 函数存储在数组中
const operations = [
    (a, b) => a + b,
    (a, b) => a - b,
    (a, b) => a * b
];
console.log(operations[0](5, 3)); // 8

// 作为对象方法
const calculator = {
    value: 0,
    add(n) {
        this.value += n;
        return this;
    }
};
calculator.add(5);
console.log(calculator.value); // 5
```

## 二、高阶函数

### 概念
- **是什么**：高阶函数（Higher-Order Function）是满足至少以下条件之一的函数：
  1. 接受一个或多个函数作为参数。
  2. 返回一个函数作为结果。
- **为什么需要**：它允许对函数进行抽象和组合，将通用控制流程封装起来，通过传入具体函数实现不同行为，提升代码复用性和可读性。
- **解决什么问题**：减少重复代码，实现声明式编程（如数组的`map`、`filter`），以及创建函数工厂和修饰器。

### 原理
- **底层实现**：基于函数是一等公民的特性，函数的参数和返回值可以是函数引用。调用高阶函数时，传入的函数不会立即执行，而是等待高阶函数内部在合适时机调用（同步或异步）。
- **工作流程**：
  1. 定义高阶函数，声明参数为函数类型（通过形参接收）。
  2. 在高阶函数内部，根据逻辑调用传入的函数（可能传递参数）。
  3. 若返回函数，则创建并返回一个新函数（常形成闭包）。
- **关键技术点**：回调函数的执行时机、闭包的形成、函数引用的保存。

### 使用方法
- **基本用法**：
  - 接受函数参数：`function repeat(n, action) { for (let i=0; i<n; i++) action(i); }`
  - 返回函数：`function greaterThan(n) { return m => m > n; }`
- **常见场景**：
  - 数组迭代方法：`map`, `filter`, `reduce`, `forEach`
  - 事件监听：`addEventListener('click', callback)`
  - 定时器：`setTimeout(callback, delay)`
  - 函数节流/防抖：返回包装后的函数
- **最佳实践**：
  - 保持高阶函数功能单一，职责明确。
  - 使用箭头函数简化返回函数的语法。
  - 对于可能异步执行的函数，考虑错误处理（如传递错误优先回调）。

### 注意事项
- **常见错误**：
  - 在调用高阶函数时误将函数执行结果传入（`fn()`而不是`fn`）。
  - 忽略回调函数的`this`指向问题，可使用箭头函数或`bind`解决。
- **性能问题**：过度使用高阶函数可能导致调用栈加深，但一般可接受。注意避免在循环中创建不必要的闭包。
- **兼容性**：数组高阶方法在ES5引入，现代环境支持良好。旧浏览器可能需要polyfill。

### 代码示例
```javascript
// 接受函数参数：抽象重复操作
function repeat(n, action) {
    for (let i = 0; i < n; i++) {
        action(i);
    }
}
repeat(3, console.log); // 0, 1, 2

// 返回函数：比较器工厂
function greaterThan(n) {
    return m => m > n;
}
const greaterThan10 = greaterThan(10);
console.log(greaterThan10(11)); // true

// 内置高阶函数：map
const numbers = [1, 2, 3];
const doubled = numbers.map(x => x * 2);
console.log(doubled); // [2, 4, 6]
```

## 三、回调函数

### 概念
- **是什么**：回调函数（Callback Function）是作为参数传递给另一个函数，并在某个特定时刻被“唤回”调用的函数。
- **为什么需要**：JavaScript是单线程异步驱动的语言，回调函数允许我们在操作完成（如用户点击、数据响应）后执行后续逻辑，避免阻塞主线程。
- **解决什么问题**：处理异步操作的结果、响应事件、实现控制反转（将“做什么”的决定权交给调用者）。

### 原理
- **底层实现**：依赖于事件循环（Event Loop）。当异步操作完成时，对应的回调函数被放入任务队列，等待主线程空闲时执行。
- **工作流程**：
  1. 同步代码先执行。
  2. 异步操作（如`setTimeout`）将回调函数注册到Web API，完成后放入任务队列。
  3. 主线程执行完所有同步代码后，检查微任务队列，然后取一个宏任务执行。
  4. 重复此过程（事件循环）。
- **关键技术点**：
  - 同步回调（如数组`forEach`）立即在当前栈执行。
  - 异步回调（如`setTimeout`）延迟执行。
  - 微任务（Promise回调）优先级高于宏任务。

### 使用方法
- **基本用法**：将函数作为参数传递，并在高阶函数内部调用。
- **常见场景**：
  - 事件监听：`button.onclick = handleClick`
  - 定时器：`setTimeout(handleTimeout, 1000)`
  - 网络请求：`fetch(url).then(handleResponse)`
  - Node.js文件操作：`fs.readFile(path, callback)`
- **最佳实践**：
  - 为回调函数命名，便于调试。
  - 遵循错误优先模式（Node.js风格）：`callback(err, result)`。
  - 避免回调地狱，使用Promise或async/await。

### 注意事项
- **常见错误**：
  - 回调地狱：多层嵌套导致代码难以维护。
  - 错误处理不当：未捕获回调中的异常。
  - `this`指向问题：在回调中使用普通函数时`this`可能丢失，需使用箭头函数或`bind`。
- **性能问题**：过多的嵌套回调可能影响可读性，但对性能影响较小。需要注意内存泄漏，如未移除事件监听。
- **兼容性**：回调函数是基础特性，所有环境都支持。

### 代码示例
```javascript
// 同步回调：数组forEach
[1, 2, 3].forEach(item => {
    console.log(item); // 立即执行
});

// 异步回调：setTimeout
console.log('开始');
setTimeout(() => {
    console.log('1秒后执行');
}, 1000);
console.log('结束');
// 输出顺序：开始 -> 结束 -> (1秒后) 1秒后执行

// 错误优先回调风格
function readFile(path, callback) {
    // 模拟异步读取
    setTimeout(() => {
        if (path === 'valid.txt') {
            callback(null, '文件内容');
        } else {
            callback(new Error('文件不存在'));
        }
    }, 100);
}

readFile('valid.txt', (err, data) => {
    if (err) {
        console.error(err);
    } else {
        console.log(data);
    }
});
```

## 四、闭包

### 概念
- **是什么**：闭包（Closure）是指函数与其词法环境的组合。具体地，当一个内部函数引用了外部函数的变量，并且该内部函数在外部函数之外被调用时，就形成了闭包。
- **为什么需要**：闭包允许函数记住并访问其定义时的作用域，即使该函数在其作用域外执行。这为数据封装、函数工厂、模块模式等提供了基础。
- **解决什么问题**：模拟私有变量、创建具有持久状态的函数、实现柯里化和偏函数。

### ##### 原理
- **底层实现**：JavaScript引擎在函数定义时会保存作用域链。当内部函数被返回并在外部执行时，其作用域链仍然包含外部函数的变量对象，因此这些变量不会被垃圾回收。
- **工作流程**：
  1. 外部函数执行，创建执行上下文，变量对象被创建。
  2. 内部函数定义，其`[[Scope]]`属性指向外部函数的变量对象。
  3. 外部函数返回内部函数的引用。
  4. 外部函数执行结束，但其变量对象因被内部函数引用而保留在内存中。
  5. 调用内部函数时，通过作用域链访问外部变量。
- **关键技术点**：词法作用域、垃圾回收机制、作用域链。

### ##### 使用方法
- **基本用法**：在函数内定义另一个函数并返回它，内部函数引用外部函数的变量。
- **常见场景**：
  - 封装私有变量：模块模式、单例模式。
  - 函数工厂：生成特定功能的函数（如幂函数）。
  - 柯里化：将多参数函数转为单参数链式调用。
  - 事件处理中保存状态。
- **最佳实践**：
  - 明确闭包的目的，避免无意识创建闭包导致内存泄漏。
  - 在不再需要闭包时，解除引用（如设为`null`）以允许垃圾回收。
  - 在循环中创建闭包时，注意变量捕获问题（使用`let`或IIFE）。

### ##### 注意事项
- **常见错误**：
  - 循环中创建闭包捕获循环变量（var导致共享同一变量）。
  - 误以为闭包会复制变量的值，实际上是引用。
  - 在不需要时创建闭包，导致内存占用。
- **性能问题**：闭包会增加内存消耗，因为被引用的变量无法回收。大量使用闭包可能影响性能，需合理设计。
- **兼容性**：闭包是JavaScript核心特性，所有环境支持。

### ##### 代码示例
```javascript
// 基本闭包：计数器
function createCounter() {
    let count = 0;
    return {
        increment() { return ++count; },
        decrement() { return --count; },
        getCount() { return count; }
    };
}
const counter = createCounter();
console.log(counter.increment()); // 1
console.log(counter.increment()); // 2
console.log(counter.decrement()); // 1

// 函数工厂：幂函数
function makePower(power) {
    return function(num) {
        return Math.pow(num, power);
    };
}
const square = makePower(2);
console.log(square(5)); // 25

// 循环陷阱及解决
// 错误示例（var）
function createFunctions() {
    var funcs = [];
    for (var i = 0; i < 3; i++) {
        funcs.push(function() { return i; });
    }
    return funcs;
}
const fns = createFunctions();
console.log(fns[0]()); // 3（期望0）

// 正确（let）
function createFunctionsFixed() {
    const funcs = [];
    for (let i = 0; i < 3; i++) {
        funcs.push(() => i);
    }
    return funcs;
}
const fnsFixed = createFunctionsFixed();
console.log(fnsFixed[0]()); // 0
```

## 五、纯函数

### ##### 概念
- **是什么**：纯函数（Pure Function）是指满足以下两个条件的函数：
  1. 相同的输入始终得到相同的输出。
  2. 没有副作用（不修改外部状态、不依赖外部可变状态）。
- **为什么需要**：纯函数使代码更可预测、易于测试和调试，是函数式编程的基石。
- **解决什么问题**：减少bug来源，支持引用透明性（可用计算结果替换函数调用），便于缓存和并行计算。

### ##### 原理
- **底层实现**：纯函数内部只使用传入参数和局部变量，不访问或修改全局变量、DOM、文件系统等。其返回值完全由参数决定。
- **工作流程**：函数执行时，只在其局部作用域内操作，不产生对外部的影响。
- **关键技术点**：引用透明、无副作用、可缓存性。

### ##### 使用方法
- **基本用法**：编写不依赖外部状态、不修改输入参数的函数。
- **常见场景**：
  - 工具函数（如数学计算、字符串处理）。
  - Redux的reducer。
  - 数据转换（如`map`、`filter`的回调）。
- **最佳实践**：
  - 避免修改传入的对象或数组，应返回新副本。
  - 不要使用随机数、当前时间等不稳定值。
  - 尽量声明所有依赖为参数。

### ##### 注意事项
- **常见错误**：
  - 不经意修改了对象属性（如`obj.prop = ...`）。
  - 调用非纯函数（如`console.log`、`Math.random`）产生副作用。
- **性能问题**：返回新副本可能带来额外内存开销，但通常可接受。对于大对象可使用持久化数据结构优化。
- **兼容性**：纯函数是编码规范，与语言版本无关。

### ##### 代码示例
```javascript
// 纯函数
function add(a, b) {
    return a + b;
}

// 非纯函数：依赖外部变量
let tax = 0.1;
function calculatePrice(price) {
    return price * (1 + tax); // tax可能变化
}

// 非纯函数：修改输入
function addToArray(arr, item) {
    arr.push(item); // 修改了原数组
    return arr;
}

// 纯函数版本
function addToArrayPure(arr, item) {
    return [...arr, item]; // 返回新数组
}

// 使用纯函数的好处：可缓存
const memoizedAdd = (() => {
    const cache = {};
    return function(a, b) {
        const key = `${a},${b}`;
        if (cache[key] !== undefined) return cache[key];
        const result = a + b;
        cache[key] = result;
        return result;
    };
})();
```

## 六、函数组合

### ##### 概念
- **是什么**：函数组合（Function Composition）是将多个函数组合成一个新函数的过程，新函数按照顺序（从右向左或从左向右）应用这些函数。
- **为什么需要**：组合允许我们将小型、单一职责的函数组合成复杂操作，提高代码的模块化和可读性。
- **解决什么问题**：避免深层嵌套的函数调用，实现声明式的数据处理管道。

### ##### 原理
- **底层实现**：通过高阶函数接收多个函数，返回一个新函数，该函数内部依次调用这些函数，并将前一个结果作为后一个的输入。
- **工作流程**：
  - `compose`：从右向左执行（数学中复合函数的顺序）。
  - `pipe`：从左向右执行（Unix管道风格）。
- **关键技术点**：函数返回值传递、参数个数控制（一元函数组合最自然）。

### ##### 使用方法
- **基本用法**：定义`compose`或`pipe`工具函数，传入多个函数。
- **常见场景**：
  - 数据处理：对数据应用一系列变换。
  - 中间件系统（如Koa、Redux中间件）。
  - 将多个验证函数组合成一个验证器。
- **最佳实践**：
  - 保持组合的函数为单参数（一元函数），多参数可通过柯里化处理。
  - 组合顺序要符合逻辑，通常数据流从左到右更直观（使用pipe）。

### ##### 注意事项
- **常见错误**：
  - 组合的函数参数个数不匹配（如接收两个参数的函数需要先柯里化）。
  - 忽略函数的执行顺序，导致错误结果。
- **性能问题**：每次组合都会创建新函数，但开销很小。大量组合时注意栈深度。
- **兼容性**：需要手动实现或使用库（如lodash的`flow`）。

### ##### 代码示例
```javascript
// 组合函数实现
function compose(...fns) {
    return x => fns.reduceRight((acc, fn) => fn(acc), x);
}
function pipe(...fns) {
    return x => fns.reduce((acc, fn) => fn(acc), x);
}

// 示例函数
const toUpperCase = str => str.toUpperCase();
const exclaim = str => `${str}!`;
const repeat = str => `${str} ${str}`;

// compose从右向左：先repeat，然后toUpperCase，最后exclaim
const shout = compose(exclaim, toUpperCase, repeat);
console.log(shout('hello')); // HELLO HELLO!

// pipe从左向右：先repeat，然后toUpperCase，最后exclaim
const shoutPipe = pipe(repeat, toUpperCase, exclaim);
console.log(shoutPipe('hello')); // HELLO HELLO!

// 实际应用：数据处理
const users = [
    { name: '张三', age: 30, active: true },
    { name: '李四', age: 25, active: false },
    { name: '王五', age: 35, active: true }
];
const getActiveUserNames = pipe(
    users => users.filter(u => u.active),
    users => users.map(u => u.name),
    names => names.sort()
);
console.log(getActiveUserNames(users)); // ['张三', '王五']
```

## 七、柯里化

### ##### 概念
- **是什么**：柯里化（Currying）是将一个接受多个参数的函数转换为一系列接受单个参数的函数的过程。
- **为什么需要**：柯里化允许我们逐步提供参数，实现参数复用和延迟计算，便于函数组合。
- **解决什么问题**：将多参数函数适配到一元函数组合的模型中，创建更灵活的函数工厂。

### ##### 原理
- **底层实现**：通过闭包保存已传入的参数，返回新函数等待剩余参数，直到所有参数收集完毕才执行原函数。
- **工作流程**：
  1. 原函数`fn(a, b, c)`被柯里化为`curriedFn(a)(b)(c)`。
  2. 每次调用返回一个新函数，该函数通过闭包记住已传入的参数。
  3. 当提供的参数数量达到原函数所需时，执行原函数并返回结果。
- **关键技术点**：参数长度检测（函数的`length`属性）、递归收集参数。

### ##### 使用方法
- **基本用法**：编写柯里化函数转换器，或将函数手动转换为嵌套返回形式。
- **常见场景**：
  - 函数工厂：固定某些参数（如`add(1)(2)`）。
  - 与函数组合结合：将多参数函数转换为单参数函数以便组合。
  - 日志格式化：先固定前缀，再传消息。
- **最佳实践**：
  - 柯里化函数通常设计为从前往后逐步传入参数。
  - 可配合占位符实现更灵活的偏函数应用。
  - 不要过度柯里化，保持代码可读性。

### ##### 注意事项
- **常见错误**：
  - 柯里化后的函数调用方式错误（如`curriedAdd(1,2)`可能不会按预期工作）。
  - 忘记在参数收集完成后调用（实际上最后一个调用会执行）。
- **性能问题**：柯里化会创建多层闭包，但一般开销可接受。
- **兼容性**：需手动实现或使用库（如lodash的`curry`）。

### ##### 代码示例
```javascript
// 手动柯里化
function add(a) {
    return function(b) {
        return function(c) {
            return a + b + c;
        };
    };
}
console.log(add(1)(2)(3)); // 6

// 通用柯里化函数
function curry(fn) {
    return function curried(...args) {
        if (args.length >= fn.length) {
            return fn.apply(this, args);
        } else {
            return function(...more) {
                return curried.apply(this, args.concat(more));
            };
        }
    };
}

// 使用通用柯里化
function sum(a, b, c) {
    return a + b + c;
}
const curriedSum = curry(sum);
console.log(curriedSum(1)(2)(3)); // 6
console.log(curriedSum(1, 2)(3)); // 6
console.log(curriedSum(1)(2, 3)); // 6

// 实际应用：日志函数
function log(level, timestamp, message) {
    console.log(`[${level}] ${timestamp}: ${message}`);
}
const curriedLog = curry(log);
const errorLog = curriedLog('ERROR')(new Date().toISOString());
errorLog('发生错误'); // [ERROR] 2026-...: 发生错误
```

## 八、偏函数

### ##### 概念
- **是什么**：偏函数（Partial Application）是指固定一个函数的部分参数，生成一个新函数，这个新函数接收剩余的参数。
- **为什么需要**：与柯里化类似，但更灵活，可以一次固定多个参数，且不要求参数逐个传入。
- **解决什么问题**：当我们需要多次调用同一个函数，且部分参数相同时，可以使用偏函数减少重复代码。

### ##### 原理
- **底层实现**：通过高阶函数接收原函数和预设参数，返回新函数，新函数在调用时将预设参数与后续参数合并后调用原函数。
- **工作流程**：
  1. `partial(fn, ...presetArgs)`返回一个新函数。
  2. 新函数被调用时，将`presetArgs`与新传入的参数合并，然后调用`fn`。
- **关键技术点**：参数合并顺序、占位符支持（用于跳过某些预设位置）。

### ##### 使用方法
- **基本用法**：定义`partial`工具函数，传入函数和要固定的参数。
- **常见场景**：
  - 接口请求：固定URL前缀，剩下传具体路径。
  - 事件处理：固定事件类型，剩下传处理函数。
  - 减少样板代码：如固定`console.log`的第一个参数为标签。
- **最佳实践**：
  - 通常预设参数放在前面，剩余参数放在后面。
  - 如果需要跳过位置，可以使用占位符（如`_`）。

### ##### 注意事项
- **常见错误**：
  - 参数顺序错误，导致逻辑错误。
  - 预设参数与剩余参数的类型不匹配。
- **性能问题**：偏函数每次调用都会创建新函数，但通常开销很小。
- **兼容性**：需手动实现或使用库。

### ##### 代码示例
```javascript
// 简单偏函数实现
function partial(fn, ...presetArgs) {
    return function(...laterArgs) {
        return fn(...presetArgs, ...laterArgs);
    };
}

// 示例函数
function greet(greeting, name, punctuation) {
    return `${greeting}, ${name}${punctuation}`;
}

const sayHello = partial(greet, 'Hello');
console.log(sayHello('张三', '!')); // Hello, 张三!

const sayHiToJohn = partial(greet, 'Hi', 'John');
console.log(sayHiToJohn('?')); // Hi, John?

// 带占位符的偏函数（复杂版）
function partialWithPlaceholders(fn, ...presetArgs) {
    const _ = partialWithPlaceholders._;
    return function(...laterArgs) {
        let args = [];
        let laterIndex = 0;
        for (let arg of presetArgs) {
            if (arg === _) {
                args.push(laterArgs[laterIndex++]);
            } else {
                args.push(arg);
            }
        }
        // 剩余参数追加
        args = [...args, ...laterArgs.slice(laterIndex)];
        return fn(...args);
    };
}
partialWithPlaceholders._ = Symbol('placeholder');

// 使用占位符
const greetPartial = partialWithPlaceholders(
    greet,
    partialWithPlaceholders._,
    '李四',
    partialWithPlaceholders._
);
console.log(greetPartial('Hi', '?')); // Hi, 李四?
```

## 九、函子（Functor）

### ##### 概念
- **是什么**：函子（Functor）是一个实现了`map`方法的容器，它允许我们对容器内的值应用一个函数，并返回一个新的容器。常见的函子包括`Array`、`Maybe`、`Either`等。
- **为什么需要**：函子提供了一种统一的接口来操作包装在上下文中的值（如可选值、异步值、异常），使得我们可以用一致的方式处理这些上下文，而不需要手动展开。
- **解决什么问题**：避免空值检查、错误处理的重复代码，支持函数式编程中的副作用隔离。

### ##### 原理
- **底层实现**：函子内部保存一个值，`map`方法接收一个函数，将该函数应用于内部值，然后将结果包装回一个新的同类函子。
- **工作流程**：
  1. 创建函子实例（如`Maybe.of(value)`）。
  2. 调用`map`传入变换函数。
  3. `map`根据函子类型决定如何处理（如Maybe会检查值是否为null）。
  4. 返回新函子。
- **关键技术点**：值不变性（每次`map`返回新实例）、函子定律（恒等性和组合性）。

### ##### 使用方法
- **基本用法**：创建自定义函子或使用内置数组（数组也是函子）。
- **常见场景**：
  - 安全处理可能为`null`的值（`Maybe`）。
  - 函数式错误处理（`Either`）。
  - 异步操作（`Promise`可以看作函子，但`then`不完全等同`map`）。
- **最佳实践**：
  - 函子的`map`应保持结构不变，只改变内部值。
  - 避免在`map`中引入副作用。

### ##### 注意事项
- **常见错误**：
  - 修改原函子内部值（应返回新实例）。
  - 混淆`map`和`flatMap`（用于处理嵌套函子）。
- **性能问题**：函子链式调用可能创建多个中间对象，但现代引擎优化良好。
- **兼容性**：需手动实现，或使用函数式编程库（如folktale、ramda）。

### ##### 代码示例
```javascript
// Maybe函子：处理空值
class Maybe {
    constructor(value) {
        this._value = value;
    }
    static of(value) {
        return new Maybe(value);
    }
    isNothing() {
        return this._value === null || this._value === undefined;
    }
    map(f) {
        return this.isNothing() ? Maybe.of(null) : Maybe.of(f(this._value));
    }
    getOrElse(defaultValue) {
        return this.isNothing() ? defaultValue : this._value;
    }
}

// 使用
const user = { address: { street: '长安街' } };
const street = Maybe.of(user)
    .map(u => u.address)
    .map(addr => addr.street)
    .getOrElse('未知');
console.log(street); // 长安街

const emptyUser = null;
const street2 = Maybe.of(emptyUser)
    .map(u => u.address)
    .map(addr => addr.street)
    .getOrElse('未知');
console.log(street2); // 未知

// Either函子：错误处理
class Either {
    constructor(value) {
        this._value = value;
    }
    static left(value) {
        return new Left(value);
    }
    static right(value) {
        return new Right(value);
    }
}
class Left extends Either {
    map(f) { return this; }
    getOrElse(defaultValue) { return defaultValue; }
}
class Right extends Either {
    map(f) { return Either.right(f(this._value)); }
    getOrElse(defaultValue) { return this._value; }
}

function divide(a, b) {
    if (b === 0) return Either.left('除数不能为0');
    return Either.right(a / b);
}

const result = divide(10, 2).map(x => x * 3).getOrElse(0);
console.log(result); // 15
const errorResult = divide(10, 0).map(x => x * 3).getOrElse(0);
console.log(errorResult); // 0
```

## 十、防抖与节流

### ##### 概念
- **是什么**：防抖（Debounce）和节流（Throttle）是两种限制函数执行频率的技术。
  - **防抖**：当事件连续触发时，只有在最后一次触发后等待一定时间没有新触发，才执行函数。
  - **节流**：在一定时间间隔内，函数最多执行一次。
- **为什么需要**：优化高频事件（如滚动、输入、resize）的性能，避免过多计算或请求。
- **解决什么问题**：减少资源消耗，提升用户体验。

### ##### 原理
- **底层实现**：
  - **防抖**：通过`setTimeout`延迟执行，每次事件触发清除前一个定时器并重新设置。
  - **节流**：通过时间戳判断是否超过间隔，或使用定时器保证间隔内只执行一次。
- **工作流程**：
  - 防抖：事件触发 -> 清除timer -> 设置新timer -> timer到达后执行。
  - 节流（时间戳版）：记录上次执行时间，当前时间减上次时间大于间隔则执行并更新时间。
- **关键技术点**：闭包保存定时器ID或上次执行时间。

### ##### 使用方法
- **基本用法**：编写`debounce`和`throttle`工具函数，返回包装后的函数。
- **常见场景**：
  - 防抖：搜索框输入（等待用户停止输入后发送请求）、窗口resize（调整结束后重新计算）。
  - 节流：滚动事件（每隔一段时间计算位置）、按钮点击防止重复提交。
- **最佳实践**：
  - 根据需求选择防抖或节流。
  - 注意`this`指向和参数传递，使用`apply`确保正确。
  - 考虑是否需要立即执行选项（如防抖的第一次立即执行）。

### ##### 注意事项
- **常见错误**：
  - 忘记清除定时器导致内存泄漏。
  - 在类组件中使用时未绑定`this`。
- **性能问题**：本身是性能优化手段，正确使用无性能问题。
- **兼容性**：需手动实现或使用lodash。

### ##### 代码示例
```javascript
// 防抖
function debounce(fn, delay, immediate = false) {
    let timer = null;
    return function(...args) {
        const context = this;
        if (timer) clearTimeout(timer);
        if (immediate && !timer) {
            // 立即执行
            fn.apply(context, args);
            timer = setTimeout(() => {
                timer = null;
            }, delay);
        } else {
            timer = setTimeout(() => {
                fn.apply(context, args);
                timer = null;
            }, delay);
        }
    };
}

// 节流（时间戳版）
function throttle(fn, interval) {
    let lastTime = 0;
    return function(...args) {
        const context = this;
        const now = Date.now();
        if (now - lastTime >= interval) {
            fn.apply(context, args);
            lastTime = now;
        }
    };
}

// 使用示例
const handleInput = debounce(function(e) {
    console.log('搜索:', e.target.value);
}, 500);
document.querySelector('#search').addEventListener('input', handleInput);

const handleScroll = throttle(function() {
    console.log('滚动位置:', window.scrollY);
}, 200);
window.addEventListener('scroll', handleScroll);
```

## 十一、once函数（单次执行）

### ##### 概念
- **是什么**：`once`函数确保一个函数只被执行一次，后续调用返回第一次执行的结果。
- **为什么需要**：用于初始化操作、事件绑定只执行一次的场景，防止重复执行导致错误或浪费。
- **解决什么问题**：避免重复初始化、多次提交表单、重复添加事件监听等。

### ##### 原理
- **底层实现**：通过闭包保存一个标志位（是否已执行）和第一次执行的结果。第一次调用时执行原函数并保存结果，后续调用直接返回保存的结果。
- **工作流程**：
  1. 返回的函数首次被调用时，执行原函数，将结果缓存，并将标志置为已执行。
  2. 后续调用直接返回缓存结果，不再执行原函数。
- **关键技术点**：闭包缓存结果、标志位控制。

### ##### 使用方法
- **基本用法**：`const initializedFn = once(initFunction);`
- **常见场景**：
  - 数据库连接初始化。
  - 事件监听只添加一次（如使用`once: true`选项的替代）。
  - 惰性加载单例。
- **最佳实践**：确保原函数不依赖于后续变化的参数（如果需要参数，通常只在第一次调用时使用）。

### ##### 注意事项
- **常见错误**：
  - 如果原函数有副作用，第二次调用不会发生副作用，需确保设计符合预期。
  - 在需要不同参数调用时，`once`可能不合适。
- **性能问题**：极低。
- **兼容性**：需手动实现。

### ##### 代码示例
```javascript
function once(fn) {
    let called = false;
    let result;
    return function(...args) {
        if (!called) {
            called = true;
            result = fn.apply(this, args);
        }
        return result;
    };
}

// 使用
const initialize = once(() => {
    console.log('初始化执行一次');
    return { initialized: true };
});

console.log(initialize()); // 输出"初始化执行一次"，返回{initialized: true}
console.log(initialize()); // 直接返回{initialized: true}，不再执行
```

## 十二、调试技巧：tap与trace

### ##### 概念
- **是什么**：`tap`和`trace`是用于在函数链式调用中插入调试的函数，它们在不改变数据流的情况下观察中间值。
- **为什么需要**：在函数组合或链式调用中，我们可能希望查看每个阶段的输出，但又不希望破坏原有的数据传递。
- **解决什么问题**：提供一种无侵入的方式调试函数式管道。

### ##### 原理
- **底层实现**：
  - `tap`接收一个值和副作用函数，执行副作用后返回原值。
  - `trace`接收一个标签，返回一个函数，该函数接收值，打印标签和值，然后返回值。
- **工作流程**：在管道中插入`tap`或`trace`，值会流经它们，同时打印信息，然后继续传递。
- **关键技术点**：高阶函数、副作用隔离。

### ##### 使用方法
- **基本用法**：`pipe(step1, trace('after step1'), step2)`
- **常见场景**：
  - 调试复杂的数据处理管道。
  - 记录日志而不影响结果。
- **最佳实践**：确保`tap`中的函数是纯副作用（如`console.log`），不会改变值。

### ##### 注意事项
- **常见错误**：在`tap`中错误地修改了值（应只读）。
- **性能问题**：调试函数应在开发环境使用，生产环境移除。
- **兼容性**：手动实现简单。

### 代码示例
```javascript
function tap(value, fn) {
    fn(value);
    return value;
}

function trace(label) {
    return value => {
        console.log(`${label}:`, value);
        return value;
    };
}

// 使用
const result = [1, 2, 3]
    .map(x => x * 2)
    .filter(x => {
        tap(x, val => console.log('过滤前:', val));
        return x > 3;
    })
    .reduce((acc, x) => acc + x, 0);
console.log('结果:', result);

// 在管道中使用
const process = pipe(
    x => x + 1,
    trace('加1后'),
    x => x * 2,
    trace('乘2后'),
    x => x - 3
);
console.log(process(5));
// 输出:
// 加1后: 6
// 乘2后: 12
// 9
```

---

*本笔记基于ECMAScript规范及权威教材整理，涵盖了JavaScript中高阶函数、回调函数、闭包以及相关函数式编程概念的核心知识点，并按照指定模板进行了系统化组织。*