---
layout: post
title: Android Jetpack --Behavior（行为）
category: Jetpack
tags: [android, Jetpack]
keywords: Jetpack
excerpt: Android Jetpack --Behavior
---



##Android Jetpack --Behavior（行为）

[TOC]

### 一、下载管理器

#### 1.简介

DownloadManager是android2.3以后，系统下载的方法，是处理长期运行的HTTP下载的系统服务。客户端可以请求的URI被下载到一个特定的目标文件。客户端将会在后台与http交互进行下载，或者在下载失败，或者连接改变，重新启动系统后重新下载。还可以进入系统的下载管理界面查看进度。DownloadManger有两个内部类，Request 和Query。Request类可设置下载的一些属性。Query类可查询当前下载的进度，下载地址，文件存放目录等数据。

请求通过此API下载的应用程序应注册ACTION_NOTIFICATION_CLICKED的广播接收器，以便在用户单击通知中的正在运行的下载时或从下载UI中进行适当处理。 请注意，应用程序必须具有Manifest.permission.INTERNET权限才能使用此类。 必须使用带有参数DownloadManager.class或Context.getSystemService（String）且带有Context.DOWNLOAD_SERVICE参数的Context.getSystemService（Class）获取此类的实例。

#### 2.如何使用

##### 2.1.DownloadManager

- 所需权限

```xml
<uses-permission android:name="android.permission.INTERNET" />;
<uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE"/>;
```

- 获取对象，开始下载

```java
DownloadManager downloadManager = (DownloadManager)getSystemService(DOWNLOAD_SERVICE);
DownloadManager.Request request = new DownloadManager.Request(Uri.parse(apkUrl));
long id = downloadManager.enqueue(request);
```

- 取消下载

```java
downloadManager.remove(REFERENCE_1, REFERENCE_2, REFERENCE_3);  
```

##### 2.2.Request

- 指定下载位置及文件名称

```java
     /**
         * 方法1: 
         * 目录: Android -> data -> com.app -> files -> Download -> dxtj.apk
         * 这个文件是你的应用所专用的,软件卸载后，下载的文件将随着卸载全部被删除
         */
request.setDestinationInExternalFilesDir( this , Environment.DIRECTORY_DOWNLOADS ,  "dxtj.apk" );  

        /**
         * 方法2:
         * 下载的文件存放地址  SD卡 download文件夹，dxtj.apk
         * 软件卸载后，下载的文件会保留
         */
        //在SD卡上创建一个文件夹
        request.setDestinationInExternalPublicDir(  "/epmyg/"  , "dxtj.apk" ) ;  


        /**
         * 方法3:
         * 如果下载的文件希望被其他的应用共享
         * 特别是那些你下载下来希望被Media Scanner扫描到的文件（比如音乐文件）
         */
        request.setDestinationInExternalPublicDir( Environment.DIRECTORY_MUSIC,  "告白气球.mp3" );  

        /**
         * 方法4
         * 文件将存放在外部存储的确实download文件内，如果无此文件夹，创建之，如果有，下面将返回false。
         * 系统有个下载文件夹，比如小米手机系统下载文件夹  SD卡--> Download文件夹
         */
        //创建目录
        Environment.getExternalStoragePublicDirectory(Environment.DIRECTORY_DOWNLOADS).mkdir() ; 

        //设置文件存放路径
        request.setDestinationInExternalPublicDir(  Environment.DIRECTORY_DOWNLOADS  , "dxtj.apk" ) ;
    }
```

- 指定下载的网络类型

```java
//指定在WIFI状态下，执行下载操作。
request.setAllowedNetworkTypes(DownloadManager.Request.NETWORK_WIFI);
//指定在MOBILE状态下，执行下载操作
request.setAllowedNetworkTypes(DownloadManager.Request.NETWORK_MOBILE);
//是否允许漫游状态下，执行下载操作
request.setAllowedOverRoaming(boolean);
//是否允许“计量式的网络连接”执行下载操作
request.setAllowedOverMetered(boolean); //默认是允许的。
```

- 定制Notification样式

```java
VISIBILTY_HIDDEN: Notification:将不会显示，如果设置该属性的话，必须要添加权限 
Android.permission.DOWNLOAD_WITHOUT_NOTIFICATION. 
VISIBILITY_VISIBLE： Notification显示，但是只是在下载任务执行的过程中显示，下载完成自动消失。（默认值） 
VISIBILITY_VISIBLE_NOTIFY_COMPLETED : Notification显示，下载进行时，和完成之后都会显示。 
VISIBILITY_VISIBLE_NOTIFY_ONLY_COMPLETION ：只有当任务完成时，Notification才会显示。 
对Notification的设定方法相对较少。
```

- 设置下载文件类型

```java
request.setMimeType("application/vnd.android.package-archive");
```

