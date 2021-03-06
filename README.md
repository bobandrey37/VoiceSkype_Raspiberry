# VoiceSkype_Raspiberry
树莓派端-云书鸿音-助力于盲人与亲人朋友联系的一个智能音箱

考虑到盲人在使用微信等聊天软件时的不便，也进一步的加大了盲人和亲人好友之间的沟通代价。

于是想到了制作一款这样的软件。

缺陷：原名：**云书微音**。由于微信网页版的封绝，已经无法通过网页版的API和其封装访问微信。如果通过hook等形式做的话，成本比较大，也不符合嵌入式设备的音箱的要求。

于是换成使用Skype。

---

### 硬件需求

 1. 程序需要在MacOS或者Linux等基于Unix的操作系统上使用
 Mac成本比较大，推荐使用树莓派+普通音箱+普通免驱麦克风
 或者使用搭载有Ubuntu的电脑也可以
 
 树莓派1GB版本也可以很好的运行
 
 2. 本版本使用的硬件信息：
    树莓派4B 2GB *1  
    32GB flash闪存卡 *1  
    5v/9v 2A/1A 充电头 *1  
    typeC充电器 *1  
    树莓派九层亚克力板框架+风扇*1  
    麦克风阵列（86元）*1  
    索爱音箱（20元）*1  
    整套下来450元  

### 操作系统限制

由于使用的snowboy的热词检测和唤醒，仅支持Unix系统的平台，所以，windows上无法部署。


### 实现简述

基于python现成的pyaudio库实现录音wav文件

使用ffmpeg实现wav到pcm文件的转换

利用百度、腾讯的接口实现语音识别和语音合成（现有版本是混用，后面可以修正为全用腾讯）

利用snowboy热词检测功能实现唤醒(hotword detect)

利用webrtc项目的vad(具有python的封装库)，实现语音实时检测，检测一段语音的停止。

利用腾讯的智能闲聊实现闲聊模式

使用正则匹配实现命令的识别

使用高德地图的天气服务和IP定位，实现城市定位和天气预报

使用VLC的python封装实现在线音乐播放

使用https://github.com/Binaryify/NeteaseCloudMusicApi/issues提供的网易云音乐api，通过nodejs直接在树莓派上搭建本地API服务器。

以上是简略的一些介绍。

### 安装

1. 安装python3和pip

2. ```sudo bash install.sh```

3. 检查设备是否已经连接音箱和麦克风(检查麦克风是否可用)

   ```bash
   # bash
   rec t.wav
   ```

​       如果发现错误时，说明没有连接麦克风或者声卡不可用

​       参考snowboy http://docs.kitt.ai/snowboy/#set-up-audio

4. 由于snowboy的编译仓库下载速度比较慢，故独立出

   ```
   cd 
   git clone https://github.com/Kitt-AI/snowboy.git
   cd swig/Python3
   make
   ```

5. 将编译得到的

   _snowboydetect.so
   snowboydecoder.py
   snowboydetect.py

   复制到项目的snowboy目录下

6. 树莓派需要另外配置声卡，Ubuntu不需要

   ```
   sudo nano ~/.asoundrc
   ```

      ```
 type asym
   playback.pcm {
     type plug
     slave.pcm "hw:0,0"
   }
   capture.pcm {
     type plug
     slave.pcm "hw:1,0"
   }
}
      ```

将上述内容复制到文件中

7. 启动网易云音乐API服务器

      ```
cd
cd NeteaseCloudMusicApi
sudo nohup node app.js &
      ```



(仍然在完善内容中，暂时不全)

### 实现的功能

实现的功能有：

1. 语音对话 

2. vod热词唤醒：默认词为小瓜小瓜，留有拓展性，可以更改 
3. 询问天气和明天天气，可以是自动定位，也可以是指定地址 
4. 询问日期、时间
5.  进入闲聊模式：腾讯的智能闲聊功能
6.  登录skype账号
7.  自动随时接受好友的消息，并通过语音对用户进行提示 
8. 通过语音进行发送skype消息 
9. 通过语音进行播放音乐 
10. 播放过程随时可以停止、暂停、增加音量、减小音量 
11. 比较友好地提示和比较全的错误处理



### 使用的命令

1. 唤醒：小瓜小瓜
2. 天气：今天天气怎么样？｜明天天气怎么样？ ｜今天**[地名]**天气怎么样？｜明天**[地名]**天气怎么样？
3. 时间和日期：现在是几点钟？ ｜ 现在是什么时间？｜今天是几号？
4. 进入闲聊模式：进入闲聊模式
5. 退出闲聊模式：退出闲聊模式
6. 播放音乐：播放音乐｜播放歌曲（得到响应后告知歌曲名称）
7. 减小音量｜小声点
8. 增大音量｜大声点
9. 暂停播放｜继续播放｜停止播放



### 注意事项：

snowboy需要另外进行手动编译

同时，不建议make install 

