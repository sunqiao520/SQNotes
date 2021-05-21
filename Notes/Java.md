## Java基础

##### Java、C++ 和 Matlab的区别

**Java 与 C++**

- 都是面向对象的语言，都支持封装、继承和多态
- Java 不提供指针来直接访问内存，程序内存更加安全
- Java 的类是单继承的，C++ 支持多重继承；虽然 Java 的类不可以多继承，但是接口可以多继承。
- Java 有自动内存管理垃圾回收机制(GC)，不需要程序员手动释放无用内存。
- C ++同时支持方法重载和操作符重载，但是 Java 只支持方法重载（操作符重载增加了复杂性，这与 Java 最初的设计思想不符）。

##### Java 为什么不用多继承

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
2. **静态代码块:** 静态代码块定义在类中方法外, 静态代码块在非静态代码块之前执行(静态代码块—>非静态代码块—>构造方法)。 该类不管创建多少对象，静态代码块只执行一次。
3. **静态内部类（static修饰类的话只能修饰内部类）：** 静态内部类与非静态内部类之间存在一个最大的区别: 非静态内部类在编译完成之后会隐含地保存着一个引用，该引用是指向创建它的外围类，但是静态内部类却没有。没有这个引用就意味着：1. 它的创建是不需要依赖外围类的创建。2. 它不能使用任何外围类的非static成员变量和方法。
4. **静态导包(用来导入类中的静态资源，1.5之后的新特性):** 格式为：`import static` 这两个关键字连用可以指定导入某个类中的指定静态资源，并且不需要使用类名调用类中静态成员，可以直接使用类中静态成员变量和成员方法。

#####  static存在的主要意义

static的主要意义是在于创建独立于具体对象的域变量或者方法。以致于即使没有创建对象，也能使用属性和调用方法！

static关键字还有一个比较关键的作用就是 用来形成静态代码块以优化程序性能。static块可以置于类中的任何地方，类中可以有多个static块。在类初次被加载的时候，会按照static块的顺序来执行每个static块，并且只会执行一次。

为什么说static块可以用来优化程序性能，是因为它的特性:只会在类加载的时候执行一次。因此，很多时候会将一些只需要进行一次的初始化操作都放在static代码块中进行。

##### 抽象类和接口

**相同点**

接口和抽象类都不能实例化

都位于继承的顶端，用于被其他实现或继承

都包含抽象方法，其子类都必须覆写这些抽象方法

**不同点**

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

hashCode() 的作用是获取哈希码，也称为散列码；它实际上是返回一个int整数。这个哈希码的作用是确定该对象在哈希表中的索引位置。hashCode() 定义在JDK的Object.java中，这就意味着Java中的任何类都包含有hashCode()函数。散列表存储的是键值对(key-value)，它的特点是：能根据“键”快速的检索出对应的“值”。这其中就利用到了散列码！（可以快速找到所需要的对象）

- 如果两个对象相等，则hashcode一定也是相同的

- 两个对象相等，对两个对象调用equals()方法返回true

- 两个对象有相同的hashcode值，它们也不一定是相等的

- 因此，equals 方法被覆盖过，则 hashCode 方法也必须被覆盖

- hashCode() 的默认行为是对堆上的对象产生独特值。如果没有重写 hashCode()，则该 class 的两个对象无论如何都不会相等（即使这两个对象指向相同的数据）

##### Collection 与 Map

**Collection**

<img src="https://gitee.com/sun-qiao321/picture/raw/master/images/image-20210420162553690.png" style="zoom: 67%;" />

Set

- TreeSet：基于红黑树实现，支持有序性操作，例如根据一个范围查找元素的操作。但是查找效率不如 HashSet，HashSet 查找的时间复杂度为 O(1)，TreeSet 则为 O(logN)。
- HashSet：基于哈希表实现，支持快速查找，但不支持有序性操作。并且失去了元素的插入顺序信息，也就是说使用 Iterator 遍历 HashSet 得到的结果是不确定的。
- LinkedHashSet：具有HashSet 的查找效率，并且内部使用双向链表维护元素的插入顺序。

 

List

- ArrayList：基于动态数组实现，支持随机访问。
- Vector：和 ArrayList 类似，但它是线程安全的。
- LinkedList：基于双向链表实现，只能顺序访问，但是可以快速地在链表中间插入和删除元素。不仅如此，LinkedList 还可以用作栈、队列和双向队列。

 add()添加至尾部 

 

Queue

- LinkedList：可以用它来实现双向队列。

offer() 添加至尾部 poll()  检索并删除头部

- PriorityQueue：基于堆结构实现，可以用它来实现优先队列

 

Map

<img src="https://gitee.com/sun-qiao321/picture/raw/master/images/clip_image002.png" style="zoom:67%;" />

 

- TreeMap：基于红黑树实现。
- HashMap：基于哈希表实现。
- HashTable：和  HashMap 类似，但它是线程安全的，这意味着同一时刻多个线程同时写入 HashTable     不会导致数据不一致。它是遗留类，不应该去使用它，而是使用 ConcurrentHashMap 来支持线程安全，ConcurrentHashMap 的效率会更高，因为 ConcurrentHashMap 引入了分段锁。
- LinkedHashMap：使用双向链表来维护元素的顺序，顺序为插入顺序或者最近最少使用（LRU）顺序。

##### ArrayList 和 Vector 的区别

- `ArrayList` 是 `List` 的主要实现类，底层使用 `Object[ ]`存储，适用于频繁的查找工作，线程不安全 ；
- `Vector` 是 `List` 的古老实现类，底层使用` Object[ ]` 存储，线程安全的。

##### ArrayList 和 LinkedList 的区别

