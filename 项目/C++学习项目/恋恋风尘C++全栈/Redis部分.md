
## 环境配置
### redis服务器
#### windows
[redis-windows/redis-windows: Redis 6.0.20 6.2.18 7.0.15 7.2.8 7.4.3 8.0.0 for Windows](https://github.com/redis-windows/redis-windows)
使用的是redis-windows，选用的msys2的Service版本，更新比较快。

### C++
使用**vcpkg**进行包管理，C++库选用的是**redis++**(redis plus plus)
听说hiredis会污染Windows下的socket API，虽然redis++是基于hiredis的，但是通过C++作用域去限定了作用访问，避免了API污染。
安装命令
```cmd
vcpkg install redis-plus-plus
```
安装完成后自动输出通用cmake配置
```cmake
# this is heuristically generated, and may not be correct
find_package(redis++ CONFIG REQUIRED)
target_link_libraries(main PRIVATE redis++::redis++)
```

