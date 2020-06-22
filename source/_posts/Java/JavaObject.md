---
title: JavaObject
date: 2020-06-21T14:12:04+08:00
categories:
- java
tags:
- java
- 技术文档
---

本文是我学习Java面向对象编程时的笔记，大部分内容来自廖雪峰的Java教程和java3y的公众号。
<!--more-->

# Java面向对象

## 一、面向对象编程

### 1、方法

- 可变参数用`类型…`定义，可变参数相当于数组类型：

  ```java
  public void setNames(String...names){
  	this.names=names;
  }
  ```

- 方法名相同，但各自的参数不同，称为方法重载（`Overload`）

### 2、继承

- 继承是面向对象编程中非常强大的一种机制，它首先可以复用代码。当我们让`Student`从`Person`继承时，`Student`就获得了`Person`的所有功能，我们只需要为`Student`编写新增的功能。

  ```java
  class Student extends Person {
      // 不要重复name和age字段/方法,
      // 只需要定义新增score字段/方法:
      private int score;
  
      public int getScore() { … }
      public void setScore(int score) { … }
  }
  ```

- 继承有个特点，就是子类无法访问父类的`private`字段或者`private`方法。用`protected`修饰的字段可以被子类访问;

- 这是因为在**Java**中，任何**class**的构造方法，第一行语句必须是调用父类的构造方法。如果没有明确地调用父类的构造方法，编译器会帮我们自动加一句`super()`;

- 这是因为`Student`继承自`Person`，因此，它拥有`Person`的全部功能。`Person`类型的变量，如果指向`Student`类型的实例，对它进行操作，是没有问题的！

  ```java
  Student s = new Student();
  Person p = s; // upcasting, ok
  Object o1 = p; // upcasting, ok
  Object o2 = s; // upcasting, ok
  ```

  这种把一个子类类型安全地变为父类类型的赋值，被称为向上转型（`upcasting`）。

- 向下转型很可能会失败（当出现多的功能和属性时）。失败的时候，**Java**虚拟机会报`ClassCastException`

- `instanceof`实际上判断一个变量所指向的实例是否是指定类型，或者这个类型的子类。如果一个引用变量为`null`，那么对任何`instanceof`的判断都为`false`。

  ```java
  Person p = new Student();
  if (p instanceof Student) {
      // 只有判断成功才会向下转型:
      Student s = (Student) p; // 一定会成功
  }
  ```

- 从Java 14开始，判断`instanceof`后，可以直接转型为指定变量，避免再次强制转型

  ```java
  Object obj = "hello";
  if (obj instanceof String) {
      String s = (String) obj;
      System.out.println(s.toUpperCase());
  }
  ```

### 3、组合

- 具有has关系不应该使用继承，而是使用组合，即Student可以持有一个Book实例：

  ```java
  class Student extends Person {
      protected Book book;
      protected int score;
  }
  ```

### 4、覆写和多态

- 加上`@Override`可以让编译器帮助检查是否进行了正确的覆写。希望进行覆写，但是不小心写错了方法签名，编译器会报错。
- **Java**的实例方法调用是基于运行时的实际类型的动态调用，而非变量的声明类型。

```java
Person p = new Student();
p.run();//应该打印Student.run
```

- 多态是指，针对某个类型的方法调用，其真正执行的方法取决于运行时期实际类型的方法。多态允许添加更多类型的子类实现功能扩展，却不需要修改基于父类的代码。

### 5、final

- 如果一个父类不允许子类对它的某个方法进行覆写，可以把该方法标记为`final`。
- 如果一个类不希望任何其他类继承自它，那么可以把这个类本身标记为`final`。
- 对于一个类的实例字段，同样可以用`final`修饰。用`final`修饰的字段在初始化后不能被修改。

### 6、Object方法

- Object是对象的最高级别的，所有的Java对象都**隐式**地继承了Object对象。

