
## 1、简介
### 1.1 PostMessage介绍
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
### 1.2 SendMessage介绍
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

### 1.3 发布消息和发送消息
> 参考资料：
> [关于消息和消息队列 - Win32 apps | Microsoft Learn](https://learn.microsoft.com/zh-cn/windows/win32/winmsg/about-messages-and-message-queues#posting-and-sending-messages)


| 特性   | SendMessage   | PostMessage       |
| ---- | ------------- | ----------------- |
| 通信模式 | 同步            | 异步                |
| 执行流程 | 直接调用目标窗口的窗口过程 | 将消息放入目标线程的消息队列    |
| 消息队列 | 不经过，直接调用窗口过程  | 经过，通过GetMessage取出 |
| 返回值  | LRESULT       | BOOL              |
| 线程状态 | 阻塞，直到窗口过程执行完  | 不阻塞，继续执行后续代码      |
