## 操作符重载

### 概念

#### 是什么
操作符重载（Operator Overloading）是面向对象编程中的一项重要特性，它允许开发者为自定义的类重新定义标准操作符（如 `+`、`-`、`*`、`==` 等）的行为。在 Dart 中，操作符本质上是具有特殊名称的实例方法，当使用操作符表达式时，实际上是在调用该方法。

#### 为什么需要
操作符重载的主要目的是**提升代码的可读性和表达的直观性**。它让自定义类型能够像内置类型（如 `int`、`double`）一样自然地参与表达式运算，避免冗长的方法调用。例如，用 `vector1 + vector2` 代替 `vector1.add(vector2)`，使代码更贴近数学或领域特定语言的表达方式。

#### 解决什么问题
- **数学计算**：为向量、矩阵、复数等数学类定义直观的算术运算
- **对象比较**：自定义对象的相等性判断（`==`）和大小比较（`<`、`>`）
- **容器访问**：通过重载 `[]` 和 `[]=` 使自定义集合支持下标访问
- **领域特定语言（DSL）**：创建更符合业务语义的表达方式
- **代码简洁性**：减少冗余的方法调用，使代码更简洁易读

### 原理

#### 底层实现
在 Dart 中，操作符重载本质上是**在类中定义特殊名称的实例方法**。当编译器遇到诸如 `a + b` 的表达式时，会将其转换为对左操作数 `a` 的 `+` 方法的调用：`a.+(b)`。

使用 `operator` 关键字声明操作符方法，语法格式如下：
```dart
class 类名 {
  返回类型 operator 操作符 (参数列表) {
    // 自定义实现逻辑
  }
}
```

#### 工作流程
1. **表达式解析**：编译器解析源代码中的操作符表达式，识别操作符、左操作数和右操作数
2. **方法查找**：根据左操作数的静态类型，查找对应的操作符方法
3. **类型检查**：检查方法参数类型是否与右操作数兼容
4. **方法调用**：调用该方法，传入右操作数作为参数（一元操作符无参数）
5. **结果返回**：方法执行结果即为整个表达式的值

#### 关键技术点
- **`operator` 关键字**：声明操作符方法的必需关键字
- **参数规则**：一元操作符无参数，二元操作符一个参数（右操作数）
- **返回类型**：理论上可返回任意类型，但应遵循语义习惯
- **左操作数决定**：二元操作符的行为由左操作数的类型决定
- **`==` 的特殊性**：定义在 `Object` 类中，重写时需使用 `@override` 注解

### 使用方法

#### 基本用法

**语法模板**：
```dart
ReturnType operator operatorSymbol (ParameterType parameter) {
  // 实现逻辑
  return result;
}
```

**简单示例**（Point 类加法）：
```dart
class Point {
  final int x;
  final int y;
  
  Point(this.x, this.y);
  
  Point operator +(Point other) {
    return Point(x + other.x, y + other.y);
  }
  
  @override
  String toString() => 'Point($x, $y)';
}

void main() {
  var p1 = Point(1, 2);
  var p2 = Point(3, 4);
  var p3 = p1 + p2;  // 使用重载的 + 运算符
  print(p3); // Point(4, 6)
}
```

#### 常见应用场景

**1. 数学计算类（向量运算）**：
```dart
class Vector {
  final double x;
  final double y;
  
  Vector(this.x, this.y);
  
  // 向量加法
  Vector operator +(Vector other) => Vector(x + other.x, y + other.y);
  
  // 向量减法
  Vector operator -(Vector other) => Vector(x - other.x, y - other.y);
  
  // 标量乘法
  Vector operator *(double scalar) => Vector(x * scalar, y * scalar);
  
  // 一元负号
  Vector operator -() => Vector(-x, -y);
  
  @override
  String toString() => 'Vector($x, $y)';
}
```

**2. 自定义比较（商品价格比较）**：
```dart
class Good {
  final double price;
  final String name;
  
  Good(this.price, this.name);
  
  // 重载 < 运算符
  bool operator <(Good other) => price < other.price;
  
  // 重载 > 运算符
  bool operator >(Good other) => price > other.price;
  
  // 重载 == 运算符（必须同时重写 hashCode）
  @override
  bool operator ==(Object other) {
    if (identical(this, other)) return true;
    if (other is! Good) return false;
    return price == other.price && name == other.name;
  }
  
  @override
  int get hashCode => Object.hash(price, name);
}
```

