## 一、对象字面量 `{}` 创建

#### 概念
- **是什么**：使用花括号 `{}` 直接定义对象的方式，也称为对象初始化器。在大括号内以 `key: value` 的形式列出属性。
- **为什么需要**：这是创建单个对象最简洁、最直观的方法，无需调用构造函数，代码量少，可读性强。
- **解决什么问题**：快速创建结构明确、属性固定的对象，适用于大多数普通对象的初始化场景。

#### 原理
- **底层实现**：JavaScript 引擎在解析到对象字面量时，会在内存中分配一个新对象，并根据字面量中的键值对设置属性。每次执行到字面量都会创建一个独立的对象实例。
- **工作流程**：解析 → 创建空对象 → 按顺序添加属性 → 返回对象引用。
- **关键技术点**：属性名可以是标识符或字符串，值可以是任意表达式；支持嵌套对象和方法定义。

#### 使用方法
- **基本用法**：
  ```javascript
  const person = {
    name: 'Alice',
    age: 30,
    greet() { console.log('Hello'); }
  };
  ```
- **常见场景**：
  - 创建配置对象、数据传输对象（DTO）
  - 定义单例对象、工具模块
  - 作为函数参数的默认选项
- **最佳实践**：
  - 使用简洁的 ES6 方法语法定义函数属性。
  - 多词属性名用引号包裹，并通过方括号访问。
  - 保持对象结构扁平，避免过深嵌套。

#### 注意事项
- **常见错误**：忘记属性值前的逗号导致语法错误；在属性值中使用未定义的变量。
- **性能问题**：字面量创建非常高效，但频繁在循环中创建大对象可能影响性能。
- **兼容性**：所有 JavaScript 环境都支持，ES6 的简洁方法语法在 IE 中不支持。

#### 代码示例
```javascript
const car = {
  brand: 'Toyota',
  model: 'Camry',
  year: 2020,
  start() {
    console.log(`${this.brand} ${this.model} started.`);
  }
};
car.start(); // 输出: Toyota Camry started.
```

## 二、使用 `new Object()` 创建

#### 概念
- **是什么**：通过内置构造函数 `Object` 并使用 `new` 关键字创建对象。
- **为什么需要**：提供了一种与字面量等效的编程式创建对象的方式，在早期 JavaScript 中常用。
- **解决什么问题**：当无法使用字面量语法（如动态确定属性）时，可以作为备选方案。

#### 原理
- **底层实现**：`new Object()` 调用构造函数，内部会创建一个新对象，将新对象的 `__proto__` 指向 `Object.prototype`，然后返回该对象。
- **工作流程**：分配内存 → 将原型指向 `Object.prototype` → 返回对象。
- **关键技术点**：与 `{}` 创建的对象完全相同，都继承自 `Object.prototype`。

#### 使用方法
- **基本用法**：
  ```javascript
  const obj = new Object();
  obj.name = 'Bob';
  ```
- **常见场景**：
  - 需要显式调用构造函数时
  - 在无法使用字面量的极少数动态代码中
- **最佳实践**：通常推荐使用字面量 `{}`，因为它更简洁且性能略优。

#### 注意事项
- **常见错误**：忘记 `new` 关键字，此时 `Object()` 会被当作普通函数调用，效果相同但存在歧义。
- **性能问题**：相比字面量，`new Object()` 多了一次函数调用，但差异微乎其微。
- **兼容性**：所有环境均支持。

#### 代码示例
```javascript
const user = new Object();
user.name = 'Charlie';
user.age = 25;
console.log(user); // { name: 'Charlie', age: 25 }
```

## 三、使用构造函数创建

#### 概念
- **是什么**：定义一个普通的函数，通过 `new` 关键字调用，用来创建多个结构相似的对象。
- **为什么需要**：实现类似“类”的功能，批量创建具有相同属性和方法的对象，复用代码。
- **解决什么问题**：避免重复编写对象字面量，支持通过参数初始化对象。

#### 原理
- **底层实现**：当使用 `new` 调用函数时，JavaScript 会自动创建一个新对象，将函数的 `this` 绑定到这个新对象，并将新对象的 `__proto__` 指向构造函数的 `prototype` 属性，最后返回这个对象（除非函数显式返回另一个对象）。
- **工作流程**：`new` 操作符 → 创建空对象 → 链接原型 → 绑定 `this` → 执行构造函数 → 返回对象。
- **关键技术点**：构造函数名通常首字母大写；通过 `this` 添加属性；方法定义在 `prototype` 上以共享。

