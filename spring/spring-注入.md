# 注入(Injection)
## 1. 什么是注入
```markdown
通过Spring工厂及配置文件，为所创建对象的成员变量赋值
```
### 1.1. 为什么需要注入
```java
/**
 * 原因: 通过编码的方式为成员变量赋值存在耦合，如下代码
 * 测试注入
 */
@Test
public void testInjection() {
    ApplicationContext context = new ClassPathXmlApplicationContext("/applicationContext.xml");
    Person person = (Person) context.getBean("person");
    person.setName("Ale");
    person.setAge(18);    
}
```

### 1.2. 通过配置文件注入
```markdown
1. 类的成员变量提供get set方法
2. 配置Spring的配置文件 property -> name:代表当前配置类的成员变量名称 value:代表需要赋值的数据
<bean id="person" class="org.example.spring.Person">
    <property name="age">
        <value>10</value>
    </property>
    <property name="name">
        <value>Dream</value>
    </property>
</bean>

3. 缩写版
<bean id="person" class="org.example.spring.Person">
    <property name="age" value="10"/>
    <property name="name" value="Dream"/>
</bean>

4. 好处以及适用场景
    - 好处: 解耦合
    - 适用场景: 例如数据库的配置以及各中间件的配置
```

## 2. Spring注入的原理分析(应用级简易版分析)
```markdown
Spring底层会通过配置文件的配置信息调用对应类的set方法来完成成员变量的赋值，这种方式也称之为set注入
```