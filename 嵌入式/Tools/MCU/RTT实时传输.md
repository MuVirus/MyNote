
# RTT介绍

> 参考：
> https://www.segger.com/products/debug-probes/j-link/technology/about-real-time-transfer/

> SEGGER 的实时传输（RTT）是嵌入式应用中系统监控和交互式用户输入输出的成熟技术。它结合了软件运营和半托管的优势，且性能非常高。

> 通过 RTT，可以从目标微控制器输出信息，并以极高速度向应用程序发送输入，而不影响目标的实时行为。SEGGER RTT 可与任何 [J-Link](https://www.segger.com/products/debug-probes/j-link/) 型号及任何支持后台内存访问的目标处理器（即 Cortex-M 和 RX 目标）使用。

就是可以将RTT当成调试信息日志来使用，用法和printf类似，并且几乎不阻塞主程序。

SEGGER上位机开发主要针对的是Jlink，通过
https://www.segger.com/downloads/jlink/ 下载jlink驱动，就可以获得上位机与针对于MCU的库文件。

至于用其他DAPlink、STLink也可以使用jlink提供的MCU的库文件。不过上位机就需要替换了。

# 环境配置

## Jlink驱动安装

下载地址
https://www.segger.com/downloads/jlink/

默认的话，就直接安装（默认继续应该就可以了）。
![](img/Pasted%20image%2020251229150910.png)
安装完之后，如果可能像我这样没有看到RTT文件夹。（可能我是新版本问题？通过JLink_V896目录得知当然为jlink v8.96）
![](img/Pasted%20image%2020251229151034.png)
我们选择安装旧版本的，我这里不知道哪个旧版本有。

> 参考：
> https://www.bilibili.com/video/BV1TLmFBFEe2/
> https://zhuanlan.zhihu.com/p/1957767517675184310

第一个是V6.44版本，第二个是V8.30版本。我这里先看的第一个，固然安装的是V6.44版本。
我说的参考链接内容有点无语的一点就是，让我们下载的版本和它自己用的版本不一样。

这回就有了。
![](img/Pasted%20image%2020251229151657.png)

该解压缩的解压缩，可以看到这个目录。在MCU使用的是RTT目录下的。我们直接将该文件目录放到我们的MCU工程目录即可。接下来操作便不细说。

![](img/Pasted%20image%2020251229151750.png)

## Jlink RTT上位机

直接使用jlink驱动安装目录下的`JLinkRTTViewer.exe`即可。

## STLink RTT上位机

> 我这里使用的是PyOCD，用的是uv包管理工具（可以管理python版本和pip包）

> 参考:
> 1、将如何使用PyOCD RTT
> https://blog.csdn.net/qq_36973838/article/details/131564806
> 2、PyOCD基本操作（讲了一些坑）
> https://blog.csdn.net/desert187/article/details/144031136



