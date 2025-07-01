参考： 
[如何在 Ubuntu 22.04 上安装并启用 OpenSSH](https://cn.linux-console.net/?p=14853)

# 快速安装使用
```shell
sudo apt update
sudo apt upgrade
sudo apt install openssh-server
sudo systemctl enable --now ssh
sudo systemctl start ssh
```
