## 一、构造函数

### 概念
- **是什么**：构造函数是用于创建和初始化对象的普通函数，通过 `new` 关键字调用。为了区分普通函数，约定首字母大写。
- **为什么需要**：在 JavaScript 中，需要一种统一的方式生成多个结构相似的对象。构造函数提供了一种模板，可以批量创建具有相同属性和方法的对象。
- **解决什么问题**：解决了手动重复创建对象的问题，实现了代码复用和对象创建的标准化。

### 原理
- **底层实现**：构造函数本质上是一个函数对象，当使用 `new` 调用时，会触发特殊的内部操作（`[[Construct]]`），将其转换为构造函数模式。
- **工作流程**：当 `new` 调用时，会执行以下步骤：
  1. 创建一个新的空对象。
  2. 将新对象的内部原型（`[[Prototype]]`）指向构造函数的 `prototype` 对象。
  3. 将构造函数内部的 `this` 绑定到新对象，并执行函数体（通常为对象添加属性）。
  4. 如果构造函数没有返回对象，则返回新对象。
- **关键技术点**：`new` 绑定、原型链接、返回值处理。

### 使用方法
- **基本用法**：定义构造函数，内部使用 `this` 添加属性，通过 `new` 创建实例。
  ```javascript
  function Person(name, age) {
      this.name = name;
      this.age = age;
  }
  const p = new Person('Alice', 25);
  ```
- **常见场景**：创建多个相似对象，如用户、商品、组件等。
- **最佳实践**：首字母大写以区分普通函数；避免在构造函数内定义方法（应放在原型上）；使用 `new` 调用，否则 `this` 会指向全局对象（严格模式下为 `undefined`），导致错误。

### 注意事项
- **常见错误**：忘记使用 `new` 调用，导致属性被添加到全局对象（非严格模式）或抛出错误（严格模式）。可使用 `new.target` 检测是否通过 `new` 调用。
- **性能问题**：在构造函数内定义方法会导致每个实例都创建新函数，浪费内存。应将方法定义在原型上。
- **兼容性**：构造函数模式在所有 JavaScript 环境中都支持。

### 代码示例
```javascript
// 正确使用
function Dog(name) {
    this.name = name;
}
const d = new Dog('Buddy');
console.log(d.name); // Buddy

// 错误使用（忘记 new）
const d2 = Dog('Max'); // 非严格模式下，this 指向 window，name 被挂载到全局
console.log(d2); // undefined
```

---

## 二、new 操作符

### 概念
- **是什么**：`new` 是一个运算符，用于调用构造函数并创建该构造函数的实例对象。
- **为什么需要**：提供了一种语言层面的机制，将普通函数调用转换为对象创建过程，自动处理原型链接和 `this` 绑定。
- **解决什么问题**：简化了基于原型的对象创建过程，隐藏了手动设置原型和返回对象的繁琐步骤。

### 原理
- **底层实现**：执行以下四个步骤：
  1. 创建一个空的简单 JavaScript 对象（即 `{}`）。
  2. 将空对象的原型（`__proto__`）链接到构造函数的 `prototype` 对象。
  3. 将构造函数内部的 `this` 绑定到新对象，并执行构造函数。
  4. 如果构造函数返回一个对象，则返回该对象；否则返回新创建的对象。
- **工作流程**：见上述步骤。
- **关键技术点**：`Object.create()` 模拟原型链接；构造函数返回值判断。

### 使用方法
- **基本用法**：`new 构造函数(参数)`
- **常见场景**：创建任何自定义对象或内置对象（如 `new Array()`、`new Date()`）。
- **最佳实践**：始终使用 `new` 调用构造函数；可手写 `new` 以加深理解，但在实际开发中直接使用语言提供的 `new`。

### 注意事项
- **常见错误**：忘记 `new` 导致属性意外挂载；构造函数返回非对象（如原始值）会被忽略，仍然返回新对象。
- **性能问题**：`new` 本身开销很小，但构造函数内的复杂操作会影响性能。
- **兼容性**：全支持。

