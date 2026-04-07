## 概览
CTPL是一个使用C++语言开发的一款线程池库。
我们只分析STL版本，另一个版本用的是boost，基本思路估计大差不差。
总览整个代码，一共有两个类：1、Queue 2、thread_pool

## Queue队列类
该类是一个模板类，表示该队列存储的内容的数据类型。
内部存储两个成员变量，一个是stl的单端队列，一个是互斥锁。
内部有三个成员方法，分别是push、pop、empty，都是队列该有的成员方法，有一个很大的不同就是每次操作前都会加锁，保证多线程情况下的数据安全性。
在stl中，pop方法是不会返回队头的数值的，但是在这个类的成员方法的参数中，可以通过左值引用将值给传出。

## thread_pool线程池类
### 成员变量
1、**任务队列**q：数据类型为`std::function<void(int)> *`，通过std::function通用可调用对象包装器去包装一个返回值为void，一个int参数的函数。
2、**工作线程**threads：通过std::vector动态数组进行动态调整当前线程个数。
3、flag：
4、isDone：
5、isStop：
6、nWaiting：
7、mutex、cv：互斥锁与条件变量，互斥锁用来操作的时候上锁的，保证线程安全，cv用来进行等待与唤醒的。

### 成员方法
### 构造方法
有两个，一个是无参构造方法，另一个是有一个参数的构造方法。
构造方法：
``` cpp
thread_pool() { this->init(); }
thread_pool(int nThreads) { this->init(); this->resize(nThreads); }
```
init方法：
``` cpp
void init() { this->nWaiting = 0; this->isStop = false; this->isDone = false; }
```
无参构造函数直接调用