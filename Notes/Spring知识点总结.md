## Spring

### Spring 框架两大核心机制（IoC、AOP）

- IoC（控制反转）/ DI（依赖注入）
- AOP（面向切面编程）

Spring 是一个企业级开发框架，是软件设计层面的框架，优势在于可以将应用程序进行分层，开发者可以自主选择组件。

### Spring模块

<img src="https://gitee.com/sun-qiao321/picture/raw/master/images/20210424154051.png" style="zoom: 50%;" />

- **Spring Core：** 基础,可以说 Spring 其他所有的功能都需要依赖于该类库。主要提供 IoC 依赖注入功能。
- **Spring Aspects** ： 该模块为与AspectJ的集成提供支持。
- **Spring AOP** ：提供了面向切面的编程实现。
- **Spring JDBC** : Java数据库连接。
- **Spring JMS** ：Java消息服务。
- **Spring ORM** : 用于支持Hibernate等ORM工具。
- **Spring Web** : 为创建Web应用程序提供支持。
- **Spring Test** : 提供了对 JUnit 和 TestNG 测试的支持。

MVC：Struts2、Spring MVC

ORMapping：Hibernate、MyBatis、Spring Data

### 如何使用 IoC

- 创建 Maven 工程，pom.xml 添加依赖

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>com.southwind</groupId>
    <artifactId>aispringioc</artifactId>
    <version>1.0-SNAPSHOT</version>

    <dependencies>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-context</artifactId>
            <version>5.0.11.RELEASE</version>
        </dependency>
    </dependencies>

</project>
```

- 创建实体类 Student

```java
package com.southwind.entity;

import lombok.Data;

@Data
public class Student {
    private long id;
    private String name;
    private int age;
}
```

- 传统的开发方式，手动 new Student

```java
Student student = new Student();
student.setId(1L);
student.setName("张三");
student.setAge(22);
System.out.println(student);
```

- 通过 IoC 创建对象，在配置文件中添加需要管理的对象，XML 格式的配置文件，文件名可以自定义。

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:p="http://www.springframework.org/schema/p"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans-3.2.xsd
	http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context-4.3.xsd
">

    <bean id="student" class="com.southwind.entity.Student">
        <property name="id" value="1"></property>
        <property name="name" value="张三"></property>
        <property name="age" value="22"></property>
    </bean>

</beans>
```

- 从 IoC 中获取对象，通过 id 获取。

```java
//加载配置文件
ApplicationContext applicationContext = new ClassPathXmlApplicationContext("spring.xml");
Student student = (Student) applicationContext.getBean("student");
System.out.println(student);
```



### 配置文件

- 通过配置 `bean` 标签来完成对象的管理。

  - `id`：对象名。

  - `class`：对象的模版类（所有交给 IoC 容器来管理的类必须有无参构造函数，因为 Spring 底层是通过反射机制来创建对象，调用的是无参构造）。构造方法可以通过@AllArgsConstructor 和@NoArgsConstructor注解快速生成。

- 对象的成员变量通过 `property` 标签完成赋值。

  - `name`：成员变量名。
  - `value`：成员变量值（基本数据类型，String 可以直接赋值，如果是其他引用类型，不能通过 value 赋值）
  - `ref`：将 IoC 中的另外一个 bean 赋给当前的成员变量（DI）

  ```xml
  <bean id="student" class="com.southwind.entity.Student">
      <property name="id" value="1"></property>
      <property name="name" value="张三"></property>
      <property name="age" value="22"></property>
      <property name="address" ref="address"></property>
  </bean>
  
  <bean id="address" class="com.southwind.entity.Address">
      <property name="id" value="1"></property>
      <property name="name" value="科技路"></property>
  </bean>
  ```

  

### IoC 底层原理

- 读取配置文件，解析 XML。
- 通过反射机制实例化配置文件中所配置所有的 bean。

```java
package com.southwind.ioc;

import org.dom4j.Document;
import org.dom4j.DocumentException;
import org.dom4j.Element;
import org.dom4j.io.SAXReader;

import java.lang.reflect.Constructor;
import java.lang.reflect.Field;
import java.lang.reflect.InvocationTargetException;
import java.lang.reflect.Method;
import java.util.HashMap;
import java.util.Iterator;
import java.util.Map;

public class ClassPathXmlApplicationContext implements ApplicationContext {
    private Map<String,Object> ioc = new HashMap<String, Object>();
    public ClassPathXmlApplicationContext(String path){
        try {
            SAXReader reader = new SAXReader();
            Document document = reader.read("./src/main/resources/"+path);
            Element root = document.getRootElement();
            Iterator<Element> iterator = root.elementIterator();
            while(iterator.hasNext()){
                Element element = iterator.next();
                String id = element.attributeValue("id");
                String className = element.attributeValue("class");
                //通过反射机制创建对象
                Class clazz = Class.forName(className);
                //获取无参构造函数，创建目标对象
                Constructor constructor = clazz.getConstructor();
                Object object = constructor.newInstance();
                //给目标对象赋值
                Iterator<Element> beanIter = element.elementIterator();
                while(beanIter.hasNext()){
                    Element property = beanIter.next();
                    String name = property.attributeValue("name");
                    String valueStr = property.attributeValue("value");
                    String ref = property.attributeValue("ref");
                    if(ref == null){
                        String methodName = "set"+name.substring(0,1).toUpperCase()+name.substring(1);
                        Field field = clazz.getDeclaredField(name);
                        Method method = clazz.getDeclaredMethod(methodName,field.getType());
                        //根据成员变量的数据类型将 value 进行转换
                        Object value = null;
                        if(field.getType().getName() == "long"){
                            value = Long.parseLong(valueStr);
                        }
                        if(field.getType().getName() == "java.lang.String"){
                            value = valueStr;
                        }
                        if(field.getType().getName() == "int"){
                            value = Integer.parseInt(valueStr);
                        }
                        method.invoke(object,value);
                    }
                    ioc.put(id,object);
                }
            }
        } catch (DocumentException e) {
            e.printStackTrace();
        } catch (ClassNotFoundException e){
            e.printStackTrace();
        } catch (NoSuchMethodException e){
            e.printStackTrace();
        } catch (InstantiationException e){
            e.printStackTrace();
        } catch (IllegalAccessException e){
            e.printStackTrace();
        } catch (InvocationTargetException e){
            e.printStackTrace();
        } catch (NoSuchFieldException e){
            e.printStackTrace();
        }
    }

    public Object getBean(String id) {
        return ioc.get(id);
    }
}
```



### 通过运行时类获取 bean

```java
ApplicationContext applicationContext = new ClassPathXmlApplicationContext("spring.xml");
Student student = (Student) applicationContext.getBean(Student.class);
System.out.println(student);
```

这种方式存在一个问题，配置文件中一个数据类型的对象只能有一个实例，否则会抛出异常，因为没有唯一的 bean。



### 通过有参构造创建 bean

- 在实体类中创建对应的有参构造函数。（需要创建对应的构造方法）
- 配置文件

```xml
<bean id="student3" class="com.southwind.entity.Student">
    <constructor-arg name="id" value="3"></constructor-arg>
    <constructor-arg name="name" value="小明"></constructor-arg>
    <constructor-arg name="age" value="18"></constructor-arg>
    <constructor-arg name="address" ref="address"></constructor-arg>
</bean>
```



```xml
<bean id="student3" class="com.southwind.entity.Student">
    <constructor-arg index="0" value="3"></constructor-arg>
    <constructor-arg index="2" value="18"></constructor-arg>
    <constructor-arg index="1" value="小明"></constructor-arg>
    <constructor-arg index="3" ref="address"></constructor-arg>
</bean>
```



### 给 bean 注入集合

```xml
<bean id="student" class="com.southwind.entity.Student">
    <property name="id" value="2"></property>
    <property name="name" value="李四"></property>
    <property name="age" value="33"></property>
    <property name="addresses">
        <list>
            <ref bean="address"></ref>
            <ref bean="address2"></ref>
        </list>
    </property>
</bean>

<bean id="address" class="com.southwind.entity.Address">
    <property name="id" value="1"></property>
    <property name="name" value="科技路"></property>
</bean>

<bean id="address2" class="com.southwind.entity.Address">
    <property name="id" value="2"></property>
    <property name="name" value="高新区"></property>
</bean>
```



### scope 作用域

Spring 管理的 bean 是根据 scope 来生成的，表示 bean 的作用域，共4种，默认值是 singleton。

- singleton：单例，表示通过 IoC 容器获取的 bean 是唯一的。
- prototype：原型，表示通过 IoC 容器获取的 bean 是不同的。
- request：请求，表示在一次 HTTP 请求内有效。
- session：会话，表示在一个用户会话内有效。

request 和 session 只适用于 Web 项目，大多数情况下，使用单例和原型较多。

prototype 模式当业务代码获取 IoC 容器中的 bean 时，Spring 才去调用无参构造创建对应的 bean。

singleton 模式无论业务代码是否获取 IoC 容器中的 bean，Spring 在加载 spring.xml 时就会创建 bean。



### Spring 的继承

与 Java 的继承不同，Java 是类层面的继承，子类可以继承父类的内部结构信息；Spring 是对象层面的继承，子对象可以继承父对象的属性值。

```xml
<bean id="student2" class="com.southwind.entity.Student">
    <property name="id" value="1"></property>
    <property name="name" value="张三"></property>
    <property name="age" value="22"></property>
    <property name="addresses">
        <list>
            <ref bean="address"></ref>
            <ref bean="address2"></ref>
        </list>
    </property>
</bean>

<bean id="address" class="com.southwind.entity.Address">
    <property name="id" value="1"></property>
    <property name="name" value="科技路"></property>
</bean>

<bean id="address2" class="com.southwind.entity.Address">
    <property name="id" value="2"></property>
    <property name="name" value="高新区"></property>
</bean>

<bean id="stu" class="com.southwind.entity.Student" parent="student2">
    <property name="name" value="李四"></property>
</bean>
```

Spring 的继承关注点在于具体的对象，而不在于类，即不同的两个类的实例化对象可以完成继承，前提是子对象必须包含父对象的所有属性，同时可以在此基础上添加其他的属性。



### Spring 的依赖

与继承类似，依赖也是描述 bean 和 bean 之间的一种关系，配置依赖之后，被依赖的 bean 一定先创建，再创建依赖的 bean，A 依赖于 B，先创建 B，再创建 A。

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans-3.2.xsd
                           ">

    <bean id="student" class="com.southwind.entity.Student" depends-on="user"></bean>

    <bean id="user" class="com.southwind.entity.User"></bean>

</beans>
```



### Spring 的 p 命名空间

p 命名空间是对 IoC / DI 的简化操作，使用 p 命名空间可以更加方便的完成 bean 的配置以及 bean 之间的依赖注入。（主要作用是配置<property>时更方便。

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:p="http://www.springframework.org/schema/p"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans-3.2.xsd
	http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context-4.3.xsd
">

    <bean id="student" class="com.southwind.entity.Student" p:id="1" p:name="张三" p:age="22" p:address-ref="address"></bean>

    <bean id="address" class="com.southwind.entity.Address" p:id="2" p:name="科技路"></bean>

</beans>
```



### Spring 的工厂方法

IoC 通过工厂模式创建 bean 的方式有两种：

- 静态工厂方法
- 实例工厂方法

> 静态工厂方法

```java
package com.southwind.entity;

import lombok.AllArgsConstructor;
import lombok.Data;
import lombok.NoArgsConstructor;

@Data
@AllArgsConstructor
@NoArgsConstructor
public class Car {
    private long id;
    private String name;
}
```

```java
package com.southwind.factory;

import com.southwind.entity.Car;

import java.util.HashMap;
import java.util.Map;

public class StaticCarFactory {
    private static Map<Long, Car> carMap;
    static{
        carMap = new HashMap<Long, Car>();
        carMap.put(1L,new Car(1L,"宝马"));
        carMap.put(2L,new Car(2L,"奔驰"));
    }

    public static Car getCar(long id){
        return carMap.get(id);
    }
}
```

```xml
<!-- 配置静态工厂创建 Car -->
<bean id="car" class="com.southwind.factory.StaticCarFactory" factory-method="getCar">
    <constructor-arg value="2"></constructor-arg>
</bean>
```

> 实例工厂方法

```java
package com.southwind.factory;

import com.southwind.entity.Car;

import java.util.HashMap;
import java.util.Map;

public class InstanceCarFactory {
    private Map<Long, Car> carMap;
    public InstanceCarFactory(){
        carMap = new HashMap<Long, Car>();
        carMap.put(1L,new Car(1L,"宝马"));
        carMap.put(2L,new Car(2L,"奔驰"));
    }

    public Car getCar(long id){
        return carMap.get(id);
    }
}
```

```xml
<!-- 配置实例工厂 bean -->
<bean id="carFactory" class="com.southwind.factory.InstanceCarFactory"></bean>

<!-- 实例工厂创建 Car -->
<bean id="car2" factory-bean="carFactory" factory-method="getCar">
    <constructor-arg value="1"></constructor-arg>
</bean>
```



### IoC 自动装载（Autowire）

IoC 负责创建对象，DI 负责完成对象的依赖注入，通过配置 property 标签的 ref 属性来完成，同时 Spring 提供了另外一种更加简便的依赖注入方式：自动装载，不需要手动配置 property，IoC 容器会自动选择 bean 完成注入。

自动装载有两种方式：

- byName：通过属性名自动装载
- byType：通过属性的数据类型自动装载

> byName

```xml
<bean id="cars" class="com.southwind.entity.Car">
    <property name="id" value="1"></property>
    <property name="name" value="宝马"></property>
</bean>

<bean id="person" class="com.southwind.entity.Person" autowire="byName">
    <property name="id" value="11"></property>
    <property name="name" value="张三"></property>
</bean>
```

> byType

