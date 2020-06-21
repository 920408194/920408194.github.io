---
title: JavaException
date: 2020-06-21T14:30:41+08:00
categories:
- java
tags:
- java
- 技术文档
---

本文是我学习Java异常处理时的笔记，大部分内容来自廖雪峰的Java教程和java3y的公众号。

<!-- more -->

# Java异常处理

## 1、什么是异常

- 所谓错误，就是程序调用某个函数的时候，如果失败了，就表示出错。调用方如何获知调用失败的信息？有两种方法：

- - 方法一：约定返回错误码。
  - 方法二：在语言层面上提供一个异常处理机制。

- Java内置了一套异常处理机制，总是使用异常来表示错误。异常是一种`class`，因此它本身带有类型信息。异常可以在任何地方抛出，但只需要在上层捕获，这样就和方法调用分离了：

  ```java
  try {
      String s = processFile(“C:\\test.txt”);
      // ok:
  } catch (FileNotFoundException e) {
      // file not found:
  } catch (SecurityException e) {
      // no read permission:
  } catch (IOException e) {
      // io error:
  } catch (Exception e) {
      // other error:
  }
  ```

- ![exception](https://raw.githubusercontent.com/920408194/pic/master/img/exception.png)

- `Throwable`是异常体系的根，`Throwable`有两个体系：`Error`和`Exception`，

- - `Error`表示严重的错误，程序对此一般无能为力，
  - 而`Exception`则是运行时的错误，它可以被捕获并处理。

- 必须捕获的异常，包括`Exception`及其子类，但不包括`RuntimeException`及其子类，这种类型的异常称为`Checked Exception`。

- 不需要捕获的异常，包括`Error`及其子类，`RuntimeException`及其子类。

## 2、捕获异常

- 捕获异常使用`try...catch`语句，把可能发生异常的代码放到`try {…}`中，然后使用`catch`捕获对应的`Exception`及其子类：

- 还有一些童鞋喜欢在函数内部“消化”异常：

  ```java
  static byte[] toGBK(String s) {
      try {
          return s.getBytes("GBK");
      } catch (UnsupportedEncodingException e) {
          // 什么也不干
      }
      return null;
  ```

- 可以使用多个`catch`语句，每个`catch`分别捕获对应的`Exception`及其子类。**JVM**在捕获到异常后，会从上到下匹配`catch`语句，匹配到某个`catch`后，执行`catch`代码块，然后不再继续匹配。存在多个`catch`的时候，`catch`的顺序非常重要：子类必须写在前面。

- 那么如何消除这些重复的代码？**Java**的`try … catch`机制还提供了`finally`语句，`finally`语句块保证有无错误都会执行。`finally`语句不是必须的，可写可不写；`finally`总是最后执行。

- 某些情况下，可以没有`catch`，只使用`try … finally`结构。

- 如果处理某两个异常的代码是相同的，所以我们可以把它两用`|`合并到一起

## 3、抛出异常

- 需要捕获异常的语句，一般在定义时会抛出一个异常：

- ```java
  public byte[] getBytes(String charsetName) throws UnsupportedEncodingException{
    ....
  }
  ```

- 如果是测试代码，`try`的写法就略显麻烦。如果不想写任何`try`代码，可以直接把`main()`方法定义为`throws Exception`。因为`main()`方法声明了可能抛出`Exception`，也就声明了可能抛出所有的`Exception`，因此在内部就无需捕获了。代价就是一旦发生异常，程序会立刻退出

- 通过`printStackTrace()`可以打印出方法的调用栈

- 当发生错误时，例如，用户输入了非法的字符，我们就可以抛出异常

  ```java
  void process2(String s) {
      if (s==null) {
          throw new NullPointerException();
      }
  }
  ```

- 如果一个方法捕获了某个异常后，又在`catch`子句中抛出新的异常，就相当于把抛出的异常类型“转换”了。但是新的异常丢失了原始异常信息，我们已经看不到原始异常的信息了。为了能追踪到完整的异常栈，在构造异常的时候，把原始的`Exception`实例作为参数传进去。**捕获到异常并再次抛出时，一定要留住原始异常，否则很难定位第一案发现场！**

- `finally`抛出异常后，原来在`catch`中准备抛出的异常就“消失”了，因为只能抛出一个异常。没有被抛出的异常称为“被屏蔽”的异常（`Suppressed Exception`）我们一般先用`origin`变量保存原始异常，然后调用`Throwable.addSuppressed()`，把原始异常添加进来，最后在`finally`抛出

## 4、自定义异常

- 自定义一个`BaseException`作为“根异常”，然后，派生出各种业务类型的异常。`BaseException`需要从一个适合的`Exception`派生，通常建议从`RuntimeException`派生

## 5、断言

- 语句`assert x >= 0`;即为断言，断言条件`x >= 0`预期为`true`。如果计算结果为`false`，则断言失败，抛出`AssertionError`。
- Java断言的特点是：断言失败时会抛出`AssertionError`，导致程序结束退出。因此，断言不能用于可恢复的程序错误，只应该用于开发和测试阶段。
- 断言需要命令行开启。

## 6、JDK logging

- 输出日志，而不是用`System.out.println()`，有以下几个好处：

- 1. 可以设置输出样式，避免自己每次都写"ERROR: " + var；
  2. 可以设置输出级别，禁止某些级别输出。例如，只输出错误日志；
  3. 可以被重定向到文件，这样可以在程序运行结束后查看日志；
  4. 可以按包名控制日志级别，只输出某些包打的日志；
  5. 可以……

- ![log1](https://raw.githubusercontent.com/920408194/pic/master/img/log1.png)

- ![log2](https://raw.githubusercontent.com/920408194/pic/master/img/log2.png)

- 和Java标准库提供的日志不同，`Commons Logging`是一个第三方日志库，它是由Apache创建的日志模块。
- 使用`Commons Logging`只需要和两个类打交道，并且只有两步：第一步，通过`LogFactory`获取`Log`类的实例； 第二步，使用`Log`实例的方法打日志。（需要apache的第三方包）
- `Log4j`是一种非常流行的日志框架，最新版本是2.x。当我们使用`Log4j`输出一条日志时，`Log4j`自动通过不同的`Appender`把同一条日志输出到不同的目的地。

