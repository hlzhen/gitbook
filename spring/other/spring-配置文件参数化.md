# 配置文件参数化

## 将可变参数转移到.properties文件中
```xml
<!-- 抽取可变参数，例如数据库的连接配置 --> 
<!-- 整合参数配置文件 -->
<context:property-placeholder location="classpath:/db.properties"/>

<!-- 配置信息参数化 -->
<bean id="conn" class="org.example.spring.factory.ConnectionFactory">
    <property name="driverClassName" value="${jdbc.classDriver}"/>
    <property name="url" value="${jdbc.url}"/>
    <property name="username" value="${jdbc.username}"/>
    <property name="password" value="${jdbc.password}"/>
</bean>

<!-- 
.properties中配置如下
jdbc.classDriver=com.mysql.jdbc.Driver
jdbc.url=jdbc:mysql://localhost:3306/plo_admin?useSSL=false
jdbc.username=xxx
jdbc.password=xxx
 -->
```