#### 使用方法
- **基本用法**：
  ```javascript
  function Person(name, age) {
    this.name = name;
    this.age = age;
  }
  Person.prototype.greet = function() {
    console.log(`Hi, I'm ${this.name}`);
  };
  const p1 = new Person('Alice', 30);
  ```
- **常见场景**：
  - 需要创建多个相似对象（如用户、商品、组件实例）
  - 实现基于原型的继承
- **最佳实践**：
  - 将共享方法定义在 `prototype` 上以节省内存。
  - 使用 ES6 `class` 语法作为现代替代，但底层仍是构造函数。

#### 注意事项
- **常见错误**：忘记 `new` 关键字，导致 `this` 指向全局对象，污染全局作用域。
- **性能问题**：构造函数本身无显著性能问题，但每个实例的方法如果定义在 `this` 上则会占用更多内存。
- **兼容性**：所有环境均支持。

#### 代码示例
```javascript
function Book(title, author) {
  this.title = title;
  this.author = author;
}
Book.prototype.getInfo = function() {
  return `${this.title} by ${this.author}`;
};
const book1 = new Book('1984', 'George Orwell');
console.log(book1.getInfo()); // 1984 by George Orwell
```

## 四、使用 `Object.create()` 创建

#### 概念
- **是什么**：`Object.create(proto, propertiesObject)` 创建一个新对象，使用现有对象作为新对象的原型。
- **为什么需要**：提供更精细的原型控制，可以实现直接继承，创建无原型的纯净对象。
- **解决什么问题**：解决构造函数模式无法直接指定原型的问题，实现原型式继承。

#### 原理
- **底层实现**：内部创建一个空对象，将该对象的 `__proto__` 设置为 `proto` 参数，然后根据 `propertiesObject` 定义属性。
- **工作流程**：接收原型对象 → 创建新对象 → 链接原型 → 添加属性 → 返回对象。
- **关键技术点**：`proto` 必须为对象或 `null`；第二个参数可选，格式与 `Object.defineProperties` 一致。

#### 使用方法
- **基本用法**：
  ```javascript
  const animal = { speak() { console.log('...'); } };
  const dog = Object.create(animal);
  dog.name = 'Buddy';
  ```
- **常见场景**：
  - 实现原型式继承
  - 创建没有原型的“纯净”对象（用于字典映射，避免原型污染）
  - 克隆对象（浅拷贝）
- **最佳实践**：在需要明确原型链时使用，对于普通对象仍推荐字面量。

#### 注意事项
- **常见错误**：传入 `null` 创建的对象没有原型，无法使用 `toString` 等方法。
- **性能问题**：`Object.create` 比字面量稍慢，但通常可忽略。
- **兼容性**：ES5 引入，IE9+ 支持。

#### 代码示例
```javascript
// 创建一个纯净对象（无原型）
const pureMap = Object.create(null);
pureMap.key = 'value';
console.log(pureMap.toString); // undefined