```xml
<bean id="car" class="com.southwind.entity.Car">
    <property name="id" value="2"></property>
    <property name="name" value="奔驰"></property>
</bean>

<bean id="person" class="com.southwind.entity.Person" autowire="byType">
    <property name="id" value="11"></property>
    <property name="name" value="张三"></property>
</bean>
```

byType 需要注意，如果同时存在两个及以上的符合条件的 bean 时，自动装载会抛出异常。



### AOP

AOP：Aspect Oriented Programming 面向切面编程。

AOP 的优点：

- 降低模块之间的耦合度。
- 使系统更容易扩展。
- 更好的代码复用。
- 非业务代码更加集中，不分散，便于统一管理。
- 业务代码更加简洁存粹，不参杂其他代码的影响。

AOP 是对面向对象编程的一个补充，在运行时，动态地将代码切入到类的指定方法、指定位置上的编程思想就是面向切面编程。将不同方法的同一个位置抽象成一个切面对象，对该切面对象进行编程就是 AOP。

### 如何使用？

- 创建 Maven 工程，pom.xml 添加

```xml
<dependencies>
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-context</artifactId>
        <version>5.0.11.RELEASE</version>
    </dependency>
    
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-aop</artifactId>
        <version>5.0.11.RELEASE</version>
    </dependency>

    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-aspects</artifactId>
        <version>5.0.11.RELEASE</version>
    </dependency>
</dependencies>
```

- 创建一个计算器接口 Cal，定义4个方法。

```java
package com.southwind.utils;

public interface Cal {
    public int add(int num1,int num2);
    public int sub(int num1,int num2);
    public int mul(int num1,int num2);
    public int div(int num1,int num2);
}
```

- 创建接口的实现类 CalImpl。

```java
package com.southwind.utils.impl;

import com.southwind.utils.Cal;

public class CalImpl implements Cal {
    public int add(int num1, int num2) {
        System.out.println("add方法的参数是["+num1+","+num2+"]");
        int result = num1+num2;
        System.out.println("add方法的结果是"+result);
        return result;
    }

    public int sub(int num1, int num2) {
        System.out.println("sub方法的参数是["+num1+","+num2+"]");
        int result = num1-num2;
        System.out.println("sub方法的结果是"+result);
        return result;
    }

    public int mul(int num1, int num2) {
        System.out.println("mul方法的参数是["+num1+","+num2+"]");
        int result = num1*num2;
        System.out.println("mul方法的结果是"+result);
        return result;
    }

    public int div(int num1, int num2) {
        System.out.println("div方法的参数是["+num1+","+num2+"]");
        int result = num1/num2;
        System.out.println("div方法的结果是"+result);
        return result;
    }
}
```



上述代码中，日志信息和业务逻辑的耦合性很高，不利于系统的维护，使用 AOP 可以进行优化，如何来实现 AOP？使用动态代理的方式来实现。

给业务代码找一个代理，打印日志信息的工作交个代理来做，这样的话业务代码就只需要关注自身的业务即可。

```java
package com.southwind.utils;

import java.lang.reflect.InvocationHandler;
import java.lang.reflect.Method;
import java.lang.reflect.Proxy;
import java.util.Arrays;

public class MyInvocationHandler implements InvocationHandler {
    //接收委托对象
    private Object object = null;

    //返回代理对象
    public Object bind(Object object){
        this.object = object;
        return Proxy.newProxyInstance(object.getClass().getClassLoader(),object.getClass().getInterfaces(),this);
    }

    public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
        System.out.println(method.getName()+"方法的参数是："+ Arrays.toString(args));
        Object result = method.invoke(this.object,args);
        System.out.println(method.getName()+"的结果是"+result);
        return result;
    }
}
```

以上是通过动态代理实现 AOP 的过程，比较复杂，不好理解，Spring 框架对 AOP 进行了封装，使用 Spring 框架可以用面向对象的思想来实现 AOP。

Spring 框架中不需要创建 InvocationHandler，只需要创建一个切面对象，将所有的非业务代码在切面对象中完成即可，Spring 框架底层会自动根据切面类以及目标类生成一个代理对象。

LoggerAspect

```java
package com.southwind.aop;

import org.aspectj.lang.JoinPoint;
import org.aspectj.lang.annotation.*;
import org.springframework.stereotype.Component;

import java.util.Arrays;

@Aspect
@Component
public class LoggerAspect {

    @Before(value = "execution(public int com.southwind.utils.impl.CalImpl.*(..))")
    public void before(JoinPoint joinPoint){
        //获取方法名
        String name = joinPoint.getSignature().getName();
        //获取参数
        String args = Arrays.toString(joinPoint.getArgs());
        System.out.println(name+"方法的参数是："+ args);
    }

    @After(value = "execution(public int com.southwind.utils.impl.CalImpl.*(..))")
    public void after(JoinPoint joinPoint){
        //获取方法名
        String name = joinPoint.getSignature().getName();
        System.out.println(name+"方法执行完毕");
    }

    @AfterReturning(value = "execution(public int com.southwind.utils.impl.CalImpl.*(..))",returning = "result")
    public void afterReturning(JoinPoint joinPoint,Object result){
        //获取方法名
        String name = joinPoint.getSignature().getName();
        System.out.println(name+"方法的结果是"+result);
    }

    @AfterThrowing(value = "execution(public int com.southwind.utils.impl.CalImpl.*(..))",throwing = "exception")
    public void afterThrowing(JoinPoint joinPoint,Exception exception){
        //获取方法名
        String name = joinPoint.getSignature().getName();
        System.out.println(name+"方法抛出异常："+exception);
    }

}
```

LoggerAspect 类定义处添加的两个注解：

- `@Aspect`：表示该类是切面类。
- `@Component`：将该类的对象注入到 IoC 容器。

具体方法处添加的注解：

`@Before`：表示方法执行的具体位置和时机。

CalImpl 也需要添加 `@Component`，交给 IoC 容器来管理。

```java
package com.southwind.utils.impl;

import com.southwind.utils.Cal;
import org.springframework.stereotype.Component;

@Component
public class CalImpl implements Cal {
    public int add(int num1, int num2) {
        int result = num1+num2;
        return result;
    }

    public int sub(int num1, int num2) {
        int result = num1-num2;
        return result;
    }

    public int mul(int num1, int num2) {
        int result = num1*num2;
        return result;
    }

    public int div(int num1, int num2) {
        int result = num1/num2;
        return result;
    }
}
```

spring.xml 中配置 AOP。

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:aop="http://www.springframework.org/schema/aop"
       xmlns:p="http://www.springframework.org/schema/p"
       xsi:schemaLocation="http://www.springframework.org/schema/aop http://www.springframework.org/schema/aop/spring-aop-4.3.xsd
       http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans-4.3.xsd
	http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context-4.3.xsd
">

    <!-- 自动扫描 -->
    <context:component-scan base-package="com.southwind"></context:component-scan>

    <!-- 使Aspect注解生效，为目标类自动生成代理对象 -->
    <aop:aspectj-autoproxy></aop:aspectj-autoproxy>

</beans>
```

`context:component-scan` 将 `com.southwind` 包中的所有类进行扫描，如果该类同时添加了 `@Component`，则将该类扫描到 IoC 容器中，即 IoC 管理它的对象。

`aop:aspectj-autoproxy` 让 Spring 框架结合切面类和目标类自动生成动态代理对象。

测试的对象需要从spring.xml中加载：

```java
public static void main(String[] args) {
    ApplicationContext applicationContext = new ClassPathXmlApplicationContext("spring.xml");
    Cal cal = (Cal) applicationContext.getBean("callmpl"); //括号中必须为类名开头字母小写。
    cal.add(1,2);
    cal.sub(2,1);
    cal.mul(1,2);
    cal.div(2,1);
}
```



- 切面：横切关注点被模块化的抽象对象。
- 通知：切面对象完成的工作。
- 目标：被通知的对象，即被横切的对象。
- 代理：切面、通知、目标混合之后的对象。
- 连接点：通知要插入业务代码的具体位置。
- 切点：AOP 通过切点定位到连接点。

## Spring MVC

Spring MVC 是目前主流的实现 MVC 设计模式的企业级开发框架，Spring 框架的一个子模块，无需整合，开发起来更加便捷。

#### 什么是 MVC 设计模式？

##### MVC设计模式

<img src="https://img-blog.csdnimg.cn/20190603152425689.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MjE1MzQxMA==,size_16,color_FFFFFF,t_70" alt="img" style="zoom: 33%;" />

M：model模型，负责各个功能的实现（如登录、增加、删除等）。用JavaBean实现。

> JavaBeans :
> ①是Java中一种特殊的类（换言之：JavaBean就是一个Java类）.
> 一个Java类 ，满足以下要求，则可称为一个JavaBean
> a. public修饰的类，提供public 无参构造方法
> b. 所有属性 都是private
> c. 提供getter和setter方法
> ②从使用层面来看，JavaBean分为2大类：
> a. 封装业务逻辑的JavaBean （eg:LoginDao.java 封装了登录逻辑）
> b. 封装数据的JavaBean （实体类：eg：Student.java  Vedio.java 。往往对应于数据库中的一张表，即数据库中有个Student表，项目中就有个Student.java类）
> ③JavaBean 是一个可以重复使用的组件，通过编写一个组件来实现某种通用功能，“一次编写、任何地方执行、任何地方重用”。

V：视图View，负责页面的显示；与用户的交互。包含各种表单。 实现视图用到的技术html/css/jsp/js等前端技术。用户交互：用户鼠标点击页面；填写页面中各种表单........等等。

C：控制器Controller，控制器负责将视图与模型一一对应起来。相当于一个模型分发器。所谓分发就是：①接收请求，并将该请求跳转（转发，重定向）到模型进行处理。②模型处理完毕后，再通过控制器，返回给视图中的请求处。**建议使用Servlet实现控制器。**

将应用程序分为 Controller、Model、View 三层，Controller 接收客户端请求，调用 Model 生成业务数据，传递给 View。

Spring MVC 就是对这套流程的封装，屏蔽了很多底层代码，开放出接口，让开发者可以更加轻松、便捷地完成基于 MVC 模式的 Web 开发。

#### Spring MVC 的核心组件

- DispatcherServlet：前置控制器，是整个流程控制的核心，控制其他组件的执行，进行统一调度，降低组件之间的耦合性，相当于总指挥。
- Handler：处理器，完成具体的业务逻辑，相当于 Servlet 或 Action。
- HandlerMapping：DispatcherServlet 接收到请求之后，通过 HandlerMapping 将不同的请求映射到不同的 Handler。
- HandlerInterceptor：处理器拦截器，是一个接口，如果需要完成一些拦截处理，可以实现该接口。
- HandlerExecutionChain：处理器执行链，包括两部分内容：Handler 和 HandlerInterceptor（系统会有一个默认的 HandlerInterceptor，如果需要额外设置拦截，可以添加拦截器）。
- HandlerAdapter：处理器适配器，Handler 执行业务方法之前，需要进行一系列的操作，包括表单数据的验证、数据类型的转换、将表单数据封装到 JavaBean 等，这些操作都是由 HandlerApater 来完成，开发者只需将注意力集中业务逻辑的处理上，DispatcherServlet 通过 HandlerAdapter 执行不同的 Handler。
- ModelAndView：装载了模型数据和视图信息，作为 Handler 的处理结果，返回给 DispatcherServlet。
- ViewResolver：视图解析器，DispatcheServlet 通过它将逻辑视图解析为物理视图，最终将渲染结果响应给客户端。

#### Spring MVC 的工作流程

- 客户端请求被 DisptacherServlet 接收。
- 根据 HandlerMapping 映射到 Handler。
- 生成 Handler 和 HandlerInterceptor。
- Handler 和 HandlerInterceptor 以 HandlerExecutionChain 的形式一并返回给 DisptacherServlet。
- DispatcherServlet 通过 HandlerAdapter 调用 Handler 的方法完成业务逻辑处理。
- Handler 返回一个 ModelAndView 给 DispatcherServlet。
- DispatcherServlet 将获取的 ModelAndView 对象传给 ViewResolver 视图解析器，将逻辑视图解析为物理视图 View。
- ViewResovler 返回一个 View 给 DispatcherServlet。
- DispatcherServlet 根据 View 进行视图渲染（将模型数据 Model 填充到视图 View 中）。
- DispatcherServlet 将渲染后的结果响应给客户端。

![image-20210306094734424](C:\Users\SQ\AppData\Roaming\Typora\typora-user-images\image-20210306094734424.png)

Spring MVC 流程非常复杂，实际开发中很简单，因为大部分的组件不需要开发者创建、管理，只需要通过配置文件的方式完成配置即可，真正需要开发者进行处理的只有 Handler 、View。

#### 如何使用？

创建Maven工程，勾选Create from archetype 选择maven-archetype-webapp模板，然后在main下新建java和resources文件夹，右键分别Mark Directory as 为Sources Root和Resources root。

- 创建 Maven 工程，pom.xml

```xml
<dependencies>

    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-webmvc</artifactId>
        <version>5.0.11.RELEASE</version>
    </dependency>

</dependencies>
```

- 在 web.xml 中配置 DispatcherServlet。

```xml
<!DOCTYPE web-app PUBLIC
 "-//Sun Microsystems, Inc.//DTD Web Application 2.3//EN"
 "http://java.sun.com/dtd/web-app_2_3.dtd" >

<web-app>
  <display-name>Archetype Created Web Application</display-name>
  
  <servlet>
    <servlet-name>dispatcherServlet</servlet-name>
    <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
    <init-param>
      <param-name>contextConfigLocation</param-name>
      <param-value>classpath:springmvc.xml</param-value>
    </init-param>
  </servlet>
  
  <servlet-mapping>
    <servlet-name>dispatcherServlet</servlet-name>
    <url-pattern>/</url-pattern>
  </servlet-mapping>
  
