# Spring Data JPA 

## 原理

Spring Data JPA 是 Spring 基于 ORM 框架、JPA 规范的基础上封装的一套 JPA 应用框架，底层使用了 Hibernate 的 JPA 技术实现，可使开发者用极简的代码即可实现对数据的访问和操作。它提供了包括增删改查等在内的常用功能，且易于扩展！学习并使用 Spring Data JPA 可以极大提高开发效率。

## 使用

#### 一、导包

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-jpa</artifactId>
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
    url: jdbc:mysql://localhost:3306/phone_store_demo?useUnicode=true&characterEncoding=UTF-8&serverTimezone=Asia/Shanghai
    username: root
    password: 123456
    driver-class-name: com.mysql.cj.jdbc.Driver
  jpa:
    show-sql: true
    properties:
      hibernate:
        format_sql: true
```

#### 三、建立实体类

```java
@Entity
public class User {

  @Id
  @GeneratedValue
  private long id;
  @Column(nullable = false, unique = true)
  private String userName;
  @Column(nullable = false)
  private String password;
  @Column(nullable = false)
  private int age;
}
```

- @Entity 是一个类注解，用来注解该类是一个**实体类**用来进行和数据库中的表建立关联关系，首次启动项目的时候，默认会在数据中生成一个同实体类相同名字的表（table），也可以通过注解中的 name 属性来修改表（table）名称， 如@Entity(name=“user”) , 这样数据库中表的名称则是 user 。该注解十分重要，如果没有该注解首次启动项目的时候你会发现数据库没有生成对应的表。
- @Table 注解也是一个类注解，该注解可以用来修改表的名字，该注解完全可以忽略掉不用，@Entity 注解已具备该注解的功能。
- **@Id 类的属性注解，该注解表明该属性字段是一个主键，该属性必须具备，不可缺少。**
- @GeneratedValue 该注解通常和 @Id 主键注解一起使用，用来定义主键的呈现形式，该注解通常有多种使用策略，先总结如下：
- @GeneratedValue(strategy= GenerationType.IDENTITY) 该注解由数据库自动生成，**主键自增型**，在 mysql 数据库中使用最频繁，oracle 不支持。
- @GeneratedValue(strategy= GenerationType.AUTO) 主键由程序控制，默认的主键生成策略，oracle 默认是序列化的方式，mysql 默认是主键自增的方式。
- @GeneratedValue(strategy= GenerationType.SEQUENCE) 根据底层数据库的序列来生成主键，条件是数据库支持序列，Oracle支持，Mysql不支持。
- @GeneratedValue(strategy= GenerationType.TABLE) 使用一个特定的数据库表格来保存主键，较少使用。

上面这些主键生成策略中，以 mysql 为例， IDENTITY  和 AUTO 用的较多，二者当中 IDENTITY 用的多些，以下文章当中演示的 demo 主键均使用 @GeneratedValue(strategy= GenerationType.IDENTITY) 的生成策略。

@Column 是一个类的属性注解，该注解可以定义一个字段映射到数据库属性的具体特征，比如字段长度，映射到数据库时属性的具体名字等。

@Transient  是一个属性注解，该注解标注的字段不会被映射到数据库当中。

#### 四、声明 `UserRepository`接口，继承`JpaRepository`

```java
public interface UserRepository extends JpaRepository<User, Long> { //泛型

}
```

这里的 JpaRepository继承了接口PagingAndSortingRepository和QueryByExampleExecutor。而，PagingAndSortingRepository又继承CrudRepository。

因此，JpaRepository接口同时拥有了基本CRUD功能以及分页功能。因此，这里我们可以继承JpaRepository，从而获得Spring为我们预先定义的多种基本数据操作方法。

#### 五、查询

查询可以分为基本查询和自定义查询，一种是 spring data 默认已经实现，只需要要继承`JpaRepository`，一种是根据查询的方法来自动解析成 SQL。

#### 六、自定义简单查询

自定义的简单查询就是根据方法名来自动生成SQL，主要的语法是`findXXBy,readAXXBy,queryXXBy,countXXBy, getXXBy`后面跟属性名称，举几个例子：

```java
User findByUserName(String userName);

User findByUserNameOrEmail(String username, String email);

Long deleteById(Long id);

Long countByUserName(String userName);

List<User> findByEmailLike(String email);

User findByUserNameIgnoreCase(String userName);

List<User> findByUserNameOrderByEmailDesc(String email);
```

#### 七、复杂查询

在实际的开发中我们需要用到分页、删选、连表等查询的时候就需要特殊的方法或者自定义 SQL，以分页查询为例，分页查询在实际使用中非常普遍了，spring data jpa已经帮我们实现了分页的功能，在查询的方法中，需要传入参数Pageable，当查询中有多个参数的时候Pageable建议做为最后一个参数传入。Pageable是 spring 封装的分页实现类，使用的时候需要传入页数、每页条数和排序规则

```java
Page<User> findALL(Pageable pageable);

Page<User> findByUserName(String userName,Pageable pageable);
```

#### 八、自定义SQL

Spring data 大部分的 SQL 都可以根据方法名定义的方式来实现，但是由于某些原因我们想使用自定义的 SQL 来查询，spring data 也是完美支持的，如下所示：

```java
@Modifying
@Query("update User u set u.userName = ?1 where c.id = ?2")
int modifyByIdAndUserId(String  userName, Long id);

@Transactional
@Modifying
@Query("delete from User where id = ?1")
void deleteByUserId(Long id);

@Transactional(timeout = 10)
@Query("select u from User u where u.emailAddress = ?1")
User findByEmailAddress(String emailAddress);
```