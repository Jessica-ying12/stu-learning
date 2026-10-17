## 1. 函数定义与类型

### 概念
- **是什么**：函数是一段可重复使用的代码块，用于执行特定任务。在 Dart 中，函数也是对象，类型为 `Function`。
- **为什么需要**：函数提供了代码模块化和复用的机制，使程序结构清晰、易于维护。
- **解决什么问题**：避免重复代码，提高代码的可读性和可维护性，支持面向对象编程中的行为封装。

### 原理
- **底层实现**：Dart 中每个函数都是一个 `Function` 类的实例，函数定义在编译时被转换为函数对象，存储在堆中。
- **工作流程**：当函数被调用时，会创建执行上下文（包括参数、局部变量），执行函数体，然后返回结果（如果没有 `return`，默认返回 `null`）。
- **关键技术点**：函数类型（`Function`）是所有函数的父类型，可以通过 `void Function(int)` 等形式指定具体的函数签名。

### 使用方法
#### 基本用法
```dart
// 标准定义
bool isNoble(int atomicNumber) {
  return _nobleGases[atomicNumber] != null;
}

// 省略类型（不推荐公开 API）
isNoble(atomicNumber) {
  return _nobleGases[atomicNumber] != null;
}
```

#### 常见场景
- 定义工具函数、业务逻辑、回调函数等。
- 作为类的方法。

#### 最佳实践
- 公开 API 应显式标注类型，提高可读性和类型安全。
- 遵循 Effective Dart 规范。

### 注意事项
- **常见错误**：忘记返回类型导致隐式返回 `null`，可能引发预期外的行为。
- **性能问题**：函数调用有一定开销，但 Dart 编译器会优化内联等。
- **兼容性**：类型省略可能导致类型推断问题，在强类型检查模式下可能报错。

### 代码示例
```dart
// 函数定义示例
int add(int a, int b) {
  return a + b;
}

void main() {
  print(add(3, 5)); // 8
}
```

---

## 2. 返回值

### 概念
- **是什么**：函数执行后返回给调用者的结果。Dart 中所有函数都有返回值，未显式返回时默认返回 `null`。
- **为什么需要**：函数可以计算值并传递给调用者，实现数据流动。
- **解决什么问题**：支持函数式编程，使函数能够产生输出。

### 原理
- **底层实现**：函数执行完毕后，返回值被放置在指定的寄存器或栈位置，由调用者获取。
- **工作流程**：遇到 `return` 语句时，计算表达式值，退出函数并返回；若无 `return`，隐式插入 `return null;`。
- **关键技术点**：返回类型可以是任意类型，包括 `void`（表示不关心返回值，但实际仍返回 `null`）。

### 使用方法
#### 基本用法
```dart
int multiply(int x, int y) {
  return x * y; // 显式返回
}

void log(String message) {
  print(message); // 无 return，隐式返回 null
}
```

#### 常见场景
- 计算并返回结果。
- 使用 `void` 表示不返回有用值（如打印日志）。
- 返回多个值可使用记录（Record）。

#### 最佳实践
- 明确指定返回类型，避免隐式 `dynamic`。
- 若函数无返回值，使用 `void` 类型。

### 注意事项
- **常见错误**：误以为 `void` 函数不能有 `return`，实际上可以 `return;` 提前退出。
- **性能问题**：无额外性能影响。
- **兼容性**：Dart 2.0 起强制类型检查，返回类型不符会报错。

### 代码示例
```dart
// 返回多个值
(String, int) getUserInfo() {
  return ('Alice', 25);
}

void main() {
  var (name, age) = getUserInfo();
  print('$name is $age years old.');
  
  // 隐式返回 null
  assert(log('Hi') == null); // log 返回 void，但实际值是 null
}
```

---

## 3. 参数

### 概念
- **是什么**：函数定义时声明的输入占位符，调用时传入实际值。Dart 支持必需位置参数、命名参数和可选位置参数。
- **为什么需要**：使函数能够接收外部数据，增强通用性。
- **解决什么问题**：参数化行为，同一函数可处理不同数据。

### 原理
- **底层实现**：参数在函数调用时被压入栈或通过寄存器传递，Dart 虚拟机负责管理。
- **工作流程**：调用时实参与形参绑定，函数体内使用参数值。
- **关键技术点**：必需位置参数必须按顺序传递；命名参数通过参数名匹配；可选参数可省略。

### 使用方法
#### 基本用法
- **必需位置参数**：直接列出，调用时必须按顺序提供。
```dart
void greet(String name, int age) { ... }
greet('Bob', 30);
```

- **命名参数**：用 `{}` 包裹，调用时使用 `参数名: 值`。
```dart
void enableFlags({bool? bold, bool? hidden}) { ... }
enableFlags(bold: true, hidden: false);
```

- **可选位置参数**：用 `[]` 包裹，可省略。
```dart
String say(String from, String msg, [String? device]) { ... }
say('Bob', 'Howdy'); // 省略 device
```

#### 常见场景
- 命名参数常用于 Flutter 组件构造器，提高代码可读性。
- 可选参数用于提供扩展功能但非必需的情况。

