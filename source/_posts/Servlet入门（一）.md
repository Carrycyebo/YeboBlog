---
title: Servlet入门（一）
tags:
  - Servlet
  - JavaWeb
categories:
  - JavaWeb
  - Servlet
abbrlink: c326e04b
series: Servlet
date: 2023-11-21 15:27:03
keywords:
description:
top_img:
cover:
---

# Java Web开发 - Servlet入门 (一)

## 前言-Servlet简介

Java Servlet 是运行在 Web 服务器或应用服务器上的程序 是Server Applet（服务端小程序）的简称，它是作为来自 Web 浏览器或其他 HTTP 客户端的请求和 HTTP 服务器上的数据库或应用程序之间的中间层。

使用 Servlet，您可以收集来自网页表单的用户输入，呈现来自数据库或者其他源的记录，还可以动态创建网页。



## 一、Servlet的部署

### 1. TomCat服务器

不知道在哪里下载？[点这里！](https://tomcat.apache.org/)

下载TomCat 9版本(别问为什么，问就是稳定)

直接解压就能用。

##### IDEA配置TomCat服务器

1. IDEA中 新建一个项目，可以采用Maven构建（推荐），也可以用InterlliJ构建后导入servlet-api.jar（可以从TomCat的lib目录中拷一份，然后添加目录）的依赖，建议使用 JDK 1.8 进行创建。

2. 如过没有使用Maven构建项目，需要手动添加Web应用程序的框架支持  双击<kbd>shift</kbd> 搜索 ”添加框架支持“ 选择”Web应用程序“
3. IDEA右上角”编辑配置“ ---->添加新配置，选择”TomCat服务器“ ----> "本地"  在应用程序服务器配置中将主目录添加为刚刚解压好的TomCat服务器根目录。
4. 手动添加”部署“ 或者 点击右下角”修复“，修复部署。

### 2. Servlet的注册

#### 1. web.xml中进行映射注册

```xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee http://xmlns.jcp.org/xml/ns/javaee/web-app_4_0.xsd"
         version="4.0">

    <servlet>
        <servlet-name>a</servlet-name>
        <servlet-class>com.msb.testservlet.Servlet04</servlet-class>
    </servlet>
    <servlet-mapping>
        <servlet-name>a</servlet-name>
        <url-pattern>/s04</url-pattern>
    </servlet-mapping>
</web-app>
```

servlet标签中写的servlet-name为 为该Servlet编辑注册的名称（可以随便起），servlet-class填写Servlet在本地资源的Servlet路径

servlet-mapping中servlet-name为解映射名称，url-pattern在URL中的映射地址。

在URL后添加/s04即可访问该Servlet

#### 2. 添加@WebServlet注解注册

在高版本的Servlet中支持注解注册

可以参考下文”三、Servlet 的创建“，了解用法（比web.xml简单多了）

## 二、Servlet的生命周期

Servlet的生命周期：从Servlet被创建到Servlet被销毁的过程

Servlet一次创建多次服务

一个Servlet只有一个对象，用于服务所有的请求

- Servlet创建时调用init()方法。
- Servlet调用service()方法来处理客户端的请求。
- Servlet调用destory()方法来进行销毁。

### 1. init()方法

   init()方法只会在第一次创建Servlet时被调用，在后续的请求中不再被调用。

   也就是说只有 在服务器启动之后第一个用户第一次通过URL访问该Servlet时，init()方法才会被调用。

   该方法被定义在 javax.servlet 的Servlet接口中，下面是init()的定义。

   ```java
   void init(ServletConfig var1) throws ServletException{`
   
    //
   
   }
   ```

###2.service()方法

   Service()方法是执行客户端请求的主体，客户端的请求事务，一般都在这里被处理。

   ```java
   void service(HttpServletRequest req, HttpServletResponse resp) 
       throws ServletException, IOException{
    //
   }
   ```

### 3. doGet()方法

   GET 请求来自于一个 URL 的正常请求，或者来自于一个未指定 METHOD 的 HTML 表单，它由 doGet() 方法处理。

   ```java
   void doGet(HttpServletRequest request,HttpServletResponse response)
       throws ServletException, IOException {
       //
   }
   ```

### 4. doPost()方法

与doGet()方法类似，来自于一个未指定 METHOD 的 HTML 表单。

```java
void doPost(HttpServletRequest request,
               HttpServletResponse response)
throws ServletException, IOException {
//
}
```



### 5. destory()
destory()方法在Servlet的生命周期结束时被调用。一般是在服务器被关闭时进行销毁。

```java
void destory(){
	//
}
```



#### *通过代码加强对Servlet的理解

```java
package com.msb.testservlet;

import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.IOException;


@WebServlet("/s05")
public class Servlet05 extends HttpServlet {

    public Servlet05(){
        System.out.println("构造器被调用！");
    }
    @Override
    public void init() throws ServletException {
        System.out.println("Servlet-init"); //在Servlet被调用初始化时，控制台打印出Servlet-init
    }

    @Override
    public void service(HttpServletRequest req, HttpServletResponse res) throws ServletException, IOException {

        System.out.println("处理业务"); //service被调用时 ，控制台打印出”处理业务“
    }

    @Override
    public void destroy() {
        System.out.println("Servlet被销毁！"); //Servlet被销毁时，打印出”Servlet被销毁！“
    }
}

```

接下来启动服务器，访问该Servlet

控制台会输出

```
构造器被调用！
Servlet-init
处理业务
```

如果刷新页面，控制台将直接输出

```
处理业务
```

而不会再次调用init()方法

直到服务器被关闭时控制台才会输出

```
Servlet被销毁！
```




## 三、Servlet 的创建

 ### Servlet的实现有三种方式

#### 1、实现Servlet接口    

由于是实现Servlet的接口，因此需要对Servlet中的方法进行实现：

```java
package com.msb.testservlet;

import javax.servlet.*;
import javax.servlet.annotation.WebServlet;
import java.io.IOException;

@WebServlet("/s03")
public class Servlet03 implements Servlet {
    @Override
    public void init(ServletConfig servletConfig) throws ServletException {

    }
    @Override
    public ServletConfig getServletConfig() {
        return null;
    }

    @Override
    public void service(ServletRequest servletRequest, ServletResponse servletResponse) throws ServletException, IOException {
        System.out.println("servlet03");
    }

    @Override
    public String getServletInfo() {
        return null;
    }

    @Override
    public void destroy() {

    }
}

```

#### 2、继承GenericServlet

GenericServlet实现了Servlet, ServletConfig, Serializable

一般只需要对其中的Service()方法进行重写

```java
package com.msb.testservlet;

import javax.servlet.GenericServlet;
import javax.servlet.ServletException;
import javax.servlet.ServletRequest;
import javax.servlet.ServletResponse;
import javax.servlet.annotation.WebServlet;
import java.io.IOException;


@WebServlet("/s02")
public class Servlet02 extends GenericServlet {
    @Override
    public void service(ServletRequest servletRequest, ServletResponse servletResponse) throws ServletException, IOException {
        System.out.println("servlet02...");
    }
}

```



#### 3、继承HttpServlet

第三种方法也是最常用的方法，HttpServlet这个抽象类继承于GenericServlet。

```java
package com.msb.testservlet;

import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.IOException;


@WebServlet("/s01")
public class Servlet01 extends HttpServlet {
    @Override
    protected void service(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        //控制台录入一句话
        System.out.println("Hi Servlet!");

        //响应一句话给浏览器
        resp.getWriter().write("Hi Servlet!");
    }
}

```
