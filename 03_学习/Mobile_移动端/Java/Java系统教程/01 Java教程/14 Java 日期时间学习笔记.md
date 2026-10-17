## 一、Date 类基础

### 1.1 构造方法

```java
// 方式1：使用当前日期和时间初始化
Date date1 = new Date();

// 方式2：使用毫秒数初始化（从1970年1月1日起）
Date date2 = new Date(long millisec);
```

### 1.2 常用方法

|方法|说明|返回值|
|---|---|---|
|`boolean after(Date date)`|判断是否在指定日期之后|true/false|
|`boolean before(Date date)`|判断是否在指定日期之前|true/false|
|`int compareTo(Date date)`|比较两个日期|0(相等)/负数(之前)/正数(之后)|
|`boolean equals(Object date)`|判断两个日期是否相等|true/false|
|`long getTime()`|获取毫秒数|毫秒值|
|`void setTime(long time)`|设置时间|void|
|`String toString()`|转换为字符串|日期字符串|

### 1.3 获取当前时间

```java
import java.util.Date;

public class Main {
    public static void main(String args[]) {
        Date date = new Date();
        System.out.println(date.toString());
        // 输出：Mon May 04 09:51:52 CDT 2013
    }
}
```

---

## 二、日期格式化 - SimpleDateFormat

### 2.1 基本使用

```java
import java.util.*;
import java.text.*;

public class Main {
    public static void main(String args[]) {
        Date dNow = new Date();
        SimpleDateFormat ft = new SimpleDateFormat("E yyyy.MM.dd 'at' hh:mm:ss a zzz");
        System.out.println("Current Date: " + ft.format(dNow));
        // 输出：Current Date: Sun 2004.07.18 at 04:14:09 PM PDT
    }
}
```

### 2.2 时间模式字符

|字符|描述|示例|
|---|---|---|
|`G`|纪元标记|AD|
|`y`|四位年份|2001|
|`M`|月份|July 或 07|
|`d`|日期|10|
|`h`|12小时制|12|
|`H`|24小时制|22|
|`m`|分钟|30|
|`s`|秒|55|
|`S`|毫秒|234|
|`E`|星期几|Tuesday|
|`D`|一年中的第几天|360|
|`w`|一年中第几周|40|
|`a`|AM/PM 标记|PM|
|`z`|时区|Eastern Standard Time|

---

## 三、printf 格式化日期

### 3.1 基本格式化

```java
import java.util.Date;

public class Main {
    public static void main(String args[]) {
        Date date = new Date();
        String str = String.format("Current Date/Time : %tc", date);
        System.out.printf(str);
        // 输出：Current Date/Time : Sat Dec 15 16:37:57 MST 2012
    }
}
```

### 3.2 使用索引

```java
// 方式1：使用索引
System.out.printf("%1$s %2$tB %2$td, %2$tY", "Due date:", date);

// 方式2：使用 < 标志（重复使用前一个参数）
System.out.printf("%s %tB %<te, %<tY", "Due date:", date);
// 输出：Due date: February 09, 2004
```

### 3.3 日期时间转换字符

|字符|描述|示例|
|---|---|---|
|`c`|完整日期和时间|Mon May 04 09:51:52 CDT 2009|
|`F`|ISO 8601 格式|2004-02-09|
|`D`|美国格式 (月/日/年)|02/09/2004|
|`T`|24小时时间|18:05:19|
|`r`|12小时时间|06:05:19 pm|
|`Y`|4位年份|2004|
|`y`|2位年份|04|
|`B`|月份全称|February|
|`b`|月份简称|Feb|
|`A`|星期全称|Monday|
|`a`|星期简称|Mon|
|`H`|24小时制小时|18|
|`I`|12小时制小时|06|
|`M`|分钟|05|
|`S`|秒|19|
|`L`|毫秒|047|
|`p`|上下午标志|pm|
|`z`|时区偏移|-0800|

---

## 四、字符串解析为日期

```java
import java.util.*;
import java.text.*;

public class Main {
    public static void main(String args[]) {
        SimpleDateFormat ft = new SimpleDateFormat("yyyy-MM-dd");
        String input = "1818-11-11";
        
        try {
            Date t = ft.parse(input);
            System.out.println(t);
            // 输出：Wed Nov 11 00:00:00 GMT 1818
        } catch (ParseException e) {
            System.out.println("Unparseable using " + ft);
        }
    }
}
```

---

## 五、实用功能

### 5.1 线程休眠

```java
import java.util.*;

public class Main {
    public static void main(String args[]) {
        try {
            System.out.println(new Date());
            Thread.sleep(3000); // 休眠3秒
            System.out.println(new Date());
        } catch (Exception e) {
            System.out.println("Got an exception!");
        }
    }
}
```

### 5.2 测量时间间隔

```java
import java.util.*;

public class Main {
    public static void main(String args[]) {
        try {
            long start = System.currentTimeMillis();
            System.out.println(new Date());
            
            Thread.sleep(3000);
            
            long end = System.currentTimeMillis();
            long diff = end - start;
            System.out.println("Difference is : " + diff);
            // 输出：Difference is : 3050
        } catch (Exception e) {
            System.out.println("Got an exception!");
        }
    }
}
```

