## 1. 列表（List）

### 概念
- **是什么**：列表（List）是 Dart 中最基本的集合类型，代表一个**有序**的对象组，类似于其他语言中的数组。每个元素在列表中有固定的索引位置，索引从 0 开始。
- **为什么需要**：在编程中经常需要存储多个相同类型或不同类型的对象，并按顺序访问它们。列表提供了方便的索引访问、动态增删元素等操作，是组织数据的常用手段。
- **解决什么问题**：解决数据的有序存储和随机访问问题，支持重复元素，适合维护序列数据（如待办事项列表、商品清单等）。

### 原理
- **底层实现**：Dart 中的 `List` 实际上是一个接口，其默认实现是 `_GrowableList`（可增长列表），内部基于一个动态数组（类似于 Java 的 `ArrayList` 或 C++ 的 `std::vector`）。当列表长度超过当前容量时，会自动分配更大的内存空间并复制原有元素。
- **工作流程**：创建列表时，若指定长度或使用字面量，Dart 会分配连续内存。通过索引访问元素时间复杂度为 O(1)，插入或删除元素（非末尾）可能需要移动后续元素，时间复杂度为 O(n)。
- **关键技术点**：
  - 索引访问：`list[index]` 直接通过下标读写。
  - 可增长：`add()`、`addAll()` 等方法会触发扩容机制。
  - 泛型：支持 `List<T>` 指定元素类型，增强类型安全。

### 使用方法
#### 基本用法
- **创建列表**：字面量方式、构造函数方式。
  ```dart
  var list1 = [1, 2, 3];                // 推断为 List<int>
  var list2 = <String>['a', 'b'];        // 指定类型
  var list3 = List<int>.empty(growable: true); // 可增长空列表
  List<int> list4 = [];                  // 空列表
  ```
- **访问与修改**：使用 `[]` 运算符。
  ```dart
  var list = ['apple', 'banana', 'cherry'];
  print(list[1]);        // 'banana'
  list[0] = 'orange';    // 修改
  ```
- **常用方法**：
  - 添加：`add(element)`、`addAll(iterable)`
  - 插入：`insert(index, element)`、`insertAll(index, iterable)`
  - 删除：`remove(element)`、`removeAt(index)`、`removeLast()`、`clear()`
  - 查找：`contains(element)`、`indexOf(element)`
  - 遍历：`for`、`forEach()`、`map()` 等
  - 排序：`sort([comparator])`

#### 常见场景
- 存储有序数据：如用户列表、历史记录。
- 数据转换：使用 `map()` 生成新列表。
- 过滤：使用 `where()` 筛选元素。
- 作为函数返回值或参数传递。

#### 最佳实践
- 使用尾随逗号使多行列表更易维护。
- 尽量指定列表类型，避免使用 `var` 推断为 `dynamic` 导致运行时错误。
- 对于频繁在列表头部插入/删除的操作，考虑使用 `Queue` 替代。

### 注意事项
- **常见错误**：
  - 访问不存在的索引：`list[-1]` 或 `list[list.length]` 会引发 `RangeError`。
  - 在固定长度列表上调用 `add()`：若列表是固定长度（例如通过 `List.filled` 创建且 `growable: false`），修改大小的方法会抛出异常。
- **性能问题**：
  - 频繁在列表中间插入/删除元素成本较高，建议使用 `LinkedList` 或 `ListBuffer`（如果适用）。
  - 列表扩容涉及内存拷贝，若事先知道元素数量，可使用 `List.generate` 或指定初始容量优化。
- **兼容性**：列表操作在所有 Dart 版本中基本一致，但展开运算符等特性需要 Dart 2.3+。

### 代码示例
```dart
void main() {
  // 创建列表
  var fruits = ['apple', 'banana', 'cherry'];
  fruits.add('date');
  fruits.insert(1, 'blueberry');
  print(fruits); // [apple, blueberry, banana, cherry, date]

  // 访问元素
  print(fruits[2]); // banana

  // 修改元素
  fruits[0] = 'apricot';
  print(fruits); // [apricot, blueberry, banana, cherry, date]

  // 删除元素
  fruits.remove('banana');
  fruits.removeAt(2);
  print(fruits); // [apricot, blueberry, date]

  // 遍历
  for (var fruit in fruits) {
    print(fruit);
  }

  // 排序
  fruits.sort();
  print(fruits); // [apricot, blueberry, date]

  // 固定长度列表（不可变）
  var constantList = const [1, 2, 3];
  // constantList[1] = 5; // 错误！
}
```

