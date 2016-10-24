### 背景
&emsp;&emsp;基线中有大量业务有动态化需求，现在我们使用WebView加载H5实现页面动态化。跟电商团队部分同事讨论了一下，大家普遍反映WebView加载H5页面存在交互体验差、稳定性差(各种兼容性问题和内存问题导致的Crash)等。另一方面，业务发展导致APP安装包越来越大，对轻量级APP开发的技术方案需求也越来越强烈。为了解决WebView加载H5页面带来的各种问题，同时满足业务方对动态化的需求，我们开始调研React Native在爱奇艺业务中的使用。

### 调研
1. RN iOS Demo 跑起来
  - 了解RN技术栈
  - 了解业界实践案例
  - 了解开发、调试、部署流程
<br><br/>
2. [电商页面RN改造实验](https://github.com/ustcqidi/Article/blob/master/%E7%94%B5%E5%95%86%E7%A7%92%E6%9D%80%E9%A1%B5%E9%9D%A2RN%E6%94%B9%E9%80%A0%E5%AE%9E%E9%AA%8C.md)
  - 性能测试、对比
    - CPU
    - 内存
    - 动画
  - 开发方式
  - 包大小
  - 真机体验

### 框架层工作
![](https://raw.githubusercontent.com/ustcqidi/Image/master/iqiyi_rn_arch.jpeg)

1. RN 框架集成
  - 集成方式
    - 基础Pods库
    - 集成电商
    - 集成基线
  - 维护成本
<br><br/>
2. 业务插件支持
  - 扩展机制研究
    - RN扩展，js部分发布到本地npm服务器
    - RN扩展，native部分集成到RN框架工程中
  - 收银台
  - 登录
  - 分享
  - Native <-> RN <-> Web 跳转研究
  - Cookie同步
<br><br/>
3. 前端打包、部署
  - npm服务搭建
<br><br/>
4. 优化
  - 优化点
    - 首屏
    - 包大小 Release版本增加1M
    - Navigation动画卡顿问题
      - [官方资料](https://facebook.github.io/react-native/docs/performance.html )
      - NavigatorIOS
  - 优化方法
    - [提前上下文初始化(Pre-loading the Bridge)](https://github.com/dsibiski/react-native-hybrid-app-examples/blob/master/README.md)
    - [bundle拆分](https://github.com/ustcqidi/Article/blob/master/React%20Native%20repack%20solution/repack.md)
    - 缓存 AsyncStorage
<br><br/>
5. 风险控制、降级方案

6. 踩过的坑
  - RCT开头的模块，RN加载的时候回去掉RCT前缀
  - React Native框架工程引用基线工程模块是通过配置Header Search Paths完成编译的
    - RN框架会编译成静态库，不会链接出错，最终运行需要再基线主工程中确保可以链接到基线基础库
    - RN框架依赖的基线库和头文件可以使用init.sh脚步拉取
  - RN与系统Cookie同步
  - 接触了Gerrit
  - 电商首页是Tabbar方式布局的，我们要改造的"秒杀"列表页面只是其他一个Tab页面，点击"秒杀"列表项时进入商品详情页，这时候默认情况下底部的Tabbar还在；考虑过重新启一个RN页面实例，然后使用pushViewConroller方式打开详情页面；这种方案有两个问题：1. 多个RN实例，内存、CPU开销变大；2. RN只有入口url，不是每个页面都有url，要新启动RN的View需要业务方针对“秒杀”列表和详情页面分别写两个入口，这样的话会涉及两个页面的参数传递问题。最后把解决问题的焦点放在 ***隐藏Tabbar*** 上，具体方案参考[这里](https://github.com/ustcqidi/Article/blob/master/iOS%20App%E5%BC%80%E5%8F%91%E8%AE%B0%E5%BD%95.md)。

### 推广
  - [React Native调研分享](https://github.com/ustcqidi/Keynote/blob/master/rn.pptx.zip)

### 产品落地
#### 电商
&emsp;&emsp;目前iOS包大小问题比较严重，因此决定先调研RN在iOS平台的产品化落地方案；经过讨论，我们决定选择电商"秒杀"场景做整个闭环链路的RN改造调研，具体工作规划如下：

1. 目标
<br>&emsp;&emsp;完成电商"秒杀"业务全链路RN改造，包括秒杀首页 -> 商品详情 -> 用户下单 -> 购物车 -> 完成支付，另外期望通过这个工作对RN ***基础框架的性能、稳定性、业务扩展支持、包大小影响*** 以及 ***业务开发、部署流程*** 等基础知识有一些积累，便于后面更多业务方接入RN。

2. 具体工作
  - 前端RN组件调研
  - RN页面部署方案研究
  - 秒杀首页、商品详情、购物车等页面RN实现
  - "秒杀"全链路业务逻辑RN实现
  - RN框架集成
  - RN扩展机制研究
  - 基线收银台模块RN扩展插件
  - 基线登录模块RN扩展插件
  - 基线分享RN扩展插件
  - [RN分包研究](https://github.com/ustcqidi/Article/blob/master/React%20Native%20repack%20solution/repack.md)
<br>
</br>
3. 人员
  - 刘永生
  - 祁 迪
  - 马 颉
<br>
</br>
3. 排期
<br></br>&emsp;&emsp;框架集成、扩展机制调研等需要2周左右，“秒杀业务”改造、业务部署流程调研等大概需要4周左右。

![](https://github.com/ustcqidi/Image/blob/master/imall_plan.jpeg?raw=true)

#### 泡泡
1. 具体工作
  - Kickoff会议，讨论泡泡可改造页面

#### iPAD活动页面
1. 具体工作
  - Kickoff会议
  - 出差协助改造
  - 7.10上线

#### 动漫

#### 文学
