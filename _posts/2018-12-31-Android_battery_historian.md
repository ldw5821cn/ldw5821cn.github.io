---
layout: post
title: Android 电量分析工具
category: android
tags: [android]
keywords: Android
excerpt: Android 电量分析工具
---

## Android 电量分析工具（battery-historian）

#### 下载安装
1. 安装 `docker`

 	mac：
 	  
 		brew cask install docker
 		
 	other:
 	
 		https://www.docker.com/community-edition
2. 开始运行 `docker`

  ![](https://i.stack.imgur.com/KYhp8m.png)
  ![](https://i.stack.imgur.com/yTOjRm.png)
  ​	

  

  ```java
    //测试 docker 是否运行正常
    $ docker run hello-world
    Unable to find image 'hello-world:latest' locally
    latest: Pulling from library/hello-world
    78445dd45222: Pull complete
    Digest: sha256:c5515758d4c5e1e838e9cd307f6c6a0d620b5e07e6f927b07d05f6d12a1ac8d7
    Status: Downloaded newer image for hello-world:latest
  
  
    Hello from Docker!
    This message shows that your installation appears to be working correctly.
  
    To generate this message, Docker took the following steps:
     1. The Docker client contacted the Docker daemon.
     2. The Docker daemon pulled the "hello-world" image from the Docker Hub.
     3. The Docker daemon created a new container from that image which runs the
        executable that produces the output you are currently reading.
     4. The Docker daemon streamed that output to the Docker client, which sent it
        to your terminal.
  
    To try something more ambitious, you can run an Ubuntu container with:
     $ docker run -it ubuntu bash
  
    Share images, automate workflows, and more with a free Docker ID:
     https://cloud.docker.com/
  
    For more examples and ideas, visit:
     https://docs.docker.com/engine/userguide/
  
    $ docker version
    Client:
     Version:      17.03.1-ce
     API version:  1.27
     Go version:   go1.7.5
     Git commit:   c6d412e
     Built:        Tue Mar 28 00:40:02 2017
     OS/Arch:      darwin/amd64
  
    Server:
     Version:      17.03.1-ce
     API version:  1.27 (minimum version 1.12)
     Go version:   go1.7.5
     Git commit:   c6d412e
     Built:        Fri Mar 24 00:00:50 2017
     OS/Arch:      linux/amd64
     Experimental: true
  ```

3. 安装 `Battery Historian`

   ```javascript
   //官网的下载命令（如果有报错，使用下面的，port_number 自己指定一个即可）
   
   ​	docker --run -p port_number:9999 gcr.io/android-battery-historian:2.1 --port 9999
   ​	
   ​	$docker run -p 5665:9999 gcr.io/android-battery-historian/stable:3.0 --port 9999
   ​	Unable to find image 'gcr.io/android-battery-historian/stable:3.0' locally
   ​	3.0: Pulling from android-battery-historian/stable
   ​	c62795f78da9: Pull complete
   ```

4. 使用 `Battery Historian`

  1). 连接你的手机到电脑
  2). 从`terminal` 窗口，杀掉 `ADB`

  	adb kill-server
  3). 重启 `ADB` 连接你的手机

  	`adb devices`
  	
  	List of devices attached
  	* daemon not running; starting now at tcp:5037
  	* daemon started successfully
  	015bb4e85da8d282        device
  4).重置电池数据采集

     `adb shell dumpsys batterystats --reset`
  ​	
  	设备始终在后台收集batterystats和其他调试信息。
  	重置会删除旧的电池收集数据。如果不重置，输出将是巨大的。
  5).断开设备与计算机的连接，以便仅从设备的电池中吸取电流。
  6).使用应用执行你的操作; 例如，断开与WiFi的连接并将数据发送到云端
  7).重新连接你的手机，确认连接成功
  ​	`adb devices`
  8).`dump` 所有的电池数据,(`path`为你指定的存储电脑上的路径，不指定会存储在主目录)
  ​	`adb shell dumpsys batterystats > [path/]batterystats.txt`
  9).从原始数据创建报告

  	For devices running Android 7.0 and higher:
  		adb bugreport > [path/]bugreport.zip
  	For devices running Android 6.0 and lower:
  		adb bugreport > [path/]bugreport.txt
  		
  	注 7.0以上的命令行，执行后没有正确的结果，请使用：
  		adb bugreport [path/]bugreport.zip
  10).使用浏览器打开 `Battery Historian` 网站
  ​	点击 Browse 选中你的文件，点击Submit

  ![](https://ldw5821cn.github.io/assets/img/Android_battery/battery.jpg)

  ![](https://ldw5821cn.github.io/assets/img/Android_battery/001.png)	

参考内容：

1.[battery-historian](https://developer.android.com/studio/profile/battery-historian)

2.[install battery-historian](https://stackoverflow.com/questions/40523307/brew-install-docker-does-not-include-docker-engine)

