# 自定义类型转换器

## 1. 类型转换器
```markdown
1. 类型转换
    - 顾名思义类型转换就是把一个类型转换成另一个类型
    - 而类型转换器则是这个转换过程的一种封装
2. 作用
    - Spring通过类型转换器把配置文件中字符串数据转换成了对象中成员变量对应的类型，进而完成了注入
```
![类型转换流程](/assets/spring/spring_type_converter.png)

## 2. 自定义类型转换器
1. 实现Converter<S,T>接口 
```java
/**
 * 自定义的类型转换器
 * Created by Ale on 2022/3/22
 */
public class MyDateConverter implements Converter<String, Date> {

    private String pattern;

    public void setPattern(String pattern) {
        this.pattern = pattern;
    }

    @Override
    public Date convert(String source) {
        Date date = null;
        try {
            SimpleDateFormat sdf = new SimpleDateFormat(pattern);
            date = sdf.parse(source);
        } catch (ParseException e) {
            e.printStackTrace();
        }
        return date;
    }
}
```

2. 在Spring配置文件中进行配置
```xml
    <!-- 
        配置自定义的类型转换器Bean
        Spring内置已定义了日期的类型转换，只不过格式是有要求的例如2022/11/10,
        在某些业务场景中可能需要自己定义格式所以内置的并不能满足一些特定需求
    -->
    <bean id="myDateConverter" class="xxx.MyDateConverter">
        <property name="pattern" value="yyyy-MM-dd"/>
    </bean>

    <!-- 
        类型转换器注册 
            目的：告知Spring框架，所创建的MyDateConverter是一个类型转换器 
            ConversionServiceFactoryBean用于注册类型转换器 
            注意: id名称一定是conversionService，不然会出现换转换器失效
    -->
    <bean id="conversionService" class="org.springframework.context.support.ConversionServiceFactoryBean">
        <property name="converters">
            <set>
                <ref bean="myDateConverter"/>
            </set>
        </property>
    </bean>
```