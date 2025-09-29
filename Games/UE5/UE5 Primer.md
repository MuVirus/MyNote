# 基础操作
## 1、蓝图项目如何转C++项目
新建C++类，项目就会自动转成C++项目了。
![](img/Pasted%20image%2020250914100321.png)
## 2、添加新手包、第一人称、第三人称包
![](img/Pasted%20image%2020250914101354.png)
![](img/Pasted%20image%2020250914101416.png)
## 3、UE5常规操作
参考
[UE5基础操作 - 知乎](https://zhuanlan.zhihu.com/p/657126353)
### 1、视角操作

**移动视角**：按住**鼠标右键**可通过**WASD**来移动视角位置，**E**为垂直向上，**Q**为垂直向下；如果距离交远移动速度慢，按下**鼠标右键**的同时**滚动滚轮**，可加快移动的速度

**快速将视角聚焦到物体：** 在关卡和大纲视图下选中物体，按下**F**可快速将视角聚焦到该物体
### 2、对象操作

**查看对象物体文件的位置**：选中一个物体，按下**CTRL+B**，可在内容浏览器中显示该物体文件的位置。

**将物体紧贴地面**：选中一个物体，按下**End键**，会将该物体向下贴到第一个接触的平面上

### 3、蓝图操作

**断开连线**：按下**alt**+点击一条连线即可断开。

**添加注释**：选中，然后按下C键。
# 蓝图基础知识
生成一个蓝图默认有三个界面：`视口`、`构造脚本`、`事件图表`。
![](img/Pasted%20image%2020250922162950.png)

## 事件图表
一个actor默认有三个事件(Event)，分别为`Event BeginPlay`、`Event ActorBeginOverlap`、`Event Tick` 。
![](img/Pasted%20image%2020250922162129.png)
- `Event BeginPlay`：此actor的播放开始时调用的事件。
- `Event ActorBeginOverlap`：此actor与另一个actor重叠(Overlap)时调用的事件。
- `Event Tick`：启用了tick后每帧调用的事件。

## 数据类型

### 基础数据类型

![](img/Pasted%20image%2020250929143146.png)
#### 简介

| 变量类型                 | 颜色    | 范例                                                                                                                                   | 表示                                                                  |
| -------------------- | ----- | ------------------------------------------------------------------------------------------------------------------------------------ | ------------------------------------------------------------------- |
| **布尔（Boolean）**      | 栗色    | ![](https://d1iv7db44yhgxn.cloudfront.net/documentation/images/f111d278-e8b4-4ca9-9eb2-659d6e2bb4b0/redwire.png)                     | true或false值（`bool`）。                                                |
| **字节（Byte）**         | 夏尔巴蓝色 | ![](https://d1iv7db44yhgxn.cloudfront.net/documentation/images/d126e7e3-4ccd-4658-8050-726aacf69ade/get-byte-variable-icon.png)      | 0与255之间的整数值（`unsigned char`）。                                       |
| **整数（Integer）**      | 海绿色   | ![](https://d1iv7db44yhgxn.cloudfront.net/documentation/images/ef633ada-e995-45a2-bfb3-45122b4acd43/cyanwire.png)                    | −2,147,483,648与2,147,483,647之间的整数值（`int`）。                          |
| **64位整数（Integer64）** | 苔绿色   | ![](https://d1iv7db44yhgxn.cloudfront.net/documentation/images/f922826a-6383-4647-be55-9336e9cfe3e8/get-integer64-variable-icon.png) | −9,223,372,036,854,775,808与9,223,372,036,854,775,807之间的整数值（`long`）。 |
| **浮点（Float）**        | 黄绿色   | ![](https://d1iv7db44yhgxn.cloudfront.net/documentation/images/bbef53fc-9da9-42e5-bcdc-8cde6a99b52a/greenwire.png)                   | 例如0.0553、101.2887、-78.322等带小数的数值（`float`）。                          |
| **命名（Name）**         | 淡紫色   | ![](https://d1iv7db44yhgxn.cloudfront.net/documentation/images/e45ace01-7225-4be8-a2d0-1ef6b3cf916b/get-name-variable-icon.png)      | 用于在游戏中识别事物的一段文本。                                                    |
| **字符串（String）**      | 洋红色   | ![](https://d1iv7db44yhgxn.cloudfront.net/documentation/images/e7c1b819-b2d9-4581-9edf-fa2ae77c70fb/magentawire.png)                 | 例如 `Hello World` 之类的一组字母数字字符（`string`）。                             |
| **文本（Text）**         | 粉色    | ![](https://d1iv7db44yhgxn.cloudfront.net/documentation/images/3d5893d5-c9a5-4e00-8a31-0238632e6ca2/pinkwire.png)                    | 向用户显示的文本。针对要本地化的文本使用此类型。                                            |
| **矢量（Vector）**       | 金色    | ![](https://d1iv7db44yhgxn.cloudfront.net/documentation/images/5e4c00e2-0156-43bf-8c07-bd60121debe2/goldwire.png)                    | 三个数字组成的集（X、Y、Z）。此类型对3D坐标和RGB颜色数据很有用。                                |
| **旋转体（Rotator）**     | 菊蓝色   | ![](https://d1iv7db44yhgxn.cloudfront.net/documentation/images/dd5bcc68-65b5-4e26-847e-8f65147b00d8/purplewire.png)                  | 定义3D空间中旋转的一组数字。                                                     |
| **变形（Transform）**    | 橙色    | ![](https://d1iv7db44yhgxn.cloudfront.net/documentation/images/c2864573-a89e-4bc3-baad-6b45aa86d8bf/orangewire.png)                  | 结合平移（3D位置）、旋转和缩放的数据集。                                               |
| **对象（Object）**       | 蓝色    | ![](https://d1iv7db44yhgxn.cloudfront.net/documentation/images/35463b63-1d09-4b5a-8eb7-11fb68039c02/bluewire.png)                    | 如光源、Actor、静态网格体、摄像机和SoundCue等蓝图对象。                                  |
#### Transform
可以分为**Location**、**Rotaion**和**Scale**。
![](img/Pasted%20image%2020250929143407.png)
其中**Location**和**Scale**都是**Vector**类型的，然后**Rotaion**是旋转体**Rotator**。
### 容器

![](img/Pasted%20image%2020250929150952.png)
#### 数组 Array
![](img/Pasted%20image%2020250929151444.png)
#### 集 Set
不能有重复的元素。
![](img/Pasted%20image%2020250929151548.png)
#### 映射 Map
可选键(key)、值(value)的数据类型。
![](img/Pasted%20image%2020250929151716.png)
![](img/Pasted%20image%2020250929151816.png)

### 枚举
#### 创建
创建枚举类型
![](img/Pasted%20image%2020250929154545.png)
创建命名：E_xxx（E为前缀为枚举类型）
![](img/Pasted%20image%2020250929155210.png)
定义枚举类型
![](img/Pasted%20image%2020250929155105.png)
#### 使用
##### 1、判断当前玩家状态
创建变量PlayerStatusVar，类型为E_PlayerEnum（自己设置的枚举类型）。
然后可以使用Switch进行判断。
![](img/Pasted%20image%2020250929155413.png)
### 结构体
#### 创建
创建，然后命名S开头（S_xxxx）。
![](img/Pasted%20image%2020250929161024.png)
#### 结构
![](img/Pasted%20image%2020250929161928.png)
#### 默认值
![](img/Pasted%20image%2020250929161955.png)
#### 分隔
![](img/Pasted%20image%2020250929161727.png)
#### 使用
![](img/Pasted%20image%2020250929163249.png)
### Object

## 流程控制
### 分支语句（Branch)
判断玩家状态（是否死亡）
![](img/Pasted%20image%2020250929172854.png)

# 蓝图与面向对象

# 蓝图之间的通讯

# 输入系统与动画

# 碰撞系统

# Level(关卡)与Level Blueprint

# UI与HUB

# 游戏控制系统(Game Mode)


# 问题
## 1、世界场景设置在哪里？
在第一次打开UE时（我这里第一次打开UE5.6），发现右侧边栏没有世界场景设置。
**解决方法：** 菜单中的“窗口”->选择“世界场景设置"即可。
## 2、关闭Living Coding
在UE5 IDE右下角，点击三个点，关闭“启用实时代码编写”.
![](img/Pasted%20image%2020250819132259.png)
## 3、资产(Asset)打开之后跳出新窗口，怎么解决？
在编辑->编辑器偏好设置->通用->外观(Appearance)->资产打开路径->主窗口(Main Window)
