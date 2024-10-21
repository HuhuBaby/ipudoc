# Session管理

概要
===
>IPU开发框架虽然是混合开发模式的产物，但Session管理却并没有使用Web容器的那一套Session实现，而是充分考虑了服务端隔离层的高可用和水平扩展能力。

##1. IPU Session的原理
IPU的Session管理是脱离Web容器的SNA(Share Nothing Architecture) Session。Session对象存储在分布式缓存中，目前使用的是memcached，支持集群(散列和冗余两种)。
由于客户端的数据请求没有使用ajax技术，而是使用各个平台的原生代码实现，因此也没有使用Cookie来存储SessionId，SessionId是存储在客户端的内存或者沙盒中。

##2. IPU Session的元素
IPU Session的相关元素有：Session、Session管理器(SessionManager)、上下文数据(ContextData)、上下文(Context)、上下文管理器(ContextManager)。
其中Session对象包含上下文数据对象，存储在分布式缓存中，作为一级缓存；上下文对象包含上下文数据对象，作为二级缓存，存储在jvm中。
每一个http请求只会获取一次Session对象，请求结束时，如果上下文数据对象变脏，则通过Session回写到分布式缓存中。
一个http请求的逻辑中如果多次使用上下文数据，第一次从Session对象中获取，同时通过上下文数据对象创建上下文。后面则全部从上下文中获取。这样可以有效减少访问分布式缓存的频率。

##3. IPU Session的具体实现
具体实现全量代码可以参阅：display-sever>更多>场景演示>登陆
- js端触发登陆接口

- 服务端实现登陆，登陆校验成功以后创建上下文数据对象和Session对象，并返回SessionId。
```java
boolean isSuccess = true; //登陆成功
if(isSuccess){
    DisplayContextData displayContextData = new DisplayContextData(param.getString("ACCOUNT"));
    String sessionId = DisplaySessionManager.getInstance().createSession(displayContextData);
    param.put(Constant.Session.SESSION_ID, sessionId);
}
return param;
```
>源码来自com.ai.server.bean.LoginBean

- 上下文对象DisplayContextData是DefaultContextData的子类，封装了一系列的get和set方法，便于使用。
```java
public String getAccount() {
    return contextData.getString(Constant.Context.ACCOUNT, "");
}
public void setAccount(String account) {
    put(Constant.Context.ACCOUNT, account);
}
public String getVerifyCode() {
    return getData().getString("VERIFY_CODE");
}
public void setVerifyCode(String verifyCode) {
    put("VERIFY_CODE", verifyCode);
}
```
>源码来自com.ai.server.core.context.DisplayContextData

- 同时为了使所有bean类方便获取上下文数据对象，定义了一个基类DisplayBean，继承自AppBean，并重写getContextData()方法。
```java
public class LoginBean extends DisplayBean
```
```java
protected DisplayContextData getContextData() throws Exception {
        IContext<?> context = getContext();
        return (DisplayContextData)context.getContextData();
}
```
>源码来自com.ai.server.core.bean.DisplayBean

- Session管理器会默认校验Session是否有效和是否超时。如果存在个性化的Session校验，需要实现自定义Session管理器，继承AbstractSessionManager，实现customVerify方法，并在server-config.xml中注册。
```java
public class DisplaySessionManager extends AbstractSessionManager{
	/**
	 * 自定义的校验逻辑
	 */
	@Override
	public void customVerify(String sessionId, IData param, IContextData contextData) throws Exception {
		……
	}
}
```
```xml
<!-- 自定义Session管理器 -->
<config name="sessionManager" value="com.ai.server.core.session.DisplaySessionManager"/>
```
>源码来自com.ai.server.core.session.DisplaySessionManager 

- 登陆成功返回的SessionId存储在应用内存中
```javascript
有待补充
```
>源码来自display-server/web/biz/js/scene/Login.js

- 后续每次请求都会携带SessionId，因此抽象了Common.callSvc方法，自动装载SessionId。
对于每次请求的参数，都拼接了SessionId和StaffId。
```javascript
Common.get(function(data) {
	if (typeof data == "string") {
		data = new Wade.DataMap(data);
	}
	if (data.get(Constant.SESSION_ID)) {
		param.put(Constant.SESSION_ID, data.get(Constant.SESSION_ID));
	}
	if (data.get(Constant.STAFF_ID)) {
		param.put(Constant.STAFF_ID, data.get(Constant.STAFF_ID));
	}
	callSvc(action, param, callback, isEscape, error);
}, [Constant.SESSION_ID,Constant.STAFF_ID]);
```
>源码来自display-server/web/biz/js/common/common.js

- 对于无需做Session校验的数据接口，关闭校验。找到server-data.xml文件中对应接口的配置，修改verify属性为false即可。
```xml
<action name="LoginBean.login" class="com.ai.server.bean.LoginBean" method="login" verify="false"></action>
```

- Session校验异常处理
从数据接口的返回结果集中可以获取到一些公共信息，通过关键字X_RESULTCODE获取结果编码，通过关键字X_RESULTINFO获取结果信息。
Session校验失败时候返回的编码为-100，可以根据此规则定制Session异常后的逻辑，如：
```javascript
if(x_code==-100){
    Mobile.openPage("Login");
}
```
>源码来自display-server/web/biz/js/common/common.js

- 配置Session的缓存方式
开发时，设置etc\servser-config.xml中的cache为jvm，则不必启用memecached：
```xml
<config name="cache" value="jvm"/>
```
发布时，设置etc\servser-config.xml中的cache为mem，支持集群部署：
```xml
<config name="cache" value="mem"/>
```
Session使用的缓存名固定为**SSN_CACHE**，需要配置memcache.xml，可参阅[服务端>进阶>《缓存使用》]：
```
<?xml version = '1.0' encoding = 'UTF-8'?>
<memcache>
    <default-datacenter>center1</default-datacenter>
    <datacenter name="center1" >
        <cluster name="SSN_CACHE">
            <heartbeat-second>2</heartbeat-second>
            <pool-size>5</pool-size>
            <address master="10.7.5.77:10001"/>
        </cluster>
    </datacenter>
</memcache>  
```