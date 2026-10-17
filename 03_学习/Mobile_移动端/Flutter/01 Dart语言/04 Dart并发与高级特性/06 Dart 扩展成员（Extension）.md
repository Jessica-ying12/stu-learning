# Dart 扩展成员（Extension）完整学习笔记

## 知识点名称：Dart 扩展成员（Extension）

### 一、概念

#### 1.1 是什么
**扩展成员（Extensions）** 是 Dart 2.7 版本引入的一种语言特性，它允许开发者在不修改原有类代码、不创建子类的前提下，为现有的类（包括第三方库中的类、Dart 原生类型如 `String`、`int`、`List` 等）添加新的功能。这些功能可以是方法、getter、setter 或运算符。

扩展成员通过 `extension` 关键字定义，使用时如同调用对象自身的成员一样自然。

#### 1.2 为什么需要
在实际开发中，我们经常面临以下场景：
- **为第三方库增强功能**：无法修改他人的 API，但希望添加实用的工具方法
- **为原生类型添加工具方法**：如为 `String` 添加 `isEmail`、`toCapitalized` 等方法
- **为 Flutter 的 `BuildContext` 添加便捷方法**：如 `showSnackBar`、`pushNamed` 等

传统做法是创建工具类（如 `StringUtil`），将对象作为参数传入，这种写法不够直观，且 IDE 无法提供智能提示。扩展成员让这些功能看起来像是类本身的一部分，提升代码的可读性和开发效率。

#### 1.3 解决什么问题
- **API 增强**：为无法修改的类添加新的行为
- **代码组织**：将与某类型相关的功能聚合在一起，避免散落在各处
- **语义表达**：通过"对象.方法"的形式调用，更符合面向对象的思维习惯
- **模块化**：扩展方法可以按需导入，避免代码膨胀

#### 1.4 扩展方法 vs 扩展类型
Dart 中有两个名称相似但用途不同的概念，需要明确区分：

| 特性 | 扩展方法 (Extension Methods) | 扩展类型 (Extension Types) |
|------|------------------------------|----------------------------|
| 关键字 | `extension` | `extension type` |
| 本质 | 为现有类型添加新功能 | 创建一个全新的、包装现有类型的类型 |
| 运行时行为 | 静态解析，无运行时开销 | 编译时抽象，无包装器成本 |
| 类型关系 | 不改变类型 | 创建新的类型，与原类型不同 |
| 主要用途 | 添加工具方法 | 类型安全、互操作、约束接口 |

本文主要聚焦于**扩展方法**，但会在"注意事项"部分完整介绍扩展类型作为补充。


### 二、原理

#### 2.1 底层实现
扩展成员是**静态解析（statically resolved）** 的，而非运行时动态绑定。编译器在编译时根据变量的**静态类型**来决定可以调用哪些扩展方法，并将这些调用转换为对扩展中定义的静态函数的调用。

关键点：
- **不修改原有类**：扩展方法不会修改原有类的虚函数表
- **不改变对象类型**：对象的运行时类型保持不变
- **无运行时开销**：扩展方法的调用与普通静态函数一样快

#### 2.2 工作流程
当编译器遇到 `obj.method()` 时，遵循以下解析顺序：

1. **检查实例成员**：首先检查 `obj` 的静态类型 `T` 中是否有名为 `method` 的实例成员。如果有，则调用该实例成员（实例成员优先级高于扩展成员）
2. **查找扩展成员**：如果没有，编译器会在当前作用域内查找所有应用于 `T` 的扩展（包括通过导入引入的扩展）
3. **唯一匹配**：如果找到唯一匹配的扩展方法，则将其编译为对扩展中对应函数的调用，并将 `obj` 作为参数传入（在扩展内部通过 `this` 访问）
4. **冲突处理**：如果找到多个匹配的扩展，则需要显式解决冲突

#### 2.3 关键技术点
- **静态性**：扩展方法依赖于变量的声明类型，而非运行时类型
  ```dart
  var v = '2';      // 静态类型为 String
  print(v.parseInt()); // ✅ 正确
  
  dynamic d = '2';  // 静态类型为 dynamic
  print(d.parseInt()); // ❌ 运行时错误：NoSuchMethodError
  ```

