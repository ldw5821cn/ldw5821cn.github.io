---
layout: post
title: Android Jetpack--Architecture（架构 下）
category: Jetpack
tags: [android, Jetpack]
keywords: Jetpack
excerpt: Android Jetpack--Architecture（架构 下）

---



## Android Jetpack--Architecture（架构）

[TOC]



### 五、DataBinding

#### 1.简介

数据绑定库是一个支持库，允许您使用声明性的格式而不是以编程方式将布局中的UI组件绑定到应用程序中的数据源。 布局通常在具有调用UI框架方法的代码的活动中定义。

####2.如何使用DataBinding

- 启用 DataBinding

数据绑定库提供了灵活性和广泛的兼容性 - 它是一个支持库，因此您可以将其用于运行Android 4.0（API级别14）或更高版本的设备。

建议在项目中使用最新的Gradle Android插件。但是，版本1.5.0及更高版本支持数据绑定。

```kotlin
android {
    ...
    dataBinding {
        enabled = true
    }
}
```

注意：如果app依赖了一个使用 Data Binding 的库，那么app module 的 build.gradle 也必须配置 Data Binding。

- 布局和绑定表达式

```xml
<?xml version="1.0" encoding="utf-8"?>
<layout xmlns:android="http://schemas.android.com/apk/res/android">
   <data>
       <variable name="user" type="com.example.User"/>
   </data>
   <LinearLayout
       android:orientation="vertical"
       android:layout_width="match_parent"
       android:layout_height="match_parent">
       <TextView android:layout_width="wrap_content"
           android:layout_height="wrap_content"
           android:text="@{user.firstName}"/>
       <TextView android:layout_width="wrap_content"
           android:layout_height="wrap_content"
           android:text="@{user.lastName}"/>
   </LinearLayout>
</layout>
```

- Data object

```kotlin
data class User(val firstName: String, val lastName: String)
```

- 绑定数据

为每个布局文件生成绑定类。 默认情况下，类的名称基于布局文件的名称，将其转换为Pascal大小写并向其添加Binding后缀。 上面的布局文件名是activity_main.xml，因此相应的生成类是ActivityMainBinding。 此类包含布局属性（例如，用户变量）到布局视图的所有绑定，并知道如何为绑定表达式指定值。创建绑定的推荐方法是在扩展布局时执行此操作，如 如下例所示：

```kotlin
override fun onCreate(savedInstanceState: Bundle?) {
    super.onCreate(savedInstanceState)

    val binding: ActivityMainBinding = DataBindingUtil.setContentView(
            this, R.layout.activity_main)

    binding.user = User("Test", "User")
}
```

在运行时，应用程序在UI中显示Test用户。或者，您可以使用LayoutInflater获取视图，如以下示例所示：

```kotlin
val binding: ActivityMainBinding = ActivityMainBinding.inflate(getLayoutInflater())
```

如果在Fragment，ListView或RecyclerView适配器中使用数据绑定项，则可能更喜欢使用绑定类或DataBindingUtil类的inflate（）方法，如以下代码示例所示：

```kotlin
val listItemBinding = ListItemBinding.inflate(layoutInflater, viewGroup, false)
// or
val listItemBinding = DataBindingUtil.inflate(layoutInflater, R.layout.list_item, viewGroup, false)
```

- 表达语言

表达式语言看起来很像托管代码中的表达式。您可以在表达式语言中使用以下运算符和关键字：

```
数学运算 + - / * %
字符串连接 +
逻辑运算 && ||
二进制运算 & | ^
一元运算符 + - ! ~
位移运算 >> >>> <<
比较运算 == > < >= <=
instanceof
组 ()
字面量 - 字符，字符串，数字，null
类型转换
函数调用
字段存取
数组存取 []
三元运算符 ?:
```

```xml
android:text="@{String.valueOf(index + 1)}"
android:visibility="@{age < 13 ? View.GONE : View.VISIBLE}"
android:transitionName='@{"image_" + id}'
```



#### 3.参考内容

https://www.jianshu.com/p/c1403af34932

https://developer.android.com/topic/libraries/data-binding/?hl=zh-cn#using_the_data_binding_library



### 六、LiveData

#### 1.简介

`LiveData`是一个用于持有数据并支持数据可被监听（观察）。和传统的观察者模式中的被观察者不一样，LiveData是一个`生命周期感知`组件，因此观察者可以指定某一个`LifeCycle`给LiveData，并对数据进行监听。

如果观察者指定`LifeCycle`处于`Started`或者`RESUMED`状态，LiveData会将观察者视为活动状态，并通知其数据的变化。