---

## 2. 集合（Set）

### 概念
- **是什么**：集合（Set）是一个**无序**且元素**唯一**的对象集合。Dart 中的 `Set` 不允许重复元素，常用于去重或集合运算。
- **为什么需要**：当需要确保一组元素不重复，或者执行数学上的集合运算（并集、交集、差集）时，`Set` 是理想选择。
- **解决什么问题**：解决元素唯一性约束和高效成员关系查询问题。

### 原理
- **底层实现**：Dart 中 `Set` 的默认实现是 `LinkedHashSet`，它基于哈希表，并且维护了元素的插入顺序（尽管集合本身无序，但迭代顺序与插入顺序一致）。哈希表提供 O(1) 的添加、删除、查找操作。
- **工作流程**：向集合添加元素时，先计算元素的哈希码（通过 `hashCode` 方法），然后确定存储位置。如果该位置已有元素且 equals 比较相等，则新元素不被添加。
- **关键技术点**：
  - 哈希码与相等性：自定义对象需正确重写 `==` 和 `hashCode` 才能被集合正确处理。
  - 去重：添加重复元素时无变化。
  - 集合运算：提供 `union`、`intersection`、`difference` 等方法。

### 使用方法
#### 基本用法
- **创建集合**：字面量方式（花括号）或构造函数。
  ```dart
  var set1 = {'a', 'b', 'c'};           // Set<String>
  Set<int> set2 = {1, 2, 3};            // 显式类型
  var set3 = <String>{};                 // 空集合（必须指定类型）
  var set4 = Set.from([1, 2, 3]);        // 从其他可迭代对象创建
  ```
- **添加元素**：`add(element)`、`addAll(iterable)`
  ```dart
  var set = {'apple', 'banana'};
  set.add('orange');
  set.add('apple');   // 无变化，因为已存在
  ```
- **删除元素**：`remove(element)`、`clear()`
- **查询**：`contains(element)`、`length`

#### 常见场景
- 数组去重：`var unique = Set.from(list).toList();`
- 成员关系测试：`if (favoriteFruits.contains('mango')) ...`
- 集合运算：比较两组数据差异。
- 维护不重复的标签、分类等。

#### 最佳实践
- 对自定义对象使用集合时，务必重写 `==` 和 `hashCode`。
- 使用 `LinkedHashSet` 的默认行为即可满足大部分需求。
- 对于大量数据且不需要顺序的场景，可考虑 `HashSet`（来自 `dart:collection`）以提高性能。

### 注意事项
- **常见错误**：
  - 误用 `{}` 创建空集合：`var set = {};` 实际上创建的是 `Map`，而不是 `Set`。必须显式类型注解：`var set = <String>{};` 或 `Set<String> set = {};`。
  - 向集合添加元素时，如果元素未正确重写 `hashCode`，可能导致无法正确去重。
- **性能问题**：
  - 哈希冲突会影响性能，但一般可接受。
  - 集合的迭代顺序通常与插入顺序一致（默认实现），但不应依赖此特性。
- **兼容性**：集合字面量在 Dart 2.2 之后完全支持。展开运算符等需要 Dart 2.3+。

### 代码示例
```dart
void main() {
  // 创建集合
  var fruits = {'apple', 'banana', 'cherry'};
  fruits.add('date');
  fruits.add('banana'); // 重复，无影响
  print(fruits); // {apple, banana, cherry, date}（顺序与插入相关）

  // 检查成员
  print(fruits.contains('cherry')); // true

  // 删除
  fruits.remove('banana');
  print(fruits); // {apple, cherry, date}

  // 集合运算
  var tropical = {'banana', 'mango', 'pineapple'};
  var common = fruits.intersection(tropical); // 交集
  print(common); // {}

  var all = fruits.union(tropical); // 并集
  print(all); // {apple, cherry, date, banana, mango, pineapple}

  // 从列表去重
  var listWithDuplicates = [1, 2, 2, 3, 1];
  var uniqueSet = Set.from(listWithDuplicates);
  print(uniqueSet); // {1, 2, 3}

  // 常量集合
  final constantSet = const {'fluorine', 'chlorine'};
  // constantSet.add('helium'); // 错误！
}
```

---

## 3. 映射（Map）

