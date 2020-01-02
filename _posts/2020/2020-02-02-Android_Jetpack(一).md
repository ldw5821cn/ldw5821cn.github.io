---
layout: post
title: Android Jetpack --Foundation（行为）
category: Jetpack
tags: [android, Jetpack]
keywords: Jetpack
excerpt: Android Jetpack --Foundation（行为）
---

##Android Jetpack --Foundation（行为）

[TOC]



### 一、AppCompat

#### 1.简介

Android 支持库提供了诸多未内置于框架的功能。这些库提供向后兼容版本的新功能、框架中未包含的实用 UI 元素，以及应用可以利用的一系列实用程序。

Android 支持库软件包含可以添加至应用的多个库。每个库均支持特定范围的 Android 平台版本和功能。

本指南介绍了各支持库提供的重要功能和版本支持，从而帮助您决定在应用中添加哪些支持库。一般而言，我们建议添加 [v4 支持库](https://developer.android.com/topic/libraries/support-library/features?hl=zh-cn#v4)和 [v7 appcompat 库](https://developer.android.com/topic/libraries/support-library/features?hl=zh-cn#v7-appcompat)，因为它们支持一系列 Android 版本，并且可以为推荐的用户界面模式提供 API。

要使用以下任一库，您必须将库文件下载到 Android SDK 安装位置中。请按照[支持库设置](https://developer.android.com/tools/support-library/setup.html?hl=zh-cn#download)中下载支持库的说明完成此步骤。要在应用中添加特定支持库，您还必须执行其他步骤。有关如何在应用中添加支持库的重要信息，请参阅下面各个库内容的末尾部分。

#####1.1概览

许多情况下，某项功能可能对应用开发者很有用，但是添加到 Android 框架却并不合适。例如，某个应用可能仅需要用于特定用例的某项功能，如在不同版本的 Android 系统之间顺畅切换。

为了解决这一问题，Android SDK 添加了多个库，这些库统称为 *Android 支持库*。如果应用开发者想要在应用中集成库功能，他们可以添加其中任意一个库。

支持库提供一系列不同的功能：

- [向后兼容](https://developer.android.com/topic/libraries/support-library/?hl=zh-cn#backward)版本的框架组件。
- 用于实现建议的 Android [布局模式](https://developer.android.com/topic/libraries/support-library/?hl=zh-cn#layout-patterns)的 UI 元素。
- 支持不同的[设备类型](https://developer.android.com/topic/libraries/support-library/?hl=zh-cn#form-factors)。
- 其他[实用程序](https://developer.android.com/topic/libraries/support-library/?hl=zh-cn#utils)功能。

#####1.2向后兼容性

支持库可以让在旧版本 Android 平台上运行的应用支持为新版本平台推出的功能。例如，应用在依赖于框架类的 5.0（API 级别 21）版本以下的 Android 系统上运行时，将无法显示 Material Design 元素，因为该版本的 Android 框架不支持 Material Design。但是，如果此应用添加了支持库的 [appcompat 库](https://developer.android.com/tools/support-library/features.html?hl=zh-cn)，则可以访问 API 级别 21 中具有的许多功能，其中包括对 Material Design 的支持。因此，您的应用可以在多个平台版本中提供更为一致的体验。

某些情况下，类的支持库版本很大程度上取决于框架提供的功能。因此，如果应用调用其中一个支持类的方法，则支持库的行为将取决于运行应用的 Android 版本。如果框架提供必要的功能，则支持库将通过调用框架执行任务。如果应用在旧版本的 Android 上运行，且框架未显示所需的功能，则支持库自身可能会尝试提供功能或什么都不做。无论是哪一种情形，应用通常都不需要检查其在哪一版本的 Android 上运行，而是通过支持库执行检查并选择适当的行为。通常情况下，名称以 `…Compat`（如 `ActivityCompat`）结束的类即是如此。

而另外一些情况下，支持库类提供一个不依赖于任何框架 API 可用性的完整、独立版框架类。这些方法可以在支持的所有平台中提供一致的行为。

无论是哪一种情形，应用均无需在运行期间检查系统版本。应用可通过支持库类执行适当的系统检查，并在必要时修改其行为。

![](https://developer.android.com/images/tools/support-library/appbar-kitkat.png?hl=zh-cn)



**图 1.** 由于此应用使用支持库 UI 元素，即使是在对 Material Design 不提供原生支持的 Android 4.4 上运行，其界面仍符合 Material Design 原则。

#####1.3支持一般布局模式

支持库提供 Android 框架中未提供的用户界面元素。例如，Android 支持库提供其他布局类，如 `DrawerLayout`。这些类遵循建议的 Android 设计做法；例如，设计库以一种适合多个 Android 版本的方式遵循 Material Design 的原则。

通过使用这些支持库类，您可以避免做一些重复性工作；如果应用有特殊的用户界面要求，您可以利用现有代码，这些代码将提供用户已经熟悉的用户界面。这些元素还可以帮助您开发看起来像 Android 生态系统一部分的应用。例如，许多应用需要显示任意长的元素列表，且需要能够在列表发生变化时快速有效地重复使用这些元素；这可以是电子邮件列表、联系人列表以及音乐专辑列表，等等。这些应用可以使用支持库 `RecyclerView` 小部件显示列表。这既可以让应用开发者不必从头开始开发列表，又能确保用户看到一个外观和行为与其他应用中的列表类似的列表。

#####1.4支持不同的设备类型

Android SDK 为 TV 和穿戴式设备等多种不同的设备类型提供库。应用可以通过相应的支持库为各种平台版本提供功能，且可以在外部屏幕、扬声器和其他目标设备上提供内容。

#####1.5一般实用程序

Android 支持库提供后向兼容的实用程序功能。应用可以使用这些实用程序功能为各种 Android 系统版本提供相应的用户体验。例如，支持库的权限处理方式取决于运行应用的平台版本。如果平台支持运行时权限模式，这些方法会向用户请求相应的权限；在不支持运行时权限模式的平台版本中，这些方法将在安装时检查是否已获得相应的权限。

####2.参考内容

https://developer.android.com/topic/libraries/support-library/?hl=zh-cn

https://developer.android.com/topic/libraries/support-library/features?hl=zh-cn

https://developer.android.com/topic/libraries/support-library/setup?hl=zh-cn

https://developer.android.com/topic/libraries/support-library/packages?hl=zh-cn

### 二、Android KTX

#### 1.简介

Android KTX 是一组 Kotlin 扩展程序，属于 Android [Jetpack](https://developer.android.com/jetpack?hl=zh-cn) 系列。它优化了供 Kotlin 使用的 Jetpack 和 Android 平台 API。Android KTX 旨在让您利用 Kotlin 语言功能（例如扩展函数/属性、lambda、命名参数和参数默认值），以更简洁、更愉悦、更惯用的方式使用 Kotlin 进行 Android 开发。Android KTX 不会向现有的 Android API 添加任何新功能。

要详细了解 Android KTX，请观看我们的 [DevBytes 视频](https://www.youtube.com/watch?v=r_19VZ0xRO8&feature=youtu.be&hl=zh-cn)。

#### 2.如何使用

- 添加Android KTX

```groovy
//要开始使用 Android KTX，请将以下代码添加到项目的 build.gradle 文件中：
repositories {
    google()
}

//Android KTX 划分为不同的模块。每个模块都包含一个或多个软件包。使用模块时，请在应用的 build.gradle 文件中为每个 Android KTX 软件工件添加一个依赖项。请记住在软件工件后面附上版本号。例如，如果您使用 core-ktx 模块，则完整的依赖项将如下所示：

dependencies {
    implementation 'androidx.core:core-ktx:1.0.0-alpha1'
}
```

- 模块列表

Android KTX 由以下 Maven 软件工件组成。要获取 API 参考文档，请点击特定软件包名称并查看扩展函数摘要。

| 模块（软件工件）                                 | 版本          | 软件包                                                       |
| ------------------------------------------------ | ------------- | ------------------------------------------------------------ |
| androidx.core:core-ktx                           | 1.0.0-alpha1  | 查看下面的[所有核心软件包](https://developer.android.com/kotlin/ktx?hl=zh-cn#core-packages)。 |
| androidx.fragment:fragment-ktx                   | 1.0.0-alpha1  | [androidx.fragment.app](https://developer.android.com/reference/kotlin/androidx/fragment/app/package-summary?hl=zh-cn#extension-functions-summary) |
| androidx.palette:palette-ktx                     | 1.0.0-alpha1  | [androidx.palette.graphics](https://developer.android.com/reference/kotlin/androidx/palette/graphics/package-summary?hl=zh-cn#extension-functions-summary) |
| androidx.sqlite:sqlite-ktx                       | 1.0.0-alpha1  | [androidx.sqlite.db](https://developer.android.com/reference/kotlin/androidx/sqlite/db/package-summary?hl=zh-cn#extension-functions-summary) |
| androidx.collection:collection-ktx               | 1.0.0-alpha1  | [androidx.collection](https://developer.android.com/reference/kotlin/androidx/collection/package-summary?hl=zh-cn#extension-functions-summary) |
| androidx.lifecycle:lifecycle-viewmodel-ktx       | 2.0.0-alpha1  | [androidx.lifecycle](https://developer.android.com/reference/kotlin/androidx/lifecycle/package-summary?hl=zh-cn#extension-functions-summary) |
| androidx.lifecycle:lifecycle-reactivestreams-ktx | 2.0.0-alpha1  | [androidx.lifecycle](https://developer.android.com/reference/kotlin/androidx/lifecycle/package-summary?hl=zh-cn#extension-functions-summary) |
| android.arch.navigation:navigation-common-ktx    | 1.0.0-alpha01 | [androidx.navigation](https://developer.android.com/reference/kotlin/androidx/navigation/package-summary?hl=zh-cn#extension-functions-summary) |
| android.arch.navigation:navigation-fragment-ktx  | 1.0.0-alpha01 | [androidx.navigation.fragment](https://developer.android.com/reference/kotlin/androidx/navigation/fragment/package-summary?hl=zh-cn#extension-functions-summary) |
| android.arch.navigation:navigation-runtime-ktx   | 1.0.0-alpha01 | [androidx.navigation](https://developer.android.com/reference/kotlin/androidx/navigation/package-summary?hl=zh-cn#extension-functions-summary) |
| android.arch.navigation:navigation-testing-ktx   | 1.0.0-alpha01 | [androidx.navigation.testing](https://developer.android.com/reference/kotlin/androidx/navigation/testing/package-summary?hl=zh-cn#extension-functions-summary) |
| android.arch.navigation:navigation-ui-ktx        | 1.0.0-alpha01 | [androidx.navigation.ui](https://developer.android.com/reference/kotlin/androidx/navigation/ui/package-summary?hl=zh-cn#extension-functions-summary) |
| android.arch.work:work-runtime-ktx               | 1.0.0-alpha01 | [androidx.work.ktx                                           |



​	核心模块包括以下软件包：

​	[androidx.core.animation](https://developer.android.com/reference/kotlin/androidx/core/animation/package-summary?hl=zh-cn#extension-functions-summary)

​	[androidx.core.content](https://developer.android.com/reference/kotlin/androidx/core/content/package-summary?hl=zh-cn#extension-functions-summary)

​	[androidx.core.graphics](https://developer.android.com/reference/kotlin/androidx/core/graphics/package-summary?hl=zh-cn#extension-functions-summary)

​	[androidx.core.graphics.drawable](https://developer.android.com/reference/kotlin/androidx/core/graphics/drawable/package-summary?hl=zh-cn#extension-functions-summary)

​	[androidx.core.net](https://developer.android.com/reference/kotlin/androidx/core/net/package-summary?hl=zh-cn#extension-functions-summary)

​	[androidx.core.os](https://developer.android.com/reference/kotlin/androidx/core/os/package-summary?hl=zh-cn#extension-functions-summary)

​	[androidx.core.preference](https://developer.android.com/reference/kotlin/androidx/core/preference/package-summary?hl=zh-cn#extension-functions-summary)

​	[androidx.core.text](https://developer.android.com/reference/kotlin/androidx/core/text/package-summary?hl=zh-cn#extension-functions-summary)

​	[androidx.core.transition](https://developer.android.com/reference/kotlin/androidx/core/transition/package-summary?hl=zh-cn#extension-functions-summary)

​	[androidx.core.util](https://developer.android.com/reference/kotlin/androidx/core/util/package-summary?hl=zh-cn#extension-functions-summary)

​	[androidx.core.view](https://developer.android.com/reference/kotlin/androidx/core/view/package-summary?hl=zh-cn#extension-functions-summary)

​	[androidx.core.widget](https://developer.android.com/reference/kotlin/androidx/core/widget/package-summary?hl=zh-cn#extension-functions-summary)



- 示例

Android KTX 是 [Android Jetpack](https://developer.android.com/jetpack/?hl=zh-cn) 基础组件。您可以在 [Sunflower](https://github.com/googlesamples/android-sunflower) 演示应用中查看它的使用情况。

以下示例演示了一些 Android KTX 扩展函数。它们按模块（软件工件）名称分组。有关扩展函数的完整列表，请查看完整的软件包参考文档。

**androidx.core:core-ktx**

```kotlin
sharedPreferences.edit()
    .putBoolean("key", value)
    .apply()

view.viewTreeObserver.addOnPreDrawListener(
    object : ViewTreeObserver.OnPreDrawListener {
        override fun onPreDraw(): Boolean {
            viewTreeObserver.removeOnPreDrawListener(this)
            actionToBeTriggered()
            return true
        }
    }
)
```

**androidx.sqlite:sqlite-ktx**

```kotlin
db.beginTransaction()
try {
    // insert data
    db.setTransactionSuccessful()
} finally {
    db.endTransaction()
}
```

**androidx.fragment:fragment-ktx**

```kotlin
supportFragmentManager
    .beginTransaction()
    .replace(R.id.my_fragment_container, myFragment, FRAGMENT_TAG)
    .commitAllowingStateLoss()
```

#### 3.参考内容

https://developer.android.com/kotlin/ktx?hl=zh-cn#kotlin

https://developer.android.com/kotlin/resources?hl=zh-cn

###三、MultiDex

#### 1.简介

Dex（适用于Dalvik Executable）是一个可执行文件，可在Android上的虚拟机Dalvik上运行。 所以dex文件只是Android的可执行文件。 Dalvik可执行规范将65,536设置为.dex文件可包含的最大方法数。

作为Android SDK的方法，您的项目所有库的方法依赖于此限制的计数，您将很容易达到该限制。 在这种情况下，您需要将应用程序拆分为多个.dex文件。 要做到这一点，你需要使用Multidex，这是一个允许你这样做的库。

#### 2.如何使用

- minSdkVersion >= 21

如果您的 `minSdkVersion` 设置为 21 或更高值，您只需在模块级 `build.gradle` 文件中将 `multiDexEnabled` 设置为 `true`，如此处所示：

```groovy
android {
    defaultConfig {
        ...
        minSdkVersion 21 
        targetSdkVersion 28
        multiDexEnabled true
    }
    ...
}
```

- minSdkVersion < 21

修改模块级 `build.gradle` 文件以启用 Dalvik 可执行文件分包，并将 Dalvik 可执行文件分包库添加为依赖项，如此处所示：

```groovy
android {
    defaultConfig {
        ...
        minSdkVersion 15 
        targetSdkVersion 28
        multiDexEnabled true
    }
    ...
}

dependencies {
  compile 'com.android.support:multidex:1.0.3'
}
```

根据是否要替换 [`Application`](https://developer.android.com/reference/android/app/Application.html?hl=zh-cn) 类，执行以下操作之一：

如果您*没有*替换 [`Application`](https://developer.android.com/reference/android/app/Application.html?hl=zh-cn) 类，请编辑清单文件，按如下方式设置 `<application>` 标记中的 `android:name`：

```xml
<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    package="com.example.myapp">
    <application
            android:name="android.support.multidex.MultiDexApplication" >
        ...
    </application>
</manifest>
```



如果您替换了 [`Application`](https://developer.android.com/reference/android/app/Application.html?hl=zh-cn) 类，请按如下方式对其进行更改以扩展 `MultiDexApplication`（如果可能）：

```java
public class MyApplication extends MultiDexApplication { ... }
```

或者，如果您替换了 [`Application`](https://developer.android.com/reference/android/app/Application.html?hl=zh-cn) 类，但无法更改基本类，则可以改为替换 [`attachBaseContext()`](https://developer.android.com/reference/android/content/ContextWrapper.html?hl=zh-cn#attachBaseContext(android.content.Context)) 方法并调用 [`MultiDex.install(this)`](https://developer.android.com/reference/android/support/multidex/MultiDex.html?hl=zh-cn#install(android.content.Context)) 来启用 Dalvik 可执行文件分包：

```kotlin
public class MyApplication extends SomeOtherApplication {
  @Override
  protected void attachBaseContext(Context base) {
     super.attachBaseContext(base);
     MultiDex.install(this);
  }
}
```

构建应用后，Android 构建工具会根据需要构建主 DEX 文件 (`classes.dex`) 和辅助 DEX 文件（`classes2.dex` 和 `classes3.dex` 等）。然后，构建系统会将所有 DEX 文件打包到您的 APK 中。

运行时，Dalvik 可执行文件分包 API 使用特殊的类加载器来搜索适用于您的方法的所有 DEX 文件（而不是仅在主 `classes.dex` 文件中搜索）。

#### 3.参考内容

https://developer.android.com/studio/build/multidex?hl=zh-cn

### 四、Test

#### 1.简介

Android 测试支持库提供了大量用于测试 Android 应用的框架。此库提供了一组 API，让您可以为应用快速构建何运行测试代码，包括 JUnit 4 和功能性用户界面 (UI) 测试。您可以从 [Android Studio IDE](https://developer.android.com/tools/studio/index.html) 或命令行运行使用这些 API 创建的测试。

Android 测试支持库通过 Android SDK 管理器提供。如需了解详细信息，请参阅[测试支持库设置](https://developer.android.com/topic/libraries/testing-support-library/#setup)

本页介绍了 Android 测试支持库提供了哪些工具、如何在测试环境中使用这些工具，以及库版本的相关信息。

**测试支持库功能**

Android 测试支持库包括以下自动化测试工具：

- **AndroidJUnitRunner**：适用于 Android 且与 JUnit 4 兼容的测试运行器
- **Espresso**：UI 测试框架；适合应用中的功能性 UI 测试
- **UI Automator**：UI 测试框架；适合跨系统和已安装应用的跨应用功能性 UI 测试

#### 2.如何使用

- 添加测试支持库设置

```groovy
dependencies {
  androidTestCompile 'com.android.support.test:runner:0.4'
  // Set this dependency to use JUnit 4 rules
  androidTestCompile 'com.android.support.test:rules:0.4'
  // Set this dependency to build and run Espresso tests
  androidTestCompile 'com.android.support.test.espresso:espresso-core:2.2.1'
  // Set this dependency to build and run UI Automator tests
  androidTestCompile 'com.android.support.test.uiautomator:uiautomator-v18:2.1.2'
}
```

要将 [`AndroidJUnitRunner`](https://developer.android.com/reference/android/support/test/runner/AndroidJUnitRunner.html?hl=zh-cn) 设置为 Gradle 项目中的默认测试仪器运行器，请在 `build.gradle` 文件中指定此依赖关系：

```groovy
android {
    defaultConfig {
        testInstrumentationRunner "android.support.test.runner.AndroidJUnitRunner"
    }
}
```

强烈建议将 Android 测试支持库与 Android Studio IDE 搭配使用。Android Studio 提供支持测试开发的功能，例如：

- 基于 Gradle 的灵活构建系统，为测试代码提供依赖关系管理支持
- 单一的项目结构，包含单元与仪器测试代码以及应用源代码
- 支持在虚拟或物理设备上通过命令行或图形用户界面部署和运行测试

如需了解有关 Android Studio 及其下载的详细信息，请参阅[下载 Android Studio 和 SDK 工具](https://developer.android.com/studio/index.html?hl=zh-cn)。

#### 3.参考内容

https://developer.android.com/topic/libraries/testing-support-library/?hl=zh-cn

