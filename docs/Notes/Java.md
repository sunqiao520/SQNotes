## Java基础

##### Java、C++ 和 Matlab的区别

**Java 与 C++**

- 都是面向对象的语言，都支持封装、继承和多态
- Java 不提供指针来直接访问内存，程序内存更加安全
- Java 的类是单继承的，C++ 支持多重继承；虽然 Java 的类不可以多继承，但是接口可以多继承。
- Java 有自动内存管理垃圾回收机制(GC)，不需要程序员手动释放无用内存。
- C ++同时支持方法重载和操作符重载，但是 Java 只支持方法重载（操作符重载增加了复杂性，这与 Java 最初的设计思想不符）。

##### 面向对象的三大基本特征，五大基本原则

1. 三大基本特征

- 封装
- 继承
- 多态

2. 五大基本原则

- 单一职责原则（SRP）
- 开放封闭原则（OCP）

对象或实体应该对扩展开放，对修改封闭。

- 里氏替换原则（LSP）

在对象 x 为类型 T 时 q(x) 成立，那么当 S 是 T 的子类时，对象 y 为类型 S 时 q(y) 也应成立。（即对父类的调用同样适用于子类）

- 依赖倒置原则（DIP）

高层次的模块不应该依赖于低层次的模块，他们都应该依赖于抽象。具体实现应该依赖于抽象，而不是抽象依赖于实现。

- 接口隔离原则（ISP）

不应强迫客户端实现一个它用不上的接口，或是说客户端不应该被迫依赖它们不使用的方法，使用多个专门的接口比使用单个接口要好的多！

##### Java 为什么不用多继承

多重继承让语言变得复杂、低效。

容易概念冲突：一个类继承两个类，这两个类再继承一个类，形成菱形的继承结构，很容易造成概念混淆；

多态方面：先举一个多重继承的例子，我们定义一个动物（类）既是狗（父类1）也是猫（父类2），两个父类都有“叫”这个方法。那么当我们调用“叫”这个方法时，它就不知道是狗叫还是猫叫了，这就是多重继承的冲突。

##### 栈和堆

~~~java
Student student = new Student();
~~~

其中student为变量，存储在java栈空间中，而右边的对象存在堆空间中，student存储的是对象的地址。

**代码优化**：变量定义在循环外可节省栈空间。

##### ASCII码表

<img src="https://gitee.com/sun-qiao321/picture/raw/master/images/clip_image001.png" style="zoom:67%;" />

##### ACM输入常见问题

1、Scanner输入的一个问题：在Scanner类中如果我们在任何一个nextXXX()方法之后调用nextLine()方法，这nextLine()方法不能够从控制台读取任何内容，并且，这游标不会进入控制台，它将跳过这一步。nextXXX()方法包括nextInt()，nextFloat()， nextByte()，nextShort()，nextDouble()，nextLong()，next()。此时应该使用BufferedReader。

BufferedReader br = new BufferedReader(new InputStreamReader(System.in));

br.close();

2、正则表达式

str.split("\\s+") 以任何字符隔开

`\d`表示   0-9 的数字,

`\s`表示 空格,回车,换行等空白符,

`\w`表示单词字符(数字字母下划线)

`+`号表示一个或多个的意思

##### 基本数据类型

<img src="https://gitee.com/sun-qiao321/picture/raw/master/images/clip_image001-1618904589613.png" style="zoom: 50%;" />

##### 访问修饰符

