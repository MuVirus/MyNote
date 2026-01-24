# Protothreads 库参考

本文档整理了 Protothreads 库中的各种简写、宏定义及其含义，帮助开发者快速理解和使用该库。

## 核心数据结构

### struct pt
原型线程控制结构，包含一个本地继续（local continuation）变量。

```c
struct pt {
  lc_t lc;
};
```

### struct pt_sem
信号量结构，用于实现计数信号量。

```c
struct pt_sem {
  unsigned int count;
};
```

## 状态常量

| 常量 | 值 | 含义 |
|------|-----|------|
| PT_WAITING | 0 | 线程正在等待某个条件 |
| PT_YIELDED | 1 | 线程已让步，暂时放弃执行 |
| PT_EXITED | 2 | 线程已退出 |
| PT_ENDED | 3 | 线程已结束 |

## 初始化相关

### PT_INIT(pt)
初始化原型线程控制结构。

**参数**：
- pt：指向原型线程控制结构的指针

**用法**：
```c
struct pt my_pt;
PT_INIT(&my_pt);
```

## 线程声明和定义

### PT_THREAD(name_args)
声明一个原型线程函数。

**参数**：
- name_args：函数名和参数列表

**用法**：
```c
PT_THREAD(my_thread(struct pt *pt))
{
  // 线程代码
}
```

### PT_BEGIN(pt)
声明原型线程的开始。

**参数**：
- pt：指向原型线程控制结构的指针

**用法**：
```c
PT_THREAD(my_thread(struct pt *pt))
{
  PT_BEGIN(pt);
  // 线程代码
  PT_END(pt);
}
```

### PT_END(pt)
声明原型线程的结束。

**参数**：
- pt：指向原型线程控制结构的指针

**用法**：
```c
PT_THREAD(my_thread(struct pt *pt))
{
  PT_BEGIN(pt);
  // 线程代码
  PT_END(pt);
}
```

## 阻塞等待

### PT_WAIT_UNTIL(pt, condition)
阻塞线程，直到条件为真。

**参数**：
- pt：指向原型线程控制结构的指针
- condition：条件表达式

**用法**：
```c
PT_WAIT_UNTIL(pt, flag == 1);
```

### PT_WAIT_WHILE(pt, condition)
阻塞线程，当条件为真时继续阻塞。

**参数**：
- pt：指向原型线程控制结构的指针
- condition：条件表达式

**用法**：
```c
PT_WAIT_WHILE(pt, flag == 0);
```

## 层次化原型线程

### PT_WAIT_THREAD(pt, thread)
阻塞当前线程，直到子线程完成。

**参数**：
- pt：指向原型线程控制结构的指针
- thread：子线程函数调用

**用法**：
```c
struct pt child_pt;
PT_INIT(&child_pt);
PT_WAIT_THREAD(pt, child_thread(&child_pt));
```

### PT_SPAWN(pt, child, thread)
创建并启动子线程，直到子线程退出。

**参数**：
- pt：指向原型线程控制结构的指针
- child：指向子线程控制结构的指针
- thread：子线程函数调用

**用法**：
```c
struct pt child_pt;
PT_SPAWN(pt, &child_pt, child_thread(&child_pt));
```

## 退出和重启

### PT_RESTART(pt)
重启原型线程，使其从 PT_BEGIN 处重新开始执行。

**参数**：
- pt：指向原型线程控制结构的指针

**用法**：
```c
if (error) {
  PT_RESTART(pt);
}
```

### PT_EXIT(pt)
退出原型线程。

**参数**：
- pt：指向原型线程控制结构的指针

**用法**：
```c
if (done) {
  PT_EXIT(pt);
}
```

## 调度

### PT_SCHEDULE(f)
调度原型线程，返回非零值表示线程正在运行，零表示线程已退出。

**参数**：
- f：原型线程函数调用

**用法**：
```c
while (PT_SCHEDULE(my_thread(&my_pt))) {
  // 其他代码
}
```

## 让步

### PT_YIELD(pt)
使当前线程让步，允许其他处理。

**参数**：
- pt：指向原型线程控制结构的指针

**用法**：
```c
PT_YIELD(pt);
```

### PT_YIELD_UNTIL(pt, condition)
使线程让步，直到条件为真。

**参数**：
- pt：指向原型线程控制结构的指针
- condition：条件表达式

**用法**：
```c
PT_YIELD_UNTIL(pt, flag == 1);
```

## 信号量相关

### PT_SEM_INIT(s, c)
初始化信号量。

**参数**：
- s：指向信号量结构的指针
- c：信号量的初始计数

**用法**：
```c
struct pt_sem sem;
PT_SEM_INIT(&sem, 1);
```

### PT_SEM_WAIT(pt, s)
等待信号量，当信号量计数为零时阻塞。

**参数**：
- pt：指向原型线程控制结构的指针
- s：指向信号量结构的指针

**用法**：
```c
PT_SEM_WAIT(pt, &sem);
```

### PT_SEM_SIGNAL(pt, s)
发送信号量，增加信号量计数。

**参数**：
- pt：指向原型线程控制结构的指针
- s：指向信号量结构的指针

