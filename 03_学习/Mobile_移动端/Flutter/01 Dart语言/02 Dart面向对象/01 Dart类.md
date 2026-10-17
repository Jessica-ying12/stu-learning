#### 1. 类定义
##### 概念
- **是什么**：类是对象的蓝图或模板，定义了对象的成员（属性和方法）。Dart 中所有对象都是类的实例。
- **为什么需要**：类提供了一种组织代码的方式，将数据和操作封装在一起，支持面向对象编程。
- **解决什么问题**：实现代码复用、模块化、抽象和封装。

##### 原理
- **底层实现**：Dart 中的类在运行时由虚拟机管理，每个类都有一个类型对象，实例对象存储其字段和方法表。
- **工作流程**：通过 `class` 关键字定义类，可以包含实例变量、方法、构造函数等。创建对象时调用构造函数分配内存并初始化。
- **关键技术点**：类可以继承（单继承），可以实现多个接口，支持 mixin。

##### 使用方法
- **基本用法**：
```dart
class Point {
  double x = 0; // 实例变量
  double y = 0;

  void display() { // 实例方法
    print('($x, $y)');
  }
}
```
- **常见场景**：定义实体模型、服务类、工具类等。
- **最佳实践**：遵循单一职责原则，合理使用访问控制。

##### 注意事项
- **常见错误**：忘记初始化非空实例变量。
- **性能问题**：无。
- **兼容性**：类修饰符（如 `abstract`、`base`、`final`）可控制库的访问权限（需 Dart 2.17+）。

##### 代码示例
```dart
class Person {
  String name;
  int age;
  Person(this.name, this.age); // 构造函数
  void greet() => print('Hello, I\'m $name');
}
```

---

#### 2. 实例变量
##### 概念
- **是什么**：实例变量是定义在类中、每个对象独立拥有的数据成员。
- **为什么需要**：存储对象的状态。
- **解决什么问题**：实现对象属性的封装。

##### 原理
- **底层实现**：每个实例变量在内存中占用空间，对象创建时分配。非 `late` 变量在构造函数执行前初始化。
- **工作流程**：通过点号访问，隐式生成 getter，非 `final` 还生成 setter。
- **关键技术点**：可空类型、`late` 延迟初始化。

##### 使用方法
- **基本用法**：
```dart
class Point {
  double? x; // 可空，默认 null
  double y = 0; // 非空，必须初始化
}
```
- **常见场景**：存储对象属性，如用户姓名、坐标等。
- **最佳实践**：尽量使用 `final` 声明不可变属性；避免公开暴露可变内部状态。

##### 注意事项
- **常见错误**：未初始化非空实例变量导致编译错误。
- **性能问题**：`late` 变量有额外检查开销。
- **兼容性**：初始化表达式不能访问 `this`（非 `late`）。

##### 代码示例
```dart
class Profile {
  final String name; // 必须在构造函数初始化
  late DateTime createdAt; // 延迟初始化
  Profile(this.name) {
    createdAt = DateTime.now(); // 构造函数体赋值
  }
}
```

---

#### 3. 构造函数
##### 概念
- **是什么**：构造函数是用于创建和初始化类实例的特殊函数。
- **为什么需要**：确保对象创建时正确初始化。
- **解决什么问题**：控制对象创建过程，提供多种初始化方式。

##### 原理
- **底层实现**：构造函数调用时分配内存，按顺序执行初始化列表、父类构造函数、自身构造函数体。
- **工作流程**：使用类名调用，可带参数，可命名，可重定向。
- **关键技术点**：生成式构造函数、命名构造函数、常量构造函数、工厂构造函数、重定向构造函数。

##### 使用方法
- **基本用法**：
```dart
class Point {
  double x, y;
  Point(this.x, this.y); // 生成式构造函数
}
```
- **常见场景**：对象创建时设置初始值；提供多种创建方式（如从JSON）。
- **最佳实践**：使用初始化形式参数简化代码；用命名构造函数增强可读性。

##### 注意事项
- **常见错误**：在工厂构造函数中返回 `null`（不允许）。
- **性能问题**：常量构造函数可生成编译时常量，节省内存。
- **兼容性**：构造函数的 tear-off 功能需要 Dart 2.14+。

##### 代码示例
```dart
class Person {
  String name;
  int age;
  Person(this.name, this.age); // 生成式
  Person.guest() : this('Guest', 0); // 重定向
}
```

---

#### 4. 默认构造函数
##### 概念
- **是什么**：如果没有显式定义构造函数，Dart 会提供一个无参数的默认构造函数。
- **为什么需要**：保证类至少有一个构造函数可以创建对象。
- **解决什么问题**：简化简单类的定义。

##### 原理
- **底层实现**：编译器自动添加，调用父类的无参构造函数。
- **工作流程**：当使用 `ClassName()` 创建对象时，默认构造函数被调用。
- **关键技术点**：默认构造函数是生成式的。

##### 使用方法
- **基本用法**：
```dart
class Point {
  double x = 0, y = 0; // 声明时初始化
}
void main() {
  var p = Point(); // 使用默认构造函数
}
```
- **常见场景**：类不需要参数初始化，或者所有实例变量都有默认值。
- **最佳实践**：如果类需要初始化，显式定义构造函数比依赖默认构造函数更清晰。

##### 注意事项
- **常见错误**：当父类没有无参构造函数时，子类必须显式调用父类构造函数。
- **性能问题**：无。
- **兼容性**：始终存在，除非显式定义构造函数。

##### 代码示例
```dart
class Animal {
  String name = 'unknown';
}
void main() {
  var a = Animal(); // 默认构造函数
}
```

---

#### 5. 命名构造函数
##### 概念
- **是什么**：为类定义多个构造函数，通过 `ClassName.identifier` 形式调用。
- **为什么需要**：提供多种对象创建方式，提高代码可读性。
- **解决什么问题**：避免参数类型混淆，表达构造意图。

##### 原理
- **底层实现**：命名构造函数也是生成式构造函数，可以有初始化列表和重定向。
- **工作流程**：类似普通构造函数，但名字不同。
- **关键技术点**：不能继承，子类需显式实现。

##### 使用方法
- **基本用法**：
```dart
class Point {
  final double x, y;
  Point(this.x, this.y);
  Point.origin() : x = 0, y = 0; // 命名构造函数
}
```
- **常见场景**：从不同数据源创建对象（如从JSON、从默认值）。
- **最佳实践**：命名应清晰表达用途，如 `.fromJson()`、`.zero()`。

##### 注意事项
- **常见错误**：命名构造函数与类名冲突（不允许）。
- **性能问题**：无。
- **兼容性**：总是可用。

##### 代码示例
```dart
class User {
  final String name;
  final int age;
  User(this.name, this.age);
  User.fromJson(Map<String, dynamic> json)
      : name = json['name'],
        age = json['age'];
}
```

---

#### 6. 工厂构造函数
##### 概念
- **是什么**：使用 `factory` 关键字定义的构造函数，不一定会创建新实例。
- **为什么需要**：当需要返回缓存实例、子类实例，或执行复杂预处理时。
- **解决什么问题**：控制实例创建逻辑，实现单例、对象池等。

##### 原理
- **底层实现**：工厂构造函数可以返回任意对象（但不能为 `null`），不会自动分配内存。
- **工作流程**：调用时执行函数体，必须返回一个实例。
- **关键技术点**：不能访问 `this`，常用于从缓存返回对象。

##### 使用方法
- **基本用法**：
```dart
class Logger {
  final String name;
  static final _cache = <String, Logger>{};
  factory Logger(String name) {
    return _cache.putIfAbsent(name, () => Logger._internal(name));
  }
  Logger._internal(this.name);
}
```
- **常见场景**：单例模式、对象池、从JSON创建对象时进行验证。
- **最佳实践**：使用工厂构造函数返回子类实例或现有实例；避免在工厂中做耗时操作。

