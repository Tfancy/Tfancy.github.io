---
title: springboot系列之集成freemarker
date: 2019-03-20 17:35:24
tags: springboot
---
FreeMarker是一款用java语言编写的模版引擎，它虽然不是web应用框架，但它很合适作为web应用框架的一个组件。  

特点：  


1. 轻量级模版引擎，不需要Servlet环境就可以很轻松的嵌入到应用程序中

2. 能生成各种文本，如html，xml，java，等

3. 入门简单，它是用java编写的，很多语法和java相似  

#### 在项目中引入freemarker分为三个步骤：
- **加入依赖**  
- **在application.properties配置freemarker**  
- **写controller和ftl页面**

#### **（1）加入freemarker依赖**
把以下代码加入到pom.xml即可

```
<dependency>
	<groupId>org.springframework.boot</groupId>
	<artifactId>spring-boot-starter-freemarker</artifactId>
</dependency>
```

#### （2）在application.properties配置freemarker
我用的是application.properties的配置方式，加入以下代码：

```
#设定ftl文件路径
spring.freemarker.tempalte-loader-path=classpath:/templates/
#关闭缓存，及时刷新，上线生产环境需要修改为true
spring.freemarker.cache=false
spring.freemarker.charset=UTF-8
spring.freemarker.check-template-location=true
spring.freemarker.content-type=text/html
spring.freemarker.expose-request-attributes=true
spring.freemarker.expose-session-attributes=true
spring.freemarker.request-context-attribute=request
spring.freemarker.suffix=.ftl

```
更多配置查看文末。
#### （3）写controller和ftl页面
##### FreeMarkerController
  
```
package com.tjm.freemarker;

import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.servlet.ModelAndView;

@Controller
@RequestMapping(value="/freemarker")
public class FreeMarkerController {

    @RequestMapping(value = "/test")
    public String findOne(){
        return "test";
    }

    //传值到页面
    @RequestMapping(value="/test2")
    public ModelAndView test2(ModelAndView mv){
        mv.addObject("name", "jack");
        mv.setViewName("test2");
        return mv;
    }

}

```
由于我们在步骤2配置了freemarker的路径是classpath:/templates/，所以我们要现在项目的**resources中创建一个templates的文件夹**。controller里面跳转的时候，路径写ftl的路径即可。以下贴出test.ftl和test2.ftl源码：
#### test.ftl

```
<!DOCTYPE>
<html>
<head>
    <title>freemark</title>
</head>
<body>
<h1>Hello freemark11111111111111</h1>
</body>
</html>


```
#### test2.ftl

```
<!DOCTYPE>
<html>
<head>
    <title>freemark</title>
</head>
<body>
<h1>Hello ${name}</h1>
</body>
</html>


```
最后启动项目，然后在浏览器输入：[localhost:8080/freemarker/test](https://note.youdao.com/)

--------------------**更多的 FreeMarker 配置详解**--------------------

spring.freemarker.allow-request-override=false  
翻译：设置是否允许HttpServletRequest属性覆盖（隐藏）控制器生成的具有相同名称的模型属性。 

spring.freemarker.allow-session-override=false   
翻译：设置是否允许HttpSession属性覆盖（隐藏）同名控制器生成的模型属性。  

spring.freemarker.cache=false  
翻译：启用模板缓存。

spring.freemarker.charset=UTF-8  
翻译：模板编码。  

spring.freemarker.check-template-location=true  
翻译：检查模板位置是否存在。  

spring.freemarker.content-type=text/html  
翻译：内容类型值。  

spring.freemarker.enabled=true  
翻译：为此技术启用MVC视图分辨率。  

spring.freemarker.expose-request-attributes=false  
翻译：设置在与模板合并之前是否应将所有请求属性添加到模型中。  

spring.freemarker.expose-session-attributes=false  
翻译：设置是否应该在与模板合并之前将所有HttpSession属性添加到模型中。  

spring.freemarker.expose-spring-macro-helpers=true  
翻译：设置是否公开一个由Spring的宏库使用的名为“springMacroRequestContext”的RequestContext。  

spring.freemarker.prefer-file-system-access=true  
翻译：首选文件系统访问模板加载。文件系统访问启用模板更改的热检测。  

spring.freemarker.prefix=  
翻译：在构建URL时，前缀会被视为查看名称。  

spring.freemarker.request-context-attribute=  
翻译：所有视图的RequestContext属性的名称。  

spring.freemarker.settings.*=  
翻译：将已知的FreeMarker键将被传递给FreeMarker的配置。 

spring.freemarker.suffix=  
翻译：在构建URL时被附加到查看名称的后缀。  

spring.freemarker.template-loader-path=classpath:/templates/  
翻译：模板路径列表。  

spring.freemarker.view-names=  
翻译：可以解决的视图名称的白名单。