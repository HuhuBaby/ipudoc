# 1.loadingStart

**功能**

弹出一个“正在加载”的对话框，以便告知用户，程序正在执行一个耗时的任务。##定义WadeMobile.loadingStart(msg,title,cancelable,err);




**参数**

| 参数名     | 参数类型 | 参数描述   | 参数备注     |
| ---------- | -------- | --------------- | ---------------- |
| msg        | string   | 对话框的提示信息     | 必填          |
| title      | string   | 对话框的标题        | 必填          |
| cancelable | boolean  | 点击回退键按钮，是否可以取消对话框 | 可选，默认true；true：可以取消，false：不可以取消 |
| err        | function | 错误信息          | 可选          |



**范例**

```
Mobile.loadingStart("登陆中,请稍等……","等待");
```



**备注**

无



# 2.loadingStop

**功能**

关闭“正在加载”对话框。##定义WadeMobile.loadingStop(err);



**参数**

| 参数名 | 参数类型 | 参数描述 | 参数备注 |
| ------ | -------- | -------- | -------- |
| err    | function | 错误信息 | 可选     |



**范例**

```
WadeMobile.loadingStop();
```



**备注**

无



# 3.tip

**功能**

弹出一个提示信息的对话框，该对话框将在几秒之后渐渐隐去。



**定义**

WadeMobile.tip(msg,type,err);



**参数**

| 参数名 | 参数类型 | 参数描述       | 参数备注      |
| ------ | -------- | ---------------- | ----------------- |
| msg    | string   | 提示信息的内容  | 必填         |
| type   | int      | 提示信息的显示时间 | 可选，0或1，缺省为0；0：此提示信息会在较短时间后渐渐隐去；1：此提示信息会在较长时间后渐渐隐去 |
| err    | function | 错误信息  | 可选          |



**范例**

```
Mobile.tip("业务办理成功！");
```



**备注**

无



# 4.getDate

**功能**

调用原生的时间选择器供用户选择，返回用户选择的时间。



**定义**

WadeMobile.getDate(callback,date,format,err);



**参数**

| 参数名   | 参数类型 | 参数描述           | 参数备注         |
| -------- | -------- | ---------------- | ------------------ |
| callback | function | 回调函数      | 必填           |
| date     | string   | 时间选择器打开时默认选中的时间 | 可选，若不传则获取系统当前时间      |
| format   | string   | 日期格式     | 可选，缺省为yyyy-MM-dd。注意：此处设定的日期格式，应当与defDate格式保持一致。并且返回的日期格式也会自动与此处设定的格式保存一致 |
| err      | function | 错误信息   | 可选            |



**范例**

```
//年月日：date为空，默认选中系统当前时间；format为空，默认yyyy-MM-dd
$("#date1").tap(function() {
    WadeMobile.getDate(function(time) {
        $("#dateContent1").html(time);
    });
});
//年月
$("#date2").tap(function() {
    WadeMobile.getDate(function(time) {
        $("#dateContent2").html(time);
    }, '2012年12月', 'yyyy年MM月');
});
//时分
$("#date3").tap(function() {
    WadeMobile.getDate(function(time) {
        $("#dateContent3").html(time);
    }, '19:12', 'HH:mm');
});
// 年月日时分
$("#date4").tap(function() {
    WadeMobile.getDate(function(date) {
        WadeMobile.getDate(function(time) {
            date += " " + time;
            $("#dateContent4").html(date);
        }, '19:12', 'HH:mm');
    }, '2012/12/19', 'yyyy/MM/dd');
});
```



**备注**

示例中的time:字符串类型，返回用户选择的时间。



# 5.getChoice

**功能**

打开一个原生的选择菜单，返回选择的数据。

**定义**

WadeMobile.getChoice(callback,options,values,title,iconName);

**参数**

| 参数名   | 参数类型 | 参数描述       | 参数备注       |
| -------- | -------- | ------------- | ------------------ |
| callback | function | 回调函数       | 必填       |
| options  | string   | 菜单选项       | 可选，多个值时以英文逗号分隔    |
| values   | string   | 菜单选项对应的值  | 可选，缺省时返回的数据为菜单的索引 |
| title    | string   | 选择菜单的标题     | 可选        |
| iconName | string   | 选择菜单显示的图标 | 可选，缺省时使用系统默认图标。注意："min"：对应客户端res/drawable目录下的min.png图片 |



** 范例**

```
WadeMobile.getChoice(function(value){
    alert("选择的值为：" + value);
},"张三,李四,王五","18,28,38","用户选择","min");
```



**备注**

允许设置自定义图标。 

示例中的value:字符串类型，返回用户选择的菜单选项对应的值。



# 6.showNotification

**功能**

在手机顶栏中弹出一个通知消息



**定义**

WadeMobile.showNotification(callback,text,title,title,iconName,id)



**参数**

| 参数名   | 参数类型 | 参数描述     | 参数备注          |
| -------- | -------- | ------------ | ----------------- |
| callback | function | 回调函数     | 必填                      |
| text     | string   | 消息内容     | 必填，通知消息内容                |
| title    | string   | 消息标题     | 可选，通知消息标题，默认为appName |
| iconName | string   | 提示图标命名  | 可选，小图标R值，默认为appIcon    |
| id       | int      | 通知id值     | 可选，默认为0；         |



**范例**

```
showNotification:function(content,title,icon){
execute("showNotification",[content,title,icon]);
}
```


**备注**

无