</web-app>
```

- springmvc.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:mvc="http://www.springframework.org/schema/mvc"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context.xsd
       http://www.springframework.org/schema/mvc
       http://www.springframework.org/schema/mvc/spring-mvc-3.2.xsd">

    <!-- 自动扫描 -->
    <context:component-scan base-package="com.southwind"></context:component-scan>

    <!-- 配置视图解析器 -->
    <bean class="org.springframework.web.servlet.view.InternalResourceViewResolver">
        <property name="prefix" value="/"></property>
        <property name="suffix" value=".jsp"></property>
    </bean>

</beans>
```

- 创建 Handler

```java
package com.southwind.controller;

import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.RequestMapping;

@Controller
public class HelloHandler {

    @RequestMapping("/index")
    public String index(){
        System.out.println("执行了index...");
        return "index";
    }
}
```

加Tomcat运行（注意配置Tomcat的Deployment）。

### Spring MVC 注解

- @RequestMapping 

Spring MVC 通过 @RequestMapping 注解将 URL 请求与业务方法进行映射，在 Handler 的类定义处以及方法定义处都可以添加 @RequestMapping ，在类定义处添加，相当于客户端多了一层访问路径。

- @Controller

@Controller 在类定义处添加，将该类交个 IoC 容器来管理（结合 springmvc.xml 的自动扫描配置使用），同时使其成为一个控制器，可以接收客户端请求。

```java
package com.southwind.controller;

import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.RequestMapping;

@Controller
@RequestMapping("/hello")
public class HelloHandler {

    @RequestMapping("/index")
    public String index(){
        System.out.println("执行了index...");
        return "index";
    }
}
```

- @RequestMapping 相关参数

1、value：指定 URL 请求的实际地址，是 @RequestMapping 的默认值。

```java
@RequestMapping("/index")
public String index(){
    System.out.println("执行了index...");
    return "index";
}
```

等于

```java
@RequestMapping(value="/index")
public String index(){
    System.out.println("执行了index...");
    return "index";
}
```

2、method：指定请求的 method 类型，GET、POST、PUT、DELETE。

```java
@RequestMapping(value = "/index",method = RequestMethod.GET)
public String index(){
    System.out.println("执行了index...");
    return "index";
}
```

上述代码表示 index 方法只能接收 GET 请求。

3、params：指定请求中必须包含某些参数，否则无法调用该方法。

```java
@RequestMapping(value = "/index",method = RequestMethod.GET,params = {"name","id=10"})
public String index(){
    System.out.println("执行了index...");
    return "index";
}
```

上述代码表示请求中必须包含 name 和 id 两个参数，同时 id 的值必须是 10。

关于参数绑定，在形参列表中通过添加 @RequestParam 注解完成 HTTP 请求参数与业务方法形参的映射。

```java
@RequestMapping(value = "/index",method = RequestMethod.GET,params = {"name","id=10"})
public String index(@RequestParam("name") String str,@RequestParam("id") int age){
    System.out.println(str);
    System.out.println(age);
    System.out.println("执行了index...");
    return "index";
}
```

上述代码表示将请求的参数 name 和 id 分别赋给了形参 str 和 age ，同时自动完成了数据类型转换，将 “10” 转为了 int 类型的 10，再赋给 age，这些工作都是由 HandlerAdapter 来完成的。

Spring MVC 也支持 RESTful 风格的 URL。

传统类型：http://localhost:8080/hello/index?name=zhangsan&id=10

REST：http://localhost:8080/hello/index/zhangsan/10

```java
@RequestMapping("/rest/{name}/{id}")  //此时不会自动匹配，必须注解映射
public String rest(@PathVariable("name") String name,@PathVariable("id") int id){
    System.out.println(name);
    System.out.println(id);
    return "index";
}
```

通过 @PathVariable 注解完成请求参数与形参的映射。

- 映射 Cookie

Spring MVC 通过映射可以直接在业务方法中获取 Cookie 的值。

```java
@RequestMapping("/cookie")
public String cookie(@CookieValue(value = "JSESSIONID") String sessionId){
    System.out.println(sessionId);
    return "index";
}
```

- 使用 JavaBean 绑定参数

Spring MVC 会根据请求参数名和 JavaBean 属性名进行自动匹配，自动为对象填充属性值，同时支持及联属性。

```java
package com.southwind.entity;

import lombok.Data;

@Data
public class Address {
    private String value;
}
```

```java
package com.southwind.entity;

import lombok.Data;

@Data
public class User {
    private long id;
    private String name;
    private Address address;
}
```

```jsp
<%--
  Created by IntelliJ IDEA.
  User: southwind
  Date: 2019-03-13
  Time: 15:33
  To change this template use File | Settings | File Templates.
--%>
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>Title</title>
</head>
<body>
    <form action="/hello/save" method="post">
        用户id：<input type="text" name="id"/><br/>
        用户名：<input type="text" name="name"/><br/>
        用户地址：<input type="text" name="address.value"/><br/>
        <input type="submit" value="注册"/>
    </form>
</body>
</html>
```

```java
@RequestMapping(value = "/save",method = RequestMethod.POST)
public String save(User user){
    System.out.println(user);
    return "index";
}
```

如果出现中文乱码问题，只需在 web.xml 添加 Spring MVC 自带的过滤器即可。

```xml
<filter>
    <filter-name>encodingFilter</filter-name>
    <filter-class>org.springframework.web.filter.CharacterEncodingFilter</filter-class>
    <init-param>
        <param-name>encoding</param-name>
        <param-value>UTF-8</param-value>
    </init-param>
</filter>

<filter-mapping>
    <filter-name>encodingFilter</filter-name>
    <url-pattern>/*</url-pattern>
</filter-mapping>
```

- JSP 页面的转发和重定向：

Spring MVC 默认是以转发的形式响应 JSP。

1、转发（地址栏不改变）

```java
@RequestMapping("/forward")
public String forward(){
    return "forward:/index.jsp";
    //        return "index";
}
```

2、重定向（地址栏改变）

```java
@RequestMapping("/redirect")
public String redirect(){
    return "redirect:/index.jsp";
}
```



### Spring MVC 数据绑定

数据绑定：在后端的业务方法中直接获取客户端 HTTP 请求中的参数，将请求参数映射到业务方法的形参中，Spring MVC 中数据绑定的工作是由 HandlerAdapter 来完成的。（以前是通过req.getParamater取得的）

- 基本数据类型

```java
@RequestMapping("/baseType")
@ResponseBody
public String baseType(int id){
    return id+"";
}
```

@ResponseBody 表示 Spring MVC 会直接将业务方法的返回值响应给客户端，如果不加 @ResponseBody 注解，Spring MVC 会将业务方法的放回值传递给 DispatcherServlet，再由 DisptacherServlet 调用 ViewResolver 对返回值进行解析，映射到一个 JSP 资源。

- 包装类

```java
@RequestMapping("/packageType")
@ResponseBody
public String packageType(@RequestParam(value = "num",required = false,defaultValue = "0") Integer id){
    return id+"";
}
```

包装类可以接收 null，当 HTTP 请求没有参数时，使用包装类定义形参的数据类型，程序不会抛出异常。

@RequestParam

value = "num"：将 HTTP 请求中名为 num 的参数赋给形参 id。

requried：设置 num 是否为必填项，true 表示必填，false 表示非必填，可省略。

defaultValue = “0”：如果 HTTP 请求中没有 num 参数，默认值为0.

- 数组

```java
@RestController
@RequestMapping("/data")
public class DataBindHandler {
    @RequestMapping("/array")
    public String array(String[] name){
        String str = Arrays.toString(name);
        return str;
    }
}
```

@RestController 表示该控制器会直接将业务方法的返回值响应给客户端，不进行视图解析。

@Controller 表示该控制器的每一个业务方法的返回值都会交给视图解析器进行解析，如果只需要将数据响应给客户端，而不需要进行视图解析，则需要在对应的业务方法定义处添加 @ResponseBody。

```java
@RestController
@RequestMapping("/data")
public class DataBindHandler {
    @RequestMapping("/array")
    public String array(String[] name){
        String str = Arrays.toString(name);
        return str;
    }
}
```

等同于

```java
@Controller
@RequestMapping("/data")
public class DataBindHandler {
    @RequestMapping("/array")
    @ResponseBody
    public String array(String[] name){
        String str = Arrays.toString(name);
        return str;
    }
}
```

- List

Spring MVC 不支持 List 类型的直接转换，需要对 List 集合进行包装。

集合封装类

```java
package com.southwind.entity;

import lombok.Data;

import java.util.List;

@Data
public class UserList {
    private List<User> users;
}
```

JSP

```jsp
<%--
  Created by IntelliJ IDEA.
  User: southwind
  Date: 2019-03-14
  Time: 09:12
  To change this template use File | Settings | File Templates.
--%>
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>Title</title>
</head>
<body>
    <form action="/data/list" method="post">
        用户1编号：<input type="text" name="users[0].id"/><br/>
        用户1名称：<input type="text" name="users[0].name"/><br/>
        用户2编号：<input type="text" name="users[1].id"/><br/>
        用户2名称：<input type="text" name="users[1].name"/><br/>
        用户3编号：<input type="text" name="users[2].id"/><br/>
        用户3名称：<input type="text" name="users[2].name"/><br/>
        <input type="submit" value="提交"/>
    </form>
</body>
</html>
```

业务方法

```java
@RequestMapping("/list")
public String list(UserList userList){
    StringBuffer str = new StringBuffer();
    for(User user:userList.getUsers()){
        str.append(user);
    }
    return str.toString();
}
```

处理 @ResponseBody 中文乱码，在 springmvc.xml 中配置消息转换器。

```xml
<mvc:annotation-driven>
    <!-- 消息转换器 -->
    <mvc:message-converters register-defaults="true">
        <bean class="org.springframework.http.converter.StringHttpMessageConverter">
            <property name="supportedMediaTypes" value="text/html;charset=UTF-8"></property>
        </bean>
    </mvc:message-converters>
</mvc:annotation-driven>
```



- Map

自定义封装类

```java
package com.southwind.entity;

import lombok.Data;

import java.util.Map;

@Data
public class UserMap {
    private Map<String,User> users;
}
```

业务方法

```java
@RequestMapping("/map")
public String map(UserMap userMap){
    StringBuffer str = new StringBuffer();
    for(String key:userMap.getUsers().keySet()){
        User user = userMap.getUsers().get(key);
        str.append(user);
    }
    return str.toString();
}
```

JSP

```jsp
<%--
  Created by IntelliJ IDEA.
  User: southwind
  Date: 2019-03-14
  Time: 09:12
  To change this template use File | Settings | File Templates.
--%>
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>Title</title>
</head>
<body>
    <form action="/data/map" method="post">
        用户1编号：<input type="text" name="users['a'].id"/><br/>
        用户1名称：<input type="text" name="users['a'].name"/><br/>
        用户2编号：<input type="text" name="users['b'].id"/><br/>
        用户2名称：<input type="text" name="users['b'].name"/><br/>
        用户3编号：<input type="text" name="users['c'].id"/><br/>
        用户3名称：<input type="text" name="users['c'].name"/><br/>
        <input type="submit" value="提交"/>
    </form>
</body>
</html>
```



- JSON

客户端发生 JSON 格式的数据，直接通过 Spring MVC 绑定到业务方法的形参中。

处理 Spring MVC 无法加载静态资源，在 web.xml 中添加配置即可。

```xml
<servlet-mapping>
    <servlet-name>default</servlet-name>
    <url-pattern>*.js</url-pattern>
</servlet-mapping>
```

JSP

```jsp
<%--
  Created by IntelliJ IDEA.
  User: southwind
  Date: 2019-03-14
  Time: 10:35
  To change this template use File | Settings | File Templates.
--%>
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>Title</title>
    <script type="text/javascript" src="js/jquery-3.3.1.min.js"></script>
    <script type="text/javascript">
        $(function(){
           var user = {
               "id":1,
               "name":"张三"
           };
           $.ajax({
               url:"/data/json",
               data:JSON.stringify(user),
               type:"POST",
               contentType:"application/json;charset=UTF-8",
               dataType:"JSON",
               success:function(data){
                   alter(data.id+"---"+data.name);
               }
           })
        });
    </script>
</head>
<body>

</body>
</html>
```

业务方法

```java
@RequestMapping("/json")
public User json(@RequestBody User user){
    System.out.println(user);
    user.setId(6);
    user.setName("张六");
    return user;
}
```

Spring MVC 中的 JSON 和 JavaBean 的转换需要借助于 fastjson，pom.xml 引入相关依赖。

```xml
<dependency>
    <groupId>com.alibaba</groupId>
    <artifactId>fastjson</artifactId>
    <version>1.2.32</version>
</dependency>
```

springmvc.xml 添加 fastjson 配置。

```xml
<mvc:annotation-driven>
    <!-- 消息转换器 -->
    <mvc:message-converters register-defaults="true">
        <bean class="org.springframework.http.converter.StringHttpMessageConverter">
            <property name="supportedMediaTypes" value="text/html;charset=UTF-8"></property>
        </bean>
        <!-- 配置fastjson -->
        <bean class="com.alibaba.fastjson.support.spring.FastJsonHttpMessageConverter4"></bean>
    </mvc:message-converters>
</mvc:annotation-driven>
```



### Spring MVC 模型数据解析

JSP 四大作用域对应的内置对象：pageContext、request、session、application。

模型数据的绑定是由 ViewResolver 来完成的，实际开发中，我们需要先添加模型数据，再交给 ViewResolver 来绑定。

Spring MVC 提供了以下几种方式添加模型数据：

- Map
- Model
- ModelAndView
- @SessionAttribute
- @ModelAttribute

> 将模式数据绑定到 request 对象。

1、Map

