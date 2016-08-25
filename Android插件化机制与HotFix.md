### 为什么需要插件化
1. Dalvik虚拟机有方法数限制:Dex 64k method size limit / LinearAlloc exceeded 5MB capacity, App规模变大时会遇到这个问题
2. 模块解耦
3. 动态升级
4. 按需下载

### 概念
1. 插件化-指将一个程序划分为不同的部分，比如App皮肤样式
2. 组件化-指通用及复用性较高的构件，比如图片缓存可以看成一个组件被多个 App 共用
3. 动态加载

### 技术点
1. dex拆分和插件dex加载
-Android官方解决方案:multidex support library
2. 插件资源访问
-通过反射，通过调用AssetManager中的addAssetPath方法
3. 插件Activity生命周期
-代理Activity

### 技术原理
ClassLoader[6]
1. 隔离问题
2. Android ClassLoader
  - dalvik.system.DexClassLoader
  - dalvik.system.PathClassLoader

### 开源方案
- [DynamicLoadApk](https://github.com/singwhatiwanna/dynamic-load-apk)
- [AndroidDynamicLoader](https://github.com/mmin18/AndroidDynamicLoader)
- [PluginManager](https://github.com/houkx/android-pluginmgr)
- [DroidPlugin](https://github.com/Qihoo360/DroidPlugin)
- [AndFix](https://github.com/alibaba/AndFix)


### 参考资料
- [Android 插件化 动态升级](http://www.trinea.cn/android/android-plugin/)
- [DroidPlugin](https://github.com/Qihoo360/DroidPlugin)
- [Android拆分与加载Dex的多种方案对比](http://dwz.cn/3Xxhg8)
- [預防 Android Dex 64k Method Size Limit](http://ingramchen.io/blog/2014/09/prevention-of-android-dex-64k-method-size-limit.html)
- [Java ClassLoader基础及加载不同依赖 Jar 中的公共类](http://www.trinea.cn/android/java-loader-common-class/)
- [DynamicLoadApk](https://github.com/singwhatiwanna/dynamic-load-apk)
- [Andfix](https://github.com/alibaba/AndFix)
- [携程Android App插件化和动态加载实践]( http://www.infoq.com/cn/articles/ctrip-android-dynamic-loading)
- [微信 Tinker 负责人张绍文关于 Android 热修复直播分享记录
](http://www.diycode.cc/topics/231)