1. **是否保证线程安全：** `ArrayList` 和 `LinkedList` 都是不同步的，也就是不保证线程安全；
2. **底层数据结构：** `Arraylist` 底层使用的是 **`Object` 数组**；`LinkedList` 底层使用的是 **双向链表** 数据结构（JDK1.6 之前为循环链表，JDK1.7 取消了循环。注意双向链表和双向循环链表的区别，下面有介绍到！）
3. **插入和删除是否受元素位置的影响：** ① **`ArrayList` 采用数组存储，所以插入和删除元素的时间复杂度受元素位置的影响。** 比如：执行`add(E e)`方法的时候， `ArrayList` 会默认在将指定的元素追加到此列表的末尾，这种情况时间复杂度就是 O(1)。但是如果要在指定位置 i 插入和删除元素的话（`add(int index, E element)`）时间复杂度就为 O(n-i)。因为在进行上述操作的时候集合中第 i 和第 i 个元素之后的(n-i)个元素都要执行向后位/向前移一位的操作。 ② **`LinkedList` 采用链表存储，所以对于`add(E e)`方法的插入，删除元素时间复杂度不受元素位置的影响，近似 O(1)，如果是要在指定位置`i`插入和删除元素的话（`(add(int index, E element)`） 时间复杂度近似为`o(n))`因为需要先移动到指定位置再插入。**
4. **是否支持快速随机访问：** `LinkedList` 不支持高效的随机元素访问，而 `ArrayList` 支持。快速随机访问就是通过元素的序号快速获取元素对象(对应于`get(int index)`方法)。
5. **内存空间占用：** ArrayList 的空间浪费主要体现在在 list 列表的结尾会预留一定的容量空间，而 LinkedList 的空间花费则体现在它的每一个元素都需要消耗比 ArrayList 更多的空间（因为要存放直接后继和直接前驱以及数据）。

##### HashSet 、LinkedHashSet 和 TreeSet的异同

`HashSet` 是 `Set` 接口的主要实现类 ，`HashSet` 的底层是 `HashMap`，线程不安全的，可以存储 null 值；

`LinkedHashSet` 是 `HashSet` 的子类，能够按照添加的顺序遍历；

`TreeSet` 底层使用红黑树，能够按照添加元素的顺序进行遍历，排序的方式有自然排序和定制排序。

##### HashTable和HashMap的区别

1. **线程是否安全：** `HashMap` 是非线程安全的，`HashTable` 是线程安全的,因为 `HashTable` 内部的方法基本都经过`synchronized` 修饰。（如果你要保证线程安全的话就使用 `ConcurrentHashMap` 吧！）；
2. **效率：** 因为线程安全的问题，`HashMap` 要比 `HashTable` 效率高一点。另外，`HashTable` 基本被淘汰，不要在代码中使用它；
3. **对 Null key 和 Null value 的支持：** `HashMap` 可以存储 null 的 key 和 value，但 null 作为键只能有一个，null 作为值可以有多个；HashTable 不允许有 null 键和 null 值，否则会抛出 `NullPointerException`。
4. **初始容量大小和每次扩充容量大小的不同 ：** ① 创建时如果不指定容量初始值，`Hashtable` 默认的初始大小为 11，之后每次扩充，容量变为原来的 2n+1。`HashMap` 默认的初始化大小为 16。之后每次扩充，容量变为原来的 2 倍。② 创建时如果给定了容量初始值，那么 Hashtable 会直接使用你给定的大小，而 `HashMap` 会将其扩充为 2 的幂次方大小（`HashMap` 中的`tableSizeFor()`方法保证，下面给出了源代码）。也就是说 `HashMap` 总是使用 2 的幂作为哈希表的大小,后面会介绍到为什么是 2 的幂次方。
5. **底层数据结构：** JDK1.8 以后的 `HashMap` 在解决哈希冲突时有了较大的变化，当链表长度大于阈值（默认为 8）（将链表转换成红黑树前会判断，如果当前数组的长度小于 64，那么会选择先进行数组扩容，而不是转换为红黑树）时，将链表转化为红黑树，以减少搜索时间。Hashtable 没有这样的机制。

##### HashMap和TreeMap 区别

区别：

①定义

先看HashMap的定义：

public class HashMap<K,V> extends AbstractMap<K,V>
   implements Map<K,V>, Cloneable, Serializable

再看TreeMap的定义：

public class TreeMap<K,V>
   extends AbstractMap<K,V>
   implements NavigableMap<K,V>, Cloneable, java.io.Serializable

从类的定义来看，HashMap和TreeMap都继承自AbstractMap，不同的是HashMap实现的是Map接口，而TreeMap实现的是NavigableMap接口。NavigableMap是SortedMap的一种，实现了对Map中key的排序。

②排序区别

TreeMap输出是排好序的，而HashMap不是。

③性能区别

HashMap的底层是Array，所以HashMap在添加，查找，删除等方法上面速度会非常快。而TreeMap的底层是一个Tree结构，所以速度会比较慢。

另外HashMap因为要保存一个Array，所以会造成空间的浪费，而TreeMap只保存要保持的节点，所以占用的空间比较小。

HashMap如果出现hash冲突的话，效率会变差，不过在java 8进行TreeNode转换之后，效率有很大的提升。

TreeMap在添加和删除节点的时候会进行重排序，会对性能有所影响。

相同点：两者都不允许重复的Key，两者都不是线程安全的。

##### HashMap 的底层实现

JDK1.8 之前 `HashMap` 底层是 **数组和链表** 结合在一起使用也就是 **链表散列**。**HashMap 通过 key 的 hashCode 经过扰动函数处理过后得到 hash 值，然后通过 (n - 1) & hash 判断当前元素存放的位置（这里的 n 指的是数组的长度），如果当前位置存在元素的话，就判断该元素与要存入的元素的 hash 值以及 key 是否相同，如果相同的话，直接覆盖，不相同就通过拉链法解决冲突。**

**所谓扰动函数指的就是 HashMap 的 hash 方法。使用 hash 方法也就是扰动函数是为了防止一些实现比较差的 hashCode() 方法 换句话说使用扰动函数之后可以减少碰撞。**

相比于之前的版本， JDK1.8 之后在解决哈希冲突时有了较大的变化，当链表长度大于阈值（默认为 8）（将链表转换成红黑树前会判断，如果当前数组的长度小于 64，那么会选择先进行数组扩容，而不是转换为红黑树）时，将链表转化为红黑树，以减少搜索时间。

##### 给一个k 查找值的过程中调用了哪些函数

```java
public V get(Object key) {
    Node<K,V> e;
    return (e = getNode(hash(key), key)) == null ? null : e.value;
}
```

一、首先调用hashcode（）