```java
@RequestMapping("/map")
public String map(Map<String,User> map){
    User user = new User();
    user.setId(1L);
    user.setName("张三");
    map.put("user",user);
    return "view";
}
```

JSP

```jsp
<%--
  Created by IntelliJ IDEA.
  User: southwind
  Date: 2019-03-14
  Time: 11:36
  To change this template use File | Settings | File Templates.
--%>
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<%@ page isELIgnored="false" %>
<html>
<head>
    <title>Title</title>
</head>
<body>
    ${requestScope.user}
</body>
</html>
```

2、Model

```java
@RequestMapping("/model")
public String model(Model model){
    User user = new User();
    user.setId(1L);
    user.setName("张三");
    model.addAttribute("user",user);
    return "view";
}
```

3、ModelAndView

```java
@RequestMapping("/modelAndView")
public ModelAndView modelAndView(){
    User user = new User();
    user.setId(1L);
    user.setName("张三");
    ModelAndView modelAndView = new ModelAndView();
    modelAndView.addObject("user",user);
    modelAndView.setViewName("view");
    return modelAndView;
}

@RequestMapping("/modelAndView2")
public ModelAndView modelAndView2(){
    User user = new User();
    user.setId(1L);
    user.setName("张三");
    ModelAndView modelAndView = new ModelAndView();
    modelAndView.addObject("user",user);
    View view = new InternalResourceView("/view.jsp");
    modelAndView.setView(view);
    return modelAndView;
}

@RequestMapping("/modelAndView3")
public ModelAndView modelAndView3(){
    User user = new User();
    user.setId(1L);
    user.setName("张三");
    ModelAndView modelAndView = new ModelAndView("view");
    modelAndView.addObject("user",user);
    return modelAndView;
}

@RequestMapping("/modelAndView4")
public ModelAndView modelAndView4(){
    User user = new User();
    user.setId(1L);
    user.setName("张三");
    View view = new InternalResourceView("/view.jsp");
    ModelAndView modelAndView = new ModelAndView(view);
    modelAndView.addObject("user",user);
    return modelAndView;
}

@RequestMapping("/modelAndView5")
public ModelAndView modelAndView5(){
    User user = new User();
    user.setId(1L);
    user.setName("张三");
    Map<String,User> map = new HashMap<>();
    map.put("user",user);
    ModelAndView modelAndView = new ModelAndView("view",map);
    return modelAndView;
}

@RequestMapping("/modelAndView6")
public ModelAndView modelAndView6(){
    User user = new User();
    user.setId(1L);
    user.setName("张三");
    Map<String,User> map = new HashMap<>();
    map.put("user",user);
    View view = new InternalResourceView("/view.jsp");
    ModelAndView modelAndView = new ModelAndView(view,map);
    return modelAndView;
}

@RequestMapping("/modelAndView7")
public ModelAndView modelAndView7(){
    User user = new User();
    user.setId(1L);
    user.setName("张三");
    ModelAndView modelAndView = new ModelAndView("view","user",user);
    return modelAndView;
}

@RequestMapping("/modelAndView8")
public ModelAndView modelAndView8(){
    User user = new User();
    user.setId(1L);
    user.setName("张三");
    View view = new InternalResourceView("/view.jsp");
    ModelAndView modelAndView = new ModelAndView(view,"user",user);
    return modelAndView;
}
```

4、HttpServletRequest

```java
@RequestMapping("/request")
public String request(HttpServletRequest request){
    User user = new User();
    user.setId(1L);
    user.setName("张三");
    request.setAttribute("user",user);
    return "view";
}
```

5、@ModelAttribute

- 定义一个方法，该方法专门用来返回要填充到模型数据中的对象。

```java
@ModelAttribute
public User getUser(){
    User user = new User();
    user.setId(1L);
    user.setName("张三");
    return user;
}
```

```java
@ModelAttribute
public void getUser(Map<String,User> map){
    User user = new User();
    user.setId(1L);
    user.setName("张三");
    map.put("user",user);
}
```

```java
@ModelAttribute
public void getUser(Model model){
    User user = new User();
    user.setId(1L);
    user.setName("张三");
    model.addAttribute("user",user);
}
```

- 业务方法中无需再处理模型数据，只需返回视图即可。

```java
@RequestMapping("/modelAttribute")
public String modelAttribute(){
    return "view";
}
```

> 将模型数据绑定到 session 对象

1、直接使用原生的 Servlet API。

```java
@RequestMapping("/session")
public String session(HttpServletRequest request){
    HttpSession session = request.getSession();
    User user = new User();
    user.setId(1L);
    user.setName("张三");
    session.setAttribute("user",user);
    return "view";
}

@RequestMapping("/session2")
public String session2(HttpSession session){
    User user = new User();
    user.setId(1L);
    user.setName("张三");
    session.setAttribute("user",user);
    return "view";
}
```

2、@SessionAttribute

```java
@SessionAttributes(value = {"user","address"}) //直接给类添加注解
public class ViewHandler {
}
```

对于 ViewHandler 中的所有业务方法，只要向 request 中添加了 key = "user"、key = "address" 的对象时，Spring MVC 会自动将该数据添加到 session 中，保存 key 不变。

```java
@SessionAttributes(types = {User.class,Address.class})
public class ViewHandler {
}
```

对于 ViewHandler 中的所有业务方法，只要向 request 中添加了数据类型是 User 、Address 的对象时，Spring MVC 会自动将该数据添加到 session 中，保存 key 不变。

> 将模型数据绑定到 application 对象

```java
@RequestMapping("/application")
public String application(HttpServletRequest request){
    ServletContext application = request.getServletContext();
    User user = new User();
    user.setId(1L);
    user.setName("张三");
    application.setAttribute("user",user);
    return "view";
}
```



### Spring MVC 自定义数据转换器

数据转换器是指将客户端 HTTP 请求中的参数转换为业务方法中定义的形参，自定义表示开发者可以自主设计转换的方式，HandlerApdter 已经提供了通用的转换，String 转 int，String 转 double，表单数据的封装等，但是在特殊的业务场景下，HandlerAdapter 无法进行转换，就需要开发者自定义转换器。

客户端输入 String 类型的数据 "2019-03-03"，自定义转换器将该数据转为 Date 类型的对象。

- 创建 DateConverter 转换器，实现 Conveter 接口。

```java
package com.southwind.converter;

import org.springframework.core.convert.converter.Converter;

import java.text.ParseException;
import java.text.SimpleDateFormat;
import java.util.Date;

public class DateConverter implements Converter<String, Date> {

    private String pattern;

    public DateConverter(String pattern){
        this.pattern = pattern;
    }

    @Override
    public Date convert(String s) {
        SimpleDateFormat simpleDateFormat = new SimpleDateFormat(this.pattern);
        Date date = null;
        try {
            date = simpleDateFormat.parse(s);
        } catch (ParseException e) {
            e.printStackTrace();
        }
        return date;
    }
}
```

- springmvc.xml 配置转换器。

```xml
<!-- 配置自定义转换器 -->
<bean id="conversionService" class="org.springframework.context.support.ConversionServiceFactoryBean">
    <property name="converters">
        <list>
            <bean class="com.southwind.converter.DateConverter">
                <constructor-arg type="java.lang.String" value="yyyy-MM-dd"></constructor-arg>
            </bean>
        </list>
    </property>
</bean>

<mvc:annotation-driven conversion-service="conversionService">
    <!-- 消息转换器 -->
    <mvc:message-converters register-defaults="true">
        <bean class="org.springframework.http.converter.StringHttpMessageConverter">
            <property name="supportedMediaTypes" value="text/html;charset=UTF-8"></property>
        </bean>
        <!-- 配置fastjson -->
        <bean class="com.alibaba.fastjson.support.spring.FastJsonHttpMessageConverter4"></bean>
    </mvc:message-converters>
</mvc:annotation-driven>
```

- JSP

```jsp
<%--
  Created by IntelliJ IDEA.
  User: southwind
  Date: 2019-03-14
  Time: 14:47
  To change this template use File | Settings | File Templates.
--%>
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>Title</title>
</head>
<body>
    <form action="/converter/date" method="post">
        请输入日期:<input type="text" name="date"/>(yyyy-MM-dd)<br/>
        <input type="submit" value="提交"/>
    </form>
</body>
</html>
```

- Handler

```java
package com.southwind.controller;

import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

import java.util.Date;

@RestController
@RequestMapping("/converter")
public class ConverterHandler {

    @RequestMapping("/date")
    public String date(Date date){
        return date.toString();
    }
}
```

String 转 Student

StudentConverter

```java
package com.southwind.converter;

import com.southwind.entity.Student;
import org.springframework.core.convert.converter.Converter;

public class StudentConverter implements Converter<String, Student> {
    @Override
    public Student convert(String s) {
        String[] args = s.split("-");
        Student student = new Student();
        student.setId(Long.parseLong(args[0]));
        student.setName(args[1]);
        student.setAge(Integer.parseInt(args[2]));
        return student;
    }
}
```

springmvc.xml

```xml
<!-- 配置自定义转换器 -->
<bean id="conversionService" class="org.springframework.context.support.ConversionServiceFactoryBean">
    <property name="converters">
        <list>
            <bean class="com.southwind.converter.DateConverter">
                <constructor-arg type="java.lang.String" value="yyyy-MM-dd"></constructor-arg>
            </bean>
            <bean class="com.southwind.converter.StudentConverter"></bean>
        </list>
    </property>
</bean>

<mvc:annotation-driven conversion-service="conversionService">
    <!-- 消息转换器 -->
    <mvc:message-converters register-defaults="true">
        <bean class="org.springframework.http.converter.StringHttpMessageConverter">
            <property name="supportedMediaTypes" value="text/html;charset=UTF-8"></property>
        </bean>
        <!-- 配置fastjson -->
        <bean class="com.alibaba.fastjson.support.spring.FastJsonHttpMessageConverter4"></bean>
    </mvc:message-converters>
</mvc:annotation-driven>
```

JSP

```jsp
<%--
  Created by IntelliJ IDEA.
  User: southwind
  Date: 2019-03-14
  Time: 15:23
  To change this template use File | Settings | File Templates.
--%>
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>Title</title>
</head>
<body>
    <form action="/converter/student" method="post">
        请输入学生信息：<input type="text" name="student"/>(id-name-age)<br/>
        <input type="submit" value="提交"/>
    </form>
</body>
</html>
```

Handler

```java
@RequestMapping("/student")
public String student(Student student){
    return student.toString();
}
```



### Spring MVC REST

REST：Representational State Transfer，资源表现层状态转换，是目前比较主流的一种互联网软件架构，它结构清晰、标准规范、易于理解、便于扩展。

- 资源（Resource）

网络上的一个实体，或者说网络中存在的一个具体信息，一段文本、一张图片、一首歌曲、一段视频等等，总之就是一个具体的存在。可以用一个 URI（统一资源定位符）指向它，每个资源都有对应的一个特定的 URI，要获取该资源时，只需要访问对应的 URI 即可。

- 表现层（Representation）

资源具体呈现出来的形式，比如文本可以用 txt 格式表示，也可以用 HTML、XML、JSON等格式来表示。

- 状态转换（State Transfer）

客户端如果希望操作服务器中的某个资源，就需要通过某种方式让服务端发生状态转换，而这种转换是建立在表现层之上的，所有叫做"表现层状态转换"。



#### 特点

- URL 更加简洁。
- 有利于不同系统之间的资源共享，只需要遵守一定的规范，不需要进行其他配置即可实现资源共享。



#### 如何使用

REST 具体操作就是 HTTP 协议中四个表示操作方式的动词分别对应 CRUD 基本操作。

GET 用来表示获取资源。

POST 用来表示新建资源。

PUT 用来表示修改资源。

DELETE 用来表示删除资源。



Handler

```java
package com.southwind.controller;

import com.southwind.entity.Student;
import com.southwind.entity.User;
import com.southwind.repository.StudentRepository;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Repository;
import org.springframework.web.bind.annotation.*;

import javax.servlet.http.HttpServletResponse;
import java.util.Collection;

@RestController
@RequestMapping("/rest")
public class RESTHandeler {

    @Autowired
    private StudentRepository studentRepository;

    @GetMapping("/findAll")
    public Collection<Student> findAll(HttpServletResponse response){
        response.setContentType("text/json;charset=UTF-8");
        return studentRepository.findAll();
    }

    @GetMapping("/findById/{id}")
    public Student findById(@PathVariable("id") long id){
        return studentRepository.findById(id);
    }

    @PostMapping("/save")
    public void save(@RequestBody Student student){
        studentRepository.saveOrUpdate(student);
    }

    @PutMapping("/update")
    public void update(@RequestBody Student student){
        studentRepository.saveOrUpdate(student);
    }

    @DeleteMapping("/deleteById/{id}")
    public void deleteById(@PathVariable("id") long id){
        studentRepository.deleteById(id);
    }

}
```

StudentRepository

```java
package com.southwind.repository;

import com.southwind.entity.Student;

import java.util.Collection;

public interface StudentRepository {
    public Collection<Student> findAll();
    public Student findById(long id);
    public void saveOrUpdate(Student student);
    public void deleteById(long id);
}
```

StudentRepositoryImpl

```java
package com.southwind.repository.impl;

import com.southwind.entity.Student;
import com.southwind.repository.StudentRepository;
import org.springframework.stereotype.Repository;

import java.util.Collection;
import java.util.HashMap;
import java.util.Map;

@Repository
public class StudentRepositoryImpl implements StudentRepository {

    private static Map<Long,Student> studentMap;

    static{
        studentMap = new HashMap<>();
        studentMap.put(1L,new Student(1L,"张三",22));
        studentMap.put(2L,new Student(2L,"李四",23));
        studentMap.put(3L,new Student(3L,"王五",24));
    }

    @Override
    public Collection<Student> findAll() {
        return studentMap.values();
    }

    @Override
    public Student findById(long id) {
        return studentMap.get(id);
    }

    @Override
    public void saveOrUpdate(Student student) {
        studentMap.put(student.getId(),student);
    }

    @Override
    public void deleteById(long id) {
        studentMap.remove(id);
    }
}
```



