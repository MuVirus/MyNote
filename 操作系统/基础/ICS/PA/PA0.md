
最后按照下面链接做就行。
[Getting Source Code for PAs · GitBook](https://nju-projectn.github.io/ics-pa-gitbook/ics2025/0.6.html#rtfsc-and-enjoy)

# 学习

## Git分支管理
当然可以！你贴出的这段内容来自南京大学《计算机系统基础》（ICS）课程 PA0 实验文档中关于 **Git 分支开发流程** 的教学部分。下面我将**逐层深入、详细解释**它的含义、背后的 Git 原理，以及为什么课程要这样设计工作流。

---


### 🌟 一、核心目标：用 Git 分支管理实验开发过程

课程希望你：

- **每个编程作业（PA0, PA1, ..., PA4）都在独立分支上开发**
- **主干（`master`）始终保持“干净”或“可运行”的状态**
- **通过 `git commit` 记录你的思考和进展，作为“过程证据”**

这不仅是版本控制的最佳实践，更是**防止你搞乱代码、便于回溯、也方便助教判断是否独立完成**的重要手段。

---

### 🔍 二、逐步解析文档中的操作与原理

#### 1. 初始状态：只有一个 `master` 分支

```bash
git branch
# 输出：
# * master
```

- `*` 表示当前所在分支。
- 此时所有文件都是从远程仓库克隆下来的“原始状态”。

> ✅ **为什么叫 `master`？**  
> 虽然现代 Git 推荐用 `main`，但本课程沿用传统名称 `master` 作为“主干分支”，代表官方基线代码。

---

#### 2. 创建并切换到新分支 `pa0`

```bash
git checkout -b pa0
```

- `-b` 表示 **create a new branch and switch to it**。
- 相当于两步：
    
    ```bash
    git branch pa0      # 创建分支（指针指向当前 commit）
    git checkout pa0    # 切换到该分支
    ```
    

> 🧠 **Git 分支的本质是什么？**  
> Git 的分支只是一个 **轻量级的指针**，指向某个 commit。  
> 创建分支几乎不占用空间，只是新建一个引用（ref）。

此时：

```bash
git branch
#   master
# * pa0
```

- 你在 `pa0` 上，但 `pa0` 和 `master` 指向**同一个 commit**（因为刚创建，还没修改）。

---

#### 3. 修改 `Makefile` 中的个人信息

```makefile
STUID = 241221234
STUNAME = 李四
```

- 这是课程要求的“身份标识”，用于后续自动检查和报告生成。

运行 `git status`：

```bash
On branch pa0
Changes not staged for commit:
  modified:   Makefile
```

- Git 告诉你：**当前在 `pa0` 分支，`Makefile` 被修改了，但还没“暂存”（staged）**。

运行 `git diff`：

- 显示具体改了哪些行（对比上一次 commit 的内容）。

> 💡 注意：此时修改**只存在于工作区（working directory）**，还没进入 Git 的历史。

---

#### 4. 提交修改：`git add` + `git commit`

```bash
git add .
git commit
```

- `git add .`：把当前目录所有修改**暂存（stage）**，准备提交。
- `git commit`：打开编辑器，写提交信息（如 "modified my info"），然后生成一个新的 commit。

✅ 提交后：

- `pa0` 分支的指针**向前移动**，指向这个新 commit。
- `master` 分支**仍然指向原来的 commit**，完全不受影响！

---

#### 5. 切换回 `master`：发现修改“消失”了！

```bash
git checkout master
cat Makefile  # STUID/STUNAME 还是原始值！
git log       # 看不到刚才的提交！
```

这不是 bug，而是 Git 的**核心特性：分支隔离**。

> 🧩 **关键理解**：  
> Git 不是“保存文件”，而是“保存快照（snapshot）”。  
> 每个 commit 是一个完整项目状态的快照。  
> 切换分支 = 把工作区文件**恢复成该分支最新 commit 的快照**。

所以：

- 在 `pa0` 上的修改，只存在于 `pa0` 的 commit 历史中。
- 切到 `master`，Git 就把文件“还原”成 `master` 最新 commit 的样子。

---

#### 6. 切回 `pa0`：一切又回来了！

```bash
git checkout pa0
cat Makefile  # 又变成你的学号和名字！
git log       # 能看到自己的提交！
```

- 再次验证：**分支是独立的开发线**。

---

### 🛠 三、课程推荐的完整 PA 开发工作流

|阶段|操作|目的|
|---|---|---|
|**开始 PA0**|`git checkout -b pa0`|创建独立开发环境|
|**编码调试**|修改代码、编译、测试|所有改动记录在 `pa0`|
|**阶段性保存**|`git add . && git commit -m "xxx"`|留下思考痕迹，便于回退|
|**完成 PA0**|`git checkout master`  <br>`git merge pa0`|把成果合并到主干|
|**开始 PA1**|`git checkout -b pa1`|新建下一个实验分支|

> ⚠️ 注意：PA0 的最后一步（merge）可能在文档后面才要求，但流程是一致的。

---

### ❓ 四、为什么课程如此强调 Git 使用？

1. **防止“代码爆炸”**  
    如果直接在 `master` 上改，一旦出错很难回退。分支让你大胆尝试。
    
2. **过程即证据**  
    助教会检查你的 `git log`：
    
    - 是否有合理的提交历史？
    - 是否一次性提交全部代码（疑似抄袭）？
    - 是否在截止前几秒才提交（可能没认真做）？
3. **培养工程素养**  
    工业界标准开发流程就是：**feature branch → PR/MR → review → merge**。  
    课程提前训练你这种习惯。
    
4. **支持自动化检查**  
    后续 PA 中，框架会自动读取 `STUID`、分析提交日志、甚至根据分支名判断你做到哪一步。
    

---

### 💡 五、常见误区提醒

|误区|正确做法|
|---|---|
|“我直接在 `master` 上改吧，省事”|❌ 会导致无法隔离实验，merge 时混乱|
|“我不 commit，反正能跑就行”|❌ 没有提交 = 没有过程证据，可能判为未完成|
|“我用 GUI 工具点点就行”|⚠️ 可以，但必须理解背后原理，否则出错不会排查|
|“分支多了好乱”|✅ 完成后可 `git branch -d pa0` 删除（merge 后）|

---

### ✅ 总结一句话：

> **课程通过 `git branch` 强制你建立“每个实验独立开发、主干保持干净”的工程习惯，并用提交历史作为你独立思考和持续工作的证明。**

这不仅是 PA0 的要求，更是贯穿整个 ICS 课程（乃至未来软件开发）的核心方法论。

---

如果你现在就去执行这些命令，你会发现 Git 真的像“时光机”——随时回到任意状态，再也不怕“改崩了” 😄

需要我帮你写一个完整的 PA0 Git 操作清单吗？

## Git分支代码切换到放哪了？

这是一个非常关键的问题！你问：

> **“这些修改的数据都放在哪个文件夹？”**

答案是：**所有 Git 管理的修改（包括分支、提交、文件变更等）都存储在项目根目录下的 `.git` 隐藏文件夹中。**

---

### 📁 1. `.git` 目录：Git 的“大脑”和“仓库”

当你执行 `git init` 或 `git clone` 时，Git 会在项目根目录创建一个名为 **`.git`** 的隐藏目录。例如：

```
ics2025/
├── .git/          ← 所有 Git 元数据都在这里！
├── Makefile
├── init.sh
├── nemu/          ← 子项目（可能被 gitignore）
└── abstract-machine/
```

#### `.git` 里面存了什么？

| 内容            | 说明                                             |
| ------------- | ---------------------------------------------- |
| `objects/`    | 所有 commit、tree、blob（文件内容）的压缩快照（以 SHA-1 哈希命名）   |
| `refs/heads/` | **分支指针**！比如 `master`、`pa0` 文件，里面只存一个 commit ID |
| `HEAD`        | 指向当前所在分支（如 `ref: refs/heads/pa0`）              |
| `index`       | 暂存区（staging area）的二进制文件                        |
| `logs/`       | 分支历史变更日志（用于 `git reflog`）                      |
| `config`      | 本地仓库配置（如 remote 地址）                            |

> ✅ 所以，你在 `pa0` 分支做的修改、提交的 commit，本质上：
> 
> - 文件内容 → 被压缩存入 `.git/objects/`
> - 分支 `pa0` → 是 `.git/refs/heads/pa0` 里的一个文本文件，内容是一个 commit hash
> - 当前在哪 → 由 `.git/HEAD` 指向

---

### 🧩 2. 工作区（Working Directory） vs 仓库（.git）

|区域|位置|作用|
|---|---|---|
|**工作区**|`ics2025/` 下的普通文件（如 `Makefile`）|你编辑的“当前文件”|
|**暂存区（Index）**|`.git/index`|`git add` 后的“准备提交的快照”|
|**仓库（Repository）**|`.git/objects/` 等|所有历史 commit 和元数据|

当你：

- 修改 `Makefile` → 只改了**工作区**
- `git add Makefile` → 把修改写入**暂存区**
- `git commit` → 把暂存区打包成一个**commit object**，存入 `.git/objects/`，并更新 `pa0` 分支指针

---

### 🔁 3. 切换分支时发生了什么？

当你运行：

```bash
git checkout master
```

Git 会：

1. 读取 `.git/HEAD` → 发现要切到 `master`
2. 读取 `.git/refs/heads/master` → 得到目标 commit ID
3. 从 `.git/objects/` 中找出该 commit 对应的**完整文件树**
4. **用这个快照覆盖你的工作区文件**（所以 `Makefile` 变回原始值）

同理，切回 `pa0` 时，Git 用 `pa0` 分支指向的 commit 快照覆盖工作区。

> 💡 所以：**文件内容不“属于”某个分支，而是属于某个 commit；分支只是指向 commit 的标签。**

---

### ❓ 4. 那 `nemu/`、`abstract-machine/` 这些子目录呢？

根据你之前看到的 `init.sh` 脚本：

- 如果 `init.sh` 用的是 **“不追踪”模式**（`trace=false`），它会把 `nemu/` 加入 `.gitignore`，并且 **不会把它们的 `.git` 目录删掉**。
- 如果是 **“追踪”模式**（`trace=true`），它会 `rm -rf nemu/.git`，然后 `git add nemu`，把整个子项目代码作为**普通文件**纳入主仓库管理。

但在 PA0 中，通常 `nemu` 和 `am` 是**独立仓库**，主项目通过 `.gitignore` 忽略它们，所以：

- 它们的修改**不受主仓库 Git 控制**
- 它们自己也有 `.git` 目录（在 `nemu/.git/` 里）

---

### ✅ 总结

|问题|答案|
|---|---|
|**我的修改存在哪？**|文件内容存在 `.git/objects/`（作为 blob），提交历史存在 commit 对象中|
|**分支信息存在哪？**|`.git/refs/heads/` 下的文件（如 `pa0`、`master`）|
|**当前在哪个分支？**|由 `.git/HEAD` 文件记录|
|**切换分支为什么文件会变？**|Git 用目标分支的 commit 快照**覆盖工作区**|
|**能删掉 `.git` 吗？**|⚠️ 删了就**丢失所有历史、分支、提交**，变成普通文件夹！|

---

🔍 你可以亲自验证：

```bash
ls -la ics2025/.git/refs/heads/
# 你会看到 master 和 pa0 文件

cat ics2025/.git/refs/heads/pa0
# 输出一串 hash，比如 a1b2c3d...

cat ics2025/.git/HEAD
# 输出 ref: refs/heads/pa0
```

这就是 Git 的魔法所在：**一切皆对象，一切皆可追溯** ✨

## Git合并（在PA1开始之前）

你提到的这段指令是南京大学 ICS 课程在 **PA0 结束、PA1 开始前**要求学生执行的标准 Git 操作流程。它的目的不仅是“整理分支”，更是**确保你的实验过程符合评分规范**。下面我将逐条解释每一步的作用、背后的逻辑，以及**为什么不做会影响成绩**。

---

### 🎯 总体目标

> **将 PA0 的成果合并到 `master` 主干，并为 PA1 创建干净的新分支 `pa1`，同时留下明确的“阶段标记”提交。**

---

### 🔍 逐条解析

#### 1. `git commit --allow-empty -am "before starting pa1"`

##### ✅ 做了什么？

- 创建一个 **空提交（empty commit）**，不修改任何文件，但生成一条新的 commit 记录。
- 提交信息为 `"before starting pa1"`。

##### ❓ 为什么要空提交？

- **作为“时间戳”和“阶段分界线”**：表明“此时 PA0 已完成，即将开始 PA1”。
- 助教的自动检查脚本会扫描你的提交历史，寻找这类标志性提交，来判断：
    - 你是否按流程开发？
    - 是否在合理时间点切换任务？
    - 是否存在“最后时刻一次性提交”的嫌疑？

> ⚠️ 如果没有这个提交，系统可能认为你没完成 PA0 的收尾，或直接在 `pa0` 上继续做 PA1，导致流程混乱。

---

#### 2. `git checkout master`

##### ✅ 做了什么？

- 切换回主干分支 `master`。

##### ❓ 为什么必须切回 `master`？

- 合并操作（`git merge`）通常应在**目标分支**上进行。
- 我们希望把 `pa0` 的成果**合并进 `master`**，所以必须先在 `master` 上。

> 💡 此时 `master` 应该还是原始状态（未包含你的学号等修改），正等待接收 `pa0` 的成果。

---

#### 3. `git merge pa0`

##### ✅ 做了什么？

- 将 `pa0` 分支的所有提交**合并到当前分支（即 `master`）**。
- 合并后，`master` 就包含了你在 PA0 中做的所有修改（如 `Makefile` 中的学号、姓名等）。

##### ❓ 为什么必须 merge？

- **保证 `master` 是最新、完整的基线**，供后续 PA 使用。
- 后续 PA（如 PA1）的框架代码可能依赖 `master` 上的配置（比如 `STUID`）。
- 如果不 merge，`master` 仍是原始状态，可能导致：
    - 编译错误
    - 自动评测找不到你的身份信息
    - 被判为“未完成 PA0”

> ✅ 合并成功后，`master` 和 `pa0` 的 HEAD 应指向同一个 commit（快进合并）。

---

#### 4. `git checkout -b pa1`

##### ✅ 做了什么？

- 基于当前 `master`（已包含 PA0 成果）**创建新分支 `pa1`**，并切换过去。

##### ❓ 为什么不能直接在 `pa0` 上做 PA1？

- **每个 PA 必须独立分支**，这是课程硬性规定。
- 目的：
    - 隔离不同实验的代码改动
    - 便于单独回溯、调试某个 PA
    - 防止 PA1 的 bug 污染 PA0 的成果
    - 符合软件工程的“功能分支”开发模型

> 📌 重要：**PA1 的所有修改必须在 `pa1` 分支上进行！**  
> 如果你在 `master` 或 `pa0` 上写 PA1 代码，会被视为流程错误，可能扣分。

---

### 🧩 完整流程图解

```text
初始状态（PA0 完成后）：
master: A
pa0:    A — B — C   ← 你在 pa0 上做了修改（B, C）

执行命令后：
1. git commit --allow-empty → pa0: A—B—C—D ("before pa1")
2. git checkout master      → 当前在 master (A)
3. git merge pa0           → master: A—B—C—D （快进合并）
4. git checkout -b pa1     → pa1 指向 D

最终：
master: A—B—C—D
pa0:    A—B—C—D
pa1:    A—B—C—D   ← 从这里开始做 PA1
```

---

### ⚠️ 为什么“否则将影响你的成绩”？

课程使用**自动化脚本**检查以下内容：

1. **是否存在 `pa0` 分支？**
2. **`master` 是否包含 `pa0` 的修改？**（通过比对 `STUID`）
3. **是否有 `"before starting pa1"` 这样的阶段提交？**
4. **PA1 的提交是否发生在 `pa1` 分支上？**

如果缺少任一环节，系统可能：

- 无法识别你完成了 PA0
- 认为你抄袭（因为没有过程提交）
- 在 PA1 评测时找不到你的学号
- 直接判定流程不规范，扣分甚至零分

---

### ✅ 正确操作建议

在 `ics2025/` 目录下依次执行：

```bash
# 1. 留下阶段标记（即使没改代码也要做！）
git commit --allow-empty -am "before starting pa1"

# 2. 切回主干
git checkout master

# 3. 合并 PA0 成果
git merge pa0

# 4. 创建 PA1 开发分支
git checkout -b pa1
```

然后确认：

```bash
git branch
# 应显示：
#   master
#   pa0
# * pa1

cat Makefile | grep STUID  # 应显示你的学号（说明 merge 成功）
```

---

### 💡 小贴士

- 如果 `git merge pa0` 报错（比如冲突），说明你可能在 `master` 上也改过文件。应先 `git checkout master` 并 `git reset --hard origin/master` 回退，再 merge。
- 不要手动修改 `.git` 目录！所有操作都用 Git 命令。
- 每次 PA 开始前，重复类似流程：`commit --allow-empty` → `checkout master` → `merge paN` → `checkout -b pa(N+1)`

---

这样做，你不仅能满足评分要求，还养成了**专业级的版本控制习惯** 👨‍💻✨

## Git空提交的参数

好问题！你问的是：

> **`git commit --allow-empty -am "before starting pa1"` 中的 `-am` 是什么？**

我们来拆解这个命令：

```bash
git commit --allow-empty -am "before starting pa1"
```

其中：

- `--allow-empty`：允许创建一个**不包含任何文件变更的空提交**（empty commit）。
- `-am`：其实是 **两个选项连写**：`-a` + `-m`

---

### 🔍 一、`-a` 是什么？

`-a` 是 `--all` 的缩写，意思是：

> **自动暂存（stage）所有已跟踪（tracked）文件的修改和删除，但不包括新文件（untracked files）。**

#### ✅ 具体行为：

- 如果你修改了 `Makefile`、`init.sh` 等**已经被 Git 跟踪的文件**，`-a` 会自动把它们加到暂存区（相当于先执行 `git add <这些文件>`）。
- 但如果你新建了一个文件（比如 `test.c`），而它还没被 `git add` 过，那么 `-a` **不会包含它**。

> 📌 注意：在你这个命令中，其实**没有文件被修改**，所以 `-a` 实际上“什么也没做”——但这没关系，因为后面有 `--allow-empty`。

---

### 🔍 二、`-m` 是什么？

`-m` 是 `--message` 的缩写，用于**直接在命令行指定提交信息**，而不用打开编辑器。

例如：

```bash
git commit -m "fix bug in timer"
```

等价于：

```bash
git commit          # 然后弹出编辑器，让你输入 "fix bug in timer"
```

所以 `-m "before starting pa1"` 就是把提交信息设为 `"before starting pa1"`。

---

### 🔗 三、为什么可以写成 `-am`？

Git 允许将**多个单字母选项连在一起写**，只要它们都不需要额外参数（或只有最后一个需要）。

- `-a` 不需要参数
- `-m` 需要一个字符串参数（提交信息）

所以：

```bash
git commit -a -m "msg"
```

可以简写为：

```bash
git commit -am "msg"
```

这是 Git 的常见习惯写法，简洁高效。

---

### ❓ 四、在这个命令中，`-a` 有必要吗？

严格来说，**在这个特定场景下，`-a` 是多余的**，因为：

- 你只是想创建一个**空提交**（`--allow-empty`）
- 并没有修改任何文件
- 所以有没有 `-a`，结果都一样

但课程文档保留 `-am`，可能是出于**习惯统一**——平时提交都用 `-am`，这里也沿用，避免学生混淆。

> ✅ 更“精确”的写法其实是：
> 
> ```bash
> git commit --allow-empty -m "before starting pa1"
> ```
> 
> （去掉 `-a`，因为不需要暂存任何东西）

但加上 `-a` 也不会出错，所以无伤大雅。

---

### ✅ 总结

|选项|含义|
|---|---|
|`-a`|自动暂存所有已跟踪文件的修改（对空提交无影响）|
|`-m`|指定提交信息，避免打开编辑器|
|`-am`|`-a` 和 `-m` 的连写，Git 允许这样简写|
|`--allow-empty`|允许创建没有任何文件变更的提交（关键！）|

所以整条命令的意思是：

> **“即使没有修改任何文件，也创建一个提交，提交信息为 'before starting pa1'。”**

这在标记开发阶段、触发 CI 流水线、或满足课程流程要求时非常有用 ✅

## 编译运行nemu

这段内容主要描述了如何编译和运行 NEMU（南京大学 ICS 课程中的模拟器项目），并介绍了在遇到问题时如何调试和解决。以下是详细的解释和步骤：

### 1. 编译前配置

在首次编译 NEMU 之前，需要生成一个配置文件：

```bash
make menuconfig
```

- 这将弹出一个菜单配置界面。
- **不要修改任何设置**，直接选择退出并保存新配置。

> **注意**：即使你不想更改任何配置，也需要执行这一步骤来生成必要的配置文件。

### 2. 编译 NEMU

执行以下命令进行编译：

```bash
make
```

如果一切正常，NEMU 将被成功编译。

- 如果编译过程中遇到错误，请仔细阅读输出信息，尝试理解问题的根源。
- 根据提示安装缺少的工具（比如 `make menuconfig` 可能需要 `libncurses5-dev` 等依赖）。

### 3. 清理旧的编译结果

如果你需要重新编译整个项目，可以先清理旧的编译结果：

```bash
make clean
```

然后再次执行 `make`。

### 4. 运行 NEMU

编译完成后，可以通过以下命令运行 NEMU：

```bash
make run
```

然而，此时你会看到一个错误消息：

```
[src/monitor/monitor.c:35 welcome] Exercise: Please remove me in the source code and compile NEMU again.
riscv32-nemu-interpreter: src/monitor/monitor.c:36: welcome: Assertion `0' failed.
```

这个错误是故意设计的，目的是让学生在 PA1 中修复它。具体来说，你需要编辑 `nemu/src/monitor/monitor.c` 文件，找到并移除或注释掉导致断言失败的代码段，然后重新编译 NEMU。

### 5. 使用 GDB 调试 NEMU

为了使用 GDB 调试 NEMU，可以执行：

```bash
make gdb
```

这会启动 GDB 并加载 NEMU，方便你进行调试。

### 6. 开发追踪

一旦编译成功，所有对源代码的修改都会被 Git 自动追踪。你可以通过以下命令查看提交日志：

```bash
git log
```

你应该能看到类似如下的提交记录：

```
commit 4072d39e5b6c6b6837077f2d673cb0b5014e6ef9
Author: tracer-ics2025 <tracer@njuics.org>
Date:   Sun Jul 26 14:30:31 2025 +0800

    >  run NEMU
    241220000 张三
    Linux 9900k 5.10.0-10-amd64 #1 SMP Debian 5.10.84-1 (2021-12-08) x86_64 GNU/Linux
    15:57:01 up 22 days,  6:01, 16 users,  load average: 0.00, 0.00, 0.00
```

这表明你的更改已经被 Git 成功追踪。

#### 特别说明：“一生一芯”分支的日志

如果你参加了“一生一芯”项目，应该使用以下命令查看代码跟踪日志：

```bash
git log tracer-ysyx
```

### 7. 解决 `.git/index.lock` 锁定问题

有时，当你执行 `make` 时可能会遇到如下错误：

```
fatal: Unable to create '/home/user/ics2025/.git/index.lock': File exists.

If no other git process is currently running, this probably means a git process crashed in this repository earlier. Make sure no other git process is running and remove the file manually to continue.
```

这意味着有一个残留的 `.git/index.lock` 文件阻止了 Git 的操作。解决方法如下：

1. 确保没有其他 Git 进程正在运行。
2. 手动删除该锁定文件：
    
    ```bash
    rm /home/user/ics2025/.git/index.lock
    ```
    
3. 清理并重新编译：
    
    ```bash
    make clean
    make
    ```
    

如果问题依旧存在，请尽快联系课程支持人员。

---

希望这些解释能帮助你更好地理解和完成 NEMU 的编译与调试任务！如果有更多疑问或遇到特定错误，随时欢迎提问。

# git使用SSH

## Github配置SSH Key的步骤（大白话+原理解释）

在使用 GitHub 时，如果你希望避免每次 `git push` 或 `git pull` 都要输入账号密码，可以配置 **SSH Key**。以下是详细步骤和通俗原理解释。

---

### 第一步：检查本地是否已有 SSH Key

打开终端（或 Git Bash），运行：

```bash
cd ~/.ssh
ls
```

查看是否存在以下两个文件：

- `id_rsa`（私钥）
- `id_rsa.pub`（公钥）

如果存在，说明你已经生成过 SSH Key，可以直接跳到第三步；如果没有，继续第二步。

---

### 第二步：生成新的 SSH Key

执行以下命令（将邮箱替换为你自己的 GitHub 注册邮箱）：

```bash
ssh-keygen -t rsa -C "your_email@example.com"
```

然后一路按回车（使用默认路径和空密码即可）。完成后，会在 `~/.ssh/` 目录下生成 `id_rsa` 和 `id_rsa.pub`。

---

### 第三步：获取公钥内容

运行：

```bash
cat ~/.ssh/id_rsa.pub
```

复制输出的全部内容（以 `ssh-rsa AAAAB3...` 开头，到你的邮箱结尾）。

---

### 第四步：将公钥添加到 GitHub 账号

1. 登录 GitHub。
2. 点击右上角头像 → **Settings**。
3. 左侧菜单选择 **SSH and GPG keys** → **New SSH key**。
4. Title 随便填（比如 “My Laptop”），Key 粘贴刚才复制的内容。
5. 点击 **Add SSH key**。

---

### 第五步：验证配置是否成功

在终端运行：

```bash
ssh -T git@github.com
```

如果看到类似以下提示，说明配置成功：

```
Hi <你的用户名>! You've successfully authenticated, but GitHub does not provide shell access.
```

✅ 此后，**克隆仓库时务必使用 SSH 地址**（形如 `git@github.com:username/repo.git`），而不是 HTTPS 地址。

---

### 原理解释（通俗版）

SSH 使用**非对称加密**：

- **私钥（id_rsa）**：留在你本地电脑，绝对不能泄露。
- **公钥（id_rsa.pub）**：上传到 GitHub，相当于“门锁”。

**认证过程如下**：

1. 你发起连接请求（比如 `git push`）。
2. GitHub 用你上传的**公钥**加密一段随机信息，发回给你。
3. 你的电脑用本地的**私钥**解密，并把结果发回去。
4. GitHub 验证解密内容是否正确 → 正确就放行！

这就像你有一把独一无二的钥匙（私钥），GitHub 有对应的锁（公钥）。只要钥匙能开锁，就证明你是“自己人”，无需再输密码。

---

### 常见问题解答

1. **为什么要配 SSH Key？**  
    → 免去每次输入账号密码的麻烦，提升效率。
    
2. **每台电脑都要配吗？**  
    → 是的！SSH Key 是**绑定到具体主机**的。换电脑就得重新生成并添加。
    
3. **配了就能随便 push 别人的仓库吗？**  
    → 不行！你只能 push 到**你有权限的仓库**（自己的 or 被授权的）。
    

---

> ⚠️ 注意：配置成功后，请始终使用 **SSH URL**（`git@github.com:...`）而非 HTTPS URL（`https://github.com/...`）进行 clone/push 操作，否则仍会要求输入密码。

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

## branch分支重命名到master干啥？

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