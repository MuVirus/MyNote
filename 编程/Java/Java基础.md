# Java基本概念
## 基本概念
1. Java中，只有一个公共类（Public)，并且该类名与文件名一致。
2. 使用`javac`可以将`.java`源码编译成`.class`**字节码**文件；
3. 使用`java`可以运行一个已编译的Java程序`.class`，参数是类名。
4. Java是面向对象编译解释型语言。
5. 内存回收线程（垃圾回收器，Garbage Collector，GC）的主要职责就是自动释放无用的内存对象。它会定期检查哪些对象不再被程序引用，然后释放这些对象占用的内存。
6. Java 程序不需要用户手动创建线程来释放内存。垃圾回收是 JVM 的自动功能，用户不需要干预内存的释放过程。
7. Java 是一种高级语言，它隐藏了指针的使用，以提高安全性和易用性。在 Java 中，用户不能直接使用指针来管理内存。内存的分配和释放都是由 JVM（Java 虚拟机）自动管理的。
8. Java 的垃圾回收机制（Garbage Collection, GC）是自动的，但它**不能保证在指定的时间释放内存对象**。
## 标识符与关键字
1. 表示float浮点数后面要加上f，默认为double类型浮点数。
2. 三元运算符会将条件结果的数据类型提升到一致。

## 面向对象
### 基本概念
1. 程序中没有`main()`方法会导致无法运行，但是不会导致编译失败。
### 继承
1. 在JAVA中，如果想使类不能被继承可以使用关键字final
2. 同样类的对象可以相互访问字段（包括private），不同类的对象不可以直接相互访问字段。
### 对象创建过程
1. 父类构造方法调用
2. 成员变量初始化
3. 子类构造方法剩余部分
### 重载
能够重载的主要判别点（前提是方法名相同）是参数列表的不同（个数、类型、顺序），而返回类型、访问修饰符不是判别点。
### 重写
方法重写（Override）的规则包括：
1. 方法名和参数列表必须相同
2. 返回值类型必须相同
3. 访问权限不能比父类方法更严格
4. 必须发生在继承关系中
5. 子类可以重写父类的方法
### 抽象
1. 子类是否可以是抽象类，与父类是否是抽象类无关。
### 接口
1. 接口不支持实例字段
2. 接口中的属性，都是静态属性，并且是常量。（public static final）
3. 接口不能有构造器
4. 接口中的访问权限总是public
5. 接口支持多重继承
6. 子接口继承父接口用`extends`，类实现接口用`implements`。
7. 接口不能直接被实例化，但是可以声明接口类型的变量和数组。
8. 接口方法不能有具体的实现。
### 多态
1. 字段是静态绑定的（编译时确定），方式是动态绑定的（运行时确定）。
2. 类方法的调用是看实际类型的(比如Animal dog = new Dog()；如果Dog内重写了Animal中的方法，再调用dog对象中的方法就是Dog内的方法，如果没有重写，则条用dog对象中的方法就是调用Animal中的方法)
## 异常

## 容器
### List
#### 概述
1. 接口类
2. 列表
#### 接口方法
##### 1、of()
原型：将形参中的元素转换成List对象
``` java
static <E> List<E> of(E... elements)
```
例
``` java
List<Integer> list = List.of(1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17);
System.out.println(list);
// out: [1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17]
```
##### 2、size()
获取大小
##### 3、toArray()
调用toArray()，返回`Object[]`数组
``` java
Object[] toArray();
```
##### 4、索引
1、indexOf()
正序查找
``` java
int indexOf(Object o);
```
2、lastIndexOf()
倒序查找
``` java
int lastIndexOf(Object o);
```
#### 一、ArrayList
##### 创建
``` java
ArrayList<Integer> list = new ArrayList<Integer>();
```
或者
``` java
List<Integer> list = new ArrayList<Integer>();
```
##### 1、添加
``` java
// 添加整数2
list.add(2);
```
##### 2、修改
``` java
// 索引1，修改成2
list.set(1, 2);
```
##### 3、移除
原型，可以看到一种是通过索引删除，另一种方法是通过对象删除。
``` java
public E remove(int index);
public boolean remove(Object o);
```
##### 4、遍历
1、使用for循环
``` java
for(int i = 0; i < list.size(); i++) {
	System.out.println(list.get(i));
}
```
2、for-each
``` java
for(Integer i : list) {
    System.out.println(i);
}
```
3、迭代器
``` java
Iterator<Integer> it = list.iterator();
for(; it.hasNext(); ) {
    System.out.println(it.next());
}
```
### Set
#### 概述
1. 集合
#### 一、HashSet()

### Map
#### 概述

#### 一、HashMap()
##### 1、创建对象
带有key键，value值。
例：
``` java
HashMap<Integer, String> hashMap = new HashMap<Integer, String>();
```
##### 2、添加
原型
``` java
public V put(K key, V value)
```
##### 3、通过key获取value
通过get()方法
``` java
public V get(Object key);
```
##### 4、返回key集
``` java
public Set<K> keySet();
```
##### 5、返回value集
``` java
public Collection<V> values()
```
#### 二、TreeMap()