```java
static final int hash(Object key) {
    int h;
    return (key == null) ? 0 : (h = key.hashCode()) ^ (h >>> 16);
}
```

二、经过hash() 得到hash值后

三、调用equals()  返回值

```java
final Node<K,V> getNode(int hash, Object key) {
    Node<K,V>[] tab; Node<K,V> first, e; int n; K k;
    if ((tab = table) != null && (n = tab.length) > 0 &&
        (first = tab[(n - 1) & hash]) != null) {
        if (first.hash == hash && // always check first node
            ((k = first.key) == key || (key != null && key.equals(k))))
            return first;
        if ((e = first.next) != null) {
            if (first instanceof TreeNode)
                return ((TreeNode<K,V>)first).getTreeNode(hash, key);
            do {
                if (e.hash == hash &&
                    ((k = e.key) == key || (key != null && key.equals(k))))
                    return e;
            } while ((e = e.next) != null);
        }
    }
    return null;
}
```

首先计算key的hashcode，根据hash算法找到数组中对应位置的某一元素，然后通过key的equals方法在对应位置的链表中找到需要的元素。所以，hashcode与equals方法对于找到对应元素是两个关键方法。

Hashmap的key可以是任何类型的对象，例如User这种对象，为了保证两个具有相同属性的user的hashcode相同，我们就需要改写hashcode方法，比方把hashcode值的计算与User对象的id关联起来，那么只要user对象拥有相同id，那么他们的hashcode也能保持一致了，这样就可以找到在hashmap数组中的位置了。如果这个位置上有多个元素，还需要用key的equals方法在对应位置的链表中找到需要的元素，所以只改写了hashcode方法是不够的，equals方法也是需要改写滴~当然啦，按正常思维逻辑，equals方法一般都会根据实际的业务内容来定义，例如根据user对象的id来判断两个user是否相等。

##### HashMap 的长度为什么是2 的幂次方

为了能让 HashMap 存取高效，尽量较少碰撞，也就是要尽量把数据分配均匀。我们上面也讲到了过了，Hash 值的范围值-2147483648 到 2147483647，前后加起来大概 40 亿的映射空间，只要哈希函数映射得比较均匀松散，一般应用是很难出现碰撞的。但问题是一个 40 亿长度的数组，内存是放不下的。所以这个散列值是不能直接拿来用的。用之前还要先做对数组的长度取模运算，得到的余数才能用来要存放的位置也就是对应的数组下标。这个数组下标的计算方法是“ `(n - 1) & hash`”。（n 代表数组长度）。这也就解释了 HashMap 的长度为什么是 2 的幂次方。

**这个算法应该如何设计呢？**

我们首先可能会想到采用%取余的操作来实现。但是，重点来了：**“取余(%)操作中如果除数是 2 的幂次则等价于与其除数减一的与(&)操作（也就是说 hash%length==hash&(length-1)的前提是 length 是 2 的 n 次方；）。”** 并且 **采用二进制位操作 &，相对于%能够提高运算效率，这就解释了 HashMap 的长度为什么是 2 的幂次方。**

##### 同步容器与并发容器

- 同步容器：Vector、Stack、HashTable

- 并发容器：

ConcurrentHashMap：线程安全的HashMap的实现

CopyOnWriteArrayList：线程安全且在读操作时无锁的ArrayList

CopyOnWriteArraySet：基于CopyOnWriteArrayList，不添加重复元素

ArrayBlockingQueue：基于数组、先进先出、线程安全，可实现指定时间的阻塞读写，并且容量可以限制

LinkedBlockingQueue：基于链表实现，读写各用一把锁，在高并发读写操作都多的情况下，性能优于ArrayBlockingQueue

##### HashMap 多线程操作导致死循环问题

主要原因在于并发下的 Rehash 会造成元素之间会形成一个循环链表。不过，jdk 1.8 后解决了这个问题，但是还是不建议在多线程下使用 HashMap,因为多线程下使用 HashMap 还是会存在其他问题比如数据丢失。并发环境下推荐使用 ConcurrentHashMap 。

详情请查看：https://coolshell.cn/articles/9606.html

##### HashMap 有哪几种常见的遍历方式?

1. 使用迭代器（Iterator）EntrySet 的方式进行遍历；

   ```java
   public class HashMapTest {
       public static void main(String[] args) {
           // 创建并赋值 HashMap
           Map<Integer, String> map = new HashMap();
           map.put(1, "Java");
           map.put(2, "JDK");
           map.put(3, "Spring Framework");
           map.put(4, "MyBatis framework");
           map.put(5, "Java中文社群");
           // 遍历
           Iterator<Map.Entry<Integer, String>> iterator = map.entrySet().iterator();
           while (iterator.hasNext()) {
               Map.Entry<Integer, String> entry = iterator.next();
               System.out.println(entry.getKey());
               System.out.println(entry.getValue());
           }
       }
   }
   ```

2. 使用迭代器（Iterator）KeySet 的方式进行遍历；

   ```java
   public class HashMapTest {
       public static void main(String[] args) {
           // 创建并赋值 HashMap
           Map<Integer, String> map = new HashMap();
           map.put(1, "Java");
           map.put(2, "JDK");
           map.put(3, "Spring Framework");
           map.put(4, "MyBatis framework");
           map.put(5, "Java中文社群");
           // 遍历
           Iterator<Integer> iterator = map.keySet().iterator();
           while (iterator.hasNext()) {
               Integer key = iterator.next();
               System.out.println(key);
               System.out.println(map.get(key));
           }
       }
   }
   ```

3. 使用 For Each EntrySet 的方式进行遍历；

   ```java
   public class HashMapTest {
       public static void main(String[] args) {
           // 创建并赋值 HashMap
           Map<Integer, String> map = new HashMap();
           map.put(1, "Java");
           map.put(2, "JDK");
           map.put(3, "Spring Framework");
           map.put(4, "MyBatis framework");
           map.put(5, "Java中文社群");
           // 遍历
           for (Map.Entry<Integer, String> entry : map.entrySet()) {
               System.out.println(entry.getKey());
               System.out.println(entry.getValue());
           }
       }
   }
   ```

   

