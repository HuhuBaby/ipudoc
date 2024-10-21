
#iOS客户端信息采集SDK.md

##1 功能概述

提供iOS端网络信息采集SDK，收集移动端的设备信息、系统信息、网络信息、地理信息等等。

##2 集成使用

###2.1 集成SDK

将IpuCount.framework包集成到iOS工程当中即可

###2.2 集成配置文件
创建plist文件，放至工程中的Res/config文件夹下，文件命名为：count-service.plist，配置如下图所示参数：
![count-service-ios](../source/images/count-service-ios.png)

###2.3 API调用
在应用启动方法入口，引入下面的代码即可采集设备信息、系统信息、网络信息、地理信息等。
```javascript
   [IpuCountReport reportAppStart];
```