### 概念
- **是什么**：映射（Map）是一个**无序**的键值对集合，每个键唯一，对应一个值。键和值可以是任意类型的对象。
- **为什么需要**：当需要根据某个键快速查找关联的值时，映射提供了高效的字典功能。常用于缓存、配置、数据映射等场景。
- **解决什么问题**：解决键值关联和快速检索问题，类似于其他语言的字典或哈希表。

### 原理
- **底层实现**：Dart 中 `Map` 的默认实现是 `LinkedHashMap`，它基于哈希表，并维护插入顺序（迭代顺序与键的插入顺序一致）。键的哈希码用于定位存储位置，`==` 用于判断键相等性。
- **工作流程**：添加键值对时，根据键的哈希码找到桶位，如果键已存在，则更新值；否则插入新条目。查询时通过键哈希快速定位。
- **关键技术点**：
  - 键的唯一性：重复键会导致旧值被覆盖。
  - 可为空：键或值均可为 `null`，但键不能重复（`null` 只能出现一次）。
  - 不可变映射：使用 `const` 创建编译时常量映射。

### 使用方法
#### 基本用法
- **创建映射**：字面量方式（`{key: value}`）或构造函数。
  ```dart
  var gifts = {
    'first': 'partridge',
    'second': 'turtledoves',
  }; // Map<String, String>
  var nobleGases = {
    2: 'helium',
    10: 'neon',
  }; // Map<int, String>
  var emptyMap = {}; // 注意：这是 Map<dynamic, dynamic>，不是 Set
  Map<String, int> scores = {}; // 显式类型空映射
  var map = Map<String, int>(); // 构造函数
  ```
- **添加/修改键值对**：使用 `[]=` 运算符。
  ```dart
  gifts['third'] = 'golden rings'; // 添加新键
  gifts['first'] = 'a partridge';   // 修改已有键
  ```
- **访问值**：使用 `[]` 运算符，键不存在返回 `null`。
  ```dart
  print(gifts['first']); // 'a partridge'
  print(gifts['fifth']); // null
  ```
- **常用方法**：
  - 获取所有键：`keys`（返回 `Iterable<K>`）
  - 获取所有值：`values`
  - 检查键存在：`containsKey(key)`
  - 删除键值对：`remove(key)`
  - 清空：`clear()`
  - 遍历：`forEach((key, value) {...})`、`for-in` 遍历 `keys` 或 `entries`

#### 常见场景
- 配置参数：如 `Map<String, dynamic> config = {'host': 'localhost', 'port': 8080};`
- 数据缓存：以查询条件为键，结果为值。
- 对象属性动态存储：当属性名不确定时用映射管理。
- JSON 数据解析与生成。

#### 最佳实践
- 尽量明确键和值的类型，避免使用 `dynamic` 引发运行时错误。
- 访问可能不存在的键时，使用 `??` 提供默认值：`var value = map['key'] ?? defaultValue;`
- 若需要遍历所有键值对，使用 `entries` 属性获取 `MapEntry` 迭代器。
- 对于频繁读取的场景，映射性能优异；但若需要有序键值，可考虑使用 `LinkedHashMap`（默认）或 `SplayTreeMap`。

### 注意事项
- **常见错误**：
  - 误用 `{}` 创建映射或集合：字面量 `{}` 默认为 `Map`，若意图是空集合，必须显式类型。
  - 使用 `map[key]` 访问时，假设键一定存在而未判空，可能导致意外使用 `null`。
  - 对自定义对象作为键，必须正确实现 `==` 和 `hashCode`，否则可能无法正确匹配。
- **性能问题**：
  - 映射的哈希冲突会影响性能，但通常可接受。
  - 遍历 `keys` 或 `values` 时，不要在遍历过程中修改映射（除非使用 `entries` 并小心）。
- **兼容性**：映射字面量从 Dart 1.0 就支持，展开/空感知等特性需要 Dart 2.3+（映射的展开从 Dart 2.3 开始支持）。

### 代码示例
```dart
void main() {
  // 创建映射
  var gifts = {'first': 'partridge'};
  gifts['second'] = 'turtledoves';
  gifts['fourth'] = 'calling birds';

  // 访问
  print(gifts['first']); // partridge
  print(gifts['third']); // null

  // 修改
  gifts['first'] = 'a partridge';
  print(gifts['first']); // a partridge

  // 删除
  gifts.remove('second');
  print(gifts); // {first: a partridge, fourth: calling birds}

  // 遍历
  gifts.forEach((key, value) {
    print('$key: $value');
  });

  for (var entry in gifts.entries) {
    print('${entry.key}: ${entry.value}');
  }

  // 键值集合
  var keys = gifts.keys; // Iterable<String>
  var values = gifts.values; // Iterable<String>

  // 常量映射
  final constantMap = const {2: 'helium', 10: 'neon'};
  // constantMap[2] = 'Helium'; // 错误！
}
```

