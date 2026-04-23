
## 一、介绍
### 1、PostMessage介绍
> 参考资料：
> [PostMessageA 函数 （winuser.h） - Win32 apps | Microsoft Learn](https://learn.microsoft.com/zh-cn/windows/win32/api/winuser/nf-winuser-postmessagea)

将消息**置于消息队列**中，该消息与创建指定窗口的线程相关联，并返回，而无需等待线程处理消息。
**语法**
``` cpp
BOOL PostMessageA(
  [in, optional] HWND   hWnd,
  [in]           UINT   Msg,
  [in]           WPARAM wParam,
  [in]           LPARAM lParam
);
```
其中Msg就是要传递到消息队列的消息事件
### 2、SendMessage介绍
>参考资料：
>[sendMessage 函数 (winuser.h) - Win32 apps | Microsoft Learn](https://learn.microsoft.com/zh-cn/windows/win32/api/winuser/nf-winuser-sendmessage)

将指定的消息发送到一个或多个窗口。 SendMessage 函数**调用**指定窗口的**窗口过程**，**在窗口过程处理消息之前不会返回** 。
**语法：**
``` cpp
LRESULT SendMessage(
  [in] HWND   hWnd,
  [in] UINT   Msg,
  [in] WPARAM wParam,
  [in] LPARAM lParam
);
```
其中Msg就是要传递到窗口过程的消息事件。
### 3、发布消息和发送消息对比
> 参考资料：
> [关于消息和消息队列 - Win32 apps | Microsoft Learn](https://learn.microsoft.com/zh-cn/windows/win32/winmsg/about-messages-and-message-queues#posting-and-sending-messages)

| 特性   | SendMessage       | PostMessage       |
| ---- | ----------------- | ----------------- |
| 通信模式 | 同步                | 异步                |
| 执行流程 | 直接调用目标窗口的窗口过程     | 将消息放入目标线程的消息队列    |
| 消息队列 | 不经过，直接调用窗口过程      | 经过，通过GetMessage取出 |
| 返回值  | LRESULT（窗口过程的返回值） | BOOL              |
| 线程状态 | 阻塞，直到窗口过程执行完      | 不阻塞，继续执行后续代码      |
## 二、项目体现
下面是PostMessage和SendMessage在画板项目中的简单体现
### 1、快捷键Q退出
#### 写法
可以看到，在窗口过程中，当uMsg为键盘事件时，并且wParam参数为'Q'时，就会触发代码设定的SendMessage参数，将WM_DESTROY发送到窗口过程当中。
![](img/Pasted%20image%2020260423095619.png)
#### 函数过程
当执行到SendMessage的时候，通过调试可以明显看出，下一个调用的就是窗口过程WindowsProc，所以说SendMessage是阻塞的。
**![](img/Pasted%20image%2020260423100029.png)**
#### 注意
>参考：
>[窗口消息（Win32 和 C++ 入门） - Win32 apps | Microsoft Learn](https://learn.microsoft.com/zh-cn/windows/win32/learnwin32/window-messages#the-message-loop)

当然为什么不发送WM_QUIT，而要发送WM_DESTROY，是因为函数过程中switch-case中就没有WM_QUIT，所以说执行执行DefWindowProc了，会返回无法预料到的值。

当然也可以想窗口过程传递WM_CLOSE，在 WM_CLOSE的情况下， DefWindowProc 会自动调用 DestroyWindow。参考[关闭窗口 - Win32 apps | Microsoft Learn](https://learn.microsoft.com/zh-cn/windows/win32/learnwin32/closing-the-window)
![685](img/Pasted%20image%2020260423110142.png)
### 2、其他PostMessage部分
代码中有很多地方是用到了PostMessage的，比如PostQuitMessage，他相当于PostMessage(hwnd, WM_QUIT, 0, 0)，参考[关闭窗口 - Win32 apps | Microsoft Learn](https://learn.microsoft.com/zh-cn/windows/win32/learnwin32/closing-the-window)
因为他是不阻塞的，直接放到目标线程的消息队列中。
WM_QUIT 是一条特殊消息：它会导致 GetMessage 返回零，从而向消息循环的末尾发出信号。 参考[窗口消息（Win32 和 C++ 入门） - Win32 apps | Microsoft Learn](https://learn.microsoft.com/zh-cn/windows/win32/learnwin32/window-messages)
![624](img/Pasted%20image%2020260423111515.png)
## 三、业务线程与主线程的工作区别
在图形界面开发当中，一般主线程用来跑UI，业务任务为了不阻塞主线程创建了业务线程，其中消息处理方面的处理涉及到程序的稳定性。
### 主线程
- **职责**：处理创建窗口，处理用户输入、UI操作，维护消息循环。
- **消息处理**：可以使用Send/PostMessage，按照情况而定，SendMessage更直接，直接调用窗口过程。
- **窗口过程瓶颈处理**：不应该在主线程的窗口过程中执行耗时任务，应该使用新线程、使用线程池、异步IO调用等。参考[编写窗口过程 - Win32 apps | Microsoft Learn](https://learn.microsoft.com/zh-cn/windows/win32/learnwin32/writing-the-window-procedure#avoiding-bottlenecks-in-your-window-procedure)
### 业务线程
- **职责**：用于处理业务代码，处理耗时任务（IO等待、CPU计算）
- **消息处理**：业务线程bu