##### 注意事项
- **常见错误**：返回 `null` 会导致运行时错误。
- **性能问题**：缓存策略需考虑内存使用。
- **兼容性**：工厂构造函数可以重定向到其他构造函数（使用 `=` 语法）。

##### 代码示例
```dart
class Singleton {
  static final Singleton _instance = Singleton._internal();
  factory Singleton() => _instance;
  Singleton._internal();
}
```

---

#### 7. 常量构造函数
##### 概念
- **是什么**：使用 `const` 关键字定义的构造函数，用于创建编译时常量对象。
- **为什么需要**：创建不可变对象，且可以在编译时确定值，提高性能。
- **解决什么问题**：实现常量对象共享（相同常量对象是同一个实例）。

##### 原理
- **底层实现**：常量构造函数必须将所有实例变量设为 `final`，且在常量上下文中调用时生成编译时常量。
- **工作流程**：使用 `const` 关键字调用构造函数，多个相同参数调用返回同一实例。
- **关键技术点**：常量构造函数也可以在非 `const` 上下文中调用，此时创建非常量对象。

##### 使用方法
- **基本用法**：
```dart
class ImmutablePoint {
  final double x, y;
  const ImmutablePoint(this.x, this.y);
}
void main() {
  var p1 = const ImmutablePoint(1, 1);
  var p2 = const ImmutablePoint(1, 1);
  assert(identical(p1, p2)); // true
}
```
- **常见场景**：定义数学点、颜色、配置等不可变数据。
- **最佳实践**：优先使用常量构造函数创建不可变对象。

##### 注意事项
- **常见错误**：在常量构造函数中初始化非 `final` 变量会报错。
- **性能问题**：常量对象在编译时创建，减少运行时开销。
- **兼容性**：常量构造函数需满足所有字段为 `final` 且不能有 body。

##### 代码示例
```dart
class TextStyle {
  final double fontSize;
  final String fontFamily;
  const TextStyle(this.fontSize, this.fontFamily);
}
const style = TextStyle(14, 'Arial');
```

---

#### 8. 初始化列表
##### 概念
- **是什么**：在构造函数体执行前，用于初始化实例变量的表达式列表，用冒号 `:` 引入。
- **为什么需要**：在构造函数体运行前设置 final 字段或执行断言验证。
- **解决什么问题**：处理无法在参数列表中完成的初始化逻辑，如调用父类构造函数。

##### 原理
- **底层实现**：初始化列表中的表达式在构造函数体之前、父类构造函数调用之后执行（实际上是父类构造函数调用之前？需注意顺序：先初始化列表，然后父类构造函数，然后当前构造函数体）。
- **工作流程**：按照声明顺序执行初始化器，可以使用 `assert` 进行调试检查。
- **关键技术点**：可以访问构造函数参数，但不能访问 `this`。

##### 使用方法
- **基本用法**：
```dart
class Point {
  final double x;
  final double y;
  Point.fromJson(Map<String, double> json)
      : x = json['x']!,
        y = json['y']! {
    print('Point created: ($x, $y)');
  }
}
```
- **常见场景**：初始化 final 字段、调用父类构造函数、参数验证。
- **最佳实践**：使用初始化列表设置 final 字段，避免在构造函数体中赋值。

##### 注意事项
- **常见错误**：初始化列表中不能调用实例方法。
- **性能问题**：初始化列表中的复杂表达式可能影响性能，但通常可忽略。
- **兼容性**：总是可用。

##### 代码示例
```dart
class Person {
  final String name;
  final int age;
  Person.withValidation(this.name, this.age)
      : assert(age >= 0), // 验证
        print('Creating Person'); // 不允许，初始化列表不能有语句，只能表达式
}
```

---

#### 9. 重定向构造函数
##### 概念
- **是什么**：一个构造函数委托给同一类中的另一个构造函数，使用 `this` 调用。
- **为什么需要**：避免代码重复，提供多种构造方式但共享核心逻辑。
- **解决什么问题**：简化构造函数的实现。

##### 原理
- **底层实现**：重定向构造函数的函数体为空，调用目标构造函数后目标构造函数执行初始化。
- **工作流程**：参数传递给目标构造函数，然后执行目标构造函数的初始化。
- **关键技术点**：重定向构造函数可以有初始化列表吗？通常不能，因为重定向后由目标构造函数处理。

##### 使用方法
- **基本用法**：
```dart
class Point {
  double x, y;
  Point(this.x, this.y);
  Point.alongXAxis(double x) : this(x, 0); // 重定向
}
```
- **常见场景**：为常用参数组合提供快捷构造函数。
- **最佳实践**：重定向构造函数应简洁，不包含额外逻辑。

##### 注意事项
- **常见错误**：重定向构造函数不能有函数体，也不能有除重定向外的初始化列表。
- **性能问题**：无。
- **兼容性**：总是可用。

##### 代码示例
```dart
class Rectangle {
  double width, height;
  Rectangle(this.width, this.height);
  Rectangle.square(double size) : this(size, size);
}
```

---

#### 10. 实例方法
##### 概念
- **是什么**：定义在类中、操作实例数据的函数。
- **为什么需要**：为对象提供行为。
- **解决什么问题**：封装与对象相关的操作。

##### 原理
- **底层实现**：实例方法接收一个隐式的 `this` 参数，指向调用对象。
- **工作流程**：通过对象点号调用，方法内部可以访问实例变量和其他方法。
- **关键技术点**：可重写（虚方法），抽象方法必须在子类实现。

##### 使用方法
- **基本用法**：
```dart
class Point {
  double x, y;
  Point(this.x, this.y);
  double distanceTo(Point other) {
    var dx = x - other.x;
    var dy = y - other.y;
    return sqrt(dx*dx + dy*dy);
  }
}
```
- **常见场景**：业务逻辑、计算属性、工具方法。
- **最佳实践**：保持方法短小，遵循单一职责。

##### 注意事项
- **常见错误**：在方法中修改变量导致意外副作用。
- **性能问题**：方法调用有一定开销，但通常可忽略。
- **兼容性**：实例方法支持 `async`/`await`。

##### 代码示例
```dart
class Counter {
  int _count = 0;
  void increment() => _count++;
  int get count => _count;
}
```

---

#### 11. 类方法（静态方法）
##### 概念
- **是什么**：使用 `static` 关键字定义的方法，属于类本身而非实例。
- **为什么需要**：实现与类相关但不依赖实例数据的工具函数。
- **解决什么问题**：提供全局性的操作，如工厂方法、工具函数。

##### 原理
- **底层实现**：静态方法存储在类对象中，调用时无需传递 `this`。
- **工作流程**：通过类名直接调用，不能访问实例成员，但可以访问静态变量。
- **关键技术点**：静态方法可以用于构造函数以外的对象创建逻辑。

##### 使用方法
- **基本用法**：
```dart
class MathUtils {
  static double distanceBetween(Point a, Point b) {
    var dx = a.x - b.x;
    var dy = a.y - b.y;
    return sqrt(dx*dx + dy*dy);
  }
}
```
- **常见场景**：工具类、辅助函数、工厂方法。
- **最佳实践**：对于紧密相关的工具函数，使用静态方法；对于广泛使用的功能，考虑顶层函数。

##### 注意事项
- **常见错误**：在静态方法中访问实例变量。
- **性能问题**：静态方法调用比实例方法略快（无动态分派）。
- **兼容性**：静态方法可以被常量化，作为常量构造函数的参数。

