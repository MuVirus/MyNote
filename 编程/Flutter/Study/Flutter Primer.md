# 创建新工程
## 1、第一次创建
推荐先使用Android Studio进行创建flutter工程，然后再在VsCode中打开flutter工程。如果Android Studio不能创建flutter工程，就先在Android Studio上安装flutter和dart的插件，VSCode中也需要安装flutter和dart插件，也可以安装一个flutter的提示工具。

# Widget
Flutter中几乎所有的对象都是由Widget（部件，控件）构成的。
可以看到Widget是一个抽象类，继承于DiagonosticableTree，然后要实现的方法是createElement。
``` dart
@immutable
abstract class Widget extends DiagnosticableTree
```
``` dart
Element createElement();
```
在Flutter中Widget有两种，一种是StatelessWidget无状态组件，一种是StatefulWidget有状态组件。
## StatelessWidget

继承于Widget，需要实现的方法是build。
``` dart
abstract class StatelessWidget extends Widget
// ...
@protected
Widget build(BuildContext context);
```

## StatefulWidget
继承于Widget，需要实现的方法是createState。
``` dart
abstract class StatefulWidget extends Widget
// ...
@protected
@factory
State createState();
```
## Widget 生命周期

虽然 Widget 本身是不可变的，但 StatefulWidget 关联的 State 对象有明确的生命周期：

1. **创建阶段**
- `createState()`：创建 State 对象
- `initState()`：初始化状态，只调用一次

2. **构建阶段**
- `build()`：构建 Widget 树，每次状态变化都会调用
- `didUpdateWidget()`：当父 Widget 重建导致子 Widget 变化时调用

3. **活跃阶段**
- `setState()`：触发状态更新和重建
- `didChangeDependencies()`：当依赖的 InheritedWidget 变化时调用

4. **销毁阶段**
- `deactivate()`：Widget 即将从树中移除时调用
- `dispose()`：Widget 被永久移除时调用，用于清理资源

理解生命周期有助于正确管理资源和状态，避免内存泄漏。

# 组件
## 基础组件
### 文本
可以看到Text类有很多属性的
``` dart
(new) Text Text(
  String data, {
  Key? key,
  TextStyle? style,
  StrutStyle? strutStyle,
  TextAlign? textAlign,
  TextDirection? textDirection,
  Locale? locale,
  bool? softWrap,
  TextOverflow? overflow,
  double? textScaleFactor,
  TextScaler? textScaler,
  int? maxLines,
  String? semanticsLabel,
  TextWidthBasis? textWidthBasis,
  TextHeightBehavior? textHeightBehavior,
  Color? selectionColor,
})
```
- `data`：表示我们的文本的文字
- `maxLines`、`overflow`：表示最大的行数和溢出时的处理方式。
- `textScalerFactor`：（废弃）表示文字缩放的系数。
- `textScaler`：也表示文字缩放，但是不是直接赋double值的。

**AI生成的快速查表**

