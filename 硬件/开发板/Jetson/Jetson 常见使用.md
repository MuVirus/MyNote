# 品立/丽台 常见问题
[plink-ai.com/cn/list-5-92.html](http://www.plink-ai.com/cn/list-5-92.html)

# 品立/丽台  Jetson Orin Nano 4GB 更新到L4T 36.4.0 版本

L4T 36.4.0对应的系统版本是Ubuntu22.04，5.15.148-tegra。

参考官方：
[plink-jetpack: Plink AI carrier board BSP - Gitee.com](https://gitee.com/plink718/plink-jetpack/tree/master/flashPatch/36.4/Orin-Nano/Y-C6#https://gitee.com/link?target=https%3A%2F%2Fdeveloper.nvidia.com%2Fembedded%2Fjetson-linux-archive)

# 没有iwlwifi*.ko*

[Jetson Orin NX安装(烧写)Ubuntu系统+解决识别wifi模块问题_jetson orin nx wifi-CSDN博客](https://blog.csdn.net/taoshiwu/article/details/142585730)

[[jetson]jetson更新系统时候提示nvidia-l4t-bootloader的错误_在处理时有错误发生: nvidia-l4t-bootloader-CSDN博客](https://blog.csdn.net/FL1623863129/article/details/128092493)
# 输入法
[jetson nano 超详细安装中文输入法包括找不到安装包换源_jetson nano 中文输入法-CSDN博客](https://blog.csdn.net/m0_75034220/article/details/139084367)

# Jetson Orin Nano还原
当然可以！将 Ubuntu 的软件源更换为国内镜像（如清华、中科大、阿里云等）可以**显著提升软件下载和更新速度**，尤其在国内网络环境下。

以下是针对您当前使用 `ubuntu-ports`（适用于 ARM 等架构）的系统，更换为国内镜像的详细步骤：

---

### ✅ 一、备份原始源文件（重要！）

```bash
sudo cp /etc/apt/sources.list /etc/apt/sources.list.bak
```

> 这一步是为了防止配置出错时可以快速恢复。

---

### ✅ 二、编辑软件源配置文件

```bash
sudo nano /etc/apt/sources.list
```

> 或使用 `vim`：`sudo vim /etc/apt/sources.list`

---

### ✅ 三、替换为国内镜像源（任选其一）

#### 🌐 推荐 1：**清华大学 TUNA 镜像源**（速度快，稳定性好）

```bash
# 清华源 - Ubuntu Ports (适用于 ARM/ARM64 等架构)
deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu-ports/ jammy main restricted universe multiverse
deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu-ports/ jammy-updates main restricted universe multiverse
deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu-ports/ jammy-security main restricted universe multiverse
deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu-ports/ jammy-backports main restricted universe multiverse

# 源码（可选，开发者使用）
# deb-src https://mirrors.tuna.tsinghua.edu.cn/ubuntu-ports/ jammy main restricted universe multiverse
# deb-src https://mirrors.tuna.tsinghua.edu.cn/ubuntu-ports/ jammy-updates main restricted universe multiverse
# deb-src https://mirrors.tuna.tsinghua.edu.cn/ubuntu-ports/ jammy-security main restricted universe multiverse
# deb-src https://mirrors.tuna.tsinghua.edu.cn/ubuntu-ports/ jammy-backports main restricted universe multiverse
```

---

#### 🌐 推荐 2：**中国科学技术大学 USTC 镜像源**

```bash
# 中科大源 - Ubuntu Ports
deb https://mirrors.ustc.edu.cn/ubuntu-ports/ jammy main restricted universe multiverse
deb https://mirrors.ustc.edu.cn/ubuntu-ports/ jammy-updates main restricted universe multiverse
deb https://mirrors.ustc.edu.cn/ubuntu-ports/ jammy-security main restricted universe multiverse
deb https://mirrors.ustc.edu.cn/ubuntu-ports/ jammy-backports main restricted universe multiverse
```

---

#### 🌐 推荐 3：**阿里云镜像源**

```bash
# 阿里云源 - Ubuntu Ports
deb http://mirrors.aliyun.com/ubuntu-ports/ jammy main restricted universe multiverse
deb http://mirrors.aliyun.com/ubuntu-ports/ jammy-updates main restricted universe multiverse
deb http://mirrors.aliyun.com/ubuntu-ports/ jammy-security main restricted universe multiverse
deb http://mirrors.aliyun.com/ubuntu-ports/ jammy-backports main restricted universe multiverse
```

> ⚠️ 注意：阿里云使用的是 `http`，但通常也支持 `https`，建议优先使用 `https`。

---

### ✅ 四、保存并退出编辑器

- 如果使用 `nano`：按 `Ctrl + O` 保存 → 回车确认 → `Ctrl + X` 退出。
- 如果使用 `vim`：按 `Esc` → 输入 `:wq` → 回车。

---

### ✅ 五、更新软件包列表

```bash
sudo apt clean
sudo apt update
```

> 如果没有报错，说明更换成功！如果出现 GPG 错误，可能需要导入密钥（见下方补充）。

---

### ✅ 六、升级系统（可选）

```bash
sudo apt upgrade -y
```

> 建议定期执行，保持系统安全和稳定。

---

### 🔐 补充：如果出现 GPG 密钥错误

有时会提示类似：

```
NO_PUBKEY XXXXXXXX
```

可以运行以下命令导入密钥（以清华源为例）：

```bash
sudo apt-key adv --keyserver keyserver.ubuntu.com --recv-keys 4F4EA0AAE5267A6C
```

> ⚠️ 注意：在较新版本 Ubuntu 中 `apt-key` 已被弃用，推荐使用 `signed-by` 方式，但临时解决可用上述命令。

---

### 📌 总结

| 步骤 | 操作 |
|------|------|
| 1 | 备份原始文件 |
| 2 | 编辑 `/etc/apt/sources.list` |
| 3 | 替换为国内镜像源（清华/中科大/阿里云） |
| 4 | 保存并运行 `sudo apt update` |

---

✅ **推荐选择**：
- 一般用户：**清华大学镜像源**
- 教育网用户：**中科大或清华**
- 阿里云服务器：**阿里云镜像源**

---

如果您不确定自己的系统架构或版本，可以运行：

```bash
lsb_release -c    # 查看版本代号（如 jammy）
uname -m          # 查看系统架构（如 aarch64）
```

如有问题，欢迎继续提问！😊

# 虚拟内存

## 开启
对于 **Jetson Orin Nano 4GB** 版本，**强烈建议开启虚拟内存（Swap）**。

原因如下：

1.  **物理内存有限**：4GB LPDDR5 内存虽然性能不错，但在运行现代AI应用、深度学习模型、Docker容器或桌面环境时，很容易被耗尽。尤其是系统内存和GPU显存是共享的，AI计算会占用大量内存。

2.  **避免系统崩溃**：当物理内存用尽时，系统可能会开始终止进程（OOM Killer），导致你的应用或开发环境意外关闭。Swap作为“安全垫”，可以将不活跃的内存数据转移到磁盘，防止这种情况发生。

3.  **开发和运行需求**：Jetson Orin Nano 的主要用途是AI和机器人开发。许多AI框架（如TensorFlow, PyTorch）和模型在加载时就会占用大量内存。开启Swap是确保这些应用能顺利运行的基本操作。

4.  **性能权衡**：虽然磁盘Swap的速度比物理内存慢，但总比程序崩溃或系统卡死要好。你可以通过以下方式优化：
    *   **优先使用ZRAM**：Orin Nano 默认使用zram（压缩的内存Swap），速度很快。配置额外的磁盘Swap时，可以设置较低的优先级（如 `pri=10`），让系统优先使用zram。
    *   **使用高速存储**：将Swap文件创建在NVMe SSD上，而不是慢速的SD卡或eMMC，可以显著提升Swap性能。

**如何开启（简要步骤）：**

根据知识库中的信息，可以创建一个8GB的Swap文件：

```bash
# 1. 创建swap目录并设置权限
sudo mkdir -p /swap
sudo chmod 700 /swap

# 2. 创建8GB的swap文件 (使用fallocate或dd)
sudo fallocate -l 8G /swap/swapfile2
# 如果fallocate报错，用dd命令替代：
# sudo dd if=/dev/zero of=/swap/swapfile2 bs=1M count=8192 status=progress

# 3. 设置文件权限并格式化为swap
sudo chmod 600 /swap/swapfile2
sudo mkswap /swap/swapfile2

# 4. 启用swap
sudo swapon /swap/swapfile2

# 5. 添加到/etc/fstab以实现开机自动挂载
echo '/swap/swapfile2 none swap sw,pri=10,nofail 0 0' | sudo tee -a /etc/fstab

# 6. 应用fstab更改
sudo mount -a
```

**额外建议：**

*   **关闭桌面环境**：如知识库中提到的，运行 `sudo init 3` 可以临时关闭桌面，节省近500MB-800MB的内存，对于内存紧张的4GB版本非常有效。
*   **监控内存使用**：使用 `jtop` 命令（Jetson专属的系统监控工具）来实时查看内存、CPU、GPU的使用情况。

**总结**：对于Jetson Orin Nano 4GB，开启虚拟内存不仅是建议，几乎是**必须的**，它是保证系统稳定性和能够运行复杂AI应用的关键步骤。

## 修改
在 Linux 系统中，**你不能直接“实时”修改一个已存在的 swap 文件的大小**。但是，你可以通过一个标准且安全的流程来“动态调整”虚拟内存的大小，这个过程对系统运行的影响很小，可以视为“准实时”。

以下是针对 **Jetson Orin Nano** 或其他 Linux 系统的标准操作流程：

---

### ✅ **调整 Swap 文件大小的标准流程**

假设你想将 swap 大小从 8GB 调整为 16GB。

#### 步骤 1：禁用当前的 swap 文件

```bash
sudo swapoff /swap/swapfile2
```

> **说明**：这会将当前 swap 中的数据移回物理内存。只要你的物理内存（RAM）足够容纳这些数据，系统不会中断。对于 Orin Nano 4GB 版本，建议在轻负载或空闲时操作。

#### 步骤 2：删除旧的 swap 文件

```bash
sudo rm /swap/swapfile2
```

#### 步骤 3：创建新的、更大（或更小）的 swap 文件

```bash
# 创建 16GB 的新 swap 文件
sudo fallocate -l 16G /swap/swapfile2

# 设置权限
sudo chmod 600 /swap/swapfile2
```

> 💡 提示：如果 `fallocate` 有问题，可以用 `dd` 替代：
> ```bash
> sudo dd if=/dev/zero of=/swap/swapfile2 bs=1M count=16384 status=progress
> ```

#### 步骤 4：格式化为 swap

```bash
sudo mkswap /swap/swapfile2
```

#### 步骤 5：重新启用 swap

```bash
sudo swapon /swap/swapfile2
```

#### 步骤 6：验证

```bash
sudo swapon --show
# 或
free -h
```

你应该看到 swap 大小已经更新为 16G。

---

### ⚠️ 注意事项

1.  **`swapoff` 需要足够物理内存**  
    执行 `swapoff` 时，系统会把 swap 中的数据读回 RAM。如果物理内存不足，可能导致系统卡顿甚至 OOM（内存溢出）。建议在系统空闲时操作。

2.  **不要删除正在使用的 swap**  
    必须先 `swapoff`，再删除文件，否则会出错。

3.  **`/etc/fstab` 不需要修改**  
    因为你使用的是同一个路径 `/swap/swapfile2`，所以之前添加到 `/etc/fstab` 的开机自动挂载条目仍然有效，无需更改。

4.  **ZRAM 优先级更高（可选优化）**  
    Jetson 默认启用了 ZRAM（压缩内存作为 swap）。你可以通过设置优先级让系统优先使用 ZRAM，而磁盘 swap 作为后备：
    ```bash
    # 查看当前 swap 优先级
    cat /proc/swaps

    # 如果你想让磁盘 swap 优先级低于 ZRAM，可以修改 /etc/fstab 中的 pri 值（如 pri=1）
    # 例如：/swap/swapfile2 none swap sw,pri=1,nofail 0 0
    ```

---

### ✅ 总结

虽然不能“直接修改”swap文件大小，但通过 **`swapoff → 删除 → 重建 → swapon`** 这四步，你可以在几秒内完成 swap 大小的调整，对系统影响极小，完全可以满足“实时调整”的需求。

📌 **核心命令回顾**：
```bash
sudo swapoff /swap/swapfile2
sudo rm /swap/swapfile2
sudo fallocate -l 16G /swap/swapfile2
sudo chmod 600 /swap/swapfile2
sudo mkswap /swap/swapfile2
sudo swapon /swap/swapfile2
```

