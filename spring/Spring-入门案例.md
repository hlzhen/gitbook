# IOC入门案例

## 1. 版本下载
```git
官方下载地址：https://repo.spring.io/ui/native/release/org/springframework/spring
```

## 2. 创建Java工程
```git
// 引入相关Jar包
commons-logging-1.1.1.jar 
spring-beans-5.2.6.RELEASE.jar 
spring-context-5.2.6.RELEASE.jar 
spring-core-5.2.6.RELEASE.jar 
spring-expression-5.2.6.RELEASE.jar

注意：commons-logging是额外依赖的日志包，需自行下载，下载地址：https://mvnrepository.com/
```

## 3. 创建对应bean.xml配置文件
```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">

    <!-- id为当前bean对象唯一名称，class表示需要创建对象的类路径 User为需要交给Spring管理的自定义对象-->
    <bean id="user" class="com.proerp.spring.User"/>
</beans>
```

```java
package com.proerp.spring;

// 由于当前Gitbook插件对于代码块的格式还不是很完善，导致类注释与多行注释存在错位问题，所以就暂时使用单行注释代替
public class User {
    // 添加用户
    public void add() {
        System.out.println("添加用户..");
    }
}
```

## 4. 获取bean对象并进行调用
```java
// 测试通过xml配置方式创建Bean对象
// Created by Ale on 2022/3/12
public class TestUser {

    @Test
    public void testAdd() {
        // 1、加载spring.xml配置文件
        ApplicationContext context = new ClassPathXmlApplicationContext("bean1.xml");


        // 2、获取spring.xml配置文件中的配置对象(bean)
        User user = context.getBean("user", User.class);
        user.add();
    }
}
```