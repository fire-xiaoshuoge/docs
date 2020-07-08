

## java

Java是一门[面向对象](https://baike.baidu.com/item/面向对象)编程语言，不仅吸收了[C++](https://baike.baidu.com/item/C%2B%2B)语言的各种优点，还摒弃了C++里难以理解的[多继承](https://baike.baidu.com/item/多继承)、[指针](https://baike.baidu.com/item/指针/2878304)等概念，因此Java语言具有功能强大和简单易用两个特征。Java语言作为静态面向对象编程语言的代表，极好地实现了面向对象理论，允许程序员以优雅的思维方式进行复杂的编程  。

Java具有简单性、面向对象、[分布式](https://baike.baidu.com/item/分布式/19276232)、[健壮性](https://baike.baidu.com/item/健壮性/4430133)、[安全性](https://baike.baidu.com/item/安全性/7664678)、平台独立与可移植性、[多线程](https://baike.baidu.com/item/多线程/1190404)、动态性等特点  。Java可以编写[桌面应用程序](https://baike.baidu.com/item/桌面应用程序/2331979)、[Web应用程序](https://baike.baidu.com/item/Web应用程序)、[分布式系统](https://baike.baidu.com/item/分布式系统/4905336)和[嵌入式系统](https://baike.baidu.com/item/嵌入式系统/186978)应用程序等 。

## java语言基础

### 基本数据类型

![image-20200229195240912](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200229195240912.png)

```java
public class PrimitiveTypeTest {  
    public static void main(String[] args) {  
        // byte  
        System.out.println("基本类型：byte 二进制位数：" + Byte.SIZE);  
        System.out.println("包装类：java.lang.Byte");  
        System.out.println("最小值：Byte.MIN_VALUE=" + Byte.MIN_VALUE);  
        System.out.println("最大值：Byte.MAX_VALUE=" + Byte.MAX_VALUE);  
        System.out.println();  
  
        // short  
        System.out.println("基本类型：short 二进制位数：" + Short.SIZE);  
        System.out.println("包装类：java.lang.Short");  
        System.out.println("最小值：Short.MIN_VALUE=" + Short.MIN_VALUE);  
        System.out.println("最大值：Short.MAX_VALUE=" + Short.MAX_VALUE);  
        System.out.println();  
  
        // int  
        System.out.println("基本类型：int 二进制位数：" + Integer.SIZE);  
        System.out.println("包装类：java.lang.Integer");  
        System.out.println("最小值：Integer.MIN_VALUE=" + Integer.MIN_VALUE);  
        System.out.println("最大值：Integer.MAX_VALUE=" + Integer.MAX_VALUE);  
        System.out.println();  
  
        // long  
        System.out.println("基本类型：long 二进制位数：" + Long.SIZE);  
        System.out.println("包装类：java.lang.Long");  
        System.out.println("最小值：Long.MIN_VALUE=" + Long.MIN_VALUE);  
        System.out.println("最大值：Long.MAX_VALUE=" + Long.MAX_VALUE);  
        System.out.println();  
  
        // float  
        System.out.println("基本类型：float 二进制位数：" + Float.SIZE);  
        System.out.println("包装类：java.lang.Float");  
        System.out.println("最小值：Float.MIN_VALUE=" + Float.MIN_VALUE);  
        System.out.println("最大值：Float.MAX_VALUE=" + Float.MAX_VALUE);  
        System.out.println();  
  
        // double  
        System.out.println("基本类型：double 二进制位数：" + Double.SIZE);  
        System.out.println("包装类：java.lang.Double");  
        System.out.println("最小值：Double.MIN_VALUE=" + Double.MIN_VALUE);  
        System.out.println("最大值：Double.MAX_VALUE=" + Double.MAX_VALUE);  
        System.out.println();  
  
        // char  
        System.out.println("基本类型：char 二进制位数：" + Character.SIZE);  
        System.out.println("包装类：java.lang.Character");  
        // 以数值形式而不是字符形式将Character.MIN_VALUE输出到控制台  
        System.out.println("最小值：Character.MIN_VALUE="  
                + (int) Character.MIN_VALUE);  
        // 以数值形式而不是字符形式将Character.MAX_VALUE输出到控制台  
        System.out.println("最大值：Character.MAX_VALUE="  
                + (int) Character.MAX_VALUE);  
    }  
}
```

```java
基本类型：short 二进制位数：16
包装类：java.lang.Short
最小值：Short.MIN_VALUE=-32768
最大值：Short.MAX_VALUE=32767

基本类型：int 二进制位数：32
包装类：java.lang.Integer
最小值：Integer.MIN_VALUE=-2147483648
最大值：Integer.MAX_VALUE=2147483647

基本类型：long 二进制位数：64
包装类：java.lang.Long
最小值：Long.MIN_VALUE=-9223372036854775808
最大值：Long.MAX_VALUE=9223372036854775807

基本类型：float 二进制位数：32
包装类：java.lang.Float
最小值：Float.MIN_VALUE=1.4E-45
最大值：Float.MAX_VALUE=3.4028235E38

基本类型：double 二进制位数：64
包装类：java.lang.Double
最小值：Double.MIN_VALUE=4.9E-324
最大值：Double.MAX_VALUE=1.7976931348623157E308

基本类型：char 二进制位数：16
包装类：java.lang.Character
最小值：Character.MIN_VALUE=0
最大值：Character.MAX_VALUE=65535
```

### 转义字符

| 符号   | 字符含义                 |
| :----- | :----------------------- |
| \n     | 换行 (0x0a)              |
| \r     | 回车 (0x0d)              |
| \f     | 换页符(0x0c)             |
| \b     | 退格 (0x08)              |
| \0     | 空字符 (0x20)            |
| \s     | 字符串                   |
| \t     | 制表符                   |
| \"     | 双引号                   |
| \'     | 单引号                   |
| \\     | 反斜杠                   |
| \ddd   | 八进制字符 (ddd)         |
| \uxxxx | 16进制Unicode字符 (xxxx) |

### 自动类型转换

自动类型转换必须满足转换前的数据类型的位数要低于转换后的数据类型，例如: short数据类型的位数为16位，就可以自动转换位数为32的int类型，同样float数据类型的位数为32，可以自动转换为64位的double类型。

![img](https://images2018.cnblogs.com/blog/1295451/201808/1295451-20180802104136382-348221547.png)

据类型转换必须满足如下规则：

- \1. 不能对boolean类型进行类型转换。
- \2. 不能把对象类型转换成不相关类的对象。
- \3. 在把容量大的类型转换为容量小的类型时必须使用强制类型转换。
- \4. 转换过程中可能导致溢出或损失精度。

### 强制类型转换

- \1. 条件是转换的数据类型必须是兼容的。
- \2. 格式：(type)value type是要强制类型转换后的数据类型 。

### 关键字

| 关键字       | 含义                                                         |
| ------------ | ------------------------------------------------------------ |
| abstract     | 表明类或者成员方法具有抽象属性                               |
| assert       | 用来进行程序调试                                             |
| boolean      | 基本数据类型之一，布尔类型                                   |
| break        | 提前跳出一个块                                               |
| byte         | 基本数据类型之一，字节类型                                   |
| case         | 用在switch语句之中，表示其中的一个分支                       |
| catch        | 用在异常处理中，用来捕捉异常                                 |
| char         | 基本数据类型之一，字符类型                                   |
| class        | 类                                                           |
| const        | 保留关键字，没有具体含义                                     |
| continue     | 回到一个块的开始处                                           |
| default      | 默认，例如，用在switch语句中，表明一个默认的分支             |
| do           | 用在do-while循环结构中                                       |
| double       | 基本数据类型之一，双精度浮点数类型                           |
| else         | 用在条件语句中，表明当条件不成立时的分支                     |
| enum         | 枚举                                                         |
| extends      | 表明一个类型是另一个类型的子类型，这里常见的类型有类和接口   |
| final        | 用来说明最终属性，表明一个类不能派生出子类，或者成员方法不能被覆盖，或者成员域的值不能被改变 |
| finally      | 用于处理异常情况，用来声明一个基本肯定会被执行到的语句块     |
| float        | 基本数据类型之一，单精度浮点数类型                           |
| for          | 一种循环结构的引导词                                         |
| goto         | 保留关键字，没有具体含义                                     |
| if           | 条件语句的引导词                                             |
| implements   | 表明一个类实现了给定的接口                                   |
| import       | 表明要访问指定的类或包                                       |
| instanceof   | 用来测试一个对象是否是指定类型的实例对象                     |
| int          | 基本数据类型之一，整数类型                                   |
| interface    | 接口                                                         |
| long         | 基本数据类型之一，长整数类型                                 |
| native       | 用来声明一个方法是由与计算机相关的语言（如C/C++/FORTRAN语言）实现的 |
| new          | 用来创建新实例对象                                           |
| package      | 包                                                           |
| private      | 一种访问控制方式：私用模式                                   |
| protected    | 一种访问控制方式：保护模式                                   |
| public       | 一种访问控制方式：共用模式                                   |
| return       | 从成员方法中返回数据                                         |
| short        | 基本数据类型之一,短整数类型                                  |
| static       | 表明具有静态属性                                             |
| strictfp     | 用来声明FP_strict（单精度或双精度浮点数）表达式遵循IEEE 754算术规范 |
| super        | 表明当前对象的父类型的引用或者父类型的构造方法               |
| switch       | 分支语句结构的引导词                                         |
| synchronized | 表明一段代码需要同步执行                                     |
| this         | 指向当前实例对象的引用                                       |
| throw        | 抛出一个异常                                                 |
| throws       | 声明在当前定义的成员方法中所有需要抛出的异常                 |
| transient    | 声明不用序列化的成员域                                       |
| try          | 尝试一个可能抛出异常的程序块                                 |
| void         | 声明当前成员方法没有返回值                                   |
| volatile     | 表明两个或者多个变量必须同步地发生变化                       |
| while        | 用在循环结构中                                               |

### 运算符

| 运 算 符 | 名 称    | 说 明                        | 例 子      |
| -------- | -------- | ---------------------------- | ---------- |
| -        | 取反符号 | 取反运算                     | b=-a       |
| ++       | 自加一   | 先取值再加一，或先加一再取值 | a++ 或 ++a |
| --       | 自减一   | 先取值再减一，或先减一再取值 | a-- 或 --a |

| 运 算 符 | 名 称 | 说 明                                                    | 例 子 |
| -------- | ----- | -------------------------------------------------------- | ----- |
| +        | 加    | 求 a 加 b 的和，还可用于 String 类型，进行字符串连接操作 | a + b |
| -        | 减    | 求 a 减 b 的差                                           | a - b |
| *        | 乘    | 求 a 乘以 b 的积                                         | a * b |
| /        | 除    | 求 a 除以 b 的商                                         | a / b |
| %        | 取余  | 求 a 除以 b 的余数                                       | a % b |

| 运 算 符 | 名 称    | 例 子            |
| -------- | -------- | ---------------- |
| +=       | 加赋值   | a += b、a += b+3 |
| -=       | 减赋值   | a -= b           |
| *=       | 乘赋值   | a *= b           |
| /=       | 除赋值   | a /= b           |
| %=       | 取余赋值 | a %= b           |

| 运算符 | 含义                                                         | 实例           | 结果 |
| ------ | ------------------------------------------------------------ | -------------- | ---- |
| +=     | 将该运算符左边的数值加上右边的数值， 其结果赋值给左边变量本身 | int a=5; a+=2; | a=7  |
| -=     | 将该运算符左边的数值减去右边的数值， 其结果赋值给左边变量本身 | int a=5; a-=2; | a=3  |
| *=     | 将该运算符左边的数值乘以右边的数值， 其结果赋值给左边变量本身 | int a=5; a*=2; | a=10 |
| /=     | 将该运算符左边的数值整除右边的数值， 其结果赋值给左边变量本身 | int a=5; a/=2; | a=2  |
| %=     | 将该运算符左边的数值除以右边的数值后取余，其结果赋值给左边变量本身 | int a=5; a%=2; | a=1  |

| 运算符 | 用法   | 含义   | 说明                                               | 实例       | 结果  |
| :----- | :----- | :----- | :------------------------------------------------- | :--------- | :---- |
| &&     | a&&b   | 短路与 | ab 全为 true 时，计算结果为 true，否则为 false。   | 2>1&&3<4   | true  |
| \|\|   | a\|\|b | 短路或 | ab 全为 false 时，计算结果为 false，否则为 true。  | 2<1\|\|3>4 | false |
| !      | !a     | 逻辑非 | a 为 true 时，值为 false，a 为 false 时，值为 true | !(2>4)     | true  |
| \|     | a\|b   | 逻辑或 | ab 全为 false 时，计算结果为 false，否则为 true    | 1>2\|3>5   | false |
| &      | a&b    | 逻辑与 | ab 全为 false 时，计算结果为 false，否则为 true    | 1<2&3<5    | true  |

| 算符 | 含义             | 说明                                                         | 实例                            | 结果                 |
| ---- | ---------------- | ------------------------------------------------------------ | ------------------------------- | -------------------- |
| >    | 大于运算符       | 只支持左右两边操作数是数值类型。如果前面变量的值大于后面变量的值， 则返回 true。 | 2>3                             | false                |
| >=   | 大于或等于运算符 | 只支持左右两边操作数是数值类型。如果前面变量的值大于等于后面变量的值， 则返回 true。 | 4>=2                            | true                 |
| <    | 小于运算符       | 只支持左右两边操作数是数值类型。如果前面变量的值小于后面变量的值，则返回 true。 | 2<3                             | true                 |
| <=   | 小于或等于运算符 | 只支持左右两边操作数是数值类型。如果前面变量的值小于等于后面变量的值， 则返回 true。 | 4<=2                            | false                |
| ==   | 相等运算符       | 如果进行比较的两个操作数都是数值类型，无论它们的数据类型是否相同，只要它们的值不相等，也都将返回 true。 如果两个操作数都是引用类型，只有当两个引用变量的类型具有父子关系时才可以比较，只要两个引用指向的不是同一个对象就会返回 true。 [Java](http://c.biancheng.net/java/) 也支持两个 boolean 类型的值进行比较。 | 4==4 97=='a' 5.0==5 true==false | true true true false |
| !=   | 不相等运算符     | 如果进行比较的两个操作数都是数值类型，无论它们的数据类型是否相同，只要它们的值不相等，也都将返回 true。 如果两个操作数都是引用类型，只有当两个引用变量的类型具有父子关系时才可以比较，只要两个引用指向的不是同一个对象就会返回 true。 | 4!=2                            | true                 |

| 运算符 | 含义                                      | 实例                | 结果    |
| ------ | ----------------------------------------- | ------------------- | ------- |
| i++    | 将 i 的值先使用再加 1 赋值给 i 变量本身   | int i=1; int j=i++; | i=2 j=1 |
| ++i    | 将 i 的值先加 1 赋值给变量 i 本身后再使用 | int i=1; int j=++i; | i=2 j=2 |
| i--    | 将 i 的值先使用再减 1 赋值给变量 i 本身   | int i=1; int j=i--; | i=0 j=1 |
| --i    | 将 i 的值先减 1 后赋值给变量 i 本身再使用 | int i=1; int j=--i; | i=0 j=0 |

| 运算符 | 含义                    | 实例   | 结果 |
| ------ | ----------------------- | ------ | ---- |
| &      | 按位进行与运算（AND）   | 4 & 5  | 4    |
| \|     | 按位进行或运算（OR）    | 4 \| 5 | 5    |
| ^      | 按位进行异或运算（XOR） | 4 ^ 5  | 1    |
| ~      | 按位进行取反运算（NOT） | ~ 4    | -5   |

| 运算符 | 含义         | 实例 | 结果 |
| ------ | ------------ | ---- | ---- |
| »      | 右移位运算符 | 8»1  | 4    |
| «      | 左移位运算符 | 9«2  | 36   |

| 运算符 | 含义         | 实例          | 结果                       |
| ------ | ------------ | ------------- | -------------------------- |
| &=     | 按位与赋值   | num1 &= num2  | 等价于 num 1=num 1 & num2  |
| \|=    | 按位或赋值   | num1 \|= num2 | 等价于 num 1=num 1 \| num2 |
| ^=     | 按位异或赋值 | num1 ^= num2  | 等价于 num 1=num 1 ^ num2  |
| -=     | 按位取反赋值 | num1 ~= num2  | 等价于 num 1=num 1 ~ num2  |
| «=     | 按位左移赋值 | num1 «= num2  | 等价于 num 1=num 1 « num2  |
| »=     | 按位右移赋值 | num1 »= num2  | 等价于 num 1=num 1 » num2  |

| 优先级 | 运算符                                           | 结合性   |
| ------ | ------------------------------------------------ | -------- |
| 1      | ()、[]、{}                                       | 从左向右 |
| 2      | !、+、-、~、++、--                               | 从右向左 |
| 3      | *、/、%                                          | 从左向右 |
| 4      | +、-                                             | 从左向右 |
| 5      | «、»、>>>                                        | 从左向右 |
| 6      | <、<=、>、>=、instanceof                         | 从左向右 |
| 7      | ==、!=                                           | 从左向右 |
| 8      | &                                                | 从左向右 |
| 9      | ^                                                | 从左向右 |
| 10     | \|                                               | 从左向右 |
| 11     | &&                                               | 从左向右 |
| 12     | \|\|                                             | 从左向右 |
| 13     | ?:                                               | 从右向左 |
| 14     | =、+=、-=、*=、/=、&=、\|=、^=、~=、«=、»=、>>>= | 从右向左 |

### 代码注释

单行注释//，多行注释 /* */，文档注释/** */。

### &和&&

虽然二者都要求运算符左右两端的布尔值都是true整个表达式的值才是true。&&之所以称为短路运算是因为，如果&&左边的表达式的值是false，右边的表达式会被直接短路掉，不会进行运算。

### ++i与i++

在编程时，经常会用到变量的自增或自减操作，尤其在循环中用得最多。以自增为例，有两种自增方式：前置与后置，即++i和i++，它们的不同点在于i++是在程序执行完毕后进行自增，而++i是在程序开始执行前进行自增

际上，不管是前置 ++，还是后置 ++，都是先将变量的值加1，然后才继续计算的。二者之间真正的区别是：前置 ++ 是将变量的值加1后，使用增值后的变量进行运算的，而后置++ 是首先将变量赋值给一个临时变量，接下来对变量的值加1，然后使用那个临时变量进行运算。

## 流程控制

### if 结构

![img](http://c.biancheng.net/uploads/allimg/180928/3-1P92Q50459617.jpg)

### if-else 结构

![img](http://c.biancheng.net/uploads/allimg/180928/3-1P92Q509533Y.jpg)

###  if-else-if 语句

![img](http://c.biancheng.net/uploads/allimg/180928/3-1P92Q51552222.jpg)

### 嵌套 if

![img](http://c.biancheng.net/uploads/allimg/180928/3-1P92Q51945a7.jpg)

### switch 语句

![switch语句执行流程图](http://c.biancheng.net/uploads/allimg/180928/3-1P92Q6201MD.jpg)

### while 语句

![img](http://c.biancheng.net/uploads/allimg/180928/3-1P92QHR5F9.jpg)

### do-while 语句

![img](http://c.biancheng.net/uploads/allimg/180928/3-1P92QH615B1.jpg)



while 循环和 do-while 循环的相同处是：都是循环结构，使用 while(循环条件) 表示循环条件，使用大括号将循环操作括起来。

while 循环和 do-while 循环的不同处如下：

- 语法不同：与 while 循环相比，do-while 循环将 while 关键字和循环条件放在后面，而且前面多了 do 关键字，后面多了一个分号。
- 执行次序不同：while 循环先判断，再执行。do-while 循环先执行，再判断。
- 一开始循环条件就不满足的情况下，while 循环一次都不会执行，do-while 循环则不管什么情况下都至少执行一次。

### for循环

```java
for(条件表达式1;条件表达式2;条件表达式3) {
    语句块;
}
```

| 表达式       | 形式                               | 功能                                          | 举例    |
| ------------ | ---------------------------------- | --------------------------------------------- | ------- |
| 条件表达式 1 | 赋值语句                           | 循环结构的初始部分，为循环变量赋初值          | int i=1 |
| 条件表达式 2 | 条件语句                           | 循环结构的循环条件                            | i>40    |
| 条件表达式 3 | 迭代语句，通常使用 ++ 或 -- 运算符 | 循环结构的迭代部分，通常用来修改循环 变量的值 | i++     |

![img](http://c.biancheng.net/uploads/allimg/180929/3-1P92Z93211K2.jpg)

| 名称     | 概念                               | 适用场景                   | 特点                                                         |
| -------- | ---------------------------------- | -------------------------- | ------------------------------------------------------------ |
| for      | 根据循环次数限制做多少次重复操作   | 适合循环次数是已知的操作   | 初始化的条件可以使用局部变量和外部变量使用局部变量时，控制执行在 for 结束后会自动释放，提高内存使用效率。且变量在 for 循环结束后，不能被访问。先判断，再执行 |
| while    | 当满足什么条件的时候，才做某种操作 | 适合循环次数是未知的操作   | 初始化的条件只能使用外部变量，且变量在 while 循环结束后可以访问先判断，再执行 |
| do-while | 先执行一次，在判断是否满足条件     | 适合至少执行一次的循环操作 | 在先需要执行一次的情况下，代码更加简洁。先执行一次，再判断   |

### for循环嵌套

![嵌套循环的执行流程](http://c.biancheng.net/uploads/allimg/190918/5-1Z91Q456014E.jpg)

### foreach语句的用法

```java
for(类型 变量名:集合) {
    语句块;
}
```

![img](http://c.biancheng.net/uploads/allimg/180929/3-1P929101250L5.jpg)

###  return语句

return 关键字并不是专门用于结束循环的，return 语句用于终止函数的执行或退出类的方法，并把控制权返回该方法的调用者。如果这个方法带有返回类型，return 语句就必须返回这个类型的值；如果这个方法没有返回值，可以使用没有表达式的 return 语句。

### break语句

某些时候需要在某种条件出现时强行终止循环，而不是等到循环条件为 false 时才退出循环。此时，可以使用 break 来完成这个功能。

break 用于完全结束一个循环，跳出循环体。不管是哪种循环，一旦在循环体中遇到 break，系统将完全结束该循环，开始执行循环之后的代码。

###  continue语句

continue 语句是跳过循环体中剩余的语句而强制执行下一次循环，其作用为结束本次循环，即跳过循环体中下面尚未执行的语句，接着进行下一次是否执行循环的判定。

continue 语句类似于 break 语句，但它只能出现在循环体中。它与 break 语句的区别在于：continue 并不是中断循环语句，而是中止当前迭代的循环，进入下一次的迭代。简单来讲，continue 是忽略循环语句的当次循环。

## 字符串

### String转换为int

String 字符串转整型 int 有以下两种方式：

- Integer.parseInt(str)
- Integer.valueOf(str).intValue()

### int转换为String

整型 int 转 String 字符串类型有以下 3 种方法：

- String s = String.valueOf(i);
- String s = Integer.toString(i);
- String s = "" + i;

### valueOf() 、parse()和toString()

1）valueOf()

valueOf() 方法将数据的内部格式转换为可读的形式。它是一种静态方法，对于所有 [Java](http://c.biancheng.net/java/) 内置的类型，在字符串内被重载，以便每一种类型都能被转换成字符串。valueOf() 方法还被类型 Object 重载，所以创建的任何形式类的对象也可被用作一个参数。这里是它的几种形式：

```java
static String valueOf(double num)
static String valueOf(long num)
static String valueOf(Object ob)
static String valueOf(char chars[])
```

2）parse()

parseXxx(String) 这种形式，是指把字符串转换为数值型，其中 Xxx 对应不同的数据类型，然后转换为 Xxx 指定的类型，如 int 型和 float 型。

3）toString()

toString() 可以把一个引用类型转换为 String 字符串类型，是 sun 公司开发 Java 的时候为了方便所有类的字符串操作而特意加入的一个方法。

### **简单的字符串拼接**

```java
String str2="content2";
String str3=str1+"----"+str2;
System.out.println(str3);//content1----content2
System.out.println(str1.concat(str2).concat(str3));//content1content2content1----content2
```

### **字符串长度**

```java
System.out.print(str1.length());//8
System.out.print("东小东".length());//3
```

### **将字符串分割为字符数组**

```java
System.out.print(str1.toCharArray()[0]);//c
System.out.print("东小东".toCharArray()[0]);//东
```

### **获取指定位置的字符**

```java
System.out.print(str1.charAt(0));//c
System.out.print("东小东".charAt(0));//东
```

### **去掉字符串的左右空格**

```java
System.out.print("   东小东    ".trim());//东小东
```

### **全部转换为大小写**

```java
System.out.print("AbCd".toUpperCase());//ABCD
System.out.print("AbCd".toLowerCase());//abcd
```

### **判断字符串是否以某个字符开头或者结尾**

```java
System.out.println("AbCd".startsWith("A"));//true
System.out.println("AbCd".endsWith("A"));//false
System.out.println("AbCd".startsWith("C",2));//true//指定开始位置
```

### **字符串替换**

```java
System.out.println("AbCdA".replace('A','8'));//8bCd8
System.out.println("AbCd".replace("A","123456"));//123456bCd
System.out.println("AbCA".replaceFirst("A","123456"));//123456bCA
System.out.println("AbCA".replaceAll("A","123456"));//123456bC123456
```

### **获取字符串的索引**

```java
System.out.println("A123A456A789".indexOf('A'));//0
System.out.println("A123A456A789".indexOf('A',1));//4
System.out.println("A123A456A789".indexOf("A"));//0
System.out.println("A123A456A789".indexOf("A",1));//4

System.out.println("A123A456A789".lastIndexOf('A'));//8
System.out.println("A123A456A789".lastIndexOf('A',8));//8 //从指定索引反向搜索
System.out.println("A123A456A789".lastIndexOf("A"));//8
System.out.println("A123A456A789".lastIndexOf("A",8));//8

System.out.println("A123A456A789".lastIndexOf("100"));//-1  //查找失败
```

### **字符串提取**

```java
System.out.println("AbCA".substring(1));//bCA
System.out.println("AbCA".substring(1,3));//bC
```

### StringBuffer

StringBuffer 类提供了 3 个构造方法来创建一个字符串，如下所示：

- StringBuffer() 构造一个空的字符串缓冲区，并且初始化为 16 个字符的容量。
- StringBuffer(int length) 创建一个空的字符串缓冲区，并且初始化为指定长度 length 的容量。
- StringBuffer(String str) 创建一个字符串缓冲区，并将其内容初始化为指定的字符串内容 str，字符串缓冲区的初始容量为 16 加上字符串 str 的长度。

### String、StringBuffer、StringBuilder

Java语言有4个类可以对字符或字符串进行操作，它们分别是Character、String、String-Buffer和StringTokenizer，其中Character用于单个字符操作，String用于字符串操作，属于不可变类，而StringBuffer也是用于字符串操作，不同之处是StringBuffer属于可变类。

StringBuilder也是可以被修改的字符串，它与StringBuffer类似，都是字符串缓冲区，但StringBuilder不是线程安全的，如果只是在单线程中使用字符串缓冲区，那么StringBuilder的效率会更高些。因此在只有单线程访问时可以使用StringBuilder，当有多个线程访问时，最好使用线程安全的StringBuffer。因为StringBuffer必要时可以对这些方法进行同步，所以任意特定实例上的所有操作就好像是以串行顺序发生的，该顺序与所涉及的每个线程进行的方法调用顺序一致。

## Java数字处理和日期类

### Java Math

| 方法                                 | 说明                   |
| ------------------------------------ | ---------------------- |
| static int abs(int a)                | 返回 a 的绝对值        |
| static long abs(long a)              | 返回 a 的绝对值        |
| static float abs(float a)            | 返回 a 的绝对值        |
| static double abs(double a)          | 返回 a 的绝对值        |
| static int max(int x,int y)          | 返回 x 和 y 中的最大值 |
| static double max(double x,double y) | 返回 x 和 y 中的最大值 |
| static long max(long x,long y)       | 返回 x 和 y 中的最大值 |
| static float max(float x,float y)    | 返回 x 和 y 中的最大值 |
| static int min(int x,int y)          | 返回 x 和 y 中的最小值 |
| static long min(long x,long y)       | 返回 x 和 y 中的最小值 |
| static double min(double x,double y) | 返回 x 和 y 中的最小值 |
| static float min(float x,float y)    | 返回 x 和 y 中的最小值 |

| 方法                          | 说明                                                         |
| ----------------------------- | ------------------------------------------------------------ |
| static double ceil(double a)  | 返回大于或等于 a 的最小整数                                  |
| static double floor(double a) | 返回小于或等于 a 的最大整数                                  |
| static double rint(double a)  | 返回最接近 a 的整数值，如果有两个同样接近的整数，则结果取偶数 |
| static int round(float a)     | 将参数加上 1/2 后返回与参数最近的整数                        |
| static long round(double a)   | 将参数加上 1/2 后返回与参数最近的整数，然后强制转换为长整型  |

| 方法                                   | 说明                                                       |
| -------------------------------------- | ---------------------------------------------------------- |
| static double sin(double a)            | 返回角的三角正弦值，参数以孤度为单位                       |
| static double cos(double a)            | 返回角的三角余弦值，参数以孤度为单位                       |
| static double asin(double a)           | 返回一个值的反正弦值，参数域在 [-1,1]，值域在 [-PI/2,PI/2] |
| static double acos(double a)           | 返回一个值的反余弦值，参数域在 [-1,1]，值域在 [0.0,PI]     |
| static double tan(double a)            | 返回角的三角正切值，参数以弧度为单位                       |
| static double atan(double a)           | 返回一个值的反正切值，值域在 [-PI/2,PI/2]                  |
| static double toDegrees(double angrad) | 将用孤度表示的角转换为近似相等的用角度表示的角             |
| staticdouble toRadians(double angdeg)  | 将用角度表示的角转换为近似相等的用弧度表示的角             |

| 方法                                 | 说明                               |
| ------------------------------------ | ---------------------------------- |
| static double exp(double a)          | 返回 e 的 a 次幂                   |
| static double pow(double a,double b) | 返回以 a 为底数，以 b 为指数的幂值 |
| static double sqrt(double a)         | 返回 a 的平方根                    |
| static double cbrt(double a)         | 返回 a 的立方根                    |
| static double log(double a)          | 返回 a 的自然对数，即 lna 的值     |
| static double log10(double a)        | 返回以 10 为底 a 的对数            |

### 随机数

Random 类提供了丰富的随机数生成方法，可以产生 boolean、int、long、float、byte 数组以及 double 类型的随机数，这是它与 random() 方法最大的不同之处。random() 方法只能产生 double 类型的 0~1 的随机数。

Random 类位于 java.util 包中，该类常用的有如下两个构造方法。

- Random()：该构造方法使用一个和当前系统时间对应的数字作为种子数，然后使用这个种子数构造 Random 对象。
- Random(long seed)：使用单个 long 类型的参数创建一个新的随机数生成器。


Random 类提供的所有方法生成的随机数字都是均匀分布的，也就是说区间内部的数字生成的概率是均等的，在表 1 中列出了 Random 类中常用的方法。

| 方法                    | 说明                                                         |
| ----------------------- | ------------------------------------------------------------ |
| boolean nextBoolean()   | 生成一个随机的 boolean 值，生成 true 和 false 的值概率相等   |
| double nextDouble()     | 生成一个随机的 double 值，数值介于 [0,1.0)，含 0 而不包含 1.0 |
| int nextlnt()           | 生成一个随机的 int 值，该值介于 int 的区间，也就是 -231~231-1。如果 需要生成指定区间的 int 值，则需要进行一定的数学变换 |
| int nextlnt(int n)      | 生成一个随机的 int 值，该值介于 [0,n)，包含 0 而不包含 n。如果想生成 指定区间的 int 值，也需要进行一定的数学变换 |
| void setSeed(long seed) | 重新设置 Random 对象中的种子数。设置完种子数以后的 Random 对象 和相同种子数使用 new 关键字创建出的 Random 对象相同 |
| long nextLong()         | 返回一个随机长整型数字                                       |
| boolean nextBoolean()   | 返回一个随机布尔型值                                         |
| float nextFloat()       | 返回一个随机浮点型数字                                       |
| double nextDouble()     | 返回一个随机双精度值                                         |

### 数字格式化

DecimalFormat 是 NumberFormat 的一个子类，用于格式化十进制数字。DecimalFormat 类包含一个模式和一组符号，常用符号的说明如表 1 所示。

| 符号 | 说明                                                         |
| ---- | ------------------------------------------------------------ |
| 0    | 显示数字，如果位数不够则补 0                                 |
| #    | 显示数字，如果位数不够不发生变化                             |
| .    | 小数分隔符                                                   |
| -    | 减号                                                         |
| ,    | 组分隔符                                                     |
| E    | 分隔科学记数法中的尾数和小数                                 |
| %    | 前缀或后缀，乘以 100 后作为百分比显示                        |
| ?    | 乘以 1000 后作为千进制货币符显示。用货币符号代替。如果双写，用国际货币符号代替； 如果出现在一个模式中，用货币十进制分隔符代替十进制分隔符 |

### 大数字

在 [Java](http://c.biancheng.net/java/) 中提供了用于大数字运算的类，即 java.math.BigInteger 类和 java.math.BigDecimal 类。这两个类用于高精度计算，其中 BigInteger 类是针对整型大数字的处理类，而 BigDecimal 类是针对大小数的处理类。

创建 BigInteger 对象之后，便可以调用 BigInteger 类提供的方法进行各种数学运算操作，表 1 列出了 BigInteger 类的常用运算方法。

| 方法名称                           | 说明                                                         |
| ---------------------------------- | ------------------------------------------------------------ |
| add(BigInteger val)                | 做加法运算                                                   |
| subtract(BigInteger val)           | 做减法运算                                                   |
| multiply(BigInteger val)           | 做乘法运算                                                   |
| divide(BigInteger val)             | 做除法运算                                                   |
| remainder(BigInteger val)          | 做取余数运算                                                 |
| divideAndRemainder(BigInteger val) | 做除法运算，返回数组的第一个值为商，第二个值为余数           |
| pow(int exponent)                  | 做参数的 exponent 次方运算                                   |
| negate()                           | 取相反数                                                     |
| shiftLeft(int n)                   | 将数字左移 n 位，如果 n 为负数，则做右移操作                 |
| shiftRight(int n)                  | 将数字右移 n 位，如果 n 为负数，则做左移操作                 |
| and(BigInteger val)                | 做与运算                                                     |
| or(BigInteger val)                 | 做或运算                                                     |
| compareTo(BigInteger val)          | 做数字的比较运算                                             |
| equals(Object obj)                 | 当参数 obj 是 Biglnteger 类型的数字并且数值相等时返回 true, 其他返回 false |
| min(BigInteger val)                | 返回较小的数值                                               |
| max(BigInteger val)                | 返回较大的数值                                               |

BigDecimal 类的方法可以用来做超大浮点数的运算，像加、减、乘和除等。在所有运算中，除法运算是最复杂的，因为在除不尽的情况下，末位小数的处理方式是需要考虑的。

BigDecimal 常用的构造方法如下。

- BigDecimal(double val)：实例化时将双精度型转换为 BigDecimal 类型。
- BigDecimal(String val)：实例化时将字符串形式转换为 BigDecimal 类型。

| 模式名称                    | 说明                                                         |
| --------------------------- | ------------------------------------------------------------ |
| BigDecimal.ROUND_UP         | 商的最后一位如果大于 0，则向前进位，正负数都如此             |
| BigDecimal.ROUND_DOWN       | 商的最后一位无论是什么数字都省略                             |
| BigDecimal.ROUND_CEILING    | 商如果是正数，按照 ROUND_UP 模式处理；如果是负数，按照 ROUND_DOWN 模式处理 |
| BigDecimal.ROUND_FLOOR      | 与 ROUND_CELING 模式相反，商如果是正数，按照 ROUND_DOWN 模式处理； 如果是负数，按照 ROUND_UP 模式处理 |
| BigDecimal.ROUND_HALF_ DOWN | 对商进行五舍六入操作。如果商最后一位小于等于 5，则做舍弃操作，否则对最后 一位进行进位操作 |
| BigDecimal.ROUND_HALF_UP    | 对商进行四舍五入操作。如果商最后一位小于 5，则做舍弃操作，否则对最后一位 进行进位操作 |
| BigDecimal.ROUND_HALF_EVEN  | 如果商的倒数第二位是奇数，则按照 ROUND_HALF_UP 处理；如果是偶数，则按 照 ROUND_HALF_DOWN 处理 |

### Java时间日期

Date 类表示系统特定的时间戳，可以精确到毫秒。Date 对象表示时间的默认顺序是星期、月、日、小时、分、秒、年。

Date 类有如下两个构造方法。

- Date()：此种形式表示分配 Date 对象并初始化此对象，以表示分配它的时间（精确到毫秒），使用该构造方法创建的对象可以获取本地的当前时间。
- Date(long date)：此种形式表示从 GMT 时间（格林尼治时间）1970 年 1 月 1 日 0 时 0 分 0 秒开始经过参数 date 指定的毫秒数。

| 方法                            | 描述                                                         |
| ------------------------------- | ------------------------------------------------------------ |
| boolean after(Date when)        | 判断此日期是否在指定日期之后                                 |
| boolean before(Date when)       | 判断此日期是否在指定日期之前                                 |
| int compareTo(Date anotherDate) | 比较两个日期的顺序                                           |
| boolean equals(Object obj)      | 比较两个日期的相等性                                         |
| long getTime()                  | 返回自 1970 年 1 月 1 日 00:00:00 GMT 以来，此 Date 对象表示的毫秒数 |
| String toString()               | 把此 Date 对象转换为以下形式的 String: dow mon dd hh:mm:ss zzz yyyy。 其中 dow 是一周中的某一天(Sun、Mon、Tue、Wed、Thu、Fri 及 Sat) |

Calendar 类是一个抽象类，它为特定瞬间与 YEAR、MONTH、DAY_OF—MONTH、HOUR 等日历字段之间的转换提供了一些方法，并为操作日历字段（如获得下星期的日期） 提供了一些方法。

当创建了一个 Calendar 对象后，就可以通过 Calendar 对象中的一些方法来处理日期、时间。Calendar 类的常用方法如表 2 所示。

| 方法                                                         | 描述                                                         |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| void add(int field, int amount)                              | 根据日历的规则，为给定的日历字段 field 添加或减去指定的时间量 amount |
| boolean after(Object when)                                   | 判断此 Calendar 表示的时间是否在指定时间 when 之后，并返回判断结果 |
| boolean before(Object when)                                  | 判断此 Calendar 表示的时间是否在指定时间 when 之前，并返回判断结果 |
| void clear()                                                 | 清空 Calendar 中的日期时间值                                 |
| int compareTo(Calendar anotherCalendar)                      | 比较两个 Calendar 对象表示的时间值（从格林威治时间 1970 年 01 月 01 日 00 时 00 分 00 秒至现在的毫秒偏移量），大则返回 1，小则返回 -1，相等返回 0 |
| int get(int field)                                           | 返回指定日历字段的值                                         |
| int getActualMaximum(int field)                              | 返回指定日历字段可能拥有的最大值                             |
| int getActualMinimum(int field)                              | 返回指定日历字段可能拥有的最小值                             |
| int getFirstDayOfWeek()                                      | 获取一星期的第一天。根据不同的国家地区，返回不同的值         |
| static Calendar getInstance()                                | 使用默认时区和语言坏境获得一个日历                           |
| static Calendar getInstance(TimeZone zone)                   | 使用指定时区和默认语言环境获得一个日历                       |
| static Calendar getInstance(TimeZone zone, Locale aLocale)   | 使用指定时区和语言环境获得一个日历                           |
| Date getTime()                                               | 返回一个表示此 Calendar 时间值（从格林威治时间 1970 年 01 月 01 日 00 时 00 分 00 秒至现在的毫秒偏移量）的 Date 对象 |
| long getTimeInMillis()                                       | 返回此 Calendar 的时间值，以毫秒为单位                       |
| void set(int field, int value)                               | 为指定的日历字段设置给定值                                   |
| void set(int year, int month, int date)                      | 设置日历字段 YEAR、MONTH 和 DAY_OF_MONTH 的值                |
| void set(int year, int month, int date, int hourOfDay, int minute, int second) | 设置字段 YEAR、MONTH、DAY_OF_MONTH、HOUR、 MINUTE 和 SECOND 的值 |
| void setFirstDayOfWeek(int value)                            | 设置一星期的第一天是哪一天                                   |
| void setTimeInMillis(long millis)                            | 用给定的 long 值设置此 Calendar 的当前时间值                 |

Calendar 类中定义了许多常量，分别表示不同的意义。

- Calendar.YEAR：年份。
- Calendar.MONTH：月份。
- Calendar.DATE：日期。
- Calendar.DAY_OF_MONTH：日期，和上面的字段意义完全相同。
- Calendar.HOUR：12小时制的小时。
- Calendar.HOUR_OF_DAY：24 小时制的小时。
- Calendar.MINUTE：分钟。
- Calendar.SECOND：秒。
- Calendar.DAY_OF_WEEK：星期几。

### 日期格式化

DateFormat 是日期/时间格式化子类的抽象类，它以与语言无关的方式格式化并解析日期或时间。日期/时间格式化子类（如 SimpleDateFormat）允许进行格式化（也就是日期→文本）、解析（文本→日期）和标准化日期。

在创建 DateFormat 对象时不能使用 new 关键字，而应该使用 DateFormat 类中的静态方法 getDateInstance()，示例代码如下：

```java
DateFormat df = DateFormat.getDatelnstance();
```

| 方法                                                         | 描述                                                         |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| String format(Date date)                                     | 将 Date 格式化日期/时间字符串                                |
| Calendar getCalendar()                                       | 获取与此日期/时间格式相关联的日历                            |
| static DateFormat getDateInstance()                          | 获取具有默认格式化风格和默认语言环境的日期格式               |
| static DateFormat getDateInstance(int style)                 | 获取具有指定格式化风格和默认语言环境的日期格式               |
| static DateFormat getDateInstance(int style, Locale locale)  | 获取具有指定格式化风格和指定语言环境的日期格式               |
| static DateFormat getDateTimeInstance()                      | 获取具有默认格式化风格和默认语言环境的日期/时间 格式         |
| static DateFormat getDateTimeInstance(int dateStyle,int timeStyle) | 获取具有指定日期/时间格式化风格和默认语言环境的 日期/时间格式 |
| static DateFormat getDateTimeInstance(int dateStyle,int timeStyle,Locale locale) | 获取具有指定日期/时间格式化风格和指定语言环境的 日期/时间格式 |
| static DateFormat getTimeInstance()                          | 获取具有默认格式化风格和默认语言环境的时间格式               |
| static DateFormat getTimeInstance(int style)                 | 获取具有指定格式化风格和默认语言环境的时间格式               |
| static DateFormat getTimeInstance(int style, Locale locale)  | 获取具有指定格式化风格和指定语言环境的时间格式               |
| void setCalendar(Calendar newCalendar)                       | 为此格式设置日历                                             |
| Date parse(String source)                                    | 将给定的字符串解析成日期/时间                                |

格式化样式主要通过 DateFormat 常量设置。将不同的常量传入到表 1 所示的方法中，以控制结果的长度。DateFormat 类的常量如下。

- SHORT：完全为数字，如 12.5.10 或 5:30pm。
- MEDIUM：较长，如 May 10，2016。
- LONG：更长，如 May 12，2016 或 11:15:32am。
- FULL：是完全指定，如 Tuesday、May 10、2012 AD 或 11:l5:42am CST。

SimpleDateFormat 是一个以与语言环境有关的方式来格式化和解析日期的具体类，它允许进行格式化（日期→文本）、解析（文本→日期）和规范化。SimpleDateFormat 使得可以选择任何用户定义的日期/时间格式的模式。

SimpleDateFormat 类主要有如下 3 种构造方法。

- SimpleDateFormat()：用默认的格式和默认的语言环境构造 SimpleDateFormat。
- SimpleDateFormat(String pattern)：用指定的格式和默认的语言环境构造 SimpleDateF ormat。
- SimpleDateFormat(String pattern,Locale locale)：用指定的格式和指定的语言环境构造 SimpleDateF ormat。


SimpleDateFormat 自定义格式中常用的字母及含义如表 2 所示。

| 字母 | 含义                                                         | 示例                                                         |
| ---- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| y    | 年份。一般用 yy 表示两位年份，yyyy 表示 4 位年份             | 使用 yy 表示的年扮，如 11； 使用 yyyy 表示的年份，如 2011    |
| M    | 月份。一般用 MM 表示月份，如果使用 MMM，则会 根据语言环境显示不同语言的月份 | 使用 MM 表示的月份，如 05； 使用 MMM 表示月份，在 Locale.CHINA 语言环境下，如“十月”；在 Locale.US 语言环境下，如 Oct |
| d    | 月份中的天数。一般用 dd 表示天数                             | 使用 dd 表示的天数，如 10                                    |
| D    | 年份中的天数。表示当天是当年的第几天， 用 D 表示             | 使用 D 表示的年份中的天数，如 295                            |
| E    | 星期几。用 E 表示，会根据语言环境的不同， 显示不 同语言的星期几 | 使用 E 表示星期几，在 Locale.CHINA 语 言环境下，如“星期四”；在 Locale.US 语 言环境下，如 Thu |
| H    | 一天中的小时数（0~23)。一般用 HH 表示小时数                  | 使用 HH 表示的小时数，如 18                                  |
| h    | 一天中的小时数（1~12)。一般使用 hh 表示小时数                | 使用 hh 表示的小时数，如 10 (注意 10 有 可能是 10 点，也可能是 22 点） |
| m    | 分钟数。一般使用 mm 表示分钟数                               | 使用 mm 表示的分钟数，如 29                                  |
| s    | 秒数。一般使用 ss 表示秒数                                   | 使用 ss 表示的秒数，如 38                                    |
| S    | 毫秒数。一般使用 SSS 表示毫秒数                              | 使用 SSS 表示的毫秒数，如 156                                |

## 数组

### 数组

数组（array）是一种最简单的复合数据类型，它是有序数据的集合，数组中的每个元素具有相同的数据类型，可以用一个统一的数组名和不同的下标来确定数组中唯一的元素。根据数组的维度，可以将其分为一维数组、二维数组和多维数组等。

总的来说，数组具有以下特点：

- 数组可以是一维数组、二维数组或多维数组。
- 数值数组元素的默认值为 0，而引用元素的默认值为 null。
- 数组的索引从 0 开始，如果数组有 n 个元素，那么数组的索引是从 0 到（n-1）。
- 数组元素可以是任何类型，包括数组类型。
- 数组类型是从抽象基类 Array 派生的引用类型。

### 一维数组

```java
type[] arrayName;    // 数据类型[] 数组名;
type arrayName[];    // 数据类型 数组名[];
type[] arrayName = new int[size];
type[] arrayName = new type[]{值 1,值 2,值 3,值 4,• • •,值 n};
type[] arrayName = {值 1,值 2,值 3,...,值 n};
```

数组可以存储一组基本数据类型的元素，但是数组整体属于引用数据类型。当声明一个数组变量时，其实是创建了一个类型为“数据类型[]”（如 int[]、double[]、String[]）的数组对象，它具有表 1 所示的方法和属性。

| 方法 | 名称     | 返回值                       |
| ---- | -------- | ---------------------------- |
|      | Object   | clone()                      |
|      | boolean  | equals(Object obj)           |
|      | Class<?> | getClass()                   |
|      | int      | hashCode()                   |
|      | void     | notify()                     |
|      | void     | notify All()                 |
|      | String   | toString()                   |
|      | void     | wait()                       |
|      | void     | wait(long timeout)           |
|      | void     | wait(long timeout,int nanos) |
| 属性 | length   | int                          |

### 二维数组

```java
type arrayName[][];    // 数据类型 数组名[][];
type[][] arrayName;    // 数据类型[][] 数组名;
type[][] arrayName = new type[][]{值 1,值 2,值 3,…,值 n};    // 在定义时初始化
type[][] arrayName = new type[size1][size2];    // 给定空间，在赋值
type[][] arrayName = new type[size][];    // 数组第二维长度为空，可变化
```

![img](http://c.biancheng.net/uploads/allimg/181016/3-1Q016131301F7.jpg)

### 多维数组

除了一维数组和二维数组外，[Java](http://c.biancheng.net/java/) 中还支持更多维的数组，如三维数组、四维数组和五维数组等，它们都属于多维数组。经过前面一维，二维的练习后不难发现，想要提高数组的维数，只要在声明数组时将索引与中括号再加一组即可，所以三维数组的声明为 int score[][][]，而四维数组为 int score[][][][]，以此类推。

### 复制数组

1) copyOf()

```java
Arrays.copyOf(dataType[] srcArray,int length);
```

srcArray 表示要进行复制的数组，length 表示复制后的新数组的长度

2) CopyOfRange()

```java
Arrays.copyOfRange(dataType[] srcArray,int startIndex,int endIndex)
```

srcArray 表示原数组，startIndex 表示开始复制的起始索引，endIndex 表示终止索引。

3) arraycopy()

```java
System.arraycopy(dataType[] srcArray,int srcIndex,int destArray,int destIndex,int length)
```

srcArray 表示原数组，srcIndex 表示源数组中的起始索引，destArray 表示目标数组，destIndex 表示目标数组中的起始索引，length 表示要复制的数组长度。

4) clone()

```java
array_name.clone()
```

## 类和对象

### 面向对象

继承性

如同生活中的子女继承父母拥有的所有财产，程序中的继承性是指子类拥有父类的全部特征和行为，这是类之间的一种关系。Java 只支持单继承。

封装性

封装是将代码及其处理的数据绑定在一起的一种编程机制，该机制保证了程序和数据都不受外部干扰且不被误用。封装的目的在于保护信息，使用它的主要优点如下。

- 保护类中的信息，它可以阻止在外部定义的代码随意访问内部代码和数据。
- 隐藏细节信息，一些不需要程序员修改和使用的信息，比如取款机中的键盘，用户只需要知道按哪个键实现什么操作就可以，至于它内部是如何运行的，用户不需要知道。
- 有助于建立各个系统之间的松耦合关系，提高系统的独立性。当一个系统的实现方式发生变化时，只要它的接口不变，就不会影响其他系统的使用。
- 提高软件的复用率，降低成本。每个系统都是一个相对独立的整体，可以在不同的环境中得到使用。

Java 语言的基本封装单位是类。由于类的用途是封装复杂性，所以类的内部有隐藏实现复杂性的机制。Java 提供了私有和公有的访问模式，类的公有接口代表外部的用户应该知道或可以知道的每件东西，私有的方法数据只能通过该类的成员代码来访问，这就可以确保不会发生不希望的事情。

多态性

面向对象的多态性，即“一个接口，多个方法”。多态性体现在父类中定义的属性和方法被子类继承后，可以具有不同的属性或表现方式。多态性允许一个接口被多个同类使用，弥补了单继承的不足。

### 类

- `public`：表示“共有”的意思。如果使用 public 修饰，则可以被其他类和程序访问。每个 Java 程序的主类都必须是 public 类，作为公共工具供其他类和程序使用的类应定义为 public 类。
- `abstract`：如果类被 abstract 修饰，则该类为抽象类，抽象类不能被实例化，但抽象类中可以有抽象方法（使用 abstract 修饰的方法）和具体方法（没有使用 abstract 修饰的方法）。继承该抽象类的所有子类都必须实现该抽象类中的所有抽象方法（除非子类也是抽象类）。
- `final`：如果类被 final 修饰，则不允许被继承。
- `class`：声明类的关键字。
- `class_name`：类的名称。
- `extends`：表示继承其他类。
- `implements`：表示实现某些接口。
- `property_type`：表示成员变量的类型。
- `property`：表示成员变量名称。
- `function()`：表示成员方法名称。

Java 类名的命名规则：

1. 类名应该以下划线（_）或字母开头，最好以字母开头。
2. 第一个字母最好大写，如果类名由多个单词组成，则每个单词的首字母最好都大写。
3. 类名不能为 Java 中的关键字，例如 boolean、this、int 等。
4. 类名不能包含任何嵌入的空格或点号以及除了下划线（_）和美元符号（$）字符之外的特殊字符。

### 类的属性

各参数的含义如下。

- public、protected、private：用于表示成员变量的访问权限。
- static：表示该成员变量为类变量，也称为静态变量。
- final：表示将该成员变量声明为常量，其值无法更改。
- type：表示变量的类型。
- variable_name：表示变量名称。

### this

大部分时候，普通方法访问其他方法、成员变量时无须使用 this 前缀，但如果方法里有个局部变量和成员变量同名，但程序又需要在该方法里访问这个被覆盖的成员变量，则必须使用 this 前缀。

### this.方法名

this 关键字最大的作用就是让类中一个方法，访问该类里的另一个方法或实例变量。

### this( )访问构造方法

this( ) 用来访问本类的构造方法（构造方法是类的一种特殊方法，方法名称和类名相同，没有返回值。详细了解可参考《[Java构造方法](http://c.biancheng.net/view/976.html)》一节），括号中可以有参数，如果有参数就是调用指定的有参构造方法。

### 显式创建对象

1. 使用 new 关键字创建对象

这是常用的创建对象的方法，语法格式如下：

```
类名 对象名 = new 类名()；
```

2. 调用 java.lang.Class 或者 java.lang.reflect.Constuctor 类的 newlnstance() 实例方法

在 Java 中，可以使用 java.lang.Class 或者 java.lang.reflect.Constuctor 类的 newlnstance() 实例方法来创建对象，代码格式如下：

```
java.lang.Class Class 类对象名称 = java.lang.Class.forName(要实例化的类全称);
类名 对象名 = (类名)Class类对象名称.newInstance();
```


调用 java.lang.Class 类中的 forName() 方法时，需要将要实例化的类的全称（比如 com.mxl.package.Student）作为参数传递过去，然后再调用 java.lang.Class 类对象的 newInstance() 方法创建对象。

3. 调用对象的 clone() 方法

该方法不常用，使用该方法创建对象时，要实例化的类必须继承 java.lang.Cloneable 接口。 调用对象的 clone() 方法创建对象的语法格式如下：

```
类名对象名 = (类名)已创建好的类对象名.clone();
```

4. 调用 java.io.ObjectlnputStream 对象的 readObject() 方法

### 隐含创建对象

1）String strName = "strValue"，其中的“strValue”就是一个 String 对象，由 Java 虚拟机隐含地创建。

2）字符串的“+”运算符运算的结果为一个新的 String 对象，示例如下：

```java
String str1 = "Hello";
String str2 = "Java";
String str3 = str1+str2;    // str3引用一个新的String对象
```

3）当 Java 虚拟机加载一个类时，会隐含地创建描述这个类的 Class 实例。

### 对象比较

总结的来说：

　　1）对于==，比较的是值是否相等

​               如果作用于基本数据类型的变量，则直接比较其存储的 “值”是否相等；

​               如果作用于引用类型的变量，则比较的是所指向的对象的地址

　　2）对于equals方法，注意：equals方法不能作用于基本数据类型的变量，equals继承Object类，比较的是是否是同一个对象

　　　　如果没有对equals方法进行重写，则比较的是引用类型的变量所指向的对象的地址；

　　　　诸如String、Date等类对equals方法进行了重写的话，比较的是所指向的对象的内容。


### “＝＝”、equals和hashCode

1）“＝＝”运算符用来比较两个变量的值是否相等。也就是说，该运算符用于比较变量对应的内存中所存储的数值是否相同，要比较两个基本类型的数据或两个引用变量是否相等，只能使用“＝＝”运算符。

2）equals是Object类提供的方法之一。每一个Java类都继承自Object类，所以每一个对象都具有equals这个方法。Object类中定义的equals（Object）方法是直接使用“＝＝”运算符比较的两个对象，所以在没有覆盖equals（Object）方法的情况下，equals（Object）与“＝＝”运算符一样，比较的是引用。

3）hashCode（）方法是从Object类中继承过来的，它也用来鉴定两个对象是否相等。Object类中的hashCode（）方法返回对象在内存中地址转换成的一个int值，所以如果没有重写hash-Code（）方法，任何对象的hashCode（）方法都是不相等的。

Java语言规范要求equals方法具有下面的特性：

1）自反性：对于任何非空引用x，x.equals（x）应该返回true。

2）对称性：对于任何引用x和y，当且仅当y.equals（x）返回true，x.equals（y）也应该返回true。

3）传递性：对于任何引用x、y和z，如果x.equals（y）返回true，y.equals（z）返回true，x.equals（z）也应该返回true。

4）一致性：如果x和y引用的对象没有发生变化，反复调用x.equals（y）应该返回同样的结果。

5）对于任意非空引用x，x.equals（null）应该返回false。

### 访问控制修饰

| 访问范围         | private  | friendly(默认) | protected | public |
| ---------------- | -------- | -------------- | --------- | ------ |
| 同一个类         | 可访问   | 可访问         | 可访问    | 可访问 |
| 同一包中的其他类 | 不可访问 | 可访问         | 可访问    | 可访问 |
| 不同包中的子类   | 不可访问 | 不可访问       | 可访问    | 可访问 |
| 不同包中的非子类 | 不可访问 | 不可访问       | 不可访问  | 可访问 |

访问控制在面向对象技术中处于很重要的地位，合理地使用访问控制符，可以通过降低类和类之间的耦合性（关联性）来降低整个项目的复杂度，也便于整个项目的开发和维护。在 Java 语言中，访问控制修饰符有 4 种。

1. private

用 private 修饰的类成员，只能被该类自身的方法访问和修改，而不能被任何其他类（包括该类的子类）访问和引用。因此，private 修饰符具有最高的保护级别。例如，设 PhoneCard 是电话卡类，电话卡都有密码，因此该类有一个密码域，可以把该类的密码域声明为私有成员。

2. friendly（默认）

如果一个类没有访问控制符，说明它具有默认的访问控制特性。这种默认的访问控制权规定，该类只能被同一个包中的类访问和引用，而不能被其他包中的类使用，即使其他包中有该类的子类。这种访问特性又称为包访问性（package private）。

同样，类内的成员如果没有访问控制符，也说明它们具有包访问性，或称为友元（friend）。定义在同一个文件夹中的所有类属于一个包，所以前面的程序要把用户自定义的类放在同一个文件夹中（Java 项目默认的包），以便不加修饰符也能运行。

3. protected

用保护访问控制符 protected 修饰的类成员可以被三种类所访问：该类自身、与它在同一个包中的其他类以及在其他包中的该类的子类。使用 protected 修饰符的主要作用，是允许其他包中它的子类来访问父类的特定属性和方法，否则可以使用默认访问控制符。

4. public

当一个类被声明为 public 时，它就具有了被其他包中的类访问的可能性，只要包中的其他类在程序中使用 import 语句引入 public 类，就可以访问和引用这个类。

类中被设定为 public 的方法是这个类对外的接口部分，避免了程序的其他部分直接去操作类内的数据，实际就是数据封装思想的体现。每个 Java 程序的主类都必须是 public 类，也是基于相同的原因。

### static

在类中，使用 static 修饰符修饰的属性（成员变量）称为静态变量，也可以称为类变量，常量称为静态常量，方法称为静态方法或类方法，它们统称为静态成员，归整个类所有。

静态成员不依赖于类的特定实例，被类的所有实例共享，就是说 static 修饰的方法或者变量不需要依赖于对象来进行访问，只要这个类被加载，[Java](http://c.biancheng.net/java/) 虚拟机就可以根据类名找到它们。

### 静态变量

类的成员变量可以分为以下两种：

1. 静态变量（或称为类变量），指被 static 修饰的成员变量。
2. 实例变量，指没有被 static 修饰的成员变量。


静态变量与实例变量的区别如下：

1）静态变量

- 运行时，Java 虚拟机只为静态变量分配一次内存，在加载类的过程中完成静态变量的内存分配。
- 在类的内部，可以在任何方法内直接访问静态变量。
- 在其他类中，可以通过类名访问该类中的静态变量。


2）实例变量

- 每创建一个实例，Java 虚拟机就会为实例变量分配一次内存。
- 在类的内部，可以在非静态方法中直接访问实例变量。
- 在本类的静态方法或其他类中则需要通过类的实例对象进行访问。

### 静态方法

与成员变量类似，成员方法也可以分为以下两种：

1. 静态方法（或称为类方法），指被 static 修饰的成员方法。
2. 实例方法，指没有被 static 修饰的成员方法。


静态方法与实例方法的区别如下：

- 静态方法不需要通过它所属的类的任何实例就可以被调用，因此在静态方法中不能使用 this 关键字，也不能直接访问所属类的实例变量和实例方法，但是可以直接访问所属类的静态变量和静态方法。另外，和 this 关键字一样，super 关键字也与类的特定实例相关，所以在静态方法中也不能使用 super 关键字。
- 在实例方法中可以直接访问所属类的静态变量、静态方法、实例变量和实例方法。

### 静态代码块

静态代码块指 Java 类中的 static{ } 代码块，主要用于初始化类，为类的静态变量赋初始值，提升程序性能。

静态代码块的特点如下：

- 静态代码块类似于一个方法，但它不可以存在于任何方法体中。
- 静态代码块可以置于类中的任何地方，类中可以有多个静态初始化块。 
- Java 虚拟机在加载类时执行静态代码块，所以很多时候会将一些只需要进行一次的初始化操作都放在 static 代码块中进行。
- 如果类中包含多个静态代码块，则 Java 虚拟机将按它们在类中出现的顺序依次执行它们，每个静态代码块只会被执行一次。
- 静态代码块与静态方法一样，不能直接访问类的实例变量和实例方法，而需要通过类的实例对象来访问。

### final

final 在 [Java](http://c.biancheng.net/java/) 中的意思是最终，也可以称为完结器，表示对象是最终形态的，不可改变的意思。final 应用于类、方法和变量时意义是不同的，但本质是一样的，都表示不可改变，类似 [C#](http://c.biancheng.net/csharp/) 里的 sealed 关键字。

使用 final 关键字声明类、变量和方法需要注意以下几点：

- final 用在变量的前面表示变量的值不可以改变，此时该变量可以被称为常量。
- final 用在方法的前面表示方法不可以被重写（子类中如果创建了一个与父类中相同名称、相同返回值类型、相同参数列表的方法，只是方法体中的实现不同，以实现不同于父类的功能，这种方式被称为方法重写，又称为方法覆盖。这里了解即可，教程后面我们会详细讲解）。
- final 用在类的前面表示该类不能有子类，即该类不可以被继承。

### final 修饰变量

final 修饰的变量即成为常量，只能赋值一次，但是 final 所修饰局部变量和成员变量有所不同。

1. final 修饰的局部变量必须使用之前被赋值一次才能使用。
2. final 修饰的成员变量在声明时没有赋值的叫“空白 final 变量”。空白 final 变量必须在构造方法或静态代码块中初始化。

### final修饰方法

final 修饰的方法不可被重写，如果出于某些原因，不希望子类重写父类的某个方法，则可以使用 final 修饰该方法。

Java 提供的 Object 类里就有一个 final 方法 getClass()，因为 Java 不希望任何类重写这个方法，所以使用 final 把这个方法密封起来。但对于该类提供的 toString() 和 equals() 方法，都允许子类重写，因此没有使用 final 修饰它们。

### final修饰类

final 修饰的类不能被继承。当子类继承父类时，将可以访问到父类内部数据，并可通过重写父类方法来改变父类方法的实现细节，这可能导致一些不安全的因素。为了保证某个类不可被继承，则可以使用 final 修饰这个类。

### final 总结

1. final 修饰类中的变量

表示该变量一旦被初始化便不可改变，这里不可改变的意思对基本类型变量来说是其值不可变，而对对象引用类型变量来说其引用不可再变。其初始化可以在两个地方：一是其定义处，也就是说在 final 变量定义时直接给其赋值；二是在构造方法中。这两个地方只能选其一，要么在定义时给值，要么在构造方法中给值，不能同时既在定义时赋值，又在构造方法中赋予另外的值。

2. final 修饰类中的方法

说明这种方法提供的功能已经满足当前要求，不需要进行扩展，并且也不允许任何从此类继承的类来重写这种方法，但是继承仍然可以继承这个方法，也就是说可以直接使用。在声明类中，一个 final 方法只被实现一次。

3. final 修饰类

表示该类是无法被任何其他类继承的，意味着此类在一个继承树中是一个叶子类，并且此类的设计已被认为很完美而不需要进行修改或扩展。

### main()方法

在 [Java](http://c.biancheng.net/java/) 中，main() 方法是 Java 应用程序的入口方法，程序在运行的时候，第一个执行的方法就是 main() 方法。main() 方法和其他的方法有很大的不同。

其中，使用 main() 方法时应该注意如下几点：

- 访问控制权限是公有的（public）。
- main() 方法是静态的。如果要在 main() 方法中调用本类中的其他方法，则该方法也必须是静态的，否则需要先创建本类的实例对象，然后再通过对象调用成员方法。
- main() 方法没有返回值，只能使用 void。
- main() 方法具有一个字符串数组参数，用来接收执行 Java 程序的命令行参数。命令行参数作为字符串，按照顺序依次对应字符串数组中的元素。
- 字符串中数组的名字（代码中的 args）可以任意设置，但是根据习惯，这个字符串数组的名字一般和 Java 规范范例中 main() 参数名保持一致，命名为 args，而方法中的其他内容都是固定不变的。
- main() 方法定义必须是“public static void main(String[] 字符串数组参数名)”。
- 一个类只能有一个 main() 方法，这是一个常用于对类进行单元测试（对软件中的最小可测试单元进行检查和验证）的技巧。

### 构造方法

构造方法是类的一种特殊方法，用来初始化类的一个新的对象，在创建对象（new 运算符）之后自动调用。[Java](http://c.biancheng.net/java/) 中的每个类都有一个默认的构造方法，并且可以有一个以上的构造方法。

Java 构造方法有以下特点：

- 方法名必须与类名相同
- 可以有 0 个、1 个或多个参数
- 没有任何返回值，包括 void
- 默认返回类型就是对象类型本身
- 只能与 new 运算符结合使用

### 浅复制(浅克隆)

被复制对象的所有变量都含有与原来的对象相同的值，而所有的对其他对象的引用仍然指向原来的对象。换言之，浅复制仅仅复制所拷贝的对象，而不复制它所引用的对象。

### 深复制(深克隆)

被复制对象的所有变量都含有与原来的对象相同的值，除去那些引用其他对象的变量。那些引用其他对象的变量将指向被复制过的新对象，而不再是原有的那些被引用的对象。换言之，深复制把要复制的对象所引用的对象都复制了一遍。

## 继承和多态

### 类的封装

封装将类的某些信息隐藏在类内部，不允许外部程序直接访问，只能通过该类提供的方法来实现对隐藏信息的操作和访问。

封装的特点：

- 只能通过规定的方法访问数据。
- 隐藏类的实例细节，方便修改和实现。


实现封装的具体步骤如下：

1. 修改属性的可见性来限制对属性的访问，一般设为 private。
2. 为每个属性创建一对赋值（setter）方法和取值（getter）方法，一般设为 public，用于属性的读写。
3. 在赋值和取值方法中，加入属性控制语句（对属性值的合法性进行判断）。

### 继承

Java 不支持多继承，只允许一个类直接继承另一个类，即子类只能有一个直接父类，extends 关键字后面只能有一个类名。

使用继承的注意点：

1. 子类一般比父类包含更多的属性和方法。
2. 父类中的 private 成员在子类中是不可见的，因此在子类中不能直接使用它们。
3. 父类和其子类间必须存在“是一个”即“is-a”的关系，否则不能用继承。但也并不是所有符合“is-a”关系的都应该用继承。例如，正方形是一个矩形，但不能让正方形类来继承矩形类，因为正方形不能从矩形扩展得到任何东西。正确的继承关系是正方形类继承图形类。
4. Java 只允许单一继承（即一个子类只能有一个直接父类），C++ 可以多重继承（即一个子类有多个直接父类）。

继承的优缺点

在面向对象语言中，继承是必不可少的、非常优秀的语言机制，它有如下优点：

1. 实现代码共享，减少创建类的工作量，使子类可以拥有父类的方法和属性。
2. 提高代码维护性和可重用性。
3. 提高代码的可扩展性，更好的实现父类的方法。


自然界的所有事物都是优点和缺点并存的，继承的缺点如下：

1. 继承是侵入性的。只要继承，就必须拥有父类的属性和方法。
2. 降低代码灵活性。子类拥有父类的属性和方法后多了些约束。
3. 增强代码耦合性（开发项目的原则为高内聚低耦合）。当父类的常量、变量和方法被修改时，需要考虑子类的修改，有可能会导致大段的代码需要重构。

### super

由于子类不能继承父类的构造方法，因此，如果要调用父类的构造方法，可以使用 super 关键字。super 可以用来访问父类的构造方法、普通方法和属性。

super 关键字的功能：

- 在子类的构造方法中显式的调用父类构造方法
- 访问父类的成员方法和变量。

当子类的成员变量或方法与父类同名时，可以使用 super 关键字来访问。如果子类重写了父类的某一个方法，即子类和父类有相同的方法定义，但是有不同的方法体，此时，我们可以通过 super 来调用父类里面的这个方法。

使用 super 访问父类中的成员与 this 关键字的使用相似，只不过它引用的是子类的父类。

### super和this的区别

this 指的是当前对象的引用，super 是当前对象的父对象的引用。下面先简单介绍一下 super 和 this 关键字的用法。

super 关键字的用法：

- super.父类属性名：调用父类中的属性
- super.父类方法名：调用父类中的方法
- super()：调用父类的无参构造方法
- super(参数)：调用父类的有参构造方法


如果构造方法的第一行代码不是 this() 和 super()，则系统会默认添加 super()。


this 关键字的用法：

- this.属性名：表示当前对象的属性
- this.方法名(参数)：表示调用当前对象的方法


当局部变量和成员变量发生冲突时，使用`this.`进行区分。

关于 [Java](http://c.biancheng.net/java/) super 和 this 关键字的异同，可简单总结为以下几条。

1. 子类和父类中变量或方法名称相同时，用 super 关键字来访问。可以理解为 super 是指向自己父类对象的一个指针。在子类中调用父类的构造方法。
2. this 是自身的一个对象，代表对象本身，可以理解为 this 是指向对象本身的一个指针。在同一个类中调用其它方法。
3. this 和 super 不能同时出现在一个构造方法里面，因为 this 必然会调用其它的构造方法，其它的构造方法中肯定会有 super 语句的存在，所以在同一个构造方法里面有相同的语句，就失去了语句的意义，编译器也不会通过。
4. this( ) 和 super( ) 都指的是对象，所以，均不可以在 static 环境中使用，包括 static 变量、static 方法和 static 语句块。
5. 从本质上讲，this 是一个指向对象本身的指针, 然而 super 是一个 Java 关键字。

### 方法重载

[Java](http://c.biancheng.net/java/) 允许同一个类中定义多个同名方法，只要它们的形参列表不同即可。如果同一个类中包含了两个或两个以上方法名相同的方法，但形参列表不同，这种情况被称为方法重载（overload）。

例如，在 JDK 的 java.io.PrintStream 中定义了十多个同名的 println() 方法。

```java
public void println(int i){…}
public void println(double d){…}
public void println(String s){…}
```

这些方法完成的功能类似，都是格式化输出。根据参数的不同来区分它们，以进行不同的格式化处理和输出。它们之间就构成了方法的重载。实际调用时，根据实参的类型来决定调用哪一个方法。

方法重载的要求是两同一不同：同一个类中方法名相同，参数列表不同。至于方法的其他部分，如方法返回值类型、修饰符等，与方法重载没有任何关系。

使用方法重载其实就是避免出现繁多的方法名，有些方法的功能是相似的，如果重新建立一个方法，重新取个方法名称，会降低程序可读性。

### 方法重写

在子类中如果创建了一个与父类中相同名称、相同返回值类型、相同参数列表的方法，只是方法体中的实现不同，以实现不同于父类的功能，这种方式被称为方法重写（override），又称为方法覆盖。当父类中的方法无法满足子类需求或子类具有特有功能的时候，需要方法重写。

子类可以根据需要，定义特定于自己的行为。既沿袭了父类的功能名称，又根据子类的需要重新实现父类方法，从而进行扩展增强。

在重写方法时，需要遵循下面的规则：

- 参数列表必须完全与被重写的方法参数列表相同。
- 返回的类型必须与被重写的方法的返回类型相同（[Java](http://c.biancheng.net/java/)1.5 版本之前返回值类型必须一样，之后的 Java 版本放宽了限制，返回值类型必须小于或者等于父类方法的返回值类型）。
- 访问权限不能比父类中被重写方法的访问权限更低（public>protected>default>private）。
- 重写方法一定不能抛出新的检査异常或者比被重写方法声明更加宽泛的检査型异常。例如，父类的一个方法声明了一个检査异常 IOException，在重写这个方法时就不能抛出 Exception，只能拋出 IOException 的子类异常，可以抛出非检査异常。


另外还要注意以下几条：

- 重写的方法可以使用 @Override 注解来标识。
- 父类的成员方法只能被它的子类重写。
- 声明为 final 的方法不能被重写。
- 声明为 static 的方法不能被重写，但是能够再次声明。
- 构造方法不能被重写。
- 子类和父类在同一个包中时，子类可以重写父类的所有方法，除了声明为 private 和 final 的方法。
- 子类和父类不在同一个包中时，子类只能重写父类的声明为 public 和 protected 的非 final 方法。
- 如果不能继承一个方法，则不能重写这个方法。

### 多态

多态性是面向对象编程的又一个重要特征，它是指在父类中定义的属性和方法被子类继承之后，可以具有不同的数据类型或表现出不同的行为，这使得同一个属性或方法在父类及其各个子类中具有不同的含义。

对面向对象来说，多态分为编译时多态和运行时多态。其中编译时多态是静态的，主要是指方法的重载，它是根据参数列表的不同来区分不同的方法。通过编译之后会变成两个不同的方法，在运行时谈不上多态。而运行时多态是动态的，它是通过动态绑定来实现的，也就是大家通常所说的多态性。

[Java](http://c.biancheng.net/java/) 实现多态有 3 个必要条件：继承、重写和向上转型。只有满足这 3 个条件，开发人员才能够在同一个继承结构中使用统一的逻辑实现代码处理不同的对象，从而执行不同的行为。

- 继承：在多态中必须存在有继承关系的子类和父类。
- 重写：子类对父类中某些方法进行重新定义，在调用这些方法时就会调用子类的方法。
- 向上转型：在多态中需要将子类的引用赋给父类对象，只有这样该引用才既能可以调用父类的方法，又能调用子类的方法。

### instanceof

严格来说 instanceof 是 [Java](http://c.biancheng.net/java/) 中的一个双目运算符，由于它是由字母组成的，所以也是 Java 的保留关键字。在 Java 中可以使用 instanceof 关键字判断一个对象是否为一个类（或接口、抽象类、父类）的实例，语法格式如下所示。

```java
boolean result = obj instanceof Class
```

Java instanceof 关键字的几种用法。

1）声明一个 class 类的对象，判断 obj 是否为 class 类的实例对象（很普遍的一种用法），如以下代码：

```java
Integer integer = new Integer(1);
System.out.println(integer instanceof Integer);  // true
```

2）声明一个 class 接口实现类的对象 obj，判断 obj 是否为 class 接口实现类的实例对象，如以下代码：

Java 集合中的 List 接口有个典型实现类 ArrayList。

```java
public class ArrayList<E> extends AbstractList<E>
    implements List<E>, RandomAccess, Cloneable, java.io.Serializable
```

所以我们可以用 instanceof 运算符判断 ArrayList 类的对象是否属于 List 接口的实例，如果是返回 true，否则返回 false。

```java
ArrayList arrayList = new ArrayList();
System.out.println(arrayList instanceof List);  // true
```

或者反过来也是返回 true

```java
List list = new ArrayList();
System.out.println(list instanceof ArrayList);  // true
```

3）obj 是 class 类的直接或间接子类

instanceof 运算符只能用作对象的判断。

### abstract

在面向对象的概念中，所有的对象都是通过类来描绘的，但是反过来，并不是所有的类都是用来描绘对象的，如果一个类中没有包含足够的信息来描绘一个具体的对象，那么这样的类称为抽象类。

在 Java 中抽象类的语法格式如下：

```java
<abstract>class<class_name> {
    <abstract><type><method_name>(parameter-iist);
}
```

如果一个方法使用 abstract 来修饰，则说明该方法是抽象方法，抽象方法只有声明没有实现。需要注意的是 abstract 关键字只能用于普通方法，不能用于 static 方法或者构造方法中。

抽象方法的 3 个特征如下：

1. 抽象方法没有方法体
2. 抽象方法必须存在于抽象类中
3. 子类重写父类时，必须重写父类所有的抽象方法


注意：在使用 abstract 关键字修饰抽象方法时不能使用 private 修饰，因为抽象方法必须被子类重写，而如果使用了 private 声明，则子类是无法重写的。

抽象类的定义和使用规则如下：

1. 抽象类和抽象方法都要使用 abstract 关键字声明。
2. 如果一个方法被声明为抽象的，那么这个类也必须声明为抽象的。而一个抽象类中，可以有 0~n 个抽象方法，以及 0~n 个具体方法。
3. 抽象类不能实例化，也就是不能使用 new 关键字创建对象。

### Interface

抽象类是从多个类中抽象出来的模板，如果将这种抽象进行的更彻底，则可以提炼出一种更加特殊的“抽象类”——接口（Interface）。接口是 [Java](http://c.biancheng.net/java/) 中最重要的概念之一，它可以被理解为一种特殊的类，不同的是接口的成员没有执行体，是由全局常量和公共的抽象方法所组成。

Java 接口的定义方式与类基本相同，不过接口定义使用的关键字是 interface，接口定义的语法格式如下：

```java
[public] interface interface_name [extends interface1_name[, interface2_name,…]] {
    // 接口体，其中可以包含定义常量和声明方法
    [public] [static] [final] type constant_name = value;    // 定义常量
    [public] [abstract] returnType method_name(parameter_list);    // 声明方法
}
```

对以上语法的说明如下：

- public 表示接口的修饰符，当没有修饰符时，则使用默认的修饰符，此时该接口的访问权限仅局限于所属的包；
- interface_name 表示接口的名称。接口名应与类名采用相同的命名规则，即如果仅从语法角度来看，接口名只要是合法的标识符即可。如果要遵守 Java 可读性规范，则接口名应由多个有意义的单词连缀而成，每个单词首字母大写，单词与单词之间无需任何分隔符。
- extends 表示接口的继承关系；
- interface1_name 表示要继承的接口名称；
- constant_name 表示变量名称，一般是 static 和 final 型的；
- returnType 表示方法的返回值类型；
- parameter_list 表示参数列表，在接口中的方法是没有方法体的。

注意：一个接口可以有多个直接父接口，但接口只能继承接口，不能继承类。

接口对于其声明、变量和方法都做了许多限制，这些限制作为接口的特征归纳如下：

- 具有 public 访问控制符的接口，允许任何类使用；没有指定 public 的接口，其访问将局限于所属的包。
- 方法的声明不需要其他修饰符，在接口中声明的方法，将隐式地声明为公有的（public）和抽象的（abstract）。
- 在 Java 接口中声明的变量其实都是常量，接口中的变量声明，将隐式地声明为 public、static 和 final，即常量，所以接口中定义的变量必须初始化。
- 接口没有构造方法，不能被实例化。

### implements 

接口的主要用途就是被实现类实现，一个类可以实现一个或多个接口，继承使用 extends 关键字，实现则使用 implements 关键字。因为一个类可以实现多个接口，这也是 Java 为单继承灵活性不足所作的补充。类实现接口的语法格式如下：

```java
<public> class <class_name> [extends superclass_name] [implements interface1_name[, interface2_name…]] {
    // 主体
}
```

对以上语法的说明如下：

- public：类的修饰符；
- superclass_name：需要继承的父类名称；
- interface1_name：要实现的接口名称。


实现接口需要注意以下几点：

- 实现接口与继承父类相似，一样可以获得所实现接口里定义的常量和方法。如果一个类需要实现多个接口，则多个接口之间以逗号分隔。
- 一个类可以继承一个父类，并同时实现多个接口，implements 部分必须放在 extends 部分之后。
- 一个类实现了一个或多个接口之后，这个类必须完全实现这些接口里所定义的全部抽象方法（也就是重写这些抽象方法）；否则，该类将保留从父接口那里继承到的抽象方法，该类也必须定义成抽象类。

### 抽象类和接口的区别

| 参数               | 抽象类                                                       | 接口                                                         |
| ------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| 实现               | 子类使用 extends 关键字来继承抽象类，如果子类不是抽象类，则需要提供抽象类中所有声明的方法的实现。 | 子类使用 implements 关键字来实现接口，需要提供接口中所有声明的方法的实现。 |
| 访问修饰符         | 可以用 public、protected 和 default 修饰                     | 默认修饰符是 public，不能使用其它修饰符                      |
| 方法               | 完全可以包含普通方法                                         | 只能包含抽象方法、静态方法、默认方法和私有方法，不能为普通方法提供方法实现 |
| 变量               | 既可以定义普通成员变量，也可以定义静态常量                   | 只能定义静态常量，不能定义普通成员变量                       |
| 构造方法           | 抽象类里的构造方法并不是用于创建对象，而是让其子类调用这些构造方法来完成属于抽象类的初始化操作 | 没有构造方法                                                 |
| 初始化块           | 可以包含初始化块                                             | 不能包含初始化块                                             |
| main 方法          | 可以有 main 方法，并且能运行                                 | 没有 main 方法                                               |
| 与普通Java类的区别 | 抽象类不能实例化，除此之外和普通 Java 类没有任何区别         | 是完全不同的类型                                             |
| 运行速度           | 比接口运行速度要快                                           | 需要时间去寻找在类种实现的方法，所以运行速度稍微有点慢       |

抽象类的应用场景：

1. 父类只知道其子类应该包含怎样的方法，不能准确知道这些子类如何实现这些方法的情况下，使用抽象类。
2. 从多个具有相同特征的类中抽象出一个抽象类，以这个抽象类作为子类的模板，从而避免了子类设计的随意性。


接口的应用场景：

1. 一般情况下，实现类和它的抽象类之前具有 "is-a" 的关系，但是如果我们想达到同样的目的，但是又不存在这种关系时，使用接口。
2. 由于 Java 中单继承的特性，导致一个类只能继承一个类，但是可以实现一个或多个接口，此时可以使用接口。


什么时候使用抽象类和接口：

- 如果拥有一些方法并且想让它们有默认实现，则使用抽象类。
- 如果想实现多重继承，那么必须使用接口。因为 Java 不支持多继承，子类不能继承多个类，但可以实现多个接口，因此可以使用接口。
- 如果基本功能在不断改变，那么就需要使用抽象类。如果使用接口并不断需要改变基本功能，那么就需要改变所有实现了该接口的类。

### 内部类

在类内部可定义成员变量和方法，且在类内部也可以定义另一个类。如果在类 Outer 的内部再定义一个类 Inner，此时类 Inner 就称为内部类（或称为嵌套类），而类 Outer 则称为外部类（或称为宿主类）。

内部类可以很好地实现隐藏，一般的非内部类是不允许有 private 与 protected 权限的，但内部类可以。内部类拥有外部类的所有元素的访问权限。

内部类可以分为：实例内部类、静态内部类和成员内部类，每种内部类都有它特定的一些特点，本节先详细介绍一些和内部类相关的知识。

在类 A 中定义类 B，那么类 B 就是内部类，也称为嵌套类，相对而言，类 A 就是外部类。如果有多层嵌套，例如类 A 中有内部类 B，而类 B 中还有内部类 C，那么通常将最外层的类称为顶层类（或者顶级类）。

内部类也可以分为多种形式，与变量非常类似，如图 1 所示。

![img](http://c.biancheng.net/uploads/allimg/181023/3-1Q02311045J93.jpg)

内部类的特点如下：

1. 内部类仍然是一个独立的类，在编译之后内部类会被编译成独立的`.class`文件，但是前面冠以外部类的类名和`$`符号。
2. 内部类不能用普通的方式访问。内部类是外部类的一个成员，因此内部类可以自由地访问外部类的成员变量，无论是否为 private 的。
3. 内部类声明成静态的，就不能随便访问外部类的成员变量，仍然是只能访问外部类的静态成员变量。

### 实例内部类

实例内部类是指没有用 static 修饰的内部类，有的地方也称为非静态内部类。示例代码如下：

```java
public class Outer {
    class Inner {
        // 实例内部类
    }
}
```

实例内部类有如下特点。

1）在外部类的静态方法和外部类以外的其他类中，必须通过外部类的实例创建内部类的实例。

2）在实例内部类中，可以访问外部类的所有成员。

3）在外部类中不能直接访问内部类的成员，而必须通过内部类的实例去访问。如果类 A 包含内部类 B，类 B 中包含内部类 C，则在类 A 中不能直接访问类 C，而应该通过类 B 的实例去访问类 C。

4）外部类实例与内部类实例是一对多的关系，也就是说一个内部类实例只对应一个外部类实例，而一个外部类实例则可以对应多个内部类实例。

5）在实例内部类中不能定义 static 成员，除非同时使用 final 和 static 修饰。

### 静态内部类

静态内部类是指使用 static 修饰的内部类。示例代码如下：

```java
public class Outer {    
   static class Inner {        
      // 静态内部类    
    }
}
```

上述示例中的 Inner 类就是静态内部类。静态内部类有如下特点。

1）在创建静态内部类的实例时，不需要创建外部类的实例。

2）静态内部类中可以定义静态成员和实例成员。外部类以外的其他类需要通过完整的类名访问静态内部类中的静态成员，如果要访问静态内部类中的实例成员，则需要通过静态内部类的实例。

3）静态内部类可以直接访问外部类的静态成员，如果要访问外部类的实例成员，则需要通过外部类的实例去访问。

### 局部内部类

局部内部类是指在一个方法中定义的内部类。示例代码如下：

```java
public class Test {    
   public void method() { 
      class Inner {            
      // 局部内部类        
      }    
   }
}
```

局部内部类有如下特点：

1）局部内部类与局部变量一样，不能使用访问控制修饰符（public、private 和 protected）和 static 修饰符修饰。

2）局部内部类只在当前方法中有效。

3）局部内部类中不能定义 static 成员。

4）局部内部类中还可以包含内部类，但是这些内部类也不能使用访问控制修饰符（public、private 和 protected）和 static 修饰符修饰。

5）在局部内部类中可以访问外部类的所有成员。

6）在局部内部类中只可以访问当前方法中 final 类型的参数与变量。如果方法中的成员与外部类中的成员同名，则可以使用 <OuterClassName>.this.<MemberName> 的形式访问外部类中的成员。

### 匿名类

匿名类是指没有类名的内部类，必须在创建时使用 new 语句来声明类。其语法形式如下：

```java
new <类或接口>() {    
       // 类的主体
};
```

这种形式的 new 语句声明一个新的匿名类，它对一个给定的类进行扩展，或者实现一个给定的接口。使用匿名类可使代码更加简洁、紧凑，模块化程度更高。

匿名类有两种实现方式：

- 继承一个类，重写其方法。
- 实现一个接口（可以是多个），实现其方法。

### 匿名内部类

匿名类有如下特点：

1）匿名类和局部内部类一样，可以访问外部类的所有成员。如果匿名类位于一个方法中，则匿名类只能访问方法中 final 类型的局部变量和参数。

2）匿名类中允许使用非静态代码块进行成员初始化操作。

3）匿名类的非静态代码块会在父类的构造方法之后被执行。

### Lambda表达式

Lambda 表达式（Lambda expression）是一个匿名函数，基于数学中的λ演算得名，也可称为闭包（Closure）。现在很多语言都支持 Lambda 表达式，如 [C++](http://c.biancheng.net/cplus/)、[C#](http://c.biancheng.net/csharp/)、[Java](http://c.biancheng.net/java/)、 [Python](http://c.biancheng.net/python/) 和 [JavaScript](http://c.biancheng.net/js/) 等。

Lambda 表达式标准语法形式如下：

```
(参数列表) -> {
  // Lambda表达式体
}
```

`->`被称为箭头操作符或 Lambda 操作符，箭头操作符将 Lambda 表达式拆分成两部分：

- 左侧：Lambda 表达式的参数列表。
- 右侧：Lambda 表达式中所需执行的功能，用`{ }`包起来，即 Lambda 体。

Java Lambda 表达式的优缺点

优点：

1. 代码简洁，开发迅速
2. 方便函数式编程
3. 非常容易进行并行计算
4. Java 引入 Lambda，改善了集合操作（引入 Stream API）

缺点：

1. 代码可读性变差
2. 在非并行计算中，很多计算未必有传统的 for 性能要高
3. 不容易进行调试

### Lambda表达式的3种简写方式

省略参数类型

Lambda 表达式可以根据上下文环境推断出参数类型。calculate 方法中 Lambda 表达式能推断出参数 a 和 b 是 int 类型，简化形式如下：

```java
public static Calculable calculate(char opr) {
    Calculable result;
    if (opr == '+') {
        // Lambda表达式实现Calculable接口
        result = (a, b) -> {
            return a + b;
        };
    } else {
        // Lambda表达式实现Calculable接口
        result = (a, b) -> {
            return a - b;
        };
    }
    return result;
}
```

省略参数小括号

如果 Lambda 表达式中的参数只有一个，可以省略参数小括号。修改 Calculable 接口中的 calculateInt 方法，代码如下。

```java
// 可计算接口
@FunctionalInterface
public interface Calculable {
    // 计算一个int数值
    int calculateInt(int a);
}
```

其中 calculateInt 方法只有一个 int 类型参数，返回值也是 int 类型。调用 calculateInt 方法代码如下：

```java
public static void main(String[] args) {
    int n1 = 10;
    // 实现二次方计算Calculable对象
    Calculable f1 = calculate(2);
    // 实现三次方计算Calculable对象
    Calculable f2 = calculate(3);
    // 调用calculateInt方法进行加法计算
    System.out.printf("%d二次方 = %d \n", n1, f1.calculateInt(n1));
    // 调用calculateInt方法进行减法计算
    System.out.printf("%d三次方 = %d \n", n1, f2.calculateInt(n1));
}
/**
* 通过幂计算
*
* @param power 幂
* @return 实现Calculable接口对象
*/
public static Calculable calculate(int power) {
    Calculable result;
    if (power == 2) {
        // Lambda表达式实现Calculable接口
        // 标准形式
        result = (int a) -> {
            return a * a;
        };
    } else {
        // Lambda表达式实现Calculable接口
        // 省略形式
        result = a -> {
            return a * a * a;
        };
    }
    return result;
}
```

省略return和大括号

如果 Lambda 表达式体中只有一条语句，那么可以省略 return 和大括号，代码如下：

```java
public static Calculable calculate(int power) {
    Calculable result;
    if (power == 2) {
        // Lambda表达式实现Calculable接口
        // 标准形式
        result = (int a) -> {
            return a * a;
        };
    } else {
        // Lambda表达式实现Calculable接口
        // 省略形式
        result = a -> a * a * a;
    }
    return result;
}
```

### 函数式接口

对于只有一个抽象方法的接口，需要这种接口的对象时，就可以提供一个lambda表达式。这种接口称为函数式接口（functional interface）。

#### 方法引用

有时，可能已经有现成的方法可以完成你想要传递到其他代码的某个动作。例如，假设你希望只要出现一个定时器事件就打印这个事件对象。当然，为此也可以调用：

```java
Timer t = new Timer(1000, event->System.otu.println(event));
```

但是，如果直接把println方法传递到Timer构造器就更好了。具体做法如下：

```java
Timer t = new Timer(1000,system.out::println);
```

表达式System.out：：println是一个方法引用（method reference），它等价于lambda表达式x->System.out.println（x）。

要用：：操作符分隔方法名与对象或类名。主要有3种情况：

·object：：instanceMethod

·Class：：staticMethod

·Class：：instanceMethod

在前2种情况中，方法引用等价于提供方法参数的lambda表达式。前面已经提到，System.out：：println等价于x->System.out.println（x）。类似地，Math：：pow等价于（x，y）->Math.pow（x，y）。

对于第3种情况，第1个参数会成为方法的目标。例如，String：：compareToIgnoreCase等同于（x，y）->x.compareToIgnoreCase（y）。

Lambda 表达式实现的接口不是普通的接口，而是函数式接口。如果一个接口中，有且只有一个抽象的方法（Object 类中的方法不包括在内），那这个接口就可以被看做是函数式接口。这种接口只能有一个方法。如果接口中声明多个抽象方法，那么 Lambda 表达式会发生编译错误：

The target type of this expression must be a functional interface

这说明该接口不是函数式接口，为了防止在函数式接口中声明多个抽象方法，Java 8 提供了一个声明函数式接口注解 @FunctionalInterface，示例代码如下。

```java
// 可计算接口
@FunctionalInterface
public interface Calculable {    
       // 计算两个int数值    
       int calculateInt(int a, int b);
}
```

在接口之前使用 @FunctionalInterface 注解修饰，那么试图增加一个抽象方法时会发生编译错误。但可以添加默认方法和静态方法。

@FunctionalInterface 注解与 @Override 注解的作用类似。Java 8 中专门为函数式接口引入了一个新的注解 @FunctionalInterface。该注解可用于一个接口的定义上，一旦使用该注解来定义接口，编译器将会强制检查该接口是否确实有且仅有一个抽象方法，否则将会报错。需要注意的是，即使不使用该注解，只要满足函数式接口的定义，这仍然是一个函数式接口，使用起来都一样。


提示：Lambda 表达式是一个匿名方法代码，Java 中的方法必须声明在类或接口中，那么 Lambda 表达式所实现的匿名方法是在函数式接口中声明的。

### Java Lambda表达式的使用

作为参数使用Lambda表达式

Lambda 表达式一种常见的用途就是作为参数传递给方法，这需要声明参数的类型声明为函数式接口类型。示例代码如下：

```java
public static void main(String[] args) {
    int n1 = 10;
    int n2 = 5;
    // 打印加法计算结果
    display((a, b) -> {
        return a + b;
    }, n1, n2);
    // 打印减法计算结果
    display((a, b) -> a - b, n1, n2);
}
/**
* 打印计算结果
*
* @param calc Lambda表达式
* @param n1   操作数1
* @param n2   操作数2
*/
public static void display(Calculable calc, int n1, int n2) {
    System.out.println(calc.calculateInt(n1, n2));
}
```

访问变量

Lambda 表达式可以访问所在外层作用域定义的变量，包括成员变量和局部变量。

访问成员变量

成员变量包括实例成员变量和静态成员变量。在 Lambda 表达式中可以访问这些成员变量，此时的 Lambda 表达式与普通方法一样，可以读取成员变量，也可以修改成员变量。

```java
public class LambdaDemo {
    // 实例成员变量
    private int value = 10;
    // 静态成员变量
    private static int staticValue = 5;
    // 静态方法，进行加法运算
    public static Calculable add() {
        Calculable result = (int a, int b) -> {
            // 访问静态成员变量，不能访问实例成员变量
            staticValue++;
            int c = a + b + staticValue;
            // this.value;
            return c;
        };
        return result;
    }
    // 实例方法，进行减法运算
    public Calculable sub() {
        Calculable result = (int a, int b) -> {
            // 访问静态成员变量和实例成员变量
            staticValue++;
            this.value++;
            int c = a - b - staticValue - this.value;
            return c;
        };
        return result;
    }
}
```

访问局部变量

对于成员变量的访问 Lambda 表达式与普通方法没有区别，但是访问局部变量时，变量必须是 final 类型的（不可改变）。

示例代码如下：

```java
public class LambdaDemo {
    // 实例成员变量
    private int value = 10;
    // 静态成员变量
    private static int staticValue = 5;
    // 静态方法，进行加法运算
    public static Calculable add() {
        // 局部变量
        int localValue = 20;
        Calculable result = (int a, int b) -> {
            // localValue++;
            // 编译错误
            int c = a + b + localValue;
            return c;
        };
        return result;
    }
    // 实例方法，进行减法运算
    public Calculable sub() {
        // final局部变量
        final int localValue = 20;
        Calculable result = (int a, int b) -> {
            int c = a - b - staticValue - this.value;
            // localValue = c;
            // 编译错误
            return c;
        };
        return result;
    }
}
```

方法引用

方法引用可以理解为 Lambda 表达式的快捷写法，它比 Lambda 表达式更加的简洁，可读性更高，有很好的重用性。如果实现比较简单，复用的地方又不多，推荐使用 Lambda 表达式，否则应该使用方法引用。

Java 8 之后增加了双冒号`::`运算符，该运算符用于“方法引用”，注意不是调用方法。“方法引用”虽然没有直接使用 Lambda 表达式，但也与 Lambda 表达式有关，与函数式接口有关。 方法引用的语法格式如下：

```
ObjectRef::methodName 
```

其中，ObjectRef 是类名或者实例名，methodName 是相应的方法名。

注意：被引用方法的参数列表和返回值类型，必须与函数式接口方法参数列表和方法返回值类型一致，示例代码如下。

```java
public class LambdaDemo {
    // 静态方法，进行加法运算
    // 参数列表要与函数式接口方法calculateInt(int a, int b)兼容
    public static int add(int a, int b) {
        return a + b;
    }
    // 实例方法，进行减法运算
    // 参数列表要与函数式接口方法calculateInt(int a, int b)兼容
    public int sub(int a, int b) {
        return a - b;
    }
}
```

## 异常处理

### 异常

[Java](http://c.biancheng.net/java/) 中的异常又称为例外，是一个在程序执行期间发生的事件，它中断正在执行程序的正常指令流。为了能够及时有效地处理程序中的运行错误，必须使用异常类，这可以让程序具有极好的容错性且更加健壮。 

在 Java 中一个异常的产生，主要有如下三种原因：

1. Java 内部错误发生异常，Java 虚拟机产生的异常。
2. 编写的程序代码中的错误所产生的异常，例如空指针异常、数组越界异常等。
3. 通过 throw 语句手动生成的异常，一般用来告知该方法的调用者一些必要信息。


Java 通过面向对象的方法来处理异常。在一个方法的运行过程中，如果发生了异常，则这个方法会产生代表该异常的一个对象，并把它交给运行时的系统，运行时系统寻找相应的代码来处理这一异常。

我们把生成异常对象，并把它提交给运行时系统的过程称为拋出（throw）异常。运行时系统在方法的调用栈中查找，直到找到能够处理该类型异常的对象，这一个过程称为捕获（catch）异常。

### 异常类型

Java提供了两种错误的异常类，分别为Error和Exception，且它们拥有共同的父类——Throwable。

Error表示程序在运行期间出现了非常严重的错误，并且该错误是不可恢复的，由于这属于JVM层次的严重错误，因此这种错误是会导致程序终止执行的。此外，编译器不会检查Er-ror是否被处理，因此在程序中不推荐去捕获Error类型的异常，主要原因是运行时异常多是由于逻辑错误导致的，属于应该解决的错误，也就是说，一个正确的程序中是不应该存在Error的。OutOfMemoryError、ThreadDeath等都属于错误。当这些异常发生时，JVM一般会选择将线程终止。

Exception表示可恢复的异常，是编译器可以捕捉到的。它包含两种类型：检查异常（checked exception）和运行时异常（runtime exception）。

（1）检查异常检查异常是在程序中最经常碰到的异常。所有继承自Exception并且不是运行时异常的异常都是检查异常，比如最常见的IO异常和SQL异常。这种异常都发生在编译阶段，Java编译器强制程序去捕获此类型的异常，即把可能会出现这些异常的代码放到try块中，把对异常的处理的代码放到catch块中。

（2）运行时异常不同于检查异常，编译器没有强制对其进行捕获并处理。如果不对这种异常进行处理，当出现这种异常时，会由JVM来处理，例如NullPointerException异常，它就是运行时异常。在Java语言中，最常见的运行时异常包括NullPointerException（空指针异常）、ClassCastException（类型转换异常）、ArrayIndexOutOfBoundsException（数组越界异常）、Array-StoreException（数组存储异常）、BufferOverflowException（缓冲区溢出异常）、ArithmeticExcep-tion（算术异常）等。

![image-20200227222908893](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200227222908893.png)

| 异常类型                      | 说明                                                  |
| ----------------------------- | ----------------------------------------------------- |
| ArithmeticException           | 算术错误异常，如以零做除数                            |
| ArraylndexOutOfBoundException | 数组索引越界                                          |
| ArrayStoreException           | 向类型不兼容的数组元素赋值                            |
| ClassCastException            | 类型转换异常                                          |
| IllegalArgumentException      | 使用非法实参调用方法                                  |
| lIIegalStateException         | 环境或应用程序处于不正确的状态                        |
| lIIegalThreadStateException   | 被请求的操作与当前线程状态不兼容                      |
| IndexOutOfBoundsException     | 某种类型的索引越界                                    |
| NullPointerException          | 尝试访问 null 对象成员，空指针异常                    |
| NegativeArraySizeException    | 再负数范围内创建的数组                                |
| NumberFormatException         | 数字转化格式异常，比如字符串到 float 型数字的转换无效 |
| TypeNotPresentException       | 类型未找到                                            |

| 异常类型                     | 说明                       |
| ---------------------------- | -------------------------- |
| ClassNotFoundException       | 没有找到类                 |
| IllegalAccessException       | 访问类被拒绝               |
| InstantiationException       | 试图创建抽象类或接口的对象 |
| InterruptedException         | 线程被另一个线程中断       |
| NoSuchFieldException         | 请求的域不存在             |
| NoSuchMethodException        | 请求的方法不存在           |
| ReflectiveOperationException | 与反射有关的异常的超类     |

### Error和Exception的异同

Error（错误）和 Exception（异常）都是 java.lang.Throwable 类的子类，在 [Java](http://c.biancheng.net/java/) 代码中只有继承了 Throwable 类的实例才能被 throw 或者 catch。

Exception 和 Error 体现了 Java 平台设计者对不同异常情况的分类，Exception 是程序正常运行过程中可以预料到的意外情况，并且应该被开发者捕获，进行相应的处理。Error 是指正常情况下不大可能出现的情况，绝大部分的 Error 都会导致程序处于非正常、不可恢复状态。所以不需要被开发者捕获。

Error 错误是任何处理技术都无法恢复的情况，肯定会导致程序非正常终止。并且 Error 错误属于未检查类型，大多数发生在运行时。Exception 又分为可检查（checked）异常和不检查（unchecked）异常，可检查异常在源码里必须显示的进行捕获处理，这里是编译期检查的一部分。不检查异常就是所谓的运行时异常，通常是可以编码避免的逻辑错误，具体根据需要来判断是否需要捕获，并不会在编译器强制要求。

如下是常见的 Error 和 Exception：

1）运行时异常（RuntimeException）：

- NullPropagation：空指针异常；
- ClassCastException：类型强制转换异常
- IllegalArgumentException：传递非法参数异常
- IndexOutOfBoundsException：下标越界异常
- NumberFormatException：数字格式异常


2）非运行时异常：

- ClassNotFoundException：找不到指定 class 的异常
- IOException：IO 操作异常


3）错误（Error）：

- NoClassDefFoundError：找不到 class 定义异常
- StackOverflowError：深递归导致栈被耗尽而抛出的异常
- OutOfMemoryError：内存溢出异常

### 异常处理机制

[Java](http://c.biancheng.net/java/) 的异常处理通过 5 个关键字来实现：try、catch、throw、throws 和 finally。try catch 语句用于捕获并处理异常，finally 语句用于在任何情况下（除特殊情况外）都必须执行的代码，throw 语句用于拋出异常，throws 语句用于声明可能会出现的异常。

本节先主要介绍异常处理的机制及基本的语句结构。

Java 的异常处理机制提供了一种结构性和控制性的方式来处理程序执行期间发生的事件。异常处理的机制如下：

- 在方法中用 try catch 语句捕获并处理异常，catch 语句可以有多个，用来匹配多个异常。
- 对于处理不了的异常或者要转型的异常，在方法的声明处通过 throws 语句拋出异常，即由上层的调用方法来处理。

以下代码是异常处理程序的基本结构：

```java
try {
    逻辑程序块
} catch(ExceptionType1 e) {
    处理代码块1
} catch (ExceptionType2 e) {
    处理代码块2
    throw(e);    // 再抛出这个"异常"
} finally {
    释放资源代码块
}
```

java 的异常处理通过 5 个关键字来实现：try、catch、throw、throws 和 finally。try catch 语句用于捕获并处理异常，finally 语句用于在任何情况下（除特殊情况外）都必须执行的代码，throw 语句用于拋出异常，throws 语句用于声明可能会出现的异常。

### try catch finally语句

finally 语句可以与前面介绍的 [try catch](http://c-local.biancheng.net/view/6732.html) 语句块匹配使用，语法格式如下：

```java
try {
    // 可能会发生异常的语句
} catch(ExceptionType e) {
    // 处理异常语句
} finally {
    // 清理代码块
}
```

对于以上格式，无论是否发生异常（除特殊情况外），finally 语句块中的代码都会被执行。此外，finally 语句也可以和 try 语句匹配使用，其语法格式如下：

```java
try {
    // 逻辑代码块
} finally {
    // 清理代码块
}
```

使用 try-catch-finally 语句时需注意以下几点：

1. 异常处理语法结构中只有 try 块是必需的，也就是说，如果没有 try 块，则不能有后面的 catch 块和 finally 块；
2. catch 块和 finally 块都是可选的，但 catch 块和 finally 块至少出现其中之一，也可以同时出现；
3. 可以有多个 catch 块，捕获父类异常的 catch 块必须位于捕获子类异常的后面；
4. 不能只有 try 块，既没有 catch 块，也没有 finally 块；
5. 多个 catch 块必须位于 try 块之后，finally 块必须位于所有的 catch 块之后。
6. finally 与 try 语句块匹配的语法格式，此种情况会导致异常丢失，所以不常见。

一般情况下，无论是否有异常拋出，都会执行 finally 语句块中的语句，执行流程如图 1 所示。

![img](http://c.biancheng.net/uploads/allimg/181024/3-1Q024110159364.jpg)

try catch finally 语句块的执行情况可以细分为以下 3 种情况：

1. 如果 try 代码块中没有拋出异常，则执行完 try 代码块之后直接执行 finally 代码块，然后执行 try catch finally 语句块之后的语句。
2. 如果 try 代码块中拋出异常，并被 catch 子句捕捉，那么在拋出异常的地方终止 try 代码块的执行，转而执行相匹配的 catch 代码块，之后执行 finally 代码块。如果 finally 代码块中没有拋出异常，则继续执行 try catch finally 语句块之后的语句；如果 finally 代码块中拋出异常，则把该异常传递给该方法的调用者。
3. 如果 try 代码块中拋出的异常没有被任何 catch 子句捕捉到，那么将直接执行 finally 代码块中的语句，并把该异常传递给该方法的调用者。

除非在 try 块、catch 块中调用了退出虚拟机的方法`System.exit(int status)`，否则不管在 try 块或者 catch 块中执行怎样的代码，出现怎样的情况，异常处理的 finally 块总会执行。

### try-with-resources

JDK1.7开始，java引入了 try-with-resources 声明，将 try-catch-finally 简化为 try-catch，这其实是一种语法糖，在编译时会进行转化为 try-catch-finally 语句。新的声明包含三部分：try-with-resources 声明、try 块、catch 块。它要求在 try-with-resources 声明中定义的变量实现了 AutoCloseable 接口，这样在系统可以自动调用它们的close方法，从而替代了finally中关闭资源的功能。

在 try-with-resources 结构中，异常处理也有两种情况（注意，不论 try 中是否有异常，都会首先自动执行 close 方法，然后才判断是否进入 catch 块，建议阅读后面的反编译代码）：

try 块没有发生异常时，自动调用 close 方法，如果发生异常，catch 块捕捉并处理异常。
try 块发生异常，然后自动调用 close 方法，如果 close 也发生异常，catch 块只会捕捉 try 块抛出的异常，close 方法的异常会在catch 中被压制，但是你可以在catch块中，用 Throwable.getSuppressed 方法来获取到压制异常的数组。

### 声明和抛出异常

[Java](http://c.biancheng.net/java/) 中的异常处理除了捕获异常和处理异常之外，还包括声明异常和拋出异常。实现声明和抛出异常的关键字非常相似，它们是 throws 和 throw。可以通过 throws 关键字在方法上声明该方法要拋出的异常，然后在方法内部通过 throw 拋出异常对象。

### throws声明异常

当一个方法产生一个它不处理的异常时，那么就需要在该方法的头部声明这个异常，以便将该异常传递到方法的外部进行处理。使用 throws 声明的方法表示此方法不处理异常。throws 具体格式如下：

```java
returnType method_name(paramList) throws Exception 1,Exception2,…{…}
```

其中，returnType 表示返回值类型；method_name 表示方法名；paramList 表示参数列表；Exception 1，Exception2，… 表示异常类。

如果有多个异常类，它们之间用逗号分隔。这些异常类可以是方法中调用了可能拋出异常的方法而产生的异常，也可以是方法体中生成并拋出的异常。

使用 throws 声明抛出异常的思路是，当前方法不知道如何处理这种类型的异常，该异常应该由向上一级的调用者处理；如果 main 方法也不知道如何处理这种类型的异常，也可以使用 throws 声明抛出异常，该异常将交给 JVM 处理。JVM 对异常的处理方法是，打印异常的跟踪栈信息，并中止程序运行，这就是前面程序在遇到异常后自动结束的原因。

### throw 拋出异常

与 throws 不同的是，throw 语句用来直接拋出一个异常，后接一个可拋出的异常类对象，其语法格式如下：

```
throw ExceptionObject;
```


其中，ExceptionObject 必须是 Throwable 类或其子类的对象。如果是自定义异常类，也必须是 Throwable 的直接或间接子类。例如，以下语句在编译时将会产生语法错误：

```
throw new String("拋出异常");    // String类不是Throwable类的子类
```


当 throw 语句执行时，它后面的语句将不执行，此时程序转向调用者程序，寻找与之相匹配的 catch 语句，执行相应的异常处理程序。如果没有找到相匹配的 catch 语句，则再转向上一层的调用程序。这样逐层向上，直到最外层的异常处理程序终止程序并打印出调用栈情况。

throw 关键字不会单独使用，它的使用完全符合异常的处理机制，但是，一般来讲用户都在避免异常的产生，所以不会手工抛出一个新的异常类的实例，而往往会抛出程序中已经产生的异常类的实例。

**throws 关键字和 throw 关键字在使用上的几点区别如下**：

- throws 用来声明一个方法可能抛出的所有异常信息，表示出现异常的一种可能性，但并不一定会发生这些异常；throw 则是指拋出的一个具体的异常类型，执行 throw 则一定抛出了某种异常对象。
- 通常在一个方法（类）的声明处通过 throws 声明方法（类）可能拋出的异常信息，而在方法（类）内部通过 throw 声明一个具体的异常信息。
- throws 通常不用显示地捕获异常，可由系统自动将所有捕获的异常信息抛给上级方法； throw 则需要用户自己捕获相关的异常，而后再对其进行相关包装，最后将包装后的异常信息抛出。

throw和throws的区别如下。

◎ 位置不同：throws作用在方法上，后面跟着的是异常的类；而throw作用在方法内，后面跟着的是异常的对象。

◎ 功能不同：throws用来声明方法在运行过程中可能出现的异常，以便调用者根据不同的异常类型预先定义不同的处理方式；throw用来抛出封装了异常信息的对象，程序在执行到throw时后续的代码将不再执行，而是跳转到调用者，并将异常信息抛给调用者。也就是说，throw后面的语句块将无法被执行（finally语句块除外）。

### 多异常捕获

[Java](http://c.biancheng.net/java/) 7 推出了多异常捕获技术，可以把这些异常合并处理。上述代码修改如下：

```java
try{    
     // 可能会发生异常的语句
} catch (IOException | ParseException e) {    
// 调用方法methodA处理
}
```

注意：由于 FileNotFoundException 属于 IOException 异常，IOException 异常可以捕获它的所有子类异常。所以不能写成 `FileNotFoundException | IOException | ParseException` 。

使用一个 catch 块捕获多种类型的异常时需要注意如下两个地方。

- 捕获多种类型的异常时，多种异常类型之间用竖线`|`隔开。
- 捕获多种类型的异常时，异常变量有隐式的 final 修饰，因此程序不能对异常变量重新赋值。

### 自定义异常

如果 [Java](http://c.biancheng.net/java/) 提供的内置异常类型不能满足程序设计的需求，这时我们可以自己设计 Java 类库或框架，其中包括异常类型。实现自定义异常类需要继承 Exception 类或其子类，如果自定义运行时异常类需继承 RuntimeException 类或其子类。

自定义异常的语法形式为：

```java
<class><自定义异常名><extends><Exception>
```

在编码规范上，一般将自定义异常类的类名命名为 XXXException，其中 XXX 用来代表该异常的作用。

自定义异常类一般包含两个构造方法：一个是无参的默认构造方法，另一个构造方法以字符串的形式接收一个定制的异常消息，并将该消息传递给超类的构造方法。

### Java.util.logging

日志用来记录程序的运行轨迹，方便查找关键信息，也方便快速定位解决问题。下面介绍 [Java](http://c.biancheng.net/java/) 自带的日志工具类 java.util.logging 的使用。

如果要生成简单的日志记录，可以使用全局日志记录器并调用其 info 方法，代码如下：

```
Logger.getGlobal().info("打印信息");
```

JDK Logging 把日志分为如下表 7 个级别，等级依次降低。

| 级别     | SEVERE   | WARNING   | INFO   | CONFIG   | FINE   | FINER   | FINEST   |
| -------- | -------- | --------- | ------ | -------- | ------ | ------- | -------- |
| 调用方法 | severe() | warning() | info() | config() | fine() | finer() | finest() |
| 含义     | 严重     | 警告      | 信息   | 配置     | 良好   | 较好    | 最好     |

Logger 的默认级别是 INFO，比 INFO 级别低的日志将不显示。Logger 的默认级别定义在 jre 安装目录的 lib 下面。

```
# Limit the message that are printed on the console to INFO and above.
java.util.logging.ConsoleHandler.level = INFO
```

所以在默认情况下，日志只显示前三个级别，对于所有的级别有下面几种记录方法：

```
logger.warning(message);
logger.fine(message);
```

同时，还可以使用 log 方法指定级别，例如：

```
logger.log(Level.FINE, message);
```

## 集合、泛型和枚举

### 集合详解

Java 集合类型分为 Collection 和 Map，它们是 Java 集合的根接口，这两个接口又包含了一些子接口或实现类。图 1 和图 2 分别为 Collection 和 Map 的子接口及其实现类。

![Collection接口结构](http://c.biancheng.net/uploads/allimg/191205/5-1912051036333V.png)

![Map接口结构](http://c.biancheng.net/uploads/allimg/191205/5-191205103G5960.png)

| 接口名称        | 作  用                                                       |
| --------------- | ------------------------------------------------------------ |
| Iterator 接口   | 集合的输出接口，主要用于遍历输出（即迭代访问）Collection 集合中的元素，Iterator 对象被称之为迭代器。迭代器接口是集合接口的父接口，实现类实现 Collection 时就必须实现 Iterator 接口。 |
| Collection 接口 | 是 List、Set 和 Queue 的父接口，是存放一组单值的最大接口。所谓的单值是指集合中的每个元素都是一个对象。一般很少直接使用此接口直接操作。 |
| Queue 接口      | Queue 是 Java 提供的队列实现，有点类似于 List。              |
| Dueue 接口      | 是 Queue 的一个子接口，为双向队列。                          |
| List 接口       | 是最常用的接口。是有序集合，允许有相同的元素。使用 List 能够精确地控制每个元素插入的位置，用户能够使用索引（元素在 List 中的位置，类似于数组下标）来访问 List 中的元素，与数组类似。 |
| Set 接口        | 不能包含重复的元素。                                         |
| Map 接口        | 是存放一对值的最大接口，即接口中的每个元素都是一对，以 key➡value 的形式保存。 |

| 类名称     | 作用                                                         |
| ---------- | ------------------------------------------------------------ |
| HashSet    | 为优化査询速度而设计的 Set。它是基于 HashMap 实现的，HashSet 底层使用 HashMap 来保存所有元素，实现比较简单 |
| TreeSet    | 实现了 Set 接口，是一个有序的 Set，这样就能从 Set 里面提取一个有序序列 |
| ArrayList  | 一个用数组实现的 List，能进行快速的随机访问，效率高而且实现了可变大小的数组 |
| ArrayDueue | 是一个基于数组实现的双端队列，按“先进先出”的方式操作集合元素 |
| LinkedList | 对顺序访问进行了优化，但随机访问的速度相对较慢。此外它还有 addFirst()、addLast()、getFirst()、getLast()、removeFirst() 和 removeLast() 等方法，能把它当成栈（Stack）或队列（Queue）来用 |
| HsahMap    | 按哈希算法来存取键对象                                       |
| TreeMap    | 可以对键对象进行排序                                         |

### Collection接口

Collection 接口是 List、Set 和 Queue 接口的父接口，通常情况下不被直接使用。Collection 接口定义了一些通用的方法，通过这些方法可以实现对集合的基本操作。定义的方法既可用于操作 Set 集合，也可用于操作 List 和 Queue 集合。

Collection 接口中常用的方法：

| 方法名称                          | 说明                                                         |
| --------------------------------- | ------------------------------------------------------------ |
| boolean add(E e)                  | 向集合中添加一个元素，如果集合对象被添加操作改变了，则返回 true。E 是元素的数据类型 |
| boolean addAll(Collection c)      | 向集合中添加集合 c 中的所有元素，如果集合对象被添加操作改变了，则返回 true。 |
| void clear()                      | 清除集合中的所有元素，将集合长度变为 0。                     |
| boolean contains(Object o)        | 判断集合中是否存在指定元素                                   |
| boolean containsAll(Collection c) | 判断集合中是否包含集合 c 中的所有元素                        |
| boolean isEmpty()                 | 判断集合是否为空                                             |
| Iterator<E>iterator()             | 返回一个 Iterator 对象，用于遍历集合中的元素                 |
| boolean remove(Object o)          | 从集合中删除一个指定元素，当集合中包含了一个或多个元素 o 时，该方法只删除第一个符合条件的元素，该方法将返回 true。 |
| boolean removeAll(Collection c)   | 从集合中删除所有在集合 c 中出现的元素（相当于把调用该方法的集合减去集合 c）。如果该操作改变了调用该方法的集合，则该方法返回 true。 |
| boolean retainAll(Collection c)   | 从集合中删除集合 c 里不包含的元素（相当于把调用该方法的集合变成该集合和集合 c 的交集），如果该操作改变了调用该方法的集合，则该方法返回 true。 |
| int size()                        | 返回集合中元素的个数                                         |
| Object[] toArray()                | 把集合转换为一个数组，所有的集合元素变成对应的数组元素。     |

### List

List 是一个有序、可重复的集合，集合中每个元素都有其对应的顺序索引。List 集合允许使用重复元素，可以通过索引来访问指定位置的集合元素。List 集合默认按元素的添加顺序设置元素的索引，第一个添加到 List 集合中的元素的索引为 0，第二个为 1，依此类推。

List 实现了 Collection 接口，它主要有两个常用的实现类：ArrayList 类和 LinkedList 类。

### ArrayList 类

ArrayList 类实现了可变数组的大小，存储在内的数据称为元素。它还提供了快速基于索引访问元素的方式，对尾部成员的增加和删除支持较好。使用 ArrayList 创建的集合，允许对集合中的元素进行快速的随机访问，不过，向 ArrayList 中插入与删除元素的速度相对较慢。

ArrayList 类的常用构造方法有如下两种重载形式：

- ArrayList()：构造一个初始容量为 10 的空列表。
- ArrayList(Collection<?extends E>c)：构造一个包含指定 Collection 元素的列表，这些元素是按照该 Collection 的迭代器返回它们的顺序排列的。


ArrayList 类除了包含 Collection 接口中的所有方法之外，还包括 List 接口中提供的如表 1 所示的方法。

| 方法名称                                    | 说明                                                         |
| ------------------------------------------- | ------------------------------------------------------------ |
| E get(int index)                            | 获取此集合中指定索引位置的元素，E 为集合中元素的数据类型     |
| int index(Object o)                         | 返回此集合中第一次出现指定元素的索引，如果此集合不包含该元 素，则返回 -1 |
| int lastIndexOf(Object o)                   | 返回此集合中最后一次出现指定元素的索引，如果此集合不包含该 元素，则返回 -1 |
| E set(int index, Eelement)                  | 将此集合中指定索引位置的元素修改为 element 参数指定的对象。 此方法返回此集合中指定索引位置的原元素 |
| List<E> subList(int fromlndex, int tolndex) | 返回一个新的集合，新集合中包含 fromlndex 和 tolndex 索引之间 的所有元素。包含 fromlndex 处的元素，不包含 tolndex 索引处的 元素 |

### LinkedList类

LinkedList 类采用链表结构保存对象，这种结构的优点是便于向集合中插入或者删除元素。需要频繁向集合中插入和删除元素时，使用 LinkedList 类比 ArrayList 类效果高，但是 LinkedList 类随机访问元素的速度则相对较慢。这里的随机访问是指检索集合中特定索引位置的元素。

LinkedList 类除了包含 Collection 接口和 List 接口中的所有方法之外，还特别提供了表 2 所示的方法。

| 方法名称           | 说明                         |
| ------------------ | ---------------------------- |
| void addFirst(E e) | 将指定元素添加到此集合的开头 |
| void addLast(E e)  | 将指定元素添加到此集合的末尾 |
| E getFirst()       | 返回此集合的第一个元素       |
| E getLast()        | 返回此集合的最后一个元素     |
| E removeFirst()    | 删除此集合中的第一个元素     |
| E removeLast()     | 删除此集合中的最后一个元素   |

### ArrayList 类和 LinkedList 类

ArrayList 与 LinkedList 都是 List 接口的实现类，因此都实现了 List 的所有未实现的方法，只是实现的方式有所不同。

ArrayList 是基于动态数组[数据结构](http://c.biancheng.net/data_structure/)的实现，访问元素速度优于 LinkedList。LinkedList 是基于链表数据结构的实现，占用的内存空间比较大，但在批量插入或删除数据时优于 ArrayList。

对于快速访问对象的需求，使用 ArrayList 实现执行效率上会比较好。需要频繁向集合中插入和删除元素时，使用 LinkedList 类比 ArrayList 类效果高。

不同的结构对应于不同的算法，有的考虑节省占用空间，有的考虑提高运行效率，对于程序员而言，它们就像是“熊掌”和“鱼肉”，不可兼得。高运行速度往往是以牺牲空间为代价的，而节省占用空间往往是以牺牲运行速度为代价的。

### Set

Set 集合类似于一个罐子，程序可以依次把多个对象“丢进”Set 集合，而 Set 集合通常不能记住元素的添加顺序。也就是说 Set 集合中的对象不按特定的方式排序，只是简单地把对象加入集合。Set 集合中不能包含重复的对象，并且最多只允许包含一个 null 元素。

Set 实现了 Collection 接口，它主要有两个常用的实现类：HashSet 类和 TreeSet类。

### HashSet 类

HashSet 是 Set 接口的典型实现，大多数时候使用 Set 集合时就是使用这个实现类。HashSet 是按照 Hash 算法来存储集合中的元素。因此具有很好的存取和查找性能。

HashSet 具有以下特点：

- 不能保证元素的排列顺序，顺序可能与添加顺序不同，顺序也有可能发生变化。
- HashSet 不是同步的，如果多个线程同时访问或修改一个 HashSet，则必须通过代码来保证其同步。
- 集合元素值可以是 null。


当向 HashSet 集合中存入一个元素时，HashSet 会调用该对象的 hashCode() 方法来得到该对象的 hashCode 值，然后根据该 hashCode 值决定该对象在 HashSet 中的存储位置。如果有两个元素通过 equals() 方法比较返回的结果为 true，但它们的 hashCode 不相等，HashSet 将会把它们存储在不同的位置，依然可以添加成功。

也就是说，两个对象的 hashCode 值相等且通过 equals() 方法比较返回结果为 true，则 HashSet 集合认为两个元素相等。

在 HashSet 类中实现了 Collection 接口中的所有方法。HashSet 类的常用构造方法重载形式如下。

- HashSet()：构造一个新的空的 Set 集合。
- HashSet(Collection<? extends E>c)：构造一个包含指定 Collection 集合元素的新 Set 集合。其中，“< >”中的 extends 表示 HashSet 的父类，即指明该 Set 集合中存放的集合元素类型。c 表示其中的元素将被存放在此 Set 集合中。

下面的代码演示了创建两种不同形式的 HashSet 对象。

```java
HashSet hs = new HashSet();    // 调用无参的构造函数创建HashSet对象
HashSet<String> hss = new HashSet<String>();    // 创建泛型的 HashSet 集合对象
```

### TreeSet 类

TreeSet 类同时实现了 Set 接口和 SortedSet 接口。SortedSet 接口是 Set 接口的子接口，可以实现对集合进行自然排序，因此使用 TreeSet 类实现的 Set 接口默认情况下是自然排序的，这里的自然排序指的是升序排序。

TreeSet 只能对实现了 Comparable 接口的类对象进行排序，因为 Comparable 接口中有一个 compareTo(Object o) 方法用于比较两个对象的大小。例如 a.compareTo(b)，如果 a 和 b 相等，则该方法返回 0；如果 a 大于 b，则该方法返回大于 0 的值；如果 a 小于 b，则该方法返回小于 0 的值。

表 1 列举了 JDK 类库中实现 Comparable 接口的类，以及这些类对象的比较方式。



| 类                                                           | 比较方式                                  |
| ------------------------------------------------------------ | ----------------------------------------- |
| 包装类（BigDecimal、Biglnteger、 Byte、Double、 Float、Integer、Long 及 Short) | 按数字大小比较                            |
| Character                                                    | 按字符的 Unicode 值的数字大小比较         |
| String                                                       | 按字符串中字符的 Unicode 值的数字大小比较 |


TreeSet 类除了实现 Collection 接口的所有方法之外，还提供了如表 2 所示的方法。



| 方法名称                                       | 说明                                                         |
| ---------------------------------------------- | ------------------------------------------------------------ |
| E first()                                      | 返回此集合中的第一个元素。其中，E 表示集合中元素的数据类型   |
| E last()                                       | 返回此集合中的最后一个元素                                   |
| E poolFirst()                                  | 获取并移除此集合中的第一个元素                               |
| E poolLast()                                   | 获取并移除此集合中的最后一个元素                             |
| SortedSet<E> subSet(E fromElement,E toElement) | 返回一个新的集合，新集合包含原集合中 fromElement 对象与 toElement 对象之间的所有对象。包含 fromElement 对象，不包含 toElement 对象 |
| SortedSet<E> headSet<E toElement〉             | 返回一个新的集合，新集合包含原集合中 toElement 对象之前的所有对象。 不包含 toElement 对象 |
| SortedSet<E> tailSet(E fromElement)            | 返回一个新的集合，新集合包含原集合中 fromElement 对象之后的所有对 象。包含 fromElement 对象 |

### Map

Map 是一种键-值对（key-value）集合，Map 集合中的每一个元素都包含一个键（key）对象和一个值（value）对象。用于保存具有映射关系的数据。

Map 集合里保存着两组值，一组值用于保存 Map 里的 key，另外一组值用于保存 Map 里的 value，key 和 value 都可以是任何引用类型的数据。Map 的 key 不允许重复，value 可以重复，即同一个 Map 对象的任何两个 key 通过 equals 方法比较总是返回 false。

Map 中的 key 和 value 之间存在单向一对一关系，即通过指定的 key，总能找到唯一的、确定的 value。从 Map 中取出数据时，只要给出指定的 key，就可以取出对应的 value。

Map 接口主要有两个实现类：HashMap 类和 TreeMap 类。其中，HashMap 类按哈希算法来存取键对象，而 TreeMap 类可以对键对象进行排序。

Map 接口中提供的常用方法如表 1 所示。

| 方法名称                                 | 说明                                                         |
| ---------------------------------------- | ------------------------------------------------------------ |
| void clear()                             | 删除该 Map 对象中的所有 key-value 对。                       |
| boolean containsKey(Object key)          | 查询 Map 中是否包含指定的 key，如果包含则返回 true。         |
| boolean containsValue(Object value)      | 查询 Map 中是否包含一个或多个 value，如果包含则返回 true。   |
| V get(Object key)                        | 返回 Map 集合中指定键对象所对应的值。V 表示值的数据类型      |
| V put(K key, V value)                    | 向 Map 集合中添加键-值对，如果当前 Map 中已有一个与该 key 相等的 key-value 对，则新的 key-value 对会覆盖原来的 key-value 对。 |
| void putAll(Map m)                       | 将指定 Map 中的 key-value 对复制到本 Map 中。                |
| V remove(Object key)                     | 从 Map 集合中删除 key 对应的键-值对，返回 key 对应的 value，如果该 key 不存在，则返回 null |
| boolean remove(Object key, Object value) | 这是 [Java](http://c.biancheng.net/java/) 8 新增的方法，删除指定 key、value 所对应的 key-value 对。如果从该 Map 中成功地删除该 key-value 对，该方法返回 true，否则返回 false。 |
| Set entrySet()                           | 返回 Map 集合中所有键-值对的 Set 集合，此 Set 集合中元素的数据类型为 Map.Entry |
| Set keySet()                             | 返回 Map 集合中所有键对象的 Set 集合                         |
| boolean isEmpty()                        | 查询该 Map 是否为空（即不包含任何 key-value 对），如果为空则返回 true。 |
| int size()                               | 返回该 Map 里 key-value 对的个数                             |
| Collection values()                      | 返回该 Map 里所有 value 组成的 Collection                    |

### 遍历Map集合

Map 集合的遍历与 List 和 Set 集合不同。Map 有两组值，因此遍历时可以只遍历值的集合，也可以只遍历键的集合，也可以同时遍历。Map 以及实现 Map 的接口类（如 HashMap、TreeMap、LinkedHashMap、Hashtable 等）都可以用以下几种方式遍历。

1）在 for 循环中使用 entries 实现 Map 的遍历（最常见和最常用的）。

```java
public static void main(String[] args) {
    Map<String, String> map = new HashMap<String, String>();
    map.put("Java入门教程", "http://c.biancheng.net/java/");
    map.put("C语言入门教程", "http://c.biancheng.net/c/");
    for (Map.Entry<String, String> entry : map.entrySet()) {
        String mapKey = entry.getKey();
        String mapValue = entry.getValue();
        System.out.println(mapKey + "：" + mapValue);
    }
}
```

2）使用 for-each 循环遍历 key 或者 values，一般适用于只需要 Map 中的 key 或者 value 时使用。性能上比 entrySet 较好。

```java
Map<String, String> map = new HashMap<String, String>();
map.put("Java入门教程", "http://c.biancheng.net/java/");
map.put("C语言入门教程", "http://c.biancheng.net/c/");
// 打印键集合
for (String key : map.keySet()) {
    System.out.println(key);
}
// 打印值集合
for (String value : map.values()) {
    System.out.println(value);
}
```

3）使用迭代器（Iterator）遍历

```java
Map<String, String> map = new HashMap<String, String>();
map.put("Java入门教程", "http://c.biancheng.net/java/");
map.put("C语言入门教程", "http://c.biancheng.net/c/");
Iterator<Entry<String, String>> entries = map.entrySet().iterator();
while (entries.hasNext()) {
    Entry<String, String> entry = entries.next();
    String key = entry.getKey();
    String value = entry.getValue();
    System.out.println(key + ":" + value);
}
```

4）通过键找值遍历，这种方式的效率比较低，因为本身从键取值是耗时的操作。

```java
for(String key : map.keySet()){
    String value = map.get(key);
    System.out.println(key+":"+value);
}
```

### Java 8为Map新增的方法

| 名称                                                         | 说明                                                         |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| Object compute(Object key, BiFunction remappingFunction)     | 该方法使用 remappingFunction 根据原 key-value 对计算一个新 value。只要新 value 不为 null，就使用新 value 覆盖原 value；如果原 value 不为 null，但新 value 为 null，则删除原 key-value 对；如果原 value、新 value 同时为 null，那么该方法不改变任何 key-value 对，直接返回 null。 |
| Object computeIfAbsent(Object key, Function mappingFunction) | 如果传给该方法的 key 参数在 Map 中对应的 value 为 null，则使用 mappingFunction 根据 key 计算一个新的结果，如果计算结果不为 null，则用计算结果覆盖原有的 value。如果原 Map 原来不包括该 key，那么该方法可能会添加一组 key-value 对。 |
| Object computeIfPresent(Object key, BiFunction remappingFunction) | 如果传给该方法的 key 参数在 Map 中对应的 value 不为 null，该方法将使用 remappingFunction 根据原 key、value 计算一个新的结果，如果计算结果不为 null，则使用该结果覆盖原来的 value；如果计算结果为 null，则删除原 key-value 对。 |
| void forEach(BiConsumer action)                              | 该方法是 Java 8 为 Map 新增的一个遍历 key-value 对的方法，通过该方法可以更简洁地遍历 Map 的 key-value 对。 |
| Object getOrDefault(Object key, V defaultValue)              | 获取指定 key 对应的 value。如果该 key 不存在，则返回 defaultValue。 |
| Object merge(Object key, Object value, BiFunction remappingFunction) | 该方法会先根据 key 参数获取该 Map 中对应的 value。如果获取的 value 为 null，则直接用传入的 value 覆盖原有的 value（在这种情况下，可能要添加一组 key-value 对）；如果获取的 value 不为 null，则使用 remappingFunction 函数根据原 value、新 value 计算一个新的结果，并用得到的结果去覆盖原有的 value。 |
| Object putIfAbsent(Object key, Object value)                 | 该方法会自动检测指定 key 对应的 value 是否为 null，如果该 key 对应的 value 为 null，该方法将会用新 value 代替原来的 null 值。 |
| Object replace(Object key, Object value)                     | 将 Map 中指定 key 对应的 value 替换成新 value。与传统 put() 方法不同的是，该方法不可能添加新的 key-value 对。如果尝试替换的 key 在原 Map 中不存在，该方法不会添加 key-value 对，而是返回 null。 |
| boolean replace(K key, V oldValue, V newValue)               | 将 Map 中指定 key-value 对的原 value 替换成新 value。如果在 Map 中找到指定的 key-value 对，则执行替换并返回 true，否则返回 false。 |
| replaceAll(BiFunction function)                              |                                                              |

###  Collections类

Collections 类是 [Java](http://c.biancheng.net/java/) 提供的一个操作 Set、List 和 Map 等集合的工具类。Collections 类提供了许多操作集合的静态方法，借助这些静态方法可以实现集合元素的排序、查找替换和复制等操作。下面介绍 Collections 类中操作集合的常用方法。

#### 排序（正向和逆向）

Collections 提供了如下方法用于对 List 集合元素进行排序。

- void reverse(List list)：对指定 List 集合元素进行逆向排序。
- void shuffle(List list)：对 List 集合元素进行随机排序（shuffle 方法模拟了“洗牌”动作）。
- void sort(List list)：根据元素的自然顺序对指定 List 集合的元素按升序进行排序。
- void sort(List list, Comparator c)：根据指定 Comparator 产生的顺序对 List 集合元素进行排序。
- void swap(List list, int i, int j)：将指定 List 集合中的 i 处元素和 j 处元素进行交换。
- void rotate(List list, int distance)：当 distance 为正数时，将 list 集合的后 distance 个元素“整体”移到前面；当 distance 为负数时，将 list 集合的前 distance 个元素“整体”移到后面。该方法不会改变集合的长度。

#### 查找、替换操作

Collections 还提供了如下常用的用于查找、替换集合元素的方法。

- int binarySearch(List list, Object key)：使用二分搜索法搜索指定的 List 集合，以获得指定对象在 List 集合中的索引。如果要使该方法可以正常工作，则必须保证 List 中的元素已经处于有序状态。
- Object max(Collection coll)：根据元素的自然顺序，返回给定集合中的最大元素。
- Object max(Collection coll, Comparator comp)：根据 Comparator 指定的顺序，返回给定集合中的最大元素。
- Object min(Collection coll)：根据元素的自然顺序，返回给定集合中的最小元素。
- Object min(Collection coll, Comparator comp)：根据 Comparator 指定的顺序，返回给定集合中的最小元素。
- void fill(List list, Object obj)：使用指定元素 obj 替换指定 List 集合中的所有元素。
- int frequency(Collection c, Object o)：返回指定集合中指定元素的出现次数。
- int indexOfSubList(List source, List target)：返回子 List 对象在父 List 对象中第一次出现的位置索引；如果父 List 中没有出现这样的子 List，则返回 -1。
- int lastIndexOfSubList(List source, List target)：返回子 List 对象在父 List 对象中最后一次出现的位置索引；如果父 List 中没有岀现这样的子 List，则返回 -1。
- boolean replaceAll(List list, Object oldVal, Object newVal)：使用一个新值 newVal 替换 List 对象的所有旧值 oldVal。

#### 复制

Collections 类的 copy() 静态方法用于将指定集合中的所有元素复制到另一个集合中。执行 copy() 方法后，目标集合中每个已复制元素的索引将等同于源集合中该元素的索引。

copy() 方法的语法格式如下：

```
void copy(List <? super T> dest,List<? extends T> src)
```

其中，dest 表示目标集合对象，src 表示源集合对象。

注意：目标集合的长度至少和源集合的长度相同，如果目标集合的长度更长，则不影响目标集合中的其余元素。如果目标集合长度不够而无法包含整个源集合元素，程序将抛出 IndexOutOfBoundsException 异常。

### Lambda表达式遍历Collection集合

[Java](http://c.biancheng.net/java/) 8 为 Iterable 接口新增了一个 forEach(Consumer action) 默认方法，该方法所需参数的类型是一个函数式接口，而 Iterable 接口是 Collection 接口的父接口，因此 Collection 集合也可直接调用该方法。

当程序调用 Iterable 的 forEach(Consumer action) 遍历集合元素时，程序会依次将集合元素传给 Consumer 的 accept(T t) 方法（该接口中唯一的抽象方法）。正因为 Consumer 是函数式接口，因此可以使用 Lambda 表达式来遍历集合元素。

如下程序示范了使用 Lambda 表达式来遍历集合元素。

```java
public class CollectionEach {
    public static void main(String[] args) {
        // 创建一个集合
        Collection objs = new HashSet();
        objs.add("C语言中文网Java教程");
        objs.add("C语言中文网C语言教程");
        objs.add("C语言中文网C++教程");
        // 调用forEach()方法遍历集合
        objs.forEach(obj -> System.out.println("迭代集合元素：" + obj));
    }
}
```

###  Iterator（迭代器）

Iterator（迭代器）是一个接口，它的作用就是遍历容器的所有元素，也是 [Java](http://c.biancheng.net/java/) 集合框架的成员，但它与 Collection 和 Map 系列的集合不一样，Collection 和 Map 系列集合主要用于盛装其他对象，而 Iterator 则主要用于遍历（即迭代访问）Collection 集合中的元素。

Iterator 接口隐藏了各种 Collection 实现类的底层细节，向应用程序提供了遍历 Collection 集合元素的统一编程接口。Iterator 接口里定义了如下 4 个方法。

- boolean hasNext()：如果被迭代的集合元素还没有被遍历完，则返回 true。
- Object next()：返回集合里的下一个元素。
- void remove()：删除集合里上一次 next 方法返回的元素。
- void forEachRemaining(Consumer action)：这是 Java 8 为 Iterator 新增的默认方法，该方法可使用 Lambda 表达式来遍历集合元素。

```java
import java.util.Collection;
import java.util.HashSet;
import java.util.Iterator;
public class IteratorTest {
    public static void main(String[] args) {
        // 创建一个集合
        Collection objs = new HashSet();
        objs.add("C语言中文网Java教程");
        objs.add("C语言中文网C语言教程");
        objs.add("C语言中文网C++教程");
        // 调用forEach()方法遍历集合
        // 获取books集合对应的迭代器
        Iterator it = objs.iterator();
        while (it.hasNext()) {
            // it.next()方法返回的数据类型是Object类型，因此需要强制类型转换
            String obj = (String) it.next();
            System.out.println(obj);
            if (obj.equals("C语言中文网C语言教程")) {
                // 从集合中删除上一次next()方法返回的元素
                it.remove();
            }
            // 对book变量赋值，不会改变集合元素本身
            obj = "C语言中文网Python语言教程";
        }
        System.out.println(objs);
    }
}

```

Iterator 迭代器采用的是快速失败（fail-fast）机制，一旦在迭代过程中检测到该集合已经被修改（通常是程序中的其他线程修改），程序立即引发 ConcurrentModificationException 异常，而不是显示修改后的结果，这样可以避免共享资源而引发的潜在问题。

### Lambda表达式遍历Iterator迭代器

[Java](http://c.biancheng.net/java/) 8 为 Iterator 引入了一个 forEachRemaining(Consumer action) 默认方法，该方法所需的 Consumer 参数同样也是函数式接口。当程序调用 Iterator 的 forEachRemaining(Consumer action) 遍历集合元素时，程序会依次将集合元素传给 Consumer 的 accept(T t) 方法（该接口中唯一的抽象方法）

```java
public class IteratorEach {
    public static void main(String[] args) {
        // 创建一个集合
        Collection objs = new HashSet();
        objs.add("C语言中文网Java教程");
        objs.add("C语言中文网C语言教程");
        objs.add("C语言中文网C++教程");
        // 获取objs集合对应的迭代器
        Iterator it = objs.iterator();
        // 使用Lambda表达式（目标类型是Comsumer）来遍历集合元素
        it.forEachRemaining(obj -> System.out.println("迭代集合元素：" + obj));
    }
}
```

### foreach循环遍历Collection集合

当使用 foreach 循环迭代访问集合元素时，该集合也不能被改变，否则将引发 ConcurrentModificationException 异常。

```java
public class ForeachTest {
    public static void main(String[] args) {
        // 创建一个集合
        Collection objs = new HashSet();
        objs.add("C语言中文网Java教程");
        objs.add("C语言中文网C语言教程");
        objs.add("C语言中文网C++教程");
        for (Object obj : objs) {
            // 此处的obj变量也不是集合元素本身
            String obj1 = (String) obj;
            System.out.println(obj1);
            if (obj1.equals("C语言中文网Java教程")) {
                // 下面代码会引发 ConcurrentModificationException 异常
                objs.remove(obj);
            }
        }
        System.out.println(objs);
    }
}
```

### Java 8新增的Predicate操作Collection集合

[Java](http://c.biancheng.net/java/) 8 起为 Collection 集合新增了一个 removeIf(Predicate filter) 方法，该方法将会批量删除符合 filter 条件的所有元素。该方法需要一个 Predicate 对象作为参数，Predicate 也是函数式接口，因此可使用 Lambda 表达式作为参数。

如下程序示范了使用 Predicate 来过滤集合。

```java
public class ForeachTest {
    public static void main(String[] args) {
        // 创建一个集合
        Collection objs = new HashSet();
        objs.add(new String("C语言中文网Java教程"));
        objs.add(new String("C语言中文网C++教程"));
        objs.add(new String("C语言中文网C语言教程"));
        objs.add(new String("C语言中文网Python教程"));
        objs.add(new String("C语言中文网Go教程"));
        // 使用Lambda表达式(目标类型是Predicate)过滤集合
        objs.removeIf(ele -> ((String) ele).length() < 12);
        System.out.println(objs);
    }
}
```

### Java 8新增的Stream操作Collection集合

[Java](http://c.biancheng.net/java/) 8 还新增了 Stream、IntStream、LongStream、DoubleStream 等流式 API，这些 API 代表多个支持串行和并行聚集操作的元素。上面 4 个接口中，Stream 是一个通用的流接口，而 IntStream、LongStream、 DoubleStream 则代表元素类型为 int、long、double 的流。

Java 8 还为上面每个流式 API 提供了对应的 Builder，例如 Stream.Builder、IntStream.Builder、LongStream.Builder、DoubleStream.Builder，开发者可以通过这些 Builder 来创建对应的流。

独立使用 Stream 的步骤如下：

1. 使用 Stream 或 XxxStream 的 builder() 类方法创建该 Stream 对应的 Builder。
2. 重复调用 Builder 的 add() 方法向该流中添加多个元素。
3. 调用 Builder 的 build() 方法获取对应的 Stream。
4. 调用 Stream 的聚集方法。


在上面 4 个步骤中，第 4 步可以根据具体需求来调用不同的方法，Stream 提供了大量的聚集方法供用户调用，具体可参考 Stream 或 XxxStream 的 API 文档。对于大部分聚集方法而言，每个 Stream 只能执行一次。例如如下程序。

```java
public class IntStreamTest {
    public static void main(String[] args) {
        IntStream is = IntStream.builder().add(20).add(13).add(-2).add(18).build();
        // 下面调用聚集方法的代码每次只能执行一行
        System.out.println("is 所有元素的最大值：" + is.max().getAsInt());
        System.out.println("is 所有元素的最小值：" + is.min().getAsInt());
        System.out.println("is 所有元素的总和：" + is.sum());
        System.out.println("is 所有元素的总数：" + is.count());
        System.out.println("is 所有元素的平均值：" + is.average());
        System.out.println("is所有元素的平方是否都大于20: " + is.allMatch(ele -> ele * ele > 20));
        System.out.println("is是否包含任何元素的平方大于20 : " + is.anyMatch(ele -> ele * ele > 20));
        // 将is映射成一个新Stream,新Stream的每个元素是原Stream元素的2倍+1
        IntStream newIs = is.map(ele -> ele * 2 + 1);
        // 使用方法引用的方式来遍历集合元素
        newIs.forEach(System.out::println); // 输岀 41 27 -3 37
    }
}
```

Stream 提供了大量的方法进行聚集操作，这些方法既可以是“中间的”（intermediate），也可以是 "末端的"（terminal）。

- 中间方法：中间操作允许流保持打开状态，并允许直接调用后续方法。上面程序中的 map() 方法就是中间方法。中间方法的返回值是另外一个流。
- 末端方法：末端方法是对流的最终操作。当对某个 Stream 执行末端方法后，该流将会被“消耗”且不再可用。上面程序中的 sum()、count()、average() 等方法都是末端方法。


除此之外，关于流的方法还有如下两个特征。

- 有状态的方法：这种方法会给流增加一些新的属性，比如元素的唯一性、元素的最大数量、保证元素以排序的方式被处理等。有状态的方法往往需要更大的性能开销。
- 短路方法：短路方法可以尽早结束对流的操作，不必检查所有的元素。


下面简单介绍一下 Stream 常用的中间方法。

| 方法                           | 说明                                                         |
| ------------------------------ | ------------------------------------------------------------ |
| filter(Predicate predicate)    | 过滤 Stream 中所有不符合 predicate 的元素                    |
| mapToXxx(ToXxxFunction mapper) | 使用 ToXxxFunction 对流中的元素执行一对一的转换，该方法返回的新流中包含了 ToXxxFunction 转换生成的所有元素。 |
| peek(Consumer action)          | 依次对每个元素执行一些操作，该方法返回的流与原有流包含相同的元素。该方法主要用于调试。 |
| distinct()                     | 该方法用于排序流中所有重复的元素（判断元素重复的标准是使用 equals() 比较返回 true）。这是一个有状态的方法。 |
| sorted()                       | 该方法用于保证流中的元素在后续的访问中处于有序状态。这是一个有状态的方法。 |
| limit(long maxSize)            | 该方法用于保证对该流的后续访问中最大允许访问的元素个数。这是一个有状态的、短路方法。 |

下面简单介绍一下 Stream 常用的末端方法。

| 方法                           | 说明                                                       |
| ------------------------------ | ---------------------------------------------------------- |
| forEach(Consumer action)       | 遍历流中所有元素，对每个元素执行action                     |
| toArray()                      | 将流中所有元素转换为一个数组                               |
| reduce()                       | 该方法有三个重载的版本，都用于通过某种操作来合并流中的元素 |
| min()                          | 返回流中所有元素的最小值                                   |
| max()                          | 返回流中所有元素的最大值                                   |
| count()                        | 返回流中所有元素的数量                                     |
| anyMatch(Predicate predicate)  | 判断流中是否至少包含一个元素符合 Predicate 条件。          |
| allMatch(Predicate predicate)  | 判断流中是否每个元素都符合 Predicate 条件                  |
| noneMatch(Predicate predicate) | 判断流中是否所有元素都不符合 Predicate 条件                |
| findFirst()                    | 返回流中的第一个元素                                       |
| findAny()                      | 返回流中的任意一个元素                                     |

### Java 9新增的不可变集合

[Java](http://c.biancheng.net/java/) 9 版本以前，假如要创建一个包含 6 个元素的 Set 集合，程序需要先创建 Set 集合，然后调用 6 次 add() 方法向 Set 集合中添加元素。Java 9 对此进行了简化，程序直接调用 Set、List、Map 的 of() 方法即可创建包含 N 个元素的不可变集合，这样一行代码就可创建包含 N 个元素的集合。

不可变意味着程序不能向集合中添加元素，也不能从集合中删除元素。

如下程序示范了如何创建不可变集合。

```java
public class Java9Collection {
    public static void main(String[] args) {
        // 创建包含4个元素的Set集合
        Set set = Set.of("Java", "Kotlin", "Go", "Swift");
        System.out.println(set);
        // 不可变集合，下面代码导致运行时错误
        // set.add("Ruby");
        // 创建包含4个元素的List集合
        List list = List.of(34, -25, 67, 231);
        System.out.println(list);
        // 不可变集合，下面代码导致运行时错误
        // list.remove(1);
        // 创建包含3个key-value对的Map集合
        Map map = Map.of("语文", 89, "数学", 82, "英语", 92);
        System.out.println(map);
        // 不可变集合，下面代码导致运行时错误
        // map.remove("语文");
        // 使用Map.entry()方法显式构建key-value对
        Map map2 = Map.ofEntries(Map.entry("语文", 89), Map.entry("数学", 82), Map.entry("英语", 92));
        System.out.println(map2);
    }
}
```

### 泛型

泛型可以在编译的时候检查类型安全，并且所有的强制转换都是自动和隐式的，提高了代码的重用率。

### 泛型集合

泛型本质上是提供类型的“类型参数”，也就是参数化类型。我们可以为类、接口或方法指定一个类型参数，通过这个参数限制操作的数据类型，从而保证类型转换的绝对安全。

```java
public class Book {
    private int Id; // 图书编号
    private String Name; // 图书名称
    private int Price; // 图书价格
    public Book(int id, String name, int price) { // 构造方法
        this.Id = id;
        this.Name = name;
        this.Price = price;
    }
    public String toString() { // 重写 toString()方法
        return this.Id + ", " + this.Name + "，" + this.Price;
    }
}
```

```java
public class Test14 {
    public static void main(String[] args) {
        // 创建3个Book对象
        Book book1 = new Book(1, "唐诗三百首", 8);
        Book book2 = new Book(2, "小星星", 12);
        Book book3 = new Book(3, "成语大全", 22);
        Map<Integer, Book> books = new HashMap<Integer, Book>(); // 定义泛型 Map 集合
        books.put(1001, book1); // 将第一个 Book 对象存储到 Map 中
        books.put(1002, book2); // 将第二个 Book 对象存储到 Map 中
        books.put(1003, book3); // 将第三个 Book 对象存储到 Map 中
        System.out.println("泛型Map存储的图书信息如下：");
        for (Integer id : books.keySet()) {
            // 遍历键
            System.out.print(id + "——");
            System.out.println(books.get(id)); // 不需要类型转换
        }
        List<Book> bookList = new ArrayList<Book>(); // 定义泛型的 List 集合
        bookList.add(book1);
        bookList.add(book2);
        bookList.add(book3);
        System.out.println("泛型List存储的图书信息如下：");
        for (int i = 0; i < bookList.size(); i++) {
            System.out.println(bookList.get(i)); // 这里不需要类型转换
        }
    }
}
```

### 泛型类

除了可以定义泛型集合之外，还可以直接限定泛型类的类型参数。语法格式如下：

```java
public class class_name<data_type1,data_type2,…>{}
```

其中，class_name 表示类的名称，data_ type1 等表示类型参数。Java 泛型支持声明一个以上的类型参数，只需要将类型用逗号隔开即可。

泛型类一般用于类中的属性类型不确定的情况下。在声明属性时，使用下面的语句：

```java
private data_type1 property_name1;
private data_type2 property_name2;
```

### 泛型方法

泛型方法使得该方法能够独立于类而产生变化。如果使用泛型方法可以取代类泛型化，那么就应该只使用泛型方法。另外，对一个 static 的方法而言，无法访问泛型类的类型参数。因此，如果 static 方法需要使用泛型能力，就必须使其成为泛型方法。

定义泛型方法的语法格式如下：

```java
[访问权限修饰符][static][final]<类型参数列表>返回值类型方法名([形式参数列表])
public static List<T> find(Class<T>class,int userId){}
```

一般来说编写 Java 泛型方法，其返回值类型至少有一个参数类型应该是泛型，而且类型应该是一致的，如果只有返回值类型或参数类型之一使用了泛型，那么这个泛型方法的使用就被限制了。

### 泛型的高级用法

泛型的用法非常灵活，除在集合、类和方法中使用外，本节将从三个方面介绍泛型的高级用法，包括限制泛型可用类型、使用类型通配符、继承泛型类和实现泛型接口。

1. 限制泛型可用类型

在 Java 中默认可以使用任何类型来实例化一个泛型类对象。当然也可以对泛型类实例的类型进行限制，语法格式如下：

```java
class 类名称<T extends anyClass>
```


其中，anyClass 指某个接口或类。使用泛型限制后，泛型类的类型必须实现或继承 anyClass 这个接口或类。无论 anyClass 是接口还是类，在进行泛型限制时都必须使用 extends 关键字。

2. 使用类型通配符

Java 中的泛型还支持使用类型通配符，它的作用是在创建一个泛型类对象时限制这个泛型类的类型必须实现或继承某个接口或类。

使用泛型类型通配符的语法格式如下：

```java
泛型类名称<? extends List>a = null;
```

其中，“<? extends List>”作为一个整体表示类型未知，当需要使用泛型对象时，可以单独实例化。

3. 继承泛型类和实现泛型接口

定义为泛型的类和接口也可以被继承和实现。例如下面的示例代码演示了如何继承泛型类。

```java
public class FatherClass<T1>{}
public class SonClass<T1,T2,T3> extents FatherClass<T1>{}
```

### 枚举

枚举是一个被命名的整型常数的集合，用于声明一组带标识符的常数。

声明枚举时必须使用 enum 关键字，然后定义枚举的名称、可访问性、基础类型和成员等。枚举声明的语法如下：

```java
enum-modifiers enum enumname:enum-base {
    enum-body,
}
```


其中，enum-modifiers 表示枚举的修饰符主要包括 public、private 和 internal；enumname 表示声明的枚举名称；enum-base 表示基础类型；enum-body 表示枚举的成员，它是枚举类型的命名常数。

任意两个枚举成员不能具有相同的名称，且它的常数值必须在该枚举的基础类型的范围之内，多个枚举成员之间使用逗号分隔。

Java 中的每一个枚举都继承自 java.lang.Enum 类。当定义一个枚举类型时，每一个枚举类型成员都可以看作是 Enum 类的实例，这些枚举成员默认都被 final、public, static 修饰，当使用枚举类型成员时，直接使用枚举名称调用成员即可。

所有枚举实例都可以调用 Enum 类的方法，常用方法如表 1 所示。

| 方法名称    | 描述                             |
| ----------- | -------------------------------- |
| values()    | 以数组形式返回枚举类型的所有成员 |
| valueOf()   | 将普通字符串转换为枚举实例       |
| compareTo() | 比较两个枚举成员在定义时的顺序   |
| ordinal()   | 获取枚举成员的索引位置           |

### EnumMap 与 EnumSet

为了更好地支持枚举类型，java.util 中添加了两个新类：`EnumMap` 和 `EnumSet`。使用它们可以更高效地操作枚举类型。

#### EnumMap 类

EnumMap 是专门为枚举类型量身定做的 Map 实现。虽然使用其他的 Map（如 [HashMap](http://c-local.biancheng.net/view/1067.html)）实现也能完成枚举类型实例到值的映射，但是使用 EnumMap 会更加高效。

HashMap 只能接收同一枚举类型的实例作为键值，并且由于枚举类型实例的数量相对固定并且有限，所以 EnumMap 使用数组来存放与枚举类型对应的值，使得 EnumMap 的效率非常高。

#### EnumSet 类

EnumSet 是枚举类型的高性能 Set 实现，它要求放入它的枚举常量必须属于同一枚举类型。EnumSet 提供了许多工厂方法以便于初始化，如表 2 所示。

| 方法名称                      | 描述                                                         |
| ----------------------------- | ------------------------------------------------------------ |
| allOf(Class<E> element type)  | 创建一个包含指定枚举类型中所有枚举成员的 EnumSet 对象        |
| complementOf(EnumSet<E> s)    | 创建一个与指定 EnumSet 对象 s 相同的枚举类型 EnumSet 对象， 并包含所有 s 中未包含的枚举成员 |
| copyOf(EnumSet<E> s)          | 创建一个与指定 EnumSet 对象 s 相同的枚举类型 EnumSet 对象， 并与 s 包含相同的枚举成员 |
| noneOf(<Class<E> elementType) | 创建指定枚举类型的空 EnumSet 对象                            |
| of(E first,e...rest)          | 创建包含指定枚举成员的 EnumSet 对象                          |
| range(E from ,E to)           | 创建一个 EnumSet 对象，该对象包含了 from 到 to 之间的所有枚 举成员 |

### HashMap

#### 简介

类定义

```java
public class HashMap<K,V>
         extends AbstractMap<K,V> 
         implements Map<K,V>, Cloneable, Serializable
```

主要简介

![img](https://upload-images.jianshu.io/upload_images/944365-086b58aeee2d00cb.png?imageMogr2/auto-orient/strip|imageView2/2/format/webp)

####  数据结构

红黑树

![img](https://upload-images.jianshu.io/upload_images/944365-dc8b2b6ae269ffee.png?imageMogr2/auto-orient/strip|imageView2/2/format/webp)

![img](https://upload-images.jianshu.io/upload_images/944365-98178707855677bc.jpg)

#### 存储流程

![img](https://upload-images.jianshu.io/upload_images/944365-441706c5a99e02a3.png)

#### 数组元素 & 链表节点的 实现类

```java
/** 
  * Node  = HashMap的内部类，实现了Map.Entry接口，本质是 = 一个映射(键值对)
  * 实现了getKey()、getValue()、equals(Object o)和hashCode()等方法
  **/  

  static class Node<K,V> implements Map.Entry<K,V> {

        final int hash; // 哈希值，HashMap根据该值确定记录的位置
        final K key; // key
        V value; // value
        Node<K,V> next;// 链表下一个节点

        // 构造方法
        Node(int hash, K key, V value, Node<K,V> next) {
            this.hash = hash;
            this.key = key;
            this.value = value;
            this.next = next;
        }
        
        public final K getKey()        { return key; }   // 返回 与 此项 对应的键
        public final V getValue()      { return value; } // 返回 与 此项 对应的值
        public final String toString() { return key + "=" + value; }

        public final V setValue(V newValue) {
            V oldValue = value;
            value = newValue;
            return oldValue;
        }

      /** 
        * hashCode（） 
        */
        public final int hashCode() {
            return Objects.hashCode(key) ^ Objects.hashCode(value);
        }

      /** 
        * equals（）
        * 作用：判断2个Entry是否相等，必须key和value都相等，才返回true  
        */
        public final boolean equals(Object o) {
            if (o == this)
                return true;
            if (o instanceof Map.Entry) {
                Map.Entry<?,?> e = (Map.Entry<?,?>)o;
                if (Objects.equals(key, e.getKey()) &&
                    Objects.equals(value, e.getValue()))
                    return true;
            }
            return false;
        }
    }
```

#### 红黑树节点 实现类

```java
/**
  * 红黑树节点 实现类：继承自LinkedHashMap.Entry<K,V>类
  */
  static final class TreeNode<K,V> extends LinkedHashMap.Entry<K,V> {  

    // 属性 = 父节点、左子树、右子树、删除辅助节点 + 颜色
    TreeNode<K,V> parent;  
    TreeNode<K,V> left;   
    TreeNode<K,V> right;
    TreeNode<K,V> prev;   
    boolean red;   

    // 构造函数
    TreeNode(int hash, K key, V val, Node<K,V> next) {  
        super(hash, key, val, next);  
    }  
  
    // 返回当前节点的根节点  
    final TreeNode<K,V> root() {  
        for (TreeNode<K,V> r = this, p;;) {  
            if ((p = r.parent) == null)  
                return r;  
            r = p;  
        }  
    } 
```

#### 主要使用API

```java
V get(Object key); // 获得指定键的值
V put(K key, V value);  // 添加键值对
void putAll(Map<? extends K, ? extends V> m);  // 将指定Map中的键值对 复制到 此Map中
V remove(Object key);  // 删除该键值对

boolean containsKey(Object key); // 判断是否存在该键的键值对；是 则返回true
boolean containsValue(Object value);  // 判断是否存在该值的键值对；是 则返回true
 
Set<K> keySet();  // 单独抽取key序列，将所有key生成一个Set
Collection<V> values();  // 单独value序列，将所有value生成一个Collection

void clear(); // 清除哈希表中的所有键值对
int size();  // 返回哈希表中所有 键值对的数量 = 数组中的键值对 + 链表中的键值对
boolean isEmpty(); // 判断HashMap是否为空；size == 0时 表示为 空 

```

#### HashMap中的重要参数

```java
 /** 
   * 主要参数 同  JDK 1.7 
   * 即：容量、加载因子、扩容阈值（要求、范围均相同）
   */

  // 1. 容量（capacity）： 必须是2的幂 & <最大容量（2的30次方）
  static final int DEFAULT_INITIAL_CAPACITY = 1 << 4; // 默认容量 = 16 = 1<<4 = 00001中的1向左移4位 = 10000 = 十进制的2^4=16
  static final int MAXIMUM_CAPACITY = 1 << 30; // 最大容量 =  2的30次方（若传入的容量过大，将被最大值替换）

  // 2. 加载因子(Load factor)：HashMap在其容量自动增加前可达到多满的一种尺度 
  final float loadFactor; // 实际加载因子
  static final float DEFAULT_LOAD_FACTOR = 0.75f; // 默认加载因子 = 0.75

  // 3. 扩容阈值（threshold）：当哈希表的大小 ≥ 扩容阈值时，就会扩容哈希表（即扩充HashMap的容量） 
  // a. 扩容 = 对哈希表进行resize操作（即重建内部数据结构），从而哈希表将具有大约两倍的桶数
  // b. 扩容阈值 = 容量 x 加载因子
  int threshold;

  // 4. 其他
  transient Node<K,V>[] table;  // 存储数据的Node类型 数组，长度 = 2的幂；数组的每个元素 = 1个单链表
  transient int size;// HashMap的大小，即 HashMap中存储的键值对的数量
 

  /** 
   * 与红黑树相关的参数
   */
   // 1. 桶的树化阈值：即 链表转成红黑树的阈值，在存储数据时，当链表长度 > 该值时，则将链表转换成红黑树
   static final int TREEIFY_THRESHOLD = 8; 
   // 2. 桶的链表还原阈值：即 红黑树转为链表的阈值，当在扩容（resize（））时（此时HashMap的数据存储位置会重新计算），在重新计算存储位置后，当原有的红黑树内数量 < 6时，则将 红黑树转换成链表
   static final int UNTREEIFY_THRESHOLD = 6;
   // 3. 最小树形化容量阈值：即 当哈希表中的容量 > 该值时，才允许树形化链表 （即 将链表 转换成红黑树）
   // 否则，若桶内元素太多时，则直接扩容，而不是树形化
   // 为了避免进行扩容、树形化选择的冲突，这个值不能小于 4 * TREEIFY_THRESHOLD
   static final int MIN_TREEIFY_CAPACITY = 64;
```

![img](https://upload-images.jianshu.io/upload_images/944365-b85819e2f8a3c30a.jpg)

![img](https://upload-images.jianshu.io/upload_images/944365-67768bc4f0d23d69.png)



<img src="https://upload-images.jianshu.io/upload_images/944365-04658793bae1ed90.png" alt="img" style="zoom: 150%;" />

#### HashMap添加数据

![img](https://upload-images.jianshu.io/upload_images/944365-45ec8c640c5e5363.png)

添加数据的流程如下

![img](https://upload-images.jianshu.io/upload_images/944365-2914343d292dc7e0.png?imageMogr2/auto-orient/strip|imageView2/2/w/939/format/webp)

#### 为什么不直接采用经过hashCode（）处理的哈希码作为存储数组table的下标位置？

- 结论：容易出现哈希码与数组大小范围不匹配的情况，即 计算出来的哈希码可能 不在数组大小范围内，从而导致无法匹配存储位置
- 原因描述

![img](https://upload-images.jianshu.io/upload_images/944365-d18ee0697a1a1b53.png?imageMogr2/auto-orient/strip|imageView2/2/w/1200/format/webp)

为了解决 “哈希码与数组大小范围不匹配” 的问题，`HashMap`给出了解决方案：**哈希码 与运算（&） （数组长度-1）**

#### 为什么采用 哈希码 与运算(&) （数组长度-1） 计算数组下标？

- 结论：根据HashMap的容量大小（数组长度），按需取 哈希码一定数量的低位 作为存储的数组下标位置，从而 解决 “哈希码与数组大小范围不匹配” 的问题

- 具体解决方案描述

  ![img](https://upload-images.jianshu.io/upload_images/944365-04658793bae1ed90.png?imageMogr2/auto-orient/strip|imageView2/2/w/1200/format/webp)

#### 为什么在计算数组下标前，需对哈希码进行二次处理：扰动处理？

- 结论：加大哈希码低位的随机性，使得分布更均匀，从而提高对应数组存储下标位置的随机性 & 均匀性，最终减少Hash冲突
- 具体描述
- ![img](https://upload-images.jianshu.io/upload_images/944365-20d396364b968713.png?imageMogr2/auto-orient/strip|imageView2/2/w/1200/format/webp)

#### 存放数据到哈希表中

![img](https://upload-images.jianshu.io/upload_images/944365-4f700e47dda01f7f.png?imageMogr2/auto-orient/strip|imageView2/2/w/1060/format/webp)

#### 扩容机制

![img](https://upload-images.jianshu.io/upload_images/944365-9fb3fec07a0764ec.png?imageMogr2/auto-orient/strip|imageView2/2/w/518/format/webp)



![img](https://upload-images.jianshu.io/upload_images/944365-a31e51b24f135d7c.png?imageMogr2/auto-orient/strip|imageView2/2/w/1120/format/webp)

#### `JDK 1.8`扩容时，数据存储位置重新计算的方式

![img](https://upload-images.jianshu.io/upload_images/944365-a467fdaa3a110350.png?imageMogr2/auto-orient/strip|imageView2/2/w/1200/format/webp)

![img](https://upload-images.jianshu.io/upload_images/944365-2466f5db47fd7685.png?imageMogr2/auto-orient/strip|imageView2/2/w/890/format/webp)

![img](https://upload-images.jianshu.io/upload_images/944365-d78ae9079c14f222.png?imageMogr2/auto-orient/strip|imageView2/2/w/1200/format/webp)

![img](https://upload-images.jianshu.io/upload_images/944365-e706a4817a35b021.png?imageMogr2/auto-orient/strip|imageView2/2/w/1200/format/webp)

#### 源码总结

![img](https://upload-images.jianshu.io/upload_images/944365-38b76323d9e4ceb7.png?imageMogr2/auto-orient/strip|imageView2/2/w/1200/format/webp)

![img](https://upload-images.jianshu.io/upload_images/944365-3edece748ed29c71.png?imageMogr2/auto-orient/strip|imageView2/2/w/1200/format/webp)

![img](https://upload-images.jianshu.io/upload_images/944365-5ff3ae3f2149184e.png?imageMogr2/auto-orient/strip|imageView2/2/w/920/format/webp)

#### 哈希表如何解决Hash冲突

![img](https://upload-images.jianshu.io/upload_images/944365-7621b15f58e87a66.png?imageMogr2/auto-orient/strip|imageView2/2/w/1200/format/webp)

#### 为什么HashMap具备下述特点

![img](https://upload-images.jianshu.io/upload_images/944365-ce5aa2227f269410.jpg?imageMogr2/auto-orient/strip|imageView2/2/w/1162/format/webp)

#### 为什么 HashMap 中 String、Integer 这样的包装类适合作为 key 键

![img](https://upload-images.jianshu.io/upload_images/944365-318b6e178419065b.png?imageMogr2/auto-orient/strip|imageView2/2/w/1200/format/webp)

#### HashMap 中的 `key`若 `Object`类型， 则需实现哪些方法？

![img](https://upload-images.jianshu.io/upload_images/944365-23536584ac590783.png?imageMogr2/auto-orient/strip|imageView2/2/w/1200/format/webp)

#### **size为什么必须是2的幂?**

HashMap为了实现存取高效，要尽量减少碰撞，就是要尽量做到：把数据分配均匀，保证每个链表长度大致相同，我们就需要一个算法来实现：将存入的数据保存到那个链表中的算法；而这个算法实际就是取模：`hash%length`

但是，大家都道这种运算不如位移运算快。因此，源码中做了优化 `hash&(length-1)`。

也就是说 `hash%length==hash&(length-1)`

因为2的n次方实际就是1后面n个0，而2的n次方-1，实际就是n个1。

例如长度为8时候，3&(8-1)=3 2&(8-1)=2 ，不同位置上，不碰撞。而长度为5的时候，3&(5-1)=0 2&(5-1)=0，都在0上，出现碰撞了。所以，保证容积是2的n次方，是为了保证在做(length-1)的时候，每一位都能 &1 ，也就是和1111……1111111进行与运算。`即：两位同时为“1”，结果才为“1”，否则为0`.

我们一开始提到过，添加元素时索引的下标可以通过取模运算获得，但是我们知道计算机的运行效率：加法(减法)>乘法>除法>取模，取模的效率是最低的。所以我们要在HashMap中避免频繁的取模运算，又因为在我们HashMap中他要通过取模去定位我们的索引，并且HashMap是在不停的扩容，数组一旦达到容量的阈值的时候就需要对数组进行扩容。那么扩容就意味着要进行数组的移动，数组一旦移动，每移动一次就要重回记算索引，这个过程中牵扯大量元素的迁移，就会大大影响效率。那么如果说我们直接使用与运算，这个效率是远远高于取模运算的！

#### 负载因子0.75?

```java
 /**
     * The load factor used when none specified in constructor.
     */
    static final float DEFAULT_LOAD_FACTOR = 0.75f;
```

我们知道，最理想的情况就是，当我们put进来的元素刚好平铺在数组上，而不产生链表，尽量不产生Hash碰撞。但是我们明白这种情况只是理想化的，是难以实现的。

那么我们反证一下：当我们将负载因子不定为0.75的时候（两种情况）：

1、 假如负载因子定为1（最大值），那么只有当元素填慢数组长度的时候才会选择去扩容，虽然负载因子定为1可以最大程度的提高空间的利用率，但是我们的查询效率会变得低下（因为链表查询比较慢）

**结论：所以当加载因子比较大的时候：节省空间资源，耗费时间资源**

2、加入负载因子定为0.5（一个比较小的值），也就是说，知道到达数组空间的一半的时候就会去扩容。虽然说负载因子比较小可以最大可能的降低hash冲突，链表的长度也会越少，但是空间浪费会比较大

**结论：所以当加载因子比较小的时候：节省时间资源，耗费空间资源**

#### Hashmap的结构，1.7和1.8有哪些区别

不同点：
（1）JDK1.7用的是头插法，而JDK1.8及之后使用的都是尾插法，那么他们为什么要这样做呢？因为JDK1.7是用单链表进行的纵向延伸，当采用头插法时会容易出现逆序且环形链表死循环问题。但是在JDK1.8之后是因为加入了红黑树使用尾插法，能够避免出现逆序且链表死循环的问题。

（2）扩容后数据存储位置的计算方式也不一样：1. 在JDK1.7的时候是直接用hash值和需要扩容的二进制数进行&（这里就是为什么扩容的时候为啥一定必须是2的多少次幂的原因所在，因为如果只有2的n次幂的情况时最后一位二进制数才一定是1，这样能最大程度减少hash碰撞）（hash值 & length-1）

   而在JDK1.8的时候直接用了JDK1.7的时候计算的规律，也就是扩容前的原始位置+扩容的大小值=JDK1.8的计算方式，而不再是JDK1.7的那种异或的方法。但是这种方式就相当于只需要判断Hash值的新增参与运算的位是0还是1就直接迅速计算出了扩容后的储存方式。

![这里写图片描述](https://img-blog.csdn.net/20180905103627222?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2NTIwMjM1/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

在计算hash值的时候，JDK1.7用了9次扰动处理=4次位运算+5次异或，而JDK1.8只用了2次扰动处理=1次位运算+1次异或。

**扩容流程对比图：**

![这里写图片描述](https://img-blog.csdn.net/20180905105129591?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2NTIwMjM1/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

**（3）JDK1.7的时候使用的是数组+ 单链表的数据结构。但是在JDK1.8及之后时，使用的是数组+链表+红黑树的数据结构（当链表的深度达到8的时候，也就是默认阈值，就会自动扩容把链表转成红黑树的数据结构来把时间复杂度从O（n）变成O（logN）提高了效率）**

![这里写图片描述](https://img-blog.csdn.net/20180905104854587?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2NTIwMjM1/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)



#### **为什么在JDK1.7的时候是先进行扩容后进行插入，而在JDK1.8的时候则是先插入后进行扩容的呢？**

```java
//其实就是当这个Map中实际插入的键值对的值的大小如果大于这个默认的阈值的时候（初始是16*0.75=12）的时候才会触发扩容，
//这个是在JDK1.8中的先插入后扩容
if (++size > threshold)
            resize();
```

  其实这个问题也是JDK8对HashMap中，主要是因为对链表转为红黑树进行的优化，因为你插入这个节点的时候有可能是普通链表节点，也有可能是红黑树节点，但是为什么1.8之后HashMap变为先插入后扩容的原因，我也有点不是很理解？欢迎来讨论这个问题？

  但是在JDK1.7中的话，是先进行扩容后进行插入的，就是当你发现你插入的桶是不是为空，如果不为空说明存在值就发生了hash冲突，那么就必须得扩容，但是如果不发生Hash冲突的话，说明当前桶是空的（后面并没有挂有链表），那就等到下一次发生Hash冲突的时候在进行扩容，但是当如果以后都没有发生hash冲突产生，那么就不会进行扩容了，减少了一次无用扩容，也减少了内存的使用

```java
void addEntry(int hash, K key, V value, int bucketIndex) {
		//这里当钱数组如果大于等于12（假如）阈值的话，并且当前的数组的Entry数组还不能为空的时候就扩容
    　　if ((size >= threshold) && (null != table[bucketIndex])) {
　　　　　　 //扩容数组，比较耗时
       　　 resize(2 * table.length);
        　　hash = (null != key) ? hash(key) : 0;
        　　bucketIndex = indexFor(hash, table.length);
    　　}

    　　createEntry(hash, key, value, bucketIndex);
　　}

 void createEntry(int hash, K key, V value, int bucketIndex) {
    　　Entry<K,V> e = table[bucketIndex];
　　　　//把新加的放在原先在的前面，原先的是e，现在的是new，next指向e
   　　 table[bucketIndex] = new Entry<>(hash, key, value, e);//假设现在是new
    　　size++;
　　}
```

#### **为什么在JDK1.8中进行对HashMap优化的时候，把链表转化为红黑树的阈值是8,而不是7或者不是20呢？**

  如果选择6和8（如果链表小于等于6树还原转为链表，大于等于8转为树），中间有个差值7可以有效防止链表和树频繁转换。假设一下，如果设计成链表个数超过8则链表转换成树结构，链表个数小于8则树结构转换成链表，如果一个HashMap不停的插入、删除元素，链表个数在8左右徘徊，就会频繁的发生树转链表、链表转树，效率会很低。
  还有一点重要的就是由于treenodes的大小大约是常规节点的两倍，因此我们仅在容器包含足够的节点以保证使用时才使用它们，当它们变得太小（由于移除或调整大小）时，它们会被转换回普通的node节点，容器中节点分布在hash桶中的频率遵循泊松分布，桶的长度超过8的概率非常非常小。所以作者应该是根据概率统计而选择了8作为阀值

```java
//Java中解释的原因
   * Because TreeNodes are about twice the size of regular nodes, we
     * use them only when bins contain enough nodes to warrant use
     * (see TREEIFY_THRESHOLD). And when they become too small (due to
     * removal or resizing) they are converted back to plain bins.  In
     * usages with well-distributed user hashCodes, tree bins are
     * rarely used.  Ideally, under random hashCodes, the frequency of
     * nodes in bins follows a Poisson distribution
     * (http://en.wikipedia.org/wiki/Poisson_distribution) with a
     * parameter of about 0.5 on average for the default resizing
     * threshold of 0.75, although with a large variance because of
     * resizing granularity. Ignoring variance, the expected
     * occurrences of list size k are (exp(-0.5) * pow(0.5, k) /
     * factorial(k)). The first values are:
     *
     * 0:    0.60653066
     * 1:    0.30326533
     * 2:    0.07581633
     * 3:    0.01263606
     * 4:    0.00157952
     * 5:    0.00015795
     * 6:    0.00001316
     * 7:    0.00000094
     * 8:    0.00000006
     * more: less than 1 in ten million
```

在理想情况下,使用随机哈希码,节点出现的频率在hash桶中遵循泊松分布，同时给出了桶中元素个数和概率的对照表。从上面的表中可以看到当桶中元素到达8个的时候，概率已经变得非常小，也就是说用0.75作为加载因子，每个碰撞位置的链表长度超过８个的概率达到了一百万分之一。

在JDK1.8之后，HashMap底层的数组扩容后迁移的方法进行了优化。把一个链表分成了两组，分成高为和低位分别去迁移，避免了死环问题。而且在迁移的过程中并没有进行任何的rehash（重新记算hash），提高了性能。它是直接将链表给断掉，进行几乎是一个均等的拆分，然后通过头指针的指向将整体给迁移过去，这样就减小了链表的长度。

#### 

## 反射

### 反射机制

[Java](http://c.biancheng.net/java/) 反射机制是 Java 语言的一个重要特性。在学习 Java 反射机制前，大家应该先了解两个概念，编译期和运行期。

编译期是指把源码交给编译器编译成计算机可以执行的文件的过程。在 Java 中也就是把 Java 代码编成 class 文件的过程。编译期只是做了一些翻译功能，并没有把代码放在内存中运行起来，而只是把代码当成文本进行操作，比如检查错误。

运行期是把编译后的文件交给计算机执行，直到程序运行结束。所谓运行期就把在磁盘中的代码放到内存中执行起来。

Java 反射机制是在运行状态中，对于任意一个类，都能够知道这个类的所有属性和方法；对于任意一个对象，都能够调用它的任意方法和属性；这种动态获取信息以及动态调用对象方法的功能称为 Java 语言的反射机制。简单来说，反射机制指的是程序在运行时能够获取自身的信息。在 Java 中，只要给定类的名字，就可以通过反射机制来获得类的所有信息。

Java 反射机制在服务器程序和中间件程序中得到了广泛运用。在服务器端，往往需要根据客户的请求，动态调用某一个对象的特定方法。此外，在 ORM 中间件的实现中，运用 Java 反射机制可以读取任意一个 JavaBean 的所有属性，或者给这些属性赋值。

![img](http://c.biancheng.net/uploads/allimg/191213/5-19121314235A02.png)

Java 反射机制主要提供了以下功能，这些功能都位于`java.lang.reflect`包。

- 在运行时判断任意一个对象所属的类。
- 在运行时构造任意一个类的对象。
- 在运行时判断任意一个类所具有的成员变量和方法。
- 在运行时调用任意一个对象的方法。
- 生成动态代理。


要想知道一个类的属性和方法，必须先获取到该类的字节码文件对象。获取类的信息时，使用的就是 Class 类中的方法。所以先要获取到每一个字节码文件（.class）对应的 Class 类型的对象.

众所周知，所有 Java 类均继承了 Object 类，在 Object 类中定义了一个 getClass() 方法，该方法返回同一个类型为 Class 的对象。例如，下面的示例代码：

```java
Class labelCls = label1.getClass();    // label1为 JLabel 类的对象
```

利用 Class 类的对象 labelCls 可以访问 labelCls 对象的描述信息、JLabel 类的信息以及基类 Object 的信息。表 1 列出了通过反射可以访问的信息。



| 类型                      | 访问方法            | 返回值类型                 | 说明                                             |
| ------------------------- | ------------------- | -------------------------- | ------------------------------------------------ |
| 包路径                    | getPackage()        | Package 对象               | 获取该类的存放路径                               |
| 类名称                    | getName()           | String 对象                | 获取该类的名称                                   |
| 继承类                    | getSuperclass()     | Class 对象                 | 获取该类继承的类                                 |
| 实现接口                  | getlnterfaces()     | Class 型数组               | 获取该类实现的所有接口                           |
| 构造方法                  | getConstructors()   | Constructor 型数组         | 获取所有权限为 public 的构造方法                 |
| getDeclaredContruectors() | Constructor 对象    | 获取当前对象的所有构造方法 |                                                  |
| 方法                      | getMethods()        | Methods 型数组             | 获取所有权限为 public 的方法                     |
| getDeclaredMethods()      | Methods 对象        | 获取当前对象的所有方法     |                                                  |
| 成员变量                  | getFields()         | Field 型数组               | 获取所有权限为 public 的成员变量                 |
| getDeclareFileds()        | Field 对象          | 获取当前对象的所有成员变量 |                                                  |
| 内部类                    | getClasses()        | Class 型数组               | 获取所有权限为 public 的内部类                   |
| getDeclaredClasses()      | Class 型数组        | 获取所有内部类             |                                                  |
| 内部类的声明类            | getDeclaringClass() | Class 对象                 | 如果该类为内部类，则返回它的成员类，否则返回 nul |

### 反射机制的优缺点

优点：

- 能够运行时动态获取类的实例，大大提高系统的灵活性和扩展性。
- 与 Java 动态编译相结合，可以实现无比强大的功能。
- 对于 Java 这种先编译再运行的语言，能够让我们很方便的创建灵活的代码，这些代码可以在运行时装配，无需在组件之间进行源代码的链接，更加容易实现面向对象。


缺点：

- 反射会消耗一定的系统资源，因此，如果不需要动态地创建一个对象，那么就不需要用反射；
- 反射调用方法时可以忽略权限检查，获取这个类的私有方法和属性，因此可能会破坏类的封装性而导致安全问题。

### Class获取方式

- 利用对象调用getClass()方法获取该对象的Class实例；
- 使用Class类的静态方法forName()，用类的名字获取一个Class实例 ；
- 运用.class的方式来获取Class实例，对于基本数据类型的封装类，还可以采用.TYPE来获取相对应的基本数据类型的Class实例；

### 反射访问构造方法

为了能够**动态获取**对象构造方法的信息，首先需要通过下列方法之一创建一个 `Constructor` 类型的对象或者数组。

- getConstructors()
- getConstructor(Class<?>…parameterTypes)
- getDeclaredConstructors()
- getDeclaredConstructor(Class<?>...parameterTypes)


如果是访问指定的构造方法，需要根据该构造方法的入口参数的类型来访问。例如，访问一个入口参数类型依次为 int 和 String 类型的构造方法，下面的两种方式均可以实现。

```java
objectClass.getDeclaredConstructor(int.class,String.class);
objectClass.getDeclaredConstructor(new Class[]{int.class,String.class});
```


创建的每个 Constructor 对象表示一个构造方法，然后利用 Constructor 对象的方法操作构造方法。Constructor 类的常用方法如表 1 所示。

| 方法名称                       | 说明                                                         |
| ------------------------------ | ------------------------------------------------------------ |
| isVarArgs()                    | 查看该构造方法是否允许带可变数量的参数，如果允许，返回 true，否则返回 false |
| getParameterTypes()            | 按照声明顺序以 Class 数组的形式获取该构造方法各个参数的类型  |
| getExceptionTypes()            | 以 Class 数组的形式获取该构造方法可能抛出的异常类型          |
| newInstance(Object … initargs) | 通过该构造方法利用指定参数创建一个该类型的对象，如果未设置参数则表示 采用默认无参的构造方法 |
| setAccessiable(boolean flag)   | 如果该构造方法的权限为 private，默认为不允许通过反射利用 netlnstance() 方法创建对象。如果先执行该方法，并将入口参数设置为 true，则允许创建对 象 |
| getModifiers()                 | 获得可以解析出该构造方法所采用修饰符的整数                   |

通过 java.lang.reflect.Modifier 类可以解析出 getMocMers() 方法的返回值所表示的修饰符信息。在该类中提供了一系列用来解析的静态方法，既可以查看是否被指定的修饰符修饰，还可以字符串的形式获得所有修饰符。表 2 列出了 Modifier 类的常用静态方法。

| 静态方法名称         | 说明                                                     |
| -------------------- | -------------------------------------------------------- |
| isStatic(int mod)    | 如果使用 static 修饰符修饰则返回 true，否则返回 false    |
| isPublic(int mod)    | 如果使用 public 修饰符修饰则返回 true，否则返回 false    |
| isProtected(int mod) | 如果使用 protected 修饰符修饰则返回 true，否则返回 false |
| isPrivate(int mod)   | 如果使用 private 修饰符修饰则返回 true，否则返回 false   |
| isFinal(int mod)     | 如果使用 final 修饰符修饰则返回 true，否则返回 false     |
| toString(int mod)    | 以字符串形式返回所有修饰符                               |

### 反射执行方法

要**动态获取**一个对象方法的信息，首先需要通过下列方法之一创建一个 `Method` 类型的对象或者数组。

- getMethods()
- getMethods(String name,Class<?> …parameterTypes)
- getDeclaredMethods()
- getDeclaredMethods(String name,Class<?>...parameterTypes)


如果是访问指定的构造方法，需要根据该方法的入口参数的类型来访问。例如，访问一个名称为 max，入口参数类型依次为 int 和 String 类型的方法。

下面的两种方式均可以实现：

```java
objectClass.getDeclaredConstructor("max",int.class,String.class);
objectClass.getDeclaredConstructor("max",new Class[]{int.class,String.class});
```

| 静态方法名称                     | 说明                                                         |
| -------------------------------- | ------------------------------------------------------------ |
| getName()                        | 获取该方法的名称                                             |
| getParameterType()               | 按照声明顺序以 Class 数组的形式返回该方法各个参数的类型      |
| getReturnType()                  | 以 Class 对象的形式获得该方法的返回值类型                    |
| getExceptionTypes()              | 以 Class 数组的形式获得该方法可能抛出的异常类型              |
| invoke(Object obj,Object...args) | 利用 args 参数执行指定对象 obj 中的该方法，返回值为 Object 类型 |
| isVarArgs()                      | 查看该方法是否允许带有可变数量的参数，如果允许返回 true，否则返回 false |
| getModifiers()                   | 获得可以解析出该方法所采用修饰符的整数                       |

### 反射访问成员变量

通过下列任意一个方法访问成员变量时将返回 Field 类型的对象或数组。

- getFields()
- getField(String name)
- getDeclaredFields()
- getDeclaredField(String name)


上述方法返回的 Field 对象代表一个成员变量。例如，要访问一个名称为 price 的成员变量，示例代码如下：

```
object.getDeciaredField("price");
```


Field 类的常用方法如表 1 所示

| 方法名称                          | 说明                                                         |
| --------------------------------- | ------------------------------------------------------------ |
| getName()                         | 获得该成员变量的名称                                         |
| getType()                         | 获取表示该成员变量的 Class 对象                              |
| get(Object obj)                   | 获得指定对象 obj 中成员变量的值，返回值为 Object 类型        |
| set(Object obj, Object value)     | 将指定对象 obj 中成员变量的值设置为 value                    |
| getlnt(0bject obj)                | 获得指定对象 obj 中成员类型为 int 的成员变量的值             |
| setlnt(0bject obj, int i)         | 将指定对象 obj 中成员变量的值设置为 i                        |
| setFloat(Object obj, float f)     | 将指定对象 obj 中成员变量的值设置为 f                        |
| getBoolean(Object obj)            | 获得指定对象 obj 中成员类型为 boolean 的成员变量的值         |
| setBoolean(Object obj, boolean b) | 将指定对象 obj 中成员变量的值设置为 b                        |
| getFloat(Object obj)              | 获得指定对象 obj 中成员类型为 float 的成员变量的值           |
| setAccessible(boolean flag)       | 此方法可以设置是否忽略权限直接访问 private 等私有权限的成员变量 |
| getModifiers()                    | 获得可以解析出该方法所采用修饰符的整数                       |

## 输入/输出（I/O）流

### 输入/输出流

输入就是将数据从各种输入设备（包括文件、键盘等）中读取到内存中，输出则正好相反，是将数据写入到各种输出设备（比如文件、显示器、磁盘等）。例如键盘就是一个标准的输入设备，而显示器就是一个标准的输出设备，但是文件既可以作为输入设备，又可以作为输出设备。

数据流是 Java 进行 I/O 操作的对象，它按照不同的标准可以分为不同的类别。

- 按照流的方向主要分为输入流和输出流两大类。
- 数据流按照数据单位的不同分为字节流和字符流。
- 按照功能可以划分为节点流和处理流。

![输入流模式](http://c.biancheng.net/uploads/allimg/200115/5-200115142HWK.png)

![输出流模式](http://c.biancheng.net/uploads/allimg/200115/5-200115142K1644.png)

### 输入流

Java 流相关的类都封装在 java.io 包中，而且每个数据流都是一个对象。所有输入流类都是 InputStream 抽象类（字节输入流）和 Reader 抽象类（字符输入流）的子类。其中 InputStream 类是字节输入流的抽象类，是所有字节输入流的父类，其层次结构如图 3 所示。

![InputStream类的层次结构图](http://c.biancheng.net/uploads/allimg/200115/5-200115145253550.png)

InputStream 类中所有方法遇到错误时都会引发 IOException 异常。如下是该类中包含的常用方法。

| 名称                               | 作用                                                         |
| ---------------------------------- | ------------------------------------------------------------ |
| int read()                         | 从输入流读入一个 8 字节的数据，将它转换成一个 0~ 255 的整数，返回一个整数，如果遇到输入流的结尾返回 -1 |
| int read(byte[] b)                 | 从输入流读取若干字节的数据保存到参数 b 指定的字节数组中，返回的字节数表示读取的字节数，如果遇到输入流的结尾返回 -1 |
| int read(byte[] b,int off,int len) | 从输入流读取若干字节的数据保存到参数 b 指定的字节数组中，其中 off 是指在数组中开始保存数据位置的起始下标，len 是指读取字节的位数。返回的是实际读取的字节数，如果遇到输入流的结尾则返回 -1 |
| void close()                       | 关闭数据流，当完成对数据流的操作之后需要关闭数据流           |
| int available()                    | 返回可以从数据源读取的数据流的位数。                         |
| skip(long n)                       | 从输入流跳过参数 n 指定的字节数目                            |
| boolean markSupported()            | 判断输入流是否可以重复读取，如果可以就返回 true              |
| void mark(int readLimit)           | 如果输入流可以被重复读取，从流的当前位置开始设置标记，readLimit 指定可以设置标记的字节数 |
| void reset()                       | 使输入流重新定位到刚才被标记的位置，这样可以重新读取标记过的数据 |

### 输出流

在 Java 中所有输出流类都是 OutputStream 抽象类（字节输出流）和 Writer 抽象类（字符输出流）的子类。其中 OutputStream 类是字节输出流的抽象类，是所有字节输出流的父类，其层次结构如图 4 所示。

![OutputStream类的层次结构图](http://c.biancheng.net/uploads/allimg/200115/5-200115151G3J0.png)

OutputStream 类是所有字节输出流的超类，用于以二进制的形式将数据写入目标设备，该类是抽象类，不能被实例化。OutputStream 类提供了一系列跟数据输出有关的方法，如下所示。

| 名称                                 | 作用                                                     |
| ------------------------------------ | -------------------------------------------------------- |
| int write(b)                         | 将指定字节的数据写入到输出流                             |
| int write (byte[] b)                 | 将指定字节数组的内容写入输出流                           |
| int write (byte[] b,int off,int len) | 将指定字节数组从 off 位置开始的 len 字节的内容写入输出流 |
| close()                              | 关闭数据流，当完成对数据流的操作之后需要关闭数据流       |
| flush()                              | 刷新输出流，强行将缓冲区的内容写入输出流                 |

### 系统流

每个 [Java](http://c.biancheng.net/java/) 程序运行时都带有一个系统流，系统流对应的类为 java.lang.System。Sytem 类封装了 Java 程序运行时的 3 个系统流，分别通过 in、out 和 err 变量来引用。这 3 个系统流如下所示：

- System.in：标准输入流，默认设备是键盘。
- System.out：标准输出流，默认设备是控制台。
- System.err：标准错误流，默认设备是控制台。

System.in 是 InputStream 类的一个对象，因此上述代码的 System.in.read() 方法实际是访问 InputStream 类定义的 read() 方法。该方法可以从键盘读取一个或多个字符。对于 System.out 输出流主要用于将指定内容输出到控制台。

System.out 和 System.error 是 PrintStream 类的对象。因为 PrintStream 是一个从 OutputStream 派生的输出流，所以它还执行低级别的 write() 方法。因此，除了 print() 和 println() 方法可以完成控制台输出以外，System.out 还可以调用 write() 方法实现控制台输出。

### 字符编码介绍

算机中，任何的文字都是以指定的编码方式存在的，在 [Java](http://c.biancheng.net/java/) 程序的开发中最常见的是 ISO8859-1、GBK/GB2312、Unicode、 UTF 编码。

Java 中常见编码说明如下：

- ISO8859-1：属于单字节编码，最多只能表示 0~255 的字符范围。
- GBK/GB2312：中文的国标编码，用来表示汉字，属于双字节编码。GBK 可以表示简体中文和繁体中文，而 GB2312 只能表示简体中文。GBK 兼容 GB2312。
- Unicode：是一种编码规范，是为解决全球字符通用编码而设计的。UTF-8 和 UTF-16 是这种规范的一种实现，此编码不兼容 ISO8859-1 编码。Java 内部采用此编码。
- UTF：UTF 编码兼容了 ISO8859-1 编码，同时也可以用来表示所有的语言字符，不过 UTF 编码是不定长编码，每一个字符的长度为 1~6 个字节不等。一般在中文网页中使用此编码，可以节省空间。


在程序中如果处理不好字符编码，就有可能出现乱码问题。例如现在本机的默认编码是 GBK，但在程序中使用了 ISO8859-1 编码，则就会出现字符的乱码问题。就像两个人交谈，一个人说中文，另外一个人说英语，语言不同就无法沟通。为了避免产生乱码，程序编码应与本地的默认编码保持一致。

### File类

在 [Java](http://c.biancheng.net/java/) 中，File 类是 java.io 包中唯一代表磁盘文件本身的对象，也就是说，如果希望在程序中操作文件和目录，则都可以通过 File 类来完成。File 类定义了一些方法来操作文件，如新建、删除、重命名文件和目录等。

File 类不能访问文件内容本身，如果需要访问文件内容本身，则需要使用输入/输出流。

File 类提供了如下三种形式构造方法。

1. File(String path)：如果 path 是实际存在的路径，则该 File 对象表示的是目录；如果 path 是文件名，则该 File 对象表示的是文件。
2. File(String path, String name)：path 是路径名，name 是文件名。
3. File(File dir, String name)：dir 是路径对象，name 是文件名。


使用任意一个构造方法都可以创建一个 File 对象，然后调用其提供的方法对文件进行操作。在表 1 中列出了 File 类的常用方法及说明。

| 方法名称                      | 说明                                                         |
| ----------------------------- | ------------------------------------------------------------ |
| boolean canRead()             | 测试应用程序是否能从指定的文件中进行读取                     |
| boolean canWrite()            | 测试应用程序是否能写当前文件                                 |
| boolean delete()              | 删除当前对象指定的文件                                       |
| boolean exists()              | 测试当前 File 是否存在                                       |
| String getAbsolutePath()      | 返回由该对象表示的文件的绝对路径名                           |
| String getName()              | 返回表示当前对象的文件名或路径名（如果是路径，则返回最后一级子路径名） |
| String getParent()            | 返回当前 File 对象所对应目录（最后一级子目录）的父目录名     |
| boolean isAbsolute()          | 测试当前 File 对象表示的文件是否为一个绝对路径名。该方法消除了不同平台的差异，可以直接判断 file 对象是否为绝对路径。在 UNIX/Linux/BSD 等系统上，如果路径名开头是一条斜线`/`，则表明该 File 对象对应一个绝对路径；在 Windows 等系统上，如果路径开头是盘符，则说明它是一个绝对路径。 |
| boolean isDirectory()         | 测试当前 File 对象表示的文件是否为一个路径                   |
| boolean isFile()              | 测试当前 File 对象表示的文件是否为一个“普通”文件             |
| long lastModified()           | 返回当前 File 对象表示的文件最后修改的时间                   |
| long length()                 | 返回当前 File 对象表示的文件长度                             |
| String[] list()               | 返回当前 File 对象指定的路径文件列表                         |
| String[] list(FilenameFilter) | 返回当前 File 对象指定的目录中满足指定过滤器的文件列表       |
| boolean mkdir()               | 创建一个目录，它的路径名由当前 File 对象指定                 |
| boolean mkdirs()              | 创建一个目录，它的路径名由当前 File 对象指定                 |
| boolean renameTo(File)        | 将当前 File 对象指定的文件更名为给定参数 File 指定的路径名   |

File 类中有以下两个常用常量：

- public static final String pathSeparator：指的是分隔连续多个路径字符串的分隔符，Windows 下指`;`。例如 `java -cp test.jar;abc.jar HelloWorld`。
- public static final String separator：用来分隔同一个路径字符串中的目录的，Windows 下指`/`。例如 `C:/Program Files/Common Files`。

注意：可以看到 File 类的常量定义的命名规则不符合标准命名规则，常量名没有全部大写，这是因为 Java 的发展经过了一段相当长的时间，而命名规范也是逐步形成的，File 类出现较早，所以当时并没有对命名规范有严格的要求，这些都属于 Java 的历史遗留问题。

Windows 的路径分隔符使用反斜线“\”，而 Java 程序中的反斜线表示转义字符，所以如果需要在 Windows 的路径下包括反斜线，则应该使用两条反斜线或直接使用斜线“/”也可以。Java 程序支持将斜线当成平台无关的路径分隔符。

假设在 Windows 操作系统中有一文件 `D:\javaspace\hello.java`，在 Java 中使用的时候，其路径的写法应该为 `D:/javaspace/hello.java` 或者 `D:\\javaspace\\hello.java`。

### 字节流的使用

#### 字节输入流

InputStream 类及其子类的对象表示字节输入流，InputStream 类的常用子类如下。

- ByteArrayInputStream 类：将字节数组转换为字节输入流，从中读取字节。
- FileInputStream 类：从文件中读取数据。
- PipedInputStream 类：连接到一个 PipedOutputStream（管道输出流）。
- SequenceInputStream 类：将多个字节输入流串联成一个字节输入流。
- ObjectInputStream 类：将对象反序列化。


使用 InputStream 类的方法可以从流中读取一个或一批字节。表 1 列出了 InputStream 类的常用方法。



| 方法名及返回值类型                   | 说明                                                         |
| ------------------------------------ | ------------------------------------------------------------ |
| int read()                           | 从输入流中读取一个 8 位的字节，并把它转换为 0~255 的整数，最后返回整数。 如果返回 -1，则表示已经到了输入流的末尾。为了提高 I/O 操作的效率，建议尽量 使用 read() 方法的另外两种形式 |
| int read(byte[] b)                   | 从输入流中读取若干字节，并把它们保存到参数 b 指定的字节数组中。 该方法返回 读取的字节数。如果返回 -1，则表示已经到了输入流的末尾 |
| int read(byte[] b, int off, int len) | 从输入流中读取若干字节，并把它们保存到参数 b 指定的字节数组中。其中，off 指 定在字节数组中开始保存数据的起始下标；len 指定读取的字节数。该方法返回实际 读取的字节数。如果返回 -1，则表示已经到了输入流的末尾 |
| void close()                         | 关闭输入流。在读操作完成后，应该关闭输入流，系统将会释放与这个输入流相关 的资源。注意，InputStream 类本身的 close() 方法不执行任何操作，但是它的许多 子类重写了 close() 方法 |
| int available()                      | 返回可以从输入流中读取的字节数                               |
| long skip(long n)                    | 从输入流中跳过参数 n 指定数目的字节。该方法返回跳过的字节数  |
| void mark(int readLimit)             | 在输入流的当前位置开始设置标记，参数 readLimit 则指定了最多被设置标记的字 节数 |
| boolean markSupported()              | 判断当前输入流是否允许设置标记，是则返回 true，否则返回 false |
| void reset()                         | 将输入流的指针返回到设置标记的起始处                         |


注意：在使用 mark() 方法和 reset() 方法之前，需要判断该文件系统是否支持这两个方法，以避免对程序造成影响。

#### 字节输出流

OutputStream 类及其子类的对象表示一个字节输出流。OutputStream 类的常用子类如下。

- ByteArrayOutputStream 类：向内存缓冲区的字节数组中写数据。
- FileOutputStream 类：向文件中写数据。
- PipedOutputStream 类：连接到一个 PipedlntputStream（管道输入流）。
- ObjectOutputStream 类：将对象序列化。


利用 OutputStream 类的方法可以从流中写入一个或一批字节。表 2 列出了 OutputStream 类的常用方法。



| 方法名及返回值类型                   | 说明                                                         |
| ------------------------------------ | ------------------------------------------------------------ |
| void write(int b)                    | 向输出流写入一个字节。这里的参数是 int 类型，但是它允许使用表达式， 而不用强制转换成 byte 类型。为了提高 I/O 操作的效率，建议尽量使用 write() 方法的另外两种形式 |
| void write(byte[] b)                 | 把参数 b 指定的字节数组中的所有字节写到输出流中              |
| void write(byte[] b,int off,int len) | 把参数 b 指定的字节数组中的若干字节写到输出流中。其中，off 指定字节 数组中的起始下标，len 表示元素个数 |
| void close()                         | 关闭输出流。写操作完成后，应该关闭输出流。系统将会释放与这个输出 流相关的资源。注意，OutputStream 类本身的 close() 方法不执行任何操 作，但是它的许多子类重写了 close() 方法 |
| void flush()                         | 为了提高效率，在向输出流中写入数据时，数据一般会先保存到内存缓冲 区中，只有当缓冲区中的数据达到一定程度时，缓冲区中的数据才会被写 入输出流中。使用 flush() 方法则可以强制将缓冲区中的数据写入输出流， 并清空缓冲区 |

#### 字节数组输入流

ByteArrayInputStream 类可以从内存的字节数组中读取数据，该类有如下两种构造方法重载形式。

1. ByteArrayInputStream(byte[] buf)：创建一个字节数组输入流，字节数组类型的数据源由参数 buf 指定。
2. ByteArrayInputStream(byte[] buf,int offse,int length)：创建一个字节数组输入流，其中，参数 buf 指定字节数组类型的数据源，offset 指定在数组中开始读取数据的起始下标位置，length 指定读取的元素个数。

```java
public class test08 {
    public static void main(String[] args) {
        byte[] b = new byte[] { 1, -1, 25, -22, -5, 23 }; // 创建数组
        ByteArrayInputStream bais = new ByteArrayInputStream(b, 0, 6); // 创建字节数组输入流
        int i = bais.read(); // 从输入流中读取下一个字节，并转换成int型数据
        while (i != -1) { // 如果不返回-1，则表示没有到输入流的末尾
            System.out.println("原值=" + (byte) i + "\t\t\t转换为int类型=" + i);
            i = bais.read(); // 读取下一个
        }
    }
}
```

#### 字节数组输出流

ByteArrayOutputStream 类可以向内存的字节数组中写入数据，该类的构造方法有如下两种重载形式。

1. ByteArrayOutputStream()：创建一个字节数组输出流，输出流缓冲区的初始容量大小为 32 字节。
2. ByteArrayOutputStream(int size)：创建一个字节数组输出流，输出流缓冲区的初始容量大小由参数 size 指定。


ByteArrayOutputStream 类中除了有前面介绍的字节输出流中的常用方法以外，还有如下两个方法。

1. intsize()：返回缓冲区中的当前字节数。
2. byte[] toByteArray()：以字节数组的形式返回输出流中的当前内容。

```java
public class Test09 {
    public static void main(String[] args) {
        ByteArrayOutputStream baos = new ByteArrayOutputStream();
        byte[] b = new byte[] { 1, -1, 25, -22, -5, 23 }; // 创建数组
        baos.write(b, 0, 6); // 将字节数组b中的前4个字节元素写到输出流中
        System.out.println("数组中一共包含：" + baos.size() + "字节"); // 输出缓冲区中的字节数
        byte[] newByteArray = baos.toByteArray(); // 将输出流中的当前内容转换成字节数组
        System.out.println(Arrays.toString(newByteArray)); // 输出数组中的内容
    }
}
```

#### 文件输入流

FileInputStream 是 Java 流中比较常用的一种，它表示从文件系统的某个文件中获取输入字节。通过使用 FileInputStream 可以访问文件中的一个字节、一批字节或整个文件。

在创建 FileInputStream 类的对象时，如果找不到指定的文件将拋出 FileNotFoundException 异常，该异常必须捕获或声明拋出。

FileInputStream 常用的构造方法主要有如下两种重载形式。

1. FileInputStream(File file)：通过打开一个到实际文件的连接来创建一个 FileInputStream，该文件通过文件系统中的 File 对象 file 指定。
2. FileInputStream(String name)：通过打开一个到实际文件的链接来创建一个 FileInputStream，该文件通过文件系统中的路径名 name 指定。

#### 文件输出流

FileOutputStream 类继承自 OutputStream 类，重写和实现了父类中的所有方法。FileOutputStream 类的对象表示一个文件字节输出流，可以向流中写入一个字节或一批字节。在创建 FileOutputStream 类的对象时，如果指定的文件不存在，则创建一个新文件；如果文件已存在，则清除原文件的内容重新写入。

FileOutputStream 类的构造方法主要有如下 4 种重载形式。

1. FileOutputStream(File file)：创建一个文件输出流，参数 file 指定目标文件。
2. FileOutputStream(File file,boolean append)：创建一个文件输出流，参数 file 指定目标文件，append 指定是否将数据添加到目标文件的内容末尾，如果为 true，则在末尾添加；如果为 false，则覆盖原有内容；其默认值为 false。
3. FileOutputStream(String name)：创建一个文件输出流，参数 name 指定目标文件的文件路径信息。
4. FileOutputStream(String name,boolean append)：创建一个文件输出流，参数 name 和 append 的含义同上。


注意：使用构造方法 FileOutputStream(String name,boolean append) 创建一个文件输出流对象，它将数据附加在现有文件的末尾。该字符串 name 指明了原文件，如果只是为了附加数据而不是重写任何已有的数据，布尔类型参数 append 的值应为 true。

对文件输出流有如下四点说明：

1. 在 FileOutputStream 类的构造方法中指定目标文件时，目标文件可以不存在。
2. 目标文件的名称可以是任意的，例如 D:\\abc、D:\\abc.de 和 D:\\abc.de.fg 等都可以，可以使用记事本等工具打开并浏览这些文件中的内容。
3. 目标文件所在目录必须存在，否则会拋出 java.io.FileNotFoundException 异常。
4. 目标文件的名称不能是已存在的目录。例如 D 盘下已存在 Java 文件夹，那么就不能使用 Java 作为文件名，即不能使用 D:\\Java，否则抛出 java.io.FileNotFoundException 异常。

### 字符流的使用

#### 字符输入流

Reader 类是所有字符流输入类的父类，该类定义了许多方法，这些方法对所有子类都是有效的。

Reader 类的常用子类如下。

- CharArrayReader 类：将字符数组转换为字符输入流，从中读取字符。
- StringReader 类：将字符串转换为字符输入流，从中读取字符。
- BufferedReader 类：为其他字符输入流提供读缓冲区。
- PipedReader 类：连接到一个 PipedWriter。
- InputStreamReader 类：将字节输入流转换为字符输入流，可以指定字符编码。


与 InputStream 类相同，在 Reader 类中也包含 close()、mark()、skip() 和 reset() 等方法，这些方法可以参考 InputStream 类的方法。下面主要介绍 Reader 类中的 read() 方法，如表 1 所示。

| 方法名及返回值类型                    | 说明                                                         |
| ------------------------------------- | ------------------------------------------------------------ |
| int read()                            | 从输入流中读取一个字符，并把它转换为 0~65535 的整数。如果返回 -1， 则表示 已经到了输入流的末尾。为了提高 I/O 操作的效率，建议尽量使用下面两种 read() 方法 |
| int read(char[] cbuf)                 | 从输入流中读取若干个字符，并把它们保存到参数 cbuf 指定的字符数组中。 该方 法返回读取的字符数，如果返回 -1，则表示已经到了输入流的末尾 |
| int read(char[] cbuf,int off,int len) | 从输入流中读取若干个字符，并把它们保存到参数 cbuf 指定的字符数组中。其中， off 指定在字符数组中开始保存数据的起始下标，len 指定读取的字符数。该方法返 回实际读取的字符数，如果返回 -1，则表示已经到了输入流的末尾 |

#### 字符输出流

与 Reader 类相反，Writer 类是所有字符输出流的父类，该类中有许多方法，这些方法对继承该类的所有子类都是有效的。

Writer 类的常用子类如下。

- CharArrayWriter 类：向内存缓冲区的字符数组写数据。
- StringWriter 类：向内存缓冲区的字符串（StringBuffer）写数据。
- BufferedWriter 类：为其他字符输出流提供写缓冲区。
- PipedWriter 类：连接到一个 PipedReader。
- OutputStreamReader 类：将字节输出流转换为字符输出流，可以指定字符编码。


与 OutputStream 类相同，Writer 类也包含 close()、flush() 等方法，这些方法可以参考 OutputStream 类的方法。下面主要介绍 Writer 类中的 write() 方法和 append() 方法，如表 2 所示。

| 方法名及返回值类型                         | 说明                                                         |
| ------------------------------------------ | ------------------------------------------------------------ |
| void write(int c)                          | 向输出流中写入一个字符                                       |
| void write(char[] cbuf)                    | 把参数 cbuf 指定的字符数组中的所有字符写到输出流中           |
| void write(char[] cbuf,int off,int len)    | 把参数 cbuf 指定的字符数组中的若干字符写到输出流中。其中，off 指定 字符数组中的起始下标，len 表示元素个数 |
| void write(String str)                     | 向输出流中写入一个字符串                                     |
| void write(String str, int off,int len)    | 向输出流中写入一个字符串中的部分字符。其中，off 指定字符串中的起 始偏移量，len 表示字符个数 |
| append(char c)                             | 将参数 c 指定的字符添加到输出流中                            |
| append(charSequence esq)                   | 将参数 esq 指定的字符序列添加到输出流中                      |
| append(charSequence esq,int start,int end) | 将参数 esq 指定的字符序列的子序列添加到输出流中。其中，start 指定 子序列的第一个字符的索引，end 指定子序列中最后一个字符后面的字符 的索引，也就是说子序列的内容包含 start 索引处的字符，但不包括 end 索引处的字符 |


注意：Writer 类所有的方法在出错的情况下都会引发 IOException 异常。关闭一个流后，再对其进行任何操作都会产生错误。

#### 字符文件输入流

为了读取方便，Java 提供了用来读取字符文件的便捷类——FileReader。该类的构造方法有如下两种重载形式。

1. FileReader(File file)：在给定要读取数据的文件的情况下创建一个新的 FileReader 对象。其中，file 表示要从中读取数据的文件。
2. FileReader(String fileName)：在给定从中读取数据的文件名的情况下创建一个新 FileReader 对象。其中，fileName 表示要从中读取数据的文件的名称，表示的是一个文件的完整路径。


在用该类的构造方法创建 FileReader 读取对象时，默认的字符编码及字节缓冲区大小都是由系统设定的。要自己指定这些值，可以在 FilelnputStream 上构造一个 InputStreamReader。

注意：在创建 FileReader 对象时可能会引发一个 FileNotFoundException 异常，因此需要使用 try catch 语句捕获该异常。

字符流和字节流的操作步骤相同，都是首先创建输入流或输出流对象，即建立连接管道，建立完成后进行读或写操作，最后关闭输入/输出流通道。

要将 D:\myJava\HelloJava.java 文件中的内容读取并输出到控制台，使用 FileReader 类的实现代码如下：

```java
public class Test12 {
    public static void main(String[] args) {
        FileReader fr = null;
        try {
            fr = new FileReader("D:/myJava/HelloJava.java"); // 创建FileReader对象
            int i = 0;
            System.out.println("D:\\myJava\\HelloJava.java文件内容如下：");
            while ((i = fr.read()) != -1) { // 循环读取
                System.out.print((char) i); // 将读取的内容强制转换为char类型
            }
        } catch (Exception e) {
            System.out.print(e);
        } finally {
            try {
                fr.close(); // 关闭对象
            } catch (IOException e) {
                e.printStackTrace();
            }
        }
    }
}
```

#### 字符文件输出流

Java 提供了写入字符文件的便捷类——FileWriter，该类的构造方法有如下 4 种重载形式。

1. FileWriter(File file)：在指定 File 对象的情况下构造一个 FileWriter 对象。其中，file 表示要写入数据的 File 对象。
2. FileWriter(File file,boolean append)：在指定 File 对象的情况下构造一个 FileWriter 对象，如果 append 的值为 true，则将字节写入文件末尾，而不是写入文件开始处。
3. FileWriter(String fileName)：在指定文件名的情况下构造一个 FileWriter 对象。其中，fileName 表示要写入字符的文件名，表示的是完整路径。
4. FileWriter(String fileName,boolean append)：在指定文件名以及要写入文件的位置的情况下构造 FileWriter 对象。其中，append 是一个 boolean 值，如果为 true，则将数据写入文件末尾，而不是文件开始处。


在创建 FileWriter 对象时，默认字符编码和默认字节缓冲区大小都是由系统设定的。要自己指定这些值，可以在 FileOutputStream 上构造一个 OutputStreamWriter 对象。

FileWriter 类的创建不依赖于文件存在与否，如果关联文件不存在，则会自动生成一个新的文件。在创建文件之前，FileWriter 将在创建对象时打开它作为输出。如果试图打开一个只读文件，将引发一个 IOException 异常。

编写一个程序，将用户输入的 4 个字符串保存到 D:\myJava\book.txt 文件中。在这里使用 FileWriter 类中的 write() 方法循环向指定文件中写入数据，实现代码如下：

```java
public class Test13 {
    public static void main(String[] args) {
        Scanner input = new Scanner(System.in);
        FileWriter fw = null;
        try {
            fw = new FileWriter("D:\\myJava\\book.txt"); // 创建FileWriter对象
            for (int i = 0; i < 4; i++) {
                System.out.println("请输入第" + (i + 1) + "个字符串：");
                String name = input.next(); // 读取输入的名称
                fw.write(name + "\r\n"); // 循环写入文件
            }
            System.out.println("录入完成！");
        } catch (Exception e) {
            System.out.println(e.getMessage());
        } finally {
            try {
                fw.close(); // 关闭对象
            } catch (IOException e) {
                e.printStackTrace();
            }
        }
    }
}
```

#### 字符缓冲区输入流

BufferedReader 类主要用于辅助其他字符输入流，它带有缓冲区，可以先将一批数据读到内存缓冲区。接下来的读操作就可以直接从缓冲区中获取数据，而不需要每次都从数据源读取数据并进行字符编码转换，这样就可以提高数据的读取效率。

BufferedReader 类的构造方法有如下两种重载形式。

1. BufferedReader(Reader in)：创建一个 BufferedReader 来修饰参数 in 指定的字符输入流。
2. BufferedReader(Reader in,int size)：创建一个 BufferedReader 来修饰参数 in 指定的字符输入流，参数 size 则用于指定缓冲区的大小，单位为字符。

除了可以为字符输入流提供缓冲区以外，BufferedReader 还提供了 `readLine()` 方法，该方法返回包含该行内容的字符串，但该字符串中不包含任何终止符，如果已到达流末尾，则返回 null。readLine() 方法表示每次读取一行文本内容，当遇到换行（\n）、回车（\r）或回车后直接跟着换行标记符即可认为某行已终止。

使用 BufferedReader 类中的 readLine() 方法逐行读取 D:\myJava\Book.txt 文件中的内容，并将读取的内容在控制台中打印输出，代码如下：

```java
public class Test13 {
    public static void main(String[] args) {
        FileReader fr = null;
        BufferedReader br = null;
        try {
            fr = new FileReader("D:\\myJava\\book.txt"); // 创建 FileReader 对象
            br = new BufferedReader(fr); // 创建 BufferedReader 对象
            System.out.println("D:\\myJava\\book.txt 文件中的内容如下：");
            String strLine = "";
            while ((strLine = br.readLine()) != null) { // 循环读取每行数据
                System.out.println(strLine);
            }
        } catch (FileNotFoundException e1) {
            e1.printStackTrace();
        } catch (IOException e) {
            e.printStackTrace();
        } finally {
            try {
                fr.close(); // 关闭 FileReader 对象
                br.close();
            } catch (IOException e) {
                e.printStackTrace();
            }
        }
    }
}
```

#### 字符缓冲区输出流

BufferedWriter 类主要用于辅助其他字符输出流，它同样带有缓冲区，可以先将一批数据写入缓冲区，当缓冲区满了以后，再将缓冲区的数据一次性写到字符输出流，其目的是为了提高数据的写效率。

BufferedWriter 类的构造方法有如下两种重载形式。

1. BufferedWriter(Writer out)：创建一个 BufferedWriter 来修饰参数 out 指定的字符输出流。
2. BufferedWriter(Writer out,int size)：创建一个 BufferedWriter 来修饰参数 out 指定的字符输出流，参数 size 则用于指定缓冲区的大小，单位为字符。


该类除了可以给字符输出流提供缓冲区之外，还提供了一个新的方法 newLine()，该方法用于写入一个行分隔符。行分隔符字符串由系统属性 line.separator 定义，并且不一定是单个新行（\n）符。

### 转换流

InputStreamReader 用于将字节输入流转换为字符输入流，其中 OutputStreamWriter 用于将字节输出流转换为字符输出流。使用转换流可以在一定程度上避免乱码，还可以指定输入输出所使用的字符集。

## 注解

### 注解（Annotation）

从 [Java](http://c.biancheng.net/java/) 5 版本之后可以在源代码中嵌入一些补充信息，这种补充信息称为注解（Annotation），是 Java 平台中非常重要的一部分。注解都是 @ 符号开头的，例如我们在学习方法重写时使用过的 @Override 注解。同 Class 和 Interface 一样，注解也属于一种类型。

> Annotation 可以翻译为“注解”或“注释”，一般翻译为“注解”，因为“注释”一词已经用于说明“//”、“/**...*/”和“/*...*/”等符号了，这里的“注释”是英文 Comment 翻译。

### 内置的注解

Java 定义了一套注解，共有 7 个，3 个在 java.lang 中，剩下 4 个在 java.lang.annotation 中。

**作用在代码的注解是**

- @Override - 检查该方法是否是重载方法。如果发现其父类，或者是引用的接口中并没有该方法时，会报编译错误。
- @Deprecated - 标记过时方法。如果使用该方法，会报编译警告。
- @SuppressWarnings - 指示编译器去忽略注解中声明的警告。

作用在其他注解的注解(或者说 元注解)是:

- @Retention - 标识这个注解怎么保存，是只在代码中，还是编入class文件中，或者是在运行时可以通过反射访问。
- @Documented - 标记这些注解是否包含在用户文档中。
- @Target - 标记这个注解应该是哪种 Java 成员。
- @Inherited - 标记这个注解是继承于哪个注解类(默认 注解并没有继承于任何子类)

从 Java 7 开始，额外添加了 3 个注解:

- @SafeVarargs - Java 7 开始支持，忽略任何使用参数为泛型变量的方法或构造函数调用产生的警告。
- @FunctionalInterface - Java 8 开始支持，标识一个匿名函数或函数式接口。
- @Repeatable - Java 8 开始支持，标识某注解可以在同一个声明上使用多次。

### Annotation 组成部分

java Annotation 的组成中，有 3 个非常重要的主干类。它们分别是：

```java
Annotation.java
package java.lang.annotation;
public interface Annotation {

    boolean equals(Object obj);

    int hashCode();

    String toString();

    Class<? extends Annotation> annotationType();
}
```

```java
ElementType.java
package java.lang.annotation;

public enum ElementType {
    TYPE,               /* 类、接口（包括注释类型）或枚举声明  */

    FIELD,              /* 字段声明（包括枚举常量）  */

    METHOD,             /* 方法声明  */

    PARAMETER,          /* 参数声明  */

    CONSTRUCTOR,        /* 构造方法声明  */

    LOCAL_VARIABLE,     /* 局部变量声明  */

    ANNOTATION_TYPE,    /* 注释类型声明  */

    PACKAGE             /* 包声明  */
}
```

```
RetentionPolicy.java
package java.lang.annotation;
public enum RetentionPolicy {
    SOURCE,            /* Annotation信息仅存在于编译器处理期间，编译器处理完之后就没有该Annotation信息了  */

    CLASS,             /* 编译器将Annotation存储于类对应的.class文件中。默认行为  */

    RUNTIME            /* 编译器将Annotation存储于class文件中，并且可由JVM读入 */
}
```

### 自定义注解

声明自定义注解使用 @interface 关键字（interface 关键字前加 @ 符号）实现。定义注解与定义接口非常像，如下代码可定义一个简单形式的注解类型。

```java
// 定义一个简单的注解类型
public @interface Test {
}
```

上述代码声明了一个 Test 注解。默认情况下，注解可以在程序的任何地方使用，通常用于修饰类、接口、方法和变量等。

定义注解和定义类相似，注解前面的访问修饰符和类一样有两种，分别是公有访问权限（public）和默认访问权限（默认不写）。一个源程序文件中可以声明多个注解，但只能有一个是公有访问权限的注解。且源程序文件命名和公有访问权限的注解名一致。

不包含任何成员变量的注解称为标记注解，例如上面声明的 Test 注解以及基本注解中的 @Override 注解都属于标记注解。根据需要，注解中可以定义成员变量，成员变量以无形参的方法形式来声明，其方法名和返回值定义了该成员变量的名字和类型。

```java
public @interface MyTag {
    // 定义带两个成员变量的注解
    // 注解中的成员变量以方法的形式来定义
    String name();
    int age();
}
```

以上代码中声明了一个 MyTag 注解，定义了两个成员变量，分别是 name 和 age。成员变量也可以有访问权限修饰符，但是只能有公有权限和默认权限。

```java
public class Test {
    // 使用带成员变量的注解时，需要为成员变量赋值
    @MyTag(name="xx", age=6)
    public void info() {
        ...
    }
    ...
}
```

注解中的成员变量也可以有默认值，可使用 default 关键字。如下代码定义了 @MyTag 注解，该注解里包含了 name 和 age 两个成员变量。

```java
public @interface MyTag {
    // 定义了两个成员变量的注解
    // 使用default为两个成员变量指定初始值
    String name() default "C语言中文网";
    int age() default 7;
}
```

如果为注解的成员变量指定了默认值，那么使用该注解时就可以不为这些成员变量赋值，而是直接使用默认值。

```java
public class Test {
    // 使用带成员变量的注解
    // MyTag注释的成员变量有默认值，所以可以不为它的成员变量赋值
    @MyTag
    public void info() {
        ...
    }
    ...
}
```

