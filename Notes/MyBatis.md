# MyBatis

## 原理

MyBatis是一个数据持久层(ORM)框架。把实体 类和SQL语句之间建立了映射关系，是一种半自 动化的ORM实现。MyBATIS需要开发人员自己来写sql语句，这可以增加了程序的灵活性，在一定程度上可以作为ORM的一种补充。

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