4. 使用 For Each KeySet 的方式进行遍历；

   ```java
   public class HashMapTest {
       public static void main(String[] args) {
           // 创建并赋值 HashMap
           Map<Integer, String> map = new HashMap();
           map.put(1, "Java");
           map.put(2, "JDK");
           map.put(3, "Spring Framework");
           map.put(4, "MyBatis framework");
           map.put(5, "Java中文社群");
           // 遍历
           for (Integer key : map.keySet()) {
               System.out.println(key);
               System.out.println(map.get(key));
           }
       }
   }
   ```

   

5. 使用 Lambda 表达式的方式进行遍历；

   ```java
   public class HashMapTest {
       public static void main(String[] args) {
           // 创建并赋值 HashMap
           Map<Integer, String> map = new HashMap();
           map.put(1, "Java");
           map.put(2, "JDK");
           map.put(3, "Spring Framework");
           map.put(4, "MyBatis framework");
           map.put(5, "Java中文社群");
           // 遍历
           map.forEach((key, value) -> {
               System.out.println(key);
               System.out.println(value);
           });
       }
   }
   ```

   

6. 使用 Streams API 单线程的方式进行遍历；

   ```java
   public class HashMapTest {
       public static void main(String[] args) {
           // 创建并赋值 HashMap
           Map<Integer, String> map = new HashMap();
           map.put(1, "Java");
           map.put(2, "JDK");
           map.put(3, "Spring Framework");
           map.put(4, "MyBatis framework");
           map.put(5, "Java中文社群");
           // 遍历
           map.entrySet().stream().forEach((entry) -> {
               System.out.println(entry.getKey());
               System.out.println(entry.getValue());
           });
       }
   }
   ```

   

7. 使用 Streams API 多线程的方式进行遍历。

   ```java
   public class HashMapTest {
       public static void main(String[] args) {
           // 创建并赋值 HashMap
           Map<Integer, String> map = new HashMap();
           map.put(1, "Java");
           map.put(2, "JDK");
           map.put(3, "Spring Framework");
           map.put(4, "MyBatis framework");
           map.put(5, "Java中文社群");
           // 遍历
           map.entrySet().parallelStream().forEach((entry) -> {
               System.out.println(entry.getKey());
               System.out.println(entry.getValue());
           });
       }
   }
   ```

`EntrySet` 之所以比 `KeySet` 的性能高是因为，`KeySet` 在循环时使用了 `map.get(key)`，而 `map.get(key)` 相当于又遍历了一遍 Map 集合去查询 `key` 所对应的值。为什么要用“又”这个词？那是因为**在使用迭代器或者 for 循环时，其实已经遍历了一遍 Map 集合了，因此再使用 `map.get(key)` 查询时，相当于遍历了两遍**。

而 `EntrySet` 只遍历了一遍 Map 集合，之后通过代码“Entry<Integer, String> entry = iterator.next()”把对象的 `key` 和 `value` 值都放入到了 `Entry` 对象中，因此再获取 `key` 和 `value` 值时就无需再遍历 Map 集合，只需要从 `Entry` 对象中取值就可以了。

所以，**`EntrySet` 的性能比 `KeySet` 的性能高出了一倍，因为 `KeySet` 相当于循环了两遍 Map 集合，而 `EntrySet` 只循环了一遍**。

##### ConcurrentHashMap 和 Hashtable 的区别

`ConcurrentHashMap` 和 `Hashtable` 的区别主要体现在实现线程安全的方式上不同。

- **底层数据结构：** JDK1.7 的 `ConcurrentHashMap` 底层采用 **分段的数组+链表** 实现，JDK1.8 采用的数据结构跟 `HashMap1.8` 的结构一样，数组+链表/红黑二叉树。`Hashtable` 和 JDK1.8 之前的 `HashMap` 的底层数据结构类似都是采用 **数组+链表** 的形式，数组是 HashMap 的主体，链表则是主要为了解决哈希冲突而存在的；
- **实现线程安全的方式（重要）：** ① **在 JDK1.7 的时候，`ConcurrentHashMap`（分段锁）** 对整个桶数组进行了分割分段(`Segment`)，每一把锁只锁容器其中一部分数据，多线程访问容器里不同数据段的数据，就不会存在锁竞争，提高并发访问率。 **到了 JDK1.8 的时候已经摒弃了 `Segment` 的概念，而是直接用 `Node` 数组+链表+红黑树的数据结构来实现，并发控制使用 `synchronized` 和 CAS 来操作。（JDK1.6 以后 对 `synchronized` 锁做了很多优化）** 整个看起来就像是优化过且线程安全的 `HashMap`，虽然在 JDK1.8 中还能看到 `Segment` 的数据结构，但是已经简化了属性，只是为了兼容旧版本；② **`Hashtable`(同一把锁)** :使用 `synchronized` 来保证线程安全，效率非常低下。当一个线程访问同步方法时，其他线程也访问同步方法，可能会进入阻塞或轮询状态，如使用 put 添加元素，另一个线程不能使用 put 添加元素，也不能使用 get，竞争会越来越激烈效率越低。

**两者的对比图：**

**HashTable:**

<img src="https://gitee.com/sun-qiao321/picture/raw/master/images/20210424184954.png" style="zoom: 33%;" />

**JDK1.7 的 ConcurrentHashMap：**

<img src="https://gitee.com/sun-qiao321/picture/raw/master/images/20210424185025.png" style="zoom:33%;" />

**JDK1.8 的 ConcurrentHashMap：**

<img src="https://gitee.com/sun-qiao321/picture/raw/master/images/20210424185051.png" style="zoom:33%;" />

JDK1.8 的 `ConcurrentHashMap` 不在是 **Segment 数组 + HashEntry 数组 + 链表**，而是 **Node 数组 + 链表 / 红黑树**。不过，Node 只能用于链表的情况，红黑树的情况需要使用 **`TreeNode`**。当冲突链表达到一定长度时，链表会转换成红黑树。

##### ConcurrentHashMap 线程安全的具体实现方式/底层具体实现

**JDK1.7（上面有示意图）**

首先将数据分为一段一段的存储，然后给每一段数据配一把锁，当一个线程占用锁访问其中一个段数据时，其他段的数据也能被其他线程访问。

**`ConcurrentHashMap` 是由 `Segment` 数组结构和 `HashEntry` 数组结构组成**。

