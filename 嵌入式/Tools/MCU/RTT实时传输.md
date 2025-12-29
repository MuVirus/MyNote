
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

> 我这里使用的是`PyOCD`，用的是`uv`包管理工具（可以管理python版本和pip包），你要是用全局pip或者其他虚拟工具，按照其他即可，都大同小异。

> 参考:
> 1、将如何使用PyOCD RTT
> https://blog.csdn.net/qq_36973838/article/details/131564806
> 2、PyOCD基本操作（讲了一些坑）
> https://blog.csdn.net/desert187/article/details/144031136

### 1、安装PyOCD

根据参考2，我们需要安装PyOCD版本是0.34.3。

我的全局python是Python13，不过AI告诉我Python11稳妥，我就用Python11了。

安装Python11
```
uv python install 3.11
```

创建自己的项目目录，在自己的项目目录下创建Python11虚拟环境
```
uv venv --python 3.11
```

执行脚本
```
.\.venv\Scripts\Activate.ps1
```

安装PyOcd0.34.3
```
uv add pyocd==0.34.3
```
或（我用的后者）
```
uv pip install pyocd==0.34.3
```

还需要安装
```
uv pip install setuptools
```

最后测试
```
uv run pyocd
```
或者直接（前提是每次要运行脚本）
```
pyocd
```

### 2、使用PyOCD的RTT

一般PyOCD都带有一般的功能，我们插入我们的stlink。

`查看设备列表`一般可以直接用，提前是插上stlink，然后应该可以在命令行显示STLink，有时候可以显示stlink版本。


#### 查看设备列表

```
pyocd list
```

#### 安装设备pack

查找设备pack
```
uv run pyocd pack find STM32L475VETx
```

结果

```
❯ uv run pyocd pack find STM32L475VETx
E:\File\Study\Embedded\pyocd_test\.venv\Lib\site-packages\pyocd\core\plugin.py:18: UserWarning: pkg_resources is deprecated as an API. See https://setuptools.pypa.io/en/latest/pkg_resources.html. The pkg_resources package is slated for removal as early as 2025-11-30. Refrain from using this package or pin to Setuptools<81.
  import pkg_resources
  Part            Vendor               Pack                 Version   Installed
---------------------------------------------------------------------------------
  STM32L475VETx   STMicroelectronics   Keil.STM32L4xx_DFP   3.1.0     True
```

安装

```
uv run pyocd pack install STM32L475VETx
```

#### 使用RTT

```
uv run pyocd rtt -t STM32L475VETx
```

# API

> 参考：
> https://www.segger.com/products/debug-probes/j-link/technology/about-real-time-transfer/

# 示例1

> 使用STLink、PyOCD使用RTT，来打印按键日志。

PyOCD上位机
![](img/Pasted%20image%2020251229154504.png)

MCU代码部分

![](img/Pasted%20image%2020251229154548.png)