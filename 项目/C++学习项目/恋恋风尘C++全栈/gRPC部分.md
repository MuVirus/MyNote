
## 环境配置
### vcpkg
总共来说就是这几步
1、安装vcpkg
2、使用vcpkg安装grpc
3、通过CMake或者手动VS IDE配置头文件、库文件

#### 安装命令
``` powershell
vcpkg install grpc:x64-windows
```
安装完后会输出CMake写法，自己的项目包含就行了。
``` cmake
# this is heuristically generated, and may not be correct
find_package(gRPC CONFIG REQUIRED)
# note: 15 additional targets are not displayed.
target_link_libraries(main PRIVATE gRPC::gpr gRPC::grpc gRPC::grpc++ gRPC::grpc++_alts)
```
另外，如果cmake找不到的话，可以在CMakeLists.txt的版本控制命令下一行添加一行，其中VCPKG_ROOT可以在系统环境变量中配置，为vcpkg安装目录。
```cmake
if(DEFINED ENV{VCPKG_ROOT} AND NOT DEFINED CMAKE_TOOLCHAIN_FILE)
  set(CMAKE_TOOLCHAIN_FILE "$ENV{VCPKG_ROOT}/scripts/buildsystems/vcpkg.cmake" CACHE STRING "")
endif()
```

## ProtoBuffer文件编写
包含三类：
1. 验证服务
2. 对话服务
3. 登录服务