---

## 4. 集合元素的高级特性

Dart 从 2.3 版本开始，在列表、集合、映射的字面量内部支持嵌入**控制流元素**和**展开元素**，极大增强了集合构建的灵活性。从 3.8 开始还引入了**空感知元素**。

### 概念
- **是什么**：集合元素的高级特性允许在集合字面量内部使用 `if`、`for`、展开运算符（`...` 和 `...?`）以及空感知元素（`?`），从而动态生成集合内容。
- **为什么需要**：传统的集合构建往往需要多次调用 `add` 或临时变量，代码冗长。这些特性使得集合的初始化更加声明式、简洁，类似其他语言的列表推导式。
- **解决什么问题**：解决集合构建时需要条件判断、循环、合并多个集合的场景，使代码更易读、更高效。

### 原理
- **底层实现**：这些特性是语法糖，在编译时被转换为等价的代码。例如，展开运算符会被展开为循环添加元素，`if` 元素被转换为条件分支等。
- **工作流程**：
  - **展开元素**：`...collection` 会将 `collection` 中的每个元素依次添加到当前集合中。
  - **空感知展开**：`...?collection` 首先判断 `collection` 是否为 `null`，若不为 `null` 再展开。
  - **空感知元素**：`?expression` 在表达式值为 `null` 时忽略该项，否则添加该值。
  - **if 元素**：根据条件决定是否包含某个元素或一段集合。
  - **for 元素**：循环生成多个元素。
- **关键技术点**：
  - 类型安全：展开集合的类型必须与当前集合的元素类型兼容。
  - 空安全：对于可能为 `null` 的集合，必须使用 `...?` 而不是 `...`。
  - 作用域：`if` 和 `for` 内部可访问外部变量，但不能声明新的同名变量。

### 使用方法
#### 展开元素
- **基本用法**：使用 `...` 将另一个可迭代对象展开插入。
  ```dart
  var a = [1, 2, 3];
  var b = [0, ...a, 4]; // [0, 1, 2, 3, 4]
  ```
- **空感知展开**：当集合可能为 `null` 时使用 `...?`。
  ```dart
  List<int>? a = null;
  var b = [1, 2];
  var c = [0, ...?a, ...b, 3]; // [0, 1, 2, 3]
  ```

#### 空感知元素
> **版本要求**：Dart 3.8+

在表达式前加 `?`，若表达式为 `null` 则不添加。
```dart
int? maybeValue = null;
var list = [1, ?maybeValue, 3]; // [1, 3]

// 映射中的空感知键或值
String? key = 'name';
int? value = 10;
var map = {?key: value, 'age': ?value}; // {name: 10, age: 10}
// 若 key 为 null，则整个条目被忽略
```

#### If 元素
- **布尔条件**：`if (condition) element` 或带 `else` 分支。
  ```dart
  var include = true;
  var list = [0, if (include) 1, 2]; // [0, 1, 2]
  var list2 = [0, if (!include) 1 else 2]; // [0, 2]
  ```
- **模式匹配**：`if (expression case pattern) element`。
  ```dart
  var obj = 123;
  var info = [
    if (obj case int i) 'integer $i',
    if (obj case String s) 'string $s',
  ]; // ['integer 123']
  ```
- **嵌套 if**：可以连续使用 `else if`。
  ```dart
  var fruit = 'apple';
  var list = [
    0,
    if (fruit == 'banana') 1 else if (fruit == 'apple') 2,
  ]; // [0, 2]
  ```

#### For 元素
- **迭代 for-in**：
  ```dart
  var nums = [2, 3, 4];
  var list = [1, for (var n in nums) n * n, 5]; // [1, 4, 9, 16, 5]
  ```
- **传统 for 循环**：
  ```dart
  var list = [1, for (int i = 5; i > 2; i--) i, 0]; // [1, 5, 4, 3, 0]
  ```

