---
layout: post
title: Android Jetpack
category: JetPack
tags: [android,Jetpack]
keywords: Android,JetPack
excerpt: Android Jetpack
---



#Android Jetpack

### 什么是Android Jetpack？

Android Jetpack 是一套组件、工具和指导，可以帮助您构建出色的 Android 应用。Android Jetpack 组件将现有的支持库与架构组件联系起来，并将它们分成四个类别：

![img](https://1.bp.blogspot.com/-dwL58chu7wo/WvD1RrHln3I/AAAAAAAAFUg/cRTc0IZga_wMPTWr3CI53IZ5BwtnZMeYACLcBGAs/s1600/Screen%2BShot%2B2018-05-05%2Bat%2B11.49.30%2BAMimage1.png)

### Android Jetpack的组成部分

####1、Foundation （基础）

基础组件提供核心系统功能、Kotlin 扩展以及对多 dex 处理和自动化测试的支持。

- AppCompat：在较低版本的 Android 系统上恰当地降级
- Android KTX：编写更简洁、惯用的 Kotlin 代码
- 多 dex 处理：为具有多个 DEX 文件的应用提供支持
- 测试：用于单元和运行时界面测试的 Android 测试框架

#### 2、Architecture （架构）

架构组件具有可帮助管理界面组件生命周期、处理数据持久性等的类。

- 数据绑定：以声明方式将可观察数据绑定到界面元素
- Lifecycles：管理您的 Activity 和 Fragment 生命周期
- LiveData：在底层数据库更改时通知视图
- Navigation：处理应用内导航所需的一切
- Paging：逐步从您的数据源按需加载信息
- Room：流畅地访问 SQLite 数据库
- ViewModel：以注重生命周期的方式管理界面相关的数据
- WorkManager：管理您的 Android 后台作业

#### 3、Behavior （行为）

行为组件可帮助您设计稳健、可测试且易维护的应用。

- 下载管理器：安排和管理大量下载任务
- 媒体和播放：用于媒体播放和路由的向后兼容 API（包括 Google Cast）
- 通知：提供向后兼容的通知 API，支持 Wear 和 Auto
- 权限：用于检查和请求应用权限的兼容性 API
- 共享：提供适合应用操作栏的共享操作
- 切片：创建可在应用外部显示应用数据的灵活界面元素

#### 4、UI （界面）

界面组件让您能够轻松地使您的应用用起来不仅简单，而且令人愉悦。

 - 动画和过渡：移动微件和在屏幕之间过渡

 - Auto：帮助开发 Android Auto 应用的组件。

 - 表情符号：在旧版平台上启用最新的表情符号字体

 - Fragment：组件化界面的基本单位

 - 布局：使用不同的算法布置微件

 - 调色板：从调色板中提取出有用的信息

 - TV：帮助开发 Android TV 应用的组件。

 - Wear OS by Google 谷歌：帮助开发 Wear 应用的组件。




