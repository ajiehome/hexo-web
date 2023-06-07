---
title: JavaScript获取视频流且回显
date: 2023-06-07 15:58:40
tags:
categories: 
- Java基础
---
# JavaScript获取视频流且回显

```HTML
<!DOCTYPE html>
<html>
	<head>
		<style>

		</style>
	</head>
	<body>
		<video id="video" width="640" height="480" autoplay="autoplay"></video>
		<br/>
		<button id="snap">截取图像</button><button id="start">开始录制</button><button id="stop">停止录制</button>
		<br/>
		<canvas id="canvas" width="640" height="480"></canvas>
	</body>
	<script type="text/javascript">
		let videoView = document.getElementById("video");
		let buttonView = document.getElementById("snap");
		let startButton = document.getElementById("start");
		let stopButton = document.getElementById("stop");
		let canvasView = document.getElementById("canvas");

		navigator.mediaDevices.getDisplayMedia({
			video: {
				width: 640,
				height: 480
			},
			audio: true
		}).then(stream => {
			console.log("摄像头打开成功！！！", stream);
			//以ID为基准,video控件的ID是什么此处的video就是什么
			video.srcObject = stream;

			// 创建下载对象
			let mediaRecorder = new MediaRecorder(stream, {
				audioBitsPerSecond: 128000, // 音频码率
				videoBitsPerSecond: 100000, // 视频码率
				mimeType: 'video/webm;codecs=h264' // 编码格式
			});
			startButton.onclick = (e => {
				console.log("触发下载按钮！！！", e)
				mediaRecorder.start(5000);
			});
			stopButton.onclick = (e => {
				console.log("触发结束按钮！！！", e)
				mediaRecorder.stop();
			});
			mediaRecorder.ondataavailable = (e => {
				console.log("开始下载")
			    // 下载视频
			    var blob = new Blob([e.data], { 'type' : 'video/mp4' })
			    let a = document.createElement('a')
			    a.href = URL.createObjectURL(blob)
			    a.download = `test.mp4`
			    a.click()
			});

			//监听流录入失败
			video.onerror = (err => {
				console.log("加载错误！！！", err);
			});
			//监听流录入成功
			video.onloadeddata = (e => {
				console.log("加载成功！！！", e);
			});
		}).catch(stream => {
			console.log("摄像头打开失败！！！", stream);
		});
		//给button按钮设置一个点击事件监听
		buttonView.onclick = function() {
			//获取画布的2d上下文，然后把视频内的一帧绘制到画布上
			canvasView.getContext("2d").drawImage(videoView, 0, 0, 640, 480)
		}
	</script>
</html>

```

