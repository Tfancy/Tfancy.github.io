---
title: springboot系列之快速构建springboot项目的两种方式（一）
date: 2019-01-10 15:54:16
tags: springboot
---


该教程也是十分小白的说，主要是想先出了这个最简单构建，后面就很方便集成其他组件了。那么开始吧，本文将介绍两种构建方式：  
- 基于Spring官网构建  
- 基于IDEA构建  

## 基于Spring官网构建  
**第一步**，打开[https://start.spring.io/](https://note.youdao.com/)，如图：  
{% asset_img buildproject.png build%}
输入groupId和artifactId,以及依赖包，点击Generate Project。

**第二步**，解压第一步生成的springboot项目，导入IDEA。  
到这里，其实就已经完成springboot项目的构建了。找到xxxApplication.java文件，执行main方法即可。也许你不确定是不是真的成功了，那么我们做一个restful的接口来验证下。  
**第三步**，在pom.xml中加入spring web依赖包  

```
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
```
**第四步**，新建包com.tjm.controller,新建类   
**HelloController.java**  
```
package com.tjm.controller;

import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

@RestController
public class HelloController {

    @RequestMapping("hello")
    public Object test(){
        return "hello this lovely world";
    }
}
```
<font color=red>
**注意：**</font> 启动类xxxApplication.java必须放在代码的最上层，spring扫描的时候只会扫描启动类所在的包以及其子包。在这个例子里，启动类就应该放到com.tjm下面。不放也行！那就要在启动类主动扫描包的操作，如@ComponentScan(basePackages={"com.tjm.controller"})  

**第五步**，启动main方法，在浏览器中输入：[http://localhost:8080/hello](https://note.youdao.com/)。完~  


题外话：在根目录有两个文件mvnw和mvnw.cmd，如果你看他们不顺眼可以删掉的。（ 这两个文件只是允许你在没有Maven安装的情况下运行Maven项目）

## 基于IDEA构建

这个也挺简单的，看图就好~  
{% asset_img buildByIDEA_1.png build%}
{% asset_img buildByIDEA_2.png build%}
{% asset_img buildByIDEA_3.png build%}





