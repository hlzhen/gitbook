# Spring整合Struts2

## 1. 环境搭建
```xml
<?xml version="1.0" encoding="UTF-8"?>

<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>org.example.web</groupId>
    <artifactId>spring5-web</artifactId>
    <version>1.0-SNAPSHOT</version>
    <packaging>war</packaging>

    <name>spring5-web Maven Webapp</name>
    <!-- FIXME change it to the project's website -->
    <url>http://www.example.com</url>

    <properties>
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
        <maven.compiler.source>1.8</maven.compiler.source>
        <maven.compiler.target>1.8</maven.compiler.target>

        <java.version>1.8</java.version>
        <struts2-version>2.3.8</struts2-version>
        <spring-version>5.2.6.RELEASE</spring-version>
        <aspectjrt-version>1.9.5</aspectjrt-version>
        <aspectjweaver-version>1.9.5</aspectjweaver-version>
    </properties>

    <dependencies>
        <!-- wbe 开发对应依赖 -->
        <dependency>
            <groupId>javax.servlet</groupId>
            <artifactId>javax.servlet-api</artifactId>
            <version>3.1.0</version>
            <scope>provided</scope>
        </dependency>
        <dependency>
            <groupId>javax.servlet</groupId>
            <artifactId>jstl</artifactId>
            <version>1.2</version>
        </dependency>
        <dependency>
            <groupId>javax.servlet.jsp</groupId>
            <artifactId>javax.servlet.jsp-api</artifactId>
            <version>2.3.3</version>
            <scope>provided</scope>
        </dependency>

        <!-- struts2-mvc依赖 -->
        <dependency>
            <groupId>org.apache.struts</groupId>
            <artifactId>struts2-spring-plugin</artifactId>
            <version>${struts2-version}</version>
        </dependency>

        <!-- spring对web开发的支持依赖 -->
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-web</artifactId>
            <version>${spring-version}</version>
        </dependency>

        <!-- spring核心 -->
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-core</artifactId>
            <version>${spring-version}</version>
        </dependency>

        <!-- bean注解的开发 -->
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-beans</artifactId>
            <version>${spring-version}</version>
        </dependency>

        <!-- spring-context依赖注入容器 -->
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-context</artifactId>
            <version>${spring-version}</version>
        </dependency>
        <!-- spring事物控制 -->
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-tx</artifactId>
            <version>${spring-version}</version>
        </dependency>
        <!-- spring-aop面向切面 -->
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-aop</artifactId>
            <version>${spring-version}</version>
        </dependency>
        <!-- aop 注解相关 -->
        <dependency>
            <groupId>org.aspectj</groupId>
            <artifactId>aspectjrt</artifactId>
            <version>${aspectjrt-version}</version>
        </dependency>
        <!-- aop 切入点表达式相关依赖 -->
        <dependency>
            <groupId>org.aspectj</groupId>
            <artifactId>aspectjweaver</artifactId>
            <version>${aspectjweaver-version}</version>
        </dependency>

        <!-- log4j日志 -->
        <dependency>
            <groupId>org.slf4j</groupId>
            <artifactId>slf4j-log4j12</artifactId>
            <version>1.7.30</version>
        </dependency>
        <dependency>
            <groupId>log4j</groupId>
            <artifactId>log4j</artifactId>
            <version>1.2.17</version>
        </dependency>
    </dependencies>
</project>
```

