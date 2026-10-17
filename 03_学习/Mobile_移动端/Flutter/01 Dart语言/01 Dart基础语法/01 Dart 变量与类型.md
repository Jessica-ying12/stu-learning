## 一、变量声明

### 1. `var` 关键字

#### 概念
`var` 用于声明一个变量，并让 Dart 根据初始值自动推断变量的类型。一旦类型被推断，该变量的类型就固定了，不能再改变。

#### 原理
Dart 编译器在编译时会分析初始化表达式的静态类型，并将该类型作为变量的类型。这属于静态类型推断，不会影响运行时性能。

#### 使用方法
```dart
var name = 'Bob';        // 推断为 String
var age = 30;            // 推断为 int
var list = [1, 2, 3];    // 推断为 List<int>
```
- 必须在声明时初始化，否则无法推断类型（此时变量类型为 `dynamic`，不推荐）。
- 局部变量优先使用 `var`（遵循 Dart 风格指南）。

#### 注意事项
- 不能将不同类型的值重新赋值给 `var` 变量：
  ```dart
  var name = 'Bob';
  name = 42; // 编译错误：String 不能赋值给 int
  ```
- 如果声明时未初始化，变量类型为 `dynamic`，会失去静态类型检查的优势。

#### 代码示例
```dart
void main() {
  var message = 'Hello';  // String
  // message = 123;       // 错误

  var number = 10;        // int
  print(number + 5);      // OK

  var dynamicVar;         // 类型为 dynamic，不推荐
  dynamicVar = 'hello';
  dynamicVar = 123;       // 无错误，但危险
}
```

---

### 2. 显式类型声明

#### 概念
在变量名前指定类型，明确告诉编译器变量的类型。这是类型安全的保证，也是 Dart 作为强类型语言的基础。

#### 原理
编译器直接使用指定的类型进行静态检查，不会进行推断。这样可以强制类型约束，避免意外赋值错误。

#### 使用方法
```dart
String name = 'Bob';
int age = 30;
Object anything = 'A string';  // Object 可以指向任何非空对象
dynamic anything2 = 'A string'; // dynamic 完全关闭类型检查
```

#### 注意事项
- **`Object` vs `dynamic`**：
  - `Object` 是类型层次结构的根，所有非空类型都是其子类。声明为 `Object` 的变量可以调用 `Object` 的方法（如 `toString()`），但不能调用子类特有方法。
  - `dynamic` 会关闭静态类型检查，任何方法调用都被允许，但可能引发运行时错误。
- 对于泛型集合，推荐显式指定类型参数以避免 `dynamic`：
  ```dart
  List<String> names = [];  // 推荐
  var names = <String>[];   // 等价，类型推断仍为 List<String>
  ```

#### 代码示例
```dart
void main() {
  String name = 'Bob';
  // name = 42;           // 错误

  Object obj = 'Hello';
  print(obj.toString());   // OK
  // print(obj.length);    // 错误：Object 没有 length 属性

  dynamic dyn = 'Hello';
  print(dyn.length);       // 运行时 OK，但如果 dyn 是数字则会出错
}
```

---

### 3. 默认值

#### 概念
未初始化的变量在 Dart 中会有默认值，但仅适用于可空类型。对于非空类型，必须在使用前显式初始化。

#### 原理
Dart 的空安全机制要求变量要么在声明时初始化，要么在使用前确保已赋值。可空类型默认初始化为 `null`，而非空类型没有默认值。

#### 使用方法
```dart
int? lineCount;        // 可空，默认 null
assert(lineCount == null);

int nonNullable;       // 错误：非空变量必须初始化
```

#### 注意事项
- 顶级变量和类变量是延迟初始化的，即第一次使用时才初始化，但空安全规则仍然适用。
- 局部变量在使用前必须初始化，否则编译错误。

#### 代码示例
```dart
int? globalNullable;   // 默认 null

void main() {
  int? localNullable;
  print(localNullable); // null

  // int localNonNull;  // 编译错误

  int localNonNull = 0; // 必须初始化
}
```

---

### 4. `late` 延迟初始化

#### 概念
`late` 修饰符用于声明一个非空变量，但不在声明时立即初始化。它告诉编译器：“这个变量会在使用前被赋值，请相信我。”

#### 原理
`late` 变量会在第一次访问时检查是否已被赋值。如果未赋值，则抛出运行时错误。如果 `late` 变量有初始化器，初始化器会延迟到第一次访问时执行（惰性初始化）。

#### 使用方法
- **场景一**：非空变量但无法在声明时初始化（如依赖条件赋值）：
  ```dart
  late String description;
  void main() {
    description = 'Feijoada!';
    print(description);
  }
  ```
- **场景二**：开销大的初始化，希望只在需要时执行：
  ```dart
  late String temperature = readThermometer(); // 仅当使用时才调用
  ```