- **优先级规则**：实例成员 > 扩展成员。如果类本身已经定义了同名方法，扩展方法永远不会被调用

- **扩展名的作用**：扩展名用于解决冲突和显式调用，但并非必需；匿名扩展也可正常工作，但只能在当前库内使用


### 三、使用方法

#### 3.1 基本语法

**定义扩展**：
```dart
extension <扩展名>? on <目标类型> {
  // 成员定义：方法、getter、setter、运算符
  返回类型 方法名(参数列表) { ... }
  返回类型 get getter名 => ...;
  void set setter名(值) => ...;
  返回类型 operator 运算符(参数) => ...;
}
```

**使用扩展**：
```dart
目标类型的对象.扩展成员(...);
```

#### 3.2 扩展方法示例

**示例1：为 String 添加方法**
```dart
extension NumberParsing on String {
  int parseInt() {
    return int.parse(this);
  }
  
  double parseDouble() {
    return double.parse(this);
  }
}

void main() {
  print('42'.parseInt()); // 输出: 42
}
```

**示例2：为 String 添加 getter**
```dart
extension StringExtension on String {
  bool get isEmail => RegExp(r'^[^@]+@[^@]+\.[^@]+').hasMatch(this);
  
  bool get isBlank => trim().isEmpty;
  
  String capitalize() {
    if (isEmpty) return this;
    return this[0].toUpperCase() + substring(1);
  }
}
```

**示例3：隐藏字符串（实际开发常用）**
```dart
extension TolyStringExt on String {
  String hide({
    String fix = "*",
    int start = 2,
    int end = 2,
  }) {
    if (length <= start) return this;
    
    String p0 = substring(0, start);
    int hideCount = length - start - end;
    
    if (hideCount <= 0) {
      return p0.padRight(length, fix);
    }
    
    String p1 = List.filled(hideCount, fix).join();
    String p2 = substring(length - end);
    return "$p0$p1$p2";
  }
}

void main() {
  print("1981462002".hide()); // 19******02
}
```

#### 3.3 泛型扩展

扩展可以作用于泛型类型，使其能更灵活地服务于特定类型的泛型类。

**示例1：为 List<T> 添加泛型扩展**
```dart
extension MyFancyList<T> on List<T> {
  int get doubleLength => length * 2;
  List<T> operator -() => reversed.toList();
  List<List<T>> split(int at) => [sublist(0, at), sublist(at)];
}

void main() {
  List<int> numbers = [1, 2, 3, 4, 5];
  print(-numbers);      // [5, 4, 3, 2, 1]
  print(numbers.split(3)); // [[1, 2, 3], [4, 5]]
}
```

**示例2：针对特定类型参数（如 num）的扩展**
```dart
extension NumListTools on List<num> {
  num sum() => reduce((a, b) => a + b);
  
  double average() {
    if (isEmpty) return 0;
    return sum() / length;
  }
  
  num max() => reduce((a, b) => a > b ? a : b);
}

void main() {
  var numbers = [10.5, 20.3, 30.1];
  print(numbers.average()); // 20.3
}
```

#### 3.4 扩展运算符

扩展不仅可以添加方法，还可以添加运算符。

**示例：为 List 添加 ^ 运算符**
```dart
extension<T> on List<T> {
  // 假设价格提高 n 倍
  List<num> operator ^(int n) =>
      map((item) => num.parse("$item") * n).toList();
}

void main() {
  List prices = [1, 1.99, 4];
  print(prices ^ 3); // [3, 5.97, 12]
}
```

#### 3.5 扩展属性

扩展可以添加计算属性（getter）。

**示例：为 List 添加标签计数属性**
```dart
extension<T> on List<T> {
  // 假设每个价格需要打印 3 个标签
  int get labelCount => length * 3;
}

void main() {
  List prices = [1, 1.99, 4];
  print(prices.labelCount); // 9
}
```

#### 3.6 匿名扩展

可以省略扩展名称，匿名扩展仅在其声明所在的库中可见，不能用于显式解决冲突。

