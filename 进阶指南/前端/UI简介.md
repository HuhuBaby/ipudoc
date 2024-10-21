# ipuUI

ipuUI是对webapp场景下前端开发，提供一些常用UI组件库，目的是减少前端重复功能开发，提升开发效率，本内容只是简单介绍，UI的详细Demo和API文档见下面链接

+ [UI Demo](http://www.aiipu.com:8084/static/ui/0.2.2/demo/)
+ [API文档](http://www.aiipu.com:8084/static/ui/0.2.2/docs/)

## 与网格通App框架关系
+ 网格通App框架3.0及以前版本，框架不依赖ipuUI，开发者可选择ipuUI为前端库，也可以选择其它第三方UI库来开发。
+ 3.1版本开始，在浏览器上运行页面时，打开新页面的api如openPage对ipuUI存在依赖，默认需引入ipuUI，若要换成其它第三方库需修改框架代码，移除依赖。


## 库文件及依赖
#### 库文件:
  + ipuUI.js
  + ipuUI.css

#### 依赖的第三方库
  + jQuery (工具库)
  + iScroll.js (滚动库，4.x版本，库代码有变动)
  + Hammer.js (手势库)
  + FastClick.js (点击延迟问题处理，库代码有变动)
  + MaterialDesignIcon (demo中使用字体图标库，选用的库，可不使用)

#### requirejs配置
网格通App框架使用requirejs模块化加载js代码，ipuUI.js及依赖库的Requirejs配置，可参考如下配置，第三库的模块名称如iScroll，在ipuUI.js代码中已写死，不可变更配置中第三方库的模块名称

  ```
 require.config({
   paths: {
     'iScroll': 'dep/js/iscroll/iscroll',
     'Hammer': 'dep/js/hammer/hammer',
     'jquery': 'dep/js/jquery/jquery',
     'FastClick': 'dep/js/fastclick/fastclick',
     'ipuUI': 'dist/js/ipuUI'
   }
 });
  ```

## 宽度适配方案
本UI库使用rem和媒体查询处理移动端宽度适配，最小适配宽度为320px，宽度小于320px，样式显示可能会不正常，
若宽度大于640px时，显示内容尺寸同640宽度时显示，如720宽度时内容尺寸按640宽度下尺寸显示

#### rem
rem是css尺寸单位，单位是一个相对值，是根据html元素的字体大小换算得来，若html字体大小为100px，则
+ 1rem = 1*100px = 100px
+ 0.2rem = 0.2*100px = 20px

#### 媒体查询
通过css媒体查询，在不同显示宽度下，调整html元素设置对应字体大小，配合rem来适配页面显示，
UI库中设置了在375px宽度时，html元素字体大小为100px，其它宽度范围时进行计算转换对应值


显示宽度(单位px) | html字体大小(单位px)
---|---
0~320 | 320*100/375 = 85.33333
320~350 | 320*100/375 = 85.33333
350~375 | 350*100/375 = 93.33333
375~384 | 375*100/375 = 100
384~400 | 384*100/375 = 102.4
400~414 | 400*100/375 = 106.66667
411~450 | 414*100/375 = 110.4
450~480 | 450*100/375 = 120
480~520 | 480*100/375 = 128
520~640 | 520*100/375 = 138.66667
640~    | 640*100/375 = 170.66667

#### 整体内容尺寸大小调整
可在html元素上，添加size="sm|lg|xl"，一共有sm、lg、xl三个可选值，则界面内容尺寸整体在默认尺寸上缩小1.1，放大1.1和放大1.1*1.1=1.21倍，
注意此种方式调整，可能导致部分样式异常，慎用此功能。

#### 自定义适配
在html元素上加上class 'ipu-non-query'，则UI库定义的媒体查询适配代码将失效，此时html字体大小默认是320宽度时对应大小，开发人员可再行定义媒体查询适配代码


## 文档结构
UI库对html文档结构设置有一定要求，否则可能导致样式不正常

#### html抬头为h5
```
<!DOCTYPE html>
```

#### viewport设置

```
<meta name="viewport" content="width=device-width, initial-scale=1,minimum-scale=1, maximum-scale=1">

```

#### 完整html文档参考


```
<!DOCTYPE html>
<html>
<head>
  <meta charset="utf-8">
  <title>卡片</title>
  <meta name="viewport" content="width=device-width, initial-scale=1,minimum-scale=1, maximum-scale=1">

  <link rel="stylesheet" type="text/css" href="../../../dist/css/ipuUI.css">
  <link rel="stylesheet" type="text/css" href="../../biz/css/mdi/css/mdi.css">
  <link rel="stylesheet" type="text/css" href="../../biz/css/demo.css">
</head>
<body>
<div class="ipu-flex-row ipu-flex-vertical">
  <div class="ipu-flex-col">
    <header class="ipu-toolbar">
      <a class="ipu-fn-left" href="javascript:history.back(-1);">
        <i class="ipu-icon mdi mdi-chevron-left"></i>
      </a>
      <h1 class="ipu-toolbar-title">卡片</h1>
    </header>
  </div>

  <div class="ipu-flex-col ipu-flex-col-auto">
    <div class="page-content">
        页面内容区...
    </div>
  </div>
</div>
</body>
</html>
```

## 其它
+ UI库定义的class都是以‘ipu-’为前缀，覆写此类class定义的样式要注意，以免影响UI组件正常工作
+ 所有js组件都是通过 ipuUI 对象初始化，一次只能初始化一个组件(若组件初始化参数匹配到多个元素，
  会选择第一个元素进行组件初始化)，若要初始化多个组件，需要分别进行初始化
+ 部分组件有一定html结构要求，有‘ipu-’开头class的元素不能轻易变动结构，若ul元素有‘ipu-’
  开头的class，则对应子元素li虽然没有‘ipu-’开头class，但li仍属于必须的结构，不能变动

{% include "../../common/comment.md" %}