### Spring MVC 文件上传下载

> 单文件上传

底层是使用 Apache fileupload 组件完成上传，Spring MVC 对这种方式进行了封装。

- pom.xml

```xml
<dependency>
    <groupId>commons-io</groupId>
    <artifactId>commons-io</artifactId>
    <version>2.5</version>
</dependency>

<dependency>
    <groupId>commons-fileupload</groupId>
    <artifactId>commons-fileupload</artifactId>
    <version>1.3.3</version>
</dependency>
```

- JSP

```jsp
<%--
  Created by IntelliJ IDEA.
  User: southwind
  Date: 2019-03-15
  Time: 09:09
  To change this template use File | Settings | File Templates.
--%>
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<%@ page isELIgnored="false" %>
<html>
<head>
    <title>Title</title>
</head>
<body>
    <form action="/file/upload" method="post" enctype="multipart/form-data">
        <input type="file" name="img"/>
        <input type="submit" value="上传"/>
    </form>
    <img src="${path}">
</body>
</html>
```

1、input 的 type 设置为 file。

2、form 的 method 设置为 post（get 请求只能将文件名传给服务器）

3、from 的 enctype 设置为 multipart-form-data（如果不设置只能将文件名传给服务器）

- Handler

```java
package com.southwind.controller;

import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.PostMapping;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.multipart.MultipartFile;

import javax.servlet.http.HttpServletRequest;
import java.io.File;
import java.io.IOException;

@Controller
@RequestMapping("/file")
public class FileHandler {

    @PostMapping("/upload")
    public String upload(MultipartFile img, HttpServletRequest request){
        if(img.getSize()>0){
            //获取保存上传文件的file路径
            String path = request.getServletContext().getRealPath("file");
            //获取上传的文件名
            String name = img.getOriginalFilename();
            File file = new File(path,name);
            try {
                img.transferTo(file);
                //保存上传之后的文件路径
                request.setAttribute("path","/file/"+name);
            } catch (IOException e) {
                e.printStackTrace();
            }
        }
        return "upload";
    }
}
```

- springmvc.xml

```xml
<!-- 配置上传组件 -->
<bean id="multipartResolver" class="org.springframework.web.multipart.commons.CommonsMultipartResolver"></bean>
```

- web.xml 添加如下配置，否则客户端无法访问 png

```xml
<servlet-mapping>
    <servlet-name>default</servlet-name>
    <url-pattern>*.png</url-pattern>
</servlet-mapping>
```

> 多文件上传

pom.xml

```xml
<dependency>
    <groupId>jstl</groupId>
    <artifactId>jstl</artifactId>
    <version>1.2</version>
</dependency>

<dependency>
    <groupId>taglibs</groupId>
    <artifactId>standard</artifactId>
    <version>1.1.2</version>
</dependency>
```

JSP

```jsp
<%--
  Created by IntelliJ IDEA.
  User: southwind
  Date: 2019-03-15
  Time: 09:32
  To change this template use File | Settings | File Templates.
--%>
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<%@ page isELIgnored="false" %>
<%@ taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core" %>
<html>
<head>
    <title>Title</title>
</head>
<body>
    <form action="/file/uploads" method="post" enctype="multipart/form-data">
        file1:<input type="file" name="imgs"/><br/>
        file2:<input type="file" name="imgs"/><br/>
        file3:<input type="file" name="imgs"><br/>
        <input type="submit" value="上传"/>
    </form>
    <c:forEach items="${files}" var="file" >
        <img src="${file}" width="300px">
    </c:forEach>
</body>
</html>
```

Handler

```java
@PostMapping("/uploads")
public String uploads(MultipartFile[] imgs,HttpServletRequest request){
    List<String> files = new ArrayList<>();
    for (MultipartFile img:imgs){
        if(img.getSize()>0){
            //获取保存上传文件的file路径
            String path = request.getServletContext().getRealPath("file");
            //获取上传的文件名
            String name = img.getOriginalFilename();
            File file = new File(path,name);
            try {
                img.transferTo(file);
                //保存上传之后的文件路径
                files.add("/file/"+name);
            } catch (IOException e) {
                e.printStackTrace();
            }
        }
    }
    request.setAttribute("files",files);
    return "uploads";
}
```



> 下载

- JSP

```jsp
<%--
  Created by IntelliJ IDEA.
  User: southwind
  Date: 2019-03-15
  Time: 10:36
  To change this template use File | Settings | File Templates.
--%>
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>Title</title>
</head>
<body>
    <a href="/file/download/1">1.png</a>
    <a href="/file/download/2">2.png</a>
    <a href="/file/download/3">3.png</a>
</body>
</html>
```

- Handler

```java
@GetMapping("/download/{name}")
public void download(@PathVariable("name") String name, HttpServletRequest request, HttpServletResponse response){
    if(name != null){
        name += ".png";
        String path = request.getServletContext().getRealPath("file");
        File file = new File(path,name);
        OutputStream outputStream = null;
        if(file.exists()){
            response.setContentType("application/forc-download");
            response.setHeader("Content-Disposition","attachment;filename="+name);
            try {
                outputStream = response.getOutputStream();
                outputStream.write(FileUtils.readFileToByteArray(file));
                outputStream.flush();
            } catch (IOException e) {
                e.printStackTrace();
            } finally {
                if(outputStream != null){
                    try {
                        outputStream.close();
                    } catch (IOException e) {
                        e.printStackTrace();
                    }
                }
            }
        }
    }
}
```



### Spring MVC 表单标签库

- Handler

```java
@GetMapping("/get")
public ModelAndView get(){
    ModelAndView modelAndView = new ModelAndView("tag");
    Student student = new Student(1L,"张三",22);
    modelAndView.addObject("student",student);
    return modelAndView;
}
```

- JSP

```jsp
<%--
  Created by IntelliJ IDEA.
  User: southwind
  Date: 2019-03-15
  Time: 10:53
  To change this template use File | Settings | File Templates.
--%>
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<%@ page isELIgnored="false" %>
<%@ taglib prefix="form" uri="http://www.springframework.org/tags/form" %>
<html>
<head>
    <title>Title</title>
</head>
<body>
    <h1>学生信息</h1>
    <form:form modelAttribute="student">
        学生ID：<form:input path="id"/><br/>
        学生姓名：<form:input path="name"/><br/>
        学生年龄：<form:input path="age"/><br/>
        <input type="submit" value="提交"/>
    </form:form>
</body>
</html>
```

1、JSP 页面导入 Spring MVC 表单标签库，与导入 JSTL 标签库的语法非常相似，前缀 prefix 可以自定义，通常定义为 form。

```jsp
<%@ taglib prefix="form" uri="http://www.springframework.org/tags/form" %>
```

2、将 form 表单与模型数据进行绑定，通过 modelAttribute 属性完成绑定，将 modelAttribute 的值设置为模型数据对应的 key 值。

```java
Handeler:modelAndView.addObject("student",student);
JSP:<form:form modelAttribute="student">
```

3、form 表单完成绑定之后，将模型数据的值取出绑定到不同的标签中，通过设置标签的 path 属性完成，将 path 属性的值设置为模型数据对应的属性名即可。

```jsp
学生ID：<form:input path="id"/><br/>
学生姓名：<form:input path="name"/><br/>
学生年龄：<form:input path="age"/><br/>
```

#### 常用的表单标签

- from

```jsp
<form:from modelAttribute="student"/>
```

渲染的是 HTML 中的`<form></from>`，通过 modelAttribute 属性绑定具体的模型数据。

- input

```jsp
<form:input path="name"/>
```

渲染的是 HTML 中的 `<input type="text"/>`，from 标签绑定的是模型数据，input 标签绑定的是模型数据中的属性值，通过 path 属性可以与模型数据中的属性名对应，并且支持及联操作。

```jsp
<from:input path="address.name"/>
```

- password

```jsp
<form:password path="password"/>
```

渲染的是 HTML 中的 `<input type="password"/>`，通过 path 属性与模型数据的属性值进行绑定，password 标签的值不会在页面显示。

- checkbox

```jsp
<form:checkbox path="hobby" value="读书"/>
```

```java
student.setFlag(false);
```

```jsp
checkbox：<form:checkbox path="flag" value="flag"></form:checkbox><br/>
```

渲染的是 HTML 中的 `<input type="checkbox"/>`，通过 path 与模型数据的属性值进行绑定，可以绑定 boolean、数组和集合。

如果绑定 boolean 值，若该变量的值为 true，则表示该复选框选中，否则表示不选中。

如果绑定数组或者集合，数组/集合中的元素等于 checkbox 的 value 值，则选中。

```java
student.setHobby(Arrays.asList("读书","看电影","玩游戏"));
modelAndView.addObject("student",student);
```

```jsp
爱好：<form:checkbox path="hobby" value="摄影"></form:checkbox>摄影<br/>
<form:checkbox path="hobby" value="读书"></form:checkbox>读书<br/>
<form:checkbox path="hobby" value="听音乐"></form:checkbox>听音乐<br/>
<form:checkbox path="hobby" value="看电影"></form:checkbox>看电影<br/>
<form:checkbox path="hobby" value="旅游"></form:checkbox>旅游<br/>
<form:checkbox path="hobby" value="玩游戏"></form:checkbox>玩游戏<br/>
<input type="submit" value="提交"/>
```

- checkboxes

```jsp
<form:checkboxes items=${student.hobby} path="selecHobby"/>
```

渲染的是 HTML 中的一组 `<input type="checkbox"/>`，是对 `<form:checkbox/>` 的一种简化，需要结合 items 和 path 属性来使用，items 绑定被遍历的集合或数组，path 绑定被选中的集合或数组，可以这样理解，items 为全部可选集合，path 为默认的选中集合。

```java
student.setHobby(Arrays.asList("摄影","读书","听音乐","看电影","旅游","玩游戏"));
student.setSelectHobby(Arrays.asList("摄影","读书","听音乐"));
modelAndView.addObject("student",student);
```

```jsp
爱好：<form:checkboxes path="selectHobby" items="${student.hobby}"/><br/>
```

需要注意的是 path 可以直接绑定模型数据的属性值，items 则需要通过 EL 表达式的形式从域对象中获取数据，不能直接写属性名。

- rabiobutton

```jsp
<from:radiobutton path="radioId" value="0"/>
```

渲染的是 HTML 中的一个 `<input type="radio"/>`，绑定的数据与标签的 value 值相等则为选中，否则不选中。

```java
student.setRadioId(1);
modelAndView.addObject("student",student);
```

```jsp
radiobutton:<form:radiobutton path="radioId" value="1"/>radiobutton<br/>
```

- radiobuttons

```jsp
<form:radiobuttons itmes="${student.grade}" path="selectGrade"/>
```

渲染的是 HTML 中的一组 `<input type="radio"/>`，这里需要结合 items 和 path 两个属性来使用，items 绑定被遍历的集合或数组，path 绑定被选中的值，items 为全部的可选类型，path 为默认选中的选项，用法与 `<form:checkboxes/>` 一致。

```java
Map<Integer,String> gradeMap = new HashMap<>();
gradeMap.put(1,"一年级");
gradeMap.put(2,"二年级");
gradeMap.put(3,"三年级");
gradeMap.put(4,"四年级");
gradeMap.put(5,"五年级");
gradeMap.put(6,"六年级");
student.setGradeMap(gradeMap);
student.setSelectGrade(3);
modelAndView.addObject("student",student);
```

```jsp
学生年级：<form:radiobuttons items="${student.gradeMap}" path="selectGrade"/><br/>
```

- select

```jsp
<form:select items="${student.citys}" path="selectCity"/>
```

渲染的是 HTML 中的一个 `<select/>` 标签，需要结合 items 和 path 两个属性来使用，items 绑定被遍历的集合或数组，path 绑定被选中的值，用法与 `<from:radiobuttons/>`一致。

```java
Map<Integer,String> cityMap = new HashMap<>();
cityMap.put(1,"北京");
cityMap.put(2,"上海");
cityMap.put(3,"广州");
cityMap.put(4,"深圳");
student.setCityMap(cityMap);
student.setSelectCity(3);
modelAndView.addObject("student",student);
```

```jsp
所在城市：<form:select items="${student.cityMap}" path="selectCity"></form:select><br/>
```

- options

`form:select` 结合 `form:options` 的使用，`from:select` 只定义 path 属性，在 `form:select` 标签内部添加一个子标签 `form:options` ，设置 items 属性，获取被遍历的集合。

```jsp
所在城市：<form:select path="selectCity">
  				<form:options items="${student.cityMap}"></form:options>
				</form:select><br/>
```

- option

  `form:select` 结合 `form:option` 的使用，`from:select` 定义 path 属性，给每一个 `form:option` 设置 value 值，path 的值与哪个 value 值相等，该项默认选中。

```jsp
所在城市：<form:select path="selectCity">
            <form:option value="1">杭州</form:option>
            <form:option value="2">成都</form:option>
            <form:option value="3">西安</form:option>
        </form:select><br/>
```

- textarea

渲染的是 HTML 中的一个 `<textarea/>` ，path 绑定模型数据的属性值，作为文本输入域的默认值。

```java
student.setIntroduce("你好，我是...");
modelAndView.addObject("student",student);
```

```jsp
信息：<form:textarea path="introduce"/><br/>
```

- errors

处理错误信息，一般用在数据校验，该标签需要结合 Spring MVC 的验证器结合起来使用。

### Spring MVC 数据校验

Spring MVC 提供了两种数据校验的方式：1、基于 Validator 接口。2、使用 Annotation JSR - 303 标准进行校验。

