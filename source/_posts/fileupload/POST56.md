---
title: SWFUpload文件上传详解
tags:
  - swfupload
copyright: true
category: fileupload
abbrlink: 15202
date: 2017-12-01 11:34:36
---
![image](http://ovi3ob9p4.bkt.clouddn.com/TIETU/CT0034.jpg)

SWFUpload是一个客户端文件上传工具，最初由Vinterwebb.se开发，它通过整合Flash与JavaScript技术为WEB开发者提供了一个具有丰富功能继而超越传统
input type="file"标签的文件上传模式。 
<!--more-->
官方站点：<http://www.swfupload.org/> 
DEMO地址：<http://demo.swfupload.org/> 

### 主要特点

* 可以同时上传多个文件； 
* 类似AJAX的无刷新上传； 
* 可以显示上传进度； 
* 良好的浏览器兼容性； 
* 兼容其他JavaScript库 (例如：jQuery, Prototype等)； 
* 支持Flash 8和Flash 9； 

SWFUpload不同于其他基于Flash构建的上传工具，它有着优雅的代码设计，开发者可以利用XHTML、CSS和JavaScript来随心所欲的定制它在浏览器下的外观；它还提供了一组简明的JavaScript事件，借助它们开发者可以方便的在文件上传过程中更新页面内容来营造各种动态效果。 

在使用SWFUpload之前，请确认你具备一定的JavaScript和DOM知识。在实际开发中，大部分的错误都是由于错误的设置和低劣的Event Handlers处理程序所造成的。 
<!--more-->

### SWFUpload的文件上传流程： 
* 引入相应的js文件 
* 实例化SWFUpload对象，传入一个配置参数对象进行各方面的配置。 
* 点击SWFUpload提供的Flash按钮，弹出文件选取窗口选择要上传的文件； 
* 文件选取完成后符合规定的文件会被添加到上传的队列里； 
* 调用startUpload方法让队列里文件开始上传； 
* 文件上传过程中会触发相应的事件，开发者利用这些事件来更新ui、处理错误、发出提示等等；

### SWFUpload包括三部分的内容：

SWFUpload.js、swfupload.swf、初始化配置参数及各种事件处理函数。

* 所以首先在页面引入SWFUpload.js

* 在页面中实例化一个SWFUpload对象：

```javascript
var swfu;  
window.onload = function () {  
var settings_object = {//定义参数配置对象  
upload_url : "http://www.swfupload.org/upload.php",  
flash_url : "http://www.swfupload.org/swfupload.swf",  
file_post_name : "Filedata",  
post_params : {  
"post_param_name_1" : "post_param_value_1",  
"post_param_name_2" : "post_param_value_2",  
"post_param_name_n" : "post_param_value_n"  
},  
use_query_string : false,  
requeue_on_error : false,  
http_success : [201, 202],  
assume_success_timeout : 0,  
file_types : "*.jpg;*.gif",  
file_types_description: "Web Image Files",  
file_size_limit : "1024",  
file_upload_limit : 10,  
file_queue_limit : 2,  
debug : false,  
prevent_swf_caching : false,  
preserve_relative_urls : false,  
button_placeholder_id : "element_id",  
button_image_url : "http://www.swfupload.org/button_sprite.png",  
button_width : 61,  
button_height : 22,  
button_text : "<b>Click</b> <span class="redText">here</span>",  
button_text_style : ".redText { color: #FF0000; }",  
button_text_left_padding : 3,  
button_text_top_padding : 2,  
button_action : SWFUpload.BUTTON_ACTION.SELECT_FILES,  
button_disabled : false,  
button_cursor : SWFUpload.CURSOR.HAND,  
button_window_mode : SWFUpload.WINDOW_MODE.TRANSPARENT,  
swfupload_loaded_handler : swfupload_loaded_function,  
file_dialog_start_handler : file_dialog_start_function,  
file_queued_handler : file_queued_function,  
file_queue_error_handler : file_queue_error_function,  
file_dialog_complete_handler : file_dialog_complete_function,  
upload_start_handler : upload_start_function,  
upload_progress_handler : upload_progress_function,  
upload_error_handler : upload_error_function,  
upload_success_handler : upload_success_function,  
upload_complete_handler : upload_complete_function,  
debug_handler : debug_function,  
};  
swfu = new SWFUpload(settings_object);//实例化一个SWFUpload，传入参数配置对象  
};  
/*定义各种事件监听函数*/  
function swfupload_loaded_function(){}  
function file_dialog_start_function(){}  
//...等等  
```

​	我们看到要实现一个SWFUpload上传功能很简单，就是实例化一个SWFUpload对象。但繁琐的地方就在于实例化实要用到的参数配置对象，以及各种事件的发生时机以和提供的参数。所以重点来了。下面几个表格对开发中要用到的东西列举了出来，虽然已经蛮多了，但并不是SWFUpload的全部，我列出来的只是常用的。要查看完整的文档，请到SWFUpload官网上查询。

### 配置参数对象中的常用属性及说明

| 属性                           | 类型          | 默认值      | 描述                                       |
| ---------------------------- | ----------- | -------- | ---------------------------------------- |
| upload_url                   | String      |          | 处理上传文件的服务器端页面的url地址，可以是绝对地址，也可以是相对地址，当为相对地址时相对的是当前代码所在的文档地址 |
| preserve_relative_urls       | Boolean     | false    | 如果为false则SWFUpload会把swfupload.swf用到的相对地址转换为绝对地址，以达到更好的兼容性 |
| file_post_name               | String      | Filedata | 相当于用普通的文件域上传文件时的name属性，服务器端接收页面通过该名称来获取上传的文件 |
| post_params                  | Object(直接量) |          | 一个对象直接量，里面的键/值对会随着每一个文件一起上传，文件上传要附加一些信息时很有用 |
| use_query_string             | Boolean     | false    | 为false时,post_params属性定义的参数会以post方式上传；为true时，则会以get方式上传（即参数会以查询字符串的形式附加到url后面） |
| file_types                   | String      |          | 该属性指定了允许上传的文件类型，当有多个类型时使用分号隔开，比如：*.jpg;*.png ,允许所有类型时请使用 *.* |
| file_types_description       | String      |          | 指定在文件选取窗口中显示的文件类型描述，起一个提示和说明的作用吧         |
| file_size_limit              | String      |          | 指定要上传的文件的最大体积，可以带单位，合法的单位有:B、KB、MB、GB，如果省略了单位，则默认为KB。该属性为0时，表示不限制文件的大小。 |
| file_upload_limit            | Number      |          | 指定最多能上传多少个文件，当上传成功的文件数量达到了这个最大值后，就不能再上传文件了，也不能往上传队列里添加文件了。把该属性设为0时表示不限制文件的上传数量。 |
| file_queue_limit             | Number      |          | 指定文件上传队列里最多能同时存放多少个文件。当超过了这个数目后只有当队列里有文件上传成功、上传出错或被取消上传后，等同数量的其他文件才可以被添加进来。当file_upload_limit的数值或者剩余的能上传的文件数量小于file_queue_limit时，则取那个更小的值 |
| flash_url                    | String      |          | swfupload.swf文件的绝对或相对地址，相对地址是指相对于当前的页面地址。实例化swfupload后，就不能再改变该属性的值了。 |
| prevent_swf_caching          | Boolean     |          | 为true时会加一个随机数在swfupload.swf地址的后面，以阻止flash影片被缓存，这是为了防止某些版本的IE浏览器在读取缓存的falsh影片时出现的bug |
| button_placeholder_id        | String      |          | 指定一个dom元素的id,该dom元素在swfupload实例化后会被Flash按钮代替，这个dom元素相当于一个占位符 |
| button_placeholder           | DOMElement  |          | 指定一个dom元素,该dom元素在swfupload实例化后会被Flash按钮代替，这个dom元素相当于一个占位符。当button_placeholder_id与button_placeholder都存在时，以button_placeholder_id为优先 |
| button_image_url             | String      |          | 指定Flash按钮的背景图片，相对地址或绝对地址都可以。该地址会受到preserve_relative_urls属性的影响，遵从与upload_url一样的规则。该背景图片必须是一个sprite图片,从上到下包含了Flash按钮的正常、鼠标悬停、按下、禁用这四种状态。因此该图片的高度应该是Flash按钮高度的四倍 |
| button_width                 | Number      |          | 指定Flash按钮的宽度                             |
| button_height                | Number      |          | 指定Flash按钮的高度，应该为button_image_url所指定的按钮背景图片高度的1/4 |
| button_text                  | String      |          | 指定Flash按钮上的文字，也可以是html代码                 |
| button_text_style            | String      |          | Flash按钮上的文字的样式，使用方法见示例                   |
| button_text_top_padding      | Number      |          | 指定Flash按钮顶部的内边距，可使用负值                    |
| button_text_left_padding     | Number      |          | 指定Flash按钮左边的内边距，可使用负值                    |
| button_disabled              | Boolean     | false    | 为true时Flash按钮将变为禁用状态，点击也不会触发任何行为         |
| button_cursor                |             |          | 指定鼠标悬停在Flash按钮上时的光标样式，可用值为SWFUpload.CURSOR里定义的常量 |
| button_window_mode           |             |          | 指定Flash按钮的WMODE属性，可用值为SWFUpload.WINDOW_MODE里定义的常量 |
| file_dialog_start_handler    | Function    |          | fileDialogStart事件侦听函数                    |
| file_queued_handler          | Function    |          | fileQueued事件侦听函数                         |
| file_queue_error_handler     | Function    |          | fileQueueError事件侦听函数                     |
| file_dialog_complete_handler | Function    |          | fileDialogComplete事件侦听函数                 |
| upload_start_handler         | Function    |          | uploadStart事件侦听函数                        |
| upload_progress_handler      | Function    |          | uploadProgress事件侦听函数                     |
| upload_error_handler         | Function    |          | uploadError事件侦听函数                        |
| upload_success_handler       | Function    |          | uploadSuccess事件侦听函数                      |
| upload_complete_handler      | Function    |          | uploadComplete事件侦听函数                     |

 

### 各种事件说明

要实现与用户的交互，靠的就是在这些事件上做文章了

 

| fileDialogStart ( )                      |
| ---------------------------------------- |
| 在文件选取窗口将要弹出时触发                           |
| fileQueued ( file object )               |
| 当一个文件被添加到上传队列时会触发此事件，提供的唯一参数为包含该文件信息的file object对象 |
| fileQueueError ( file object, error code, message ) |
| 当文件添加到上传队列失败时触发此事件，失败的原因可能是文件大小超过了你允许的数值、文件是空的或者文件队列已经满员了等。该事件提供了三个参数。第一个参数是当前出现问题的文件对象，第二个参数是具体的错误代码，可以参照SWFUpload.QUEUE_ERROR中定义的常量 |
| fileDialogComplete ( number of files selected, number of files queued, total number of files in the queued ) |
| 当文件选取完毕且选取的文件经过处理后（指添加到上传队列），会立即触发该事件。可以在该事件中调用this.startUpload()方法来实现文件的自动上传参数number of files selected指本次在文件选取框里选取的文件数量参数number of files queued指本次被添加到上传队列的文件数量参数total number of files in the queued指当前上传队列里共有多少个文件（包括了本次添加进去的文件） |
| uploadStart ( file object )              |
| 当文件即将上传时会触发该事件,该事件给了你在文件上传前的最后一次机会来验证文件信息、增加要随之上传的附加信息或做其他工作。可以通过返回false来取消本次文件的上传参数file object为当前要上传的文件的信息对象 |
| uploadProgress ( file object, bytes complete, total bytes ) |
| 该事件会在文件的上传过程中反复触发，可以利用该事件来实现上传进度条参数file object为文件信息对象参数bytes complete为当前已上传的字节数参数total bytes为文件总的字节数 |
| uploadError ( file object, error code, message ) |
| 文件上传被中断或是文件没有成功上传时会触发该事件。停止、取消文件上传或是在uploadStart事件中返回false都会引发这个事件，但是如果某个文件被取消了但仍然还在队列中则不会触发该事件参数file object为文件信息对象参数error code为错误代码，具体的可参照SWFUpload.UPLOAD_ERROR中定义的常量 |
| uploadSuccess ( file object, server data, received response ) |
| 当一个文件上传成功后会触发该事件参数file object为文件信息对象参数server data为服务器端输出的数据 |
| uploadComplete( file object )            |
| 当一次文件上传的流程完成时（不管是成功的还是不成功的）会触发该事件，该事件表明本次上传已经完成，上传队列里的下一个文件可以开始上传了。该事件发生后队列中下一个文件的上传将会开始 |

 

### swfupload实例的方法


方法中大多数是动态改变参数配置对象的方法


| destroy ( )                              |
| ---------------------------------------- |
| 当不需要再使用SWFUpload了的时候,可以使用该方法来销毁它的实例和dom元素 |
| startUpload( file_id )                   |
| 开始上传队列中指定的文件参数file_id代表要上传的文件的id，如果未填写这个参数，则会上传队列中第一个文件 |
| cancelUpload ( file_id, trigger_error_event ) |
| 取消文件的上传参数file_id为要取消的文件的id,如果该参数为undefined或者未填写，则会取消队列里的第一个文件参数trigger_error_event接受一个布尔值，当为false时取消文件不会触发uploadError事件，默认为true |
| stopUpload ( )                           |
| 终止当前正在上传的文件，会触发uploadError事件。如果当前没有文件在上传，则该方法什么都不会做 |
| getStats ( )                             |
| 获取队列的stats object                        |
| setStats ( stats_object )                |
| 修改队列的stats_object，传入修改过的stats_object作为参数 |
| getFile ( file_id, index )               |
| 根据文件id或文件索引来获取一个File Object,当使用文件id时只能获得队列里的文件，当使用文件索引时所有文件（包括队列内和队列外）都可获得 |
| addPostParam ( name, value)              |
| 往配置对象中post_params指定的附加信息对象中增加键/值对        |
| removePostParam ( name)                  |
| 移除置配置对象中的post_params包含的某一个键/值对，参数name为要移除的值的键名 |
| addFileParam ( file_id, name, value)     |
| 为某个特定文件增加随之一起上传的附加信息。注意，只有在该指定的文件上传时，附加的信息才会一起上传。而配置对象中post_param设置的附加信息在任一文件上传时都会与之一起发送。参数file_id为要指定的文件id,参数name和value分别为附加信息的名称和值 |
| removeFileParam ( file_id, name)         |
| 移除通过addFileParam方法增加的附加信息，两个参数相信就不用我多讲了吧 |
| setUploadURL ( url)                      |
| 动态设置配置对象中upload_url的值                    |
| setPostParams ( param_object)            |
| 动态设置配置对象中post_params属性的值，新的值会覆盖旧的值。参数param_object必须为一个对象直接量，且里面的属性和值都只能为字符串 |
| setFileTypes ( types, description)       |
| 动态设置配置对象中file_types 和 file_types_description属性的值。两个参数都不能省略 |
| setFileSizeLimit ( file_size_limit)      |
| 动态设置配置对象中file_size_limit属性的值             |
| setFileUploadLimit ( file_upload_limit)  |
| 动态设置配置对象中file_upload_limit属性的值           |
| setFileQueueLimit ( file_queue_limit)    |
| 动态设置配置对象中file_queue_limit属性的值            |
| setFilePostName ( file_post_name)        |
| 动态设置配置对象中file_post_name属性的值              |
| setUseQueryString ( use_query_string)    |
| 动态设置配置对象中use_query_string属性的值            |
| setButtonImageURL ( url)                 |
| 动态设置配置对象中button_image_url属性的值            |
| setButtonDimensions ( width, height)     |
| 动态设置Flash按钮的宽度和高度，两个参数分别为宽度和高度的值，类型为数字，且不能带单位 |
| setButtonText ( text)                    |
| 动态设置配置对象中button_text属性的值                 |
| setButtonTextStyle ( css_style_text)     |
| 动态设置配置对象中button_text_style属性的值           |
| setButtonTextPadding ( left, top )       |
| 动态设置Flash按钮的左边内边距和顶部内边距                  |
| setButtonDisabled ( isDisabled )         |
| 动态对Flash按钮进行禁用和不禁用的操作，参数为一个布尔值           |
| setButtonCursor ( buttonCursor )         |
| 动态设置配置对象中button_cursor的值                 |


### 文件信息对象 File Object


在事件监听函数中，经常要用到文件信息对象来获取文件的信息以供下一步的操作


| 属性               | 类型     | 描述                                       |
| ---------------- | ------ | ---------------------------------------- |
| id               | String | SWFUpload定义的文件id,用来控制文件的上传               |
| index            | Number | 文件的索引，用在getFile(i)方法中                    |
| name             | String | 文件的原始名称，不包括路径                            |
| type             | String | 文件类型                                     |
| creationdate     | Date   | 文件的创建日期                                  |
| modificationdate | Date   | 文件的最后修改日期                                |
| filestatus       | Number | 当前文件的状态，详细的请参照SWFUpload.FILE_STATUS中定义的常量 |


### 队列状态对象 Stats Object

用来获取当前队列的状况


| 属性                 | 类型     | 描述                              |
| ------------------ | ------ | ------------------------------- |
| in_progress        | Number | 得到的值为1或0，表明当前队列是否有文件正在上传中       |
| files_queued       | Number | 目前上传队列中的文件数量                    |
| successful_uploads | Number | 已成功上传(指触发了uploadSuccess事件)的文件数量 |
| upload_errors      | Number | 上传失败的文件数量(包括被取消上传的文件)           |
| upload_cancelled   | Number | 被取消上传的文件数量                      |
| queue_errors       | Number | 触发了fileQueueError事件的文件数量        |


### 一些常量

定义的一些常量，便于理解

 

| 常量名                                      | 描述                                       |
| ---------------------------------------- | ---------------------------------------- |
| SWFUpload.instances                      | 该常量是一个对象，代表一个页面上所有的SWFUpload实例的引用的集合，用SWFUpload实例的movieName属性进行索引 |
| SWFUpload.movieCount                     | 页面上存在的SWFUpload实例的数量                     |
|                                          |                                          |
| SWFUpload.QUEUE_ERROR.QUEUE_LIMIT_EXCEEDED | 用户选取的文件超过了允许的数量                          |
| SWFUpload.QUEUE_ERROR.FILE_EXCEEDS_SIZE_LIMIT | 文件的体积超过了允许的大小                            |
| SWFUpload.QUEUE_ERROR.ZERO_BYTE_FILE     | 文件是空的                                    |
| SWFUpload.QUEUE_ERROR.INVALID_FILETYPE   | 不允许的文件类型                                 |
|                                          |                                          |
| SWFUpload.UPLOAD_ERROR.HTTP_ERROR        | 服务器返回的状态码不是200                           |
| SWFUpload.UPLOAD_ERROR.MISSING_UPLOAD_URL | 没有设置 upload_url                          |
| SWFUpload.UPLOAD_ERROR.IO_ERROR          | 读取或传输文件时发生错误                             |
| SWFUpload.UPLOAD_ERROR.SECURITY_ERROR    | 上传受到了安全方面的限制                             |
| SWFUpload.UPLOAD_ERROR.UPLOAD_LIMIT_EXCEEDED | 上传的文件数量超过了允许的最大值                         |
| SWFUpload.UPLOAD_ERROR.UPLOAD_FAILED     | 上传出现错误                                   |
| SWFUpload.UPLOAD_ERROR.SPECIFIED_FILE_ID_NOT_FOUND | 给startUpload()方法传入的文件id不存在               |
| SWFUpload.UPLOAD_ERROR.FILE_VALIDATION_FAILED | uploadStart()方法中返回了false                 |
| SWFUpload.UPLOAD_ERROR.FILE_CANCELLED    | 上传被取消了                                   |
| SWFUpload.UPLOAD_ERROR.UPLOAD_STOPPED    | 上传被终止了                                   |
|                                          |                                          |
| SWFUpload.FILE_STATUS.QUEUED             | 文件正在队列中等待上传                              |
| SWFUpload.FILE_STATUS.IN_PROGRESS        | 文件正在上传                                   |
| SWFUpload.FILE_STATUS.ERROR              | 文件在添加到队列或是上传的时候出现了错误                     |
| SWFUpload.FILE_STATUS.COMPLETE           | 文件已上传成功                                  |
| SWFUpload.FILE_STATUS.                   | 文件被取消上传                                  |
|                                          |                                          |
| SWFUpload.CURSOR.ARROW                   | 鼠标以箭头显示                                  |
| SWFUpload.CURSOR.HAND                    | 鼠标以手形显示                                  |
|                                          |                                          |
| SWFUpload.WINDOW_MODE.WINDOW             | Flash按钮会显示在页面的所有dom元素上面                  |
| SWFUpload.WINDOW_MODE.OPAQUE             | 允许其他dom元素覆盖住Flash按钮                      |
| SWFUpload.WINDOW_MODE.TRANSPARENT        | 允许Flash按钮透明显示                            |



### struts2+swfUpload简单例子

###### index.jsp

```jsp
<%@ page language="java" import="java.util.*" pageEncoding="UTF-8"%>  
<%  
String path = request.getContextPath();  
String basePath = request.getScheme()+"://"+request.getServerName()+":"+request.getServerPort()+path+"/";  
%>  
  
<!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN">  
<html>  
  <head>  
  <link href="<%=basePath%>css/default.css" rel="stylesheet" type="text/css" />  
    <script type="text/javascript" src="<%=basePath%>js/swfupload.js"></script>  
    <script type="text/javascript" src="<%=basePath%>js/swfupload.queue.js"></script>  
    <script type="text/javascript" src="<%=basePath%>js/fileprogress.js"></script>  
    <script type="text/javascript" src="<%=basePath%>js/handlers.js"></script>  
    <!-- 初始化swfupload 对象-->  
   <script type="text/javascript">  
        var upload1;  
  
        window.onload = function() {  
            upload1 = new SWFUpload({  
  
                //提交路径  
                upload_url: "upload.action",  
                //向后台传递额外的参数  
                post_params: {"name" : "kaobian"},  
                //上传文件的名称  
                file_post_name: "file",  
                  
                // 下面自己按照字面意思理解  
                file_size_limit : "102400", // 100MB  
                file_types : "*.*",  
                file_types_description : "All Files",  
                file_upload_limit : "10",  
                file_queue_limit : "0",  
  
                // 事件处理  
                file_dialog_start_handler : fileDialogStart,  
                file_queued_handler : fileQueued,  
                file_queue_error_handler : fileQueueError,  
                file_dialog_complete_handler : fileDialogComplete,  
                upload_start_handler : uploadStart,  
                upload_progress_handler : uploadProgress,  
                upload_error_handler : uploadError,  
                upload_success_handler : uploadSuccess,  
                upload_complete_handler : uploadComplete,  
  
                // 按钮的处理  
                button_image_url : "images/XPButtonUploadText_61x22.png",  
                button_placeholder_id : "spanButtonPlaceholder1",  
                button_width: 61,  
                button_height: 22,  
                  
                // Flash Settings  
                flash_url : "js/swfupload.swf",  
                  
  
                custom_settings : {  
                    progressTarget : "fsUploadProgress1",  
                    cancelButtonId : "btnCancel1"  
                },  
                  
                // Debug Settings  
                debug: false  
            });  
         }  
          
    </script>  
  </head>  
    
  <body>  
</div>  
  <div id="content">  
    <form action="upload.action" method="post" name="thisform" enctype="multipart/form-data">  
          
        <table>  
            <tr valign="top">  
                <td>  
                    <div>  
                          
                        <div style="padding-left: 5px;">  
                              
                            <span id="spanButtonPlaceholder1"></span>  
                            <!--<input type="button" value="上传" onclick="upload1.addPostParam('idname',encodeURI(document.getElementById('myFileName').value));upload1.startUpload();"/>  
                            --><input id="btnCancel1" type="button" value="Cancel Uploads" onclick="cancelQueue(upload1);" disabled="disabled" style="margin-left: 2px; height: 22px; font-size: 8pt;" />  
                            <br />  
                        </div>  
                        <div class="fieldset flash" id="fsUploadProgress1">  
                            <span class="legend">文件上传</span>  
                        </div>  
                    </div>  
                </td>  
            </tr>  
        </table>  
    </form>  
    </div>  
  </body>  
</html>  
```

##### Upload.action

```java
package com.action;  
  
import java.io.File;  
import java.io.FileInputStream;  
import java.io.FileOutputStream;  
import java.io.InputStream;  
import java.io.OutputStream;  
  
import org.apache.struts2.ServletActionContext;  
import com.opensymphony.xwork2.ActionSupport;  
  
public class FileUploadAction extends ActionSupport {  
    private File file;  
    private String fileFileName;  
    private String fileContentType;  
    private String savePath;  
  
    public String execute() throws Exception {  
          
        InputStream is = new FileInputStream(file);  
        String root = getSavePath();  
        //String tempName = System.currentTimeMillis()+this.getFileFileName().substring(this.getFileFileName().indexOf("."));     
        File deskFile = new File(root, this.getFileFileName());  
        OutputStream os = new FileOutputStream(deskFile);  
        byte[] bytefer = new byte[1024];  
        int length = 0;  
        while ((length = is.read(bytefer)) != -1) {  
            os.write(bytefer, 0, length);  
        }  
        os.close();  
        is.close();  
        return "success";  
    }  
  
      
    public String getSavePath() {  
        return ServletActionContext.getServletContext().getRealPath(savePath);  
    }  
  
  
    public void setSavePath(String savePath) {  
        this.savePath = savePath;  
    }  
  
  
    public File getFile() {  
        return file;  
    }  
  
    public void setFile(File file) {  
        this.file = file;  
    }  
  
    public String getFileFileName() {  
        return fileFileName;  
    }  
  
    public void setFileFileName(String fileFileName) {  
        this.fileFileName = fileFileName;  
    }  
  
    public String getFileContentType() {  
        return fileContentType;  
    }  
  
    public void setFileContentType(String fileContentType) {  
        this.fileContentType = fileContentType;  
    }  
  
}  
```

##### Web.xml 

```xml
<?xml version="1.0" encoding="UTF-8"?>  
<web-app version="2.5" xmlns="http://java.sun.com/xml/ns/javaee"  
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"  
    xsi:schemaLocation="http://java.sun.com/xml/ns/javaee   
    http://java.sun.com/xml/ns/javaee/web-app_2_5.xsd">  
    <welcome-file-list>  
        <welcome-file>index.jsp</welcome-file>  
    </welcome-file-list>  
    <filter>  
        <filter-name>struts2</filter-name>  
        <filter-class>  
            org.apache.struts2.dispatcher.ng.filter.StrutsPrepareAndExecuteFilter</filter-class>  
    </filter>  
    <filter-mapping>  
        <filter-name>struts2</filter-name>  
        <url-pattern>/*</url-pattern>  
    </filter-mapping>  
</web-app>  
```

##### Struts.xml 

```xml
<?xml version="1.0" encoding="UTF-8" ?>  
<!DOCTYPE struts PUBLIC  "-//Apache Software Foundation//DTD Struts Configuration 2.0//EN" "http://struts.apache.org/dtds/struts-2.0.dtd">  
<struts>  
  
    <!-- 配置struts2.1.8 上传是文件的最大限制为100M -->  
    <constant name="struts.multipart.maxSize" value="104857600" />  
      
    <!-- 设置struts2 上传文件时  保存的临时目录 -->  
    <constant name="struts.multipart.saveDir" value="C:\temp"></constant>  
      
    <package name="struts2" extends="struts-default">  
        <action name="upload" class="com.action.FileUploadAction">  
            <param name="savePath">/upload</param>  
            <result name="success">/index.jsp</result>  
            <result name="input">/index.jsp</result>  
        </action>  
    </package>  
</struts>  
```

​	SWFUpload的上传过程中各个步骤的显示方式要改变；我们需要往服务器传递参数；需要从服务器返回参数；这些问题上面的例子都没有解决。 

看下面这个图片，这是我在项目中的SWFUpload上传附件的效果（其实仿的是126邮箱做的）。 

上传完成。（上传成功的附件不消失，上传发生错误文件消失。） 

删除：删除上传完成的文件（删除后文件逐渐消失）。   

### SWFUpload如何改upload控件的样式

###### 修改上传flash按钮样式

SWFUpload Demo文件中提供了一个XPButtonUploadText_61x22.png 图片文件 

重新制作一个类似的图片文件替换它。 

如果你做的这个图片文件的长款与原图不同，不要忘记修改 

```javascript
/ 按钮的处理  
button_image_url : "images/XPButtonUploadText_61x22.png",  
button_placeholder_id : "spanButtonPlaceholder1",  
button_width: 61,  
button_height: 22,  Java代码  
```

###### 修改上传队列附件样式

这个东西的构建是通过fileprogress.js文件和defaut.css来完成的。 

```js
function FileProgress(file, targetID) {  
    this.fileProgressID = file.id;  
  
    this.opacity = 100;  
    this.height = 0;  
      
  
    this.fileProgressWrapper = document.getElementById(this.fileProgressID);  
    if (!this.fileProgressWrapper) {  
        this.fileProgressWrapper = document.createElement("div");  
        this.fileProgressWrapper.className = "progressWrapper";  
        this.fileProgressWrapper.id = this.fileProgressID;  
  
        this.fileProgressElement = document.createElement("div");  
        this.fileProgressElement.className = "progressContainer";  
  
        var progressCancel = document.createElement("a");  
        progressCancel.className = "progressCancel";  
        progressCancel.href = "#";  
        progressCancel.style.visibility = "hidden";  
        progressCancel.appendChild(document.createTextNode(" "));  
  
        var progressText = document.createElement("div");  
        progressText.className = "progressName";  
        progressText.appendChild(document.createTextNode(file.name));  
  
        var progressBar = document.createElement("div");  
        progressBar.className = "progressBarInProgress";  
  
        var progressStatus = document.createElement("div");  
        progressStatus.className = "progressBarStatus";  
        progressStatus.innerHTML = "&nbsp;";  
  
        this.fileProgressElement.appendChild(progressCancel);  
        this.fileProgressElement.appendChild(progressText);  
        this.fileProgressElement.appendChild(progressStatus);  
        this.fileProgressElement.appendChild(progressBar);  
  
        this.fileProgressWrapper.appendChild(this.fileProgressElement);  
  
        document.getElementById(targetID).appendChild(this.fileProgressWrapper);  
    } else {  
        this.fileProgressElement = this.fileProgressWrapper.firstChild;  
        this.reset();  
    }  
  
    this.height = this.fileProgressWrapper.offsetHeight;  
    this.setTimer();  
  
  
}  
//……  
}  
```

每当我们选择一个文件上传时它就会new FileProgress(file, this.customSettings.progressTarget); 

根据上面的代码分析其实就是在网页中添加了以下html代码 

```js
<div id=file.id class="progressWrapper">  
    <div class="progressContainer" >  
        <a href="#" class="progressCancel" style="visibility:hidden"> </a>  
        <div class="progressName"> file.name </div>  
        <div class="progressBarInProgress"></div>  
        <div class="progressBarStatus">&nbsp;</div>  
    </div>  
</div>  
//css文件在defaut.css中。  
//file.id 和file.name 选中上传文件的id和name属性的值。  
```

这段代码会被插入下面这段代码里面 

```html
<div class="fieldset flash" id="fsUploadProgress1">  
    <span class="legend">文件上传</span>  
</div>  
```

如果你的css和javascrpt能力很强，动态在页面中创建对象改变这个控件的外貌是不是很容易呢。 

选择好事件重写事件代码就ok了。

不过提醒一下，不要改变现有的这些对象的顺序，您看下后面的代码就这道了。 

```java
this.fileProgressElement.childNodes[3].className = "progressBarInProgress";  
this.fileProgressElement.childNodes[3].style.width = percentage + "%";  
```

###### 如何控制上传队列中的文件消失（hidden）情况

```java
// Fades out and clips away the FileProgress box.  
FileProgress.prototype.disappear = function () {//…}  
```

这段代码是FileProgress文件disappear的属性代码。 

 	1) 如果想上传后FileProgress不消失

