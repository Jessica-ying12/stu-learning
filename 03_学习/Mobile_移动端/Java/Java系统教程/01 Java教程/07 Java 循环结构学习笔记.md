## 一、循环结构概述

### 1.1 为什么需要循环

- 顺序结构的程序语句只能被执行一次
- 如果想要同样的操作执行多次，需要使用循环结构

### 1.2 Java 中的循环类型

1. **while 循环**
2. **do...while 循环**
3. **for 循环**
4. **增强型 for 循环**（Java 5 引入，主要用于数组）

---

## 二、while 循环

### 2.1 语法格式

```java
while (布尔表达式) {
    // 循环内容
}
```

### 2.2 执行流程

1. 判断布尔表达式
2. 如果为 `true`，执行循环体
3. 重复步骤 1 和 2
4. 如果为 `false`，退出循环

### 2.3 特点

- 先判断条件，再执行循环体
- 如果条件一开始就不满足，循环体一次都不会执行

### 2.4 示例代码

```java
public class Test {
    public static void main(String args[]) {
        int x = 10;
        
        while (x < 20) {
            System.out.print("value of x : " + x);
            x++;
            System.out.print("\n");
        }
    }
}
```

**输出**：

```
value of x : 10
value of x : 11
value of x : 12
value of x : 13
value of x : 14
value of x : 15
value of x : 16
value of x : 17
value of x : 18
value of x : 19
```

---

## 三、do...while 循环

### 3.1 语法格式

```java
do {
    // 代码语句
} while (布尔表达式);
```

**注意**：语句末尾有分号 `;`

### 3.2 执行流程

1. 先执行一次循环体
2. 判断布尔表达式
3. 如果为 `true`，继续执行循环体
4. 如果为 `false`，退出循环

### 3.3 特点

- 先执行循环体，再判断条件
- 至少会执行一次循环体
- 布尔表达式在循环体的后面

### 3.4 与 while 的区别

|特性|while|do...while|
|---|---|---|
|判断时机|先判断后执行|先执行后判断|
|最少执行次数|0 次|1 次|
|适用场景|不确定是否需要执行|至少需要执行一次|

### 3.5 示例代码

```java
public class Test {
    public static void main(String args[]) {
        int x = 10;
        
        do {
            System.out.print("value of x : " + x);
            x++;
            System.out.print("\n");
        } while (x < 20);
    }
}
```

**输出**：

```
value of x : 10
value of x : 11
value of x : 12
value of x : 13
value of x : 14
value of x : 15
value of x : 16
value of x : 17
value of x : 18
value of x : 19
```

---

## 四、for 循环

### 4.1 语法格式

```java
for (初始化; 布尔表达式; 更新) {
    // 代码语句
}
```

### 4.2 执行流程

1. **初始化**：最先执行，只执行一次
2. **判断布尔表达式**：如果为 `true`，执行循环体；如果为 `false`，退出循环
3. **执行循环体**
4. **更新**：更新循环控制变量
5. 重复步骤 2-4

### 4.3 for 循环说明

- **初始化**：可以声明一种类型，初始化一个或多个循环控制变量，也可以是空语句
- **布尔表达式**：控制循环是否继续
- **更新**：通常用于更新循环控制变量
- 执行次数在执行前就确定

### 4.4 示例代码

```java
public class Test {
    public static void main(String args[]) {
        for (int x = 10; x < 20; x = x + 1) {
            System.out.print("value of x : " + x);
            System.out.print("\n");
        }
    }
}
```

**输出**：

```
value of x : 10
value of x : 11
value of x : 12
value of x : 13
value of x : 14
value of x : 15
value of x : 16
value of x : 17
value of x : 18
value of x : 19
```

### 4.5 for 循环的灵活性

```java
// 标准 for 循环
for (int i = 0; i < 10; i++) { }

// 多个初始化变量
for (int i = 0, j = 10; i < j; i++, j--) { }

// 省略初始化
int i = 0;
for (; i < 10; i++) { }

// 省略更新
for (int i = 0; i < 10;) {
    i++;
}

// 无限循环
for (;;) {
    // 需要 break 退出
}
```

