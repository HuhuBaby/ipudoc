# iOS客户端设置状态栏和loading框颜色

1. 设置顶部状态栏颜色有两种方式：
    * 在ViewController中调用setStatusBarColor方法
    * 在`Res/config/mobile-config.xml`中添加`statusbar_color`属性
2. 设置资源更新框的ProgressBar颜色
    * 在`Res/config/mobile-config.xml`中添加`progressbar_color`属性

示例：

```
<config name="statusbar_color" value="#FD0000"/>
<config name="progressbar_color" value="#68A2E5"/> 
```