// 原型式继承
const parent = { a: 1 };
const child = Object.create(parent);
child.b = 2;
console.log(child.a); // 1 (继承自 parent)
```

## 五、属性访问：点语法 vs 方括号

#### 概念
- **是什么**：访问对象属性的两种语法：点语法（`obj.key`）和方括号语法（`obj['key']`）。
- **为什么需要**：提供灵活的方式读取和设置属性值，适应不同的属性命名和动态需求。
- **解决什么问题**：点语法简洁直观；方括号支持变量和特殊字符，动态性更强。

#### 原理
- **底层实现**：点语法将标识符直接作为属性名访问；方括号先计算表达式，将结果转换为字符串后作为属性名访问。
- **工作流程**：点语法 → 检查属性是否存在 → 返回值或 `undefined`；方括号 → 计算表达式 → 转为字符串 → 检查属性 → 返回值。
- **关键技术点**：方括号内可以是变量、表达式，甚至函数调用；两者访问同一属性时结果相同。

#### 使用方法
- **基本用法**：
  ```javascript
  const obj = { name: 'Alice', 'my-age': 25 };
  console.log(obj.name);      // 点语法
  console.log(obj['my-age']); // 方括号，处理特殊字符
  const key = 'name';
  console.log(obj[key]);      // 动态属性名
  ```
- **常见场景**：
  - 已知属性名且符合标识符规则：使用点语法。
  - 属性名包含空格、连字符、数字开头：必须使用方括号。
  - 属性名动态生成（如循环变量、函数返回）：使用方括号。
- **最佳实践**：优先使用点语法，可读性更好；需要动态或特殊字符时用方括号。

#### 注意事项
- **常见错误**：使用点语法访问特殊字符属性（如 `obj.my-age` 被解析为减法运算）。
- **性能问题**：点语法略快于方括号（无需计算表达式），差异极小。
- **兼容性**：全支持。

#### 代码示例
```javascript
const user = {
  'first name': 'John',
  age: 30,
  '1stPlace': 'gold'
};
console.log(user['first name']); // John
console.log(user.age);           // 30
const prop = '1stPlace';
console.log(user[prop]);         // gold
```

## 六、添加/修改属性

#### 概念
- **是什么**：通过赋值语句为对象添加新属性或修改现有属性。
- **为什么需要**：对象是动态的，可以在创建后随时扩展或更新。
- **解决什么问题**：实现对象的动态行为，允许按需调整对象状态。

#### 原理
- **底层实现**：当赋值给一个不存在的属性时，JavaScript 引擎会在对象上创建该属性并赋值；如果属性已存在，则更新其值。
- **工作流程**：检查属性是否存在 → 如果不存在则创建并设置值 → 如果存在则更新值。
- **关键技术点**：属性可随时添加，无论对象是否通过字面量或构造函数创建。

#### 使用方法
- **基本用法**：
  ```javascript
  const car = { brand: 'Tesla' };
  car.model = 'Model 3';    // 添加
  car.brand = 'BMW';        // 修改
  ```
- **常见场景**：
  - 动态配置对象
  - 逐步构建复杂对象
  - 响应式数据更新
- **最佳实践**：尽量在对象创建时就定义好结构，避免过多动态添加导致代码难以维护。

#### 注意事项
- **常见错误**：在严格模式下，给冻结或密封对象赋值会抛出错误。
- **性能问题**：添加属性会触发对象结构变化，V8 等引擎会进行隐藏类转换，频繁添加可能影响性能。
- **兼容性**：全支持。

#### 代码示例
```javascript
const user = {};
user.name = 'Alice';
user.age = 25;
user.age = 26; // 修改
console.log(user); // { name: 'Alice', age: 26 }
```

## 七、删除属性：`delete` 操作符

#### 概念
- **是什么**：`delete` 操作符用于移除对象的自有属性。
- **为什么需要**：清理不再需要的属性，释放内存（实际由垃圾回收处理）。
- **解决什么问题**：动态管理对象结构，避免属性污染或实现对象重置。

#### 原理
- **底层实现**：`delete` 从对象中移除指定属性，如果属性不存在则返回 `true`；不能删除继承属性或不可配置属性。
- **工作流程**：检查属性是否存在且可配置 → 如果是，则删除并返回 `true`；否则返回 `false` 或在严格模式下抛出错误。
- **关键技术点**：`delete` 只影响自有属性，不影响原型链；删除数组元素会留下空位，不改变长度。

#### 使用方法
- **基本用法**：
  ```javascript
  const obj = { a: 1, b: 2 };
  delete obj.a;
  console.log(obj); // { b: 2 }
  ```
- **常见场景**：
  - 从配置对象中移除不需要的选项
  - 清理缓存对象
  - 实现类似“重置”功能
- **最佳实践**：只在必要时使用，过度删除可能降低代码可读性；对于数组，考虑使用 `splice` 而不是 `delete`。

#### 注意事项
- **常见错误**：试图删除变量或函数（它们不是对象属性）会失败或抛出语法错误。
- **性能问题**：删除属性可能导致引擎优化失效（如隐藏类变化），但通常影响不大。
- **兼容性**：全支持。

#### 代码示例
```javascript
const person = { name: 'Bob', age: 30, email: 'bob@example.com' };
delete person.email;
console.log(person.email); // undefined
```

## 八、属性存在性检测

#### 概念
- **是什么**：检测属性是否存在于对象中的几种方法：`in` 操作符、`hasOwnProperty()` 方法、与 `undefined` 比较。
- **为什么需要**：避免读取不存在的属性得到 `undefined` 而误判，需要准确知道属性是否存在。
- **解决什么问题**：区分“属性不存在”和“属性存在但值为 undefined”的情况。

#### 原理
- **底层实现**：
  - `in`：沿着原型链查找属性，返回布尔值。
  - `hasOwnProperty`：只检查对象自有属性，忽略原型链。
  - `undefined` 比较：通过属性访问结果是否为 `undefined` 判断，但无法区分属性不存在和值为 `undefined`。
- **工作流程**：取决于具体方法。
- **关键技术点**：`hasOwnProperty` 是继承自 `Object.prototype` 的方法，对于无原型对象需谨慎调用。

#### 使用方法
- **基本用法**：
  ```javascript
  const obj = { a: 1, b: undefined };
  console.log('a' in obj);           // true
  console.log(obj.hasOwnProperty('a')); // true
  console.log(obj.c !== undefined);  // false
  console.log(obj.b !== undefined);  // false (值为 undefined，误判)
  ```
- **常见场景**：
  - 安全地访问嵌套属性前检查
  - 遍历时过滤原型属性（`for...in` 中结合 `hasOwnProperty`）
  - 判断方法是否存在
- **最佳实践**：用 `in` 检测任意属性（包括原型），用 `hasOwnProperty` 检测自有属性；谨慎使用 `undefined` 比较。

#### 注意事项
- **常见错误**：使用 `obj.hasOwnProperty` 时，如果对象本身有同名属性覆盖，可能导致调用失败，可用 `Object.prototype.hasOwnProperty.call(obj, prop)` 保证安全。
- **性能问题**：无显著差异。
- **兼容性**：全支持。

#### 代码示例
```javascript
const parent = { inherited: true };
const child = Object.create(parent);
child.own = 'value';

