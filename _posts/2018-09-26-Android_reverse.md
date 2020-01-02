---
layout: post
title: Android 应用逆向工具介绍(一)
category: android
tags: [android]
keywords: Android
---


## Android 应用逆向工具介绍

### 1.APK Analyzer
需要 `Android Studio 2.2`以上版本

**1.1 Build->Analyze APK 打开APK**

![]({{site.baseurl}}/assets/images/2018/Android_reverse/file.png)

**1.2 内容说明**

- classes.dex: 源码编译生成的字节码文件
- res: 资源文件
- resources.arsc:资源索引表
- AndroidManifest: Android 配置清单文件
- META-INF:apk的签名信息

![]({{site.baseurl}}/assets/img/Android_battery/content.png)

**1.3 查看AndroidManifest文件**


![]({{site.baseurl}}/assets/img/Android_battery/build.png)

**1.4：[更多介绍及使用方式](https://developer.android.com/studio/build/apk-analyzer)**

### 2.Dex2Jar
**2.1 介绍 `dex2jar`**

将`dex`字节文件反编译为 `jar`包

**2.2 安装 `dex2jar`**

官方下载：[下载链接](https://github.com/pxb1988/dex2jar)

Mac 可以使用 `Homebrew` 安装

	Install the App
	Press Command+Space and type Terminal and press enter/return key.
	Run in Terminal app:
	ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)" < /dev/null 2> /dev/null
	and press enter/return key. 
	If the screen prompts you to enter a password, please enter your Mac's user password to continue. When you type the password, it won't be displayed on screen, but the system would accept it. So just type your password and press ENTER/RETURN key. Then wait for the command to finish.
	Run:
	brew install dex2jar
	Done! You can now use dex2jar.

**2.3 使用dex2jar**

![]({{site.baseurl}}/assets/img/Android_battery/d2j.jpg)

	 ✘XXXXX ~  d2j-dex2jar --help
	d2j-dex2jar -- convert dex to jar
	usage: d2j-dex2jar [options] <file0> [file1 ... fileN]
	options:
	 -d,--debug-info              translate debug info
	 -e,--exception-file <file>   detail exception file, default is $current_dir/[fi
	                              le-name]-error.zip
	 -f,--force                   force overwrite
	 -h,--help                    Print this help message
	 -n,--not-handle-exception    not handle any exception throwed by dex2jar
	 -nc,--no-code
	 -o,--output <out-jar-file>   output .jar file, default is $current_dir/[file-na
	                              me]-dex2jar.jar
	 -os,--optmize-synchronized   optmize-synchronized
	 -p,--print-ir                print ir to Syste.out
	 -r,--reuse-reg               reuse regiter while generate java .class file
	 -s                           same with --topological-sort/-ts
	 -ts,--topological-sort       sort block by topological, that will generate more
	                               readable code, default enabled
	version: reader-2.0, translator-2.0, ir-2.0

`dj2-dex2jar xxxxx.dex` 在当前目录生成`xxxx.jar`

![]({{site.baseurl}}/assets/img/Android_battery/dex2jar.jpg)

### 3.JD-GUI 
**3.1 介绍 JD-GUI**

查看`APK`中`classes.dex`转化成出的`jar`文件

**3.2 安装 JD-GUI**

官网：[下载链接](http://jd.benow.ca/)

Mac 可以使用 `Homebrew` 安装

	Install the App
	Press Command+Space and type Terminal and press enter/return key.
	Run in Terminal app:
	ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)" < /dev/null 2> /dev/null ; brew install caskroom/cask/brew-cask 2> /dev/null
	and press enter/return key. 
	If the screen prompts you to enter a password, please enter your Mac's user password to continue. When you type the password, it won't be displayed on screen, but the system would accept it. So just type your password and press ENTER/RETURN key. Then wait for the command to finish.
	Run:
	brew cask install jd-gui
**3.3 使用 JD-GUI**

双击

![]({{site.baseurl}}/assets/img/Android_battery/jd.jpg)

打开界面,查看源码信息


![]({{site.baseurl}}/assets/img/Android_battery/jdui.jpg)

### 4.JAD
**4.1 介绍 JAD**

可以将多种格式文件`（.apk, .dex, .jar or .class）`反编译为 `java `文件。

**4.2 安装 JAD**

下载：[下载链接](http://www.javadecompilers.com/jad)

Mac 可以使用 `Homebrew` 安装

	Launch Terminal by pressing command+space, type terminal and hit Enter key.
	Run
	ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)" < /dev/null 2> /dev/null
	Install
	brew install jadx

**4.2 使用 JAD**	

	jadx - dex to java decompiler, version: 0.7.1
	
	usage: jadx [options] <input file> (.apk, .dex, .jar or .class)
	options:
	  -d, --output-dir      - output directory
	  -ds, --output-dir-src - output directory for sources
	  -dr, --output-dir-res - output directory for resources
	  -r, --no-res          - do not decode resources
	  -s, --no-src          - do not decompile source code
	  -e, --export-gradle   - save as android gradle project
	  -j, --threads-count   - processing threads count
	  --show-bad-code       - show inconsistent code (incorrectly decompiled)
	  --no-imports          - disable use of imports, always write entire package name
	  --no-replace-consts   - don't replace constant value with matching constant field
	  --escape-unicode      - escape non latin characters in strings (with \u)
	  --deobf               - activate deobfuscation
	  --deobf-min           - min length of name
	  --deobf-max           - max length of name
	  --deobf-rewrite-cfg   - force to save deobfuscation map
	  --deobf-use-sourcename- use source file name as class name alias
	  --cfg                 - save methods control flow graph to dot file
	  --raw-cfg             - save methods control flow graph (use raw instructions)
	  -f, --fallback        - make simple dump (using goto instead of 'if', 'for', etc)
	  -v, --verbose         - verbose output
	  --version             - print jadx version
	  -h, --help            - print this help
	Example:
	  jadx -d out classes.dex //把dex文件编译为 java文件。

### 5.AndroidKiller  
**5.1 介绍 AndroidKiller**

Android killer 是一款可视化的安卓应用逆向工具，集Apk反编译、Apk打包、Apk签名，编码互转，ADB通信（应用安装-卸载-运行-设备文件管理）等特色功能于一身，支持logcat日志输出，语法高亮，基于关键字（支持单行代码或多行代码段）项目内搜索，可自定义外部工具；吸收融汇多种工具功能与特点，打造一站式逆向工具操作体验，大大简化了安卓应用/游戏修改过程中各类繁琐工作

AndroidKiller：[下载链接](https://www.52pojie.cn/thread-421203-1-1.html)

**注：**
AndroidKiller 目前仅有 `Windows` 平台下载使用。

### 6.ApkTool 
**6.1 介绍 ApkTool**

Android apktool是一个用来处理APK文件的工具,可以对APK进行反编译生成程序的源代码和图片、XML配置、语言资源等文件。

**6.2 安装 ApkTool**

下载：[下载链接](https://ibotpeaches.github.io/Apktool/)

Mac 可以使用 `Homebrew` 安装

	Install the App
	Press Command+Space and type Terminal and press enter/return key.
	Run in Terminal app:
	ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)" < /dev/null 2> /dev/null
	and press enter/return key. 
	If the screen prompts you to enter a password, please enter your Mac's user password to continue. When you type the password, it won't be displayed on screen, but the system would accept it. So just type your password and press ENTER/RETURN key. Then wait for the command to finish.
	Run:
	brew install apktool
	Done! You can now use apktool.

**6.3 使用 ApkTool**
	
	//反编译 apk 到 当前目录下同名目录
	$ apktool d testapp.apk
	
	//编译 bar 目录 输出新的apk
	$ apktool b bar -o new_bar.apk

ApkTool：[更多使用方式](https://ibotpeaches.github.io/Apktool/documentation/)


### 7.Classyshark
**7.1 介绍 Classyshark**

ClassyShark是Google发布的一款可以查看Android可执行文件的浏览工具，支持.dex, .aar, .so, .apk, .jar, .class, .xml 等文件格式。

**7.2 安装 Classyshark**
下载：[下载链接](https://github.com/google/android-classyshark/releases)

**7.3 使用 Classyshark**

	java -jar ClassyShark.jar

Android_reverse![]({{site.baseurl}}/assets/img/Android_battery/Android_reverse/shark.jpg)

拖拽 `apk` 文件到窗口

![]({{site.baseurl}}/assets/img/Android_battery/shark2.jpg)

**注：**
该工具在方法的细节上以 {...} 表示。

![]({{site.baseurl}}/assets/img/Android_battery/mc.png)