- LiveData 的优点：

  - 确保您的UI符合您的数据状态：

    LiveData遵循观察者模式。 生命周期状态更改时，LiveData会通知Observer对象。 您可以合并代码以更新这些Observer对象中的UI。 每次应用程序数据更改时，您的观察者都可以在每次更改时更新UI，而不是更新UI。

  - 没有内存泄漏：

    因为 [Observer](https://link.juejin.im/?target=https%3A%2F%2Fdeveloper.android.com%2Freference%2Fjava%2Futil%2FObserver.html) 被绑定到它们自己的 [Lifecycle](https://link.juejin.im/?target=https%3A%2F%2Fdeveloper.android.com%2Freference%2Fandroid%2Farch%2Flifecycle%2FLifecycle.html) 对象上，所以，当它们的 [Lifecycle](https://link.juejin.im/?target=https%3A%2F%2Fdeveloper.android.com%2Freference%2Fandroid%2Farch%2Flifecycle%2FLifecycle.html) 被销毁时，它们能自动的被清理。

  - 不会因为 activity 停止而崩溃：

    如果 [Observer](https://link.juejin.im/?target=https%3A%2F%2Fdeveloper.android.com%2Freference%2Fjava%2Futil%2FObserver.html) 的 [Lifecycle](https://link.juejin.im/?target=https%3A%2F%2Fdeveloper.android.com%2Freference%2Fandroid%2Farch%2Flifecycle%2FLifecycle.html) 处于闲置状态（例如：activity 在后台时），它们不会收到变更事件。

   - 不再手动管理生命周期

     你可能已经注意到，fragment 只是在需要的时候观察数据，不用担心被停止或者在停止之后启动观察。由于 fragment 在观察数据时提供了其 [Lifecycle](https://link.juejin.im/?target=https%3A%2F%2Fdeveloper.android.com%2Freference%2Fandroid%2Farch%2Flifecycle%2FLifecycle.html)，所以 [LiveData](https://link.juejin.im/?target=https%3A%2F%2Fdeveloper.android.com%2Freference%2Fandroid%2Farch%2Flifecycle%2FLiveData.html) 会自动管理这一切。

  - 始终保持最新数据：

    如果 [Lifecycle](https://link.juejin.im/?target=https%3A%2F%2Fdeveloper.android.com%2Freference%2Fandroid%2Farch%2Flifecycle%2FLifecycle.html) 重新启动（例如：activity 从后台返回到启动状态）将会收到最新的位置数据（除非还没有）。

  - 正确处理配置更改：

    如果 activity 或 fragment 由于配置更改（如：设备旋转）重新创建，将会立即收到最新的有效[位置](https://link.juejin.im/?target=https%3A%2F%2Fdeveloper.android.com%2Freference%2Fandroid%2Flocation%2FLocation.html)数据。

  - 资源共享：

    您可以使用单例模式扩展LiveData对象以包装系统服务，以便可以在应用程序中共享它们。 LiveData对象连接到系统服务一次，然后任何需要该资源的观察者都可以只观看LiveData对象。 有关更多信息，请参阅扩展LiveData。

#### 2.如何使用LiveData

- 引入LiveData

  ```groovy
  dependencies {
      def lifecycle_version = "2.0.0"
      // alternatively - just LiveData
      implementation "androidx.lifecycle:lifecycle-livedata:$lifecycle_version"
  }
  ```

  注：新的项目默认会引入lifecycle-livedata，不需要再build.gradle中配置。

- 创建 LiveData 对象

  使用`LiveData`对象包装数据，一般情况下我们会把数据存储在`ViewModel`中使用，然后把包装对象通过`getter`方法返回：

  ```kotlin
  class NameViewModel : ViewModel() {
  
      // Create a LiveData with a String
      val currentName: MutableLiveData<String> by lazy {
          MutableLiveData<String>()
      }
  
      // Rest of the ViewModel...
  }
  ```

- 观察LiveData对象

  多数情况下，App组件的`onCreate()`方法是开始观察LiveData对象适合的地方，原因如下：

  - 确保了系统不会再Activity和Fragment的`onResume`方法中冗余调用。
  - 确保了Activity和Fragment在活跃后立即显示数据，一旦应用程序组件处于`START`的状态，它就从它所观察到的`LiveData`对象接收最新的值。只有在设置要观察的`LiveData`对象时才会发生这种情况。

  一般来说，LiveData只在数据更改时才会提供更新，并且只提供给活动的观察者。一个例外的行为是，当观察者从非活动状态变为活动状态时，也会收到更新。此外，如果观察者第二次从非活动状态变为活动状态，那么它只会接收到自上次激活后值发生变化的更新。
  下面的代码展示了如果启动观察LiveData对象：

  ```kotlin
  class NameActivity : AppCompatActivity() {
      private lateinit var mModel: NameViewModel
      override fun onCreate(savedInstanceState: Bundle?) {
          super.onCreate(savedInstanceState)
          // Other code to setup the activity...
          // Get the ViewModel.
          mModel = ViewModelProviders.of(this).get(NameViewModel::class.java)
          // Create the observer which updates the UI.
          val nameObserver = Observer<String> { newName ->
              // Update the UI, in this case, a TextView.
              mNameTextView.text = newName
          }
          // Observe the LiveData, passing in this activity as the LifecycleOwner and the observer.
          mModel.currentName.observe(this, nameObserver)
      }
  }
  ```

  当`observe()`方法被调用后，`nameObserver`传递了进去，`onChanged()`方法被立即调用，提供以mCurrentName存储的最新值，如果LiveData对象没有设置mCurrentName的值，那么onChanged()就不会被调用。

- 更新LiveData 对象

  LiveData对象没有提供公开的方法去更新存储的值。不过[`MutableLiveData`](https://link.jianshu.com?t=https%3A%2F%2Fdeveloper.android.com%2Freference%2Fandroid%2Farch%2Flifecycle%2FMutableLiveData.html)这个类暴露了`setValue(T)`和`postValue(T)`公共方法可以变更在LiveData对象中存储的值。通常在[`ViewModel`](https://link.jianshu.com?t=https%3A%2F%2Fdeveloper.android.com%2Freference%2Fandroid%2Farch%2Flifecycle%2FViewModel.html)中使用[`MutableLiveData`](https://link.jianshu.com?t=https%3A%2F%2Fdeveloper.android.com%2Freference%2Fandroid%2Farch%2Flifecycle%2FMutableLiveData.html)，而[`ViewModel`](https://link.jianshu.com?t=https%3A%2F%2Fdeveloper.android.com%2Freference%2Fandroid%2Farch%2Flifecycle%2FViewModel.html)只向观察者公开不可变的LiveData对象。

  当你设置了观察者关系之后，你就可以更新LiveData的值，就像下面事例一样,当点击按钮后会触发所有的观察者：

  ```kotlin
  mButton.setOnClickListener {
      val anotherName = "John Doe"
      mModel.currentName.setValue(anotherName)
  }
  ```

  在这个示例中，调用`setValue(T)`方法会导致观察者将其onChanged()方法与John Doe的值联系起来。
   注意：你必须调用`setValue(T)`方法去从主线程中去更新LiveData的值，如果代码的执行是在子线程，你必须使用`postValue(T)`方法去更新LiveData对象的值。

- 在Room中使用LiveData

  [Room](https://link.jianshu.com?t=https%3A%2F%2Fdeveloper.android.com%2Ftraining%2Fdata-storage%2Froom%2Findex.html)是一个数据持久化库支持可观察查询并返回一个LiveData对象。
   当数据库更新时，[Room](https://link.jianshu.com?t=https%3A%2F%2Fdeveloper.android.com%2Ftraining%2Fdata-storage%2Froom%2Findex.html)会生成所有必需的代码来更新LiveData对象。生成的代码是异步运行在后台线程上的。此模式有助于将数据显示在UI中与存储在数据库中的数据同步。您可以阅读更多关于[Room](https://link.jianshu.com?t=https%3A%2F%2Fdeveloper.android.com%2Ftraining%2Fdata-storage%2Froom%2Findex.html)和DAOs在[Room persistent library guide](https://link.jianshu.com?t=https%3A%2F%2Fdeveloper.android.com%2Ftopic%2Flibraries%2Farchitecture%2Froom.html)。

- 扩展LiveData

  如果观察者的生命周期处于`STARTED`状态或`RESUMED`状态，则LiveData认为观察者处于活动状态。下面的代码展示如何拓展LiveData:

  ```kotlin
  class StockLiveData(symbol: String) : LiveData<BigDecimal>() {
      private val mStockManager = StockManager(symbol)
  
      private val mListener = { price: BigDecimal ->
          value = price
      }
  
      override fun onActive() {
          mStockManager.requestPriceUpdates(mListener)
      }
  
      override fun onInactive() {
          mStockManager.removeUpdates(mListener)
      }
  }
  ```

  此示例中价格监听器的实现包括以下重要方法：

  - onActive()
     这个方法当`LiveData`被一个观察者激活的时候调用。这意味着我们需要开启一个从这个设备位置更新的观察。
  - onInactive()
     这个方法当`LiveData`没有任何一个观察者的时候被调用。由于没有正在监听的观察者，也就没有理由一直连接到定位管理器服务。这很很重要的，因为保持着连接将占用大量的电池电源还没有任何好处。

  - setValue()
     调用这个方法会更新`LiveData`实例的值，并且迅速的通知观察者关于值的变化。
     我们可以像下面一样使用`StockLiveData`:

    ```kotlin
    override fun onActivityCreated(savedInstanceState: Bundle?) {
        super.onActivityCreated(savedInstanceState)
        val myPriceListener: LiveData<BigDecimal> = ...
        myPriceListener.observe(this, Observer<BigDecimal> { price: BigDecimal? ->
            // Update the UI.
        })
    }
    ```

    `observe()`方法传递fragment，这是`LifecycleOwner`的一个实例，作为第一个参数。这样做意味着该观察者绑定到与所有者关联的生命周期对象，意义如下:

    1. 如果生命周期对象不是处于活动状态，那么即使值发生变化，观察者也不会被调用。
    2. 在生命周期对象被销毁后，观察者将被自动删除。

    LiveData对象是生命周期的，这意味着您可以在多个活动、片段和服务之间共享它们。为了保持示例的简单性，可以将LiveData类作为一个单例来实现:

    ```kotlin
    class StockLiveData(symbol: String) : LiveData<BigDecimal>() {
        private val mStockManager: StockManager = StockManager(symbol)
    
        private val mListener = { price: BigDecimal ->
            value = price
        }
    
        override fun onActive() {
            mStockManager.requestPriceUpdates(mListener)
        }
    
        override fun onInactive() {
            mStockManager.removeUpdates(mListener)
        }
    
        companion object {
            private lateinit var sInstance: StockLiveData
    
            @MainThread
            fun get(symbol: String): StockLiveData {
                sInstance = if (::sInstance.isInitialized) sInstance else StockLiveData(symbol)
                return sInstance
            }
        }
    }
    ```

    你可以在Fragment中使用它：

    ```kotlin
    class MyFragment : Fragment() {
    
        override fun onActivityCreated(savedInstanceState: Bundle?) {
            StockLiveData.get(symbol).observe(this, Observer<BigDecimal> { price: BigDecimal? ->
                // Update the UI.
            })
        }
    ```

    可能会有多个`fragment`和`activity`来观察我们的`MyPriceListener`实例，`LiveData`可以完美的管理了它们，只有当他们之中有任何一个是可见的时候，就会将其连接到系统服务。

- 转换 LiveData

  某些时候你或许会想在派发给观察员之前改变`LiveData`的值或者你需要返回一个不同的基于另外一个值的`LiveData`的实例。
  `Lifecycle`包提供了一个`Transformations`类包含了一些可用的方法来完成这些操作。

  ```kotlin
  val userLiveData: LiveData<User> = UserLiveData()
  val userName: LiveData<String> = Transformations.map(userLiveData) {
      user -> "${user.name} ${user.lastName}"
  }
  ```

  与map（）类似，将函数应用于存储在LiveData对象中的值，并将结果解包并调度到下游。传递给switchMap（）的函数必须返回一个LiveData对象，如以下示例所示：

  ```kotlin
  private fun getUser(id: String): LiveData<User> {
    ...
  }
  val userId: LiveData<String> = ...
  val user = Transformations.switchMap(userId) { id -> getUser(id) }
  ```

  使用这些转换允许在链中传递观察者生命周期信息,这样，除非观察者观察返回`LiveData`，否则这些转换不会被计算。这种延迟计算的性质允许隐式地传递与生命周期相关的行为，而不需要添加显式的调用或依赖项。
   当你认为你需要有一个生命周期的对象在ViewModel中时，`Transformations`可能是解决方案。
   下面的例子，我们假设我们有一个UI界面，用户放入一个地址且收到这个地址的邮政编码，一个单纯的`ViewModel`可能会像这样：

  ```kotlin
  class MyViewModel(private val repository: PostalCodeRepository) : ViewModel() {
  
      private fun getPostalCode(address: String): LiveData<String> {
          // DON'T DO THIS
          return repository.getPostCode(address)
      }
  }
  ```

  如果是这样来实现，UI上将每次调用`getPostalCode()`方法的时候需要注销上一个`LiveData`并且重新注册一个新的实例，此外，如果UI界面被重新创建，将会触发另外的`repository.getPostCode()`方法而不是以前的调用的那个对象结果。
   你可以使用让地址输入作为邮政编码信息的转换来替代这种方法的实现。

  ```kotlin
  class MyViewModel(private val repository: PostalCodeRepository) : ViewModel() {
      private val addressInput = MutableLiveData<String>()
      private val postalCode: LiveData<String> =
              Transformations.switchMap(addressInput) { address -> repository.getPostCode(address) }
  
  
      private fun setInput(address: String) {
          addressInput.value = address
      }
  }
  ```

  在这种情况下，postalCode字段是public和final，因为该字段永远不会更改。 postalCode字段定义为addressInput的转换，这意味着在addressInput更改时调用repository.getPostCode（）方法。 如果存在活动的观察者，则如果在调用repository.getPostCode（）时没有活动的观察者，则在添加观察者之前不进行任何计算。 此机制允许较低级别的应用程序创建按需延迟计算的LiveData对象。 ViewModel对象可以轻松获取对LiveData对象的引用，然后在它们之上定义转换规则。

- 创建新的转换

  在你的应用中或许会用很多不同的转变特性，但是他们不是被默认提供的。你可以使用[MediatorLiveData](https://link.jianshu.com?t=https%3A%2F%2Fdeveloper.android.com%2Freference%2Fandroid%2Farch%2Flifecycle%2FMediatorLiveData.html)类来实现你自己的转变,这是专门为了正确地监听其他`Livedata`实例和过程事件发出的。[MediatorLiveData](https://link.jianshu.com?t=https%3A%2F%2Fdeveloper.android.com%2Freference%2Fandroid%2Farch%2Flifecycle%2FMediatorLiveData.html)会将它的活动/不活动状态正确地传播到源[LiveData](https://link.jianshu.com?t=https%3A%2F%2Fdeveloper.android.com%2Ftopic%2Flibraries%2Farchitecture%2Flivedata.html%23transformations_of_livedata)中。您可以查看[Transformations ](https://link.jianshu.com?t=https%3A%2F%2Fdeveloper.android.com%2Freference%2Fandroid%2Farch%2Flifecycle%2FTransformations.html)类的实现以获得详细信息。


#### 3.参考内容

​	https://developer.android.com/topic/libraries/architecture/livedata?hl=zh-cn#transform_livedata

​	https://www.jianshu.com/p/13ed71d42b4e

​	https://juejin.im/post/5937e402a0bb9f005808d00e



### 七、Paging

#### 1.简介

Paging 使您的应用程序配合RecyclerView更容易从数据源中高效优雅地加载所需的数据，不会因为数据库数据量大而造成查询时间过长。

在我们的实际项目中有大量的数据，但是我们常常只需要向用户展示一小部分信息。例如，一个应用中可能会有几千个item但是我们常常只需要显示十几个或者几十个。我们处理不当加载数据时可能会造成APP崩溃。如果数据被存储或与远程数据库同步,这也会减慢应用程序的速度，影响用户的体验。

Paging库能够提供给你的app观察和显示这个数据合理的子集。这个功能库有几个优点：

1. 数据请求占用比较少的网络带宽和系统资源。
2. 在数据更新和刷新期间，App也能够快速响应用户输入。
   如果你的app已经包含了分页处理的逻辑，我们已经提供了更新现有app的指导。

#### 2.如何使用Paging

- 引入Paging

```groovy
dependencies {
    def paging_version = "2.1.0-rc01"

    implementation "androidx.paging:paging-runtime:$paging_version" // use -ktx for Kotlin

    // alternatively - without Android dependencies for testing
    testImplementation "androidx.paging:paging-common:$paging_version" // use -ktx for Kotlin

    // optional - RxJava support
    implementation "androidx.paging:paging-rxjava2:$paging_version" // use -ktx for Kotlin
}
```

- Library 架构

Paging Library的关键组件是PagedList类，它是一个异步加载应用程序数据块或页面的集合。此类充当应用程序架构的其他部分之间的中介：

**Data**

PagedList的每个实例都从其DataSource加载应用程序数据的最新快照。数据从应用程序的后端或数据库流入PagedList对象。

分页库支持各种应用程序体系结构，包括独立数据库和与后端服务器通信的数据库

有关详细信息，请参[阅数据组件和注意事项](https://developer.android.google.cn/topic/libraries/architecture/paging/data)。

**UI**

PagedList类与PagedListAdapter一起使用，可以将项目加载到RecyclerView中。这些类一起工作以在加载内容时获取和显示内容，预取视图内容并动画内容更改。

要了解更多信息，请参阅[UI组件和注意事项](https://developer.android.google.cn/topic/libraries/architecture/paging/ui)。

分页库实现了观察者模式。实际上，这些组件会创建一个`LiveData<PagedList>`数据对象（或者Rxjava2基础类的可观察数据对象）你的app UI就可以将这些数据从PagingList中展示，同时具有可控的生命周期。

- 支持不同的数据架构

分页库支持三种数据获取：仅从网络获取；仅从本地数据库获取；从网络和数据库同时获取。

我们提供了用于不同数据架构的推荐模式的示例。要查看它们，请参阅GitHub上的[PagingWithNetwork示例](https://github.com/googlesamples/android-architecture-components/tree/master/PagingWithNetworkSample)。

- 网络和数据库

```kotlin
class ConcertViewModel {
    fun search(query: String): ConcertSearchResult {
        val boundaryCallback =
                ConcertBoundaryCallback(query, myService, myCache)
        // Use a LiveData object to communicate your network's state back
        // to your app's UI, as in the following example. Note that error
        // handling isn't shown in this snippet.
        // val loadingState: LiveData<MyNetworkState> =
        //        boundaryCallback.loadingState
    }
}

class ConcertBoundaryCallback(
        private val query: String,
        private val service: MyService,
        private val cache: MyLocalCache
) : PagedList.BoundaryCallback<Concert>() {
    // Requests initial data from the network, replacing all content currently
    // in the database.
    override fun onZeroItemsLoaded() {
        requestAndReplaceInitialData(query)
    }

    // Requests additional data from the network, appending the results to the
    // end of the database's existing data.
    override fun onItemAtEndLoaded(itemAtEnd: Concert) {
        requestAndAppendData(query, itemAtEnd.key)
    }
}
```

要查看分页库解决方案如何从网络和数据库中获取数据的更多扩展示例，请导航到GitHub上的[Paging codelab](https://codelabs.developers.google.com/codelabs/android-paging/index.html)或[PagingWithNetwork示例](https://github.com/googlesamples/android-architecture-components/tree/master/PagingWithNetworkSample)。

- 处理网络错误

  当使用网络获取或分页您正在使用分页库显示的数据时，重要的是不要将网络视为“可用”或“不可用”，因为许多连接是间歇性的或片状的:

  - 特定服务器可能无法响应网络请求。
  - 设备可能连接到缓慢或弱的网络。

  相反，您的应用应检查每个失败请求，并在网络不可用的情况下尽可能优雅地恢复。 例如，您可以提供“重试”按钮，供用户选择数据刷新步骤是否不起作用。 如果在数据分页步骤期间发生错误，则最好自动重试分页请求。

- 更新现有的应用

  如果您的应用已经消耗了数据库或后端源中的数据，则可以直接升级到分页库提供的功能。本节介绍如何升级具有通用现有设计的应用程序。




#### 3.参考内容

https://www.jianshu.com/p/a05943b8a9c1

https://juejin.im/entry/5b0d3b0251882532321468ff

https://developer.android.com/topic/libraries/architecture/paging/?hl=zh-cn

### 八、ViewModel

#### 1.简介

该[`ViewModel`](https://developer.android.google.cn/reference/android/arch/lifecycle/ViewModel.html)课程旨在存储和管理用户界面相关的数据生命周期中的意识的方式。在 [`ViewModel`](https://developer.android.google.cn/reference/android/arch/lifecycle/ViewModel.html)类允许数据生存如屏幕旋转配置更改。

Android框架管理UI控制器的生命周期，例如活动和片段。框架可以决定销毁或重新创建UI控制器以响应完全不受您控制的某些用户动作或设备事件。

如果系统销毁或重新创建UI控制器，则存储在其中的任何瞬态UI相关数据都将丢失。例如，您的应用可能会在其中一项活动中包含用户列表。为配置更改重新创建活动时，新活动必须重新获取用户列表。对于简单数据，活动可以使用该`onSaveInstanceState()`方法并从包中恢复其数据 `onCreate()`，但此方法仅适用于可以序列化然后反序列化的少量数据，而不适用于潜在的大量数据，如用户列表或位图。

另一个问题是UI控制器经常需要进行可能需要一些时间才能返回的异步调用。UI控制器需要管理这些调用并确保系统在销毁后清理它们以避免潜在的内存泄漏。此管理需要大量维护，并且在为配置更改重新创建对象的情况下，这会浪费资源，因为对象可能必须重新发出已经进行的调用。

诸如活动和片段之类的UI控制器主要用于显示UI数据，对用户操作作出反应或处理操作系统通信，例如许可请求。要求UI控制器也负责从数据库或网络加载数据，这会给类增加膨胀。为UI控制器分配过多的责任可能导致单个类尝试自己处理应用程序的所有工作，而不是将工作委托给其他类。以这种方式为UI控制器分配过多的责任也会使测试变得更加困难。

将视图数据所有权与UI控制器逻辑分离起来更容易，更有效。

#### 2.如何使用ViewModel

- 引入VIewModel

```groovy
dependencies {
    def lifecycle_version = "2.0.0"
    // alternatively - just ViewModel
    implementation "androidx.lifecycle:lifecycle-viewmodel:$lifecycle_version" // use -ktx for Kotlin
 
}
```

- 实现 VIewModel

Architecture Components为[`ViewModel`](https://developer.android.google.cn/reference/android/arch/lifecycle/ViewModel.html)UI控制器提供 帮助程序类，负责为UI准备数据。 [`ViewModel`](https://developer.android.google.cn/reference/android/arch/lifecycle/ViewModel.html)在配置更改期间会自动保留对象，以便它们保存的数据可立即用于下一个活动或片段实例。例如，如果您需要在应用中显示用户列表，请确保分配责任以获取并保留用户列表 [`ViewModel`](https://developer.android.google.cn/reference/android/arch/lifecycle/ViewModel.html)，而不是活动或片段，如以下示例代码所示：

```kotlin
class MyViewModel : ViewModel() {
    private lateinit var users: MutableLiveData<List<User>>

    fun getUsers(): LiveData<List<User>> {
        if (!::users.isInitialized) {
            users = MutableLiveData()
            loadUsers()
        }
        return users
    }

    private fun loadUsers() {
        // Do an asynchronous operation to fetch users.
    }
}
```

然后，您可以从活动中访问列表，如下所示：

```kotlin
class MyActivity : AppCompatActivity() {

    override fun onCreate(savedInstanceState: Bundle?) {
        // Create a ViewModel the first time the system calls an activity's onCreate() method.
        // Re-created activities receive the same MyViewModel instance created by the first activity.

        val model = ViewModelProviders.of(this).get(MyViewModel::class.java)
        model.getUsers().observe(this, Observer<List<User>>{ users ->
            // update UI
        })
    }
}
```

如果重新创建活动，它将接收由第一个活动创建的相同MyViewModel实例。当所有者活动完成时，框架调用ViewModel对象的onCleared（）方法，以便它可以清理资源。

注意：ViewModel绝不能引用视图，生命周期或任何可能包含对活动上下文的引用的类。

[`ViewModel`](https://developer.android.google.cn/reference/android/arch/lifecycle/ViewModel.html)对象被设计为比视图的特定实例更长久 [`LifecycleOwners`](https://developer.android.google.cn/reference/android/arch/lifecycle/LifecycleOwner.html)。此设计还意味着您可以编写测试以[`ViewModel`](https://developer.android.google.cn/reference/android/arch/lifecycle/ViewModel.html)更轻松地覆盖， 因为它不了解视图和 [`Lifecycle`](https://developer.android.google.cn/reference/android/arch/lifecycle/Lifecycle.html)对象。 [`ViewModel`](https://developer.android.google.cn/reference/android/arch/lifecycle/ViewModel.html) 对象可以包含 [`LifecycleObservers`](https://developer.android.google.cn/reference/android/arch/lifecycle/LifecycleObserver.html)，例如[`LiveData`](https://developer.android.google.cn/reference/android/arch/lifecycle/LiveData.html)对象。但是， [`ViewModel`](https://developer.android.google.cn/reference/android/arch/lifecycle/ViewModel.html)对象必须永远不会观察到生命周期感知的可观察[`LiveData`](https://developer.android.google.cn/reference/android/arch/lifecycle/LiveData.html)对象（例如对象）的更改。如果 [`ViewModel`](https://developer.android.google.cn/reference/android/arch/lifecycle/ViewModel.html)需要 `Application`上下文，例如查找系统服务，它可以扩展 [`AndroidViewModel`](https://developer.android.google.cn/reference/android/arch/lifecycle/AndroidViewModel.html) 类并具有在构造函数中接收`Application` 的构造函数，因为`Application`类扩展`Context`。

- ViewModel的生命周期

[`ViewModel`](https://developer.android.google.cn/reference/android/arch/lifecycle/ViewModel.html)对象的范围是[`Lifecycle`](https://developer.android.google.cn/reference/android/arch/lifecycle/Lifecycle.html)传递给 [`ViewModelProvider`](https://developer.android.google.cn/reference/android/arch/lifecycle/ViewModelProvider.html) 获取时的 [`ViewModel`](https://developer.android.google.cn/reference/android/arch/lifecycle/ViewModel.html)。[`ViewModel`](https://developer.android.google.cn/reference/android/arch/lifecycle/ViewModel.html)内存中的 遗骸直到[`Lifecycle`](https://developer.android.google.cn/reference/android/arch/lifecycle/Lifecycle.html) 它的范围永久消失：在活动的情况下，当它完成时，在片段的情况下，当它被分离时。

图1显示了活动经历轮换然后结束时的各种生命周期状态。该图还显示[`ViewModel`](https://developer.android.google.cn/reference/android/arch/lifecycle/ViewModel.html)了相关活动生命周期的 下一个生命周期。此特定图表说明了活动的状态。相同的基本状态适用于片段的生命周期。

![è¯´æViewModelä½ä¸ºæ´"å¨æ´æ¹ç¶æççå½å¨æã](https://developer.android.google.cn/images/topic/libraries/architecture/viewmodel-lifecycle.png)

您通常[`ViewModel`](https://developer.android.google.cn/reference/android/arch/lifecycle/ViewModel.html)在系统第一次调用活动对象的`onCreate()`方法时 请求 。系统可以`onCreate()`在活动的整个生命周期中多次调用 ，例如在旋转设备屏幕时。在 [`ViewModel`](https://developer.android.google.cn/reference/android/arch/lifecycle/ViewModel.html)存在从当你第一次请求 [`ViewModel`](https://developer.android.google.cn/reference/android/arch/lifecycle/ViewModel.html)，直到活动结束和销毁。

- 在片段之间共享数据

活动中的两个或多个片段需要相互通信是很常见的。想象一下主从细节片段的常见情况，其中有一个片段，用户从列表中选择一个项目，另一个片段显示所选项目的内容。这种情况从来都不是微不足道的，因为两个片段都需要定义一些接口描述，并且所有者活动必须将两者绑定在一起。此外，两个片段都必须处理尚未创建或可见其他片段的场景。

这个常见的痛点可以通过使用[`ViewModel`](https://developer.android.google.cn/reference/android/arch/lifecycle/ViewModel.html)对象来解决 。这些片段可以共享 [`ViewModel`](https://developer.android.google.cn/reference/android/arch/lifecycle/ViewModel.html)使用其活动范围来处理此通信，如以下示例代码所示：

```kotlin
class SharedViewModel : ViewModel() {
    val selected = MutableLiveData<Item>()

    fun select(item: Item) {
        selected.value = item
    }
}

class MasterFragment : Fragment() {

    private lateinit var itemSelector: Selector

    private lateinit var model: SharedViewModel

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        model = activity?.run {
            ViewModelProviders.of(this).get(SharedViewModel::class.java)
        } ?: throw Exception("Invalid Activity")
        itemSelector.setOnClickListener { item ->
            // Update the UI
        }
    }
}

class DetailFragment : Fragment() {

    private lateinit var model: SharedViewModel

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        model = activity?.run {
            ViewModelProviders.of(this).get(SharedViewModel::class.java)
        } ?: throw Exception("Invalid Activity")
        model.selected.observe(this, Observer<Item> { item ->
            // Update the UI
        })
    }
}
```

请注意，两个片段都会检索包含它们的活动。这样，当每个片段都获得时 [`ViewModelProvider`](https://developer.android.google.cn/reference/android/arch/lifecycle/ViewModelProvider.html)，它们会收到相同的`SharedViewModel`实例，该实例的范围限定为此活动。

​	这种方法具有以下优点：

  - 

      - 活动不需要做任何事情，也不需要了解这种沟通。
      - 除了`SharedViewModel` 合同之外，碎片不需要彼此了解。如果其中一个碎片消失，另一个碎片继续照常工作。

      - 每个片段都有自己的生命周期，不受另一个片段的生命周期的影响。如果一个片段替换另一个片段，则UI继续工作而没有任何问题。

- 用ViewModel替换加载器

类似的Loader类`CursorLoader`经常用于将应用程序UI中的数据与数据库保持同步。您可以使用 [`ViewModel`](https://developer.android.google.cn/reference/android/arch/lifecycle/ViewModel.html)其他几个类来替换加载器。使用a [`ViewModel`](https://developer.android.google.cn/reference/android/arch/lifecycle/ViewModel.html)将UI控制器与数据加载操作分开，这意味着类之间的强引用较少。

在使用加载器的一种常见方法中，应用程序可能使用a `CursorLoader`来观察数据库的内容。当数据库中的值发生更改时，加载程序会自动触发重新加载数据并更新UI：

![](https://developer.android.google.cn/images/topic/libraries/architecture/viewmodel-loader.png)

**图2.使用**加载器加载数据

[`ViewModel`](https://developer.android.google.cn/reference/android/arch/lifecycle/ViewModel.html)与 [Room](https://developer.android.google.cn/topic/libraries/architecture/room.html)和 [LiveData一起使用](https://developer.android.google.cn/topic/libraries/architecture/livedata.html)来替换加载器。在[`ViewModel`](https://developer.android.google.cn/reference/android/arch/lifecycle/ViewModel.html)该数据存续的设备配置改变确保。 [房间](https://developer.android.google.cn/topic/libraries/architecture/room.html)通知您[`LiveData`](https://developer.android.google.cn/reference/android/arch/lifecycle/LiveData.html)数据库更改的时间，然后[LiveData](https://developer.android.google.cn/topic/libraries/architecture/livedata.html)会使用修改后的数据更新您的UI。

![](https://developer.android.google.cn/images/topic/libraries/architecture/viewmodel-replace-loader.png)

**图3.**使用ViewModel加载数据

#### 3.参考内容

https://developer.android.google.cn/topic/libraries/architecture/viewmodel

https://www.jianshu.com/p/a05943b8a9c1

https://www.jianshu.com/p/59adff59ed29