console.log('inherited' in child);               // true
console.log(child.hasOwnProperty('inherited'));  // false
console.log('own' in child);                      // true
console.log(child.hasOwnProperty('own'));        // true
```

## 九、属性枚举与遍历

#### 概念
- **是什么**：遍历对象属性的几种方式：`for...in` 循环，以及 `Object.keys()`、`Object.values()`、`Object.entries()` 静态方法。
- **为什么需要**：访问对象的所有键或值，用于数据处理、转换或调试。
- **解决什么问题**：提供不同的遍历粒度和范围，满足不同需求。

#### 原理
- **底层实现**：
  - `for...in`：遍历对象及其原型链上所有可枚举属性。
  - `Object.keys/values/entries`：只返回对象自有可枚举属性，以数组形式呈现。
- **工作流程**：这些方法内部会收集属性名/值/键值对，然后返回数组或进行迭代。
- **关键技术点**：`for...in` 不保证顺序（但现代引擎通常按创建顺序）；数组方法顺序固定（按数字索引升序，然后按创建顺序）。

#### 使用方法
- **基本用法**：
  ```javascript
  const obj = { a: 1, b: 2, c: 3 };
  for (let key in obj) console.log(key);          // a, b, c
  console.log(Object.keys(obj));                  // ['a', 'b', 'c']
  console.log(Object.values(obj));                // [1, 2, 3]
  console.log(Object.entries(obj));               // [['a',1], ['b',2], ['c',3]]
  ```
- **常见场景**：
  - `for...in`：需要同时遍历原型属性时（如调试）。
  - `Object.keys`：获取所有键，然后进行数组操作（如 `map`）。
  - `Object.entries`：配合 `for...of` 进行键值对解构遍历。
- **最佳实践**：默认使用 `Object.keys` 遍历自有属性，避免原型污染；明确需要原型属性时才用 `for...in` 并配合 `hasOwnProperty`。

#### 注意事项
- **常见错误**：在 `for...in` 中修改对象属性可能导致无限循环或意外行为。
- **性能问题**：数组方法通常比 `for...in` 快，因为不涉及原型链查找。
- **兼容性**：`Object.keys/values/entries` 为 ES5/ES2017 特性，IE9+ 支持 `keys`，Edge 支持 `values/entries`。

#### 代码示例
```javascript
const user = { name: 'Alice', age: 30 };
Object.entries(user).forEach(([key, value]) => {
  console.log(`${key}: ${value}`);
});
// name: Alice
// age: 30
```

## 十、对象方法定义与简洁语法

#### 概念
- **是什么**：在对象字面量中，可以使用函数表达式或 ES6 的简洁语法定义方法。
- **为什么需要**：为对象添加行为，使其具有操作自身数据的能力。
- **解决什么问题**：封装与对象相关的逻辑，实现面向对象编程中的方法概念。

#### 原理
- **底层实现**：方法本质上就是函数类型的属性。简洁语法 `method() {}` 会被转换成 `method: function() {}` 的形式。
- **工作流程**：定义方法后，可以通过对象调用，方法内部的 `this` 指向调用对象。
- **关键技术点**：简洁方法不能用作构造函数（没有 `prototype` 属性），且不支持 `super` 关键字（但在 ES6 class 中可用）。

#### 使用方法
- **基本用法**：
  ```javascript
  const calculator = {
    value: 0,
    add(x, y) { return x + y; },
    increment() { this.value++; }
  };
  ```
- **常见场景**：
  - 为对象定义业务逻辑
  - 事件处理函数
  - 工具模块中的函数集合
- **最佳实践**：优先使用简洁语法，代码更简洁；方法应尽量短小，避免过深嵌套。

#### 注意事项
- **常见错误**：在简洁方法中使用箭头函数定义内部函数时，注意箭头函数不会绑定自己的 `this`，会捕获外层 `this`。
- **性能问题**：无显著差异。
- **兼容性**：ES6 简洁语法在 IE 中不支持，现代浏览器均支持。

#### 代码示例
```javascript
const counter = {
  count: 0,
  reset() {
    this.count = 0;
  },
  increment(step = 1) {
    this.count += step;
  }
};
counter.increment(2);
console.log(counter.count); // 2
```

## 十一、`this` 在对象方法中的指向

#### 概念
- **是什么**：在对象的方法内部，`this` 关键字指向调用该方法的对象。
- **为什么需要**：使方法能够访问和操作所属对象的其他属性，实现对象的状态和行为绑定。
- **解决什么问题**：解决函数执行时如何引用当前上下文的问题，是 JavaScript 动态绑定的核心。

#### 原理
- **底层实现**：`this` 的绑定是在函数调用时确定的，取决于调用方式。当函数作为对象的方法调用时，`this` 指向该对象。
- **工作流程**：函数调用 → 检查调用方式（方法调用、函数调用、构造函数调用等）→ 根据规则绑定 `this`。
- **关键技术点**：
  - 方法调用：`obj.method()` → `this = obj`
  - 普通函数调用：`fn()` → `this` 指向全局对象（非严格模式）或 `undefined`（严格模式）
  - 箭头函数：不绑定自己的 `this`，继承外层作用域的 `this`

#### 使用方法
- **基本用法**：
  ```javascript
  const user = {
    name: 'Alice',
    greet() {
      console.log(`Hello, I'm ${this.name}`);
    }
  };
  user.greet(); // Hello, I'm Alice
  ```
- **常见场景**：
  - 对象方法中访问其他属性
  - 事件监听器中访问触发元素（此时 `this` 指向元素）
  - 构造函数中使用 `this` 初始化实例属性
- **最佳实践**：避免将方法赋值给变量后直接调用，以免丢失 `this`；可以使用 `bind`、`call`、`apply` 或箭头函数固定 `this`。

#### 注意事项
- **常见错误**：回调函数中 `this` 指向丢失，常见于定时器、事件处理等。
- **性能问题**：`this` 绑定本身开销很小，但频繁使用 `bind` 创建新函数可能增加内存。
- **兼容性**：全支持。

#### 代码示例
```javascript
const obj = {
  name: 'MyObject',
  logLater() {
    setTimeout(function() {
      console.log(this.name); // 这里的 this 指向全局对象（非严格模式）或 undefined
    }, 100);
  }
};
obj.logLater(); // 可能输出 undefined

