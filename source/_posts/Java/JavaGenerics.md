---
title: JavaGenerics
date: 2020-06-29 19:54:00
categories:
- java
tags:
- java
- 技术文档
---

本文是我学习Java泛型时的笔记，大部分内容来自廖雪峰的Java教程和java3y的公众号。

<!--more-->

# java泛型

## 一、什么是泛型

- 在了解什么是泛型之前，我们先观察Java标准库提供的`ArrayList`，它可以看作“可变长度”的数组，因为用起来比数组更方便。实际上`ArrayList`内部就是一个`Object[]`数组，配合存储一个当前分配的长度，就可以充当“可变数组”：

  ```java
  public class ArrayList {
      private Object[] array;
      private int size;
      public void add(Object e) {...}
      public void remove(int index) {...}
      public Object get(int index) {...}
  }
  ```

  如果用上述`ArrayList`存储`String`类型，会有这么几个缺点：

  - 需要强制转型；

  - 不方便，易出错。

    ```java
    list.add(new Integer(123));
    // ERROR: ClassCastException:
    String second = (String) list.get(1);
    ```

- 为了解决上述问题，我们可以把`ArrayList`变成一种模板：`ArrayList<T>`，代码如下：

  ```java
  public class ArrayList<T> {
      private T[] array;
      private int size;
      public void add(T e) {...}
      public void remove(int index) {...}
      public T get(int index) {...}
  }
  ```

  `T`可以是任何class。这样一来，我们就实现了：编写一次模版，可以创建任意类型的`ArrayList`：

  ```java
  // 创建可以存储String的ArrayList:
  ArrayList<String> strList = new ArrayList<String>();
  // 创建可以存储Float的ArrayList:
  ArrayList<Float> floatList = new ArrayList<Float>();
  // 创建可以存储Person的ArrayList:
  ArrayList<Person> personList = new ArrayList<Person>();
  ```

  这就是泛型。

- 在Java标准库中的`ArrayList<T>`实现了`List<T>`接口，它可以向上转型为`List<T>`：

  ```java
  public class ArrayList<T> implements List<T> {
      ...
  }
  
  List<String> list = new ArrayList<String>();
  ```

  即类型`ArrayList<T>`可以向上转型为`List<T>`。

  - 要*特别注意*：不能把`ArrayList<Integer>`向上转型为`ArrayList<Number>`或`List<Number>`。这是为什么呢？假设`ArrayList<Integer>`可以向上转型为`ArrayList<Number>`，观察一下代码：

    ```java
    // 创建ArrayList<Integer>类型：
    ArrayList<Integer> integerList = new ArrayList<Integer>();
    // 添加一个Integer：
    integerList.add(new Integer(123));
    // “向上转型”为ArrayList<Number>：
    ArrayList<Number> numberList = integerList;
    // 添加一个Float，因为Float也是Number：
    numberList.add(new Float(12.34));
    // 从ArrayList<Integer>获取索引为1的元素（即添加的Float）：
    Integer n = integerList.get(1); // ClassCastException!
    ```

  - 我们把一个`ArrayList<Integer>`转型为`ArrayList<Number>`类型后，这个`ArrayList<Number>`就可以接受`Float`类型，因为`Float`是`Number`的子类。但是，`ArrayList<Number>`实际上和`ArrayList<Integer>`是同一个对象，也就是`ArrayList<Integer>`类型，它不可能接受`Float`类型， 所以在获取`Integer`的时候将产生`ClassCastException`。

  - 实际上，编译器为了避免这种错误，根本就不允许把`ArrayList<Integer>`转型为`ArrayList<Number>`。

## 二、使用泛型

- 使用`ArrayList`时，如果不定义泛型类型时，泛型类型实际上就是`Object`。此时，只能把`<T>`当作`Object`使用，没有发挥泛型的优势。

- 除了`ArrayList<T>`使用了泛型，还可以在接口中使用泛型。例如，`Arrays.sort(Object[])`可以对任意数组进行排序，但待排序的元素必须实现`Comparable<T>`这个泛型接口：

  ```java
  public interface Comparable<T> {
      /**
       * 返回-1: 当前实例比参数o小
       * 返回0: 当前实例与参数o相等
       * 返回1: 当前实例比参数o大
       */
      int compareTo(T o);
  }
  
  // sort
  import java.util.Arrays;
  
  public class Main {
      public static void main(String[] args) {
          String[] ss = new String[] { "Orange", "Apple", "Pear" };
          Arrays.sort(ss);
          System.out.println(Arrays.toString(ss));
      }
  }
  ```

  这是因为`String`本身已经实现了`Comparable<String>`接口。如果换成我们自定义的`Person`类型试试：

  ```java
  // sort
  import java.util.Arrays;
  
  public class Main {
      public static void main(String[] args) {
          Person[] ps = new Person[] {
              new Person("Bob", 61),
              new Person("Alice", 88),
              new Person("Lily", 75),
          };
          Arrays.sort(ps);
          System.out.println(Arrays.toString(ps));
      }
  }
  
  class Person implements Comparable<Person> {
      String name;
      int score;
      Person(String name, int score) {
          this.name = name;
          this.score = score;
      }
      public int compareTo(Person other) {
          return this.name.compareTo(other.name);
      }
      public String toString() {
          return this.name + "," + this.score;
      }
  }
  
  ```

  就需要先实现`Comparable<T>`接口。

