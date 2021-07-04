## 1、权限管理的数据模型

![image-20210702145645605](https://gitee.com/sun-qiao321/picture/raw/master/images/image-20210702145645605.png)



## 2、登录流程debug测试

![image-20210702145939274](https://gitee.com/sun-qiao321/picture/raw/master/images/image-20210702145939274.png)



### 获取前端数据

首先，进入TokenLoginFilter 过滤器中，获取到前端提交的数据，封装到securty 中的 User 对象中。

```java
@Data
@ApiModel(description = "用户实体类")
public class User implements Serializable {

    private static final long serialVersionUID = 1L;

    @ApiModelProperty(value = "微信openid")
    private String username;

    @ApiModelProperty(value = "密码")
    private String password;

    @ApiModelProperty(value = "昵称")
    private String nickName;

    @ApiModelProperty(value = "用户头像")
    private String salt;

    @ApiModelProperty(value = "用户签名")
    private String token;

}
```



![image-20210702150306139](D:\SQNotes\docs\Notes\谷粒学院项目\image-20210702150306139.png)

可以取出user信息，里面放置的是username和password，既然看到了User，那就需要说一下entity中的User.java了，它和service_acl中entity中的User.java相似，只是部分字段不同，另外service_acl对应的是数据库中的acl_user表，而spring_security中的User.java是spring security要用的，所以用途也是不同的。

![image-20210702150354376](https://gitee.com/sun-qiao321/picture/raw/master/images/image-20210702150354376.png)

### 获取数据库用户信息

然后会到UserDetailsService的实现类中，该类从数据库根据username 得到对应的用户信息。通过loadUserByUsername方法将用户信息和权限信息统一封装到SecurityUser对象中。



![image-20210702150437725](https://gitee.com/sun-qiao321/picture/raw/master/images/image-20210702150437725.png)

![image-20210702150530892](https://gitee.com/sun-qiao321/picture/raw/master/images/image-20210702150530892.png)

![image-20210702150615410](https://gitee.com/sun-qiao321/picture/raw/master/images/image-20210702150615410.png)

![image-20210702150705024](https://gitee.com/sun-qiao321/picture/raw/master/images/image-20210702150705024.png)

![image-20210702150743431](https://gitee.com/sun-qiao321/picture/raw/master/images/image-20210702150743431.png)

### 密码比对

然后进入自定义的密码比对方法中，raw为前端传入的数据，ecoded为数据库中已加密的密码

![image-20210702150839841](https://gitee.com/sun-qiao321/picture/raw/master/images/image-20210702150839841.png)



### 生成token

然后进入TokenManager 中将用户信息

![image-20210702150933554](https://gitee.com/sun-qiao321/picture/raw/master/images/image-20210702150933554.png)

![image-20210702151004006](https://gitee.com/sun-qiao321/picture/raw/master/images/image-20210702151004006.png)

