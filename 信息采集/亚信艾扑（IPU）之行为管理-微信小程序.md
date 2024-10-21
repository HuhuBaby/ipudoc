# 亚信艾扑（IPU）之行为管理-微信小程序

# 1 阅读指南

## 1.1 使用建议

*【内容】*

向读者介绍如何阅读入门这个操作手册，如何快速定位查找、初学者或熟练者该如何分别针对性的阅读此手册的学习方法。

 

## 1.2 阅读对象

*【内容】*

列举了产品操作手册所针对的不同读者。

 

## 1.3 名词解释

*【说明】*

本小节应提供正确解释此文档所需的全部术语的定义、首字母缩写词和缩略语。以保证读者能够准确而一致地理解本文档中出现的概念、术语和缩写。

 

## 1.4 参考资料

*【内容】*

本小节应完整列出此文档中其他部分所引用的任何文档。

# 2 系统简介

## 2.1 系统功能和用途

> 主要用于微信小程序收集信息，并通过UDP协议将收集的信息发送出去。

## 2.1 系统运行环境说明

> 微信小程序基础库2.7.0+（2.7.0才开始支持UDP）
>
> UDP服务只支持域名访问（微信平台限制不能使用IP）

# 3 框架集成

> `SDK下载：`
>
> 链接：https://pan.baidu.com/s/1Y3XPf0bLcy0ODfaPDK00Mg
>
> 提取码：jlow

## 3.1 初始化

### 3.1.1 功能说明

> 微信小程序引入SDK后，需要进行初始化操作。初始化操作只进行一次。多次初始化操作，后续操作不执行，且会在控制台处进行提醒。

### 3.1.2 集成方式

#### 3.1.2.1 原生开发

```js
1) 公共页面引入SDK的js文件
let ca = require('../build/ipu-contact-analysis-miniapp.es.min.js');

2) 初始化
ca.init(track_host, track_port, {
	"debug": false,
	"token": "default",
	"auto_pv": true,
	"session_interval_mins": 30,
	"errorHandler":function(error, type){},
	"onUnsupport":function(error){},
	"onSendFailed":function(error){}
});
```

#### 3.1.2.2 Mpvue框架开发

```js
1) 公共页面引入SDK的js文件
import ca from '../build/ipu-contact-analysis-miniapp.es5.min.js'

2) 初始化
ca.init(track_host, track_port, {
	"debug": false,
	"token": "default",
	"auto_pv": true,
	"session_interval_mins": 30,
	"errorHandler":function(error, type){},
	"onUnsupport":function(error){},
	"onSendFailed":function(error){}
});
```

### 3.1.3 参数说明

| 参数                  | 数据类型 | 描述                                                         |
| --------------------- | -------- | ------------------------------------------------------------ |
| track_host            | String   | **【必传】**接收服务器地址。微信小程序推荐并默认使用域名。非测试环境下，不直接使用IP地址。 |
| track_port            | Number   | **【必传】**接收服务器端口号。                               |
| debug                 | Boolean  | **【可选】**启用debug功能配置，发送的数据会在控制台打印，默认值为false。 |
| token                 | String   | **【可选】**该字段会传输给服务器，可用于校验等操作，默认值为default。 |
| auto_pv               | Boolean  | **【可选】**是否自动触发PV事件，默认值为true（自动触发）。针对mpvue框架开发的小程序，需要设置为false，然后通过ca.send_pv()手动触发。 |
| session_interval_mins | Number   | **【可选】**会话超时时长（分钟），用于在发送事件信息时判断与上一次发送事件信息的时间差是否大于session_interval_mins，大于则会发送销毁事件（ca_session_close）和重建事件（ca_session_start），建议设置成与服务端超时时间一致，默认值为30。 |
| errorHandler          | Function | **【可选】**异常处理统一回调函数，error为异常，type为异常类型，取值为unsupport、sendFailed。 |
| onUnsupport           | Function | **【可选】**微信小程序版本低于2.7.0等导致的SDK不可用等异常处理函数，error为异常。 |
| onSendFailed          | Function | **【可选】**发送事件消息失败的异常处理函数，error为异常。    |

### 3.1.4 调用示例

#### 3.1.4.1 原生开发

```js
1) 公共页面引入SDK的js文件
let ca= require('./utils/ipu-contact-analysis-miniapp.js');

2) 初始化
ca.init("udp.aiipu.com", 8102, {"token": "appName", "auto_pv": false});
```

#### 3.1.4.2 Mpvue方式

```js
1) 公共页面引入SDK的js文件
import ca from '../build/ipu-contact-analysis-miniapp.es5.min.js'

2) 初始化
ca.init("udp.aiipu.com", 8102, {"token": "appName", "auto_pv": false});
```



## 3.2 设置公共参数

### 3.2.1 功能说明

> 事件消息中，除了业务参数之外，可能还存在公共参数。SDK提供设置公共参数的API。该API可调用多次，但每次会覆盖上一次的结果。考虑到实际情况，推荐在执行初始化API之后调用一次。公共参数会写入到事件消息的attributes节点下。

