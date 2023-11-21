---
title: Servlet入门（二）
tags:
  - Servlet
  - JavaWeb
categories:
  - JavaWeb
  - Servlet
abbrlink: f3300c93
series: Servlet
date: 2023-11-21 20:02:16
keywords:
description:
top_img:
cover:
---

# Java Web开发 - Servlet入门（二）

## 一、HttpServletRequest对象

### 1. 包含的get型方法

```java
        //获取客户端请求时完整的URL
        StringBuffer url = req.getRequestURL();

        //获取请求行中的资源名称部分（项目名称开始）
        String uri = req.getRequestURI();

        //获取请求行中的参数部分
        String queryString = req.getQueryString();

        //获取客户端请求方式
        String method = req.getMethod();

        //获取HTTP版本号
        String protocol = req.getProtocol();

        //获取WebApp名字
        String contextPath = req.getContextPath();

        //获取指定名称的参数的值
        String uname = req.getParameter("uname");
        String pwd = req.getParameter("pwd");

        //获取指定名称的参数的所有的值
        String[] hobby = req.getParameterValues("hobby");//返回类型为一个数组


```

下面是一个实例：

```java
package com.msb.testrequest;

import com.sun.media.jfxmediaimpl.HostUtils;

import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.IOException;
import java.lang.reflect.Array;
import java.util.Arrays;


@WebServlet("/sr01")
public class Servlet01 extends HttpServlet {
    @Override
    protected void service(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        //获取客户端请求时完整的URL
        StringBuffer url = req.getRequestURL();
        System.out.println("客户端请求时完整的url是：" + url);

        //获取请求行中的资源名称部分（项目名称开始）
        String uri = req.getRequestURI();
        System.out.println("客户端请求的资源部分：" + uri);

        //获取请求行中的参数部分
        String queryString = req.getQueryString();
        System.out.println("请求的参数：" + queryString);

        //获取客户端请求方式
        String method = req.getMethod();
        System.out.println("请求方式：" + method);

        //获取HTTP版本号
        String protocol = req.getProtocol();
        System.out.println("请求协议：" + protocol);

        //获取WebApp名字
        String contextPath = req.getContextPath();
        System.out.println("获取项目的站点名：" + contextPath);


        System.out.println("---------------------------------------------");
        //获取指定名称的参数的值
        String uname = req.getParameter("uname");
        System.out.println("指定的uname参数为" + uname);

        String pwd = req.getParameter("pwd");
        System.out.println("指定的pwd参数为" + pwd);

        //获取指定名称的参数的所有的值
        String[] hobby = req.getParameterValues("hobby");//返回类型为一个数组
        System.out.println("指定的hobby参数为：" + Arrays.toString(hobby));


        //构造payload  http://localhost:8080/s/sr01?uname=zhao&pwd=123456&hobby=hahah&hobby=heiheiehi  观察控制台输出
    }
}

```

我的部署地址为/s因此构造payload   上面的paload 观察控制台输出

```
客户端请求时完整的url是：http://localhost:8080/s/sr01
客户端请求的资源部分：/s/sr01
请求的参数：uname=zhao&pwd=123456&hobby=hahah&hobby=heiheiehi
请求方式：GET
请求协议：HTTP/1.1
获取项目的站点名：/s
---------------------------------------------
指定的uname参数为zhao
指定的pwd参数为123456
指定的hobby参数为：[hahah, heiheiehi]
```



#### 发现传入中文乱码问题？

查看   JSPServlet开发中遇到的乱码问题



### 2. 请求转发

在客户端的一次请求中，使用请求转发，可将HttpServletRequest对象再次传递给其他Servlet。

```java
req.getRequestDispatcher("Servlet的映射名").forward(req, resp);
```

请求转发: 可以跳至服务器内部资源  如其他Servlet或页面

```java
        req.getRequestDispatcher("sr04").forward(req, resp);
//        req.getRequestDispatcher("index.jsp").forward(req, resp);
```

但是不能跳转服务器外部的资源，比如：

```java
××× req.getRequestDispatcher("http://www.baidu.com").forward(req,resp);//不可以
```

请求转发也可以实现 域对象数据共享



下面是一个实例 假设有/sr03和/sr04两个servlet：

