## [android 开发题](https://juejin.cn/post/6844903891625050119)
## [android 进阶到高级-突破篇](https://pangrongxian.github.io/2017/07/17/Android%20%E8%BF%9B%E9%98%B6%E5%88%B0%E9%AB%98%E7%BA%A7%20-%20%E7%AA%81%E7%A0%B4%E7%AF%87/)

## [热修复原理](https://juejin.cn/post/6844903985602789384)

## [Service AIDL之重连方法](https://www.jianshu.com/p/476abecc7292)

## [美团技术团队](https://tech.meituan.com/archives)

## [启智观-ActivityManagerService](https://duanqz.github.io/2016-07-15-AMS-LaunchProcess#Android%E5%90%AF%E6%99%BA%E8%A7%82)

## ADB
adb shell dumpsys meminfo  进程的内存专用后可以接包名  
adb shell dumpsys activities  activity栈栈信息  

## [Binder机制](https://blog.csdn.net/carson_ho/article/details/73560642)

## [AIDL DeadObjectException 死亡代理](https://blog.csdn.net/jqwei2/article/details/98611551)

## [https](https://www.runoob.com/w3cnote/http-vs-https.html)
https://blog.csdn.net/wu_noah/article/details/108883328
1.什么是对称加密,非对称加密对称加密是指加解密使用的是同样的密钥非对称加密是指加解密使用的密钥不同。  
2.对称加密的优缺点对称加密的特点是简单快速。 密钥越大，加密越强，但加解密过程越慢。  
3.非对称加密的优缺点非对称加密使用了一对密钥，公钥和私钥。  

## 反射机制
加载完类之后，在堆中就产生了一个Class类型的对象（一个类只有一个Class对象），这个对象包含了类的完整结构信息。通过这个对象得到类的结构。这个对象就像一面镜子，透过这个镜子看到类的结构，所以，形象的称之为反射

## [DataBinding](https://www.bilibili.com/video/BV1Zv411k7xe?p=2&spm_id_from=pageDriver)  [博客](https://www.wuhaojie.top/2018/10/11/AndroidDataBindingComprehension/)
1、生成一份-layout.xml,生成一份和原布局一样的添加tag:  一次递归找到所有的View(相比findViewById性能提高,-layout目录：intermediates-->data_binding_layout_info_type_merge，布局目录intermediates-->mergeDebugResources-->stripped.dir，BindingImpl目录:generated-->source-->apt-->包路径-->databinding下)   
2、ObservableField 继承Observable -->notifyPropertyChanged(BR.属性)--> mDirtyFlags
3、SparseIntArray 存放id,index--》ActivityWarningDetailBindingImpl赋值完之后清除tag
4、Bind根据layoutId找到localizedLayoutId，mapBindings方法View根据id找到-layout的tag(id)找到对应的index

## [LiveData](https://www.jianshu.com/p/d66b2fd4d918)
observe传入--》LifecycleOwner只有一个作用获取--》LifecycleRegistry（继承Lifecycle，activity、fragment中实现了LifecycleOwner可以获取到LifecycleRegistry，用于获取可以获取生命周期相关数据，mObserverMap管理监听）
observe自动移除
observeForever 需要手动移除 根据版本更新

## [ViewModel](https://deskid.github.io/2017/07/28/ViewModel/)
1、ViewModel 数据层 和 UI Controller 之间分离的很干净。UI Controller 不用负责获取数据，也不用在重建时负责数据的有效性。  
2、ViewModel 数据层能感知到 UI Controller 的生命周期：保证 UI Controller 重建后，持有的是同一个ViewModel数据实例; UI Controller 结束生命周期后，系统自动调用ViewModel的clear()，释放资源。
配合 LiveData 使用效果更佳。  
3、之前放到onSaveInstanceState()的复杂数据，现在可以放到ViewModel（系统UI相关的除外）  
4、由于职责划分更加清晰，测试更方便。  
重点：Fragment在设置setRetainInstance(true)后，当 host Activity re-creation 时，fragment不会被destroyed，而是keep在内存中。当re-creation时，fragment 跳过 onDestroy() 和 onCreate()生命周期，并重新执行一遍 onAttach() 和 onDetach()之间的回调。
5、onDestroy时判断  mChangingConfigurations为false clear

