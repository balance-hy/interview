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

![image-20240425154101566](https://raw.githubusercontent.com/balance-hy/typora/master/thinkbook/image-20240425154101566.png)

### BeanFactory 和 FactoryBean 的区别

`BeanFactory`和`FactoryBean`都是Spring框架中的重要接口，但它们的职责和使用场景是不同的。

1. **BeanFactory**：`BeanFactory`是Spring框架中最核心的接口，它提供了高级的IoC（控制反转）功能。`BeanFactory`负责创建和管理Spring应用中的bean，包括bean的创建、初始化、装配以及销毁。当我们在Spring配置文件中定义一个bean时，Spring的`BeanFactory`就会负责创建和管理这个bean。
2. **FactoryBean**：`FactoryBean`是一个工厂Bean，可以用来产生其他的bean。当我们需要创建的bean的初始化逻辑比较复杂，或者需要进行一些特殊的配置时，就可以使用`FactoryBean`。`FactoryBean`的`getObject()`方法用来返回由FactoryBean创建的bean实例，`getObjectType()`方法返回FactoryBean创建的bean的类型，`isSingleton()`方法用来确定由FactoryBean创建的bean是否是单例。

总的来说，`BeanFactory`是Spring的IoC容器，负责创建和管理bean；而`FactoryBean`是一个可以产生或修饰对象创建的工厂bean，通常用来封装复杂的初始化逻辑。

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



























