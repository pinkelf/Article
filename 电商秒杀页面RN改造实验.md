### 背景
&emsp;&emsp;基线中有大量业务有动态化需求，现在我们使用WebView加载H5实现页面动态化。跟电商团队部分同事讨论了一下，大家普遍反映WebView加载H5页面存在交互体验差、稳定性差(各种兼容性问题和内存问题导致的Crash)等。另一方面，业务发展导致APP安装包越来越大，对轻量级APP开发的技术方案需求也越来越强烈。为了解决WebView加载H5页面带来的各种问题，同时满足业务方对动态化的需求；我们尝试对电商的“秒杀”页面用RN (React Native)改造，以下是“秒杀”页面的RN版本和WebView版本的对比分析。

### 效果图
&emsp;&emsp;下面分别是电商"秒杀"首页的WebView版本和RN版本的截图
<div>
<img src="https://github.com/ustcqidi/Image/blob/master/webview_imall_home.jpeg?raw=true" width="459" height="773" alt="WebView秒杀首页"/>

<img src="https://github.com/ustcqidi/Image/blob/master/rn_imall_home.jpeg?raw=true" width="459" height="773" alt="React Native秒杀首页"/>
</div>

### 对比分析

|对比项|WebView|React Native|说明
|:---|:---|:-----|:----
|内存|峰值为303MB，容易OOM|峰值183MB|数据参考下面的图
|CPU|CPU使用率抖动较大|CPU使用率比较平稳|数据参考下面的图
|交互体验|列表滚动不流畅、网页图片加载慢|列表滚动流畅，体验接近原生|参考下面的录屏动画
|包大小|没有额外包|RN框架会增加2M|无
|开发方式|Web前端技术栈|React.js 前端开发可快速上手|上述Demo页面前端同事1天完成

<br>
***WebView内存***
<br>
<img src="https://github.com/ustcqidi/Image/blob/master/webview_imall_mem.jpeg?raw=true" width="860" height="578" alt="WebView秒杀页内存"/>
<br></br>

***RN内存***
<br>
<img src="https://github.com/ustcqidi/Image/blob/master/RN_imall_mem.jpeg?raw=true" width="860" height="578" alt="RN秒杀页内存"/>
<br></br>

***WebView CPU***
<br>
<img src="https://github.com/ustcqidi/Image/blob/master/ios_webview_cpu.jpeg?raw=true" width="854" height="410" alt="WebView秒杀页CPU"/>
<br></br>

***RN CPU***
<br>
<img src="https://github.com/ustcqidi/Image/blob/master/ios_rn_cpu.jpeg?raw=true" width="854" height="410" alt="RN秒杀页CPU"/>
<br></br>

***RN vs WebView***
<br>
<div style="display:inline;">
  <img src="https://github.com/ustcqidi/Image/blob/master/RN_imall.gif?raw=true" width="375" height="691" alt="RN交互"/>

  &emsp;&emsp;&emsp;&emsp;&emsp;&emsp;

  <img src="https://github.com/ustcqidi/Image/blob/master/webview_imall.gif?raw=true" width="375" height="691" alt="WebView交互"/>
</div>
