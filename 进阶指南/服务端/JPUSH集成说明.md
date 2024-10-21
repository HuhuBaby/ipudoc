# JPUSH集成说明

## Android客户端
1. 集成附件中的aar包，放到ipu-mobile-common的lib目录下，并且在应用的build.gradle文件中添加依赖：implementation(name:'ipu-push-jpush-1.0', ext:'aar’)
2. mobile-action.xml配置文件中，添加配置(部分配置可能之前已有，可以先判断一下，不要重复添加):

    ```xml
    <action name="registerForPushWithJpush"    class="com.ai.ipu.push.func.IpuJPushPlugin" method="registerForPush"></action>
        <action name="unregisterForPushWithJpush"  class="com.ai.ipu.push.func.IpuJPushPlugin" method="unregisterForPush"></action>
        <action name="setJpushAlias" class="com.ai.ipu.push.func.IpuJPushPlugin" method="setAlias"></action>
        <action name="deleteAlias" class="com.ai.ipu.push.func.IpuJPushPlugin" method="deleteAlias" />
        <action name="setJpushTags" class="com.ai.ipu.push.func.IpuJPushPlugin" method="setTags"></action>
        <action name="getJpushInfo" class="com.ai.ipu.push.func.IpuJPushPlugin" method="getJpushInfo”/>
    ```

## iOS客户端
1. 集成附件的framework包，放到IPUCommon下
2. mobile-action.xml配置文件中，添加配置(部分配置可能之前已有，可以先判断一下，不要重复添加):
    
    ```xml
    <action name="registerForPushWithJpush"   class="IpuPushJPush" method="registerForPush"/>
    <action name="unregisterForPushWithJpush" class="IpuPushJPush" method="unregisterForPush"/>
    <action name="setJpushTags"               class="IpuPushJPush" method="setPushWithTags"/>
    <action name="setJpushAlias"              class="IpuPushJPush" method="setPushWithAlias"/>
    <action name="setJpushAlias"              class="IpuPushJPush" method="setPushWithAlias"/>
    <action name="getJpushInfo"               class="IpuPushJPush" method="getJpushInfo"/>
    <action name="isNotificationBlocked"      class="IpuPushJPush" method="isNotificationBlocked”/>
    ```

3. AppDelegate.m需要补充一些初始化，可参考附件的AppDelegate.m，进行了jpush操作的地方都需要补充(注意：因为工程不是完全一致，请不要直接覆盖，而是参考后与原沃行销代码做合并)

## 服务端使用说明：
1. 新增插件js定义：
    ```javascript
    getJpushInfo:function(callback,err){
        storageCallback("getJpushInfo", callback);
        execute("getJpushInfo",[],err)
    }
    ```
2. 登录成功时
    ```javascript
    WadeMobile.registerForPushWithJpush(username, function(e){
        alert("出错了：" + e) //这是出错时的回调
    })
    ```
    注册推送，并使用登录用户名username设置别名（alias）。
    后端推送通知时可通过alias指定推送用户
3. 退出登录时
    ```javascript
    //停止接收推送通知
    WadeMobile.unregisterForPushWithJpush() 
    ```
4. 普通页面监听点击通知的事件(可以在一个公共js里写一段代码，所有页面都引入这个js)：
    ```javascript
    WadeMobile.listenerEvent("IPUJPUSHNOTICE", function(msg){
        // msg是json串，包含后端发送通知时传的参数tile、alert、extras。js解析json串获取参数值，实现跳转页面等逻辑
    });
    ```

5. 可能应用在后台，之前打开的页面已经不在了，点击通知会重新打开应用，加载登录页面，登录页面需要主动读取是否有通知：
    ```javascript
    WadeMobile.getJpushInfo(function(res){
        if (res) {
            // res是json串，包含后端发送通知时传的参数tile、alert、extras。js解析json串获取参数值，实现跳转页面等逻辑
        }
    });
    ```
