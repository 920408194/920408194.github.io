---
title: SpringMVC
date: 2020-06-22T18:44:54+08:00
categories:
- java
tags:
- Spring
- 技术文档
---

本文是我学习SpringMVC是的笔记，资源来自互联网。
<!-- more -->

# SpringMVC 

### @RequestMapping和@RequestParam

#### @RequestMapping 

- **@RequestMapping** 是 Spring Web 应用程序中最常被用到的注解之一。这个注解会将 HTTP 请求映射到 MVC 和 REST 控制器的处理方法上。

- 要配置 Web 请求的映射，就需要你用上 @RequestMapping 注解。 

- @RequestMapping 注解可以在控制器类的级别和/或其中的方法的级别上使用。  在类的级别上的注解会将一个特定请求或者请求模式映射到一个控制器之上。之后你还可以另外添加方法级别的注解来进一步指定到处理方法的映射关系。 

- 你可以将多个请求映射到一个方法上去，只需要添加一个带有请求路径值列表的 @RequestMapping 注解就行了。 

  ```java
  @RestController  
  @RequestMapping("/home")  
  public class IndexController {  
    
      @RequestMapping(value = {  
          "",  
          "/page",  
          "page*",  
          "view/*,**/msg"  
      })  
      String indexMultipleMapping() {  
          return "Hello from index multiple mapping.";  
      }  
  }  
  ```

- @RequestMapping 支持统配符以及ANT风格的路径。前面这段代码中，如下的这些 URL 都会由 indexMultipleMapping() 来处理： 

  localhost:8080/home
  localhost:8080/home/
  localhost:8080/home/page
  localhost:8080/home/pageabc
  localhost:8080/home/view/
  localhost:8080/home/view/view

#### **带有 @RequestParam 的 @RequestMapping** 

- @RequestParam 注解配合 @RequestMapping 一起使用，可以将请求的参数同处理方法的参数绑定在一起。 

- @RequestParam 注解使用的时候可以有一个值，也可以没有值。这个值指定了需要被映射到处理方法参数的请求参数, 代码如下所示： 

  ```java
  @RestController  
  @RequestMapping("/home")  
  public class IndexController {  
    
      @RequestMapping(value = "/id")  
      //id 这个请求参数被映射到了 thegetIdByValue() 这个处理方法的参数 personId 上
      String getIdByValue(@RequestParam("id") String personId) {  
          System.out.println("ID is " + personId);  
          return "Get ID from query string of URL with value element";  
      }  
      @RequestMapping(value = "/personId")  
      //如果请求参数和处理方法参数的名称一样的话，@RequestParam 注解的 value 这个参数就可省掉了
      String getId(@RequestParam String personId) {  
          System.out.println("ID is " + personId);  
          return "Get ID from query string of URL without value element";  
      }  
  }  
  ```

- @RequestParam 注解的 required 这个参数定义了参数值是否是必须要传的。

  ```java
  @RestController  
  @RequestMapping("/home")  
  public class IndexController {  
      @RequestMapping(value = "/name")  
      String getName(@RequestParam(value = "person", required = false) String personName) {  
          return "Required element of request param";  
      }  
  }  
  ```

  在这段代码中，因为 required 被指定为 false，所以 getName() 处理方法对于如下两个 URL 都会进行处理： 

  - /home/name?person=xyz
  - /home/name

- @RequestParam 的 defaultValue 取值就是用来给取值为空的请求参数提供一个默认值的。 

  ```java
  @RestController  
  @RequestMapping("/home")  
  public class IndexController {  
      @RequestMapping(value = "/name")  
      String getName(@RequestParam(value = "person", defaultValue = "John") String personName) {  
          return "Required element of request param";  
      }  
  }  
  ```

  在这段代码中，如果 person 这个请求参数为空，那么 getName() 处理方法就会接收 John 这个默认值作为其参数。 

#### **用 @RequestMapping 处理 HTTP 的各种方法** 

- Spring MVC 的 @RequestMapping 注解能够处理 HTTP 请求的方法, 比如 GET, PUT, POST, DELETE 以及 PATCH。 

- 所有的请求默认都会是 HTTP GET 类型的。 

