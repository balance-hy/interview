# 框架篇

## spring

### Spring框架中的单例bean是线程安全的吗？

![image-20240425141857351](https://raw.githubusercontent.com/balance-hy/typora/master/thinkbook/image-20240425141857351.png)

不是线程安全的

Spring框架中有一个@Scope注解，默认的值就是singleton，单例的。

因为**一般在spring的bean的中都是注入无状态的对象，没有线程安全问题**，

**如果在bean中定义了可修改的成员变量，是要考虑线程安全问题的，可以使用多例或者加锁来解决**

****

### spring中事务如何实现

Spring支持**编程式事务管理和声明式事务管理**两种方式。

* 编程式事务控制：需使用TransactionTemplate来进行实现，对业务代码有侵入性，项目中很少使用
* 声明式事务管理：声明式事务管理建立在AOP之上的。其本质是通过AOP功能，对方法前后进行拦截，将事务处理的功能编织到拦截的方法中，**也就是在目标方法开始之前加入一个事务，在执行完目标方法之后根据执行情况提交或者回滚事务。**

### spring中事务失效场景

#### 异常捕获处理

事务通知只有捉到了目标抛出的异常，才能进行后续的回滚处理，如果**目标自己处理掉异常，事务通知无法知悉**

解决方法：自己在 catch 块中手动 throw new 异常

![image-20240425145341398](https://raw.githubusercontent.com/balance-hy/typora/master/thinkbook/image-20240425145341398.png)

#### 抛出检查异常

Spring默认只会回滚非检查异常，即运行时异常

解决方法，在注解的值中添加rollbackfor属性设置为 Exception.class

![image-20240425150336410](https://raw.githubusercontent.com/balance-hy/typora/master/thinkbook/image-20240425150336410.png)

检查异常时编译阶段由编译器强制检查的异常

#### 非public方法

Spring为方法创建代理、添加事务通知、前提条件都是该方法是public的，不是的话就会造成事务失效

解决方法，改为public方法

![image-20240425150900241](https://raw.githubusercontent.com/balance-hy/typora/master/thinkbook/image-20240425150900241.png)

****

### bean的生命周期

![image-20240425151222210](https://raw.githubusercontent.com/balance-hy/typora/master/thinkbook/image-20240425151222210.png)

![image-20240425151611059](https://raw.githubusercontent.com/balance-hy/typora/master/thinkbook/image-20240425151611059.png)

* 通过BeanDefinition获取bean的定义信息
* 调用构造函数实例化bean
* bean的依赖注入
* 处理Aware接口(BeanNameAware、BeanFactoryAware、ApplicationContextAware)
* Bean的前置处理器BeanPostProcessor
* 初始化方法(InitializingBean、init-method)
* Bean的后置处理器BeanPostProcessor
* 使用
* 销毁bean

**Aware接口允许Bean感知到其所处的环境或容器，并与之进行交互。**

以下是几种常见的Aware接口及其作用：

1. **BeanNameAware**：
   - 实现BeanNameAware接口的Bean可以获得自己在容器中的名字。
   - 通过实现该接口，Bean可以获取到自己在Spring容器中注册的Bean名称。
2. **ApplicationContextAware**：
   - 实现ApplicationContextAware接口的Bean可以获得对ApplicationContext的引用。
   - 通过实现该接口，Bean可以获取到Spring容器的ApplicationContext，从而可以访问容器中的其他Bean、资源等。

****

### bean的循环引用（依赖）

**在创建A对象的同时需要使用的B对象，在创建B对象的同时需要使用到A对象**

![image-20240425153931109](https://raw.githubusercontent.com/balance-hy/typora/master/thinkbook/image-20240425153931109.png)

#### 依赖注入

二级缓存解决一般的循环依赖

**代理对象不能使用二级缓存的原因,违背了Spring对代理对象的设计原则,bean对象只有初始化后才可以创建代理对象,使用二级缓存的话直接就要为对象创建代理对象,会创建多个代理对象显然不合理**

![image-20240425154029115](https://raw.githubusercontent.com/balance-hy/typora/master/thinkbook/image-20240425154029115.png)

#### 构造注入

**使用Lazy注解**：可以在其中一个类上使用@Lazy注解，这样这个类在初始化的时候不会去创建依赖的bean，而是等到真正使用的时候再去创建。

![image-20240425154101566](https://raw.githubusercontent.com/balance-hy/typora/master/thinkbook/image-20240425154101566.png)

### BeanFactory 和 FactoryBean 的 区别

`BeanFactory`和`FactoryBean`是Spring框架中两个重要的接口，它们的作用和使用场景是不同的。

1. **BeanFactory**：`BeanFactory`是Spring框架中最核心的接口，它提供了高级的IoC（控制反转）功能。`BeanFactory`是一个工厂模式的实现，它使用控制反转来将应用程序的配置和依赖性规范与实际的应用程序代码分离。在Spring中，`BeanFactory`提供了管理和查找bean的功能，例如`getBean(String name)`方法。

```java
ApplicationContext context = new ClassPathXmlApplicationContext("beans.xml");
User user = (User) context.getBean("user");
```

2. **FactoryBean**：`FactoryBean`是一个创建对象的工厂Bean，它的主要作用是产生复杂的或者需要特殊逻辑来创建的bean。当你在Spring配置文件中定义一个bean时，通常bean的类会直接实例化，但是如果这个bean实现了`FactoryBean`接口，那么Spring会调用它的`getObject()`方法来创建实例。

```java
public class UserFactoryBean implements FactoryBean<User> {
    @Override
    public User getObject() throws Exception {
        // 这里可以进行复杂的初始化逻辑
        return new User();
    }

    @Override
    public Class<?> getObjectType() {
        return User.class;
    }

    @Override
    public boolean isSingleton() {
        return true;
    }
}
```

总的来说，`BeanFactory`是一个获取bean的工厂，而`FactoryBean`是一个可以产生或修饰对象的工厂。在实际使用中，`BeanFactory`更多的是被用来获取bean，而`FactoryBean`则是被用来创建复杂的bean。

### spring 中用到了哪些设计模式

* 工厂模式，在各种 BeanFactory 以及 ApplicationContext 创建中都用到了
* 单例模式，比如在创建 bean 的时候，getSingleton 的重载方法中就是 spring 单例模式的实现，创建 Bean 之后将对象从早期 map 中移入到单例 Map
* 代理模式，Spring AOP 利用了 AspectJ AOP 实现的! AspectJ AOP 的底层用了动态代理

* spring MVC 中的适配器模式
  在 Spring MVC 中，DispatcherServlet 根据请求信息调用 HandlerMapping，解析请求对应的 Handler。
  解析到对应的 Handler（也就是我们平常说的 Controller 控制器）后，开始由 HandlerAdapter 适配器处理。
  HandlerAdapter 作为期望接口，具体的适配器实现类用于对目标类进行适配，Controller 作为需要适配的类。

****

### BeanFactory 和 ApplicationContext 的区别知道吗？

`BeanFactory`和`ApplicationContext`都是Spring框架中的核心接口，它们都用于访问Spring容器。`ApplicationContext`是`BeanFactory`的子接口，也就是说，`ApplicationContext`包含了`BeanFactory`的所有功能，并且还提供了更多的功能。

以下是`BeanFactory`和`ApplicationContext`的主要区别：

1. **启动时机**：`BeanFactory`在明确要求获取bean时才会初始化bean和bean的依赖，也就是说，**`BeanFactory`采用的是延迟加载的方式，只有当我们调用`getBean()`方法时，才会去创建bean。而`ApplicationContext`在启动时就会创建所有的singleton bean，所以`ApplicationContext`采用的是预加载的方式。**
2. **功能**：**`ApplicationContext`提供了更多的功能，比如国际化支持**、事件发布、资源访问、应用层的Context（如WebApplicationContext）等。而`BeanFactory`主要提供了IoC和DI的基本功能。
3. **AOP支持**：**`ApplicationContext`有更好的面向切面编程（AOP）的支持，因为它能自动检测并装配`BeanPostProcessor`和`BeanFactoryPostProcessor`类型的bean，而`BeanFactory`需要手动调用。**

## springmvc

### springmvc的执行流程

#### 视图阶段jsp

![image-20240425154811182](https://raw.githubusercontent.com/balance-hy/typora/master/thinkbook/image-20240425154811182.png)

#### 前后端分离

![image-20240425155017473](https://raw.githubusercontent.com/balance-hy/typora/master/thinkbook/image-20240425155017473.png)

## springboot

### 自动配置原理

![image-20240425155334190](https://raw.githubusercontent.com/balance-hy/typora/master/thinkbook/image-20240425155334190.png)

![image-20240425155455192](https://raw.githubusercontent.com/balance-hy/typora/master/thinkbook/image-20240425155455192.png)

![image-20240425155629235](https://raw.githubusercontent.com/balance-hy/typora/master/thinkbook/image-20240425155629235.png)

![image-20240425155716061](https://raw.githubusercontent.com/balance-hy/typora/master/thinkbook/image-20240425155716061.png)

****

### 常见注解

#### spring

![image-20240425155835535](https://raw.githubusercontent.com/balance-hy/typora/master/thinkbook/image-20240425155835535.png)

#### springmvc

![image-20240425160048019](https://raw.githubusercontent.com/balance-hy/typora/master/thinkbook/image-20240425160048019.png)

#### springboot

![image-20240425160206859](https://raw.githubusercontent.com/balance-hy/typora/master/thinkbook/image-20240425160206859.png)

## Mybatis

### mybatis执行流程

![image-20240425163916870](https://raw.githubusercontent.com/balance-hy/typora/master/thinkbook/image-20240425163916870.png)

![image-20240425163954675](https://raw.githubusercontent.com/balance-hy/typora/master/thinkbook/image-20240425163954675.png)

****

### mybatis延迟加载

Mybatis支持延迟加载，**但默认没有开启**

现有两个表，用户表和订单表

查询用户的时候，把用户所属的订单数据也查询出来，**这个是立即加载**

**查询用户的时候，暂时不查询订单数据**，当需要订单的时候，再查询订单，**这个就是延迟加载** 也就是按需加载

![image-20240425165410658](https://raw.githubusercontent.com/balance-hy/typora/master/thinkbook/image-20240425165410658.png)

结果

![image-20240425164521618](https://raw.githubusercontent.com/balance-hy/typora/master/thinkbook/image-20240425164521618.png)

懒加载

![image-20240425164613400](https://raw.githubusercontent.com/balance-hy/typora/master/thinkbook/image-20240425164613400.png)

![image-20240425164717629](https://raw.githubusercontent.com/balance-hy/typora/master/thinkbook/image-20240425164717629.png)

**刚才在mapper中是局部设置懒加载，我们可以在mybatis配置文件中全局设置懒加载**

```xml
<setting name="lazyLoadingEnabled" value="true"></setting>
```

![image-20240425165039525](https://raw.githubusercontent.com/balance-hy/typora/master/thinkbook/image-20240425165039525.png)



****

### 一级和二级缓存

> 详见mybatis学习笔记

**本地缓存：**基于PerpetualCache，本质是一个HashMap

**一级缓存：**作用域是session级别 sqlsession~

* 一级缓存:基于 PerpetualCache 的 HashMap 本地缓存，其存储作用域为 Session，当Session进行flush或close之后，该Session中的所有Cache就将清空，默认打开一级缓存

**二级缓存：**作用域是namespace和mapper的作用域，不依赖于session

![image-20240425170122471](https://raw.githubusercontent.com/balance-hy/typora/master/thinkbook/image-20240425170122471.png)



