##### 代码示例
```dart
class FileUtils {
  static Future<String> readFile(String path) async {
    return await File(path).readAsString();
  }
}
```

---

#### 12. Getter/Setter
##### 概念
- **是什么**：用于读取和写入对象属性的特殊方法，分别用 `get` 和 `set` 定义。
- **为什么需要**：提供对内部数据的受控访问，可添加验证或计算逻辑。
- **解决什么问题**：封装字段，实现计算属性，保持接口一致性。

##### 原理
- **底层实现**：getter/setter 像方法一样被调用，但语法上像直接访问字段。
- **工作流程**：当访问属性时，调用对应的 getter；赋值时调用 setter。
- **关键技术点**：每个实例变量隐式有 getter，非 `final` 还有 setter。可以自定义 getter/setter 替代字段。

##### 使用方法
- **基本用法**：
```dart
class Rectangle {
  double left, top, width, height;
  Rectangle(this.left, this.top, this.width, this.height);
  double get right => left + width;
  set right(double value) => left = value - width;
}
```
- **常见场景**：计算属性、数据验证、懒加载。
- **最佳实践**：避免在 getter 中执行耗时操作；setter 中可进行验证或触发更新。

##### 注意事项
- **常见错误**：getter 和 setter 命名与字段冲突，导致无限递归。
- **性能问题**：getter/setter 本质是方法调用，但有内联优化。
- **兼容性**：总是可用。

##### 代码示例
```dart
class Temperature {
  double _celsius;
  Temperature(this._celsius);
  double get fahrenheit => _celsius * 1.8 + 32;
  set fahrenheit(double value) => _celsius = (value - 32) / 1.8;
}
```

---

#### 13. 计算属性
##### 概念
- **是什么**：通过 getter/setter 实现的属性，其值不直接存储，而是通过计算得到。
- **为什么需要**：提供类似字段的访问语法，但背后有逻辑。
- **解决什么问题**：封装派生数据，避免存储冗余信息。

##### 原理
- **底层实现**：计算属性本质是 getter 方法，每次访问都执行计算。
- **工作流程**：每次访问时重新计算，不保存状态。
- **关键技术点**：可以只读（只有 getter）或读写（有 setter）。

##### 使用方法
- **基本用法**：见 Getter/Setter 示例。
- **常见场景**：面积、周长、全名等派生值。
- **最佳实践**：确保计算轻量，避免副作用；若计算复杂可考虑缓存。

##### 注意事项
- **常见错误**：计算属性依赖的值变化后未更新（因为不存储，始终实时计算）。
- **性能问题**：频繁访问时可能影响性能，需考虑缓存。
- **兼容性**：总是可用。

##### 代码示例
```dart
class Circle {
  double radius;
  Circle(this.radius);
  double get area => 3.14159 * radius * radius;
}
```

---

#### 14. 私有成员（以 _ 开头）
##### 概念
- **是什么**：Dart 中以下划线 `_` 开头的标识符表示库私有，仅在当前文件内可见。
- **为什么需要**：实现封装，隐藏内部实现细节。
- **解决什么问题**：控制访问权限，防止外部代码依赖内部实现。

##### 原理
- **底层实现**：Dart 没有传统的 `private` 关键字，而是通过库级别可见性实现。每个 Dart 文件都是一个库。
- **工作流程**：在标识符前加 `_`，则只能在定义它的库（文件）内访问。
- **关键技术点**：子类如果在同一文件内，可以访问父类的私有成员；不同文件则不行。

##### 使用方法
- **基本用法**：
```dart
class BankAccount {
  double _balance = 0; // 私有变量
  void deposit(double amount) => _balance += amount;
  double get balance => _balance;
}
```
- **常见场景**：隐藏内部状态，暴露受控接口。
- **最佳实践**：默认将变量设为私有，提供必要的 getter/setter；API 设计遵循最小暴露原则。

##### 注意事项
- **常见错误**：在不同文件尝试访问私有成员导致编译错误。
- **性能问题**：无。
- **兼容性**：始终可用。

##### 代码示例
```dart
// file1.dart
class Person {
  String _name; // 私有
  Person(this._name);
}
// file2.dart
import 'file1.dart';
void main() {
  var p = Person('Alice');
  print(p._name); // 错误：不能访问
}
```

---

#### 15. 抽象方法
##### 概念
- **是什么**：只有声明没有实现的方法，必须存在于抽象类或 mixin 中。
- **为什么需要**：定义接口规范，强制子类实现。
- **解决什么问题**：实现多态，让不同子类有不同行为。

##### 原理
- **底层实现**：抽象方法在编译时没有实现体，子类必须重写并提供实现。
- **工作流程**：子类继承抽象类或实现接口时，必须实现所有抽象方法。
- **关键技术点**：抽象类不能被实例化，但可以有具体方法。

##### 使用方法
- **基本用法**：
```dart
abstract class Animal {
  void makeSound(); // 抽象方法
}
class Dog extends Animal {
  void makeSound() => print('Woof');
}
```
- **常见场景**：定义接口、模板方法模式。
- **最佳实践**：用抽象类定义公共接口，用具体子类提供实现。

##### 注意事项
- **常见错误**：子类未实现抽象方法会编译错误。
- **性能问题**：抽象方法调用是虚方法调用，有少量开销。
- **兼容性**：总是可用。

##### 代码示例
```dart
abstract class Shape {
  double get area; // 抽象 getter
}
class Square extends Shape {
  final double side;
  Square(this.side);
  double get area => side * side;
}
```

---

#### 16. 操作符重载
##### 概念
- **是什么**：为类自定义运算符的行为，使用 `operator` 关键字。
- **为什么需要**：使自定义类型支持常用运算符，提升代码可读性。
- **解决什么问题**：让对象能像内置类型一样使用运算符（如 `+`、`==`）。

##### 原理
- **底层实现**：操作符重载本质是定义了一个特殊名称的实例方法。
- **工作流程**：当使用运算符时，编译器将其转换为对应的方法调用。
- **关键技术点**：必须重载允许的运算符（如 `+`、`-`、`==` 等），不能重载 `=`、`.` 等。

##### 使用方法
- **基本用法**：
```dart
class Vector {
  final int x, y;
  Vector(this.x, this.y);
  Vector operator +(Vector v) => Vector(x + v.x, y + v.y);
  @override
  bool operator ==(Object other) =>
      other is Vector && x == other.x && y == other.y;
}
```
- **常见场景**：数学向量、复数、矩阵等自定义数值类型。
- **最佳实践**：重载运算符时保持语义一致；通常同时重写 `hashCode`。

##### 注意事项
- **常见错误**：重载 `==` 但未重写 `hashCode` 导致集合行为异常。
- **性能问题**：无。
- **兼容性**：只能重载特定运算符列表。

##### 代码示例
```dart
class Fraction {
  final int numerator, denominator;
  Fraction(this.numerator, this.denominator);
  Fraction operator *(Fraction other) {
    return Fraction(numerator * other.numerator,
                    denominator * other.denominator);
  }
}
```

---

#### 17. 可调用对象（Callable objects）
##### 概念
- **是什么**：实现了 `call()` 方法的类，其实例可以像函数一样被调用。
- **为什么需要**：让对象具有函数的行为，可用于封装状态和行为。
- **解决什么问题**：模拟函数对象（仿函数），在需要函数的地方传递对象。

##### 原理
- **底层实现**：`call()` 方法允许使用 `obj()` 语法调用，编译器将其转为 `obj.call()`。
- **工作流程**：定义 `call` 方法，可以有任意参数和返回值。
- **关键技术点**：`call` 方法可以像普通方法一样重载，但通常只有一个。