#### 嵌套控制流
可以将多种元素任意组合嵌套。
```dart
var numbers = [1, 2, 3, 4, 5, 6];
var result = [
  0,
  for (var n in numbers)
    if (n.isEven) n, // 只取偶数
  7,
]; // [0, 2, 4, 6, 7]

// 深度嵌套示例
var xs = [1, 2];
var ys = [10, 20];
var combined = [
  for (var x in xs)
    for (var y in ys)
      if (x < y) x + y,
]; // [11, 21, 12, 22]（注意顺序）
```

#### 常见场景
- 根据条件动态添加元素（如表单字段非必填）。
- 合并多个列表。
- 从集合中过滤元素并生成新集合。
- 生成矩阵或笛卡尔积。

#### 最佳实践
- 使用展开运算符合并集合比多次调用 `addAll` 更简洁。
- 当集合可能为 `null` 时，优先使用 `...?` 避免空异常。
- 对于复杂的嵌套逻辑，保持代码可读性，必要时拆分成辅助方法。
- 利用 `if-case` 结合模式匹配进行条件解构，使代码更清晰。

### 注意事项
- **常见错误**：
  - 对可能为 `null` 的集合使用 `...` 而不是 `...?` 会导致编译错误（启用空安全后）。
  - 在 `if` 或 `for` 元素内错误地使用 `var` 声明变量（会导致语法错误，因为内部不能声明新变量，只能引用外部变量）。
  - 忘记空感知元素 `?` 是 Dart 3.8+ 特性，在低版本中使用会导致编译错误。
- **性能问题**：
  - 展开元素本质上是一个循环，对于大集合可能产生性能开销，但通常可接受。
  - 嵌套多层 `for` 会产生大量元素，注意避免不必要的计算。
- **兼容性**：
  - 展开运算符和 `if`/`for` 元素需要 Dart 2.3+。
  - 空感知元素（`?` 用于元素）需要 Dart 3.8+。
  - 模式匹配（`if-case`）需要 Dart 3.0+。

### 代码示例
```dart
void main() {
  // 展开元素
  var list1 = [1, 2, 3];
  var list2 = [0, ...list1, 4];
  print(list2); // [0, 1, 2, 3, 4]

  // 空感知展开
  List<int>? nullableList = null;
  var list3 = [0, ...?nullableList, 5];
  print(list3); // [0, 5]

  // 空感知元素（Dart 3.8+）
  int? a = null;
  int? b = 2;
  var list4 = [1, ?a, ?b, 3];
  print(list4); // [1, 2, 3]

  // if 元素（布尔条件）
  var addExtra = true;
  var list5 = [1, if (addExtra) 2, 3];
  print(list5); // [1, 2, 3]

  // if 元素（模式匹配）
  var data = 42;
  var list6 = [
    'start',
    if (data case int x) 'int: $x',
    'end'
  ];
  print(list6); // [start, int: 42, end]

  // for 元素
  var squares = [for (int i = 1; i <= 3; i++) i * i];
  print(squares); // [1, 4, 9]

  // 嵌套控制流
  var numbers = [1, 2, 3, 4];
  var result = [
    0,
    for (var n in numbers)
      if (n.isEven) n * 2,
    10,
  ];
  print(result); // [0, 4, 8, 10]

  // 在映射中使用展开（Dart 2.3+）
  var defaults = {'theme': 'light', 'lang': 'en'};
  var userPrefs = {'theme': 'dark', ...defaults};
  print(userPrefs); // {theme: dark, lang: en}（注意展开的键被覆盖）

  // 空感知展开在映射中
  Map<String, String>? extra = null;
  var config = {'debug': 'true', ...?extra};
  print(config); // {debug: true}
}
```

---

## 5. 其他集合类型

除了 `List`、`Set`、`Map` 三个核心集合外，Dart 的 `dart:collection` 库还提供了更多专用集合，适用于特定场景。

### 5.1 队列（Queue）
- **概念**：`Queue` 是一个先进先出（FIFO）的集合，支持在两端高效添加和删除元素。默认实现为 `ListQueue`（基于循环数组）或 `DoubleLinkedQueue`（基于双向链表）。
- **适用场景**：任务调度、消息队列、广度优先搜索等。
- **基本用法**：
  ```dart
  import 'dart:collection';

  void main() {
    var queue = Queue<String>();
    queue.add('first');    // 添加到队尾
    queue.add('second');
    queue.addFirst('start'); // 添加到队首
    print(queue); // {start, first, second}

    var first = queue.removeFirst(); // 'start'
    var last = queue.removeLast();   // 'second'
  }
  ```
