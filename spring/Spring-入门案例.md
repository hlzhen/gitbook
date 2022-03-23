# Spring入门案例


## 1. 软件版本
```markdown
1. JDK1.8+
2. Maven3.5+
3. springframework5.1.6
4. spring 官方地址：https://www.spring.io
5. spring 官方jar包下载地址：https://repo.spring.io/ui/native/release/org/springframework/spring
6. Spring maven版本仓地址：https://mvnrepository.com/artifact/org.springframework/spring-context
```

## 2. 环境搭建
### 2.1 Maven环境
```xml
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-context</artifactId>
    <version>5.2.6.RELEASE</version>
</dependency>
```
### 2.2 Spring的配置文件
```markdown
1. 配置文件的放置位置
    - 位置任意，没有硬性要求
2. 配置文件的命名
    - 没有硬性要求，建议命名 applicationContext.xml
注意：虽然spring的配置文件没有硬性要求，但在使用过程中需要进行配置文件路径的设置
```
### 2.3 IDEA中Spring配置文件的创建方式
![IDEA中spring配置文件的创建方式](/assets/spring/springconfig_createforidea.png)

### 2.4 Java工程相关Jar依赖
```markdown
commons-logging-1.1.1.jar 
spring-beans-5.2.6.RELEASE.jar 
spring-context-5.2.6.RELEASE.jar 
spring-core-5.2.6.RELEASE.jar 
spring-expression-5.2.6.RELEASE.jar

注意：commons-logging是额外依赖的日志包，需自行下载，下载地址：https://mvnrepository.com/
```

## 3. Spring核心API
```markdown
1. ApplicationContext(工厂类，接口类型)
    - 作用: 用于对象的创建
    - 好处: 解耦合，线程安全
2. 接口的好处: 屏蔽实现的差异化
3. ApplicationContext接口的实现
    - 非web环境: ClassPathXmlApplicationContext
    - web环境: XmlWebApplicationContext

4. ApplicationContext 是重量级资源，其对象会占用大量内存资源。正因是重量级资源，不会频繁的创建对象，一个应用中只会创建一个工厂对象
```
![Spring核心Api](/assets/spring/spring_appliction_api.png)

## 4. Spring Hello World编写

### 4.1 配置bean
```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">

    <!-- id为当前bean对象唯一标识，class表示需要创建对象的类路径 Person为需要交给Spring管理的自定义对象-->
    <bean id="person" class="org.example.spring.Person"/>
</beans>
```
### 4.2 获取bean
```java
// 1. 获取Spring的工厂
ApplicationContext context = new ClassPathXmlApplicationContext("/applicationContext.xml");

// 2. 通过工厂类获取对象
Person person = (Person) context.getBean("person");
```

## 5. 工厂类的常用方法细节点
```markdown
1. getBean的重载方法说名
    - context.getBean(String name): 通过配置文件中的bean的ID值获取对应的bean(需要进行强制转换)
        Person person = (Person) context.getBean("person");

    - getBean(String name, Class<T> requiredType): 通过配置文件中的bean的ID值获取对应的bean,给定类型后无需进行强制转换
        Person person = context.getBean("person", Person.class);

    - getBean(Class<T> requiredType): 通过配置文件中的bean的Clss值对应的类类型获取对应的bean(配置文件中只能配置一个)
        Person person = context.getBean(Person.class);

2. getBeanDefinitionNames()获取Bean的定义集合，也就是获取配置文件中所有bean标签的ID名称
    - String[] names = context.getBeanDefinitionNames();
    - Arrays.stream(names).forEach(s -> System.out.println(s));
    - 输出 person person1

3. getBeanNamesForType(@Nullable Class<?> type):返回给定类型的所有bean名称
    -  String[] namesForType = context.getBeanNamesForType(Person.class);
    -  Arrays.stream(namesForType).forEach(id -> System.out.println(id));
    - 输出 person person1

4. containsBeanDefinition(String beanName): 是否包含给定名称id的bean定义
    - boolean containsBeanDefinition = context.containsBeanDefinition("abc");
    - 用于判断配置文件中是否包含某个bean，根据bean的ID进行判断，不能判断name属性

5. containsBean(String name): 与containsBeanDefinition相似但是有着明显的区别
    - 如果找到了与给定名称匹配的bean定义或单例，则无论命名的bean定义是具体的还是抽象的、惰性的还是即时的、
    - 是否在作用域中，该方法都将返回true。
    - 注意: 相对于containsBeanDefinition 的话，containsBean既能判断ID也能判断name
    -  boolean containsBean = context.containsBean("abc");
```

## 6. 配置文件细节点
```markdown
1. bean的配置
    - 可以不配置ID，Spring默认会生成一个ID，可通过getBeanDefinitionNames()获取查看
    - 建议还是定义一个ID，这样就省去了Spring的生成
    - 应用场景：如果这个bean只需要使用一次就可省略ID值

2. bean的属性说明
    - id: 当前bean的唯一标识
    - name: 当前bean的名称(别名)
    - class: 当前bean所指向的类(全类名)

3. bean的id与name的差异
    - 相同：都可以通过id或者name获取当前bean
    - 区别：
        1. name可以定义多个，如name="p1,p2,p3", 而id值只能有一个并且是唯一的
        2. (XML现如今以没有这个要求了)XML的id属性的值，命名要求：必须以字母开头，字母 数字 下划 线连字符, 不能以特殊字符开头 /person
        反之name属性的命名是没有要求的
```

## 7. Spring简易版工厂原理
![spring简易版工厂原理](/assets/spring/spring_simplefactory.png)

## 8. 其它
### 8.1 Spring整合Log4j
```xml
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
```
```properties
#resources文件夹根目录下创建log4j.properties文件
#配置根
log4j.rootLogger=debug,console
#日志输出到控制台显示
log4j.appender.console=org.apache.log4j.ConsoleAppender
log4j.appender.console.Target=System.out
log4j.appender.console.layout=org.apache.log4j.PatternLayout
log4j.appender.console.layout.ConversionPattern=%d{yyyy-MM-dd HH:mm:ss} %-5p %c{1}:%L - %m%n
```