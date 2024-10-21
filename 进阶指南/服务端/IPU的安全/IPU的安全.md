# IPU的安全

###前言
IPU安全目前考虑了传输安全、本地模板安全、接口有效性验证、android代码混淆。其中android代码混淆和接口有效性验证不在这里重复阐述。

###1.数据传输安全
###实现方式
IPU加密传输通过DES+RSA算法实现，流程为：[客户端随机生成DES密钥]>[DES密钥加密数据参数]>[RSA公钥加密DES密钥]>[请求发起-密文数据参数和密文DES密钥]>[服务端RSA私钥解密密文DES密钥]>[DES密钥解密密文数据参数]>[数据参数用于业务逻辑处理并返回结果]>[DES密钥加密返回结果]>[请求响应-密文返回结果]>[客户端DES解密得到返回结果]
在调用dataRequest(dataAction,params)和openPage(pageAction,params)这两个方法时，对params参数进行了加密。
###具体实现步骤如下:
1. 在客户端防止公钥
IPU框架中公私钥命名固定为：public_key和private_key。其中private_key在服务端的classpath下。而public_key则需要放置在客户端中：
    - android版本，public_key放置在res/raw目录下。
    - ios版本，public_key放置在Res/key目录下。

2. 配置需要加密的接口
```xml
<action name="Login.doLogin" class="com.ipu.server.bean.Login" method="doLogin" encrypt="true"></action>
```
###优劣势
优势：
- 模拟https实现的安全传输，既保证安全性，又可灵活指定敏感数据接口。有效降低服务端cpu的使用率，同时提升服务端性能。
- 对于单进程的容器不必要求服务端所有应用都强制使用https，降低影响，减少维护工作量。

劣势：
必须使用IPU服务端，有一定的开发和测试工作量。

>注：接口传输的加密是双向的。


###2.本地模板安全
修改etc/server-config.xml的fileEncrypt属性为true，ant编译build/build.xml，会创建web/encrypt目录存放加密后的html文件。文件加密密钥默认为“12345678”，如果需要修改秘钥，新建类且继承DefaultSecurityHandler，并重写getResKey()方法，最后在server-config.xml中注册。
```xml
<!-- 自定义安全管理器 -->
<config name="securityHandler" value="com.ipu.server.core.handle.XxxxxxHandler"/>
```

[创建公私钥](./创建公私钥.md)

