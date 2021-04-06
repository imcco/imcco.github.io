---
title: Java知识梳理（三）—— html&JavaScript&ajax部分
category:
  - Java
  - html
copyright: true
tags:
  - Java
  - html
  - JavaScript
  - ajax
abbrlink: 42994
date: 2017-09-05 19:02:20
---

![image](http://ovi3ob9p4.bkt.clouddn.com/TIETU/CT0011.jpg)

 html&JavaScript&ajax

<!--more-->

##### 判断第二个日期比第一个日期大

​	如何用脚本判断用户输入的的字符串是下面的时间格式2004-11-21 必须要保证用户的输入是此格式，并且是时间，比如说月份不大于12等等，另外我需要用户输入两个，并且后一个要比前一个晚，只允许用JAVASCRIPT，请详细帮助作答.

//这里可用正则表达式判断提前判断一下格式，然后按下提取各时间字段内容

```javascript
<scripttype="text/javascript">
window.onload = function(){
//这么写是为了实现js代码与html代码的分离，当我修改js时，不能影响html代码。
document.getElementById("frm1").onsubmit=
function(){
  var d1 =this.d1.value;
  var d2 =this.d2.value;
  if(!verifyDate (d1)){alert("第一个日期格式不对");return false;}
  if(!verifyDate (d2)){alert("第二个日期格式不对");return false;}
  if(!compareDate(d1,d2)){alert("第二个日期比第一日期小");return false;}
	};
}
functioncompareDate(d1,d2){
    var arrayD1 =d1.split("-");
    var date1 = new Date(arrayD1[0],arrayD1[1],arrayD1[2]);
    var arrayD2 =d2.split("-");
    var date2 = newDate(arrayD2[0],arrayD2[1],arrayD2[2]);
    if(date1 >date2) return false;
    return true;
}
functionverifyDate(d){
	var datePattern= /^\d{4}-(0?[1-9]|1[0-2])-(0?[1-9]|[1-2]\d|3[0-1])$/;
returndatePattern.test(d);
}
</script>
<formid="frm1" action="xxx.html">
<inputtype="text" name="d1" />
<inputtype="text" name="d2" />
<inputtype="submit"/>
</form>
```

#####  用table显示n条记录，每3行换一次颜色，即1，2，3用红色字体，4，5，6用绿色字体，7，8，9用红颜色字体。

```html
<body>
<tableid="tbl">
	<tr><td>1</td></tr>
	<tr><td>2</td></tr>
	<tr><td>3</td></tr>
	<tr><td>4</td></tr>
	<tr><td>5</td></tr>
	<tr><td>6</td></tr>
	<tr><td>7</td></tr>
	<tr><td>8</td></tr>
	<tr><td>9</td></tr>
	<tr><td>10</td></tr>
</table>
</body>
<scripttype="text/javascript">
window.onload=function(){
	var tbl =document.getElementById("tbl");
	rows =tbl.getElementsByTagName("tr");
for(i=0;i<rows.length;i++){
	var j =parseInt(i/3);
	if(j%2==0)rows[i].style.backgroundColor="#f00";
	else rows[i].style.backgroundColor="#0f0";
	}
}
</script>
```

##### HTML 的 form 提交之前如何验证数值文本框的内容全部为数字? 否则的话提示用户并终止提交? 

```javascript
<formonsubmit=’return chkForm(this)’>
<inputtype="text" name="d1"/>
<inputtype="submit"/>
</form>
<scripttype=”text/javascript” />
	function chkForm(this){
		var value= thist.d1.value;
		var len =value.length;
	for(vari=0;i<len;i++){
	if(value.charAt(i)>"9"|| value.charAt(i)<"0"){
		alert("含有非数字字符");return false;
		}
	}
	return true;
}
</script>
```

##### 请写出用于校验HTML文本框中输入的内容全部为数字的javascript代码

```javascript
<inputtype="text" id="d1" onblur=" chkNumber(this)"/>
<scripttype=”text/javascript” />
functionchkNumber(eleText) {
	var value =eleText.value;
	var len =value.length;
for(vari=0;i<len;i++){
	if(value.charAt(i)>"9"|| value.charAt(i)<"0"){
		alert("含有非数字字符"); 
		eleText.focus();
		break;
		}
	}
}
</script>
```

除了写完代码，还应该在网页上写出实验步骤和在代码中加入实现思路，让面试官一看就明白你的意图和检查你的结果。

 
