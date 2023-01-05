---
title: spring拦截器资源控制
tag: JAVA奇技淫巧
---
<!--more-->
## 一、前文
> 场景：对于系统中公共资源进行放行，对需要访问控制的资源进行权限拦截。
## 二、思路
> 客户端上传文件时，根据不同功能模块上传到不同文件目录中。

> 如头像上传到/avatar目录中。证书资源上传到/cert目录中。上传接口必须定义**统一资源前缀**，便于拦截器拦截控制。

> 拦截器根据业务目录（如/avatar，/cert）需要对某些资源进行放行，不放行的资源必须存在token才可以访问，即实现了对资源的访问控制。

## 三、实践
> 引入 ResourceConfig

**定义统一资源前缀**:Constants.RESOURCE_PREFIX--->**/profile**
定义资源映射的文件目录:DccSmart.getProfile()--->/usr/local/upload



```JAVA
@Configuration
public class ResourcesConfig implements WebMvcConfigurer
{
 
    @Autowired
    private PhotoInterceptor photoInterceptor;

    @Override
    public void addResourceHandlers(ResourceHandlerRegistry registry)
    {
  
        registry.addResourceHandler(Constants.RESOURCE_PREFIX + "/**")
                .addResourceLocations("file:" + DccSmart.getProfile() + "/");


        /** swagger配置 */
        registry.addResourceHandler("/swagger-ui/**")
                .addResourceLocations("classpath:/META-INF/resources/webjars/springfox-swagger-ui/");
    }

    /**
     * 自定义拦截规则
     */
    @Override
    public void addInterceptors(InterceptorRegistry registry)
    {
                   registry.addInterceptor(photoInterceptor).addPathPatterns(Constants.RESOURCE_PREFIX + "/**");
    }

    /**
     * 跨域配置
     */
    @Bean
    public CorsFilter corsFilter()
    {
        CorsConfiguration config = new CorsConfiguration();
        config.setAllowCredentials(true);
        // 设置访问源地址
        config.addAllowedOriginPattern("*");
        // 设置访问源请求头
        config.addAllowedHeader("*");
        // 设置访问源请求方法
        config.addAllowedMethod("*");
        // 有效期 1800秒
        config.setMaxAge(1800L);
        // 添加映射路径，拦截一切请求
        UrlBasedCorsConfigurationSource source = new UrlBasedCorsConfigurationSource();
        source.registerCorsConfiguration("/**", config);
        // 返回新的CorsFilter
        return new CorsFilter(source);
    }
}
```
> 举例：
> 头像资源：http://localhost:8080/profile/avatar/abc.png;
> 文件路径：/usr/local/upload/avatar/abc.png


> 引入PhotoInterceptor拦截器
资源拦截器可拦截/profile前缀的资源，若为资源地址时进行访问控制。对于公共资源进行放行，对于业务资源进行控制。
```JAVA
/**
 * 资源拦截器
 */
@Component
@Slf4j
public class PhotoInterceptor implements HandlerInterceptor {


    @Autowired
    private TokenService tokenService;

    @Override
    public boolean preHandle(HttpServletRequest httpServletRequest, HttpServletResponse httpServletResponse, Object o) throws Exception {
        log.info("###preHandle");
        if (hasAuth(httpServletRequest)) {
            return true;
        } else {
            // 设置返回内容
            httpServletResponse.setStatus(HttpStatus.FORBIDDEN.value());
            httpServletResponse.setContentType(MediaType.APPLICATION_JSON_VALUE);
            httpServletResponse.setCharacterEncoding("UTF-8");
            httpServletResponse.getWriter().write("认证失败，无法访问资源");
            return false;
        }
    }

    /**
     * 后期处理，业务方法执行完成后执行，可以对业务返回的数据进行处理。
     * DispatcherServlet试图处理之前运行，可以对ModelAndView进行调整。
     *
     * @param httpServletRequest
     * @param httpServletResponse
     * @param o
     * @param modelAndView
     * @throws Exception
     */
    @Override
    public void postHandle(HttpServletRequest httpServletRequest, HttpServletResponse httpServletResponse, Object o, ModelAndView modelAndView) throws Exception {
        log.info("###postHandle");
    }

    /**
     * 请求处理完成后返回是执行，一般用于资源清理。
     * 在DispatcherServlet完全处理完后调用
     *
     * @param httpServletRequest
     * @param httpServletResponse
     * @param o
     * @param e
     * @throws Exception
     */
    @Override
    public void afterCompletion(HttpServletRequest httpServletRequest, HttpServletResponse httpServletResponse, Object o, Exception e) throws Exception {
        log.info("###afterCompletion");
    }


    private boolean hasAuth(HttpServletRequest request) {
        String requestURI = request.getRequestURI();
      
        //所有apk放行
        if (requestURI.contains("apk")) {
            return true;
        }
        //所有头像放行
        if (requestURI.contains("avatar")) {
            return true;
        }
        //获取cookies中的token
            Cookie[] cookies = request.getCookies();
            if (null == cookies) {
                return false;
            }
            String tokenValue = null;

            for (Cookie x : cookies) {
                String name = x.getName();
                if (name.equals("Admin-Token")) {
                    tokenValue = x.getValue();
                }
            }
            //若token存在则校验token合法性
            return tokenService.verify(tokenValue) != null;
       
    }
}

```