#### 注意事项
- 如果 `late` 变量在使用前未被赋值，会抛出 `LateInitializationError`。
- `late` 可以用于实例变量，常用于依赖注入或后续初始化。
- 可以与 `final` 结合：`late final` 表示延迟初始化且只赋值一次。

#### 代码示例
```dart
class HeavyData {
  late final String data = computeData(); // 惰性初始化
  String computeData() {
    print('Computing...');
    return 'result';
  }
}

void main() {
  var h = HeavyData();
  print('Object created'); // 不会触发 computeData
  print(h.data);            // 第一次访问，触发 computeData
}
```

---

### 5. `final` 和 `const`

#### 概念
- **`final`**：变量只能被赋值一次（运行时常量）。
- **`const`**：变量是编译时常量，值在编译时确定，且完全不可变。

#### 原理
- `final` 变量在运行时确定值，一旦赋值后不可修改。
- `const` 变量是编译时常量，Dart 会在编译期间计算其值，并确保该值在整个生命周期内不变。`const` 可用于变量、字面量、构造函数等。

#### 使用方法
```dart
final name = 'Bob';           // 运行时常量
final String nickname = 'Bobby';

const bar = 1000000;          // 编译时常量
const double atm = 1.01325 * bar; // 算术表达式也是常量

var foo = const [];           // foo 本身可变，但当前值是常量
final bar = const [];         // bar 不可变，值是常量
const baz = [];               // 相当于 const []
```

#### 注意事项
- 实例变量可以是 `final`，但不能是 `const`（因为实例变量在运行时才存在）。
- `const` 变量必须用编译时常量初始化，例如数字、字符串、`const` 修饰的构造函数等。
- `final` 对象本身不能修改，但其字段可能可变（如果字段不是 `final`）。
- `const` 对象及其所有字段都不可变。

#### 代码示例
```dart
void main() {
  final list1 = [1, 2, 3];
  list1[0] = 10;        // OK：list1 本身不可重新赋值，但其内容可变

  const list2 = [1, 2, 3];
  // list2[0] = 10;     // 错误：常量列表不可修改

  final now = DateTime.now();   // OK，运行时确定
  // const now = DateTime.now(); // 错误：不是编译时常量
}
```

#### 编译时常量 vs 运行时常量
- **编译时常量（`const`）**：值在编译期间确定，且完全不可变。编译器会将所有 `const` 对象统一为同一个实例（规范化）。只有数字、字符串、布尔值、`const` 修饰的集合或构造函数的结果才能作为编译时常量。
- **运行时常量（`final`）**：值在运行时确定，但一旦赋值不可改变。`final` 变量的值可以是任何运行时计算的结果，例如 `DateTime.now()`。

#### 写代码测试三者区别
```dart
void main() {
  // var：类型推断，可重新赋值
  var a = 10;
  a = 20; // OK

  // final：运行时常量，只能赋值一次
  final b = DateTime.now().second; // 运行时确定
  // b = 30; // 错误

  // const：编译时常量，值必须在编译时已知
  const c = 30 * 2; // 编译时计算
  // const d = DateTime.now().second; // 错误：不是编译时常量

  // const 的规范化
  const list1 = [1, 2, 3];
  const list2 = [1, 2, 3];
  print(identical(list1, list2)); // true，同一个对象

  final list3 = [1, 2, 3];
  final list4 = [1, 2, 3];
  print(identical(list3, list4)); // false，不同对象

  // 实例变量可以是 final，但不能是 const
  class Example {
    final x = 10; // OK
    // const y = 20; // 错误：const 不能用于实例变量
  }
}
```

---

### 6. 通配符变量

#### 概念
通配符变量使用下划线 `_` 声明一个局部变量或参数，表示“我不关心这个值”，该变量不可访问。同一作用域内可以存在多个 `_` 而不会冲突。

#### 原理
这是语法层面的占位符，编译器会将其视为一个不可用的标识符。主要用于明确表达“忽略此值”的意图，避免产生未使用变量的警告。

#### 使用方法
- 局部变量：
  ```dart
  var _ = 1;
  int _ = 2; // 不同变量，允许
  ```
- for 循环：
  ```dart
  for (var _ in list) { ... }
  ```
- catch 子句：
  ```dart
  try { ... } catch (_) { print('error'); }
  ```
- 函数参数：
  ```dart
  list.where((_) => true);
  void f(int _, String _) { ... } // 两个参数都忽略
  ```
- 泛型参数：
  ```dart
  class T<_> {}
  ```

#### 注意事项
- 仅能在局部作用域使用，不能在顶层或类成员中使用（因为这些地方可能影响库可见性）。
- 通配符变量的值无法读取，只能用于占位。
- 如果一个函数参数是通配符，调用时仍需传递参数，但函数体内无法使用该参数。

