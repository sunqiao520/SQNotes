## SpringBoot实现登录拦截器

## 1、原理

SpringBoot 通过实现`HandlerInterceptor`接口实现拦截器，通过实现`WebMvcConfigurer`接口实现一个配置类，在配置类中注入拦截器，最后再通过 @Configuration 注解注入配置.

### 1.1、实现 HandlerInterceptor 接口

实现 `HandlerInterceptor` 的三个方法

```java
package com.sq.interceptor;

import org.springframework.stereotype.Component;
import org.springframework.web.servlet.HandlerInterceptor;
import org.springframework.web.servlet.ModelAndView;

import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
@Component
public class AdminLoginInterceptor implements HandlerInterceptor {
     /***
     * 在请求处理之前进行调用(Controller方法调用之前)
     */
    @Override
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
        String requestServletPath = request.getServletPath();
        if (requestServletPath.startsWith("/admin") && null == request.getSession().getAttribute("loginUser")) {
            request.getSession().setAttribute("errorMsg", "请登陆");
            response.sendRedirect(request.getContextPath() + "/admin/login");
            return false;
        } else {
            request.getSession().removeAttribute("errorMsg");
            return true;
        //如果设置为false时，被请求时，拦截器执行到此处将不会继续操作
        //如果设置为true时，请求将会继续执行后面的操作
        }
    }

    /***
     * 请求处理之后进行调用，但是在视图被渲染之前（Controller方法调用之后）
     */
    @Override
    public void postHandle(HttpServletRequest request, HttpServletResponse response, Object handler, ModelAndView modelAndView) throws Exception {

    }
    /***
     * 整个请求结束之后被调用，也就是在DispatchServlet渲染了对应的视图之后执行（主要用于进行资源清理工作）
     */
    @Override
    public void afterCompletion(HttpServletRequest request, HttpServletResponse response, Object handler, Exception ex) throws Exception {

    }
}
```

`preHandle`在 Controller 之前执行，因此拦截器的功能主要就是在这个部分实现：

1. 检查 session 中是否有`user`对象存在；
2. 如果存在，就返回`true`，那么 Controller 就会继续后面的操作；
3. 如果不存在，就会重定向到登录界面
   就是通过这个拦截器，使得 Controller 在执行之前，都执行一遍`preHandle`.

### 1.2、实现`WebMvcConfigurer`接口，注册拦截器

实现`WebMvcConfigurer`接口来实现一个配置类，将上面实现的拦截器的一个对象注册到这个配置类中.

```Java
package com.sq.config;
@Configuration
public class NewBeeMallWebMvcConfigurer implements WebMvcConfigurer {

    @Autowired
    private AdminLoginInterceptor adminLoginInterceptor;

    @Override
    public void addInterceptors(InterceptorRegistry registry) {
        // 添加一个拦截器，拦截以/admin为前缀的url路径（后台登陆拦截）
        registry.addInterceptor(adminLoginInterceptor)
                .addPathPatterns("/admin/**")
                .excludePathPatterns("/admin/login")
                .excludePathPatterns("/admin/dist/**")
                .excludePathPatterns("/admin/plugins/**");

}
```

将拦截器注册到了拦截器列表中，并且指明了拦截哪些访问路径，不拦截哪些访问路径，不拦截哪些资源文件；最后再以 @Configuration 注解将配置注入。

### 1.3、保持登录状态

只需一次登录，如果登录过，下一次再访问的时候就无需再次进行登录拦截，可以直接访问网站里面的内容了。

在正确登录之后，就将`user`保存到`session`中，再次访问页面的时候，登录拦截器就可以找到这个`user`对象，就不需要再次拦截到登录界面了.