![](https://gitee.com/sun-qiao321/picture/raw/master/images/clip_image001-1618904630299.png)

##### final关键字

用于修饰类、属性和方法；

1. 被final修饰的类不可以被继承
2. 被final修饰的方法不可以被重写
3. 被final修饰的变量是常量，如果是基本数据类型的变量，则其数值一旦在初始化之后便不能再更改；如果是引用类型的变量，则再初始化之后便不能让其指向另一个对象

##### final finally finalize的区别

**final**可以修饰类、变量、方法，修饰类表示该类不能被继承、修饰方法表示该方法不能被重写、修饰变量表示该变量是一个常量不能被重新赋值。

**finally**一般作用在try-catch代码块中，在处理异常的时候，通常我们将一定要执行的代码方法finally代码块中，表示不管是否出现异常，该代码块都会执行，一般用来存放一些关闭资源的代码。

**finalize**是一个方法，属于Object类的一个方法，而Object类是所有类的父类，该方法一般由垃圾回收器来调用，当我们调用System.gc() 方法的时候，由垃圾回收器调用finalize()，回收垃圾，一个对象是否可回收的最后判断。

##### static关键字

1. **修饰成员变量和成员方法:** 被 static 修饰的成员属于类，不属于单个这个类的某个对象，被类中所有对象共享，可以并且建议通过类名调用。被static 声明的成员变量属于静态成员变量，静态变量 存放在 Java 内存区域的方法区。调用格式：`类名.静态变量名` `类名.静态方法名()`
2. **静态代码块:** 静态代码块定义在类中方法外, 静态代码块在非静态代码块之前执行==(静态代码块—>非静态代码块—>构造方法)==。 该类不管创建多少对象，静态代码块只执行一次。
3. **静态内部类（static修饰类的话只能修饰内部类）：** 静态内部类与非静态内部类之间存在一个最大的区别: 非静态内部类在编译完成之后会隐含地保存着一个引用，该引用是指向创建它的外围类，但是静态内部类却没有。没有这个引用就意味着：1. 它的创建是不需要依赖外围类的创建。2. 它不能使用任何外围类的非static成员变量和方法。
4. **静态导包(用来导入类中的静态资源，1.5之后的新特性):** 格式为：`import static` 这两个关键字连用可以指定导入某个类中的指定静态资源，并且不需要使用类名调用类中静态成员，可以直接使用类中静态成员变量和成员方法。

#####  static存在的主要意义

static的主要意义是在于创建独立于具体对象的域变量或者方法。以致于即使没有创建对象，也能使用属性和调用方法！

static关键字还有一个比较关键的作用就是 用来形成静态代码块以优化程序性能。**static块可以置于类中的任何地方，类中可以有多个static块。在类初次被加载的时候，会按照static块的顺序来执行每个static块，并且只会执行一次**。

为什么说static块可以用来==优化程序性能==，是因为它的特性:只会在类加载的时候执行一次。因此，很多时候会将一些只需要进行一次的初始化操作都放在static代码块中进行。

##### 抽象类和接口

**相同点**

接口和抽象类都不能实例化

都位于继承的顶端，用于被其他实现或继承

都包含抽象方法，其子类都必须覆写这些抽象方法

**不同点**

抽象类的作用：提供一个通用的基类，而不是特定的实例类。

接口：描述类有什么功能，而不给出每个功能的具体实现。

<img src="https://gitee.com/sun-qiao321/picture/raw/master/images/20210425100900.png" style="zoom:50%;" />

##### 内部类

在Java中，可以将一个类的定义放在另外一个类的定义内部，这就是内部类。内部类本身就是类的一个属性，与其他属性的定义方式一致。

- 静态内部类：定义在类内部的静态类

```java
public class Outer {
    private static int radius = 1;
    static class StaticInner {
        public void visit() {
        System.out.println("visit outer static variable:" + radius);
        }
    }
}
```
静态内部类可以访问外部类所有的静态变量，而不可访问外部类的非静态变量；静态内部类的创建方式，new 外部类.静态内部类()

```java
Outer.StaticInner inner = new Outer.StaticInner();
inner.visit();
```

- 成员内部类：定义在类内部，成员位置上的非静态类

```java
public class Outer {
    private static int radius = 1;
    private int count =2;	     
    class Inner {
        public void visit() {
        System.out.println("visit outer static variable:" + radius);
        System.out.println("visit outer variable:" + count);
        }
    }
}
```

成员内部类可以访问外部类所有的变量和方法，包括静态和非静态，私有和公有。成员内部类依赖于外部类的实例，它的创建方式 外部类实例.new 内部类()，如下：

```java
 Outer outer = new Outer();
 Outer.Inner inner = outer.new Inner();
 inner.visit();
```

- 局部内部类：定义在方法中的内部类

定义在实例方法中的局部类可以访问外部类的所有变量和方法，定义在静态方法中的局部类只能访问外部类的静态变量和方法。局部内部类的创建方式，在对应方法内，new 内部类()

```java
public class Outer {
    private  int out_a = 1;
    private static int STATIC_b = 2;
    public void testFunctionClass(){
        int inner_c =3;
        class Inner {
            private void fun(){
                System.out.println(out_a);
                System.out.println(STATIC_b);
                System.out.println(inner_c);
            }
        }
        Inner inner = new Inner();
        inner.fun();
    }
    public static void testStaticFunctionClass(){
        int d =3;
        class Inner {
            private void fun(){
                // System.out.println(out_a); 编译错误，定义在静态方法中的局部类不可以访问外部类的实例变量
                System.out.println(STATIC_b);
                System.out.println(d);
            }
        }
        Inner inner = new Inner();
        inner.fun();
    }
}
```

- 匿名内部类：没有名字的内部类

```java
public class Outer {
   private void test(final int i) {
   new Service() {
   public void method() {
        for (int j = 0; j < i; j++) {
              System.out.println("匿名内部类" );
              }
            }
        }.method();
    }
 }
 //匿名内部类必须继承或实现一个已有的接口
interface Service{
    void method();
}
```

##### == 和 equals()的区别？

== ：它的作用是判断两个对象的地址是否相等。即：判断两个对象是不是同一个对象。（基本数据类型 == 比较的是值，引用数据类型 == 比较的是内存地址）

equals() ：它的作用也是判断两个对象是否相等。但它一般有两种使用情况：

情况一：类没有覆盖equals()方法。则通过equals()比较该类的两个对象时，等价于通过“==”比较这两个对象。

情况二：类覆盖了equals()方法。一般，我们都覆盖equals()方法来判断两个对象的内容相等；若它们的内容相等，则返回true。

```java
public class test1 {
    public static void main(String[] args) {
    String a = new String("ab"); // a 为一个引用
    String b = new String("ab"); // b为另一个引用,对象的内容一样
    String aa = "ab"; // 放在常量池中
    String bb = "ab"; // 从常量池中查找
    if (aa == bb) // true
        System.out.println("aa==bb");
    if (a == b) // false，非同一对象
        System.out.println("a==b");
    if (a.equals(b)) // true
        System.out.println("aEQb");
    if (42 == 42.0) { // true
        System.out.println("true");
    }
  }
}
```

- String中的equals方法是被重写过的，因为object的equals方法是比较的对象的内存地址，而String的equals方法比较的是对象的值。
- 当创建String类型的对象时，虚拟机会在常量池中查找有没有已经存在的值和要创建的值相同的对象，如果有就把它赋给当前引用。如果没有就在常量池中重新创建一个String对象。

##### hashCode与equal()

hashCode() 的作用是获取哈希码，也称为散列码；它实际上是返回一个int整数。这个哈希码的作用是**确定该对象在哈希表中的索引位置**。hashCode() 定义在JDK的Object.java中，这就意味着Java中的任何类都包含有hashCode()函数。散列表存储的是键值对(key-value)，它的特点是：能根据“键”快速的检索出对应的“值”。这其中就利用到了散列码！（可以快速找到所需要的对象）

- 如果两个对象相等，则hashcode一定也是相同的

- 两个对象相等，对两个对象调用equals()方法返回true

- 两个对象有相同的hashcode值，它们也不一定是相等的

- 因此，equals 方法被覆盖过，则 hashCode 方法也必须被覆盖

- hashCode() 的默认行为是对堆上的对象产生独特值。如果没有重写 hashCode()，则该 class 的两个对象无论如何都不会相等（即使这两个对象指向相同的数据）

##### String str="i"与 String str=new String(“i”)

不一样，因为内存的分配方式不一样。String str="i"的方式，java 虚拟机会将其分配到常量池中；而 String str=new String(“i”) 则会被分到堆内存中。

##### StringBuffer 与 StringBuilder

<img src="https://gitee.com/sun-qiao321/picture/raw/master/images/clip_image001-1618908408161.png" style="zoom:50%;" />

##### Comparable 与 Comparator的区别

Comparable是排序接口。若一个类实现； Comparable接口，就意味着该类支持排序。实现了Comparable接口的类的对象列表或数组可以通过Collections.sort或Arrays.sort进行自动排序。

```Java
package java.lang;
import java.util.*;
public interface Comparable<T> {
	public int compareTo(T o);
}
```

Comparator是比较接口，如果我们需要控制某个类的次序，而该类本身不支持排序（即没有实现Comparable接口），那么我们可以建立一个“该类的比较器”来进行排序。

```java
package java.util;
public interface Comparator<T>{
	int compare(T o1, T o2);
	boolean equals(Object obj);
}
```

Comparable是排序接口，若一个类实现了Comparable接口，就意味着“该类支持排序”。而Comparator是比较器，我们若需要控制某个类的次序，可以建立一个“该类的比较器”来进行排序。

Comparable相当于“内部比较器”，而Comparator相当于“外部比较器”。
两种方法各有优劣， 用Comparable 简单， 只要实现Comparable 接口的对象直接就成为一个可以比较的对象，但是需要修改源代码。 用Comparator 的好处是不需要修改源代码， 而是另外实现一个比较器， 当某个自定义的对象需要作比较的时候，把比较器和对象一起传递过去就可以比大小了， 并且在Comparator 里面用户可以自己实现复杂的可以通用的逻辑，使其可以匹配一些比较简单的对象，那样就可以节省很多重复劳动了。



