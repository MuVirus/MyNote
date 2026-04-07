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
#### 构造方法/初始化方法
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
init方法将一些参数给初始化。
通过上面可以看到，如果只构造了thread_pool对象的话，是不是额外开辟线程的，需要传入指定数量的线程数来开辟对应数量的工作线程。
#### resize设置工作线程数量
resize方法，用来动态设置当前工作线程的线程个数；如果大于原有线程数则会关闭一些线程，这里是直接detach，然后条件变量通知；如果小于原有线程数则会开启一部分线程数；不管是关闭还是开启，重要要达到设定的nThreads线程个数。
``` cpp
void resize(int nThreads) {
    if (!this->isStop && !this->isDone) {
        int oldNThreads = static_cast<int>(this->threads.size());
        if (oldNThreads <= nThreads) {  // if the number of threads is increased
            this->threads.resize(nThreads);
            this->flags.resize(nThreads);
            
            for (int i = oldNThreads; i < nThreads; ++i) {
                this->flags[i] = std::make_shared<std::atomic<bool>>(false);
                this->set_thread(i);
            }
        }
        else {  // the number of threads is decreased
            for (int i = oldNThreads - 1; i >= nThreads; --i) {
                *this->flags[i] = true;  // this thread will finish
                this->threads[i]->detach();
            }
            {
                // stop the detached threads that were waiting
                std::unique_lock<std::mutex> lock(this->mutex);
                this->cv.notify_all();
            }
            this->threads.resize(nThreads);  // safe to delete because the threads are detached
            this->flags.resize(nThreads);  // safe to delete because the threads have copies of shared_ptr of the flags, not originals
        }
    }
} 
```

#### push
