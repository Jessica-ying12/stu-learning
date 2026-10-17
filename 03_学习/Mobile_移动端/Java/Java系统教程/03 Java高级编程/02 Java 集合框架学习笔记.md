## 一、集合框架概述

### 1.1 历史背景

**Java 2 之前的特设类：**

- `Dictionary`
- `Vector`
- `Stack`
- `Properties`

**存在的问题：**

- 缺少核心统一的主题
- 使用方式差异很大
- 没有统一的接口规范

### 1.2 集合框架的设计目标

|目标|说明|
|---|---|
|**高性能**|基本集合实现必须高效|
|**互操作性**|不同类型的集合以类似方式工作|
|**易扩展**|扩展和适应必须简单|
|**标准接口**|围绕标准接口设计|

### 1.3 集合框架的位置

- **包路径**：`java.util`
- **使用前**：需要导入包

```java
import java.util.*;
// 或导入具体的类
import java.util.ArrayList;
import java.util.HashMap;
```

## 二、集合框架结构

### 2.1 两大根接口

```
Java 集合框架
├── Collection 接口（单列集合）
│   ├── List（有序，可重复）
│   ├── Set（无序，不可重复）
│   └── Queue（队列）
│
└── Map 接口（双列集合，键值对）
    ├── HashMap
    ├── TreeMap
    └── ...
```

### 2.2 框架组成

|组成部分|说明|表示方式|
|---|---|---|
|**接口**|6个接口|短虚线|
|**抽象类**|5个抽象类|长虚线|
|**实现类**|8个实现类|实线|

### 2.3 三大集合类型

|类型|特点|访问方式|
|---|---|---|
|**List**|有序，可重复|通过索引访问|
|**Set**|无序，不可重复|通过元素本身访问|
|**Map**|键值对|通过 Key 访问 Value|

## 三、集合接口

### 3.1 核心接口总览

|序号|接口|说明|
|---|---|---|
|1|`Collection`|集合层次结构的根接口|
|2|`List`|有序集合，允许重复元素|
|3|`Set`|不包含重复元素的集合|
|4|`SortedSet`|保存有序的集合|
|5|`Map`|键值对映射|
|6|`Map.Entry`|Map 中的一个元素（键值对）|
|7|`SortedMap`|Key 保持升序排列的 Map|
|8|`Enumeration`|传统接口，已被迭代器取代|

### 3.2 Collection 接口

```java
public interface Collection<E> extends Iterable<E> {
    // 基本操作
    int size();
    boolean isEmpty();
    boolean contains(Object o);
    boolean add(E e);
    boolean remove(Object o);
    
    // 批量操作
    boolean containsAll(Collection<?> c);
    boolean addAll(Collection<? extends E> c);
    boolean removeAll(Collection<?> c);
    void clear();
    
    // 数组操作
    Object[] toArray();
    <T> T[] toArray(T[] a);
    
    // 迭代器
    Iterator<E> iterator();
}
```

### 3.3 List 接口

```java
public interface List<E> extends Collection<E> {
    // 位置访问操作
    E get(int index);
    E set(int index, E element);
    void add(int index, E element);
    E remove(int index);
    
    // 搜索操作
    int indexOf(Object o);
    int lastIndexOf(Object o);
    
    // 列表迭代器
    ListIterator<E> listIterator();
    ListIterator<E> listIterator(int index);
    
    // 子列表
    List<E> subList(int fromIndex, int toIndex);
}
```

### 3.4 Set 接口

```java
public interface Set<E> extends Collection<E> {
    // Set 不添加新方法，只是强调不允许重复元素
    boolean add(E e);  // 如果元素已存在，返回 false
}
```

### 3.5 Map 接口

```java
public interface Map<K, V> {
    // 基本操作
    V put(K key, V value);
    V get(Object key);
    V remove(Object key);
    boolean containsKey(Object key);
    boolean containsValue(Object value);
    
    // 批量操作
    void putAll(Map<? extends K, ? extends V> m);
    void clear();
    
    // 视图
    Set<K> keySet();
    Collection<V> values();
    Set<Map.Entry<K, V>> entrySet();
    
    // 内部接口
    interface Entry<K, V> {
        K getKey();
        V getValue();
        V setValue(V value);
    }
}
```

