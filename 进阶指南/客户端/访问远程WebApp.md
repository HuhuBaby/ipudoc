# 访问远程WebApp

#场景描述

公司已有自己的web服务端，主要针对了PC端而做。希望增加一个APP的客户端，以浏览器的方式访问远程服务端页面。不做资源本地化，非js模块化编程。



##解决思路

此时ipu客户端主要作为一个手机终端的壳，桥接html页面和手机终端设备。可以考虑将base64.js、expand-mobile.js、jcl.js、mobile-core.js(webview安全注入PluginManager的片断js)、mobile-util.js、wade-mobile.js、zepto.js里面的js接口对象绑定到window上。在使用时，引入这些js文件。

##解决方案

>### 1. 继承NetMobileActivity

```java
package com.ai.mobile.display;
import android.content.pm.ActivityInfo;
import android.os.Bundle;
import com.wade.mobile.app.MobileAppInfo;
import com.wade.mobile.app.MobileOperation;
import com.wade.mobile.frame.activity.NetMobileActivity;
import com.wade.mobile.frame.net.NetWebView;
import com.wade.mobile.ui.comp.dialog.ConfirmBlockDialog;
import com.wade.mobile.util.Messages;
public class MainActivity extends NetMobileActivity {
	@Override
	public void onCreate(Bundle savedInstanceState) {
		setTheme(R.style.Theme_Sherlock_Light);
		MobileAppInfo appInfo = MobileAppInfo.getInstance(this);
		if(appInfo.isTablet()){
			setRequestedOrientation(ActivityInfo.SCREEN_ORIENTATION_LANDSCAPE);
		}else{
			setRequestedOrientation(ActivityInfo.SCREEN_ORIENTATION_PORTRAIT);
		}
		super.onCreate(savedInstanceState);
	}
	@Override
	public void onBackPressed() {
		getWadeMobileClient().shutdownByConfirm(Messages.CONFIRM_CLOSE);
	}
	@Override
	public void loadingError(final NetWebView webView, final int errorCode,
			final String description, final String failingUrl) {
		MainActivity.this.runOnUiThread(new Runnable() {
			@Override
			public void run() {
				try {
					webView.loadAssetHtml("html/welcome.html");
				} catch (Exception e) {
					e.printStackTrace();
				}finally{
					ConfirmBlockDialog dialog = new ConfirmBlockDialog(MainActivity.this, "异常", "资源加载异常，请检查网络与防火墙设置。即将退出应用……" , "确定" , null);
					dialog.show();
					if(dialog.getResult() == ConfirmBlockDialog.Result.OK ){
						MobileOperation.exitApp();
					}
				}
			}
		});
	}
}
```

>### 2. 配置mobile-config.xml
>
>>主要注意如下两个配置项

```xml
<?xml version="1.0" encoding="utf-8"?>
<configs>
	<!-- 非必须， 默认false。是否开启多WebView模式 -->
	<config name="is_mult_webview" value="false"/>
	<!-- 远程服务端地址 -->
    <config name="remote_url" value="http://10.0.2.2:8080/g/html/myindex.html" />
</configs>
```

>>总体配置如下：

