---
layout: post
title: Android Jetpack --Architecture（架构 上）
category: JetPack
tags: [android,Jetpack]
keywords: Android,JetPack
excerpt: Android Jetpack --Architecture（架构）

---

## Android Jetpack --Architecture（架构）

[TOC]

###一、Lifecycles 

#### 1.简介

生命周期感知型组件可执行操作来响应另一个组件（如 Activity 和 Fragment）的生命周期状态的变化。这些组件有助于生成更有条理且往往更精简的代码，此类代码更易于维护。

一种常见的模式是在 Activity 和 Fragment 的生命周期方法中实现依赖组件的操作。但是，这种模式会导致代码条理性很差而且会扩散错误。通过使用生命周期感知型组件，您可以将依赖组件的代码从生命周期方法移入组件本身中。

[`android.arch.lifecycle`](https://developer.android.com/reference/android/arch/lifecycle/package-summary.html?hl=zh-cn) 软件包提供了可用于构建生命周期感知型组件的类和接口 - 这些组件可以根据 Activity 或 Fragment 的当前生命周期状态自动调整其行为。

[`Lifecycle`](https://developer.android.com/reference/android/arch/lifecycle/Lifecycle.html?hl=zh-cn) 是一个类，用于存储有关组件（如 Activity 或 Fragment）的生命周期状态的信息，并允许其他对象观察此状态。

[`Lifecycle`](https://developer.android.com/reference/android/arch/lifecycle/Lifecycle.html?hl=zh-cn) 使用两种主要枚举跟踪其关联组件的生命周期状态：

- 事件

从框架和 [`Lifecycle`](https://developer.android.com/reference/android/arch/lifecycle/Lifecycle.html?hl=zh-cn) 类分派的生命周期事件。这些事件映射到 Activity 和 Fragment 中的回调事件。

- 状态

由 [`Lifecycle`](https://developer.android.com/reference/android/arch/lifecycle/Lifecycle.html?hl=zh-cn) 对象跟踪的组件的当前状态。

![img](https://developer.android.com/images/topic/libraries/architecture/lifecycle-states.png)



####2.如何实现 LifecycleObserver

引入 [`android.arch.lifecycle`](https://developer.android.com/reference/android/arch/lifecycle/package-summary.html?hl=zh-cn) 软件包提供的类和接口。

注：Android Studio 新建Project 时，默认会导入` android.arch.lifecycle:runtime:x.x.x`,`android.arch.lifecycle:common:x.x.x` 等相关的包。

- 使用 `Java 7`  实现 `LifecycleObserver`

  ```java
  class TDLifecycleObserver : LifecycleObserver {
      @OnLifecycleEvent(Lifecycle.Event.ON_RESUME)
      fun onResume() {
          Log.i("TAG", "onResume..............")
      }
      
      @OnLifecycleEvent(Lifecycle.Event.ON_PAUSE)
      fun onPause() {
          Log.i("TAG", "onPause..............")
      }
      
      @OnLifecycleEvent(Lifecycle.Event.ON_STOP)
      fun onStop() {
          Log.i("TAG", "onStop..............")
      }
  
      @OnLifecycleEvent(Lifecycle.Event.ON_START)
      fun onStart() {
          Log.i("TAG", "onStart..............")
      }
  
      @OnLifecycleEvent(Lifecycle.Event.ON_CREATE)
      fun onCreate() {
          Log.i("TAG", "onCreate..............")
      }
  
      @OnLifecycleEvent(Lifecycle.Event.ON_DESTROY)
      fun onDestroy() {
          Log.i("TAG", "onDestroy..............")
      }
  
      @OnLifecycleEvent(Lifecycle.Event.ON_ANY)
      fun onAny() {
          Log.i("TAG", "onAny..............")
      }
  }
  ```

- 使用 `Java 8 ` 实现 `DefaultLifecycleObserver`

  注：当使用 `Java 8` 时，建议使用新`API`

  ```java
  class TD8LifecyleObserver:DefaultLifecycleObserver {
      override fun onCreate(owner: LifecycleOwner) {
          Log.i("TAG", "onCreate..............")
      }
  
      override fun onDestroy(owner: LifecycleOwner) {
          Log.i("TAG", "onDestroy..............")
      }
  
      override fun onPause(owner: LifecycleOwner) {
          Log.i("TAG", "onPause..............")
      }
  
      override fun onResume(owner: LifecycleOwner) {
          Log.i("TAG", "onResume..............")
      }
  
      override fun onStart(owner: LifecycleOwner) {
          Log.i("TAG", "onStart..............")
      }
  
      override fun onStop(owner: LifecycleOwner) {
          Log.i("TAG", "onStop..............")
      }
  }
  ```



  #### 3.使用 LifecycleObserver

  在 Activity 和 Fragment 中 添加注册：

  ```java
  class MainActivity : AppCompatActivity() {
      override fun onCreate(savedInstanceState: Bundle?) {
          super.onCreate(savedInstanceState)
          setContentView(R.layout.main_activity)
          lifecycle.addObserver(TDLifecycleObserver()) // 注册Java 7 版本
          lifecycle.addObserver(TD8LifecyleObserver()) // 注册Java 8 版本
      }
  }
  ```

  注：Support Library 26.1.0 及更高版本中的 Fragment 和 Activity 已实现 [`LifecycleOwner`](https://developer.android.com/reference/android/arch/lifecycle/LifecycleOwner.html?hl=zh-cn) 接口。

  如果您有一个自定义类并希望使其成为 [`LifecycleOwner`](https://developer.android.com/reference/android/arch/lifecycle/LifecycleOwner.html?hl=zh-cn)，您可以使用 [LifecycleRegistry](https://developer.android.com/reference/android/arch/lifecycle/LifecycleRegistry.html?hl=zh-cn) 类，但需要将事件转发到该类，如以下代码示例中所示：

  ```java
  public class MyActivity extends Activity implements LifecycleOwner {
      private LifecycleRegistry mLifecycleRegistry;
  
      @Override
      protected void onCreate(Bundle savedInstanceState) {
          super.onCreate(savedInstanceState);
  
          mLifecycleRegistry = new LifecycleRegistry(this);
          mLifecycleRegistry.markState(Lifecycle.State.CREATED);
      }
  
      @Override
      public void onStart() {
          super.onStart();
          mLifecycleRegistry.markState(Lifecycle.State.STARTED);
      }
  
      @NonNull
      @Override
      public Lifecycle getLifecycle() {
          return mLifecycleRegistry;
      }
  }
  ```

#### 4.对比`ActivityLifecycleCallbacks`

| 对比内容                 | ActivityLifecycleCallbacks | LifecycleObserver              | DefaultLifecycleObserver       |
| ------------------------ | -------------------------- | ------------------------------ | ------------------------------ |
| 注册方式                 | 在Application中注册        | 在每个Activity、Fragment中注册 | 在每个Activity、Fragment中注册 |
| 执行顺序                 | 优先执行                   | 按注册顺序执行                 | 按注册顺序执行                 |
| 是否可以获取当前Activity | 是                         | 否                             | 是                             |

注：多种监控方式，执行互不影响。优先执行 `ActivityLifecycleCallbacks` , `LifecycleObserver` 根据注册的顺序调用。

测试 Log：

```java
I/TAG: onActivityCreated..............MainActivity     // ActivityLifecycleCallbacks
I/TAG: TDLifecycleObserver onCreate..............	   // LifecycleObserver
I/TAG: TDLifecycleObserver onAny..............		   // LifecycleObserver
I/TAG: TD8LifecyleObserver onCreate..............MainActivity // DefaultLifecycleObserver
I/TAG: onActivityStarted..............MainActivity
I/TAG: TDLifecycleObserver onStart..............
I/TAG: TDLifecycleObserver onAny..............
I/TAG: TD8LifecyleObserver onStart..............MainActivity
I/TAG: onActivityResumed..............MainActivity
I/TAG: TDLifecycleObserver onResume..............
I/TAG: TDLifecycleObserver onAny..............
I/TAG: TD8LifecyleObserver onResume..............MainActivity

```



#### 5.参考内容

https://www.jianshu.com/p/2c9bcbf092bc

https://developer.android.com/jetpack/arch/lifecycle?hl=zh-cn#lc

https://codelabs.developers.google.com/codelabs/android-lifecycles/index.html?index

### 二、Navigation

#### 1.简介

导航是应用程序设计的重要组成部分。通过导航，您可以设计允许用户在应用内的不同内容区域中移动，移入和移出的交互。

导航架构组件可帮助您在应用程序中实现常见但复杂的导航要求，使您能够更轻松地为用户提供一致且可预测的体验。

导航手柄在您的应用目的地之间导航 - 即，您的应用中用户可以导航的任何位置。 虽然目标通常是代表特定屏幕的Fragment，但导航架构组件支持下面列出的其他目标类型：

- Activities
- Navigation graphs and subgraphs - when the destination is a graph or subgraph, users navigate to the start destination of that graph or subgraph
- [Custom destination types](https://developer.android.com/topic/libraries/architecture/navigation/navigation-add-new.html?hl=zh-cn)

这些目的地通过操作连接，一组目的地和连接操作包含应用程序的导航图

导航架构组件提供了许多其他好处，包括：

- Handling Fragment transactions
- Handling Up and Back actions correctly by default
- Providing standardized resources for animations and transitions
- Treating deep linking as a first-class operation
- Including Navigation UI patterns, such as navigation drawers and bottom navigation, with minimal additional work
- Providing type safety when passing information while navigating
- Visualizing and editing navigation from with Android Studio's [Navigation Editor](https://developer.android.google.cn/topic/libraries/architecture/navigation/new-navigation-editor)

注：如果要将导航架构组件与Android Studio一起使用，则必须使用Android Studio 3.2 Canary 14或更高版本。



#### 2.如何使用Navigation

- 引入依赖

  ```java
  dependencies {
      implementation "android.arch.navigation:navigation-fragment:1.0.0-alpha07"
      implementation "android.arch.navigation:navigation-ui:1.0.0-alpha07"
  } 
  ```

- 创建 Navigation

  前提：**Enable Navigation on 3.2**

  1. Click **File > Settings** (**Android Studio > Preferences** on Mac)
  2. Select the **Experimental** category in the left pane
  3. Check **Enable Navigation Editor**
  4. **Restart** Android Studio

  ![img](https://codelabs.developers.google.com/codelabs/android-navigation/img/a03dbbab6e6975a4.png)

  1.在res目录右键`New`->`New Resource File`，弹出`New Resource File`的对话框

  2.填写`File name`比如：nav_graph

  3.Resource type`选择`Navigation

  4.点击OK

  ![image-20181210172655978](/Users/liudawei/Library/Application Support/typora-user-images/image-20181210172655978.png)



  然后会在res下生成一个navigation的目录，里面有我们刚才创建的nav_graph.xml文件，文件内容如下:

  ```xml
  <?xml version="1.0" encoding="utf-8"?>
  <navigation xmlns:android="http://schemas.android.com/apk/res/android"
              xmlns:app="http://schemas.android.com/apk/res-auto"
              xmlns:tools="http://schemas.android.com/tools" android:id="@+id/nav_graph"
              app:startDestination="@id/mainFragment">
  
      <fragment android:id="@+id/mainFragment"
                android:name="com.talkingdata.jetpackdemo.MainFragment"
                android:label="main_fragment"
                tools:layout="@layout/main_fragment">
          <action android:id="@+id/action_mainFragment_to_secondFragment"
                  app:destination="@id/secondFragment"/>
      </fragment>
      <fragment android:id="@+id/secondFragment"
                android:name="com.talkingdata.jetpackdemo.SecondFragment"
                android:label="second_fragment"
                tools:layout="@layout/second_fragment">
          <action android:id="@+id/action_secondFragment_to_mainFragment"
                  app:destination="@id/mainFragment"/>
      </fragment>
  </navigation>
  ```

- 创建页面布局

  ![image-20181210173136651](/Users/liudawei/Library/Application Support/typora-user-images/image-20181210173136651.png)



- 使用Navigation

  ```java
  override fun onActivityCreated(savedInstanceState: Bundle?) {
          super.onActivityCreated(savedInstanceState)
          // Set up a click listener on the login button
          view?.findViewById<Button>(R.id.navigate_bt)?.setOnClickListener { it ->
              // Navigate to the login destination
              view?.let { Navigation.findNavController(it).navigate(R.id.action_mainFragment_to_secondFragment) }
  }
  }
  ```


#### 3.参考内容

https://developer.android.google.cn/topic/libraries/architecture/navigation/

https://codelabs.developers.google.com/codelabs/android-navigation/#1

https://juejin.im/entry/5b082c806fb9a07ab1119cc6



### 三、Room

#### 1.简介 

[Room](https://developer.android.com/training/data-storage/room/index.html?hl=zh-cn) 持久性库在 SQLite 上提供了一个抽象层，以便在充分利用 SQLite 的强大功能的同时，能够流畅地访问数据库。

该库可帮助您在运行应用的设备上创建应用数据的缓存。此缓存充当应用的单一可信来源，使用户能够在应用中查看关键信息的一致副本，无论用户是否具有互联网连接。

Room主要由以下三个部分组成：

- **DataBase** 用 Room.databaseBuilder() orRoom.inMemoryDatabaseBuilder().方法获取DataBase实例
- **Entity** 可以看成是数据库中的表
- **DAO** 提供访问数据库的方法



![img](https://developer.android.com/images/training/data-storage/room_architecture.png?hl=zh-cn)

#### 2.如何使用Room

- 引入Room 组件

  ```groovy
  dependencies {
      implementation "android.arch.persistence.room:runtime:1.0.0"
      annotationProcessor "android.arch.persistence.room:compiler:1.0.0"
  }
  ```

  **注：使用kotlin需要使用以下配置：**

  ```groovy
  apply plugin: 'kotlin-kapt'
  ...
  dependencies {
      implementation "android.arch.persistence.room:runtime:1.1.1"
      kapt "android.arch.persistence.room:compiler:1.1.1"
  
  }
  ```

- Dao

  ```kotlin
  @Dao
  interface UserDao {
      @Query("SELECT * FROM user")
      fun getAll(): List<User>
  
      @Query("SELECT * FROM user WHERE uid IN (:userIds)")
      fun loadAllByIds(userIds: IntArray): List<User>
  
      @Query("SELECT * FROM user WHERE first_name LIKE :first AND " +
             "last_name LIKE :last LIMIT 1")
      fun findByName(first: String, last: String): User
  
      @Insert
      fun insertAll(vararg users: User)
  
      @Delete
      fun delete(user: User)
  }
  ```

- Entity

  ```kotlin
  @Entity
  data class User(
      @PrimaryKey var uid: Int,
      @ColumnInfo(name = "first_name") var firstName: String?,
      @ColumnInfo(name = "last_name") var lastName: String?
  )
  ```

- Database

```kotlin
@Database(entities = arrayOf(User::class), version = 1)
abstract class AppDatabase : RoomDatabase() {
    abstract fun userDao(): UserDao
}
```

- 获取数据对象，进行操作

  ```kotlin
  val db = Room.databaseBuilder(
              applicationContext,
              AppDatabase::class.java, "database-name"
          ).build()
  //插入
   val user = User(id,"bob"+id,"test")
   db.userDao().insertAll(user)
  //更新
   db.userDao().updata(User(1,"bob2","test"))
  //删除
   db.userDao().delete(User(1,"bob","test"))
  //查询
  val users  =  db.userDao().getAll()
  ```

- 查看数据库表：

  ![image-20181211164501701](/Users/liudawei/Library/Application Support/typora-user-images/image-20181211164501701.png)


#### 3.参考内容

https://developer.android.com/training/data-storage/room/?hl=zh-cn

https://developer.android.com/training/data-storage/room/defining-data#kotlin

https://www.jianshu.com/p/ffcbfcea0ad2



### 四、WorkManager

#### 1.简介

首先我们来看下WorkManager的定义，WorkManager API可以轻松地让异步任务延迟执行以及何时运行它们，这些API可让我们创建任务并将其交给WorkManager，以便立即或在适当的时间运行。例如，应用程序可能需要不时从网络下载新资源，我们可以使用WorkManager API设置一个任务，然后选择适合它运行的环境（例如“仅在设备充电和联网时”），并在符合条件时将其交给 WorkManager 运行，即使该应用程序被强制退出或者设备重新启动，这个任务仍然可以保证运行。

> **注意**：WorkManager适用于那些即使应用程序退出，系统也能够保证这个任务正常运行的场景，比如将应用程序数据上传到服务器。它不适用于应用进程内的后台工作，如果应用进程消失，就可以安全地终止，对于这种情况，推荐你使用[线程池](https://link.juejin.im/?target=https%3A%2F%2Fdeveloper.android.google.cn%2Ftraining%2Fmultiple-threads%2Fcreate-threadpool%23ThreadPool)。

WorkManager会根据移动设备的API级别和应用程序状态等因素选择适当的方式来运行我们的任务，如果WorkManager在应用程序运行时执行其中一项任务，则WorkManager可以在应用进程中开启一个的新线程中运行这个任务。如果你的应用程序未运行，WorkManager将选择合适的方式来安排后台任务 - 这取决于设备API级别和应用包含的依赖关系，WorkManager可能使用[JobScheduler](https://link.juejin.im/?target=https%3A%2F%2Fdeveloper.android.google.cn%2Freference%2Fandroid%2Fapp%2Fjob%2FJobScheduler)， [Firebase JobDispatcher](https://link.juejin.im/?target=https%3A%2F%2Fgithub.com%2Ffirebase%2Ffirebase-jobdispatcher-android%23user-content-firebase-jobdispatcher-)或[AlarmManager](https://link.juejin.im/?target=https%3A%2F%2Fdeveloper.android.google.cn%2Freference%2Fandroid%2Fapp%2FAlarmManager)。我们完全不需要编写设备逻辑来确定设备具有哪些功能并选择适当的API，相反，我们可以将任务交给WorkManager并让它选择最佳选项。

#### 2.如何使用 WorkManager

- 引入依赖

  ```groovy
  dependencies {
      // java
      implementation "android.arch.work:work-runtime:1.0.0-alpha01"
  	//kotlin
      implementation "android.arch.work:work-runtime-ktx:1.0.0-alpha01"
      
      // optional - RxJava2 support
      implementation "android.arch.work:work-rxjava2:1.0.0-alpha01"
  
      // optional - Test helpers
      androidTestImplementation "android.arch.work:work-testing:1.0.0-alpha01"
  }
  
  ```

- 类和概念

WorkManager API使用几个不同的类。在某些情况下，您需要子类化其中一个API类。

这些是最重要的WorkManager类：

**Worker**：在WorkManager世界中，Worker等同于需要在后台执行的任务或作业。这是一个抽象类。你需要继承它。您的Worker类包含有关如何执行该任务的信息，但它没有关于何时运行的信息。

 **WorkRequest**：它代表了工作调度请求。每个工作必须在安排工作之前创建工作请求。 WorkRequest将包含工作的唯一标识，约束条件说明应在哪种情况下执行任务。这是一个抽象类。该库提供了这个类的两个直接子类：OneTimeWorkRequest和PeriodicWorkRequest。

 **WorkManager**：它是基于WorkRequest中定义的约束来管理和调度任务的类。

 **WorkStatus**：这个类包装了任何work请求的状态，你可以通过唯一的id来查询任何work的状态。



- 创建work

  ```kotlin
  class CompressWorker(context : Context, params : WorkerParameters)
      : Worker(context, params) {
  
      override fun doWork(): Result {
  
          // Do the work here--in this case, compress the stored images.
          // In this example no parameters are passed; the task is
          // assumed to be "compress the whole library."
          myCompress()
  
          // Indicate success or failure with your return value:
          return Result.success()
  
          // (Returning Result.retry() tells WorkManager to try this task again
          // later; Result.failure() says not to try again.)
  
      }
  
  }
  ```

- 创建约束

  ```kotlin
  class CompressWorker(context : Context, params : WorkerParameters)
      : Worker(context, params) {
  
      override fun doWork(): Result {
  
          // Do the work here--in this case, compress the stored images.
          // In this example no parameters are passed; the task is
          // assumed to be "compress the whole library."
          myCompress()
  
          // Indicate success or failure with your return value:
          return Result.success()
  
          // (Returning Result.retry() tells WorkManager to try this task again
          // later; Result.failure() says not to try again.)
  
      }
  
  }
  ```

- 取消任务

  ```kotlin
  val compressionWorkId:UUID = compressionWork.getId()
  WorkManager.getInstance().cancelWorkById(compressionWorkId)
  ```

- 标记work

  ```kotlin
  val cacheCleanupTask =
          OneTimeWorkRequestBuilder<MyCacheCleanupWorker>()
      .setConstraints(myConstraints)
      .addTag("cleanup")
      .build()
  ```

- 重复任务

  ```kotlin
  val photoCheckBuilder =
          PeriodicWorkRequestBuilder<PhotoCheckWorker>(12, TimeUnit.HOURS)
  // ...if you want, you can apply constraints to the builder here...
  
  // Create the actual work object:
  val photoCheckWork = photoCheckBuilder.build()
  // Then enqueue the recurring task:
  WorkManager.getInstance().enqueue(photoCheckWork)
  
  ```


#### 3.参考内容

https://developer.android.com/topic/libraries/architecture/workmanager/advanced

https://developer.android.com/topic/libraries/architecture/workmanager/basics#kotlin

https://www.jianshu.com/p/5ad43e800e05

https://juejin.im/post/5af4aa91f265da0b8d41f714