## 四、集合实现类

### 4.1 List 实现类

|类名|特点|使用场景|
|---|---|---|
|**ArrayList**|动态数组，查询快|频繁查询|
|**LinkedList**|双向链表，增删快|频繁增删|
|**Vector**|线程安全的动态数组|多线程环境（已过时）|
|**Stack**|栈（后进先出）|栈操作|

#### ArrayList 示例

```java
import java.util.ArrayList;
import java.util.List;

public class ArrayListDemo {
    public static void main(String[] args) {
        // 创建 ArrayList
        List<String> list = new ArrayList<>();
        
        // 添加元素
        list.add("Apple");
        list.add("Banana");
        list.add("Cherry");
        
        // 访问元素
        System.out.println("第一个元素: " + list.get(0));
        
        // 修改元素
        list.set(1, "Blueberry");
        
        // 删除元素
        list.remove(2);
        
        // 遍历
        for (String fruit : list) {
            System.out.println(fruit);
        }
        
        // 大小
        System.out.println("大小: " + list.size());
    }
}
```

#### LinkedList 示例

```java
import java.util.LinkedList;

public class LinkedListDemo {
    public static void main(String[] args) {
        LinkedList<String> list = new LinkedList<>();
        
        // 添加元素
        list.add("A");
        list.add("B");
        list.add("C");
        
        // 在开头添加
        list.addFirst("Start");
        
        // 在末尾添加
        list.addLast("End");
        
        // 获取并删除第一个元素
        String first = list.removeFirst();
        System.out.println("删除的第一个元素: " + first);
        
        // 获取并删除最后一个元素
        String last = list.removeLast();
        System.out.println("删除的最后一个元素: " + last);
        
        System.out.println("剩余元素: " + list);
    }
}
```

### 4.2 Set 实现类

|类名|特点|使用场景|
|---|---|---|
|**HashSet**|基于哈希表，无序|快速查找，不关心顺序|
|**LinkedHashSet**|维护插入顺序|需要保持插入顺序|
|**TreeSet**|基于红黑树，有序|需要排序|

#### HashSet 示例

```java
import java.util.HashSet;
import java.util.Set;

public class HashSetDemo {
    public static void main(String[] args) {
        Set<String> set = new HashSet<>();
        
        // 添加元素
        set.add("Apple");
        set.add("Banana");
        set.add("Cherry");
        set.add("Apple");  // 重复元素，不会被添加
        
        System.out.println("Set 大小: " + set.size());  // 输出: 3
        
        // 检查元素
        System.out.println("包含 Banana? " + set.contains("Banana"));
        
        // 删除元素
        set.remove("Banana");
        
        // 遍历
        for (String fruit : set) {
            System.out.println(fruit);
        }
    }
}
```

#### TreeSet 示例

```java
import java.util.TreeSet;

public class TreeSetDemo {
    public static void main(String[] args) {
        TreeSet<Integer> set = new TreeSet<>();
        
        // 添加元素（自动排序）
        set.add(5);
        set.add(2);
        set.add(8);
        set.add(1);
        
        System.out.println("TreeSet: " + set);  // 输出: [1, 2, 5, 8]
        
        // 获取第一个和最后一个元素
        System.out.println("第一个: " + set.first());
        System.out.println("最后一个: " + set.last());
        
        // 获取子集
        System.out.println("小于 5 的元素: " + set.headSet(5));
        System.out.println("大于等于 5 的元素: " + set.tailSet(5));
    }
}
```

### 4.3 Map 实现类

|类名|特点|使用场景|
|---|---|---|
|**HashMap**|基于哈希表，无序|快速查找|
|**LinkedHashMap**|维护插入顺序|需要保持插入顺序|
|**TreeMap**|基于红黑树，有序|需要排序|
|**Hashtable**|线程安全（已过时）|多线程环境|

#### HashMap 示例

