---
layout: w
title: springboot系列之集成mybatis
date: 2019-03-27 16:27:11
tags: springboot
---
**在已有springboot项目中引入mybatis分为三个步骤：**

- **创建数据库和表**
- **加入依赖**
- **配置mybatis**
- **写entity，mapper，service，test类**
  

### （1）创建数据库和表
**执行sql**

```
CREATE DATABASE IF NOT EXISTS `springboottest` 
USE `springboottest`;

CREATE TABLE IF NOT EXISTS `user` (
  `id` int(11) NOT NULL AUTO_INCREMENT,
  `name` varchar(50) NOT NULL,
  `age` int(11) DEFAULT NULL,
  `birthday` datetime DEFAULT NULL,
  PRIMARY KEY (`id`)
) ENGINE=InnoDB AUTO_INCREMENT=11 DEFAULT CHARSET=utf8;
```

### （2）加入mybatis和mysql依赖

把以下代码加入到pom.xml即可


```
        <!-- spring-boot整合mybatis -->
        <dependency>
            <groupId>org.mybatis.spring.boot</groupId>
            <artifactId>mybatis-spring-boot-starter</artifactId>
            <version>1.2.0</version>
        </dependency>

        <!-- https://mvnrepository.com/artifact/mysql/mysql-connector-java -->
        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
            <version>6.0.3</version>
        </dependency>
```

### （3）配置mybatis

首先，在application.properties中加入以下代码：


```
#mybatis配置（我的mysql端口是3307.注意修改）
spring.datasource.url=jdbc:mysql://localhost:3307/SpringBootTest?useUnicode=true&characterEncoding=UTF-8&useSSL=false&serverTimezone=GMT%2B8&allowMultiQueries=true
spring.datasource.username=root
spring.datasource.password=123456
#注意：我用的是mysql8.0+，驱动要改为com.mysql.cj.jdbc.Driver
spring.datasource.driver-class-name=com.mysql.cj.jdbc.Driver
```
然后，使用注解的方式配置数据源以及扫描mapper文件的路径，具体是在项目中加入以下两个配置文件：  
**MapperScannerConfig**

```
import org.mybatis.spring.mapper.MapperScannerConfigurer;
import org.springframework.boot.autoconfigure.AutoConfigureAfter;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

/**
 * 配置mybatis需要扫描的mapper接口
 */
@Configuration
//MapperScannerConfigurer执行的比较早，所以必须有下面的注解
@AutoConfigureAfter(MybatisConfig.class)
public class MapperScannerConfig {

    @Bean
    public MapperScannerConfigurer mapperScannerConfigurer() {
        MapperScannerConfigurer mapperScannerConfigurer = new MapperScannerConfigurer();
        mapperScannerConfigurer.setBasePackage("com.tjm.mybatis.mapper");
        return mapperScannerConfigurer;
    }
}

```
**MybatisConfig**

```
import org.apache.ibatis.session.SqlSessionFactory;
import org.mybatis.spring.SqlSessionFactoryBean;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.core.io.support.PathMatchingResourcePatternResolver;
import org.springframework.jdbc.datasource.DataSourceTransactionManager;

import javax.sql.DataSource;

@Configuration
public class MybatisConfig {

    /**
     * 注入一个默认数据源
     */
    @Autowired
    private DataSource dataSource;

    /**
     * SqlSessionFactory配置
     * @return
     * @throws Exception
     */
    @Bean
    public SqlSessionFactory sqlSessionFactoryBean() throws Exception {
        SqlSessionFactoryBean sqlSessionFactoryBean = new SqlSessionFactoryBean();
        sqlSessionFactoryBean.setDataSource(dataSource);

        PathMatchingResourcePatternResolver resolver = new PathMatchingResourcePatternResolver();
        //配置mapper文件位置
        sqlSessionFactoryBean.setMapperLocations(resolver.getResources("classpath:mapper/*.xml"));

        return sqlSessionFactoryBean.getObject();
    }

    /**
     * 配置事物管理器
     * @return
     */
    @Bean
    public DataSourceTransactionManager transactionManager() {
        DataSourceTransactionManager dataSourceTransactionManager = new DataSourceTransactionManager();
        dataSourceTransactionManager.setDataSource(dataSource);
        return dataSourceTransactionManager;
    }
}
```

### （4）写entity，mapper,service，test类
我的项目结构图：  
{% asset_img addMybatis.jpg springboot集成mybatis %}  
**UserEntity**

```
import java.util.Date;

public class UserEntity {

    private Integer id;
    private String name;
    private Integer age;
    private Date birthday;

    public Integer getId() {
        return id;
    }

    public void setId(Integer id) {
        this.id = id;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public Integer getAge() {
        return age;
    }

    public void setAge(Integer age) {
        this.age = age;
    }

    public Date getBirthday() {
        return birthday;
    }

    public void setBirthday(Date birthday) {
        this.birthday = birthday;
    }
}
```
**UserMapper**

```
import com.tjm.mybatis.entity.UserEntity;

public interface UserMapper {

    int insert(UserEntity userEntity);
}
```
**UserService**

```
import com.tjm.mybatis.entity.UserEntity;
import com.tjm.mybatis.mapper.UserMapper;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;

@Service
public class UserService {

    @Autowired
    private UserMapper userMapper;

    public int createUser(UserEntity userEntity){
        return userMapper.insert(userEntity);
    }
}
```
由于在步骤（3）配置了mapper的位置：classpath:mapper/*.xml，所以要在项目的resources下面创建一个mapper的文件夹，然后创建一个UserMapper.xml。这里的xml相当于是UserMapper.java接口类的实现，所以xml的名字最好和接口类的名字一致吧（不一致不会报错的）  
**UserMapper.xml**

```
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN" "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.tjm.mybatis.mapper.UserMapper">

    <insert id="insert" parameterType="com.tjm.mybatis.entity.UserEntity">
        <selectKey resultType="java.lang.Integer" keyProperty="id" keyColumn="id">
            SELECT LAST_INSERT_ID()
        </selectKey>
        insert into user(name,age,birthday) values(#{name},#{age},#{birthday})
    </insert>
</mapper>

```
最后写一个单元测试类：
**UserServiceTest**

```
import com.tjm.SpringBootMybatisApplication;
import com.tjm.mybatis.entity.UserEntity;
import com.tjm.mybatis.service.UserService;
import org.junit.Test;
import org.junit.runner.RunWith;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.test.context.junit4.SpringJUnit4ClassRunner;

import java.util.Date;

@RunWith(SpringJUnit4ClassRunner.class)
@SpringBootTest(classes = SpringBootMybatisApplication.class)
public class UserServiceTest {

    @Autowired
    private UserService userService;

    @Test
    public void createUser(){
        UserEntity user = new UserEntity();
        user.setName("test1");
        user.setAge(1);
        user.setBirthday(new Date());
        userService.createUser(user);
    }
}
```


启动测试类，再看看数据库，已经成功插入一条新纪录了，完~


----------------------搭建过程中遇到的问题----------------------  
报错：Caused by: java.sql.SQLException: The server time zone value '�й���׼ʱ��' is unrecognized or represents more than one time zone. You must configure either the server or JDBC driver (via the serverTimezone configuration property) to use a more specifc time zone value if you want to utilize time zone support.

解决方案：在数据库连接url上加上北京东八区的配置：**&serverTimezone=GMT%2B8**