---

## 六、Calendar 类

### 6.1 创建 Calendar 对象

```java
// 方式1：当前日期
Calendar c = Calendar.getInstance();

// 方式2：指定日期
Calendar c1 = Calendar.getInstance();
c1.set(2009, 6 - 1, 12); // 注意：月份从0开始
```

### 6.2 字段类型常量

|常量|描述|
|---|---|
|`Calendar.YEAR`|年份|
|`Calendar.MONTH`|月份（0-11）|
|`Calendar.DATE`|日期|
|`Calendar.HOUR`|12小时制|
|`Calendar.HOUR_OF_DAY`|24小时制|
|`Calendar.MINUTE`|分钟|
|`Calendar.SECOND`|秒|
|`Calendar.DAY_OF_WEEK`|星期几（1=周日）|

### 6.3 设置日期信息

```java
Calendar c1 = Calendar.getInstance();

// 方式1：设置完整日期
c1.set(2009, 5, 12); // 2009年6月12日

// 方式2：设置单个字段
c1.set(Calendar.DATE, 10);
c1.set(Calendar.YEAR, 2008);

// 方式3：增加/减少日期
c1.add(Calendar.DATE, 10);  // 加10天
c1.add(Calendar.DATE, -10); // 减10天
```

### 6.4 获取日期信息

```java
Calendar c1 = Calendar.getInstance();

int year = c1.get(Calendar.YEAR);           // 年份
int month = c1.get(Calendar.MONTH) + 1;     // 月份（需要+1）
int date = c1.get(Calendar.DATE);           // 日期
int hour = c1.get(Calendar.HOUR_OF_DAY);    // 小时
int minute = c1.get(Calendar.MINUTE);       // 分钟
int second = c1.get(Calendar.SECOND);       // 秒
int day = c1.get(Calendar.DAY_OF_WEEK);     // 星期几（1=周日）
```

---

## 七、GregorianCalendar 类

### 7.1 构造方法

```java
// 1. 默认构造
GregorianCalendar gc1 = new GregorianCalendar();

// 2. 指定日期
GregorianCalendar gc2 = new GregorianCalendar(2009, 5, 12);

// 3. 指定日期和时间
GregorianCalendar gc3 = new GregorianCalendar(2009, 5, 12, 18, 30);

// 4. 完整时间
GregorianCalendar gc4 = new GregorianCalendar(2009, 5, 12, 18, 30, 45);
```

### 7.2 常用方法

```java
GregorianCalendar gcalendar = new GregorianCalendar();

// 判断是否为闰年
boolean isLeap = gcalendar.isLeapYear(2024);

// 获取时间
Date date = gcalendar.getTime();

// 设置时间
gcalendar.setTime(new Date());

// 添加时间
gcalendar.add(Calendar.DATE, 10);

// 滚动时间（不影响更大的字段）
gcalendar.roll(Calendar.DATE, true);
```

### 7.3 完整示例

```java
import java.util.*;

public class GregorianCalendarDemo {
    public static void main(String args[]) {
        String months[] = {
            "Jan", "Feb", "Mar", "Apr", "May", "Jun",
            "Jul", "Aug", "Sep", "Oct", "Nov", "Dec"
        };
        
        GregorianCalendar gcalendar = new GregorianCalendar();
        
        // 显示日期
        System.out.print("Date: ");
        System.out.print(months[gcalendar.get(Calendar.MONTH)]);
        System.out.print(" " + gcalendar.get(Calendar.DATE) + " ");
        int year = gcalendar.get(Calendar.YEAR);
        System.out.println(year);
        
        // 显示时间
        System.out.print("Time: ");
        System.out.print(gcalendar.get(Calendar.HOUR) + ":");
        System.out.print(gcalendar.get(Calendar.MINUTE) + ":");
        System.out.println(gcalendar.get(Calendar.SECOND));
        
        // 判断闰年
        if(gcalendar.isLeapYear(year)) {
            System.out.println("当前年份是闰年");
        } else {
            System.out.println("当前年份不是闰年");
        }
    }
}
```

---

## 八、重点总结

### 8.1 类的选择

- **Date**：简单的日期时间表示
- **SimpleDateFormat**：日期格式化和解析
- **Calendar**：日期计算和操作（抽象类）
- **GregorianCalendar**：Calendar 的具体实现（公历）

### 8.2 注意事项

1. **月份从0开始**：Calendar 中 0 表示1月，11 表示12月
2. **星期从1开始**：Calendar 中 1 表示周日，7 表示周六
3. **线程安全**：SimpleDateFormat 不是线程安全的
4. **时区问题**：注意处理不同时区的日期时间

### 8.3 最佳实践

```java
// 获取当前时间
Date now = new Date();

// 格式化日期
SimpleDateFormat sdf = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss");
String dateStr = sdf.format(now);

// 日期计算
Calendar cal = Calendar.getInstance();
cal.add(Calendar.DATE, 7); // 7天后
Date futureDate = cal.getTime();

// 判断闰年
GregorianCalendar gc = new GregorianCalendar();
boolean isLeap = gc.isLeapYear(2024);
```