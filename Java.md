# Java

## Head First Java

### Chap1. 基本概念

#### Java 的工作方式

1. 源代码(.java)
2. 编译器(检查错误)
3. 输出字节码(.class)
4. Java 虚拟机(读取和执行.class 文件)

Java 虚拟机启动执行时，会寻找在命令列所指定的类，接着会去执行 main 方法，每个 Java 程序最少都会有一个类以及一个 main()，每个应用程序只有一个 main()函数。

在 Java 中所有东西都会属于某个类，`.java`文件会被编译为类`.class`文件，真正被执行的是类。

### Chap2. 类与对象

对象可以理解为`instance`。

类是对象的蓝图。

`main()`的两种用途:

- 测试类
- 启动 Java 应用程序

Java 创建的对象会被放在堆内存中，Java 会主动回收内存，当某个对象被 JVM 标记为可回收，当内存不足式就会启动垃圾回收器来清理，回收空间。

### Chap3. primitive 主数据类型和引用

> Variables must have a type

> Variables must have a name

primitive 主数据类型(8 种)

- boolean
- char
- byte
- short
- int
- long
- float
- double

| 类型                   | 位数              | 值域                   |
| ---------------------- | ----------------- | ---------------------- |
| boolean                | (Java 虚拟机决定) | true/false             |
| char                   | 16bits            | 0~65535                |
| 数值(带正负号)interger |                   |
| byte                   | 8bits             | -128~127               |
| short                  | 16bits            | -32768~32767           |
| int                    | 32bits            | -2147483648~2147483647 |
| long                   | 64bits            | -很大~+很大            |
| 浮点数                 |                   |
| float                  | 32bits            | 范围规模可变           |
| double                 | 64bits            | 范围规模可变           |

变量命名方法

- 名称必须以字母，下划线(\_)，或者\$符号开头，不能用数字开头
- 除了第一个字符之外，后面可以用数字
- 还需要避开 Java 保留字

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

- 实参(argument)
- 形参(parameter)

> 如果将一个方法声明有返回值，就必须返回所声明类型的值

Java 是通过值传递传递的，也就是说通过拷贝传递。

`getter`和`setter`正式的命名`Accessor`与`Mutator`

封装的基本原则:

- 将实例变量标记为`private`
- 将`getter`和`setter`标记为`public`

实例变量永远都会有默认值:

| 类型            | 默认值 |
| --------------- | ------ |
| integers        | 0      |
| floating points | 0.0    |
| booleans        | false  |
| references      | null   |

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

#### 变量的比较(primitive 主数据类型或引用)

使用`==`来比较两个`primitive`主数据类型或者判断两个引用是否引用同一个对象。

使用`equals()`来判断两个对象在意义上是否相等(比如两个`String`对象是否带有相同的字节组合)。

### Chap5. 编写程序

伪码(专注于逻辑不需要考虑程序语法)

测试码(测试用的程序代码)

真实码(实际设计出的真正 Java 程序代码)

```java
Integer.parseInt("3"); // 将string转换成int

for(int cell:locationCells) {} // for in

int randomNum = (int)(Math.random() * 5) // Math.ramdom会返回一个0~1之间的书，double类型
```

### Chap6. 使用 Java API

以`ArrayList`为例

```java
// 创建
ArrayList<Egg> myList = new ArrayList<Egg>();

// 加入元素
Egg s = new Egg();
myList.add(s);

// 查询大小
int theSize = myList.size();

// 查询特定元素
boolean isIn = myList.contains(s);

// 查询特定元素的位置
int inx = myList.indexOf(b);

// 判断集合是否为空
bpoolean empty = myList.isEmpty();

// 删除元素
myList.remove(s);
```

### Chap7. 继承与多态

子类中引用父类的方法再加上额外的行为

```java
publiv void roam() {
    super.roam();
    // my own roam stuff
}
```

- public 类型的成员会被继承
- private 类型的成员不会被继承

子类会继承父类所有`public`类型的实例变量和方法，但不会继承父类所有`private`类型的变量和方法。

继承下来的方法可以被覆盖掉，但实例变量不能被覆盖掉。

当某个方法再子类中被覆盖过，调用这个方法时会调用到覆盖过的版本。

#### 多态(`polymorphism`)

```java
// 引用类型与对象类型一致
Dog myDog = new Dog();

// 多态下，引用与对象可以是不同的类型
Animal myDog = new Dog();
```

运用多态时，引用类型可以是实际对象类型的父类。

final 这个修饰符表示是继承树的末端，不能被继承。

覆盖的规则:

- 参数必须要一样，且返回类型必须要兼容
- 不能降低方法的存取权限

方法的重载：

- 返回类型可以不同
- 不能只改变返回类型
- 可以更改存取权限

### Chap8. 接口与抽象类

```java
// 有些类不应该被初始化
Animal anim = new Animal();
```

需要有`Animal`这个类来产生继承和多态，但是要限制只有它的子类才能被初始化。我们要的是`Lion`,`Hippo`对象，而不是`Animal`对象。于是有了抽象类。抽象的类代表此类必须要被`extend`过。

```java
abstract class Canine extends Animal {
  public void roam() { }
}
```

方法也可以抽象，抽象的方法没有实体。抽象的方法代表此方法一定要被覆盖。

如果声明出一个抽象的方法，就必须将类也标记为抽象的。不能在非抽象类中拥有抽象方法。

```java
public abstract void eat();
```

