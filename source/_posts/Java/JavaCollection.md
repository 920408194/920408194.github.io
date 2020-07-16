---
title: JavaCollection
date: 2020-06-29 19:54:00
categories:
- java
tags:
- java
- 技术文档
---

本文是我学习Java集合时的笔记，大部分内容来自廖雪峰的Java教程和java3y的公众号。

<!--more-->

# Java集合

- 在Java中，如果一个Java对象可以在内部持有若干其他Java对象，并对外提供访问接口，我们把这种Java对象称为集合。
- 在Java中，如果一个Java对象可以在内部持有若干其他Java对象，并对外提供访问接口，我们把这种Java对象称为集合。很显然，Java的数组可以看作是一种集合
- 几何和数组的区别：数组有如下限制：
  - 数组初始化后大小不可变；
  - 数组只能按索引顺序存取。

## Collection

- Java标准库自带的`java.util`包提供了集合类：`Collection`，它是除`Map`外所有其他集合类的根接口。Java的`java.util`包主要提供了以下三种类型的集合：
  - `List`：一种有序列表的集合，例如，按索引排列的`Student`的`List`；
  - `Set`：一种保证没有重复元素的集合，例如，所有无重复名称的`Student`的`Set`；
  - `Map`：一种通过键值（key-value）查找的映射表集合，例如，根据`Student`的`name`查找对应`Student`的`Map`。
- Java集合的设计有几个特点：一是实现了接口和实现类相分离，例如，有序表的接口是`List`，具体的实现类有`ArrayList`，`LinkedList`等，二是支持泛型，我们可以限制在一个集合中只能放入同一种数据类型的元素。
- Java访问集合总是通过统一的方式——迭代器（Iterator）来实现，它最明显的好处在于无需知道集合内部元素是按什么方式存储的。

## 一、List

- 在集合类中，`List`是最基础的一种集合：它是一种有序列表。

- `List`的行为和数组几乎完全相同：`List`内部按照放入元素的先后顺序存放，每个元素都可以通过索引确定自己的位置，`List`的索引和数组一样，从`0`开始。

- 数组和`List`类似，也是有序结构，如果我们使用数组，在添加和删除元素的时候，会非常不方便。例如，从一个已有的数组`{'A', 'B', 'C', 'D', 'E'}`中删除索引为`2`的元素：

  ```
  ┌───┬───┬───┬───┬───┬───┐
  │ A │ B │ C │ D │ E │   │
  └───┴───┴───┴───┴───┴───┘
                │   │
            ┌───┘   │
            │   ┌───┘
            │   │
            ▼   ▼
  ┌───┬───┬───┬───┬───┬───┐
  │ A │ B │ D │ E │   │   │
  └───┴───┴───┴───┴───┴───┘
  ```

  这个“删除”操作实际上是把`'C'`后面的元素依次往前挪一个位置，而“添加”操作实际上是把指定位置以后的元素都依次向后挪一个位置，腾出来的位置给新加的元素。这两种操作，用数组实现非常麻烦。

### 1、ArrayList和LinkedList

- 在实际应用中，需要增删元素的有序列表，我们使用最多的是`ArrayList`。实际上，`ArrayList`在内部使用了数组来存储所有元素。例如，一个`ArrayList`拥有5个元素，实际数组大小为`6`（即有一个空位）：

  ```
  size=5
  ┌───┬───┬───┬───┬───┬───┐
  │ A │ B │ C │ D │ E │   │
  └───┴───┴───┴───┴───┴───┘
  ```

  当添加一个元素并指定索引到`ArrayList`时，`ArrayList`自动移动需要移动的元素：

  ```
  size=5
  ┌───┬───┬───┬───┬───┬───┐
  │ A │ B │   │ C │ D │ E │
  └───┴───┴───┴───┴───┴───┘
  ```

  然后，往内部指定索引的数组位置添加一个元素，然后把`size`加`1`：

  ```
  size=6
  ┌───┬───┬───┬───┬───┬───┐
  │ A │ B │ F │ C │ D │ E │
  └───┴───┴───┴───┴───┴───┘
  ```

  继续添加元素，但是数组已满，没有空闲位置的时候，`ArrayList`先创建一个更大的新数组，然后把旧数组的所有元素复制到新数组，紧接着用新数组取代旧数组：

  ```
  size=6
  ┌───┬───┬───┬───┬───┬───┬───┬───┬───┬───┬───┬───┐
  │ A │ B │ F │ C │ D │ E │   │   │   │   │   │   │
  └───┴───┴───┴───┴───┴───┴───┴───┴───┴───┴───┴───┘
  ```

  现在，新数组就有了空位，可以继续添加一个元素到数组末尾，同时`size`加`1`：

  ```
  size=7
  ┌───┬───┬───┬───┬───┬───┬───┬───┬───┬───┬───┬───┐
  │ A │ B │ F │ C │ D │ E │ G │   │   │   │   │   │
  └───┴───┴───┴───┴───┴───┴───┴───┴───┴───┴───┴───┘
  ```

