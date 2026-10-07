---
date: '2025-12-14 15:36:58'
tags: []
title: C++ wasm nodejs 环境搭建+简单尝试
updated: '2025-12-14 16:17:17'
---
# 前言

想用C++弄一个中间层，之前没碰过C++，感觉C++包管理还有导库有点麻烦，看能不能通过wasm在nodejs使用npm/pnpm这样包管理工具中的库。
如果顺利的话，作为理想中无依赖中间件的C++库就可以抽象层许多抽象层，先用nodejs去实现这些抽象接口，之后如果对C++的一般网络库、日志库、配置库熟悉之后再重新使用也不迟，并且在UE5中，也可以通过使用UE5现有的UE5的库实现抽象接口，真实理想中的理想。
不过就是感觉之后调试起来是不是有点麻烦。

# 参考
## 英文（最新）
Emscripten官方
[Download and install — Emscripten 4.0.22-git (dev) documentation](https://emscripten.org/docs/getting_started/downloads.html)
MDN
[Compiling a new C/C++ module to WebAssembly - WebAssembly | MDN](https://developer.mozilla.org/en-US/docs/WebAssembly/Guides/C_to_Wasm)

## 中文
[编译 C/C++ 为 WebAssembly - WebAssembly | MDN](https://developer.mozilla.org/zh-CN/docs/WebAssembly/Guides/C_to_Wasm)

# Windows环境搭建

## 1. 创建工作目录

``` powershell
E:\File\Web\nodejs>mkdir cwasm
E:\File\Web\nodejs>cd cwasm
```

- 创建名为 `cwasm` 的目录作为工作区。
## 克隆 emsdk 仓库

Emscription官方提供
``` powershell
git clone https://github.com/emscripten-core/emsdk.git
```

MDN中文版（我这里用的，上当了）
``` powershell
git clone https://github.com/juj/emsdk.git
```

> ⚠️ 注意：现在官方仓库是 `emscripten-core/emsdk`，而 `juj/emsdk` 是旧仓库（作者 Jukka Jylänki）。虽然还能用，但建议用官方源。

- 下载 Emscripten SDK 的管理脚本（主要是 `emsdk.py` 和相关批处理文件）。
- 此时只下载了“安装器”，还没安装真正的编译工具。


## 成功安装最新版 Emscripten

```
emsdk install latest
```

- `latest` 是一个别名，自动解析为当前最新稳定版（这里是 `4.0.21`）。
- 实际安装的是：
    - `node-22.16.0-64bit`
    - `python-3.13.3-64bit`
    - `releases-d70a5da89b3e673bf6a482724478fc17e81e575e-64bit`（即 Emscripten 核心）

> 📦 所有依赖都由 emsdk 自动下载并解压到本地（无需你手动装 Node/Python）。

## 激活环境

```
emsdk activate latest
```

- 设置当前 shell 的环境变量，让 `emcc`、`node`、`python` 指向 emsdk 安装的版本。
- 输出提示：**仅对当前终端有效**。
- 建议加 `--permanent` 让所有新终端都生效：

```
emsdk activate latest --permanent
```

## 执行 `emsdk_env.bat`

```
emsdk_env.bat
```

- 手动加载环境变量（等价于 `activate` 的效果）。
- 如果你新开一个 CMD 窗口，就需要再次运行这个脚本，或使用 `--permanent`。
## 验证安装

```
emcc --version
```


# HelloWorld编写 (Windows)

## 代码编写

需要自己编写的文件有
- build.bat
- hello.cpp
- index.js

hello.cpp
``` cpp
#include <emscripten.h>
#include <stdio.h>

extern "C" {
    EMSCRIPTEN_KEEPALIVE
    void printHello() {
        printf("Hello from C++!\n");
    }
}
```

build.bat
``` bat
@echo off
emcc hello.cpp -o hello.js ^
  -s EXPORTED_FUNCTIONS="['_printHello']" ^
  -s EXPORTED_RUNTIME_METHODS="['ccall']" ^
  -s MODULARIZE=1 ^
  -s ENVIRONMENT=node ^
  -s EXPORT_ES6=1 ^
  --no-entry
echo 'hello' Build complete!
```

index.js
``` javascript
import createModule from './hello.js';

const Module = await createModule();

Module.ccall('printHello', null, [], []);
```

## ESModule风格

初始化pnpm
``` shell
pnpm init
```

然后在package.json中添加
``` json
"type": "module"
```

![](img/Pasted%20image%2020251214160727.png)
## 编译

首先要激活emcc，就需要使用之前的命令。（临时性）

```
emsdk_env.bat
```

如果不在同一目录下，就按照绝对路径进行激活。(临时性)

```
E:\web\emcc\emsdk_env.bat
```

然后就开始编译

```
build.bat
```

会生成

- hello.wasm
- hello.js

## 运行

``` shell
node index.js
```

![](img/Pasted%20image%2020251214160915.png)