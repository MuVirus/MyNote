
#####  从状态机视角理解程序运行

题目：
以上一小节中`1+2+...+100`的指令序列为例, 尝试画出这个程序的状态机.

这个程序比较简单, 需要更新的状态只包括`PC`和`r1`, `r2`这两个寄存器, 因此我们用一个三元组`(PC, r1, r2)`就可以表示程序的所有状态, 而无需画出内存的具体状态. 初始状态是`(0, x, x)`, 此处的`x`表示未初始化. 程序`PC=0`处的指令是`mov r1, 0`, 执行完之后`PC`会指向下一条指令, 因此下一个状态是`(1, 0, x)`. 如此类推, 我们可以画出执行前3条指令的状态转移过程:

```
(0, x, x) -> (1, 0, x) -> (2, 0, 0) -> (3, 0, 1)
```

请你尝试继续画出这个状态机, 其中程序中的循环只需要画出前两次循环和最后两次循环即可.

参考：
```
// PC: instruction    | // label: statement
0: mov  r1, 0         |  pc0: r1 = 0;
1: mov  r2, 0         |  pc1: r2 = 0;
2: addi r2, r2, 1     |  pc2: r2 = r2 + 1;
3: add  r1, r1, r2    |  pc3: r1 = r1 + r2;
4: blt  r2, 100, 2    |  pc4: if (r2 < 100) goto pc2;   // branch if less than
5: jmp 5              |  pc5: goto pc5;
```

状态转移过程

```
(0, x, x) -> (1, 0, x) -> (2, 0, 0) -> (3, 0, 1) -> (4, 1, 1) -> (2, 1, 1) -> (3, 1, 2) -> (4, 3, 2) -> (2, 3, 2) -> (2, 3, 3) -> ... -> (2, 4851, 98) -> (3, 4851, 99) -> (4, 4950, 99) -> (2, 4950, 99) -> (2, 4950, 100) -> (2, 5050, 100)
```


# menuConfig

![](img/Pasted%20image%2020251217115428.png)

你展示的这张图是 **NEMU 项目的 `make menuconfig` 配置界面**，它是一个基于 ncurses 的图形化菜单系统，用于配置 NEMU 模拟器的各种选项。这个界面非常类似于 Linux 内核的 `menuconfig` 工具。

---

## 🧩 界面解析

### 🔹 标题栏

```
.config – NEMU Configuration Menu
```

- 当前正在编辑的是 `.config` 文件（位于项目根目录）。
- 这个文件决定了整个 NEMU 构建时启用哪些功能。

---

### 🔹 使用说明（顶部提示）

```
Arrow keys navigate the menu. <Enter> selects submenus ---> (or empty submenus ----).
Highlighted letters are hotkeys.
Pressing <Y> includes, <N> excludes, <M> modularizes features.
Press <Esc><Esc> to exit, <?> for Help, </> for Search.
Legend: [*] built-in  [ ] excluded  <M> module  < > module capable
```

|操作|含义|
|---|---|
|↑↓←→|移动光标|
|Enter|进入子菜单（如果右边有 `--->`）或选择当前项|
|Y|将某个选项设置为“编译进内核”（built-in）|
|N|排除该选项（不编译）|
|M|设置为模块（可动态加载）——NEMU 中通常不支持模块，所以多为 `[*]` 或 `[ ]`|
|Esc+Esc|退出菜单|
|?|查看帮助|
|/|搜索关键词|

---

### 🔹 菜单项详解

#### ✅ Base ISA (riscv) --->

- 当前选择的指令集架构（ISA）是 **RISC-V**
- 可以切换到其他 ISA，如 x86、mips32 等（取决于源码中是否支持）

#### ➡️ ISA-dependent Options for riscv --->

- RISC-V 特有的选项，比如：
    - 是否启用 RV32I、RV32M、RV32F 等扩展
    - 是否启用 `CONFIG_RVE`（嵌入式模式，16 个寄存器）
    - 是否启用 `CONFIG_RV64`（64 位模式）

