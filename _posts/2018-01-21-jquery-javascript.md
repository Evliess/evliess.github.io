---
layout: index
title: "Javascript常用的方法"
category: javascript
date: 2018-01-21 15:17:55
---

#### Javascript对象或者数组转化为字符串

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

#### How to add a HTMLEvent to Html element

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

#### Add indicator when ajax call

{% highlight javascript %}
$(document)
	  .ajaxStart(function () {
	    $loading.show();
	  })
	  .ajaxStop(function () {
	    $loading.hide();
	  });
{% endhighlight %}

#### Html 中取得Iframe中的元素

{% highlight javascript%}
var f = document.getElementById("#FrameId");
f.contentDocument.getElementById("#ElementId").value;
{% endhighlight%}