#### 最佳实践
- 公开 API 优先使用命名参数，尤其参数较多时。
- 参数顺序：必需位置参数在前，可选参数在后。

### 注意事项
- **常见错误**：混用命名参数和可选位置参数（不能同时使用）。
- **性能问题**：无显著影响。
- **兼容性**：命名参数在 Dart 2.0 后稳定支持。

### 代码示例
```dart
void describePerson(String name, {int age = 0, String? city}) {
  print('Name: $name, Age: $age, City: $city');
}

void main() {
  describePerson('Alice', age: 25, city: 'New York');
  describePerson('Bob'); // 使用默认 age
}
```

---

## 4. 默认参数值

### 概念
- **是什么**：为可选参数（命名或位置）指定默认值，当调用者未提供时使用该值。
- **为什么需要**：减少重复代码，提供合理的默认行为。
- **解决什么问题**：使函数更易用，避免每次调用都要传入相同的值。

### 原理
- **底层实现**：默认值在编译时确定，作为常量存储。调用时若参数缺失，编译器自动插入默认值。
- **工作流程**：函数调用时，若参数未提供，则使用默认值初始化形参。
- **关键技术点**：默认值必须是编译时常量（如字面量、const 构造的结果）。

### 使用方法
#### 基本用法
```dart
// 命名参数默认值
void enableFlags({bool bold = false, bool hidden = false}) { ... }

// 可选位置参数默认值
String say(String from, String msg, [String device = 'carrier pigeon']) { ... }
```

#### 常见场景
- 为配置选项提供默认值。
- 函数重载的替代方案。

#### 最佳实践
- 默认值应选择最常用、最安全的选项。
- 避免使用可变对象作为默认值（如列表、Map），因为默认值在多次调用间共享可能引发意外。

### 注意事项
- **常见错误**：将默认值设为 `null` 但类型不可空，导致编译错误。
- **性能问题**：无。
- **兼容性**：默认值在 Dart 1.x 就支持。

### 代码示例
```dart
void showMessage(String message, {String prefix = 'Info:', bool bold = false}) {
  var output = bold ? '**$prefix $message**' : '$prefix $message';
  print(output);
}

void main() {
  showMessage('Hello'); // Info: Hello
  showMessage('Warning', prefix: 'Error:', bold: true); // **Error: Warning**
}
```

---

## 5. 箭头函数

### 概念
- **是什么**：使用 `=>` 语法定义的简写函数，适用于函数体只包含一个表达式的情况。
- **为什么需要**：简化代码，提高可读性，尤其适合短小的回调或映射函数。
- **解决什么问题**：避免冗余的 `{ return ...; }` 模板代码。

### 原理
- **底层实现**：箭头函数是语法糖，编译器将其转换为包含返回语句的普通函数。
- **工作流程**：`=> expr` 等价于 `{ return expr; }`。
- **关键技术点**：`expr` 必须是表达式（有值），不能是语句（如 if、for）。

### 使用方法
#### 基本用法
```dart
bool isEven(int n) => n % 2 == 0;

// 匿名箭头函数
var list = [1, 2, 3];
list.forEach((item) => print(item));
```

#### 常见场景
- 简单的计算函数。
- 集合操作的回调（如 `map`、`where`）。
- 返回函数的函数（闭包）。

#### 最佳实践
- 仅当函数体简单且直观时使用箭头函数，复杂逻辑应使用完整函数体。
- 注意箭头后面不能跟语句，如需条件判断可用条件表达式。

### 注意事项
- **常见错误**：在箭头后面使用 `if` 等语句。
- **性能问题**：无区别。
- **兼容性**：所有 Dart 版本均支持。

### 代码示例
```dart
void main() {
  // 错误：箭头后面不能跟语句
  // var isPositive = (int n) => if (n > 0) true else false; // 编译错误

  // 正确：使用条件表达式
  var isPositive = (int n) => n > 0;

  print(isPositive(5)); // true
}
```

---

## 6. 匿名函数（Lambda/闭包）

### 概念
- **是什么**：没有名称的函数，也称为 lambda 表达式或闭包。可作为参数传递、赋值给变量。
- **为什么需要**：临时定义一次性使用的函数，特别是作为回调。
- **解决什么问题**：使代码更灵活，支持函数式编程风格。

### 原理
- **底层实现**：匿名函数也是 `Function` 对象，与命名函数无异，但可能捕获周围作用域的变量（形成闭包）。
- **工作流程**：定义时创建函数对象，可以访问定义时的作用域（词法作用域）。
- **关键技术点**：闭包特性使得匿名函数可以记住并访问其词法作用域中的变量。

### 使用方法
#### 基本用法
```dart
// 完整形式
var multiply = (int a, int b) {
  return a * b;
};

// 箭头简写
var add = (int a, int b) => a + b;
```

#### 常见场景
- 集合方法如 `forEach`、`map` 的回调。
- 事件处理。
- 创建闭包保存状态。

