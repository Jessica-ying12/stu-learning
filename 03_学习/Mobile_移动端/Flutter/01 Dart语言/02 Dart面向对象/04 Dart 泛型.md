## 一、泛型概述

### 概念
- **是什么**  
  泛型（Generics）是指在定义类、接口或方法时使用类型参数，使得代码可以操作多种类型而不需要为每种类型重写。在 Dart 中，使用尖括号 `<...>` 表示泛型，例如 `List<E>` 表示元素类型为 `E` 的列表。

- **为什么需要**  
  1. **类型安全**：在编译时捕获类型错误，避免运行时因类型不匹配而崩溃。  
  2. **减少代码重复**：一套代码适用于多种类型，无需为每种类型编写单独的类或方法。  
  3. **更好的代码生成**：正确指定泛型类型有助于编译器优化。

- **解决什么问题**  
  泛型解决了**类型与逻辑的耦合**问题。例如缓存类、集合类等容器，其存储逻辑与数据类型无关，但需要类型检查来保证安全——泛型正是为此而生。

### 原理
- **底层实现**  
  Dart 的泛型是**具体化（reified）**的，这意味着泛型类型信息在运行时仍然保留。这与 Java 的类型擦除不同——在 Dart 中，你可以判断一个对象是否为 `List<String>` 类型。

- **工作流程**  
  1. **编译时**：静态分析器根据泛型声明检查类型一致性。  
  2. **运行时**：对象携带其实际类型参数信息，支持 `is` 和 `as` 操作。

- **关键技术点**  
  - 类型参数惯用名称：`E`（集合元素）、`K`/`V`（键/值）、`T`/`S`/`R`（任意类型）。  
  - 类型推断：Dart 可以根据上下文自动推断泛型类型。


## 二、类泛型（Generic Classes）

### 概念
- **是什么**  
  在类名后使用 `<T>` 声明的类称为泛型类，其中的 `T` 可在类的字段、方法参数、返回值中使用。

- **为什么需要**  
  当类的内部逻辑不依赖于特定类型（如集合、栈、缓存）时，泛型类可以最大化代码复用。

- **解决什么问题**  
  解决**容器类型的安全复用**问题：一个 `Stack<T>` 可以同时用于 `int` 和 `String`，且编译器保证类型正确。

### 原理
- **底层实现**  
  Dart 为每个泛型类的不同实例化生成独立的代码，但会进行优化避免过度膨胀。由于类型具体化，每个实例化在运行时都是可区分的。

- **工作流程**  
  1. 定义时：声明类型参数 `T`。  
  2. 实例化时：指定具体类型（如 `Stack<int>`），编译器检查所有使用 `T` 的地方。  
  3. 运行时：对象携带类型信息，可进行类型测试。

### 使用方法
- **基本用法**
  ```dart
  class Cache<T> {
    final Map<String, T> _map = {};
    
    void set(String key, T value) => _map[key] = value;
    T? get(String key) => _map[key];
  }
  
  void main() {
    var stringCache = Cache<String>();
    stringCache.set('name', 'Dart');  // OK
    // stringCache.set('age', 30);     // 编译错误
    
    var intCache = Cache<int>();
    intCache.set('age', 30);          // OK
  }
  ```

- **常见场景**  
  - 集合类：`List<T>`、`Set<T>`、`Map<K, V>`  
  - 数据结构：栈、队列、树  
  - 异步类型：`Future<T>`、`Stream<T>`  
  - Flutter 中的 `State<T>`

- **最佳实践**  
  1. 尽量显式指定类型参数，避免依赖隐式 `dynamic`。  
  2. 使用有意义的类型参数名称（遵循惯例）。  
  3. 对于简单的集合，使用字面量语法：`<String>[]`、`<String, int>{}`。

### 注意事项
- **常见错误**  
  - 忘记指定类型导致变量类型被推断为 `dynamic`，失去类型安全。  
  - 将错误类型的对象添加到泛型集合中，若集合声明为 `dynamic` 则错误延迟到运行时。