- **注意：** compareTo方法 参数可以是一个 Byte, Double, Integer, Float, Long 或 Short 类型的参数。

## 三、编写泛型

- 一般来说，我们很少需要编写泛型类。

- 如果我们确实需要编写一个泛型类，可以按照以下步骤来编写一个泛型类。

  ```java
  //1. 首先，按照某种类型，例如：String，来编写类：
  //2. 然后，标记所有的特定类型，这里是String：
  //3. 最后，把特定类型String替换为T，并申明<T>：
  public class Pair<T> {
      private T first;
      private T last;
      public Pair(T first, T last) {
          this.first = first;
          this.last = last;
      }
      public T getFirst() {
          return first;
      }
      public T getLast() {
          return last;
      }
  }
  ```

- 编写泛型类时，要特别注意，泛型类型`<T>`不能用于静态方法。对于静态方法，我们可以单独改写为“泛型”方法，只需要使用另一个类型即可。

  ```java
  public static <K> Pair<K> create(K first, K last) {
          return new Pair<K>(first, last);
  }
  ```

## 四、擦拭法

- Java语言的泛型实现方式是擦拭法（Type Erasure）。Java语言的泛型实现方式是擦拭法（Type Erasure）。谓擦拭法是指，虚拟机对泛型其实一无所知，所有的工作都是编译器做的。

- Java使用擦拭法实现泛型，导致了：

  - 编译器把类型`<T>`视为`Object`；
  - 编译器根据`<T>`实现安全的强制转型。

  所以，Java的泛型是由编译器在编译时实行的，编译器内部永远把所有类型`T`视为`Object`处理，但是，在需要转型的时候，编译器会根据`T`的类型自动为我们实行安全地强制转型。

- Java泛型的局限：

  - `<T>`不能是基本类型，例如`int`，因为实际类型是`Object`，`Object`类型无法持有基本类型.

  - 无法取得带泛型的`Class`.

    ```java
    Pair<String> p1 = new Pair<>("Hello", "world");
    Pair<Integer> p2 = new Pair<>(123, 456);
    Class c1 = p1.getClass();
    Class c2 = p2.getClass();
    System.out.println(c1); // true
    System.out.println(c1==Pair.class); // true
    ```

    因为`T`是`Object`，我们对`Pair<String>`和`Pair<Integer>`类型获取`Class`时，获取到的是同一个`Class`，也就是`Pair`类的`Class`。换句话说，所有泛型实例，无论`T`的类型是什么，`getClass()`返回同一个`Class`实例，因为编译后它们全部都是`Pair<Object>`。

  - 无法判断带泛型的类型

    ```java
    Pair<Integer> p = new Pair<>(123, 456);
    // Compile error:
    if (p instanceof Pair<String>) {
    }
    ```

  - 不能实例化`T`类型
  
- 一个类可以继承自一个泛型类。

  ```java
  public class IntPair extends Pair<Integer> {...}
  ```

  使用的时候，因为子类`IntPair`并没有泛型类型，所以，正常使用即可。

- 我们无法获取`Pair<T>`的`T`类型，即给定一个变量`Pair<Integer> p`，无法从`p`中获取到`Integer`类型。

- **注意**：`Pair<Integer>`不是`Pair<Number>`的子类。

## 五、通配符

### 1、extends

- 使用`Pair<? extends Number>`使得方法接收所有泛型类型为`Number`或`Number`子类的`Pair`类型。

  ```java
  public class Main {
      public static void main(String[] args) {
          Pair<Integer> p = new Pair<>(123, 456);
          int n = add(p);
          System.out.println(n);
      }
  
      static int add(Pair<? extends Number> p) {
          Number first = p.getFirst();
          Number last = p.getLast();
          return first.intValue() + last.intValue();
      }
  
  }
  
  class Pair<T> {
      private T first;
      private T last;
      public Pair(T first, T last) {
          this.first = first;
          this.last = last;
      }
      public T getFirst() {
          return first;
      }
      public T getLast() {
          return last;
      }
  }
  ```

