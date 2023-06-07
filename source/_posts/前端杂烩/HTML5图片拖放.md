---
title: HTML5图片拖放
date: 2023-06-07 15:58:40
tags:
categories: 
- 前端杂烩
---
## 实现HTML5中图片的拖放
```html
<!DOCTYPE html>
<html>
	<head>
		<meta charset="utf-8">
		<title>阿杰之家</title>
		<style type="text/css">
			#div1,
			#div2 {
				float: left;
				width: 500px;
				height: 350px;
				margin: 10px;
				padding: 10px;
				border: 1px solid #aaaaaa;
			}
		</style>
		<script>
			function allowDrop(ev) {
				// 6、默认地，无法将数据/元素放置到其他元素中。如果需要设置允许放置，我们必须阻止对元素的默认处理方式,这要通过调用 ondragover 事件的 event.preventDefault() 方法.
				ev.preventDefault();
			}

			function drag(ev) {
				// 3、dataTransfer.setData() 方法设置被拖数据的数据类型和值
				// 4、Text 是一个 DOMString 表示要添加到 drag object 的拖动数据的类型。值是可拖动元素的 id ("drag1")。
				ev.dataTransfer.setData("Text", ev.target.id);
			}

			function drop(ev) {
				// 8、调用 preventDefault() 来避免浏览器对数据的默认处理（drop 事件的默认行为是以链接形式打开）
				ev.preventDefault();
				// 9、通过 dataTransfer.getData("Text") 方法获得被拖的数据。该方法将返回在 setData() 方法中设置为相同类型的任何数据。被拖数据是被拖元素的 id ("drag1")
				var data = ev.dataTransfer.getData("Text");
				// 10、把被拖元素追加到放置元素（目标元素）中
				ev.target.appendChild(document.getElementById(data));
			}
		</script>
	</head>
	<body>
		<!-- 5、ondragover 事件规定在何处放置被拖动的数据。 -->
		<!-- 7、当放置被拖数据时，会发生 drop 事件 -->
		<div id="div1" ondrop="drop(event)" ondragover="allowDrop(event)">
			<!-- 1、为了使元素可拖动，把 draggable 属性设置为 true -->
			<!-- 2、ondragstart 属性调用了一个函数，drag(event)，它规定了被拖动的数据。 -->
			<img src="./img/logo.png" draggable="true" ondragstart="drag(event)" id="drag1" width="500" height="350"></div>
		<div id="div2" ondrop="drop(event)" ondragover="allowDrop(event)"></div>

	</body>
</html>
```
