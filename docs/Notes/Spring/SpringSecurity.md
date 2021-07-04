# SpringSecurity

## 登录拦截

#### 导入pom.xml 依赖

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-security</artifactId>
</dependency>
```

#### 登录实现

此时访问任何页面，都会跳到 Spring Security 自定义的login页面

<img src="https://gitee.com/sun-qiao321/picture/raw/master/images/20210609111348.png" style="zoom:67%;" />

##### 自定义登录页面

首先自定义一个SecurityConfig 配置类 继承WebSecurityConfigureAdpter ，并标上 `@EnableWebSecurity` 注解。

```java
@EnableWebSecurity
public class SecurityConfig extends WebSecurityConfigurerAdapter {
}
```

```java
@Override
public void configure(WebSecurity web) throws Exception {
  web.ignoring().antMatchers("/js/**", "/css/**","/images/**");
}
@Override
protected void configure(HttpSecurity http) throws Exception {
    //login所有人可以访问
    http.authorizeRequests().antMatchers("/admin/login").permitAll()
                            .antMatchers("/common/**").permitAll()
                            .anyRequest().authenticated()
                            .and()
                            .csrf().disable();
        //没有权限，去login页面
    http.formLogin().loginPage("/admin/login")    //登录页面
            .loginProcessingUrl("/admin/login")   //登录接口，对应到form 表单到提交action
            .usernameParameter("userName")       //对应表单 的 名字
            .passwordParameter("password")
            .defaultSuccessUrl("/admin/index");   //重定向
//           .successForwardUrl("/admin/index");   //服务端跳转
}
```

1. web.ignoring() 用来配置忽略掉的 URL 地址，一般对于静态文件，我们可以采用此操作。
2. and 方法表示结束当前标签，上下文回到HttpSecurity，开启新一轮的配置。
3. permitAll 表示登录相关的页面/接口不要被拦截。
4. 最后记得关闭 csrf 。

当我们定义了登录页面为 admin/login.html 的时候，Spring Security 也会帮我们自动注册一个 admin/login.html 的接口，这个接口是 POST 请求，用来处理登录逻辑。



#### 基于数据库的登录验证

- 找到管理实体类

注意数据库设计密码的 varchar 要较大，大于50，不然可能存不下加密后的密码。

- 实体类实现 UserDetails

重写对应的方法。

```java
@Data
public class AdminUser implements UserDetails{

    private Integer adminUserId;

    private String loginUserName;

    private String loginPassword;

    private String nickName;

    private Byte locked;

    @Override
    public Collection<? extends GrantedAuthority> getAuthorities() {
        return null;
    }

    @Override
    public String getPassword() {
        return loginPassword;
    }

    @Override
    public String getUsername() {
        return loginUserName;
    }


    @Override
    public boolean isAccountNonExpired() {
        return true;
    }

    @Override
    public boolean isAccountNonLocked() {
        return true;
    }

    @Override
    public boolean isCredentialsNonExpired() {
        return true;
    }

    @Override
    public boolean isEnabled() {
        return true;
    }
}

```

- dao 层增加通过用户名查找用户的方法

```xml
<select id="selectByLoginUserName" resultMap="BaseResultMap">
    select
    <include refid="Base_Column_List"/>
    from tb_newbee_mall_admin_user
    where login_user_name = #{userName,jdbcType=VARCHAR}
</select>
```

- service 层实现 UserDetailsService

实现其中的 loadUserByUsername 方法

```java
@Override
public UserDetails loadUserByUsername(String s) throws UsernameNotFoundException {
    AdminUser adminUser = adminUserMapper.selectByLoginUserName(s);
    if(adminUser == null) {
        throw new UsernameNotFoundException("用户不存在");
    }
    return adminUser;
}
```

- SecurityConfig 中增加数据库连接配置

```java
//认证
@Override
protected void configure(AuthenticationManagerBuilder auth) throws Exception {
    auth.userDetailsService(adminUserService).passwordEncoder(new BCryptPasswordEncoder());
}
```

注意必须添加编码，数据库中的密码也需使用BCryptPasswordEncoder 编码存放，否则产生 java.lang.IllegalArgumentException: There is no PasswordEncoder 错误。



## 增加验证码登录

#### 自定义过滤器

```java
@Component
public class VerificationCodeFilter extends GenericFilter {
    @Override
    public void doFilter(ServletRequest servletRequest, ServletResponse servletResponse, FilterChain filterChain) throws IOException, ServletException {
        HttpServletRequest req = (HttpServletRequest) servletRequest;
        HttpServletResponse resp = (HttpServletResponse) servletResponse;
        if("POST".equals(req.getMethod()) && "/admin/login".equals(req.getServletPath())) {
            String code = req.getParameter("verifyCode");
            String verify = (String) req.getSession().getAttribute("verifyCode");
            if(code == null || "".equals(code) || !verify.equals(code)) {
                req.setAttribute("errorMsg","验证码错误");
                resp.sendRedirect("/admin/login");
            }else {
                filterChain.doFilter(req,resp);
            }
        } else {
            filterChain.doFilter(req,resp);
        }
    }
}
```

自定义过滤器继承自 GenericFilterBean，并实现其中的 doFilter 方法，在 doFilter 方法中，当请求方法是 POST，并且请求地址是 `/admin/login` 时，获取参数中的 code 字段值，该字段保存了用户从前端页面传来的验证码，然后获取 session 中保存的验证码，如果用户没有传来验证码，则抛出验证码不能为空异常，如果用户传入了验证码，则判断验证码是否正确，如果不正确则抛出异常，否则执行 `chain.doFilter(request, response);` 使请求继续向下走。

#### 配置过滤器

在SecurityConfig 中注入上面定义的过滤器即可

```java
 @Override
    protected void configure(HttpSecurity http) throws Exception {
        http.addFilterBefore(verificationCodeFilter, UsernamePasswordAuthenticationFilter.class);
        //login所有人可以访问
        http.authorizeRequests().antMatchers("/admin/login").permitAll()
                                .antMatchers("/common/**").permitAll()
                                .anyRequest().authenticated()
                                .and()
                                .csrf().disable();

        //没有权限，去login页面
        http.formLogin().loginPage("/admin/login")    //登录页面
                .loginProcessingUrl("/admin/login")   //登录接口，对应到form 表单到提交action
                .usernameParameter("userName")       //对应表单 的 名字
                .passwordParameter("password")
                .defaultSuccessUrl("/admin/index");   //重定向
//                .successForwardUrl("/admin/index");   //服务端跳转
    }
```
