# Spring-注解详解

## 1. 基础注解
```markdown
1. @Component
    - @Component注解用于标注在类上,被标注的类表示将由Spring管理并创建 
    - @Component 等同于 <bean id="user" class="xxx.xxx.User"/>

2. @Scope
    - @Scope("singleton | prototype") 注解用于标注在类上
    - singleton 是默认属性表示单例 被该注解该属性标注的类会与spring工厂一同创建，无论创建多少次拿到的都是同一个实例
    - prototype 表示当前创建的是原型对象，获取时才创建，无论创建多少次都会生成新的实例
    - @Scope 与配置文件Scope属性一致 <bean id="user" Scope="singleton | prototype" class="xxx.xxx.User"/>

3. @Lazy
    - @Lazy(懒加载) 注解用于标注在类上，通常与@Scope结合使用
    - @Lazy 与配置文件lazy-init属性一致 <bean id="user" Scope="singleton | prototype" lazy-init="true" class="xxx.xxx.User"/>

4. @Autowired
    - @Autowired 用于成员属性注入，只按照类型进行注入。
    - @Autowired 如想按照名称注入，需要结合@Qualifier注解一起使用
    
5. @Autowired 与 @Resource 的区别 ？
    - @Autowired 注解由Spring提供 而 @Resource 是由J2EE提供
    - @Autowired只按照类型注入
    - @Resource 可根据类型或名称注入
        - 如果同时指定了name和type，则从Spring上下文中找到唯一匹配的bean进行装配，找不到则抛出异常
        - 如果指定了name，则从Spring上下文中查找名称(id)匹配的bean进行装配，找不到则抛出异常
        - 如果指定了type，则从Spring上下文中找到类型匹配的唯一bean进行装配，找不到或找到多个，都抛出异常
        - 如果既没指定name，也没指定type,则自动按照byName方式进行装配。如果没有匹配，则回退为一个原始类型进行匹配，如果匹配则自动装配。

6. @PropertySource + @Value
    - @PropertySource 注解标注在类上，用于指定配置文件的位置，结合@Value进行读取数据
    - 例如：@PropertySource("classpath:/init.properties")
    - @Value 用于标注在类成员变量上，被标注的属性可从配置文件中获取对应数据
    - 例如：@Value("${user.id}") 表示从init.properties文件中获取key为user.id的值

7. @ImportResource
    - 导入对应的配置文件
```