#### 代码示例
```dart
void main() {
  // 忽略返回值
  final _ = someFunction();

  // 忽略循环变量
  for (var _ in [1, 2, 3]) {
    print('hello');
  }

  // 忽略 catch 异常
  try {
    throw 'error';
  } catch (_) {
    print('caught');
  }
}

int someFunction() => 42;
```

---

## 二、内置类型

### 1. 数字类型：`num`、`int`、`double`

#### 概念
- **`num`**：数字类型的父类，包含 `int` 和 `double`。
- **`int`**：整数。平台相关（原生 64 位，Web 为 JavaScript 数字）。
- **`double`**：64 位双精度浮点数（IEEE 754）。

#### 原理
Dart 中所有类型都是对象，数字也不例外。`int` 和 `double` 分别实现了 `num` 接口。支持运算符重载（如 `+`、`-`）。整数位运算在 `int` 类中定义。

#### 使用方法
- **字面量**：
  ```dart
  int a = 1;
  int hex = 0xDEADBEEF;
  double b = 1.1;
  double exp = 1.42e5;
  ```
- **数字分隔符**（Dart 3.6+）：
  ```dart
  var large = 1_000_000;
  var mac = 0x00_14_22_01_23_45;
  ```
- **转换**：
  ```dart
  int.parse('42');               // String -> int
  double.parse('3.14');          // String -> double
  42.toString();                 // int -> String
  3.14159.toStringAsFixed(2);    // double -> String 保留两位小数
  ```
- **位运算**：
  ```dart
  assert((3 << 1) == 6);  // 左移
  assert((3 | 4) == 7);   // 按位或
  assert((3 & 4) == 0);   // 按位与
  ```

#### 注意事项
- 在 Web 平台上，`int` 和 `double` 都是 JavaScript 数字类型，但 `int` 限制为 -2^53 到 2^53-1 的整数范围。
- 算术运算结果可能产生 `double`，如 `3 / 2` 得到 `1.5`。若需整数除法，可用 `~/`（截断除法）。
- 数字字面量是编译时常量，包含算术运算（操作数为常量）也是常量。

#### 代码示例
```dart
void main() {
  num x = 1;        // 可以同时接受 int 和 double
  x = 2.5;          // OK

  int a = 10;
  int b = 3;
  print(a / b);     // 3.3333333333333335 (double)
  print(a ~/ b);    // 3 (int)

  // 检查 NaN
  double result = 0 / 0;
  print(result.isNaN); // true
}
```

---

### 2. 字符串（`String`）

#### 概念
Dart 字符串是 UTF-16 编码的不可变字符序列。可以通过字面量、插值、连接等方式创建。

#### 原理
字符串对象存储的是 UTF-16 代码单元序列。`==` 运算符比较的是内容是否相等，而非引用。字符串是不可变的，任何修改都会创建新字符串。

#### 使用方法
- **字面量**：单引号、双引号、三引号（多行）、`r` 原始字符串。
  ```dart
  var s1 = 'Single quotes';
  var s2 = "Double quotes";
  var s3 = '''Multi-line
  string''';
  var raw = r'No \n escape';
  ```
- **插值**：`${expression}` 或 `$identifier`。
  ```dart
  var name = 'Bob';
  print('Hello, $name!');            // Hello, Bob!
  print('Length: ${name.length}');   // Length: 3
  ```
- **连接**：
  ```dart
  var s = 'String ' 'concatenation'; // 自动连接
  var s2 = 'Hello ' + 'World';
  ```
- **常用方法**：`toUpperCase()`、`toLowerCase()`、`trim()`、`substring()`、`split()`、`contains()` 等。

#### 注意事项
- 字符串不可变，连接大量字符串时使用 `StringBuffer` 提高效率。
- 使用 `==` 比较内容，而非引用。
- 如需处理 Unicode 字符（如表情符号），建议使用 `characters` 包。
- 字符串字面量可以是编译时常量，只要插值表达式也是常量。

#### 代码示例
```dart
void main() {
  // 插值
  var age = 30;
  print('I am $age years old.');

  // 原始字符串
  var path = r'C:\Program Files\Dart';
  print(path); // 打印 C:\Program Files\Dart

  // 多行字符串
  var multiline = '''
  This is line 1
  This is line 2
  ''';
  print(multiline);

  // 使用 characters 包处理表情
  import 'package:characters/characters.dart';
  var emoji = '👨‍👩‍👧';
  print(emoji.characters.length); // 1
}
```

---

### 3. 布尔（`bool`）

#### 概念
布尔类型只有两个值：`true` 和 `false`，均为编译时常量。用于逻辑判断。

#### 原理
`bool` 是一个类，`true` 和 `false` 是该类的唯一实例。条件表达式（如 `if`、`while`）必须显式求值为布尔值。

#### 使用方法
```dart
bool isRaining = true;
bool isSunny = false;

if (isRaining) {
  print('Take an umbrella');
}
```