- **特点**：添加/删除首尾元素时间复杂度 O(1)，但随机访问较慢（O(n)）。

### 5.2 双向链表（LinkedList）
- **概念**：`LinkedList` 是一个基于节点的双向链表，每个元素必须是 `LinkedListEntry` 的子类（如 `LinkedListEntry`）。允许在任意位置高效插入/删除（需持有节点引用），但随机访问仍需遍历。
- **适用场景**：需要频繁在列表中间插入/删除，且不需要随机索引的场景。
- **注意**：链表中的每个元素必须唯一，因为节点本身是元素，不能重复加入。
- **示例**：
  ```dart
  import 'dart:collection';

  class Task extends LinkedListEntry<Task> {
    final String name;
    Task(this.name);
    @override
    String toString() => name;
  }

  void main() {
    var list = LinkedList<Task>();
    var task1 = Task('write');
    var task2 = Task('test');
    list.add(task1);
    list.add(task2);
    print(list); // (write, test)

    task1.insertAfter(Task('debug')); // 在 task1 后插入
    task2.insertBefore(Task('review')); // 在 task2 前插入
    print(list); // (write, debug, review, test)
  }
  ```

### 5.3 哈希集合与映射的可选实现
- **HashSet** / **HashMap**：不保证任何顺序，仅基于哈希码，性能略高于 `LinkedHashSet`/`LinkedHashMap`，但迭代顺序不确定。
- **SplayTreeSet** / **SplayTreeMap**：基于伸展树实现，元素按键的自然顺序或自定义比较器排序，支持范围查询等操作。查找、插入、删除时间复杂度 O(log n)。
- **LinkedHashSet** / **LinkedHashMap**：默认实现，保留插入顺序。
- **选择建议**：
  - 需要顺序：`LinkedHashSet`/`LinkedHashMap`。
  - 仅需快速查找，不关心顺序：`HashSet`/`HashMap`。
  - 需要排序或范围操作：`SplayTreeSet`/`SplayTreeMap`。

## 6. 不可变集合与视图

### 6.1 不可变集合（Unmodifiable）
- **`List.unmodifiable`**：创建不可修改的列表，任何修改操作（如 `add`、`[]=`）都会抛出 `UnsupportedError`。
  ```dart
  var list = List.unmodifiable([1, 2, 3]);
  // list[0] = 0; // 错误！
  ```
- **`Set.unmodifiable`** / **`Map.unmodifiable`** 类似。
- **const 集合**：编译时常量，完全不可变且共享实例。
  ```dart
  const list = [1, 2, 3]; // 深度不可变（元素也必须是常量）
  ```

### 6.2 视图（View）
某些操作返回原集合的视图，而非新集合，修改视图可能影响原集合（或抛出异常）。
- **`List.asMap()`**：返回列表的映射视图，键为索引，值为元素。修改映射会修改原列表。
- **`Map.keys` / `Map.values`**：返回键/值的 `Iterable` 视图，修改原映射会反映在视图中。
- **`List.sublist()`** 返回新列表（拷贝），不是视图。
- **`List.skip()` / `List.take()`** 等返回 `Iterable`，是惰性视图。

## 7. 集合性能概览

| 集合类型             | 查找     | 插入/删除（末尾） | 插入/删除（任意位置） | 有序性         |
| -------------------- | -------- | ----------------- | --------------------- | -------------- |
| `List`（可增长）     | O(1)     | 分摊 O(1)         | O(n)                  | 索引顺序       |
| `List`（固定长度）   | O(1)     | 不支持            | 不支持                | 索引顺序       |
| `Queue`              | O(n)     | 首尾 O(1)         | 不支持直接中间插入    | FIFO           |
| `LinkedList`         | O(n)     | 首尾 O(1)         | O(1)（需持有节点）    | 链表顺序       |
| `HashSet`/`HashMap`  | 期望 O(1)| O(1)              | O(1)                  | 无             |
| `LinkedHashSet`/`LinkedHashMap` | 期望 O(1)| O(1)          | O(1)                  | 插入顺序       |
| `SplayTreeSet`/`SplayTreeMap` | O(log n) | O(log n)      | O(log n)              | 比较器顺序     |

> **说明**：哈希集合的插入/删除在冲突严重时可能退化为 O(n)，但通常可接受。

## 8. 集合与空安全深入