// 修正：使用箭头函数
const obj2 = {
  name: 'MyObject',
  logLater() {
    setTimeout(() => {
      console.log(this.name); // 箭头函数继承外层 this，输出 MyObject
    }, 100);
  }
};
obj2.logLater(); // MyObject
```

## 十二、对象合并：`Object.assign()`

#### 概念
- **是什么**：`Object.assign(target, ...sources)` 方法将一个或多个源对象的所有可枚举自有属性复制到目标对象。
- **为什么需要**：合并多个对象的属性，用于配置合并、对象克隆等场景。
- **解决什么问题**：提供一种标准方式将多个对象的属性组合成一个新对象，避免手动循环赋值。

#### 原理
- **底层实现**：遍历每个源对象的可枚举自有属性，使用赋值操作（`[[Set]]`）将属性值复制到目标对象。
- **工作流程**：创建目标对象（如果传入空对象）→ 依次处理源对象 → 对每个属性调用目标对象的 `[[Set]]` 写入 → 返回目标对象。
- **关键技术点**：同名属性后面的源对象会覆盖前面的；只复制自有属性，不复制继承属性；`getter` 和 `setter` 会被作为普通值复制。

#### 使用方法
- **基本用法**：
  ```javascript
  const target = { a: 1 };
  const source = { b: 2, c: 3 };
  const result = Object.assign(target, source);
  console.log(target); // { a: 1, b: 2, c: 3 } (target 被修改)
  console.log(result === target); // true
  ```
- **常见场景**：
  - 合并默认配置和用户选项：`const options = Object.assign({}, defaults, userOptions);`
  - 浅拷贝对象：`const copy = Object.assign({}, original);`
  - 合并多个对象
- **最佳实践**：为避免修改原始对象，通常将目标对象设为 `{}`；确保源对象是可枚举属性；对于嵌套对象，`assign` 是浅拷贝。

#### 注意事项
- **常见错误**：误以为 `Object.assign` 执行深拷贝，实际上只复制属性引用。
- **性能问题**：对于大对象，合并操作可能较慢，但通常可接受。
- **兼容性**：ES6，IE 不支持，但在大多数现代环境可用。

#### 代码示例
```javascript
const defaults = { theme: 'light', showSidebar: true };
const userPrefs = { theme: 'dark' };
const settings = Object.assign({}, defaults, userPrefs);
console.log(settings); // { theme: 'dark', showSidebar: true }

