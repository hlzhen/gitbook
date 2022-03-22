# Bean的生命周期

## 1. 什么是Bean的生命周期
```markdown
指的是一个对象创建、存活、消亡的一个完整过程
```

## 2. 生命周期的三个阶段
### 2.1 创建阶段
```markdown
1. 当scope为singleton时，只要工厂创建了，对应的Bean也会创建，随工厂的创建而创建
2. 当scope为prototype时则只有当对应的Bean被获取时才会创建懒加载的策略
3. 如果需要当scope为singleton时Bean的创建为懒加载模式，则在Bean配置中添加lazy-init="true"即可
```

### 2.2 初始化阶段
```markdown
1. Spring工厂创建完对象后，调用对象的初始化方法完成对应的初始化操作
2. 初始化方法的提供：根据业务的需求编写初始化方法，最终完成初始化操作
3. 初始化的操作交由Spring工厂进行调用  

# 实现步骤
1. 方式一：实现 InitializingBean接口并实现afterPropertiesSet()方法
2. 方式二：提供一个普通方法myInit()然后配置 <bean id="product" class="xxx.xx.Product" init-method="myInit"/>

# 初始化细节分析
1. 如上两种方式同时存在时，那么优先会执行afterPropertiesSet()其次才是自定义的初始化方法。
2. 注入一定是发生在初始化操作之前。
```

### 2.3 销毁阶段
```markdown
Spring销毁对象前，会调用对象的销毁方法，完成销毁
1. Spring什么时候销毁所创建的对象？
    context(工厂对象).close();
2. 销毁方法是程序猿根据自己的需求去定义，完成销毁操作，交由Spring调用
 
 # 实现步骤
 1. 实现 DisposableBean 接口并实现destroy()方法
 2. 自定义myDestroy()方法，在配置文件中配置
 <bean id="product" class="xxx.xx.Product" init-method="myInit" destroy-method="myDestroy"/>

 # 细节分析
1. 销毁方式的操作只适用于scope="singleton"
2. 主要是对资源的释放，例如io,数据库连接
```

## 3. 总结
![生命周期总结](/assets/spring/spring_bean_life.png)