| 参数                 | 典型用途                 |
| ------------------ | -------------------- |
| style              | （文本样式）字体、颜色、大小       |
| strutStyle         | 统一行高，避免抖动            |
| textAlign          | 左/右/居中/两端            |
| textDirection      | 强制 RTL 或 LTR         |
| locale             | 多语言字体回退              |
| softWrap           | 是否自动换行               |
| overflow           | 超出后如何显示              |
| textScaler         | 系统字体缩放               |
| maxLines           | 最大行数                 |
| semanticsLabel     | 读屏朗读文本               |
| textWidthBasis     | 宽度计算规则               |
| textHeightBehavior | 首/末行空隙               |
| selectionColor     | 选中背景色（Text本身是不支持选中的） |
### 按钮
可以看看这四类按钮的默认样子。
``` dart
TextButton(
  onPressed: () {}, 
  child: Text("Click me!")
),
ElevatedButton(
  onPressed: () {},
   child: Text("Click me!")
),
OutlinedButton(
  onPressed: () {}, 
  child: Text("Click me!")
),
IconButton(
  onPressed: () {}, 
  icon: Icon(Icons.thumb_up)
)
```
![](img/Pasted%20image%2020250721121255.png)
可以看到
- `TextButton`：默认没有边框，背景透明，没有阴影。
- `ElevatedButton`：默认有背景色，有阴影。
- `OutlinedButton`：默认有边框，无阴影。
- `IconButton`：图标式的按钮。
相同点可以看到，1、他们点击都有一个水波式的动画效果。2、他们点击后会有一个回调函数onPressed。
#### 图标+文字按钮
TextButton、ElevatedButton、OutlinedButton这三类按钮他们都有有一个icon的一个工厂构造函数(Factory Constructor)。
不过有一个缺点是他这个是写好了的，自定义空间有限。
例：
``` dart
TextButton.icon(
    onPressed: () {}, 
    label: Text("Click me!", style: TextStyle(fontSize: 50),),
    icon: Icon(Icons.thumb_up, size: 50,),
)
```
![](img/Pasted%20image%2020250721124956.png)
### 图片
先看一个示例
``` dart
class MyPicture extends StatelessWidget {
  const MyPicture({super.key});

  @override
  Widget build(BuildContext context) {
    return Container(
      margin: EdgeInsets.only(top: 20),
      child: Row(
        mainAxisAlignment: MainAxisAlignment.center,
        children: [
          Image(image: AssetImage("images/author.jpg")),
          Container(
            height: 400,
            width: 300,
            child: Image(image: NetworkImage("https://img.hongyoubizhi.com/picture/pages/regular/2025/03/16/15/132845706_p0_master1200.jpg?x-oss-process=image/resize,m_fill,w_1000")),
          )
        ],
      ),
    );
  }
```
**效果**
其中mainAxisAligment是讲他们进行一个水平居中（主轴）。
![](img/Pasted%20image%2020250721154330.png)
#### 加载本地的图片
先在根目录（项目的根目录）里面创建文件夹images，然后讲图片放在images文件夹内即可，再根目录下的pubsec.yaml文件里flutter项中的asset项的注释去掉 ，像下面这样写即可。（注意：yaml tab是两个空格）
``` yaml
  assets:
    - images/author.jpg
```
使用asset（资产）进行加载图片
``` dart
Image(image: AssetImage("images/author.jpg")),
```
还可以使用下面这种方式，更加简洁。
``` dart
Image.asset("images/author.jpg"),
```
#### 加载网络的图片
使用到NetworkImage，进行加载
``` dart
Image(image: NetworkImage("https://img.hongyoubizhi.com/picture/pages/regular/2025/03/16/15/132845706_p0_master1200.jpg?x-oss-process=image/resize,m_fill,w_1000"),
```
当然可以使用`Image.network`进行简洁化编程。
### Icon
#### 使用flutter自带的图标
``` dart
      child: Row(
        mainAxisAlignment: MainAxisAlignment.center,
        children: [
          Icon(Icons.format_list_bulleted_sharp),
          Icon(Icons.thumb_up)
        ],
      ),
```
![](img/Pasted%20image%2020250721163232.png)
我们可以再Icon类中放入Icons.xx，去使用flutter自带的图标。
再Icon类属性中提供不同的参数，会有不同的效果。
##### 1、修改颜色
例：
``` dart
Icon(Icons.format_list_bulleted_sharp, color: Colors.amber),
```
![](img/Pasted%20image%2020250721163534.png)
将Colors.amber赋值给color属性，为鼠标进行上色。
#### 使用自定义的图标
在flutter中，我们使用的图片的格式为`.ttf`文件拓展名后缀的。这种文件为字体图标，因为ttf一般是字体文件格式。
我们可以在阿里巴巴图标库中收集图标，放进购物车，下载代码，就可以得到我们的ttf文件了。
![](img/Pasted%20image%2020250721164140.png)
解压文件，可以看到有好几个文件，我们需要的就是一个json文件和一个ttf文件。json文件表明我们的图标在ttf对应的信息。ttf文件就是我们的文本图标了。
![](img/Pasted%20image%2020250721164334.png)
我们在flutter工程根目录下新建文件夹fonts，把ttf文件放进fonts文件夹中。
在pubspe.yaml中flutter项下的font项给取消注释，按照下的进行书写。
``` yaml
  fonts:
    - family: FirstIcon
      fonts:
        - asset: fonts/less1.ttf
```
然后再lib文件夹下创建一个文件（什么命名可以，文件拓展名为dart即可），用来进行封装字体成为。我这里叫fontPara.dart，下面是我的代码
``` dart
import 'package:flutter/widgets.dart';

class IconPara {
  static const IconData time = IconData(
    0xe7fc,
    fontFamily: 'FirstIcon',
    matchTextDirection: true
  );
  static const IconData id_card = IconData(
    0xe7fb,
    fontFamily: 'FirstIcon',
    matchTextDirection: true
  );
  static const IconData check_on = IconData(
    0xe7fa,
    fontFamily: 'FirstIcon',
    matchTextDirection: true
  );  
}
```
可以看到
- IconPara是我们的类名
- 我们自定义的icon是IconData类型的。
- 我们必须需要的是codePoint和fontFamily
- codePoint是我们json中unicode码，记得前面加上`0x`即可。
- 然后fontFamily就是我们在pubspe.yaml中定义的fontFamily。
![](img/Pasted%20image%2020250721173450.png)
最后我们在main.dart添加上我的三个自定义图标。
首先在main.dart先导入我们的fonPara.dart文件。
``` dart
import 'package:first_demo/iconPara.dart';
```
然后下面添加
``` dart
Icon(IconPara.check_on),
Icon(IconPara.id_card),
Icon(IconPara.time),
```
重新运行，就显示出来了。
![](img/Pasted%20image%2020250721173854.png)
### 单选开关和复选框