- `toString()`：把`instance`输出为`String`；
- `equals()`：判断两个`instance`是否逻辑相等；
- `hashCode()`：计算一个`instance`的哈希值。

### 7、抽象类

- 把一个方法声明为`abstract`，表示它是一个抽象方法，本身没有实现任何方法语句。因为这个抽象方法本身是无法执行的，所以，`Person`类也无法被实例化。编译器会告诉我们，无法编译`Person`类，因为它包含抽象方法

  ```java
  abstract class Person {
      public abstract void run();
  }
  ```

- 因为无法执行抽象方法，因此这个类也必须申明为抽象类（`abstract class`）。

- 继承一个抽象类，就必须覆写抽象方法

### 8、接口

- 在抽象类中，抽象方法本质上是定义接口规范：即规定高层类的接口，从而保证所有子类都有相同的接口实现，如果一个抽象类没有字段，所有方法全部都是抽象方法，就可以把该抽象类改写为接口：`interface`。

  ```java
  interface Person {
      void run();
      String getName();
  }
  ```

- 当一个具体的`class`去实现一个`interface`时，需要使用`implements`关键字

  ```java
  class Student implements Person {
      private String name;
  
      public Student(String name) {
          this.name = name;
      }
  
      @Override
      public void run() {
          System.out.println(this.name + " run");
      }
  
      @Override
      public String getName() {
          return this.name;
      }
  }
  ```

  一个类可以实现多个`interface`

- Java的接口特指`interface`的定义，表示一个接口类型和一组方法签名，而编程接口泛指接口规范，如方法签名，数据格式，网络协议等。

- 一个`interface`可以继承自另一个`interface`。`interface`继承自`interface`使用`extends`

- 在接口中，可以定义`default`方法。实现类可以不必覆写default方法。default方法的目的是，当我们需要给接口新增一个方法时，会涉及到修改全部子类。如果新增的是default方法，那么子类就不必全部修改，只需要在需要覆写的地方去覆写新增方法。

### 9、静态字段和静态方法

- 在一个`class`中定义的字段，我们称之为实例字段。实例字段的特点是，每个实例都有独立的字段，各个实例的同名字段互不影响。

- 还有一种字段，是用static修饰的字段，称为静态字段：`static field`。静态字段只有一个共享“空间”，所有实例都会共享该字段。对于静态字段，无论修改哪个实例的静态字段，效果都是一样的：所有实例的静态字段都被修改了。

- 把静态字段理解成类的字段而非实例的字段。

- 调用实例方法必须通过一个实例变量，而调用静态方法则不需要实例变量，通过类名就可以调用。因为静态方法属于`class`而不属于实例，因此，静态方法内部，无法访问`this`变量，也无法访问实例字段，它只能访问静态字段。

- 因为`interface`是一个纯抽象类，所以它不能定义实例字段。但是，`interface`是可以有静态字段的，并且静态字段必须为`final`类型。实际上，因为`interface`的字段只能是`public static final`类型，所以我们可以把这些修饰符都去掉

- ```java
  public interface Person {
      // 编译器会自动加上public statc final:
      int MALE = 1;
      int FEMALE = 2;
  }
  ```

### 10、包

- **Java**定义了一种名字空间，称之为包：`package`。一个类总是属于某个包，类名（比如`Person`）只是一个简写，真正的完整类名是`包名.类名`。
- 在定义`class`的时候，我们需要在第一行声明这个`class`属于哪个包。
- 如果有两个`class`名称相同，例如，`mr.jun.Arrays`和`java.util.Arrays`，那么只能`import`其中一个，另一个必须写完整类名。
- 包作用域是指一个类允许访问同一个`package`的没有`public`、`private`修饰的`class`，以及没有`public`、`protected`、`private`修饰的字段和方法。

## 二、java核心类

### 1、String

- **Java**字符串的一个重要特点就是字符串`不可变`。