Segment 实现了 `ReentrantLock`,所以 `Segment` 是一种可重入锁，扮演锁的角色。`HashEntry` 用于存储键值对数据。

```
static class Segment<K,V> extends ReentrantLock implements Serializable {
}
```

一个 `ConcurrentHashMap` 里包含一个 `Segment` 数组。`Segment` 的结构和 `HashMap` 类似，是一种数组和链表结构，一个 `Segment` 包含一个 `HashEntry` 数组，每个 `HashEntry` 是一个链表结构的元素，每个 `Segment` 守护着一个 `HashEntry` 数组里的元素，当对 `HashEntry` 数组的数据进行修改时，必须首先获得对应的 `Segment` 的锁。

 **JDK1.8 （上面有示意图）**

`ConcurrentHashMap` 取消了 `Segment` 分段锁，采用 CAS 和 `synchronized` 来保证并发安全。数据结构跟 HashMap1.8 的结构类似，数组+链表/红黑二叉树。Java 8 在链表长度超过一定阈值（8）时将链表（寻址时间复杂度为 O(N)）转换为红黑树（寻址时间复杂度为 O(log(N))）

`synchronized` 只锁定当前链表或红黑二叉树的首节点，这样只要 hash 不冲突，就不会产生并发，效率又提升 N 倍。

##### 优先队列priorityQueue

Java中通过二叉小顶堆实现，

**优先队列的作用是能保证每次取出的元素都是队列中权值最小的**（Java的优先队列每次取最小元素，C++的优先队列每次取最大元素）。这里牵涉到了大小关系，**元素大小的评判可以通过元素本身的自然顺序（*natural ordering*），也可以通过构造时传入的比较器**（*Comparator*，类似于C++的仿函数）。

添加offer()  删除remove() or poll()

##### map集合通过value值排序的问题

①桶排序

桶的下表表示频率，即value，桶内存储 key，从后往前遍历桶中元素即按大到小输出。

②通过将键和值传到对象中，对对象排序

```java
class Duilie {
    public char c;
    public int score;
    public Duilie (char c,int score){
        this.c = c;
        this.score = score;
    }
    public String toString(){
        return c+" "+score;
    }
}

   Duilie[] Dui = new Duilie[map.size()];
    int i = 0;
    for(Map.Entry<Character,Integer> set : map.entrySet()){
        Dui[i] = new Duilie(set.getKey(),set.getValue());
        i++;
    }
    Arrays.sort(Dui,new Comparator<Duilie>(){
        public int compare(Duilie o1,Duilie o2){
            return o2.score - o1.score;
        }
    });
 
```

③通过map.entrySet()

```java
public class mapValueSort {
    public static void main(String[] args) {
        // 创建并赋值 HashMap
        Map<String,Integer> map = new HashMap();
        map.put("A",7);
        map.put("B",4);
        map.put("C",5);
        map.put("D",2);
        map.put("E",9);
        Comparator<Map.Entry<String,Integer>> valueComparator = new Comparator<Map.Entry<String, Integer>>() {
            @Override
            public int compare(Map.Entry<String, Integer> o1, Map.Entry<String, Integer> o2) {
                return o2.getValue()-o1.getValue();    //降序排列
            }
        };
        List<Map.Entry<String,Integer>> list = new ArrayList<>(map.entrySet());
        list.sort(valueComparator);
        for(Map.Entry<String,Integer> entry : list){
            System.out.println(entry.getKey() +" "+entry.getValue());
        }
    }
}
```

##### [Arrays.asList()的使用](https://github.com/Snailclimb/JavaGuide/blob/master/docs/java/basis/Java基础知识疑难点.md)

将一个数组转化为一个List集合

```java
String[] myArray = {"Apple", "Banana", "Orange"};
List<String> myList = Arrays.asList(myArray);
//上面两个语句等价于下面一条语句
List<String> myList = Arrays.asList("Apple","Banana", "Orange");
```

使用注意事项
①传递的数组必须是对象数组，而不是基本类型（需要包装）
②使用集合的修改方法：add()  remove()  clear() 会抛出异常。
Arrays.asList() 方法返回的并不是 java.util.ArrayList ，而是 java.util.Arrays 的一个内部类,这个内部类并没有实现集合的修改方法或者说并没有重写这些方法。

如何正确的将数组转换为ArrayList呢？
①手动实现方法

```java
static <T> List<T> arrayToList(final T[] array) {
    final List<T> l = new ArrayList<T>(array.length);
    for (final T s : array) {
	    l.add(s);
 	}
 	return l;
}
```

②最简单方法

```java
List list = new ArrayList<>(Arrays.asList("a","b","c"));
```

③使用java8的Stream

```java
Integer [] myArray = { 1, 2, 3 };
List myList = Arrays.stream(myArray).collect(Collectors.toList());
//基本类型也可以实现转换（依赖boxed的装箱操作）
int [] myArray2 = { 1, 2, 3 };
List myList = Arrays.stream(myArray2).boxed().collect(Collectors.toList());
```

④使用java9的List.of()方法

```java
Integer[] array = {1, 2, 3};
List<Integer> list = List.of(array);
System.out.println(list); /* [1, 2, 3]  不支持基本数据类型 */
```

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





### IO流

java中的IO流分为几种？

按照流的流向分，可以分为输入流和输出流；

按照操作单元划分，可以划分为字节流和字符流；

按照流的角色划分为节点流和处理流。

Java Io流共涉及40多个类，这些类看上去很杂乱，但实际上很有规则，而且彼此之间存在非常紧密的联系， Java I0流的40多个类都是从如下4个抽象类基类中派生出来的。

 

InputStream/Reader: 所有的输入流的基类，前者是字节输入流，后者是字符输入流。

OutputStream/Writer: 所有输出流的基类，前者是字节输出流，后者是字符输出流。

- 按操作方式分类：

<img src="https://gitee.com/sun-qiao321/picture/raw/master/images/image-20210420163844146.png" style="zoom:50%;" />

- 按操作对象分：

<img src="https://gitee.com/sun-qiao321/picture/raw/master/images/clip_image001-1618907878319.jpg" style="zoom:67%;" />

##### Java中3中常见IO模型

###### BIO（Blocking I/O）

