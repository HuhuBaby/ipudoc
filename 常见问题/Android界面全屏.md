# Android界面全屏

#1. 场景描述
在某些页面，比如欢迎页面、广告页面，页面可能需要全屏展示，去掉标题栏和状态栏。

#2. 解决思路
通过设置原生窗口属性去掉标题栏和状态栏，让界面全屏显示。

#3. 解决方法
在Android的客户端的MainActivity中，重写下面这个方法：

```java
@Override
protected void settingWindowStyle(Window window) {
    // 在窗口显示之前，设置窗口无标题
    requestWindowFeature(Window.FEATURE_NO_TITLE);
    // 在窗口显示之前，设置窗口全屏显示（即无状态栏）
    getWindow().setFlags(WindowManager.LayoutParams.FLAG_FULLSCREEN,  WindowManager.LayoutParams.FLAG_FULLSCREEN);
}

```