- 想比较字符串的内容是否相同。必须使用`equals()`方法`s1.equals(s2)`

- 要忽略大小写比较，使用`equalsIgnoreCase()`方法。

  ```java
  // 是否包含子串:
  "Hello".contains("ll"); // true
  //搜索子串的更多的例子：
  "Hello".indexOf("l"); // 2
  "Hello".lastIndexOf("l"); // 3
  "Hello".startsWith("He"); // true
  "Hello".endsWith("lo"); // true
  //提取子串的例子：
  "Hello".substring(2); // "llo"
  "Hello".substring(2, 4); "ll"
  ```

- 使用`trim()`方法可以移除字符串首尾空白字符。空白字符包括空格，`\t`，`\r`，`\n`：

  ```java
  "  \tHello\r\n ".trim(); // "Hello"
  ```

- `String`还提供了`isEmpty()`和`isBlank()`来判断字符串是否为空和空白字符串：

- ```java
  "".isEmpty(); // true，因为字符串长度为0
  "  ".isEmpty(); // false，因为字符串长度不为0
  "  \n".isBlank(); // true，因为只包含空白字符
  " Hello ".isBlank(); // false，因为包含非空白字符
  ```

- 要在字符串中替换子串，有两种方法。一种是根据字符或字符串替换：

- ```java
  String s = "hello";
  s.replace('l', 'w'); // "hewwo"，所有字符'l'被替换为'w'
  s.replace("ll", "~~"); // "he~~o"，所有子串"ll"被替换为"~~"
  ```

  另一种是通过正则表达式替换：

  ```java
  String s = "A,,B;C ,D";
  s.replaceAll("[\\,\\;\\s]+", ","); // "A,B,C,D"
  ```

- 要分割字符串，使用`split()`方法，并且传入的也是正则表达式：

  ```java
  String s = "A,B,C,D";
  String[] ss = s.split("\\,"); // {"A", "B", "C", "D"}
  ```

- 拼接字符串使用静态方法`join()`，它用指定的字符串连接字符串数组：

  ```java
  String[] arr = {"A", "B", "C"};
  String s = String.join("***", arr); // "A***B***C"
  ```

- 要把任意基本类型或引用类型转换为字符串，可以使用静态方法`valueOf()`。这是一个重载方法，编译器会根据参数自动选择合适的方法：

- ```java
  String.valueOf(123); // "123"
  String.valueOf(45.67); // "45.67"
  String.valueOf(true); // "true"
  String.valueOf(new Object()); // 类似java.lang.Object@636be97c
  ```

- 要把字符串转换为其他类型，就需要根据情况。`parseInt`

- `String`和`char[]`类型可以互相转换，方法是：

- ```java
  char[] cs = "Hello".toCharArray(); // String -> char[]
  String s = new String(cs); // char[] -> String
  ```

  如果修改了`char[]`数组，`String`并不会改变,采用复制一份的方法。

- 在Java中，`char`类型实际上就是两个字节的`Unicode`编码。如果我们要手动把字符串转换成其他编码，可以这样做：

- ```java
  byte[] b1 = "Hello".getBytes(); // 按系统默认编码转换，不推荐
  byte[] b2 = "Hello".getBytes("UTF-8"); // 按UTF-8编码转换
  byte[] b2 = "Hello".getBytes("GBK"); // 按GBK编码转换
  byte[] b3 = "Hello".getBytes(StandardCharsets.UTF_8); // 按UTF-8编码转换
  ```

  如果要把已知编码的`byte[]`转换为`String`，可以这样做：

  ```java
  byte[] b = ...
  String s1 = new String(b, "GBK"); // 按GBK转换
  String s2 = new String(b, StandardCharsets.UTF_8); // 按UTF-8转换
  ```

- Java编译器对`String`做了特殊处理，使得我们可以直接用`+`拼接字符串

- 为了能高效拼接字符串，Java标准库提供了`StringBuilder`它是一个可变对象，可以预分配缓冲区，

