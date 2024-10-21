# 网格通App结合vue开发
本文档为网格通App框架结合vue开发的开发指导。
vue工程打包后生成文件，与网格通App框架传统开发（基于jquery方式）并无区别，都是html+css+js等资源文件，把vue页面当作一个普通的网格通App页面即可，需对vue工程调整以适于网格通App框架环境运行。
<a href="./ipu-vue-base.zip" target="_blank">demo工程下载</a>

## vue调整
需对vue工程的路由和打包设置进行调整才能让vue工程在ipu框架下运行。

#### 路由模式设置
vue页面在ipu框架app中运行时，vur-router的路由模式目前只支持abstract模式(hash模式页面可跳转，但不返回，history模式不能工作)，初始化路由组件时，mode属性设置为abstract（可参考demo工程 src\router\index.js）
```
const router = new VueRouter({
  mode: 'abstract',  
  routes
})
```
abstract下，没有默认路由跳转，需在代码中主动进行一次路由跳转，否则页面加载后未进入任何路由,页面一般显示为空白，可在App.vue组件的creat方法，代码主动进行一次路由跳转（可参考demo工程 src\App.vue）
```

if (this.$router.mode == 'abstract') {      // 路由为absract模式时，需要主动进行一次路由跳转
  this.$router.push({name: 'HelloWorld'});  // HelloWorld应调整为工程实际首页路由名称
}

```

#### vue打包设置
在ipu框架中，ipu页面加载路径都是相对web根目录，需在vue.config.js中配置publicPath，为vue打包后文件在server端工程相对web根目录下路径，路径不正确，页面的资源文件会加载失败

假设要服务端web目录为webapp，vue打后文件放在webapp/template/dist目录，参考配置如下（可参考demo工程 vue.config.js）
```
let isProd = process.env.NODE_ENV === 'production';
let vueFilePath = './template/dist';     // 项目中根据实际路径进行调整

module.exports = {
  lintOnSave: true,       // 移除js编译警告，默认值default，有警告时不能访问页面
  publicPath: isProd ? serverFilePath : '/',  // 打包时使用server工程中的目录位置，非打包使用默认配置
  filenameHashing: !isProd     // 打包生成文件是否hash，ipu更新静态资源不会移除历史已有资源，不使用hash，可减少客户端静态资源体积
};
```

## 调用ipu插件
在vue工程中调用ipu框架插件功能，需在页面引入ipu框架js，修改为vue工程js语法（es6）,再进行调用
#### 引入ipu框架js
将服务端工程中ipu框架js拷贝到vue工程（ipu框架有哪些js文件，可参考demo工程 ipuframe中js文件），请注意不同ipu版本中js文件存在一些差异，请以得到的服务端工程中框架js为准。

#### 修改ipu框架为es6语法
ipu框架js是require.js加载，使用的是amd语法，需调整为es6语法，具体修改可参考demo工程框架js文件，demo工程将ipu框架设定需要暴露的4个对象。
```
export {
  Common,         // 通用业务方法封装
  Mobile,         // 框架对象，适配浏览器与app环境
  IpuMobile,      // 插件对象(部分版本中为WadeMobile对象)
  jcl             // Zepto扩展，增加了jcl.DataMap和jcl.DataSet数据格式，部分插件参数为jcl.DataMap或jcl.DatasetList
}
```
#### 调用插件
在页面引入框架，即可使用框架js调用插件功能，可参考demo工程src\components\PlugDemo.vue
```
// 请求服务端数据接口
var param = new jcl.DataMap(); 
param.put("data", "testCommon");
Common.callSvc("SceneBean.dataRequestScene",param,function (resultData) { // 获取返回的数据
  if (typeof (resultData) == "string") {
    resultData = new jcl.DataMap(resultData);
  }
  alert(resultData);
});
```
## 其它说明

#### 开发生产使用不同路由模式
在app环境只可使用abstract模式，在开发时，在浏览器中运行，可使用其它模式，可在vue工程设置环境变量，在开发模式中使用其它路由模式如hash模式，可参考vue[环境变量和模式](https://cli.vuejs.org/zh/guide/mode-and-env.html)，demo工程参考 .env.development、.env.production，使用环境变量参考src\router\index.js

#### Android返回物理键响应
若需处理Android返回键，让页面进行后退，可参考下面代码

```
let _this = this
if (IpuMobile.isAndroid()) { // 若为APP环境Android环境运行
  IpuMobile.setKeyDownFlag("back", true);  // js负责处理手机物理键返回事件

  IpuMobile.setKeyListener("back", function () {
    // 此判断条件目前只适用于abstract模式，vue-router文档未有描述history描述，此属性非标准属性，不同vue-router版本可能有差异导致判断条件失效
    if (_this.$router.history.index) {  // 其它则调用路由后退
      _this.$router.back();
    } else {            // 退至起始页，则调用退出，
      IpuMobile.setKeyDownFlag("back", false);  // APP负责处理手机物理键返回事件
      IpuMobile.back();    // 若是返回别的页面，调用此方法
      //IpuMobile.close(true);    // 退出app调用此方法，有参数true，则会有确认框，否则没有确认框
    }
  });
}
```

#### 本地代码在app中开发调试
在app中调试vue工程代码，需要先将vue工程打包后，放到服务端工程，再发布，再更新到客户端，整个周期较长，可使用下面方式：
- 条件：有一个本地app，且可访问本机电脑web服务。
- 新建一个ipu页面，在页面加入逻辑：如点击某个按钮后，页面跳转到本地vue工程开发时访问路径，请参考下面代码。
- 将此页面在服务端发布更新，在app中打开此页面，点击按钮后，页面将进入本地vue工程页面，可这般调试vue功能开发，修改本地vue工程代码，不再需要在发布打包和在服务端发布。
```
 // 也可在页面内提供一个输入框，点按钮时让页面进入输入的地址
 location.href = 'http://10.13.13.79:8080/plugdemo'     // 可调整为本地实际路径
```

#### 请求接口数据
若未使用ipu的插件请求数据接口，使用web的ajax请求，在app环境运行时页面为本地方式加载，需要服务端支持跨域，请求路径且需为完整url路径，不能使用相对路径。

#### demo工程说明
- 本工程通过vue脚手架工程生生
- 其中的框架js可参考，不要直接拷贝到工程，js的版本需与客户端服务端一致，否则可能插件功能可能失败出错等。


 {% include "../../common/comment.md" %}