**3. 容器类（教室管理）**：
```dart
class Student {
  final String name;
  final int age;
  
  Student(this.name, this.age);
  
  // 将学生加入教室
  ClassRoom operator +(List<Student> others) {
    List<Student> allStudents = [this, ...others];
    return ClassRoom(allStudents);
  }
  
  // 比较年龄大小
  Student operator >(Student other) {
    return age > other.age ? this : other;
  }
  
  @override
  String toString() => 'Student{name: $name, age: $age}';
}

class ClassRoom {
  final List<Student> students;
  ClassRoom(this.students);
}
```

**4. 索引操作符（稀疏向量）**：
```dart
class SparseVector {
  final Map<int, double> _data = {};
  
  // 读取操作符
  double operator [](int index) => _data[index] ?? 0.0;
  
  // 写入操作符
  void operator []=(int index, double value) {
    if (value == 0.0) {
      _data.remove(index);
    } else {
      _data[index] = value;
    }
  }
}
```

#### 最佳实践

1. **保持语义直观**：重载的操作符应做用户预期的事，`+` 表示加法/合并，`-` 表示减法/移除

2. **重写 `==` 必须同时重写 `hashCode`**：这是 Dart 的强制性规则，确保基于哈希的集合（如 `HashSet`、`HashMap`）能正确工作

3. **保持对称性**：如果定义了 `a + b`，应考虑 `b + a` 是否在逻辑上成立且结果一致

4. **使用 `@override` 注解**：重写从 `Object` 继承的操作符（如 `==`）时，加上注解让意图更明确

5. **文档化非标准行为**：如果操作符有特殊行为，应提供清晰的文档说明

6. **适度使用**：只在能显著提升代码可读性时使用，避免滥用导致代码难以理解

7. **类型检查**：在操作符方法内部进行必要的类型检查和错误处理

### 注意事项

#### 常见错误

1. **忘记重写 `hashCode`**：
```dart
// 错误示例
class Point {
  int x, y;
  Point(this.x, this.y);
  
  @override
  bool operator ==(Object other) {
    // 重写了 == 但没有重写 hashCode
    return other is Point && x == other.x && y == other.y;
  }
  // 缺少 hashCode 重写 - 会导致 HashSet 等集合行为异常
}
```

2. **类型检查不严谨**：
```dart
// 错误示例
bool operator ==(Object other) {
  // 缺少类型检查，直接假设 other 是当前类型
  return x == (other as Point).x && y == (other as Point).y;
}
```

3. **改变操作符的常规含义**：
```dart
// 反模式示例 - 让 + 执行减法
int operator +(int other) => this.value - other;  // 导致混淆
```

4. **忽视左操作数类型决定规则**：
```dart
var result = vectorObject + someObject;  // 使用 Vector 中定义的 +
var result2 = someObject + vectorObject; // 使用 someObject 类型中定义的 +
```

#### 性能问题

- **方法调用开销**：操作符重载本质是方法调用，有轻微性能开销，通常可忽略
- **对象创建**：频繁操作（如循环中大量向量运算）应避免不必要的临时对象创建
- **哈希计算**：重写 `hashCode` 时，避免在每次调用时进行复杂计算

#### 兼容性限制

- **操作符优先级固定**：重载不能改变操作符的优先级和结合性
- **不可创建新操作符**：只能重载已有操作符，不能发明新符号
- **不可重载的操作符**：
  - 赋值操作符：`=`（基础赋值不可重载）
  - 逻辑运算符：`&&`、`||`（短路求值特性无法模拟）
  - 条件运算符：`? :`
  - 级联操作符：`..`、`?..`（Dart 特有语法）
  - 类型判定：`is`、`is!`、`as`
  - 成员访问：`.`（点操作符）
  - 空安全相关：`??`、`??=`

### 代码示例

#### 完整示例：复数类（Complex Number）