- `List<E>`的几个主要的接口方法：

  - 在末尾添加一个元素：`void add(E e)`
  - 在指定索引添加一个元素：`void add(int index, E e)`
  - 删除指定索引的元素：`int remove(int index)`
  - 删除某个元素：`int remove(Object e)`
  - 获取指定索引的元素：`E get(int index)`
  - 获取链表大小（包含元素的个数）：`int size()`

- 实现`List`接口并非只能通过数组（即`ArrayList`的实现方式）来实现，另一种`LinkedList`通过“链表”也实现了List接口。在`LinkedList`中，它的内部每个元素都指向下一个元素：

  ```
          ┌───┬───┐   ┌───┬───┐   ┌───┬───┐   ┌───┬───┐
  HEAD ──>│ A │ ●─┼──>│ B │ ●─┼──>│ C │ ●─┼──>│ D │   │
          └───┴───┘   └───┴───┘   └───┴───┘   └───┴───┘
  ```

- ArrayList和LinkedList

|                     | ArrayList    | LinkedList           |
| ------------------- | ------------ | -------------------- |
| 获取指定元素        | 速度很快     | 需要从头开始查找元素 |
| 添加元素到末尾      | 速度很快     | 速度很快             |
| 在指定未知添加/删除 | 需要移动元素 | 不需要移动元素       |
| 内存占用            | 少           | 较大                 |

通常情况下，我们总是优先使用`ArrayList`。

### 2、List的特点

- `List`接口允许我们添加重复的元素，即`List`内部的元素可以重复。`List`还允许添加`null`：

### 3、创建List

- 除了使用`ArrayList`和`LinkedList`，我们还可以通过`List`接口提供的`of()`方法，根据给定元素快速创建`List`：

  ```java
  List<Integer> list = List.of(1, 2, 5);
  ```

  但是`List.of()`方法不接受`null`值，如果传入`null`，会抛出`NullPointerException`异常。

### 4、遍历List

- 使用迭代器`Iterator`来访问`List`。`Iterator`本身也是一个对象，但它是由`List`的实例调用`iterator()`方法的时候创建的。`Iterator`对象知道如何遍历一个`List`，并且不同的`List`类型，返回的`Iterator`对象实现也是不同的，但总是具有最高的访问效率。

- `Iterator`对象有两个方法：

  - `boolean hasNext()`判断是否有下一个元素；
  - `E next()`返回下一个元素。

- 使用`Iterator`遍历`List`代码如下：

  ```java
  public class Main {
      public static void main(String[] args) {
          List<String> list = List.of("apple", "pear", "banana");
          for (Iterator<String> it = list.iterator(); it.hasNext(); ) {
              String s = it.next();
              System.out.println(s);
          }
      }
  }
  ```

  通过`Iterator`遍历`List`永远是最高效的方式。

- 由于`Iterator`遍历是如此常用，所以，Java的`for each`循环本身就可以帮我们使用`Iterator`遍历。把上面的代码再改写如下：

  ```java
  public class Main {
      public static void main(String[] args) {
          List<String> list = List.of("apple", "pear", "banana");
          for (String s : list) {
              System.out.println(s);
          }
      }
  }
  ```

### 5、List和Array转换

- 把`List`变为`Array`有三种方法

- 第一种是调用`toArray()`方法直接返回一个`Object[]`数组：

  ```java
  public class Main {
      public static void main(String[] args) {
          List<String> list = List.of("apple", "pear", "banana");
          Object[] array = list.toArray();
          for (Object s : array) {
              System.out.println(s);
          }
      }
  }
  ```

  这种方法会丢失类型信息，所以实际应用很少。

