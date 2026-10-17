## 一、数据类型分类（原始类型 vs 引用类型）
##### 概念
- **是什么**：JavaScript中的数据类型分为两大类：原始类型（Primitive Types）和引用类型（Reference Types）。原始类型包括：number、string、boolean、undefined、null、symbol、bigint；引用类型主要是object（包含数组、函数、日期等）。
- **为什么需要**：这种分类反映了数据在内存中的存储方式和访问机制不同，直接影响变量的赋值、比较和传递行为。
- **解决什么问题**：理解分类有助于避免赋值和比较时的意外错误，正确设计数据结构和函数参数传递。

##### 原理
- **底层实现**：原始类型值直接存储在栈内存中，占据固定大小空间；引用类型的值存储在堆内存中，栈内存中存储的是指向堆内存的引用地址（指针）。
- **工作流程**：
  - 原始类型赋值：创建一个新值的副本，两个变量独立。
  - 引用类型赋值：复制引用地址，两个变量指向同一个对象，修改一个会影响另一个。
- **关键技术点**：动态属性（引用类型可添加属性）、按值传递与按引用传递。

##### 使用方法
- **基本用法**：声明变量时自动根据值确定类型，可通过赋值改变类型（动态类型）。
- **常见场景**：
  - 原始类型用于基本数据（数字、字符串、布尔等）。
  - 引用类型用于复杂数据结构和需要共享数据的场景。
- **最佳实践**：明确变量预期类型；避免不必要的对象引用共享，必要时使用深拷贝。

##### 注意事项
- **常见错误**：误以为引用类型赋值会创建新对象，导致意外修改原对象。
- **性能问题**：大量原始类型操作较快，频繁操作大对象注意引用导致的内存泄漏。
- **兼容性**：所有环境均支持基本分类，bigint和symbol需注意ES6+环境。

##### 代码示例
```javascript
// 原始类型赋值
let a = 10;
let b = a;
a = 20;
console.log(b); // 10

// 引用类型赋值
let obj1 = { name: 'Alice' };
let obj2 = obj1;
obj1.name = 'Bob';
console.log(obj2.name); // 'Bob'

// 深拷贝示例（避免共享引用）
let obj3 = JSON.parse(JSON.stringify(obj1));
obj1.name = 'Charlie';
console.log(obj3.name); // 'Bob'
```

## 二、原始类型详解
##### 概念
- **是什么**：原始类型是JavaScript中最基本的数据类型，表示不可变（immutable）的简单值。包括：number、string、boolean、undefined、null、symbol、bigint。
- **为什么需要**：它们是语言构建块，用于表示最简单的数据，保证数据不可变性有助于程序可预测性。
- **解决什么问题**：提供了内存效率高、无副作用的数值、文本、逻辑表示。

##### 原理
- **底层实现**：原始值直接存储在栈内存，变量访问的是实际值。不可变性意味着任何操作（如字符串方法）都会返回新值，原值不变。
- **工作流程**：
  - number：双精度64位浮点数，遵循IEEE 754，存在精度问题。
  - string：由UTF-16码元组成的序列，不可变。
  - boolean：仅true/false。
  - undefined：表示未定义，变量未赋值时默认值。
  - null：表示空对象指针，需主动赋值。
  - symbol：唯一且不可变，用于对象属性键。
  - bigint：任意精度整数，通过n后缀或BigInt()创建。
- **关键技术点**：包装对象机制允许临时将原始值当作对象调用方法。

##### 使用方法
- **基本用法**：直接字面量创建，或通过构造函数（但不推荐）。
- **常见场景**：
  - number：数学运算、计数。
  - string：文本处理。
  - boolean：条件判断。
  - undefined：检测变量是否初始化。
  - null：释放对象引用。
  - symbol：创建唯一属性键，防止冲突。
  - bigint：大整数计算（如加密、时间戳）。
- **最佳实践**：优先使用字面量；处理浮点数时注意精度；使用Symbol.for()创建全局共享符号。

##### 注意事项
- **常见错误**：
  - typeof null === 'object' 的bug。
  - NaN不等于自身。
  - 字符串方法不会改变原字符串。
  - bigint不能与number混用算术运算。
- **性能问题**：原始类型操作通常很快，大量字符串拼接可使用数组或模板字符串优化。
- **兼容性**：symbol和bigint在旧环境可能不支持，需转译或降级。