## [ARouter](https://rebooters.github.io/2019/07/20/ARouter-API-%E5%88%86%E6%9E%90/)
https://juejin.cn/post/6885932290615509000

## SparseArray
1、无需包装：直接使用基本类型值，不需要包装成对象。   
2、无需hash，无需比对Key对象  直接使用基本类型值排序索引和判断相等，无碰撞，无需调用hashCode方法，无需equals比较。   
3、更小的内部数组：相比于ArrayMap，无需单独的hash排序数组，内部只需等长的两个数组分别存放Key和Value   
4、延迟删除：对于移除操作，SparseArray并不是在每次remove操作直接移动数组元素，而是用一个删除标记将对应key的value标记为已删除，并标记需要回收，等待下次添加、扩容等需要移动数组元素的地方统一操作，进一步提升性能。   
5、有序：所有键值对均是按照基本类型key的自然排序，支持下标访问(keyAt方法和valueAt方法)，迭代遍历和数组相同

## [Glide](https://www.jianshu.com/p/b85f89fce019)

## Activity启动流程  
android-26
scheduleLaunchActivity（H发送消息）--》performLaunchActivity
1、创建LoadedApk对象   
2、创建ContextImpl对象   
3、获取ClassLoader， 调用mInstrumentation创建Activity对象(通过反射)  
4、创建Application对象、然后调用attach（attach中attachBaseContext-->LoadedApk）
、onCreate（通过反射）  
5、将Application/ContextImpl都attach到Activity对象  
6、执行回调onCreate、执行回调onStart、执行回调onStart

## [apk打包流程](https://blog.csdn.net/aha_jasper/article/details/104944929)
aapt阶段：aapt工具编译res资源文件，把大部分xml文件编译成二进制文件（图片文件除外），同时生成R.Java文件和resources.arsc文件，里面保存了资源的ID和在APK中的路径。   
aidl阶段：如果项目中有使用AIDL，那么就会把.aidl文件编译成.java文件。   
javaCompiler阶段：将所有.java文件(包括R文件和AIDL生成的.java文件)，通过javac工具生成class文件。   
dex阶段：将生成的.class文件和第三方库的.class文件通过dx工具生成classes.dex文件(如果有分包，那么可能有多个)。    
apkBuilder阶段：aapt阶段中的资源文件、dex文件和第三方的非java资源包(.so)，通过apkbuilder工具生成未签名的apk包。   
jarSigner阶段：签名，jarsigner工具，如果是debug模式用默认签名，release模式用开发者的签名。   
zipAligin阶段：对齐，通过zipalign工具对apk中的未压缩资源（图片、视频）进行“对齐操作”，让资源按4字节的边界进行对齐，使得资源访问速度更快。   

## [Gralde依赖问题](https://segmentfault.com/a/1190000015805844)
Gradle 默认开启了 依赖传递 意思就是 项目依赖了A，A又依赖了B和C，这时候，我们只需要写一行代码：implementation A就行了，由传递依赖导致的冲突，默认是以最高版本的依赖为准，要想查看整个项目的依赖传递关系，使用命令：
./gradlew app:dependencies --configuration releaseRuntimeClasspath
app是具体的module。
releaseRuntimeClasspath是具体的variants类型。
符号的含义：
x.x.x (*) 该依赖已经有了，将不再重复依赖。  
x.x.x -> x.x.x 该依赖的版本被箭头所指的版本代替。   
x.x.x -> x.x.x(*) 该依赖的版本被箭头所指的版本代替，并且该依赖已经有了，不再重复依赖。  
https://blog.51cto.com/u_14202100/5825621

### [深入探究Android应用启动起点](https://juejin.cn/post/6844904104490172430)
Android应用程序启动是从用户点击桌面图标开始，点击图标的第一响应是在Launcher进程，通过ActivityManagerService将创建进程信息传给zygote，zygote再执行fork，中间经历了两次跨进程通信，一次是Launcher进程通过Binder调用进入system_server进程，一次是system_server进程通过socket将创建进程信息传给zygote，zygote从睡梦中醒来，开始创建进程

### [EventBus底层实现原理](https://blog.csdn.net/donghejk/article/details/81607908)
###[SurfaceView及TextureView区别](https://blog.csdn.net/while0/article/details/81481771)
