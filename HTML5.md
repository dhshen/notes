# HTML5



## history.pushState/history.replaceState

执行pushState()方法后，新的状态信息就会被加入历史状态栈，而浏览器‘地址栏会变成新的相对URL，但是，浏览器并不会真的向服务器发送请求！

按下'后退'按钮，会触发window对象的popstate事件，popstate事件的对象有一个state属性，这个属性就包含着当初以第一个参数传递给pushState()的状态对象

```
EventUtil.addHandler(window,'popstate',function(){
  	var state = event.state;
});
```

点击后退按钮返回浏览器加载的第一个页面时，event.state值为null.

以下是DEMO:

```
<!DOCTYPE html>
<html lang="en">
<head>
	<meta charset="UTF-8">
	<title>History</title>
</head>
<body>
	<div>页面初始加载的状态</div>
	<a id="btn" href="javascript:void(0)">click</a>
<script>
	if(!window.history.pushState){
		alert('当前浏览器不支持pushState');
	}

	var btn = document.getElementById('btn');
	btn.onclick = function(){
		window.history.pushState({urlname:'hello'},'','test.html');
		console.log('url change');
		btn.innerHTML = 'hello';
	}

	var EventUtil = {

		addHandler: function(element,type,handler){
			if(element.addEventListenser){
				element.addEventListenser(type,handler,false);
			}else if(element.attachEvent){
				element.attachEvent('on'+type,handler);
			}else{
				element["on"+type] = handler;
			}
		}


	};

	//浏览器在后退时并不是去改变任何页面的内容，因此页面的改变需要通过代码来实现
	EventUtil.addHandler(window,'popstate',function(event){
		var state = event.state;
		if(state){
			console.log('state:',state,'根据state的值来重置浏览器中的数据');
			btn.innerHTML = state.urlname;
		}else{
			console.log('state 为null，此为浏览器加载的第一个页面');
			btn.innerHTML = 'first page'
		}
		
	});

</script>
</body>
</html>
```