### 3.2.2 调用方式

```js
ca.register_event_super_properties(attributes);
```

### 3.2.3 参数说明

| 参数       | 数据类型 | 描述       |
| ---------- | -------- | ---------- |
| attributes | JSON     | 公共参数。 |

### 3.2.4 调用示例

```js
ca.register_event_super_properties({"common": "This is a WeChat Miniapp Example"});
```

## 3.3 自定义事件

### 3.3.1 功能说明

> 用户可以手动触发自定义事件。触发事件后，SDK将会向指定的服务端发送事件消息。

### 3.3.2 调用方式

```js
ca.send_message(event_id, attributes);
```

### 3.3.3 参数说明

| 参数       | 数据类型 | 描述       |
| ---------- | -------- | ---------- |
| event_id   | String   | 事件ID。   |
| attributes | JSON     | 业务参数。 |

### 3.3.4 调用示例

```js
ca.send_message("test", {"commodityId": "C123456"});
```

## 3.4 PV事件

### 3.4.1 功能说明

> 如果用户在初始化的时候，将auto_pv设置为false，则需要用户手动调用PV事件。触发事件后，SDK将会向指定的服务端发送事件消息。

### 3.4.2 调用方式

```js
ca.send_pv();
```

### 3.4.3 参数说明

无

### 3.4.4 调用示例

```js
ca.send_pv();
```

## 3.5 用户登录

### 3.5.1 功能说明

> SDK提供用户登录API，可将用户标识记录下来。上报的事件消息中将会带上用户标识（userId）。用户可多次登录，但后者会覆盖前者。

### 3.5.2 调用方式

```js
ca.login(user_id);
```

### 3.5.3 参数说明

| 参数    | 数据类型 | 描述       |
| ------- | -------- | ---------- |
| user_id | String   | 用户标识。 |

### 3.5.4 调用示例

```js
ca.login("admin");
```

## 3.6 用户登出

### 3.6.1 功能说明

> 若不调用用户登出，则用户标识一直会保持为上一用户，除非用户清理微信缓存。

### 3.6.2 调用方式

```js
ca.logout();
```

### 3.6.3 参数说明

无

### 3.6.4 调用示例

```js
ca.logout();
```

## 3.7 消息格式定义

```json
{
	"dataType":"se", //取值：se/be;se：系统事件，如会话，用户，pv等;be：业务事件,如自定义事件等
	"userId":"admin", //【option】用户名，登录过有值才传
	"sdkType":"js", //sdk 类型，取值：js/wechat;js：Web端;wechat：微信小程序
	"sdkVersion":"0.1.0", //库文件版本
	"eventId":"ca_pv", //事件id，这里是pv事件
	"time":1582081695941, //发送时间戳
	"persistedTime":1582081674295, //首次访问网站时间戳，类似新用户注册时间
	"deviceId":"1705b6a8c2c3b0-0c4bbe2d01ef35-5a40201d-921600-1705b6a8c2d5ff", //首次访问时创建，代码生成的id
	"pageOpenScene":"Browser", //页面打开场景，取值：Browser/WeChat;Browser：Web端;WeChat：微信小程序
	"token":"default", //该字段会传输给服务器，可用于校验等操作。
	"sessionUuid":"1705b6a8c32499-035e5c16e323df-5a40201d-921600-1705b6a8c335e9", //首次会话创建，会话时长由配置项session_interval_mins决定，默认30分钟
	"attributes":{ //用户设置的通用属性和自定义属性，如果某个业务属性与事件通用属性相同的话，业务属性值只会单次覆盖事件通用属性值，事件通用属性不会做更新操作。
		"事件通用属性1":"事件通用属性值1",
		"事件通用属性2":"事件通用属性值2",
		"业务属性1":"业务属性值1",
		"业务属性2":"业务属性值2"
	},
	"deviceModel":"iPhone", //设备类型
	"deviceOs":"windows", //设备操作系统，如：iPhone、Android、Window等
	"deviceOsVersion":"Win10", //设备操作系统版本，如Win10、Android 10等
	"devicePlatform":"desktop", //设备类型，如desktop、mobile
	"browser":"chrome", //浏览器类型，如safari、WeChat
	"browserVersion":"73.0.3683.86", //浏览器版本,如11.0、7.0.10
	"title":"示例", //页面标题
	"urlPath":"/JavaWebSocket/test/test.html", //页面路径 
	"currentUrl":"http://localhost:8080/JavaWebSocket/test/test.html", //页面绝对地址
	"currentDomain":"localhost:8080", //服务器地址
	"referrer":"", //页面从何处地址跳转至
	"referringDomain":"", //页面从何处域名页面跳转至
	"language":"zh-CN", //浏览器语言环境
	"screenWidth":1280, //屏幕宽度
	"screenHeight":720 //屏幕高度
}
```

# 4附录

## 4.1附录A：FAQ—常见问题解答

*【内容】*
主要对开发框架进行功能扩展的二次开发时，可能出现的主要问题进行解答。
