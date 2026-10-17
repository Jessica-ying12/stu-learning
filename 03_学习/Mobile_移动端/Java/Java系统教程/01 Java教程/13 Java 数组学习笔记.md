## 一、数组概述

### 1.1 什么是数组

- 数组是用来存储**固定大小**的**同类型元素**的数据结构
- 数组是每一门编程语言的重要数据结构之一
- 可以用一个数组变量代替多个独立变量

### 1.2 数组的特点

- **固定大小**：创建后大小不可改变
- **同类型**：只能存储相同数据类型的元素
- **有序集合**：元素按索引顺序存储
- **索引从 0 开始**：第一个元素索引为 0
- **引用类型**：数组是对象，需要实例化

---

## 二、声明数组

### 2.1 声明语法

```java
// 方式1：推荐
dataType[] arrayRefVar;

// 方式2：C/C++ 风格（不推荐）
dataType arrayRefVar[];
```

### 2.2 声明示例

```java
// 推荐的声明方式
double[] myList;
int[] numbers;
String[] names;

// C/C++ 风格（不推荐）
double myList[];
int numbers[];
String names[];
```

**注意**：建议使用 `dataType[] arrayRefVar` 的声明风格。

---

## 三、创建数组

### 3.1 使用 new 操作符创建

```java
// 语法
arrayRefVar = new dataType[arraySize];

// 示例
int[] numbers = new int[5];
double[] scores = new double[10];
String[] names = new String[3];
```

### 3.2 声明和创建一步完成

```java
// 动态初始化
dataType[] arrayRefVar = new dataType[arraySize];

// 示例
int[] numbers = new int[5];
double[] myList = new double[10];
```

### 3.3 静态初始化

```java
// 语法
dataType[] arrayRefVar = {value0, value1, ..., valuek};

// 示例
int[] numbers = {1, 2, 3, 4, 5};
double[] myList = {1.9, 2.9, 3.4, 3.5};
String[] names = {"张三", "李四", "王五"};
```

### 3.4 分步创建数组

```java
public class ArrayDemo {
    public static void main(String args[]) {
        int data[] = null;           // 声明数组
        data = new int[3];           // 开辟空间
        data[0] = 10;                // 赋值
        data[1] = 20;
        data[2] = 30;
        
        for (int x = 0; x < data.length; x++) {
            System.out.println(data[x]);
        }
    }
}
```

---

## 四、访问数组元素

### 4.1 通过索引访问

```java
// 语法
arrayRefVar[index]

// 示例
int[] numbers = {10, 20, 30, 40, 50};
System.out.println(numbers[0]);  // 10
System.out.println(numbers[2]);  // 30
System.out.println(numbers[4]);  // 50
```

### 4.2 索引范围

- 索引从 **0** 开始
- 最大索引为 **arrayRefVar.length - 1**
- 超出范围会抛出 `ArrayIndexOutOfBoundsException` 异常

```java
int[] numbers = {10, 20, 30};
// 有效索引：0, 1, 2
System.out.println(numbers[0]);  // 10
System.out.println(numbers[2]);  // 30
// System.out.println(numbers[3]);  // 异常！
```

### 4.3 数组长度

```java
int[] numbers = {10, 20, 30, 40, 50};
System.out.println(numbers.length);  // 5

// 遍历数组
for (int i = 0; i < numbers.length; i++) {
    System.out.println(numbers[i]);
}
```

---

## 五、数组的默认值

### 5.1 基本类型数组的默认值

|数据类型|默认值|
|---|---|
|byte|0|
|short|0|
|int|0|
|long|0L|
|float|0.0f|
|double|0.0d|
|char|'\u0000'|
|boolean|false|

### 5.2 引用类型数组的默认值

```java
String[] names = new String[3];
System.out.println(names[0]);  // null
System.out.println(names[1]);  // null
System.out.println(names[2]);  // null
```

### 5.3 示例