## 2. 高级注解
```markdown
1. @Configuration
    - @Configuration 注解用于标注在类上，表示该类是一个配置类，本质也是@Component的衍生注解
    - 被标注的类可看作成spring的applicationContext.xml文件
    - 其类内部可通过其它注解对applicationContext.xml 进行替换，@Configuration可看作applicationContext.xml的代替
    - 在使用@Configuration替换applicationContext.xml后对应工厂的创建方式也发生改变
    - 替换之前 ApplicationContext ctx = new ClassPathXmlApplicationContext(configLocation:"/applicationContext.xml");
    - 替换之后 ApplicationContext ctx = new AnnotationConfigApplicationContext(AppConfig.class);
    - ApplicationContext ctx = new AnnotationConfigApplicationContext("com.xx.xxx"); 可通过包扫描创建
    - Spring在配置Bean中加入了@Configuration注解后，底层就会通过Cglib的代理方式，来进行对象相关的配置、处理

2. @Bean
    - @Bean注解在配置bean中进行使用，等同于xml配置文件中的<bean>标签
    - 与@Component不同的是@bean只能标注在方法上
    - 使用@Bean注解的前提是类中必须标注@Configuration
    - @Bean标注的方法其修饰符必须是public
        - 返回值则必须为一个指定的类型
        - 方法名默认则等同于<bean>中的id值，如需更改id值则注解改成@Bean("自定义id值")即可
        - 方法体则由自己实现创建对象
    - 如需控制对象的创建次数则可结合@Scope注解使用，默认是单实例

- @Bean自定义类型注入
@Bean
public UserDAO userDAO() {
    return new UserDAOImpl();
}
@Bean
public UserService userService(UserDAO userDAO) {
    // 通过形参注入
    UserServiceImpl userService = new UserServiceImpl();
    userService.setUserDAO(userDAO);
    return userService;
}

@Bean
public UserService userService() {
    // 调用方法形式注入
    UserServiceImpl userService = new UserServiceImpl();
    userService.setUserDAO(userDAO());
    return userService;
}

- @Bean JDK类型注入
@Bean("u")
public User user1() {
    User user = new User();
    user.setId(10);
    user.setUsername("xxh");
    user.setPassword("123");
    return user;
}


3. @ComponentScan
    - @ComponentScan 等同于配置文件中的<context:component-scan>标签
    - 用于扫描相关注解(@Component、@Autowired、@Value等)
    - @ComponentScan(basePackages="com.xxx.scan") 表示扫描某个包以及子包下的相关注解

    - <context:component-scan> 标签排除方式
        - context:exclude-filter 表示排除
        - type: assignable 排除特定的类型，不进行扫描
                annotation 排除特定的注解，不进行扫描
                aspectj 切入点表达式(org.example.annotation.entity..*)，对表达式内的包或类不进行扫描，只支持包或类
                regex 正则表达式 对匹配正则的不进行扫描
                custom 自定义排除策略

        - 多种策略可叠加使用
            - <context:exclude-filter type="annotation" expression="org.springframework.stereotype.Service"/>
            - <context:exclude-filter type="aspectj" expression="org.example.annotation.entity..*"/>
        - Java语法
        @ComponentScan(
            basePackages = "org.example.annotation",
            excludeFilters = {
                    @ComponentScan.Filter(type = FilterType.ANNOTATION, value = Service.class)
            }
        )

    - <context:component-scan> 标签包含方式
        - 语法与排除方式一致
        - 需设置 use-default-filters="false" 关闭默认扫描策略
        - <context:include-filter  type="annotation" expression="org.springframework.stereotype.Service"/>
        - Java语法
        @ComponentScan(
            basePackages = "org.example.annotation",
            useDefaultFilters = false,
            includeFilters = {
                    @ComponentScan.Filter(type = FilterType.ANNOTATION, value = Service.class)
            }
        )

```


## 3. 纯注解版AOP开发
### 3.1 开发步骤
 1. 原始对象
 2. 创建切面类(额外功能、切入点、组装切面)
 3. 开启切面自动代理

### 3.2 代码实现
```java
/**
 * 纯注解开发AOP
 * Created by Ale on 2022/4/1
 */
@Configuration
@ComponentScan(basePackages = "org.example.annotation.aop")
@EnableAspectJAutoProxy
public class AppConfig {
}


/**
 * 切面类
 * Created by Ale on 2022/4/1
 */
@Aspect
@Component
public class MyAspect {

    /**
     * 切入点
     */
    @Pointcut("execution(* org.example.annotation.aop..*.*(..))")
    public void pointcut() {
    }

    /**
     * 额外功能
     * @param joinPoint
     * @return
     */
    @Around("pointcut()")
    public Object around(ProceedingJoinPoint joinPoint) {
        Object ret = null;
        try {
            System.out.println("---- log ----");
            ret = joinPoint.proceed();
        } catch (Throwable throwable) {
            throwable.printStackTrace();
        }
        return ret;
    }
}
```

### 3.3 纯注解AOP细节分析
```markdown
1. 代理方式的切换 JDK | Cglib
<aop:aspectj-autoproxy proxy-target-class=true|false/> 默认false JDK
@EnableAspectJAutoProxy(proxyTargetClass = true) 表示开启AOP自动代理并切换为Cglib方式 默认false为JDK

2. 在SpringBoot开发中@EnableAspectJAutoProxy 无需在定义了，SpringBoot已经集成并且默认代理方式为Cglib
```