### Queue

## 日期时间

## 常用类
### String
#### 字段
1. `value`字段
	- 类型：byte[]
	- 作用：
		- 用于存储字符数据。
		- 通过coder字段来更高效的存储字符数据。
	- 注意：
		- Java8之前的类型为char[]
		- Java9之后的类型为byte[]
2. `coder`字段
	- 类型：byte
	- 作用：
		- `coder`字段指示字符的编码方法
		- 值
			- LATIN1(值为0)，表示一个字符表示一个字节大小
			- UTF16(值为1)，表示一个字符表示两个字符大小
 ![](img/Pasted%20image%2020250615101503.png)
#### 不可继承
可以查看String类源码，被`final`修饰过了，表示不可被子类继承。
![](img/Pasted%20image%2020250615094926.png)
#### 创建字符串
String类有很多构造方法，可以通过不同的方法创建字符串
还可以不通过构造方法进行创建，
```java
String str = "Hello World!";
```
#### 方法
##### 一、字符串长度
1、length
##### 二、连接字符串
1、concat()方法
使用该方法会返回相连接后的新字符串。
```java
String str = string1.concat(string2)
```
![](img/Pasted%20image%2020250615104100.png)
2、使用`+`操作符
```
String str = string1 + string2;
```
##### 三、格式化字符串
为静态方法，直接通过String类使用format方法。
```java
String str = String.format("value: %d", valueNum);
```
![](img/Pasted%20image%2020250615104602.png)
##### 四、比较
###### 1、equals()
- 返回值：`boolean`
- 原型：
```java
public boolean equals(Object anObject)
```
- 作用：
	- 当两个字符串完全相同时，返回true。
	- 否则为false。
###### 2、equalsIgnoreCase()
- 返回值：`boolean`
- 作用：
	- 忽视大小写，完全相同时，返回值为true
	- 否则为false
###### 3、compareTo()
- 返回值：`int`
- 原型：
```java
public int compareTo(String anotherString)
```
- 作用：
	- 当前String类对象完全等于anotherString，返回值为0
	- 当前String类对象小于anotherString，返回值为负数
	- 当前String类对象大于anotherString，返回值为正数
###### 4、compareToIgnoreCase()
- 返回值：`int`
- 原型：
```java
public int compareToIgnoreCase(String str)
```
- 作用：
	- 忽视大小写
	- 当前String类对象完全等于anotherString，返回值为0
	- 当前String类对象小于anotherString，返回值为负数
	- 当前String类对象大于anotherString，返回值为正数
##### 五、返回指定索引处的char值
1、charAt()
``` java
public char charAt(int index)
```
##### 六、返回指定字符在字符串中第一次出现的索引
1、indexOf()
有很多重载indexOf方法，下面是部分。
``` java
public int indexOf(int ch)
```
``` java
public int indexOf(int ch, int fromIndex)
```
``` java
public int indexOf(int ch, int beginIndex, int endIndex)
```
``` java
public int indexOf(String str)
```
##### 七、处理字符串
1、trim()
去除字符串两端的空白字符
2、split()
根据字符串或者正则字符串，进行拆分字符串。
### StringBuffer
#### 作用
当要对字符串进行修改的时候，需要用到StringBuffer。
#### 初始化StringBuffer对象
不能用不带构造方法初始化StringBuffer对象，如下方
``` java
StringBuffer sBuffer = "Welcome!!!";
```
需要用带有构造方法方式初始化
``` java
StringBuffer sBuffer = new StringBuffer("Welcome!!!");
```
#### 方法
##### 一、修改指定处的字符
1、setCharAt()
``` java
public synchronized void setCharAt(int index, char ch)
```
##### 二、删除指定处的字符
1、deleteCharAt()
``` java
public synchronized StringBuffer deleteCharAt(int index)
```
##### 三、容量
1、capacity
在初始化的时候，默认为16。如果当扩充的时候，capacity会变大。
#### StringBuilder
##### 作用
与StringBuilder的不同是，StringBuffer类中的方法带有`synchronized`关键字进行了同步，考虑到了多线程环境下的安全问题。
在非多线程环境下，StringBuffer执行效率较低，就会使用StringBuilder。
##### 初始化StringBuilder对象
和StringBuffer类似，
``` java
StringBuilder sBuilder = new StringBuilder("Welcome!!!");
```
##### 方法
跟StringBuilder方法差不多。
# 问题
## 1、java class文件名 运行错误
错误: 找不到或无法加载主类 Hello 原因: java.lang.ClassNotFoundException: Hello

如果你是新版jdk的话（我是jdk22），那么环境变量中是不需要classpath的，可以将电脑中的环境变量的classpath删掉。

