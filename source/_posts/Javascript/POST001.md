---
title: JS实现分页功能
tags: JavaScript
category: JavaScript
date: 2018-02-19 21:44:36
---
![image](http://ovi3ob9p4.bkt.clouddn.com/TIETU/CT0121.jpg)

JS分页
<!--more-->
#### 使用

- 引入JS文件

```js
<script type="text/javascript" src="jquery.min.js"></script>
<script type="text/javascript" src="/dist/MyPaging.js"></script>
<script type="text/javascript">
    $(function() {
        // 调用下拉插件
        $("#AirsList").find(".airsList").MyPaging({
            'btnNum': 5, //分页按钮显示的数目
            'perItem': 10 //每页显示的数目
        });
    });
</script>
```

- 页面模板

```html
<ul class="selecter">
  <li></li>
    .
    .
    .
  <li></li>
</ul>
```

#### MyPaging.js

```js
(function($){
  $.fn.MyPaging = function( options ) {  
    var $this = this;
    var defaults = {
        'btnNum': 5,
        'perItem': 10
    };
    var settings = $.extend(defaults, options);
    return this.each(function() {        
        //插入分页按钮 
        if(!($("#pageGro").length>0)){
            $("head").append(function(){
              var $style ="<style>";
              $style +="#pageGro *{padding:0px;margin:0px;list-style:none;text-align:center;}";
              $style +="#pageGro{clear:both;color:#333;padding:5px 0px 5px 0px;margin:0px auto;padding-top:30px;display:-webkit-box;display:-moz-box;display:-ms-flexbox;display:-webkit-flex;display:flex;align-items:center;justify-content:center;}";
              $style +="#pageGro:after{content:'';display:table;clear:both;}";
              $style +="#pageGro span,#pageGro li{float:left;box-sizing:border-box;height:32px;line-height:30px;border-radius:2px;font-size:15px;color:#333;margin-left:5px;}"; 
              $style +="#pageGro .list{display:-webkit-box;display:-moz-box;display:-ms-flexbox;display:-webkit-flex;display:flex;}";
              $style +="#pageGro li{border:1px solid #999;padding:0 7px;cursor:pointer;text-decoration:none;background:#fff;}";
              $style +="#pageGro .prev,#pageGro .next,#pageGro .start,#pageGro .end{width:63px;border:1px solid #999;cursor:pointer;}";
              $style +="#pageGro li.on,#pageGro li:hover,#pageGro span:hover{background:#003399;color:#fff;border:1px solid #003399;}";
              $style +="#pageGro .dis,#pageGro .dis:hover{border:1px solid #DFDFDF;background-color:#FFF;color:#DFDFDF;cursor:default;}";
              $style +="</style>";
              return $style;
            });
            $this.after("<div id='pageGro'><span class='start dis'>首页</span><span class='prev dis'>上一页</span><ul class='list'></ul><span class='next'>下一页</span><span class='end'>尾页</span>");
        }
        //根据总页数判断，如果小于5页，则显示所有页数，如果大于5页，则显示5页。根据当前点击的页数生成
        var btnNum = settings.btnNum;
        //每页显示的数目
        var perItem = settings.perItem;
        //获取content对象里面，数据的数量
        var totalItems = $this.children().size();
        //计算页面显示的总页数
        var pageCount =1;
        if (totalItems>perItem) {
            pageCount = Math.ceil(totalItems / perItem);
            $("#pageGro span").removeClass("dis");
            $("#pageGro .start").addClass("dis");
            $("#pageGro .prev").addClass("dis");
        }else{
            $("#pageGro span").addClass("dis");
        }
        //默认值
        var startPage = 1;
        var currentPage = 1;
        var lastPage = pageCount;
        var startOn,endOn;
        //隐藏该对象下面的所有子元素
        $this.children().hide();
        //显示第n（perItem）元素
        $this.children().slice(0, perItem).show();
        //生成分页按钮
        creatBtn(1, pageCount, 0);
        if (pageCount > btnNum) {
            showBtn(0, btnNum);
        } else {
            showBtn(0, pageCount);
        }
        //点击分页按钮触发
        $this.parent().on("click", "#pageGro .list li", function() {
            var newCount = $(this).parent().children().size();//获取现在总页数
            if (newCount>1) {
                currentPage = parseInt($(this).html()); //获取当前页数
                //开始
                startOn = (currentPage-1) * perItem;
                //结束
                endOn = startOn + perItem;
                //隐藏内容ul的所有子元素，获取特定项目并显示它们
                $this.children().hide().slice(startOn, endOn).show();
                $(this).addClass("on").siblings("li").removeClass("on");
                if (newCount > btnNum) {
                    //显示页面
                    pageGroup(currentPage, pageCount);
                    console.log(1)
                }
                if (newCount==1) {
                    $("#pageGro span").addClass("dis");
                } else if (currentPage==1&&newCount!=1) {
                    $("#pageGro span").removeClass("dis");
                    $("#pageGro .start").addClass("dis");
                    $("#pageGro .prev").addClass("dis");
                } else if(currentPage==newCount&&newCount!=1) {
                    $("#pageGro span").removeClass("dis");
                    $("#pageGro .end").addClass("dis");
                    $("#pageGro .next").addClass("dis");
                } else {
                    $("#pageGro span").removeClass("dis");
                }
            };
        });
        //点击上一页触发
        $this.parent().on("click", "#pageGro .prev", function() {
            var currentPage = parseInt($("#pageGro li.on").html());//获取当前页
            var newCount = $(this).siblings(".list").children().size();//获取现在总页数
            if (currentPage<=newCount&&currentPage>1) {
                $(this).siblings(".list").find("li").eq(currentPage-2).click();
            }
        });
        //点击下一页触发
        $this.parent().on("click", "#pageGro .next", function() {
            var currentPage = parseInt($("#pageGro li.on").html());//获取当前页
            var newCount = $(this).siblings(".list").children().size();//获取现在总页数
            if (currentPage<newCount) {
                $(this).siblings(".list").find("li").eq(currentPage).click();
            }
        });
        //点击首页
        $this.parent().on("click", "#pageGro .start", function() {
            var currentPage = parseInt($("#pageGro li.on").html());//获取当前页
            var newCount = $(this).siblings(".list").children().size();//获取现在总页数
            if (currentPage<=newCount&&currentPage>1) {
                $(this).siblings(".list").find("li").eq(0).click();
            }
        });
        //点击尾页
        $this.parent().on("click", "#pageGro .end", function() {
            var currentPage = parseInt($("#pageGro li.on").html());//获取当前页
            var newCount = $(this).siblings(".list").children().size();//获取现在总页数
            if (currentPage<newCount) {
                $(this).parents("#pageGro").find(".list li").last().click();
            }
        });
        //根据当前选中页生成页面点击按钮
        function creatBtn(page, count, eq) {
            var $htm_ = "";
            for (var i = page; i <= count; i++) {
                $htm_ += "<li>" + i + "</li>";
            }
            $this.parent().find("#pageGro .list").html($htm_);
            $this.parent().find("#pageGro ul li").eq(eq).addClass('on');
        }
        function showBtn(page, count){
            $this.parent().find("#pageGro .list").children().hide().slice(page, count).show();
        }
        //点击跳转页面
        function pageGroup(pageNum, pageCount) {
            if (pageNum<Math.ceil(btnNum/2)) {
                showBtn(0, btnNum);
            } else if(pageNum>=pageCount-Math.ceil(btnNum/2)) {
                showBtn(pageCount-btnNum, pageCount);
            }else{
                showBtn(pageNum - Math.ceil(btnNum/2 -1), pageNum + Math.ceil(btnNum/2 +1));
            }
        }
    });
  };
})(jQuery);
```

#### index.html


```html
<!DOCTYPE html>
<html>

<head>
    <meta charset="utf-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Jquery分页插件</title>
    <link rel="stylesheet" type="text/css" href="../dist/MySelect.css">
    <script type="text/javascript" src="jquery.min.js"></script>
    <script type="text/javascript" src="../dist/MyPaging.js"></script>
    <script type="text/javascript">
      $(function() {
            $("#AirsList").find(".airsList").MyPaging({
                'btnNum': 5, //分页按钮显示的数目
                'perItem': 10 //每页显示的数目
            });
      });
    </script>
</head>

<body style="font-size:14px;width:100%;height:100%;">
    <ul class="selecter" style="text-align:center;">
        <li>109222</li>
        <li>109222</li>
        <li>109222</li>
        <li>109222</li>
    </ul>
</body>

</html>
```