### 代码示例
```javascript
// 手写 new 函数（模拟过程）
function myNew(Constructor, ...args) {
    // 1. 创建空对象，原型指向 Constructor.prototype
    const obj = Object.create(Constructor.prototype);
    // 2. 执行构造函数，绑定 this
    const result = Constructor.apply(obj, args);
    // 3. 判断返回值
    return (result !== null && typeof result === 'object') || typeof result === 'function' ? result : obj;
}

function Person(name) {
    this.name = name;
}
const p = myNew(Person, 'Alice');
console.log(p.name); // Alice
```

---

## 三、实例属性与方法

### 概念
- **是什么**：在构造函数内部通过 `this` 添加的属性和方法，属于每个实例自身。
- **为什么需要**：每个实例需要拥有独立的数据，例如用户名、ID 等。
- **解决什么问题**：确保实例之间的数据不会相互干扰，满足对象的个性化需求。

### 原理
- **底层实现**：当构造函数执行时，`this` 指向新对象，通过赋值操作将属性直接添加到该对象上。
- **工作流程**：`new` 创建对象后，构造函数内的 `this.xxx = yyy` 将属性写入对象自身。
- **关键技术点**：属性描述符（可配置、可枚举、可写）默认均为 `true`。

### 使用方法
- **基本用法**：在构造函数内使用 `this.property = value`。
- **常见场景**：存储每个实例独有的状态，如姓名、年龄、坐标等。
- **最佳实践**：仅将需要独立的数据定义为实例属性；方法应定义在原型上以节省内存。

### 注意事项
- **常见错误**：在构造函数内定义方法，导致每个实例都创建新函数，浪费内存。
- **性能问题**：大量实例属性可能增加内存占用，但通常可接受。
- **兼容性**：全支持。

### 代码示例
```javascript
function Car(brand) {
    this.brand = brand;       // 实例属性
    this.drive = function() {  // 实例方法（不推荐）
        console.log('Driving');
    };
}
const car1 = new Car('Toyota');
const car2 = new Car('Honda');
console.log(car1.brand); // Toyota
console.log(car1.drive === car2.drive); // false，方法不共享
```

---

## 四、静态属性与方法

### 概念
- **是什么**：直接挂载到构造函数本身的属性和方法，不属于任何实例。
- **为什么需要**：用于存放与类型相关而非与实例相关的功能，例如工具函数、类型常量等。
- **解决什么问题**：提供了类级别的成员，方便组织和调用，类似于其他语言中的静态成员。

### 原理
- **底层实现**：构造函数本身是一个对象，可以直接添加属性。
- **工作流程**：通过 `Constructor.prop = value` 定义，实例无法访问（除非通过构造函数）。
- **关键技术点**：静态方法通常用于实现与实例无关的逻辑。

### 使用方法
- **基本用法**：`构造函数.属性名 = 值`。
- **常见场景**：工具方法（如 `Array.isArray`）、常量（如 `Math.PI`）、工厂方法。
- **最佳实践**：静态方法内部不应依赖 `this` 指向实例，应使用构造函数名或 `this`（此时 `this` 指向构造函数本身）。

### 注意事项
- **常见错误**：在静态方法内使用 `this` 访问实例属性，会导致错误。
- **性能问题**：无。
- **兼容性**：全支持。

### 代码示例
```javascript
function Person(name) {
    this.name = name;
}
Person.species = 'Homo sapiens';        // 静态属性
Person.compare = function(p1, p2) {     // 静态方法
    return p1.age - p2.age;
};

console.log(Person.species); // Homo sapiens
const p = new Person('Alice');
console.log(p.species); // undefined（实例无法访问静态属性）
```

---

## 五、prototype 属性

