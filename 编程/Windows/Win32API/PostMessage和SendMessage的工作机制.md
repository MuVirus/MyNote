
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

### 3、发布消息和发送消息
> 参考资料：
> [关于消息和消息队列 - Win32 apps | Microsoft Learn](https://learn.microsoft.com/zh-cn/windows/win32/winmsg/about-messages-and-message-queues#posting-and-sending-messages)


| 特性   | SendMessage   | PostMessage       |
| ---- | ------------- | ----------------- |
| 通信模式 | 同步            | 异步                |
| 执行流程 | 直接调用目标窗口的窗口过程 | 将消息放入目标线程的消息队列    |
| 消息队列 | 不经过，直接调用窗口过程  | 经过，通过GetMessage取出 |
| 返回值  | LRESULT       | BOOL              |
| 线程状态 | 阻塞，直到窗口过程执行完  | 不阻塞，继续执行后续代码      |
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

### 2、其他PostMessage部分
#### 
代码中有很多地方是用到了PostMessage的
1. 调用PostQuitMessage