#### 最佳实践
- 如果函数较长或需要复用，考虑定义为命名函数。
- 注意闭包可能导致变量生命周期延长。

### 注意事项
- **常见错误**：在循环中创建匿名函数并引用循环变量，可能捕获同一变量导致意外。
- **性能问题**：频繁创建匿名函数可能增加 GC 压力，但通常可忽略。
- **兼容性**：广泛支持。

### 代码示例
```dart
void main() {
  var list = ['a', 'b', 'c'];
  
  // 匿名函数作为参数
  list.forEach((item) {
    print(item.toUpperCase());
  });
  
  /*
   - list 是 ['a', 'b', 'c']                                      
  - forEach 会遍历列表中的每个元素
  - (item) { ... } 就是一个**匿名函数**——没有名字，直接写在参数里        
  - 每次循环把元素转成大写并打印                                       
  输出： 
  A
  B 
  C
  */
  
  // 赋值给变量
  var toUpper = (String s) => s.toUpperCase();
  print(toUpper('hello')); // HELLO
}

/*
  - (String s) => s.toUpperCase() 是一个**箭头函数**（匿名函数的简写形式，函数体只有一个表达式时可以用 =>）                 
  - 把这个函数赋值给变量 toUpper，之后就可以像普通函数一样调用 toUpper('hello')
  输出：          
  HELLO
  */
```

---

## 7. 词法作用域

### 概念
- **是什么**：变量的作用域由代码的物理结构（大括号）决定，内部可以访问外部变量。
- **为什么需要**：提供变量可见性的规则，避免命名冲突，实现封装。
- **解决什么问题**：确定变量在何处可用，支持闭包的基础。

### 原理
- **底层实现**：编译器在解析时构建作用域链，每个大括号块创建一个新的作用域，嵌套块形成链。
- **工作流程**：访问变量时，从当前作用域向上查找，直到找到或到达全局作用域。
- **关键技术点**：Dart 采用静态作用域（词法作用域），即变量的作用域在编译时确定。

### 使用方法
#### 基本用法
```dart
bool topLevel = true;

void main() {
  var insideMain = true;

  void myFunction() {
    var insideFunction = true;

    void nestedFunction() {
      var insideNested = true;
      assert(topLevel);
      assert(insideMain);
      assert(insideFunction);
      assert(insideNested);
    }
  }
}
```

#### 常见场景
- 理解变量访问规则。
- 设计嵌套函数时避免变量名冲突。

#### 最佳实践
- 尽量保持作用域扁平，避免过深嵌套。
- 使用局部变量隐藏外部变量时需谨慎。

### 注意事项
- **常见错误**：混淆作用域，试图访问外部不可访问的变量。
- **性能问题**：作用域链查找有微小开销，但现代引擎优化良好。
- **兼容性**：所有 Dart 版本遵循词法作用域。

### 代码示例
```dart
void main() {
  var x = 10;
  
  void inner() {
    var x = 20; // 隐藏外部 x
    print(x); // 20
  }
  
  inner();
  print(x); // 10
}
```

---

## 8. 词法闭包

### 概念
- **是什么**：函数对象能够记住并访问其词法作用域中的变量，即使函数在定义该作用域之外执行。
- **为什么需要**：实现数据隐藏、工厂函数、柯里化等高级编程模式。
- **解决什么问题**：允许函数携带环境状态，创建具有私有状态的函数。

### 原理
- **底层实现**：闭包在创建时，会将引用的外部变量保存到堆上，形成一个闭包环境。函数对象持有对环境的引用。
- **工作流程**：当函数被调用时，除了执行自身代码，还可以访问保存在闭包环境中的变量。
- **关键技术点**：捕获的变量生命周期延长，直到没有闭包引用它们。

### 使用方法
#### 基本用法
```dart
Function makeAdder(int addBy) {
  return (int i) => addBy + i; // 捕获 addBy
}

void main() {
  var add2 = makeAdder(2);
  print(add2(3)); // 5
}
```

#### 常见场景
- 创建带有私有状态的函数（如计数器）。
- 实现回调函数携带额外数据。
- 函数式编程中的柯里化。

#### 最佳实践
- 注意闭包可能导致内存泄漏，如果闭包长时间持有大对象。
- 理解捕获的变量是引用还是值：Dart 捕获的是变量引用，因此修改变量会影响所有闭包。

### 注意事项
- **常见错误**：在循环中创建闭包并期望捕获循环变量当前值，可能捕获到同一个变量（需要使用局部副本解决）。
- **性能问题**：闭包需要额外内存存储环境，但通常可接受。
- **兼容性**：所有 Dart 版本支持。

### 代码示例
```dart
void main() {
  // 计数器示例
  Function makeCounter() {
    int count = 0;
    return () {
      count++;
      return count;
    };
  }
  
  var counter = makeCounter();
  print(counter()); // 1
  print(counter()); // 2
  
  // 循环中的陷阱
  List<Function> functions = [];
  for (var i = 0; i < 3; i++) {
    functions.add(() => print(i)); // 捕获同一个 i
  }
  functions.forEach((f) => f()); // 输出 3 3 3 （因为循环结束后 i=3）
  
  // 解决方法：创建局部副本
  for (var i = 0; i < 3; i++) {
    var j = i;
    functions.add(() => print(j)); // 捕获 j
  }
  functions.forEach((f) => f()); // 输出 0 1 2
}
```