##### 代码示例
```javascript
// 原始类型示例
let num = 42;
let str = 'hello';
let bool = true;
let undef;
let n = null;
let sym = Symbol('id');
let big = 123n;

// 不可变性
let s = 'abc';
s[0] = 'x'; // 无效，但不会报错（严格模式下报错）
console.log(s); // 'abc'

// 包装对象
console.log('abc'.toUpperCase()); // 'ABC'

// typeof null bug
console.log(typeof null); // 'object'

// NaN
console.log(NaN === NaN); // false
console.log(isNaN(NaN)); // true

// bigint运算
console.log(10n + 20n); // 30n
// console.log(10n + 10); // TypeError
```

## 三、引用类型详解
##### 概念
- **是什么**：引用类型指对象（Object），包括普通对象、数组、函数、日期、正则等。它们存储的是堆内存中的引用。
- **为什么需要**：用来表示复杂数据结构和行为，支持属性动态添加，便于数据共享和方法组织。
- **解决什么问题**：提供了面向对象编程的基础，能够封装数据和逻辑。

##### 原理
- **底层实现**：对象在堆内存中分配，栈内存存储其引用地址。对象属性键只能是字符串或Symbol，值可以是任何类型。
- **工作流程**：访问对象时，先通过引用找到堆内存中的对象，再操作属性。函数也是对象，可调用。
- **关键技术点**：原型链（继承机制）、属性描述符（可配置、可枚举、可写）、动态属性。

##### 使用方法
- **基本用法**：对象字面量`{}`，数组字面量`[]`，函数声明等。
- **常见场景**：
  - 普通对象：存储键值对。
  - 数组：有序集合。
  - 函数：封装可复用代码。
  - 日期：处理时间。
  - 正则：模式匹配。
- **最佳实践**：使用对象解构、扩展运算符简化操作；注意避免循环引用导致内存泄漏；合理使用原型继承。

##### 注意事项
- **常见错误**：误判对象类型（如数组用typeof返回object）；直接修改函数参数对象可能影响外部。
- **性能问题**：频繁访问深层嵌套属性可能较慢；大量对象创建需考虑内存。
- **兼容性**：所有环境均支持基本对象，但ES6+特性（如Map、Set）需注意兼容。

##### 代码示例
```javascript
// 对象
let person = {
  name: 'Tom',
  age: 20,
  greet() {
    console.log(`Hello, I'm ${this.name}`);
  }
};
person.job = 'Engineer'; // 动态添加属性

// 数组
let arr = [1, 2, 3];
arr.push(4);
console.log(arr instanceof Array); // true
console.log(arr instanceof Object); // true

// 函数
function sum(a, b) {
  return a + b;
}
sum.description = 'adds two numbers'; // 函数也是对象，可添加属性

// 日期
let now = new Date();

// 引用传递
function changeName(obj) {
  obj.name = 'Jerry';
}
changeName(person);
console.log(person.name); // 'Jerry'
```

## 四、类型检测
##### 概念
- **是什么**：JavaScript提供了多种方式来检测值的类型，包括`typeof`、`instanceof`、`Object.prototype.toString`等。
- **为什么需要**：由于动态类型，运行时可能需要判断值的类型以执行不同逻辑。
- **解决什么问题**：确保操作安全、正确，避免类型错误。

##### 原理
- **底层实现**：
  - `typeof`：根据值的内部类型标签返回字符串，存在历史遗留问题（null返回"object"）。
  - `instanceof`：检查构造函数的prototype是否在对象的原型链上。
  - `Object.prototype.toString.call`：返回内部[[Class]]属性格式为"[object Type]"的字符串，可精确区分内置类型。
- **工作流程**：
  - `typeof`直接读取类型信息。
  - `instanceof`遍历原型链进行比较。
  - `toString`通过调用内部方法获取类型。
- **关键技术点**：跨全局对象问题（不同iframe的构造函数不同），`Symbol.hasInstance`可自定义`instanceof`行为。

##### 使用方法
- **基本用法**：
  - `typeof value`：检测原始类型和函数。
  - `value instanceof Constructor`：检测对象具体类型。
  - `Object.prototype.toString.call(value)`：通用精确检测。
- **常见场景**：
  - `typeof`：检测undefined、boolean、number、string、symbol、bigint、function。
  - `instanceof`：检测数组、日期、自定义类实例。
  - `toString`：检测所有内置类型，包括null、数组、日期等。
- **最佳实践**：
  - 优先使用`typeof`检测原始类型。
  - 检测数组使用`Array.isArray()`（跨框架可靠）。
  - 需要精确内置类型时使用`Object.prototype.toString.call`。
  - 避免使用`instanceof`检测可能跨窗口传递的对象。

##### 注意事项
- **常见错误**：
  - `typeof null === 'object'`。
  - `typeof [] === 'object'`，无法区分数组。
  - `instanceof`在跨全局对象时失效。
- **性能问题**：`instanceof`原型链查找可能较慢，但通常可忽略。
- **兼容性**：所有方法都广泛支持，`Symbol.hasInstance`为ES6特性。

##### 代码示例
```javascript
// typeof
console.log(typeof 42);           // 'number'
console.log(typeof 'hello');       // 'string'
console.log(typeof true);          // 'boolean'
console.log(typeof undefined);     // 'undefined'
console.log(typeof null);          // 'object' (bug)
console.log(typeof {});            // 'object'
console.log(typeof []);            // 'object'
console.log(typeof function(){});  // 'function'
console.log(typeof Symbol());      // 'symbol'
console.log(typeof 123n);          // 'bigint'

