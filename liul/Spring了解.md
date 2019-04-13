# Spring学习笔记

> 主要介绍了一下spring的基本信息

## 第一节 spring简介

spring是2003年兴起的一个轻量级java开发框架，其优点主要包括：

​	1.方便解耦，简化开发

​	2.IOC控制反转，对象的创建交给了spring完成，并将创建好的对象交给使用者

​	3.AOP面向切面编程，可以将非业务逻辑从代码中抽出

​	4.非侵入式

## 第二节 第一个spring项目

1.首先我们要创建一个maven项目，并引入与spring有关的依赖

~~~java
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-context</artifactId>
    <version>5.0.4.RELEASE</version>
</dependency>
~~~

2.创建spring的配置文件

​	这个配置文件的名字可以随意取名，一般取名为applicationContext.xml，最好放于resources文件夹下

​	需要引入一些xsd文件

~~~
<beans xmlns="http://www.springframework.org/schema/beans"
   xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
   xmlns:aop="http://www.springframework.org/schema/aop"
   xmlns:context="http://www.springframework.org/schema/context"
   xsi:schemaLocation="http://www.springframework.org/schema/beans
    http://www.springframework.org/schema/beans/spring-beans.xsd
    http://www.springframework.org/schema/aop
    http://www.springframework.org/schema/aop/spring-aop.xsd
    http://www.springframework.org/schema/context
    http://www.springframework.org/schema/context/spring-context.xsd">
</beans>
~~~

3.创建一个类

~~~java
public class User {
    public void buy(){
        System.out.println("buy something");
    }
}
~~~

4.在配置文件配置bean

在第二步新建的配置文件中，配置第三步中创建的类的bean信息

~~~
<bean id="user" class="service.User"></bean>
~~~

注意这里的id可以随便取名字，class是带对应的包名和类名

5.创建测试类

~~~java
public static void main(String [] args){
        //加载配置文件
        ApplicationContext context= new ClassPathXmlApplicationContext("applicationContext.xml");
        //得到对象
        User user=(User)context.getBean("user");
        user.buy();
    }
}
~~~

这样要插一句的就是，读取配置文件时，如果配置文件在项目的类路径下，使用ClassPathXmlApplicationContext

如果配置文件不在项目的类路径下则使用FileSystemXmlApplicationContext

## 第三节 spring中的IOC

控制反转（IOC）是一种思想，指的是将创建对象的操作权交给容器（例如spring），通过容器来装配和管理对象的创建，控制反转其实就是对这些对象控制权的反转，控制权由程序交给了外部的容器。

依赖注入是实现控制反转这种思想的比较常用的一种方式

### ApplicationContext接口

在实例程序中通过ApplicationContext接口获取到spring注入给程序的对象，这里的ApplicationContext就相当于是springIoc的容器，applicationContext使用**反射**的方式创建bean对象，并在读取配置文件后将里面注册的bean全部创建对象。

可以通过该接口的两个实现类来创建容器，这两个实现类即ClassPathXmlApplicationContext和FileSystemXmlApplicationContext其区别已经在上面说清楚

BeanFactory接口是ApplicationContext的父接口，其区别在于BeanFactory在读取配置文件后不会创建里面的bean对象，而是在使用的时候才会创建

### bean的作用域

使用spring ioc容器为我们创建对象的时候，可以设定该对象的作用域，默认是单例的。

singleton： 单态模式。即在一个Spring ioc容器中，使用 singleton 定义的 Bean 是单例的，只有一个实例。默认为单例的。

prototype： 原型模式。即每次使用 getBean 方法获取的同一个bean的实例都是一个新的实例。

request：对于每次 HTTP 请求，都将会产生一个不同的 Bean 实例。

session：对于每个不同的 HTTP session，都将产生一个不同的 Bean 实例。

application：在一个web应用中会产生一个bean实例，就相当于在一个ServletContext中只有该bean的实例。

websocket：在一个websocket中会产生一个bean实例。

对bean作用域的设置代码写法为

~~~
<bean id="user" class="service.User" scope="prototype"></bean>
~~~

### bean的生命周期

如果我们想在bean初始化或者销毁的时候调用某些特定的方法，可以在该bean对应的类中写两个方法，比如我写了init和delete方法（这里的方法名可以随便取）

~~~java
public void init(){
        System.out.println("初始化bean");
    }
public void delete(){
        System.out.println("结束bean");
    }
