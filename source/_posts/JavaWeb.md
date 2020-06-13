---
title: JavaWeb
date: 2020-06-10 20:07:43
categories:
- JavaWeb
tags:
- java
- 后端
- 技术文档
---

本文是我学习JavaWeb时的笔记，大部分内容来自廖雪峰的Java教程和java3y的公众号。

<!-- more -->

# JavaWeb

## 一、web应用服务器

`tomcat`、`Jboos`、`Weblogic`、`jetty`

### 1、Tomcat

- Tomcat简单的说就是一个运行JAVA的网络服务器，底层是`Socket`的一个程序，它也是`JSP`和`Serlvet`的一个容器。

### 2、使用指南

#### 		1、官网下载tomcat zip

#### 		2、解压

​		`bin`：存放各个平台下启动和停止`Tomcat`服务的脚本文件

​		`conf`：存放各种`Tomcat`服务器的配置文件

​		`	lib`：第三方`jar`包

​		`		logs`：`Tomcat`服务的日志信息

​		`	temp`：`Tomcat`运行时的临时文件

​		`webapps`：存放允许客户端访问的资源（**java**程序）

​		`work`：存放`Tomcat`将`jsp`转换成的`servlet`文件

#### 		3、idea新建企业java webapp

#### 4、tomcat控制台输出：

- 需要动态查看tomcat控制台输出，在logs目录下执行：`tail -f catalina.out`

### 3、url的组成

| http:// |  localhost:  | 8080/ |  web1/  | Hello.html |
| :-----: | :----------: | :---: | :-----: | :--------: |
|  协议   | 主机（域名） | 端口  | web应用 |  资源文件  |

其中，web应用和资源文件统称uri

### 4、虚拟目录

- 如果把所有web站点的目录都放在webapps下，可能导致**磁盘空间不够用**，也**不利于对web站点目录的管理**【如果存在非常多的web站点目录】把**web站点的目录分散到其他磁盘管理就需要配置虚拟目录【默认情况下，只有webapps下的目录才能被Tomcat自动管理成一个web站点】**把web应用所在目录交给web服务器管理，这个过程称之为虚拟目录的映射

- 步骤：

  - 在其他盘符下创建一个web站点目录，并创建WEB-INF目录和一个html文件。

  - 找到Tomcat目录下/conf/server.xml文件

  - 在server.xml中的节点下添加如下代码。**path表示的是访问时输入的web项目名，docBase表示的是站点目录的绝对路径**

    ```xml
    <Context path="/web1" docBase="D:\web1"/>
    ```

- 方法2:

  - 进入到conf\Catalina\localhost文件下，创建一个xml文件，**该文件的名字就是web应用的名字。**

  - xml文件的代码如下，**docBase是你web站点的绝对路径**

    ```xml
    <?xml version="1.0" encoding="UTF-8"?>
    <Context docBase="D:\web1" reloadable="true"></Context>
    ```

  - 访问web站点下的html资源 

### 5、配置临时域名

- 在hosts文件下配置临时域名

### 6、虚拟主机

- 为什么需要用到虚拟主机？例子：我现在开发了4个网站，有4个域名。如果我不配置虚拟主机，一个Tomcat服务器运行一个网站，我就需要4台电脑才能把4个网站运行起来。

- 步骤：

  - 在tomcat的server.xml文件中添加主机名

    ```xml
    <Host name="panda" appBase="D:\web1">
    <Context path="/web1" docBase="D:\web1"/>
    </Host>
    ```

  - 访问虚拟主机下的web站: panda:8080/web1

## 二、HTTP协议

> 超文本传输协议（HTTP，HyperText Transfer Protocol)是互联网上应用最为广泛的一种网络协议。所有的WWW文件都必须遵守这个标准。它是TCP/IP协议的一个**应用层协议**

- **HTTP协议就是客户端和服务器交互的一种通迅的格式**。
- 当在浏览器中点击某个链接的时候，**浏览器会向服务器发送一段文本**，**告诉服务器请求打开的是哪一个网页。服务器收到请求后，就返回一段文本给浏览器，浏览器会将该文本解析，然后显示出来。**这段文本就是遵循HTTP协议规范的。

### 1、HTTP1.0和HTTP1.1的区别

