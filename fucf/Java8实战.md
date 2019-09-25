[TOC]
# Java8 实战
Author：fucf

## 第一章 为什么要关心Java8
Q：为什么应该关心Java8？
* 编写代码更加简单
* 处理并发更加方便

### 1.1 Java怎么还在变
* C、C++占用资源少，但是安全性不加，主要用于操作系统和嵌入式系统。
* Java、C# 安全性语言在资源不紧张的应用中取代了C++

## 第四章 引入流
### 4.1 流是什么
流是Java API的新成员，它允许你以声明性方式处理数据集合（通过查询语句来表达，而不 是临时编写一个实现）。

可以简单理解为不需要我们自己去做筛选（filter）、提取（map）或截断（limit）功能，Streams库已经自带了。  -- by Sam

### 4.2 流简介
* Java8中的集合支持一个新的stream方法，它会返回一个流（接口定义在java.utils.stream.Stream里）

流到底是什么？简短的定义就是“从支持数据处理操作的源生成的元素序列”。
* 元素序列--就像集合一样，流也提供了一个接口，可以访问特定元素类型的一组有序值。
* 源--流会使用一个提供数据的源，如集合、数组或输入/输出资源。请注意，从有序集合生成流时会保留原有的顺序。由列表（list）生成的流，其元素顺序与列表一致。
* 数据处理操作--流的数据处理功能支持类似于数据库的操作，以及函数式编程语言中 的常用操作，如filter、map、reduce、find、match、sort等。流操作可以顺序执 行，也可并行执行。
*
流操作有两个重要特点：
* 流水线 -- 很多流操作本身会返回一个流，这样多个操作就可以链接起来，形成一个大 的流水线。
* 内部迭代 -- 与使用迭代器显式迭代的集合不同，流的迭代操作是在背后进行的。

### 4.3 流与集合
Java现有的集合概念和新的流概念都提供了接口，来配合代表元素型有序值的数据接口。所 谓有序，就是说我们一般是按顺序取用值，而不是随机取用的。

集合与流之间的差异：
* 集合是一个内存中的数据结构， 它包含数据结构中目前所有的值——集合中的每个元素都得先算出来才能添加到集合中。
* 流是在概念上固定的数据结构(你不能添加或删除元素),其元素则是按需计算的。

![-w940](https://it-learn-1259257911.cos.ap-beijing.myqcloud.com/2019/09/18/15687386794785.jpg)

流只能遍历一次
![-w994](https://it-learn-1259257911.cos.ap-beijing.myqcloud.com/2019/09/18/15687387336294.jpg)

中间操作
![-w968](https://it-learn-1259257911.cos.ap-beijing.myqcloud.com/2019/09/18/15687389194786.jpg)

终端操作
![-w968](https://it-learn-1259257911.cos.ap-beijing.myqcloud.com/2019/09/18/15687389378418.jpg)


## 第五章 使用流


## 第六章
流可以用类似于数据库的操作帮助你处理集合。

流支持两种类型的操作：
* 中间操作（如filter和map）
* 终端操作（如count、findFirst、forEach和reduce）

中间操作不会消耗流；终端操作会消耗流，以产生一个最终结果，例如返回流中的最大元素。

函数式编程相对于指令式编程的一个主要优势：你只需指出希望的结果--做什么，而不用操心执行的步骤—-如何做


```
long howManyDishes = menu.stream().count();

int totalCalories = menu.stream().collect(summingInt(Dish::getCalories));
```

### 6.2 归约和汇总
#### joining
joining工厂方法返回的收集器会把对流中每一个对象应用toString方法得到的所有字符串连接成一个字符串。这意味着你把菜单中所有菜肴的名称连接起来，如下所示：
joining内部使用了StingBuilder来把生成的字符串逐个追加起来。
```
String shortMenu = menu.stream().map(Dish::getName).collect(joining());

String shortMenu = menu.stream().map(Dish::getName).collect(joining(", "));
```

#### reducing

```java
int totalCalories = menu.stream().collect(reducing( 0, Dish::getCalories, (i, j) -> i + j));
```

它需要三个参数：
* 第一个参数是归约操作的起始值，也是流中没有元素时的返回值。


## 第10章 用Optional取代null

### 10.3 应用Optional的几种模式
**创建Optional对象**
1. 声明一个空的Optional
```
Optional<Car> optCar = Optional.empty();
```
2. 依据一个非空值创建Optional
```
//如果car是一个null,这段代码会抛出NullPointerException
Optional<Car> optCar = Optional.of(car);
```
1. 可接受null的Optional
```
//如果car是null,那么得到的Optional对象就是一个空对象
Optional<Car> optCat = Optional.ofNullable(car);
```

**使用map从Optional对象中提取和转换值**
```
Optional<Insurance> optInsurance = Optional.ofNullable(insurance); Optional<String> name = optInsurance.map(Insurance::getName);
```
如果Optional包含一个值，那函数就将该值作为参数传递给map，对该值进行转换。如果Optional为空，就什么也不做。
![](https://it-learn-1259257911.cos.ap-beijing.myqcloud.com/2019/09/25/15694263490816.jpg)
