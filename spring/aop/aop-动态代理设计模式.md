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

### 3.2 MethodInterceptor接口
```markdown
1. MethodInterceptor接口 可以根据需要运行在目标方法执行前、后、前后
```
```java
/**
 * Created by Ale on 2022/3/26
 */
public class Around implements MethodInterceptor {

    /*
        invoke方法的作用：额外功能书写在invoke
                        额外功能 原始方法之前
                        原始方法之后
                        原始方法执行之前之后
        确定：原始方法怎么运行
        参数：MethodInvocation
             (Method):额外功能所增加给的那个原始方法
        返回值：Object:原始方法的返回值 ，如需影响目标方法返回值则不要直接返回目标方法运行结果(前提是要与目标方法返回值类型相等)
        例如，直接返回false
    */
    @Override
    public Object invoke(MethodInvocation invocation) throws Throwable {
        // 运行在目标方法之前
        System.out.println("----- before ------");
        Object proceed = invocation.proceed();
        return false;
    }

    @Override
    public Object invoke(MethodInvocation invocation) throws Throwable {
        // 运行在目标方法之后
        Object proceed = invocation.proceed();
        System.out.println("----- after ------");
        return proceed;
    }

    @Override
    public Object invoke(MethodInvocation invocation) throws Throwable {
        // 运行在目标方法前后
        System.out.println("----- before ------");
        Object proceed = invocation.proceed();
        System.out.println("----- after ------");
        return proceed;
    }

    @Override
    public Object invoke(MethodInvocation invocation) throws Throwable {
        Object proceed = null;
        try {
            System.out.println("----- before ------");
            proceed = invocation.proceed();
            System.out.println("----- after ------");
        } catch (Throwable e) {
            System.out.println("----- exception -------");
            e.printStackTrace();
        } finally {
            System.out.println("------ finally ------");
        }
        return proceed;
    }
}
```
```xml
    <bean id="userService" class="org.example.spring.proxy.UserServiceImpl"></bean>

    <!-- <bean id="myBefore" class="org.example.spring.dynamic.MyBeforeAdvice"/> -->

    <bean id="around" class="org.example.spring.dynamic.Around"/>

    <aop:config>
        <!-- 定义切入点，代表所有目标方法都添加额外功能 -->
        <aop:pointcut id="pc" expression="execution(* *(..))"/>

        <!-- 整合切入点与额外功能  -->
        <aop:advisor advice-ref="around" pointcut-ref="pc"/>
    </aop:config>
```

## 4. 切入点详解
```xml
<aop:pointcut id="pc" expression="execution(* *(..))"/>
execution(* *(..)) 代表所有方法 
execution() 代表切入点函数
* *(..)     代表切入点表达式
```
### 4.1 方法切入点表达式
```markdown
1. 对方法无要求 切入点表达式
* *(.. )
* ---> 第一个*代表方法修饰符，返回值
* ---> 第二个*代表方法名
()---> 参数表
.. ---> 对于参数没要求(参数有或者没有，参数有多少、参数类型是什么都可以)

2. 针对某个方法进行切入且对修饰符、返回值、参数无要求
* xxx(..)

3. 针对某个方法且方法参数有两个类型为String的方法切入
* xxx(String,String)

注意：
    - 如需针对自定义类型则需要此类型的全类名(xxx.xxx.xxx.User)
    例如：* register(org.example.spring.User)
    - 针对第一个参数有明确要求且后续参数无要求的
    * xxx(String,..) 可匹配xxx(String),xxx(String,String),xxx(String,User),xxx(String,User,int)

4. 精准切入表达式
修饰符、返回值  包.类.方法(参数表)
* xx.xxx.UserServiceImpl.login(..)
* xx.xxx.UserServiceImpl.login(String,String)
```

### 4.2 类切入点表达式
```markdown
1. 指定特定的类作为切入点，自然该类中的所有方法都会加上对应的额外功能
    - 指定包
        * xxx.xxx.xxx.UserServiceImpl.*(..)
    - 不指定包
        * *.UserServiceImpl.*(..) 一级包
        * *..UserServiceImpl.*(..) 多级包
```

### 4.3 包切入点表达式
```markdown
指定包作为额外功能的加入位置，该包中的所有类所有方法都会加入此额外功能
* xxx.xxx.proxy.*.*(..)  注意：此表达式不能支持proxy的子包
* xxx.xxx.proxy..*.*(..)   表示子包也生效
```

## 5. 切入点函数
```markdown
1. execution()函数
    - 用于执行切入点表达式，是最为重要的切入点函数，功能最全。可执行方法，类，包切入点表达式

2. args()函数
    - 主要用于方法参数的匹配 
    - 例如args(String,String) 
    - args(String,String) 与 execution(* xxx(String,String)) 是一致的

3. within()函数
    - 主要用于类，包切入点表达式的匹配
    - 例如within(*..UserServiceImpl.*(..))
    - within(*..UserServiceImpl.*(..)) 与 execution(* *..UserServiceImpl.*(..)) 一致

4. @annotation()函数
    - 为具有特殊注解的方法加入额外功能
    - @annotation(xxx.xx.Log)

5. 切入点函数的逻辑运算(and,or)
## and与操作
    - 匹配方法是login 同时 参数为(String,String)
    - execution(* login(String,String)) 
    - execution(* login(..)) and args(String,String) 
    - 注意：and 操作不能用于同种类型的切入点函数

## or或者操作
    - 可用于同种类型的切入点函数
    - execution(* login(..)) or execution(* register(..))
```
