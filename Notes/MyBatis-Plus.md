# [MyBatis-Plus](https://mp.baomidou.com/)

## 原理

`MyBatis` 是一款优秀的持久层框架，它支持定制化 SQL、存储过程以及高级映射，而实际开发中，我们都会选择使用 `MyBatisPlus`，它是对 `MyBatis`框架的进一步增强，能够极大地简化我们的持久层代码

**框架结构**

<img src="C:\Users\SQ\AppData\Roaming\Typora\typora-user-images\image-20210601173244941.png" alt="image-20210601173244941" style="zoom: 50%;" />

## [Spring Boot 整合使用](https://mp.weixin.qq.com/s/Hz6l9iXHorj3jSeRWtll0w)

#### 一、导入依赖

```xml
<dependency>
    <groupId>mysql</groupId>
    <artifactId>mysql-connector-java</artifactId>
    <scope>runtime</scope>
</dependency>
<!--MP插件-->
<dependency>
    <groupId>com.baomidou</groupId>
    <artifactId>mybatis-plus-boot-starter</artifactId>
    <version>3.2.0</version>
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
```

#### 三、实体类

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

#### 四、dao 层 mapper接口

```java
@Mapper
public interface PhoneCategoryMapper extends BaseMapper<PhoneCategory> {
}
```

#### 五、测试mapper 接口 

```java
@SpringBootTest
class PhoneCategoryTest {
    @Autowired
    PhoneCategoryMapper phoneCategoryMapper;

    @Test
    public void findAll(){
        for (PhoneCategory phoneCategory : phoneCategoryMapper.selectList(null)) {
            System.out.println(phoneCategory);
        }
    }

}
```

#### 六、[自定义查询场景](https://mp.weixin.qq.com/s/tKeOw8JiSC8G4mIqQvReYA)

首先在mapper 接口中声明方法

```java
@Mapper
public interface PhoneCategoryMapper extends BaseMapper<PhoneCategory> {
    public PhoneCategory findByUserName( String categoryName);
}
```

自己编写配置文件实现该方法，在 `resource` 目录下新建一个`mapping` 文件夹，然后在该文件夹下创建 `PhoneCategoryMapper.xml` 文件：

扫描路径配置：

```yml
mybatis-plus:
  mapper-locations: classpath:/mapping/*.xml
```

#### 七、mapper 实现

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN" "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.sq.dao.PhoneCategoryMapper">

    <select id="findByUserName" parameterType="java.lang.String" resultType="com.sq.entity.PhoneCategory">
        select * from phone_category where category_name = #{categoryName}
    </select>

</mapper>
```

#### 八、测试

