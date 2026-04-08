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
3、flags：类型std::vector<..>表示对应的工作线程会在运行完任务后立刻退出
4、isDone：类型为`atomic<bool>`，一般用于运行到cv.wait时唤醒工作线程，一直运行到任务队列为空时，工作线程才退出（当stop方法中的参数isWait为true时，isDone才会设置为true）
5、isStop：`atomic<bool>`，表示该工作线程停止运行
6、nWaiting：当前空闲的工作队列
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

#### push 将任务放置任务队列队尾
都是模板类，不同点是传入的函数除了第一个参数外，一个有函数参数，一个没有。传入的函数必须是第一个参数为int类型的。
``` cpp
template<typename F, typename... Rest>
auto push(F && f, Rest&&... rest) ->std::future<decltype(f(0, rest...))> {
    auto pck = std::make_shared<std::packaged_task<decltype(f(0, rest...))(int)>>(
        std::bind(std::forward<F>(f), std::placeholders::_1, std::forward<Rest>(rest)...)
        );
    auto _f = new std::function<void(int id)>([pck](int id) {
        (*pck)(id);
    });
    this->q.push(_f);
    std::unique_lock<std::mutex> lock(this->mutex);
    this->cv.notify_one();
    return pck->get_future();
}
// run the user's function that excepts argument int - id of the running thread. returned value is templatized
// operator returns std::future, where the user can get the result and rethrow the catched exceptins
template<typename F>
auto push(F && f) ->std::future<decltype(f(0))> {
    auto pck = std::make_shared<std::packaged_task<decltype(f(0))(int)>>(std::forward<F>(f));
    auto _f = new std::function<void(int id)>([pck](int id) {
        (*pck)(id);
    });
    this->q.push(_f);
    std::unique_lock<std::mutex> lock(this->mutex);
    this->cv.notify_one();
    return pck->get_future();
}
```
1、将一个任务通过`package_task`进行包装起来，作为一个异步任务，之后就和调用一个普通函数一样简单。
2、这个任务通过`bind`进行绑定，留出第一个参数，作为传入参数，之后通过`function`进行包装。
3、将这个包装器的指针放入到任务队列中，这个期间内部会自动上锁。
4、thread_pool全局锁进行加锁，条件变量需要用，进行唤醒其他工作线程。
5、返回packaged_task关联的future，可以用来获取返回值。

#### pop 将任务从任务队列队首移除
``` cpp
// pops a functional wrapper to the original function
std::function<void(int)> pop() {
    std::function<void(int id)> * _f = nullptr;
    this->q.pop(_f);
    std::unique_ptr<std::function<void(int id)>> func(_f);
    std::function<void(int)> f;
    if (_f)
        f = *_f;
    return f;
}
```
1、首先用用一个可调用对象包装器的指针_f获取任务队列的对头成员。
2、然后用一个unique_ptr去接收这个_f，可以在函数结束以及之后出现异常时仍然可以销毁该指针所指向资源，避免内存泄漏。
3、然后通过值传递给一个可调用对象包装器f，最后返回该包装器。
#### stop关闭线程池
![](img/Pasted%20image%2020260408085419.png)
是否等待所有任务完成时关闭工作线程；如果为true，则isDone为true，一直运行到任务队列为空，工作线程退出；如果为false，则让flags所有成员设置为true，强制让工作线程退出，并且将任务队列清除。
唤醒所有的工作线程。
等待全部工作线程完成。
清除任务队列，工作线程数组，工作线程flag数组。
#### set_thread 将工作线程lambda表达式放入到线程中
![](img/Pasted%20image%2020260408083054.png)
set_thread部分：
1、通过shared_ptr共享这个`this->flag[i]`给flag，之后就不需要反复使用`this->flag[i]`来获取，只需要一个`*flag`就行了。
2、将一个lamdba表达式赋值给f，然后将f放入到相应的线程当中。
lamdba表达式部分：
1、将`*flag`起一个别名`_flag`。
2、从任务队列中pop一个任务给_f，isPop表示有没有返回一个任务。
3、while(true)死循环，表明工作线程需要一直接收任务，并且运行任务。
while(true)部分：
while(isPop)部分：当有任务时，运行
1、通过一个unique_ptr接收_f，如果之后发现异常或者作用域结束，可以及时销毁资源。
2、运行任务，任务运行完之后，如果发现_flag=true，就退出该工作线程，表示该工作线程该销毁了；如果_flag=false，表明不需要及时销毁/不销毁，就继续获取任务队列中的任务。
1、运行到这里表明isPop为false，表明现在没任务了，下面进行wait进行等待。
2、wait上下有nWaiting的自增自减，表明不同时刻空闲工作线程数量的动态变化。
3、运行到wait时，先判断lamdba表达式返回值是否为true，其中lamdba表达式步骤：1、看现在有没有任务，如果有，则获取，并且isPop=true；2、isPop：有任务；isDone：不让工作线程进行wait，一直执行任务队列中的任务，直到执行完，再销毁；`_flag`唤醒并且立刻销毁工作线程