### 概念
- **是什么**：每个函数（除箭头函数外）都有一个 `prototype` 属性，它是一个对象，指向该函数的原型对象。
- **为什么需要**：作为实现继承的基础，让所有实例共享属性和方法。
- **解决什么问题**：解决构造函数内定义方法导致的内存浪费问题，实现方法的复用。

### 原理
- **底层实现**：函数在创建时，JS 引擎会自动为其添加 `prototype` 属性，并初始化一个对象，该对象包含 `constructor` 属性指向函数自身。
- **工作流程**：当使用 `new` 创建实例时，实例的 `__proto__` 被设置为构造函数的 `prototype`，从而可以访问原型上的成员。
- **关键技术点**：`prototype` 只是一个普通对象，可以被修改或替换。

### 使用方法
- **基本用法**：通过 `构造函数.prototype.方法名 = function() {...}` 添加共享方法。
- **常见场景**：定义所有实例共用的方法，如 `Person.prototype.sayName`。
- **最佳实践**：所有实例方法都应定义在原型上；静态属性直接挂载构造函数。

### 注意事项
- **常见错误**：重写整个 `prototype` 对象时丢失 `constructor` 属性，需手动修复。
- **性能问题**：原型链查找比直接访问实例属性稍慢，但通常可忽略。
- **兼容性**：全支持。

### 代码示例
```javascript
function Person(name) {
    this.name = name;
}
Person.prototype.sayName = function() {
    console.log(this.name);
};
const p1 = new Person('Alice');
const p2 = new Person('Bob');
console.log(p1.sayName === p2.sayName); // true
```

---

## 六、原型对象

### 概念
- **是什么**：原型对象是指构造函数的 `prototype` 属性指向的对象，它包含了可以被所有实例共享的属性和方法。
- **为什么需要**：作为实例之间的共享区域，实现基于原型的继承。
- **解决什么问题**：提供了一种轻量级的继承机制，不需要类即可实现属性和方法的复用。

### 原理
- **底层实现**：原型对象本身也是一个普通对象，它的 `__proto__` 指向上一级原型（通常是 `Object.prototype`），形成原型链。
- **工作流程**：当访问实例属性时，如果实例自身没有，就会去其原型对象中查找。
- **关键技术点**：原型对象中的方法内部的 `this` 指向调用方法的实例（动态绑定）。

### 使用方法
- **基本用法**：通过 `构造函数.prototype` 访问和修改原型对象。
- **常见场景**：添加共享方法、实现继承（将子类原型设置为父类实例）。
- **最佳实践**：尽量在原型上定义方法，而不是属性（属性若为引用类型，会被所有实例共享，可能导致意外修改）。

### 注意事项
- **常见错误**：在原型上定义引用类型属性（如数组），一个实例修改会影响所有实例。
- **性能问题**：无。
- **兼容性**：全支持。

### 代码示例
```javascript
function Person() {}
Person.prototype.name = 'Default'; // 不推荐，属性共享
Person.prototype.colors = ['red']; // 危险，引用类型共享

const p1 = new Person();
const p2 = new Person();
p1.colors.push('blue');
console.log(p2.colors); // ['red', 'blue']，被意外修改

// 正确做法：属性定义在实例上，方法定义在原型上
```

---

## 七、__proto__ 属性

### 概念
- **是什么**：每个对象（除了 `null`）都有一个内部属性 `[[Prototype]]`，在大多数浏览器中可通过 `__proto__` 访问，它指向创建该对象的构造函数的 `prototype`。
- **为什么需要**：提供了一条从实例到原型的连接，是原型链查找的基础。
- **解决什么问题**：实现了对象的继承链，让对象可以访问其原型上的成员。

### 原理
- **底层实现**：当创建对象时（如通过 `{}`、`new`、`Object.create`），引擎会设置其 `[[Prototype]]` 为某个对象。
- **工作流程**：属性访问时，引擎沿着 `__proto__` 链向上查找。
- **关键技术点**：`__proto__` 是非标准属性，推荐使用 `Object.getPrototypeOf()` 获取原型。