- 为了能将一个请求映射到一个特定的 HTTP 方法，你需要在 @RequestMapping 中使用 method 来声明 HTTP 请求所使用的方法类型。

  ```java
  @RestController  
  @RequestMapping("/home")  
  public class IndexController {  
      @RequestMapping(method = RequestMethod.GET)  
      String get() {  
          return "Hello from get";  
      }  
      @RequestMapping(method = RequestMethod.DELETE)  
      String delete() {  
          return "Hello from delete";  
      }  
      @RequestMapping(method = RequestMethod.POST)  
      String post() {  
          return "Hello from post";  
      }  
      @RequestMapping(method = RequestMethod.PUT)  
      String put() {  
          return "Hello from put";  
      }  
      @RequestMapping(method = RequestMethod.PATCH)  
      String patch() {  
          return "Hello from patch";  
      }  
  }  
  ```

  在上述这段代码中， @RequestMapping 注解中的 method 元素声明了 HTTP 请求的 HTTP 方法的类型。 

  所有的处理处理方法会处理从这同一个 URL( /home)进来的请求, 但要看指定的 HTTP 方法是什么来决定用哪个方法来处理。

#### **用 @RequestMapping 来处理生产和消费对象** 

- 可以使用 @RequestMapping 注解的 produces 和 consumes 这两个元素来**缩小请求映射类型的范围**。 为了能用请求的媒体类型来产生对象, 你要用到 @RequestMapping 的 produces 元素再结合着 @ResponseBody 注解。 你也可以利用 @RequestMapping 的 comsumes 元素再结合着 @RequestBody 注解用请求的媒体类型来消费对象。 

  ```java
  @RestController  
  @RequestMapping("/home")  
  public class IndexController {  
      @RequestMapping(value = "/prod", produces = {  
          "application/JSON"  
      })  
      @ResponseBody  
      String getProduces() {  
          return "Produces attribute";  
      }  
    
      @RequestMapping(value = "/cons", consumes = {  
          "application/JSON",  
          "application/XML"  
      })  
      String getConsumes() {  
          return "Consumes attribute";  
      }  
  }  
  ```

  在这段代码中， getProduces() 处理方法会产生一个 JSON 响应， getConsumes() 处理方法可以同时处理请求中的 JSON 和 XML 内容。 

- **使用 @RequestMapping 来处理消息头。** @RequestMapping 注解提供了一个 header 元素来根据请求中的消息头内容缩小请求映射的范围。 在可以指定 header 元素的值，用 myHeader = myValue 这样的格式： 

  ```java
  @RestController  
  @RequestMapping("/home")  
  public class IndexController {  
      @RequestMapping(value = "/head", headers = {  
          "content-type=text/plain"  
      })  
      String post() {  
          return "Mapping applied along with headers";  
      }  
  }  
  ```

  在上面这段代码中， @RequestMapping 注解的 headers 属性将映射范围缩小到了 post() 方法。有了这个，post() 方法就只会处理到 /home/head 并且 content-typeheader 被指定为 text/plain 这个值的请求。

- 你也可以像下面这样指定多个消息头： 

  ```java
  @RestController  
  @RequestMapping("/home")  
  public class IndexController {  
      @RequestMapping(value = "/head", headers = {  
          "content-type=text/plain",  
          "content-type=text/html"  
      }) String post() {  
          return "Mapping applied along with headers";  
      }  
  }  
  ```

  这样， post() 方法就能同时接受 text/plain 还有 text/html 的请求了。 

#### **使用 @RequestMapping 来处理请求参数** 

- @RequestMapping 直接的 params 元素可以进一步帮助我们缩小请求映射的定位范围。使用 params 元素，你可以让多个处理方法处理到同一个URL 的请求, 而这些请求的参数是不一样的。 你可以用 myParams = myValue 这种格式来定义参数，也可以使用通配符来指定特定的参数值在请求中是不受支持的。 

  ```java
  @RestController  
  @RequestMapping("/home")  
  public class IndexController {  
      @RequestMapping(value = "/fetch", params = {  
          "personId=10"  
      })  
      String getParams(@RequestParam("personId") String id) {  
          return "Fetched parameter using params attribute = " + id;  
      }  
      @RequestMapping(value = "/fetch", params = {  
          "personId=20"  
      })  
      String getParamsDifferent(@RequestParam("personId") String id) {  
          return "Fetched parameter using params attribute = " + id;  
      }  
  }  
  ```

  在这段代码中，getParams() 和 getParamsDifferent() 两个方法都能处理相同的一个 URL (/home/fetch) ，但是会根据 params 元素的配置不同而决定具体来执行哪一个方法。 

  例如，当 URL 是 /home/fetch?id=10 的时候, getParams() 会执行，因为 id 的值是10,。对于 localhost:8080/home/fetch?personId=20 这个URL, getParamsDifferent() 处理方法会得到执行，因为 id 值是 20。 

