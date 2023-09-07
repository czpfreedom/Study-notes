# Android Studio开发学习

## 1. 什么是Android应用

### 参考资料

https://zhuanlan.zhihu.com/p/628232942

### Android应用运行在哪

​	安卓应用是运行在虚拟机中的。安卓应用是使用Java语言编写的，然后使用Android SDK编译成Dalvik字节码，最后在Dalvik虚拟机中运行。Dalvik虚拟机是Android操作系统的一部分，它是一个基于寄存器的虚拟机，专门为移动设备设计。

#### Dalvik虚拟机

Dalvik虚拟机是Google等厂商合作开发的Android移动设备平台的核心组成部分之一。它可以支持已转换为.dex（即「Dalvik Executable」）格式的Java应用程序的运行。.dex格式是专为Dalvik设计的一种压缩格式，适合内存和处理器速度有限的系统。

Dalvik虚拟机主要负责完成对象生命周期管理、堆栈管理、线程管理、安全和异常管理，以及垃圾回收等。

### Android 四大组件

#### Activity

##### Activity介绍

Activity是一个应用程序组件，提供一个屏幕，用户可以用来交互为了完成某项任务

Activity中所有操作都与用户密切相关，是一个负责与**用户交互**的组件，可以通过setContentView(View)来**显示指定控件**

在一个android应用中，一个Activity通常就是一个单独的屏幕，它上面可以显示一些控件也可以监听并处理用户的事件做出响应。Activity之间通过Intent进行通信

##### Activity 流程

<img src="D:\最重要的文档们\学习笔记\data\Android学习\Activity流程详解.png" width="40%">

在上图中，Activity有三个关键的循环：

1. 整个的生命周期，从onCreate(Bundle)开始到onDestroy()结束。Activity在onCreate()中设置所有的“全局”状态，在onDestory()中释放所有的资源
2. 可见的生命周期，从onStart()开始到onStop()结束。在这段时间，可以看到Activity在屏幕上，尽管有可能不在前台，不能和用户交互。在这两个接口之间，需要保持显示给用户的UI数据和资源等
3.  前台的生命周期，从onResume()开始到onPause()结束。在这段时间里，该Activity处于所有 Activity的最前面，和用户进行交互。Activity可以经常性地在resumed和paused状态之间切换

#### Service服务

Service是android系统的四大组件之一，是一种长生命周期的，没有可视化界面，运行于后台的一种服务程序

##### Service启动方式

1. Started Service

被开启的service通过其他组件调用startService()被创建，这种service可以无限地运行下去，必须调用stopSelf()方法或者其他组件调用stopService()方法来停止它，当service被停止时，系统会销毁它

2. Bounded Service

被绑定的service是当其他组件（一个客户）调用bindService()来创建的，客户可以通过一个IBinder接口和service进行通信，客户可以通过unbindService()方法来关闭这种连接，一个service可以同时和多个客户绑定，当多个客户都解除绑定之后，系统会销毁service

##### Service生命周期

<img src="D:\最重要的文档们\学习笔记\data\Android学习\Service流程详解.png" width="40%">

#### BroadcastReceiver广播接收器

#### Content Provider内容提供者



## 2.  Kotlin的基础知识

### 继承

#### Kotlin

Kotlin中，继承是通过冒号（:）符号实现的。

实例：

```kotlin
class ChildClass : ParentClass() {
    // ChildClass code
}
```

这实际上继承了父类的构造函数。

#### Java

Java中，继承是通过extends关键字实现的。

### 语法

#### 指定函数参数的默认值

例如这个函数声明

```kotlin
public fun ComponentActivity.setContent(    
    parent: CompositionContext? = null,    content: @Composable () -> Unit )
```

在这个函数声明中，第一个参数是CompositionContext类型的，如果没有给值，则默认为null

### 注解

#### @Composable

@Composable是一个注解，用于标记函数是一个组合函数。

组合函数是Compose框架中的一种函数类型，用于定义UI内容。

## 3. Jetpack Compose

## 工程结构

### app

### Manifests

### java

### res

#### drawable

如果您想创建一个 **Drawable**对象，该对象最初并不依赖于由您的代码定义的变量或用户互动，则最好在 XML 中定义 **Drawable**。即使您预期 **Drawable**在用户与您的应用互动时属性会更改，您也应考虑在 XML 中定义该对象，因为您可以在对其进行实例化之后修改其属性。

#### layout

### Gradle Scripts

## 常用类

### BaseAdapter

如下图所示，我们需要理解这四个重写方法。

```java
public class MyBaseAdapter extends BaseAdapter {
    @Override
    public int getCount() {
        return 0;
    }
    @Override
    public Object getItem(int position) {
        return null;
    }
    @Override
    public long getItemId(int position) {
        return 0;
    }
    @Override
    public View getView(int position, View convertView, ViewGroup parent) {
        return null;
    }
}
```

#### getCount

返回要显示的条目的数量。

#### getItem

待学习。

#### getItemId

待学习。

#### getView

返回该条目要显示的界面。

##### convertView

### Context

Context类是一个抽象类，我们Acitvity、Service等类都是Context的子类。

### LayoutInflater

LayoutInflater是用来找layout下xml布局文件，并且实例化。
对于一个没有被载入或者想要动态加载的界面，都需要使用LayoutInflater.inflate()来载入。

#### 获取LayoutInflater实例

```java
LayoutInflater inflater1 = LayoutInflater.from(this);  
LayoutInflater inflater2 = getLayoutInflater();  
LayoutInflater inflater3 = (LayoutInflater) getSystemService(LAYOUT_INFLATER_SERVICE);
```

#### inflate方法

inflate方法有三个参数(int resource, ViewGroup root, boolean attachToRoot)。

参数的含义如下：

1. resource：需要加载布局文件的id，意思是需要将这个布局文件中加载到Activity中来操作。

2. root：需要附加到resource资源文件的根控件，什么意思呢，就是inflate()会返回一个View对象，如果第三个参数attachToRoot为true，就将这个root作为根对象返回，否则仅仅将这个root对象的LayoutParams属性附加到resource对象的根布局对象上，也就是布局文件resource的最外层的View上，比如是一个LinearLayout或者其它的Layout对象。

3. attachToRoot：是否将root附加到布局文件的根视图上。

inflate将返回一个view对象。

## 随记

AndroidManifest.xml和build.gradle比较重要
