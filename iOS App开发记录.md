##开源项目

### UI
- [TabBar](https://github.com/ezescaruli/ESTabBarController.git)
- [Autolayout](https://github.com/SnapKit/Masonry)
- [引导页](https://github.com/bing6/KSGuide.git)
- [轮播组件](https://github.com/tedy51/BannerLoop)


### 基础功能组件
- [PINCache](https://github.com/pinterest/PINCache.git)
- [RestKit](https://github.com/RestKit/RestKit.git)
- [Reachability](https://github.com/tonymillion/Reachability.git)
- [页面跳转解耦](https://github.com/mogujie/MGJRouter)
- [Hex color strings to UIColor](https://github.com/kevinrenskers/UIColor-HexString)

##备忘

### 状态栏
- View controller-based status bar appearance -> NO
- Status bar is initially hidden -> YES

### 网络
- App Transport Security Settings -> Allow Arbitrary Loads -> YES

### UITabbar和UINavigationController
- main.story 中的UIViewController可以通过菜单Eidter -> Embed in -> Navigation Controller
- UITabbar对应的UIViewController的导航栏应该在每个ViewController实现
- self.navigationController.navigationBarHidden = NO; 设置导航栏是否可见
- 隐藏 Tabbar
  - self.tabbar.hidden = hidden;
  - [self.tabBar setTranslucent:hidden];

### Cordova
- Status Plugin插件会resize webview

### CocoaPods
- include of non-modular header inside framework module
  - pod repo push <repoName> <podspec> --use-libraries
- Develop Pods

## 文章
- [COCOAPODS创建私有PODS](http://www.cnblogs.com/tufeibo/p/5654268.html)
