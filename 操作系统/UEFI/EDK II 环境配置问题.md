# NMAKE : fatal error U1077: "C:\nasmnasm"
可以发现中间的斜杆不见了，我们需要在tools_def.txt(edk2/Conf/中去找)，去搜NASM_PATH（或者直接搜ENV(NASM_PREFIX)nasm
），然后在nasm前面加上斜杠
最后：
```text
*_*_*_NASM_PATH                = ENV(NASM_PREFIX)\nasm
```


# NMAKE : fatal error U1077
``` dos
UefiShellDebug1CommandsLib.lib(BufferImage.obj) : error LNK2001: ޷ⲿ memcpy
```
好像就是VS2022对其进行了优化，结果最后优化成memcpy，又因为UefiShellDebug1CommandsLib中没有memcpy，所以发生了报错。
## 解决方法一
```
build.py...
 : error F002: Failed to build module
        E:\File\Library\UEFI\edk2\ShellPkg\Application\Shell\Shell.inf [X64, VS2022, RELEASE]
```
我们可以在这个Shell.inf中加入下面一句，就可以达成局部的不优化
```
[BuildOptions]
  MSFT:*_*_*_CC_FLAGS = /Od
```
## 解决方法二
用的就是全局法，最后我还是用的这种办法（用VS2022的时候）
修改Conf/tools_def.txt
要修改两处
1、VS2022 X64（看你是Release还是Debug）=> CC_FLAGS 下 加上 /Od /GL-
2、 DLINK_FLAGS 下删除  /LTCG
# 参考

[安装EDKII环境遇到的错误解决办法汇总_edkii 模块更新失败-CSDN博客](https://blog.csdn.net/wlswls1711/article/details/120487875)

[【UEFI实战】BIOS编译过程中报错“无法解析的外部符号memcpy”_uefi buildoptions -d-CSDN博客](https://blog.csdn.net/jiangwei0512/article/details/148121598)

[Step to UEFI (136）哪里来的的 memset – WWW.LAB-Z.COM](https://www.lab-z.com/stu136/)