// 浅拷贝示例
const original = { nested: { value: 42 } };
const copy = Object.assign({}, original);
copy.nested.value = 100;
console.log(original.nested.value); // 100 (互相影响)
```

## 十三、对象合并：展开运算符 `{...obj1, ...obj2}`

#### 概念
- **是什么**：在对象字面量中使用展开运算符 `...` 将一个对象的可枚举自有属性展开到新对象中。
- **为什么需要**：提供更简洁、直观的语法来合并对象，是 `Object.assign` 的语法糖。
- **解决什么问题**：简化对象合并和复制操作，提高代码可读性。

#### 原理
- **底层实现**：展开运算符是 ES2018 引入的语法，编译时会被转换成类似 `Object.assign` 的辅助函数（或由引擎直接支持）。
- **工作流程**：在对象字面量中，`...obj` 将 `obj` 的所有可枚举自有属性复制到当前对象，后面的同名属性会覆盖前面的。
- **关键技术点**：展开运算符只能用于可迭代对象？不，对象展开是特定的，用于字面量中；它执行的是浅拷贝。

#### 使用方法
- **基本用法**：
  ```javascript
  const obj1 = { a: 1, b: 2 };
  const obj2 = { c: 3 };
  const merged = { ...obj1, ...obj2, d: 4 };
  console.log(merged); // { a: 1, b: 2, c: 3, d: 4 }
  ```
- **常见场景**：
  - 合并多个对象
  - 创建对象的浅拷贝
  - 在 React/Redux 中更新状态
- **最佳实践**：与 `Object.assign` 类似，但语法更优雅；常用于函数式编程中不可变更新。

#### 注意事项
- **常见错误**：展开运算符在对象字面量外使用会报错；只能展开对象自身的可枚举属性。
- **性能问题**：与 `Object.assign` 相当。
- **兼容性**：ES2018（对象展开），Node.js 8.6+，现代浏览器支持；IE 不支持。

#### 代码示例
```javascript
const user = { name: 'Alice', age: 30 };
const location = { city: 'New York', country: 'USA' };
const fullProfile = { ...user, ...location, id: 123 };
console.log(fullProfile);
// { name: 'Alice', age: 30, city: 'New York', country: 'USA', id: 123 }

