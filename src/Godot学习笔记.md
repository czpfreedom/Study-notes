# Godot学习笔记

## 命名规则

类、导出变量和方法使用 PascalCase（大驼峰命名法），私有字段使用 _camelCase（前缀下划线的小驼峰命名法），局部变量和参数使用 camelCase（小驼峰命名法）。连接信号时，请务必准确键入方法名称。

## 脚本

### 默认成员方法

#### _EnterTree()

在Godot中，_EnterTree是一种回调函数，用于在节点被添加到场景树中时执行一些操作。

#### _Process(double delta)

其中变量delta表示时间。

引擎和游戏开发者尽最大努力以恒定的时间间隔更新游戏世界和渲染图像，但在帧的渲染时间上总是存在着微小的变化。这就是为什么引擎为我们提供了这个delta时间值，使我们的运动与我们的帧速率无关。

#### _ExitTree()

与_EnterTree同理，将节点从场景树中移除的时候调用。

## 信号

### 通过界面添加信号

通过c#在界面添加信号目前还有一些bug，需要手动在脚本中添加相关函数，不能自动生成。

以下以官网的例子为例，实现其c#版本。

1. 创建一个sprite节点和一个button节点。
2. 选择 Button 节点，然后在编辑器的右侧，单击检查器旁边名为“节点”的选项卡。双击“pressed”信号，打开节点连接窗口。如下图所示。

<div align=left>
    <img src="..\fig\Godot笔记\信号列表.png" width="50%"/>
</div>


3. 点击连接，生成回调方法。按照惯例，这些回调方法命名为“_on_Button_pressed”。
4. 由于Godot目前对c#支持仍有bug，因此需要手动添加该方法。

完整代码如下，预期效果是点击button的时候，Logo停止。

```c#
public partial class Logo : Sprite2D
{
...
    public void _on_logo_button_pressed() {
        SetProcess(!IsProcessing());
    }
...
}
```

### 用代码连接信号

1. 创建一个sprite节点并添加Timer节点。
2. 选中Timer节点并勾选 **Autostart**（自动开启）属性。
3. 从 Sprite 获取 Timer 的引用。
3. 将Timer的信号与节点相连。

完整代码如下，预期效果是Logo一秒闪一次。

```c#
public partial class Logo : Sprite2D
{
    public override void _Ready()
	{
        Timer timer = GetNode<Timer>("Timer");
        timer.Timeout += _on_Timer_timeout;
    }
    
        private void _on_Timer_timeout() {
        Visible = !Visible;
    }
}
```

### 自定义信号

没搞懂，以后用的时候再说吧。

## Transform2D

## Node

### CanvasItem

#### Node2D

##### CollisionShape2D

- bool **disabled** - 一个有缺陷的碰撞形状在世界上不会造成影响。
- bool **one_way_collision** - 设置碰撞形状是否只检测一侧（顶部或底部）的碰撞。
- Shape2D **shape** - 这个碰撞形状所拥有的实际形状。

##### Path2D

- Curve2D **curve**  - 一个用来描述路径的Curve2D变量

#### Control

##### Label

在一行中显示纯文本，或在一个矩形内包裹。对于格式化的文本，使用RichTextLabel。

##### RichTextLabel

显示富文本的标签。

富文本可以包含自定义文本、字体、图像和一些基本格式。该标签会将这些以内部标签堆栈的形式进行管理。它还可以适应给定的宽度/高度。

## Reference

### Resource

#### Curve2D

在二维空间中，描述一条Bezier curve。

##### Bezier curve（贝塞尔曲线）

1. 一阶Bezier curve

假设有两个端点A和B，那么满足$B(t)=tA+(1-t)B,t\in[0,1]$的点所构成的曲线，称为一阶Bezier curve。

2. 二阶Bezier curve

假设有两个端点A和B，以及锚点P，那么选择AP和PB上的点M和N，使得$t_0=\frac{AM}{AP}=\frac{PN}{PB}$，此时取点O为MN的一阶Bezier curve在$t=t_0$的点，即选择MN上的点O满足$t=\frac{MO}{MN}$。那么$t_0$遍历$[0,1]$时O点所构成的曲线，即为二阶Bezier curve。

二阶Bezier curve的数学公式为$B(t)=t^2A+2t(1-t)P+(1-t)^2B,t\in[0,1]$。

图像如下所示：

<div align=left>
<img src="..\fig\Godot笔记\二阶Bezier curve.webp" />
</div>

3. 二阶Bezier curve的物理解释

可以看作锚点按照t的比例分别拉扯A和B两个端点，线段AB被拉扯的样子。

4. 高阶Bezier curve

n阶Bezier curve可以转化为n-1阶的Bezier curve，方法同二阶转一阶相同。

高阶Bezier curve的数学公式为：

$B(t)=t^nA+nt^{n-1}(1-t)P_1+\cdots+(1-t)^nB$。可以看出来，系数是$(t+(1-t))^n$的二项展开式。

## 拾遗

### 德劳内三角形生成法

### GDExtension

GDExtension 是一个特定于 Godot 引擎 的技术，它允许引擎在运行时与本机共享库进行交互。你可以使用它在不将代码与引擎一起编译的情况下运行本机代码。

GDExtension 不是一种脚本语言，而是一种用于与本机代码交互的技术。它提供了一种方式，让你能够在 Godot 引擎中运行 C++ 代码，而无需重新编译整个引擎。

### GDScript

略。



label

transform2D