- ```java
  StringBuilder sb = new StringBuilder(1024);
  for (int i = 0; i < 1000; i++) {
      sb.append(',');
      sb.append(i);
  }
  String s = sb.toString();
  ```

  `StringBuilder`还可以进行链式操作：

  ```java
   var sb = new StringBuilder(1024);
          sb.append("Mr ")
            .append("Bob")
            .append("!")//Mr.Bob!
            .insert(0, "Hello, ");//Hello,Mr.Bob!
  ```

  进行链式操作的关键是，定义的`append()`方法会返回`this`

- `String`还提供了一个静态方法`join()`

- ```java
  String[] names = {"Bob", "Alice", "Grace"};
  var s = String.join(", ", names);
  ```

### 2、包装类型

- 基本类型：`byte`，`short`，`int`，`long`，`boolean`，`float`，`double`，`char`。引用类型可以赋值为`null`，表示空，但基本类型不能赋值为`null`

- 比如，想要把`int`基本类型变成一个引用类型，我们可以定义一个`Integer`类，它只包含一个实例字段`int`，这样，`Integer`类就可以视为`int`的包装类（`Wrapper Class`）

  ```java
  public class Integer {
      private int value;
  
      public Integer(int value) {
          this.value = value;
      }
  
      public int intValue() {
          return this.value;
      }
  }
  ```

- 实际上，因为包装类型非常有用，Java核心库为每种基本类型都提供了对应的包装类型：

  ![class](JavaObject/class.png)

- 因为`int`和`Integer`可以互相转换，所以，**Java**编译器可以帮助我们自动在`int`和`Integer`之间转型，这种直接把`int`变为`Integer`的赋值写法，称为自动装箱（`Auto Boxing`），反过来，把`Integer`变为`int`的赋值写法，称为自动拆箱（`Auto Unboxing`）。

- 所有的包装类型都是不变类。对两个`Integer`实例进行比较要特别注意：绝对不能用`==`比较，因为`Integer`是引用类型，必须使用`equals()`比较。

- 因为`Integer.valueOf()`可能始终返回同一个`Integer`实例，因此，在我们自己创建`Integer`的时候，以下两种方法：

- - 方法1：`Integer n = new Integer(100)`;
  - 方法2：`Integer n = Integer.valueOf(100)`
  - `方法2`更好，因为`方法1`总是创建新的`Integer`实例，`方法2`把内部优化留给`Integer`的实现者去做，即使在当前版本没有优化，也有可能在下一个版本进行优化。

  - 我们把能创建“新”对象的静态方法称为静态工厂方法。`Integer.valueOf()`就是静态工厂方法，它尽可能地返回缓存的实例以节省内存。

- `Integer`类本身还提供了大量方法，例如，最常用的静态方法`parseInt()`可以把字符串解析成一个整数。`Integer`还可以把整数格式化为指定进制的字符串。

  ```java
  Integer.toString(100,36);//"2s",表示36进制
  ```

- **Java**的包装类型还定义了一些有用的静态变量

- ```java
  // boolean只有两个值true/false，其包装类型只需要引用Boolean提供的静态字段:
  Boolean t = Boolean.TRUE;
  Boolean f = Boolean.FALSE;
  // int可表示的最大/最小值:
  int max = Integer.MAX_VALUE; // 2147483647
  int min = Integer.MIN_VALUE; // -2147483648
  // long类型占用的bit和byte数量:
  int sizeOfLong = Long.SIZE; // 64 (bits)
  int bytesOfLong = Long.BYTES; // 8 (bytes)
  ```

- 最后，所有的整数和浮点数的包装类型都继承自`Number`，因此，可以非常方便地直接通过包装类型获取各种基本类型：

- ```java
  // 向上转型为Number:
  Number num = new Integer(999);
  // 获取byte, int, long, float, double:
  byte b = num.byteValue();
  int n = num.intValue();
  long ln = num.longValue();
  float f = num.floatValue();
  double d = num.doubleValue();
  ```