---

## 9. Tear-off（分离）

### 概念
- **是什么**：当引用一个函数、方法或命名构造函数但不加括号调用时，Dart 会创建一个 tear-off，即一个与原始函数相同签名的闭包。
- **为什么需要**：简化将函数作为参数传递的语法，避免手动包装 lambda。
- **解决什么问题**：减少样板代码，提高可读性。

### 原理
- **底层实现**：tear-off 是一个函数对象，它包装了对原始函数的调用。当 tear-off 被调用时，它会将参数转发给原始函数。
- **工作流程**：例如 `list.forEach(print)` 中，`print` 是一个 tear-off，每次迭代时 `forEach` 调用该 tear-off 并传入元素，tear-off 再调用真正的 `print` 函数。
- **关键技术点**：tear-off 适用于任何函数、方法（包括实例方法）和命名构造函数，要求签名匹配。

### 使用方法
#### 基本用法
```dart
void printElement(int element) => print(element);

void main() {
  var list = [1, 2, 3];
  list.forEach(printElement); // tear-off
}
```

#### 常见场景
- 将函数作为回调传递。
- 简化集合操作。

#### 最佳实践
- 尽量使用 tear-off 而非显式 lambda（`(x) => f(x)`），更简洁且性能略好。
- 注意实例方法的 tear-off 会捕获 `this` 引用，可能延长对象生命周期。

### 注意事项
- **常见错误**：认为 tear-off 是直接传递函数指针，实际上 tear-off 保持了上下文（如实例方法绑定到特定对象）。
- **性能问题**：tear-off 通常比手动 lambda 更高效，因为编译器可直接复用函数对象。
- **兼容性**：Dart 2.x 支持。

### 代码示例
```dart
void main() {
  var list = [1, 2, 3];
  var buffer = StringBuffer();
  
  // 方法 tear-off
  list.forEach(buffer.write); // 等价于 (x) => buffer.write(x)
  
  print(buffer); // 123
}
```

---

## 10. 函数作为一等对象

### 概念
- **是什么**：函数可以作为值赋值给变量、作为参数传递给其他函数、作为函数的返回值。Dart 中函数是 `Function` 类型的对象。
- **为什么需要**：支持高阶函数、函数式编程风格，提高代码灵活性。
- **解决什么问题**：实现回调、策略模式、事件处理等。

### 原理
- **底层实现**：每个函数在内存中都是一个对象，实现了 `Function` 接口。函数类型（如 `void Function(int)`）是 `Function` 的子类型。
- **工作流程**：函数赋值只是引用传递，调用时通过引用执行。
- **关键技术点**：函数类型可以显式声明，方便类型检查。

### 使用方法
#### 基本用法
```dart
void printMessage(String msg) => print(msg);

void main() {
  // 赋值给变量
  void Function(String) myPrinter = printMessage;
  myPrinter('Hello'); // Hello
  
  // 作为参数传递
  void execute(void Function(String) func, String arg) {
    func(arg);
  }
  execute(printMessage, 'Hi'); // Hi
  
  // 作为返回值
  Function makeGreeter(String greeting) {
    return (String name) => '$greeting $name';
  }
  var greeter = makeGreeter('Hello');
  print(greeter('Dart')); // Hello Dart
}
```

#### 常见场景
- 事件监听、回调。
- 集合操作如 `map`、`where`。
- 依赖注入。

#### 最佳实践
- 使用明确的函数类型而非 `Function`，提高类型安全。
- 对于复杂签名，可定义 `typedef`。

### 注意事项
- **常见错误**：使用 `Function` 类型导致丢失参数类型信息，可能引发运行时错误。
- **性能问题**：函数对象传递无额外开销。
- **兼容性**：所有 Dart 版本支持。

### 代码示例
```dart
typedef IntOperation = int Function(int, int);

int add(int a, int b) => a + b;
int multiply(int a, int b) => a * b;

void main() {
  IntOperation op = add;
  print(op(3, 4)); // 7
  
  op = multiply;
  print(op(3, 4)); // 12
}
```

---

## 11. 函数相等性

### 概念
- **是什么**：函数也是对象，可以比较两个函数对象是否相等（引用相等）。
- **为什么需要**：在需要判断函数是否相同时使用，例如去重、缓存。
- **解决什么问题**：确定两个函数引用是否指向同一个函数对象。

### 原理
- **底层实现**：Dart 使用引用相等性判断两个函数是否相同。对于顶层函数、静态方法，只有一个实例；对于实例方法，每个实例绑定的方法闭包是不同的对象。
- **工作流程**：使用 `==` 比较函数变量，比较的是引用地址。
- **关键技术点**：顶层函数、静态方法全局唯一；实例方法每次绑定到不同实例时生成新的闭包。