BIO属于同步阻塞模型，应用程序发起read调用后，会一直阻塞，直到内核把数据拷贝到用户空间。

<img src="https://gitee.com/sun-qiao321/picture/raw/master/images/20210424194100.png" style="zoom:50%;" />

在客户端连接数量不高的情况下，是没问题的。但是，当面对十万甚至百万级连接的时候，传统的 BIO 模型是无能为力的。因此，我们需要一种更高效的 I/O 处理模型来应对更高的并发量。

###### NIO（Non-blocking/New I/O）

Java 中的 NIO 于 Java 1.4 中引入，对应 `java.nio` 包，提供了 `Channel` , `Selector`，`Buffer` 等抽象。NIO 中的 N 可以理解为 Non-blocking，不单纯是 New。它支持面向缓冲的，基于通道的 I/O 操作方法。 对于高负载、高并发的（网络）应用，应使用 NIO 。

Java 中的 NIO 可以看作是 **I/O 多路复用模型**。也有很多人认为，Java 中的 NIO 属于同步非阻塞 IO 模型。

<img src="https://gitee.com/sun-qiao321/picture/raw/master/images/20210424194422.png" style="zoom:50%;" />

同步非阻塞 IO 模型中，应用程序会一直发起 read 调用，等待数据从内核空间拷贝到用户空间的这段时间里，线程依然是阻塞的，直到在内核把数据拷贝到用户空间。

相比于同步阻塞 IO 模型，同步非阻塞 IO 模型确实有了很大改进。通过轮询操作，避免了一直阻塞。

但是，这种 IO 模型同样存在问题：**应用程序不断进行 I/O 系统调用轮询数据是否已经准备好的过程是十分消耗 CPU 资源的。**

<img src="https://gitee.com/sun-qiao321/picture/raw/master/images/20210424194521.png" style="zoom:50%;" />

IO 多路复用模型中，线程首先发起 select 调用，询问内核数据是否准备就绪，等内核把数据准备好了，用户线程再发起 read 调用。read 调用的过程（数据从内核空间->用户空间）还是阻塞的。

> 目前支持 IO 多路复用的系统调用，有 select，epoll 等等。select 系统调用，是目前几乎在所有的操作系统上都有支持
>
> - **select 调用** ：内核提供的系统调用，它支持一次查询多个系统调用的可用状态。几乎所有的操作系统都支持。
> - **epoll 调用** ：linux 2.6 内核，属于 select 调用的增强版本，优化了 IO 的执行效率。

**IO 多路复用模型，通过减少无效的系统调用，减少了对 CPU 资源的消耗。**

Java 中的 NIO ，有一个非常重要的**选择器 ( Selector )** 的概念，也可以被称为 **多路复用器**。通过它，只需要一个线程便可以管理多个客户端连接。当客户端数据到了之后，才会为其服务。

###### AIO（Asynchronous I/O）

异步I/O模型

异步 IO 是基于事件和回调机制实现的，也就是应用操作之后会直接返回，不会堵塞在那里，当后台处理完成，操作系统会通知相应的线程进行后续的操作。

<img src="https://gitee.com/sun-qiao321/picture/raw/master/images/20210424194717.png" style="zoom:50%;" />

### 反射

##### 什么是反射机制？

JAVA反射机制是在运行状态中，对于任意一个类，都能够知道这个类的所有属性和方法；对于任意一个对象，都能够调用它的任意一个方法和属性；这种动态获取的信息以及动态调用对象的方法的功能称为java语言的反射机制。

 

静态编译和动态编译

 

静态编译：在编译时确定类型，绑定对象

动态编译：运行时确定类型，绑定对象

反射机制优缺点

 

优点： 运行期类型的判断，动态加载类，提高代码灵活度。

缺点： 性能瓶颈：反射相当于一系列解释操作，通知 JVM 要做的事情，性能比直接的java代码要慢很多。

##### Java获取反射的四种方法

- 通过new对象实现反射机制

- 通过路径实现反射机制

- 通过类名实现反射机制

- 通过类加载器ClassLoader.loadClass()传入类路径获取

  ```java
  public class Student {
      private int id;
      String name;
      protected boolean sex;
      public float score;
  }
	
	public class Get {
	      //获取反射机制四种方式
	    public static void main(String[] args) throws ClassNotFoundException {
	          //方式一(通过对象实例)
	        Student stu = new Student();
	        Class classobj1 = stu.getClass();
	        System.out.println(classobj1.getName());
	        //方式二（所在通过路径-相对路径）
	        Class classobj2 = Class.forName("fanshe.Student");
	        System.out.println(classobj2.getName());
	        //方式三（通过类名）
	        Class classobj3 = Student.class;
	        System.out.println(classobj3.getName());
	        //方式四 （类加载器）
	        Class classobj4 = ClassLoader.LoadClass("fanshe.Student");
	    }
	} 
	```

## Java多线程

##### 死锁与产生的必要条件

死锁：是指两个或两个以上的进程（或线程）在执行过程中，因争夺资源而造成的一种互相等待的现象，若无外力作用，它们都将无法推进下去。

产生死锁的必要条件：

①互斥条件：所谓互斥就是进程在某一时间内独占资源。

②请求与保持条件：一个进程因请求资源而阻塞时，对已获得的资源保持不放。

③不剥夺条件：进程已获得资源，在未使用完之前，不能强行剥夺。

④循环等待条件：若干进程之间形成一种头尾相接的循环等待资源。

##### 活锁

任务失败或者执行者没有被阻塞，由于某些条件没有满足，导致一直重复尝试，失败，尝试，失败。

 

死锁与活锁的区别：处于活锁的实体是在不断的改变状态，所谓的“活”，而处于死锁的实体表现为等待；活锁有可能自行解开，死锁则不能。

##### 饥饿

一个或多个线程因为种种原因无法获得所需要的资源，导致一直无法执行的状态。

导致饥饿的原因：

①高优先级线程吞噬所有低优先级线程的CPU时间。

②线程被永久阻塞在一个等待进入同步块的状态，因为其他线程总是能在它之前持续地对该同步块进行访问。

③线程在等待一个本身也处于永久等待完成的对象（比如调用这个对象的wait（）方法），因为其他线程总是被持续地获得唤醒。