### 3、枚举类

- 为了让编译器能自动检查某个值在枚举的集合内，并且，不同用途的枚举需要不同的类型来标记，不能混用，我们可以使用`enum`来定义枚举类

- ```java
  public class Main {
      public static void main(String[] args) {
          Weekday day = Weekday.SUN;
          if (day == Weekday.SAT || day == Weekday.SUN) {
              System.out.println("Work at home!");
          } else {
              System.out.println("Work at office!");
          }
      }
  }
  
  enum Weekday {
      SUN, MON, TUE, WED, THU, FRI, SAT;
  }
  ```

  注意到定义枚举类是通过关键字`enum`实现的，我们只需依次列出枚举的常量名。首先，`enum`常量本身带有类型信息，即`Weekday.SUN`类型是`Weekday`，编译器会自动检查出类型错误。不同类型的枚举不能互相比较或者赋值，因为类型不符。

- 因为`enum`类型的每个常量在JVM中只有一个唯一实例，所以可以直接用`==`比较：

- 和其他`class`的区别

- - 定义的`enum`类型总是继承自`java.lang.Enum`，且无法被继承；
  - 只能定义出`enum`的实例，而无法通过`new`操作符创建`enum`的实例；
  - 定义的每个实例都是引用类型的唯一实例；
  - 可以将`enum`类型用于`switch`语句。

- 一些方法：

- - `String s = Weekday.SUN.name(); // "SUN"`
  - `int n = Weekday.MON.ordinal(); // 1`
  - 默认情况下，对枚举常量调用`toString()`会返回和`name()`一样的字符串。但是，`toString()`可以被覆写，而`name()`则不行。我们可以给`Weekday`添加`toString()`方法：

- 我们可以定义`private`的构造方法，并且，给每个枚举常量添加字段

  ```java
  enum Weekday {
    MON(1), TUE(2), WED(3), THU(4), FRI(5), SAT(6), SUN(0);
  	public final int dayValue;
  
  	private Weekday(int dayValue) {
      this.dayValue = dayValue;
  	}
  }
  ```

  这样就无需担心顺序的变化，新增枚举常量时，也需要指定一个int值。

### 4、BigInteger和BigDecimal

- 如果我们使用的整数范围超过了`long`型怎么办？这个时候，就只能用软件来模拟一个大整数。`java.math.BigInteger`就是用来表示任意大小的整数。`BigInteger`内部用一个`int[]`数组来模拟一个非常大的整数：

- 对`BigInteger`做运算的时候，只能使用实例方法，例如，加法运算

- ```java
  BigInteger i1 = new BigInteger("1234567890");
  BigInteger i2 = new BigInteger("12345678901234567890");
  BigInteger sum = i1.add(i2); // 12345678902469135780
  ```

- `BigInteger`和`Integer`、`Long`一样，也是不可变类，并且也继承自`Number`类。

  - 转换为`byte`：`byteValue()`
  - 转换为`short`：`shortValue()`
  - 转换为`int`：`intValue()`
  - 转换为`long`：`longValue()`
  - 转换为`float`：`floatValue()`
  - 转换为`double`：`doubleValue()`

  通过上述方法，可以把`BigInteger`转换成基本类型。如果`BigInteger`表示的范围超过了基本类型的范围，转换时将丢失高位信息，即结果不一定是准确的。如果需要准确地转换成基本类型，可以使用`intValueExact()`、`longValueExact()`等方法，在转换时如果超出范围，将直接抛出`ArithmeticException`异常。