- 添加请求下载的网络链接的Http，比如User-Agent，gzip压缩等：

```java
request.addRequestHeader(String header, String value)；
```

##### 2.3.Query

我们的需求，可能不只是在Notification 中显示进度就好了，也许，在app中也需要获取实时下载进度。所以Query类就是提供查询的一些方法。 
但API中就只有两个方法，原来，他把数据保存在[数据库](https://link.juejin.im/?target=http%3A%2F%2Flib.csdn.net%2Fbase%2Fmysql)中去了。我们需要获得一个Cursor 结果集，通过结果集获得我们想要的数据。

```java
 DownloadManager.Query query = new DownloadManager.Query();
 Cursor cursor = downloadManager.query(query.setFilterById(id));
                if (cursor != null && cursor.moveToFirst()) {
                    //下载的文件到本地的目录
                    String address = cursor.getString(cursor.getColumnIndex(DownloadManager.COLUMN_LOCAL_URI));
                    //已经下载的字节数
                    int bytes_downloaded = cursor.getInt(cursor.getColumnIndex(DownloadManager.COLUMN_BYTES_DOWNLOADED_SO_FAR));
                    //总需下载的字节数
                    int bytes_total = cursor.getInt(cursor.getColumnIndex(DownloadManager.COLUMN_TOTAL_SIZE_BYTES));
                    //Notification 标题
                    String title =cursor.getString(cursor.getColumnIndex(DownloadManager.COLUMN_TITLE));
                    //描述
                    String description =cursor.getString(cursor.getColumnIndex(DownloadManager.COLUMN_DESCRIPTION));
                    //下载对应id
                    long id =cursor.getLong(cursor.getColumnIndex(DownloadManager.COLUMN_ID));
                    //下载文件名称
                    String filename =cursor.getString(cursor.getColumnIndex(DownloadManager.COLUMN_LOCAL_FILENAME));
                    //下载文件的URL链接
                    String url =cursor.getString(cursor.getColumnIndex(DownloadManager.COLUMN_URI));
}
```

这只能获取一次，数据库中的信息。我们可以使用Timer类，每隔一段时间去查询数据库即可。也可以使用ContentProvider去访问。

#### 3.参考内容

https://gist.github.com/ysmintor/e9a6a41a43bbb0d9b3ecde7cff12b469

https://developer.android.com/reference/android/app/DownloadManager

https://juejin.im/entry/58b69df2a22b9d005ec81a72

### 二、媒体和播放

#### 1.简介

本节介绍如何将媒体播放器应用程序分隔为媒体控制器（用于UI）和媒体会话（用于实际播放器）。 它描述了两种媒体应用程序架构：适用于音频应用程序的客户端/服务器设计以及适用于视频播放器的单活动设计。 它还说明了如何使媒体应用程序响应硬件控制并与使用音频输出流的其他应用程序合作。

#####1.1.播放器和UI

播放音频或视频的多媒体应用程序通常包含两部分：

- 将数字媒体带入并将其呈现为视频和/或音频的播放器
- 具有传输控件的UI，用于运行播放器并可选择显示播放器的状态

![](https://developer.android.com/guide/topics/media-apps/images/ui-and-player.png)

在Android中，您可以从头开始构建自己的播放器，或者您可以从以下选项中进行选择：

- MediaPlayer类为支持最常见音频/视频格式和数据源的简单播放器提供基本功能。
- ExoPlayer是一个开源库，可以公开较低级别的Android音频API。 ExoPlayer支持MediaPlayer中没有的高性能功能，如DASH和HLS流。 您可以自定义ExoPlayer代码，从而轻松添加新组件。 ExoPlayer只能用于Android 4.1及更高版本。

##### 1.2.媒体应用和Android音频基础设施

精心设计的媒体应用程序应与其他播放音频的应用程序“很好地协同工作”。它应该准备好共享手机并与您设备上使用音频的其他应用程序合作。它还应响应设备上的硬件控制。

![](https://developer.android.com/guide/topics/media-apps/images/plays-with-others.png)

控制音频输出中描述了所有这些行为。

##### 1.3.媒体兼容库

media-compat库包含有助于构建播放音频和视频的应用程序的类。 这些类与运行Android 2.3（API级别9）及更高版本的设备兼容。 它们还可以与其他Android功能配合使用，以创建舒适，熟悉的Android体验。

媒体会话和媒体控制器的推荐实现是MediaSessionCompat和MediaControllerCompat类，它们在media-compat支持库中定义。 它们取代了Android 5.0（API级别21）中引入的早期版本的MediaSession和MediaController。 compat类提供相同的功能，但更容易开发您的应用程序，因为您只需要写入一个API。 该库通过将媒体会话方法转换为旧平台版本上的等效方法（如果可用）来处理向后兼容性。

如果您已经有一个使用旧类的工作应用程序，我们建议您更新到compat类。当您使用compat版本时，您可以删除对registerMediaButtonReceiver（）的所有调用以及RemoteControlClient中的所有方法。

##### 1.4.测量性能

在Android 8.0（API级别26）及更高版本中，getMetrics（）方法适用于某些媒体类。 它返回包含配置和性能信息的PersistableBundle对象，表示为属性和值的映射。 为这些媒体类定义了getMetrics（）方法：

- `MediaPlayer.getMetrics()`
- `MediaRecorder.getMetrics()`
- `MediaCodec.getMetrics()`
- `MediaExtractor.getMetrics()`

为每个实例单独收集度量标准，并在实例的生命周期内持续存在。如果没有可用的度量标准，则该方法返回null。返回的实际指标取决于类。

#### 2.参考内容：

https://developer.android.com/guide/topics/media-apps/audio-app/building-an-audio-app

###三、通知

#### 1.简介

通知是您可以在应用的常规 UI 外部向用户显示的消息。当您告知系统发出通知时，它将先以图标的形式显示在**通知区域**中。用户可以打开**抽屉式通知栏**查看通知的详细信息。 通知区域和抽屉式通知栏均是由系统控制的区域，用户可以随时查看

![](https://developer.android.com/images/ui/notifications/notification_area.png)

​							     **图 1.** 通知区域中的通知。

![](https://developer.android.com/images/ui/notifications/notification_drawer.png)

**图 2.** 抽屉式通知栏中的通知。

#### 2.参考内容

https://developer.android.com/guide/topics/ui/notifiers/notifications

###四、共享

#### 1.简介

通过在Android 4.0（API级别14）中引入ActionProvider，可以更轻松地在ActionBar中实现有效且用户友好的共享操作。 ActionProvider一旦附加到操作栏中的菜单项，就会处理该项的外观和行为。 在ShareActionProvider的情况下，您提供共享意图并完成剩下的工作。

注意：从API Level 14及更高版本开始，可以使用ShareActionProvider。

#### 2.如何使用

- 更新菜单声明

要开始使用ShareActionProviders，请在菜单资源文件中为相应的<item>定义android：actionProviderClass属性：

```xml
<menu xmlns:android="http://schemas.android.com/apk/res/android">
    <item
            android:id="@+id/menu_item_share"
            android:showAsAction="ifRoom"
            android:title="Share"
            android:actionProviderClass=
                "android.widget.ShareActionProvider" />
    ...
</menu>
```

![](https://developer.android.com/images/ui/actionbar-shareaction.png)

图1. Gallery应用程序中的ShareActionProvider。

这将项目的外观和功能的责任委托给ShareActionProvider。但是，您需要告知提供商您想要分享的内容。

- 设置分享Intent

为了使ShareActionProvider正常运行，您必须为其提供共享意图。 此共享意图应与将简单数据发送到其他应用课程中所述相同，操作为ACTION_SEND，并通过额外内容（如EXTRA_TEXT和EXTRA_STREAM）设置其他数据。 要分配共享意图，首先在活动或片段中夸大菜单资源时找到相应的MenuItem。 接下来，调用MenuItem.getActionProvider（）以检索ShareActionProvider的实例。 使用setShareIntent（）更新与该操作项关联的共享意图。 这是一个例子：

```kotlin
private var mShareActionProvider: ShareActionProvider? = null
...

override fun onCreateOptionsMenu(menu: Menu): Boolean {
    // Inflate menu resource file.
    menuInflater.inflate(R.menu.share_menu, menu)

    // Locate MenuItem with ShareActionProvider
    menu.findItem(R.id.menu_item_share).also { menuItem ->
        // Fetch and store ShareActionProvider
        mShareActionProvider = menuItem.actionProvider as? ShareActionProvider
    }

    // Return true to display menu
    return true
}

// Call to update the share intent
private fun setShareIntent(shareIntent: Intent) {
    mShareActionProvider?.setShareIntent(shareIntent)
}
```

您可能只需要在创建菜单期间设置一次共享意图，或者您可能希望设置它，然后在UI更改时更新它。 例如，当您在“图库”应用中全屏查看照片时，在照片之间切换时，共享意图会发生变化。 有关ShareActionProvider对象的进一步讨论，请参阅[操作视图](https://developer.android.com/reference/android/widget/ShareActionProvider.html)和[操作提供程序](https://developer.android.com/training/appbar/action-views.html)。

#### 3.参考内容

https://developer.android.com/training/sharing/shareaction

http://www.jcodecraeer.com/a/anzhuokaifa/androidkaifa/2014/1121/2029.html

###五、权限

#### 1.简介

权限的目的是保护Android用户的隐私。Android应用必须请求访问敏感用户数据（如联系人和短信）的权限，以及某些系统功能（如相机和互联网）。根据功能的不同，系统可能会自动授予权限，也可能会提示用户批准请求。

#### 2.参考内容

https://developer.android.com/guide/topics/permissions/overview?hl=zh-cn

###六、切片

#### 1.简介

切片是一种UI模板，可以在Google搜索应用中以及稍后在Google智能助理等其他位置显示来自您应用的丰富，动态和互动内容。 通过在全屏应用体验之外启用参与，切片可以帮助用户更快地执行任务。 您可以将切片构建为App Actions的增强功能。 Android Jetpack内置了对切片的支持，可以一直延伸到Android 4.4，大约95％的Android用户。

Slices 在国内应用的范围不广，重要是因为Slices是 Google Assistant 的延伸，谷歌希望使用者能过快速到达App里面的某个特点功能，举一例子就是，你对Google Assistant说你要回家，那么以前可能只会出现滴滴，Uber的选项，但是引进Slices之后会显示更加详细的数据列表，比如滴滴item下会出现到家多少距离，多少钱，是否立即打车等等。Google Assistant 在国内不好用，但是谷歌有这个功能开源我们自己其实也可以去实现，可能小米会把这个功能给小艾同学吧。

开始搭建我们的Slices吧。注意注意：开发环境必须是Android Studio 3.2 以及以上，最低版本Android 4.4 (API level 19) 。

#### 2.如何使用

#####2.1.预先条件

虽然并非总是需要，但Android Studio 3.2或更高版本包含可帮助您进行Slice开发的其他工具和功能，包括：

- AndroidX重构工具：如果您正在使用AndroidX库的项目中工作，则需要此工具。
- 切片lint检查：在构建切片时捕获常见的反实践
- SliceProvider模板：在构建SliceProvider时处理样板

##### 2.2.下载和安装Slice Viewer

下载最新的示例[Slice Viewer APK](https://github.com/googlesamples/android-SliceViewer/releases)版本，您可以使用它来测试切片而无需实现SliceView API。您可以选择在此处查看/克隆[SliceViewer](https://github.com/googlesamples/android-SliceViewer/releases/download/1.0.0-alpha1/slice-viewer.apk)源。

注意：Slice Viewer需要Android 4.4（API级别19）或更高版本。 如果在您的环境中未正确设置ADB，请参阅ADB指南以获取更多信息。

通过在与下载的slice-viewer.apk相同的目录中运行以下命令，在您的设备上安装SliceViewer：

```
adb install -r -t slice-viewer.apk
```

##### 2.3.运行Slice Viewer

您可以从Android Studio项目或命令行启动Slice Viewer：

1. In your project, select **Run** > **Edit Configurations…**

2. In the top-left corner, click the green plus sign

3. Select **Android App**

   ![](https://developer.android.com/guide/slices/images/sliceviewer-setup-1.png?hl=zh-cn)

4. Enter **slice** in the name field

5. Select your app module in the **Module** dropdown

6. Under **Launch Options**, select **URL** from the **Launch** dropdown

7. Enter `slice-<your slice URI>` in the URL field

   Example: `slice-content://com.example.your.sliceuri

   ![](https://developer.android.com/guide/slices/images/sliceviewer-setup-2.png?hl=zh-cn)

8. Click **OK**

注意：下次要启动切片查看器以查看切片时，可以使用刚刚创建的配置

##### 2.4通过ADB启动Slice Viewer工具（命令行）

Run your app from Android Studio:

```
adb install -t -r <yourapp>.apk
```

View your Slice by running the following command:

```bash
adb shell am start -a android.intent.action.VIEW -d slice-<your slice URI>
```

![](https://developer.android.com/guide/slices/images/sliceviewer-1.png?hl=zh-cn)

*Slice Viewer showing a single WiFi Slice*

#### 3.参考内容

https://juejin.im/entry/5b32fd8d51882574cb648083

https://juejin.im/entry/5b32fd8d51882574cb648083

https://developer.android.com/guide/slices/getting-started?hl=zh-cn

#### 七、偏好

##### 1.简介

设置允许用户更改应用程序的功能和行为。设置可能会影响后台行为，例如应用程序与云同步数据的频率，或者它们可能更广泛，例如更改用户界面的内容和显示。

将用户可配置设置集成到应用程序中的推荐方法是使用AndroidX Preference Library。此库管理用户界面并与存储进行交互，以便您仅定义用户可以配置的各个设置。该库带有Material主题，可跨设备和OS版本提供一致的用户体验。

##### 2.参考内容

https://developer.android.com/guide/topics/ui/settings/

https://www.jianshu.com/p/6d6f84e2f50d

https://www.tinyx.cc/android/android-experience/android-preference.html