- 我们不可预测实际类型就是`Integer`，所以，下面的代码是无法通过编译的：

  ```java
  Integer x = p.getFirst();
  ```

- `<? extends Number>`通配符有一个重要限制：方法参数签名`setFirst(? extends Number)`无法传递任何`Number`类型给`setFirst(? extends Number)`。这个限制可以让方法只读。

- 在定义泛型类型`Pair<T>`的时候，也可以使用`extends`通配符来限定`T`的类型：

  ```java
  public class Pair<T extends Number> { ... }
  ```

  非`Number`类型将无法通过编译。

### 2、super通配符

- 和`extends`通配符相反，这次，我们希望接受`Pair<Integer>`类型。我们使用`super`通配符来改写这个方法：

  ```java
  void set(Pair<? super Integer> p, Integer first, Integer last) {
      p.setFirst(first);
      p.setLast(last);
  }
  ```

  注意到`Pair<? super Integer>`表示，方法参数接受所有泛型类型为`Integer`或`Integer`父类的`Pair`类型。

- `Pair<? super Integer>`的`setFirst()`方法，它的方法签名实际上是：

  ```java
  void setFirst(? super Integer);
  ```

  因此，可以安全地传入`Integer`类型。
  
- 使用`<? super Integer>`通配符表示：

  - 允许调用`set(? super Integer)`方法传入`Integer`的引用；
  - 不允许调用`get()`方法获得`Integer`的引用。

### 3、对比extends和super通配符

- 作为方法参数，`<? extends T>`类型和`<? super T>`类型的区别在于：

  - `<? extends T>`允许调用读方法`T get()`**获取**`T`的引用，但不允许调用写方法`set(T)`传入`T`的引用（传入`null`除外）；
  - `<? super T>`允许调用写方法`set(T)`**传入**`T`的引用，但不允许调用读方法`T get()`获取`T`的引用（获取`Object`除外）。

  一个是允许读不允许写，另一个是允许写不允许读。

- 何时使用`extends`，何时使用`super`？为了便于记忆，我们可以用**PECS原则**：Producer Extends Consumer Super。即：如果需要返回`T`，它是生产者（Producer），要使用`extends`通配符；如果需要写入`T`，它是消费者（Consumer），要使用`super`通配符。

### 4、无限定通配符

- 实际上，Java的泛型还允许使用无限定通配符（Unbounded Wildcard Type），即只定义一个`?`：

  ```java
  void sample(Pair<?> p) {...}
  ```

- 因为`<?>`通配符既没有`extends`，也没有`super`，因此：

  - 不允许调用`set(T)`方法并传入引用（`null`除外）；
  - 不允许调用`T get()`方法并获取`T`引用（只能获取`Object`引用）。

  换句话说，既不能读，也不能写，那只能做一些`null`判断：

  ```java
  static boolean isNull(Pair<?> p) {
      return p.getFirst() == null || p.getLast() == null;
  }
  ```

- 大多数情况下，可以引入泛型参数`<T>`消除`<?>`通配符：

  ```java
  static <T> boolean isNull(Pair<T> p) {
      return p.getFirst() == null || p.getLast() == null;
  }
  ```

- `<?>`通配符有一个独特的特点，就是：`Pair<?>`是所有`Pair<T>`的超类：

## 六、泛型和反射

- Java的部分反射API也是泛型。例如：`Class<T>`就是泛型。调用`Class`的`getSuperclass()`方法返回的`Class`类型是`Class<? super T>`：

- 构造方法`Constructor<T>`也是泛型。

- 我们可以声明带泛型的数组，但不能用`new`操作符创建带泛型的数组：编译器不会检查不是泛型的数组。

- 带泛型的数组实际上是编译器的类型擦除，所以我们不能直接创建泛型数组`T[]`，因为擦拭后代码变为`Object[]`：

  ```Java
  // compile error:public class Abc<T> {
      T[] createArray() {
          return new T[5];
      }
  }
  ```

  必须借助`Class<T>`来创建泛型数组：

    ```java
    T[] createArray(Class<T> cls) {
        return (T[]) Array.newInstance(cls, 5);
    }
    ```

    我们还可以利用可变参数创建泛型数组`T[]`：

    ```java
    public class ArrayHelper {@SafeVarargs
        static <T> T[] asArray(T... objs) {
            return objs;
        }
    }

    String[] ss = ArrayHelper.asArray("a", "b", "c");
    Integer[] ns = ArrayHelper.asArray(1, 2, 3);
    ```