```java
public class ArrayDemo {
    public static void main(String args[]) {
        int data[] = new int[3];
        // 未赋值，输出默认值
        for (int x = 0; x < data.length; x++) {
            System.out.println(data[x]);  // 0 0 0
        }
    }
}
```

---

## 六、处理数组

### 6.1 使用 for 循环

```java
public class TestArray {
    public static void main(String[] args) {
        double[] myList = {1.9, 2.9, 3.4, 3.5};
        
        // 打印所有数组元素
        for (int i = 0; i < myList.length; i++) {
            System.out.println(myList[i] + " ");
        }
        
        // 计算所有元素的总和
        double total = 0;
        for (int i = 0; i < myList.length; i++) {
            total += myList[i];
        }
        System.out.println("Total is " + total);
        
        // 查找最大元素
        double max = myList[0];
        for (int i = 1; i < myList.length; i++) {
            if (myList[i] > max) max = myList[i];
        }
        System.out.println("Max is " + max);
    }
}
```

**输出**：

```
1.9 
2.9 
3.4 
3.5 
Total is 11.7
Max is 3.5
```

### 6.2 使用 foreach 循环（增强型 for 循环）

#### 语法

```java
for (type element : array) {
    System.out.println(element);
}
```

#### 示例

```java
public class TestArray {
    public static void main(String[] args) {
        double[] myList = {1.9, 2.9, 3.4, 3.5};
        
        // 打印所有数组元素
        for (double element : myList) {
            System.out.println(element);
        }
    }
}
```

**输出**：

```
1.9
2.9
3.4
3.5
```

#### foreach 循环的特点

- JDK 1.5 引入
- 不使用下标遍历数组
- 代码更简洁
- 只能读取，不能修改数组元素

```java
int[] numbers = {1, 2, 3, 4, 5};

// ❌ 不能修改数组元素
for (int num : numbers) {
    num = num * 2;  // 不会改变数组
}

// ✅ 使用普通 for 循环修改
for (int i = 0; i < numbers.length; i++) {
    numbers[i] = numbers[i] * 2;
}
```

---

## 七、数组作为方法参数

### 7.1 传递数组给方法

```java
public static void printArray(int[] array) {
    for (int i = 0; i < array.length; i++) {
        System.out.print(array[i] + " ");
    }
}

// 调用方法
public static void main(String[] args) {
    int[] numbers = {3, 1, 2, 6, 4, 2};
    printArray(numbers);
    
    // 或者直接传递匿名数组
    printArray(new int[]{3, 1, 2, 6, 4, 2});
}
```

### 7.2 完整示例

```java
public class ArrayMethodDemo {
    public static void printArray(int[] array) {
        for (int i = 0; i < array.length; i++) {
            System.out.print(array[i] + " ");
        }
        System.out.println();
    }
    
    public static int sum(int[] array) {
        int total = 0;
        for (int num : array) {
            total += num;
        }
        return total;
    }
    
    public static void main(String[] args) {
        int[] numbers = {1, 2, 3, 4, 5};
        printArray(numbers);           // 1 2 3 4 5
        System.out.println(sum(numbers));  // 15
    }
}
```

---

## 八、数组作为方法返回值

### 8.1 返回数组

```java
public static int[] reverse(int[] list) {
    int[] result = new int[list.length];
    
    for (int i = 0, j = result.length - 1; i < list.length; i++, j--) {
        result[j] = list[i];
    }
    
    return result;
}
```

### 8.2 完整示例

```java
public class ArrayReturnDemo {
    public static int[] reverse(int[] list) {
        int[] result = new int[list.length];
        for (int i = 0, j = result.length - 1; i < list.length; i++, j--) {
            result[j] = list[i];
        }
        return result;
    }
    
    public static void main(String[] args) {
        int[] numbers = {1, 2, 3, 4, 5};
        int[] reversed = reverse(numbers);
        
        for (int num : reversed) {
            System.out.print(num + " ");  // 5 4 3 2 1
        }
    }
}
```