### 输入框及表单

### 进度指示器

## 容器类组件
### Padding
padding在布局中表示组件内边距，而margin表示组件与组件之间的边距。
在Contrainer中，有padding属性，可以下面这样用，参数为EdgeInsets.all(20)，可以与上下左右各格20dp。
``` dart
Icon(Icons.format_list_bulleted_sharp, color: Colors.amber),
Container(
  padding: EdgeInsets.all(20),
  child: Icon(Icons.thumb_up),
),
Icon(IconPara.check_on),
Icon(IconPara.id_card),
Icon(IconPara.time),
```
![](img/Pasted%20image%2020250721191745.png)
但是如果是单纯用Contrainer进行padding去分隔开部件的话，会非常占用内存空间，就有了一个Padding的部件，我们看一下Padding部件的成员，只有三个成员（padding、child、key），单独用padding隔开的话，可以使用Padding用来减少内存消耗。
1. padding就是内边距。
2. child就是Widget类型的孩子
3. key就是一个标签
``` dart
(new) Padding Padding({
  Key? key,
  required EdgeInsetsGeometry padding,
  Widget? child,
})
```
下面是示例。
``` dart
Padding(
  padding: EdgeInsets.all(20),
  child: Icon(IconPara.check_on),
),
```
![](img/Pasted%20image%2020250721192016.png)

## 布局类组件
### 线性布局
#### 主轴与纵轴
线性布局中有主轴与纵轴之分，当布局沿水平方向（一般为竖屏），那么主轴就是水平方向，反之就是垂直方向。
#### Row和Column
1. `Row`的子组件是按照`主轴`依次排列。
2. `Column`的子组件是按照`纵轴`依次排列。
Row类的**成员**
``` dart
(new) Row Row({
  Key? key,
  MainAxisAlignment mainAxisAlignment = MainAxisAlignment.start,
  MainAxisSize mainAxisSize = MainAxisSize.max,
  CrossAxisAlignment crossAxisAlignment = CrossAxisAlignment.center,
  TextDirection? textDirection,
  VerticalDirection verticalDirection = VerticalDirection.down,
  TextBaseline? textBaseline,
  double spacing = 0.0,
  List<Widget> children = const <Widget>[],
})
```
- `key`表示标签
- `xxxAxisAlignment`：表示xxx轴的对齐方式。
- `xxxAxisSize`：表示xxx轴所占空间。
- `MainAxis`：主轴、`CrossAxis`：纵轴
- `TextDirection`：表示主轴上的排列顺序（比如：从左向右）
- `VerticalDirection`：表示纵轴上的排列顺序（比如：从上向下）

### 弹性布局
>弹性布局允许子组件按照一定比例来分配父容器空间。

#### Flex
`Flex`组件可以沿着水平或垂直方向排列子组件，如果你知道主轴方向，使用`Row`或`Column`会方便一些，**因为`Row`和`Column`都继承自`Flex`**，参数基本相同，所以能使用Flex的地方基本上都可以使用`Row`或`Column`。
#### Expanded
Expanded 只能作为 Flex 的孩子（否则会报错），它可以按比例“扩伸”`Flex`子组件所占用的空间。因为 `Row`和`Column` 都继承自 Flex，所以 Expanded 也可以作为它们的孩子。

# Flutter问题以及语法疑难杂症


## dart语法
### 1、`=>` 是什么？
表示单行函数或方法的简写。
