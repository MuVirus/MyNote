
最后按照下面链接做就行。
[Getting Source Code for PAs · GitBook](https://nju-projectn.github.io/ics-pa-gitbook/ics2025/0.6.html#rtfsc-and-enjoy)



# 问题

## 1、为什么要加`bash`执行*.sh文件

这是一个非常好的问题！你问的是：

> 为什么执行 `init.sh` 脚本时要写成 `bash init.sh nemu`，而不是直接写 `./init.sh nemu` 或 `init.sh nemu`？

---

### ✅ 简短回答：

**加上 `bash` 是为了显式指定用 `bash` 解释器来运行这个脚本，避免因脚本没有可执行权限、或系统默认 shell 不是 bash 而导致失败。**

---

### 🔍 详细解释：

#### 1. **脚本可能没有“可执行权限”**

在 Linux 中，一个文件要能像程序一样直接运行（比如 `./init.sh`），必须具备 **可执行权限（executable permission）**。

如果你刚克隆仓库，`init.sh` 的权限可能是：

```bash
-rw-r--r-- 1 user user ... init.sh   # 没有 'x' 权限
```

这时候直接运行：

```bash
./init.sh nemu
```

会报错：

```
bash: ./init.sh: Permission denied
```

但如果你用 `bash init.sh nemu`，就**不需要脚本有可执行权限**，因为你是让 `bash` 这个程序去读取并解释这个文件，就像你用 `python script.py` 不需要 `script.py` 有执行权限一样。

---

#### 2. **确保使用 `bash`，而不是其他 shell（如 dash、zsh）**

有些 Linux 发行版（比如 Ubuntu）的 `/bin/sh` 默认指向 `dash`（一个更轻量但功能较少的 shell），而 `init.sh` 脚本中可能用了 **bash 特有的语法**（比如数组、`[[ ]]`、某些变量扩展等）。

如果直接运行：

```bash
./init.sh nemu
```

系统会根据脚本第一行的 **shebang（如 `#!/bin/bash`）** 来决定用什么解释器。但如果：

- 脚本**没有 shebang**，
- 或者你用 `sh init.sh`（强制用 sh），
- 或者你的登录 shell 是 zsh/fish，

就可能导致兼容性问题。

而显式写 `bash init.sh nemu`，**100% 确保用 bash 执行**，避免这类问题。

---

#### 3. **教学上的“明确性”和“容错性”**

在教学环境中，学生用的操作系统千差万别（WSL、Ubuntu、macOS、Debian 等），shell 配置也各不相同。  
讲义选择 `bash init.sh ...` 这种写法，是为了：

- **降低门槛**：不用教学生先 `chmod +x init.sh`
- **提高成功率**：不管权限或默认 shell 是什么，只要装了 bash 就能跑
- **强调工具链依赖**：本课程明确依赖 `bash`，所以直接调用它

---

### 🔄 对比几种写法：

|写法|是否需要可执行权限？|是否依赖 shebang？|是否保证用 bash？|
|---|---|---|---|
|`bash init.sh nemu`|❌ 不需要|❌ 不依赖|✅ 是|
|`./init.sh nemu`|✅ 需要|✅ 依赖（如 `#!/bin/bash`）|⚠️ 只有 shebang 正确才是|
|`sh init.sh nemu`|❌ 不需要|❌ 不依赖|❌ 用的是 `sh`（可能是 dash）|

---

### ✅ 总结：

> 使用 `bash init.sh nemu` 是一种**更健壮、更通用、对初学者更友好**的做法，尤其在教学场景下，能避免大量因权限或 shell 差异导致的“奇怪错误”。

等你熟悉 Linux 后，也可以：

```bash
chmod +x init.sh
./init.sh nemu
```

但前提是脚本开头有正确的 `#!/bin/bash`，且你的系统支持。

---

📚 小知识：你可以用 `file init.sh` 或 `head -n1 init.sh` 看看脚本有没有 shebang 行 😊

## 

你提到的命令：

```bash
git branch -m master
```

出现在 [PA0 实验指导页面](https://nju-projectn.github.io/ics-pa-gitbook/ics2025/0.6.html#rtfsc-and-enjoy) 的上下文中，它的作用是 **将当前分支重命名为 `master`**。但要理解“**为啥要这么做**”，我们需要结合课程仓库的实际情况来看。

---

### 🔍 背景：克隆时默认分支可能不是 `master`

在较新的 Git 仓库中（尤其是 GitHub 上 2020 年之后创建的），**默认分支名通常不再是 `master`，而是 `main`**。  
南京大学 ICS PA 课程的仓库（`NJU-ProjectN/ics-pa`）虽然保留了 `master` 分支，但如果你用某些 Git 客户端或配置，**克隆后可能处于一个“分离头指针”状态**，或者本地没有正确创建 `master` 分支。

更关键的是：**实验脚本和 Makefile 可能硬编码依赖 `master` 分支名**（比如用于判断是否在主干、生成提交日志等）。如果本地分支叫 `main` 或其他名字，后续流程可能出错。

---

### ✅ 所以，`git branch -m master` 的目的其实是：

> **确保本地当前分支的名字是 `master`，以符合课程框架的预期。**

这行命令通常出现在以下流程中：

```bash
git clone -b 2025 ... ics2025
cd ics2025
git branch -m master   # ← 把刚克隆下来的分支（可能是 "2025" 或 HEAD）强制改名为 master
```

注意：`-b 2025` 表示克隆远程的 `2025` 分支，但克隆后本地分支名**默认也叫 `2025`**。  
而课程后续操作（如创建 `pa0` 分支、提交检查、日志分析）都假设你有一个叫 `master` 的主分支。

所以这里通过 `git branch -m master` 把本地分支重命名，**统一命名规范**，避免后续工具链出错。

---

### 🛠 补充说明：`git branch -m` 的行为

- 如果你当前在分支 `2025` 上，执行：
    
    ```bash
    git branch -m master
    ```
    
    效果是：**把 `2025` 分支重命名为 `master`**，当前工作区切换到 `master`。
    
- 这不会改变提交历史，只是改个名字。
    
- 之后你可以基于 `master` 创建实验分支：
    
    ```bash
    git checkout -b pa0
    ```
    

---

### ❓为什么不直接用 `2025` 分支？

因为：

1. **课程文档和脚本统一使用 `master` 作为基准分支名**，便于教学和自动化检查。
2. `2025` 是**远程分支名**，代表“2025 春季学期的代码基线”，但本地开发习惯仍沿用 `master` 作为主干。
3. 避免学生混淆“学期分支”和“本地主干”的概念。

---

### ✅ 总结

> `git branch -m master` 的作用是：**将克隆后本地的分支（如 `2025`）重命名为 `master`，以匹配课程框架对分支命名的约定，确保后续脚本、提交检查、实验流程能正常工作。**

这是一种**工程上的约定优于配置（convention over configuration）** 的体现——大家统一用 `master`，就不用到处改脚本了。

---

💡 小贴士：你可以用 `git branch` 查看当前有哪些本地分支，带 `*` 的就是当前所在分支。

## clone时不能用ssh，如何换https

你贴出的这段脚本是 `init.sh` 的一部分，用于初始化子项目（如 NEMU）。你提到：

> “我这里好像只能 HTTPS 请求”

这非常关键！因为脚本中使用的是 **SSH 方式克隆仓库**：

```bash
git clone -b $2 git@github.com:$1.git $3
#                ↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑
#                这是 SSH 协议（需要配置 SSH 密钥）
```

而如果你的网络环境 **只允许 HTTPS（比如校园网、公司防火墙、或没配 SSH key）**，那么 `git clone` 会失败，卡在“Permission denied (publickey)”或超时。

---

### ✅ 解决方案：把 SSH 地址改成 HTTPS 地址

你需要修改 `init.sh` 中的克隆命令，将：

```bash
git@github.com:NJU-ProjectN/nemu.git
```

改为：

```bash
https://github.com/NJU-ProjectN/nemu.git
```

#### 🔧 修改方法（推荐两种）：

---

#### 方法一：直接编辑 `init.sh`（简单粗暴）

找到这一行（在 `init()` 函数里）：

```bash
git clone -b $2 git@github.com:$1.git $3
```

改成：

```bash
git clone -b $2 https://github.com/$1.git $3
```

✅ 改完后，执行：

```bash
bash init.sh nemu
```

就能走 HTTPS 克隆了！

---

#### 方法二：用 Git 的 `insteadOf` 功能（全局替换，一劳永逸）

如果你不想改脚本，可以告诉 Git：“凡是遇到 `git@github.com:`，都自动换成 `https://github.com/`”。

运行一次这个命令（只需一次）：

```bash
git config --global url."https://github.com/".insteadOf "git@github.com:"
```

之后，即使脚本写的是 `git@github.com:...`，Git 也会自动用 HTTPS 去拉取。

> 💡 原理：这是 Git 的 URL 重写机制，非常适合不能用 SSH 的环境。

---

### 📌 额外提醒

1. **HTTPS 克隆不需要 SSH key，但推送（push）需要账号密码（或 token）**  
    不过 PA 实验一般**只需要拉取（clone）和本地提交**，不需要 push 到 NJU 官方仓库，所以 HTTPS 完全够用。
    
2. **如果你用 GitHub 的个人 fork**（比如自己 fork 了 nemu），地址要相应改成：
    
    ```bash
    https://github.com/你的用户名/nemu.git
    ```
    
    但根据课程要求，PA0 通常直接用官方仓库，无需 fork。
    
3. **确保你能访问 GitHub**  
    如果连 `https://github.com/NJU-ProjectN/nemu` 都打不开，那可能需要科学上网或使用镜像（但课程一般不建议用镜像，以防代码不一致）。
    

---

### ✅ 总结

|问题|原因|解决办法|
|---|---|---|
|`git clone` 失败，卡住或报权限错|脚本用 SSH (`git@`)，但你只能用 HTTPS|**改脚本为 `https://`** 或 **设置 `insteadOf`**|

推荐你用 **方法二（`insteadOf`）**，因为：

- 不用改官方脚本（避免后续 `git pull` 冲突）
- 对所有 GitHub 仓库生效
- 一行命令搞定

```bash
git config --global url."https://github.com/".insteadOf "git@github.com:"
```

然后就可以愉快地运行：

```bash
bash init.sh nemu
bash init.sh abstract-machine
```

顺利初始化啦！🎉