```java
import java.util.HashMap;
import java.util.Map;

public class HashMapDemo {
    public static void main(String[] args) {
        Map<String, Integer> map = new HashMap<>();
        
        // 添加键值对
        map.put("Apple", 10);
        map.put("Banana", 20);
        map.put("Cherry", 30);
        
        // 获取值
        System.out.println("Apple 的数量: " + map.get("Apple"));
        
        // 检查键
        System.out.println("包含 Banana? " + map.containsKey("Banana"));
        
        // 检查值
        System.out.println("包含值 30? " + map.containsValue(30));
        
        // 遍历方式1：遍历键
        for (String key : map.keySet()) {
            System.out.println(key + " = " + map.get(key));
        }
        
        // 遍历方式2：遍历键值对
        for (Map.Entry<String, Integer> entry : map.entrySet()) {
            System.out.println(entry.getKey() + " = " + entry.getValue());
        }
        
        // 删除
        map.remove("Banana");
        
        System.out.println("大小: " + map.size());
    }
}
```

#### TreeMap 示例

```java
import java.util.TreeMap;

public class TreeMapDemo {
    public static void main(String[] args) {
        TreeMap<String, Integer> map = new TreeMap<>();
        
        // 添加元素（按 Key 自动排序）
        map.put("Charlie", 30);
        map.put("Alice", 25);
        map.put("Bob", 28);
        
        System.out.println("TreeMap: " + map);
        // 输出: {Alice=25, Bob=28, Charlie=30}
        
        // 获取第一个和最后一个键
        System.out.println("第一个键: " + map.firstKey());
        System.out.println("最后一个键: " + map.lastKey());
        
        // 获取子映射
        System.out.println("小于 'C' 的键: " + map.headMap("C"));
    }
}
```

## 五、集合类对比

### 5.1 List 实现类对比

|特性|ArrayList|LinkedList|Vector|
|---|---|---|---|
|**底层结构**|动态数组|双向链表|动态数组|
|**查询速度**|快 O(1)|慢 O(n)|快 O(1)|
|**增删速度**|慢 O(n)|快 O(1)|慢 O(n)|
|**线程安全**|否|否|是|
|**推荐使用**|✅|✅|❌（已过时）|

### 5.2 Set 实现类对比

|特性|HashSet|LinkedHashSet|TreeSet|
|---|---|---|---|
|**底层结构**|哈希表|哈希表+链表|红黑树|
|**是否有序**|无序|插入顺序|自然顺序|
|**性能**|最快|较快|较慢|
|**允许 null**|是|是|否|

### 5.3 Map 实现类对比

|特性|HashMap|LinkedHashMap|TreeMap|Hashtable|
|---|---|---|---|---|
|**底层结构**|哈希表|哈希表+链表|红黑树|哈希表|
|**是否有序**|无序|插入顺序|Key 排序|无序|
|**允许 null**|Key/Value 都可|Key/Value 都可|Value 可|都不可|
|**线程安全**|否|否|否|是|
|**推荐使用**|✅|✅|✅|❌（已过时）|

## 六、迭代器（Iterator）

### 6.1 Iterator 接口

```java
public interface Iterator<E> {
    boolean hasNext();  // 是否有下一个元素
    E next();           // 返回下一个元素
    void remove();      // 删除当前元素
}
```

### 6.2 Iterator 使用示例

```java
import java.util.ArrayList;
import java.util.Iterator;
import java.util.List;

public class IteratorDemo {
    public static void main(String[] args) {
        List<String> list = new ArrayList<>();
        list.add("A");
        list.add("B");
        list.add("C");
        
        // 获取迭代器
        Iterator<String> iterator = list.iterator();
        
        // 遍历
        while (iterator.hasNext()) {
            String element = iterator.next();
            System.out.println(element);
            
            // 删除元素
            if (element.equals("B")) {
                iterator.remove();
            }
        }
        
        System.out.println("删除后: " + list);
    }
}
```

### 6.3 ListIterator 接口

```java
public interface ListIterator<E> extends Iterator<E> {
    // Iterator 的方法
    boolean hasNext();
    E next();
    void remove();
    
    // ListIterator 新增的方法
    boolean hasPrevious();  // 是否有前一个元素
    E previous();           // 返回前一个元素
    int nextIndex();        // 下一个元素的索引
    int previousIndex();    // 前一个元素的索引
    void set(E e);          // 替换当前元素
    void add(E e);          // 插入元素
}
```

### 6.4 ListIterator 使用示例

