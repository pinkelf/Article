##开源项目

### UI
- [TabBar](https://github.com/ezescaruli/ESTabBarController.git)
- [Autolayout](https://github.com/SnapKit/Masonry)
- [引导页](https://github.com/bing6/KSGuide.git)
- [轮播组件](https://github.com/tedy51/BannerLoop)
- [国家码选择](https://github.com/Dwarven/PhoneCountryCodePicker)


### 基础功能组件
- [PINCache](https://github.com/pinterest/PINCache.git)
- [RestKit](https://github.com/RestKit/RestKit.git)
- [Reachability](https://github.com/tonymillion/Reachability.git)
- [页面跳转解耦](https://github.com/mogujie/MGJRouter)
- [Hex color strings to UIColor](https://github.com/kevinrenskers/UIColor-HexString)
- [多语言](https://github.com/whde/WhdeLocalized)
- [Method Swizzling](https://github.com/rentzsch/jrswizzle)
- [EventBus-iOS](https://github.com/github-xiaogang/EventBus-iOS)
- [Navigation Router](https://github.com/idevzhou/ZYYRouter)
- [in APP Debug](https://github.com/Flipboard/FLEX)

##备忘

### 状态栏
- View controller-based status bar appearance -> NO
- Status bar is initially hidden -> YES

### 网络
- App Transport Security Settings -> Allow Arbitrary Loads -> YES

### UITabbar和UINavigationController
- main.story 中的UIViewController可以通过菜单Editor -> Embed in -> Navigation Controller
- UITabbar对应的UIViewController的导航栏应该在每个ViewController实现
- self.navigationController.navigationBarHidden = NO; 设置导航栏是否可见
- 隐藏 Tabbar
  - self.tabbar.hidden = hidden;
  - [self.tabBar setTranslucent:hidden];
- [self.navigationController.navigationBar.topItem setTitle:@"XXX"];

### UITableViewController 顶部空白
  - self.tableView.contentInset = UIEdgeInsetsMake(-40, 0, 0, 0);
  - self.edgesForExtendedLayout = UIRectEdgeNone;
  - self.automaticallyAdjustsScrollViewInsets = NO;

### Cordova
- Status Plugin插件会resize webview

### CocoaPods
- include of non-modular header inside framework module
  - pod repo push <repoName> <podspec> --use-libraries
- Develop Pods

### 组件化
- 组件页面跳转
  - 页面跳转返回后，ViewWillAppear时刷新
- 组件事件通知
- Catagory扩展AppDelegate某些方法，比如配置社会登录等功能，不用每次重新配置AppDelegate
- bundle资源路径问题

### Method Swizzling
- App需要支持动态语言切换，找到了[这个](https://github.com/whde/WhdeLocalized)解决方案，为了实现所以页面动态语言切换，需要所有ViewController继承这个库提供的基类(继承ViewDidLoad方法)，改动较大。可以使用Method Swizzling Hook ViewController的ViewDidLoad方法，加入切换语言的逻辑，这样保证改动最小；

- 社会化登录需要在AppDelegate相关方法中配置AppKey等许多基础信息，目前登录模块已经抽取为独立组件为多个App使用，每次都这样配置AppKey太繁琐，可以使用Method Swizzling Hoot AppDelegate的相关方法，加入相关逻辑；

- 使用UITableViewController每次都要做一些共同的基础配置，同样可以使用Method Swizzling 加入相关逻辑。

## 参考文章
- [COCOAPODS创建私有PODS](http://www.cnblogs.com/tufeibo/p/5654268.html)
- [Objective-C Runtime 运行时之四：Method Swizzling](http://blog.jobbole.com/79580/)
- [iOS黑魔法－Method Swizzling](http://www.cocoachina.com/ios/20160121/15076.html)
- [iOS 组件化方案探索](http://blog.cnbang.net/tech/3080/)
- [做一个 App 前需要考虑的几件事](http://limboy.me/tech/2016/07/06/starting-an-app.html)
- [最快让你上手ReactiveCocoa之基础篇](http://www.jianshu.com/p/87ef6720a096)
- [Masonry介绍与使用实践(快速上手Autolayout)](http://adad184.com/2014/09/28/use-masonry-to-quick-solve-autolayout/)
- [iOS可执行文件瘦身方法](http://blog.cnbang.net/tech/2544/)