---

## 九、Arrays 类

### 9.1 Arrays 类概述

- 位于 `java.util.Arrays` 包
- 提供操作数组的静态方法
- 可以方便地对数组进行排序、查找、比较等操作

### 9.2 常用方法

#### 1. sort() - 排序

```java
import java.util.Arrays;

public class ArraysDemo {
    public static void main(String[] args) {
        int[] numbers = {5, 2, 8, 1, 9};
        Arrays.sort(numbers);
        
        for (int num : numbers) {
            System.out.print(num + " ");  // 1 2 5 8 9
        }
    }
}
```

#### 2. binarySearch() - 二分查找

```java
import java.util.Arrays;

public class ArraysDemo {
    public static void main(String[] args) {
        int[] numbers = {1, 2, 5, 8, 9};
        
        int index = Arrays.binarySearch(numbers, 5);
        System.out.println("索引: " + index);  // 2
        
        int notFound = Arrays.binarySearch(numbers, 7);
        System.out.println("未找到: " + notFound);  // 负数
    }
}
```

**注意**：使用 `binarySearch()` 前必须先排序！

#### 3. fill() - 填充数组

```java
import java.util.Arrays;

public class ArraysDemo {
    public static void main(String[] args) {
        int[] numbers = new int[5];
        Arrays.fill(numbers, 10);
        
        for (int num : numbers) {
            System.out.print(num + " ");  // 10 10 10 10 10
        }
    }
}
```

#### 4. equals() - 比较数组

```java
import java.util.Arrays;

public class ArraysDemo {
    public static void main(String[] args) {
        int[] arr1 = {1, 2, 3};
        int[] arr2 = {1, 2, 3};
        int[] arr3 = {1, 2, 4};
        
        System.out.println(Arrays.equals(arr1, arr2));  // true
        System.out.println(Arrays.equals(arr1, arr3));  // false
    }
}
```

#### 5. toString() - 转换为字符串

```java
import java.util.Arrays;

public class ArraysDemo {
    public static void main(String[] args) {
        int[] numbers = {1, 2, 3, 4, 5};
        System.out.println(Arrays.toString(numbers));  // [1, 2, 3, 4, 5]
    }
}
```

#### 6. copyOf() - 复制数组

```java
import java.util.Arrays;

public class ArraysDemo {
    public static void main(String[] args) {
        int[] original = {1, 2, 3, 4, 5};
        int[] copy = Arrays.copyOf(original, original.length);
        
        System.out.println(Arrays.toString(copy));  // [1, 2, 3, 4, 5]
        
        // 复制部分
        int[] partial = Arrays.copyOf(original, 3);
        System.out.println(Arrays.toString(partial));  // [1, 2, 3]
    }
}
```

### 9.3 Arrays 方法列表

|序号|方法|描述|
|---|---|---|
|1|`binarySearch(Object[] a, Object key)`|二分查找（数组必须已排序）|
|2|`equals(long[] a, long[] a2)`|比较两个数组是否相等|
|3|`fill(int[] a, int val)`|用指定值填充数组|
|4|`sort(Object[] a)`|对数组进行升序排序|
|5|`toString(int[] a)`|返回数组的字符串表示|
|6|`copyOf(int[] original, int newLength)`|复制数组|

---

## 十、常见错误和注意事项

### 10.1 空指针异常

```java
// ❌ 错误：未初始化数组
int[] data = null;
// System.out.println(data[0]);  // NullPointerException

// ✅ 正确：先初始化
int[] data = new int[3];
System.out.println(data[0]);  // 0
```

### 10.2 数组越界异常

```java
// ❌ 错误：索引越界
int[] numbers = {1, 2, 3};
// System.out.println(numbers[3]);  // ArrayIndexOutOfBoundsException

// ✅ 正确：检查索引范围
if (index >= 0 && index < numbers.length) {
    System.out.println(numbers[index]);
}
```

