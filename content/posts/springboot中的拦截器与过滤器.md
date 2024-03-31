---
share: "true"
title: springboot中的拦截器与过滤器
date: 2019-03-20 21:54:26
tags: 
---

# springboot中的拦截器与过滤器

关于过滤器和拦截器的区别,在此不展开,仅仅记录下在一个springboot项目中如何配置生效过滤器以及拦截器

## 过滤器

一般来讲,我们会使用FilterRegistrationBean来注册过滤器.使用流程如下:

### 定义过滤器

```
public class MyFilter implements Filter {
	@Override
	public void init(FilterConfig filterConfig) throws ServletException {

	}

	@Override
	public void doFilter(ServletRequest servletRequest, ServletResponse servletResponse, FilterChain filterChain) throws IOException, ServletException {
		System.out.println("doFilter");
		filterChain.doFilter(servletRequest, servletResponse);
		return;
	}

	@Override
	public void destroy() {

	}
}
```

在这个过滤器中,我们仅仅是打印日志,之后将调用链接着传递

### 配置过滤器

```
@Configuration
public class FilterConfig {
	@Bean
	public FilterRegistrationBean CASFilter(){
		FilterRegistrationBean registration = new FilterRegistrationBean();
		registration.addUrlPatterns("/*");
		Filter filter = new MyFilter();
		registration.setFilter(filter);
		return registration;
	}
}
```

* 注意Configuration和Bean注解的使用
* 注意过滤器的作用范围(registration.addUrlPatterns)

### 编写测试接口

```
@RestController
public class TestController {
	@RequestMapping(value = "/test", method = RequestMethod.GET)
	public String test() {
		return "test";
	}
}
```

### 调用测试接口

可以看到控制台打印如下:

```
doFilter
```

### 后记

* 过滤器经常被用在登录请求的校验中,也可以用在一些请求的加解密当中.
* 多个过滤器可以通过设置优先级达到确定执行顺序的目的.

## 拦截器

#### 自定义拦截器

```
public class MyInterceptor implements HandlerInterceptor {
	@Override
	public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
		System.out.println("preHandle");
		return true;
	}

	@Override
	public void postHandle(HttpServletRequest request, HttpServletResponse response, Object handler, ModelAndView modelAndView) throws Exception {
		System.out.println("postHandle");

	}

	@Override
	public void afterCompletion(HttpServletRequest request, HttpServletResponse response, Object handler, Exception ex) throws Exception {
		System.out.println("afterCompletion");
	}
}
```

* 注意重载的preHandle的返回值,如果返回false,则请求不会接着往下走.

#### 配置拦截器

```
@Configuration
public class InterceptorConfig implements WebMvcConfigurer {
	@Override
	public void addInterceptors(InterceptorRegistry registry) {
		registry.addInterceptor(new MyInterceptor());
	}
}
```

#### 调用测试接口

可以看到控制台打印如下

```
doFilter
preHandle
postHandle
afterCompletion
```

很直观的可以看到执行顺序.

## 后记

关于拦截器和过滤器的区别,只能再仔细研究,再开一篇.
