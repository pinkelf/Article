## Android WebView漏洞修复建议
&emsp;&emsp;[金刚安全监测报告](http://service.security.tencent.com/uploadimg_dir/jingang/049757476f0bd0936b93b46bbac4f6f0.html)显示，基线代码中存在低版本WebView JS注入攻击的隐患，Android API 16.0及之前的版本中存在安全漏洞，该漏洞源于程序没有正确限制使用WebView.addJavascriptInterface方法。远程攻击者可通过使用Java Reflection API利用该漏洞执行任意Java对象的方法 Google Android <= 4.1.2 (API level 16) 受到此漏洞的影响。附件是利用这个漏洞进行攻击的Demo，可以在4.1以及以下的机器试试；在网页端注入以下JS代码，就可以直接获取本地SDCard根目录所有文件，如下图所示。
<pre><code>function execute(cmdArgs)
   {
    for (var obj in window) {
        console.log(obj);
        if ("getClass" in window[obj]) {
            alert(obj);
            return window[obj].getClass().forName("java.lang.Runtime").getMethod("getRuntime",null).invoke(null,null).exec(cmdArgs);
         }
     }
   }

  var p = execute(["ls","/mnt/sdcard/"]);
  document.write(getContents(p.getInputStream()));
</code></pre>

![](https://raw.githubusercontent.com/ustcqidi/Image/master/webview_attack.jpeg)

&emsp;&emsp;CVE(Common Vulnerabilities and Exposures)已经收录了WebView addJavascriptInterface相关漏洞，详细信息可以参考: [CVE-2012-6636](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2012-6636), [CVE-2014-1939](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2014-1939), [CVE-2014-7224](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2014-7224)

&emsp;&emsp;根据扫描结果，基线代代码有较多地方引使用了WebView.addJavasciptInterface方法，具体如下：
<pre><code>
[assets\pluginapp\org.qiyi.android.pay.qywallet.apk]org\qiyi\android\pay\qybank\views\QYBankPayWebViewFragment.java
[assets\pluginapp\org.qiyi.android.pay.qywallet.apk]org\qiyi\android\pay\qywallet\activitys\WalletBindPhoneActivity.java
[assets\pluginapp\org.qiyi.android.pay.qywallet.apk]org\qiyi\android\pay\qywallet\views\security\WModifyTelWebViewFragment.java
[assets\pluginapp\org.qiyi.android.pay.qywallet.apk]org\qiyi\android\plugin\pay\bank\states\UnionPayWebViewState.java
</code></pre>

可以看出其中涉及了钱包、银行卡等安全敏感业务，所以必须及时修复。


### 修复建议
1. Android 4.2以上的系统
<br>Google作了修正，通过在Java的远程方法上面声明一个@JavascriptInterface，如下面代码：
<pre><code>
class JsObject {
   @JavascriptInterface
   public String toString() {
      return "injectedObject";
    }
}
</code></pre>

2. Android 4.2以下的系统
<br>Android的WebView中有一个WebChromeClient类，这个类其实就是用来监听一些WebView中的事件的，具体方法如下:
<pre><code>
@Override
public boolean onJsPrompt(WebView view, String url, String message, String defaultValue, JsPromptResult result) {
    return super.onJsPrompt(view, url, message, defaultValue, result);
}
@Override
public boolean onJsAlert(WebView view, String url, String message, JsResult result) {
    return super.onJsAlert(view, url, message, result);
}
@Override
public boolean onJsConfirm(WebView view, String url, String message, JsResult result) {
    return super.onJsConfirm(view, url, message, result);
}
</code></pre>
在JS脚本中调用prompt方法，这样对应的就会走到WebChromeClient类的onJsPrompt()方法中。因此可以自定义一套私有协议完成JS和Native的通信：JS端按照规则拼接字符串然后调用prompt，Java端重写OnJsPrompt后解析协议并实现通信逻辑，通信协议字符串如下：
<pre><code>
javascript:(function JsAddJavascriptInterface_(){
   if(typeof(window.XXX_js_interface_name)!='undefined'){
       console.log('window.XXX_js_interface_name is exist!!');
   }else{
       window.XXX_js_interface_name={
           XXX:function(arg0,arg1){
               return prompt('Qiyi:'+JSON.stringify({obj:'XXX_js_interface_name',func:'XXX_',args:[arg0,arg1]}));
           },
       };
   }
})()
</code></pre>

因此，针对不同版本的机器可以有不同的修复方案：第一种方案只适用于4.2以及以上版本的机器，修改起来比较简单；第二种方案是通用的自定义协议，适用于所有版本，修改起来稍微复杂。自定义协议具体实现请参考附件的SafaWebView.java
