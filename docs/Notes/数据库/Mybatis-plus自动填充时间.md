该配置使用的mybatis-plus版本为 3.0.5


## 1、Entity 类设置


在需要填充的类上加`@TableField(.. fill = FieldFill.INSERT)` 注解。
mybatis -plus默认有四种填充设置。


```java
    //默认不处理
    DEFAULT,
    //插入时填充字段
    INSERT,
    //更新时填充字段
    UPDATE,
    //插入和更新时都填充
    INSERT_UPDATE;
```


## 2、增加配置类


```java
@Component
public class MyMetaObjectHandler implements MetaObjectHandler {
    @Override
    public void insertFill(MetaObject metaObject) {
        this.setFieldValByName("gmtCreate", new Date(), metaObject);
        this.setFieldValByName("gmtModified", new Date(), metaObject);
    }

    @Override
    public void updateFill(MetaObject metaObject) {
        this.setFieldValByName("gmtModified", new Date(), metaObject);
    }
}
```


setFieldValByName() 方法中第一个参数应与对应实体类的字段名一致。