### 使用方法
- **基本用法**：`obj.__proto__` 读取或设置原型（但不建议直接设置，影响性能）。
- **常见场景**：调试时查看原型链；模拟原型操作。
- **最佳实践**：避免直接操作 `__proto__`，使用 `Object.getPrototypeOf` 和 `Object.setPrototypeOf`（但后者也尽量避免）。

### 注意事项
- **常见错误**：误以为 `__proto__` 是标准属性，实际为浏览器实现，但现代环境普遍支持。
- **性能问题**：修改 `__proto__` 会导致引擎优化失效，性能开销大，应避免。
- **兼容性**：除 IE 10 以下，现代浏览器均支持。

### 代码示例
```javascript
function Person() {}
const p = new Person();
console.log(p.__proto__ === Person.prototype); // true
console.log(Object.getPrototypeOf(p) === Person.prototype); // true
```

---

## 八、constructor 属性

### 概念
- **是什么**：原型对象默认包含一个 `constructor` 属性，指向该原型所关联的构造函数。
- **为什么需要**：提供一种从实例或原型回溯到构造函数的方式，便于类型检测和实例创建。
- **解决什么问题**：让对象知道自己的构造函数是谁，在需要动态创建同类对象时有用。

### 原理
- **底层实现**：当函数创建时，引擎自动为其 `prototype` 对象添加 `constructor` 属性，值为函数本身。
- **工作流程**：实例可以通过原型链访问到 `constructor`，从而获取构造函数。
- **关键技术点**：`constructor` 属性是可写的，重写原型时需要手动维护。

### 使用方法
- **基本用法**：`实例.constructor` 获取构造函数，可用于创建新实例或类型判断。
- **常见场景**：通过 `obj.constructor` 创建同类型新对象（但不如使用构造函数直接）；判断对象类型（不可靠，因为可修改）。
- **最佳实践**：重写原型后，手动将 `constructor` 指回正确的构造函数，并保持其不可枚举性。

### 注意事项
- **常见错误**：重写原型后忘记修复 `constructor`，导致 `instance.constructor` 指向错误。
- **性能问题**：无。
- **兼容性**：全支持。

### 代码示例
```javascript
function Person(name) {
    this.name = name;
}
const p = new Person('Alice');
console.log(p.constructor === Person); // true

// 重写原型
Person.prototype = {
    sayName() {}
};
const p2 = new Person('Bob');
console.log(p2.constructor === Person); // false，指向 Object
// 修复
Object.defineProperty(Person.prototype, 'constructor', {
    value: Person,
    enumerable: false,
    writable: true
});
```

---

## 九、原型链

### 概念
- **是什么**：每个对象都有 `__proto__` 属性指向其原型，而原型本身也是对象，也有自己的 `__proto__`，这样层层向上直到 `null`，形成的链条称为原型链。
- **为什么需要**：实现继承的机制，让对象可以访问到原型链上所有属性和方法。
- **解决什么问题**：实现了属性和方法的共享，以及原型链上的动态查找。

### 原理
- **底层实现**：引擎内部通过 `[[Prototype]]` 链实现属性查找。
- **工作流程**：当访问对象属性时，首先在自身查找，若未找到则沿 `__proto__` 链向上查找，直到找到或到达 `null` 返回 `undefined`。
- **关键技术点**：原型链的终点是 `Object.prototype.__proto__ === null`；所有对象最终继承自 `Object.prototype`。

### 使用方法
- **基本用法**：通过原型链实现继承，如 `Child.prototype = new Parent()`。
- **常见场景**：构建继承关系、扩展内置对象（不推荐）。
- **最佳实践**：使用 `Object.create()` 或 ES6 `class` 进行继承，避免直接操作 `__proto__`。