**用法**：
```c
PT_SEM_SIGNAL(pt, &sem);
```

## 代码示例

### 基本示例

```c
#include "pt.h"

static int flag;

PT_THREAD(my_thread(struct pt *pt))
{
  PT_BEGIN(pt);
  
  while (1) {
    PT_WAIT_UNTIL(pt, flag != 0);
    printf("Flag is set!\n");
    flag = 0;
  }
  
  PT_END(pt);
}

int main(void)
{
  struct pt pt;
  PT_INIT(&pt);
  
  while (PT_SCHEDULE(my_thread(&pt))) {
    // 其他代码
    flag = 1; // 设置标志，唤醒线程
  }
  
  return 0;
}
```

### 生产者-消费者示例

```c
#include "pt.h"
#include "pt-sem.h"

#define BUFSIZE 8

static int buffer[BUFSIZE];
static int bufptr;
static struct pt_sem full, empty;

PT_THREAD(producer(struct pt *pt))
{
  static int item = 0;
  
  PT_BEGIN(pt);
  
  while (1) {
    PT_SEM_WAIT(pt, &full);
    buffer[bufptr] = item++;
    bufptr = (bufptr + 1) % BUFSIZE;
    PT_SEM_SIGNAL(pt, &empty);
  }
  
  PT_END(pt);
}

PT_THREAD(consumer(struct pt *pt))
{
  static int item;
  
  PT_BEGIN(pt);
  
  while (1) {
    PT_SEM_WAIT(pt, &empty);
    item = buffer[bufptr];
    bufptr = (bufptr + 1) % BUFSIZE;
    printf("Consumed: %d\n", item);
    PT_SEM_SIGNAL(pt, &full);
  }
  
  PT_END(pt);
}

int main(void)
{
  struct pt pt_producer, pt_consumer;
  
  PT_INIT(&pt_producer);
  PT_INIT(&pt_consumer);
  
  PT_SEM_INIT(&empty, 0);
  PT_SEM_INIT(&full, BUFSIZE);
  
  while (1) {
    PT_SCHEDULE(producer(&pt_producer));
    PT_SCHEDULE(consumer(&pt_consumer));
  }
  
  return 0;
}
```

## 总结

Protothreads 是一种轻量级的线程实现，适用于资源受限的系统，如嵌入式系统。它通过宏定义实现了类似线程的行为，但不需要完整的线程栈，因此内存占用非常小。

本文档整理的各种简写和宏定义是使用 Protothreads 的基础，掌握它们将帮助开发者更高效地使用该库实现复杂的状态机和并发逻辑。

## 注意事项

1. 原型线程中的局部变量需要使用 `static` 关键字声明，以避免它们被存储在栈上。
2. 原型线程不是真正的线程，它们在单个线程中执行，通过协作式调度实现并发。
3. 原型线程不能使用阻塞系统调用，因为这会阻塞整个进程。
4. 原型线程的执行顺序是确定性的，取决于它们的调度顺序。

通过合理使用本文档中整理的各种简写和宏定义，开发者可以在资源受限的环境中实现复杂的并发逻辑，而不需要完整的线程支持。



# Local Continuations (LC) 参考

本文档整理了 Local Continuations (LC) 库中的各种宏定义、实现原理及其用法，帮助开发者理解 Protothreads 库的底层机制。

## 什么是 Local Continuations？

Local Continuations（本地继续）是 Protothreads 库的基础，用于实现轻量级的协程机制。本地继续允许在函数中设置一个执行点，之后可以从该点恢复执行，而不需要保存完整的调用栈。

## 核心数据类型

### lc_t
本地继续的类型，在 switch 实现中定义为无符号短整型。

```c
typedef unsigned short lc_t;
```

## 核心宏定义

### LC_INIT(s)
初始化本地继续变量。

**定义**：
```c
#define LC_INIT(s) s = 0;
```

**参数**：
- s：本地继续变量

**作用**：
将本地继续变量初始化为 0，重置其状态。

**用法**：
```c
lc_t lc;
LC_INIT(lc);
```

### LC_RESUME(s)
恢复本地继续，从上次设置的点开始执行。

**定义**：
```c
#define LC_RESUME(s) switch(s) { case 0:
```

**参数**：
- s：本地继续变量

**作用**：
使用 switch 语句跳转到上次 LC_SET 设置的位置。如果是首次执行，则从 case 0 开始。

**用法**：
```c
LC_RESUME(lc);
// 代码执行...
```

### LC_SET(s)
设置本地继续，保存当前执行位置。

**定义**：
```c
#define LC_SET(s) s = __LINE__; case __LINE__:
```

**参数**：
- s：本地继续变量

**作用**：
1. 将本地继续变量设置为当前代码行号
2. 添加一个 case 标签，对应当前行号

**注意**：
- 此实现依赖于 `__LINE__` 宏，它会被编译器替换为当前代码行号
- 不能在另一个 switch 语句内部使用 LC_SET，否则会导致错误

**用法**：
```c
LC_SET(lc);
// 代码执行...
```

### LC_END(s)
结束本地继续，关闭 switch 语句。

**定义**：
```c
#define LC_END(s) }
```

