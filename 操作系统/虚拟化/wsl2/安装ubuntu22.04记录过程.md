# 简单安装步骤
## 1、开启虚拟化
### BIOS 设置
首先要在BIOS开启虚拟化，检测方式可以打开任务管理器查看CPU有没有开启虚拟化。
### Windows 设置
然后打开Windows功能（在控制面板 -> 程序和功能 -> 启动或关闭Windows 功能）
打开 **适用于LInux的Windows子系统** 、 **Windows虚拟机监控程序平台** 、 **虚拟机平台** 
## 2、更新wsl
```shell
wsl --update
```
## 3、查看wsl版本
```shell
wsl -v
```
## 4、列出可安装的Linux发行版
```shell
wsl --list --online
```
## 5、安装Ubuntu22.04
```shell
wsl --install Ubuntu-22.04
```
## 6、检查安装成功
新开一个命令行，输入以下内容，可以查看我们正在运行的系统
```shell
wsl -l -v
```
## 7、设置用户名、密码
在原命令行中会提示你创建新的用户名，只需要填写即可。
# 小技巧
## 1、快速进入Ubuntu22.04的方法
### 命令行方式
新建命令行，输入wsl -d 即可。如果只有一个，则直接输入wsl即可。
### 开始菜单
在开始菜单中，搜索ubuntu即可。
## 2、更新Ubuntu软件库
```shell
sudo apt update && sudo apt upgrade
```
## 3、查看正在运行的系统
```shell
wsl -l -v
```
## 4、关闭正在运行的系统
```shell
wsl --shutdown
```
## 5、迁移目录
迁移目录越早越好，之后迁移可能会导致数据丢失，软件崩溃症状。
### 1、关闭正在运行的系统
```shell
wsl --shutdown
```
### 2、导出
```shell
wsl --export Ubuntu-22.04 F:\WSL\Tar\Ubuntu.tar
```
### 3、注销Ubuntu22.04
```shell
wsl --unregister Ubuntu-22.04
```
### 4、导入我们的Tar到我们想要的目录
```shell
wsl --import Ubuntu-22.04 F:\WSL\Ubuntu2204 F:\WSL\Tar\Ubuntu.tar --version 2
```
### 5、直接进入Ubuntu
```shell
wsl -d Ubuntu-22.04
```
## 6、迁移目录后的问题
迁移目录后，我们发现我们登录进去的是root，而我们的用户名是username
### 查看我们的用户名
我们可能会忘记我们的用户名，我们在root下输入下面的命令，就可以看到我们以用户名起名的目录
```shell
cd /home
```
### 修改/etc/wsl.conf文件
输入以下命令，利用vim修改wsl.conf文件
```shell
vim /etc/wsl.conf
```
然后输入i，进入插入模式，在最后插入文本 **（注意：your_username是你设置的用户名）** ，输完之后按下esc键，输入:wq，按下回车。
```shell
[user]
default=your_username
```
### 最后的收尾
cmd中退出ubuntu命令
```shell
exit
```
然后我们关闭我们运行的系统
```shell
wsl --shutdown
```
重新进入，我们就可以发现我们用户名了
```
wsl
```
## 7、确认Ubuntu在wsl的安装正确性
安装两个软件：**neofetch** 、 **htop** 
```shell
sudo apt install neofetch htop
```
安装完后运行，如果没有什么问题，则安装挺成功的。
neofetch可以查看系统信息。
htop是一款交互式管理进程的命令行工具。