- **性能问题**  
  具体化泛型对性能影响极小，Dart 编译器会进行优化。

### 代码示例
```dart
// 泛型栈实现
class Stack<E> {
  final List<E> _list = [];
  
  void push(E value) => _list.add(value);
  E pop() => _list.removeLast();
  bool get isEmpty => _list.isEmpty;
  E? peek() => _list.isNotEmpty ? _list.last : null;
}

void main() {
  final stack = Stack<int>();
  stack.push(1);
  stack.push(2);
  print(stack.pop()); // 2
  // stack.push('hello'); // 编译错误
}
```


## 三、方法泛型（Generic Methods）

### 概念
- **是什么**  
  在函数或方法名后使用 `<T>` 声明类型参数，使方法独立于类的泛型而拥有自己的类型变量。

- **为什么需要**  
  某些工具方法（如交换元素、查找最大值）需要在方法级别保持类型安全，但与类级别的泛型无关。

- **解决什么问题**  
  解决**算法与类型解耦**问题：一个 `first` 方法可以返回任何类型列表的第一个元素，同时保证返回类型与列表元素类型一致。

### 原理
- **底层实现**  
  与泛型类类似，泛型方法的类型参数在运行时具体化。编译器根据调用时的参数推断实际类型。

- **工作流程**  
  1. 调用时，根据传入参数类型确定 `T`。  
  2. 编译器验证方法体内所有使用 `T` 的地方。  
  3. 运行时，方法闭包携带类型信息。

### 使用方法
- **基本用法**
  ```dart
  T first<T>(List<T> items) {
    if (items.isEmpty) throw StateError('empty list');
    return items.first;
  }
  
  void main() {
    var numbers = [1, 2, 3];
    var firstNum = first(numbers);     // 类型推断为 int
    var firstStr = first<String>(['a', 'b']); // 显式指定
  }
  ```

- **常见场景**  
  - 集合转换函数（如 `map`、`where`）  
  - 工厂方法创建特定类型的对象  
  - 回调或事件处理器

- **最佳实践**  
  1. 利用类型推断简化代码，但上下文不明确时可显式指定。  
  2. 多个类型参数时，使用不同的字母（如 `T`、`R`）。

### 注意事项
- **常见错误**  
  - 在方法体内尝试实例化 `T`（如 `T()`）是不允许的，因为不能保证 `T` 有默认构造函数。  
  - 忽略类型推断可能导致 `T` 被推断为 `dynamic`。

### 代码示例
```dart
// 交换列表中两个元素
void swap<T>(List<T> list, int i, int j) {
  var temp = list[i];
  list[i] = list[j];
  list[j] = temp;
}

// 类型转换映射
List<R> map<T, R>(List<T> items, R Function(T) transform) {
  var result = <R>[];
  for (var item in items) {
    result.add(transform(item));
  }
  return result;
}

void main() {
  var list = [1, 2, 3];
  swap(list, 0, 2);
  print(list); // [3, 2, 1]
  
  var strings = map<int, String>(list, (i) => 'Number $i');
  print(strings); // [Number 3, Number 2, Number 1]
}
```


## 四、泛型约束（Bounds / extends）

### 概念
- **是什么**  
  通过 `extends` 关键字限制类型参数必须是指定类型或其子类型，称为**有界泛型**。

- **为什么需要**  
  没有约束时，泛型参数只能调用 `Object` 的方法。约束允许在泛型代码中调用特定类型的方法（如 `compareTo`）。

- **解决什么问题**  
  解决**泛型功能受限**问题：例如要编写一个求最大值的函数，需要确保类型参数实现了 `Comparable` 接口。

### 原理
- **底层实现**  
  约束信息在编译时用于类型检查，并指导代码生成。当类型参数被约束时，编译器可以安全地调用约束类型的方法。

