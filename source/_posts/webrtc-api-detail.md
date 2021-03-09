---
title: "webrtc API 详解"
date: 2021-03-7
tags: ["webrtc", "Javacript"]
categories: ["Javacript"]
top_image: /images/webrtc.jpeg
---

# 获取音视频

```js
navigator.mediaDevices.getUserMedia(constraints)
```
<!--more-->

# 获取屏幕数据

```js
navigator.mediaDevices.getDisplayMedia(constraints)
```

# 获取音视频设备

```javascript
navigator.mediaDevices.enumerateDevices()
```



## MediaDevicesInfo

|   S属性    |                      说明                      |
| :--------: | :--------------------------------------------: |
| `deviceID` |                     设备ID                     |
|  `label`   |                   设备的名称                   |
|   `kind`   |                    设备类型                    |
| `groupID`  | 两个设备等 `groupID`相同，说明是同一个物理设备 |

# 音视频API

|    名称    |                            约束值                            |
| :--------: | :----------------------------------------------------------: |
|  `video`   |                    [视频约束](#视频约束)                     |
|  `audio`   |                    [音频约束](#音频约束)                     |
| `deviceId` | 指定设备ID，可以通过[navigator.mediaDevices.enumerateDevices](#获取音视频设备) API 获取 |

## 视频约束

```js
let constraints = {
  video:{
      	width: 640, // 宽
      	height: 480, 	// 高
      	aspectRatio: 1.3, // 视频比例，一般不设置
      	frameRate: 30, // 帧率
      	facingMode: 'environment', // user (前置摄像头) environment (后置摄像头) left （前置左侧摄像头） right （前置右侧摄像头）
    },
  	audio: false
}
```



## 音频约束

```js
let constraints = {
    video:true,
    audio: {
    	noiseSuppressuib: true, // 降噪
      	echoCancellation: true, // 回音消除 
    }
}
```

> 约束变更之后需要重新调用 `getUserMedia` 



# MediaStram

媒体流

## 方法

|        方法        |     说明     |
| :----------------: | :----------: |
|    `addTrack()`    | 添加轨道信息 |
|  `removeTrack()`   |   移除轨道   |
| `getVideoTracks()` | 获取视频轨道 |
| `getAudioTracks()` | 获取音频轨道 |

## 事件

|      事件       |    说明    |
| :-------------: | :--------: |
|  `onaddtrack`   |  添加轨道  |
| `onremovetrack` |  移除轨道  |
|    `onended`    | 媒体流结束 |



# MediaRecorder

媒体录制

```js
// stream 媒体流，可以从getUserMedia、<video>、<audio>或者<canvas>获取
// 其他配置选项
let mediaRecorder = new MediaRecorder(stream, options)
```

**options 配置项**

|         选项         | 说明                                                         |
| :------------------: | ------------------------------------------------------------ |
|      `mimeType`      | `video/webm`<br />`audio/webm`<br />`video/mp4;codecs=vp8`<br />`video/webm;codecs=h264`<br />`audio/mp3;codecs=opus` |
| `audioBitsPerSecond` | 音频码率                                                     |
| `videoBitsPerSecond` | 视频码率                                                     |
|   `bitsPerSecond`    | 整体码率                                                     |

**API**

| api                               | 说明                                                        |
| --------------------------------- | ----------------------------------------------------------- |
| `MediaRecorder.start(timeslice)`  | 开始录制媒体，`timeslice`选填，如果设置则按时间切片存储数据 |
| `MediaRecorder.stop()`            | 停止录制，此时触发包括最终Blob数据的`dataavailable`事件     |
| `MediaRecorder.pause()`           | 暂停录制                                                    |
| `MediaRecorder.resume()`          | 恢复录制                                                    |
| `MediaRecorder.isTypeSupported()` | 检查文件格式是否支持录制                                    |

**事件**

| 事件                            | 说明                                                         |
| ------------------------------- | ------------------------------------------------------------ |
| `MediaRecorder.ondataavailable` | 每次记录一定时间的数据时（如果没有指定时间片，则记录完整个数据触发）会定期触发 |
| `MediaRecorder.onerror`         | 录制发生错误时触发，并停止录制                               |

**示例**

```js
let buffer = [];
let options = { mimeType: "video/webm;codecs=vp8" };

if (MediaRecorder.isTypeSupported(options.mimeType)) {
  let mediaRecorder = new MediaRecorder(stream, options);

  mediaRecorder.ondataavailable = e => {
    if (e.data && e.data.size > 0) {
      buffer.push(e.data);
    }
  };

  mediaRecorder.start(10);
}

// 播放录制的视频
const onPlay = () => {
		let blob = new Blob(buffer, {type: "video/webm"})
    let player = document.getElementById("video#player")
    player.src = window.URL.createObjectURL(blob)
  	player.play()
}

// 下载录制的视频
const onDownload = () => {
  	let blob = new Blob(buffer, {type: "video/webm"})
    let url = window.URL.createObjectURL(blob)
    
    let a = document.createElement("a")
    a.href = url
  	a.style.display = "none"
  	a.download = "test.webm"
  	a.click()
}

```



# RTCPeerConnection

## 媒体协商

![image-20210307174514581](/images/webrtc.png)

|          方法          |      说明       |
| :--------------------: | :-------------: |
|     `createOffer`      |  创建offer信息  |
|     `createAnswer`     | 创建answer信息  |
| `setLocalDescription`  | 保存本地SDP信息 |
| `setRemoteDescription` | 保存远程SDP信息 |



## Stream/Track

| 方法                      | 说明                                                         |
| ------------------------- | ------------------------------------------------------------ |
| `addTrack(track, stream)` | 添加媒体轨道信息，`track`媒体轨，`stream`指定 track 所在的stream |
| `removeTrack(rtpSender)`  | 移除媒体轨道信息                                             |



## 传输相关

## 统计相关

## 事件

| 事件                  | 说明             |
| --------------------- | ---------------- |
| `onnegotiationneeded` | 需要协商时触发   |
| `onicecandidate`      | 收到候选者时触发 |



# 端到端连接流程

![image-20210307174514581](/images/webrtc-connect.png)

# 实战应用

## 从视频中截取图片

```js
// HTML
<video id="video" src="" autoplay playsinline></video>
<canvas id="snapshot"></canvas>

// Javascript
let videoPlay = document.getElementById("video");
navigator.mediaDevices.getDisplayMedia().then(res => {
    videoPlay.srcObject = res;
});

let snapshot = document.getElementById("snapshot");
snapshot.getContext("2d").drawImage(videoPlay, 0, 0, snapshot.width, snapshot.height);

```

