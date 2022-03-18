# Set注入详解

## 1. 注入的类型
```markdown
针对不同类型的成员变量，在<property>标签当中需要嵌套其它标签。
```
![配置类型](/assets/spring/spring_set_classtype.png)

## 2. JDK内置类型的注入方式
### 2.1 String + 8种基本数据类型
```markdown
直接在<property>标签中使用<value>xxx</value>即可
```
### 2.2 数组
```xml
1. property中嵌套list，list中继续嵌套value 每个value就相当于一个数组的元素
<property name="emails">
    <list>
        <value>xxxx@163.com</value>
        <value>xxxxn@163.com</value>
    </list>
</property>
```
### 2.3 Set集合
```xml
1. 如value值重复的话根据set集合的特性，那么最终会把重复的值给去除掉
<property name="tels">
    <set>
        <value>1111</value>
        <value>1111</value>
        <ref bean=""></ref>
    </set>
</property>
```
### 2.4 List集合
```xml
1. list集合与数组使用的注入方式一致，因为list底层也是数组
<property name="emails">
    <list>
        <value>xxxx@163.com</value>
        <value>xxxxn@163.com</value>
    </list>
</property>
```

### 2.5 Map集合
```xml
<property name="qqs">
    <map>
        <entry>
            <key>
                <value>ssss</value>
            </key>
            <value>1111</value>
        </entry>
    </map>
</property>
```

### 2.6 Properties
```xml
Properties类型是一个特殊的map, key的类型是String value的类型也是String
<property name="properties">
    <props>
        <prop key="key1">value1</prop>
        <prop key="key2">value2</prop>
    </props>
</property>
```

### 2.7 复杂的JDK类型(Date)
```markdown
需要自定义的类型转换器进行处理
```

## 3. 自定义类型注入
### 3.1 方式一
```xml
1. 为成员变量提供set get方法
2. 配置文件中进行注入
<bean id="userService" class="xxxx.xx.UserServiceImpl">
    <property name="userDao">
        <bean class="xxx.UserDAOImpl"/>
    </property>
</bean>
```
### 3.2 方式二
```markdown
1. 第一种赋值方式存在的问题
    - 配置文件代码冗余，如多个service需要配置多个userDao
```
![自定义类型注入](/assets/spring/spring_set_diyclasstype.png)

## 4. 基于命名空间注入
```xml
<bean id="user" class="org.example.spring.User"/>
<bean id="person5" class="org.example.spring.Person" p:name="xxs" p:age="18" p:user-ref="user"/>
```