- **工作流程**  
  1. 定义时：使用 `<T extends SomeType>` 声明约束。  
  2. 实例化时：编译器检查实际类型是否满足约束。  
  3. 泛型内部：可将 `T` 视为 `SomeType` 类型。

### 使用方法
- **基本用法**
  ```dart
  // 确保类型非空
  class NonEmptyBox<T extends Object> {
    T content;
    NonEmptyBox(this.content);
  }
  
  // 数值计算
  T doubleValue<T extends num>(T value) => (value * 2) as T;
  
  // 可比较类型
  T max<T extends Comparable<T>>(T a, T b) {
    return a.compareTo(b) > 0 ? a : b;
  }
  ```

- **常见场景**  
  - 数值运算：`T extends num`  
  - 比较操作：`T extends Comparable<T>`  
  - 序列化：`T extends JsonSerializable`  
  - 确保非空：`T extends Object`（默认是 `Object?`）

- **最佳实践**  
  1. 使用最宽泛的约束，避免过度限制。  
  2. 对于需要自身比较的类型，使用 F-bounds：`T extends Comparable<T>`。

### 注意事项
- **常见错误**  
  - 忘记约束导致无法调用必要方法。  
  - 约束过于严格，限制了泛型的适用范围。

### 代码示例
```dart
class Person {
  final String name;
  Person(this.name);
}

class Member<T extends Person> {
  T person;
  Member(this.person);
  
  String get name => person.name; // 可以安全访问 name
}

void main() {
  var member = Member<Person>(Person('Alice'));
  print(member.name); // Alice
  // var invalid = Member<String>('Bob'); // 编译错误
}
```


## 五、泛型的协变与逆变（Covariance and Contravariance）

### 概念
- **是什么**  
  型变描述具有继承关系的类型在应用于泛型后，泛型类型之间的子类型关系。  
  - **协变**：如果 `Cat` 是 `Animal` 的子类型，那么 `List<Cat>` 也是 `List<Animal>` 的子类型。  
  - **逆变**：如果 `Cat` 是 `Animal` 的子类型，那么 `Function(Animal)` 是 `Function(Cat)` 的子类型（参数位置）。  
  - **不变**：`List<Cat>` 与 `List<Animal>` 无继承关系。

- **为什么需要**  
  实际编程中，需要将子类型的容器赋值给父类型变量（如将 `List<Cat>` 传给期望 `List<Animal>` 的方法），这需要明确的型变规则。

- **解决什么问题**  
  解决**泛型多态**问题：在保证类型安全的前提下，支持继承关系在泛型中的运用。

### 原理
- **底层实现**  
  Dart 的泛型默认是**协变**的。这意味着可以安全地将 `List<Cat>` 赋值给 `List<Animal>`。但协变在可变集合中可能带来风险，Dart 在运行时检查这类操作，防止类型污染。

- **工作流程**  
  1. 类型检查器允许协变赋值。  
  2. 通过父类型引用向集合添加元素时，Dart 进行运行时类型检查，若实际类型不匹配则抛出错误。  
  3. 函数类型在处理参数时表现出逆变。

### 使用方法
- **基本用法**
  ```dart
  class Animal {}
  class Cat extends Animal {}
  class Dog extends Animal {}
  
  void main() {
    List<Cat> cats = [Cat(), Cat()];
    List<Animal> animals = cats; // 协变允许
    
    // animals.add(Dog()); // 运行时错误！实际类型是 List<Cat>
    
    // 函数逆变
    void handleAnimal(Animal a) => print('animal');
    void Function(Cat) catHandler = handleAnimal; // 允许
  }
  ```

- **常见场景**  
  - 将子类列表传递给期望父类列表的方法（只读操作安全）  
  - 事件监听器：回调函数参数使用逆变  
  - 生产者-消费者模式

- **最佳实践**  
  1. 只读集合使用协变安全；可变集合需谨慎。  
  2. 理解函数类型的逆变规则，避免类型错误。