// instanceof
let arr = [1,2,3];
console.log(arr instanceof Array);   // true
console.log(arr instanceof Object);  // true
console.log(arr instanceof RegExp);  // false

// Object.prototype.toString.call
let toString = Object.prototype.toString;
console.log(toString.call(123));      // '[object Number]'
console.log(toString.call('abc'));    // '[object String]'
console.log(toString.call(true));     // '[object Boolean]'
console.log(toString.call(undefined));// '[object Undefined]'
console.log(toString.call(null));     // '[object Null]'
console.log(toString.call([]));       // '[object Array]'
console.log(toString.call({}));       // '[object Object]'
console.log(toString.call(new Date()));// '[object Date]'
console.log(toString.call(/a/));      // '[object RegExp]'
console.log(toString.call(function(){}));// '[object Function]'

// Array.isArray
console.log(Array.isArray([]));       // true
console.log(Array.isArray({}));       // false
```

## 五、显式类型转换
##### 概念
- **是什么**：通过手动调用函数将值从一种类型转换为另一种类型，如`String()`、`Number()`、`Boolean()`、`parseInt()`、`parseFloat()`。
- **为什么需要**：为了满足特定操作的需求，确保数据类型符合预期。
- **解决什么问题**：避免隐式转换可能带来的意外结果，增强代码可读性和可预测性。

##### 原理
- **底层实现**：
  - `String(value)`：根据`ToString`抽象操作规则转换。
  - `Number(value)`：根据`ToNumber`抽象操作规则转换。
  - `Boolean(value)`：根据`ToBoolean`抽象操作规则转换。
  - `parseInt(string, radix)`：解析字符串直到非数字字符，返回整数。
  - `parseFloat(string)`：解析浮点数。
- **工作流程**：
  - 对象转换时，会先调用`Symbol.toPrimitive`或`valueOf`/`toString`方法获取原始值，再按规则转换。
- **关键技术点**：转换规则表（假值列表、字符串转数字规则等）。

##### 使用方法
- **基本用法**：
  - `String(value)`：转为字符串。
  - `Number(value)`：转为数字（可能得到NaN）。
  - `Boolean(value)`：转为布尔值。
  - `parseInt(value, radix)`：解析整数。
  - `parseFloat(value)`：解析浮点数。
- **常见场景**：
  - 字符串拼接前确保类型为字符串。
  - 用户输入转为数字进行计算。
  - 条件判断时显式转为布尔。
- **最佳实践**：
  - 使用`Number()`而非`parseInt()`进行严格数字转换，除非需要解析前缀。
  - `parseInt`始终指定基数（radix）以避免意外八进制解析。
  - 布尔转换使用双重非`!!value`简洁，但`Boolean(value)`更清晰。

##### 注意事项
- **常见错误**：
  - `Number('123abc')` 返回 `NaN`，而 `parseInt('123abc')` 返回 `123`。
  - `parseInt('08')` 在旧环境可能被当作八进制（ES5后统一为十进制，但指定基数更安全）。
  - 空字符串 `''` 转换为数字为 `0`，转为布尔为 `false`。
  - 对象转换为数字或字符串可能调用`valueOf`/`toString`导致意料之外的副作用。
- **性能问题**：转换通常很快，但大量转换需注意。
- **兼容性**：所有环境均支持。

##### 代码示例
```javascript
// 转字符串
console.log(String(123));       // '123'
console.log(String(true));      // 'true'
console.log(String(null));      // 'null'
console.log(String(undefined)); // 'undefined'
console.log(String([1,2]));     // '1,2'
console.log(String({}));        // '[object Object]'