> ⚠️ 这些选项会直接影响 `MUXDEF(CONFIG_RVE, 16, 32)` 和 `MUXDEF(CONFIG_RV64, riscv64_CPU_state, riscv32_CPU_state)` 的展开结果！

#### ➡️ NEMU execution engine (Interpreter) --->

- 执行引擎的选择：
    - Interpreter（解释执行）
    - JIT（Just-In-Time 编译）——可能未实现或不可选
- 会影响模拟速度和复杂度

#### ➡️ Running mode (System mode) --->

- 模拟运行模式：
    - System mode：完整系统模拟（支持设备、内存映射等）
    - User mode：仅用户程序执行（无 MMU、中断等）

#### ➡️ Build target (Executable on Linux Native) --->

- 构建目标平台：
    - Linux Native：直接在 Linux 上运行
    - ARM、x86 等交叉编译目标（如果有支持）

#### ➡️ Build Options --->

- 编译选项：
    - C 编译器（GCC/Clang）
    - 是否启用 LTO（Link Time Optimization）
    - 是否开启调试信息（-Og -ggdb3）
    - 是否启用 ASan（AddressSanitizer）进行内存错误检测

#### ➡️ Testing and Debugging --->

- 调试相关：
    - 是否启用 `ITRACE_COND`（指令跟踪条件）
    - 是否输出日志
    - 是否启用断点、单步执行等

#### ➡️ Memory Configuration --->

- 内存配置：
    - 物理内存大小（如 128MB）
    - 是否启用虚拟内存（MMU）
    - 地址空间布局（ASLR？）

#### [ ] Devices ----

- 设备支持（目前未启用）
- 可能包括：
    - UART、GPIO、Timer、PCI 等外设模拟
    - 如果选中，会在构建时包含对应驱动代码

#### ➡️ Miscellaneous --->

- 其他杂项：
    - 版本号、日志级别、性能统计开关等

---

## 🔄 如何使用？

1. **进入子菜单**：用方向键选中某一项 → 按 `Enter`
2. **修改选项**：在子菜单中按 `Y/N/M` 来启用/禁用
3. **搜索**：按 `/` 输入关键字快速定位
4. **保存并退出**：
    - 修改完成后，按 `<Save>` 保存 `.config`
    - 按 `<Exit>` 返回上级，最终确认保存

> 💡 示例：你想让 NEMU 支持 64 位 RISC-V 并启用地址检查？
> 
> - 进入 `ISA-dependent Options for riscv`
> - 找到 `CONFIG_RV64` → 按 `Y`
> - 回到主菜单 → `Build Options` → 选中 `CONFIG_CC_ASAN` → 按 `Y`
> - 最后 `<Save>` → `<Exit>`

---

## 📂 配置结果去哪了？

当你点击 `<Save>` 后：

- 生成 `.config` 文件（项目根目录下）
- Kconfig 工具会自动解析 `.config`，生成：
    - `include/config/auto.conf`
    - `include/config/auto.conf.cmd`
- Makefile 通过 `-include` 引入这些文件，读取 `CONFIG_XXX` 宏值
- 最终影响 `MUXDEF` 展开、结构体定义、编译选项等

---

## ✅ 总结

|功能|说明|
|---|---|
|`menuconfig`|图形化配置工具，来自 `scripts/kconfig/mconf`|
|`.config`|用户配置文件，决定所有构建行为|
|`CONFIG_XXX`|Kconfig 宏，在预处理阶段被替换|
|`MUXDEF`|利用 `CONFIG_XXX` 值做类型/常量选择|
|`Base ISA`|决定 CPU 架构（riscv/x86/mips）|
|`Build Options`|控制编译器、优化、调试等|

---

🎯 **建议操作**：

```bash
make menuconfig
# 修改配置
make clean && make
# 重新编译
```

这样就能根据你的需求定制一个专属的 NEMU 模拟器了！

如果你想知道某个具体选项对代码的影响，我可以帮你分析它如何改变 `CPU_state` 或 `isa-def.h` 的内容 😊