#### **使用 @RequestMapping 处理动态 URI** 

- @RequestMapping 注解可以同 @PathVaraible 注解一起使用，用来处理动态的 URI，URI 的值可以作为控制器中处理方法的参数。你也可以使用正则表达式来只处理可以匹配到正则表达式的动态 URI。 

  ```java
  @RestController  
  @RequestMapping("/home")  
  public class IndexController {  
      @RequestMapping(value = "/fetch/{id}", method = RequestMethod.GET)  
      String getDynamicUriValue(@PathVariable String id) {  
          System.out.println("ID is " + id);  
          return "Dynamic URI parameter fetched";  
      }  
      @RequestMapping(value = "/fetch/{id:[a-z]+}/{name}", method = RequestMethod.GET)  
      String getDynamicUriValueRegex(@PathVariable("name") String name) {  
          System.out.println("Name is " + name);  
          return "Dynamic URI parameter fetched using regex";  
      }  
  }  
  ```

  在这段代码中，方法 getDynamicUriValue() 会在发起到 localhost:8080/home/fetch/10 的请求时执行。这里 getDynamicUriValue() 方法 id 参数也会动态地被填充为 10 这个值。 方法 getDynamicUriValueRegex() 会在发起到 localhost:8080/home/fetch/category/shirt 的请求时执行。不过，如果发起的请求是 /home/fetch/10/shirt 的话，会抛出异常，因为这个URI并不能匹配正则表达式。 

- @PathVariable 同 @RequestParam的运行方式不同。你使用 @PathVariable 是为了从 URI 里取到查询参数值。换言之，你使用 @RequestParam 是为了从 URI 模板中获取参数值。 

#### **@RequestMapping 默认的处理方法** 

- 在控制器类中，你可以有一个默认的处理方法，它可以在有一个向默认 URI 发起的请求时被执行。

  ```java
  @RestController  
  @RequestMapping("/home")  
  public class IndexController {  
      @RequestMapping()  
      String  
      default () {  
          return "This is a default method for the class";  
      }  
  }  
  ```

  在这段代码中，向 /home 发起的一个请求将会由 default() 来处理，因为注解并没有指定任何值。

#### **@RequestMapping 快捷方式** 

- Spring 4.3 引入了方法级注解的变体，也被叫做 @RequestMapping 的组合注解。组合注解可以更好的表达被注解方法的语义。它们所扮演的角色就是针对 @RequestMapping 的封装，而且成了定义端点的标准方法。 

  例如，@GetMapping 是一个组合注解，它所扮演的是 @RequestMapping(method =RequestMethod.GET) 的一个快捷方式。 
  方法级别的注解变体有如下几个： 

  - @GetMapping
  - @PostMapping
  - @PutMapping
  - @DeleteMapping
  - @PatchMapping

  ```java
  @RestController  
  @RequestMapping("/home")  
  public class IndexController {  
      @GetMapping("/person")  
      public @ResponseBody ResponseEntity < String > getPerson() {  
          return new ResponseEntity < String > ("Response from GET", HttpStatus.OK);  
      }  
      @GetMapping("/person/{id}")  
      public @ResponseBody ResponseEntity < String > getPersonById(@PathVariable String id) {  
          return new ResponseEntity < String > ("Response from GET with id " + id, HttpStatus.OK);  
      }  
      @PostMapping("/person")  
      public @ResponseBody ResponseEntity < String > postPerson() {  
          return new ResponseEntity < String > ("Response from POST method", HttpStatus.OK);  
      }  
      @PutMapping("/person")  
      public @ResponseBody ResponseEntity < String > putPerson() {  
          return new ResponseEntity < String > ("Response from PUT method", HttpStatus.OK);  
      }  
      @DeleteMapping("/person")  
      public @ResponseBody ResponseEntity < String > deletePerson() {  
          return new ResponseEntity < String > ("Response from DELETE method", HttpStatus.OK);  
      }  
      @PatchMapping("/person")  
      public @ResponseBody ResponseEntity < String > patchPerson() {  
          return new ResponseEntity < String > ("Response from PATCH method", HttpStatus.OK);  
      }  
  }  
  ```

  在这段代码中，每一个处理方法都使用 @RequestMapping 的组合变体进行了注解。尽管每个变体都可以使用带有方法属性的 @RequestMapping 注解来互换实现, 但组合变体仍然是一种最佳的实践 — 这主要是因为组合注解减少了在应用程序上要配置的元数据，并且代码也更易读。 

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