### 注意事项
- **常见错误**  
  利用协变赋值后，通过父类型引用向集合添加不兼容类型，导致运行时错误。

### 代码示例
```dart
void feedAnimals(List<Animal> animals) {
  for (var a in animals) {
    print('feeding...'); // 只读操作安全
  }
}

void main() {
  List<Cat> cats = [Cat(), Cat()];
  feedAnimals(cats); // 协变允许
  
  // 但这样做不安全
  void addDog(List<Animal> animals) {
    animals.add(Dog());
  }
  // addDog(cats); // 运行时错误
}
```


## 六、类型具体化（Reified Generics）

### 概念
- **是什么**  
  类型具体化指泛型的类型信息在运行时仍然保留，而不是被擦除。在 Dart 中，可以检查一个对象是否为 `List<String>` 类型。

- **为什么需要**  
  类型擦除（如 Java）使得运行时无法判断泛型类型，限制了某些操作（如序列化、类型测试）。具体化让泛型更加强大。

- **解决什么问题**  
  解决**运行时类型信息丢失**问题，支持 `is`、`as` 操作和基于类型的分支逻辑。

### 原理
- **底层实现**  
  Dart 虚拟机（VM）会保留泛型类型参数。当创建泛型对象时，类型信息被编码到对象中，运行时比较这些信息进行类型测试。

- **工作流程**  
  1. 创建泛型对象时存储类型参数。  
  2. 执行 `is` 操作时比较对象的类型参数。  
  3. 泛型方法中，`T` 被绑定到实际类型，可用于 `is T` 检查。

### 使用方法
- **基本用法**
  ```dart
  void printType<T>(T value) {
    print('Type of T: $T');
    print('value is T? ${value is T}'); // 总是 true
  }
  
  void main() {
    var names = <String>[];
    print(names is List<String>); // true
    print(names is List<int>);    // false
    
    printType(42);  // Type of T: int
  }
  ```

- **常见场景**  
  - 运行时类型检查  
  - 泛型方法内部基于类型做分支逻辑  
  - 序列化框架自动选择编码方式

- **最佳实践**  
  1. 利用类型具体化避免传递额外的 `Type` 对象。  
  2. 注意编译为 JavaScript 时某些类型信息可能简化，但 Dart 尽量保持一致。

### 注意事项
- **常见错误**  
  在泛型方法中使用 `T()` 尝试创建实例是不允许的，因为不能保证 `T` 有默认构造函数。

- **性能问题**  
  运行时类型检查有轻微开销，但在性能敏感代码中应避免大量 `is` 检查。

### 代码示例
```dart
// 根据类型参数创建集合
List<T> createEmptyList<T>() => <T>[];

// 运行时类型分支
void handleValue<T>(T value) {
  if (T == int) {
    print('Integer: ${value + 1}');
  } else if (T == String) {
    print('String: ${value.toUpperCase()}');
  } else {
    print('Other: $value');
  }
}

void main() {
  var intList = createEmptyList<int>();
  intList.add(10);
  print(intList is List<int>); // true
  
  handleValue(42);     // Integer: 43
  handleValue('dart'); // String: DART
}
```


## 七、自引用类型参数限制（F-bounds）

### 概念
- **是什么**  
  F-bounds 是指类型参数的约束引用到自身，例如 `<T extends Comparable<T>>`，表示 `T` 必须能够与自身比较。

- **为什么需要**  
  某些操作（如比较、复制）要求两个对象属于同一具体类型，F-bounds 可以强制这种一致性。

- **解决什么问题**  
  解决**自引用类型约束**问题：确保泛型类型能够与自己类型的其他实例进行操作。

### 原理
- **底层实现**  
  编译器在检查类型约束时，允许类型参数在其自己的约束中出现。这创建了一个递归约束，确保类型的一致性。