在fileprogress.js文件找到下面代码，像我一样注释掉最后3行就ok了。当然你也可以在相应的事件中重写函数。 

```js
FileProgress.prototype.setComplete = function () {  
    this.fileProgressElement.className = "progressContainer blue";  
    this.fileProgressElement.childNodes[3].className = "progressBarComplete";  
    this.fileProgressElement.childNodes[3].style.width = "";  
  
    var oSelf = this;  
    //this.setTimer(setTimeout(function () {  
    //  oSelf.disappear();  
    //}, 10000));  
};  
```

​	2) 删除的时候让FileProgress消失

在fileprogress.js文件中增加 

```js
FileProgress.prototype.setDelete = function () {  
    this.fileProgressElement.className = "progressContainer";  
    this.fileProgressElement.childNodes[3].className = "progressBarError";  
    this.fileProgressElement.childNodes[3].style.width = "";  
  
    var oSelf = this;  
    this.setTimer(setTimeout(function () {  
        oSelf.disappear();  
    }, 0));  
};  
```

在删除事件中调用即可。 

### SWFUpload向服务器传递参数

- SWFUpload传递参数有3中方式： 

 1、在url后加连接： 

 ```java
  upload_url: "<%=basePath%>upload?action=up",  
 ```

 2、使用swfupload传递参数，在setting中配置 

 ```java
  post_params: {  
                  "hello" : "Here I Am",  
                  "name" : "张三",  
                  "ff" : document.getElementById("tf").value  
              },  
    
  use_query_string : true,//要传递参数用到的配置  
 ```

 3、采用swfupload的函数： 

 ```java
  addPostParam("myFileName",encodeURI(file.name));//这个是我用来传递文件名称的  
 ```