~~~

然后在配置文件中写上

~~~java
<bean id="user" class="service.User" init-method="init" destroy-method="delete" ></bean>
~~~

注意这个时候的作用域最后选择单例模式

### 依赖注入

依赖注入分为两种情况一种是基于配置文件的依赖注入，一种是基于注解的依赖注入

基于配置文件的依赖注入又包括set注入和构造注入

set注入：

我们先写一个作为注入对象的类

~~~java
public class Dao {
    public void pay(){
        System.out.println("支付了账单");
    }
}
~~~

写出set方法

~~~java
public class User01 {
    Dao dao;

    public void setDao(Dao dao) {
        this.dao = dao;
    }

    public void buy(){
        System.out.println("买了东西");
        dao.pay();
    }
}
~~~

配置文件中的写法

~~~java
<bean id="user01" class="service.User01" >
        <property name="dao" ref="daoId"></property>
    </bean>
    <bean id="daoId" class="util.Dao"></bean>
~~~

测试类

~~~java
public class Text02 {
    public static void main(String [] args){
        ApplicationContext context=new ClassPathXmlApplicationContext("applicationContext.xml");
        User01 user01=(User01)context.getBean("user01");
        user01.buy();
    }
}
~~~

构造注入：

写出构造方法

~~~java
public class User01 {
    Dao dao;

  public User01(Dao dao){
      this.dao=dao;
  }
    public void buy(){
        System.out.println("买了东西");
        dao.pay();
    }
}
~~~

配置文件中的写法

~~~java
<bean id="user01" class="service.User01" >
       <constructor-arg name="dao" ref="daoId"></constructor-arg>
    </bean>
    <bean id="daoId" class="util.Dao"></bean>
~~~



### 基于注解的注入：

> Component 
>
> repository:dao层的常用注释
>
> Service：service层常用注释
>
> Controller：controller层的常用注释

在配置文件中写上文件扫描器

~~~java
<context:component-scan base-package="com.liulei"></context:component-scan>
~~~

在类上写明标签

~~~java
@Service("dao")
public class Dao {
    public void pay(){
        System.out.println("支付了账单");
    }
}
~~~

使用Autowired标签完成注入

~~~java
@Service("user01")
public class User01 {
    @Autowired
    Dao dao;

  public User01(Dao dao){
      this.dao=dao;
  }
    public void buy(){
        System.out.println("买了东西");
        dao.pay();
    }
}
~~~

测试类的写法，测试类中同样要有加载配置文件的操作

~~~java
public class Text02 {
    @Autowired
    static User01 user01;
    public static void main(String [] args){
    ApplicationContext context=new ClassPathXmlApplicationContext("applicationContext.xml");
        user01=(User01)context.getBean("user01");
        user01.buy();
    }
}
~~~

这里我们来介绍一下@Autowired注解和@Resource注解的区别

Autowired注解是spring提供的，这个注解默认按照类型自动装配可以和Qualifier注解配合按照名称装配，如果名称写不对则找不到对应的bean

~~~java
 @Autowired
    @Qualifier("dao")
    Dao dao;
~~~

@Resource是java提供的，是按照名称去找对应的bean的

### 一些其他的注解

1.使用注解进行初始化和销毁操作

~~~java
  @PostConstruct
    public void before(){
        System.out.println("初始化");
    }
    @PreDestroy
    public void delete(){
        System.out.println("销毁");
    }
~~~

2.@Scope注解用于调制bean的作用域

### 有多个配置文件的情况

当有多个配置文件时，我们采取的解决方法有两个，第一个是在测试类中写一个string数组在创建ApplicationContext对象时将这个数组作为参数传进去。第二种方法是在测试类中只写主配置文件，在主配置文件中使用import语句将其他的配置文件引入进来。

~~~java
String[] files = {"spring-aop.xml","spring-bean.xml","applicationContext.xml"};
ApplicationContext context = new ClassPathXmlApplicationContext(files);
~~~

~~~java
<import resource="spring-*.xml"/>
~~~

## 第四节 spring中的AOP

### 首先解释一下我们遇到的问题

在编写一些业务代码时，常常会遇到一些非业务的代码注入记录日志和事务管理的代码，最早的解决方法如下

1.编写非业务逻辑相关的类，这里以记录日志为例

