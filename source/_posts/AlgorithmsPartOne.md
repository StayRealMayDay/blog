---
title: AlgorithmsPartOne
date: 2017-10-03 18:18:06
comments: true
tags:
  - java foundation
  - Stack
  - Queue
  - bag
categories: java
---

# 算法

## 第一章

### 1.1

原始数据类型：整型，双精度实数型，布尔型，字符型。

库：其实就是一个类，在类中可能有许多静态方法，当我们import这个库之后就可以直接调用其中的静态函数，或者是实例函数，包含这些函数的类就是一个库，

API：应用程序编程接口，其实就是每一个类的中所定义的那些静态函数或者public函数，可以让别人来调用的，不过一般来说API还包括说明这个函数如何使用，即每个库方法的方法名称，签名（函数的传递参数部分），简短的描述。

字符串：字符串可以用 + 号进行拼接，同时还可以自动转化如果+号的一个参数是字符串，另一个参数是其他类型的话会自动转化为字符串类型然后进行拼接。

<!--more-->

格式化输出：printf（“ ”，x）接受两个参数，第一个是格式字符串，描述第二个参数应该如何在输出中被转换为一个字符串，%跟着一个字符表示转换代码。%d表示十进制数，f浮点数，s字符串，可以在%和字符之间插入整数来限制值的宽度。%4d表示输出十进制数，4表示在左边加上四个空格，如果要在右边加空格，则需要负数，%.4d表示保留小数点后第四位。\n表示换行。

标准输入：这里用的是StdIn这个库，读取的是命令行的输入，这些输入由空白字符分开（空格，制表符，换行符），有这些符号读入的时候会分开读。StdIn.isEmpty()这个函数会一直检查命令行是否有输入，如果没有输入，他会一直等待输入。用strl+d结束。

基于文件的输入输出：In和Out库中readInts（）,readStrings（）,readDoubles（）用于读取文件中的多个数据，并返回一个数组，write（）也是一样，write（）函数有两个参数 static void write（String[ ] a,String name）第一个是要写入的数据，第二个是要写入的文件名字。

二分查找算法：

```java
public static int rank (int key, int[] a)
{int lo = 0;
 int hi = a.length - 1;
 while(lo <= hi){
   int mid = (lo + hi) / 2;
   if (key < a[mid]) hi = mid - 1;
   if (key > a[mid]) lo = mid + 1;
   else return mid;
 }
 return -1;
}

```

### 1.2数据抽象

java编程基础主要是用关键字class构造被称为引用类型的数据类型。也称为面向对象编程。

ADT：抽象数据类型，简单来说就是我们自己写的类类型，和原始数据类型区别，

继承的方法：所有数据类型都会继承toString()方法，当我们用print输出的时候就会默认调用toString方法。

对象：对象是能承载数据类型的值的实体，三大特征：状态，标识，行为。状态就是对象的值，标识就是区别对象的，可以看作是在内存当中的地址，行为就是函数了，对数据类型的操作。引用是访问对象的方式，引用可以看作是内存地址，如对象a，a里面保存的是一个地址，就是该对象的值的首地址。

创建对象：为新对象分配内存空间，调用构造函数初始化对象中的值，返回该对象的一个引用。

抽象数据类型的实现：第一部分定义数据类型的值的实例变量（就是类中的变量），第二就是实现对实例变量的操作的构造函数和实例函数（类中的函数），一个实例变量如果希望不被改变可以用final来修饰

```java
private final String name;
```

每个类都至少有一个构造函数来创建一个对象的标识，每个构造函数创建一个对象，并返回该对象的引用，即在内存中开辟地址给实例变量，然后初始化变量，再返回这块地址的首地址（对象的引用）。

接口与继承：接口用关键字 interface ，他只是声明一下有哪些函数，然后通过继承他的类来对这些方法进行实现（implements），用接口的对象来指向他的子类的引用，这样可以调用子类的方法从而实现多态（对同一个消息作出不同的应答，原因是因为父类的对象可以指向子类，指向不同的子类就可以调用不同子类的方法。）。继承就是用关键字extends 继承破坏了封装，父类改变，所有的子类都会改变，也可以实现多态。

### 1.3背包、队列和栈

背包的、队列和栈：是三种数据结构，背包是只能向其中增加数据，不能移除，可以统计，但是和顺序无关，队列就是先进先出，栈就是先进后出。

泛型：也叫参数化类型，就是用参数来确定到底我储存的是什么数据类型，在类名后面的<Item>记号将Item定义为一个类型参数，即Item就是一个类型，可以在类中声明这种类型的参数，然后进行一些列操作运算，但是Item到底是什么类型只有在创建对象的时候在对Item这个参数赋值之后才知道。

```java
public class Stack<Item> implements Iterable<Item>{
  Stack()
  void push(Item item)
 public static void main{
  Stack<String> stack = new Stack<String>();  
 }
}
```

通过上面的代码可以知道，当我们要创建一个Stack对象的时候才知道Item代表是什么数据类型。

迭代：许多时候需要处理集合中每个元素，迭代访问集合中的每个元素，如果集合是可迭代的，则可以用foreach语句来处理

