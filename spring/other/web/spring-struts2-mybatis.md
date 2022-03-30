# SpringWeb开发 - SSM整合(基于配置文件)

## 1 创建web工程进行环境搭建
```xml
<?xml version="1.0" encoding="UTF-8"?>

<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>org.example.web</groupId>
    <artifactId>spring5-struts2-mybatis</artifactId>
    <version>1.0</version>
    <packaging>war</packaging>

    <name>spring5-struts2-mybatis Maven Webapp</name>
    <!-- FIXME change it to the project's website -->
    <url>http://www.example.com</url>

    <properties>
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
        <maven.compiler.source>1.8</maven.compiler.source>
        <maven.compiler.target>1.8</maven.compiler.target>

        <java.version>1.8</java.version>
        <struts2-version>2.3.8</struts2-version>
        <spring-version>5.2.6.RELEASE</spring-version>
        <mysql-connector-version>5.1.47</mysql-connector-version>
        <spring-jdbc-version>5.2.13.RELEASE</spring-jdbc-version>
        <spring-mybatis-version>2.0.6</spring-mybatis-version>
        <mybatis-version>3.5.6</mybatis-version>
        <druid-version>1.2.6</druid-version>
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

        <!-- mysql驱动包 -->
        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
            <version>${mysql-connector-version}</version>
        </dependency>

        <!--spring针对jdbc封装-->
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-jdbc</artifactId>
            <version>${spring-jdbc-version}</version>
        </dependency>

        <!-- mybatis与spring整合相关 -->
        <dependency>
            <groupId>org.mybatis</groupId>
            <artifactId>mybatis-spring</artifactId>
            <version>${spring-mybatis-version}</version>
        </dependency>

        <!-- mybatis -->
        <dependency>
            <groupId>org.mybatis</groupId>
            <artifactId>mybatis</artifactId>
            <version>${mybatis-version}</version>
        </dependency>

        <!-- druid 连接池 -->
        <dependency>
            <groupId>com.alibaba</groupId>
            <artifactId>druid</artifactId>
            <version>${druid-version}</version>
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

## 2. 引入配置文件
```markdown
1. Spring -> applicationContext.xml
2. Struts2 -> struts.xml
3. log4j -> log4j.properties
三个文件在resources文件夹下创建
```

## 3. 初始化web.xml配置
```xml
<?xml version="1.0" encoding="UTF-8" ?>
<web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="
            http://xmlns.jcp.org/xml/ns/javaee
            http://xmlns.jcp.org/xml/ns/javaee/web-app_3_1.xsd"
         version="3.1"
         metadata-complete="true">
    <display-name>Archetype Created Web Application</display-name>

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

## 4. 整合mybatis
```markdown
1. 配置文件的配置
    1.DataSource
    2.SqlSessionFactory ----SqlSessionFactoryBean
        1.dataSource
        2.typeAliasesPackage
        3.mapperLocations
    3.MapperScannerConfigur-->DAO接口实现类
2.编码
    1.entity
    2.table
    3.DA0接口
    4.实现Mapper文件
    5.编写service加入事物
        1. 原始对象
        2. 额外功能
        3. 切入点(@Tranactionnal)
        4. 组装切面<tx:annotation-driven>
```
```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:tx="http://www.springframework.org/schema/tx"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
       http://www.springframework.org/schema/beans/spring-beans.xsd http://www.springframework.org/schema/tx http://www.springframework.org/schema/tx/spring-tx.xsd">

    <!-- 连接池配置 -->
    <bean id="dataSource" class="com.alibaba.druid.pool.DruidDataSource">
        <property name="driverClassName" value="com.mysql.jdbc.Driver"/>
        <property name="url" value="jdbc:mysql://localhost:3306/huanglz?useSSL=false"/>
        <property name="username" value="root"/>
        <property name="password" value="root"/>
    </bean>

    <!-- 配置SqlSessionFactoryBean -->
    <bean id="sqlSessionFactoryBean" class="org.mybatis.spring.SqlSessionFactoryBean">
        <!-- 数据源连接池指定 -->
        <property name="dataSource" ref="dataSource"/>

        <!-- 实体别名配置 -->
        <property name="typeAliasesPackage" value="org.example.spring.pojo"/>

        <!-- mapper.xml映射路径 -->
        <property name="mapperLocations">
            <list>
                <value>classpath:mapper/*Mapper.xml</value>
            </list>
        </property>
    </bean>

    <!-- 配置DAO对象指定包的扫描路径 -->
    <bean id="mapperScanner" class="org.mybatis.spring.mapper.MapperScannerConfigurer">
        <property name="sqlSessionFactoryBeanName" value="sqlSessionFactoryBean"/>
        <property name="basePackage" value="org.example.web.dao"/>
    </bean>

    <!-- AOP编程四部曲 1.原始对象 -->
    <bean id="userService" class="org.example.web.service.UserServiceImpl">
        <property name="userDAO" ref="userDAO"/>
    </bean>

    <!-- AOP编程四部曲 2.额外功能 -->
    <bean id="dataSourceTransactionManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
        <property name="dataSource" ref="dataSource"/>
    </bean>

    <!-- AOP编程四部曲 3.切入点 @Transactional -->

    <!-- AOP编程四部曲 4.组装额外功能与切入点  -->
    <tx:annotation-driven transaction-manager="dataSourceTransactionManager"/>
</beans>
```

## 5. 整合Struts2
```markdown
1.开发控制器implements Action注入Service
2.Spring的配置文件
    1.注入Service
    2.scope prototype
3.struts.xml
<action class="spring配置文件中action对应的id值"/>
```
```java

/**
 * 需要注入此Action
 * Created by Ale on 2022/3/30
 */
public class RegAction implements Action {
    private UserService userService;

    private User user;

    public void setUser(User user) {
        this.user = user;
    }

    public void setUserService(UserService userService) {
        this.userService = userService;
    }

    @Override
    public String execute() throws Exception {
        userService.register(user);
        return Action.SUCCESS;
    }
}
```
```xml
<!-- 注入action -->
<bean id="regAction" scope="prototype" class="org.example.web.action.RegAction">
    <property name="userService" ref="userService"/>
</bean>
```
```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE struts PUBLIC
        "-//Apache Software Foundation//DTD Struts Configuration 2.0//EN"
        "http://struts.apache.org/dtds/struts-2.0.dtd">
<struts>
    <package name="ssm" extends="struts-default">
        <action name="reg" class="regAction">
            <!-- 请求成功后的跳转页面 -->
            <result name="success">/regOk.jsp</result>
        </action>
    </package>
</struts>
```