##### wait() 与 sleep() 的区别？

sleep()是使线程暂停执行一段时间的方法。wait()也是一种使线程暂停执行的方法。二者区别为：

- **原理不同**

 sleep()方法是Thread类的静态方法，是线程用来控制自身流程的，它会使此线程暂停执行一段时间，而把执行机会让给其他线程，等到计时时间一到，此线程会自动苏醒。而wait()方法是Object类的方法，用于线程间的通信，这个方法会使当前拥有该对象锁的进程等待，直到其他线程用调用notify()或notifyAll()时才苏醒过来，开发人员也可以给它指定一个时间使其自动醒来。

- **对锁的处理机制不同**

 由于sleep()方法的主要作用是让线程暂停一段时间，时间一到则自动恢复，不涉及线程间的通信，因此调用sleep()方法并不会释放锁。而wait()方法则不同，当调用wait()方法后，线程会释放掉它所占用的锁，从而使线程所在对象中的其他synchronized数据可被别的线程使用。

- **使用区域不同**

 wait()方法必须放在同步控制方法或者同步语句块中使用，而sleep方法则可以放在任何地方使用。

 sleep()方法必须捕获异常，而wait()、notify()、notifyAll()不需要捕获异常。在sleep的过程中，有可能被其他对象调用它的interrupt()，产生InterruptedException异常。



 由于sleep不会释放锁标志，容易导致死锁问题的发生，一般情况下，不推荐使用sleep()方法，而推荐使用wait()方法。

##### Java内存模型JMM





##### [ThreadLocal](https://mp.weixin.qq.com/s/DMj4wY2Z30MylAt9QJ5pUg)

主要用来做线程变量的隔离。

程序再处理用户请求的时候，通常后端服务器是有一个线程池，来一个请求就交给一个线程来处理，那为了防止多线程并发处理请求的时候发生串数据，比如AB线程分别处理



##### BlockingQueue的原理与实现

**阻塞队列 (BlockingQueue)**是Java util.concurrent包下重要的数据结构，BlockingQueue提供了线程安全的队列访问方式：当阻塞队列进行插入数据时，如果队列已满，线程将会阻塞等待直到队列非满；从阻塞队列取数据时，如果队列已空，线程将会阻塞等待直到队列非空。并发包下很多高级同步类的实现都是基于BlockingQueue实现的。

*BlockingQueue 的操作方法*
BlockingQueue 具有 4 组不同的方法用于插入、移除以及对队列中的元素进行检查。如果请求的操作不能得到立即执行的话，每个方法的表现也不同。这些方法如下：