// 浅拷贝
const copy = { ...fullProfile };
console.log(copy === fullProfile); // false
```

## 十四、对象冻结：`Object.freeze()`

#### 概念
- **是什么**：`Object.freeze()` 方法冻结一个对象，使其不可变：不能添加新属性，不能删除或修改现有属性。
- **为什么需要**：创建真正的常量对象，防止意外修改，增强代码安全性和可预测性。
- **解决什么问题**：实现对象级别的不可变性，在函数式编程或配置管理中很有用。

#### 原理
- **底层实现**：将对象的所有自有属性设置为不可配置和不可写（如果属性是数据属性）；阻止添加新属性；冻结操作是浅层的。
- **工作流程**：遍历对象自有属性 → 将每个属性的 `writable` 和 `configurable` 设为 `false` → 设置 `[[PreventExtensions]]` 内部方法阻止扩展。
- **关键技术点**：冻结后不能解冻；试图修改在非严格模式下静默失败，严格模式下抛出 `TypeError`。

#### 使用方法
- **基本用法**：
  ```javascript
  const config = { api: 'https://api.example.com', timeout: 5000 };
  Object.freeze(config);
  config.timeout = 10000; // 无效
  delete config.api;       // 无效
  config.newProp = 'test'; // 无效
  console.log(config); // { api: 'https://api.example.com', timeout: 5000 }
  ```
- **常见场景**：
  - 定义常量配置对象
  - 防止第三方库修改内部状态
  - 实现不可变数据模式
- **最佳实践**：注意浅冻结，如需深冻结需递归调用；使用 `Object.isFrozen` 检查对象是否冻结。

#### 注意事项
- **常见错误**：忘记冻结是浅层的，嵌套对象仍可修改。
- **性能问题**：冻结操作本身有一定开销，但对后续访问无影响。
- **兼容性**：ES5，IE9+ 支持（IE9 部分支持）。

#### 代码示例
```javascript
const obj = Object.freeze({ inner: { value: 42 } });
obj.inner.value = 100; // 可以修改（因为 inner 对象本身未被冻结）
console.log(obj.inner.value); // 100

// 深冻结函数示例
function deepFreeze(obj) {
  Object.freeze(obj);
  Object.values(obj).forEach(val => {
    if (typeof val === 'object' && val !== null) deepFreeze(val);
  });
  return obj;
}
```

## 十五、对象密封：`Object.seal()`

#### 概念
- **是什么**：`Object.seal()` 方法密封一个对象，阻止添加新属性或删除现有属性，但允许修改现有属性的值。
- **为什么需要**：在允许修改属性值的同时，保持对象结构不变，防止意外添加或删除属性。
- **解决什么问题**：平衡灵活性和安全性，常用于需要固定结构但值可变的场景。

#### 原理
- **底层实现**：将对象所有自有属性设置为不可配置（`configurable: false`），但保持可写性（除非属性本身不可写）；阻止扩展。
- **工作流程**：遍历自有属性 → 设置 `configurable` 为 `false` → 设置 `[[PreventExtensions]]`。
- **关键技术点**：密封后属性不可删除，不可重新配置（如从数据属性改为访问器属性），但值可以修改（如果 `writable` 为 `true`）。

#### 使用方法
- **基本用法**：
  ```javascript
  const settings = { volume: 70, muted: false };
  Object.seal(settings);
  settings.volume = 50; // 可以修改
  delete settings.muted; // 无效
  settings.newProp = 'test'; // 无效
  console.log(settings); // { volume: 50, muted: false }
  ```
- **常见场景**：
  - 固定对象结构，但允许更新值（如组件状态）
  - 防止意外删除或添加属性
- **最佳实践**：使用 `Object.isSealed` 检查对象是否密封。

#### 注意事项
- **常见错误**：混淆 `seal` 和 `freeze`，`seal` 允许修改值。
- **性能问题**：与冻结类似。
- **兼容性**：ES5，IE9+ 支持。

#### 代码示例
```javascript
const user = { name: 'Alice', age: 30 };
Object.seal(user);
user.age = 31;        // 允许
user.email = 'a@b.com'; // 忽略
delete user.name;     // 忽略
console.log(user); // { name: 'Alice', age: 31 }
```

## 十六、属性标志与描述符（进阶）

#### 概念
- **是什么**：对象的每个属性除了值之外，还有三个隐藏的特性（标志）：`writable`（可写）、`enumerable`（可枚举）、`configurable`（可配置）。这些标志通过属性描述符对象来描述。
- **为什么需要**：精细控制属性的行为，例如设置只读属性、隐藏属性（不在 `for...in` 中出现）、防止删除等。
- **解决什么问题**：提供元编程能力，让开发者可以定义属性的更细致行为。

#### 原理
- **底层实现**：JavaScript 引擎内部为每个属性维护一个属性描述符记录，包含 `value` 和三个布尔标志，或者包含 `get`/`set` 方法。通过 `Object.defineProperty` 等方法可以读取和修改这些记录。
- **工作流程**：`Object.getOwnPropertyDescriptor(obj, prop)` 获取描述符；`Object.defineProperty(obj, prop, descriptor)` 定义或修改属性及标志。
- **关键技术点**：
  - `writable`：是否可以修改值。
  - `enumerable`：是否在枚举（如 `for...in`）中出现。
  - `configurable`：是否可以删除属性或修改属性标志（包括从数据属性改为访问器属性）。
  - 一旦将 `configurable` 设为 `false`，就无法再改回。

#### 使用方法
- **基本用法**：
  ```javascript
  const obj = {};
  Object.defineProperty(obj, 'readOnly', {
    value: 42,
    writable: false,
    enumerable: true,
    configurable: false
  });
  obj.readOnly = 100; // 无效
  console.log(obj.readOnly); // 42
  ```
- **常见场景**：
  - 定义常量属性
  - 隐藏内部实现细节（不可枚举）
  - 实现 getter/setter
  - 实现不可删除属性
- **最佳实践**：谨慎使用，避免过度元编程导致代码难以理解；对于公开 API，可以使用属性描述符保证稳定性。

#### 注意事项
- **常见错误**：试图修改不可配置属性会抛出错误（严格模式）或静默失败。
- **性能问题**：使用 `defineProperty` 比直接赋值稍慢，但通常可接受。
- **兼容性**：ES5，IE9+ 支持。

#### 代码示例
```javascript
const person = {};
Object.defineProperty(person, 'name', {
  value: 'Alice',
  writable: false,
  enumerable: false,
  configurable: false
});
console.log(Object.keys(person)); // [] (因为不可枚举)
person.name = 'Bob'; // 无效
delete person.name;  // 无效