### 10.3 数组大小不可改变

```java
int[] numbers = new int[3];
// numbers.length = 5;  // 错误！length 是只读的

// ✅ 正确：创建新数组
numbers = new int[5];
```

---

## 十一、实用示例

### 11.1 查找数组中的最大值和最小值

```java
public class ArrayMinMax {
    public static void main(String[] args) {
        int[] numbers = {5, 2, 8, 1, 9, 3};
        
        int max = numbers[0];
        int min = numbers[0];
        
        for (int i = 1; i < numbers.length; i++) {
            if (numbers[i] > max) max = numbers[i];
            if (numbers[i] < min) min = numbers[i];
        }
        
        System.out.println("最大值: " + max);  // 9
        System.out.println("最小值: " + min);  // 1
    }
}
```

### 11.2 数组反转

```java
public class ArrayReverse {
    public static void main(String[] args) {
        int[] numbers = {1, 2, 3, 4, 5};
        
        int left = 0;
        int right = numbers.length - 1;
        
        while (left < right) {
            int temp = numbers[left];
            numbers[left] = numbers[right];
            numbers[right] = temp;
            left++;
            right--;
        }
        
        System.out.println(Arrays.toString(numbers));  // [5, 4, 3, 2, 1]
    }
}
```

### 11.3 数组复制

```java
public class ArrayCopy {
    public static void main(String[] args) {
        int[] source = {1, 2, 3, 4, 5};
        
        // 方式1：手动复制
        int[] copy1 = new int[source.length];
        for (int i = 0; i < source.length; i++) {
            copy1[i] = source[i];
        }
        
        // 方式2：使用 System.arraycopy()
        int[] copy2 = new int[source.length];
        System.arraycopy(source, 0, copy2, 0, source.length);
        
        // 方式3：使用 Arrays.copyOf()
        int[] copy3 = Arrays.copyOf(source, source.length);
        
        System.out.println(Arrays.toString(copy3));  // [1, 2, 3, 4, 5]
    }
}
```

### 11.4 统计数组元素出现次数

```java
public class ArrayCount {
    public static void main(String[] args) {
        int[] numbers = {1, 2, 3, 2, 1, 3, 2, 4};
        int target = 2;
        int count = 0;
        
        for (int num : numbers) {
            if (num == target) {
                count++;
            }
        }
        
        System.out.println(target + " 出现了 " + count + " 次");  // 2 出现了 3 次
    }
}
```

---

## 十二、重要知识点总结

### 12.1 数组的特点

1. **固定大小**：创建后不可改变
2. **同类型**：只能存储相同类型的元素
3. **索引从 0 开始**：第一个元素索引为 0
4. **引用类型**：数组是对象
5. **有默认值**：未赋值的元素有默认值

### 12.2 数组操作要点

```java
// 声明和创建
int[] arr = new int[5];

// 静态初始化
int[] arr = {1, 2, 3, 4, 5};

// 访问元素
int value = arr[0];

// 获取长度
int len = arr.length;

// 遍历数组
for (int i = 0; i < arr.length; i++) {
    System.out.println(arr[i]);
}

// foreach 循环
for (int num : arr) {
    System.out.println(num);
}
```

### 12.3 最佳实践

1. **优先使用 `dataType[]` 声明风格**
2. **使用 foreach 循环遍历（只读）**
3. **使用 Arrays 类的工具方法**
4. **注意数组越界和空指针异常**
5. **数组作为参数传递时是引用传递**

### 12.4 常见应用场景

- 存储一组相同类型的数据
- 作为方法参数和返回值
- 实现数据结构（栈、队列等）
- 批量处理数据

恭喜你完成了 Java 数组的学习！数组是 Java 编程的基础，掌握数组操作对后续学习集合框架非常重要。