### 使用方法
#### 基本用法
```dart
void foo() {}
class A {
  static void bar() {}
  void baz() {}
}

void main() {
  Function x;
  
  x = foo;
  assert(foo == x);
  
  x = A.bar;
  assert(A.bar == x);
  
  var v = A();
  var w = A();
  x = w.baz;
  assert(w.baz == x);
  assert(v.baz != w.baz);
}
```

#### 常见场景
- 缓存回调函数时判断是否已存在。
- 调试时确认函数引用。

#### 最佳实践
- 不要依赖函数相等性进行业务逻辑，除非明确知道函数来源。
- 注意实例方法绑定的闭包每次 `==` 比较可能为 `false`。

### 注意事项
- **常见错误**：误以为相同代码的匿名函数是相等的，实际上每次创建都是新对象。
- **性能问题**：比较函数对象是 O(1) 操作。
- **兼容性**：稳定。

### 代码示例
```dart
void main() {
  var f1 = () => print('hi');
  var f2 = () => print('hi');
  print(f1 == f2); // false，因为是不同的闭包对象
}
```

---

## 12. Getter 和 Setter

### 概念
- **是什么**：用于读取和写入属性的特殊方法，分别用 `get` 和 `set` 关键字定义。变量隐式创建 getter 和 setter。
- **为什么需要**：提供对属性访问的拦截，可以在读写时执行额外逻辑（如验证、计算、日志）。
- **解决什么问题**：实现封装，将内部表示与外部接口分离，同时保持属性访问的语法简洁。

### 原理
- **底层实现**：getter 是无参数函数，setter 是有一个参数的函数。属性访问（如 `obj.prop`）实际上调用 getter；赋值（如 `obj.prop = value`）调用 setter。
- **工作流程**：当代码访问属性时，编译器生成调用对应 getter/setter 的指令。
- **关键技术点**：getter 和 setter 可以替代计算属性，也可用于包装私有字段。

### 使用方法
#### 基本用法
```dart
class Rectangle {
  double width, height;
  
  Rectangle(this.width, this.height);
  
  // Getter 计算面积
  double get area => width * height;
  
  // Setter 修改宽度（同时可以做验证）
  set width(double value) {
    if (value < 0) throw ArgumentError('Width cannot be negative');
    width = value; // 注意：这里会导致递归调用，需要使用不同的字段名
  }
}
```

正确做法：通常使用私有字段存储实际值。
```dart
class Rectangle {
  double _width, _height;
  
  Rectangle(this._width, this._height);
  
  double get width => _width;
  set width(double value) {
    if (value < 0) throw ArgumentError('Width cannot be negative');
    _width = value;
  }
  
  double get area => _width * _height;
}
```

#### 常见场景
- 计算属性（如面积）。
- 数据验证、转换。
- 懒加载属性。

#### 最佳实践
- getter 不应有副作用，setter 可以包含验证逻辑。
- 避免在 getter/setter 中执行耗时操作。

### 注意事项
- **常见错误**：在 setter 内直接给同名属性赋值导致无限递归（应使用不同的后备字段）。
- **性能问题**：getter/setter 调用相当于方法调用，但通常开销可忽略。
- **兼容性**：Dart 原生支持。

### 代码示例
```dart
class Person {
  String _name;
  
  Person(this._name);
  
  String get name => _name.toUpperCase();
  
  set name(String newName) {
    if (newName.isNotEmpty) {
      _name = newName;
    }
  }
}

void main() {
  var p = Person('alice');
  print(p.name); // ALICE
  p.name = 'bob';
  print(p.name); // BOB
}
```

---

## 13. 生成器函数

### 概念
- **是什么**：能够延迟生成一系列值的函数。同步生成器返回 `Iterable`，异步生成器返回 `Stream`。
- **为什么需要**：处理大量数据时避免一次性加载所有元素，节省内存，支持惰性计算。
- **解决什么问题**：实现惰性序列、无限序列、异步数据流。

### 原理
- **底层实现**：生成器函数使用 `sync*` 或 `async*` 修饰，内部通过 `yield` 或 `yield*` 产出值。编译器将其转换为状态机，每次迭代执行到下一个 `yield` 暂停并返回值。
- **工作流程**：
  - 同步生成器：每次迭代调用 `moveNext()`，函数执行到下一个 `yield` 返回，保存状态。
  - 异步生成器：类似，但值通过 `Stream` 发送。
- **关键技术点**：`yield` 产出单个值，`yield*` 用于递归调用另一个生成器（扁平化产出）。

### 使用方法
#### 基本用法
```dart
// 同步生成器
Iterable<int> naturalsTo(int n) sync* {
  int k = 0;
  while (k < n) yield k++;
}

// 异步生成器
Stream<int> asynchronousNaturalsTo(int n) async* {
  int k = 0;
  while (k < n) yield k++;
}

// 递归使用 yield*
Iterable<int> naturalsDownFrom(int n) sync* {
  if (n > 0) {
    yield n;
    yield* naturalsDownFrom(n - 1);
  }
}
```

