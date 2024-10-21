#1.getFloCardInfoBD

**功能**

通过百度平台识别获取水牌上的文字信息

直接传图像信息

```javascript
//定义
WadeMobile.getFloCardInfoBD(callback,type,picInfo,needMark,token,acc_type,err);
```

**参数**

| 参数名        | 参数类型 | 参数描述                                                     | 参数备注           |
| ------------- | -------- | ------------------------------------------------------------ | ------------------ |
| callback      | function | 返回的Json格式字符串                                         | 必填               |
| type          | int      | 0:base64；1:地址                                             | 必填               |
| picInfo       | String   | 与type关联；0:base64字符串；1:图像绝对地址信息               | 必填               |
| needMark      | boolean  | true:返回识别标记图像信息(base64格式)；false:不返回          | 必填               |
| token         | String   | 百度接口所需token                                            | 必填               |
| acc_type      | int      | 0:高精；1：一般                                              | 可选；默认0        |
| back_pic_type | int      | 0:base64；1:地址；返回标记图的类型                           | 可选；默认base64   |
| max_size      | int      | picInfo的最大size（单位为kb）；默认最小50kb；前提参数2type为地址时 | 可选；不填则为原图 |
| err           | function | 错误信息                                                     | 可选               |

返回参数：

JSON数组：

[[识别文字数组],"标记过图片base64信息"]

例如：[["4层"，“1栋”，“北京”,......],"/i9......"]

如果不需要标记图片信息，参数needMark为false即可，识别后将不返回；

**范例**

```javascript
//识别
        $("#ocr_floor_recog").click(function(){
            //
            if(floor_pic_info != null){
                WadeMobile.getFloCardInfoBD(function (result) {
                    var resJson = JSON.parse(result);
                    var wordsArray = resJson[0];
                    var bmpInfo = resJson[1];
                    $("#floor_path").html(wordsArray.toString());
                    $("#floor_pic").html("<img src='"+ "data:image/jpeg;base64," + bmpInfo + "'/>");
                },1,floor_pic_info,true,token,0);
            }
        });
```



**备注**

依赖百度OCR接口;

**注：参数token值需先调用百度平台鉴权认证API获取**,请求URL如下:

```text
https://aip.baidubce.com/oauth/2.0/token?grant_type=client_credentials&client_id=百度平台申请的client_id(AK)&client_secret=百度平台申请的client_secret(SK)
```



# 2.getFloCardInfo

**功能**

识别获取水牌上的文字信息

直接传图像信息

```javascript
//定义
WadeMobile.getFloCardInfo(callback,type,picInfo,needMark,sdk_type,sdk_key,err);
```

**参数**

| 参数名   | 参数类型   | 参数描述                                                     | 参数备注 |
| -------- | ---------- | ------------------------------------------------------------ | -------- |
| callback | function   | 返回的Json格式字符串                                         | 必填     |
| type     | int        | 0:base64；1:地址                                             | 必填     |
| picInfo  | String     | 与type关联；0:base64字符串；1:图像绝对地址信息               | 必填     |
| needMark | boolean    | true:返回识别标记图像信息(base64格式)；false:不返回          | 必填     |
| sdk_type | int        | 0:百度；1:AI2;                                               | 必填     |
| sdk_key  | JsonObject | 百度(第三方)sdk或接口的校验信息；例如百度{ak:"fadf",sk:"afaf"} | 必填     |
| err      | function   | 错误信息                                                     | 可选     |

返回参数：

JSON数组：

[[识别文字数组],"标记过图片base64信息"]

例如：[["4层"，“1栋”，“北京”,......],"/i9......"]

如果不需要标记图片信息，参数needMark为false即可，识别后将不返回；

**范例**

```javascript
//识别
        $("#ocr_floor_recog").click(function(){
            //
            if(floor_pic_info != null){
                WadeMobile.getFloCardInfoBD(function (result) {
                    var resJson = JSON.parse(result);
                    var wordsArray = resJson[0];
                    var bmpInfo = resJson[1];
                    $("#floor_path").html(wordsArray.toString());
                    $("#floor_pic").html("<img src='"+ "data:image/jpeg;base64," + bmpInfo + "'/>");
                },1,floor_pic_info,true,token,0);
            }
        });
```



**备注**

依赖百度OCR接口;

注：token值需实现调用百度平台Api获取：请求URL:

```text
https://aip.baidubce.com/oauth/2.0/token?grant_type=client_credentials&client_id=百度平台申请的client_id(AK)&client_secret=百度平台申请的client_secret(SK)&
```

# 3.getIdCardInfo

##A.获取身份证信息

**功能**

上传身份证图像，自动识别获取身份证图像文字信息

```javascript
//定义
WadeMobile.getIdCardInfo(callback,type,picInfo,needMark,sdk_type,sdk_key,err);
```

**参数**

