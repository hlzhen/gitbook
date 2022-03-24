# Spring AOP 动态代理

## 1. 概念
```markdown
1. 动态代理与静态代理的核心思想都在代理上面，核心目的都是对目标类(原始类)的一种增强，只是实现的方式略有差异。
而动态代理是对静态代理的一种加强，是为了解决静态代理的缺点而衍生的，其本质还是通过代理类对原始类增加额外功能。
```

## 2. 编码实现
### 2.1 环境搭建
```xml
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-context</artifactId>
    <version>5.2.6.RELEASE</version>
</dependency>

<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-aop</artifactId>
    <version>5.2.6.RELEASE</version>
</dependency>

<dependency>
    <groupId>org.aspectj</groupId>
    <artifactId>aspectjrt</artifactId>
    <version>1.9.5</version>
</dependency>

<dependency>
    <groupId>org.aspectj</groupId>
    <artifactId>aspectjweaver</artifactId>
    <version>1.9.5</version>
</dependency>
```

### 2.2 创建原始对象
```java
/**
 * 原始类(目标类)
 * Created by Ale on 2022/3/23
 */
public class UserServiceImpl implements UserService {

    @Override
    public boolean login(String username, String password) {
        // 目标方法
        System.out.println("UserServiceImpl.login, username:" + username);
        return true;
    }

    @Override
    public void register(User user) {
        // 目标方法
        System.out.println("UserServiceImpl.register user:" + user);
    }
}
```
```xml
<bean id="userService" class="org.example.spring.proxy.UserServiceImpl"></bean>
```
### 2.3 额外功能(MethodBeforeAdvice接口)
```java
/**
 * 运行在目标方法之前的额外功能
 * Created by Ale on 2022/3/24
 */
public class MyBeforeAdvice implements MethodBeforeAdvice {
    @Override
    public void before(Method method, Object[] objects, Object o) throws Throwable {
        System.out.println("MyBeforeAdvice.before");
    }
}
```
```xml
<bean id="myBefore" class="org.example.spring.dynamic.MyBeforeAdvice"/>
```

### 2.4 定义切入点
```markdown
切入点：额外功能增加的具体位置
目的：具体需要把额外功能加入到哪个目标方法中或者说所有目标方法都增加
```
```xml
<aop:config>
    <!-- 定义切入点，代表所有目标方法都添加额外功能 -->
    <aop:pointcut id="pc" expression="execution(* *(..))"/>
</aop:config>
```

### 2.5 整合切入点与额外功能
```xml
<aop:config>
    <!-- 定义切入点，代表所有目标方法都添加额外功能 -->
    <aop:pointcut id="pc" expression="execution(* *(..))"/>

    <!-- 整合切入点与额外功能 -->
    <aop:advisor advice-ref="myBefore" pointcut-ref="pc"/>
</aop:config>
```
### 2.6 测试
```java
//创建工厂对象
 ClassPathXmlApplicationContext context = new ClassPathXmlApplicationContext("/applicationContext5.xml");

//如何获取代理对象？ spring工厂通过原始对象的id值获取的就是代理对象，获取的代理对象可通过原始对象的接口类型声明
UserService userService = (UserService) context.getBean("userService");
userService.login("xxh", "123456");
```

### 2.7 分析
```markdown
1. Spring动态的代理类在那里？
    - Spring框架运行时，通过动态字节码技术，在JVM内创建的运行在JVM内，程序结束后随着JVM一起消失

2. 什么叫动态字节码技术？
    - 通过第三方字节码技术框架，在JVM中创建对应类的字节码进而创建对象。

3. 动态字节码技术的框架
    - ASM
    - Javassist
    - Cglib

- 动态代理相比静态代理是不需要定义代理类文件，都是由JVM运行过程中动态创建的，所以跟静态代理一样，代理类文件过多影响项目管理。
- 动态代理简化代理开发
- 动态代理维护性大大增强
```

## 3. 动态代理详解
### 3.1 MethodBeforeAdvice接口
```markdown
1. 接口方法: 
    - void before(Method method, Object[] args, @Nullable Object target) throws Throwable
    - 在...之前执行
2. 参数解释：
    - method 目标(原始类)方法
    - args 目标(原始类)方法参数
    - target 目标类(原始类)
```