# Android客户端文件路径

#1. 说明

为文件的统一管理和框架后期的升级，建议，对于本地文件的操作，可以使用以下方式获取相关路径。



#2. 示例

```java

/*==========路径获取============*/
DirectionUtil util = DirectionUtil.getInstance(context);
/*获取图片相对路径*/
//1.
util.getImagePath();
//2.
util.getRelativePath(null, Constant.TYPE_IMAGE);
/*获取图片绝对路径*/
//1.
util.getImageDirection(true);
//2.
util.getDirection(util.getRelativePath(null, Constant.TYPE_IMAGE), true);
/*===================================*/

```