基于 Validator 接口的方式需要自定义 Validator 验证器，每一条数据的验证规则需要开发者手动完成，使用 Annotation JSR - 303 标准则不需要自定义验证器，通过注解的方式可以直接在实体类中添加每个属性的验证规则，这种方式更加方便，实际开发中推荐使用。

> 基于 Validator 接口

- 实体类 Account

```java
package com.southwind.entity;

import lombok.Data;

@Data
public class Account {
    private String name;
    private String password;
}
```

- 自定义验证器 AccountValidator，实现 Validator 接口。

```java
package com.southwind.validator;

import com.southwind.entity.Account;
import org.springframework.validation.Errors;
import org.springframework.validation.ValidationUtils;
import org.springframework.validation.Validator;

public class AccountValidator implements Validator {
    @Override
    public boolean supports(Class<?> aClass) {
        return Account.class.equals(aClass);
    }

    @Override
    public void validate(Object o, Errors errors) {
        ValidationUtils.rejectIfEmpty(errors,"name",null,"姓名不能为空");
        ValidationUtils.rejectIfEmpty(errors,"password",null,"密码不能为空");
    }
}
```

- 控制器

```java
package com.southwind.controller;

import com.southwind.entity.Account;
import org.springframework.stereotype.Controller;
import org.springframework.ui.Model;
import org.springframework.validation.BindingResult;
import org.springframework.validation.annotation.Validated;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.PostMapping;
import org.springframework.web.bind.annotation.RequestMapping;

@Controller
@RequestMapping("/validator")
public class ValidatorHandler {

    @GetMapping("/login")
    public String login(Model model){
        model.addAttribute("account",new Account());
        return "login";
    }

    @PostMapping("/login")
    public String login(@Validated Account account, BindingResult bindingResult){
        if(bindingResult.hasErrors()){
            return "login";
        }
        return "index";
    }
}
```

- springmvc.xml 配置验证器。

```xml
<bean id="accountValidator" class="com.southwind.validator.AccountValidator"></bean>
<mvc:annotation-driven validator="accountValidator"></mvc:annotation-driven>
```

- JSP

```jsp
<%--
  Created by IntelliJ IDEA.
  User: southwind
  Date: 2019-03-18
  Time: 10:31
  To change this template use File | Settings | File Templates.
--%>
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<%@ page isELIgnored="false" %>
<%@ taglib prefix="form" uri="http://www.springframework.org/tags/form" %>
<%@ taglib prefix="from" uri="http://www.springframework.org/tags/form" %>
<html>
<head>
    <title>Title</title>
</head>
<body>
    <form:form modelAttribute="account" action="/validator/login" method="post">
        姓名：<form:input path="name"/><from:errors path="name"></from:errors><br/>
        密码：<form:input path="password"/><from:errors path="password"></from:errors><br/>
        <input type="submit" value="登录"/>
    </form:form>
</body>
</html>
```

> Annotation JSR - 303 标准

使用 Annotation JSR - 303 标准进行验证，需要导入支持这种标准的依赖 jar 文件，这里我们使用 Hibernate Validator。

- pom.xml

```xml
<!-- JSR-303 -->
<dependency>
  <groupId>org.hibernate</groupId>
  <artifactId>hibernate-validator</artifactId>
  <version>5.3.6.Final</version>
</dependency>

<dependency>
  <groupId>javax.validation</groupId>
  <artifactId>validation-api</artifactId>
  <version>2.0.1.Final</version>
</dependency>

<dependency>
  <groupId>org.jboss.logging</groupId>
  <artifactId>jboss-logging</artifactId>
  <version>3.3.2.Final</version>
</dependency>
```

- 通过注解的方式直接在实体类中添加相关的验证规则。

```java
package com.southwind.entity;

import lombok.Data;
import org.hibernate.validator.constraints.Email;
import org.hibernate.validator.constraints.NotEmpty;
import javax.validation.constraints.Pattern;
import javax.validation.constraints.Size;

@Data
public class Person {
    @NotEmpty(message = "用户名不能为空")
    private String username;
    @Size(min = 6,max = 12,message = "密码6-12位")
    private String password;
    @Email(regexp = "^[a-zA-Z0-9_.-]+@[a-zA-Z0-9-]+(\\\\.[a-zA-Z0-9-]+)*\\\\.[a-zA-Z0-9]{2,6}$",message = "请输入正确的邮箱格式")
    private String email;
    @Pattern(regexp = "^((13[0-9])|(14[5|7])|(15([0-3]|[5-9]))|(18[0,5-9]))\\\\\\\\d{8}$",message = "请输入正确的电话")
    private String phone;
}
```

- ValidatorHandler

```java
@GetMapping("/register")
public String register(Model model){
    model.addAttribute("person",new Person());
    return "register";
}

@PostMapping("/register")
public String register(@Valid Person person, BindingResult bindingResult){
    if(bindingResult.hasErrors()){
        return "register";
    }
    return "index";
}
```

- springmvc.xml

```xml
<mvc:annotation-driven />
```

- JSP

```jsp
<%--
  Created by IntelliJ IDEA.
  User: southwind
  Date: 2019-03-18
  Time: 11:29
  To change this template use File | Settings | File Templates.
--%>
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<%@ page isELIgnored="false" %>
<%@ taglib prefix="form" uri="http://www.springframework.org/tags/form" %>
<html>
<head>
    <title>Title</title>
</head>
<body>
    <form:form modelAttribute="person" action="/validator/register2" method="post">
        用户名：<form:input path="username"></form:input><form:errors path="username"/><br/>
        密码：<form:password path="password"></form:password><form:errors path="password"/><br/>
        邮箱：<form:input path="email"></form:input><form:errors path="email"/><br/>
        电话：<form:input path="phone"></form:input><form:errors path="phone"/><br/>
        <input type="submit" value="提交"/>
    </form:form>
</body>
</html>
```

校验规则详解：

@Null					被注解的元素必须为null

@NotNull				  被注解的元素不能为null

@Min(value)			     被注解的元素必须是一个数字，其值必须大于等于指定的最小值

@Max(value)			    被注解的元素必须是一个数字，其值必须小于于等于指定的最大值	

@Email				     被注解的元素必须是电子邮箱地址

@Pattern				  被注解的元素必须符合对应的正则表达式

@Length				   被注解的元素的大小必须在指定的范围内

@NotEmpty			      被注解的字符串的值必须非空

Null 和 Empty 是不同的结果，String str = null，str 是 null，String str = ""，str 不是 null，其值为空。

## SSM框架整合

Spring + Spring MVC + MyBatis

Spring MVC 负责实现 MVC 设计模式，MyBatis 负责数据持久层，Spring 负责管理 Spring MVC 和 MyBatis 相关对象的创建和依赖注入。

- 创建 Maven 工程（选择maven-archetype-webapp），pom.xml

```xml
<dependencies>
  <!-- SpringMVC -->
  <dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-webmvc</artifactId>
    <version>5.0.11.RELEASE</version>
  </dependency>

  <!-- Spring JDBC -->
  <dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-jdbc</artifactId>
    <version>5.0.11.RELEASE</version>
  </dependency>

  <!-- Spring AOP -->
  <dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-aop</artifactId>
    <version>5.0.11.RELEASE</version>
  </dependency>
  <dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-aspects</artifactId>
    <version>5.0.11.RELEASE</version>
  </dependency>

  <!-- MyBatis -->
  <dependency>
    <groupId>org.mybatis</groupId>
    <artifactId>mybatis</artifactId>
    <version>3.4.5</version>
  </dependency>

  <!-- MyBatis整合Spring -->
  <dependency>
    <groupId>org.mybatis</groupId>
    <artifactId>mybatis-spring</artifactId>
    <version>1.3.1</version>
  </dependency>

  <!-- MySQL驱动 -->
  <dependency>
    <groupId>mysql</groupId>
    <artifactId>mysql-connector-java</artifactId>
    <version>8.0.11</version>
  </dependency>

  <!-- C3P0 -->
  <dependency>
    <groupId>c3p0</groupId>
    <artifactId>c3p0</artifactId>
    <version>0.9.1.2</version>
  </dependency>

  <!-- JSTL -->
  <dependency>
    <groupId>jstl</groupId>
    <artifactId>jstl</artifactId>
    <version>1.2</version>
  </dependency>

  <!-- ServletAPI -->
  <dependency>
    <groupId>javax.servlet</groupId>
    <artifactId>javax.servlet-api</artifactId>
    <version>3.1.0</version>
  </dependency>
  
  <dependency>
    <groupId>org.projectlombok</groupId>
    <artifactId>lombok</artifactId>
    <version>1.18.6</version>
    <scope>provided</scope>
  </dependency>
</dependencies>
```

- web.xml 中配置 SpringMVC、Spring、字符编码过滤器、加载静态资源。

```xml
<!DOCTYPE web-app PUBLIC
 "-//Sun Microsystems, Inc.//DTD Web Application 2.3//EN"
 "http://java.sun.com/dtd/web-app_2_3.dtd" >

<web-app>
  <display-name>Archetype Created Web Application</display-name>
  <!-- 启动Spring -->
  <context-param>
    <param-name>contextConfigLocation</param-name>
    <param-value>classpath:spring.xml</param-value>
  </context-param>
  <listener>
    <listener-class>org.springframework.web.context.ContextLoaderListener</listener-class>
  </listener>

  <!-- Spring MVC -->
  <servlet>
    <servlet-name>dispatcherServlet</servlet-name>
    <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
    <init-param>
      <param-name>contextConfigLocation</param-name>
      <param-value>classpath:springmvc.xml</param-value>
    </init-param>
  </servlet>
  
  <servlet-mapping>
    <servlet-name>dispatcherServlet</servlet-name>
    <url-pattern>/</url-pattern>
  </servlet-mapping>

  <!-- 字符编码过滤器 -->
  <filter>
    <filter-name>characterEncodingFilter</filter-name>
    <filter-class>org.springframework.web.filter.CharacterEncodingFilter</filter-class>
    <init-param>
      <param-name>encoding</param-name>
      <param-value>UTF-8</param-value>
    </init-param>
  </filter>
  <filter-mapping>
    <filter-name>characterEncodingFilter</filter-name>
    <url-pattern>/*</url-pattern>
  </filter-mapping>
  
  <!-- 加载静态资源 -->
  <servlet-mapping>
    <servlet-name>default</servlet-name>
    <url-pattern>*.js</url-pattern>
  </servlet-mapping>
  <servlet-mapping>
    <servlet-name>default</servlet-name>
    <url-pattern>*.css</url-pattern>
  </servlet-mapping>
  <servlet-mapping>
    <servlet-name>default</servlet-name>
    <url-pattern>*.jpg</url-pattern>
  </servlet-mapping>
</web-app>
```

- 在 spring.xml 中配置 MyBatis 和 Spring 的整合。

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:p="http://www.springframework.org/schema/p"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans-3.2.xsd
	http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context-4.3.xsd
">

    <!-- 整合MyBatis -->
    <bean id="dataSource" class="com.mchange.v2.c3p0.ComboPooledDataSource">
        <property name="user" value="root"></property>
        <property name="password" value="root"></property>
        <property name="jdbcUrl" value="jdbc:mysql://localhost:3306/test?useUnicode=true&amp;characterEncoding=UTF-8"></property>
        <property name="driverClass" value="com.mysql.cj.jdbc.Driver"></property>
        <property name="initialPoolSize" value="5"></property>
        <property name="maxPoolSize" value="10"></property>
    </bean>

    <!-- 配置MyBatis SqlSessionFactory -->
    <bean id="sqlSessionFactory" class="org.mybatis.spring.SqlSessionFactoryBean">
        <property name="dataSource" ref="dataSource"></property>
        <property name="mapperLocations" value="classpath:com/southwind/repository/*.xml"></property>
        <property name="configLocation" value="classpath:config.xml"></property>
    </bean>

    <!-- 扫描自定义的Mapper接口 -->
    <bean class="org.mybatis.spring.mapper.MapperScannerConfigurer">
        <property name="basePackage" value="com.southwind.repository"></property>
    </bean>

</beans>
```

- config.xml 配置一些 MyBatis 辅助信息，比如打印 SQL 等。

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE configuration PUBLIC "-//mybatis.org//DTD Config 3.0//EN" "http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>
    <settings>
        <!-- 打印SQL-->
        <setting name="logImpl" value="STDOUT_LOGGING" />
    </settings>

    <typeAliases>
        <!-- 指定一个包名，MyBatis会在包名下搜索需要的JavaBean-->
        <package name="com.southwind.entity"/>
    </typeAliases>

</configuration>
```

- 配置 springmvc.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:mvc="http://www.springframework.org/schema/mvc"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans-3.0.xsd
        http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context-3.0.xsd
        http://www.springframework.org/schema/mvc http://www.springframework.org/schema/mvc/spring-mvc-3.2.xsd">

    <!-- 启动注解驱动 -->
    <mvc:annotation-driven></mvc:annotation-driven>

    <!-- 扫描业务代码 -->
    <context:component-scan base-package="com.southwind"></context:component-scan>

    <!-- 配置视图解析器 -->
    <bean class="org.springframework.web.servlet.view.InternalResourceViewResolver">
        <property name="prefix" value="/"></property>
        <property name="suffix" value=".jsp"></property>
    </bean>

</beans>
```

- 实体类

```java
package com.southwind.entity;

import lombok.Data;

@Data
public class User {
    private long id;
    private String name;
    private String password;
    private double score;
}
```

- UserRepository

```java
package com.southwind.repository;

import com.southwind.entity.User;

import java.util.List;

public interface UserRepository {
    public List<User> findAll();
}
```

- UserRepository.xml

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN" "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.southwind.repository.UserRepository">
    <select id="findAll" resultType="User">
        select * from user
    </select>
</mapper>
```

