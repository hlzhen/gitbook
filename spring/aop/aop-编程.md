# AOP编程
## 1. 概念
```markwodn
AOP(Aspect Oriented Programing)     面向切面编程
以切面为基本单位的程序开发，通过切面见的协同，相互调用完成程序的构建
面向切面编程其本质就是Spring的动态代理开发，通过代理类为原始类增加额外功能，利于原始类的维护

什么叫切面呢？
切面 = 切入点 + 额外功能

OOP(Object oriented Programing)     面向对象编程
以对象为基本单位的程序开发，通过对象间的协同，相互调用完成程序的构建

POP(Producer oriented Programing)   面向过程(方法，函数)编程
以过程为基本单位的程序开发，通过过程间的协同，相互调用完成程序的构建
```

## 2. AOP编程的开发步骤
```markdown
1. 准备原始对象
2. 额外功能(需实现MethodInterceptor)
3. 切入点
4. 组装切面(额外功能+切入点)
```

## 3. AOP底层实现原理
### 3.1 JDk的动态代理
- Proxy.newProxyInstance方法详解
![JDK动态代理分析](/assets/spring/spring_jdk_proxy.png)
![ClassLoader](/assets/spring/spring_jdk_proxy_cl.png)
- JDK代理的创建

```java
/**
 * JDK创建动态代理
 * Created by Ale on 2022/3/26
 */
public class TestJDKProxy {

    public static void main(String[] args) {
        /**
         * 1. 创建原始对象
         */
        UserService userService = new UserServiceImpl();


        /**
         * 2. 额外功能
         * 注意：注意如果在JDK1.8之前内部类访问外部成员需要外部成员加上 final 修饰
         * final UserService userService = new UserServiceImpl();
         */
        InvocationHandler handler = new InvocationHandler() {
            @Override
            public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
                System.out.println("----- before ------");
                Object obj = method.invoke(userService, args);
                System.out.println("----- after ------");
                return obj;
            }
        };


        /**
         * 2. 创建动态代理(整合)
         * 注意： 类加载器可借用其它类，不一定非要本类，不过推荐本类中的类加载器
         */
        UserService userServiceProxy = (UserService) Proxy.newProxyInstance(
                TestJDKProxy.class.getClassLoader(),
                userService.getClass().getInterfaces(),
                handler
        );

        userServiceProxy.login("xxh", "123456");
    }
}
```
### 3.2 Cglib的动态代理
- Cglib与JDK的区别
  ```markdown
  Cglib创建动态代理的原理：父子继承关系创建代理类，原始类作为父类，代理类作为子类，这样既可以保证二者方法一致，
  同时在代理类中提供新的实现(增加额外功能)
  ```
  ![Cglib与JDK的区别](/assets/spring/spring_cglib_proxy.png)
- Cglib编码实现

```java
/**
 * Created by Ale on 2022/3/27
 */
public class TestCglibProxy {
    public static void main(String[] args) {
        /**
         * 1. 创建原始对象
         */
        UserService userService = new UserService();


        /**
         * 2. 通过Cglib方式创建动态代理对象
         *    JDK方式创建
         *      Proxy.newProxyInstance(classloader,interface,invocationhandler)
         *    Cglib方式创建
         *      Enhancer.setClassLoader()
         *      Enhancer.setSuperClass()
         *      Enhancer.setCallback()   ---> cglib包中的 MethodInterceptor()
         */
        Enhancer enhancer = new Enhancer();
        enhancer.setClassLoader(TestCglibProxy.class.getClassLoader());
        enhancer.setSuperclass(userService.getClass());

        MethodInterceptor interceptor = new MethodInterceptor() {
            @Override
            public Object intercept(Object o, Method method, Object[] args, MethodProxy methodProxy) throws Throwable {
                System.out.println("--- before ---");
                Object ret = method.invoke(userService, args);
                System.out.println("--- after ---");
                return ret;
            }
        };

        enhancer.setCallback(interceptor);

        UserService userServiceProxy = (UserService) enhancer.create();
        userServiceProxy.login("xxh", "123456");
    }
}
```

## 4. 回看BeanPostProcessor
- 核心问题，为什么通过ID获取的对象实例是代理对象？
  ![beanPostProcessor](/assets/spring/spring_bean_post_processor_1.png)
- 编码实现

```java
/**
 * Created by Ale on 2022/3/27
 */
public class ProxyBeanPostProcessor implements BeanPostProcessor {


    @Override
    public Object postProcessBeforeInitialization(Object bean, String beanName) throws BeansException {
        return bean;
    }

    @Override
    public Object postProcessAfterInitialization(Object bean, String beanName) throws BeansException {
        InvocationHandler handler = new InvocationHandler() {
            @Override
            public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
                System.out.println("---- before ----");
                Object ret = method.invoke(bean, args);
                System.out.println("---- after ----");
                return ret;
            }
        };
        return Proxy.newProxyInstance(ProxyBeanPostProcessor.class.getClassLoader(), bean.getClass().getInterfaces(), handler);
    }
}
```
```xml
<bean id="userService" class="org.example.spring.postprocessor.UserServiceImpl"/>

<!--
    1.实现BeanPostProcessor进行加工
    2.配置文件中对BeanPostProcessor进行配置
 -->
<bean id="proxyBeanPostProcessor" class="org.example.spring.postprocessor.ProxyBeanPostProcessor"/>
```

## 5. JDK与Cglib创建方式总结
```markdown
1. JDK动态代理 Proxy.newProxyInstance(classloader,interface,invocationhandler) 通过接口创建代理的实现类
2. Cglib动态代理 Enhancer.create() 通过继承父类创建代理类
```

