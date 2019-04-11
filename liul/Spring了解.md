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





