#### 注意事项
- Dart 的类型安全要求条件必须是 `bool` 类型，不能使用其他类型隐式转换（如 JavaScript 中的 `if (nonNull)`）。
- 必须显式检查：
  ```dart
  var name = '';
  if (name.isEmpty) { ... }   // 正确
  // if (name) { ... }        // 错误
  ```

#### 代码示例
```dart
void main() {
  var items = [];
  // 正确：显式检查
  if (items.isEmpty) {
    print('No items');
  }

  // 检查 null
  String? maybeName;
  if (maybeName != null) {
    print(maybeName.length);
  }

  // 检查 NaN
  double value = 0 / 0;
  if (value.isNaN) {
    print('Not a number');
  }
}
```

---

### 4. 其他内置类型

- **Records**（Dart 3.0+）：匿名不可变聚合类型，可包含多个命名字段或位置字段。
- **集合类型**：`List`、`Set`、`Map`（详见集合章节）。
- **Symbols**：表示标识符，如 `#radix`。
- **Null**：类型 `Null`，唯一值 `null`。
- **Function**：函数也是对象，类型为 `Function`。

---

### 5. 基本数据类型总结
Dart 中所有类型都是对象，基本数据类型也不例外。常用基本类型包括：
- **数字**：`int`、`double`、`num`
- **字符串**：`String`
- **布尔**：`bool`
- **记录**：`Record`（Dart 3.0+）
- **集合**：`List`、`Set`、`Map`
- **符号**：`Symbol`
- **空**：`Null`

### 6. 类型转换练习
#### 数字与字符串转换
```dart
void main() {
  // String → int
  int one = int.parse('1');
  // String → double
  double pi = double.parse('3.14');
  // int → String
  String oneStr = 1.toString();
  // double → String（保留两位小数）
  String piStr = 3.14159.toStringAsFixed(2); // '3.14'
  // 进制转换
  int hex = int.parse('DEADBEEF', radix: 16);
  print(hex.toRadixString(16)); // 'deadbeef'
}
```

#### 字符串与数字相互转换的风险
- 转换失败会抛出 `FormatException`，需用 `tryParse` 返回可空值。
```dart
void main() {
  int? value = int.tryParse('not a number');
  print(value); // null
}
```

#### 类型检查与转换
- 使用 `is` 进行类型检查，`as` 进行强制转换（通常配合 `is` 使用）。
```dart
void main() {
  Object obj = 'hello';
  if (obj is String) {
    print(obj.length); // 类型提升后可直接调用 String 方法
  }

  // 不安全的转换
  // String str = obj as String; // 如果 obj 不是 String 则抛出异常
}
```

---

## 三、类型推断与 `dynamic`/`Object`

### 概念
- **类型推断**：Dart 编译器根据上下文自动推断变量、泛型参数的类型。
- **`dynamic`**：放弃静态类型检查，任何操作在编译时都被允许，运行时可能失败。
- **`Object`**：所有非空类型的父类，可以进行静态类型检查。

### 原理
类型推断基于初始化表达式或上下文信息，推断出最具体的类型。`dynamic` 告诉编译器不进行静态检查，所有调用都视为合法。`Object` 则保留静态检查，只能调用 `Object` 的方法。

### 使用方法
- **类型推断示例**：
  ```dart
  var x = 3;               // int
  var y = 3.0;             // double
  var list = [1, 2, 3];    // List<int>
  var map = {'a': 1};      // Map<String, int>
  ```
- **泛型推断**：
  ```dart
  List<String> names = []; // 显式
  var names = <String>[];  // 类型推断为 List<String>
  ```
- **`dynamic` 使用**：
  ```dart
  dynamic a = 'hello';
  a = 123;      // OK
  a.someMethod(); // 编译通过，但运行时可能出错
  ```
- **`Object` 使用**：
  ```dart
  Object obj = 'hello';
  print(obj.toString()); // OK
  // print(obj.length);  // 编译错误：Object 无 length 属性
  ```

### 注意事项
- 优先使用类型推断，减少冗余类型标注，但不要牺牲可读性。
- 避免使用 `dynamic`，除非与 JavaScript 交互等特殊情况。
- `Object` 可用于需要存储任意类型但保持类型检查的场景。
- 使用 `is` 进行类型检查后可安全调用特定类型的方法：
  ```dart
  Object obj = 'hello';
  if (obj is String) {
    print(obj.length); // 此处 obj 被提升为 String
  }
  ```

### 代码示例
```dart
void main() {
  // 类型推断
  var value = 10;        // int
  // value = 'hello';    // 错误

  // dynamic
  dynamic dyn = 10;
  dyn = 'hello';         // OK
  print(dyn.length);     // 运行时 OK（dyn 现在是字符串）

  // Object
  Object obj = 10;
  // obj = 'hello';      // OK（赋值任意非空类型）
  // print(obj.length);  // 编译错误

  // 类型提升
  if (obj is String) {
    print(obj.length);   // OK
  }
}
```