#### 常见场景
- 生成斐波那契数列等无限序列。
- 分页加载数据。
- 处理大文件逐行读取。

#### 最佳实践
- 使用生成器处理可能无限或大量数据。
- 注意 `yield*` 提高递归效率。

### 注意事项
- **常见错误**：在生成器内使用 `return` 返回值（生成器不允许返回有意义的值，只用于结束）。
- **性能问题**：生成器状态机开销通常小于显式构建集合。
- **兼容性**：Dart 2.0 后支持。

### 代码示例
```dart
Iterable<int> fibonacci() sync* {
  int a = 0, b = 1;
  while (true) {
    yield a;
    var next = a + b;
    a = b;
    b = next;
  }
}

void main() {
  var fib = fibonacci().take(10); // 取前10个
  print(fib.toList()); // [0, 1, 1, 2, 3, 5, 8, 13, 21, 34]
}
```

---

## 14. 外部函数

### 概念
- **是什么**：使用 `external` 关键字声明的函数，其实现由外部提供（如其他 Dart 库或原生代码）。
- **为什么需要**：用于与原生平台（C、JavaScript）交互，或声明平台特定 API。
- **解决什么问题**：提供跨语言调用的桥梁，允许 Dart 使用现有原生库。

### 原理
- **底层实现**：`external` 函数声明告诉编译器该函数将在外部实现，编译器生成调用外部代码的桩代码。
- **工作流程**：调用外部函数时，Dart 运行时通过 FFI（Foreign Function Interface）或 JS 互操作层调用对应实现。
- **关键技术点**：不同平台实现机制不同（如 `dart:ffi` 用于 C 互操作，`js` 包用于 JavaScript）。

### 使用方法
#### 基本用法
```dart
// 声明一个外部函数
external void someFunc(int i);

// 使用 dart:ffi 绑定 C 函数
import 'dart:ffi';

typedef CFunction = Void Function(Int32);
final cFunc = nativeLib
    .lookup<NativeFunction<CFunction>>('c_function')
    .asFunction();
```

#### 常见场景
- 调用操作系统 API。
- 复用现有 C/C++ 库。
- 在 Web 平台调用 JavaScript 函数。

#### 最佳实践
- 确保外部函数签名与实现一致。
- 处理平台差异，使用条件导入。

### 注意事项
- **常见错误**：外部函数未正确链接导致运行时错误。
- **性能问题**：跨语言调用有一定开销，但通常可接受。
- **兼容性**：依赖具体平台和 FFI 支持。

### 代码示例
```dart
// 假设有一个 C 库提供了 int add(int a, int b)
import 'dart:ffi';
import 'dart:io' show Platform;

final DynamicLibrary nativeLib = Platform.isWindows
    ? DynamicLibrary.open('my_lib.dll')
    : DynamicLibrary.open('libmy_lib.so');

typedef CAdd = Int32 Function(Int32 a, Int32 b);
typedef DartAdd = int Function(int a, int b);

final DartAdd add = nativeLib
    .lookup<NativeFunction<CAdd>>('add')
    .asFunction();

void main() {
  print(add(3, 4)); // 7
}
```

---

## 15. main() 函数

### 概念
- **是什么**：每个 Dart 程序的入口点，必须存在一个顶层 `main()` 函数。
- **为什么需要**：定义程序启动时执行的代码。
- **解决什么问题**：作为程序的起点，接收命令行参数。

### 原理
- **底层实现**：Dart 虚拟机或编译后的可执行文件在启动时调用 `main` 函数。
- **工作流程**：运行环境加载程序，调用 `main`，`main` 执行完毕后程序退出。
- **关键技术点**：`main` 返回 `void`，可选接受 `List<String>` 参数。

### 使用方法
#### 基本用法
```dart
void main() {
  print('Hello, World!');
}
```

带命令行参数：
```dart
void main(List<String> arguments) {
  print(arguments);
}
```

#### 常见场景
- 命令行工具解析参数。
- 初始化应用。
- 启动 Flutter 应用（`runApp`）。

#### 最佳实践
- 保持 `main` 简洁，调用其他函数完成工作。
- 使用 `args` 库解析复杂命令行参数。

### 注意事项
- **常见错误**：定义多个 `main` 函数导致冲突。
- **性能问题**：无。
- **兼容性**：始终存在。

### 代码示例
```dart
// 命令行参数示例
void main(List<String> args) {
  if (args.isEmpty) {
    print('Usage: dart program.dart <name>');
    return;
  }
  print('Hello, ${args[0]}!');
}
```

以下是对 Dart 函数相关知识的补充，涵盖函数类型别名、递归函数和异步函数等内容。

---

## 16. 函数类型别名（typedef）

### 概念
- **是什么**：使用 `typedef` 关键字为函数类型定义一个名称，方便在代码中复用该类型。
- **为什么需要**：当函数类型复杂（如参数较多、返回类型特殊）时，使用类型别名可以提高代码可读性和可维护性。
- **解决什么问题**：避免重复书写冗长的函数签名，支持更清晰的类型注解。

