---
title: SpringBoot
date: 2020-07-02T18:44:54+08:00
categories:
- Spring
tags:
- java
- 技术文档

---

本文是我学习SpringBoot是的笔记，资源来自互联网。
<!-- more -->

# SpringBoot

## 一、什么是SpringBoot

## 二、快速开始

### 1、从idea创建SpringBoot项目

1. 新建idea maven项目，不勾选依赖。

2. 在pom中加入以下依赖，作用见注释

   ```xml
           <!-- SpringBoot启动器  -->
           <dependency>
               <groupId>org.springframework.boot</groupId>
               <artifactId>spring-boot-starter</artifactId>
           </dependency>
   
           <!--Web依赖-->
           <dependency>
               <groupId>org.springframework.boot</groupId>
               <artifactId>spring-boot-starter-web</artifactId>
           </dependency>
   
   ```

3. 编写control类

   ```java
   @Controller
   @RequestMapping("/hello")
   public class HelloController {
       @ResponseBody
       @GetMapping("/hello")
       public String Hello(){
           return "Hello";
       }
   }
   ```

   ![image-20200702171450483](SpringBoot/image-20200702171450483.png)

   

4. 在application.yml中设置端口等信息

   ```yml
   #服务端容器的配置
   server:
     port: 8887
   ```

5. 点击application的run方法，启动服务。

## 2、使用JPA访问数据库

1. 导入JPA依赖

   ```xml
           <!-- MySQL的java驅動 -->
           <dependency>
               <groupId>mysql</groupId>
               <artifactId>mysql-connector-java</artifactId>
           </dependency>
           
           <!--spring data jpa-->
           <dependency>
               <groupId>org.springframework.boot</groupId>
               <artifactId>spring-boot-starter-data-jpa</artifactId>
           </dependency>
   ```

2. 跟据数据编写实体类

   ```java
   @Entity // jpa的注解，需要加
   @Data // lombok
   public class User implements Serializable{
       @Id
       private String id;
   
       private String name;
   
       private String password;
   
       private String email;
   }
   ```

3. 编写 Repository类

   ```java
   //参数为实体类类型和主键类型
   public interface UserRepository extends JpaRepository<User, String> {
   
   }
   ```

4. 编写Service类（可省略）

   ```java
   // 接口
   public interface UserService {
       List<User> getAllUser();
   }
   
   // 实现
   @Service
   public class UserServiceImpl implements UserService {
   
       @Autowired
       private UserRepository userRepository;
   
       @Override
       public List<User> getAllUser() {
           return userRepository.findAll();
       }
   }
   ```

5. 编写control类

   ```java
   @RestController
   public class UserController {
   
       @Autowired
       private UserService userService;
   
       /**
        * 得到所有用户
        */
       @GetMapping(value = "/user", produces = {"application/json;charset=UTF-8"})
       public List<User> getAllUser () {
   
           List<User> allUser = userService.getAllUser();
   		//早命令行输出
           for (User user : allUser) {
               System.out.println(user);
           }
   		//在页面输出
           return  allUser;
       }
   }
   ```

   ![image-20200702173941632](SpringBoot/image-20200702173941632.png)