```java
Queue<Transaction> collection = new Queue<Transaction>();
for (Transaction t : collection){
  print(t);
}
```

背包：一种不支持从中删除元素的集合数据类型，目的就是帮助用例收集并迭代遍历所有收集到的元素。

先进先出队列：FIFO，集合能保存它们的相对顺序。

下压栈：基于一种后进先出策略的集合类型。

集合数据类型的实现：可以通过数组或者是链表来实现，下面展示一种数组实现的栈

```java
public class FixedCapacityStack<Item>{
  private Item[] a;  //保存元素
  private int  N; // 记录栈中元素个数
  public FixedCapacityStack(int max){
    a = (Item[]) new Object[max];
    public boolean isEmpty(){ return N == 0;}
    public int size(){ return  N;}
    public void push(Item item){
      a[N++] = item;
    }
    public Item pop(){
      Item temp = a[--N];
      a[N] = null; //把弹栈的数据赋值为null，避免对象游离；
      return temp;
    }
  }
}
```

动态调整数组大小：这样解决了上面栈的大小固定问题

```java
private void resize(int max){
  Item[] temp = (Item[]) new Object[max];
  for(int i = 0; i < a.length; i++){
    temp[i] = a[i];
  }
  a = temp; //把数组a大小扩展为max长度的大小
}
```

之后push的时候如果数组长度满了就扩展为原来的两倍

```java
public void push(Item item){
  if(N == a.length) resize(2*a.length);
  a[N++] = item;
}
```

pop的时候如果数组剩余长度为原来总长度的四分之一则把长度缩短一半

```java
public Item pop(){
  Item item = a[--N];
  a[N] = null;
  if(N > 0 && N <= a.length / 4) resize(a.length / 2);
  return item;
}
```

迭代：集合数据类型的基本才做之一就是用迭代，即foreach语句迭代便利集合中的每一个元素

```java
Queue<Transaction> collection = new Queue<Transaction>();
for (Transaction t : collection){
  print(t);
}
```

等价于

```java
Iterator<String> i = collection.iterator();
while(i.hasNext()){
  String s = i.next();
  print(s);
}
```

从上面可以看出集合数据类型必须实现一个iterator( )函数并且返回一个Iterator对象

Iterator 类必须包含两个方法 hasNext( )返回一个布尔值和netx( )返回一个泛型对象；

所以实现一个可以迭代的集合类需要三步。

1.要实现Iterator<Item>这个接口，接口如下：为什么需要是泛型，原因是因为我们的集合类是泛型的，所以我们的迭代器也需要是泛型，因为迭代器是一个个处理我们集合类的元素，然后集合类的元素是泛型。

```java
public interface Iterable<Item>{
  Iterator<Item> iterator();
}
```

因为需要实现这个接口所以在集合类中必须要有一个iterator方法并且返回一个 Iterator<Item>的对象,这个对象是一个迭代器，迭代器都是泛型的，所以可以用它来遍历所有类型的对象。

在集合类中如何实现这个iterator函数呢。

```java
public Iterator<Item> iterator(){
  return new XXXXIterator();
}
```

即返回一个自定义的迭代器就好了；这个自定义的迭代器我们需要自己实现，当然这个自定义迭代器要是Iterator迭代器的子类；因为迭代器是一个接口我们需要根据自己的实际情况去实现它。迭代器父类如下：

```java
public interface Iterator<Item>{
  boolean hasNext();
  Item next();
  void remove();
}
```

这个子类是集合类的嵌套类，它包含在集合类中。子类的实现如下

```java
public class XXXXIterator implements Iterator<Item>{
 private int i = N;/*N是集合类中的实例变量，记录的是集合类中元素的个数，为了能这样访问，这必须是一个嵌套类，
                    *这个类必须是在集合类中定义的*/
  boolean hasNext(){return i > 0;}
  public Item next(){
    return a[--i];
  }
  public void remove(){ }
}
```

三步曲，1.要继承可以迭代接口类 Iterable<Item> 2.自己定义一个迭代器实现接口类迭代器Iterator<Item>，3实现接口类Iterable<Item>中的函数 Iterator<Item> iterator( )即返回一个迭代器。

算法1.1下压栈（LIFO）

```java
import java.util.Iterator
public class ResizingArraysStack<Item> implement Iterable<Item>{
  private Item[] a = (Item[]) new Object[1];
  private int N;
  public boolean isEmpty(){ return N == 0;}
  public int size(){return N;}
  private void resize(int max){
    Item[] temp = (Item[]) new Object[max];
    for(int i = 0; i < N; i++){temp[i] = a[i];}
    a = temp;
  }
  public void push(Item item){
    if(N == a.length) resize(2*a.length);
    a[N++] = item;
  }
  public Item pop(){
    Item item = a[--N];
    a[N] = null;
    if(N > 0 && N <= a.length / 4) resize(a.length / 2);
    return temp;
  }
  public Iterator<Item> iterator(){
    return new ReverseArrayIterator();
  }
  public class ReverseArrayIterator implements Iterator<Item>{
    int i = N;
    public boolean hasNext(){return i > 0;}
    public Item next(){return a[--i];}
    public void remove(){}
  }
}
```