### 原理
- **底层实现**：`typedef` 在编译时被解析为对应的函数类型，不影响运行时性能。它本质上是给函数类型起了一个别名。
- **工作流程**：定义别名后，可以在变量声明、参数类型、返回类型等位置使用该别名。
- **关键技术点**：Dart 2.13 开始支持非函数类型的类型别名（泛型别名），但函数类型别名一直存在。

### 使用方法
#### 基本用法
```dart
typedef IntOperation = int Function(int a, int b);

int add(int a, int b) => a + b;
int multiply(int a, int b) => a * b;

void main() {
  IntOperation op = add;
  print(op(3, 4)); // 7
  
  op = multiply;
  print(op(3, 4)); // 12
}
```

#### 带泛型的类型别名
```dart
typedef Compare<T> = int Function(T a, T b);

int intCompare(int a, int b) => a - b;
int stringCompare(String a, String b) => a.compareTo(b);

void main() {
  Compare<int> intCmp = intCompare;
  print(intCmp(5, 3)); // 2
  
  Compare<String> strCmp = stringCompare;
  print(strCmp('b', 'a')); // 1
}
```

#### 常见场景
- 定义回调函数类型。
- 简化高阶函数的参数类型。
- 统一多个相似函数的类型。

#### 最佳实践
- 为频繁使用的函数类型定义别名。
- 别名命名应清晰反映其用途（如 `Filter`, `Mapper`）。

### 注意事项
- **常见错误**：在旧版 Dart 中，`typedef` 只能用于函数类型，现在已支持泛型别名。
- **性能问题**：无。
- **兼容性**：Dart 2.13+ 支持泛型别名，早期版本只支持函数类型别名。

### 代码示例
```dart
typedef ProgressCallback = void Function(double progress);

void downloadFile(String url, ProgressCallback onProgress) {
  for (int i = 0; i <= 100; i += 10) {
    onProgress(i / 100);
  }
}

void main() {
  downloadFile('http://example.com/file', (p) {
    print('Downloaded ${p * 100}%');
  });
}
```

---

## 17. 递归函数

### 概念
- **是什么**：函数直接或间接调用自身，称为递归函数。
- **为什么需要**：解决可以分解为相似子问题的问题，如树遍历、分治算法、数学计算（阶乘、斐波那契）。
- **解决什么问题**：提供一种简洁的解决方案，替代循环实现某些算法。

### 原理
- **底层实现**：每次递归调用都会在调用栈上创建一个新的栈帧，保存局部变量和返回地址。递归深度过大可能导致栈溢出。
- **工作流程**：递归函数通常包含基线条件（停止递归）和递归步骤（调用自身逼近基线）。
- **关键技术点**：尾递归优化（Dart 不支持自动尾递归优化，但可通过循环或 trampoline 模拟）。

### 使用方法
#### 基本用法
```dart
// 阶乘
int factorial(int n) {
  if (n <= 1) return 1;
  return n * factorial(n - 1);
}

// 斐波那契
int fibonacci(int n) {
  if (n <= 1) return n;
  return fibonacci(n - 1) + fibonacci(n - 2);
}
```

#### 常见场景
- 遍历树形结构（如文件系统、DOM 树）。
- 分治算法（快速排序、归并排序）。
- 回溯算法（八皇后、迷宫求解）。

#### 最佳实践
- 确保基线条件正确，避免无限递归。
- 注意递归深度，对于大数据集考虑使用迭代或尾递归优化。
- 使用尾递归风格时，可通过循环重写以提高性能。

### 注意事项
- **常见错误**：忘记基线条件导致栈溢出；重复计算（如斐波那契）导致指数级时间复杂度。
- **性能问题**：递归调用开销较大，且可能引发栈溢出。可通过记忆化（缓存）优化重复计算。
- **兼容性**：所有 Dart 版本支持。

### 代码示例
```dart
// 尾递归风格的阶乘（Dart 不会优化，但可手工转为循环）
int factorialTail(int n, [int acc = 1]) {
  if (n <= 1) return acc;
  return factorialTail(n - 1, n * acc);
}

// 使用记忆化优化斐波那契
Map<int, int> cache = {0: 0, 1: 1};
int fibMemo(int n) {
  if (cache.containsKey(n)) return cache[n]!;
  cache[n] = fibMemo(n - 1) + fibMemo(n - 2);
  return cache[n]!;
}

void main() {
  print(factorial(5)); // 120
  print(fibonacci(10)); // 55
  print(fibMemo(40)); // 102334155（快速计算）
}
```

---

## 18. 异步函数（async/await）

### 概念
- **是什么**：使用 `async` 关键字标记的函数，允许在其中使用 `await` 等待 Future 完成，返回一个 Future 对象。
- **为什么需要**：简化异步代码编写，避免回调地狱，使异步代码看起来像同步代码。
- **解决什么问题**：处理 I/O 操作、网络请求、定时器等非阻塞任务，提高程序响应性。

