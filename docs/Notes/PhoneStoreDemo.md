# PhoneStoreDemo

### 后端结构

```text
com.sq.phone_store_demo
----config
        CorsConfig   //解决跨域问题
----controller
        AddressHandler
        OrderHandler
        PhoneHandler
----dto
        OrderDTO
----entity
        BuyerAddress
        OrderMaster
        PhoneCategory
        PhoneInfo
        PhoneSpecs
----enums
		PayStatusEnum
		ResultEnum
----exception
		PhoneException
----form
		AddressForm
		OrderForm
----repository
		BuyerAddressRepository
		OrderMasterRepository
		PhoneCategoryRepository
		PhoneInfoRepository
		PhoneSpecsRepository
----service
	----impl
	        AddressServiceImpl
	        OrderServiceImpl
	        PhoneServiceImpl
        AddressService
        OrderService
        PhoneService
----util
        KeyUtil
        PhoneUtil
        ResultVOUtil
----vo
        AddressVO
        DataVO
        OrderDetailVO
        PhoneCategoryVO
        PhoneInfoVO
        PhoneSpecsCasVO
        PhoneSpecsVO
        ResultVO
        SkuVO
        SpecsPackageVO
        TreeVO
    PhoneStoreDemoApplication
```

### 后端工程创建

SpringBoot模板

![](https://gitee.com/sun-qiao321/picture/raw/master/images/20210515162318.png)

组件选择

Lombok

Spring Web

Spring Data JPA

MySQL Driver

![](https://gitee.com/sun-qiao321/picture/raw/master/images/20210515162533.png)

### 后端代码

##### yaml 环境配置

```yml
server:
  port: 8181
spring:
  datasource:
    url: jdbc:mysql://localhost:3306/phone_store_demo?useUnicode=true&characterEncoding=UTF-8&serverTimezone=Asia/Shanghai
    username: root
    password: 123456
    driver-class-name: com.mysql.cj.jdbc.Driver
  jpa:
    show-sql: true   //在控制台显示JPA生成的sql
    properties:
      hibernate:
        format_sql: true  //控制台显示生成的sql的时候进行格式化
```

##### entity类的编写（JPA）

注意类名注意数据库的表名，变量名对应表的列名

`@Entity` 表明该类 (UserEntity) 为一个实体类，它默认对应数据库中的表名是user_entity。

`@Id`  用于声明一个实体类的属性映射为数据库的主键列。该属性通常置于属性声明语句之前，可与声明语句同行，也可写在单独行上。 

`@GeneratedValue` 用于标注主键的生成策略，通过strategy 属性指定。默认情况下，JPA 自动选择一个最适合底层数据库的主键生成策略：SqlServer对应identity，MySQL 对应 auto increment。 
在javax.persistence.GenerationType中定义了以下几种可供选择的策略： 

- IDENTITY：采用数据库ID自增长的方式来自增主键字段，Oracle 不支持这种方式； 
- AUTO： JPA自动选择合适的策略，是默认选项； 
- SEQUENCE：通过序列产生主键，通过@SequenceGenerator 注解指定序列名，MySql不支持这种方式 ；
- TABLE：通过表产生主键，框架借由表模拟序列产生主键，使用该策略可以使应用更易于数据库移植。

##### repository接口的编写

为每个表创建对应的repository 接口 实现 JpaRepository<操作的实体类类型，主键类型>

Repository居于业务层和数据层之间，将两者隔离开来，在它的内部封装了数据查询和存储的逻辑。这样设计的好处有两个：

1. 降低层级之间的耦合：更换、升级ORM引擎（Hibernate）并不会影响业务逻辑
2. 提高测试效率：如果在测试时能用Mock数据对象代替实际的数据库操作，运行速度会快很多

根据需要为接口添加方法，不需要实现。

当我们使用 Spring Data JPA 的时候，典型的用法是这样的 :

1. 将 spring-data-jpa 包，数据库驱动包等添加为项目依赖；
2. 定义业务领域实体类，比如通过@Entity注解；
3. 定义自己业务相关的的JPA repository接口，这些接口都继承自JpaRepository或者JpaSpecificationExecutor;
4. 将上面自定义JPA repository接口注入到服务层并使用它们进行相应的增删改查;

##### 对repository 进行测试

利用SpringBoot单元测试

对每个需要用到的方法进行测试

##### service 接口 与 实现类

服务层，封装DAO层（多表查询），为Controller 提供服务。

对数据进行封装vo

`@JsonProperty`用于给前端返回Json数据与后端命名不一致情况

##### 对service 接口 与 实现类 进行测试



##### Controller层编写



##### 解决跨域问题