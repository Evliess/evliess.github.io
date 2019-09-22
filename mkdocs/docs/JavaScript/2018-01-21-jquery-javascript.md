---
layout: index
title: "Javascript handbook"
category: javascript
date: 2018-01-21 15:17:55
---

# Javascript handbook
## Bootstrap 

```javascript

//禁止bootstrap carousel 自动播放

$('.carousel').carousel({
  interval: false
})

```

## Jquery UI Autocomplete  

```javascript
// 在bootstrap modal 上不显示 autocomplete dropdown的内容

$( "#company" ).autocomplete({       
    source : availableTags ,
    appendTo: "#exportOrder"  //这里需要指定modal元素
});

```
## Jquery UI Custom tooltip

```javascript

var buildCustomTooltip = function() {
  $("i.statistic-tooltip").each(function() {
    $(this).tooltip({
      items: $(this),
      tooltipClass: "statistic-tooltip",
      position: {
        my: "left bottom",
        at: "right top"
      },
      content: function() {
        var total = $(this).attr("total");
        return "<ul class=\"list-group\">" +
                  "<li class=\"list-group-item\"><span class=\"badge\">" + total + "</span> Total of Executions: </li>" +
                "</ul>";
      }
    });
  });
  $("i.statistic-tooltip").hover(function(){
    $(this).tooltip('open');
  }, function(){
    $(this).tooltip('close');
  });
}

```


## Dropzone

```javascript

$("#id").dropzone({
	autoProcessQueue: false, //设置是否文件拖拽上去就执行上传
	maxFiles: 1, //设置一次上传的最大文件数
	...

	init: function() {
	  var uploadBtn = $("#uploadBtn");
	  uploadBtn.on("click", function() {
		  //执行文件上传
		  this.processQueue();
	  });
	  
	  //当文件数等于设置的maxFiles的事件
	  //file 指的是当前刚添加的文件
	  this.on("maxfilesexceeded", function(file) {
		if(this.files.length > 1) {
			//this.removeAllFiles();  //删除所有已上传的文件
			//this.addFile(file);     //删除当前文件
			this.removeFile(file);    //添加当前文件
		};
	  });
	  //添加文件的事件
	  this.on("addedfile", function(file) {

	  });
	  //文件上传成功的事件
	  this.on("success", function(file, resp) {

	  });


	}
});

```

## Jquery Unbind 事件

```javascript
("#id").unbind();
```

## Jquery DataTable  

[1.Multiple checkbox select](https://jsfiddle.net/gyrocode/abhbs4x8/)

```javascript

//Refresh sorting/filter information after dom change.
//https://datatables.net/reference/api/cell().invalidate()
table.column(3).invalidate('dom').draw(false);
table.row(3).invalidate('dom').draw(false);
table.cell(3, 3).invalidate('dom').draw(false);

//fetch selected rows number
table.rows('.selected').count();

table.row(3).data();

table.column(3).nodes();

//fetch visible rows
table.rows({page: "current"});  

```


## CodeMirror 获取实例

```javascript
//Get a reference to the CodeMirror editor
var editor = $('.CodeMirror')[0].CodeMirror;

//You can then use it as you wish
editor.setValue('lorem ipsum yada yada');
editor.replaceSelection('this is a test');

```


## Checkbox widget

```javascript
//判断是否选中， chrome 选中返回"checked"，未选中返回undefined
$("#Checkbox2").attr("checked") 
//设置radio选中
$("#Checkbox2").attr("checked", "checked")
$("#Checkbox2").prop("checked", "checked");
//触发onChanges事件
$("#Checkbox2").prop("checked", "checked").change()

```

## Radio widget

```javascript
//设置radio选中
$("#radio").prop("checked", "checked");
//设置radio不选中
$("#radio").prop("checked", false);
```

## Select widget

```javascript
//设置select是disable 但是此状态form提交的时候，该字段的值不会被提交
$("#select").prop("disable", false);

```



## Selec2 设置select控件只读
```javascript
//version 4.x
$("select#commandType").prop("disabled", true);
```


## Javascript对象或者数组转化为字符串

{% highlight javascript %}
//1. Javascript对象或者数组转化为字符串

/**
* value: Javascript对象或者数组
* replacer[optional]: 用来做格式化输出
* space[optional]：值可以是字符串或者数字，用来替换空白字符
*/
JSON.stringify(value[, replacer [, space]]);
e.g:
JSON.stringify(JSONObject, null , 2);

//2. 字符串转化为JSON对象

/**
* text: JSON字符串
* reviver[optional]: 可以操作转化的结果
*/

JSON.parse(text[, reviver])
e.g:
JSON.parse('{"p": 5}', (key, value) =>
  typeof value === 'number'
    ? value * 2 // return value * 2 for numbers
    : value     // return everything else unchanged
);


{% endhighlight %}

## How to add a HTMLEvent to Html element

{% highlight javascript %}
<script type="text/javascript">
	var ele = document.getElementById("input_1");
	/**
	*事件类型
	*事件被触发后执行的回调函数
	*useCapture https://developer.mozilla.org/zh-CN/docs/Web/API/EventTarget/addEventListener
	**/
	ele.addEventListener("input", function(){console.log(this.value)}, false);
	/**
	*创建一个InputEvent https://developer.mozilla.org/en-US/docs/Web/Events
	**/
	var inputEvent = new InputEvent("input");
	//分发事件 https://developer.mozilla.org/en-US/docs/Web/API/EventTarget/dispatchEvent
	ele.dispatchEvent(inputEvent);


	//触发built-in 事件 https://developer.mozilla.org/en-US/docs/Web/Guide/Events/Creating_and_triggering_events#Triggering_built-in_events
</script>
{% endhighlight %}

## Add indicator when ajax call

{% highlight javascript %}
$(document)
	  .ajaxStart(function () {
	    $loading.show();
	  })
	  .ajaxStop(function () {
	    $loading.hide();
	  });
{% endhighlight %}

## Html 中取得Iframe中的元素

{% highlight javascript%}
var f = document.getElementById("#FrameId");
f.contentDocument.getElementById("#ElementId").value;
{% endhighlight%}

## 阻止表单通过entry提交  

```javascript

$(document).on("keyup keypress", "form input", function(e) {
          if(e.which == 13) {
            e.preventDefault();
            return false;
          }
        });

```

