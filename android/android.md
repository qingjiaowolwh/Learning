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

## [Arouter](https://juejin.cn/post/6885932290615509000)

## SparseArray
1、无需包装：直接使用基本类型值，不需要包装成对象。   
2、无需hash，无需比对Key对象  直接使用基本类型值排序索引和判断相等，无碰撞，无需调用hashCode方法，无需equals比较。   
3、更小的内部数组：相比于ArrayMap，无需单独的hash排序数组，内部只需等长的两个数组分别存放Key和Value   
4、延迟删除：对于移除操作，SparseArray并不是在每次remove操作直接移动数组元素，而是用一个删除标记将对应key的value标记为已删除，并标记需要回收，等待下次添加、扩容等需要移动数组元素的地方统一操作，进一步提升性能。   
5、有序：所有键值对均是按照基本类型key的自然排序，支持下标访问(keyAt方法和valueAt方法)，迭代遍历和数组相同

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