**参数**：
- s：本地继续变量

**作用**：
关闭由 LC_RESUME 打开的 switch 语句。

**用法**：
```c
// 代码执行...
LC_END(lc);
```

## 实现原理

Local Continuations 的 switch 实现基于以下原理：

1. **利用 switch 语句的跳转能力**：switch 语句可以直接跳转到其内部的任何 case 标签，无论该标签位于何种控制结构（如 if、while 等）内部。

2. **使用行号作为状态标识**：`__LINE__` 宏提供了唯一的行号，用作 switch 语句的 case 标签，标识代码中的特定位置。

3. **状态变量保存执行位置**：本地继续变量 (lc_t) 用于保存最后执行的 LC_SET 语句的行号，作为下次执行时的入口点。

## 工作流程

1. **初始化**：调用 `LC_INIT(lc)` 将本地继续变量设置为 0。

2. **首次执行**：调用 `LC_RESUME(lc)` 展开为 `switch(lc) { case 0:`，执行从 case 0 开始。

3. **设置继续点**：当执行到 `LC_SET(lc)` 时，将当前行号存储到 lc 变量中，并添加一个对应的 case 标签。

4. **退出与恢复**：当函数返回时，lc 变量保持其值。下次调用 `LC_RESUME(lc)` 时，switch 语句会直接跳转到上次设置的 case 标签处，从该点继续执行。

5. **结束**：调用 `LC_END(lc)` 关闭 switch 语句。

## 代码示例

### 基本示例

```c
#include "lc.h"

void example_function(void) {
  static lc_t lc;  // 必须是静态的，否则会在函数返回时丢失
  static int count = 0;
  
  LC_RESUME(lc);
  
  printf("开始执行\n");
  
  count++;
  printf("计数: %d\n", count);
  
  LC_SET(lc);  // 设置继续点
  printf("第一次执行到这里\n");
  
  count++;
  printf("计数: %d\n", count);
  
  LC_SET(lc);  // 设置另一个继续点
  printf("第二次执行到这里\n");
  
  count++;
  printf("计数: %d\n", count);
  
  LC_END(lc);
}

int main(void) {
  printf("=== 第一次调用 ===\n");
  example_function();
  
  printf("\n=== 第二次调用 ===\n");
  example_function();
  
  printf("\n=== 第三次调用 ===\n");
  example_function();
  
  return 0;
}
```

**预期输出**：
```
=== 第一次调用 ===
开始执行
计数: 1
第一次执行到这里
计数: 2
第二次执行到这里
计数: 3

=== 第二次调用 ===
第二次执行到这里
计数: 4

=== 第三次调用 ===
第二次执行到这里
计数: 5
```

### 在 Protothreads 中的应用

Local Continuations 是 Protothreads 库的核心，以下是 Protothreads 如何使用 LC 的简化示例：

```c
#include "pt.h"

static struct pt pt;
static int flag = 0;

static int protothread_example(struct pt *pt) {
  PT_BEGIN(pt);  // 展开为 LC_RESUME(pt->lc)
  
  while (1) {
    PT_WAIT_UNTIL(pt, flag != 0);  // 展开包含 LC_SET
    printf("Flag is set!\n");
    flag = 0;
  }
  
  PT_END(pt);  // 展开为 LC_END(pt->lc)
}

int main(void) {
  PT_INIT(&pt);  // 展开为 LC_INIT(pt->lc)
  
  while (1) {
    protothread_example(&pt);
    // 其他代码
    if (some_condition) {
      flag = 1;
    }
  }
  
  return 0;
}
```

## 局限性

1. **不能在 switch 语句内使用**：由于实现基于 switch 语句，不能在另一个 switch 语句内部使用 LC_SET。

2. **不保存局部变量**：本地继续不会保存函数的局部变量（自动变量），只保存程序计数器的位置。因此，需要使用 static 变量来保持状态。

3. **行号限制**：由于 lc_t 被定义为 unsigned short，最大行号限制为 65535。

4. **调试困难**：由于使用了 switch 语句和行号跳转，调试时可能会看到不直观的执行流程。

## 总结

Local Continuations 是一种巧妙的技术，通过 C 语言的 switch 语句实现了轻量级的协程机制。虽然有一些局限性，但其简洁的实现和低内存开销使其成为嵌入式系统和资源受限环境的理想选择。

在 Protothreads 库中，Local Continuations 被封装在更高级的宏后面，提供了更友好的协程编程接口，同时保持了底层的高效性。

## 与 Protothreads 的关系

Local Continuations 是 Protothreads 的基础，Protothreads 库通过以下方式使用 LC：

1. **PT_BEGIN**：展开为 `{ char PT_YIELD_FLAG = 1; LC_RESUME((pt)->lc)`
2. **PT_END**：展开包含 `LC_END((pt)->lc)`
3. **PT_WAIT_UNTIL**：展开包含 `LC_SET((pt)->lc)`
4. **PT_INIT**：展开为 `LC_INIT((pt)->lc)`

通过这些封装，Protothreads 提供了更高级、更易用的协程编程接口，同时保持了 Local Continuations 的轻量级特性。