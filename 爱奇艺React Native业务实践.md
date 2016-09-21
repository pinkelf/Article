### 背景
&emsp;&emsp;基线中有大量业务有动态化需求，现在我们使用WebView加载H5实现页面动态化。跟电商团队部分同事讨论了一下，大家普遍反映WebView加载H5页面存在交互体验差、稳定性差(各种兼容性问题和内存问题导致的Crash)等。另一方面，业务发展导致APP安装包越来越大，对轻量级APP开发的技术方案需求也越来越强烈。为了解决WebView加载H5页面带来的各种问题，同时满足业务方对动态化的需求，我们开始调研React Native在爱奇艺业务中的使用。

### 调研
1. RN iOS Demo 跑起来
  - 了解RN技术栈
  - 了解业界实践案例
  - 了解开发、调试、部署流程
<br><br/>
2. 电商页面RN改造实验
  - 性能测试、对比
    - CPU
    - 内存
    - 动画
  - 开发方式
  - 包大小
  - 真机体验

### 推广
- [React Native调研分享](https://github.com/ustcqidi/Keynote/blob/master/rn.pptx.zip)

### 实施
1. RN 框架集成
  - 风险控制、降级方案
  - 维护成本
<br><br/>
2. 优化
  - 优化点
    - 首屏
  - 优化方法
    - 提前上下文初始化
    - 缓存 AsyncStorage
    - bundle拆分
