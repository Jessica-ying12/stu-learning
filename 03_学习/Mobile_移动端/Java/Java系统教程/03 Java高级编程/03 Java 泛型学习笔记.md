## 一、泛型概述

### 1.1 什么是泛型？

**泛型(Generics)**：参数化类型，将类型作为参数传递，在编译时提供类型安全检查。

### 1.2 为什么使用泛型？

|优势|说明|
|---|---|
|**类型安全**|编译时检查类型错误|
|**消除强制转换**|不需要显式类型转换|
|**代码复用**|一套代码适用多种类型|
|**更好的可读性**|代码意图更清晰|

### 1.3 泛型的本质

**参数化类型**：在不创建新类型的情况下，通过泛型指定不同类型来控制形参的具体类型。

```java
// 没有泛型：需要强制转换
List list = new ArrayList();
list.add("Hello");
String str = (String) list.get(0);  // 需要强制转换

// 使用泛型：类型安全，无需转换
List<String> list = new ArrayList<>();
list.add("Hello");
String str = list.get(0);  // 无需转换
```

---

## 二、泛型的特性

### 2.1 泛型只在编译阶段有效

**类型擦除(Type Erasure)**：泛型信息只在编译时存在，运行时会被擦除。

```java
public class GenericTest {
    public static void main(String[] args) {
        List<String> stringList = new ArrayList<>();
        List<Integer> integerList = new ArrayList<>();
        
        Class classString = stringList.getClass();
        Class classInteger = integerList.getClass();
        
        if (classString.equals(classInteger)) {
            System.out.println("类型相同");  // 输出：类型相同
        }
    }
}
```

**结论**：

- ✅ 泛型类型在逻辑上可以看成多个不同类型
- ✅ 实际上都是相同的基本类型
- ✅ 泛型信息不会进入运行时阶段

### 2.2 类型擦除的影响

```java
// 编译前
List<String> list = new ArrayList<>();
list.add("Hello");
String str = list.get(0);

// 编译后（类型擦除）
List list = new ArrayList();
list.add("Hello");
String str = (String) list.get(0);  // 编译器自动添加转换
```

---

## 三、泛型方法

### 3.1 定义规则

|规则|说明|
|---|---|
|**类型参数声明**|在方法返回类型之前用 `<>` 声明|
|**类型参数位置**|可用于返回值、参数、方法体|
|**命名约定**|通常使用单个大写字母（T, E, K, V）|
|**引用类型**|只能代表引用类型，不能是基本类型|

### 3.2 基本语法

```java
public <T> T methodName(T param) {
    // 方法体
}
```

### 3.3 完整示例

```java
public class GenericMethodTest {
    // 泛型方法：打印数组
    public static <E> void printArray(E[] inputArray) {
        for (E element : inputArray) {
            System.out.printf("%s ", element);
        }
        System.out.println();
    }
    
    public static void main(String args[]) {
        // 整型数组
        Integer[] intArray = {1, 2, 3, 4, 5};
        System.out.println("Array integerArray contains:");
        printArray(intArray);
        
        // 双精度数组
        Double[] doubleArray = {1.1, 2.2, 3.3, 4.4};
        System.out.println("\nArray doubleArray contains:");
        printArray(doubleArray);
        
        // 字符数组
        Character[] charArray = {'H', 'E', 'L', 'L', 'O'};
        System.out.println("\nArray characterArray contains:");
        printArray(charArray);
    }
}
```

**输出：**

```
Array integerArray contains:
1 2 3 4 5 

Array doubleArray contains:
1.1 2.2 3.3 4.4 

Array characterArray contains:
H E L L O 
```

### 3.4 多个类型参数

```java
public class Pair<K, V> {
    private K key;
    private V value;
    
    public Pair(K key, V value) {
        this.key = key;
        this.value = value;
    }
    
    public K getKey() {
        return key;
    }
    
    public V getValue() {
        return value;
    }
    
    // 泛型方法
    public static <K, V> boolean compare(Pair<K, V> p1, Pair<K, V> p2) {
        return p1.getKey().equals(p2.getKey()) &&
               p1.getValue().equals(p2.getValue());
    }
    
    public static void main(String[] args) {
        Pair<Integer, String> p1 = new Pair<>(1, "Apple");
        Pair<Integer, String> p2 = new Pair<>(2, "Banana");
        
        boolean result = Pair.compare(p1, p2);
        System.out.println("Are pairs equal? " + result);  // false
    }
}
```

---

## 四、有界类型参数

### 4.1 什么是有界类型？

**有界类型参数**：限制可以传递给类型参数的类型范围。

### 4.2 语法

```java
<T extends UpperBound>
```

**注意**：`extends` 在这里表示"继承"（类）或"实现"（接口）

### 4.3 单个边界示例

