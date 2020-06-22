---
title: SpringMVC
date: 2020-06-22T18:44:54+08:00
categories:
- java
tags:
- Spring
- 技术文档
---

# SpringMVC

本文是我学习SpringMVC是的笔记，资源来自互联网。
<!-- more -->

 ## springMVC中的 ModelAndView

 - **ModelAndView构造方法可以指定返回的页面名称，也可以通过setViewName()方法跳转到指定的页面 ,**
 - 构造ModelAndView对象当控制器处理完请求时，通常会将包含视图名称或视图对象以及一些模型属性的ModelAndView对象返回到DispatcherServlet。因此，经常需要在控制器中构造ModelAndView对象。
 - ModelAndView类提供了几个重载的构造器和一些方便的方法，让你可以根据自己的喜好来构造ModelAndView对象。这些构造器和方法以类似的方式支持视图名称和视图对象。
 - **通过ModelAndView构造方法可以指定返回的页面名称，也可以通过setViewName()方法跳转到指定的页面 , 使用addObject()设置需要返回的值，addObject()有几个不同参数的方法，可以默认和指定返回对象的名字**。

 ### ModelAndView的构造方法

 - ModelAndView的第一种用法，先创建ModelAndView对象，再通过它的方法去设置数据与转发的视图名

   - setViewName(String viewName)：‎设置此 ModelAndView 的视图名称, 由 DispatcherServlet 通过 ViewResolver 解析‎
   - addObject(String attributeName, Object attributeValue)：通过key/value的方式绑定数据

   ```java
   // SpringMVC的控制器(业务控制器)
   // 定义的方法就是一个请求处理的方法
   @Controller
   @RequestMapping("/user")
   public class TestController {
       // 利用ModelAndView来转发数据,给前端视图
       // @return
       @RequestMapping("/m06")
       public ModelAndView m06() {
           ModelAndView modelAndView = new ModelAndView();
           modelAndView.setViewName("m06");
           modelAndView.addObject("message", "Hello World, Hello Kitty");
           return modelAndView;
       }
   ```

 - ModelAndView的第二种方法，可以直接通过带有参数的构造方法 ModelAndView(String viewName, String attributeName, Object attributeValue) 来返回数据与转发的视图名

   ```java
   // SpringMVC的控制器(业务控制器)
   // 定义的方法就是一个请求处理的方法
   @Controller
   @RequestMapping("/user")
   public class TestController {
       
   	// 利用ModelAndView来转发数据,给前端视图
       // @return
       @RequestMapping("/m07")
       public ModelAndView m07() {
           return new ModelAndView("m07", "message", "Hello World");
       }
       
   }
   ```

   

 - ModelAndView的第三种用法，设置重定向

   ```java
   // SpringMVC的控制器(业务控制器)
   // 定义的方法就是一个请求处理的方法
   @Controller
   @RequestMapping("/user")
   public class TestController {
       
       // ModelAndView默认转发
       // ModelAndView还是可以设置重定向
       // 1. 重定向另一个控制器
       // 2. 重定向具体的jsp页面
       // @param name
       // @return
       @RequestMapping("/{name}/m07")
       public ModelAndView m07(@PathVariable String name) {
           if (!"admin".equals(name)) {
               return new ModelAndView("redirect:/m07.jsp");
           }
           return new ModelAndView("m07");
       }
       
   }
   ```

 ### ModelAndView使用实例

 - 要点：

 1. @RequestMapping 注解的使用

 2. modelandview 的使用

 3. jsp页面request作用域的取值

 4. 视图解析器配置
    	

 ```java
 // ModelAndView 使用代码
 package com.dgr.controller;
 
 import org.springframework.stereotype.Controller;
 import org.springframework.web.bind.annotation.RequestMapping;
 import org.springframework.web.servlet.ModelAndView;
 
 @RequestMapping("mvc")
 @Controller
 public class TestRequestMMapping {
 @RequestMapping(value="/testModelAndView")
     public ModelAndView testModelAndView(){
         ModelAndView mav = new ModelAndView();
         mav.setViewName("hello");//跳转新的页面名称
         mav.addObject("address", "中国广东省广州市");//传入request作用域参数
         return mav;
     }
 }
 ```



 跳转前jsp页面链接设置

 ```jsp
 <a href="mvc/testModelAndView">Test ModelAndView</a>
 ```

 跳转后jsp页面以及request作用于取值

 ```jsp
 <body>
 	<h1>ModelAndView 跳转</h1>
     
     <br>
     ${requestScope.address}   
     <br>
     ${address }    
     <br>	
     
 </body>
 ```

视图解析器配置

![img](SpringMVC/20180817135644542)

![img](SpringMVC/20180817135701102)