// 转数字
console.log(Number('123'));     // 123
console.log(Number('123.45'));  // 123.45
console.log(Number(''));        // 0
console.log(Number('123abc'));  // NaN
console.log(Number(true));      // 1
console.log(Number(false));     // 0
console.log(Number(null));      // 0
console.log(Number(undefined)); // NaN
console.log(Number({}));        // NaN

// 转布尔
console.log(Boolean(0));        // false
console.log(Boolean(''));       // false
console.log(Boolean(null));     // false
console.log(Boolean(undefined));// false
console.log(Boolean(NaN));      // false
console.log(Boolean(false));    // false
console.log(Boolean('hello'));  // true
console.log(Boolean([]));       // true
console.log(Boolean({}));       // true

// parseInt / parseFloat
console.log(parseInt('123abc'));   // 123
console.log(parseInt('12.34'));    // 12
console.log(parseInt('  123'));    // 123 (忽略前导空格)
console.log(parseInt('0xFF'));     // 255 (十六进制)
console.log(parseInt('101', 2));   // 5 (指定基数为2)
console.log(parseFloat('12.34abc'));// 12.34
```

## 六、隐式类型转换
##### 概念
- **是什么**：JavaScript在运行时根据需要自动进行的类型转换，常见于运算符、条件判断、相等比较等场景。
- **为什么需要**：弱类型语言设计使代码编写更灵活，减少显式转换的繁琐。
- **解决什么问题**：允许不同类型数据进行操作，但容易导致意外结果。

##### 原理
- **底层实现**：遵循ECMAScript规范中的抽象操作：`ToPrimitive`、`ToNumber`、`ToString`、`ToBoolean`。
- **工作流程**：
  - **算术运算符**（- * / %）：操作数转为数字。
  - **加号+**：如果任一操作数是字符串，执行字符串拼接；否则转为数字相加。
  - **关系运算符**（> < >= <=）：若两个都是字符串，按字典序比较；否则转为数字比较。
  - **相等==**：按复杂规则转换（如布尔转数字、对象转原始值、null/undefined特殊相等）。
  - **逻辑上下文**（if、while、&&、||）：转为布尔值。
- **关键技术点**：
  - 假值列表：`undefined`、`null`、`false`、`0`、`NaN`、`''`。
  - 对象转换原始值时，根据场景偏好调用`valueOf`或`toString`（Date偏字符串，其他偏数字）。

##### 使用方法
- **基本用法**：隐式转换自动发生，无需手动调用。
- **常见场景**：
  - 字符串拼接：`'The number is ' + 42` → `'The number is 42'`。
  - 数字运算：`'6' / '2'` → `3`。
  - 条件判断：`if (value) {...}` 自动转布尔。
  - 相等比较：`1 == '1'` → `true`。
- **最佳实践**：避免依赖隐式转换，尽量使用显式转换和严格相等`===`，以减少代码歧义和错误。

##### 注意事项
- **常见错误**：
  - `0 == ''` 为 `true`。
  - `false == '0'` 为 `true`。
  - `null == undefined` 为 `true`，但 `null === undefined` 为 `false`。
  - `[] == ![]` 结果为 `true`（因为`![]`为false，然后空数组转为0，false转为0）。
  - `NaN`不与任何值相等，包括自身。
- **性能问题**：隐式转换通常很快，但复杂对象转换可能涉及方法调用，需注意。
- **兼容性**：所有环境行为一致，但不同版本可能微调（如ES5后八进制字面量不再被parseInt隐式识别）。

##### 代码示例
```javascript
// 算术运算符
console.log('6' - 2);   // 4 (字符串'6'转为数字6)
console.log('6' * '2'); // 12
console.log('hello' - 1); // NaN

// 加号
console.log(1 + '2');     // '12' (数字1转为字符串)
console.log(1 + 2);       // 3
console.log(true + true); // 2 (true转为1)

// 关系运算符
console.log('10' > 5);    // true ('10'转为数字10)
console.log('10' > '5');  // false (字符串比较，'1' < '5')

// 相等 ==
console.log(1 == '1');     // true
console.log(0 == false);   // true
console.log(null == undefined); // true
console.log([] == false);  // true (空数组转为0，false转为0)
console.log([] == ![]);    // true (![]为false，[]转为0，0==0)

// 逻辑上下文
if ('hello') {
  console.log('truthy'); // 执行
}
if (0) {
  console.log('falsy');  // 不执行
}

// 对象转换
let obj = {
  valueOf() { return 42; },
  toString() { return '42'; }
};
console.log(obj + 1);    // 43 (valueOf返回原始值42)
console.log(String(obj));// '42' (toString返回'42')
```