​           我的方法是修改了一下uploadStart函数 

 ```java
  function uploadStart(file) {  
      try {  
          /* I don't want to do any file validation or anything,  I'll just update the UI and return true to indicate that the upload should start */  
          var progress = new FileProgress(file, this.customSettings.progressTarget);  
            
          //progress.setStatus("Uploading...");  
          progress.setStatus("上传中...");  
          progress.toggleCancel(true, this);  
          this.setPostParams({   
                'fileName':encodeURIComponent(file.name)   
                });   
      }  
      catch (ex) {  
      }  
        
      return true;  
  }  
 ```

 ### SWFUpload接受服务器Action返回的参数

 - 首先我们要了解这个函数 

 ```js
  function uploadSuccess(file, serverData) {  
      try {  
          var progress = new FileProgress(file, this.customSettings.progressTarget);  
          progress.setComplete();  
          progress.setStatus("Complete.");  
          progress.toggleCancel(false);  
    
      } catch (ex) {  
          this.debug(ex);  
      }  
  }  
 ```

 file为上传的文件对象，我们可以获得，file.id、file.name、file.type、file.size 等信息。 

 serverData的意思是服务器返回的数据，如果你指定页面，那服务器返回的应该就是这个jsp的数据了，这里最好用type=json的格式 

 action 代码 

 ```java
  //……  
  //返回页面信息  
         HttpServletResponse response=ServletActionContext.getResponse();  
         response.setContentType("text/html; charset=UTF-8");  
          response.setHeader("Cache-Control", "no-cache");  
          PrintWriter out = response.getWriter();  
          out.write(“action返回的参数”);  
            
          out.flush();  
          out.close();  
    
        return SUCCESS;  
 ```

 struts.xml 

 ```xml
  <package name="upload"[color=red] extends="json-default" [/color]namespace="">  
          <action name="upload" class="com.action.FileUploadAction">  
              <param name="savePath">/upload</param>  
              <result name="success" type="json">/index.jsp</result>  
          </action>  
  </package>  
 ```

 这个配置会出现一个bug（如果你以前没用过json插件的话） 

 当前使用struts2.23版本，使用了jsonplugin-0.3x.jar报错 

 引用

 java.lang.ClassNotFoundException: com.opensymphony.xwork2.util.TextUtils

 解决： 

 在下载好的Struts2的lib文件夹里找到了以下jar包： 

 引用

 json-lib-2.x.jar 
  struts2-json-plugin-2.x.x.jar 
  struts2-junit-plugin-2.x.x.jar

 上面三个包加入项目里之后，再删除jsonplugin-0.3x.jar包 

 ### SWFUpload中文乱码问题

 - 在网上搜的帖子，关于这个问题的解决方法有很多。 

 第一种：fileName= new String(fileName.getBytes(&quot;UTF-8&quot;),&quot;GBK&quot;); 
  用这种方式能解决大部分乱码，但如果文件名中有特殊字符和标点符号有时候会转不过来。 
  第二种：我使用的是这种办法，测试已经通过 

 ```java
  /**在设置时需要设置一下上传事件  
    *upload_start_handler : UploadStart,  
    *动态传参数，解决文件名中文乱码问题  
  **/    
  function uploadStart(file) {  
      try {  
          /* I don't want to do any file validation or anything,  I'll just update the UI and return true to indicate that the upload should start */  
          var progress = new FileProgress(file, this.customSettings.progressTarget);  
            
          //progress.setStatus("Uploading...");  
          progress.setStatus("上传中...");  
          progress.toggleCancel(true, this);  
          this.setPostParams({   
                'fileName':<span style="color: #ff0000;">encodeURIComponent(file.name)   
  </span>           });   
      }  
      catch (ex) {  
      }  
        
      return true;  
  }  
 ```

 在action中使用 

 ```java
  fileName = URLDecoder.decode(fileName, "UTF-8");     
 ```

​       第三种：配置web.config配置文件这种方法我没试，不知道行不行。 

参考：[谈web开中几种经典的大文件上传组件](http://www.cnblogs.com/yjmyzz/archive/2010/03/19/1689996.html) 