- UserService

```java
package com.southwind.service;

import com.southwind.entity.User;

import java.util.List;

public interface UserService {
    public List<User> findAll();
}
```

- UserServiceImpl

```java
package com.southwind.service.impl;

import com.southwind.entity.User;
import com.southwind.repository.UserRepository;
import com.southwind.service.UserService;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;

import java.util.List;

@Service
public class UserServiceImpl implements UserService {

    @Autowired
    private UserRepository userRepository;

    @Override
    public List<User> findAll() {
        return userRepository.findAll();
    }
}
```

- UserHandler

```java
package com.southwind.controller;

import com.southwind.service.UserService;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.servlet.ModelAndView;

@Controller
@RequestMapping("/user")
public class UserHandler {
    @Autowired
    private UserService userService;

    @GetMapping("/findAll")
    public ModelAndView findAll(){
        ModelAndView modelAndView = new ModelAndView();
        modelAndView.setViewName("index");
        modelAndView.addObject("list",userService.findAll());
        return modelAndView;
    }
}
```

## Spring Boot

Spring Boot 是一个快速开发框架，可以迅速搭建出一套基于 Spring 框架体系的应用，是 Spring Cloud 的基础。

Spring Boot 开启了各种自动装配，从而简化代码的开发，不需要编写各种配置文件，只需要引入相关依赖就可以迅速搭建一个应用。

- 特点

1、不需要 web.xml

2、不需要 springmvc.xml

3、不需要 tomcat，Spring Boot 内嵌了 tomcat

4、不需要配置 JSON 解析，支持 REST 架构

5、个性化配置非常简单

- 如何使用

1、创建 Maven 工程，导入相关依赖。

```xml
<!-- 继承父包 -->
<parent>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-parent</artifactId>
    <version>2.0.7.RELEASE</version>
</parent>

<dependencies>
  <!-- web启动jar -->
  <dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
  </dependency>
  <dependency>
    <groupId>org.projectlombok</groupId>
    <artifactId>lombok</artifactId>
    <version>1.18.6</version>
    <scope>provided</scope>
  </dependency>
</dependencies>
```

2、创建 Student 实体类

```java
package com.southwind.entity;

import lombok.Data;

@Data
public class Student {
    private long id;
    private String name;
    private int age;
}
```

3、StudentRepository

```java
package com.southwind.repository;

import com.southwind.entity.Student;

import java.util.Collection;

public interface StudentRepository {
    public Collection<Student> findAll();
    public Student findById(long id);
    public void saveOrUpdate(Student student);
    public void deleteById(long id);
}
```

4、StudentRepositoryImpl

```java
package com.southwind.repository.impl;

import com.southwind.entity.Student;
import com.southwind.repository.StudentRepository;
import org.springframework.stereotype.Repository;

import java.util.Collection;
import java.util.HashMap;
import java.util.Map;

@Repository
public class StudentRepositoryImpl implements StudentRepository {

    private static Map<Long,Student> studentMap;

    static{
        studentMap = new HashMap<>();
        studentMap.put(1L,new Student(1L,"张三",22));
        studentMap.put(2L,new Student(2L,"李四",23));
        studentMap.put(3L,new Student(3L,"王五",24));
    }

    @Override
    public Collection<Student> findAll() {
        return studentMap.values();
    }

    @Override
    public Student findById(long id) {
        return studentMap.get(id);
    }

    @Override
    public void saveOrUpdate(Student student) {
        studentMap.put(student.getId(),student);
    }

    @Override
    public void deleteById(long id) {
        studentMap.remove(id);
    }
}
```

5、StudentHandler

```java
package com.southwind.controller;

import com.southwind.entity.Student;
import com.southwind.repository.StudentRepository;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.*;

import java.util.Collection;

@RestController
@RequestMapping("/student")
public class StudentHandler {

    @Autowired
    private StudentRepository studentRepository;

    @GetMapping("/findAll")
    public Collection<Student> findAll(){
        return studentRepository.findAll();
    }

    @GetMapping("/findById/{id}")
    public Student findById(@PathVariable("id") long id){
        return studentRepository.findById(id);
    }

    @PostMapping("/save")
    public void save(@RequestBody Student student){
        studentRepository.saveOrUpdate(student);
    }

    @PutMapping("/update")
    public void update(@RequestBody Student student){
        studentRepository.saveOrUpdate(student);
    }

    @DeleteMapping("/deleteById/{id}")
    public void deleteById(@PathVariable("id") long id){
        studentRepository.deleteById(id);
    }
}
```

6、application.yml

```yaml
server:
  port: 9090
```

7、启动类

```java
package com.southwind;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

@SpringBootApplication
public class Application {
    public static void main(String[] args) {
        SpringApplication.run(Application.class,args);
    }
}
```

`@SpringBootApplication` 表示当前类是 Spring Boot 的入口，Application 类的存放位置必须是其他相关业务类的存放位置的父级。

### Spring Boot 整合 JSP

- pom.xml

```xml
<parent>
  <groupId>org.springframework.boot</groupId>
  <artifactId>spring-boot-starter-parent</artifactId>
  <version>2.0.7.RELEASE</version>
</parent>

<dependencies>
  <!-- web -->
  <dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
  </dependency>

  <!-- 整合JSP -->
  <dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-tomcat</artifactId>
  </dependency>
  <dependency>
    <groupId>org.apache.tomcat.embed</groupId>
    <artifactId>tomcat-embed-jasper</artifactId>
  </dependency>

  <!-- JSTL -->
  <dependency>
    <groupId>jstl</groupId>
    <artifactId>jstl</artifactId>
    <version>1.2</version>
  </dependency>

  <dependency>
    <groupId>org.projectlombok</groupId>
    <artifactId>lombok</artifactId>
    <version>1.18.6</version>
    <scope>provided</scope>
  </dependency>
</dependencies>
```

- 创建配置文件 application.yml

```yaml
server:
  port: 8181
spring:
  mvc:
    view:
      prefix: /
      suffix: .jsp
```

- 创建 Handler

```java
package com.southwind.controller;

import com.southwind.entity.Student;
import com.southwind.repository.StudentRepository;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.PathVariable;
import org.springframework.web.bind.annotation.PostMapping;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.servlet.ModelAndView;

@Controller
@RequestMapping("/hello")
public class HelloHandler {

    @Autowired
    private StudentRepository studentRepository;

    @GetMapping("/index")
    public ModelAndView index(){
        ModelAndView modelAndView = new ModelAndView();
        modelAndView.setViewName("index");
        modelAndView.addObject("list",studentRepository.findAll());
        return modelAndView;
    }

    @GetMapping("/deleteById/{id}")
    public String deleteById(@PathVariable("id") long id){
        studentRepository.deleteById(id);
        return "redirect:/hello/index";
    }

    @PostMapping("/save")
    public String save(Student student){
        studentRepository.saveOrUpdate(student);
        return "redirect:/hello/index";
    }

    @PostMapping("/update")
    public String update(Student student){
        studentRepository.saveOrUpdate(student);
        return "redirect:/hello/index";
    }

    @GetMapping("/findById/{id}")
    public ModelAndView findById(@PathVariable("id") long id){
        ModelAndView modelAndView = new ModelAndView();
        modelAndView.setViewName("update");
        modelAndView.addObject("student",studentRepository.findById(id));
        return modelAndView;
    }
}
```

- JSP

```jsp
<%--
  Created by IntelliJ IDEA.
  User: southwind
  Date: 2019-03-21
  Time: 12:02
  To change this template use File | Settings | File Templates.
--%>
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<%@ page isELIgnored="false" %>
<%@ taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core" %>
<html>
<head>
    <title>Title</title>
</head>
<body>
    <h1>学生信息</h1>
    <table>
        <tr>
            <th>学生编号</th>
            <th>学生姓名</th>
            <th>学生年龄</th>
            <th>操作</th>
        </tr>
        <c:forEach items="${list}" var="student">
            <tr>
                <td>${student.id}</td>
                <td>${student.name}</td>
                <td>${student.age}</td>
                <td>
                    <a href="/hello/findById/${student.id}">修改</a>
                    <a href="/hello/deleteById/${student.id}">删除</a>
                </td>
            </tr>
        </c:forEach>
    </table>
    <a href="/save.jsp">添加学生</a>
</body>
</html>
```



```jsp
<%--
  Created by IntelliJ IDEA.
  User: southwind
  Date: 2019-03-21
  Time: 12:09
  To change this template use File | Settings | File Templates.
--%>
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>Title</title>
</head>
<body>
    <form action="/hello/save" method="post">
        ID:<input type="text" name="id"/><br/>
        name:<input type="text" name="name"/><br/>
        age:<input type="text" name="age"/><br/>
        <input type="submit" value="提交"/>
    </form>
</body>
</html>
```



```jsp
<%--
  Created by IntelliJ IDEA.
  User: southwind
  Date: 2019-03-21
  Time: 12:09
  To change this template use File | Settings | File Templates.
--%>
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>Title</title>
</head>
<body>
    <form action="/hello/update" method="post">
        ID:<input type="text" name="id" value="${student.id}" readonly/><br/>
        name:<input type="text" name="name" value="${student.name}"/><br/>
        age:<input type="text" name="age" value="${student.age}"/><br/>
        <input type="submit" value="提交"/>
    </form>
</body>
</html>
```

### Spring Boot HTML

Spring Boot 可以结合 Thymeleaf 模版来整合 HTML，使用原生的 HTML 作为视图。

Thymeleaf 模版是面向 Web 和独立环境的 Java 模版引擎，能够处理 HTML、XML、JavaScript、CSS 等。

```
<p th:text="${message}"></p>
```

- pom.xml

```xml
<!-- 继承父包 -->
<parent>
  <groupId>org.springframework.boot</groupId>
  <artifactId>spring-boot-starter-parent</artifactId>
  <version>2.0.7.RELEASE</version>
</parent>

<dependencies>
  <!-- web启动jar -->
  <dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
  </dependency>

  <dependency>
    <groupId>org.projectlombok</groupId>
    <artifactId>lombok</artifactId>
    <version>1.18.6</version>
    <scope>provided</scope>
  </dependency>

  <dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-thymeleaf</artifactId>
  </dependency>
</dependencies>
```

- appliction.yml

```yaml
server:
  port: 9090
spring:
  thymeleaf:
    prefix: classpath:/templates/
    suffix: .html
    mode: HTML5
    encoding: UTF-8
```

- Handler

```java
package com.southwind.controller;

import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RequestMapping;

@Controller
@RequestMapping("/index")
public class IndexHandler {

    @GetMapping("/index")
    public String index(){
        System.out.println("index...");
        return "index";
    }
}
```

- HTML

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
    <h1>Hello World</h1>
</body>
</html>
```

如果希望客户端可以直接访问 HTML 资源，将这些资源放置在 static 路径下即可，否则必须通过 Handler 的后台映射才可以访问静态资源。

### Thymeleaf 常用语法

- 赋值、拼接

```java
@GetMapping("/index2")
public String index2(Map<String,String> map){
  map.put("name","张三");
  return "index";
}
```

```html
<p th:text="${name}"></p>
<p th:text="'学生姓名是'+${name}+2"></p>
<p th:text="|学生姓名是,${name}|"></p>
```

- 条件判断：if/unless

th:if 表示条件成立时显示内容，th:unless 表示条件不成立时显示内容

```java
@GetMapping("/if")
public String index3(Map<String,Boolean> map){
    map.put("flag",true);
    return "index";
}
```

```html
<p th:if="${flag == true}" th:text="if判断成立"></p>
<p th:unless="${flag != true}" th:text="unless判断成立"></p>
```

- 循环

```java
@GetMapping("/index")
public String index(Model model){
    System.out.println("index...");
    List<Student> list = new ArrayList<>();
    list.add(new Student(1L,"张三",22));
    list.add(new Student(2L,"李四",23));
    list.add(new Student(3L,"王五",24));
    model.addAttribute("list",list);
    return "index";
}
```

```html
<table>
  <tr>
    <th>index</th>
    <th>count</th>
    <th>学生ID</th>
    <th>学生姓名</th>
    <th>学生年龄</th>
  </tr>
  <tr th:each="student,stat:${list}" th:style="'background-color:'+@{${stat.odd}?'#F2F2F2'}">
    <td th:text="${stat.index}"></td>
    <td th:text="${stat.count}"></td>
    <td th:text="${student.id}"></td>
    <td th:text="${student.name}"></td>
    <td th:text="${student.age}"></td>
  </tr>
</table>
```

stat 是状态变量，属性：

- index 集合中元素的index（从0开始）
- count 集合中元素的count（从1开始）
- size 集合的大小
- current 当前迭代变量
- even/odd 当前迭代是否为偶数/奇数（从0开始计算）
- first 当前迭代的元素是否是第一个
- last 当前迭代的元素是否是最后一个



- URL

Thymeleaf 对于 URL 的处理是通过 `@{...}` 进行处理，结合 th:href 、th:src

```html
<h1>Hello World</h1>
<a th:href="@{http://www.baidu.com}">跳转</a>
<a th:href="@{http://localhost:9090/index/url/{na}(na=${name})}">跳转2</a>
<img th:src="${src}">
<div th:style="'background:url('+ @{${src}} +');'">
<br/>
<br/>
<br/>
</div>
```

- 三元运算

```java
@GetMapping("/eq")
public String eq(Model model){
    model.addAttribute("age",30);
    return "test";
}
```

```html
<input th:value="${age gt 30?'中年':'青年'}"/>
```

- gt great than 大于
- ge great equal 大于等于
- eq equal 等于
- lt less than 小于
- le less equal 小于等于
- ne not equal 不等于



- switch

```java
@GetMapping("/switch")
public String switchTest(Model model){
    model.addAttribute("gender","女");
    return "test";
}
```

```html
<div th:switch="${gender}">
  <p th:case="女">女</p>
  <p th:case="男">男</p>
  <p th:case="*">未知</p>