- 和`BigInteger`类似，`BigDecimal`可以表示一个任意大小且精度完全准确的浮点数。`BigDecimal`用`scale()`表示小数位数

  ```java
  BigDecimal d1 = new BigDecimal("123.45");
  BigDecimal d2 = new BigDecimal("123.4500");
  BigDecimal d3 = new BigDecimal("1234500");
  System.out.println(d1.scale()); // 2,两位小数
  System.out.println(d2.scale()); // 4
  System.out.println(d3.scale()); // 0
  ```

  如果一个`BigDecimal`的`scale()`返回负数，例如，`-2`，表示这个数是个整数，并且末尾有2个`0`。

- 可以对一个`BigDecimal`设置它的`scale`，如果精度比原始值低，那么按照指定的方法进行四舍五入或者直接截断：

  ```java
  BigDecimal d1 = new BigDecimal("123.456789");
  BigDecimal d2 = d1.setScale(4, RoundingMode.HALF_UP); // 四舍五入，123.4568
  BigDecimal d3 = d1.setScale(4, RoundingMode.DOWN); // 直接截断，123.4567
  ```

- 在比较两个`BigDecimal`的值是否相等时，要特别注意，使用`equals()`方法不但要求两个`BigDecimal`的值相等，还要求它们的`scale()`相等

- 使用`compareTo()`方法来比较，它根据两个值的大小分别返回`负数`、`正数`和`0`，分别表示小于、大于和等于。

### 5、Math

- 求绝对值： `Math.abs(-100); // 100`
      `Math.abs(-7.8); // 7.8`
- 取最大或最小值：`Math.max(100, 99); // 100`
      `Math.min(1.2, 2.3); // 1.2`
- 计算`x`y次方：`Math.pow(2,      10); // 2的10次方=1024`
- 计算`√x`：`Math.sqrt(2);      // 1.414…`
- 计算`e`x次方：`Math.exp(2);      // 7.389…`
- 计算以`e`为底的对数：`Math.log(4);      // 1.386…`
- 计算以10为底的对数：`Math.log10(100);      // 2`
- 三角函数：`Math.sin(3.14); // 0.00159…`
      ` Math.cos(3.14); // -0.9999...`
      ` Math.tan(3.14); // -0.0015…`
      ` Math.asin(1.0); // 1.57079...`
       `Math.acos(1.0); // 0.0`
- 几个数学常量：`double pi = Math.PI; // 3.14159…`
      ` double e = Math.E; // 2.7182818…`
       `Math.sin(Math.PI / 6); // sin(π/6) = 0.5`
- 生成一个随机数`x`，`x`的范围是`0 <= x < 1`：`Math.random(); //      0.53907...      `每次都不一样

### 6、Random和SecureRandom

- Random用来创建伪随机数。所谓伪随机数，是指只要给定一个初始的种子，产生的随机数序列是完全一样的。

  ```java
  Random r = new Random();
  r.nextInt(); // 2071575453,每次都不一样
  r.nextInt(10); // 5,生成一个[0,10)之间的int
  r.nextLong(); // 8811649292570369305,每次都不一样
  r.nextFloat(); // 0.54335...生成一个[0,1)之间的float
  r.nextDouble(); // 0.3716...生成一个[0,1)之间的double
  ```

- 这是因为我们创建`Random`实例时，如果不给定种子，就使用系统当前时间戳作为种子，因此每次运行时，种子不同，得到的伪随机数序列就不同。如果我们在创建`Random`实例时指定一个种子，就会得到完全确定的随机数序列

  ```java
  Random r = new Random(12345);
  ```

- `Math.random()`实际上内部调用了`Random`类，所以它也是伪随机数，只是我们无法指定种子。

- `SecureRandom`是用来创建安全的随机数的

- ```java
  SecureRandom sr = new SecureRandom();
  System.out.println(sr.nextInt(100));
  ```

- `SecureRandom`无法指定种子，它使用`RNG`（`random number generator`）算法。`SecureRandom`的安全性是通过操作系统提供的安全的随机种子来生成随机数。这个种子是通过CPU的热噪声、读写磁盘的字节、网络流量等各种随机事件产生的“熵”。