---

### 4. 动态类型怎么用
`dynamic` 用于需要完全动态行为的场景，例如与 JavaScript 互操作、处理 JSON 数据（但通常建议使用 `Map<String, dynamic>` 结合类型检查）。使用 `dynamic` 会失去静态类型检查，应谨慎。

```dart
void main() {
  dynamic data = fetchJson(); // 假设返回动态数据
  print(data['name']); // 假设 data 是 Map，可能运行时出错

  // 更好的方式：使用 Map<String, dynamic> 并检查
  Map<String, dynamic> json = fetchJson();
  if (json['name'] is String) {
    print(json['name']);
  }
}
```

### 5. 理解类型系统
Dart 的类型系统是**静态类型**的，但在很多地方支持类型推断。类型系统的主要目标是在编译时捕获类型错误，同时保持灵活性。关键概念：
- **类型安全**：变量类型一旦确定，不能随意改变。
- **类型推断**：减少冗余，但推断结果一旦确定就固定。
- **类型层次**：所有非空类型继承自 `Object`，可空类型是 `Null` 与对应类型的联合。
- **可空性**：通过 `?` 标记，与空安全机制紧密集成。

---

## 四、空安全

### 概念
空安全是 Dart 2.12 引入的特性，旨在防止空引用错误。它通过类型系统区分可空类型和非空类型，确保非空变量永远不为 `null`。

### 原理
- **可空类型**：在类型后加 `?`，例如 `String?`，表示变量可以是 `null` 或该类型。
- **非空类型**：默认类型（不加 `?`）不能为 `null`。
- **类型提升**：通过条件判断，可将可空类型提升为非空类型。
- **空检查操作符**：`?.`（安全访问）、`!`（断言非空）、`??`（空合并）、`??=`（空赋值）。

### 使用方法
- **声明可空变量**：
  ```dart
  String? nullableName;
  nullableName = null;   // 允许
  ```
- **声明非空变量**：
  ```dart
  String name = 'Bob';   // 必须初始化
  ```
- **安全访问**：
  ```dart
  String? name;
  int? length = name?.length; // 如果 name 为 null，结果为 null
  ```
- **空合并**：
  ```dart
  String displayName = name ?? 'Guest';
  name ??= 'Guest';       // 如果 name 为 null，则赋值为 'Guest'
  ```
- **断言非空**：
  ```dart
  String? name;
  // String nonNull = name; // 错误
  String nonNull = name!;   // 运行时如果 name 为 null 则抛出异常
  ```
- **延迟初始化**：使用 `late` 声明非空变量但不立即初始化。
- **空感知扩展**（集合）：
  ```dart
  List<String>? extra;
  var all = ['dart', ...?extra]; // 如果 extra 为 null，不展开
  ```

### 注意事项
- 非空变量必须在使用前初始化，否则编译错误。
- 不能直接调用可空变量的方法或属性，除非使用 `?.` 或 `!`。
- `!` 应谨慎使用，只有确定变量不为 null 时才使用，否则引发异常。
- 空安全与 `late` 结合时，要确保 `late` 变量在使用前确实被赋值。
- 顶级变量和静态变量默认延迟初始化，但仍受空安全规则约束。

### 代码示例
```dart
void main() {
  // 可空类型
  String? maybeName;
  print(maybeName?.length); // null

  // 安全调用链
  String? name;
  String? upper = name?.toUpperCase(); // null

  // 空合并
  String display = name ?? 'Unknown';
  print(display); // Unknown

  // 断言非空（风险）
  // String definitely = name!; // 运行时抛出异常

  // 类型提升
  if (name != null) {
    print(name.length); // 此处 name 被提升为非空
  }

  // 空感知扩展
  List<int>? numbers;
  var list = [0, ...?numbers, 2];
  print(list); // [0, 2]

  // late 变量
  late String lateVar;
  // print(lateVar); // 运行时错误，未初始化
  lateVar = 'initialized';
  print(lateVar); // OK
}
```

---


### 5. Null Safety 机制详解
空安全在编译时通过静态分析确保非空变量不会为 `null`。其核心机制包括：
- **非空类型**：默认不可为 `null`，必须初始化。
- **可空类型**：显式允许 `null`，使用时需检查。
- **类型提升**：基于条件（如 `if (x != null)`）将可空类型提升为非空。
- **流分析**：Dart 编译器会跟踪变量的状态，识别赋值和检查路径。
- **严格初始化**：所有非空变量必须在声明或构造函数中初始化（通过初始化列表、参数或 `late`）。

### 6. 处理可空类型的更多技巧
#### 使用 `?.` 安全调用链
```dart
String? name;
int? length = name?.length; // 如果 name 为 null，结果为 null
```

#### 使用 `??` 提供默认值
```dart
String display = name ?? 'Guest';
```