##### 使用方法
- **基本用法**：
```dart
class Adder {
  final int addend;
  Adder(this.addend);
  int call(int value) => value + addend;
}
void main() {
  var add5 = Adder(5);
  print(add5(10)); // 15
}
```
- **常见场景**：策略模式、闭包替代、函数式编程。
- **最佳实践**：避免滥用，仅在需要函数对象时使用。

##### 注意事项
- **常见错误**：忘记定义 `call` 方法却尝试调用实例。
- **性能问题**：与普通方法调用无差异。
- **兼容性**：总是可用。

##### 代码示例
```dart
class Multiplier {
  final int factor;
  Multiplier(this.factor);
  int call(int x) => x * factor;
}
void main() {
  var double = Multiplier(2);
  print(double(5)); // 10
}
```

---

#### 18. 继承与构造函数调用
##### 概念
- **是什么**：子类不会继承父类的构造函数，但必须调用父类构造函数初始化继承的成员。
- **为什么需要**：确保父类状态正确初始化。
- **解决什么问题**：维护继承链的正确初始化顺序。

##### 原理
- **底层实现**：子类构造函数在初始化列表中调用父类构造函数（默认或显式）。
- **工作流程**：子类构造函数执行顺序：初始化列表 -> 父类构造函数 -> 子类构造函数体。
- **关键技术点**：如果父类没有无参构造函数，子类必须显式调用一个父类构造函数。

##### 使用方法
- **基本用法**：
```dart
class Person {
  String name;
  Person(this.name);
}
class Employee extends Person {
  int id;
  Employee(String name, this.id) : super(name); // 显式调用父类构造函数
}
```
- **常见场景**：子类扩展父类属性时。
- **最佳实践**：尽量使用 super 参数简化代码。

##### 注意事项
- **常见错误**：忘记调用父类构造函数，而父类没有默认构造函数。
- **性能问题**：无。
- **兼容性**：super 参数需要 Dart 2.17+。

##### 代码示例
```dart
class Vehicle {
  int speed;
  Vehicle(this.speed);
}
class Car extends Vehicle {
  int doors;
  Car(int speed, this.doors) : super(speed);
}
```

---

#### 19. Super 参数
##### 概念
- **是什么**：Dart 2.17+ 引入的特性，允许在子类构造函数参数中直接使用 `super.x` 将参数转发给父类构造函数。
- **为什么需要**：减少样板代码，避免手动传递参数给 `super`。
- **解决什么问题**：简化继承中的构造函数编写。

##### 原理
- **底层实现**：编译器自动将 `super.x` 参数传递给父类构造函数，相当于隐式调用 `super(x)`。
- **工作流程**：子类构造函数参数中的 `super.x` 表示该参数用于初始化父类的同名（或对应位置）字段。
- **关键技术点**：可以与普通参数混合，但必须注意避免重复传递。

##### 使用方法
- **基本用法**：
```dart
class Point {
  double x, y;
  Point(this.x, this.y);
}
class Point3D extends Point {
  double z;
  Point3D(super.x, super.y, this.z); // 自动转发 x, y 给父类
}
```
- **常见场景**：子类继承父类属性，只需添加新属性。
- **最佳实践**：当子类构造函数参数与父类字段一一对应时，优先使用 super 参数。

##### 注意事项
- **常见错误**：在同时使用 super 参数和显式 `super(...)` 调用时导致重复传递。
- **性能问题**：无。
- **兼容性**：需要 Dart 2.17 或更高版本。

##### 代码示例
```dart
class Animal {
  String name;
  Animal(this.name);
}
class Dog extends Animal {
  String breed;
  Dog(super.name, this.breed); // 等价于 Dog(name, this.breed) : super(name);
}
```

---
#### 20. 静态变量（类变量）
##### 概念
- **是什么**：使用 `static` 关键字声明的变量，属于类本身而非类的实例。所有实例共享同一个静态变量。
- **为什么需要**：存储类级别的状态或常量，避免为每个实例重复创建相同的数据。
- **解决什么问题**：实现全局状态（谨慎使用）、共享配置、缓存、常量定义。

##### 原理
- **底层实现**：静态变量存储在类的静态存储区，在第一次访问时被初始化（延迟初始化），而不是在类加载时。
- **工作流程**：通过类名直接访问（`ClassName.variableName`），无需创建实例。静态变量的初始化是线程安全的（Dart 是单线程模型，但 isolate 间独立）。
- **关键技术点**：静态变量可以是 `final` 或 `const`，常用于定义类常量。

##### 使用方法
- **基本用法**：
```dart
class Config {
  static const String appName = 'MyApp'; // 常量
  static int instanceCount = 0; // 可变静态变量
  Config() {
    instanceCount++; // 每次创建实例时计数
  }
}
void main() {
  print(Config.appName); // 通过类名访问
  print(Config.instanceCount); // 0
  var c1 = Config();
  var c2 = Config();
  print(Config.instanceCount); // 2
}
```
- **常见场景**：
  - 定义常量（如 `static const`）
  - 缓存实例（如工厂构造函数中的 `_cache`）
  - 计数器或全局状态
- **最佳实践**：
  - 优先使用 `const` 定义不可变常量
  - 避免滥用可变静态变量，它们会引入全局状态，增加测试和维护难度
  - 静态变量命名遵循 `lowerCamelCase`（虽然常量通常也用 `lowerCamelCase`，但 style 推荐如此）

##### 注意事项
- **常见错误**：在实例方法中错误地使用静态变量（可以访问，但需通过类名），或认为静态变量在实例方法中会自动绑定。
- **性能问题**：静态变量访问比实例变量稍快（无需通过实例），但差别微乎其微。
- **兼容性**：总是可用。

##### 代码示例
```dart
class Logger {
  static final Map<String, Logger> _cache = {}; // 私有静态缓存
  final String name;
  factory Logger(String name) {
    return _cache.putIfAbsent(name, () => Logger._internal(name));
  }
  Logger._internal(this.name);
}
```

---

#### 21. 抽象类
##### 概念
- **是什么**：使用 `abstract` 关键字修饰的类，不能被实例化。抽象类可以包含抽象方法和具体方法。
- **为什么需要**：定义一组相关的接口规范，作为其他类的基类。
- **解决什么问题**：提供公共行为的模板，强制子类实现特定方法，实现多态。

##### 原理
- **底层实现**：抽象类在编译时会被标记，禁止直接实例化。如果尝试 `new AbstractClass()` 会报编译错误。
- **工作流程**：子类继承抽象类，必须实现所有抽象方法（除非子类也是抽象的），然后才能被实例化。
- **关键技术点**：抽象类可以没有抽象方法（纯粹作为基类防止实例化），也可以有实例变量和具体方法。

##### 使用方法
- **基本用法**：
```dart
abstract class Shape {
  double get area; // 抽象 getter
  void describe() {
    print('This is a shape with area: $area');
  }
}
class Circle extends Shape {
  final double radius;
  Circle(this.radius);
  @override
  double get area => 3.14159 * radius * radius;
}
```
- **常见场景**：
  - 定义接口（类似 Java 的 interface，但 Dart 有单独的 `interface` 类修饰符）
  - 模板方法模式：在抽象类中定义算法骨架，子类实现具体步骤
  - 作为基类提供默认实现
- **最佳实践**：
  - 抽象类命名通常使用形容词或抽象名词（如 `Shape`、`Renderable`）
  - 如果只定义接口而无任何实现，考虑使用 `interface class`（Dart 3.0+）或 `mixin`

##### 注意事项
- **常见错误**：忘记实现抽象方法导致编译错误。
- **性能问题**：抽象方法调用是虚方法调用，有轻微开销。
- **兼容性**：抽象类始终可用。