```dart
extension on String {
  bool get isBlank => trim().isEmpty;
}
```

#### 3.7 静态成员

扩展中也可以定义静态字段和方法，通过扩展名访问。

```dart
extension StringHelpers on String {
  static String capitalize(String input) {
    return input[0].toUpperCase() + input.substring(1);
  }
}

void main() {
  print(StringHelpers.capitalize('alice')); // Alice
}
```

#### 3.8 Flutter 实战应用

**示例1：为 BuildContext 添加 SnackBar 方法**
```dart
import 'package:flutter/material.dart';

extension ToastContext on BuildContext {
  void toast(Color color, String msg) {
    ScaffoldMessenger.of(this).showSnackBar(
      SnackBar(backgroundColor: color, content: Text(msg)),
    );
  }
  
  void warning(String msg) => toast(Colors.orange, msg);
  void error(String msg) => toast(Colors.red, msg);
  void success(String msg) => toast(Colors.green, msg);
}

// 使用
// context.warning('当前领域秘钥未修改，无需提交');
```

**示例2：为 BuildContext 添加导航方法**
```dart
extension NavigationExtension on BuildContext {
  Future<T?> pushNamed<T>(String routeName, {Object? arguments}) {
    return Navigator.of(this).pushNamed<T>(routeName, arguments: arguments);
  }
  
  void pop([result]) {
    Navigator.of(this).pop(result);
  }
}
```

**示例3：provider 中的扩展（实际源码）**
```dart
extension ReadContext on BuildContext {
  T read<T>() {
    return Provider.of<T>(this, listen: false);
  }
}

extension WatchContext on BuildContext {
  T watch<T>() {
    return Provider.of<T>(this);
  }
}
```


### 四、注意事项

#### 4.1 常见错误

**错误1：对 dynamic 变量调用扩展方法**
```dart
dynamic d = 'hello';
d.capitalize(); // 运行时错误：NoSuchMethodError
```
**解决方案**：确保变量具有明确的静态类型，或使用类型断言。

**错误2：扩展方法冲突**
```dart
// 两个扩展都为 String 定义了同名方法
extension E1 on String {
  int get charCount => 1;
}

extension E2 on String {
  int get charCount => 2;
}

void f(String s) {
  print(s.charCount); // 编译错误：ambiguous_extension_member_access
}
```

**解决方案**有三种：
1. **隐藏冲突扩展**：
   ```dart
   import 'lib1.dart';
   import 'lib2.dart' hide E2;
   ```

2. **显式调用扩展**：
   ```dart
   print(E1(s).charCount); // 显式选择使用哪个扩展
   ```

3. **使用导入前缀**：
   ```dart
   import 'lib1.dart';
   import 'lib2.dart' as lib2;
   
   print(lib2.E2(s).charCount);
   ```

**错误3：与实例方法冲突**：如果目标类后续添加了同名实例方法，扩展方法将不再被隐式调用，可能导致代码行为意外改变。

**错误4：访问私有成员**：扩展方法只能访问原始类的公共成员，无法访问私有成员。

#### 4.2 性能问题
- **无运行时开销**：扩展方法在编译时被转换为静态函数调用，性能与普通静态函数相当
- **无需担心**：扩展方法不会引入额外的运行时成本

#### 4.3 兼容性
- **Dart 版本要求**：扩展方法是 Dart 2.7 及以后版本支持的
- **Flutter 版本要求**：Flutter 1.17 及以上版本（对应 Dart 2.8+）
- **如果项目需要兼容更早版本**，则无法使用此特性

#### 4.4 最佳实践

**1. 命名清晰**：给扩展起一个有意义的名字（如 `StringUtils`、`ListExtensions`），便于导入时区分

**2. 单一职责**：一个扩展应专注于一个类型的一组相关功能，避免大而全的扩展

**3. 优先实例成员**：如果某个功能确实应该成为类的原生部分，且你有权修改类本身，应优先考虑直接添加实例方法

**4. 避免滥用**：
> "拓展方法虽好，可不要贪杯哦，肆意的拓展，可能会使代码很难让别人读懂，这点和运算符的重载类似。以语义为准绳，不要为了炫技而覆写或拓展。"

