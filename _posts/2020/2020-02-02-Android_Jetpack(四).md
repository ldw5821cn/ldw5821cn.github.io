---
layout: post
title:Android Jetpack(四)--UI（界面）
category: Jetpack
tags: [android, Jetpack]
keywords: Android
excerpt: Android Jetpack(四)--UI（界面）
---

## Android Jetpack(四)--UI（界面）

[TOC]

### 一、动画和过渡

#### 1.简介

当您的UI响应用户操作而更改时，您应该为布局过渡设置动画。 这些动画为用户提供有关其操作的反馈，并帮助他们按照UI进行操作。 Android包含转换框架，使您可以轻松地为两个视图层次结构之间的更改设置动画。 该框架通过随时间更改其某些属性值来在运行时为视图设置动画。 该框架包含用于常见效果的内置动画，并允许您创建自定义动画和转换生命周期回调。

#### 2.参考内容

https://developer.android.com/training/animation/?hl=zh-cn

http://johnnyshieh.me/posts/android-transition-animation/

### 二、Auto

#### 1.简介

让您在编写应用时无需担心特定于车辆的硬件差异（如屏幕分辨率、软件界面、旋钮和触摸式控件）。用户可以通过手机上的 Android Auto 应用访问您的应用。或者，当连接到兼容车辆时，运行 Android 5.0（或更高版本）的手持设备上的应用可以与通过 Android Auto 投射到车辆的应用进行通信。