##### 代码示例
```dart
abstract class Animal {
  void makeSound(); // 抽象方法
  void sleep() => print('Zzz'); // 具体方法
}
class Dog extends Animal {
  @override
  void makeSound() => print('Woof');
}
```

---

#### 22. 隐式接口
##### 概念
- **是什么**：每个类都隐式定义了一个接口，包含该类所有实例成员（方法、getter、setter）及其实现的接口中的成员。类可以通过 `implements` 关键字实现一个或多个隐式接口。
- **为什么需要**：提供一种方式让类遵循某个类的 API 但不继承其实现，实现代码复用和解耦。
- **解决什么问题**：允许多个类共享相同的行为规范，即使它们没有继承关系。

##### 原理
- **底层实现**：当一个类被用作接口时，它只提供成员签名，不提供实现。实现类必须提供所有成员的具体实现。
- **工作流程**：使用 `implements` 声明类实现一个或多个接口，然后为每个接口成员提供实现（可以重写）。
- **关键技术点**：接口可以多实现（用逗号分隔），但注意成员冲突需自行解决。

##### 使用方法
- **基本用法**：
```dart
class Person {
  String _name;
  Person(this._name);
  String greet(String who) => 'Hello $who, I\'m $_name';
}
class Impostor implements Person {
  String get _name => '';
  String greet(String who) => 'Hi $who, I\'m not real';
}
void main() {
  Person p = Impostor(); // 多态：Impostor 是 Person 接口的实现
  print(p.greet('Bob'));
}
```
- **常见场景**：
  - 当需要某个类具有另一个类的 API 但不想继承其实现时
  - 实现多个接口，模拟多重继承
  - 测试时提供 Mock 实现
- **最佳实践**：
  - 使用 `implements` 表示“是一个”的行为契约，而非“是一个”的实现继承
  - 接口应保持小而专注（接口隔离原则）

##### 注意事项
- **常见错误**：实现接口时遗漏某个成员导致编译错误。
- **性能问题**：接口方法调用是虚方法调用，与普通方法无异。
- **兼容性**：总是可用。Dart 3.0 引入了 `interface class` 关键字，可以声明一个类专门用作接口，防止被继承。

##### 代码示例
```dart
class Flyable {
  void fly() => print('Flying');
}
class Swimmable {
  void swim() => print('Swimming');
}
// Duck 实现两个接口
class Duck implements Flyable, Swimmable {
  @override
  void fly() => print('Duck flying');
  @override
  void swim() => print('Duck swimming');
}
```

---

#### 23. 类修饰符（Class modifiers）
##### 概念
- **是什么**：Dart 2.17+（以及 Dart 3.0+）引入的修饰符，用于控制类在其他库中的使用方式。包括 `abstract`、`base`、`final`、`interface`、`sealed`、`mixin` 等。
- **为什么需要**：提供更精细的封装控制，防止滥用继承或实现，增强库设计的安全性。
- **解决什么问题**：解决继承的脆弱性，明确类的预期用途（是可被继承、可被实现、还是只能实例化）。

##### 原理
- **底层实现**：编译器根据修饰符生成相应的访问控制规则，违反规则会在编译时报错。
- **工作流程**：在类声明前加上修饰符，限定其他库对该类的操作（继承、实现、实例化等）。
- **关键技术点**：
  - `abstract`：抽象类，不能实例化（但可以包含工厂构造）。
  - `base`：禁止在其他库中被实现（implements），只允许继承（extends）。强制子类也必须是 `base`、`final` 或 `sealed`。
  - `final`：禁止在其他库中被继承或实现，只能直接使用（实例化）。类本身可以包含工厂构造。
  - `interface`：禁止在其他库中被继承，只允许实现。实现类必须提供所有成员实现。
  - `sealed`：禁止在其他库中被继承或实现，但允许在同一文件内派生子类。常用于代数数据类型（ADT）。
  - `mixin`：专门用于声明 mixin 类，限制用法。

##### 使用方法
- **基本用法**：
```dart
// 库 a.dart
base class Animal { void move() {} }
final class Calculator { int add(int a, int b) => a + b; }
interface class Drawable { void draw() {} }
sealed class Result {} // 同一文件内可派生
class Success extends Result {}
class Error extends Result {}

// 库 b.dart
import 'a.dart';
class Dog extends Animal {} // 允许，因为 Animal 是 base
// class Cat implements Animal {} // 错误：不能实现 base 类
// class BetterCalc extends Calculator {} // 错误：final 类不能被继承
class Circle implements Drawable { void draw() {} } // 允许
// class OtherResult extends Result {} // 错误：sealed 类在不同文件不能继承
```
- **常见场景**：
  - 库作者控制 API 的扩展方式
  - 模式匹配与 `sealed` 类结合实现穷尽检查
  - 防止意外继承导致的安全问题
- **最佳实践**：
  - 默认情况下，如果不确定，可以不使用修饰符（允许任何用法）
  - 如果类设计为基类，使用 `base` 或 `abstract base`
  - 如果类只作为接口，使用 `interface`
  - 如果类不希望被扩展，使用 `final`
  - 使用 `sealed` 来模拟枚举的扩展（可枚举的子类型）

##### 注意事项
- **常见错误**：在不同库中违反修饰符规则导致编译错误。
- **性能问题**：无运行时影响，仅编译期检查。
- **兼容性**：需要 Dart 2.17+（部分修饰符）或 Dart 3.0+（如 `sealed`）。使用前请确认项目 SDK 版本。

##### 代码示例
```dart
// 定义一个密封类，表示网络请求状态
sealed class NetworkState {}
class Loading extends NetworkState {}
class Success<T> extends NetworkState {
  final T data;
  Success(this.data);
}
class Failure extends NetworkState {
  final String message;
  Failure(this.message);
}

// 使用 switch 表达式时，编译器可检查是否穷尽所有子类型
String handleState(NetworkState state) => switch (state) {
  Loading() => 'loading',
  Success(data: var d) => 'success: $d',
  Failure(message: var m) => 'failure: $m',
};
```

---

#### 23. 类修饰符（Class modifiers）
##### 概念
- **是什么**：Dart 2.17+（以及 Dart 3.0+）引入的修饰符，用于控制类在其他库中的使用方式。包括 `abstract`、`base`、`final`、`interface`、`sealed`、`mixin` 等。
- **为什么需要**：提供更精细的封装控制，防止滥用继承或实现，增强库设计的安全性。
- **解决什么问题**：解决继承的脆弱性，明确类的预期用途（是可被继承、可被实现、还是只能实例化）。

##### 原理
- **底层实现**：编译器根据修饰符生成相应的访问控制规则，违反规则会在编译时报错。
- **工作流程**：在类声明前加上修饰符，限定其他库对该类的操作（继承、实现、实例化等）。
- **关键技术点**：
  - `abstract`：抽象类，不能实例化（但可以包含工厂构造）。
  - `base`：禁止在其他库中被实现（implements），只允许继承（extends）。强制子类也必须是 `base`、`final` 或 `sealed`。
  - `final`：禁止在其他库中被继承或实现，只能直接使用（实例化）。类本身可以包含工厂构造。
  - `interface`：禁止在其他库中被继承，只允许实现。实现类必须提供所有成员实现。
  - `sealed`：禁止在其他库中被继承或实现，但允许在同一文件内派生子类。常用于代数数据类型（ADT），配合模式匹配可以实现穷尽性检查。

