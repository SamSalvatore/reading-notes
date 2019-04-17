# springMVC框架

## 第一节 第一个springMVC案例

springmvc是一个表现层的框架，在没有这类的框架之前一般使用servlet和javaEE来处理前台发送的请求。

创建speingMVC的几步

第一步：创建一个maven项目

第二步：导入相关的依赖

~~~java
<dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
            <version>3.8.1</version>
            <scope>test</scope>
        </dependency>
        <dependency>
            <groupId>javax.servlet</groupId>
            <artifactId>javax.servlet-api</artifactId>
            <version>3.1.0</version>
        </dependency>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-webmvc</artifactId>
            <version>5.0.4.RELEASE</version>
        </dependency>
~~~

第三步 在web.xml文件中写中央配置器（web.xml文件的地址 src/main/webapp/WEB-INF/web.xml）

~~~java
<servlet>
        <servlet-name>springMVC</servlet-name>
        <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
        <init-param>
            <param-name>contextConfigLocation</param-name>
            <param-value>classpath:springMVC.xml</param-value>
        </init-param>
    </servlet>
    <servlet-mapping>
        <servlet-name>springMVC</servlet-name>
        <url-pattern>*.do</url-pattern>
    </servlet-mapping>
~~~

第四步 编写springMVC的配置文件（配置文件的名字可以随便取，路径一般为src/main/resources/spring.xml）

~~~java
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:p="http://www.springframework.org/schema/p"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="
        http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/context
        http://www.springframework.org/schema/context/spring-context.xsd">


</beans>
~~~

第五步 创建一个实现了org.springframework.web.servlet.mvc.Controller接口的类，并重写handleRequest方法，在这个方法中我们可以对ModelAndView对象进行相应的操作

~~~java
public class Mycontroller implements Controller {
    @Override
    public ModelAndView handleRequest(HttpServletRequest httpServletRequest, HttpServletResponse httpServletResponse) throws Exception {
        System.out.println("11111");
        ModelAndView mv=new ModelAndView();
        mv.addObject("hello","liulei mvc");
        mv.setViewName("/WEB-INF/jsp/first.jsp");

        return mv;
    }
}
~~~

第6步 在springmvc配置文件中注册controller

~~~java
<bean id="/hello.do" class="com.liulei.test.Mycontroller"/>
~~~

第7步 创建相应的jsp文件

~~~java
<%@ page language="java" contentType="text/html; charset=utf-8"
         pageEncoding="utf-8"%>
<!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
<%@page isELIgnored="false" %>
<html>
<head>
    <title>Insert title here</title>
</head>
<body>
 ${hello}
</body>
</html>
~~~

将项目配置到tomcat中即完成了相应的编辑工作

springMVC的工作流程

浏览器的url（hello.do）到达服务器，第一站是web.xml文件，在web文件的中央控制器中我们知道了以.do结尾的文件都交给了dispatcherServlet类去处理，并且告诉了这个类，springMVC配置文件的地址和名字，这个类就会带着hello.do去springmvc的配置文件中寻找有没有controller类，我们正好在其中配置了，hello.do的类去找 Mycontroller这个类，到达这个类以后调用handleRequest方法，提供了一个跳转页面，对ModelAndView文件进行处理。