```java
package com.msb.testrequest;

import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.IOException;

@WebServlet("/sr03")
public class Servlet03 extends HttpServlet {
    @Override
    protected void service(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        //获取前端传来的数据
        String uname = req.getParameter("uname");
        System.out.println("前端传到sr03-Servlet的数据为：" + uname);

        //域对象数据共享
        req.setAttribute("age", "18");
        String age = (String) req.getAttribute("age");
        System.out.println("sr03-Servlet中的域对象age的值为：" + age);

        //请求转发: 可以跳服务器内部资源  如其他Servlet或页面
        req.getRequestDispatcher("sr04").forward(req, resp);
//        req.getRequestDispatcher("index.jsp").forward(req, resp);


        //但不能跳服务器外部资源 如下
//   ××× req.getRequestDispatcher("http://www.baidu.com").forward(req,resp);

        //在请求转发后 输出一句话
        System.out.println("-------sr03");
    }
}

```

```java
package com.msb.testrequest;

import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.IOException;


@WebServlet("/sr04")
public class Servlet04 extends HttpServlet {
    @Override
    protected void service(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        //获取请求的数据
        String uname = req.getParameter("uname");

        //获取域对象中的数据：
        String age = (String) req.getAttribute("age");

        System.out.println("sr04-Servlet获取的uname数值为：" + uname);
        System.out.println("sr04-Servlet获取的域对象age的值为：" + age);


    }
}

//构造payload http://localhost:8080/s/sr03?uname=zhao
```

构造上面payload 观察控制台输出：

```
前端传到sr03-Servlet的数据为：zhao
sr03-Servlet中的域对象age的值为：18
sr04-Servlet获取的uname数值为：zhao
sr04-Servlet获取的域对象age的值为：18
-------sr03
```

发现：

1. ”-------sr03“    最后输出  说明 当触发请求转发时，立即转向指向的Servlet，待指向的Servlet运行结束时，继续执行该Servlet

2. 请求的URL没有变化，说明请求转发时服务器内部进行的一个动作，客户端没有进行第二次请求。

## 二、HttpServletResponse对象

### 1.包含的部分方法

```java
	//响应数据：
       //！！！！字符输出流和字节输出流不能同时响应
        //字符输出流
        resp.getWriter().write("hahah...");
        resp.getWriter().print("heiheihei");

        //字节输出流
        //resp.getOutputStream().write("hai...".getBytes());

        //设置响应头
        resp.setHeader("n", "duiduidui");
        resp.setHeader("v", "addd");
        resp.setHeader("n", "changed?"); //键是一样的时候，值会覆盖前面的响应

        //添加响应头
        resp.addHeader("n", "duiduidui");
        resp.addHeader("v", "addd");
        resp.addHeader("n", "add?"); //键是一样的时候，仍会添加一个新的响应头
```

注意  字符输出流和字节输出流不能同时被响应，如果同时使用优先响应先被定义的输出，后一种响应的输出将报错不能输出。

### 2.重定向

重定向(Redirect)就是通过各种方法将各种网络请求重新定个方向转到其它位置（如：网页重定向、[域名](https://baike.baidu.com/item/域名/86062?fromModule=lemma_inlink)的重定向、路由选择的变化也是对数据报文经由路径的一种重定向），状态码为: 302 。

在HttpServletResponse对象中也提供关于重定向的方法

```java
resp.sendRedirect("重定向的目标")
```

重定向可以定向到服务器外部的资源 如：baidu.com

下面是一个实例 假设有/red01和/red02两个servlet：

```java
package com.msb.testresponse;

import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.IOException;

@WebServlet("/red01")
public class Servlet03 extends HttpServlet {
    @Override
    protected void service(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        //获取前端传来的参数
        String uname = req.getParameter("uname");
        System.out.println("前端传来的参数uname：" + uname);

        //重定向
        resp.sendRedirect("red02");

        //重定向可以请求服务器外部的资源
        //resp.sendRedirect("http://www.baidu.com");

        //重定向后打印一句话
        System.out.println("------------重定向后打印的内容------------");


    }
}

```

```java
package com.msb.testresponse;

import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import javax.swing.text.html.HTML;
import java.io.IOException;

@WebServlet("/red02")
public class Servlet04 extends HttpServlet {
    @Override
    protected void service(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        //是否可以接受到重定向的数据
        String uname = req.getParameter("uname");
        System.out.println("是否可以接受到重定向的数据：" + uname); //重定向接受不到数据

        //重定向是一种服务器指导客户端发出请求的行为

    }
}
//payload : http://localhost:8080/s/red01?uname=zhao
```

构造payload 观察控制台输出：

```
前端传来的参数uname：zhao
------------重定向后打印的内容------------
是否可以接受到重定向的数据：null
```

发现：

1. 重定向后的数据不能够被发送。
2. 重定向的servlet会先执行完当前servlet的内容，再去指导浏览器重定向新的内容。
3. 重定向后浏览器上的URL发生变化。

说明：重定向是一种服务器指导客户端发出请求的行为，客户端（浏览器）根据服务器的指导，对服务器指导目标发送了二次请求。