```xml
<?xml version="1.0" encoding="utf-8"?>
<configs>
    <!-- 必须。10.0.2.2为Android模拟器的保留ip地址。访问到本机电脑时使用 -->
	<config name="request_host" value="http://10.0.2.2:8080"/>
	<!-- 请求主机名或请求地址。包括服务器的ip地址和端口 -->
	<!-- <config name="request_host" value="http://192.168.0.143:8080"/>  -->
	<!-- 必须。容器应用名或请求根路径 -->
	<config name="request_path" value="/ipu"/>
	<!-- 必须。数据接口的servlet路径 -->
	<config name="request_servlet" value="/mobiledata"/>
	<!-- 非必须，默认应用名。sd卡上的应用根目录名字,解决应用中文名的问题 -->
	<config name="app_path" value="ipu"/>
	<!-- 非必须，默认UTF-8。 应用需要用到的编码 -->
	<config name="encode" value="UTF-8"/>
	<!-- 非必须， 默认false。是否开启多WebView模式 -->
	<config name="is_mult_webview" value="false"/>
	<!-- 必须。应用license -->
	<config name="license" value="GYk5f-NYLRI2OJTdQUWT8GF7aMoeEsjr7CZL76qTTExBJ0LkITg-MYW8eDdcpQFoKrxvlrYk/SSw1Fqo5tMTDu95u6MXZTjt-8Ek1weBr8TC8ZCt988aKBNSgDXCu0qedYK00qaN52Mpxi4WETT7sANs-zmNlkmCxCVzZfjazsQ=|@@MDBCN0FGQ0U4REYxN0E5QTQ5OTFCMDVDREQ2NTI5ODA1MDEwNjA5RjIzMUQ0NTcwODdDQTFEQzcxNjdEMzM1NTVDNjk4NzYwODZCQkY0QTQxRjJFREZGMUNFMTRFNEUwNEExNThGQjExNEFEMTNCOURBMjJFRjhDNDFEMkU1MjU5MTExMjA4NjJFQTk1QzZBOERFOTdCQzg0MkM5MTQwMjkyOUZENzlDMkQyMzk1OEFBN0E2MkY3QUZBMTc3NzlDMzZDM0IwQjc4MTFBRjM5NjJFQzI5QjMwQzZDQ0JENDE0M0I1RTNBRDNDOUIyN0Q5NkMxMzZBNTlCRjlGNEM4QjY5" ></config>
	<!-- 非必须。进入应用时的欢迎页面 --><!-- file:///android_asset为固定写法，表示工程的asset目录 -->
	<config name="loading_page" value="file:///android_asset/local/welcome.html"/>
	<!-- 非必须。更新应用的url -->
	<config name="update_url" value="setup/ipu-client.apk"/>
	<!-- 推送平台服务地址 -->
	<config name="push_address" value="192.168.100.224"/>
	<!-- 推送平台服务端口 -->
	<config name="push_port" value="23456"/>
	<!-- 非必须，默认false。是否强制更新 -->
	<config name="is_force_update" value="true"/>
	<!-- 非必须，默认2。缓存模式 --><!-- APP_CACHE_MODE -1:LOAD_DEFAULT,0:LOAD_NORMAL,1:LOAD_CACHE_ELSE_NETWORK,2:LOAD_NO_CACHE,3:LOAD_CACHE_ONLY -->
	<config name="cache_mode" value="2"/>
	<!-- *********非模板引擎时使用的配置********* -->
	<!-- 非必须，默认false。页面过渡是否显示loading对话框 -->
	<config name="is_loading_dialog" value="false"/>
	<!-- 非必须。页面过渡的loading图片 -->
	<config name="loading_bg_image" value=""/>
	<!-- 非必须,默认false。页面加载超时是否弹出重试对话框 -->
	<config name="is_overtime_retry" value="false"/>
	<!-- 非必须,默认20000。页面加载的超时时间 -->
	<config name="loadurl_timeout" value="20000"/>
	<!-- 非必须，默认false。打开调试模式，不再比对资源版本，客户端每次下载所有资源-->
	<config name="is_debug" value="false"/> 
	<!-- 安全的进行js对象注入，默认true -->
	<config name="safe_inject" value="true" />
	<!-- 远程服务端地址 -->
    <config name="remote_url" value="http://10.0.2.2:8080/g/html/myindex.html" />
</configs>
```

>### 3.下载general-web-server
>
>>百度云链接: http://pan.baidu.com/s/1nudrFp3 密码: krwd  

>### 4.在general-web-server服务端编写测试页面
```html
<!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
<html>
	<head>
		<meta http-equiv="Content-Type" content="text/html; charset=UTF-8">
		<title></title>
		<script src="../res/js/mobile-core.js"></script>
		<script src="../res/js/zepto.js"></script>
		<script src="../res/js/base64.js"></script>
		<script src="../res/js/jcl.js"></script>
		<script src="../res/js/wade-mobile.js"></script>
		<script src="../res/js/expand-mobile.js"></script>
		<script src="../biz/js/common/biz-mobile.js"></script>
	</head>
	<body>
		<script type="text/javascript">
			WadeMobile.tip("世界，你好！");
		</script>
		<div style="margin: 0 auto;"><h1>演示</h1></div>
		<br /><br />
		<button style="width:200px;height:100px;" id="openBrowser">打开浏览器(openBrowser)</button>
		<script type="text/javascript">
			var btn = document.getElementById("openBrowser");
			btn.onclick = function(){
				WadeMobile.openBrowser("http://3g.baidu.com");
			}
		</script>
		<br /><br />
		<button style="width:200px;height:100px;" id="getImei">获取手机IMEI号(回调)</button>
		<script type="text/javascript">
			var btn = document.getElementById("getImei");
			btn.onclick = function(){
				WadeMobile.getImei(function(result){
					alert("手机IMEI号为：" + result);
				});
			}
		</script>
		<br /><br />
		<button style="width: 200px;height: 100px;" id="sendAjaxPost">发送Post请求</button>
		<script type="text/javascript">
			var btn = document.getElementById("sendAjaxPost");
			btn.onclick = function(){
				//创建XMLHttpRequest对象
				xhr = new XMLHttpRequest();
				//注册回调函数
				xhr.onreadystatechange=callback;
				//第一个参数设置成post，第二个写url地址，第三个为是否采用异步方式
				xhr.open("POST","../data",true);
				//post请求需要自己设置请求头
				xhr.setRequestHeader("Content-Type","application/x-www-form-urlencoded");
				//post请求
				xhr.send("name=lisi");
			}
			function callback(){
				//接收响应数据
				//判断对象状态是否交互完成，如果为4则交互完成
				if(xhr.readyState == 4){
					//判断对象状态是否交互成功，如果成功则为200
					if(xhr.status == 200){
						//接收数据，得到服务器输出的纯属文本数据
						var response = xhr.responseText;
						alert("返回的数据为：" + response);
					}
				}
			}
		</script>
		<br /><br />
		<button style="width:200px;height:100px;" id="hrefBaidu">跳转到百度(href)</button>
		<script type="text/javascript">
			var btn = document.getElementById("hrefBaidu");
			btn.onclick = function(){
				window.location.href="http://3g.baidu.com";
			}
		</script>
	</body>
</html>
```