#### 使用 `??=` 仅在 null 时赋值
```dart
name ??= 'Anonymous';
```

#### 使用 `!` 断言非空（仅当确定不为 null 时使用）
```dart
String definitely = name!; // 如果 name 为 null，抛出运行时异常
```

#### 可空类型的泛型使用
```dart
List<String?> nullableStrings = ['hello', null, 'world'];
for (var s in nullableStrings) {
  print(s?.toUpperCase()); // 安全调用
}
```

#### 空安全与类型提升的边界
```dart
void main() {
  String? nullable;
  if (nullable != null) {
    // 此处 nullable 被提升为 String
    print(nullable.length);
  }

  // 但以下情况不会提升：
  // 1. 变量可能被其他线程修改（Dart 单线程，不考虑）
  // 2. 变量是实例变量且不是 final（可能被其他方法修改）
  class Example {
    String? field;
    void method() {
      if (field != null) {
        // 不会提升，因为其他方法可能修改 field
        print(field!.length); // 必须用 ! 或局部变量
      }
    }
  }
}
```

#### 处理可空类型的实用模式
- **使用局部变量**：将可空实例变量赋值给局部变量，然后检查。
```dart
class Example {
  String? field;
  void method() {
    var local = field; // 类型为 String?
    if (local != null) {
      print(local.length); // local 被提升为非空
    }
  }
}
```
- **使用空安全的三元运算符**：
```dart
String? name;
String display = (name != null) ? name.toUpperCase() : 'N/A';
// 或使用 ?. 和 ??
String display = name?.toUpperCase() ?? 'N/A';
```
- **与集合操作结合**：
```dart
List<String?> items = ['a', null, 'c'];
var nonNullItems = items.where((e) => e != null).cast<String>(); // 转为非空列表
```

---

## 五、集合类型

### 1. `List` 列表

#### 概念
`List` 是有序的集合，可以通过索引访问元素。Dart 中的列表可以是固定长度或可增长长度，默认可增长。

#### 原理
列表是基于数组实现的可索引集合。当添加元素超出容量时，会重新分配内存。列表元素类型在创建时确定，支持泛型。

#### 使用方法
- **创建列表**：
  ```dart
  var list1 = [1, 2, 3];                    // 字面量，推断为 List<int>
  List<int> list2 = [];                      // 空列表，指定类型
  var list3 = <String>['a', 'b'];            // 指定泛型
  var list4 = List.filled(3, 'initial');     // 固定长度填充列表
  var list5 = List.generate(5, (i) => i * i); // 生成列表
  ```
- **访问元素**：
  ```dart
  var list = [1, 2, 3];
  print(list[0]);          // 1
  list[1] = 5;              // 修改
  ```
- **常用操作**：
  ```dart
  list.add(4);              // 添加单个
  list.addAll([5, 6]);      // 添加多个
  list.insert(1, 10);       // 在索引1插入
  list.remove(2);           // 移除值2
  list.removeAt(0);         // 移除索引0
  list.indexOf(3);          // 查找索引
  list.sort();              // 排序
  list.forEach(print);      // 遍历
  ```

#### 注意事项
- 列表索引从0开始。
- 列表是引用类型，复制时需要显式拷贝（如 `List.from(original)`）。
- 使用 `const` 创建编译时常量列表：`const [1, 2, 3]`，该列表不可变。

#### 代码示例
```dart
void main() {
  var fruits = ['apple', 'banana', 'orange'];
  fruits.add('grape');
  print(fruits); // [apple, banana, orange, grape]

  var numbers = List.generate(5, (i) => i);
  print(numbers); // [0, 1, 2, 3, 4]

  // 列表拷贝
  var copy = List.of(fruits); // 新列表
  copy[0] = 'pear';
  print(fruits[0]); // 仍为 apple，不受影响
}
```

---

### 2. `Set` 集合

#### 概念
`Set` 是无序且元素唯一的集合。常用于去重和集合运算（交集、并集等）。

#### 原理
基于哈希表实现，要求元素正确实现 `hashCode` 和 `==`。元素的唯一性由 `==` 和 `hashCode` 决定。

#### 使用方法
- **创建集合**：
  ```dart
  var set1 = {1, 2, 3};                   // 字面量，推断为 Set<int>
  Set<String> set2 = {};                   // 空集合，注意 {} 默认是 Map
  var set3 = <String>{'a', 'b'};           // 指定泛型
  var set4 = Set.from([1, 2, 3]);          // 从 Iterable 创建
  ```
- **常用操作**：
  ```dart
  set1.add(4);              // 添加
  set1.addAll([5, 6]);      // 批量添加
  set1.remove(2);           // 移除
  set1.contains(3);         // 检查存在
  var union = set1.union(set2);          // 并集
  var intersection = set1.intersection(set2); // 交集
  ```

