# Spring 手撸练习

本文非原创，大部分参考公众号《bugstack虫洞栈》，来学习Spring 框架。

[Spring 专栏 ](https://github.com/small-spring)

## 1、简单的容器实现

### 1.1、Spring Bean 是什么？

Spring 包含并管理应用对象的配置和生命周期，在这个意义上它是一种用于承载对象的容器，你可以配置你的每个 Bean 对象是如何被创建的，这些 Bean 可以创建一个单独的实例或者每次需要时都生成一个新的实例，以及它们是如何相互关联构建和使用的。

如果一个 Bean 对象交给 Spring 容器管理，那么这个 Bean 对象就应该以类似零件的方式被拆解后存放到 Bean 的定义中，这样相当于一种把对象解耦的操作，可以由 Spring 更加容易的管理，就像处理循环依赖等操作。

当一个 Bean 对象被定义存放以后，再由 Spring 统一进行装配，这个过程包括 Bean 的初始化、属性填充等，最终我们就可以完整的使用一个 Bean 实例化后的对象了。

### 1.2、Bean 容器的设计

HashMap

一个简单的 Spring Bean 容器实现，还需 Bean 的定义、注册、获取三个基本步骤

- 定义：BeanDefinition，可能这是你在查阅 Spring 源码时经常看到的一个类，例如它会包括 singleton、prototype、BeanClassName 等。但目前我们初步实现会更加简单的处理，只定义一个 Object 类型用于存放对象。
- 注册：这个过程就相当于我们把数据存放到 HashMap 中，只不过现在 HashMap 存放的是定义了的 Bean 的对象信息。
- 获取：最后就是获取对象，Bean 的名字就是key，Spring 容器初始化好 Bean 以后，就可以直接获取了。

![](https://gitee.com/sun-qiao321/picture/raw/master/images/ef1d757b24d6ce7bad42bcc9db81a575.png)

### 1.3、工程代码

![](https://gitee.com/sun-qiao321/picture/raw/master/images/20210621170407.png)

**BeanDenifinition类**

```java
package com.sq.springframework;
public class BeanDefinition {
    private Object bean;

    public BeanDefinition(Object bean) {
        this.bean = bean;
    }
    public Object getBean() {
        return bean;
    }
}
```

用于定义Bean 实例化信息。

**BeanFactory 类**

```java
package com.sq.springframework;

import java.util.Map;
import java.util.concurrent.ConcurrentHashMap;
public class BeanFactory {
    private Map<String,BeanDefinition> beanDefinitionMap = new ConcurrentHashMap<>();

    public Object getBean(String name) {
        return beanDefinitionMap.get(name).getBean();
    }

    public void registerBeanDefinition(String name,BeanDefinition beanDefinition) {
        beanDefinitionMap.put(name,beanDefinition);
    }
}
```

Bean 对象的工厂，可以存放Bean定义到Map 中以及获取。

**测试**

```java
package com.sq.springframework.test.bean;
public class UserService {

    public void queryUserInfo(){
        System.out.println("查询用户信息");
    }

}
```

```java
import com.sq.springframework.test.bean.UserService;
import org.junit.Test;
public class ApiTest {
    @Test
    public void test_BeanFactory(){
        // 1.初始化 BeanFactory
        BeanFactory beanFactory = new BeanFactory();

        // 2.注册 bean
        BeanDefinition beanDefinition = new BeanDefinition(new UserService());
        beanFactory.registerBeanDefinition("userService", beanDefinition);

        // 3.获取 bean
        UserService userService = (UserService) beanFactory.getBean("userService");
        userService.queryUserInfo();
    }
}

```

**测试结果**

```java
查询用户信息

Process finished with exit code 0
```

### 1.4、总结

Bean 的实例化在BeanDenifinition 类中完成得到beanDefinition，工厂方法BeanFactory 将 实例化的beanDefinition以键值对的形式存到Map容器中，获取Bean 的时候通过名称获取得到的Bean。

了解Bean 注册获取的基本原理。

## 2、运用设计模式，实现 Bean 的定义、注册、获取

### 2.1、Spring Beean 容器结构完善

![](https://gitee.com/sun-qiao321/picture/raw/master/images/5b0bee381f6cbef6dc8798fb53071691.png)

- 模板模式设计BeanFactory ：需要定义 BeanFactory 这样一个 Bean 工厂，提供 Bean 的获取方法 `getBean(String name)`，之后这个 Bean 工厂接口由抽象类 AbstractBeanFactory 实现。

### 2.2、工程结构

![](https://gitee.com/sun-qiao321/picture/raw/master/images/20210621193320.png)

![](https://gitee.com/sun-qiao321/picture/raw/master/images/26a54e0a191ddcde03c5d230a0d54271.png)

### 2.3、代码实现

**统一异常处理类 BeansException**

```java
package com.sq.springframework.beans;

public class BeansException extends RuntimeException{
    public BeansException(String msg) {
        super(msg);
    }
    public BeansException(String msg,Throwable cause) {
        super(msg,cause);
    }
}
```

**BeanDefinition** 

```java
package com.sq.springframework.beans.factory.config;

public class BeanDefinition {
    private Class beanClass;

    public BeanDefinition(Class beanClass) {
        this.beanClass = beanClass;
    }
    public Class getBeanClass(){
        return beanClass;
    }
    public void setBeanClass(Class beanClass) {
        this.beanClass = beanClass;
    }
}

```

注意将 Object 替换成 Class ，通过反射获取对象。

**SingletonBeanRegistry**

```java
package com.sq.springframework.beans.factory.config;

public interface SingletonBeanRegistry {
    Object getSingleton(String beanName);
}
```

单例注册接口：获取单例对象的接口

**DefaultSingletonRegistry**

```Java
package com.sq.springframework.beans.factory.support;

import com.sq.springframework.beans.factory.config.SingletonBeanRegistry;

import java.util.HashMap;
import java.util.Map;

public class DefaultSingletonRegistry implements SingletonBeanRegistry {
    private Map<String,Object> singletonObjects = new HashMap<>();
    @Override
    public Object getSingleton(String beanName) {
        return singletonObjects.get(beanName);
    }

    protected void addSingleton(String beanName, Object singletonObject) {
        singletonObjects.put(beanName, singletonObject);
    }
}

```

**BeanFactory**

```java
package com.sq.springframework.beans.factory;

import com.sq.springframework.beans.BeansException;

public interface BeanFactory {
    Object getBean(String name) throws BeansException;
}

```

**AbstractBeanFactory**

```java
package com.sq.springframework.beans.factory.support;

import com.sq.springframework.beans.BeansException;
import com.sq.springframework.beans.factory.BeanFactory;
import com.sq.springframework.beans.factory.config.BeanDefinition;

public abstract class AbstractBeanFactory extends DefaultSingletonRegistry implements BeanFactory {
    @Override
    public Object getBean(String name) throws BeansException {
        Object bean = getSingleton(name);
        if(bean != null) {
            return bean;
        }
        BeanDefinition beanDefinition = getBeanDefinition(name);
        return createBean(name,beanDefinition);
    }

    protected abstract BeanDefinition getBeanDefinition(String beanName) throws BeansException;
    protected abstract Object createBean(String beanName, BeanDefinition beanDefinition) throws BeansException;
}
```

- AbstractBeanFactory 首先继承了 DefaultSingletonBeanRegistry，也就具备了使用**单例注册类方法**。
- 接下来很重要的一点是关于接口 BeanFactory 的实现，在方法 getBean 的实现过程中可以看到，主要是对单例 Bean 对象的获取以及在获取不到时需要拿到 Bean 的定义做相应 Bean 实例化操作。那么 getBean 并没有自身的去实现这些方法，而是只定义了调用过程以及提供了抽象方法，由实现此抽象类的其他类做相应实现。
- 后续继承抽象类 AbstractBeanFactory 的类有两个，包括：AbstractAutowireCapableBeanFactory、DefaultListableBeanFactory，这两个类分别做了相应的实现处理

**AbstractAutowireCapableBeanFactory**

```java
package com.sq.springframework.beans.factory.support;

import com.sq.springframework.beans.BeansException;
import com.sq.springframework.beans.factory.config.BeanDefinition;

public abstract class AbstractAutowireCapableBeanFactory extends AbstractBeanFactory {

    @Override
    protected Object createBean(String beanName, BeanDefinition beanDefinition) throws BeansException {
        Object bean = null;
        try {
            bean = beanDefinition.getBeanClass().newInstance();
        } catch (InstantiationException | IllegalAccessException e) {
            throw new BeansException("Instantiation of bean failed", e);
        }

        addSingleton(beanName, bean);
        return bean;
    }
}
```

- 在 AbstractAutowireCapableBeanFactory 类中实现了 Bean 的实例化操作 `newInstance`

- 在处理完 Bean 对象的实例化后，直接调用 `addSingleton` 方法存放到单例对象的缓存中去。

**DefaultListableBeanFactory**

```java
package com.sq.springframework.beans.factory.support;

import com.sq.springframework.beans.BeansException;
import com.sq.springframework.beans.factory.config.BeanDefinition;

import java.util.HashMap;
import java.util.Map;

public class DefaultListableBeanFactory extends AbstractAutowireCapableBeanFactory implements BeanDefinitionRegistry {

    private Map<String, BeanDefinition> beanDefinitionMap = new HashMap<>();

    @Override
    public void registerBeanDefinition(String beanName, BeanDefinition beanDefinition) {
        beanDefinitionMap.put(beanName, beanDefinition);
    }

    @Override
    public BeanDefinition getBeanDefinition(String beanName) throws BeansException {
        BeanDefinition beanDefinition = beanDefinitionMap.get(beanName);
        if (beanDefinition == null) {throw new BeansException("No bean named '" + beanName + "' is defined");}
        return beanDefinition;
    }

}
```

**测试**

UserService

```java
package com.sq.springframework.test.bean;

public class UserService {

    public void queryUserInfo(){
        System.out.println("查询用户信息");
    }

}
```

ApiTest

```java
package com.sq.springframework.test;

import com.sq.springframework.beans.factory.config.BeanDefinition;
import com.sq.springframework.beans.factory.support.DefaultListableBeanFactory;
import com.sq.springframework.test.bean.UserService;
import org.junit.Test;

public class ApiTest {

    @Test
    public void test_BeanFactory(){
        // 1.初始化 BeanFactory
        DefaultListableBeanFactory beanFactory = new DefaultListableBeanFactory();

        // 2.注册 bean
        BeanDefinition beanDefinition = new BeanDefinition(UserService.class);
        beanFactory.registerBeanDefinition("userService", beanDefinition);

        // 3.第一次获取 bean
        UserService userService = (UserService) beanFactory.getBean("userService");
        userService.queryUserInfo();

        // 4.第二次获取 bean from Singleton
        UserService userService_singleton = (UserService) beanFactory.getBean("userService");
        userService_singleton.queryUserInfo();
    }

}
```

结果

```java
查询用户信息
查询用户信息

Process finished with exit code 0
```

### 2.4、总结

通过 继承和实现关系使得各个类各施其职，便于扩展。

三大接口：

- BeanFactory：定义getbean 从容器获取实例对象
- BeanDefinitionRegistry：定义registerBeanDefinition ，以键值对的形式注册对象到容器中
- SingletonBeanRegistry：获得单例对象

