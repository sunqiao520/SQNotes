## 1、添加逻辑删除插件


```java
@Configuration
@MapperScan("com.sq.eduservice.mapper")
public class EduConfig {
    /**
     * 逻辑删除插件
     */
    @Bean
    public ISqlInjector sqlInjector() {
        return new LogicSqlInjector();
    }
}
```


注意添加[@Configuration ](/Configuration ) 和@MapperScan("mapper所在的文件夹")  注解 


## 2、在实体类逻辑删除字段添加[@TableLogic ](/TableLogic ) 注解 


```java
    @ApiModelProperty(value = "逻辑删除 1（true）已删除， 0（false）未删除")
    @TableLogic
    private Boolean isDeleted;
```


## 3、删除结果


UPDATE edu_teacher SET is_deleted=1 WHERE id=? AND is_deleted=0
在控制台打印sql 语句发现增加了 is_deleted = 0判断条件。
此时做查询同样会在后面添加 is_deleted != 0