~~~java
@Repository
public class MyLog {
    public  void doLog(Class<?> clazz){
        System.out.println("记录日志:" + clazz.getName());
    }
}
~~~

2.在service层调用这个方法

~~~java
@Service("user01")
public class User01 {
    @Autowired
    MyLog myLog;
public void buy(){
    System.out.println("buy something");
    //在购买后记录日志
    myLog.doLog(this.getClass());
    }
}
~~~

3.在测试类中测试

~~~java
public class Text01 {
    public static void main(String [] args){
        ApplicationContext context=new ClassPathXmlApplicationContext("spring.xml");
        User01 user01=(User01)context.getBean("user01");
        user01.buy();
    }
}
~~~

通过上面的例子我们可以看出，记录日志的代码已经写进了service的业务逻辑层，其缺点主要有两个其一是造成业务代码的冗余复杂。其二一旦非业务代码要做出修改，每个业务程序都有进行修改。

非业务代码应用广，如果能将非业务代码从业务程序中抽离出来就最好不过了

### 使用动态代理来解决这个问题

1.为service层的类写一个接口，方法还有记录日志的类

~~~java
public interface UserIn {
     void buy();
}

public class User01 implements UserIn {
public void buy(){
    System.out.println("buy something");
    }
}

public class MyLog {
    public  void doLog(Class<?> clazz){
        System.out.println("记录日志:" + clazz.getName());
    }
}
~~~

2.写出自己的动态代理类

~~~java
public class MyInvocationHandler implements InvocationHandler {
    MyLog myLog=new MyLog();
    private Object target;

    public MyInvocationHandler(Object target) {
        this.target = target;
    }

    @Override
    public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
        //记录日志
        myLog.doLog(target.getClass());
        //业务逻辑
        Object invoke=method.invoke(target,args);

        return invoke;
    }
}
~~~

3.测试类

~~~java
public class Text01 {
    public static void main(String [] args){

        User01 user01=new User01();
        //创建代理
        UserIn userPorxy=(UserIn) Proxy.newProxyInstance(user01.getClass().getClassLoader(),user01.getClass().getInterfaces(),new MyInvocationHandler(user01));
        userPorxy.buy();
    }
}
~~~

虽然对动态代理有点迷糊但是我们可以发现，我们的业务程序 user01中已经没有了记录日志的这种非业务代码

下面我们用点时间去理解动态代理，比较spring的AOP也是基于动态代理的

***

在动态代理中有个比较重要的接口和类即InvocationHandler接口和Proxy

我们首先来看看InvocationHandler接口吧

api中对这个接口的描述为：

每一个动态代理类都必须要实现InvocationHandler接口，并且每个代理类都关联到一个handler，当我们通过代理对象调用一个方法的时候，这个方法的调用就会被转发为由InvocationHandler接口的invoke方法来进行调用，下面我们来看看这个接口的唯一的一个方法invoke方法：

~~~java
Object invoke(Object proxy, Method method, Object[] args) throws Throwable
~~~

其中的三个参数 proxy即我们所代理的那个真实对象，method指的是我们要调用真实对象的某个方法的Method对象，args为这个调用真是对象某个方法时接受的参数。不懂没关系我们接着看看 Proxy这个类

API对这个类的描述是：这个类的作用就是用来动态创建一个代理对象的类，太提供了很多的方法，我们用的最多的就是newProxyInstance这个方法

~~~java
public static Object newProxyInstance(ClassLoader loader, Class<?>[] interfaces,  InvocationHandler h)  throws IllegalArgumentException
~~~

loader 一个classLoader对象，定义了由那个ClassLoader对象来对生成的代理对象进行加载

interface 一个interface对象数组，表示我们将要给我们需要代理的对象提供一组什么接口，如果我提供一组接口给他那么这个代理对象就宣称实现了该接口（多态），这样我们就能调用这组接口的方法了

h一个InvocationHandler对象

接下来让我们从main方法进入这个动态代理的过程：

~~~java
 //创建代理
        UserIn userPorxy=(UserIn) Proxy.newProxyInstance(user01.getClass().getClassLoader(),user01.getClass().getInterfaces(),new MyInvocationHandler(user01));
~~~

这里就解释了为什么我们被代理的对象user01必须要实现一个接口userIn，这里其实就是生产了一个与user01实现了相同的接口（UserIn）的类，在参数中我们给他提供了我们类的信息，这个类实现的接口的信息，已经我们的中间类的信息，当我们产生的代理对象userPorxy调用方法是就会跳转到MyInvocationHandler类的invoke方法中，下面我们来看看这个invoke方法中我们写了什么