**5. 注意 null-safety**：扩展中 `this` 可能为 `null`（当目标类型为可空类型时），需妥善处理

**6. 模块化组织**：将相关的扩展放在独立的库文件中，按需导入


### 五、扩展类型（补充概念）

#### 5.1 什么是扩展类型

**扩展类型（Extension Types）** 是 Dart 3.0 引入的特性，它是一种编译时抽象，用不同的、仅静态的接口"包装"现有类型。扩展类型对底层类型（称为**表示类型**）的对象可用的操作集实施约束。

#### 5.2 核心语法

```dart
extension type 类型名(表示类型 变量名) {
  // 定义可用的操作
  // 可以选择性暴露、替换或添加成员
}
```

**示例：为 ID 号码创建安全类型**
```dart
extension type IdNumber(int id) {
  // 包装 int 类型的 '<' 运算符
  operator <(IdNumber other) => id < other.id;
  // 不声明 '+' 运算符，因为对 ID 号码来说加法无意义
}

void main() {
  int myUnsafeId = 42424242;
  myUnsafeId = myUnsafeId + 10; // 可以工作，但不应该允许对 ID 做加法
  
  var safeId = IdNumber(42424242);
  // safeId + 10; // 编译错误：没有 '+' 运算符
  // myUnsafeId = safeId; // 编译错误：类型不匹配
  
  safeId < IdNumber(42424241); // 可以：使用包装的 '<' 运算符
}
```

#### 5.3 扩展类型 vs 扩展方法

| 对比维度 | 扩展方法 | 扩展类型 |
|---------|---------|---------|
| 关键字 | `extension` | `extension type` |
| 本质 | 为类型添加功能 | 创建新类型包装原类型 |
| 类型关系 | 不改变类型 | 创建新类型，不能直接赋值给原类型 |
| 成员暴露 | 添加新成员 | 选择性地暴露/隐藏/替换原类型成员 |
| 主要用途 | 工具方法、功能增强 | 类型安全、约束接口、JS互操作 |

#### 5.4 透明扩展类型

当扩展类型实现（`implements`）其表示类型时，称为**透明扩展类型**，它允许扩展类型访问底层类型的所有成员。

```dart
extension type NumberT(int value) implements int {
  // 不需要显式声明 int 的成员
  NumberT get i => this;
}

void main() {
  var v1 = NumberT(1);
  int v2 = NumberT(2); // 可以赋值给 int
  var v3 = v1.i - v1;  // 可以使用 int 的运算符
}
```

#### 5.5 使用场景

- **静态 JS 互操作**：修改现有类型的接口，无需包装器成本
- **创建值类型**：如货币、ID、非空字符串等
- **接口约束**：限制对底层类型的操作，提高类型安全性


### 六、完整代码示例

以下是一个综合示例，展示了扩展方法的完整用法：

