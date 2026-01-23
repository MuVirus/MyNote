在 **Windows** 系统下（非 WSL），如果你希望从 **GitHub 仓库中只下载某个单独的文件夹**（而不是整个仓库），同样会遇到 Git 本身不支持“直接下载子目录”的限制。但由于 GitHub 已**完全停用 SVN 支持（2024 年起）**，过去常用的 `svn export` 方法也已失效。

不过别担心！以下是 **适用于 Windows 的几种实用方案**，按推荐程度排序：

---

## ✅ 方案一：使用 Git 的 `sparse-checkout`（最推荐！官方支持）

> 要求：安装 [Git for Windows](https://git-scm.com/download/win)（自带 Git Bash）

### 操作步骤（在 **Git Bash** 中执行）：

```bash
# 1. 创建并进入目标文件夹
mkdir my-folder && cd my-folder

# 2. 初始化空仓库
git init

# 3. 添加远程仓库（替换为你的仓库地址）
git remote add origin https://github.com/用户名/仓库名.git

# 4. 启用 sparse-checkout
git config core.sparseCheckout true

# 5. 指定要下载的子目录（注意结尾的 /）
echo "path/to/your/folder/" >> .git/info/sparse-checkout

# 6. 拉取指定分支（通常是 main 或 master）
git pull origin main
```

✅ **优点**：
- 只下载你指定的文件夹，节省时间和流量
- 官方 Git 功能，稳定可靠
- 后续还能 `git pull` 更新

📌 **示例**：下载 `https://github.com/microsoft/PowerToys` 中的 `src/common/` 文件夹

```bash
mkdir pt-common && cd pt-common
git init
git remote add origin https://github.com/microsoft/PowerToys.git
git config core.sparseCheckout true
echo "src/common/" >> .git/info/sparse-checkout
git pull origin main
```

> 💡 提示：如果不知道默认分支名，可先在浏览器打开仓库首页，看顶部显示的是 `main` 还是 `master`。

---

## ✅ 方案二：完整克隆 + 手动提取（适合小项目）

如果仓库不大，最简单的方法：

1. 安装 [Git for Windows](https://git-scm.com/download/win)
2. 在任意文件夹右键 → **Git Bash Here**
3. 执行：
   ```bash
   git clone --depth=1 https://github.com/用户名/仓库名.git
   ```
4. 进入克隆出的文件夹，复制你需要的子目录到其他位置
5. 删除整个克隆文件夹（可选）

> 优点：操作简单，无需记命令  
> 缺点：需下载整个 repo（但 `--depth=1` 能大幅减少数据量）

---

## ✅ 方案三：使用 PowerShell + GitHub Raw API（适合少量文件）

如果你只需要**文件夹中的几个具体文件**，可以用 PowerShell 下载：

```powershell
# 示例：下载单个文件
Invoke-WebRequest -Uri "https://raw.githubusercontent.com/用户名/仓库名/main/path/to/file.txt" -OutFile "file.txt"
```

但 **无法自动递归下载整个子目录**（除非写脚本遍历，复杂且受 GitHub 限制）。

> ⚠️ 注意：`raw.githubusercontent.com` 只能访问**单个文件**，不能列目录。

---

## ❌ 不可行的方法（请避免）

| 方法 | 原因 |
|------|------|
| `svn export https://github.com/...` | GitHub **已彻底关闭 SVN 支持**（2024 年起） |
| 浏览器直接下载文件夹 | GitHub 不提供“下载文件夹 ZIP”功能（只能下载整个仓库） |
| 第三方网站“GitHub folder downloader” | 多数不可靠、有广告、隐私风险高 |

---

## 🔧 额外建议：使用 GitHub CLI（高级用户）

如果你经常操作 GitHub，可安装 [GitHub CLI](https://cli.github.com/)：

```powershell
# 安装后（通过 winget）：
winget install GitHub.cli

# 但它也不支持只下载子目录，仍需配合 sparse-checkout
```

所以核心还是回到 **sparse-checkout**。

---

## ✅ 总结：Windows 下如何只下载 GitHub 的某个文件夹？

| 场景 | 推荐方法 |
|------|--------|
| 想高效、干净地获取子目录 | **Git sparse-checkout（首选）** |
| 仓库很小，图省事 | `git clone --depth=1` + 手动复制 |
| 只需要 1~2 个文件 | 用浏览器打开 → Raw → 右键另存为，或用 PowerShell 下载 |

---

如果你告诉我：
- GitHub 仓库地址（如 `https://github.com/xxx/yyy`）
- 具体想下载的文件夹路径（如 `examples/demo/`）

我可以为你生成完整的 Windows Git Bash 命令！