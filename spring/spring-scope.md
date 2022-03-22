# Scope实例控制

## 1. singleton
```markdown
1. 单例(默认) 表示无论创建多少次只会存在一个实例
2. 栗子
<!-- Scope实例控制 -->
<bean id="account" scope="singleton" class="org.example.spring.scope.Account">
    <property name="id" value="10"/>
    <property name="name" value="san"/>
</bean>

ApplicationContext context = new ClassPathXmlApplicationContext("/applicationContext.xml");
Account account1 = context.getBean("account", Account.class);

Account account2 = context.getBean("account", Account.class);
System.out.println("account1 = " + account1);
System.out.println("account2 = " + account2);

输出结果
account1 = org.example.spring.scope.Account@48fa0f47
account2 = org.example.spring.scope.Account@48fa0f47
```

## 2. prototype
```markdown
1. 原型 每个实例都是独一无二的
2. 还是上面的栗子
结果输出
account1 = org.example.spring.scope.Account@48fa0f47
account2 = org.example.spring.scope.Account@6ac13091
```

## 3. singleton 与 prototype的适用场景
### 3.1 singleton
```markdown
1. SqlSessionFactory
2. DAO
3. Service
```
### 3.2 prototype
```markdown
1. Connection
2. SqlSession | Session
3. Struts2 Action
```