**Android Auto**是[Google](https://zh.wikipedia.org/wiki/Google)推出的专为汽车所设计之[Android](https://zh.wikipedia.org/wiki/Android)功能，其需要连接[Android Lollipop](https://zh.wikipedia.org/wiki/Android_Lollipop)以上版本操作系统的手机使用。其预览在2014年6月26日的[Google I/O](https://zh.wikipedia.org/wiki/Google_I/O)之开幕式主题[演讲](https://zh.wikipedia.org/wiki/%E6%BC%94%E8%AE%B2)中被首度公之于众[[1\]](https://zh.wikipedia.org/wiki/Android_Auto#cite_note-keynote-1)。Android Auto目前仅在[美国](https://zh.wikipedia.org/wiki/%E7%BE%8E%E5%9C%8B)等少数国家及地区提供下载与服务。2018年11月[台湾](https://zh.wikipedia.org/wiki/%E5%8F%B0%E7%81%A3)已经提供下载。

#### 2.参考内容

https://developer.android.com/training/auto/?hl=zh-cn

https://developer.android.com/auto/?hl=zh-cn

https://www.zhihu.com/question/30715413/answer/54701620

### 三、表情符号

#### 1.简介

EmojiCompat支持库旨在使Android设备与最新的表情符号保持同步。它可以防止您的应用以☐的形式显示缺少的表情符号字符，表示您的设备没有显示文本的字体。 通过使用EmojiCompat支持库，您的应用用户无需等待Android操作系统更新即可获取最新的表情符号。

![](https://developer.android.com/guide/topics/ui/images/look-and-feel/emoji-compat/emoji-comparison.png?hl=zh-cn)

图1.表情符号比较

#### 2.参考内容

https://developer.android.com/guide/topics/ui/look-and-feel/emoji-compat?hl=zh-cn

http://www.jcodecraeer.com/a/anzhuokaifa/androidkaifa/2018/0115/9140.html

### 四、Fragment

#### 1.简介

`Fragment` 表示 `Activity` 中的行为或用户界面部分。您可以将多个片段组合在一个 Activity 中来构建多窗格 UI，以及在多个 Activity 中重复使用某个片段。您可以将片段视为 Activity 的模块化组成部分，它具有自己的生命周期，能接收自己的输入事件，并且您可以在 Activity 运行时添加或移除片段（有点像您可以在不同 Activity 中重复使用的“子 Activity”）。

片段必须始终嵌入在 Activity 中，其生命周期直接受宿主 Activity 生命周期的影响。 例如，当 Activity 暂停时，其中的所有片段也会暂停；当 Activity 被销毁时，所有片段也会被销毁。 不过，当 Activity 正在运行（处于*已恢复*[生命周期状态](https://developer.android.com/guide/components/activities.html?hl=zh-cn#Lifecycle)）时，您可以独立操纵每个片段，如添加或移除它们。 当您执行此类片段事务时，您也可以将其添加到由 Activity 管理的返回栈 — Activity 中的每个返回栈条目都是一条已发生片段事务的记录。 返回栈让用户可以通过按*返回*按钮撤消片段事务（后退）。

当您将片段作为 Activity 布局的一部分添加时，它存在于 Activity 视图层次结构的某个 `ViewGroup` 内部，并且片段会定义其自己的视图布局。您可以通过在 Activity 的布局文件中声明片段，将其作为 `<fragment>` 元素插入您的 Activity 布局中，或者通过将其添加到某个现有 `ViewGroup`，利用应用代码进行插入。不过，片段并非必须成为 Activity 布局的一部分；您还可以将没有自己 UI 的片段用作 Activity 的不可见工作线程。

本文描述如何在开发您的应用时使用片段，包括将片段添加到 Activity 返回栈时如何保持其状态、如何与 Activity 及 Activity 中的其他片段共享事件、如何为 Activity 的操作栏发挥作用等等。

**设计原理**

Android 在 Android 3.0（API 级别 11）中引入了片段，主要是为了给大屏幕（如平板电脑）上更加动态和灵活的 UI 设计提供支持。由于平板电脑的屏幕比手机屏幕大得多，因此可用于组合和交换 UI 组件的空间更大。利用片段实现此类设计时，您无需管理对视图层次结构的复杂更改。 通过将 Activity 布局分成片段，您可以在运行时修改 Activity 的外观，并在由 Activity 管理的返回栈中保留这些更改。

例如，新闻应用可以使用一个片段在左侧显示文章列表，使用另一个片段在右侧显示文章 — 两个片段并排显示在一个 Activity 中，每个片段都具有自己的一套生命周期回调方法，并各自处理自己的用户输入事件。 因此，用户不需要使用一个 Activity 来选择文章，然后使用另一个 Activity 来阅读文章，而是可以在同一个 Activity 内选择文章并进行阅读，如图 1 中的平板电脑布局所示。

您应该将每个片段都设计为可重复使用的模块化 Activity 组件。也就是说，由于每个片段都会通过各自的生命周期回调来定义其自己的布局和行为，您可以将一个片段加入多个 Activity，因此，您应该采用可复用式设计，避免直接从某个片段直接操纵另一个片段。 这特别重要，因为模块化片段让您可以通过更改片段的组合方式来适应不同的屏幕尺寸。 在设计可同时支持平板电脑和手机的应用时，您可以在不同的布局配置中重复使用您的片段，以根据可用的屏幕空间优化用户体验。 例如，在手机上，如果不能在同一 Activity 内储存多个片段，可能必须利用单独片段来实现单窗格 UI。

![](https://developer.android.com/images/fundamentals/fragments.png?hl=zh-cn)

**图 1.** 有关由片段定义的两个 UI 模块如何适应不同设计的示例：通过组合成一个 Activity 来适应平板电脑设计，通过单独片段来适应手机设计。

例如 — 仍然以新闻应用为例 — 在平板电脑尺寸的设备上运行时，该应用可以在 *Activity A* 中嵌入两个片段。 不过，在手机尺寸的屏幕上，没有足以储存两个片段的空间，因此*Activity A* 只包括用于显示文章列表的片段，当用户选择文章时，它会启动*Activity B*，其中包括用于阅读文章的第二个片段。 因此，应用可通过重复使用不同组合的片段来同时支持平板电脑和手机，如图 1 所示。

如需了解有关通过利用不同片段组合来适应不同屏幕配置这种方法设计应用的详细信息，请参阅[支持平板电脑和手机](https://developer.android.com/guide/practices/tablets-and-handsets.html?hl=zh-cn)指南。

#### 2.参考内容

https://developer.android.com/guide/components/fragments?hl=zh-cn

### 五、布局

#### 1.简介

布局定义用户界面的视觉结构，如[Activity](https://developer.android.com/guide/components/activities.html?hl=zh-cn)或[应用小部件](https://developer.android.com/guide/topics/appwidgets/index.html?hl=zh-cn)的 UI。您可以通过两种方式声明布局：

- **在 XML 中声明 UI 元素**。Android 提供了对应于 View 类及其子类的简明 XML 词汇，如用于小部件和布局的词汇；
- **运行时实例化布局元素**。您的应用可以通过编程创建 View 对象和 ViewGroup 对象（并操纵其属性）。

Android 框架让您可以灵活地使用以下一种或两种方法来声明和管理应用的 UI。例如，您可以在 XML 中声明应用的默认布局，包括将出现在布局中的屏幕元素及其属性。然后，您可以在应用中添加可在运行时修改屏幕对象（包括那些已在 XML 中声明的对象）状态的代码。

- 您还应尝试使用[层次结构查看器](https://developer.android.com/tools/debugging/debugging-ui.html?hl=zh-cn#hierarchyViewer)工具来调试布局—当您在模拟器或设备上进行调试时，它会显示布局属性值、绘制具有内边距/外边距指示符的线框以及完整渲染视图。
- 您可以利用 [layoutopt](https://developer.android.com/tools/debugging/debugging-ui.html?hl=zh-cn#layoutopt) 工具快速分析布局和层次结构中是否存在低效环节或其他问题。

在 XML 中声明 UI 的优点在于，您可以更好地将应用的外观与控制应用行为的代码隔离。您的 UI 描述位于应用代码外部，这意味着您在修改或调整描述时无需修改您的源代码并重新编译。例如，您可以创建适用于不同屏幕方向、不同设备屏幕尺寸和不同语言的 XML 布局。此外，在 XML 中声明布局还能更轻松地显示 UI 的结构，从而简化问题调试过程。因此，本文将侧重于示范如何在 XML 中声明布局。如果您对在运行时实例化 View 对象感兴趣，请参阅 `ViewGroup` 类和 `View` 类的参考资料。

一般而言，用于声明 UI 元素的 XML 词汇严格遵循类和方法的结构和命名方式，其中元素名称对应于类名称，属性名称对应于方法。实际上，这种对应关系往往非常直接，让您可以猜到对应于类方法的 XML 属性，或对应于给定 XML 元素的类。但请注意，并非所有词汇都完全相同。在某些情况下，在命名上略有差异。例如，EditText 元素具有的 `text` 属性对应的类方法是 `EditText.setText()`。

**提示**：如需了解有关不同布局类型的更多信息，请参阅[常见布局对象](https://developer.android.com/guide/topics/ui/layout-objects.html?hl=zh-cn)。

#### 2.参考内容

https://developer.android.com/guide/topics/ui/declaring-layout?hl=zh-cn

### 六、调色板

#### 1.简介

良好的视觉设计对于成功的应用程序至关重要，配色方案是设计的主要组成部分。 调色板库是一个支持库，可从图像中提取突出的颜色，以帮助您创建引人入胜的应用程序。 您可以使用调色板库来设计布局主题，并将自定义颜色应用于应用程序中的可视元素。 例如，您可以使用调色板根据其专辑封面为歌曲创建颜色协调的标题卡，或者在其背景图像更改时调整应用程序的工具栏颜色。 通过Palette对象，您可以访问位图图像中的颜色，同时还提供位图中的六种主要颜色配置文件，以帮助您了解设计选择。

#### 2.如何使用

#####2.1设置库

要使用调色板库，请将Android支持库安装或更新至24.0.0或更高版本，并按照添加支持库的说明将调色板库添加到应用程序开发项目中。

确保依赖项标识符中指定的版本与build.gradle文件中设置的应用程序的compileSdkVersion匹配:

```groovy
android {
  compileSdkVersion 28
  ...
}

dependencies {
  ...
  implementation 'com.android.support:palette-v7:28.0.0'
}
```

注意：如果您使用的是Gradle 3.0.0或更高版本的Android插件，请使用“implementation”关键字。如果您使用的是另一版本的Gradle Android插件，请使用“compile”关键字。

有关添加调色板依赖关系的更多信息，请阅读[支持库文档](https://developer.android.com/topic/libraries/support-library/features.html?hl=zh-cn#v7-palette)中的调色板功能。

#####2.2创建调色板

通过Palette对象，您可以访问图像中的主要颜色，以及覆盖文本的相应颜色。使用调色板设计应用程序的样式，并根据给定的源图像动态更改应用程序的颜色方案。

要创建调色板，首先从位图实例化Palette.Builder。然后，您可以使用Palette.Builder在生成之前自定义调色板。本节将介绍从位图图像生成和自定义调色板。

**生成Palette实例**

```kotlin
// Generate palette synchronously and return it
fun createPaletteSync(bitmap: Bitmap): Palette = Palette.from(bitmap).generate()

// Generate palette asynchronously and use it on a different
// thread using onGenerated()
fun createPaletteAsync(bitmap: Bitmap) {
    Palette.from(bitmap).generate { palette ->
        // Use generated instance
    }
}
```

**自定义你的调色板**

Palette.Builder允许您通过选择生成的调色板中的颜色数量，构建器用于生成调色板的图像区域以及调色板中允许的颜色来自定义调色板。例如，您可以过滤掉黑色或确保构建器仅使用图像的上半部分来生成调色板。

使用Palette.Builder类中的以下方法微调调色板的大小和颜色：

- [addFilter()](https://developer.android.com/reference/android/support/v7/graphics/Palette.Builder.html?hl=zh-cn#addFilter(android.support.v7.graphics.Palette.Filter))

此方法添加一个过滤器，指示生成的调色板中允许的颜色。传入自己的Palette.Filter并修改其isAllowed（）方法，以确定从调色板中过滤哪些颜色。

- [maximumColorCount()](https://developer.android.com/reference/android/support/v7/graphics/Palette.Builder.html?hl=zh-cn#maximumColorCount(int))

此方法设置调色板中的最大颜色数。 默认值为16，最佳值取决于源图像。 对于风景，最佳值范围为8-16，而带有人脸的图片通常具有介于24-32之间的值。 Palette.Builder需要更长的时间来生成具有更多颜色的调色板。

- [setRegion()](https://developer.android.com/reference/android/support/v7/graphics/Palette.Builder.html?hl=zh-cn#setRegion(int,%20int,%20int,%20int))

此方法指示构建器在创建调色板时使用的位图区域。您只能在从位图生成调色板时使用此方法，并且它不会影响原始图像。

- [addTarget()](https://developer.android.com/reference/android/support/v7/graphics/Palette.Builder.html?hl=zh-cn#addTarget(android.support.v7.graphics.Target))

此方法允许您通过向构建器添加目标颜色配置文件来执行自己的颜色匹配。如果默认目标不够，高级开发人员可以使用Target.Builder创建自己的目标。

**提取颜色配置文件**

基于材料设计的标准，调色板库从图像中提取常用的颜色配置文件。 每个配置文件由目标定义，并且从位图图像提取的颜色基于饱和度，亮度和总体（由颜色表示的位图中的像素数）对每个配置文件进行评分。 对于每个配置文件，具有最佳分数的颜色定义给定图像的颜色配置文件。

默认情况下，Palette对象包含给定图像的16种基色。 生成调色板时，可以使用Palette.Builder自定义其颜色数。 提取更多颜色可为每个颜色配置文件提供更多可能的匹配，但也会导致Palette.Builder在生成调色板时需要更长时间。

调色板库尝试提取以下六种颜色配置文件：

- Light Vibrant
- Vibrant
- Dark Vibrant
- Light Muted
- Muted
- Dark Muted

每个Palette的get <Profile> Color（）方法返回与该特定配置文件关联的调色板中的颜色，其中<Profile>由六个颜色配置文件之一的名称替换。例如，获取Dark Vibrant颜色配置文件的方法是getDarkVibrantColor（）。由于并非所有图像都包含所有颜色配置文件，因此您还必须提供要返回的默认颜色。

图1显示了get <Profile> Color（）方法中的照片及其相应的颜色配置文件。

![](https://developer.android.com/training/material/images/palette-library-color-profiles_2-1_2x.png?hl=zh-cn)

图1.给定调色板的默认最大颜色数（16）的示例图像及其提取的颜色配置文件。

**使用调色板创建配色方案**

Palette类还为每个颜色配置文件生成Palette.Swatch对象。 Palette.Swatch对象包含该配置文件的关联颜色，以及颜色的像素数。 样本具有其他方法，用于访问有关颜色配置文件的更多信息，例如HSL值和像素填充。 您可以使用样本来帮助使用getBodyTextColor（）和getTitleTextColor（）方法创建更全面的配色方案和应用主题。 这些方法返回适合在色板颜色上使用的颜色。

每个Palette的get <Profile> Swatch（）方法都返回与该特定配置文件关联的样本，其中<Profile>由六个颜色配置文件之一的名称替换。 虽然调色板的get <Profile> Swatch（）方法不需要默认值参数，但如果图像中不存在该特定配置文件，则它们将返回null。 因此，在使用之前，应检查样本是否为空。 例如，如果Vibrant样例不为null，则以下代码从调色板中获取标题文本颜色：

```kotlin
val vibrant = myPalette.vibrantSwatch
// In Kotlin, check for null before accessing properties on the vibrant swatch.
val titleColor = vibrant?.titleTextColor
```

要访问调色板中的所有颜色，getSwatches（）方法将返回从图像生成的所有样本的列表，包括标准的六种颜色配置文件。

以下代码片段使用上述代码片段中的方法同步生成调色板，获取其生动的样本，并更改工具栏的颜色以匹配位图图像。图2显示了生成的图像和工具栏。

```
// Set the background and text colors of a toolbar given a
// bitmap image to match
fun setToolbarColor(bitmap: Bitmap) {
    // Generate the palette and get the vibrant swatch
    val vibrantSwatch = createPaletteSync(bitmap).vibrantSwatch

    // Set the toolbar background and text colors.
    // Fall back to default colors if the vibrant swatch is not available.
    with(findViewById<Toolbar>(R.id.toolbar)) {
        setBackgroundColor(vibrantSwatch?.rgb ?:
                ContextCompat.getColor(context, R.color.default_title_background))
        setTitleTextColor(vibrantSwatch?.titleTextColor ?:
                ContextCompat.getColor(context, R.color.default_title_color))
    }
}
```

![](https://developer.android.com/training/material/images/palette-library-title-text-color_2-1_2x.png?hl=zh-cn)



#### 3.参考内容

https://developer.android.com/training/material/palette-colors?hl=zh-cn

https://www.jianshu.com/p/450c89bf9d38

### 七、TV

#### 1.简介

构建可让用户在大屏幕上体验沉浸式内容的应用。用户可以在主屏幕上发现内容推荐信息，并且 Leanback 库提供了各种 API，可以帮助您打造良好的远程控制使用体验。

#### 2.参考内容

https://developer.android.com/tv/?hl=zh-cn

http://f.talkingdata.com/?/article/195191

### 八、WearOS by Google

#### 1.简介

借助 Wear OS by Google 利用好每一分钟。智能手表可让您随时了解自己的健康状况、与关心的人保持联系，查看您所关注的信息，以及与 Google 助理互动；这些操作都能通过手表完成。

#### 2.参考内容

https://developer.android.com/training/wearables/apps/creating?hl=zh-cn

https://github.com/googlesamples/android-AlwaysOn