```java
import java.util.ArrayList;
import java.util.List;
import java.util.ListIterator;

public class ListIteratorDemo {
    public static void main(String[] args) {
        List<String> list = new ArrayList<>();
        list.add("A");
        list.add("B");
        list.add("C");
        
        // 获取列表迭代器
        ListIterator<String> iterator = list.listIterator();
        
        // 正向遍历
        System.out.println("正向遍历:");
        while (iterator.hasNext()) {
            System.out.println(iterator.next());
        }
        
        // 反向遍历
        System.out.println("\n反向遍历:");
        while (iterator.hasPrevious()) {
            System.out.println(iterator.previous());
        }
    }
}
```

### 6.5 增强 for 循环

```java
import java.util.ArrayList;
import java.util.List;

public class ForEachDemo {
    public static void main(String[] args) {
        List<String> list = new ArrayList<>();
        list.add("A");
        list.add("B");
        list.add("C");
        
        // 增强 for 循环（内部使用迭代器）
        for (String element : list) {
            System.out.println(element);
        }
    }
}
```

## 七、集合算法（Collections 类）

### 7.1 常用算法

|方法|说明|
|---|---|
|`sort(List)`|排序|
|`reverse(List)`|反转|
|`shuffle(List)`|随机打乱|
|`binarySearch(List, Object)`|二分查找|
|`max(Collection)`|最大值|
|`min(Collection)`|最小值|
|`frequency(Collection, Object)`|出现次数|
|`copy(List dest, List src)`|复制|
|`fill(List, Object)`|填充|

### 7.2 Collections 示例

```java
import java.util.ArrayList;
import java.util.Collections;
import java.util.List;

public class CollectionsDemo {
    public static void main(String[] args) {
        List<Integer> list = new ArrayList<>();
        list.add(5);
        list.add(2);
        list.add(8);
        list.add(1);
        list.add(9);
        
        System.out.println("原始列表: " + list);
        
        // 排序
        Collections.sort(list);
        System.out.println("排序后: " + list);
        
        // 反转
        Collections.reverse(list);
        System.out.println("反转后: " + list);
        
        // 打乱
        Collections.shuffle(list);
        System.out.println("打乱后: " + list);
        
        // 最大值和最小值
        System.out.println("最大值: " + Collections.max(list));
        System.out.println("最小值: " + Collections.min(list));
        
        // 二分查找（需要先排序）
        Collections.sort(list);
        int index = Collections.binarySearch(list, 5);
        System.out.println("5 的索引: " + index);
        
        // 出现次数
        list.add(5);
        System.out.println("5 出现的次数: " + Collections.frequency(list, 5));
    }
}
```

### 7.3 不可变集合

```java
import java.util.ArrayList;
import java.util.Collections;
import java.util.List;

public class UnmodifiableDemo {
    public static void main(String[] args) {
        List<String> list = new ArrayList<>();
        list.add("A");
        list.add("B");
        list.add("C");
        
        // 创建不可修改的集合
        List<String> unmodifiableList = Collections.unmodifiableList(list);
        
        System.out.println(unmodifiableList);
        
        // 尝试修改会抛出异常
        try {
            unmodifiableList.add("D");
        } catch (UnsupportedOperationException e) {
            System.out.println("不能修改不可变集合！");
        }
    }
}
```

## 八、总结

### 8.1 核心要点

|要点|说明|
|---|---|
|**Collection**|单列集合的根接口|
|**Map**|双列集合的根接口|
|**List**|有序可重复|
|**Set**|无序不可重复|
|**Iterator**|遍历集合的标准方式|
|**Collections**|集合工具类|

### 8.2 选择建议

**选择 List：**

- 需要按索引访问元素
- 允许重复元素
- 需要保持插入顺序

**选择 Set：**

- 不允许重复元素
- 不关心元素顺序（HashSet）
- 需要排序（TreeSet）

**选择 Map：**

- 需要键值对存储
- 通过键快速查找值

### 8.3 记忆口诀

- **List 有序可重复**：ArrayList 查询快，LinkedList 增删快
- **Set 无序不重复**：HashSet 最常用，TreeSet 能排序
- **Map 键值对**：HashMap 最常用，TreeMap 能排序
- **Iterator 来遍历**：hasNext 和 next 配合用
- **Collections 工具类**：排序查找很方便