- **工作流程**  
  1. 定义：`<T extends Comparable<T>>` 声明 `T` 必须实现 `Comparable<T>`。  
  2. 实例化：实际类型必须实现 `Comparable<自身>`。  
  3. 使用时：可以安全地比较两个 `T` 类型的对象。

### 使用方法
- **基本用法**
  ```dart
  abstract interface class Comparable<T> {
    int compareTo(T other);
  }
  
  T max<T extends Comparable<T>>(T a, T b) {
    return a.compareTo(b) > 0 ? a : b;
  }
  
  class Person implements Comparable<Person> {
    final int age;
    Person(this.age);
    
    @override
    int compareTo(Person other) => age.compareTo(other.age);
  }
  
  void main() {
    print(max(Person(20), Person(30)).age); // 30
  }
  ```

- **常见场景**  
  - 可比较类型：`Comparable<T>`  
  - 构建器模式：`Builder<T extends Builder<T>>`  
  - 链式调用返回子类型

- **最佳实践**  
  1. 确保实现类正确实现了对自身的接口。  
  2. 利用 Dart 3.5+ 的**使用边界进行推断**功能，F-bounds 类型可以自动推断，无需显式指定。

### 注意事项
- **常见错误**  
  实现 `Comparable<T>` 时指定了错误类型参数（如 `Comparable<Object>`），导致不满足约束。

### 代码示例
```dart
// 链式调用示例
abstract class Builder<T extends Builder<T>> {
  T withName(String name);
  T withAge(int age);
}

class PersonBuilder implements Builder<PersonBuilder> {
  String? _name;
  int? _age;
  
  @override
  PersonBuilder withName(String name) {
    _name = name;
    return this;
  }
  
  @override
  PersonBuilder withAge(int age) {
    _age = age;
    return this;
  }
}

void main() {
  var builder = PersonBuilder()
      .withName('Alice')
      .withAge(25); // 返回类型始终是 PersonBuilder
}
```


## 八、类型推断与最佳实践

### 概念
- **是什么**  
  Dart 具有强大的类型推断能力，可以在不显式指定类型的情况下自动确定泛型类型参数。

- **为什么需要**  
  减少代码冗余，同时保持类型安全。

### 原理
- **工作流程**  
  1. **向下推断**：根据上下文（如赋值的目标类型）推断。  
  2. **向上推断**：根据参数类型推断。  
  3. **使用边界推断**（Dart 3.5+）：结合约束进行更精确的推断，特别是对 F-bounds 类型。

### 使用建议
1. **优先使用 `var` 但确保有足够上下文**：
   ```dart
   var list = <int>[]; // 好：明确类型
   var list = [];      // 不好：推断为 List<dynamic>
   ```

2. **集合字面量尽量指定类型**：
   ```dart
   var names = <String>['Seth', 'Kathy']; // 推荐
   ```

3. **利用类型推断简化泛型方法调用**：
   ```dart
   var result = map(list, (i) => i.toString()); // 自动推断
   ```

4. **在公共 API 中显式指定类型**，提高可读性和稳定性。


## 九、总结

| 知识点 | 核心作用 | 关键特性 |
|--------|----------|----------|
| 类泛型 | 类型安全的容器复用 | 具体化、类型推断 |
| 方法泛型 | 算法与类型解耦 | 独立类型参数 |
| 泛型约束 | 限制类型范围，调用特定方法 | `extends` 关键字 |
| 协变/逆变 | 支持泛型多态 | 默认协变，函数参数逆变 |
| 类型具体化 | 运行时保留类型信息 | 支持 `is`/`as` |
| F-bounds | 自引用类型约束 | `T extends Comparable<T>` |

Dart 的泛型系统通过**具体化**和**协变**提供了比许多语言更灵活的类型安全机制，同时结合**类型推断**和**边界推断**持续优化开发者体验。掌握这些知识点，可以编写出既安全又复用的高质量代码。


> 注：本笔记基于 Dart 3.8 版本，所有代码示例均可在 Dart 2.12+ 环境中运行。