#### 注意事项
- `{}` 默认创建 `Map`，空集合需指定类型：`<int>{}` 或 `Set<int>()`。
- 集合元素必须唯一，重复添加无效。
- 自定义对象需正确实现 `==` 和 `hashCode` 才能保证唯一性。

#### 代码示例
```dart
void main() {
  var setA = {1, 2, 3};
  var setB = {3, 4, 5};
  print(setA.union(setB));        // {1, 2, 3, 4, 5}
  print(setA.intersection(setB)); // {3}
  print(setA.difference(setB));   // {1, 2}

  // 去重
  var numbers = [1, 2, 2, 3, 3, 4];
  var unique = Set.of(numbers);
  print(unique); // {1, 2, 3, 4}
}
```

---

### 3. `Map` 映射

#### 概念
`Map` 是键值对集合，键唯一，值可重复。通常称为字典或哈希表。

#### 原理
基于哈希表实现，键必须正确实现 `hashCode` 和 `==`。插入、查找和删除操作平均时间复杂度为 O(1)。

#### 使用方法
- **创建映射**：
  ```dart
  var map1 = {'a': 1, 'b': 2};                // 字面量，Map<String, int>
  Map<String, int> map2 = {};                  // 空映射
  var map3 = <int, String>{1: 'one', 2: 'two'};// 指定泛型
  var map4 = Map.fromEntries([MapEntry('x', 10)]);
  ```
- **常用操作**：
  ```dart
  map1['c'] = 3;                // 添加或更新
  var value = map1['a'];         // 获取，不存在返回 null
  map1.remove('b');              // 移除
  map1.containsKey('a');         // 检查键
  map1.containsValue(2);         // 检查值
  map1.forEach((key, value) => print('$key: $value'));
  ```

#### 注意事项
- 访问不存在的键返回 `null`（即使值类型为非空，`Map` 查找结果类型是 `V?`）。
- 键必须唯一，后添加的会覆盖之前的值。
- 使用 `const` 创建编译时常量映射：`const {'a': 1}`。

#### 代码示例
```dart
void main() {
  var scores = {'Alice': 95, 'Bob': 87};
  scores['Charlie'] = 92;
  print(scores['Bob']); // 87
  print(scores['David']); // null

  // 遍历
  scores.forEach((name, score) {
    print('$name: $score');
  });

  // 获取所有键或值
  print(scores.keys.toList());   // [Alice, Bob, Charlie]
  print(scores.values.toList()); // [95, 87, 92]
}
```

---

### 4. 集合的高级特性

#### Spread 操作符（`...` 和 `...?`）
用于将集合元素展开到另一个集合中。
```dart
var list = [1, 2, 3];
var list2 = [0, ...list];         // [0, 1, 2, 3]
var nullableList;
var list3 = [0, ...?nullableList]; // [0]，如果 nullableList 为 null 则展开为空
```

#### Collection if 和 Collection for
允许在集合字面量中使用条件或循环动态生成元素。
```dart
var promoActive = true;
var nav = [
  'Home',
  'Furniture',
  'Plants',
  if (promoActive) 'Outlet'       // 条件添加
];

var listOfInts = [1, 2, 3];
var listOfStrings = [
  '#0',
  for (var i in listOfInts) '#$i' // 循环生成
];
```

#### 集合类型转换
- `list.toSet()` → 转换为 `Set`
- `set.toList()` → 转换为 `List`
- `map.entries` → 获取 `MapEntry` 的集合
- `List.from(iterable)`、`Map.fromIterable()` 等

#### 不可变集合
使用 `List.unmodifiable`、`Set.unmodifiable`、`Map.unmodifiable` 创建不可变视图。
```dart
final immutableList = List.unmodifiable([1, 2, 3]);
// immutableList[0] = 5; // 抛出异常
```

---

## 六、类型别名（`typedef`）

#### 概念
类型别名为现有类型定义一个替代名称，可以简化复杂类型签名，提高代码可读性。Dart 中的 `typedef` 不仅可用于函数类型，还可用于其他类型（Dart 2.13+）。

#### 原理
`typedef` 在编译时被替换为原类型，不创建新类型，只是别名。

#### 使用方法
- **函数类型别名**：
  ```dart
  typedef Compare<T> = int Function(T a, T b);
  int sort(int a, int b) => a - b;
  Compare<int> cmp = sort; // 使用别名
  ```
- **字段类型别名**（Dart 2.13+）：
  ```dart
  typedef IntList = List<int>;
  IntList il = [1, 2, 3];
  ```
- **泛型别名**：
  ```dart
  typedef JsonMap = Map<String, dynamic>;
  typedef MyList<T> = List<T>;
  ```

#### 注意事项
- 类型别名仅用于提高可读性，不增加运行时开销。
- 避免过度使用，以免混淆。
- 类声明本身不是别名，使用 `typedef` 定义函数类型时，通常用于函数参数或变量。