---

## 五、增强型 for 循环（for-each）

### 5.1 语法格式

```java
for (声明语句 : 表达式) {
    // 代码语句
}
```

### 5.2 说明

- **声明语句**：声明新的局部变量，类型必须和数组元素类型匹配
- **表达式**：要访问的数组名，或返回值为数组的方法
- **作用域**：变量的作用域限定在循环语句块内
- **值**：变量的值与当前数组元素的值相等

### 5.3 特点

- Java 5 引入
- 主要用于数组和集合
- 代码更简洁
- 不能修改数组元素
- 不能获取索引

### 5.4 示例代码

```java
public class Test {
    public static void main(String args[]) {
        int[] numbers = {10, 20, 30, 40, 50};
        
        // 遍历数组
        for (int x : numbers) {
            System.out.print(x);
            System.out.print(",");
        }
        System.out.print("\n");
        
        String[] names = {"James", "Larry", "Tom", "Lacy"};
        
        // 遍历字符串数组
        for (String name : names) {
            System.out.print(name);
            System.out.print(",");
        }
    }
}
```

**输出**：

```
10,20,30,40,50,
James,Larry,Tom,Lacy,
```

---

## 六、break 关键字

### 6.1 用途

- 主要用在循环语句或 switch 语句中
- 跳出整个语句块
- 跳出最里层的循环，继续执行该循环下面的语句

### 6.2 语法

```java
break;
```

### 6.3 示例代码

```java
public class Test {
    public static void main(String args[]) {
        int[] numbers = {10, 20, 30, 40, 50};
        
        for (int x : numbers) {
            if (x == 30) {
                break;  // 遇到 30 就退出循环
            }
            System.out.print(x);
            System.out.print("\n");
        }
    }
}
```

**输出**：

```
10
20
```

### 6.4 带标签的 break

```java
public class Test {
    public static void main(String args[]) {
        outer:  // 标签
        for (int i = 0; i < 3; i++) {
            for (int j = 0; j < 3; j++) {
                if (i == 1 && j == 1) {
                    break outer;  // 跳出外层循环
                }
                System.out.println("i=" + i + ", j=" + j);
            }
        }
    }
}
```

---

## 七、continue 关键字

### 7.1 用途

- 适用于任何循环控制结构
- 让程序立刻跳转到下一次循环的迭代
- 跳过当前循环的剩余代码

### 7.2 行为

- **for 循环**：跳转到更新语句
- **while/do...while 循环**：跳转到布尔表达式的判断

### 7.3 语法

```java
continue;
```

### 7.4 示例代码

```java
public class Test {
    public static void main(String args[]) {
        int[] numbers = {10, 20, 30, 40, 50};
        
        for (int x : numbers) {
            if (x == 30) {
                continue;  // 跳过 30，继续下一次循环
            }
            System.out.print(x);
            System.out.print("\n");
        }
    }
}
```

**输出**：

```
10
20
40
50
```

### 7.5 带标签的 continue

```java
public class Test {
    public static void main(String args[]) {
        outer:
        for (int i = 0; i < 3; i++) { //外层循环
            for (int j = 0; j < 3; j++) { // 内层循环
                if (j == 1) {
                    continue outer;  // 跳到外层循环的下一次迭代
                }
                System.out.println("i=" + i + ", j=" + j);
            }
        }
    }
}
```

---

## 八、循环对比总结

### 8.1 三种基本循环对比

|特性|while|do...while|for|
|---|---|---|---|
|判断时机|先判断后执行|先执行后判断|先判断后执行|
|最少执行次数|0 次|1 次|0 次|
|适用场景|不确定循环次数|至少执行一次|确定循环次数|
|代码结构|分散|分散|集中|

### 8.2 循环选择建议

