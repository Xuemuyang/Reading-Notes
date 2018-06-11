# Java

## Head First Java

### Chap1. 基本概念

#### Java的工作方式

1. 源代码(.java)
2. 编译器(检查错误)
3. 输出字节码(.class)
4. Java虚拟机(读取和执行.class文件)

Java虚拟机启动执行时，会寻找在命令列所指定的类，接着会去执行main方法，每个Java程序最少都会有一个类以及一个main()，每个应用程序只有一个main()函数。

在Java中所有东西都会属于某个类，`.java`文件会被编译为类`.class`文件，真正被执行的是类。

### Chap2. 类与对象

对象可以理解为`instance`。

类是对象的蓝图。

`main()`的两种用途:

+ 测试类
+ 启动Java应用程序

Java创建的对象会被放在堆内存中，Java会主动回收内存，当某个对象被JVM标记为可回收，当内存不足式就会启动垃圾回收器来清理，回收空间。

### Chap3. primitive主数据类型和引用

> Variables must have a type

> Variables must have a name

primitive主数据类型(8种)

+ boolean
+ char
+ byte
+ short
+ int
+ long
+ float
+ double

类型|位数|值域
---|---|---
boolean|(Java虚拟机决定)|true/false
char|16bits|0~65535
数值(带正负号)interger||
byte|8bits|-128~127
short|16bits|-32768~32767
int|32bits|-2147483648~2147483647
long|64bits|-很大~+很大
浮点数||
float|32bits|范围规模可变
double|64bits|范围规模可变

变量命名方法

+ 名称必须以字母，下划线(_)，或者$符号开头，不能用数字开头
+ 除了第一个字符之外，后面可以用数字
+ 还需要避开Java保留字

#### 对象引用

```java
Book b = new Book();
Book c = new Book();

Book d = c; // c与d引用同一个对象
```

创建数组

```java
int[] nums; // 声明一个int数组变量
nums = new int[7]; // 创建大小为7的数组，并将它赋值给nums
```

> 一旦数组被声明出来，就只能装入所声明类型的元素

### Chap4. 方法操作实例变量

+ 实参(argument)
+ 形参(parameter)

> 如果将一个方法声明有返回值，就必须返回所声明类型的值

Java是通过值传递传递的，也就是说通过拷贝传递。

`getter`和`setter`正式的命名`Accessor`与`Mutator`

封装的基本原则:

+ 将实例变量标记为`private`
+ 将`getter`和`setter`标记为`public`

实例变量永远都会有默认值:

类型|默认值
---|---
integers|0
floating points|0.0
booleans|false
references|null

实例变量与局部变量之间的差别:

```java
// 实例变量是声明在类内而不是方法中
class Horse {
    private double height = 15.2;
    private String bread;
    ...
}

// 局部变量是声明在方法中的
class AddThing {
    int a;
    int b = 12;

    public int add() {
        int total = a + b;
        return total;
    }
}

// 局部变量在使用前必须初始化
class Foo {
    public void go() {
        int x;
        int z = x + 3; // !!!无法编译
    }
}
```

#### 变量的比较(primitive主数据类型或引用)

使用`==`来比较两个`primitive`主数据类型或者判断两个引用是否引用同一个对象。

使用`equals()`来判断两个对象在意义上是否相等(比如两个`String`对象是否带有相同的字节组合)。