```java
public class MaximumTest {
    // 比较三个值并返回最大值
    public static <T extends Comparable<T>> T maximum(T x, T y, T z) {
        T max = x;  // 假设 x 是初始最大值
        
        if (y.compareTo(max) > 0) {
            max = y;  // y 更大
        }
        
        if (z.compareTo(max) > 0) {
            max = z;  // z 更大
        }
        
        return max;  // 返回最大对象
    }
    
    public static void main(String args[]) {
        System.out.printf("Max of %d, %d and %d is %d\n\n",
                3, 4, 5, maximum(3, 4, 5));
        
        System.out.printf("Max of %.1f, %.1f and %.1f is %.1f\n\n",
                6.6, 8.8, 7.7, maximum(6.6, 8.8, 7.7));
        
        System.out.printf("Max of %s, %s and %s is %s\n",
                "pear", "apple", "orange", maximum("pear", "apple", "orange"));
    }
}
```

**输出：**

```
Max of 3, 4 and 5 is 5

Max of 6.6, 8.8 and 7.7 is 8.8

Max of pear, apple and orange is pear
```

### 4.4 多个边界

```java
// 多个边界：T 必须同时满足所有条件
public class MultipleBouns {
    public static <T extends Number & Comparable<T>> T findMax(T a, T b) {
        return a.compareTo(b) > 0 ? a : b;
    }
    
    public static void main(String[] args) {
        Integer max1 = findMax(10, 20);
        Double max2 = findMax(3.14, 2.71);
        
        System.out.println("Max integer: " + max1);  // 20
        System.out.println("Max double: " + max2);   // 3.14
    }
}
```

**注意**：

- 类必须放在第一位
- 接口可以有多个
- 用 `&` 连接

---

## 五、泛型类

### 5.1 定义语法

```java
public class ClassName<T> {
    private T data;
    
    public void set(T data) {
        this.data = data;
    }
    
    public T get() {
        return data;
    }
}
```

### 5.2 基本示例

```java
public class Box<T> {
    private T t;
    
    public void add(T t) {
        this.t = t;
    }
    
    public T get() {
        return t;
    }
    
    public static void main(String[] args) {
        Box<Integer> integerBox = new Box<>();
        Box<String> stringBox = new Box<>();
        
        integerBox.add(new Integer(10));
        stringBox.add(new String("Hello World"));
        
        System.out.printf("Integer Value: %d\n\n", integerBox.get());
        System.out.printf("String Value: %s\n", stringBox.get());
    }
}
```

**输出：**

```
Integer Value: 10

String Value: Hello World
```

### 5.3 多类型参数的泛型类

```java
public class Pair<K, V> {
    private K key;
    private V value;
    
    public Pair(K key, V value) {
        this.key = key;
        this.value = value;
    }
    
    public K getKey() {
        return key;
    }
    
    public V getValue() {
        return value;
    }
    
    public void setKey(K key) {
        this.key = key;
    }
    
    public void setValue(V value) {
        this.value = value;
    }
    
    @Override
    public String toString() {
        return "Pair{" + "key=" + key + ", value=" + value + '}';
    }
}

// 使用
public class TestPair {
    public static void main(String[] args) {
        Pair<String, Integer> pair1 = new Pair<>("Age", 25);
        Pair<String, String> pair2 = new Pair<>("Name", "John");
        
        System.out.println(pair1);  // Pair{key=Age, value=25}
        System.out.println(pair2);  // Pair{key=Name, value=John}
    }
}
```

### 5.4 泛型类的继承

```java
// 父类
public class Parent<T> {
    private T value;
    
    public T getValue() {
        return value;
    }
    
    public void setValue(T value) {
        this.value = value;
    }
}

// 子类1：保留泛型
public class Child1<T> extends Parent<T> {
    // 继承父类的泛型
}

// 子类2：指定具体类型
public class Child2 extends Parent<String> {
    // 指定父类泛型为 String
}

// 子类3：添加新的泛型参数
public class Child3<T, E> extends Parent<T> {
    private E extra;
    
    public E getExtra() {
        return extra;
    }
    
    public void setExtra(E extra) {
        this.extra = extra;
    }
}
```

---

## 六、泛型接口

### 6.1 定义泛型接口

```java
public interface Generator<T> {
    T generate();
}
```

### 6.2 实现泛型接口

```java
// 方式1：实现时指定具体类型
public class StringGenerator implements Generator<String> {
    @Override
    public String generate() {
        return "Generated String";
    }
}

// 方式2：实现时保留泛型
public class GenericGenerator<T> implements Generator<T> {
    @Override
    public T generate() {
        // 实现逻辑
        return null;
    }
}
```

### 6.3 完整示例

```java
// 泛型接口
public interface Container<T> {
    void add(T item);
    T get(int index);
    int size();
}

// 实现类
public class ListContainer<T> implements Container<T> {
    private List<T> list = new ArrayList<>();
    
    @Override
    public void add(T item) {
        list.add(item);
    }
    
    @Override
    public T get(int index) {
        return list.get(index);
    }
    
    @Override
    public int size() {
        return list.size();
    }
}

// 使用
public class Test {
    public static void main(String[] args) {
        Container<String> container = new ListContainer<>();
        container.add("Apple");
        container.add("Banana");
        
        System.out.println(container.get(0));  // Apple
        System.out.println(container.size());  // 2
    }
}
```