### 注意事项
- **常见错误**：过长的原型链会影响属性查找性能；原型上的引用类型属性被所有实例共享。
- **性能问题**：每深入一层原型链，查找开销略有增加，但现代引擎已优化，通常可忽略。
- **兼容性**：全支持。

### 代码示例
```javascript
function Parent() {
    this.name = 'parent';
}
Parent.prototype.say = function() {
    console.log('Hello');
};
function Child() {}
Child.prototype = new Parent(); // 原型链继承
Child.prototype.constructor = Child;

const c = new Child();
console.log(c.name); // parent
c.say(); // Hello
console.log(c.toString()); // [object Object]，来自 Object.prototype
```

---

## 十、hasOwnProperty() 方法

### 概念
- **是什么**：`hasOwnProperty` 是 `Object.prototype` 上的一个方法，用于检查一个属性是否存在于对象自身（而非原型链上）。
- **为什么需要**：在遍历对象属性时，需要区分自有属性和原型链上的属性。
- **解决什么问题**：解决了属性归属的判断问题，避免误操作原型链上的属性。

### 原理
- **底层实现**：该方法会检查对象自身的属性描述符，返回布尔值。
- **工作流程**：调用 `obj.hasOwnProperty(prop)`，只检查对象自身是否包含属性 `prop`，不沿原型链查找。
- **关键技术点**：即使属性值为 `undefined` 或 `null`，只要属性存在于对象自身，就返回 `true`。

### 使用方法
- **基本用法**：`obj.hasOwnProperty('属性名')`。
- **常见场景**：与 `for...in` 配合，过滤掉原型链上的属性；判断属性是否为自有。
- **最佳实践**：安全调用方式：`Object.prototype.hasOwnProperty.call(obj, prop)`，防止对象覆盖了 `hasOwnProperty` 方法。

### 注意事项
- **常见错误**：对象可能自定义了 `hasOwnProperty` 属性，导致调用失败，应使用 `Object.prototype.hasOwnProperty.call`。
- **性能问题**：无。
- **兼容性**：全支持，但 IE 8 及以下对 DOM 对象的支持有问题（但可忽略）。

### 代码示例
```javascript
function Person(name) {
    this.name = name;
}
Person.prototype.age = 30;
const p = new Person('Alice');

console.log(p.hasOwnProperty('name')); // true
console.log(p.hasOwnProperty('age'));  // false

// for...in 配合使用
for (let key in p) {
    if (p.hasOwnProperty(key)) {
        console.log(key); // 只输出 'name'
    }
}
```

---

## 十一、in 操作符

### 概念
- **是什么**：`in` 是一个运算符，用于检查一个属性是否存在于对象中（无论自有还是原型链上）。
- **为什么需要**：判断属性是否可访问，而不关心它来自何处。
- **解决什么问题**：提供了一种简单的存在性检查，常用于条件判断。

### 原理
- **底层实现**：引擎会沿着原型链查找属性，如果找到则返回 `true`，否则 `false`。
- **工作流程**：`prop in obj`，在 `obj` 及其原型链上搜索属性名 `prop`。
- **关键技术点**：即使属性值为 `undefined`，只要属性存在，就返回 `true`。

### 使用方法
- **基本用法**：`'属性名' in 对象`。
- **常见场景**：检查对象是否具有某个方法或属性（如 `'toString' in obj`）；在条件语句中使用。
- **最佳实践**：与 `hasOwnProperty` 区分使用，根据需要决定是否包含原型链。

### 注意事项
- **常见错误**：混淆 `in` 和 `hasOwnProperty`，导致判断失误。
- **性能问题**：需要遍历原型链，但开销很小。
- **兼容性**：全支持。

### 代码示例
```javascript
function Person(name) {
    this.name = name;
}
Person.prototype.age = 30;
const p = new Person('Alice');

console.log('name' in p);      // true
console.log('age' in p);       // true
console.log('toString' in p); // true（来自 Object.prototype）
console.log('xyz' in p);      // false
```

---

## 十二、原型链的动态性