##### 使用方法
- **基本用法**：
```dart
// 库 a.dart
base class Animal { void move() {} }
final class Calculator { int add(int a, int b) => a + b; }
interface class Drawable { void draw() {} }
sealed class Result {} // 同一文件内可派生
class Success extends Result {}
class Error extends Result {}

// 库 b.dart
import 'a.dart';
class Dog extends Animal {} // 允许，因为 Animal 是 base
// class Cat implements Animal {} // 错误：不能实现 base 类
// class BetterCalc extends Calculator {} // 错误：final 类不能被继承
class Circle implements Drawable { void draw() {} } // 允许
// class OtherResult extends Result {} // 错误：sealed 类在不同文件不能继承
```
- **常见场景**：
  - 库作者控制 API 的扩展方式
  - 模式匹配与 `sealed` 类结合实现穷尽检查
  - 防止意外继承导致的安全问题
- **最佳实践**：
  - 默认情况下，如果不确定，可以不使用修饰符（允许任何用法）
  - 如果类设计为基类，使用 `base` 或 `abstract base`
  - 如果类只作为接口，使用 `interface`
  - 如果类不希望被扩展，使用 `final`
  - 使用 `sealed` 来模拟枚举的扩展（可枚举的子类型）

##### 注意事项
- **常见错误**：在不同库中违反修饰符规则导致编译错误。
- **性能问题**：无运行时影响，仅编译期检查。
- **兼容性**：需要 Dart 2.17+（部分修饰符）或 Dart 3.0+（如 `sealed`）。使用前请确认项目 SDK 版本。

##### 代码示例
```dart
// 定义一个密封类，表示网络请求状态
sealed class NetworkState {}
class Loading extends NetworkState {}
class Success<T> extends NetworkState {
  final T data;
  Success(this.data);
}
class Failure extends NetworkState {
  final String message;
  Failure(this.message);
}

// 使用 switch 表达式时，编译器可检查是否穷尽所有子类型
String handleState(NetworkState state) => switch (state) {
  Loading() => 'loading',
  Success(data: var d) => 'success: $d',
  Failure(message: var m) => 'failure: $m',
};
```

---

#### 24. Mixin
##### 概念
- **是什么**：Mixin 是一种在多个类层次结构中重用代码的方法。使用 `mixin` 关键字定义，使用 `with` 关键字引入。
- **为什么需要**：实现代码的横向复用，解决单一继承无法多继承代码的问题。
- **解决什么问题**：将通用的方法和属性组合到一起，供不相关的类使用，同时避免多重继承的复杂性。

##### 原理
- **底层实现**：Mixin 不是继承，而是将 mixin 中的实现复制（组合）到目标类中。如果多个 mixin 有冲突，后面的覆盖前面的。
- **工作流程**：类使用 `with` 关键字引入一个或多个 mixin，从而获得 mixin 中的所有成员。
- **关键技术点**：mixin 不能有构造函数，不能用于实例化。可以使用 `on` 关键字指定只有特定类的子类才能使用该 mixin。

##### 使用方法
- **基本用法**：
```dart
mixin Logger {
  void log(String msg) => print('Log: $msg');
}

mixin JsonSerializable {
  String toJson() => '{"type": "$runtimeType"}';
}

// 使用 with 引入多个 mixin
class User with Logger, JsonSerializable {
  String name;
  User(this.name);
}

void main() {
  var user = User('Alice');
  user.log('User created'); // 可以使用 Logger 的方法
  print(user.toJson());      // 可以使用 JsonSerializable 的方法
}
```
- **常见场景**：
  - 在 Flutter 中监听组件生命周期
  - 为多个不相关的类添加相同功能（如日志、缓存）
  - 实现插件式架构
- **最佳实践**：
  - 优先使用 `mixin` 关键字而不是 `class` 来定义 mixin
  - 使用 `on` 关键字限制 mixin 的使用范围，避免滥用
  - Mixin 应保持功能单一，避免过于庞大

##### 注意事项
- **常见错误**：多个 mixin 中存在同名方法，顺序靠后的 mixin 覆盖前者。
- **性能问题**：与普通方法调用无异。
- **兼容性**：Dart 2.1.0 开始支持 `mixin` 关键字。

##### 代码示例
```dart
// 限制只有 State 的子类才能使用 LogStateMixin
mixin LogStateMixin<T extends StatefulWidget> on State<T> {
  @override
  void initState() {
    super.initState();
    print('${runtimeType} initState');
  }
  
  @override
  void dispose() {
    print('${runtimeType} dispose');
    super.dispose();
  }
}

class MyWidgetState extends State<MyWidget> with LogStateMixin<MyWidget> {
  // 无需额外代码，自动拥有生命周期日志
}
```

---

#### 25. 扩展方法（Extension methods）
##### 概念
- **是什么**：允许向现有类添加新功能，而无需修改原类或创建子类。
- **为什么需要**：扩展现有库或系统类的功能，尤其是在无法修改原代码的情况下。
- **解决什么问题**：解决需要为已有类型添加方法但又不想改变其原始定义的场景。

##### 原理
- **底层实现**：扩展方法是静态的、编译时的语法糖。编译器将扩展方法的调用转换为对静态函数的调用。
- **工作流程**：定义扩展时指定 `on` 类型，然后为该类型添加方法。使用时只要导入扩展所在的库，就可以像调用实例方法一样调用扩展方法。
- **关键技术点**：扩展不能重写现有方法，不能访问私有成员。

##### 使用方法
- **基本用法**：
```dart
extension StringExtensions on String {
  bool get isEmail {
    return RegExp(r'^[\w-\.]+@([\w-]+\.)+[\w-]{2,4}$').hasMatch(this);
  }
  
  String reverse() => split('').reversed.join('');
}

void main() {
  print('test@example.com'.isEmail); // true
  print('hello'.reverse()); // 'olleh'
}
```
- **常见场景**：
  - 为系统类（如 String、List）添加工具方法
  - 在 Flutter 中为 BuildContext 添加导航简化方法
  - 第三方库为已有类提供扩展而不破坏原有结构
- **最佳实践**：
  - 命名扩展以避免冲突，尤其是当多个扩展有同名方法时
  - 扩展方法应保持与原类功能的相关性

##### 注意事项
- **常见错误**：扩展方法不能访问私有成员，试图访问会报错。
- **性能问题**：无额外开销，编译时解析。
- **兼容性**：Dart 2.7.0 开始支持。

##### 代码示例
```dart
extension ListExtension<T> on List<T> {
  T? get firstOrNull => isEmpty ? null : first;
  
  List<T> operator *(int times) {
    return List.generate(times * length, (i) => this[i % length]);
  }
}

void main() {
  print([1, 2, 3] * 2); // [1, 2, 3, 1, 2, 3]
}
```

---

#### 26. 级联操作（Cascade notation）
##### 概念
- **是什么**：使用 `..` 操作符，允许在同一个对象上连续调用多个方法或设置多个属性。
- **为什么需要**：简化对同一对象进行多次操作的代码，避免重复书写对象引用。
- **解决什么问题**：实现流畅接口（Fluent interface），提高代码可读性。

##### 原理
- **底层实现**：级联操作不是方法调用链，而是语法糖。`..` 左边的表达式求值一次，然后依次执行右侧的操作，最后返回原始对象。
- **工作流程**：`对象..方法1()..方法2()..属性=值`，每个 `..` 操作都作用于同一个对象，并返回该对象本身。
- **关键技术点**：级联可以嵌套使用。如果希望级联操作返回的不是原对象，可以使用 `...`（空级联）？实际上 Dart 中只有 `..`。

##### 使用方法
- **基本用法**：
```dart
class Person {
  String name = '';
  int age = 0;
  void sayHello() => print('Hello, I\'m $name');
}

void main() {
  Person person = Person()
    ..name = 'Bob'
    ..age = 30
    ..sayHello();
}
```
- **常见场景**：
  - 对象初始化时设置多个属性
  - Flutter 中配置 Widget 属性
  - 构建器模式的简化