---

## 七、通配符

### 7.1 通配符类型

|通配符|说明|示例|
|---|---|---|
|`?`|无界通配符|`List<?>`|
|`? extends T`|上界通配符|`List<? extends Number>`|
|`? super T`|下界通配符|`List<? super Integer>`|

### 7.2 无界通配符

```java
public class WildcardTest {
    public static void printList(List<?> list) {
        for (Object obj : list) {
            System.out.print(obj + " ");
        }
        System.out.println();
    }
    
    public static void main(String[] args) {
        List<Integer> intList = Arrays.asList(1, 2, 3);
        List<String> strList = Arrays.asList("A", "B", "C");
        
        printList(intList);  // 1 2 3
        printList(strList);  // A B C
    }
}
```

### 7.3 上界通配符（extends）

```java
public class UpperBoundedWildcard {
    // 只能读取，不能添加（除了 null）
    public static double sumOfList(List<? extends Number> list) {
        double sum = 0.0;
        for (Number num : list) {
            sum += num.doubleValue();
        }
        return sum;
    }
    
    public static void main(String[] args) {
        List<Integer> intList = Arrays.asList(1, 2, 3);
        List<Double> doubleList = Arrays.asList(1.1, 2.2, 3.3);
        
        System.out.println("Sum of integers: " + sumOfList(intList));
        System.out.println("Sum of doubles: " + sumOfList(doubleList));
    }
}
```

### 7.4 下界通配符（super）

```java
public class LowerBoundedWildcard {
    // 可以添加，但读取时只能作为 Object
    public static void addNumbers(List<? super Integer> list) {
        list.add(1);
        list.add(2);
        list.add(3);
    }
    
    public static void main(String[] args) {
        List<Number> numberList = new ArrayList<>();
        List<Object> objectList = new ArrayList<>();
        
        addNumbers(numberList);
        addNumbers(objectList);
        
        System.out.println(numberList);  // [1, 2, 3]
        System.out.println(objectList);  // [1, 2, 3]
    }
}
```

### 7.5 PECS 原则

**PECS**: Producer Extends, Consumer Super

```java
// Producer（生产者）：使用 extends
public static void copy1(List<? extends Number> src, List<Number> dest) {
    for (Number num : src) {
        dest.add(num);  // 从 src 读取
    }
}

// Consumer（消费者）：使用 super
public static void copy2(List<Number> src, List<? super Number> dest) {
    for (Number num : src) {
        dest.add(num);  // 向 dest 写入
    }
}
```

---

## 八、泛型的限制

### 8.1 不能使用基本类型

```java
// ❌ 错误
List<int> list = new ArrayList<>();

// ✅ 正确：使用包装类
List<Integer> list = new ArrayList<>();
```

### 8.2 不能创建泛型数组

```java
// ❌ 错误
List<String>[] array = new List<String>[10];

// ✅ 正确：使用通配符
List<?>[] array = new List<?>[10];
```

### 8.3 不能实例化类型参数

```java
public class GenericClass<T> {
    // ❌ 错误
    // T obj = new T();
    
    // ✅ 正确：使用反射
    public T createInstance(Class<T> clazz) throws Exception {
        return clazz.newInstance();
    }
}
```

### 8.4 不能使用 instanceof

```java
// ❌ 错误
if (obj instanceof List<String>) { }

// ✅ 正确
if (obj instanceof List<?>) { }
```

---

## 九、重点总结

### 9.1 泛型的核心概念

1. **参数化类型**：将类型作为参数传递
2. **类型擦除**：泛型信息只在编译时存在
3. **类型安全**：编译时检查类型错误
4. **代码复用**：一套代码适用多种类型

### 9.2 泛型命名约定

|字母|含义|
|---|---|
|T|Type（类型）|
|E|Element（元素）|
|K|Key（键）|
|V|Value（值）|
|N|Number（数字）|
|?|通配符|

### 9.3 最佳实践

```java
// ✅ 推荐：使用泛型
List<String> list = new ArrayList<>();

// ✅ 推荐：使用钻石操作符（Java 7+）
Map<String, List<Integer>> map = new HashMap<>();

// ✅ 推荐：使用有界类型参数
public <T extends Comparable<T>> T max(T a, T b) {
    return a.compareTo(b) > 0 ? a : b;
}

// ✅ 推荐：使用通配符
public void process(List<? extends Number> list) {
    // 处理逻辑
}
```

### 9.4 记忆口诀

```
泛型参数化类型，编译检查更安全
类型擦除运行时，逻辑多个实际一
方法类接口都可用，T E K V 是约定
extends 上界 super 下，PECS 原则要记牢
基本类型不能用，包装类来替代它
```