### 原理
- **底层实现**：`async` 函数被编译器转换为状态机。遇到 `await` 时，函数暂停执行，将后续代码注册为 Future 的回调，然后返回未完成的 Future。当等待的 Future 完成后，恢复执行。
- **工作流程**：
  1. 调用 async 函数，立即返回一个 Future。
  2. 函数体开始同步执行，直到遇到第一个 `await`。
  3. 暂停，等待 Future 完成，期间不会阻塞线程。
  4. Future 完成后，从暂停处恢复执行。
  5. 最终返回值（或异常）作为 Future 的结果。
- **关键技术点**：`async` 函数必须返回 `Future<T>`，但 Dart 允许返回 `T` 时自动包装为 `Future<T>`。

### 使用方法
#### 基本用法
```dart
Future<String> fetchUserData() async {
  // 模拟网络请求
  await Future.delayed(Duration(seconds: 1));
  return 'User data';
}

void main() async {
  print('Start');
  String data = await fetchUserData();
  print(data);
  print('End');
}
```

#### 错误处理
```dart
Future<void> fetchData() async {
  try {
    var data = await http.get(Uri.parse('...'));
    print(data.body);
  } catch (e) {
    print('Error: $e');
  }
}
```

#### 并行等待
```dart
Future<void> fetchMultiple() async {
  var future1 = fetchUserData();
  var future2 = fetchSettings();
  
  // 并行执行
  var results = await Future.wait([future1, future2]);
  print(results);
}
```

#### 常见场景
- 网络请求、文件读写、数据库操作。
- 定时任务。
- 任何可能耗时的 I/O 操作。

#### 最佳实践
- 使用 `async`/`await` 而非原始的 `then()`，提高可读性。
- 注意不要滥用 `async`，如果函数不需要等待，不必标记为 `async`。
- 处理错误时使用 `try/catch` 而非 `.catchError`。

### 注意事项
- **常见错误**：在 async 函数中忘记返回 Future 类型；在非 async 函数中使用 `await`；过度串行化导致性能低下。
- **性能问题**：`await` 会引入微任务调度，但通常开销可忽略。并行操作应使用 `Future.wait`。
- **兼容性**：Dart 2.0 后支持。

### 代码示例
```dart
Future<int> calculateHeavy() async {
  // 模拟耗时计算
  await Future.delayed(Duration(seconds: 2));
  return 42;
}

void main() async {
  print('Calculating...');
  int result = await calculateHeavy();
  print('Result: $result');
  
  // 并行示例
  var f1 = Future.delayed(Duration(seconds: 1), () => 'Hello');
  var f2 = Future.delayed(Duration(seconds: 2), () => 'World');
  
  var greetings = await Future.wait([f1, f2]);
  print(greetings); // [Hello, World]
}
```

---

## 19. 函数组合与高阶函数

### 概念
- **是什么**：高阶函数是指接受函数作为参数或返回函数的函数。函数组合是将多个函数组合成一个新函数的技术。
- **为什么需要**：实现更抽象的代码复用，构建管道式数据处理流程。
- **解决什么问题**：简化复杂变换，提高代码模块化程度。

### 原理
- **底层实现**：高阶函数基于 Dart 的函数一等对象特性实现。组合函数通过闭包捕获中间函数并调用它们。
- **工作流程**：例如 `compose(f, g)(x)` 等价于 `f(g(x))`。
- **关键技术点**：函数组合通常通过手动定义组合函数或使用扩展方法实现。

### 使用方法
#### 基本用法（手动组合）
```dart
int add2(int x) => x + 2;
int multiply3(int x) => x * 3;

// 手动组合
int add2ThenMultiply3(int x) => multiply3(add2(x));

void main() {
  print(add2ThenMultiply3(5)); // (5+2)*3 = 21
}
```

#### 使用扩展方法实现组合
```dart
extension FunctionComposition<T, R> on R Function(T) {
  T Function(S) compose<S>(R Function(S) f) => (s) => this(f(s));
}

void main() {
  var add2 = (int x) => x + 2;
  var multiply3 = (int x) => x * 3;
  
  var composed = multiply3.compose(add2);
  print(composed(5)); // 21
}
```

#### 常见场景
- 数据管道处理（如集合的 `map`、`where` 链式调用本身就是一种组合）。
- 中间件模式（如 HTTP 请求处理）。
- 装饰器模式（函数增强）。

#### 最佳实践
- 组合函数应保持纯函数（无副作用）以利于测试和推理。
- 组合顺序应清晰，通常从右向左（`f(g(x))`）。

### 注意事项
- **常见错误**：组合时参数类型不匹配导致编译错误。
- **性能问题**：多次函数调用开销，但通常可忽略。
- **兼容性**：所有 Dart 版本。

### 代码示例
```dart
// 扩展方法实现组合
extension Compose<T, R> on R Function(T) {
  R Function(S) pipe<S>(S Function(T) before) => (s) => this(before(s));
}

void main() {
  var toInt = (String s) => int.parse(s);
  var abs = (int x) => x.abs();
  
  var parseAndAbs = abs.pipe(toInt); // 先 toInt，再 abs
  print(parseAndAbs('-42')); // 42
}
```