- **最佳实践**：
  - 级联操作应与创建对象结合使用，使代码更加紧凑
  - 避免在级联中执行有副作用但返回值无关紧要的操作

##### 注意事项
- **常见错误**：误将 `..` 写成 `.`，导致方法返回值而不是原对象。
- **性能问题**：无。
- **兼容性**：总是可用。

##### 代码示例
```dart
var sb = StringBuffer()
  ..write('Hello, ')
  ..write('world!')
  ..write(' This is Dart.');
print(sb.toString()); // Hello, world! This is Dart.
```

---

#### 27. 枚举类（Enum）
##### 概念
- **是什么**：使用 `enum` 关键字定义的特殊的类，表示固定数量的常量值。
- **为什么需要**：表示一组预定义的、有名字的常量，如星期、方向、状态等。
- **解决什么问题**：增强代码可读性和类型安全，避免使用字符串或整数表示状态。

##### 原理
- **底层实现**：在 Dart 中，枚举是类，每个枚举值都是该类的实例。枚举可以有字段、方法、构造函数（需为 `const`）。
- **工作流程**：定义枚举时列出所有可能的值，每个值自动获得 `index` 属性和 `values` 列表。
- **关键技术点**：Dart 2.17+ 支持增强型枚举（Enhanced enums），可以有字段、方法和实现接口。

##### 使用方法
- **基本用法**：
```dart
enum Color { red, green, blue }

void main() {
  print(Color.red.index); // 0
  print(Color.values); // [Color.red, Color.green, Color.blue]
  
  Color c = Color.red;
  switch (c) {
    case Color.red: print('Red'); break;
    case Color.green: print('Green'); break;
    case Color.blue: print('Blue'); break;
  }
}
```
- **增强枚举**：
```dart
enum Planet {
  mercury(3.3e23, 2.4e6),
  venus(4.87e24, 6.05e6),
  earth(5.97e24, 6.37e6);

  final double mass; // 质量
  final double radius; // 半径
  
  const Planet(this.mass, this.radius);
  
  double get density => mass / (4/3 * 3.14159 * radius * radius * radius);
}
```
- **常见场景**：
  - 表示状态机状态
  - 定义错误码
  - 固定选项的集合
- **最佳实践**：
  - 枚举值命名使用 `lowerCamelCase`
  - 当需要关联额外数据时使用增强枚举

##### 注意事项
- **常见错误**：在增强枚举中构造函数未声明为 `const`。
- **性能问题**：无。
- **兼容性**：增强枚举需要 Dart 2.17+。

##### 代码示例
```dart
enum ResultStatus { success, error, loading }

class ApiResult<T> {
  final ResultStatus status;
  final T? data;
  final String? message;
  
  const ApiResult.success(this.data) : status = ResultStatus.success, message = null;
  const ApiResult.error(this.message) : status = ResultStatus.error, data = null;
  const ApiResult.loading() : status = ResultStatus.loading, data = null, message = null;
}
```

---

#### 28. Typedef（类型别名）
##### 概念
- **是什么**：为函数类型或类类型定义一个别名，使用 `typedef` 关键字。
- **为什么需要**：简化复杂类型签名，提高代码可读性和可维护性。
- **解决什么问题**：当一个函数类型被多处使用时，使用 typedef 可以避免重复书写相同的函数签名。

##### 原理
- **底层实现**：typedef 只是编译时的类型别名，不产生新类型，但提供类型检查。
- **工作流程**：定义 typedef 后，可以像使用普通类型一样使用它声明变量或参数。
- **关键技术点**：Dart 2.13+ 支持非函数类型的 typedef（如类类型别名）。

##### 使用方法
- **基本用法**：
```dart
// 函数类型别名
typedef Compare<T> = int Function(T a, T b);

void sort<T>(List<T> list, Compare<T> compare) {
  // 实现排序
}

void main() {
  Compare<int> intCompare = (a, b) => a - b;
  sort([3, 1, 2], intCompare);
}
```
- **非函数类型别名**：
```dart
typedef IntList = List<int>;
IntList numbers = [1, 2, 3]; // 相当于 List<int> numbers = [1, 2, 3];
```
- **常见场景**：
  - 定义回调函数类型
  - 为复杂的泛型类型创建简短名称
  - 在集合或映射中使用时简化代码
- **最佳实践**：
  - 当一个函数类型在代码中重复出现时，使用 typedef
  - 为回调函数命名，使其用途更清晰

##### 注意事项
- **常见错误**：在 Dart 2 之前 typedef 只能用于函数类型，但 Dart 2.13+ 已支持任意类型别名。
- **性能问题**：无。
- **兼容性**：非函数类型别名需要 Dart 2.13+。

##### 代码示例
```dart
// 函数类型别名
typedef LoggerCallback = void Function(String message);

class Logger {
  LoggerCallback? onLog;
  
  void log(String msg) {
    print(msg);
    onLog?.call(msg);
  }
}
```

---

#### 29. NoSuchMethod 方法
##### 概念
- **是什么**：当对象尝试调用不存在的方法或访问不存在的属性时，`noSuchMethod` 方法会被调用。
- **为什么需要**：提供动态响应未定义成员的能力，实现代理模式或模拟对象。
- **解决什么问题**：在不改变类定义的情况下，优雅地处理对不存在的成员的调用。

##### 原理
- **底层实现**：当调用对象的方法或访问属性时，如果查找失败，Dart VM 会调用对象的 `noSuchMethod` 方法。
- **工作流程**：要使用 `noSuchMethod`，类必须重写 `Object` 中的 `noSuchMethod` 方法，并使用 `@override` 注解。同时，通常需要声明类具有 `dynamic` 类型的成员或实现一些接口来避免静态检查错误。
- **关键技术点**：`noSuchMethod` 接收一个 `Invocation` 对象，包含调用的方法名、参数等信息。

##### 使用方法
- **基本用法**：
```dart
class DynamicProxy {
  final Map<String, dynamic> _properties = {};
  
  @override
  void noSuchMethod(Invocation invocation) {
    if (invocation.isMethod) {
      print('Method ${invocation.memberName} called with $invocation');
    } else if (invocation.isGetter) {
      var name = invocation.memberName.toString().substring(1, -1);
      if (_properties.containsKey(name)) {
        return _properties[name];
      }
    } else if (invocation.isSetter) {
      var name = invocation.memberName.toString().substring(1, -1);
      _properties[name] = invocation.positionalArguments.first;
    }
    super.noSuchMethod(invocation); // 或者返回适当的默认值
  }
}

void main() {
  dynamic proxy = DynamicProxy();
  proxy.name = 'Dart'; // 调用 setter，由 noSuchMethod 处理
  print(proxy.name);   // 调用 getter，由 noSuchMethod 处理
}
```
- **常见场景**：
  - 实现动态代理模式
  - 创建模拟对象用于测试
  - JSON 序列化的灵活处理
- **最佳实践**：
  - 仅在必要时使用，优先考虑静态类型安全
  - 在文档中说明类的动态行为

##### 注意事项
- **常见错误**：未将变量声明为 `dynamic` 或未实现接口，导致静态类型检查阻止编译。
- **性能问题**：`noSuchMethod` 调用比普通方法调用慢，有反射开销。
- **兼容性**：总是可用。

##### 代码示例
```dart
class MockSpy implements dynamic {
  @override
  void noSuchMethod(Invocation invocation) {
    print('Intercepted: $invocation');
  }
}

void main() {
  var mock = MockSpy();
  mock.someMethod(1, 2, 3); // 不会报错，会打印 Intercepted...
}
```

---