### 概念
- **是什么**：原型对象上的修改会立即影响到所有现有实例（即使实例在修改之前创建），因为实例的 `__proto__` 只是对原型对象的引用。
- **为什么需要**：体现了原型的动态特性，可以在运行时给所有实例添加新方法或属性。
- **解决什么问题**：允许在程序运行过程中扩展对象的功能，无需修改每个实例。

### 原理
- **底层实现**：实例的 `__proto__` 指向原型对象，两者是引用关系，不是复制。因此对原型对象的修改，通过引用立即可见。
- **工作流程**：修改原型对象上的属性后，下次访问实例的该属性时，会从新原型上获取。
- **关键技术点**：如果重写整个原型对象（赋值新对象），之前创建的实例仍然引用旧原型，不受影响。

### 使用方法
- **基本用法**：直接在原型上添加或修改属性。
- **常见场景**：为内置对象添加 polyfill；动态扩展功能。
- **最佳实践**：利用动态性进行灵活编程，但避免在原型上添加过多属性，以免污染。

### 注意事项
- **常见错误**：重写原型导致旧实例无法获得新功能，造成预期之外的行为。
- **性能问题**：原型修改本身开销小，但过度动态化可能影响代码可维护性。
- **兼容性**：全支持。

### 代码示例
```javascript
function Person() {}
const p = new Person();
Person.prototype.sayHi = function() { console.log('Hi'); };
p.sayHi(); // 'Hi'，动态生效

// 重写原型
Person.prototype = { sayHello() {} };
const p2 = new Person(); // p2 使用新原型
p.sayHello(); // 错误，p 仍然指向旧原型
```

---

## 十三、原型链的性能考虑

### 概念
- **是什么**：原型链上的属性查找比访问自有属性稍慢，因为需要遍历链；同时，过长的原型链会加重查找开销。
- **为什么需要**：了解性能影响，以编写高效的代码。
- **解决什么问题**：指导我们在设计继承结构时，避免过深的原型链和不必要的原型属性访问。

### 原理
- **底层实现**：引擎在查找属性时，需要沿着 `__proto__` 链迭代，直到找到或到达终点。现代引擎有内联缓存等优化，但深链仍可能产生额外开销。
- **工作流程**：每次属性访问都会触发原型链查找。
- **关键技术点**：常用属性应尽量放在实例自身或浅层原型上。

### 使用方法
- **基本用法**：在设计对象时，将频繁访问的属性定义为自有属性；保持原型链短小。
- **常见场景**：高性能场景（如游戏、动画）中，避免通过原型链访问热点属性。
- **最佳实践**：使用 `Object.create(null)` 创建无原型的纯净对象，用于纯字典存储，避免原型链干扰。

### 注意事项
- **常见错误**：在循环中通过原型链访问属性，导致不必要的性能损耗。
- **性能问题**：原型链深度超过几层时，可能影响性能，但一般应用无需过度优化。
- **兼容性**：不涉及。

### 代码示例
```javascript
// 性能对比（理论示例）
function A() { this.a = 1; }
A.prototype.b = 2;
const obj = new A();

// 访问自有属性 a 较快
console.log(obj.a);
// 访问原型属性 b 稍慢（需要查找一次原型）
console.log(obj.b);
```

---

## 十四、继承模式

### 概念
- **是什么**：基于原型链实现对象之间继承的多种设计模式，包括原型链继承、构造函数继承、组合继承、寄生组合继承等。
- **为什么需要**：实现代码复用，建立对象间的层次关系。
- **解决什么问题**：在没有类的语言中，提供多种方式模拟传统面向对象继承。

### 原理
- **底层实现**：通过设置原型（`__proto__`）让子类对象能访问父类原型上的方法；通过借用构造函数（`Parent.call(this)`）继承实例属性。
- **工作流程**：不同模式采用不同组合，最终目标是既继承实例属性又继承原型方法。
- **关键技术点**：组合继承是常用模式，寄生组合继承是最优模式。

