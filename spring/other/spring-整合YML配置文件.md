# Spring整合YML配置文件

## 1. 什么是YML
```markdown
1. YMl(YAML)是一种新形式的配置文件，比XML更简单，比properties更强大
```

## 2. properties配置存在的问题
```markdown
1. properties表达过于复杂，无法表达数据的内在联系
2. properties无法表达对象 集合类型
3. YML与properties配置区别如下图
```
![YML与properties配置区别](/assets/spring/spring_yml.png)

## 3. YML语法简介
### 3.1 如何创建YML文件
```markdown
1. 如何创建YML文件
    - xxx.yml就代表是一个YML文件 application.yml
```

### 3.2 语法
```yml
#基本语法 k-v
name: xxh
password: 99909

#对象属性
product:
    id: 1
    name: 长袖
    type: x

#集合类型
list:
    - 1111
    - 3111
```

## 4. Spring集成YML集成思路的分析
```markdown
1. 准备YML文件 
   init.yml
   name: xxh
   password: 123

2. 读取yml转换成properties
    YMLpropertiesFactoryBean.setResource("yml配置文件路径")
    YMLpropertiesFactoryBean.getObject() --- > Propterties

3. 应用PropertySourcesPlaceholderConfigurer
    PropertySourcesPlaceholderConfigurer.setProperties();

4. 在类中使用@Value注解进行属性注入
```

## 5. 编码整合
### 5.1 环境搭建
```xml
<!--
    由于spring是不支持YML文件的解析与处理，所以需要引入特定的依赖
    snakeyaml 最低版本不能低于1.8
-->
<dependency>
    <groupId>org.yaml</groupId>
    <artifactId>snakeyaml</artifactId>
    <version>1.23</version> 
</dependency>
```

### 5.2 编码
```java
/**
 * Spring整合YML
 * Created by Ale on 2022/4/2
 */
@Configuration
@ComponentScan("org.example.annotation.yml")
public class YmlAutoConfiguration {

    @Bean
    public PropertySourcesPlaceholderConfigurer placeholderConfigurer(){
        YamlPropertiesFactoryBean yamlPropertiesFactoryBean = new YamlPropertiesFactoryBean();
        yamlPropertiesFactoryBean.setResources(new ClassPathResource("init.yml"));
        Properties properties = yamlPropertiesFactoryBean.getObject();
        PropertySourcesPlaceholderConfigurer placeholderConfigurer = new PropertySourcesPlaceholderConfigurer();
        placeholderConfigurer.setProperties(properties);
        return placeholderConfigurer;
    }
}
```

### 5.3 问题点
```markdown
1. yml集合问题的处理
    list:
        - 1111
        - 2222
  - 需要结合SpringEL表达式进行处理，需要把list改成 list: 111,222 在进行分割处理
  - @Value("#{'${list}'.split(',')}")

2. 对象类型YML配置问题
    - @Value("${account.name}") 如属性比较多的话需要重复书写account 这就造成一定的冗余目前Spring是没法解决的，只能这么写

针对上述两个问题，在SpringBoot的@ConfigurationProperties注解中都得到了解决
```
