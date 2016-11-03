## 准备 react-native 开发环境
&emsp;&emsp;see https://facebook.github.io/react-native/docs/getting-started.html
<br>&emsp;&emsp;请使用React Native 0.33版本~

## 在基线中使用react-native框架
&emsp;&emsp;我们基于官方的react-native框架基础做了针对爱奇艺基线业务模块的扩展：比如云控、 性能埋点、基线登录、收银台等，如下图：

![](https://raw.githubusercontent.com/ustcqidi/Image/master/iqiyi_rn_arch.jpeg)

&emsp;&emsp;目前iOS端基线集成react-native框架已经完成，在Podfile中加入下面的依赖即可:
<pre>
<code>
pod 'React', '7.10.4', :configurations => ['Release']
pod 'React_debug', '7.10.4', :configurations => ['Debug']
</code>
</pre>

&emsp;&emsp;业务方可以继承RCTViewController(iOS)/QYRNBaseActivity(Android)实现自己的业务扩展，调用初始化接口initWithBundle传入测试bundle url和moduleName就可以了。具体使用方式可以参考基线中RCTCenteriPadViewController.mm的实现。

&emsp;&emsp;Android端集成正在进行中；后续会更新。

## RN页面与Native页面相互跳转
1. Native -> RN
<br>
RN页面最终封装成ViewController和Activity，所以Native -> RN跳转，类似于Native -> Native页面跳转。

2. RN -> Native
<br>
很多业务场景中会存在RN页面跳转基线Native页面的需求，比如RN页面跳转到基线登录页面、收银台页面、分享页面等等，为此我们开发了QYRouter组件：类似于H5页面跳转Native页面的JS Bridge协议，我们定义一份Schema约定实现RN页面跳转到Native页面，比如跳转到登录页面的Schema如下：
<pre>
<code>
{
  "action": "login",
  "params" : {
    ...
  }
}
</code>
</pre>

调用方式如下：
<pre>
<code>
//import QYRouter
var QYRNRouter = NativeModules.QYRouter;
...
//通过QYRouter调起基线Login页面
try{
  var routeParam = JSON.stringify({
      action: 'login',
      params: {
        from: 'imall'
      }
  });
  QYRNRouter.route(routeParam, (data)=>{
      <!-- successCallback(data); -->
  },  (code, data) => {
      <!-- errorCallback(err, data); -->
  });
}catch(e){
  console.error(e);
}
</pre>
</code>

## 业务方自定义Schema实现扩展

&emsp;&emsp;上面介绍了如何通过QYRouter传递Login的Schema调起基线Native的登录页面，但是每个业务方可能都有自己的特殊需求，比如 ***文学*** 可能需要调用基线的阅读器模块、***电影票*** 可能需要调用基线的播放器组件；这种情况下，业务方可以自定义Schema ***(必须按照约定的Schema格式)*** 实现自己的需求，比如调用阅读器的Schema可以这样定义(调用方式与上面的例子一致):

<code>
{
  "action": "reader",
  "params" : {
    ...
  }
}
</code>

### 实现自定义Schema
&emsp;&emsp;自定义的Schema需要业务方自己实现；上面提到了，业务方需要继承RCTViewController(iOS)/QYRNBaseActivity(Android)实现自己的业务扩展，所有框架层没处理的自定义的Schema都会通过QYRCTSchemaParser发送出来，业务方实现以下这个接口就行了，比如：

[super setQYRCTSchemaParser:^(NSDictionary *schemaDic) {
        //Schema处理结果回调
        self.failueCallBack = [schemaDic objectForKey:@"failueCB"];
        self.successCallBack = [schemaDic objectForKey:@"successCB"];

        NSString *action = [schemaDic objectForKey:@"action"];

        if ([action isEqualToString:@"reader"]) {
          NSDictionary *paramDic = [schemaDic objectForKey:@"params"];
          //处理reader相关逻辑
        }
      }];


## 内存消耗
对比场景，iOS/Android纯Native的hello work对比RN的hello world页面

*** iOS RN比Native增加约18MB（注意：如果RN用的debug版本，则增加约33MB）***
![](https://github.com/ustcqidi/Image/blob/master/rn_ios.jpeg?raw=true)

*** Android RN比Native增加约15MB（注意：如果RN用的debug版本，则增加约23MB Pss）***
![](https://github.com/ustcqidi/Image/blob/master/rn_android.jpeg?raw=true)
