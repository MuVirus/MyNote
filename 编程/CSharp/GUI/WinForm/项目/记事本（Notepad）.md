# 前言
## 环境
1. IDE：Visual Studio 2010
2. C# WinForm 开发

## 参考
1. Win7记事本

# 步骤
## 页面设计
### 1、主窗体

#### 1、窗体名称、窗体大小、窗体文本
![](img/Pasted%20image%2020250515165048.png)
![](img/Pasted%20image%2020250515165131.png)

#### 2、菜单设计
##### 菜单栏文本
在工具箱 -> 菜单与工具箱 找到 MenuStrip（菜单栏），拖拽到窗体中即可。
按照win7记事本中的菜单栏的文字进行复刻即可。

##### 菜单栏分隔
如果在某一个文本上面想加一个分隔符来分隔，使用鼠标右键->插入->Separator
效果：（另存为与页面设计中间的形式）
![](img/Pasted%20image%2020250515171713.png)

##### 设置快捷键
在每个菜单栏单元的属性中，可以找到ShortcutKeys（快捷键）这个属性，我们就可以设置快捷键了

##### 另起快捷键名称
可以看到上面还有一个ShortcutKeyDisplay，作用是给快捷键另外起名字。
比如Del键的默认名称是Delete，但是我们需要Del名称，我们就可以在ShortcutKeyDisplay中进行重定义名称。

##### 可勾选
选择一项，在属性中找到CheckOnClick，即可。
如果默认想勾选，则Checked选择True即可

##### 菜单名称命名
菜单命名一般以tsmi命名(Tool Strip Menu item)为前缀

#### 3、文本框设计
我们采用富文本框(RickTextBox)

##### 锚定(Anchor)
属性选择Anchor，然后Top，Left，Right，Bottom都勾选。

#### 4、状态栏设计
工具栏选择StatusStrip，拖到窗口即可。
将RightToLeft选择Yes。

## 功能设计
### 保存


### 另存为


### 打开
#### OpenFileDialog
##### 属性
1、title
就是打开对话框的标题。

2、Filter
就是文件过滤器
用法参考：（前一个为文本，后一个为类型（其中*为通配符））
```
文本文檔(*.txt)|*.txt|所有文件|*.*
```

3、SafeFileName
只获取文件名

4、FileName
获取文件地址

### 打印
##### PrintDocument


### 自动换行
#### 原理
通过修改RichTextBox中的WordWarp即可。

### 退出程序


# 问题与解决方案（学习技巧）
## 1、抄别人的界面来回切换麻烦
使用Snipaste软件，使用Shift+F4或者Ctrl+F1截图，然后通过F3进行贴图即可。