#### 30. CopyWith 模式
##### 概念
- **是什么**：一种常见的编码模式，特别是在不可变类中，用于创建一个新对象，该对象是原对象的副本，但某些属性被更新。
- **为什么需要**：当使用不可变对象时（所有字段为 `final`），无法直接修改属性，需要通过创建新对象来更新。
- **解决什么问题**：提供一种便捷的方式来修改不可变对象的某些属性，同时保留其他属性不变。

##### 原理
- **底层实现**：在类中定义一个 `copyWith` 方法，该方法接收所有或部分属性的可选参数，返回一个新的实例，新实例的参数值是原值与传入新值的组合。
- **工作流程**：调用 `copyWith` 时，传入要修改的属性，其他属性自动沿用原对象的值。
- **关键技术点**：通常配合命名构造或默认参数实现。

##### 使用方法
- **基本用法**：
```dart
class User {
  final String name;
  final int age;
  final String email;
  
  const User({required this.name, required this.age, required this.email});
  
  User copyWith({String? name, int? age, String? email}) {
    return User(
      name: name ?? this.name,
      age: age ?? this.age,
      email: email ?? this.email,
    );
  }
}

void main() {
  final user = User(name: 'Alice', age: 30, email: 'alice@example.com');
  final updatedUser = user.copyWith(age: 31);
  print(updatedUser); // User(name: Alice, age: 31, email: alice@example.com)
}
```
- **常见场景**：
  - 不可变数据模型（如 DTO、实体）
  - Flutter 中 State 的更新
  - Redux 或 BLoC 模式中的状态管理
- **最佳实践**：
  - 类中所有字段为 `final`
  - `copyWith` 参数应为可选参数，默认值为 `null`
  - 考虑使用 `@immutable` 注解标记不可变类

##### 注意事项
- **常见错误**：忘记处理复杂类型（如列表）的深拷贝。
- **性能问题**：创建新对象有开销，但在现代 Dart 中通常可接受。
- **兼容性**：总是可用。

##### 代码示例
```dart
@immutable
class AppState {
  final int counter;
  final List<String> logs;
  
  const AppState({required this.counter, required this.logs});
  
  AppState copyWith({int? counter, List<String>? logs}) {
    return AppState(
      counter: counter ?? this.counter,
      logs: logs ?? this.logs, // 注意：这是浅拷贝，列表内容不变
    );
  }
  
  // 如果需要对列表进行深拷贝
  AppState copyWithDeep({int? counter, List<String>? logs}) {
    return AppState(
      counter: counter ?? this.counter,
      logs: logs != null ? List.of(logs) : List.of(this.logs),
    );
  }
}
```

---

#### 31. 泛型类
##### 概念
- **是什么**：使用类型参数定义的类，在实例化时指定具体的类型。
- **为什么需要**：编写可以处理多种类型的代码，同时保持类型安全。
- **解决什么问题**：避免为每种类型重复编写相似的代码，实现代码复用和抽象。

##### 原理
- **底层实现**：泛型在 Dart 中是类型安全的，运行时类型信息会被保留（reified），但可以通过类型擦除理解？实际上 Dart 保留了运行时类型信息。
- **工作流程**：在类定义中使用 `<T>` 作为占位符，实例化时传入具体类型。
- **关键技术点**：可以使用类型约束（如 `<T extends SomeType>`）限制可用的类型。

##### 使用方法
- **基本用法**：
```dart
class Box<T> {
  T value;
  Box(this.value);
  
  T getValue() => value;
  void setValue(T newValue) => value = newValue;
}

void main() {
  var intBox = Box<int>(123);
  var stringBox = Box<String>('Hello');
  
  int val = intBox.getValue(); // 类型安全，不需要强制转换
}
```
- **类型约束**：
```dart
class NumberBox<T extends num> {
  T value;
  NumberBox(this.value);
  
  double square() => value * value; // 可以调用 num 的方法
}
```
- **常见场景**：
  - 集合类型（List、Map、Set）的实现
  - 数据结构和算法库
  - 仓储模式（Repository）
- **最佳实践**：
  - 使用单个字母命名类型参数，如 `E`（元素）、`T`（类型）、`K`（键）、`V`（值）
  - 尽量使用泛型而不是 `dynamic` 以保持类型安全

##### 注意事项
- **常见错误**：忘记指定类型参数，默认为 `dynamic`，失去类型安全。
- **性能问题**：泛型在编译时处理，无运行时开销。
- **兼容性**：总是可用。

##### 代码示例
```dart
class Cache<T> {
  final Map<String, T> _store = {};
  
  void put(String key, T value) => _store[key] = value;
  T? get(String key) => _store[key];
  
  void putAll(Map<String, T> items) => _store.addAll(items);
}

// 使用 extends 约束
class ComparableCache<T extends Comparable<T>> {
  T? max() {
    if (_store.isEmpty) return null;
    return _store.values.reduce((a, b) => a.compareTo(b) > 0 ? a : b);
  }
}
```

---

#### 32. 扩展类型别名（Extension type）
##### 概念
- **是什么**：Dart 3.3+ 引入的特性，使用 `extension type` 关键字定义，包装现有类型以提供更窄的接口或添加静态检查，但几乎没有运行时开销。
- **为什么需要**：为现有类型创建一个零开销的包装器，提供类型安全和更专注的 API，而不引入额外的对象分配。
- **解决什么问题**：解决使用原始类型不够语义化的问题（如 `int` 表示 ID 容易混淆），同时避免包装类的性能开销。

##### 原理
- **底层实现**：扩展类型在编译时被擦除，编译后被视为其表示类型，因此零开销。
- **工作流程**：定义扩展类型时指定表示类型（representation type），它可以提供新的方法，但不能访问原始类型的成员（除非通过转发）。
- **关键技术点**：扩展类型不能有额外的字段（除了表示字段），可以有方法、getter、setter 和操作符重载。

##### 使用方法
- **基本用法**：
```dart
extension type IdNumber(int value) {
  // 可以添加方法
  String get asFormatted => 'ID: $value';
  
  // 可以添加操作符
  bool operator <(IdNumber other) => value < other.value;
  
  // 可以添加工厂构造
  factory IdNumber.fromString(String s) => IdNumber(int.parse(s));
}

void main() {
  IdNumber id = IdNumber(42);
  print(id.asFormatted); // ID: 42
  
  // int value = id; // 错误：不能直接赋值给 int
  int value = id.value; // 需要显式访问表示字段
}
```
- **转发成员**：
```dart
extension type Email(String _value) implements String {
  bool get isValid => _value.contains('@');
}

void main() {
  Email email = Email('test@example.com');
  print(email.isValid); // true
  print(email.length); // 因为 implements String，可以调用 String 的方法
}
```
- **常见场景**：
  - 为原始类型创建语义化类型（如 Email、PhoneNumber）
  - 提供更专注的 API，隐藏不必要的原始类型方法
  - 在不影响性能的情况下进行类型验证
- **最佳实践**：
  - 使用扩展类型封装业务概念
  - 通过 `implements` 暴露需要的方法

##### 注意事项
- **常见错误**：试图添加新字段（除了表示字段本身）。
- **性能问题**：零开销，无额外对象分配。
- **兼容性**：需要 Dart 3.3+。

##### 代码示例
```dart
extension type ProductCode._(String code) {
  // 验证构造
  factory ProductCode(String code) {
    if (!_isValid(code)) throw ArgumentError('Invalid product code');
    return ProductCode._(code);
  }
  
  static bool _isValid(String code) {
    return code.length == 10 && RegExp(r'^[A-Z0-9]+$').hasMatch(code);
  }
  
  String get abbreviated => code.substring(0, 3);
}
```

