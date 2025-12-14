# 前言

想用C++弄一个中间层，之前没碰过C++，感觉C++包管理还有导库有点麻烦，看能不能通过wasm在nodejs使用npm/pnpm这样包管理工具中的库。
如果顺利的话，作为理想中无依赖中间件的C++库就可以抽象层许多抽象层，先用nodejs去实现这些抽象接口，之后如果对C++的一般网络库、日志库、配置库熟悉之后再重新使用也不迟，并且在UE5中，也可以通过使用UE5现有的UE5的库实现抽象接口，真实理想中的理想。
不过就是感觉之后调试起来是不是有点麻烦。

# 参考

[编译 C/C++ 为 WebAssembly - WebAssembly | MDN](https://developer.mozilla.org/zh-CN/docs/WebAssembly/Guides/C_to_Wasm)

# Windows环境搭建

