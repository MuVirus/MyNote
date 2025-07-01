# 下载VSCode
## 安装VSCode
![](img/Pasted%20image%2020250621182223.png)
![](img/Pasted%20image%2020250621182311.png)
选择安装的目录
![](img/Pasted%20image%2020250621182527.png)
最好都选中，以后用方便。
![](img/Pasted%20image%2020250621182607.png)
## 配置中文（非必须）
![](img/Pasted%20image%2020250621182837.png)
重启VSCode
![](img/Pasted%20image%2020250621182914.png)
## 缩放修改
![](img/Pasted%20image%2020250621200048.png)
输入zoom

## 安装Flutter
### 安装Flutter插件
![](img/Pasted%20image%2020250621183048.png)
#### 出现安装错误
那就重新在安装一遍Flutter
![](img/Pasted%20image%2020250621183219.png)
#### 安装成功
![](img/Pasted%20image%2020250621183447.png)
### 安装Flutter SDK
在VSCode中快捷键Ctrl+Shift+P，输入Flutter，点击New Project。
![](img/Pasted%20image%2020250621183638.png)
因为没有安装Flutter SDK，所以弹出这个，我们点击Download SDK。
![](img/Pasted%20image%2020250621183716.png)
会弹出这个，因为没有下载Git，我们需要先下载Git。
![](img/Pasted%20image%2020250621183841.png)
![](img/Pasted%20image%2020250621183943.png)
![](img/Pasted%20image%2020250621184034.png)
![](img/Pasted%20image%2020250621184053.png)
因为下载的地址在Github中，可以先用“迅雷”软件进行下载，或者直接科学上网。
#### 迅雷下载
要合理安装迅雷，迅雷本质属于流氓软件。
选择好目录，同意，然后开始安装。
![](img/Pasted%20image%2020250621184651.png)
取消
![](img/Pasted%20image%2020250621185331.png)
改成你电脑中的下载目录。
![](img/Pasted%20image%2020250621185232.png)

返回到Git界面，在链接中右键，复制链接。
![](img/Pasted%20image%2020250621185427.png)
会弹出
![](img/Pasted%20image%2020250621185510.png)
下载完成。
![](img/Pasted%20image%2020250621185531.png)
![](img/Pasted%20image%2020250621185637.png)直接全部Next。。。


返回到VSCode，Ctrl+Shift+P，输入Flutter，点击Create Project，然后Download SDK。（注意：需要重启VSCode）
选择一个目录来放置Flutter SDK。
![](img/Pasted%20image%2020250621190106.png)
好像也不行，因为通过Git下载的Flutter SDK也是在Github中。

我们通过这个网站
```
https://docs.flutter.cn/community/china/
```
在powershell（通过搜索搜索），输入以下两条命令。
![](img/Pasted%20image%2020250621190619.png)
进行下载
![](img/Pasted%20image%2020250621190644.png)
1、下面可以不需要命令行进行操作，我们在一个英文目录下创建一个目录（目录名为Flutter），然后把压缩包解压缩（可以通过7zip进行解压缩，系统自带解压缩太慢了），然后把里面的内容复制到Flutter目录里。
2、在搜索中输入“环境变量”，系统
![](img/Pasted%20image%2020250621191152.png)
处理过后的，Flutter目录下应该如下
![](img/Pasted%20image%2020250621192910.png)
打开环境变量的方法
![](img/Pasted%20image%2020250621191909.png)
按照红色的进行，然后按下新建，然后输入Flutter下的bin目录路径，然后全部确定即可。
![](img/Pasted%20image%2020250621193335.png)

发现最后Network resources没有问题，只是Android、Chrome、VS没有安装而已，现在可以不用在意。
![](img/Pasted%20image%2020250621194001.png)
通过命令行$env设置的环境变量是临时的，我们可以通过在环境变量中直接配置
![](img/Pasted%20image%2020250621194657.png)
重新打开命令行，输入flutter doctor，看到红框那个，表明配置成功。
![](img/Pasted%20image%2020250621194841.png)
然后在VSCode下，按照之前的操作选择Flutter: Create Project，就不会跳出下载Flutter SDK，而会直接跳出你需要什么模板，选择Application。
![](img/Pasted%20image%2020250621195130.png)
选择一个代码目录
![](img/Pasted%20image%2020250621195638.png)
输入一个小写开头的项目名字
![](img/Pasted%20image%2020250621195815.png)



## 运行
### 网页
通过右下角选择Edge浏览器，进行web开发。
![](img/Pasted%20image%2020250621200741.png)
### 手机
手机需要打开USB调试，并且传输内容为文件（不能为仅充电，这一步其实没啥）。
![](img/Pasted%20image%2020250621201130.png)
#### 有Android Studio的情况下
在设置中，SDK Tool，框图中要选中（其中Command line Tools一定要选中）。
![](img/Pasted%20image%2020250621202935.png)
然后再VSCode下，点击右下角，就可以看到我们的手机设备，就可以进入Lib目录下的main.dart，点击右上角的运行按钮，就可以在手机上调试了。
![](img/Pasted%20image%2020250621203358.png)
#### 未Android Studio的情况下
没有Android Studio的情况下，我们需要下载Android的SDK了。
可以参考：
[sdkmanager  |  Android Studio  |  Android Developers](https://developer.android.google.cn/tools/sdkmanager?hl=zh-cn)
