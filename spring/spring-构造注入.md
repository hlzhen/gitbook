# Spring构造注入

## 1. 回顾注入
```markdown
1. 什么是注入？
    - 通过Spring的配置文件，为成员变量赋值
2. 什么是Set注入？
    - Spring调用Set方法，通过配置文件为成员变量赋值
```

## 2. 构造注入
```xml
1. Spring调用构造方法通过配置文件为成员变量赋值

<!-- 
    1. 需提供构造方法，顺序必须保持一致
    2. 一个constructor-arg标签表示一个参数
    3. constructor-arg 属性:
        type:表示数据类型
        name:表示成员变量名称
 -->
<bean id="customer" class="org.example.spring.Customer">
    <constructor-arg name="id" value="10"/>
    <constructor-arg name="name" value="xxs"/>
</bean>
```

## 3. Spring注入总结
![注入总结](/assets/spring/spring_inject.png)
