# Swagger使用教程

## 1、引入 Swagger2 的依赖

```xml
<dependency>
    <groupId>io.springfox</groupId>
    <artifactId>springfox-swagger2</artifactId>
    <version>2.7.0</version>
</dependency>
<dependency>
    <groupId>io.springfox</groupId>
    <artifactId>springfox-swagger-ui</artifactId>
    <version>2.7.0</version>
</dependency>
```

## 2、创建 Swagger2 配置类

```java
@Configuration
@EnableSwagger2
public class SwaggerConfig {
    @Bean
    public Docket webApiConfig(){
        return new Docket(DocumentationType.SWAGGER_2)
                .groupName("webApi")
                .apiInfo(webApiInfo())
                .select()
//                .paths(Predicates.not(PathSelectors.regex("/admin/.*")))
                .paths(Predicates.not(PathSelectors.regex("/error.*")))
                .build();

    }

    private ApiInfo webApiInfo(){

        return new ApiInfoBuilder()
                .title("网站-课程中心API文档")
                .description("本文档描述了课程中心微服务接口定义")
                .version("1.0")
                .contact(new Contact("java", "https://i.csdn.net/#/user-center/profile?spm=1000.2115.3001.5111", "760526001@qq.com"))
                .build();
    }
}
```

通过 @Configuration 注解，让Spring来加载该类配置。再通过@EnableSwagger2注解来启用Swagger2

## 3、注意事项

Swagger2 的配置类需要被接口所在的 启动类扫描到，因此如果将Swagger2 的配置放在工具模块中，需要引入该模块，并在启动类加上

```java
@ComponentScan(basePackages = {"com.sq"})
```

让Swagger 被扫描到。

## 4、定义接口说明和参数说明

定义在类上：@Api

定义在方法上：@ApiOperation

定义在参数上：@ApiParam

## 5、效果

![image-20210630165329788](https://gitee.com/sun-qiao321/picture/raw/master/images/image-20210630165329788.png)



在小方框中输入参数，点击try it out ！