- HTTP1.0协议中，客户端与web服务器建立连接后，只能获得一个web资源【短连接，获取资源后就断开连接】
- HTTP1.1协议，允许客户端与web服务器建立连接后，在一个连接上获取多个web资源【保持连接】

### 2、http请求

- **浏览器向服务器请求某个web资源时，称之为浏览器向服务器发送了一个http请求。**一个完整http请求应该包含三个部分：

  1. 请求行【描述客户端的**请求方式**、**请求的资源名称**，以及使用的**HTTP协议版本号**】
  2. 多个消息头【描述客户端请求哪台主机，以及**客户端的一些环境信息**等】
  3. 一个空行

- 请求行：GET /java.html HTTP/1.1

  - **请求行中的GET称之为请求方式**，请求方式有：POST,GET,HEAD,OPTIONS,DELETE,TRACE,PUT。**常用的有：POST,GET**。

    一般来说，当我们**点击超链接，通过地址栏访问都是get请求方式**。通过**表单提交的数据一般是post方式**。

    可以简单理解**GET方式用来查询数据**,**POST方式用来提交数据**，**get的提交速度比post快**。

    GET方式：在URL地址后**附带的参数是有限制的**，其**数据容量通常不能超过1K**。

    POST方式：可以在**请求的实体内容中向服务器发送数据**，**传送的数据量无限制**。

