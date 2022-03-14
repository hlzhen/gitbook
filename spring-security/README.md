# Spring-Security简介

> [!TIP|style:callout|label:安全框架概述 |iconVisibility:hidden]
 什么是安全框架？
> 解决系统安全问题的框架。如果没有安全框架，我们需要手动处理每个资源的访问控制，非 常麻烦。使用安全框架，我们可以通过配置的方式实现对资源的访问限制。

<br/>


> [!TIP|style:callout|label:常用安全框架 |iconVisibility:hidden]
 Spring-Security:Spring家族一员。是一个能够为基于Spring的企业应用系统提供声明式的安全访问控制解决 方案的安全框架。它提供了一组可以在Spring应用上下文中配置的Bean,充分利用了Spring Ioc,DI(控制反转Inversion of Control,DI:Dependency Injecion依赖注入)和AoP(面向切面编程)功能，为应用系 统提供声明式的安全访问控制功能，减少了为企业系统安全控制编写大量重复代码的工作。 
> Apache-Shiro:一个功能强大且易于使用的ava安全框架，提供了认证，授权，加密，和会话管理。

<br/>

> [!TIP|style:callout|label:Spring-Security概述 |iconVisibility:hidden]
> Spring Security是一个高度自定义的安全框架。利用Spring loC/DI和AOP功能，为系统提供了声明式安全访问 控制功能，减少了为系统安全而编写大量重复代码的工作。使用Spring Secruity的原因有很多，但大部分都是发现了javaEE的Servlet规范或EJB规范中的安全功能缺乏典型企业应用场景。同时认识到他们在WAR或EAR级别无法移植。因此如果你更换服务器环境，还有大量工作去重新配置你的应用程序。使用Spring-Security解决了这 些问题，也为你提供许多其他有用的、可定制的安全功能。正如你可能知道的两个应用程序的两个主要区域是 "认证" 和 "授权"（或者访问控制）。这两点也是Spring-Security重要核心功能。"认证"，是建立一个他声明的主体的 过程（一个"主体"一般是指用户，设备或一些可以在你的应用程序中执行动作的其他系统），通俗点说就是系统认 为用户是否能登录。"授权"指确定一个主体是否允许在你的应用程序执行一个动作的过程。通俗点讲就是系统判断 用户是否有权限去做某些事情。