~~~java
 public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
        //记录日志
        myLog.doLog(target.getClass());
        //业务逻辑
        Object invoke=method.invoke(target,args);
        return invoke;
    }
~~~

这里我们告诉了invoke方法我们正在使用哪个代理类，使用的是哪个方法，这个方法中的参数

在这个方法中我们加入了扩展方法doLog

执行了委托类的方法并得到返回结果，这里的target为

~~~java
 private Object target;

    public MyInvocationHandler(Object target) {
        this.target = target;
    }

Proxy.newProxyInstance(user01.getClass().getClassLoader(),user01.getClass().getInterfaces(),new MyInvocationHandler(user01));
~~~

到这里我们大概理解了动态代理的一些内容，就是我们使用proxy类中的newProxyInstance方法得到了一个与我们需要被代理的类继承了同一个接口的动态代理类，使用这个代理类去执行被代理类的方法是，就会跳转到中介类MyInvocationHandler中的invoke方法中，就是在这个方法中对被代理类的方法做了加强。

### AOP简介

面向切面编程是对面向对象编程的一个补充，spring底层就是采用了动态代理实现AOP，采用了两种代理模式

如果被代理类实现了接口，会默认使用jdk代理

如果类没有实现接口，会使用CGLIB代理

AOP术语

​	目标对象：目标对象指要被增强的对象，即包含主业务逻辑的类的对象

​	切面：泛指非业务逻辑

​	连接点：业务接口中的方法均为连接点

​	切入点：指具体织入的方法

​	通知：前置通知，后置通知，异常通知，最终通知，环绕通知

​	织入

### AspectJ对AOP的支持

第一步 在pom文件中增加响应的依赖

~~~java
<dependency>
        <groupId>org.aspectj</groupId>
        <artifactId>aspectjweaver</artifactId>
        <version>1.8.13</version>
    </dependency>
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-aspects</artifactId>
        <version>5.0.4.RELEASE</version>
    </dependency>

~~~

第二步 创建接口及其实现类，根据上文中对动态代理的讲解，这里必须使用接口

~~~java
public interface UserService {
        void add();
        void deleteUser();
        void selectUser();
}

public class UserServiceIm  implements UserService{
    @Override
    public void add() {
        System.out.println("添加用户");
    }


    @Override
    public void deleteUser() {
        System.out.println("执行deleteUser方法");

    }

    @Override
    public void selectUser() {
        System.out.println("执行selectUser方法");

    }
}
~~~

第三步 写出切面类

~~~java
public class MyAspect {
    public void before(){
        System.out.println("=====前置通知=====");
    }
    public void after(){
        System.out.println("=====最终通知=====");
    }
    
    public Object around(ProceedingJoinPoint pjp) throws Throwable{
        System.out.println("=====环绕通知:前=====");
        Object proceed=pjp.proceed();
        System.out.println("=====环绕通知：后=====");
        return proceed;
    }
}
~~~

第四步 写配置文件

~~~java
  <bean id="userService" class="com.liulei.service.UserServiceIm"></bean>
    <bean id="myAspect" class="com.liulei.util.MyAspect"></bean>

    <!--配置AOP-->
    <aop:config>
        <!--定义切入点-->
        <aop:pointcut id="addPointCut" expression="execution(* com.liulei.service.UserServiceIm.add())"/>
        <aop:pointcut id="selectPointCut" expression="execution(* com.liulei.service.UserServiceIm.selectUser())"/>
        <aop:pointcut id="deleteUser" expression="execution(* com.liulei.service.UserServiceIm.deleteUser())"/>

        <!--定义切面-->
        <aop:aspect ref="myAspect">
            <aop:before method="before" pointcut-ref="addPointCut"/>
            <aop:after method="after" pointcut-ref="selectPointCut"/>
            <aop:around method="around" pointcut-ref="deleteUser"/>
        </aop:aspect>
    </aop:config>
~~~

第五步 写测试类 注意这里由bean得到的实现了相同接口的类

~~~java
public class Test {
    public static void main(String [] args){
        ApplicationContext context=new ClassPathXmlApplicationContext("spring.xml");
        UserService userService=(UserService)context.getBean("userService");
        userService.add();
    }
}
~~~





