// 获取描述符
const descriptor = Object.getOwnPropertyDescriptor(person, 'name');
console.log(descriptor);
// { value: 'Alice', writable: false, enumerable: false, configurable: false }
```

## 十七、Getter 和 Setter（访问器属性）

#### 概念
- **是什么**：访问器属性是通过 `get` 和 `set` 语法定义的属性，它们在读取和写入时执行自定义函数，而不是直接存储值。
- **为什么需要**：允许在属性访问时执行额外逻辑（如验证、计算、日志），同时保持与普通属性一样的使用方式。
- **解决什么问题**：实现计算属性、数据绑定、属性拦截等，增强对象行为的封装性。

#### 原理
- **底层实现**：访问器属性不包含数据值，而是包含一对 getter 和 setter 函数。当访问该属性时，调用 getter；当赋值时，调用 setter，传入赋的值。
- **工作流程**：读取属性 → 调用 getter 函数 → 返回值；写入属性 → 调用 setter 函数，传入新值。
- **关键技术点**：访问器属性可以只有 getter（只读）或只有 setter（只写）；它们同样具有 `enumerable` 和 `configurable` 标志。

#### 使用方法
- **基本用法**：
  ```javascript
  const user = {
    firstName: 'John',
    lastName: 'Doe',
    get fullName() {
      return `${this.firstName} ${this.lastName}`;
    },
    set fullName(value) {
      [this.firstName, this.lastName] = value.split(' ');
    }
  };
  console.log(user.fullName); // John Doe
  user.fullName = 'Jane Smith';
  console.log(user.firstName); // Jane
  ```
- **常见场景**：
  - 计算属性（如面积、全名）
  - 数据验证（在 setter 中校验）
  - 触发副作用（如更新 UI）
  - 封装私有变量（配合闭包）
- **最佳实践**：getter 不应修改对象状态；setter 应进行必要的验证和转换。

#### 注意事项
- **常见错误**：在 getter 中递归调用自身导致无限循环。
- **性能问题**：getter/setter 有函数调用开销，但通常可忽略。
- **兼容性**：ES5，IE9+ 支持。

#### 代码示例
```javascript
const temperature = {
  _celsius: 0,
  get celsius() {
    return this._celsius;
  },
  set celsius(value) {
    if (value < -273.15) {
      throw new Error('Temperature below absolute zero');
    }
    this._celsius = value;
  },
  get fahrenheit() {
    return this._celsius * 9/5 + 32;
  }
};
temperature.celsius = 25;
console.log(temperature.fahrenheit); // 77
```