```java
// 1. 已知循环次数 → 使用 for
for (int i = 0; i < 10; i++) {
    // 执行 10 次
}

// 2. 不确定循环次数，根据条件判断 → 使用 while
while (scanner.hasNext()) {
    // 处理输入
}

// 3. 至少执行一次 → 使用 do...while
do {
    // 显示菜单
    choice = scanner.nextInt();
} while (choice != 0);

// 4. 遍历数组或集合 → 使用增强型 for
for (String item : list) {
    // 处理每个元素
}
```

---

## 九、break 和 continue 对比

### 9.1 区别总结

|特性|break|continue|
|---|---|---|
|作用|终止循环|跳过本次循环|
|循环是否继续|不继续|继续|
|后续代码|执行循环后的代码|执行下一次循环|

### 9.2 示例对比

```java
// break 示例
for (int i = 0; i < 5; i++) {
    if (i == 3) break;
    System.out.println(i);
}
// 输出：0 1 2

// continue 示例
for (int i = 0; i < 5; i++) {
    if (i == 3) continue;
    System.out.println(i);
}
// 输出：0 1 2 4
```

---

## 十、循环最佳实践

### 10.1 避免无限循环

```java
// ❌ 错误：无限循环
while (true) {
    // 没有 break 语句
}

// ✅ 正确：有退出条件
while (true) {
    if (condition) break;
}
```

### 10.2 循环变量的作用域

```java
// ✅ 推荐：循环变量作用域最小化
for (int i = 0; i < 10; i++) {
    // i 只在循环内可见
}

// ❌ 不推荐：循环变量作用域过大
int i;
for (i = 0; i < 10; i++) {
    // i 在循环外仍可见
}
```

### 10.3 增强型 for 循环的限制

```java
int[] arr = {1, 2, 3, 4, 5};

// ❌ 不能修改数组元素
for (int num : arr) {
    num = num * 2;  // 不会改变数组
}

// ✅ 使用普通 for 循环修改
for (int i = 0; i < arr.length; i++) {
    arr[i] = arr[i] * 2;
}
```

### 10.4 嵌套循环优化

```java
// ✅ 外层循环次数少，内层循环次数多
for (int i = 0; i < 10; i++) {
    for (int j = 0; j < 1000; j++) {
        // 处理
    }
}

// ❌ 避免不必要的嵌套
for (int i = 0; i < n; i++) {
    for (int j = 0; j < n; j++) {
        if (i == j) {
            // 可以在外层循环直接处理
        }
    }
}
```

---

## 十一、常见循环模式

### 11.1 累加求和

```java
int sum = 0;
for (int i = 1; i <= 100; i++) {
    sum += i;
}
System.out.println("1到100的和：" + sum);
```

### 11.2 查找元素

```java
int[] arr = {10, 20, 30, 40, 50};
int target = 30;
boolean found = false;

for (int num : arr) {
    if (num == target) {
        found = true;
        break;
    }
}
System.out.println("找到了：" + found);
```

### 11.3 倒序遍历

```java
for (int i = arr.length - 1; i >= 0; i--) {
    System.out.println(arr[i]);
}
```

### 11.4 步长循环

```java
// 每次增加 2
for (int i = 0; i < 10; i += 2) {
    System.out.println(i);  // 0, 2, 4, 6, 8
}
```

---

## 十二、重要知识点总结

### 12.1 循环选择原则

1. **次数确定** → `for` 循环
2. **次数不确定** → `while` 循环
3. **至少执行一次** → `do...while` 循环
4. **遍历数组/集合** → 增强型 `for` 循环

### 12.2 关键要点

- `while` 和 `for` 可能一次都不执行
- `do...while` 至少执行一次
- `break` 终止循环
- `continue` 跳过本次循环
- 增强型 `for` 循环不能修改元素
- 避免无限循环
- 注意循环变量的作用域

### 12.3 常见错误

```java
// ❌ 死循环
for (int i = 0; i < 10; ) {
    // 忘记更新 i
}

// ❌ 数组越界
for (int i = 0; i <= arr.length; i++) {
    // 应该是 i < arr.length
}

// ❌ 修改增强型 for 循环的变量
for (int num : arr) {
    num = 0;  // 不会改变数组
}
```