## 2. Spring整合MVC(Struts2)框架的核心思路
### 2.1 准备工厂
```markdown
1. 工厂的创建方式
 - AppicationContext context = new ClassPathXmlAppicationContext("/applicationContext.xml");
2. 基于Web的创建方式
AppicationContext context = new WebXmlAppicationContext("/applicationContext.xml");

3. 如何保证工厂唯一性的同时又能被公用
 - 工厂是一个重量级资源，那么如果在每一次调用的同时去创建的话会非常消耗内存资源，那结合Web ServletContext作用域的引入就可避免这个工厂被创建多次进而又可达到共用目的

 - ServletContext 特点就是只创建一次并且整个应用可通过ServletContext.getAttribute("xxxx")获取对应数据。
 那么在ServletContext创建时可通过ServletContextListener监听器监听，只要ServletContext创建就可创建Spring的工厂从而达到唯一性

4. 总结
 ServletContextListener(唯一)
    AppicationContext context = new WebXmlAppicationContext("/applicationContext.xml");
    ServletContext.setAttribute("xxxx",context);(共用)

5. ContextLoaderListener
 - Spring的ContextLoaderListener就是针对上述问题的封装,通过web.xml配置即可


<listener>
    <!-- 加载Spring工厂 -->
    <listener-class>org.springframework.web.context.ContextLoaderListener</listener-class>
</listener>

<context-param>
    <!-- 指定Spring配置文件位置 -->
    <param-name>contextConfigLocation</param-name>
    <param-value>classpath:applicationContext.xml</param-value>
</context-param>
```

### 2.2 配置文件引入
1. Spring的applicationContext.xml
2. struts2的struts.xml
3. log4j.propertis

### 2.3 初始化配置
```xml
<?xml version="1.0" encoding="UTF-8" ?>
<web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="
            http://xmlns.jcp.org/xml/ns/javaee
            http://xmlns.jcp.org/xml/ns/javaee/web-app_3_1.xsd"
         version="3.1"
         metadata-complete="true">

    <!-- 加载Spring工厂 -->
    <listener>
        <listener-class>org.springframework.web.context.ContextLoaderListener</listener-class>
    </listener>

    <!-- 指定Spring配置文件 -->
    <context-param>
        <param-name>contextConfigLocation</param-name>
        <param-value>classpath:applicationContext.xml</param-value>
    </context-param>

    <!-- 配置struts2 -->
    <filter>
        <filter-name>struts2</filter-name>
        <filter-class>org.apache.struts2.dispatcher.ng.filter.StrutsPrepareAndExecuteFilter</filter-class>
    </filter>
    <filter-mapping>
        <filter-name>struts2</filter-name>
        <url-pattern>/*</url-pattern>
    </filter-mapping>
</web-app>
```
```properties
###配置根
log4j.rootLogger=debug,console
##日志输出到控制台显示
log4j.appender.console=org.apache.log4j.ConsoleAppender
log4j.appender.console.Target=System.out
log4j.appender.console.layout=org.apache.log4j.PatternLayout
log4j.appender.console.layout.ConversionPattern=%d{yyyy-MM-dd HH:mm:ss} %-5p %c{1}:%L - %m%n
```

### 2.4 服务层开发
```java
public interface UserService {

    void register(User user);
}

public class UserServiceImpl implements UserService {

    @Override
    public void register(User user) {
        System.out.println("UserServiceImpl.register");
    }
}
```

```java
public class RegAction implements Action {

    private UserService userService;

    public UserService getUserService() {
        return userService;
    }

    public void setUserService(UserService userService) {
        this.userService = userService;
    }

    @Override
    public String execute() throws Exception {
        userService.register(new User());
        return Action.SUCCESS;
    }
}
```
```xml
<!-- spring配置 -->
<bean id="userService" class="org.example.web.service.UserServiceImpl"/>

<bean id="regAction" scope="prototype" class="org.example.web.action.RegAction">
    <property name="userService" ref="userService"/>
</bean>
```
```xml
<!-- struts2配置 -->
<package name="ssm" extends="struts-default">
    <!-- 
        通过url访问action时如果访问成功则返回对应的jsp页面
        因为需要访问Action但此时Spring中已经配置了对应的Action所以就直接使用Spring创建的Action即可
     -->
    <action name="reg" class="regAction">
        <result name="success">/index.jsp</result>
    </action>
</package>
```

### 2.5 测试
```markdown
把应用部署到tomcat中浏览器访问localhost:端口号/reg.action即可
```