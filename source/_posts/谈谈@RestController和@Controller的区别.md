---
title: 谈谈@RestController和@Controller的区别
date: 2019-03-15 17:40:42
tags: Spring
---

先让我们来看看官方文档对这两个注解的解析~

**文档上解析@RestController：**  
A convenience annotation that is itself annotated with @Controller and @ResponseBody.
Types that carry this annotation are treated as controllers where @RequestMapping methods assume @ResponseBody semantics by default.

**文档上解析@Controller：**  
Indicates that an annotated class is a "Controller" (e.g. a web controller).
This annotation serves as a specialization of @Component, allowing for implementation classes to be autodetected through classpath scanning. It is typically used in combination with annotated handler methods based on the RequestMapping annotation.

**文档上解析@ResponseBody：**  
Annotation that indicates a method return value should be bound to the web response body. 
（指示方法返回值的注释应该绑定到web响应主体)
Supported for annotated handler methods in Servlet environments.

**总结一下:**  
(1) @RestController = @Controller + @ResponseBody  
(2) @RestController用在返回json，xml等数据格式数据的场景，不能返回视图。  
(3) @Controller标识一个类为控制器，配合视图解析器 InternalResourceViewResolver可返回页面以及配合@ResponseBody便可返回json，xml等数据格式的数据。