### 使用方法
- **基本用法**：见各模式代码示例。
- **常见场景**：构建复杂的对象层次，如 UI 组件库、数据模型等。
- **最佳实践**：优先使用寄生组合继承（或 ES6 `class`），避免两次调用父类构造函数。

### 注意事项
- **常见错误**：单独使用原型链继承导致引用类型属性共享；单独使用构造函数继承导致方法无法复用。
- **性能问题**：组合继承两次调用父类构造函数，浪费内存；寄生组合继承无此问题。
- **兼容性**：全支持，但 ES6 `class` 需要 ES6 环境或转译。

### 代码示例
```javascript
// 寄生组合继承（最优）
function Parent(name) {
    this.name = name;
    this.colors = ['red'];
}
Parent.prototype.sayName = function() {
    console.log(this.name);
};

function Child(name, age) {
    Parent.call(this, name); // 继承实例属性
    this.age = age;
}

// 继承原型方法
Child.prototype = Object.create(Parent.prototype);
Child.prototype.constructor = Child;
Child.prototype.sayAge = function() {
    console.log(this.age);
};

const c = new Child('Alice', 10);
c.colors.push('blue');
console.log(c.colors); // ['red', 'blue']
c.sayName(); // Alice
c.sayAge();  // 10
```

---

## 十五、ES6 class 与原型

### 概念
- **是什么**：ES6 引入的 `class` 语法，是原型继承的语法糖，提供更清晰、面向对象的写法。
- **为什么需要**：简化原型继承的写法，让代码更易读和维护，同时支持 `extends`、`super` 等关键字。
- **解决什么问题**：降低了 JavaScript 中实现继承的门槛，同时保持了底层的原型机制。

### 原理
- **底层实现**：`class` 本质是函数，`constructor` 对应构造函数，类内定义的方法被放在 `prototype` 上，静态方法放在类本身上。`extends` 实现原型链链接（`Child.__proto__ === Parent` 和 `Child.prototype.__proto__ === Parent.prototype`）。
- **工作流程**：`new` 调用类时，执行 `constructor`，自动进行原型链接。
- **关键技术点**：`super` 关键字用于调用父类构造函数或方法；类定义内部默认严格模式。

### 使用方法
- **基本用法**：`class 子类 extends 父类 { constructor(...) { super(...); } }`。
- **常见场景**：现代 JavaScript 开发中，替代传统构造函数和原型操作。
- **最佳实践**：使用 `class` 和 `extends` 进行继承，代码更简洁；注意 `super` 必须在访问 `this` 之前调用。

### 注意事项
- **常见错误**：忘记调用 `super()` 导致 `this` 未初始化；在静态方法中错误使用 `this`。
- **性能问题**：与手写原型链相当，无额外开销。
- **兼容性**：ES6 环境，旧环境需转译（如 Babel）。

### 代码示例
```javascript
class Parent {
    constructor(name) {
        this.name = name;
    }
    sayName() {
        console.log(this.name);
    }
    static hello() {
        console.log('Hello');
    }
}

class Child extends Parent {
    constructor(name, age) {
        super(name); // 调用父类构造函数
        this.age = age;
    }
    sayAge() {
        console.log(this.age);
    }
}

const c = new Child('Alice', 10);
c.sayName(); // Alice
c.sayAge();  // 10
Child.hello(); // Hello（静态方法继承）
```

---

## 十六、原型操作 API

### 概念
- **是什么**：JavaScript 提供了一系列操作原型的方法，如 `Object.create`、`Object.getPrototypeOf`、`Object.setPrototypeOf` 等。
- **为什么需要**：提供标准、安全的方式来操作对象的原型，避免直接操作非标准的 `__proto__`。
- **解决什么问题**：规范了原型的读写操作，便于开发者进行原型链相关编程。