- **请求头**

  - Accept: text/html,image/* 【浏览器告诉服务器，它支持的数据类型】
  - Accept-Charset: ISO-8859-1 【浏览器告诉服务器，它支持哪种**字符集**】
  - Accept-Encoding: gzip,compress 【浏览器告诉服务器，它支持的**压缩格式**】
  - Accept-Language: en-us,zh-cn 【浏览器告诉服务器，它的语言环境】
  - Host: www.it315.org:80【浏览器告诉服务器，它的想访问哪台主机】
  - If-Modified-Since: Tue, 11 Jul 2000 18:23:51 GMT【浏览器告诉服务器，缓存数据的时间】
  - Referer: http://www.it315.org/index.jsp【浏览器告诉服务器，客户机是从那个页面来的---**反盗链**】
  - 8.User-Agent: Mozilla/4.0 (compatible; MSIE 5.5; Windows NT 5.0)【浏览器告诉服务器，浏览器的内核是什么】
  - Cookie【浏览器告诉服务器，**带来的Cookie是什么**】
  - Connection: close/Keep-Alive 【浏览器告诉服务器，请求完后是断开链接还是保持链接】
  - Date: Tue, 11 Jul 2000 18:23:51 GMT【浏览器告诉服务器，请求的时间】

### 3、HTTP响应

- 一个HTTP响应代表着**服务器向浏览器回送数据**。一个完整的HTTP响应应该包含四个部分:
  1. 一个状态行【用于描述**服务器对请求的处理结果。**】
  2. 多个消息头【用于描述**服务器的基本信息**，以及**数据的描述**，**服务器通过这些数据的描述信息，可以通知客户端如何处理等一会儿它回送的数据**】
  3. 一个空行
  4. 实体内容【**服务器向客户端回送的数据**】
  
- 状态行：

  - 格式： HTTP版本号　状态码　原因叙述

  - 状态行：HTTP/1.1 200 OK

  - 状态码用于表示**服务器对请求的处理结果**，它是一个**三位的十进制数**。响应状态码分为5类

    | 状态码   | 含义                                                         |
    | -------- | :----------------------------------------------------------- |
    | 100～199 | 表示成功接收请求，要求客户端继续提交下一次请求才能完成整个处理过程 |
    | 200～299 | 表示成功接收到请求并已完成整个处理过程，常用200              |
    | 300～399 | 为完成请求，客户需进一步细化请求。例如：请求的资源已移动到一个新地址，常用302、307和304 |
    | 400～499 | 客户端的请求有错误，常用404                                  |
    | 500～599 | 服务器端出现错误，常用500                                    |

- 响应头

  - Location: http://www.it315.org/index.jsp 【服务器告诉浏览器**要跳转到哪个页面**】
  - Server:apache tomcat【服务器告诉浏览器，服务器的型号是什么】
  - Content-Encoding: gzip 【服务器告诉浏览器**数据压缩的格式**】
  - Content-Length: 80 【服务器告诉浏览器回送数据的长度】
  - Content-Language: zh-cn 【服务器告诉浏览器，服务器的语言环境】
  - Content-Type: text/html; charset=GB2312 【服务器告诉浏览器，**回送数据的类型**】
  - Last-Modified: Tue, 11 Jul 2000 18:23:51 GMT【服务器告诉浏览器该资源上次更新时间】
  - Refresh: 1;url=http://www.it315.org【服务器告诉浏览器要**定时刷新**】
  - Content-Disposition: attachment; filename=aaa.zip【服务器告诉浏览器**以下载方式打开数据**】
  - Transfer-Encoding: chunked 【服务器告诉浏览器数据以分块方式回送】
  - Set-Cookie:SS=Q0=5Lb_nQ; path=/search【服务器告诉浏览器要**保存Cookie**】
  - Expires: -1【服务器告诉浏览器**不要设置缓存**】
  - Cache-Control: no-cache 【服务器告诉浏览器**不要设置缓存**】
  - Pragma: no-cache 【服务器告诉浏览器**不要设置缓存**】
  - Connection: close/Keep-Alive 【服务器告诉浏览器连接方式】
  - Date: Tue, 11 Jul 2000 18:23:51 GMT【服务器告诉浏览器回送数据的时间】

## 三、Servlet和JSP

### 1、什么是Servlet

- `Servlet`是JavaWeb开发的基石，一种与平台无关的服务器组件
- 它是运行在`Servlet`容器/Web应用服务器，负责与客户端进行通信
- 是一个**遵循Servlet开发的java类**。Serlvet是**由服务器调用的**，**运行在服务器端**。

### 2、Servlet的功能

- **处理浏览器带来HTTP请求，并返回一个响应给浏览器，从而实现浏览器和服务器的交互**。

- 创建并返回基于客户端请求的动态HTML页面
- 与数据库进行通信

### 3、JavaWeb目录结构

![JavaWeb](https://raw.githubusercontent.com/920408194/pic/master/img/JavaWeb.png)

- bbs目录代表一个web应用
- bbs目录下的html,jsp文件可以直接被浏览器访问
- **WEB-INF目录下的资源是不能直接被浏览器访问的**
- web.xml文件是web程序的主要配置文件
- 所有的classes文件都放在classes目录下
- jar文件放在lib目录下

### 4、使用Servlet

- `Servlet`本身是一组接口，在`javax.servlet`包里面。

#### 1、基础配置

- 自定义一个类，并实现`Sevlet`接口，这个类就具备了接受客户端请求以及做出响应的功能。

  - 我们发现有5个方法需要重写，有init【初始化】，destroy【销毁】,service【服务】,ServletConfig【Servlet配置】,getServletInfo【Serlvet信息】。、

  - 其中，service()方法是写逻辑代码的地方。调用ServletResponse对象的方法向浏览器输出

    ```java
    @Override
        public void service(ServletRequest servletRequest, ServletResponse servletResponse) throws ServletException, IOException {
            servletResponse.getWriter().write("This is panda's server!");
        }
    ```

  - 配置xml文件

    ```xml
     <web-app ...>
     <servlet>
            <servlet-name>MyServlet</servlet-name>
            <servlet-class>MyServlet</servlet-class>
        </servlet>
        <servlet-mapping>
            <servlet-name>MyServlet</servlet-name>
            <url-pattern>/MyServlet</url-pattern>
        </servlet-mapping>
    </web-app>
    
    ```

    或者用注解：

    ```java
    @WebServlet(value = "/MyServlet")
    public class MyServlet implements Servlet {...}
    ```

    

  - 访问：url/MyServlet

#### 2、Servlet生命周期

- **加载Servlet**。当Tomcat第一次访问Servlet的时候，**Tomcat会负责创建Servlet的实例**
- **初始化**。当Servlet被实例化后，Tomcat会**调用init()方法初始化这个对象**
- **处理服务**。当浏览器**访问Servlet**的时候，Servlet **会调用service()方法处理请求**
- **销毁**。当Tomcat关闭时或者检测到Servlet要从Tomcat删除的时候会自动调用destroy()方法，**让该实例释放掉所占的资源**。一个Servlet如果长时间不被使用的话，也会被Tomcat自动销毁
- **卸载**。当Servlet调用完destroy()方法后，等待垃圾回收。如果**有需要再次使用这个Servlet，会重新调用init()方法进行初始化操作**。

简单总结：**只要访问Servlet，service()就会被调用。init()只有第一次访问Servlet的时候才会被调用。 destroy()只有在Tomcat关闭的时候才会被调用。**

#### 3、Servlet补充细节

- **同一个Servlet可以被映射到多个URL上。**(只能写在xml里面)

- Servlet映射的URL可以使用通配符：\*.扩展名、正斜杠（/）开头并以“/*”结尾。

  如果.扩展名和正斜杠（/）开头并以“/”结尾两种通配符同时出现:

  1. **看谁的匹配度高，谁就被选择**
  2. `*.`扩展名的优先级最低

- Servlet映射的URL可以使用通配符和Servlet可以被映射到多个URL上的作用：

  1. **隐藏网站是用什么编程语言写的**【.php,.net,.asp实际上访问的都是同一个资源】
  2. 用特定的**后缀声明版权**【公司缩写】

- Servlet是单例的：**浏览器多次对Servlet的请求**，一般情况下，**服务器只创建一个Servlet对象**，也就是说，Servlet对象**一旦创建了**，就会**驻留在内存中，为后续的请求做服务，直到服务器关闭**。

  - 对于**每次访问请求**，Servlet引擎都会**创建一个新的HttpServletRequest请求对象和一个新的HttpServletResponse响应对象**，然后将这两**个对象作为参数传递给它调用的Servlet的service()方法**，**service方法再根据请求方式分别调用doXXX方法**。所以每次访问请求对象和响应对象都是新的

- load-on-startup：如果配置了一个load-on-startup，那么**WEB应用程序在启动时**，就会**装载并创建Servlet的实例对象**、以及**调用Servlet实例对象的init()方法**。

  ```xml
  <servlet>
          <servlet-name>MyServlet</servlet-name>
          <servlet-class>MyServlet</servlet-class>
          <load-on-startup>1</load-on-startup>
      </servlet>
  ```

  或

  ```java
  @javax.servlet.annotation.WebServlet(value = "/MyServlet",loadOnStartup = 1)
  ```

- 

#### 4、线程安全问题

​	当多个用户访问Servlet的时候，**服务器会为每个用户创建一个线程**。**当多个用户并发访问Servlet共享资源的时候就会出现线程安全问题**。

- 如果一个**变量需要多个用户共享**，则应当在访问该变量的时候，**加同步机制synchronized (对象){}**
- 如果一个变量**不需要共享**，则**直接在 doGet() 或者 doPost()定义**.这样不会存在线程安全问题

#### 5、在web访问任何资源都是访问Servlet

- 凡是在web.xml文件中**找不到匹配的元素的URL**，它们的访问**请求都将交给缺省Servlet处理**，也就是说，**缺省Servlet用于处理所有其他Servlet都不处理的访问请求**
- 访问web上的任何字资源实质上都是在访问这个缺省的Servlet。没有手工配置缺省Servlet的时候，**你访问静态图片，静态网页，缺省Servlet会在你web站点中寻找该图片或网页**，如果有就返回给浏览器，没有就报404错误

#### 6、ServletConfig对象

- 通过此对象可以读取web.xml中配置的初始化参数。

- xml写法

  ```xml
  <servlet>
          <servlet-name>MyServlet</servlet-name>
          <servlet-class>MyServlet</servlet-class>
          <init-param>
              <param-name>name</param-name>
              <param-value>panda</param-value>
          </init-param>
          <load-on-startup>1</load-on-startup>
      </servlet>
  ```

- 注解写法

  ```java
  @WebServlet(
          name = "ServletByIdea",
          value = "/ServletByIdea",
          loadOnStartup = 1,
          initParams = {
              @WebInitParam(name="name",value = "panda")
          }
  )
  ```

- `getServletConfig()`的实现：需要在`init()`中得到`ServletConfig`，然后在方法中返回

  ```java
  public class MyServlet implements Servlet {
      private ServletConfig servletConfig;
    
      @Override
      public void init(ServletConfig servletConfig) throws ServletException {
          this.servletConfig = servletConfig;
      }
  
      @Override
      public ServletConfig getServletConfig() {
          return servletConfig;
      }
  }
  ```

#### 7、ServletContext对象

- 当Tomcat启动的时候，就会创建一个ServletContext对象。它**代表着当前web站点**

- ServletContext有什么用？

  1. ServletContext既然代表着当前web站点，那么**所有Servlet都共享着一个ServletContext对象**，所以**Servlet之间可以通过ServletContext实现通讯**。
  2. ServletConfig获取的是配置的是单个Servlet的参数信息，**ServletContext可以获取的是配置整个web站点的参数信息**
  3. **利用ServletContext读取web站点的资源文件**
  4. 实现Servlet的转发【用ServletContext转发不多，主要用request转发】

- ServletContext对象可以被称之为**域对象**。可以简单理解成**一个容器【类似于Map集合】**。实现Servlet之间通讯就要**用到ServletContext的setAttribute(String name,Object obj)方法**， 第一个参数是关键字，第二个参数是你要存储的对象。

- Servlet代码

  ```java
  //获取ServletContext对象并存值到域对象
  ServletContext servletContext = this.getServletContext();
  String value = "panda!";
  servletContext.setAttribute("MyName",value);
  
  //获取其他Servlet存储的信息，实现多Servlet通信
  ServletContext servletContext = this.getServletContext();
  String value = (String) servletContext.getAttribute("MyName");
  ```

  注意：要先执行存值的servlet！！！！

- `getServletContext()`实现

  ```java
  public ServletContext getServletContext() {
  		return getServletConfig().getServletContext();
  }
  ```

#### 8、获取web站点配置的信息

- web.xml文件支持**对整个站点进行配置参数信息**【**所有Servlet都可以取到该参数信息**】

  ```xml
  <context-param>
     <param-name>usr</param-name>
     <param-value>panda</param-value>
  </context-param>
  ```

#### 9、读取资源文件

- 通过**ServletContext读取**（文件在包目录/img下）

  <img src="https://raw.githubusercontent.com/920408194/pic/master/img/xxximg.png" alt="xxximg" style="zoom: 50%;" />

  ```java
  //读取文件/图片
  String path = "/WEB-INF/classes/img/xxx.jpg";
  InputStream inputStream = servletContext.getResourceAsStream(path);//java.io.ByteArrayInputStream@64bcce8a
  response.getWriter().write(inputStream+"\n");
  ```

- 文件在web目录下

  <img src="https://raw.githubusercontent.com/920408194/pic/master/img/xxximg2.png" style="zoom:50%;" />

  ```java
  //从web目录读取文件/图片
  InputStream inputStream2 = servletContext.getResourceAsStream("xxx.jpg");
  response.getWriter().write(inputStream2+"\n");
  ```

- 通过**类装载器读取资源文件**。

  ![](https://raw.githubusercontent.com/920408194/pic/master/img/xxximg3.png)

  ```java
  //通过类装载器读取资源文件。
  InputStream inputStream3 = servletContext.getResourceAsStream("xxx.jpg");
  response.getWriter().write(inputStream3+"\n");
  ```

- **如果文件太大，就不能用类装载器的方式去读取，会导致内存溢出**

- 

#### 10、继承HttpServlet编写Servlet程序

在上面我们实现Servlet接口，要实现5个方法。这样太麻烦了！而**HttpServlet类已经实现了Servlet接口的所有方法**，编写Servlet时，只需要**继承HttpServlet，重写你需要的方法即可**，并且它在原有Servlet接口上**添加了一些与HTTP协议处理方法**，它**比Servlet接口的功能更为强大**。

- 一般我们开发的时候，都是**重写doGet()和doPost()方法的**。对于idea而言，创建Servlet的时候已经帮你重写好了

### 5、request和response

#### 1、response、request对象

- Tomcat收到客户端的http请求，会针对每一次请求，分别创建一个**代表请求的request对象**、和**代表响应的response对象**
- 我们**获取浏览器提交过来的数据，找request对象**即可；我们**向浏览器输出数据，找response对象**即可。

#### 2、HttpServletResponse对象

- **HttpServletResponse对象封装了http响应的信息。**http响应由状态行、实体内容、消息头、一个空行组成。

- 调用getOutputStream()方法向浏览器输出数据,**getOutputStream()方法可以使用print()也可以使用write()**

  ```java
  //调用getOutputStream()方法向浏览器输出数据
  ServletOutputStream servletOutputStream = response.getOutputStream();
  servletOutputStream.println("aaa");//输出中文可能出现乱码
  servletOutputStream.write("bbb".getBytes());
  ```

  但是，程序**要实现通用性，应该使用的是UTF-8编码**。

  ```java
  servletOutputStream.write("ccc".getBytes("UTF-8"));
  ```

- 但是浏览器编码不为utf-8时，又会出现乱码。于是乎**设置消息头**

  ```java
  //设置头信息，告诉浏览器我回送的数据编码是utf-8的
  response.setHeader("Content-Type", "text/html;charset=UTF-8");
  ```

  除了使用HttpServletResponse对象设置消息头的方法，我可以**使用html的标签模拟一个http消息头**

  ```java
  //使用meta标签模拟http消息头，告诉浏览器回送数据的编码和格式
  servletOutputStream.
  write("<meta http-equiv='content-type' content='text/html;charset=UTF-8'>".getBytes());
  ```

#### 3、调用getWriter()方法向浏览器输出数据

- 对于getWriter()方法而言，是Writer的子类，那么**只能向浏览器输出字符数据，不能输出二进制数据**

  ```java
  //获取到printWriter对象
  PrintWriter printWriter = response.getWriter();
  printWriter.write("看完博客点赞！");
  ```

- 

## 四、JDBC

`Java DataBase Connectivity`是一个独立与特定数据库的管理系统，通用的SQL数据存取和操作的公共接口，定义了一组标准，未访问不同数据库提供了统一的途径。

### 1、JDBC体系结构

- JDBC的接口包含两个层面：

  - 面向应用的api，供开发者调用；
  - 面向数据库的api，供厂商开发数据库驱动；

- 提供者：java官方；内容：供开发者调用的接口；

- `java.sql`和`javax.sql`：`DriverManager`类、`Connection`接口、`Statement`接口、`ResultSet`接口

- `DriverManager`

  - 提供者：java官方
  - 作用：管理不同的JDBC驱动

- JDBC驱动

- - 提供者：各数据库厂商
  - 作用：负责连接数据库

### 2、JDBC的使用

- 加载数据库驱动，Java程序和数据库之间的桥梁

- 获取`Connection`，Java程序与数据库一次连接

- 创建`Statement`对象，由`Connection`产生，执行SQL语句

- 如果需要接收返回值，创建`Result`对象，保存`Statement`执行之后所查询到的结果

  ```java
  package com.panda.tomcat0314;
  
  import java.sql.*;
  import java.util.Date;
  
  /**
   * @author panda
   * @description
   * @date 2020/3/14
   */
  public class Test {
      public static void main(String[] args) {
          //try catch处理异常
          try {
              //反射加载驱动
              Class.forName("com.mysql.cj.jdbc.Driver");
              //获取连接地址,并处理中文乱码
              String url = "jdbc:mysql://localhost:3306/test?useUnicode=true&characterEncoding=UTF-8";
              //用户信息
              String usr = "root";
              String password = "panyuwen";
              //新建connection连接
              Connection connection = DriverManager.getConnection(url,usr,password);
              //获取statement并执行语句
              Statement statement = connection.createStatement();
              //String sql = "insert into foods(title,owner,price,date,sold,image) values('可乐','panda',2.5,'2020-01-01',0,'n')";
              //statement.executeUpdate(sql);
              String sql = "select * from foods";
              ResultSet resultSet =  statement.executeQuery(sql);
              while (resultSet.next()){
                  int id  = resultSet.getInt("id");
                  String title = resultSet.getString("title");
                  String owner = resultSet.getString("owner");
                  double price = resultSet.getDouble("price");
                  Date date = resultSet.getDate("date");
                  int sold = resultSet.getInt("sold");
                  String image = resultSet.getString("image");
                  System.out.println(title);
  
              }
          } catch (ClassNotFoundException | SQLException e) {
              e.printStackTrace();
          }
      }
  }
  ```