- 第二种方式是给`toArray(T[])`传入一个类型相同的`Array`，`List`内部自动把元素复制到传入的`Array`中：

  ```java
  public class Main {
      public static void main(String[] args) {
          List<Integer> list = List.of(12, 34, 56);
          Integer[] array = list.toArray(new Integer[3]);
          for (Integer n : array) {
              System.out.println(n);
          }
      }
  }
  ```

  注意到这个`toArray(T[])`方法的泛型参数`<T>`并不是`List`接口定义的泛型参数`<E>`，所以，我们实际上可以传入其他类型的数组，

  例如我们传入`Number`类型的数组，返回的仍然是`Number`类型：

  ```java
  public class Main {
      public static void main(String[] args) {
          List<Integer> list = List.of(12, 34, 56);
          Number[] array = list.toArray(new Number[3]);
          for (Number n : array) {
              System.out.println(n);
          }
      }
  }
  ```

  但是，如果我们传入类型不匹配的数组，例如，`String[]`类型的数组，由于`List`的元素是`Integer`，所以无法放入`String`数组，这个方法会抛出`ArrayStoreException`。

  - 如果我们传入的数组大小和`List`实际的元素个数不一致，根据[List接口](https://docs.oracle.com/en/java/javase/14/docs/api/java.base/java/util/List.html#toArray(T[]))的文档：如果传入的数组不够大，那么`List`内部会创建一个新的刚好够大的数组，填充后返回；如果传入的数组比`List`元素还要多，那么填充完元素后，剩下的数组元素一律填充`null`。

- 最后一种更简洁的写法是通过`List`接口定义的`T[] toArray(IntFunction<T[]> generator)`方法：

  ```java
  Integer[] array = list.toArray(Integer[]::new);
  ```

- 反过来，把`Array`变为`List`就简单多了，通过`List.of(T...)`方法最简单：

  ```java
  Integer[] array = { 1, 2, 3 };
  List<Integer> list = List.of(array);
  ```

  对于JDK 11之前的版本，可以使用`Arrays.asList(T...)`方法把数组转换成`List`。

- 要注意的是，返回的`List`不一定就是`ArrayList`或者`LinkedList`，因为`List`只是一个接口，如果我们调用`List.of()`，它返回的是一个只读`List`，对只读`List`调用`add()`、`remove()`方法会抛出`UnsupportedOperationException`。

## 二、equals方法

- `List`提供了`boolean contains(Object o)`方法来判断`List`是否包含某个指定元素。此外，`int indexOf(Object o)`方法可以返回某个元素的索引，如果元素不存在，就返回`-1`。

- `List`内部并不是通过`==`判断两个元素是否相等，而是使用`equals()`方法判断两个元素是否相等，例如`contains()`方法可以实现如下：

  ```java
  public class ArrayList {
      Object[] elementData;
      public boolean contains(Object o) {
          for (int i = 0; i < size; i++) {
              if (o.equals(elementData[i])) {
                  return true;
              }
          }
          return false;
      }
  }
  ```

- 因此，要正确使用`List`的`contains()`、`indexOf()`这些方法，放入的实例必须正确覆写`equals()`方法，否则，放进去的实例，查找不到。我们之所以能正常放入`String`、`Integer`这些对象，是因为Java标准库定义的这些类已经正确实现了`equals()`方法。

- `equals()`方法要求我们必须满足以下条件：

  - 自反性（Reflexive）：对于非`null`的`x`来说，`x.equals(x)`必须返回`true`；
  - 对称性（Symmetric）：对于非`null`的`x`和`y`来说，如果`x.equals(y)`为`true`，则`y.equals(x)`也必须为`true`；
  - 传递性（Transitive）：对于非`null`的`x`、`y`和`z`来说，如果`x.equals(y)`为`true`，`y.equals(z)`也为`true`，那么`x.equals(z)`也必须为`true`；
  - 一致性（Consistent）：对于非`null`的`x`和`y`来说，只要`x`和`y`状态不变，则`x.equals(y)`总是一致地返回`true`或者`false`；
  - 对`null`的比较：即`x.equals(null)`永远返回`false`。

- 编写`equals()`方法如下：

  ```java
  public class Person {
  		public String name;
      public int age;
  }
  public boolean equals(Object o) {
      if (o instanceof Person) {
          Person p = (Person) o;
          return this.name.equals(p.name) && this.age == p.age;
      }
      return false;
  }
  ```

  对于引用字段比较，我们使用`equals()`，对于基本类型字段的比较，我们使用`==`。

- 如果`this.name`为`null`，那么`equals()`方法会报错，因此，需要继续改写如下：

  ```java
  public boolean equals(Object o) {
      if (o instanceof Person) {
          Person p = (Person) o;
          boolean nameEquals = false;
          if (this.name == null && p.name == null) {
              nameEquals = true;
          }
          if (this.name != null) {
              nameEquals = this.name.equals(p.name);
          }
          return nameEquals && this.age == p.age;
      }
      return false;
  }
  ```

- 如果`Person`有好几个引用类型的字段，上面的写法就太复杂了。要简化引用类型的比较，我们使用`Objects.equals()`静态方法：

- 因此，我们总结一下`equals()`方法的正确编写方法：

  1. 先确定实例“相等”的逻辑，即哪些字段相等，就认为实例相等；
  2. 用`instanceof`判断传入的待比较的`Object`是不是当前类型，如果是，继续比较，否则，返回`false`；
  3. 对引用类型用`Objects.equals()`比较，对基本类型直接用`==`比较。

- 使用`Objects.equals()`比较两个引用类型是否相等的目的是省去了判断`null`的麻烦。两个引用类型都是`null`时它们也是相等的。

- 如果不调用`List`的`contains()`、`indexOf()`这些方法，那么放入的元素就不需要实现`equals()`方法。

## 三、Map

### 1、Map的特点

- `Map`键值（key-value）映射表的数据结构，作用就是能高效通过`key`快速查找`value`（元素）。

  用`Map`来实现根据`name`查询某个`Student`的代码如下：

  ```java
  public class Main {
      public static void main(String[] args) {
          Student s = new Student("Xiao Ming", 99);
          Map<String, Student> map = new HashMap<>();
          map.put("Xiao Ming", s); // 将"Xiao Ming"和Student实例映射并关联
          Student target = map.get("Xiao Ming"); // 通过key查找并返回映射的Student实例
          System.out.println(target == s); // true，同一个实例
          System.out.println(target.score); // 99
          Student another = map.get("Bob"); // 通过另一个key查找
          System.out.println(another); // 未找到返回null
      }
  }
  
  class Student {
      public String name;
      public int score;
      public Student(String name, int score) {
          this.name = name;
          this.score = score;
      }
  ```

- 通过上述代码可知：`Map<K, V>`是一种键-值映射表，当我们调用`put(K key, V value)`方法时，就把`key`和`value`做了映射并放入`Map`。当我们调用`V get(K key)`时，就可以通过`key`获取到对应的`value`。如果`key`不存在，则返回`null`。和`List`类似，`Map`也是一个接口，最常用的实现类是`HashMap`。

如果只是想查询某个`key`是否存在，可以调用`boolean containsKey(K key)`方法。

如果我们在存储`Map`映射关系的时候，对同一个key调用两次`put()`方法，分别放入不同的`value`，会有什么问题呢？例如：

import java.util.HashMap; import java.util.Map; Run

重复放入`key-value`并不会有任何问题，但是一个`key`只能关联一个`value`。在上面的代码中，一开始我们把`key`对象`"apple"`映射到`Integer`对象`123`，然后再次调用`put()`方法把`"apple"`映射到`789`，这时，原来关联的`value`对象`123`就被“冲掉”了。实际上，`put()`方法的签名是`V put(K key, V value)`，如果放入的`key`已经存在，`put()`方法会返回被删除的旧的`value`，否则，返回`null`。

始终牢记：Map中不存在重复的key，因为放入相同的key，只会把原有的key-value对应的value给替换掉。

此外，在一个`Map`中，虽然`key`不能重复，但`value`是可以重复的：

```
Map<String, Integer> map = new HashMap<>();
map.put("apple", 123);
map.put("pear", 123); // ok
```

### 2、遍历Map

对`Map`来说，要遍历`key`可以使用`for each`循环遍历`Map`实例的`keySet()`方法返回的`Set`集合，它包含不重复的`key`的集合：

import java.util.HashMap; import java.util.Map; Run

同时遍历`key`和`value`可以使用`for each`循环遍历`Map`对象的`entrySet()`集合，它包含每一个`key-value`映射：

import java.util.HashMap; import java.util.Map; Run

`Map`和`List`不同的是，`Map`存储的是`key-value`的映射关系，并且，它*不保证顺序*。在遍历的时候，遍历的顺序既不一定是`put()`时放入的`key`的顺序，也不一定是`key`的排序顺序。使用`Map`时，任何依赖顺序的逻辑都是不可靠的。以`HashMap`为例，假设我们放入`"A"`，`"B"`，`"C"`这3个`key`，遍历的时候，每个`key`会保证被遍历一次且仅遍历一次，但顺序完全没有保证，甚至对于不同的JDK版本，相同的代码遍历的输出顺序都是不同的！

遍历Map时，不可假设输出的key是有序的！