# Spring 框架

1. 是一个 bean 容器框架
2. 提供了 IoC,DI 和 context 的功能
3. 能够更好的解耦，依赖的注入交给 spring 管理，不需要我们去主动的 new，可以通过更改配置文件来修改注入的类型
4. 导入了 spring 的 core,context 和 bean 包，context 提供了存储 spring 各种信息的位置，bean 提供了生成 bean 的方式，可以将 bean 放到上下文中去。

## IoC

1. Inreverse of Cpntroller 控制反转：将对象的创建交给 spring 管理
2. lazy-init: 懒加载，不常用
3. 负责对象创建，属性赋值，对象管理
4. 使用：ClassPathXmlApplication("config.xml"),getBean("id")方法可获得相应对象

### 基于配置文件

1. 创建一个配置文件，如 config.xml，引入 context,bean 的约束，将需要的创建的 bean 声明在里面，设置 id(唯一标识符) 和 class(全限定类名) 的值,有需要注入的属性则在里面添加 property 节点绑定 name(属性的名称) 和 ref 属性(bean 的 id);
2. 创建 ApplicationContext 对象，并将 xml 的 path 传进去
3. 通过 2 的 getBean 方法根据 id 取值

### 基于注解

1. 在类上加@bean 的注解
2. 在需要注入的属性上加@Autowired 默认 byType

## DI

1. 通过反射将依赖的属性注入到 java 类中，
2. 默认饿汉式

## AOP

1. 当业务有多个操作时（特别是有写操作时），就需要事务来控制，传统的写法是在连接后开启事务，在逻辑结束后提交事务，但这样需要在每个事务开始结束都添加相同的代码。
2. 基于上述问题，使用良好的设计方法来进行优化，减少耦合，降低重复代码的编写，模板方法模式，代理模式
3. 代理模式：在客户端不能接触目标对象的情况下，通过代理来间接调用核心逻辑的一种设计模式
   - 静态代理：目标类实现接口，代理类通过接口注入目标对象来间接调用目标对象，并在其前后执行辅助逻辑
   - 动态代理：java.lang.reflect.Proxy 来生成代理对象（需要有接口，）或 cglib 生成代理对象（运行时生成子类）
   - jdk Proxy.newProxyInstance(classLoader(target 的类加载器，用来装载产生的字节码文件),interfaces(继承的接口),InvocationHandler(回调逻辑))
   - cglib 导入 spring-cglib 依赖，创建产生代理对象的实例 Enhancer()，enhancer.setSuperClass(target.class)设置代理类的父类为 target,setCallBack(MethodInterceptor)进行代理，create()创建代理对象
   - new MethodInterceptor(
     proxy, //cglib 包装后的对象
     method, //目标核心方法
     args, //方法参数
     proxy //代理对象
     ){
     preMethod();
     Object obj = method.invoke(target,args)
     postMethod();
     return obj;
     }

### Spring AOP

```xml
<!-- 目标 -->
<bean id="target" class="xx.xxx.xx"></bean>
<!-- 切面逻辑 -->
<bean id="handler" class="x.x.x"></bean>
<!-- 上述交给spring管理 -->
<aop:config>
   <aop:aspect ref="handler">
      <aop:pointcut id="cut" expression="* execution(x.xx.x..*Service.*(..))"/>
      <!--ref handler中的方法 -->
      <aop:before method="abc" pointcut="* execution(x.xx.x..*Service.*(..))"/>
      <aop:after-returning method="xyz" pointcut-ref="cut"/>
   </aop:aspect>
</aop:config>
```

上述实现了 0 耦合的 aop 操作

### 事务

1. 切面逻辑：在项目中与具体业务没有直接关联的逻辑，这样的逻辑在各个模块中用线条穿起来，就形成一个；切点：在具体部分应用切面逻辑
2. aspect oriented Programming：面向切面编程，利用动态成字节码的方式
   ，创建目标的代理对象，从而将具体的代理逻辑和切面逻辑解耦
   配置切面

```xml
    <bean id="txManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
        <property name="dataSource" ref="ds"/>
    </bean>
```

这个管理器中，主要是包含了具体的 JDBC 的事务管理实现，而 MyBatis 又是基于 JDBC 的，这个类在`AOP`实际上就是`切面逻辑`

```xml
    <!--配置通知bean：事务的传播特性-->
    <tx:advice id="advice1" transaction-manager="txManager">
        <tx:attributes>
            <tx:method name="select*" propagation="SUPPORTS" isolation="REPEATABLE_READ"/>
            <tx:method name="query*" propagation="SUPPORTS"/>
            <tx:method name="find*" propagation="SUPPORTS"/>
            <tx:method name="save*" propagation="REQUIRED"/>
            <tx:method name="insert*" propagation="REQUIRED"/>
            <tx:method name="update*" propagation="REQUIRED"/>
            <tx:method name="delete*" propagation="REQUIRED"/>
            <tx:method name="del*" propagation="REQUIRED"/>
        </tx:attributes>
    </tx:advice>
```

- SUPPORTS 表示如果该方法被内嵌到另一个含有事务的方法中进行调用，则支持事务，否则，不应用事务
- REQUIRED 必须应用事务，如果被内嵌到另一个含有事务的方法中进行调用，则沿用上层事务
- isolation：数据库的隔离级别，默认就是`RR`

```xml
    <!--配置切面：切点(where) + 执行什么样的切面逻辑(do what)-->
    <aop:config>
        <!--配置通知者-->
        <aop:advisor advice-ref="advice1" pointcut="execution(* com.example.service..*Service.*(..))"/>
    </aop:config>
```

- advice-ref：指向某一个事务通知，因为通知里面有具体的哪些方法应用事务，哪些不应用的信息
- pointcut：通过表达式，回答这样一个问题：具体事务的边界在哪里？但是它回答这个具体的切点，颗粒度一般指向到某一个具体的类，具体的方法是否应用事务，由通知来回答
- 之前的 before（开启事务）和 after-returning（提交事务）、after（释放连接资源）、after-throwing（回滚事务），都已经整合到`DataSourceTransactionManager + TransactionTemplate`

## springMVC

1. 是基于 spring 的 mvc 框架
2. 松耦合，插件式，灵活，比 baseServlet 更具扩展性，提供了一套完整的注解，在数据绑定和视图解析提供了更多配置方式，可以灵活配置
3. 对 restful url 设计方法提供了良好支持

## mybatisPlus

1. 添加依赖
2. mapper 继承 BaseMapper\<T>,就可以有大量方法
3. 调用方法，传递 Wrapper 即可

## SSM 整合

1. 配置 web.xml 前端控制器 DispatcherServlet
   - contextConfigLocation 传递 spring 配置文件
   - spring 配置文件添加 bean
2. spring 配置文件中添加 mybatisplus 配置，注入数据源
3. 添加注解支持
4. 三层加相应注解，调用继承的 mapper 传递 wrapper,传到 ResponseBody 向前端传复杂数据需 json