![](https://gitee.com/sun-qiao321/picture/raw/master/images/clip_image001-1618908783219.png)

**四组不同的行为方式解释：**
• 抛异常：如果试图的操作无法立即执行，抛一个异常。
• 特定值：如果试图的操作无法立即执行，返回一个特定的值(常常是 true / false)。
• 阻塞：如果试图的操作无法立即执行，该方法调用将会发生阻塞，直到能够执行。
• 超时：如果试图的操作无法立即执行，该方法调用将会发生阻塞，直到能够执行，但等待时间不会超过给定值。返回一个特定值以告知该操作是否成功(典型的是true / false)。
无法向一个 BlockingQueue 中插入 null。如果你试图插入 null，BlockingQueue 将会抛出一个 NullPointerException。
可以访问到 BlockingQueue 中的所有元素，而不仅仅是开始和结束的元素。比如说，你将一个对象放入队列之中以等待处理，但你的应用想要将其取消掉。那么你可以调用诸如 remove(o) 方法来将队列之中的特定对象进行移除。但是这么干效率并不高(译者注：基于队列的数据结构，获取除开始或结束位置的其他对象的效率不会太高)，因此你尽量不要用这一类的方法，除非你确实不得不那么做。
**BlockingQueue 的实现类**
BlockingQueue 是个接口，你需要使用它的实现之一来使用BlockingQueue，Java.util.concurrent包下具有以下 BlockingQueue 接口的实现类：
• **ArrayBlockingQueue**：ArrayBlockingQueue 是一个有界的阻塞队列，其内部实现是将对象放到一个数组里。有界也就意味着，它不能够存储无限多数量的元素。它有一个同一时间能够存储元素数量的上限。你可以在对其初始化的时候设定这个上限，但之后就无法对这个上限进行修改了(译者注：因为它是基于数组实现的，也就具有数组的特性：一旦初始化，大小就无法修改)。
• **DelayQueue**：DelayQueue 对元素进行持有直到一个特定的延迟到期。注入其中的元素必须实现 java.util.concurrent.Delayed 接口。
• **LinkedBlockingQueue**：LinkedBlockingQueue 内部以一个链式结构(链接节点)对其元素进行存储。如果需要的话，这一链式结构可以选择一个上限。如果没有定义上限，将使用 Integer.MAX_VALUE 作为上限。
• **PriorityBlockingQueue**：PriorityBlockingQueue 是一个无界的并发队列。它使用了和类 java.util.PriorityQueue 一样的排序规则。你无法向这个队列中插入 null 值。所有插入到 PriorityBlockingQueue 的元素必须实现 java.lang.Comparable 接口。因此该队列中元素的排序就取决于你自己的 Comparable 实现。
• **SynchronousQueue**：SynchronousQueue 是一个特殊的队列，它的内部同时只能够容纳单个元素。如果该队列已有一元素的话，试图向队列中插入一个新元素的线程将会阻塞，直到另一个线程将该元素从队列中抽走。同样，如果该队列为空，试图向队列中抽取一个元素的线程将会阻塞，直到另一个线程向队列中插入了一条新的元素。据此，把这个类称作一个队列显然是夸大其词了。它更多像是一个汇合点。
使用例子：
阻塞队列的最长使用的例子就是生产者消费者模式,也是各种实现生产者消费者模式方式中首选的方式。使用者不用关心什么阻塞生产，什么时候阻塞消费，使用非常方便，代码如下：

```java
package MyThread; 
import java.util.Random;
import java.util.concurrent.BlockingQueue;
import java.util.concurrent.LinkedBlockingQueue;
import java.util.concurrent.TimeUnit; 

public class BlockingQueueTest {
	  //生产者
    public static class Producer implements Runnable{
    private final BlockingQueue<Integer> blockingQueue;
    private volatile boolean flag;
    private Random random; 
    public Producer(BlockingQueue<Integer> blockingQueue) {
        this.blockingQueue = blockingQueue;
        flag=false;
        random=new Random();	 
    }
        
    public void run() {
       while(!flag){
           int info=random.nextInt(100);
           try {
	          blockingQueue.put(info);
	          System.out.println(Thread.currentThread().getName()+" produce "+info);
	          Thread.sleep(50);
	        } catch (InterruptedException e) {
	          // TODO Auto-generated catch block
	          e.printStackTrace();
	        }        
	   }
     }
        
	 public void shutDown(){
	      flag=true;
	    }
     }
	  //消费者
    public static class Consumer implements Runnable{
	    private final BlockingQueue<Integer> blockingQueue;
	    private volatile boolean flag;
	    public Consumer(BlockingQueue<Integer> blockingQueue) {
	      this.blockingQueue = blockingQueue;
	    }
    public void run() {
	      while(!flag){
47	        int info;
48	        try {
49	          info = blockingQueue.take();
50	          System.out.println(Thread.currentThread().getName()+" consumer "+info);
51	          Thread.sleep(50);
52	        } catch (InterruptedException e) {
53	          // TODO Auto-generated catch block
54	          e.printStackTrace();
55	        }        
56	      }
57	    }
58	    public void shutDown(){
59	      flag=true;
60	    }
61	  }
62	  public static void main(String[] args){
63	    BlockingQueue<Integer> blockingQueue = new LinkedBlockingQueue<Integer>(10);
64	    Producer producer=new Producer(blockingQueue);
65	    Consumer consumer=new Consumer(blockingQueue);
66	    //创建5个生产者，5个消费者
67	    for(int i=0;i<10;i++){
68	      if(i<5){
69	        new Thread(producer,"producer"+i).start();
70	      }else{
71	        new Thread(consumer,"consumer"+(i-5)).start();
72	      }
73	    }
74	 
75	    try {
76	      Thread.sleep(1000);
77	    } catch (InterruptedException e) {
78	      // TODO Auto-generated catch block
79	      e.printStackTrace();
80	    }
81	    producer.shutDown();
82	    consumer.shutDown();
83	 
84	  }
85	}
```

**阻塞队列原理：**
其实阻塞队列实现阻塞同步的方式很简单，使用的就是是lock锁的多条件（condition）阻塞控制。使用BlockingQueue封装了根据条件阻塞线程的过程，而我们就不用关心繁琐的await/signal操作了。
下面是Jdk 1.7中ArrayBlockingQueue部分代码：

```java
1	public ArrayBlockingQueue(int capacity, boolean fair) {
2	 
3	    if (capacity <= 0)
4	      throw new IllegalArgumentException();
5	    //创建数组  
6	    this.items = new Object[capacity];
7	    //创建锁和阻塞条件
8	    lock = new ReentrantLock(fair);  
9	    notEmpty = lock.newCondition();
10	    notFull = lock.newCondition();
11	  }
12	//添加元素的方法
13	public void put(E e) throws InterruptedException {
14	    checkNotNull(e);
15	    final ReentrantLock lock = this.lock;
16	    lock.lockInterruptibly();
17	    try {
18	      while (count == items.length)
19	        notFull.await();
20	      //如果队列不满就入队
21	      enqueue(e);
22	    } finally {
23	      lock.unlock();
24	    }
25	  }
26	 //入队的方法
27	 private void enqueue(E x) {
28	    final Object[] items = this.items;
29	    items[putIndex] = x;
30	    if (++putIndex == items.length)
31	      putIndex = 0;
32	    count++;
33	    notEmpty.signal();
34	  }
35	 //移除元素的方法
36	 public E take() throws InterruptedException {
37	    final ReentrantLock lock = this.lock;
38	    lock.lockInterruptibly();
39	    try {
40	      while (count == 0)
41	        notEmpty.await();
42	      return dequeue();
43	    } finally {
44	      lock.unlock();
45	    }
46	  }
47	 //出队的方法
48	 private E dequeue() {
49	    final Object[] items = this.items;
50	    @SuppressWarnings("unchecked")
51	    E x = (E) items[takeIndex];
52	    items[takeIndex] = null;
53	    if (++takeIndex == items.length)
54	      takeIndex = 0;
55	    count--;
56	    if (itrs != null)
57	      itrs.elementDequeued();
58	    notFull.signal();
59	    return x;
60  }
```

双端阻塞队列（BlockingDeque）
concurrent包下还提供双端阻塞队列（BlockingDeque），和BlockingQueue是类似的，只不过BlockingDeque提供从任意一端插入或者抽取元素的队列。



##### 实现A和B交替输出

```java
public class Signal {
    private static volatile int signal = 0;
    static class ThreadA implements Runnable{
        @Override
        public void run() {
            while(signal <10){
                if(signal % 2 == 0) {
                    System.out.println("threadA: " +signal);
                    signal++;
                }
            }
        }
    }
    static class ThreadB implements Runnable {
        @Override
        public void run() {
            while (signal < 10) {
                if (signal % 2 == 1) {
                    System.out.println("threadB: " + signal);
                    signal = signal + 1;
                }
            }
        }
    }

    public static void main(String[] args) throws InterruptedException {
        new Thread(new ThreadA()).start();
        Thread.sleep(1000);
        new Thread(new ThreadB()).start();
    }
}
```

##### 实现A先输出 、B再输出

```java
public class ObjectLock {
    private static Object lock = new Object();
    static class ThreadA implements Runnable{
        @Override
        public void run() {
            synchronized (lock){
                for(int i = 0;i<100;i++){
                    System.out.println("Thread A "+i);
                }
            }
        }
    }
    static class ThreadB implements Runnable {
        @Override
        public void run() {
            synchronized (lock) {
                for (int i = 0; i < 100; i++) {
                    System.out.println("Thread B " + i);
                }
            }
        }
    }

    public static void main(String[] args) throws InterruptedException {
        new Thread(new ThreadA()).start();
        new Thread(new ThreadB()).start();
    }
}

```