```dart
// ---------- 1. 字符串工具扩展 ----------
extension StringUtils on String {
  /// 判断是否为有效邮箱
  bool get isEmail => RegExp(r'^[^@]+@[^@]+\.[^@]+').hasMatch(this);
  
  /// 判断是否为空或仅含空白字符
  bool get isBlank => trim().isEmpty;
  
  /// 首字母大写
  String capitalize() {
    if (isEmpty) return this;
    return this[0].toUpperCase() + substring(1);
  }
  
  /// 隐藏中间部分（用于手机号、身份证脱敏）
  String obscure({int start = 2, int end = 2, String mask = '*'}) {
    if (length <= start + end) return this;
    return substring(0, start) +
        mask * (length - start - end) +
        substring(length - end);
  }
  
  /// 安全解析为整数
  int? tryParseInt() => int.tryParse(this);
}

// ---------- 2. 泛型列表扩展 ----------
extension ListTools<T> on List<T> {
  /// 获取中间元素（如果长度为偶数，返回中间两个）
  List<T> get middle {
    if (isEmpty) return [];
    var mid = length ~/ 2;
    if (length.isOdd) return [this[mid]];
    return [this[mid - 1], this[mid]];
  }
  
  /// 随机打乱顺序
  List<T> get shuffled {
    var list = toList();
    list.shuffle();
    return list;
  }
  
  /// 安全获取第一个元素
  T? get firstOrNull => isEmpty ? null : first;
  
  /// 循环移位
  List<T> rotate(int positions) {
    if (isEmpty) return this;
    var n = positions % length;
    if (n == 0) return this;
    return [...sublist(length - n), ...sublist(0, length - n)];
  }
}

// ---------- 3. 数字列表专用扩展 ----------
extension NumListTools on List<num> {
  /// 求和
  num sum() => reduce((a, b) => a + b);
  
  /// 平均值
  double average() => isEmpty ? 0 : sum() / length;
  
  /// 最大值
  num max() => reduce((a, b) => a > b ? a : b);
  
  /// 最小值
  num min() => reduce((a, b) => a < b ? a : b);
  
  /// 中位数
  double median() {
    if (isEmpty) return 0;
    var sorted = [...this]..sort();
    var mid = length ~/ 2;
    if (length.isOdd) return sorted[mid].toDouble();
    return (sorted[mid - 1] + sorted[mid]) / 2;
  }
}

// ---------- 4. Flutter BuildContext 扩展 ----------
// （需要导入 flutter/material.dart）
import 'package:flutter/material.dart';

extension ContextExtension on BuildContext {
  /// 主题
  ThemeData get theme => Theme.of(this);
  
  /// 文本样式
  TextTheme get textTheme => theme.textTheme;
  
  /// 媒体查询信息
  MediaQueryData get mediaQuery => MediaQuery.of(this);
  
  /// 屏幕宽度
  double get width => mediaQuery.size.width;
  
  /// 屏幕高度
  double get height => mediaQuery.size.height;
  
  /// 显示 SnackBar
  void showSnackBar(String message, {Color? color, Duration? duration}) {
    ScaffoldMessenger.of(this).showSnackBar(
      SnackBar(
        content: Text(message),
        backgroundColor: color,
        duration: duration ?? const Duration(seconds: 2),
      ),
    );
  }
  
  /// 显示成功提示
  void showSuccess(String message) => 
      showSnackBar(message, color: Colors.green);
  
  /// 显示错误提示
  void showError(String message) => 
      showSnackBar(message, color: Colors.red);
  
  /// 导航到命名路由
  Future<T?> pushNamed<T>(String routeName, {Object? arguments}) {
    return Navigator.of(this).pushNamed<T>(routeName, arguments: arguments);
  }
  
  /// 返回上一页
  void pop<T>([T? result]) {
    Navigator.of(this).pop(result);
  }
}

// ---------- 5. 使用示例 ----------
void main() {
  // 字符串工具
  print('test@example.com'.isEmail); // true
  print('   '.isBlank);              // true
  print('hello world'.capitalize());  // Hello world
  print('1981462002'.obscure());      // 19******02
  print('42'.tryParseInt());          // 42
  
  // 列表工具
  var list = [1, 2, 3, 4, 5];
  print(list.middle);                 // [3]
  print(list.shuffled);                // 随机顺序
  print(list.rotate(2));               // [4, 5, 1, 2, 3]
  
  // 数字列表统计
  var nums = [10, 20, 30, 40, 50];
  print(nums.average());               // 30.0
  print(nums.median());                // 30.0
  print(nums.max());                   // 50
  
  // 注意：不能对 dynamic 使用扩展
  dynamic d = 'hello';
  // d.capitalize(); // 运行时错误
}
```


### 七、总结

| 核心要点 | 说明 |
|---------|------|
| **本质** | 为现有类型添加新功能的静态编译机制 |
| **优点** | 不修改原类、按需导入、语义清晰、性能无开销 |
| **使用场景** | 字符串工具、集合操作、BuildContext 扩展、第三方库增强 |
| **注意事项** | 静态类型依赖、冲突处理、避免滥用 |
| **相关概念** | 扩展类型（`extension type`）用于创建全新类型 |

扩展成员是 Dart 语言中非常实用的特性，合理运用可以显著提升代码质量和开发效率。掌握扩展成员，让你的 Dart/Flutter 代码更加优雅高效。