#### 代码示例
```dart
typedef StringTransformer = String Function(String);

String toUpperCase(String s) => s.toUpperCase();
String toLowerCase(String s) => s.toLowerCase();

void applyTransformer(String input, StringTransformer transformer) {
  print(transformer(input));
}

void main() {
  applyTransformer('Hello', toUpperCase); // HELLO
  applyTransformer('Hello', toLowerCase); // hello
}
```

---

## 七、记录类型（Records）

#### 概念
记录是一种匿名不可变的聚合类型，可以包含多个命名字段或位置字段。Dart 3.0 引入，用于轻量级数据组合。

#### 原理
记录是值类型，基于其字段结构自动生成 `hashCode` 和 `==`。记录类型由字段的类型和名称定义，没有显式的类声明。

#### 使用方法
- **位置字段**：
  ```dart
  var record = ('first', a: 2, b: true, 'last');
  print(record.$1); // 'first'
  print(record.a);  // 2
  print(record.$2); // 'last'（第三个位置字段）
  ```
- **命名字段**：
  ```dart
  var record = (name: 'Alice', age: 30);
  print(record.name); // Alice
  print(record.age);  // 30
  ```
- **类型注解**：
  ```dart
  (String, int) record = ('hello', 42);
  ({String name, int age}) person = (name: 'Bob', age: 25);
  ```
- **解构记录**：
  ```dart
  var (name, age) = ('Charlie', 28);
  print(name); // Charlie
  print(age);  // 28
  ```

#### 注意事项
- 记录是不可变的，字段不能重新赋值。
- 记录的类型由字段类型和字段名称决定，两个具有相同字段类型和名称的记录是同一类型。
- 记录可用于返回多个值，替代创建小型类。
- 记录支持相等性比较：具有相同字段（顺序、名称、值）的记录相等。

#### 代码示例
```dart
// 返回多个值
(String, int) getUserInfo() {
  return ('David', 32);
}

void main() {
  var (name, age) = getUserInfo();
  print('$name is $age years old');

  // 使用命名字段更清晰
  ({String name, int age}) user = (name: 'Eve', age: 28);
  print('${user.name} is ${user.age}');

  // 记录相等
  var point1 = (x: 1, y: 2);
  var point2 = (x: 1, y: 2);
  print(point1 == point2); // true
}
```

---

## 八、模式匹配（Patterns）

#### 概念
模式匹配是 Dart 3.0 引入的强大特性，允许根据值的结构进行匹配和解构。可用于变量声明、赋值、`switch` 语句等。

#### 原理
模式匹配在编译时转换为相应的类型检查和拆解代码。它结合了解构和类型检查，使代码更简洁。

#### 使用方法
- **解构模式**：
  ```dart
  var list = [1, 2, 3];
  var [a, b, c] = list;          // 解构列表
  print(a); // 1

  var (x, y) = (10, 20);         // 解构记录

  var map = {'key': 'value'};
  var {'key': value} = map;       // 解构映射
  print(value); // value
  ```
- **`switch` 表达式和语句**：
  ```dart
  String describe(dynamic value) => switch (value) {
        int i => '整数 $i',
        String s => '字符串 $s',
        (int x, int y) => '点 ($x, $y)',
        _ => '其他'
      };
  ```
- **类型检查和转换**：
  ```dart
  if (obj is (int, int)) {
    var (x, y) = obj;
    print('坐标 ($x, $y)');
  }
  ```
- **守卫子句**（`when`）：
  ```dart
  switch (pair) {
    case (int x, int y) when x == y:
      print('相等');
    case (int x, int y):
      print('不相等');
  }
  ```

#### 注意事项
- 模式匹配必须完备，否则可能抛出异常。
- 解构时变量名必须是有效的标识符。
- 列表模式要求元素数量匹配，可使用 `...` 表示剩余元素。
  ```dart
  var [first, ..., last] = [1, 2, 3, 4];
  print(first); // 1
  print(last);  // 4
  ```
- 映射模式解构时只匹配指定的键。

#### 代码示例
```dart
void main() {
  // 解构列表并忽略部分元素
  var [first, second, ...rest] = [10, 20, 30, 40];
  print(first); // 10
  print(second); // 20
  print(rest); // [30, 40]

  // switch 表达式
  Object obj = (1, 2);
  var message = switch (obj) {
    (int a, int b) => '坐标 ($a, $b)',
    [int a, int b] => '列表 [$a, $b]',
    { 'x': int x, 'y': int y } => '映射 x=$x, y=$y',
    _ => '未知'
  };
  print(message); // 坐标 (1, 2)

  // 守卫条件
  var point = (3, 4);
  switch (point) {
    case (int x, int y) when x == y:
      print('点在对角线上');
    case (int x, int y):
      print('点 ($x, $y) 不在对角线上');
  }
}
```