| 参数名   | 参数类型   | 参数描述                                                     | 参数备注 |
| -------- | ---------- | ------------------------------------------------------------ | -------- |
| callback | function   | 返回的Json格式字符串                                         | 必填     |
| type     | int        | 0：base64；1：地址                                           | 必填     |
| picInfo  | String     | 与type关联；0：base64字符串；1：图像绝对地址信息             | 必填     |
| needMark | boolean    | true:返回识别标记图像信息(base64格式)；false:不返回          | 必填     |
| sdk_type | int        | 0:百度;1:AI2；                                               | 必填     |
| sdk_key  | JsonObject | AI2(第三方)的sdk或接口校验信息；例如AI2{token:"fadf",app_id:"..."} | 必填     |
| err      | function   | 错误信息                                                     | 可选     |

返回参数：

JSON数组：

[[识别文字数组],"标记过图片base64信息"]

例如：[["姓名"，“性别”，“出生”,......],"/i9......"]

如果不需要标记图片信息，参数needMark为false即可，识别后将不返回；



ps:随着第三方的引入，校验流程的不同，看之后是否加入一个第三方类别参数，作为判断校验的逻辑判断；

**范例**

调用：

```javascript
$("#ocr_idcard_recog").click(function(){
            WadeMobile.getIdCardInfo(function (result) {
                var resJson = JSON.parse(result);
                var wordsArray = resJson[0];
                var bmpInfo = resJson[1];
                $("#idcard_path").html(wordsArray.toString());
                $("#idcard_pic").html("<img src='"+ "data:image/jpeg;base64," + bmpInfo + "'/>");
            },1,idcard_pic_info,true,1,ai_sdk_key)
        });
```

# 4.getIdCardInfoByBD

**功能**

通过上传图片信息识别获取身份证数据信息

**参数**

| 参数名   | 参数类型 | 参数描述                                                     | 参数备注                               |
| -------- | -------- | ------------------------------------------------------------ | -------------------------------------- |
| callback | function | 回调                                                         | 必填                                   |
| type     | String   | 0:base64；1:地址                                             | 必填                                   |
| picInfo  | String   | 与type关联；0:base64字符串；1:图像绝对地址信息               | 必填                                   |
| token    | String   | 百度所需token                                                | 必填                                   |
| cardSide | String   | 身份证照类型(front/back);front：身份证含照片的一面；back：身份证带国徽的一面；不填默认为正面front | 选填                                   |
| maxSize  | int      | picInfo的最大size（单位为kb）；默认最小50kb                  | 选填；当type为路径时生效，不填则为原图 |

返回参数:

{"住址":{"location":{"width":412,"top":324,"left":207,"height":89},"words":"湖南省邵阳县五峰铺镇白田村下尤铺组17号"},"出生":{"location":{"width":279,"top":241,"left":211,"height":35},"words":"19760320"},"姓名":{"location":{"width":132,"top":78,"left":206,"height":46},"words":"蒋飞成"},"公民身份号码":{"location":{"width":534,"top":523,"left":368,"height":37},"words":"430523197603204314"},"性别":{"location":{"width":36,"top":165,"left":202,"height":38},"words":"男"},"民族":{"location":{"width":28,"top":167,"left":409,"height":31},"words":"汉"}}

**范例:**

```javascript
$("#ocr_idcard_recog").click(function(){
            WadeMobile.getIdCardInfoByBD(function (result) {
                var resJson = JSON.parse(result);
            },1,idcard_pic_info,token,front)
        });
```



**备注**

目前依赖AI2 接口

注：依赖AI2接口，需先从AI2平台处获取参数token，app_id值，然后传入识别插件；

* token：

  url:http://10.13.6.109:8182/aiSquare/openApi/uc/oauth/token



##B.对接相机或相册

**功能**

调用原生相机或相册能力，获取图像信息后进行识别，

getIdCardInfo(callback,type)

**参数**

| 参数名   | 参数类型 | 参数描述             | 参数备注                                     |
| -------- | -------- | -------------------- | -------------------------------------------- |
| callback |          |                      |                                              |
| type     | int      | 0：相机；1：选择相片 |                                              |
| needLoc  | boolean  | 是否需要返回位置图像 | 如需要(true)，则在callback回调中以base64返回 |



**说明：**

各平台对接鉴权说明流程；如使用中有疑问，请联系我们；

* 百度平台：百度识别需要在百度平台上申请应用后，并使用token来鉴权；
  1.是百度人工平台ak和sk的申请
  2.百度的token申请

* AI2平台：AI2的使用可参考AI2操作文档，token，app_id作为固定参数传入，可以提前申请，只需一次请求；
  -身份证流程：身份证识别接口需要找AI2申请用户名和密码来后去token，并注册申请app_id；
  -人脸流程：人脸识别接口不需要用户名，密码来申请token，只需注册申请app_id即可；