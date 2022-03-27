# AOP基于注解开发
## 编码实现
```markdown
1. 基本套路 原始对象 -> 额外功能 -> 切入点 -> 组装切面
```
```java
/**
 * @Aspect 注解表示当前为切面类
 * Created by Ale on 2022/3/27
 */
@Aspect
public class MyAspect {

    /*
            1.额外功能(纯代码版本)
                public class MyAround implements MethodInterceptor {
                    public Object invoke(MethodInvocation invocation){
                        Object ret invocation.proceed();
                        return ret;
                    }
                }
            2.切入点
            <aop:config
                <aop:pointcut id=""expression="execution(*login(..))"/>
     */

    /**
     * 通过 @Around("execution(* login(..))") 定义切入点
     * @param joinPoint
     * @return
     */
    @Around("execution(* login(..))")
    public Object around(ProceedingJoinPoint joinPoint) {
        Object ret = null;
        try {
            ret = joinPoint.proceed();
        } catch (Throwable throwable) {
            throwable.printStackTrace();
        }
        return ret;
    }
}
```
```xml
<bean id="userService" class="org.example.spring.aspect.UserServiceImpl"/>

<!--
    1.额外红能
    2.切入点
    3.组装
-->
<bean id="around" class="org.example.spring.aspect.MyAspect"/>

<!-- 告知Spring基于注解进行AOP编程 -->
<aop:aspectj-autoproxy/>
```

## 2. 切入点的复用
```java
/**
 * Created by Ale on 2022/3/27
 */
@Aspect
public class MyAspect {

    /**
     * 切入点复用
     */
    @Pointcut("execution(* login(..))")
    public void myPointcut(){}

    /*
            1.额外功能(纯代码版本)
                public class MyAround implements MethodInterceptor {
                    public Object invoke(MethodInvocation invocation){
                        Object ret invocation.proceed();
                        return ret;
                    }
                }
            2.切入点
            <aop:config
                <aop:pointcut id=""expression="execution(*login(..))"/>
     */

    /**
     * 注解版本
     * 通过 @Around("execution(* login(..))") 定义切入点
     * @param joinPoint
     * @return
     */
    @Around("myPointcut()")
    public Object around(ProceedingJoinPoint joinPoint) {
        Object ret = null;
        try {
            System.out.println("--- before ---");
            ret = joinPoint.proceed();
        } catch (Throwable throwable) {
            throwable.printStackTrace();
        }
        return ret;
    }
}
```

## 3. 动态代理的切换方式
```xml
<!-- 
    告知Spring基于注解进行AOP编程 
    proxy-target-class false(默认) 表示jdk代理 true(表示开启Cglib代理)
-->
<aop:aspectj-autoproxy proxy-target-class="false"/>
```

## 4. ApplicationContextAware接口
```markdwon
1. ApplicationContextAware是什么？
    - 用于获取Spring工厂对象，由于Spring工厂是重量级资源，不应该随处创建，
    所以可通过实现ApplicationContextAware接口中的setApplicationContext方法来获取工厂对象

2. 通过ApplicationContextAware接口中的setApplicationContext方法获取的工厂对象有什么用？
    - 例如当业务需求越来越复杂，服务内部有可能会相互调用，那么此时如果配置了Aop相关的信息，
    那么就需要谨慎使用this调用服务内部方法，因为this指向的是原始类，而不是代理类。
    如果使用的是this调用则代理类相关的额外功能添加不了(根据业务需求进行处理到底是this还是通过Spring工厂获取代理对象调用)
```