</div>
```



- 基本对象
  - `#ctx` ：上下文对象
  - `#vars`：上下文变量
  - `#locale`：区域对象
  - `#request`：HttpServletRequest 对象
  - `#response`：HttpServletResponse 对象
  - `#session`：HttpSession 对象
  - `#servletContext`：ServletContext 对象

```java
@GetMapping("/object")
public String object(HttpServletRequest request){
    request.setAttribute("request","request对象");
    request.getSession().setAttribute("session","session对象");
    return "test";
}
```

```html
<p th:text="${#request.getAttribute('request')}"></p>
<p th:text="${#session.getAttribute('session')}"></p>
<p th:text="${#locale.country}"></p>
```



- 内嵌对象

可以直接通过 # 访问。

1、dates：java.util.Date 的功能方法

2、calendars：java.util.Calendar 的功能方法

3、numbers：格式化数字

4、strings：java.lang.String 的功能方法

5、objects：Object 的功能方法

6、bools：对布尔求值的方法

7、arrays：操作数组的功能方法

8、lists：操作集合的功能方法

9、sets：操作集合的功能方法

10、maps：操作集合的功能方法

```java
@GetMapping("/util")
public String util(Model model){
    model.addAttribute("name","zhangsan");
    model.addAttribute("users",new ArrayList<>());
    model.addAttribute("count",22);
    model.addAttribute("date",new Date());
    return "test";
}
```

```html
<!-- 格式化时间 -->
<p th:text="${#dates.format(date,'yyyy-MM-dd HH:mm:sss')}"></p>
<!-- 创建当前时间，精确到天 -->
<p th:text="${#dates.createToday()}"></p>
<!-- 创建当前时间，精确到秒 -->
<p th:text="${#dates.createNow()}"></p>
<!-- 判断是否为空 -->
<p th:text="${#strings.isEmpty(name)}"></p>
<!-- 判断List是否为空 -->
<p th:text="${#lists.isEmpty(users)}"></p>
<!-- 输出字符串长度 -->
<p th:text="${#strings.length(name)}"></p>
<!-- 拼接字符串 -->
<p th:text="${#strings.concat(name,name,name)}"></p>
<!-- 创建自定义字符串 -->
<p th:text="${#strings.randomAlphanumeric(count)}"></p>
```



### Spring Boot 数据校验

```java
package com.southwind.entity;

import lombok.Data;
import org.hibernate.validator.constraints.Length;

import javax.validation.constraints.Min;
import javax.validation.constraints.NotEmpty;
import javax.validation.constraints.NotNull;

@Data
public class User {
    @NotNull(message = "id不能为空")
    private Long id;
    @NotEmpty(message = "姓名不能为空")
    @Length(min = 2,message = "姓名长度不能小于2位")
    private String name;
    @Min(value = 16,message = "年龄必须大于16岁")
    private int age;
}
```

```java
@GetMapping("/validator")
public void validatorUser(@Valid User user,BindingResult bindingResult){
  System.out.println(user);
  if(bindingResult.hasErrors()){
    List<ObjectError> list = bindingResult.getAllErrors();
    for(ObjectError objectError:list){
      System.out.println(objectError.getCode()+"-"+objectError.getDefaultMessage());
    }
  }
}
```



### Spring Boot 整合 JDBC

- pom.xml

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-jdbc</artifactId>
</dependency>

<dependency>
    <groupId>mysql</groupId>
    <artifactId>mysql-connector-java</artifactId>
    <version>8.0.11</version>
</dependency>
```

- application.yml

```yaml
server:
  port: 9090
spring:
  thymeleaf:
    prefix: classpath:/templates/
    suffix: .html
    mode: HTML5
    encoding: UTF-8
  datasource:
    url: jdbc:mysql://localhost:3306/test?useUnicode=true&characterEncoding=UTF-8
    username: root
    password: root
    driver-class-name: com.mysql.cj.jdbc.Driver
```

- User

```java
package com.southwind.entity;

import lombok.Data;
import org.hibernate.validator.constraints.Length;

import javax.validation.constraints.Min;
import javax.validation.constraints.NotEmpty;
import javax.validation.constraints.NotNull;

@Data
public class User {
    @NotNull(message = "id不能为空")
    private Long id;
    @NotEmpty(message = "姓名不能为空")
    @Length(min = 2,message = "姓名长度不能小于2位")
    private String name;
    @Min(value = 60,message = "成绩必须大于60分")
    private double score;
}
```

- UserRepository

```java
package com.southwind.repository;

import com.southwind.entity.User;

import java.util.List;

public interface UserRepository {
    public List<User> findAll();
    public User findById(long id);
    public void save(User user);
    public void update(User user);
    public void deleteById(long id);
}
```

- UserRepositoryImpl

```java
package com.southwind.repository.impl;

import com.southwind.entity.User;
import com.southwind.repository.UserRepository;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.jdbc.core.BeanPropertyRowMapper;
import org.springframework.jdbc.core.JdbcTemplate;
import org.springframework.stereotype.Repository;

import java.util.List;

@Repository
public class UserRepositoryImpl implements UserRepository {

    @Autowired
    private JdbcTemplate jdbcTemplate;

    @Override
    public List<User> findAll() {
        return jdbcTemplate.query("select * from user",new BeanPropertyRowMapper<>(User.class));
    }

    @Override
    public User findById(long id) {
        return jdbcTemplate.queryForObject("select * from user where id = ?",new Object[]{id},new BeanPropertyRowMapper<>(User.class));
    }

    @Override
    public void save(User user) {
        jdbcTemplate.update("insert into user(name,score) values(?,?)",user.getName(),user.getScore());
    }

    @Override
    public void update(User user) {
        jdbcTemplate.update("update user set name = ?,score = ? where id = ?",user.getName(),user.getScore(),user.getId());
    }

    @Override
    public void deleteById(long id) {
        jdbcTemplate.update("delete from user where id = ?",id);
    }
}
```

- Handler

```java
package com.southwind.controller;

import com.southwind.entity.User;
import com.southwind.repository.UserRepository;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.*;

import java.util.List;

@RestController
@RequestMapping("/user")
public class UserHandler {

    @Autowired
    private UserRepository userRepository;

    @GetMapping("/findAll")
    public List<User> findAll(){
        return userRepository.findAll();
    }

    @GetMapping("/findById/{id}")
    public User findById(@PathVariable("id") long id){
        return userRepository.findById(id);
    }

    @PostMapping("/save")
    public void save(@RequestBody User user){
        userRepository.save(user);
    }

    @PutMapping("/update")
    public void update(@RequestBody User user){
        userRepository.update(user);
    }

    @DeleteMapping("/deleteById/{id}")
    public void deleteById(@PathVariable("id") long id){
        userRepository.deleteById(id);
    }
}
```



### Spring Boot 整合 MyBatis

- pom.xml

```xml
<dependency>
  <groupId>org.mybatis.spring.boot</groupId>
  <artifactId>mybatis-spring-boot-starter</artifactId>
  <version>1.3.1</version>
</dependency>
```

- application.yml

```yaml
server:
  port: 9090
spring:
  thymeleaf:
    prefix: classpath:/templates/
    suffix: .html
    mode: HTML5
    encoding: UTF-8
  datasource:
    url: jdbc:mysql://localhost:3306/test?useUnicode=true&characterEncoding=UTF-8
    username: root
    password: root
    driver-class-name: com.mysql.cj.jdbc.Driver
mybatis:
  mapper-locations: classpath:/mapping/*.xml
  type-aliases-package: com.southwind.entity
```

- UserRepository

```java
package com.southwind.mapper;

import com.southwind.entity.User;

import java.util.List;

public interface UserRepository {
    public List<User> findAll(int index,int limit);
    public User findById(long id);
    public void save(User user);
    public void update(User user);
    public void deleteById(long id);
    public int count();
}
```

- UserRepository.xml

```xml
<?xml version="1.0" encoding="UTF-8" ?>
        <!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN" "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.southwind.mapper.UserRepository">

    <select id="findAll" resultType="User">
        select * from user limit #{param1},#{param2}
    </select>

    <select id="count" resultType="int">
        select count(id) from user
    </select>

    <select id="findById" parameterType="long" resultType="User">
        select * from user where id = #{id}
    </select>

    <insert id="save" parameterType="User">
        insert into user(name,score) values(#{name},#{score})
    </insert>

    <update id="update" parameterType="User">
        update user set name = #{name},score = #{score} where id = #{id}
    </update>

    <delete id="deleteById" parameterType="long">
        delete from user where id = #{id}
    </delete>
</mapper>
```

- User

```java
package com.southwind.entity;

import lombok.Data;
import org.hibernate.validator.constraints.Length;

import javax.validation.constraints.Min;
import javax.validation.constraints.NotEmpty;
import javax.validation.constraints.NotNull;

@Data
public class User {
    @NotNull(message = "id不能为空")
    private Long id;
    @NotEmpty(message = "姓名不能为空")
    @Length(min = 2,message = "姓名长度不能小于2位")
    private String name;
    @Min(value = 60,message = "成绩必须大于60分")
    private double score;
}
```

- Handler

```java
package com.southwind.controller;
import com.southwind.entity.User;
import com.southwind.mapper.UserRepository;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.*;
import org.springframework.web.servlet.ModelAndView;

@Controller
@RequestMapping("/mapper")
public class UserMapperHandler {

    @Autowired
    private UserRepository userRepository;
    private int limit = 10;

    @GetMapping("/findAll/{page}")
    public ModelAndView findAll(@PathVariable("page") int page){
        ModelAndView modelAndView = new ModelAndView();
        int index = (page-1)*limit;
        modelAndView.setViewName("show");
        modelAndView.addObject("list",userRepository.findAll(index,limit));
        modelAndView.addObject("page",page);
        //计算总页数
        int count = userRepository.count();
        int pages = 0;
        if(count%limit == 0){
            pages = count/limit;
        }else{
            pages = count/limit+1;
        }
        modelAndView.addObject("pages",pages);
        return modelAndView;
    }

    @GetMapping("/deleteById/{id}")
    public String deleteById(@PathVariable("id") long id){
        userRepository.deleteById(id);
        return "redirect:/mapper/findAll/1";
    }

    @GetMapping("/findById")
    public ModelAndView findById(@RequestParam("id") long id){
        ModelAndView modelAndView = new ModelAndView();
        modelAndView.addObject("user",userRepository.findById(id));
        modelAndView.setViewName("update");
        return modelAndView;
    }

    @PostMapping("/update")
    public String update(User user){
        userRepository.update(user);
        return "redirect:/mapper/findAll/1";
    }

    @PostMapping("/save")
    public String save(User user){
        userRepository.save(user);
        return "redirect:/mapper/findAll/1";
    }

    @GetMapping("/redirect/{name}")
    public String redirect(@PathVariable("name") String name){
        return name;
    }
}
```

- HTML

```html
<!DOCTYPE html>
<html xmlns:th="http://www.thymeleaf.org">
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
    <form action="/mapper/save" method="post">
        用户姓名：<input type="text" name="name" /><br/>
        用户成绩：<input type="text" name="score" /><br/>
        <input type="submit" value="提交"/>
    </form>
</body>
</html>
```



```html
<!DOCTYPE html>
<html xmlns:th="http://www.thymeleaf.org">
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
    <form action="/mapper/update" method="post">
        用户ID：<input type="text" name="id" th:value="${user.id}" readonly/><br/>
        用户姓名：<input type="text" name="name" th:value="${user.name}" /><br/>
        用户成绩：<input type="text" name="score" th:value="${user.score}" /><br/>
        <input type="submit" value="提交"/>
    </form>
</body>
</html>
```



```html
<!DOCTYPE html>
<html xmlns:th="http://www.thymeleaf.org">
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <script type="text/javascript" th:src="@{/jquery-3.3.1.min.js}"></script>
    <script type="text/javascript">
        $(function(){
            $("#first").click(function(){
                var page = $("#page").text();
                page = parseInt(page);
                if(page == 1){
                    return false;
                }
                window.location.href="/mapper/findAll/1";
            });
            $("#previous").click(function(){
                var page = $("#page").text();
                page = parseInt(page);
                if(page == 1){
                    return false;
                }
                page = page-1;
                window.location.href="/mapper/findAll/"+page;
            });
            $("#next").click(function(){
                var page = $("#page").text();
                var pages = $("#pages").text();
                if(page == pages){
                    return false;
                }
                page = parseInt(page);
                page = page+1;
                window.location.href="/mapper/findAll/"+page;
            });
            $("#last").click(function(){
                var page = $("#page").text();
                var pages = $("#pages").text();
                if(page == pages){
                    return false;
                }
                window.location.href="/mapper/findAll/"+pages;
            });
        });
    </script>
</head>
<body>
    <h1>用户信息</h1>
    <table>
        <tr>
            <th>用户ID</th>
            <th>用户名</th>
            <th>成绩</th>
            <th>操作</th>
        </tr>
        <tr th:each="user:${list}">
            <td th:text="${user.id}"></td>
            <td th:text="${user.name}"></td>
            <td th:text="${user.score}"></td>
            <td>
                <a th:href="@{/mapper/deleteById/{id}(id=${user.id})}">删除</a>
                <a th:href="@{/mapper/findById(id=${user.id})}">修改</a>
            </td>
        </tr>
    </table>
    <a id="first" href="javascript:void(0)">首页</a>
    <a id="previous" href="javascript:void(0)">上一页</a>
    <span id="page" th:text="${page}"></span>/<span id="pages" th:text="${pages}"></span>
    <a id="next" href="javascript:void(0)">下一页</a>
    <a id="last" href="javascript:void(0)">尾页</a><br/>
    <a href="/mapper/redirect/save">添加用户</a>
</body>
</html>
```