```dart
import 'dart:math';

/// 复数类，完整展示操作符重载
class Complex {
  final double real;      // 实部
  final double imaginary; // 虚部
  
  Complex(this.real, this.imaginary);
  
  // 模长
  double get magnitude => sqrt(real * real + imaginary * imaginary);
  
  // 加法：复数相加
  Complex operator +(Complex other) {
    return Complex(real + other.real, imaginary + other.imaginary);
  }
  
  // 减法：复数相减
  Complex operator -(Complex other) {
    return Complex(real - other.real, imaginary - other.imaginary);
  }
  
  // 乘法：复数相乘 (a+bi)*(c+di) = (ac-bd) + (ad+bc)i
  Complex operator *(Complex other) {
    return Complex(
      real * other.real - imaginary * other.imaginary,
      real * other.imaginary + imaginary * other.real
    );
  }
  
  // 除法：复数相除
  Complex operator /(Complex other) {
    double denominator = other.real * other.real + other.imaginary * other.imaginary;
    return Complex(
      (real * other.real + imaginary * other.imaginary) / denominator,
      (imaginary * other.real - real * other.imaginary) / denominator
    );
  }
  
  // 一元负号
  Complex operator -() => Complex(-real, -imaginary);
  
  // 相等比较
  @override
  bool operator ==(Object other) {
    if (identical(this, other)) return true;
    if (other is! Complex) return false;
    return real == other.real && imaginary == other.imaginary;
  }
  
  // hashCode 重写（与 == 保持一致）
  @override
  int get hashCode => Object.hash(real, imaginary);
  
  // 小于比较（基于模长）
  bool operator <(Complex other) => magnitude < other.magnitude;
  
  // 大于比较（基于模长）
  bool operator >(Complex other) => magnitude > other.magnitude;
  
  @override
  String toString() => imaginary >= 0 
      ? '$real + ${imaginary}i' 
      : '$real - ${-imaginary}i';
}

void main() {
  final c1 = Complex(3, 4);
  final c2 = Complex(1, 2);
  
  // 算术运算
  print('c1 = $c1');                    // 3 + 4i
  print('c2 = $c2');                    // 1 + 2i
  print('c1 + c2 = ${c1 + c2}');        // 4 + 6i
  print('c1 - c2 = ${c1 - c2}');        // 2 + 2i
  print('c1 * c2 = ${c1 * c2}');        // -5 + 10i (因为 (3+4i)*(1+2i) = 3+6i+4i+8i² = 3+10i-8 = -5+10i)
  print('c1 / c2 = ${c1 / c2}');        // 2.2 - 0.4i (近似值)
  print('-c1 = ${-c1}');                 // -3 - 4i
  
  // 比较运算
  print('c1 == Complex(3,4): ${c1 == Complex(3, 4)}'); // true
  print('c1 == c2: ${c1 == c2}');                       // false
  print('c1 < c2: ${c1 < c2}');                         // false (模长 5 > 约2.236)
  print('c1 > c2: ${c1 > c2}');                         // true
  
  // 作为集合元素
  final Set<Complex> complexSet = {c1, c2, Complex(3, 4)};
  print('Set size: ${complexSet.length}'); // 2 (因为 Complex(3,4) 与 c1 相等)
}
```

#### 可重载操作符完整列表

根据官方文档和权威资料，Dart 允许重载以下操作符：

| 类别 | 操作符 | 说明 |
|:---|:---|:---|
| **一元后缀** | `() [] . ?.` | 括号、索引、成员访问（部分可重载） |
| **一元前缀** | `-` `!` `~` `++` `--` | 负号、逻辑非、按位非、自增自减 |
| **乘法类型** | `*` `/` `%` `~/` | 乘法、除法、取模、整数除法 |
| **加法类型** | `+` `-` | 加法、减法 |
| **移位** | `<<` `>>` | 左移、右移 |
| **按位与** | `&` | 按位与 |
| **按位异或** | `^` | 按位异或 |
| **按位或** | `|` | 按位或 |
| **关系比较** | `>=` `<=` `>` `<` `as` `is` `is!` | 大小比较、类型判断 |
| **等价** | `==` `!=` | 相等判断（`!=` 不可直接重载，通过 `==` 取反实现） |
| **索引** | `[]` `[]=` | 索引读取和赋值 |

**注意**：`!=` 操作符无法直接重载，表达式 `a != b` 在 Dart 中被编译器转换为 `!(a == b)`。

## 深入理解：`==` 与 `hashCode` 的关系

### 为什么必须同时重写

Dart 中基于哈希的集合（如 `HashSet`、`HashMap`）使用对象的 `hashCode` 进行快速定位和存储。核心约定是：**如果两个对象通过 `==` 比较相等，那么它们的 `hashCode` 也必须相等**。违反此约定会导致：

- 对象放入 `HashSet` 后，无法正确判断是否已存在
- 从 `HashMap` 中根据键查找时，可能找不到明明相等的键
- 集合中出现逻辑上重复的元素

### 正确实现步骤

1. **`identical` 快速判断**：检查是否为同一对象引用
2. **类型检查**：使用 `is` 验证参数类型
3. **属性比较**：比较关键属性
4. **`hashCode` 重写**：使用 `Object.hash()` 传入与 `==` 比较的属性

```dart
@override
bool operator ==(Object other) {
  // 1. 同一引用
  if (identical(this, other)) return true;
  
  // 2. 类型检查
  if (other is! Person) return false;
  
  // 3. 属性比较（使用唯一标识）
  return id == other.id;
}

@override
int get hashCode {
  // 4. 只使用 == 中比较的属性
  return Object.hash(id);
}
```

