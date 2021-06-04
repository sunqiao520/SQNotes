# MyBatis

## 原理

MyBatis是一个数据持久层(ORM)框架。把实体 类和SQL语句之间建立了映射关系，是一种半自 动化的ORM实现。MyBATIS需要开发人员自己来写sql语句，这可以增加了程序的灵活性，在一定程度上可以作为ORM的一种补充。

##### #{} 和 ${} 的区别是什么？

${} 是字符串替换。#{}是预处理；

Mabatis 在处理${}时。就是把${}直接替换成变量的值。而Mybatis在处理#{}时，会对sql语句进行预处理，将sql中的#{}替换为？号，调用PreparedStatement 的set方法来赋值。使用#{}可以有效防止SQL注入，提高系统安全性。

##### Xml映射文件中，通过常见的select 、insert、update、delete标签之外，还有哪些标签？

<resultMap> <parameterMap> <sql> <include> <selectKey>，加上动态sql 的9个标签，trim、where、set、foreach、if、choose、when、otherwise、bind等，其中sql片段标签，通过<include>标签引入sql片段，<selectKey>为不支持自增主键生成策略标签。

##### 通常一个mapper.xml文件，都会对应一个Dao接口，这个Dao接口的工作原理是什么？Dao接口里的方法，参数不同时，方法能重载吗？

Mapper 接口的工作原理是JDK动态代理，Mybatis运行时会使用JDK动态代理为Mapper接口生成代理对象proxy，代理对象会拦截接口方法，根据类的全限定名+方法名，唯一定位到一个MapperStatement并调用执行器执行所代表的sql，然后将sql执行结果返回。

Mapper接口里的方法，是不能重载的，因为是使用 全限名+方法名 的保存和寻找策略。

> Dao接口即Mapper接口。接口的全限名，就是映射文件中的namespace的值；接口的方法名，就是映射文件中Mapper的Statement的id值；接口方法内的参数，就是传递给sql的参数。
>
> 当调用接口方法时，接口全限名+方法名拼接字符串作为key值，可唯一定位一个MapperStatement。在Mybatis中，每一个SQL标签，比如<select>、<insert>、<update>、<delete>标签，都会被解析为一个MapperStatement对象。
>
> 举例：com.mybatis3.mappers.StudentDao.findStudentById，可以唯一找到namespace为com.mybatis3.mappers.StudentDao下面 id 为 findStudentById 的 MapperStatement。

##### 当实体类中的属性名和表中的字段名不一样 ，怎么办 ？

第1种： 通过在查询的sql语句中定义字段名的别名，让字段名的别名和实体类的属性名一致。

```xml
<select id=”selectorder” parametertype=”int” resultetype=”me.gacl.domain.order”>
   select order_id id, order_no orderno ,order_price price form orders where order_id=#{id};
</select>
```

第2种： 通过<resultMap>来映射字段名和实体类属性名的一一对应的关系。

```xml
<select id="getOrder" parameterType="int" resultMap="orderresultmap">
    select * from orders where order_id=#{id}
</select>

<resultMap type=”me.gacl.domain.order” id=”orderresultmap”>
    <!–用id属性来映射主键字段–>
    <id property=”id” column=”order_id”>

    <!–用result属性来映射非主键字段，property为实体类属性名，column为数据表中的属性–>
    <result property = “orderno” column =”order_no”/>
    <result property=”price” column=”order_price” />
</reslutMap>
```



## SpringBoot整合使用

#### 一、导包

```xml
<dependency>
    <groupId>org.mybatis.spring.boot</groupId>
    <artifactId>mybatis-spring-boot-starter</artifactId>
    <version>1.3.2</version>
</dependency>
<dependency>
    <groupId>mysql</groupId>
    <artifactId>mysql-connector-java</artifactId>
    <scope>runtime</scope>
</dependency>
```

#### 二、yml 配置

```yml
spring:
  datasource:
    url: jdbc:mysql://localhost:3306/test?useUnicode=true&characterEncoding=UTF-8
    username: root
    password: 123456
    driver-class-name: com.mysql.cj.jdbc.Driver
mybatis:
  # xml文件位置
  mapper-locations: classpath:/mapping/*.xml
  type-aliases-package: com.sq.entity
  #开启驼峰式命名
  configuration:
    map-underscore-to-camel-case: true
```

#### 三、实体类创建

```java
@Data
public class PhoneCategory {
    private Integer categoryId;
    private String categoryName;
    private Integer categoryType;
    private Date createTime;
    private Date updateTime;
}
```

#### 四、dao 层接口

```java
@Repository
@Mapper
public interface PhoneCategoryMapper {
    public List<PhoneCategory> findAll();
}

```

#### 五、mapper.xml 编写

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN" "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.sq.dao.PhoneCategoryMapper">
    <select id="findAll"  resultType="com.sq.entity.PhoneCategory">
        select * from phone_category
    </select>

</mapper>
```

#### 六、测试类

```java
@SpringBootTest
class PhoneCategoryMapperTest {

    @Autowired
    PhoneCategoryMapper phoneCategoryMapper;

    @Test
    void findAll() {
        for (PhoneCategory phoneCategory : phoneCategoryMapper.findAll()) {
            System.out.println(phoneCategory);
        }
    }

    @Test
    void findById(){
        System.out.println(phoneCategoryMapper.findById(1));
    }
}
```

#### 七、service 层接口与实现类

```java
@Service
public class PhoneCategoryService {
    @Autowired
    PhoneCategoryMapper phoneCategoryMapper;

    public List<PhoneCategory> findAll(){
        return phoneCategoryMapper.findAll();
    }
}
```

#### 八、controller 层

