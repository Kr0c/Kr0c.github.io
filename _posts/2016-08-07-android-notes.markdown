---
layout: post
title: Android学习笔记
tags: Java
---

1.几种基本的应用组件：

  Activity：应用程序的表示层；
  Service：应用程序中不可见的工作者；
  ContentProvider：一个可共享的持久数据存储器；
  Intent：一个强大的应用程序间的消息传递框架；
  BroadcastReceiver：Intent监听器；
  Widget：通常添加到设备主屏幕的可视化应用程序组件；
  Notification：允许向用户发送通知。

2.在Activity组件中，对象和控件是一一对应的，如创建textView对象及调用其方法来控制TextView的属性。

3.为控件绑定监听器：

1. 获取代表控件的类；
2. 定义一个类（内部类），实现监听器接口；
3. 生成监听器对象；
4. 为控件绑定监听器对象；
5. 常用OnClickListener与OnCheckedChangeListener接口作为监视器监视Button（按钮）、CheckBox（多选按钮）和RadioButton（单选按钮）等控件，后者常用方法为setChecked()、isChecked()。

4.控件布局：

1. 使用布局文件完成控件布局（方便，固定）；
2. 在Java代码当中完成控件布局（可以动态改变，灵活）；
3. 距离单位：px, dp, sp.
    - dpi = sqrt(height^2 + width^2) / size;
    - px = dp * (dpi / 160), 使用dp可以让控件根据屏幕像素密度自动适应大小；
4. sp单位通常用于指定字体的大小（首选字体大小为12sp, 14sp, 18sp, 22sp），当用户修改手机显示字体时，sp会随之改变。
5. margin：外边距    padding：内边距；
6. android:gravity:控制元素在控件中的显示位置；android:layout_gravity:控制子控件在父控件中的位置。
7. android:weight:
    - 计算出的宽度权重 =原来宽度 + 剩余空间所占百分比的宽度

5.ImageView控件中，通过scaleType属性（CENTER, CENTER_CROP, CENTER_INSIDE, FIT_CENTER(START, END), FIT_XY）设置图片的显示。

6.相对布局属性：
  - 与兄弟控件的相对位置
  ```
  android:layout_below
  android:layout_above
  android:layout_toLeftOf
  android:layout_toRightOf

  android:layout_alignLeft
  android:layout_alignRight
  android:layout_alignTop
  android:layout_alignBottom

  android:layout_alignBaseLine
  ```

  - 与父控件的相对位置
  ```
  android:layout_alignParentLeft
  android:layout_alignParentRight
  android:layout_alignParentTop
  android:layout_alignParentButton

  android:layout_centerInParent
  android:layout_centerHorizontal
  android:layout_centerVertical
  ```

  - 新特性
  ```
  android:layout_alignStart
  android:layout_alignEnd
  android:layout_alignParentStart
  android:layout_alignParentEnd
  ```

7.时间与日期
  - TimePicker
  - OnTimeChangedListener
  - DatePicker
  - AnalogClock

8.进度条主要属性
  - 进度条最大值：max；
  - 当前进度：progress；
  - 次要进度的值：SecondaryProgress。

9.每创建一个Activity，需要在AndroidManifest.xml中注册。

10.Activity生命周期：

启动一个Activity的方法：

    1. 生成一个意图Intent对象；
    2. 调用setClass方法设置所要启动的Activity；
    3. 调用startActivity方法启动Activity。

Activity的生命周期函数：

生命周期函数 | 调用时机
:---:|:---:
onCreate  | 在Activity对象被第一次创建时调用
onStart   | 当Activity变得可见时调用该函数
onResume  | 当Activity开始准备与用户交互时调用该方法
onPause   | 当系统即将启动另外一个Activity之前调用该方法
onStop    | 当前Activity变得不可见时调用该方法
onDestroy | 当前Activity被销毁之前将会调用该方法
onRestart | 当一个Activity再次启动之前将会调用该方法

**使用onSaveInstanceState()方法保存Activity状态。**


11. Intent组件：
  使用Intent对象传递数据：
    - 在Activity之间可以通过Intent对象传递数据；
    - 使用putExtra()系列方法向Intent对象当中存储数据；
    - 使用getXXXExtra()系列方法从Intent对象当中取出数据；

12. 线程：
  在一个应用程序中，主线程通常用于接收用户的输入，以及将运算结果反馈给用户，因此对于一些可能会产生阻塞的操作，必须放在Worker Thread当中。
  大部分UI控件只能在主线程中修改，ProgressBar等除外。

13. 定位服务：
    - LocationManager：用于管理Android的用户定位服务；
    - LocationProviders：提供多种定位方式。
  ```
  1). 在AndroidManifest.xml中声明权限：
    android.permission.ACCESS_FINE_LOCATION（精确定位）或者android.permission.ACCESS_COARSE_LOCATION（粗略定位）。
  2). 获取LocationManager对象；
  3). 选择LocationProvider；
  4). 绑定LocationListener对象。
  ```