Java 中所有类都是从 Object 这个类继承出来的。

有下面几个方法:

- equals(Object o)
- getClass()
- hashCode()
- toString()

```java
if (o instanceof Dog) {
  Dog d = (Dog) o;
}
```

将引用比作遥控器很恰当，我们可以吧`Snowboard`当做`Snowboard`或者`Object`，`Object`的遥控器只有几个按钮，`Snowboard`的遥控器包含`Object`和自己定义的按钮，在继承树往下走时，遥控器的按钮越来越多。

关于多态的几句总结

- 不管实际上所引用的对象是什么类型，只有在引用变量的类型就是带有某方法的类型时才能调用该方法
- `Object`引用变量在没有类型转换的情况下不能赋值给其他类型`Dog d = (Dog) x.getObject(aDog);`
- 从`ArrayList<Object>`取出的对象只能被`Object`引用，不然就要用类型转换来改变

#### 接口

```java
// 接口的定义
public interface Pet {
  public abstract void beFriendly(); // 接口的方法一定是抽象的
  public abstract void play();
}

// 接口的实现
public class Dog extends Canine implements Pet {
  public void beFriendly() {...} // 必须在这里实现出接口的方法
  public void play();

  public void roam() {...} // 一般的覆盖方法
}
```

使用接口就可以继承超过一个以上的来源。

`super`关键字可以实现在子类中调用父类的方法。

### Chap9. 构造器与垃圾收集器

- 变量的生存空间 ==> 栈(stack)
- 对象的生存空间 ==> 堆(heap)

java 同样有调用栈

- 实例变量是声明在类中方法之外的地方，位于堆上
- 局部变量声明在方法或方法的参数上

创建对象的三个步骤

- 声明
- 创建
- 赋值

```java
// 声明引用变量|
Duck muDuck = new Duck(); // 创建出新的引用变量

//          | 创建对象
Duck muDuck = new Duck();

//    |连接对象和引用|
Duck muDuck = new Duck(); // 赋值对象给引用
```

`new Duck()`调用的是对象的构造函数

构造函数必须与类同名且没有返回类型

如果没有写构造函数，编译器会安排一个

默认的构造函数没有参数

如果写了构造函数，编译器就不会调用

最好能有无参数的构造函数让人可以选择使用默认值

某种对象不应该在状态被初始化前就用，使用构造函数来初始化`Duck`的状态

构造函数重载

在创建新对象时，所有继承下来的构造函数都会执行，这样的过程被称为"构造函数链(Constructor Chaining)"

创建`Hippo`也代表创建`Animal`与`Object`

```java
new Hippo()
```

构造函数堆栈

```chart
                Object
        Animal  Animal  Animal
Hippo   Hippo   Hippo   Hippo
```

使用`super()`调用父类的构造函数，如果没有调用`super()`，编译器会帮我们加上`super()`的调用

每个子类的构造函数会立即调用父类的构造函数

```java
public Boop(int i) {
  size = i;
  super(); // 这样过不了编译
}
```

当把引用设置为`null`时，相当于一个没有电视的遥控器，对`null`引用使用圆点运算符会在执行期遇到`NullPointerException`这样的错误。

### Chap10. 数字与静态

> `Math`这个类中的所有方法都不需要实例变量值，这些方法都是静态的，无需`Math`的实例

一个静态方法表示"一种不依靠实例变量就诶回去不需要对象的行为"

```java
// 以类的名称调用静态的方法
Math.min(88, 86);

// 以引用变量的名称调用非静态的方法
Song t2 = new Song();
t2.play();
```

静态的方法是在无关特定类的实例情况下执行的，不能调用非静态的变量，也不能调用非静态的方法

静态变量的值对所有的实例来说都相同，静态变量被同类所有实例共享

- 实例变量: 每个实例一个
- 静态变量：每个类一个

对于一个`final`变量，如果是基本数据类型的变量，则其数值一旦在初始化之后便不能更改；如果是引用类型的变量，则在对其初始化之后便不能再让其指向另一个对象。

```java
public static final double PI = 3.1415926;
```

`final`修饰`method`表示不能覆盖`method`

`final`修饰类表示不能继承该类(也就是创建它的子类)

- 如果类只有静态的方法，可以将构造函数标记为`private`以避免被初始化
- Java 中的常量是把变量同时标记为`static`和`final`的
- 常量的命名惯例是全部使用大写字母

Math 的方法

- `Math.random()`返回介于 0.0~1.0 之间的双精度浮点
- `Math.abs()`返回双精度浮点参数的绝对值
- `Math.round()`返回四舍五入的`int`
- `Math.min()`返回两个参数中较小的
- `Math.max()`返回两个参数中较大的

包装类

- Boolean
- Character
- Byte
- Short
- Interger
- Long
- Float
- Double

```java
// 包装
int i = 288;
Interger iWrap = new Interger(i);

// 解开包装
int unWrapped = iWrap.intValue();
```

数字的格式化

```java
String s = String.format("%, d", 1000000)
```

### Chap11. 异常处理

调用有风险的方法时需要加上异常处理

异常是一种`Exception`的对象

```java
try {
  // 危险动作
} catch(Exception ex) {
  // 尝试恢复
} finally {
  // 无论如何都会执行
}
```

### Chap14. 序列化和文件的输入/输出

### Chap15. 网络与线程

### Chap16. 集合与泛型

泛型意味着更好的类型安全性，创建类型安全更好的集合，让问题在编译期就能抓到