### 8.1 集合类型参数的可空性
- `List<int?>`：元素可为 `null`。
- `List<int>?`：列表本身可为 `null`，但元素不能为 `null`。
- `List<int?>?`：列表和元素均可为 `null`。

### 8.2 空安全操作
- 遍历可能为 `null` 的集合时，需先判空：
  ```dart
  List<int>? numbers = getNumbers();
  if (numbers != null) {
    for (var n in numbers) { ... }
  }
  ```
- 使用 `...?` 展开可能为 `null` 的集合。
- 使用 `?[]` 安全索引（Dart 3.3+）：
  ```dart
  List<int>? list = ...
  int? value = list?[0]; // 若 list 为 null 则返回 null
  ```

### 8.3 集合元素的可空性与类型提升
- 对 `List<int?>` 进行类型判断后，元素类型可能提升为 `int`。
  ```dart
  List<int?> nullableInts = [1, null, 3];
  for (var item in nullableInts) {
    if (item != null) {
      // 此处 item 类型提升为 int
      print(item + 1);
    }
  }
  ```

## 9. 集合转换与操作

Dart 为 `Iterable` 提供了丰富的函数式方法（`List`、`Set` 均实现 `Iterable`）。

### 9.1 常用转换方法
- **`map<T>(T f(E e))**：对每个元素应用函数，返回新 `Iterable`。
- **`where(bool test(E e))**：筛选符合条件的元素。
- **`expand<T>(Iterable<T> f(E e))**：将每个元素展开为多个元素。
- **`take(int count)**：取前 count 个元素。
- **`skip(int count)**：跳过前 count 个元素。
- **`toList()` / `toSet()`**：转换为新集合。
- **`cast<T>()**：类型转换（若实际类型兼容）。

### 9.2 归约与折叠
- **`reduce(E combine(E value, E element))**：从左到右归约，要求集合非空。
- **`fold<T>(T initialValue, T combine(T previous, E element))**：带初始值的归约。

### 9.3 检查与查找
- **`any(bool test(E e))**：是否存在满足条件的元素。
- **`every(bool test(E e))**：是否所有元素满足条件。
- **`firstWhere(bool test(E e), {E orElse()?})**：查找第一个满足条件的元素。
- **`singleWhere`**：查找唯一满足条件的元素，否则抛异常。

### 9.4 惰性求值
`map`、`where` 等方法返回的是 `Iterable`，是惰性求值的，仅在迭代时执行。需注意多次迭代可能重复计算。

### 9.5 示例
```dart
var numbers = [1, 2, 3, 4, 5];
var evenSquares = numbers
    .where((n) => n.isEven)
    .map((n) => n * n)
    .toList(); // [4, 16]

var sum = numbers.fold(0, (prev, n) => prev + n); // 15
```

## 10. 总结与最佳实践

### 10.1 选择合适的集合
- **有序、可重复、频繁索引访问** → `List`
- **唯一元素、成员关系测试、集合运算** → `Set`
- **键值对关联、快速查找** → `Map`
- **先进先出** → `Queue`
- **频繁中间插入/删除且不需要索引** → `LinkedList` 或 `List` 配合 `insert`/`removeAt`（但 O(n)）
- **需要排序/范围查询** → `SplayTreeSet` / `SplayTreeMap`
- **不关心顺序、追求极致性能** → `HashSet` / `HashMap`

### 10.2 代码可读性
- 使用集合字面量配合控制流元素（`if`、`for`、`...`）构建集合，使意图更清晰。
- 避免过深的嵌套控制流，可提取为辅助函数。
- 为自定义类型正确实现 `==` 和 `hashCode` 以在 `Set`/`Map` 中正常工作。

### 10.3 性能注意事项
- 预先分配 `List` 容量：`List.generate` 或 `List.filled` 指定长度。
- 对频繁查找的场景，使用 `Set` 而非 `List`（`List.contains` 是 O(n)）。
- 注意 `Iterable` 方法的惰性求值，避免多次迭代。
- 对于大量数据，考虑使用流式处理或分块加载。

### 10.4 空安全
- 始终考虑集合或元素的可空性，使用空感知运算符（`?.`、`...?`、`?[]`）安全处理。
- 利用类型提升在判空后简化代码。

### 10.5 不可变性与防御性编程
- 对于不希望被修改的集合，返回不可变视图（`List.unmodifiable`）或拷贝（`.toList()`）。
- 内部使用可变集合时，注意不要意外暴露引用导致外部修改。
