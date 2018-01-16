---
title: Agora video screen share
date: 2018-01-16 10:54:39
tags: [agora, video, chrome]
categories: [使用说明, 备忘]
---

项目之前使用的是自己搭建的webrtc及信令服务器，但是由于不同的网络运营商信号同步有问题，不能区域有可能连通不了，需要穿墙，比较麻烦，配置还很多，没有区域分流功能，距离远视频质量也不好。所以决定找第三方的，暂时选定声网的服务。
[准备工作](https://docs.agora.io/cn/2.0.2/product/Video/Integration%20Guide/webrtc_guide_video#extensionid-get)
[API](https://docs.agora.io/cn/2.0.2/product/Video/API%20Reference/web_API_video?platform=Web)

<!-- more --> 

从 Agora 官方网站 [下载](https://docs.agora.io/cn/2.0/download) 最新版 Agora Web SDK 软件包，并获取其中的 .js 文件。
获取安装屏幕分享插件，得到插件ID，修改插件里面的json文件，修改match行的域名，替换为自己的正式和测试域名。
步骤：
* 创建 Client 对象
* 初始化 Client 对象
* 加入频道
* 创建本地流
* 设置本地流 Profile
* 初始化本地流
* 发布本地流
* 监听流事件并订阅远端流
* 播放本地/远端流

关于同时使用视频和屏幕分享的功能的解决方法

**同时创建两个client,屏幕分享不要跟视频使用同一个端口**

```js
    // 视频聊天功能
    function join() {
        var channel_key = null;
        // 创建 Client 对象
        client = AgoraRTC.createClient({ mode: 'interop' });
        // 初使化Client对象
        client.init(key.value, function () {
          // 加入特定的频道，实现视频联通
          client.join(channel_key, channel.value, null, function (uid) {
            // 可以设置是否以访客模式进入，只能看别人，false是访客模式
            if (document.getElementById("video").checked) {
              // 创建本地视频音频流，video与screen屏幕分享是互斥的
              localStream = AgoraRTC.createStream({ streamID: uid, audio: true, cameraId: camera, microphoneId: microphone, video: true, screen: false });
              // 设置本地流视频属性, 详细列表可查看, https://document.agora.io/cn/1.14/api/web.html#setvideoprofile
              localStream.setVideoProfile('720p_3');
              // 初始化本地流, 并同时申请本地媒体采集权限
              localStream.init(function () {
                // 将本地流在id为agora-remote的dom中播放
                localStream.play('agora_local');
                // 发布本地流以使其可以被远端接收到
                client.publish(localStream, function (err) {
                  console.log("Publish local stream error: " + err);
                });
                client.on('stream-published', function (evt) {
                  console.log("Publish local stream successfully");
                });
              }, function (err) {
                console.log("getUserMedia failed", err);
              });
            }
          }, function (err) {
            console.log("Join channel failed", err);
          });
        }, function (err) {
          console.log("AgoraRTC client init failed", err);
        });

        channelKey = "";

        client.on('error', function (err) {
          console.log("Got error msg:", err.reason);
          if (err.reason === 'DYNAMIC_KEY_TIMEOUT') {
            client.renewChannelKey(channelKey, function () {
              console.log("Renew channel key successfully");
            }, function (err) {
              console.log("Renew channel key failed: ", err);
            });
          }
        });
        // 监听流添加事件
        client.on('stream-added', function (evt) {
          var stream = evt.stream;
          console.log("New stream added: " + stream.getId());
          console.log("Subscribe ", stream);
          client.subscribe(stream, function (err) {
            console.log("Subscribe stream failed", err);
          });
        });
        // 监听当有人进入房间后显示加入者的视频流到本地
        client.on('stream-subscribed', function (evt) {
          var stream = evt.stream;
          console.log("Subscribe remote stream successfully: " + stream.getId());
          if ($('div#video #agora_remote' + stream.getId()).length === 0) {
            $('div#video').append('<div id="agora_remote' + stream.getId() + '" style="float:left; width:810px;height:607px;display:inline-block;"></div>');
          }
          stream.play('agora_remote' + stream.getId());
        });
        // 监听流移除事件
        client.on('stream-removed', function (evt) {
          var stream = evt.stream;
          stream.stop();
          $('#agora_remote' + stream.getId()).remove();
          console.log("Remote stream is removed " + stream.getId());
        });
        // 监听离开房间事件
        client.on('peer-leave', function (evt) {
          var stream = evt.stream;
          if (stream) {
            stream.stop();
            $('#agora_remote' + stream.getId()).remove();
            console.log(evt.uid + " leaved from this channel");
          }
        });
    }
```

```js
    // 屏幕分享功能
    function share() {
        var channel_key = null;
        clientShare = AgoraRTC.createClient({ mode: 'interop' });
        clientShare.init(key.value, function () {
          clientShare.join(channel_key, channel.value + '01', null, function (uid) {
            // 是否允许分享屏幕
            if (document.getElementById("screen").checked) {
              shareStream = AgoraRTC.createStream({
                streamID: uid,
                audio: true, cameraId: camera, microphoneId: microphone, // 监听麦克风，目的是用于直播时不分享摄像头分享本地视频采集视频的音频，如果仅分享屏幕可以不要这行
                video: false,
                screen: true,
                extensionId: 'minllpmhdgpndnkomcoccfekfegnlikg'
              });
              shareStream.setVideoProfile('720p_3');
              shareStream.init(function () {
                shareStream.play('agora_local');
                clientShare.publish(shareStream, function (err) {
                  console.log("Publish local stream error: " + err);
                });
                clientShare.on('stream-published', function (evt) {
                  console.log("Publish local stream successfully");
                });
              }, function (err) {
                console.log("getUserMedia failed", err);
              });
            }
          }, function (err) {
            console.log("Join channel failed", err);
          });
        }, function (err) {
          console.log("AgoraRTC clientShare init failed", err);
        });

        channelKey = "";

        clientShare.on('error', function (err) {
          console.log("Got error msg:", err.reason);
          if (err.reason === 'DYNAMIC_KEY_TIMEOUT') {
            clientShare.renewChannelKey(channelKey, function () {
              console.log("Renew channel key successfully");
            }, function (err) {
              console.log("Renew channel key failed: ", err);
            });
          }
        });

        clientShare.on('stream-added', function (evt) {
          var stream = evt.stream;
          console.log("New stream added: " + stream.getId());
          console.log("Subscribe ", stream);
          clientShare.subscribe(stream, function (err) {
            console.log("Subscribe stream failed", err);
          });
        });

        clientShare.on('stream-subscribed', function (evt) {
          var stream = evt.stream;
          console.log("Subscribe remote stream successfully: " + stream.getId());
          if ($('div#video #agora_remote' + stream.getId()).length === 0) {
            $('div#video').append('<div id="agora_remote' + stream.getId() + '" style="float:left; width:810px;height:607px;display:inline-block;"></div>');
          }
          stream.play('agora_remote' + stream.getId());
        });
    }
```

以上仅是个人浅见，记录下来，方便备忘，随时补充，更改。[demo](//freeser.github.io/example/agora/demo)