### 原理
- **底层实现**：这些方法是语言内置的，封装了内部 `[[Prototype]]` 的读写。
- **工作流程**：`Object.create(proto)` 创建新对象，其原型为 `proto`；`Object.getPrototypeOf(obj)` 返回对象的原型；`Object.setPrototypeOf(obj, proto)` 设置原型（性能差）。
- **关键技术点**：`Object.create` 可同时定义属性描述符；`Object.setPrototypeOf` 可能影响性能。

### 使用方法
- **基本用法**：
  - `Object.create(proto, propertiesObject)`
  - `Object.getPrototypeOf(obj)`
  - `Object.setPrototypeOf(obj, proto)`
- **常见场景**：创建以指定对象为原型的对象；获取原型用于调试；动态改变原型（很少用）。
- **最佳实践**：尽量使用 `Object.create` 代替 `Object.setPrototypeOf`；避免在生产代码中动态修改原型。

### 注意事项
- **常见错误**：混淆 `Object.create` 和 `new`；认为 `Object.create` 会执行构造函数。
- **性能问题**：`Object.setPrototypeOf` 会破坏引擎优化，性能差，应避免。
- **兼容性**：ES5+ 支持。

### 代码示例
```javascript
const parent = { name: 'parent' };
const child = Object.create(parent, {
    age: { value: 10, enumerable: true }
});
console.log(child.name); // parent（来自原型）
console.log(Object.getPrototypeOf(child) === parent); // true

// 动态修改原型（不推荐）
Object.setPrototypeOf(child, { newProto: true });
```

---

## 十七、instanceof 与 isPrototypeOf

### 概念
- **是什么**：
  - `instanceof`：运算符，检查一个对象是否是一个构造函数的实例，即检查构造函数的 `prototype` 是否出现在对象的原型链上。
  - `isPrototypeOf`：方法，检查一个对象是否存在于另一个对象的原型链上。
- **为什么需要**：用于类型检测和原型链关系判断。
- **解决什么问题**：提供运行时检查对象与构造函数/原型之间关系的能力。

### 原理
- **底层实现**：
  - `instanceof`：沿着对象的 `__proto__` 链查找，看是否有等于 `构造函数.prototype` 的对象。
  - `isPrototypeOf`：沿着参数对象的 `__proto__` 链查找，看是否有等于调用者本身。
- **工作流程**：两者都遍历原型链。
- **关键技术点**：`instanceof` 要求右边是构造函数（有 `prototype` 属性）；`isPrototypeOf` 可作用于任何对象。

### 使用方法
- **基本用法**：
  - `obj instanceof Constructor`
  - `prototypeObj.isPrototypeOf(obj)`
- **常见场景**：判断对象类型；检查继承关系。
- **最佳实践**：对于基本类型（如字符串、数字），使用 `typeof`；对于对象，使用 `instanceof` 但要小心跨窗口问题（不同全局环境）。`isPrototypeOf` 更安全。

### 注意事项
- **常见错误**：跨窗口（iframe）时，`instanceof` 可能失效，因为构造函数不同；修改原型后，`instanceof` 结果可能改变。
- **性能问题**：需要遍历原型链，但通常可接受。
- **兼容性**：全支持。

### 代码示例
```javascript
function Person() {}
const p = new Person();
console.log(p instanceof Person); // true
console.log(Person.prototype.isPrototypeOf(p)); // true

// 跨窗口问题
const iframe = document.createElement('iframe');
document.body.appendChild(iframe);
const iframeArray = new iframe.contentWindow.Array();
console.log(iframeArray instanceof Array); // false（不同全局）
console.log(Array.isArray(iframeArray)); // true（使用专用方法）
```

---

以上是 JavaScript 原型与原型链的完整学习笔记，涵盖了从基础概念到高级应用的各个知识点，并按照统一的模板格式进行了组织。每个知识点都包含了概念、原理、使用方法、注意事项和代码示例，旨在帮助读者深入理解并正确运用原型机制。